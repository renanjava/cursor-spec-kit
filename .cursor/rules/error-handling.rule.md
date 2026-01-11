# Error Handling Rules

## Princípios Fundamentais

- Falhar rápido e de forma clara
- Nunca engolir erros silenciosamente
- Logar contexto suficiente para debugging
- Usar tipos de erro específicos

## Padrões por Tipo de Erro

### Erros de Validação
- Lançar erros específicos com mensagens claras
- Incluir campo/valor que falhou
- Retornar status 400 em APIs

```javascript
if (!email.includes('@')) {
  throw new ValidationError('Email inválido', { field: 'email', value: email });
}
```

### Erros de Negócio
- Custom error classes
- Mensagens amigáveis ao usuário
- Códigos de erro documentados

```javascript
class InsufficientFundsError extends Error {
  constructor(available, required) {
    super(`Saldo insuficiente: R$${available} disponível, R$${required} necessário`);
    this.code = 'INSUFFICIENT_FUNDS';
  }
}
```

### Erros de Infraestrutura
- Retry com exponential backoff
- Circuit breaker para serviços externos
- Logging detalhado

```javascript
async function fetchWithRetry(url, retries = 3) {
  try {
    return await fetch(url);
  } catch (error) {
    if (retries > 0 && isRetryable(error)) {
      await sleep(Math.pow(2, 3 - retries) * 1000);
      return fetchWithRetry(url, retries - 1);
    }
    throw error;
  }
}
```

### Erros Críticos
- Alertas automáticos
- Logging estruturado completo
- Stack trace preservado

## Logging de Erros

Sempre incluir:
- Timestamp
- Contexto da operação
- User ID se relevante
- Correlation ID para rastreamento
- Stack trace completo

Nunca incluir:
- Senhas ou tokens
- Dados pessoais sensíveis
- Chaves de API

## Anti-Patterns

❌ Nunca fazer:
```javascript
try {
  riskyOperation();
} catch (e) {
  // Ignora erro
}
```

❌ Catch genérico sem ação:
```javascript
try {
  complexOperation();
} catch (error) {
  console.log('Erro');
}
```

✅ Preferir:
```javascript
try {
  complexOperation();
} catch (error) {
  logger.error('Falha em complexOperation', {
    error: error.message,
    stack: error.stack,
    context: { userId, operationId }
  });
  throw error;
}
```

## Em Produção

- Sanitizar mensagens de erro para usuários
- Nunca expor stack traces
- Logar erros centralizadamente
- Monitorar taxa de erro

## Testes

Sempre testar:
- Casos de erro esperados
- Timeouts
- Falhas de rede
- Dados inválidos
- Estados inconsistentes
