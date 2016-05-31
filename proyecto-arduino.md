<meta http-equiv="content-type" content="text/html;charset=UTF-8">

#Proyecto ArduinoNetRC

##Idea general

La idea consiste en construir un coche basado en Arduino, que se pueda controlar de forma
remota a través de internet mediante una aplicación cliente.

El objetivo es que el coche se pueda controlar desde cualquier parte del mundo,
pudiendo estar el usuario y el coche en ciudades distintas.

###Funcionalidades del Vehículo

El vehículo contaría con una cámara, que mandaría imágenes en directo a la
aplicación cliente; y sensores de movimiento, que mejorarían el control del vehículo.

Además, contaría con un motor auxiliar, control de revoluciones del motor principal,
y opción de turbo; todos controlables por el usuario desde la aplicación cliente.

Opcionalmente, se le podrían añadir sensores de luminosidad conectados a unos faros,
que encendiesen los faros al pasar por una habitación poco iluminada.

##Desarrollo del Proyecto

El desarrollo del proyecto se realizara de forma incremental, empezando por un coche
básico de un solo motor y cámara, y realizando ampliaciones y mejoras hasta llegar
al proyecto final.

Para realizar el desarrollo, el equipo del proyecto se dividiría en 3 subequipos:

 * Construcción del Vehículo: Formado por personal de las áreas de Mecánica y Electrónica,
se encargarían de la parte mecánica del coche

 * Control y Comunicaciones: Formado por personal de Informática y Electrónica,
se encargaría de programar el control del coche, y establecer la conexión con la aplicación cliente.

 * Desarrollo de la Aplicación: Formado por personal de Informática,
preferiblemente del área de ingeniería del software, se encargaría de programar la aplicación cliente.

Para poder realizar un desarrollo eficaz, el equipo de Control y Comunicaciones
debe estar en contacto e informar de sus decisiones de implementación con los
otros dos equipos, pues el montaje del coche esta determinado por los componentes
que se usen para su control, y el desarrollo de la aplicación depende de las
implementación de comunicaciones que se realice.

El equipo completo deberá estar formado por, al menos, 9 personas; habiendo 3 personas en cada equipo.

## Detalles de Diseño e Implementación

### Construcción del Vehículo

El vehículo será de tracción trasera, con dos motores: un motor principal, y un motor auxiliar.
Además, las ruedas delanteras contarán con un mecanismo de dirección, de forma similar a un coche real.

Los motores deberán ser de tipo Paso a Paso, para poder permitir la realización del control de revoluciones,

El motor principal aportaría potencia al vehículo, pero poca velocidad,
la cual podra ser compensada mediante la activacion del motor auxiliar.

El motor auxiliar sera un motor algo mas pequeño que el principal, pero con un
numero de revoluciones superior a este.

Este puede ser activado de forma manual por el usuario, o de forma automática,
cuando el motor principal llegue a su máximo de revoluciones.

El turbo proporcionaría un incremento brusco de velocidad, que seria instantáneo,
durando apenas un par de segundos.

Este permitiría proporcionar una velocidad superior al máximo de revoluciones de los dos motores.

Los sensores de proximidad serian 3, y estarían situados en la parte delantera
del vehículo, uno en el frontal, y uno a cada lado.

Todas estas ideas estarían sujetos a cambios por decisión del equipo de montaje

###Control y Comunicaciones

El control del Vehículo se haría a través de una placa Arduino,
que recibiría instrucciones a través de Internet de la aplicación cliente.

La imagen de la cámara usara por defecto una calidad VGA,
para que la imagen sea mas ligera y fácil de enviar.

Las instrucciones se recibirían codificadas mediante un código numérico,
siendo por ejemplo el control de movimientos de esta forma:

```
1000 – Avanzar
0001 – Retroceder
0100 – Girar a la izquierda
0010 – Girar a la derecha
```

Las revoluciones del motor se indicaran mediante un código similar,
concatenando un 2 al numero de revoluciones que se desea aplicar. Por ejemplo:

```
2156 – 156 rpm
```

Existen varios códigos publicados por Internet, con licencia libre, de sistemas parecidos,
que podríamos adaptar para nuestro proyecto, facilitando la tarea de programación de la tarjeta Arduino

Sobre las comunicaciones, hay que tener en cuenta que esta debe ser bidireccional,
puesto que la aplicación, además de enviar las instrucciones del usuario,
debe recibir la señal de la cámara y la información sobre los sensores y el estado del vehículo

En todos los casos, las comunicaciones del vehículo se realizarían a través de un
conexión tipo WiFi (en un futuro se podria añadir conexión 3G/4G), para dotar de
una mayor autonomía al vehículo.

Para la realización de las comunicaciones se pueden usar tres estrategias:

 1 Uso de un servidor entre la aplicación y arduino

Arduino y la aplicación cliente mandarían sus informaciones a un servidor común,
que redireccionaria estas informaciones a sus destinos correspondientes.

Como ventajas incluiría la simplicidad del diseño, usando una única dirección IP
para todas las comunicaciones.

Como inconvenientes incluiría la obligatoriedad de obtener una IP publica,
con el coste económico que esta conlleva.

