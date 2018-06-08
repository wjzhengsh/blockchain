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
{:tip: .tip}

# Implementando uma rede de negócios no Enterprise Plan
{: #deploying-a-business-network}

Ferramentas de desenvolvedor do {{site.data.keyword.blockchainfull}} Platform: Develop ajudam a criar uma **Definição de rede de negócios** que pode ser empacotada em um archive de rede de negócios (`.bna`). O ambiente de desenvolvedor permite implementar arquivos `.bna` em um {{site.data.keyword.blockchain}} local ou de nuvem para desenvolvimento e compartilhamento.

Este tutorial aborda a próxima etapa de um ciclo de vida de rede de negócios, ou seja, ativar a sua rede de negócios implementando o `.bna` para o {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan.

## Antes de iniciar

Assegure-se de ter instalado o ambiente de desenvolvedor do {{site.data.keyword.blockchainfull}}: Develop e de estar confortável com o desenvolvimento e a implementação de redes de negócios. Orientação sobre como escrever redes de negócios está disponível na [Documentação do Hyperledger Composer](https://hyperledger.github.io/composer/latest/business-network/business-network-index).

É necessário acesso a uma instância do {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan. Para obter mais informações sobre o {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan, veja a [Visão geral do Enterprise Plan](./enterprise_plan.html).

## Etapa um: Crie um perfil de conexão para o {{site.data.keyword.blockchainfull_notm}} Platform

1. Crie um diretório para armazenar os seus detalhes de conexão, por exemplo:

        /Users/myUserId/.composer-connection-profiles/bmx-hlfv1

    Cada perfil de conexão deve conter um arquivo `connection.json`. Crie um novo diretório em `.composer-connection-profiles`, nessa instância `bmx-hlfv1`. Este será o nome do perfil que você usará ao trabalhar com o Hyperledger Composer e o {{site.data.keyword.blockchainfull_notm}} Platform.

        mkdir -p ~/.composer-connection-profiles/bmx-hlfv1
        cd ~/.composer-connection-profiles/bmx-hlfv1

2. Você agora deve ter a estrutura de diretório a seguir:

        /Users/myUserId/.composer-connection-profiles/bmx-hlfv1

    Crie um arquivo no diretório recém-criado e o denomine `connection.json`. É possível usar o modelo a seguir para o seu arquivo `connection.json`:

        {
            "name": "bmx-hlfv1",
            "description": "A description for a V1 Profile",
            "type": "hlfv1",
            "orderers": [
                {
                    "url": "grpcs://abca.4.secure.blockchain.ibm.com:12345"
                }
            ],
            "ca": {
                "url": "https://abc.4.secure.blockchain.ibm.com:98765",
                "name": "PeerOrg1CA"
            },
            "peers": [
                {
                    "requestURL": "grpcs://abcd.4.secure.blockchain.ibm.com:22222",
                    "eventURL": "grpcs://abcd.4.secure.blockchain.ibm.com:33333"
                }
            ],
            "keyValStore": "/Users/jeff/.composer-credentials-mychannel-hsbn",
            "channel": "mychannel",
            "mspID": "PeerOrg1",
            "globalCert": "-----BEGIN CERTIFICATE-----\r\n...LotsOfStuff\r\n-----END CERTIFICATE-----\r\n-----BEGIN CERTIFICATE-----\r\nMorestuff\r\n-----END CERTIFICATE-----\r\n",
            "timeout": 300
        }

    Você preencherá o arquivo recém-criado `connection.json` com atributos que são fornecidos por meio de seu painel do {{site.data.keyword.blockchainfull_notm}} Platform. No seu painel, selecione **Visão geral** e, em seguida, clique no botão **Perfil de conexão** para exibir o terminal e as informações de certificado para os membros do canal.

## Etapa dois: Incluindo informações do solicitante

1. Agora, podemos começar a modificar o modelo com as informações que o Perfil de conexão fornece. Pode haver múltiplos solicitantes no Perfil de conexão, mas apenas um é necessário para um arquivo `connection.json`.

    Substitua os valores url do solicitante no modelo pelas informações relevantes de seu Perfil de conexão no formato a seguir:

        “url”: “grpcs://abca.4.secure.blockchain.ibm.com:12345”

## Etapa três: Incluindo as informações da autoridade de certificação

1. Substitua o valor ca em `connection.json` por **url** e o **caName** de uma das entradas na seção **certificateAuthorities**.

## Etapa quatro: Incluindo informações do peer

1. A **requestURL** e a **eventURL** para cada peer devem ser configuradas. Substitua o atributo **url** pelo valor **url** que está localizado em seu Perfil de conexão. Substitua o atributo **eventURL** pelo **eventUrl** localizado no seu Perfil de conexão. Depois de fazer as mudanças, a seção peers de `connection.json` deve ter o formato a seguir:

        "peers": [{
              "requestURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345",
              "eventURL": "grpcs://abca.4.secure.blockchain.ibm.com:12345"

## Etapa cinco: Incluindo informações keyValStore

1. Configure o atributo **keyValStore** para apontar para o diretório apropriado. Crie um diretório para usar para o seu **keyValStore**. Por exemplo, um novo diretório sob o seu diretório inicial chamado `.composer-credentials-mychannel`. Certifique-se de que o atributo **keyValStore** aponte para o seu diretório recém-criado no formato a seguir:

        "keyValStore": "/Users/myUserId/.composer-credentials-mychannel",

## Etapa seis: Incluindo informações de canal

1. Substitua o valor de canal no `connection.json` para corresponder ao nome do canal no qual você planeja criar e implementar a sua rede de negócios.

## Etapa sete: Incluindo um mspID

O valor **mspID** em seu arquivo `connection.json` deve ser configurado para o mspID de sua organização. O Perfil de conexão fornece uma lista das organizações com os seus valores de mspid associados. É necessário usar o valor do atributo **mspid** de sua organização.

## Etapa oito: Incluindo o globalCert
1. O {{site.data.keyword.blockchainfull_notm}} Platform usa um certificado TLS comum para os solicitantes e peers. Para cada solicitante e peer, há um atributo **tlsCACerts** que todos contêm o mesmo certificado. Substitua o valor simulado no arquivo `connection.json` pelo valor **tlsCACerts**. Ele deve tomar o formato a seguir:

        "globalCert": "-----BEGIN CERTIFICATE-----\r\.......

## Etapa nove: Preparando os seus peers

**Observe**: essa etapa **deve** ser executadas antes de você criar o canal no qual implementar uma rede de negócios. Se essa etapa for executada após a criação do canal, uma rede de negócios implementada **não será iniciada**.

No documento Perfil de conexão, em **certificateAuthorities**, está um atributo **registrar** que contém atributos para **enrollId** e **enrollSecret** no formato a seguir:

        "registrar": [
            {
                "affiliation": "org1",
                "enrollId": "admin",
                "enrollSecret": "PA55W0RD12"
            }
        ],

1. Solicite os certificados usando o comando a seguir:

        composer identity request -p bmx-hlfv1 -i admin -s PA55W0RD12

    Isso faz download de três arquivos no diretório `.identityCredentials` sob o seu diretório inicial. Os arquivos de interesse são baseados no **enrollId**. Então, no exemplo acima, haverá dois arquivos que são chamados **admin-pub.pem** e **admin-priv.pem**

3. Selecione **Membros** no menu de navegação e, em seguida, selecione a opção de menu **Certificados** e clique no botão **Incluir certificado**.

4. Insira um nome exclusivo para esse certificado no campo **Nome**; esse nome não pode incluir traços ou hifens.

5. Abra o arquivo `admin-pub.pem` criado anteriormente e copie o conteúdo desse arquivo no campo **Chave** e pressione o botão **Enviar**.

6. Use a interface com o usuário para os peers e, em seguida, inicie os peers.

7. O certificado deve agora aparecer na lista de certificados.

## Etapa dez: Criando o seu canal

1. Selecione **Canais** no menu de navegação no painel esquerdo e clique no botão **Novo canal**.

2. Insira um Nome de canal e uma descrição opcional e clique em **Avançar**. Observe que o Nome de canal deverá corresponder ao nome que você especificar em seu perfil de conexão para o atributo do canal.

3. Dê permissões conforme necessário e clique em **Avançar**.

4. Selecione a política do número de operadores que precisam aceitar atualizações do canal e enviar a solicitação.

5. Agora você deve ser levado à seção **Notificações** na qual deve haver uma nova solicitação para revisão. Clique no botão **Revisar solicitação**.

6. Clique no botão **Aceitar** e você será levado de volta para a seção **Notificações** na qual será possível ver que a solicitação poderá ser enviada agora. Clique no botão **Enviar solicitação** para abrir o diálogo de envio e, em seguida, clique no botão **Enviar**. O novo canal foi criado.

7. Selecione **Canais** no menu de navegação. O novo canal na lista de canais e deve ter "Nenhum peer incluído ainda". Clique no menu Ações ao lado dele e selecione **Associar peers**. Marque as caixas de seleção próximas aos Peers que você deseja incluir e pressione **Incluir selecionados**.

## Etapa onze: Importando uma nova identidade para administrar a sua rede de negócios

Crie uma identidade no Composer usando os certificados que foram solicitados anteriormente. Essa nova identidade terá a autoridade para instalar um chaincode nos peers que tiverem os seus certificados públicos transferidos por upload e será uma emissora para as autoridades de certificação.

1. Para criar a nova identidade, execute o comando a seguir:

        composer identity import -p bmx-hlfv1 -u admin -c ~/.identityCredentials/admin-pub.pem -k ~/.identityCredentials/admin-priv.pem

    Em que `bmx-hlfv1` é o nome do perfil criado anteriormente. Agora estamos prontos para implementar o seu arquivo `.bna` no {{site.data.keyword.blockchainfull_notm}} Platform.


## Etapa doze: Implementando a rede de negócios

Agora você pode implementar o arquivo `.bna` no {{site.data.keyword.blockchainfull_notm}} Platform.

1. Com a identidade criada na etapa anterior, implemente a rede de negócios usando o comando a seguir:

        composer network deploy -a myNetwork.bna -p bmx-hlfv1 -i admin -s anyString
