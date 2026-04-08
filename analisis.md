# Informe Técnico: Fases 1 a 3 de CRISP-DM

## Introducción
El presente informe desarrolla las tres primeras fases de la metodología CRISP-DM sobre un conjunto de datos de precipitaciones diarias registradas en estaciones meteorológicas de Chile. El propósito principal no consiste todavía en construir un modelo final, sino en comprender el problema, analizar la calidad y estructura de los datos, y preparar una base analítica consistente para etapas posteriores de modelamiento.

---

## 1. Entendimiento del Negocio

### 1.1 Contexto
Chile presenta una alta diversidad climática y geográfica. En su territorio conviven zonas desérticas, cordilleranas, costeras, valles interiores y áreas australes con regímenes de precipitación muy distintos. Esta heterogeneidad convierte el análisis de lluvias en un problema relevante para la gestión hídrica, la planificación territorial, la actividad agrícola y la prevención de eventos extremos.

En este contexto, estudiar la precipitación diaria tiene valor porque permite observar no solo la magnitud de la lluvia, sino también su frecuencia, estacionalidad y variación entre estaciones. A diferencia de una base mensual agregada, la granularidad diaria permite distinguir días secos, eventos intensos y patrones temporales más finos.

### 1.2 Objetivo del negocio
El objetivo del negocio es comprender el comportamiento de la precipitación diaria y dejar preparada una base analítica que permita, en una fase posterior, desarrollar modelos capaces de estimar o explicar la variable `agua_caida`.

Desde la lógica de CRISP-DM, esta fase busca responder tres preguntas:
- qué problema se quiere resolver,
- qué valor tendría resolverlo,
- y si los datos disponibles permiten avanzar con suficiente respaldo analítico.

### 1.3 Datos relevantes para el problema
El análisis se sustenta en el archivo `dataset_precipitaciones_diarias_chile_COMPLETO.csv`, complementado con metadata de estaciones contenida en `getEstacionesRedEma.json`. Esta combinación permite enriquecer los códigos técnicos con nombres de estación, región y zona geográfica, mejorando la interpretación territorial del estudio.

El conjunto analizado contiene 187.876 registros, cubre el período entre 2021 y 2025, representa 109 estaciones meteorológicas y 16 regiones del país. La variable central del problema es `agua_caida`, medida en milímetros.

### 1.4 KPI relevantes
En estas primeras fases, los KPI más importantes no son métricas predictivas, sino indicadores de calidad y pertinencia del dataset. Entre los más relevantes destacan la cobertura temporal, la cobertura territorial, la completitud de la variable objetivo y la ausencia de duplicados por estación y fecha.

El análisis mostró que:
- el 90,92% de los registros posee valor disponible en `agua_caida`,
- el 67,68% corresponde a días sin precipitación,
- el 23,24% registra precipitación positiva,
- y el 9,08% presenta valores nulos en la variable objetivo.

Estos indicadores permiten concluir que la base es suficientemente amplia y diversa para análisis descriptivo y preparación de modelamiento, aunque exige tratamiento de faltantes y una interpretación cuidadosa de la fuerte concentración en cero.

### 1.5 Hipótesis de trabajo
A partir del contexto y de la estructura del dataset, se plantean las siguientes hipótesis:
- la precipitación diaria presenta estacionalidad,
- la ubicación geográfica influye en su comportamiento,
- existen diferencias estructurales entre estaciones y regiones,
- la variable objetivo tendrá una distribución asimétrica,
- y los valores faltantes deben analizarse antes de cualquier fase predictiva.

### 1.6 Problema de negocio
El problema de negocio se define como la necesidad de comprender y preparar una base analítica que permita explicar la variación de la precipitación diaria en Chile. El valor del proyecto está en transformar registros meteorológicos en información útil para la toma de decisiones y para una eventual etapa de predicción posterior.

---

## 2. Entendimiento de los Datos

### 2.1 Estructura y naturaleza del dataset
El conjunto contiene variables de ubicación, tiempo y precipitación. Desde el punto de vista analítico, incluye atributos numéricos continuos como `latitud`, `longitud` y `agua_caida`, variables discretas como `anio`, `mes`, `dia` y `altura`, y variables categóricas nominales como `nombre_region`, `zona_geografica` y `nombre_estacion`.