La implementación física se podría realizar a través de una tarjeta de expansión
(shield) GSM para Arduino

 2.  Creación de una red privada virtual (VPN)

Se crearía una VPN con una subred donde estarían conectadas la aplicación y arduino.

Todas las informaciones se mandarían de forma cifrada usando esta red,
existiendo una dirección IP diferente para la placa Arduino y la aplicación cliente.

Como ventaja, contaría con la simplicidad de su implementación, puesto que las
comunicaciones se reducirían a una (falsa) red local.

Como inconveniente, tendría la posible obligación de una IP publica, y la complicación
del montaje físico, dado que el shield 3G de Arduino podría no contar con esta
funcionalidad, lo que nos obligaría a hacer uso de una Raspberry Pi junto
a un minirouter 3G para establecer la conexión.

Se podria implementar usando un dispositivo Arduino Yun, que incorpora un modulo
SOC compatible con Linux.

La IP publica se podria obtener a traves del servicio NoIP, el cual nos ofrece
un servicio DNS de bajo coste, y con un año de prueba gratuita.

 3. Conexión “tradicional” tipo Web, usando router, DNS y puertos

Ambos extremos podrán disponer de IP's dinámicas, y serán los routers y los DNS
los encargados de redirigir la conexión a través de sus respectivos puertos.

Como ventaja, contaría con la posibilidad de uso del shield Arduino 3G y el
ahorro del coste de la IP publica.

Como inconveniente, estarían los costos del DNS y la complejidad del diseño.

Finalmente, para ahorrar costes, existiría otra posibilidad de implementación
física de la red, que se ajustaría a las tres estrategias de diseño.

Esta posibilidad consistiría en el uso de un smartphone (cualquier smartphone
antiguo valdría) y un shield bluetooth conectado a Arduino.

El smartphone establecería la conexión y recibiría los datos, que los transmitiría
a la placa Arduino a traves de bluetooth

Como ventaja estaría el ahorro de costes y la simplificación de las
comunicaciones a una conexión bluetooth, que también permitía reciclar
código de proyectos parecidos.

Como inconveniente estaría la reducción de autonomía del vehículo,
debido al gasto de batería del smartphone.

##Decision

Tras evaluar todas las opciones planteadas, finalmente nos hemos decantado
por la opcion 2, basada en VPN. 

El uso de un dispositivo Arduino Yun permitira darle mas flexibilidad al proyecto,
con un coste inferior al del resto de opciones y una implementacion mas
sencilla que el resto de opciones, ahorrandonos el uso de shield WiFi
y de otros componentes externos.

Ademas, el uso de VPN aporta una mayor seguridad en las comunicaciones,
las cuales podran ir cifradas y protegidas de ataques externos.

##Desarrollo de la Aplicación

La aplicación seria un simple cliente, con el único objetivo de enviar y recibir
las instrucciones e informaciones del vehículo.

La aplicación cliente debe estar capacitada para establecer la conexión
con la placa Arduino y mandar/recibir las informaciones del vehículo.

Para simplificar la programación, la aplicación tendrá una interfaz de escritorio.

Esta interfaz estará dividida en tres partes:

 * La parte superior mostraría la imagen en tiempo real de la cámara del vehículo.

 *  En la parte central estarán situados los controles de movimiento, en principio,
adelante, atrás, izquierda y derecha.

 * En la parte inferior estaría el panel de estado del vehículo,
con la información de los sensores, el estado de cada motor,
y el numero de revoluciones de cada uno.

Además, en ese panel estarían las opciones de activar/desactivar motor auxiliar,
aumentar/reducir revoluciones del motor y activar turbo (el turbo es instantáneo,
por lo que se desactiva solo)

El control de movimiento se podrá realizar con el ratón pulsando los botones
de la interfaz, con el teclado, o usando un gamepad/joystick.

Un posible boceto de la interfaz seria este:

El control de revoluciones sera un scroll entre los valores mínimos y máximos del motor.

Además, incluirá un cuadro de texto para que el usuario pueda ajustar de forma mas
precisa el numero de revoluciones que desea aplicar al motor.

## Financiación

Al ser un proyecto hardware, es básico tener una fuente de financiación para poder comprar los componentes.

El presupuesto completo ronda los 200 euros, lo cual no es excesivo, pero es
demasiado caro para ser costeado por pocas personas.

 * Una forma de abaratar estos costes serian usar componentes reciclados o
donados por integrantes del equipo o colaboradores, pero algunos componentes
son poco comunes y habrá que comprarlos.

 * Otra forma de financiación sencilla seria reunir el dinero entre los
miembros del equipo, pero seria bastante dificultoso e incomodo para los mismos,
y habrá personas que por sus circunstancias no puedan aportar nada; aunque para
algunos componentes sencillos se podría hacer.

 * Otra método, algo mas complicado, consistiría en vender artículos de
promoción del proyecto, pero podría ser útil para conseguir dinero de forma medianamente rápida.

 * La forma mas efectiva seria la obtención de un patrocinio, pero seria
complicado de conseguir con una idea abstracta, aunque seria posible una
vez tengamos un primer prototipo.

Para ahorrar costes, podemos empezar usando las implementaciones mas baratas para los primeros prototipos, que iremos mejorando conforme vayamos obteniendo financiacion.
