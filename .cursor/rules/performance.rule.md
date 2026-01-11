# Performance Rules

## Princípio

Otimize apenas após medir. Performance importa, mas manutenibilidade também.

## Web Performance

### Core Web Vitals

**LCP (Largest Contentful Paint):**
- Meta: < 2.5s
- Otimizações:
  - Otimizar imagens (WebP, lazy loading)
  - Server-side rendering crítico
  - CDN para assets estáticos
  - Preload recursos críticos

**FID (First Input Delay):**
- Meta: < 100ms
- Otimizações:
  - Code splitting
  - Defer JavaScript não crítico
  - Web Workers para tarefas pesadas
  - Reduzir long tasks

**CLS (Cumulative Layout Shift):**
- Meta: < 0.1
- Otimizações:
  - Dimensões fixas em imagens/vídeos
  - Reservar espaço para ads/embeds
  - Evitar inserir conteúdo acima de conteúdo existente
  - Usar CSS aspect-ratio

### Bundle Size

**Metas:**
- Initial bundle: < 200KB gzipped
- Total JavaScript: < 500KB gzipped

**Estratégias:**
```tsx
// Code splitting por rota
const Dashboard = lazy(() => import('./Dashboard'));
const Settings = lazy(() => import('./Settings'));

// Tree shaking
import { debounce } from 'lodash-es'; // ✅
import _ from 'lodash'; // ❌

// Analisar bundle
npm run build -- --analyze
```

### Imagens

```html
<!-- Formatos modernos com fallback -->
<picture>
  <source srcset="image.avif" type="image/avif">
  <source srcset="image.webp" type="image/webp">
  <img src="image.jpg" alt="Descrição" loading="lazy">
</picture>

<!-- Responsive images -->
<img 
  srcset="small.jpg 480w, medium.jpg 800w, large.jpg 1200w"
  sizes="(max-width: 600px) 480px, (max-width: 900px) 800px, 1200px"
  src="medium.jpg" 
  alt="Descrição"
>
```

### Caching

**Browser Caching:**
```
# Assets com hash
Cache-Control: public, max-age=31536000, immutable

# HTML
Cache-Control: no-cache

# API responses
Cache-Control: private, max-age=300
```

**Service Workers:**
```javascript
// Cache-first para assets estáticos
self.addEventListener('fetch', (event) => {
  if (event.request.destination === 'image') {
    event.respondWith(
      caches.match(event.request).then((response) => {
        return response || fetch(event.request);
      })
    );
  }
});
```

## Backend Performance

### Database

**N+1 Queries - Evitar:**
```typescript
// ❌ N+1
const users = await User.findAll();
for (const user of users) {
  user.posts = await Post.findAll({ where: { userId: user.id } });
}

// ✅ Eager loading
const users = await User.findAll({
  include: [Post]
});
```

**Indexação:**
```sql
-- Queries frequentes devem ter índices
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_user_created ON orders(user_id, created_at);

-- Verificar uso de índices
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'test@example.com';
```

**Connection Pooling:**
```typescript
const pool = new Pool({
  max: 20, // máximo de conexões
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
});
```

### Caching

**Redis para dados acessados frequentemente:**
```typescript
async function getUser(id: string) {
  const cached = await redis.get(`user:${id}`);
  if (cached) return JSON.parse(cached);
  
  const user = await db.users.findById(id);
  await redis.setex(`user:${id}`, 3600, JSON.stringify(user));
  return user;
}
```

**Cache Invalidation:**
```typescript
async function updateUser(id: string, data: any) {
  const user = await db.users.update(id, data);
  await redis.del(`user:${id}`); // Invalidar cache
  return user;
}
```

### Rate Limiting

```typescript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100,
  standardHeaders: true,
  legacyHeaders: false,
});

app.use('/api/', limiter);
```

## React Performance

### Memoization

**Usar com moderação:**
```tsx
// ✅ Componente pesado que re-renderiza frequentemente
const ExpensiveComponent = memo(({ data }) => {
  // Renderização cara
});

// ✅ Callback passado para child que usa useEffect
const handleClick = useCallback(() => {
  doSomething(id);
}, [id]);

// ✅ Cálculo caro
const sortedData = useMemo(() => 
  heavySort(data), 
  [data]
);

// ❌ Componente simples
const Button = memo(({ label }) => <button>{label}</button>);
```

### Virtualização

```tsx
// Para listas grandes (> 100 itens)
import { FixedSizeList } from 'react-window';

<FixedSizeList
  height={600}
  itemCount={items.length}
  itemSize={50}
  width="100%"
>
  {({ index, style }) => (
    <div style={style}>{items[index].name}</div>
  )}
</FixedSizeList>
```

### Debouncing/Throttling

```tsx
import { debounce } from 'lodash-es';

const SearchInput = () => {
  const [search, setSearch] = useState('');
  
  const debouncedSearch = useMemo(
    () => debounce((value) => {
      performSearch(value);
    }, 300),
    []
  );
  
  const handleChange = (e) => {
    setSearch(e.target.value);
    debouncedSearch(e.target.value);
  };
  
  return <input value={search} onChange={handleChange} />;
};
```

## Benchmark e Profiling

### Frontend

```javascript
// Performance API
performance.mark('start-operation');
performOperation();
performance.mark('end-operation');
performance.measure('operation', 'start-operation', 'end-operation');

// Chrome DevTools
// 1. Performance tab
// 2. Start recording
// 3. Perform action
// 4. Stop recording
// 5. Analyze flamegraph
```

### Backend

```typescript
// Custom timing
const start = Date.now();
await expensiveOperation();
const duration = Date.now() - start;
logger.info('Operation completed', { duration });

// Node.js profiling
node --prof app.js
node --prof-process isolate-*.log > processed.txt
```

### Load Testing

```bash
# Artillery
artillery quick --count 100 --num 10 https://api.example.com

# k6
k6 run --vus 100 --duration 30s script.js
```

## Anti-Patterns

❌ Otimização prematura
❌ Memoizar tudo sem medir
❌ Não usar índices em queries frequentes
❌ Carregar todas dependências no initial bundle
❌ Não lazy load imagens
❌ Não usar CDN para assets estáticos
❌ Não implementar caching
❌ Bloquear main thread com operações pesadas

## Checklist de Performance

Antes de ir para produção:
- [ ] Lighthouse score > 90
- [ ] Bundle size analisado e otimizado
- [ ] Imagens otimizadas (WebP/AVIF)
- [ ] Lazy loading implementado
- [ ] Database queries otimizadas
- [ ] Caching implementado
- [ ] CDN configurado
- [ ] Compressão habilitada (gzip/brotli)
- [ ] Load testing executado
