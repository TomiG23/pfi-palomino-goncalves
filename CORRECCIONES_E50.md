# Correcciones entrega 50% (E50) — PFI Recomendaciones accionables para e-commerce

**Origen:** feedback de Santiago Gabriel Martín (mail «RE: E50 PFI - SAB Curso 568650», 13/10/2026), grupo SB22.
**Documento corregido:** `E50_PFI_2026_-_Palomino_-_Goncalves_...pdf` (las páginas citadas refieren a esa versión).
**Rúbricas de referencia:** rúbrica 50% y **rúbrica final** (evaluación del informe final, 100 pts). Columna «Rúbrica final» = ítem donde impacta en la nota final.
**Dónde se corrige:** repo LaTeX del documento.

## Cómo usar este archivo

1. Esperar el `.md` de la **auditoría de código**. Las tareas marcadas **🔧 AUDIT** dependen de lo que diga: puede ser documentar algo que ya existe, o implementarlo primero y después documentarlo.
2. Resolver en orden de prioridad: **P0 críticas** (subrayadas por el docente), **P1 obligatorias** por la rúbrica final, **P2 forma/mejoras**.
3. Al cerrar una tarea, marcar `[x]` y referenciar el ID en el commit (ej. `fix(C-07): anonimizar entrevistas E01–E05`).

**Advertencia general del docente:** el trabajo usa **datos sintéticos**. Deberán justificar cómo se generan y qué proporción representan, y validar los resultados con **datos reales**. Métricas solo sobre datos sintéticos no demuestran funcionamiento real. (Ver C-14).

## Cruce con la auditoría de código (13/10/2026, commit `9baeba3`)

Fuente: `auditoria-tesis.md`. Resumen: el **código está bastante más avanzado que el documento** en IA y seguridad, pero hay defectos que hoy impiden afirmar cosas que el documento (o las correcciones) necesitan afirmar. Regla: **no documentar como funcionando algo que la auditoría marca como roto**; o se arregla el código, o se documenta como limitación.

### A. El documento describe algo distinto de lo que hay en el código

| # | Documento E50 | Código real | Qué hacer en el documento |
|---|---|---|---|
| A-1 | LLM: **GPT-4o-mini de OpenAI** (RF-19, 6.3.5, tabla comparativa de LLM) | **Google Gemini** vía `@google/genai`, modelo `GEMINI_MODEL` (por defecto `gemini-3.5-flash-lite`), salida JSON con `responseSchema`, plantilla de respaldo si falla | Reescribir RF-19, 6.3.5 y la justificación: la tabla ya compara contra Gemini 2.5 Flash-Lite, rehacer la elección. Afecta C-10. |
| A-2 | Motor: «scoring con actualización basada en outcomes» (Sutton y Barto), sin detalle | **Tres capas:** (1) pronóstico Holt-Winters / SES / promedio móvil con validación WAPE, y segmentación RFM + k-means (silueta) + CLV; (2) seis reglas con umbrales por rubro; (3) **bandit Thompson Sampling** Beta(α, β) por tienda y tipo, con priors por rubro | Resuelve gran parte de C-09. Marco Teórico: agregar pronóstico de series de tiempo, segmentación RFM/k-means y multi-armed bandit (Sutton y Barto cap. 2 sigue siendo cita válida). |
| A-3 | Infraestructura: ngrok, comparación con Vercel | Neon (PostgreSQL serverless, ramas main/dev), worker separado con cola **pg-boss**, Docker/compose, Railway **planificado sin desplegar**, DolarApi | Actualizar Cap. 6 / C-13 y diagramas de arquitectura. **No** afirmar que está desplegado. |
| A-4 | Protección de token: solo «queda en el backend» | Token OAuth **cifrado AES-256-GCM** (IV aleatorio, clave en `TOKEN_ENCRYPTION_KEY`), JWT de Nexo, aislamiento por tienda, rate limit, helmet, anonimización para el LLM | Resuelve gran parte de C-11 (ver bloqueantes B-3). |
| A-5 | Nimbus como sistema de diseño (RNF-03, 8 menciones) | **Nimbus no está instalado**: CSS y componentes propios | Decisión D-2. |
| A-6 | Tienda de desarrollo real que valida la integración (pág. 9) | La rama dev de Neon solo tiene **3 tiendas del seed**; main no se revisó | Verificar main antes de afirmarlo. |
| A-7 | Sin pruebas documentadas | **134 tests** backend con `node:test`, todos pasan; frontend sin tests; sin medición de cobertura | Base para C-08. |

