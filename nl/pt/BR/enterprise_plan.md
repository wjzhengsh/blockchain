---

copyright:
  years: 2018, 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Sobre o Enterprise Plan
{: #enterprise-plan-about}

O {{site.data.keyword.blockchainfull}} Platform Enterprise Plan é uma oferta pronta para produção para organizações que gostariam de criar ou se associar a uma rede de blockchain para empresas reais. Esse plano fornece a infraestrutura chave juntamente com as ferramentas e suporte para iniciar facilmente uma rede altamente segura e pronta para produção. O Enterprise Plan foi atualizado do Hyperledger Fabric V1.0 para o V1.1 em 15 de maio de 2018. Todas as redes que foram criadas após 15 de maio de 2018 estão no nível do Fabric V1.1. No entanto, redes que foram criadas anteriormente permanecerão no nível do Fabric V1.0.
{:shortdesc}

O **Enterprise Plan** é um ambiente de produção que oferece altos níveis de segurança e suporte. A implementação de sua rede no Enterprise Plan permite aproveitar os recursos a seguir:

* Um ambiente de segurança ultra alta com muitos recursos de segurança de hardware, de firmware e de software.
* Arquitetura protegida para escalabilidade, resiliência e disponibilidade.
* Otimizado para desempenho e execução na computação do Linux mais rápida do mundo.

A operação de sua rede no {{site.data.keyword.blockchainfull_notm}} Platform inclui ferramentas e recursos para simplificar tarefas administrativas:

* Painéis para monitorar e gerenciar os recursos na rede.
* Upgrades ininterruptos da pilha de código integral.
* Suporte técnico 24/7 que está integrado ao portal.
* Pilha de segurança protegida sem acesso privilegiado, resistência a malware e violação, 100% de criptografia e muito mais recursos para redes com dados sensíveis em setores regulamentados.
* As redes corporativas são submetidas a backup externamente uma vez a cada 24 horas. No caso de um desastre, essas redes podem ser restauradas para o mesmo site ou para um site alternativo.

O Enterprise Plan é um serviço de plataforma no {{site.data.keyword.cloud_notm}} e segue os [Termos de serviços do {{site.data.keyword.cloud_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "Termos de serviços do {{site.data.keyword.cloud_notm}}") em acordos de nível de serviço (SLAs). Observe que as redes do Enterprise Plan são provisionadas em **múltiplos ambientes** em data centers separados geograficamente.

**Notas:**
- O {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan fornece um ambiente de produção. Se você precisar de um ambiente de desenvolvimento e teste, veja [Sobre o Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about).
- O {{site.data.keyword.blockchainfull_notm}} Platform é um serviço de plataforma no {{site.data.keyword.cloud_notm}} e todas as ofertas de associação seguem os [Termos de serviços do {{site.data.keyword.cloud_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://www-03.ibm.com/software/sla/sladb.nsf/sla/bm "Termos de serviços do {{site.data.keyword.cloud_notm}}") em acordos de nível de serviço (SLAs). As redes do Enterprise Plan são provisionadas em **diversos ambientes** em data centers separados geograficamente.

Para membros que irão iniciar a rede, a IBM fornece uma interface gráfica com o usuário para guiar o inicializador de rede pelas etapas principais para configurar e provisionar a rede. Isso inclui convidar outros membros e configurar regras de governança. Para obter mais informações, veja [Governar a rede do Enterprise Plan](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan). Após a rede ser implementada, uma interface gráfica com o usuário interativa, o Monitor de rede, está disponível para monitorar o funcionamento e a atividade da rede; gerenciar atividades de rede chaves que incluem novas implementações, inclusão ou remoção de membros, ciclo de vida do chaincode e gerenciamento de canal; e buscar suporte técnico. Para obter mais informações, consulte [Usando o monitor de rede](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard).

Inscreva-se agora para a sua associação do [{{site.data.keyword.blockchainfull_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod).

O {{site.data.keyword.blockchainfull_notm}} Platform foi construído com os componentes chave do Hyperledger Fabric que incluem uma Autoridade de certificação (CA) e pelo menos 1 peer (máximo de 6).  O Enterprise Plan também fornece um serviço de ordenação do Kafka tolerante a falhas de travamento (CFT) para os membros de rede.

A autoridade de certificação do Fabric é a fornecida com o Enterprise Plan. Duas autoridades de certificação intermediárias são fornecidas por membro, as quais concedem associação à rede. Usando a CA, o membro também pode fornecer certificados de associação para usuários da rede.

É importante entender que para uma transação ser anexada ao livro-razão, há três fases que estão envolvidas:
1. Simulação e Endosso de Transação (peer)
2. Pedido (serviço de ordenação)
3. Validação e Confirmação (peer)

Os peers do Fabric pertencentes aos membros são a interface ou o gateway para aplicativos para execução do chaincode, fornecendo a lógica de negócios para executar transações com relação ao livro-razão. Todas as transações devem ser aprovadas. Os outros membros da rede fazem esse endosso. Após o endosso, as transações são enviadas para um serviço de pedido fornecido pela IBM.

Além dos componentes de blockchain principais, a opção Associação Corporativa fornece uma infraestrutura com armazenamento de dados seguros e comunicações (TLS), além de alta disponibilidade.  Embora as redes do Fabric compartilhem esses recursos de infraestrutura, é fornecido isolamento para os nós do componente Fabric em uma rede e cada nó é executado em um contêiner do Docker seguro que protege o ambiente de execução.

O único aspecto que deve ser determinado é o tamanho dos peers que a rede requer. Essa decisão é baseada no número de canais que são necessários, além da carga de trabalho por canal, do uso de memória e do espaço em disco (armazenamento).

É necessário usar o Enterprise Plan para implementações mais estáveis, de produção ou quase no nível de produção. Para propósitos de teste, use [Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about) ou [instale imagens do Docker localmente](http://hyperledger-fabric.readthedocs.io/en/release-1.1/build_network.html).

<!--- The Enterprise plan provides the ordering service and CA. The membership fee is $1,000, and a per peer fee of $1,000 that is associated with the network. If you want to have high availability (HA), you must purchase an additional peer to provide the HA capabilities. For example, one organization (associated membership fee of $1,000) of two peers ($1,000 X 2 peers) with HA ($1,000 X 2 HA peers) requires a monthly charge of $5,000.  --->

## Precificação
{: #enterprise-plan-about-pricing}

Para usar o Enterprise Plan, membros de rede devem pagar US$ 1.000 por mês como a taxa de associação e adicionalmente US$ 1.000 por mês para cada um de seus peers na rede.  As taxas mensais são faturadas diariamente rateadas.  Por exemplo, um membro (taxa de participação associada de U$ 1.000) de dois peers (por taxa peer de U$ 1.000 × 2 peers) precisa pagar U$ 3.000 por mês.  Se o mês tiver 30 dias, o membro pagará U$ 100 (U$ 3.000/30) todos os dias.  Observe que se a alta disponibilidade (HA) for necessária, será necessário dobrar os números de peers necessários para fornecer os recursos de HA.

Os membros de rede podem pagar a sua conta com as suas próprias contas do {{site.data.keyword.cloud_notm}} que contêm o espaço para criar a instância de rede. Como alternativa, um membro de rede pode cobrir a conta para todos os membros na rede. Para obter mais informações sobre como pagar pelas redes de blockchain, veja [Pagando pela rede](/docs/services/blockchain/howto/paying_mode.html#paying-mode).
