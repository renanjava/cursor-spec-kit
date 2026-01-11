# Code Review Checklist

Antes de considerar código pronto para commit, validar todos os itens:

## Funcionalidade

- [ ] Testes unitários passando
- [ ] Comportamento validado manualmente
- [ ] Edge cases identificados e cobertos
- [ ] Performance aceitável em casos realistas
- [ ] Compatibilidade com browsers/plataformas alvo

## Qualidade de Código

- [ ] Nomes de variáveis/funções descritivos
- [ ] Funções com responsabilidade única
- [ ] Sem duplicação óbvia de código
- [ ] Sem código comentado não utilizado
- [ ] Sem console.log ou debug statements
- [ ] Comentários removidos (exceto "por quê")
- [ ] Imports organizados e sem não utilizados

## Testes

- [ ] Cobertura adequada de casos de sucesso
- [ ] Casos de erro testados
- [ ] Testes são determinísticos
- [ ] Testes rodam rápido (< 100ms por teste unitário)
- [ ] Mocks minimizados

## Segurança

- [ ] Inputs validados no backend
- [ ] Dados sensíveis não expostos
- [ ] Sem SQL/NoSQL injection possível
- [ ] XSS prevention implementado
- [ ] Autenticação/autorização correta
- [ ] Rate limiting onde necessário

## Performance

- [ ] Sem N+1 queries
- [ ] Recursos liberados adequadamente
- [ ] Sem memory leaks óbvios
- [ ] Bundle size considerado (frontend)
- [ ] Lazy loading implementado onde faz sentido

## Acessibilidade (Frontend)

- [ ] HTML semântico usado
- [ ] Labels em todos os inputs
- [ ] Navegação por teclado funcional
- [ ] Contraste de cores adequado
- [ ] ARIA quando necessário

## Documentação

- [ ] README atualizado se necessário
- [ ] API pública documentada (JSDoc/TSDoc)
- [ ] Variáveis de ambiente documentadas
- [ ] Migration/setup steps se aplicável

## Git

- [ ] Commit message descritivo
- [ ] Conventional commits seguido
- [ ] Sem arquivos sensíveis commitados
- [ ] .gitignore atualizado se necessário

## Dívida Técnica

- [ ] Dívida técnica documentada explicitamente
- [ ] TODOs têm issue associado
- [ ] Trade-offs documentados

## Antes de Push

Executar:
```bash
npm run lint
npm test
npm run build
```

Se houver falhas, corrigir antes de prosseguir.