### B. Bloqueantes: código a corregir antes de poder escribir la corrección

| # | Defecto (auditoría) | Bloquea | Esfuerzo estimado |
|---|---|---|---|
| B-1 | Webhooks leen `x-linked-secret` en vez de `x-linkedstore-hmac-sha256` → **todo webhook real da 401**, incluidos los de privacidad | C-12 (Ley 25.326 / derechos de borrado), C-11, sync en tiempo real | Bajo (cambiar encabezado + test del encabezado) |
| B-2 | `privacy_requests.result` guarda **datos personales en claro** y nunca se purgan; `customers-redact` no borra `customer_segments`; `customer/deleted` borra órdenes | C-11, C-12 | Bajo/medio |
| B-3 | Sync incremental **no genera snapshots** (compara después de actualizar) → la ejecución inferida no funciona con datos reales | C-14 (validación con datos reales), C-02 si un objetivo habla de inferencia | Medio |
| B-4 | `low_rotation` nunca se evalúa (falta `expected_forecast`) → el bandit no aprende ese tipo | C-09, C-14 | Bajo |
| B-5 | Sin cobertura medida y **frontend sin tests**; sin tests de aislamiento entre tiendas | C-08 (85%) | **Alto** (sobre todo el frontend) |
| B-6 | Prompt: los nombres de los 5 productos destacados y de producto/variante entran como texto libre; el sanitizador apunta a datos personales, no a inyección | C-10 | Bajo (delimitar/escapar campos de la tienda, límite de longitud, test) |
| B-7 | Badge «Generado por IA» aparece aunque el texto sea la plantilla de respaldo | Honestidad del Cap. 6 / demo | Bajo |

### C. Lo que la auditoría aporta para escribir (números y hechos verificados)

- **C-09 scoring:** features por regla (tabla 2.6 de la auditoría), umbrales por rubro (`rubroParams.js`), constantes fijas (2.3), regla de actualización del bandit (+1 α si ejecutada con impacto > 0; +1 β si sin impacto, sin dato o vencida), orden: nivel de la regla → muestra de Thompson → puntaje. Umbrales dinámicos RF-08 = inactividad por tienda (max(piso del rubro, mediana de recompra × 2)) y horizonte de pronóstico.
- **C-14 datos sintéticos:** 3 tiendas, 30 productos, 67 variantes, 470 clientes, 10.490 órdenes, 05/04–06/10/2026; seed determinista (verificado con hash). **100% sintético hoy; 0% real.** El bandit se entrenó con 491 recomendaciones simuladas y sus tasas de éxito salen de `SUCCESS_RATES` fijadas en el seed → **es exactamente la circularidad que marca el docente**; las 143 «inferidas» del seed son etiquetas al azar. Declararlo tal cual.
- **Métricas honestas del pronóstico (WAPE por variante, modelo / plano):** gastronomía 53,0% / 55,5%; indumentaria 142,4% / 141,9%; electrónica 118,8% / 118,3%. Solo en gastronomía el modelo supera la referencia. Horizonte real 17 días (gastronomía, indumentaria) y 21 (electrónica), no 14.
- **Segmentación (silueta):** 0,640 (k=3), 0,513 (k=3), 0,517 (k=4).
- **Rendimiento:** el motor tarda 14–29 s por tienda sin Gemini → candidato para C-02.
- **C-13 metodología:** desarrollo por etapas planificadas (planes 00–04: general, arquitectura, IA, seguridad, frontend) con ramas por etapa y PR a `main` (17 PR). Los planes **no están en el repo** (`docs/planes/`): subirlos sirve de evidencia.

### D. Decisiones: qué decían los planes (00–04) y qué encontró la auditoría

