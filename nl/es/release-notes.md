---

copyright:
  years: 2019
lastupdated: "2019-04-18"

---

{:new_window: target="_blank"}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:shortdesc: .shortdesc}
{:pre: .pre}

# Notas del release
{: #release-notes-saas-20}

Utilice estas notas del release agrupadas por fecha para obtener información sobre los cambios más recientes en
{{site.data.keyword.blockchainfull}} Platform gratuita 2.0 beta, basada en Hyperledger Fabric v1.4.0.
{:shortdesc}


## 17 de abril de 2019

**Posibilidad de dimensionar y escalar recursos de nodo**  

Al desplegar un nodo, ahora tiene la posibilidad de especificar la cantidad de CPU, memoria y almacenamiento en los contenedores, cuando proceda. Más adelante, puede aumentar o reducir la escala de los recursos según los patrones de uso. Para obtener más información, consulte
[Asignación de recursos](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-orderer-allocate-resources).

**Uso de IBM Cloud Identity and Access Management (IAM)**  

IAM se utiliza para controlar el acceso de usuario a la interfaz de usuario de la consola, así como para restringir las acciones que se pueden realizar en la interfaz de usuario.  Consulte este tema para obtener información sobre cómo
[añadir y eliminar usuarios de la consola](/docs/services/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove).

## 3 de abril de 2019
{: #04-03-2019}

**Soporte para CA externa**

Al añadir un nodo igual o clasificador, tiene la opción de utilizar certificados de una CA externa, una que no esté alojada en {{site.data.keyword.IBM_notm}}. Consulte este tema sobre la [Utilización de certificados de una CA externa con su igual o clasificador](/docs/services/blockchain?topic=blockchain-ibp-console-build-network#ibp-console-build-network-third-party-ca) para obtener más información.

**Ajuste del rendimiento del clasificador**

Hay nuevos parámetros de ajuste de clasificador disponibles en la consola para proporcionarle un mayor control sobre la producción y el rendimiento del clasificador. Consulte este tema sobre el [Ajuste del clasificador](/docs/services/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-orderer-tuning) para obtener instrucciones sobre cómo configurar los parámetros.
