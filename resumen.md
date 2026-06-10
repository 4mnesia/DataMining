# Resumen del proyecto

Estado actual del notebook `EV2_BIY7121_005D.ipynb` para retomarlo sin perder contexto. Proyecto EV2 Minería de Datos BIY7121-005D, CRISP-DM, sobre clima de Chile (109 estaciones, 2021-2025).

## Decisión central (vigente)

Cada técnica predice la variable donde es eficaz. NO hay un objetivo único; las tres familias conviven:

- **Regresión → temperatura media diaria** (`temp_media`). R²≈0.93.
- **Pronóstico de lluvia → ocurrencia** (`llueve`, binario). F1≈0.77 (Árbol depth=8). Pronóstico honesto: sin temperatura del mismo día; versión por validación cruzada.
- **Cluster en 2 granularidades.** **HK1 (espacial):** estaciones→zonas Köppen, K=7 (clima por estación del año; intrínseca Hopkins≈0.68/silhouette≈0.41, extrínseca ARI≈0.31/NMI≈0.49 vs Köppen; Köppen = etiqueta externa). **HK2 (temporal):** mediciones mensuales normalizadas por estación→4 estaciones del año (ARI≈0.34 vs estación).

Razón: se midió que la lluvia diaria es ~89% ruido temporal (caótica, techo R²≈0.18-0.42). Por eso NO se hace regresión de lluvia. `lluvia_intensa` se descartó como objetivo (rara, ~4.6%, caótica) y quedó como variable descriptiva.

> Nota: una versión anterior de este proyecto tenía como foco `lluvia_intensa` y regresión de `agua_caida`. Eso quedó OBSOLETO. No volver a ese enfoque.

## Datos

Dos datasets, ambos en memoria desde raw GitHub (`4mnesia/DataMining_Datos`):
1. Meteorológico DMC (medido): precipitación + temperatura por estación-día.
2. Atmosférico NASA POWER (reanálisis, `atmosfera_diario.csv`): humedad, viento, radiación, nubosidad, etc.

## Avance por fase

- **Fase 1** Negocio: contexto, **6 hipótesis (2 por familia de modelo, trazables a F4/F5)**, KPIs, problema. Hecha.
- **Fase 2** Datos: tipos, nulos, outliers, encoding, estadísticos, **2 matrices de correlación (inicial 2.6 + general con todas las variables 2.9)**, nuevas variables, zonas Köppen, mapas territoriales, dataset atmosférico, IQR por agrupador, lluvia intensa sostenida. Es la fase más extensa. Hecha.
- **Fase 3** Transformación: solo escalamiento. Hecha.
- **Fase 4** Modelamiento: 4.1 regresión (3 modelos + gráficos propios), 4.2 clasificación (3 modelos × 3 versiones + ajuste de umbral), 4.3 cluster (K-Means K=7 + K-Means K=4 macro + Jerárquico K=7 ward). Hecha.
- **Fase 5** Evaluación: métricas (incl. balanced accuracy), matrices de confusión, ROC, residuos, codo, silhouette, dendrograma, mapas, sobreajuste, mejoras. Hecha.
- **Fase 6** Deploy: **VACÍA. Pendiente.**

## Resultados (medidos)

- Regresión: SVR R²=0.931 / Lineal 0.918 / Árbol 0.908, RMSE≈1.5°C, sin sobreajuste.
- Pronóstico `llueve`: Árbol F1=0.767 (depth=8, elegido por CV) / SVM 0.741 / Logística 0.672. Sin temperatura del mismo día. Se probó umbral por zona: peor que el global (descartado).
- Cluster (K=7 = zonas Köppen con ≥3 estaciones, de 10 presentes): clima MEDIDO (temperatura + nubosidad + humedad + lluvia como feature + oscilación térmica diaria + índice de aridez (balance hídrico)), QuantileTransformer + PCA-3, sin las 2 insulares; Köppen reservado como etiqueta externa. Intrínseca: Hopkins=0.68, K-Means K=7 silhouette=0.413, Calinski=114.5, codo=K=5. Extrínseca vs Köppen: K-Means K=7 ARI=0.309, NMI=0.493, Fowlkes-Mallows=0.421 (el mejor). El perfil estacional subió la recuperación de Köppen (ARI 0.26→0.32), porque Köppen se define por la estacionalidad.

## Estado técnico

Notebook ejecuta de punta a punta sin errores ni warnings, numeración monótona, 57 gráficos. Para detalle de pendientes y revisión crítica, ver `tareas.md`.
