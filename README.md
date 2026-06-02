# Análisis de clima en Chile — Minería de Datos (EV2)

Proyecto académico de **Minería de Datos** (metodología CRISP-DM) sobre precipitación y temperatura en Chile: **109 estaciones**, **16 regiones**, periodo **2021–2025**. Todo el trabajo vive en un único notebook: **`EV2_BIY7121_005D.ipynb`**.

## Objetivo

Predecir y describir el comportamiento climático aplicando cada técnica a la variable que mejor predice:

- **Regresión → temperatura media diaria** (`temp_media`). R² ≈ **0.93** (SVR), RMSE ≈ 1.5 °C.
- **Clasificación → lluvia diaria** (`llueve` y `lluvia_intensa`). F1 ≈ **0.76** (`llueve`); para el evento raro `lluvia_intensa` se maneja el desbalance (umbral + pesos), F1 ≈ **0.52**, balanced accuracy ≈ 0.82.
- **Clustering → perfiles de estaciones** (K-Means). *En desarrollo.*

> **¿Por qué la regresión es de temperatura y no de lluvia?** Se midió que la lluvia diaria es ~89% ruido temporal (caos atmosférico): su R² topa en ~0.18–0.42 incluso con datos atmosféricos. La temperatura, en cambio, es predecible a escala diaria. Por eso la lluvia se modela como **clasificación** y la temperatura como **regresión**.

## Datos

Todo se carga **en memoria** desde las *raw URLs* del repositorio público [`4mnesia/DataMining_Datos`](https://github.com/4mnesia/DataMining_Datos) (no se descarga ni se clona nada al disco). Dos datasets:

1. **Meteorológico (estaciones DMC de Chile)** — `dataset_precipitaciones_temperaturas_chile.csv`: precipitación y temperatura por estación-día.
2. **Atmosférico (reanálisis NASA POWER)** — `atmosfera_diario.csv`: humedad, viento, radiación, nubosidad, presión, etc. por estación-día. Fuente: [NASA POWER](https://power.larc.nasa.gov/) (API `temporal/daily/point`). Se carga directamente desde el raw del repo de datos:
   `https://raw.githubusercontent.com/4mnesia/DataMining_Datos/refs/heads/main/atmosfera_diario.csv`

Apoyo: metadata de estaciones (`getEstacionesRedEma.json`), raster Köppen-Geiger 1 km (`.tif`, leído con PIL) y GeoJSON de Chile.

## Cómo ejecutar

Requiere Python con las dependencias de `requirements.txt` y **conexión a internet** (los datos se leen de GitHub).

```bash
pip install -r requirements.txt
python -m nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=1800 EV2_BIY7121_005D.ipynb
```

`kneed` (detección del codo en K-Means) se asegura con `%pip install -q kneed` en la primera celda.

## Estructura del notebook (6 fases CRISP-DM)

1. **Entendimiento del Negocio** — contexto, datos, hipótesis, KPIs, problema.
2. **Entendimiento de Datos** — tipos, nulos, outliers, transformaciones, estadísticos, correlaciones, nuevas variables, zonas Köppen, mapas, dataset atmosférico. *(rica en gráficos)*
3. **Transformación** — escalamiento (`StandardScaler`, ajustado solo en train).
4. **Modelamiento** — vista general por familia (regresión, clasificación, cluster); 3 versiones de hiperparámetros por modelo.
5. **Evaluación** — métricas (accuracy, balanced accuracy, F1, RMSE/R²), matrices de confusión, gráficos de codo/silhouette y análisis de sobreajuste.
6. **Deploy** — ficha conceptual.

## Archivos

| Archivo | Descripción |
|---|---|
| `EV2_BIY7121_005D.ipynb` | Notebook principal (el entregable) |
| `atmosfera_diario.csv` | Dataset atmosférico NASA POWER (también en el repo de datos) |
| `estructura.txt` | Estructura oficial obligatoria del proyecto |
| `requirements.txt` | Dependencias |
| `CLAUDE.md` | Guía para asistentes de IA |
| `PLAN_DE_TRABAJO_DATAMINING.md` | Guía operativa del proyecto |
| `PLAN_DE_MEJORAS.md` | Estado y mejoras |