Esta combinación hace que el problema no sea solo temporal, sino también territorial, ya que la lluvia cambia según la estación, la región y la macrozona.

### 2.2 Identificación de nulos
Los valores faltantes se concentran prácticamente por completo en `agua_caida`. Esto es importante porque no se trata de un problema distribuido en muchas variables, sino de una ausencia focalizada en la variable objetivo.

Además, los nulos no deben tratarse de forma mecánica, ya que eliminarlos implicaría perder observaciones valiosas de tiempo y territorio. Por ello se optó por un tratamiento de imputación en lugar de una eliminación masiva de filas.

### 2.3 Tratamiento de nulos
El tratamiento de nulos se realizó mediante `KNNImputer`. La lógica de esta decisión es que un valor faltante de precipitación puede estimarse a partir de registros similares en términos espaciales, temporales y territoriales.

Para ello se utilizaron como apoyo:
- variables numéricas: `altura`, `latitud`, `longitud`, `anio`, `mes` y `dia`,
- y variables categóricas codificadas de `nombre_region` y `zona_geografica`.

Antes de imputar, las variables numéricas se estandarizaron para evitar que atributos con mayor escala, como `altura`, dominaran artificialmente el cálculo de distancias. Luego, el imputador reemplazó los faltantes usando 5 vecinos más cercanos con ponderación por distancia.

Desde un punto de vista analítico, esta estrategia es superior a reemplazar por media global, porque conserva la lógica espacial y temporal del fenómeno. Además, se creó una marca de imputación para dejar trazabilidad sobre qué registros fueron completados.

### 2.4 Encoder aplicado
Las variables categóricas `nombre_region` y `zona_geografica` fueron transformadas mediante `one-hot encoding`. Esto significa que cada categoría se convirtió en una columna binaria de valores 0 y 1.

Esta decisión es adecuada porque se trata de categorías nominales sin orden natural. Aplicar una codificación numérica simple habría introducido relaciones artificiales entre categorías. En cambio, la representación one-hot conserva la diferencia entre territorios sin imponer jerarquías inexistentes.

No se codificó exhaustivamente `nombre_estacion` en esta etapa, ya que su alta cardinalidad habría incrementado demasiado el número de columnas y habría dificultado la interpretación inicial.

### 2.5 Identificación y tratamiento de outliers
La variable `agua_caida` mostró una distribución fuertemente sesgada hacia cero, con una gran cantidad de días secos y una cola larga de eventos intensos. Bajo este escenario, métodos tradicionales como IQR tienden a marcar demasiados registros como outliers.

Por esa razón no se eliminaron automáticamente los valores extremos. En un problema meteorológico, un valor muy alto puede representar un evento real y relevante, no necesariamente un error.

La estrategia adoptada fue conservadora:
- mantener los registros,
- aplicar la transformación `log1p` sobre `agua_caida`,
- crear una marca para eventos extremos sobre el percentil 99,
- y dejar una marca adicional para registros mayores a 100 mm.

Analíticamente, este enfoque permite reducir asimetría y facilitar comparaciones sin perder información valiosa del fenómeno.

### 2.6 Estadística descriptiva y patrones detectados
El análisis descriptivo confirmó que `agua_caida` posee una distribución muy asimétrica: la media supera ampliamente a la mediana y los máximos se ubican muy por encima del comportamiento habitual.

A nivel territorial, las regiones del sur y la zona austral concentran mayores promedios y mayor frecuencia de días con lluvia, mientras que el norte presenta valores bajos y una ocurrencia mucho más escasa. A nivel temporal, los meses de invierno concentran los mayores niveles de precipitación, lo que confirma una estacionalidad clara.

### 2.7 Correlación
La matriz de correlación inicial mostró relaciones lineales débiles entre `agua_caida` y las variables numéricas originales. Esto indica que la precipitación diaria no queda bien representada por relaciones lineales simples usando solo atributos crudos.

