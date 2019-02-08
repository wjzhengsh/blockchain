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

# Sobre o {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services
{: #remote-peer-overview}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

**Nota:** o programa {{site.data.keyword.blockchainfull}} Platform Remote Peer on ICP (Beta) foi encerrado. Se você ainda deseja executar peers em seu ambiente de ICP, use a oferta ** {{site.data.keyword.blockchainfull_notm}} Platform for ICP** em substituição. Para obter mais informações, consulte [Sobre peers no ICP](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-peer).

É possível executar o peer do {{site.data.keyword.blockchainfull_notm}} Platform no AWS Cloud depois de conectá-lo a uma rede de blockchain existente no {{site.data.keyword.cloud_notm}}. A execução de peers fora do {{site.data.keyword.cloud_notm}} fornece mais flexibilidade para aumentar ou se associar a uma rede de blockchain enquanto aproveita uma rede existente dentro do {{site.data.keyword.cloud_notm}}. Os peers no AWS Cloud alavancam as Autoridades de Certificação (CAs) e o serviço de ordenação na plataforma, mas permitem que você coloque seu peer com outros aplicativos fora do {{site.data.keyword.cloud_notm}}.
{:shortdesc}


## Considerações
{: #remote-peer-limitations}

O {{site.data.keyword.blockchainfull_notm}} Platform for AWS não tem acesso à funcionalidade completa ou suporte de peers que estão hospedados no {{site.data.keyword.blockchainfull_notm}} Platform. Antes de executar o {{site.data.keyword.blockchainfull_notm}} Platform for AWS, assegure-se de que você compreenda as restrições e limitações a seguir:
- Os peers que são executados em outros ambientes de nuvem não são visíveis no Monitor de Rede da rede de blockchain no {{site.data.keyword.cloud_notm}}.
- Os peers em execução no {{site.data.keyword.blockchainfull_notm}} Platform for AWS não podem ser tratados usando a IU do Swagger na IU do Monitor de Rede.
- Você é responsável pelo gerenciamento do monitoramento de funcionamento, pela segurança, pela criação de log e pelo uso de recursos de seus nós de peer do {{site.data.keyword.blockchainfull_notm}} Platform for AWS.
- É possível conectar seus peers do {{site.data.keyword.blockchainfull_notm}} Platform for AWS apenas às redes de blockchain que estão no nível do Fabric v1.1 ou v1.2.1. É possível localizar a versão do Fabric abrindo a [janela Preferências de rede](/docs/services/blockchain/v10_dashboard.html#network-preferences) em seu Network Monitor.
- O tipo de banco de dados do peer do {{site.data.keyword.blockchainfull_notm}} Platform for AWS deve corresponder ao tipo de banco de dados de sua rede de blockchain, LevelDB ou CouchDB.
- A interface do CouchDB Fauxton não está disponível no peer do AWS.
- Peers do [Gossip](/docs/services/blockchain/glossary.html#gossip) for AWS não são suportados atualmente. Isso significa que os recursos do Fabric que dependem do gossip, como [dados privados ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data-arch.html "dados privados") e [descoberta de serviço ![](../images/external_link.svg  "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/discovery-overview.html "descoberta de serviço") também não são suportados.

## Pré-requisitos
{: #remote-peer-prereq}

Para usar um peer do {{site.data.keyword.blockchainfull_notm}} Platform for AWS, deve-se ter uma organização que seja membro de uma rede Starter Plan ou Enterprise Plan no {{site.data.keyword.blockchainfull_notm}} Platform. O peer do {{site.data.keyword.blockchainfull_notm}} Platform for AWS alavanca os terminais de API, autoridades de certificação do Hyperledger Fabric e o serviço de ordenação da rede do {{site.data.keyword.blockchainfull_notm}} Platform para operar. Se você não for membro de nenhuma rede de blockchain, precisará criar ou associar-se a uma rede. Para obter mais informações, consulte [Criando uma rede](/docs/services/blockchain/get_start.html#creating-a-network) ou [Associando-se a uma rede](/docs/services/blockchain/get_start.html#joining-a-network).

## Licença e precificação
{: #remote-peer-license-pricing}

O {{site.data.keyword.blockchainfull_notm}} Platform for AWS é atualmente oferecido como uma Community Edition, livre de encargos; no futuro, o IBP for AWS poderá mudar para um modelo Bring-Your-Own-License (BYOL), o que exigirá a compra de uma licença da IBM.

**Nota:** para operar um peer AWS, deve-se ter uma organização que pertença a uma rede Starter Plan ou Enterprise Plan no {{site.data.keyword.blockchainfull_notm}} Platform. Isso implica que você ou outro membro na rede deve pagar a {{site.data.keyword.blockchainfull_notm}} [taxa de associação](/docs/services/blockchain/howto/pricing.html#key-elements-of-pricing) para sua organização. Para obter mais informações sobre o pagamento de taxas, consulte [Modo de pagamento](/docs/services/blockchain/howto/paying_mode.html).


## Implementando um peer do AWS
{: #deploy-remote-peer}

Use o [Modelo de iniciação rápida do AWS ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/ "Modelo de iniciação rápida") para implementar facilmente o {{site.data.keyword.blockchainfull_notm}} Platform for AWS. Para obter mais informações, veja o [Guia de implementação de iniciação rápida do {{site.data.keyword.blockchainfull_notm}} Platform for AWS ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://s3.amazonaws.com/aws-quickstart/quickstart-ibm-fabric/doc/ibm-blockchain-platform-for-aws.pdf "Implementação de referência de iniciação rápida do IBM Blockchain Platform for AWS").

Para obter instruções sobre como implementar o {{site.data.keyword.blockchainfull_notm}} Platform for AWS, consulte [Implementando peers no Amazon Web Services](/docs/services/blockchain/howto/remote_peer_aws.html "Implementando peers no Amazon Web Services").

O diagrama a seguir descreve o processo para implementar um peer do {{site.data.keyword.blockchainfull_notm}} Platform for AWS.

<img usemap="#home_map1" border="0" class="image" id="image_ztx_crb_f1b2" src="../images/remote_peer_AWS_flow.png" width="750" alt="Clique em uma caixa para obter mais detalhes sobre o processo." style="width:750px;" />
<map name="home_map1" id="home_map1">
<area href="remote_peer_aws.html#remote-peer-aws-account" alt="Configurar ou acessar o AWS" title="Configurar ou acessar" shape="rect" coords="157.05, 52.53, 283.62, 127.11" />
<area href="remote_peer_aws.html#remote-peer-aws-account" alt="Criar par de chaves" title="Criar par de chaves" shape="rect" coords="300.97, 52.53, 427.54, 127.11" />
<area href="remote_peer_aws.html#prerequisites-aws" alt="Criar ou associar-se a uma rede" title="Criar ou associar-se a uma rede" shape="rect" coords="157.05, 131.8, 283.62, 206.37" />
<area href="remote_peer_operate_aws.html#aws-peer-operate-with-sdk" alt="Associar-se a um canal" title="Associar-se a um canal" shape="rect" coords="300.97, 131.8, 427.54, 206.37" />
<area href="remote_peer_aws.html#aws-register-peer" alt="Registrar identidade do peer" title="Registrar identidade do peer" shape="rect" coords="443.95, 131.8, 570.53, 206.37" />
<area href="remote_peer_aws.html#aws-network-endpoints" alt="Recuperar informações de configuração de peer" title="Recuperar informações de configuração de peer" shape="rect" coords="585.53, 131.8, 712.1, 206.37" />
<area href="remote_peer_aws.html#remote-peer-aws-launchqs" alt="Clicar no link" title="Clicar no link" shape="rect" coords="157.05, 258.43, 283.62, 333.48" />
<area href="remote_peer_aws.html#remote-peer-aws-launchqs" alt="Configurar instâncias de peer" title="Configurar instâncias de peer" shape="rect" coords="300.97, 258.43, 427.54, 333.48" />
<area href="remote_peer_aws.html#remote-peer-aws-test" alt="Verificar implementação" title="Verificar implementação" shape="rect" coords="443.95, 258.43, 570.53, 333.48" />
<area href="remote_peer_operate_aws.html#aws-peer-operate-with-sdk" alt="Usar Fabric SDK" title="Usar Fabric SDK" shape="rect" coords="157.05, 338.64, 283.62, 413" />
<area href="remote_peer_operate_aws.html#aws-peer-cli-operate" alt="Usar CLI de ferramentas do Fabric" title="Usar CLI de ferramenta do Fabric" shape="rect" coords="443.95, 338.64, 570.53, 413" />
</map>

*Figura 1. {{site.data.keyword.blockchainfull_notm}} Platform para fluxo de implementação do AWS no AWS*


## Operando um peer do AWS
{: #operate-remote-peer}

Depois de implementar o peer do AWS, é necessário concluir várias etapas operacionais antes que seu peer possa enviar transações para a rede. As etapas operacionais compreendem a inclusão de sua organização em um canal, a associação de seu peer ao canal, a instalação do chaincode em seu peer, a instanciação do chaincode no canal e a conexão de aplicativos em seu peer. Para obter mais informações, consulte [Operando peers no Amazon Web Service](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-operate-aws).

## Residência de dados
{: #data-residency}

Como as redes de blockchain são indiferentes para o tipo de dados que são processados, etapas extras devem ser tomadas algumas vezes para manter determinados tipos de dados seguros. O requisito mais comum sobre residência de dados está associado às leis dentro de certos países, que exigem que todos os dados que são processados e armazenados num sistema de TI devem permanecer dentro das fronteiras de um país específico. Da mesma forma, algumas empresas em indústrias altamente regulamentadas, como governo, assistência médica e serviços financeiros, requerem que os dados sejam armazenados inteiramente atrás de seu firewall. Portanto, para alcançar a residência de dados, todos os componentes da rede de blockchain devem fazer parte do mesmo [canal](/docs/services/blockchain/glossary.html#channel) e residirem na fronteira de um único país.

Para tratar dos requisitos de residência de dados, é importante entender a arquitetura do Hyperledger Fabric subjacente ao {{site.data.keyword.blockchainfull_notm}} Platform. A arquitetura é centrada em torno de três componentes principais: Autoridade de Certificação (CA), solicitador e peer. Um peer recebe atualizações de estado pedidas no formato de blocos do serviço de ordenação e mantém o estado e o livro-razão. Portanto, um peer e um solicitador têm um relacionamento direto. O livro-razão contém os valores mais recentes para todas as chaves e os dados que os logs de transações incluem.

Além disso, os aplicativos clientes usam os [SDKs do Fabric](/docs/services/blockchain/v10_application.html#using-the-fabric-sdks) para enviar transações para os peers e o serviço de ordenação. Essas transações incluem dados [Conjunto de leitura/gravação ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/readwrite.html "Semântica do conjunto de leitura/gravação"), que contém os pares de chave-valor no livro-razão.

Se a residência de dados no país for um requisito para seus negócios, o solicitador, os peers e os aplicativos clientes deverão residir no mesmo país. Quando uma rede do {{site.data.keyword.blockchainfull_notm}} Platform é criada no {{site.data.keyword.cloud_notm}}, você tem a opção de selecionar um local para a rede. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->Para obter mais informações sobre regiões e locais, consulte [Regiões e locais do {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/reference/ibp_regions.html). Para alcançar a residência de dados em um desses países, seu peer deve residir no mesmo país que o local da rede do {{site.data.keyword.blockchainfull_notm}} Platform.

### Um caso de uso para residência de dados

Considere uma rede do {{site.data.keyword.blockchainfull_notm}} Platform que inclui o Solicitador e a Autoridade de certificação junto com um consórcio de quatro organizações. As organizações têm um ou mais nós de peer. Todas as quatro organizações fazem parte de um único canal e todos os componentes da rede residem na região (por exemplo, Frankfurt), em que a rede do {{site.data.keyword.blockchainfull_notm}} Platform foi implementada. Por último, os aplicativos clientes que interagem com os peers também residem na Alemanha. A residência de dados é mantida.  

![Residência de dados quando todos os componentes estão no mesmo país](../images/remote_peer_data_res_1.png "Residência de dados quando todos os componentes estão no mesmo país")  
*Figura 3. Residência de dados quando todos os componentes estão no mesmo país*

Agora, vamos considerar as implicações quando um **peer** ingressa em uma das organizações. Um peer pode residir na mesma região que o restante da rede ou em qualquer lugar fora da região de rede do IBP:

-	Se o peer residir no mesmo país que o restante da rede, a residência de dados será mantida. Todos os dados do livro-razão permanecem dentro da Alemanha como na **Figura 3** acima.
-	Se o peer residir em um país diferente (como os EUA, por exemplo), a residência de dados não será mais mantida porque os dados no livro-razão de peer são compartilhados fora da fronteira do país.

Para resolver esse problema, os **canais** podem ser usados para segregar dados para um subconjunto de peers na rede. Quando a rede do {{site.data.keyword.blockchainfull_notm}} Platform contém peers e solicitadores fora de fronteiras do país, os canais fornecem isolamento de dados do livro-razão de organizações com peers fora da fronteira do país.  

**Nota:** os solicitadores estão sempre localizados na região do data center selecionada para hospedar a rede. Não é possível ter múltiplos solicitadores entre as fronteiras do país. No entanto, os peers podem estar localizados no data center ou em um local remoto fora do {{site.data.keyword.cloud_notm}}.

![Residência de dados quando os peers estão fora do país da região do IBM Blockchain Platform](../images/remote_peer_data_res_2.png "Os peers de residência de dados residem fora do país da região do IBM Blockchain Platform")  
*Figura 4. Os peers de Residência de Dados residem fora do país da região do IBM Blockchain Platform*

Na **Figura 4**, a residência de dados não é necessária para `OrgC` e `OrgD`. Na verdade, `OrgD` agora inclui dois peers, `OrgD-peer1` e `OrgD-peer2`, que residem nos *Estados Unidos*. Portanto, para que `OrgA`, `OrgB` e seus respectivos aplicativos clientes e peers que residem na Alemanha isolem os dados do livro-razão no canal `X`, um novo canal `Y` é criado para `OrgC` e `OrgD`.

Para um entendimento mais profundo do fluxo de dados na rede do {{site.data.keyword.blockchainfull_notm}} Platform, consulte a documentação do [Fabric no fluxo de transação![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/txflow.html "Fluxo de transação").

No futuro, a nova tecnologia no Hyperledger Fabric melhorará a capacidade de alcançar residência de dados adicional usando as [Coletas de dados privados ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html "Coletas de dados privados") e o Zero Knowledge Proof.

- Uma coleta de dados privados assegura que os dados privados sejam compartilhados ponto a ponto (por meio do protocolo gossip) apenas para os peers autorizados a vê-los, por exemplo, peers que estão dentro das fronteiras do país. Os dados são armazenados em um banco de dados privado no peer.  O serviço de ordenação não está envolvido aqui e não vê os dados privados. Um hash desses dados é gravado nos livros-razão de cada peer no canal. O hash que é usado para validação de estado serve como evidência da transação e pode ser usado para propósitos de auditoria. Os dados privados estão disponíveis para redes no {{site.data.keyword.blockchainfull_notm}} Platform que estão em execução no Fabric versão 1.2.1. No entanto, o recurso de dados privados não está disponível para peers.

- Um Zero-Knowledge Proof (ZKP) permite que um "provador" assegure a um "verificador" que conhece um segredo sem revelar o próprio segredo. É uma maneira de mostrar que você sabe algo que satisfaz uma instrução sem mostrar o que você sabe.

É possível obter mais informações sobre essas tecnologias no White Paper sobre [Transações privadas e confidenciadas com o Hyperledger Fabric ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/developerworks/cloud/library/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/index.html "Transações privadas e confidenciais com o Hyperledger Fabric").

## Obtendo Suporte
{: #remote-peer-support}

O IBM Blockchain Platform não fornece suporte para esta oferta. Se você encontrar problemas relacionados ao seu peer, será possível usar recursos de desenvolvedor de blockchain gratuitos e fóruns de suporte e obter ajuda do {{site.data.keyword.IBM_notm}} e da comunidade do Fabric. Para obter mais informações, consulte [recursos de blockchain e fóruns de suporte](/docs/services/blockchain/ibmblockchain_support.html#resources). Também é possível visualizar os recursos de suporte na tela **Obter ajuda** do Monitor de rede.

- Para problemas que estão relacionados ao AWS, é possível usar os [fóruns de suporte da comunidade ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://forums.aws.amazon.com/index.jspa "Fóruns de suporte da comunidade AWS") e o [Suporte premium do AWS ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://aws.amazon.com/premiumsupport/ "Suporte premium do AWS").

O {{site.data.keyword.blockchainfull_notm}} não suporta casos que são abertos no {{site.data.keyword.cloud_notm}} e que estão relacionados ao {{site.data.keyword.blockchainfull_notm}} Platform for AWS. A Community Edition está destinada à exploração, ao desenvolvimento e ao teste. Não a utilize para produção.
