---

copyright:
  years: 2018,2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Interagindo com a rede usando APIs do Swagger
{: #ibp-swagger}

O {{site.data.keyword.blockchainfull_notm}} Platform expõe uma série de APIs de REST no Swagger que podem ser usadas para gerenciar os nós, os canais, os peers e os membros de sua rede. Seus aplicativos podem usar essas APIs para controlar recursos de rede importantes sem usar o monitor de rede.

{:shortdesc}

Antes de iniciar, é necessário criar uma instância de serviço do [{{site.data.keyword.blockchain}} Platform ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/catalog/services/ibm-blockchain-5-prod) no {{site.data.keyword.cloud_notm}} e criar ou se associar a uma rede de blockchain do Starter Plan<!--or Enterprise Plan -->.


## Recuperando credenciais de rede
{: #ibp-swagger-retrieving-network-credentials}

Entre no Monitor de rede de sua rede de Blockchain e abra a tela "APIs" do navegador esquerdo. É possível ver as suas credenciais de rede para as APIs de REST. Posteriormente, você autorizará as APIs usando os valores da "chave" e do "segredo" exibidos aqui e executará as APIs com o "network_id" como um parâmetro. Clique em **Mostrar segredo** para revelar o valor do campo segredo. Copie os valores dos campos chave, segredo e network_id, que você poderá usar posteriormente na UI do Swagger.

A **Figura 1** mostra a tela "APIs":
![Tela APIs](../images/API_screen_starter.png "Tela APIs")
*Figura 1. APIs*

Se você estiver usando o Starter Plan, será possível alternar entre organizações no Monitor de rede. Com o Starter Plan, duas organizações são configuradas por padrão. Alternar entre organizações pode ser útil para testar as APIs de REST por meio da perspectiva de cada organização. Para obter as credenciais para outra organização em sua rede, clique em seu nome do usuário no canto superior direito do console do Monitor de rede. No menu que é aberto, clique na seta suspensa ao lado da Organização para visualizar todas as Organizações. Selecione a organização para a qual você gostaria de alternar e visualizar as credenciais de rede associadas.

A **Figura 2** mostra como Alternar entre organizações:

![Switching between organizations](../images/switch_orgs_starter.gif "Switching between organizations")  
*Figura 2. Alternando entre organizações*


## Autorizando APIs do Swagger
{: #ibp-swagger-authorizing-swagger}

Clique no link **UI do Swagger** na tela "APIs" para abrir a UI do Swagger.  

Na UI do Swagger, clique no botão **Autorizar** e a janela de autorização aparece. Insira o valor de "chave" e "segredo" em suas credenciais de rede como nome do usuário e senha e clique em **Autorizar** e, em seguida, em **Concluído**. Agora você está pronto para executar as APIs. Observe que se você atualizar o seu navegador, precisará autorizar novamente com as suas credenciais.

Usando autenticação Aut. básica, quaisquer credenciais que você especificar na janela Autorizar serão armazenadas após você clicar nos botões **Autorizar** e, em seguida, **Concluído** e serão transmitidas em cada chamada de API de REST.

A **Figura 3** mostra o processo para autorizar APIs do Swagger:

![Authorize APIs](../images/swaggerUIAuthorize.gif "Authorize APIs")  
*Figura 3. Autorizar APIs*


## Tentando APIs
{: #ibp-swagger-try-out}

Clique na API de REST que você deseja executar e clique no botão **Tentar**.

A **Figura 4** mostra o botão "Experimente" na "UI do Swagger":

![Botão Experimente na UI do Swagger](../images/swaggerUITryItOut.png "Botão Experimente na UI do Swagger")  
*Figura 4. Botão "Experimente" na "UI do Swagger"*

Após você clicar no botão **Experimente**, será possível inserir parâmetros necessários para usar a API. É possível localizar `networkID` em suas credenciais de rede e localizar outros parâmetros em seu Monitor de rede. Após você inserir os parâmetros, clique em **Executar** para executar a chamada API de REST com relação à sua rede.

A **Figura 5** mostra parâmetros na "UI do Swagger":

![Parâmetros na UI do Swagger](../images/swaggerUIParams.png "Parâmetros na UI do Swagger")  
*Figura 5. Inserindo parâmetros*  

Após você clicar em **Executar**, será possível ver a resposta da chamada API com relação à sua rede. Também é possível ver um comando CURL que pode chamar a API diretamente de sua linha de comandos.

A **Figura 6** mostra o corpo de resposta da API, a URL e o comando CURL:

![Resposta da API na UI do Swagger](../images/swaggerUICurlResponse.png "Resposta da API na UI do Swagger")  
*Figura 6. Resposta da API*    

## Dicas de resolução de problemas
{: #ibp-swagger-troubleshooting}

### 401 Desautorizado  
{: #ibp-swagger-401}

  Assegure-se de que você tenha autorizado a API de REST fornecendo as suas credenciais de rede. Para obter mais informações, veja [Autorizando APIs do Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger-authorizing-swagger).

### 400 Erro: Solicitação inválida
{: #ibp-swagger-400}

  Algumas APIs podem levar um argumento no Corpo da solicitação que age como um filtro para mostrar resultados somente para um período específico. Um fragmento de amostra é fornecido no Corpo, que, se usado, precisa ser editado para especificar o peer ou a lista de peers nos quais você gostaria de filtrar. Para evitar esse erro, edite o fragmento para especificar um peer em sua rede ou remova o fragmento inteiramente.
