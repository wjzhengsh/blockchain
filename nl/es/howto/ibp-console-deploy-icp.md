---

copyright:
  years: 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Despliegue de la consola de {{site.data.keyword.blockchainfull_notm}} Platform en {{site.data.keyword.cloud_notm}} Private
{: #ibp-console-deploy-icp}

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

En las instrucciones siguientes se describe cómo desplegar una consola de {{site.data.keyword.blockchainfull}} Platform en su propia infraestructura mediante {{site.data.keyword.cloud_notm}} Private. No es necesario que despliegue la consola en el mismo entorno que sus otros componentes de blockchain.
{:shortdesc}

Utilizará un diagrama de Helm para desplegar la consola de {{site.data.keyword.blockchainfull_notm}} Platform en {{site.data.keyword.cloud_notm}} Private. Cada uno de los despliegues instala una sola instancia de la consola en el espacio de nombres de {{site.data.keyword.cloud_notm}} Private.

## Consideraciones
{: #ibp-console-deploy-icp-considerations}

Antes de desplegar la consola, asegúrese de que comprende las consideraciones siguientes:

- Los componentes de blockchain ya se deben haber desplegado en {{site.data.keyword.cloud_notm}} Private o en Amazon Web Services (AWS). Si aún no lo ha hecho, consulte [Acerca de {{site.data.keyword.blockchainfull_notm}} Platform para {{site.data.keyword.cloud_notm}} Private] o Acerca de [IBM Blockchain Platform para AWS] para obtener más información.
- El usuario es responsable de gestionar la supervisión del estado, la seguridad, el registro y el uso de recursos de la consola.
- Solo puede conectar la consola a los componentes de blockchain que están en el nivel de Hyperledger Fabric v1.2.1.

## Recursos necesarios
{: #ibp-console-deploy-icp-resources-required}

Asegúrese de que el sistema {{site.data.keyword.cloud_notm}} Private cumple los requisitos de recursos de hardware mínimos:

| Componente | vCPU | RAM | Disco para almacenamiento de datos |
|-----------|------|-----|-----------------------|
| Consola de IBP | 1 | 192 MB | 1 GB |
| Consola de CouchDB para IBP | 1 | 1 GB | 1 GB |

 **Notas:**
 - Un vCPU es un núcleo virtual que se asigna a una máquina virtual o a un núcleo de procesador físico si el servidor no está particionado para máquinas virtuales. Debe tener en cuenta los requisitos de vCPU cuando decida el núcleo de procesador virtual (VPC) para su despliegue en {{site.data.keyword.cloud_notm}} Private. VPC es una unidad de medida que determina el coste de licencias de los productos de IBM. Para obtener más información sobre los casos de ejemplo para decidir el VPC, consulte
[Núcleo de procesador virtual (VPC) ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Estos niveles mínimos de recursos son suficientes para pruebas y experimentación. Para un entorno con un volumen grande de transacciones, es importante asignar una cantidad de almacenamiento lo suficientemente grande; por ejemplo, 250 GB para la consola de {{site.data.keyword.blockchainfull_notm}} Platform. La cantidad de almacenamiento a utilizar dependerá del número de transacciones y del número de firmas necesarias de la red. Si está a punto de agotar el almacenamiento en el igual o en el clasificador, debe desplegar una nueva consola con un sistema de archivos más grande e importar de nuevo los componentes de la red.

## Almacenamiento
{: #ibp-console-deploy-icp-resources-required-storage}

Debe determinar el almacenamiento que utilizarán la consola. Si utiliza los valores predeterminados, el diagrama de Helm creará una nueva reclamación de volumen persistente (PVC) de 8 Gi con el nombre `console-pvc` para los datos de la consola y otra PVC de 8 Gi con el nombre
`couchdb-pvc` para CouchDB.

Si no desea utilizar los valores de almacenamiento predeterminados, asegúrese de que se configure una *nueva* `storageClass` durante la instalación de {{site.data.keyword.cloud_notm}} Private; si no es así, el administrador del sistema Kubernetes tiene que crear una storageClass antes de que realice el despliegue.

Puede optar por desplegar la consola en las plataformas AMD64 o S390X. No obstante, debe tener en cuenta que el [suministro dinámico](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) solo está disponible para los nodos AMD64 en {{site.data.keyword.cloud_notm}} Private. Si el clúster incluye una combinación de nodos trabajadores S390X y AMD64, el suministro dinámico no se puede utilizar.

Si no utiliza el suministro dinámico, deberán crearse [volúmenes persistentes ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) y configurarse con etiquetas que se puedan utilizar para refinar el proceso de enlace de PVC de Kubernetes.

## Requisitos previos
{: #ibp-console-icp-prereq}

Antes de desplegar la consola de {{site.data.keyword.blockchainfull_notm}} Platform en {{site.data.keyword.cloud_notm}} Private, asegúrese de completar los pasos de las guías de aprendizaje sobre [Configuración de {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup).

También tiene que instalar o configurar las siguientes dependencias antes de desplegar la consola de {{site.data.keyword.blockchainfull_notm}} Platform.

- [Regístrese con el servicio App ID de IBM](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id)
- [Instale Docker](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-docker)

### Registro con un servicio App ID
{: #ibp-console-icp-prereq-app-id}

[App ID ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/docs/services/appid/index.html#gettingstarted "App ID") es un servicio de gestión de usuarios que proporciona {{site.data.keyword.cloud_notm}}. La consola de {{site.data.keyword.blockchainfull_notm}} Platform utiliza el servicio App ID para gestionar quién podrá utilizar el panel de control y trabajar con la red blockchain. Esto permite a los usuarios de la IU utilizar las credenciales emitidas por su propia organización o por terceros, como Google o Facebook, sin necesidad de que tengan ID de IBM. Solo el usuario que despliegue la consola necesitará un ID de {{site.data.keyword.IBM_notm}} para registrarse con el servicio.

Antes de desplegar la consola, debe obtener un ID de IBM para realizar un registro de una sola vez con el directorio App ID Cloud. A continuación, debe captar las credenciales del servicio App ID para pasarlas a la consola. Siga los pasos siguientes para registrarse con el servicio y obtener las credenciales de servicio.

1. Si aún no tiene uno, debe crear un [ID de {{site.data.keyword.IBM_notm}} ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-19776&target=https%3A%2F%2Fidaas.iam.ibm.com%2Fidaas%2Foidc%2Fendpoint%2Fdefault%2Fauthorize%3Fresponse_type%3Dcode%26client_id%3Dmyibmlondonprod%26state%3DbfAvZHoYtGHytcifRjeE%26redirect_uri%3Dhttps%3A%2F%2Fmyibm.ibm.com%2Fmymga%2Foidcclient%2Fredirect%2Famapp-runtime-BlueIDProd%26scope%3Dopenid).

2. Utilice su ID de {{site.data.keyword.IBM_notm}} para iniciar una sesión o para registrarse para [{{site.data.keyword.cloud_notm}} ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/app-id "IBM Cloud App ID"). A continuación, vaya al servicio [App ID ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://console.bluemix.net/catalog/services/app-id "IBM Cloud App ID") en el catálogo. Seleccione el plan `Lite` o el `Nivel graduado` en función de sus necesidades de servicio. Luego pulse el botón **Crear** para iniciar la pantalla de "Bienvenida".

3. Cuando esté en la pantalla de "Bienvenida", pulse el enlace **Gestionar** en el panel de navegación izquierdo para ver o actualizar los proveedores de identidades preferidos. Pueden ser proveedores de terceros, como Google o el propio servicio de identidad de su organización.

4. Pulse el separador **Valores** de la tabla para configurar un URL de redirección. Será el URL en el que se desplegará y desde el que se accederá a la consola de {{site.data.keyword.blockchainfull_notm}} Platform. Para utilizar localhost, especifique `http://localhost/auth/cb` y pulse el signo `+`.

5. Añada la dirección de correo electrónico al servicio para que pueda iniciar la sesión cuando configure la consola por primera vez. Pulse el enlace **Usuarios** en el panel de navegación de la izquierda y pulse el botón **Añadir usuario**. Especifique la información de correo electrónico y pulse **Guardar**.

6. Pulse el enlace **Credenciales de servicio** en el panel de navegación de la izquierda. Si esta es la primera vez que utiliza el servicio, debe generar credenciales pulsando **Nueva credencial**. Pulse **Ver credenciales** para copiar las credenciales necesarias para acceder al servicio.  Cuando aparezcan los detalles para la creación de nuevas credenciales, seleccione la opción `lector` y un ID de servicio si desea asignar a la IU un nombre específico. Las credenciales de App ID son similares a los valores JSON que se indican a continuación: ![Credenciales de servicio](../images/service_credentials.gif "Credenciales de servicio")

7. Pulse el icono Copiar y guarde las credenciales. Las utilizará cuando configure la consola.

### Instalación de Docker
{: #ibp-console-icp-prereq-docker}

Instale [Docker ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.docker.com/get-started) versión 17.06.2-ce o superior en el entorno en el que desplegará la consola de {{site.data.keyword.blockchainfull_notm}} Platform. Puede comprobar la versión de Docker que ha instalado con el mandato siguiente desde una ventana de terminal:

```
docker --version
```
{:codeblock}

**Nota:** la instalación de Docker para Mac o Windows o de Docker Toolbox también instalará el Docker Compose. Si ya tiene Docker instalado, debe comprobar que tiene instalado Docker Compose versión 1.14.0 o posterior. Si no es así, le recomendamos que instale una versión más reciente de Docker. Puede comprobar la versión de Docker Compose que ha instalado con el mandato siguiente desde una ventana de terminal:

```
docker-compose --version
```
{:codeblock}


## Paso uno: descargar el paquete de la consola de {{site.data.keyword.blockchainfull_notm}} Platform desde {{site.data.keyword.IBM_notm}} Passport Advantage
{: #ibp-console-icp-download-icp}

Póngase en contacto con el vendedor de {{site.data.keyword.IBM_notm}} para descargar el paquete de la consola de {{site.data.keyword.blockchainfull_notm}} Platform desde [Passport Advantage ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage en línea para clientes"). ¿No tiene vendedor de IBM? Visite este <link> para registrarse y obtener uno...

## Paso dos: importar el diagrama de Helm de la consola de {{site.data.keyword.blockchainfull_notm}} Platform en {{site.data.keyword.cloud_notm}} Private
{: #ibp-console-icp-import-helmchart}

Para obtener más información, consulte [Instalación de {{site.data.keyword.blockchainfull_notm}} Platform en {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

## Paso tres: desplegar la consola de {{site.data.keyword.blockchainfull_notm}} Platform en {{site.data.keyword.cloud_notm}} Private
{: #ibp-console-deploy-helmchart-icp}

Después de importar el diagrama de Helm de la consola de {{site.data.keyword.blockchainfull_notm}} Platform, puede seguir los pasos siguientes para configurar e instalar la consola.

1. Inicie una sesión en la consola de {{site.data.keyword.cloud_notm}} Private y pulse el enlace **Catálogo** en la esquina superior derecha.
2. Pulse `Blockchain` en el panel de navegación de la izquierda para localizar el mosaico llamado `ibm-blockchain-platform-ui`. Pulse el mosaico para abrirlo; verá un archivo Readme que incluye información sobre la configuración e instalación del diagrama de Helm.
3. Pulse el separador **Configuración** de la parte superior del panel o pulse el botón **Configurar** de la esquina inferior derecha.
4. Complete los [valores de configuración](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-parameters).
5. Pulse **Instalar**.

### Parámetros de la consola de {{site.data.keyword.blockchainfull_notm}} Platform
{: #ibp-console-icp-parameters}

En la tabla siguiente se muestran los parámetros configurables de la consola de {{site.data.keyword.blockchainfull_notm}} Platform y sus valores predeterminados.

|  Parámetro     | Descripción    | Valor predeterminado  | Obligatorio |
| --------------|-----------------|-------|------- |
| `Nombre de release de Helm`| Nombre del release de Helm. Debe comenzar por una letra minúscula y terminar por un carácter alfanumérico, y solo debe contener guiones y caracteres alfanuméricos. Debe utilizar un nombre de release de Helm exclusivo cada vez cuando intente instalar una consola. | ninguno | sí |
| `Espacio de nombres de destino`| Elija el espacio de nombres de Kubernetes para instalar el diagrama de Helm. | ninguno | sí |
| `Repositorio de imágenes`| Ubicación del diagrama de Helm de la consola de {{site.data.keyword.blockchainfull_notm}} Platform. | registry.ng.bluemix.net/op-tools/op-tools | sí |
| `Etiqueta de imagen`| Valor de la etiqueta asociada a la imagen de la consola de {{site.data.keyword.blockchainfull_notm}} Platform. Este campo se rellena automáticamente con la versión de la imagen. No lo modifique. | v0.0.34 | sí |
| `Tipo de servicio` | Se usa para especificar si los [puertos externos se deben exponer ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) en el igual. Seleccione NodePort para exponer los puertos externamente (recomendado), y ClusterIP para no exponer los puertos. LoadBalancer y ExternalName no se admiten en este release. | NodePort | sí |
| `Entrada a consola` | Especifique el nombre de host que desea utilizar para acceder a la consola de {{site.data.keyword.blockchainfull_notm}} Platform. | ninguno | no |
| `Entrada a configtxlator` | Especifique el nombre de host que desea utilizar para acceder al servicio configtxlator. | ninguno | no |
| `Persistencia habilitada` | Si se marca, los datos estarán disponibles cuando se reinicie el contenedor. De lo contrario, se perderán todos los datos en el caso de una migración tras error o de un reinicio del pod. | marcada | no |
| `Utilizar suministro dinámico` | Marcar para habilitar el suministro dinámico para volúmenes de almacenamiento. | marcada | no |
| `Nombre de clase de almacenamiento`| Especifique un nombre de clase de almacenamiento exclusivo. De lo contrario, se utilizará la clase de almacenamiento predeterminada en el clúster. | ninguno | Depende de cómo se haya configurado el clúster de {{site.data.keyword.cloud_notm}} Private. Consulte al administrador del clúster. |
| `Reclamación de volumen existente`| Especifique el nombre de una reclamación de volumen existente y deje todos los demás campos en blanco. | ninguno | no |
| `Etiqueta de selector`| [Etiqueta de selector ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas y selectores") de la PVC. | ninguno | no |
| `Valor de selector`| [Valor de selector ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas y selectores") de la PVC. | ninguno | no |
| `Modalidad de acceso al almacenamiento`| Especifique la [modalidad de acceso](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "modalidad de acceso") al almacenamiento para la PVC. | ReadWriteMany | sí |
| `Nombre de volumen`| Especifique el nombre del PVC. | ninguno | sí |
| `Solicitud de CPU` | Número mínimo de CPU que se asignarán a la consola. | 250m | sí |
| `Límite de CPU` | Número máximo de CPU que se asignarán a la consola.| 500m | sí |
| `Solicitud de memoria` | Cantidad mínima de memoria que se asignará a la consola. | 256 Mi | sí |
| `Límite de memoria` | Cantidad máxima de memoria que se asignará a la consola. | 1Gi | sí |

## Paso cuatro: configurar la IP de proxy
{: #ibp-console-icp-config-proxy-ip}

Una vez que finalice la instalación, pulse el botón **Ver release de Helm** para abrir la página de release de Helm de la consola. También puede pulsar **Menú** > **Cargas de trabajo** > **Releases de Helm** en {{site.data.keyword.cloud_notm}} Private y pulsar el release de Helm.

En la página de release de Helm de la consola, busque el IP de proxy del clúster de {{site.data.keyword.cloud_notm}} Private, por ejemplo `9.12.19.115`.

En la máquina cliente en la que desea acceder a la consola, añada entradas en `/etc/hosts` para correlacionar los nombres de host que ha añadido en el paso anterior para los servicios de optools y configtxlator a la IP de proxy. Por ejemplo:
```
9.12.19.115     ibp-optools.ibm.com
9.12.19.115     ibp-configtxlator.ibm.com
```

## Paso cinco: configurar la consola de {{site.data.keyword.blockchainfull_notm}} Platform
{: #ibp-console-icp-setup}

*Esta sección ganaría con una animación en forma de gif.*

1. En {{site.data.keyword.cloud_notm}}, vaya a **Proveedores de identidades** > **Gestionar**
desde el panel de navegación de la izquierda y seleccione el separador **Valores de autenticación**.
2. En el campo **Añadir URL de redirección web**, especifique el URL que desea utilizar para acceder a la consola con App ID.
3. En el navegador web, acceda a la consola con el nombre de host que ha configurado en {{site.data.keyword.cloud_notm}} Private. Cuando inicie la sesión por primera vez verá los paneles de configuración de la consola.
  1. En el separador **Autenticación**, especifique su App ID.
  2. En el separador **Configuración**, pegue las credenciales de servicio App ID que ha copiado en el [Paso uno: registrarse con el servicio App ID](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id).
  3. En el separador **Añadir usuarios**, especifique una lista de direcciones de correo electrónico de los usuarios con los roles `Admin` y `General` que tienen autorización de acceso a la consola. Los dominios de correo electrónico están restringidos al conjunto de proveedores de identidades, como por ejemplo corporativo, Google o Facebook, que se seleccionan cuando el servicio App ID se registra en {{site.data.keyword.cloud_notm}}.
    - Se necesita el rol `Admin` para poder añadir nuevos usuarios o para eliminar usuarios existentes de la lista de autorizaciones de la consola.   **Sugerencia:** si es la persona que va a administrar la consola, recuerde incluir su propia dirección de correo electrónico en la lista.
    - El rol `General` permite a los usuarios acceso de **solo visualización** a los componentes de la consola.

    Por último, debe especificar un solo `Correo electrónico de contacto de admin` que se muestra como dirección de correo electrónico de contacto cuando un usuario no autorizado (uno que no está en ninguna de las listas anteriores) intenta acceder a la consola. Esta dirección de correo electrónico no tiene que ser un usuario administrador en la consola. Tenga en cuenta que esta información también se puede crear o modificar más adelante en el separador **Miembros** de la consola.
  4. Pulse **Continuar para iniciar sesión**. Ahora todos los usuarios especificados en las listas anteriores pueden iniciar una sesión en la consola de {{site.data.keyword.blockchainfull_notm}} Platform utilizando sus direcciones de correo electrónico.
