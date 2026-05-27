---
title: Perfil
description: Perfil del participante, progreso, nivel, racha, leaderboard y resumen de actividad
---

# Perfil

El perfil del participante se muestra en el Tab "Eventos" (`app/features/(tabs)/one.tsx`) y en componentes distribuidos. Incluye progreso tipo batería, racha de actividades, leaderboard en tiempo real y resumen de actividad completada.

## Arquitectura

Los componentes de perfil se distribuyen en:

- **`CardComponent`**: Saludo, fecha actual y barra de progreso
- **`ProgressBarVibra`**: Barra tipo batería con nivel de energía
- **`UserRankingList`**: Leaderboard con modal de detalle
- **`ActivityHistoryList`**: Historial de actividades del participante
- **`ScoreCounter`**: Puntaje animado con barra de progreso

## CardComponent

`app/shared/components/ui/CardComponent.tsx`

Componente que muestra la información principal del perfil:

```tsx
<Text>Hola {user.username}</Text>
<Text>¿Que tal tu dia! Enseñanos tus emociones...</Text>
<ProgressBarVibra />
```

Incluye `useCurrentDate()` para mostrar la fecha actual formateada.

## ProgressBarVibra (Nivel de Vibra)

`app/shared/components/ui/ProgressBar.tsx`

Barra de progreso con diseño tipo batería:

- Contenedor rectangular con "cabeza" de batería
- Carga animada con interpolación (`Animated.sequence` + `Animated.timing`)
- Nivel fijo en 70% con animación pulsante que fluctúa entre 70% y 95%
- Código de colores según nivel:
  - Rojo (`#FF4444`): ≤ 20%
  - Amarillo (`#FFBB33`): ≤ 50%
  - Verde (`#4CAF50`): > 50%

```tsx
<Animated.View style={[styles.batteryCharge, { width: animatedWidth, backgroundColor: getChargeColor(chargeLevel) }]} />
```

## ActivityHistoryList (Historial)

`app/features/activity/screens/ActivityHistoryList.tsx`

Lista paginada con infinite scroll:

- FlatList con `onEndReached` y `fetchNextPage`
- Cada item muestra: nombre de emoción (`EmotionBadge`), fecha, título, número de recursos y preguntas
- SearchInput para filtrar actividades por título
- Estados: loading (`ActivityIndicator`), error (texto rojo), empty

Datos obtenidos con `useActivities(page)` de React Query.

## UserRankingList (Leaderboard)

`app/features/users/UserRankingList.tsx`

Lista de participantes con modal de detalle:

- Petición `GET /users/all`
- Cada usuario como item touchable
- Modal con: nombre, avatar, rank numérico, estrellas (3/5), documento, email, rol, curso
- Socket.io para eventos en tiempo real (`customEvent`)

## Resumen de Actividad Completada

Cuando el participante completa una actividad, se muestra un modal con:

- **ScoreCounter**: Puntaje numérico animado con barra de progreso (`currentScore / maxScore`)
- **Modal interno**: Overlay con icono de check verde, "¡Actividad Completada!", puntaje, tiempo y respuestas correctas
- Al finalizar todas las actividades del día: botón "Finalizar Actividades" que redirige al home

```tsx
{isCompleted && (
  <Animated.View style={[styles.resultsContainer, { opacity: successAnim }]}>
    <MaterialIcons name="check-circle" size={64} color="#10B981" />
    <Text>¡Actividad Completada!</Text>
    <Text>Puntuación: {score}</Text>
    <Text>Tiempo: {formatTime(timeSpent)}</Text>
  </Animated.View>
)}
```

## ScoreSummaryBanner

Componente presente en varias pantallas como resumen visual del rendimiento. Se compone de:

- `ScoreCounter`: número animado + barra de progreso
- `EmotionBadge`: badge con color e icono según la emoción

## Estado Global

El perfil consume datos de:

- **UserContext** (`app/context/UserContext.tsx`): estado global del usuario logueado
- **ActivityStore** (`app/shared/store/activity.store.ts`): progreso de la actividad actual
- **React Query**: datos de actividades y rankings cacheados

## Actualizaciones en Tiempo Real

El ranking se actualiza mediante Socket.io:
- Servicio: `app/shared/hooks/useRanking.ts`
- Eventos: `rankingsUpdate`, `historicalRankings`
- Sala: `joinRankingRoom`

## Métricas del Perfil

| Métrica | Componente | Fuente |
|---------|-----------|--------|
| Nivel | ProgressBarVibra (battery) | Mock (70%) |
| Racha | Contador de días consecutivos | API (en desarrollo) |
| Puntaje | ScoreCounter | score-utils.ts |
| Ranking | UserRankingList | GET /users/all |
| Historial | ActivityHistoryList | GET /api/activities |
| Actividades/día | DailyActivityScreen | GET /api/activities/daily/current |
