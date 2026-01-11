# Component Template

```tsx
import { useState } from 'react';
import { cn } from '@/lib/utils';
// import { useSomething } from '@/hooks/useSomething';

interface ComponentNameProps {
  /**
   * Description of the prop
   */
  title: string;
  /**
   * Optional className for styling
   */
  className?: string;
  /**
   * Optional children
   */
  children?: React.ReactNode;
}

/**
 * ComponentName
 * 
 * Por quê: [Explique brevemente o propósito deste componente se não for óbvio]
 * 
 * @example
 * <ComponentName title="Hello" />
 */
export function ComponentName({ 
  title, 
  className,
  children 
}: ComponentNameProps) {
  // State: Local UI state only
  const [isOpen, setIsOpen] = useState(false);

  // Handlers
  const handleClick = () => {
    setIsOpen(!isOpen);
  };

  return (
    <div className={cn('flex flex-col gap-2', className)}>
      <h2 className="text-xl font-bold">{title}</h2>
      
      <button 
        onClick={handleClick}
        className="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
        type="button"
        aria-expanded={isOpen}
      >
        toggle
      </button>

      {isOpen && (
        <div className="p-4 border rounded">
          {children}
        </div>
      )}
    </div>
  );
}
```
