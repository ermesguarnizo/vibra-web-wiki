---
title: Referencia de Endpoints API
description: Documentación completa de todos los endpoints REST de Vibra API agrupados por módulo
---

# Referencia de Endpoints API

**Base URL:** `http://localhost:4000` (desarrollo) — configurable mediante variable de entorno `PORT` (default `4000`)

**Autenticación:** La mayoría de los endpoints requieren token JWT en el header `Authorization: Bearer <token>`. Los endpoints públicos están marcados explícitamente.

**Formato de respuesta:** JSON. Las respuestas paginadas retornan `{ data: [], total: N }` o `{ docs: [], totalDocs, page, limit }`.

---

## Módulo: Auth

Controlador: `AuthController` | Ruta base: `/api/auth`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| POST | `/api/auth/health` | Verificar salud del servicio | Público |
| POST | `/api/auth/register` | Registrar nuevo usuario | Público |
| POST | `/api/auth/login` | Iniciar sesión (username + password) | Público |
| POST | `/api/auth/refresh-token` | Refrescar token JWT | Público |
| POST | `/api/auth/logout` | Cerrar sesión (invalida token) | Público |
| POST | `/api/auth/change-password` | Cambiar contraseña del usuario autenticado | JWT |
| POST | `/api/auth/verify-token` | Verificar validez de un token JWT | JWT |
| GET | `/api/auth/profile` | Obtener perfil del usuario autenticado | JWT |
| GET | `/api/auth/my-permissions` | Obtener permisos resueltos del usuario autenticado | JWT |

### Detalle de parámetros

**POST `/api/auth/register`**
- Body: `{ username: string, password: string, email?: string, name?: string, avatar?: string, documentNumber?: string, documentType?: string, role?: string, company?: string }`
- Respuesta: `{ message: "User registered successfully" }`

**POST `/api/auth/login`**
- Body: `{ username: string, password: string }`
- Respuesta: `{ access_token: string, user: {...} }`

**POST `/api/auth/change-password`**
- Body: `{ currentPassword: string, newPassword: string }`

**POST `/api/auth/logout`**
- Body: `{ userId: string }`

**POST `/api/auth/verify-token`**
- Body: `{ token: string }`

---

## Módulo: Recuperación de Contraseña

Controlador: `PasswordResetController` | Ruta base: `/api/password-reset`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| POST | `/api/password-reset/forgot-password` | Solicitar restablecimiento de contraseña (envía correo) | Público |
| GET | `/api/password-reset/validate-token` | Validar token de restablecimiento | Público |
| POST | `/api/password-reset/reset-password` | Restablecer contraseña con token | Público |

### Detalle de parámetros

**POST `/api/password-reset/forgot-password`**
- Body: `{ email: string }`
- Respuesta: `{ message: "Si el email existe, recibirás un enlace de restablecimiento" }`

**GET `/api/password-reset/validate-token`**
- Query: `?token=<jwt_token>`
- Respuesta: `{ valid: boolean, email: string }`

**POST `/api/password-reset/reset-password`**
- Body: `{ token: string, newPassword: string }`
- Respuesta: `{ message: "Contraseña actualizada exitosamente" }`

---

## Módulo: Users

Controlador: `UsersController` | Ruta base: `/api/users`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| POST | `/api/users/create` | Crear un nuevo usuario (admin) | JWT |
| POST | `/api/users` | Actualizar un usuario por `_id` | Público |
| GET | `/api/users/all` | Listar todos los usuarios | JWT |
| GET | `/api/users/allPaginate` | Lista paginada de usuarios (`{ data, total }`) | JWT |
| GET | `/api/users/search/:username` | Buscar usuario por username | JWT |
| GET | `/api/users/id/:id` | Obtener usuario por `_id` | Público |
| GET | `/api/users/count-all-users` | Conteo total de usuarios | Público |
| GET | `/api/users/search-by-role/docentes` | Buscar usuarios con rol docente | JWT |
| GET | `/api/users/trigger` | Emitir evento WebSocket de prueba | JWT |

### Detalle de parámetros

**POST `/api/users/create`**
- Body: `{ username: string, password: string, email?: string, name?: string, documentNumber?: string, documentType?: string, avatar?: string, role?: string, company?: string, gender?: string, birthDate?: string }`
- Respuesta: `{ username: string }`