| # | Tema | Plan | Auditoría 13/10 | Estado |
|---|---|---|---|---|
| D-1 | WooCommerce | Arq E4, **Tomi**, control sáb 17/10. La prueba de concepto se hacía **fuera del repo principal** hasta cerrar Arq E3 | Nada en ninguna rama remota | [ ] Confirmar con Tomi si existe en local, en otro repo o en la PoC. Recorte previsto en el plan: plugin mínimo (iframe + token firmado) |
| D-2 | Nimbus (RNF-03) | Plan 04, **Romi**: E1 semana 1, E2 semana 2, E3–E4 semana 3. El plan solo permite recortar «detalles visuales en pantallas secundarias» | No instalado, sin rama | [ ] Verificar si hay trabajo local sin pushear |
| D-3 | Datos reales | El plan aceptaba seed sintético con feedback simulado y marcado (IA E7), tienda de desarrollo de indumentaria + una de otro rubro, y cargar historial sobre una tienda real conectada (E7 tarea 6) | 100% sintético; la tienda de prueba también tiene datos generados por script | [ ] La advertencia del docente va **más allá del plan**: hace falta trabajo nuevo (C-14) |
| D-4 | Cobertura 85% | No está en ningún plan | 134 tests backend, sin medir cobertura, frontend sin tests | [ ] Definir alcance |
| D-5 | Despliegue | Arq E5, Romi, semana 3: **Railway** (api, worker, frontend) + Neon main; AWS documentado como camino de migración. **Es lo primero que se recorta** si no alcanza el tiempo, y se puede hacer en la semana de documento | Dockerfiles y `docs/deploy.md`, sin desplegar; nginx sin proxy de `/api` | [ ] Ver nota sobre WiFi de UADE |

- **Fechas del plan:** congelamiento del código el **21/10** y entrega del 90% el **28/10** (el plan dice que es un supuesto a ajustar).
- **Planes en el repo:** subirlos a `docs/planes/` (la auditoría no los encontró). Sirven de evidencia para el capítulo de Metodología (C-13).
- **Prompts de documento de cada plan:** cada plan trae su parte «Documento» con las secciones a escribir. Se usan junto con este archivo (ver F).

### E. Orden de trabajo propuesto

1. Decisiones D-1 a D-5.
2. Código: B-1, B-2, B-4, B-6, B-7 (bajos, desbloquean C-10, C-11, C-12) → B-3 → B-5.
3. Validación con datos reales (C-14) y recolección de métricas (C-02, C-08).
4. Documento: P0 → P1 → P2, usando las secciones A y C como fuente.
5. Ajustar la clasificación de ideas del brainstorming (C-26) contra lo que realmente existe en el código.

### F. Partes «Documento» de los planes que cubren correcciones

| Plan | Secciones que pide | Correcciones que cubre |
|---|---|---|
| 02 IA | Marco teórico: series temporales, RFM/k-means, bandit; ciclo de retroalimentación; HU-02/04/06/07/08/09/10; RF-16 con Thompson; 6.3.5 Gemini; arquitectura del motor; **evaluación de modelos con feedback simulado declarado** | C-09, C-14, A-1, A-2 |
| 03 Seguridad | Sección propia con STRIDE, autenticación, multi-tenancy, AES-256-GCM, secretos, webhooks, **Ley 25.326**, anonimización para Gemini; RNF-05/14 reescritos; CU-07 y CU-01 | C-07, C-11, C-12 (C-10 prompt injection **no está en el plan**: agregarlo a la tabla STRIDE) |
| 01 Arquitectura | Alcance con WooCommerce, fila multiplataforma en tabla comparativa, RNF-12, CU-02, CU de WooCommerce, arquitectura de ejecución, multiplataforma, contenerización, base en la nube, entorno de desarrollo, trabajo futuro | C-13, A-3, C-07 (RNF-12) |
| 04 Nimbus | 6.3.1, RNF-03, mockups y pantallas finales | A-5 |

Solo escribir lo que la auditoría confirme como implementado. Lo que no se haga queda como limitación o trabajo futuro.

---

## P0 — Críticas (subrayadas en el mail)

