---
id: desarrollo-frontend-web
title: Desarrollo del Frontend Web (Vibra Web)
sidebar_label: Desarrollo Frontend Web
---

# Informe de actividades desarrolladas en las semanas 7, 8, 9 y 10 en el proyecto Vibra para el desarrollo del Frontend Web

Este documento detalla el estado actual de las tareas de desarrollo del proyecto **vibra-web**, cubriendo la arquitectura implementada, el desglose técnico de los módulos desarrollados y la evidencia de las vistas y flujos construidos, en cumplimiento de la Fase 3 del plan de desarrollo (Semanas 7–10).

---

## 1. Arquitectura Técnica Implementada

El proyecto **vibra-web** se ha construido sobre una base moderna y escalable, siguiendo las mejores prácticas de desarrollo con **Next.js 14** y **React 18**.

### Tecnologías Base

| Categoría | Tecnología | Versión |
| :--- | :--- | :--- |
| Framework | Next.js | 14.2.3 |
| Lenguaje | TypeScript | 5.x |
| UI Base | Radix UI + Tailwind CSS | 3.4.1 |
| Estado Global | Jotai (atomic state) | 2.10.4 |
| HTTP Client | Axios | 1.6.8 |
| Tiempo Real | Socket.io-client | 4.8.0 |
| Formularios | React Hook Form | 7.51.4 |
| Gráficas | Recharts + Chart.js | 2.12.7 |
| Animaciones | Framer Motion | 11.2.10 |
| Notificaciones | Sonner (toast) | 1.2.3 |
| Fechas | Moment.js + React DatePicker | 2.30.1 |
| Exportación | XLSX + jsPDF | — |
| i18n | react-i18next | 15.5.1 |
| Logging | Pino + Pino-pretty | 9.1.0 |
| Autenticación | JWT (@nestjs/jwt) + bcrypt | — |

### Patrones y Estructura

Se ha implementado una arquitectura híbrida con **App Router** (Next.js 14) para el layout raíz y **Pages Router** para las vistas de negocio, separando claramente las responsabilidades:

```
src/
├── api/          (23 módulos de integración con la API)
├── components/   (50+ componentes React organizados por dominio)
├── config/       (Configuración de entornos y logging)
├── models/       (Entidades TypeScript)
├── pages/        (Rutas y vistas de negocio)
├── registry/     (Librería de componentes Radix UI)
├── services/     (Contextos, Auth Provider, servicios)
└── utils/        (Constantes, helpers, enums)
```

### Configuraciones Globales Establecidas

- **Entornos:** Variables de entorno separadas por `.env`, `.env.development` y `.env.production`.
- **Logging:** Logger con `Pino` y `Pino-pretty` para desarrollo y producción.
- **Calidad de código:** ESLint + Prettier con plugins para Tailwind CSS y reglas de Next.js.
- **Exportación estática:** Configurado `output: 'export'` en `next.config.mjs` para despliegue en CDN.
- **Optimización de imágenes:** Patrones remotos configurados para avatares de GitHub.

---

## 2. Configuración del Proyecto Next.js (Semana 7)

### Estructura de Rutas y Navegación

Se ha implementado un sistema de navegación de doble capa:

**App Router (Next.js 14):**
- `app/layout.tsx` — Layout raíz con metadatos globales y fuente Inter de Google.
- `app/page.tsx` — Página de entrada con enlace al login.

**Pages Router (vistas de negocio):**

| Ruta | Descripción |
| :--- | :--- |
| `/forms/login/login-form` | Formulario de autenticación |
| `/home-dashboard` | Dashboard principal |
| `/activity-dashboard` | Dashboard de actividades |
| `/notification-dashboard` | Dashboard de notificaciones |
| `/config-dashboard` | Dashboard de configuraciones |
| `/preTest-dashboard` | Dashboard de pre-tests |
| `/users/user` | Gestión de usuarios |
| `/company/company` | Gestión de empresas |
| `/client/client` | Gestión de participantes |
| `/role/role` | Gestión de roles |
| `/permission/permission` | Gestión de permisos |
| `/permission-template/permission-template` | Plantillas de permisos |
| `/activity/activity` | Gestión de actividades |
| `/emotion/emotion` | Gestión de emociones |
| `/config/config` | Gestión de configuraciones |
| `/preTest/preTest` | Gestión de pre-tests |
| `/reports/dashboard-reports` | Reportes y estadísticas |
| `/profile` | Perfil de usuario |
| `/settings` | Configuración de la aplicación |
| `/about` | Información de la aplicación |

