---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-23"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Sobre o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private
{: #ibp-icp-about}

O {{site.data.keyword.blockchainfull}} Platform libera o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private, que é uma plataforma de aplicativo para desenvolver e gerenciar aplicativos conteinerizados. A oferta do {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private é baseada no Kubernetes, o que permite que os usuários implementem Autoridades de Certificação (CAs), solicitadores e peers no x86, no LinuxONE e no IBM Z. É possível implementar o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private usando os gráficos do Helm do Kubernetes.
{:shortdesc}

O upgrade do {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private para o Hyperledger Fabric v1.4.0 aconteceu no dia 23 de abril de 2019. No entanto, por enquanto, os dados de boatos e privados não serão suportados.
{:note}

O {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private entrega os componentes necessários para executar uma rede de blockchain em sua própria infraestrutura por meio do {{site.data.keyword.cloud_notm}} Private. Os componentes incluem o Hyperledger Fabric, uma Autoridade de Certificação (CA), um solicitador e um peer, que você implementa, gerencia e configura usando os gráficos Helm do Kubernetes. **Esta oferta é destinada a clientes com experiência avançada no Hyperledger Fabric.**

O {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private permite que redes de blockchain sejam implementadas em uma nuvem privada para tratar requisitos de residência de dados, regulamentações de mercado e preferência de infraestrutura. Ele simplifica a implementação de elementos essenciais de uma rede de blockchain em sua própria infraestrutura por meio do {{site.data.keyword.cloud_notm}} Private, uma plataforma de aplicativo baseada em Kubernetes para desenvolver e gerenciar aplicativos conteinerizados no local.

 * Permite que os clientes gerenciem redes do {{site.data.keyword.blockchainfull_notm}} Platform com sua própria infraestrutura. Uma Community Edition grátis permite que os clientes sejam executados em seus próprios ambientes isolados e seguros, mas nenhum suporte será fornecido.
 * Permite que os clientes configurem o Fabric no Kubernetes usando gráficos Helm e a documentação detalhada para operações.
 * Autoriza clientes com suporte técnico avançado, a menos que você use a Community Edition.

O {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private é um produto em pacote configurável para clientes do {{site.data.keyword.cloud_notm}} Private para implementar componentes de blockchain em seu ambiente local. Depois de importar o gráfico do Helm, será possível localizá-lo como um tile do {{site.data.keyword.blockchainfull_notm}} Platform no Catálogo do {{site.data.keyword.cloud_notm}} Private. Para obter mais informações sobre o {{site.data.keyword.cloud_notm}} Private, consulte a documentação para o [{{site.data.keyword.cloud_notm}} Private versão 3.1.2 ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 3.1.2").

## O que o  {{site.data.keyword.blockchainfull_notm}}  Platform for  {{site.data.keyword.cloud_notm}}  Private oferece

O {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private permite que você implemente todos os componentes fundamentais de um blockchain do Hyperledger Fabric: uma Autoridade de Certificação, um serviço de solicitações e peers. Ele fornece a flexibilidade para implementar diferentes componentes, dependendo de suas necessidades de negócios. É possível usar o {{site.data.keyword.blockchainfull_notm}} for {{site.data.keyword.cloud_notm}} Private para iniciar uma rede de blockchain implementando e configurando um serviço de pedido que liga as organizações em um consórcio de blockchain. Também é possível implementar peers e associar-se a outras redes que usam componentes com base no Fabric, incluindo redes do {{site.data.keyword.blockchainfull_notm}} Platform implementadas em nuvens usando o {{site.data.keyword.cloud_notm}} Private ou redes do Starter Plan e Enterprise Plan hospedadas no IBM Cloud. Para obter mais informações sobre os blocos de construção de redes do Hyperledger Fabric, consulte a [visão geral do componente de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview).

## É o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private adequado para você

A execução dos componentes do {{site.data.keyword.blockchainfull_notm}} Platform fora do {{site.data.keyword.cloud_notm}} fornece mais flexibilidade para aumentar ou ingressar em uma rede de blockchain. Ela ajuda os iniciadores de rede a cultivarem suas redes, permitindo que novos membros se associem usando a plataforma de sua escolha. Isso permitirá que as organizações que estão interessadas em se associar às redes de blockchain coloquem seus peers com seus aplicativos existentes ou se integrem a seus sistemas de registro.

O processo para implementar o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private é complicado e presume um alto grau de conhecimento em Fabric. Se você for novo no Fabric, {{site.data.keyword.cloud_notm}} Private ou {{site.data.keyword.blockchainfull_notm}} Platform e seu objetivo for configurar um ambiente de desenvolvimento ou prova de conceito, considere usar o [Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about) no lugar. Observe também que nem todas as configurações de implementação em potencial são suportadas no {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private.
{:improtant}

Os usuários dessa oferta gerenciarão sua própria segurança e infraestrutura. O {{site.data.keyword.cloud_notm}} não fornece esses serviços. Revise as [Considerações e limitações](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations) na próxima seção antes de iniciar.

## Considerações e limitações
{: #ibp-icp-about-considerations}

Antes de iniciar, assegure-se de entender as **considerações** e **limitações** a seguir:

- Você é responsável pelo monitoramento de funcionamento, pela segurança, pela criação de log e pelo gerenciamento do uso de recurso de seus componentes.
- Componentes que são executados em outros ambientes de nuvem não são visíveis no Monitor de Rede das redes em execução no {{site.data.keyword.cloud_notm}}.
- Os gráficos Helm implementam uma única instância de um solicitador, de um peer ou da CA.
- É possível implementar múltiplos componentes em um único namespace no {{site.data.keyword.cloud_notm}} Private, desde que eles tenham nomes de liberação diferentes.
- Os componentes não podem ser endereçados usando a IU do Swagger na IU do Monitor de Rede.
- TLS mútuo não é suportado.
- A tecnologia subjacente, o Hyperledger Fabric, usa contêineres para executar o chaincode e o Docker para iniciar esse contêiner. A única maneira de um peer iniciar um contêiner de chaincode é usar o Docker-in-Docker, que requer acesso privilegiado.

**Considerações de CA**
- Esse gráfico Helm implementa uma única instância da CA. Como é considerado uma melhor prática ter uma autoridade de certificação separada para cada organização, pode ser necessário implementar várias autoridades de certificação. Por exemplo, se você planeja implementar um solicitador e três peers, precisará de pelo menos duas autoridades de certificação (uma para a organização do solicitador e outra para a organização de peer).
- Embora você possa optar por executar um banco de dados MySQL separado, essa opção não está presente no gráfico Helm. No entanto, o gráfico do Helm implementará um banco de dados SQLite dentro da CA para manipular suas necessidades de banco de dados, que incluem o rastreamento do número de inscrições por usuário e qualquer certificado revogado.

**Considerações do solicitador**
- O serviço de solicitação é compatível com qualquer componente do Hyperledger Fabric v1.1 ou mais recente.
- Este gráfico Helm implementa uma única instância do serviço de ordenação SOLO (um solicitador). Observe que não é possível implementar mais de um solicitador SOLO em um canal para tornar o serviço de ordenação altamente disponível. Essa é uma razão pela qual os serviços de ordenação SOLO são considerados para ambientes de desenvolvimento, em vez de produção. No entanto, é possível implementar diversas instâncias do serviço de ordenação SOLO em redes diferentes (ou seja, com um consórcio separado).

**Considerações sobre peer**

- É possível conectar seus peers somente às redes de blockchain que estão no nível do Fabric v1.1 ou mais recente. É possível localizar a versão do Hyperledger Fabric abrindo a [janela Preferências de rede](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) em seu Network Monitor. Siga as [instruções](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-peer-connection-information) para recuperar as informações de conexão de peer das redes Starter ou Enterprise.
- O tipo de banco de dados do peer deve corresponder ao tipo de banco de dados de sua rede de blockchain, LevelDB ou CouchDB.
- A interface do CouchDB Fauxton não está disponível no peer.
- [Gossip](/docs/services/blockchain/glossary.html#glossary-gossip) para peers não é suportado atualmente. Isso significa que os recursos do Fabric que dependem do gossip, como [dados privados ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data-arch.html "dados privados") e [descoberta de serviço ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "descoberta de serviço"), também não são suportados.

## Pré-requisitos do sistema
{: #ibp-icp-about-prerequisites}

O {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private suporta os sistemas operacionais a seguir:
- Red Hat Enterprise Linux (RHEL) 7.3, 7.4, 7.5
- Ubuntu 18.04 LTS e 16.04 LTS
- SUSE Linux Enterprise Server (SLES) 12 SP3

O gráfico do Helm do {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private foi validado para ser executado nos clusters do {{site.data.keyword.cloud_notm}} Private v3.1.2, no Ubuntu Linux, usando os nós do trabalhador e o armazenamento auxiliar a seguir:

- **LinuxONE e IBM Z**: z/VM e KVM, que estão usando NFS.
- **x86**: Linux de 64 bits que usa GlusterFS.

## Licença e precificação
{: #ibp-icp-about-license-pricing}

O {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private consiste em duas edições, uma oferta paga que é transferível por download a partir do Passport Advantage e uma Community edition gratuita que está disponível no [GitHub ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts").

### Licença
{: #ibp-icp-about-license}

O {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private fornece os componentes necessários para executar uma rede de blockchain em sua própria infraestrutura e é implementado como um aplicativo do {{site.data.keyword.cloud_notm}} Private. É possível acessar o PPA e [fazer download do gráfico Helm](/docs/services/blockchain/howto/helm_install_icp.html#helm-install). O suporte técnico do {{site.data.keyword.blockchainfull_notm}} é incluído na compra.

O {{site.data.keyword.blockchainfull_notm}} Platform for IBM Cloud Private Community Edition é uma oferta gratuita, adequada para a avaliação e a experimentação, que é implementada como um aplicativo do {{site.data.keyword.cloud_notm}} Private. Não use a Community Edition para produção. O {{site.data.keyword.blockchainfull_notm}} Platform não fornece suporte para o Community Edition. É possível acessar o [pacote GitHub ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.2.tgz "IBM/charts") e fazer download do gráfico Helm.

### Precificação
{: #ibp-icp-about-pricing}

A precificação do {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private é baseada no número de Núcleos do Processador Virtual (VPCs) usados. Um VPC pode ser um núcleo virtual que é designado a um servidor virtual ou um núcleo de processador físico em um servidor não particionado. Deve-se obter uma titularidade de licença para cada VPC disponibilizado para o {{site.data.keyword.blockchainfull_notm}} Platform. <!-- A VPC is a unit of measurement by which a program can be licensed.-->

Para obter mais informações sobre como determinar o seu uso de VPCs, veja este artigo sobre ["Virtual Processor Cores (VPC)" ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "Virtual Processor Cores (VPCs)") no {{site.data.keyword.IBM_notm}} Knowledge Center. É possível usar o [{{site.data.keyword.IBM_notm}} License Metric Tool](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/welcome/LMT_welcome.html) para configurar e criar um relatório que pode ser usado para determinar o número de VPCs necessários para obter uma licença.


## Instalando o  {{site.data.keyword.blockchainfull_notm}}  Platform for  {{site.data.keyword.cloud_notm}}  Private
{: #ibp-icp-about-install}

O {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private é entregue como um arquivo de gráfico do Helm que pode ser instalado em um cluster local do {{site.data.keyword.cloud_notm}} Private. Depois de instalar o gráfico do Helm, é possível localizar o {{site.data.keyword.blockchainfull_notm}} Platform como um aplicativo no catálogo do {{site.data.keyword.cloud_notm}} Private.

- O {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private é entregue por meio do Passport Advantage. É necessário ter a licença necessária para acessar o [Passport Advantage Online](https://www.ibm.com/software/passportadvantage/pao_customer.html). Mediante a compra, o suporte técnico para o {{site.data.keyword.blockchainfull_notm}} Platform é incluído.

- O {{site.data.keyword.blockchainfull_notm}} Platform Community Edition destina-se à exploração, ao desenvolvimento e aos testes. Essa versão grátis do {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private pode ser acessada por meio do GitHub. **Nota:** o {{site.data.keyword.blockchainfull_notm}} Platform não fornece suporte para a Community Edition.

Para obter instruções sobre como instalar o gráfico do Helm e os pré-requisitos necessários, consulte [Instalando o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

Se você for um novo usuário do {{site.data.keyword.cloud_notm}} Private e desejar informações e dicas sobre como instalar e implementar o {{site.data.keyword.cloud_notm}} Private, veja [Configurando o {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup).

Depois de instalar o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private, é necessário implementar cada componente de sua rede individualmente. Não é possível implementar vários componentes ao mesmo tempo. Consulte a [Introdução ao {site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#get-started-icp) para aprender as melhores práticas para construir ou associar-se a uma rede de blockchain. Em seguida, revise as etapas para implementar e operar os componentes individuais das seções a seguir.

### Instalando o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private atrás de um firewall
{: #ibp-icp-about-firewall}

É possível implementar os componentes do {{site.data.keyword.blockchainfull_notm}} Platform atrás de um firewall, sem ter acesso à Internet pública. O pacote de gráficos Helm transferidos por download inclui todas as imagens do Docker do componente Fabric que o {{site.data.keyword.blockchainfull_notm}} Platform usa, sem puxá-los do DockerHub durante a implementação.

O pacote de gráficos Helm do {{site.data.keyword.blockchainfull_notm}} Platform Community Edition não inclui as imagens do Docker do componente Fabric necessárias. Ele é configurado para fazer download dessas imagens do DockerHub durante a implementação e falhará sem ter acesso à Internet pública. No entanto, é possível executar algumas etapas adicionais para implementar os componentes da Community Edition atrás de um firewall. Para obter mais informações, consulte [Instalando o Community Edition atrás de um firewall](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-prereqs-firewall).


## Sobre autoridades de certificação no {{site.data.keyword.cloud_notm}} Private
{: #ibp-icp-about-ca}

As autoridades de certificação (CAs) fornecem identidade na rede. Uma CA pode ser considerada semelhante a um tabelião público que atua como âncora de confiança entre várias partes. Todas as entidades na rede recebem um certificado assinado por uma autoridade de certificação raiz que encapsula sua identidade digital. Esse certificado é a raiz de confiança para todas as operações de assinatura e verificação executadas na rede. Para obter mais informações sobre autoridades de certificação e a função que elas desempenham em uma rede de blockchain, consulte [Visão geral do componente do Blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview).

A CA validará a identidade e emitirá certificados para os outros componentes em sua rede que você precisa implementar. Como resultado, é necessário implementar uma CA para sua organização antes de implementar outros componentes.

- Para saber como configurar e implementar uma CA após instalar o gráfico Helm, consulte [Implementando uma autoridade de certificação no {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy).

- Para saber como usar a CA para gerar certificados e concluir as etapas de pré-requisito para implementar componentes adicionais, veja [Operando uma autoridade de certificação no {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_operate.html#ca-operate).

## Sobre os orderers no  {{site.data.keyword.cloud_notm}}  Privado
{: #ibp-icp-about-orderer}

Os solicitadores autenticam os clientes, as transações de pedidos e as transações de transmissão em uma rede de blockchain. Eles são a ligação comum das redes de blockchain com base no Hyperledger Fabric. Como resultado, a organização que constrói uma rede precisa implementar e iniciar um "serviço de solicitação" (o nó ou a coleção de nós que fazem a solicitação) antes que outras organizações possam implementar seus peers, canais de junção e iniciar transações nela. Para obter mais informações sobre os solicitadores e a função que eles desempenham em uma rede de blockchain, consulte [Visão geral do componente do Blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-orderer).

Se estiver construindo uma rede de blockchain, será necessário implementar um solicitador. Depois que ele tiver sido implementado, será possível convidar outras organizações para o seu consórcio, que poderão, então, criar canais próprios.

- Para saber como configurar e implementar um solicitador após a instalação do gráfico do Helm, veja [Implementando um solicitador no {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy).

- Para saber como construir um consórcio, veja [Operando um solicitador no {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate).

## Sobre peers no  {{site.data.keyword.cloud_notm}}  Privado
{: #ibp-icp-about-peer}

Os peers são um elemento fundamental da rede porque eles hospedam livros-razão e contratos inteligentes. Contratos inteligentes e livros-razão são usados para encapsular os processos compartilhados e informações compartilhadas em uma rede, respectivamente. Para obter mais informações sobre os peers e a função que eles desempenham em uma rede de blockchain, consulte [Visão geral do componente do Blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview).

- Quando você estiver pronto para ingressar em uma rede, será possível implementar um peer que associará canais, endossará transações e armazenará livros-razão do canal. Para obter informações sobre como implementar um peer no {{site.data.keyword.cloud_notm}} Private que se conectará a outros componentes no {{site.data.keyword.cloud_notm}} Private, veja [Implementando um peer no {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy). Para obter informações sobre a implementação de um peer no {{site.data.keyword.cloud_notm}} Private que se conectará a uma rede do Starter ou Enterprise Plan, veja [Implementando um peer que se conectará ao Starter ou Enterprise](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy).

- Depois de configurar o peer, é necessário concluir várias etapas operacionais antes de poder enviar transações e ler o livro-razão da rede de blockchain.

  - Se estiver conectando seu peer a um {{site.data.keyword.blockchainfull_notm}} Platform implementado no {{site.data.keyword.cloud_notm}} Private, consulte [Operando peers no {{site.data.keyword.cloud_notm}} Private com uma Rede Multicloud](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate).
  - Se você estiver conectando seu peer a uma rede Starter Plan ou Enterprise Plan implementada no {{site.data.keyword.cloud_notm}}, consulte [Peers operacionais no {{site.data.keyword.cloud_notm}} Private com Starter Plan ou Enterprise Plan](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate).

## Considerações de segurança
{: #ibp-icp-about-security}

Como esses componentes são implementados fora do {{site.data.keyword.cloud_notm}}, você é responsável por gerenciar sua segurança. Isso inclui áreas importantes de segurança, como gerenciamento de chaves e criptografia de dados. Revise os tópicos a seguir ao considerar a segurança de seus componentes.

### Segurança de dados
{: #ibp-icp-about-security-data}

Os planos Starter e Enterprise do {{site.data.keyword.blockchainfull_notm}} Platform usam criptografia de disco inteiro que é baseada na [Criptografia de chave simétrica ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Criptografia simétrica") para proteger todos os dados que as redes usam. Deve-se executar etapas semelhantes em seu próprio ambiente para proteger seus dados do peer.

Os dados em seu banco de dados de estado, se você usa LevelDB ou CouchDB, não são criptografados. É possível usar a criptografia de nível do aplicativo para proteger os dados em repouso em seu banco de dados de estado.

### Residência de dados
{: #ibp-icp-about-security-data-residency}

Os requisitos de residência de dados podem exigir que o processamento e armazenamento de todos os dados do livro-razão do blockchain permaneçam dentro da fronteira de um único país (ou dentro de algum outro limite definido). Para obter mais informações sobre como a residência de dados pode ser realizada, consulte [Residência de dados](#ibp-icp-about-data-residency).

### Gerenciamento de chave
{: #ibp-icp-about-security-key-management}

O gerenciamento de chave é um aspecto crítico da segurança. Se uma chave privada estiver comprometida ou perdida, agentes hostis poderão acessar seus dados e sua funcionalidade. A IBM usa dispositivos físicos chamados [Módulos de Segurança de Hardware](/docs/services/blockchain/glossary.html#glossary-hsm) (HSM) para armazenar as chaves privadas das redes Enterprise Plan do {{site.data.keyword.blockchainfull_notm}} Platform.

Quando você implementa um componente no {{site.data.keyword.cloud_notm}} Private, você é responsável por gerenciar suas chaves privadas. Embora o {{site.data.keyword.blockchainfull_notm}} Platform gere chaves privadas, essas chaves não são armazenadas no Platform. É essencial armazenar suas chaves com segurança para que elas não sejam comprometidas. É possível localizar a chave privada de seu componente na pasta de keystore do MSP de seu peer, no diretório `/mnt/crypto/peer/peer/msp/keystore/` dentro de seu componente. Para obter mais informações sobre os certificados dentro de seu peer, consulte a seção [Membership Services Provider](/docs/services/blockchain/certificates.html#managing-certificates-msp) do tutorial [Gerenciando certificados no {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates).

É possível usar o Key Escrow para recuperar chaves privadas perdidas. Isso precisa ser executado antes da perda de qualquer chave. Se uma chave privada não puder ser recuperada, será necessário obter novas chaves privadas registrando uma nova identidade com sua Autoridade de certificação. Também será necessário remover e substituir o seu signCert de qualquer canal ao qual tenha se associado.

### TLS
{: #ibp-icp-about-security-tls}

[A Segurança da Camada de Transporte ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "Uma visão geral do handshake SSL ou TLS") (TLS) é integrada no modelo de confiança do Hyperledger Fabric. Todos os componentes Starter e Enterprise no {{site.data.keyword.blockchainfull_notm}} Platform usam TLS para se autenticar e se comunicar uns com os outros. Portanto, os componentes de rede no Platform precisam ser capazes de concluir um handshake TLS com seus peers. Uma implicação disso é que você precisa ativar o intermediário, usando a lista de aplicativos confiáveis, por exemplo, em seu firewall de aplicativos clientes para seu peer.

### Configuração do Provedor de Serviços de Associação
{: #ibp-icp-about-security-MSP}

Os componentes do {{site.data.keyword.blockchainfull_notm}} Platform consomem identidades via Membership Service Providers (MSPs). MSPs associam os certificados que as autoridades de certificação emitem com as funções de rede e canal. Para obter mais informações sobre como os MSPs trabalham com o peer, consulte [Membership Services Provides (MSPs)](/docs/services/blockchain/certificates.html#managing-certificates-msp).

### Segurança do aplicativo
{: #ibp-icp-about-security-appl}

Como todas as chamadas de chaincode são assinadas, o Fabric gerencia a segurança do aplicativo. Além disso, o Fabric também inclui verificações de nível de aplicativo baseadas na ACL.

## Residência de dados
{: #ibp-icp-about-data-residency}

Como as redes de blockchain são indiferentes para o tipo de dados que são processados, etapas extras devem ser tomadas algumas vezes para manter determinados tipos de dados seguros. O requisito mais comum sobre residência de dados está associado às leis dentro de certos países, que exigem que todos os dados que são processados e armazenados num sistema de TI devem permanecer dentro das fronteiras de um país específico. Da mesma forma, algumas empresas em indústrias altamente regulamentadas, como governo, assistência médica e serviços financeiros, requerem que os dados sejam armazenados inteiramente atrás de seu firewall. Portanto, para alcançar a residência de dados, todos os componentes da rede de blockchain devem fazer parte do mesmo [canal](/docs/services/blockchain/glossary.html#glossary-channel) e residirem na fronteira de um único país.

Para tratar dos requisitos de residência de dados, é importante entender a arquitetura do Hyperledger Fabric subjacente ao {{site.data.keyword.blockchainfull_notm}} Platform. A arquitetura é centrada em torno de três componentes-chave: um serviço de ordenação (composto de solicitadores), Autoridades de Certificação (CA) e peers. Um peer recebe atualizações de estado pedidas no formato de blocos do serviço de ordenação e mantém o estado e o livro-razão. Portanto, um peer e um serviço de ordenação têm um relacionamento direto. O livro-razão contém os valores mais recentes para todas as chaves e os dados que os logs de transações incluem.

Além disso, os aplicativos clientes usam os [SDKs do Fabric](/docs/services/blockchain/v10_application.html#dev-app-fabric-sdks) para enviar transações para os peers e o serviço de ordenação. Essas transações incluem dados [Conjunto de leitura/gravação ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/readwrite.html "Semântica do conjunto de leitura/gravação"), que contém os pares de chave-valor no livro-razão.

Se a residência de dados no país for um requisito, o solicitador, os peers e os aplicativos clientes deverão residir no mesmo país. Quando uma rede do {{site.data.keyword.blockchainfull_notm}} Platform é criada no {{site.data.keyword.cloud_notm}}, você tem a opção de selecionar um local para a rede. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->Para obter mais informações sobre regiões e locais, veja [Regiões e locais do {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/reference/ibp_regions.html#ibp-regions-locations).

### Um caso de uso para residência de dados

Considere uma rede do {{site.data.keyword.blockchainfull_notm}} Platform que inclua o solicitador e a CA juntamente com um consórcio de quatro organizações. As organizações possuem um ou mais nós de peer e todas as quatro organizações fazem parte de um único canal e todos os componentes da rede residem na região (por exemplo, Frankfurt), em que a rede do {{site.data.keyword.blockchainfull_notm}} Platform foi implementada. Por último, os aplicativos clientes que interagem com os peers também residem na Alemanha.

Nesse exemplo, a residência de dados é mantida.

![Residência de dados quando todos os componentes estão no mesmo país](images/remote_peer_data_res_1.png "Residência de dados quando todos os componentes estão no mesmo país")
*Figura 1. Residência de dados quando todos os componentes estão no mesmo país*

Agora, vamos considerar as implicações quando um **peer distribuído** ingressa em uma das organizações. Um peer distribuído pode residir na mesma região que o restante da rede ou em qualquer lugar fora da região da rede do {{site.data.keyword.blockchainfull_notm}} Platform:

-	Se o peer residir no mesmo país que o restante da rede, a residência de dados será mantida. Todos os dados do livro-razão permanecem dentro da Alemanha como na **Figura 1** acima.
-	Se o peer residir em um país diferente (como os EUA, por exemplo), a residência de dados não será mais mantida porque os dados no livro-razão de peer são compartilhados fora da fronteira do país.

Para resolver esse problema, os **canais** podem ser usados para segregar dados para um subconjunto de peers na rede. Quando a rede do {{site.data.keyword.blockchainfull_notm}} Platform contém um peer distribuído e solicitadores além das fronteiras do país, os canais fornecem isolamento de dados do livro-razão das organizações com peers fora da fronteira do país.

**Nota:** os solicitadores estão sempre localizados na região do data center selecionada para hospedar a rede. Não é possível ter múltiplos solicitadores entre as fronteiras do país. Os peers, no entanto, podem estar localizados no data center ou em um local remoto fora do {{site.data.keyword.cloud_notm}}.

![Residência de dados quando os peers estão fora do país da região do {{site.data.keyword.blockchainfull_notm}} Platform](images/remote_peer_data_res_2.png "Residência de dados quando os peers estão fora do país da região do {{site.data.keyword.blockchainfull_notm}} Platform")
*Figura 2. Residência de dados quando os peers estão fora do país da região do {{site.data.keyword.blockchainfull_notm}} Platform*

Na **Figura 2**, a residência de dados não é requerida para `OrgC` e `OrgD`. Na verdade, o `OrgD` agora inclui dois peers distribuídos, `OrgD-peer1` e `OrgD-peer2`, que residem nos *Estados Unidos*. Portanto, para que `OrgA`, `OrgB` e seus respectivos aplicativos clientes e peers que residem na Alemanha isolem os dados do livro-razão no canal `X`, um novo canal `Y` é criado para `OrgC` e `OrgD`.

Para um entendimento mais profundo do fluxo de dados na rede do {{site.data.keyword.blockchainfull_notm}} Platform, consulte a [documentação do Fabric no fluxo de transação![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "Fluxo de transação").

No futuro, a nova tecnologia no Hyperledger Fabric melhorará a capacidade de obtenção de mais residência de dados usando [Coletas de dados privados ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html "Coletas de dados privados") e prova de conhecimento zero.

- Uma coleta de dados privados assegura que os dados privados sejam compartilhados ponto a ponto (por meio do protocolo gossip) apenas para os peers autorizados a vê-los, por exemplo, peers que estão dentro das fronteiras do país. Os dados são armazenados em um banco de dados privado no peer. O serviço de ordenação não está envolvido aqui e não vê os dados privados. Um hash desses dados é gravado nos livros-razão de cada peer no canal. O hash que é usado para validação de estado serve como evidência da transação e pode ser usado para propósitos de auditoria. Os dados privados estão disponíveis para redes no {{site.data.keyword.blockchainfull_notm}} Platform que estão em execução no Fabric versão 1.2.1 ou mais recente. No entanto, o recurso de dados privados não está disponível para os peers em execução no {{site.data.keyword.cloud_notm}} Private, pois as redes do {{site.data.keyword.cloud_notm}} Private não usam atualmente o gossip.

- Uma prova de conhecimento zero (ZKP) permite que um “comprovador” assegure a um “verificador” que ele conhece um segredo sem revelar o próprio segredo.

É possível obter mais informações sobre essas tecnologias no White Paper sobre [Transações privadas e confidenciadas com o Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://developer.ibm.com/tutorials/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/ "Transações privadas e confidenciais com o Hyperledger Fabric").

## Obtendo Suporte
{: #ibp-icp-about-support}

Para obter informações sobre ofertas de suporte digital, consulte os [recursos de suporte e fóruns de suporte](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-resources) do {{site.data.keyword.blockchainfull_notm}} Platform.

### {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private

Se você comprou uma licença do {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private e deseja entrar em contato com o Suporte ao cliente, veja as informações sobre [como acessar a Comunidade do suporte IBM e abrir um chamado de suporte ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www-01.ibm.com/support/docview.wss?uid=ibm10740041 "Suporte do {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private").

### {{site.data.keyword.blockchainfull_notm}} Platform Community Edition

A Community Edition é destinado à exploração, ao desenvolvimento e ao teste. O {{site.data.keyword.blockchainfull_notm}} Platform não fornece suporte para o {{site.data.keyword.blockchainfull_notm}} Platform Community Edition.

Se você encontrar problemas relacionados aos seus componentes blockchain, será possível usar recursos de desenvolvedor de blockchain gratuitos e fóruns de suporte e obter ajuda da IBM e da comunidade do Fabric. Para obter mais informações, consulte [recursos de blockchain e fóruns de suporte](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-resources).

Para problemas que estão relacionados ao IBM Cloud Private, é possível aproveitar o [suporte digital grátis e o suporte pago que o {{site.data.keyword.cloud_notm}} Private oferece](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us).
