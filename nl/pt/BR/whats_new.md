---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# o quê há de novo
{: #whatsnew}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

## 7 de dezembro de 2018

Os tópicos *Operar rede do Starter Plan* e *Operar rede do Enterprise Plan* são combinados e substituídos por um único tutorial sobre [Usando o monitor de rede](/docs/services/blockchain/v10_dashboard.html).

## 27 de novembro de 2018

O {{site.data.keyword.blockchainfull}} Platform libera o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private (ICP). O ICP é uma plataforma de aplicativo para desenvolvimento e gerenciamento de aplicativos conteinerizados que são baseados no Kubernetes e permite que os usuários implementem Autoridades de Certificação (CAs), solicitadores e peers no x86, LinuxONE e IBM Z. O {{site.data.keyword.blockchainfull_notm}} Platform for ICP é baseado no Hyperledger Fabric v1.2.1 e é implementado usando gráficos Helm do Kubernetes. Depois de instalar o gráfico Helm, é possível localizá-lo como um serviço de pacote configurável no Catálogo do ICP. Observe que [esta oferta](/docs/services/blockchain/ibp-for-icp-about.html) é mais adequada para usuários experientes do Fabric.

Para obter mais informações sobre o ICP, consulte a documentação do [Documentação do {{site.data.keyword.cloud_notm}} Private v3.1.0 ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private v3.1.0").

A liberação do {{site.data.keyword.blockchainfull_notm}} Platform for ICP também marca o fim do programa IBM Blockchain Platform Remote Peer (Beta). Ainda é possível implementar um peer no ICP ou no AWS e conectá-lo a uma rede Starter Plan ou Enterprise Plan. Para obter mais informações, consulte [implementando um peer no Starter ou no Enterprise Plan](/docs/services/blockchain/howto/peer_deploy_ibp.html) e [implementando um peer no AWS](/docs/services/blockchain/howto/remote_peer_aws.html). Esses peers são considerados peers **distribuídos** em vez de peers remotos porque a implementação é gerenciada pelo cliente e, como resultado, não há nenhum local central a partir do qual ser remoto.

Essa liberação também apresenta algumas melhorias no índice da documentação. Se você for um usuário Starter ou Enterprise, seu conteúdo ainda poderá ser localizado nas seções **APRENDIZADO**, **INSTRUÇÕES**, **REFERÊNCIA** e **AJUDA** e os links ainda são os mesmos. Ele pode apenas estar em uma subseção diferente no índice.

## 13 de novembro de 2018

O {{site.data.keyword.blockchainfull_notm}} Platform libera o {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services (AWS).

O {{site.data.keyword.blockchainfull_notm}} Platform for AWS permite que você execute peers no AWS Cloud e conecte os peers às redes de blockchain existentes no {{site.data.keyword.cloud_notm}}. Seus peers no AWS podem alavancar o perfil de conexão e outros componentes de blockchain nas redes existentes, enquanto fornece mais flexibilidade para colocar seus peers com outros aplicativos fora do {{site.data.keyword.cloud_notm}}. Para obter mais informações, consulte [Sobre o {{site.data.keyword.blockchainfull_notm}} Platform for Amazon Web Services](/docs/services/blockchain/howto/remote_peer.html).

## 4 de outubro de 2018

O {{site.data.keyword.blockchainfull_notm}} Platform faz upgrade do Starter Plan a partir do Hyperledger Fabric v1.1.0 a v1.2.1. Para obter mais informações, veja [Sobre o Starter Plan](/docs/services/blockchain/starter_plan.html).

**Importante:** o Fabric v1.2.1 atualmente não é compatível com o beta Remote Peer, que usa um peer v1.1.0. As redes do Starter Plan, que foram criadas antes de 4 de outubro de 2018 e são baseadas no Fabric v1.1.0, não são impactadas e ainda podem ser usadas com o beta Remote Peer. O {{site.data.keyword.blockchainfull_notm}} Platform atualizará o beta Remote Peer para usar o peer v1.2.1, que será compatível com as novas redes Starter, que estão no nível do Fabric v1.2.1 e nas redes Enterprise, que estão no nível do Fabric v1.1.0. Até que o beta Remote Peer seja atualizado para o Fabric v1.2.1, não tente implementar um peer remoto v1.1.0 com uma nova rede Starter v1.2.1.

## 4 de setembro de 2018

O {{site.data.keyword.blockchainfull_notm}} Platform libera a oferta Beta do Remote Peer. A oferta do Remote Peer é baseada no Hyperledger Fabric v1.1.0. Usando o Remote Peer, é possível executar os nós de peer do {{site.data.keyword.blockchainfull_notm}} Platform em seu próprio ambiente de nuvem do IBM Cloud Private (ICP) ou do Amazon Web Services (AWS). Para obter mais informações, consulte [Sobre peers remotos](/docs/services/blockchain/howto/remote_peer.html).

## 15 de junho de 2018

O {{site.data.keyword.blockchainfull_notm}} Platform libera a disponibilidade geral do Starter Plan. Para obter mais informações, veja [Sobre o Starter Plan](/docs/services/blockchain/starter_plan.html).

## 15 de maio de 2018

O {{site.data.keyword.blockchainfull_notm}} Platform faz upgrade do Enterprise Plan do Hyperledger Fabric v1.0.0 para a v1.1.0. Para obter mais informações, veja [Sobre o plano corporativo](/docs/services/blockchain/enterprise_plan.html).

## 18 de março de 2018

O {{site.data.keyword.blockchainfull_notm}} Platform libera o Starter Plan Beta. O Starter Plan oferece a você um ambiente de desenvolvimento e teste para aprender e praticar em uma rede do {{site.data.keyword.blockchainfull_notm}} Platform. Para obter mais informações, veja [Sobre o Starter Plan](/docs/services/blockchain/starter_plan.html).

## 11 de agosto de 2017

O {{site.data.keyword.blockchainfull_notm}} Platform substitui o
{{site.data.keyword.blockchainfull_notm}} on Cloud e libera o Enterprise Plan. Para obter mais informações, veja [Sobre o plano corporativo](/docs/services/blockchain/enterprise_plan.html).
