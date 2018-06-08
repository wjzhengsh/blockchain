---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-15"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Interagindo com a rede usando APIs do Swagger

O {{site.data.keyword.blockchainfull}} Platform expõe uma série de APIs de REST para facilitar o seu desenvolvimento de aplicativo. É possível testar com relação às suas redes de blockchain usando uma UI do Swagger.
{:shortdesc}

Antes de iniciar, é necessário criar uma instância de serviço do [{{site.data.keyword.blockchain}} Platform ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/blockchain) no {{site.data.keyword.Bluemix_notm}} e criar ou se associar a uma rede de blockchain do Starter Plan<!--or Enterprise Plan -->.


## Recuperando credenciais de rede

{: #retrieving-network-credentials}

Entre no Monitor de rede de sua rede de Blockchain e abra a tela "APIs" do navegador esquerdo. É possível ver as suas credenciais de rede para as APIs de REST. Posteriormente, você autorizará as APIs usando os valores da "chave" e do "segredo" exibidos aqui e executará as APIs com o "network_id" como um parâmetro. Clique em **Mostrar segredo** para revelar o valor do campo segredo. Copie os valores dos campos chave, segredo e network_id, que você poderá usar posteriormente na UI do Swagger.

<!-- Removing this code snippet so people don't try to use these values
```
},
   "x-api": {
       "url": "https://ibmblockchain.bluemix.net",
       "key": "PeerOrg1",
       "network_id": "e1f5b3341b1d483bbaf829f601144023",
       "secret": "71a329aabde9ff20de0aa4bfafd72a4466d78c87f637e7ff92c2534b5ce81cc0"
   }
```
-->

**Figura 1** mostra a tela "APIs":

![Tela de APIs](../images/API_screen_starter.png "Tela de APIs")
*Figura 1. APIs*

Se você estiver usando o Starter Plan, será possível alternar entre organizações no Monitor de rede. Com o Starter Plan, duas organizações são configuradas por padrão. Alternar entre organizações pode ser útil para testar as APIs de REST por meio da perspectiva de cada organização. Para obter as credenciais para outra organização em sua rede, clique em seu nome do usuário no canto superior direito do console do Monitor de rede. No menu que é aberto, clique na seta suspensa ao lado da Organização para visualizar todas as Organizações. Selecione a organização para a qual você gostaria de alternar e visualizar as credenciais de rede associadas.

A **Figura 2** mostra como Alternar entre organizações:

![Switching between organizations](../images/switch_orgs_starter.gif "Switching between organizations")  
*Figura 2. Alternando entre organizações*


## Autorizando APIs do Swagger

Clique no link **UI do Swagger** na tela "APIs" para abrir a UI do Swagger.  
<!-- remove this line because the link is different depending on if you are starter or enterprise plan
You can also open the Swagger UI with the URL in the connection profiles. For example, `http://blockchain-swagger-dev.stage1.mybluemix.net`.
-->

Na UI do Swagger, clique no botão **Autorizar** e a janela de autorização aparece. Insira o valor de "chave" e "segredo" em suas credenciais de rede como nome do usuário e senha e clique em **Autorizar** e, em seguida, em **Concluído**. Agora você está pronto para executar as APIs. Observe que se você atualizar o seu navegador, precisará autorizar novamente com as suas credenciais.

Usando autenticação Aut. básica, quaisquer credenciais que você especificar na janela Autorizar serão armazenadas após você clicar nos botões **Autorizar** e, em seguida, **Concluído** e serão transmitidas em cada chamada de API de REST.

A **Figura 3** mostra o processo para autorizar APIs do Swagger:

![Authorize APIs](../images/swaggerUIAuthorize.gif "Authorize APIs")  
*Figura 3. Autorizar APIs*


## Tentando APIs

Clique na API de REST que você deseja executar e clique no botão **Tentar**. Insira os parâmetros necessários e clique em **Executar**. A chamada API de REST é executada com relação à sua rede.

**Figura 4** mostra a "UI do Swagger":

![Swagger UI](../images/swaggerUITryItOut.png "Swagger UI")  
*Figura 4. UI do Swagger*


## Dicas de resolução de problemas

### 401 Desautorizado  
  Assegure-se de que você tenha autorizado a API de REST fornecendo as suas credenciais de rede. Para obter mais informações, veja [Autorizando APIs do Swagger](#authorizing-swagger-apis).

### 400 Erro: Solicitação inválida
  Algumas APIs podem levar um argumento no Corpo da solicitação que age como um filtro para mostrar resultados somente para um período específico. Um fragmento de amostra é fornecido no Corpo, que, se usado, precisa ser editado para especificar o peer ou a lista de peers nos quais você gostaria de filtrar. Para evitar esse erro, edite o fragmento para especificar um peer em sua rede ou remova o fragmento inteiramente.
