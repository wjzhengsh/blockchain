---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Sobre o Starter Plan
{: #overview}

O {{site.data.keyword.blockchainfull}} Platform Starter Plan é uma opção de nível de entrada que permite que as organizações simulem redes de blockchain de múltiplas organizações, desenvolvam aplicativos rapidamente e trabalhem com contratos inteligentes de amostra e redes de negócios. Ele também possui a mesma experiência de UI que outras opções de associação, ajudando a eliminar qualquer curva de aprendizado. Redes do Starter Plan são construídas sobre o Hyperledger Fabric V1.1.
{:shortdesc}

**Notas**:
1. O {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan é um ambiente de desenvolvimento e teste e não é adequado para cargas de trabalho de produção. Se você precisar de um ambiente de produção, veja [Sobre o Enterprise Plan](enterprise_plan.html). Você pode verificar as [Considerações do Starter Plan](#considerations) antes de usar o Starter Plan.
2. O Starter Plan se moveu do Beta para o GA em 14 de junho de 2018. Se você tiver redes do Beta, poderá ler as [considerações de migração](#beta-to-ga).

Inscreva-se para a sua associação do [{{site.data.keyword.blockchainfull_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) e experimente o Starter Plan com [créditos de avaliação](howto/pricing.html#starter-plan-pricing) agora! <!--Note that you must choose **US South** as the region in {{site.data.keyword.cloud_notm}} to create blockchain networks with Starter Plan.-->

## O que o Stater Plan oferece

- **_Rede pronta a um clique_**  
    O Starter Plan provisiona você com uma rede de blockchain em tempo real com um único clique. Cada rede vem com um serviço de solicitação, autoridades de certificação, duas organizações (com um peer por organização) e um canal padrão no qual transacionar e implementar o chaincode. O {{site.data.keyword.blockchainfull_notm}} Platform manipula a criação e a configuração dessa rede (você poderá atualizá-lo depois que ele ficar em tempo real), permitindo que você se concentre no desenvolvimento. Redes do Starter Plan são construídas no Hyperledger Fabric V1.1 e usam o CouchDB como o banco de dados de livro-razão. <!--The free trial provides you up to two organizations and two peers.-->
- **_Eficiência de custo_**  
    A opção de associação do Starter Plan fornece muitos dos mesmos recursos de blockchain que as opções de associação do Enterprise Plan, mas com um custo inferior. Durante um período de avaliação do Starter Plan, é possível provisionar uma rede de blockchain com recursos básicos de rede gratuitamente.
- **_Simulação de rede de múltiplas organizações_**  
    É possível usar o Starter Plan para simular a construção de uma rede com múltiplas organizações. Não é necessário realmente convidar outras organizações para a sua rede, mas é possível agir como outras organizações você mesmo. Esse mecanismo permite que você aprenda como uma nova organização pode se associar à rede, como múltiplas organizações trabalham juntas na rede e assim por diante. É possível alternar entre as suas organizações por meio do Monitor de rede para visualizar e gerenciar a rede por meio da visualização das diferentes organizações.
    **Nota**: isso poderá causar custo extra se você incluir mais recursos do que os recursos básicos de a avaliação grátis de duas organizações e dois peers.
<!-- - **_Easy to deploy sample applications_**  
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](howto/prebuilt_samples.html). -->
- **_Integração de aplicativos do {{site.data.keyword.blockchainfull_notm}} Platform: Develop_**  
    O Starter Plan permite implementar redes de negócios que você desenvolveu no [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](https://blockchaindevelop.mybluemix.net/login). Para obter mais informações, veja [Importando aplicativos do IBP: Develop](link).

## Público-alvo

Se você se encaixar em uma das situações a seguir, o Starter Plan será adequado para você começar a sua jornada de blockchain.
- **_Aprendendo {{site.data.keyword.blockchainfull_notm}} Platform._**  
    Se você estiver curioso sobre o {{site.data.keyword.blockchainfull_notm}} Platform ou até mesmo for novo para o blockchain, o Starter Plan oferece um ótimo jeito de aprender como desenvolver e controlar uma rede real de blockchain. É possível localizar os componentes que compõem uma rede, gerenciar a associação, aprender como implementar e gerenciar o chaincode (também conhecido como "contratos inteligentes"), como incluir canais (e gerenciar permissões de canal) e rastrear quando um novo bloco é gerado, como em uma rede de produção.
- **_Construindo soluções de demo em uma rede em tempo real._**  
    O Starter Plan fornece um ambiente poderoso para demonstrar aplicativos de blockchain. A rede de blockchain pronta para uso permite apresentações rápidas para colegas, gerenciamento e parceiros através das ferramentas operacionais e de gerenciamento que o Monitor de rede fornece.
- **_Movendo-se além do desenvolvimento de organização única._**  
    O Starter Plan permite que você aja como múltiplas organizações, o que permite ver como o IBM Blockchain Platform gerencia tarefas colaborativas como a criação de canal e instanciação do chaincode, bem como testar aplicativos e chamar transações. Também é possível convidar outras pessoas para colaborar em uma rede do Starter Plan (como em planos Enterprise). Isso ajuda você a construir em um ambiente mais realista, com múltiplos partidos endossando chaincode e transações.
- **_Iterativamente desenvolver e testar aplicativos de blockchain._**  
    O Starter Plan oferece a você uma área de preparação para desenvolver continuamente o seu código em uma rede de blockchain. Mover para nuvem permite que os desenvolvedores e testadores se concentrem em funcionalidades e se movam facilmente do teste de unidade para o teste funcional. O Starter Plan fornece as mesmas funcionalidades de rede de blockchain assim como ferramentas operacionais e de gerenciamento que o Enterprise Plan. Após você estar pronto para enviar por push para um dos planos Enterprise, será possível operar da mesma forma que no Starter Plan, mas com mais oportunidades para aumentar a sua rede.


## Considerações Starter Plan
{: #considerations}

O Starter Plan é um ponto de entrada para o {{site.data.keyword.blockchainfull_notm}} Platform e destina-se ao propósito de desenvolvimento e teste.  Verifique os itens a seguir antes de usar o Starter Plan.

- **{{site.data.keyword.cloud_notm}} conta requisito**  	
    Você deve ter uma conta do {{site.data.keyword.cloud_notm}} paga, por exemplo, um tipo **Pay-As-You-Go**. Todos os planos de associação que o {{site.data.keyword.blockchainfull_notm}} Platform oferece requerem uma conta paga do {{site.data.keyword.cloud_notm}}. Para fazer upgrade de sua conta para um tipo Pré-pago, acesse **Gerenciar** > **Faturamento e uso** > **Faturamento** no console do {{site.data.keyword.cloud_notm}} e clique em **Incluir cartão de crédito**.  
- **Diferenças do Enterprise Plan**
    - [CA](glossary.html#ca) e [serviço de solicitação](glossary.html#orderer) não são tolerantes a falhas porque cada organização tem apenas uma Autoridade de certificação e uma rede possui apenas um [solicitante](glossary.html#orderer).
    - O serviço de solicitação usa apenas [SOLO](glossary.html#SOLO) [consenso](glossary.html#consensus). Uma rede do Starter Plan consiste apenas de um [solicitante](glossary.html#orderer) que executa consenso para todos os peers.
    - O [Hardware Security Module (HSM)](glossary.html#hsm) não está disponível para proteger e gerenciar chaves digitais para autenticação forte e o processamento de criptografia.
- **Rede limitação de recurso**  
    O Starter Plan designa 1 CPU e 2 Gi de RAM para cada peer e 20 Gi de armazenamento para cada instância de serviço do {{site.data.keyword.cloud_notm}}. 
- **Inativo de Exclusão**  
    Após criar uma rede do Starter Plan, se você não visitar a rede nem emitir nenhuma transação nela durante 15 dias, o {{site.data.keyword.blockchainfull_notm}} Platform excluirá a rede.
    - Se você não precisar mais da rede, não precisará tomar nenhuma ação e a sua rede será excluída automaticamente.
    - Se você ainda precisar usar a rede, poderá mantê-la ativa emitindo transações nela. É possível localizar a sua rede do Starter Plan em seu Painel do [{{site.data.keyword.cloud_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/dashboard/apps/){:new_window}. Se você não tiver transações regulares para emitir, siga as [instruções](howto/prebuilt_samples.html#deploying-sample-applications-in-starter-plan) para experimentar os aplicativos de amostra.
- **Manutenção e upgrade**
    A manutenção do Starter Plan e as atualizações de rede são executadas em um planejamento fixo. Durante o período de manutenção, não é possível provisionar novas redes e podem ser observados breves períodos de interrupção de rede.
- **Retenção de dados**
    O Starter Plan não garante a retenção de dados com upgrades de liberação, incluindo a movimentação do Beta para o GA.
- **Considerações da Migração**
    - A migração dos dados de uma rede do Starter Plan para outros planos de associação não é suportada atualmente. No entanto, é possível migrar arquivos `.bna`, o chaincode e aplicativos que foram testados no Starter Plan. Para obter mais informações, veja [Migrando do Starter Plan para o Enterprise Plan](get_start_starter_plan.html#migrate).

<!--    - Starter Plan is built on Hyperledger Fabric V1.1.  If your chaincode is at Fabric V1.0 level, you need to upgrade your chaincode before you use it in Starter Plan. For more information, see [Updating chaincode for Hyperledger Fabric 1.1](knownissues.html/update-chaincode-fabric11).
-->

## Migrando do Beta para GA
{: #beta-to-ga}

O Starter Plan foi movido para o estágio GA em 14 de junho de 2018. Na GA, o IBM Blockchain Platform oferece créditos de avaliação de US$ 500 para cada conta do IBM Cloud para criar redes de blockchain com o plano Starter. Para obter mais informações sobre os créditos de teste, o Starter Plan foi movido para o estágio GA em 14 de junho de 2018. Na GA, o IBM Blockchain Platform oferece créditos de avaliação de US$ 500 para cada conta do IBM Cloud para criar redes de blockchain com o plano Starter. Para obter mais informações sobre os créditos de avaliação, veja a seção *Avaliação do Starter Plan* em [Precificação do Starter Plan](howto/pricing.html#starter-plan-pricing). Assegure-se de que você tenha uma conta do IBM Cloud paga, por exemplo, um tipo **Pré-pago**.
. Assegure-se de que você tenha uma conta do IBM Cloud paga, por exemplo, um tipo **Pré-pago**.

Qualquer rede de blockchain que for criada com o Starter Plan Beta permanece **grátis** até ser excluída **30 dias** após o Starter Plan GA. A migração de dados não é suportada de redes do Starter Plan Beta para redes do GA. **Os seus dados em redes do Beta serão perdidos.**  No entanto, é possível migrar o seu chaincode, redes de negócios e aplicativos manualmente.
- Se você tiver chaincode em execução em redes do Beta, instale e instancie o chaincode em redes do GA. Para obter mais informações, veja [Instalando, instanciando e atualizando um chaincode](howto/install_instantiate_chaincode.html).
- Se você implementou uma rede de negócios em redes do Beta, implemente a rede de negócios com o arquivo `.bna` em redes do GA. Para obter mais informações, veja [Implementando uma rede de negócios no Starter Plan](develop_starter.html).
- Se você executou aplicativos autoimplementados com relação as redes do Beta, atualize os terminais de API em seus aplicativos para apontar para os nós de rede do GA. Para obter mais informações, veja [Incluindo terminais de API de rede em seu aplicativo](v10_application.html#adding-network-api-endpoints-to-your-application).
