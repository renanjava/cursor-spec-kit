# Documentation Rules

## Princípio

Documentação deve complementar código auto-explicativo, não substituí-lo.

## Comentários no Código

### Quando Comentar

Apenas para explicar "por quê":
- Decisões não óbvias
- Workarounds necessários
- Otimizações complexas
- Referências a bugs externos

### Quando NÃO Comentar

Nunca para explicar "o quê":
- Código auto-explicativo
- Nomes de funções/variáveis claros
- Lógica simples

## API Documentation

### Funções Públicas

Usar JSDoc/TSDoc:
```typescript
/**
 * Calcula o valor total do pedido incluindo impostos e descontos.
 * 
 * @param items - Lista de itens do pedido
 * @param discountCode - Código de desconto opcional
 * @returns Valor total em centavos
 * @throws {ValidationError} Se items estiver vazio
 */
function calculateTotal(items: Item[], discountCode?: string): number {
  // implementação
}
```

### Classes e Interfaces

Documentar propósito e responsabilidades:
```typescript
/**
 * Gerencia autenticação de usuários via JWT.
 * Responsável por login, logout e refresh de tokens.
 */
class AuthService {
  // implementação
}
```

## README

Sempre incluir:
- Descrição do projeto em 1-2 parágrafos
- Instruções de setup
- Variáveis de ambiente necessárias
- Como rodar testes
- Como fazer deploy

Exemplo:
```markdown
# Nome do Projeto

Breve descrição do que o projeto faz.

## Setup

```bash
npm install
cp .env.example .env
# Editar .env com suas credenciais
npm run dev
```

## Variáveis de Ambiente

- `DATABASE_URL` - String de conexão PostgreSQL
- `JWT_SECRET` - Chave para assinatura de tokens

## Testes

```bash
npm test
```
```

## Arquitetura

Manter `architecture.md` atualizado:
- Decisões arquiteturais importantes
- Padrões adotados
- Estrutura de pastas
- Fluxo de dados

## ADRs (Architecture Decision Records)

Para decisões técnicas significativas:
```markdown
# ADR-001: Escolha de PostgreSQL sobre MongoDB

## Contexto
Precisamos armazenar dados transacionais com forte consistência.

## Decisão
Usar PostgreSQL como banco de dados principal.

## Consequências
- Transações ACID garantidas
- Schema rígido facilita migrations
- Melhor para queries complexas
- Trade-off: menos flexibilidade em schema
```

## Commits

Seguir Conventional Commits:
```
feat: adiciona autenticação via OAuth
fix: corrige race condition no checkout
refactor: extrai lógica de validação para service
docs: atualiza README com setup de dev
test: adiciona testes para edge cases de pagamento
```

Incluir contexto no corpo se necessário:
```
fix: corrige timeout em importação de dados

Importações grandes estavam causando timeout do nginx.
Aumentado tempo limite para 5 minutos e adicionado
progress logging.

Fixes #123
```

## Manutenção de Documentação

Quando mudar código:
- [ ] Atualizar README se API mudou
- [ ] Atualizar JSDoc se assinatura mudou
- [ ] Atualizar exemplos se quebraram
- [ ] Criar ADR se decisão arquitetural
- [ ] Atualizar variáveis de ambiente se necessário

## Anti-Patterns

❌ Documentação desatualizada
❌ Comentários que duplicam código
❌ READMEs sem instruções de setup
❌ Commits vagos ("fix bug", "update")
