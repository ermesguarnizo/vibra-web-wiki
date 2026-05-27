---
title: Panel Emociones
description: Gestión CRUD de emociones — catálogo de estados emocionales con categoría, intensidad e icono
---

# Panel Emociones

## Descripción

Catálogo de emociones del sistema. Cada emoción tiene nombre, categoría (Positiva, Negativa, Neutra, Básica, Compleja), intensidad numérica 1-10, icono emoji, nota de orientación y porcentaje de nota. Las emociones se vinculan a actividades y se usan en los registros emocionales de los participantes.

## Componentes

| Componente | Ruta | Propósito |
|---|---|---|
| `EmotionDataPage` | `components/emotion/data-page.tsx` | Página de listado con tabla inline |
| `EmotionComponent` | `components/emotion/emotion.tsx` | Formulario de creación/edición |
| `table.tsx` | `components/emotion/table.tsx` | **Deprecado** — la tabla es inline |

### EmotionDataPage

Página con tabla inline (sin `ListPageLayout`). Columnas:

| Columna | Render |
|---|---|
| Nombre | `emotion.name` (font-medium) |
| Categoría | `emotion.category` o `-` |
| Intensidad | `emotion.intensity` o `-` |
| Icono | `emotion.icono` o `-` |
| Estado | Badge `Activo`/`Inactivo` según `emotion.isActive` |
| Acciones | ✏️ Editar (abre tab `/Emocion/{id}`) y 🔁 Activar/Desactivar |

Usa `Pagination` standalone y `Search` con `entity="emotion"`. Estados: loading (fila única con texto), empty ("No hay emociones registradas"), error (toast con `toast.error`). Confirmación de toggle vía `ModalConfirm`.

### EmotionComponent

Formulario de una sola sección con campos:

- **Nombre** (obligatorio) — texto plano
- **Categoría** (obligatorio) — select con opciones: `Positiva`, `Negativa`, `Neutra`, `Basica`, `Compleja`
- **Intensidad** (1-10, default 5) — input number
- **Icono** — texto (emoji, default `😊`)
- **Descripción** — textarea (2 rows)
- **Nota de orientación** — textarea con placeholder "Enfócate en mantener..."
- **% Nota** — número 0-100

## Integración API

| Función | Método | Uso |
|---|---|---|
| `getAll(page, pageSize)` | GET | Carga paginada de emociones (retorna `{ data, total }`) |
| `getById(id)` | GET | Carga datos para edición |
| `create(payload)` | POST | Crear nueva emoción |
| `update(id, payload)` | PUT | Actualizar emoción existente |
| `toggleActive(id)` | PATCH | Activar/desactivar emoción |

## Payload

```typescript
interface CreateEmotionPayload {
  id?: string;
  name: string;
  description?: string;
  orientationNote?: string;
  icono?: string;
  percentNote?: number;
  category: 'Positiva' | 'Negativa' | 'Neutra' | 'Basica' | 'Compleja';
  intensity?: number;
}
```