### Theming y Layout Base

- Soporte de tema claro/oscuro implementado con `next-themes`.
- Layout principal con sidebar colapsable, área de contenido central y sidebar derecho para usuarios activos.
- Diseño responsivo con detección de dispositivo (`DeviceContext`) para adaptar la UI en móvil, tablet y escritorio.
- Breadcrumb de navegación contextual implementado.

---

## 3. Diseño de Componentes Reutilizables (Semana 7–8)

### Librería de Componentes UI (Radix UI)

Se ha construido una librería interna de componentes en `src/registry/new-york/ui/` basada en Radix UI, cubriendo:

- Primitivos de formulario: `Button`, `Input`, `Label`, `Checkbox`, `Radio Group`, `Select`, `Switch`, `Slider`
- Contenedores: `Card`, `Dialog`, `Sheet`, `Popover`, `Accordion`, `Collapsible`
- Navegación: `Tabs`, `Navigation Menu`, `Breadcrumb`, `Dropdown Menu`, `Context Menu`
- Feedback: `Toast`, `Alert Dialog`, `Progress`, `Skeleton`, `Badge`
- Datos: `Table`, `Separator`, `Scroll Area`
- Utilidades: `Avatar`, `Tooltip`, `Hover Card`, `Toggle`, `Toggle Group`

### Componentes de Formulario Especializados

Ubicados en `src/components/forms/`:

| Componente | Descripción |
| :--- | :--- |
| `checkbox.tsx` | Checkbox con estado controlado |
| `select.tsx` | Select con estilos personalizados |
| `toggleSwitch.tsx` | Toggle switch animado |
| `currency-input.tsx` | Input con formato de moneda |
| `phoneNumberMask.tsx` | Input con máscara de teléfono |
| `sortable-list.tsx` | Lista con reordenamiento drag & drop |
| `user.tsx` | Formulario de usuario completo |

### Componentes de Layout

Ubicados en `src/components/layouts/`:

| Componente | Descripción |
| :--- | :--- |
| `sidebar/` | Sidebar colapsable con menú jerárquico y control de permisos |
| `footer/` | Footer con información de la aplicación |
| `breadcrumb/` | Breadcrumb de navegación contextual |
| `modal/` | Diálogos modales reutilizables |
| `loading/` | Estados de carga (skeletons y spinners) |
| `menu/` | Componentes de menú principal |
| `datePicker/` | Selector de rango de fechas |
| `download/` | Descarga de datos en CSV |
| `translate/` | Selector de idioma (i18n) |
| `tour/` | Tour de onboarding para nuevos usuarios |

---

## 4. Rutas, Navegación e Integración con la API (Semana 8–9)

### Sistema de Navegación con Tabs Dinámicos

Se implementó un sistema de navegación por pestañas (`TabsContext`) que permite abrir múltiples módulos simultáneamente sin recargar la página:

- `TabbedNavigation` — Componente de pestañas con apertura, cierre y activación dinámica.
- `DynamicTabs` — Variante con soporte para contenido dinámico por pestaña.
- `MainNav` — Barra de navegación superior con menús desplegables (Radix Navigation Menu) y accesos rápidos a Notificaciones e Informes.

### Integración con la API Backend (23 módulos)

Todos los módulos de integración en `src/api/` consumen la API REST de `vibra-api` usando Axios con configuración basada en entorno:

