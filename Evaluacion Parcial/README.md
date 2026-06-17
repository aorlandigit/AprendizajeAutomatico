# Southwave - Clasificación Automática de Perfiles Térmicos CORAL

Este repositorio contiene el desarrollo del proyecto **Southwave**, orientado al análisis y clasificación automática de perfiles térmicos atmosféricos obtenidos mediante el sistema lidar **CORAL**.

El objetivo principal del proyecto es aplicar técnicas de aprendizaje automático no supervisado para identificar patrones recurrentes en perfiles verticales de temperatura nocturna.

El modelo final permite recibir un archivo CSV original del sistema CORAL, procesarlo automáticamente, clasificar sus perfiles térmicos en clusters estadísticos y generar un reporte gráfico de la noche analizada.

---

## Objetivo del proyecto

El proyecto busca construir una herramienta que permita:

- procesar archivos CSV originales del sistema CORAL;
- reconstruir perfiles térmicos verticales;
- aplicar criterios de calidad y preprocesamiento;
- imputar valores faltantes mediante KNN;
- clasificar perfiles mediante un modelo K-Means previamente entrenado;
- resumir la composición de clusters de una noche;
- generar un reporte gráfico automático.

La salida del modelo corresponde a una clasificación estadística de perfiles térmicos. Los clusters identificados no deben interpretarse todavía como categorías atmosféricas físicas definitivas. La interpretación física queda planteada como una etapa posterior de validación con especialistas.

---

## Estructura del repositorio

La organización general sigue una estructura inspirada en Cookiecutter Data Science:

```text
southwave-coral-model/
│
├── data/
│   ├── raw/                  # Link a archivo unificado
|   |                         # Archivo de ejemplo
│   ├── interim/              # Listado de Archivos intermedios
|   |                           generados durante el procesamiento
│   └── processed/            # Link a Dataset final utilizado para modelado
│
├── models/                   # Modelo entrenado y objetos serializados
│   └── modelo_coral_kmeans_k4.pkl
│
├── notebooks/                # Notebooks del proyecto
│   └── Nb10_aplicacion_modelo_southwave.ipynb
│
├── reports/
│   ├── figures/              # Imagenes utilizadas en Entregas
|   |                         # Ejemplo de Reportes gráficos generados automáticamente
│   └── entregas/             # Entregas del proyecto
│
├── references/               # Documentación de referencia
│
├── requirements.txt          # Dependencias necesarias
│
└── README.md
```

---

## Modelo incluido

El modelo operativo se encuentra en:

```text
models/modelo_coral_kmeans_k4.pkl
```

Este archivo contiene:

- modelo K-Means entrenado con `k = 4`;
- imputador KNN entrenado sobre el Dataset C;
- lista de alturas utilizadas por el modelo;
- metadatos del entrenamiento.

El modelo fue entrenado sobre perfiles térmicos entre aproximadamente **15 km y 80 km** de altura.

---

## Datos de entrada

El notebook operativo espera recibir un archivo CSV original del sistema CORAL, con las columnas:

```text
time
altitude
temperature
```

Cada archivo puede contener múltiples perfiles de una misma noche. El notebook reconstruye automáticamente la matriz:

```text
perfil × altura
```

donde cada fila corresponde a un perfil térmico y cada columna a una altura.

---

## Preprocesamiento aplicado

Para cada archivo CORAL nuevo, el pipeline realiza:

1. lectura del CSV original;
2. limpieza de nombres de columnas;
3. reconstrucción de perfiles térmicos mediante pivot;
4. recorte vertical al rango usado por el modelo;
5. eliminación de perfiles completamente vacíos o en cero;
6. conversión de ceros a valores faltantes;
7. alineación con las alturas utilizadas durante el entrenamiento;
8. imputación de valores faltantes con KNN;
9. clasificación de perfiles mediante K-Means.

---

## Salidas generadas

El notebook genera:

### 1. Clasificación por perfil

Una tabla con:

