---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Monitorando uma rede de {{site.data.keyword.blockchain}}

Este tutorial mostra como visualizar e monitorar as informações de status de sua rede de {{site.data.keyword.blockchain}} no {{site.data.keyword.Bluemix_short}}.
{:shortdesc}


## Monitorando canais

Entre no Monitor de Rede e localize o canal que você deseja visualizar e monitorar na tela "Canal".  Na tela do canal específico, é possível visualizar as informações de status dos dados, os membros e o chaincode instanciado deste canal em três guias:

* **Visão geral do canal**  
  A guia "Visão Geral do Canal" mostra as informações de bloco nesse canal:
    * Uma série de pontos de dados, que incluem o número total de blocos que são criados, o intervalo de tempo desde a última transação, o número de instanciações de chaincode e o número de chamadas de chaincode.
    * Uma tabela que lista todos os blocos neste canal. Expanda um bloco e você poderá visualizar as informações detalhadas sobre o bloco.  

  ![Visão geral do canal](../images/channel_overview_detail.png "Visão geral do canal")  

* **Membros**  
  A guia "Membros" mostra as informações dos membros nesse canal, incluindo os endereços de e-mail para os operadores organizacionais.
  ![Membros do canal](../images/channel_members.png "Membros do canal")  

* **Chaincode**  
  A guia "Chaincode" lista todos os chaincodes que são instanciados neste canal com o ID do chaincode, a versão e o número de peers que estão executando o chaincode.   

  Expanda uma linha de chaincode para obter informações detalhadas sobre o chaincode:  
    * É possível clicar em **JSON** para visualizar o arquivo JSON do chaincode.
    * É possível clicar em **Logs** para visualizar logs do chaincode.
    * É possível clicar em **Excluir** para remover o contêiner de chaincode em execução.
    **Observação**: excluir o contêiner de chaincode em execução não exclui realmente o chaincode. Um chaincode instanciado na rede de blockchain não pode ser excluído.

  ![Chaincode de canal](../images/channel_chaincode.png "Chaincode de canal")


## Monitorando chaincodes

Entre em Monitor de rede e abra a tela "Código de Instalação" (tela "Chaincode" na rede de Enterprise Plan). Se você tiver chaincodes em execução, escolha um peer na lista suspensa e poderá ver todos os chaincodes desse peer na tabela com IDs e versões de chaincode. É possível executar a instalação e instanciação para o seu chaincode nessa tela. Para obter mais informações, consulte [Instalando e instanciando um chaincode](install_instantiate_chaincode.html).

  ![Chaincode](../images/chaincode_install_overview.png "Chaincode")


## Monitorando aplicativos de amostra

Em uma rede do Starter Plan, é possível visualizar e acessar aplicativos de amostra na tela "Tentar amostras" do Monitor de rede. Depois de implementar um aplicativo de amostra, será possível clicar no botão **Ativar** para inserir a sua interface do aplicativo ou o link **Visualizar no GitHub** para visitar o repositório de código. Para obter mais informações, veja [Implementando aplicativos de amostra](prebuilt_samples.html).

  ![Aplicativos de amostra](../images/sampleappflow0.png "Aplicativos de amostra")
