# Plan de ajustes del documento

**Para qué:** corregir la tesis con todas las observaciones de la entrega del 50%, lo pedido en persona, lo que exige la rúbrica final y lo que cambió en el código. Al terminar no tiene que quedar ninguna corrección abierta (ver «Cobertura» al final).
**Cuándo:** hoy, viernes 16/10, en paralelo con los planes de cierre del código.
**Dónde se corre:** Claude Code en el proyecto `pfi-palomino-goncalves` (LaTeX). Lo corren **los dos**, cada uno sus bloques, en su propia sesión. Este archivo y `CORRECCIONES_E50.md` van en la raíz de ese repo.

## Cómo está ordenado para no depender uno del otro

1. **Cada capítulo tiene un solo dueño.** Nadie edita un capítulo del otro. Git combina solo los cambios de distintas partes de un mismo archivo, así que no hay conflictos aunque el documento esté en un único `.tex`.
2. **Lo nuevo va en archivos nuevos.** Brainstorming, negocio, motor de IA, multiplataforma, evaluación y conclusiones se escriben en `.tex` propios. Se enganchan al documento recién en la pasada final.
3. **Los cambios que tocan todo el documento van al final**, en una sola pasada de Romi: PyME → emprendedores, agrupar en «Antecedentes», renombrar el capítulo 6, dividir anexos, sacar menciones al 25% y «Estructura del documento». Así nadie tiene que esperar a nadie para empezar.
4. **Cada bloque técnico verifica el código por su cuenta** (el repo `proyecto-final-de-ingeniera` está abierto en el mismo VS Code, solo lectura) antes de afirmar que algo está implementado. No hay que esperar a un informe común.

**Las únicas esperas que quedan, al final de la noche:**
- Tomi escribe las Conclusiones (T-D6) cuando Romi le avisa que subió R-D6.
- Romi hace la pasada final (R-D7) cuando Tomi le avisa que subió T-D6.


**Estado del repo al 16/10, 22:40 (después de R-D1)**
- El PR #4 de Tomi ya había agrupado Marco Teórico y Estado del Arte en un capítulo «Antecedentes» (`chapter01-antecedentes.tex`) y renombrado el capítulo de tecnologías a `chapter04-metodologia.tex`. C-15 y el renombre de C-13 están **hechos**: la pasada final solo los verifica.
- Para que Tomi y Romi no editen el mismo archivo, `chapter01-antecedentes.tex` se divide en dos archivos incluidos (`chapter01-marco.tex` y `chapter01-estado.tex`), sin cambiar la estructura del capítulo. Lo hace Romi antes de R-D2 (ver «R-D1b»).
- El resumen, el abstract y los agradecimientos están vacíos («Completar»). Se escriben en la pasada final.
- La carátula de `cover/` es un PDF: Romi la revisa a mano y, si tiene el título viejo o los datos de la tutora, la regenera por fuera.

## Dueños

| Parte del documento | Dueño |
|---|---|
| Preámbulo, `main.tex`, carátula, encabezado, índices | Romi |
| Cap. 1 Introducción | Tomi |
| Cap. «Antecedentes» (`chapter01-antecedentes.tex`): encabezado e introducción del capítulo | Romi |
| Marco Teórico (`chapter01-marco.tex`, incluido desde Antecedentes) | Tomi |
| Estado del Arte (`chapter01-estado.tex`, incluido desde Antecedentes) | Romi |
| Cap. 4 Análisis estratégico (entrevistas, personas, FODA, Porter, Triple P) | Tomi |
| Anexos de entrevistas | Tomi |
| Cap. 5 Requerimientos (RF, RNF, HU, CU, trazabilidad, mockups) | Romi |
| Cap. Metodología (`chapter04-metodologia.tex`, todo lo que existe hoy, incluida la sección del proveedor de IA) | Romi |
| Resumen, abstract y agradecimientos (`summary.tex`, `abstract.tex`, `acknowledgments.tex`) | Romi (en la pasada final) |
| Anexo A Cronograma | Romi |
| Archivos nuevos: `brainstorming.tex`, `anexo-brainstorming.tex`, `negocio-modelo.tex` | Romi |
| Archivos nuevos: `negocio-financiero.tex`, `motor-ia.tex`, `multiplataforma.tex`, `evaluacion-modelos.tex`, `conclusiones.tex` | Tomi |

