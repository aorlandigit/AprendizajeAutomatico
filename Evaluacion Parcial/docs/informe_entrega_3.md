# Entrega 3: Presentación del Modelo y Análisis de Resultados

## Clasificación de noches atmosféricas a partir de perfiles de temperatura obtenidos con LIDAR CORAL

---

## 1. Introducción

El presente trabajo tiene como objetivo aplicar técnicas de Aprendizaje Automático para analizar perfiles de temperatura atmosférica obtenidos mediante el sistema LIDAR CORAL, ubicado en la Estación Astronómica Río Grande.

Desde el inicio del proyecto, el problema fue planteado como la necesidad de clasificar noches de observación de acuerdo con el estado de la atmósfera. Originalmente se proponía identificar distintos tipos de noches, tales como noches tranquilas, noches con fuertes ondas de montaña, estratopausa elevada, fuertes perturbaciones en la mesosfera superior, doble estratopausa con ondas de montaña en la estratósfera y noches con estructuras complejas, denominadas informalmente como “sopa de ondas”.

Luego, a partir del intercambio con el especialista, esta clasificación fue refinada hacia un conjunto de estados físicamente más acotado: noche tranquila, ondas de montaña en la estratósfera, ondas de montaña en la mesósfera, ondas de montaña presentes en ambas regiones, estratopausa elevada y doble estratopausa.

Sin embargo, el dataset disponible no contaba con etiquetas previas que indicaran a qué tipo de estado atmosférico correspondía cada noche. Por ese motivo, el problema fue abordado como un caso de aprendizaje no supervisado. En lugar de entrenar un modelo para predecir una categoría conocida, se buscó identificar patrones dominantes en los perfiles de temperatura y luego analizar si esos patrones podían vincularse con los estados atmosféricos esperados.

El enfoque general del trabajo consistió en clasificar primero los perfiles individuales de temperatura y, posteriormente, resumir cada noche a partir de la composición de clusters presentes durante esa noche. De esta forma, el análisis no se limitó a clasificar perfiles aislados, sino que avanzó hacia el objetivo principal del proyecto: caracterizar noches completas de observación.

---

## 2. Origen de los datos

Los datos utilizados provienen del sistema CORAL, un instrumento LIDAR instalado en la Estación Astronómica Río Grande. Este sistema permite obtener perfiles verticales de temperatura atmosférica a partir de observaciones nocturnas.

El conjunto de datos corresponde a observaciones realizadas durante los años 2022, 2023 y 2024. Los archivos originales contenían información de temperatura en función de la altura y del tiempo de observación. Cada archivo representaba una noche de medición y contenía distintos perfiles registrados a lo largo de esa noche.

Las variables principales consideradas fueron:

- archivo de origen;
- fecha de observación;
- tiempo de medición;
- altura;
- temperatura.

En las primeras etapas del trabajo se consolidaron los archivos originales en un único dataset maestro. Luego se aplicaron distintos criterios de limpieza y recorte para obtener un conjunto de datos adecuado para el análisis.

El rango vertical finalmente utilizado fue de 15.000 a 80.000 metros. Este recorte respondió tanto a criterios exploratorios como a criterios físicos validados con el especialista. Las alturas inferiores a 15 km podían estar afectadas por contaminación de las mediciones en capas bajas, mientras que por encima de 80 km los perfiles comenzaban a perder consistencia.

---

## 3. Preprocesamiento de los datos

Antes de aplicar el modelo de Aprendizaje Automático fue necesario realizar un proceso de limpieza y preparación del dataset.

En primer lugar, se descartaron los perfiles completamente nulos, ya que no representaban observaciones atmosféricas válidas. También se analizaron los valores iguales a cero dentro de perfiles que sí contenían información. Estos valores fueron tratados como datos faltantes cuando no tenían sentido físico dentro del perfil térmico.

