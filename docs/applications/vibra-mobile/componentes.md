---
title: Componentes Mobile
description: Catálogo de componentes reutilizables de la aplicación móvil Vibra
---

# Componentes Mobile

Catálogo de componentes reutilizables de la UI de Vibra Mobile. Organizados por tipo y funcionalidad.

## EmotionalAssistant

No implementado como componente individual. La asistencia emocional se maneja mediante `EmotionBadge` y las pantallas de actividad. Ver:

- `EmotionBadge` — badge visual por emoción
- `EmotionBoxActivity` — actividad interactiva de clasificación emocional

## PeekingBuddy

No implementado como componente individual. Las animaciones de asistencia se manejan mediante:

- `Animated.View` con interpolaciones en `ScoreCounter` (escala pulsante)
- `boxPulseAnim` en `EmotionBoxActivity` (pulso en cajas de emociones)

## ScoreTracker

Ubicado en dos partes:

### ScoreCounter (Componente Visual)

`app/features/activity/components/ScoreCounter.tsx`

```tsx
interface ScoreCounterProps {
  currentScore: number;
  maxScore?: number;
}
```

Características:

- Contador numérico animado con `Animated.timing` + `Easing.out(Easing.exp)` (800ms)
- Escala pulsante al cambiar puntaje (1 → 1.2 → 1)
- Barra de progreso horizontal animada
- Formato de dos decimales
- Listener en `animatedValue` para actualización reactiva del texto

### calculateScore / calculateMaxScore (Utils)

`app/shared/utils/score-utils.ts`

```typescript
calculateScore(responses): number
  // BASE_MULTIPLIER(100) * respuestas correctas - TIME_PENALTY(0.1/s) + COMPLETENESS_BONUS(50)

calculateMaxScore(questionsCount): number
  // questionsCount * 100 + 50
```

### ScoreSummaryBanner

Composición de `ScoreCounter` + `EmotionBadge` presente en:

- `DailyActivityScreen`: sección scoreContainer debajo de QuestionSection
- `WordSearchGame`: ScoreCounter en header junto al timer
- `MatchingConceptsGame`: ScoreCounter en header
- `QuestionSection`: modal de resultados al completar preguntas

## ActivityCard

No existe como componente único. Equivalente funcional:

### ActivityHistoryList (Historial)

`app/features/activity/screens/ActivityHistoryList.tsx`

Renderiza actividades en `FlatList` con:

- SearchInput para filtro por título
- EmotionBadge con nombre de emoción
- Fecha formateada
- Título de la actividad
- Conteo de recursos y preguntas
- Infinite scroll con `onEndReached`

### CardComponent (Resumen del Día)

`app/shared/components/ui/CardComponent.tsx`

```tsx
<Text>Hola {user.username}</Text>
<Text>¿Que tal tu dia! Enseñanos tus emociones...</Text>
<ProgressBarVibra />
```

### CardSlider (Slider Promocional)

`app/shared/components/ui/CardSlider.tsx`

Slider horizontal con:

- Auto-play configurable (default: 5s)
- Indicadores de página
- Botones de navegación izquierda/derecha
- Tarjetas con título, descripción, botón CTA
- Datos desde `sliderService.getSliderData()`
- Animación de pulso en tarjeta activa

## CustomButton

`app/shared/components/ui/CustomButton.tsx`

Botón versátil con las siguientes características:

### Props

```typescript
interface CustomButtonProps {
  title: string;
  variantColor?: 'blue' | 'red' | 'green' | 'purple' | 'orange' | 'yellow' | 'gray';
  onPress: () => void;
  neonEffect?: boolean;
  icon?: string; // MaterialIcons name
  iconPosition?: 'left' | 'right';
  iconSize?: number;
  disabled?: boolean;
  buttonType?: 'standard' | 'iconTop';
  fullWidth?: boolean;
}
```

### Variantes

- **Standard**: Botón con icono a la izquierda o derecha
- **iconTop**: Icono grande arriba, texto abajo (usado en tabs)
- **Neon**: Efecto de gradiente + animación de pulso (escala 1 → 1.2)
- **Disabled**: Opacidad reducida (0.6)

### Colores por Variante

```typescript
'blue':   ['#0066FF', '#00CCFF']
'red':    ['#FF0000', '#FF6666']
'green':  ['#00CC00', '#66FF66']
'purple': ['#6600CC', '#CC66FF']
'orange': ['#FF6600', '#FFCC00']
'yellow': ['#FFCC00', '#FFFF66']
'gray':   ['#666666', '#CCCCCC']
```