## Reglas para todos los prompts

Se aplican a cada bloque. Pegá este párrafo al principio de cada prompt (ya está incluido en los de abajo como «[Reglas]»):

```
[Reglas] Antes de empezar, hacé git pull. Editá solo las partes del documento que este bloque indica: no toques capítulos ni archivos de otro bloque. Mostrame todos los cambios del bloque juntos (texto exacto, archivo y sección) y esperá mi confirmación una sola vez. Redacción impersonal, formato de citas de la bibliografía existente, cada referencia nueva verificada. Usá "emprendedores", nunca "PyME", en todo el texto nuevo. Sin menciones a entregas parciales ("25%", "50%", "presente entrega"). Las correcciones con su detalle están en CORRECCIONES_E50.md. Si algo de lo que vas a afirmar depende del código, verificalo antes en proyecto-final-de-ingeniera (main, solo lectura): lo que no esté implementado va como limitación o trabajo futuro. Al terminar: compilá, hacé un commit por corrección con su ID (por ejemplo "doc(C-07): RNF en forma afirmativa"), git push, y cerrá con el mensaje "Listo. Avisale a <compañero> que haga git pull."
```

En «compañero» va «Tomi» en los bloques de Romi y «Romi» en los de Tomi.

## Orden de la noche

**Grupo 1: no dependen del código. Arrancan ya, en paralelo con la programación.**

| Romi | Tomi |
|---|---|
| R-D1 Formato | T-D1 Entrevistas |
| R-D2 Estado del Arte | T-D2 Viabilidad económico-financiera |
| R-D3 Brainstorming | T-D3 Marco Teórico |
| R-D4 Modelo de negocio y marca | |

**Grupo 2: dependen del código. Arrancan cuando cada uno terminó su parte del código** (no hace falta esperar al otro).

| Romi | Tomi |
|---|---|
| R-D5 Requerimientos | T-D4 Introducción y objetivos |
| R-D6 Metodología, arquitectura, seguridad y validación | T-D5 Motor de IA, multiplataforma y evaluación |

**Grupo 3: cierre.**

1. Romi termina R-D6 → avisa a Tomi.
2. Tomi hace T-D6 Conclusiones → avisa a Romi.
3. Romi hace R-D7 Pasada final.
4. Los dos leen el PDF completo.

---

## Bloques de Romi

### R-D1 — Formato (Grupo 1)

```
[Reglas]
Contexto: corregimos el formato de la tesis según CORRECCIONES_E50.md. Este bloque solo toca el preámbulo, main.tex, la carátula, el encabezado y los índices; no toca el texto de los capítulos.

Primero mostrame cómo está organizado el proyecto: archivo principal, un .tex por capítulo o no, dónde están carátula, encabezado, índices, anexos y bibliografía. Guardá ese mapa en ESTRUCTURA.md en la raíz (lo usan los demás bloques).

Tareas:
1. C-20 Título: "Del dato a la decisión: prototipo de recomendaciones accionables para e-commerce de emprendedores en Buenos Aires (2026)", en la carátula, \title y los metadatos del PDF.
2. C-21 Encabezado de página en máximo 2 líneas (título abreviado y autores).
3. C-22 Lista de figuras y lista de tablas inmediatamente después del índice general (sacar la lista de tablas de después de la bibliografía).
4. C-23 Tablas numeradas con el número de capítulo en arábigo y el de tabla en romano ("Tabla 3.I"), reiniciando por capítulo. Si hay referencias a tablas escritas a mano en el texto, listámelas pero no las cambies (cada dueño las corrige en su capítulo; la pasada final las verifica).
5. C-24 Carátula: sacar la afiliación y la dirección postal de la tutora.

Criterios de aceptación: el PDF compila y ESTRUCTURA.md está subido.
```

