---

copyright:
  years: 2017
lastupdated: "2017-12-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Controle a rede
{: #getting-started-with-blockchain}

O {{site.data.keyword.blockchainfull}} Platform on {{site.data.keyword.Bluemix_notm}} fornece uma rede {{site.data.keyword.blockchain}} com alta segurança, integridade, escalabilidade e desempenho. É possível provisionar rapidamente uma rede de {{site.data.keyword.blockchain}} totalmente funcional e usar o Monitor de Rede para executar imediatamente o chaincode e os aplicativos sem a necessidade de projetar e configurar uma rede de blockchain privada. 
{:shortdesc}
 
Este tutorial de introdução apresenta os pré-requisitos e as etapas que você precisa seguir para obter uma rede de {{site.data.keyword.blockchain}} hospedada no ambiente altamente disponível e seguro da IBM.  

As etapas a seguir indicam o fluxo básico para lançar uma rede de {{site.data.keyword.blockchain}} com várias organizações de membro:
1. Um **inicializador de rede**, como um tipo especial de membro de rede, cria a rede de {{site.data.keyword.blockchain}} e define políticas de controle. O inicializador de rede pode, então, convidar outras instituições para participar dessa rede de {{site.data.keyword.blockchain}} como membros de rede.  Para obter mais detalhes, consulte [Criando uma rede](#creating-a-network).
2. Os **membros de rede** recebem uma notificação por e-mail que fornece instruções para que eles se associem a uma rede do {{site.data.keyword.blockchain}}. Além das instruções na notificação por e-mail, também é possível seguir as etapas em [Participando de uma rede](#joining-a-network).
3. Todos os **membros de rede**, após criar ou participar de uma rede, podem entrar no Monitor de Rede, que é um painel da GUI, para configurar e gerenciar seus recursos de rede. É possível configurar canais com um grupo de membros de rede para executar transações particulares em um livro razão do canal específico, que somente membros do canal podem acessar. No Monitor de Rede, também é possível juntar seus próprios peers no canal e instalar e instanciar chaincode neles. Para obter mais detalhes, consulte [Configurando recursos de rede e ambiente](#configuring-network-resources-and-environment).
4. **Desenvolvedores de aplicativos** criam aplicativos que permitem a interação com a rede do {{site.data.keyword.blockchain}}. Para obter mais informações, consulte [Ativando aplicativos para interagir com a rede](#enabling-applications-to-interact-with-the-network).
5. **Operadores de rede** monitoram transações em seus canais usando o Monitor de Rede. Para obter mais detalhes, consulte [Monitorando recursos de rede](#monitoring-network-resources).

## Criando uma rede
Antes de iniciar, é necessário criar uma instância de serviço do [{{site.data.keyword.blockchain}} Plataform ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/blockchain) no {{site.data.keyword.Bluemix_notm}}. Será necessário efetuar login com seu {{site.data.keyword.Bluemix_notm}} ID. Se você não tiver um ID, clique no botão **Inscreva-se para** criar.  Renomeie o serviço e os nomes de credenciais para sua instância para que você possa reconhecê-lo facilmente no futuro. Selecione a região, a organização e o espaço do {{site.data.keyword.Bluemix_notm}}, na qual é possível implementar sua rede do {{site.data.keyword.blockchain}}. Em seguida, selecione **Plano corporativo** na tabela de planos de precificação e clique no botão **Criar**.  

É possível localizar a {{site.data.keyword.blockchain}} instância de serviço do Platform em seu [{{site.data.keyword.Bluemix_notm}} painel de serviços ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}}").   

Se você for um inicializador de rede, clique no botão **Criar rede** para iniciar uma rede do {{site.data.keyword.blockchain}}  Siga o assistente para concluir a configuração básica de sua rede e seus recursos.  
![Assistente de Criação de Rede](images/create_network_name.png "Assistente de Criação de Rede")  

1. Na tela "Vamos começar", forneça um nome para sua rede, escolha o local de sua organização do {{site.data.keyword.Bluemix_notm}} e inclua o nome de sua instituição. Quando você convidar outros membros de rede, eles procurarão por este nome de rede para participar. Clique em **Avançar**.
2. (Opcional) Na tela "Convidar Membros", insira o nome da instituição e o endereço de e-mail do membro que você deseja convidar para sua rede. O nome da instituição que você designa não é um título oficial. Ele simplesmente permite que a instituição seja reconhecida facilmente e pode ser mudado quando eles se juntam à rede. Observe que uma rede pode ter até 15 membros, incluindo você. Esta etapa é opcional e você pode convidar membros para sua rede posteriormente no Monitor de Rede.  Clique em **Avançar**.	Os membros que você convidar receberão uma notificação por e-mail sobre seu convite após você concluir todas as etapas para criar a rede.
3. Na tela "Definir Regras de Controle", estabeleça as políticas para associação, criação de canal e chaincode. Por padrão, todos os membros de rede podem convidar outros membros para participarem da rede, criar canais e instanciar chaincode. Para este GA, sua rede usa as políticas de controle padrão.  Clique em **Avançar**.
4. Na tela "Resumo da Revisão", verifique sua configuração de rede. Se você desejar fazer modificações, clique em **Editar** ao lado do cabeçalho da seção ou clique no botão **Anterior** para voltar para as telas anteriores. Ao concluir a configuração de rede, clique em **Pronto**.  
5. Na tela "Rede Criada", você será notificado de que sua rede foi criada com êxito. É possível clicar em **Incluir peers** para configurar seus recursos de rede ou clicar em **Inserir monitor** diretamente para abrir o Monitor de Rede. Se você não incluir os peers primeiro, eles poderão ser incluídos posteriormente no Monitor de Rede.  Para obter mais informações sobre peers, consulte [Visão Geral](v10_dashboard.html#overview).
    
Agora você está pronto para implementar uma rede do {{site.data.keyword.blockchain}} que possa suportar os recursos de rede a seguir:  
* Uma autoridade de certificação (CA) específica do membro
* Políticas de controle padrão
* Até 15 membros de rede  
* Três solicitadores e dois nós de CA intermediários
* Até três pequenos peers para cada membro  
* Um serviço de ordenação tolerante a falhas e travamento
* Até 150 canais
* Até 10 instanciações de chaincodes


## Participando de uma rede
Semelhante à criação de uma rede, é necessário criar uma instância de serviço do [{{site.data.keyword.blockchain}} Plataform ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/blockchain) no {{site.data.keyword.Bluemix_notm}}. Será necessário efetuar login com seu ID do {{site.data.keyword.Bluemix_notm}} Se você não tiver um ID, clique no botão **Inscreva-se para ** criar. Renomeie o serviço e os nomes de credenciais para sua instância para que você possa reconhecê-lo facilmente no futuro. Selecione a região, a organização e o espaço do {{site.data.keyword.Bluemix_notm}}, na qual é possível implementar sua rede do {{site.data.keyword.blockchain}}. Em seguida, selecione **Plano corporativo** na tabela de planos de precificação e clique no botão **Criar**.   

Será possível localizar sua instância de serviço do {{site.data.keyword.blockchain}} Platform no painel de serviços do [{{site.data.keyword.Bluemix_notm}} painel de serviços ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/dashboard/services "{{site.data.keyword.Bluemix_notm}} painel de serviços"). 

Se você for um membro de rede convidado, clique no botão **Convite pendente ->** selecione a rede da qual deseja participar na lista suspensa e clique no botão **Participar da rede!**. Siga o assistente para visualizar a configuração básica de sua rede e configurar seus próprios componentes de rede.  
![Assistente de Participação da Rede](images/join_network_name.png "Assistente de Participação da Rede")  

1. Na tela "Vamos começar", inclua o nome de sua instituição e verifique o local de sua organização do {{site.data.keyword.Bluemix_notm}}. Clique em **Avançar**.
2. Na tela "Revisar Regras de Controle", visualize as políticas de controle de associação, de criação de canal e de chaincode da rede. Clique em **Avançar**.
3. (Opcional) Na tela "Incluir Peers", escolha o tamanho e a quantidade de peers que você deseja incluir. Clique em **Avançar**. Cada membro em uma rede pode incluir até 3 peers; neste momento somente "pequenos" peers estão disponíveis. Esta etapa é opcional e você pode incluir seus peers posteriormente no Monitor de Rede. Para obter mais informações sobre peers, consulte [Visão Geral](v10_dashboard.html#overview).
4. Na tela "Revisar Resumo de Rede", verifique a configuração de rede. Se você desejar fazer modificações, clique em **Editar** ao lado do cabeçalho da seção ou clique no botão **Anterior** para voltar para as telas anteriores. Ao concluir a configuração dos recursos, clique em **Pronto**. Você será notificado de que se associou à rede com êxito. Em seguida, é possível clicar em **Entrar no monitor** para abrir o Monitor de Rede ou clicar em **Criar um canal** para iniciar uma solicitação de criação de canal. É possível criar canais posteriormente no Monitor de Rede. Para obter mais informações, consulte [Canais](v10_dashboard.html#channels).
 

## Configurando recursos de rede e ambiente

1. Entre em seu Monitor de Rede após criar ou participar de uma rede do {{site.data.keyword.blockchain}}. O Monitor de Rede é um painel da GUI no qual você pode gerenciar e controlar informações de status da rede. Para obter mais informações, consulte [Monitor de Rede](v10_dashboard.html).
2. Inclua seus próprios peers na rede. Se você já incluiu peers suficientes, ignore esta etapa. Os peers executam chaincode e eles são o terminal para interagir com seus aplicativos. Clique em **Incluir peers** na tela "Visão Geral" e selecione a quantidade e o tamanho de seus peers. Para obter mais informações, consulte [Visão Geral](v10_dashboard.html#resources).
3. Configure um canal. Todos os membros no mesmo canal recebem um livro razão específico do canal, o que fornece isolamento e confidencialidade dos dados. Para obter informações sobre como criar um canal, consulte [Criando um canal](howto/create_channel.html#creating-a-channel).  
	
	Se você for um membro do canal que foi convidado a participar de um canal, receberá uma notificação por e-mail com um link para o assistente que permitirá que você participe do canal. 
4. Associe peers ao canal.  Apenas peers que estão associados ao canal podem acessar seu livro razão. Para obter mais informações, consulte [Canais](v10_dashboard.html#channels).
5. Instale e instancie o chaincode. Todos os membros do canal precisam instalar o mesmo chaincode com o mesmo nome e versão em cada peer que executará o chaincode. Após ser instalado, o chaincode deve ser instanciado no canal antes que possa ser usado. Para obter mais informações, consulte [Instalando e instanciando um chaincode](howto/install_instantiate_chaincode.html).  

**Nota**: para obter alta disponibilidade, cada instituição deve comprar pelo menos 2 peers e, dentro de um canal, cada instituição participante deve se associar a pelo menos 2 peers.

## Ativando aplicativos para interagirem com a rede
Os aplicativos alavancam as APIs do SDK para interagirem com seus componentes de rede do {{site.data.keyword.blockchain}}. É necessário incluir as informações do terminal de API de seus componentes de rede em seu aplicativo para que ele possa finalmente alcançar seus peers com solicitações de transação. É possível, então, incluir as informações do terminal de API no Monitor de Rede. Os aplicativos podem ser hospedados em seu sistema de arquivos local ou em {{site.data.keyword.Bluemix_notm}}. Para obter mais informações, consulte [Desenvolvendo aplicativos](v10_application.html).

## Monitorando recursos de rede  
Após uma transação ter sido acionada no seu aplicativo, é possível visualizar informações de status da transação no Monitor de Rede. Para obter mais informações sobre o monitoramento de rede, consulte [Monitorando uma rede](howto/monitor_network.html).
  
## Sair de uma rede 
Se você desejar sair de uma rede, exclua a instância de serviço de blockchain do painel do {{site.data.keyword.Bluemix_notm}}.  

**Nota**: antes de sair de uma rede, assegure-se de não ser membro de nenhum canal da rede. Caso contrário, você receberá mensagens de erro quando sair da rede. Uma remoção de membros do canal é necessária para concluir o processo de atualização do canal. Para obter mais informações sobre o processo de atualização do canal, consulte [Atualizando um canal](howto/create_channel.html#updating-a-channel).


<!--
## References
* For more information about {{site.data.keyword.blockchainfull_notm}} offerings, see [Blockchain offerings](index.html).
* For more information about Hyperledger Fabric V1.0, see [Hyperledger Fabric documentation ![External link icon](images/external_link.svg "External link icon")](http://hyperledger-fabric.readthedocs.io/en/latest/){:new_window}.
-->
