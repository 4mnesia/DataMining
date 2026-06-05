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
- **Clasificación → `llueve` (binario sí/no llueve).** F1≈0.77.
- **Cluster → perfiles de estaciones.** silhouette≈0.48.
- **`lluvia_intensa` NO es objetivo de ningún modelo.** Es variable descriptiva de EDA (rara y caótica). No reintroducirla como target.
- **`agua_caida_tratada` NO se predice con regresión.** Es la lluvia tratada de outliers, base de `llueve`.

## Reglas de trazabilidad

- CRISP-DM y `estructura.txt` al pie de la letra. No renombrar secciones numeradas.
- Separar `X`, `Y` y variables excluidas. Advertir fuga.
- No usar variables derivadas del objetivo como predictoras (ej: no usar temp_max/min para predecir temp_media; no usar agua_caida para clasificar llueve).
- Imputación y escalado solo en train. Split temporal 2021-2023 / 2024-2025.
- Solo técnicas estándar: LinearRegression, LogisticRegression, DecisionTree, SVR/SVC, KMeans, AgglomerativeClustering. Nada de RandomForest/XGBoost/redes.
- Fase 4 = vista general de modelos; Fase 5 = evaluación. No mezclar.

## Datos

- Dos datasets en memoria desde raw GitHub (`4mnesia/DataMining_Datos`): meteorológico DMC (medido) + atmosférico NASA POWER (`atmosfera_diario.csv`, reanálisis).
- No reemplazar el dato medido (DMC) por el modelado (NASA).
- Apoyo: catálogo de estaciones (json), raster Köppen (tif, con PIL), GeoJSON Chile.

## Pendiente

Ver `tareas.md`. Lo único sin hacer de la estructura es la **Fase 6 Deploy**.