### R-D1b — Separar Antecedentes en dos archivos (antes de R-D2, 5 minutos)

```
[Reglas]
Contexto: chapter01-antecedentes.tex contiene las secciones Marco Teórico (la edita Tomi) y Estado del Arte (la edito yo). Para que no editemos el mismo archivo en paralelo, separalas sin cambiar el contenido ni la estructura del capítulo.

Tareas:
1. Dejar en chapter01-antecedentes.tex el \chapter, su texto introductorio (si existe) y dos \input: chapters/chapter01-marco y chapters/chapter01-estado.
2. Mover el contenido de la sección Marco Teórico a chapter01-marco.tex y el de Estado del Arte a chapter01-estado.tex, tal cual.
3. Compilar y verificar que el PDF quede idéntico (mismas páginas, mismas referencias).
4. Actualizar ESTRUCTURA.md.

Al terminar, el mensaje final es: "Listo. Avisale a Tomi que haga git pull: el Marco Teórico ahora está en chapters/chapter01-marco.tex."
```

### R-D2 — Estado del Arte (Grupo 1)

```
[Reglas]
Contexto: corregimos el Estado del Arte (solo chapters/chapter01-estado.tex). Ver CORRECCIONES_E50.md: C-17, C-18, C-25.

Tareas:
1. C-17 Texto introductorio entre «Estado del Arte» y su primera subsección, y en cualquier título de la sección pegado a su subtítulo.
2. Plan 01 de arquitectura, punto 2: fila "Soporte multiplataforma" en la tabla comparativa (Decidata: Tiendanube y WooCommerce por adaptadores, si el código lo confirma).
3. C-25 Estrategia del Océano Azul, antes de la conclusión: Curva de Valor (lienzo estratégico) contra las alternativas de la tabla comparativa y Matriz ERIC (eliminar, reducir, incrementar, crear). Proponé los factores a partir de la tabla.
4. C-18 Renombrar "Análisis comparativo y brecha identificada" a "Conclusión del Estado del Arte" y reescribirla para que deje claro el diferencial, la importancia del tema y el aporte a la sociedad.
```

### R-D3 — Brainstorming (Grupo 1)

```
[Reglas]
Contexto: nos exigieron en persona sumar un brainstorming al user research (C-26). Se hizo como sesión de validación y evolución del motor con perfiles de la industria: las dos autoras/es como facilitadores, 2 desarrolladores backend de Mercado Libre, un devops, un product manager y una persona de marketing. Te paso la guía y los resultados. Escribí todo en dos archivos nuevos, brainstorming.tex y anexo-brainstorming.tex. No los incluyas en main.tex (eso se hace en la pasada final).

Tareas:
1. brainstorming.tex: objetivo, por qué brainstorming frente a encuestas (coherente con la sección de metodología de las entrevistas), participantes por rol, agenda y dinámica (ideación individual, agrupación por afinidad, votación por puntos, subgrupos "Datos y reglas" y "Valor e impacto", matriz impacto/esfuerzo).
2. Presentarlo con honestidad como validación del diseño, no como su origen, con la fecha real.
3. Resultados: ideas que coinciden con lo implementado (verificalo en el código), nuevas incorporadas, trabajo futuro y descartadas con su justificación.
4. Cerrar mencionando que el user research usa dos herramientas (entrevistas y brainstorming).
5. anexo-brainstorming.tex: tabla completa de ideas y las fotos del tablero y de la matriz (te paso las imágenes).
```

### R-D4 — Modelo de negocio y marca (Grupo 1)

```
[Reglas]
Contexto: la rúbrica final pide un modelo de negocio claro (cómo el producto se vuelve sustentable y rentable) y el desarrollo del logo con la explicación de su diseño (N-1, N-3 en CORRECCIONES_E50.md). El documento no tiene nada de esto. Escribí en un archivo nuevo, negocio-modelo.tex. No lo incluyas en main.tex.

Tareas:
1. Business Model Canvas con los nueve bloques, coherente con la Triple P, el FODA y Porter del capítulo 4 (leelos, no los modifiques).
2. Marca Decidata: nombre, propuesta de valor en una frase, tono, paleta alineada con Nimbus y logo con la explicación de su diseño (te paso el logo).
3. Una frase que remita a la viabilidad económico-financiera (la escribe Tomi en negocio-financiero.tex).
```

