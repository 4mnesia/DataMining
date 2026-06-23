# Tareas pendientes y revisión — `EV2_BIY7121_005D.ipynb`

Documento de trabajo para el equipo. Estado al cierre de la última sesión. El notebook **no está terminado** y tiene puntos que requieren revisión crítica antes de la entrega. Leer completo antes de tocar nada.

---

## 1. La visión del proyecto (el foco)

El proyecto NO predice lluvia con regresión, y esto es deliberado. Hay que entenderlo o se rompe la coherencia:

- **Regresión → temperatura media diaria (`temp_media`).** Se eligió temperatura, no lluvia, porque la lluvia diaria es caótica (se midió: ~89% de la varianza es ruido temporal, techo R²≈0.18-0.42 aun con datos atmosféricos). La temperatura sí es predecible. Quien proponga "volver a predecir lluvia con regresión" debe leer la descomposición de varianza de la Fase 5 antes.
- **Pronóstico → `llueve`** (sí/no llueve). La lluvia se modela como problema binario, no numérico. Es un **pronóstico honesto**: excluye la temperatura del mismo día (solo info disponible de antemano); la versión de cada modelo se elige por validación cruzada.
- **Cluster → perfiles climáticos de estaciones**.
- **Dos datasets**: el meteorológico de la DMC (medido) y el atmosférico de NASA POWER (reanálisis). El segundo aporta humedad, viento, radiación, nubosidad. **No reemplazar el dato medido por el modelado.**

Regla dura: el proyecto sigue `estructura.txt` al pie de la letra. No renombrar ni reordenar secciones numeradas.

---

## 2. Resultados actuales (medidos, no optimistas)

| Familia | Mejor modelo | Métrica | Lectura honesta |
|---|---|---|---|
| Regresión | SVR RBF | R²=0.932, RMSE=1.40°C | Bueno. Sin sobreajuste (brecha train-test ≈0). |
| Pronóstico (clasif.) | Árbol depth=16 | F1=0.792, balanced acc=0.849 | Aceptable. `llueve` se pronostica razonable, no excelente. Honesto: sin temp del mismo día. |
| Cluster (HK1 espacial) | K-Means K=7 (zonas ≥3 est.) | silhouette=0.47, Hopkins=0.68 (intr.) / ARI=0.33, NMI=0.53, FM=0.44 (extr. vs Köppen) | K=7 = zonas Köppen bien representadas (10 presentes, 3 marginales). Clima medido (precipitación anual + temperatura media + índice de aridez; Köppen = etiqueta externa). |
| Cluster (HK2 temporal) | K-Means K=4 (mes×estación normalizado) | ARI=0.34, NMI=0.39 (vs estación del año) | Recupera las 4 estaciones del año desde las mediciones mensuales normalizadas por estación; verano/invierno nítidos, otoño/primavera transición. |

No vender estos números como sobresalientes. La regresión es sólida; clasificación y cluster son correctos pero no destacados. El cluster cubre el marco del PPT (Modulo 3, 3 tareas): tendencia (Hopkins=0.68), n° clusters (codo/KneeLocator), calidad INTRÍNSECA (silhouette, inercia, Calinski-Harabasz) y EXTRÍNSECA vs Köppen (ARI, NMI, Fowlkes-Mallows). Köppen no se usa como variable. Calinski-Harabasz SÍ se usa (lo pide el PPT).

---

## 3. Lo que falta (obligatorio antes de entregar)

- [x] **Fase 6 — Deploy.** HECHA. La celda de la Fase 6 puntúa todas las filas con el mejor modelo de cada familia (SVR temperatura, Árbol lluvia, K-Means cluster) y exporta un único `powerbi/panel_powerbi.csv` (dataset tratado + predicciones + `conjunto` train/test). Más abajo hay insights y 3 acciones estratégicas (apoyo a la toma de decisiones).
- [x] **Panel de control en Power BI.** Carpeta `powerbi/`: `panel_powerbi.csv` (datos exportados), `medidas_dax.md` (medidas DAX; las de desempeño filtran `conjunto = "test"`), `plan_powerbi.md` (5 páginas, visuales nativos + visuales de Python para ROC/confusión/residuos/dendrograma).

> **Estado:** la estructura obligatoria (6 fases) está completa. Queda pulir/entregar.

---

## 4. Puntos débiles a revisar (no son errores de ejecución, son de criterio)

