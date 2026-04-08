# Guia de Defensa del Notebook

## Proposito de este archivo

Este documento esta pensado para ayudarte a defender el trabajo frente a tu profesor. No solo resume lo que aparece en `DataMining.ipynb`, sino que explica por que se uso cada tecnica importante entre la fase 1 y la fase 3.

La idea es que no memorices codigo suelto, sino la logica detras de cada decision. Si entiendes eso, podras responder mejor preguntas del tipo:

- por que usaste esa variable y no otra;
- por que trataste los nulos de esa forma;
- por que no eliminaste outliers;
- por que escalaste asi los datos;
- y por que la base final quedo lista para modelamiento.

---

## Como usar este documento

Cada seccion esta organizada con tres preguntas:

1. Que hice.
2. Por que lo hice.
3. Como lo defenderia oralmente.

Si te aprendes bien esa estructura, te sera mucho mas facil explicar el notebook aunque no recites cada linea de codigo.

---

# 1. Fase 1: Entendimiento del Negocio

## 1.1 Por que parti con CRISP-DM

### Que hice
Organice el notebook siguiendo la logica de CRISP-DM:

- entendimiento del negocio;
- entendimiento de los datos;
- transformacion.

### Por que lo hice
Porque el trabajo no pedia partir modelando de inmediato. Primero habia que justificar el caso, comprender el dataset y preparar la base.

### Como defenderlo
"Use CRISP-DM porque me permite avanzar de forma ordenada: primero entender el problema, despues entender los datos y finalmente transformar la base antes de modelar. En este trabajo las tres primeras fases eran las mas importantes."

---

## 1.2 Contexto del problema

### Que hice
Explique por que estudiar precipitaciones diarias en Chile tiene sentido.

### Por que lo hice
Porque Chile tiene alta diversidad climatica y geografica. No llueve igual en el norte que en el sur, ni en cordillera que en costa. Eso hace que el problema tenga valor real y no sea un ejercicio artificial.

### Como defenderlo
"El contexto era importante porque la precipitacion en Chile no es homogenea. Justamente por esa diversidad territorial tiene sentido analizar el fenomeno y despues intentar explicarlo o predecirlo."

---

## 1.3 Datos relevantes

### Que hice
Mostre cual era la fuente principal y cual era la metadata complementaria:

- `dataset_precipitaciones_diarias_chile_COMPLETO.csv`
- `Set de datos/getEstacionesRedEma.json`

Tambien resumi el tamano, periodo, estaciones, regiones y variable objetivo.

### Por que lo hice
Porque en fase 1 no basta con decir "tengo un dataset". Hay que explicar que contiene, por que sirve para el problema y que limitaciones tiene.

### Como defenderlo
"No solo use el CSV principal. Tambien use la metadata para enriquecer los codigos de estacion y region. Eso mejora mucho la interpretacion del problema, porque el analisis deja de ser solo tecnico y pasa a ser territorial."

---

## 1.4 Hipotesis de trabajo

### Que hice
Plantee hipotesis relacionadas con:

- estacionalidad;
- ubicacion geografica;
- diferencias entre estaciones;
- distribucion asimetrica de `agua_caida`;
- importancia de los nulos.

### Por que lo hice
Porque en CRISP-DM la fase 1 no es solo descripcion. Tambien hay que dejar explicitadas las ideas que guiaran el analisis posterior.

### Como defenderlo
"Las hipotesis me sirvieron para orientar el trabajo. Por ejemplo, si yo espero estacionalidad, entonces despues tiene sentido revisar patrones por mes o crear variables ciclicas."

---

## 1.5 KPI relevantes

### Que hice
Use KPI de cobertura y calidad del dataset, no metricas predictivas finales.

### Por que lo hice
Porque todavia no estabamos en modelamiento. En esta etapa era mas coherente medir:

- cobertura temporal;
- cobertura territorial;
- completitud de `agua_caida`;
- presencia de dias secos;
- consistencia del dataset.

### Como defenderlo
"No use RMSE o MAE como KPI principal en la fase 1 porque aun no habia modelo. En esta etapa lo importante era evaluar si el conjunto era suficientemente completo, representativo y util para seguir avanzando."

