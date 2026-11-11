# Producto: AppNotes

## Visión

Capturar y encontrar notas rápidas en el teléfono en segundos, sin cuentas ni conexión.

## Usuario y problema

Una persona, en su propio teléfono Android, que necesita anotar ideas o
recordatorios al momento y encontrarlos después. Uso personal: un solo usuario,
sin compartir.

## Acción principal

Crear una nota (título y contenido) y verla en la lista principal, la más
reciente primero.

## Fuente de datos

Solo local, en el teléfono. Las notas persisten al cerrar y reabrir la app.
Sin servidor ni API.

## Restricciones

- **Tiempo:** unos 90 minutos.
- **Offline:** la app funciona siempre sin conexión.
- **Autenticación:** ninguna.
- **Idioma:** interfaz en español.
- **Solo texto:** cada nota tiene título y contenido.

## Alcance de la v1 (por prioridad)

Las cuatro features van en orden; se construyen las que alcancen en el tiempo.
El mínimo es la primera completa.

1. **Crear y listar notas.** Es el núcleo. Si título y contenido están vacíos,
   no se guarda nada; con al menos uno de los dos, sí.
2. **Editar una nota.**
3. **Borrar una nota.** Se borra al instante y un snackbar ofrece "Deshacer".
4. **Buscar notas.** Filtra la lista por texto en el título o el contenido.

## Criterio de éxito

- **Demo en el emulador:** crear una nota, verla en la lista, cerrar y reabrir
  la app, y que la nota siga ahí.
- **Verificación:** `scripts/verify.sh` en verde (lint, tests y build).

## Fuera de alcance

- Cuentas, login y sincronización en la nube.
- Etiquetas, carpetas y colores.
- Checklists, imágenes y adjuntos.
- Compartir o exportar notas, y recordatorios o notificaciones.

## Preguntas abiertas

- Al editar una nota, ¿sube al principio de la lista o conserva su lugar? Es
  decir, ¿la lista se ordena por fecha de creación o de última modificación?
- Si al editar se dejan vacíos el título y el contenido, ¿qué pasa: se descarta
  el cambio, se borra la nota o se avisa?
- ¿Cuánto dura el snackbar de "Deshacer" al borrar?
- Búsqueda: ¿ignora mayúsculas y acentos? ¿Qué se muestra cuando no hay resultados?
- ¿Qué se muestra cuando todavía no hay notas (estado vacío)?
- ¿Cuánto del contenido se ve en cada elemento de la lista?
