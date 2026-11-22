# COMPROMISOS: documento contra código

Checklist de lo que afirma el documento y dónde está en el código. Cada ítem sale de una línea del documento.

| | |
|---|---|
| Fecha | 22/11/2026 |
| Documento | `main` en `2a62bd3` (PR #21) |
| Código | `main` en `8536edb` (PR #61) de `proyecto-final-de-ingeniera`, solo lectura; T-05 verificado en `ec89c85` |
| Conteo | 97 ítems: 80 Hecho · 14 Difiere · 1 Falta · 2 No verificado |

Rutas de código relativas a `backend/src/` salvo que digan otra cosa. «Demo local» = base local con el seed (`docs/evaluacion/k83-tipos-2026-11-22.md`): «hoy» es el seed hasta el 21/11 y «12/12» el ensayo con el seed hasta el 11/12. «Tienda conectada» = «Demo Del Dato a la Decisión» en producción. `force-rule` = `node scripts/force-rule.js --store <tienda> --rule <tipo> --confirm CONFIRMAR` (agregar `--fecha 2026-12-12` para el ensayo del 12/12).

Valores vigentes desde el 21/11, contrastados: quiebre con demanda mínima 14 d/5 u, 60 d/6 u, 90 d/2 u (M-02, Hecho); pantalla de métricas eliminada y RF-11 por `GET /api/products/metrics` (I-14, Hecho); deshacer revierte en Tiendanube (T-11, Hecho); pestañas Para hacer · En curso · Historial (I-01, Hecho).

## Motor y reglas

| id | Qué afirma el documento | Dónde está en el código | Cómo se dispara | Cómo mostrarlo en vivo | Estado | Nota |
|---|---|---|---|---|---|---|
| M-01 | Quiebre con stock > 0: días hasta agotarse ≤ alerta (3/14/21), urgente ≤ 1/5/7 (`motor-ia.tex:107`, `:163-165`; RF-08 `chapter05-requerimientos.tex:52`) | `services/engine/stockRule.js`; umbrales en `engine/config/rubroParams.js` | Motor después de cada sync | Demo local hoy: sola en las tres tiendas. `force-rule --rule stock_replenishment` | Hecho | |
| M-02 | Stock = 0: reposición solo si vendió `restock_min_units` en `restock_lookback_days` (14 d/5 u, 60 d/6 u, 90 d/2 u); la notificación sale siempre (`motor-ia.tex:107`, `:167-169`; `chapter05-requerimientos.tex:52`) | `stockRule.js` (filtro `outPendingQualified`); `rubroParams.js` | Variante agotada (motor o webhook) | Demo local hoy: Lucía, MariCafé, TecnoShop | Hecho | Coincide con los valores vigentes |
| M-03 | Preparación anticipada: pico 2 a 7 días adelante ≥ 130%, ≥ 1 u., sin baja confianza; atención a ≤ 3 días (`motor-ia.tex:109`, `:147`) | `engine/preparationRule.js`; `PREPARATION` en `rubroParams.js` | Motor | Demo local hoy: MariCafé sola; `force-rule --rule preparation` | Hecho | |
| M-04 | Fecha clave a 1-14 días, 5 productos más vendidos en 28 días; calendario y factores (`motor-ia.tex:111`, `:221-239`) | `engine/keyDateRule.js`; `engine/config/keyDates.js` (factores y ventanas coinciden) | Motor | Black Friday (27/11) en Lucía y TecnoShop y en la tienda conectada; Navidad en las tres el 12/12 | Hecho | |
| M-05 | Baja rotación: caída ≥ 25/30/30% en ventanas de 7/15/30 días (`motor-ia.tex:113`, `:171-173`; RF-10 `:58`) | `engine/rotationRule.js` | Motor | Demo local hoy: las tres | Hecho | |
| M-06 | Revisión de precio: 30/45/15 días sin cambio; dólar 15/10/5% (`motor-ia.tex:115`, `:175-177`; RF-20 `:89`) | `engine/priceRule.js`; `services/exchangeRate` | Motor y tipo de cambio diario | Demo local: Lucía y TecnoShop (recomendación y avisos) | Hecho | |
| M-07 | Riesgo de abandono: hasta 10 clientes inactivos de mayor CLV; urgente con cuartil superior (`motor-ia.tex:117`; RF-13 `:67`) | `engine/churnRule.js` | Motor | Demo local hoy: Lucía y MariCafé | Hecho | |
| M-08 | Fidelización: 3 clientes de mayor facturación en 90 días con correo; esperado = tasa de retorno de sus segmentos (`motor-ia.tex:119`; RF-25 `:104`) | `engine/loyaltyRule.js` (`expectedReturnRate`, `expected_return`) | Motor | `force-rule --rule loyalty_reward` (Lucía, TecnoShop) | Hecho | La marca A2-esperado (`motor-ia.tex:119`) quedó vieja: el esperado se guarda |
| M-09 | Cambio de estación: palabras clave, ≥ 3 u., 21 días antes a 42 después; puntaje `min(2s, 100)` (`motor-ia.tex:123`, `:147`; RF-24 `:101`) | `engine/seasonRule.js` | Motor en la ventana | 12/12: Lucía, «Cárdigan de hilo». Hoy sin candidatas (la ventana empieza el 30/11) | Difiere | El documento da el puntaje en escala 0-100; el código guarda `min(s/50, 1)`, en escala 0-1. Mismo orden |
| M-10 | Carrito abandonado: carritos de los últimos 7 días con un producto con stock (`motor-ia.tex:126`, `:150`; RF-31 `:127`) | `engine/abandonedCartRule.js`; `sync/abandonedCarts.js` | Motor | Demo local hoy: TecnoShop; 12/12: las tres | Hecho | WooCommerce no informa carritos (lo dice `conclusiones.tex:84`) |
| M-11 | Segunda compra: una sola compra hace 30 a 60 días (`motor-ia.tex:129`; RF-32 `:131`) | `engine/secondPurchaseRule.js` | Motor | 12/12: TecnoShop. Hoy sin candidatas | Hecho | Depende de la fecha de fin del seed |
| M-12 | Curva de talles rota: ≥ 4 talles, stock solo en extremos (`motor-ia.tex:132`; RF-33 `:135`) | `engine/sizeCurveRule.js` | Motor (solo indumentaria) | `force-rule --store Lucía --rule broken_size_curve` | Hecho | |
| M-13 | Día de baja demanda: ≤ 60% del promedio en 4 semanas (`motor-ia.tex:135`; RF-34 `:139`) | `engine/weekdayDemandRule.js` | Motor (solo gastronomía) | `force-rule --store MariCafé --rule weekday_demand` (lunes) | Hecho | |
| M-14 | Venta cruzada: equipo sin accesorios en 15 días (`motor-ia.tex:138`; RF-35 `:143`) | `engine/crossSellRule.js` | Motor (solo electrónica) | `force-rule --store TecnoShop --rule accessory_cross_sell` | Hecho | |
| M-15 | Cuatro reglas propias de un rubro; nueve generales (`motor-ia.tex:84`) | `RUBRO_ONLY_RULES` en `rubroParams.js`; `RUBROS_CON_ESTACIONALIDAD` en `seasonRule.js` | — | `force-rule` de una regla de otro rubro responde «sin candidatas» | Hecho | |
| M-16 | Tabla de umbrales por rubro (`motor-ia.tex:163-198`) | `THRESHOLDS` y `NEW_RULES` en `rubroParams.js` | — | Mostrar el archivo | Hecho | Todos los valores coinciden |
| M-17 | Rubro inferido: ≥ 3 coincidencias y 60% para un rubro; si no, genéricos con aviso (`motor-ia.tex:81`; RF-06 `:46`) | `engine/rubro.js` (`MIN_MATCHES = 3`, `MIN_SHARE = 0.6`) | Motor | Detalle: «No pudimos identificar el rubro de tu tienda…» | Hecho | |
| M-18 | Hasta 4 activas por tienda, una nueva por tipo por ejecución (`motor-ia.tex:272`; RF-23 `:98`) | `engine/candidates.js` (`MAX_ACTIVE_RECS = 4`, `seenTypes`) | Motor | Panel: nunca más de 4 en Para hacer | Hecho | El código suma un tope de 1 recomendación de cupón activa (`MAX_ACTIVE_COUPON_RECS`), que el documento no menciona |
| M-19 | Gemini 3.5 Flash-Lite, configurable con `GEMINI_MODEL` (`motor-ia.tex:322`; RF-19 `:85`) | `engine/messageGenerator.js` (`DEFAULT_MODEL`) | Motor al guardar | Mostrar el archivo | Hecho | |
| M-20 | Salida de Gemini: un número con % la descarta; código `^[A-Z0-9]{4,20}$`; sin enlaces; menciona el objetivo (`motor-ia.tex:324-326`; RF-19 `:85`) | `messageGenerator.js` (`PERCENT_PATTERN`, `COUPON_CODE_RE`, `validateOutput`) | Cada texto generado | `npm test` (pruebas del generador) | Hecho | |
| M-21 | Si falla la validación o el servicio, texto de plantilla; se registra la fuente (`motor-ia.tex:326`) | `messageGenerator.js` (`metadata.text_source`) | Cada texto generado | `mediciones-2026-11-22.md`: fuentes de texto por tienda | Hecho | En la medición del 22/11 la mayoría de los textos salió por plantilla (rechazos y errores 503 del proveedor) |

## Te lo deja listo

| id | Qué afirma el documento | Dónde está en el código | Cómo se dispara | Cómo mostrarlo en vivo | Estado | Nota |
|---|---|---|---|---|---|---|
| T-01 | Cupón en abandono, fidelización, carrito, segunda compra y venta cruzada; precio promocional en baja rotación, estación, fecha clave y curva de talles (`motor-ia.tex:282-283`) | `couponAction` / `promoPriceAction` en `churnRule.js`, `loyaltyRule.js`, `customerListCandidate.js`, `rotationRule.js`, `seasonRule.js`, `keyDateRule.js`, `sizeCurveRule.js` | Regla al generar | Botón de la tarjeta | Hecho | |
| T-02 | Día de baja demanda sin acción aplicable; stock, preparación y precio en texto (`motor-ia.tex:286`; RF-34) | `weekdayDemandRule.js` (sin acción) | — | Tarjeta de MariCafé | Hecho | |
| T-03 | Tabla de descuento: 10/20/30, 10/15/20, 5/10/15 según días sin vender (`motor-ia.tex:303-307`; RF-30 `:123`) | `services/apply/discountSuggestion.js` (`PROMO_PRICE_DISCOUNTS`) | Vista previa del precio promocional | Tienda conectada: «Aplicar» en una de baja rotación | Hecho | |
| T-04 | Piso de margen: costo × 1,10; si ni un descuento mínimo lo respeta, no propone (`motor-ia.tex:288`; RF-30) | `discountSuggestion.js` (`floor`, `margin_floor`) | Vista previa | Pruebas de `discountSuggestion` | Hecho | |
| T-05 | Sin costo cargado, la interfaz sugiere cargar el costo (`motor-ia.tex:288`) | El backend devuelve `warned: 'no_cost'`; `frontend/src/components/Modals.jsx` muestra «Este producto no tiene costo cargado en Tiendanube. Cargalo para que el descuento cuide tu margen.» | Vista previa sin costo | Tienda conectada: «Aplicar» en un producto sin costo | Hecho | Agregado el 22/11 (`fix/T11-aviso-costo`, merge `208369e`) |
| T-06 | Cupón: 10% editable, máximo sugerido 15% con aviso, vigencia 14 días (`motor-ia.tex:290`; RF-30) | `COUPON_DISCOUNT` en `rubroParams.js`; aviso «⚠ Descuento alto, revisá tu margen» en `frontend/src/components/Modals.jsx` | Vista previa del cupón | Tienda conectada: subir a 16% | Hecho | |
| T-07 | Cupón vigente equivalente: porcentaje igual o mayor y vigencia que cubra la ventana; si hay, se difunde ese (`motor-ia.tex:317`; RF-30) | `routes/apply.js` (`findEquivalentActiveCoupon`) | Vista previa del cupón | Tienda conectada con un cupón activo | Difiere | El código toma cualquier cupón porcentual activo y sin vencer; no compara porcentaje ni vigencia |
| T-08 | Antes de crear un cupón, el backend vuelve a consultar los cupones para no duplicar ni chocar con un código (`chapter04-metodologia.tex:350`) | `POST /:id/apply-coupon` en `routes/apply.js` | Confirmar el cupón | — | Difiere | `apply-coupon` crea el cupón sin volver a listar; solo evita dos cupones para la misma recomendación (409) |
| T-09 | Botones «Crear cupón en mi tienda», «Aplicar promoción», «Prefiero hacerlo yo» y «Marcar como ejecutada» siempre disponible (RF-28 `:116`) | `frontend/src/components/Modals.jsx`; tarjeta en `DetailScreens.jsx` | — | Tienda conectada | Difiere | Existen «Crear cupón en mi tienda» y «Prefiero hacerlo yo». El de precio dice «Aplicar precio promocional». No hay «Marcar como ejecutada»: la tarjeta ofrece «Ya lo apliqué por mi cuenta» |
| T-10 | Aplicar en Tiendanube: cupón en `/coupons`, precio en `promotional_price` de la variante; si la plataforma rechaza, sigue activa (`chapter04-metodologia.tex:350`; RF-28) | `routes/apply.js` (`POST apply`, `POST apply-coupon`); `platforms/tiendanube/api.js` | Clic de confirmación | Tienda conectada (capturas 05-06 y 11-12) | Hecho | e2e del cupón contra Tiendanube simulado; el precio promocional solo se prueba en la tienda conectada. Falta la verificación en producción |
| T-11 | Deshacer: restaura el precio promocional anterior o lo vacía, o borra el cupón; no se ofrece sin estado anterior o con cupón enviado por correo (RF-29 `:119`; `chapter04-metodologia.tex:350`) | `POST /:id/undo-apply` (`previous_promo_price`, `undo_unavailable`); botón en `InProgressCard.jsx` y `DetailScreens.jsx` | «Deshacer» → «Sí, deshacer» | Tienda conectada (capturas 09-10 y 13) | Hecho | Coincide con el valor vigente. Falta la verificación en producción (marca `VERIFICAR-COD`) |
| T-12 | Cupón por correo en abandono y fidelización: hasta 10 clientes, no se repite, allowlist fuera de producción (RF-26 `:108`; `chapter04-metodologia.tex:314-341`) | `routes/coupons.js`; `services/email.js` | «Enviar cupón» | Formulario en una de abandono | Hecho | |
| T-13 | Conversión del cupón: órdenes con el código enviado (RF-26 `:108`; `chapter04-metodologia.tex:339`) | `services/couponConversion.js` (worker); «Resultado del cupón» en `DetailScreens.jsx` | Worker después de la sync | No hay caso en los datos de demo (`conclusiones.tex:84`) | Hecho | Las marcas A3-conversion quedaron viejas |
| T-14 | Carrito, segunda compra y venta cruzada sugieren un cupón «enviado por correo» (RF-31 `:127`, RF-32 `:131`, RF-35 `:143`) | `routes/coupons.js` acepta solo `churn_risk` y `loyalty_reward` | — | — | Difiere | Esos tres tipos tienen el cupón con un clic en Tiendanube, pero no el envío por correo |
| T-15 | WooCommerce: sin un clic; «Prefiero hacerlo yo» y ejecución manual o inferida (`chapter04-metodologia.tex:352`; `conclusiones.tex:84`) | `requireTiendanube` en `routes/apply.js` (403) | — | Tienda WooCommerce local | Hecho | |
| T-16 | Tiendas de ejemplo sin ID de variante responden con un mensaje claro (`conclusiones.tex:84`) | `routes/apply.js`: 400 «Esta tienda es de demostración y no está conectada a Tiendanube.» | Aplicar en una tienda del seed | Lucía en modo demo | Hecho | |

## Bandit e impacto

| id | Qué afirma el documento | Dónde está en el código | Cómo se dispara | Cómo mostrarlo en vivo | Estado | Nota |
|---|---|---|---|---|---|---|
| B-01 | Trece brazos por tienda, Beta(α, β) (`motor-ia.tex:251`; RF-16 `:76`) | `services/bandit/thompson.js` (`RECOMMENDATION_TYPES`) | — | `node scripts/banditReport.js` | Hecho | La marca A1-bandit (`motor-ia.tex:250`) quedó vieja |
| B-02 | Previa por rubro equivalente a 4 observaciones; Beta(2, 2) sin datos (`motor-ia.tex:255`) | `thompson.js` (`PRIOR_STRENGTH = 4`, `DEFAULT_PRIOR`) | Brazo nuevo | `banditReport.js` | Hecho | |
| B-03 | Descartes: «No aplica» y «Otro» fracaso; «Ya lo hice» éxito; «No me interesa ahora» no cuenta; deshacer antes del registro no cuenta (`motor-ia.tex:265`; `chapter05-requerimientos.tex:792`) | `services/bandit/index.js` (`reconcileDiscards`, conteo por motivo) | Próxima corrida del motor | Descartar con «No aplica a mi tienda» y ver `banditReport.js` | Hecho | La marca A4-bandit (`motor-ia.tex:264`) quedó vieja |
| B-04 | Orden: prioridad, muestra de Thompson del día, puntaje; semilla tienda + día (`motor-ia.tex:270`) | `thompson.js` (`seededRandom`, `PRIORITY_ORDER`) | Panel | Recargar: el orden no cambia en el día | Hecho | |
| B-05 | Tres orígenes: aplicada desde Decidata, confirmada, inferida (`motor-ia.tex:268`) | `execution_source`: `one_click`, `manual`, `inferred` | — | Tienda conectada (aplicada) | Hecho | |
| B-06 | Explicación de lo aprendido en el panel (`motor-ia.tex:270`) | `bandit_explanation` en `DetailScreens.jsx` | — | Detalle de una recomendación | Hecho | |
| B-07 | Evaluación de impacto diaria a las 5 (hora de Argentina) (`motor-ia.tex:46`) | `worker.js` (`IMPACT_EVALUATION_CRON = '0 5 * * *'`) | Worker | Logs de Railway | Hecho | |
| B-08 | Resultado a los 7, 14 o 30 días por prioridad; 14 predeterminado y marcado como recomendado (RF-27 `:112`; `chapter05-requerimientos.tex:1262`) | `frontend/src/components/FeedbackConfigModal.jsx`; `routes/recommendations.js` (`mode`) | Configuración del panel | Botón de ajustes del panel | Difiere | El modal ofrece «inmediato» o 7/14/30; sin preferencia guardada el backend usa `immediate`, y no hay etiqueta de recomendado |
| B-09 | Impacto esperado contra real, como correlación indicativa (RF-15 `:73`; RNF-10 `:203`; `chapter05-requerimientos.tex:1300`) | `services/impact/`; «X vendidas · esperabas ~N» en `frontend/src/lib/derive.js`; pie «no una relación de causa comprobada» | Fin de la ventana | Historial ▸ Aplicadas ▸ Ver resultado | Hecho | El banner dice «Desde que aplicaste… vendiste…», sin «Gracias a» (la marca de `:1227` quedó vieja) |
| B-10 | Deshacer una ejecución manual antes de evaluarla (RF-21 `:92`) | `POST /:id/undo` en `routes/recommendations.js`; «Deshacer: todavía no la apliqué» | — | En curso ▸ detalle | Hecho | |
| B-11 | Inferencia: stock +20% (mín. 1 u.), precio ±1%, baja de 5% (`motor-ia.tex:147`) | `services/impact/config.js`; `impact/inference.js` | Después de la sync | — | Hecho | |

## Notificaciones

| id | Qué afirma el documento | Dónde está en el código | Cómo se dispara | Cómo mostrarlo en vivo | Estado | Nota |
|---|---|---|---|---|---|---|
| N-01 | Notificaciones separadas: no ocupan cupo, no actualizan el bandit, sin impacto (`motor-ia.tex:145`; RF-22 `:95`) | `KINDS.NOTIFICATION` en `engine/candidates.js` | — | Campanita del panel | Hecho | Sin tope propio: después de regenerar hubo 10 avisos «Sin stock» por tienda del seed (informe de cierre del 22/11) |
| N-02 | Webhook de variante sin stock: notificación siempre y evaluación inmediata de la regla de stock (`motor-ia.tex:11`; RF-08 `:52`) | `services/sync/stockOut.js`; trabajo `stock-out` en `worker.js` | Webhook de producto | Poner una variante en 0 en la tienda conectada | Hecho | |

## Interfaz

| id | Qué afirma el documento | Dónde está en el código | Cómo se dispara | Cómo mostrarlo en vivo | Estado | Nota |
|---|---|---|---|---|---|---|
| I-01 | Pestañas Para hacer · En curso · Historial; filtros Todas / Aplicadas / Descartadas / No aplicadas (`chapter05-requerimientos.tex:1228`) | `frontend/src/pages/RecommendationsPage.jsx`; `components/HistoryList.jsx` | — | Panel (captura 01) | Hecho | Coincide con el valor vigente |
| I-02 | Banner «Lo que venís logrando con Decidata» con «En curso, midiéndose →» (`:1228`) | `components/ResultsBlock.jsx` | — | Panel (captura 02) | Hecho | |
| I-03 | Tarjeta En curso: título, fecha, barra de medición y «Deshacer»; detalle «Qué aplicamos en tu tienda» (`:1230`) | `components/InProgressCard.jsx`; `DetailScreens.jsx` (`WhatWeApplied`) | Aplicar con un clic | Tienda conectada (capturas 07-08) | Hecho | Falta la verificación en producción |
| I-04 | Insignia «Recomendación de Decidata» en cada tarjeta (`:1232`) | No está en `frontend/src` | — | — | Falta | Dos búsquedas sin resultado |
| I-05 | Detalle: «Por qué te lo recomendamos», «Lo que vemos en tu tienda» y aviso al pie (`:1232`) | `DetailScreens.jsx` | — | Detalle | Hecho | |
| I-06 | Descarte con motivo, texto hasta 200 caracteres y deshacer desde el aviso (RF-14 `:70`; `:1262`; `:790-792`) | `Modals.jsx` (`DISCARD_OPTIONS`, `maxLength={200}`); aviso con «Deshacer» en `RecommendationsPage.jsx` | «No me sirve» | Descartar y tocar «Deshacer» | Hecho | |
| I-07 | Motivos para reposición: discontinuado / reponer más adelante / otro (`:790`) | `DISCARD_OPTIONS_STOCK` en `Modals.jsx` | «No me sirve» en una de stock | Captura 04 | Hecho | |
| I-08 | Formulario del cupón: todos elegidos, `{nombre}`, código, dirección de respuesta (`:1247`) | `Modals.jsx` (formulario del cupón) | «Enviar cupón» | Una de abandono | Hecho | |
| I-09 | Acción armada con campos editables, confirmación y «Prefiero hacerlo yo»; aplicada, ofrece «Deshacer» (`:1278`) | `Modals.jsx` (`DiscountPreviewModal`); `appliedLine` en `lib/derive.js` | Botón de la tarjeta | Tienda conectada (capturas 05 y 11) | Hecho | Se abre en un modal desde el botón, no dentro del detalle |
| I-10 | Ejecución inferida: etiqueta «Ejecutada (inferida)» y botón «No fui yo» (`:1293`) | Pregunta «¿Lo aplicaste por esta recomendación?» (Sí/No) y botón «No la apliqué por esta recomendación» en `DetailScreens.jsx` | Inferencia | — | Difiere | Otros textos; la función (rechazar la inferencia) existe |
| I-11 | Comparación «Esperado / Real» (`:1300`) | `lib/derive.js` («vendidas · esperabas ~N») | Ventana cerrada | Ver resultado | Hecho | El texto no dice literalmente «Esperado / Real» |
| I-12 | Tarjeta de reposición sin «Marcar como ejecutada»; detalle con variantes sin stock y unidades vendidas; «Ver en Productos» abre los productos del admin (`:1321`) | `DetailScreens.jsx` (`isStockType`); `frontend/src/lib/admin.js` | Reposición activa | Captura 03 de reposición | Hecho | «Ver en Productos» solo aparece dentro del iframe de Tiendanube. Hoy no hay reposición activa en la tienda conectada. Falta la verificación en producción |
| I-13 | Ante un error, aviso con «Volver a recomendaciones» (RNF-24 `:246`) | `components/ErrorBoundary.jsx` | Error de pantalla | Pruebas de `ErrorBoundary` | Hecho | |
| I-14 | Métricas por producto por `GET /api/products/metrics`, sin pantalla propia (RF-11 `:61`) | `routes/productMetrics.js`; `services/products/metrics.js` | — | `curl` con el token de sesión | Hecho | Coincide con el valor vigente |
| I-15 | En WordPress, menú «Decidata» con la app en un iframe (`:1327`) | `integrations/woocommerce-plugin/decidata/decidata.php` | — | wp-admin ▸ Decidata | Hecho | |

## Seguridad y privacidad

| id | Qué afirma el documento | Dónde está en el código | Cómo se dispara | Cómo mostrarlo en vivo | Estado | Nota |
|---|---|---|---|---|---|---|
| S-01 | API sin estado: JWT de Nexo HS256 con `timingSafeEqual`; token de WooCommerce con HMAC; `req.store`; ninguna ruta acepta un `store_id` externo (`chapter04-metodologia.tex:504`; RNF-14 `:215`; RNF-17 `:224`) | `middleware/auth.js` | Cada pedido | e2e «Rutas sin sesión» (incluye `?store_id=`) | Hecho | |
| S-02 | AES-256-GCM, IV aleatorio, formato `1:<iv>:<tag>:<ct>`, clave de 32 bytes o no arranca (`chapter04-metodologia.tex:544`; RNF-16 `:221`) | `security/crypto.js`; `index.js` | — | Pruebas de `crypto` | Hecho | |
| S-03 | Migración de tokens en texto plano con `migrate-encrypt-tokens.js`, repetible (`chapter04-metodologia.tex:546`) | `backend/scripts/migrate-encrypt-tokens.js` | Manual | — | Hecho | |
| S-04 | Webhooks de Tiendanube con HMAC-SHA256 en `x-linkedstore-hmac-sha256`, `timingSafeEqual`, 401 si falla (`chapter04-metodologia.tex:551`; RNF-18 `:227`) | `middleware/webhookAuth.js` (`requireWebhookHmac`) | Webhook | Pruebas de webhooks | Hecho | |
| S-05 | Webhooks de privacidad `store-redact`, `customers-redact`, `customers-data-request`, idempotentes, con `PRIVACY_REQUESTS` (`chapter04-metodologia.tex:556-563`; RF-18 `:82`) | `routes/webhooks.js` | Webhook de privacidad | Pruebas de privacidad | Hecho | |
| S-06 | Anonimización para Gemini: `{{cliente}}`, sin correos, DNI de 7-8 dígitos ni enlaces (`chapter04-metodologia.tex:571`; RNF-19 `:230`) | `security/llmSanitizer.js` (`EMAIL_PATTERN`, `DNI_PATTERN`, `LINK_PATTERN`) | Cada texto | Pruebas de `llmSanitizer` | Hecho | |
| S-07 | Textos de la tienda reducidos a una línea de hasta 120 caracteres, sin control, enlaces ni delimitadores, en un bloque separado (`motor-ia.tex:326`; `chapter04-metodologia.tex:573`) | `llmSanitizer.js` (`STORE_TEXT_MAX_LENGTH = 120`) | Cada texto | Pruebas de `llmSanitizer` | Hecho | |
| S-08 | Correo: modo `allowlist` predeterminado, `live` solo desplegado; no envía dentro de una prueba ni sin clave (`chapter04-metodologia.tex:342`; RNF-21 `:237`) | `services/email.js`; `routes/coupons.js` | Envío de cupón | — | Hecho | |
| S-09 | Permisos de Tiendanube: `read_products`, `read_orders`, `read_customers`, `write_webhooks`, `read_coupons`, `write_coupons`, `write_products`; no pide órdenes, riesgo, plantillas ni descuentos (`chapter04-metodologia.tex:509-539`; RNF-22 `:240`) | No están en el código: se configuran en el panel de Partners | Instalación | Panel de Partners | Hecho | No está en el código. Confirmado por Tomi contra el panel de Partners (22/11) |
| S-10 | WooCommerce con claves de solo lectura; rechaza conexiones con escritura (`chapter04-metodologia.tex:539`; `multiplataforma.tex:42`) | `routes/authWoocommerce.js` (`permissions !== 'read'` → 400) | Conexión | Pruebas de `wcRoutes` | Hecho | |
| S-11 | Ninguna escritura en la tienda sin confirmación explícita (RNF-23 `:243`) | Las escrituras solo están en `POST` de `routes/apply.js`, llamadas desde el modal | Clic | Tienda conectada | Hecho | |
| S-12 | Límite de pedidos por IP detrás de nginx, como limitación (`conclusiones.tex:87`) | `index.js` (`app.set('trust proxy', 1)`) | — | — | Hecho | Se declara como limitación |
| S-13 | Clave de Gemini solo en el backend (RNF-11 `:206`) | `GEMINI_API_KEY` solo en `backend`; nada en `frontend/src` | — | — | Hecho | |

## Integraciones

| id | Qué afirma el documento | Dónde está en el código | Cómo se dispara | Cómo mostrarlo en vivo | Estado | Nota |
|---|---|---|---|---|---|---|
| G-01 | OAuth 2.0 con Tiendanube, token cifrado (RF-01 `:31`) | `routes/auth.js` | Instalación | Tienda conectada | Hecho | |
| G-02 | WooCommerce: `/wc-auth/v1/authorize` de solo lectura, claves al backend (`multiplataforma.tex:42`) | `routes/authWoocommerce.js` | Conexión desde el plugin | Tienda WooCommerce local | Hecho | |
| G-03 | Menú visible con el permiso de administrar WooCommerce; token que vence a los 10 minutos (`multiplataforma.tex:46`) | `decidata.php` (`manage_woocommerce`, `DECIDATA_TOKEN_TTL = 600`) | — | wp-admin | Hecho | |
| G-04 | Ingesta incremental de WooCommerce con `modified_after` y paginación (`multiplataforma.tex:48`) | `platforms/woocommerce/api.js` | Sync | — | Hecho | |
| G-05 | Webhooks de WooCommerce registrados por el plugin, firma base64 en `X-WC-Webhook-Signature`, secreto derivado (`multiplataforma.tex:50`) | `platforms/woocommerce/webhooks.js`; `decidata.php` (`decidata_register_webhooks`) | Webhook | — | Hecho | |
| G-06 | Rubro elegido en el plugin (`multiplataforma.tex:52`; RF-06) | `connectInfoSchema` en `routes/authWoocommerce.js` | Conexión | — | Hecho | |
| G-07 | WooCommerce no informa carritos abandonados (`conclusiones.tex:84`) | Sin carritos en el adaptador de WooCommerce | — | — | Hecho | Se declara como limitación |
| G-08 | Sync incremental cada 6 horas con pg-boss (RNF-15 `:218`; `motor-ia.tex:11`) | `worker.js` (`SYNC_ALL_STORES`, `America/Argentina/Buenos_Aires`) | Worker | Logs de Railway | Hecho | |
| G-09 | Adaptadores con interfaz canónica `PlatformAdapter` (RNF-13 `:212`) | `platforms/interface.js`; `platforms/registry.js` | — | — | Hecho | |

## Cifras medidas

| id | Qué afirma el documento | De dónde sale | Estado | Nota |
|---|---|---|---|---|
| C-01 | Cobertura backend: 469 pruebas, 85,2% de líneas, ramas 88,8%, del 24/10 sobre `20e01a2` (`chapter04-metodologia.tex:656`; `conclusiones.tex:49`) | No se encontró un informe guardado con esas cifras (dos búsquedas) | Difiere | Última medición: `docs/evaluacion/cobertura-2026-11-22.md` sobre `cc8dc02`: 816/816, líneas 90,94%, ramas 90,37%, funciones 88,91%, sentencias 90,94% |
| C-02 | Cobertura frontend: 73 pruebas, al menos 85% de líneas (`chapter04-metodologia.tex:656`; `conclusiones.tex:49`) | Ídem | Difiere | 22/11: 322/322, líneas 94,00%, ramas 87,76%, funciones 91,40%, sentencias 92,31% |
| C-03 | RNF-20: cobertura de líneas ≥ 85% en backend y frontend (`:233`) | `cobertura-2026-11-22.md` | Hecho | Las cuatro métricas superan 85% en los dos lados |
| C-04 | WAPE (Tabla `eval-wape`, conjunto del 17/10): total de tienda 16,6% / 28,1% / 27,7% (`evaluacion-modelos.tex:164-186`; `conclusiones.tex:30`) | `docs/evaluacion-modelos.md` | Difiere | 22/11 (seed hasta el 14/11, `mediciones-2026-11-22.md`): total de tienda MariCafé 17,0%, Lucía 41,0%, TecnoShop 38,1%; por variante 53,8% / 122,5% / 111,1% |
| C-05 | Silueta 0,662 / 0,546 / 0,444 (`conclusiones.tex:30`) | `docs/evaluacion-modelos.md` | Difiere | 22/11: MariCafé 0,6625, Lucía 0,5184, TecnoShop 0,3829 |
| C-06 | Objetivo 2 cumplido: WAPE total < 30% y silueta ≥ 0,4 (`conclusiones.tex:30`) | `docs/evaluacion-modelos.md` | Difiere | Con la medición del 22/11, Lucía y TecnoShop superan 30% de WAPE total y la silueta de TecnoShop queda en 0,38 |
| C-07 | Análisis por tienda sin Gemini entre 7,4 y 8,4 s, máximo 9,2 s (`evaluacion-modelos.tex:281-295`; `conclusiones.tex:28`) | `docs/evaluacion-modelos.md` | Difiere | 22/11: promedio 7,34 / 6,53 / 6,64 s, máximo 8,02 s. El objetivo de < 10 s se sigue cumpliendo |
| C-08 | Latencia del panel: p95 < 240 ms; objetivo p95 < 300 ms (`evaluacion-modelos.tex:308-324`; `conclusiones.tex:28`) | `docs/evaluacion-modelos.md` | Difiere | 22/11: p95 306,0 / 265,9 / 304,8 ms (Lucía, MariCafé, TecnoShop); dos tiendas pasan de 300 ms. Backend local contra Neon desde una PC |
| C-09 | 178 de 178 recomendaciones con texto: 67 del modelo y 111 de plantilla (`conclusiones.tex:32`) | No se encontró el resultado guardado (dos búsquedas) | No verificado | En la medición del 22/11 todas tienen texto (`mediciones-2026-11-22.md`) |
| C-10 | Proporción de datos generados y fila `dev_tiendanube` de la tabla de origen (`evaluacion-modelos.tex:122-152`) | `node scripts/dataOrigin.js`; `raw-2026-11-22/data-origin.txt` | No verificado | Recalculada el 22/11 sobre una copia de producción (solo lectura): seed 3 tiendas, 1.730 órdenes (74,2%); `dev_tiendanube` 1 tienda, 356 órdenes (15,3%), 641 ítems, 63 clientes; `real` 1 tienda, 244 órdenes (10,5%); sintético 89,5% de las órdenes. Pendiente: la tienda marcada `real` («decidata-gastro») es la tienda WooCommerce local de desarrollo, no un comerciante real |

## Pendientes para decidir

Todos los Difiere, Falta y No verificado, ordenados por riesgo en la defensa: primero lo que se ve en una demo en vivo. No se arregló nada ni se cambió el documento.

**Se ve en la demo**

1. **T-09** Textos de los botones: el documento dice «Aplicar promoción» y «Marcar como ejecutada»; la app muestra «Aplicar precio promocional» y «Ya lo apliqué por mi cuenta».
2. **I-04** Falta la insignia «Recomendación de Decidata» en las tarjetas.
3. **B-08** Frecuencia del resultado: el documento dice 14 días predeterminado y marcado como recomendado; la app tiene además «inmediato», usa `immediate` sin preferencia y no marca ninguna opción como recomendada.
4. **T-05** Sin costo cargado no aparece el aviso para cargarlo (el backend devuelve `warned: 'no_cost'` y el frontend no lo muestra).
5. **I-10** Ejecución inferida: el documento dice «Ejecutada (inferida)» y «No fui yo»; la app pregunta «¿Lo aplicaste por esta recomendación?» y ofrece «No la apliqué por esta recomendación».
6. **T-14** Carrito, segunda compra y venta cruzada: el documento dice cupón «enviado por correo»; el envío por correo solo existe para abandono y fidelización.
7. **T-07** Cupón equivalente: el código toma cualquier cupón porcentual vigente, sin comparar porcentaje ni vigencia.
8. **T-08** Antes de crear el cupón, el código no vuelve a consultar los cupones de la tienda.

**Se pregunta en la defensa (cifras y detalles)**

9. **C-06** Objetivo 2 (WAPE total < 30% y silueta ≥ 0,4): con la medición del 22/11 no se cumple en Lucía (41,0%) ni en TecnoShop (38,1%; silueta 0,38). El documento usa el conjunto del 17/10.
10. **C-04** y **C-05** WAPE y silueta del documento (17/10) distintos de los del 22/11.
11. **C-08** Latencia: el documento dice p95 < 240 ms; el 22/11 dio 306,0 y 304,8 ms en dos tiendas (objetivo < 300 ms).
12. **C-01** y **C-02** Cobertura del documento (24/10: 469 y 73 pruebas) desactualizada; la del 22/11 es mayor (816 y 322 pruebas, todas las métricas > 85%).
13. **C-07** Tiempos del análisis distintos (el 22/11 son menores; el objetivo se cumple igual).
14. **M-09** Puntaje del cambio de estación en escala 0-1 en el código y 0-100 en la tabla.
15. **S-09** Permisos de Tiendanube: no se pueden confirmar desde el código (se configuran en el panel de Partners).
16. **C-09** 178 de 178 recomendaciones con texto: no se encontró el resultado guardado.
17. **C-10** Proporción de datos generados y fila `dev_tiendanube`: no verificado (marcas en `evaluacion-modelos.tex:151-152`).

## Marcas en los `.tex`

73 marcas en `main` (`2a62bd3`). No se sacó ninguna. La columna «Ítem» indica con qué fila de arriba se relaciona, cuando hay una.

| Archivo:línea | Marca | Texto | Ítem |
|---|---|---|---|
| `chapters/abstract.tex:6` | PENDIENTE-IMPL | B1 | T-10, T-11 |
| `chapters/brainstorming.tex:144` | PENDIENTE-IMPL | B1, B2 (acción con un clic y piso de margen incorporados después de la sesión) | T-10; T-04 |
| `chapters/brainstorming.tex:154` | PENDIENTE-IMPL | B1, B2 | T-10, T-11; T-03, T-04 |
| `chapters/chapter00.tex:4` | PENDIENTE-IMPL | B1 | T-10, T-11 |
| `chapters/chapter00.tex:46` | PENDIENTE-IMPL | B1 (aplicación con un clic en Tiendanube) | T-10 |
| `chapters/chapter04-metodologia.tex:208` | PENDIENTE-IMPL | B5 (acción sugerida y registro de acciones aplicadas) | B-05 |
| `chapters/chapter04-metodologia.tex:209` | VERIFICAR-COD | diagrama ER con las tablas nuevas (coupon_sends, acción aplicada, campos de descarte) | — |
| `chapters/chapter04-metodologia.tex:302` | PENDIENTE-IMPL | B2 (textos de la acción sugerida) | M-20 |
| `chapters/chapter04-metodologia.tex:339` | PENDIENTE-IMPL | A3-conversion | T-13 |
| `chapters/chapter04-metodologia.tex:344` | PENDIENTE-IMPL | B1 (subsección completa) | T-10, T-11 |
| `chapters/chapter04-metodologia.tex:404` | PENDIENTE-IMPL | B1 (escrituras en la plataforma) | T-10 |
| `chapters/chapter04-metodologia.tex:405` | VERIFICAR-COD | la Figura de componentes no muestra el servicio de correo ni las escrituras en la API de la plataforma | — |
| `chapters/chapter04-metodologia.tex:511` | VERIFICAR-COD | nombre exacto del permiso de Tiendanube para leer los carritos abandonados (RF-31) | S-09 |
| `chapters/chapter05-requerimientos.tex:52` | VERIFICAR-COD | re-medición del motor con la regla de stock nueva | C-04 a C-08 |
| `chapters/chapter05-requerimientos.tex:107` | PENDIENTE-IMPL | A3-conversion (orders.coupon_code se sincroniza, pero la conversión del cupón todavía no se calcula) | T-13 |
| `chapters/chapter05-requerimientos.tex:111` | PENDIENTE-IMPL | A5-default (sin preferencia guardada, el backend muestra el resultado apenas se mide; el modal preselecciona 14) | B-08 |
| `chapters/chapter05-requerimientos.tex:115` | PENDIENTE-IMPL | B1 (aplicar la acción con un clic) | T-10 |
| `chapters/chapter05-requerimientos.tex:119` | VERIFICAR-COD | confirmar en producción (deshacer aplicado) | T-11 |
| `chapters/chapter05-requerimientos.tex:122` | PENDIENTE-IMPL | B2 (descuento sugerido con piso de margen) | T-04 |
| `chapters/chapter05-requerimientos.tex:126` | PENDIENTE-IMPL | B4 (carrito abandonado) | M-10 |
| `chapters/chapter05-requerimientos.tex:130` | PENDIENTE-IMPL | B4 (segunda compra) | M-11 |
| `chapters/chapter05-requerimientos.tex:134` | PENDIENTE-IMPL | B4 (curva de talles rota) | M-12 |
| `chapters/chapter05-requerimientos.tex:138` | PENDIENTE-IMPL | B4 (día de baja demanda) | M-13 |
| `chapters/chapter05-requerimientos.tex:142` | PENDIENTE-IMPL | B4 (venta cruzada de accesorios) | M-14 |
| `chapters/chapter05-requerimientos.tex:478` | PENDIENTE-IMPL | B1 (HU completa) | T-10, T-11 |
| `chapters/chapter05-requerimientos.tex:497` | PENDIENTE-IMPL | B4 (HU completa) | M-10 a M-14 |
| `chapters/chapter05-requerimientos.tex:513` | PENDIENTE-IMPL | B4 (HU completa) | M-10 a M-14 |
| `chapters/chapter05-requerimientos.tex:529` | PENDIENTE-IMPL | B4 (HU completa) | M-10 a M-14 |
| `chapters/chapter05-requerimientos.tex:546` | PENDIENTE-IMPL | B4 (HU completa) | M-10 a M-14 |
| `chapters/chapter05-requerimientos.tex:563` | PENDIENTE-IMPL | B4 (HU completa) | M-10 a M-14 |
| `chapters/chapter05-requerimientos.tex:605` | PENDIENTE-IMPL | B4 (trece reglas), B1 (CU-11) | M-01 a M-14; T-10, T-11 |
| `chapters/chapter05-requerimientos.tex:711` | PENDIENTE-IMPL | B4 (señales y tipos nuevos), B1-B2 (acción sugerida) | M-10 a M-14 |
| `chapters/chapter05-requerimientos.tex:821` | PENDIENTE-IMPL | A4-bandit (resultado de los descartes según el motivo) | B-03 |
| `chapters/chapter05-requerimientos.tex:970` | PENDIENTE-IMPL | A3-conversion | T-13 |
| `chapters/chapter05-requerimientos.tex:984` | PENDIENTE-IMPL | B1 (CU completo) | T-10, T-11 |
| `chapters/chapter05-requerimientos.tex:1014` | VERIFICAR-COD | confirmar en producción (deshacer aplicado: revierte precio promocional o borra cupón, devuelve a «Para hacer») | T-11 |
| `chapters/chapter05-requerimientos.tex:1080` | PENDIENTE-IMPL | B1, B4 (filas HU-14 a HU-19) | M-10 a M-14 |
| `chapters/chapter05-requerimientos.tex:1113` | PENDIENTE-IMPL | B1, B3 | T-10, T-11 |
| `chapters/chapter05-requerimientos.tex:1227` | VERIFICAR-COD | el banner de ImpactSummary dice «Gracias a que ejecutaste las recomendaciones, vendiste ~$X», redacción causal que contradice RNF-10 | B-09 |
| `chapters/chapter05-requerimientos.tex:1228` | PENDIENTE-CAPTURA | pestañas Para hacer, En curso e Historial con sus filtros y el banner | I-01 |
| `chapters/chapter05-requerimientos.tex:1230` | VERIFICAR-COD | confirmar en producción (pestaña En curso y detalle «Qué aplicamos en tu tienda») | I-03 |
| `chapters/chapter05-requerimientos.tex:1234` | PENDIENTE-CAPTURA | A6 (pantallas del rediseño que pasa Romi: panel con banner de impacto y detalle) | I-01, I-02, I-05 |
| `chapters/chapter05-requerimientos.tex:1249` | PENDIENTE-CAPTURA | A3 (formulario de envío del cupón) | I-08 |
| `chapters/chapter05-requerimientos.tex:1264` | PENDIENTE-CAPTURA | A4-A5 (cuadro de descarte con motivo y modal de frecuencia del resultado) | I-06; B-08 |
| `chapters/chapter05-requerimientos.tex:1274` | PENDIENTE-IMPL | B1 (pantalla de la acción armada) | I-09 |
| `chapters/chapter05-requerimientos.tex:1280` | PENDIENTE-CAPTURA | B1 (detalle con la acción armada y el botón de confirmación) | I-09 |
| `chapters/chapter05-requerimientos.tex:1321` | VERIFICAR-COD | confirmar en producción (tarjeta de reposición: variantes sin stock, unidades vendidas, botón Ver en Productos) | I-12 |
| `chapters/chapter05-requerimientos.tex:1322` | PENDIENTE-CAPTURA | captura de la tarjeta y detalle de reposición de stock | I-12 |
| `chapters/conclusiones.tex:27` | VERIFICAR-COD | los tiempos (fila 1) y las 178 recomendaciones (fila 3) se midieron con seis reglas; volver a medir con las trece | C-07, C-09 |
| `chapters/conclusiones.tex:54` | PENDIENTE-IMPL | B1 | T-10, T-11 |
| `chapters/conclusiones.tex:64` | PENDIENTE-IMPL | B1 (aplicada desde el panel), A4-bandit (descarte «Ya lo hice por mi cuenta») | B-03; T-10 |
| `chapters/evaluacion-modelos.tex:151` | VERIFICAR-COD | proporción de datos generados | C-10 |
| `chapters/evaluacion-modelos.tex:152` | VERIFICAR-COD | actualizar la fila dev_tiendanube de la Tabla eval-origen con los conteos de la base donde se corrió la prueba de punta a punta con Tiendanube | C-10 |
| `chapters/motor-ia.tex:4` | PENDIENTE-IMPL | B4 (trece reglas), B2 (cálculo del descuento) | M-01 a M-14; T-03, T-04 |
| `chapters/motor-ia.tex:10` | PENDIENTE-IMPL | B4 (trece reglas), B1 (acción sugerida armada) | M-01 a M-14; T-01 |
| `chapters/motor-ia.tex:22` | PENDIENTE-IMPL | B4 (trece reglas), B1 (ejecución aplicada desde Decidata) | M-01 a M-14; B-05 |
| `chapters/motor-ia.tex:83` | PENDIENTE-IMPL | B4 (cinco reglas nuevas: trece en total, cuatro propias de un rubro) | M-10 a M-14 |
| `chapters/motor-ia.tex:119` | PENDIENTE-IMPL | A2-esperado (el código no guarda la tasa de retorno esperada de la fidelización) | M-08 |
| `chapters/motor-ia.tex:122` | VERIFICAR-COD | seasonRule.js guarda rule_score = min(s/50, 1), en escala 0 a 1 y no 0 a 100 como el resto (la tabla lo expresa como min(2s, 100)) | M-09 |
| `chapters/motor-ia.tex:125` | PENDIENTE-IMPL | B4 (carrito abandonado) | M-10 |
| `chapters/motor-ia.tex:128` | PENDIENTE-IMPL | B4 (segunda compra) | M-11 |
| `chapters/motor-ia.tex:131` | PENDIENTE-IMPL | B4 (curva de talles rota) | M-12 |
| `chapters/motor-ia.tex:134` | PENDIENTE-IMPL | B4 (día de baja demanda) | M-13 |
| `chapters/motor-ia.tex:137` | PENDIENTE-IMPL | B4 (venta cruzada de accesorios) | M-14 |
| `chapters/motor-ia.tex:149` | PENDIENTE-IMPL | B4 (umbrales de las reglas nuevas) | M-16 |
| `chapters/motor-ia.tex:189` | PENDIENTE-IMPL | B4 (filas de las reglas nuevas) | M-16 |
| `chapters/motor-ia.tex:250` | PENDIENTE-IMPL | B4 y A1-bandit (el código tiene siete brazos; faltan season_change y los cinco tipos nuevos) | B-01 |
| `chapters/motor-ia.tex:264` | PENDIENTE-IMPL | A4-bandit (hoy los descartes no actualizan el bandit; TODO en bandit/index.js) | B-03 |
| `chapters/motor-ia.tex:267` | PENDIENTE-IMPL | B1 (origen «aplicada desde Decidata») | B-05 |
| `chapters/motor-ia.tex:272` | PENDIENTE-IMPL | B4 (más reglas compitiendo por el cupo) | M-18 |
| `chapters/motor-ia.tex:275` | PENDIENTE-IMPL | B1, B2 (subsección completa) | T-10, T-11; T-03, T-04 |
| `chapters/negocio-financiero.tex:57` | VERIFICAR-COD | costo real de infraestructura | — |
| `chapters/summary.tex:6` | PENDIENTE-IMPL | B1 | T-10, T-11 |

## Decisiones del 22/11 (Tomi, con Romi)

Congelamiento del código: 7/12. Los cambios del documento los aplica otra sesión.

| Ítem | Decisión |
|---|---|
| T-09 | Ajustar el documento a los textos reales: «Aplicar precio promocional» y «Ya lo apliqué por mi cuenta» |
| I-04 | Sacar del documento la insignia «Recomendación de Decidata» |
| B-08 | Ajustar el documento: opción «inmediato», predeterminado sin preferencia, sin etiqueta de recomendado |
| T-05 | Cambio en el código (mostrar el aviso de costo faltante): hecho y mergeado (`208369e`). El documento ya lo describe así |
| I-10 | Ajustar el documento a los textos reales de la inferencia |
| T-14 | Ajustar el documento: carrito, segunda compra y venta cruzada sugieren un cupón que se crea en la tienda, sin envío por correo |
| T-07 | Ajustar el documento: si ya hay un cupón porcentual vigente, se informa en lugar de crear otro |
| T-08 | Ajustar el documento: la vista previa consulta los cupones vigentes |
| C-06, C-04, C-05 | Ajustar el documento con las cifras guardadas del 22/11 (`mediciones-2026-11-22.md`): WAPE total 17,0% / 41,0% / 38,1%; silueta 0,6625 / 0,5184 / 0,3829. El objetivo 2 se cumple en parte (MariCafé sí; Lucía y TecnoShop no) |
| M-09 | Dejar como está |
| S-09 | Confirmado contra el panel de Partners |
| C-09 | Reemplazar «178 de 178» por «30 de 30 (6 de Gemini, 24 de plantilla), medición del 22/11» |
| C-10 | Recalculada con la copia de producción (`docs/evaluacion/origen-datos-produccion-2026-11-22.txt` del código). «decidata-gastro» es una tienda de desarrollo (confirmado por Tomi): no hay datos de comerciantes reales. Seed 1.730 órdenes (74,2%); tiendas de desarrollo 600 órdenes (25,8%) |
| C-01, C-02, C-07 | Actualizar el documento con las cifras del 22/11 (cobertura `cobertura-2026-11-22.md`; tiempos `mediciones-2026-11-22.md`) |
| C-08 | Se mantiene la latencia de octubre con su fecha (conjunto del 17/10). La medición del 22/11 (p95 de 306,0 ms en Lucía y 304,8 ms en TecnoShop) queda en `mediciones-2026-11-22.md` |

