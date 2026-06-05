# Tareas pendientes y revisión — `EV2_BIY7121_005D.ipynb`

Documento de trabajo para el equipo. Estado al cierre de la última sesión. El notebook **no está terminado** y tiene puntos que requieren revisión crítica antes de la entrega. Leer completo antes de tocar nada.

---

## 1. La visión del proyecto (el foco)

El proyecto NO predice lluvia con regresión, y esto es deliberado. Hay que entenderlo o se rompe la coherencia:

- **Regresión → temperatura media diaria (`temp_media`).** Se eligió temperatura, no lluvia, porque la lluvia diaria es caótica (se midió: ~89% de la varianza es ruido temporal, techo R²≈0.18-0.42 aun con datos atmosféricos). La temperatura sí es predecible. Quien proponga "volver a predecir lluvia con regresión" debe leer la descomposición de varianza de la Fase 5 antes.
- **Clasificación → `llueve`** (sí/no llueve). La lluvia se modela como problema binario, no numérico.
- **Cluster → perfiles climáticos de estaciones**.
- **Dos datasets**: el meteorológico de la DMC (medido) y el atmosférico de NASA POWER (reanálisis). El segundo aporta humedad, viento, radiación, nubosidad. **No reemplazar el dato medido por el modelado.**

Regla dura: el proyecto sigue `estructura.txt` al pie de la letra. No renombrar ni reordenar secciones numeradas.

---

## 2. Resultados actuales (medidos, no optimistas)

| Familia | Mejor modelo | Métrica | Lectura honesta |
|---|---|---|---|
| Regresión | SVR RBF | R²=0.931, RMSE=1.46°C | Bueno. Sin sobreajuste (brecha train-test ≈0). |
| Clasificación | Árbol | F1=0.766, balanced acc=0.836 | Aceptable. `llueve` se predice razonable, no excelente. |
| Cluster | K-Means K=4 | silhouette=0.481 | Mediocre-aceptable. Los grupos tienen sentido geográfico pero la cohesión es baja. |

No vender estos números como sobresalientes. La regresión es sólida; clasificación y cluster son correctos pero no destacados.

---

## 3. Lo que falta (obligatorio antes de entregar)

- [ ] **Fase 6 — Deploy.** Está VACÍA (1 celda de código sin contenido). El material 3.6 (Toma de Decisiones) la cubre. Falta la ficha de despliegue: entradas, salidas, usuario, frecuencia, riesgos, e integración con la toma de decisiones. Es la única sección de la estructura sin hacer.

---

## 4. Puntos débiles a revisar (no son errores de ejecución, son de criterio)

- [ ] **Cluster con silhouette bajo (0.48).** Funciona y los grupos son coherentes (norte/centro/sur), pero la cohesión es baja. Se excluyeron 2 estaciones insulares (Isla de Pascua, Juan Fernández) porque colapsaban el modelo. Verificar si esa decisión está bien justificada en el texto. No subir el silhouette artificialmente con K=2 (da 0.9 pero no segmenta nada).
- [ ] **`lluvia_intensa` quedó como variable de EDA, no de modelo.** Se descartó como objetivo de clasificación por ser rara (~4.6%) y caótica. Confirmar que ningún texto la presente como objetivo predictivo. Se definió con la regla de Tukey (Q3+1.5·IQR) y tiene una versión "sostenida" (con persistencia). Revisar que el relato sea consistente.
- [ ] **Gráfico SVM 2D es ilustrativo, no real.** Usa solo 2 de ~25 variables (no se puede dibujar el margen real en 25D). El texto lo aclara, pero verificar que nadie lo interprete como el desempeño real del modelo.
- [ ] **El R² alto de regresión (0.93) puede dar falsa impresión.** Es temperatura, no lluvia. La temperatura es intrínsecamente más fácil. No presentarlo como "predecimos el clima con 93% de acierto".

---

## 5. Verificación de errores (hacer SIEMPRE antes de entregar)

Ejecutar el notebook completo y confirmar que no hay fallos:

```bash
python -m nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=1800 EV2_BIY7121_005D.ipynb
```

Checklist post-ejecución (al cierre de esta sesión, todo OK menos Deploy):
- [x] 0 errores de ejecución
- [x] 0 warnings (stderr) en outputs
- [x] Numeración de celdas monótona (1→98, sin saltos)
- [x] Sin celdas de código vacías salvo Fase 6 (placeholder)
- [x] Sin imports muertos, sin DataFrames "de la nada"
- [ ] Fase 6 completada (pendiente)

Requiere **internet**: los datos se leen en memoria desde raw URLs de GitHub (`4mnesia/DataMining_Datos`), no hay archivos locales.

---

## 6. Reglas de trabajo (no romper)

- Solo técnicas estándar: `LinearRegression`, `LogisticRegression`, `DecisionTree`, `SVR/SVC`, `KMeans`, `AgglomerativeClustering`. Prohibido RandomForest, XGBoost, redes, Prophet, AutoML.
- **No citar el material de apoyo ni los módulos del curso dentro del notebook.** Es contexto interno, no va en la entrega.
- Patrón por paso: encabezado breve → código → análisis del output. Sin "mini-objetivos".
- Fase 4 = vista general de modelos. Fase 5 = evaluación (métricas, matrices, codo/silhouette, sobreajuste). No mezclar.
- De cada modelo de regresión y clasificación se prueban 3 versiones de hiperparámetros; se elige por validación, sin tocar el test.
- Imputación y escalado se ajustan SOLO en train (sin fuga). Split temporal 2021-2023 / 2024-2025.
- Español sin tildes en código y markdown del notebook (estilo consistente).
- El `.ipynb` pesa ~7 MB (gráficos embebidos). No abrirlo entero en editores lentos; editar por celda.

---

## 7. Cómo está armado (para ubicarse rápido)

- **Fase 1** Negocio: contexto, 4 hipótesis (patrones de datos), KPIs, problema.
- **Fase 2** Datos: tipos, nulos, outliers, encoding, estadísticos, correlaciones, nuevas variables, Köppen, mapas territoriales, dataset atmosférico, IQR por agrupador. Es la fase más larga.
- **Fase 3** Transformación: solo escalamiento.
- **Fase 4** Modelamiento: 4.1 regresión (con gráficos por modelo), 4.2 clasificación, 4.3 cluster.
- **Fase 5** Evaluación: métricas, matrices de confusión, ROC, residuos, codo, silhouette, dendrograma, mapas. Bien cubierta.
- **Fase 6** Deploy: VACÍA.
