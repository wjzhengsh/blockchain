---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Desarrollo de redes empresariales
{: #develop-the-network}

**IBM recomienda el uso de Hyperledger Composer únicamente para demostraciones y pruebas de concepto. IBM no proporciona soporte para redes que utilicen Hyperledger Composer en producción, incluyendo la CLI de Composer, las API de JavaScript, el servidor REST y Web Playground.**

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


El desarrollo de soluciones de blockchain mediante {{site.data.keyword.blockchainfull}} Platform aprovecha el conjunto de herramientas de desarrollo de código abierto de Hyperledger Composer. Hyperledger Composer utiliza un lenguaje de modelado adaptado, que se combina con transacciones JavaScript y reglas de control de acceso para modelar una red empresarial de blockchain por completo _antes_ de desplegar nada en una red blockchain real.
{:shortdesc}

**Nota:** el Plan inicial proporciona aplicaciones de ejemplo que puede probar y de las que puede aprender. Puede ejecutar aplicaciones de ejemplo en el Plan inicial antes de desarrollar su red empresarial personalizada. Para obtener más información, consulte [Acerca del Plan inicial](starter_plan.html).

Este documento le guía por el proceso de desarrollo de una solución {{site.data.keyword.blockchain}} que empieza por crear y modelar una red empresarial y la despliega en una red blockchain en ejecución.

En los siguientes pasos se describe el desarrollo básico de una solución {{site.data.keyword.blockchain}}  alojada en la plataforma {{site.data.keyword.blockchainfull_notm}}:

1. Cree la estructura de datos que contendrá la definición de la red empresarial. La red empresarial se define en términos de activos, participantes y transacciones mediante el [lenguaje de modelado](https://hyperledger.github.io/composer/latest/reference/cto_language).

2. Modele la red empresarial, definiendo las clases de activos, las clases de participantes, las clases de transacciones, las clases de sucesos y las reglas de control de acceso que se utilizarán después del despliegue.

3. Empaquete la red empresarial definida en un solo archivo que se pueda desplegar.

4. Despliegue la red empresarial empaquetada en la plataforma {{site.data.keyword.blockchainfull_notm}}. Cuando la red empresarial se haya desplegado, se puede generar un servidor REST que permita la integración con aplicaciones externas.

## Antes de empezar

Asegúrese de que tiene instaladas las [herramientas de desarrollo de {{site.data.keyword.blockchainfull_notm}}: Develop](./develop_install.html).

## Paso uno: Creación de la estructura de la red empresarial

Un concepto clave para desarrollar soluciones {{site.data.keyword.blockchain}} es la **definición de red empresarial (BND)**. La BND define el modelo de datos, la lógica de las transacciones y las reglas de control de acceso para su solución blockchain. Para crear una BND, debe crear en disco una estructura de proyecto adecuada.

El modo más fácil de empezar es utilizar el generador Yeoman para crear el esqueleto de la red empresarial. El generador Yeoman crea un directorio que contiene todos los componentes de una red empresarial, que se puede modificar para adaptarla a su caso de uso específico.

1. Cree un directorio nuevo para el proyecto y vaya al mismo en la línea de mandatos.

2. En el nuevo directorio, utilice Yeoman para crear el esqueleto de una red empresarial. El siguiente mandato requiere un nombre de red empresarial, una descripción, un nombre de autor, la dirección de correo electrónico del autor, una selección de licencia y un espacio de nombres:
    ```
    yo hyperledger-composer:businessnetwork
    ```
    {:codeblock}

3. Escriba `tutorial-network` como nombre de red y la información que desee para la descripción, el nombre del autor y la dirección de correo electrónico del autor.

4. Seleccione `Apache-2.0` como licencia.

5. Seleccione `org.acme.biznet` como espacio de nombres.

## Paso dos: Definición de la red empresarial

Una red empresarial se compone de activos, participantes, transacciones, reglas de control de acceso y, opcionalmente, sucesos y consultas. El esqueleto de la red empresarial que se crea en el paso uno incluye un archivo modelo (`.cto`), que contiene las definiciones de clase para todos los activos, participantes y transacciones en la red empresarial. El esqueleto de la red empresarial también contiene un documento de control de acceso (`permissions.acl`) con las reglas básicas de control de acceso, un archivo script (`logic.js`) que contiene funciones de procesador de transacciones y un archivo de paquete (`package.json`) que contiene los metadatos de la red empresarial.

### Modelado de activos, participantes y transacciones

El primer documento que se debe actualizar es el archivo modelo (`.cto`). El archivo de modelo se escribe utilizando el [lenguaje de modelado de Hyperledger Composer](https://hyperledger.github.io/composer/latest/reference/cto_language). El archivo modelo contiene las definiciones de cada clase de activo, transacción, participante y suceso. Extiende de forma implícita el modelo de sistema que se describe en la documentación del lenguaje de modelado.

1. Abra el archivo de modelo `org.acme.biznet.cto` en un editor de su elección. Se encuentra en la carpeta `models` de la red empresarial que ha creado en el último paso.

2. Sustituya el contenido por la información siguiente:

    ```
        /**
         * My commodity trading network
         */
        namespace org.acme.biznet
        asset Commodity identified by tradingSymbol {
            o String tradingSymbol
            o String description
            o String mainExchange
            o Double quantity
            --> Trader owner
        }
        participant Trader identified by tradeId {
            o String tradeId
            o String firstName
            o String lastName
        }
        transaction Trade {
            --> Commodity commodity
            --> Trader newOwner
        }
    ```
    {:codeblock}

3. Guarde los cambios en el archivo `org.acme.biznet.cto`.


### Adición de lógica de transacción JavaScript

En el archivo modelo, se ha definido una transacción `Trade` que especifica una relación entre un activo y un participante. El archivo de la función del procesador de transacciones contiene la lógica de JavaScript para ejecutar las transacciones definidas en el archivo modelo.

El objetivo de la transacción `Trade` es simplemente aceptar el identificador del activo `Commodity` que se comercializa y el identificador del participante `Trader` que se va a establecer como nuevo propietario.

1. Abra el archivo script `logic.js` en el directorio `lib`.

2. Sustituya el contenido por la información siguiente:

    ```
        /**
         * Track the trade of a commodity from one trader to another
         * @param {org.acme.biznet.Trade} trade - the trade to be processed
         * @transaction
         */
        function tradeCommodity(trade) {
            trade.commodity.owner = trade.newOwner;
            return getAssetRegistry('org.acme.biznet.Commodity')
                .then(function (assetRegistry) {
                    return assetRegistry.update(trade.commodity);
                });
        }
    ```
    {:codeblock}

3. Guarde los cambios en `logic.js`.

### Adición de control de acceso

El control de acceso es una parte clave de una red empresarial. Todas las redes empresariales deben tener un archivo de control de acceso que se denomina `permissions.acl`. Siempre que se invoca un proceso en una red empresarial desplegada, se comprueba la lista de control de acceso para garantizar que la identidad o el participante que realiza la invocación puede invocar la operación.

En esta guía de aprendizaje se configurará una ACL sencilla. Tenga en cuenta que este método no se debería desplegar en un entorno de producción.

1. Cree un archivo `permissions.acl` en el directorio `tutorial-network`.

2. Añada las siguientes reglas de control de acceso a `permissions.acl`:

    ```
        /**
         * Access control rules for tutorial-network
         */
        rule Default {
            description: "Allow all participants access to all resources"
            participant: "ANY"
            operation: ALL
            resource: "org.acme.biznet.*"
            action: ALLOW
        }

        rule SystemACL {
          description:  "System ACL to permit all access"
          participant: "ANY"
          operation: ALL
          resource: "org.hyperledger.composer.system.**"
          action: ALLOW
        }
    ```
    {:codeblock}

3. Guarde los cambios en `permissions.acl`.

## Paso tres: Empaquetado de la red empresarial

Una vez definida la red empresarial, se debe empaquetar en un archivo de archivado de la red empresarial (`.bna`) que se pueda desplegar.

1. Mediante la línea de mandatos, vaya al directorio `tutorial-network`.

2. Desde el directorio `tutorial-network`, ejecute el siguiente mandato:

    ```
    composer archive create -t dir -n .
    ```
    {:codeblock}

Después de ejecutar el mandato, se crea un archivo de archivado de red empresarial (`tutorial-network@0.0.1.bna`) en el directorio `tutorial-network`.

## Paso cuatro: Despliegue de la red empresarial y generación de un servidor REST

Despliegue la red empresarial en {{site.data.keyword.blockchainfull_notm}} Platform utilizando el [Plan inicial](./develop_starter.html) o el [Plan empresarial](./develop_enterprise.html).

Después de desplegar la red empresarial en la plataforma {{site.data.keyword.blockchainfull_notm}}, se puede generar un servidor REST adaptado. El servidor REST examina el contenido de la red empresarial y crea llamadas de API RESTful para los activos, las transacciones y los participantes de la red. Encontrará instrucciones para generar un servidor REST en la [documentación de Hyperledger Composer](https://hyperledger.github.io/composer/latest/integrating/getting-started-rest-api).
