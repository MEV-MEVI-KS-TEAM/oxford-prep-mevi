# oxford-prep-mevi · Banco de contenido

Banco de **contenido en crudo + entregables legibles** para la sección
**"Prepárate para tu Examen Oficial Oxford"** de la plataforma **MEVI**.

> **Este repo NO es la app web ni la landing.** Es solo el contenido (datos +
> documentos) para que un desarrollador —o Claude Code— lo implemente dentro de
> MEVI en su propia plataforma. No incluye `index.html`, CSS, JS ni dependencias.

El contenido cubre el **Oxford Test of English (OTE)** y se compone de dos piezas:

1. **Mini curso** — material de estudio por nivel (A1–B2): introducción, los 4
   módulos del examen, lecciones por habilidad, ejemplos, tips y estrategias.
2. **Examen demo** — banco de **129 preguntas** de práctica con nivel, habilidad,
   tipo, opciones, respuesta correcta y explicación.

---

## Estructura del repositorio

```
oxford-prep-mevi/
├── data/
│   ├── course.json   # Mini curso (estructura, lecciones A1–B2, estrategias)
│   └── bank.json     # Examen demo (129 preguntas)
├── entregables/
│   ├── Mini-curso-Oxford.docx   # course.json en Word, legible para humanos
│   └── Examen-demo-Oxford.xlsx  # bank.json en Excel (1 hoja por nivel + "Todas")
└── README.md
```

## Descripción de cada archivo

| Archivo | Formato | Para qué sirve |
|---|---|---|
| `data/course.json` | JSON | **Fuente de verdad** del mini curso. Esto es lo que se renderiza en MEVI. |
| `data/bank.json` | JSON | **Fuente de verdad** del examen demo (129 preguntas). Esto alimenta el quiz. |
| `entregables/Mini-curso-Oxford.docx` | Word | Mismo contenido de `course.json` pero formateado para leer/revisar sin tocar código. |
| `entregables/Examen-demo-Oxford.xlsx` | Excel | Mismo contenido de `bank.json` en tablas (hoja "Todas" + A1/A2/B1/B2). Útil para revisar preguntas. |

> Para **implementar en MEVI usa los JSON de `data/`**. Los archivos de
> `entregables/` son la versión legible para personas (revisión de contenido),
> no la fuente que consume el código.

---

## Esquema de `bank.json`

`bank.json` es un objeto cuyas llaves son los **niveles** (`"A1"`, `"A2"`,
`"B1"`, `"B2"`). El valor de cada llave es un **array de preguntas**:

```json
{
  "A1": [ { …pregunta… }, { …pregunta… } ],
  "A2": [ … ],
  "B1": [ … ],
  "B2": [ … ]
}
```

### Campos de cada pregunta

| Campo | Tipo | Descripción |
|---|---|---|
| `id` | string | Identificador único, p. ej. `"A1-1"`, `"B2-49"`. Formato `NIVEL-N`. |
| `level` | string | Nivel MCER: `"A1"`, `"A2"`, `"B1"` o `"B2"`. Coincide con la llave que la contiene. |
| `skill` | string | Habilidad evaluada: `"use_of_english"`, `"reading"`, `"listening"` o `"writing"`. |
| `type` | string | Tipo de pregunta: `"mcq"` o `"paragraph"` (ver aviso abajo). |
| `prompt` | string | Enunciado / consigna que ve el alumno. |
| `options` | string[] | Opciones de respuesta. **3 opciones** en las `mcq`; **vacío `[]`** en las `paragraph`. |
| `answer` | string | Texto de la opción correcta (debe coincidir con un elemento de `options`). **Vacío `""`** en las `paragraph`. |
| `explanation` | string | Explicación / retroalimentación (en español) que se muestra tras responder. |

### Ejemplo — pregunta de opción múltiple (`mcq`)

```json
{
  "id": "A1-1",
  "level": "A1",
  "skill": "use_of_english",
  "type": "mcq",
  "prompt": "Choose the correct option: \"I ____ a student.\"",
  "options": ["am", "is", "are"],
  "answer": "am",
  "explanation": "Con el sujeto «I» se usa «am» (verbo to be)."
}
```

