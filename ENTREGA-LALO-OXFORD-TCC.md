# ENTREGA — Corrección de nomenclatura Oxford TCC (para Lalo)

**Fecha:** 2026-09-07 · **Commit:** `4aba60a` en `main`

## Qué cambió y por qué

El producto real que se entrega es **The Oxford TCC (Oxford Tutorial College
Certificate)**, de **Oxford International Education Group, Reino Unido** — NO el
"Oxford Test of English" de Oxford University Press. Todo el contenido usaba el
nombre y las mecánicas del examen equivocado (publicidad engañosa); se corrigió
la nomenclatura y se eliminaron las afirmaciones que pertenecían al otro examen.

## Qué re-importar en la plataforma MEVI

| Archivo | Acción |
|---|---|
| `data/course.json` | **Re-importar** (mini curso corregido) |
| `data/bank.json` | **Re-importar** (14 explicaciones de listening corregidas) |

## ⚠️ CAMBIO DE ESQUEMA — revisar la UI antes de re-importar

`meta.modules_overview` cambió:

- Ya **NO trae** los campos **`time`** ni **`parts`** (eran mecánica del examen
  oficial y se eliminaron). Cada ítem ahora es solo `{ icon, name, desc }`.
- Pasó de **4 módulos** (Speaking/Listening/Reading/Writing) a **5 habilidades**
  (uso del inglés, comprensión lectora, comprensión auditiva, expresión escrita,
  expresión oral).

**Si la UI renderiza columnas de tiempo/partes, hay que quitarlas.** Y si asume
exactamente 4 ítems, ahora son 5.

## Entregables Office

- **Nuevos (usar estos):** `entregables/Mini-curso-Oxford-TCC.docx` y
  `entregables/Examen-demo-Oxford-TCC.xlsx` — regenerados desde los JSON
  corregidos, mismo formato que los anteriores.
- **Viejos (OBSOLETOS, no usar ni distribuir):** `Mini-curso-Oxford.docx` y
  `Examen-demo-Oxford.xlsx`. Quedan en el repo solo mientras confirmas la
  migración; avisa para borrarlos.

## Reglas permanentes (copiadas del README)

> **⚠️ NOMENCLATURA** — Este contenido prepara para la **Certificación Oxford TCC
> (The Oxford Tutorial College Certificate)** de **Oxford International Education
> Group, Reino Unido**. NO es el Oxford Test of English de Oxford University
> Press. Son certificaciones distintas de instituciones distintas. Nunca uses
> "Oxford Test of English", "OTE" ni "Universidad de Oxford" en este contenido.

> **⚠️ CLAIMS PROHIBIDOS** — No agregues a este contenido afirmaciones sobre la
> mecánica del examen oficial: duraciones por módulo, número de módulos o
> partes, si es adaptativo, cuántas veces se reproduce el audio, cómo se
> puntúa, qué contenido "siempre aparece", ni comparaciones con Cambridge,
> TOEFL o IELTS. Solo se documenta lo verificado: 5 habilidades (uso del
> inglés, comprensión lectora, comprensión auditiva, expresión escrita,
> expresión oral), alineación al MCER, proceso en 3 etapas (diagnóstico →
> mock → certificación) y certificación vitalicia.