| ID | Tarea | Rúbrica final | Audit |
|---|---|---|---|
| C-01 | Sección «Estructura del documento» | Formalidades › Estructura | |
| C-02 | Objetivos específicos cuantificables | Hilo conductor / Conclusiones | 🔧 |
| C-03 | Declarar sesgo por vínculo personal y muestreo por conveniencia | Investigación › User research | |
| C-04 | Anonimizar entrevistados y consentimiento informado | Investigación › User research / Viabilidad legal | |
| C-05 | Suavizar «disposición a pagar comprobada empíricamente» | Investigación › User research | |
| C-06 | Un anexo por entrevista | Formalidades › Estructura | |
| C-07 | Redacción de RNF | Desarrollo funcional | |
| C-08 | Meta de cobertura de tests (85%) | Desarrollo funcional › Pruebas | 🔧 |
| C-09 | Documentar scoring (RF-16) y umbrales dinámicos (RF-08) | Calidad técnica › Tendencias / Datasets | 🔧 |
| C-10 | Prompt injection en la integración con el LLM (el código usa **Gemini**, no GPT-4o-mini: ver A-1) | Calidad técnica › Arquitectura | 🔧 |
| C-11 | Protección de datos en reposo (token OAuth, clientes, pedidos) | Calidad técnica › Arquitectura | 🔧 |
| C-12 | Ley 25.326 (consentimiento, finalidad) | Investigación › Viabilidad legal | 🔧 |
| C-13 | Capítulo «Metodología de desarrollo» con estructura canónica | Desarrollo funcional › Metodología | 🔧 |
| C-14 | Datos sintéticos: generación, proporción y validación con datos reales | Calidad técnica › Uso de datasets / Desarrollo funcional › Resultados de pruebas | 🔧 |

### C-01 — Estructura del documento
- [ ] Agregar `1.4 Estructura del documento` al final de la Introducción (hoy termina en 1.3.2 Componentes Excluidos).
- [ ] Abrir nombrando el PFI y recorrer cada capítulo con referencias cruzadas (`\ref{}`).
- [ ] Presentar también los anexos (cronograma y cada entrevista) y qué contienen.
- [ ] Actualizar si cambia la numeración por C-15 (Antecedentes) y C-06 (anexos).

### C-02 — Objetivos específicos cuantificables (Sección 1.2, pág. 6)
- [ ] Agregar a cada uno de los 4 objetivos una cota o umbral medible.
- [ ] **Criterio propio:** usar métricas que el prototipo pueda demostrar (latencia máxima de la API, tiempo de generación de recomendaciones, % de recomendaciones evaluadas como útiles por comerciantes, % de cobertura de tests). **No** prometer «incremento de ventas X%»: el prototipo no puede probarlo y se vuelve en contra en la defensa.
- [ ] Los números salen de la auditoría y de las pruebas (C-08, C-14). Las Conclusiones (C-16) deben retomarlos uno por uno.

### C-03 — Sesgo de muestreo (págs. 18, 87, 102)
- [ ] Declarar explícitamente que el muestreo fue **por conveniencia** (contactos de las autoras).
- [ ] Declarar el vínculo personal con la entrevistada B.1 y las referencias a amistades como posible sesgo.
- [ ] Ubicarlo en la metodología del user research (Sección 4.1) y en limitaciones.

### C-04 — Anonimización y consentimiento (Sección 4.1 pág. 19, Anexo B págs. 89–118)
- [ ] Reemplazar nombres reales por **E01–E05** en todo el documento (incluida «Shakira (nombre real)», pág. 114).
- [ ] Eliminar o genericizar nombres comerciales de las tiendas («tienda de indumentaria», etc.).
- [ ] Mantener solo perfil no identificable (rubro, antigüedad, plataforma, tamaño).
- [ ] Agregar mención de consentimiento informado (verbal/escrito) para uso académico y publicación anonimizada.
- [ ] Revisar transcripciones: nombres propios mencionados al pasar, ciudades, cuentas de redes.

### C-05 — Sobre-generalización (págs. 29 y 37)
- [ ] Reemplazar «disposición a pagar comprobada empíricamente» por algo como «indicios de disposición a pagar en la muestra entrevistada», aclarando que no es generalizable.

