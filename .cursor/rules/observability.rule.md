# Observability Rules

## Princípios

- Sistemas devem ser observáveis em produção
- Logs, métricas e traces complementam-se
- Nunca logar dados sensíveis
- Estruturar dados para facilitar busca

## Logging

### Níveis de Log

**DEBUG:**
- Informações detalhadas para debugging
- Apenas em desenvolvimento
- Desabilitado em produção

**INFO:**
- Eventos normais do sistema
- Início/fim de operações importantes
- Mudanças de estado significativas

**WARN:**
- Situações anormais mas recuperáveis
- Uso de fallbacks
- Depreciações

**ERROR:**
- Erros que requerem atenção
- Falhas em operações críticas
- Exceptions não esperadas

### Formato Estruturado

```typescript
// ✅ JSON estruturado
logger.info('User login', {
  userId: user.id,
  email: user.email,
  timestamp: new Date().toISOString(),
  ip: req.ip,
  correlationId: req.headers['x-correlation-id']
});

// ❌ String não estruturada
console.log(`User ${user.email} logged in`);
```

### Contexto Essencial

Sempre incluir:
- Timestamp ISO 8601
- Correlation ID (para rastrear requests)
- User ID (quando aplicável)
- Operação sendo executada
- Duração (para operações lentas)

```typescript
const logger = winston.createLogger({
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  )
});
```

### Dados Sensíveis

**❌ Nunca logar:**
- Senhas
- Tokens de autenticação
- Números de cartão de crédito
- Dados pessoais sensíveis (CPF, RG)
- Chaves de API

**✅ Pode logar:**
- User IDs (não email completo)
- Timestamps
- Status codes
- Tipos de operação
- Duração de operações

### Sanitização

```typescript
const sanitizeUser = (user) => ({
  id: user.id,
  email: maskEmail(user.email), // j***@example.com
  role: user.role
  // Omitir password, tokens, etc
});

logger.info('User created', { user: sanitizeUser(newUser) });
```

## Métricas

### O que Medir

**Latência:**
```typescript
const start = Date.now();
await processOrder(order);
metrics.histogram('order_processing_duration_ms', Date.now() - start);
```

**Taxa de Erro:**
```typescript
try {
  await fetchData();
  metrics.increment('api_requests_success');
} catch (error) {
  metrics.increment('api_requests_error');
  throw error;
}
```

**Throughput:**
```typescript
metrics.increment('orders_processed_total');
metrics.gauge('active_connections', server.connections);
```

### RED Method

Para cada serviço, medir:
- **R**ate - Requisições por segundo
- **E**rrors - Taxa de erro
- **D**uration - Latência

### USE Method

Para recursos, medir:
- **U**tilization - % de uso
- **S**aturation - Fila de espera
- **E**rrors - Taxa de erro

## Tracing Distribuído

### Correlation IDs

```typescript
// Middleware para gerar/propagar correlation ID
app.use((req, res, next) => {
  req.correlationId = req.headers['x-correlation-id'] || uuidv4();
  res.setHeader('x-correlation-id', req.correlationId);
  next();
});

// Usar em logs e chamadas externas
logger.info('Processing request', { correlationId: req.correlationId });

await fetch(externalAPI, {
  headers: {
    'x-correlation-id': req.correlationId
  }
});
```

### Spans

```typescript
const tracer = require('dd-trace').init();

app.get('/api/orders/:id', async (req, res) => {
  const span = tracer.startSpan('get_order');
  
  try {
    const order = await db.query('SELECT * FROM orders WHERE id = ?', [req.params.id]);
    span.setTag('order.id', req.params.id);
    span.setTag('order.status', order.status);
    
    res.json(order);
  } catch (error) {
    span.setTag('error', true);
    span.log({ event: 'error', message: error.message });
    throw error;
  } finally {
    span.finish();
  }
});
```

## Alertas

### Quando Alertar

**Crítico (PagerDuty):**
- Serviço down
- Taxa de erro > 5%
- Latência p99 > 5s
- Falha em operações de pagamento

**Aviso (Slack):**
- Taxa de erro > 1%
- Latência p95 > 2s
- Uso de CPU > 80%
- Disco > 85% cheio

### Runbooks

Para cada alerta, documentar:
- O que está acontecendo
- Impacto no usuário
- Como investigar
- Como resolver
- Quando escalar

```markdown
## Alert: High Error Rate

**Descrição:** Taxa de erro > 5% nos últimos 5 minutos

**Impacto:** Usuários podem estar recebendo erros 500

**Investigação:**
1. Checar logs: `kubectl logs -l app=api --since=10m | grep ERROR`
2. Verificar métricas de database
3. Checar serviços externos (status pages)

**Resolução:**
- Se database: escalar replicas
- Se serviço externo: ativar fallback
- Se bug: rollback para versão anterior

**Escalar para:** Time de infraestrutura se persistir > 15min
```

## Dashboards

### Essenciais

**Visão Geral:**
- Requests/second
- Error rate
- Latência (p50, p95, p99)
- Active users

**Performance:**
- Response time por endpoint
- Database query time
- External API latency
- Cache hit rate

**Recursos:**
- CPU usage
- Memory usage
- Disk I/O
- Network bandwidth

**Negócio:**
- Conversão de checkout
- Receita por hora
- Usuários ativos
- Itens no carrinho

## Ferramentas

### Stack Recomendado

**Logs:** ELK (Elasticsearch, Logstash, Kibana) ou Datadog
**Métricas:** Prometheus + Grafana ou Datadog
**Tracing:** Jaeger ou Datadog APM
**Alertas:** PagerDuty, Opsgenie, ou Alertmanager

## Anti-Patterns

❌ Logar em arquivos locais em produção
❌ Não estruturar logs (apenas strings)
❌ Logar dados sensíveis
❌ Alertas sem runbooks
❌ Não correlacionar requests
❌ Métricas sem labels/tags
❌ Não medir operações críticas
❌ Dashboards sem contexto de negócio
