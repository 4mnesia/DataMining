# Medidas DAX para Power BI — panel_powerbi.csv (dataset completo)

El CSV trae **todo el dataset tratado** (todas las filas). Las predicciones (`llueve_pred`, `temp_pred`)
solo existen en las filas de prueba, marcadas por la columna **`conjunto = "test"`**.
Por eso **todas las medidas de desempeño filtran `conjunto = "test"`** (si no, las filas de train ensucian RMSE/R²).

Valores reales = columnas nativas `llueve` (lluvia 0/1) y `temp_media` (temperatura °C).
Asume que la tabla importada se llama **`panel_powerbi`**.

---

## Lluvia (clasificación — Árbol de decisión)

```DAX
N Dias Test = CALCULATE(COUNTROWS('panel_powerbi'), 'panel_powerbi'[conjunto] = "test")
```
```DAX
Accuracy Lluvia = CALCULATE(AVERAGE('panel_powerbi'[acierto_lluvia]), 'panel_powerbi'[conjunto] = "test")
```
```DAX
VP = CALCULATE(COUNTROWS('panel_powerbi'), 'panel_powerbi'[conjunto] = "test", 'panel_powerbi'[llueve] = 1, 'panel_powerbi'[llueve_pred] = 1)
```
```DAX
FP = CALCULATE(COUNTROWS('panel_powerbi'), 'panel_powerbi'[conjunto] = "test", 'panel_powerbi'[llueve] = 0, 'panel_powerbi'[llueve_pred] = 1)
```
```DAX
FN = CALCULATE(COUNTROWS('panel_powerbi'), 'panel_powerbi'[conjunto] = "test", 'panel_powerbi'[llueve] = 1, 'panel_powerbi'[llueve_pred] = 0)
```
```DAX
VN = CALCULATE(COUNTROWS('panel_powerbi'), 'panel_powerbi'[conjunto] = "test", 'panel_powerbi'[llueve] = 0, 'panel_powerbi'[llueve_pred] = 0)
```
```DAX
Recall Lluvia = DIVIDE([VP], [VP] + [FN])
```
```DAX
Precision Lluvia = DIVIDE([VP], [VP] + [FP])
```
```DAX
F1 Lluvia = DIVIDE(2 * [Precision Lluvia] * [Recall Lluvia], [Precision Lluvia] + [Recall Lluvia])
```

---

## Temperatura (regresión — SVR)

```DAX
MAE Temp = CALCULATE(AVERAGE('panel_powerbi'[temp_error_abs]), 'panel_powerbi'[conjunto] = "test")
```
```DAX
RMSE Temp =
CALCULATE(
    SQRT( AVERAGEX('panel_powerbi', ('panel_powerbi'[temp_media] - 'panel_powerbi'[temp_pred]) ^ 2) ),
    'panel_powerbi'[conjunto] = "test"
)
```
```DAX
Sesgo Temp =
CALCULATE(
    AVERAGEX('panel_powerbi', 'panel_powerbi'[temp_media] - 'panel_powerbi'[temp_pred]),
    'panel_powerbi'[conjunto] = "test"
)
```
```DAX
R2 Temp =
CALCULATE(
    VAR Media = AVERAGE('panel_powerbi'[temp_media])
    VAR SS_res = SUMX('panel_powerbi', ('panel_powerbi'[temp_media] - 'panel_powerbi'[temp_pred]) ^ 2)
    VAR SS_tot = SUMX('panel_powerbi', ('panel_powerbi'[temp_media] - Media) ^ 2)
    RETURN 1 - DIVIDE(SS_res, SS_tot),
    'panel_powerbi'[conjunto] = "test"
)
```

---

## Cómo usarlas en el panel

- **Tarjetas (KPI):** `Accuracy Lluvia`, `F1 Lluvia`, `Recall Lluvia` (formato %); `RMSE Temp`, `MAE Temp`, `R2 Temp`.
- **Acierto por zona/mes:** matriz con `zona_koppen_codigo` o `mes` en filas y `Accuracy Lluvia` / `Recall Lluvia` en valores.
- **Matriz de confusión:** tabla con `[VP]`, `[FP]`, `[FN]`, `[VN]`.
- **Temperatura real vs predicha:** líneas por `fecha` con `temp_media` (real) y `temp_pred`; tarjeta `RMSE Temp`.
- **Mapa estratégico:** dispersión `latitud`/`longitud` coloreada por `cluster_grupo` o `cluster_koppen_dominante` (usa todas las filas, no requiere filtro de conjunto).

> Para gráficos de las variables tratadas (humedad, radiación, lluvia mm, etc.) NO filtres por conjunto: esas columnas están completas en todas las filas.
