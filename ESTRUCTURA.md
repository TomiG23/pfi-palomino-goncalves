# Estructura del proyecto LaTeX

## Archivo principal

`main.tex` — punto de entrada único. Contiene el preámbulo completo (paquetes, estilos, encabezado, fuentes) y los `\input{}` que ensamblan el documento.

## Páginas preliminares (orden en main.tex)

| Archivo | Contenido |
|---|---|
| `cover/caratula_nueva_Tesis.pdf` | Carátula oficial UADE (incluida con `\includepdf`; no editable como `.tex`) |
| `chapters/title.tex` | Página de título interna: título, autores, tutora, año, logo UADE |
| `chapters/summary.tex` | Resumen en español (`\chapter*{Resumen}`) |
| `chapters/abstract.tex` | Abstract en inglés (`\chapter*{Abstract}`) |
| `chapters/acknowledgments.tex` | Agradecimientos (`\chapter*{Agradecimientos}`) |

Después de los preliminares: `\tableofcontents`, `\listoffigures`, `\listoftables`.

## Encabezado y pie de página

Definidos en `main.tex` con el paquete `titleps`, estilo `ruled`:
- Encabezado izquierdo: logo UADE (`images/UADE_LARGE`)
- Encabezado centro: título abreviado + autores (2 líneas)
- Pie: número de página / total

## Capítulos (orden en `main.tex`)

| N.° | Archivo | Contenido | Dueño |
|---|---|---|---|
| 1 | `chapters/chapter00.tex` | Introducción: objetivos, alcance, estructura del documento | Tomi |
| 2 | `chapters/chapter01-antecedentes.tex` | Antecedentes: `\chapter` + texto introductorio + dos `\input` | Romi |
| — | `chapters/chapter01-marco.tex` | 2.1 Marco Teórico (incluido desde Antecedentes) | Tomi |
| — | `chapters/chapter01-estado.tex` | 2.2 Estado del Arte (incluido desde Antecedentes) | Romi |
| 3 | `chapters/chapter03-analisis-estrategico.tex` | Análisis Estratégico: entrevistas, personas, brainstorming, FODA, Porter, Triple P | Tomi |
| — | `chapters/brainstorming.tex` | Sesión de brainstorming (incluido desde cap. 3, entre Design Thinking y FODA) | Romi |
| 4 | `\chapter{Desarrollo del negocio}` *(declarado en `main.tex`)* | Capítulo sin archivo propio; incluye los dos archivos siguientes | Romi/Tomi |
| — | `chapters/negocio-modelo.tex` | Modelo de negocio + marca Decidata | Romi |
| — | `chapters/negocio-financiero.tex` | Viabilidad económico-financiera | Tomi |
| 5 | `chapters/chapter05-requerimientos.tex` | Requerimientos: RF, RNF, HU, CU | Romi |
| 6 | `chapters/chapter04-metodologia.tex` | Metodología de desarrollo: arquitectura, seguridad, motor de IA, multiplataforma, validación | Romi |
| — | `chapters/motor-ia.tex` | Arquitectura del motor de IA (incluido en cap. 6, antes de Validación) | Tomi |
| — | `chapters/multiplataforma.tex` | Arquitectura multiplataforma (incluido en cap. 6, antes de Validación) | Tomi |
| — | `chapters/evaluacion-modelos.tex` | Evaluación de modelos y datos sintéticos (incluido al final de cap. 6) | Tomi |
| 7 | `chapters/conclusiones.tex` | Conclusiones: cumplimiento de objetivos, limitaciones, trabajo futuro | Tomi |

## Bibliografía

`biblio.bib` — base de datos BibTeX. Backend: `biber`, estilo: `iso-authoryear`.

## Anexos (orden en `chapters/appendix/annex.tex`)

| Letra | Archivo | Contenido | Dueño |
|---|---|---|---|
| A | `chapters/appendix/schedule_of_activities.tex` | Cronograma de Proyecto (Gantt + justificación de desvíos) | Romi |
| B | `chapters/appendix/interviews.tex` → `interview1.tex` | Entrevista E01 — Indumentaria bordada | Tomi |
| C | `chapters/appendix/interviews.tex` → `interview2.tex` | Entrevista E02 — Indumentaria | Tomi |
| D | `chapters/appendix/interviews.tex` → `interview3.tex` | Entrevista E03 — Gastronomía | Tomi |
| E | `chapters/appendix/interviews.tex` → `interview4.tex` | Entrevista E04 — Cómics y mangas | Tomi |
| F | `chapters/appendix/interviews.tex` → `interview5.tex` | Entrevista E05 — Indumentaria estampada | Tomi |
| G | `chapters/appendix/anexo-brainstorming.tex` | Brainstorming: material de la sesión | Romi |

El archivo `chapters/appendix/annex.tex` define el comando `\anexo{}` y redefine `\appendixname`.

## Configuración

`config/environment.tex` — entornos adicionales.
`config/symbols.tex` — símbolos y macros propios.

## Figuras e imágenes

`images/` — logos e imágenes inline.
`gantt/` — imagen del Gantt.
`figures/` — figuras LaTeX.
`tables/` — tablas LaTeX.

## Numeración de tablas

Formato: `\arabic{chapter}.\Roman{table}` → "Tabla 3.I", "Tabla 3.II", etc. Reinicia por capítulo (automático en clase `book`).
