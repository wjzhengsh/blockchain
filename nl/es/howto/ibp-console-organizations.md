---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: organizations, MSPs, create an MSP, MSP JSON file, consortium, system channel

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Gestión de organizaciones
{: #ibp-console-organizations}

Puede utilizar la consola de {{site.data.keyword.blockchainfull}} Platform para crear una definición de organización formal conocida como Proveedor de servicios de pertenencia (MSP). La definición de MSP de su organización permite que otros miembros del consorcio de blockchain verifiquen la identidad de los nodos y de las aplicaciones. La definición de MSP también contiene los certificados de administración de la organización.

También puede utilizar la consola para gestionar las organizaciones que son miembros de la red. El administrador del servicio de ordenación puede utilizar el separador Organizaciones para añadir miembros al [consorcio](/docs/services/blockchain/glossary.html#glossary-consortium) de blockchain. Luego los miembros del consorcio pueden utilizar la consola para añadir miembros a canales nuevos o existentes.

**Audiencia de destino:** este tema está diseñado para los operadores de red responsables de crear, supervisar y gestionar la red blockchain.

## Visión general de los MSP
{: #console-organizations-about-msp}

{{site.data.keyword.blockchainfull_notm}} Platform se basa en Hyperledger Fabric y crea redes blockchain con permisos. Los participantes tienen que ser conocidos por la red para que puedan enviar transacciones e interactuar con los activos del libro mayor. Fabric reconoce la identidad a través de un grupo de organizaciones invitadas, conocidas como el consorcio. Las organizaciones del consorcio pueden emitir credenciales válidas a sus miembros y permitir que se conviertan en participantes en la red. Luego los participantes pueden trabajar con nodos de blockchain y enviar transacciones desde aplicaciones cliente.

Cada organización del consorcio tiene que trabajar con su propia entidad emisora de certificados, conocida como la CA raíz. Esta entidad emisora de certificados (o sus CA intermedias) crea todas las identidades que pertenecen a la organización y emite para cada identidad un certificado para firmas y una clave privada. Estas claves están firmadas por la CA y las utilizan los miembros de la organización para firmar y verificar sus acciones. El hecho de unirse al consorcio permite que otras organizaciones reconozcan su firma de CA y verifiquen que los iguales y las aplicaciones son participantes válidos. Para obtener más información sobre la pertenencia a Hyperledger Fabric, consulte el [tema sobre el concepto de pertenencia ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html "Pertenencia") en la documentación de Fabric.

Para que su organización pueda unirse a un consorcio, tiene que crear una definición de organización conocida como **Proveedor de servicios de pertenencia (MSP)**. El MSP contiene la información siguiente:
- Certificado firmado por la **entidad emisora de certificados raíz**. Este certificado se utiliza para verificar la identidad de los nodos, de los canales y de las aplicaciones.
- Certificado firmado por la **CA de TLS**. Un certificado de TLS raíz permite que los iguales participen en el protocolo gossip de la organización, lo cual es necesario para aprovechar las características de recopilaciones de [datos privados ****](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) y [Service Discovery ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Service Discovery") de Hyperledger Fabric.
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

  1. Cree un certificado para firmas y una clave privada para cada identidad de administrador.
  2. Proporcione el certificado para firmas de cada identidad de administrador en la definición de MSP.
  3. Añada la identidad a la cartera de la consola. Los nodos o canales que cree la consola utilizan certificados del MSP para comprobar quién es un administrador válido. Como resultado, el mismo par de certificado para firmas y clave privada que haya utilizado para añadir un certificado de administrador al MSP se debe almacenar en la cartera de la consola.

  Puede utilizar el panel **Crear definición de MSP** para llevar a cabo estas acciones para una de las identidades de administrador. Después de seleccionar la CA raíz, siga los pasos siguientes en la sección **Generar certificado de administrador de organización**:
  1. Especifique el ID de inscripción y el secreto de inscripción de una identidad de administrador registrada con la CA raíz. Después de especificar el ID de inscripción y el secreto de inscripción, elija un nombre para visualizar la identidad en la cartera de la consola.
  2. Pulse **Generar**. Esto generará un certificado y una clave privada y añadirá automáticamente las claves a la cartera de la consola. Luego podrá encontrar su identidad de administrador en la cartera utilizando el nombre que ha seleccionado en este panel. Estas claves solo se almacenan en el almacenamiento local del navegador; por lo tanto, si cambia de navegador, no estarán en la cartera de la consola. Tendrá que exportar la identidad desde el navegador original e importarla en la cartera de la consola del nuevo navegador.
  3. A continuación pulse **Exportar** para descargar el par de claves en el sistema de archivos y para protegerlos.

- La sección **Administradores (Opcional)** del panel lateral contiene las claves de certificados para firmas de sus administradores. El certificado que ha generado en la sección **Generar certificado de administrador de organización** se puede encontrar en la primera fila del campo **Certificado de administrador**. Si desea utilizar varias identidades de administrador para trabajar con la red, puede pegar los certificados de otros nodos o administradores de la organización en los campos **Certificado de administrador**.

Puesto que los certificados de administrador se pasan a los nodos y a los canales utilizando la definición de MSP, debe asegurarse de que cada uno de los certificados de administrador de la organización y de nodo se almacene en el MSP. Cuando utilice la consola para crear un clasificador, un igual o un canal, tendrá que **Asociar** una de las identidades que ha exportado en la cartera de la consola con los certificados de administrador que se han proporcionado a la definición de MSP. Cuando encuentre una sección o un panel **Asociar identidad**, seleccione una identidad que ha generado y guardado en la cartera al crear la definición de MSP.

Una vez que haya seleccionado la CA raíz y el ID de MSP y que haya creado los certificados de administrador, pulse **Crear definición de MSP** para crear la definición de MSP. Ahora aparecerá como una organización en el separador Organizaciones. Utilizará la definición de MSP cuando despliegue los nodos y se una a un consorcio de blockchain.

## Creación manual de un archivo JSON de MSP
{: #console-organizations-build-msp}

**Esta opción es solo para usuarios avanzados que estén familiarizados con la manera en que se utilizan los certificados en la gestión de identidades de blockchain.**

Si prefiere utilizar certificados para el igual o el servicio de ordenación de una **CA externa**, una que no esté alojada en
{{site.data.keyword.IBM_notm}}, debe crear un archivo JSON de definición MSP que represente la definición MSP de la organización del igual o el servicio de ordenación.

Tenga en cuenta que todos los certificados deben estar codificados en formato base64.
{:important}

Puede convertir el contenido del archivo de certificado, `<cert.pem>`, del formato `PEM` a una serie base64 ejecutando el mandato siguiente en la máquina local:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat <cert.pem> | base64 $FLAG
```
{:codeblock}


Cree un archivo JSON utilizando el formato siguiente:

```
{
    "name": "<organization_name>",
    "msp_id": "<organization_id>",
    "type": "msp",
    "root_certs": [
        "<root_certs>"
    ],
     "intermediate_certs": [
         "<intermediate_certs>"
     ],
    "admins": [
        "<admins>"
    ],
    "tls_root_certs": [
        "<tls_root_certs>"
    ],
    "tls_intermediate_certs": [
        "<tls_intermediate_certs>"
    ]
}
```
{:codeblock}

- **organization_name**: especifique el nombre a utilizar para identificar esta definición MSP en la consola.
- **organization_id**: especifique un ID utilizado para representar este MSP internamente en la consola.
- **root_certs**: (opcional) pegue una matriz que contenga uno o más certificados raíz de la CA externa en formato
`base64`. Debe proporcionar un certificado raíz de CA o un certificado de CA intermedio, o ambos.
- **intermediate_certs**: (opcional) pegue una matriz que contenga uno o más certificados de la CA intermedia externa en formato `base64`. Debe proporcionar un certificado raíz de CA o un certificado de CA intermedio, o ambos.
- **admins**: pegue el certificado para firmas del administrador de la organización en formato `base64`.
- **tls_root_certs**: (opcional) pegue una matriz que contenga uno o más certificados raíz de la CA de TLS en formato
`base64`. Debe proporcionar un certificado raíz de CA TLS externo o un certificado de CA TLS intermedio externo, o ambos.
- **tls_intermediate_certs**: (opcional) pegue una matriz que contenga uno o más certificados de la CA TLS intermedia en formato `base64`. Debe proporcionar un certificado raíz de CA TLS externo o un certificado de CA TLS intermedio externo, o ambos.  

Los campos adicionales siguientes también están disponibles en la definición de MSP, pero no son obligatorios:
- **organizational_unit_identifiers**: una lista de unidades organizativas (OU) que los miembros válidos de este MSP deben incluir en su certificado X.509. Este es un parámetro de configuración opcional que se utiliza cuando varias organizaciones hacen uso de la misma raíz de confianza y las mismas CA intermedias, y han reservado un campo de OU para sus miembros. Con frecuencia, una organización se divide en varias unidades organizativas (OU), cada una de las cuales tiene un conjunto de responsabilidades determinado. Por ejemplo, la organización ORG1 puede tener las OU ORG1-MANUFACTURING y ORG1-DISTRIBUTION para reflejar estas líneas de negocio independientes. Cuando una CA emite certificados X.509, el campo de OU en el certificado especifica la línea de negocio a la que pertenece la identidad. Consulte este tema sobre
[Clasificación de identidades ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo") ](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html#identity-classification "Clasificación de identidades") en la documentación de Fabric para obtener más información.  
- **fabric_node_OUs**: unidades organizativas específicas de Fabric que permiten la clasificación de identidades. `NodeOUs` contienen información sobre cómo distinguir clientes, iguales y clasificadores en función de su unidad organizativa. Si se fuerza la comprobación, estableciendo Enabled en true, el MSP considerará válida una identidad si es una identidad de un
`cliente`, un `igual` o un `clasificador`. Una identidad debe tener solo una de estas unidades organizativas especiales. Consulte este tema para ver un ejemplo de [cómo especificar la `fabric_node_OU` en un MSP ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/discovery-cli.html#configuration-query "Consulta de configuración") en la documentación de Fabric Service Discovery.
- **revocation_list**: una lista de certificados que ya no son válidos. Para las identidades basadas en X.509, estos identificadores son pares de series conocidas como Identificador clave de asunto (SKI) e Identificador de acceso de autorización (AKI), y se comprueban siempre que se utilice el certificado X.509 para asegurarse de que el certificado no se ha revocado. Consulte este tema de la documentación de Fabric para obtener más información sobre
[Listas de revocación de certificados ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html?highlight=revocation%20list#revoking-a-certificate-or-identity "Revocación de un certificado o identidad").

Por ejemplo, el archivo JSON tendrá un aspecto similar a:

```
{
    "name": "Org1 MSP",
    "msp_id": "org1msp",
    "type": "msp",
    "root_certs": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVZFhUcWNZVVhRS3U3WHVQWmcxUHBsekpFVFlNd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTS0K"
    ],
    "admins": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlDWHpDQ0FnYWdBd0lCQWdJVVp6NFdQdWwxRXRVOUNIcTl4NFg0Y2QwakNpNHdDZ1lJS29aSXpqMEVBd0l3DQphREVMTUFrR0ExVUVCaE1DVlZNeEZ6QVZCZ05WQkFnVERrNXZjblJvSUVOaGNtOXNhVzVoTVJRd0VnWURWUVFLDQpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbFLS0tLS0NCg=="
    ],
    "tls_root_certs": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNHRENDQWI2Z0F3SUJBZ0lVTzVhWU9WbjNwTkRMZGVLTFlIanRIUEtNTnY4d0NnWUlLb1pJemowRUF3SXcKWFRFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVE0d0RBWamd5TURRM01EQmFNRjB4Q3pBSkJnTlZCQVlUQWxWVE1SY3cKRlFZRFZRUUlFdztLS0K"
    ]
}
```
{:codeblock}

Guarde esta definición como el archivo `JSON` de su definición de MSP.  

Ha construido una definición de MSP, que define la organización para los nodos de igual o de servicio de ordenación y que utiliza certificados de una CA externa. Ahora puede volver a las instrucciones que describen
[Cómo utilizar certificados de una CA externa con su igual o clasificador](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-third-party-ca).

## Importación de un MSP
{: #console-organizations-import-msp}

Solo el administrador del clasificador puede añadir nuevas organizaciones al consorcio. Si es el administrador del clasificador, tendrá que recopilar las definiciones de MSP de todas las organizaciones que han sido invitadas al consorcio e importar los MSP en la consola. Luego añadirá los MSP al servicio de ordenación, utilizando el nodo del clasificador.

Después de que un administrador cree una definición de MSP, puede utilizar el separador Organizaciones para descargar el MSP en formato JSON en su sistema de archivos local. Luego le puede enviar el archivo JSON de MSP en una operación fuera de banda. Vaya al separador **Organizaciones** y utilice la opción **Importar definición de MSP** para importar el archivo MSP en la consola. Cuando una definición de MSP aparece en la sección **Organizaciones disponibles**, puede navegar hasta el nodo del clasificador para [añadir la organización al consorcio](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium).


## Adición de una organización a un consorcio
{: #console-organizations-add-consortium}

El consorcio de organizaciones se aloja en el servicio de ordenación.

Si es el administrador del servicio de ordenación, puede utilizar la consola para añadir una organización al consorcio. Vaya al separador **Nodos** y pulse el nodo de ordenación. En el panel del nodo de ordenación, en **Miembros de consorcio**, pulse **Añadir organización**. Esto abrirá un panel lateral que le permitirá seleccionar en la lista de definiciones de MSP disponibles que ha [importado en el separador de organizaciones](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp). También puede utilizar la opción **Cargar JSON** para importar directamente el archivo de definición de MSP creado por otra organización.

## Creación y edición de un canal
{: #console-organizations-create-channel}

Una vez que se ha añadido una organización al consorcio, la organización puede utilizar el servicio de ordenación para crear un nuevo canal o bien se puede añadir a un canal existente. La información que le permite participar en un canal, como por ejemplo unir sus iguales al canal, crear instancias de contratos inteligentes y enviar transacciones, se proporciona utilizando las definiciones de MSP.

Después de que se haya añadido una organización a un consorcio, puede crear un canal siguiendo los pasos siguientes:

1. Importe el nodo de ordenación que aloja el consorcio en la consola. No es necesario que sea un administrador del nodo de ordenación, pero tiene que proporcionar el nombre de nodo de ordenación y la información de punto final en la consola.
2. Importe los MSP de las organizaciones que desea añadir al nuevo canal en la consola en el separador **Organizaciones**. **Tenga en cuenta** que las organizaciones se tienen que añadir al consorcio para que se puedan añadir a un canal.
3. Vaya al separador **Canales** y pulse **Crear canal**. Esto abrirá un panel lateral que le permite especificar el nombre del canal, la pertenencia y las políticas del canal. Puede añadir al nuevo canal cualquier organización que se haya añadido al consorcio.

Para obtener más información sobre estos pasos, consulte el apartado sobre [creación de un canal](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel1) de la guía de aprendizaje sobre cómo **Crear una red**.

### Actualización de un MSP en una definición de canal
{: #console-organizations-update-channel}

Con el tiempo, puede que necesite actualizar los certificados en una definición de MSP que ya está asociada a un canal. Si se produce esta situación, siga los pasos que se describen a continuación para actualizar la definición de MSP de una organización en el canal:  

1. Vaya al separador **Canales** de la consola.
2. Pulse sobre el canal que contiene el MSP de la organización que desea actualizar y ábralo.
3. Pulse sobre el separador **Detalles del canal**.
4. Pulse sobre el mosaico del miembro de canal asociado que desea actualizar.
5. Si todavía no ha importado la definición de MSP actualizada a la consola, puede cargar el archivo aquí. **Nota:** mediante esta acción no se actualizará la definición de MSP asociado en el separador Organizaciones. Si ya ha actualizado la definición de MSP en el separador Organizaciones de la consola, puede seleccionarlo de la lista desplegable.
