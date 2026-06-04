# Segunda Entrega – Descripción y Origen del Dataset

## Descripción del Dataset

El dataset con el que vamos a trabajar se construyó a partir de observaciones atmosféricas obtenidas mediante el sistema lidar CORAL (*Compact Rayleigh Autonomous Lidar*), instalado en la Estación Astronómica Río Grande (EARG), en Tierra del Fuego.

Los datos originales fueron procesados y reorganizados para obtener un formato adecuado para su análisis mediante técnicas de Aprendizaje Automático. El dataset final contiene **25.692 registros** y **723 columnas**, donde cada registro representa un perfil atmosférico individual de temperaturas en función de la altitud, tomados de una observación nocturna, en una fecha y horario en particular.

Las tres primeras columnas contienen información de identificación y temporal:

- Nombre del archivo de origen.
- Fecha de observación (obtenida del nombre del archivo original).
- Variable temporal (*time*) proveniente del archivo original.

La combinación de la fecha y la variable time es única para cada uno de los perfiles.

Las restantes **720 columnas** corresponden a temperaturas atmosféricas medidas en distintos niveles de altura de una grilla vertical común construida durante el procesamiento de los datos.

El conjunto de datos utilizado para este trabajo está compuesto por **1277 noches de observación válidas**, con un promedio de **20,12 perfiles por noche**. La resolución vertical de las mediciones es de **100 metros**, lo que permite reconstruir perfiles de temperatura hasta aproximadamente 80 km de altitud.

### Tabla 1. Resumen del dataset utilizado

| Característica | Valor |
|----------------|--------|
| Cantidad de registros | 25.692 |
| Cantidad de columnas | 723 |
| Cantidad de noches observadas | 1277 |
| Resolución vertical | 100 m |
| Altura máxima observada | ~80 km |
| Promedio de perfiles por noche | 20,12 |
| Tipo de variables principales | Numéricas continuas |
| Tipo de aprendizaje | No supervisado |

---

## Origen del Dataset

Los datos fueron obtenidos mediante el sistema lidar CORAL (Compact Rayleigh Autonomous Lidar), operado en la Estación Astronómica Río Grande (EARG).

La técnica lidar (Light Detection and Ranging) consiste en emitir pulsos láser hacia la atmósfera y medir la señal retrodispersada por las moléculas atmosféricas. A partir de estas mediciones es posible reconstruir perfiles verticales de temperatura de la atmósfera media.

El trabajo se enfoca en las observaciones correspondientes al período 2022–2024. La validación definitiva de la cobertura temporal y la limpieza final del dataset se realizarán en etapas posteriores del proyecto.

Los datos originales fueron proporcionados en formato netCDF (Network Common Data Form), un estándar ampliamente utilizado en ciencias atmosféricas para almacenar datos multidimensionales y los metadatos asociados a las observaciones.

---

## Estructura de los Archivos Originales

Cada archivo original corresponde a una noche de observación y contiene información organizada principalmente en función del tiempo y la altura.

Las dimensiones principales presentes en los archivos son:

### Tabla 2. Dimensiones principales de los archivos originales

| Dimensión | Descripción |
|------------|------------|
| time | Instantes de observación registrados durante la noche. |
| altitude | Niveles de altura utilizados para reconstruir los perfiles atmosféricos. |
| temperature | Temperatura atmosférica reconstruida. |

La combinación de time, altitude y temperature permiten representar la evolución temporal de los perfiles atmosféricos observados durante cada noche.

### Variables contenidas en los archivos originales

Además de las dimensiones mencionadas, los archivos contienen variables atmosféricas, geométricas e instrumentales asociadas a las observaciones y al procesamiento de los datos.

#### Variables presentes en los archivos originales

