# Resumen del proyecto

Estado actual del notebook `EV2_BIY7121_005D.ipynb` para retomarlo sin perder contexto. Proyecto EV2 Minería de Datos BIY7121-005D, CRISP-DM, sobre clima de Chile (109 estaciones, 2021-2025).

## Decisión central (vigente)

Cada técnica predice la variable donde es eficaz. NO hay un objetivo único; las tres familias conviven:

- **Regresión → temperatura media diaria** (`temp_media`). R²≈0.93.
- **Clasificación → ocurrencia de lluvia** (`llueve`, binario). F1≈0.77.
- **Cluster → perfiles climáticos de estaciones**. silhouette≈0.48.

Razón: se midió que la lluvia diaria es ~89% ruido temporal (caótica, techo R²≈0.18-0.42). Por eso NO se hace regresión de lluvia. `lluvia_intensa` se descartó como objetivo (rara, ~4.6%, caótica) y quedó como variable descriptiva.

> Nota: una versión anterior de este proyecto tenía como foco `lluvia_intensa` y regresión de `agua_caida`. Eso quedó OBSOLETO. No volver a ese enfoque.

## Datos

Dos datasets, ambos en memoria desde raw GitHub (`4mnesia/DataMining_Datos`):
1. Meteorológico DMC (medido): precipitación + temperatura por estación-día.
2. Atmosférico NASA POWER (reanálisis, `atmosfera_diario.csv`): humedad, viento, radiación, nubosidad, etc.

## Avance por fase

- **Fase 1** Negocio: contexto, 4 hipótesis de patrones, KPIs, problema. Hecha.
- **Fase 2** Datos: tipos, nulos, outliers, encoding, estadísticos, 2 matrices de correlación + matriz general, nuevas variables, zonas Köppen, mapas territoriales, dataset atmosférico, IQR por agrupador, lluvia intensa sostenida. Es la fase más extensa. Hecha.
- **Fase 3** Transformación: solo escalamiento. Hecha.
- **Fase 4** Modelamiento: 4.1 regresión (3 modelos + gráficos propios), 4.2 clasificación (3 modelos × 3 versiones + ajuste de umbral), 4.3 cluster (2 K-Means + jerárquico). Hecha.
- **Fase 5** Evaluación: métricas (incl. balanced accuracy), matrices de confusión, ROC, residuos, codo, silhouette, dendrograma, mapas, sobreajuste, mejoras. Hecha.
- **Fase 6** Deploy: **VACÍA. Pendiente.**

## Resultados (medidos)

- Regresión: SVR R²=0.931 / Lineal 0.918 / Árbol 0.908, RMSE≈1.5°C, sin sobreajuste.
- Clasificación `llueve`: Árbol F1=0.766 / SVM 0.744 / Logística 0.676.
- Cluster: K-Means K=4 silhouette=0.481 (grupos coherentes norte/centro/sur, sin las 2 estaciones insulares).

## Estado técnico

Notebook ejecuta de punta a punta sin errores ni warnings, numeración monótona, 57 gráficos. Para detalle de pendientes y revisión crítica, ver `tareas.md`.
