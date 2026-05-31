# Estado y mejoras — `EV2_BIY7121_005D.ipynb`

Documento de seguimiento del notebook (estado actual y mejoras pendientes). Proyecto CRISP-DM sobre clima de Chile (109 estaciones, 2021–2025), con dos datasets: meteorológico DMC + atmosférico NASA POWER.

## Estado actual

- **Regresión = temperatura media diaria**, R² ≈ **0.93** (SVR; Lineal 0.92, Árbol 0.91), RMSE ≈ 1.5 °C. Cumple el requisito de R² ≥ 0.7 a escala diaria.
- **Clasificación = lluvia** (`llueve`, `lluvia_intensa`) y **cluster** de estaciones/días: quedaron como **esqueleto** (encabezados + celda vacía) para reinsertar.
- **Datos en memoria** desde raw GitHub (sin `.data/` ni clon). Dataset atmosférico **NASA POWER** integrado y citado como fuente.
- Notebook ejecuta de punta a punta (`python -m nbconvert ... --execute`).

## Mejoras ya aplicadas

- **Diagnóstico del R²:** se probó que la lluvia diaria es ~89% ruido temporal (techo ~0.18–0.42); se pivotó la regresión a **temperatura** (predecible) y la lluvia quedó como clasificación.
- **Segundo dataset (NASA POWER):** humedad, viento, radiación, nubosidad, presión por estación-día; tratado en Fase 2 (estadísticos, nulos, correlación) y usado como predictores.
- **Hiperparámetros por validación cruzada** con **heatmaps** de comparación (árbol `max_depth × min_samples_leaf`; SVR `C × gamma`) en 4.1.2, sin fuga.
- **Fase 1 afinada:** fuentes correctas (raw, sin `.data/`), 2 datasets, **4 hipótesis** centradas en patrones de datos (ya no se "validan" con métricas de Fase 4), KPIs con énfasis.
- **Limpieza/orden:** eliminados los "mini-objetivos"; cada paso queda **encabezado → código → análisis**. Reducidas las "tablas de la nada": `df.dtypes`, `print`, `df.describe()` en duro; conceptos movidos a markdown.
- **Énfasis** en nulos, outliers, encoding y escalamiento (qué/por qué/cómo; incl. `StandardScaler` solo en train).
- **Markdown coherente:** sin referencias obsoletas (regresión de lluvia, mensual, Köppen×mes, hurdle, `.data/`, clon).

## Pendiente / mejoras futuras

- **Insertar clasificación (4.2) y cluster (4.3)** en el esqueleto, con sus métricas (matriz de confusión / silhouette) y análisis.
- **Reconstruir Fase 5** (consolidación de las tres familias) y **Fase 6 Deploy** tras insertar los modelos.
- Opcional: validación temporal adicional, manejo del desbalance en `lluvia_intensa`, más años de datos para estabilizar la climatología.

## Reglas que se mantienen

Estructura `estructura.txt` intacta; solo técnicas del curso; imports arriba; sin fuga (imputación/escalado solo en train, split temporal 2021-23 / 2024-25); estilo español sin tildes.