El logo lo diseñás vos fuera de Claude Code; el bloque solo lo documenta.

### R-D5 — Requerimientos (Grupo 2)

```
[Reglas]
Contexto: corregimos el capítulo de Requerimientos (solo ese capítulo). El corrector puso "No cumple" por la redacción de los RNF (C-07), y hay que reflejar lo que cambió en el código. Usá la parte "Documento" de los planes en proyecto-final-de-ingeniera/docs/planes/: 02 (puntos 6 a 14), 01 (puntos 4 a 6), 03 (puntos 2 a 4) y 04 (puntos 2 a 4).

Tareas:
1. C-07 Todos los RNF arrancan con "El sistema debe...". RNF-05 y RNF-14 en forma positiva (por ejemplo "El sistema debe almacenar y usar el Client Secret exclusivamente en el backend"). RNF-10, RNF-11 y RNF-12 salen de la tabla y se renumera (Tomi los pasa a Componentes Excluidos en la Introducción).
2. RNF nuevos: integración con Tiendanube y WooCommerce por adaptadores, API sin estado y escalable, procesamiento en segundo plano y sincronización periódica, cifrado de credenciales, aislamiento entre tiendas, verificación de webhooks, anonimización de datos enviados a proveedores de IA y cobertura mínima de pruebas. RNF-03 (Nimbus) solo como cumplido si el código lo confirma, agregando que la misma interfaz se usa en el panel de WordPress.
3. HU-02, HU-06, HU-07, HU-08, HU-09 y HU-10 según lo implementado; HU-04 a trabajo futuro.
4. RF-16 como scoring con Thompson Sampling; RF-08 con los umbrales dinámicos reales; RF-19 con Gemini (no GPT-4o-mini).
5. CU-01 (cifrado del token y verificación de la sesión), CU-02 (sincronización incremental, backoff, límites de tasa, ingesta de todas las órdenes y filtro de pagadas en el motor), CU-03, CU-05, CU-06, CU-07 (firma, borrado real, anonimización, registro de auditoría), CU-08 y un CU nuevo: conectar una tienda WooCommerce.
6. Tabla de trazabilidad actualizada.
7. Documentar lo que existe y no estaba especificado: deshacer, configuración de cuándo ver el impacto (modo inmediato solo para demo) y exportación a CSV.
8. Mockups: agregar las capturas de las pantallas finales junto a los mockups (te las paso), incluidas ejecución inferida, esperado contra real, preparación anticipada, fechas clave y la app dentro de WordPress.
```

### R-D6 — Metodología, arquitectura, seguridad y validación (Grupo 2)

