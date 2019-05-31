---

copyright:
  years: 2017, 2019
lastupdated: "2019-05-16"

keywords: vs code extension, Visual Studio Code extension, smart contract, development tools

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Desarrollo de contratos inteligentes con la extensión de Visual Studio Code
{: #develop-vscode}


La extensión de Visual Studio (VS) Code de {{site.data.keyword.blockchainfull}} Platform proporciona un entorno dentro de Visual Studio Code para desarrollar, empaquetar y probar contratos inteligentes. Puede utilizar la extensión para crear el proyecto de contrato inteligente y empezar a desplegar su lógica de negocio. A continuación, puede utilizar VS Code para probar el contrato inteligente en su máquina local utilizando una instancia preconfigurada de Hyperledger Fabric antes de desplegar el contrato inteligente en {{site.data.keyword.blockchainfull_notm}} Platform. Esta guía de aprendizaje describe cómo utilizar la extensión de VS Code.


![Flujo de trabajo de desarrollo de un contrato inteligente típico](images/SmartContractflow.png "Flujo de trabajo de desarrollo de un contrato inteligente típico")
*Figura 1. Flujo de trabajo de desarrollo de un contrato inteligente típico con la extensión de VS Code de
{{site.data.keyword.blockchainfull_notm}} Platform*  

<!--
<img usemap="#home_map1" border="0" class="image" id="image_ztx_crb_f1b2" src="images/SmartContractflow.png" width="750" alt="Click a box to get more details on the process." style="width:750px;" />
<map name="home_map1" id="home_map1">
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-creating-a-project" alt="Create a smart contract project" title="Create a Smart contract project" shape="rect" coords="157.05, 52.53, 283.62, 127.11" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-creating-a-project" alt="Develop contract code in VS Code" title="Create key pair" shape="rect" coords="300.97, 52.53, 427.54, 127.11" />
<area href="/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract" alt="Package the smart contract" title="Package the smart contract" shape="rect" coords="157.05, 131.8, 283.62, 206.37" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-deploy" alt="Deploy locally to debug" title="Deploy locally to debug" shape="rect" coords="300.97, 131.8, 427.54, 206.37" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-exporting-deleting-smart-contract-package" alt="Export the package" title="Export the package" shape="rect" coords="443.95, 131.8, 570.53, 206.37" />
<area href="/docs/services/blockchain/vscode-extension.html#develop-vscode-connecting-ibp" alt="Deploy to IBM Cloud" title="Deploy to IBM Cloud" shape="rect" coords="585.53, 131.8, 712.1, 206.37" />
-->

La extensión de {{site.data.keyword.blockchainfull_notm}} Platform funciona de manera completamente integrada con cualquier instancia de {{site.data.keyword.blockchainfull_notm}} Platform que utilice versiones de Hyperledger Fabric 1.4 y posteriores.
{: note}

## Paso uno: instalar la extensión de VS Code de {{site.data.keyword.blockchainfull_notm}} Platform de forma gratuita
{: #develop-vscode-install}

Antes de instalar la extensión de VS Code de {{site.data.keyword.blockchainfull_notm}} Platform, debe completar los requisitos previos.

### Requisitos previos
{: #develop-vscode-prerequisites}

- **Instale Visual Studio Code**  
  Instale el editor de código de [Visual Studio](https://code.visualstudio.com/).  
- **Instale Yeoman**  
  Yeoman es una herramienta generadora que puede utilizar para crear proyectos de contratos inteligentes de esqueleto. Instale Yeoman utilizando el mandato siguiente: `npm install -g yo`.  
- **Instale Docker**  
  Para ejecutar la instancia preconfigurada de Hyperledger Fabric, asegúrese de haber instalado [Docker ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://www.docker.com/).  
- **Requisitos del sistema operativo**  
  Actualmente, esta extensión es compatible con Mac, Windows y Linux.  
- **Requisitos de la versión de Hyperledger Fabric**  
  Esta extensión es compatible con las versiones 1.4.0 y posteriores de Hyperledger Fabric.  

### Instalar la extensión
{: #develop-vscode-installing-the-extension}

1. Vaya a la [página de mercado de la extensión Visual Studio Code ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform) o busque **{{site.data.keyword.blockchainfull_notm}} Platform** en el panel de extensiones de Visual Studio Code.
2. Pulse **Instalar**.
3. Reinicie Visual Studio Code para completar la instalación de la extensión.

Tras la instalación, puede utilizar el icono {{site.data.keyword.blockchainfull_notm}} de la parte izquierda de VS Code para abrir el panel de {{site.data.keyword.blockchainfull_notm}} Platform.

La extensión añade también nuevos mandatos a la paleta de mandatos de Visual Studio Code. Puede utilizar la paleta de mandatos para completar muchas de las operaciones que se explican en detalle en esta guía.

## Paso dos: crear un proyecto de contrato inteligente
{: #develop-vscode-creating-a-project}

Puede utilizar la extensión para crear un nuevo proyecto de contrato inteligente en Visual Studio Code. La extensión crea un contrato inteligente básico que gestiona un activo de ejemplo en el lenguaje que prefiera. Puede utilizar la estructura del ejemplo como punto de partida para desarrollar su propia lógica empresarial. La extensión proporciona todas las dependencias necesarias para desplegar su contrato inteligente en una instancia de Hyperledger Fabric.

1. Abra el separador **{{site.data.keyword.blockchainfull_notm}}**. Pulse sobre el menú de desbordamiento del panel de paquetes de contratos inteligentes y pulse **Crear proyecto de contrato inteligente**.
2. Seleccione el lenguaje en el que desee crear un contrato inteligente. Las opciones actuales son JavaScript, TypeScript, Go y Java. **Nota:** {{site.data.keyword.blockchainfull_notm}} Platform no admite código de encadenamiento Java.
3. Seleccione un activo que se vaya a gestionar mediante el contrato de ejemplo. Por ejemplo, ***bond***.
4. Cree una carpeta con el nombre del proyecto y ábrala.
5. Seleccione cómo abrir el nuevo proyecto. Ahora debe abrirse la carpeta del proyecto.

Cuando se abra el proyecto, podrá encontrar el nuevo contrato inteligente en la ventana del explorador del panel de la izquierda. La estructura del proyecto depende del lenguaje que haya seleccionado. No obstante, cada contrato inteligente contiene los mismos elementos:
- El código fuente del contrato inteligente. La extensión crea un contrato inteligente básico utilizando la API
`fabric-contract-api` con una serie de funciones que gestionan su activo de ejemplo. Por ejemplo, si ha seleccionado
***bond***, encontrará las funciones
`createBond`, `updateBond`, `readBond`, `bondExists` y
`deleteBond`.
- Un archivo de prueba.
- Las dependencias que acompañan al contrato inteligente.

## Paso tres: empaquetar un contrato inteligente
{: #packaging-a-smart-contract}

Necesita empaquetar un contrato inteligente en formato `.cds` para poder instalarlo en su red de
{{site.data.keyword.blockchainfull_notm}} Platform o en la red preconfigurada de Hyperledger Fabric. Siga los pasos siguientes para empaquetar el contrato inteligente:

1. En VS Code, vaya al panel **{{site.data.keyword.blockchainfull_notm}} Platform**. Asegúrese de tener un proyecto de contrato inteligente abierto en el visor de archivos.
2. En el panel **Paquetes de contratos inteligentes**, pulse **+**.
  - Si tiene un proyecto de contrato inteligente, se empaquetará automáticamente y se mostrará en el panel
**Paquetes de contratos inteligentes**.
  - Si tiene varias carpetas de contratos inteligentes abiertas, se le preguntaré cuál desea empaquetar.
  - Si no tiene carpetas de contratos inteligentes abiertas, obtendrá un mensaje de error.

### Exportación, importación y supresión de un paquete de contratos inteligentes
{: #develop-vscode-exporting-deleting-smart-contract-package}

Tras empaquetar un proyecto de contrato inteligente, puede exportarlo desde VS Code:

1. En el panel de la extensión de {{site.data.keyword.blockchainfull_notm}} Platform, pulse el botón derecho del ratón sobre el paquete de contrato inteligente y seleccione **Exportar paquete**.
2. Elija el directorio en el que guardar el archivo del paquete de contrato inteligente y pulse **Exportar**.

También puede importar un paquete de contrato inteligente existente en el panel
{{site.data.keyword.blockchainfull_notm}} Platform:

1. En el panel **Paquetes de contratos inteligentes**. Pulse sobre el menú de desbordamiento y seleccione
**Importar paquete**.
2. Vaya hasta el paquete de contrato inteligente que desee importar y pulse **Importar**.

También puede pulsar **Suprimir paquete** para eliminar el paquete de contrato inteligente de la lista de paquetes.

## Paso cuatro: desplegar un contrato inteligente en una red preconfigurada de Hyperledger Fabric
{: #develop-vscode-deploy}

Puede utilizar VS Code para desplegar el contrato inteligente en una red preconfigurada de Hyperledger Fabric que la extensión cree en la máquina local. Esto le permite instalar, instanciar y probar el contrato inteligente antes de desplegarlo en una red activa.

### Despliegue de la red preconfigurada de Hyperledger Fabric
{: #develop-vscode-connecting-and-disconnecting}

Utilice los pasos siguientes para desplegar la red preconfigurada:

1. Asegúrese de que Docker esté en ejecución en la máquina.
2. Abra el separador **{{site.data.keyword.blockchainfull_notm}} Platform** en VS Code.
3. En el panel **Operaciones locales de Fabric**, pulse **tiempo de ejecución de fabric local**. Si Docker está en ejecución, se debe descargar e iniciar la instancia local de Hyperledger Fabric.
4. Realice una doble pulsación en **local_fabric** en el panel **Pasarelas de Fabric** para conectarse a la red local. De forma predeterminada, la conexión utiliza la identidad de administrador en el panel Carteras de Fabric. Puede crear una nueva identidad pulsando con el botón derecho del ratón sobre el nodo de la entidad emisora de certificados en el panel
**Operaciones locales de Fabric**. A continuación, se puede añadir esta nueva identidad a una cartera y se puede asociar con la conexión **local_fabric**.

La extensión de VS Code crea una red básica de Fabric que incluye un clasificador, un igual y una entidad emisora de certificados. El igual se une a un canal denominado `mychannel`. Puede obtener la lista de nodos, organizaciones y canales que pertenecen a la red en el panel
**Operaciones locales de Fabric**. Encima de estos nodos, puede encontrar la lista de contratos inteligentes que se han instalado e instanciado.

### Detención, reinicio y eliminación de la red preconfigurada
{: #develop-vscode-stop-Fabric-runtime}

Puede detener o reiniciar la red preconfigurada una vez que se haya creado:

1. En el panel **Operaciones locales de Fabric**, pulse sobre el menú de desbordamiento.
2. Seleccione **Reiniciar entorno de ejecución de Fabric** o **Detener entorno de ejecución de Fabric** para reiniciar o detener el contenedor.

Los detalles de conexión se guardan en un directorio denominado **local_fabric**, incluido en el directorio del proyecto actual. También puede seleccionar **Interrumpir el entorno de ejecución de Fabric** para eliminar completamente la red local de Fabric. **Nota:** esta eliminación provocará la pérdida de los datos de libro mayor y de escenario mundial.

### Despliegue del contrato inteligente en la red preconfigurada
{: #develop-vscode-deploy-smart-contract}

Puede desplegar cualquier paquete del panel **Paquetes de contratos inteligentes** en una red preconfigurada en ejecución.

En primer lugar, debe instalar el contrato inteligente en un igual:

1. En el panel **Operaciones locales de Fabric**, pulse **Instalar contrato inteligente**.
2. Seleccione el igual en el que desea instalar el contrato inteligente.
3. Seleccione el paquete de contrato inteligente que desee instalar y pulse **Instalar**.

A continuación, puede crear una instancia del contrato inteligente en un canal:

1. En el panel **Operaciones locales de Fabric**, pulse **Crear instancia de contrato inteligente**.
2. Seleccione el contrato inteligente instalado del que desee crear una instancia.
3. (Opcional) Especifique el nombre de la función de creación de instancias en el contrato inteligente. Si ha utilizado la plantilla predeterminada de contrato inteligente, no se utiliza ninguna función de creación de instancias.
4. (Opcional) Especifique los argumentos que necesite la función de creación de instancias.
5. (Opcional) Vaya al archivo de configuración de la recopilación si el contrato inteligente utiliza datos privados.
6. Pulse **Crear instancia**.

Si realiza cambios en el código del contrato inteligente y vuelve a empaquetarlo, puede actualizar el contrato inteligente instanciado para desplegar una versión más reciente en la red:

1. Asegúrese de que se haya creado una instancia del contrato inteligente que desee actualizar.
2. Instale la nueva versión del contrato inteligente en un igual de la misma red.
3. Pulse el botón derecho del ratón sobre el contrato inteligente instanciado y seleccione **Actualizar contrato inteligente**.
4. (Opcional) Ejecute una transacción después de que se haya creado una instancia del nuevo contrato inteligente.

### Interacción con el contrato inteligente
{: #develop-vscode-submitting-transactions}

Después de que se haya instalado e instanciado un contrato inteligente, puede enviar transacciones a las funciones que incluye el contrato inteligente utilizando el panel **Pasarelas de Fabric**:

1. Asegúrese de que se haya instalado y creado una instancia del contrato inteligente, y de estar conectado a la red.
2. En el panel de conexiones, expanda **Contratos inteligentes instanciados**.
3. Expanda el contrato inteligente con el que desee interactuar. Encontrará la lista de transacciones que aparecen junto al contrato inteligente.
4. Pulse el botón derecho del ratón sobre la transacción que se va a enviar y seleccione **Enviar transacción**. Por ejemplo, si ha creado y empaquetado el contrato inteligente de vínculos de ejemplo, pulse **createBond**.
5. Especifique los argumentos que requiera la transacción y pulse **Intro**. Por ejemplo, especifique `["bond01","100"]` para crear el primer vínculo.

### Conexión de las aplicaciones a la red preconfigurada
{: #develop-vscode-exploring-connection-details}

Puede probar las aplicaciones cliente conectándolas a la red preconfigurada y enviando transacciones al contrato inteligente.

En primer lugar, necesita exportar el perfil de conexión:

1. Inicie la red y expanda **Nodos** en el panel **Operaciones locales de Fabric**.
2. Pulse el botón derecho del ratón sobre el igual y seleccione **Exportar perfil de conexión**.

A continuación, puede utilizar los SDK de Fabric y el perfil de conexión para inscribir su identidad de administrador utilizando el nombre de usuario `admin` y la contraseña `adminpw`. A continuación, puede utilizar esta identidad para invocar el contrato inteligente o registrar e inscribir usuarios adicionales.

## Paso cinco: depurar contratos inteligentes utilizando la modalidad de desarrollo
{: #develop-vscode-development-mode}

Puede ejecutar la red preconfigurada en **Modalidad de desarrollo** para desarrollar y depurar de manera iterativa los contratos inteligentes de forma local, sin tener que volver a empaquetar ni actualizar el contrato inteligente después de cada cambio. La depuración de un contrato inteligente le permite realizar la ejecución a través de las transacciones de contrato inteligente con puntos de interrupción y salida, garantizando que las transacciones funcionan según lo previsto.

Utilice los pasos siguientes para habilitar la modalidad de desarrollo en la red preconfigurada:

1. Una vez que se haya iniciado la red, en el panel **Operaciones locales de Fabric**, expanda la sección
**Nodos**.
2. Pulse el botón derecho del ratón sobre el igual y seleccione **Conmutar modalidad de desarrollo**.

Con el funcionamiento normal, un igual crea y mantiene un contenedor de código de encadenamiento para ejecutar contratos inteligentes de los que se han creado instancias. Al cambiar a la modalidad de desarrollo, el igual permite que el contenedor de código de encadenamiento se ejecute manualmente, lo que le permite desplegar actualizaciones en los contratos inteligentes directamente en la red activa utilizando la vista
**Depuración** de VS Code.

1. Asegúrese de estar conectado a la conexión de **local_fabric** que está en modalidad de desarrollo.
2. Abra el proyecto de contrato inteligente.
3. Abra la vista **Depuración** en VS Code desde la barra de navegación de la izquierda.
4. Seleccione la configuración **Depurar contrato inteligente** en la lista desplegable de la parte superior izquierda.
5. Empaquete e instale el contrato inteligente pulsando el botón **reproducir**.
6. Añada puntos de interrupción al contrato inteligente pulsando sobre los números de línea relevantes en los archivos de contrato inteligente.
7. En la barra de herramientas de depuración, pulse el botón **Blockchain** para crear una instancia del contrato inteligente.
8. En la barra de herramientas de depuración, pulse el botón **Blockchain** para enviar o evaluar transacciones.

Para modificar el contrato inteligente durante la depuración, pulse el botón **Reiniciar** después de realizar cambios en el contrato inteligente. El reinicio de la depuración implica que no necesitará volver a crear una instancia del contrato.

## Paso seis: probar un contrato inteligente instanciado
{: #develop-vscode-testing-instantiated-smart-contract}

Puede generar pruebas para los contratos inteligentes para los que se haya creado una instancia en las redes a las que se conecte. Las pruebas se pueden generar como **JavaScript** o **TypeScript**, y se pueden ejecutar o depurar.

1. Asegúrese de que se haya creado una instancia del contrato inteligente.
2. En **Contratos inteligentes instanciados**, pulse el botón derecho del ratón sobre el contrato inteligente para el que desee generar pruebas.
3. Seleccione **Generar pruebas de contrato inteligente**.
4. Seleccione el lenguaje del archivo de pruebas, ya sea **JavaScript** o **TypeScript**. La extensión de {{site.data.keyword.blockchainfull_notm}} Platform instalará los módulos npm necesarios y compilará el archivo de pruebas.

Después de que se haya compilado el archivo de pruebas, se pueden ejecutar las pruebas pulsando el botón **Ejecutar pruebas** en el archivo.


## Paso siete: conectarse a la red de {{site.data.keyword.blockchainfull_notm}} Platform
{: #develop-vscode-connecting-ibp}

También puede utilizar la extensión para conectarse a {{site.data.keyword.blockchainfull_notm}} Platform e invocar los contratos inteligentes que se hayan instalado e instanciado utilizando la interfaz de usuario de la consola de
{{site.data.keyword.blockchainfull_notm}} Platform.

Abra la consola de {{site.data.keyword.blockchainfull_notm}} Platform que esté asociada a su instancia de
{{site.data.keyword.blockchainfull_notm}} Platform. Vaya al separador **Contratos inteligentes**. Utilice la tabla
**Contratos inteligentes instanciados** del separador Contratos inteligentes para descargar el
[perfil de conexión](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-profile) en el sistema de archivos local. A continuación, [cree una identidad de aplicación](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-identities) utilizando la CA y guarde el enrollID y el secreto. Siga los pasos siguientes para conectarse a
{{site.data.keyword.blockchainfull_notm}} Platform desde VS Code.

1. Abra el separador _{{site.data.keyword.blockchainfull_notm}} Platform_.
2. En el panel **Pasarelas de Fabric**, pulse **+**.
3. Especifique el nombre de la conexión.
4. Especifique la vía de acceso de archivo completa del perfil de conexión. La conexión debería aparecer en la lista de conexiones bajo **local_fabric**.
5. En el panel **Carteras de Fabric**, pulse **+**.
6. Elija **Crear una cartera nueva y añadir una identidad** en las opciones. Proporcione un nombre para la cartera y la identidad.
7. Especifique el ID de MSP de su organización.
8. Seleccione la opción **Seleccionar una pasarela y proporcionar un ID de inscripción y un secreto** y elija la pasarela que haya creado anteriormente.
9. Especifique el enrollID y el secreto de la identidad de aplicación que haya creado con la consola. Se creará una nueva identidad en el panel
**Carteras de Fabric**.
10. Ahora puede conectarse a su instancia de la red de {{site.data.keyword.blockchainfull_notm}} Platform. Realice una doble pulsación en el nombre de conexión y seleccione el nombre de la cartera que acaba de crear. También puede asociar la cartera que ha creado con la pasarela pulsando el botón derecho del ratón sobre la pasarela y seleccionando **Asociar una cartera**. Esto permite que la conexión utilice la misma cartera cada vez que se conecte.

Tras conectarse a {{site.data.keyword.blockchainfull_notm}} Platform desde VS Code, puede ver la lista de canales a los que se han unido los iguales de su organización en la pasarela. Bajo cada canal, puede ver la lista de contratos inteligentes que se han instanciado en cada canal y las funciones que incluye cada contrato inteligente. Puede enviar transacciones a la red pulsando con el botón derecho del ratón sobre una función, seleccionando **Enviar transacción** y pasando los argumentos necesarios. También puede generar un archivo de prueba para los contratos inteligentes instanciados en los canales.

### Adición de carteras y usuarios
{: #develop-vscode-add-a-wallet}

Utilice los pasos siguientes para crear una nueva cartera utilizando un certificado y una clave privada:

1. En el panel **Carteras de Fabric**, pulse **+**.
2. Elija **Crear una cartera nueva y añadir una identidad** en las opciones. Proporcione un nombre para la cartera y la identidad.
3. Especifique el ID de MSP de su organización.
4. Elija añadir un certificado y una clave privada.
5. Si utiliza un certificado y una clave privada, navegue hasta el certificado y la clave privada.

También puede añadir nuevos usuarios a las carteras que ya se hayan creado:

1. En el panel **Carteras de Fabric**, pulse el botón derecho del ratón sobre una cartera y seleccione
**Añadir identidad**.
2. Proporcione un nombre para la identidad y un MSPID.
3. Elija si desea utilizar certificado y clave privada o ID de inscripción y secreto.
4. Si utiliza un certificado y una clave privada, navegue hasta el certificado y la clave privada.
5. Si utiliza un ID de inscripción y un secreto, elija la pasarela con la realizar la inscripción y especifique el ID de inscripción y el secreto.

### Edición, desconexión y supresión de conexiones
{: #develop-vscode-editing-connection}

Desde la extensión, pulse el botón derecho del ratón sobre una conexión en la parte inferior izquierda para abrir un menú contextual con las opciones para añadir una identidad, editar la conexión o suprimir la conexión.

Para editar una conexión, realice los pasos siguientes:
1. Seleccione la opción **Editar conexión**. Se abrirá la página **Valores de usuario**, con los detalles de la conexión resaltados.
2. Realice los cambios que necesite y guarde la página de valores.

Cuando esté listo para desconectarse de la red, pulse el icono **Desconectar** de la parte superior derecha del panel
**Pasarelas de Fabric**.

Para suprimir una conexión, pulse el botón derecho del ratón sobre la conexión y seleccione **Suprimir pasarela**.
