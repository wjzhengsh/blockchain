---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Despliegue de redes empresariales en el Plan inicial y empresarial
{: #deploying-a-business-network}

{{site.data.keyword.IBM}} no proporciona soporte para redes que utilicen Hyperledger Composer en producción, incluyendo la CLI de Composer, las API de JavaScript, el servidor REST y Web Playground.
{:note}

Las [redes empresariales](/docs/services/blockchain/glossary.html#glossary-business-network) se pueden desarrollar y desplegar en redes del plan inicial y empresarial utilizando el entorno de desarrollador de {{site.data.keyword.blockchainfull_notm}} Platform y el conjunto de herramientas del desarrollador de Hyperledger Composer.
{:shortdesc}

Con el entorno del desarrollador, puede modelar y probar rápidamente las redes empresariales de blockchain y desplegarlas en una red del plan inicial o empresarial de {{site.data.keyword.blockchainfull_notm}} Platform.

## Despliegue de una red empresarial en el Plan inicial
{: #deploying-a-business-network-starter-plan}

### Antes de empezar
{: #deploying-a-business-network-before-begin}

Asegúrese de leer el apartado [Acerca del Plan inicial](/docs/services/blockchain/starter_plan.html#starter-plan-about) y de haber creado una red del Plan inicial siguiendo las instrucciones de [Iniciación al Plan inicial](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan).

Asegúrese de haber instalado Node v8.9 o superior npm v5.x e Hyperledger Composer:

- Si la red está en Fabric versión 1.2, utilice Hyperledger Composer v0.20.x.
- Si la red está en Fabric versión 1.1, utilice Hyperledger Composer v0.19.x.

Puede encontrar la versión de Fabric abriendo la [ventana Preferencias de red](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) en el supervisor de red.

### Paso uno: recuperar el secreto de administración
{: #deploying-a-business-network-retrieve-admin-secret}

1. Desde la pantalla de visión general del Plan inicial, pulse **Perfil de conexión** y, a continuación, descargue. Cambie el nombre de este archivo a 'connection-profile.json'.

2. Mueva este archivo para que esté en el mismo directorio que el archivo `.bna`.

3. Dentro del perfil de conexión, vaya hacia abajo hasta que vea 'registrar'. Dentro de 'registrar', bajo 'enrollId', hay una propiedad **enrollSecret**. Recupere el secreto y guarde una copia del mismo.

    ![Recuperar secreto de administración](images/get_enroll_secret.gif "Recuperar secreto de administración")

### Paso dos: crear una tarjeta de la entidad emisora de certificados
{: #deploying-a-business-network-CA-card}

El secreto recuperado en el paso anterior se utilizará para crear una tarjeta de red empresarial para la entidad emisora de certificados (CA). La tarjeta de CA se importará y se utilizará para intercambiar el **enrollSecret** por certificados válidos de la entidad emisora de certificados del Plan inicial.

1. Mediante el **enrollSecret** anotado en el paso uno, ejecute el siguiente mandato para crear la tarjeta de red de visita de la entidad emisora de certificados:

   ```
   composer card create -f ca.card -p connection-profile.json -u admin -s enrollSecret
   ```
   {:pre}

  Sustituya `enrollSecret` en el mandato anterior con el secreto de administrador recuperado del perfil de conexión.

2. Importe la tarjeta con el mandato siguiente:

   ```
   composer card import -f ca.card -c ca
   ```
   {:codeblock}

3. Ahora que la tarjeta está importada, se podrá utilizar para intercambiar el **enrollSecret** para certificados válidos de la CA. Ejecute el siguiente mandato para solicitar certificados de la entidad emisora de certificados:

   ```
   composer identity request --card ca --path ./credentials -u admin -s enrollSecret
   ```
   {:codeblock}

  Sustituya `enrollSecret` en el mandato anterior con el secreto de administrador recuperado del perfil de conexión. El mandato `composer identity request` crea un directorio `credentials` que contiene archivos `.pem` de certificado.

### Paso tres: añadir certificados a la instancia del Plan inicial
{: #deploying-a-business-network-add-certs-to-starter-plan}

Los certificados se deben añadir a la red del Plan inicial. Para su comodidad, puede añadirlas utilizando el supervisor de red de {{site.data.keyword.blockchainfull_notm}} Platform. Los certificados deben añadirse y luego los iguales se deben reiniciar, y a continuación los certificados se deben sincronizar en el canal. El certificado necesario es el archivo `admin-pub.pem` que se ha generado desde el mandato anterior, que está en el directorio `credentials`.

1. En el supervisor de red del Plan inicial, pulse el separador **Miembros**, luego **Certificados** y, a continuación, **Añadir certificado**. Vaya al directorio `credentials`, y copie y pegue el contenido del archivo `admin-pub.pem` en el recuadro de certificado. Envíe el certificado y reinicie los iguales. Nota: reiniciar los iguales lleva un minuto.

    ![Añadir certificados](images/add_cert.gif "Añadir certificados")

2. A continuación, los certificados se deben sincronizar en el canal. Pulse el separador **Canales**, luego el botón **Acciones**, y luego **Sincronizar certificado** y **Enviar**.

    ![Sincronizar certificados](images/sync_cert.gif "Sincronizar certificados")

### Paso cuatro: crear una tarjeta de red empresarial de administración
{: #deploying-a-business-network-create-admin-card}

Ahora que los certificados correctos se han sincronizado con los iguales, se pueden crear tarjetas de red empresarial, con permisos para instalar el entorno de ejecución de Hyperledger Composer y para iniciar el código de encadenamiento.

1. Cree una tarjeta de administración con los roles de administrador de canal y administrador de igual con el siguiente mandato:

   ```
   composer card create -f adminCard.card -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
   ```
   {:codeblock}

   Esta tarjeta se utilizará para desplegar una red empresarial en el Plan inicial.

2. Importe la tarjeta creada en el paso anterior con el mandato siguiente:

   ```
   composer card import -f adminCard.card -c adminCard
   ```
   {:codeblock}

### Paso cinco: instalar e iniciar la red empresarial
{: #deploying-a-business-network-install-start-network}

A continuación, la tarjeta creada en el paso anterior se puede utilizar para instalar e iniciar una red empresarial. Para esta guía, instalaremos el ejemplo de red de fabricación de vehículos, bien utilice el ejemplo de fabricación de vehículos o bien instale su propia red empresarial, pero asegúrese de cambiar los nombres de redes empresariales especificados en los mandatos. El mandato para iniciar una red empresarial también creará una tarjeta. Para el Plan inicial, esta tarjeta se debe suprimir; el mandato de ejemplo asigna a esta tarjeta el nombre `delete_me.card`, de modo que se pueda distinguir fácilmente.

1. Instale el entorno de ejecución Hyperledger Composer con el siguiente mandato:

   ```
   composer network install -c adminCard -a vehicle-manufacture-network.bna
   ```
   {:codeblock}

   Anote el número de versión de la red empresarial que se devuelve al ejecutar este mandato. Se necesitará en el paso siguiente.

2. Inicie la red empresarial con el mandato siguiente. Si obtiene un error, espere un minuto y vuelva a intentarlo. Utilice el número de versión del último paso después de la opción `-V`.

    ```
    composer network start -c adminCard -n vehicle-manufacture-network -V 0.0.1 -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. Suprima la tarjeta de red empresarial llamada `delete_me.card`.

4. Cree una nueva tarjeta de red empresarial y haga referencia a los certificados recuperados anteriormente con el siguiente mandato:

   ```
   composer card create -n vehicle-manufacture-network -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
   ```
   {:codeblock}

5. Importe la tarjeta de red empresarial con el siguiente mandato:

    ```
    composer card import -f ./admin@vehicle-manufacture-network.card
    ```
    {:codeblock}

Ahora la red empresarial se ha desplegado en la instancia del Plan inicial.

### Paso seis: hacer ping en la red empresarial para asegurarse de que se está ejecutando correctamente
{: #deploying-a-business-network-ping-business-network}

Ejecute el mandato siguiente para hacer ping en la red empresarial:

   ```
   composer network ping -c admin@vehicle-manufacture-network
   ```
   {:codeblock}

Para ver los registros del código de encadenamiento, pulse **Canales** y seleccione su canal. <!-- Click the dropdown arrow to view the logs, or the Actions symbol to view in more detail. --> Pulse el separador **Código de encadenamiento**. Amplíe la fila del código de encadenamiento y pulse el botón **JSON** o **Registros**.

<!-- [fN-Yuj](https://i.makeagif.com/media/4-13-2018/fN-Yuj.gif) -->

Ahora la red empresarial se ha desplegado correctamente en la instancia del Plan inicial.

## Despliegue de una red empresarial en el Plan empresarial
{: #deploying-a-business-network-to-enterprise-plan}

Las herramientas del desarrollador de {{site.data.keyword.blockchainfull_notm}} Platform le ayudan a crear una **Definición de red empresarial**, que luego se puede empaquetar en un archivo de red empresarial (`.bna`). El entorno del desarrollador le permite desplegar archivos `.bna` en un {{site.data.keyword.blockchain}} local o de nube para su desarrollo y compartición.

Esta guía aborda el siguiente paso del ciclo de vida de la red empresarial, es decir, activar la red empresarial desplegando el archivo `.bna` en el Plan empresarial de la plataforma {{site.data.keyword.blockchainfull_notm}}.

### Antes de empezar
{: #deploying-a-business-network-enterprise-plan-before-begin}

Asegúrese de que ha instalado el entorno de desarrollador de {{site.data.keyword.blockchainfull_notm}} y de estar familiarizado con el desarrollo y despliegue de redes empresariales. Dispone de instrucciones sobre cómo escribir redes empresariales en la [documentación de Hyperledger Composer](https://hyperledger.github.io/composer/latest/business-network/business-network-index).

Necesita acceso a una instancia del Plan empresarial de {{site.data.keyword.blockchainfull_notm}} Platform y de tener creados los iguales de antemano. Para obtener más información sobre el Plan empresarial de la plataforma {{site.data.keyword.blockchainfull_notm}}, consulte la [visión general del Plan empresarial](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

### Paso uno: crear un perfil de conexión para IBM Blockchain Platform
{: #deploying-a-business-network-create-connection-profile}

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

### Paso dos: recuperar el perfil de conexión
{: #deploying-a-business-network-retrieve-connection-profile}

1. Desde el panel de control de la plataforma {{site.data.keyword.blockchainfull_notm}}, seleccione
**Visión general** y luego **Connection Profile**, y pulse el botón **Descargar** para recuperar su perfil de conexión.

2. Guarde el perfil de conexión en la estructura de directorios creada en el paso anterior. Póngale el nombre **connection.json**.

### Paso tres: añadir información de canal
{: #deploying-a-business-network-add-channel-information}

1. Añada el valor del canal en el archivo `connection.json` de modo que coincida con el nombre del canal que tiene previsto crear y en el que piensa desplegar su red empresarial. En la plantilla de perfil de conexión de ejemplo proporcionada, el elemento channels es el siguiente:
`"channels": {},`.

### Paso cuatro: preparar los iguales
{: #deploying-a-business-network-prepare-peers}

Este paso se **DEBE** realizar antes de crear el canal en el que se va a desplegar una red empresarial. Si este paso se realiza después de crear el canal, es posible que la red empresarial desplegada **no se inicie**.
{:note}

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

### Paso cinco: crear un canal
{: #deploying-a-business-network-create-your-channel}

1. Seleccione **Canales** en el menú de navegación del panel izquierdo y pulse el botón **Nuevo canal**.

2. Especifique un nombre de canal y una descripción opcional y pulse **Siguiente**. Tenga en cuenta que el nombre del canal debe coincidir con el nombre que especifique en el perfil de conexión para el atributo de canal.

3. Asigne los permisos necesarios y pulse **Siguiente**.

4. Seleccione la política del número de operadores que necesitan aceptar actualizaciones de canal y envíe la solicitud.

5. Esto le debería llevar a la sección **Notificaciones**, donde debería haber una nueva solicitud que revisar. Pulse el botón **Revisar solicitud**.

6. Pulse el botón **Aceptar** y volverá a la sección **Notificaciones**, donde puede ver que ahora la solicitud se puede enviar. Pulse el botón **Enviar solicitud** para abrir el diálogo de envío y pulse el botón **Enviar**. El nuevo canal se ha creado.

7. Seleccione **Canales** en el menú de navegación. El nuevo canal debería estar en la lista de canales y debería mostrar "No se han añadido iguales aún". Pulse el menú de acciones que hay junto al canal y seleccione **Unir iguales**. Marque los recuadros de selección que hay junto a los iguales que desea añadir y pulse **Añadir seleccionados**.

### Paso seis: crear una nueva identidad para administrar la red empresarial
{: #deploying-a-business-network-add-new-identity-enterprise-plan}

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


### Paso siete: desplegar la red empresarial
{: #deploying-a-business-network-deploy-business-network-enterprise-plan}

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
