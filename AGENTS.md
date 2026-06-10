# Instrucciones para agentes

El notebook `EV2_BIY7121_005D.ipynb` tiene una narrativa definida y validada con datos. No rehacer desde cero. Antes de cambiar el foco de cualquier modelo, leer la evidencia (la descomposición de varianza de la Fase 5).

## Idioma y estilo

- Trabajar en español, sin tildes en código/markdown del notebook (estilo consistente).
- Tono académico, claro, trazable.
- Patrón por paso: markdown breve → código → análisis del output. Sin "mini-objetivos".
- Evitar celdas largas. No crear DataFrames/variables auxiliares si ya existe equivalente.
- No mencionar dentro del notebook el material de apoyo, módulos del curso ni archivos guía internos.

## Foco analítico (estado actual, NO el viejo)

Cada técnica se aplica a la variable que mejor predice. Las tres familias son válidas, no hay "una principal":

- **Regresión → `temp_media` (temperatura media diaria).** R²≈0.93. NO es regresión de lluvia: la lluvia diaria es caótica (~89% ruido, techo R²≈0.18-0.42 medido).
- **Pronóstico de lluvia → `llueve` (binario sí/no).** F1≈0.77 (Árbol depth=8, versión por validación cruzada). **Pronóstico honesto:** EXCLUYE la temperatura del mismo día; usa solo info de antemano (ubicación, mes, lags de lluvia, atmosféricas). Se probó umbral por zona climática: medido peor que el global, descartado.
- **Cluster → zonas climáticas Köppen por estación.** **K=7** = zonas Köppen con ≥3 estaciones (10 presentes; 3 marginales con 1-2 est.: Csc/Dfc/ET). Variables = **clima MEDIDO** (temperatura + nubosidad + humedad + lluvia SOLO como feature + oscilación térmica diaria + índice de aridez De Martonne), QuantileTransformer + PCA-3, sin insulares. El detalle estacional importa porque Köppen se define por la estacionalidad (Cs = verano seco). Sumar nubosidad/humedad/oscilación subió la cohesión (silhouette 0.30→0.41); el índice de aridez (balance hídrico lluvia/temperatura) refuerza la detección de la clase árida B. **Köppen NO es variable: etiqueta externa.** Evaluación del PPT del Módulo 3 (3 tareas): **tendencia** (Hopkins≈0.68), **codo** (KneeLocator K=5), **calidad intrínseca** (silhouette≈0.41, **Calinski-Harabasz≈115**) + **extrínseca vs Köppen** (ARI≈0.31, NMI≈0.49, **Fowlkes-Mallows≈0.42**). El perfil estacional subió la recuperación de Köppen (ARI 0.26→0.32). K-Means K=7 = mejor extrínseca. Calinski SÍ se usa (PPT); Davies-Bouldin no. **Dos granularidades:** HK1 = espacial (estaciones→Köppen); HK2 = temporal = clustering de mediciones mensuales normalizadas por estación (K=4) → recupera las 4 estaciones del año (ARI≈0.34 vs estación).
- **`lluvia_intensa` NO es objetivo de ningún modelo.** Es variable descriptiva de EDA (rara y caótica). No reintroducirla como target.
- **`agua_caida_tratada` NO se predice con regresión.** Es la lluvia tratada de outliers, base de `llueve`.

## Reglas de trazabilidad

- CRISP-DM y `estructura.txt` al pie de la letra. No renombrar secciones numeradas.
- Separar `X`, `Y` y variables excluidas. Advertir fuga.
- No usar variables derivadas del objetivo como predictoras (ej: no usar temp_max/min para predecir temp_media; no usar agua_caida para clasificar llueve).
- Imputación y escalado solo en train. Split temporal 2021-2023 / 2024-2025.
- Solo técnicas estándar (del curso): LinearRegression, LogisticRegression, DecisionTree, SVR/SVC, GaussianNB, KMeans, AgglomerativeClustering, KNeighborsClassifier (KNN, material 2.7 vecindad). Nada de RandomForest/XGBoost/redes.
- Fase 4 = vista general de modelos; Fase 5 = evaluación. No mezclar.

## Datos

- Dos datasets en memoria desde raw GitHub (`4mnesia/DataMining_Datos`): meteorológico DMC (medido) + atmosférico NASA POWER (`atmosfera_diario.csv`, reanálisis).
- No reemplazar el dato medido (DMC) por el modelado (NASA).
- Apoyo: catálogo de estaciones (json), raster Köppen (tif, con PIL), GeoJSON Chile.

## Pendiente

Ver `tareas.md`. Lo único sin hacer de la estructura es la **Fase 6 Deploy**.
