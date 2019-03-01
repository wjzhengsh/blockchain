---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-11"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Visão geral do componente do Blockchain
{: #blockchain-component-overview}

Os componentes e a estrutura do {{site.data.keyword.blockchainfull}} Platform são baseados na infraestrutura subjacente e nas ferramentas do [Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/), uma solução de blockchain permissionária de software livre para a qual o {{site.data.keyword.IBM_notm}} é um importante contribuidor. As redes baseadas no Fabric incluem vários componentes padrão que podem ser implementados em várias configurações para suportar uma ampla variedade de casos de uso.

Para obter uma visualização mais abrangente das redes do Fabric e a inter-relação dos componentes que o compõem, veja [este documento sobre a estrutura de uma rede de blockchain](https://hyperledger-fabric.readthedocs.io/en/release-1.2/network/network.html) na documentação da comunidade do Fabric, que mostra como uma rede pode ser iniciada e expirada.

Para obter uma visão geral resumida dos componentes em uma rede baseada no Fabric, veja o vídeo abaixo:

<iframe class="embed-responsive-item" id="youtubeplayer" title="Vídeos do Starter Plan" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/sJaT2L99BUo" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

*Embora esse vídeo esteja focado nos componentes da perspectiva de redes Starter e Enterprise, as informações ainda são altamente relevantes para a solução gerenciada pelo cliente do {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.cloud_notm}} Private.*

Para os propósitos desta visão geral, vamos focar apenas nas autoridades de certificação (CAs), nos solicitadores, nos peers, nos contratos inteligentes e nos aplicativos. Como você pode ver no [Guia do {{site.data.keyword.blockchainfull_notm}} Platform 2.0 para construir uma rede](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) e nos tópicos do [Guia de implementação do {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#get-started-icp), essa sequência não é arbitrária; ela reflete a sequência na qual os componentes em uma rede baseada no Fabric serão implementados.

## Autoridades de certificação
{: #blockchain-component-overview-ca}

O suporte de uma rede de blockchain baseada no Fabric são identidades e permissões. Identidades tomam a forma de certificados x.509 que uma CA emite e que são como um cartão de crédito pelo fato de que *identificam* alguém, o que pode incluir atributos sobre elas. Esses certificados são, então, vinculados a permissões por sua inclusão nas pastas do MSP no nível do componente ou do canal. Portanto, por exemplo, um MSP de peer terá uma subpasta MSP chamada **admins**. Qualquer usuário cujo certificado esteja dentro dessa pasta administrativa, é um administrador do peer, o que significa que ele tem a capacidade de executar qualquer ação que o administrador desse peer tenha permissão para executar. Um sistema de validação dentro do peer executa uma verificação sempre que um usuário, identificado por seu certificado de assinatura, tenta executar uma ação administrativa. O certificado corresponde ao que está na pasta "admin"? Em caso afirmativo, a ação poderá ser executada. Caso contrário, a solicitação para executar a ação será rejeitada.

As CAs do {{site.data.keyword.blockchainfull_notm}} Platform são baseadas no [Hyperledger Fabric-CA ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.2/ "Hyperledger Fabric CA"), embora seja possível usar outra CA, desde que use um PKI com base em certificados x.509. Pode haver, e geralmente deve haver, vários níveis de autoridades de certificação. A "autoridade de certificação raiz" para uma rede normalmente não será exposta, exceto para fornecer certificados para "autoridades de certificação intermediárias", o que emitirá certificados para usuários e componentes diretamente ou para mais camadas de autoridades de certificação intermediárias. Para obter mais detalhes sobre como as autoridades de certificação são usadas para estabelecer identidade e associação, consulte [Documentação do Hyperledger Fabric na identidade ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/identity/identity.html) e na [associação ![](images/external_link.svg  "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/membership/membership.html)

## Solicitadores
{: #blockchain-component-overview-orderer}

Embora o serviço de pedido seja frequentemente referido como o "coração" de uma rede, sua função é realmente muito simples: pedir transações que foram validadas pelos peers em blocos e enviá-las de volta para os peers para serem gravadas em seus livros-razão. Nas versões anteriores do Fabric, essa funcionalidade era empacotada dentro do peer, mas a partir do Fabric v1.0, ela está em um componente separado para aumentar o desempenho do peer e evitar anomalias que poderiam resultar em potenciais bifurcações de estado.

Em um nível físico, essa função de ordenação geralmente requer um conjunto de solicitadores que são conhecidos coletivamente como o "serviço de ordenação", embora em ambientes de teste ou POC, seja possível usar um único nó (conhecido como um solicitador SOLO).

## Peers
{: #blockchain-component-overview-peer}

Em um nível físico, uma rede de blockchain é composta principalmente por nós de peer (ou, simplesmente, peers). Os peers são os elementos fundamentais da rede porque eles hospedam livros-razão e contratos inteligentes (que estão contidos em ["chaincode" ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/chaincodenamespace.html "Namespace do chaincode")). Mais precisamente, o peer hospeda **instâncias** do livro-razão e **instâncias** de contratos inteligentes. Como os contratos inteligentes e os livros-razão são usados para encapsular os processos compartilhados e as informações compartilhadas em uma rede, respectivamente, esses aspectos de um peer fazem deles um bom ponto de partida para entender o que uma rede do Fabric realmente faz.

Para saber mais sobre os peers especificamente, verifique [este documento que se concentra apenas em peers ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/peers/peers.html) na documentação da comunidade do Fabric.

## Canais
{: #blockchain-component-overview-channels}

Um canal é um mecanismo que fornece uma camada aberta de comunicação entre os membros na rede. Múltiplos canais podem ser criados entre subconjuntos de membros, suportando, assim, um dos [muitos mecanismos para implementar privacidade ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://developer.ibm.com/tutorials/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/ "Transações privadas e confidenciais com o Hyperledger Fabric"). Os dados na rede de blockchain são armazenados nos livros-razão do canal. Os livros-razão do canal são hospedados nos peers das organizações que se associaram ao canal. Para obter mais informações sobre os canais e como usá-los, veja a [documentação do Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channels.html)

## Contratos inteligentes
{: #blockchain-component-overview-smart-contracts}

Antes de as empresas poderem transacionar entre si, é necessário chegar a um entendimento comum sobre as regras e os processos e definir um certo tipo de contrato. Em conjunto, estes contratos estabelecem o "modelo de negócio" que rege todas as interações entre parceiros de negócios.

A mesma necessidade existe em redes de blockchain, e onde o termo da indústria para esses modelos de negócios é "contratos inteligentes", o Fabric e o {{site.data.keyword.blockchainfull_notm}} Platform contêm esses contratos em uma estrutura maior conhecida como "chaincode", que inclui não apenas a lógica de negócios, mas a infraestrutura subjacente que valida as identidades dos usuários que tentam chamar o contrato inteligente.

Enquanto os contratos no mundo dos negócios são assinados e arquivados em empresas de advocacia, os contratos inteligentes são instalados em peers e "instanciados" em um canal.

## Aplicativos
{: #blockchain-component-overview-applications}

Os aplicativos clientes em uma rede baseada no Fabric como o {{site.data.keyword.blockchainfull_notm}} Platform alavancam infraestruturas subjacentes, como APIs, SDKs e contratos inteligentes para permitir interações do cliente (chamadas e consultas) em um nível mais alto de abstração.

Para ver como os aplicativos interagem com uma rede baseada no Fabric, verifique a [documentação sobre como criar seu primeiro aplicativo ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/write_first_app.html "Criando seu primeiro aplicativo"). Além disso, esteja ciente de que a área está passando por melhorias substanciais no Fabric v1.4, que estão disponíveis no {{site.data.keyword.blockchainfull_notm}} Platform 2.0. Veja o tópico do Hyperledger Fabric [Desenvolvendo aplicativos ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/developapps/developing_applications.html "Desenvolvendo aplicativos") para obter mais informações. Embora esses recursos diretos, como a classe Contrato, não sejam compatíveis com o Fabric v1.2, que é a versão atual do Starter Plan e {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private, ela contém informações conceituais e outras dicas úteis que valem a pena fatorar em seu design do aplicativo e que preparam-no para o futuro.

Para ver como um aplicativo interage com uma rede com base no Fabric, verifique a documentação sobre como [criar aplicativos](/docs/services/blockchain/howto/ibp-console-create-app.html#ibp-console-app).

## Uma rede de exemplo
{: #blockchain-component-overview-example-network}

A **Figura 1** descreve um exemplo de uma rede de blockchain implementada que consiste em quatro membros (cada um deles possuindo dois pares), Autoridades de certificação que são responsáveis por distribuir material de identidade criptográfica e um serviço de ordenação que define políticas e participantes da rede. O canal azul contém todos os quatro membros da rede e o canal amarelo é restrito a apenas três membros: Bancos B, C e D. Também é possível ver que o Banco A desempenha a função de inicializador de rede e que o Banco D existe apenas como um membro no contexto do canal amarelo. Por último, um usuário ou aplicativo na posse de um certificado x509 devidamente assinado pode enviar chamadas para peers na rede.

![Rede de Blockchain](images/blockchain_network_2-01.png "Exemplo de rede de blockchain")

*Figura 1. Um exemplo de rede de blockchain com quatro membros que utilizam canais para isolar dados*
