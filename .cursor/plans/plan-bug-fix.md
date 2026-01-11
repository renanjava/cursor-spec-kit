# Bug Fix Plan

Objetivo: Corrigir bugs de forma estruturada e prevenir regressões.

## Passos obrigatórios:

1. **Reproduzir o Bug**
   - Documentar passos exatos para reprodução
   - Identificar condições necessárias
   - Capturar estado atual vs. esperado

2. **Criar Teste de Regressão**
   - Escrever teste que falha com o bug
   - Garantir que o teste é determinístico
   - Cobrir edge cases relacionados

3. **Implementar Correção Mínima**
   - Mudançasurgical apenas no necessário
   - Evitar refatorações grandes
   - Manter compatibilidade

4. **Validar Abrangência**
   - Executar suite completa de testes
   - Verificar que não quebrou funcionalidades
   - Testar manualmente cenários críticos

## Regras:

- Priorizar correção sobre otimização
- Documentar causa raiz no commit
- Marcar se necessita backport
- Considerar se precisa hotfix

## Entregáveis:

- Bug reproduzido e documentado
- Teste de regressão passando
- Fix validado
- Documentação de causa raiz
