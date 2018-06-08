---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-29"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Problemas conocidos

Los siguientes problemas ya se han notificado:
- Todavía no se admite la configuración de una entidad emisora de certificados externa.  Como alternativa, puede generar y subir los certificados de administración a través del Supervisor de red. Para obtener más información, consulte [Generación de certificados del lado del cliente](v10_application.html#generating-the-client-side-certificates) y la descripción del [separador "Certificados" de la pantalla "Miembros"](v10_dashboard.html#members) del supervisor de red.
- En el Supervisor de red de una red de Plan inicial, al pulsar **Ver registros** en los nodos que se listan en la pantalla "Visión general", se abrirá la interfaz Registro de kibana de {{site.data.keyword.cloud}}. De forma predeterminada, kibana estará preconfigurado para mostrar registros de los últimos 15 minutos de actividad. Si no hay actividad en los últimos 15 minutos, verá un mensaje que indica 'No results found'. Para ver otros registros, puede pulsar el icono de temporizador de la esquina superior derecha bajo el nombre de usuario y establecer un rango de tiempo más amplio, como por ejemplo 'Esta semana' o 'Este mes'.
- Puede encontrar problemas de estabilidad, como la caída de los proxies, y será necesario que vuelva a empezar. Antes de reiniciar, intente sobrescribir los valores de tiempo de espera predeterminados en los SDK de Fabric de las aplicaciones con números más grandes. Para obtener más información sobre cómo establecer valores de tiempo de espera, consulte [Establecimiento de valores de tiempo de espera en los SDK de Fabric](v10_application.html#set-timeout-in-sdk).

Para obtener soporte y ayuda con la red de {{site.data.keyword.blockchainfull_notm}} Platform en {{site.data.keyword.Bluemix}}, consulte [Obtención de soporte](ibmblockchain_support.html).

<!--
## Updating chaincode with Enterprise Plan migration to Hyperledger Fabric 1.1
-	Users who migrate from networks based on Hyperledger Fabric 1.0 to networks based on Fabric 1.1 will need to update the dependencies in their chaincode. If they do not, there is a risk of a service disruption.
- This does not apply to users that uploaded their chaincode without dependencies, using a .go file.
- **Update your chaincode using the following steps:**
  **1.** You can use any  golang vendoring tool to update your chaincode. It will be easiest to use the same tool that was used to include dependencies in the original file. Many early Fabric samples used the govendor tool. If your chaincode used govendor, you can update your dependencies using the following command in the directory above the vendor folder.
      govendor update all +v
  * You can use `go build` to check that the new code compiles and that the update worked.
  * You can test your chaincode by installing and instantiating it on a Starter Plan Network. All chaincode that works on Starter Plan will also work on Enterprise Plan after the upgrade.
  **2.** Once your chaincode dependencies are up to date, you can use the network monitor to [update your chaincode](install_instatiate_chaincode.html#Updating a chaincode).
-->
