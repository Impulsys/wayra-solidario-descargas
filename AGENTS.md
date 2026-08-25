# AGENTS.md — reglas para agentes de código en este repo

Este archivo es el contrato para CUALQUIER agente de código que no sea la
sesión principal de Claude (Codex, u otros). Si estás leyendo esto como
agente: estas reglas no son sugerencias.

## Qué es este repo

Proyecto `wayra-solidario-descargas` de Impulsys. (Descripción a completar por la primera sesión de Claude que trabaje acá: qué es, para qué cliente, si está en producción.)

Hasta que "Estado actual" de `COORDINACION.md` diga lo contrario, asumí que hay clientes usando producción: un cambio mal integrado rompe algo que alguien usa hoy.

## Antes de cualquier tarea: leé `COORDINACION.md`

`COORDINACION.md` (raíz) es el documento compartido entre TODAS las sesiones
que trabajan en este repo: Claude en la PC, Claude en la notebook, y vos.
Es la última versión del contexto. Antes de opinar, auditar o tocar algo:

1. `git pull` (o pedí que lo hagan) para tener la última versión.
2. Leé el bloque **"Estado actual"** de arriba de todo: qué hay en
   producción, qué está en curso, qué está pendiente y qué pregunta abierta
   hay entre agentes.
3. Leé las entradas del registro que no conocías. Lo que parece faltante
   puede haberse hecho ayer en la otra máquina o por el otro agente.

Y después de cada tarea tuya que deje algo (una auditoría, una edición,
un commit, una pregunta que quede abierta), dejás tu entrada ahí. Formato en
el mismo archivo.

## Tus tres modos de trabajo

La regla de este repo no es de capacidad, es de **autorización**: podés hacer
todo lo que Lautaro te pida, pero solo cuando te lo pide explícitamente para
esa tarea. Sin ese OK, sos solo lectura. El permiso se da por tarea, en la
conversación en curso; no arrastra a la tarea siguiente ni a otra sesión.

### Modo 1 — LECTURA Y ANÁLISIS (por defecto, siempre)

- Podés leer todo el código (salvo lo listado en "Datos que no se tocan"),
  analizar diffs, correr `tsc`/build/tests de solo lectura, señalar bugs y
  proponer cambios como diff o como texto.
- **No modificás ningún archivo del working tree**, salvo los dos del modo
  3 (`AUDITORIAS.md` y tu entrada en `COORDINACION.md`).
- Si el pedido implica editar código y Lautaro no dijo explícitamente que
  edites, entregás la propuesta (diff o archivo aparte) y preguntás. No
  aplicás.

### Modo 2 — TRABAJO COMPLETO (solo con OK explícito de Lautaro para esa tarea)

Con OK explícito de Lautaro podés hacer **todo** lo que él te pida para esa
tarea: editar archivos, instalar dependencias, correr builds, commitear,
pushear y deployar.

- Se habilita únicamente cuando Lautaro, en esta conversación y para esta
  tarea, dice algo inequívoco: "editá", "aplicalo", "implementalo vos",
  "hacé el cambio", "commiteá", "pusheá", "deployá". Un "dale" a una
  propuesta ambigua **no** alcanza: preguntá antes de tocar.
- **El permiso cubre exactamente lo que dijo.** "Editá" no incluye commit;
  "commiteá" no incluye push; "pusheá" no incluye deploy. Cada paso
  siguiente, pedilo. Aprobar un deploy una vez no aprueba los siguientes.
- Es por tarea y por conversación. Terminada la tarea, volvés al modo 1.
- Editás solo los archivos que la tarea necesita. Nada de "ya que estoy".
  Si en el medio el cambio resulta más grande de lo acordado, frenás y
  avisás: el permiso cubre lo que se pidió, no lo que apareció.
- Al terminar dejás tu entrada en `COORDINACION.md` — tipo `edición Codex`
  si solo editaste, `commit` o `deploy` si llegaste hasta ahí — con la lista
  exacta de archivos, el hash y qué verificaste. **Si commiteás, la entrada
  va en el mismo commit** (regla del repo). Si deployás, verificación
  post-deploy y registro apenas termina. Después de un commit o deploy,
  reescribís también el bloque "Estado actual".
- Si la tarea toca reglas de seguridad, dinero o datos de clientes, decilo
  en la entrada para que Claude lo revise en su próxima sesión.

### Modo 3 — SUBIR TU AUDITORÍA A GIT (permiso permanente, alcance cerrado)

No necesita OK: es permanente, pero está acotado a dos archivos:
**`AUDITORIAS.md`** y **tu entrada en `COORDINACION.md`**. Nada más viaja en
un commit de este modo.

