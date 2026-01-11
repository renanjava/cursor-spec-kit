# Exemplos de Workflow

## Exemplo 1: Nova Feature com TDD

**Plano:** `plan-tdd.md`

1. **Contexto:**
   "Preciso criar uma função que calcula o preço do frete baseado no peso e distância."

2. **Teste (Red):**
   ```typescript
   // shipping.spec.ts
   describe('calculateShipping', () => {
     it('deve cobrar 10 reais para peso < 1kg e dist < 100km', () => {
       expect(calculateShipping(0.5, 50)).toBe(10);
     });
   });
   ```

3. **Implementação (Green):**
   ```typescript
   export function calculateShipping(weight: number, distance: number) {
     if (weight < 1 && distance < 100) return 10;
     return 0;
   }
   ```

4. **Refatoração (Refactor):**
   Criar constantes para valores mágicos, tipos para input, etc.

## Exemplo 2: Correção de Bug

**Plano:** `plan-bug-fix.md`

1. **Bug:** "Usuários com email maiúsculo não conseguem logar."

2. **Reprodução:**
   Testar login com "USER@EXAMPLE.COM" -> Falha.

3. **Teste de Regressão:**
   ```typescript
   it('deve aceitar emails case-insensitive', async () => {
     await register('teste@example.com');
     const result = await login('TESTE@EXAMPLE.COM');
     expect(result.success).toBe(true);
   });
   ```

4. **Fix:**
   ```typescript
   // auth.service.ts
   const normalizedEmail = email.toLowerCase();
   ```

5. **Validação:**
   Rodar testes de auth.

## Exemplo 3: Interação com Agente IA

**Arquivo:** `.cursor/rules/ai-interaction.rule.md`

**Prompt Eficiente:**
> "Estou seguindo o `plan-tdd.md`.
> Crie apenas o arquivo de teste `shipping.spec.ts` para o cálculo de frete.
> Regras de negócio:
> - Base R$10
> - +R$1 por kg adicional
> - +R$0.50 por km acima de 100km
> Não implemente a função, apenas os testes cobrindo esses casos."

**Prompt Ineficiente:**
> "Faz uma função de frete pra mim."
