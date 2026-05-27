---
title: Actividad Diaria
description: Flujo de actividad diaria, orquestación de tipos de actividad, barra de progreso, cálculo de puntaje y React Query
---

# Actividad Diaria

La pantalla principal de eventos diarios del participante. Orquesta secuencialmente 5 tipos de actividad configurados desde el backend.

## Arquitectura

La actividad diaria se compone de tres capas:

- **Screen**: `DailyActivityScreen` — `app/features/activity/screens/DailyActivityScreen.tsx`
- **Store**: Zustand `useActivityStore` — `app/shared/store/activity.store.ts`
- **Hooks**: React Query — `app/features/activity/hooks/activity.ts`

## Flujo de Actividad Diaria

`EmotionScreen` (`app/features/activity/screens/emotion.tsx`) es la entrada que renderiza `DailyActivityScreen`. Navegación desde el Tab "Eventos" vía `router.push('/features/activity/screens/emotion')`.

`DailyActivityScreen` ejecuta los siguientes pasos en orden, controlados por `activityType` del store:

```text
Question → WordSearch → MatchingConcepts → EmotionBox → DiceGame
```

Cada tipo se renderiza condicionalmente con `{activityType === 'Question' && ...}`. Al completar un tipo, el store avanza al siguiente mediante `actions.nextActivityType()`.

## Activity Store (Zustand)

`app/shared/store/activity.store.ts`

```typescript
type ActivityType = 'Question' | 'WordSearch' | 'MatchingConcepts' | 'EmotionBox' | 'DiceGame';
```

Estado manejado:
- `currentStep`: paso actual dentro de la actividad tipo Question
- `responses`: arreglo de respuestas acumuladas
- `activityType`: tipo de actividad activo
- `actions.nextActivityType()`: avanza en la secuencia Question → WordSearch → MatchingConcepts → EmotionBox → DiceGame
- `actions.nextStep()`: avanza al siguiente recurso/pregunta
- `actions.addResponse()`: agrega respuesta evitando duplicados por `questionId`

## React Query — Hooks

`app/features/activity/hooks/activity.ts`

### `useDailyActivity()`

Obtiene la actividad configurada para el día vía `GET /api/activities/daily/current`. Stale time de 5 minutos. Retorna datos de tipo `ActivityResponse` con la estructura `{ activity, schedule }`.

### `useSubmitResponse()`

Mutation vía `POST /api/activities/:activityId/:userId/submit`. Invalida las queries `['daily-activity']` y `['rankings']` al completar.

### `useActivities(page)`

Obtiene historial paginado de actividades con `GET /api/activities?page=&limit=10`.

## Barra de Progreso

`app/shared/components/ui/ProgressBarNew.tsx`

```
<ProgressBarII total={data.activity?.resources?.length - 1} current={currentStep} />
```

Muestra "Pregunta X de Y". La barra se renderiza en el header del bloque Question.

## ScoreTracker

`app/features/activity/components/ScoreCounter.tsx`

Componente animado con:
- Contador numérico con animación de escala (`Animated.sequence` + `Easing.out(Easing.exp)`)
- Barra de progreso horizontal con `Animated.Value`
- Formato de dos decimales en el puntaje
- Props: `currentScore` y `maxScore`

Cálculo de puntaje en `app/shared/utils/score-utils.ts`:

```typescript
const calculateScore = (responses: Response[]): number => {
  const BASE_MULTIPLIER = 100;
  const TIME_PENALTY_PER_SECOND = 0.1;
  const COMPLETENESS_BONUS = 50;
  // baseScore - timePenalty + completenessBonus
};
const calculateMaxScore = (questionsCount: number): number => questionsCount * 100 + 50;
```

## Tipos de Actividad

### Question (`QuestionSection.tsx`)

Renderiza preguntas de tipo `open` (TextInput multilinea) o `multiple` (botones de opción). Al enviar, llama `actions.addResponse()` con `{ questionId, isCorrect, points, responseTime }` y luego `onSubmit` que ejecuta `useSubmitResponse`. Si es la última pregunta (`isLastQuestion`), muestra modal de resultados con puntaje y tiempo.

### WordSearch (`WordSearchGame.tsx`)

Sopa de letras con cuadrícula de tamaño configurable. El juego:
- Coloca palabras en la grilla con direcciones aleatorias (8 direcciones)
- Llena espacios vacíos con letras aleatorias
- Permite seleccionar celdas consecutivas para formar palabras
- Colorea palabras encontradas con 6 colores distintos
- Puntaje: `longitud_palabra * 10`, bonus de 100 si encuentra todas
- Al completar, llama `actions.nextActivityType()` mediante Alert

### MatchingConcepts (`MatchingConceptsGame.tsx`)

Empareja conceptos con definiciones. Implementa:
- Algoritmo Fisher-Yates para mezclar ítems
- Selección visual con animación de escala y borde dorado
- Match visual con icono de check verde al acertar
- Animación de shake al fallar
- Puntaje: 50 puntos por par, bonus de tiempo, bonus de 100 si completa todos
- Notificación de finalización vía `Alert.alert`

### EmotionBox (`EmotionBoxScreen.tsx` → `EmotionBoxActivity.tsx`)

Clasifica emociones arrastrando (PanResponder) a "Emociones Sanas" o "Emociones por Gestionar". Incluye:
- Configuración de emociones y tiempo límite
- Detección de colisión con `onLayout`
- +10 puntos por colocación correcta, -5 por incorrecta
- Overlay de resultados animado al completar
- Tipos definidos en `app/features/activity/types/emotion-box.ts`

### DiceGame (`DiceGameScreen.tsx` → `DiceGameActivity.tsx`)

Juego de dados educativo. Fases: `ready → rolling → question → result`. Animaciones de giro (spin), rebote (spring) y escala. Preguntas asociadas a cada valor del dado (1-6). Tipos `open` y `multiple`. Puntaje basado en el valor del dado.

## Botones Continuar / Finalizar

Entre cada tipo de actividad, se muestra un bloque de transición con `CustomButton`:

```tsx
<CustomButton title="Continuar" variantColor="blue" neonEffect={true}
  onPress={() => actions.nextActivityType()} icon="arrow-forward" />
```

En el último tipo (DiceGame), el botón cambia a "Finalizar Actividades" con variante verde, que ejecuta `actions.reset()` y redirige al tab principal.

## API

Endpoint base: `config/env.json` → `development.apiBaseUrl`

Cliente Axios configurado con timeout de 10s en `app/shared/services/api/api.ts`.