| Módulo API | Endpoints consumidos |
| :--- | :--- |
| `auth-login.ts` | `POST /api/auth/login`, `POST /api/auth/logout`, verificación JWT |
| `user.ts` | CRUD usuarios, búsqueda, paginación, conteo |
| `activity.ts` | CRUD actividades, asignación de usuarios, cambio de estado |
| `notification.ts` | Crear, listar, marcar como leída, conteo por día |
| `participant.ts` | CRUD participantes |
| `company.ts` | Gestión de empresas, empresa principal |
| `role.ts` | Gestión de roles |
| `permission.ts` | Gestión de permisos por usuario |
| `permissionTemplate.ts` | Plantillas de permisos |
| `emotion.ts` | Registro y listado de emociones |
| `config.ts` | Feature flags, control de acceso por configuración |
| `log.ts` | Logs de auditoría |
| `reports.ts` | Generación y consulta de reportes |
| `feedback.ts` | Retroalimentación de usuarios |
| `file.ts` | Carga y descarga de archivos |
| `documentType.ts` | Tipos de documento |
| `policy.ts` | Políticas de privacidad |
| `preTest.ts` | Pre-tests |
| `translate.ts` | Traducciones almacenadas en BD |
| `admin.ts` | Herramientas administrativas |
| `general.ts` | Búsqueda genérica |
| `service.ts` | Servicios generales |
| `notification.ts` | Notificaciones con conteo diario |

### Protección de Rutas y Control de Acceso

- `AuthProvider` carga permisos del usuario al iniciar sesión y los expone vía `AuthContext`.
- El `Sidebar` filtra los ítems del menú según los permisos del usuario autenticado.
- Feature flags controlados desde la API (`config.ts`) permiten habilitar/deshabilitar módulos en tiempo de ejecución.
- Rutas protegidas redirigen al login si no hay token válido en `localStorage`.

---

## 5. Gestión de Estado Global (Semana 8–9)

### Contextos React (Context API)

| Contexto | Responsabilidad |
| :--- | :--- |
| `AuthContext` | Token JWT, OTP, usuario, permisos, empresa principal |
| `FilterContext` | Filtros globales: rango de fechas, año, participante, estado |
| `DeviceContext` | Detección de dispositivo (móvil, tablet, escritorio) |
| `TabsContext` | Gestión de pestañas abiertas y pestaña activa |

### Almacenamiento Seguro

- `safe-token-storage.ts` — Utilidad SSR-safe para acceso a `localStorage`, evitando errores en renderizado del servidor.
- Persistencia de: token JWT, OTP, datos de usuario, estado de expansión del sidebar.

### Estado Atómico con Jotai

- Jotai utilizado para estado local de componentes complejos, evitando prop drilling.

---

## 6. Optimización de Rendimiento (Semana 9–10)

### Estrategias Implementadas

| Técnica | Implementación |
| :--- | :--- |
| Code Splitting | Automático por Next.js (por ruta y componente) |
| Memoización | `useMemo` en `AuthProvider` para el valor del contexto |
| Renderizado condicional | Componentes renderizados solo cuando el usuario está autenticado |
| Lazy loading de imágenes | `react-lazy-load-image-component` para avatares y recursos visuales |
| Exportación estática | `output: 'export'` para distribución en CDN |
| CSS optimizado | Tailwind CSS con purge automático de clases no usadas |
| Logging eficiente | Pino logger con niveles diferenciados por entorno |

### Accesibilidad (a11y)

- Componentes Radix UI con soporte nativo de ARIA y navegación por teclado.
- Tooltips descriptivos en acciones de la interfaz.
- Contraste de colores gestionado por el sistema de theming de Tailwind.

---

## 7. Desglose de Módulos de Interfaz Desarrollados

### 7.1. Módulo de Autenticación

**Flujo implementado:**
1. Formulario de login (`/forms/login/login-form`) con validación de credenciales.
2. Verificación OTP (`validate-otp.tsx`) con input de 6 dígitos y auto-foco.
3. Decodificación y validación del JWT recibido.
4. Carga de permisos y empresa principal en el `AuthProvider`.
5. Redirección al dashboard principal.

**Componentes:**
- `src/components/login/validate-otp.tsx` — Validación de segundo factor.
- `src/components/login/auth-controller.ts` — Controlador del flujo de autenticación.
- `src/services/auth-provider.tsx` — Proveedor de contexto de autenticación.

### 7.2. Dashboard Principal

