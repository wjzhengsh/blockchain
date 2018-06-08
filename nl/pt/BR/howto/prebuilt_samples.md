---

copyright:
  years: 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Implementando aplicativos de amostra
{: #overview}

O {{site.data.keyword.blockchainfull}} Platform fornece aplicativos de amostra que é possível implementar e testar, para que seja possível obter um melhor entendimento de redes de blockchain e desenvolvimento de aplicativos.
{: shortdesc}

Após você provisionar uma rede do Starter Plan, será possível implementar os aplicativos de amostra no Monitor de rede, que automatiza as etapas para ativar aplicativos de amostra para executar em sua rede. Também é possível ativar o passo a passo de amostras para aprender o processo inteiro de implementação do aplicativo, que você precisa seguir ao implementar os seus próprios aplicativos.

## Implementando aplicativos de amostra no Starter Plan

O Starter Plan fornece uma abordagem simples para implementar aplicativos de amostra alavancando o serviço de cadeia de ferramentas no {{site.data.keyword.cloud_notm}} com apenas alguns cliques. Após você implementar e ativar um aplicativo de amostra, ele será executado em sua rede de blockchain automaticamente.

O Starter Plan fornece dois aplicativos de amostra com os quais você iniciará.

* **Bolinhas**

  A amostra Marbles permite que os usuários criem bolinhas virtuais com diferentes propriedades e as transfiram com outros usuários. Para obter mais informações sobre o Marbles, veja [Demo do Marbles ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/IBM-Blockchain/marbles).

<!--
* **Perishable Goods**

  The Perishable Goods sample enables users to deploy legal contracts that purchase goods in a supply chain based on delivery and temperature readings. For more information about this sample, see [Perishable Goods ![External link icon](../images/external_link.svg "External link icon")](https://github.com/clauseHQ/demo-clause-ibm-perishable-goods).

-->

* **Fabricação de veículo**

  A amostra de Fabricação de veículo permite que os usuários passem pelo ciclo de vida de um veículo virtual. Para obter mais informações sobre esta amostra, veja [Fabricação de veículo ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/IBM-Blockchain/vehicle-manufacture).

Conclua as etapas a seguir para implementar um aplicativo de amostra:

1. Insira o **Monitor de rede** de sua rede do Starter Plan. Se você não tiver uma, veja [Criando uma rede](../get_start_starter_plan.html#creating-a-network).

2. Abra a tela "Tente amostras" em seu Monitor de rede. Escolha o aplicativo de amostra que você deseja implementar e clique no botão **Implementar via cadeia de ferramentas**.
<!--
    ![sampleappflow0](../images/sampleappflow0.png)
-->
3. Uma janela de configuração de serviço de cadeia de ferramentas é aberta. Assegure-se de que todas as ferramentas necessárias estejam corretamente integradas. Observe que se você tiver mais de uma organização, certifique-se de inserir o nome correto da organização. O nome da organização deve ser o endereço de e-mail que você usa para se inscrever para a rede.
    **Dica**: deve-se desativar os bloqueadores de pop-up para que a página de configuração de serviço de cadeia de ferramentas possa abrir.
<!--
    ![sampleappflow1](../images/sampleappflow1.png)
-->
  Se este for o primeiro aplicativo que você implementar por meio da cadeia de ferramentas, precisará autorizar a cadeia de ferramentas a acessar o repositório GitHub.

  ![sampleappflow2](../images/sampleappflow2.png)

  Após você clicar no botão "Autorizar", será levado para o GitHub. Se você não tiver uma conta do GitHub, precisará criar uma. Forneça à Cadeia de ferramentas acesso aos seus repositórios inserindo as informações de sua conta. Se você não desejar dar à cadeia de ferramentas esse acesso, será possível implementar os aplicativos de amostra manualmente. Para obter mais informações, veja [Implementando aplicativos de amostra manualmente](#deploy_sample_applications_manually).

5. Clique no botão **Criar** na parte inferior da página de cadeia de ferramentas. Isso deve levá-lo de volta para o Monitor de rede, na qual o envio do Marbles deve estar em processo. Este processo deve levar de cinco a 10 minutos.
<!--
    ![sampleappflow3](../images/sampleappflow3.png)
-->
Após a implementação ser concluída, será possível iniciar o uso da amostra do Marbles em sua rede do Starter Plan.

Como esse processo cria um repositório GitHub bifurcado ao qual você tem acesso e controle, é possível fazer mudanças no Marbles no repositório bifurcado e confirmá-las. Essas confirmações acionarão uma construção automática de seu aplicativo Marbles e permitirão que você faça a demo no {{site.data.keyword.cloud_notm}}.

## Implementando aplicativos de amostra manualmente
{: #deploy_sample_applications_manually}

Se você desejar implementar aplicativos de amostra sem usar o Monitor de rede, assegure-se de instalar todos os pré-requisitos de software em seu sistema de arquivos local. Para obter mais informações, consulte [Configurando o ambiente de desenvolvimento de aplicativo](../v10_application.html#setting-up-application-development-environment).

Também é necessária uma rede de blockchain no {{site.data.keyword.cloud_notm}} com um Starter Plan ou Enterprise Plan e configure um canal e os seus peers. Para obter mais informações, veja [Governar a rede do Starter Plan](../get_start_starter_plan.html) e [Governar a rede do Enterprise Plan](../get_start.html). Após você provisionar uma rede e poder implementar aplicativos nela, recupere os terminais de API de seus recursos de rede que o seu aplicativo acessará. Para obter mais informações, veja [Incluindo terminais de API de rede em seu aplicativo](../v10_application.html#adding-network-api-endpoints-to-your-application).

É possível implementar um dos aplicativos de amostra a seguir em sua rede:

- **Bolinhas**

  No aplicativo Marbles, múltiplos usuários podem criar bolinhas com diferentes propriedades e transferi-las para outros. O aplicativo Marbles é gravado em JavaScript e o chaincode é gravado em Go.

  É possível localizar o código de amostra e as instruções em [Marbles no GitHub ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/IBM-Blockchain/marbles).

  Use as instruções do Bluemix em vez das instruções para hospedar Marbles localmente e insira a informação relevante de sua rede. Observe que as capturas de tela no GitHub do Marbles mostram a UI do Enterprise Plan (pois o ele suporta apenas o caminho manual para implementar o Marbles), que é um pouco diferente da UI do Starter Plan. No entanto, ambas as UIs têm as mesmas partes básicas e é possível localizar os nomes de seus peers, canais e outras informações de credencial de serviço nas telas apropriadas.

- **Fabcar**

  Em Fabcar, é possível executar **consultas** e **atualizações do livro-razão** em registros de carro no livro-razão. Fabcar é escrito em JavaScript e o chaincode é escrito em Go.

  É possível localizar o código de amostra em [Fabric car no GitHub ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/hyperledger/fabric-samples/tree/release/fabcar) e instruções em [Gravando seu Primeiro Aplicativo ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/en/latest/write_first_app.html).

- **Outros aplicativos**

  Para obter mais informações sobre como hospedar os seus próprios aplicativos no {{site.data.keyword.cloud_notm}}, veja [Hospedando aplicativos](../v10_application.html#hosting-applications).

## Excluindo um aplicativo de amostra

Para excluir um aplicativo de amostra que foi adquirido através do processo de cadeia de ferramentas, navegue para o local em que a amostra vive na UI. Como os aplicativos de amostra são instanciados em um canal, é possível localizar a amostra em canais. Clique em **Canais** na navegação esquerda para abrir a tela "Canais". Clique no canal relevante no qual a amostra é instanciada e, em seguida, clique em **Chaincode**. Isso exibe chaincodes que são instanciados nesse canal.

Se você clicar no chaincode de seu aplicativo, poderá ver uma guia **Excluir**. No entanto, clicar em **Excluir** sozinho não exclui o aplicativo de amostra, mas exclui apenas o contêiner de chaincode.  Também é necessário navegar para o painel do {{site.data.keyword.cloud_notm}} e o painel da cadeia de ferramentas para excluir a amostra lá.
