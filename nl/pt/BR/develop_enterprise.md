---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Implementando uma rede de negócios no Enterprise Plan
{: #deploying-a-business-network}

**A IBM recomenda usar o Hyperledger Composer exclusivamente para demos e provas de conceitos. A IBM não fornece suporte para redes que usam o Hyperledger Composer na produção, incluindo a CLI do Composer, as APIs JavaScript, o servidor REST e o Web Playground.**

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Ferramentas de desenvolvedor do {{site.data.keyword.blockchainfull}} Platform: Develop ajudam a criar uma **Definição de rede de negócios** que pode ser empacotada em um archive de rede de negócios (`.bna`). O ambiente de desenvolvedor permite implementar arquivos `.bna` em um {{site.data.keyword.blockchain}} local ou de nuvem para desenvolvimento e compartilhamento.

Este tutorial aborda a próxima etapa de um ciclo de vida de rede de negócios, ou seja, ativar a sua rede de negócios implementando o `.bna` para o {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan.

## Antes de iniciar

Assegure-se de que você tenha instalado o ambiente do desenvolvedor do {{site.data.keyword.blockchainfull_notm}} e esteja confortável com o desenvolvimento e a implementação de redes de negócios. Orientação sobre como escrever redes de negócios está disponível na [Documentação do Hyperledger Composer](https://hyperledger.github.io/composer/latest/business-network/business-network-index).

É necessário o acesso a uma instância do Enterprise Plan do {{site.data.keyword.blockchainfull_notm}} Platform e ter criado seus peers antecipadamente. Para obter mais informações sobre o {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan, veja a [Visão geral do Enterprise Plan](./enterprise_plan.html).

## Etapa um: Crie um perfil de conexão para o {{site.data.keyword.blockchainfull_notm}} Platform

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

## Etapa dois: recuperar o perfil de conexão

1. No painel do {{site.data.keyword.blockchainfull_notm}} Platform, selecione **Visão geral**, em seguida, **Perfil de conexão** e o botão **Fazer download** para recuperar seu perfil de conexão.

2. Salve o perfil de conexão na estrutura de diretório criada na etapa anterior. Nomeie-o como **connection.json**.

## Etapa três: incluindo informações do canal

1. Inclua o valor do canal no `connection.json` para corresponder ao nome do canal no qual você planeja criar e implementar sua rede de negócios. No modelo de perfil de conexão de exemplo fornecido, o elemento de canais é o seguinte: `"channels": {},`.

## Etapa quatro: preparando seus peers

**Observe**: essa etapa **deve** ser executadas antes de você criar o canal no qual implementar uma rede de negócios. Se essa etapa for executada após a criação do canal, uma rede de negócios implementada **poderá não ser iniciada**.

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

## Etapa cinco: criando seu canal

1. Selecione **Canais** no menu de navegação no painel esquerdo e clique no botão **Novo canal**.

2. Insira um Nome de canal e uma descrição opcional e clique em **Avançar**. Observe que o Nome de canal deverá corresponder ao nome que você especificar em seu perfil de conexão para o atributo do canal.

3. Dê permissões conforme necessário e clique em **Avançar**.

4. Selecione a política do número de operadores que precisam aceitar atualizações do canal e enviar a solicitação.

5. Agora você deve ser levado à seção **Notificações** na qual deve haver uma nova solicitação para revisão. Clique no botão **Revisar solicitação**.

6. Clique no botão **Aceitar** e você será levado de volta para a seção **Notificações** na qual será possível ver que a solicitação poderá ser enviada agora. Clique no botão **Enviar solicitação** para abrir o diálogo de envio e, em seguida, clique no botão **Enviar**. O novo canal foi criado.

7. Selecione **Canais** no menu de navegação. O novo canal na lista de canais e deve ter "Nenhum peer incluído ainda". Clique no menu Ações ao lado dele e selecione **Associar peers**. Marque as caixas de seleção próximas aos Peers que você deseja incluir e pressione **Incluir selecionados**.

## Etapa seis: criando uma nova identidade para administrar sua rede de negócios

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


## Etapa sete: implementando a rede de negócios

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
