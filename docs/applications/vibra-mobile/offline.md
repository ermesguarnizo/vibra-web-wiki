---
title: Modo Offline
description: Soporte para modo sin conexión, almacenamiento local con AsyncStorage, SecureStore y manejo de errores de red
---

# Modo Offline

La aplicación maneja la falta de conectividad mediante almacenamiento local, manejo de errores de red y persistencia de sesión.

## Capa de Almacenamiento

`app/shared/store/storage.ts`

Capa unificada que combina `@react-native-async-storage/async-storage` para datos generales y `expo-secure-store` para datos sensibles.

### SecureStore (Tokens JWT)

```typescript
async setToken(value: string): Promise<void> {
  return SecureStore.setItemAsync('auth_token', value);
}
async getToken(): Promise<string | null> {
  return SecureStore.getItemAsync('auth_token');
}
async removeToken(): Promise<void> {
  return SecureStore.deleteItemAsync('auth_token');
}
```

### AsyncStorage (Datos Generales)

```typescript
async setItem(key: string, value: any): Promise<void> {
  const jsonValue = JSON.stringify(value);
  return AsyncStorage.setItem(`app_${key}`, jsonValue);
}
async getItem<T>(key: string): Promise<T | null> {
  const jsonValue = await AsyncStorage.getItem(`app_${key}`);
  return jsonValue ? JSON.parse(jsonValue) : null;
}
```

Todos los items usan el prefijo `app_` para evitar colisiones.

### Limpieza Global

```typescript
async clear(): Promise<void> {
  await SecureStore.deleteItemAsync('auth_token');
  await AsyncStorage.clear();
}
```

## Manejo de Errores de Red

### ErrorScreen

`app/shared/components/common/ErrorScreen.tsx`

Pantalla de error genérica con:

- Icono de alerta rojo (`Ionicons alert-circle`)
- Mensaje personalizable (por defecto "Algo salió mal. Por favor, inténtalo de nuevo.")
- Botón "Inicio" con efecto neon para reintentar

Usada en `DailyActivityScreen`:

```typescript
if (error) return <ErrorScreen message={error.message} />;
if (!data || !data.activity)
  return <ErrorScreen onRetry={() => { router.back() }}
    message={'Por favor configure una actividad para el día!'} />;
```

### Estado de Carga

```typescript
if (isLoading) return <ActivityIndicator size="large" />;
```

## Detección de Conectividad

La detección temprana de conectividad se maneja en múltiples capas:

### Axios Timeout

El cliente HTTP (`app/shared/services/api/api.ts`) tiene timeout de 10 segundos:

```typescript
const api = axios.create({
  baseURL: `${apiBaseUrl}`,
  timeout: 10000,
});
```

### Manejo en Hooks

Los hooks de React Query manejan errores de red automáticamente. `useDailyActivity()` y `useSubmitResponse()` propagan errores de Axios que `DailyActivityScreen` captura con `ErrorScreen`.

### AuthService

`app/shared/services/api/auth.ts` captura errores de conexión:

```typescript
try {
  const response = await api.post('/api/auth/validate', { email, password });
  // ...
} catch (error) {
  throw new Error('Credenciales incorrectas o error en la conexión.');
}
```

## Persistencia de Sesión

`app/shared/hooks/useAuth.ts`

```typescript
const checkAuth = async () => {
  const token = await SecureStore.getItemAsync('authToken');
  setIsAuthenticated(!!token);
  return !!token;
};
```

Aunque actualmente el token no se persiste (el checkAuth retorna siempre falso), la infraestructura está lista para almacenar y recuperar sesiones.

## Cache de React Query

QueryClient configurado en `app/_layout.tsx`:

```typescript
const queryClient = new QueryClient();
```

Las queries tienen `staleTime: 1000 * 60 * 5` (5 minutos), lo que permite operar con datos cacheados durante cortos períodos sin conexión.

El storage incluye métodos específicos para React Query:

```typescript
async getQueryCache(): Promise<any> { return this.getItem('queryCache'); }
async setQueryCache(value: any): Promise<void> { return this.setItem('queryCache', value); }
```

## Estrategia Offline (Futuro)

La arquitectura actual soporta:

1. **Cache local**: React Query mantiene datos frescos por 5 minutos
2. **Almacenamiento seguro**: SecureStore para tokens de sesión
3. **Persistencia general**: AsyncStorage para preferencias y datos no críticos
4. **Manejo de errores**: ErrorScreen como fallback visual
5. **Fallback de API**: Timeout de 10s con captura de excepciones

## Estructura de Archivos

```
app/
  shared/
    store/
      storage.ts          ← AsyncStorage + SecureStore
    components/
      common/
        ErrorScreen.tsx   ← Pantalla de error visual
    services/
      api/
        api.ts            ← Axios con timeout
        auth.ts           ← Auth con manejo de errores
    hooks/
      useAuth.ts          ← Sesión y checkAuth
  features/
    activity/
      hooks/
        activity.ts       ← React Query con staleTime
```

## Dependencias

| Paquete | Versión | Uso |
|---------|---------|-----|
| `@react-native-async-storage/async-storage` | ^1.23.1 | Almacenamiento local general |
| `expo-secure-store` | ^14.0.1 | Almacenamiento seguro para tokens |
| `@tanstack/react-query` | ^5.67.2 | Cache y estado de servidor |
| `axios` | ^1.7.9 | Cliente HTTP con timeout |
