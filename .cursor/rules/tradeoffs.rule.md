# Trade-off Management

## Princípio

Toda decisão técnica envolve trade-offs. Documentá-los permite revisitar escolhas quando o contexto mudar.

## Quando Documentar Trade-offs

Sempre que:
- Escolher entre múltiplas abordagens válidas
- Priorizar um aspecto sobre outro
- Aceitar limitações conhecidas
- Fazer otimizações que adicionam complexidade
- Escolher dependências externas

## Formato de Documentação

### Template Básico

```markdown
## Trade-off: [Título Descritivo]

**Contexto:**
[Situação que requer a decisão]

**Opções Consideradas:**
1. Opção A - [Breve descrição]
2. Opção B - [Breve descrição]
3. Opção C - [Breve descrição]

**Escolha:**
Opção [X]

**Por quê:**
[Justificativa detalhada da escolha]

**O que ganhamos:**
- [Benefício 1]
- [Benefício 2]

**O que perdemos:**
- [Trade-off 1]
- [Trade-off 2]

**Quando Revisar:**
[Condições que podem invalidar esta decisão]
```

## Exemplos Práticos

### Exemplo 1: Escolha de Biblioteca

```markdown
## Trade-off: React Query vs. Custom Data Fetching

**Contexto:**
Precisamos gerenciar estado de dados assíncronos na aplicação.
Atualmente cada componente faz fetch próprio, causando duplicação.

**Opções Consideradas:**
1. React Query - Biblioteca completa de data fetching
2. SWR - Alternativa mais leve
3. Custom hooks - Implementação própria

**Escolha:**
React Query

**Por quê:**
- Time já conhece a biblioteca
- Suporte excelente a cache e invalidação
- Devtools facilitam debugging
- Comunidade ativa e documentação completa

**O que ganhamos:**
- Cache automático e inteligente
- Retry e error handling built-in
- Optimistic updates simplificados
- Menos código a manter

**O que perdemos:**
- +45KB no bundle (gzipped)
- Curva de aprendizado para novos devs
- Abstração adicional sobre fetch
- Dependency extra a manter

**Quando Revisar:**
- Se bundle size se tornar problema crítico
- Se surgir alternativa nativa do React
- Se precisarmos de controle mais fino sobre caching
```

### Exemplo 2: Performance vs. Manutenibilidade

```markdown
## Trade-off: Otimização de Query Complexa

**Contexto:**
Query de relatório levando 3s, usuários reclamando.
Identificamos que o JOIN complexo é o gargalo.

**Opções Consideradas:**
1. Materializar view - Pre-computar resultados
2. Otimizar query - Reescrever com subqueries
3. Cache de aplicação - Redis com TTL
4. Denormalizar dados - Duplicar informações

**Escolha:**
Materializar view com refresh incremental

**Por quê:**
- Reduz latência para sub-segundo
- Mantém lógica no banco (mais fácil debugar)
- Refresh pode rodar em background
- Não requer mudanças na aplicação

**O que ganhamos:**
- Performance excelente (3s → 200ms)
- Transparente para aplicação
- Manutenibilidade razoável

**O que perdemos:**
- Dados podem ficar até 5min desatualizados
- Complexidade adicional no schema
- Espaço adicional no banco
- Job de refresh a monitorar

**Quando Revisar:**
- Se dados precisarem ser real-time
- Se espaço em disco se tornar problema
- Se surgir alternativa com dados frescos e rápidos
```

### Exemplo 3: Segurança vs. UX

```markdown
## Trade-off: Session Timeout

**Contexto:**
Balancear segurança com experiência do usuário.

**Opções Consideradas:**
1. 15 minutos - Máxima segurança
2. 1 hora - Balance
3. 8 horas (dia de trabalho) - Máxima conveniência

**Escolha:**
1 hora com refresh token de 30 dias

**Por quê:**
- Usuários raramente ficam mais de 1h sem interagir
- Refresh automático é transparente
- Comprometimento razoável

**O que ganhamos:**
- Segurança adequada (limite exposição de token)
- UX boa (raros re-logins)
- Compliance com requisitos de segurança

**O que perdemos:**
- Não é o mais seguro possível
- Complexidade de refresh tokens
- Risco se refresh token vazar

**Quando Revisar:**
- Se houver incidentes de segurança
- Se compliance exigir timeout menor
- Se metrics mostrarem frustração de usuários
```

## Onde Documentar

### Decisões Arquiteturais
ADRs em `.cursor/architecture.md` ou arquivos separados

### Decisões de Implementação
Comentários no código com link para issue/doc

### Decisões de Produto
PRD ou design docs

### Decisões Temporárias
TODOs com justificativa inline

## Revisão de Trade-offs

Periodicidade:
- Revisão trimestral de decisões críticas
- Quando contexto mudar significativamente
- Se métricas indicarem problemas
- Durante refatorações grandes

## Anti-Patterns

❌ Não documentar escolhas
❌ Documentar sem incluir alternativas
❌ Omitir downsides da escolha
❌ Não revisar quando contexto muda
❌ Tomar decisões sem considerar trade-offs
