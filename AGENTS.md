# Instrucciones Para Agentes

Este proyecto debe continuarse con cuidado porque el notebook ya tiene una narrativa definida. No rehacer desde cero salvo que el usuario lo pida explicitamente.

## Idioma y estilo

- Trabajar en espanol.
- Mantener tono academico, claro y trazable.
- Antes de agregar codigo, incluir Markdown breve que explique el objetivo y la decision.
- Despues de tablas o graficos importantes, incluir interpretacion breve.
- Evitar celdas largas y bloques de codigo gigantes.
- No crear DataFrames o variables auxiliares si ya existe una variable equivalente arriba.

## Foco analitico

El foco predictivo principal es `lluvia_intensa`.

- Clasificacion es la familia mas importante para la problematica.
- Regresion es apoyo para estimar `agua_caida_tratada`.
- Cluster es apoyo descriptivo para segmentar estaciones.
- No volver a presentar el proyecto como tres objetivos con igual prioridad.

## Reglas de trazabilidad

- Respetar CRISP-DM: negocio, datos, preparacion, modelamiento, evaluacion y despliegue.
- Separar explicitamente `X`, `Y` y variables excluidas.
- Advertir fuga de informacion cuando corresponda.
- Mantener originales para auditoria si se crean variables derivadas o tratadas.
- No usar variables derivadas del objetivo como predictoras.
- No mencionar dentro del notebook que se esta siguiendo un archivo externo o una pauta auxiliar; debe verse como un trabajo propio y natural.

## Datos y variables clave

Notebook principal: `EV2_BIY7121_005D.ipynb`.

Variables objetivo:

- Regresion: `agua_caida_tratada`.
- Clasificacion principal: `lluvia_intensa`.
- Variable auxiliar: `llueve`.
- Cluster: sin `Y`.

Matrices preparadas en Fase 3:

- `X_train`, `X_test`
- `X_train_escalado`, `X_test_escalado`
- `y_train_regresion`, `y_test_regresion`
- `y_train_clasificacion`, `y_test_clasificacion`
- `cluster_estaciones`, `X_cluster_escalado`

Variables excluidas de `X`:

- `agua_caida`
- `codigo_estacion`
- `fecha`
- `dia`
- `nombre_estacion`
- objetivos supervisados cuando actuen como `Y`

## Modelos actuales

Regresion:

- `LinearRegression()`
- `DecisionTreeRegressor(max_depth=6, min_samples_leaf=80, random_state=42)`
- `LinearSVR(C=0.5, epsilon=0.1, max_iter=10000, random_state=42, dual='auto')`

Clasificacion:

- `DecisionTreeClassifier(max_depth=6, min_samples_leaf=80, class_weight='balanced', random_state=42)`
- `GaussianNB()`
- `LinearSVC(C=0.5, class_weight='balanced', max_iter=10000, random_state=42, dual='auto')`

Cluster:

- `KMeans(n_clusters=4, n_init=10, random_state=42)`
- `AgglomerativeClustering(n_clusters=4, linkage='ward')`
- `DBSCAN(eps=1.25, min_samples=4)`

## Estado de resultados

Fase 4 ya esta completa y ejecutada.

- Mejor regresion: Arbol regresor, `RMSE=3.026`, `R2=0.181`.
- Mejor clasificacion: Arbol clasificador, `F1=0.254`, `recall=0.804`, `precision=0.151`, `FN=423`.
- Mejor cluster: K-Means, `silhouette=0.522`, `inercia=187.470`.
- Comparacion cluster: Agglomerative obtuvo `silhouette=0.516`; DBSCAN obtuvo `silhouette=0.470` y marco 11 estaciones como ruido.

Interpretacion: el clasificador detecta una buena proporcion de eventos intensos, pero genera muchos falsos positivos. En Fase 5 hay que evaluar el trade-off entre `recall` y `precision`.

## Alineacion Con Rubrica

La rubrica revisada en `material de apoyo` exige especialmente:

- Deteccion y tratamiento de outliers.
- Busqueda de valores inexistentes/nulos.
- Transformacion de variables para modelos.
- Interpretacion de resultados conectada al negocio.
- Analisis de caracteristicas de datos y negocio con insights de alto impacto.
- Desarrollo de 6 hipotesis bien sustentadas.
- Implementacion de 9 modelos.

Estado actual frente a la rubrica:

- Nulos: cubierto.
- Outliers: cubierto, pero puede profundizarse con una tabla final de decision por variable.
- Transformaciones: cubierto con imputacion, encoding, variables derivadas y escalamiento.
- Interpretacion: cubierta en cada fase, pero debe reforzarse en Fase 5.
- Insights: cubiertos, pero conviene cerrar con una sintesis final de alto impacto.
- Hipotesis: existen 6, pero falta una tabla final de evidencia por hipotesis.
- Modelos: cubierto con 9 modelos reales.

Punto delicado:

- K-Means esta directamente respaldado por el material del curso.
- Agglomerative y DBSCAN no aparecen tan claramente en el material. Se mantienen porque el usuario necesita 3 modelos distintos de clustering, pero deben justificarse como modelos complementarios no supervisados: jerarquico y por densidad.

## Pendientes Para Nota Alta

Antes de cerrar el proyecto, completar:

- Fase 5 Evaluacion formal.
- Fase 6 Deploy conceptual.
- Tabla de sustento de hipotesis: `Hipotesis`, `evidencia`, `grafico/modelo`, `decision`.
- Tabla final de outliers: `variable`, `metodo`, `tratamiento`, `justificacion`.
- Grafico de comparacion de clasificacion: `precision`, `recall`, `F1`.
- Matriz de confusion visual del mejor clasificador.
- Justificacion clara del trade-off entre falsos positivos y falsos negativos.

## Proximos pasos recomendados

Fase 5:

- Evaluar formalmente los modelos destacados.
- Priorizar clasificacion de `lluvia_intensa`.
- Analizar matriz de confusion y falsos negativos.
- Justificar si conviene privilegiar `recall` sobre `precision`.
- Proponer mejoras sin rehacer toda la preparacion.

Fase 6:

- Plantear despliegue conceptual, no necesariamente una app.
- Explicar como se usaria el modelo para apoyar decision climatica.
- Incluir limitaciones: disponibilidad de variables termicas, desbalance y necesidad de validacion futura.

## Validacion

Comando usado para ejecutar el notebook:

```powershell
py -3 -m jupyter nbconvert --to notebook --execute EV2_BIY7121_005D.ipynb --inplace --ExecutePreprocessor.timeout=1800
```

Ultima validacion conocida:

- Errores en outputs: `0`.
- Warnings guardados: `0`.
