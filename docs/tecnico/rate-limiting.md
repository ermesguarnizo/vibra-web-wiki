---
title: Rate Limiting
description: Implementación de limitación de tasa en Vibra API
---

# Rate Limiting en Vibra API

## Descripción

Mecanismo de limitación de tasa implementado en Vibra API para proteger los endpoints contra abusos y ataques de fuerza bruta.

## Implementación

Usando el paquete `@nestjs/throttler` integrado con NestJS. La configuración actual limita las solicitudes por dirección IP.

### Configuración Actual

- **Límite de solicitudes**: 20 solicitudes
- **Período de tiempo**: 60 segundos (1 minuto)

Cada dirección IP puede hacer un máximo de 20 solicitudes por minuto a cualquier endpoint protegido.

## Estructura de Archivos

- `src/infrastructure/throttler/throttler.module.ts`: Configuración del ThrottlerModule
- `src/infrastructure/throttler/throttler.guard.ts`: Guard personalizado que aplica la limitación

## Respuesta al Cliente

Cuando se excede el límite, el cliente recibe:

- **HTTP 429** (Too Many Requests)
- **Mensaje**: "Too many requests, please try again later."
- **Timestamp**: Fecha y hora de la limitación

## Exclusiones

Endpoints excluidos de rate limiting:

- `/api`: Documentación Swagger
- `/health`: Health check de la aplicación

## Personalización

```typescript
ThrottlerModule.forRoot([
  {
    ttl: 60000, // tiempo en milisegundos (1 minuto)
    limit: 20,  // máximo de solicitudes en el período ttl
  },
]),
```

## Logging

Cuando se excede el límite, se registra una advertencia con la IP y la URL solicitada.
