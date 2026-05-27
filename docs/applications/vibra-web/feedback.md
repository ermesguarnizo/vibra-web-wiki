---
title: Panel Feedback
description: Panel de feedback recibido — tabla paginada con filtros, modal de detalle, conversión feedback→idea y FAB flotante
---

# Panel Feedback

## Descripción

Panel de administración de feedback recibido desde la app mobile y el FAB flotante web. Muestra tabla paginada con filtro de búsqueda textual, barra de estadísticas (total, pendientes, convertidos, en desarrollo), modal de detalle y modal de conversión feedback → idea del backlog. El FAB flotante permite a usuarios web enviar feedback tipo mejora o apoyo desde cualquier página.

## Componentes

| Componente | Ruta | Propósito |
|---|---|---|
| `FeedbackDataPage` | `components/feedback/feedback-data-page.tsx` | Página de listado con tabla, stats y modales |
| `FeedbackConvertModal` | `components/feedback/feedback-convert-modal.tsx` | Modal de conversión feedback → idea |
| `FeedbackModal` | `components/feedback/feedback-modal.tsx` | Modal de envío de feedback desde FAB |
| `FloatingFeedbackBtn` | `components/ui/floating-feedback-btn.tsx` | FAB flotante en esquina inferior derecha |

### FeedbackDataPage

Tabla con columnas:

| Columna | Render |
|---|---|
| Serial | `#feedback.serial \|\| feedback._id.slice(-4)` (mono) |
| Título | `feedback.title` (truncado max 180px) |
| Descripción | Truncada a 100 caracteres con `…` |
| Tipo | Badge `Mejora` (feature) / `Apoyo` (support) |
| Estado | Badge `Convertido` (con ideaId) / `Pendiente` |
| Acciones | 👁️ Ver detalle y 📝 Convertir a Idea |

**Barra de estadísticas** (4 cards en grid):

| Card | Color |
|---|---|
| Feedback Total | Gris |
| Pendientes | Ámbar |
| Convertidos | Verde |
| En Desarrollo (backlog) | Índigo |

**Búsqueda**: input con icono `SearchIcon`, botón de limpiar `XCircleIcon`, llama a `GET /api/feedback/search/term?searchTerm=...`.

**Modal de detalle**: overlay fijo con información completa del feedback (serial, tipo, título, descripción, fecha, estado) y botón "Convertir a Idea" si está pendiente.

### FeedbackConvertModal

Modal de conversión con formulario:

- **Título** — pre-poblado desde feedback, editable
- **Descripción** — pre-poblada desde feedback, editable
- **Prioridad** — select: 🔴 Alta, 🟡 Media, 🟢 Baja
- **Tags** — input con `AutocompleteTags` que sugiere tags disponibles desde `getAvailableTags()`

Al confirmar, llama a `convertFeedbackToIdea(feedbackId, payload)`. Muestra toast de éxito con el ID de la idea creada.

### FloatingFeedbackBtn (vibra-073)

FAB posicionado `fixed bottom-24 right-6 z-50`. Al hacer clic, expande dos opciones:

| Opción | Ícono | Color hover |
|---|---|---|
| Enviar apoyo | 💬 | Verde |
| Sugerir mejora | ✨ | Azul |

Al seleccionar, abre `FeedbackModal` con el tipo correspondiente. El modal envía `POST /api/feedback` con `{ title, description, isFeature, isSupport, createdBy }`.

## Integración API

| Función | Método | Uso |
|---|---|---|
| `GET /api/feedback?page=&rows=` | GET | Lista paginada de feedbacks |
| `GET /api/feedback/search/term` | GET | Búsqueda textual |
| `POST /api/feedback` | POST | Enviar feedback desde FAB |
| `convertFeedbackToIdea(id, payload)` | POST | Convertir feedback a idea |
| `getIdeasStatus()` | GET | Conteo de ideas "en desarrollo" |
| `getAvailableTags()` | GET | Tags disponibles para autocompletar |
