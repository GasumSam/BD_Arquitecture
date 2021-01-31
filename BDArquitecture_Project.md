# Arquitectura de datos para monitorización de cuenca hidrográfica

## Brainstorm

El cambio climático es una de las principales amenazas a las que se enfrenta la humanidad. Entre sus consecuencias se encuentra la ampliación de los periodos de sequía, intercalados con episodios de precipitaciones torrenciales. Esto se traduce en una cada vez más acuciante escasez de agua, unida a la incapacidad de los actuales sistemas hídricos por aprovechar este recurso natural.

El agua es sinónimo de vida, garantiza la producción de alimentos y el correcto equilibrio medioambiental. Espacios naturales y agricultores, especialmente los minifundistas, se ven paulatinamente afectados por la escasez de agua, de ahí que haya pasado de ser un bien natural a un bien escaso que cotiza en bolsa (https://elpais.com/clima-y-medio-ambiente/2020-12-08/que-significa-que-el-agua-empiece-a-cotizar-en-el-mercado-de-futuros-de-wall-street.html). ¿Y si el big data pudiese determinar cuándo es necesario abrir o cerrar las compuertas de un embalse? ¿Y si el Machine Learning pudiese determinar las consecuencias de la producción de alimentos en la cuenca en función de la naturaleza del ciclo del agua?, etc.

Proyectamos el desarrollo de un sistema de control y análisis, a través de los datos, de las principales cuencas hidrográficas, formada por las tierras que vierten sus aguas al mar a través de un río y sus afluentes. Para ello, el objetivo es:

* Monitorizar el curso de una cuenta hidrográfica con el objetivo de determinar los flujos naturales del agua, sus principales amenazas y cómo estas afectan a los ecosistemas que se desarrollan a su paso.

* Monitorizar la puvliometría de las principales sub cuentas, así como la naturaleza del suelo, a fin de cruzar los datos con el caudal en distintas épocas del año.

* Acceder a la predicción meteorológica para proyectar, sobre los datos ya monitorizados, posibles comportamientos de la cuenta; como inundaciones, sequías, evaporación de las cuentas o erosión del terreno. 

# Diseño del DAaaS

## Definición de la estrategia del DAaaS

Un dashboard dinámico que ofrezca los datos, a tiempo real, de las tres principales fuentes de información recogidas para el proyecto: la naturaleza del cauce del río, las condiciones y las previsiones meteorológicas. Además, en base al procesamiento de dichos datos, un cuarto grupo proyectará, a través de machine learning, el comportamiento de la cuenca y la disponibilidad de agua a futuro.

Los datos recogidos en el primer grupo (naturaleza del cauce) permitirían visualizar las alteraciones en el caudal con gran precisión, lo que se convertiría en una información de gran valor a la hora de monitorizar vertidos o posibles desvios no autorizados.

La empresa Libelium (www.libelium.com) será la encargada de suministrar, instalar y mantener el servicio de sensores que se colocarán en diferentes puntos de los cauces de la cuenca (datos de ubicación por coordenadas), tecnología autónoma alimentada por energía solar y conectividad 4G. Para la elección del tipo de sensor que aportará los datos sobre el flujo del agua (https://www.libelium.com/iot-solutions/smart-water/) necesitaremos la aportación de una consultoría química que determine qué valores son necesarios para la correcta visualización de los valores químicos (contaminación, por ejemplo). En el caso de los valores físicos (caudal, temperatura, turbiedad, etc.) será una consultoría medioambiental la que determine los tipos de datos más importantes. Los datos sobre las condiciones medioambientales se recogerán con sensores de la serie 'Smart Agriculture' de Libelium (https://www.libelium.com/iot-solutions/smart-agriculture/) y contarán, para su diseño, con el informe de la consultoría medioambiental. 

La previsión meteorológica se obtendrá desde el servicio AEMET Open Data (https://opendata.aemet.es/centrodedescargas/AEMETApi?), API de la Agencia Estatal de Meteorología (AEMET).

## Arquitectura DAaaS

Desplegaremos la arquitectura del proyecto en Google Cloud Platform (GCP) con la siguiente estructura:

* Cluster de Dataproc con el componente de HBase (Zookeeper). ()

* Dos máquinas Kafka virtualizadas, conectadas a través de Spark con HBase.

* Dataset en Bucket central para datos recogidos por ambas máquinas virtuales Kafka (agua y ambientales).

* Cloud Function de conexión API de AEMET con un segundo dataset en Bucket central con predicción meteorológica.

* Tarea Dataproc a través de MapReduce para generar un dataset enriquecido que proyecte a futuro las condiciones ambientales y del agua.

* Conexión a través del *Driver ODBC HBase* de HBase con Tableau.

* Dashboard Tableau a demanda del cliente, conectado con Spark (Streaming) y HBase (Batch).

------

*Nota*: La idea de utilizar el componente Spark es porque nos permitiría desplegar ad-hoc otras herramientas en función de las necesidades de desarrollo del proyecto. Esto se traduce en una mayor versatilidad y escalabilidad de la estructura.

*Nota 2*: Durante el diseño del presente ejercicio, comprobamos que Google Cloud ofrece el servicio *BigTable*, una alternativa a tener en cuenta para evolucionar el presente diseño arquitectónico en base a un caudal de datos en streaming que necesitamos acumular (integrado con Spark, por ejemplo). Es una muestra de la evolución de las diferentes herramientas disponibles.

## DAaaS Operating Model Design and Rollout

### Diseño de la infraestructura

El diseño del presente proyecto se determinará por informes medioambientales previos y sobre el estudio de las características físicas y orográficas de la cuenca hidrigráfica (río principal y afluentes). Esta información nos permitiría *elaborar un mapa con los puntos de monitorización óptimos*, de manera que se cumplan los siguientes requisitos:

* *Cobertura ponderada de la cuenca hidrográfica*. Los puntos de obtención de información deben permitir la monitorización de todo el territorio en función de los afluentes, zonas potenciales de erosión hídrica (zonas de cultivo próxima o núcleos poblacionales) o especial vulnerabilidad a los cambios climáticos.

* *Número total de puntos de monitorización*. Cada punto de sondeo supone el despliegue de dos sensores, uno para la naturaleza del agua y otro ambiental. Este aspecto es fundamental para el presupuesto final del proyecto.

* *Conectividad de los puntos de monitorización*. En función de las zonas recomendadas de instalación se tendrán en cuenta aspectos como la conectividad de los datos o el funcionamiento (autosuficiente mediante energía solar).

* *Mantenimiento*. Será necesario contemplar los posibles fallos técnicos en los puntos de monitorización.

### Diseño del Data Lake

*Nota:* El funcionamiento del flujo de datos estará determinado por el diseño de la infraestructura y los requerimientos del cliente (periodicidad, por ejemplo. No siendo lo mismo que se obtengan datos de un número determinado de sensores una vez al día que cada hora en punto). Aún así, simulamos el diseño en base a un sistema de monitorización a tiempo real, al margen de los recursos que se necesiten:

1. Cada hora, cada sensor (PUB) envía un log con su información (event) a HBase (SUB) a través de Spark.

2. De forma simultánea, con registro horario similar, Cloud Function ejecuta un script por el que se genera un dataset, desde la API de AEMET, con la proyección meteorológica por horas para la zona correspondiente a cada sensor. 

3. Diariamente, a través de MapReduce, ejecutamos la tarea de generar un nuevo dataset enriquecido en el que se proyecten las condiciones por horas (durante los siguientes siete días). Una función que nos permitiría desplegar un sistema de Machine Learning por la cual se ajusten los posibles fallos del procesamiento de datos.

------------

A través de Cloud Function podríamos lanzar un script, sobre cada uno de los valores más importantes, determinando los valores medios y que lance una alerta en caso de que alguno de los logs sobrepasen dichos registros (Alteración en los valores, como si de un análisis médico se tratara).

## Desarrollo de plataforma DAaaS

El presente proyecto se basa en un caso clásico de datos en streaming con Kafka, respondiendo a la lectura a tiempo real de los sensores (PUB). Su complejidad puede residir en que dichos datos se acumulen (HBase) y estén disponibles para el despliegue, a posteriori, de una utilidad Machine Learning que permita ajustar las proyecciones a futuro y adelantarse a las posibles alteraciones medioambientales. 

A tal efecto, para el desarrollo se ha priorizado en la sencillez (monitorización), añadiendo las funciones de almacenamiento (HBase) y procesamiento (Spark para streaming y MapReduce para batch) para un dataset enriquecido que de soporte al futuro desarrollo Machine Learning.

1. *Kafka* recoge los datos en streaming, *Spark* los procesa y almacena en un dataset en HBase.
2. *Cloud Function* ejecuta un script para crear un nuevo dataset con los datos extraidos de la API de AEMET.
3. Lanzamos un job de MapReduce, desde Dataproc, para generar un dataset enriquecido con valores a futuro.
4. Conectamos Spark (Streaming) con Tableau para dashboard en streaming.
5. Conectamos HBase (*Driver ODBC HBase*) con Tableau para dashboard datos en batch.

# Diagrama de la arquitectura

![Arquitectura de datos para monitorización de cuenca hidrográfica](/BDArquitecture_Project_.png)

[Enlace a diagrama en Google Drawings](https://docs.google.com/drawings/d/1wQxIFXGDK3snHXSAF7KA6dBUVqSDmFxiT8tY4RMxU8Y/edit?usp=sharing)


