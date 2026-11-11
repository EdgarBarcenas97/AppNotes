# Diseño v1: AppNotes

**Estado:** aprobado el 2026-11-11 · mockup publicado en
https://claude.ai/artifact/SdkdFYGLh7ypGXCuwMvgMb

Pantallas, estados, textos y testTags de la v1. El mockup visual está en
[`docs/design/mockup.html`](design/mockup.html). Las specs de `/feature` toman
de aquí los textos exactos (van a `strings.xml`) y los testTags (van a
`object XTags` en el archivo de cada pantalla).

Tema: `AppTheme` de `core/designsystem` (verde `#1F6F5C`), claro u oscuro según
el sistema. Componentes Material 3, sin librerías extra.

## Mapa de navegación

```
Lista de notas ──(FAB +)──────────────► Editor (nueva)
      ▲  │                                   │ Guardar → Lista
      │  └─(tocar una nota)─────────────► Editor (editar)
      │                                      │ Guardar → Lista
      │                                      │ Borrar  → Lista + snackbar "Nota borrada · Deshacer"
      └───────────(← Volver: sale sin guardar)┘

Lista de notas ──(lupa)──► modo búsqueda (misma pantalla) ──(← cerrar)──► Lista
```

Dos destinos de Navigation Compose: `notes` (lista) y `editor?noteId={id}`
(sin `noteId` crea una nota; con `noteId` la edita).

## Pantalla 1: Lista de notas (`notes`)

**Propósito:** ver todas las notas, la más reciente primero, y entrar a crear,
editar o buscar.

**Features:** `create_and_list_notes` (lista y FAB), `edit_note` (tocar una
nota), `delete_note` (snackbar), `search_notes` (modo búsqueda).

**Componentes:** `Scaffold` · `TopAppBar` con título e icono de búsqueda ·
`LazyColumn` de `Card` (título en `titleMedium` a 1 línea, contenido en
`bodyMedium` a 2 líneas, ambos con elipsis) · `FloatingActionButton` con icono
`+` · `SnackbarHost`. En modo búsqueda, la `TopAppBar` cambia a un campo de
texto con flecha de cerrar.

Si la nota no tiene título, la card muestra solo el contenido, hasta 3 líneas.
Si no tiene contenido, solo el título.

### Estados

| Estado | Qué se ve | Feature |
|---|---|---|
| Cargando | Indicador circular centrado (`LoadingState`) | create_and_list_notes |
| Vacío | Mensaje centrado: "Todavía no tienes notas." y debajo "Toca + para escribir la primera." FAB visible | create_and_list_notes |
| Contenido | Lista de cards, la más reciente arriba. FAB visible | create_and_list_notes |
| Error | "No se pudieron cargar tus notas." y botón "Reintentar" (`ErrorState`) | create_and_list_notes |
| Nota borrada | Lista sin la nota y snackbar "Nota borrada" con acción "Deshacer" | delete_note |
| Buscando | Campo de búsqueda en la barra; la lista muestra solo las notas que coinciden. Sin FAB | search_notes |
| Sin resultados | Mensaje centrado: "Ninguna nota coincide con «{texto}»." | search_notes |

### Textos (`strings.xml`)

| Clave | Texto |
|---|---|
| `notes_title` | Notas |
| `notes_create` | Nueva nota _(contentDescription del FAB)_ |
| `notes_search` | Buscar _(contentDescription de la lupa)_ |
| `notes_search_hint` | Buscar en tus notas |
| `notes_search_close` | Cerrar búsqueda _(contentDescription)_ |
| `notes_empty_title` | Todavía no tienes notas. |
| `notes_empty_hint` | Toca + para escribir la primera. |
| `notes_error` | No se pudieron cargar tus notas. |
| `state_retry` | Reintentar _(ya existe)_ |
| `notes_search_empty` | Ninguna nota coincide con «%1$s». |
| `notes_deleted` | Nota borrada |
| `notes_undo` | Deshacer |

### testTags (`object NotesListTags`)

| Constante | Valor | Elemento |
|---|---|---|
| `CREATE_FAB` | `notes_create_fab` | FAB `+` |
| `SEARCH_BUTTON` | `notes_search_button` | Lupa de la barra |
| `LIST` | `notes_list` | `LazyColumn` |
| `ITEM_PREFIX` | `note_item_` | Cada card: `note_item_{id}` |
| `SEARCH_FIELD` | `notes_search_field` | Campo de búsqueda |
| `SEARCH_CLOSE` | `notes_search_close` | Flecha que cierra la búsqueda |
| `SEARCH_EMPTY` | `notes_search_empty` | Mensaje "Ninguna nota coincide…" |
| `SNACKBAR` | `notes_snackbar` | Snackbar de nota borrada (la acción se busca por el texto "Deshacer") |

Los estados comunes usan `StateTags` de `core/designsystem`:
`state_loading`, `state_empty`, `state_error` y `state_retry`.

