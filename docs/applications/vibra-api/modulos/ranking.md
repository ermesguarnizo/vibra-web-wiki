---
title: Módulo Ranking
description: Ranking de participantes — ActivityCompletion, agregaciones MongoDB, caché en memoria, WebSocket rankingsUpdate en tiempo real
---

# Módulo Ranking

El módulo `Rankings` (`src/domains/rankings/`) implementa el sistema de ranking y leaderboard de la plataforma, con soporte para consultas REST y actualizaciones en tiempo real vía WebSocket.

## Schema `ActivityCompletion`

Definido en `src/domains/activity-completions/schemas/activity-completion.schema.ts`. Colección: `activitycompletions`.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `participant` | `ObjectId` → `Participant` | Participante que completó (indexado) |
| `activity` | `ObjectId` → `Activity` | Actividad completada (indexado) |
| `plannedScore` | `number` | Puntaje planeado de la actividad |
| `achievedScore` | `number` | Puntaje alcanzado por el participante |
| `timeSpent` | `number` | Tiempo empleado (segundos) |
| `gamesCompleted` | `Array` | Resultados de juegos: `{ type, score, maxScore }` |
| `completedAt` | `Date` | Fecha de completación |

### Índices

```typescript
ActivityCompletionSchema.index({ participant: 1, completedAt: -1 });
ActivityCompletionSchema.index({ achievedScore: -1 });
```

## Rankings REST Service

Implementado en `rankings-rest.service.ts`. Proporciona tres niveles de ranking:

### Endpoints REST

| Método | Ruta | Descripción |
|--------|------|-------------|
| `GET` | `/api/rankings/general` | Ranking global de todos los participantes |
| `GET` | `/api/rankings/course/:courseId` | Ranking por curso |
| `GET` | `/api/rankings/institution/:institutionId` | Ranking por institución |

### Parámetros de consulta

- `limit` — Límite de resultados (default: 20)
- `offset` — Desplazamiento para paginación
- `search` — Búsqueda por nickname (regex, case-insensitive)

### Ranking por institución

Para el ranking por institución, el servicio:
1. Busca todos los `User` que pertenecen a la compañía (`company`)
2. Filtra `Participant` cuyo `userId` esté en esa lista
3. Ordena por `points` descendente

### Cache en memoria (30s TTL)

```typescript
private readonly CACHE_TTL = 30_000; // 30 segundos
private cache = new Map<string, { data: RankingResponseDto; timestamp: number }>();
```

Cada combinación de `{ scope, scopeId, limit, offset }` genera una clave de caché. El servicio:
- Consulta caché antes de MongoDB
- Si expiró (30s), lo elimina y consulta de nuevo
- Si el mapa excede 100 entradas, limpia las expiradas

### Enriquecimiento de datos

El método `enrichParticipants()` adjunta a cada entry del ranking:
- `nickname`, `avatar`, `points`, `level` (del participante)
- `courseName` (resuelto desde la colección Course)
- `institutionName` (resuelto desde `User.company.name`)

## WebSocket Gateway

### `/rankings` namespace (`ranking.service.ts`)

```typescript
@WebSocketGateway({ namespace: '/rankings' })
```

El gateway de rankings opera en el namespace `/rankings` y emite actualizaciones periódicas:

- **`handleConnection`**: al conectar, envía `rankingsUpdate` y `initialRankings`
- **Cron `EVERY_5_MINUTES`**: recalcula el leaderboard, actualiza caché y emite `broadcastUpdate()`
- **`joinRankingRoom`**: cliente solicita unirse — recibe rankings actualizados
- **`requestUserPosition`**: cliente solicita posición de un usuario — retorna `userPosition`
- **`requestFullLeaderboard`**: cliente solicita leaderboard completo — emite `fullLeaderboard`

### `/realtime` namespace (`ranking.gateway.ts`)

```typescript
@WebSocketGateway({ namespace: '/realtime' })
```

Gateway adicional que maneja:
- **`joinRankingRoom`**: Une al cliente a la sala 'rankings' y envía `initialRankings`
- **`requestHistoricalRankings`**: Retorna rankings históricos (N días atrás)

### Activity Completions endpoints

| Método | Ruta | Descripción |
|--------|------|-------------|
| `POST` | `/api/activity-completions` | Registrar completación de actividad |
| `GET` | `/api/activity-completions/participant/:participantId` | Historial del participante |
| `GET` | `/api/activity-completions/stats/:participantId` | Estadísticas del participante |
| `GET` | `/api/activity-completions/today-count` | Completaciones del día actual |
| `GET` | `/api/activity-completions/ranking` | Ranking global por puntaje acumulado |
| `GET` | `/api/activity-completions/:id` | Detalle de completación |
