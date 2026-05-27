---
title: Arquitectura
description: Arquitectura del backend Vibra API (NestJS + MongoDB)
---

# Vibra API — Arquitectura

**Vibra API** es la aplicación backend del ecosistema Vibra, construida con NestJS y MongoDB. Gestiona los datos de la aplicación móvil y el panel de administración web.

## Tecnologías

<p align="center">
  <a href="https://nestjs.com/" target="_blank">
  <img src="https://img.shields.io/badge/NestJS-10.x-E0234E?style=flat&logo=nestjs&logoColor=white" alt="NestJS" />
  </a> 
  <a href="https://www.typescriptlang.org/" target="_blank">
  <img src="https://img.shields.io/badge/TypeScript-5.x-3178C6?style=flat&logo=typescript&logoColor=white" alt="TypeScript" />
  </a>
  <a href="https://www.mongodb.com/" target="_blank">
  <img src="https://img.shields.io/badge/MongoDB-7.x-47A248?style=flat&logo=mongodb&logoColor=white" alt="MongoDB" />
  </a>
  <a href="https://socket.io/" target="_blank">
  <img src="https://img.shields.io/badge/Socket.io-4.x-010101?style=flat&logo=socket.io&logoColor=white" alt="Socket.io" />
  </a>
  <a href="https://swagger.io/" target="_blank">
  <img src="https://img.shields.io/badge/Swagger-3.x-85EA2D?style=flat&logo=swagger&logoColor=white" alt="Swagger" />
  </a>
  <a href="https://jestjs.io/" target="_blank">
  <img src="https://img.shields.io/badge/Jest-29.x-C21325?style=flat&logo=jest&logoColor=white" alt="Jest" />
  </a>
  <a href="https://www.docker.com/" target="_blank">
  <img src="https://img.shields.io/badge/Docker-24.x-2496ED?style=flat&logo=docker&logoColor=white" alt="Docker" />
  </a>
</p>

## Visión General

El proyecto sigue una **arquitectura modular** (Modular Monolith) inspirada en principios de **Domain-Driven Design (DDD)** y **Arquitectura Hexagonal**. El código se organiza principalmente por "dominios" (funcionalidades) en lugar de capas técnicas.

## Estructura del Proyecto

```
src/
├── common/           # DTOs, interfaces y utilidades compartidas
├── config/           # Variables de entorno y configuración de BD
├── domains/          # Módulos de negocio (Features)
│   ├── activities/   # Gestión de actividades y horarios
│   ├── auth/         # Autenticación y autorización (JWT)
│   ├── users/        # Gestión de usuarios
│   ├── ...           # Otros dominios (company, client, reports, etc.)
├── infrastructure/   # Implementaciones técnicas
│   ├── emails/       # Servicio de envío de emails
│   ├── file-upload/  # Servicio de subida de archivos (GridFS)
│   ├── sockets/      # WebSocket Gateways
│   └── throttler/    # Rate limiting
├── helpers/          # Helpers transversales (Logger, etc.)
└── main.ts           # Punto de entrada
```

## Diagrama de Arquitectura

```mermaid
graph TD
    Client[Web/Mobile Client] -->|HTTP/REST| API[Vibra API (NestJS)]
    Client -->|WebSocket| SocketGW[Socket Gateway]
    
    subgraph "Vibra API"
        Auth[Auth Module]
        Users[Users Module]
        Activities[Activities Module]
        Infra[Infrastructure Modules]
    end
    
    API --> Auth
    API --> Users
    API --> Activities
    
    Auth --> DB[(MongoDB)]
    Users --> DB
    Activities --> DB
    
    Infra -->|Email| SendGrid[SendGrid / SMTP]
    Infra -->|Files| GridFS[MongoDB GridFS]
    Infra -->|Cache| Redis[Redis]
```

## Módulos Principales

### Core
- **Auth**: Registro, login y validación de JWT. Usa Passport y estrategias JWT.
- **Users**: Perfiles de usuario, roles y permisos.
- **Config**: Gestión centralizada de configuración.

### Negocio
- **Activities**: Lógica principal de actividades programadas.
- **Companies**: Gestión de empresas/organizaciones.
- **Clients**: Gestión de clientes de empresa.
- **Emotions**: Manejo de emociones y estado de ánimo.
- **Notifications**: Notificaciones internas.
- **Rankings**: Sistema de puntuación y clasificación (con Redis para rendimiento).
- **Reports**: Generación de reportes.
- **Scheduling**: Programación semanal y asignación de tareas.

### Infraestructura
- **AuditLog**: Registro de auditoría de acciones importantes.
- **Notification**: Sistema de notificaciones internas y push.
- **FileUpload**: Abstracción para subir archivos a GridFS.
- **Sockets**: `AppGateway` y `EventsGateway` para presencia de usuarios y eventos en tiempo real.

## Base de Datos

Usa **MongoDB** con **Mongoose** como ODM.

### Colecciones Principales
- `users`: Usuarios del sistema
- `companies`: Entidades corporativas
- `activities`: Actividades asignables
- `auditLogs`: Trazabilidad
- `userSessions`: Historial de sesiones

## Seguridad

- **Autenticación**: JWT (JSON Web Tokens)
- **Rate Limiting**: Implementado con `@nestjs/throttler`
- **Validación**: DTOs con `class-validator`
- **Guards**: `AuthGuard` y `PermissionsGuard` protegen endpoints

## Configuraciones del Proyecto

| Herramienta   | Propósito        |
|---------------|------------------|
| TypeScript    | Lenguaje principal |
| ESLint        | Linting           |
| Prettier      | Formateo          |
| Swagger       | Documentación API |
| Jest          | Testing           |
| Docker        | Contenedores      |
| Cron          | Tareas programadas |

## Inicio Rápido

```bash
git clone https://github.com/Vibra-Maya/vibra-api.git
npm i
# Configurar .env
npm run start-env
# http://localhost:4000
# Swagger: http://localhost:4000/api
```

## Despliegue

```bash
npm run compile
npm run start:prod
```

## Autores

- **Ermes Guarnizo Motta** - Ingeniero de Sistemas - [Guarnizo2023](https://github.com/Guarnizo2023)
- **Yovany Suárez Silva** - Full Stack Software Engineer - [desobsesor](https://github.com/desobsesor)
