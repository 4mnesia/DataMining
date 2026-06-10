# Plan de trabajo — Proyecto Data Mining (EV2)

Guía operativa de `EV2_BIY7121_005D.ipynb`. Proyecto académico CRISP-DM sobre clima de Chile (109 estaciones, 16 regiones, 2021–2025). El entregable es el notebook ejecutado y defendible frente a la rúbrica.

## Objetivo actual del modelamiento

Cada técnica se aplica a la variable que mejor predice:

- **Regresión → temperatura media diaria (`temp_media`).** R² ≈ 0.93 (SVR). La lluvia diaria es caótica (techo R² ≈ 0.18–0.42 incluso con datos atmosféricos), por lo que NO se usa como objetivo de regresión.
- **Pronóstico de lluvia → ocurrencia** (`llueve`, binario). 3 modelos: Regresión Logística, SVM (RBF), Árbol de decisión; versión por **validación cruzada**. Manejo de desbalance con `class_weight='balanced'` + **ajuste de umbral** en validación. **Pronóstico honesto:** sin temperatura del mismo día (solo info de antemano). `lluvia_intensa` NO se modela (solo variable descriptiva de EDA).
- **Clustering → zonas climáticas Köppen, K=7** (= zonas con ≥3 estaciones; K-Means K=7, K-Means K=4 macro y Jerárquico K=7 ward). Variables = clima MEDIDO **por estación del año** `latitud+altura+temp_verano/invierno+lluvia_verano/otono/invierno/primavera+frecuencia_lluvia` (Köppen se define por estacionalidad), QuantileTransformer, sin insulares. **Köppen NO es variable: etiqueta externa** para validación extrínseca.

## Datos (en memoria, sin descarga ni clon)

Todo se carga **en memoria desde raw URLs de GitHub** (`4mnesia/DataMining_Datos`). **No existe carpeta `.data/` ni se clona nada.** Dos datasets:

1. **Meteorológico DMC** (`dataset_precipitaciones_temperaturas_chile.csv`): precipitación y temperatura por estación-día. Requiere carga controlada (filas entre comillas, mojibake).
2. **Atmosférico NASA POWER** (`atmosfera_diario.csv`, reanálisis): humedad, viento (dir/vel), radiación, radiacion_lw, indice_claridad, nubosidad, presión, rocío, hum_especifica, nasa_temp, nasa_precip por estación-día. Fuente: NASA POWER (`https://power.larc.nasa.gov/`, API `temporal/daily/point`). Subido al repo; se carga por raw URL.

Apoyo: metadata de estaciones (`getEstacionesRedEma.json`), raster Köppen-Geiger (`.tif`, leído con PIL), GeoJSON de Chile.

## Reglas duras (no negociables)

- Seguir `estructura.txt` (6 fases CRISP-DM con su numeración). No inventar ni renombrar secciones numeradas.
- Solo técnicas estándar e interpretables (del curso): `LinearRegression`, `LogisticRegression`, `DecisionTree`, `SVR/SVC`, `GaussianNB`, `KMeans`, `AgglomerativeClustering`, `KNeighborsClassifier` (KNN, material 2.7). Prohibido RandomForest, XGBoost, redes, Prophet, AutoML.
- **No citar el material de apoyo ni módulos del curso** en el notebook (es solo contexto del asistente, no para el profe).
- Patrón por paso: **encabezado breve → celda de código → análisis del output** (sin "mini-objetivos").
- Separar **Fase 4 (vista general, gráficos generales)** de **Fase 5 (evaluación: métricas, matrices de confusión, codo/silhouette, sobreajuste)**. De cada modelo se prueban **3 versiones** de hiperparámetros y se elige la mejor por validación.
- Evitar "tablas de la nada": si el dato sale del `df`, sacarlo en duro (`df.dtypes`, `df.describe()`, `print`).
- Imports solo arriba. Sin notas internas ni conversación en el notebook.
- Ajustar imputación/escalado **solo en train** (sin fuga). Split temporal 2021-2023 / 2024-2025.

## Comando para ejecutar/verificar

```bash
python -m nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=1800 EV2_BIY7121_005D.ipynb
```

Requiere internet (todo se baja de raw GitHub). `kneed` se asegura con `%pip install -q kneed` en la primera celda.

## Estructura (6 fases)

1. **Negocio**: contexto, 2 datasets, **6 hipótesis (2 por familia de modelo, patrones/relaciones — no métricas — trazables a F4/F5)**, KPIs, problema.
2. **Datos**: tipos, nulos, outliers (IQR + umbral físico 150 mm), transformaciones, estadísticos, **2 matrices de correlación (inicial 2.6 + general con todas las variables 2.9)**, nuevas variables, **2.11 Köppen · 2.12 mapa · 2.13 categóricas/insights · 2.14 transformaciones para modelar**. Rica en gráficos.
3. **Transformación**: solo **3.1 Escalamiento** (`StandardScaler`, solo train).
4. **Modelamiento**: 4.1 regresión (temperatura, hiperparámetros por CV + heatmaps), 4.2 clasificación (3 modelos × 3 versiones, vista general), 4.3 cluster (K-Means K=7 + K-Means K=4 macro + Jerárquico K=7 ward, sin insulares).
5. **Evaluación**: clasificación (accuracy, balanced accuracy, precision, recall, F1, matrices de confusión, ROC); regresión (R²/RMSE, residuos); cluster segun el PPT del Modulo 3 (3 tareas): tendencia (Hopkins), n° clusters (codo/KneeLocator + silhouette por K), y calidad intrínseca (silhouette, inercia, Calinski-Harabasz, dendrograma) + extrínseca vs Köppen (ARI, NMI, Fowlkes-Mallows).
6. **Deploy**: ficha conceptual.

## Resultados actuales

- **Regresión temperatura:** SVR R²=0.931, Lineal 0.918, Árbol 0.908 (RMSE ~1.5 °C).
- **Pronóstico `llueve`:** Árbol F1=0.767 (balanced acc 0.837, depth=8 por CV), SVM 0.741, Logística 0.672. Sin temperatura del mismo día. Desbalance manejado con `class_weight='balanced'` + umbral ajustado + `nasa_precip` + lags atmosféricos. Se probó umbral por zona (descartado, peor que el global).
- **Cluster (zonas Köppen, K=7):** intrínseca Hopkins=0.68, K-Means K=7 silhouette=0.413, Calinski=114.5, codo=K=5; extrínseca vs Köppen K-Means K=7 ARI=0.309, NMI=0.493, Fowlkes-Mallows=0.421 (el mejor). Clima medido (temp+nubosidad+humedad+lluvia+oscilación térmica diaria+índice de aridez; Köppen reservado como etiqueta externa). El perfil estacional subió la recuperación de Köppen (ARI 0.26→0.32). **HK2 (temporal):** clustering de mediciones mensuales normalizadas por estación → recupera las 4 estaciones del año (ARI 0.34 vs estación).
- `lluvia_intensa` **no se modela**: quedó como variable descriptiva de EDA (rara ~4.6% y caótica).

## Trabajar con el notebook

- El `.ipynb` pesa ~5-6 MB (PNG embebidos). **No leerlo entero**; operar por `cell_id` con un script Python pequeño o `NotebookEdit`.
- Verificar métricas parseando los outputs de las celdas tras ejecutar, no releyendo el archivo.
- Español **sin tildes** en código/markdown (estilo consistente).
