---
title: Panel Participantes
description: Gestión de participantes del sistema — tabla paginada, búsqueda, edición de perfil y visualización de progreso
---

# Panel Participantes

## Descripción

Gestión de participantes del ecosistema Vibra. Los participantes se crean automáticamente desde la app mobile (auto-registro). El panel web permite editar nickname, avatar, curso y estado, además de visualizar métricas de progreso (puntos, nivel, racha). Incluye integración con Recharts para gráficas de ranking.

## Componentes

| Componente | Ruta | Propósito |
|---|---|---|
| `ParticipantDataPage` | `components/participant/data-page.tsx` | Página de listado con `ListPageLayout` |
| `ParticipantComponent` | `components/participant/participant.tsx` | Formulario de edición |
| `table.tsx` | `components/participant/table.tsx` | **Deprecado** — tabla inline vía `ListPageLayout` |
| `user-list-item.tsx` | `components/participant/user-list-item.tsx` | Item de lista para selección de usuarios |

### ParticipantDataPage

Usa `ListPageLayout`. Columnas:

| Columna | Render |
|---|---|
| Nickname | `p.nickname \|\| p.name \|\| '-'`, clase `min-w-[150px]` |
| Nivel | Badge con color según nivel: `bronce` (naranja), `plata` (gris), `oro` (amarillo), `platino` (azul), `diamante` (púrpura) |
| Puntos | `p.points?.toString() \|\| '0'` |
| Racha | `p.currentStreak` + "días" |
| Actividades | `p.totalActivitiesCompleted?.toString() \|\| '0'` |
| Estado | Badge `Activo`/`Inactivo` |

Acciones: **✏️ Editar** (abre tab `/Participante/{id}`) y **🗑️ Eliminar** (modal danger `"Esta acción no se puede deshacer"`).

Paginación: `pageSize` default 8. Búsqueda con `searchEntity="participant"`.

### ParticipantComponent

Usa `FormPageLayout`. Incluye transformación de datos vía función `toParticipant()` que normaliza `ParticipantResponse` a `Participant`.

Campos editables:
- Nickname (obligatorio, disabled si no está editando)
- Avatar (URL)
- Curso actual
- Estado (Activo/Inactivo)

Sección **Progreso (solo lectura)** con 4 cards:
- **Puntos** — color índigo
- **Nivel** — color verde (capitalizado)
- **Racha** — color amarillo (días)
- **Curso** — color azul

Los participantes **no se crean** desde el panel web. Se muestra mensaje informativo: "Los participantes se crean automáticamente cuando un usuario se registra e inicia sesión desde la aplicación móvil."

## Integración API

| Función | Método | Uso |
|---|---|---|
| `getAll(page, pageSize)` | GET | Lista paginada (retorna `{ participants, count }`) |
| `getById(id)` | GET | Carga datos para edición |
| `update(payload)` | PUT | Actualizar participante |
| `remove(id)` | DELETE | Eliminar participante |
| `getCountAllParticipants()` | GET | Total de participantes (usado en Analytics) |

## Gráficas con Recharts

El dashboard de Analytics integra el componente `ParticipantRankingChart` que consume `GET /api/participants/leaderboard?limit=10` y muestra el top 10 de participantes por puntaje, con avatar, nivel (badge coloreado), posición #1-#3 destacada y puntos totales.
