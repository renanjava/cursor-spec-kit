# Database Rules

## Princípios

- Dados são o ativo mais crítico
- Migrations devem ser reversíveis
- Performance de queries é essencial
- Backups são obrigatórios

## Migrations

### Versionamento

**Nomenclatura:**
```
migrations/
├── 001_create_users_table.sql
├── 002_add_email_index.sql
├── 003_create_orders_table.sql
```

**Ferramentas:**
- Knex.js
- Prisma Migrate
- TypeORM
- Flyway

### Regras

**✅ Fazer:**
- Migrations incrementais e pequenas
- Testar rollback antes de deploy  
- Incluir dados de seed quando necessário
- Versionar no git

**❌ Evitar:**
- Editar migrations já aplicadas
- Migrations que levam > 5min
- Breaking changes sem período de transição
- Deletar dados sem backup

### Exemplo

```sql
-- Up
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  name VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);

-- Down
DROP INDEX IF EXISTS idx_users_email;
DROP TABLE IF EXISTS users;
```

## Schema Design

### Normalização

**3NF (Third Normal Form) como baseline:**
```sql
-- ✅ Normalizado
CREATE TABLE orders (
  id UUID PRIMARY KEY,
  user_id UUID REFERENCES users(id),
  total DECIMAL(10,2),
  created_at TIMESTAMP
);

CREATE TABLE order_items (
  id UUID PRIMARY KEY,
  order_id UUID REFERENCES orders(id),
  product_id UUID REFERENCES products(id),
  quantity INTEGER,
  price DECIMAL(10,2)
);
```

### Denormalização Estratégica

Quando performance exigir:
```sql
-- Adicionar coluna calculada
ALTER TABLE orders ADD COLUMN items_count INTEGER;

-- Manter atualizada via trigger
CREATE TRIGGER update_items_count
AFTER INSERT OR DELETE ON order_items
FOR EACH ROW EXECUTE FUNCTION update_order_items_count();
```

### Constraints

```sql
-- Primary Key
id UUID PRIMARY KEY DEFAULT gen_random_uuid()

-- Foreign Key
user_id UUID REFERENCES users(id) ON DELETE CASCADE

-- Unique
email VARCHAR(255) UNIQUE NOT NULL

-- Check
age INTEGER CHECK (age >= 18)

-- Not Null
name VARCHAR(255) NOT NULL
```

## Indexação

### Quando Criar Índices

**✅ Criar para:**
- Colunas em WHERE frequentemente
- Colunas em JOIN
- Colunas em ORDER BY
- Foreign keys

**❌ Evitar para:**
- Tabelas muito pequenas (< 1000 rows)
- Colunas com baixa cardinalidade (ex: boolean)
- Colunas raramente consultadas

### Tipos de Índices

**B-Tree (padrão):**
```sql
CREATE INDEX idx_users_email ON users(email);
```

**Composto:**
```sql
-- Ordem importa! user_id deve vir primeiro se filtrar por ele
CREATE INDEX idx_orders_user_date ON orders(user_id, created_at);
```

**Parcial:**
```sql
-- Apenas para registros ativos
CREATE INDEX idx_active_users ON users(email) WHERE active = true;
```

**Full-text:**
```sql
-- PostgreSQL
CREATE INDEX idx_products_search ON products USING GIN(to_tsvector('portuguese', name));
```

### Verificar Uso

```sql
-- PostgreSQL
EXPLAIN ANALYZE 
SELECT * FROM users WHERE email = 'test@example.com';

-- Verificar índices não utilizados
SELECT schemaname, tablename, indexname
FROM pg_stat_user_indexes
WHERE idx_scan = 0;
```

## Query Optimization

### N+1 Queries

**❌ Problema:**
```typescript
// 1 query para users + N queries para posts
const users = await db.query('SELECT * FROM users');
for (const user of users) {
  user.posts = await db.query('SELECT * FROM posts WHERE user_id = ?', [user.id]);
}
```

**✅ Solução:**
```typescript
// 2 queries total
const users = await db.query('SELECT * FROM users');
const userIds = users.map(u => u.id);
const posts = await db.query('SELECT * FROM posts WHERE user_id = ANY(?)', [userIds]);

// Ou JOIN
const result = await db.query(`
  SELECT u.*, p.*
  FROM users u
  LEFT JOIN posts p ON p.user_id = u.id
`);
```

### Paginação

**❌ Offset para grandes datasets:**
```sql
-- Lento para páginas altas
SELECT * FROM orders 
ORDER BY created_at DESC 
LIMIT 20 OFFSET 10000;
```

**✅ Cursor-based:**
```sql
-- Mais eficiente
SELECT * FROM orders 
WHERE created_at < '2024-01-01'
ORDER BY created_at DESC 
LIMIT 20;
```

### Select Específico

```sql
-- ❌ Evitar
SELECT * FROM users;

-- ✅ Apenas colunas necessárias
SELECT id, name, email FROM users;
```

## Transações

### ACID Compliance

```typescript
// PostgreSQL
await db.transaction(async (trx) => {
  const order = await trx('orders').insert({ user_id, total });
  await trx('order_items').insert(items.map(i => ({ ...i, order_id: order.id })));
  await trx('inventory').decrement('quantity', 1).where({ product_id });
});
```

### Isolation Levels

```sql
-- Default (Read Committed) geralmente adequado
BEGIN TRANSACTION ISOLATION LEVEL READ COMMITTED;

-- Serializable para operações críticas
BEGIN TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

## Connection Pooling

```typescript
// Configuração adequada
const pool = new Pool({
  host: process.env.DB_HOST,
  database: process.env.DB_NAME,
  user: process.env.DB_USER,
  password: process.env.DB_PASSWORD,
  max: 20, // Conexões máximas
  min: 5,  // Conexões mínimas mantidas
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
});
```

## Backups

### Estratégia

- **Diários:** Full backup
- **Horários:** Incremental/WAL archiving
- **Retenção:** 30 dias mínimo
- **Teste restore:** Mensalmente

### Exemplo PostgreSQL

```bash
# Backup
pg_dump -h localhost -U postgres -d mydb > backup.sql

# Restore
psql -h localhost -U postgres -d mydb < backup.sql

# Continuous archiving
archive_mode = on
archive_command = 'cp %p /backup/archive/%f'
```

## Segurança

### SQL Injection Prevention

**✅ Prepared Statements:**
```typescript
// Seguro
await db.query('SELECT * FROM users WHERE email = ?', [email]);

// ❌ Vulnerável
await db.query(`SELECT * FROM users WHERE email = '${email}'`);
```

### Least Privilege

```sql
-- Usuário de aplicação com permissões limitadas
CREATE USER app_user WITH PASSWORD 'secure_password';
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO app_user;
REVOKE DROP, TRUNCATE ON ALL TABLES IN SCHEMA public FROM app_user;
```

### Encryption

- Dados em trânsito: SSL/TLS
- Dados em repouso: Disk encryption
- Dados sensíveis: Column-level encryption

## Monitoring

### Métricas Essenciais

- Query performance (slow query log)
- Connection pool usage
- Cache hit ratio
- Disk usage
- Replication lag

```sql
-- PostgreSQL slow queries
ALTER DATABASE mydb SET log_min_duration_statement = 1000; # Log queries > 1s
```

## Anti-Patterns

❌ Não usar prepared statements
❌ SELECT * em produção
❌ Não criar índices em foreign keys
❌ Migrations sem rollback
❌ Não fazer backups
❌ Não monitorar slow queries
❌ Usar ORM sem entender SQL gerado
❌ Não usar transactions para operações relacionadas
