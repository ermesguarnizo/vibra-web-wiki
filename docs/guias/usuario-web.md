---
title: Guía de Usuario — Vibra Web (Dashboard)
description: Manual de uso del panel de administración Vibra Web para administradores y docentes
---

# Guía de Usuario — Vibra Web (Dashboard)

## 1. Introducción

**Vibra Web** es el panel de administración central de la plataforma Vibra. Está diseñado para que administradores y docentes gestionen actividades educativas, emociones, participantes, cursos y contenido interactivo. El dashboard consolida herramientas de autoría, seguimiento de progreso, analíticas y feedback en un solo lugar.

El sistema está construido sobre una arquitectura modular: cada funcionalidad principal vive en un módulo independiente accesible desde el panel lateral. Esto permite una navegación rápida y una experiencia de uso coherente en toda la plataforma.

---

## 2. Acceso al Sistema

### 2.1 Inicio de Sesión

1. Abre el navegador y dirígete a la URL del dashboard (proporcionada por el administrador del sistema).
2. Ingresa tu **correo electrónico** y **contraseña** en el formulario de inicio de sesión.
3. Haz clic en **Iniciar Sesión**.

![Pantalla de login](assets/login-screen.png)

Si las credenciales son correctas, accederás al dashboard principal. En caso de error, se mostrará un mensaje en pantalla indicando la causa (usuario no encontrado, contraseña incorrecta, cuenta inactiva).

### 2.2 Recuperación de Contraseña

1. En la pantalla de inicio de sesión, haz clic en **¿Olvidaste tu contraseña?**.
2. Ingresa el correo electrónico asociado a tu cuenta.
3. Revisa tu bandeja de entrada: recibirás un enlace único para restablecer la contraseña.
4. Haz clic en el enlace, ingresa una nueva contraseña y confírmala.
5. La contraseña debe cumplir con los requisitos de seguridad: mínimo 8 caracteres, al menos una mayúscula, una minúscula y un número.

> **Nota:** El enlace de restablecimiento expira después de 1 hora. Si no encuentras el correo, revisa la carpeta de spam.

---

## 3. Navegación General

### 3.1 Sidebar (Panel Lateral)

El sidebar es el componente principal de navegación. Se encuentra en el lado izquierdo de la pantalla y contiene los siguientes módulos:

| Módulo | Descripción |
|---|---|
| **Actividades** | Gestión de actividades educativas (crear, editar, listar, filtrar) |
| **Emociones** | CRUD del catálogo de emociones |
| **Participantes** | Lista y gestión de estudiantes |
| **Cursos** | Creación y asignación de cursos |
| **Feedback** | Revisión de retroalimentación recibida |
| **Analytics** | Dashboard de gráficas y métricas |
| **Perfil** | Edición de datos personales y contraseña |

Cada módulo se resalta al pasar el cursor. El módulo activo se marca con un color de fondo distintivo y un indicador visual en el borde izquierdo.

### 3.2 Sistema de Tabs

Dentro de cada módulo, la información se organiza en pestañas (tabs). Por ejemplo, el módulo de Actividades tiene:

- **Listado** — vista general con tabla de actividades
- **Crear** — formulario para nueva actividad
- **Detalle** — vista individual al seleccionar una actividad

Las tabs permiten cambiar entre vistas sin perder el contexto de navegación.

### 3.3 Breadcrumbs (Migas de Pan)

En la parte superior de cada pantalla se muestra la ruta de navegación actual:

```
Inicio > Actividades > Editar: "Reto de trabajo en equipo"
```

Cada segmento es cliqueable y permite retroceder a un nivel anterior sin usar el navegador.

### 3.4 Barra de Búsqueda Global

El campo de búsqueda en la parte superior del dashboard permite localizar actividades, participantes o cursos por nombre o identificador. Los resultados se muestran en un dropdown con acceso directo al elemento encontrado.

---

## 4. Gestión de Actividades

El módulo de **Actividades** es el núcleo del dashboard. Aquí se crean y gestionan todas las experiencias educativas que los estudiantes verán en la aplicación móvil.

### 4.1 Listado de Actividades

La vista de listado muestra una tabla con las siguientes columnas:

- **Nombre** — título de la actividad
- **Tipo** — reto, evento personal o actividad en pares
- **Dificultad** — fácil, media, difícil
- **Emociones** — emociones asociadas (tags)
- **Estado** — publicada, borrador, archivada
- **Fecha de Creación**
- **Acciones** — editar, duplicar, eliminar

La tabla soporta:

- **Ordenamiento**: haz clic en cualquier encabezado de columna para ordenar ascendente o descendente.
- **Paginación**: navegación entre páginas con控制 de resultados por página (10, 25, 50).
- **Selección múltiple**: marca varias actividades para acciones masivas (publicar, archivar, eliminar).

