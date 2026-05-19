# Plan maestro de trabajo - Proyecto Data Mining

Este documento contiene las instrucciones completas para continuar el proyecto `EV2_BIY7121_005D.ipynb` en este computador o en otro PC. Debe usarse como guia operativa para que el trabajo mantenga el mismo criterio, cumpla la rubrica y use solamente contenidos vistos en el material de apoyo.

El objetivo del proyecto es desarrollar un analisis de Data Mining sobre datos climaticos de Chile, trabajando con precipitaciones, temperaturas y estaciones meteorologicas.

## 1. Instruccion principal para quien continue el proyecto

Quien continue este proyecto debe actuar como cientista de datos senior, pero usando tecnicas del curso. La prioridad no es hacer codigo complejo, sino construir un notebook formal, trazable, bien explicado y defendible frente a la rubrica.

Reglas principales:

- Seguir la estructura oficial del archivo `Material de apoyo/rubrik/estructura para datamining.txt`.
- Usar como base metodologica CRISP-DM: negocio, datos, preparacion/transformacion, modelamiento, evaluacion y deploy.
- Usar solo tecnicas vistas o compatibles con el material de apoyo: Python, Numpy, Pandas, Matplotlib, Seaborn si esta disponible, estadistica descriptiva, nulos, outliers, encoding, escalamiento, correlacion, regresion, clasificacion, arboles, Naive Bayes, SVM y K-Means.
- No usar modelos externos avanzados no vistos en el material, como Random Forest, XGBoost, redes neuronales, Prophet, AutoML o tecnicas no justificadas por el curso.
- Cada decision debe estar justificada en markdown con evidencia del dataset.
- Cada bloque de codigo debe tener un markdown breve antes del codigo.
- Cada tabla o grafico importante debe tener analisis despues del output.
- No duplicar imports, funciones ni procesos.
- Los imports deben quedar solo en la parte superior del notebook.
- Mantener el notebook limpio: no incluir conversaciones, reglas internas ni notas que no sean evaluables.
- Ejecutar el notebook completo al final y revisar que no tenga errores.

## 2. Archivos que deben existir

La estructura de carpetas debe conservarse igual para que el notebook funcione sin cambiar rutas:

- Notebook principal: `EV2_BIY7121_005D.ipynb`
- Plan de trabajo: `PLAN_DE_TRABAJO_DATAMINING.md`
- Estructura oficial: `Material de apoyo/rubrik/estructura para datamining.txt`
- Rubrica 1: `Material de apoyo/rubrik/rubrica 1/`
- Rubrica 2: `Material de apoyo/rubrik/rubrica 2/`
- Dataset principal: `.data/DataMining_Datos/dataset_precipitaciones_temperaturas_chile.csv`
- Dataset complementario: `.data/DataMining_Datos/dataset_precipitaciones_diarias_chile_COMPLETO.csv`
- Estaciones meteorologicas: `.data/DataMining_Datos/getEstacionesRedEma.json`
- GeoJSON de Chile: `.data/chile_country.geojson`

Si se trabaja en otro PC, primero verificar que esas rutas existan.

## 3. Material de apoyo que se debe considerar

El proyecto debe apoyarse en el material de las carpetas `1` y `2`.

Material 1:

- `1.1 Introduccion a la Mineria de Datos`: usar para explicar que el proyecto busca descubrir patrones utiles para apoyar decisiones.
- `1.2 Metodologia Mineria de Datos / CRISP-DM`: usar como base para ordenar las fases del notebook.
- `1.3 Tecnicas en la Mineria de Datos`: usar para justificar tareas descriptivas y predictivas.
- `1.4 Manejo de Python, Numpy y Pandas`: usar para carga, limpieza, transformacion y analisis tabular.
- `1.5 Entendiendo los Datos`: usar para tipos de variables, estadisticos, distribuciones, nulos y primeras relaciones.
- `1.6 Limpieza y Transformacion`: usar para tratamiento de nulos, outliers y transformaciones.
- `1.7 Analisis y Preprocesamiento`: usar para encoding, escalamiento y preparacion de datos para modelos.

