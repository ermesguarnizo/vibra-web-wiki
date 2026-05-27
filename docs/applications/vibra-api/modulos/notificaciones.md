---
title: Módulo Notificaciones
description: Sistema de notificaciones push y en-band — schema Notification, Firebase/Expo Push, WebSocket gateway, CRUD
---

# Módulo Notificaciones

El módulo `Notification` (`src/domains/notification/`) implementa el sistema de notificaciones de la plataforma, combinando almacenamiento en MongoDB con envío push vía Expo Push API y actualizaciones en tiempo real vía WebSocket.

## Schema `Notification`

Definido en `schemas/notification.schema.ts`. Colección: `notifications`.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `ID` | `string` | Identificador interno |
| `title` | `string` | Título de la notificación |
| `message` | `string` | Cuerpo del mensaje |
| `isRead` | `boolean` | Estado de lectura |
| `user` | `ObjectId` → `User` | Usuario destinatario |
| `notificationType` | `ObjectId` → `NotificationType` | Tipo de notificación |
| `notificationChannel` | `ObjectId` → `NotificationChannel` | Canal de entrega |
| `priority` | `number` | Prioridad (1-5) |
| `serial` | `string` | Serial auto-generado |
| `isActive` | `boolean` | Estado activo |
| `createdBy` | `string` | Creador de la notificación |

## Endpoints CRUD

| Método | Ruta | Descripción |
|--------|------|-------------|
| `POST` | `/api/notifications` | Crear notificación |
| `POST` | `/api/notifications/batch` | Crear múltiples notificaciones |
| `GET` | `/api/notifications/all` | Listar todas (paginado, popula user/type/channel) |
| `GET` | `/api/notifications/:id` | Obtener por ID (popula relaciones) |
| `GET` | `/api/notifications/title/:title` | Obtener por título |
| `PUT` | `/api/notifications/:id` | Actualizar notificación |
| `DELETE` | `/api/notifications/:id` | Eliminar notificación |
| `PUT` | `/api/notifications/read/:id` | Marcar como leída |
| `GET` | `/api/notifications/unread/count/:id` | Conteo de no leídas por usuario |
| `GET` | `/api/notifications/count-all-notifications` | Conteo total |
| `GET` | `/api/notifications/count-all-notifications-by-day` | Conteo por día |
| `GET` | `/api/notifications/search/:term` | Buscar por término |
| `POST` | `/api/notifications/batch/upload` | Carga masiva CSV |

## Push Notifications con Firebase / Expo

El servicio `PushNotificationService` (`src/domains/push-notifications/`) maneja el envío de notificaciones push usando la **Expo Push API**.

### Registro de dispositivos

| Método | Ruta | Descripción |
|--------|------|-------------|
| `POST` | `/api/push-notifications/register` | Registrar token push de dispositivo |
| `POST` | `/api/push-notifications/unregister` | Desactivar token (logout) |

Los tokens se almacenan en la colección `devicetokens` con `{ token, userId, platform, isActive }`.

### Envío a todos (`sendPushToAll`)

1. Obtiene todos los tokens activos de la BD
2. Construye mensajes `ExpoPushMessage: { to, sound, title, body, data }`
3. Envía en chunks de máximo 100 mensajes (límite de Expo Push API)
4. Procesa tickets de error: si `DeviceNotRegistered` o `InvalidCredentials`, desactiva el token

### Integración con Activities

Cuando se crea una actividad con fecha igual al día actual, `ActivitiesService.create()` envía automáticamente una notificación push a todos los dispositivos:

```
Título: "🎉 ¡Nueva actividad disponible!"
Cuerpo: "Ya tienes una actividad para hoy. ¡Descúbrela ahora!"
Data: { url: "/features/(tabs)/one", type: "new_activity" }
```

## WebSocket Gateway

### AppGateway (`src/infrastructure/sockets/appGateway.gateway.ts`)

Gateway principal en el namespace raíz que maneja:
- **Conexión/desconexión**: rastrea usuarios conectados en `connectedUsers` Map
- **`users-update`**: emite lista de usuarios activos a todos los clientes
- **`server-status`**: estado del servidor con conteo de usuarios conectados
- **`identifyUser`**: registra usuario en el mapa de conectados
- **`requestUsersUpdate`**: fuerza actualización de lista de usuarios

### EventsGateway (`src/infrastructure/sockets/events.gateway.ts`)

Gateway para eventos adicionales en tiempo real. Se conecta al ranking service para emitir actualizaciones del leaderboard.

## Estadísticas

El módulo expone endpoints para métricas:
- `count-all-notifications` — Conteo total de notificaciones
- `count-all-notifications-by-day` — Conteo agregado por día
- `unread/count/:id` — Notificaciones no leídas de un usuario específico