Posteriormente se aplicó el recorte vertical entre 15.000 y 80.000 metros. Este paso redujo el dataset a la zona atmosférica de interés y permitió trabajar con perfiles más consistentes.

Luego se analizaron los valores faltantes resultantes. Como parte del trabajo se generaron distintos datasets para comparar estrategias de tratamiento de datos faltantes:

| Dataset | Criterio aplicado | Propósito |
|---|---|---|
| Dataset A | Solo perfiles completos | Utilizar una base sin imputación como control |
| Dataset B | Imputación por mediana vertical | Evaluar una estrategia simple de imputación |
| Dataset C | Imputación mediante KNN | Mantener la estructura de los perfiles considerando similitud entre observaciones |

El Dataset C, generado mediante imputación KNN, fue utilizado como dataset principal para el clustering. De todos modos, el Dataset A se mantuvo como referencia de control para verificar que los patrones observados no dependieran exclusivamente del método de imputación.

El dataset final utilizado para el análisis incluyó 11.041 perfiles válidos, cada uno con información de temperatura en aproximadamente 650 niveles de altura dentro del rango 15–80 km.

---

## 4. Análisis exploratorio de datos

El análisis exploratorio permitió comprender la estructura del dataset antes de aplicar el modelo. Se analizaron la cantidad de perfiles por noche, la distribución temporal de las observaciones, la presencia de valores faltantes, la variabilidad de los perfiles y la estructura térmica general.

### 4.1 Cantidad de perfiles por noche

El dataset final incluyó 597 noches válidas. La cantidad de perfiles disponibles por noche resultó variable. Algunas noches contaban con muy pocos perfiles, mientras que otras presentaban una mayor cantidad de observaciones.

Las estadísticas principales fueron:

| Estadístico | Perfiles por noche |
|---|---:|
| Cantidad de noches | 597 |
| Promedio | 18,49 |
| Desvío estándar | 14,92 |
| Mínimo | 1 |
| Primer cuartil | 8 |
| Mediana | 15 |
| Tercer cuartil | 24 |
| Máximo | 87 |

Esta variabilidad resulta importante porque muestra que no todas las noches tienen el mismo nivel de representatividad. Por ese motivo, para clasificar noches completas no alcanza con observar un único perfil, sino que es necesario resumir la composición de perfiles presentes durante cada noche.

> **Insertar gráfico:** distribución de cantidad de perfiles por noche.

### 4.2 Distribución temporal de las observaciones

También se analizó la distribución mensual de perfiles. Se observó que la cantidad de observaciones no era uniforme a lo largo del año. En general, los meses de otoño e invierno presentaron mayor cantidad de perfiles, mientras que en verano la disponibilidad fue menor.

Esta característica debía tenerse en cuenta durante el análisis, ya que una mayor presencia de datos en determinados meses podía influir en la interpretación de los resultados. Por ese motivo, además de analizar la cantidad absoluta de perfiles por cluster, se estudió la distribución relativa por mes y por estación.

> **Insertar gráfico:** cantidad promedio de perfiles por mes.

### 4.3 Valores faltantes e imputación

Luego del recorte vertical se identificaron perfiles con valores faltantes. Para no descartar una cantidad importante de información, se evaluaron distintas estrategias de imputación.

La comparación entre imputación por mediana e imputación KNN mostró que ambas estrategias mantenían la estructura general de los perfiles, aunque la imputación KNN conservaba mejor la variabilidad térmica por altura. Por este motivo, el Dataset C fue adoptado como dataset principal para el modelo.

> **Insertar gráfico:** comparación de perfiles medios y variabilidad entre Dataset A, B y C.

### 4.4 Variabilidad intra-noche e inter-noche

Un aspecto relevante del análisis fue comparar la variabilidad dentro de una misma noche con la variabilidad entre noches. Este análisis permitió reforzar la idea de que la noche debía ser considerada como unidad final de análisis.

