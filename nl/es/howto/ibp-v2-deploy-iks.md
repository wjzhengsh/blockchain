---

copyright:
  years: 2019
lastupdated: "2019-04-23"

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

# Iniciación a {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta
{: #ibp-v2-deploy-iks}

{{site.data.keyword.blockchainfull}} Platform 2.0 es un release beta gratuito que incluye la consola de {{site.data.keyword.blockchainfull_notm}} Platform, una GUI que puede simplificar y acelerar el proceso de despliegue y gestión de componentes de blockchain. En esta guía de aprendizaje se describe cómo empezar a trabajar con {{site.data.keyword.blockchainfull_notm}} Platform 2.0 y cómo utilizar la consola para desplegar y gestionar componentes de blockchain en el clúster del servicio Kubernetes de {{site.data.keyword.cloud_notm}} en {{site.data.keyword.cloud_notm}}. Para obtener más información sobre Kubernetes y {{site.data.keyword.cloud_notm}} Kubernetes Service, consulte [Kubernetes](/docs/services/blockchain/reference/k8s.html "Kubernetes").
{:shortdesc}

**Audiencia de destino:** este tema está diseñado para los administradores de red responsables de configurar un clúster de Kubernetes en {{site.data.keyword.cloud_notm}} y de desplegar {{site.data.keyword.blockchainfull_notm}} Platform.

Después de enlazar {{site.data.keyword.blockchainfull_notm}} Platform al clúster de {{site.data.keyword.cloud_notm}} Kubernetes, puede iniciar la consola para crear y gestionar los componentes de blockchain. El uso de un clúster del servicio Kubernetes para desplegar {{site.data.keyword.blockchainfull_notm}} Platform 2.0 le ofrece las siguientes ventajas importantes:

- **Control:** puede controlar y gestionar los componentes de blockchain y los certificados desde una consola central. Despliegue solo los componentes necesarios para su negocio y añada más a medida que crezcan sus necesidades.
- **Despliegue flexible basado en Kubernetes:** puede aprovechar las opciones de cálculo (CPU, memoria, almacenamiento) del clúster de Kubernetes y las opciones integradas de HA y DR.


## Consideraciones
{: #ibp-v2-deploy-iks-considerations}

Antes de desplegar la consola, asegúrese de que comprende las consideraciones siguientes:

- {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta se basa en Hyperledger Fabric v1.4.
- Todos los iguales desplegados con la versión beta 2.0 gratuita utilizan CouchDB como base de datos de estado.
- Tiene la opción de utilizar un clúster de Kubernetes gratuito para la evaluación de la oferta beta; sin embargo, la capacidad y el rendimiento están limitados, no se pueden migrar datos y el clúster se suprime después de 30 días.
- El usuario es responsable de gestionar la supervisión del estado, la seguridad y el registro del clúster de Kubernetes. Consulte esta [información ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/docs/containers/cs_responsibilities.html#your-responsibilities-by-using-ibm-cloud-kubernetes-service "Responsabilidades de gestión del clúster") para ver detalles sobre lo que gestiona {{site.data.keyword.cloud_notm}} y sobre cuáles son sus responsabilidades.
- También es responsable de supervisar el uso de recursos del clúster de Kubernetes utilizando el panel de control de Kubernetes. Si tiene que aumentar la capacidad de almacenamiento o el rendimiento del clúster, consulte esta información sobre cómo [modificar el volumen existente ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/docs/containers/cs_storage_file.html#change_storage_configuration "Cambiar el tamaño y el IOPS del dispositivo de almacenamiento existente").
- Es responsable de gestionar y proteger sus certificados, sus claves públicas y privadas. IBM no almacena sus certificados en el clúster de Kubernetes.
- La oferta gratuita 2.0 beta solo está disponible en la región **Dallas** del servicio Kubernetes de {{site.data.keyword.cloud_notm}}. Esta región incluye centros de datos en Dallas, San José, Houston y Brasil. Por lo tanto, todos los componentes de blockchain pueden residir en cualquiera de estos cuatro centros de datos. No se despliegan en ningún otro sitio.
- Aunque la oferta beta es gratuita, tiene que pagar el clúster de Kubernetes, si elige un clúster de pago.
- Kubernetes debe tener la versión 1.11 o superior en el clúster de Kubernetes de {{site.data.keyword.cloud_notm}}. Utilice estas instrucciones para [actualizar los clústeres nuevos y existentes](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes) a esta versión.

## Vídeo de guía de aprendizaje
{: #ibp-v2-deploy-video}

Vea el vídeo siguiente para obtener más información sobre {{site.data.keyword.blockchainfull}} Platform gratuita 2.0 beta y sobre cómo empezar a trabajar para desplegar {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta.

<iframe class="embed-responsive-item" id="youtubeplayer" title="Vídeo de IBM Blockchain Platform gratuita 2.0 beta - guía de aprendizaje para el despliegue" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/gPnkVQiHRqk" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

## Antes de empezar
{: #ibp-v2-deploy-iks-prereq}

Antes de empezar:

- Asegúrese de que tiene una [cuenta de pago de {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/catalog/services/blockchain "Catálogo"). Si no tiene una cuenta:
   1. Pulse el botón **Registro**.
   2. Después de crear una cuenta de prueba gratuita, actualícela a un tipo **Pago según uso** yendo a **Gestionar** > **Facturación y uso** > **Facturación** en la consola de {{site.data.keyword.cloud_notm}} y pulsando **Añadir tarjeta de crédito**.
   3. Asegúrese de que el usuario tiene los roles de Administrador y Gestor para el clúster de Kubernetes al que van a enlazar su instancia de servicio de blockchain. Consulte estos pasos sobre [cómo asignar roles de acceso de Kubernetes](#ibp-v2-deploy-iks-k8x-access-roles) para obtener más información.

Cuando tenga intención de utilizar la instancia del servicio en el contexto de una solución más amplia a nivel de organización, se recomienda que las organizaciones participantes utilicen una dirección de correo electrónico funcional para crear su red. En este caso, el acceso a la red no depende de la disponibilidad de ninguna persona individual.
{:tip}  

- Si tiene previsto utilizar un clúster del servicio Kubernetes de {{site.data.keyword.cloud_notm}} existente, compruebe la versión de Kubernetes y actualice a la versión 1.11 o superior si es necesario. Para obtener más información sobre cómo determinar qué versión de Kubernetes está ejecutando el clúster y cómo actualizar la versión, consulte [Actualización de la versión de Kubernetes del clúster](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes).

### Navegadores
{: #ibp-v2-deploy-iks-browsers}
En la lista siguiente se especifica el software de navegador mínimo necesario para la consola de {{site.data.keyword.blockchainfull_notm}} Platform:

- Chrome: versión más reciente para el sistema operativo
- Firefox: versiones normales (no ESR) más recientes para el sistema operativo
- Safari: versión más reciente para Mac
- Edge: v44.17763.1.0 o superior

### Recursos necesarios
{: #ibp-v2-deploy-iks-resources-required}

#### Clústeres gratuitos
{: #ibp-v2-deploy-iks-resources-required-free}

Para desplegar la consola de {{site.data.keyword.blockchainfull_notm}} Platform en un clúster del servicio Kubernetes de {{site.data.keyword.cloud_notm}}, debe asegurarse de que el clúster de Kubernetes cumpla con los requisitos mínimos de recursos de hardware:

|Tipo de clúster de Kubernetes | Caso de uso | CPU | RAM | Nodos trabajadores |
|-----------|------|-----|-----------------------|
|Estándar (recomendado) | Adecuado para MVP | 4 (compartidas) | 16 GB (compartidos)|múltiples|
|Gratuito | Adecuado para evaluación | 2 | 4 GB | 1 |  

Estos recursos son suficientes para pruebas y experimentación. La [guía de aprendizaje Crear una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network), en la que creará dos iguales, dos CA y un clasificador, consume aproximadamente 1,1 CPU, lo que permite cierto espacio adicional para pruebas (por ejemplo, creando varios canales, cada uno de los cuales tendrá un libro mayor distinto). Si utiliza un clúster de Kubernetes gratuito, tenga en cuenta que el clúster se suprimirá después de la prueba de 30 días y que se eliminarán todos los activos asociados. Además, el rendimiento es significativamente más lento en un clúster gratuito.
{:note}

#### Clústeres de pago
{: #ibp-v2-deploy-iks-resources-required-paid}

Los despliegues a nivel de producción de {{site.data.keyword.blockchainfull_notm}} se realizarán en un clúster de pago del servicio
{{site.data.keyword.cloud_notm}} Kubernetes. El tamaño y la configuración de este clúster dependerán de las necesidades del caso de uso específico. Los despliegues de un tamaño mayor necesariamente deberán realizarse en clústeres más grandes. Cuánto mayor será el clúster que el despliegue proyectado dependerá de usted. Es deseable tener al menos un poco de espacio libre, ya que permitirá que los iguales y los clasificadores se puedan unir a canales adicionales y asumir una carga mayor sin tener que desplegar recursos adicionales en el clúster de Kubernetes **antes** de ajustar el tamaño de los nodos. Para obtener más información sobre cómo se ajustan estos valores, consulte
[Reasignación de recursos](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-reallocate-resources).

La creación de un despliegue inicial de tamaño suficiente para permitir el crecimiento es especialmente importante para usuarios que elijan no utilizar el [escalador automático del servicio {{site.data.keyword.cloud_notm}} Kubernetes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](/docs/containers?topic=containers-ca#ca "Escalador automático del servicio {{site.data.keyword.cloud_notm}}Kubernetes"), que puede asumir parte de la carga del despliegue de nodos y pods adicionales para el usuario.


Aunque es más fácil tener suficientes recursos desplegados en el servicio
{{site.data.keyword.cloud_notm}} Kubernetes y poder expandir los pods y los nodos de trabajador cuando sea necesario sin tener que aumentar primero el despliegue del clúster de Kubernetes, cuanto mayores sean los despliegues de clústeres de Kubernetes, mayor será el coste económico. Los usuarios deberán estudiar detenidamente sus opciones y reconocer qué están sacrificando independientemente de la opción que elijan.

Para tener una idea de cuánto almacenamiento y potencia de cálculo necesitará en su clúster, consulte este gráfico, que contiene los valores predeterminados actuales para el igual, el clasificador y la CA:

| **Componente** (todos los contenedores) | CPU (en milicpus) | CPU (en CPU) | Memoria (en megabytes) | Memoria (en gigabytes) | Almacenamiento (en gigabytes) |
|--------------------------------|--------------------|---------------|-----------------------|-----------------------|------------------------|
| **Igual**                       | 1100               | 1,1           | 2200                  | 2,2                   | 200                    |
| **CA**                         | 300                | 0,3            | 600                   | 0,6                    | 10                     |
| **Clasificador**                    | 450                | 0,45           | 900                   | 0,9                    | 100                    |



## Paso uno: crear una instancia de servicio en {{site.data.keyword.cloud_notm}}
{: #ibp-v2-deploy-iks-create-service-instance}

Siga los pasos siguientes para crear una instancia de servicio de {{site.data.keyword.blockchainfull_notm}} Platform 2.0 en {{site.data.keyword.cloud_notm}}.

1. Localice el [servicio blockchain ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/catalog/services/blockchain) en el catálogo de {{site.data.keyword.cloud_notm}} o busque `Blockchain` en la página del catálogo de {{site.data.keyword.cloud_notm}}.
2. Si lo desea, puede cambiar el **Nombre de servicio** de la instancia para reconocerla fácilmente en el futuro.
3. Para la versión beta, **Dallas** es la única región disponible y no se puede modificar.
4. Puede dejar si modificar los campos de grupo de recursos y etiquetas.
5. Pulse **Crear** para suministrar la instancia de servicio.

## Paso dos: desplegar {{site.data.keyword.blockchainfull_notm}} Platform 2.0
{: #ibp-v2-deploy-iks-steps}

Puede seguir la guía para desplegar {{site.data.keyword.blockchainfull_notm}} Platform 2.0 inmediatamente después de crear la instancia de servicio.

1. El paso de **bienvenida y requisitos previos**. Si ya tiene un clúster del servicio Kubernetes de {{site.data.keyword.IBM_notm}} existente en la región **Dallas** y desea utilizarlo para el servicio de blockchain, marque el recuadro de selección. **Si utiliza un clúster existente, puede omitir el paso siguiente; pero asegúrese de que la versión de Kubernetes sea la v1.11 o posterior**. Pulse **Continuar**.
2. El paso **Crear clúster**. Si marca el recuadro de selección del paso 1 para utilizar un clúster de Kubernetes existentes, este paso se omite. De lo contrario, pulse **Crear un nuevo clúster**, lo que inicia el panel de control de Kubernetes de {{site.data.keyword.cloud_notm}} para crear un clúster. Para obtener más información, consulte [Iniciación al servicio {{site.data.keyword.cloud_notm}} Kubernetes
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](/docs/containers/container_index.html). Este proceso tarda un rato.
  - Independientemente del tipo de clúster que elija, debe seleccionar la ubicación del clúster de Kubernetes **Dallas** para el release beta.
  - Seleccione **Clúster estándar (recomendado):** si necesita una opción de plazo más largo que incluya varios nodos para alta disponibilidad. **Asegúrese de elegir la versión de Kubernetes v1.11 o posterior.** Para desplegar un clúster de pago, consulte
[Creación de un clúster estándar
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](/docs/containers?topic=containers-clusters#clusters_ui_standard "Creación de un clúster estándar"). Tenga en cuenta que si desea una alta disponibilidad o recuperación tras desastre, necesitará tomar una decisión en relación con la clase de almacenamiento que se va a utilizar. El suministro dinámico utilizará la clase de almacenamiento predeterminada del clúster. Así, los clientes pueden establecer cualquier clase de almacenamiento como predeterminada. Para obtener más información, consulte
[Cómo decidir la configuración de almacenamiento de archivos ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](/docs/containers?topic=containers-file_storage#file_predefined_storageclass "Cómo decidir la configuración de almacenamiento de archivos").
  - Seleccione **Clúster gratuito:** si tiene previsto utilizar el clúster durante menos de 30 días. **Tenga en cuenta** que no se puede migrar de un clúster gratuito a un clúster de pago. El tipo de clúster gratuito ofrece un almacenamiento y un rendimiento de transacciones limitados. Para obtener instrucciones sobre qué hacer cuando caduque el clúster de Kubernetes, consulte este tema sobre la
[Caducidad del clúster de Kubernetes](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-cluster-expiration).
  - Para obtener más información sobre las diferencias entre los clústeres de Kubernetes gratuitos y los de pago en {{site.data.keyword.cloud_notm}}, consulte [Comparación entre clústeres gratuitos y estándares ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/docs/containers?topic=containers-cluster_types#cluster_types "Comparación entre clústeres gratuitos y estándares").  

   Debe volver a este separador en el navegador después de crear el clúster de modo que completar el proceso de despliegue de {{site.data.keyword.blockchainfull_notm}} Platform 2.0.  
   {:important}  

  Debe esperar hasta que el clúster se haya desplegado correctamente. A continuación, pulse el botón **Tengo un clúster**.
3. La versión de Kubernetes que se ejecute el clúster debe ser la versión 1.11 o superior. Siga estos [pasos](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes) para comprobar la versión del clúster y actualizarla si es necesario. Luego vuelva y continúe con estas instrucciones.
4. El paso **Desplegar en clúster**. Seleccione el clúster de Kubernetes en el que desee desplegar {{site.data.keyword.blockchainfull_notm}} Platform 2.0 en la lista desplegable y pulse **Desplegar en clúster**.  

  Si el clúster de Kubernetes no está visible en la lista desplegable, podría deberse a las condiciones siguientes:
  - El proceso de creación del clúster puede tardar hasta 60 minutos en completarse. Si ha creado un clúster, deje pasar un tiempo hasta que el estado del clúster sea **Normal**.
  - Los clústeres que están fuera de la región **Dallas** no resultan visibles y no se pueden utilizar.
  - Asegúrese de que no está utilizando la versión ESR de Firefox. Si es así, cambie a otro navegador, como Chrome, y vuelva a intentarlo.

5. El paso **Iniciar consola**. Después de que {{site.data.keyword.blockchainfull_notm}} Platform 2.0 se haya desplegado correctamente, pulse **Iniciar {{site.data.keyword.blockchainfull_notm}} Platform** para abrir la consola de {{site.data.keyword.blockchainfull_notm}} Platform. El botón puede tardar unos minutos en habilitarse mientras se suministra la consola.

## (Opcional) Añadir usuarios adicionales a la consola
{: #ibp-v2-deploy-iks-add-users}

De forma predeterminada, la consola utiliza [{{site.data.keyword.cloud_notm}} Identity and Access Management (IAM) ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](/docs/iam?topic=iam-iamoverview#iamoverview "IBM Cloud Identity and Access Management") como proveedor de servicios de identidad de {{site.data.keyword.cloud_notm}}. La consola de {{site.data.keyword.blockchainfull_notm}} Platform se suministra configurando la dirección de correo electrónico del propietario de {{site.data.keyword.IBM_notm}} como administrador de la consola. Como administrador, este usuario tiene autorización para otorgar a otros usuarios acceso a la consola a través de sus direcciones de correo electrónico.  Consulte las instrucciones sobre cómo
[añadir y eliminar usuarios de la consola](/docs/services/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove) para obtener más información.

## Siguientes pasos
{: #ibp-v2-deploy-iks-next-steps}

Ahora que la consola está lista para ser utilizada, puede continuar con la [guía de aprendizaje sobre cómo crear una red](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

## Actualización de la versión de Kubernetes del clúster
{: #ibp-v2-deploy-iks-updating-kubernetes}

Si utiliza un clúster del servicio Kubernetes de {{site.data.keyword.cloud_notm}} existente, asegúrese de que la versión de Kubernetes sea la versión 1.11 o superior.

Puede comprobar la versión de Kubernetes del clúster en la [página de clústeres de Kubernetes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/kubernetes/clusters) en {{site.data.keyword.cloud_notm}}, que muestra todos los clústeres en una tabla.

Si la versión de Kubernetes no es la versión 1.11 o superior, debe seguir los pasos siguientes para actualizar la versión de Kubernetes del clúster.

1. Pulse sobre el icono de menú de desbordamiento que hay al final de la fila y seleccione **Actualizar versión**. Este proceso tarda alrededor de una hora en completarse. Si la versión se actualiza correctamente, puede ver la versión actualizada del clúster en la columna **Versión de Kubernetes**.  
2. Seleccione la versión de Kubernetes 1.11 o posterior en la lista desplegable de versiones de Kubernetes y pulse **Actualizar**.
3. Pulse el clúster y vaya al separador **Nodos trabajadores**. Marque el recuadro de selección correspondiente al nodo trabajador que desea actualizar y pulse **Actualizar Kubernetes** en la barra de menús emergente. Si el clúster contiene varios nodos trabajadores, se deben actualizar todos ellos.

  Las actualizaciones de los nodos trabajadores pueden hacer que las apps y los servicios estén un tiempo inactivos. Se vuelve a crear una imagen de la máquina del nodo trabajador y los datos se suprimen si no están [almacenados fuera del pod ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/docs/containers/cs_storage_planning.html#persistent_storage_overview).
  {:important}

![Actualizar versión de Kubernetes](../images/update_k8s_version.gif)

Para obtener más información sobre cómo actualizar la versión de Kubernetes de un clúster del servicio Kubernetes de {{site.data.keyword.IBM_notm}} y de los nodos de trabajo, consulte [Actualización de clústeres, nodos trabajadores y complementos ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](/docs/containers?topic=containers-update#update).  

Debe esperar a que finalice la actualización para poder [reanudar el despliegue de {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-steps).

## Cómo asignar roles de acceso de Kubernetes
{: #ibp-v2-deploy-iks-k8x-access-roles}

El usuario que enlaza la instancia de servicio de blockchain al clúster de Kubernetes debe tener los roles Administrador y Gestor en Kubernetes.
Para configurar este acceso, debe realizar los pasos siguientes:
   1. En el panel de control de {{site.data.keyword.cloud_notm}}, pulse la lista desplegable **Gestionar** y, a continuación, pulse **Acceso (IAM)**.
   2. En el menú de navegación de la izquierda, pulse **Usuarios** y pulse el ID del usuario que vaya a enlazar la instancia de servicio con el clúster de Kubernetes.
   3. Pulse **Políticas de acceso** y luego **Asignar acceso**.
   4. Pulse el mosaico **Asignar acceso a recursos**.
   5. En la lista desplegable **Servicios**, seleccione **Servicio Kubernetes**.
   6. Marque los roles **Administrador** y **Gestor** para este usuario.
   7. Pulse **Asignar**.

![Actualizar versión de Kubernetes](../images/k8sAccess.gif)

Para obtener más información sobre el control de acceso de Kubernetes, consulte
[cómo elegir la política de acceso y el rol correctos para los usuarios](/docs/containers?topic=containers-users#access_roles).

## Supresión de una instancia de servicio
{: #ibp-v2-deploy-iks-delete-service-instance}

Cuando ya no necesite una instancia de servicio, se puede suprimir del clúster de Kubernetes para liberar recursos. Puede utilizar la interfaz de usuario de IBM Cloud para suprimir una instancia de servicio de {{site.data.keyword.blockchainfull_notm}} Platform gratuita 2.0 beta.

1. Vaya al [panel de control de {{site.data.keyword.cloud_notm}} de ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://cloud.ibm.com/ "Panel de control").
2. Localice el mosaico **Resumen de recursos** y pulse **Servicios**.
3. En el triángulo **Servicios**, localice la instancia de servicio que desea suprimir y pulse **Suprimir**
en el menú Acciones.

Si la supresión de la instancia de servicio falla, podría deberse a que no se puede acceder al clúster de Kubernetes. Si ocurre esto, abra una [incidencia de soporte](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-cases) para que se suprima la instancia de servicio.