- [ ] **Cluster K=7 = zonas Köppen con ≥3 estaciones (validación intrínseca + extrínseca).** 7 de 10 zonas presentes (3 marginales Csc/Dfc/ET con 1-2 est. se documentan, no forman grupo). Sobre **clima MEDIDO** (precipitación anual + temperatura media + índice de aridez (balance hídrico)); **Köppen se reserva como etiqueta externa**. Validación PPT Módulo 3: tendencia (Hopkins=0.68), codo/KneeLocator (K=4), intrínseca (silhouette=0.47, Calinski-Harabasz=200) y extrínseca vs Köppen (ARI=0.33, NMI=0.53, Fowlkes-Mallows=0.44). El núcleo precip+temp+aridez maximiza la recuperación de Köppen. Se excluyen 2 insulares. Nota: por silhouette gana K=4 (macro), pero se destaca K=7 porque recupera mejor las zonas (ARI) — verificar que el texto justifique esa elección.
- [ ] **`lluvia_intensa` quedó como variable de EDA, no de modelo.** Se descartó como objetivo de clasificación por ser rara (~4.6%) y caótica. Confirmar que ningún texto la presente como objetivo predictivo. Se definió con la regla de Tukey (Q3+1.5·IQR) y tiene una versión "sostenida" (con persistencia). Revisar que el relato sea consistente.
- [ ] **Gráfico SVM 2D es ilustrativo, no real.** Usa solo 2 de ~25 variables (no se puede dibujar el margen real en 25D). El texto lo aclara, pero verificar que nadie lo interprete como el desempeño real del modelo.
- [ ] **El R² alto de regresión (0.93) puede dar falsa impresión.** Es temperatura, no lluvia. La temperatura es intrínsecamente más fácil. No presentarlo como "predecimos el clima con 93% de acierto".
- [x] **Datos exportados al CSV: auditados y curados (caja negra → datos limpios).** El export parte de `df_eda` pero la Fase 6 lo deja en **36 columnas esenciales, 0 nulos**. Correcciones aplicadas: (a) consistencia física de temperatura `temp_min ≤ temp_media ≤ temp_max` (la imputación por mediana dejaba ~0.5% de filas con el orden roto → `rango_termico` negativo; se reordena el trío por fila); (b) se quitan `isoterma_cero` (artefacto de ingeniería, rango imposible, redundante con altura+temp) y `lluvia_ayer` (lag de modelado sin insight propio en el panel). `agua_caida` cruda se descarta (se conserva `agua_caida_tratada`, capada a 150 mm). Los "outliers" de lluvia/altura que marca el IQR NO son errores: son la naturaleza real del dato (lluvia cero-inflada, estaciones de cordillera).

---

## 5. Verificación de errores (hacer SIEMPRE antes de entregar)

Ejecutar el notebook completo y confirmar que no hay fallos:

```bash
python -m nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=1800 EV2_BIY7121_005D.ipynb
```

Checklist post-ejecución:
- [x] 0 errores de ejecución
- [x] 0 warnings (stderr) en outputs
- [x] Numeración de celdas monótona, sin saltos
- [x] Sin celdas de código vacías
- [x] Sin imports muertos, sin DataFrames "de la nada"
- [x] Fase 6 completada (Deploy + export a Power BI)
- [x] Notebook escrito sin tildes (estilo consistente; se conservan Köppen, R², °C)

Requiere **internet**: los datos se leen en memoria desde raw URLs de GitHub (`4mnesia/DataMining_Datos`), no hay archivos locales.

---

## 6. Reglas de trabajo (no romper)

- Solo técnicas estándar (del curso): `LinearRegression`, `LogisticRegression`, `DecisionTree`, `SVR/SVC`, `GaussianNB`, `KMeans`, `AgglomerativeClustering`, `KNeighborsClassifier` (KNN, material 2.7). Prohibido RandomForest, XGBoost, redes, Prophet, AutoML.
- **No citar el material de apoyo ni los módulos del curso dentro del notebook.** Es contexto interno, no va en la entrega.
- Patrón por paso: encabezado breve → código → análisis del output. Sin "mini-objetivos".
- Fase 4 = vista general de modelos. Fase 5 = evaluación (métricas, matrices, codo/silhouette, sobreajuste). No mezclar.
- De cada modelo de regresión y clasificación se prueban 3 versiones de hiperparámetros; se elige por validación, sin tocar el test.
- Imputación y escalado se ajustan SOLO en train (sin fuga). Split aleatorio 70/30.
- Español sin tildes en código y markdown del notebook (estilo consistente).
- El `.ipynb` pesa ~7 MB (gráficos embebidos). No abrirlo entero en editores lentos; editar por celda.

---

## 7. Cómo está armado (para ubicarse rápido)

- **Fase 1** Negocio: contexto, **6 hipótesis (2 por familia de modelo, trazables a F4/F5: HR1/HR2, HC1/HC2, **HK1 cluster espacial→Köppen / HK2 cluster temporal→estaciones del año**)**, KPIs, problema.
- **Fase 2** Datos: tipos, nulos, outliers, encoding, estadísticos, **2 matrices de correlación (inicial 2.6 + general 2.9)**, nuevas variables, Köppen, mapas territoriales, dataset atmosférico, IQR por agrupador. Es la fase más larga.
- **Fase 3** Transformación: solo escalamiento.
- **Fase 4** Modelamiento: 4.1 regresión (con gráficos por modelo), 4.2 clasificación, 4.3 cluster.
- **Fase 5** Evaluación: métricas, matrices de confusión, ROC, residuos, codo, silhouette, dendrograma, mapas. Bien cubierta.
- **Fase 6** Deploy: VACÍA.
