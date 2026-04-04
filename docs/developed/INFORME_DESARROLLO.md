---
id: desarrollo-capa-api
title: Desarrollo de la Capa API
sidebar_label: Desarrollo de la Capa API
---

# Informe de actividades desarrolladas en las semanas 1,2,3,4 en el proyecto Vibra para el desarrollo de la capa API

Este documento detalla el estado actual de las tareas de desarrollo del proyecto **vibra-api**, cubriendo la arquitectura implementada, el desglose técnico de los módulos desarrollados y la evidencia de los endpoints construidos.

## 1. Arquitectura Técnica Implementada

El proyecto **vibra-api** se ha construido sobre una base sólida y escalable, siguiendo las mejores prácticas de desarrollo con **NestJS**.

![screenshot notificaciones](/img/screenshot/api.png)

### Tecnologías Base
- **Framework:** NestJS (Node.js)
- **Base de Datos:** MongoDB (con Mongoose como ODM)
- **Lenguaje:** TypeScript

### Patrones y Estructura
Se ha implementado una **Arquitectura Modular** con principios de **Clean/Hexagonal Architecture**, separando claramente las responsabilidades:
- **Domains (`src/domains/`):** Contiene la lógica de negocio agrupada por módulos funcionales.
- **Infrastructure (`src/infrastructure/`):** Servicios técnicos transversales (Auth, Emails, Sockets, Uploads).
- **Common/Utils/Helpers:** Utilidades compartidas.

### Desarrollo de Configuraciones Globales
Se han establecido los cimientos técnicos del API:
- **Documentación API:** Swagger (OpenAPI) configurado y accesible en `/api`.
- **Validación de Datos:** `ValidationPipe` global implementado usando `class-validator` y `class-transformer` en los DTOs.
- **Manejo de Logs:** Implementación robusta con `Winston` (logs en consola y archivo de errores).
- **Seguridad:**
  - Autenticación JWT (JSON Web Tokens).
  - Guards para protección de rutas (`JwtAuthGuard`).
  - CORS habilitado y configurado.
  - Throttling (Rate Limiting) para protección contra ataques de fuerza bruta.
- **Manejo de Errores:** Filtros de excepciones globales (`ThrottlerExceptionFilter`).

## 2. Desarrollo de Módulos (Backend)

Se ha completado el desarrollo de los módulos core planificados y se han añadido módulos adicionales para soportar funcionalidades avanzadas.

### Dominios Core (Planificados)
| Dominio | Estado | Detalles de Implementación |
| :--- | :--- | :--- |
| **Users** | ✅ Completado | Gestión de usuarios, esquemas con auditoría (`createdBy`, `editedBy`) y soft-delete (`deletedAt`). Integración con Sockets. |
| **Auth** | ✅ Completado | Login, Registro, Estrategias JWT, Protección de rutas. |
| **Roles** | ✅ Completado | Gestión de roles y permisos (RBAC). |
| **Activities** | ✅ Completado | Gestión de actividades, programación semanal (`weekly-schedule`), respuestas de usuarios. |
| **Emotions** | ✅ Completado | Registro y gestión de emociones. |
| **UserResponses** | ✅ Completado | Almacenamiento de respuestas de usuarios a actividades. |
| **Notifications** | ✅ Completado | Sistema completo con Tipos, Canales y gestión de notificaciones. |
| **Permissions** | ✅ Completado | Sistema granular de permisos, categorías y plantillas (`permissionTemplates`). |
| **Companies** | ✅ Completado | Gestión de organizaciones/instituciones educativas. |
| **Policies** | ✅ Completado | Gestión de políticas de privacidad y términos. |

### Dominios Adicionales (Valor Agregado)
Además de lo planificado principalmente, se han desarrollado los siguientes módulos:
- **AuditLog:** Registro de auditoría de acciones del sistema.
- **Rankings:** Sistema de clasificación con actualizaciones en tiempo real vía WebSockets.
- **Feedback:** Módulo de retroalimentación.
- **CronJob:** Gestión de tareas programadas.
- **Translates:** Sistema de internacionalización/traducciones almacenadas en BD.
- **Reports:** Generación de reportes.
- **Files:** Gestión de metadatos de archivos.
- **Admin & MongoDBAdmin:** Herramientas administrativas.
- **Documentación API:** Swagger (OpenAPI) configurado en `/api`.
- **Validación:** `ValidationPipe` global con `class-validator`.
- **Logs:** `Winston Logger` para trazabilidad y errores.
- **Seguridad:** JWT, Guards, CORS, Throttling.

---

