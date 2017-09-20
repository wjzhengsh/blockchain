---

copyright:
  years: 2017
lastupdated: "2017-09-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan

O {{site.data.keyword.blockchainfull}} Platform Enterprise Plan é a primeira opção de associação disponível para organizações que desejam localizar ou participar de uma rede de negócios de blockchain. Esse plano fornece a infraestrutura principal juntamente com as ferramentas e o suporte para implementar facilmente uma rede de blockchain altamente segura pronta para produção.

Para membros que irão iniciar a rede, a IBM fornece uma interface gráfica com o usuário para guiar o inicializador de rede pelas etapas principais para configurar e provisionar a rede. Isso inclui convidar outros membros e configurar regras de governança. Quando a rede é implementada, uma interface gráfica com o usuário interativa fica disponível para monitorar o funcionamento e a atividade da rede; gerenciar as principais atividades de rede, incluindo novas implementações, inclusão/remoção de membros, ciclo de vida de chaincode e gerenciamento de canal; e buscar suporte técnico. Localize mais informações sobre como obter o [suporte](ibmblockchain_support.html).

O {{site.data.keyword.blockchainfull_notm}} Platform foi construído com os principais componentes do Hyperledger Fabric, incluindo uma Autoridade de Certificação (CA) e pelo menos 1 peer (máximo de 6). A IBM também fornece um serviço de solicitação Kafka tolerante a falhas (CFT) para os membros de rede. 

A CA do Fabric é a autoridade de certificação fornecida com o plano Enterprise. Duas CAs intermediárias são fornecidas por membro, as quais concedem associação à rede. Usando a CA o membro também pode fornecer associação (certificações) aos usuários finais da rede.

É importante entender que, para que uma transação seja anexada ao livro razão, há três fases envolvidas:  
1. Simulação e Endosso de Transação (peer)
2. Pedido (serviço de solicitação)
3. Validação e Confirmação (peer)

Os peers do Fabric pertencentes aos membros são a interface ou o gateway para aplicativos para execução do chaincode, fornecendo a lógica de negócios para executar transações com relação ao livro razão. Todas as transações devem ser aprovadas. Os outros membros da rede fazem esse endosso. Após o endosso, as transações são enviadas para um serviço de solicitação fornecido pela IBM (Kafka)

Além dos componentes de blockchain principais, a opção Associação Corporativa fornece uma infraestrutura com armazenamento de dados seguros e comunicações (TLS), além de alta disponibilidade. Embora as redes do Fabric compartilhem esses recursos de infraestrutura, é fornecido isolamento para os nós do componente Fabric em uma rede e cada nó é executado em um contêiner do docker seguro protegendo o ambiente de execução.

O único aspecto que deve ser determinado é o tamanho dos peers necessários para a rede. Essa decisão é baseada no número de canais necessários, além da carga de trabalho por canal, do uso de memória e do espaço em disco (armazenamento). Abaixo você encontrará os tamanhos de peer disponíveis por meio do plano de serviço de nível corporativo e orientação escolhendo o nível apropriado.

Inscreva-se agora para sua associação [{{site.data.keyword.blockchainfull_notm}} da {{site.data.keyword.IBM_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps).