```text
archivo | fecha | time | cluster | n_imputados
```

### 2. Resumen nocturno

Incluye:

- cantidad de perfiles originales;
- cantidad de perfiles descartados;
- cantidad de perfiles válidos;
- distribución de clusters;
- cluster dominante;
- secuencia temporal de clusters.

### 3. Reporte gráfico automático

El reporte se guarda automáticamente en:

```text
reports/figures/
```

con nombre:

```text
reporte_YYYYMMDD.png
```

El reporte incluye:

- resumen del archivo analizado;
- resultados de clasificación;
- evolución temporal de clusters;
- perfiles térmicos coloreados por cluster;
- indicación de tramos imputados.

---

## Dependencias principales

El proyecto utiliza principalmente:

```text
numpy
pandas
matplotlib
scikit-learn
joblib
jupyter
ipykernel
```

Para reproducir el entorno de manera más precisa, se recomienda mantener versiones compatibles con aquellas utilizadas al momento de entrenar y exportar el modelo.

---

## Uso del notebook operativo

El notebook principal para aplicar el modelo es:

```text
notebooks/10_aplicacion_modelo_southwave.ipynb
```

Dentro del notebook, modificar la variable:

```python
archivo_csv = BASE_DIR / "data" / "raw" / "nombre_del_archivo.csv"
```

Luego ejecutar todas las celdas.

El notebook:

1. carga el modelo entrenado;
2. procesa el archivo CORAL;
3. clasifica los perfiles;
4. genera tablas de resultados;
5. guarda el reporte gráfico automáticamente.

---

## Interpretación de los resultados

Los clusters obtenidos representan patrones térmicos recurrentes identificados automáticamente por el algoritmo.

Por ejemplo, una noche puede presentar:

```text
C0: 2 perfiles
C1: 2 perfiles
C2: 0 perfiles
C3: 3 perfiles
```

y una secuencia temporal como:

```text
C0 → C0 → C3 → C3 → C1 → C1 → C3
```

Esto indica una evolución de patrones térmicos durante la noche, pero no implica todavía una clasificación física definitiva como "noche tranquila", "ondas de montaña" o "doble estratopausa".

---

## Limitaciones

- El modelo clasifica perfiles en clusters estadísticos, no en categorías atmosféricas físicas definitivas.
- La interpretación física de los clusters requiere validación con especialistas.
- El modelo fue entrenado sobre datos CORAL procesados entre 15 km y 80 km.
- Los archivos de entrada deben conservar la estructura esperada: `time`, `altitude`, `temperature`.
- El archivo `.pkl` debe mantenerse en la carpeta `models/`.

---

## Archivos grandes

El modelo entrenado pesa aproximadamente 62 MB. Se incluye link a googledrive en el repositorio para facilitar la ejecución del notebook operativo.

Los archivos CSV originales completos no se incluyen en el repositorio. Se recomienda mantener sólo archivos de ejemplo y evitar subir grandes volúmenes de datos crudos.

---

## Estado del proyecto

Estado actual:

```text
Modelo operativo funcional
```

El proyecto cuenta con:

- pipeline de procesamiento de archivos CORAL;
- modelo K-Means entrenado;
- imputador KNN exportado;
- notebook operativo limpio;
- generación automática de reportes;
- clasificación por perfil y resumen nocturno.

---

## Próximos pasos posibles

- Validar físicamente los clusters con especialistas.
- Comparar noches representativas de cada cluster.
- Analizar transiciones entre clusters.
- Evaluar estabilidad del modelo con nuevas campañas de observación.
- Convertir el pipeline en un script o paquete Python reutilizable.
- Incorporar procesamiento por lotes de múltiples archivos CORAL.

---

## Autor

Proyecto desarrollado en el marco de la Tecnicatura Superior en Ciencia de Datos e Inteligencia Artificial, en Aprendizaje Automático, dictado por Nicolás Caballero, en el Centro Politécnico Superior Malvinas Argentinas.

Autor: Alexandro Orlandi
