---

copyright:
  years: 2017
lastupdated: "2017-08-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Aplicativos de amostra

É possível implementar e testar aplicativos de amostra para obter um melhor entendimento da rede de {{site.data.keyword.blockchainfull}} e do desenvolvimento de aplicativos.
{:shortdesc}

## Antes de iniciar

Certifique-se de instalar todos os pré-requisitos de software em seu sistema de arquivos local. Para obter mais informações, consulte [Configurando o ambiente de desenvolvimento de aplicativo](../v10_application.html#setting-up-application-development-environment).

Também é necessário ter uma rede de {{site.data.keyword.blockchain}} no {{site.data.keyword.Bluemix_short}} com um canal e seus peers configurados. Para obter mais informações, consulte [Controle a rede](../get_start.html). Quando sua rede estiver pronta para uso, recupere os terminais de API de seus recursos de rede que seu aplicativo acessará. Para obter mais informações, consulte [Incluindo credenciais de serviço de rede em seu aplicativo](../v10_application.html#adding-network-service-credentials-to-your-application).


## Aplicativo Marbles

No aplicativo Marbles, múltiplos usuários podem criar marbles com diferentes propriedades e transferi-los com outros. O aplicativo Marbles é gravado em Javascript e o chaincode é gravado em Go.

É possível localizar o código de amostra e as instruções em [Marbles no GitHub ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/IBM-Blockchain/marbles).


## Aplicativo Fabric car

No aplicativo Fabric car, é possível executar **consultas** e **atualizações** em registros de carro no livro razão. O aplicativo Fabric car é gravado em Javascript e o chaincode é gravado em Go.

É possível localizar o código de amostra em [Fabric car no GitHub ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar) e instruções em [Gravando seu Primeiro Aplicativo ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html).

<!-- 
## High available application
-->
<!--
The high available application demonstrate how to enable the following features to ensure the high availability of a {{site.data.keyword.blockchain}} network.
1. Have 2 peers and have your application smart enough to talk to one and if it is getting errors or no response switch over to the other.
2. Same for orderers, 2 or 3 and have your application smart enough to fail over if needed.
OR put orderers/peers behind a load balancer.
-->