Este resultado justificó la construcción de nuevas variables más expresivas, capaces de capturar mejor la naturaleza territorial y cíclica del fenómeno.

### 2.8 Nuevas variables derivadas
A partir del análisis exploratorio se construyeron nuevas variables:
- `estacion_anual`,
- `dia_del_anio`,
- componentes cíclicas `mes_sin`, `mes_cos`, `dia_sin` y `dia_cos`,
- `macrozona`,
- y `lluvia_evento` como indicador de ocurrencia.

Estas variables permiten representar mejor la estructura del tiempo y del territorio. En particular, las componentes seno y coseno evitan tratar el calendario como una escala lineal simple, mientras que la macrozona resume diferencias espaciales de mayor nivel.

### 2.9 Síntesis analítica de la fase 2
La fase de entendimiento de los datos mostró que el dataset es útil y rico en información, pero también confirmó tres características clave del problema:
- la precipitación diaria está altamente concentrada en cero,
- existe una heterogeneidad territorial muy marcada,
- y la variable objetivo no se explica bien con relaciones lineales básicas.

Por ello, las decisiones de imputación, transformación logarítmica, codificación categórica y creación de nuevas variables no fueron solo técnicas, sino metodológicamente necesarias para representar mejor el fenómeno.

---

## 3. Transformación

### 3.1 Justificación del escalamiento
Luego del análisis exploratorio, se observó que las variables continuas operan en escalas muy distintas. Por ejemplo, `altura` tiene una dispersión mucho mayor que las variables cíclicas, mientras que `latitud`, `longitud` y `anio` se ubican en rangos intermedios.

Si estas diferencias se mantuvieran intactas, los algoritmos sensibles a distancia o magnitud podrían privilegiar unas variables por sobre otras sin que ello refleje realmente su aporte analítico.

### 3.2 Aplicación del escalamiento
Por esta razón se aplicó `StandardScaler` sobre las variables continuas:
`altura`, `latitud`, `longitud`, `anio`, `mes_sin`, `mes_cos`, `dia_sin` y `dia_cos`.

La estandarización centró las variables en torno a cero y las dejó con desviación estándar cercana a uno. Esto no modifica el significado del fenómeno, pero sí mejora la comparabilidad entre atributos.

La variable objetivo `agua_caida` se mantuvo en su escala original para conservar interpretabilidad. Asimismo, se evitó incluir como predictores variables derivadas directamente de la propia respuesta, con el fin de no introducir fuga de información en etapas posteriores.

### 3.3 Dataset preparado para modelamiento
Como resultado de esta fase se construyó una matriz analítica `X_preparado`, compuesta por variables continuas escaladas y codificación territorial de macrozona, junto con `y_objetivo` como variable respuesta.

Esta salida deja el conjunto listo para procesos posteriores como partición entrenamiento-prueba, selección de variables, comparación de modelos y evaluación.

### 3.4 Síntesis analítica de la fase 3
La fase de transformación no tuvo como objetivo mejorar aún el rendimiento de un modelo, sino dejar los datos en una forma adecuada para que futuros algoritmos trabajen sobre una base coherente, comparable y metodológicamente justificada.

En conjunto, el escalamiento completa el trabajo iniciado en la fase 2: primero se comprendió el fenómeno, luego se limpiaron y enriquecieron los datos, y finalmente se estandarizaron los atributos continuos para preparar el terreno del modelamiento.

---

## Conclusión General
Las fases 1 a 3 permitieron avanzar desde la comprensión del problema de negocio hasta la preparación de un set de datos listo para etapas predictivas. El caso analizado mostró que la precipitación diaria en Chile está determinada por una combinación de estacionalidad, localización geográfica y alta heterogeneidad territorial.

El análisis confirmó que no bastaba con revisar estadísticas descriptivas simples. Fue necesario tratar nulos con un enfoque contextual, conservar y caracterizar eventos extremos en lugar de eliminarlos, transformar variables categóricas en formato analítico y escalar los predictores continuos para evitar sesgos por magnitud.

En consecuencia, el proyecto queda metodológicamente preparado para abordar la fase de modelamiento con una base más sólida, interpretable y consistente con la naturaleza real del fenómeno estudiado.