| Variable | Descripción |
|-----------|-------------|
| value | Señal medida por el sistema lidar. |
| channels | Canal de detección utilizado durante la adquisición. |
| time | Tiempo de observación. |
| altitude | Altitud correspondiente a cada medición. |
| station_latitude | Latitud de la estación. |
| station_longitude | Longitud de la estación. |
| station_height | Altitud de la estación. |
| time_offset | Referencia temporal utilizada durante el procesamiento. |
| altitude_offset | Referencia de altura utilizada durante el procesamiento. |
| wavelength | Longitud de onda del láser utilizado. |
| zenith_cosine | Coseno del ángulo cenital de observación. |
| z0 | Parámetro auxiliar utilizado durante el procesamiento. |
| integration_start_time | Inicio del intervalo de integración. |
| integration_end_time | Fin del intervalo de integración. |
| max_countrate | Máxima tasa de conteo registrada. |
| countrate_limit_bin | Bin límite para la tasa de conteo. |
| merge1_top_bin | Parámetro de procesamiento asociado a la primera etapa de combinación. |
| merge1_bottom_bin | Parámetro de procesamiento asociado a la primera etapa de combinación. |
| merge1_mean_diff | Diferencia media asociada a la primera etapa de combinación. |
| merge2_top_bin | Parámetro de procesamiento asociado a la segunda etapa de combinación. |
| merge2_bottom_bin | Parámetro de procesamiento asociado a la segunda etapa de combinación. |
| merge2_mean_diff | Diferencia media asociada a la segunda etapa de combinación. |
| temperature_err | Error estimado de la temperatura reconstruida. |

Para el desarrollo de este trabajo se utilizarán principalmente las variables **temperature**, **time** y **altitude**, ya que son las que permiten reconstruir los perfiles atmosféricos que constituyen la base del análisis.

Las restantes variables contienen información auxiliar relacionada con la ubicación de la estación, la configuración instrumental y distintos parámetros utilizados durante el procesamiento de los datos.

Adicionalmente, durante la construcción del dataset se extrajo la **fecha de observación** a partir del nombre de cada archivo original. Esta información no se encuentra explícitamente almacenada como una variable dentro de los archivos netCDF, pero fue incorporada al dataset final por su relevancia para el análisis temporal de las observaciones.

---

## Preprocesamiento Realizado

Para poder trabajar con los datos mediante técnicas de Aprendizaje Automático fue necesario realizar algunas tareas de preprocesamiento.

En primer lugar, se procesaron los archivos netCDF para extraer las variables de interés. Posteriormente se construyó una grilla vertical común y se reorganizaron los perfiles de temperatura en formato tabular.

La transformación realizada consistió en representar:

- cada perfil atmosférico como una fila de la matriz de datos;
- cada nivel de altura como una columna independiente;
- cada valor de la matriz como la temperatura observada para una determinada altura.

También se incorporó el nombre del archivo, que incluye la fecha de observación, y se creó una columna con la fecha de observación a partir del nombre de cada archivo original. Esta variable permitió identificar y agrupar los perfiles correspondientes a una misma noche de medición.

Los datos originales estaban compuestos por **1702 archivos de observación**, cada uno correspondiente a una noche de medición. Durante la etapa de exploración y limpieza se detectaron archivos cuyos perfiles contenían únicamente valores nulos o temperaturas iguales a cero en todos los niveles de altura. Debido a que estos registros no aportaban información útil para el análisis, fueron excluidos del dataset.

Como resultado, el conjunto de datos quedó conformado por **1277 noches válidas**, a partir de las cuales se obtuvieron los **25.692 perfiles atmosféricos** utilizados en este trabajo.

Durante el análisis exploratorio también se observó la presencia de valores faltantes en algunos niveles de altura, particularmente en las capas más bajas de la atmósfera observada. Esta situación es habitual en este tipo de mediciones lidar y será considerada durante las etapas posteriores del proyecto.

### Tabla 4. Resumen del proceso de construcción del dataset

| Etapa | Cantidad |
|---------|---------:|
| Archivos originales | 1702 |
| Noches válidas utilizadas | 1277 |
| Perfiles atmosféricos finales | 25.692 |
| Promedio de perfiles por noche | 20,12 |
| Variables finales | 723 |

---

## Consideraciones para el modelado

El dataset no posee etiquetas que indiquen el estado atmosférico asociado a cada observación. Por este motivo, el problema se abordará mediante técnicas de Aprendizaje Automático no supervisado.

Un análisis preliminar de la variabilidad de los perfiles mostró que las diferencias observadas entre noches son, en general, mayores que las variaciones registradas dentro de una misma noche de observación. Este resultado sugiere que la noche puede constituir una unidad de análisis adecuada para las etapas posteriores del proyecto y para la aplicación de técnicas de agrupamiento (clustering) destinadas a identificar distintos estados de la atmósfera.