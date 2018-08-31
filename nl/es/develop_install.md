---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Instalación de un entorno de desarrollo
{: #installing-a-development-environment}

Siga estas instrucciones para obtener las herramientas de despliegue {{site.data.keyword.blockchainfull}} Platform: Develop para crear y probar redes empresariales. Para proporcionar flexibilidad y permitir el número máximo de escenarios de desarrollo, prueba y despliegue, las herramientas de desarrollo se entregan como módulos npm, que se controlan desde la línea de mandatos.

Son necesarias distintas versiones de algunos componentes para utilizarlas con el Plan inicial y el Plan empresarial. Para utilizar el entorno de desarrollo con el Plan inicial, los módulos deben instalarse en la versión `0.19.x`. Para utilizar el entorno de desarrollo con el Plan empresarial, los módulos deben instalarse en la versión `0.16.x`

## Antes de empezar

Asegúrese de instalar los siguientes requisitos previos:

- Ubuntu Linux 14.04 / 16.04 LTS (ambos de 64 bits) o Mac OS 10.12
- Node v8.9 o superior (no se da soporte a la v9)
- npm v5.x
- git v2.9.x o superior
- Python v2.7.x
- El editor de código que prefiera, como por ejemplo VSCode.

Si está utilizando el Plan empresarial, instale los siguientes requisitos previos adicionales:

- Docker Engine v17.03 o superior
- Docker-Compose v1.8 o superior

Si utiliza VSCode, dispone de una extensión de VSCode de Hyperledger Composer [aquí](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client).

## Paso uno: Instalar las herramientas de línea de mandatos

Las herramientas de línea de mandatos incluyen **composer-cli**, el módulo principal de línea de mandatos de {{site.data.keyword.blockchainfull_notm}} Platform: Develop; **generator-hyperledger-composer**, **composer-rest-server** y **Yeoman**; estos módulos se utilizan para generar redes empresariales, servidores REST adaptados y aplicaciones Angular.

1. Para utilizar con el Plan inicial, instale **composer-cli** con el siguiente mandato:

    ```
    npm install -g composer-cli@0.19.x
    ```
    {:codeblock}

    Para utilizar con el Plan empresarial, instale **composer-cli** con el siguiente mandato:

    ```
    npm install -g composer-cli@0.16.x
    ```
    {:codeblock}

2. Para utilizar con el Plan inicial, instale **composer-rest-server** con el siguiente mandato:

    ```
    npm install -g composer-rest-server@0.19.x
    ```
    {:codeblock}

    Para utilizar con el Plan empresarial, instale **composer-rest-server** con el siguiente mandato:

    ```
    npm install -g composer-rest-server@0.16.x
    ```
    {:codeblock}

    Utilice el módulo **composer-rest-server** para crear un servidor REST en su máquina para exponer sus redes empresariales como API RESTful.

3. Para utilizar con el Plan inicial, instale **generator-hyperledger-composer** con el siguiente mandato:

    ```
    npm install -g generator-hyperledger-composer@0.19.x
    ```
    {:codeblock}

    Para utilizar con el Plan empresarial, instale **generator-hyperledger-composer** con el siguiente mandato:

    ```
    npm install -g generator-hyperledger-composer@0.16.x
    ```
    {:codeblock}

    Utilice **generator-hyperledger-composer** para generar el esqueleto de estructuras de red empresarial, modelos y una aplicación Angular.

4. Yeoman es una herramienta para generar las aplicaciones que `generator-hyperledger-composer` utiliza:

    ```
    npm install -g yo
    ```
    {:codeblock}

## Paso dos: Instalar un recinto de pruebas local

El recinto de pruebas de Hyperledger Composer es una interfaz de usuario que se puede conectar a un {{site.data.keyword.blockchain}} real o se puede utilizar como entorno simulado para probar una red empresarial. Para utilizar con el Plan inicial, instale el recinto de pruebas con el siguiente mandato:

```
npm install -g composer-playground@0.19.x
```
{:codeblock}


Para utilizar con el Plan empresarial, instale el recinto de pruebas con el siguiente mandato:


```
npm install -g composer-playground@0.16.x
```
{:codeblock}

## Opcional: Configurar el IDE

Puede utilizar el recinto de pruebas para desarrollar, editar y probar redes empresariales. Sin embargo, si desea desarrollar una red empresarial en un IDE, dispone de una extensión de VSCode, que añade funciones de resaltado de sintaxis al lenguaje de modelado Hyperledger Composer.

1. Instale VSCode desde este URL: [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. Abra VSCode, vaya a Extensiones y busque e instale la extensión de Hyperledger Composer.


## Solo Plan empresarial: Instale un Hyperledger Fabric local y conecte al mismo con el recinto de pruebas

Si despliega una instancia de un Hyperledger Fabric local, puede probar de forma exhaustiva las reglas de control y las redes empresariales. Asegúrese de que ha iniciado Docker antes de seguir estos pasos.

1. Cree el directorio `fabric-tools` con el siguiente mandato:

   ```
   mkdir ~/fabric-tools && cd ~/fabric-tools
   ```
   {:codeblock}

En el directorio que elija, por ejemplo `~/fabric-tools`, obtenga el archivo `.tar.gz` que contiene las herramientas para instalar Hyperledger Fabric:

2. Descargue el archivo `tar.gz` que contiene las herramientas para instalar Hyperledger Fabric en un entorno de Docker.

   ```
   curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.tar.gz
        tar -xvf fabric-dev-servers.tar.gz
   ```
   {:codeblock}

    También está disponible un archivo `.zip`. Sustituya el archivo `.tar.gz` por `fabric-dev-servers.zip` y el mandato `tar -xvf` por un mandato `unzip` en el fragmento de código anterior.

3. Utilice los mandatos siguientes para descargar un entorno de ejecución local de Hyperledger Fabric v1:

   ```
   cd ~/fabric-tools
   export FABRIC_VERSION=hlfv1
   ./downloadFabric.sh
   ```
   {:codeblock}

   El directorio `fabric-tools` contiene un conjunto de scripts que controlan la instancia descargada de Hyperledger Fabric. Debe ejecutar el mandato `./createPeerAdminCard.sh` para generar una tarjeta de red empresarial, que puede utilizar para desplegar la red empresarial en el Docker que contiene la instancia de Hyperledger Fabric.

4. Ejecute los mandatos siguientes para iniciar la instancia de Hyperledger Fabric v1:

   ```
   cd ~/fabric-tools
   export FABRIC_VERSION=hlfv1
   ./startFabric.sh
   ./createPeerAdminCard.sh
   ```
   {:codeblock}

   Para empezar a desarrollar una red empresarial, utilice el recinto de pruebas de Hyperledger Composer o el IDE.

5. Para iniciar el recinto de pruebas, ejecute el mandato siguiente:

    ```
    composer-playground
    ```
    {:codeblock}

    El recinto de pruebas se abre en el siguiente URL: http://localhost:8080/login. Se puede utilizar la tarjeta **PeerAdmin@hlfv1** creada en el paso anterior para desplegar una red empresarial.


### Inicio y detención de Hyperledger Fabric

Hay un conjunto de scripts en el directorio `fabric-tools` que controlan la instancia de Hyperledger Fabric. Puede detener e iniciar el entorno de ejecución con `~/fabric-tools/stopFabric.sh` y `~/fabric-tools/startFabric.sh`.

`~/fabric-tools/teardownFabric.sh` requiere que cree una nueva tarjeta PeerAdmin la siguiente vez que se inicie el entorno.
