---

copyright:
  years: 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Gestión de organizaciones
{: #ibp-console-organizations}

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Puede utilizar la consola de {{site.data.keyword.blockchainfull}} Platform para crear una definición de organización formal conocida como Proveedor de servicios de pertenencia (MSP). La definición de MSP de su organización permite que otros miembros del consorcio de blockchain verifiquen la identidad de los nodos y de las aplicaciones. La definición de MSP también contiene los certificados de administración de la organización.

Puede utilizar la consola para gestionar las organizaciones que son miembros de la red. El administrador del servicio de ordenación puede utilizar el separador Organizaciones para añadir miembros al [consorcio](/docs/services/blockchain/glossary.html#glossary-consortium) de blockchain. Luego los miembros del consorcio pueden utilizar la consola para añadir miembros a canales nuevos o existentes.

## Visión general de los MSP
{: #console-organizations-about-msp}

{{site.data.keyword.blockchainfull_notm}} Platform se basa en Hyperledger Fabric y crea redes blockchain con permisos. Los participantes tienen que ser conocidos por la red para que puedan enviar transacciones e interactuar con los activos del libro mayor. Fabric reconoce la identidad a través de un grupo de organizaciones invitadas, conocidas como el consorcio. Las organizaciones del consorcio pueden emitir credenciales válidas a sus miembros y permitir que se conviertan en participantes en la red. Luego los participantes pueden trabajar con nodos de blockchain y enviar transacciones desde aplicaciones cliente.

Cada organización del consorcio tiene que trabajar con su propia entidad emisora de certificados, conocida como la CA raíz. Esta entidad emisora de certificados (o sus CA intermedias) crea todas las identidades que pertenecen a la organización y emite para cada identidad una clave pública y privada. Estas claves están firmadas por la CA y las utilizan los miembros de la organización para firmar y verificar sus acciones. El hecho de unirse al consorcio permite que otras organizaciones reconozcan su firma de CA y verifiquen que los iguales y las aplicaciones son participantes válidos. Para obtener más información sobre la pertenencia a Hyperledger Fabric, consulte el [tema sobre el concepto de pertenencia ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html "Pertenencia") en la documentación de Fabric.

Para que su organización pueda unirse a un consorcio, tiene que crear una definición de organización conocida como **Proveedor de servicios de pertenencia (MSP)**. El MSP contiene la información siguiente:
- Certificado firmado por la **entidad emisora de certificados raíz**. Este certificado se utiliza para verificar la identidad de los nodos, de los canales y de las aplicaciones.
- Certificado firmado por la **CA de TLS**. Un certificado de TLS raíz permite que los iguales participen en el protocolo gossip de la organización, lo cual es necesario para aprovechar las características de recopilaciones de [datos privados ****](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) y [Service Discovery ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Service discovery") de Hyperledger Fabric.
- El **ID de MSP**. El ID de MSP es el nombre formal de su organización dentro del consorcio. Tiene que recordar el ID de MSP para sus nodos y aplicaciones.
- **Certificados de administración** de las identidades **Admin igual** y **Admin de org**. Estos certificados se pasan al servicio de ordenación y se utilizan para verificar qué identidades de la organización tienen permiso para crear o editar canales. Cuando utilice la consola para crear un clasificador o un igual, los certificados de administrador del MSP se despliegan dentro del nuevo nodo. Luego estos certificados se pueden utilizar para trabajar con los iguales o con los clasificadores desde la consola o desde una aplicación cliente.

## Gestión de MSP en la consola

Vaya al separador **Organizaciones**. Puede utilizar este separador para [crear una definición de MSP](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp) utilizando una entidad emisora de certificados que exista en la consola. También puede utilizar este separador para [importar un MSP](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp) que haya creado otra organización.

Puede ver todos los MSP que ha creado o importado bajo **Organizaciones disponibles**. Puede utilizar las definiciones de MSP del separador de organizaciones para las funciones importantes dentro de la consola:
- Si va a crear nodos iguales o de clasificador, el MSP de la organización se utiliza para proporcionar certificados de administrador al nuevo nodo.
- Si es el administrador de un nodo de ordenación, puede utilizar los MSP para [añadir nuevas organizaciones al consorcio](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium).
- Si es miembro del consorcio, puede importar los MSP de otros miembros del consorcio en su consola y luego añadir los miembros a canales nuevos o existentes.

## Creación de un MSP para su organización
{: #console-organizations-create-msp}

Utilice el separador **Organizaciones** para generar una definición de MSP para la organización. Al pulsar **Crear MSP**, se abre un panel lateral que le permite especificar toda la información necesaria: la CA raíz, el ID de MSP y los administradores de la organización.

- Utilice la sección **Detalles de MSP** del panel para seleccionar el ID de MSP de su organización. Este ID es el nombre formal que utilizarán los otros miembros de la red para hacer referencia a su organización. **Guarde el ID de MSP.**

- Utilice la sección **Detalles de la entidad emisora de certificados raíz** para seleccionar la CA raíz de la organización. Es la CA que ha utilizado (o que utilizará) para crear todas las identidades de nodo y de aplicación que pertenecen a su organización. Si utiliza CA intermedias, esta es la CA que ha utilizado para crear dichas CA. Seleccione la CA raíz en la lista de CA gestionadas utilizando la consola. Si ha creado una CA utilizando la consola, al seleccionar una CA raíz también se creará un certificado TLS raíz.

- También puede utilizar la sección **Detalles de la entidad emisora de certificados raíz** para generar uno de los certificados de administración de la organización. Antes de crear la definición de MSP de la organización, debe registrar los administradores de la organización y de los nodos con la CA raíz. Luego debe seguir los pasos siguientes para poder utilizar estas identidades para trabajar con la red:

  1. Cree un par de claves pública y privada para cada identidad de administrador.
  2. Proporcione la clave pública (certificado) de cada identidad de administrador en la definición de MSP.
  3. Añada la identidad a la cartera de la consola. Los nodos o canales que cree la consola utilizan certificados del MSP para comprobar quién es un administrador válido. Como resultado, el par de claves pública y privada que ha utilizado para añadir un certificado de administrador al MSP se debe almacenar en la cartera de la consola.

  Puede utilizar el panel **Crear definición de MSP** para llevar a cabo estas acciones para una de las identidades de administrador. Después de seleccionar la CA raíz, siga los pasos siguientes en la sección **Generar certificado de administrador de organización**:
  1. Especifique el ID de inscripción y el secreto de inscripción de una identidad de administrador registrada con la CA raíz. Después de especificar el ID de inscripción y el secreto de inscripción, elija un nombre para visualizar la identidad en la cartera de la consola.
  2. Pulse **Generar**. Esto generará un nuevo conjunto de claves públicas y privadas y añadirá automáticamente las claves a la cartera de la consola. Luego podrá encontrar su identidad de administrador en la cartera utilizando el nombre que ha seleccionado en este panel. Estas claves solo se almacenan en el almacenamiento local del navegador; por lo tanto, si cambia de navegador, no estarán en la cartera de la consola. Tendrá que exportar la identidad desde el navegador original e importarla en la cartera de la consola del nuevo navegador.
  3. A continuación pulse **Exportar** para descargar el par de claves en el sistema de archivos y para protegerlos.

- La sección **Administradores (Opcional)** del panel lateral contiene las claves públicas de sus administradores. El certificado que ha generado en la sección **Generar certificado de administrador de organización** se puede encontrar en la primera fila del campo **Certificado de administrador**. Si desea utilizar varias identidades de administrador para trabajar con la red, puede pegar los certificados de otros nodos o administradores de la organización en los campos **Certificado de administrador**.

Puesto que los certificados de administrador se pasan a los nodos y a los canales utilizando la definición de MSP, debe asegurarse de que cada uno de los certificados de administrador de la organización y de nodo se almacene en el MSP. Cuando utilice la consola para crear un clasificador, un igual o un canal, tendrá que **Asociar** una de las identidades que ha exportado en la cartera de la consola con los certificados de administrador que se han proporcionado a la definición de MSP. Cuando encuentre una sección o un panel **Asociar identidad**, seleccione una identidad que ha generado y guardado en la cartera al crear la definición de MSP.

Una vez que haya seleccionado la CA raíz y el ID de MSP y que haya creado los certificados de administrador, pulse **Crear definición de MSP** para crear la definición de MSP. Ahora aparecerá como una organización en el separador Organizaciones. Utilizará la definición de MSP cuando despliegue los nodos y se una a un consorcio de blockchain.

## Importación de un MSP
{: #console-organizations-import-msp}

Solo el administrador del clasificador puede añadir nuevas organizaciones al consorcio. Si es el administrador del clasificador, tendrá que recopilar las definiciones de MSP de todas las organizaciones que han sido invitadas al consorcio e importar los MSP en la consola. Luego añadirá los MSP al servicio de ordenación, utilizando el nodo del clasificador.

Después de que un administrador cree una definición de MSP, puede utilizar el separador Organizaciones para descargar el MSP en formato JSON en su sistema de archivos local. Luego le puede enviar el archivo JSON de MSP en una operación fuera de banda. Vaya al separador **Organizaciones** y utilice la opción **Importar definición de MSP** para importar el archivo MSP en la consola. Cuando una definición de MSP aparece en la sección **Organizaciones disponibles**, puede navegar hasta el nodo del clasificador para [añadir la organización al consorcio](docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium).


## Adición de una organización a un consorcio
{: #console-organizations-add-consortium}

El consorcio de organizaciones se aloja en el servicio de ordenación.

Si es el administrador del servicio de ordenación, puede utilizar la consola para añadir una organización al consorcio. Vaya al separador **Nodos** y pulse el nodo de ordenación. En el panel del nodo de ordenación, en **Miembros de consorcio**, pulse **Añadir organización**. Esto abrirá un panel lateral que le permitirá seleccionar en la lista de definiciones de MSP disponibles que ha [importado en el separador de organizaciones](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp). También puede utilizar la opción **Cargar JSON** para importar directamente el archivo de definición de MSP creado por otra organización.

## Creación y edición de un canal

Una vez que se ha añadido una organización al consorcio, se puede utilizar el servicio de ordenación para crear un nuevo canal o se puede añadir a un canal. La información que le permite participar en un canal, como por ejemplo unir sus iguales al canal, crear instancias de contratos inteligentes y enviar transacciones, se proporciona utilizando las definiciones de MSP.

Después de que se haya añadido una organización a un consorcio, se puede crear un canal siguiendo los pasos siguientes:

1. Importe el nodo de ordenación que aloja el consorcio en la consola. No es necesario que sea un administrador del nodo de ordenación. Sin embargo, su consola necesita tener el nombre del clasificador y la información del punto final.
2. Importe los MSP de las organizaciones que desea añadir al nuevo canal en su consola utilizando el separador de organizaciones. **Tenga en cuenta** que las organizaciones se tienen que añadir al consorcio para que se puedan añadir a un canal.
3. Vaya al separador **Canales** y pulse **Crear canal**. Esto abrirá un panel lateral que le permitirá especificar el nombre del canal, la pertenencia y las políticas del canal. Puede añadir al nuevo canal cualquier organización que se haya añadido al consorcio.

Para obtener más información sobre estos pasos, consulte el apartado sobre [creación de un canal](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel1) de la guía de aprendizaje sobre cómo crear una red.
