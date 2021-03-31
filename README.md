<a href="https://www.gotoiot.com/">
    <img src="doc/logo-social-networks.png" alt="Goto IoT logo" title="Goto IoT" align="right" height="60" />
</a>

Sphere MQTT
===========

**Autor**: Agustin Bassi - 2021

:star: Nos ayudarías mucho apoyando a Goto IoT con una estrella en Github!

## Tabla de contenido

* [Introducción](#introducción)
* [Detalles principales](#detalles-principales)
* [Instalar dependencias](#instalar-dependencias)
* [Descargar el código](#descargar-el-código)
* [Ejecutar la aplicación](#ejecutar-la-aplicación)
* [Conectar clientes remotos](#conectar-clientes-remotos)
* [Colaborar](#colaborar)
* [Licencia](#licencia)

## Introducción

Este proyecto es una esfera que alberga distintas aplicaciones y servicios que conforman una plataforma integral MQTT. 

Está formado por un broker y un cliente web que corren sobre el ecosistema Docker, y pueden conectarse clientes externos como sensores remotos y centrales de monitoreo.

Para que tengas una idea clara, en esta imagen hay un diagrama de como interactuan las partes entre sí.

![architecture](doc/architecture.png)

## Inicio rápido

Seguí esta guía de pasos para ejecutar rápidamente el proyecto.

<details><summary><b>Ver los pasos rápidos</b></summary>

1. Instalá Docker y Docker Compose en tu sistema.
2. Descargá el código.
3. Poné en marcha la plataforma.
4. Configurá el cliente web para conectarte al broker.
5. Probá la comunicación.
6. Configura y poné en marcha un cliente embebido para interactuar con la plaatforma (opcional).

</details>

## Detalles principales

Conociendo las generalidades del proyecto ya podes ver detalles más específicos.

<details><summary><b>Mira los detalles del proyecto</b></summary>

#### `Cómo está armado el repo`

Las piezas de la aplicación están separadas en distintos repositorios e incluídas como submódulos. Esto permite tener una arquitectura orientada a microservicios, y actualizar/mantener las partes de manera independiente.

#### `El broker`

El broker es la columna vertebral del sistema, donde los distintos clientes se comunican entre sí. Está basado en `Mosquitto` y soporta la conexión por Websockets en el puerto 9001, MQTT en el 1883 y el 8883 para comunicación con autenticación. Se ejecuta sobre un contenedor de Docker para poder correrlo de igual manera en distintas plataformas. Se encuentra en el directorio `service-mqtt-broker` y los detalles sobre cómo funciona los podes ver el [README del proyecto](https://github.com/gotoiot/service-mqtt-broker).

#### `El cliente web`

El cliente web es una `single-page-application` que se comunica con el broker através de WebSockets. Desde acá se pueden publicar y suscribirse a topics, y visualizar los mensajes en tiempo real. El cliente web es accedido a través de un servidor que también se ejecuta sobre un contenedor de Docker. Se encuentra en el directorio `web-mqtt-client` y los detalles sobre cómo funciona los podes ver en el [README del proyecto](https://github.com/gotoiot/web-mqtt-client).

#### `Ejecución de servicios`

Los servicios de la aplicación se encuentran sobre contenedores de Docker, así se pueden desplegar de igual manera en diferentes plataformas. Los detalles sobre cómo funcionan los servicios los podés ver directamente en el archivo `docker-compose.yml` y complementar la información con el README de cada parte de la app.

#### `Conexión de sensores/actuadores`

La conexión con sensores y actuadores no está incluída dentro de este repositorio, ya que la plataforma se ejecuta sobre el ecosistema Docker. De todas maneras tenemos el repo [Embed IoT Core](https://github.com/gotoiot/embed-iot-core), que es una plataforma para desarrollar aplicaciones embebidas basadas en IoT. Tiene aplicaciones de ejemplo para diferentes protocolos de comunicación, y entre ellas, una sección dedicada a MQTT. Más abajo, en la sección [Conectar clientes remotos](#conectar-clientes-remotos), vas a encontrar los detalles para esto.

</details>

## Instalar dependencias

Para correr este proyecto es necesario que instales `Docker` y `Docker Compose`. 

<details><summary><b>Mira instrucciones de instalación</b></summary>

En [este documento](https://www.gotoiot.com/pages/articles/docker_installation/index.html) publicado en nuestra web están los detalles para instalar Docker y Docker Compose. Si querés instalar ambas herramientas en una Raspberry Pi podés seguir [esta guía](https://devdojo.com/bobbyiliev/how-to-install-docker-and-docker-compose-on-raspberry-pi) que muestra todos los detalles de instalación.

En caso que tengas algún incoveniente o quieras profundizar al respecto, podes leer la documentación oficial de [Docker](https://docs.docker.com/get-docker/) y también la de [Docker Compose](https://docs.docker.com/compose/install/).

Continua con la descarga del código cuando tengas las dependencias instaladas y funcionando.

</details>

## Descargar el código

Para descargar el codigo, lo más conveniente es realizar un `fork` de este proyecto a tu cuenta personal haciendo click en [este link](https://github.com/gotoiot/sphere-mqtt/fork). Una vez que ya tengas el fork a tu cuenta, descargalo desde la terminal con este comando (acordate de poner tu usuario en el link):

```
git clone https://github.com/USER/sphere-mqtt.git
```

Cuando tengas el código principal, descargá/actualizá los submódulos del proyecto con este comando y continua con la ejecución de la aplicación una vez que descarguen:

```
git submodule update --init --recursive --remote
```

> En caso que no tengas una cuenta en Github podes clonar directamente este repo y descargar los submódulos.

## Ejecutar la aplicación

Corre el comando `docker-compose up` desde la raíz del proyecto para descargar las imágenes de Docker del broker MQTT y del servidor web y luego ponerlas en funcionamiento. Para acceder al cliente web ingresa esta URL [http://localhost:5001/](http://localhost:5001/) en el navegador (cambia la IP de la URL si la estás corriendo de manera remota). 

Si pudiste acceder al cliente web significa que la aplicación se encuentra corriendo bien, ahora hay que configurarlo.

<details><summary><b>Cofiguración del cliente web</b></summary>

En el cliente web hay varios campos que tienen valores precargados para que no tengas que escribirlos cada vez.

Modifica los campos que necesites para el broker y comenzá presionando `CONNECT`. Después `SUBSCRIBE` para suscribirte a todos los topics (`#`) y una vez que te suscribas presioná `PUBLISH` para producir un `echo` del mensaje enviado. Fijate que en la sección de logs aparecen las acciones que vas realizando.

También podés abrir una nueva instancia del cliente web en otra pestaña del navegador y probar la comunicación publicando topics desde un cliente y recibiendo por el otro. 

Si querés saber más detalles podes ir al [README del proyecto](https://github.com/gotoiot/web-mqtt-client). 

</details>

## Conectar clientes remotos

Como viste en el diagrama de las partes, la `Sphere MQTT` tiene un core compuesto por el broker y el cliente web, y luego distintos clientes pueden conectarse para interactuar con la plataforma. En esta sección podes ver más detalles al respecto.

<details><summary><b>Aplicaciones MQTT basadas en "Embed IoT Core"</b></summary>

En la sección de [Detalles principales](#detalles-principales) viste que es posible conectar sensores/actuadores a la plataforma. 

Dentro de nuestros proyectos tenemos a [Embed IoT Core](https://github.com/gotoiot/embed-iot-core), que es una plataforma exclusivamente dedicada al desarrollo de aplicaciones embebidas orientadas a IoT. Dentro de este proyecto hay aplicaciones de ejemplo para distintos protocolos de comunicación, y entre ellas, ejemplos para MQTT. 

Para poder correr cualquiera de las aplicaciones vas a necesitar una placa basada en el módulo `ESP32` o similar; instalar `Visual Studio Code` en tu sistema, y dentro de VS Code instalar la extensión `PlatformIO`. Esta extensión es un toolchain para desarrollo de sistemas embebidos especialmente útil, que se encarga del manejo de bibliotecas, compilación y descarga de código, debugging, y muchas cosas más. 

Si querés poner en marcha una aplicación embebida para conectarte, te recomendamos que le des una leída al [README del proyecto](https://github.com/gotoiot/embed-iot-core), que tiene toda la información para que entiendas de qué se trata, cómo descargar el código e instalar las dependencias, y cómo poner en marcha un ejemplo de prueba.

Cuando tengas andando el ejemplo de prueba, podés elegir algunas de las aplicaciones MQTT dentro de la [lista de aplicaciones](https://github.com/gotoiot/embed-iot-core#lista-de-aplicaciones) para conectarte a la `Sphere MQTT`. 

La info necesaria para configurar el proyecto y correr las aplicaciones está directamente en el [README del proyecto](https://github.com/gotoiot/embed-iot-core), así podés siempre acceder a la información actualizada. 

</details>

<details><summary><b>Comunicación entre clientes</b></summary>

Si ya tenés configurado y andando el cliente web, y también pusiste en marcha alguna aplicación MQTT basada en `Embed IoT Core` es hora que hagas algunas pruebas de todas las partes para ver cómo interactúan. 

El propósito del cliente web es actuar como una terminal para enviar/recibir topics y visualizarlos en tiempo real. Suscribite a todos los topics desde el cliente web y luego intentá conectarte broker con la aplicación embebida. Si todo funciona bien, dentro del cliente web deberías ver los topics que envía la app embebida.

Lee la documentación de la app embebida para ver qué topics puede recibir, así le podes mandar mensajes desde el cliente web para controlar el dispositivo.

Realizando estas pruebas vas a poder tener un caso de uso real en el que un dispositivo remoto es controlado desde un navegador web.

</details>

## Colaborar

¿Te gustó el proyecto? Si es así no dudes en apoyarlo con una Star en Github desde [la home del proyecto](https://github.com/gotoiot/sphere-mqtt), esto motiva mucho a seguir adelante con el desarrollo de código para la comunidad. Si estás interesado en recibir novedades cuando se hagan actualizaciones, podes suscribirte desde [este link](https://github.com/gotoiot/sphere-mqtt/subscription).

Si te gustaría aplicar mejoras a este proyecto podes abrir un hilo de discusión en [este link](https://github.com/gotoiot/sphere-mqtt/issues/new) para conversarlas y luego podrías enviarlas mediante un `pull request`. 

Finalmente podés compartir este proyecto para que más personas puedan utilizarlo y beneficiarse de esta gran comunidad del software libre.

## Licencia

[MIT](https://choosealicense.com/licenses/mit/)

![footer](doc/gotoiot-footer.png)
