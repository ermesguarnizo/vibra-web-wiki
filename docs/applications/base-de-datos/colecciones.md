---
title: Colecciones
description: Listado completo de colecciones MongoDB del sistema Vibra con descripción, campos principales y relaciones entre entidades.
---

# Colecciones

## `users`

Colección principal para la gestión de usuarios del sistema. Almacena credenciales, perfil, estado y metadatos de auditoría.

| Campo | Tipo | Descripción |
|---|---|---|
| `_id` | `ObjectId` | Identificador único |
| `name` | `string` | Nombre completo del usuario |
| `email` | `string` | Correo electrónico único |
| `password` | `string` | Hash bcrypt de la contraseña |
| `serial` | `string` | Código serial único del usuario |
| `roleId` | `ObjectId` | Referencia a `roles._id` |
| `companyId` | `ObjectId` | Referencia a `companies._id` |
| `isActive` | `boolean` | Estado de la cuenta |
| `deleted` | `boolean` | Flag de soft-delete |
| `createdAt` | `Date` | Fecha de creación |
| `updatedAt` | `Date` | Fecha de última modificación |

**Relaciones:** `roleId` → `roles`, `companyId` → `companies`

---

## `roles`

Define los roles del sistema y sus permisos asociados.

| Campo | Tipo | Descripción |
|---|---|---|
| `_id` | `ObjectId` | Identificador único |
| `name` | `string` | Nombre del rol (admin, teacher, student) |
| `description` | `string` | Descripción del rol |
| `permissions` | `ObjectId[]` | Lista de referencias a `permissions._id` |
| `isActive` | `boolean` | Estado del rol |
| `deleted` | `boolean` | Flag de soft-delete |
| `createdAt` | `Date` | Fecha de creación |
| `updatedAt` | `Date` | Fecha de modificación |

**Relaciones:** `permissions[]` → `permissions`

---

## `activities`

Actividades educativas del sistema. Representa ejercicios, juegos y recursos pedagógicos.

| Campo | Tipo | Descripción |
|---|---|---|
| `_id` | `ObjectId` | Identificador único |
| `title` | `string` | Título de la actividad |
| `type` | `string` | Tipo: game, quiz, reflection, dynamics |
| `emotions` | `ObjectId[]` | Referencias a `emotions._id` |
| `difficulty` | `number` | Nivel de dificultad (1-5) |
| `isActive` | `boolean` | Actividad publicada y disponible |
| `games` | `ObjectId[]` | Referencias a juegos asociados |
| `schedule` | `object` | Configuración de programación |
| `schedule.startDate` | `Date` | Fecha de inicio |
| `schedule.endDate` | `Date` | Fecha de fin |
| `schedule.repeat` | `string` | Frecuencia: none, daily, weekly, monthly |
| `resources` | `object[]` | Recursos multimedia asociados |
| `resources[].url` | `string` | URL del recurso |
| `resources[].type` | `string` | Tipo: image, video, pdf, audio |
| `resources[].name` | `string` | Nombre del recurso |
| `questions` | `object[]` | Preguntas de la actividad |
| `questions[].text` | `string` | Enunciado |
| `questions[].options` | `string[]` | Opciones de respuesta |
| `questions[].correctIndex` | `number` | Índice de la respuesta correcta |
| `deleted` | `boolean` | Flag de soft-delete |
| `createdAt` | `Date` | Fecha de creación |
| `updatedAt` | `Date` | Fecha de modificación |

**Relaciones:** `emotions[]` → `emotions`, `games[]` → colección de juegos

---

## `emotions`

Catálogo de emociones utilizadas en las actividades educativas.

| Campo | Tipo | Descripción |
|---|---|---|
| `_id` | `ObjectId` | Identificador único |
| `serial` | `number` | Número serial único autoincremental |
| `name` | `string` | Nombre de la emoción (alegría, tristeza, miedo) |
| `description` | `string` | Descripción detallada |
| `icono` | `string` | Identificador del icono o URL del asset |
| `deleted` | `boolean` | Flag de soft-delete |
| `createdAt` | `Date` | Fecha de creación |

---

## `participants`

Registro de participación de usuarios en cursos. Contiene el progreso y puntaje acumulado.

| Campo | Tipo | Descripción |
|---|---|---|
| `_id` | `ObjectId` | Identificador único |
| `userId` | `ObjectId` | Referencia a `users._id` |
| `courseId` | `ObjectId` | Referencia a `courses._id` |
| `points` | `number` | Puntaje acumulado total |
| `level` | `number` | Nivel actual del participante |
| `deleted` | `boolean` | Flag de soft-delete |
| `createdAt` | `Date` | Fecha de inscripción |
| `updatedAt` | `Date` | Fecha de última actividad |

**Relaciones:** `userId` → `users`, `courseId` → `courses`

---

## `activitycompletions`

Registro de finalización de actividades por participante. Almacena resultados y métricas.

| Campo | Tipo | Descripción |
|---|---|---|
| `_id` | `ObjectId` | Identificador único |
| `participantId` | `ObjectId` | Referencia a `participants._id` |
| `activityId` | `ObjectId` | Referencia a `activities._id` |
| `score` | `number` | Puntaje obtenido (0-100) |
| `date` | `Date` | Fecha de completación |
| `answers` | `object[]` | Respuestas del participante |
| `answers[].questionIndex` | `number` | Índice de la pregunta |
| `answers[].selectedIndex` | `number` | Índice de la respuesta seleccionada |
| `answers[].correct` | `boolean` | Si fue correcta |
| `deleted` | `boolean` | Flag de soft-delete |
| `createdAt` | `Date` | Fecha de creación |

