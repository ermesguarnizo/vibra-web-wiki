---
title: Guía de Desarrollo
description: Setup local, estándares de código y cómo contribuir al proyecto Vibra
---

# Guía de Desarrollo

## Introducción al ecosistema Vibra

El proyecto Vibra se organiza en cuatro repositorios independientes:

| Repositorio    | Descripción                                          | Stack                          |
|---------------|------------------------------------------------------|--------------------------------|
| `vibra-api`   | Backend REST — módulos de negocio, auth, notificaciones | NestJS, Mongoose, Redis       |
| `vibra-web`   | Frontend web — panel de administración y landing     | Next.js, Tailwind CSS, shadcn |
| `vibra-web-mb`| Versión web tipo PWA para navegadores móviles        | Next.js, Tailwind CSS, PWA     |
| `vibra-web-wiki`| Documentación técnica del ecosistema               | Markdown, VitePress            |

Cada repositorio se versiona de forma independiente pero comparten convenciones de código, tooling y estándares de calidad.

---

## Setup local

### 1. Clonar los repositorios

```bash
git clone https://github.com/cdsmaya/vibra-api.git
git clone https://github.com/cdsmaya/vibra-web.git
git clone https://github.com/cdsmaya/vibra-web-mb.git
git clone https://github.com/cdsmaya/vibra-web-wiki.git
```

### 2. Instalar dependencias

```bash
cd vibra-api && npm install
cd ../vibra-web && npm install
cd ../vibra-web-mb && npm install
cd ../vibra-web-wiki && npm install
```

### 3. Configurar variables de entorno

**vibra-api** — crear `vibra-api/.env`:

```env
PORT=3000
NODE_ENV=development
DATABASE_URL=mongodb://localhost:27017/vibra-dev
JWT_SECRET=dev-secret-key-no-uses-en-produccion
JWT_EXPIRES_IN=7d
RESEND_API_KEY=
CORS_ORIGINS=http://localhost:3001,http://localhost:5173
```

**vibra-web** — crear `vibra-web/.env.local`:

```env
NEXT_PUBLIC_API_URL=http://localhost:3000
```

**vibra-web-mb** — crear `vibra-web-mb/.env.local`:

```env
NEXT_PUBLIC_API_URL=http://localhost:3000
```

### 4. Iniciar MongoDB local

**Opción A — Instalación local**

Descargar e instalar MongoDB Community Edition desde [mongodb.com](https://www.mongodb.com/try/download/community). Luego iniciar el servicio:

```bash
mongod --dbpath /data/db
```

**Opción B — MongoDB Compass + Atlas**

Usar [MongoDB Compass](https://www.mongodb.com/products/compass) como GUI y conectarse a un cluster gratuito de MongoDB Atlas:

```
mongodb+srv://usuario:password@cluster0.xxxxx.mongodb.net/vibra-dev
```

**Opción C — Docker**

```bash
docker run -d --name mongo-local -p 27017:27017 mongo:7
```

### 5. Ejecutar en modo desarrollo

| Proyecto       | Comando                | Puerto | URL                    |
|----------------|------------------------|--------|------------------------|
| `vibra-api`    | `npm run start:dev`    | 3000   | http://localhost:3000   |
| `vibra-web`    | `npm run dev`          | 3001   | http://localhost:3001   |
| `vibra-web-mb` | `npm run dev`          | 3002   | http://localhost:3002   |
| `vibra-web-wiki` | `npm run dev`       | 5173   | http://localhost:5173   |

Para **Vibra Mobile** (si se trabaja desde el monorepo o repositorio aparte):

```bash
npx expo start
```

Escanea el código QR con Expo Go en tu dispositivo físico o presiona `a` para abrir el emulador Android.

---

## Estándares de código

### TypeScript estricto

Todos los proyectos usan TypeScript en modo estricto. El archivo `tsconfig.json` debe incluir:

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "exactOptionalPropertyTypes": false
  }
}
```

### ESLint + Prettier

```bash
# Verificar lint
npm run lint