**POST `/api/users`** (actualizar)
- Body: `{ _id: string, username?: string, password?: string, email?: string, name?: string, ... }`

**GET `/api/users/search/:username`**
- Path: `username` — nombre de usuario a buscar

**GET `/api/users/search-by-role/docentes`**
- Query: `?searchTerm=<termino>&limit=<numero>`

---

## Módulo: Activities

Controlador: `ActivitiesController` | Ruta base: `/api/activities`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| GET | `/api/activities` | Lista paginada con filtro por emoción y userId | JWT (bypass) |
| POST | `/api/activities` | Crear una nueva actividad | JWT |
| GET | `/api/activities/all` | Lista paginada de actividades (sin filtros) | JWT |
| GET | `/api/activities/:id` | Obtener detalle de una actividad por ID | JWT |
| PUT | `/api/activities/:id` | Actualizar una actividad | JWT |
| DELETE | `/api/activities/:id` | Desactivar actividad (soft delete) | JWT |
| GET | `/api/activities/by-month` | Actividades completadas agrupadas por mes | JWT |
| GET | `/api/activities/count-all-activities` | Número total de actividades | JWT (bypass) |
| GET | `/api/activities/count-by-type` | Contar actividades por tipo | JWT (bypass) |
| GET | `/api/activities/emotions/list` | Listar IDs de emociones disponibles según actividades | JWT (bypass) |
| GET | `/api/activities/user/:userId` | Listar actividades de un usuario (con filtro por tipo) | JWT (bypass) |
| GET | `/api/activities/daily/current` | Obtener actividad activa del día actual | JWT (bypass) |
| POST | `/api/activities/:id/:userId/submit` | Registrar respuestas de usuario a una actividad | JWT (bypass) |

### Detalle de parámetros

**GET `/api/activities`**
- Query: `?emotion=<id|all>&userId=<id>&page=<num>&limit=<num>`

**POST `/api/activities`**
- Body: `{ title: string, description?: string, emotion: string, difficulty: number, type: string, resources: [{ type, url, duration?, metadata? }], questions: [{ id, questionText, type, options?, correctAnswer?, points }], isActive?: boolean }`

**GET `/api/activities/by-month`**
- Query: `?year=<2026>&courseId=<id>`

**GET `/api/activities/count-by-type`**
- Query: `?type=<reto|evento_personal|actividad_pares|otro>`

**GET `/api/activities/user/:userId`**
- Path: `userId`
- Query: `?type=<tipo>&page=<num>&limit=<num>`

**POST `/api/activities/:id/:userId/submit`**
- Path: `id` (actividad), `userId` (usuario)
- Body: `{ responses: [{ questionId: string, answer: any, points?: number }] }`

---

## Módulo: Emotions

Controlador: `EmotionsController` | Ruta base: `/api/emotions`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| GET | `/api/emotions` | Listar emociones (paginado) | JWT (bypass) |
| POST | `/api/emotions` | Crear una nueva emoción | JWT |
| GET | `/api/emotions/:id` | Obtener emoción por ID | JWT |
| PUT | `/api/emotions/:id` | Actualizar una emoción | JWT |
| DELETE | `/api/emotions/:id` | Desactivar emoción (soft delete) | JWT |
| GET | `/api/emotions/distribution` | Distribución de respuestas por emoción (gráfica donut) | JWT |
| GET | `/api/emotions/evolution` | Evolución temporal de emociones por día (gráfica líneas) | JWT |
| GET | `/api/emotions/by-name/:name` | Consultar emoción por nombre exacto | JWT |

### Detalle de parámetros

**POST `/api/emotions`**
- Body: `{ name: string, description?: string, icono: string, percentNote: number, category?: string, intensity?: number, orientationNote?: string, isActive?: boolean }`

**GET `/api/emotions`**
- Query: `?page=<num>&limit=<num>`

**GET `/api/emotions/distribution`**
- Query: `?startDate=<ISO>&endDate=<ISO>&courseId=<id>`

**GET `/api/emotions/evolution`**
- Query: `?days=<30>&startDate=<ISO>&endDate=<ISO>&courseId=<id>`

---

## Módulo: Participants

