# App MQTT Connection

**Autor**: Agustin Bassi - 2021

## Tabla de contenido

* [Introducción](#introducción)
* [Instalar dependencias](#instalar-dependencias)
* [Descargar el código](#descargar-el-código)
* [Ejecutar la aplicación](#ejecutar-la-aplicación)
* [Colaborar](#colaborar)
* [Licencia](#licencia)

## Introducción

Este proyecto es una aplicación de IoT basada en MQTT que se compone de un broker y un cliente web para comunicarse con el broker. Se ejecuta sobre Docker y es capaz de correr en diversos sistemas operativos, lo que la hace ideal para realizar pruebas con el protocolo MQTT en diferentes ambientes.

A través del broker se pueden conectar también distintos dispositivos y clientes, por lo que desde el cliente web es posible comunicarse con dispositivos embebidos, controlar sus salidas y leer los datos de sus sensores.

Para que tengas una idea clara sobre cómo está armado el proyecto, en la imagen siguiente podes ver un diagrama de la arquitectura general de la aplicación.

![architecture](doc/architecture.png)

[Web MQTT Client](https://github.com/gotoiot/web-mqtt-client) es una `single-page-application` que se comunica con el broker MQTT através de `WebSockets`. Funciona como un cliente donde se pueden publicar y suscribirse a topics; y visualizar los mensajes en tiempo real. El broker está basado en `Mosquitto` y soporta la conexión por Websockets en el puerto 9001, mientras que el puerto 1883 está dedicado a MQTT en texto plano y el puerto 8883 para comunicación con autenticación. 

Cualquier dispositivo embebido se puede conectar al broker; publicar y suscribirse a topics. Para que puedas probar distintas aplicaciones embebidas, armamos el proyecto [Embed IoT Platform](https://github.com/gotoiot/embed-iot-platform), que es un proyecto integral de desarrollo embebido para IoT y que tiene varias aplicaciones. Más adelante te explicamos cómo correr una de las aplicaciones MQTT disponibles.

Tanto el `Web MQTT Client`, como el proyecto `Embed IoT Platform` están configurados como submódulos dentro de este repositorio. Esto permite que ambos proyectos puedan ser actualizados y mejorados de manera independiente, sin la necesidad de modificar este proyecto.

## Instalar dependencias

Para correr este proyecto es necesario que instales `Docker` y `Docker Compose`. Ejecutarse sobre el ecosistema Docker le da la capacidad de implementarse de igual manera en cualquier sistema operativo, ya que la herramienta se encarga de abstraer la aplicación en contenedores de software independientes dentro del sistema.

En [este documento](https://www.gotoiot.com/pages/articles/docker_installation/index.html) publicado en nuestra web están los detalles para instalar Docker y Docker Compose. Si querés instalar el ecosistema Docker en una Raspberry Pi podés seguir [esta guía](https://devdojo.com/bobbyiliev/how-to-install-docker-and-docker-compose-on-raspberry-pi) que muestra todos los detalles de instalación.

En caso que tengas algún incoveniente o quieras profundizar al respecto, podes leer la documentación oficial de [Docker](https://docs.docker.com/get-docker/) y también la de [Docker Compose](https://docs.docker.com/compose/install/).

## Descargar el código

Para descargar el codigo, lo más conveniente es realizar un `fork` de este proyecto a tu cuenta personal haciendo click en [este link](https://github.com/gotoiot/app-mqtt-connection/fork). Una vez que ya tengas el fork a tu cuenta, descargalo con este comando (acordate de poner tu usuario en el link):

```
git clone https://github.com/TU_USUARIO/app-mqtt-connection.git
```

> En caso que no tengas una cuenta en Github podes clonar directamente este repo.

Cuando tengas el código principal, descargá/actualizá los submódulos del proyecto con este comando:

```
git submodule update --init --recursive --remote
```

Abrí la carpeta del proyecto desde VS Code cuando descargues el código.

## Ejecutar la aplicación

Accedé a la raíz del proyecto desde una terminal y ejecutá el siguiente comando, que se encarga de descargar las imágenes de Docker y ejecutar los contenedores de la aplicación:

```
docker-compose up
```

Para acceder al `Web MQTT Client` ingresa esta URL [http://localhost:5001/](http://localhost:5001/) en el navegador. En caso que estés corriendo la aplicacion de manera remota, cambiá `localhost` por la IP correspondiente. Si pudiste acceder al cliente web significa que la aplicación se encuentra corriendo adecuadamente, ahora hay que configurarlo.

`Comunicación con cliente web`

Una vez dentro del cliente web, tenes que setear los campos para conectarte al broker. Muchos de los campos tienen valores precargados para no tener que escribirlos cada vez.

Si los datos por defecto te sirven, comenzá presionando `CONNECT` y después `SUBSCRIBE` para suscribirte a todos los topics (`#`). Fijate que en la sección de logs aparecen las acciones que vas realizando. Después una vez que te suscribas a todos los topics presioná el botón `PUBLISH`. Esto va a producir un `echo` del mensaje enviado.

Para hacer una prueba más completa podés abrir en otra pestaña una nueva instancia del cliente web, entonces desde una te suscribis a topics y desde la otra publicas, así podes ver la comunicación entre dos clientes utilizando el broker. Podés ver todos los detalles en el [README del proyecto](https://github.com/gotoiot/web-mqtt-client).

Si querés ir un paso más allá, podés conectar un cliente embebido para realizar una prueba MQTT más integral, como te mostramos a continuación.

`Comunicación con cliente embebido`

Tal como mencionamos anteriormente, el proyecto `Embed IoT Platform` es una plataforma que facilita el desarrollo e implementación de aplicaciones embebidas IoT, y que tiene una sección específica de aplicaciones MQTT.

Para que puedas poner en marcha las distintas aplicaciones vas a necesitar una placa `ESP32` o similar, instalar `Visual Studio Code` en el sistema, y `PlatformIO` dentro de VS Code. Todos los detalles del proyecto así como también los de instalación de herramientas están plasmados en el [README del proyecto](https://github.com/gotoiot/embed-iot-platform), por lo que es necesario que primero completes los pasos generales antes de correr cualquiera de los ejemplos. Una vez funcionando el programa de ejemplo, pongamos en marcha una aplicación embebida MQTT.

Vamos a probar la aplicación MQTT más simple, así que como primer paso, copia el [código de ejemplo "Pressure Measurer"](https://github.com/gotoiot/embed-iot-platform/blob/master/examples/mqtt/pressure_measurer/pressure_measurer.cpp) en el archivo `embed-iot-platform/src/main.cpp` y cargá adecuadamente los valores del ID del dispositivo, del WiFi y del broker MQTT en el archivo `embed-iot-platform/src/secrets.h` tal como está explicado en el [README del ejemplo](https://github.com/gotoiot/embed-iot-platform/blob/master/examples/mqtt/pressure_measurer/README.md).

Una vez que compilas y descargas el código a la placa, en la terminal serie se va a mostrar una salida similar a la siguiente:

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

Si estás suscripto a todos los topics desde el cliente web (`#`) vas a poder visualizar los topics que envía la aplicación embebida, y si publicás el topic `DEVICE_ID/config` desde el cliente web con un valor entre 1000 y 10000 ms vas a poder cambiar el período en que el dispositivo publica las mediciones fake de presión.

Ahora que ya sabés como correr el ejemplo Pressure Measurer, podés probar cualquier otra aplicación MQTT dentro de la [sección de ejemplos](https://github.com/gotoiot/embed-iot-platform/blob/master/examples/mqtt). Los detalles de implementación los vas a encontrar dentro del README de cada una.

## Colaborar

¿Te gustó el proyecto? Si es así no dudes en apoyarlo con una estrella en Github desde [la home del proyecto](https://github.com/gotoiot/app-mqtt-connection), esto motiva mucho a seguir adelante con el desarrollo de código para la comunidad. Si estás interesado en recibir novedades cuando se hagan actualizaciones, podes suscribirte desde [este link](https://github.com/gotoiot/app-mqtt-connection/subscription).

Si te gustaría aplicar mejoras a este proyecto podes abrir un hilo de discusión en [este link](https://github.com/gotoiot/app-mqtt-connection/issues/new) para conversarlas y luego podrías enviarlas mediante un `pull request`. 

Finalmente podés compartir este proyecto para que más personas puedan utilizarlo y beneficiarse de esta gran comunidad del software libre.

## Licencia

[MIT](https://choosealicense.com/licenses/mit/)

![footer](doc/gotoiot-footer.png)