```
[Reglas]
Contexto: el capítulo 6 ("Tecnologías y Arquitectura de Red") pasa a ser "Metodología de desarrollo" en la pasada final. En este bloque escribí el contenido en ese mismo capítulo, organizado en tres secciones: "Metodología de desarrollo" (nueva, al principio), "Arquitectura y tecnologías utilizadas" (el contenido actual, corregido) y "Validación del sistema" (nueva, al final). El renombre del capítulo lo hace la pasada final. Usá la parte "Documento" de los planes 01 (puntos 7, 8, 10, 10b, 11 y 12), 03 y 04, docs/seguridad.md y la auditoría (docs/auditoria/).

Tareas:
1. C-13 Metodología de desarrollo: ciclo de vida y metodología con su justificación (desarrollo incremental por pilares con planes por etapa, una rama por etapa, criterios de aceptación, revisión cruzada, congelamiento; los planes en docs/planes/ como evidencia), coherente con el cronograma del Anexo A.
2. A-1 Proveedor de IA: Gemini (modelo configurable por variable de entorno), no GPT-4o-mini; reescribir la tabla comparativa con esa decisión (plan 02, punto 15).
3. Arquitectura: monorepo frente a monolito; arquitectura de ejecución (API sin estado, worker, pg-boss, trabajos programados, diagrama y tabla comparativa de colas); base de datos en Neon (tabla comparativa, ramas por desarrollador, regla de migraciones); contenerización y despliegue (Railway como hecho solo si está desplegado); ngrok solo para desarrollo; entorno con docker compose. Actualizar las figuras de arquitectura de red y de componentes. Dejar una referencia a las secciones de Tomi (motor-ia.tex y multiplataforma.tex) sin repetirlas.
4. N-4 Diagrama entidad-relación con las tablas nuevas (snapshots, pronósticos, segmentos, bandit, privacidad, credenciales de WooCommerce), tomado del schema.prisma real.
5. Frontend con Nimbus (plan 04): paquetes, tema oscuro y la misma app en Tiendanube y WordPress.
6. Sección de seguridad (plan 03 completo): STRIDE incluyendo prompt injection con las mitigaciones implementadas (C-10, leelas en el código); autenticación de las dos plataformas; aislamiento entre tiendas; AES-256-GCM y protección de datos en reposo (C-11); gestión de secretos; webhooks; privacidad y Ley 25.326 citada (finalidad, consentimiento, derechos de acceso, rectificación y supresión) (C-12); anonimización para Gemini; endurecimiento; limitaciones. Donde hoy hay medidas sueltas, dejar solo una referencia a esta sección.
7. C-08 Validación del sistema: estrategia de pruebas, herramientas, meta de 85% y cobertura real (corré npm run test:coverage en backend y frontend), con conclusiones y decisiones a partir de los resultados. Dejar una referencia a evaluacion-modelos.tex de Tomi.

Al terminar, el mensaje final es: "Listo. Avisale a Tomi que haga git pull y que ya puede escribir las Conclusiones."
```

### R-D7 — Pasada final (Grupo 3, cuando Tomi avisa que subió las Conclusiones)

```
[Reglas]
Contexto: todo el contenido está corregido. Esta pasada hace los cambios que tocan todo el documento, engancha los archivos nuevos y verifica contra las correcciones y la rúbrica final. Acá sí podés editar cualquier capítulo.

Tareas:
1. C-15 Verificar que el capítulo "Antecedentes" agrupa Marco Teórico y Estado del Arte (ya lo hizo el PR #4) y que tiene texto introductorio antes de la primera sección (C-17); si no, escribirlo.
2. C-13 Verificar que el capítulo de metodología se titula "Metodología de desarrollo" (el archivo ya se renombró en el PR #4; revisar el \chapter) e incluir motor-ia.tex y multiplataforma.tex en "Arquitectura y tecnologías utilizadas" y evaluacion-modelos.tex en "Validación del sistema".
3. Incluir brainstorming.tex en el capítulo 4, después de las entrevistas; crear el capítulo "Desarrollo del negocio" con negocio-modelo.tex y negocio-financiero.tex (proponé dónde va); incluir conclusiones.tex como capítulo antes de la bibliografía.
4. C-06 Un anexo por entrevista (hoy todas en el Anexo B) y el anexo del brainstorming.
5. C-19 Sacar del Anexo A y de todo el documento las menciones a entregas parciales y el lenguaje de trabajo en progreso; reescribir los desvíos del cronograma en pasado.
6. C-27 Reemplazar "PyME"/"PyMEs" (todas las grafías) por "emprendedores" en todo el documento, revisando la concordancia caso por caso. Lo que dice una fuente citada no se cambia: se deja el término original con una frase puente. Mostrame primero la lista de casos dudosos.
7. C-01 "Estructura del documento" al final de la Introducción: abre nombrando el trabajo y recorre cada capítulo y cada anexo con referencias cruzadas (\ref), diciendo qué contiene.
8. Búsqueda de restos: "PyME", "GPT-4o", "OpenAI" (salvo en la comparación de proveedores), "25%", "50%", "presente entrega", "TODO", nombres reales de entrevistados, referencias rotas (??), referencias a tablas escritas a mano.
9. Verificar: índice con máximo 4 niveles; listas de figuras y tablas completas; tablas en formato "3.I"; encabezado en 2 líneas; cada figura y tabla citada en el texto; cada cita en la bibliografía y viceversa.
9b. Escribir el resumen (summary.tex), el abstract en inglés (abstract.tex) y los agradecimientos (acknowledgments.tex), que hoy dicen "Completar". El resumen y el abstract en un párrafo de 200 a 300 palabras cada uno, con palabras clave, a partir del documento final.
10. Recorrer la sección "Cobertura" de PLAN_AJUSTES_DOCUMENTO.md: marcar cada corrección con el commit que la resuelve y listar las que falten.
11. Recorrer la rúbrica final ítem por ítem y decir qué sección la cubre.

Criterios de aceptación: ninguna corrección abierta en "Cobertura" y el PDF compila sin referencias rotas.
Al terminar, el mensaje final es: "Listo. Avisale a Tomi que haga git pull y lea el PDF completo."
```

