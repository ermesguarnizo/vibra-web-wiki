---
title: Guía de Despliegue
description: Instrucciones para desplegar Vibra API, Web y Mobile en producción
---

# Guía de Despliegue

## Introducción

El ecosistema Vibra está compuesto por tres artefactos desplegables independientes:

- **Vibra API** — Backend basado en NestJS que expone servicios REST para los clientes web y mobile.
- **Vibra Web** — Frontend Next.js que sirve la versión web de la aplicación.
- **Vibra Mobile** — App móvil construida con Expo (React Native) publicada en stores.

Cada componente tiene su propio pipeline de build y despliegue. Esta guía cubre los pasos necesarios para cada uno.

---

## Vibra API (NestJS)

### Requisitos

| Recurso     | Versión mínima | Notas                        |
|-------------|----------------|------------------------------|
| Node.js     | 20 LTS         | Se recomienda usar nvm       |
| MongoDB     | 6.0+           | Requerido en producción      |
| Redis       | 7.0+           | Opcional, para caché/colas   |
| npm / pnpm  | 9+ / 8+        | Usar pnpm si el repo lo exige |

### Variables de entorno

Crear un archivo `.env` en la raíz del proyecto `vibra-api/` con las siguientes variables:

```env
# Servidor
PORT=3000
NODE_ENV=production

# Base de datos
DATABASE_URL=mongodb://usuario:password@host:27017/vibra?retryWrites=true&w=majority

# JWT
JWT_SECRET=una-clave-segura-de-al-menos-32-caracteres
JWT_EXPIRES_IN=7d

# Resend (emails transaccionales)
RESEND_API_KEY=re_xxxxxxxxxxxx

# Redis (opcional)
REDIS_URL=redis://host:6379

# Firebase Admin (notificaciones push)
FIREBASE_PROJECT_ID=proyecto-id
FIREBASE_CLIENT_EMAIL=firebase-adminsdk@proyecto.iam.gserviceaccount.com
FIREBASE_PRIVATE_KEY="-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----\n"

# CORS
CORS_ORIGINS=https://midominio.com,https://admin.midominio.com

# Swagger
SWAGGER_ENABLED=true
SWAGGER_PATH=api
```

### Build y ejecución

```bash
# Compilar TypeScript a JavaScript
npm run compile

# Iniciar en producción
npm run start:prod
```

El comando `compile` genera los artefactos en la carpeta `dist/`. El comando `start:prod` ejecuta `node dist/main.js` con las variables de entorno cargadas.

### Docker

El proyecto incluye un `Dockerfile` multietapa optimizado para producción:

```dockerfile
# Etapa de build
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run compile

# Etapa de producción
FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./
EXPOSE 3000
CMD ["node", "dist/main.js"]
```

Construir y ejecutar:

```bash
docker build -t vibra-api .
docker run -d --name vibra-api -p 3000:3000 --env-file .env vibra-api
```

También se provee un `docker-compose.yml` para entornos que requieran MongoDB y Redis adjuntos:

```yaml
version: "3.8"
services:
  api:
    build: .
    ports:
      - "3000:3000"
    env_file: .env
    depends_on:
      - mongo
      - redis
  mongo:
    image: mongo:7
    volumes:
      - mongo-data:/data/db
  redis:
    image: redis:7-alpine
volumes:
  mongo-data:
```

### Swagger

Una vez desplegado, la documentación interactiva de la API está disponible en la ruta configurada:

```
https://api.midominio.com/api
```

Para entornos productivos se recomienda proteger esta ruta con un middleware de autenticación básica o deshabilitarla estableciendo `SWAGGER_ENABLED=false`.

---

## Vibra Web (Next.js)

### Build estático

```bash
cd vibra-web
npm run build
```

El output se genera en la carpeta `out/` (configurado mediante `output: "export"` en `next.config.ts`).

### Variables de entorno

```env
NEXT_PUBLIC_API_URL=https://api.midominio.com
NEXT_PUBLIC_APP_NAME=Vibra
```

Todas las variables que comienzan con `NEXT_PUBLIC_` se incluyen en el bundle del cliente. Cualquier variable sensible debe omitir ese prefijo.

### Opciones de hosting

**Vercel (recomendado para Next.js)**

```bash
npm i -g vercel
vercel --prod
```

Vercel detecta automáticamente Next.js y configura el build. Las variables de entorno se configuran desde el dashboard o CLI.

**Netlify**

Configurar en `netlify.toml`:

```toml
[build]
  command = "npm run build"
  publish = "out"

[build.environment]
  NEXT_PUBLIC_API_URL = "https://api.midominio.com"
```

