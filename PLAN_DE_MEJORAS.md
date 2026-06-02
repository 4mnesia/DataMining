# Estado y mejoras — `EV2_BIY7121_005D.ipynb`

Documento de seguimiento del notebook (estado actual y mejoras pendientes). Proyecto CRISP-DM sobre clima de Chile (109 estaciones, 2021–2025), con dos datasets: meteorológico DMC + atmosférico NASA POWER.

## Estado actual

- **Regresión = temperatura media diaria**, R² ≈ **0.93** (SVR; Lineal 0.92, Árbol 0.91), RMSE ≈ 1.5 °C. Cumple R² ≥ 0.7 a escala diaria. Hiperparámetros elegidos por validación cruzada con heatmaps (4.1.2).
- **Clasificación = lluvia** (`llueve`, `lluvia_intensa`), 3 modelos (Logística, SVM, Árbol) × 3 versiones c/u. Vista general en Fase 4.2; evaluación en Fase 5.
  - `llueve`: Árbol F1 0.765 (balanced acc 0.838), SVM 0.744, Logística 0.677.
  - `lluvia_intensa`: Logística F1 0.520 (balanced acc 0.822), Árbol 0.502, SVM 0.407.
- **Cluster (4.3):** pendiente (esqueleto con encabezados + celdas vacías).
- **Datos en memoria** desde raw GitHub (sin `.data/` ni clon). Dataset atmosférico NASA POWER integrado, citado y subido al repo de datos.
- Notebook ejecuta de punta a punta (`python -m nbconvert ... --execute`).

## Mejoras ya aplicadas

- **Diagnóstico del R²:** se probó que la lluvia diaria es ~89% ruido temporal (techo ~0.18–0.42); se pivotó la regresión a **temperatura** (predecible) y la lluvia quedó como clasificación.
- **Segundo dataset (NASA POWER):** integrado y tratado en Fase 2 (estadísticos, nulos, correlación). Se midió que el agua precipitable (PW/TQV) NO aporta (redundante con humedad/rocío) → no se agregó.
- **Manejo del desbalance en clasificación:** `class_weight='balanced'` + **ajuste de umbral** en validación (sube `lluvia_intensa` de F1≈0.32 a ≈0.52) + features `nasa_precip` y lags atmosféricos.
- **Hiperparámetros por validación cruzada** con heatmaps (árbol `max_depth × min_samples_leaf`; SVR `C × gamma`); en clasificación 3 versiones por modelo.
- **Métricas completas en Fase 5:** accuracy, **balanced accuracy** (clave con desbalance), precision, recall, F1, classification_report, matrices de confusión y análisis de sobreajuste (3 versiones).
- **Fase 1 afinada:** fuentes correctas (raw, sin `.data/`), 2 datasets, **4 hipótesis** centradas en patrones de datos (no en métricas de Fase 4), KPIs con énfasis.
- **Reorganización por estructura.txt:** todo lo exploratorio (Köppen, mapa, categóricas, transformaciones) movido a Fase 2 (2.11–2.14); **Fase 3 = solo escalamiento**.
- **Limpieza/orden:** eliminados los "mini-objetivos" y las referencias al material de apoyo; cada paso queda **encabezado → código → análisis**; menos "tablas de la nada" (`df.dtypes`, `print`, `df.describe()`).
- **Markdown y `.md` coherentes:** sin referencias obsoletas (regresión de lluvia, mensual, Köppen×mes, hurdle, `.data/`, clon, material de apoyo).

## Pendiente / mejoras futuras

- **Armar cluster (4.3):** K-Means con 3 valores de K, vista general en Fase 4; **gráfico de codo + silhouette** y evaluación en Fase 5.
- **Reconstruir Fase 5 consolidada** (las tres familias juntas) y **Fase 6 Deploy** tras el cluster.
- Opcional: `lluvia_intensa` sube fuerte solo agregando en escala (semana/zona); a escala diaria está cerca de su techo (evento raro y caótico).

## Reglas que se mantienen

Estructura `estructura.txt` intacta; solo técnicas estándar (sin RandomForest/XGBoost/redes); sin citar material de apoyo; imports arriba; sin fuga (imputación/escalado solo en train, split temporal 2021-23 / 2024-25); Fase 4 vista general / Fase 5 evaluación; estilo español sin tildes.
