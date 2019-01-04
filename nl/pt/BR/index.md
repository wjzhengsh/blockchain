---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

**ATENÇÃO:** antes de usar uma oferta do {{site.data.keyword.blockchainfull}} Platform, leia as informações técnicas e de suporte na seção [Renúncia de responsabilidade](needtoknow.html).
{:shortdesc}

O {{site.data.keyword.blockchainfull_notm}} Platform (IBP) é a única plataforma integrada pronta para negócios que aborda todo o ciclo de vida de uma rede de blockchain com várias organizações. Ele foi projetado para acelerar, por meio da colaboração em cada fase, a criação de redes de blockchain globais "construídas para os negócios" com o desempenho e a segurança até mesmo para os casos de uso e os setores regulamentados mais exigentes. As ofertas de rede gerenciadas pelo {{site.data.keyword.IBM_notm}} no {{site.data.keyword.cloud_notm}} são adequadas para clientes que são novos no blockchain. Os clientes com experiências no uso do Hyperledger Fabric podem implementar componentes de rede ou uma rede em sua própria infraestrutura por meio do {{site.data.keyword.cloud_notm}} Private (ICP) ou do Amazon Web Services (AWS).

O {{site.data.keyword.blockchainfull_notm}} Platform fornece diferentes ofertas para ajudar todos os tipos de usuários a iniciar em sua jornada de blockchain e mover seus aplicativos para a produção.

