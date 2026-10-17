# Estructura del proyecto LaTeX

## Archivo principal

`main.tex` — punto de entrada único. Contiene el preámbulo completo (paquetes, estilos, encabezado, fuentes) y los `\input{}` que ensamblan el documento.

## Carátula

`cover/284ef8e2-6978-41c9-868c-e8f23e315f9b.pdf` — PDF de la carátula oficial incluido con `\includepdf`. No es editable como `.tex`.

`chapters/title.tex` — página de título interna (titlepage LaTeX): título, autores, tutora, año, logo UADE.

## Encabezado y pie de página

Definidos en `main.tex` con el paquete `titleps`, estilo `ruled`:
- Encabezado izquierdo: logo UADE (`images/UADE_LARGE`)
- Encabezado centro: título abreviado + autores (2 líneas)
- Pie: número de página / total

## Índices

Generados en `main.tex` inmediatamente después de `\tableofcontents`:
- `\listoffigures` → Lista de Figuras
- `\listoftables` → Lista de Tablas

## Capítulos (orden en `main.tex`)

| Archivo | Contenido | Dueño |
|---|---|---|
| `chapters/chapter00.tex` | Cap. 1 Introducción | Tomi |
| `chapters/chapter01-antecedentes.tex` | Cap. 2 Antecedentes (2.1 Marco Teórico + 2.2 Estado del Arte) | Tomi / Romi |
| `chapters/chapter03-analisis-estrategico.tex` | Cap. 3 Análisis Estratégico (entrevistas, personas, FODA, Porter, Triple P) | Tomi |
| `chapters/chapter05-requerimientos.tex` | Cap. 4 Requerimientos (RF, RNF, HU, CU, mockups) | Romi |
| `chapters/chapter04-metodologia.tex` | Cap. 5 Metodología de desarrollo, arquitectura, seguridad, validación | Romi |

## Archivos nuevos pendientes (no incluidos en main.tex aún)

Se enganchan en la pasada final (R-D7):

| Archivo | Contenido | Dueño |
|---|---|---|
| `brainstorming.tex` | Sesión de brainstorming (cap. 3) | Romi |
| `negocio-modelo.tex` | Business Model Canvas + marca | Romi |
| `negocio-financiero.tex` | Viabilidad económico-financiera | Tomi |
| `motor-ia.tex` | Motor de IA (tres capas) | Tomi |
| `multiplataforma.tex` | Adaptadores Tiendanube / WooCommerce | Tomi |
| `evaluacion-modelos.tex` | Evaluación de modelos, datos sintéticos | Tomi |
| `conclusiones.tex` | Conclusiones generales | Tomi |
| `chapters/appendix/anexo-brainstorming.tex` | Anexo brainstorming | Romi |

## Anexos

`chapters/appendix/annex.tex` — define `\anexo{}` y redefine `\appendixname`; incluye:
- `chapters/appendix/schedule_of_activities.tex` — Anexo A: Cronograma (Romi)
- `chapters/appendix/interviews.tex` — Anexo B: Entrevistas (Tomi; se divide en R-D7)
  - `chapters/appendix/interview1.tex` … `interview5.tex`

## Bibliografía

`biblio.bib` — base de datos BibTeX. Backend: `biber`, estilo: `iso-authoryear`.

## Configuración

`config/environment.tex` — entornos adicionales (Abstract, Resumen, etc.).
`config/symbols.tex` — símbolos y macros propios.

## Figuras e imágenes

`images/` — logos e imágenes inline.
`figures/` — figuras LaTeX (ejemplo en `figures/example.tex`).
`tables/` — tablas LaTeX (ejemplo en `tables/table_example.tex`).

## Numeración de tablas

Formato: `\arabic{chapter}.\Roman{table}` → "Tabla 3.I", "Tabla 3.II", etc. Reinicia por capítulo (automático en clase `book`).