### 4.2 Filtros

El panel de filtros permite acotar los resultados:

- **Por tipo**: reto, evento personal, actividad en pares
- **Por emoción**: selección múltiple del catálogo de emociones
- **Por dificultad**: fácil, media, difícil
- **Por juego asociado**: sopa de letras, emparejar conceptos, caja de emociones, juego de dados
- **Por estado**: publicada, borrador, archivada
- **Por rango de fechas**: filtro por fecha de creación o última modificación

Los filtros se aplican en combinación (AND). Para limpiar todos los filtros, haz clic en **Limpiar Filtros**.

### 4.3 Crear Actividad

1. Navega al módulo **Actividades** y selecciona la tab **Crear**.
2. Completa los campos del formulario:

#### Campos del formulario

| Campo | Tipo | Obligatorio | Descripción |
|---|---|---|---|
| Nombre | Texto | Sí | Título visible de la actividad (máx. 120 caracteres) |
| Descripción | Texto largo | Sí | Instrucciones o descripción detallada |
| Tipo | Selector | Sí | Reto, Evento Personal o Actividad en Pares |
| Dificultad | Selector | Sí | Fácil, Media, Difícil |
| Emociones | Multiselect | No | Emociones asociadas desde el catálogo |
| Edad Mínima | Número | No | Restricción por edad (3-18 años) |
| Edad Máxima | Número | No | Restricción por edad |
| Juego | Selector | No | Minijuego asociado (opcional) |

#### Tipos de Actividad

- **Reto**: desafíos grupales donde los estudiantes compiten o colaboran para alcanzar un objetivo. Aparecen en la pestaña "Retos" de la app móvil.
- **Evento Personal**: actividades individuales que cada estudiante completa a su propio ritmo. Aparecen en "E-Personal" en la app.
- **Actividad en Pares**: ejercicios diseñados para realizar en parejas, fomentando la colaboración.

#### Emociones Asociadas

Selecciona una o más emociones del catálogo. Las emociones seleccionadas aparecen como tags debajo del selector. Puedes quitarlas haciendo clic en la "x" de cada tag.

#### Juegos Configurables

Al seleccionar un juego, se despliegan opciones adicionales de configuración:

- **Sopa de Letras**: define las palabras a encontrar, tamaño de la cuadrícula, tiempo límite.
- **Emparejar Conceptos**: pares de concepto-definición que el estudiante debe relacionar.
- **Caja de Emociones**: personaliza las emociones que aparecerán en la caja.
- **Juego de Dados**: configura el número de dados, caras y la actividad asociada a cada resultado.

3. Haz clic en **Guardar** para crear la actividad en estado **borrador**, o en **Guardar y Publicar** para publicarla inmediatamente.

### 4.4 Editar Actividad

1. Desde el listado, haz clic en el ícono de lápiz en la fila de la actividad.
2. El formulario se carga con los datos actuales.
3. Realiza los cambios necesarios.
4. Guarda los cambios. El sistema registra la fecha de última modificación.

### 4.5 Duplicar Actividad

La acción **Duplicar** crea una copia exacta de la actividad con el sufijo "(copia)" en el nombre. Esto es útil para crear variaciones rápidas sin partir de cero.

### 4.6 Eliminar Actividad

Al eliminar una actividad:

1. El sistema solicita confirmación: "¿Estás seguro de eliminar esta actividad? Esta acción no se puede deshacer."
2. Si la actividad tiene respuestas asociadas de estudiantes, se muestra una advertencia adicional.
3. Confirma para eliminar definitivamente.

> **Importante:** La eliminación es permanente. Para ocultar una actividad sin perder datos, usa el estado "archivada".

---

## 5. Gestión de Emociones

El módulo de **Emociones** administra el catálogo de emociones que se asocian a las actividades.

### 5.1 Listado de Emociones

Tabla con columnas:

- **Nombre** — nombre de la emoción (ej: alegría, tristeza, sorpresa)
- **Descripción** — breve definición
- **Color** — color representativo (mostrado como swatch)
- **Icono** — ícono asociado
- **Actividades** — contador de actividades que usan esta emoción
- **Estado** — activa / inactiva

### 5.2 Crear Emoción

1. Haz clic en **Nueva Emoción**.
2. Ingresa nombre, descripción y selecciona color e ícono.
3. Guarda. La emoción queda disponible inmediatamente en los selectores de actividades.

### 5.3 Editar / Eliminar