### Ejemplo — pregunta de redacción (`paragraph`)

```json
{
  "id": "A1-9",
  "level": "A1",
  "skill": "writing",
  "type": "paragraph",
  "prompt": "Write 3–4 sentences about your family.",
  "options": [],
  "answer": "",
  "explanation": "Tarea de escritura. Revisa estructura, conectores y extensión solicitada. Un asesor evaluará coherencia y gramática."
}
```

### ⚠️ IMPORTANTE — hay DOS tipos de pregunta

El examen demo mezcla dos tipos y **deben tratarse distinto en la UI y en la calificación**:

| `type` | Qué es | `options` | `answer` | Calificación |
|---|---|---|---|---|
| `"mcq"` | Opción múltiple | 3 opciones | la opción correcta | **Automática.** Compara la elección del alumno contra `answer`. |
| `"paragraph"` | Redacción (Writing) | `[]` vacío | `""` vacío | **NO automática.** Es una tarea abierta que **evalúa un asesor humano**. No hay respuesta correcta única. |

- Hay **118 preguntas `mcq`** (autocalificables) y **11 preguntas `paragraph`** (Writing).
- En las `paragraph`, **no intentes autocalificar**: muestra el `prompt`, recibe el texto del alumno y márcalo como "pendiente de revisión por asesor". Usa la `explanation` como guía de evaluación.
- Las 11 tareas de Writing son: `A1-9`, `A1-10`, `A2-38`, `A2-39`, `A2-40`, `B1-78`, `B1-79`, `B1-80`, `B2-127`, `B2-128`, `B2-129`.

### 🧹 Nota de datos (limpieza recomendada)

Por un artefacto del origen de los datos, **9 preguntas `mcq` traen un valor
extra `"Multiple Choice"` como primer elemento de `options`** (no es una opción
real de respuesta; el `answer` correcto nunca es ese valor). Al renderizar,
**filtra ese valor**:

```js
const options = q.options.filter(o => o !== "Multiple Choice");
```

Afecta a: `B1-41`, `B1-68`, `B2-81`, `B2-83`, `B2-88`, `B2-101`, `B2-112`,
`B2-124`, `B2-126`. (En el `.xlsx` de `entregables/` ya viene corregido; en el
`bank.json` crudo sigue presente para que decidas cómo limpiarlo.)

---

## Conteo de preguntas

| Nivel | Preguntas |
|---|---|
| A1 | 10 |
| A2 | 30 |
| B1 | 40 |
| B2 | 49 |
| **Total** | **129** |

Distribución por habilidad (informativa): `use_of_english` 92 · `listening` 14 ·
`reading` 12 · `writing` 11. Por tipo: `mcq` 118 · `paragraph` 11.

> Nota: el examen demo es mayormente *Use of English*. **Speaking no tiene
> preguntas en el banco** porque es un módulo que se evalúa en vivo con un
> evaluador; sí aparece como habilidad en el mini curso (`course.json`).

---

## Esquema de `course.json`

Objeto con **4 llaves de nivel superior**: `meta`, `skills`, `lessons`, `strategies`.

### `meta` — portada y estructura del examen
```jsonc
{
  "title": "Prepárate para tu Examen Oficial Oxford",
  "subtitle": "…",
  "exam": "Oxford Test of English (OTE)",
  "intro": "Texto introductorio del curso…",
  "modules_overview": [               // los 4 módulos del examen
    { "icon": "🗣️", "name": "Speaking", "time": "~15 min", "parts": "4 partes", "desc": "…" },
    { "icon": "🎧", "name": "Listening", … },
    { "icon": "📖", "name": "Reading", … },
    { "icon": "✍️", "name": "Writing", … }
  ],
  "stages": ["Diagnóstico", "Mock (simulacro)", "Certificación"]
}
```

### `skills` — las habilidades que se evalúan
Objeto indexado por id de habilidad. Llaves: `use_of_english`, `reading`,
`listening`, `writing`, `speaking`. Cada una:
```jsonc
"use_of_english": {
  "name": "Uso del inglés (gramática)",
  "icon": "🧩",
  "why": "Por qué importa esta habilidad…"
}
```
> Los `skill` de `bank.json` usan estos mismos ids (excepto `speaking`, que no
> tiene preguntas). Úsalos para mostrar nombre + ícono legibles junto a cada pregunta.

