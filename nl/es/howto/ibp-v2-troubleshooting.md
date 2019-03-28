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
{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:pre: .pre}

# Resolución de problemas
{: #ibp-v2-troubleshooting}

Es posible que se produzcan problemas generales al utilizar la consola para gestionar nodos, canales o contratos inteligentes. En muchos de los casos, puede solucionar estos problemas siguiendo unos sencillos pasos.

## ¿Por qué fallan mis operaciones de nodo después de crear el igual o el clasificador?
{: #ibp-console-build-network-troubleshoot-entry1}

Es posible que reciba un error al gestionar un nodo existente. Cuando esto ocurre, suele resultar útil consultar los registros del nodo.  

Por ejemplo, cuando intenta trabajar con el nodo, la acción puede fallar.
{: tsSymptoms}

Después de crear un nuevo igual o un nuevo clasificador, en función de la configuración del almacenamiento del clúster, se puede tardar unos minutos en que el nodo esté listo para su funcionamiento.
{: tsCauses}

Compruebe el panel de control de Kubernetes y asegúrese de que el estado del igual o del nodo sea `En ejecución`. A continuación, intente de nuevo la acción. Si sigue teniendo problemas después de que se active el nodo, [consulte los registros del nodo](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) para ver si hay errores.  
{: tsResolve}

## ¿Por qué no se inicia mi igual?
{: #ibp-console-build-network-troubleshoot-entry2}

Es posible que experimente este error bajo diversas condiciones.

El registro del igual incluye `2019-02-06 19:43:24.159 UTC [main] InitCmd -> ERRO 001 No se puede ejecutar el igual porque no se puede iniciar la criptografía, la carpeta “/certs/msp” no existe`
{: tsSymptoms}

- Se puede
producir este error bajo las siguientes condiciones:
  - Al crear la definición de MSP de la organización del igual o del clasificador, ha especificado un ID y un secreto de inscripción que corresponden a una identidad de tipo `peer` y no `client`. Debe ser de tipo `client`.
  - Al crear la definición de MSP de la organización del igual o del clasificador, ha especificado un ID y un secreto de inscripción que no coinciden con el ID y el secreto de inscripción de la identidad de administración de la organización correspondiente.
  - Al crear el igual o el clasificador, ha especificado el ID y el secreto de inscripción de una identidad que no es de tipo 'peer'.

- Abra el nodo de CA del igual o del clasificador y visualice las identidades registradas que se muestran en la tabla **Usuarios registrados**.
- Suprima el igual o el clasificador y vuelva a crearlo, especificando el ID y el secreto de inscripción correctos.
- Tenga en cuenta que, antes de crear el igual o el clasificador, debe crear un id de administrador de la organización de tipo 'client'. Asegúrese de especificar el mismo ID que el ID de inscripción al crear la definición de MSP de la organización. Consulte estas instrucciones para [registrar identidades del igual](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-org1) y estas instrucciones para [registrar identidades del clasificador](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-orderer).
{: tsResolve}

## ¿Por qué ha fallado la instalación, la creación de una instancia o la actualización de mi contrato inteligente?
{: #ibp-console-smart-contracts-troubleshoot-entry1}

Es posible que reciba un error al instalar, al crear una instancia o al actualizar un contrato inteligente.  Por ejemplo, cuando intenta instalar un contrato inteligente en un igual, falla con el error `Se ha producido un error al instalar el contrato inteligente en el igual`.
{: tsSymptoms}

Puede recibir este error si esta versión del contrato inteligente ya existe en el igual, o si el igual se ha quedado sin recursos.
{: tsCauses}

- Abra el panel de control de Kubernetes y asegúrese de que el estado del igual sea `En ejecución`.  
- Abra el nodo igual y asegúrese de que la versión del contrato inteligente no exista aún en el igual y vuelva a intentarlo con la versión adecuada.
- Si sigue teniendo problemas después de que se active el nodo, [consulte los registros del nodo](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) para ver si hay errores.  
{: tsResolve}

## ¿Cómo puedo ver los registros del contenedor del contrato inteligente?
{: #ibp-console-smart-contracts-troubleshoot-entry2}

Es posible que tenga que ver el contrato inteligente, o el código de encadenamiento, y los registros del contenedor para depurar un problema del contrato inteligente.
{: tsSymptoms}

Siga estas instrucciones para [ver los registros del contenedor](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-container-logs).
{: tsResolve}

## Mi canal, los contratos inteligentes y las identidades han desaparecido de la consola. ¿Cómo puedo recuperarlos?
{: #ibp-v2-troubleshooting-browser-storage}

Las identidades de la cartera de la consola constan de un par de claves pública y privada que le permiten gestionar los componentes de blockchain, pero solo se almacenan en el almacenamiento local del navegador. Usted es el responsable de proteger y gestionar estas identidades. Le recomendamos que las exporte al sistema de archivos después de crearlas. Siempre que crea un nodo nuevo, asocia una identidad de la cartera de la consola al nodo. Esta identidad de administrador es lo que le permite gestionar el nodo. Cuando cambia de navegador navegadores o cambia a un navegador en otra máquina, estas identidades ya no se encuentran en la cartera. Por lo tanto, no puede gestionar los componentes.
{: tsSymptoms}

Una de las características nuevas de {{site.data.keyword.blockchainfull_notm}} Platform 2.0 es que ahora usted es el responsable de proteger y gestionar los certificados. Por lo tanto, solo se mantienen en el almacenamiento local del navegador para permitirle gestionar el componente.
{: tsCauses}

- Cada vez que cree una nueva definición de MSP de la organización, genera claves para una identidad que tiene permiso para administrar la organización. Por lo tanto, durante este proceso debe pulsar los botones **Generar** y luego **Exportar** para almacenar la identidad generada en la cartera de la consola y, a continuación, guardarla en el sistema de archivos como un archivo JSON.
- Para resolver este problema en el navegador, tiene que importar dichas identidades y asociarlas con el nodo correspondiente:
  - En el navegador donde está experimentando el problema, pulse el separador **Cartera** seguido de **Añadir identidad** para importar el archivo JSON en la cartera.
  - Pulse **Cargar JSON** y examine el archivo JSON que ha exportado utilizando el botón **Añadir archivos**.
  - Pulse **Enviar**.
  - Ahora abra el nodo igual o clasificador al que estaba asociada originalmente esta identidad y pulse el icono **Valores**.
  - Pulse el botón **Asociar identidad**.
  - Seleccione la identidad que acaba de importar en la cartera de la consola desde la lista desplegable.
  - Pulse **Asociar**.
- Repita este proceso para cada identidad que había en la cartera del navegador original.
{: tsResolve}

## ¿Por qué recibo el error `Se ha producido un error al actualizar el canal` al intentar añadir una organización a mi canal?
{: #ibp-v2-troubleshooting-update-channel}

Cuando intenta añadir otra organización a un canal, la actualización falla con el mensaje `Se ha producido un error al actualizar el canal`.
{: tsSymptoms}

Este error se produce cuando el **ID de MSP del actualizador del canal** seleccionado en el panel **Actualizar canal** no es un administrador del canal.
{: tsCauses}

En el panel **Actualizar canal**, desplácese hacia abajo hasta el **ID de MSP del actualizador del canal**
y seleccione el ID de MSP que se ha especificado al crear el canal o especifique el ID de MSP del administrador del canal.
{: tsResolve}
