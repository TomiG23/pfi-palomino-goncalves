# Material para R-D3 — Brainstorming

Fuente: guía de la sesión («Brainstorming PFI: guía de la sesión», doc del 10/10/2026) y análisis de resultados del 13/10/2026, reclasificado con la auditoría del código del 13/10 y el cierre del 16/10.

**Fecha de la sesión: 10/10/2026.** Las fotos de las 3 cartulinas van en `images/brainstorming/`. Las 21 ideas de abajo están transcriptas de la foto de la matriz.

## Datos de la sesión

- Formato: presencial, 60 minutos, sin computadoras (post-its, marcadores y 3 cartulinas). Grabada con consentimiento para citar en la tesis.
- 7 personas:

| Persona | Rol en la sesión |
|---|---|
| Tomás Gonçalves (autor) | Facilitador: lee las preguntas, da la palabra, frena las críticas. No vota. |
| Romina Micaela Palomino Gayoso (autora) | Tiempos y registro: pega y ordena los post-its, cuida la grabación. No vota. |
| Desarrollador backend junior (Mercado Libre) | Participante |
| Desarrollador backend semi senior (Mercado Libre) | Participante; estima el esfuerzo en la matriz |
| DevOps | Participante; estima el esfuerzo en la matriz |
| Product Manager | Participante |
| Marketing | Participante |

En la tesis los invitados van **por rol, sin nombre** (mismo criterio de anonimización que las entrevistas, C-04).

## Por qué brainstorming y no encuestas

- La sección de metodología de las entrevistas ya argumenta contra los instrumentos cerrados: una encuesta solo mide lo que el investigador ya pensó.
- Se buscaba divergencia (ideas nuevas) y validación cruzada con perfiles técnicos y de producto que no son el usuario final, como complemento de las entrevistas a comerciantes.
- Ningún invitado tiene tienda: todas las preguntas se formularon en tercera persona, sobre las User Personas.

## Dinámica

| Tiempo | Bloque | Qué se hizo |
|---|---|---|
| 0:00–0:05 | Bienvenida | Aviso de grabación. Cada invitado: nombre, rol y «Si fueras dueño de una tienda online, ¿qué es lo primero que mirarías cada mañana?» |
| 0:05–0:09 | Contexto y reglas | Problema en 3–4 frases, User Personas y mockup del panel, reglas (cantidad antes que calidad, no criticar, ideas locas bienvenidas, construir sobre las ideas de otros, hablar de a uno) |
| 0:09–0:21 | Pregunta 1 | 4 min de escritura individual en silencio (mínimo 3 post-its), ronda de lectura y pegado |
| 0:21–0:33 | Pregunta 2 | Igual que la 1 |
| 0:33–0:43 | Pregunta 3 (3a y 3b) | 4 min de escritura y 6 de ronda |
| 0:43–0:49 | Agrupar | Afinidad: los post-its de la cartulina 1 pasan a la 2, se juntan los que dicen lo mismo y se nombra cada grupo |
| 0:49–0:53 | Votar | Votación por puntos: cada invitado anota sus 3 grupos y después marca a la vez. Los autores no votan, para no sesgar |
| 0:53–0:58 | Matriz | Los grupos más votados pasan a la cartulina 3, matriz impacto/esfuerzo. El grupo decide el impacto; los dos desarrolladores y el DevOps estiman el esfuerzo |
| 0:58–1:00 | Cierre | Fotos de las 3 cartulinas |

### Preguntas tal como se hicieron

1. **Confianza y acción:** ¿Cómo podríamos lograr que un comerciante que no sabe de análisis de datos confíe en una recomendación y la ejecute? (Apoyo: ¿qué tendría que decir la recomendación para que una User Persona le haga caso? Si la app se equivoca una vez, ¿qué tendría que pasar para que siga confiando?)
2. **Nuevas oportunidades:** Hoy la app avisa 4 cosas: que un producto se queda sin stock, que algo se dejó de vender, que un precio quedó viejo y que un cliente dejó de comprar. ¿Qué otras cosas debería avisarle? (Ejemplos para destrabar: Día de la Madre, Hot Sale, aguinaldo, cambio de temporada, feriado largo.)
3. **Cómo funcionaría el motor:**
   - 3a. Si ustedes tuvieran que programar el aviso de «este producto se va a quedar sin stock», ¿cómo lo harían? ¿Qué datos usarían y cada cuánto lo revisarían?
   - 3b. La app le recomienda algo al comerciante y él lo hace. ¿Cómo se da cuenta la app de si le sirvió o no, para recomendar mejor la próxima vez?