Material 2:

- `2.1 Extraccion de Patrones`: usar solo si se justifica algun patron descriptivo; Market Basket no es prioridad porque el dataset climatico no es transaccional.
- `2.2 Metodos Predictivos y Descriptivos`: usar para distinguir regresion, clasificacion y cluster.
- `2.3 Modelos estadisticos parametricos / regresion`: usar para modelos de regresion.
- `2.4 Modelos Bayesianos`: usar para Naive Bayes en clasificacion si corresponde.
- `2.5 Arboles de decision y sistemas de reglas`: usar para modelos de arbol en regresion o clasificacion.
- `2.6 Maquinas de Vectores de Soporte`: usar para SVM en clasificacion o regresion si se implementa con criterios simples.
- `2.7 Metodos basados en casos y vecindad / K-Means`: usar para clusterizacion de estaciones, regiones o macrozonas climaticas.

## 4. Rubrica: criterios que deben cumplirse

La rubrica exige que el notebook no solo tenga codigo, sino tambien interpretacion, conexion con negocio e hipotesis sustentadas.

Rubrica 1:

- Identificar fases de CRISP-DM y explicar su valor para apoyar la toma de decisiones.
- Identificar objetivos claves y KPIs coherentes con Business Understanding.
- Calcular estadisticos e identificar insights relevantes.
- Reconocer tipos de datos y su naturaleza.
- Identificar outliers y missing values, proponiendo rutinas de limpieza.
- Aplicar matriz de correlacion para analizar relaciones entre variables.
- Proponer pasos posteriores conectados con los resultados obtenidos.

Rubrica 2:

- Aplicar estrategias de deteccion de valores atipicos con buenas practicas.
- Buscar valores inexistentes en las caracteristicas del dataset.
- Transformar variables de acuerdo con los modelos matematicos.
- Interpretar resultados segun necesidades y caracteristicas del negocio.
- Analizar caracteristicas de datos y negocio, identificando insights de alto impacto.
- Desarrollar hipotesis bien sustentadas; para nivel excelente se deben incluir 6 hipotesis.
- Implementar modelos de acuerdo con las hipotesis; para nivel excelente la rubrica menciona 9 modelos o implementaciones.

Por lo tanto, el proyecto debe apuntar a:

- 6 hipotesis bien justificadas.
- Graficos suficientes para respaldar cada hipotesis.
- Transformaciones completas y explicadas.
- Modelos de regresion, clasificacion y cluster.
- Metricas y evaluacion conectadas con el problema de negocio.

## 5. Estructura oficial del notebook

El notebook debe respetar esta estructura, con titulos y numeracion clara:

1. Entendimiento del Negocio
   - 1.1 Contexto
   - 1.2 Datos relevantes
   - 1.3 Hipotesis y tesis consideradas
   - 1.4 KPI's relevantes
   - 1.5 Problema de negocio: Prediccion

2. Entendimiento Datos
   - 2.1 Identificar tipos de variables/atributos
   - 2.2 Identificar nulos
   - 2.3 Identificar outliers
   - 2.4 Transformaciones
   - 2.4.1 A numericos: Encoder
   - 2.4.2 Tratamiento de nulos
   - 2.4.3 Tratamiento de outliers
   - 2.5 Analisis estadisticos basicos
   - 2.6 Matriz de correlacion
   - 2.7 Patrones/comportamientos detectados
   - 2.8 Nuevas variables a partir de analisis
   - 2.9 Matriz de correlacion
   - 2.10 Patrones/comportamientos detectados con nuevas variables

3. Transformacion
   - 3.1 Escalamiento