# Formatear código
npm run format
```

Las reglas están definidas en `eslint.config.js` (formato flat config para ESLint 9) y `prettier.config.js`. Los conflictos entre ESLint y Prettier se resuelven mediante `eslint-config-prettier`.

### Conventional commits

Todos los mensajes de commit deben seguir el formato [Conventional Commits](https://www.conventionalcommits.org/):

```
<tipo>(<alcance>): <descripción>

[body opcional]
```

| Tipo       | Uso                                       |
|------------|-------------------------------------------|
| `feat`     | Nueva funcionalidad                       |
| `fix`      | Corrección de error                       |
| `refactor` | Cambio interno sin cambio funcional       |
| `style`    | Cambios de formato (espacios, commas)     |
| `docs`     | Documentación                             |
| `test`     | Pruebas                                   |
| `chore`    | Tareas de mantenimiento (build, CI, etc.) |

Ejemplos:

```
feat(api): agregar endpoint de recuperación de contraseña
fix(web): corregir validación de formulario en registro
docs(wiki): actualizar guía de despliegue
```

### Husky pre-commit hooks

El proyecto usa [Husky](https://typicode.github.io/husky/) para ejecutar validaciones antes de cada commit:

```bash
# .husky/pre-commit
npx lint-staged
```

`lint-staged` ejecuta ESLint y Prettier únicamente sobre los archivos modificados:

```json
{
  "*.ts": ["eslint --fix", "prettier --write"],
  "*.tsx": ["eslint --fix", "prettier --write"],
  "*.json": ["prettier --write"]
}
```

---

## Estructura de proyecto

### Vibra API — Modular monolith

```
vibra-api/
├── src/
│   ├── domains/             # Módulos de negocio
│   │   ├── auth/
│   │   │   ├── auth.module.ts
│   │   │   ├── auth.controller.ts
│   │   │   ├── auth.service.ts
│   │   │   ├── dto/
│   │   │   │   ├── login.dto.ts
│   │   │   │   └── register.dto.ts
│   │   │   ├── schemas/
│   │   │   │   └── user.schema.ts
│   │   │   └── strategies/
│   │   │       └── jwt.strategy.ts
│   │   ├── shipping/
│   │   │   ├── shipping.module.ts
│   │   │   ├── shipping.controller.ts
│   │   │   ├── shipping.service.ts
│   │   │   ├── dto/
│   │   │   │   └── create-shipment.dto.ts
│   │   │   └── schemas/
│   │   │       └── shipment.schema.ts
│   │   └── notifications/
│   │       ├── notifications.module.ts
│   │       ├── notifications.service.ts
│   │       └── providers/
│   │           ├── email.provider.ts
│   │           └── push.provider.ts
│   ├── common/              # Utilidades compartidas
│   │   ├── filters/
│   │   ├── guards/
│   │   ├── interceptors/
│   │   └── pipes/
│   ├── config/              # Configuración centralizada
│   │   └── env.config.ts
│   └── main.ts
├── test/
├── .env
├── Dockerfile
├── nest-cli.json
├── tsconfig.json
└── package.json
```

### Vibra Web — Next.js híbrido (App Router + Pages Router)

```
vibra-web/
├── app/                     # App Router
│   ├── layout.tsx
│   ├── page.tsx
│   ├── login/
│   │   └── page.tsx
│   ├── dashboard/
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   └── envios/
│   │       └── page.tsx
│   └── api/                 # API Routes
│       └── auth/
│           └── [...nextauth].ts
├── pages/                   # Pages Router (para páginas existentes)
│   └── landing/
│       └── index.tsx
├── components/
│   ├── ui/                  # shadcn/ui components
│   ├── layout/
│   └── forms/
├── lib/
│   ├── api.ts               # Cliente HTTP tipado
│   └── utils.ts
├── hooks/
├── public/
├── next.config.ts
├── tailwind.config.ts
└── package.json
```

### Vibra Mobile — Expo Router

```
vibra-mobile/
├── app/                     # Expo Router (file-based routing)
│   ├── _layout.tsx
│   ├── index.tsx
│   ├── login.tsx
│   ├── (tabs)/
│   │   ├── _layout.tsx
│   │   ├── envios.tsx
│   │   └── perfil.tsx
│   └── envio/
│       └── [id].tsx
├── src/
│   ├── features/            # Funcionalidades agrupadas por dominio
│   │   ├── auth/
│   │   ├── shipping/
│   │   └── notifications/
│   ├── shared/              # Código compartido
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── api/
│   │   └── utils/
│   └── providers/
├── app.json
├── eas.json
└── package.json
```

---

## Convenciones de nomenclatura

| Elemento                | Convención     | Ejemplo                         |
|------------------------|----------------|----------------------------------|
| Schemas / Mongoose     | PascalCase     | `User`, `Shipment`, `Rate`       |
| DTOs                   | PascalCase     | `CreateUserDto`, `LoginDto`      |
| Servicios              | PascalCase     | `AuthService`, `ShippingService` |
| Controladores          | PascalCase     | `AuthController`                 |
| Módulos NestJS         | PascalCase     | `AuthModule`, `ShippingModule`   |
| Componentes React      | PascalCase     | `LoginForm`, `ShipmentCard`      |
| Hooks React            | camelCase      | `useAuth`, `useShipments`        |
| Archivos fuente        | kebab-case     | `login-form.tsx`, `auth.service.ts` |
| Archivos de esquema    | kebab-case     | `user.schema.ts`                 |
| Directorios            | kebab-case     | `auth/`, `shipping/`, `login-form/` |
| Interfaces TypeScript  | PascalCase     | `IUser`, `IShipmentPayload`      |
| Tipos TypeScript       | PascalCase     | `UserRole`, `ShipmentStatus`     |
| Variables              | camelCase      | `userName`, `shipmentList`       |
| Funciones              | camelCase      | `getUser()`, `createShipment()`  |
| Constantes             | UPPER_SNAKE    | `MAX_RETRIES`, `DEFAULT_PAGE`    |

---

## Pull Requests y contribución

### Flujo de trabajo

1. Crear un branch desde `main` con el formato `<tipo>/<descripcion-breve>`:

```bash
git checkout -b feat/agregar-login-biometrico
git checkout -b fix/corregir-validacion-rut
git checkout -b docs/actualizar-readme
```

2. Desarrollar la funcionalidad haciendo commits atómicos con mensajes convencionales.

3. Mantener el branch actualizado con `main`:

```bash
git fetch origin
git rebase origin/main
```

4. Abrir un Pull Request en GitHub con la siguiente plantilla:

```markdown
## Descripción

[Descripción clara de qué hace este PR y por qué]

## Cambios

- [x] Nueva funcionalidad X
- [x] Pruebas unitarias para X
- [ ] Documentación actualizada

## Screenshots (si aplica)

[Capturas de pantalla o video]

## Tips de revisión

- [ ] Revisar archivo `src/domains/auth/auth.service.ts` línea 42
- [ ] Probar flujo de login con usuario inválido
```

### Reglas de revisión

- Todo PR requiere al menos una aprobación de otro miembro del equipo
- No se puede mergear sin que pase CI (lint + tests + build)
- Los cambios de schema MongoDB deben incluir el script de migración correspondiente
- Los cambios en la API deben mantener o actualizar la documentación Swagger
- Los cambios visuales deben incluir capturas (web) o video (mobile)

### Checklist previo al PR

- [ ] Ejecuté `npm run lint` y no hay errores
- [ ] Ejecuté `npm run format` (Prettier)
- [ ] Agregué pruebas para los nuevos cambios
- [ ] Todas las pruebas existentes siguen pasando (`npm test`)
- [ ] Actualicé la documentación si es necesario
- [ ] Verifiqué que no hay secretos ni credenciales en el código
- [ ] El mensaje del PR sigue el formato convencional
