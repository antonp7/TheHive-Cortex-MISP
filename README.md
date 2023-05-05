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
Para poder definir los contenedores que usaremos, tendremos que ejecutar el siguiente comando en la misma carpeta donde está situado el archivo docker-compose.yml:
```console
 docker-compose --env-file .env up -d
```
Con el comando anterior estaremos indicando el archivo de environment con la opción ```--env-file``` y ejecutando cada uno de los contenedores en segundo plano con la opción ```-d```

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

## Integración Cortex con TheHive

## Integración MISP con TheHive

## Webhooks en TheHive