**Componentes:**
- `general-dashboard.tsx` — Wrapper principal del dashboard con layout de sidebar + contenido.
- `home-dashboard.tsx` — Vista de inicio con métricas generales.
- `general-dashboard/overview.tsx` — Resumen de métricas clave.
- `general-dashboard/profile.tsx` — Perfil del usuario autenticado.
- `general-dashboard/search.tsx` — Búsqueda global.
- `general-dashboard/user-nav.tsx` — Navegación de usuario (avatar, logout).
- `general-dashboard/team-switcher.tsx` — Selector de empresa/equipo.
- `general-dashboard/notifications-item.tsx` — Ítem de notificación en la barra superior.
- `general-dashboard/date-range-picker.tsx` — Selector de rango de fechas para filtros globales.

![screenshot dashboard](/img/screenshot/dashboard.png)

### 7.3. Módulo de Actividades

**Componentes:**
- `activity/activity.tsx` — Vista principal de gestión de actividades.
- `activity/table.tsx` — Tabla paginada con búsqueda y filtros.
- `activity/data-page.tsx` — Página de datos con CRUD completo.

**Integración API:** `createActivity`, `getAll` (paginado), `getActivityById`, `updateActivityStatus`, `assignUserToActivity`, `getCountAllActivities`.

![screenshot actividades](/img/screenshot/actividades.png)

### 7.4. Módulo de Notificaciones

**Componentes:**
- `notification/notifications.tsx` — Vista principal de notificaciones.
- `notification/notification-list.tsx` — Lista paginada de notificaciones.
- `notification/card-item.tsx` — Tarjeta individual de notificación.
- `notification/active-notification.tsx` — Notificaciones activas en tiempo real.
- `notification-tray.tsx` — Bandeja de notificaciones en la barra superior.

**Integración API:** `createNotification`, `getAll`, `getAllNotifications`, `markAsRead`, `getCountAllNotifications`, `getCountAllNotificationsByDay`.

![screenshot notificaciones](/img/screenshot/notificaciones.png)

### 7.5. Módulo de Usuarios

**Componentes:**
- `user/` — Gestión completa de usuarios con tabla paginada y formulario CRUD.
- `user/active-user.tsx` — Panel lateral de usuarios activos en tiempo real.
- `user/user-list-item.tsx` — Ítem de usuario en listas.

**Integración API:** `createUser`, `getAll`, `getUserById`, `searchByQuery`, `getCountAllUsers`, `getForgotPassword`.

![screenshot notificaciones](/img/screenshot/usuarios.png)

### 7.6. Módulo de Participantes

**Componentes:**
- `participant/participant.tsx` — Vista de gestión de participantes.
- `participant/table.tsx` — Tabla con paginación y búsqueda.
- `participant/data-page.tsx` — Formulario CRUD de participantes.
- `participant/chart/top-participants-chart.tsx` — Gráfica de top participantes (Recharts).

![screenshot notificaciones](/img/screenshot/participantes.png)

### 7.7. Módulo de Roles y Permisos

**Componentes:**
- `role/` — Gestión de roles con tabla y formulario.
- `permission/` — Gestión granular de permisos (múltiples variantes de tabla).
- `permissionTemplate/` — Plantillas de permisos predefinidas.

### 7.8. Módulo de Emociones

**Componentes:**
- `emotion/emotion.tsx` — Vista de gestión de emociones.
- `emotion/table.tsx` — Tabla de emociones con filtros.
- `emotion/data-page.tsx` — Formulario CRUD de emociones.

![screenshot notificaciones](/img/screenshot/emociones.png)

### 7.9. Módulo de Reportes

**Componentes:**
- `reports/dashboard-reports.tsx` — Dashboard de reportes con acordeón.
- Exportación a CSV y PDF implementada con `XLSX` y `jsPDF`.

### 7.10. Módulo de Auditoría (Logger)

**Componentes:**
- `logger/audit-log.tsx` — Vista de logs de auditoría.
- `logger/logs-table.tsx` — Tabla de logs con filtros.
- `logger/chart/logs-dashboard-chart.tsx` — Gráfica de actividad de logs.
- `logger/client-ip.tsx` — Visualización de IP del cliente.

### 7.11. Módulo de Configuración (Feature Flags)