Si bien cada perfil individual aporta información, el objetivo del proyecto no es únicamente clasificar perfiles aislados, sino caracterizar el estado atmosférico de una noche completa. Por eso, el trabajo avanzó desde una clasificación de perfiles hacia una agregación nocturna de los resultados.

---

## 5. Conclusiones del análisis exploratorio

El análisis exploratorio permitió establecer varios criterios importantes para el desarrollo del modelo:

1. Los perfiles completamente nulos debían eliminarse, ya que no representaban mediciones válidas.
2. El rango vertical más adecuado para el análisis era 15–80 km.
3. La cantidad de perfiles por noche era variable, por lo que resultaba necesario resumir cada noche mediante indicadores agregados.
4. Los valores faltantes debían tratarse cuidadosamente para no perder información útil.
5. El dataset presentaba una estructura estacional clara.
6. Los perfiles térmicos mostraban diferencias suficientes como para justificar la aplicación de técnicas de clustering.
7. La clasificación final debía interpretarse a escala nocturna, no solamente a escala de perfil individual.

Estas conclusiones guiaron la etapa posterior de modelado.

---

## 6. Modelo de Aprendizaje Automático

Dado que el dataset no contaba con etiquetas previas, el modelo fue desarrollado bajo un enfoque de aprendizaje no supervisado. El algoritmo principal utilizado fue K-Means.

K-Means permite agrupar observaciones en función de su similitud. En este caso, cada observación corresponde a un perfil vertical de temperatura. El algoritmo busca formar grupos de perfiles térmicos similares entre sí y diferentes respecto de los demás grupos.

El flujo general del modelo fue el siguiente:

1. Selección de perfiles válidos.
2. Recorte vertical entre 15.000 y 80.000 metros.
3. Tratamiento de valores faltantes.
4. Construcción del Dataset C mediante imputación KNN.
5. Aplicación del algoritmo K-Means sobre los perfiles de temperatura.
6. Evaluación de distintos valores de k.
7. Selección del modelo con k = 4.
8. Asignación de un cluster a cada perfil.
9. Agregación de los perfiles por noche.
10. Clasificación de cada noche según su composición de clusters.

El modelo fue evaluado para distintos valores de k, principalmente entre k = 2 y k = 10. La selección final de k = 4 se basó en una combinación de criterios cuantitativos e interpretativos. No se eligió únicamente el valor con mejor métrica numérica, sino aquel que ofrecía un equilibrio razonable entre separación de grupos, estabilidad del modelo e interpretación física de los resultados.

---

## 7. Métricas de evaluación del modelo

La consigna de la entrega menciona métricas como precisión, recall y F1-score. Sin embargo, estas métricas corresponden a problemas supervisados, donde existe una etiqueta real contra la cual comparar las predicciones del modelo.

En este proyecto no se disponía de una clasificación previa de cada perfil o noche. Por lo tanto, no fue posible calcular accuracy, precision, recall o F1-score en sentido estricto. En su lugar, se utilizaron métricas internas de clustering y criterios de interpretación física.

Las métricas utilizadas fueron:

| Métrica | Interpretación |
|---|---|
| Inercia | Mide la compactación interna de los clusters. Valores menores indican grupos más compactos. |
| Silhouette score | Evalúa qué tan bien separado está cada perfil de otros clusters. |
| Calinski-Harabasz | Mide la relación entre separación entre clusters y compactación interna. |
| Davies-Bouldin | Evalúa la similitud entre clusters. Valores menores indican mejor separación. |
| Distribución estacional | Permite analizar si los clusters tienen coherencia con la evolución anual de la atmósfera. |
| Entropía nocturna | Mide qué tan mezclada se encuentra una noche en términos de clusters. |
| Porcentaje dominante | Indica si una noche está claramente asociada a un cluster o si presenta mezcla de estados. |
| Matriz de transición | Permite analizar cambios de estado dentro de una misma noche o entre observaciones consecutivas. |

