# Boas Práticas - Exemplos

## Componentes UI

### ✅ Bom: Separado e Tipado

```tsx
interface UserCardProps {
  name: string;
  email: string;
  role: 'admin' | 'user';
  onEdit: () => void;
}

export function UserCard({ name, email, role, onEdit }: UserCardProps) {
  return (
    <div className="card">
      <h3>{name}</h3>
      <p>{email}</p>
      <Badge variant={role === 'admin' ? 'primary' : 'secondary'}>
        {role}
      </Badge>
      <button onClick={onEdit} aria-label={`Editar ${name}`}>
        Editar
      </button>
    </div>
  );
}
```

## Tratamento de Erros

### ✅ Bom: Contextualizado

```typescript
try {
  await processPayment(amount);
} catch (error) {
  logger.error('Falha no pagamento', {
    amount,
    currency: 'BRL',
    error: error.message,
    code: error instanceof PaymentError ? error.code : 'UNKNOWN'
  });
  
  throw new BusinessError('Não foi possível processar o pagamento. Tente novamente.');
}
```

## Efeitos (useEffect)

### ✅ Bom: Com Cleanup e Dependências

```typescript
useEffect(() => {
  const subscription = dataSource.subscribe(id, handleData);
  
  // Cleanup function essencial
  return () => {
    subscription.unsubscribe();
  };
}, [id]); // Dependência explícita
```

## Comentários

### ✅ Bom: Explicando "Por Quê"

```typescript
// Precisamos adicionar um delay artificial aqui porque a API
// legado tem uma race condition se chamarmos imediatamente
// após a criação.
// Ticket relacionado: LEG-123
await sleep(1000);
```
