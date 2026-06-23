# Plan del panel de control en Power BI

**Fuente:** `panel_powerbi.csv` (un archivo, todo el dataset tratado).
**Medidas:** las de `medidas_dax.md` (las de desempeño filtran `conjunto = "test"`).

Dos tipos de visual:
- 🟦 **Nativo** = visual estándar de Power BI con campos/medidas (mapas, barras, líneas, KPIs).
- 🐍 **Visual de Python** = *Objeto visual de Python* que ejecuta matplotlib con los datos en vivo (ROC, matriz de confusión, dendrograma, etc.). **No son fotos**: se filtran y recalculan con los segmentadores del informe.

### Cómo usar el visual de Python (una vez)
1. *Archivo → Opciones → Scripting de Python* → apuntar al entorno que tenga `pandas`, `matplotlib`, `numpy`, `scikit-learn`, `scipy` (el mismo venv que usas en el notebook sirve).
2. *Insertar → Objeto visual de Python*. Arrastrar los campos indicados al área **Valores**.
3. Power BI entrega esos campos como un DataFrame llamado **`dataset`** (quita filas duplicadas → por eso se incluye `codigo_estacion` y `fecha`, para conservar todas las filas).
4. Pegar el código y *Ejecutar*. Filtrar la página a `conjunto = "test"` para los gráficos de desempeño.

---

## Página 1 — Resumen ejecutivo
| Visual | Tipo | Campos / Medidas |
|---|---|---|
| 6 tarjetas KPI | 🟦 Nativo | `Accuracy Lluvia`, `F1 Lluvia`, `Recall Lluvia`, `R2 Temp`, `RMSE Temp`, `MAE Temp` |
| Mapa de estaciones | 🟦 Nativo (Mapa) | Lat=`latitud`, Long=`longitud`, Leyenda=`cluster_koppen_dominante`, Tamaño=`precip_anual` |
| Texto | 🟦 Nativo | "Mejores modelos: Árbol (lluvia) · SVR (temperatura) · K-Means K=7 (zonas)" |

## Página 2 — Pronóstico de lluvia
| Visual | Tipo | Campos / código |
|---|---|---|
| Acierto por zona | 🟦 Nativo (barras) | Eje=`zona_koppen_codigo`, Valor=`Recall Lluvia` |
| Acierto por mes | 🟦 Nativo (columnas) | Eje=`mes`, Valor=`Accuracy Lluvia` |
| **Curva ROC** | 🐍 Python | Valores: `codigo_estacion`, `fecha`, `llueve`, `llueve_score` |
| **Matriz de confusión** | 🐍 Python | Valores: `codigo_estacion`, `fecha`, `llueve`, `llueve_pred` |

## Página 3 — Temperatura
| Visual | Tipo | Campos / código |
|---|---|---|
| Real vs predicha (tiempo) | 🟦 Nativo (líneas) | Eje=`fecha`, Valores=`temp_media`, `temp_pred` |
| Tarjetas | 🟦 Nativo | `R2 Temp`, `RMSE Temp`, `MAE Temp` |
| **Reales vs predichos** | 🐍 Python | Valores: `codigo_estacion`, `fecha`, `temp_media`, `temp_pred` |
| **Distribución de residuos** | 🐍 Python | Valores: `codigo_estacion`, `fecha`, `temp_media`, `temp_pred` |

## Página 4 — Segmentación (cluster)
| Visual | Tipo | Campos / código |
|---|---|---|
| Mapa por cluster | 🟦 Nativo (Mapa) | Lat=`latitud`, Long=`longitud`, Leyenda=`cluster_grupo` |
| Tabla de grupos | 🟦 Nativo | `cluster_grupo`, `cluster_koppen_dominante`, prom. `precip_anual`, `aridez` |
| **Dendrograma** | 🐍 Python | Valores: `codigo_estacion`, `precip_anual`, `temp_media`, `aridez` |
| **Codo + silhouette** | 🐍 Python | Valores: `codigo_estacion`, `precip_anual`, `temp_media`, `aridez` |

## Página 5 — Insights y acciones (texto + visuales de apoyo)
Las 3 acciones estratégicas (ya en la Fase 6 del notebook) con un visual nativo al lado cada una.

---

# Código de los visuales de Python (copiar/pegar)

> En todos: el DataFrame de entrada se llama `dataset`. Terminar siempre con `plt.show()`.

### Curva ROC  (filtrar página a `conjunto = "test"`)
```python
import matplotlib.pyplot as plt
from sklearn.metrics import roc_curve, roc_auc_score
d = dataset.dropna(subset=['llueve', 'llueve_score'])
fpr, tpr, _ = roc_curve(d['llueve'], d['llueve_score'])
auc = roc_auc_score(d['llueve'], d['llueve_score'])
plt.figure(figsize=(6, 6))
plt.plot(fpr, tpr, linewidth=2, label=f'Arbol (AUC = {auc:.3f})')
plt.plot([0, 1], [0, 1], '--', color='gray', label='Azar')
plt.xlabel('Tasa de falsos positivos'); plt.ylabel('Tasa de verdaderos positivos')
plt.title('Curva ROC - pronostico de lluvia'); plt.legend(loc='lower right')
plt.tight_layout(); plt.show()
```