El uso combinado de estas métricas permitió evaluar el modelo desde dos perspectivas: una matemática, asociada a la calidad del clustering, y otra física, relacionada con la interpretación atmosférica de los grupos encontrados.

---

## 8. Resultados del clustering

El modelo con k = 4 identificó cuatro clusters principales de perfiles térmicos. La distribución general fue la siguiente:

| Cluster | Cantidad de perfiles | Porcentaje |
|---|---:|---:|
| C0 | 1.752 | 15,87 % |
| C1 | 2.741 | 24,83 % |
| C2 | 3.776 | 34,20 % |
| C3 | 2.772 | 25,11 % |

Estos resultados muestran que los cuatro clusters tienen una presencia relevante dentro del dataset. Ninguno de ellos representa un grupo marginal o despreciable.

> **Insertar gráfico:** distribución general de perfiles por cluster.

### 8.1 Interpretación estacional de los clusters

Al analizar la distribución de clusters por estación del año, se observó una estructura muy marcada:

| Estación | C0 | C1 | C2 | C3 |
|---|---:|---:|---:|---:|
| Verano | 0,0 % | 0,1 % | 99,2 % | 0,7 % |
| Otoño | 4,7 % | 34,0 % | 21,1 % | 40,2 % |
| Invierno | 38,0 % | 18,7 % | 5,6 % | 37,7 % |
| Primavera | 10,9 % | 36,9 % | 50,1 % | 2,2 % |

El Cluster 2 aparece claramente asociado al verano, donde concentra casi la totalidad de los perfiles. Esto sugiere que representa un régimen térmico estival muy definido.

El Cluster 1 aparece principalmente en primavera y otoño, por lo que puede interpretarse como un régimen de transición entre condiciones estivales e invernales.

Los Clusters 0 y 3 se concentran principalmente en invierno. Esto indica que el modelo no identifica un único estado invernal, sino al menos dos configuraciones térmicas diferentes durante esa estación.

> **Insertar gráfico:** distribución mensual o estacional de clusters.

### 8.2 Interpretación preliminar de cada cluster

A partir de la distribución estacional y de la forma de los perfiles representativos, se propone la siguiente interpretación preliminar:

| Cluster | Comportamiento observado | Interpretación preliminar |
|---|---|---|
| C0 | Predomina en invierno junto con C3 | Estado térmico invernal 1 |
| C1 | Frecuente en primavera y otoño | Estado de transición estacional |
| C2 | Predomina casi exclusivamente en verano | Estado térmico estival |
| C3 | Predomina en invierno junto con C0 | Estado térmico invernal 2 |

Esta interpretación debe considerarse preliminar. Los clusters identificados no deben entenderse automáticamente como categorías físicas cerradas, sino como regímenes térmicos dominantes que requieren interpretación atmosférica posterior.

---

## 9. Clasificación de noches completas

Como el objetivo original del proyecto era clasificar noches de observación, se realizó una agregación de los resultados a escala nocturna.

Para cada noche se calcularon los siguientes indicadores:

- cantidad de perfiles asignados a C0;
- cantidad de perfiles asignados a C1;
- cantidad de perfiles asignados a C2;
- cantidad de perfiles asignados a C3;
- cluster dominante;
- porcentaje del cluster dominante;
- entropía nocturna;
- evolución temporal de clusters durante la noche.

El porcentaje dominante permite identificar si una noche está claramente asociada a un único estado. Por ejemplo, una noche con 90 % de perfiles en C2 puede interpretarse como una noche muy representativa de ese cluster.

La entropía permite medir el grado de mezcla interna de la noche. Una entropía baja indica que la noche está dominada por un cluster, mientras que una entropía alta indica presencia de varios clusters en proporciones más similares.

De esta manera, la clasificación nocturna no se limita a asignar una única etiqueta, sino que permite distinguir entre noches puras, noches mixtas y noches de transición.

