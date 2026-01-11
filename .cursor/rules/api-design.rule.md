# API Design Rules

## Princípios REST

### Recursos e URIs

**Boas Práticas:**
```
GET    /api/users          # Lista usuários
GET    /api/users/:id      # Busca usuário específico
POST   /api/users          # Cria usuário
PUT    /api/users/:id      # Atualiza usuário completo
PATCH  /api/users/:id      # Atualiza parcialmente
DELETE /api/users/:id      # Remove usuário
```

**Sub-recursos:**
```
GET /api/users/:id/orders           # Pedidos de um usuário
GET /api/orders/:id/items           # Items de um pedido
```

**Anti-Patterns:**
```
❌ /api/getUsers
❌ /api/user/delete
❌ /api/createNewUser
✅ GET  /api/users
✅ DELETE /api/users/:id
✅ POST /api/users
```

### HTTP Status Codes

**2xx Success:**
- `200 OK` - Sucesso geral (GET, PUT, PATCH)
- `201 Created` - Recurso criado (POST)
- `204 No Content` - Sucesso sem retorno (DELETE)

**4xx Client Errors:**
- `400 Bad Request` - Validação falhou
- `401 Unauthorized` - Não autenticado
- `403 Forbidden` - Não autorizado
- `404 Not Found` - Recurso não existe
- `409 Conflict` - Conflito (ex: email duplicado)
- `422 Unprocessable Entity` - Validação de negócio

**5xx Server Errors:**
- `500 Internal Server Error` - Erro inesperado
- `503 Service Unavailable` - Serviço indisponível

## Versionamento

### Estratégias

**URL Path (Recomendado):**
```
/api/v1/users
/api/v2/users
```

**Header:**
```
Accept: application/vnd.api.v2+json
```

### Regras

- Versão major quando breaking change
- Manter versão anterior por período de depreciação
- Documentar mudanças entre versões
- Avisar usuários com antecedência

## Request/Response

### Request Validation

```typescript
// Exemplo com Zod
const createUserSchema = z.object({
  name: z.string().min(2).max(100),
  email: z.string().email(),
  age: z.number().int().min(18).optional()
});

app.post('/api/users', async (req, res) => {
  try {
    const data = createUserSchema.parse(req.body);
    // processar...
  } catch (error) {
    return res.status(400).json({
      error: 'Validation failed',
      details: error.errors
    });
  }
});
```

### Response Format

**Sucesso:**
```json
{
  "data": {
    "id": "123",
    "name": "João",
    "email": "joao@example.com"
  }
}
```

**Lista com Paginação:**
```json
{
  "data": [...],
  "pagination": {
    "page": 1,
    "perPage": 20,
    "total": 156,
    "totalPages": 8
  }
}
```

**Erro:**
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Dados inválidos",
    "details": [
      {
        "field": "email",
        "message": "Email inválido"
      }
    ]
  }
}
```

## Autenticação e Autorização

### JWT Pattern

```typescript
// Headers
Authorization: Bearer <token>

// Validação
const authenticateJWT = (req, res, next) => {
  const token = req.headers.authorization?.split(' ')[1];
  
  if (!token) {
    return res.status(401).json({ error: 'Token não fornecido' });
  }
  
  try {
    const user = jwt.verify(token, process.env.JWT_SECRET);
    req.user = user;
    next();
  } catch {
    return res.status(401).json({ error: 'Token inválido' });
  }
};
```

### Autorização

```typescript
const requireRole = (role: string) => {
  return (req, res, next) => {
    if (req.user.role !== role) {
      return res.status(403).json({ error: 'Acesso negado' });
    }
    next();
  };
};

app.delete('/api/users/:id', 
  authenticateJWT, 
  requireRole('admin'), 
  deleteUser
);
```

## Rate Limiting

```typescript
import rateLimit from 'express-rate-limit';

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutos
  max: 100, // limite de 100 requests
  message: 'Muitas requisições, tente novamente mais tarde'
});

app.use('/api/', limiter);
```

## Documentação

### OpenAPI/Swagger

```yaml
paths:
  /api/users:
    get:
      summary: Lista usuários
      parameters:
        - name: page
          in: query
          schema:
            type: integer
      responses:
        200:
          description: Lista de usuários
          content:
            application/json:
              schema:
                type: object
                properties:
                  data:
                    type: array
                    items:
                      $ref: '#/components/schemas/User'
```

### Regras

- Documentar todos endpoints
- Incluir exemplos de request/response
- Documentar códigos de erro possíveis
- Manter docs sincronizadas com código

## Performance

### Paginação

```typescript
GET /api/users?page=1&perPage=20
```

### Filtros e Ordenação

```typescript
GET /api/users?status=active&sort=-createdAt&search=joão
```

### Field Selection

```typescript
GET /api/users?fields=id,name,email
```

### Caching

```typescript
// Cache-Control headers
res.set('Cache-Control', 'public, max-age=300'); // 5 minutos
```

## Segurança

### Checklist

- [ ] Validação de todos inputs
- [ ] Sanitização de dados
- [ ] Rate limiting implementado
- [ ] CORS configurado apropriadamente
- [ ] Headers de segurança (Helmet)
- [ ] HTTPS em produção
- [ ] Secrets em variáveis de ambiente
- [ ] SQL injection prevenido (prepared statements)
- [ ] XSS prevenido (sanitização)

## Anti-Patterns

❌ Verbos em URLs
❌ Retornar senhas ou tokens em responses
❌ Não validar inputs
❌ Expor stack traces em produção
❌ Não versionar API
❌ Não documentar endpoints
❌ Ignorar códigos de status HTTP corretos
