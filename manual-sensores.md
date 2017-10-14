# Informe técnico sensores Acueducto progresar

Antecedentes

El Acueducto progresar había desarrollado junto con la universidad de los Andes dispositivos de toma de datos de sensores de conductividad y temperatura,  por diferentes motivos estos sensores  no habían podido trabajar correctamente, por ello el acueducto estableció los requerimientos técnicos:

* Que la toma de datos fuera fiable
* Que los datos quedarán registrados y fueron susceptibles de análisis
* Que los datos fueran visualizados por medio de una interfaz basada en un mapa
* dado que el sensor  debe sumergirse en el agua,  necesita un recipiente hermético.  se  requiere reducir al mínimo la necesidad de abrir este recipiente

[Image: image01.png]
Estos sensores estaban construidos de acuerdo a las siguientes características de diseño

* Tienen una tarjeta principal la cual tiene un circuito  de medición de conductividad
* Esta tarjeta tiene dos conectores principales
    * Uno para el arduino mini pro qué es la tarjeta controladora principal
    * Otro para la tarjeta zona 808 qué es la tarjeta que permite una comunicación a través de la red gsm gprs del dispositivo

## Comunicaciones

Uno de los grandes retos de este dispositivo es la transmisión y registro de los datos obtenidos,  en el proyecto inicial  liderado por la universidad de los Andes, estos datos se habían pensado transmitir a través de mensajes de texto SMS,   luego se decidió enviar estos datos a través de una petición WEB sobre la red gprs,  con este fin se desarrolló una interfaz que escribía en una hoja de cálculo de Google la misma para todos los sensores.

Problema de la memoria

El principal problema técnico que surgió en este diseño fue la memoria limitada de de la placa arduino Pro mini.

Por esta razón se generaba mucha inestabilidad en el dispositivo. Finalmente en esta fase se logró una comunicación de una vía desde el dispositivo hasta la hoja de cálculo de Google logrando así registrar los datos, trabando al borde de la memoria permitida.

