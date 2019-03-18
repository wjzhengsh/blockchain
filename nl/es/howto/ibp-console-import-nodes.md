---

copyright:
  years: 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Importación de nodos
{: #ibp-console-import-nodes}

La consola incluye la opción de importar nodos que se crean utilizando otra consola de {{site.data.keyword.blockchainfull}} Platform.
{:shortdesc}  

**Audiencia de destino:** este tema está diseñado para los operadores de red responsables de crear, supervisar y gestionar la red blockchain.

## ¿Por qué importar un nodo?

Importe nodos de CA, clasificador e igual desde otras consolas de {{site.data.keyword.blockchainfull_notm}} Platform cuando necesite utilizarlos además de los nodos ya existentes en su consola. Por ejemplo, puede utilizar la opción de importación de un igual si desea unir iguales de organizaciones externas a su consola a canales de su consola. Como los servicios de {{site.data.keyword.blockchainfull_notm}} Platform 2.0 se despliegan entre varios entornos de {{site.data.keyword.cloud_notm}}, es probable que algunas instancias de servicio solo incluyan CA e iguales, mientras que otras alojen servicios de ordenación. La importación de distintos nodos en la consola proporciona una forma de ver y trabajar con estos componentes distribuidos desde una sola interfaz de usuario para que puedan realizar transacciones conjuntamente en blockchain.

Una vez importe nodos en la consola, dispondrá de un potente conjunto de funciones operativas, como por ejemplo:
- Añadir nuevas organizaciones al consorcio
- Crear nuevos canales
- Unir iguales a canales
- Instalar contratos inteligentes en iguales, independientemente del lugar en el que se hayan desplegado
- Crear instancias de contratos inteligentes en canales
- Actualizar contratos inteligentes en canales

Cuando importe un componente, debe proporcionar su información de conexión. Cuando importe un componente, en realidad no importa el componente físico propiamente dicho, sino que se utiliza la información de conexión para crear una representación del componente en la consola de modo que se pueda utilizar desde la consola.

 Después de importar un nodo en la consola, también puede modificar su información de conexión mediante el separador **Valores** del nodo.

{: note}
Para poder importar nodos en la consola, deben exportarse desde la consola de {{site.data.keyword.blockchainfull_notm}} Platform donde se han creado.  Simplemente exporte la información de nodo desde la consola a un archivo `JSON` e importe el archivo generado utilizando el botón `Cargar JSON` en el paso del panel lateral de nodo de importación de la consola.


## Empiece aquí: recopilación de certificados o credenciales

Para poder importar un componente de blockchain existente desde otra instancia de servicio de {{site.data.keyword.blockchainfull_notm}} Platform 2.0, debe
importar la identidad de administrador del componente en la cartera de la consola. La importación de la identidad en la cartera de la consola simplifica la operación del nodo. El operador de red que ha creado el nodo tiene que proporcionar estas credenciales.

## Importación de una CA
{: #ibp-console-import-ca}

Un nodo de CA es el componente de blockchain que emite certificados a todas las entidades de la red (iguales, clasificadores, clientes, etc.) para que dichas entidades se puedan comunicar, autenticar y, en última instancia, realizar transacciones. Cada organización tiene su propia CA que actúa como su raíz de confianza. Debe añadir sus organizaciones si está uniendo o creando un consorcio de blockchain. Encontrará más información sobre las CA en la [visión general de los componentes de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-ca).  

Hay varias razones por las que puede desear importar una CA en la consola. Después de importar la CA, puede utilizarla para añadir más iguales a la organización igual pulsando **Registrar usuario**. O bien, puede utilizar la CA para crear identidades de administrador adicionales para un igual o un clasificador.

Para importar una CA en la consola de {{site.data.keyword.blockchainfull_notm}} Platform y trabajar con la misma, ya debe haber exportado la CA desde otra {{site.data.keyword.blockchainfull_notm}} Platform. El hecho de importar una CA le permite registrar nuevos usuarios e [inscribir identidades](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll).

### Antes de empezar
{: #ibp-console-import-ca-before-you-begin}

- Asegúrese de que ya ha importado el archivo JSON de identidad de administrador de la CA en la cartera de la consola.
- Asegúrese de que el archivo JSON de la CA que se ha exportado desde la consola donde se ha creado está disponible.

### Cómo importar una CA  

La importación de una CA se realiza desde el separador **Nodos**. Pulse **Añadir entidad emisora de certificados** `+` en la sección de entidades emisoras de certificados y luego pulse **Importar una CA existente**. Siga los pasos del panel lateral que se abre para especificar la información de conexión.

- Pulse el botón **Cargar JSON** para omitir la entrada manual de la información y navegar hasta el archivo JSON que se ha exportado desde la consola en la que se ha creado la CA.
- Establezca la identidad de administrador para la CA pulsando **Identidad existente** y seleccionando la identidad de administrador de CA desde la cartera de la consola.

Si lo desea, cuando importe una CA también puede especificar las credenciales para la CA de TLS adicional si se incluye una.  

Una vez que haya importado la CA en la consola, puede utilizar la CA para crear nuevas identidades y generar los certificados necesarios para trabajar con los componentes y enviar transacciones a la red. Para obtener más información, consulte [Gestión de entidades emisoras de certificados](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-manage-ca).

### Importación de identidades de administrador en la cartera de la consola

Cada componente de {{site.data.keyword.blockchainfull_notm}} Platform se despliega con su propia clave pública, el certificado de firma, de un administrador del componente incluida. Cuando el administrador realiza acciones sobre el componente, esta clave pública se adjunta a la transacción y se valida en la configuración del componente. Las claves permiten al administrador trabajar con sus componentes, como por ejemplo registrar nuevos usuarios, crear un nuevo canal o instalar contratos inteligentes en los iguales. Si desea utilizar la consola trabajar con un clasificador o con un igual que se ha creado utilizando otra consola, debe cargar la identidad de administrador asociada en la cartera de la consola. Luego puede asociar el nodo importado con la identidad en la cartera de la consola. Estas identidades se deben exportar desde la consola en la que se han creado y son necesarias cuando se importa el nodo.

Para importar una nueva identidad, abra el separador **Cartera** y pulse **Añadir identidad**. Pulse **Cargar JSON** para examinar el archivo de identidad JSON que ha exportado el operador de la red desde el lugar en el que se ha creado el nodo.

Después de completar el panel **Añadir identidad** y de pulsar Enviar, puede ver la nueva identidad de administrador en la pantalla de visión general de la cartera. Ahora puede hacer referencia a estas identidades cuando importe los componentes CA, igual o clasificador.

## Importación de un clasificador
{: #ibp-console-import-orderer}

Un nodo clasificador es el componente de blockchain que recopila las transacciones de los miembros de la red, ordena las transacciones y las empaqueta en bloques. El servicio de ordenación, que consta de una colección de clasificadores, es el enlace común de los consorcios de blockchain, y se debe desplegar si crea un consorcio al que se unirán otras organizaciones. Encontrará más información sobre los clasificadores en la [visión general de los componentes de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-orderer).

La importación de un clasificador en la consola le permite crear nuevos canales para que los iguales realicen transacciones en privado.

### Antes de empezar
{: #ibp-console-import-orderer-before-you-begin}

- Asegúrese de que ya ha importado el archivo JSON de identidad de administrador del clasificador en la cartera de la consola.
- Asegúrese de que el archivo JSON del clasificador que se ha exportado desde la consola donde se ha creado está disponible.

### Cómo importar un clasificador
La importación de un clasificador se realiza desde el separador **Nodos**. Pulse **Añadir clasificador** `+` en la sección de clasificador y luego pulse **Importar un clasificador existente**. Siga los pasos del panel lateral que se abre para especificar manualmente la información de conexión.
- Pulse el botón **Cargar JSON** para omitir la entrada manual de la información y navegar hasta el archivo JSON que se ha exportado desde la consola en la que se ha creado el clasificador.
- Establezca la identidad de administrador para el clasificador pulsando **Identidad existente** y seleccionando la identidad de administrador del clasificador desde la cartera de la consola.

Una vez que haya importado el clasificador en la consola, puede añadir nuevos miembros de la organización y seleccionar el clasificador al crear nuevos canales.

## Importación de un igual
{: #ibp-console-import-peer}

Un nodo igual es el componente blockchain que mantiene un libro mayor y ejecuta un contrato inteligente para realizar las operaciones de consulta y actualización en el libro mayor. Los miembros de la organización poseen y mantienen los iguales.  Cada organización que se une a un consorcio debe desplegar al menos un igual. Encontrará más información sobre los iguales en la [visión general de los componentes de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-peer).  

Después de importar un igual en la consola, puede instalar contratos inteligentes en el igual y unir el igual a otros canales de su blockchain.

**Nota:** si necesita añadir más iguales a la organización igual o crear identidades de administrador adicionales para un igual, tiene que importar la CA del igual y luego utilizar dicha CA para realizar dichas operaciones.

### Antes de empezar
{: #ibp-console-import-peer-before-you-begin}

Antes de importar un igual, debe recopilar la información y los certificados siguientes:

- Asegúrese de que ya ha importado el archivo JSON de identidad de administrador del igual en la cartera de la consola.
- Asegúrese de que el archivo JSON del igual que se ha exportado desde la consola donde se ha creado está disponible.

### Cómo importar un igual

La importación de un igual se realiza desde el separador **Nodos**. Pulse **Añadir igual** `+` en la sección de iguales y luego pulse **Importar un igual existente**. Siga los pasos del panel lateral que se abre para especificar la información de conexión.
- Pulse el botón **Cargar JSON** para omitir la entrada manual de la información y navegar hasta el archivo JSON que se ha exportado desde la consola en la que se ha creado el igual.
- Establezca la identidad de administrador para el igual pulsando **Identidad existente** y seleccionando la identidad de administrador del igual desde la cartera de la consola.

Después de importar el igual en la consola, puede instalar contratos inteligentes en el igual y unir el igual a los canales de su blockchain.


<!-- ## Importing an MSP -->