Controlador: `ParticipantController` | Ruta base: `/api/participants`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| GET | `/api/participants` | Listar participantes (paginado) | JWT |
| POST | `/api/participants` | Crear participante desde registro de User | JWT |
| GET | `/api/participants/:id` | Obtener participante por `_id` | JWT |
| GET | `/api/participants/by-user/:userId` | Obtener participante por userId | JWT |
| PATCH | `/api/participants/:id/points` | Incrementar puntos y actualizar streak/level | JWT |
| GET | `/api/participants/:id/activity-history` | Historial de actividades completadas por día | JWT |
| GET | `/api/participants/leaderboard` | Leaderboard de participantes por puntos | JWT |
| POST | `/api/participants/update` | Actualizar datos del participante | Público |
| POST | `/api/participants/delete` | Eliminar participante | JWT |
| POST | `/api/participants/bulk` | Carga masiva de participantes (CSV) | JWT |
| GET | `/api/participants/search` | Buscar participantes por término | JWT |
| GET | `/api/participants/filter` | Listar por rango de fechas | JWT |
| GET | `/api/participants/paginated` | Listar participantes con paginación independiente | JWT |
| GET | `/api/participants/count-all-participants` | Conteo total de participantes | Público |

### Detalle de parámetros

**POST `/api/participants`**
- Body: `{ userId: string, nickname: string, avatar?: string, courseId?: string }`

**PATCH `/api/participants/:id/points`**
- Body: `{ points: number, activityId?: string }`

**GET `/api/participants/leaderboard`**
- Query: `?courseId=<id>&limit=<num>`

**GET `/api/participants/search`**
- Query: `?searchTerm=<termino>`

**GET `/api/participants/filter`**
- Query: `?startDate=<YYYY-MM-DD>&endDate=<YYYY-MM-DD>&limit=<num>`

**POST `/api/participants/bulk`**
- Body: multipart/form-data con campo `file` (CSV)

---

## Módulo: Feedback

Controlador: `FeedbackController` | Ruta base: `/api/feedback`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| GET | `/api/feedback` | Listar feedbacks (paginado) | JWT |
| POST | `/api/feedback` | Crear feedback | JWT |
| GET | `/api/feedback/:id` | Obtener feedback por ID | JWT |
| PUT | `/api/feedback/:id` | Actualizar feedback | JWT |
| DELETE | `/api/feedback/:id` | Eliminar feedback (soft delete) | JWT |
| DELETE | `/api/feedback/:id/hard` | Eliminar feedback (hard delete) | JWT |
| PUT | `/api/feedback/:id/status` | Actualizar estado (isActive) del feedback | JWT |
| PUT | `/api/feedback/:id/soft-delete` | Soft delete con parámetros | JWT |
| POST | `/api/feedback/:id/convert-to-idea` | Convertir feedback en idea del backlog | JWT |
| POST | `/api/feedback/by-description` | Buscar feedback por descripción exacta | JWT |
| GET | `/api/feedback/search/term` | Buscar feedbacks por término | JWT |
| POST | `/api/feedback/upload-csv` | Cargar CSV de feedbacks | JWT |

### Detalle de parámetros

**POST `/api/feedback`**
- Body: `{ description: string, ... }`

**POST `/api/feedback/:id/convert-to-idea`**
- Path: `id` — ID del feedback
- Body: `{ title: string, description?: string, priority?: string, tags?: string[] }`
- Respuesta: `{ success: boolean, ideaId: string, idea: object }`

**POST `/api/feedback/by-description`**
- Body: `{ description: string }`

**GET `/api/feedback/search/term`**
- Query: `?searchTerm=<termino>&page=<num>&rows=<num>`

---

## Módulo: Rankings

Controlador: `RankingsController` | Ruta base: `/api/rankings`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| GET | `/api/rankings/general` | Ranking general de todos los participantes activos | Público |
| GET | `/api/rankings/course/:courseId` | Ranking por curso específico | Público |
| GET | `/api/rankings/institution/:institutionId` | Ranking por institución (company) | Público |

### Detalle de parámetros

**GET `/api/rankings/general`**
- Query: `?page=<num>&limit=<num>`

**GET `/api/rankings/course/:courseId`**
- Path: `courseId` — ID del curso
- Query: `?page=<num>&limit=<num>`

