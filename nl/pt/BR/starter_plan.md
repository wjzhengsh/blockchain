---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-23"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Sobre o Starter Plan
{: #starter-plan-about}

O {{site.data.keyword.blockchainfull}} Platform Starter Plan é uma opção de nível de entrada que permite que as organizações simulem redes de blockchain de múltiplas organizações, desenvolvam aplicativos rapidamente e trabalhem com contratos inteligentes de amostra e redes de negócios. Ele também possui a mesma experiência de UI que outras opções de associação, ajudando a eliminar qualquer curva de aprendizado. As novas redes do Starter Plan que forem criadas após 4 de outubro de 2018 serão construídas no Hyperledger Fabric V1.2.1. As redes mais antigas do Starter Plan permanecem no nível do Fabric V1.1.0.
{:shortdesc}

**Starter Plan** é um ambiente para aqueles que desejam começar a desenvolver redes de blockchain ou começar a aprender sobre a tecnologia de blockchain. É possível usar o Starter Plan como um ambiente de desenvolvimento ou de teste que permitirá iniciar em pequeno porte e escalar a associação de sua rede ou o volume de transação antes de mover-se para um ambiente de produção.

 Para implementar uma rede do Starter Plan, inscreva-se agora para a sua [associação do Starter ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) no {{site.data.keyword.cloud_notm}}. Quando você estiver pronto para começar a desenvolver sua rede, visite [introdução ao Starter Plan](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan).


**Notas:**
- O {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan é um ambiente de desenvolvimento e teste e não é adequado para cargas de trabalho de produção. Se você precisar de um ambiente de produção, veja [Sobre o Enterprise Plan](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).
- O {{site.data.keyword.blockchainfull_notm}} Platform é um serviço de plataforma no {{site.data.keyword.cloud_notm}} e todas as ofertas de associação seguem os [Termos de serviços do {{site.data.keyword.cloud_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "Termos de serviços do {{site.data.keyword.cloud_notm}}") em acordos de nível de serviço (SLAs). O Starter Plan é provisionado em um data center de local único. Para obter uma lista de locais disponíveis, consulte [Locais do {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain?topic=blockchain-ibp-regions-locations#ibp-regions-locations).

## O que o Starter Plan oferece
{: #starter-plan-about-what-starter-plan-offers}

- **_Rede pronta com um clique_**
    O Starter Plan fornece uma rede de blockchain ativa com um único clique. Cada rede é fornecida com uma CA, um solicitador, duas organizações (com um par por organização) e um canal padrão no qual transacionar e implementar o chaincode. O {{site.data.keyword.blockchainfull_notm}} Platform manipula a criação e a configuração dessa rede (você poderá atualizá-lo depois que ele ficar em tempo real), permitindo que você se concentre no desenvolvimento. As redes do Starter Plan são construídas no Fabric V1.2 e usam o CouchDB como o banco de dados de estado.
- **_Eficiência de custo_**
    A opção de associação do Starter Plan fornece muitos dos mesmos recursos de blockchain que as opções de associação do Enterprise Plan, mas com um custo menor. Para obter mais informações, veja [Precificação do Starter Plan](/docs/services/blockchain/howto/pricing.html#ibp-pricing-starter-pricing)
- **_Simulação de rede de diversas organizações_**
    É possível usar o Starter Plan para simular a construção de uma rede com várias organizações. Não é necessário realmente convidar outras organizações para a sua rede, mas é possível agir como outras organizações você mesmo. Esse mecanismo permite que você aprenda como uma nova organização pode se associar à rede, como múltiplas organizações trabalham juntas na rede e assim por diante. É possível alternar entre as suas organizações por meio do Monitor de rede para visualizar e gerenciar a rede por meio da visualização das diferentes organizações.

O {{site.data.keyword.IBM_notm}} não fornece suporte para redes que usam o Hyperledger Composer na produção, incluindo a CLI do Composer, as APIs JavaScript, o servidor REST e o Web Playground.
{:note}

## O Plano de Inicialização é adequado para você
{: #starter-plan-about-is-starter-suitable-for-you}

Se você se encaixar em uma das situações a seguir, o Starter Plan será adequado para você começar a sua jornada de blockchain.
- **_Aprendendo o {{site.data.keyword.blockchainfull_notm}} Platform._**
    Se você estiver curioso sobre o {{site.data.keyword.blockchainfull_notm}} Platform ou até mesmo for novo para o blockchain, o Starter Plan oferece um ótimo jeito de aprender como desenvolver e controlar uma rede real de blockchain. É possível localizar os componentes que compõem uma rede, gerenciar a associação, aprender como implementar e gerenciar o chaincode (também conhecido como "contratos inteligentes"), como incluir canais (e gerenciar permissões de canal) e rastrear quando um novo bloco é gerado, como em uma rede de produção.
- **_Construindo soluções demo em uma rede em tempo real._**
    O Starter Plan fornece um ambiente poderoso para demonstrar aplicativos de blockchain. A rede de blockchain pronta para uso permite apresentações rápidas para colegas, gerenciamento e parceiros através das ferramentas operacionais e de gerenciamento que o Monitor de rede fornece.
- **_Movendo além do desenvolvimento de uma única organização._**
    O Starter Plan permite que você aja como várias organizações, o que permite ver como o {{site.data.keyword.blockchainfull_notm}} Platform gerencia tarefas colaborativas, como a criação de canal e a instanciação de chaincode, bem como o teste de aplicativos e a chamada de transações. Também é possível convidar outras pessoas para colaborar em uma rede do Starter Plan (como em planos Enterprise). Isso ajuda você a construir em um ambiente mais realista, com múltiplos partidos endossando chaincode e transações.
- **_Desenvolver e testar iterativamente aplicativos blockchain._**
    O Starter Plan oferece a você uma área de preparação para desenvolver continuamente o seu código em uma rede de blockchain. Mover para nuvem permite que os desenvolvedores e testadores se concentrem em funcionalidades e se movam facilmente do teste de unidade para o teste funcional. O Starter Plan fornece as mesmas funcionalidades de rede de blockchain assim como ferramentas operacionais e de gerenciamento que o Enterprise Plan. Após você estar pronto para enviar por push para um dos planos Enterprise, será possível operar da mesma forma que no Starter Plan, mas com mais oportunidades para aumentar a sua rede.

## Considerações Starter Plan
{: #starter-plan-about-considerations}

O Starter Plan é um ponto de entrada para o {{site.data.keyword.blockchainfull_notm}} Platform e destina-se ao propósito de desenvolvimento e teste.  Verifique os itens a seguir antes de usar o Starter Plan.

- **Requisito de conta do {{site.data.keyword.cloud_notm}}**
    Deve-se ter uma conta paga do {{site.data.keyword.cloud_notm}}, por exemplo, um tipo **Pay-As-You-Go**. Todos os planos de associação que o {{site.data.keyword.blockchainfull_notm}} Platform oferece requerem uma conta paga do {{site.data.keyword.cloud_notm}}. Para fazer upgrade de sua conta para um tipo Pré-pago, acesse **Gerenciar** > **Faturamento e uso** > **Faturamento** no console do {{site.data.keyword.cloud_notm}} e clique em **Incluir cartão de crédito**.
- **Diferenças do Enterprise Plan**
    - A [CA](/docs/services/blockchain/glossary.html#glossary-CA) e serviço de pedido não são tolerantes a falhas porque cada organização tem somente uma CA e uma rede tem somente um [solicitador](/docs/services/blockchain/glossary.html#glossary-orderer).
    - O serviço de solicitação usa apenas [SOLO](/docs/services/blockchain/glossary.html#glossary-solo) [consenso](/docs/services/blockchain/glossary.html#glossary-consensus). Uma rede do Starter Plan consiste em somente um solicitador que executa o consenso para todos os peers.
    - O [Hardware Security Module (HSM)](/docs/services/blockchain/glossary.html#glossary-hsm) não está disponível para proteger e gerenciar chaves digitais para autenticação forte e o processamento de criptografia.
- **Versões e upgrade do Starter Plan**
    - As novas redes do Starter Plan que forem criadas após 4 de outubro de 2018 serão construídas no Hyperledger Fabric V1.2.1. As redes mais antigas do Starter Plan permanecem no nível do Fabric V1.1.0.
    - Novos peers que são incluídos nas redes do Starter Plan mais antigas serão construídos no Fabric v1.2.1. O desempenho de sua rede não é afetado devido à compatibilidade com versões anteriores.
    - Você tem a opção de usar [configuração do canal ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/config_update.html "configuração do canal") mais avançada e [Listas de Controle de Acesso ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/access_control.html "Listas de Controle de Acesso") ao criar ou atualizar um canal.
    - Os recursos de [Descoberta de serviço ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "descoberta de serviço") e [dados privados ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "dados privados") do Hyperledger Fabric v1.2 não são suportados no Starter Plan.
    - Se você [reconfigurar](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-reset-network) uma rede mais antiga do Starter Plan que está no Fabric V1.1.0, sua nova rede estará no nível do Fabric V1.2. Se você reconfigurar sua rede, será necessário instalar os arquivos chaincode ou .bna na nova rede, bem como convidar novamente membros de sua rede antiga.
- **Limitação de recurso de rede**
    O Starter Plan designa 1 CPU e 4 Gi de RAM para cada armazenamento de peer e 20 Gi de armazenamento para cada instância de serviço do {{site.data.keyword.cloud_notm}}, incluindo peers. Os contêineres de chaincode e os blocos de livro-razão são os componentes de rede mais intensivos em recursos. Os usuários que possuem muitos peers em sua rede, geram um monte de blocos ou usam grandes arquivos de chaincode podem experimentar o efeito de limitações de recursos no desempenho. É possível visualizar o uso de armazenamento de suas redes na [tela "Visão geral" de seu Monitor de Rede](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-storage).
- **Manutenção e upgrade**
    A manutenção do Starter Plan e as atualizações de rede são executadas em um planejamento fixo. Durante o período de manutenção, não é possível provisionar novas redes e podem ser observados breves períodos de interrupção de rede.
- ** Retenção de dados**
    O Starter Plan não garante a retenção de dados com upgrades de liberação.
- **Considerações sobre migração**
    A migração dos dados de uma rede do Starter Plan para outros planos de associação não é suportada atualmente. No entanto, é possível migrar arquivos `.bna`, o chaincode e aplicativos que foram testados no Starter Plan. Para obter mais informações, consulte [Migrando do Starter Plan para o Enterprise Plan](/docs/services/blockchain/howto/migrate_sp_ep.html#migrate_starter_to_enterprise).
