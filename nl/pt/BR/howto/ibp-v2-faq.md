---

copyright:
  years: 2019
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
{:faq: data-hd-content-type='faq'}
{:pre: .pre}

# Perguntas Frequentes
{: #ibp-v2-faq}

## Qual versão do Hyperledger Fabric está sendo usada com o {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta?
{: #ibp-v2-faq-fabric-version}
{: faq}

O {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta usa o Hyperledger Fabric v1.4 e inclui suporte para gossip, descoberta de serviço e dados privados.

## Preciso pagar alguma coisa pelo {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta?
{: #ibp-v2-faq-cost}
{: faq}

A liberação beta é grátis. Mas você ainda pode incorrer em encargos para o cluster do {{site.data.keyword.cloud_notm}} Kubernetes, a menos que você tenha selecionado um cluster grátis. No entanto, esteja ciente de que o cluster grátis é excluído automaticamente após 30 dias de acordo com a política de serviço do {{site.data.keyword.cloud_notm}} Kubernetes e o desempenho e a capacidade são limitados. Se você quiser continuar com o serviço além de 30 dias, será necessário pagar por uma instância padrão do Kubernetes Service. 

## Posso usar meu cluster do {{site.data.keyword.cloud_notm}} Kubernetes Service existente?
{: #ibp-v2-faq-existing-cluster}
{: faq}

Seu cluster Kubernetes existente funcionará com {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta, desde que satisfaça as condições a seguir:
- Ele está na região de Dallas do  {{site.data.keyword.cloud_notm}} .
- Ele está executando o Kubernetes v1.11 ou mais recente. Consulte estas instruções para obter informações sobre como [fazer upgrade da versão do Kubernetes de seu cluster](/docs/services/blockchain/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes).
- Há recursos disponíveis suficientes no cluster. Consulte este tópico sobre [requisitos mínimos de recurso](/docs/services/blockchain/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-resources-required) para obter mais informações.

## Qual banco de dados os peers usam para seu livro-razão?
{: #ibp-v2-faq-couchDB}
{: faq}

Todos os peers são implementados com o {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta. Use o banco de dados CouchDB. No entanto, não é possível consultar o CouchDB diretamente.

## Quais linguagens são suportadas para contratos inteligentes?
{: #ibp-v2-faq-cc-langs}
{: faq}

O {{site.data.keyword.blockchainfull_notm}} Platform suporta contratos inteligentes gravados em Go, Node.js e Java. O novo modelo de programação do Hyperledger Fabric atualmente suporta apenas o Node.js. Mais em breve. Se você estiver interessado em preservar o código do aplicativo existente ou usar os SDKs do Fabric para linguagens diferentes do Node.js, ainda será possível se conectar à sua rede do {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta usando a API do SDK do Fabric de nível inferior.

## Posso migrar do beta V2.0 para o GA v2.0? 
{: #ibp-v2-faq-migrate}
{: faq}

Os clientes do Enterprise Plan serão capazes de migrar para o IBM Blockchain Platform 2.0 quando ele se tornar geralmente disponível (GA). Mas você não conseguirá migrar a sua oferta beta 2.0 para a oferta GA 2.0. Além disso, não é possível migrar uma instância do Starter Plan para o beta 2.0 ou para o produto GA 2.0, quando ele se tornar disponível.

## Eu tenho acesso aos serviços de criação de log? Quais logs estão disponíveis?
{: #ibp-v2-faq-logs}
{: faq}

Com o {{site.data.keyword.blockchainfull_notm}} Platform 2.0, agora é possível acessar diretamente seus logs de peer, da autoridade de certificação e do solicitador por meio do painel do Kubernetes. Recomendamos que você aproveite o serviço de [{{site.data.keyword.cloud_notm}} LogDNA ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/catalog/services/logdna "{{site.data.keyword.IBM_notm}} Análise do log com LogDNA") que permite que você analise facilmente os logs em tempo real.

## Qual é a importância de usar o {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta sobre o Hyperledger Fabric nativo?
{: #ibp-v2-faq-native-fabric}
{: faq}

O {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta permite que os clientes implementem facilmente uma rede de blockchain customizada. A plataforma inclui um console intuitivo para implementação rápida de uma rede e a capacidade de instalar e instanciar facilmente os contratos inteligentes. Com a plataforma, você também recebe suporte de especialistas em {{site.data.keyword.IBM_notm}} para ajudá-lo a desenvolver seu contrato inteligente e a construir sua rede.

## Como posso maximizar o desempenho e escalar minha solução {{site.data.keyword.blockchainfull_notm}} Platform?
{: #ibp-v2-faq-perf-scale}
{: faq}

Consulte esta série de blog [ ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/blogs/blockchain/2019/01/answering-your-questions-on-hyperledger-fabric-performance-and-scale/ "Respondendo perguntas sobre desempenho e escala do Hyperledger Fabric") para obter fatores que afetam o desempenho de sua solução e informações sobre como o blockchain pode escalar.
