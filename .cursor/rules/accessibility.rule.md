# Accessibility Rules

## Princípios WCAG

Seguir Web Content Accessibility Guidelines nível AA mínimo.

## HTML Semântico

### Elementos Apropriados

**✅ Usar:**
```html
<button>Enviar</button>
<nav>...</nav>
<main>...</main>
<header>...</header>
<footer>...</footer>
<article>...</article>
<section>...</section>
```

**❌ Evitar:**
```html
<div onclick="submit()">Enviar</div>
<div class="navigation">...</div>
```

### Hierarquia de Headings

```html
<h1>Título Principal</h1>
  <h2>Seção 1</h2>
    <h3>Subseção 1.1</h3>
  <h2>Seção 2</h2>
```

Regras:
- Um único `<h1>` por página
- Não pular níveis (h1 → h3)
- Ordem lógica e hierárquica

## Labels e Inputs

### Forms Acessíveis

**✅ Correto:**
```html
<label for="email">Email</label>
<input id="email" type="email" name="email">
```

**Para grupos:**
```html
<fieldset>
  <legend>Informações Pessoais</legend>
  <label for="name">Nome</label>
  <input id="name" type="text">
</fieldset>
```

**Mensagens de erro:**
```html
<label for="email">Email</label>
<input 
  id="email" 
  type="email" 
  aria-invalid="true"
  aria-describedby="email-error"
>
<span id="email-error" role="alert">Email inválido</span>
```

## Navegação por Teclado

### Elementos Interativos

Todos elementos clicáveis devem ser acessíveis por teclado:
- `<button>`, `<a>`, `<input>` - Nativamente acessíveis
- Elementos customizados precisam de `tabindex` e handlers de teclado

**Exemplo de elemento customizado:**
```tsx
<div
  role="button"
  tabIndex={0}
  onClick={handleClick}
  onKeyDown={(e) => {
    if (e.key === 'Enter' || e.key === ' ') {
      handleClick();
    }
  }}
>
  Clique aqui
</div>
```

### Tab Order

- Usar `tabindex="0"` para incluir na ordem natural
- Evitar `tabindex > 0` (quebra ordem lógica)
- Usar `tabindex="-1"` para remover da navegação mas permitir focus programático

### Focus Visível

```css
/* Nunca remover outline sem substituir */
:focus {
  outline: 2px solid #007bff;
  outline-offset: 2px;
}

/* Ou usar focus-visible para diferenciar mouse de teclado */
:focus-visible {
  outline: 2px solid #007bff;
}
```

## ARIA (Accessible Rich Internet Applications)

### Quando Usar

- Elementos customizados que não são nativamente semânticos
- Estados dinâmicos
- Widgets complexos (modals, tabs, accordions)

### Roles Comuns

```html
<div role="dialog" aria-labelledby="dialog-title">
  <h2 id="dialog-title">Confirmar Ação</h2>
  ...
</div>

<button aria-expanded="false" aria-controls="menu">
  Menu
</button>
<div id="menu" hidden>...</div>

<div role="alert" aria-live="polite">
  Dados salvos com sucesso
</div>
```

### Estados e Propriedades

```html
<button aria-pressed="true">Bold</button>
<input aria-invalid="true" aria-describedby="error">
<div aria-busy="true">Carregando...</div>
<button aria-label="Fechar" onclick="closeModal()">×</button>
```

## Imagens

### Alt Text

**✅ Descritivo:**
```html
<img src="chart.png" alt="Gráfico mostrando crescimento de 25% nas vendas">
```

**Para imagens decorativas:**
```html
<img src="decoration.png" alt="">
```

**Para ícones funcionais:**
```html
<button>
  <img src="trash.svg" alt="Excluir item">
</button>
```

## Contraste de Cores

### Requisitos WCAG AA

- Texto normal: contraste mínimo 4.5:1
- Texto grande (18pt+): contraste mínimo 3:1
- Elementos de UI: contraste mínimo 3:1

**Ferramentas:**
- WebAIM Contrast Checker
- Chrome DevTools (Coverage tab)

## Leitores de Tela

### Conteúdo Oculto Visualmente

```css
.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

Uso:
```html
<button>
  <span class="visually-hidden">Adicionar ao carrinho</span>
  <span aria-hidden="true">+</span>
</button>
```

## Modals e Dialogs

```tsx
const Modal = ({ isOpen, onClose, children }) => {
  const modalRef = useRef(null);
  
  useEffect(() => {
    if (isOpen) {
      // Trap focus inside modal
      modalRef.current?.focus();
      
      // Prevent scroll on body
      document.body.style.overflow = 'hidden';
      
      return () => {
        document.body.style.overflow = '';
      };
    }
  }, [isOpen]);
  
  if (!isOpen) return null;
  
  return (
    <div
      role="dialog"
      aria-modal="true"
      aria-labelledby="modal-title"
      ref={modalRef}
      tabIndex={-1}
    >
      <h2 id="modal-title">Título</h2>
      {children}
      <button onClick={onClose}>Fechar</button>
    </div>
  );
};
```

## Testes de Acessibilidade

### Ferramentas

- **axe DevTools** - Extension do Chrome/Firefox
- **Lighthouse** - Incluído no Chrome DevTools
- **WAVE** - Web Accessibility Evaluation Tool
- **Screen readers** - NVDA (Windows), VoiceOver (Mac), JAWS

### Checklist Manual

- [ ] Navegar site completo apenas com teclado
- [ ] Testar com leitor de tela
- [ ] Validar contraste de cores
- [ ] Verificar alt text em imagens
- [ ] Testar zoom até 200%
- [ ] Validar HTML semântico
- [ ] Checar ordem de foco lógica

## Anti-Patterns

❌ `<div onclick>` ao invés de `<button>`
❌ Remover outline sem alternativa
❌ Conteúdo importante apenas em `:hover`
❌ Alt text genérico ("imagem", "foto")
❌ Formulários sem labels
❌ Modals que não trapam focus
❌ Contraste insuficiente
❌ Pular níveis de heading
