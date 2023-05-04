# TheHive-Cortex-MISP
Instalación de las plataformas TheHive, Cortex y MISP.
## Introducción
**TheHive** se trata de una plataforma de respuesta a incidentes de seguridad (SIRP) de código abierto, escalable y colaborativa que está diseñada para ayudar en la gestión y el análisis de incidentes de seguridad. Está principalmente desarrollado principalmente para CSIRT y SOC, agiliza y mejora el proceso de manejo de incidentes al proporcionar una plataforma centralizada para la gestión de casos, la asignación de tareas y la colaboración en tiempo real. 
TheHive se encuentra estrechamente relacionado con la herramienta de código libre **Cortex**. Cortex se trata de un una potente herramienta que permite analizar los observables que se han recopilado usando decenas de analizadores que se pone a disposición del usuario o mismo dar respuesta a dichas amenazas que se puedan producir.
Por otro lado, la herramienta de código libre **MISP** permite crear eventos que permite exportarlos a TheHive. Asimismo, desde TheHive también podemos exportar aquellos casos que se hayan creado a MISP.

Se instalarán estas tres herramientas usando un docker-compose.

## Requisitos Hardware
Para la instalación de todas estas herramientas se necesitan como mínimo los siguientes requisitos:
- 10GB de RAM
- 50GB de almacenamiento interno
