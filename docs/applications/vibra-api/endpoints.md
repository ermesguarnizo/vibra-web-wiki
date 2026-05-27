---
title: Endpoints API
description: Listado completo de endpoints REST de Vibra API agrupados por módulo
---

# Endpoints API

## Módulo Auth (`/api/auth`)

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| `POST` | `/api/auth/health` | Health check del servicio | Público |
| `POST` | `/api/auth/register` | Registrar nuevo usuario | Público |
| `POST` | `/api/auth/login` | Iniciar sesión (username + password) | Público |
| `POST` | `/api/auth/logout` | Cerrar sesión | Público |
| `GET` | `/api/auth/profile` | Perfil del usuario autenticado | JWT |
| `GET` | `/api/auth/my-permissions` | Permisos del usuario autenticado | JWT |
| `POST` | `/api/auth/refresh-token` | Refrescar JWT | Público |
| `POST` | `/api/auth/verify-token` | Verificar validez de token | Público |
| `POST` | `/api/auth/change-password` | Cambiar contraseña | JWT |

## Módulo Activities (`/api/activities`)

| Método | Ruta | Descripción | Permiso |
|--------|------|-------------|---------|
| `POST` | `/api/activities` | Crear actividad | `16` |
| `GET` | `/api/activities` | Listar activas (paginado, filtro por emoción) | Bypass |
| `GET` | `/api/activities/all` | Listar activas (paginado sin filtros) | — |
| `GET` | `/api/activities/:id` | Obtener actividad por ID | — |
| `PUT` | `/api/activities/:id` | Actualizar actividad | `16` |
| `DELETE` | `/api/activities/:id` | Soft delete (isActive=false) | `16` |
| `GET` | `/api/activities/daily/current` | Actividad del día | Bypass |
| `GET` | `/api/activities/by-month` | Completaciones agrupadas por mes | — |
| `GET` | `/api/activities/count-all-activities` | Conteo total de actividades | Bypass |
| `GET` | `/api/activities/count-by-type` | Conteo por tipo (reto, evento_personal, etc.) | Bypass |
| `GET` | `/api/activities/emotions/list` | IDs de emociones disponibles | Bypass |
| `GET` | `/api/activities/user/:userId` | Actividades de un usuario (filtro por tipo) | Bypass |
| `POST` | `/api/activities/:id/:userId/submit` | Registrar respuesta de usuario | Bypass |

## Módulo Emotions (`/api/emotions`)

| Método | Ruta | Descripción | Permiso |
|--------|------|-------------|---------|
| `POST` | `/api/emotions` | Crear emoción | `8` |
| `GET` | `/api/emotions` | Listar emociones (paginado) | Bypass |
| `GET` | `/api/emotions/:id` | Obtener emoción por ID | — |
| `PUT` | `/api/emotions/:id` | Actualizar emoción | `8` |
| `DELETE` | `/api/emotions/:id` | Soft delete | `8` |
| `GET` | `/api/emotions/by-name/:name` | Buscar por nombre | — |
| `GET` | `/api/emotions/distribution` | Distribución de respuestas por emoción | — |
| `GET` | `/api/emotions/evolution` | Evolución temporal (últimos N días) | — |

## Módulo Courses (`/api/courses`)

| Método | Ruta | Descripción | Permiso |
|--------|------|-------------|---------|
| `POST` | `/api/courses` | Crear curso | `10` |
| `GET` | `/api/courses` | Listar cursos (filtros: companyId, status) | Bypass |
| `GET` | `/api/courses/:id` | Obtener curso por ID | — |
| `PUT` | `/api/courses/:id` | Actualizar curso | `10` |
| `DELETE` | `/api/courses/:id` | Soft delete | `10` |
| `GET` | `/api/courses/list` | Lista simple (selectores) | — |
| `GET` | `/api/courses/progress` | Progreso de cursos (%) | — |
| `GET` | `/api/courses/company/:companyId` | Cursos por institución | — |

## Módulo Participants (`/api/participants`)

| Método | Ruta | Descripción | Permiso |
|--------|------|-------------|---------|
| `POST` | `/api/participants` | Crear participante | — |
| `GET` | `/api/participants` | Listar participantes | `9` |
| `GET` | `/api/participants/by-user/:userId` | Obtener por userId | — |
| `GET` | `/api/participants/:id` | Obtener por _id | `9` |
| `POST` | `/api/participants/update` | Actualizar participante | Bypass |
| `POST` | `/api/participants/delete` | Eliminar participante | `9` |
| `PATCH` | `/api/participants/:id/points` | Incrementar puntos + streak + level | — |
| `GET` | `/api/participants/:id/activity-history` | Historial de actividades por día | — |
| `GET` | `/api/participants/leaderboard` | Leaderboard del curso | — |
| `GET` | `/api/participants/count-all-participants` | Conteo total | Bypass |
| `GET` | `/api/participants/search` | Buscar participantes | `9` |
| `POST` | `/api/participants/bulk` | Carga masiva CSV | `9` |
| `GET` | `/api/participants/filter` | Filtrar por rango de fechas | `9` |
| `GET` | `/api/participants/paginated` | Listar paginado | `9` |

