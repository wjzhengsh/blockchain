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


# Problemas conhecidos

Os problemas a seguir já foram relatados:
- A configuração de uma CA externa ainda não é suportada.  Como alternativa, é possível gerar e fazer upload de certificados administrativos por meio do Monitor de Rede. Para obter mais informações, veja [Gerando os certificados do lado do cliente](v10_application.html#generating-the-client-side-certificates) e a descrição na [guia "Certificados" da tela "Membro"](v10_dashboard.html#members) no Monitor de rede.
- No Monitor de rede de uma rede do Starter Plan, quando você clicar em **Visualizar logs** nos nós que estiverem listados na tela "Visão geral", a interface de kibana de Criação de Log do {{site.data.keyword.cloud}} será aberta. Por padrão, o kibana é pré-configurado para mostrar logs dos últimos 15 minutos de atividade. Se não houver atividade nos últimos 15 minutos, você verá uma mensagem que diz 'Nenhum resultado localizado'. Para visualizar outros logs, é possível clicar no ícone de cronômetro no canto superior direito sob o seu nome do usuário e configurar um intervalo de tempo mais amplo, como 'Esta semana' ou 'Este mês'.
- Você pode encontrar problemas de estabilidade, como proxies caindo e será necessário reiniciar. Antes de reiniciar, tente sobrescrever os valores de tempo limite padrão em SDKs do Fabric de seus aplicativos com maiores números. Para obter mais informações sobre como configurar valores de tempo limite, veja [Configurando valores de tempo limite em SDKs do Fabric](v10_application.html#set-timeout-in-sdk).

Para obter suporte e ajuda com a sua rede do {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.Bluemix}}, veja [Obtendo suporte](ibmblockchain_support.html).

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
