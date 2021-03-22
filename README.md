# App MQTT Connection

**Autor**: Agustin Bassi - 2021

## Tabla de contenido

* [Introducción](#introducción)
* [Detalles principales](#detalles-principales)
* [Instalar dependencias](#instalar-dependencias)
* [Descargar el código](#descargar-el-código)
* [Ejecutar la aplicación](#ejecutar-la-aplicación)
* [Colaborar](#colaborar)
* [Licencia](#licencia)

## Introducción

Este proyecto es una demo de una aplicación IoT sobre MQTT. Se compone de un broker, un cliente web y otro embebido, y puede controlar un dispositivo remoto desde el navegador web.

El broker y el cliente web se ejecutan sobre el ecosistema Docker. La aplicación embebida corre sobre un ESP32 o similar y está desarrollada con PlatformIO.

Para que tengas una idea clara, en esta imagen hay un diagrama de la arquitectura de la aplicación.

![architecture](doc/architecture.png)

## Detalles principales

Ahora que ya viste la arquitectura general, podes ver detalles más específicos.

#### `Cómo está armado el repo`

Todos las piezas de la aplicación están separadas en distintos repositorios e incluídas como submódulos. Con esto se puede tener una arquitectura orientada a microservicios, y actualizar y mantener las partes de manera independiente sin necesidad de modificar este repositorio.

#### `El broker`

El broker es la columna vertebral del sistema, donde los distintos clientes se comunican entre sí. Está basado en `Mosquitto` y soporta la conexión por Websockets en el puerto 9001, MQTT en el 1883 y el 8883 para comunicación con autenticación. Se ejecuta sobre un contenedor de Docker para poder correrlo de igual manera en distintas plataformas. Dentro de esta app se encuentra en el directorio `service-mqtt-broker` y los detalles sobre cómo funciona los podes ver el [README del proyecto](https://github.com/gotoiot/service-mqtt-broker).

#### `El cliente web`

El cliente web es una `single-page-application` que se comunica con el broker através de WebSockets. Desde acá se pueden publicar y suscribirse a topics, y visualizar los mensajes en tiempo real. El cliente web es accedido a través de un servidor que también se ejecuta sobre un contenedor de Docker. Dentro de esta app se encuentra en el directorio `web-mqtt-client` y los detalles sobre cómo funciona los podes ver en el [README del proyecto](https://github.com/gotoiot/web-mqtt-client).

#### `El cliente embebido`

El cliente embebido puede ser cualquier aplicación con comunicación MQTT. Para esta app se basa en el ejemplo `Pressure Measurer` del repo [Embed IoT Platform](https://github.com/gotoiot/embed-iot-platform), que es un proyecto integral para desarrollo IoT embebido. Además de este ejemplo tiene precargadas varias aplicaciones para correr sobre el ESP32 o alguna placa similar. Dentro de esta app se encuentra en el directorio `embed-iot-platform` y más adelante vas a ver cómo ponerlo en marcha. 

#### `Ejecución de servicios`

Los servicios de la aplicación se encuentran sobre contenedores de Docker, así se pueden desplegar de igual manera en diferentes plataformas. Los detalles sobre cómo funcionan los servicios los podés ver directamente en el archivo `docker-compose.yml` y complementar la información con el README de cada parte de la app.

## Instalar dependencias

Para correr este proyecto es necesario que instales `Docker` y `Docker Compose`. Para la parte embebida hay que instalar `PlatformIO` dentro de `Visual Studio Code`.

#### `Ecosistema Docker`

En [este documento](https://www.gotoiot.com/pages/articles/docker_installation/index.html) publicado en nuestra web están los detalles para instalar Docker y Docker Compose. Si querés instalar ambas herramientas en una Raspberry Pi podés seguir [esta guía](https://devdojo.com/bobbyiliev/how-to-install-docker-and-docker-compose-on-raspberry-pi) que muestra todos los detalles de instalación.

En caso que tengas algún incoveniente o quieras profundizar al respecto, podes leer la documentación oficial de [Docker](https://docs.docker.com/get-docker/) y también la de [Docker Compose](https://docs.docker.com/compose/install/).

#### `Ecosistema embebido`

Para la aplicación embebida vas a necesitar una placa ESP32 o similar e instalar PlatformIO dentro de VS Code. Instalá VS Code directamente de la [web oficial](https://code.visualstudio.com/download) y después en el artículo de [instalación de PlatformIO en VS Code](https://www.gotoiot.com/pages/articles/platformio_vscode_installation/) de nuestra web están los detalles de configuración de la herramienta y correr un programa de ejemplo.

Continua con la descarga del código cuando tengas las dependencias instaladas y funcionando.

## Descargar el código

Para descargar el codigo, lo más conveniente es realizar un `fork` de este proyecto a tu cuenta personal haciendo click en [este link](https://github.com/gotoiot/app-mqtt-connection/fork). Una vez que ya tengas el fork a tu cuenta, descargalo desde la terminal con este comando (acordate de poner tu usuario en el link):

```
git clone https://github.com/TU_USUARIO/app-mqtt-connection.git
```

Cuando tengas el código principal, descargá/actualizá los submódulos del proyecto con este comando y pone a correr la aplicación una vez que descarguen:

```
git submodule update --init --recursive --remote
```

> En caso que no tengas una cuenta en Github podes clonar directamente este repo y descargar los submódulos.

## Ejecutar la aplicación

Corre el comando `docker-compose up` desde la raíz del proyecto para descargar las imágenes de Docker del broker MQTT y del servidor web y luego ponerlas en funcionamiento. Para acceder al cliente web ingresa esta URL [http://localhost:5001/](http://localhost:5001/) en el navegador (cambia la IP de la URL si la estás corriendo de manera remota). 

Si pudiste acceder al cliente web significa que la aplicación se encuentra corriendo bien, ahora hay que configurarlo.

#### `Comunicación con el cliente web`

En el cliente web hay varios campos que tienen valores precargados para que no tengas que escribirlos cada vez.

Modifica los campos que necesites para el broker y comenzá presionando `CONNECT`. Después `SUBSCRIBE` para suscribirte a todos los topics (`#`) y una vez que te suscribas presioná `PUBLISH` para producir un `echo` del mensaje enviado. Fijate que en la sección de logs aparecen las acciones que vas realizando.

Si querés saber más detalles podes ir al [README del proyecto](https://github.com/gotoiot/web-mqtt-client), y sino podes continuar poniendo en marcha la aplicación embebida.

#### `Comunicación con el cliente embebido`

El cliente embebido es la aplicación Pressure Measurer del proyecto `Embed IoT Platform`, que como vimos, es una plataforma que facilita el desarrollo e implementación de aplicaciones embebidas IoT.

Abrí la carpeta del proyecto desde VS Code y copia el contenido del archivo `embed-iot-platform/examples/mqtt/pressure_measurer/pressure_measurer.cpp`. Después reemplazá todo el contenido del archivo `embed-iot-platform/src/main.cpp` por el código que copiaste.

También vas a necesitar poner un ID de dispositivo, los datos del WiFi y la IP del broker MQTT dentro del archivo `embed-iot-platform/src/secrets.h`.

Los detalles sobre el Embed IoT Platform en general los podés ver en el [README del proyecto](https://github.com/gotoiot/embed-iot-platform) y los detalles específicos del Pressure Measurer los vas a encontrar en el [README del ejemplo](https://github.com/gotoiot/embed-iot-platform/blob/master/examples/mqtt/pressure_measurer/README.md).

Cuando ya tengas el código listo anda a la extensión PlatformIO en VSCode y abrí una terminal desde la sección `Quick Access->Miscellaneous->New Terminal`. Chequea que estés dentro del directorio embed-iot-platform en la terminal y corre el comando `pio run -t upload && pio device monitor` para compilar el código, descargarlo a la placa y abrir la terminal serie. Deberías ver una salida como esta cuando el programa empiece a correr:

```
Welcome to Pressure Measurer - www.gotoiot.com

Trying connection to Wifi SSID 'WIFI_SSID'.
Wifi connected. Assigned device IP: '192.168.0.103'
Subscribed to topic: 'DEVICE_ID/config'
Subscribed to topic: 'DEVICE_ID/status/get'
Sending MQTT Topic 'up' -> '{"device_id": "DEVICE_ID, "ip": "192.168.0.103", "status": "running"}'
Sending MQTT Topic 'DEVICE_ID/pressure'->'{"value": 55, "measure": "psi", "time": 12340074}
...
Sending MQTT Topic 'DEVICE_ID/pressure'->'{ "value": 83, "measure": "psi", "time": 448821023}
```

#### `Comunicación entre clientes`

Con el cliente web suscripto a todos los topics deberías comenzar a ver los mensajes enviados por el Pressure Measurer en la sección de logs. Si publicás desde el cliente web el topic `DEVICE_ID/config` con un valor entre 1000 y 10000 ms vas a poder cambiar el período en que el dispositivo publica las mediciones fake de presión y este tiempo se va a ver reflejado en cómo llegan los mensajes al log.

Realizá distintas pruebas y fijate cómo responden ambos clientes tanto en la sección de logs del cliente web como en la terminal serie del cliente embebido.

#### `Otras aplicaciones embebidas`

Ahora que ya sabés como correr el ejemplo Pressure Measurer, podés probar cualquier otra aplicación MQTT dentro de la [sección de ejemplos MQTT](https://github.com/gotoiot/embed-iot-platform/blob/master/examples/mqtt) del proyecto Embed IoT Platform. Los detalles de implementación los vas a encontrar dentro del README de cada uno.

## Colaborar

¿Te gustó el proyecto? Si es así no dudes en apoyarlo con una estrella en Github desde [la home del proyecto](https://github.com/gotoiot/app-mqtt-connection), esto motiva mucho a seguir adelante con el desarrollo de código para la comunidad. Si estás interesado en recibir novedades cuando se hagan actualizaciones, podes suscribirte desde [este link](https://github.com/gotoiot/app-mqtt-connection/subscription).

Si te gustaría aplicar mejoras a este proyecto podes abrir un hilo de discusión en [este link](https://github.com/gotoiot/app-mqtt-connection/issues/new) para conversarlas y luego podrías enviarlas mediante un `pull request`. 

Finalmente podés compartir este proyecto para que más personas puedan utilizarlo y beneficiarse de esta gran comunidad del software libre.

## Licencia

[MIT](https://choosealicense.com/licenses/mit/)

![footer](doc/gotoiot-footer.png)