### C-06 — Anexos de entrevistas
- [ ] Dividir Anexo B en un anexo por entrevista (ej. Anexo B: Entrevista E01, Anexo C: E02…, o B.1–B.5 como anexos de primer nivel según el instructivo).
- [ ] Actualizar referencias cruzadas y C-01.

### C-07 — Redacción de RNF (Tabla 5.2, págs. 42–43)
- [ ] Todos los RNF deben arrancar con **«El sistema debe…»** (RNF-01 «El frontend debe», RNF-02 «La comunicación… debe», RNF-04 «La aplicación embebida debe», RNF-03, RNF-06 a RNF-08).
- [ ] RNF-05 y RNF-14 (forma negativa «no debe exponerse»): reescribir en positivo. Ej.: «El sistema debe almacenar y utilizar el Client Secret exclusivamente en el backend».
- [ ] RNF-10, RNF-11, RNF-12 («no se contempla…»). **Criterio propio:** no son requerimientos sino exclusiones de alcance; moverlos a 1.3.2 Componentes Excluidos y renumerar RNF.
- [ ] Los RF (Tabla 5.1) ya cumplen; no tocar.

### C-08 — Cobertura de tests 🔧
- [ ] Definir meta formal de cobertura de pruebas unitarias y de integración: **mínimo 85%** según el docente, para backend (Node.js/Express) y frontend (React).
- [ ] Nombrar frameworks y herramientas (hoy el documento no menciona ninguno).
- [ ] Informar cobertura **real medida** (tabla o captura del reporte).
- *Nota:* el 85% no figura en las rúbricas del 50% ni en la final, pero está marcado como crítico. Se cumple igual.

### C-09 — Scoring y umbrales (págs. 7, 10–11, 41) 🔧
- [ ] RF-16: documentar features/variables, regla de actualización basada en outcomes (relación con Sutton y Barto), datos con que se ajusta y métricas de evaluación (accuracy, falsos positivos/negativos u otras).
- [ ] RF-08: documentar cómo se calculan los umbrales dinámicos.
- [ ] Documentar cómo se evalúa el motor de recomendaciones.
- [ ] Coherencia con el Marco Teórico: si no es RL en sentido estricto, decirlo con precisión.

### C-10 — Prompt injection (6.3.5, págs. 73–75) 🔧
- [ ] Reconocer el riesgo: el prompt incluye nombres de producto y rubro provenientes de la tienda.
- [ ] Documentar mitigaciones: sanitización/escape de campos de la tienda, separación de instrucciones de sistema y datos, límites de longitud, el LLM no ejecuta acciones (solo redacta texto), validación de salida.

### C-11 — Datos en reposo (pág. 39 RF-01, pág. 70) 🔧
- [ ] Explicar protección del token OAuth en PostgreSQL (cifrado en reposo/a nivel columna, gestión de claves).
- [ ] Control de acceso a la base y a datos de clientes y pedidos, además del aislamiento por `store_id`.
- [ ] Política de retención y borrado (vincula con webhooks de privacidad de Tiendanube).

### C-12 — Ley 25.326 (CU-07 pág. 56, 6.3.2 pág. 68) 🔧
- [ ] Citar la Ley 25.326 de Protección de Datos Personales.
- [ ] Tratar finalidad del tratamiento, consentimiento, minimización y derechos ARCO / borrado (RF-04, RF-12, RF-13).
- [ ] Ubicarlo en un apartado de viabilidad legal (suma en la rúbrica final, ítem Viabilidad legal).

### C-13 — Capítulo «Metodología de desarrollo» 🔧
- [ ] Renombrar Cap. 6 («Tecnologías y Arquitectura de Red») a **Metodología de desarrollo**, con las subsecciones:
  - Metodología de desarrollo (metodología/ciclo de vida usado y justificación)
  - Arquitectura y tecnologías utilizadas (contenido actual del Cap. 6)
  - Validación del sistema (pruebas, cobertura y resultados: C-08, C-14)