## Módulo Rankings (`/api/rankings`)

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| `GET` | `/api/rankings/general` | Ranking global | Público |
| `GET` | `/api/rankings/course/:courseId` | Ranking por curso | Público |
| `GET` | `/api/rankings/institution/:institutionId` | Ranking por institución | Público |

Query params: `limit` (default 20), `offset`, `search`

## Módulo Activity Completions (`/api/activity-completions`)

| Método | Ruta | Descripción |
|--------|------|-------------|
| `POST` | `/api/activity-completions` | Registrar completación |
| `GET` | `/api/activity-completions/participant/:participantId` | Historial del participante |
| `GET` | `/api/activity-completions/stats/:participantId` | Estadísticas del participante |
| `GET` | `/api/activity-completions/today-count` | Completaciones de hoy |
| `GET` | `/api/activity-completions/ranking` | Ranking por puntaje acumulado |
| `GET` | `/api/activity-completions/:id` | Detalle de completación |

## Módulo Feedback (`/api/feedback`)

| Método | Ruta | Descripción |
|--------|------|-------------|
| `GET` | `/api/feedback` | Listar feedbacks (paginado) |
| `GET` | `/api/feedback/:id` | Obtener por ID |
| `POST` | `/api/feedback` | Crear feedback |
| `PUT` | `/api/feedback/:id` | Actualizar feedback |
| `DELETE` | `/api/feedback/:id` | Soft delete |
| `DELETE` | `/api/feedback/:id/hard` | Hard delete |
| `PUT` | `/api/feedback/:id/status` | Actualizar estado |
| `PUT` | `/api/feedback/:id/soft-delete` | Soft delete con parámetros |
| `POST` | `/api/feedback/:id/convert-to-idea` | Convertir a idea del backlog |
| `POST` | `/api/feedback/by-description` | Buscar por descripción |
| `GET` | `/api/feedback/search/term` | Buscar por término |
| `POST` | `/api/feedback/upload-csv` | Carga masiva CSV |

## Módulo PreTests (`/api/pretests`)

| Método | Ruta | Descripción | Permiso |
|--------|------|-------------|---------|
| `POST` | `/api/pretests` | Crear pretest | `15` |
| `GET` | `/api/pretests` | Listar todos (paginado) | `15` |
| `PATCH` | `/api/pretests/:id` | Actualizar pretest | `15` |
| `DELETE` | `/api/pretests/:id` | Eliminar pretest | `15` |
| `POST` | `/api/pretests/save` | Guardar respuesta | — |
| `GET` | `/api/pretests/by-test/:testId` | Respuestas de un test | `15` |
| `POST` | `/api/pretests/search/userAndTest` | Buscar por userId + testId | `15` |
| `GET` | `/api/pretests/search/user/:userId` | Tests de un usuario | — |
| `GET` | `/api/pretests/result-all` | Todos los resultados | `15` |
| `GET` | `/api/pretests/result/:id` | Resultado por ID | — |
| `GET` | `/api/pretests/status/:userId` | Estado de completitud | — |
| `GET` | `/api/pretests/count-all-pretests` | Conteo total | Bypass |

## Módulo Notifications (`/api/notifications`)

| Método | Ruta | Descripción |
|--------|------|-------------|
| `POST` | `/api/notifications` | Crear notificación |
| `POST` | `/api/notifications/batch` | Crear múltiples |
| `GET` | `/api/notifications/all` | Listar todas (paginado) |
| `GET` | `/api/notifications/:id` | Obtener por ID |
| `GET` | `/api/notifications/title/:title` | Obtener por título |
| `PUT` | `/api/notifications/:id` | Actualizar |
| `DELETE` | `/api/notifications/:id` | Eliminar |
| `PUT` | `/api/notifications/read/:id` | Marcar como leída |
| `GET` | `/api/notifications/unread/count/:id` | No leídas por usuario |
| `GET` | `/api/notifications/count-all-notifications` | Conteo total |
| `GET` | `/api/notifications/count-all-notifications-by-day` | Conteo por día |
| `GET` | `/api/notifications/search/:term` | Buscar por término |
| `POST` | `/api/notifications/batch/upload` | Carga masiva CSV |

## Push Notifications (`/api/push-notifications`)

| Método | Ruta | Descripción |
|--------|------|-------------|
| `POST` | `/api/push-notifications/register` | Registrar token push |
| `POST` | `/api/push-notifications/unregister` | Desactivar token |
