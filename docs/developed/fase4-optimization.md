---
title: Fase 4 — Final Optimization
description: Optimizaciones finales de rendimiento y experiencia de usuario
---

# Fase 4: Final Optimization

## MongoDB Indexing

```javascript
// Colección activities
db.activities.createIndex({ type: 1, isActive: 1 });

// Colección participants  
db.participants.createIndex({ userId: 1 });

// Colección activitycompletions
db.activitycompletions.createIndex({ participantId: 1, date: -1 });

// Colección feedbacks
db.feedbacks.createIndex({ serial: -1 });
db.feedbacks.createIndex({ deleted: 1 });
```

## HTTP Caching

| Estrategia | Implementación |
|------------|---------------|
| React Query staleTime | 30s para consultas de ranking y actividad diaria |
| Caché de permisos | PermissionCacheService con TTL configurable |
| Refresh bajo demanda | Botón Refresh en tablas con recarga controlada |

## Bundling and Bundle Reduction

### Web (vibra-web)
- Build estático (`output: 'export'` en next.config.mjs)
- Imágenes sin optimización forzada de Next.js
- Tailwind purge automático en producción

### Mobile (vibra-web-mb)
- Metro bundler con tree-shaking
- Imágenes en assets locales (sin carga remota)
- Lazy loading de módulos con `expo-router`

## UI y Experiencia de Usuario

| Mejora | Módulo | Beneficio |
|--------|--------|-----------|
| Sidebar con animaciones CSS puras (vibra-065) | Web | Elimina JS hover, reduce re-renders |
| OfflineScreen + OfflineBanner (vibra-063) | Mobile | Feedback inmediato al usuario sin conexión |
| Autocompletado de tags (vibra-072) | Web | Consistencia en backlog de ideas |
| FAB feedback flotante (vibra-073) | Web/Mobile | Acceso rápido a envío de feedback |