La pregunta 2 dice abiertamente lo que la app ya hacía: la sesión se presenta como **validación y evolución** del diseño, no como su origen.

## Resultados: 21 ideas en la matriz impacto/esfuerzo

Clasificación contra lo que el código tiene al cierre (verificar en `proyecto-final-de-ingeniera` antes de escribir).

### Coinciden con lo ya implementado (validan el diseño)

| Idea | Cuadrante | Dónde está en el sistema |
|---|---|---|
| Pedir feedback al usuario | Alto impacto / poco esfuerzo | Marcar como ejecutada, deshacer, «No fui yo» (HU-07) |
| Efemérides: avisos de fechas como Black Friday | Alto / poco | Recomendación de fecha clave con calendario por rubro (HU-06) |
| Efemérides: qué productos promocionar | Alto / medio | Fecha clave sobre los productos más vendidos de los últimos 28 días |
| Promos | Alto / poco | Rotación en caída y fechas clave sugieren promociones |
| Segmentar clientes en alto, medio y bajo | Alto / poco | Segmentación RFM con k-means y CLV (HU-10) |
| Explicar por qué la recomendación | Alto / medio | «Por qué te lo recomendamos» y explicación de la priorización (bandit) en el detalle |
| Productos estacionales | Medio / mucho | Pronóstico Holt-Winters con estacionalidad semanal y preparación anticipada (HU-02) |
| Mostrar casos de éxito | Alto / poco | Vista de impacto: esperado contra real (HU-08) |
| Que analicemos si implementó la recomendación con lo que está dentro de la plataforma | Alto / poco | Inferencia de la ejecución con snapshots de stock y precio (HU-07). Verificar que funcione con datos reales (arreglo T2 de Tomi) |

### Implementadas en parte

| Idea | Qué hay | Qué falta |
|---|---|---|
| Diferenciar notificaciones de recomendaciones | Prioridades urgente, atención y oportunidad | Separar avisos («te quedás sin stock») de sugerencias («hacé una promo») |
| Pop-up para decir si le fue bien | Marcar como ejecutada; impacto medido automáticamente | Preguntar al comerciante si le sirvió |
| Botón para realizar la acción | CTA en cada tarjeta | Que el botón aplique el cambio en la tienda (requiere permisos de escritura) → trabajo futuro: «acciones con un clic» |
| Dar solo recomendaciones que generen buen profit | El bandit prioriza lo que funcionó en la tienda | Filtrar por margen (requiere el costo del producto) |
| No hablar en imperativo | El LLM redacta los mensajes | Verificar el system prompt de Gemini |
| Adaptar el tono según el rubro | El LLM recibe el rubro como contexto | Verificar el system prompt de Gemini |

### Trabajo futuro o descartadas

| Idea | Decisión | Motivo |
|---|---|---|
| Mostrar un estado general (todas las pérdidas y todas las ganancias) | Trabajo futuro | Solo si se plantea como impacto acumulado de las recomendaciones; si no, se vuelve un dashboard descriptivo |
| Feedback de acciones hechas fuera de la plataforma (ej. publicar en redes) | Trabajo futuro | No observable desde la API de la plataforma |
| Productos en tendencia del rubro que la tienda no tiene | Trabajo futuro | Requiere datos de mercado externos; el alcance limita las fuentes a la plataforma |
| Enviar reporte mensual | Trabajo futuro | Bajo impacto en la matriz |
| Dar recomendaciones a fin de cada semana | Trabajo futuro | Bajo impacto en la matriz |
| Cambio de tendencia en reviews negativas | Descartada | Bajo impacto; la API de Tiendanube no expone reviews de forma nativa |

## Lectura para la tesis

- Más de un tercio de las ideas coincidió con lo que el sistema ya hacía, propuesto por personas que no conocían la implementación: valida el diseño.
- La pregunta 3 (perfiles técnicos) llegó por su cuenta a los mismos mecanismos del motor: mirar la tendencia de ventas contra el stock para el quiebre, y medir si después de la acción mejoró la métrica para aprender (ciclo de retroalimentación).
- Las ideas nuevas alimentan el trabajo futuro con justificación.
- Con las entrevistas, el user research usa dos herramientas, como pide la rúbrica final.