---

## 1.6 Problema de negocio

### Que hice
Defini el problema como la necesidad de comprender el comportamiento de la precipitacion diaria y dejar preparada una base analitica para modelamiento posterior.

### Por que lo hice
Porque todavia no correspondia prometer un modelo final. Primero habia que justificar que el problema esta bien planteado y que los datos permiten abordarlo.

### Como defenderlo
"No presente el caso como si ya estuviera resuelto con un modelo. Lo formule como un problema de analisis y preparacion de datos que mas adelante puede proyectarse a regresion supervisada."

---

# 2. Fase 2: Entendimiento de los Datos

## 2.1 Carga y enriquecimiento del dataset

### Que hice
Primero cargue el CSV principal y la metadata. Despues hice un `merge` entre ambas fuentes.

### Por que lo hice
Porque el CSV por si solo tenia informacion tecnica, pero la metadata agregaba contexto analitico:

- nombre de estacion;
- nombre de region;
- zona geografica.

### Como defenderlo
"Hice el merge porque trabajar solo con codigos es poco interpretable. Al agregar nombres y zonas geograficas pude analizar mejor el comportamiento territorial de la precipitacion."

---

## 2.2 Identificacion de tipos de variables

### Que hice
No me quede solo con el `dtype` de pandas. Tambien hice una clasificacion analitica de las variables.

### Por que lo hice
Porque una variable puede verse numerica tecnicamente, pero funcionar como categoria en el problema. Por ejemplo:

- `codigo_estacion` es numerica, pero analiticamente es un identificador;
- `region` es numerica, pero no representa una magnitud continua.

### Como defenderlo
"Distingui entre tipo tecnico y tipo analitico porque eso afecta directamente las transformaciones. Si tratara `region` como continua, estaria imponiendo relaciones numericas que en realidad no existen."

---

## 2.3 Revision de nulos

### Que hice
Analice los nulos:

- por variable;
- por region;
- por anio.

Ademas, los mostre con tablas y graficos.

### Por que lo hice
Porque no basta con saber si hay faltantes. Tambien importa saber donde se concentran y si siguen algun patron.

### Hallazgo principal
Los nulos estaban concentrados casi completamente en `agua_caida`.

### Como defenderlo
"Revise los nulos por variable, region y anio porque queria saber si la ausencia de datos era aleatoria o si estaba asociada a zonas o periodos concretos. Eso era clave antes de decidir como tratarlos."

---

## 2.4 Por que use KNNImputer

### Que hice
En vez de eliminar todos los registros sin `agua_caida`, use `KNNImputer`.

### Por que lo hice
Porque eliminar esos registros implicaba perder mucha informacion temporal y territorial. Tampoco quise usar una media global, porque la precipitacion cambia mucho segun:

- ubicacion geografica;
- fecha;
- region;
- zona geografica.

`KNNImputer` permite estimar un faltante usando observaciones parecidas en el espacio y en el tiempo.

### Variables usadas para imputar

- `altura`
- `latitud`
- `longitud`
- `anio`
- `mes`
- `dia`
- dummies de `nombre_region`
- dummies de `zona_geografica`

### Como defenderlo
"Use KNNImputer porque necesitaba una imputacion contextual. La precipitacion no se comporta igual en todo Chile, por eso una media global me parecia demasiado pobre. KNN me permite aproximar el valor faltante usando observaciones similares."

---

## 2.5 Por que estandarice antes del KNN

### Que hice
Estandarice las variables numericas antes de aplicar el imputador.

### Por que lo hice
Porque KNN trabaja con distancias. Si una variable tiene escala mucho mayor que otra, domina el calculo de vecinos aunque no necesariamente sea mas importante.

Ejemplo:

- `altura` puede tener miles de metros;
- `mes` solo va de 1 a 12;
- `latitud` y `longitud` tienen otro rango.

Sin estandarizacion, las distancias quedarian sesgadas.

### Como defenderlo
"Estandarice antes del KNN porque si no lo hacia, variables como altura dominaban el calculo de distancia. Queria que el imputador comparara observaciones de forma mas equilibrada."

