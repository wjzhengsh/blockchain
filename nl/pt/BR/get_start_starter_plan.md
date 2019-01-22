---

copyright:
  years: 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Introdução ao Starter Plan
{: #getting-started-with-starter-plan}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


O {{site.data.keyword.blockchainfull}} Platform Starter Plan oferece uma rede de blockchain pré-configurada com um único clique. Ele provisiona uma rede permissionizada com a configuração de duas [organizações](glossary.html#organization), um [peer](glossary.html#peer) para cada organização e um [canal](glossary.html#channel) por padrão. Depois que a rede é criada, é possível escalar e incluir mais organizações e peers em sua rede. Essas redes são destinadas a usuários que são novos no {{site.data.keyword.blockchainfull_notm}} Platform. <!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

O Starter Plan permite aprender e desenvolver habilidades com o {{site.data.keyword.blockchainfull_notm}} Platform, executar aplicativos de amostra, testar os seus próprios aplicativos e simular um cenário de múltiplas organizações. Esse tutorial de introdução orientará você sobre como usar o Starter Plan para começar a desenvolver e a transacionar em uma rede de blockchain.

Se você for novo no {{site.data.keyword.blockchainfull_notm}} Platform e no blockchain, será possível aprender mais sobre blockchain revisando a [visão geral de componentes fundamentais](blockchain_component_overview.html) de redes construídas sobre o [Hyperledger Fabric](reference/v10_fabric.html) de software livre. Também é possível revisar a documentação [Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/en/release-1.2/blockchain.html "Introdução ao Blockchain").

**Nota**: o {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan é um ambiente de desenvolvimento e teste e não é adequado para cargas de trabalho de produção. Se você precisar de um ambiente de produção, veja [Sobre o Enterprise Plan](enterprise_plan.html).

## Visão geral

**Construa seu consórcio**

A primeira etapa para construir com blockchain é formar o consórcio de organizações que desejam usá-lo para transacionar e convidar essas organizações para sua rede. Os usuários do Starter Plan que estão experimentando a tecnologia podem simular um consórcio de várias organizações, criando novas próprias organizações.

- [Crie uma rede do Starter Plan](#creating-a-network)
- [Convide organizações para sua rede](#inviting-members)
- [Associando-se a uma rede do Starter Plan](#joining-a-network)

Dentro do consórcio de organizações que se associaram às suas redes, é possível transacionar de forma privada com conjuntos dessas organizações, criando canais.

- [Crie canais](#create-channels)

**Desenvolva sua rede e aplicativos**

Depois de formar seu consórcio, é necessário escrever o chaincode, também conhecido como contratos inteligentes, que conterá a lógica de negócios de sua rede e permitirá que você interaja com dados no livro-razão de blockchain. Em seguida, será necessário usar os Fabric SDKs juntamente com esses contratos inteligentes para enviar transações para sua rede a partir do aplicativo do lado do cliente.

- [Desenvolvendo chaincode](#develop-chaincode)
- [Desenvolvendo aplicativos com os Fabric SDKs](#develop-apps)

Também é possível usar o Hyperledger Composer, um conjunto de ferramentas de software livre para o Hyperledger Fabric, para desenvolver a lógica de negócios, implementar a lógica em sua rede e conectá-la a partir de seus aplicativos.

- [Desenvolvendo e implementando redes de negócios customizadas com o Hyperledger Composer](#develop-composer)

O {{site.data.keyword.blockchainfull_notm}} Platform fornece aplicativos de amostra construídos usando o Hyperledger Composer e amostras construídas com o chaincode que você pode implementar em sua rede do Starter Plan com apenas alguns cliques.

- [Implementando aplicativos de amostra](#sample-applications)

**Opere e governe sua rede**

O {{site.data.keyword.blockchainfull_notm}} Platform fornece conjunto de ferramentas e APIs que podem ser usados para gerenciar a associação, o ciclo de vida e o funcionamento de sua rede de blockchain. Muitos desses recursos podem ser acessados por meio da interface com o usuário do Monitor de Rede.

- [Monitorando recursos de rede](#monitoring-resources)
- [Recuperando credenciais de rede e perfil de conexão](#retrieving-network-credentials)
- [Gerenciando sua rede usando APIs do Swagger](#swagger)
- [Reconfigurando uma rede](#resetting-network)
- [Migrando do Starter Plan para o Enterprise Plan](#migrate)
- [Excluindo ou saindo de uma rede](#delete-network)


## Criando uma rede
{: #creating-a-network}

Você pode ter uma [rede](glossary.html#network) do Starter Plan com a configuração padrão imediatamente após criar uma instância de serviço do {{site.data.keyword.blockchainfull_notm}} Platform.

1. Localize o serviço do Blockchain do [ ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/blockchain) no Catálogo do {{site.data.keyword.cloud_notm}}.
    **Nota**: é necessário efetuar login com a sua conta paga do {{site.data.keyword.cloud_notm}}. Se você não tiver uma conta, clique no botão **Inscreva-se para criar**. Após você criar uma conta de avaliação grátis, faça upgrade dela para um tipo **Pré-pago** acessando **Gerenciar** > **Faturamento e uso** > **Faturamento** no console do {{site.data.keyword.cloud_notm}} e clicando em **Incluir cartão de crédito**.
2. Escolha a região em {{site.data.keyword.cloud_notm}} para criar a rede.
3. Selecione a sua organização e o espaço do Cloud Foundry para criar a rede.
4. Escolha **Plano de associação do iniciador** na tabela de planos de precificação.
5. Clique no botão **Criar**. Observe que se você for convidado a se associar a uma rede, poderá ver um painel pop-up de boas-vindas. Para criar uma rede, escolha **Continuar até a sua rede** e clique em **Continuar**. Para se associar a uma rede, veja a etapa 5 em [Se associando a uma rede](#joining-a-network).
  Agora você está pronto para usar a rede do Starter Plan com a configuração padrão. A rede está em execução com um solicitante (conhecido como serviço de ordenação "SOLO"), duas organizações, uma autoridade de certificação e um peer para cada organização. Um canal padrão também é criado.
6. Clique no botão **Ativar**.

É possível localizar a sua instância de serviço de blockchain em seu painel de serviço do [{{site.data.keyword.Bluemix_notm}} painel de serviço do ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} ").


## Convidando membros
{: #inviting-members}

É possível convidar outras [organizações](glossary.html#organization) para se associarem à sua rede do Starter Plan como [membros](glossary.html#member) para que seja possível executarem [transações](glossary.html#transaction) entre si. Além disso, se você desejar usar o Starter Plan para aprendizado e fins de teste, será possível simular uma rede de múltiplas organizações, incluindo membros para você na rede.

1. Na tela "Membros" de seu Monitor de rede, clique no botão **Convidar membros**.
2. A janela "Convidar membro" é aberta.
    - Se você desejar convidar outra organização, escolha "Convidar um membro".  Especifique o nome e o endereço de e-mail do operador da organização que você deseja convidar.  Também é possível inserir informações adicionais que você deseja incluir em seu convite no campo "Incluir uma nota".  Clique no botão **Enviar convite**.  A organização convidada receberá um e-mail de convite e poderá, então, seguir as instruções no e-mail para se associar à sua rede.
    - Se você deseja incluir as organizações adicionais que podem ser incluídas em um canal, escolha "Incluir um membro".  Especifique um nome para a sua nova organização. É possível opcionalmente incluir peers em sua nova organização ou fazer isso posteriormente no Monitor de rede.  Clique no botão **Criar**. Observe que se você incluir peers para a sua nova organização, você precisará alternar para essa nova organização para ver os seus peers.<!-- Para obter informações adicionais sobre a comutação de organizações, consulte [Alternar organizações](v10_dashboard.html#switch-organizations)-->


## Participando de uma rede
{: #joining-a-network}

Se você for convidado por um inicializador de rede, receberá uma notificação por e-mail com instruções sobre como se associar à rede. Siga as instruções no e-mail e você se tornará um dos membros na rede.

É necessário criar uma instância de serviço do [{{site.data.keyword.blockchain}} Platform ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/blockchain) com a opção de associação do Starter Plan no {{site.data.keyword.cloud_notm}}.

1. Efetue login com a sua conta do {{site.data.keyword.cloud_notm}}. Se você não tiver uma conta, clique no botão **Inscreva-se para criar**.
2. Selecione a organização do Cloud Foundry na qual você deseja armazenar a sua rede do {{site.data.keyword.blockchain}}.
3. Selecione **Plano de associação do iniciador** na tabela de planos de precificação.
4. Clique no botão **Criar**. A página da instância de serviço é aberta com um painel pop-up de boas-vindas. Observe que você pode escolher se associar a uma rede ou continuar a criar a sua própria. Para criar uma rede, veja a etapa 4 em [Criando uma rede](#creating-a-network).
5. No painel de boas-vindas, escolha **Associe-se à rede existente**, selecione a rede para se associar na lista suspensa e clique em **Continuar**.

É possível localizar a sua instância de serviço de blockchain no painel de serviço do [{{site.data.keyword.cloud_notm}} painel de serviço do ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} ").


## Crie canais
{: #create-channels}

Os canais são usados por conjuntos de organizações para transacionar sem expor os dados para outras organizações. É possível criar um [canal](glossary.html#channel) com os membros selecionados de sua rede Starter Plan e políticas sobre quem pode atualizar o canal.

Para obter mais informações, consulte [Criando um canal](howto/create_channel.html#creating-a-channel). Se outra organização o convidar para um canal, você também encontrará instruções sobre como aceitar o convite e associar-se aos seus peers no canal.

## Desenvolvendo chaincode
{: #develop-chaincode}

[chaincode](glossary.html#chaincode), às vezes referido como contratos inteligentes, é o software que permite que você leia e atualize dados no livro-razão do blockchain. O chaincode pode transformar a lógica de negócios em um programa executável acordado e verificado por todos os membros da rede de blockchain.

Para obter informações adicionais, consulte o tutorial [desenvolvendo chaincode](howto/develop_chaincode.html) para saber como iniciar a gravação do chaincode, bem como os recursos de malha que são acessíveis por meio do chaincode.

## Instalando e instanciando seu chaincode
{: #install-instantiate-chaincode}
Depois de associar os canais e desenvolver sua lógica de negócios, é necessário instalar o chaincode nos peers na rede. É possível usar o monitor de rede para instalar e instanciar o chaincode nos peers de sua organização, bem como atualizar o chaincode para facilitar o desenvolvimento contínuo.

Para obter mais informações sobre a implementação de amostras pré-construídas, consulte [Instalando, instanciando e atualizando um chaincode](howto/install_instantiate_chaincode.html).


## Desenvolvendo aplicativos usando os Fabric SDKs
{: #develop-apps}

O {{site.data.keyword.blockchainfull_notm}} Platform fornece terminais de API aos quais você pode se conectar usando os Fabric SDKs. É possível usar os SDKs para chamar o chaincode a partir de seus aplicativos e enviar transações para sua rede de blockchain.

Para obter mais informações, consulte o tutorial [desenvolvendo aplicativos](v10_application.html).


## Desenvolvendo e implementando redes de negócios customizadas com o Hyperledger Composer
{: #develop-composer}

O Starter Plan integra o {{site.data.keyword.blockchainfull_notm}} Platform: desenvolver o ambiente de desenvolvedor e o conjunto de ferramentas do desenvolvedor do Hyperledger Composer. É possível desenvolver a sua rede de blockchain com base em suas necessidades de negócios.  Após você desenvolver uma rede para os seus negócios, será possível implementar a sua rede de negócios na rede do Starter Plan.

Para obter mais informações, veja [Desenvolver a rede](develop.html) e [Implementando redes de negócios com o Starter Plan](develop_starter.html).


## Implementando aplicativos de amostra
{: #sample-applications}

O Starter Plan permite implementar aplicativos de amostra em sua rede com apenas alguns cliques. É possível usar essas amostras para saber mais sobre o chaincode e as redes de negócios do Hyperledger Composer, bem como usá-las como pontos de início para seu próprio desenvolvimento.

Para obter mais informações, veja [Implementando aplicativos de amostra](howto/prebuilt_samples.html).


## Monitorando recursos de rede
{: #monitoring-resources}

Se o seu aplicativo solicita uma transação, será possível visualizar informações de status da transação no Monitor de rede. Para obter mais informações sobre o monitoramento de rede, consulte [Monitorando uma rede](howto/monitor_network.html).


## Recuperando credenciais de rede e perfil de conexão
{: #retrieving-network-credentials}

Após você criar uma rede do Starter Plan no {{site.data.keyword.cloud_notm}}, será possível recuperar as credenciais de rede e o perfil de conexão da página da instância de serviço ou no Monitor de rede.

### Recuperando por meio da página da instância de serviço
Você estará na página da instância de serviço logo após criar uma instância de serviço. Também é possível clicar em seu serviço no [painel do serviço do {{site.data.keyword.cloud_notm}}![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} painel do serviço") para abrir a página da instância de serviço.

Recupere as suas credenciais de serviço com as etapas a seguir:
1. Na página da instância de serviço, clique em **Credenciais de serviço** no navegador esquerdo para mostrar a tela "Credenciais de serviço".
2. Clique em **Nova credencial** na tela "Credenciais de serviço".
3. Na tela "Incluir nova credencial", dê à credencial um nome e insira **{"type": "service_instance_token"}** no campo "Incluir parâmetro de configuração sequencial". Clique em **Incluir**. A nova credencial é incluída na tabela. É possível clicar em **Visualizar credenciais** sob a coluna "AÇÕES" para visualizar os detalhes da credencial. Essa credencial contém a chave API e o segredo, que você pode usar para autorizar APIs.  

![Retrieving network credentials](images/service_credentials.gif "Retrieving network credentials")

### Recuperando no Monitor de rede
É possível localizar as credenciais de rede na tela "APIs" em seu Monitor de rede. Para obter mais informações sobre como usar as APIs, veja [Interagindo com a rede usando APIs do Swagger](howto/swagger_apis.html).

É possível recuperar o perfil de conexão na tela "Visão geral" em seu Monitor de rede. Clique no botão **Perfil de conexão** na tela "Visão geral" e o perfil de conexão será mostrado em uma nova página.

## Gerenciando sua rede usando APIs do Swagger
{: #swagger}

O {{site.data.keyword.blockchainfull_notm}} Platform expõe uma série de APIs de REST no Swagger que podem ser usadas para gerenciar os nós, os canais, os peers e os membros de sua rede. Os seus aplicativos podem usar essas APIs para controlar recursos de rede importantes sem usar o Monitor de rede.

Para saber mais, veja [Interagindo com a rede usando as APIs do Swagger](howto/swagger_apis.html)


## Reconfigurando uma rede
{: #resetting-network}

Se você desejar limpar as suas configurações customizadas, executando o chaincode ou os aplicativos implementados, poderá configurar a sua rede de volta para a configuração padrão inicial. Para obter mais informações, consulte [Reconfigurar rede](v10_dashboard.html#reset-network).


## Migrando do Starter Plan para o Enterprise Plan
{: #migrate}

É possível implementar um `.bna`, chaincode e aplicativos que você testa com relação a uma rede do Starter Plan em uma rede do Enterprise Plan.

Se você tiver o seu archive de rede de negócios (`.bna`), siga as instruções para [implementar uma rede de negócios para Enterprise Plan](./develop_enterprise.html). Se você não tiver o seu arquivo `.bna`, use o comando `composer network download` para recuperá-lo da instância do Starter Plan. Para obter mais informações sobre o comando `composer network download`, veja [documentação da linha de comandos do Hyperledger Composer![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger.github.io/composer/latest/reference/commands){:new_window}.

O chaincode, que é semelhante a arquivos `.bna`, é desenvolvido externamente. Para implementar um chaincode que você testa em uma rede do Starter Plan no Enterprise, siga as instruções em [Instalando, instanciando e atualizando um chaincode](howto/install_instantiate_chaincode.html#installchaincode).

<!--
As you can see in [Deploying sample applications](howto/prebuilt_samples.html), Starter Plan makes it easy to get a sample application integrated with your network by using Toolchain. This setup also allows for continuous integration by automatically updating your sample application whenever your forked application repo is changed. If you want to deploy this application into an Enterprise Plan network, you can copy your forked application repo into a new repo and then follow the instructions in [Deploying sample applications manually](howto/prebuilt_samples.html#deploy_sample_applications_manually).
-->

Se você implementar qualquer aplicativo de amostra na rede do Starter Plan e desejar implementar esse aplicativo em uma rede do Enterprise Plan, será possível copiar o repositório do aplicativo bifurcado para um novo repositório e depois seguir as instruções nos aplicativos de amostra para implementá-las na rede do Enterprise Plan.


## Excluindo ou saindo de uma rede
{: #delete-network}

Se você desejar excluir ou deixar uma rede, poderá excluir a instância de serviço de blockchain do painel do {{site.data.keyword.cloud_notm}}.

**Nota**: antes de sair de uma rede, assegure-se de que você não seja um membro de nenhum canal na rede. Caso contrário, você receberá mensagens de erro quando sair da rede. Uma remoção de membros do canal é necessária para concluir o processo de atualização do canal. Para obter mais informações sobre o processo de atualização do canal, consulte [Atualizando um canal](howto/create_channel.html#updating-a-channel).


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.2, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/release-1.2/){:new_window}.
-->