---

## Bloques de Tomi

### T-D1 — Entrevistas (Grupo 1)

```
[Reglas]
Contexto: el corrector marcó como críticos varios puntos de las entrevistas (C-03, C-04, C-05). Este bloque toca solo el capítulo 4 (Análisis estratégico) y los anexos de entrevistas. No dividas los anexos (eso se hace en la pasada final).

Tareas:
1. C-04 Anonimizar a los cinco entrevistados como E01 a E05 en el capítulo 4 (sección de entrevistas, personas, mapas de empatía, FODA, Porter, Triple P) y en los anexos: sin nombres, apellidos, nombres de tiendas ni datos que permitan identificarlos. Mantener solo rubro, antigüedad, plataforma y tamaño. Agregar la mención del consentimiento informado para uso académico y publicación anonimizada. Listame primero todas las apariciones. Si un nombre real aparece en un capítulo que no es tuyo, anotalo en una lista y pasámela (lo corrige su dueño o la pasada final).
2. C-03 Declarar que el muestreo fue por conveniencia (contactos de las autoras) y el vínculo personal con E01 como posible sesgo, en la metodología de las entrevistas y en sus limitaciones.
3. C-05 Reemplazar "disposición a pagar comprobada empíricamente" (y frases equivalentes) por "indicios de disposición a pagar en la muestra entrevistada", aclarando que no es generalizable.
4. Cada anexo de entrevista con un encabezado que diga perfil, fecha y duración.
5. Plan 01 de arquitectura, punto 3: donde se mencionan la estrategia DA1 y el poder de negociación de proveedores, indicar que el prototipo la implementa con adaptadores (si el código lo confirma).
6. Texto introductorio en cualquier título del capítulo pegado a su subtítulo (C-17).
```

### T-D2 — Viabilidad económico-financiera (Grupo 1)

```
[Reglas]
Contexto: la rúbrica final pide un análisis con al menos dos escenarios y al menos una herramienta financiera (VAN, TIR, payback o punto de equilibrio) (N-2 en CORRECCIONES_E50.md). El documento no lo tiene. El precio y la estrategia comercial están en la Triple P del capítulo 4. Escribí en un archivo nuevo, negocio-financiero.tex. No lo incluyas en main.tex.

Tareas:
1. Costos: infraestructura (Railway, Neon, Gemini, dominio), comisiones de las tiendas de aplicaciones, desarrollo y mantenimiento. Precios verificados con fecha y fuente.
2. Ingresos: precio de la Triple P y supuestos de adopción en tres escenarios (pesimista, base y optimista), justificados con el estado del arte y las entrevistas.
3. Flujo de fondos a 3 años, VAN con la tasa de descuento justificada, TIR, payback y punto de equilibrio en cantidad de suscriptores.
4. Sensibilidad breve y conclusión sobre la viabilidad.
5. Una frase que remita al modelo de negocio (lo escribe Romi en negocio-modelo.tex).
```

### T-D3 — Marco Teórico (Grupo 1)