> **Insertar gráfico:** ejemplos de noches con cluster dominante alto.

> **Insertar gráfico:** ejemplos de noches mixtas o con alta entropía.

---

## 10. Transiciones entre clusters

Además de analizar la composición general de cada noche, se estudió la evolución temporal de los clusters. Este análisis permitió observar que las transiciones entre estados no parecen ser completamente casuales.

El Cluster 2 predomina en verano y aparece también en transiciones hacia primavera y otoño. El Cluster 1 se ubica principalmente en estaciones intermedias. Los Clusters 0 y 3 aparecen como dos estados característicos del invierno.

Esta estructura sugiere que el modelo está capturando una organización temporal y estacional de la atmósfera. No se trata solamente de cuatro grupos matemáticos, sino de estados que muestran cierta coherencia con la evolución anual.

También se observó que algunos pares de clusters prácticamente no aparecen combinados en noches mixtas, lo que sugiere que representan condiciones atmosféricas claramente diferenciadas.

> **Insertar gráfico:** matriz de transición entre clusters.

---

## 11. Contraste con los estados atmosféricos propuestos

Uno de los aspectos más importantes del trabajo es comparar los clusters obtenidos con los estados atmosféricos esperados originalmente.

El proyecto partía de la intención de identificar estados como:

- noche tranquila;
- fuertes ondas de montaña;
- estratopausa elevada;
- fuertes perturbaciones en la mesosfera superior;
- doble estratopausa con ondas de montaña en la estratósfera;
- sopa de ondas.

Luego, a partir del refinamiento con el especialista, se propuso trabajar con categorías como:

- noche tranquila;
- ondas de montaña en la estratósfera;
- ondas de montaña en la mesósfera;
- ondas de montaña en ambas regiones;
- estratopausa elevada;
- doble estratopausa.

El modelo, en cambio, identificó cuatro clusters principales. Esto no implica necesariamente que cada cluster corresponda a uno de los estados físicos esperados. La razón principal es que el modelo no recibió información física explícita sobre ondas de montaña, estratopausa o doble estratopausa. Solamente agrupó perfiles de temperatura de acuerdo con su similitud.

Por lo tanto, la comparación debe realizarse con cuidado. Los clusters pueden interpretarse como regímenes térmicos dominantes, mientras que los estados físicos pueden depender de rasgos más específicos, como:

- oscilaciones verticales del perfil;
- altura de la estratopausa;
- presencia de doble máximo térmico;
- perturbaciones localizadas en la estratósfera;
- perturbaciones localizadas en la mesósfera;
- evolución temporal durante la noche;
- grado de mezcla entre clusters.

La siguiente tabla resume una posible forma de contrastar los estados esperados con los indicadores generados por el modelo:

| Estado físico esperado | Qué se esperaría observar | Indicador posible en el modelo |
|---|---|---|
| Noche tranquila | Perfil estable, poca variación temporal, sin perturbaciones marcadas | Cluster dominante alto, entropía baja |
| Ondas de montaña en estratósfera | Oscilaciones térmicas entre 15 y 50 km aproximadamente | Desviaciones verticales en la parte baja-media del perfil |
| Ondas de montaña en mesósfera | Oscilaciones térmicas entre 50 y 80 km aproximadamente | Variabilidad marcada en la parte superior del perfil |
| Ondas en estratósfera y mesósfera | Perturbaciones en gran parte del perfil vertical | Alta variabilidad vertical y posible mezcla de clusters |
| Estratopausa elevada | Máximo térmico desplazado hacia mayor altura | Centroide con máximo térmico a mayor altura |
| Doble estratopausa | Dos máximos térmicos relativos | Perfil bimodal o noches con transición entre estados |
| Sopa de ondas | Perfil complejo, altamente perturbado | Entropía alta, muchas transiciones, baja dominancia |

