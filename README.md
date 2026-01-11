# Cursor Spec Kit - Desenvolvimento Assistido por IA

> Padrões e workflows para desenvolvimento de software de alta qualidade com agentes de IA.

## 📋 Visão Geral

Este spec kit define um conjunto abrangente de regras, planos e documentação para potencializar o desenvolvimento assistido por agentes de IA. O foco está em qualidade de código, testabilidade, documentação consistente e gestão consciente de trade-offs.

## 🎯 Objetivos

- ✅ **Garantir testabilidade** - Todo código é projetado para ser testável
- ✅ **Padronizar explicações** - Formato consistente de documentação
- ✅ **Reduzir dívida técnica** - Decisões conscientes e documentadas
- ✅ **Aumentar previsibilidade** - Workflows claros para diferentes contextos
- ✅ **Controlar trade-offs** - Documentar decisões e suas consequências
- ✅ **Melhorar interação com IA** - Prompts claros e contextualizados

## 📁 Estrutura

### Rules (`.cursor/rules/`)

Regras aplicadas automaticamente em toda interação:

**Qualidade e Segurança:**
- `agent-safety.rule.md` - Previne mudanças destrutivas não intencionais
- `testability.rule.md` - Garante código testável com separação de lógica
- `code-review.rule.md` - Checklist pré-commit abrangente
- `error-handling.rule.md` - Padrões de tratamento de erros

**Documentação:**
- `explanation-format.rule.md` - Formato padronizado para explicações
- `documentation.rule.md` - Padrões de documentação e commits
- `comments.rule.md` - Gestão de comentários (permitidos durante dev, removidos antes de commit)
- `tradeoffs.rule.md` - Framework para documentar decisões técnicas

**Código e Arquitetura:**
- `frontend-patterns.rule.md` - Padrões para desenvolvimento frontend
- `api-design.rule.md` - Guidelines de design de APIs REST
- `async-concurrency.rule.md` - Gestão de código assíncrono
- `legacy-code.rule.md` - Abordagem segura para código existente

**Especializadas:**
- `accessibility.rule.md` - Padrões de acessibilidade WCAG
- `performance.rule.md` - Otimizações e benchmarking
- `observability.rule.md` - Logging, métricas e tracing
- `database.rule.md` - Migrations, indexação e segurança

**Interação com IA:**
- `ai-interaction.rule.md` - Como fazer prompts efetivos

### Plans (`.cursor/plans/`)

Workflows específicos para diferentes contextos (escolher explicitamente antes de gerar código):

- `plan-tdd.md` - Test-Driven Development rigoroso
- `plan-fast-feature.md` - Entregas rápidas com risco controlado
- `plan-refactor-first.md` - Melhorar testabilidade antes de evoluir
- `plan-spike.md` - Exploração técnica e prototipagem
- `plan-bug-fix.md` - Protocolo estruturado para correção de bugs
- `plan-optimization.md` - Melhorias de performance com benchmarking
- `plan-security-review.md` - Features sensíveis com threat modeling
- `plan-migration.md` - Mudanças arquiteturais incrementais

### Documentação

- `architecture.md` - Template para documentar arquitetura do projeto
- `README.md` - Este arquivo

## 🚀 Como Usar

### 1. Antes de Iniciar Desenvolvimento

Escolha o plano apropriado ao contexto:

```
Nova feature crítica → plan-tdd.md
Protótipo rápido → plan-fast-feature.md
Código legado → plan-refactor-first.md
Exploração técnica → plan-spike.md
Correção de bug → plan-bug-fix.md
Otimização → plan-optimization.md
Feature sensível → plan-security-review.md
Mudança arquitetural → plan-migration.md
```

### 2. Durante Desenvolvimento

As regras serão aplicadas automaticamente pelo agente IA:
- Código será gerado seguindo padrões definidos
- Explicações seguirão formato padronizado
- Validações de segurança serão aplicadas
- Comentários podem ser incluídos para facilitar compreensão

### 3. Antes de Commitar