**GET `/api/rankings/institution/:institutionId`**
- Path: `institutionId` — ID de la institución
- Query: `?page=<num>&limit=<num>`

---

## Módulo: Courses

Controlador: `CourseController` | Ruta base: `/api/courses`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| GET | `/api/courses` | Listar todos los cursos con filtros | Público |
| POST | `/api/courses` | Crear un nuevo curso | JWT |
| GET | `/api/courses/:id` | Obtener un curso por ID | JWT |
| PUT | `/api/courses/:id` | Actualizar un curso | JWT |
| DELETE | `/api/courses/:id` | Eliminar un curso (soft delete) | JWT |
| GET | `/api/courses/list` | Lista simple de cursos (id, name) para selectores | JWT |
| GET | `/api/courses/progress` | Progreso de cursos (% de avance por curso) | JWT |
| GET | `/api/courses/company/:companyId` | Listar cursos por institución | JWT |

### Detalle de parámetros

**GET `/api/courses`**
- Query: `?companyId=<id>&status=<true|false>&page=<num>&rows=<num>`

**POST `/api/courses`**
- Body: `{ name: string, description?: string, company?: string, isActive?: boolean }`

**DELETE `/api/courses/:id`**
- Body: `{ deletedBy?: string }`

---

## Módulo: Notifications

Controlador: `NotificationController` | Ruta base: `/api/notifications`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| GET | `/api/notifications/all` | Listar todas las notificaciones (paginado) | JWT |
| POST | `/api/notifications` | Crear una notificación | JWT |
| POST | `/api/notifications/batch` | Crear múltiples notificaciones | JWT |
| GET | `/api/notifications/:id` | Obtener notificación por ID | JWT |
| GET | `/api/notifications/title/:title` | Obtener notificación por título | JWT |
| PUT | `/api/notifications/:id` | Actualizar notificación | JWT |
| DELETE | `/api/notifications/:id` | Eliminar notificación | JWT |
| PUT | `/api/notifications/read/:id` | Marcar notificación como leída | JWT |
| GET | `/api/notifications/search/:term` | Buscar notificaciones por término | JWT |
| GET | `/api/notifications/count-all-notifications` | Conteo total de notificaciones | JWT |
| GET | `/api/notifications/count-all-notifications-by-day` | Conteo de notificaciones por día | JWT |
| GET | `/api/notifications/unread/count/:id` | Contar notificaciones no leídas de un usuario | JWT |
| POST | `/api/notifications/batch/upload` | Cargar CSV con notificaciones | JWT |

### Detalle de parámetros

**POST `/api/notifications`**
- Body: `{ title: string, message: string, user?: string, notificationType: string, notificationChannel: string, priority: number, isActive?: boolean }`

**PUT `/api/notifications/read/:id`**
- Body: `{ editedBy?: string }`

---

## Módulo: Contacts

Controlador: `ContactsController` | Ruta base: `/api/contact`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| POST | `/api/contact` | Enviar mensaje de contacto (formulario landing) | Público |
| GET | `/api/contact/all` | Listar todos los mensajes de contacto (admin) | JWT |
| GET | `/api/contact/allPaginate` | Lista paginada de mensajes (admin) | JWT |
| GET | `/api/contact/id/:id` | Obtener mensaje por ID (admin) | JWT |
| GET | `/api/contact/stats` | Estadísticas por estado de mensajes (admin) | JWT |
| POST | `/api/contact/update` | Actualizar estado/notas de un mensaje (admin) | JWT |

### Detalle de parámetros

**POST `/api/contact`**
- Body: `{ name: string, email: string, subject: string, message: string }`

**GET `/api/contact/allPaginate`**
- Query: `?page=<num>&limit=<num>&status=<unread|read|in_progress|resolved|spam>`

**POST `/api/contact/update`**
- Body: `{ _id: string, status?: string, notes?: string }`

---

## Módulo: PreTests

