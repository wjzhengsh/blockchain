---

copyright:
  years: 2017
lastupdated: "2017-12-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Creación de canales gestionados 

**ATENCIÓN:** Antes de utilizar una oferta de {{site.data.keyword.blockchainfull}}, debe leer la información técnica y de soporte de la sección [Descargo de responsabilidad](needtoknow.html).  
{:shortdesc}

En algunos casos (por ejemplo, mercados de intercambio de divisas altamente regulados), sería necesario poner a un tercero a cargo de las funciones administrativas en canales que normalmente gestionarían diversos operadores o miembros. 

El proceso de suministro de una red así es similar a la creación de una red. La principal diferencia radica en la asignación de permisos a los miembros para la realización de transacciones en los canales.  

Los pasos para crear una red e invitar a miembros pueden encontrarse aquí: [Gobierno de red](get_start.html#creating-a-network). 

**Nota**: en un ejemplo real, el operador de esa red podría instalar códigos de encadenamiento personalizados durante la fase de "creación de red" utilizando el Editor de políticas, pero para este ejemplo vamos a suponer que la configuración de red es estándar. 

## Creación del canal

Cuando ya tenga una red y los miembros que ha invitado hayan realizado el proceso de incorporación, vaya a **Canales** en la barra de herramientas. 

Pulse **Nuevo canal**. Ello le llevará a una pantalla en la que podrá indicar un nombre y una descripción del canal (la descripción es opcional). 

Después de pulsar **Siguiente**, aparecerá la pantalla donde podrá invitar otros miembros al canal y gestionar sus permisos. En este ejemplo, supongamos que actúa como un tercero de confianza en un intercambio de divisas entre dos bancos. Defínase a sí mismo, el tercero de confianza, como “Operador” único del canal y asigne los otros dos bancos como “Escritores”. Esto le dará autoridad exclusiva para editar el canal (por ejemplo, creando en él una instancia de código de encadenamiento, como se explicará más adelante) mientras sigue otorgando a los dos bancos la posibilidad de invocar transacciones. La pantalla debería parecerse a esta: 

  ![Seleccionar roles de miembro](images/selectmemberroles.png "Seleccionar roles de miembro") 
*Donde “JoeCo” es usted, el tercero, e “IBM” y “Chris” son dos bancos (obviamente se trata de un ejemplo).* 

Cuando haya asignado los permisos adecuados, pulse **Siguiente**. 

Ello le llevará a una pantalla de actualización de políticas del canal. Puesto que solo hay un operador en este canal (usted), seleccione el número de miembros necesarios para crear el canal: “1”. A continuación, pulse **Enviar solicitud**. 

Nota: aunque sea el único operador de este canal, en este punto solo es una solicitud; deberá ir a la pantalla correcta para aprobar y enviar la solicitud para crear el canal. 

Los correos electrónicos se envían a los miembros que invite y se les solicita que se unan al canal. Sin embargo, solo tienen privilegios de “escritura”; usted es el responsable de completar el proceso de creación del canal. Vaya a la pantalla “Notificaciones” del Supervisor de red, donde aparece una alerta para crear el canal. Localice la solicitud que desea gestionar en los separadores "Todas" o "Pendientes". Pulse **Revisar solicitud** para ver los detalles del canal y pulse **Aprobar**. A continuación, pulse **Enviar solicitud**. 

¡Enhorabuena! Acaba de crear un canal gestionado. 

En una red con 15 miembros (14 bancos y usted, el tercero de confianza), podría ver docenas de estos canales. Los bancos A y B en un canal. Los bancos B y F en un canal. Los bancos podrían optar por tener canales separados para cada tipo de moneda. Sin embargo, en cada caso, el tercero de confianza crearía el canal, aprobaría las transacciones y funcionaría como operador único. 

# Creación de instancias de código de encadenamiento

El canal se ha creado correctamente, pero aún necesita código de encadenamiento conectado al mismo. En un canal dedicado a intercambios de divisas, este código de encadenamiento seguramente necesitaría los tres miembros del canal (los dos bancos en cuestión y usted, el tercero de confianza) para la aprobación de **cada** transacción. También puede escribirse este código de encadenamiento de forma que se envíe un registro del *resultado* de la transacción a un canal "de solo lectura" gestionado compuesto por cada miembro de la red (se explicará más adelante). No obstante, antes de que se pueda crear una instancia de un código de encadenamiento, este debe instalarse en los iguales de los miembros. Una vez instalado, el operador de canal (usted) podrá crear una instancia.  

El procedimiento para instalar y crear una instancia de este tipo de código de encadenamiento no es distinto de cualquier otro código de encadenamiento (salvo por el número de operadores necesarios para aprobar la instancia), por lo que puede seguir las instrucciones descritas aquí: [Instalación y creación de instancia de código de encadenamiento](install_instantiate_chaincode.html.html).

# Un canal gestionado de solo lectura

Las regulaciones financieras del mercado de divisas, siguiendo con este caso de uso, podrían exigir también que haya un canal en la red que tenga a cada banco en modalidad de “solo lectura” pasivo, de manera que se permita el seguimiento del **resultado** de estas transacciones de divisas. Ello serviría para garantizar que, por ejemplo, el Banco A, que acaba de perder millones de dólares en una "apuesta" con el Banco B (sobre cuánto crecería o caería el valor de una divisa en un período de tiempo), no deje de aprobar la transacción con la que se transferirán esos millones de dólares al Banco B. 

Para crear este tipo de canal, siga el mismo procedimiento de creación de canal que ha efectuado anteriormente, pero, en lugar de invitar a dos bancos y asignarles el rol de "Escritor" (con usted como el único operador), invite a **cada** banco y asígneles el rol de "Lector". A continuación, siga el mismo proceso para instalar y crear una instancia del código de encadenamiento. 

## Soporte de {{site.data.keyword.IBM_notm}}

{{site.data.keyword.IBM_notm}} ofrece soporte en soluciones {{site.data.keyword.blockchain}} implementadas por {{site.data.keyword.IBM_notm}}. Acceda a los detalles del Soporte de {{site.data.keyword.blockchainfull_notm}} a través de [{{site.data.keyword.blockchainfull_notm}} DockerHub ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](https://hub.docker.com/u/ibmblockchain/) y examine los compromisos de soporte disponibles.

Para obtener más detalles sobre la funcionalidad y todas las características de Hyperledger Fabric v1.0,
consulte la [documentación de Hyperledger Fabric ![Icono de enlace externo](images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/en/latest/).
