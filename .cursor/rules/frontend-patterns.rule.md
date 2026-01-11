# Frontend Patterns

## Estrutura de Componentes

### Organização de Arquivos

```
src/
├── components/
│   ├── ui/              # Componentes base (Button, Input, etc)
│   ├── features/        # Componentes específicos de features
│   └── layouts/         # Componentes de layout (Header, Sidebar)
├── hooks/               # Custom hooks
├── services/            # API clients e lógica de negócio
├── utils/               # Funções utilitárias puras
├── types/               # TypeScript types e interfaces
└── styles/              # Styles globais e temas
```

### Responsabilidades

**UI Components:**
- Apenas apresentação
- Props bem definidas
- Sem lógica de negócio
- Reutilizáveis

**Feature Components:**
- Composição de UI components
- State management local
- Comunicação com services
- Lógica de negócio específica

**Layout Components:**
- Estrutura da página
- Posicionamento
- Responsividade

## State Management

### Regras por Escopo

**Local State (useState):**
- UI state (modals abertos, tabs selecionadas)
- Form inputs não controlados
- Estado que não precisa persistir

**Context (useContext):**
- Tema da aplicação
- Informações do usuário logado
- Configurações globais
- Evitar para dados que mudam frequentemente

**Server State (React Query/SWR):**
- Dados do backend
- Cache automático
- Revalidação
- Optimistic updates

**URL State (useSearchParams):**
- Filtros de listagem
- Paginação
- Estado que deve ser compartilhável via link

### Anti-Patterns

❌ Prop drilling excessivo (> 3 níveis)
❌ Tudo em Context global
❌ Estado duplicado entre local e server
❌ Não usar URL state para filtros

## Styling

### Estratégias Aceitas

**CSS Modules:**
```tsx
import styles from './Button.module.css';

<button className={styles.primary}>Click me</button>
```

**Styled Components:**
```tsx
const Button = styled.button`
  background: ${props => props.theme.primary};
`;
```

**Tailwind (se adotado):**
```tsx
<button className="bg-blue-500 hover:bg-blue-600">
  Click me
</button>
```

### Regras

- Consistência: escolher uma estratégia por projeto
- Não misturar inline styles com CSS modules
- Usar variáveis CSS para temas
- Mobile-first para responsividade

## Performance

### Otimizações Essenciais

**Code Splitting:**
```tsx
const Dashboard = lazy(() => import('./Dashboard'));

<Suspense fallback={<Loading />}>
  <Dashboard />
</Suspense>
```

**Memoization:**
```tsx
// Apenas quando necessário, não por padrão
const MemoizedComponent = memo(ExpensiveComponent);

const memoizedValue = useMemo(() => 
  expensiveCalculation(data), 
  [data]
);

const memoizedCallback = useCallback(() => 
  handleClick(id), 
  [id]
);
```

**Virtualização:**
```tsx
// Para listas grandes (> 100 itens)
import { VirtualList } from 'react-virtual';
```

### Quando Otimizar

✅ Listas com centenas de itens
✅ Cálculos complexos em render
✅ Componentes que re-renderizam frequentemente
✅ Bundle size > 500KB

❌ Otimização prematura
❌ Componentes simples
❌ Listas pequenas (< 50 itens)

## Acessibilidade

### Checklist Mínimo

- [ ] HTML semântico (button, nav, main)
- [ ] Labels em todos inputs
- [ ] Alt text em imagens
- [ ] Navegação por teclado funcional
- [ ] Focus visível
- [ ] ARIA quando necessário

## Testes

### O que Testar

**Testes Unitários:**
- Lógica de negócio em hooks
- Funções utilitárias
- Formatadores e validators

**Testes de Componente:**
- Renderização correta
- Interações do usuário
- Estados diferentes
- Edge cases

**Testes de Integração:**
- Fluxos completos
- Comunicação com API
- Navegação entre páginas

### Ferramentas

- Jest/Vitest para testes unitários
- React Testing Library para componentes
- Playwright/Cypress para E2E

## Anti-Patterns Gerais

❌ Componentes com > 300 linhas
❌ useEffect sem array de dependências
❌ Fetch de dados em useEffect (usar React Query)
❌ Múltiplos retornos condicionais complexos
❌ Lógica de negócio dentro de components UI
❌ Não tratar loading e error states