Controlador: `PreTestController` | Ruta base: `/api/pretests`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| POST | `/api/pretests` | Crear un nuevo pre-test | JWT |
| POST | `/api/pretests/save` | Guardar respuestas de pre-test (público) | JWT (bypass) |
| GET | `/api/pretests` | Listar todos los pre-tests (paginado) | JWT |
| GET | `/api/pretests/by-test/:testId` | Respuestas de usuarios para un test específico | JWT |
| GET | `/api/pretests/count-all-pretests` | Conteo total de pretests | Público |
| PATCH | `/api/pretests/:id` | Actualizar un pre-test | JWT |
| DELETE | `/api/pretests/:id` | Eliminar un pre-test | JWT |
| POST | `/api/pretests/search/userAndTest` | Obtener respuestas por userId + testId | JWT |
| GET | `/api/pretests/search/user/:userId` | Obtener todos los tests de un usuario | JWT (bypass) |
| GET | `/api/pretests/result-all` | Obtener todos los resultados de pre-tests | JWT |
| GET | `/api/pretests/result/:id` | Obtener resultado por ID | JWT (bypass) |
| GET | `/api/pretests/status/:userId` | Estado de completitud de tests por usuario | JWT (bypass) |

### Detalle de parámetros

**POST `/api/pretests/save`**
- Body: `{ testId: string, userId: string, responses: [{ questionId: string, answer: any, points?: number }] }`

**POST `/api/pretests/search/userAndTest`**
- Body: `{ userId: string, testId: string }`

**GET `/api/pretests/by-test/:testId`**
- Path: `testId` — ID del test
- Query: `?page=<num>&limit=<num>`

**GET `/api/pretests/status/:userId`**
- Path: `userId` — documentNumber del usuario

---

## Módulo: Activity Completions

Controlador: `ActivityCompletionsController` | Ruta base: `/api/activity-completions`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| POST | `/api/activity-completions` | Registrar completación de actividad | JWT |
| GET | `/api/activity-completions/:id` | Detalle de una completación por ID | JWT |
| GET | `/api/activity-completions/participant/:participantId` | Historial de completaciones de un participante | JWT |
| GET | `/api/activity-completions/stats/:participantId` | Estadísticas de un participante (puntajes, ranking) | JWT |
| GET | `/api/activity-completions/today-count` | Número de actividades completadas hoy | JWT |
| GET | `/api/activity-completions/ranking` | Ranking global de participantes por puntaje | JWT |

### Detalle de parámetros

**POST `/api/activity-completions`**
- Body: `{ participantId: string, activityId: string, courseId?: string, score: number, maxScore?: number }`

**GET `/api/activity-completions/participant/:participantId`**
- Path: `participantId`
- Query: `?page=<num>&limit=<num>`

**GET `/api/activity-completions/ranking`**
- Query: `?limit=<num>`

---

## Módulo: Reports

Controlador: `ReportsController` | Ruta base: `/reports`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| GET | `/reports` | Listar todos los reportes | JWT |
| POST | `/reports` | Crear un reporte | JWT |
| GET | `/reports/:id` | Obtener reporte por ID | JWT |
| PATCH | `/reports/:id` | Actualizar reporte | JWT |
| DELETE | `/reports/:id` | Eliminar reporte | JWT |

---

## Módulo: Admin

Controlador: `AdminController` | Ruta base: `/api/admin`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| GET | `/api/admin/dashboard` | Dashboard administrativo | JWT |

---

## Módulo: File Upload

Controlador: `FileUploadController` | Ruta base: `/api/file-upload`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| POST | `/api/file-upload` | Subir archivo (multipart/form-data) | JWT |

---

## Módulo: Config

Controlador: `ConfigController` | Ruta base: `/api/config`

| Método | Ruta | Descripción | Auth |
|--------|------|-------------|------|
| GET | `/api/config` | Obtener configuración del sistema | JWT |
| POST | `/api/config` | Actualizar configuración | JWT |

---

## Códigos de respuesta comunes

| Código | Significado |
|--------|-------------|
| `200` | OK — operación exitosa |
| `201` | Created — recurso creado exitosamente |
| `400` | Bad Request — datos inválidos o faltantes |
| `401` | Unauthorized — token ausente, inválido o expirado |
| `403` | Forbidden — permisos insuficientes |
| `404` | Not Found — recurso no encontrado |
| `409` | Conflict — conflicto de unicidad (email duplicado, etc.) |
| `429` | Too Many Requests — rate limit excedido |
| `500` | Internal Server Error — error inesperado del servidor |
