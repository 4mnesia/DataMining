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

- **Seguir `estructura.txt` al pie de la letra**: 6 fases (1 Negocio, 2 Datos, 3 Transformación, 4 Modelamiento, 5 Evaluación, 6 Deploy) con su numeración. **No inventar ni renombrar secciones numeradas.** Todo lo exploratorio (Köppen, mapas, categóricas, transformaciones) vive en **Fase 2** (2.11–2.14); **Fase 3 = solo escalamiento** (3.1).
- **Solo técnicas estándar** (las del curso): `LinearRegression`, `LogisticRegression`, `DecisionTree`, `SVR`/`SVC`, `GaussianNB`, `KMeans`, `AgglomerativeClustering`, `KNeighborsClassifier` (KNN, material 2.7 "vecindad"). Prohibido RandomForest, XGBoost, redes neuronales, Prophet, AutoML.
- **Patrón de celdas**: encabezado breve → celda de código → análisis del output (sin "mini-objetivos"). Imports solo arriba. Sin notas internas/conversación. **No citar el material de apoyo ni módulos del curso** en el notebook (es contexto del asistente, no para el profe).
- **Fase 4 = vista general** (gráficos generales, reportes por familia); **Fase 5 = evaluación** (métricas, matrices de confusión, codo/silhouette, sobreajuste). De cada modelo se prueban **3 versiones** de hiperparámetros, la mejor por validación.
- La rúbrica de referencia es el caso "Australia" — es **solo guía metodológica**; el dataset real es Chile y eso es válido, no es un error.

## Arquitectura del flujo de datos (lo que hay que entender leyendo varias celdas)

1. **Carga controlada** (Fase 2.0): loader CSV propio que corrige filas completamente entre comillas y mojibake latin1→utf-8. Produce `df`.
2. **`df_eda`**: copia con ingeniería de variables — macrozona, codificaciones, tratamiento de outliers (`agua_caida_tratada`, umbral físico 150 mm), `isoterma_cero`, lags de lluvia (`lluvia_ayer/prom_7d/acum_3d`, NaN→0 al inicio de serie), `mes_sin/cos`, `llueve`, `lluvia_intensa`, y `zona_koppen_codigo` (asignada por coordenada desde el raster Köppen vía PIL).
3. **Matrices supervisadas DIARIAS** (Fase 3.4–3.5): `X_train/X_test` (+ `_escalado`) con split **aleatorio** 70/30. Imputación (mediana train) y `StandardScaler` ajustados solo en train. Se usan para **clasificación y cluster**.
4. **Segundo dataset atmosférico + regresión de TEMPERATURA** (Fase 2/3.5/4.1): se incorpora `atmosfera_diario.csv` (reanálisis **NASA POWER** por estación×día 2021-2025: humedad, viento dir/vel, radiación, radiacion_lw, indice_claridad, presión, nubosidad, rocío, hum_especifica, nasa_temp, nasa_precip). Subido al repo de datos; el notebook lo carga **por URL** (`https://raw.githubusercontent.com/4mnesia/DataMining_Datos/refs/heads/main/atmosfera_diario.csv`). Se trata en Fase 2 (describe/nulos/correlación). La **regresión predice `temp_media` (temperatura media diaria)**, no la lluvia: features = ubicación + mes_cos/sin + atmosféricas + nasa_temp + temp_ayer; se EXCLUYEN temp_max/min/rango_termico/isoterma_cero (fuga). La **lluvia se modela como CLASIFICACIÓN**.

