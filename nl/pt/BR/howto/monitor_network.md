---

copyright:
  years: 2018,2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Monitorando uma rede de blockchain
{: #monitor-blockchain-network}

Este tutorial mostra como visualizar e monitorar as informações de status de sua rede de {{site.data.keyword.blockchain}} no {{site.data.keyword.cloud_notm}}.
{:shortdesc}


## Peers de Monitoramento, Orderers e autoridades de certificação
{: #monitor-blockchain-network-monitor-nodes}

É possível emitir uma solicitação de HTTP **HEAD** com relação a um de seus nós de rede para verificar o status do nó. Um nó de rede pode ser um peer, um solicitador ou uma autoridade de certificação em sua rede de blockchain. Uma solicitação de **HEAD** é semelhante a uma solicitação GET e envia apenas os cabeçalhos sem corpos. Será possível obter uma resposta 200 se o nó funcionar normalmente.

1. Na tela "Visão geral" do Monitor de rede, clique em **Perfil de conexão**. Em seguida, é possível clicar em **JSON bruta** para visualizar o perfil de conexão em seu navegador da web ou clicar em **Download** para salvar o perfil de conexão localmente.
2. No perfil de conexão, localize as informações da URL do nó de rede que você deseja verificar. Por exemplo, a URL do solicitador `fabric-orderer-20190b` é `grpcs://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.
![Exemplo de URL do solicitador](../images/orderer_url.png "Exemplo de URL do solicitador")
3. Substitua **grpcs** por **https** na URL. No exemplo acima, a URL torna-se `https://fft-zbc02b.4.secure.blockchain.ibm.com:20190`.
4. Emita a solicitação de **HEAD** com relação à URL com uma ferramenta como curl ou um app Chrome Postman.
    - Se você obtiver uma resposta de status 200, o seu nó de rede funcionará normalmente.
    - Se a solicitação de **HEAD** falhar com um erro de conexão, o seu nó de rede poderá não estar em execução, a URL do nó estará errada ou um firewall bloqueará o seu acesso ao nó.  Deve-se resolver esse erro; caso contrário, os seus aplicativos não poderão conectar o nó.

O exemplo a seguir mostra uma solicitação de **HEAD** com uma resposta 200 em curl. Observe que é possível ignorar o erro grpc porque a solicitação de HTTP **HEAD** verifica se o nó está acessível. Se sim, a solicitação grpc para o nó também funcionará em seu aplicativo.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
HTTP/2 200
contnent-type: application/grpc
grpc-status: 8
grpc-message: malformed method name: "/"
```

O exemplo a seguir mostra uma solicitação de **HEAD** com um erro de conexão em curl.

```
C:\>curl -i --head https://fft-zbc02b.4.secure.blockchain.ibm.com:20190
curl: (7) Failed to connect to fft-zbc02b.4.secure.blockchain.ibm.com:20190: Connection refused
```

A figura a seguir mostra uma solicitação de **HEAD** com uma resposta 200 no app Chrome Postman.

  ![Exemplo do Postman de solicitação de HEAD](../images/orderer_head_postman.png "Exemplo do Postman de solicitação de HEAD")

## Usando os logs de rede
{: #monitor-blockchain-network-using-logs}

A tela "Visão geral" de seu Monitor de rede exibe o status de sua Autoridade de certificação, serviço de pedido e peers. Clique em **Visualizar logs** na lista suspensa sob o cabeçalho **Ações** para visualizar os logs de um componente de rede específico. Se você usar as redes do Enterprise Plan, será possível visualizar os logs de componentes em um formato de arquivo de texto. Se você usar as redes do Starter Plan, os logs de componente serão reunidos pelo serviço [{{site.data.keyword.cloud_notm}} Log Analysis ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/catalog/services/log-analysis) e será possível visualizar os logs no [Kibana](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network-viewing-kibana-logs).

Cada componente gera logs por meio de atividades diferentes. Isso porque cada componente desempenha funções diferentes na [arquitetura de rede ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/network/network.html) e nos [fluxos de transação ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html) do Hyperledger Fabric.

- **Logs da autoridade de certificação**
  A Autoridade de Certificação gerencia a identidade dos participantes na rede. Nos logs de Autoridade de certificação, é possível localizar logs de quando os participantes geram chaves públicas e privadas para se comunicar com a rede (inscrever-se), ou quando novos membros, peers ou aplicativos se registram com a Autoridade de certificação. Também será possível usar os logs de CA para depuração se houver problemas com a verificação de certificado.

- **Logs de serviço de pedido**
O serviço de pedido é o componente de ligação comum da rede de blockchain. Todas as propostas de transação endossadas por meio de peers, atualizações de canal ou atualizações de associação de rede são enviadas para o serviço de pedido para verificação. Portanto, o serviço de pedido contém logs de quando a rede é iniciada. Ele também contém logs para uma transação que foi rejeitada porque não era adequadamente endossada pelas organizações corretas. Também é possível localizar logs de quando os canais são criados ou atualizados ou de quando uma atualização de canal falha.

- ** Logs de Peer **  
  Os logs de peer contêm os resultados da instalação, da instanciação e da chamada de chaincode. É possível procurar um nome e uma versão de chaincode para localizar os logs de um certo chaincode. Também é possível ver os logs de um chaincode específico na [seção de chaincode do monitor de canal](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network-monitor-channel-cc). As mensagens, geradas por suas propostas de transação, ou quaisquer problemas de tempo limite com suas solicitações de proposta, podem ser localizadas nos logs de peer. Os logs de peer também contêm erros de transações que foram rejeitadas para não atender à [política de aprovação do chaincode](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-endorsement-policy). Também é possível localizar os resultados das solicitações de junção de canal.

O Hyperledger Fabric fornece diferentes [níveis de criação de log ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/logging-control.html "controle de criação de log") com base na gravidade da mensagem. O nível de criação de log padrão no {{site.data.keyword.blockchainfull_notm}} Platform é `INFO`. Para visualizar logs adicionais, é possível abrir um [chamado de suporte](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-cases) para configurar o nível de criação de log para o nível `DEBUG` mais detalhado. Esteja ciente de que os logs de nível `DEBUG` exibem uma grande quantia de mensagens de fofoca que podem precisar ser filtradas. Procure `warning` ou `error` em suas mensagens para detectar problemas por meio dos componentes do Hyperledger Fabric. Para detectar se o contêiner do componente falha ou é encerrado, procure por mensagens `panic` ou `killed` enviadas pelo {{site.data.keyword.cloud_notm}}.

## Visualizando logs no Kibana no Starter Plan
{: #monitor-blockchain-network-viewing-kibana-logs}

Os logs de sua rede do Starter Plan são reunidos pelo serviço [{{site.data.keyword.cloud_notm}} Log Analysis ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/catalog/services/log-analysis "serviço Log Analysis service"). Por padrão, os seus logs são coletados pelo Plano Lite do serviço de Análise do log. Esse plano é gratuito e **armazena seus logs por três dias** antes de descartá-los. Ele também permite que você **procure somente os primeiros 500 MB de seus logs por dia**. Se os logs de rede excederem 500 MB, não será possível visualizar novos logs no Kibana. Se a sua rede gerar mais de 500 MB de logs ou você desejar reter seus logs por mais de três dias, será possível fazer upgrade para uma versão paga do Serviço de Análise do Log.

Na tela "Visão geral" do Monitor de rede, clique em **Visualizar logs** na lista suspensa no cabeçalho **Ações** para abrir os logs de cada componente de rede na interface do Kibana. Quando o Kibana é aberto, ele exibe logs que são filtrados por uma barra de procura na parte superior. Por exemplo, ao clicar para visualizar os logs de peer, a procura é filtrada por seu ID de rede e ID de peer: `NETWORK_ID_str:"nf8389d520c243004bb21ff5d70fc8939" && NODE_NAME_str:"org1-peer1"`. Será possível inserir um campo adicional na barra de procura se desejar visualizar logs mais específicos. Por exemplo, é possível incluir `&& "marbles"` para exibir os logs do chaincode `"marbles"`. Excluir o termo de componente específico e procurar apenas com o ID de rede, por exemplo, `NETWORK_ID_str:"nf8389d520c243004bb21ff5d70fc8939"`, exibe os logs de todos os componentes de rede.

É possível usar o botão de intervalo de tempo no canto superior direito para mudar o período em que os logs são exibidos. Também é possível usar a guia no lado esquerdo da tela para incluir e remover campos da procura. O campo mais importante a ser exibido é o campo da mensagem. Pode ser útil procurar com uma mensagem sem o registro de data e hora para localizar todas as instâncias desse log de mensagens. Clique no botão **Salvar** para salvar sua procura atual e retornar para uma visualização específica. Para obter mais informações sobre como exibir dados no Kibana, consulte [Guia do Usuário do Kibana ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.elastic.co/guide/en/kibana/6.2/index.html "Guia do Usuário do Kibana"). Também é possível [fazer download de seus logs](https://cloud.ibm.com/docs/services/CloudLogAnalysis/how-to/manage-logs/downloading_logs_cloud.html#downloading_logs) para seu sistema de arquivos local usando a CLI do Log Analysis.

**Nota:** por padrão, o Kibana vem pré-configurado para mostrar logs a partir dos 30 dias de atividade. Se não houve atividade nos últimos 30 dias, você verá uma mensagem informando *Nenhum resultado localizado*. Para visualizar outros logs, é possível clicar no ícone de cronômetro no canto superior direito sob seu nome de usuário e configurar um intervalo de tempo mais amplo, como *Início do ano até hoje*.

## Monitorando canais
{: #monitor-blockchain-network-monitor-channnels}

Entre no Monitor de Rede e localize o canal que você deseja visualizar e monitorar na tela "Canal". Na tela do canal específico, é possível visualizar as informações de status dos dados, os membros e o chaincode instanciado deste canal em três guias:

### Visão Geral do Canal
{: #monitor-blockchain-network-monitor-channel-overview}

A guia "Visão Geral do Canal" mostra as informações de bloco nesse canal:
  * Uma série de pontos de dados, que incluem o número total de blocos que foram criados, o intervalo de tempo desde a última transação, o número de instanciações de chaincode e o número de chamadas de chaincode.
  * Uma tabela que lista todos os blocos neste canal. Expanda um bloco e você poderá visualizar as informações detalhadas sobre o bloco.

  ![Visão geral do canal](../images/channel_overview_detail.png "Visão geral do canal")

### Membros
{: #monitor-blockchain-network-monitor-channel-members}

A guia "Membros" mostra as informações dos membros neste canal, incluindo os endereços de e-mail para os operadores organizacionais.

  ![Channel members](../images/channel_members.png "Channel members")

### Chaincode
{: #monitor-blockchain-network-monitor-channel-cc}

A guia "Chaincode" lista todos os chaincodes que são instanciados nesse canal com o ID do chaincode, a versão e o número de peers que estão executando o chaincode.

Expanda uma linha de chaincode para obter informações detalhadas sobre o chaincode:
  * É possível clicar em **JSON** para visualizar o arquivo JSON do chaincode.
  * É possível clicar em **Logs** para visualizar logs do chaincode. Essa visualização exibe os logs de quais peers você instalou o chaincode e são filtrados com o nome e a versão do chaincode.

    É recomendável incluir mensagens de sucesso ou de erro exclusivas depois de cada função de chaincode para ajudar a monitorar e depurar o chaincode. Se você tiver um chaincode complexo que use muitos arquivos diferentes, será possível incluir uma palavra-chave exclusiva nos logs de chaincode para ajudá-lo a localizar mensagens de diferentes estágios de transação.
   * É possível clicar em **Excluir** para remover o contêiner de chaincode em execução. Observe que a exclusão do contêiner de chaincode em execução não exclui realmente o chaincode. Um chaincode instanciado na rede de blockchain não pode ser excluído.

  ![Chaincode de canal](../images/channel_chaincode.png "Chaincode de canal")


## Monitorando o chaincode
{: #monitor-blockchain-network-monitor-chaincode}

Insira o Monitor de rede e abra a tela "Instalar código". Se você tiver um chaincode em execução, será possível ver o chaincode com IDs e versões de chaincode na tabela. Escolha um peer na lista suspensa e será possível ver todo o chaincode desse peer na tabela. É possível visualizar os logs do chaincode na [guia "Chaincode"](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network-monitor-channel-cc) de sua tela "Canal" específica.

  ![Chaincode](../images/installed_cc.png "Chaincode")

<!----
## Monitoring sample applications
{: #monitor-apps}

In a Starter Plan network, you can view and access sample applications in the "Try Samples" screen of the Network Monitor.  After you deploy a sample application, you can click the **Launch** button to enter your application interface, or the **View on GitHub** link to visit the code repository.  For more information, see [Deploying sample applications](/docs/services/blockchain/prebuilt_samples.html#deploying-sample-applications).

  ![Sample applications](../images/sampleappflow0.png "Sample applications")
--->
