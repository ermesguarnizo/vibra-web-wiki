---
title: Fase 4 — Integration Testing
description: Pruebas de integración entre componentes del ecosistema Vibra
---

# Fase 4: Integration Testing

## API Contracts

Se auditaron y corrigieron los contratos entre frontend y backend en los siguientes módulos:

### Actividades (API → Web/Mobile)

| Endpoint | Capa | Validación |
|----------|------|------------|
| `GET /api/activities` | API | Paginación con `page`/`rows`, filtro por emoción |
| `GET /api/activities/all` | Web | Historial paginado de actividades del usuario |
| `POST /api/activities/:id/:userId/submit` | Mobile | Envío de respuestas con tipado correcto |
| `GET /api/activities/user/:userId` | Mobile | Consulta de actividades por usuario |

### Emociones (API → Web)

| Endpoint | Capa | Validación |
|----------|------|------------|
| `GET /api/emotions` | API | Listado paginado con serial, nombre, descripción |
| `POST /api/emotions` | Web | Creación con payload alineado al schema MongoDB |

### Retos y Eventos Personales (API → Mobile)

| Endpoint | Capa | Validación |
|----------|------|------------|
| `GET /api/activities?type=reto` | Mobile | Filtro por tipo para ChallengesDashboard |
| `GET /api/activities?type=evento_personal` | Mobile | Filtro por tipo para PersonalEventsDashboard |

### Feedback (API → Web)

| Endpoint | Capa | Validación |
|----------|------|------------|
| `GET /api/feedback` | Web | Listado paginado para panel admin |
| `POST /api/feedback` | Web/Mobile | Creación de feedback con title + description |
| `POST /api/feedback/:id/convert-to-idea` | Web | Conversión feedback → backlog idea |

## States and Navigation

### Web (vibra-web)
- Sidebar dinámico filtrado por permisos del usuario
- Sistema de tabs (pestañas) para navegación entre módulos
- Estados de carga y vacío en tablas (Tests, Participantes, Actividades, Feedback)
- RefreshKey para recarga controlada de datos

### Mobile (vibra-web-mb)
- Navegación por Tabs (Eventos, Retos, E-Personal, Perfil)
- Protección de rutas con AuthContext
- Redirección al login si no hay sesión activa
- OfflineScreen + OfflineBanner para estados sin conexión

## Data Consistency

| Componente | Estrategia |
|------------|------------|
| Participantes en sesión | Context API + AsyncStorage (persistencia local) |
| Actividad diaria | React Query con staleTime configurado |
| Ranking | Agregaciones MongoDB con caché 30s |
| Feedback recién creado | Refresco automático de tabla post-creación |