### `lessons` — lecciones por nivel
Objeto indexado por nivel (`A1`, `A2`, `B1`, `B2`). Cada nivel:
```jsonc
"A1": {
  "title": "A1 · Usuario Básico",
  "goal": "Objetivo del nivel…",
  "topics": ["Verbo to be", "Presente simple…", …],   // temas que cubre
  "blocks": [                                          // una lección por habilidad
    {
      "skill": "use_of_english",        // referencia a skills[...]
      "title": "Verbo to be y presente simple",
      "content": "Explicación. Usa **doble asterisco** para negritas (Markdown).",
      "examples": ["I am a student.", "They are happy.", …],
      "tip": "Consejo accionable para el examen."
    }
    // … más blocks (reading/listening/writing/speaking según el nivel)
  ]
}
```
> El campo `content` (y a veces `tip`/`goal`) usa **`**negritas**` estilo Markdown**.
> Al renderizar, conviértelo a `<strong>` o equivalente.

### `strategies` — estrategias para el día del examen
```jsonc
{
  "title": "Estrategias generales para el día del examen",
  "items": [
    { "icon": "🧠", "title": "Reading y Listening son adaptativos", "text": "…" },
    …
  ]
}
```

---

## Cómo implementar en MEVI

Sugerencias para integrar este contenido (pensadas también para Claude Code):

### 1. Cargar los datos
- Lee `data/course.json` y `data/bank.json`. Son la **fuente de verdad**;
  no transcribas el contenido a mano.
- Trátalos como datos de solo lectura (puedes versionarlos aquí y actualizarlos
  desde este repo).

### 2. Renderizar el **mini curso** por nivel
- Pinta `meta` como portada: `title`, `subtitle`, `intro`, la tabla de
  `modules_overview` (los 4 módulos) y el camino `stages`.
- Muestra `skills` como tarjetas (ícono + `name` + `why`).
- Por cada nivel en `lessons` (A1 → A2 → B1 → B2): encabezado con `title`,
  `goal`, lista de `topics`, y luego cada `block` como una lección
  (agrupa/etiqueta por `skill` usando `skills[block.skill].name`).
- Convierte las **`**negritas**`** de `content`/`tip` a HTML.
- Cierra con la sección `strategies`.

### 3. Montar el **examen demo** respetando los DOS tipos
- Agrupa por nivel usando las llaves de `bank.json` (o filtra por `level`).
- Por cada pregunta, ramifica según `type`:
  - **`mcq`** → renderiza `prompt` + `options` (¡recuerda **filtrar `"Multiple Choice"`**!).
    Al responder, compara con `answer` para autocalificar y muestra `explanation`.
  - **`paragraph`** → renderiza `prompt` + un área de texto. **No autocalifiques.**
    Guarda la respuesta y márcala como "pendiente de revisión por asesor";
    usa `explanation` como rúbrica/guía.
- Muestra `skill` con su nombre legible (`course.json → skills[skill].name`) y,
  si quieres, el ícono.
- Sugerido: barra de progreso por nivel, y al final un resumen que **solo cuente
  las `mcq`** para el puntaje automático (las `paragraph` quedan aparte).

### 4. Pseudocódigo de referencia
```js
const bank = JSON.parse(fs.readFileSync("data/bank.json", "utf-8"));

for (const level of ["A1", "A2", "B1", "B2"]) {
  for (const q of bank[level]) {
    if (q.type === "mcq") {
      const options = q.options.filter(o => o !== "Multiple Choice");
      renderMultipleChoice(q.prompt, options, q.answer, q.explanation);
    } else if (q.type === "paragraph") {
      renderOpenWriting(q.prompt, q.explanation); // evaluación por asesor, sin auto-puntaje
    }
  }
}
```

---

*Contenido para uso educativo dentro de MEVI. Oxford Test of English es una marca
de la Universidad de Oxford; este repo es material de preparación, no un producto oficial.*
