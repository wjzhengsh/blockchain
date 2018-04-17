---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Despliegue de una red empresarial en el Plan inicial
{: #deploying-a-business-networks-on-starter-plan}

Las redes empresariales se pueden desarrollar y desplegar en un entorno de Plan inicial mediante el entorno de desarrollo {{site.data.keyword.blockchainfull}} Plataform: Develop y el conjunto de herramientas del desarrollador Hyperledger Composer.

Mediante el entorno del desarrollador, puede modelar rápidamente y probar redes empresariales de {{site.data.keyword.blockchain}} y desplegarlas en una instancia de la plataforma {{site.data.keyword.blockchainfull_notm}}.

## Antes de empezar

Asegúrese de leer los apartados [Acerca del Plan inicial](./starter_plan.html) e [Iniciación al Plan inicial](./get_start_starter_plan.html). Asegúrese también de tener instalado el [entorno del desarrollador {{site.data.keyword.blockchainfull_notm}} Platform: Develop](./develop_install.html) y de haber creado una instancia del Plan inicial de la plataforma {{site.data.keyword.blockchainfull_notm}} siguiendo las instrucciones del apartado sobre [Gobierno de una red del Plan inicial](./get_start_starter_plan.html).


## Paso uno: Recuperar el secreto de administración

1. Desde la pantalla de visión general del Plan inicial, pulse **Perfil de conexión**.

2. Dentro del perfil de conexión hay una propiedad **admin secret**. Recupere el secreto y guarde una copia del mismo.

## Paso dos: Crear una tarjeta de la entidad emisora de certificados

El secreto recuperado en el paso anterior se utilizará para crear una tarjeta de red empresarial para la autoridad emisora de certificados (CA). Luego la tarjeta CA se importará y se utilizará para intercambiar el **secreto de administración** por certificados válidos de la autoridad emisora de certificados del Plan inicial.

1. Con el secreto anotado en el paso uno, ejecute el siguiente mandato para crear una tarjeta de red empresarial de CA:

        composer card create -f ca.card -p ./config/connection-profile.json -u admin -s ${SECRET}

2. Importe la tarjeta con el mandato siguiente:

        composer card import -f ca.card -n ca

3. Ahora que la tarjeta se ha importado, se puede utilizar para intercambiar el **secreto de administración** por certificados válidos de la CA. Ejecute el siguiente mandato para solicitar certificados de la autoridad emisora de certificados:

        composer identity request --card ca --path ./credentials

El mandato `composer identity request` crea un directorio `credentials` que contiene archivos `.pem` de certificado.

## Paso tres: Adición de los certificados a la instancia del Plan inicial

Los certificados se deben añadir a la instancia del Plan inicial. Para su comodidad, se pueden añadir mediante la API de la plataforma {{site.data.keyword.blockchainfull_notm}}. Los certificados se deben añadir y luego los iguales se deben detener y reiniciar y los certificados se deben sincronizar. Los datos que se necesitan en las variables de las llamadas de API se puede recuperar del **Perfil de conexión** en el supervisor de red del Plan inicial.

1. Añada el certificado mediante la siguiente llamada de API:

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary <body> ${API_URL}/api/v1/networks/${NETWORKID}/certificates

    En el ejemplo anterior, el cuerpo se ha dejado en blanco; el código siguiente es un ejemplo del cuerpo de la solicitud de API:

        {
        "msp_id": "ExamplePeerOrg1",
        "adminCertName": "exampleAdminCert1",
        "adminCertificate": "-----BEGIN CERTIFICATE-----\nMIIBkzCCATqgAwIB...",
        "peer_names": [
          "example-fabric-peer-1234a"
        ],
        "SKIP_CACHE": true
        }

2. Detenga los iguales con la siguiente llamada de API:

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/stop

3. Reinicie los iguales con la siguiente llamada de API:

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/start

4. Sincronice los certificados con la siguiente llamada de API:

        curl -X GET --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} ${API_URL}/api/v1/networks/${NETWORKID}/nodes/status

## Paso cuatro: Creación de una tarjeta de red empresarial de administración

Ahora que los certificados correctos se han sincronizado con los iguales, se pueden crear tarjetas de red empresarial, con permisos para instalar el entorno de ejecución de Hyperledger Composer y para iniciar el código de encadenamiento.

1. Cree una tarjeta de administración con los roles de administrador de canal y administrador de igual con el siguiente mandato:

        composer card create -f adminCard.card -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin

    Esta tarjeta se utilizará para desplegar una red empresarial en el Plan inicial.

2. Importe la tarjeta creada en el paso anterior con el mandato siguiente:

        composer card import -f adminCard.card -n adminCard

## Paso cinco: Instalación e inicio de la red empresarial

A continuación, la tarjeta creada en el paso anterior se puede utilizar para instalar e iniciar una red empresarial. En esta guía, vamos a instalar el ejemplo de red de fabricación de vehículos. El mandato para iniciar una red empresarial también creará una tarjeta. Para el Plan inicial, esta tarjeta se debe suprimir; el mandato de ejemplo asigna a esta tarjeta el nombre `delete_me.card`, de modo que se pueda distinguir fácilmente.

1. Instale el entorno de ejecución Hyperledger Composer con el siguiente mandato:

        composer runtime install -c adminCard -n vehicle-manufacture-network

2. Inicie la red empresarial con el siguiente mandato:

        composer network start -c adminCard -a vehicle-manufacture-network.bna -A admin -C ./credentials/admin-pub.pem -f delete_me.card

3. Suprima la tarjeta de red empresarial llamada `delete_me.card`.

4. Cree una nueva tarjeta de red empresarial y haga referencia a los certificados recuperados anteriormente con el siguiente mandato:

        composer card create -n vehicle-manufacture-network -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem

5. Importe la tarjeta de red empresarial con el siguiente mandato:

        composer card import -f ./admin@vehicle-manufacture-network.card

Ahora la red empresarial se ha desplegado en la instancia del Plan inicial.