- **Editar**: modifica cualquier campo. Si la emoción está en uso, los cambios se reflejan en todas las actividades asociadas.
- **Eliminar**: solo permitido si ninguna actividad activa tiene asociada esta emoción. En caso contrario, se sugiere desactivarla en lugar de eliminarla.

---

## 6. Gestión de Participantes

### 6.1 Lista de Participantes

Tabla de estudiantes con:

- **Nombre completo**
- **Correo electrónico**
- **Curso** — curso al que pertenece
- **Institución**
- **Actividades Completadas** — número total
- **Racha Actual** — días consecutivos activos
- **Nivel** — nivel actual del estudiante
- **Última Actividad** — fecha y hora del último acceso
- **Estado** — activo / inactivo

### 6.2 Búsqueda

El campo de búsqueda permite filtrar por nombre, correo o identificador del estudiante. La búsqueda es incremental y muestra resultados en tiempo real.

### 6.3 Editar Participante

Al hacer clic en un participante, se abre el panel de detalle con:

- **Datos personales**: nombre, correo, fecha de registro
- **Progreso**: gráfica de actividades completadas por semana/mes
- **Emociones Registradas**: distribution de emociones que ha reportado
- **Racha**: historial de racha actual y racha máxima histórica
- **Ranking**: posición global y por curso
- **Historial**: lista cronológica de actividades realizadas con fechas y scores

### 6.4 Progreso y Ranking

La sección de progreso muestra:

- **Gráfica de barra** — actividades por semana (últimas 8 semanas)
- **Gráfica de pastel** — distribución por tipo de actividad
- **Nivel actual** con barra de progreso hacia el siguiente nivel
- **Puntaje total** acumulado
- **Ranking global** y **ranking por curso** con medalla para top 3

---

## 7. Gestión de Cursos

### 7.1 Crear Curso

1. Navega al módulo **Cursos**.
2. Haz clic en **Crear Curso**.
3. Completa los campos:

| Campo | Descripción |
|---|---|
| Nombre del Curso | Ej: "5° Grado - Grupo A" |
| Institución | Selecciona del listado de instituciones registradas |
| Instructor | Selecciona del listado de docentes |
| Descripción | Opcional: información adicional sobre el curso |
| Código de Acceso | Código único que los estudiantes usarán para inscribirse |

4. Guarda. El código de acceso se genera automáticamente pero puede personalizarse.

### 7.2 Asignar Actividades a un Curso

Desde el detalle del curso, puedes:

- **Asignar actividades existentes**: busca y selecciona actividades del catálogo.
- **Establecer fechas**: define fecha de inicio y fin para cada actividad asignada.
- **Ordenar actividades**: arrastra y suelta para establecer la secuencia.
- **Publicar curso**: al publicar, los estudiantes inscritos ven las actividades en su app.

### 7.3 Estudiantes por Curso

La vista de detalle del curso incluye una tabla de estudiantes inscritos con:

- Nombre y correo
- Fecha de inscripción
- Progreso general en el curso (porcentaje)
- Promedio de score en actividades

Puedes **exportar esta lista** a CSV desde el botón de exportación.

---

## 8. Feedback

### 8.1 Revisar Feedback

El módulo de **Feedback** centraliza toda la retroalimentación enviada por los estudiantes desde la app móvil.

Cada entrada de feedback contiene:

- **Estudiante** — nombre y curso
- **Tipo** — sugerencia, reporte de problema, opinión general
- **Mensaje** — contenido del feedback
- **Fecha de envío**
- **Estado** — pendiente, revisado, convertido a idea

### 8.2 Convertir a Idea

Desde el detalle del feedback:

1. Haz clic en **Convertir a Idea**.
2. El modal de conversión prellena la descripción con el mensaje original.
3. Ajusta el título, categoría y prioridad de la idea.
4. Al guardar, la idea se registra en el backlog y el feedback pasa a estado "convertido".

### 8.3 Acciones Masivas

Selecciona múltiples entradas de feedback para:

- **Marcar como revisado** — cambia el estado a "revisado"
- **Asignar a administrador** — asigna la revisión a un miembro del equipo
- **Exportar** — descarga los feedback seleccionados en formato CSV

---

## 9. Analytics

El módulo de **Analytics** proporciona dashboards visuales con métricas clave.

### 9.1 Panel de Participación

- **Usuarios Activos (DAU/MAU)** — gráfica de línea con usuarios únicos por día y mes
- **Actividades Completadas** — total por día, con comparativa respecto al período anterior
- **Tasa de Finalización** — porcentaje de actividades iniciadas que fueron completadas
- **Tiempo Promedio** — duración media de sesión por actividad

### 9.2 Panel de Emociones