Use o checklist em `code-review.rule.md`:
- [ ] Remover todos os comentários gerados (exceto "por quê")
- [ ] Testes passando
- [ ] Sem debug statements
- [ ] Documentação atualizada
- [ ] Validações de segurança

### 4. Ao Explicar Código

Seguir formato em `explanation-format.rule.md`:
1. Resumo
2. Responsabilidades
3. Fluxo de execução
4. Dependências
5. Efeitos colaterais
6. Casos de erro
7. Pontos de atenção
8. Sugestões

## 💡 Exemplo de Uso com IA

### ❌ Prompt Vago
```
Crie um componente de login
```

### ✅ Prompt Específico
```
[Escolher plan-tdd.md]

Criar componente React de login que:

Contexto:
- App de e-commerce, usuários já podem se registrar
- Usar nosso AuthContext existente em src/contexts/AuthContext.tsx

Requisitos:
- Inputs: email e senha
- Validação client-side com Zod
- Loading state durante autenticação
- Redirect para /dashboard após sucesso

Stack:
- React 18 com TypeScript
- Seguir design system em src/components/ui
- React Testing Library para testes

Incluir:
- Testes unitários completos
- Casos de erro (credenciais inválidas, network error)
- Explicação de decisões de state management
```

## 🧠 Gestão de Comentários

**Regra especial:** `comments.rule.md`

- ✅ **Durante desenvolvimento:** Comentários são permitidos e encorajados para facilitar compreensão
- ⚠️ **Antes do commit:** Remover TODOS os comentários gerados automaticamente
- ✅ **Exceção:** Manter apenas comentários que explicam "por quê" de decisões não óbvias

**Justificativa:** Comentários ajudam no momento, mas tendem a ficar obsoletos. Código auto-explicativo é preferível.

## 📊 Documentar Trade-offs

Use `tradeoffs.rule.md` para decisões técnicas:

```markdown
## Trade-off: Escolha de React Query

**Contexto:** Gerenciar estado de servidor

**Opções:** React Query, SWR, Custom hooks

**Escolha:** React Query

**Por quê:** Cache automático, retry logic, devtools excelentes

**Ganhamos:** Menos código, melhor DX
**Perdemos:** +45KB bundle, dependência extra

**Revisar quando:** Bundle size se tornar crítico
```

## 🔄 Manutenção do Spec Kit

### Periodicidade

- **Semanal:** Revisar se regras estão sendo seguidas
- **Mensal:** Atualizar `architecture.md` com novas decisões
- **Trimestral:** Avaliar necessidade de novas regras/planos
- **Anual:** Review completo e limpeza

### Customização

Este spec kit é um template. Customize conforme necessário:

1. **Adaptar regras** - Ajustar para sua stack específica
2. **Adicionar planos** - Criar workflows para seus contextos
3. **Atualizar architecture.md** - Documentar sua arquitetura real
4. **Criar templates** - Adicionar templates específicos do projeto

### Evolução

Quando adicionar novos arquivos:
- Manter consistência de formato
- Documentar justificativa
- Atualizar este README
- Comunicar ao time

## 📚 Recursos Adicionais

### Leitura Recomendada

- Clean Code (Robert C. Martin)
- Refactoring (Martin Fowler)
- Working Effectively with Legacy Code (Michael Feathers)
- Test Driven Development (Kent Beck)

### Ferramentas Complementares

- **Linting:** ESLint / Prettier
- **Testing:** Jest / Vitest / Playwright
- **Type Checking:** TypeScript
- **Git Hooks:** Husky + lint-staged
- **Documentation:** Storybook / Docusaurus

## 🤝 Contribuindo

Para melhorar este spec kit:

1. Identificar gap ou melhoria necessária
2. Criar/editar arquivo seguindo formato existente
3. Atualizar README se adicionar nova regra/plano
4. Testar em projeto real
5. Documentar resultado

## 📝 Licença

[Definir licença conforme necessidade do projeto]

---

**Última atualização:** 2026-01-11  
**Versão:** 2.0  
**Mantedor:** [Seu nome/time]
