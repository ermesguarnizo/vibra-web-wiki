---
title: Módulo Autenticación
description: JWT login/register, PermissionGuard, decoradores @RequirePermission y @BypassPermission, roles, OTP y cambio de contraseña
---

# Módulo Autenticación

El módulo `Auth` (`src/domains/auth/`) implementa la autenticación y autorización de la API. Usa JWT con Passport y un sistema de permisos granular basado en seriales.

## JWT Login / Register

### `POST /api/auth/register`

Registra un nuevo usuario. Acepta `RegisterDto` con `username`, `password`, `email`, `name`, `documentNumber`, `avatar`.

### `POST /api/auth/login`

Valida credenciales (`username` + `password`) usando bcrypt. Retorna un JWT con payload:

```json
{
  "access_token": "eyJ...",
  "expiresIn": "24h"
}
```

Payload del token: `{ documentNumber, name, username, sub, email, role, company, avatar }`.

La secret key se define en `JWT_SECRET` (variable de entorno). El login también actualiza `isLogged=true` en el usuario y emite `sendActiveUsers()` vía WebSocket.

## PermissionGuard

Definido en `src/infrastructure/auth/permission.guard.ts`. Es un guard unificado que:
1. Extrae el JWT del header `Authorization` (Bearer) o `x-access-token`
2. Si hay token válido, decodifica y adjunta `req.user`
3. Si el endpoint tiene `@RequirePermission`, verifica que el usuario tenga ese permiso (o sea SuperAdmin)
4. Si no requiere permiso, deja pasar (incluso sin token)

### @RequirePermission decorator

```typescript
@RequirePermission('16')  // Permiso requerido: serial '16'
```

Se aplica a nivel de clase (controller) para proteger todos los endpoints del módulo. Cada módulo tiene un serial de permiso diferente:
- `8` — Emotions
- `9` — Participants
- `10` — Courses
- `15` — PreTests
- `16` — Activities

### @BypassPermission decorator

```typescript
@BypassPermission()
@Get()
```

Se aplica a nivel de método para saltar la verificación de permisos, incluso si el controller tiene `@RequirePermission` a nivel de clase. Se usa en endpoints públicos como listar actividades activas.

### @Public decorator

Salta el JWT guard por completo. Usado en endpoints como `POST /api/auth/login` y `POST /api/auth/health`.

## Resolución de permisos

El método `resolvePermissions(userId)` en `AuthService` sigue este flujo:

1. Obtiene el rol del usuario con su plantilla de permisos
2. Consulta asignaciones directas (`UserPermission`)
3. Si es SuperAdmin, retorna **todos** los permisos del sistema
4. Une y deduplica ambas listas
5. Cachea el resultado por 60s via `PermissionCacheService`

Respuesta: `{ isSuperAdmin, role, permissions[], serials[] }`

## Cambio de contraseña

`POST /api/auth/change-password`

Verifica la contraseña actual con bcrypt, valida que la nueva no sea igual a la actual, hashea y guarda.

## Logout

`POST /api/auth/logout`

- Actualiza `isLogged=false` en el usuario
- Invalida el token (almacenado en un mapa en memoria con limpieza cada 24h)
- Destruye la sesión si existe

## Otros endpoints

| Método | Ruta | Descripción |
|--------|------|-------------|
| `POST` | `/api/auth/health` | Health check del servicio |
| `GET` | `/api/auth/profile` | Perfil del usuario autenticado |
| `GET` | `/api/auth/my-permissions` | Permisos del usuario autenticado |
| `POST` | `/api/auth/refresh-token` | Refrescar JWT |
| `POST` | `/api/auth/verify-token` | Verificar validez de un token |
