---

copyright:
  years: 2017
lastupdated: "2017-07-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Monitorando uma rede de {{site.data.keyword.blockchain}}

Este tutorial mostra como visualizar e monitorar as informações de status de sua rede de {{site.data.keyword.blockchain}} no {{site.data.keyword.Bluemix_short}}.
{:shortdesc}

Entre no Monitor de Rede e localize o canal que você deseja visualizar e monitorar na tela "Canal". Na tela do canal específico, é possível visualizar as informações de status dos dados, os membros e o chaincode instanciado deste canal em três guias:

* **Visão geral do canal**  
  A guia "Visão Geral do Canal" mostra as informações de bloco nesse canal:
    * Uma série de pontos de dados, incluindo o número total de blocos que são criados, o intervalo de tempo desde a última transação, o número de instanciações de chaincode e o número de chamadas de chaincode.
    * Uma tabela listando todos os blocos neste canal. Expanda um bloco e você poderá visualizar as informações detalhadas sobre o bloco.  

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
  
  ![Chaincode de canal](../images/channel_chaincode.png "Chaincode de canal") 
  
