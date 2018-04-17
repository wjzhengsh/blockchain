---

copyright:
  years: 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Governar a rede do Starter Plan
{: #getting-started-with-starter-plan}

O {{site.data.keyword.blockchainfull}} Platform Starter Plan oferece uma rede de blockchain pré-configurada com um único clique. Ele <!--offers you a free trial of 30 days and -->provisiona uma rede com permissão com a configuração de duas [organizações](glossary.html#organization), um [peer](glossary.html#peer) para cada organização e um [canal](glossary.html#channel) por padrão. Após a rede ser criada, será possível escalar e incluir mais organizações e peers em sua rede. <!--Note that it might cause extra cost if you exceed the default resource limits of two organizations and two peers.-->
{:shortdesc}

**Notas**:
1. O Starter Plan do {{site.data.keyword.blockchainfull}} Platform é um ambiente de desenvolvimento e teste. Se você precisar de um ambiente de produção, veja [Sobre o Enterprise Plan](enterprise_plan.html).
2. O Starter Plan está em fase beta agora e está disponível apenas na região **Sul dos EUA** no {{site.data.keyword.cloud_notm}}.

O Starter Plan permite aprender e desenvolver habilidades com o {{site.data.keyword.blockchainfull_notm}} Platform, executar aplicativos de amostra, testar os seus próprios aplicativos e simular um cenário de múltiplas organizações. Este tutorial de introdução apresenta os pré-requisitos e as etapas que você precisa seguir para criar e usar uma rede do Starter Plan.

Se você é novo no {{site.data.keyword.blockchainfull_notm}} Platform e o blockchain, leia o [Glossário](glossary.html) para familiarizar-se com os termos nesta documentação e a [Documentação do Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/en/latest/blockchain.html) para saber mais sobre o blockchain.


## Criando uma rede
Você pode ter uma [rede](glossary.html#network) do Starter Plan com a configuração padrão imediatamente após criar uma instância de serviço do {{site.data.keyword.blockchainfull_notm}} Platform.

1. Localize o serviço do Blockchain do [ ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/blockchain) no Catálogo do {{site.data.keyword.cloud_notm}}. É necessário efetuar login com a sua conta do {{site.data.keyword.cloud_notm}}. Se você não tiver uma conta, clique no botão **Inscreva-se para criar**. Assegure-se de escolher **Sul dos EUA** como a região no {{site.data.keyword.cloud_notm}}.
2. Selecione a sua organização e o espaço do Cloud Foundry, no local em que você criará a sua rede.
3. Escolha **Plano de associação do iniciador** na tabela de planos de precificação.
4. Clique no botão **Criar**. Observe que se você for convidado a se associar a uma rede, poderá ver um painel pop-up de boas-vindas. Para criar uma rede, escolha **Continuar até a sua rede** e clique em **Continuar**. Para se associar a uma rede, veja a etapa 5 em [Se associando a uma rede](#joining-a-network).
  Agora você está pronto para usar a rede do Starter Plan com a configuração padrão. A rede está em execução com um solicitante (conhecido como serviço de solicitação "SOLO"), duas organizações, uma autoridade de certificação e um peer para cada organização. Um canal padrão também é criado.
5. Clique no botão **Ativar**.

É possível localizar a sua instância de serviço de blockchain em seu painel de serviço do [{{site.data.keyword.Bluemix_notm}} painel de serviço do ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} ").


## Convidando membros
É possível convidar outras [organizações](glossary.html#organization) para se associarem à sua rede do Starter Plan como [membros](glossary.html#member) para que seja possível executarem [transações](glossary.html#transaction) entre si. Além disso, se você desejar usar o Starter Plan para aprendizado e fins de teste, será possível simular uma rede de múltiplas organizações, incluindo membros para você na rede.

1. Na tela "Membros" de seu Monitor de rede, clique no botão **Convidar membros**.
2. A janela "Convidar membro" é aberta.
    - Se você desejar convidar outra organização, escolha "Convidar um membro". Especifique o nome e o endereço de e-mail do operador da organização que você deseja convidar.  Também é possível inserir informações adicionais que você deseja incluir em seu convite no campo "Incluir uma nota". Clique no botão **Enviar convite**. A organização convidada receberá um e-mail de convite e poderá, então, seguir as instruções no e-mail para se associar à sua rede.
    - Se você deseja incluir as organizações adicionais que podem ser incluídas em um canal, escolha "Incluir um membro". Especifique um nome para a sua nova organização. É possível opcionalmente incluir peers em sua nova organização ou fazer isso posteriormente no Monitor de rede. Clique no botão **Criar**. Observe que se você incluir peers para a sua nova organização, você precisará alternar para essa nova organização para ver os seus peers. Para obter mais informações sobre como alternar organizações, veja [Alternar organizações](dashboard.html#switch-organizations).


## Participando de uma rede
Se você for convidado por um inicializador de rede, receberá uma notificação por e-mail com instruções sobre como se associar à rede. Siga as instruções no e-mail e você se tornará um dos membros na rede.

É necessário criar uma instância de serviço do [{{site.data.keyword.blockchain}} Platform ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/blockchain) com a opção de associação do Starter Plan no {{site.data.keyword.cloud_notm}}.

1. Efetue login com a sua conta do {{site.data.keyword.cloud_notm}}. Se você não tiver uma conta, clique no botão **Inscreva-se para criar**.
2. Selecione a organização do Cloud Foundry na qual você deseja armazenar a sua rede do {{site.data.keyword.blockchain}}.
3. Selecione **Plano de associação do iniciador** na tabela de planos de precificação.
4. Clique no botão **Criar**. A página da instância de serviço é aberta com um painel pop-up de boas-vindas. Observe que você pode escolher se associar a uma rede ou continuar a criar a sua própria. Para criar uma rede, veja a etapa 4 em [Criando uma rede](#creating-a-network).
5. No painel de boas-vindas, escolha **Associe-se à rede existente**, selecione a rede para se associar na lista suspensa e clique em **Continuar**.

É possível localizar a sua instância de serviço de blockchain no painel de serviço do [{{site.data.keyword.cloud_notm}} painel de serviço do ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} ").

<!--
## Creating channels
You can create a [channel](glossary.html#channel) in your network and invite other organizations to your channel.  For more information on creating channels, see [Creating a channel](howto/create_channel.html#creating-a-channel).
-->
<!--
## Installing and instantiating your chaincode
You can run [chaincode](glossary.html#chaincode) on your peers in the network.  For more information about deploying pre-built samples, see [Installing and instantiating a chaincode](howto/install_instantiate_chaincode.html).
-->


## Recuperando credenciais de rede e perfil de conexão
Após você criar uma rede do Starter Plan no {{site.data.keyword.cloud_notm}}, será possível recuperar as credenciais de rede e o perfil de conexão da página da instância de serviço ou no Monitor de rede.

### Recuperando por meio da página da instância de serviço
Você estará na página da instância de serviço logo após criar uma instância de serviço. Também é possível clicar em seu serviço no [painel do serviço do {{site.data.keyword.cloud_notm}}![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.cloud_notm}} painel do serviço") para abrir a página da instância de serviço.

Recupere as suas credenciais de serviço com as etapas a seguir:
1. Na página da instância de serviço, clique em **Credenciais de serviço** no navegador esquerdo para mostrar a tela "Credenciais de serviço".
2. Clique em **Nova credencial** na tela "Credenciais de serviço".
3. Na tela "Incluir nova credencial", dê à credencial um nome e clique em **Incluir**. A nova credencial é incluída na tabela. É possível clicar em **Visualizar credenciais** sob a coluna "AÇÕES" para visualizar os detalhes da credencial. Essa credencial contém a chave API e o segredo, que você pode usar para autorizar APIs.
    Se você desejar ver o perfil de conexão de sua rede, insira **{"legacy": true}** como parâmetro de configuração sequencial ao criar novas credenciais. O perfil de conexão contém terminais de API para os seus recursos de rede, que podem ser usados em suas APIs e seus aplicativos.

### Recuperando no Monitor de rede
É possível localizar as credenciais de rede na tela "APIs" em seu Monitor de rede. Para obter mais informações sobre como usar as APIs, veja [Experimentando as APIs com o Swagger](apis.html).

É possível recuperar o perfil de conexão na tela "Visão geral" em seu Monitor de rede. Clique no botão **Perfil de conexão** na tela "Visão geral" e o perfil de conexão será mostrado em uma nova página.


## Implementando aplicativos de amostra
O Starter Plan permite implementar aplicativos de amostra em sua rede com apenas alguns cliques. Com as amostras, é possível facilmente aprender como os chaincodes e aplicativos trabalham em uma rede.

Para obter mais informações, veja [Implementando aplicativos de amostra](howto/prebuilt_samples.html).


## Desenvolvendo e Implementando redes de negócios customizadas
O Starter Plan integra o ambiente de desenvolvedor do IBM Blockchain Platform: Develop e o conjunto de ferramentas de desenvolvedor do Hyperledger Composer. É possível desenvolver a sua rede de blockchain com base em suas necessidades de negócios. Após você desenvolver uma rede para os seus negócios, será possível implementar a sua rede de negócios na rede do Starter Plan.

Para obter mais informações, veja [Desenvolver a rede](develop.html) e [Implementando redes de negócios com o Starter Plan](develop_starter.html).


## Monitorando recursos de rede
Se o seu aplicativo solicita uma transação, será possível visualizar informações de status da transação no Monitor de rede. Para obter mais informações sobre o monitoramento de rede, consulte [Monitorando uma rede](howto/monitor_network.html).


## Reconfigurando uma rede
Se você desejar limpar as suas configurações customizadas, os chaincodes em execução ou os aplicativos implementados, será possível configurar a sua rede de volta para a configuração padrão inicial. Para obter mais informações, veja [Reconfigurar rede](dashboard.html#reset-network).


## Migrando do Starter Plan para o Enterprise Plan
{: #migrate}

É possível implementar um `.bna`, chaincode e aplicativos que você testa com relação a uma rede do Starter Plan em uma rede do Enterprise Plan.

Se você tiver o seu archive de rede de negócios (`.bna`), siga as instruções para [implementar uma rede de negócios para Enterprise Plan](./develop_enterprise.html). Se você não tiver o seu arquivo `.bna`, use o comando `composer network download` para recuperá-lo da instância do Starter Plan. Para obter mais informações sobre o comando `composer network download`, veja [documentação da linha de comandos do Hyperledger Composer![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger.github.io/composer/reference/commands){:new_window}.

Chaincodes, que são semelhantes aos arquivos `.bna`, são desenvolvidos externamente. Para implementar um chaincode que você testa em uma rede do Starter Plan no Enterprise, siga as instruções em [Instalando e instanciando um chaincode](howto/install_instantiate_chaincode.html#installchaincode).

Como você pode ver em [Implementando aplicativos de amostra](howto/prebuilt_samples.html), o Starter Plan facilita obter um aplicativo de amostra integrado com sua rede usando a cadeia de ferramentas. Esta configuração também permite a integração contínua atualizando automaticamente o seu aplicativo de amostra quando o seu repositório de aplicativo bifurcado muda. Se você desejar implementar esse aplicativo em uma rede do Enterprise Plan, poderá copiar o seu repositório de aplicativo bifurcado em um novo repositório e, em seguida, seguir as instruções em [Implementando aplicativos de amostra manualmente](howto/prebuilt_samples.html#deploy_sample_applications_manually).


## Excluindo ou saindo de uma rede
Se você desejar excluir ou deixar uma rede, poderá excluir a instância de serviço de blockchain do painel do {{site.data.keyword.cloud_notm}}.

**Nota**: antes de sair de uma rede, assegure-se de não ser membro de nenhum canal da rede. Caso contrário, você receberá mensagens de erro quando sair da rede. Uma remoção de membros do canal é necessária para concluir o processo de atualização do canal. Para obter mais informações sobre o processo de atualização do canal, consulte [Atualizando um canal](howto/create_channel.html#updating-a-channel).


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.1, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.
-->
