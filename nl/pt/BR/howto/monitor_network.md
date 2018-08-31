---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Monitorando uma rede de blockchain

Este tutorial mostra como visualizar e monitorar as informações de status de sua rede de {{site.data.keyword.blockchain}} no {{site.data.keyword.cloud_short}}.
{:shortdesc}


## Monitorando peers, pedidos e autoridades de certificação
{: #monitor-nodes}

É possível emitir uma solicitação de HTTP **HEAD** com relação a um de seus nós de rede para verificar o status do nó. Um nó de rede pode ser um peer, um solicitador ou uma autoridade de certificação em sua rede de blockchain. Uma solicitação de **HEAD** é semelhante a uma solicitação GET e envia apenas os cabeçalhos sem corpos. Será possível obter uma resposta 200 se o nó funcionar normalmente.

1. Na tela "Visão geral" do Monitor de rede, clique em **Perfil de conexão**. Em seguida, é possível clicar em **JSON bruta** para visualizar o perfil de conexão em seu navegador da web ou clicar em **Download** para salvar o perfil de conexão localmente.
2. No perfil de conexão, localize as informações da URL do nó de rede que você deseja verificar. Por exemplo, a URL do solicitador `fabric-orderer-20190b` é `grpcs://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.  
    ![Exemplo de URL do solicitador](../images/orderer_url.png "Exemplo de URL do solicitador")
3. Substitua **grpcs** por **https** na URL. No exemplo acima, a URL torna-se `https://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.
4. Emita a solicitação de **HEAD** com relação à URL com uma ferramenta como curl ou um app Chrome Postman.
    - Se você obtiver uma resposta de status 200, o seu nó de rede funcionará normalmente.
    - Se a solicitação de **HEAD** falhar com um erro de conexão, o seu nó de rede poderá não estar em execução, a URL do nó estará errada ou um firewall bloqueará o seu acesso ao nó. Deve-se resolver esse erro; caso contrário, os seus aplicativos não poderão conectar o nó.

O exemplo a seguir mostra uma solicitação de **HEAD** com uma resposta 200 em curl. Observe que é possível ignorar o erro grpc porque a solicitação de HTTP **HEAD** verifica se o nó está acessível. Se sim, a solicitação grpc para o nó também funcionará em seu aplicativo.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
HTTP/2 200
contnent-type: application/grpc
grpc-status: 8
grpc-message: malformed method name: "/"
```
{:codeblock}

O exemplo a seguir mostra uma solicitação de **HEAD** com um erro de conexão em curl.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
curl: (7) Failed to connect to fft-zbc02b.4.secure.blockchain.ibm.com:20190: Connection refused
```
{:codeblock}

A figura a seguir mostra uma solicitação de **HEAD** com uma resposta 200 no app Chrome Postman.  

![Exemplo do Postman de solicitação de HEAD](../images/orderer_head_postman.png "Exemplo do Postman de solicitação de HEAD")


## Monitorando canais
{: #monitor-channnels}

Entre no Monitor de Rede e localize o canal que você deseja visualizar e monitorar na tela "Canal".  Na tela do canal específico, é possível visualizar as informações de status dos dados, os membros e o chaincode instanciado deste canal em três guias:

* **Visão geral do canal**  
  A guia "Visão Geral do Canal" mostra as informações de bloco nesse canal:
    * Uma série de pontos de dados, que incluem o número total de blocos que são criados, o intervalo de tempo desde a última transação, o número de instanciações de chaincode e o número de chamadas de chaincode.
    * Uma tabela que lista todos os blocos neste canal.  Expanda um bloco e você poderá visualizar as informações detalhadas sobre o bloco.  

  ![Visão geral do canal](../images/channel_overview_detail.png "Visão geral do canal")  

* **Membros**  
  A guia "Membros" mostra as informações dos membros nesse canal, incluindo os endereços de e-mail para os operadores organizacionais.
  ![Membros do canal](../images/channel_members.png "Membros do canal")  

* **Chaincode**  
  A guia "Chaincode" lista todos os chaincodes que são instanciados nesse canal com o ID do chaincode, a versão e o número de peers que estão executando o chaincode.   

  Expanda uma linha de chaincode para obter informações detalhadas sobre o chaincode:  
    * É possível clicar em **JSON** para visualizar o arquivo JSON do chaincode.
    * É possível clicar em **Logs** para visualizar logs do chaincode.
    * É possível clicar em **Excluir** para remover o contêiner de chaincode em execução.
    **Observação**: excluir o contêiner de chaincode em execução não exclui realmente o chaincode. Um chaincode instanciado na rede de blockchain não pode ser excluído.

  ![Chaincode de canal](../images/channel_chaincode.png "Chaincode de canal")


## Monitorando o chaincode
{: #monitor-chaincode}

Insira o Monitor de rede e abra a tela "Instalar código". Se você tiver executando o chaincode, escolha um peer na lista suspensa e poderá ver todos os chaincodes para esse peer na tabela com as IDs e as versões de chaincode.  É possível executar a instalação e instanciação para o seu chaincode nessa tela.  Para obter mais informações, veja [Instalando, instanciando e atualizando um chaincode](install_instantiate_chaincode.html).

  ![Chaincode](../images/chaincode_install_overview.png "Chaincode")


## Monitorando aplicativos de amostra
{: #monitor-apps}

Em uma rede do Starter Plan, é possível visualizar e acessar aplicativos de amostra na tela "Tentar amostras" do Monitor de rede.  Depois de implementar um aplicativo de amostra, será possível clicar no botão **Ativar** para inserir a sua interface do aplicativo ou o link **Visualizar no GitHub** para visitar o repositório de código.  Para obter mais informações, veja [Implementando aplicativos de amostra](prebuilt_samples.html).

  ![Aplicativos de amostra](../images/sampleappflow0.png "Aplicativos de amostra")
