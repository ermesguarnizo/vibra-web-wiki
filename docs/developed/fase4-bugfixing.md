---
title: Fase 4 — Bug Fixing
description: Corrección de bugs identificados durante la integración
---

# Fase 4: Bug Fixing

## High Severity

| ID | Descripción | Módulo | Resolución |
|----|------------|--------|------------|
| vibra-025 | Navegación entre actividades al hacer click en Continuar | Mobile | `nextActivityType()` con fallback, `reset()` preserva games |
| vibra-055 | Error UNAUTHORIZED en todas las peticiones GET desde mobile | Mobile | Interceptor JWT reactivado en api.ts |
| vibra-056 | Error 403 en tabs Retos y E-Personal | API | `@BypassPermission()` agregado en endpoints consumidos por mobile |
| vibra-061 | Ruteo inicial no redirige a tabs si hay sesión activa | Mobile | `useEffect` de montaje en AuthProvider con `checkAuth()` |

## Medium Severity

| ID | Descripción | Módulo | Resolución |
|----|------------|--------|------------|
| vibra-014 | Formulario de actividades enviaba payload incorrecto | Web | Payload realineado con CreateActivityDto del backend |
| vibra-014 | Lista de actividades tenía infinite loop | Web | `refreshKey` reemplaza `countData` en dependencias |
| vibra-057 | Error 403 en emociones desde mobile | API | `@BypassPermission()` en GET /api/emotions |
| vibra-059 | Logout no resetea completamente datos de sesión | Mobile | Limpieza de token, store y contexto |

## Low Severity

| ID | Descripción | Módulo | Resolución |
|----|------------|--------|------------|
| vibra-050 | Formulario de plantillas de permisos inconsistente | Web | Reescribir con PermissionPicker estandarizado |
| vibra-064 | Recuperación de contraseña no iniciaba en mobile | Mobile | Integración con endpoint existente de vibra-api |