```
[Reglas]
Contexto: el motor pasó a un sistema híbrido de tres capas (modelos predictivos, reglas por rubro y bandit) más un LLM que solo redacta. Corregimos el Marco Teórico (solo chapters/chapter01-marco.tex; si todavía no existe, hacé git pull y esperá a que Romi lo separe). Usá la parte "Documento" del plan 02 de IA, puntos 1 a 5 y 18.

Tareas:
1. C-17 Texto introductorio entre el título del capítulo y su primera sección, y en cualquier título del capítulo pegado a su subtítulo.
2. Plan 02, puntos 1 a 5: enfoque híbrido en tres capas, pronóstico de series temporales (Holt-Winters, SES, demanda intermitente, WAPE frente a MAPE), segmentación (RFM, k-means, silueta, CLV), bandit (exploración y explotación, Thompson Sampling) y ciclo de retroalimentación (inferencia de la ejecución, esperado contra real, correlación indicativa).
3. Plan 02, punto 18: estrategia de datos (seed con patrones semanales, fechas clave, feedback simulado y ramas de Neon). La validación con datos reales va en evaluacion-modelos.tex, no acá.
4. El marco teórico describe sin opinar ni decidir: las elecciones van en Metodología.
```

### T-D4 — Introducción y objetivos (Grupo 2)

```
[Reglas]
Contexto: corregimos la Introducción (solo ese capítulo). Ver C-02 y C-17 en CORRECCIONES_E50.md. No escribas "Estructura del documento" (la hace la pasada final).

Tareas:
1. C-17 Texto introductorio entre el título del capítulo y "Objetivo General".
2. C-02 A cada objetivo específico, una cota numérica que el prototipo demuestre, usando solo números medidos en proyecto-final-de-ingeniera (docs/evaluacion-modelos.md, reportes de cobertura, tiempos del motor y de la API). No prometer aumento de ventas.
3. Alcance: WooCommerce incluido (si el código lo confirma); Shopify, modo independiente y migración a AWS como trabajo futuro; despliegue en Railway solo si está hecho. Definir "emprendedores" (tamaño, canal Tiendanube o WooCommerce, rubros) para que el segmento quede delimitado.
4. C-07 (parte): sumar a Componentes Excluidos lo que hoy figura como RNF-10, RNF-11 y RNF-12 ("no se contempla...").
```

### T-D5 — Motor de IA, multiplataforma y evaluación (Grupo 2)

```
[Reglas]
Contexto: escribí en tres archivos nuevos, motor-ia.tex, multiplataforma.tex y evaluacion-modelos.tex. No los incluyas en main.tex (la pasada final los ubica en el capítulo de Metodología de desarrollo). Usá la parte "Documento" del plan 02 (puntos 16 y 17) y del plan 01 (punto 9), docs/evaluacion-modelos.md y la auditoría (secciones 2.2, 2.3, 2.6 y 2.7).

Tareas:
1. motor-ia.tex: diagrama de las tres capas y del flujo de datos, cada algoritmo con sus parámetros, tabla de umbrales por rubro y calendario de fechas clave. C-09 Scoring: variables de cada regla, umbrales por rubro y constantes fijas, regla de actualización del bandit, orden de priorización (nivel de la regla, muestra de Thompson, puntaje de la regla), umbrales dinámicos de RF-08 y cómo se evalúa el motor.
2. multiplataforma.tex: modelo canónico, interfaz de adaptadores, comparación Tiendanube frente a WooCommerce (autenticación, acceso desde el panel, webhooks, rubro) y tabla de alternativas de segunda plataforma.
3. evaluacion-modelos.tex (C-14):
   a. Cómo se genera el seed y qué proporción es sintética y cuál real.
   b. Resultados sobre datos sintéticos y sobre el dataset real (WAPE frente a las dos referencias, k y silueta), con honestidad, incluido dónde el modelo no supera la referencia.
   c. La circularidad declarada: el bandit aprendió de tasas de éxito fijadas en el seed; demuestra el mecanismo, no el aprendizaje en uso real.
   d. Limitaciones y cómo se validaría con uso real.
```

