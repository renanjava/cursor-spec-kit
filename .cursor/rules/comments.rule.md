# Comments Management Rules

## Filosofia

Comentários podem ajudar durante o desenvolvimento, mas frequentemente se tornam obsoletos e prejudiciais a longo prazo.

## Durante Desenvolvimento

Permitido:
- Comentários explicativos para facilitar compreensão
- TODOs e FIXMEs temporários
- Anotações de debugging
- Notas sobre decisões em andamento

## Antes do Commit

Obrigatório remover:
- Todos os comentários gerados automaticamente por IA
- Comentários que explicam "o quê" o código faz
- TODOs que não serão endereçados imediatamente
- Código comentado não utilizado
- Debug statements e console.logs

## Comentários Permitidos no Código Final

Manter apenas se:
- Explicam "por quê" de uma decisão não óbvia
- Documentam workarounds necessários
- Alertam sobre comportamentos contra-intuitivos
- Referenciam bugs ou issues externos
- Marcam otimizações complexas

## Formato de Comentários Aceitos

```javascript
// Por quê: Precisamos usar setTimeout ao invés de requestAnimationFrame
// devido a um bug no Safari 15 que causa race condition no WebGL context
// Ref: https://bugs.webkit.org/show_bug.cgi?id=234567
setTimeout(() => initGL(), 0);
```

## Anti-Patterns

❌ Nunca fazer:
```javascript
// Loop através dos usuários
users.forEach(user => {
  // Processa cada usuário
  processUser(user);
});
```

✅ Preferir:
```javascript
users.forEach(user => processUser(user));
```

## Exceções

Permitido manter:
- JSDoc/TSDoc para APIs públicas
- Comentários de licença obrigatórios
- Pragma directives (eslint-disable, @ts-ignore com justificativa)
- Documentação de segurança crítica

## Validação

Antes de commitar, revisar:
- Nenhum comentário explica código óbvio
- Comentários restantes agregam valor real
- Código é auto-explicativo sem comentários