### C-14 — Datos sintéticos 🔧 (advertencia principal del mail)
- [ ] Explicar cómo se genera el dataset sintético (hoy solo aparece como «Tarea 9» en el cronograma): distribuciones, supuestos, semillas, herramienta.
- [ ] Indicar qué proporción representa frente a datos reales.
- [ ] Reconocer la circularidad (reglas y scoring calibrados sobre una distribución codificada por las autoras).
- [ ] **Validar con datos reales:** la tienda demo solo prueba la integración técnica. Opción a explorar: conectar la app a la tienda real de algún entrevistado (con consentimiento) o validar recomendaciones con comerciantes.
- [ ] Reportar métricas de acierto sobre datos reales.

---

## P1 — Obligatorias por la rúbrica final

| ID | Tarea | Rúbrica final |
|---|---|---|
| C-15 | Agrupar Marco Teórico y Estado del Arte bajo «2 Antecedentes» (2.1 y 2.2) | Formalidades › Estructura |
| C-16 | Capítulo de **Conclusiones generales** (hoy se pasa del Cap. 6, pág. 82, a Bibliografía) | Investigación › Conclusiones generales |
| C-17 | Texto introductorio entre título y primer subtítulo: «1 Introducción» y «1.1» (pág. 6), «2 Marco Teórico» y «2.1» (pág. 8); revisar en todos los capítulos | Formalidades › Estructura |
| C-18 | Renombrar 3.5 «Análisis comparativo y brecha identificada» a **«Conclusión del Estado del Arte»**, resaltando importancia del tema y aporte a la sociedad | Investigación › Estado del arte conclusión |
| C-19 | Eliminar remanente del 25% en Anexo A.2 (pág. 88): sacar toda mención a «entrega 25%/50%» y lenguaje de trabajo en progreso; reescribir desvíos en tiempo pasado y autocontenido | Hilo conductor / Formato |
| C-26 | **Agregar el brainstorming** al documento (exigido en persona) | Investigación › User research (pide 2+ herramientas: entrevistas + brainstorming) |
| C-27 | Coherencia del término **PyME → emprendedores** en el cuerpo (pedido en persona junto con el título) | Hilo conductor y coherencia |

- C-26:
  - [ ] Ubicarlo en el Cap. 4 (user research / Design Thinking), después de las entrevistas.
  - [ ] Presentarlo con honestidad como **sesión de validación y evolución del motor con perfiles de la industria** (no como origen del diseño): fecha real, participantes por rol (2 facilitadores, 2 back de Mercado Libre, devops, PM, marketing), agenda y dinámica (ideación individual, agrupación por afinidad, dot voting, subgrupos «Datos y reglas» y «Valor e impacto», matriz impacto/esfuerzo).
  - [ ] Resultados: ideas que coinciden con lo ya especificado (validan el diseño), nuevas incorporadas, trabajo futuro y descartadas con justificación.
  - [ ] Anexo propio con la tabla completa de ideas y fotos del tablero / matriz.
  - [ ] Material de base: la guía y los resultados armados en el chat «Brainstorming session agenda» del proyecto. La clasificación final de ideas se ajusta **después de la auditoría** 🔧.
  - [ ] Sumarlo a «Estructura del documento» (C-01), objetivos/conclusiones si corresponde, y a la lista de anexos.
- C-27 — **Decidido:** se reemplaza «PyME» por «emprendedores» en **todo el documento**, para que título y cuerpo queden alineados.
  - [ ] Reemplazar las ~157 menciones de «PyME/PyMEs» del PDF entregado (buscar en el repo `PyME`, `PYME`, `pyme`, `Pymes`, incluidos captions, tablas, macros y encabezado). Revisar caso por caso, no con reemplazo ciego (concordancia de género y número: «las PyMEs» → «los emprendedores»).
  - [ ] Definir «emprendedores» en el Alcance (tamaño, canal Tiendanube, rubros) para que el segmento siga delimitado.
  - [ ] Citas y estadísticas del Marco Teórico que hablan de PyMEs: se mantienen como dato de contexto (no se puede cambiar lo que dice la fuente), con un puente explícito a emprendedores.
  - [ ] Revisar personas, propuesta de valor, modelo de negocio y FODA/Porter, que hablan del segmento.
- C-16: las conclusiones deben retomar el objetivo general y cada objetivo específico con sus métricas (C-02).
- C-19: buscar en todo el repo `entrega`, `25%`, `50%`, `presente entrega`.

