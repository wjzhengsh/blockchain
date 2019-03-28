---

copyright:
  years: 2017, 2019
lastupdated: "2019-03-05"

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

# Introdução ao  {{site.data.keyword.blockchainfull_notm}}  Platform
{: #get-started-ibp}

O {{site.data.keyword.blockchainfull}} Platform fornece uma oferta blockchain-as-a-service (BaaS) de pilha integral e gerenciada que permite implementar componentes de blockchain em ambientes de sua escolha. O ambiente pode ser {{site.data.keyword.cloud_notm}}, no local por meio do {{site.data.keyword.cloud_notm}} Private e nuvens de terceiros, como Amazon Web Services (AWS). Neste tutorial, conduziremos você pelo processo geral para configurar uma rede básica de blockchain com o {{site.data.keyword.blockchainfull_notm}} Platform.
{:shortdesc}

**Importante:** antes de usar uma oferta do {{site.data.keyword.blockchainfull_notm}} Platform, leia as informações técnicas e de suporte na seção [Renúncia de responsabilidade](/docs/services/blockchain/needtoknow.html#disclaimer).


## Antes de iniciar
{: #get-started-ibp-prereqs}

O {{site.data.keyword.blockchainfull_notm}} Platform fornece diferentes ofertas para ajudar todos os tipos de usuários a iniciar em sua jornada de blockchain e mover seus aplicativos para a produção. É necessário decidir o seu ambiente e a oferta que você usará. A Figura 1 lista os recursos, o público-alvo, a precificação e as plataformas de nuvem para diferentes ofertas. Para obter mais informações sobre cada oferta, clique na oferta na tabela a seguir.

| **Ofertas** | **O que está incluído** | **Política de faturamento** | **Plataforma de nuvem** |
| ------------------------- |-----------|-----------|-----------|-----------|
| [**Starter Plan**](/docs/services/blockchain/starter_plan.html#starter-plan-about) | Rede gerenciada pela {{site.data.keyword.IBM_notm}} com níveis de serviço básico, desenvolvimento e ambiente de teste | Assinatura mensal | {{site.data.keyword.cloud_notm}} |
| [**Enterprise Plan**](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about) | Rede gerenciada pela {{site.data.keyword.IBM_notm}} com níveis de serviço avançado e ambiente pronto para produção corporativa | Assinatura mensal | {{site.data.keyword.cloud_notm}} |
| [**Beta 2.0 grátis do {{site.data.keyword.blockchainfull_notm}} Platform**](/docs/services/blockchain/howto/ibp-console.html#ibp-console-overview) | Console do {{site.data.keyword.blockchainfull_notm}} Platform para implementar e gerenciar componentes de blockchain em seu cluster do {{site.data.keyword.cloud_notm}} Kubernetes | Grátis para Beta | {{site.data.keyword.cloud_notm}} |
| [**{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about) | Gráficos do Helm implementáveis de CA, solicitador e peer | [Precificação de VPC](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-pricing) e Community Edition grátis | {{site.data.keyword.cloud_notm}} Private |
| [**{{site.data.keyword.blockchainfull_notm}} Platform for AWS**](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about) | Modelo de iniciação rápida do AWS para implementar peers remotos que estão fora do {{site.data.keyword.cloud_notm}} | Grátis | AWS |

*Figura 1. {{site.data.keyword.blockchainfull_notm}} Ofertas de plataforma *

Não use o Starter Plan ou o beta 2.0 grátis para uso de produção. Ele é um ambiente de desenvolvimento e teste e não é adequado para cargas de trabalho de produção.
{: important}

## Etapa um: Obter uma oferta
{: #get-started-ibp-step1}

Assegure-se de que você tenha a conta de nuvem ou a licença do PPA para obter uma oferta do {{site.data.keyword.blockchainfull_notm}} Platform.

* **Starter Plan**, **Enterprise Plan** e **{{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta**

  Essas ofertas estão disponíveis no {{site.data.keyword.cloud_notm}} e é possível localizá-las no [painel Catálogo ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/catalog "Catálogo") do {{site.data.keyword.cloud_notm}}.

* **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

  Essa oferta é entregue como um gráfico do Helm implementável e tem a edição paga e a edição Community grátis. É possível fazer download do Enterprise Edition por meio do [Passport Advantage Online ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/software/passportadvantage/pao_customer.html) ou fazer download da edição Community grátis por meio do [GitHub ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz).

* **{{site.data.keyword.blockchainfull_notm}} Platform for AWS**

  Esta oferta está disponível no AWS e é possível implementar um blockchain peer no AWS usando o [modelo de Iniciação rápida ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/).

## Etapa dois: Implementar o  {{site.data.keyword.blockchainfull_notm}}  Platform
{: #get-started-ibp-step2}

* **Starter Plan**, **Enterprise Plan** e **{{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta**

  Efetue login no {{site.data.keyword.cloud_notm}} e crie uma instância de serviço com a oferta. Se você usar o Starter Plan, será possível obter uma rede de blockchain com a configuração padrão imediatamente depois de [criar a instância de serviço](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan). Se você usar o Enterprise Plan ou o beta 2.0 grátis do {{site.data.keyword.blockchainfull_notm}} Platform, será necessário seguir o assistente para concluir a configuração inicial para sua rede. Para obter mais informações, veja [Criando uma rede do Enterprise Plan](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network) ou [Implementando o {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.cloud_notm}} Kubernetes Service](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks).

* **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

  Antes de implementar uma rede, é necessário [importar o gráfico do Helm para seu {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install). Em seguida, é possível [configurar e instalar o {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-deploy-icp).

* **{{site.data.keyword.blockchainfull_notm}} Platform for AWS**

  Esta oferta está disponível no AWS e é possível implementar um blockchain peer no AWS usando o [modelo de Iniciação rápida ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://aws.amazon.com/quickstart/architecture/ibm-blockchain-platform/).

## Próximas etapas
{: #get-started-ibp-next-steps}

Depois de implementar o {{site.data.keyword.blockchainfull_notm}} Platform no ambiente de sua escolha, é possível configurar a rede com o consórcio, os nós, os canais, os contratos inteligentes e iniciar as transações nela. Para obter mais informações, veja os tópicos da tarefa sob a seção **COMO** na navegação à esquerda desta documentação.

## Obtendo Suporte
{: #get-started-ibp-getting-support}

O {{site.data.keyword.IBM_notm}} oferece várias opções de suporte em soluções de blockchain implementadas pela {{site.data.keyword.IBM_notm}}. Para obter mais informações sobre o suporte do {{site.data.keyword.blockchainfull_notm}} Platform, veja [Obtendo suporte](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).