Esta comparación permite entender que algunos estados físicos podrían corresponder a un cluster particular, mientras que otros podrían manifestarse como una combinación de clusters o como noches con alta variabilidad interna.

Por ejemplo, una noche tranquila no necesariamente corresponde siempre al mismo cluster. Podría definirse mejor como una noche con predominio claro de un solo cluster, baja entropía y poca variación temporal. En cambio, una noche con “sopa de ondas” podría no formar un cluster propio, sino aparecer como una noche con alta mezcla de clusters y múltiples transiciones.

En este sentido, el modelo no reemplaza la clasificación física del especialista, sino que proporciona una herramienta objetiva para ordenar las observaciones y seleccionar casos de interés.

---

## 12. Validación cualitativa con noches de referencia

Como instancia adicional de validación, se propone contrastar el resultado del modelo con un conjunto de noches de referencia previamente identificadas por el proyecto o por el especialista.

El procedimiento consiste en seleccionar noches representativas de cada estado esperado y analizar cómo fueron clasificadas por el modelo. Para cada noche se puede observar:

- cluster dominante;
- porcentaje de dominancia;
- entropía;
- distribución de perfiles entre C0, C1, C2 y C3;
- evolución temporal de los clusters;
- forma de los perfiles térmicos.

Una tabla posible para esta validación sería:

| Fecha | Estado esperado | Cluster dominante | % dominante | Entropía | Interpretación |
|---|---|---:|---:|---:|---|
| A completar | Noche tranquila | A completar | A completar | A completar | A completar |
| A completar | Ondas de montaña | A completar | A completar | A completar | A completar |
| A completar | Estratopausa elevada | A completar | A completar | A completar | A completar |
| A completar | Doble estratopausa | A completar | A completar | A completar | A completar |
| A completar | Sopa de ondas | A completar | A completar | A completar | A completar |

Esta validación no permite calcular métricas supervisadas tradicionales, ya que no existe una base completa etiquetada. Sin embargo, permite realizar una evaluación cualitativa de la coherencia del modelo.

Si una noche esperada como tranquila aparece dominada por un único cluster y presenta baja entropía, el resultado sería consistente. Si una noche esperada como perturbada presenta mezcla de clusters, transiciones o alta entropía, el modelo estaría capturando parte de esa complejidad.

Esta etapa resulta especialmente útil para vincular los resultados matemáticos del clustering con la interpretación física del problema.

---

## 13. Discusión

Los resultados obtenidos muestran que el modelo fue capaz de identificar cuatro regímenes térmicos principales en los perfiles de temperatura. La distribución de estos clusters presenta una clara estructura estacional, lo que sugiere que el modelo capturó información atmosférica relevante.

El Cluster 2 aparece asociado casi exclusivamente al verano, mientras que el Cluster 1 se vincula con estaciones de transición. Los Clusters 0 y 3 se concentran en invierno, lo que indica que el modelo distingue más de un tipo de configuración térmica invernal.

Este resultado es relevante porque varios de los estados físicos de interés, como ondas de montaña, estratopausa elevada o doble estratopausa, podrían manifestarse con mayor claridad en condiciones invernales o de transición. Sin embargo, no es posible afirmar de manera automática que un cluster representa directamente uno de esos fenómenos.

Una limitación importante del trabajo es la ausencia de etiquetas físicas previas. Al no contar con una clasificación manual de las noches, el modelo no puede ser evaluado con métricas supervisadas. Por eso, la validación se apoya en métricas internas de clustering, análisis estacional, inspección visual de perfiles y contraste con noches de referencia.

Otra limitación es que K-Means agrupa perfiles en función de distancia global. Esto significa que puede capturar diferencias generales en la forma del perfil, pero no necesariamente identificar fenómenos localizados, como ondas en una región específica de la atmósfera. Para detectar con mayor precisión ondas de montaña, estratopausa elevada o doble estratopausa, sería conveniente incorporar variables derivadas, como altura de la estratopausa, amplitud de perturbaciones o indicadores de variabilidad por capas.

