---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# o quê há de novo
{: #whats-new}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

## 8 de fevereiro de 2019
{: #whats-new-2-08-2019}

O {{site.data.keyword.blockchainfull}} Platform libera um beta 2.0 grátis, a próxima geração de soluções do {{site.data.keyword.blockchainfull_notm}} Platform que permite implementar, operar e monitorar sua rede de blockchain. O beta 2.0 grátis do {{site.data.keyword.blockchainfull_notm}} Platform inclui um novo console da interface com o usuário, que pode ser usado para implementar e gerenciar componentes de blockchain em seu próprio cluster do {{site.data.keyword.IBM_notm}} Kubernetes Service no {{site.data.keyword.cloud_notm}}. O beta 2.0 grátis do {{site.data.keyword.blockchainfull_notm}} Platform permitirá a você:

**Construir sua rede mais rápido e mais fácil dentro de uma experiência contínua**

*	Integração suave entre o desenvolvimento de contrato inteligente (VS Code) e o gerenciamento de rede
*	O DevOps simplificado permite mover do desenvolvimento para teste para produção por meio de um único console
*	Suporte para gravação de contratos inteligentes em linguagens Javascript, Java e Go

**Operar e controlar redes com controle total**

*	Implemente somente os componentes de blockchain necessários (peer, serviço de pedido, Autoridade de Certificação)
*	O console projetado novamente permite gerenciar componentes de rede em um local, não importa onde eles estejam implementados
*	Manter controle completo de suas identidades, livro-razão e contratos inteligentes

**Aumentar redes distribuídas com facilidade com a flexibilidade de multinuvem recentemente ativada**

*	Conecte-se a nós em execução em qualquer ambiente (nuvens no local, públicas, híbridas)
*	Conecte facilmente um único peer a múltiplas redes de segmentos de mercado
*	Comece pequeno, pague conforme crescer por aquilo que você usar sem investimento antecipado e faça upgrade facilmente por meio do Kubernetes

- Mais informações sobre o beta 2.0 grátis do {{site.data.keyword.blockchainfull_notm}} Platform estão disponíveis em [Sobre o beta 2.0 grátis do {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview).
- Instruções sobre como implementar a liberação beta 2.0 grátis em um cluster do {{site.data.keyword.IBM_notm}} Kubernetes Service estão disponíveis em [Introdução ao beta 2.0 grátis do {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).
- Novos tutoriais para usar o beta 2.0 grátis do {{site.data.keyword.blockchainfull_notm}} Platform estão disponíveis na subseção **{{site.data.keyword.blockchainfull_notm}} Platform 2.0** sob a categoria **COMO**.
  * [Construir um tutorial de rede](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) fornece orientação durante o processo de hospedagem de uma rede ao criar um solicitador e um peer.
  * O [tutorial Associar-se a uma rede](/docs/services/blockchain/howto/ibp-console-join-network.html#ibp-console-join-network) explica como se associar a uma rede existente criando um peer e associando-o a um canal.
  * [Implementar um contrato inteligente no tutorial de rede ](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts) fornece informações sobre como gravar um contrato inteligente e implementá-lo em sua rede.
- A oferta beta 2.0 grátis do {{site.data.keyword.blockchainfull_notm}} Platform é baseada no Hyperledger Fabric v1.4 e suporta gossip de ponto a ponto, descoberta de serviço e dados privados. Visite este [tópico](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) para saber como configurar dados privados em sua rede.

- A extensão do {{site.data.keyword.blockchainfull_notm}} Visual Studio Code está disponível por meio do Mercado do Visual Studio Code. Os desenvolvedores podem usar a extensão para criar, testar e implementar contratos inteligentes em uma instância do Hyperledger Fabric. A extensão é compatível com o Hyperledger Fabric 1.3 e superior. Para obter informações sobre como usar a extensão do VS Code, veja [Ferramentas para contratos inteligentes](/docs/services/blockchain/vscode-extension.html#develop-vscode).  

O índice é aprimorado agrupando todos os tópicos de introdução juntos em uma seção chamada **Tutoriais de introdução**. Além disso, cada uma das ofertas do {{site.data.keyword.blockchainfull_notm}} Platform que são descritas nas subseções **Sobre o {{site.data.keyword.blockchainfull_notm}} Platform** está agora contida sob a seção **APRENDER**.

**Nota:** os créditos de nuvem grátis não estão mais disponíveis para usuários do Starter Plan.

## 7 de dezembro de 2018
{: #whats-new-12-7-2018}

Os tópicos *Operar rede do Starter Plan* e *Operar rede do Enterprise Plan* são combinados e substituídos por um único tutorial sobre [Usando o monitor de rede](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard).

## 27 de novembro de 2018
{: #whats-new-11-27-2018}

O {{site.data.keyword.blockchainfull_notm}}  Platform libera o  {{site.data.keyword.blockchainfull_notm}}  Platform for  {{site.data.keyword.cloud_notm}}  Private. O {{site.data.keyword.cloud_notm}} Private é uma plataforma de aplicativo para desenvolver e gerenciar aplicativos conteinerizados que são baseados no Kubernetes e permite que os usuários implementem Autoridades de Certificação (CAs), solicitadores e peers no x86, LinuxONE e IBM Z. O {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private é baseado no Hyperledger Fabric v1.2.1 e é implementado usando os gráficos do Helm do Kubernetes. Depois de instalar o gráfico do Helm, será possível localizá-lo como um serviço empacotado no Catálogo do {{site.data.keyword.cloud_notm}} Private. Observe que [esta oferta](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) é mais adequada para usuários experientes do Fabric.

Para obter mais informações sobre o {{site.data.keyword.cloud_notm}} Private, veja a [Documentação do {{site.data.keyword.cloud_notm}} Private v3.1.0 ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "Documentação do {{site.data.keyword.cloud_notm}} Private v3.1.0").

A liberação do {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private também marca o término do programa IBM Blockchain Platform Remote Peer (Beta). Ainda é possível implementar um peer no {{site.data.keyword.cloud_notm}} Private ou no AWS e conectá-lo a uma rede do Starter Plan ou do Enterprise Plan. Para obter mais informações, consulte [implementando um peer no Starter ou no Enterprise Plan](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy) e [implementando um peer no AWS](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws). Esses peers são considerados peers **distribuídos** em vez de peers remotos porque a implementação é gerenciada pelo cliente e, como resultado, não há nenhum local central a partir do qual ser remoto.

Essa liberação também apresenta algumas melhorias no índice da documentação. Se você for um usuário Starter ou Enterprise, seu conteúdo ainda poderá ser localizado nas seções **APRENDIZADO**, **INSTRUÇÕES**, **REFERÊNCIA** e **AJUDA** e os links ainda são os mesmos. Ele pode apenas estar em uma subseção diferente no índice.

## 13 de novembro de 2018
{: #whats-new-11-13-2018}

O {{site.data.keyword.blockchainfull_notm}} Platform libera o {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services (AWS).

O {{site.data.keyword.blockchainfull_notm}} Platform for AWS permite que você execute peers no AWS Cloud e conecte os peers às redes de blockchain existentes no {{site.data.keyword.cloud_notm}}. Seus peers no AWS podem alavancar o perfil de conexão e outros componentes de blockchain nas redes existentes, enquanto fornece mais flexibilidade para colocar seus peers com outros aplicativos fora do {{site.data.keyword.cloud_notm}}. Para obter mais informações, consulte [Sobre o {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws).
/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws

## 4 de outubro de 2018
{: #whats-new-10-4-2018}

O {{site.data.keyword.blockchainfull_notm}} Platform faz upgrade do Starter Plan a partir do Hyperledger Fabric v1.1.0 a v1.2.1. Para obter mais informações, veja [Sobre o Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about).

**Importante:** o Fabric v1.2.1 atualmente não é compatível com o beta Remote Peer, que usa um peer v1.1.0. As redes do Starter Plan, que foram criadas antes de 4 de outubro de 2018 e são baseadas no Fabric v1.1.0, não são impactadas e ainda podem ser usadas com o beta Remote Peer. O {{site.data.keyword.blockchainfull_notm}} Platform atualizará o beta Remote Peer para usar o peer v1.2.1, que será compatível com as novas redes Starter, que estão no nível do Fabric v1.2.1 e nas redes Enterprise, que estão no nível do Fabric v1.1.0. Até que o beta Remote Peer seja atualizado para o Fabric v1.2.1, não tente implementar um peer remoto v1.1.0 com uma nova rede Starter v1.2.1.

## 4 de setembro de 2018
{: #whats-new-9-4-2018}

O {{site.data.keyword.blockchainfull_notm}} Platform libera a oferta Beta do Remote Peer. A oferta do Remote Peer é baseada no Hyperledger Fabric v1.1.0. Usando o Remote Peer, é possível executar os nós de peer do {{site.data.keyword.blockchainfull_notm}} Platform em seu próprio ambiente de nuvem do {{site.data.keyword.cloud_notm}} Private ou Amazon Web Services (AWS). Para obter mais informações, consulte [Sobre peers remotos](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws).

## 15 de junho de 2018
{: #whats-new-6-15-2018}

O {{site.data.keyword.blockchainfull_notm}} Platform libera a disponibilidade geral do Starter Plan. Para obter mais informações, veja [Sobre o Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about).

## 15 de maio de 2018
{: #whats-new-5-15-2018}

O {{site.data.keyword.blockchainfull_notm}} Platform faz upgrade do Enterprise Plan do Hyperledger Fabric v1.0.0 para a v1.1.0. Para obter mais informações, veja [Sobre o plano corporativo](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

## 18 de março de 2018
{: #whats-new-3-18-2018}

O {{site.data.keyword.blockchainfull_notm}} Platform libera o Starter Plan Beta. O Starter Plan oferece a você um ambiente de desenvolvimento e teste para aprender e praticar em uma rede do {{site.data.keyword.blockchainfull_notm}} Platform. Para obter mais informações, veja [Sobre o Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about).

## 11 de agosto de 2017
{: #whats-new-8-11-2017}

O {{site.data.keyword.blockchainfull_notm}} Platform substitui o
{{site.data.keyword.blockchainfull_notm}} on Cloud e libera o Enterprise Plan. Para obter mais informações, veja [Sobre o plano corporativo](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).