Neon usa `LinearGradient` de `expo-linear-gradient` con animación de bucle infinita.

## ErrorScreen

`app/shared/components/common/ErrorScreen.tsx`

```tsx
interface ErrorScreenProps {
  message?: string;
  onRetry?: () => void;
}
```

Renderiza:

- Icono `Ionicons alert-circle` (#EF4444) en círculo rojo claro
- Título "¡Algo salio mal!"
- Mensaje de error personalizable
- Botón "Inicio" con efecto neon (solo si hay `onRetry`)

## Estados de Carga

### ActivityIndicator (React Native)

Usado consistentemente en toda la app:

```typescript
if (isLoading) return <ActivityIndicator size="large" />;
```

Presente en:
- `DailyActivityScreen`
- `ActivityHistoryList`
- `UserRankingList`

### Skeleton/Loading States

No implementado actualmente. El estado de carga se maneja con `ActivityIndicator` nativo.

## Estados Vacíos

### Empty State en Listas

- `ActivityHistoryList`: lista vacía no renderiza nada (FlatList sin datos)
- `DiceGameScreen`: estado empty con icono y botón de configuración
- `UserRankingList`: no implementa estado vacío

## Otros Componentes UI

### ProgressBarII

`app/shared/components/ui/ProgressBarNew.tsx`

Barra de progreso para el flujo de preguntas:

```tsx
<ProgressBarII total={resources - 1} current={currentStep} />
// Muestra: "Pregunta X de Y"
```

### ProgressBarVibra (Battery)

`app/shared/components/ui/ProgressBar.tsx`

Barra tipo batería con nivel de energía animado y código de colores.

### EmotionBadge

`app/features/activity/components/EmotionBadge.tsx`

```tsx
interface EmotionBadgeProps {
  emotion: string;
  size?: 'small' | 'medium';
}
```

Mapa de emociones con color e icono (Ionicons):

| Emoción | Color | Icono |
|---------|-------|-------|
| alegría | #FBBF24 | happy |
| tristeza | #60A5FA | sad |
| enojo | #F87171 | flame |
| tranquilidad | #34D399 | leaf |
| ansiedad | #A78BFA | alert |
| excitación | #FB7185 | flash |
| agradecimiento | #FBBF24 | heart |

### SearchInput

`app/shared/components/ui/SearchInput.tsx`

Input de búsqueda para filtrar listas de actividades.

### CalendarComponent

`app/shared/components/ui/CalendarComponent.tsx`

Calendario con `react-native-calendars` configurado en español.

### MediaPlayer

`app/shared/components/media/MediaPlayer.tsx`
`app/shared/components/media/ReproductorMedia.tsx`
`app/shared/components/media/VideoScreen.tsx`

Reproductor multimedia para recursos de actividad (video/audio).

### RankingScreen

`app/shared/components/ui/RankingScreen.tsx`

Pantalla de ranking visual.

### DraggableBox

`app/shared/components/ui/DraggableBox.tsx`

Componente arrastrable (utilizado internamente en EmotionBoxActivity).

### FloatButton

`app/shared/components/ui/animation/FloatButton.tsx`

Botón flotante animado.

## Mapa de Archivos

```
app/shared/components/
  common/
    ErrorScreen.tsx
  media/
    MediaPlayer.tsx
    ReproductorMedia.tsx
    UploadFile.tsx
    VideoScreen.tsx
  storage/
    LocalStorageComponent.tsx
  ui/
    BlogCard.tsx
    CalendarComponent.tsx
    CardComponent.tsx
    CardSlider.tsx
    CustomButton.tsx
    DraggableBox.tsx
    FlatList.tsx
    ProgressBar.tsx (battery)
    ProgressBarNew.tsx (preguntas)
    RankingScreen.tsx
    SearchInput.tsx
    index.ts
    animation/
      FloatButton.tsx
      GestureAnimate.tsx
      GestureElasticMenu.tsx
      GesturePan.tsx
      WelcomeScreen.tsx

app/features/activity/components/
  DiceGameActivity.tsx
  DiceGameConfig.tsx
  EmotionBadge.tsx
  EmotionBoxActivity.tsx
  MatchingConceptsGame.tsx
  QuestionSection.tsx
  ScoreCounter.tsx
  WordSearchGame.tsx
```
