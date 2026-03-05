---
id: architecture
title: Project Architecture
sidebar_label: Architecture
---

# Vibra API Project Architecture

This document describes the high-level architecture of the **Vibra API** project, a backend application built with **NestJS**.

## Overview

The project follows a modular architecture (Modular Monolith) inspired by **Domain-Driven Design (DDD)** and **Hexagonal Architecture** principles. The code is primarily organized by "domains" (features) rather than technical layers, which facilitates scalability and maintenance.

## Main Technologies

- **Framework**: [NestJS](https://nestjs.com/) (Node.js)
- **Language**: TypeScript
- **Primary Database**: MongoDB (using [Mongoose](https://mongoosejs.com/))
- **Cache / Queue**: Redis (configured for rankings and sockets)
- **Real-time Communication**: WebSockets (Socket.io)
- **File Storage**: MongoDB GridFS
- **Email**: SendGrid / Nodemailer

## Project Structure

The directory structure in `src/` is as follows:

```
src/
├── common/           # DTOs, interfaces, and shared utilities
├── config/           # Environment variables and database configuration
├── domains/          # Business modules (Features)
│   ├── activities/   # Activities and schedules management
│   ├── auth/         # Authentication and authorization (JWT)
│   ├── users/        # User management
│   ├── ...           # Other domains (company, client, reports, etc.)
├── infrastructure/   # Technical implementations
│   ├── emails/       # Email sending service
│   ├── file-upload/  # File upload service (GridFS)
│   ├── sockets/      # WebSocket Gateways
│   └── throttler/    # Rate limiting
├── helpers/          # Cross-cutting helpers (Logger, etc.)
└── main.ts           # Application entry point
```

## Architecture Diagram (Conceptual)

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

## Main Modules (Domains)

### Core
- **Auth**: Handles registration, login, and JWT token validation. Uses `Passport` and JWT strategies.
- **Users**: Management of user profiles, roles, and permissions.
- **Config**: Centralized configuration management.

### Business
- **Activities**: Core logic for scheduled activities.
- **Companies**: Management of companies/organizations.
- **Clients**: Management of company clients.
- **Emotions**: Handling of emotions and mood.
- **Notifications**: Handling of internal notifications.
- **Rankings**: Scoring and classification system (possibly using Redis for performance).
- **Reports**: Generation of reports based on user and activity data.
- **Scheduling**: Weekly scheduling and task assignment.

### Infrastructure and Support
- **AuditLog**: Audit log of important actions.
- **Notification**: Internal and push notification system.
- **FileUpload**: Abstraction for uploading files to GridFS.
- **Sockets**: `AppGateway` and `EventsGateway` handle user presence and real-time events.

## Database

The project uses **MongoDB** as the primary data source. **Mongoose** is used as the ODM.

### Key Collections
- `Users`: System users.
- `Companies`: Corporate entities.
- `Activities`: Assignable activities.
- `AuditLogs`: Traceability.
- `UserSessions`: Session history.

## Security

- **Authentication**: JWT (JSON Web Tokens).
- **Rate Limiting**: Implemented with `@nestjs/throttler` to prevent abuse.
- **Validation**: DTOs with `class-validator`.
- **Guards**: `AuthGuard` and `PermissionsGuard` (implicit in role logic) protect endpoints.

## Deployment and Execution

- **Build**: `npm run build`
- **Start**: `npm run start-env` (for development)
- **Start Prod**: `npm run start:prod` (for production)