**Modelos**: **regresión** (temperatura diaria, R²≈0.93, hiperparámetros por CV con heatmaps en 4.1.2), **clasificación/pronóstico** de lluvia (`llueve` binario, F1≈0.79 con Árbol depth=16; 3 modelos Logística/SVM/Árbol × 3 versiones, **versión elegida por validación cruzada 3-fold**; desbalance manejado con `class_weight='balanced'` + **ajuste de umbral** en validación + features `nasa_precip` y lags atmosféricos), y **cluster** de estaciones (**K=7** = zonas Köppen con ≥3 estaciones, de 10 presentes; variables = clima MEDIDO (precipitación anual + temperatura media + índice de aridez De Martonne = P_anual/(T+10), el balance hídrico que define la clase árida B), QuantileTransformer + PCA-2 —las 3 variables núcleo que define Köppen (precip, temp, aridez) maximizan la recuperación—, **Köppen NO es variable sino etiqueta externa**; K-Means K=7 mejor extrínseca: silhouette≈0.47, ARI≈0.33 / NMI≈0.53 / FM≈0.44 vs Köppen; sin insulares. Se probó **radiación/índice de claridad** y NO ayuda (redundante con nubosidad/humedad; la radiación cruda mete ruido de latitud); el índice de aridez sí sube algo la detección del desierto (recall clase B ~65→70%, techo por las BSk de transición). **Reformulación a pronóstico honesto**: la clasificación de `llueve` **EXCLUYE la temperatura del mismo día** (`temp_max/min/media`, `rango_termico`, `isoterma_cero`) — usa solo info disponible de antemano (ubicación, mes, lags de lluvia, atmosféricas). Se probó un **umbral por zona climática** y resultó peor que el global (documentado y descartado). En Fase 5 hay un **EDA discriminante** (AUC univariada por variable + desempeño por zona/mes). `lluvia_intensa` **NO se modela** (solo EDA descriptiva). Métricas de clasificación incluyen accuracy y **balanced_accuracy**. Métricas de cluster **según el PPT del Módulo 3 (3.4.1), 3 tareas**: (1) **tendencia** = **Hopkins** (≈0.68); (2) **número de clusters** = **codo/elbow** con `KneeLocator`/kneed (K=4) + silhouette por K, aunque el K se ancla a las zonas Köppen (7); (3) **calidad** = **intrínseca** (silhouette + inercia + **Calinski-Harabasz**) y **extrínseca vs Köppen** (ARI + NMI + **Fowlkes-Mallows**). El modelo destacado es el de mejor ARI (recupera Köppen). **Calinski-Harabasz SÍ se usa** (lo exige el PPT); Davies-Bouldin no. **Dos granularidades / 2 hipótesis del cluster:** **HK1 = espacial** (estaciones→zonas Köppen, lo anterior); **HK2 = temporal** = clustering de las mediciones mensuales **normalizadas por estación** (z-score intra-estación, muchas variables) en K=4 → recupera las **4 estaciones del año** (ARI≈0.34 / NMI≈0.39 vs la estación real; verano e invierno nítidos, otoño/primavera mezclados por ser transición). Hay ademas una **vista complementaria del cluster por regimen TERMICO** (solo temperatura: verano/invierno/amplitud): K=4, silhouette~0.46 (alto, la temperatura es limpia), 4 franjas (calido norte / templado costero / templado continental / frio austral). Capta el eje termico de Koppen (C/D/E) pero NO la aridez (B), ARI~0.10 vs gran clase -es complementaria, no reemplaza al cluster Koppen-.

**Estructura/trazabilidad (junio 2026)**: la Fase 2 tiene **exactamente 2 matrices de correlación** — inicial (2.6) y **general con todas las variables** (2.9, slot tras "Nuevas variables"); no hay matriz categórica aparte. La Fase 1.3 tiene **6 hipótesis (2 por familia de modelo)**, tipo patrón/relación, cada una **trazable** a una evidencia de Fases 4/5 (HR1/HR2 regresión, HC1/HC2 clasificación, **HK1 cluster espacial→Köppen / HK2 cluster temporal→4 estaciones del año**).

**Hallazgo clave**: la lluvia diaria es ~89% ruido temporal (techo R²≈0.18-0.42 aun con atmosférico) → es caos físico, no se puede llevar a R²≥0.7 a escala diaria. Por eso la regresión pasó a **temperatura** (predecible, R²≈0.93) y la lluvia quedó como clasificación. Si se requiere regresión de LLUVIA con R²≥0.7, el camino es **Köppen×mes + atmosférico** (R²≈0.84). El agua precipitable (PW/TQV) de NASA se midió y NO aporta (redundante con humedad). Detalle y escalas probadas en la memoria de Claude (`.claude/.../memory/`).

## Trabajar con el notebook (importante)

- El `.ipynb` pesa ~6 MB por los PNG embebidos. **No lo leas entero con Read** (revienta el contexto). Para inspeccionar/editar, opera por `cell_id` con un script Python pequeño que cargue el JSON (`json.load`), o usa `NotebookEdit`.
- Para verificar métricas tras un cambio: ejecuta con `nbconvert` y parsea los outputs de las celdas (las tablas son `text/html`), no releyendo el archivo.
- El notebook está escrito en español **sin tildes** en código/markdown (estilo consistente: "Analisis", "regresion"); mantenerlo así.
