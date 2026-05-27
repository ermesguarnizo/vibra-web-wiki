---
title: Panel Actividades
description: Gestión CRUD de actividades educativas — tabla paginada, formulario con recursos, preguntas, juegos y programación
---

# Panel Actividades

## Descripción

Vista de gestión de actividades educativas. Permite crear, editar, buscar, activar/desactivar y listar actividades con soporte para recursos multimedia, preguntas, tips motivacionales, juegos integrados y programación por fecha/semana/año.

## Componentes

| Componente | Ruta | Propósito |
|---|---|---|
| `ActivityDataPage` | `components/activity/data-page.tsx` | Página de listado con tabla, paginación y búsqueda |
| `ActivityComponent` | `components/activity/activity.tsx` | Formulario de creación/edición |
| `table.tsx` | `components/activity/table.tsx` | **Deprecado** — tabla inline vía `ListPageLayout` |

### ActivityDataPage

Usa `ListPageLayout` como layout genérico. Columnas:

| Columna | Render |
|---|---|
| Título | `a.title`, clase `min-w-[430px]` |
| Dificultad | Puntos `●` color ámbar según `a.difficulty` (1 a 5) |
| Recursos | Conteo: `a.resources?.length` |
| Preguntas | Conteo: `a.questions?.length` |
| Programación | Fecha localizada desde `a.schedule.date` |
| Estado | Badge `Activo`/`Inactivo` según `a.isActive` |

Acciones disponibles: **✏️ Editar** (abre tab `/Actividad/{id}`) y **🔁 Activar/Desactivar** (modal de confirmación con variante danger/warning).

La paginación usa estado local `currentPage` (default 1) y `pageSize` (default 12). La búsqueda se delega al componente `Search` con `searchEntity="activity"`.

### ActivityComponent

Formulario en tabs con las siguientes secciones envueltas en `CardSection`:

1. **Información Principal** — Título, emoción (DropdownMenuButton con datos de `getAllEmotions`), dificultad numérica 1-5, toggle activo sí/no, descripción textarea.
2. **Programación** — Fecha (datepicker), semana (number 1-53), año.
3. **Recursos Multimedia** — Lista dinámica de recursos (video/audio) con URL, duración en segundos y autor.
4. **Preguntas** — Lista dinámica de preguntas tipo abierta o múltiple. Las de tipo múltiple incluyen opciones con radio button para respuesta correcta.
5. **Tips de Apoyo Emocional** — Mensajes con emoji, texto y categoría (start, question, wordsearch, matching, emotionbox, dicegame, complete).
6. **Juegos** — Agregar juegos tipo `WordSearch`, `MatchingConcepts`, `EmotionBox` o `DiceGame`, cada uno con su formulario de configuración específico (`WordSearchForm`, `MatchingConceptsForm`, `DiceGameForm`, `EmotionBoxForm`).

## Integración API

| Función | Método | Uso |
|---|---|---|
| `getAll(page, pageSize)` | GET | Carga paginada de actividades |
| `getActivityById(id)` | GET | Carga datos para edición |
| `createActivity(payload)` | POST | Crear nueva actividad |
| `updateActivity(id, payload)` | PUT | Actualizar actividad existente |
| `updateActivityStatus(id, isActive, data)` | PATCH | Activar/desactivar |
| `assignUser(activityId, userId)` | POST | Asignar usuario a actividad |

## Payload

```typescript
interface CreateActivityPayload {
  title: string;
  description?: string;
  difficulty: number;        // 1-5
  isActive: boolean;
  emotion: string;           // ID de emoción
  resources?: Resource[];
  questions?: Question[];
  tips?: Tip[];
  games?: Game[];
  schedule?: {
    date: Date;
    weekNumber: number;
    year: number;
  };
}
```
