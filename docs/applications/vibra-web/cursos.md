---
title: Panel Cursos
description: Gestión de cursos educativos — SearchableSelect para institución/instructor, formulario con fechas y tabla paginada
---

# Panel Cursos

## Descripción

Gestión de cursos educativos del sistema. Cada curso tiene nombre, institución asociada (compañía), instructor, categoría, fechas de inicio/fin, límite de estudiantes y estado activo/inactivo. Usa `SearchableSelect` para buscar y seleccionar instituciones e instructores mediante búsqueda asíncrona.

## Componentes

| Componente | Ruta | Propósito |
|---|---|---|
| `CourseListPage` | `components/course/course-list-page.tsx` | Página de listado con tabla paginada |
| `CourseFormPage` | `components/course/course-form-page.tsx` | Formulario de creación/edición |

### CourseListPage

Tabla inline con columnas:

| Columna | Render |
|---|---|
| Nombre | `course.name` (font-medium) |
| Institución | `course.companyName \|\| course.companyId \|\| '-'` |
| Instructor | `course.instructorName \|\| course.instructorId \|\| '-'` |
| Categoría | `course.category \|\| '-'` |
| Inicio | Fecha formateada desde `course.startDate` |
| Fin | Fecha formateada desde `course.endDate` |
| Estado | Badge `Activo`/`Inactivo` según `course.status` |
| Acciones | ✏️ Editar y 🗑️ Eliminar |

Paginación: `pageSize` default 12. Búsqueda con `entity="course"`. Confirmación de eliminación vía `ModalConfirm`.

### CourseFormPage

Formulario en grid de 2 columnas con los siguientes campos:

- **Nombre** (obligatorio) — texto, colspan 2
- **Descripción** — textarea 3 rows, colspan 2
- **Institución** (obligatorio) — `SearchableSelect` que busca compañías con `searchCompanies`. Render: nombre, NIT y email. Preselecciona `selectedCompany` en edición.
- **Categoría** — texto libre (ej: Matemáticas, Ciencias)
- **Instructor** — `SearchableSelect` que busca docentes con `searchDocentes`. Render: nombre, documento y email. Preselecciona `selectedInstructor` en edición.
- **Máx. estudiantes** — número
- **Fecha inicio** — datepicker
- **Fecha fin** — datepicker
- **Curso activo** — checkbox

## SearchableSelect

El componente `SearchableSelect` (en `components/forms/searchable-select.tsx`) es un input de búsqueda con debounce (300ms) y dropdown de resultados. Soporta:

- Búsqueda asíncrona vía `searchFn` (mínimo 2 caracteres)
- Navegación por teclado (ArrowUp, ArrowDown, Enter, Escape)
- Selección con render personalizado de opciones
- Botón de limpiar selección
- Spinner de carga
- Texto "Sin resultados" cuando no hay coincidencias
- Cierre automático al hacer clic fuera

## Integración API

| Función | Método | Uso |
|---|---|---|
| `getAll(page, pageSize)` | GET | Lista paginada (retorna `{ data, total }`) |
| `getCourseById(id)` | GET | Carga datos para edición |
| `createCourse(payload)` | POST | Crear nuevo curso |
| `updateCourse(id, payload)` | PUT | Actualizar curso existente |
| `deleteCourse(id)` | DELETE | Eliminar curso |
| `searchCompanies(term)` | GET | Búsqueda asíncrona de instituciones |
| `searchDocentes(term)` | GET | Búsqueda asíncrona de instructores |
| `getCourseProgress()` | GET | Progreso de cursos (para Analytics) |
| `getSimpleCourseList()` | GET | Lista simplificada para selector de cursos |
