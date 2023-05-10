# TheHive-Cortex-MISP
Instalación de las plataformas TheHive, Cortex y MISP.

## Índice

* [Introducción](#introducción)

* [Requisitos Hardware](#requisitos-hardware)

* [Contenedores a instalar](#contenedores-a-instalar)

* [Instalación de los contenedores](#instalación-de-los-contenedores)

* [Primera ejecución](#primera-ejecución)

* [Integración Cortex con TheHive](#integración-cortex-con-thehive)

* [Integración MISP con TheHive](#integración-misp-con-thehive)

* [Nginx Proxy Manager](#nginx-proxy-manager)
 


## Introducción
**TheHive** se trata de una plataforma de respuesta a incidentes de seguridad (SIRP) de código abierto, escalable y colaborativa que está diseñada para ayudar en la gestión y el análisis de incidentes de seguridad. Está principalmente desarrollado principalmente para CSIRT y SOC, agiliza y mejora el proceso de manejo de incidentes al proporcionar una plataforma centralizada para la gestión de casos, la asignación de tareas y la colaboración en tiempo real. 

TheHive se encuentra estrechamente relacionado con la herramienta de código libre **Cortex**. Cortex se trata de un una potente herramienta que permite analizar los observables que se han recopilado usando decenas de analizadores que se pone a disposición del usuario o mismo dar respuesta a dichas amenazas que se puedan producir.

Por otro lado, la herramienta de código libre **MISP** permite crear eventos que permite exportarlos a TheHive. Asimismo, desde TheHive también podemos exportar aquellos casos que se hayan creado a MISP.

![explicacion-thehive](https://user-images.githubusercontent.com/45532292/236187760-26965725-eee1-4beb-b9d5-801f6d3175e8.png)

Se instalarán estas tres herramientas usando un docker-compose por lo que para ello, se necesitará tener instalar anteriormente Docker en el propio equipo.


## Requisitos Hardware
Para la instalación de todas estas herramientas se necesitan como mínimo los siguientes requisitos hardware:
- 10GB de RAM
- 50GB de almacenamiento interno
- 4 vCPU


## Contenedores a instalar
La instalación de los contenedores se realizará mediante docker compose, en un archivo que se ha denominado como ```docker-compose.yml``` en este repositorio. En dicho archivo, se encuentran todos los contenedores que se van a instalar. Concretamente son los siguientes:
- TheHive
- Cortex
- MISP
- ElasticSearch
- Redis
- MySQL database
- Nginx Proxy Manager

Aparte, de los contenedores propios de las herramientras de TheHive, Cortex y MISP, se instalará la base de datos ElasticSearch, donde se guarde la información almacenada tanto en TheHive como en Cortex (usuarios, organizaciones, casos, observables creados, etc.). Por otro lado, MISP necesita de su propia base de datos de MySQL y de Redis para su funcionamiento, por lo que es también necesario la instalación de dichos contenedores. Aparte de estos contenedores, se ha añadido uno adional, el Nginx Proxy Manager, que nos podrá servir para poder crear certificados SSL de una forma muy sencilla y nos permitirá acceder a las herramientas con una conexión segura usando el protocolo HTTPS.



## Instalación de los contenedores
Antes de poder realizar la instalación de los contenedores, deberemos de crear un fichero que contiene unas variables de entorno con el nombre de ```.env``` en el mismo directorio donde se encontrará el archivo de ```docker-compose.yml```. En el siguiente archivo se escribirá las siguientes líneas:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236417667-08ec41a2-c590-4913-b880-bea293a1f999.png"> </p>

Estas líneas nos permitirán indicar las credenciales por defecto de MISP, la URL a la que se accederá a MISP, la zona horaria en donde nos encontramos para que la hora en MISP aparezca correctamente, y finalmente la línea de ```CORTEX_KEY=""``` será usada para [integración de Cortex con TheHive](#integración-cortex-con-thehive).

Para poder definir los contenedores que usaremos, tendremos que ejecutar el siguiente comando en la misma carpeta donde está situado el archivo docker-compose.yml:
```console
 docker-compose --env-file .env up -d
```
Con el comando anterior estaremos indicando el archivo con unas variables de entorno con la opción ```--env-file``` y ejecutando cada uno de los contenedores en segundo plano con la opción ```-d```

Para comprobar que todos los contenedores se definieron correctamnete y están ejecutandose sin fallos, se puede usar el siguiente commando:
```console
 docker ps -a
```
Para poder acceder a las distintas aplicaciones se tienen que abrir desde el navegador y se accede usando las siguientes URL:
- TheHive: [http://localhost:9000](http://localhost:9000)
- Cortex: [http://localhost:9001](http://localhost:9001)
- MISP: [http://localhost:9500](http://localhost:9500)
- Nginx Proxy Manager: [http://localhost:81](http://localhost:81)

Los puertos a los que se accede a las plataformas son las indicadas anteriormente dado que así se encuentran definidas en el ```docker-compose.yml```. Si se quisieran cambiar los puertos por los que se acceden, tan sólo habría que cambiarlos en el propio archivo.


## Primera ejecución
La primera vez que ejecutamos cada una de las aplicaciones, éstas deberemos de configurar una serie de parámetros.

### TheHive
En el caso de TheHive, nos pedirá que introduzcamos nuestras credenciales. 
![image](https://user-images.githubusercontent.com/45532292/236207112-00843d1e-69a1-4efb-886e-cf77420d1159.png)
Para la primer ejecuación usaremos las siguientes credenciales:
- login: ```admin@thehive.local```
- password: ```secret```

Una vez dentro, podremos poner añadir una nueva organización clicando en el botón de ```New Organisation``` situado en la parte superior de la pantalla. 
![image](https://user-images.githubusercontent.com/45532292/236208807-cf8b0bd0-9371-4c7f-a173-0f6d5d615b78.png)

Si queremos añadir un usuario a una organización, tan sólo hay que entrar en la organización donde queremos añadir el usuario y clicar en el botón ```Create new user```. Aparecerá una pantalla donde podremos indicar los datos del usuario que se quieran y los permisos que tendrá dentro de la Organización (Adminsitrador, Analista o Solo lectura):
![image](https://user-images.githubusercontent.com/45532292/236209465-27a0b70a-d600-4e79-8a7e-237ac4e2c196.png)

Cuando se vuelva a loguear con una cuenta de un usuario que ha sido creado, se podrá acceder a las distintas funciones de la aplicación, entre las que se incluye la creación de un nuevo caso, ver las tareas pendientes, consultar las alertas o ver un dashborard de todo lo que está sucendiendo en ese momento, como se puede ver en la siguiente imagen:
![image](https://user-images.githubusercontent.com/45532292/236211850-c711fc9d-c0ae-4d09-8f5f-de879441e1ca.png)

Para crear un nuevo caso, sólo será necesario clicar en la sección de ```New Case``` en la barra superior y se mostrará una pantalla donde deberemos cubrir una serie de campos indicando el titulo del caso, la fecha, el TLP, el PAP, la importancia del caso, lags que queremos indicar para clasificar el caso, una descripción o las tareas que se van a asignar:

![image](https://github.com/antonperezv/TheHive-Cortex-MISP/assets/45532292/bcffa110-aaf1-4805-a836-3bd05095010f)


### Cortex

Para el caso de Cortex, se nos pedirá actualizar la base de datos. 
![image](https://user-images.githubusercontent.com/45532292/236207548-19bf4a35-5c82-4d46-91e7-a9b9c88a9c8e.png)

Tan sólo tendremos que clicar en el botón de ```Update Database``` para acceder a la ventana donde podremos poner las credenciales que queramos para el caso del usuario con permisos de administrador.
![image](https://user-images.githubusercontent.com/45532292/236208046-7d1d1d87-fcc2-4cd5-9ae3-9cc4872d742e.png)

Si intentamos entrar a la aplicación con esas credenciales nuevas credas, se nos abrirá la pantalla de inicio donde podremos añadir nuevas organizaciones clicando en el botón que se encuentra en la zona superior izquierda ```Add Organization```:
![image](https://user-images.githubusercontent.com/45532292/236399386-3b405ab5-08a8-48cc-85c5-1d0a0b7cd351.png)

Y añadir usuarios en la organización creada, de la misma forma que en TheHive, pulsando en el botón ```Add user``` donde se puede indicar también los permisos de dicho usuario:
![image](https://user-images.githubusercontent.com/45532292/236399620-4acd6bbf-ce09-4ab2-8b99-13bcd2e1103b.png)

Con los nuevos usuarios creados, si intentamos acceder a la plataforma con sus credenciales, nos aparecerá una pantalla donde podremos ver un historial de lo que está sucendiendo en todo momento en Cortex y añadir Analizadores o Responders:
![image](https://user-images.githubusercontent.com/45532292/236400291-8dc461cd-9ab3-44da-a45a-f74da2104888.png)


Para añadir Analizadores o Responders, es necesario acceder a la pestaña de ```Organization``` situada en la parte superior derecha. Dentro de dicha pestaña se puede seleccionar el analizador (dentro de la sección de ```Analyzers```) o un Responder (en la sección de ```Responders```) que se quiera usar pulsando en el botón de ```Enable```, situado en la zona derecha de cada uno ed los Analizadores/Responders:
![image](https://user-images.githubusercontent.com/45532292/236400894-208c3f43-03db-4804-992f-343f22640d11.png)

Dentro de cada uno podremos indicar los distintos parámetros para configurarlo como se quiera:
![image](https://user-images.githubusercontent.com/45532292/236401064-b46d4600-be85-42c6-84df-69be0f4e2ad1.png)


### MISP
En MISP, la primera vez que se abra, nos pedirá realizar una configuración inicial.
![image](https://user-images.githubusercontent.com/45532292/236410877-97a4d8d4-7c50-4260-9e80-799e822098d1.png)

Para ello, tendremos que indicar las credenciales que se encuentran por defecto que son las siguientes:
- Email: ```admin@admin.test```
- password: ```admin```

Tras indicar las credenciales anteriores, se nos pedirá cambiar la contraseña anterior. Esta nueva contraseña deberá de ser de como mínimo 12 caracteres y contener una serie de símbolos especiales:
![image](https://user-images.githubusercontent.com/45532292/236411414-77b315aa-bb22-463c-af11-578c8c895b00.png)

Una vez actualizada la contraseña, se nos mostrará una ventana con un resumen de nuestro usuario actual y tendremos acceso a las funciones propias de la plataforma:
![image](https://user-images.githubusercontent.com/45532292/236411985-6ec30413-8875-47c4-b34a-9fa6eb23d8fc.png)

Podremos crear una nueva organización, clicando en la sección ```Add Organisations``` al desplegar la pestaña de ```Administration``` situada en la parte superior:
![image](https://user-images.githubusercontent.com/45532292/236412393-6fd9d462-4e31-4fe0-b4fc-15044936c119.png)

Y podremos añadir un nuevo usuario a dicha organización que se ha creado desde la sección de ```Add User``` al desplegar la pestaña de ```Administration```.
![image](https://user-images.githubusercontent.com/45532292/236412615-3c26fd8d-6c28-4cf2-88ca-a786c57d72a7.png)

Dentro de esta sección podremos indicar los distintos datos del usuarios que se quiere añadir, como a la organización a la que se quiere añadir o los permnisos que tiene.



## Integración Cortex con TheHive
Para la integración de Cortex con TheHive, es necesario la realización de una API Key de un usuario en Cortex. Para ello, desde la aplicación de Cortex, usando la cuenta de Admin, se necesitará acceder a la sección de Users y pulsar en el botón de ```Create API Key``` del usuario que se quiere integrar con TheHive

![image](https://user-images.githubusercontent.com/45532292/236424025-58f205fd-fec7-4dcc-ad7e-0d643e0ec139.png)

Tras crear una nueva API Key de dicho usuario, tendremos tres opciones disponibles para el apartado de API Key: Renovar la API Key(```Renew```), Eliminar dicha API Key (```Revoke```) y Mostrar en pantalla la API Key (```Reveal```):

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236424259-a87e5ea7-400c-4466-ab1f-78edba6e5ca9.png"> </p>

Si mostramos la API Key por pantalla nos saldrá de la siguiente forma:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236424715-f10eb3c2-37bf-4a7b-b39d-ada99703a640.png"> </p>

Será necesario copiar esa API Key y añadirla en la variable de entorno de ```CORTEX_KEY``` que tenemos definida en el fichero .env de la siguiente manera:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236425893-d18dd0f8-a532-499c-805b-266032098a19.png"> </p>


Para integrarlo por completo con TheHive, será necesario construir de nuevo la imagen de TheHive con el comando que se ha utilizado anteriormente:
```console
 docker-compose --env-file .env up -d
```
Podremos comprobar que la integración se ha realizado satsfactoriamente, accediendo a TheHive y clicando en el botón ```About``` situado en el menú donde se encuentra el nombre del usuario:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236426553-3d937352-308d-499b-b4c3-a4dca2e76d3a.png"> </p>

Nos saltará una pestaña que nos indicará que el módulo de CORTEX está en estado ```OK```:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236426846-94b11d6d-b459-4556-9054-f106f334d638.png"> </p>

También se puede consultar que dicha integración está correctamente configurada mirando los iconos situados en la zona inferior derecha de la página. Si el icono de Cortex está en verde, significa que está integrado de forma correcta:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236427053-8593e2a9-2fdc-4690-821b-5ca0fd274084.png"> </p>

La integración de Cortex con TheHive nos permite añadir Responders y Analizadores a TheHive. Para el caso de los Analizadores, deberemos de tener configurado al menos un Analizador en Cortex. Para usarlo, creamos un Observable primero en el caso donde se indique mediante el botón de ```Add observable```:
![image](https://user-images.githubusercontent.com/45532292/236430354-cb7103d8-da8c-4061-95d8-bd487c32bbc0.png)

Seleccionamos ese observable creado y pulsamos sobre la opción de ```Run analyzers```:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236431135-dda4fe22-43b4-471d-ab58-f0053b7be5eb.png"> </p>

Se abrirá una ventana para seleccionar el Analizador que queremos usar. Y pulsamos en ```Run selected analyzers```:
![image](https://user-images.githubusercontent.com/45532292/236431308-eaa981a8-2754-4ee0-8619-c73d45d1a824.png)

Podremos comprobar que ha funcionado correctamente desde la pestaña que aparece a la derecha, si el status se encuentra en modo ```Success```:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236431534-dd63ae08-0d74-46fb-a060-2f9d4c869049.png"> </p>

En Cortex, aparecerá el Observable con el resultado que se ha obtenido en la pestaña de ```Jobs History```. Se podrá comprobar más información de este Observable analizado pulsando sobre el botón de ```View```:

![image](https://user-images.githubusercontent.com/45532292/236432235-b5d211dc-2ca0-4a8f-92b6-a0bb1407db38.png)

Para el caso de los Responders, se puede usar dicha herramienta, de una forma similar que la de los Analizadores. Deberemos de crear primero un Observable, si no se ha creado uno anteriormente, seleccionar dicho Observable y pulsar sobre el botón de ```Responders```:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236433113-02a315f5-0c7c-471d-abf0-de3928605dc3.png"> </p>

Podremos seleccionar el Responder que queremos ejecutar, y si se realiza dicha acción, aparecerá el resultado de la ejecución del mismo de la misma manera que los Analizadores, dentro de la sección de ```Jobs History``` en Cortex.

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236433500-846bc6d7-7108-439b-a390-831006568612.png"> </p>



## Integración MISP con TheHive

Para la integración de MISP con TheHive, deberemos de tener al menos un usuario creado en MISP. Accederemos a los distintos usuarios que tenemos definidos en la sección de ```List Users``` al desplegar la pestaña de ```Administration```:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236435555-36b4de80-7a42-405d-bb25-75a0f222d8b1.png"> </p>

Y entramos en la información del usuario pulsando sobre el icono del ojo situado a la derecha de la fila del usuario:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236436397-0ed17675-d521-42a0-b4ad-066310f0bdd8.png"> </p>

En la zona inferior, dentro de la sección de ```Auth keys```, crearemos una key pulsando sobre el botón de ```Add authentification key```:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236436758-8e053a75-d44a-4656-9376-e50f085dcd31.png"> </p>
 
Una vez dentro, se nos mostrará en pantalla al Auth Key creada para ese usuario:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236440893-c95526b3-c79c-448f-9253-da13321e6348.png"> </p>

IMPORTANTE: Se necesita apuntar esa Auth Key en algún lado antes de cerrar dicha pestaña, ya que de contrario no podremos consultar de nuevo toda la Auth Key más adelante.

Una vez, apuntada dicha clave, tendremos que ir al archivo de ```application.conf```, situado dentro de la carpeta de ```thehive``` de este repositorio. Dentro de ese archivo pondremos la clave, en la línea donde se indica ```key=``` dentro de la sección de ```auth```:

<p align="center"> <img src="https://github.com/antonperezv/TheHive-Cortex-MISP/assets/45532292/1d9292df-c55e-48fc-946f-ddce30404e34"> </p>

Para poder realizar la integración con TheHive, se necesitará parar el contedor de TheHive. Esto se puede hacer con el siguiente comando:

```console
docker-compose stop
```
Y se ejecutará de nuevo los contenedores con el comando

```console
 docker-compose --env-file .env up -d
```
Asi, una vez que accedamos a TheHive, podemos comprobar en la sección de ```About```, que la conexión con MISP se encuentra en estado ```OK```:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236456563-4d98e40e-e64f-4c90-a506-1d165929c02d.png"> </p>

También se puede comprobar, como en el caso de Cortex, mediante los iconos situados en la zona inferior derecha. Si se encuentra el icono en verde, la conexión está establecida correctamente:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236456758-2d39797b-c064-40a6-86c0-6754c4d92b5d.png"> </p>

De esta forma, si integramos MISP con TheHive, desde TheHive se puede enviar casos que se han creado a MISP, clicando en el botón de ```Export``` situado en la parte superior cuando entramos en un caso para ver la información:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236457707-5ee98fae-4369-45e4-b80f-da4e91330c2d.png"> </p>

Y así podremos exportar el caso a MISP pulsando en el botón de ```Export``` que se aparecerá en la pantalla:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236457953-0505c706-c884-4cb9-9456-4777dc1d5264.png"> </p>

Al hacer esto, aparecerá en MISP el caso que se ha exportado en la sección de ```List Events```:

![image](https://user-images.githubusercontent.com/45532292/236458180-64199ca2-b2d6-482c-9a3e-0ecd3b52e07c.png)

Para pasar un evento de MISP a TheHive, crearemos un evento en MISP, entrando en la seccióin de ```Add Event``` dentro de la opción de ```Event Actions``` del menú superior de la aplicación:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236458634-6d257331-3b05-4199-8593-96ca70de1373.png"> </p>

Y le daremos a ```Submit``` para crear el evento:

![image](https://user-images.githubusercontent.com/45532292/236458465-573f40e1-5fb7-46fb-a225-33f8d5554de7.png)

Se nos mostrará en pantalla la información del nuevo Evento. Será necesario crear un atributo, mediante el símbolo de ```+``` que aparece situado en la zona inferior de la página:

![image](https://user-images.githubusercontent.com/45532292/236458997-a67ad83b-7ba7-46d6-bb9f-079ec06f0cc7.png)

Y será necesario pubicar el evento, pulsando en la opción de ```Publish Event``` en la barra de opciones situada a la izquierda:

![image](https://user-images.githubusercontent.com/45532292/236460577-67a7f957-5ee7-4f1b-8754-943ed7f1e4d5.png)

Luego de publicar el evento en MISP, aparecerá en TheHive al pasar el período de tiempo que se le indique en el atributo de ```interval``` del archivo de ```application.conf```. Una vez pasado ese tiempo, el evento llegará a TheHive y se podrá consultar en la pestaña de ```Alerts```:

![image](https://github.com/antonperezv/TheHive-Cortex-MISP/assets/45532292/6287c505-0149-49db-b680-1cc56a41e7ec)

Como se puede ver, para cada una de las alertas se ofrece una serie de opciones entre las que se encuentra ejecutar un Responder si se clica en la última opción disponible que se encuentra a la derecha de cada una de las alertas.

## Webhooks en TheHive

TheHive tiene una opción para poder notificar de todos los eventos que se produzcan y enviarlos a un enlace de Webhook. Para ello es necesario modificar el archivo de ```application.conf```, situado dentro de la carpeta de ```thehive``` de este repositorio. Para ello, será tendrá que indicar estas líneas en dicho archivo:

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236461187-dfaa1320-77e7-4b4b-b5d1-c8ab1ce2a96b.png"> </p>

Con ```name: ``` se indicará el nombre del Webhook que se quiera especificar y con la línea ```url:``` se indicará la URL del enlace de Webhook en el que se quiere enviar las notificaciones de todos los eventos que pasa en TheHive.

Para poder activar el Webhook, será necesario indicar las siguientes líneas en la terminal del ordenador:
```console
read -p 'Enter the URL of TheHive: ' thehive_url
```
```console
read -p 'Enter your login: ' thehive_user
```
```console
read -s -p 'Enter your password: ' thehive_password
```
```console
curl -XPUT -u$thehive_user:$thehive_password -H 'Content-type: application/json' $thehive_url/api/config/organisation/notification -d '
{
  "value": [
    {
      "delegate": false,
      "trigger": { "name": "AnyEvent"},
      "notifier": { "name": "webhook", "endpoint": "namewebhook" }
    }
  ]
}'
```
Se puede modificar que eventos se quiere notificar en la línea de ```trigger```. Para este caso, se notificará todos los eventos dado que está indicado con la opción de ```AnyEvent```. 

La notificación de cada uno de los eventos llegará al Webhook indicado una vez que se active con los comandos anteriores. El formato con el que llega dicha notificación, es un JSON como el de la siguiente imagen:

![formato-Webhook-recibido](https://github.com/antonperezv/TheHive-Cortex-MISP/assets/45532292/b56ee1e3-64fd-4dc7-8fd9-2ed051d6f892)


## Nginx Proxy Manager

Se trata de un contenedor adicional que se ha incluido en este proyecto y que se puede usar para crear certificados SSL de una forma fácil usando LetsEncrypt para ello. Con esto, se podrá usarlo para acceder a las aplicaciones de TheHive, Cortex y MISP usando una conexión segura mediante el protocolo HTTPS.

Para acceder a este contenedor, será necesario entrar a la URL indicada anteriormente ([http://localhost:81](http://localhost:81)). Cuando accedamos por primera vez, nos saltará una ventana para poner unas credenciales:
![image](https://github.com/antonperezv/TheHive-Cortex-MISP/assets/45532292/c6e21a4f-e69e-4408-9549-3fcda3f89ab1)

Las credenciales por defecto son las siguientes:
- Email address: ```admin@example.com```
- Password: ```changeme```

Una vez puestos esos datos, se nos pedirá cambiar tanto el correo electrónico como la contraseña:

![image](https://github.com/antonperezv/TheHive-Cortex-MISP/assets/45532292/fbf8e51a-707f-4006-8ee4-2d34d25f9344)

![image](https://github.com/antonperezv/TheHive-Cortex-MISP/assets/45532292/814e949e-c96f-4a7b-9bf4-40b37f6e8dbd)

Y se nos actualizarán los datos para que la próxima vez que accedamos a la herramienta, podamos usar esas nuevas credenciales que se han establecido. Tras realizar estos cambios, podremos ver en la pestaña de ```Dashboard```, un resumen de todos los Hosts que tenemos configurados:

![image](https://github.com/antonperezv/TheHive-Cortex-MISP/assets/45532292/b40925bf-b46d-4e9f-9b93-da90df8a5591)

Podremos configurar un nuevo Proxy Host, clicando en la sección de ```Hosts``` en la barra superior y luego en ```Proxy Hosts```. Para añadir uno nuevo, tendremos que pulsar el botón de ```Add Proxy Host```, y ya podremos indicar el nombre del dominio que le queremos poner a la aplicación y a la URL al que apunta. También se nos da la posibilidad de habilitar opciones adicionales como la posibilidad de bloquear exploits conocidos:

<p align="center"> <img src="https://github.com/antonperezv/TheHive-Cortex-MISP/assets/45532292/146b6505-c70a-4169-a2cf-4c5cce1ad7e8)"> </p>

Para pedir un certificado SSL, pulsaremos sobre la pestaña de ```SSL``` y al desplegar el menú de ```SSL Certificate```, podremos pedir un certificado SSL generado con Let's Encrypt:

<p align="center"> <img src="https://github.com/antonperezv/TheHive-Cortex-MISP/assets/45532292/1b0f6d88-45b6-405e-887e-48058ad1738b)"> </p>

Con esta última opción, podremos configurar una serie de parámetros, tales como forzar SSL, que soporte HTTP/2 o usar un DNS Challenge:

<p align="center"> <img src="https://github.com/antonperezv/TheHive-Cortex-MISP/assets/45532292/975a67c6-263b-45dc-b097-84a77fe3bfbb)"> </p>



