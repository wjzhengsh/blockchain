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

# Despliegue de una red empresarial en el Plan empresarial
{: #deploying-a-business-network}

**IBM recomienda el uso de Hyperledger Composer únicamente para demostraciones y pruebas de concepto. IBM no proporciona soporte para redes que utilicen Hyperledger Composer en producción, incluyendo la CLI de Composer, las API de JavaScript, el servidor REST y Web Playground.**

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Las herramientas del desarrollador de {{site.data.keyword.blockchainfull}} Platform: Develop le ayudan a crear una **Definición de red empresarial**, que se puede empaquetar en un archivo de red empresarial (`.bna`). El entorno del desarrollador le permite desplegar archivos `.bna` en un {{site.data.keyword.blockchain}} local o de nube para su desarrollo y compartición.

Esta guía aborda el siguiente paso del ciclo de vida de la red empresarial, es decir, activar la red empresarial desplegando el archivo `.bna` en el Plan empresarial de la plataforma {{site.data.keyword.blockchainfull_notm}}.

## Antes de empezar

Asegúrese de que ha instalado el entorno de desarrollador de {{site.data.keyword.blockchainfull_notm}} y de estar familiarizado con el desarrollo y despliegue de redes empresariales. Dispone de instrucciones sobre cómo escribir redes empresariales en la [documentación de Hyperledger Composer](https://hyperledger.github.io/composer/latest/business-network/business-network-index).

Necesita acceso a una instancia del Plan empresarial de {{site.data.keyword.blockchainfull_notm}} Platform y de tener creados los iguales de antemano. Para obtener más información sobre el Plan empresarial de la plataforma {{site.data.keyword.blockchainfull_notm}}, consulte la [visión general del Plan empresarial](./enterprise_plan.html).

## Paso uno: crear un perfil de conexión para la plataforma {{site.data.keyword.blockchainfull_notm}}

1. Cree un directorio en el que almacenará los detalles de conexión, por ejemplo:

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    Cada perfil de conexión debe contener un archivo `connection.json`. Cree un directorio nuevo bajo `.composer-connection-profiles`, en este caso `bmx-hlfv11`. Este será el nombre del perfil que utilizará cuando trabaje con Hyperledger Composer y con la plataforma {{site.data.keyword.blockchainfull_notm}}.

    ```
    mkdir -p ~/.composer-connection-profiles/bmx-hlfv11
    cd ~/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    Ahora debería tener la siguiente estructura de directorios:

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```

## Paso dos: recuperar el perfil de conexión

1. Desde el panel de control de la plataforma {{site.data.keyword.blockchainfull_notm}}, seleccione
**Visión general** y luego **Connection Profile**, y pulse el botón **Descargar** para recuperar su perfil de conexión.

2. Guarde el perfil de conexión en la estructura de directorios creada en el paso anterior. Póngale el nombre **connection.json**.

## Paso tres: añadir información de canal

1. Añada el valor del canal en el archivo `connection.json` de modo que coincida con el nombre del canal que tiene previsto crear y en el que piensa desplegar su red empresarial. En la plantilla de perfil de conexión de ejemplo proporcionada, el elemento channels es el siguiente:
`"channels": {},`.

## Paso cuatro: preparar los iguales

**Nota**: este paso se **debe** realizar antes de crear el canal en el que se va a desplegar una red empresarial. Si este paso se realiza después de crear el canal, es posible que la red empresarial desplegada **no se inicie**.

En el documento de perfil de conexión, bajo **certificateAuthorities**, hay un atributo **registrar** que contiene atributos correspondientes a **enrollId** y **enrollSecret** en el formato siguiente:

 ```
        "registrar": [
            {
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],
 ```

1. Cree una tarjeta de red empresarial para la CA utilizando el mandato siguiente:

    ```
    composer card create -f ca.card -p bmx-hlfv11 -u admin -s PA55W0RD12
    ```
    {:codeblock}

  Asegúrese de que está ejecutando el mandato en el mismo directorio que el perfil de conexión.

2. Importe la tarjeta de red empresarial utilizando el siguiente mandato:

    ```
    composer card import -f ca.card -c ca
    ```
    {:codeblock}

3. Una vez que se haya importado la tarjeta, se puede utilizar para adquirir los certificados de la entidad emisora de certificados utilizando el mandato siguiente:

    ```
    composer identity request --card ca --path ./credentials -u admin -s PA55W0RD12
    ```
    {:codeblock}

    El mandato `composer identity request` crea un directorio de credenciales que contiene los archivos de certificado relevantes.

4. Seleccione **Miembros** en el menú de navegación, seleccione la opción de menú **Certificados** y pulse el botón **Añadir certificado**.

5. Escriba un nombre exclusivo para este certificado en el campo **Nombre**; este nombre no puede incluir guiones.

6. Abra el archivo `admin-pub.pem` creado anteriormente, copie el contenido de este archivo en el campo **Key** y pulse el botón **Enviar**.

7. Utilice la interfaz de usuario para detener los iguales y luego inícielos.

8. Ahora el certificado debería aparecer en la lista de certificados.

## Paso cinco: crear un canal

1. Seleccione **Canales** en el menú de navegación del panel izquierdo y pulse el botón **Nuevo canal**.

2. Especifique un nombre de canal y una descripción opcional y pulse **Siguiente**. Tenga en cuenta que el nombre del canal debe coincidir con el nombre que especifique en el perfil de conexión para el atributo de canal.

3. Asigne los permisos necesarios y pulse **Siguiente**.

4. Seleccione la política del número de operadores que necesitan aceptar actualizaciones de canal y envíe la solicitud.

5. Esto le debería llevar a la sección **Notificaciones**, donde debería haber una nueva solicitud que revisar. Pulse el botón **Revisar solicitud**.

6. Pulse el botón **Aceptar** y volverá a la sección **Notificaciones**, donde puede ver que ahora la solicitud se puede enviar. Pulse el botón **Enviar solicitud** para abrir el diálogo de envío y pulse el botón **Enviar**. El nuevo canal se ha creado.

7. Seleccione **Canales** en el menú de navegación. El nuevo canal debería estar en la lista de canales y debería mostrar "No se han añadido iguales aún". Pulse el menú de acciones que hay junto al canal y seleccione **Unir iguales**. Marque los recuadros de selección que hay junto a los iguales que desea añadir y pulse **Añadir seleccionados**.

## Paso seis: crear una nueva identidad para administrar la red empresarial

Cree una tarjeta de red empresarial utilizando los certificados solicitados. Esta tarjeta de red empresarial tendrá autoridad para instalar código de encadenamiento en los iguales que tienen el certificado público cargado y será un emisor para las entidades emisoras de certificados.

1. Para crear la tarjeta, ejecute el mandato siguiente:

    ```
    composer card create -f adminCard.card -p bmx-hlfv11 -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
    ```
    {:codeblock}

    Donde `bmx-hlfv11` es el nombre del perfil que ha creado anteriormente.

2. Tras crear la tarjeta, impórtela utilizando el mandato siguiente:

    ```
    composer card import -f adminCard.card -c adminCard
    ```
    {:codeblock}

    Ahora esté preparado para desplegar su archivo `.bna` en la plataforma {{site.data.keyword.blockchainfull_notm}}.


## Paso siete: desplegar la red empresarial

Ahora puede desplegar el archivo `.bna` en la plataforma {{site.data.keyword.blockchainfull_notm}}.

1. Para utilizar la tarjeta que ha creado en el paso anterior, primero debe instalar y luego iniciar la red empresarial. Instale la red empresarial utilizando el mandato siguiente:

   ```
   composer network install -c adminCard -a myNetwork.bna
   ```
   {:codeblock}

2. Después de instalar la red empresarial, inicie la red empresarial utilizando el mandato siguiente:

    ```
    composer network start -c adminCard -n myNetwork -V networkVersion -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. Para comprobar que la red empresarial se ha desplegado correctamente, cree una identidad y una tarjeta de red empresarial asociada que se puede utilizar para hacer ping a la red.

    ```
    composer card create -f admin.card -p bmx-hlfv11 -u admin -n myNetwork -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
    ```
    {:codeblock}

4. Importe la tarjeta de red empresarial utilizando el siguiente mandato:

    ```
    composer card import -f admin.card
    ```
    {:codeblock}

5. Haga ping a la red para comprobar que la red esté en ejecución:

    ```
    composer network ping -c admin@myNetwork
    ```
    {:codeblock}
