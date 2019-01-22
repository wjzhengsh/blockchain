---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Sobre o Starter Plan
{: #overview}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


O {{site.data.keyword.blockchainfull}} Platform Starter Plan é uma opção de nível de entrada que permite que as organizações simulem redes de blockchain de múltiplas organizações, desenvolvam aplicativos rapidamente e trabalhem com contratos inteligentes de amostra e redes de negócios. Ele também possui a mesma experiência de UI que outras opções de associação, ajudando a eliminar qualquer curva de aprendizado. As novas redes do Starter Plan que forem criadas após 4 de outubro de 2018 serão construídas no Hyperledger Fabric V1.2.1. As redes mais antigas do Starter Plan permanecem no nível do Fabric V1.1.0.
{:shortdesc}

**Notas:**
- O {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan é um ambiente de desenvolvimento e teste e não é adequado para cargas de trabalho de produção. Se você precisar de um ambiente de produção, veja [Sobre o Enterprise Plan](enterprise_plan.html). Você pode verificar as [Considerações do Starter Plan](#considerations) antes de usar o Starter Plan.
- O {{site.data.keyword.blockchainfull_notm}} Platform é um serviço de plataforma no {{site.data.keyword.cloud_notm}} e todas as ofertas de associação seguem os [termos de Serviços do {{site.data.keyword.cloud_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "{{site.data.keyword.cloud_notm}} termos de Serviços") sobre os acordos de nível de serviço (SLAs). As redes do Starter Plan são provisionadas em **diversos ambientes** em data centers separados geograficamente.

## O que o Starter Plan oferece

- **_Rede pronta com um clique_**
    O Starter Plan fornece uma rede de blockchain ativa com um único clique. Cada rede é fornecida com uma CA, um solicitador, duas organizações (com um par por organização) e um canal padrão no qual transacionar e implementar o chaincode. O {{site.data.keyword.blockchainfull_notm}} Platform manipula a criação e a configuração dessa rede (você poderá atualizá-lo depois que ele ficar em tempo real), permitindo que você se concentre no desenvolvimento. As redes do Starter Plan são construídas no Fabric V1.2 e usam CouchDB como banco de dados de estado. <!--The free trial provides you up to two organizations and two peers.-->
- **_Eficiência de custo_**
    A opção de associação do Starter Plan fornece muitos dos mesmos recursos de blockchain que as opções de associação do Enterprise Plan, mas com um custo menor. O {{site.data.keyword.blockchainfull_notm}} Platform oferece US$ 500 de créditos em nuvem para novos usuários do Plano Starter. Esses créditos permitem provisionar uma rede de blockchain com recursos básicos de rede grátis por um mês. Consulte [Considerações do Starter Plan](#starter-plan-considerations) para obter mais detalhes.
- **_Simulação de rede de diversas organizações_**
    É possível usar o Starter Plan para simular a construção de uma rede com várias organizações. Não é necessário realmente convidar outras organizações para a sua rede, mas é possível agir como outras organizações você mesmo. Esse mecanismo permite que você aprenda como uma nova organização pode se associar à rede, como múltiplas organizações trabalham juntas na rede e assim por diante. É possível alternar entre as suas organizações por meio do Monitor de rede para visualizar e gerenciar a rede por meio da visualização das diferentes organizações.
    **Nota**: isso poderá causar custo extra se você incluir mais recursos do que os recursos básicos de a avaliação grátis de duas organizações e dois peers.
<!-- - **_Easy to deploy sample applications_**
    Starter Plan uses the Toolchain service in {{site.data.keyword.cloud_notm}} to deploy samples with simple clicks. After you deploy and launch a sample, the chaincode and applications automatically run for your blockchain network. For more information about sample applications, see [Deploying sample applications](howto/prebuilt_samples.html). -->
- **_ {{site.data.keyword.blockchainfull_notm}} Platform: desenvolva a integração de aplicativos_**
    O Starter Plan permite que você implemente redes de negócios que você desenvolveu no [{{site.data.keyword.blockchainfull_notm}} Platform: desenvolver](https://blockchaindevelop.mybluemix.net/login). Para obter mais informações, veja [Implementando uma rede de negócios no Starter Plan](develop_starter.html).

## O Plano de Inicialização é adequado para você

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
{: #considerations}

O Starter Plan é um ponto de entrada para o {{site.data.keyword.blockchainfull_notm}} Platform e destina-se ao propósito de desenvolvimento e teste.  Verifique os itens a seguir antes de usar o Starter Plan.

- **Requisito de conta do {{site.data.keyword.cloud_notm}}**
    Deve-se ter uma conta paga do {{site.data.keyword.cloud_notm}}, por exemplo, um tipo **Pay-As-You-Go**. Todos os planos de associação que o {{site.data.keyword.blockchainfull_notm}} Platform oferece requerem uma conta paga do {{site.data.keyword.cloud_notm}}. Para fazer upgrade de sua conta para um tipo Pré-pago, acesse **Gerenciar** > **Faturamento e uso** > **Faturamento** no console do {{site.data.keyword.cloud_notm}} e clique em **Incluir cartão de crédito**.
- **Creditos de nuvem do {{site.data.keyword.blockchainfull_notm}} Platform**
    O {{site.data.keyword.blockchainfull_notm}} Platform oferece $500 de creditos de nuvem para novos usuários do Starter Plan. Esses créditos ajudam os usuários a iniciarem, cobrindo o custo de uma configuração de rede padrão por um mês.
    - Novos usuários precisam inscrever-se para créditos de nuvem. Eles não são aplicados automaticamente. [Inscreva-se ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-32798 "Inscreva-se") para solicitar os créditos se ainda não tiver feito isso. Aguarde 24 horas para se certificar de que os créditos estão em sua [conta ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/docs/billing-usage/viewing_usage.html#credits "conta") antes de acessar o Starter Plan. Caso contrário, você poderá ser cobrado antes que os créditos se apliquem.
    - Os créditos de nuvem aplicam-se a todos os serviços do {{site.data.keyword.cloud_notm}}. Eles podem ser consumidos por produtos diferentes do {{site.data.keyword.blockchainfull_notm}} Platform usados no {{site.data.keyword.cloud_notm}}.
    - A oferta fornece créditos vitalício. É possível manter seus créditos desde que você mantenha sua conta do {{site.data.keyword.cloud_notm}}. Não é necessário usá-los no primeiro mês.
    Saiba mais sobre o uso de créditos de avaliação usando o [guia de precificação](howto/pricing.html#starter-plan-pricing).
- **Diferenças do Enterprise Plan**
    - [CA](glossary.html#ca) e [serviço de ordenação](glossary.html#orderer) não são tolerantes a falhas porque cada organização tem apenas uma Autoridade de certificação e uma rede possui apenas um [solicitante](glossary.html#orderer).
    - O serviço de ordenação usa apenas [consenso](glossary.html#consensus) [SOLO](glossary.html#solo). Uma rede do Starter Plan consiste apenas de um [solicitante](glossary.html#orderer) que executa consenso para todos os peers.
    - O [Hardware Security Module (HSM)](glossary.html#hsm) não está disponível para proteger e gerenciar chaves digitais para autenticação forte e o processamento de criptografia.
- **Versões e upgrade do Starter Plan**
    - As novas redes do Starter Plan que forem criadas após 4 de outubro de 2018 serão construídas no Hyperledger Fabric V1.2.1. As redes mais antigas do Starter Plan permanecem no nível do Fabric V1.1.0.
    - Novos peers que são incluídos nas redes do Starter Plan mais antigas serão construídos no Fabric v1.2.1. O desempenho de sua rede não é afetado devido à compatibilidade com versões anteriores.
    - É possível usar o recurso [dados privados ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "dados privados") do Hyperledger Fabric v1.2 em novas redes do Starter Plan. Para obter mais informações sobre como usar dados privados com seu chaincode, veja [Dados privados](howto/develop_chaincode.html#private-data).
    - Você tem a opção de usar [configuração do canal ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/config_update.html "configuração do canal") mais avançada e [Listas de Controle de Acesso ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/access_control.html "Listas de Controle de Acesso") ao criar ou atualizar um canal.
    - O recurso  [Descoberta de serviço ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "descoberta de serviço") do Hyperledger Fabric v1.2 não é suportada no {{site.data.keyword.blockchainfull_notm}}.
    - Se você [reconfigurar](v10_dashboard.html#reset-network) uma rede mais antiga do Starter Plan que está no Fabric V1.1.0, sua nova rede estará no nível do Fabric V1.2. Se você reconfigurar sua rede, será necessário instalar os arquivos chaincode ou .bna na nova rede, bem como convidar novamente membros de sua rede antiga.
- **Limitação de recurso de rede**
    O Starter Plan designa 1 CPU e 4 Gi de RAM para cada armazenamento de peer e 20 Gi de armazenamento para cada instância de serviço do {{site.data.keyword.cloud_notm}}, incluindo peers. Os contêineres de chaincode e os blocos de livro-razão são os componentes de rede mais intensivos em recursos. Os usuários que possuem muitos peers em sua rede, geram um monte de blocos ou usam grandes arquivos de chaincode podem experimentar o efeito de limitações de recursos no desempenho. É possível visualizar o uso de armazenamento de suas redes na [tela "Visão geral" de seu Monitor de Rede](v10_dashboard.html#storage).
- **Manutenção e upgrade**
    A manutenção do Starter Plan e as atualizações de rede são executadas em um planejamento fixo. Durante o período de manutenção, não é possível provisionar novas redes e podem ser observados breves períodos de interrupção de rede.
- ** Retenção de dados**
    O Starter Plan não garante a retenção de dados com upgrades de liberação.
- **Considerações sobre migração**
    A migração dos dados de uma rede do Starter Plan para outros planos de associação não é suportada atualmente. No entanto, é possível migrar arquivos `.bna`, o chaincode e aplicativos que foram testados no Starter Plan. Para obter mais informações, consulte [Migrando do Starter Plan para o Enterprise Plan](howto/migrate_sp_ep.html).


<!--
## Migrating from Beta to GA
{: #beta-to-ga}

Starter Plan moves to the GA stage on June 14, 2018. Upon GA, {{site.data.keyword.blockchainfull_notm}} Platform offers $500 trial credits for each {{site.data.keyword.cloud_notm}} account to create blockchain networks with Starter Plan. For more information about the trial credits, see the *Starter Plan trial* section in [Starter Plan pricing](howto/pricing.html#starter-plan-pricing). Ensure that you have a paid {{site.data.keyword.cloud_notm}} account, for example, a **Pay-As-You-Go** type.

Any blockchain networks that are created with Starter Plan Beta remains **free** until they are deleted **30 days** after the Starter Plan GA. Data migration is not supported from Starter Plan Beta networks to GA networks. **Your data in Beta networks will be lost.**  However, you can migrate your chaincode, business networks, and applications manually.
- If you have running chaincode in Beta networks, install and instantiate the chaincode in GA networks. For more information, see [Installing, instantiating, and updating a chaincode](howto/install_instantiate_chaincode.html).
- If you deployed a business network on Beta networks, deploy the business network with the `.bna` file on GA networks. For more information, see [Deploying a business network on Starter Plan](develop_starter.html).
- If you ran self-developed applications against Beta networks, update the API endpoints in your applications to point to GA network nodes. For more information, see [Adding network API endpoints to your application](v10_application.html#adding-network-api-endpoints-to-your-application).
-->
