---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-21"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Sobre o Starter Plan
{: #overview}

O {{site.data.keyword.blockchainfull}} Platform Starter Plan é uma opção de nível de entrada que permite que as organizações simulem redes de blockchain de múltiplas organizações, desenvolvam aplicativos rapidamente e trabalhem com exemplos fornecidos. Ele também possui a mesma experiência de UI que outras opções de associação, ajudando a eliminar qualquer curva de aprendizado. Redes do Starter Plan são construídas sobre o Hyperledger Fabric V1.1.
{:shortdesc}

**Nota**: o {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan é um ambiente de desenvolvimento e teste e não é adequado para cargas de trabalho de produção. Se você precisar de um ambiente de produção, veja [Sobre o Enterprise Plan](enterprise_plan.html). Você pode verificar as [Considerações do Starter Plan](#considerations) antes de usar o Starter Plan.

Inscreva-se para [se associar ao {{site.data.keyword.blockchainfull_notm}}![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) e experimente o Starter Plan agora! Deve-se escolher **Sul dos EUA** como a região no {{site.data.keyword.cloud_notm}} para criar redes de blockchain com o Starter Plan Beta.


## Público-alvo

Se você se encaixar em uma das situações a seguir, o Starter Plan será adequado para você começar a sua jornada de blockchain.
- **_Saiba {{site.data.keyword.blockchainfull_notm}} Platform._**  
    Se você estiver curioso sobre o {{site.data.keyword.blockchainfull_notm}} Platform ou até mesmo for novo para o blockchain, o Starter Plan oferece um ótimo jeito de aprender como desenvolver e controlar uma rede real de blockchain. É possível localizar os componentes que compõem uma rede, aprender como implementar e gerenciar o chaincode (também conhecido como "contratos inteligentes"), como incluir canais (e gerenciar permissões de canal) e rastrear quando um novo bloco é gerado, da mesma forma que em uma rede de produção.
- **_Deseja agir como múltiplas organizações para facilitar o desenvolvimento de rede._**  
    O Starter Plan permite que você aja como múltiplas organizações, o que permite ver como o IBM Blockchain Platform (IBP) gerencia tarefas colaborativas como a criação de canal e instanciação do chaincode, bem como o teste de aplicativos e a chamada de transações. Também é possível convidar outras pessoas para colaborar em uma rede do Starter Plan (como em planos Enterprise).
- **_Construir soluções de demo em uma rede em tempo real_**  
    O Starter Plan fornece um ambiente poderoso para definições de rede de teste (integrando um arquivo `.bna` que é desenvolvido usando o {{site.data.keyword.blockchainfull_notm}} Platform: Develop) e aplicativos de blockchain. A rede de blockchain pronta para uso permite apresentações rápidas para colegas, gerenciamento e parceiros através das ferramentas operacionais e de gerenciamento que o Monitor de rede fornece.
- **_Iterativamente desenvolver e testar aplicativos de blockchain._**  
    O Starter Plan oferece a você uma área de preparação para desenvolver continuamente o seu código em uma rede de blockchain. É possível desenvolver iterativamente o seu código e implementar em sua integração contínua e arquitetura de implementação contínua. O Starter Plan fornece as mesmas funcionalidades de rede de blockchain assim como ferramentas operacionais e de gerenciamento que o Enterprise Plan. Após você estar pronto para enviar por push para um dos planos Enterprise, será possível operar da mesma forma que no Starter Plan, mas com mais oportunidades para aumentar a sua rede.
- **_Testar projetos antes da produção._**  
    O Starter Plan fornece um ambiente para desenvolvedores e testadores para mover rapidamente de seu ambiente local para um ambiente de blockchain de nuvem real.  Esse mecanismo permite que os desenvolvedores e testadores se concentrem nas funcionalidades e se movam facilmente do teste de unidade para o teste funcional. As equipes do sistema, de solução e de teste de desempenho também podem usar o ambiente sem o esforço extra para configurar uma rede localmente.
- **_{{site.data.keyword.blockchainfull_notm}} Platform operacional educacional._**  
    O {{site.data.keyword.blockchainfull_notm}} Platform fornece praticamente a mesma interface com o usuário<!--the same user interface--> no Starter Plan e nos planos Enterprise, assegurando que o seu treinamento customizado use os mesmos fluxos de trabalho que a sua organização usará em um Enterprise Plan.
- **_Implemente aplicativos de amostra rapidamente usando a cadeia de ferramentas._**  
    O Starter Plan permite a implementação de aplicativos de amostra usando a cadeia de ferramentas com apenas alguns cliques. Essas amostras ajudarão os desenvolvedores fornecendo um conjunto crescente de amostras com código para modificar e seguir em frente.


## Starter Plan especializações

O Starter Plan oferece a capacidade de gerenciar a associação com Autoridade de certificação (CA), executando endosso de transação, fornecendo serviços de solicitação, construindo canais privados, gerenciando ciclos de vida de chaincode e colaborando com outros em uma rede ao vivo, como em um Enterprise Plan.

Especificamente, o Starter Plan oferece uma rede de blockchain pré-configurada que é possível desenvolver, controlar e operar por meio do Monitor de rede. Ele também fornece abordagens simples para implementar aplicativos de amostra e integra os seus aplicativos que você desenvolve com o {{site.data.keyword.blockchainfull_notm}} Platform: Develop.

- **_Rede pronta a um clique_**  
    O Starter Plan provisiona para você uma rede com um serviço de solicitação, CAs, um canal padrão e duas organizações (com um peer por organização) com um único clique. O {{site.data.keyword.blockchainfull_notm}} Platform manipula a criação e configuração dessa rede (você poderá atualizá-lo depois que ele ficar em tempo real). <!--The free trial provides you up to two organizations and two peers.-->
- **_Eficiência de custo_**  
    A opção de associação do Starter Plan fornece muitos dos mesmos recursos de blockchain que as opções de associação do Enterprise Plan, mas com um custo inferior.  <!--During a trial period of Starter Plan, you can provision a blockchain network with basic network resources for free.-->No estágio Beta, é possível usar o Starter Plan grátis.
- **_Simulação de rede de múltiplas organizações_**  
    É possível usar o Starter Plan para simular a construção de uma rede com múltiplas organizações. Não é necessário realmente convidar outras organizações para a sua rede, mas é possível agir como outras organizações você mesmo. Esse mecanismo permite que você aprenda como uma nova organização pode se associar à rede, como múltiplas organizações trabalham juntas na rede e assim por diante. É possível alternar entre as suas organizações por meio do Monitor de rede para visualizar e gerenciar a rede por meio da visualização das diferentes organizações.
    <!--**Note**: It might cause extra cost if you exceed the free trial resource limits of two organizations and two peers.-->
- **_APIs do Swagger_**  
    O Starter Plan expõe várias APIs de REST que podem ser tentadas por meio de uma interface do Swagger. Para obter mais informações, veja [Usando APIs do Swagger](swagger_apis.html).
- **_Aplicativos de amostra_**  
    O Starter Plan alavanca o serviço de cadeia de ferramentas no {{site.data.keyword.cloud_notm}} e permite a implementação de amostra com cliques simples.  Após você implementar e ativar uma amostra, o chaincode e os aplicativos serão executados automaticamente para a sua rede de blockchain. Para obter mais informações sobre aplicativos de amostra, veja [Implementando aplicativos de amostra](howto/prebuilt_samples.html).
- **_Integração de aplicativos do {{site.data.keyword.blockchainfull_notm}} Platform: Develop_**  
    O Starter Plan permite implementar os aplicativos do {{site.data.keyword.blockchainfull_notm}} Platform: Develop em sua rede.  Para obter mais informações, veja [Importando aplicativos do IBP: Develop](link).

<!--
## Migrate to enterprise membership options
After you are confident to run your real business in {{site.data.keyword.blockchainfull_notm}} Platform, you can migrate from Starter Plan to Enterprise Plan.
-->

<!--
## Pricing
Starter Plan offers you a free trial for 60 days.  During the trial period, you can have a blockchain network with the basic configuration of 2 organizations and 1 peer per each organization.  After the trial period, you must pay $300 per month for your network with the same basic configuration.  If you need more peers, you must pay $75 per month for each additional peer.
The monthly fees are prorated and billed daily. For example, a member with basic network configuration (associated fee of $300) and 2 additional peers (per peer fee of $75 X 2 peers) needs to pay $450 every month. If the month has 30 days, the member pays $15 ($450/30) every day.
Network members can pay their bill with their own {{site.data.keyword.cloud_notm}} accounts that contain the space to create the network instance.  Alternatively, one network member can cover the bill for all members in the network.  For more details about how to pay for the blockchain networks, see [Paying for the network](howto/pay_for_the_network.html).
-->

## Considerações Starter Plan
{: #considerations}

O Starter Plan é um ponto de entrada para o {{site.data.keyword.blockchainfull_notm}} Platform e destina-se ao propósito de desenvolvimento e teste. Verifique os itens a seguir antes de usar o Starter Plan.

- **{{site.data.keyword.cloud_notm}} conta requisito**  
    Starter Plan é grátis durante Beta.  No entanto, deve-se fazer upgrade de sua conta do {{site.data.keyword.cloud_notm}} para uma conta paga, por exemplo, um tipo **Pré-pago**. Todos os planos de associação que o {{site.data.keyword.blockchainfull_notm}} Platform oferece requerem uma conta paga do {{site.data.keyword.cloud_notm}}. Para fazer upgrade de sua conta para um tipo Pré-pago, acesse **Gerenciar** > **Faturamento e uso** > **Faturamento** no console do {{site.data.keyword.cloud_notm}} e clique em **Incluir cartão de crédito**.
- **Diferenças do Enterprise Plan**
    - [CA](glossary.html#ca) e [serviço de solicitação](glossary.html#orderer) não são tolerantes a falhas porque cada organização tem apenas uma Autoridade de certificação e uma rede possui apenas um [solicitante](glossary.html#orderer).
    - O serviço de solicitação usa apenas [SOLO](glossary.html#SOLO) [consenso](glossary.html#consensus). Uma rede do Starter Plan consiste apenas de um [solicitante](glossary.html#orderer) que executa consenso para todos os peers.
    - O [Hardware Security Module (HSM)](glossary.html#hsm) não está disponível para proteger e gerenciar chaves digitais para autenticação forte e o processamento de criptografia.
- **Rede limitação de recurso**  
    O Starter Plan designa 1 CPU, 2 GB de RAM e 20 Gi de armazenamento de hardware constantemente para cada instância de serviço do {{site.data.keyword.cloud_notm}}. Se você incluir mais recursos de rede na mesma rede, os seus recursos compartilharão as designações de hardware. No entanto, quando você convidar um membro para se associar à rede, o membro criará uma nova instância de serviço. Portanto, o Starter Plan designa outro conjunto de 1 CPU, 2 GB de RAM e 20 Gi de hardware de armazenamento para o novo membro.
- **Inativo de Exclusão**  
    Após a criação de uma rede do Starter Plan (Beta), se você não visitar a rede nem emitir nenhuma transação nela durante 15 dias, o {{site.data.keyword.blockchainfull_notm}} Platform excluirá a rede.
    - Se você não precisar mais da rede, não precisará tomar nenhuma ação e a sua rede será excluída automaticamente.
    - Se você ainda precisar usar a rede, poderá mantê-la ativa emitindo transações nela. É possível localizar a sua rede do Starter Plan (Beta) em seu [Painel do {{site.data.keyword.cloud_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/dashboard/apps/){:new_window}. Se você não tiver transações regulares para emitir, siga as [instruções](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan) para experimentar os aplicativos de amostra.
- **Manutenção e upgrade**  
    A manutenção e as atualizações de rede do Starter Plan são executadas em um planejamento fixo. Durante o período de manutenção, não é possível provisionar novas redes e podem ser observados breves períodos de interrupção de rede.
- **retenção de dados**  
    O Starter Plan não garante a retenção de dados com upgrades de liberação, incluindo a movimentação de Beta para GA.
- **Considerações da Migração**  
    A migração dos dados de uma rede do Starter Plan para outros planos de associação não é suportada atualmente. No entanto, é possível migrar arquivos `.bna`, o chaincode e aplicativos que foram testados no Starter Plan. Para obter mais informações, veja [Migrando do Starter Plan para o Enterprise Plan](get_start_starter_plan.html#migrate).
<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->
