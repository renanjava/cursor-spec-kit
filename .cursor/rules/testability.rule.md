# Testability Rules

Todo código deve ser projetado para ser testável.

Regras obrigatórias:
- Separar lógica de negócio de I/O
- Priorizar funções puras
- Usar injeção de dependência
- Evitar singletons e estados globais
- Não acoplar lógica a frameworks

Testes:
- Testar comportamento, não implementação
- Cobrir casos de sucesso e erro
- Minimizar mocks
- Preferir testes rápidos e determinísticos

Se o código não for testável:
- Explicar por quê
- Sugerir refatoração incremental