4. Modelamiento
   - 4.1 Modelos de regresion
   - 4.1.1 Seleccion de variables X/Y
   - 4.1.2 Seleccion de parametros
   - 4.1.3 Aplicacion de modelos
   - 4.1.4 Obtencion de metricas
   - 4.2 Modelos de clasificacion
   - 4.2.1 Seleccion de variables X/Y
   - 4.2.2 Seleccion de parametros
   - 4.2.3 Aplicacion de modelos
   - 4.2.4 Obtencion de metricas
   - 4.3 Modelos de Cluster
   - 4.3.1 Seleccion de variables X
   - 4.3.2 Seleccion de parametros
   - 4.3.3 Aplicacion de modelos
   - 4.3.4 Obtencion de metricas

5. Evaluacion
   - 5.1 Metricas: matriz de confusion, R/R2, RMSE
   - 5.2 Mejoras del proceso o modelamiento

6. Deploy

## 6. Estado actual del notebook

Ya se desarrollaron la Fase 1 y la Fase 2 en `EV2_BIY7121_005D.ipynb`.

Verificaciones actuales:

- El dataset principal contiene precipitaciones, temperaturas y estaciones.
- El dataset principal tiene rango temporal 2021-01-01 a 2025-12-31.
- Se trabaja con 109 estaciones y 16 regiones.
- Se incorporaron macrozonas para mejorar el analisis territorial.
- Se agregaron graficos de nulos, outliers, correlaciones, distribuciones, series temporales y comparaciones territoriales.
- Se corrigio el grafico de nulos para que sea legible mediante heatmap por ano.
- Las matrices de correlacion tienen etiquetas numericas.
- La lluvia extrema se trata en una variable separada llamada `agua_caida_tratada`.
- La variable original `agua_caida` se conserva para auditoria.
- El notebook fue ejecutado sin errores.

## 7. Criterio de datos y variables principales

Variables climaticas principales:

- `agua_caida`: precipitacion original.
- `agua_caida_tratada`: precipitacion tratada para analisis/modelamiento.
- `temp_media`: temperatura media.
- `temp_min`: temperatura minima.
- `temp_max`: temperatura maxima.
- `altura`: altura de la estacion.
- `latitud` y `longitud`: ubicacion geografica.
- `region`, `nombre_region`, `zona`, `macrozona`: variables territoriales.
- `fecha`, `anio`, `mes`: variables temporales.
- `llueve`: variable binaria para clasificacion de presencia de lluvia.
- `lluvia_intensa`: variable binaria para clasificacion de lluvia intensa.

Regla importante:

- No eliminar `agua_caida` original.
- Usar `agua_caida_tratada` cuando los outliers de lluvia distorsionen el analisis.
- Usar variables binarias (`llueve`, `lluvia_intensa`) para clasificacion.
- Usar `agua_caida_tratada` para regresion.

## 8. Tratamiento de nulos

El tratamiento de nulos debe estar justificado segun la naturaleza de cada variable.

Instruccion:

- Primero medir cantidad y porcentaje de nulos.
- Mostrar nulos por variable.
- Mostrar nulos por periodo temporal si aporta interpretacion.
- No imputar automaticamente sin explicar.
- Para variables climaticas numericas, usar mediana por estacion, region o macrozona cuando sea coherente.
- Si no hay suficiente informacion por estacion, usar macrozona o mediana general como respaldo.
- Conservar registro del criterio usado.

Graficos requeridos:

- Barras de porcentaje de nulos por variable.
- Heatmap de nulos climaticos por ano o periodo.
- Tabla resumen de nulos antes y despues del tratamiento.

## 9. Tratamiento de outliers

Los outliers deben analizarse por variable, no tratarse todos igual.

Temperaturas:

- Detectar outliers con metodo IQR.
- Revisar valores extremos con boxplots.
- Tratar solo valores evidentemente anomalos.
- Preferir imputacion con mediana por estacion o macrozona si se decide corregir.

Precipitacion:

- La lluvia tiene distribucion asimetrica, muchos ceros y pocos eventos extremos.
- No tratar lluvia con el mismo criterio que temperatura.
- Conservar `agua_caida` como variable original.
- Crear o mantener `agua_caida_tratada`.
- Detectar extremos de lluvia con percentil 99.
- Reemplazar valores superiores al percentil 99 por la mediana de dias con lluvia no extrema.
- No usar mediana general de lluvia, porque normalmente es 0 y puede convertir eventos extremos en dias sin lluvia.