**GitHub Pages**

```bash
# Agregar el action en .github/workflows/deploy.yml
npm run build
npx gh-pages -d out
```

**Servidor web estático (Nginx)**

```nginx
server {
    listen 443 ssl;
    server_name midominio.com;

    root /var/www/vibra-web/out;
    index index.html;

    location / {
        try_files $uri $uri.html $uri/ =404;
    }

    # Proxy inverso hacia la API (opcional)
    location /api/ {
        proxy_pass https://api.midominio.com;
    }
}
```

### Nota sobre Server Components

Si el proyecto usa funcionalidades de servidor (Server Actions, API Routes, etc.), no es posible generar un build 100% estático. En ese caso desplegar en **Vercel** o en un **VPS con Node.js** usando `npm run start` (modo servidor).

---

## Vibra Mobile (Expo)

### Requisitos previos

- Node.js 20 LTS
- Expo CLI (`npx expo`)
- EAS CLI (`npm i -g eas-cli`)
- Cuentas de desarrollador en Google Play (USD 25, pago único) y App Store (USD 99/año)
- Proyecto en Firebase Console con credenciales para notificaciones push

### Build Android

**Opción A — EAS Build (recomendada)**

```bash
eas build --platform android --profile production
```

**Opción B — Local**

```bash
npx expo run:android --variant release
```

Requiere Android SDK y JDK 17 instalados localmente.

### Build iOS

**Opción A — EAS Build (recomendada)**

```bash
eas build --platform ios --profile production
```

**Opción B — Local (solo macOS)**

```bash
npx expo run:ios --configuration Release
```

Requiere Xcode 15+ y una cuenta de Apple Developer.

### Publicación en stores

**Google Play Console**

1. Subir el archivo `.aab` generado por EAS Build
2. Completar el formulario de ficha de la app (descripción, capturas de pantalla, clasificación de contenido)
3. Enviar a revisión en el track de producción

**App Store Connect**

1. Subir el `.ipa` mediante Transporter o Xcode
2. Configurar la ficha en App Store Connect
3. Enviar a revisión con TestFlight como paso previo opcional

### Notificaciones push (Firebase Cloud Messaging)

1. Crear un proyecto en [Firebase Console](https://console.firebase.google.com)
2. Registrar la app Android (com.vibra.app) y la app iOS (com.vibra.app)
3. Descargar `google-services.json` (Android) y `GoogleService-Info.plist` (iOS)
4. Colocarlos en las rutas correspondientes del proyecto Expo
5. Configurar las credenciales del servidor FCM en el módulo de notificaciones de `vibra-api`

### EAS Update (actualizaciones OTA)

EAS Update permite enviar actualizaciones de la capa JS directamente a los usuarios sin pasar por las stores:

```bash
eas update --branch production --message "Corrige error en pantalla de login"
```

Los usuarios reciben la actualización al abrir la app (o en segundo plano si está configurado). Esto solo aplica a cambios en JavaScript/TypeScript; los módulos nativos requieren un nuevo build.

**Configuración de ramas:**

| Rama         | Propósito                          |
|-------------|------------------------------------|
| production  | Versión estable en stores          |
| staging     | QA interno                         |
| development | Desarrollo y pruebas diarias       |

---

## Despliegue automatizado (CI/CD)

Se recomienda usar **GitHub Actions** para orquestar los despliegues. Ejemplo para Vibra API:

```yaml
name: Deploy API
on:
  push:
    branches: [main]
    paths: ["vibra-api/**"]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm ci
      - run: npm run compile
      - run: npm test
      # Despliegue en VPS via SSH
      - uses: easingthemes/ssh-deploy@v5
        with:
          source: "vibra-api/"
          host: ${{ secrets.SSH_HOST }}
          username: ${{ secrets.SSH_USER }}
          key: ${{ secrets.SSH_KEY }}
```

---

## Checklist pre-despliegue

- [ ] Todas las variables de entorno están definidas y son secretas
- [ ] `JWT_SECRET` se generó con `openssl rand -hex 64`
- [ ] MongoDB tiene índices creados (`npm run migrate` o scripts equivalentes)
- [ ] CORS está configurado con los orígenes correctos
- [ ] Swagger está deshabilitado o protegido en producción
- [ ] Los certificados SSL están vigentes
- [ ] Se ejecutaron las pruebas (`npm test`)
- [ ] El build se probó en entorno de staging
- [ ] Firebase Cloud Messaging está configurado y funcional
- [ ] EAS Update apunta a la rama correcta