- Para subir, usás **siempre** el script, nunca `git add`/`commit`/`push` a
  mano:

  ```
  node scripts/subir-auditoria.mjs "<título corto de la auditoría>"
  ```

  El script hace `git pull --rebase`, agrega SOLO esos dos archivos,
  commitea como `audit(codex): <título>` y pushea a la rama principal. Si
  hay conflicto o algo no cierra, aborta y te dice qué pasa: en ese caso
  avisá, no lo resuelvas a mano.
- Si el script rechaza (por ejemplo, porque `AUDITORIAS.md` no cambió, o la
  rama no es la principal, o hay staging de otra sesión), no hay atajo: el
  rechazo es la regla.

### Lo que sigue prohibido en cualquier modo (no son permisos: es seguridad)

- **Reescribir historial compartido**: `git push --force`, `git reset --hard`
  sobre commits ya pusheados, `rebase` de ramas publicadas. Rompe el trabajo
  de las otras tres sesiones.
- **Subir, pegar o mandar a la nube** secretos o datos de clientes (sección
  "Datos que NO se tocan").
- **Reescribir o borrar entradas ajenas** en `COORDINACION.md` o
  `AUDITORIAS.md`.
- **Actuar sobre algo que en "Estado actual" figura "en curso" por otra
  sesión** sin preguntar antes.

## Dónde dejás tu trabajo: `AUDITORIAS.md`

Tu entregable principal como revisor es una entrada en **`AUDITORIAS.md`**
(raíz). Reglas:

- Usá el formato que está en ese archivo, sin cambiarlo: una entrada nueva
  ARRIBA de las anteriores, con `auditor: Codex`.
- Cada hallazgo cita **archivo y línea**. Sin cita no es hallazgo.
- Separá lo que **ROMPE** (bug, permisos, plata mal calculada) de lo que
  **MEJORA** (deuda, estilo). Primero lo que rompe.
- Todo hallazgo nuevo nace `ABIERTO`. El estado lo cambia quien integra, no
  vos. La sección "Plan de acción" la completa quien integra: dejala vacía.
- Lo dudoso va en **Preguntas**, no en la tabla.
- No pegues datos sensibles: ni montos con nombre de cliente, ni
  credenciales, ni contenido de planillas de clientes. Citá la ubicación,
  no el contenido.
- No modifiques entradas anteriores ni sus estados. Si algo ya auditado
  sigue mal, va como hallazgo nuevo en tu entrada, con referencia al
  número viejo.
- Después de escribirla: entrada en `COORDINACION.md` (tipo `auditoría`,
  una línea por hallazgo ROMPE como mínimo) y subís las dos con el script
  del modo 3. Claude la lee en su próxima sesión, en cualquiera de las dos
  máquinas.

## Cómo se habla entre agentes

- Vos y Claude no se corrigen en silencio. Si no estás de acuerdo con algo
  que Claude registró en `COORDINACION.md`, dejás una entrada tipo
  `pregunta` con las dos posturas y Lautaro decide.
- Las preguntas abiertas se listan en "Estado actual → Pendientes entre
  agentes". Las cierra quien las responde, dejando entrada.
- No repitas trabajo: si en "Estado actual" figura algo "en curso" por otra
  sesión, no lo audites ni lo edites hasta que figure como hecho.

## Reglas duras de este repo (aplican a cualquier propuesta o edición)

1. **`COORDINACION.md`** es la fuente de contexto compartida; se lee antes
   y se escribe después. Toda acción relevante queda registrada ahí.
2. **Si existe `CLAUDE.md` en la raíz, leelo: sus reglas duras aplican
   también a vos.** Lo que ahí figure como "no tocar" es "no tocar".
3. **Asumí que hay clientes usando producción** hasta que "Estado actual"
   diga lo contrario. Un cambio mal integrado rompe algo que alguien usa
   hoy.
4. **No tocar reglas de seguridad ni permisos** (`firestore.rules`,
   `storage.rules`, políticas, claims, middleware de auth) sin pedido
   explícito. Un cambio "inocente" abre datos.
5. **Nunca proponer lecturas masivas a Firestore u otra base on-demand sin
   gate de costo.** Las facturas de infraestructura son plata del negocio.
6. **No inventar datos ni números.** Si falta un dato, es una pregunta.

## Datos que NO se tocan ni se suben a ningún lado

- `.env*`, `.secrets/`, service accounts, API keys, contraseñas: no leer,
  no pegar en chat, no incluir en contexto de tareas remotas.
- Planillas, exports o carpetas con datos reales de clientes (Excel, CSV,
  backups, `docs/` interna): no las leas, no las cites, no las subas.
- Si una tarea "en la nube" necesitara subir el repo, este repo NO se delega
  a la nube: trabajá solo en local.
- Si dudás si algo es sensible, lo es.

## Estilo

- Castellano rioplatense en comentarios y textos de UI ("vos", nunca "tú").
- Sin jerga marketinera en código, comentarios ni commits.
- Commits narrativos: qué pasó, por qué se decidió así, cómo se verificó.
