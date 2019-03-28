---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Introdução ao Starter Plan
{: #getting-started-with-starter-plan}

O {{site.data.keyword.blockchainfull}} Platform Starter Plan oferece uma rede de blockchain pré-configurada com um único clique. Ele provisiona uma rede permissionizada com a configuração de duas [organizações](/docs/services/blockchain/glossary.html#glossary-organization), um [peer](/docs/services/blockchain/glossary.html#glossary-peer) para cada organização e um [canal](/docs/services/blockchain/glossary.html#glossary-channel) por padrão. Depois que a rede é criada, é possível escalar e incluir mais organizações e peers em sua rede. Essas redes são destinadas a usuários que são novos no {{site.data.keyword.blockchainfull_notm}} Platform. <!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

O Starter Plan permite aprender e desenvolver habilidades com o {{site.data.keyword.blockchainfull_notm}} Platform, executar aplicativos de amostra, testar os seus próprios aplicativos e simular um cenário de múltiplas organizações. Esse tutorial de introdução orientará você sobre como usar o Starter Plan para começar a desenvolver e a transacionar em uma rede de blockchain.

Se você for novo no {{site.data.keyword.blockchainfull_notm}} Platform e no blockchain, será possível aprender mais sobre blockchain revisando a [visão geral de componentes fundamentais](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview) de redes construídas sobre o [Hyperledger Fabric](/docs/services/blockchain/reference/v10_fabric.html#hyperledger-fabric) de software livre. Também é possível revisar a documentação [Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/en/release-1.2/blockchain.html "Introdução ao Blockchain").

**Nota**: o {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan é um ambiente de desenvolvimento e teste e não é adequado para cargas de trabalho de produção. Se você precisar de um ambiente de produção, veja [Sobre o Enterprise Plan](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

## Visão geral
{: #getting-started-with-starter-plan-overview}

**Construa seu consórcio**

A primeira etapa para construir com blockchain é formar o consórcio de organizações que desejam usá-lo para transacionar e convidar essas organizações para sua rede. Os usuários do Starter Plan que estão experimentando a tecnologia podem simular um consórcio de várias organizações, criando novas próprias organizações.

- [Crie uma rede do Starter Plan](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-creating-a-network)
- [Convide organizações para sua rede](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-inviting-members)
- [Associando-se a uma rede do Starter Plan](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-joining-a-network)

Dentro do consórcio de organizações que se associaram às suas redes, é possível transacionar de forma privada com conjuntos dessas organizações, criando canais.

- [Crie canais](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-create-channels)

**Desenvolva sua rede e aplicativos**

Depois de formar seu consórcio, é necessário escrever o chaincode, também conhecido como contratos inteligentes, que conterá a lógica de negócios de sua rede e permitirá que você interaja com dados no livro-razão de blockchain. Em seguida, será necessário usar os Fabric SDKs juntamente com esses contratos inteligentes para enviar transações para sua rede a partir do aplicativo do lado do cliente.

- [Desenvolvendo chaincode](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-develop-chaincode)
- [Desenvolvendo aplicativos com os Fabric SDKs](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-develop-apps)

O {{site.data.keyword.blockchainfull_notm}} Platform fornece aplicativos de amostra que podem ser implementados em sua rede do Starter Plan com somente alguns cliques.

- [Implementando aplicativos de amostra](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-sample-applications)

**Opere e governe sua rede**

O {{site.data.keyword.blockchainfull_notm}} Platform fornece conjunto de ferramentas e APIs que podem ser usados para gerenciar a associação, o ciclo de vida e o funcionamento de sua rede de blockchain. Muitos desses recursos podem ser acessados por meio da interface com o usuário do Monitor de Rede.

- [Monitorando recursos de rede](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-monitoring-resources)
- [Recuperando credenciais de rede e perfil de conexão](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-retrieving-network-credentials)
- [Gerenciando sua rede usando APIs do Swagger](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-swagger)
- [Reconfigurando uma rede](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-resetting-network)
- [Migrando do Starter Plan para o Enterprise Plan](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-migrate)
- [Excluindo ou saindo de uma rede](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-delete-network)


## Criando uma rede
{: #getting-started-with-starter-plan-creating-a-network}

Você pode ter uma [rede](/docs/services/blockchain/glossary.html#glossary-network) do Starter Plan com a configuração padrão imediatamente após criar uma instância de serviço do {{site.data.keyword.blockchainfull_notm}} Platform.

1. Localize o [Serviço de blockchain ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) no catálogo do {{site.data.keyword.cloud_notm}}.
    **Nota**: é necessário efetuar login com a sua conta paga do {{site.data.keyword.cloud_notm}}. Se você não tiver uma conta, clique no botão **Inscreva-se para criar**. Após você criar uma conta de avaliação grátis, faça upgrade dela para um tipo **Pré-pago** acessando **Gerenciar** > **Faturamento e uso** > **Faturamento** no console do {{site.data.keyword.cloud_notm}} e clicando em **Incluir cartão de crédito**.
2. Escolha a região em {{site.data.keyword.cloud_notm}} para criar a rede.
3. Selecione a sua organização e o espaço do Cloud Foundry para criar a rede.
4. Escolha **Plano de associação do iniciador** na tabela de planos de precificação.
5. Clique no botão **Criar**. Observe que se você for convidado a se associar a uma rede, poderá ver um painel pop-up de boas-vindas. Para criar uma rede, escolha **Continuar até a sua rede** e clique em **Continuar**. Para se associar a uma rede, veja a etapa 5 em [Se associando a uma rede](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-joining-a-network).
  Agora você está pronto para usar a rede do Starter Plan com a configuração padrão. A rede está em execução com um solicitante (conhecido como serviço de ordenação "SOLO"), duas organizações, uma autoridade de certificação e um peer para cada organização. Um canal padrão também é criado.
6. Clique no botão **Ativar**.

É possível localizar a sua instância de serviço de blockchain no painel de serviço do [{{site.data.keyword.cloud_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/resources "{{site.data.keyword.cloud_notm}} painel de serviço do ").


## Convidando membros
{: #getting-started-with-starter-plan-inviting-members}

É possível convidar outras [organizações](/docs/services/blockchain/glossary.html#glossary-organization) para se associarem à sua rede do Starter Plan como [membros](/docs/services/blockchain/glossary.html#glossary-member) para que seja possível executarem [transações](/docs/services/blockchain/glossary.html#glossary-transaction) entre si. Além disso, se você desejar usar o Starter Plan para aprendizado e fins de teste, será possível simular uma rede de múltiplas organizações, incluindo membros para você na rede.

1. Na tela "Membros" de seu Monitor de rede, clique no botão **Convidar membros**.
2. A janela "Convidar membro" é aberta.
    - Se você desejar convidar outra organização, escolha "Convidar um membro".  Especifique o nome e o endereço de e-mail do operador da organização que você deseja convidar.  Também é possível inserir informações adicionais que você deseja incluir em seu convite no campo "Incluir uma nota".  Clique no botão **Enviar convite**.  A organização convidada receberá um e-mail de convite e poderá, então, seguir as instruções no e-mail para se associar à sua rede.
    - Se você deseja incluir as organizações adicionais que podem ser incluídas em um canal, escolha "Incluir um membro".  Especifique um nome para a sua nova organização. É possível opcionalmente incluir peers em sua nova organização ou fazer isso posteriormente no Monitor de rede.  Clique no botão **Criar**. Observe que se você incluir peers para a sua nova organização, você precisará alternar para essa nova organização para ver os seus peers. É possível alternar para outra organização clicando no canto superior direito e selecionar a organização de destino na lista suspensa sob a seção **ALTERNAR ORGANIZAÇÃO**.


## Participando de uma rede
{: #getting-started-with-starter-plan-joining-a-network}

Se você for convidado por um inicializador de rede, receberá uma notificação por e-mail com instruções sobre como se associar à rede. Siga as instruções no e-mail e você se tornará um dos membros na rede.

É necessário criar uma instância de serviço do [{{site.data.keyword.blockchainfull_notm}} Platform ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) com a opção de associação do Starter Plan no {{site.data.keyword.cloud_notm}}.

1. Efetue login com a sua conta do {{site.data.keyword.cloud_notm}}. Se você não tiver uma conta, clique no botão **Inscreva-se para criar**.
2. Selecione a organização do Cloud Foundry na qual você deseja armazenar a sua rede do {{site.data.keyword.blockchain}}.
3. Selecione **Plano de associação do iniciador** na tabela de planos de precificação.
4. Clique no botão **Criar**. A página da instância de serviço é aberta com um painel pop-up de boas-vindas. Observe que você pode escolher se associar a uma rede ou continuar a criar a sua própria. Para criar uma rede, veja a etapa 4 em [Criando uma rede](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan-creating-a-network).
5. No painel de boas-vindas, escolha **Associe-se à rede existente**, selecione a rede para se associar na lista suspensa e clique em **Continuar**.

É possível localizar a sua instância de serviço de blockchain no painel de serviço do [{{site.data.keyword.cloud_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/resources "{{site.data.keyword.cloud_notm}} painel de serviço do ").


## Crie canais
{: #getting-started-with-starter-plan-create-channels}

Os canais são usados por conjuntos de organizações para transacionar sem expor os dados para outras organizações. É possível criar um [canal](/docs/services/blockchain/glossary.html#glossary-channel) com os membros selecionados de sua rede Starter Plan e políticas sobre quem pode atualizar o canal.

Para obter mais informações, consulte [Criando um canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel). Se outra organização o convidar para um canal, você também encontrará instruções sobre como aceitar o convite e associar-se aos seus peers no canal.

## Desenvolvendo chaincode
{: #getting-started-with-starter-plan-develop-chaincode}

[chaincode](/docs/services/blockchain/glossary.html#glossary-chaincode), às vezes referido como contratos inteligentes, é o software que permite que você leia e atualize dados no livro-razão do blockchain. O chaincode pode transformar a lógica de negócios em um programa executável acordado e verificado por todos os membros da rede de blockchain.

Para obter informações adicionais, consulte o tutorial [desenvolvendo chaincode](/docs/services/blockchain/howto/develop_chaincode.html#develop-smart-contracts) para saber como iniciar a gravação do chaincode, bem como os recursos de malha que são acessíveis por meio do chaincode.

## Instalando e instanciando seu chaincode
{: #getting-started-with-starter-plan-install-instantiate-chaincode}
Depois de associar os canais e desenvolver sua lógica de negócios, é necessário instalar o chaincode nos peers na rede. É possível usar o monitor de rede para instalar e instanciar o chaincode nos peers de sua organização, bem como atualizar o chaincode para facilitar o desenvolvimento contínuo.

Para obter mais informações sobre a implementação de amostras pré-construídas, consulte [Instalando, instanciando e atualizando um chaincode](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode).


## Desenvolvendo aplicativos usando os Fabric SDKs
{: #getting-started-with-starter-plan-develop-apps}

O {{site.data.keyword.blockchainfull_notm}} Platform fornece terminais de API aos quais você pode se conectar usando os Fabric SDKs. É possível usar os SDKs para chamar o chaincode a partir de seus aplicativos e enviar transações para sua rede de blockchain.

Para obter mais informações, consulte o tutorial [desenvolvendo aplicativos](/docs/services/blockchain/v10_application.html#dev-app).


## Implementando aplicativos de amostra
{: #getting-started-with-starter-plan-sample-applications}

O Starter Plan permite implementar aplicativos de amostra em sua rede com apenas alguns cliques. É possível usar essas amostras para saber mais sobre o chaincode, bem como usá-las como pontos de início para seu próprio desenvolvimento.

Para obter mais informações, veja [Implementando aplicativos de amostra](/docs/services/blockchain/howto/prebuilt_samples.html#deploying-sample-applications).


## Monitorando recursos de rede
{: #getting-started-with-starter-plan-monitoring-resources}

Se o seu aplicativo solicita uma transação, será possível visualizar informações de status da transação no Monitor de rede. Para obter mais informações sobre o monitoramento de rede, consulte [Monitorando uma rede](/docs/services/blockchain/howto/monitor_network.html#monitor-blockchain-network).


## Recuperando credenciais de rede e perfil de conexão
{: #getting-started-with-starter-plan-retrieving-network-credentials}

Após você criar uma rede do Starter Plan no {{site.data.keyword.cloud_notm}}, será possível recuperar as credenciais de rede e o perfil de conexão da página da instância de serviço ou no Monitor de rede.

### Recuperando por meio da página da instância de serviço
{: #getting-started-with-starter-plan-retrieve-service-instance}

Você estará na página da instância de serviço logo após criar uma instância de serviço. Também é possível clicar no serviço no painel de serviço do [{{site.data.keyword.cloud_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/resources "{{site.data.keyword.cloud_notm}} painel de serviço do ") para abrir a página da sua instância de serviço.

Recupere as suas credenciais de serviço com as etapas a seguir:
1. Na página da instância de serviço, clique em **Credenciais de serviço** no navegador esquerdo para mostrar a tela "Credenciais de serviço".
2. Clique em **Nova credencial** na tela "Credenciais de serviço".
3. Na tela "Incluir nova credencial", dê à credencial um nome e insira **{"type": "service_instance_token"}** no campo "Incluir parâmetro de configuração sequencial". Clique em **Incluir**. A nova credencial é incluída na tabela. É possível clicar em **Visualizar credenciais** sob a coluna "AÇÕES" para visualizar os detalhes da credencial. Essa credencial contém a chave API e o segredo, que você pode usar para autorizar APIs.

![Retrieving network credentials](images/service_credentials.gif "Retrieving network credentials")

### Recuperando no Monitor de rede
{: #getting-started-with-starter-plan-network-creds}

É possível localizar as credenciais de rede na tela "APIs" em seu Monitor de rede. Para obter mais informações sobre como usar as APIs, veja [Interagindo com a rede usando APIs do Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger).

É possível recuperar o perfil de conexão na tela "Visão geral" em seu Monitor de rede. Clique no botão **Perfil de conexão** na tela "Visão geral" e o perfil de conexão será mostrado em uma nova página.

## Gerenciando sua rede usando APIs do Swagger
{: #getting-started-with-starter-plan-swagger}

O {{site.data.keyword.blockchainfull_notm}} Platform expõe uma série de APIs de REST no Swagger que podem ser usadas para gerenciar os nós, os canais, os peers e os membros de sua rede. Os seus aplicativos podem usar essas APIs para controlar recursos de rede importantes sem usar o Monitor de rede.

Para saber mais, veja [Interagindo com a rede usando as APIs do Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger)

## Reconfigurando uma rede
{: #getting-started-with-starter-plan-reset-nw}

Se você desejar limpar as suas configurações customizadas, executando o chaincode ou os aplicativos implementados, poderá configurar a sua rede de volta para a configuração padrão inicial. Para obter mais informações, consulte [Reconfigurar rede](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-reset-network).


## Migrando do Starter Plan para o Enterprise Plan
{: #getting-started-with-starter-plan-migrate}

É possível implementar o chaincode e os aplicativos que você testa com relação a uma rede do Starter Plan em uma rede do Enterprise Plan. Para implementar um chaincode que você testa em uma rede do Starter Plan no Enterprise, siga as instruções em [Instalando, instanciando e atualizando um chaincode](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-install-cc).

Somente o chaincode e os aplicativos podem ser migrados, os dados não podem ser migrados entre as redes do Starter e do Enterprise Plan.

<!--
As you can see in [Deploying sample applications](/docs/services/blockchain/howto/prebuilt_samples.html#deploying-sample-applications), Starter Plan makes it easy to get a sample application integrated with your network by using Toolchain. This setup also allows for continuous integration by automatically updating your sample application whenever your forked application repo is changed. If you want to deploy this application into an Enterprise Plan network, you can copy your forked application repo into a new repo and then follow the instructions in [Deploying sample applications manually](/docs/services/blockchain/howto/prebuilt_samples.html#deploying-sample-applications#deploy_sample_applications_manually).
-->

Se você implementar qualquer aplicativo de amostra na rede do Starter Plan e desejar implementar esse aplicativo em uma rede do Enterprise Plan, será possível copiar o repositório do aplicativo bifurcado para um novo repositório e depois seguir as instruções nos aplicativos de amostra para implementá-las na rede do Enterprise Plan.


## Excluindo ou saindo de uma rede
{: #getting-started-with-starter-plan-delete-network}

Se você desejar excluir ou deixar uma rede, poderá excluir a instância de serviço de blockchain do painel do {{site.data.keyword.cloud_notm}}.

Antes de sair de uma rede, assegure-se de que você não seja um membro de nenhum canal na rede. Caso contrário, você receberá mensagens de erro quando sair da rede. Uma remoção de membros do canal é necessária para concluir o processo de atualização do canal. Para obter mais informações sobre o processo de atualização do canal, consulte [Atualizando um canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel).{:note}
