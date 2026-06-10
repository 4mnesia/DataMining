# Estado y mejoras — `EV2_BIY7121_005D.ipynb`

Documento de seguimiento. Proyecto CRISP-DM sobre clima de Chile (109 estaciones, 2021–2025), con dos datasets: meteorológico DMC (medido) + atmosférico NASA POWER (reanálisis).

## Estado actual (medido, sin optimismo)

- **Regresión = temperatura media diaria.** SVR R²=0.931, Lineal 0.918, Árbol 0.908; RMSE ≈ 1.5 °C. Sin sobreajuste (brecha train-test ≈0). Es el resultado más sólido del notebook. Nota: es temperatura, no lluvia — la temperatura es intrínsecamente más predecible.
- **Pronóstico = `llueve`** (binario, sí/no). 3 modelos × 3 versiones, versión elegida por **validación cruzada**. Árbol F1=0.767 (balanced acc 0.837), SVM F1=0.741, Logística F1=0.672. **Pronóstico honesto:** sin temperatura del mismo día (solo info de antemano). Aceptable, no sobresaliente.
- **Cluster = zonas climáticas Köppen, K=7** (zonas con ≥3 estaciones, de 10 presentes; 3 marginales Csc/Dfc/ET con 1-2 est.). Variables = clima MEDIDO (temperatura + nubosidad + humedad + lluvia como feature + oscilación térmica diaria + índice de aridez (balance hídrico)), QuantileTransformer + PCA-3, sin insulares. **Köppen NO es variable: etiqueta externa.** Validación **intrínseca**: Hopkins=0.68, K-Means K=7 silhouette=0.413, Calinski=114.5, codo=K=5. Validación **extrínseca vs Köppen**: K-Means K=7 ARI=0.309, NMI=0.493, Fowlkes-Mallows=0.421 (el mejor recuperando zonas). **El perfil estacional subió la recuperación de Köppen (ARI 0.263→0.322)** porque Köppen se define por la estacionalidad (Cs = verano seco). Marco completo del PPT del Módulo 3. **Dos granularidades (2 hipótesis):** HK1 = espacial (estaciones→Köppen); HK2 = temporal = clustering de mediciones mensuales normalizadas por estación → 4 estaciones del año (ARI=0.34, NMI=0.39 vs estación).
- **Datos en memoria** desde raw GitHub (`4mnesia/DataMining_Datos`), sin archivos locales.
- Notebook ejecuta de punta a punta sin errores ni warnings. Numeración monótona.

## Decisiones de fondo (no revertir sin leer la evidencia)

- **La regresión NO es de lluvia.** Se midió que la lluvia diaria es ~89% ruido temporal (techo R²≈0.18-0.42 aun con datos atmosféricos). Por eso la regresión pasó a temperatura. La descomposición de varianza está en la Fase 5.
- **`lluvia_intensa` NO se modela.** Quedó como variable descriptiva de EDA. Es rara (~4.6%) y caótica a escala diaria; un clasificador no entrega valor confiable. Definida con regla de Tukey (Q3+1.5·IQR), con versión "sostenida" (persistencia con lags).
- **Cluster excluye 2 estaciones insulares** (Isla de Pascua, Juan Fernández): colapsaban el modelo (todo en 1 grupo). Sin ellas, los clusters segmentan de verdad.
- **Agua precipitable (PW/TQV) de NASA descartada:** se midió, es redundante con humedad/rocío, no aporta.
- **Recuperación de Köppen — tres enfoques (diagnóstico):** **KNN supervisado (QuantileTransformer, k=1, leave-one-out, tras explorar escalado/k/pesos): ~90% gran clase, ~70% zona, ARI 0.51** > **Köppen por reglas desde el dato: ~81% gran clase, ARI 0.40** > **K-Means no supervisado: ARI 0.32**. Confirma que **no falta una variable** — con temp y precip mensual (lo que Köppen usa) el clima medido SÍ determina la zona (~90% macro con KNN). El techo del K-Means es metodológico (agrupa por distancia, no aplica reglas ni usa etiquetas) + desajuste raster vs medición (2021-2025 más seco → algunas estaciones norte miden B donde el raster dice C). No reabrir como "falta una variable". KNN es del material 2.7 (vecindad); k=1 gana porque la estación más parecida casi siempre comparte zona.

## Mejoras ya aplicadas

- Pivote de regresión a temperatura tras diagnóstico de varianza.
- Segundo dataset NASA POWER integrado y tratado en Fase 2.
- Desbalance de clasificación: `class_weight='balanced'` + ajuste de umbral en validación + `nasa_precip` y lags.
- **Reformulación a pronóstico honesto:** la clasificación de `llueve` excluye la temperatura del mismo día (solo info de antemano).
- Hiperparámetros por validación cruzada con heatmaps (regresión); versión por **validación cruzada 3-fold** y 3 versiones por modelo (clasificación).
- **EDA discriminante del pronóstico** (Fase 5): AUC univariada por variable + desempeño por zona/mes. Se probó umbral por zona (peor que el global, descartado).
- Métricas completas en Fase 5: clasificación (accuracy, balanced accuracy, precision, recall, F1, matrices de confusión, ROC); regresión (R²/RMSE, residuos); cluster según el PPT del Módulo 3 (tendencia/Hopkins; codo/KneeLocator; intrínseca silhouette+Calinski-Harabasz; extrínseca vs Köppen ARI+NMI+Fowlkes-Mallows; dendrograma).
- Gráficos propios por modelo: coeficientes (lineal), árbol visual + importancia (árbol), curva de respuesta (SVR), centroides y dendrograma (cluster), SVM 2D ilustrativo (junto a las matrices de confusión).
- Reorganización por `estructura.txt`: exploratorio en Fase 2, Fase 3 solo escalamiento. **2 matrices de correlación (inicial 2.6 + general 2.9)**; **6 hipótesis (2 por familia, trazables)**.
- Limpieza: sin mini-objetivos, sin referencias al material de apoyo, sin imports muertos, sin tablas innecesarias, 0 warnings.

## Pendiente (ver `tareas.md` para el detalle)

- **Fase 6 Deploy: VACÍA.** Es lo único de la estructura sin hacer. Ficha de despliegue + integración con toma de decisiones (material 3.6).
- Revisar coherencia de textos sobre `lluvia_intensa`, cluster y SVM 2D.
- Opcional (no para la entrega): el cluster usa K=7 con silhouette ~0.42 (sumar nubosidad/humedad/oscilación + PCA subió la cohesión); la concordancia con Köppen (ARI ~0.31) está limitada porque Köppen usa umbrales mensuales que el cluster no replica y 3 zonas tienen 1-2 estaciones. Subir el ARI requeriría más años de datos o método supervisado (KNN ya da ~90% gran clase). Subir la clasificación de `llueve` (F1≈0.77) requeriría escala semanal/zonal (la lluvia diaria es caótica).

## Reglas que se mantienen

`estructura.txt` intacta; solo técnicas estándar (sin RandomForest/XGBoost/redes); sin citar material de apoyo en el notebook; imports arriba; sin fuga (imputación/escalado solo en train, split temporal 2021-23 / 2024-25); Fase 4 vista general / Fase 5 evaluación; español sin tildes.
