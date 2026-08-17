---
title: Briefing — Proceso de reportes MYCSA (Campañas y redes)
date: 2026-08-17
tags:
  - mycsa
  - marketing
  - proceso
  - briefing
---

# Briefing: cómo se genera el reporte de campañas y redes de MYCSA

Documento de referencia para que cualquier agente prepare el **siguiente** reporte siguiendo el mismo proceso que el del 16 de agosto de 2026.

## 1. Fuente de datos

- **Herramienta:** Supermetrics Marketing Analytics (MCP), conectada a la cuenta de Claude.
- **Fuente autenticada:** solo Meta Ads (`ds_id = FA`). El resto de las 172 fuentes de Supermetrics NO están autenticadas — si se necesita otra red (TikTok, Google Ads, LinkedIn, etc.) hay que autenticarla primero en `hub.supermetrics.com/token-management`.
- **Cuenta publicitaria activa:** `act_940922298167545` (moneda **EUR**). Es la única de las 4 cuentas conectadas con actividad real; las otras 3 (`act_2093889504348315` "Cuenta publicitaria de Www.mycsamulder.com", `act_802510215559356`, `act_899037095901230`) no registraron gasto en el último periodo — conviene revisarlas de nuevo cada vez por si se activan.
- **Sin datos orgánicos:** no hay conector de alcance orgánico / seguidores (Facebook o Instagram Insights) autenticado. El reporte solo cubre pauta paga.

## 2. Consultas usadas (reproducibles)

Con `data_query` sobre `ds_id=FA`, `ds_accounts=["act_940922298167545"]`:

1. **Resumen de campañas:** `fields = [profile, adcampaign_name, campaignobjective, campaignstatus, cost, impressions, reach, Clicks, CTR, CPC, action_page_engagement, action_post_engagement, action_link_click]`, `date_range_type = last_30_days`.
2. **Totales de cuenta con comparación:** mismos campos sin desglose por campaña + `compare_type = prev_range` (da el Δ % vs. los 30 días previos).
3. **Tendencia diaria:** `fields = [Date, cost, impressions, reach, Clicks, action_page_engagement, action_link_click]`, `last_30_days`.
4. **Desglose por red:** `fields = [publisher_platform, adcampaign_name, cost, impressions, reach, Clicks, action_page_engagement, action_link_click]` — más una consulta aparte de `[publisher_platform, reach, Frequency, CTR, CPC]` porque `reach` **no se puede sumar** entre filas (es `is_non_aggregatable`).
5. **Fechas y presupuestos de campaña:** `fields = [adcampaign_name, campaign_start_date, campaign_end_date, campaign_daily_budget, campaign_lifetime_budget, campaignbuyingtype]`.

⚠️ Regla importante: métricas como `reach`, `Frequency`, `CTR`, `CPC` son *no agregables* — nunca sumarlas manualmente entre filas; pedir el total directamente en una consulta sin desglose.

## 3. Estructura del reporte entregado

1. **Cabecera:** periodo, periodo de comparación, cuenta, moneda, fecha de entrega.
2. **KPIs (30 días) con Δ% vs. periodo anterior:** inversión, impresiones, alcance, clics, CTR, CPC + chips secundarios (frecuencia, interacciones de página, clics en enlace).
3. **Tendencia diaria:** gráfico de inversión + impresiones, con anotaciones en fechas de lanzamiento de campaña.
4. **Reparto por red:** barras de inversión + tabla (alcance, clics, CTR, CPC) por Facebook / Instagram / Audience Network / Threads.
5. **Tabla de campañas:** nombre, objetivo, estado (pill activa/pausada), fecha de inicio, gasto, impresiones, alcance, clics, CTR, CPC, interacciones — ordenada por gasto.
6. **Lecturas clave:** 5-6 insights basados en los datos (qué disparó el gasto, qué campaña es más eficiente, qué red pesa más, etc.).
7. **Recomendaciones:** 3 acciones concretas para la siguiente revisión.

## 4. Formatos y dónde se guarda

- **HTML** (informe visual, con gráficos interactivos): `informes/reporte-campanas-redes-<YYYY-MM-DD>.html`
- **Markdown** (para Obsidian / lectura rápida): `informes/reporte-campanas-redes-<YYYY-MM-DD>.md`
- Ambos se commitean al repo `Mserpa07/proyectoproductosmycsa`, rama `claude/mycsa-campaigns-networks-report-h6gyr3` (o la que corresponda al ciclo).

## 5. Para el siguiente reporte

- Repetir las consultas de la sección 2 con el rango de fechas que corresponda.
- Volver a revisar `accounts_discovery(ds_id="FA")` por si se activó gasto en otra cuenta.
- Mantener el mismo formato de tabla/KPIs para que los reportes sean comparables entre periodos.
- Si se conecta una fuente orgánica nueva en Supermetrics, añadir una sección de alcance orgánico / seguidores.
