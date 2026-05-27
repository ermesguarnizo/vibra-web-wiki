---
title: Retos
description: Tab de retos/desafíos grupales, ranking de usuarios y leaderboard con Socket.io
---

# Retos

El Tab "Retos" corresponde al segundo tab de la navegación inferior (`app/features/(tabs)/two.tsx`). Actualmente muestra el ranking de usuarios y datos de prueba.

## Arquitectura

- **Screen**: `TabTwo` — `app/features/(tabs)/two.tsx`
- **Componentes**: `UserRankingList`, `BlogCard`, `ActivityHistoryList`
- **WebSockets**: Socket.io para ranking en tiempo real

## Vista Actual

El tab renderiza:

```tsx
<View style={{ flex: 1, padding: 20 }}>
  <Text>Bienvenido al Dashboard</Text>
  <UserRankingList />
</View>
```

Incluye datos mock del archivo `app/shared/utils/mock-data.ts`.

## UserRankingList

`app/features/users/UserRankingList.tsx`

Componente que lista participantes con las siguientes características:

- **Carga de datos**: Petición `GET /users/all` vía Axios
- **Lista**: Cada usuario se muestra con `username`, curso e institución educativa
- **Avatar**: Imagen por defecto desde `assets/avatars/03.jpg`
- **Modal**: Al presionar un usuario, se abre un modal (`react-native-modal`) con:
  - Nombre completo
  - Avatar
  - Ranking (número fijo con estrellas)
  - Tipo y número de documento
  - Email
  - Rol
  - Estado de sesión activa
  - Curso e institución

### WebSockets en UserRankingList

Conecta con Socket.io (`socket.ts`) para recibir eventos en tiempo real:

```typescript
socket.on('customEvent', (data) => {
  setMessage(data.message);
  Alert.alert('Evento Recibido', data.message);
});
socket.emit('clientEvent', 'Hola desde el cliente');
```

## Conexión Socket.io

`app/socket.ts`

```typescript
import { io } from 'socket.io-client';
const socket = io(`${apiBaseUrl}`, { transports: ['websocket'] });
```

Configuración en `config/env.json`.

## Datos Mockeados

`app/shared/utils/mock-data.ts`:

```typescript
export const mockDashboardData = {
  tabOne: 'Eventos',
  tabTwo: 'Retos',
  tabThree: 'E-Personales'
};
```

## Ranking en Tiempo Real (useRanking)

`app/shared/hooks/useRanking.ts`

Hook que conecta a `Socket.io` en `http://localhost:4000/rankings` y escucha:

- `rankingsUpdate`: actualización de la lista de rankings
- `historicalRankings`: datos históricos
- Emite `joinRankingRoom` al conectarse

## Tipos de Ranking

`app/shared/types/ranking.ts`:

```typescript
interface RankingEntry {
  userId: string;
  score: number;
  username?: string;
  avatar?: string;
  position?: number;
  lastActivity?: Date;
}

interface HistoricalRanking {
  date: Date;
  rankings: RankingEntry[];
}
```

## Integración con API

Los retos consumen datos del mismo backend NestJS que la actividad diaria, utilizando la instancia Axios compartida con timeout de 10 segundos.

### Endpoints relevantes

| Método | Endpoint | Uso |
|--------|----------|-----|
| GET | `/users/all` | Lista todos los usuarios participantes |
| WS | `/rankings` | Actualizaciones de ranking en vivo |

## Pendiente

La funcionalidad de retos grupales (desafíos colaborativos, competencias por equipo, filtro `type=reto`) está en planificación. Actualmente el tab sirve como leaderboard de participantes.