## P2 — Forma y mejoras

| ID | Tarea | Nota |
|---|---|---|
| C-20 | **Título definitivo:** «Del dato a la decisión: prototipo de recomendaciones accionables para e-commerce de emprendedores en Buenos Aires (2026)» | Sin siglas (pedido escrito y en persona) y con delimitación temporal. Actualizar carátula, encabezado (C-21), `\title` y metadatos PDF (`pdftitle`). Ver C-27 para el cuerpo. |
| C-21 | Encabezado de página: **máximo 2 líneas** (hoy 5: título en 3 líneas + 2 autores) | Título abreviado en 1 línea y autores en 1 línea (o solo título corto). Ajustar `fancyhdr`. |
| C-22 | Lista de Figuras (falta) y Lista de Tablas (está en pág. 86, después de la bibliografía) | `\listoffigures` y `\listoftables` inmediatamente después de `\tableofcontents`. |
| C-23 | Numeración de tablas: sección arábiga + número romano («Tabla 3.I») | `\renewcommand{\thetable}{\arabic{section}.\Roman{table}}` y resetear por sección. ⚠ Confirmar con el instructivo de PFI. |
| C-24 | Carátula: sacar afiliación y dirección postal de la tutora Macarena Pomerantz | ⚠ Confirmar con la plantilla oficial de carátula. |
| C-25 | Estrategia del Océano Azul: Curva de Valor (lienzo estratégico) y/o Matriz ERIC | No crítica: FODA y Porter ya cumplen la rúbrica final, pero esta la nombra explícitamente y suma al diferencial. Ubicar en Estado del Arte, antes de la conclusión. |

## Faltantes según la rúbrica final (no los marcó el 50%)

| ID | Falta | Ítem de la rúbrica final |
|---|---|---|
| N-1 | Modelo de negocio (no existe en el documento) | Desarrollo de negocio › Modelo de negocio |
| N-2 | Viabilidad económico-financiera con 2 escenarios y VAN/TIR/payback/punto de equilibrio (no existe) | Desarrollo de negocio › Viabilidad económico-financiera |
| N-3 | Logo y branding con explicación (no existe) | Desarrollo de negocio › Branding y logotipo |
| N-4 | Diagrama entidad-relación desactualizado (faltan las tablas nuevas) | Calidad técnica › Modelo de datos y arquitectura |

Plan de trabajo: `PLAN_AJUSTES_DOCUMENTO.md` (documento), `PLAN_CIERRE_TOMI.md` y `PLAN_CIERRE_ROMI.md` (código).

## Pendientes / a confirmar

- [ ] Conseguir el **instructivo de PFI** (formato y estructura) para confirmar C-23, C-24 y la estructura canónica de capítulos. La rúbrica final evalúa «según el instructivo».
- [x] Recibir el `.md` de la **auditoría de código** y mapear sus hallazgos (ver «Cruce con la auditoría»).
- [ ] Decidir la fuente de **datos reales** para C-14.
- [ ] Índice: la rúbrica final exige máximo 4 niveles de profundidad; verificar tras reestructurar (C-06, C-13, C-15).

## Checklist final contra la rúbrica final

- **Calidad técnica (25):** modelo de datos y arquitectura, justificación tecnológica, tendencias, producto entregado, **uso de datasets** (C-14).
- **Formalidades (20):** estructura (C-01, C-06, C-15, C-17), formato (C-20 a C-24), índice ≤ 4 niveles, redacción impersonal, citas, bibliografía, hilo conductor (C-02, C-19, C-27).
- **Desarrollo funcional (15):** requerimientos (C-07), diagramas, UX, **metodología** (C-13), **pruebas y resultados** (C-08, C-14).
- **Investigación (30):** marco teórico, estado del arte con tabla y herramienta de competencia (C-25), conclusión del estado del arte (C-18), user research (C-03 a C-06, C-26), **viabilidad legal** (C-12), dominio conceptual, **conclusiones generales** (C-16).
- **Negocio (10):** modelo de negocio, viabilidad económico-financiera (2 escenarios + VAN/TIR/payback/punto de equilibrio), branding y logo.
