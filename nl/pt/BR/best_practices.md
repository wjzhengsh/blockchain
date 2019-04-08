---

copyright:
  years: 2019
lastupdated: "2019-03-20"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Melhores práticas para desenvolvimento de aplicativo
{: #best-practices-app}

Este guia é voltado para usuários que já aprenderam o básico do desenvolvimento de aplicativo e estão prontos para escalar sua solução. Siga estas melhores práticas para maximizar o desempenho de sua rede e evitar tempo de inatividade do aplicativo.
{:shortdesc}

## Conectividade e disponibilidade do aplicativo
{: #best-practices-app-connectivity-availability}

O Hyperledger Fabric [Fluxo de transação ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Fluxo de Transação"){:new_window} abrange vários componentes, com os aplicativos clientes desempenhando uma função exclusiva. O SDK envia propostas de transação para os peers para endosso. Em seguida, ele coleta as propostas endossadas a serem enviadas para o serviço de ordenação, que então envia blocos de transações para os peers a serem incluídos nos livros-razão do canal. Os desenvolvedores de aplicativos de produção devem estar preparados para gerenciar suas interações entre o SDK e suas redes para eficiência e disponibilidade.

### Gerenciando Transações
{: #best-practices-app-managing-transactions}

Os clientes do aplicativo devem assegurar que suas propostas de transação sejam validadas e que as propostas sejam concluídas com sucesso. Uma proposta pode ser atrasada ou perdida por vários motivos, como uma indisponibilidade de rede ou uma falha de componente. É necessário codificar seu aplicativo para [alta disponibilidade](/docs/services/blockchain/best_practices.html#best-practices-app-ha-app) para manipular a falha do componente. Também é possível [aumentar os valores de tempo limite](/docs/services/blockchain/best_practices.html#best-practices-app-set-timeout-in-sdk) em seu aplicativo para evitar que as propostas excedam o tempo limite antes que a rede possa responder.

Se um chaincode não estiver em execução, a primeira proposta de transação enviada para esse chaincode o iniciará. Enquanto o chaincode está iniciando, todas as outras propostas são rejeitadas com um erro indicando que o chaincode está iniciando atualmente. Isso é diferente da invalidação da transação. Se alguma proposta for rejeitada enquanto o chaincode estiver sendo iniciado, os aplicativos clientes precisarão reenviar as propostas rejeitadas após o chaincode ser iniciado. Os aplicativos clientes podem usar uma fila de mensagens para evitar a perda de propostas de transação.

É possível usar um serviço de evento baseado em canal para monitorar transações e construir filas de mensagens. A classe [channelEventHub ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} pode registrar listeners com base em eventos de transação, de bloco e de chaincode. Listeners baseados em canal do canal eventhub podem ser escalados para vários canais e distinguir entre tráfego em diferentes canais.

É recomendado que você use o channelEventHub em vez da classe EventHub antiga. O EventHub tem encadeamento único e contém eventos de todos os canais que podem reduzir a velocidade ou até mesmo interromper listeners nos canais. A classe eventHub também não fornece nenhuma garantia de que um evento será entregue e não fornece nenhuma maneira de recuperar eventos de um determinado ponto, como um número de bloco, para rastrear eventos que foram perdidos.

**Nota:** a classe EventHub do peer será descontinuada em uma liberação futura do SDK do Fabric. Se você tiver aplicativos existentes que usem a classe EventHub do peer, atualize-os para que usem a classe EventHub do canal no lugar. Para obter mais informações, veja [Como usar o serviço de evento baseado em canal ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "Como usar o serviço de evento baseado em canal"){:new_window} na documentação do SDK do Node.

### Abrindo e fechando conexões de rede
{: #best-practices-app-connections}

Ao criar objetos peer e orderer com o SDK antes de enviar propostas de transação, você está abrindo uma conexão gRPC entre seu aplicativo e o componente de rede. Por exemplo, o comando a seguir abre uma conexão com o `org1-peer1`. Essa conexão continua ativa enquanto seu aplicativo está em execução.

```
var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
```
{:codeblock}

Quando você gerencia as conexões entre seu aplicativo e sua rede, você pode considerar as recomendações a seguir.

- Reutilize objetos peer e orderer ao interagir com sua rede, em vez de abrir novas conexões para enviar transações. A reutilização de objetos peer e orderer pode salvar recursos e levar a um melhor desempenho.  
- Para manter uma conexão persistente com seus componentes de rede, use [gRPC keepalives ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://github.com/grpc/grpc/blob/master/doc/keepalive.md "gRPC Keepalives"). Keepalives mantêm a conexão gRPC ativa e evita que uma conexão "não utilizada" seja fechada. O exemplo a seguir de conexão peer inclui opções do gRPC no objeto [Opções de conexão ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/global.html#ConnectionOpts "Conexão"). As opções de gRPC são configuradas com valores que o {{site.data.keyword.blockchainfull_notm}} Platform recomenda.  
  ```
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null},
  "grpcOptions": {
    "grpc.keepalive_time_ms": 120000,
    "grpc.http2.min_time_between_pings_ms": 120000,
    "grpc.keepalive_timeout_ms": 20000,
    "grpc.http2.max_pings_without_data": 0,
    "grpc.keepalive_permit_without_calls": 1
    }
  );
  ```
  {:codeblock}

  Também é possível localizar essas variáveis com as configurações recomendadas na seção `"peers"` de seu perfil de conexão de rede. As opções recomendadas serão importadas para seu aplicativo automaticamente se você usar o [perfil de conexão com o SDK](/docs/services/blockchain/v10_application.html#best-practices-app-connection-profile) para se conectar aos seus terminais de rede.

- Quando uma conexão não for mais necessária, use os comandos `peer.close()` e `orderer.close()` para liberar recursos e evitar degradação de desempenho. Para obter mais informações, consulte as classes [peer close ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Peer.html#close__anchor "peer close") e [orderer close ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Orderer.html#close__anchor "orderer close") na documentação do Node SDK. Se você usou um perfil de conexão para incluir peers e solicitadores em um objeto de canal, será possível fechar todas as conexões que estão designadas a esse canal usando um comando `channel.close()`.

### Aplicativos altamente disponíveis
{: #best-practices-app-ha-app}

Como uma boa prática de alta disponibilidade, é altamente recomendável implementar um mínimo de dois peers por organização para failover. É necessário adaptar seus aplicativos para alta disponibilidade também. Instale o chaincode em ambos os peers e inclua-os em seus canais. Em seguida, prepare-se para enviar propostas de transação para ambos os terminais de peer ao configurar sua rede e construir sua lista de destino de peer. As redes do Enterprise Plan possuem vários solicitadores para failover, o que permite que seu aplicativo cliente envie transações endossadas para um solicitador diferente se um solicitador não estiver disponível. Se você usar seu [perfil de conexão](/docs/services/blockchain/v10_application.html#dev-app-connection-profile) em vez de incluir terminais de rede manualmente, assegure-se de que seu perfil esteja atualizado e que os peers e solicitadores adicionais tenham sido incluídos no canal relevante na seção `channels` do perfil. Em seguida, o SDK pode incluir os componentes que são associaos ao canal usando o perfil de conexão.

## Ativando o TLS mútuo
{: #best-practices-app-mutual-tls}

Se estiver executando redes do Enterprise Plan que está no nível do Fabric V1.1, você terá a opção de [ativar o TLS mútuo](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) para seus aplicativos. Se você ativar o TLS mútuo, será necessário atualizar os seus aplicativos para suportarem essa função. Caso contrário, seus aplicativos não poderão se comunicar com a rede.

No Perfil de conexão, localize a seção `certificateAuthorities`, na qual é possível localizar os atributos a seguir que são necessários para inscrever e obter os certificados para se comunicar com sua rede usando o TLS mútuo.

- `url`: URL para conexão com a autoridade de certificação que pode fornecer certificados do TLS mútuo
- `enrollId`: ID de inscrição para usar para obter um certificado
- `enrollSecret`: segredo de inscrição para usar para obter um certificado
- `x-tlsCAName`: o nome da CA a ser usado para obter o certificado que permite que o aplicativo se comunique com o TLS mútuo.

Para obter mais informações sobre como atualizar seus aplicativos para suportar o TLS mútuo, consulte [Como configurar o TLS mútuo ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "tls mútuo"){:new_window}.


## (Opcional) Configurando valores de tempo limite nos SDKs do Fabric
{: #best-practices-app-set-timeout-in-sdk}

SDKs do Fabric configuram valores de tempo limite padrão em aplicativos clientes para eventos na rede de blockchain. Veja o exemplo a seguir sobre as configurações de tempo limite padrão no SDK do Fabric Java. O caminho de arquivo é `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
    /**
     * Timeout settings
     **/
    public static final String PROPOSAL_WAIT_TIME = "org.hyperledger.fabric.sdk.proposal.wait.time";
    public static final String CHANNEL_CONFIG_WAIT_TIME = "org.hyperledger.fabric.sdk.channelconfig.wait_time";
    public static final String TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME = "org.hyperledger.fabric.sdk.client.transaction_cleanup_up_timeout_wait_time";
    public static final String ORDERER_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer_retry.wait_time";
    public static final String ORDERER_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer.ordererWaitTimeMilliSecs";
    public static final String PEER_EVENT_REGISTRATION_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.eventRegistration.wait_time";
    public static final String PEER_EVENT_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.retry_wait_time";
    public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
    public static final String EVENTHUB_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.eventhub.reconnection_warning_rate";
    public static final String PEER_EVENT_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.peer.reconnection_warning_rate";
    public static final String GENESISBLOCK_WAIT_TIME = "org.hyperledger.fabric.sdk.channel.genesisblock_wait_time";

    ...

    // Default values
    /**
     * Timeout settings
     **/
    defaultProperty(PROPOSAL_WAIT_TIME, "20000");
    defaultProperty(CHANNEL_CONFIG_WAIT_TIME, "15000");
    defaultProperty(ORDERER_RETRY_WAIT_TIME, "200");
    defaultProperty(ORDERER_WAIT_TIME, "10000");
    defaultProperty(PEER_EVENT_REGISTRATION_WAIT_TIME, "5000");
    defaultProperty(PEER_EVENT_RETRY_WAIT_TIME, "500");
    defaultProperty(EVENTHUB_CONNECTION_WAIT_TIME, "5000");
    defaultProperty(GENESISBLOCK_WAIT_TIME, "5000");
    /**
     * This will NOT complete any transaction futures time out and must be kept WELL above any expected future timeout
     * for transactions sent to the Orderer. Para a limpeza interna apenas.
     */
    defaultProperty(TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME, "600000"); //10 min.
```
{:codeblock}

No entanto, você pode precisar mudar os valores de tempo limite padrão em seu próprio aplicativo. Por exemplo, quando seu aplicativo chama uma transação que precisa de mais de 5.000 ms, que é o valor de tempo limite padrão para que a conexão do hub de evento responda, talvez ocorra um erro com falha, pois o evento de chamada termina em 5.000 ms, antes da conclusão da transação. É possível configurar a propriedade de sistema para sobrescrever os valores padrão de seu aplicativo cliente. Como os valores padrão são inicializados antes de você configurar a propriedade de sistema, a propriedade de sistema pode não entrar em vigor. Portanto, é necessário configurar a propriedade de sistema para tempo limite em uma construção estática em seu aplicativo cliente. Veja o exemplo a seguir sobre a mudança do valor de tempo limite para a conexão de hub de evento para 15000 milissegundos no SDK do Fabric Java. O caminho de arquivo é `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty (EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE); }
```
{:codeblock}

Se você estiver usando o Node SDK, será possível especificar os valores de tempo limite diretamente no método chamado. Como um exemplo, você usaria a linha abaixo para aumentar o valor de tempo limite para [instanciar um chaincode ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal") para 5 minutos.
```
channel.sendInstantiateProposal (solicitação, 300000);
```
{:codeblock}

## Melhores práticas ao usar o CouchDB
{: #best-practices-app-couchdb-indices}

Se você usar o CouchDB como seu banco de dados de estado, será possível executar consultas de dados JSON por meio de seu chaincode com relação aos dados de estado do canal. É altamente recomendado que você crie índices para suas consultas JSON e os use em seu chaincode. Os índices permitem que seus aplicativos recuperem dados de forma eficiente quando sua rede inclui blocos de transações e entradas adicionais no estado mundial.

Para obter mais informações sobre o CouchDB e como configurar índices, consulte [CouchDB como banco de dados de estado ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB como banco de dados de estado"){:new_window} na documentação do Hyperledger Fabric. Também é possível localizar um exemplo que usa um índice com chaincode no [Tutorial do Fabric CouchDB ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html).

Evite usar o chaincode para consultas que resultarão em uma varredura do banco de dados CouchDB inteiro. As varreduras de banco de dados de duração completa resultarão em tempos de resposta longos e degradarão o desempenho de sua rede. É possível executar algumas das etapas a seguir para evitar e gerenciar consultas grandes:
- Configure índices com seu chaincode.
- Todos os campos no índice também devem estar nas seções de seletor ou classificação de sua consulta para que o índice seja usado.
- Consultas mais complexas terão um desempenho inferior e terão menos probabilidade de usar um índice.
- É necessário tentar evitar operadores que resultarão em uma varredura de tabela integral ou em uma varredura de índice integral, como `$or`, `$in` e `$regex`.

É possível localizar exemplos que demonstram como as consultas usam índices e que tipo de consultas terão o melhor desempenho no [Tutorial do Fabric CouchDB ![Ícone de link externo](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html#use-best-practices-for-queries-and-indexes).

Os peers no {{site.data.keyword.blockchainfull_notm}} Platform têm um queryLimit configurado e retornarão somente 10.000 entradas do banco de dados de estado. Se a sua consulta atingir o queryLimit, será possível usar múltiplas consultas para obter os resultados restantes. Se você precisar de mais resultados de uma consulta de intervalo, inicie as consultas subsequentes com a última chave retornada pela consulta anterior. Se você precisar de mais resultados de consultas JSON, classifique sua consulta usando uma das variáveis em seus dados, em seguida, use o último valor da consulta anterior em um filtro 'maior que' para a próxima consulta.

Não consulte o banco de dados inteiro com o propósito de agregação ou relatório. Se você desejar construir um painel ou coletar grandes quantias de dados como parte de seu aplicativo, será possível consultar um banco de dados fora da cadeia que replica os dados de sua rede de blockchain. Isso permitirá que você entenda os dados no blockchain sem degradar o desempenho de sua rede ou interromper transações.

É possível usar o cliente de serviços de evento baseado em canal fornecido pelos SDKs do Fabric para construir um armazenamento de dados fora da cadeia. Por exemplo, é possível usar um listener de bloco para obter as transações mais recentes que estão sendo incluídas em um livro-razão de canal. Os conjuntos de leitura e gravação de transação das transações válidas podem então ser usados para atualizar uma cópia do estado mundial que foi armazenada em um banco de dados separado. Para obter mais informações, veja [Como usar o serviço de evento baseado em canal ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "Como usar o serviço de evento baseado em canal"){:new_window} na documentação do SDK do Node.
