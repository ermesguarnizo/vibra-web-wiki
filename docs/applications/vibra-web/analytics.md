---
title: Analytics
description: Dashboard de gráficas con Recharts — participación, emociones, progreso, ranking y cards de tendencia
---

# Analytics

## Descripción

Dashboard de análisis y monitoreo del ecosistema Vibra. Presenta 4 cards de tendencia (total actividades, participantes, registros emocionales, cursos activos) y 5 gráficas interactivas construidas con **Recharts**. Filtro global por curso y selector de período.

## Componentes

| Componente | Ruta | Propósito |
|---|---|---|
| `AnalyticsDashboard` | `components/analytics/analytics-dashboard.tsx` | Dashboard principal con layout de grid |
| `ActivitiesByMonthChart` | `components/analytics/activities-by-month-chart.tsx` | Gráfico de barras: actividades por mes |
| `EmotionsDistributionChart` | `components/analytics/emotions-distribution-chart.tsx` | Gráfico de pastel: distribución de emociones |
| `EmotionsEvolutionChart` | `components/analytics/emotions-evolution-chart.tsx` | Gráfico de líneas: evolución temporal |
| `CourseProgressChart` | `components/analytics/course-progress-chart.tsx` | Gráfico de barras horizontal: progreso por curso |
| `ParticipantRankingChart` | `components/analytics/participant-ranking-chart.tsx` | Ranking visual top 10 participantes |
| `CourseSelector` | `components/analytics/course-selector.tsx` | Dropdown con búsqueda para filtrar por curso |

### AnalyticsDashboard

Layout en grid con:

**Fila de filtros**: `CourseSelector` + selector de período (desde `FilterContext`).

**Cards de tendencia** (grid 4 columnas):

| Card | Fuente de datos |
|---|---|
| Total Actividades | `getCountAllActivities()` |
| Total Participantes | `getCountAllParticipants()` |
| Registros Emocionales | `getEmotionDistribution()` — suma de values |
| Cursos Activos | `getCourseProgress()` — length del array |

**Cuadrícula de gráficas** (2 columnas):

| Posición | Gráfica | Tipo Recharts |
|---|---|---|
| Fila 1, Col 1 | `ActivitiesByMonthChart` | `<BarChart>` |
| Fila 1, Col 2 | `EmotionsDistributionChart` | `<PieChart>` con donut (innerRadius=60, outerRadius=90) |
| Fila 2, Col 1 | `CourseProgressChart` | `<BarChart layout="vertical">` |
| Fila 2, Col 2 | `ParticipantRankingChart` | Lista rankeada (no Recharts) |
| Fila 3, Full | `EmotionsEvolutionChart` | `<LineChart>` (col-span-2) |

Cada gráfica tiene botón de expandir (ícono de expandir) que abre la gráfica en una tab independiente.

### ActivitiesByMonthChart

Gráfico de barras que muestra actividades completadas por mes. Datos desde `getActivitiesByMonth(year, courseId)`. Eje X con nombres de meses abreviados (Ene, Feb... Dic). Tooltip con valores. Barras color gris `#6b7280` con bordes redondeados `radius={[4,4,0,0]}`.

### EmotionsDistributionChart

Gráfico de donut con distribución de emociones registradas. Datos desde `getEmotionDistribution(startDate, endDate, courseId)`. 10 colores predefinidos. Labels con nombre y porcentaje. Incluye `<Legend>`.

### EmotionsEvolutionChart

Gráfico de líneas que muestra evolución de registros emocionales en el tiempo. Datos desde `getEmotionEvolution(days, startDate, endDate, courseId)`. Eje X formateado como `día/mes`. Tooltip con formato de fecha. Línea continua con dots en cada punto.

### CourseProgressChart

Gráfico de barras horizontal que muestra porcentaje de participantes que han completado al menos una actividad por curso. Datos desde `getCourseProgress()`. Eje X con dominio 0-100 y unidad `%`. Usa 5 colores progresivos (rojo → amarillo → verde → azul → púrpura).

### ParticipantRankingChart

Ranking visual del top 10 participantes (no usa gráfico Recharts, es lista HTML). Datos desde `GET /api/participants/leaderboard?limit=10`. Cada entrada muestra:

- Posición (#1 oro, #2 plata, #3 bronce, #4+ gris)
- Avatar (imagen o inicial)
- Nickname
- Badge de nivel con color (`bronce`, `plata`, `oro`, `platino`, `diamante`)
- Puntos totales

### CourseSelector

Dropdown con input de búsqueda para filtrar analytics por curso. Datos desde `getSimpleCourseList()`. Opción "Todos los cursos" por defecto. Cierra al hacer clic fuera.

## Filtros

| Filtro | Fuente | Uso |
|---|---|---|
| Curso | `CourseSelector` | courseId → todas las gráficas |
| Año | `FilterContext` (yearFilter) | ActivitiesByMonthChart |
| Fecha inicio | `FilterContext` (dateInitFilter) | EmotionsDistributionChart, EmotionsEvolutionChart |
| Fecha fin | `FilterContext` (dateEndFilter) | EmotionsDistributionChart, EmotionsEvolutionChart |

## Dependencias

- `recharts` — BarChart, PieChart, LineChart, ResponsiveContainer
- `@/registry/new-york/ui/card` — shadcn card wrapper