## Pantalla 2: Editor de nota (`editor?noteId={id}`)

**Propósito:** escribir una nota nueva o cambiar una existente. La misma
pantalla sirve para las dos cosas.

**Features:** `create_and_list_notes` (nueva), `edit_note` (editar),
`delete_note` (icono de borrar).

**Componentes:** `Scaffold` · `TopAppBar` con flecha de volver, título de la
pantalla, `TextButton` "Guardar" y, solo al editar, `IconButton` de borrar
(papelera) · `TextField` sin borde para el título (`titleLarge`, una línea) ·
`TextField` sin borde para el contenido (`bodyLarge`, varias líneas, ocupa el
resto de la pantalla).

### Estados

| Estado | Qué se ve | Feature |
|---|---|---|
| Nueva, vacía | Barra "Nueva nota". Campos vacíos con sus textos de ayuda. "Guardar" deshabilitado. Sin papelera | create_and_list_notes |
| Nueva, con texto | "Guardar" habilitado en cuanto el título o el contenido tienen texto | create_and_list_notes |
| Cargando nota | Indicador circular mientras se lee la nota a editar (`LoadingState`) | edit_note |
| Editar | Barra "Editar nota". Campos con el título y el contenido actuales. "Guardar" y papelera | edit_note, delete_note |
| Editar, vaciada | Si se borran título y contenido, "Guardar" se deshabilita | edit_note |

Al tocar **Guardar** se guarda y se vuelve a la lista. Al tocar la
**papelera** se borra y se vuelve a la lista, que muestra el snackbar. La
**flecha de volver** (y el botón atrás del sistema) sale sin guardar.

### Textos (`strings.xml`)

| Clave | Texto |
|---|---|
| `editor_title_new` | Nueva nota |
| `editor_title_edit` | Editar nota |
| `editor_title_hint` | Título |
| `editor_content_hint` | Escribe tu nota |
| `editor_save` | Guardar |
| `editor_back` | Volver _(contentDescription)_ |
| `editor_delete` | Borrar nota _(contentDescription)_ |

### testTags (`object NoteEditorTags`)

| Constante | Valor | Elemento |
|---|---|---|
| `BACK` | `editor_back` | Flecha de volver |
| `TITLE_FIELD` | `editor_title_field` | Campo de título |
| `CONTENT_FIELD` | `editor_content_field` | Campo de contenido |
| `SAVE` | `editor_save` | Botón "Guardar" |
| `DELETE` | `editor_delete` | Papelera (solo al editar) |

## Qué pantalla toca cada feature

| Feature | Lista de notas | Editor |
|---|---|---|
| 1. `create_and_list_notes` | Cargando, vacío, contenido, error, FAB | Nueva (vacía y con texto) |
| 2. `edit_note` | Tocar una card | Cargando nota, editar, editar vaciada |
| 3. `delete_note` | Snackbar "Nota borrada · Deshacer" | Papelera |
| 4. `search_notes` | Lupa, modo búsqueda, sin resultados | — |

## Decisiones

| Decisión | Alternativa descartada | Por qué |
|---|---|---|
| Lista de cards en una columna | Cuadrícula escalonada de dos columnas | Se leen mejor los textos y es más simple de testear |
| Editor en pantalla aparte, igual para crear y editar | Diálogo sobre la lista | Un diálogo es incómodo para contenido largo; una sola pantalla evita duplicar UI |
| Borrar con una papelera en el editor | Deslizar la card en la lista | La papelera se descubre sola y es estable en tests; el gesto queda fuera de la v1 |
| Guardar con un botón "Guardar" explícito | Autoguardado al salir | Hace verificable la regla "una nota vacía no se guarda" (el botón se deshabilita) |
| "Guardar" deshabilitado si título y contenido están vacíos, también al editar | Avisar con un mensaje de error al intentar guardar | Evita el error en lugar de explicarlo; resuelve qué pasa al vaciar una nota en edición |
| Volver sale sin guardar | Preguntar si se descartan los cambios | Más simple para ~90 min; el aviso queda como pregunta abierta |
| Búsqueda dentro de la barra de la lista | Pantalla de búsqueda aparte | No agrega un destino de navegación; se sale con la flecha |
| Snackbar del borrado en la lista | Snackbar en el editor | La nota ya no existe en el editor; la lista es donde se ve que desapareció |

## Preguntas abiertas

Siguen las de [`docs/product.md`](product.md) que el diseño no resuelve:

- Orden después de editar: ¿la nota sube al principio o conserva su lugar? No
  cambia las pantallas; se decide en la spec de `edit_note`.
- ¿Cuánto dura el snackbar de "Deshacer"? Se propone `SnackbarDuration.Short`
  de Material, a confirmar en la spec de `delete_note`.
- Búsqueda: ¿ignora mayúsculas y acentos? Se decide en la spec de `search_notes`.
- ¿Avisar al salir del editor con cambios sin guardar? Fuera de la v1 salvo
  que se pida.