Graficos requeridos:

- Grafico general con cantidad y porcentaje de outliers por variable.
- Boxplot individual para cada variable con outliers.
- Tabla de outliers por variable.
- Top de precipitaciones originales mas extremas.
- Comparacion de distribucion original vs tratada.

## 10. Macrozona geografica

La variable `macrozona` debe mantenerse porque permite interpretar patrones climaticos de forma clara.

Definicion:

- Norte Grande: regiones 15, 1 y 2.
- Norte Chico: regiones 3 y 4.
- Zona Central: regiones 5, 13, 6, 7, 16 y 8.
- Zona Sur: regiones 9, 14 y 10.
- Zona Austral: regiones 11 y 12.

Usos obligatorios:

- Analisis descriptivo por macrozona.
- Comparacion de precipitacion y temperatura por macrozona.
- Analisis de eventos de lluvia intensa por macrozona.
- Uso como variable categorica codificada en modelamiento si aporta valor.

## 11. Graficos esperados

El notebook debe tener muchos graficos, pero cada grafico debe responder una pregunta analitica.

Graficos minimos:

- Distribucion de `agua_caida`, `agua_caida_tratada`, `temp_media`, `temp_min`, `temp_max` y `altura`.
- Porcentaje de nulos por variable.
- Heatmap de nulos climaticos por ano.
- Grafico general de outliers.
- Boxplot individual por variable.
- Comparacion de lluvia original versus lluvia tratada.
- Estadisticos por macrozona.
- Precipitacion promedio por macrozona.
- Temperatura media por macrozona.
- Eventos de lluvia intensa por region, zona y macrozona.
- Serie mensual de precipitacion.
- Serie mensual de temperatura.
- Matriz de correlacion inicial con etiquetas numericas.
- Pairplot o scatter matrix de variables principales.
- Mapa/scatter geografico de estaciones.
- Segunda matriz de correlacion con nuevas variables.
- Graficos de resultados de modelos.
- Matriz de confusion para clasificacion.
- Grafico de reales vs predichos para regresion.
- Grafico de clusters para K-Means.

Regla:

- Todo grafico debe tener titulo, nombres de ejes y analisis posterior.

## 12. Fase 1: Entendimiento del negocio

Debe estar completa y formal.

Debe incluir:

- Contexto climatico y territorial de Chile.
- Relevancia de precipitacion y temperatura para toma de decisiones.
- Datos relevantes disponibles.
- 6 hipotesis o tesis consideradas, alineadas con la rubrica.
- KPIs climaticos y de negocio.
- Problema predictivo claro.

Hipotesis sugeridas:

- H1: La precipitacion varia significativamente entre macrozonas.
- H2: La temperatura media disminuye en zonas de mayor latitud sur y/o mayor altura.
- H3: Los eventos de lluvia intensa se concentran en zonas especificas.
- H4: La estacionalidad mensual afecta la precipitacion y temperatura.
- H5: Las variables geograficas y temporales aportan capacidad predictiva.
- H6: El tratamiento de outliers mejora la estabilidad del analisis y de los modelos.

Estas hipotesis pueden ajustarse, pero no deben quedar menos de 6 si se busca nivel excelente.

## 13. Fase 2: Entendimiento de los datos

Debe demostrar dominio exploratorio.

Debe incluir:

- Carga y verificacion del dataset.
- Identificacion de tipos de variables.
- Revision de dimensiones, rango temporal, estaciones y regiones.
- Analisis de nulos.
- Analisis de outliers.
- Transformaciones iniciales.
- Estadisticos basicos.
- Matriz de correlacion.
- Patrones detectados.
- Nuevas variables.
- Segunda matriz de correlacion.
- Analisis con nuevas variables.

Las nuevas variables deben tener proposito:

- `anio`, `mes`, `mes_nombre` para estacionalidad.
- `macrozona` para analisis territorial.
- `llueve` para clasificacion.
- `lluvia_intensa` para clasificacion.
- `agua_caida_tratada` para reducir distorsion de extremos.
- Variables codificadas solo cuando sean necesarias para correlacion o modelos.

## 14. Fase 3: Transformacion

Esta fase debe preparar un dataset final para modelamiento.

Pasos:

- Definir variable objetivo de regresion: `agua_caida_tratada`.
- Definir variables objetivo de clasificacion: `llueve` y/o `lluvia_intensa`.
- Definir variables para cluster: variables climaticas, geograficas y territoriales agregadas.
- Separar variables predictoras y objetivo.
- Tratar nulos antes de entrenar modelos.
- Aplicar encoder a variables categoricas.
- Aplicar escalamiento a variables numericas cuando el modelo lo requiera.
- Separar train/test para modelos supervisados.
- Guardar y mostrar la forma final de los datasets.

Tecnicas permitidas:

- `LabelEncoder`, `OneHotEncoder` o `get_dummies` para categoricas.
- `StandardScaler` o `MinMaxScaler` para escalamiento.
- `train_test_split` para separacion de datos.
- Imputacion con mediana/moda usando Pandas o herramientas simples de Scikit-learn si ya estan en uso.

Evitar:

- Transformaciones sin explicacion.
- Escalar variables categoricas ya codificadas sin justificar.
- Usar columnas que filtren directamente la respuesta.

## 15. Fase 4: Modelamiento

La rubrica 2 menciona 9 modelos o implementaciones para nivel excelente. Se recomienda cumplirlo mediante modelos vistos en el material, separando regresion, clasificacion y cluster.

Propuesta de implementaciones:

Regresion:

- Modelo 1: Regresion lineal para predecir `agua_caida_tratada`.
- Modelo 2: Arbol de decision regresor con profundidad controlada.
- Modelo 3: SVM para regresion o una segunda configuracion justificada si el material y entorno lo permiten.

Clasificacion:

- Modelo 4: Arbol de decision para predecir `llueve`.
- Modelo 5: Arbol de decision para predecir `lluvia_intensa`.
- Modelo 6: Naive Bayes para predecir `llueve` o `lluvia_intensa`.
- Modelo 7: SVM lineal para clasificacion.

Cluster:

- Modelo 8: K-Means con un valor de K justificado por metodo del codo.
- Modelo 9: K-Means con otro K comparativo o segmentacion alternativa por variables agregadas.

Cada modelo debe incluir:

- Hipotesis o razon que lo conecta con el problema.
- Variables X/Y utilizadas.
- Parametros principales.
- Entrenamiento.
- Prediccion o asignacion de cluster.
- Metrica.
- Analisis del resultado.

Si algun modelo no ejecuta bien por rendimiento, se debe usar una muestra justificada y dejarlo explicado.

## 16. Fase 5: Evaluacion

La evaluacion debe comparar resultados y conectar con negocio.

Regresion:

- Usar MAE.
- Usar RMSE.
- Usar R2.
- Graficar reales vs predichos.
- Analizar errores.

Clasificacion:

- Usar accuracy.
- Usar matriz de confusion.
- Usar precision.
- Usar recall.
- Usar F1-score.
- Analizar si el modelo falla mas en lluvia/no lluvia o lluvia intensa/no intensa.

Cluster:

- Usar inercia.
- Usar metodo del codo.
- Interpretar perfiles de cluster.
- Relacionar clusters con macrozonas, regiones o estaciones.

Debe existir una conclusion comparativa:

- Mejor modelo de regresion.
- Mejor modelo de clasificacion.
- Utilidad del cluster.
- Limitaciones del dataset.
- Mejoras futuras.

## 17. Fase 6: Deploy

Si no se implementa una aplicacion real, el deploy debe ser conceptual pero concreto.

Debe indicar:

