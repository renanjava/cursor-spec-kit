# AI Interaction Guidelines

## Princípio

Agentes de IA são ferramentas poderosas, mas requerem contexto claro e instruções específicas para gerarem código de qualidade.

## Ao Solicitar Geração de Código

### Sempre Fornecer

**Contexto Completo:**
- Qual problema está resolvendo
- Por que está fazendo isso agora
- Onde o código se encaixa na arquitetura

**Requisitos Específicos:**
- Funcionalidades exatas necessárias
- Edge cases a considerar
- Performance requirements

**Constraints Técnicas:**
- Stack tecnológica em uso
- Padrões do projeto
- Limitações de compatibilidade

**Exemplos Quando Relevante:**
- Código similar existente
- Formato esperado de entrada/saída
- Estilo de código do projeto

### Sempre Pedir

**Explicação da Solução:**
- Como a solução funciona
- Por que essa abordagem
- Quais partes são críticas

**Alternativas Consideradas:**
- Outras abordagens possíveis
- Trade-offs de cada uma
- Por que essa foi escolhida

**Testes Incluídos:**
- Casos de sucesso
- Casos de erro
- Edge cases

**Trade-offs da Abordagem:**
- O que ganhamos
- O que perdemos
- Quando reconsiderar

## Exemplos de Prompts

### ❌ Prompt Vago
```
Crie um componente de login
```

### ✅ Prompt Específico
```
Crie um componente React de login que:

Contexto:
- Aplicação web de e-commerce
- Usuários já podem se registrar
- Precisamos adicionar login

Requisitos:
- Email e senha como inputs
- Validação client-side antes de submit
- Loading state durante autenticação
- Mensagens de erro amigáveis
- Redirect para dashboard após sucesso

Constraints:
- Usar React 18 com hooks
- Seguir nosso design system (src/components/ui)
- Usar nosso AuthContext existente
- Validação com Zod

Por favor incluir:
- Testes unitários com React Testing Library
- Casos de erro (credenciais inválidas, network error)
- Explicação de decisões de state management
```

## Workflow Recomendado

1. **Escolher Plano Apropriado**
   - Usar plan-tdd.md se precisa ser testável
   - Usar plan-fast-feature.md se é prototipo
   - Usar plan-spike.md se está explorando

2. **Fornecer Contexto**
   - Explicar o plano escolhido
   - Dar contexto arquitetural
   - Especificar requisitos

3. **Validar Proposta**
   - Pedir explicação antes de implementar
   - Revisar abordagem proposta
   - Confirmar que faz sentido

4. **Implementar Incrementalmente**
   - Pequenas mudanças de cada vez
   - Validar cada etapa
   - Iterar se necessário

5. **Review Final**
   - Usar code-review.rule.md checklist
   - Remover comentários gerados
   - Validar testes

## Evitar

❌ **Prompts Vagos:**
- "Faça um componente"
- "Adicione validação"
- "Melhore a performance"

❌ **Aceitar Sem Entender:**
- Código sem explicação
- Soluções complexas sem justificativa
- Mudanças sem testes

❌ **Gerar Sem Plano:**
- Código sem escolher workflow
- Mudanças sem explicar impacto
- Features sem validação

❌ **Não Revisar:**
- Aceitar código sem ler
- Commitar sem remover comentários
- Não validar comportamento

## Iteração e Refinamento

Se o código gerado não está ideal:

1. **Seja Específico no Feedback:**
   ❌ "Isso não está bom"
   ✅ "A função está fazendo muitas coisas. Podemos extrair a validação para uma função separada?"

2. **Peça Explicações:**
   - "Por que usou essa estrutura de dados?"
   - "Quais são os trade-offs dessa abordagem?"
   - "Existe uma forma mais simples?"

3. **Proponha Alternativas:**
   - "E se usarmos X ao invés de Y?"
   - "Podemos simplificar removendo Z?"

## Manutenção de Contexto

Para melhor qualidade:
- Usar .cursor/rules para padrões consistentes
- Manter architecture.md atualizado
- Documentar decisões importantes
- Referenciar código similar existente
