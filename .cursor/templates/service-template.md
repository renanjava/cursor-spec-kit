# Service Template

```typescript
import { apiClient } from '@/lib/api-client';
import { z } from 'zod';

// Validation Schemas
export const createItemSchema = z.object({
  name: z.string().min(2),
  email: z.string().email(),
});

export type CreateItemInput = z.infer<typeof createItemSchema>;

export interface Item {
  id: string;
  name: string;
  email: string;
  createdAt: string;
}

/**
 * ServiceName
 * 
 * Responsável por:
 * - Gerenciar operações de itens
 * - Comunicar com backend
 * - Validar dados
 */
export const ServiceName = {
  /**
   * Busca lista de itens
   * @param page Página atual (1-based)
   */
  list: async (page = 1): Promise<Item[]> => {
    const { data } = await apiClient.get<Item[]>('/items', {
      params: { page }
    });
    return data;
  },

  /**
   * Busca item por ID
   * @throws {NotFoundError} Se item não existir
   */
  getById: async (id: string): Promise<Item> => {
    const { data } = await apiClient.get<Item>(`/items/${id}`);
    return data;
  },

  /**
   * Cria novo item
   * @throws {ValidationError} Se dados forem inválidos
   */
  create: async (input: CreateItemInput): Promise<Item> => {
    // Validação local antes de enviar
    const validData = createItemSchema.parse(input);
    
    const { data } = await apiClient.post<Item>('/items', validData);
    return data;
  }
};
```
