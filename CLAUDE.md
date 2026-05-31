# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Qué es este repo

Proyecto académico de **Minería de Datos** (CRISP-DM) sobre precipitación/temperatura en Chile (109 estaciones, 16 regiones, 2021–2025). Todo el trabajo vive en **un solo notebook**: `EV2_BIY7121_005D.ipynb`. No es una aplicación; el "entregable" es el notebook ejecutado y defendible frente a una rúbrica.

## Comandos

No hay build ni tests (es un notebook). El comando central es ejecutarlo de punta a punta para verificar que corre sin errores y regenerar outputs:

```bash
python -m nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=600 EV2_BIY7121_005D.ipynb
```

- Usar `python -m nbconvert`, **no** `jupyter nbconvert`: el CLI `jupyter` no está en el PATH del entorno bash, pero el módulo sí está instalado.
- Requiere **internet**: todos los datos se descargan en memoria desde raw URLs de GitHub (`4mnesia/DataMining_Datos`); no hay archivos de datos locales (`.data/`, `DataMining_Datos/` están en `.gitignore`).
- Dependencias: `pip install -r requirements.txt`. `kneed` (detección de codo en K-Means) puede faltar; el notebook la instala con `%pip install -q kneed` en su primera celda. El GeoTIFF Köppen se lee con **PIL**, no rasterio.

## Reglas duras del proyecto (no negociables)

Estas vienen de la rúbrica y de `PLAN_DE_TRABAJO_DATAMINING.md`; romperlas baja la nota:

- **Seguir `estructura.txt` al pie de la letra**: 6 fases (1 Negocio, 2 Datos, 3 Transformación, 4 Modelamiento, 5 Evaluación, 6 Deploy) con su numeración. **No inventar ni renombrar secciones numeradas.**
- **Solo técnicas del curso**: `LinearRegression`, `DecisionTree`, `SVR`/`SVC`, `GaussianNB`, `KMeans`. Prohibido RandomForest, XGBoost, redes neuronales, Prophet, AutoML.
- **Patrón de celdas**: cada celda de código va precedida de un markdown breve y seguida de un análisis del output. Imports solo en la parte superior. Sin notas internas/conversación en el notebook (solo contenido evaluable).
- La rúbrica de referencia es el caso "Australia" — es **solo guía metodológica**; el dataset real es Chile y eso es válido, no es un error.

## Arquitectura del flujo de datos (lo que hay que entender leyendo varias celdas)

1. **Carga controlada** (Fase 2.0): loader CSV propio que corrige filas completamente entre comillas y mojibake latin1→utf-8. Produce `df`.
2. **`df_eda`**: copia con ingeniería de variables — macrozona, codificaciones, tratamiento de outliers (`agua_caida_tratada`, umbral físico 150 mm), `isoterma_cero`, lags de lluvia (`lluvia_ayer/prom_7d/acum_3d`, NaN→0 al inicio de serie), `mes_sin/cos`, `llueve`, `lluvia_intensa`, y `zona_koppen_codigo` (asignada por coordenada desde el raster Köppen vía PIL).
3. **Matrices supervisadas DIARIAS** (Fase 3.4–3.5): `X_train/X_test` (+ `_escalado`) con split **temporal** 2021-2023 / 2024-2025. Imputación (mediana train) y `StandardScaler` ajustados solo en train. Se usan para **clasificación y cluster**.
4. **Segundo dataset atmosférico + regresión de TEMPERATURA** (Fase 2/3.5/4.1): se incorpora `atmosfera_diario.csv` (reanálisis **NASA POWER** por estación×día 2021-2025: humedad, viento dir/vel, radiación, radiacion_lw, indice_claridad, presión, nubosidad, rocío, hum_especifica, nasa_temp, nasa_precip). Subido al repo de datos; el notebook lo carga **por URL** (`{RAW_BASE}/atmosfera_diario.csv`, prefiere URL salvo copia local). Se trata en Fase 2 (describe/nulos/correlación). La **regresión predice `temp_media` (temperatura media diaria)**, no la lluvia: features = ubicación + mes_cos/sin + atmosféricas + nasa_temp + temp_ayer; se EXCLUYEN temp_max/min/rango_termico/isoterma_cero (fuga). La **lluvia se modela como CLASIFICACIÓN**.

**9 modelos**: 3 regresión (**temperatura diaria**, R²≈0.93), 3 clasificación (`llueve`, `lluvia_intensa`, diario), 3 cluster (KMeans estaciones/días).

**Hallazgo clave**: la lluvia diaria es ~89% ruido temporal (techo R²≈0.18-0.42 aun con atmosférico) → es caos físico, no se puede llevar a R²≥0.7 a escala diaria. Por eso la regresión pasó a **temperatura** (predecible, R²≈0.93) y la lluvia quedó como clasificación. Si se requiere regresión de LLUVIA con R²≥0.7, el camino es **Köppen×mes + atmosférico** (R²≈0.84). Detalle y escalas probadas en la memoria de Claude (`.claude/.../memory/`).

## Trabajar con el notebook (importante)

- El `.ipynb` pesa ~6 MB por los PNG embebidos. **No lo leas entero con Read** (revienta el contexto). Para inspeccionar/editar, opera por `cell_id` con un script Python pequeño que cargue el JSON (`json.load`), o usa `NotebookEdit`.
- Para verificar métricas tras un cambio: ejecuta con `nbconvert` y parsea los outputs de las celdas (las tablas son `text/html`), no releyendo el archivo.
- El notebook está escrito en español **sin tildes** en código/markdown (estilo consistente: "Analisis", "regresion"); mantenerlo así.
