# Test Template

```typescript
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { ComponentName } from './ComponentName';

// Mock dependencies if needed
// jest.mock('@/hooks/useSomething');

describe('ComponentName', () => {
  const defaultProps = {
    title: 'Test Title'
  };

  it('deve renderizar corretamente', () => {
    render(<ComponentName {...defaultProps} />);
    
    expect(screen.getByText('Test Title')).toBeInTheDocument();
    expect(screen.getByRole('button', { name: /toggle/i })).toBeInTheDocument();
  });

  it('deve alternar conteúdo ao clicar', async () => {
    const user = userEvent.setup();
    render(
      <ComponentName {...defaultProps}>
        <div>Conteúdo Expandido</div>
      </ComponentName>
    );

    // Inicialmente fechado
    expect(screen.queryByText('Conteúdo Expandido')).not.toBeInTheDocument();

    // Clicar para abrir
    await user.click(screen.getByRole('button', { name: /toggle/i }));
    
    // Deve estar visível
    expect(screen.getByText('Conteúdo Expandido')).toBeInTheDocument();
  });

  it('deve ter atributos de acessibilidade corretos', () => {
    render(<ComponentName {...defaultProps} />);
    
    const button = screen.getByRole('button', { name: /toggle/i });
    expect(button).toHaveAttribute('aria-expanded', 'false');
  });
});
```
