---

copyright:
  years: 2017
lastupdated: "2017-12-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Criando Canais Gerenciados 

**ATENÇÃO:** antes de usar uma oferta do {{site.data.keyword.blockchainfull}}, leia as informações técnicas e de suporte na seção [Renúncia de responsabilidade](/docs/services/blockchain/needtoknow.html).  
{:shortdesc}

Em alguns casos – mercados de câmbio de moeda estrangeira altamente regulados, por exemplo – pode ser necessário colocar um terceiro confiável encarregado das funções administrativas em canais que normalmente seriam controlados por vários operadores ou membros. 

O processo de fornecimento, tal como uma rede, é semelhante à criação de qualquer rede. A principal diferença está na designação de permissões aos membros no modo como eles podem transacionar dentro de canais.  

As etapas para criar uma rede e convidar membros podem ser localizadas aqui: [Controlar uma rede](/docs/services/blockchain/get_start.html#creating-a-network). 

**Nota**: em um exemplo real, o operador de tal rede pode instalar chaincodes customizados durante a fase "Criar Rede" usando o Editor de Política, mas para o propósito deste exemplo, vamos assumir que sua configuração de rede seja padrão. 

## Crie o canal

Assim que você tiver uma rede e os membros que você convidou tiverem passado pelo processo de integração, navegue para **Canais** na barra de ferramentas. 

Clique em **Novo canal**. Isso o levará para uma tela na qual você dará um nome e uma descrição ao seu canal (a descrição é opcional). 

Após clicar em **Avançar**, você chegará à tela na qual convida membros para o canal e gerencia suas permissões. Para o propósito deste exemplo, digamos que você esteja agindo como um terceiro confiável em uma troca de moeda estrangeira entre dois bancos. Você – o terceiro confiável – se torna o único “Operador” do canal e designa os outros dois bancos como “Escritores”. Isso dará a você a autoridade exclusiva para editar o canal (instanciando chaincode nele, por exemplo -- mais sobre isso posteriormente) enquanto ainda fornece aos dois bancos a capacidade de chamar transações. Sua tela deve ser semelhante a isto: 

  ![Selecionar funções de membro](images/selectmemberroles.png "Selecionar funções de membro") 
*Em que “JoeCo“ é você, o terceiro confiável, e “IBM“ e “Chris“ são os dois bancos (este é apenas um exemplo, obviamente).* 

Depois de ter designado as permissões corretas, clique em **Avançar**. 

Isso levará você para uma tela de atualização de política do canal. Como há apenas um operador nesse canal (você), selecione o número de membros necessários para criar o canal como “1“. Em seguida, clique em **Enviar solicitação**. 

Observação: mesmo que você seja o único operador deste canal, essa ainda será apenas uma solicitação; ainda será necessário navegar para a tela correta para aprovar e enviar a solicitação para criar o canal. 

Os e-mails são enviados para os membros que você convidou, solicitando que eles participem do canal. No entanto, eles têm apenas privilégios de “gravação” e você é responsável por concluir o processo de criação de canal. Navegue para a tela “Notificações” no Monitor de rede, na qual aparece um alerta para criar o canal. Localize a solicitação a ser manipulada nas subguias "Todos" ou "Pendente". Clique em **Revisar solicitação** para exibir os detalhes do canal e clique em **Aprovar**. Em seguida, clique em **Enviar solicitação**. 

Parabéns! Você acabou de criar um canal gerenciado. 

Em uma rede com 15 membros (14 bancos, além de você, o terceiro confiável), você poderia ver dezenas desses canais. Bancos A e B em um canal. Bancos B e F em um canal. Os bancos podem optar por ter canais separados para cada tipo de moeda que eles negociam. Mas, em cada caso, o terceiro confiável cria o canal, endossa as transações e serve como o operador exclusivo. 

# Instanciando o Chaincode

Seu canal foi criado com êxito, mas ainda precisa de chaincode conectado a ele. Em um canal dedicado a trocas de moeda estrangeira, este chaincode exigiria presumidamente que todos os três membros do canal (os dois bancos em questão e você, o terceiro confiável) endossassem **cada** transação. Esse chaincode também pode ser gravado para enviar um registro do *resultado* dessa transação a um canal gerenciado "somente leitura" formado por cada membro na rede (mais sobre isso posteriormente). Antes que qualquer chaincode possa ser instanciado, no entanto, ele deve primeiro ser instalado nos peers de membros. Assim que isso ocorre, ele pode ser instanciado pelo operador do canal (você).  

O procedimento real para instalar e instanciar este tipo de chaincode não é diferente do que para qualquer chaincode (diferente do número de operadores necessários para aprovar a instanciação), portanto, siga as instruções aqui: [Instalando e instanciando um chaincode](/docs/services/blockchain/install_instantiate_chaincode.html.html).

# Um Canal Somente Leitura Gerenciado

Os regulamentos financeiros no mercado de câmbio de moeda estrangeira, para continuar com este caso de uso, podem exigir que também haja um canal na rede que tenha todos os bancos nele em um modo passivo “somente leitura“, permitindo que o **resultado** dessas transações de moeda seja rastreado. Isso serve para assegurar que, por exemplo, o Banco A – que acabou de perder milhões de dólares em uma “aposta“ com Banco B (sobre quanto o valor de uma moeda subirá ou cairá durante um período de tempo) – não deixe de endossar a transação que enviará esses milhões de dólares ao Banco B. 

Para criar esse tipo de canal, siga o mesmo procedimento de criação de canal que você seguiu anteriormente, no entanto, em vez de convidar dois bancos e torná-los "Escritores" (com você como o Operador exclusivo), você convida **cada** banco e torna todos eles "Leitores". Em seguida, siga o mesmo processo para instalar e instanciar o chaincode. 

## {{site.data.keyword.IBM_notm}} Suporte

A {{site.data.keyword.IBM_notm}} oferece suporte para as soluções {{site.data.keyword.blockchain}} implementadas pela {{site.data.keyword.IBM_notm}}. Acesse os detalhes do Suporte do {{site.data.keyword.blockchainfull_notm}} por meio do [{{site.data.keyword.blockchainfull_notm}} DockerHub ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hub.docker.com/u/ibmblockchain/) e explore os engajamentos de suporte disponíveis.

Para obter detalhes completos sobre todos os recursos e a funcionalidade do Hyperledger Fabric v1.0,
consulte a [Documentação do Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/en/latest/).