- Entrada esperada: fecha, estacion, region, macrozona, temperatura, altura, latitud, longitud u otras variables disponibles.
- Salida esperada: prediccion de lluvia, lluvia intensa o precipitacion estimada.
- Usuario objetivo: analista, institucion, area agricola, gestion territorial o planificacion climatica.
- Frecuencia de actualizacion: diaria, mensual o segun disponibilidad de datos.
- Riesgos: datos faltantes, eventos extremos, cambio climatico, estaciones con baja cobertura.
- Mejoras futuras: mas anos, variables atmosfericas adicionales, validacion operacional y monitoreo de drift.

## 18. Reglas de escritura del notebook

Cada seccion debe tener:

- Introduccion breve.
- Codigo corto y claro.
- Output visible.
- Analisis posterior.
- Cierre parcial cuando corresponda.

No escribir:

- "Hice esto porque el usuario lo pidio".
- "Regla interna".
- Conversaciones.
- Codigo largo sin separacion.
- Imports repetidos.
- Graficos sin analisis.
- Conclusiones genericas sin evidencia.

Estilo recomendado:

- Formal, academico y claro.
- Explicar decisiones con datos.
- Usar lenguaje de Data Mining y CRISP-DM.
- Relacionar hallazgos con impacto para la toma de decisiones.

## 19. Checklist por rubrica antes de entregar

Revisar uno por uno:

- El notebook identifica fases CRISP-DM y explica su utilidad.
- La Fase 1 tiene contexto, datos, KPIs, problema de negocio e hipotesis.
- Hay 6 hipotesis bien sustentadas.
- Se identifican tipos de variables.
- Se calculan estadisticos relevantes.
- Se analizan insights y patrones.
- Se detectan nulos con tablas y graficos.
- Se detectan outliers con tablas y graficos.
- Se proponen y aplican rutinas de limpieza.
- Se realiza transformacion de variables.
- Se aplican encoders cuando corresponde.
- Se aplica escalamiento en Fase 3.
- Hay matriz de correlacion inicial y segunda matriz de correlacion.
- Las matrices tienen etiquetas numericas.
- Hay graficos abundantes y bien analizados.
- Hay modelos de regresion.
- Hay modelos de clasificacion.
- Hay modelos de cluster.
- Se intenta llegar a 9 modelos o implementaciones.
- Cada modelo tiene variables, parametros, metricas y analisis.
- Se incluye matriz de confusion.
- Se incluye R2/R y RMSE en regresion.
- Se comparan resultados.
- Se proponen mejoras.
- Hay deploy conceptual.
- El notebook ejecuta completo sin errores.

## 20. Validacion tecnica final

Antes de entregar o mover el proyecto a otro PC:

- Ejecutar todas las celdas desde cero.
- Confirmar que no hay errores.
- Confirmar que no hay outputs desactualizados.
- Revisar que los graficos se vean legibles.
- Revisar que no existan imports duplicados.
- Revisar que cada celda de codigo tenga markdown previo.
- Revisar que cada grafico importante tenga analisis posterior.

Comando sugerido:

```bash
jupyter nbconvert --to notebook --execute EV2_BIY7121_005D.ipynb --inplace
```

Si se usa VS Code o Jupyter Notebook, ejecutar `Run All` y guardar el notebook con outputs actualizados.

## 21. Orden de trabajo recomendado desde ahora

1. Revisar rapidamente Fase 1 y Fase 2 contra este plan.
2. Ajustar cualquier markdown o grafico que no tenga analisis suficiente.
3. Desarrollar Fase 3 completa: transformacion, encoding, nulos finales, escalamiento y datasets de modelamiento.
4. Desarrollar Fase 4 con regresion, clasificacion y cluster.
5. Desarrollar Fase 5 con metricas, comparacion y conclusiones.
6. Desarrollar Fase 6 como propuesta de deploy.
7. Ejecutar notebook completo.
8. Revisar checklist de rubrica.
9. Corregir outputs, textos o graficos debiles.

La regla final es simple: todo lo que se haga debe poder defenderse con la rubrica, con el material de apoyo y con evidencia del dataset.