[Image: https://quip.com/-/blob/dGbAAA4Baqc/A1qyAoJqcAGKyrTUdoUe7g]
Para este proósito se trabajó con la librería de adafruit,  esta fue especialmente diseñada para este dispositivo.

Dado que es muy común el problema de la inestabilidad por falta de memoria,  investigamos sobre los proyectos que han atacado este problema,   probando varias librerías buscando un desempeño bueno en la transmisión de datos a través de una petición WEB, todo esto, con la limitada memoria que tiene el arduino Pro mini.

TinyGSM y FONA 808

Luego de probar varias librerías  decidimos usar la librería tinygsm ([_http://www.arduinolibraries.info/libraries/tiny-gsm_](http://www.arduinolibraries.info/libraries/tiny-gsm)).

[Image: https://quip.com/-/blob/dGbAAA4Baqc/0S4X6vSCwNI5zN98F-W_uA]
Sin embargo su implementación No fue fácil para el chip que tenemos que es el FONA808.   Trabajamos junto con el desarrollador cambiando algunas líneas de código para que trabajara satisfactoriamente con  nuestro Hardware.

Esta librería fue especialmente diseñada para ahorrar la memoria.

Localización de la toma de datos

Inicialmente se pensó tomar la  localización de la medición a través  del dispositivo FONA, sin embargo la electrónica del fona 808 no permite tomar la posición geográfica a través de la red GPS, esto se hizo en un principio a través de una triangulación usando la red gprs,  esta triangulación no podía obtener una precisión por encima de los 1000 metros de incertidumbre.

Por esta razón una de las primeras decisiones que tomamos fue retirar la toma de posición Geográfica a través de la red gprs,   Y dado que cada dispositivo tiene una localización determinada,  se asignó cada una de ellas fija en el mapa.

Registro de datos.

Una de las grandes necesidades surgidas del trabajo con estos sensores es la mejora en el registro y análisis de los datos,  a pesar de que la hoja de cálculo de Google trabaja satisfactoriamente,   pensamos que en este sentido se podría de mejorar la interacción con los dispositivos.

Por esta razón, como parte del proyecto también investigamos plataformas de toma de datos que ahora son conocidas como plataformas de internet de las cosas (IoT). Luego de probar el desempeño de varias plataformas,  encontramos que la que más corresponde a nuestros intereses es la plataforma thinger (thinger.io)

[Image: https://quip.com/-/blob/dGbAAA4Baqc/scrmz9SojorIStHEvazJsQ]
Esta plataforma tiene varias funcionalidades importantes para nuestro proyecto,  a saber:

* Registro de los datos independiente por cada dispositivo
* Comunicación en doble vía con cada dispositivo
    * Esto permite que en el futuro se puedan incluso controlar variables y procesos en remoto al dispositivo
* Control remoto del dispositivo a través de una app movil
* [Image: https://quip.com/-/blob/dGbAAA4Baqc/wRtAt0X0KtPUXON8pzAYzA]
* Tableros de control  personalizables de cada uno de los dispositivos
    * Las personas de progresar pueden personalizar los tableros de control sin necesidad de hacer un desarrollo de software,  esta personalización se hace de  una manera similar a Cómo se hacen reportes en una hoja de cálculo. Pero manejando datos en tiempo real

[Image: https://quip.com/-/blob/dGbAAA4Baqc/QX61kwUk7wq7oAR2qxqH0A]
Sensorica

Calibración de los sensores de conductividad

[Image: https://quip.com/-/blob/dGbAAA4Baqc/pRuY58JjESj44pEYKIcZjw]
Así que una buena parte del tiempo del desarrollo lo destinamos a la investigación y calibración de la toma de datos de conductividad,  para ello buscamos dispositivos calibrados y profesionales que realizaron esta tarea para poder comprobar los datos obtenidos por el dispositivo

El cableado de los bornes fue reemplazado y protegido con el fin de garantizar estanqueidad.

[Image: https://quip.com/-/blob/dGbAAA4Baqc/sGm9-IWFXGbb8ctxftiJUQ][Image: https://quip.com/-/blob/dGbAAA4Baqc/eTVMOAjXCF9sNxMW-oY_zA]
Después de realizar muchas tomas de datos Y compararlos con los dispositivos  calibrados  se llegó a la siguiente curva

[Image: https://quip.com/-/blob/dGbAAA4Baqc/faxV1NCUa0ADeinxECJsnA]
Concluyendo  que aunque puede tomar un dato de calibración que tiene sentido,  no es posible llegar a calibrar este dispositivo tal como están calibrados los otros con los que comparamos .

En las mediciones  de agua con determinada conductividad al hacer dos mediciones no se obtenía el mismo dato.  por eso llegamos a  esta conclusión

Calibración de los sensores de temperatura

La medición de temperatura no representa ningún problema, aunque para el rango de temperaturas que estamos trabajando y para la precisión que se requiere,  sugerimos trabajar con  un código más simple,  y con un medidor de temperatura  más sencillo.  por ejemplo el semiconductor lm35.

Sistema de encendido

Se instaló un sensor de inclinación como interruptor principal del dispositivo,  pudiendo el dispositivo ser encendido al ponerlo en posición vertical con la tapa hacia abajo.  esto permite que el dispositivo únicamente deba ser abierto para recargar la pila y no para encenderlo

Este se ubicó en la tapa del dispositivo.

Publicación del código del dispositivo

todos los códigos desarrollados para este proyecto se hicieron de uso público,  estos pueden ser los encontrados en un repositorio github:  (https://github.com/leoguti/progresar)

[Image: https://quip.com/-/blob/dGbAAA4Baqc/RW6elzLdLUM3ovuFYCCSlA]
Allí se pueden encontrar más información técnica acerca del desarrollo aparte de los códigos el si mismos.

Representación geográfica de los resultados

De acuerdo a un diseño que conjuntamente hicimos con Progresar, realizamos una interfaz basada en mapas, donde toda la información del territorio se conjuga con la información de los sensores, permitiendo interactuar con los resultados de las mediciones de manera conjunta.

[Image: https://quip.com/-/blob/dGbAAA4Baqc/Li6TdLqwcnaGxWY8NT8QNQ][Image: https://quip.com/-/blob/dGbAAA4Baqc/tTvkN0Vc1SfUJH6JEpmwsg]
La interfaz basada en el territorio conjuga todos los actores tanto de personas, como de sensores en una misma interacción. Pensamos que en el futuro podemos hacer mucho para mejorar el mapa base, siendo este un tema tan grande e importante que daría para un proyecto en si mismo.

