# Anti-Patterns - O que Evitar

## Componentes "Deus"

### ❌ Ruim: Misturando tudo

```tsx
// NÃO FAÇA ISSO
export function Dashboard() {
  const [data, setData] = useState([]);
  const [filter, setFilter] = useState('');
  
  // Lógica de negócio misturada com UI
  useEffect(() => {
    fetch('/api/big-data').then(r => r.json()).then(d => {
      // Processamento pesado no componente
      const processed = d.map(item => ({ ...item, value: item.x * 2 }));
      setData(processed);
    });
  }, []);

  return (
    <div>
      {/* 500 linhas de JSX aqui */}
    </div>
  );
}
```

## Prop Drilling

### ❌ Ruim: Passando props 4 níveis

```tsx
<Page user={user} />
  <Header user={user} />
    <UserMenu user={user} />
      <Avatar user={user} />
```

### ✅ Solução: Composição ou Context

```tsx
<Page>
  <Header>
    <UserMenu />
  </Header>
</Page>
```

## Comentários Óbvios

### ❌ Ruim: Explicando "O Quê"

```typescript
// Incrementa o contador
count++;

// Filtra usuários ativos
const activeUsers = users.filter(u => u.active);
```

## Erros Silenciosos

### ❌ Ruim: Engolindo erros

```typescript
try {
  saveData();
} catch (e) {
  console.log('erro'); // Informação perdida
  return null; // O chamador não sabe que falhou
}
```

## Mágica Excessiva

### ❌ Ruim: Lógica implícita demais

```typescript
// O que isso faz? De onde vem 'data'?
// Por que 'process' muda o objeto global?
const x = process(true); 
```
