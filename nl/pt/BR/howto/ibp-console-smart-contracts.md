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

# Tutorial de implementação de um contrato inteligente na rede
{: #ibp-console-smart-contracts}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Um contrato inteligente é o código, às vezes referido como chaincode, que permite ler e atualizar dados no livro-razão de blockchain. Um contrato inteligente pode transformar a lógica de negócios em um programa executável acordado e verificado por todos os membros de uma rede de blockchain. Este tutorial é a terceira parte na [série de tutorial de rede de amostra](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-sample-tutorial) e descreve como implementar contratos inteligentes para iniciar transações na rede de blockchain. Os dois tutoriais anteriores descrevem como criar componentes no console do {{site.data.keyword.blockchainfull_notm}} Platform e conectá-los a componentes que são criados em outros clusters para criar uma rede de blockchain verdadeiramente **distribuída**
{:shortdesc}

Os contratos inteligentes são instalados em peers e, em seguida, instanciados em canais. **Todos os membros que desejam enviar transações ou ler dados usando um contrato inteligente precisam instalar o contrato em seu peer.** Um contrato inteligente é definido por seu nome e versão. Portanto, tanto o nome quanto a versão do contrato instalado precisam ser consistentes em todos os peers no canal que planejam executar o contrato inteligente.

Depois que um contrato inteligente é instalado nos peers, um único membro de rede instancia o contrato no canal. O membro de rede precisa ter se associado ao canal para executar essa ação. A instanciação atualiza o livro-razão com os dados iniciais usados pelo contrato inteligente e, em seguida, inicia os contêineres de contrato inteligente nos peers associados ao canal que têm o contrato instalado. Os peers poderão então usar os contêineres em execução para transacionar.  
- **Somente um membro de rede precisa instanciar um contrato inteligente.**
- **Se um peer com um contrato inteligente instalado se associar a um canal no qual a mesma versão de contrato inteligente já tiver sido instanciada, o contêiner de contrato inteligente será iniciado automaticamente.**  

Neste tutorial, vamos percorrer as etapas para usar o console do {{site.data.keyword.blockchainfull_notm}} Platform para gerenciar a implementação de contratos inteligentes em sua rede:

- [ Instale contratos inteligentes em seus peers ](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-install).
- [Instancie-os em canais](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate).
- [ Especificar políticas de aprovação ](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse).
- [Faça upgrade das políticas e do código de contrato inteligente](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade).


**Antes de iniciar**

Antes de poder instalar um contrato inteligente, assegure-se de que você tenha as coisas a seguir prontas.

- Deve-se [construir uma rede ](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) ou [associar-se a uma rede](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) usando o console do {{site.data.keyword.blockchainfull_notm}} Platform.
- Como os contratos inteligentes são instalados em peers, assegure-se de que você [inclua peers](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-peer-org1) em seu console.  
- Contratos inteligentes são instanciados em um canal. Portanto, deve-se [criar um canal](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) ou [associar-se a um canal](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2) usando seu console.

## Etapa um: Gravar um contrato inteligente

O console do {{site.data.keyword.blockchainfull_notm}} gerencia a *implementação* de contratos inteligentes em vez de desenvolvimento. Se você estiver interessado em desenvolver contratos inteligentes, será possível começar a usar os tutoriais fornecidos pela comunidade do Hyperledger Fabric e o conjunto de ferramentas fornecido pela {{site.data.keyword.IBM_notm}}.

- Para saber como os contratos inteligentes podem ser usados para realizar transações entre múltiplas partes, veja o [ tópico Desenvolvendo aplicativos ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "tópico Desenvolvendo aplicativos") na documentação do Hyperledger Fabric.
- Para obter um tutorial completo de ponta a ponta sobre como usar um aplicativo para interagir com contratos inteligentes, veja o [tutorial Papel comercial do Hyperledger Fabric ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html "tutorial Papel comercial do Hyperledger Fabric").
- Para aprender sobre como incorporar os mecanismos de controle de acesso em seu contrato inteligente, veja [Chaincode for Developers ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/chaincode4ade.html#chaincode-access-control "Chaincode for Developers").
- Quando você estiver pronto para começar a construir contratos inteligentes, será possível usar a [Extensão do {{site.data.keyword.blockchainfull_notm}}Visual Studio Code ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform "{{site.data.keyword.blockchainfull_notm}} Platform - Visual Studio Marketplace") para começar a construir seu próprio projeto de contrato inteligente. Também é possível usar essa extensão para [conectar-se diretamente à sua rede por meio do Visual Studio Code](docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-vscode).
- Quando você estiver pronto para instalar, o contrato inteligente deverá ser empacotado em [Formato .cds ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "empacotando contratos inteligentes") para que ele possa ser instalado nos peers. Para obter mais informações, veja [Empacotando contratos inteligentes](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract).
<!-- Update the tutorial link to release1-4 when it is published -->


## Etapa dois: Instalar um contrato inteligente
{: #ibp-console-smart-contracts-install}

Use seu console para executar estas etapas:

1. Clique na guia **Contratos inteligentes** para instalar um ou mais contratos inteligentes.
2. Clique em **Instalar contrato inteligente ** para fazer upload do arquivo de pacote de contrato inteligente em [Formato .cds ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4noah.html#packaging "empacotando contratos inteligentes").
É possível usar a extensão do {{site.data.keyword.blockchainfull_notm}} Visual Studio Code para [criar um pacote de contrato inteligente](/docs/services/blockchain/vscode-extension.html#packaging-a-smart-contract). Ao instalar o pacote por meio da guia **Contratos inteligentes**, é possível selecionar um ou mais nós de peer nos quais instalar os contratos inteligentes.

Se somente um peer existir no console, o contrato inteligente será instalado nele. Você não é solicitado a selecionar um peer no qual instalar o contrato inteligente. É possível navegar para a guia de nós e clicar em um peer que seja gerenciado por seu console para visualizar a lista de contratos inteligentes que foram instalados em um peer individual.

É possível retornar à guia **Contratos inteligentes** mais tarde para instalar o contrato inteligente em peers adicionais, mesmo após o contrato inteligente ter sido instanciado no canal. Se a versão do contrato inteligente que você instalar for a mesma que a versão instanciada, esses peers adicionais poderão processar transações exatamente como os peers existentes.
{:tip}

**Esse console não pode ser usado para instalar os arquivos `.bna` do Hyperledger Composer.**

<!-- Instead, `.bna` files must be installed on a peer by using the [`Composer` CLI commands ![External link icon](../images/external_link.svg "External link icon")]("peer chaincode" "peer chaincode").  -->

## Etapa três: Instanciar um contrato inteligente
{: #ibp-console-smart-contracts-instantiate}

Contratos inteligentes são instanciados em um canal. Qualquer membro do console com peers associados a um canal pode instanciar um contrato inteligente e especificar a [política de endosso](/docs/services/blockchain/glossary.html#glossary-endorsement-policy) associada.

Use seu console para executar estas etapas:

1. Na guia de contratos inteligentes, localize o contrato inteligente na lista instalada em seus peers e clique em **Instanciar** no menu overflow no lado direito da linha.
2. No painel lateral que se abre, selecione um canal, `channel1`, no qual instanciar o contrato inteligente e selecione o solicitador no qual o canal reside, `Orderer`, se você estiver seguindo adiante no tutorial. Clique em **Avançar**.
3. Especifique a [política de endosso para o contrato inteligente](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-endorse), descrito na seção a seguir.
4. Também é necessário selecionar os membros da organização a serem incluídos na política. Se estiver seguindo adiante no tutorial, isso será `org1msp` e possivelmente `org2msp` se você tiver concluído os tutoriais **Construir uma rede** e **Associar-se a uma rede**.
5. No último painel, você é solicitado a especificar a função de contrato inteligente que deseja executar quando o contrato inteligente é iniciado, juntamente com os argumentos associados a serem passados para essa função.

É possível visualizar todos os contratos inteligentes que foram instanciados em um canal, clicando no ícone de canal na navegação à esquerda, selecionando um canal na tabela e, em seguida, clicando na guia **Detalhes do canal**.

Esteja ciente de que se você usar um cluster de serviço grátis do {{site.data.keyword.cloud_notm}} Kubernetes, a instanciação poderá levar significativamente mais tempo do que em um cluster pago. Dependendo do número de peers que você implementou em seu cluster, isso pode levar vários minutos.

Se o seu contrato inteligente incluir uma definição de coleta de dados privados, o contrato inteligente não poderá ser instanciado por meio do console do {{site.data.keyword.blockchainfull_notm}}. Veja estas [recomendações](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) para saber como instanciar um contrato inteligente que inclui dados privados.
{:note}

A combinação de **instalação e instanciação** é um recurso poderoso porque permite que um peer use um único contrato inteligente em vários canais. Os peers podem desejar se associar a múltiplos canais que usam o mesmo contrato inteligente, mas com conjuntos diferentes de membros de rede capazes de acessar os dados. Um peer poderá instalar o contrato inteligente uma vez e, em seguida, usar o mesmo contêiner de contrato inteligente em qualquer canal no qual ele tenha sido instanciado. Essa abordagem leve economiza espaço de cálculo e armazenamento, além de ajudar a escalar sua rede.

## Etapa quatro: enviar transações usando seus aplicativos clientes
{: #ibp-console-smart-contracts-connect-to-SDK}

Depois que um contrato inteligente tiver sido instanciado em um canal, será possível usar um aplicativo cliente para transacionar com outros membros de sua rede. Os aplicativos podem chamar a lógica de negócios contida em contratos inteligentes para criar, transferir ou atualizar ativos no livro-razão do blockchain.

### Conectar com SDK
{: #ibp-console-smart-contracts-connect-to-SDK-panel}
A guia **Contratos inteligentes** contém as informações necessárias para se conectar a um contrato inteligente instanciado por meio de um aplicativo do cliente. Próximo a cada contrato inteligente instanciado, navegue para o menu overflow. Clique no botão **Conectar a seu SDK**. Isso abre um painel lateral que fornece as informações necessárias para se conectar a esse contrato inteligente: o nome do contrato, o nome do canal e seu perfil de conexão. Para obter mais informações, consulte  [ Criando aplicativos ](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app).

## Especificando uma política de endosso
{: #ibp-console-smart-contracts-endorse}

Cada contrato inteligente deve ter uma política de endosso, que é especificada durante a instanciação. A política de endosso especifica o conjunto de organizações, os peers, em um canal que pode executar o contrato inteligente e validar independentemente a saída de transação. Por exemplo, uma política de endosso pode especificar que uma transação será incluída no livro-razão somente se a maioria dos membros no canal endossar a transação. A organização que instancia o contrato inteligente pode selecionar entre os membros que instalaram o contrato inteligente para se tornarem validadores e configura a política de endosso para todos os membros do canal. É possível atualizar sua política de endosso seguindo as etapas para [atualizar seu contrato inteligente](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-upgrade).

Quando você segue as etapas para [instanciar um contrato inteligente](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-instantiate), é possível usar o painel lateral para configurar a política de endosso de um contrato depois de selecionar o canal. Use esse painel para especificar uma política simples, selecionando os peers que precisam endossar a transação na lista de peers que instalaram o contrato inteligente no canal. É possível usar esse método para especificar uma política de endosso de todos os membros do canal, a maioria deles, um único membro ou um simples +1 evitando que os membros assinem automaticamente. A política de endosso padrão é configurada como `1 of x`, o que significa que somente um único membro é necessário para endossar uma transação de contrato inteligente.

Clique no botão **Avançado** se você desejar especificar uma política no formato JSON. É possível usar esse método para especificar políticas de endosso mais complicadas, como requerer que um determinado membro do canal tenha que validar uma transação, junto com a maioria de outros membros. É possível localizar [exemplos adicionais de políticas de endosso avançado ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/arch-deep-dive.html#example-endorsement-policies "Políticas de endosso de exemplo") na documentação do Hyperledger Fabric. Para obter mais informações sobre como gravar políticas de endosso em JSON, veja a [Documentação do SDK do Hyperledger Fabric Node ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/global.html#ChaincodeInstantiateUpgradeRequest "Documentação do SDK do Hyperledger Fabric Node").

## Fazendo upgrade de um contrato inteligente
{: #ibp-console-smart-contracts-upgrade}

É possível fazer upgrade de um contrato inteligente para mudar seu código ou política de endosso enquanto mantém seu relacionamento com os ativos no livro-razão. Há uma variedade de razões pelas quais você pode desejar fazer upgrade de um contrato inteligente instanciado.
1. É possível fazer upgrade do contrato inteligente para incluir ou remover a funcionalidade de seu código e iterar sobre a lógica de sua rede de negócios.
2. Sempre que um novo membro é incluído em um canal, a política de endosso dos contratos inteligentes instanciados *deve* ser atualizada para incluir o novo membro do canal. Para trabalhar com o novo membro do canal, o contrato inteligente deve ser reempacotado com um novo número de versão e instanciado no canal, mesmo se o contrato inteligente em si não tiver mudanças. Caso contrário, o endosso de transação não poderá ser bem-sucedido.
3. Os argumentos de inicialização de contrato inteligente foram mudados.

**Antes de fazer upgrade de um contrato inteligente instanciado, a nova versão do contrato inteligente deve ser instalada em todos os peers no canal que está executando o nível anterior do contrato inteligente.**

### Como fazer upgrade de um contrato inteligente
{: #ibp-console-smart-contracts-upgrade-howto}

Para fazer upgrade de um contrato inteligente, instale o código atualizado especificando o mesmo nome de contrato inteligente, mas usando e um novo número de versão. Se você tiver instalado uma versão mais recente de um contrato inteligente em qualquer peer no canal, observe que a versão original agora tem o botão `Upgrade Available` próximo a ela na tabela **Contratos inteligentes instanciados**.

{:important}
Quando um novo membro que executará o contrato inteligente se associa ao canal, é obrigatório atualizar o contrato inteligente instalando uma nova versão em todos os peers e instanciando-a no canal com uma política de endosso modificada que inclui o novo membro.

- Role para baixo até a tabela **Contratos inteligentes instanciados**.
- Na tabela **Contratos inteligentes instanciados**, localize a versão e o canal do contrato inteligente que você deseja fazer upgrade. Eles devem ter o rótulo **Upgrade disponível** próximo a eles.
- Clique no **menu overflow** no lado direito da linha de contrato inteligente e clique em **Upgrade** para executar as etapas a seguir:  

 1. Selecione a versão do contrato inteligente que você deseja fazer upgrade no canal na lista suspensa.
 2. Atualize a política de endosso, incluindo ou removendo membros do canal. Também é possível clicar em **Avançado** para colar em uma nova sequência formatada JSON, que modifica a política existente.
 3. (Opcional) Modifique os valores do argumento de inicialização de contrato inteligente se os parâmetros tiverem sido mudados. Se você não tiver certeza sobre isso, verifique com seu desenvolvedor de contrato inteligente. Se eles não tiverem sido mudados, esse campo poderá ser deixado em branco.

Depois de fazer upgrade do contrato inteligente, você mudará a versão do contrato que é instanciada no canal e mudará o contêiner de contrato inteligente para todos os peers que instalaram a nova versão.

### Considerações sobre quando você faz upgrade de contratos inteligentes
{: #ibp-console-smart-contracts-upgrade-considerations}

1. Eu preciso instalar a nova versão do contrato inteligente em todos os meus peers?  

  Quando um contrato inteligente é chamado em um peer, ele tenta executar a versão que está instanciada no canal. Se uma versão anterior estiver em execução no peer, isso resultará em um erro. Portanto, antes de fazer upgrade de um contrato inteligente em um canal, *a melhor prática é instalar a versão mais recente do contrato inteligente em todos os peers que estão executando a versão anterior.*  

2. Uma versão de contrato inteligente subsequente ainda pode processar dados no livro-razão de uma versão anterior do contrato inteligente?  

  Sim. Contanto que o novo código de contrato inteligente trate os dados de uma maneira compatível (incluindo novos campos JSON e não mudando a semântica ou o tipo de campos existentes), qualquer versão subsequente do contrato inteligente poderá ser executada com relação aos dados de uma versão anterior.

3. O que acontecerá com meus peers se eu esquecer de fazer upgrade deles para a versão mais recente antes de atualizar o contrato inteligente no canal?  

  Depois de atualizar o canal para usar a nova versão do contrato inteligente, se ainda houver peers no canal executando a versão anterior, eles não estarão mais aptos a endossar transações para o contrato inteligente. Além disso, você se arrisca a não ter endossos suficientes para que as transações sejam confirmadas no livro-razão, dependendo de como a política de endosso de contrato inteligente está definida. No entanto, é possível instalar a nova versão do contrato inteligente nesses peers mais tarde e eles serão novamente capazes de endossar transações, atualizando-se efetivamente.

## Dados privados
{: #ibp-console-smart-contracts-private-data}

Os dados privados são um recurso de redes do Hyperledger Fabric na versão 1.2 ou superior e são usados para manter informações confidenciais privadas de outros membros de organização **em um canal**. A privacidade de dados é obtida por meio do uso de [coletas de dados privados ![Ícone de link externo")](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#what-is-a-private-data-collection "O que é uma coleta de dados privados?"). Por exemplo, vários atacadistas e um conjunto de agricultores podem ser associados a um único canal. Se um agricultor e um atacadista desejam transacionar privadamente, eles podem criar um canal para esse propósito. Mas eles também podem decidir criar uma coleta de dados privados no contrato inteligente que governa suas interações de negócios para manter a privacidade sobre aspectos sensíveis da venda, como o preço, sem precisar criar um canal secundário. Para saber mais sobre quando usar dados privados em um blockchain, visite o artigo de conceito de [Dados privados ![Ícone de link externo")](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#private-data "Dados privados") na documentação do Fabric.

Para usar dados privados com o beta 2.0 grátis do {{site.data.keyword.blockchainfull_notm}} Platform, as três condições a seguir devem ser satisfeitas:  
1. **Defina a coleta de dados privados.** Um arquivo de coleta de dados privados pode ser incluído em seu contrato inteligente. Em seguida, no tempo de execução, seu aplicativo cliente pode usar as APIs de chaincode específicas de dados privados para inserir e recuperar dados da coleta. Para obter mais informações sobre como usar coletas de dados privados com o seu contrato inteligente, veja o tutorial em [Usando dados privados ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/private_data_tutorial.html "Usando dados privados") na documentação do Fabric.  

2. **Instale e instancie o contrato inteligente.** Depois que a coleta de dados privados do contrato inteligente tiver sido definida, será necessário instalar o contrato inteligente nos peers dos membros do canal. Quando você instanciar o contrato inteligente no canal, será necessário especificar a configuração de coleta. O console do {{site.data.keyword.blockchainfull_notm}} não fornece atualmente uma maneira de especificar uma definição de coleção **durante** a instanciação de contrato inteligente. No entanto, é possível usar o SDK do Fabric para instalar, instanciar ou atualizar um contrato inteligente que use dados privados. Para obter mais informações, consulte [Como usar dados privados ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/release-1.4/tutorial-private-data.html "como usar dados privados") na documentação do Node SDK.  

 **Nota:** um cliente precisa ser um administrador de seu peer a fim de instalar ou instanciar um contrato inteligente. Portanto, é necessário fazer download dos certificados da identidade de administrador de peer por meio de sua carteira eletrônica do console e passar a chave pública e privada do administrador do peer diretamente para o SDK em vez de criar uma identidade de aplicativo. Para obter um exemplo de como passar um par de chaves para o SDK, veja [Conectando-se à sua rede usando APIs do SDK do Fabric de nível inferior](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-low-level).  

3. **Configure peers de âncora.** Como o [gossip ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html "Protocolo de disseminação de dados de gossip") de organização deve ser ativado para que os dados privados funcionem, um peer de âncora deve existir para cada organização na definição de coleção. Esse peer de âncora não é um **tipo** especial de peer, é apenas o peer que a organização torna conhecido para outras organizações e, ao fazer isso, inicializa o gossip de organização cruzada. Portanto, pelo menos um [peer de âncora ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html#anchor-peers "Peers de âncora") deve ser definido para cada organização na definição de coleção.
 - Para configurar um peer para ser um peer de âncora, clique na guia **Canais** e abra o canal no qual o contrato inteligente foi instanciado.  
 - Clique na guia  ** Detalhes do canal ** .
 - Role para baixo até a tabela de peers de Âncora e clique em **Incluir peer de âncora**.
 - Selecione pelo menos um peer de cada organização na definição de coleção que você deseja que sirva como o peer de âncora para a organização. Por motivos de redundância, é possível considerar a seleção de mais de um peer de cada organização na coleta.

Seu canal agora está configurado para usar dados privados.

## Detecção de problemas
{: #console-operate-troubleshooting}

**Problema:** a instalação, a instanciação ou o upgrade de um contrato inteligente falha com um erro no console.  
**Solução:** se uma dessas ações em um contrato inteligente falhar, [verifique seus logs de nó](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) quanto a erros.
