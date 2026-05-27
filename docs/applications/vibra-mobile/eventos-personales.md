---
title: Eventos Personales
description: Tab de eventos personales del participante con calendario y seguimiento
---

# Eventos Personales

El Tab "E-Personal" corresponde al tercer tab de la navegación inferior (`app/features/(tabs)/three.tsx`). Proporciona un calendario para que el participante gestione y visualice sus eventos personales.

## Arquitectura

- **Screen**: `TabThree` — `app/features/(tabs)/three.tsx`
- **Componente principal**: `CalendarComponent`
- **Navegación**: `expo-router` con tabs

## Vista Actual

```tsx
<View style={{ flex: 1, padding: 20 }}>
  <Text>Bienvenido al Dashboard</Text>
  <Text>Datos mockeados: {mockDashboardData.tabOne}</Text>
  <CalendarComponent />
</View>
```

## CalendarComponent

`app/shared/components/ui/CalendarComponent.tsx`

Calendario interactivo usando `react-native-calendars` con las siguientes características:

### Configuración Regional

Configurado completamente en español usando `LocaleConfig`:

```typescript
LocaleConfig.locales['es'] = {
  monthNames: ['Enero', 'Febrero', ... , 'Diciembre'],
  dayNames: ['Domingo', 'Lunes', ... , 'Sábado'],
  dayNamesShort: ['Dom', 'Lun', 'Mar', 'Mié', 'Jue', 'Vie', 'Sáb'],
  today: 'Hoy',
};
LocaleConfig.defaultLocale = 'es';
```

### Funcionalidad

- Selección de fecha con marcador visual azul (`selectedColor: '#3B82F6'`)
- Tema personalizado con fuentes Inter
- Texto informativo que muestra la fecha seleccionada en un contenedor azul claro
- Estilos shadow y border radius

### Manejo de Estado

```typescript
const [selectedDate, setSelectedDate] = useState<string | null>(null);

const handleDayPress = (day: { dateString: string }) => {
  setSelectedDate(day.dateString);
};
```

## Diseño del Tab

- Fondo con padding de 20px
- Header con texto de bienvenida
- Calendario centrado verticalmente

## API de Eventos Personales

El sistema está diseñado para consumir eventos desde el backend NestJS a través de la instancia Axios compartida. Los endpoints esperados incluyen:

| Método | Endpoint | Descripción |
|--------|----------|-------------|
| GET | `/api/events/personal` | Lista eventos del participante |
| POST | `/api/events/personal` | Crear nuevo evento personal |
| PUT | `/api/events/personal/:id` | Actualizar evento |
| DELETE | `/api/events/personal/:id` | Eliminar evento |

## Tipo de Evento

`app/features/activity/types/events.ts`:

```typescript
export interface EventVibra {
  id: number;
  title: string;
  date: string;
  location: string;
}
```

## Integración con Store

El estado del tab se maneja con React Context (`UserContext`) para datos del usuario y con Zustand para preferencias de visualización. La información del usuario se obtiene de `useUser()`:

```typescript
const { user } = useUser(); // UserContext
```

## Mock Data

Durante desarrollo, el tab utiliza datos mockeados de `app/shared/utils/mock-data.ts`.

## Flujo de Creación y Seguimiento

1. El participante selecciona una fecha en el calendario
2. El sistema muestra los eventos registrados para esa fecha
3. El participante puede crear nuevos eventos (funcionalidad en desarrollo)
4. Los eventos se sincronizan con el backend vía Axios

## Pendiente

La funcionalidad completa de creación y seguimiento de eventos personales (filtro `type=evento_personal`) está en desarrollo. Actualmente el tab sirve como visualizador de calendario con selección de fechas.