### T-D6 — Conclusiones generales (Grupo 3, cuando Romi avisa que subió R-D6)

```
[Reglas]
Contexto: el documento no tenía conclusiones generales (C-16). La rúbrica final pide que estén ligadas directamente al objetivo general, a los objetivos específicos y al alcance. Escribí en un archivo nuevo, conclusiones.tex (la pasada final lo incluye). Leé todo el documento actualizado antes de escribir.

Tareas:
1. Retomar el objetivo general y cada objetivo específico con su métrica: cumplido, parcialmente cumplido o no, con el número obtenido.
2. Aportes principales: motor híbrido, ciclo de retroalimentación, multiplataforma, seguridad y privacidad.
3. Limitaciones: datos sintéticos, muestra de entrevistas, bandit sin uso real y todo lo que el código no llegó a implementar.
4. Trabajo futuro: Shopify, modo independiente, migración a AWS, acciones con un clic, validación con tiendas reales, HU-04 e ideas del brainstorming clasificadas como futuras.

Al terminar, el mensaje final es: "Listo. Avisale a Romi que haga git pull y arranque la pasada final."
```

---

## Cobertura: dónde se resuelve cada corrección

| ID | Corrección | Bloque |
|---|---|---|
| C-01 | Estructura del documento | R-D7 |
| C-02 | Objetivos cuantificables | T-D4, T-D6 |
| C-03 | Muestreo por conveniencia y sesgo | T-D1 |
| C-04 | Anonimizar entrevistas y consentimiento | T-D1 |
| C-05 | «Comprobada empíricamente» | T-D1 |
| C-06 | Un anexo por entrevista | R-D7 (encabezados: T-D1) |
| C-07 | Redacción de RNF | R-D5, T-D4 |
| C-08 | Cobertura de pruebas | R-D6 (código: R0, R3, T5) |
| C-09 | Scoring y umbrales | T-D5 |
| C-10 | Prompt injection | R-D6 (código: T3) |
| C-11 | Datos en reposo | R-D6 (código: R1, R-OPS) |
| C-12 | Ley 25.326 | R-D6 (código: R1) |
| C-13 | Capítulo Metodología de desarrollo | R-D6, R-D7 |
| C-14 | Datos sintéticos y validación real | T-D3, T-D5 (código: T4) |
| C-15 | Antecedentes | Hecho en PR #4 · R-D1b · R-D7 verifica |
| C-16 | Conclusiones generales | T-D6, R-D7 |
| C-17 | Textos introductorios | R-D2, T-D1, T-D3, T-D4, R-D7 |
| C-18 | Conclusión del estado del arte | R-D2 |
| C-19 | Remanentes del 25% | R-D7 |
| C-20 | Título | R-D1 |
| C-21 | Encabezado | R-D1 |
| C-22 | Listas de figuras y tablas | R-D1 |
| C-23 | Tablas «3.I» | R-D1, R-D7 |
| C-24 | Carátula de la tutora | R-D1 |
| C-25 | Océano Azul | R-D2 |
| C-26 | Brainstorming | R-D3, R-D7 |
| C-27 | PyME → emprendedores | R-D7 (texto nuevo ya sin «PyME» por regla) |
| A-1 | Gemini en lugar de GPT-4o-mini | R-D5, R-D6 |
| A-2 | Motor de tres capas | T-D3, T-D5 |
| A-3 | Infraestructura real | R-D6 |
| A-4 | Cifrado y seguridad implementados | R-D6 |
| A-5 | Nimbus | R-D5, R-D6 |
| A-6 | Tienda de desarrollo | T-D5 |
| A-7 | Pruebas | R-D6 |
| N-1 | Modelo de negocio | R-D4 |
| N-2 | Viabilidad económico-financiera | T-D2 |
| N-3 | Branding y logo | R-D4 |
| N-4 | Diagrama entidad-relación actualizado | R-D6 |
| N-5 | Resumen, abstract y agradecimientos vacíos | R-D7 |
| N-6 | Carátula en PDF con título viejo o datos de la tutora | Romi, a mano (regenerar `cover/`) |
