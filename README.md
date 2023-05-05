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

* [Webhooks en TheHive](#webhooks-en-thehive)


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

Aparte, de los contenedores propios de las herramientras de TheHive, Cortex y MISP, se instalará la base de datos ElasticSearch, donde se guarde la información almacenada tanto en TheHive como en Cortex (usuarios, organizaciones, casos, observables creados, etc.). Por otro lado, MISP necesita de su propia base de datos de MySQL y de Redis para su funcionamiento, por lo que es también necesario la instalación de dichos contenedores.

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

<p align="center"> <img src="https://user-images.githubusercontent.com/45532292/236443652-a37472d9-fd95-41b6-a430-d8d5b9f2c6e2.png"> </p>

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



## Webhooks en TheHive
