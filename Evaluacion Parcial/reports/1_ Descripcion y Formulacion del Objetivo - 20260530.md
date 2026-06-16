Aprendizaje Automático, Evaluación Parcial
Entrega 1: Descripción y Formulación del Objetivo
Consigna
En esta primera entrega, debe proporcionar una descripción detallada de su proyecto de Aprendizaje Automático.
* Formule claramente el objetivo (General y Específicos) de su trabajo, indicando qué problema específico de su interés abordará con el modelo.
* Proporcione detalles sobre el contexto en el que se encuentra el problema y por qué es relevante.
* Defina claramente si el problema se trata de una clasificación o de regresión.
* Identifique que modelos podría utilizar.
* Esta entrega se debe hacer en un archivo .PDF

Desarrollo
* Descripción del proyecto

   El proyecto se desarrolla a partir de observaciones obtenidas en la Estación Astronómica Río Grande (EARG), con el lidar (light detection and ranging) CORAL (Compact Rayleigh Autonomous Lidar). Este equipo mide perfiles de retrodispersión atmosférica mediante pulsos láser, a partir de los cuales es posible reconstruir perfiles de temperatura de la atmósfera hasta los 90 km de altitud.
   El dataset con el que vamos a estar trabajando proporciona las mediciones realizadas durante las noches de los años 2022, 2023 y 2024. Cada medición proporciona un perfil de temperatura con una resolución vertical de 100 m. Las mediciones son nocturnas ya que CORAL no posee filtros de luz diurna.
   El propósito del trabajo es clasificar las noches de observación de acuerdo con el estado de la atmósfera, que se deducirá a partir de los perfiles de temperaturas registrados cada noche y su variabilidad entre las sucesivas mediciones.
   El dataset no cuenta con una clasificación previa de los perfiles nocturnos observados, de manera que el análisis se realizará mediante la aplicación de un modelo de aprendizaje no supervisado. A partir de los resultados que se obtengan, se intentará identificar las características de los clusters y su relación con las caracterizaciones de la atmósfera propuestas.
   
* Contexto y Relevancia del proyecto
   
   La propagación de las ondas de gravedad (gravity waves, GW) es un mecanismo clave para el acoplamiento de todas las capas de la atmósfera, al transferir energía e impulso entre ellas. Las ondas de gravedad (GW) en la atmósfera media sobre los Andes meridionales han sido objeto de múltiples estudios desde 1999. En esta ubicación particular, la mayoría de los modos observados son ondas de montaña (MW), que son excitadas por fuertes corrientes de aire a través de la topografía andina1.
   La interpretación de estas observaciones se realiza mediante inspecciones y análisis manuales que requieren extensa experiencia en análisis atmosféricos. 
   El aprendizaje automático ofrece la posibilidad de explorar los datos y detectar patrones que luego puedan interpretarse desde el punto de vista físico y vincularlos con las caracterizaciones de la atmósfera.
   Según lo relevado en el proyecto y en una conversación posterior los estados posibles son los siguientes:
   
Estados atmosféricos identificados en el proyecto y caracterizaciones sugeridas por el especialista
Estados identificados en el Proyecto
Caracterización sugerida en entrevista con el especialista
Noche tranquila
Noche tranquila
Fuertes ondas de montaña
Ondas de Montaña
Estratosfera
Estratopausa elevada

Mesosfera
Fuertes perturbaciones en Mesosfera superior

Ambas
Doble estratopausa con ondas de montaña en la estratósfera
Estratopausa elevada
Sopa de ondas
Doble Estratopausa
   
* Objetivo General
   
   Desarrollar un modelo de aprendizaje automático no supervisado que permita identificar agrupamientos en las observaciones atmosféricas obtenidas mediante el lidar CORAL en Río Grande, para caracterizar los distintos estados de la atmósfera.
   
* Objetivos Específicos
   
o Analizar la estructura y calidad del dataset disponible.
o Realizar tareas de limpieza y preprocesamiento de las observaciones atmosféricas.
o Definir la unidad de análisis del problema.
o Aplicar agrupación de datos sin etiquetas (Clustering).
o Determinar una cantidad apropiada de clusters mediante análisis exploratorio y métricas de evaluación. 
o Analizar e interpretar físicamente los agrupamientos obtenidos, y vincularlos con las caracterizaciones propuestas por el proyecto Southwave.
   
* Tipo de Problema
   
   Este análisis plantea la necesidad de usar métodos de aprendizaje no supervisado, ya que los datos no poseen etiquetas que indiquen el tipo de atmósfera de cada observación. Una vez identificados los agrupamientos del conjunto de datos habrá que intentar vincularlos a la caracterización sugerida por el proyecto.
   
* Modelos potenciales
   
   Por tratarse de aprendizaje no supervisado el modelo principal al que recurriremos es K-Means. Por lo que vimos en clase también podríamos utilizar MiniBatchKMeans, que sería más eficiente, posiblemente a costa de algo de calidad de los resultados, o Affinity Propagation, pero que requeriría muchos recursos.
   Debido a la alta dimensionalidad del dataset, podría evaluarse el uso de técnicas de reducción de dimensionalidad, como PCA, en caso de ser necesario.
1 Proyecto de Prácticas Profesionalizantes para el Centro Politécnico de Nivel Superior ‘Islas Malvinas’, Proyecto Southwave, Traducido y adaptado por José Luis Hormaechea con la asistencia de https://www.deepl.com/es/translator,abril 2026.