- **Distribución General** — gráfica de pastel con todas las emociones registradas
- **Evolución Temporal** — gráfica de área que muestra cómo cambia el reporte de emociones a lo largo del tiempo
- **Top 5 Emociones** — las emociones más reportadas en el período actual

### 9.3 Panel de Progreso

- **Distribución por Nivel** — barras que muestran cuántos estudiantes hay en cada nivel
- **Curva de Aprendizaje** — progreso promedio semanal del total de estudiantes
- **Rachas Activas** — número de estudiantes con racha activa de 3+, 7+, 14+ y 30+ días

### 9.4 Panel de Ranking

- **Top 10 Global** — tabla con los 10 estudiantes con mayor puntaje acumulado
- **Top por Curso** — selector de curso para ver el ranking interno
- **Mayor Progreso** — estudiantes que más han avanzado en la última semana

### 9.5 Exportación de Datos

Cada gráfica tiene un botón de exportación que permite descargar los datos subyacentes en formato **CSV** o **PNG** (imagen de la gráfica).

Los filtros globales de fecha (últimos 7 días, 30 días, 90 días, personalizado) afectan a todos los paneles simultáneamente.

---

## 10. Perfil de Usuario

### 10.1 Editar Datos Personales

Desde el módulo **Perfil** puedes actualizar:

- **Nombre completo**
- **Correo electrónico**
- **Foto de perfil** (arrastra una imagen o haz clic para seleccionar)
- **Idioma preferido**

Los cambios se guardan automáticamente al hacer clic en **Actualizar Perfil**.

### 10.2 Cambiar Contraseña

1. En la sección **Seguridad**, haz clic en **Cambiar Contraseña**.
2. Ingresa tu contraseña actual.
3. Ingresa la nueva contraseña y confírmala.
4. Requisitos de seguridad:
   - Mínimo 8 caracteres
   - Al menos una letra mayúscula
   - Al menos una letra minúscula
   - Al menos un número
   - Caracteres especiales opcionales pero recomendados

### 10.3 Preferencias del Dashboard

Configura opciones de visualización:

- **Tema**: claro / oscuro (se aplica inmediatamente)
- **Página de inicio**: módulo que se muestra al iniciar sesión
- **Notificaciones**: habilitar/deshabilitar notificaciones por correo

---

## 11. Consejos de Uso

### 11.1 Atajos de Teclado

| Atajo | Acción |
|---|---|
| `Ctrl + /` | Enfocar campo de búsqueda global |
| `Ctrl + N` | Crear nuevo elemento (actividad/emoción/curso según el módulo activo) |
| `Ctrl + S` | Guardar formulario actual |
| `Ctrl + F` | Abrir filtros en la vista de listado |
| `Escape` | Cerrar modal o panel |
| `Ctrl + Z` | Deshacer cambio en formulario |
| `?` | Mostrar/ocultar esta ayuda de teclado |

### 11.2 Buenas Prácticas

- **Nombres descriptivos**: usa nombres que identifiquen claramente el propósito de la actividad. Evita títulos genéricos como "Actividad 1".
- **Una emoción principal**: aunque puedes asociar varias emociones, es mejor elegir una emoción principal por actividad para mantener el foco educativo.
- **Borrador primero**: crea actividades en estado borrador y revísalas antes de publicar. Así evitas exponer contenido incompleto a los estudiantes.
- **Fechas en cursos**: al asignar actividades a un curso, define fechas de inicio y fin. Esto ayuda a los estudiantes a organizar su tiempo.
- **Feedback regular**: revisa el módulo de Feedback al menos una vez por semana. Los estudiantes valoran saber que sus opiniones son escuchadas.
- **Analytics semanal**: dedica 10 minutos cada semana a revisar las gráficas de Analytics. Detectas tendencias y problemas antes de que se agraven.

### 11.3 Solución de Problemas Comunes

| Problema | Solución |
|---|---|
| No aparece una actividad en la app | Verifica que esté publicada y asignada al curso correcto |
| Un estudiante no aparece en el curso | Confirma que usó el código de acceso correcto |
| La gráfica no muestra datos | Ajusta el filtro de fechas; puede que no haya actividad en el período |
| No puedo eliminar una emoción | Desactívala en lugar de eliminarla; está asociada a actividades activas |
| Olvidé mi contraseña | Usa el flujo de recuperación desde la pantalla de login |

---

## 12. Soporte Técnico

Si encuentras algún problema no contemplado en esta guía:

1. Revisa la sección de preguntas frecuentes en el centro de ayuda.
2. Contacta al administrador del sistema.
3. Reporta el incidente a través del formulario de soporte integrado en el dashboard (ícono de interrogación en la esquina superior derecha).

---

*Documento generado para la plataforma Vibra — Versión 2.0*
