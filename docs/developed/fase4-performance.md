---
title: Fase 4 — Load & Performance Testing
description: Pruebas de carga y optimización de rendimiento en Vibra
---

# Fase 4: Load & Performance Testing

## API — Consultas Optimizadas

Se implementaron optimizaciones directas en MongoDB:

| Colección | Índice / Mejora | Impacto |
|-----------|----------------|---------|
| `activities` | Índice compuesto `{type: 1, isActive: 1}` | Filtros por tipo de actividad |
| `participants` | Índice en `userId` | Lookup por usuario en rankings |
| `feedbacks` | Índice en `serial` | Ordenamiento descendente en listados |
| `activitycompletions` | Índice compuesto `{participantId: 1, date: -1}` | Agregaciones de puntaje |

## Web — Lighthouse Metrics

Optimizaciones realizadas en vibra-web:

- **Bundling**: Eliminación de dependencias no utilizadas
- **Imágenes**: Uso de etiquetas `<img>` estándar con `unoptimized: true` (build estático)
- **CSS**: Tailwind purge en producción (clases no usadas eliminadas)
- **Animaciones**: Transiciones CSS nativas en sidebar, eliminando JS hover innecesario

## Mobile — Performance Profiling

- **Animaciones**: Reemplazo de `useState+useEffect` por animaciones CSS nativas
- **Listas**: Uso de `FlatList` con claves estables para scroll eficiente
- **Carga de datos**: React Query con caché y refetch controlado
- **Offline**: Detección temprana de conectividad para evitar llamadas fallidas
