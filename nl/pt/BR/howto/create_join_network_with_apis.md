---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Criando ou se associando a uma rede com APIs do Swagger
{: #swagger-network}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


O {{site.data.keyword.blockchainfull}} Platform expõe uma série de APIs de REST que você pode usar para criar ou se associar a uma rede de blockchain no {{site.data.keyword.cloud_notm}}. É possível experimentar essas APIs usando a [IU do Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger) que está associada à sua rede.
{:shortdesc}


## Recuperando credenciais de autenticação básica para a API
{: #swagger-network-retrieve-id-token}

Antes de iniciar, é necessário criar uma instância de serviço do [{{site.data.keyword.blockchainfull_notm}} Platform ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/blockchain) com o Starter Plan ou o Enterprise Plan no {{site.data.keyword.cloud_notm}}.

Para usar APIs do Swagger para criar ou se associar a uma rede, é necessária uma credencial de autenticação básica para assegurar que você tenha acesso à instância de serviço no {{site.data.keyword.cloud_notm}}.

1. Em seu [ painel do {{site.data.keyword.cloud_notm}} ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/dashboard/apps/), abra a instância de serviço que você criou.
2. Clique em **Credenciais de serviço** no navegador esquerdo.
3. Clique no botão "Nova credencial" na página **Credenciais de serviço** para criar uma nova credencial.
    1. Dê à credencial um nome, por exemplo, *CreateJoin*.
    2. Insira **{"type": "service_instance_token"}** no campo "Incluir parâmetro de configuração sequencial".
    3. Clique no botão **Incluir**.
    ![Recuperar credenciais de serviço](../images/service_credentials.gif "Recuperar credenciais de serviço")
4. Após a nova credencial ser criada, clique em **Visualizar credenciais** sob o cabeçalho **AÇÕES** dessa credencial. O conteúdo da credencial é semelhante a este exemplo:

    ```
    {
      "service_instance_id": "60cc757d-1234-7866-9893-491cec2d0eaa",
      "service_instance_token": "T8-W0rHv_vEya63P8KcVUwxAXXbDmihGcDRD5AcHVXGn6S2jbxx2LikPz8w26c3k",
      "description": "This token can be used with the IBP APIs to create or join a network. It can only be used once."
    }
    ```

    O `service_instance_id` serve como o ID do usuário de autenticação básica e o `service_instance_token` serve como a senha de autenticação básica. Use esses valores como a credencial de autenticação básica ao chamar as APIs **Criar rede** ou **Associar rede**.

    Se você criar uma rede de blockchain antes de seguir as etapas anteriores para criar uma credencial de serviço, o conteúdo da credencial também incluirá as informações de rede e será semelhante a este exemplo:

    ```
    {
      "PeerOrg1": {
        "url": "https://ibmblockchain_xyz.ng.bluemix.net",
        "network_id": "92d511f7e587413c8a9848fdae595ef2",
        "key": "PeerOrg1",
        "secret": "T8eUA65l-qtznUHL10KzQ7IK-3BVWWfHu5-hpCiDdXCRQyNfeyIm1p5NT7g17l6U"
      }
    }
    ```

    **Nota**: para a API **Convidar**, `key` serve como o ID do usuário de autenticação básica e `secret` como a senha de autenticação básica.


## Verificando locais de rede disponíveis
{: #swagger-network-check-location}

É possível usar APIs para criar redes de blockchain apenas em locais de rede disponíveis. Antes de criar uma rede, use a API a seguir para obter uma lista atual de locais de rede disponíveis. Nenhuma credencial é necessária para executar esta API.

```
https://ibmblockchain-v2.ng.bluemix.net/api/v1/network-locations/available
```
{:codeblock}

É retornada uma lista de locais de rede disponíveis que é semelhante ao exemplo a seguir:

```
{
  "DAL": {
    "location_id": "DAL",
    "description": "Dallas",
    "status": "disponível"
    "swagger_url": "https://ibmblockchain-v2-dal.4.secure.blockchain.ibm.com/api-docs"
  },
  "FFT": {
    "location_id": "FFT",
    "description": "Frankfurt",
    "status": "disponível"
    "swagger_url": "https://ibmblockchain-v2-fft.2.secure.blockchain.ibm.com/api-docs"
  },
  "TOR": {
    "location_id": "TOR",
    "description": "Toronto",
    "status": "disponível"
    "swagger_url": "https://ibmblockchain-v2-tor.1.secure.blockchain.ibm.com/api-docs"
  }
}
```

Se você planeja criar uma rede, selecione o local no qual gostaria de criar a sua rede por meio da lista retornada pela API. Observe que o ``location_id`` e a ``swagger_url`` estão associados a esse local.

Se você planeja se associar à rede, tome nota da ``swagger_url`` associada com o ``location_id`` especificado em seu e-mail de convite.

A ``swagger_url`` representa o terminal de API que você usará ao criar ou se associar a uma rede usando as APIs abaixo.


## Criando uma rede

**Nota**: se você usar o Starter Plan, não precisará criar uma rede com APIs porque uma rede padrão será fornecida quando você criar a instância de serviço de blockchain no {{site.data.keyword.cloud_notm}}.

Se você usar o Enterprise Plan, precisará concluir duas etapas para criar uma rede com APIs.

1. Crie uma instância de serviço do blockchain no {{site.data.keyword.cloud_notm}} com o Enterprise Plan<!-- or Enterprise Plus Plan-->.  Recupere o seu ID da instância de serviço e o token como o nome do usuário de autenticação básica e a senha. Para obter mais informações, veja [Recuperando a credencial de autenticação básica para a API](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-retrieve-id-token).

2. Chame a API **Criar rede** usando essas credenciais de serviço. Emita essa API com relação à api ``swagger_url`` recuperada de [Verificando locais de rede disponíveis](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-check-location). Navegue para o ``swagger_url link`` para usar a UI do Swagger para emitir a API Criar rede ou emitir programaticamente o comando usando o endereço da URL sem ``/api-docs``. Por exemplo,

    ```
    https://ibmblockchain-v2-tor.1.secure.blockchain.ibm.com/api/v1/networks
    ```
    {:codeblock}

**Parâmetros**:
- `location_id`: o ID de um local de rede disponível. Especifique o valor do `loation_id` anotado de [Verificando locais de rede disponíveis](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-check-location).
- `company_name`: seu identificador como um membro na rede.
- `email`: seu endereço de e-mail para receber notificações.
- `peers`: número de peers que você deseja criar para esse membro. Os valores válidos são 0-6. Também será possível criar peers para o membro, posteriormente, na IU do Monitor de rede.
- `ledger_type`: o tipo de livro-razão dessa rede. Os valores válidos são levelDB e couchDB. **levelDB** é o valor padrão.


## Convidar novos membros a uma rede

Após você criar uma rede de blockchain, será possível convidar outros membros para se associarem à sua rede. É necessário especificar o ID da rede à qual você deseja convidar novos membros a se associarem. A credencial de autenticação básica que é necessária para convidar um membro é diferente daquela que é usada na API **Criar rede**. <!--In order to get the basic auth information, you need to follow the same steps in "Retrieving basic auth information for API". --> É possível obter a credencial para convidar um membro com a API **Recuperar credenciais de rede** na [IU do Swagger](/docs/services/blockchain/howto/swagger_apis.html#ibp-swagger-retrieving-network-credentials) ou [recuperar informações de autenticação básica para a API](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-retrieve-id-token) de sua instância de serviço no {{site.data.keyword.cloud_notm}}.

```
/invite /networks/ {
```
{:codeblock}

**Parâmetros**:
- `email`: o endereço de e-mail do membro a ser convidado para a rede.
- `company_name`: o identificador que você define para o membro a se associar à rede. Os membros convidados poderão mudar os seus identificadores quando eles se associarem à rede.

Os membros convidados receberão um convite por e-mail com instruções sobre como se associarem à sua rede.


## Participando de uma rede

Se você for convidado para se associar a uma rede de blockchain, receberá um e-mail de convite de rede que incluirá o `location_id` e o `network id`.

1. Antes de se associar à rede, você precisa criar uma instância de serviço do {{site.data.keyword.blockchainfull_notm}} Platform e recuperar o seu ID da instância de serviço e o token como o nome do usuário de autenticação básica e a senha. Para obter mais informações, veja [Recuperando informações de autenticação básica para a API](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-retrieve-id-token).

2. [Verifique os locais de rede disponíveis](/docs/services/blockchain/howto/create_join_network_with_apis.html#swagger-network-check-location) para obter a `swagger_url` para o `location_id` em seu e-mail de convite. Será algo parecido com:

    ```
    https://ibmblockchain-v2-tor.1.secure.blockchain.ibm.com/api-docs
    ```
    {:codeblock}

3. Navegue para a sua ``swagger_url`` para usar a UI do Swagger para emitir a API de Junção ou enviar programaticamente a Solicitação de junção usando a ``swagger_url``. Substitua ``/api-docs`` por ``/api/v1/networks/[network_id]]/join``
e preencha o `network_id` usando o valor de seu e-mail de convite. A URL resultante seria semelhante a:

    ```
    https://ibmblockchain-v2-tor.1.secure.blockchain.ibm.com/api/v1/networks/56102acee0e4487889ef09db681bada0/join
    ```
    {:codeblock}

    **Nota**: para a API **Junção**, use o ID e token da instância de serviço que você recuperar na etapa 1 como o nome do usuário de autenticação básica e a senha.

**Parâmetros**:
- `company_name`: seu identificador como um membro na rede. Isso substituirá o nome designado pelo anfitrião.
- `email`: seu endereço de e-mail para receber notificações.  Isso deverá corresponder ao endereço de e-mail na notificação de convite.
- `peers`: número de peers que você deseja criar para esse membro. Os valores válidos são 0 e 6. Também é possível criar mais peers para o seu membro, posteriormente, no Monitor de rede.
