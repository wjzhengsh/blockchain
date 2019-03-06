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

# Associar-se a um tutorial de rede
{: #ibp-console-join-network}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

O {{site.data.keyword.blockchainfull}} Platform é uma oferta blockchain-as-a-service que permite desenvolver, implementar e operar aplicativos e redes de blockchain. É possível obter mais informações sobre os componentes de blockchain e como eles trabalham juntos visitando a [Visão geral do componente Blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview). Este tutorial é a segunda parte na [série de tutorial de rede de amostra](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-sample-tutorial) e descreve como criar nós no console do {{site.data.keyword.blockchainfull_notm}} Platform e conectá-los a um consórcio de blockchain hospedado em outro cluster.
{:shortdesc}

Se você ainda não tiver implementado o console em um cluster Kubernetes usando o {{site.data.keyword.cloud_notm}} Kubernetes Service, consulte [Introdução ao {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks). É possível criar um novo cluster Kubernetes para a implementação do console ou usar um existente em sua conta do {{site.data.keyword.cloud_notm}}. Depois de implementar o {{site.data.keyword.blockchainfull_notm}} Platform no cluster Kubernetes, é possível ativar o console para criar e gerenciar os componentes de blockchain.

Independentemente de você implementar em um cluster Kubernetes pago ou gratuito, use o painel do Kubernetes para prestar muita atenção nos recursos à sua disposição ao escolher implementar nós e criar canais. É sua responsabilidade gerenciar seu cluster Kubernetes e implementar recursos adicionais, se necessário. Embora os componentes sejam implementados com êxito em um cluster gratuito, quanto mais componentes forem incluídos, mais lenta será sua execução.
{: note}

## Amostra de séries do tutorial de rede
{: #ibp-console-join-network-structure}

Esta série de três partes do tutorial fornece orientação durante o processo de criação e interconexão de uma rede de múltiplos nós relativamente simples do {{site.data.keyword.blockchainfull_notm}} Platform 2.0, usando o console e aproveitando sua implementação do Kubernetes.

* [Construir um tutorial de rede](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) fornece orientação durante o processo de hospedagem de uma rede ao criar um solicitador e um peer.
* [Associar-se a um tutorial de rede](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) fornece orientação durante o processo de junção de uma rede existente ao criar um peer e associá-lo a um canal.
* [Implementar um contrato inteligente no tutorial de rede ](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) fornece informações sobre como gravar um contrato inteligente e implementá-lo em sua rede.

É possível usar as etapas nestes tutoriais para construir uma rede com várias organizações em um cluster para propósitos de desenvolvimento e teste. Use o tutorial **Construir uma rede** se você desejar fundar um consórcio de cadeia de blockchain. Use o tutorial **Associar-se a uma rede** para conectar um peer à rede. Seguir os tutoriais com membros de consórcio diferentes permite criar uma rede de blockchain realmente **distribuída**.  

Este tutorial é destinado a mostrar como associar um peer a uma rede **existente**. Ele presume que um solicitador, que hospeda a rede, já existe em seu cluster ou em outro cluster do {{site.data.keyword.blockchainfull_notm}} Platform. Se você não tiver uma rede existente para se associar, visite o [tutorial Construir uma rede](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) para saber como criar uma. O tutorial **Associar-se a uma rede** o conduz pelas etapas para criar os componentes de blockchain `Org2` a seguir, destacados na caixa azul:
![Associar-se à estrutura de rede](../images/ib2-join-network.png "Associar-se à estrutura de rede")  
*Figura 1. Associar estrutura de rede*  
Execute as etapas no tutorial **Associe-se à rede** para criar os componentes a seguir e concluir as ações a seguir:

* **Uma organização de peer** `Org2`  
  Crie a definição de Membership Services Provider (MSP) Org2 que define a organização `Org2`.
* ** Um peer **  ` Peer Org2 `   
  O livro-razão do blockchain, `Ledger x` na ilustração acima, é mantido pelos peers distribuídos. O peer é implementado com o [Couch DB ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html) como o banco de dados.
* **Uma Autoridade de Certificação (CA)** `Org2 CA`
Uma CA é o nó que emite certificados para todos os membros da organização. Nós criamos uma CA para a organização de peer `Org2`.
* **Associando-se a um canal**
O tutorial descreve como se associar ao canal que foi criado pelo [tutorial Construir uma rede](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

Em todo este tutorial, fornecemos **valores recomendados** para alguns dos campos no console. Isso permite que os nomes e as identidades sejam mais fáceis de reconhecer nas guias e nas listas suspensas. Esses valores não são obrigatórios, mas você os achará úteis. Fornecemos uma tabela dos valores recomendados após cada tarefa.
{:tip}

## Etapa um: criar uma organização adicional e o seu ponto de entrada para o seu blockchain
{: #ibp-console-join-network-create-ca-org2}

Para cada organização que você deseja criar usando o console, é necessário implementar pelo menos uma CA. Uma CA é o nó que emite certificados para todos os participantes da rede (peers, solicitadores, clientes e assim por diante). Esses certificados, que incluem um par de chaves públicas e privadas, permitem que os participantes da rede se comuniquem, se autentiquem e, finalmente, transacionem. Essas CAs criarão todas as identidades e certificados pertencentes à sua organização, além de definir a própria organização. É possível, então, usar essas identidades para implementar nós, operar sua rede e enviar transações para o blockchain. Para obter mais informações sobre sua CA e as identidades que você precisará criar, consulte [Gerenciando identidades](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities).

Neste tutorial, criaremos uma organização e criaremos **uma CA**.

### Criando sua CA de organização do peer
{: #ibp-console-join-network-create-CA-org2CA}

Como parte deste tutorial, sua CA emite as chaves públicas e privadas para seus usuários e nós. Essas identidades não são gerenciadas pela {{site.data.keyword.IBM_notm}} e as chaves não são armazenadas no cluster Kubernetes ou no console. Eles são armazenados somente no armazenamento local de seu navegador. Portanto, certifique-se de exportar suas identidades e o MSP da organização. Se você tentar acessar o console de uma máquina diferente ou de um navegador diferente, será necessário importar essas identidades e definições de organização.  
{:important}

Execute as etapas a seguir em seu console:  

1. Navegue para a guia **Nós** à esquerda e clique em **Incluir Autoridade de certificação**. Os painéis laterais de quatro etapas permitirão que você customize a CA que você deseja criar e a organização para a qual essa CA emitirá chaves.
2. Clique em  ** {{site.data.keyword.cloud_notm}} **  em  ** Criar autoridade de certificação **.
3. Use o segundo painel lateral para fornecer à sua CA um **nome de exibição**. Nosso valor recomendado para essa CA é `Org2 CA`.
4. No próximo painel, forneça suas credenciais de administrador de CA especificando um **ID de administrador** de `admin` e fornecendo qualquer segredo que você desejar, mas recomendamos `adminpw` para o propósito deste tutorial.
5. Clique em **Enviar**.

**Tarefa: criando a CA da organização de peer**

  | **Campo** | **Nome de exibição** | **ID de inscrição** | **Segredo** |
  | ------------------------- |-----------|-----------|-----------|
  | **Criar CA** | CA do Org2  | admin | adminpw |

*Figura 2. Criando a CA da organização de peer*  
Depois de implementar a CA, você a usará quando criar o MSP de sua organização, registrar usuários e criar seu ponto de entrada para uma rede, o **peer**.

### Usando sua CA para registrar identidades
{: #ibp-console-join-network-use-CA-org2}

Cada nó ou aplicativo que você deseja criar precisa de chaves públicas e privadas para participar na rede de blockchain. Também é necessário criar chaves de administrador para esses nós e aplicativos para que você possa gerenciá-los por meio do console. Você usará a CA para criar duas identidades:

* **Um administrador de organização** Essa Identidade permite que você opere os nós usando o console da plataforma.
* **Uma identidade de peer** Essa identidade permitirá que você implemente um peer.

Para gerar esses certificados, conclua as etapas a seguir:

1. No console, use a guia **Nós** para navegar para o `Org2 CA` que você criou.
2. Depois de selecionar sua CA, será necessário registrar um administrador para esta organização, `org2`, além de uma identidade para o próprio peer. Você já deve ter visto uma identidade nessa página; é o administrador que você criou para a autoridade de certificação. Para registrar nossos novos usuários, clique no botão **Registrar usuário**.
3. Para o administrador da organização, forneça um ID de inscrição de `org2admin`. É possível usar qualquer segredo, mas sugerimos `org2adminpw` para ajudá-lo a seguir adiante. Clique em **Avançar**.
4. Na próxima etapa, configure o Tipo para essa identidade como `client` e selecione uma das organizações afiliadas na lista suspensa. O campo de afiliação é para usuários avançados e não é usado pelo tutorial. Os itens na lista são afiliações padrão do Fabric CA. Se desejar obter mais informações sobre como as afiliações são usadas pelo Fabric CA, consulte este tópico em [Registrando uma nova identidade ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity). Por enquanto, selecione qualquer afiliação na lista, por exemplo, `org2` e clique em **Avançar**.
5. Sinta-se à vontade para deixar os campos **Inscrições máximas** e **Incluir atributos** em branco. Eles não são usados por este tutorial, mas é possível obter mais informações sobre o que eles são neste tópico em [Registrando identidades](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register).
6. Após o administrador da organização ter sido registrado, repita a etapa dois até a etapa cinco para a identidade do peer, usando o mesmo `Org2 CA`, fornecendo a ele um ID de inscrição de `peer2`. Como antes, recomendamos um segredo de `peer2pw` para ajudá-lo a seguir adiante. Essa é uma identidade do nó, portanto, selecione `peer` como o **Tipo** na próxima etapa. Em seguida, ignore **Inscrições máximas** e **Atributos** como antes.

**Tarefa: registrar usuários**

  |  **Campo** | **Descrição** | **ID de inscrição** | **Segredo** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Registrar usuários** |  Administrador do Org2 | org2admin | org2adminpw |
  | | Identidade do peer |  peer2 | peer2pw |

*Figura 3. Usando sua CA para registrar usuários*  

### Criando a organização peer-to-peer MSP
{: #ibp-console-join-network-create-peers-org2}

Agora que criamos a CA do peer e a usamos para **registrar** nossas identidades de organização, precisamos criar uma definição formal da organização de peer, que é conhecida como definição de Membership Services Provider (MSP). Muitos peers podem pertencer a uma organização. **Você não precisa criar uma nova organização toda vez que criar um peer.** Como essa é a primeira vez que passamos pelo tutorial, criaremos o ID do MSP para essa organização. Durante o processo de criação do MSP, nós vamos gerar certificados para a identidade `org2admin` e incluí-los em nossa carteira eletrônica do console.

1. Navegue para a guia **Organizações** na navegação esquerda e clique em **Criar definição de MSP**.
2. Forneça ao seu MSP um nome de exibição como `Org2 MSP` e um ID como `org2msp`. Se desejar especificar seu próprio ID do MSP nesse campo, certifique-se de seguir as especificações sobre as limitações para esse nome na dica de ferramenta.
3. Em **Detalhes da autoridade de certificação raiz**, especifique a CA do peer que criamos como sua CA raiz para sua organização. Se essa for sua primeira vez neste tutorial, você deverá ver somente um: `Org2 CA`. Selecione-o.
4. Os campos **ID de inscrição** e **Segredo de inscrição** abaixo disso serão preenchidos automaticamente com o ID de inscrição e o segredo do primeiro usuário criado com sua CA. Não use esses valores. Em vez disso, forneça o ID de inscrição e o segredo para o administrador da organização, `org2admin` e `org2adminpw`. Em seguida, forneça a essa identidade um nome de exibição, tal como `Org2 Admin`.
5. Clique no botão **Gerar** para inscrever essa identidade como o administrador de sua organização e exportar a identidade para a carteira eletrônica, na qual ela será usada ao criar o peer e ao criar canais.
6. Clique em **Exportar** para exportar os certificados de administrador para o sistema de arquivos. Como dissemos acima, essa identidade não é armazenada no cluster ou gerenciada pelo {{site.data.keyword.IBM_notm}}. Ela é armazenada somente em seu armazenamento local do navegador.
7. Clique em **Criar definição de MSP**.

**Tarefa: criar a definição de MSP da organização de peer**

  |  | **Nome de exibição** | **ID do MSP** | **ID de inscrição**  | **Segredo** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Criar organização** | Org2 MSP | org2msp |||
  | **CA raiz** | CA do Org2 ||||
  | **Certificado do administrador da organização** | |  | org2admin | org2adminpw |
  | **Identidade** | Administrador da Org2 |||||

  *Figura 4. Criar a definição de MSP da organização de peer*  

Depois de criar o MSP, você conseguirá ver o administrador da organização de peer em sua carteira eletrônica do console.

**Tarefa: verificar sua carteira eletrônica do console**

  | **Campo** |  **Nome de exibição** | **Descrição** |
  | ------------------------- |-----------|----------|
  | **Identidade** | Administrador da Org2 | Identidade do Org2 |

  *Figura 5. Verifique sua carteira eletrônica do console*  

Para obter mais informações sobre os MSPs, consulte [Gerenciando organizações](/docs/services/blockchain/howto/ibp-console-organizations.html#ibp-console-organizations).

A exportação e o salvamento de seu MSP é importante porque isso permite que sua organização seja incluída em um consórcio hospedado em outro console.
{:important}

### Criando um peer
{: #ibp-console-join-network-peer-create}

Depois de [criar uma CA](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-create-CA-org2CA), usá-la para registrar identidades e criar o [MSP da organização de peer](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-create-peers-org2), você estará pronto para criar um peer.

#### Qual função os peers executam?
{: #ibp-console-join-network-peer-role}

É importante lembrar-se de que as próprias organizações não mantêm livros-razão. Os peers executam. As organizações também usam peers para assinar propostas de transação e aprovar atualizações de configuração do canal. Como ter pelo menos dois peers em um canal os torna altamente disponíveis, ter pelo menos dois peers associados a um canal é considerado uma melhor prática para implementações de nível de produção. Neste tutorial, mostraremos apenas o processo para criar um único peer.

De uma perspectiva de alocação de recurso, é possível associar os mesmos peers a múltiplos canais. O design do peer assegura que os dados de um canal não possam passar para outro por meio do peer. No entanto, como o peer armazenará um livro-razão separado para cada canal, será necessário assegurar-se de que o peer tenha energia de processamento e armazenamento suficientes para manipular a transação e o carregamento de dados.

#### Implementando seu peer
{: #ibp-console-join-network-deploy-peer-role}

Use seu console para executar as etapas a seguir:

1. Na página **Nós**, clique em **Incluir peer**.
2. Clique no {{site.data.keyword.cloud_notm}} em **Criar um novo peer** e em **Avançar**.
3. Forneça a seu peer um **Nome de exibição** de `Peer Org2`.
4. Na próxima tela, selecione `Org2 CA` como sua CA. Em seguida, insira o ID de inscrição e o segredo para a identidade de peer que você criou para seu peer, `peer2` e `peer2pw`. Em seguida, selecione seu MSP, `Org2 MSP`, na lista suspensa e clique em **Avançar**.
5. O próximo painel lateral solicita informações de CA do TLS. Embora seja possível criar administradores separados para a CA TLS que foi implementada com sua CA, isso não é necessário.
   - Forneça o **ID de inscrição do TLS**, `admin` e o segredo `adminpw`, os mesmos valores são o ID de inscrição e o segredo de inscrição fornecidos ao criar a CA.
   - O **Nome do host do CSR TLS** destina a um usuário avançado e é usado para especificar um nome de domínio customizado que pode ser usado para direcionar o terminal de peer. Deixe o **Nome do host de TLS CSR** em branco por enquanto, ele não é usado neste tutorial.
6. O último painel lateral solicitará para **Associar uma identidade** e torná-la a administradora de seu peer. Selecione sua identidade de administrador de peer `Org2 Admin`.
7. Revise o resumo e clique em **Enviar**.

**Tarefa: implementando um peer**

  |  | **Nome de exibição** | **ID do MSP** | **ID de inscrição** | **Segredo** |
  | ------------------------- |-----------|-----------|-----------|-----------|
  | **Criar peer** | Peer Org2 | org2msp |||
  | **CA** | CA do Org2 ||||
  | **Identidade do peer** | |  | peer2 | peer2pw |
  | **Certificado de administrador** | org2msp ||||
  | **CA TLS** | CA do Org2 ||||
  | **ID de CA TLS** | || admin | adminpw |
  | **Associar identidade** | Administrador da Org2 |||||

  *Figura 6. Implementando um Período*  

## Etapa dois: incluir sua organização na lista de organizações que podem transacionar
{: #ibp-console-join-network-add-org2}

Conforme observamos anteriormente, uma organização peer deve ser um membro de um consórcio do solicitador antes que possa criar ou se associar a um canal. Isso é porque os canais são, em um nível técnico, **caminhos de sistema de mensagens** entre peers por meio do solicitador. Assim como um peer pode ser associado a vários canais sem informações passando de um canal para outro, um solicitador também pode ter vários canais em execução por meio dele sem expor dados às organizações em outros canais.

Como somente administradores de solicitadores podem incluir organizações peer no consórcio, você precisará **ser** o administrador do solicitador ou **enviar** informações de MSP para o administrador do solicitador. Neste tutorial, é possível **incluir a organização de peer no solicitador em seu console**, se você estiver hospedando o solicitador. Ou você precisa **exportar suas informações de organização** e fornecê-las ao administrador do solicitador, que criou a rede na qual o solicitador reside. O administrador do solicitador pode, então, importar sua organização e incluí-la no consórcio.

### Incluir a organização do peer no solicitador em meu console
{: #ibp-console-join-network-add-org2-local}

**Siga estas etapas somente se o seu console já inclui o solicitador e o canal aos quais você deseja se associar.** Caso contrário, continue com [Exportar suas informações da organização](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-add-org2-remote).  

Como somente os administradores do solicitador podem incluir organizações de peer no consórcio, será necessário ser o administrador do solicitador, o que significa que você tem a identidade de administrador da organização do orderer em sua carteira eletrônica do console.  

1. Navegue para a guia **Nós**.
2. Role para baixo até o solicitador que você deseja usar e clique para abri-lo.
3. Em **Membros do consórcio**, clique em **Incluir organização**.
4. Na lista suspensa, selecione `Org2 MSP`, pois esse é o MSP que representa a organização do peer `org2`.
5. Clique em **Enviar**.

Agora inclua a organização de peer no canal.
1. Navegue para a guia **Canais **, clique em `channel1`.
2. Clique no ícone **Configurações** para atualizar o canal e incluir a organização de peer no canal.
2. Role para baixo até a seção intitulada `Add organizations to the channel`, abra a lista suspensa `Select a channel member` e selecione o MSP da organização peer, `Org2 MSP` para o tutorial.
5. Clique em **Incluir** e, em seguida, designe permissões a essa organização. Recomendamos que você os torne um `Operator` para que eles possam atualizar o canal.
6. Clique em **Atualizar canal**.

Quando esse processo estiver concluído, será possível que o `org2` crie ou se associe a um canal hospedado em seu `Orderer`. É possível continuar com a [Etapa três: associar seu peer ao canal](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

### Exportar as informações de sua organização
{: #ibp-console-join-network-add-org2-remote}

**Siga estas etapas somente se o solicitador e o canal ao qual seu peer se associará residirem em outra instância de serviço do {{site.data.keyword.blockchainfull_notm}} Platform.** Caso contrário, será possível ir para a [Etapa três: associar seu peer ao canal](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network-join-peer-org2).

É necessário enviar sua definição de MSP da organização para o administrador do solicitador e ser incluído no consórcio usando as etapas abaixo.  

Ao seguir estas etapas, é necessário ser o administrador da **organização de peer**, o que significa que você tem a identidade de administrador da organização de peer em sua carteira eletrônica do console:  
1. Navegue para a guia **Organizações**. É possível ver as organizações disponíveis para exportação serem listadas em **Organizações disponíveis**. Clique no botão **download** dentro do tile da organização para fazer download do arquivo de configuração JSON que representa o MSP da organização de peer.
2. Envie esse arquivo para o administrador do solicitador em uma operação fora da banda.

### Importar a definição de organização
{: #ibp-console-join-network-import-remote-msp}
O administrador do solicitador precisa importar esse arquivo de JSON para incluir sua organização em seu console:
1. Navegue para a guia **Organizações**, clique no botão **Importar definição de MSP** e selecione o arquivo JSON que representa a definição de MSP da organização de peer.  
2. Navegue para a página **Nós** e clique em seu nó do solicitador. No painel do nó de pedido, em **Membros do consórcio**, clique em **Incluir organização**. No painel lateral que é aberto, selecione `Org2 MSP` na lista de definições de MSP de sua guia **Organizações**.
3. Navegue para a guia **Canais**, clique em `channel1` e, em seguida, clique no ícone **Configurações** para atualizar o canal e incluir a organização de peer no canal.
4. Role para baixo até a seção intitulada `Add organizations to the channel`, abra a lista suspensa `Select a channel member` e selecione o MSP da organização peer, `Org2 MSP` para o tutorial.
5. Clique em **Incluir** e, em seguida, designe permissões a essa organização. Recomendamos que você os torne um `Operator` para que eles possam atualizar o canal.
6. Clique em **Atualizar canal**.

Depois que o administrador do solicitador se associou à sua organização de peer no consórcio e no canal, é necessário importar o nó de pedido para seu console. É possível, então, associar canais que são hospedados pelo serviço de pedido. Conclua as etapas a seguir para **importar** o solicitador:

O administrador da **organização de solicitador** precisa concluir estas etapas primeiro:
1. Navegue para o nó do solicitador dentro da guia **Nós** e clique no ícone **Configurações** à direita do nome do nó para abrir o painel lateral. Clique no botão **Exportar** em **Ações** para fazer download de um arquivo de configuração JSON.
2. Envie esse arquivo para a organização de peer em uma operação fora da banda. O administrador da organização de peer pode então usar o arquivo de configuração para incluir o solicitador no console.

### Importar o orderer de outro cluster
{: #ibp-console-join-network-import-remote-orderer}

Será possível seguir estas etapas se você for o administrador da **organização de peer**:
1. Navegue para a página **Nós** e clique em **Incluir solicitador**.
2. Clique no {{site.data.keyword.cloud_notm}} em **Importar um solicitador existente**.
3. Em seguida, clique no botão **Fazer upload de JSON** e selecione o JSON que representa o próprio nó.
4. Na etapa subsequente, quando for solicitado para associar uma identidade, selecione a identidade da organização de Peer. Para o tutorial, isso seria `Org2 Admin` e clicar em **Enviar**.


## Etapa três: associar seu peer ao canal
{: #ibp-console-join-network-join-peer-org2}

Estamos quase acabando. Seu peer pode agora ser associado a um canal existente. É necessário obter o `channel name`, fora da banda, por meio do operador de rede que criou o canal. No tutorial **Construir uma rede**, criamos um canal denominado `channel1`. Se você ainda não estiver lá, navegue para a guia **Canais** na navegação esquerda.

Execute as etapas a seguir em seu console:

1. Clique no botão **Associar-se ao canal** para ativar os painéis laterais.
2. Selecione seu solicitador denominado 'Solicitador' e clique em **Avançar**.
3. Insira o nome do canal ao qual você deseja se associar, `channel1` e clique em **Avançar**.
4. Selecione quais peers você deseja associar ao canal. Para propósitos deste tutorial, clique em `Peer Org2`.
5. Clique em **Enviar**.

Se você planeja alavancar os recursos [Dados privados ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/private-data/private-data.html "Dados privados") ou [Descoberta de serviço ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/discovery-overview.html "Descoberta de serviço") do Hyperledger Fabric, deve-se configurar os peers de âncora em suas organizações na guia **Canais**. Veja este tópico em [como configurar peers de âncora para dados privados](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) usando a guia **Canais** no console.

Também é possível criar um novo canal quando sua organização é membro do consórcio. Use as etapas para [criar um canal](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel) no [tutorial Construir uma rede](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

## Próximas etapas
{: #ibp-console-join-network-next-steps}

Depois de ter associado seu peer a um canal, use as etapas a seguir para implementar um contrato inteligente e iniciar o envio de transações para o blockchain:

- [Implemente um contrato inteligente em sua rede](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) usando o console.
- Depois de ter instalado e instanciado seu contrato inteligente, será possível [enviar transações usando o aplicativo cliente](docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK).
- Use [a amostra de papel comercial](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app-commercial-paper) para implementar um contrato inteligente de exemplo e enviar transações do código do aplicativo de amostra.

## Detecção de problemas
{: #ibp-console-join-network-troubleshooting}

**Problema:** ocorrem erros quando eu tento operar um nó.    

**Solução:** se você receber erros ao operar um nó no console, [verifique os logs do nó](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) em busca de erros.  

**Problema:** o painel do Kubernetes mostra que o peer falha ao iniciar e os logs do peer incluem `2019-02-06 19:43:24.159 UTC [main] InitCmd -> ERRO 001 Cannot run peer because cannot init crypto, folder “/certs/msp” does not exist`

**Solução:** assegure-se de que ao criar a definição de MSP da organização de peer, o ID de inscrição e o segredo especificados correspondam a uma identidade do tipo `client`, e não `peer`.
