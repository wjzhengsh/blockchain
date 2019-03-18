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
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Implementando redes de negócios no Starter e no Enterprise Plan
{: #deploying-a-business-network}

O {{site.data.keyword.IBM}} não fornece suporte para redes que usam o Hyperledger Composer na produção, incluindo a CLI do Composer, as APIs JavaScript, o servidor REST e o Web Playground.
{:note}

As [redes de negócios](/docs/services/blockchain/glossary.html#glossary-business-network) podem ser desenvolvidas e implementadas nas redes do Starter e do Enterprise Plan usando o ambiente de desenvolvedor do {{site.data.keyword.blockchainfull_notm}} Platform e o conjunto de ferramentas do desenvolvedor do Hyperledger Composer.
{:shortdesc}

Ao usar o ambiente de desenvolvedor, é possível modelar e testar rapidamente as redes de negócios de blockchain e implementá-las em uma rede do Starter ou do Enterprise Plan do {{site.data.keyword.blockchainfull_notm}} Platform.

## Implementando uma rede de negócios para o Starter Plan
{: #deploying-a-business-network-starter-plan}

### Antes de iniciar
{: #deploying-a-business-network-before-begin}

Assegure-se de que você tenha lido [Sobre o Starter Plan](/docs/services/blockchain/starter_plan.html#starter-plan-about) e tenha criado uma rede do Starter Plan seguindo as instruções em [Introdução ao Starter Plan](/docs/services/blockchain/get_start_starter_plan.html#getting-started-with-starter-plan).

Assegure-se de ter instalado o Node v8.9 ou superior, o npm v5.x e o Hyperledger Composer:

- Se sua rede estiver no Fabric versão 1.2, use o Hyperledger Composer v0.20.x.
- Se sua rede estiver no Fabric versão 1.1, use o Hyperledger Composer v0.19.x.

É possível localizar a versão do Fabric abrindo a [janela Preferências de rede](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) em seu Network Monitor.

### Etapa um: Recuperar o segredo do administrador
{: #deploying-a-business-network-retrieve-admin-secret}

1. Na tela de visão geral do Starter Plan, clique em **Perfil de conexão** e, em seguida, faça download. Renomeie esse arquivo para 'connection-profile.json'.

2. Mova esse arquivo para estar no mesmo diretório que o seu arquivo `.bna`.

3. Dentro do perfil de conexão, vá até o fim até você ver 'registrar'. Dentro de 'registrar', sob 'enrollId' há uma propriedade **enrollSecret**. Recupere o segredo e salve uma cópia dele.

    ![Retrieve admin secret](images/get_enroll_secret.gif "Retrieve admin secret")

### Etapa dois: criar uma placa de autoridade de certificação
{: #deploying-a-business-network-CA-card}

O segredo recuperado na etapa anterior será usado para criar uma placa de rede de negócios para a autoridade de certificação (CA). O cartão de autoridade de certificação será, então, importado e usado para trocar o **enrollSecret** por certificados válidos da autoridade de certificação do Starter Plan.

1. Usando o **enrollSecret** observado da etapa um, execute o comando a seguir para criar o cartão de rede de negócios de autoridade de certificação:

   ```
   composer card create -f ca.card -p connection-profile.json -u admin -s enrollSecret
   ```
   {:pre}

  Substitua `enrollSecret` no comando anterior pelo segredo do administrador recuperado do perfil de conexão.

2. Importe o cartão usando o comando a seguir:

   ```
   composer card import -f ca.card -c ca
   ```
   {:codeblock}

3. Agora que o cartão foi importado, ele poderá ser usado para trocar o **enrollSecret** por certificados válidos da autoridade de certificação. Execute o comando a seguir para solicitar certificados da autoridade de certificação:

   ```
   composer identity request --card ca --path ./credentials -u admin -s enrollSecret
   ```
   {:codeblock}

  Substitua `enrollSecret` no comando anterior pelo segredo do administrador recuperado do perfil de conexão. O comando `composer identity request` cria um diretório `credentials` que contém arquivos de certificado `.pem`.

### Etapa três: incluir os certificados na instância do Starter Plan
{: #deploying-a-business-network-add-certs-to-starter-plan}

Os certificados devem ser incluídos na rede do Starter Plan. Por conveniência, é possível incluí-los usando o Monitor de rede do {{site.data.keyword.blockchainfull_notm}} Platform. Os certificados devem ser incluídos e, em seguida, os peers devem ser reiniciados e, então, os certificados devem ser sincronizados no canal. O certificado necessário é o arquivo `admin-pub.pem` que foi gerado por meio do comando prévio, que está no diretório `credentials`.

1. No monitor de rede do Starter Plan, clique na guia **Membros**, em **Certificados** e em **Incluir certificado**. Acesse o seu diretório `credentials` e copie e cole o conteúdo do arquivo `admin-pub.pem` na caixa de certificado. Envie o certificado e reinicie os peers. Nota: reiniciar os peers leva um minuto.

    ![Incluir certificados](images/add_cert.gif "Incluir certificados")

2. Em seguida, os certificados devem ser sincronizados no canal. Clique na guia **Canais**, em seguida, no botão **Ações** e, em seguida, em **Sincronizar certificado** e **Enviar**.

    ![Sync certificates](images/sync_cert.gif "Sync certificates")

### Etapa quatro: criar uma placa de rede de negócios de administrador
{: #deploying-a-business-network-create-admin-card}

Agora que os certificados corretos foram sincronizados com os peers, é possível criar placas de rede de negócios que tenham as permissões para instalar o tempo de execução do Hyperledger Composer e iniciar o chaincode.

1. Crie um cartão do administrador com o administrador do canal e funções administrativas de peer usando o comando a seguir:

   ```
   composer card create -f adminCard.card -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
   ```
   {:codeblock}

   Este cartão será usado para implementar uma rede de negócios para o Starter Plan.

2. Importe o cartão criado na etapa anterior usando o comando a seguir:

   ```
   composer card import -f adminCard.card -c adminCard
   ```
   {:codeblock}

### Etapa cinco: instalar e iniciar a rede de negócios
{: #deploying-a-business-network-install-start-network}

Em seguida, o cartão criado na etapa anterior pode ser usado para instalar e iniciar uma rede de negócios. Para este guia, nós instalaremos a amostra de rede de manufatura de veículo; use a amostra de manufatura de veículo ou instale a sua própria rede de negócios, mas certifique-se de mudar os nomes da rede de negócios especificados nos comandos. O comando para iniciar uma rede de negócios também criará um cartão. Para o Starter Plan, esse cartão deve ser excluído, o comando de exemplo fornecido nomeia esse cartão `delete_me.card` para que possa ser facilmente distinguido.

1. Instale o tempo de execução do Hyperledger Composer com o comando a seguir:

   ```
   composer network install -c adminCard -a vehicle-manufacture-network.bna
   ```
   {:codeblock}

   Observe o número da versão de rede de negócios que é retornado quando você executa esse comando. Ele será necessário na próxima etapa.

2. Inicie a rede de negócios com o comando abaixo. Se você receber um erro, espere um minuto e tente novamente. Use o número da versão da última etapa depois da opção `-V`.

    ```
    composer network start -c adminCard -n vehicle-manufacture-network -V 0.0.1 -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. Exclua a placa de rede de negócios chamada `delete_me.card`.

4. Crie uma nova placa de rede de negócios e referencie os certificados que foram recuperados anteriormente com o comando a seguir:

   ```
   composer card create -n vehicle-manufacture-network -p connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
   ```
   {:codeblock}

5. Importe a placa de rede de negócios com o comando a seguir:

    ```
    composer card import -f ./admin@vehicle-manufacture-network.card
    ```
    {:codeblock}

A rede de negócios agora está implementada na instância do Starter Plan.

### Etapa seis: executar ping na rede de negócios para assegurar que ela esteja sendo executada corretamente
{: #deploying-a-business-network-ping-business-network}

Execute o comando a seguir para executar ping na rede de negócios:

   ```
   Composer network ping -c admin@vehicle-manufacture-rede
   ```
   {:codeblock}

Para visualizar os logs de chaincode, clique em **Canais** e, em seguida, selecione seu canal. <!-- Click the dropdown arrow to view the logs, or the Actions symbol to view in more detail. --> Clique na guia  ** Chaincode ** . Expenda a linha do chaincode e, em seguida, clique no botão **JSON** ou **Logs**.

<!-- [fN-Yuj](https://i.makeagif.com/media/4-13-2018/fN-Yuj.gif) -->

A rede de negócios foi implementada agora com êxito para a instância do Starter Plan.

## Implementando uma rede de negócios para o Enterprise Plan
{: #deploying-a-business-network-to-enterprise-plan}

As ferramentas de desenvolvedor do {{site.data.keyword.blockchainfull_notm}} Platform ajudam a criar uma **Definição de rede de negócios** que pode, então, ser empacotada em um archive de rede de negócios (`.bna`). O ambiente de desenvolvedor permite implementar arquivos `.bna` em um {{site.data.keyword.blockchain}} local ou de nuvem para desenvolvimento e compartilhamento.

Este tutorial aborda a próxima etapa de um ciclo de vida de rede de negócios, ou seja, ativar a sua rede de negócios implementando o `.bna` para o {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan.

### Antes de iniciar
{: #deploying-a-business-network-enterprise-plan-before-begin}

Assegure-se de que você tenha instalado o ambiente do desenvolvedor do {{site.data.keyword.blockchainfull_notm}} e esteja confortável com o desenvolvimento e a implementação de redes de negócios. Orientação sobre como escrever redes de negócios está disponível na [Documentação do Hyperledger Composer](https://hyperledger.github.io/composer/latest/business-network/business-network-index).

É necessário o acesso a uma instância do Enterprise Plan do {{site.data.keyword.blockchainfull_notm}} Platform e ter criado seus peers antecipadamente. Para obter mais informações sobre o {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan, veja a [Visão geral do Enterprise Plan](/docs/services/blockchain/enterprise_plan.html#enterprise-plan-about).

### Etapa um: criar um perfil de conexão para o IBM Blockchain Platform
{: #deploying-a-business-network-create-connection-profile}

1. Crie um diretório para armazenar os seus detalhes de conexão, por exemplo:

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    Cada perfil de conexão deve conter um arquivo `connection.json`. Crie um novo diretório em `.composer-connection-profiles` nessa instância `bmx-hlfv11`. Este será o nome do perfil que você usará ao trabalhar com o Hyperledger Composer e o {{site.data.keyword.blockchainfull_notm}} Platform.

    ```
    mkdir -p ~/.composer-connection-profiles/bmx-hlfv11
    cd ~/.composer-connection-profiles/bmx-hlfv11
    ```
    {:codeblock}

    Você agora deve ter a estrutura de diretório a seguir:

    ```
    /Users/myUserId/.composer-connection-profiles/bmx-hlfv11
    ```

### Etapa dois: Recuperar seu perfil de conexão
{: #deploying-a-business-network-retrieve-connection-profile}

1. No painel do {{site.data.keyword.blockchainfull_notm}} Platform, selecione **Visão geral**, em seguida, **Perfil de conexão** e o botão **Fazer download** para recuperar seu perfil de conexão.

2. Salve o perfil de conexão na estrutura de diretório criada na etapa anterior. Nomeie-o como **connection.json**.

### Etapa três: Incluir informações do canal
{: #deploying-a-business-network-add-channel-information}

1. Inclua o valor do canal no `connection.json` para corresponder ao nome do canal no qual você planeja criar e implementar sua rede de negócios. No modelo de perfil de conexão de exemplo fornecido, o elemento de canais é o seguinte: `"channels": {},`.

### Etapa quatro: preparar seus peers
{: #deploying-a-business-network-prepare-peers}

Esta etapa **DEVE** ser executada antes de criar o canal para implementar uma rede de negócios. Se essa etapa for executada após a criação do canal, uma rede de negócios implementada **poderá não ser iniciada**.
{:note}

No Perfil de Conexão, o documento em **certificateAuthorities** é um atributo **registrar** que contém atributos para **enrollId** e **enrollSecret** no formato a seguir:

  ```
        "registrar": [
            {
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],
  ```

1. Crie um cartão de rede de negócios para a CA usando o comando a seguir:

    ```
    composer card create -f ca.card -p bmx-hlfv11 -u admin -s PA55W0RD12
    ```
    {:codeblock}

  Assegure-se de que você esteja executando o comando no mesmo diretório que seu perfil de conexão.

2. Importe a placa de rede de negócios usando o comando a seguir:

    ```
    composer card import -f ca.card -c ca
    ```
    {:codeblock}

3. Depois que o cartão for importado, ele poderá ser usado para adquirir os certificados da autoridade de certificação usando o comando a seguir:

    ```
    composer identity request --card ca --path ./credentials -u admin -s PA55W0RD12
    ```
    {:codeblock}

    O comando `composer identity request` cria um diretório de credenciais que contém os arquivos de certificado relevantes.

4. Selecione **Membros** no menu de navegação e, em seguida, selecione a opção de menu **Certificados** e clique no botão **Incluir certificado**.

5. Insira um nome exclusivo para esse certificado no campo **Nome**; esse nome não pode incluir traços ou hifens.

6. Abra o arquivo `admin-pub.pem` criado anteriormente e copie o conteúdo desse arquivo no campo **Chave** e pressione o botão **Enviar**.

7. Use a interface com o usuário para parar os peers e, em seguida, inicie os peers.

8. O certificado deve agora aparecer na lista de certificados.

### Etapa cinco: Criar seu Canal
{: #deploying-a-business-network-create-your-channel}

1. Selecione **Canais** no menu de navegação no painel esquerdo e clique no botão **Novo canal**.

2. Insira um Nome de canal e uma descrição opcional e clique em **Avançar**. Observe que o Nome de canal deverá corresponder ao nome que você especificar em seu perfil de conexão para o atributo do canal.

3. Dê permissões conforme necessário e clique em **Avançar**.

4. Selecione a política do número de operadores que precisam aceitar atualizações do canal e enviar a solicitação.

5. Agora você deve ser levado à seção **Notificações** na qual deve haver uma nova solicitação para revisão. Clique no botão **Revisar solicitação**.

6. Clique no botão **Aceitar** e você será levado de volta para a seção **Notificações** na qual será possível ver que a solicitação poderá ser enviada agora. Clique no botão **Enviar solicitação** para abrir o diálogo de envio e, em seguida, clique no botão **Enviar**. O novo canal foi criado.

7. Selecione **Canais** no menu de navegação. O novo canal na lista de canais e deve ter "Nenhum peer incluído ainda". Clique no menu Ações ao lado dele e selecione **Associar peers**. Marque as caixas de seleção próximas aos Peers que você deseja incluir e pressione **Incluir selecionados**.

### Etapa seis: criar uma nova identidade para administrar sua rede de negócios
{: #deploying-a-business-network-add-new-identity-enterprise-plan}

Crie o cartão de rede de negócios usando os certificados solicitados. Esse cartão de rede de negócios terá a autoridade para instalar o chaincode nos peers que possuem o certificado público transferido por upload e será um emissor para as autoridades de certificação.

1. Para criar a placa, execute o comando a seguir:

    ```
    composer card create -f adminCard.card -p bmx-hlfv11 -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin
    ```
    {:codeblock}

    Em que `bmx-hlfv11` é o nome do perfil que você criou anteriormente.

2. Após a criação do cartão, importe-o usando o comando a seguir:

    ```
    composer card import -f adminCard.card -c adminCard
    ```
    {:codeblock}

    Agora estamos prontos para implementar o seu arquivo `.bna` no {{site.data.keyword.blockchainfull_notm}} Platform.


### Etapa sete: implementar a rede de negócios
{: #deploying-a-business-network-deploy-business-network-enterprise-plan}

Agora você pode implementar o arquivo `.bna` no {{site.data.keyword.blockchainfull_notm}} Platform.

1. Para usar o cartão que foi criado na etapa anterior, é necessário primeiro instalar e, em seguida, iniciar a rede de negócios. Instale a rede de negócios usando o comando a seguir:

   ```
   composer network install -c adminCard -a myNetwork.bna
   ```
   {:codeblock}

2. Depois de instalar a rede de negócios, inicie a rede de negócios usando o comando a seguir:

    ```
    composer network start -c adminCard -n myNetwork -V networkVersion -A admin -C ./credentials/admin-pub.pem -f delete_me.card
    ```
    {:codeblock}

3. Para verificar se a rede de negócios foi implementada corretamente, crie uma identidade e um cartão de rede de negócios associado que possa ser usado para executar ping na rede.

    ```
    composer card create -f admin.card -p bmx-hlfv11 -u admin -n myNetwork -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem
    ```
    {:codeblock}

4. Importe a placa de rede de negócios usando o comando a seguir:

    ```
    composer card import -f admin.card
    ```
    {:codeblock}

5. Execute ping na rede para verificar se a rede está em execução:

    ```
    composer network ping -c admin@myNetwork
    ```
    {:codeblock}