---

## 2.6 Por que cree `agua_caida_imputada`

### Que hice
Despues de imputar, cree una marca binaria llamada `agua_caida_imputada`.

### Por que lo hice
Porque necesitaba trazabilidad. No es lo mismo un dato medido que un dato estimado.

### Como defenderlo
"Cree esa variable para no perder trazabilidad. Asi pude distinguir entre registros observados y registros completados por imputacion."

---

## 2.7 Por que use one-hot encoding

### Que hice
Transforme `nombre_region` y `zona_geografica` con `pd.get_dummies`.

### Por que lo hice
Porque son variables categoricas nominales, es decir, no tienen orden natural.

Si les hubiera asignado numeros simples, habria introducido una jerarquia artificial.

### Por que no encodee completamente la estacion
Porque `nombre_estacion` tiene alta cardinalidad. Si la codificaba completa en esa fase, el numero de columnas subia mucho y se hacia menos interpretable.

### Como defenderlo
"Use one-hot encoding porque region y zona geografica son categorias sin orden. No encodee exhaustivamente la estacion en esta etapa para no inflar innecesariamente la dimensionalidad."

---

## 2.8 Por que no elimine outliers automaticamente

### Que hice
Analice outliers con:

- IQR;
- percentiles altos;
- histogramas;
- boxplots;
- revision de registros extremos.

Pero no elimine los valores altos automaticamente.

### Por que lo hice
Porque en precipitaciones diarias un valor alto no necesariamente es error. Puede ser un evento extremo real.

Ademas, `agua_caida` tiene una estructura muy cargada en cero. En ese escenario, IQR tiende a marcar demasiados valores como supuestos outliers.

### Como defenderlo
"No elimine outliers automaticamente porque en este problema un extremo puede ser precisamente parte del fenomeno. Preferi identificarlos, caracterizarlos y mantenerlos marcados antes que borrarlos sin criterio."

---

## 2.9 Por que use `log1p(agua_caida)`

### Que hice
Cree `agua_caida_log = np.log1p(agua_caida)`.

### Por que lo hice
Porque la variable objetivo estaba muy sesgada hacia cero y con cola larga.

La transformacion logaritmica:

- reduce asimetria;
- hace mas interpretable la dispersion;
- ayuda en correlacion y comparaciones posteriores.

### Como defenderlo
"Use `log1p` para reducir la asimetria de `agua_caida` sin perder los ceros. Es una forma de trabajar mejor una variable muy concentrada en valores bajos y con pocos eventos intensos."

---

## 2.10 Por que marque eventos extremos

### Que hice
Cree indicadores como:

- `evento_extremo_p99`
- `revision_manual_mayor_100`

### Por que lo hice
Porque queria conservar los extremos, no borrarlos. Al marcarlos, luego se pueden estudiar de forma separada.

### Como defenderlo
"Preferi marcar los eventos extremos en lugar de eliminarlos. Asi no pierdo informacion y dejo abierta la posibilidad de analizarlos o tratarlos de otra manera en modelamiento."

---

## 2.11 Por que calcule correlacion inicial

### Que hice
Calcule una matriz de correlacion inicial con variables numericas crudas.

### Por que lo hice
Porque queria una primera lectura lineal basica del problema.

El resultado mostro que la correlacion simple con `agua_caida` era debil, lo que justifico construir mejores variables.

### Como defenderlo
"La correlacion inicial me sirvio como punto de partida. Como las relaciones lineales eran debiles, eso mismo justifico crear variables mas expresivas en vez de quedarme con las columnas originales."

---

## 2.12 Por que cree nuevas variables

### Que hice
Cree variables como:

- `estacion_anual`
- `dia_del_anio`
- `mes_sin`
- `mes_cos`
- `dia_sin`
- `dia_cos`
- `macrozona`
- `lluvia_evento`

### Por que lo hice
Porque el fenomeno tiene dos rasgos que las variables originales no capturan bien por si solas:

- estructura ciclica del tiempo;
- agrupacion territorial de mayor escala.

### Explicacion variable por variable

