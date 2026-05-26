# EV2 - Proyecto de Mineria de Datos

Proyecto academico de mineria de datos aplicado a precipitaciones, temperaturas y estaciones meteorologicas de Chile. El notebook principal sigue CRISP-DM y actualmente tiene desarrolladas las fases 1 a 4.

## Foco del proyecto

El foco predictivo principal es clasificar eventos de `lluvia_intensa`. La regresion y el clustering se usan como apoyo:

- Clasificacion: detectar registros con lluvia intensa.
- Regresion: estimar `agua_caida_tratada` como monto numerico de precipitacion.
- Cluster: segmentar estaciones meteorologicas con perfiles climaticos similares.

Esta decision evita que el proyecto se disperse entre demasiados objetivos y mantiene la problematica alineada con riesgo climatico y toma de decisiones.

## Archivos principales

| Archivo | Uso |
|---|---|
| `EV2_BIY7121_005D.ipynb` | Notebook principal del proyecto. |
| `estructura.txt` | Estructura base solicitada para el trabajo. |
| `requirements.txt` | Dependencias de Python necesarias. |
| `.data/` | Carpeta local de datos generada/usada por el notebook. |
| `material de apoyo/` | Archivos de respaldo del curso. |
| `resumen.md` | Memoria corta del estado actual del proyecto. |
| `AGENTS.md` | Instrucciones para continuar el trabajo con otro agente. |

## Estado actual

- Fase 1: entendimiento del negocio, hipotesis, KPI y foco predictivo.
- Fase 2: entendimiento de datos, nulos, outliers, distribuciones, correlaciones, mapas y patrones temporales/territoriales.
- Fase 3: preparacion, imputacion simple, encoding, escalamiento y dataset de cluster.
- Fase 4: modelamiento completo con 3 regresiones, 3 clasificaciones y 3 modelos de clustering.
- Fases 5 y 6: pendientes para evaluacion formal y despliegue.

## Resultados principales de Fase 4

| Familia | Modelo destacado | Criterio | Resultado |
|---|---|---|---|
| Regresion | Arbol regresor | Menor RMSE | `RMSE=3.026`, `R2=0.181` |
| Clasificacion | Arbol clasificador | Mayor F1 y recall | `F1=0.254`, `recall=0.804`, `precision=0.151`, `FN=423` |
| Cluster | K-Means | Mayor silhouette | `silhouette=0.522`, `inercia=187.470` |

Lectura breve: la clasificacion es el foco correcto, pero el problema es dificil por el desbalance. El mejor modelo detecta buena parte de los eventos intensos, aunque genera falsos positivos.

## Como ejecutar

Crear o activar un entorno de Python e instalar dependencias:

```powershell
py -3 -m pip install -r requirements.txt
```

Ejecutar el notebook completo:

```powershell
py -3 -m jupyter nbconvert --to notebook --execute EV2_BIY7121_005D.ipynb --inplace --ExecutePreprocessor.timeout=1800
```

Validacion mas reciente:

- Notebook ejecutado completo.
- Errores guardados en outputs: `0`.
- Warnings guardados en outputs: `0`.

## Consideraciones importantes

- No incluir `agua_caida`, `agua_caida_tratada`, `llueve` o `lluvia_intensa` dentro de `X` cuando sean objetivo o variables derivadas del objetivo.
- Mantener `lluvia_intensa` como foco principal de clasificacion.
- Evitar codigo redundante y DataFrames innecesarios.
- Mantener variables originales para auditoria cuando se creen variables tratadas o derivadas.
- Usar graficos temporales por periodo y visualizaciones territoriales cuando corresponda.

## Pendientes de alineacion con rubrica

Para fortalecer la entrega final:

- Completar Fase 5 con evaluacion formal de modelos.
- Completar Fase 6 con despliegue conceptual.
- Agregar tabla final de sustento de las 6 hipotesis.
- Profundizar outliers con una tabla de metodo, tratamiento y justificacion.
- Agregar graficos de evaluacion predictiva: comparacion de metricas de clasificacion y matriz de confusion visual.
- Justificar que Agglomerative y DBSCAN son modelos complementarios de clustering, mientras K-Means es el modelo base respaldado directamente por el material.