|       | [**Starter Plan**](#starter-enterprise)      | [ **Enterprise Plan**](#starter-enterprise)  | [**IBP for ICP**](#ibp-for-icp) | [**IBP for AWS**](#ibp-for-aws)|
| ------------------------- |--------------------------|-----|-----|------|
| **O que está incluído** | **Níveis básicos de serviço, desenvolvimento e ambiente de teste** | **Níveis avançados de serviço e ambiente corporativo pronto para produção** |**Gráficos Helm implementáveis de CA, Solicitador e Peer** | **Peer de iniciação rápida do AWS**  |
| **Política de faturamento** | **Assinatura mensal com [créditos de nuvem disponíveis](howto/pricing.html#starter-plan-pricing)** | **Assinatura mensal** |  ** [Precificação VPC](ibp-for-icp-about.html#ibp-icp-pricing) e Community Edition gratuita** | **Grátis** |
| **Plataforma de nuvem**| **IBM Cloud**|**IBM Cloud**|**IBM Cloud Private**| **AWS**|

**Cuidado:** não use o **Starter Plan** para uso na produção. Ele é um ambiente de desenvolvimento e teste e não é adequado para cargas de trabalho de produção.

## Recursos do {{site.data.keyword.blockchainfull_notm}} Platform

As ofertas do {{site.data.keyword.blockchainfull_notm}} são construídas no código base do [Hyperledger Fabric](reference/v10_fabric.html) que alavanca uma arquitetura modular para atingir níveis corporativos de segurança, integridade de dados, escalabilidade e desempenho para atender às suas necessidades de negócios.

O {{site.data.keyword.blockchainfull_notm}} Plataforma fornece uma rede de blockchain altamente segura e licenciada, sobre a qual os membros autenticados podem definir facilmente os ativos e criar as soluções de negócios para modificar e trocá-las. Com as ofertas do {{site.data.keyword.blockchainfull_notm}} Platform, é possível tirar vantagem de uma estrutura de orquestração que permite que você **organize rapidamente seu consórcio em uma rede de blockchain ativa**. O {{site.data.keyword.blockchainfull_notm}} Platform fornece um conjunto de ferramentas aliado projetado para tornar simples para várias instituições unir-se e criar uma rede governada democraticamente. A criação, governança e tarefas de operação da rede se tornam intuitivas e transparentes por meio de um monitor de painel integrado e de utilitários provisionados. Em vez de passar pelo processo complicado de criar uma rede e implementar a governança, os membros do consórcio podem **focar na implementação de contratos inteligentes e transferência de ativos e informações**.

**Alta disponibilidade** para os recursos integrais da rede (peers, solicitadores, Autoridades de Certificação) elimina os efeitos incapacitantes que podem surgir de pontos únicos de falha. Um monitor de painel integrado permite o fácil gerenciamento desses recursos e fornece um mecanismo poderoso para visualizar os ativos e contratos inteligentes. *Suportado somente nas ofertas Starter e Enterprise*.

A **modularidade** da arquitetura do Hyperledger Fabric e a separação distinta de funções de rede fornecem uma infraestrutura que permite escalabilidade e adaptabilidade a uma ampla variedade de casos de uso. *Disponível e suportado em todas as ofertas do {{site.data.keyword.blockchainfull_notm}} Platform*.

As verificações e saldos que ocorrem durante todo o ciclo de vida de uma transação garantem resultados consistentes e totalmente controlados e os livros-razão permanecem constantemente sincronizados por meio de uma implementação do protocolo gossip. Identidade e controle de acesso são facilmente aplicados por meio de operações de **assinatura/verificação** que ocorrem perpetuamente em toda a rede, que estão *disponíveis e são suportadas em todas as ofertas do {{site.data.keyword.blockchainfull_notm}} Platform*.

É fornecido um **conjunto de ferramentas de governança** para permitir que os membros administrem e gerenciem as regras de negócios críticas para suas redes. Por exemplo, talvez você queira implementar uma política que defina quantos membros de uma rede devem concordar para que um novo membro se associe. Ou, talvez haja um ativo que requeira aprovação de cada participante para que uma modificação ocorra. As regras de governança são fundamentais para qualquer tipo de rede de negócios e geralmente são extremamente elaboradas. O conjunto de ferramentas de governança (editores de política, por exemplo) simplifica enormemente esse processo. *Disponível somente nas ofertas Starter e Enterprise*.

As redes do Enterprise Plan são executadas em um ambiente **altamente seguro e isolado** sem acesso externo (incluindo acesso raiz) aos recursos de rede. Os dados são criptografados em andamento e em repouso e os módulos de suporte de segurança de hardware permitem que chaves digitais sejam protegidas em conformidade com os regulamentos do segmento de mercado. A virtualização de hardware é usada para executar cada nó em um ambiente isolado, protegendo, assim, outros nós na rede contra peers com chaincode potencialmente inadequado ou malicioso. O hashing, as operações de assinatura/verificação e as comunicações de nó para nó são acelerados graças às implementações avançadas de criptografia. *Disponível somente com o Enterprise*.

Para obter mais detalhes sobre todos os recursos e a funcionalidade do Hyperledger Fabric,
consulte [Documentação do Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/ "Documentação do Hyperledger Fabric").

A **Figura 1** descreve um exemplo de uma rede de blockchain implementada que consiste em quatro membros (cada um deles possuindo dois pares), Autoridades de certificação que são responsáveis por distribuir material de identidade criptográfica e um serviço de ordenação que define políticas e participantes da rede. O canal azul contém todos os quatro membros da rede e o canal amarelo é restrito a apenas três membros: Bancos B, C e D. Também é possível ver que o Banco A desempenha a função de inicializador de rede e que o Banco D existe apenas como um membro no contexto do canal amarelo. Por último, um usuário ou aplicativo na posse de um certificado x509 devidamente assinado pode enviar chamadas para peers na rede.

![Rede de Blockchain](images/blockchain_network_2-01.png "Exemplo de rede de blockchain")

*Figura 1. Um exemplo de rede de blockchain com quatro membros que utilizam canais para isolar dados*

## Desenvolver aplicativos para execução na rede

Explore e acelere o desenvolvimento de blockchain, alavancando as melhores práticas aprimoradas a partir de mais de 400 engajamentos de cliente:

* Assegurar o alinhamento estrito nos negócios utilizando uma tecnologia que reduz significativamente o tempo de desenvolvimento do aplicativo (aplicativos que de outra forma levariam seis semanas para serem desenvolvidos podem ser criados em menos de dois dias).
* Construir rapidamente habilidades de blockchain em sua lista existente de programadores utilizando as ferramentas de desenvolvedor do {{site.data.keyword.blockchainfull_notm}}.
* Fornecer aos desenvolvedores a flexibilidade para aprender e desenvolver em seus ambientes preferenciais com um conjunto de ferramentas aberto e moderno.

Como um proprietário de negócios, é possível desenvolver o seu caso de uso com a ajuda da profunda bancada de especialistas em segmento de mercado e blockchain da IBM que se reúnem no {{site.data.keyword.blockchainfull_notm}} Garage para aproveitar o poder total do {{site.data.keyword.blockchainfull_notm}} Platform.

Como um desenvolvedor, é possível alinhar os requisitos de negócios de maneira rápida e fácil e acelerar o desenvolvimento de aplicativos de blockchain no ambiente de rede do {{site.data.keyword.blockchainfull_notm}} Platform usando um playground interativo para desenvolver, iterar e testar redes de negócios. Essas ferramentas foram projetadas para transformar regras de negócios em código de rede de negócios em seu ambiente preferencial:

* **Explore on-line**
  Alavanque [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](./develop.html)<!--, which is powered by an open source development tool--> para aprender os principais conceitos de blockchain, criar definições de rede e alavancar modelos da indústria e bibliotecas de contrato inteligente reutilizáveis.

  Após o desenvolvimento de sua rede de negócios, será possível implementá-la em uma rede em tempo real executada no {{site.data.keyword.blockchainfull_notm}} Platform. Para obter mais informações, veja [Implementando redes de negócios com o Starter Plan](./develop_starter.html) e [Implementando redes de negócios com o Enterprise Plan](./develop_enterprise.html).

* **Instale localmente**
Alavanque o Hyperledger Fabric para desenvolver e testar diretamente em seu laptop. Para obter mais informações, consulte [Construindo sua primeira rede](http://hyperledger-fabric.readthedocs.io/en/release-1.2/build_network.html).

* **Colabore em um ambiente de nuvem**
Use redes ativas prontas para uso com as opções Starter Plan e Enterprise Plan para desenvolver e compartilhar seu código com outras pessoas. Para obter mais informações, veja [Sobre o Starter Plan](starter_plan.html) e [Sobre o Enterprise Plan](enterprise_plan.html).

## Suporte do {{site.data.keyword.IBM_notm}}

O {{site.data.keyword.IBM_notm}} oferece várias opções de suporte nas soluções de blockchain implementadas pelo {{site.data.keyword.IBM_notm}}. Para obter mais informações sobre o Suporte {{site.data.keyword.blockchainfull_notm}}, consulte [Obtendo suporte](ibmblockchain_support.html).


## Starter Plan e Enterprise Plan
{: #starter-enterprise}

Os planos Starter e Enterprise permitem que você implemente e opere redes de blockchain descentralizadas com um serviço seguro pronto para produção. **Starter Plan** é um ambiente para aprender ou começar a desenvolver redes de blockchain e **Enterprise Plan** é um ambiente de produção que oferece altos níveis de segurança e suporte. Comece pequeno e escale sua rede elasticamente à medida que seus volumes de associação e de transação aumentam, aproveitando os recursos a seguir:

* Um ambiente de segurança ultra alta com muitos recursos de segurança de hardware, de firmware e de software.
* Arquitetura protegida para escalabilidade, resiliência e disponibilidade.
* Otimizado para desempenho e execução na computação do Linux mais rápida do mundo.

A operação de sua rede no {{site.data.keyword.blockchainfull_notm}} Platform inclui ferramentas e recursos para simplificar tarefas administrativas:

* Painéis para monitorar e gerenciar os recursos na rede.
* Upgrades ininterruptos da pilha de código integral.
* Suporte técnico 24/7 que está integrado ao portal.
* Pilha de segurança protegida sem acesso privilegiado, resistência a malware e violação, 100% de criptografia e muito mais recursos para redes com dados sensíveis em setores regulamentados.

O Starter Plan e o Enterprise Plan são serviços de plataforma no {{site.data.keyword.cloud_notm}} e ambos os planos seguem os [Termos de serviços do {{site.data.keyword.cloud_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www-03.ibm.com/software/sla/sladb.nsf/sla/bm-6605-13 "Termos de serviços do {{site.data.keyword.cloud_notm}}") em acordos de nível de serviço (SLAs). Observe que as redes de plano Starter e Enterprise são provisionadas em **diversos ambientes** em datacenters separados geograficamente.

Para obter mais informações sobre o Starter Plan e o Enterprise Plan, consulte [Sobre o Starter Plan](starter_plan.html) e [Sobre o Enterprise Plan](enterprise_plan.html). Se você estiver pronto para iniciar, inscreva-se agora para sua [Associação ao IBP Starter ou Enterprise ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps) no {{site.data.keyword.cloud_notm}}!

## {{site.data.keyword.blockchainfull_notm}} Platform for ICP
{: #ibp-for-icp}

O {{site.data.keyword.blockchainfull_notm}} Platform for ICP entrega os componentes necessários para executar uma rede de blockchain em sua própria infraestrutura por meio do ICP. Os componentes incluem o Hyperledger Fabric, uma Autoridade de Certificação (CA), um solicitador e um peer, que você implementa, gerencia e configura usando os gráficos Helm do Kubernetes. **Esta oferta é destinada a clientes com experiência avançada no Hyperledger Fabric.**

O IBP for ICP permite que redes de blockchain sejam implementadas em uma nuvem particular para tratar requisitos de residência de dados, regulamentos de mercado e preferências de infraestrutura. Ele simplifica a implementação de elementos essenciais de uma rede de blockchain em sua própria infraestrutura por meio do {{site.data.keyword.cloud_notm}} Private, uma plataforma de aplicativo baseada em Kubernetes para desenvolver e gerenciar aplicativos conteinerizados no local.

 * Permite que os clientes gerenciem redes IBP com sua própria infraestrutura. Uma Community Edition grátis permite que os clientes sejam executados em seus próprios ambientes isolados e seguros, mas nenhum suporte será fornecido.
 * Permite que os clientes configurem o Fabric no Kubernetes usando gráficos Helm e a documentação detalhada para operações.
 * Autoriza clientes com suporte técnico avançado, a menos que você use a Community Edition.

 Para obter mais informações sobre IBP for ICP, consulte [Sobre o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](ibp-for-icp-about.html).

## {{site.data.keyword.blockchainfull_notm}} Platform for AWS
{: #ibp-for-aws}

O {{site.data.keyword.blockchainfull_notm}} Platform for AWS permite que os peers aproveitem o perfil de conexão, as autoridades de certificação do Hyperledger Fabric (autoridades de certificação) e o serviço de ordenação de uma rede existente do Starter ou do Enterprise Plan no {{site.data.keyword.cloud_notm}} para processar transações por meio de um modelo de Iniciação Rápida do AWS. A Iniciação Rápida permite que você implemente peers usando modelos AWS CloudFormation. Esse modelo destina-se aos tomadores de decisão de infraestrutura de TI e administradores de sistema que desejam configurar, implementar e executar rapidamente os peers IBP hospedados do AWS que estão conectados a uma rede de plano Starter ou Enterprise. É possível usar o modelo para construir uma nova nuvem virtual privada (VPC) no AWS ou implementar o peer em um VPC existente.

A Iniciação Rápida conclui as configurações a seguir:
 * Uma arquitetura altamente disponível que abrange duas zonas de disponibilidade.
 * Um VPC configurado com sub-redes públicas de acordo com as melhores práticas do AWS. Isso fornece a você sua própria rede virtual no AWS.
 * Um gateway de Internet para permitir acesso à Internet.
 * Nas sub-redes públicas, dois peers em duas Zonas de Disponibilidade (um par em cada sub-rede).
 * Em cada sub-rede pública, um contêiner de peer com um banco de dados LevelDB integrado ou um contêiner CouchDB secundário.

Para obter mais informações sobre o IBP for AWS, consulte [Sobre o {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services](howto/remote_peer.html).