#### `estacion_anual`
La use para traducir el mes a una idea climatica mas interpretable.

#### `dia_del_anio`
La use para ubicar cada registro dentro del ciclo anual completo.

#### `mes_sin`, `mes_cos`, `dia_sin`, `dia_cos`
Las use porque el tiempo es ciclico. Diciembre y enero estan cerca climaticamente, pero numericamente 12 y 1 parecen lejanos. Con seno y coseno esa continuidad se representa mejor.

#### `macrozona`
La use para resumir diferencias territoriales de mayor escala sin quedarme solo en la region puntual.

#### `lluvia_evento`
La use para separar ocurrencia de lluvia e intensidad. No es lo mismo que llueva o no llueva, a cuantos milimetros hayan caido.

### Como defenderlo
"Cree nuevas variables porque las columnas crudas no representaban bien ni la ciclicidad del tiempo ni la estructura territorial del problema. Estas variables acercan la base a la logica real del fenomeno."

---

## 2.13 Por que use tantos graficos

### Que hice
Use histogramas, boxplots, barras, lineas y heatmaps.

### Por que lo hice
Porque el objetivo no era solo calcular tablas, sino interpretar mejor:

- distribuciones;
- nulos;
- diferencias regionales;
- estacionalidad;
- correlaciones;
- patrones por macrozona.

### Como defenderlo
"Use graficos porque varias caracteristicas del problema se entienden mejor visualmente que con una tabla sola. Por ejemplo, la asimetria de `agua_caida` o la estacionalidad mensual se ven mucho mejor en graficos."

---

# 3. Fase 3: Transformacion

## 3.1 Por que exclui targets imputados al preparar modelamiento

### Que hice
En la fase 3 tome solo el subconjunto con `agua_caida_imputada == 0` para formar la base supervisada.

### Por que lo hice
Porque una cosa es usar imputacion para analisis descriptivo y otra distinta es usar valores imputados como si fueran verdad terreno en modelamiento.

### Como defenderlo
"En fase 2 la imputacion me ayudo a no perder estructura del dataset, pero en fase 3 exclui esos targets imputados para no entrenar con etiquetas que no fueron realmente observadas."

---

## 3.2 Por que use un corte temporal

### Que hice
Separe un bloque de referencia con corte en `2025-01-01`:

- 2021 a 2024 como `train`;
- 2025 como `test`.

### Por que lo hice
Porque el dataset es temporal y no conviene mezclar el futuro con el pasado.

### Como defenderlo
"Use un corte temporal porque la precipitacion es una serie con orden cronologico. Si mezclo anos al azar, corro el riesgo de evaluar con informacion que no respeta la secuencia real del problema."

---

## 3.3 Por que use StandardScaler

### Que hice
Aplique `StandardScaler` a las variables continuas:

- `altura`
- `latitud`
- `longitud`
- `anio`
- `mes_sin`
- `mes_cos`
- `dia_sin`
- `dia_cos`

### Por que lo hice
Porque estas variables tienen escalas distintas y varios algoritmos son sensibles a la magnitud de entrada.

El escalamiento:

- centra cerca de cero;
- deja desviacion estandar cercana a uno;
- vuelve mas comparables los predictores.

### Como defenderlo
"Use StandardScaler porque queria que las variables continuas fueran comparables en escala. Eso es especialmente importante si despues uso metodos sensibles a distancia o a la magnitud."

---

## 3.4 Por que ajuste el escalador solo con train

### Que hice
Hice:

- `fit_transform` en `train`;
- `transform` en `test`.

### Por que lo hice
Porque si ajustaba el escalador con todo el dataset, estaba dejando que el bloque futuro influyera en la transformacion del bloque pasado. Eso es fuga de informacion.

### Como defenderlo
"Ajuste el escalador solo con train para evitar leakage. Quise que la transformacion imitara un escenario real, donde no conozco el comportamiento completo del bloque futuro al momento de entrenar."

---

## 3.5 Por que no escale `agua_caida`

### Que hice
Mantube la variable objetivo en su escala original.