**Componentes:**
- `config/config.tsx` — Gestión de configuraciones y feature flags.
- `config/table.tsx` — Tabla de configuraciones activas.
- `admin/cookie-settings.tsx` — Gestión de cookies.
- `admin/database-list.tsx` — Listado de bases de datos (herramienta admin).

![screenshot notificaciones](/img/screenshot/configuraciones.png)

### 7.12. Módulo de Canal WhatsApp

**Componentes:**
- `channel/whatsapp-chat.tsx` — Integración de chat por WhatsApp.

---

## 8. Infraestructura y Servicios Transversales

- **WebSockets (Socket.io):** Integración cliente en tiempo real para notificaciones y usuarios activos.
- **Internacionalización (i18n):** `react-i18next` con traducciones almacenadas en la BD vía `translate.ts`.
- **Carga de archivos:** Componentes `upload/` para subida y listado de archivos.
- **Generación de PDF:** Modal PDF (`pdf/pdf-modal.tsx`) con `jsPDF` y `jspdf-autotable`.
- **Cron Jobs:** Componente `cron/cron-expression-config.tsx` para configuración de tareas programadas.
- **Sincronización offline:** `services/offline-sync.tsx` para manejo de estado sin conexión.
- **Tour de onboarding:** `layouts/tour/` con `@reactour/tour` para guiar nuevos usuarios.
- **Feedback:** `feedback/feedback-modal.tsx` para captura de retroalimentación de usuarios.

---

## 9. Comparativa con el Plan Inicial (Semanas 7–10)

### Fase 3: Frontend Web — Next.js/React (Semanas 7–10)

| Tarea Planificada | Estado | Evidencia |
| :--- | :--- | :--- |
| Configuración del proyecto Next.js (App Router, ESLint/Prettier, theming, layout base) | ✅ Completado | `app/layout.tsx`, `.eslintrc.json`, `.prettierrc.json`, `next-themes` configurado |
| Diseño de componentes reutilizables (UI atómica/compuesta, formularios, tablas, filtros, loaders) | ✅ Completado | `src/registry/new-york/ui/`, `src/components/forms/`, `src/components/layouts/` |
| Rutas y navegación (autenticación, dashboard, gestión de actividades, respuestas y notificaciones) | ✅ Completado | 20+ rutas en `src/pages/`, sistema de tabs dinámicos, `MainNav` con Radix Navigation Menu |
| Integración con la API Backend (React Query, Zustand, protección de rutas) | ✅ Completado | 23 módulos en `src/api/`, `AuthContext`, `FilterContext`, protección por token y permisos |
| Optimización de rendimiento (code-splitting, memoización, virtualización, a11y) | ✅ Completado | Exportación estática, `useMemo`, lazy loading de imágenes, Radix UI con ARIA nativo |
| Pruebas de usabilidad con usuarios piloto | 🔄 En progreso | Tour de onboarding implementado (`@reactour/tour`); sesiones de prueba pendientes de documentar |

> Nota: El plan original menciona React Query y Zustand para gestión de estado. En la implementación se optó por **Jotai** (estado atómico) y **Context API** como alternativas equivalentes, manteniendo los mismos objetivos de caché, estados de carga/error y estado global.

---

## 10. Repositorio de Código

El código fuente del proyecto **vibra-web** se encuentra alojado en:

- **URL del Repositorio:** `https://github.com/proyectohhss2025-unad/vibra-web`

```bash
git clone https://github.com/proyectohhss2025-unad/vibra-web
```

---

## Conclusión

El Frontend Web de Vibra (`vibra-web`) ha completado satisfactoriamente las tareas planificadas para las semanas 7 a 10. Se ha construido una aplicación de dashboard empresarial con Next.js 14, integrando los 23 módulos de la API backend, un sistema de autenticación con JWT y OTP, navegación dinámica por pestañas, gestión de estado con Context API y Jotai, y una librería de componentes UI basada en Radix UI y Tailwind CSS. La arquitectura implementada es responsiva, soporta tiempo real vía Socket.io, e incluye capacidades de exportación (CSV/PDF), internacionalización y auditoría, dejando una base sólida para la integración con la aplicación móvil en las fases siguientes.
