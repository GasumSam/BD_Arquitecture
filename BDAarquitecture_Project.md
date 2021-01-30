# BRAINSTORM

El cambio climático es una de las principales amenazas a las que se enfrenta la humanidad. Entre sus consecuencias se encuentra la ampliación de los periodos de sequía, intercalados con episodios de precipitaciones torrenciales. Esto se traduce en una cada vez más acuciante escasez de agua, unida a la incapacidad de los actuales sistemas hídricos por aprovechar este recurso natural.

El agua es sinónimo de vida, garantiza la producción de alimentos y el correcto equilibrio medioambiental. Espacios naturales y agricultores, especialmente los minifundistas, se ven paulatinamente afectados por la escasez de agua, de ahí que haya pasado de ser un bien natural a un bien escaso que cotiza en bolsa (https://elpais.com/clima-y-medio-ambiente/2020-12-08/que-significa-que-el-agua-empiece-a-cotizar-en-el-mercado-de-futuros-de-wall-street.html)

Por este motivo, proyectamos el desarrollo de un sistema de control y análisis, a través de los datos, de las principales cuencas hidrográficas, formada por las tierras que vierten sus aguas al mar a través de un río y sus afluentes. Para ello, el objetivo es:

* Monitorizar el curso de una cuenta hidrográfica con el objetivo de determinar los flujos naturales del agua, sus principales amenazas y cómo estas afectan a los ecosistemas que se desarrollan a su paso.

* Monitorizar la puvliometría de las principales sub cuentas, así como la naturaleza del suelo, a fin de cruzar los datos con el caudal en distintas épocas del año.

* Acceder a la predicción meteorológica para proyectar, sobre los datos ya monitorizados, posibles comportamientos de la cuenta; como inundaciones, sequías, evaporación de las cuentas o erosión del terreno. 

# Diseño del DAaaS

## Definición de la estrategia del DAaaS

Un dashboard dinámico que ofrezca los datos, a tiempo real, de las tres principales fuentes de información recogidas para el proyecto: la naturaleza del cauce del río, las condiciones y las previsiones meteorológicas. Además, en base al procesamiento de dichos datos, un cuarto grupo proyectará, a través de machine learning, el comportamiento de la cuenca y la disponibilidad de agua a futuro.

Los datos recogidos en el primer grupo (naturaleza del cauce) permitirían visualizar las alteraciones en el caudal con gran precisión, lo que se convertiría en una información de gran valor a la hora de monitorizar vertidos o posibles desvios no autorizados.

La empresa Libelium (www.libelium) será la encargada de suministrar, instalar y mantener el servicio de sensores que se colocarán en diferentes puntos de los cauces de la cuenca. Para la elección del tipo de sensor que aportará los datos sobre el flujo del agua (https://www.libelium.com/iot-solutions/smart-water/) necesitaremos la aportación de una consultoría química que determine qué valores son necesarios para la correcta visualización de los valores químicos (contaminación, por ejemplo). En el caso de los valores físicos (caudal, temperatura, turbiedad, etc.) será una consultoría medioambiental la que determine los tipos de datos más importantes.

Los datos sobre las condiciones medioambientales se recogerán con sensores de la serie 'Smart Agriculture' de Libelium (https://www.libelium.com/iot-solutions/smart-agriculture/) y contarán, para su diseño, con el informe de la consultoría medioambiental.

La previsión meteorológica se obtendrá desde el servicio AEMET Open Data (https://opendata.aemet.es/centrodedescargas/AEMETApi?), API de la Agencia Estatal de Meteorología.

## Arquitectura DAaaS





## DAaaS Operating Model Design and Rollout





## Desarrollo de plataforma DAaaS
