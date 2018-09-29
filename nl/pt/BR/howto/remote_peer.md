---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Sobre Peers Remotos
{: #remote-peer-overview}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


É possível executar os peers remotos do {{site.data.keyword.blockchainfull}} Platform no {{site.data.keyword.cloud_notm}} Private (ICP) <!--[AWS]or on AWS Cloud --> depois de conectá-los a uma rede de blockchain existente. A execução de peers fora do {{site.data.keyword.cloud_notm}} fornece mais flexibilidade para aumentar ou se associar a uma rede de blockchain enquanto aproveita uma rede existente dentro do {{site.data.keyword.cloud_notm}}. Os peers remotos alavancam as Autoridades de certificação (CAs) e o Serviço de solicitação na plataforma, mas permitem que você instale seu peer com outros aplicativos fora do {{site.data.keyword.cloud_notm}}.
{:shortdesc}

<!--[AWS]Move ICP description here.-->O {{site.data.keyword.cloud_notm}} Private (ICP) é uma plataforma baseada em Kubernetes para construir uma nuvem privada em um ambiente local. É possível usar o ICP para executar um peer remoto e conectá-lo a uma rede de blockchain no {{site.data.keyword.blockchainfull_notm}} Platform. O {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP alavanca o armazenamento, a segurança, a criação de log e os serviços de suporte do ICP para que você possa gerenciar os peers remotos em seu ambiente local. Para obter mais informações sobre o ICP, consulte a [documentação do {{site.data.keyword.cloud_notm}} Private ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "documentação do {{site.data.keyword.cloud_notm}} Private").  

**Nota:** o {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer é atualmente uma oferta Beta adequada para avaliação e experimentação. **Essa edição Beta não deve ser usada para produção.** Para obter acesso e mais informações, consulte [licença e precificação](#remote-peer-license-pricing).

<!--[AWS]The following cloud platforms are supported:-->
<!--[AWS]
|  Cloud Platform | Supported Versions |
| ----------------|--------------------|
| {{site.data.keyword.cloud_notm}} Private (ICP) | 2.1.0.3 |
-->
<!--[AWS]
|  Cloud Platform | Instance types |
| ----------------|--------------------|
| Amazon Web Services (AWS) | Choose from the list of available types. The minimum size is `t2.medium`, the default is `m4.xlarge`|
-->

<!--[AWS]In all cases, the network on {{site.data.keyword.blockchainfull_notm}} Platform and the remote peer nodes must be running at the same **Fabric version 1.1**.
-->

A edição Beta suporta a plataforma de nuvem do {{site.data.keyword.cloud_notm}} Private (ICP), v2.1.0.3. Observe que a rede no {{site.data.keyword.blockchainfull_notm}} Platform e os nós do peer remoto no ICP devem ser executados no mesmo **Fabric versão 1.1**.

## Considerações
{: #remote-peer-limitations}

Um peer remoto não tem acesso à funcionalidade completa ou ao suporte de peers hospedados no {{site.data.keyword.blockchainfull_notm}} Platform. Antes de executar peers remotos, assegure-se de entender as restrições e limitações a seguir:
- Os peers remotos executados em outros ambientes de nuvem não ficam visíveis no Monitor de rede da rede de blockchain no {{site.data.keyword.cloud_notm}}.
- Os peers remotos não podem ser direcionados usando a IU do Swagger na IU do Monitor de rede.
- Você é responsável pelo gerenciamento do monitoramento de funcionamento, da segurança, da criação de log e do uso de recurso de seus peers remotos.
- É possível conectar seus peers remotos apenas às redes de blockchain que estão no nível do Hyperledger Fabric v1.1.
- O tipo de banco de dados do peer remoto deve corresponder ao tipo de banco de dados de sua rede de blockchain, seja LevelDB ou CouchDB.
- A interface do CouchDB Fauxton não está disponível no peer remoto.
- A [Fofoca](../glossary.html#gossip) para peers remotos não é suportada atualmente.


## Pré-requisitos
{: #remote-peer-prereq}

Para usar um peer remoto, deve-se ter uma organização que seja membro de uma rede do Starter Plan ou do Enterprise Plan no {{site.data.keyword.blockchainfull_notm}} Platform. O peer remoto usa os terminais de API, as CAs do Hyperledger Fabric e o Serviço de solicitação da rede de Plano do {{site.data.keyword.blockchainfull_notm}} Platform para operar. Se você não for membro de nenhuma rede de blockchain, precisará criar ou associar-se a uma rede. Para obter mais informações, consulte [Criando uma rede](../get_start.html#creating-a-network) ou [Associando-se a uma rede](../get_start.html#joining-a-network).


## Licença e precificação
{: #remote-peer-license-pricing}

<!--[AWS]To access remote peers on AWS Cloud, see [License and pricing in AWS](remote_peer_aws.html#license-pricing-icp "License and pricing in AWS"). -->Para acessar peers remotos para execução no ICP, consulte [Licença e precificação para peers remotos no ICP](remote_peer_icp.html#license-pricing-icp "Licença e precificação para peers remotos no ICP"). As licenças para a edição Beta de peers remotos são gratuitas.<!--[AWS] for both platforms.--> Posteriormente, uma oferta geralmente disponível (GA) substituirá a edição beta.

A migração de Beta para GA não é suportada. É necessário fazer download e instalar novos peers remotos para usar a oferta GA, ao ser liberada. Será possível então associar novos peers remotos aos mesmos canais da mesma rede que os peers remotos Beta.

**Nota:** para operar um peer remoto, deve-se ter uma organização que pertença a uma rede do Starter Plan ou do Enterprise Plan no {{site.data.keyword.blockchainfull_notm}} Platform. Isso implica que você ou outro membro na rede deve pagar a [taxa de associação](https://console.bluemix.net/docs/services/blockchain/howto/pricing.html#key-elements-of-pricing) do IBM Blockchain de sua organização. Para obter mais informações sobre o pagamento de taxas, consulte [Modo de pagamento](paying_mode.html).  


<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer currently supports two cloud environments to run remote peers: Amazon Web Services (AWS) and {{site.data.keyword.cloud_notm}} Private (ICP).-->

<!--[AWS]### Amazon Web Services
{: #aws}-->

<!--[AWS]*Note: Need to replace the following links with real links to AWS remote peer once they are published by AWS*
You can use the [Quick Starts ![External link icon](../images/external_link.svg "External link icon")](https://amazonaws-china.com/quickstart/architecture/mongodb/ "Quick Start Template") to easily deploy {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers on AWS. For more information about deploying a remote peer on AWS, see the [AWS Remote Peer Deployment Guide ![External link icon](../images/external_link.svg "External link icon")](https://docs.aws.amazon.com/quickstart/latest/mongodb/welcome.html "Deployment Guide").

For more information about deploying remote peers in AWS, see [Deploying remote peers in Amazon Web Services](remote_peer_aws.html "Deploying remote peers in "Amazon Web Services).

The following diagram describes the process to deploy an {{site.data.keyword.blockchainfull_notm}} Platform remote peer on AWS.

![Remote Peer deployment flow on AWS](../images/remote_peer_AWS_flow.png "Remote Peer deployment flow on AWS")  
*Figure 1. Remote Peer deployment flow on AWS*
-->  

<!--[AWS]## Deploying a remote peer
{: #deploy-remote-peer}-->

<!--[AWS]### {{site.data.keyword.cloud_notm}} Private
{: #icp}-->

## Implementando um Período Remoto
{: #icp}

<!--[AWS]{{site.data.keyword.cloud_notm}} Private (ICP) is a Kubernetes-based platform for building a private cloud in an on-premises environment. You can use ICP to run a remote peer and connect the remote peer to a blockchain network on {{site.data.keyword.blockchainfull_notm}} Platform. {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer for ICP leverages the storage, security, logging, and support services of ICP so that you can manage your remote peers in your on-premises environment. For more information about ICP, see [{{site.data.keyword.cloud_notm}} Private documentation ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private documentation").-->

<!--[AWS]Nancy did some re-org here by moving ICP description to the beginning of this topic as we only support ICP now. Will move it back or other places later.-->

O diagrama a seguir descreve as etapas para implementar um peer remoto do {{site.data.keyword.blockchainfull_notm}} Platform no ICP. Para obter mais informações sobre como implementar peers remotos no ICP, consulte [Implementando peers remotos no {{site.data.keyword.cloud_notm}} Private](remote_peer_icp.html "Implementando peers remotos no {{site.data.keyword.cloud_notm}} Private").

![Fluxo de implementação do peer remoto no ICP](../images/remote_peer_ICP_flow.png "Fluxo de implementação do peer remoto no ICP")  
<!--[AWS]
*Figure 2. Remote Peer deployment flow on ICP*
-->  
*Figura 1. Fluxo de implementação do peer remoto no ICP*


## Operando um Período Remoto
{: #operate-remote-peer}

Depois de implementar o peer remoto, é necessário concluir várias etapas operacionais antes que seu peer possa enviar transações para a rede. As etapas operacionais incluem a inclusão de sua organização em um canal, a associação de seu peer remoto ao canal, a instalação do chaincode no peer remoto, a instanciação do chaincode no canal e a conexão de aplicativos em seu peer remoto. Para obter mais informações, consulte <!--[AWS][Operating remote peers in Amazon Web Service](remote_peer_operate_aws.html#remote-peer-operate-aws) or -->[Operando peers remotos no {{site.data.keyword.cloud_notm}} Private](remote_peer_operate_icp.html#remote-peer-operate).

## Residência de dados
{: #data-residency}

Requisitos de residência de dados são restrições que governam onde os dados podem residir. O requisito mais comum na residência de dados está associado às leis dentro de certos países, que determinam que todos os dados de cliente processados e armazenados em um sistema de TI devem permanecer dentro das fronteiras de um país específico. Da mesma forma, algumas empresas em indústrias altamente regulamentadas, como o governo, a assistência médica e os serviços financeiros, requerem que todos os seus dados sejam armazenados inteiramente protegidos por seu firewall. Além disso, algumas empresas têm uma política corporativa em que certos tipos de dados, geralmente, Informações pessoalmente identificáveis, devem permanecer privadas e inteiramente protegidas por seu firewall corporativo. Portanto, para alcançar a residência de dados, todos os componentes da rede de blockchain devem fazer parte do mesmo [canal](../glossary.html#channel) e residirem na fronteira de um único país.

Para tratar dos requisitos de residência de dados, é importante entender a arquitetura do Hyperledger Fabric subjacente ao {{site.data.keyword.blockchainfull_notm}} Platform. A arquitetura é centrada em três componentes-chave: Serviço de solicitação, Autoridade de certificação (CA) e Peer. Um peer recebe atualizações de estado pedidas no formato de blocos do serviço de solicitação e mantém o estado e o livro-razão. Portanto, um peer e um serviço de solicitação têm um relacionamento direto. O livro-razão contém os valores mais recentes para todas as chaves e os dados que os logs de transações incluem.

Além disso, os aplicativos clientes usam os [SDKs do Fabric](../v10_application.html#using-the-fabric-sdks) para enviar transações para os peers e o serviço de solicitação. Essas transações incluem dados de [conjunto de leitura/gravação ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/readwrite.html "Semântica de conjunto de leitura/gravação"), que contêm os pares de valor da chave no livro-razão.

Se a residência de dados no país for um requisito para seu negócio, o serviço de solicitação, os nós do peer e o aplicativo cliente deverão residir no mesmo país. Quando uma rede do {{site.data.keyword.blockchainfull_notm}} Platform é criada no {{site.data.keyword.cloud_notm}}, você tem a opção de selecionar um local para a rede. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->Para obter mais informações sobre regiões e locais, consulte [Regiões e locais do {{site.data.keyword.blockchainfull_notm}} Platform](../reference/ibp_regions.html). Para alcançar a residência de dados em um desses países, seu peer remoto deve residir no mesmo país que o local da rede do {{site.data.keyword.blockchainfull_notm}} Platform.

Se a rede do {{site.data.keyword.blockchainfull_notm}} Platform contiver nós do peer remoto e do serviço de solicitação nas fronteiras do país, será possível usar canais para segregar dados para um subconjunto de peers na rede. Os nós de solicitação estão sempre localizados no data center selecionado para hospedar a rede. Não é possível ter solicitadores nas fronteiras do país. Mas os peers podem ser localizados no data center ou em um local remoto fora do {{site.data.keyword.cloud_notm}}. Portanto, para alcançar a residência de dados, é possível criar um canal no qual o solicitador e todos os peers, sejam peers locais para o data center ou peers remotos, residam no mesmo país. Dessa maneira, todos os dados compartilhados pelo solicitador e pelos peers permanecem nas fronteiras de um único país. Outros canais ainda poderão existir, se necessário, nos quais o solicitador e os peers remotos estão localizados nas fronteiras dos países e a residência de dados não é necessária.

No futuro, a nova tecnologia no Hyperledger Fabric melhorará a capacidade de alcançar residência de dados adicional usando as [Coletas de dados privados ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "Coletas de dados privados") e o Zero Knowledge Proof.

- Uma Coleta de dados privados assegura que os dados privados sejam compartilhados ponto a ponto (por meio do protocolo de fofoca) apenas com peers autorizados a vê-los, por exemplo, peers que estejam dentro das fronteiras do país. Os dados são armazenados em um banco de dados privado no peer. O serviço de solicitação não está envolvido aqui e não vê os dados privados. Um hash desses dados é gravado nos livros-razão de cada peer no canal. O hash usado para validação de estado serve como evidência da transação e pode ser usado para propósitos de auditoria.

- Um Zero-Knowledge Proof (ZKP) permite que um "provador" assegure a um "verificador" que conhece um segredo sem revelar o próprio segredo. É uma maneira de mostrar que você sabe algo que satisfaz uma instrução sem mostrar o que você sabe.

Para obter mais informações sobre essas tecnologias, consulte o White Paper sobre [Transações privadas e confidenciais com o Hyperledger Fabric ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Transações privadas e confidenciais com o Hyperledger Fabric").

## Obtendo Suporte
{: #remote-peer-support}

A edição Beta da oferta {{site.data.keyword.blockchainfull_notm}} Remote Peer deve ser usada para exploração, desenvolvimento e teste. **Não use essa edição para produção.** O {{site.data.keyword.blockchainfull_notm}} Platform não fornece suporte para essa edição. Se você encontrar problemas relacionados ao peer remoto, consulte os [recursos e os fóruns de suporte do blockchain](../v10_dashboard.html#support-forums). Também é possível visualizar os recursos de suporte na tela **Obter ajuda** do Monitor de rede.  

<!--[AWS]
- For issues that are related to AWS, you can use both [community support forums ![External link icon](../images/external_link.svg "External link icon")](https://forums.aws.amazon.com/index.jspa "AWS community support forums") and [AWS premium support ![External link icon](../images/external_link.svg "External link icon")](https://aws.amazon.com/premiumsupport/ "AWS premium support").
-->

Para problemas relacionados ao {{site.data.keyword.cloud_notm}} Private, o ICP oferece [suporte digital e suporte da Community Edition grátis ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us "suporte digital e suporte da Community Edition grátis do ICP").

<!-- add back after GA?
If your problem cannot be solved by any of the above routes, {site.data.keyword.blockchainfull_notm}} Platform Remote Peer Enterprise Edition for ICP users can open support cases in the {{site.data.keyword.cloud_notm}} Service Portal. See [submitting support cases](../ibmblockchain_support.html#support-cases) for details on opening a support case.
-->

<!-- add back at GA
{{site.data.keyword.blockchainfull_notm}} does not support cases opened in {{site.data.keyword.cloud_notm}} relating to the {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer Community Edition. The Community Edition is meant for exploration, development and testing, and should not be used for production.-->
