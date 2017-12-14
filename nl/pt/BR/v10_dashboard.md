---

copyright:
  years: 2017
lastupdated: "2017-12-05"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Opere a rede
{: #v10_dashboard}


O Monitor de Rede fornece uma visão geral de seu ambiente de blockchain, incluindo componentes de rede, membros, canais associados, dados de desempenho e chaincodes implementados.
{:shortdesc}

O Monitor de Rede expõe as telas a seguir:
* Na tela "Visão Geral", é possível visualizar as credenciais de serviço de rede, as informações de status do componente e incluir peers.
* Na tela "Membros", é possível gerenciar membros de rede e certificados.
* Na tela "Canais", é possível criar novos canais e visualizar informações sobre canais existentes.
* Na tela "Chaincode", é possível instalar e instanciar chaincodes em seus peers.
* Na tela "Notificações", é possível manipular aprovações pendentes e visualizar aprovações concluídas.
* Na tela "Suporte", é possível localizar links para recursos de referência e ver funções novas e mudadas em cada liberação.


## Visão Geral

A tela "Visão Geral" exibe informações de status em tempo real sobre seus componentes de blockchain, incluindo o solicitante, a CA e os nós peer. Cada componente é exibido sob quatro cabeçalhos distintos: **Tipo**, **Nome**, **Status** e **Ações**. Durante a criação de sua rede de blockchain, três nós de solicitante e dois nós de CA são criados automaticamente.  Os de CA são específicos do membro, enquanto que os nós de solicitação são terminais comuns compartilhados na rede.

A **Figura 1** mostra a tela "Visão Geral":

![Tela de visão geral](images/myresources.png "Visão geral da rede")
*Figura 1. Visão Geral da Rede*

- Ações do nó

  O cabeçalho **Ações** da tabela fornece botões para iniciar ou parar seus componentes. Também é possível iniciar ou parar um grupo de nós selecionando vários nós e, em seguida, clicando no botão **Iniciar selecionado** ou **Parar selecionado**. O botão **Iniciar selecionado** ou **Parar selecionado** aparece na parte superior da tabela ao selecionar um ou mais nós.

  Você também é possível verificar os logs do componente clicando em **Visualizar logs** na lista suspensa sob o cabeçalho **Ações**. Os logs expõem as chamadas de procedimento remoto que ocorrem entre os vários componentes de rede e são úteis para depuração e resolução de problemas. Por exemplo, experimente parar um peer e tentar atingi-lo com uma transação; você verá erros de conectividade gRPC. Ao reiniciar o peer e tentar a transação novamente, você verá uma conexão bem-sucedida. Também é possível deixar um peer inativo por um longo período de tempo, pois seus canais continuam a transacionar. Quando o
peer for trazido de volta, você perceberá uma sincronização do livro razão por meio do protocolo gossip. Assim que o peer tiver sincronizado totalmente o livro razão, será possível executar chamadas e consultas normais.  
- Credenciais de Serviço  
  É possível visualizar o arquivo JSON com informações de rede de nível baixo de cada componente clicando no botão **Credenciais de serviço** na parte superior direita da guia "Recursos". Essas são todas as informações de configuração necessárias para um aplicativo. Observe, no entanto, que esse arquivo contém apenas os endereços para seus componentes específicos e os nós de solicitação compartilhados. Se for necessário atingir peers adicionais, você precisará obter seus terminais.
  O cabeçalho contendo "url" exibe o terminal de API de cada componente. Esses terminais são necessários para atingir componentes de rede específicos de um aplicativo do lado do cliente e suas definições geralmente existirão em um arquivo de configuração modelado pelo JSON que acompanha o aplicativo. Se você estiver customizando um aplicativo que requer endosso de peers que não fazem parte de sua organização, será necessário recuperar os endereços IP dos peers dos operadores relevantes em uma operação fora da banda. Os clientes devem ser capazes de se conectar
a quaisquer peers dos quais eles precisam de uma resposta.  
- Incluir peers  
  Clique no botão **Incluir peers** na parte superior direita para incluir nós peer em sua rede. Cada membro pode incluir até três peers em uma rede. É possível incluir nós peer pela primeira vez quando você cria ou se associa a uma rede ou posteriormente no Monitor de Rede.   
  No painel pop-up "Incluir Peers", selecione o número e o tamanho dos nós peer que você deseja incluir.  Atualmente, apenas peers "pequenos" estão disponíveis para compra, no entanto, eventualmente haverá "médio" e "grande" para ajudar a acomodar cargas de trabalho maiores e maior rendimento da transação.  Detalhes sobre o dimensionamento de peer e as métricas de desempenho chegarão em breve...
  
## Membros

A tela "Membros" contém duas guias para exibir informações do membro de rede na guia "Membros" e informações de certificado na guia "Certificados".

A **Figura 2** mostra a tela "Membros" inicial exibindo seus membros de rede na guia "Membros":

![Guia Membros na tela Membros](images/monitor_members.png "Membros de rede")
*Figura 2. Membros de rede*

Além dos membros que você convida quando cria a rede, é possível convidar outros membros na guia "Membros". Para convidar um membro para sua rede, insira o nome da instituição e o endereço de e-mail do operador e clique em **Incluir membro**. Uma rede pode ter um total de 15 membros (incluindo o inicializador da rede). Para remover um membro de sua rede, clique no símbolo "remover" no final da linha do membro.

A **Figura 3** mostra a tela "Membros" inicial exibindo certificados de membros na guia "Certificados":

![Guia Certificados na tela Membros](images/monitor_certificates.png "Certificados")
*Figura 3. Certificados*

Os operadores podem gerenciar os certificados para os membros na mesma instituição na guia "Certificados". Clique em **Incluir certificado** para abrir o painel "Incluir certificado". Dê um nome ao seu certificado, cole seus certificados do lado do cliente no formato PEM no campo "Chave" e clique em **Enviar**. Você precisa reiniciar seus peers antes de os certificados do lado do cliente poderem entrar em vigor.

Para obter informações sobre como gerar sua chave de certificado, consulte [Gerando os certificados do lado do cliente](v10_application.html#generating-the-client-side-certificates).

## Canais

É possível separar sua rede em canais, em que cada canal representa um subconjunto de membros que estão autorizados a ver os dados para os chaincodes instanciados nesse canal. Cada rede deve ter pelo menos um canal para que as transações ocorram. Cada canal possui um livro razão exclusivo e os usuários devem ser autenticados adequadamente para executar operações de leitura/gravação nesse livro razão. Se você não estiver em um canal, não poderá ver nenhum dado.

A **Figura 4** mostra a tela do painel inicial exibindo uma visão geral de todos os canais em sua rede:

![Canais](images/channels.png "Canais")
*Figura 4. Canais*

A criação de um canal resultará na geração de um livro razão específico do canal. Para obter mais informações, consulte [Criando um canal](howto/create_channel.html).

Também é possível selecionar um canal existente para visualizar detalhes mais precisos sobre o canal, a associação e os chaincodes ativos. Para obter mais informações, consulte [Monitorando uma rede](howto/monitor_network.html).  


## Chaincode

O chaincode define a lógica de negócios e as instruções transacionais para criar e modificar ativos.

A **Figura 5** mostra a tela do painel inicial de chaincodes:

![Chaincodes](images/chaincode_install_overview.png "Chaincodes")
*Figura 5. Chaincodes*

O Chaincode primeiro é instalado no sistema de arquivos de um peer e, em seguida, instanciado em um canal.  Para obter mais informações, consulte [Instalando e instanciando um chaincode](howto/install_instantiate_chaincode.html).


## Notificações

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


## Versão

A tela "Suporte" contém duas guias que fornecem informações de suporte na guia "Suporte" e que descrevem funções novas e mudadas de cada liberação na guia "Notas sobre a Liberação".

Use os links e recursos nesta página para acessar fóruns de resolução de problemas e suporte. Se você não puder depurar seu problema ou apurar uma resposta para sua pergunta, clique no link **Abrir um chamado de suporte do {{site.data.keyword.Bluemix_notm}}** e siga as orientações para enviar chamados.

A **Figura 7** mostra a tela "Suporte" inicial exibindo informações de suporte na guia "Suporte":

![Suporte](images/support.png "Suporte")
*Figura 7. Suporte de blockchain*

* [{{site.data.keyword.blockchainfull_notm}} Documentos de Serviço](index.html), que é este site de doc, fornece orientações sobre como iniciar o uso do {{site.data.keyword.blockchainfull}} Platform no {{site.data.keyword.Bluemix_notm}}. É possível localizar tópicos correspondentes no navegador ou procurar qualquer termo com a função de procura na parte superior.  
* [IBM Developer Works ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://developer.ibm.com/blockchain/) em **Ajuda da Comunidade** contém recursos e informações para desenvolvedores.  
* [IBM dWAnswers ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://developer.ibm.com/answers/smartspace/blockchain/) em **Chamado de Suporte** serve como uma plataforma para perguntas e respostas. É possível procurar respostas de perguntas feitas anteriormente ou enviar uma nova pergunta. Certifique-se de incluir a palavra-chave **blockchain** em sua pergunta.   
  Também é possível enviar um chamado para a equipe de suporte do {{site.data.keyword.blockchainfull_notm}} com a opção [Abrir um chamado de suporte do {{site.data.keyword.Bluemix_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")]().  Compartilhe detalhes e fragmentos de código de sua instância específica do {{site.data.keyword.Bluemix_notm}}.  
* [Aplicativos de Amostra ![Ícone de link externo](images/external_link.svg "Ícone de link externo")]() em **Aplicativos de Amostra do {{site.data.keyword.blockchain}}** fornece fragmentos de código de orientação e de amostra para ajudar no desenvolvimento de aplicativos.  
* [Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/) e [Comunidade do Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")]() em **Hyperledger Fabric** fornecem mais detalhes sobre a pilha do Hyperledger Fabric.  
  Fale com um [Especialista do Hyperledger ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://chat.hyperledger.org/channel/general) com perguntas sobre o código do Hyperledger Fabric.   
  
  
A **Figura 8** mostra a tela inicial "Membros" que exibe funções novas e mudadas de cada liberação na guia "Notas sobre a Liberação":

![Notas sobre a liberação](images/releasenotes.png "Notas sobre a liberação")
*Figura 8. Notas sobre a Liberação*

