# Estado y mejoras — `EV2_BIY7121_005D.ipynb`

Documento de seguimiento. Proyecto CRISP-DM sobre clima de Chile (109 estaciones, 2021–2025), con dos datasets: meteorológico DMC (medido) + atmosférico NASA POWER (reanálisis).

## Estado actual (medido, sin optimismo)

- **Regresión = temperatura media diaria.** SVR R²=0.931, Lineal 0.918, Árbol 0.908; RMSE ≈ 1.5 °C. Sin sobreajuste (brecha train-test ≈0). Es el resultado más sólido del notebook. Nota: es temperatura, no lluvia — la temperatura es intrínsecamente más predecible.
- **Clasificación = `llueve`** (binario, sí/no). 3 modelos × 3 versiones. Árbol F1=0.766 (balanced acc 0.836), SVM F1=0.744, Logística F1=0.676. Aceptable, no sobresaliente.
- **Cluster = perfiles de estaciones.** K-Means K=4 silhouette=0.481, K-Means K=3 0.470, Jerárquico K=3 0.438. Cohesión baja pero los grupos tienen sentido geográfico (norte/centro/sur).
- **Datos en memoria** desde raw GitHub (`4mnesia/DataMining_Datos`), sin archivos locales.
- Notebook ejecuta de punta a punta sin errores ni warnings. Numeración monótona.

## Decisiones de fondo (no revertir sin leer la evidencia)

- **La regresión NO es de lluvia.** Se midió que la lluvia diaria es ~89% ruido temporal (techo R²≈0.18-0.42 aun con datos atmosféricos). Por eso la regresión pasó a temperatura. La descomposición de varianza está en la Fase 5.
- **`lluvia_intensa` NO se modela.** Quedó como variable descriptiva de EDA. Es rara (~4.6%) y caótica a escala diaria; un clasificador no entrega valor confiable. Definida con regla de Tukey (Q3+1.5·IQR), con versión "sostenida" (persistencia con lags).
- **Cluster excluye 2 estaciones insulares** (Isla de Pascua, Juan Fernández): colapsaban el modelo (todo en 1 grupo). Sin ellas, los clusters segmentan de verdad.
- **Agua precipitable (PW/TQV) de NASA descartada:** se midió, es redundante con humedad/rocío, no aporta.

## Mejoras ya aplicadas

- Pivote de regresión a temperatura tras diagnóstico de varianza.
- Segundo dataset NASA POWER integrado y tratado en Fase 2.
- Desbalance de clasificación: `class_weight='balanced'` + ajuste de umbral en validación + `nasa_precip` y lags.
- Hiperparámetros por validación cruzada con heatmaps (regresión) y 3 versiones por modelo (clasificación).
- Métricas completas en Fase 5: accuracy, balanced accuracy, precision, recall, F1, matrices de confusión, ROC, residuos, codo, silhouette, dendrograma.
- Gráficos propios por modelo: coeficientes (lineal), árbol visual + importancia (árbol), curva de respuesta (SVR), centroides y dendrograma (cluster), SVM 2D ilustrativo.
- Reorganización por `estructura.txt`: exploratorio en Fase 2, Fase 3 solo escalamiento.
- Limpieza: sin mini-objetivos, sin referencias al material de apoyo, sin imports muertos, sin tablas innecesarias, 0 warnings.

## Pendiente (ver `tareas.md` para el detalle)

- **Fase 6 Deploy: VACÍA.** Es lo único de la estructura sin hacer. Ficha de despliegue + integración con toma de decisiones (material 3.6).
- Revisar coherencia de textos sobre `lluvia_intensa`, cluster y SVM 2D.
- Opcional (no para la entrega): subir silhouette del cluster requeriría agregación a escala mayor o menos variables; subir clasificación de lluvia intensa requeriría escala semanal/zonal.

## Reglas que se mantienen

`estructura.txt` intacta; solo técnicas estándar (sin RandomForest/XGBoost/redes); sin citar material de apoyo en el notebook; imports arriba; sin fuga (imputación/escalado solo en train, split temporal 2021-23 / 2024-25); Fase 4 vista general / Fase 5 evaluación; español sin tildes.