**Relaciones:** `participantId` → `participants`, `activityId` → `activities`

---

## `feedbacks`

Feedback enviado por los usuarios del sistema. Puede ser funcionalidad solicitada o reporte de soporte.

| Campo | Tipo | Descripción |
|---|---|---|
| `_id` | `ObjectId` | Identificador único |
| `title` | `string` | Título del feedback |
| `description` | `string` | Cuerpo del mensaje |
| `serial` | `number` | Número serial único autoincremental |
| `isFeature` | `boolean` | Es solicitud de funcionalidad |
| `isSupport` | `boolean` | Es reporte de soporte técnico |
| `userId` | `ObjectId` | Referencia a `users._id` |
| `deleted` | `boolean` | Flag de soft-delete |
| `createdAt` | `Date` | Fecha de envío |

**Relaciones:** `userId` → `users`

---

## `courses`

Catálogo de cursos académicos disponibles en el sistema.

| Campo | Tipo | Descripción |
|---|---|---|
| `_id` | `ObjectId` | Identificador único |
| `name` | `string` | Nombre del curso |
| `highSchool` | `boolean` | Indica si es nivel bachillerato |
| `description` | `string` | Descripción del curso |
| `isActive` | `boolean` | Curso activo |
| `deleted` | `boolean` | Flag de soft-delete |
| `createdAt` | `Date` | Fecha de creación |
| `updatedAt` | `Date` | Fecha de modificación |

---

## `companies`

Empresas o instituciones educativas registradas en la plataforma.

| Campo | Tipo | Descripción |
|---|---|---|
| `_id` | `ObjectId` | Identificador único |
| `name` | `string` | Nombre de la empresa o institución |
| `nit` | `string` | NIT de la empresa |
| `contactEmail` | `string` | Correo de contacto |
| `contactPhone` | `string` | Teléfono de contacto |
| `isActive` | `boolean` | Estado de la empresa |
| `deleted` | `boolean` | Flag de soft-delete |
| `createdAt` | `Date` | Fecha de registro |
| `updatedAt` | `Date` | Fecha de modificación |

---

## `notifications`

Notificaciones dirigidas a usuarios del sistema.

| Campo | Tipo | Descripción |
|---|---|---|
| `_id` | `ObjectId` | Identificador único |
| `userId` | `ObjectId` | Referencia a `users._id` |
| `title` | `string` | Título de la notificación |
| `message` | `string` | Cuerpo del mensaje |
| `type` | `string` | Tipo: info, warning, success, error |
| `read` | `boolean` | Leída o no |
| `deleted` | `boolean` | Flag de soft-delete |
| `createdAt` | `Date` | Fecha de emisión |

**Relaciones:** `userId` → `users`

---

## `permissions`

Permisos granulares del sistema. Define acciones específicas que los roles pueden ejecutar.

| Campo | Tipo | Descripción |
|---|---|---|
| `_id` | `ObjectId` | Identificador único |
| `name` | `string` | Nombre del permiso (e.g. `activities.create`) |
| `description` | `string` | Descripción del permiso |
| `module` | `string` | Módulo al que pertenece |
| `isActive` | `boolean` | Permiso activo |
| `createdAt` | `Date` | Fecha de creación |

**Relaciones:** Referenciado desde `roles.permissions[]`

---

## `pretests`

Evaluaciones preliminares aplicadas a participantes para medir conocimiento previo.

| Campo | Tipo | Descripción |
|---|---|---|
| `_id` | `ObjectId` | Identificador único |
| `participantId` | `ObjectId` | Referencia a `participants._id` |
| `courseId` | `ObjectId` | Referencia a `courses._id` |
| `score` | `number` | Puntaje obtenido |
| `answers` | `object[]` | Respuestas del pretest |
| `answers[].question` | `string` | Pregunta |
| `answers[].selected` | `string` | Respuesta seleccionada |
| `answers[].correct` | `boolean` | Si fue correcta |
| `date` | `Date` | Fecha de aplicación |
| `deleted` | `boolean` | Flag de soft-delete |
| `createdAt` | `Date` | Fecha de creación |

**Relaciones:** `participantId` → `participants`, `courseId` → `courses`

---

## `policies`

Políticas de privacidad y términos de uso del sistema.

| Campo | Tipo | Descripción |
|---|---|---|
| `_id` | `ObjectId` | Identificador único |
| `title` | `string` | Título del documento |
| `content` | `string` | Cuerpo del documento (Markdown) |
| `version` | `string` | Versión semántica |
| `isActive` | `boolean` | Política vigente |
| `effectiveDate` | `Date` | Fecha de entrada en vigor |
| `createdAt` | `Date` | Fecha de creación |
| `updatedAt` | `Date` | Fecha de modificación |

---

## Diagrama de relaciones

```
users ──→ roles ──→ permissions
users ──→ companies
users ──→ participants ──→ courses
participants ──→ activitycompletions ──→ activities ──→ emotions
participants ──→ pretests ──→ courses
users ──→ feedbacks
users ──→ notifications
```
