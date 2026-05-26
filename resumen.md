# Resumen Del Proyecto

Este archivo resume el estado actual del proyecto para poder retomarlo en otro equipo, otra sesion o con otro agente sin perder contexto.

## Contexto

Proyecto EV2 de Mineria de Datos BIY7121-005D sobre precipitaciones, temperaturas y estaciones meteorologicas de Chile. El trabajo se organiza bajo CRISP-DM y el notebook principal es `EV2_BIY7121_005D.ipynb`.

El usuario pidio mantener el proyecto paso a paso, con codigo limpio, pocas celdas, buena justificacion en Markdown y sin redundancia. Tambien pidio no mostrar de forma explicita en el notebook referencias a archivos guia internos, para que el trabajo se vea natural y profesional.

## Decision central

El proyecto estaba en riesgo de perder foco por tener regresion, clasificacion y cluster al mismo nivel. Se corrigio la narrativa:

- Foco principal: predecir `lluvia_intensa`.
- Regresion: apoyo para estimar `agua_caida_tratada`.
- Cluster: apoyo descriptivo para agrupar estaciones.

Esta decision conecta mejor con riesgo climatico, alerta y toma de decisiones.

## Avance por fase

Fase 1:

- Define contexto, datos, hipotesis, KPI y problema de negocio.
- Fue ajustada para dejar `lluvia_intensa` como problema predictivo principal.

Fase 2:

- Carga y entendimiento de datos.
- Analisis de tipos, nulos, outliers, distribuciones, correlaciones y patrones.
- Incluye analisis temporal por periodo y visualizaciones territoriales.
- Se agrego un pairplot enfocado en predictores numericos para `lluvia_intensa`, evitando `agua_caida_tratada` como eje para no introducir fuga visual.

Fase 3:

- Preparacion de datos.
- Define variables predictoras, objetivo y excluidas.
- Aplica imputacion simple con train.
- Codifica categoricas con one-hot.
- Escala variables numericas para SVM y modelos de clustering.
- Corrige split para estratificar por `lluvia_intensa`.
- Construye `X_cluster_escalado` para modelos no supervisados.

Fase 4:

- Completada con modelos entrenados y metricas.
- Incluye 3 modelos de regresion, 3 de clasificacion y 3 modelos de cluster.
- Incluye matriz de confusion, perfil de clusters, visualizacion territorial y sintesis final.

Fase 5:

- Pendiente.
- Debe evaluar formalmente los resultados, especialmente el trade-off de clasificacion.

Fase 6:

- Pendiente.
- Debe proponer despliegue conceptual y limitaciones.

## Variables importantes

Objetivos:

- `agua_caida_tratada`: objetivo de regresion.
- `lluvia_intensa`: objetivo principal de clasificacion.
- `llueve`: variable auxiliar para EDA y KPI.

Predictoras principales:

- Geograficas: `altura`, `latitud`, `longitud`.
- Temporales: `anio`, `mes`, `mes_sin`, `mes_cos`, `estacion_anio`.
- Termicas: `temp_max`, `temp_min`, `temp_media`, `rango_termico`.
- Territoriales/climaticas: `nombre_region`, `zona_geografica`, `macrozona`, `zona_koppen_codigo`.

Excluidas de `X`:

- `agua_caida`
- `codigo_estacion`
- `fecha`
- `dia`
- `nombre_estacion`
- variables objetivo cuando actuen como respuesta

## Resultados de Fase 4

Regresion:

| Modelo | MAE | RMSE | R2 |
|---|---:|---:|---:|
| Arbol regresor | 1.285 | 3.026 | 0.181 |
| Regresion lineal | 1.452 | 3.094 | 0.143 |
| SVM lineal regresion | 1.019 | 3.436 | -0.057 |

Clasificacion:

| Modelo | Accuracy | Precision | Recall | F1 | TP | FP | FN | TN |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Arbol clasificador | 0.762 | 0.151 | 0.804 | 0.254 | 1730 | 9756 | 423 | 30795 |
| SVM lineal | 0.743 | 0.145 | 0.833 | 0.246 | 1794 | 10609 | 359 | 29942 |
| Naive Bayes | 0.523 | 0.090 | 0.924 | 0.163 | 1990 | 20211 | 163 | 20340 |

Cluster:

| Modelo | Clusters | Ruido | Inercia | Silhouette |
|---|---:|---:|---:|---:|
| K-Means | 4 | 0 | 187.470 | 0.522 |
| Agglomerative | 4 | 0 | No aplica | 0.516 |
| DBSCAN | 1 | 11 | No aplica | 0.470 |

Sintesis:

- Mejor regresion: Arbol regresor.
- Mejor clasificacion: Arbol clasificador.
- Mejor cluster: K-Means.
- Lectura principal: la clasificacion detecta gran parte de eventos intensos, pero con baja precision. En evaluacion hay que decidir si se privilegia alerta amplia o precision operativa.

## Preferencias del usuario

- Avanzar paso a paso.
- No rehacer partes ya buenas.
- No usar codigo enorme.
- No crear codigo ni DataFrames innecesarios.
- Mantener trazabilidad con Markdown antes y despues del codigo.
- Seguir estructura CRISP-DM.
- Mantener alineacion con la problematica.
- No dejar frases que parezcan relleno o que expongan demasiado la pauta interna.

## Validacion reciente

Comando usado:

```powershell
py -3 -m jupyter nbconvert --to notebook --execute EV2_BIY7121_005D.ipynb --inplace --ExecutePreprocessor.timeout=1800
```

Resultado:

- Notebook ejecutado completo.
- Errores guardados en outputs: `0`.
- Warnings guardados en outputs: `0`.

## Proximo trabajo sugerido

Continuar con Fase 5:

1. Evaluar el modelo destacado de clasificacion con foco en `lluvia_intensa`.
2. Explicar por que `accuracy` no es suficiente.
3. Interpretar matriz de confusion y falsos negativos.
4. Justificar si se prioriza `recall` por alerta temprana.
5. Comparar con regresion y cluster solo como apoyo.
6. Proponer mejoras realistas sin reescribir todo el notebook.

## Checklist De Rubrica

La entrega esta bien encaminada, pero para apuntar a dominio alto/excelente se debe cerrar lo siguiente:

| Criterio de rubrica | Estado | Accion recomendada |
|---|---|---|
| Deteccion de valores atipicos | Cubierto | Agregar tabla final de decision por variable |
| Valores inexistentes/nulos | Cubierto | Mantener evidencia de nulos antes y despues del tratamiento |
| Transformacion de variables | Cubierto | No duplicar transformaciones; explicar que modelo requiere escalamiento |
| Interpretacion de resultados | Parcial para cierre final | Completar Fase 5 con lectura de negocio |
| Insights de alto impacto | Cubierto, reforzable | Agregar sintesis final de insights |
| 6 hipotesis sustentadas | Parcial | Agregar tabla `hipotesis/evidencia/decision` |
| 9 modelos implementados | Cubierto | Mantener 3 regresion, 3 clasificacion y 3 cluster |
| Deploy | Pendiente | Agregar Fase 6 conceptual |

Advertencia para otro agente:

- No cambiar el foco principal: `lluvia_intensa`.
- No eliminar K-Means; es el cluster mas respaldado por el material.
- No presentar Agglomerative y DBSCAN como si fueran del material principal; justificarlos como complementarios.
- No agregar codigo largo ni preparaciones duplicadas.
