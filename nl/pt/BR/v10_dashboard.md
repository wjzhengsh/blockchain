---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Opere a rede do Enterprise Plan
{: #v10_dashboard}

O {{site.data.keyword.blockchainfull}} Platform traz um Monitor de rede para fornecer uma visão geral de seu ambiente de blockchain, incluindo recursos de rede, membros, canais associados, dados de desempenho da transação e chaincode implementado. O Monitor de rede também oferece a você o ponto de entrada para executar APIs do Swagger, desenvolver uma rede com o {{site.data.keyword.blockchainfull_notm}} Platform: Develop e experimentar os aplicativos de amostras.
{:shortdesc}

É possível [mudar o nome de sua rede do Enterprise Plan](#ep-network-name) no Monitor de rede.

O Monitor de rede expõe as telas a seguir em três seções. É possível navegar para cada tela do navegador esquerdo no Monitor de rede.
- A seção **Minha rede** contém as telas "[Visão geral](#overview)", "[Membros](#members)", "[Canais](#channels)", "[Notificações](#notifications)" e "[APIs](#apis)".
- A seção **Meu código** contém as telas "[Desenvolver código](#write_code)", "[Instalar código](#chaincode)" e "[Tentar amostras](#samples)".
- A tela "[Obter ajuda](#support)" mostra informações de suporte, assim como as notas sobre a liberação para helios e Hyperledger Fabric (a base de código na qual o {{site.data.keyword.blockchainfull_notm}} Platform é baseado).

É possível [verificar e configurar preferências de rede](#network-preferences) no menu suspenso no canto superior direito do Monitor de rede.

Este tutorial descreve cada uma das telas e funções acima.

## Atualizar o nome da rede
{: #ep-network-name}

Quando você criar uma rede do Enterprise Plan, o {{site.data.keyword.blockchainfull_notm}} Platform designará um nome para a sua rede. No entanto, é possível atualizar esse nome da rede a qualquer momento em seu Monitor de rede.

Na parte superior do navegador esquerdo no Monitor de rede, clique no nome da rede e o campo se tornará editável. Digite o novo nome da rede que você deseja usar e pressione a tecla **Enter**. Seu nome da rede será atualizado em alguns segundos.

A **Figura 1** mostra as etapas para atualizar o nome da rede Starter Plan do nome designado para "Starter Plan Network".

![Atualizar o nome da rede](images/update_network_name_ep.gif "Atualizar o nome da rede")
*Figura 1. Atualize o nome de rede*


## Visão geral
{: #overview}

A tela "Visão geral" exibe informações de status em tempo real sobre os seus recursos de blockchain, incluindo o solicitante, a autoridade de certificação e nós de peer. Cada recurso é exibido sob quatro cabeçalhos distintos: **Tipo**, **Nome**, **Status** e **Ações**. Durante a criação de sua rede de blockchain, três nós de solicitante e dois nós de CA são criados automaticamente. As autoridades de certificação são específicas de membro, enquanto que os nós de solicitação são terminais comuns que são compartilhados ao longo da rede.

**Figura 2** mostra a tela "Visão Geral":

![Tela de visão geral](images/myresources.png "Visão geral da rede")
*Figura 2. Visão Geral da Rede*

### Ações do nó
O cabeçalho **Ações** da tabela fornece botões para iniciar ou parar os seus recursos. Também é possível iniciar ou parar um grupo de nós selecionando vários nós e, em seguida, clicando no botão **Iniciar selecionado** ou **Parar selecionado**. O botão **Iniciar selecionado** ou **Parar selecionado** aparece na parte superior da tabela ao selecionar um ou mais nós.

Observe que as ações Parar e Iniciar não estão disponíveis para um Nó solicitante. Em geral, não há necessidade de parar e iniciar os nós do Peer ou da autoridade de certificação em uma rede. As ações Parar e Iniciar são fornecidas no caso de você precisar reiniciar um peer, por exemplo, para deixá-lo ativo em um estado limpo.

Também é possível verificar os logs de componente clicando em **Visualizar logs** na lista suspensa sob o cabeçalho **Ações**. Os logs expõem as chamadas entre os vários recursos de rede e são úteis para depuração e resolução de problemas. Por exemplo, experimente parar um peer e tentar direcioná-lo com uma transação e você verá erros de conectividade. Ao reiniciar o peer e tentar a transação novamente, você verá uma conexão bem-sucedida. Também será possível deixar um peer inativo por um período de tempo estendido conforme os seus canais continuarem a transacionar. Quando o peer for trazido de volta, você perceberá uma sincronização do livro-razão, pois ele receberá os blocos que foram confirmados quando ele estava inativo. Após o livro-razão estar completamente sincronizado, será possível executar chamadas e consultas normais com relação a ele.

### Perfil de conexão
{: #enterprise-connection-profile}
É possível visualizar o arquivo JSON com informações de rede de nível baixo de cada recurso clicando no botão **Perfil de conexão**. O perfil de conexão contém todas as informações de configuração necessárias para um aplicativo. No entanto, como esse arquivo contém apenas os endereços para os seus componentes específicos e o solicitante, se você precisar direcionar adicionais, precisará obter os seus terminais. O cabeçalho que contém "url" exibe o terminal de API de cada componente. Esses terminais são necessários para atingir componentes de rede específicos de um aplicativo do lado do cliente e suas definições geralmente existirão em um arquivo de configuração modelado pelo JSON que acompanha o aplicativo. Se você estiver customizando um aplicativo que requeira endosso de peers que não façam parte de sua organização, será necessário recuperar os endereços IP desses peers por meio dos operadores relevantes em uma operação fora da banda. Os clientes devem ser capazes de se conectar
a quaisquer peers dos quais eles precisam de uma resposta.

### Incluir peers
{: #peers}
Clique no botão **Incluir peers** para incluir nós de peer em sua rede. No Starter Plan, serão incluídos dois peers automaticamente para você no momento da criação da rede. No Enterprise Plan, é possível incluir nós de peer pela primeira vez quando você cria ou se associa a uma rede ou posteriormente no Monitor de rede. Você poderá estar em diferentes cenários quando precisar de mais peers.  Por exemplo, talvez você deseje múltiplos peers para se associar ao mesmo canal para redundância. Cada peer processa transações do canal e grava em suas respectivas cópias do livro-razão. No caso de um dos peers falhar, outros peers podem continuar a processar transações e solicitações de aplicativos.  É possível balancear simetricamente todas as solicitações de aplicativo ao longo de peers ou é possível direcionar peers diferentes para funções diferentes. Por exemplo, é possível usar um peer para consultar o livro-razão e usar outro peer para processar endossos para atualizações de livro-razão.

  No painel pop-up "Incluir peers", selecione o número de nós de peer que você deseja incluir. <!--Currently only "small" peers are available for purchase, however there will eventually be "medium" and "large" to help accommodate larger workloads and higher transaction throughput.-->

## Membros
{: #members}
A tela "Membros" contém duas guias para exibir informações do membro de rede na guia "Membros" e informações de certificado na guia "Certificados".

### Membros
{: #members_tab}
A **Figura 3** mostra a tela inicial "Membros", que exibe os seus membros de rede na guia "Membros":

![Guia Membros na tela Membros](images/monitor_members.png "Membros de rede")
*Figura 3. Membros da Rede*

Além dos membros que você convida quando cria a rede, é possível convidar outros membros na guia "Membros". Para convidar um membro para sua rede, insira o nome da instituição e o endereço de e-mail do operador e clique em **Incluir membro**. Uma rede pode ter um total de 15 membros (incluindo o inicializador da rede). Para remover um membro de sua rede, clique no símbolo "remover" no final da linha do membro.

### Certificados
**Figura 4** mostra a tela inicial "Membros" que exibe certificados de membros na guia "Certificados":

![Guia Certificados na tela Membros](images/monitor_certificates.png "Certificados")
*Figura 4. Certificados*

Os operadores podem gerenciar os certificados para os membros na mesma instituição na guia "Certificados". Clique em **Incluir certificado** para abrir o painel "Incluir certificado". Dê um nome ao seu certificado, cole seus certificados do lado do cliente no formato PEM no campo "Chave" e clique em **Enviar**. Você precisa reiniciar seus peers antes de os certificados do lado do cliente poderem entrar em vigor.

Para obter mais informações sobre como gerar a sua chave do certificado, veja [Gerando os certificados do lado do cliente](v10_application.html#generating-the-client-side-certificates).

## Canais
{: #channels}

É possível separar a sua rede em canais em que cada canal representa um subconjunto de membros que estão autorizados a ver os dados para o chaincode instanciado nesse canal. Cada rede deve ter pelo menos um canal para que as transações ocorram. Cada canal possui um livro-razão exclusivo e os usuários devem ser autenticados adequadamente para executar operações de leitura/gravação nesse livro-razão. Se você não estiver em um canal, não poderá ver nenhum dado.

A **Figura 5** mostra a tela do painel inicial exibindo uma visão geral de todos os canais em sua rede:

![Canais](images/channels.png "Canais")
*Figura 5. Canais*

Criar um canal resulta na geração de um livro-razão específico do canal. Para obter mais informações, consulte [Criando um canal](howto/create_channel.html).

Também é possível selecionar um canal existente para visualizar detalhes mais precisos sobre o canal, a associação e o chaincode ativo. Para obter mais informações, consulte [Monitorando uma rede](howto/monitor_network.html).

## Notificações
{: #notifications}

É possível manipular solicitações pendentes e visualizar solicitações concluídas na tela "Notificações".

A **Figura 6** mostra a tela "Notificações":

![Notificações](images/notifications.png "Notificações")
*Figura 6. Notificações*

Quando você criar um canal ou for convidado para um novo canal, uma notificação será exibida no Monitor de Rede.

As solicitações são agrupadas nas subguias "Todos", "Pendentes" e "Concluídos". Números após o cabeçalho da subguia indicam o número de solicitações em cada subguia.
   * É possível localizar todas as suas solicitações na subguia "Todos".
   * As solicitações que você não aceitou nem recusou ou mesmo que não visualizou estão na subguia "Pendente". Clique no botão **Revisar solicitação** para visualizar a solicitação, que inclui a política do canal e os membros, além do status da votação. Se você for um operador de canal, será possível **Aceitar** ou **Recusar** a solicitação ou manipulá-la em outro momento clicando em **Depois**. Se a solicitação for aceita por operadores do canal suficientes, será possível clicar em **Enviar solicitação** para ativar a atualização do canal.
   * Uma solicitação enviada aparecerá na subguia "Concluído".  É possível clicar em **Revisar solicitação** para visualizar seus detalhes.

Quando você tem uma longa lista de solicitações, é possível procurar por uma solicitação no campo de procura na parte superior.

Solicitações pendentes podem ser excluídas selecionando as caixas na frente delas e clicando em **Excluir solicitação**. Observe que uma solicitação concluída não pode ser excluída.

## APIs
{: #apis}

Para facilitar o desenvolvimento de aplicativos, o {{site.data.keyword.blockchainfull_notm}} Platform expõe APIs que você pode testar com relação à sua rede em uma UI do Swagger.

**Figura 7** mostra a tela "APIs":

![APIs](images/API_screen.png "APIs")
*Figura 7. APIs*

Clique no link **UI do Swagger** para abrir a UI do Swagger. Observe que você precisa autorizar a UI do Swagger com as suas credenciais de rede (que podem ser localizadas nessa página de APIs) antes que você possa executar as APIs. Para obter mais informações, veja [Interagindo com a rede usando APIs do Swagger](howto/swagger_apis.html).

## Desenvolver Código
{: #write-code}

O Enterprise Plan integra o {{site.data.keyword.blockchainfull_notm}} Platform: Develop e fornece um ambiente de desenvolvimento com as ferramentas e tecnologias padrão do segmento de mercado. É possível desenvolver a sua rede no ambiente on-line ou localmente. Após desenvolver uma rede, é possível implementá-la de volta em sua rede de Enterprise Plan.

A **Figura 8** mostra a tela "Desenvolver código":

![Desenvolver código](images/write_code.png "Desenvolver código")
*Figura 8. Desenvolver código*

Para obter mais informações sobre o desenvolvimento e a implementação de seu código com o Enterprise Plan, veja [Desenvolvendo redes de negócios no Enterprise Plan](develop_enterprise.html).

## O código de instalação
{: #chaincode}

O chaincode, que também é conhecido como "contrato inteligente", são as partes de software que contêm um conjunto de funções para consultar e atualizar o livro-razão. Eles são instalados em peers e instanciados em um canal.

**Figura 9** mostra a tela "Código de instalação":

![Código de instalação](images/chaincode_install_overview.png "Código de instalação")
*Figura 9. Instale o código*

Um chaincode é o primeiro instalado em um sistema de arquivos de um peer e, em seguida, instanciado em um canal. Para obter mais informações, veja [Instalando, instanciando e atualizando um chaincode](howto/install_instantiate_chaincode.html).

## Tentar amostras
{: #samples}

Os aplicativos de amostra ajudam você a entender melhor uma rede de blockchain e desenvolvimento de aplicativo. Siga o link para o repositório Marbles no Monitor de rede para obter informações sobre como instalar o aplicativo de amostra Marbles. Para obter mais informações sobre como desenvolver e implementar as suas próprias amostras, verifique [Desenvolvendo aplicativos](v10_application.html).

A **Figura 10** mostra a tela "Tentar amostras":

![Tentar amostras](images/sample_overview_ep.png "Tentar amostras")
*Figura 10. Amostras*

## Obtenha ajuda
{: #support}

A tela "Obter ajuda" contém duas guias para fornecer informações de suporte na guia "Suporte" e para descrever funções novas e mudadas de cada liberação na guia "Notas sobre a liberação".

**Figura 11** mostra a tela inicial "Suporte" que exibe informações de suporte na guia "Suporte":

![Suporte](images/support.png "Suporte")
*Figura 11. Suporte do blockchain*

Use os links e recursos nesta página para acessar fóruns de resolução de problemas e suporte.

* [Docs de serviço do {{site.data.keyword.blockchainfull_notm}}](index.html) em **Introdução**, que é este site de doc, fornece orientação sobre a introdução ao {{site.data.keyword.blockchainfull}} Platform no {{site.data.keyword.Bluemix_notm}}. É possível localizar tópicos correspondentes no navegador esquerdo ou procurar qualquer termo com a função de procura na parte superior.
* [IBM Developer Works ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://developer.ibm.com/blockchain/) em **Ajuda da Comunidade** contém recursos e informações para desenvolvedores.
* [IBM dWAnswers ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://developer.ibm.com/answers/smartspace/blockchain/) em **Chamado de Suporte** serve como uma plataforma para perguntas e respostas. É possível procurar respostas de perguntas feitas anteriormente ou enviar uma nova pergunta. Certifique-se de incluir a palavra-chave **blockchain** em sua pergunta.
  Também é possível enviar um chamado para a equipe de suporte do {{site.data.keyword.blockchainfull_notm}} com a opção **Abrir um chamado de suporte do {{site.data.keyword.Bluemix_notm}}**.  Compartilhe detalhes e fragmentos de código de sua instância específica do {{site.data.keyword.Bluemix_notm}}.
* [Aplicativos de amostra ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://github.com/ibm-blockchain) sob **Aplicativos de amostra do blockchain** fornecem fragmentos de código de orientação e de amostra para ajudar no desenvolvimento de aplicativos.
* [Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/) e [Comunidade do Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://jira.hyperledger.org/secure/Dashboard.jspa) em **Hyperledger Fabric** fornecem mais detalhes sobre a pilha do Hyperledger Fabric.
  Fale com um [Especialista do Hyperledger ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://chat.hyperledger.org/channel/general) com perguntas sobre o código do Hyperledger Fabric.

Se não for possível depurar o seu problema ou determinar uma resposta para a sua pergunta, envie um caso de suporte no Portal de serviço do IBM Cloud. Para obter mais informações, veja [Obtendo suporte](ibmblockchain_support.html).

A Figura 12 e a Figura 13 mostram a tela inicial "Obter ajuda", que exibe funções novas e mudadas de cada liberação na guia "Notas sobre a liberação":

![Helios Notas sobre a liberação](images/releasenotes_helios.png "Helios Notas sobre a liberação")
*Figuras 12. Notas sobre a liberação para o Helios*

![Notas sobre a liberação do Fabric](images/releasenotes_Fabric.png "Notas sobre a liberação do Fabric")
*Figuras 13. Notas sobre a liberação para o Fabric*


## Preferências de rede
{: #network-preferences}

Clique no canto superior direito e abra o menu suspenso e, em seguida, **Preferências de rede**. A janela Preferências de Rede é aberta. A janela Preferências de rede mostra informações básicas sobre sua rede, como nome da rede, versão do Fabric, local de rede no {{site.data.keyword.cloud_notm}} e tipo de banco de dados do livro-razão.

Redes do Enterprise Plan foram atualizadas para o Fabric v1.1 em<!-- May 15th, 2018 will run on Hyperledger Fabric v1.1-->. Se você criar redes após o upgrade, também poderá gerenciar o tempo limite de inatividade da web e o TLS mútuo para a sua rede na janela Preferências de rede. Essas configurações podem ser mudadas apenas pelo inicializador de rede.

<!--
Enterprise Plan networks that are created after May 15th, 2018 will run on Hyperledger Fabric v1.1. If you create networks after the upgrade, you can also manage web inactivity timeout, mutual TLS, and switch your ledger to CouchDB for your network in the Network preferences window. These settings can be changed by the network initiator only.
-->

### Tempo limite de inatividade da web
{: #web-inactivity-timeout}

**Nota**: somente o **inicializador de rede** pode mudar a configuração de tempo limite de inatividade da web. Esta é uma configuração de nível de rede e afetará todos os membros da rede.

O tempo limite de inatividade da web é configurado para **Desligado** por padrão. Se você transformar o tempo limite de inatividade da web em **Ligado**, qualquer membro da rede terá o logon efetuado automaticamente após 10 minutos de inatividade. Quando o cronômetro de inatividade da web atinge 10 minutos, a função de tempo limite de inatividade da web termina as sessões inativas da web para garantir a segurança da conta do membro da rede. Clicar em um link ou atualizar o Monitor de rede reconfigura o cronômetro de inatividade. Antes de atingir 10 minutos, fechar a guia ou janela do navegador também termina a sessão da web.

### TLS mútuo
{: #mutual-tls}

O TLS mútuo assegura a comunicação entre o seu aplicativo e a sua rede e garante que só você pode se comunicar com a sua rede.

**Nota**: apenas um **inicializador de rede** pode ativar ou desativar o TLS mútuo. Esta é uma configuração de nível de rede e afetará todos os membros da rede.

O botão do TLS mútuo é configurado como **Desativado** por padrão. Se você ativar o TLS mútuo, será necessário atualizar os seus aplicativos para suportarem essa função. Caso contrário, os seus aplicativos não poderão se comunicar com a sua rede.

Para uma rede do plano do Fabric 1.1 Enterprise, cada organização tem a sua própria autoridade de certificação (CA) do TLS mútuo. As informações necessárias para se conectar à autoridade de certificação do TLS mútuo estão disponíveis no [Perfil de conexão](##enterprise-connection-profile) acessível por meio de sua tela **Visão geral** no Monitor de Rede clicando no botão **Perfil de conexão**. O perfil de conexão contém as informações necessárias para se conectar à autoridade de certificação e obter os certificados que você precisa para se conectar à sua rede.

No campo Perfil de conexão, localize a seção `certificateAuthorities` na qual você encontrará os atributos a seguir que são necessários para se inscrever e obter os certificados para se comunicar com a sua rede usando o TLS mútuo.

- `url`: URL para conexão com a autoridade de certificação que pode fornecer certificados do TLS mútuo
- `enrollId`: ID de inscrição para usar para obter um certificado
- `enrollSecret`: segredo de inscrição para usar para obter um certificado
- `x-tlsCAName`: nome da autoridade de certificação para usar para obter um certificado que permitirá que o aplicativo se comunique com TLS mútuo.

Para obter mais informações sobre como atualizar os seus aplicativos para suportar o TLS mútuo, veja [Como configurar o TLS mútuo ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html)

<!--
### CouchDB ledger type
{: #couchdb}
**Note**: Only the **network initiator** can switch the ledger database from LevelDB to CouchDB. This is a network level setting and will affect all network members. Switching to CouchDB is permanent. You cannot revert back to LevelDB.
Before Enterprise Plan upgrades to Fabric v1.1, all network peers store data in the pure key-value LevelDB. With Fabric v1.1, you can choose to use CouchDB as your ledger database. CouchDB is a document datastore that permits indexing the contents of your data and allows you to issue rich queries against the data on your peer. Note that Hyperledger Fabric does not support peers running different databases. If CouchDB is used, it must be used by all of the peers.
To use CouchDB, your data must be stored in a data format that can be modeled in chaincode, such as JSON. If the decision is made to migrate from LevelDB to CouchDB, the {{site.data.keyword.blockchainfull_notm}} Platform will migrate your data from key-value format to the CouchDB format automatically.
If you switch to CouchDB, you need to update your chaincode to take advantage of indexes and rich queries. For more information about CouchDB and how to set up index, see [CouchDB as the State Database ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/latest/couchdb_as_state_database.html). For more information about updating chaincode in {{site.data.keyword.blockchainfull_notm}} Platform, see [Updating a chaincode](howto/install_instantiate_chaincode.html#updating-a-chaincode).
-->

A **Figura 14** mostra a janela "Preferências de rede":

<! -- ![Network preferences](images/network_preferences_ep.gif "Network preferences") -- > ![Network preferences](images/network_preferences_ep_tmp.png "Network preferences")  
*Figura 14. Preferências de Rede*