## 3. Detalle de Tareas de Desarrollo por Módulo

A continuación se describen las tareas técnicas realizadas para la construcción de cada módulo, incluyendo diseño de datos, lógica de negocio y endpoints expuestos.

### 3.1. Módulo de Autenticación (Auth)
**Descripción:** Sistema robusto de seguridad para gestión de acceso y sesiones.

**Tareas de Desarrollo Realizadas:**
- **Lógica de Negocio (`AuthService`):**
  - Implementación de `validateUser` para verificar credenciales (soporte para email/username).
  - Integración de `bcrypt` para comparación segura de contraseñas.
  - Generación de JWT (`generateToken`) con payload enriquecido (datos de usuario, rol, compañía, avatar) para reducir consultas desde el frontend.
  - Gestión de `InvalidatedTokens` en memoria para control de seguridad.
  - Emisión de eventos de conexión vía WebSockets (`AppGateway`) al iniciar sesión.
- **Controlador (`AuthController`):**
  - Endpoints públicos para Login y Registro.
  - Protección de rutas sensibles mediante `JwtAuthGuard`.

**Endpoints Implementados:**
- `POST /api/auth/login`: Autenticación de usuarios.
- `POST /api/auth/register`: Registro de nuevos usuarios.
- `GET /api/auth/profile`: Obtención de datos del usuario autenticado.


![Collection diagram](/img/diagramcollectionsmin.png)

---

### 3.2. Módulo de Usuarios (Users)
**Descripción:** Gestión integral del ciclo de vida de los usuarios.

**Tareas de Desarrollo Realizadas:**
- **Modelo de Datos (`UserSchema`):**
  - Definición de campos con validación estricta (`required`, `unique`, `enum` para Género).
  - Implementación de **Auditoría de Datos**: Campos `createdBy`, `editedBy`, `editedAt`.
  - Implementación de **Soft Delete**: Campos `deleted`, `deletedAt`, `deletedBy` para no perder histórico.
  - Relaciones: Referencias `ObjectId` a `Role` y `Company`.
- **Lógica de Negocio (`UsersService`):**
  - CRUD completo con validaciones de unicidad (email, username, documento).
  - Lógica de paginación optimizada.
  - Integración con `EventsGateway` para notificar creación de usuarios en tiempo real.

**Endpoints Implementados:**
- `POST /api/users/create`: Creación de usuarios.
- `GET /api/users/allPaginate`: Listado paginado de usuarios.
- `GET /api/users/search/:username`: Búsqueda por nombre de usuario.
- `GET /api/users/trigger`: Endpoint de prueba para eventos en tiempo real.

> **[Insertar captura de Swagger del módulo Users aquí]**

---

### 3.3. Módulo de Actividades (Activities)
**Descripción:** Núcleo de la experiencia de usuario, gestionando tareas y contenido interactivo.

**Tareas de Desarrollo Realizadas:**
- **Modelo de Datos (`ActivitySchema`):**
  - Estructura flexible para diferentes tipos de actividades.
  - Relación con `Emotion` y `WeeklySchedule`.
- **Lógica de Negocio (`ActivitiesService`):**
  - **Paginación Avanzada (`paginate`):** Uso de `Promise.all` para ejecutar en paralelo:
    1. Consulta de actividades filtradas y ordenadas.
    2. Conteo total de documentos.
    3. Consulta de `UserResponses` para enriquecer la respuesta (saber si el usuario ya completó la actividad).
  - Métodos auxiliares: `getAvailableEmotions`, `getRandomEmotions`, `getTodaysActivity`.
  - Lógica de `softDelete` para desactivar actividades sin borrarlas.

**Endpoints Implementados:**
- `POST /api/activities`: Crear nueva actividad.
- `GET /api/activities`: Obtener actividades (con filtros y paginación).
- `GET /api/activities/:id`: Detalle de actividad.
- `PATCH /api/activities/:id`: Actualización parcial.
- `DELETE /api/activities/:id`: Eliminación lógica.

> **[Insertar captura de Swagger del módulo Activities aquí]**

---

### 3.4. Módulo de Respuestas de Usuario (UserResponses)
**Descripción:** Registro del desempeño y participación de los usuarios.

**Tareas de Desarrollo Realizadas:**
- **Modelo de Datos (`UserResponseSchema`):**
  - Almacenamiento detallado de respuestas: Array de objetos con `questionId`, `answer`, `isCorrect`, `responseTime`.
  - Índices compuestos (`user` + `activity`) para consultas rápidas.
  - **Propiedades Virtuales:** Cálculo automático de `timeSpent` (tiempo dedicado) basado en `startTime` y `endTime`.
