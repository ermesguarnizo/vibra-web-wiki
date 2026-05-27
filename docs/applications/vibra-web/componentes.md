---
title: Componentes Web
description: Catálogo de componentes reutilizables — ListPageLayout, FormPageLayout, SearchableSelect, PermissionPicker, CardSection, FAB flotante
---

# Componentes Web

Catálogo de componentes reutilizables del dashboard Vibra Web.

---

## ListPageLayout

`components/ui/list-page-layout.tsx`

Layout genérico para páginas de listado con tabla, paginación y búsqueda. Elimina la duplicación de código entre módulos.

### Props

```typescript
interface ListPageLayoutProps<T> {
  title: string;
  subtitle: string;
  data: T[];
  total: number;
  currentPage: number;
  pageSize: number;
  isLoading: boolean;
  onPageChange: (page: number) => void;
  onPageSizeChange: (size: number) => void;
  onRefresh: () => void;
  onAdd?: () => void;
  addLabel?: string;
  columns: Column<T>[];
  rowKey: (item: T) => string | number;
  actions?: ListAction<T>[];
  searchEntity?: string;
  onSearchData?: (data: any[]) => void;
  onSearchLoading?: (loading: boolean) => void;
  emptyMessage?: string;
  deleteConfirm?: DeleteConfirm | null;
}
```

### Columnas

```typescript
interface Column<T> {
  key: string;
  label: string;
  render: (item: T) => React.ReactNode;
  className?: string;
}
```

### Acciones

```typescript
interface ListAction<T> {
  icon: React.ReactNode;
  tooltip: string;
  onClick: (item: T) => void;
  color?: string;
  show?: (item: T) => boolean;  // condicional
}
```

### Características

- `<CurrentDateTime />` en header
- Búsqueda integrada vía componente `Search` (si se proveen `searchEntity`, `onSearchData`, `onSearchLoading`)
- Botón "Agregar" azul con `PlusCircleIcon`
- Tabla con hover `bg-blue-50`
- Spinner animado durante carga
- `<Pagination>` integrada
- `<ModalConfirm>` para confirmación de delete/toggle
- Redirección a `/layout` si no hay token

### Módulos que lo usan

`ActivityDataPage`, `ParticipantDataPage` (y otros como `user`, `company`, `role`, `permission`, `contact`, `config`, `preTest`, `permissionTemplate`).

---

## FormPageLayout

`components/ui/form-page-layout.tsx`

Layout genérico para páginas de formulario (crear/editar). Wrapper con header, auth check y botones Cancelar/Guardar.

### Props

```typescript
interface FormPageLayoutProps {
  title: string;
  isEditing: boolean;
  isSubmitting: boolean;
  onSubmit: (e: React.FormEvent) => void;
  onCancel: () => void;
  children: React.ReactNode;
  submitLabel?: string;
  cancelLabel?: string;
}
```

### Características

- Header con título
- Auth guard: redirige a `/layout` si no hay token
- Botón Cancelar (gris con borde, ícono `XCircleIcon`)
- Botón Guardar (verde, ícono `SaveIcon`, disabled durante envío)
- Estado loading con texto "Guardando..."

### Módulos que lo usan

`ParticipantComponent`, y puede usarse en cualquier formulario que necesite consistencia visual.

---

## SearchableSelect

`components/forms/searchable-select.tsx`

Componente de selección con búsqueda asíncrona y debounce. Similar a un `<select>` pero con capacidad de buscar en el servidor.

### Props

```typescript
interface SearchableSelectProps<T extends { _id: string }> {
  label: string;
  placeholder?: string;
  searchFn: (term: string) => Promise<T[]>;
  renderOption: (item: T) => React.ReactNode;
  getOptionValue?: (item: T) => string;
  value: string;
  onChange: (value: string, item?: T) => void;
  required?: boolean;
  disabled?: boolean;
  minSearchLength?: number;
  debounceMs?: number;
  initialSelectedItem?: T | null;
}
```

### Características

- **Dos modos**: "búsqueda" (input con icono search) y "seleccionado" (item con botón limpiar)
- **Debounce**: 300ms por defecto, configurable
- **Navegación por teclado**: ArrowUp/ArrowDown para moverse, Enter para seleccionar, Escape para cerrar
- **Highlight**: item destacado con fondo azul y texto blanco
- **Loading**: spinner animado durante la búsqueda
- **Click outside**: cierra el dropdown automáticamente
- **Preselección**: acepta `initialSelectedItem` para evitar llamada API extra en edición

### Módulos que lo usan

`CourseFormPage` para seleccionar institución (`searchCompanies`) e instructor (`searchDocentes`).

---

## PermissionPicker

`components/permission/permission-picker.tsx`

Selector de permisos con búsqueda y agrupación por categoría. Usado en formularios de roles y templates de permisos.

### Props

```typescript
interface PermissionPickerProps {
  selectedIds: string[];
  onChange: (ids: string[]) => void;
  label?: string;
}
```

### Características

- Carga permisos vía `getAllPermissions(1, 200)` al montar
- **Búsqueda local** por nombre, serial o descripción (filtra en cliente)
- **Agrupación** por `permissionCategory.name` con headers en `bg-gray-50` y texto uppercase
- Checkboxes con contador de seleccionados
- Scroll infinito visual (max-h-80 con overflow)
- Estados: loading ("Cargando permisos..."), empty ("No hay permisos disponibles"), sin resultados ("Sin resultados")

### Módulos que lo usan

`PermissionTemplate` para asignar permisos a templates.

---

## CardSection

`components/ui/card-section.tsx`

Sección contenedora con header degradado y cuerpo. Usada para agrupar campos de formulario en secciones visualmente distintas.

### Props

```typescript
interface CardSectionProps {
  title: string;
  subtitle?: string;
  children: React.ReactNode;
}
```

### Características

- Header con `bg-gradient-to-r from-blue-50 to-white`
- Borde inferior separador
- Padding interior de 6 (1.5rem) en el cuerpo
- Sombra suave (`shadow-sm`) y borde `border-gray-200`
- Subtítulo opcional en gris

### Módulos que lo usan

`ActivityComponent` para las secciones: Información Principal, Programación, Recursos Multimedia, Preguntas, Tips de Apoyo Emocional, Juegos.

---

## FloatingFeedbackBtn

`components/ui/floating-feedback-btn.tsx`

Botón de acción flotante (FAB) para enviar feedback desde cualquier página del dashboard. Implementa [vibra-073].

### Características

- Posición: `fixed bottom-24 right-6 z-50`
- Botón principal: círculo índigo `w-14 h-14` con ícono 📝
- Al abrir, muestra dos opciones expandidas:
  - **💬 Enviar apoyo** — hover verde
  - **✨ Sugerir mejora** — hover azul
- Al seleccionar, abre `FeedbackModal` con el tipo correspondiente
- Envía `POST /api/feedback` con `{ title, description, isFeature, isSupport, createdBy }`
- Toast de éxito/error con `sonner`
- Transiciones `transition-all duration-200` y `active:scale-95`
