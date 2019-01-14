---

copyright:
  years: 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Sobre o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private
{: #overview}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

O {{site.data.keyword.blockchainfull}} Platform libera o IBM Blockchain Platform for {{site.data.keyword.cloud_notm}} Private (ICP), uma plataforma de aplicativo para desenvolver e gerenciar aplicativos conteinerizados baseados em Kubernetes que permite que os usuários implementem Autoridades de Certificação (CAs), solicitadores, peers no x86, LinuxONE e IBM Z. O {{site.data.keyword.blockchainfull_notm}} Platform for ICP é baseado no Hyperledger Fabric v1.2.1 e é implementado usando os gráficos Helm do Kubernetes.
{:shortdesc}

O {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private é um produto empacotado para clientes ICP para implementar componentes de blockchain em seu ambiente local. Depois de importar o gráfico Helm, é possível localizá-lo como um tile do {{site.data.keyword.blockchainfull_notm}} Platform no Catálogo de ICP. Para obter mais informações sobre o ICP, consulte a documentação para o [{{site.data.keyword.cloud_notm}} Private versão 3.1.0 ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 3.1.0").

## O que o {{site.data.keyword.blockchainfull_notm}} Platform for ICP oferece

O {{site.data.keyword.blockchainfull_notm}} Platform for IBM Cloud Private (ICP) permite que você implemente todos os componentes fundamentais de um blockchain do Hyperledger Fabric: uma Autoridade de Certificação, um serviço de ordenação e os peers. Ele fornece a flexibilidade para implementar diferentes componentes, dependendo de suas necessidades de negócios. É possível usar o {{site.data.keyword.blockchainfull_notm}} for ICP para iniciar uma rede de blockchain implementando e configurando um serviço de ordenação que liga as organizações em um consórcio de blockchain. Também é possível implementar os peers e juntar outras redes que usam componentes com base no Fabric, incluindo redes do {{site.data.keyword.blockchainfull_notm}} Platform implementadas em nuvens usando ICP ou redes Starter Plan e Enterprise Plan hospedadas no IBM Cloud. Para obter mais informações sobre os blocos de construção de redes do Hyperledger Fabric, consulte a [visão geral do componente de blockchain](blockchain_component_overview.html).

## O {{site.data.keyword.blockchainfull_notm}} Platform for ICP é adequado para você?

A execução dos componentes do {{site.data.keyword.blockchainfull_notm}} Platform fora do {{site.data.keyword.cloud_notm}} fornece mais flexibilidade para aumentar ou ingressar em uma rede de blockchain. Ele ajuda os fundadores de rede a aumentarem suas redes permitindo que novos membros se associem ao mesmo tempo que usam a plataforma de sua escolha. Ele permitirá que as organizações que estão interessadas em se associar às redes de blockchain coloquem seus peers com seus aplicativos existentes e/ou integrem com seus sistemas de registro.

**Importante:** o processo de implementação do {{site.data.keyword.blockchainfull_notm}} Platform for ICP é complicado e presume um alto grau de conhecimento no Fabric. Se você for novo no Fabric, ICP ou {{site.data.keyword.blockchainfull_notm}} Platform e seu objetivo for configurar um ambiente de desenvolvimento ou prova de conceito, considere usar o [Starter Plan](starter_plan.html). Observe também que nem toda configuração de implementação potencial é suportada no {{site.data.keyword.blockchainfull_notm}} Platform for ICP.

Os usuários dessa oferta gerenciarão sua própria segurança e infraestrutura. O {{site.data.keyword.cloud_notm}} não fornece esses serviços. Revise as [Considerações e limitações](#ibp-icp-considerations) na próxima seção antes de iniciar.

## Considerações e limitações
{: #ibp-icp-considerations}

Antes de iniciar, assegure-se de entender as **considerações** e **limitações** a seguir:

- Você é responsável pelo monitoramento de funcionamento, pela segurança, pela criação de log e pelo gerenciamento do uso de recurso de seus componentes.
- Componentes que são executados em outros ambientes de nuvem não são visíveis no Monitor de Rede das redes em execução no {{site.data.keyword.cloud_notm}}.
- Os gráficos Helm implementam uma única instância de um solicitador, de um peer ou da CA.
- É possível implementar vários componentes em um único namespace no ICP, contanto que eles tenham nomes de liberação diferentes.
- Os componentes não podem ser endereçados usando a IU do Swagger na IU do Monitor de Rede.
- TLS mútuo não é suportado.

**Considerações de CA**
- Esse gráfico Helm implementa uma única instância da CA. Como é considerado uma melhor prática ter uma autoridade de certificação separada para cada organização, pode ser necessário implementar várias autoridades de certificação. Por exemplo, se você planeja implementar um solicitador e três peers, precisará de pelo menos duas autoridades de certificação (uma para a organização do solicitador e outra para a organização de peer).
- Embora você possa optar por executar um banco de dados MySQL separado, essa opção não está presente no gráfico Helm. O gráfico Helm, no entanto, implementará um banco de dados SQLite dentro da CA para manipular as necessidades do banco de dados da CA, que incluem o rastreamento do número de inscrições por usuário e quaisquer certificados revogados.

**Considerações do solicitador**
- O serviço de ordenação é compatível com qualquer componente na v1.2 do Hyperledger Fabric.
- Este gráfico Helm implementa uma única instância do serviço de ordenação SOLO (um solicitador). Observe que não é possível implementar mais de um solicitador SOLO em um canal para tornar o serviço de ordenação altamente disponível. Essa é uma razão pela qual os serviços de ordenação SOLO são considerados para ambientes de desenvolvimento, em vez de produção. No entanto, é possível implementar diversas instâncias do serviço de ordenação SOLO em redes diferentes (ou seja, com um consórcio separado).

**Considerações sobre peer**

- É possível conectar seus peers apenas às redes de blockchain que estão no nível do Fabric v1.1 ou v1.2.1. É possível localizar a versão do Hyperledger Fabric abrindo a [janela Preferências de rede](../v10_dashboard.html#network-preferences) em seu Network Monitor. Siga as [instruções](#starter-enterprise-network-endpoints) para recuperar as informações de conexão de peer das redes Starter ou Enterprise.
- O tipo de banco de dados do peer deve corresponder ao tipo de banco de dados de sua rede de blockchain, LevelDB ou CouchDB.
- A interface do CouchDB Fauxton não está disponível no peer.
- [Gossip](../glossary.html#gossip) para peers não é suportado atualmente. Isso significa que os recursos do Fabric que dependem do gossip, como [dados privados ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "dados privados") e [descoberta de serviço ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "descoberta de serviço") também não são suportados.

## Pré-requisitos do sistema
{: #prerequisites}

O {{site.data.keyword.blockchainfull_notm}} Platform for ICP suporta os sistemas operacionais a seguir:
- Red Hat Enterprise Linux (RHEL) 7.3, 7.4, 7.5
- Ubuntu 18.04 LTS e 16.04 LTS
- SUSE Linux Enterprise Server (SLES) 12 SP3

O gráfico Helm do {{site.data.keyword.blockchainfull_notm}} Platform for ICP foi validado para ser executado nos clusters do {{site.data.keyword.cloud_notm}} Private v3.1.0 no Ubuntu Linux, usando os nós do trabalhador e o armazenamento auxiliar a seguir:

- **LinuxONE e IBM Z**: z/VM e KVM, que estão usando NFS.
- **x86**: Linux de 64 bits que usa GlusterFS.

## Licença e precificação
{: #ibp-icp-license-pricing}
O {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private consiste em duas edições, uma oferta paga que é transferível por download a partir do Passport Advantage e uma Community edition gratuita que está disponível no [GitHub ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts").

### Licença
{: #ibp-icp-license}

O {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.cloud_notm}} Private fornece os componentes necessários para executar uma rede de blockchain em sua própria infraestrutura e é implementado como um aplicativo ICP. É possível acessar o PPA e [fazer download do gráfico Helm](howto/helm_install_icp.html). O suporte técnico do {{site.data.keyword.blockchainfull_notm}} é incluído na compra.

O {{site.data.keyword.blockchainfull_notm}} Platform no IBM Cloud Private Community Edition é uma oferta gratuita que é adequada para avaliação e experimentação e é implementada como um aplicativo ICP. Não use a Community Edition para produção. O {{site.data.keyword.blockchainfull_notm}} Platform não fornece suporte para o Community Edition. É possível acessar o [pacote GitHub ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz "IBM/charts") e fazer download do gráfico Helm.

### Precificação
{: #ibp-icp-pricing}

A precificação do {{site.data.keyword.blockchainfull_notm}} Platform no ICP é baseada no número de Núcleos do Processador Virtual (VPCs) usados. Um VPC pode ser um núcleo virtual que é designado a um servidor virtual ou um núcleo de processador físico em um servidor não particionado. Deve-se obter uma titularidade de licença para cada VPC disponibilizado para o {{site.data.keyword.blockchainfull_notm}} Platform. <!-- A VPC is a unit of measurement by which a program can be licensed.-->

Para obter mais informações sobre como determinar seu uso de VPCs, consulte este artigo sobre ["Núcleos de Processador Virtual (VPCs)" ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "Núcleos de Processador Virtual (VPC's)") no IBM Knowledge Center. É possível usar o [IBM License Metric Tool](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/welcome/LMT_welcome.html) para configurar e criar um relatório que possa ser usado para determinar o número de VPCs que você precisa licenciar.


## Instalando o {{site.data.keyword.blockchainfull_notm}} Platform for ICP
{: #ibp-icp-install}

O {{site.data.keyword.blockchainfull_notm}} Plataforma no {{site.data.keyword.cloud_notm}} Private é entregue como um arquivo de gráfico Helm que pode ser instalado em um Cluster ICP local. Depois de instalar o gráfico do Helm, é possível localizar o {{site.data.keyword.blockchainfull_notm}} Platform como um aplicativo no catálogo do ICP.

- O {{site.data.keyword.blockchainfull_notm}} Plataforma for ICP é entregue por meio do Passport Advantage. É necessário ter a licença necessária para acessar o [Passport Advantage Online](https://www.ibm.com/software/passportadvantage/pao_customer.html). Mediante a compra, o suporte técnico para o {{site.data.keyword.blockchainfull_notm}} Platform é incluído.

- O {{site.data.keyword.blockchainfull_notm}} Platform Community Edition destina-se à exploração, ao desenvolvimento e aos testes. Essa versão gratuita do IBM Blockchain Platform for ICP pode ser acessada por meio do GitHub. **Nota:** o {{site.data.keyword.blockchainfull_notm}} Platform não fornece suporte para a Community Edition.

Para obter instruções sobre como instalar o gráfico Helm e os pré-requisitos necessários, consulte [Instalando o {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.cloud_notm}} Private](howto/helm_install_icp.html).

Se você é um novo usuário do {{site.data.keyword.cloud_notm}} Private e deseja informações e dicas sobre como instalar e implementar o ICP, veja [Configurando o {{site.data.keyword.cloud_notm}} Private](ICP_setup.html).

Depois de instalar o {{site.data.keyword.blockchainfull_notm}} Platform for ICP, é necessário implementar cada componente de sua rede individualmente. Não é possível implementar vários componentes ao mesmo tempo. Visite o [Guia de implementação do {{site.data.keyword.blockchainfull_notm}} Platform for ICP](ibp_for_icp_deployment_guide.html) para aprender as melhores práticas para descobrir ou associar uma rede de blockchain antes de iniciar. Em seguida, revise as etapas para implementar e operar os componentes individuais das seções a seguir.

### Instalando o {{site.data.keyword.blockchainfull_notm}} Platform for ICP atrás de um firewall
{: #ibp-icp-firewall}

É possível implementar os componentes do {{site.data.keyword.blockchainfull_notm}} Platform atrás de um firewall, sem ter acesso à Internet pública. O pacote de gráficos Helm transferidos por download inclui todas as imagens do Docker do componente Fabric que o {{site.data.keyword.blockchainfull_notm}} Platform usa, sem puxá-los do DockerHub durante a implementação.

O pacote de gráficos Helm do {{site.data.keyword.blockchainfull_notm}} Platform Community Edition não inclui as imagens do Docker do componente Fabric necessárias. Ele é configurado para fazer download dessas imagens a partir do DockerHub durante a implementação e falhará sem ter acesso à Internet pública. No entanto, é possível executar algumas etapas adicionais para implementar os componentes da Community Edition atrás de um firewall. Para obter mais informações, consulte [Instalando a Community Edition atrás de um firewall](howto/helm_install_icp.html#helm-install-prereqs-firewall)


## Sobre autoridades de certificação no ICP
{: #ibp-icp-ca}

As autoridades de certificação (CAs) fornecem identidade na rede. Uma CA pode ser considerada semelhante a um tabelião público que atua como âncora de confiança entre várias partes. Todas as entidades na rede recebem um certificado assinado por uma autoridade de certificação raiz que encapsula sua identidade digital. Esse certificado é a raiz de confiança para todas as operações de assinatura e verificação executadas na rede. Para obter mais informações sobre autoridades de certificação e a função que elas desempenham em uma rede de blockchain, consulte [Visão geral do componente do Blockchain](blockchain_component_overview.html).

A CA validará a identidade e emitirá certificados para os outros componentes em sua rede que você precisa implementar. Como resultado, é necessário implementar uma CA para sua organização antes de implementar outros componentes.

- Para saber como configurar e implementar uma CA após instalar o gráfico Helm, consulte [Implementando uma autoridade de certificação no {{site.data.keyword.cloud_notm}} Private](howto/CA_deploy_icp.html).

- Para saber como usar a CA para gerar certificados e concluir as etapas de pré-requisito para implementar componentes adicionais, veja [Operando uma autoridade de certificação no {{site.data.keyword.cloud_notm}} Private](howto/CA_operate.html).

## Sobre solicitadores no ICP
{: #ibp-icp-orderer}

Os solicitadores autenticam os clientes, as transações de pedidos e as transações de transmissão em uma rede de blockchain. Eles são a ligação comum das redes de blockchain com base no Hyperledger Fabric. Como resultado, a organização que funda uma rede precisa implementar e iniciar um "serviço de ordenação" (o nó ou coleção de nós que fazem a ordenação) antes que outras organizações possam implementar seus peers, associar-se a canais e começar a transacionar na rede. Para obter mais informações sobre os solicitadores e a função que eles desempenham em uma rede de blockchain, consulte [Visão geral do componente do Blockchain](blockchain_component_overview.html#orderer).

Se você estiver fundando uma rede de blockchain, precisará implementar um solicitador. Depois que ele tiver sido implementado, será possível convidar outras organizações para o seu consórcio, que poderão, então, criar canais próprios.

- Para saber como configurar e implementar um solicitador depois de instalar o gráfico Helm, consulte [Implementando um solicitador no ICP](howto/orderer_deploy_icp.html).

- Para aprender como construir um consórcio, consulte [Operando um solicitador no ICP](howto/orderer_operate.html).

## Sobre peers no ICP
{: #ibp-icp-peer}

Os peers são um elemento fundamental da rede porque eles hospedam livros-razão e contratos inteligentes. Contratos inteligentes e livros-razão são usados para encapsular os processos compartilhados e informações compartilhadas em uma rede, respectivamente. Para obter mais informações sobre os peers e a função que eles desempenham em uma rede de blockchain, consulte [Visão geral do componente do Blockchain](blockchain_component_overview.html).

- Quando você estiver pronto para ingressar em uma rede, será possível implementar um peer que associará canais, endossará transações e armazenará livros-razão do canal. Para obter informações sobre a implementação de um peer no ICP que se conectará a outros componentes no ICP, consulte [Implementando um peer no ICP](howto/peer_deploy_icp.html). Para obter informações sobre como implementar um peer no ICP que se conectará a uma rede do Starter ou do Enterprise Plan, consulte [Implementando um peer que se conectará ao Starter ou Enterprise](howto/peer_deploy_ibp.html).

- Depois de configurar o peer, é necessário concluir várias etapas operacionais antes de poder enviar transações e ler o livro-razão da rede de blockchain.

  - Se você estiver conectando seu peer a um {{site.data.keyword.blockchainfull_notm}} Platform implementado no ICP, veja [Operando peers no {{site.data.keyword.cloud_notm}} Private com uma rede multi-cloud](howto/peer_operate_icp.html).
  - Se você estiver conectando seu peer a uma rede Starter Plan ou Enterprise Plan implementada no {{site.data.keyword.cloud_notm}}, consulte [Peers operacionais no {{site.data.keyword.cloud_notm}} Private com Starter Plan ou Enterprise Plan](howto/peer_operate_ibp.html).

## Considerações de segurança
{: #icp-security}

Como esses componentes são implementados fora do {{site.data.keyword.cloud_notm}}, você é responsável por gerenciar sua segurança. Isso inclui áreas importantes de segurança, como gerenciamento de chaves e criptografia de dados. Revise os tópicos a seguir ao considerar a segurança de seus componentes.

### Segurança de dados
{: #icp-security-data}

Os planos Starter e Enterprise do {{site.data.keyword.blockchainfull_notm}} Platform usam criptografia de disco inteiro que é baseada na [Criptografia de chave simétrica ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Criptografia simétrica") para proteger todos os dados que as redes usam. Deve-se executar etapas semelhantes em seu próprio ambiente para proteger seus dados do peer.

Os dados em seu banco de dados de estado, se você usa LevelDB ou CouchDB, não são criptografados. É possível usar a criptografia de nível do aplicativo para proteger os dados em repouso em seu banco de dados de estado.

### Residência de dados
{: #data-residency}

Os requisitos de residência de dados podem exigir que o processamento e armazenamento de todos os dados do livro-razão do blockchain permaneçam dentro da fronteira de um único país (ou dentro de algum outro limite definido). Para obter mais informações sobre como a residência de dados pode ser realizada, consulte [Residência de dados](#icp-data-residency).

### Gerenciamento de chave
{: #icp-security-key-management}

O gerenciamento de chave é um aspecto crítico da segurança. Se uma chave privada estiver comprometida ou perdida, agentes hostis poderão acessar seus dados e sua funcionalidade. A IBM usa dispositivos físicos chamados [Módulos de Segurança de Hardware](../glossary.html#hsm) (HSM) para armazenar as chaves privadas das redes Enterprise Plan do {{site.data.keyword.blockchainfull_notm}} Platform.

Ao implementar um componente no ICP, você é responsável por gerenciar suas chaves privadas. Embora o {{site.data.keyword.blockchainfull_notm}} Platform gere chaves privadas, essas chaves não são armazenadas no Platform. É essencial armazenar suas chaves com segurança para que elas não sejam comprometidas. É possível localizar a chave privada de seu componente na pasta de keystore do MSP de seu peer, no diretório `/mnt/crypto/peer/peer/msp/keystore/` dentro de seu componente. Para obter mais informações sobre os certificados dentro de seu peer, consulte a seção [Membership Services Provider](../certificates.html#msp) do tutorial [Gerenciando certificados no {{site.data.keyword.blockchainfull_notm}} Platform](../certificates.html).

É possível usar o Key Escrow para recuperar chaves privadas perdidas. Isso precisa ser executado antes da perda de qualquer chave. Se uma chave privada não puder ser recuperada, será necessário obter novas chaves privadas registrando uma nova identidade com sua Autoridade de certificação. Também será necessário remover e substituir o seu signCert de qualquer canal ao qual tenha se associado.

### TLS
{: #icp-security-tls}

[A Segurança da Camada de Transporte ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "Uma visão geral do handshake SSL ou TLS") (TLS) é integrada no modelo de confiança do Hyperledger Fabric. Todos os componentes Starter e Enterprise no {{site.data.keyword.blockchainfull_notm}} Platform usam TLS para se autenticar e se comunicar uns com os outros. Portanto, os componentes de rede no Platform precisam ser capazes de concluir um handshake TLS com seus peers. Uma implicação disso é que você precisa ativar o intermediário, usando a lista de aplicativos confiáveis, por exemplo, em seu firewall de aplicativos clientes para seu peer.

<!--
You can use Mutual TLS, which requires two way (server-client) rather than one way (server only) authentication, to secure the communication between your application and Enterprise Plan networks. You can use the Network Monitor [to enable mutual TLS](../v10_dashboard.html#mutual-tls) for peers on {{site.data.keyword.blockchainfull_notm}} Platform. To enable Mutual TLS on your peer, follow the instructions to [enable mutual-TLS for peer nodes ![External link icon](images/external_link.svg "External link icon")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/enable_tls.html "Securing Communication with Transport Layer Security") in the Hyperledger Fabric documentation. It is strongly recommended that you enable mutual-TLS for your applications.
-->

### Configuração do Provedor de Serviços de Associação
{: #icp-security-MSP}

Os componentes do {{site.data.keyword.blockchainfull_notm}} Platform consomem identidades via Membership Service Providers (MSPs). MSPs associam os certificados que as autoridades de certificação emitem com as funções de rede e canal. Para obter mais informações sobre como os MSPs trabalham com o peer, consulte [Membership Services Provides (MSPs)](../certificates.html#msp).

### Segurança do aplicativo
{: #icp-security-appl}

Como todas as chamadas de chaincode são assinadas, o Fabric gerencia a segurança do aplicativo. Além disso, o Fabric também inclui verificações de nível de aplicativo baseadas na ACL.

## Residência de dados
{: #icp-data-residency}

Como as redes de blockchain são indiferentes para o tipo de dados que são processados, etapas extras devem ser tomadas algumas vezes para manter determinados tipos de dados seguros. O requisito mais comum sobre residência de dados está associado às leis dentro de certos países, que exigem que todos os dados que são processados e armazenados num sistema de TI devem permanecer dentro das fronteiras de um país específico. Da mesma forma, algumas empresas em indústrias altamente regulamentadas, como governo, assistência médica e serviços financeiros, requerem que os dados sejam armazenados inteiramente atrás de seu firewall. Portanto, para alcançar a residência de dados, todos os componentes da rede de blockchain devem fazer parte do mesmo [canal](glossary.html#channel) e residirem na fronteira de um único país.

Para tratar dos requisitos de residência de dados, é importante entender a arquitetura do Hyperledger Fabric subjacente ao {{site.data.keyword.blockchainfull_notm}} Platform. A arquitetura é centrada em torno de três componentes-chave: um serviço de ordenação (composto de solicitadores), Autoridades de Certificação (CA) e peers. Um peer recebe atualizações de estado pedidas no formato de blocos do serviço de ordenação e mantém o estado e o livro-razão. Portanto, um peer e um serviço de ordenação têm um relacionamento direto. O livro-razão contém os valores mais recentes para todas as chaves e os dados que os logs de transações incluem.

Além disso, os aplicativos clientes usam os [SDKs do Fabric](v10_application.html#using-the-fabric-sdks) para enviar transações para os peers e o serviço de ordenação. Essas transações incluem dados [Conjunto de leitura/gravação ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/readwrite.html "Semântica do conjunto de leitura/gravação"), que contém os pares de chave-valor no livro-razão.

Se a residência de dados no país for um requisito, o solicitador, os peers e os aplicativos clientes deverão residir no mesmo país. Quando uma rede do {{site.data.keyword.blockchainfull_notm}} Platform é criada no {{site.data.keyword.cloud_notm}}, você tem a opção de selecionar um local para a rede. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->Para obter mais informações sobre regiões e locais, consulte [Regiões e locais do {{site.data.keyword.blockchainfull_notm}} Platform](reference/ibp_regions.html).

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

![Residência de dados quando os peers estão fora do país da região do IBM Blockchain Platform](images/remote_peer_data_res_2.png "Os peers de residência de dados residem fora do país da região do IBM Blockchain Platform")
*Figura 2. Os peers de residência de dados residem fora do país da região do IBM Blockchain Platform*

Na **Figura 2**, a residência de dados não é requerida para `OrgC` e `OrgD`. Na verdade, o `OrgD` agora inclui dois peers distribuídos, `OrgD-peer1` e `OrgD-peer2`, que residem nos *Estados Unidos*. Portanto, para que `OrgA`, `OrgB` e seus respectivos aplicativos clientes e peers que residem na Alemanha isolem os dados do livro-razão no canal `X`, um novo canal `Y` é criado para `OrgC` e `OrgD`.

Para um entendimento mais profundo do fluxo de dados na rede do {{site.data.keyword.blockchainfull_notm}} Platform, consulte a [documentação do Fabric no fluxo de transação![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Fluxo de transação").

No futuro, a nova tecnologia no Hyperledger Fabric melhorará a capacidade de obtenção de mais residência de dados usando [Coletas de dados privados ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "Coletas de dados privados") e prova de conhecimento zero.

- Uma coleta de dados privados assegura que os dados privados sejam compartilhados ponto a ponto (por meio do protocolo gossip) apenas para os peers autorizados a vê-los, por exemplo, peers que estão dentro das fronteiras do país. Os dados são armazenados em um banco de dados privado no peer. O serviço de ordenação não está envolvido aqui e não vê os dados privados. Um hash desses dados é gravado nos livros-razão de cada peer no canal. O hash que é usado para validação de estado serve como evidência da transação e pode ser usado para propósitos de auditoria. Os dados privados estão disponíveis para redes no {{site.data.keyword.blockchainfull_notm}} Platform que estão em execução no Fabric versão 1.2.1. No entanto, o recurso de dados privados não está disponível para peers em execução no ICP, já que as redes ICP atualmente não usam gossip.

- Uma prova de conhecimento zero (ZKP) permite que um “comprovador” assegure a um “verificador” que ele conhece um segredo sem revelar o próprio segredo.

É possível obter mais informações sobre essas tecnologias no White Paper sobre [Transações privadas e confidenciadas com o Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Transações privadas e confidenciais com o Hyperledger Fabric").

## Obtendo suporte
{: #ibp-icp-support}

Para obter informações sobre ofertas de suporte digital, consulte os [recursos e fóruns de suporte](ibmblockchain_support.html#resources) do IBM Blockchain Platform.

### {{site.data.keyword.blockchainfull_notm}} Platform for ICP

Se você comprou uma licença do IBM Blockchain Platform for ICP e deseja entrar em contato com o suporte ao cliente, veja as informações sobre [como acessar o IBM Support Community e abrir um chamado de suporte ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www-01.ibm.com/support/docview.wss?uid=ibm10740041 "Suporte do {{site.data.keyword.blockchainfull_notm}} Platform for ICP").

### {{site.data.keyword.blockchainfull_notm}} Platform Community Edition

A Community Edition é destinado à exploração, ao desenvolvimento e ao teste. O IBM Blockchain Platform não fornece suporte para o {{site.data.keyword.blockchainfull_notm}} Platform Community Edition.

Se você encontrar problemas relacionados aos seus componentes blockchain, será possível usar recursos de desenvolvedor de blockchain gratuitos e fóruns de suporte e obter ajuda da IBM e da comunidade do Fabric. Para obter mais informações, consulte [recursos de blockchain e fóruns de suporte](ibmblockchain_support.html#resources).

Para problemas que estão relacionados ao IBM Cloud Private, é possível obter vantagem tanto do [suporte digital gratuito quanto do suporte pago que o ICP oferece](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us).