### Por que lo hice
Porque queria conservar interpretabilidad. Si el modelo predice precipitacion, tiene sentido que el resultado siga estando en milimetros.

### Como defenderlo
"No escale la variable objetivo porque queria mantener interpretacion fisica directa. Es mucho mas claro decir que el modelo estima milimetros de lluvia que trabajar con una escala abstracta."

---

## 3.6 Por que use dummies de macrozona con `drop_first=True`

### Que hice
Codifique `macrozona` con dummies, omitiendo una categoria base.

### Por que lo hice
Porque si dejo todas las dummies al mismo tiempo, introduzco redundancia perfecta y eso puede generar problemas de multicolinealidad, sobre todo en modelos lineales.

### Como defenderlo
"Use `drop_first=True` para dejar una categoria base y evitar colinealidad perfecta. Asi la matriz queda mas limpia para modelos posteriores."

---

## 3.7 Por que termine con `X_train_preparado`, `X_test_preparado`, `y_train_objetivo`, `y_test_objetivo`

### Que hice
La salida final de la fase 3 no fue una sola matriz ambigua, sino cuatro objetos claramente separados:

- `X_train_preparado`
- `X_test_preparado`
- `y_train_objetivo`
- `y_test_objetivo`

### Por que lo hice
Porque eso deja la base lista para la fase de modelamiento:

- entrenamiento;
- evaluacion;
- comparacion de algoritmos;
- ajuste de parametros.

### Como defenderlo
"Termine con X e y separados para train y test porque queria dejar la base lista para modelar sin volver a rehacer la preparacion de datos."

---

# 4. Respuestas cortas para preguntas probables del profesor

## Por que usaste KNNImputer y no media o mediana?
"Porque la precipitacion depende mucho del contexto geografico y temporal. Una media global me parecia demasiado pobre. KNN usa observaciones parecidas y conserva mejor la logica del fenomeno."

## Por que estandarizaste antes del KNN?
"Porque KNN calcula distancias. Si no estandarizaba, variables como altura dominaban artificialmente el calculo."

## Por que no eliminaste outliers?
"Porque en precipitacion un valor alto puede ser un evento real, no necesariamente un error. Preferi marcar y caracterizar extremos en vez de borrarlos automaticamente."

## Por que usaste log1p?
"Porque `agua_caida` estaba muy sesgada hacia cero. `log1p` reduce asimetria y mantiene interpretables los ceros."

## Por que usaste one-hot encoding?
"Porque `nombre_region` y `zona_geografica` son categorias nominales sin orden natural. Con numeros simples habria impuesto jerarquias falsas."

## Por que creaste variables seno y coseno?
"Porque el tiempo es ciclico. Diciembre y enero estan cerca climaticamente, aunque numericamente 12 y 1 parezcan lejanos."

## Por que hiciste una macrozona?
"Porque queria una representacion territorial mas agregada. Me ayuda a capturar patrones climaticos grandes que a veces una region aislada no muestra tan bien."

## Por que separaste lluvia_evento de agua_caida?
"Porque una cosa es que llueva y otra cosa es cuanto llueva. Separar ocurrencia de intensidad mejora la interpretacion del fenomeno."

## Por que el escalador se ajusto solo con train?
"Para evitar fuga de informacion. Si uso todo el dataset para ajustar el escalador, el bloque futuro influye en la transformacion del pasado."

## Por que no usaste targets imputados en fase 3?
"Porque queria que la etapa supervisada trabajara con precipitaciones realmente observadas, no con etiquetas estimadas."

---

# 5. Idea central que debes aprenderte

Si tuvieras que resumir todo el trabajo en pocas lineas, la idea mas importante seria esta:

"En la fase 1 justifique el problema y el valor del analisis. En la fase 2 entendi la estructura real del dataset, trate nulos con una estrategia contextual, caracterice outliers sin eliminar informacion util y cree variables mas representativas del fenomeno. En la fase 3 deje una base preparada para modelamiento, cuidando no mezclar targets imputados ni introducir fuga de informacion en el escalamiento."

Si logras explicar eso con seguridad, ya tienes una muy buena base para defender el notebook frente a tu profesor.