- **Lógica de Negocio:**
  - Cálculo de puntajes (`score`).
  - Validación de respuestas anidadas.

**Endpoints Implementados:**
- `POST /api/user-responses`: Registrar respuesta a una actividad.
- `GET /api/user-responses/user/:userId`: Historial de un usuario.

> **[Insertar captura de Swagger del módulo UserResponses aquí]**

---

### 3.5. Módulo de Notificaciones (Notifications)
**Descripción:** Sistema de comunicación multicanal.

**Tareas de Desarrollo Realizadas:**
- **Modelo de Datos (`NotificationSchema`):**
  - Clasificación por `NotificationType` (Alerta, Info) y `NotificationChannel` (Inbox, Email, Push).
  - Generación automática de seriales (`generateSerial`).
- **Lógica de Negocio (`NotificationService`):**
  - Métodos especializados: `createGeneralNotification` (broadcast) y `createNotification` (personalizada).
  - **Carga Masiva:** Implementación de `bulkCreate` con procesamiento de archivos CSV/Excel usando `multer` y streams.
  - Gestión de estado de lectura (`isRead`).

**Endpoints Implementados:**
- `POST /api/notifications`: Crear notificación.
- `POST /api/notifications/bulk`: Carga masiva de notificaciones.
- `GET /api/notifications`: Listar notificaciones del usuario.

> **[Insertar captura de Swagger del módulo Notifications aquí]**

---

### 3.6. Módulo de Permisos y Roles (Permissions & Roles)
**Descripción:** Control de acceso basado en roles (RBAC) granular.

**Tareas de Desarrollo Realizadas:**
- **Modelo de Datos:**
  - `RoleSchema`: Definición de roles.
  - `PermissionSchema`: Definición atómica de permisos.
  - `PermissionTemplate`: Plantillas predefinidas de permisos para facilitar la asignación.
- **Lógica de Negocio:**
  - CRUD completo para administración de seguridad.
  - Servicios para validar permisos en tiempo real.

**Endpoints Implementados:**
- `GET /api/roles`: Listado de roles.
- `POST /api/permissions`: Creación de permisos.
- `GET /api/permissions`: Listado de permisos disponibles.

> **[Insertar captura de Swagger de Roles/Permissions aquí]**

---

## 4. Infraestructura y Servicios Transversales

- **WebSockets (Socket.IO):** Implementado en `infrastructure/sockets` para eventos en tiempo real (ej. notificaciones, rankings).
- **Email Service:** Servicio de envío de correos electrónicos (`infrastructure/emails`).
- **File Upload:** Servicio para carga de archivos (`infrastructure/file-upload`).
- **Scheduling:** Servicio de programador semanal (`scheduling`).

## 5. Repositorio de Código

El código fuente del proyecto **vibra-api** se encuentra alojado y protegido en un repositorio de Git, garantizando el control de versiones y la seguridad de la propiedad.

- **URL del Repositorio:** `https://github.com/proyectohhss2025-unad/vibra-api`
- **URL del repositorio de documentación:** `https://github.com/proyectohhss2025-unad/vibra-web-wiki`

**Ejemplo de acceso para clonar el repositorio:**
```bash
git clone https://github.com/proyectohhss2025-unad/vibra-api
```

## 6. Comparativa con el Plan Inicial a desarrollar

### Hito 1: Planificación y Análisis (Semanas 1–2)
- **Estado:** ✅ Completado.
- **Evidencia:** La estructura del proyecto **vibra-api** refleja una definición clara de requisitos y arquitectura. Los modelos de datos en MongoDB cubren todos los dominios especificados.

### Hito 2: Desarrollo Backend (API) (Semanas 3–4)
- **Configuración NestJS:** ✅ Completada (Estructura modular, Validaciones, Logger, Swagger).
- **Modelos de Datos:** ✅ Completados y extendidos. Se implementaron referencias `ObjectId`, patrones de auditoría y soft-delete en los esquemas.
- **Endpoints Básicos (CRUD):** ✅ Completados para todos los dominios principales y extendidos a dominios de soporte.

## Conclusión
El API de Vibra (`vibra-api`) ha superado las expectativas iniciales de la fase de "Desarrollo Backend". No solo se han implementado todos los módulos requeridos para la gestión de usuarios, actividades y notificaciones, sino que se ha establecido una infraestructura robusta con características avanzadas como comunicación en tiempo real (Sockets), auditoría detallada, y un sistema de permisos granular, dejando una base sólida para las siguientes fases del proyecto (Integración Frontend y Móvil).
