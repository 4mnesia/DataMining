# Análisis de clima en Chile — Minería de Datos (EV2)

Proyecto académico de **Minería de Datos** (metodología CRISP-DM) sobre precipitación y temperatura en Chile: **109 estaciones**, **16 regiones**, periodo **2021–2025**. Todo el trabajo vive en un único notebook: **`EV2_BIY7121_005D.ipynb`**.

## Objetivo

Predecir y describir el comportamiento climático aplicando cada técnica a la variable que mejor predice:

- **Regresión → temperatura media diaria** (`temp_media`). R² ≈ **0.93** (SVR), RMSE ≈ 1.5 °C.
- **Pronóstico de lluvia → ocurrencia** (`llueve`, binario sí/no). F1 ≈ **0.77** (Árbol depth=8), balanced accuracy ≈ 0.84. Es un **pronóstico honesto**: usa solo información disponible de antemano (ubicación, mes, persistencia/lags de lluvia, atmosféricas) y **excluye la temperatura del mismo día**. La lluvia intensa NO se modela (es rara y caótica); queda como variable descriptiva.
- **Clustering → zonas climáticas Köppen por estación**, **K=7** (= zonas con ≥3 estaciones, de 10 presentes en Chile continental). Variables = clima **medido por estación del año** (ubicación, altitud, temperatura y lluvia de verano/otoño/invierno/primavera); **Köppen se reserva como etiqueta externa**. El detalle estacional es clave porque Köppen se define por la estacionalidad (clima mediterráneo = verano seco): subió la recuperación de Köppen frente al promedio anual. Validación **intrínseca** (Hopkins ≈ 0.68, silhouette ≈ 0.41) + **extrínseca vs Köppen** (ARI ≈ 0.31, NMI ≈ 0.49, Fowlkes-Mallows ≈ 0.43). K-Means K=7 es el mejor recuperando las zonas. **Segunda granularidad (temporal):** un clustering de las mediciones mensuales normalizadas por estación recupera las **4 estaciones del año** (ARI ≈ 0.34) — el clima muestra estructura tanto espacial (Köppen) como temporal (estaciones).

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

No requiere dependencias fuera de `requirements.txt` (numpy, pandas, matplotlib, scikit-learn, scipy, pillow, seaborn opcional).

## Estructura del notebook (6 fases CRISP-DM)

1. **Entendimiento del Negocio** — contexto, datos, **6 hipótesis (2 por familia de modelo, trazables)**, KPIs, problema.
2. **Entendimiento de Datos** — tipos, nulos, outliers, transformaciones, estadísticos, **2 matrices de correlación (inicial + general)**, nuevas variables, zonas Köppen, mapas, dataset atmosférico. *(rica en gráficos)*
3. **Transformación** — escalamiento (ajustado solo en train).
4. **Modelamiento** — vista general por familia (regresión, clasificación, cluster); 3 versiones de hiperparámetros por modelo, con gráficos propios de cada uno.
5. **Evaluación** — clasificación (accuracy, balanced accuracy, F1, matrices de confusión, curva ROC); regresión (RMSE/R², residuos); cluster segun el marco del Modulo 3 (tendencia con Hopkins; codo; calidad intrínseca silhouette/Calinski-Harabasz; extrínseca vs Köppen ARI/NMI/Fowlkes-Mallows), dendrograma; análisis de sobreajuste.
6. **Deploy** — *pendiente* (ver `tareas.md`).

## Archivos

| Archivo | Descripción |
|---|---|
| `EV2_BIY7121_005D.ipynb` | Notebook principal (el entregable) |
| `atmosfera_diario.csv` | Dataset atmosférico NASA POWER (también en el repo de datos) |
| `estructura.txt` | Estructura oficial obligatoria del proyecto |
| `requirements.txt` | Dependencias |
| `tareas.md` | **Tareas pendientes y revisión crítica para el equipo** |
| `CLAUDE.md` | Guía para asistentes de IA |
| `PLAN_DE_TRABAJO_DATAMINING.md` | Guía operativa del proyecto |
| `PLAN_DE_MEJORAS.md` | Estado y mejoras |