### Matriz de confusión  (filtrar a `conjunto = "test"`)
```python
import matplotlib.pyplot as plt, numpy as np
from sklearn.metrics import confusion_matrix
d = dataset.dropna(subset=['llueve', 'llueve_pred'])
cm = confusion_matrix(d['llueve'].astype(int), d['llueve_pred'].astype(int))
fig, ax = plt.subplots(figsize=(5, 4))
ax.imshow(cm, cmap='Blues')
for (i, j), v in np.ndenumerate(cm):
    ax.text(j, i, f'{v:,}', ha='center', va='center')
ax.set_xticks([0, 1]); ax.set_xticklabels(['No', 'Si'])
ax.set_yticks([0, 1]); ax.set_yticklabels(['No', 'Si'])
ax.set_xlabel('Predicho'); ax.set_ylabel('Real'); ax.set_title('Matriz de confusion - lluvia')
plt.tight_layout(); plt.show()
```

### Reales vs predichos (temperatura)  (filtrar a `conjunto = "test"`)
```python
import matplotlib.pyplot as plt
d = dataset.dropna(subset=['temp_media', 'temp_pred'])
lo, hi = d['temp_media'].min(), d['temp_media'].max()
plt.figure(figsize=(6, 6))
plt.scatter(d['temp_media'], d['temp_pred'], s=6, alpha=0.15)
plt.plot([lo, hi], [lo, hi], '--', color='red')
plt.xlabel('Temperatura real (C)'); plt.ylabel('Temperatura predicha (C)')
plt.title('Real vs predicho - temperatura (SVR)')
plt.tight_layout(); plt.show()
```

### Distribución de residuos  (filtrar a `conjunto = "test"`)
```python
import matplotlib.pyplot as plt
d = dataset.dropna(subset=['temp_media', 'temp_pred'])
res = d['temp_media'] - d['temp_pred']
plt.figure(figsize=(7, 4))
plt.hist(res, bins=60)
plt.axvline(0, color='red', linestyle='--')
plt.xlabel('Residuo (real - predicho, C)'); plt.ylabel('Frecuencia')
plt.title('Distribucion de residuos'); plt.tight_layout(); plt.show()
```

### Dendrograma de estaciones  (sin filtro de conjunto)
```python
import matplotlib.pyplot as plt
from scipy.cluster.hierarchy import linkage, dendrogram
from sklearn.preprocessing import QuantileTransformer
est = dataset.dropna(subset=['precip_anual']).groupby('codigo_estacion')[['precip_anual', 'temp_media', 'aridez']].mean()
X = QuantileTransformer(n_quantiles=min(40, len(est)), random_state=42).fit_transform(est)
Z = linkage(X, method='ward')
plt.figure(figsize=(10, 4))
dendrogram(Z, no_labels=True, color_threshold=Z[-6, 2])
plt.title('Dendrograma de estaciones'); plt.ylabel('Distancia de union')
plt.tight_layout(); plt.show()
```

### Codo + silhouette  (sin filtro de conjunto)
```python
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
from sklearn.metrics import silhouette_score
from sklearn.preprocessing import QuantileTransformer
est = dataset.dropna(subset=['precip_anual']).groupby('codigo_estacion')[['precip_anual', 'temp_media', 'aridez']].mean()
X = QuantileTransformer(n_quantiles=min(40, len(est)), random_state=42).fit_transform(est)
ks = list(range(2, 11)); inercia = []; sil = []
for k in ks:
    km = KMeans(n_clusters=k, random_state=42, n_init=10).fit(X)
    inercia.append(km.inertia_); sil.append(silhouette_score(X, km.labels_))
fig, ax = plt.subplots(1, 2, figsize=(11, 4))
ax[0].plot(ks, inercia, 'o-'); ax[0].axvline(7, color='red', ls=':'); ax[0].set_title('Metodo del codo'); ax[0].set_xlabel('K'); ax[0].set_ylabel('Inercia')
ax[1].plot(ks, sil, 's-'); ax[1].set_title('Silhouette por K'); ax[1].set_xlabel('K'); ax[1].set_ylabel('Silhouette')
plt.tight_layout(); plt.show()
```

---

# Visuales de Python adicionales (insights y mejor modelo)

> Probados contra el CSV real: todos quedan bajo el límite de 150.000 filas. Mismo patrón: el DataFrame de entrada se llama `dataset` y se termina con `plt.show()`.

