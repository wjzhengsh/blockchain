---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Despliegue de una red empresarial en el Plan empresarial
{: #deploying-a-business-network}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Las herramientas del desarrollador de {{site.data.keyword.blockchainfull}} Platform: Develop le ayudan a crear una **Definición de red empresarial**, que se puede empaquetar en un archivo de red empresarial (`.bna`). El entorno del desarrollador le permite desplegar archivos `.bna` en un {{site.data.keyword.blockchain}} local o de nube para su desarrollo y compartición.

Esta guía aborda el siguiente paso del ciclo de vida de la red empresarial, es decir, activar la red empresarial desplegando el archivo `.bna` en el Plan empresarial de la plataforma {{site.data.keyword.blockchainfull_notm}}.

## Antes de empezar

Asegúrese de que ha instalado el entorno del desarrollador {{site.data.keyword.blockchainfull}}: Develop y de estar familiarizado con el desarrollo y despliegue de redes empresariales. Dispone de instrucciones sobre cómo escribir redes empresariales en la [documentación de Hyperledger Composer](https://hyperledger.github.io/composer/latest/business-network/business-network-index).

Necesita acceso a una instancia del Plan empresarial de la plataforma {{site.data.keyword.blockchainfull_notm}}. Para obtener más información sobre el Plan empresarial de la plataforma {{site.data.keyword.blockchainfull_notm}}, consulte la [visión general del Plan empresarial](./enterprise_plan.html).

## Paso uno: Crear un perfil de conexión para la plataforma {{site.data.keyword.blockchainfull_notm}}

1. Cree un directorio en el que almacenará los detalles de conexión, por ejemplo:

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv1
    ```
    {:codeblock}

    Cada perfil de conexión debe contener un archivo `connection.json`. Cree un directorio nuevo bajo `.composer-connection-profiles`, en este caso `bmx-hlfv1`. Este será el nombre del perfil que utilizará cuando trabaje con Hyperledger Composer y con la plataforma {{site.data.keyword.blockchainfull_notm}}.

    ```
    mkdir -p ~/.composer-connection-profiles/bmx-hlfv1
        cd ~/.composer-connection-profiles/bmx-hlfv1
    ```
    {:codeblock}

2. Ahora debería tener la siguiente estructura de directorios:

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv1
    ```
    {:codeblock}

    Cree un archivo en el directorio recién creado y llámelo `connection.json`. Puede utilizar la siguiente plantilla para el archivo `connection.json`:

    ```
        {
            "name": "bmx-hlfv1",
            "description": "A description for a V1 Profile",
            "type": "hlfv1",
            "orderers": [
                {
                    "url": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
                }
            ],
            "ca": {
                "url": "https://abc.4.secure.blockchain.ibm.com:98765",
                "name": "PeerOrg1CA"
            },
            "peers": [
                {
                    "requestURL": "grpcs://abcd.4.secure.blockchain.ibm.com:22222",
                    "eventURL": "grpcs://abcd.4.secure.blockchain.ibm.com:33333"
                }
            ],
            "keyValStore": "/Users/jeff/.composer-credentials-mychannel-hsbn",
            "channel": "mychannel",
            "mspID": "PeerOrg1",
            "globalCert": "-----BEGIN CERTIFICATE-----\r\n...LotsOfStuff\r\n-----END CERTIFICATE-----\r\n-----BEGIN CERTIFICATE-----\r\nMorestuff\r\n-----END CERTIFICATE-----\r\n",
            "timeout": 300
        }
    ```
    {:codeblock}

    Deberá llenar el archivo `connection.json` recién creado con los atributos que se proporcionan mediante el panel de control de la plataforma {{site.data.keyword.blockchainfull_notm}}. Desde el panel de control, seleccione **Visión general** y luego el botón **Perfil de conexión** para visualizar el punto final y la información de certificado para los miembros del canal.

## Paso dos: Adición de información del clasificador

1. Ahora podemos empezar a modificar la plantilla con la información que proporciona el perfil de conexión. Podría haber varios clasificadores en el perfil de conexión, pero solo se necesita uno para un archivo `connection.json`.

    Sustituya los valores url del clasificador de la plantilla por la información relevante del perfil de conexión en el formato siguiente:

    ```
    "url": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
    ```
    {:codeblock}

## Paso tres: Adición de información sobre la entidad emisora de certificados

1. Sustituya el valor ca del archivo `connection.json` por el **url** y el **caName** de las entradas de la sección **certificateAuthorities**.

## Paso cuatro: Adición de información de iguales

1. Se deben establecer los valores de **requestURL** y **eventURL** para cada igual. Sustituya el atributo **url** por el valor **url** que se encuentra en el perfil de conexión. Sustituya el atributo **eventURL** por el valor **eventUrl** que se encuentra en el perfil de conexión. Después de realizar los cambios, la sección peers del archivo `connection.json` debería tener el siguiente formato:

    ```
        "peers": [{
              "requestURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345",
              "eventURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
    ```
    {:codeblock}

## Paso cinco: Adición de información de keyValStore

1. Establezca el atributo **keyValStore** de modo que apunte al directorio adecuado. Cree un directorio para utilizarlo para **keyValStore**. Por ejemplo, un directorio nuevo bajo el directorio inicial llamado `.composer-credentials-mychannel`. Asegúrese de que el atributo **keyValStore** apunte al directorio recién creado en el siguiente formato:

    ```
    "keyValStore": "/Users/myUserId/.composer-credentials-mychannel",
    ```
    {:codeblock}

## Paso seis: Adición de información de canal

1. Sustituya el valor del canal en el archivo `connection.json` de modo que coincida con el nombre del canal que tiene previsto crear y en el que piensa desplegar su red empresarial.

## Paso siete: Adición de un mspID

El valor de **mspID** del archivo `connection.json` debe establecerse en el mspID de la organización. El perfil de conexión proporciona una lista de las organizaciones con sus valores mspid asociados. Debe utilizar el valor del atributo **mspid** de su organización.

## Paso ocho: Adición de globalCert
1. La plataforma {{site.data.keyword.blockchainfull_notm}} utiliza un certificado TLS común para clasificadores e iguales. Para cada clasificador e igual, hay un atributo **tlsCACerts**; todos contienen el mismo certificado. Sustituya el valor ficticio del archivo `connection.json` por el valor de **tlsCACerts**. Debe tener el siguiente formato:

    ```
    "globalCert": "-----BEGIN CERTIFICATE-----\r\.......
    ```
    {:codeblock}

## Paso nueve: Preparación de los iguales

**Nota**: este paso se **debe** realizar antes de crear el canal en el que se va a desplegar una red empresarial. Si este paso se realiza después de crear el canal, la red empresarial desplegada **no se iniciará**.

En el documento de perfil de conexión, bajo **certificateAuthorities**, hay un atributo **registrar** que contiene atributos correspondientes a **enrollId** y **enrollSecret** en el formato siguiente:

 ```
        "registrar": [
            {
                "affiliation": "org1",
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],
 ```
 {:codeblock}

1. Solicite los certificados con el mandato siguiente:

    ```
    composer identity request -p bmx-hlfv1 -i admin -s PA55W0RD12
    ```
    {:codeblock}

    Esto descarga tres archivos en el directorio `.identityCredentials` bajo el directorio de inicio. Los archivos que le interesan se basan en **enrollId**. Así, en el ejemplo anterior habrá dos archivos llamados **admin-pub.pem** y **admin-priv.pem**

3. Seleccione **Miembros** en el menú de navegación, seleccione la opción de menú **Certificados** y pulse el botón **Añadir certificado**.

4. Escriba un nombre exclusivo para este certificado en el campo **Nombre**; este nombre no puede incluir guiones.

5. Abra el archivo `admin-pub.pem` creado anteriormente, copie el contenido de este archivo en el campo **Key** y pulse el botón **Enviar**.

6. Utilice la interfaz de usuario para detener los iguales y luego inicie los iguales.

7. Ahora el certificado debería aparecer en la lista de certificados.

## Paso diez: Creación de un canal

1. Seleccione **Canales** en el menú de navegación del panel izquierdo y pulse el botón **Nuevo canal**.

2. Especifique un nombre de canal y una descripción opcional y pulse **Siguiente**. Tenga en cuenta que el nombre del canal debe coincidir con el nombre que especifique en el perfil de conexión para el atributo de canal.

3. Asigne los permisos necesarios y pulse **Siguiente**.

4. Seleccione la política del número de operadores que necesitan aceptar actualizaciones de canal y envíe la solicitud.

5. Esto le debería llevar a la sección **Notificaciones**, donde debería haber una nueva solicitud que revisar. Pulse el botón **Revisar solicitud**.

6. Pulse el botón **Aceptar** y volverá a la sección **Notificaciones**, donde puede ver que ahora la solicitud se puede enviar. Pulse el botón **Enviar solicitud** para abrir el diálogo de envío y pulse el botón **Enviar**. El nuevo canal se ha creado.

7. Seleccione **Canales** en el menú de navegación. El nuevo canal debería estar en la lista de canales y debería mostrar "No se han añadido iguales aún". Pulse el menú de acciones que hay junto al canal y seleccione **Unir iguales**. Marque los recuadros de selección que hay junto a los iguales que desea añadir y pulse **Añadir seleccionados**.

## Paso once: Importación de una nueva identidad para administrar la red empresarial

Cree una identidad en Composer utilizando los certificados solicitados con anterioridad. Esta nueva identidad tendrá autoridad para instalar un código de encadenamiento en los iguales que tienen el certificado público cargado y será un emisor para las entidades emisoras de certificados.

1. Para crear la nueva identidad, ejecute el mandato siguiente:

    ```
    composer identity import -p bmx-hlfv1 -u admin -c ~/.identityCredentials/admin-pub.pem -k ~/.identityCredentials/admin-priv.pem
    ```
    {:codeblock}

    Donde `bmx-hlfv1` es el nombre del perfil que ha creado anteriormente. Ahora esté preparado para desplegar su archivo `.bna` en la plataforma {{site.data.keyword.blockchainfull_notm}}.


## Paso doce: Despliegue de la red empresarial

Ahora puede desplegar el archivo `.bna` en la plataforma {{site.data.keyword.blockchainfull_notm}}.

1. Con la identidad creada en el paso anterior, despliegue la red empresarial con el siguiente mandato:

   ```
   composer network deploy -a myNetwork.bna -p bmx-hlfv1 -i admin -s anyString
   ```
   {:codeblock}
