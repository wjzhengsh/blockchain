---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Instalación de un entorno de desarrollo de red empresarial
{: #installing-a-development-environment}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Siga estas instrucciones para obtener las herramientas de despliegue {{site.data.keyword.blockchainfull}} Platform: Develop para crear y probar redes empresariales. Para proporcionar flexibilidad y permitir el número máximo de escenarios de desarrollo, prueba y despliegue, las herramientas de desarrollo se entregan como módulos npm, que se controlan desde la línea de mandatos.

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

Las herramientas de línea de mandatos incluyen **composer-cli**, el módulo principal de línea de mandatos de {{site.data.keyword.blockchainfull_notm}} Platform: Develop; **generator-hyperledger-composer**, **composer-rest-server** y **Yeoman**; estos módulos se utilizan para generar redes empresariales, servidores REST adaptados y aplicaciones Angular. La versión que instale dependerá de la versión de Hyperledger Fabric que ejecute su Plan inicial o Plan empresarial. Puede encontrar la versión de Fabric abriendo la [ventana Preferencias de red](/docs/services/blockchain/v10_dashboard.html#network-preferences) en el supervisor de red.

1. Para su uso con nuevas instancias del Plan inicial que ejecutan Hyperledger Fabric v1.2.1, instale
**composer-cli** utilizando el mandato siguiente:

    ```
    npm install -g composer-cli@0.20.x
    ```
    {:codeblock}

  Para su uso con instancias del Plan empresarial y el Plan inicial que ejecutan Hyperledger Fabric v1.1, instale **composer-cli** con el mandato siguiente:

    ```
    npm install -g composer-cli@0.19.x
    ```
    {:codeblock}

2. Para su uso con nuevas instancias del Plan inicial que ejecutan Hyperledger Fabric v1.2.1, instale
**composer-rest-server** utilizando el mandato siguiente:

    ```
    npm install -g composer-rest-server@0.20.x
    ```
    {:codeblock}

  Para su uso con instancias del Plan empresarial y el Plan inicial que ejecutan Hyperledger Fabric v1.1, instale **composer-rest-server** con el mandato siguiente:

    ```
    npm install -g composer-rest-server@0.19.x
    ```
    {:codeblock}

 Utilice el módulo **composer-rest-server** para crear un servidor REST en su máquina para exponer sus redes empresariales como API RESTful.

3. Para su uso con nuevas instancias del Plan inicial que ejecutan Hyperledger Fabric v1.2.1, instale
**generator-hyperledger-composer** utilizando el mandato siguiente:

    ```
    npm install -g generator-hyperledger-composer@0.20.x
    ```
    {:codeblock}

  Para su uso con instancias del Plan empresarial y el Plan inicial que ejecutan Hyperledger Fabric v1.1, instale **generator-hyperledger-composer** con el mandato siguiente:

    ```
    npm install -g generator-hyperledger-composer@0.19.x
    ```
    {:codeblock}

    Utilice **generator-hyperledger-composer** para generar el esqueleto de estructuras de red empresarial, modelos y una aplicación Angular.

4. Yeoman es una herramienta para generar las aplicaciones que `generator-hyperledger-composer` utiliza:

    ```
    npm install -g yo
    ```
    {:codeblock}

## Paso dos: Instalar un recinto de pruebas local

El recinto de pruebas de Hyperledger Composer es una interfaz de usuario que se puede utilizar como entorno simulado para probar una red empresarial. Instale el recinto de pruebas utilizando el mandato siguiente:

```
npm install -g composer-playground@0.20.x
```
{:codeblock}


## Opcional: Configurar el IDE

Puede utilizar el recinto de pruebas para desarrollar, editar y probar redes empresariales. Sin embargo, si desea desarrollar una red empresarial en un IDE, dispone de una extensión de VSCode, que añade funciones de resaltado de sintaxis al lenguaje de modelado Hyperledger Composer.

1. Instale VSCode desde este URL: [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. Abra VSCode, vaya a Extensiones y busque e instale la extensión de Hyperledger Composer.


### Inicio y detención de Hyperledger Fabric

Hay un conjunto de scripts en el directorio `fabric-tools` que controlan la instancia de Hyperledger Fabric. Puede detener e iniciar el entorno de ejecución con `~/fabric-tools/stopFabric.sh` y `~/fabric-tools/startFabric.sh`.

`~/fabric-tools/teardownFabric.sh` requiere que cree una nueva tarjeta PeerAdmin la siguiente vez que se inicie el entorno.