A pesar de estas limitaciones, el modelo representa un avance importante. Permite organizar un conjunto grande de observaciones, identificar patrones térmicos dominantes y seleccionar noches representativas para análisis físico posterior.

---

## 14. Conclusiones finales

El trabajo permitió desarrollar un modelo de aprendizaje no supervisado para analizar perfiles de temperatura atmosférica obtenidos mediante el sistema LIDAR CORAL.

A partir del preprocesamiento de los datos, se construyó un dataset limpio y consistente dentro del rango 15–80 km. Luego se aplicó K-Means para identificar grupos de perfiles térmicos similares. El modelo con k = 4 permitió reconocer cuatro clusters principales, con una distribución estacional claramente diferenciada.

El análisis mostró que:

- C2 representa un régimen térmico fuertemente asociado al verano.
- C1 aparece principalmente en estaciones de transición.
- C0 y C3 representan dos configuraciones térmicas asociadas al invierno.
- La clasificación de noches completas puede realizarse a partir de la composición de clusters.
- Las noches con baja entropía y alta dominancia pueden interpretarse como noches más estables.
- Las noches con alta entropía o múltiples transiciones pueden considerarse candidatas a noches perturbadas o complejas.

El modelo no permite, por sí solo, asignar de manera definitiva etiquetas físicas como “ondas de montaña”, “estratopausa elevada” o “doble estratopausa”. Sin embargo, sí permite reducir el problema a un conjunto de estados térmicos dominantes y organizar las observaciones de manera objetiva.

De esta forma, el modelo aborda el problema formulado inicialmente al ofrecer una primera clasificación de noches atmosféricas basada en datos. El resultado final no debe interpretarse como una clasificación física cerrada, sino como una herramienta exploratoria que facilita el análisis posterior de los estados de la atmósfera.

---

## 15. Organización del repositorio GIT

La entrega final se realiza mediante un repositorio GIT que contiene los archivos necesarios para reproducir el análisis.

La estructura sugerida del repositorio es:

```text
Entrega_3/
│
├── data/
│   ├── dataset_A_casos_completos.csv
│   ├── dataset_C_knn.csv
│   ├── dataset_C_clusterizado_k2_k7_mes_estacion.csv
│   └── resumen_nocturno_clusters.csv
│
├── notebooks/
│   ├── Nb5_tratamiento_nan.ipynb
│   ├── Nb6_clustering_perfiles.ipynb
│   └── Nb7_noches_completas.ipynb
│
├── figures/
│   ├── distribucion_perfiles_por_noche.png
│   ├── perfiles_representativos_clusters.png
│   ├── distribucion_mensual_clusters.png
│   ├── matriz_transicion_clusters.png
│   └── evolucion_nocturna_ejemplos.png
│
├── docs/
│   └── informe_entrega_3.md
│
├── video/
│   └── presentacion_entrega_3.mp4
│
└── README.md
```

El repositorio incluye los datasets procesados, los notebooks utilizados para el análisis, los gráficos generados, el informe final y el video explicativo de la entrega.

---

## 16. Cierre

En síntesis, el proyecto permitió pasar de un conjunto amplio de perfiles atmosféricos individuales a una clasificación interpretable de noches de observación. El enfoque no supervisado permitió identificar patrones sin necesidad de etiquetas previas y abrió la posibilidad de contrastar esos patrones con estados atmosféricos de interés físico.

El principal aporte del modelo es ofrecer una primera organización objetiva de las noches observadas, diferenciando estados térmicos estacionales, noches estables, noches mixtas y posibles noches perturbadas. A partir de esta clasificación, el análisis puede continuar incorporando validación experta y variables físicas específicas que permitan identificar con mayor precisión fenómenos como ondas de montaña, estratopausa elevada o doble estratopausa.