### Mapa de clusters — el mejor modelo (K-Means) en el territorio  (sin filtro de conjunto)
Página sugerida: 4 (Segmentación). **Valores:** `codigo_estacion`, `latitud`, `longitud`, `cluster_grupo`, `cluster_koppen_dominante`.
```python
import matplotlib.pyplot as plt
import numpy as np
est = dataset.dropna(subset=['latitud', 'longitud', 'cluster_grupo']).copy()
est['cluster_grupo'] = est['cluster_grupo'].astype(int)
est = est.groupby('codigo_estacion').first().reset_index()
colores = plt.cm.tab10(np.linspace(0, 1, 10))
plt.figure(figsize=(6, 9))
for g in sorted(est['cluster_grupo'].unique()):
    sub = est[est['cluster_grupo'] == g]
    etq = sub['cluster_koppen_dominante'].iloc[0]
    nombre = f'Grupo {g} ({etq})' if g != -1 else 'Insular (sin grupo)'
    plt.scatter(sub['longitud'], sub['latitud'], s=45, color=colores[g % 10],
                label=nombre, edgecolor='gray', linewidth=0.3)
plt.xlabel('Longitud'); plt.ylabel('Latitud')
plt.title('Segmentacion de estaciones (K-Means)')
plt.legend(fontsize=7, loc='upper left')
plt.tight_layout(); plt.show()
```

### Acierto del pronostico de lluvia por mes  (filtrar a `conjunto = "test"`)
Página sugerida: 2 (Pronóstico). **Valores:** `codigo_estacion`, `fecha`, `mes`, `acierto_lluvia`.
```python
import matplotlib.pyplot as plt
d = dataset.dropna(subset=['mes', 'acierto_lluvia'])
acc = d.groupby('mes')['acierto_lluvia'].mean()
meses = ['Ene', 'Feb', 'Mar', 'Abr', 'May', 'Jun', 'Jul', 'Ago', 'Sep', 'Oct', 'Nov', 'Dic']
plt.figure(figsize=(9, 4))
plt.bar(acc.index, acc.values, color='#4C72B0')
plt.xticks(acc.index, [meses[int(m) - 1] for m in acc.index])
plt.ylim(0, 1); plt.ylabel('Acierto (accuracy)'); plt.xlabel('Mes')
plt.title('Acierto del pronostico de lluvia por mes (test)')
for x, y in zip(acc.index, acc.values):
    plt.text(x, y + 0.01, f'{y:.0%}', ha='center', fontsize=8)
plt.tight_layout(); plt.show()
```

### Perfil climatico de cada cluster — insight para las acciones  (sin filtro de conjunto)
Página sugerida: 4 o 5 (Insights). **Valores:** `codigo_estacion`, `cluster_grupo`, `precip_anual`, `temp_media`, `aridez`.
```python
import matplotlib.pyplot as plt
est = dataset.dropna(subset=['cluster_grupo']).copy()
est['cluster_grupo'] = est['cluster_grupo'].astype(int)
est = est.groupby('codigo_estacion').agg(
    cluster_grupo=('cluster_grupo', 'first'),
    precip_anual=('precip_anual', 'mean'),
    temp_media=('temp_media', 'mean'),
    aridez=('aridez', 'mean')).reset_index()
perfil = est.groupby('cluster_grupo')[['precip_anual', 'temp_media', 'aridez']].mean().sort_index()
fig, ax = plt.subplots(1, 3, figsize=(12, 4))
cols = [('precip_anual', 'Precip. anual (mm)', '#4C72B0'),
        ('temp_media', 'Temp. media (C)', '#DD8452'),
        ('aridez', 'Indice de aridez', '#55A868')]
for a, (col, tit, c) in zip(ax, cols):
    a.barh(perfil.index.astype(str), perfil[col], color=c)
    a.set_title(tit); a.set_ylabel('Cluster')
plt.suptitle('Perfil climatico medio por cluster')
plt.tight_layout(); plt.show()
```

### Precision de la regresion de temperatura por macrozona  (filtrar a `conjunto = "test"`)
Página sugerida: 3 (Temperatura). **Valores:** `codigo_estacion`, `fecha`, `macrozona`, `temp_error_abs`.
```python
import matplotlib.pyplot as plt
d = dataset.dropna(subset=['macrozona', 'temp_error_abs'])
err = d.groupby('macrozona')['temp_error_abs'].mean().sort_values()
plt.figure(figsize=(8, 4))
plt.barh(err.index, err.values, color='#C44E52')
plt.xlabel('Error absoluto medio de temperatura (C)')
plt.title('Precision de la regresion por macrozona (test)')
for y, (i, v) in enumerate(err.items()):
    plt.text(v + 0.02, y, f'{v:.2f}', va='center', fontsize=8)
plt.tight_layout(); plt.show()
```

---

**Notas:**
- El visual de Python funciona en **Power BI Desktop**; límite de 150.000 filas por visual (el test son 56k, OK).
- Para que la ROC funcione agrego la columna **`llueve_score`** (probabilidad del modelo) al CSV.
- Si un visual de Python sale vacío, revisar que se incluyó `codigo_estacion` + `fecha` en *Valores* (evita que Power BI colapse filas duplicadas).
