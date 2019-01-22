---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Herramientas para contratos inteligentes
{: #overview}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


La extensión de Visual Studio Code de la plataforma {{site.data.keyword.blockchainfull}} proporciona un entorno dentro de Visual Studio Code para desarrollar, empaquetar y desplegar paquetes de contratos inteligentes. La extensión incluye también mandatos para configurar una instancia local preconfigurada de Hyperledger Fabric para un desarrollo de contratos inteligentes locales simplificado.

**Tenga en cuenta lo siguiente:** la extensión de la plataforma {{site.data.keyword.blockchainfull_notm}} es compatible con las redes del Plan inicial de la plataforma {{site.data.keyword.blockchainfull_notm}}, así como con Hyperledger Fabric 1.3.x.

## Requisitos previos

Antes de instalar la extensión de Visual Studio Code de la plataforma {{site.data.keyword.blockchainfull_notm}}, complete los requisitos previos siguientes.

- **Instale Yeoman**

  Yeoman es una herramienta generadora utilizada para crear proyectos de contratos inteligentes de esqueleto. Instale Yeoman utilizando el mandato siguiente: `npm install -g yo`

- **Instale el generador de Fabric para Yeoman**

  Después de instalar Yeoman, instale el generador de Fabric para la creación de paquetes de contratos inteligentes de esqueleto. Instale el generador de Fabric utilizando el mandato siguiente: `npm install -g generator-fabric`

- **Instale Docker**

  Para ejecutar la instancia preconfigurada de Hyperledger Fabric, asegúrese de tener instalado
[Docker](https://www.docker.com/).

- **Requisitos del sistema operativo**

  Actualmente, esta extensión es compatible con Mac, Windows y Linux.

- **Requisitos de la versión de Hyperledger Fabric**

  Esta extensión es compatible con las versiones 1.3.0 y posteriores de Hyperledger Fabric.

- **Requisitos de la versión de Hyperledger Fabric**

  Esta extensión es compatible con las versiones 1.3.0 y posteriores de Hyperledger Fabric.


## Instalación de la extensión

1. Vaya a la [página de mercado de la extensión de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform) o busque **Plataforma {{site.data.keyword.blockchainfull_notm}}** en el panel de extensiones dentro de Visual Studio Code.
2. Pulse **Instalar**.
3. Reinicie Visual Studio Code para completar la instalación de la extensión.

## Paleta de mandatos de Visual Studio Code

Esta extensión añade muchos mandatos a la paleta de mandatos de Visual Studio Code. Muchas de las operaciones detalladas en este documento se pueden iniciar también ejecutando el mandato desde la paleta de mandatos de Visual Studio Code.

## Creación de un proyecto

Para crear un proyecto nuevo, siga estos pasos:

1. Abra la paleta de mandatos, ejecute el mandato **Blockchain: Create Smart Contract Project**.
2. Seleccione el lenguaje en el que desee crear un contrato inteligente; actualmente, las opciones son JavaScript o TypeScript.
3. Cree una carpeta con el nombre del proyecto y ábrala.
4. Seleccione cómo abrir el nuevo proyecto. Ahora debe abrirse la carpeta del proyecto.

## Conexión y desconexión de una red

Al utilizar esta extensión, puede instalar e instanciar paquetes de contratos inteligentes en iguales y canales de la instancia de Hyperledger Fabric. Esta extensión puede inicializar una instancia local preconfigurada de Hyperledger Fabric utilizando Docker.

### Conexión con la instancia preconfigurada de Hyperledger Fabric

Para conectarse a la instancia preconfigurada de Hyperledger Fabric, asegúrese en primer lugar de que Docker esté en ejecución en la máquina:

1. Abra el separador _Plataforma {{site.data.keyword.blockchainfull_notm}}_ en Visual Studio Code.
2. En el panel _Plataforma {{site.data.keyword.blockchainfull_notm}}_, pulse **local_fabric**. Si Docker está en ejecución, se debe descargar e iniciar la instancia local de Hyperledger Fabric.
3. Después de que se haya iniciado la instancia local de Hyperledger Fabric, realice una doble pulsación sobre
**local_fabric** para conectarse a ella. Ahora debería ver un canal denominado
`mychannel`.

#### Reinicio del entorno de ejecución preconfigurado de Hyperledger Fabric

Para reiniciar el entorno de ejecución de `local_fabric`:

1. Pulse el botón derecho del ratón sobre la conexión de `local_fabric` cuando se establezca.
2. Seleccione **Reiniciar entorno de ejecución de Fabric**.

Los contenedores de Hyperledger Fabric se detendrán y se reiniciarán.

#### Interrupción del entorno de ejecución de Hyperledger Fabric

Para interrumpir el entorno de ejecución de `local_fabric`:

1. Pulse el botón derecho del ratón sobre la conexión de `local_fabric` cuando se establezca.
2. Seleccione **Interrumpir el entorno de ejecución de Fabric**.

Al provocar la interrupción de la red de `local_fabric` cerrará todos los contenedores de Hyperledger Fabric. **Tenga en cuenta lo siguiente**: esto dará lugar a la pérdida de los datos de libro mayor y de escenario mundial.

#### Habilitación de la modalidad de desarrollo en el entorno de ejecución preconfigurado de Hyperledger Fabric

Con el funcionamiento normal, un igual creará y mantendrá un contenedor de código de encadenamiento para ejecutar contratos inteligentes instanciados. Al cambiar a la modalidad de desarrollo, el igual permite que el contenedor de código de encadenamiento se ejecute manualmente. La ejecución manual del contenedor de código de encadenamiento, en la línea de mandatos o en un terminal, resulta de ayuda para el desarrollo iterativo y la depuración de contratos inteligentes.

Para habilitar la modalidad de desarrollo con el entorno de ejecución preconfigurado de Hyperledger Fabric:

1. Pulse el botón derecho del ratón sobre la conexión de `local_fabric` cuando se establezca.
2. Seleccione **Conmutar modalidad de desarrollo**.

Es necesario habilitar la modalidad de desarrollo para poder utilizar la característica de depuración de la extensión.

### Conexión con el Plan inicial de la plataforma {{site.data.keyword.blockchainfull_notm}}

La extensión de Visual Studio Code de la plataforma {{site.data.keyword.blockchainfull_notm}} se puede utilizar para conectarse a una instancia del Plan inicial de la plataforma {{site.data.keyword.blockchainfull_notm}}. Tras establecer la conexión, la extensión se puede utilizar para desarrollar y desplegar contratos inteligentes.

**Nota:** actualmente la extensión no es compatible con el Plan empresarial de la plataforma
{{site.data.keyword.blockchainfull_notm}}.

1. Si no tiene una instancia del Plan inicial de la plataforma {{site.data.keyword.blockchainfull_notm}}, cree una
[aquí](https://console.bluemix.net/catalog/services/blockchain).
2. Abra la interfaz de usuario de IBP pulsando **Iniciar**.
3. Recupere el perfil de conexión pulsando **Visión general** > **Perfil de conexión** > **Descargar**.
4. Genere los certificados de administración necesarios pulsando **Entidad emisora de certificados** > **Generar certificado** y guardando el certificado y la clave privada en el sistema de archivos.
5. Añada los certificados a la instancia del Plan inicial de la plataforma {{site.data.keyword.blockchainfull_notm}} pulsando **Miembros** > **Certificados** > **Añadir certificado**, y seleccionando el certificado creado en el paso anterior.
6. En Visual Studio Code, abra la vista de la extensión de la plataforma {{site.data.keyword.blockchainfull_notm}} y pulse **Añadir conexión nueva**.
7. Especifique el nombre de la conexión y la vía de acceso al perfil de conexión, el certificado y la clave privada.

### Conexión con su propia instancia de Hyperledger Fabric

Mediante el uso de esta extensión, puede conectarse a una instancia local preconfigurada de Hyperledger Fabric, o puede conectarse a su propia instancia de Hyperledger Fabric. Si decide conectarse a su propia instancia de Fabric instance, debe ser Fabric 1.3.0 o posterior.

Para conectarse a su propia instancia de Hyperledger Fabric, realice los pasos siguientes:

1. Abra el separador _Plataforma {{site.data.keyword.blockchainfull_notm}}_ en Visual Studio Code.
2. En el panel _Plataforma {{site.data.keyword.blockchainfull_notm}}_, pulse **Añadir conexión nueva**.
3. Especifique el nombre de la conexión. Este nombre se visualizará en el panel _Plataforma {{site.data.keyword.blockchainfull_notm}}_.
4. Especifique la vía de acceso de archivo completa del perfil de conexión de Hyperledger Fabric.
5. Especifique la vía de acceso de archivo completa del certificado para su identidad.
6. Especifique la vía de acceso de archivo completa de la clave privada para su uso con el certificado proporcionado en el paso anterior.
7. La conexión no debe aparecer en la lista de conexiones bajo `local_fabric`. Realice una doble pulsación sobre el nombre de conexión para conectarse.

Si no tiene una instancia preexistente de Hyperledger Fabric de la versión 1.3.0 o posterior, puede conectarse utilizando la instancia preconfigurada de Hyperledger Fabric, o ejecutar los mandatos siguientes para extraer y etiquetar las imágenes de la versión 1.3.0:

```
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable hyperledger/fabric-ca
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable hyperledger/fabric-orderer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable hyperledger/fabric-peer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable hyperledger/fabric-tools
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable hyperledger/fabric-ccenv
```
{:codeblock}

### Desconexión de una red

Cuando está conectado a una red, puede cerrar la conexión pulsando sobre el icono de desconexión situado en la parte superior derecha del panel Conexiones de Blockchain. También puede desconectarse ejecutando el mandato **Desconectar de blockchain** de la paleta de mandatos.

## Edición o supresión de una conexión

Las conexiones se pueden editar o suprimir en el panel de conexiones.

### Edición de una conexión

Al editar una conexión, puede cambiar la vía de acceso de archivo del perfil de conexión, el nombre proporcionado para la conexión y las vías de acceso de archivo a los certificados de identidad y claves privadas.

Para editar una conexión:

1. Desde la extensión, pulse el botón derecho del ratón sobre la conexión a editar en la parte inferior izquierda; esto abre un menú contextual con opciones para añadir una identidad, editar la conexión o suprimir la conexión.
2. Seleccione **Editar conexión**.
3. Se abrirá la página _Valores de usuario_, con los detalles de la conexión resaltados.
4. Realice los cambios que desee y guarde la página de valores.

### Supresión de una conexión

Las conexiones se pueden suprimir de la siguiente manera:

1. Desde la extensión, pulse el botón derecho del ratón sobre la conexión a editar en la parte inferior izquierda; esto abre un menú contextual con opciones para añadir una identidad, editar la conexión o suprimir la conexión.
2. Seleccione **Suprimir conexión**.
3. Aparecerá un recuadro de diálogo para confirmar la supresión de la conexión. Pulse **Sí**.

La conexión se suprimirá.

## Adición de identidades

Al añadir una conexión, debe enviar un perfil de conexión, un certificado y una clave privada. La identidad asociada con el par certificado/clave privada se establecerá como la identidad predeterminada a utilizar cuando se establezca la conexión con la instancia de Hyperledger Fabric.

Para añadir una identidad a una conexión que ya está establecida:

1. Desde la extensión, pulse el botón derecho del ratón sobre la conexión a editar en la parte inferior izquierda; esto abre un menú contextual con opciones para añadir una identidad, editar la conexión o suprimir la conexión.
2. Seleccione **Añadir una identidad**.
3. Proporcione las vías de acceso de archivo de los certificados de identidad y la clave privada.

## Exploración de conexiones

Después de conectarse a una instancia de Hyperledger Fabric, puede ver una lista de los canales disponibles y de los iguales en dichos canales en el panel _Plataforma {{site.data.keyword.blockchainfull_notm}}_. Dedique un minuto a explorar la instancia de Hyperledger Fabric para familiarizarse con la estructura. En primer lugar aparecen los canales dentro de una conexión, bajo un canal hay una lista de iguales que son miembros de dicho canal, y una lista de paquetes de contratos inteligentes instanciados en dicho canal. Bajo cada igual de la lista, hay una lista de todos los paquetes de contratos inteligentes instalados en dicho igual. También puede ver las versiones de los paquetes de contratos inteligentes que hay instalados o instanciados.

### Exportación de detalles de conexión para el entorno de ejecución preconfigurado de Hyperledger Fabric

Los detalles de conexión necesarios para conectarse a la conexión de `local_fabric` se pueden exportar. Los detalles de conexión de la conexión de `local_fabric` resultan útiles para probar aplicaciones cliente diseñadas para conectarse o interactuar con la instancia de Hyperledger Fabric.

Para exportar los detalles de conexión de `local_fabric`:

1. Inicie el entorno de ejecución preconfigurado de Hyperledger Fabric.
2. Pulse el botón derecho del ratón sobre la conexión de `local_fabric` y seleccione **Exportar detalles de conexión**.

Los detalles de conexión se guardan en un directorio denominado `local_fabric`, incluido en el directorio del proyecto actual.

## Empaquetado de un contrato inteligente

Cuando un contrato inteligente está listo para su despliegue, en primer lugar se debe empaquetar. Para empaquetar un contrato inteligente, realice los pasos siguientes:

1. En Visual Studio Code, vaya al panel _Plataforma {{site.data.keyword.blockchainfull_notm}}_.
2. En el panel _Paquetes de contratos inteligentes_, pulse el icono +. Si tiene un proyecto de contrato inteligente abierto en el visor de archivos, se empaquetará automáticamente y se mostrará en el panel _Paquetes de contratos inteligentes_. Si tiene varias carpetas de contratos inteligentes abiertas, se le preguntaré cuál desea empaquetar. Si no tiene carpetas de contratos inteligentes abiertas, obtendrá un mensaje de error.

## Instalación de paquetes de contratos inteligentes

Tras conectarse a una instancia de Hyperledger Fabric, puede instalar e instanciar paquetes de contratos inteligentes en los iguales.

1. En el panel _Plataforma {{site.data.keyword.blockchainfull_notm}}_, vaya al igual en el que desee instalar el paquete de contrato inteligente.
2. Pulse el botón derecho del ratón sobre el igual en el que desee instalar el paquete de contrato inteligente y seleccione
**Instalar contrato inteligente**.

## Creación de instancias de paquetes de contratos inteligentes

Para comenzar la ejecución en un canal, en primer lugar es necesario instanciar un paquete de contrato inteligente instalado.

1. Conéctese a la instancia de Hyperledger Fabric en la que está instalado el paquete de contrato inteligente.
2. Pulse el botón derecho del ratón sobre el canal en el que desea instanciar el paquete de contrato inteligente y pulse
**Crear instancia de contrato inteligente**.
3. Seleccione el paquete de contrato inteligente y la versión que se va a instanciar. El paquete de contrato inteligente debe estar instalado en un igual que sea miembro de este canal.
4. Especifique el nombre de la función de creación de instancias en el contrato inteligente.
5. Especifique los argumentos que necesite la función de creación de instancias.

## Exportación o supresión de un paquete de contrato inteligente

Después de que se haya empaquetado un contrato inteligente, se puede exportar como un archivo
`.cds` o se puede suprimir si ya no es necesario.

Para suprimir un paquete de contrato inteligente:

1. En el panel de la extensión de la plataforma {{site.data.keyword.blockchainfull_notm}}, pulse el botón derecho del ratón sobre el paquete de contrato inteligente que desee suprimir.
2. Seleccione **Suprimir paquete**.

El paquete se suprimirá ahora y desaparecerá de la lista de paquetes de contratos inteligentes.

Para exportar un paquete de contrato inteligente:

1. En el panel de la extensión de la plataforma {{site.data.keyword.blockchainfull_notm}}, pulse el botón derecho del ratón sobre el paquete de contrato inteligente que desee exportar.
2. Seleccione **Exportar paquete**.
3. Elija el directorio en el que guardar el archivo del paquete de contrato inteligente y pulse **Exportar**.

## Prueba de un contrato inteligente instanciado

Se pueden generar pruebas para un contrato inteligente después de que se haya instanciado el contrato inteligente. Las pruebas se pueden generar como JavaScript o TypeScript, y se pueden ejecutar o depurar.

Para generar pruebas de contratos inteligentes:

1. Asegúrese de que se haya creado una instancia del contrato inteligente.
2. En **Contratos inteligentes instanciados**, pulse el botón derecho del ratón sobre el contrato inteligente para el que desee generar pruebas.
3. Seleccione **Generar pruebas de contrato inteligente**.
4. Seleccione ahora el lenguaje del archivo de pruebas, ya sea **JavaScript** o **TypeScript**. La extensión de la plataforma {{site.data.keyword.blockchainfull_notm}} instalará ahora los módulos npm necesarios y compilará el archivo de pruebas.

Después de que se haya compilado el archivo de pruebas, se pueden ejecutar las pruebas pulsando el botón **Ejecutar pruebas** en el archivo.

## Depuración de un contrato inteligente utilizando el entorno de ejecución preconfigurado de Hyperledger Fabric

La depuración de un contrato inteligente le permite realizar la ejecución a través de las transacciones de contrato inteligente con puntos de interrupción y salida, garantizando que las transacciones funcionan según lo previsto. Para depurar el contrato inteligente:

1. Asegúrese de estar conectado a la conexión de `local_fabric` que está en modalidad de desarrollo.
2. Abra el proyecto de contrato inteligente.
3. Abra la vista de depuración en Visual Studio Code utilizando la barra de navegación situada a la izquierda.
4. Seleccione la configuración Depurar contrato inteligente utilizando la lista desplegable de la parte superior izquierda.
5. Empaquete e instale el contrato inteligente pulsando el botón **reproducir**.
6. Añada puntos de interrupción al contrato inteligente pulsando sobre los números de línea relevantes en los archivos de contrato inteligente.
7. Pulse el botón derecho del ratón sobre el contrato inteligente instalado y seleccione **Crear instancia**. Ahora puede pulsar el botón derecho del ratón para enviar transacciones y la ejecución se detendrá en los puntos de interrupción definidos.

Para realizar cambios en el contrato inteligente durante la depuración, pulse el botón **reiniciar** después de realizar los cambios en el contrato inteligente. El reinicio de la depuración implica que no necesitará volver a crear una instancia del contrato.

**Tenga en cuenta lo siguiente**: el reinicio de la depuración almacena el contrato inteligente en la memoria local; si realiza un gran número de cambios en contratos inteligentes de gran tamaño, es posible que necesite volver a crear la instancia del contrato inteligente.

## Actualización de un contrato inteligente instanciado

Después de que se haya instalado un contrato inteligente en un igual y se haya creado una instancia en un canal, se puede actualizar para desplegar una versión más reciente del contrato inteligente.

1. Asegúrese de que el contrato inteligente que desea actualizar está instanciado.
2. Instale la nueva versión del contrato inteligente en un igual de la misma red.
3. Pulse el botón derecho del ratón sobre el contrato inteligente instanciado y seleccione **Actualizar contrato inteligente**.
4. De forma opcional, elija una transacción que ejecutar cuando se cree una instancia del nuevo contrato inteligente.

## Envío de transacciones

Después de que se haya instalado y creado una instancia de un contrato inteligente, se pueden enviar las transacciones desde el panel de conexiones del panel de la extensión de la plataforma {{site.data.keyword.blockchainfull_notm}}.

Para enviar una transacción:

1. Asegúrese de que se haya instalado y creado una instancia del contrato inteligente, y de estar conectado a la red.
2. En el panel de conexiones, expanda **Contratos inteligentes instanciados**.
3. Expanda el contrato inteligente que contiene la transacción que se va a enviar.
4. Pulse el botón derecho del ratón sobre la transacción que se va a enviar y seleccione **Enviar transacción**.
5. Especifique los argumentos necesarios para la transacción y pulse **Intro**.
