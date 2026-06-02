# Plan de trabajo — Proyecto Data Mining (EV2)

Guía operativa de `EV2_BIY7121_005D.ipynb`. Proyecto académico CRISP-DM sobre clima de Chile (109 estaciones, 16 regiones, 2021–2025). El entregable es el notebook ejecutado y defendible frente a la rúbrica.

## Objetivo actual del modelamiento

Cada técnica se aplica a la variable que mejor predice:

- **Regresión → temperatura media diaria (`temp_media`).** R² ≈ 0.93 (SVR). La lluvia diaria es caótica (techo R² ≈ 0.18–0.42 incluso con datos atmosféricos), por lo que NO se usa como objetivo de regresión.
- **Clasificación → lluvia diaria** (`llueve`, `lluvia_intensa`). 3 modelos: Regresión Logística, SVM (RBF), Árbol de decisión. Manejo de desbalance con `class_weight='balanced'` + **ajuste de umbral** en validación.
- **Clustering → perfiles de estaciones / días** (K-Means). *Pendiente de armar (4.3).*

## Datos (en memoria, sin descarga ni clon)

Todo se carga **en memoria desde raw URLs de GitHub** (`4mnesia/DataMining_Datos`). **No existe carpeta `.data/` ni se clona nada.** Dos datasets:

1. **Meteorológico DMC** (`dataset_precipitaciones_temperaturas_chile.csv`): precipitación y temperatura por estación-día. Requiere carga controlada (filas entre comillas, mojibake).
2. **Atmosférico NASA POWER** (`atmosfera_diario.csv`, reanálisis): humedad, viento (dir/vel), radiación, radiacion_lw, indice_claridad, nubosidad, presión, rocío, hum_especifica, nasa_temp, nasa_precip por estación-día. Fuente: NASA POWER (`https://power.larc.nasa.gov/`, API `temporal/daily/point`). Subido al repo; se carga por raw URL.

Apoyo: metadata de estaciones (`getEstacionesRedEma.json`), raster Köppen-Geiger (`.tif`, leído con PIL), GeoJSON de Chile.

## Reglas duras (no negociables)

- Seguir `estructura.txt` (6 fases CRISP-DM con su numeración). No inventar ni renombrar secciones numeradas.
- Solo técnicas estándar e interpretables: `LinearRegression`, `LogisticRegression`, `DecisionTree`, `SVR/SVC`, `GaussianNB`, `KMeans`. Prohibido RandomForest, XGBoost, redes, Prophet, AutoML.
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

1. **Negocio**: contexto, 2 datasets, 4 hipótesis (patrones de datos, no métricas), KPIs, problema.
2. **Datos**: tipos, nulos, outliers (IQR + umbral físico 150 mm), transformaciones, estadísticos, 2 matrices de correlación, nuevas variables, **2.11 Köppen · 2.12 mapa · 2.13 categóricas · 2.14 transformaciones para modelar**. Rica en gráficos.
3. **Transformación**: solo **3.1 Escalamiento** (`StandardScaler`, solo train).
4. **Modelamiento**: 4.1 regresión (temperatura, hiperparámetros por CV + heatmaps), 4.2 clasificación (3 modelos × 3 versiones, vista general), 4.3 cluster *(pendiente)*.
5. **Evaluación**: métricas (accuracy, balanced accuracy, precision, recall, F1, R²/RMSE), matrices de confusión, sobreajuste; codo/silhouette al armar cluster.
6. **Deploy**: ficha conceptual.

## Resultados actuales

- **Regresión temperatura:** SVR R²=0.931, Lineal 0.918, Árbol 0.908 (RMSE ~1.5 °C).
- **Clasificación `llueve`:** Árbol F1=0.765 (balanced acc 0.838), SVM 0.744, Logística 0.677.
- **Clasificación `lluvia_intensa`:** Logística F1=0.520 (balanced acc 0.822), Árbol 0.502, SVM 0.407. Desbalance manejado con umbral ajustado + `nasa_precip` + lags atmosféricos.

## Trabajar con el notebook

- El `.ipynb` pesa ~5-6 MB (PNG embebidos). **No leerlo entero**; operar por `cell_id` con un script Python pequeño o `NotebookEdit`.
- Verificar métricas parseando los outputs de las celdas tras ejecutar, no releyendo el archivo.
- Español **sin tildes** en código/markdown (estilo consistente).
