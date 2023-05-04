# TheHive-Cortex-MISP
Instalación de las plataformas TheHive, Cortex y MISP.
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
- TheHive: [a link](http://localhost:9000)
- TheHive: [a link](http://localhost:9001)
- TheHive: [a link](http://localhost:9500)

Los puertos a los que se accede a las plataformas son las indicadas anteriormente dado que así se encuentran definidas en el ```docker-compose.yml```. Si se quisieran cambiar los puertos por los que se acceden, tan sólo habría que cambiarlos en el propio archivo.
