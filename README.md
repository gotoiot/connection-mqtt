<a href="https://www.gotoiot.com/">
    <img src="doc/gotoiot-logo.png" alt="logo" title="Goto IoT" align="right" width="60" height="60" />
</a>

Connection MQTT
===============

*Ayudaría mucho si apoyaras este proyecto con una ⭐ en Github!*

Este proyecto es una esfera que alberga distintas aplicaciones y servicios que conforman una plataforma integral MQTT. 

Está formado por un broker y un cliente web que corren sobre el ecosistema Docker, y pueden conectarse clientes externos como sensores remotos y centrales de monitoreo.

Para que tengas una idea clara, en esta imagen hay un diagrama de como interactuan las partes entre sí.

![architecture](doc/architecture.png)


## Comenzando 🚀

Esta sección es una guía con los pasos escenciales para que puedas poner en marcha la aplicación.

<details><summary><b>Mira los pasos necesarios</b></summary><br>

### Instalar las dependencias

Para correr este proyecto es necesario que instales `Docker` y `Docker Compose`. 

En [este artículo](https://www.gotoiot.com/pages/articles/docker_installation_linux/) publicado en nuestra web están los detalles para instalar Docker y Docker Compose en una máquina Linux. Si querés instalar ambas herramientas en una Raspberry Pi podés seguir [este artículo](https://www.gotoiot.com/pages/articles/rpi_docker_installation) de nuestra web que te muestra todos los pasos necesarios.

En caso que quieras instalar las herramientas en otra plataforma o tengas algún incoveniente, podes leer la documentación oficial de [Docker](https://docs.docker.com/get-docker/) y también la de [Docker Compose](https://docs.docker.com/compose/install/).

Continua con la descarga del código cuando tengas las dependencias instaladas y funcionando.

### Descargar el código

Para descargar el codigo, lo más conveniente es realizar un `fork` de este proyecto a tu cuenta personal haciendo click en [este link](https://github.com/gotoiot/connection-mqtt/fork). Una vez que ya tengas el fork a tu cuenta, descargalo desde la terminal con este comando (acordate de poner tu usuario en el link):

```
git clone https://github.com/USER/connection-mqtt.git
```

Cuando tengas el código principal, descargá/actualizá los submódulos del proyecto con este comando y continua con la ejecución de la aplicación una vez que descarguen:

```
git submodule update --init --recursive --remote
```

> En caso que no tengas una cuenta en Github podes clonar directamente este repo y descargar los submódulos.

### Ejecutar la aplicación

Para ejecutar la aplicación tenes que correr el comando `docker-compose up` desde la raíz del proyecto. Este comando va a  descargar las imágenes de Docker del broker MQTT y del servidor web y luego ponerlas en funcionamiento. Para acceder al cliente web ingresa a esta URL [http://localhost:5001/](http://localhost:5001/) en el navegador (cambia la IP de la URL si la estás corriendo de manera remota). 

Si pudiste acceder al cliente web significa que la aplicación se encuentra corriendo bien.

</details>

Continuá explorando el proyecto una vez que lo tengas funcionando.


## Configuraciones de funcionamiento 🔩

Una vez que la aplicación comienza a ejecutarse es necesario conectar clientes al broker. En esta sección podés ver los detalles al respecto

<details><summary><b>Lee cómo configurar la aplicación</b></summary><br>

### Cofiguración del Web Client

En el cliente web hay varios campos que tienen valores precargados para que no tengas que escribirlos cada vez.

Modifica los campos que necesites para el broker y comenzá presionando `CONNECT`. Después `SUBSCRIBE` para suscribirte a todos los topics (`#`) y una vez que te suscribas presioná `PUBLISH` para producir un `echo` del mensaje enviado. Fijate que en la sección de logs aparecen las acciones que vas realizando.

Para hacer una prueba más completa, podés abrir una nueva instancia del cliente web en otra pestaña del navegador y probar la comunicación publicando topics desde un cliente y recibiendo los mensajes por el otro. 

Si querés saber más detalles podes ir al [README del proyecto](https://github.com/gotoiot/web-mqtt-client). 

### Conectar clientes embebidos MQTT basados en "Embed IoT Core"

Dentro de nuestros proyectos tenemos a [Embed IoT Core](https://github.com/gotoiot/embed-iot-core), que es una plataforma exclusivamente dedicada al desarrollo de aplicaciones embebidas orientadas a IoT. En este proyecto hay aplicaciones de ejemplo para distintos protocolos de comunicación; está optimizado para funcionar en una placa `ESP32` o similar y utiliza la herramienta `PlatformIO` para el manejo de bibliotecas, compilación y descarga del código a la placa y muchas cosas más. 

Si contás con una placa y querés poner en marcha una aplicación embebida para conectarte, te recomendamos que le des una mirada al [README del proyecto](https://github.com/gotoiot/embed-iot-core), que tiene toda la información para que entiendas de qué se trata, cómo descargar el código e instalar las dependencias, y cómo poner en marcha un ejemplo de prueba.

Cuando tengas andando el ejemplo de prueba, podés elegir algunas de las aplicaciones MQTT dentro de la [lista de aplicaciones](https://github.com/gotoiot/embed-iot-core#lista-de-aplicaciones) para conectarte con el broker MQTT.

La info necesaria para configurar y correr las diferentes aplicaciones la vas a encontrar directamente en el [README del proyecto](https://github.com/gotoiot/embed-iot-core), así podés acceder siempre a la información actualizada. 

### Comunicación entre Web Client y otros clientes

Si ya tenés configurado y andando el cliente web, y también pusiste en marcha alguna aplicación MQTT basada en `Embed IoT Core` es hora que hagas algunas pruebas de todas las partes para ver cómo interactúan. 

El propósito del cliente web es actuar como una terminal para enviar/recibir topics y visualizarlos en tiempo real. Suscribite a todos los topics desde el cliente web y luego intentá conectarte broker con la aplicación embebida. Si todo funciona bien, dentro del cliente web deberías ver los topics que envía la app embebida.

Lee la documentación de la app embebida para ver qué topics puede recibir, así le podes mandar mensajes desde el cliente web para controlar el dispositivo.

Realizando estas pruebas vas a poder tener un caso de uso real en el que un dispositivo remoto es controlado desde un navegador web.

</details>

## Detalles principales 🔍

En esta sección vas a encontrar las características más relevantes del proyecto.

<details><summary><b>Mira los detalles más importantes de la aplicación</b></summary>

### El broker

El broker es la columna vertebral del sistema, donde los distintos clientes se comunican entre sí. Está basado en **Mosquitto** y soporta la conexión por Websockets en el puerto 9001, MQTT en el 1883 y el 8883 para comunicación con autenticación. Se ejecuta sobre un contenedor de Docker para poder correrlo de igual manera en distintas plataformas. Se encuentra en el directorio **service-mqtt-broker** y los detalles sobre cómo funciona los podes ver el [README del proyecto](https://github.com/gotoiot/service-mqtt-broker).

### El cliente web

El cliente web es una **single-page-application** que se comunica con el broker através de WebSockets. Desde acá se pueden publicar y suscribirse a topics, y visualizar los mensajes en tiempo real. El cliente web es accedido a través de un servidor que también se ejecuta sobre un contenedor de Docker. Se encuentra en el directorio **web-mqtt-client** y los detalles sobre cómo funciona los podes ver en el [README del proyecto](https://github.com/gotoiot/web-mqtt-client).

### Ejecución de servicios

Los servicios de la aplicación se ejecutan sobre contenedores de Docker, así se pueden desplegar de igual manera en diferentes plataformas. Los detalles sobre cómo funcionan los servicios los podés ver directamente en el archivo **docker-compose.yml** y complementar la información con el README de cada parte de la app.

### Conexión de sensores/actuadores

La conexión con sensores y actuadores no está incluída dentro de este repositorio ya que la plataforma se ejecuta sobre el ecosistema Docker. De todas maneras tenemos el repo [Embed IoT Core](https://github.com/gotoiot/embed-iot-core), que es una plataforma para desarrollar aplicaciones embebidas basadas en IoT. Tiene aplicaciones de ejemplo para diferentes protocolos de comunicación, y entre ellas, una sección dedicada a MQTT. Más abajo, en la sección [Conectar clientes remotos](#conectar-clientes-remotos), vas a encontrar los detalles para esto.

</details>


## Tecnologías utilizadas 🛠️

En esta sección podés ver las tecnologías más importantes utilizadas.

<details><summary><b>Mira la lista completa de tecnologías</b></summary><br>

* [Docker](https://www.docker.com/) - Ecosistema que permite la ejecución de contenedores de software.
* [Docker Compose](https://docs.docker.com/compose/) - Herramienta que permite administrar múltiples contenedores de Docker.
* [Material Design](https://material.io/design) - Bibliotecas de estilo responsive para aplicaciones web.
* [Mosquitto](https://mosquitto.org/) - Broker MQTT libre creado por Eclipse Foundation, ampliamente utilizado en el mundo IoT.

</details>


## Contribuir 🖇️

Si estás interesado en el proyecto y te gustaría sumar fuerzas para que siga creciendo y mejorando, podés abrir un hilo de discusión para charlar tus propuestas en [este link](https://github.com/gotoiot/connection-mqtt/issues/new). Así mismo podés leer el archivo [Contribuir.md](https://github.com/gotoiot/gotoiot-doc/wiki/Contribuir) de nuestra Wiki donde están bien explicados los pasos para que puedas enviarnos pull requests.

## Sobre Goto IoT 📖

Goto IoT es una plataforma que publica material y proyectos de código abierto bien documentados junto a una comunidad libre que colabora y promueve el conocimiento sobre IoT entre sus miembros. Acá podés ver los links más importantes:

* **[Sitio web](https://www.gotoiot.com/):** Donde se publican los artículos y proyectos sobre IoT. 
* **[Github de Goto IoT:](https://github.com/gotoiot)** Donde están alojados los proyectos para descargar y utilizar. 
* **[Comunidad de Goto IoT:](https://groups.google.com/g/gotoiot)** Donde los miembros de la comunidad intercambian información e ideas, realizan consultas, solucionan problemas y comparten novedades.
* **[Twitter de Goto IoT:](https://twitter.com/gotoiot)** Donde se publican las novedades del sitio y temas relacionados con IoT.
* **[Wiki de Goto IoT:](https://github.com/gotoiot/doc/wiki)** Donde hay información de desarrollo complementaria para ampliar el contexto.

## Muestas de agradecimiento 🎁

Si te gustó este proyecto y quisieras apoyarlo, cualquiera de estas acciones estaría más que bien para nosotros:

* Apoyar este proyecto con una ⭐ en Github para llegar a más personas.
* Sumarte a [nuestra comunidad](https://groups.google.com/g/gotoiot) abierta y dejar un feedback sobre qué te pareció el proyecto.
* [Seguirnos en twitter](https://github.com/gotoiot/doc/wiki) y dejar algún comentario o like.
* Compartir este proyecto con otras personas.

## Autores 👥

Las colaboraciones principales fueron realizadas por:

* **[Agustin Bassi](https://github.com/agustinBassi)**: Ideación, puesta en marcha y mantenimiento del proyecto.

También podés mirar todas las personas que han participado en la [lista completa de contribuyentes](https://github.com/connection-mqtt/contributors).

## Licencia 📄

Este proyecto está bajo Licencia ([MIT](https://choosealicense.com/licenses/mit/)). Podés ver el archivo [LICENSE.md](LICENSE.md) para más detalles sobre el uso de este material.

---

**Copyright © Goto IoT 2021** ⌨️ [**Website**](https://www.gotoiot.com) ⌨️ [**Group**](https://groups.google.com/g/gotoiot) ⌨️ [**Github**](https://www.github.com/gotoiot) ⌨️ [**Twitter**](https://www.twitter.com/gotoiot) ⌨️ [**Wiki**](https://github.com/gotoiot/doc/wiki)