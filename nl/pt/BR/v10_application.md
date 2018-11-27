---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Desenvolvendo aplicativos
{: #dev_app}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


O {{site.data.keyword.blockchainfull}} Platform fornece APIs que podem ser usadas para conectar aplicativos à rede de blockchain. É possível usar os terminais de API da rede no perfil de conexão para chamar seu chaincode e atualizar ou consultar o livro-razão específico do canal em seus peers. Também é possível usar APIs na [IU do Swagger](howto/swagger_apis.html) para gerenciar nós, canais e membros de sua rede.
{:shortdesc}

É possível usar este tutorial para aprender como acessar as APIs do {{site.data.keyword.blockchainfull_notm}} Platform e usá-las para inscrever-se e registrar seu aplicativo com a rede. Você também aprenderá como interagir com sua rede e emitir transações por meio de seu aplicativo. O tutorial baseia-se no tutorial [Gravando seu primeiro aplicativo ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/write_first_app.html) na documentação do Hyperledger Fabric. Você usará muitos dos mesmos arquivos e comandos do tutorial **Gravando seu primeiro aplicativo**, mas os utilizará para interagir com uma rede no {{site.data.keyword.blockchainfull_notm}} Platform. Este tutorial descreve cada etapa do desenvolvimento do aplicativo usando o Hyperledger Fabric Node SDK. Você também aprenderá como inscrever-se e registrar usuários usando o Fabric CA Client como uma alternativa ao uso do SDK.

Além deste tutorial, é possível usar aplicativos de amostra e o chaincode que o {{site.data.keyword.blockchainfull}} Platform fornece como modelos quando você cria suas próprias soluções de negócios. Para obter mais informações, veja [Implementando aplicativos de amostra](howto/prebuilt_samples.html).

## Pré-requisitos
São necessários os pré-requisitos a seguir antes de poder usar o tutorial **Gravando o seu primeiro aplicativo** no {{site.data.keyword.blockchainfull_notm}} Platform.

- Se você não tiver uma rede de blockchain no {{site.data.keyword.cloud_notm}}, será necessário criar uma com um Starter ou Enterprise Membership Plan. Para obter mais informações, consulte [Criando uma rede do Starter Plan](get_start_starter_plan.html#creating-a-network) ou [Criando uma rede do Enterprise Plan](get_start.html#creating-a-network).

  Depois de entrar no Monitor de rede de sua rede, inclua pelo menos um peer para sua organização na tela "Visão geral". Em seguida, crie pelo menos um canal em sua rede. Para obter mais informações, consulte [Criando um canal](howto/create_channel.html#creating-a-channel). **Observe** que se você usar uma rede do Starter Plan, sua rede já terá um canal com o nome de `defaultchannel` que poderá ser usado para implementar o chaincode.

- Instale as ferramentas necessárias para fazer download de amostras do Hyperledger Fabric e para usar o Node SDK.
  * [Curl ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/prereqs.html#install-curl "Curl") ou [Git ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git "Git")
  * [Node.js ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/prereqs.html#node-js-runtime-and-npm "Node.js")

- Instale as amostras do Hyperledger Fabric fazendo download do diretório `fabric-samples`. É possível seguir o [guia de introdução ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/samples.html) na documentação do Hyperledger Fabric.

  Depois de fazer download do diretório `fabric-samples` em sua máquina local, navegue para `fabric-samples/fabcar`. É possível fazer uma cópia desse diretório e renomeá-la para que seja possível tentar e testar o aplicativo de amostra em um diretório limpo.

  No diretório `fabcar`, execute o comando `npm install` para instalar os pacotes necessários para usar o Fabric SDK, que incluem `fabric-client` e `fabric-ca-client`.

- Instale e instancie o chaincode fabcar em seu canal usando o [Monitor de rede](howto/install_instantiate_chaincode.html#installchaincode). É possível localizar o chaincode fabcar na pasta `fabric-samples` em `fabric-samples > chaincode > fabcar > go`.

- Recupere o Perfil de conexão de sua rede na tela "Visão geral" do Monitor de rede. Salve o Perfil de conexão em seu diretório `fabcar` e renomeie-o para `creds.json`.

## Utilizando os SDKs do Fabric
{: #using-the-fabric-sdks}

Os SDKs do Hyperledger Fabric fornecem um conjunto poderoso de APIs que permitem que os aplicativos interajam com redes de blockchain. É possível localizar a lista mais recente de idiomas suportados na [documentação da Comunidade do Hyperledger Fabric SDK ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/getting_started.html#hyperledger-fabric-sdks "documentação da Comunidade do Hyperledger Fabric SDK"){:new_window}. Recomenda-se usar o Node SDK ou o Java SDK com o {{site.data.keyword.blockchainfull_notm}} Platform. É possível saber mais sobre as APIs fornecidas pelos SDKs nos repositórios individuais dos SDKs.

Este tutorial usa o [Node SDK ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/ "Node SDK"){:new_window} para registrar e inscrever seu aplicativo e, em seguida, usar o aplicativo para emitir transações chamando e consultando o chaincode. Este tutorial descreve as informações que você precisa fornecer para o SDK para que seu aplicativo possa se conectar à rede de blockchain. Ele também apresenta algumas das APIs que podem ser usadas e como o SDK interage e envia transações para a sua rede de blockchain.

## Incluindo terminais de API de rede em seu aplicativo
{: #api-endpoints}

É necessário fornecer ao aplicativo os terminais de API de recursos de rede específicos, incluindo os nós do solicitador, da CA e do peer, na rede de blockchain no {{site.data.keyword.cloud_notm}}. O seu aplicativo pode interagir com a rede por meio desses terminais de API. É possível localizar os terminais de API no Perfil de conexão de sua rede. O Perfil de conexão está no formato JSON e contém as informações sobre terminais de API e os IDs/segredos de inscrição para seus recursos de rede.

1. Recupere as informações do terminal de API de seus recursos de rede por meio de seu Monitor de rede com um dos métodos a seguir:
  * Na tela "Visão geral", clique em **Perfil de conexão**. O Perfil de conexão contém um conjunto completo de informações sobre terminais de API de todos os recursos de rede.
![Perfil de conexão no Monitor de rede](images/service_credentials.png "Perfil de conexão no Monitor de rede")

  * Se você tiver o chaincode em execução na rede, será possível obter as informações sobre terminais de API específicas para o chaincode. Na tela "Canais", clique na linha do canal na qual o chaincode está em execução para abrir a tela de canal específica. Em seguida, localize o chaincode e clique no botão **JSON**.
    ![Terminais de API por chaincode](images/channel_chaincode.png "Terminais de API por chaincode")

2. Localize as informações sobre terminais de API de seus recursos de rede, que são semelhantes à URL da linha `peer1-org1` no exemplo a seguir:
  ```
    "peers": {
        "org1-peer1": {
            "url": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31002",
            "eventUrl": "grpcs://n7413e3b503174a58b112d30f3af55016-org1-peer1.us3.blockchain.ibm.com:31003",
                  ...
  ```
  {:codeblock}

  **Nota**: talvez você queira destinar recursos de rede fora de sua organização com seu aplicativo. Por exemplo, se uma [política de endosso](howto/install_instantiate_chaincode.html#specifying-chaincode-endorsement-policies) do chaincode requerer endossos de outras organizações no canal, será necessário obter as informações sobre terminais de seus peers e os certificados TLS que acompanham. É possível localizar essas informações na seção de peers do Perfil de conexão. No entanto, é necessário entrar em contato com o administrador das outras organizações sobre quais peers eles associaram a canais específicos.

3. Vincule as informações do terminal de API a um arquivo de configuração de seu aplicativo, conforme mostrado no exemplo a seguir:
  ```
  grpcs://n7413e3b503174a58b112d30f3af55016-orderer.us3.blockchain.ibm.com:31001
  ```
  {:codeblock}

  Também é possível enviar [solicitações de HEAD](howto/monitor_network.html#monitor-nodes) para esses terminais para verificar a disponibilidade de seus recursos de rede.

  Se você estiver usando os SDKs do Fabric, também poderá se conectar à rede usando seu Perfil de conexão. Este tutorial fornece as informações sobre terminais de sua rede para o SDK manualmente. No entanto, é possível localizar um tutorial e orientação sobre [como usar seu perfil de conexão com o SDK](#using-your-connection-profile-with-the-sdk) em uma seção posterior.

## Inrolando seu aplicativo
{: #enroll-app}

Antes de conectar um aplicativo à sua rede no {{site.data.keyword.blockchainfull_notm}} Platform, é necessário provar a autenticidade de seu aplicativo para a rede. Não vamos nos aprofundar nos detalhes do x509 e da infraestrutura de chave pública, pois é possível localizar muitos [recursos externos ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://en.wikipedia.org/wiki/Public_key_infrastructure){:new_window} para isso. Simplesmente falando, os fluxos de comunicação no Fabric usam as operações de assinatura/verificação em cada ponto de contato. Portanto, qualquer aplicativo que envie chamadas, como consultas ou atualizações do livro-razão, para a rede, precisa assinar cargas úteis com sua chave privada e anexar um certificado x509 devidamente assinado para propósitos de verificação. **Inscrição** é o processo de geração de chaves e certificados necessários da Autoridade de certificação apropriada. Após a inscrição, seu aplicativo está pronto para se comunicar com a rede.

Esta seção explica como recuperar as chaves e os certificados usando o Fabric SDK como parte do tutorial **Gravando seu primeiro aplicativo**. Também é possível saber como usar o Fabric CA Client por meio da linha de comandos para inscrever seu aplicativo.

<!-- this last sentance when the certificates article goes live --->

### Inscrição utilizando o Fabric SDK
{: #enroll-app-sdk}

No diretório `fabcar` na pasta `fabric-samples`, abra o arquivo `enrollAdmin.js` em um editor de texto.

1. Em primeiro lugar, o arquivo cria uma instância do Fabric Client.
  ```
  var fabric_client = new Fabric_Client();
  ```

2. Em seguida, o arquivo cria um armazenamento de valor da chave (KVS) para gerenciar seus certificados. O SDK usa a classe [KeyValueStore ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/module-api.KeyValueStore.html "KeyValueStore"){:new_window} para criar o armazenamento de valor da chave e a classe [CryptoSuite ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/module-api.CryptoSuite.html "CryptoSuite"){:new_window} para executar os cálculos de criptografia. É possível ver o bloco de código relevante abaixo.
  ```
  # create the key value store as defined in the fabric-client/config/default.json 'key-value-store' setting
  Fabric_Client.newDefaultKeyValueStore({ path: store_path
    }).then((state_store) => {
   // assign the store to the fabric client
   fabric_client.setStateStore(state_store);
   var crypto_suite = Fabric_Client.newCryptoSuite();
   // use the same location for the state store (where the users' certificate are kept)
   // and the crypto store (where the users' keys are kept)
   var crypto_store = Fabric_Client.newCryptoKeyStore({path: store_path});
   crypto_suite.setCryptoKeyStore(crypto_store);
   fabric_client.setCryptoSuite(crypto_suite);
   var	tlsOptions = {
     trustedRoots: [],
     verify: false
   };
  ```
  {:codeblock}

3. Após a definição do KVS, será possível usar alguns métodos da classe [Fabric Client ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Client.html "Fabric Client") e a API do Fabric-CA-Client <!---[FabricCAServices ![External link icon](images/external_link.svg "External link icon")](https://fabric-sdk-node.github.io/FabricCAServices.html "FabricCAServices")---> para se comunicar com o Servidor da CA. Será necessário fornecer ao SDK o nome e a URL de sua Autoridade de certificação. Abra o arquivo JSON do **Perfil de conexão** na tela **Visão geral** no Monitor de rede e localize as seguintes variáveis na seção `certificateAuthorites`:
  * URL para CA: `url` sob `certificateAuthorities`
  * ID do usuário administrador: ``enrollId``
  * Senha do administrador: ``enrollSecret``
  * Nome da CA: `caName`

  **Edite** as linhas relevantes no arquivo `enrollAdmin.js` com estas informações da seguinte maneira:
  ```
  fabric_ca_client = new Fabric_CA_Client('https://<enrollID>:<enrollSecret>@<ca_url_with_port>', null ,<caName>, crypto_suite);
  ```
  {:codeblock}

  Por exemplo:
  ```
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null ,'org1CA', crypto_suite);
  ```
  {:codeblock}

  O Fabric Client, em seguida, verifica se o seu aplicativo já está inscrito. **Edite** a linha abaixo com o `enrollID` do perfil de conexões:
  ```
  return fabric_client.getUserContext('<enrollID>', true);
  ```
  {:codeblock}

4. É necessário enviar a chamada "inscrever" para o Servidor da CA. Seu `admin` já está registrado com sua rede. A chamada de inscrição recupera uma chave privada e uma chave pública agrupada em um certificado x509 e assinada pela CA de destino. Esse certificado agrupado e assinado é chamado de signCert. O signCert permite que os membros da rede verifiquem chamadas que se originam no cliente. É necessário fornecer seu nome de organização, sua senha e o ID do MSP por meio do arquivo de credenciais. Na seção `certificateAuthorities` de suas credenciais de rede, localize seu `enrollID`, seu `enrollSecret` e seu `x-mspid`. **Edite** o bloco de código abaixo com esses valores e substitua a seção relevante de seu arquivo.
  ```
  return fabric_ca_client.enroll({
    enrollmentID: '<enrollID>',
    enrollmentSecret: '<enrollSecret>'
      }).then((enrollment) => {
    console.log('Successfully enrolled admin user');
    return fabric_client.createUser(
         username: 'admin',
            mspid: '<x-mspid>',
            cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
        });
  ```
  {:codeblock}

5. Salve o arquivo  ` enrollAdmin.js ` .

No diretório `fabcar`, inscreva o administrador emitindo o comando a seguir:
```
enrollAdmin.js do nó
```
{:codeblock}

O comando de inscrição gera o signCert e exporta-o para uma pasta denominada `hfc-key-store`. Os arquivos futuros neste tutorial procurarão seus certificados nesta pasta. Caso possa localizar os certificados de administrador na pasta `hfc-key-store`, o comando enroll funciona.

Se quiser [operar sua rede usando o SDK](#operate-sdk), será necessário fazer upload de seu signCert de administrador para o {{site.data.keyword.blockchainfull}} Platform. É possível localizar seu signCert de administrador na pasta `hfc-key-store`. Abra o arquivo `admin` e copie o certificado dentro das aspas após o campo `certificate`. Use uma ferramenta ou um editor de texto para converter o certificado no formato PEM. É possível, então, fazer upload do certificado administrativo para sua rede de blockchain do Monitor de Rede. Para obter mais informações sobre como incluir certificados, veja [a guia "Certificados" da tela "Membro"](v10_dashboard.html#members) no Monitor de rede. Isso não será necessário se você estiver usando apenas o SDK para chamar ou consultar o chaincode.

<!-- you can eliminate this section when the certificates article goes live --->

### Inscrição utilizando o Fabric CA Client
{: #enroll-app-caclient}

1. Faça download dos [binários fabric-ca](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) para sua máquina local e extraia-os. Mova o cliente executável `fabric-ca` do diretório no qual ele foi transferido por download para um diretório no qual é possível usá-lo para executar comandos.

2. Faça download dos certificados TLS do {{site.data.keyword.cloud_notm}}, dependendo do plano de serviço, local e cluster que você usa.
  - Certificado de raiz do TLS para o Starter Plan  
    - EUA: [us01.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - Reino Unido: [uk01.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sydney: [aus01.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
  - [Certificado de Raiz do TLS para o Enterprise Plan ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Salve o conteúdo em uma pasta, por exemplo, ``$HOME/tls``. Esta etapa permite que o fluxo de dados seja criptografado na conexão.

3. Abra o arquivo JSON **Perfil de conexão** por meio de sua tela **Visão geral** no Monitor de rede e localize as variáveis a seguir:
  * URL para CA: `url` sob `certificateAuthorities`
  * ID do usuário administrador: ``enrollId``
  * Senha do administrador: ``enrollSecret``
  * Nome da CA: ``caName``

4. É possível usar o cliente da autoridade de certificação do Fabric para enviar uma chamada `enroll` para a autoridade de certificação transmitindo o caminho de certificados TLS e as quatro sequências acima com o comando a seguir:
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Você precisará executar esse comando no diretório para o qual moverá o cliente da autoridade de certificação do Fabric. Uma chamada real pode ser semelhante ao comando de exemplo a seguir:
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

5. Localize seu certificado de administrador em `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`. É possível, então, fazer upload do certificado administrativo para sua rede de blockchain do Monitor de Rede. Para obter mais informações sobre como incluir certificados, veja [a guia "Certificados" da tela "Membro"](v10_dashboard.html#members) no Monitor de rede.

  Também é possível localizar o certificado raiz da CA e a chave privada do administrador nos diretórios a seguir:
  * Certificado raiz da CA: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
  * A chave privada do administrador: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`


## Registrando seu aplicativo
{: #register-app}

Depois de gerar os certificados do lado do cliente, será necessário registrar seu aplicativo com a Autoridade de certificação da rede. O registro inclui seu aplicativo na lista de componentes que a rede pode reconhecer. Uma boa prática é registrar o aplicativo como uma identidade separada, em vez de usar o `admin` para assinar solicitações.

### Registrando usando o SDK
{: #register-app-sdk}

É possível usar o arquivo `registerUser.js` para registrar e inscrever o aplicativo como `user1` usando seu signCert `admin`. Abra  ` registerUser.js `  em um editor de texto.

1. Forneça a URL da CA e o nome para uma nova instância do Fabric CA Client.

2. Forneça o enrollID para o método `getUserContext` por meio do [Fabric Client ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Client.html) para verificar se o `admin` está inscrito e tem permissão para emitir essa solicitação. **Edite** o bloco de códigos relevante em seu arquivo com base na amostra abaixo:
  ```
  // be sure to change the http to https when the CA is running TLS enabled
  fabric_ca_client = new Fabric_CA_Client('https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011', null , '<caName>', crypto_suite);

  // first check to see if the admin is already enrolled
  return fabric_client.getUserContext('admin', true);
  }).then ((user_from_store) = > {
  if (user_from_store & & user_from_store.isEnrollroll()) {
      console.log('Successfully loaded admin from persistence');
      admin_user = user_from_store;
  } else {
      throw new Error (' Falha ao obter admin .... executar enrollAdmin.js '); }
  ```
  {:codeblock}

3. Use o **Fabric CA Client** para registrar e inscrever o usuário com a CA e, em seguida, use o **Fabric Client** para criar o novo signCert. **Edite** o bloco a seguir com seu ID do MSP e sua afiliação da organização. É possível localizar suas `x-affiliations` na seção de autoridades de certificação de suas credenciais de rede e usar qualquer afiliação listada. Inclua o nome do usuário que você deseja criar. A amostra fabcar usa `user1` por padrão.
  ```
  return fabric_ca_client.register({enrollmentID: 'user1', affiliation: '<x-affiliations>',role: 'client'}, admin_user)
    ...
  return fabric_ca_client.enroll({enrollmentID: 'user1', enrollmentSecret: secret});
  }).then ((cadastramento) = > {
  console.log('Successfully enrolled member user "user1" ');
  return fabric_client.createUser(
   {username: 'user1',
   mspid: '<x-mspid>',
   cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
   });
  ```
  {:codeblock}

4. Salve o arquivo  ` registerUser.js ` .

Execute o comando `node registerUser.js` para registrar e inscrever o `user1`. Caso possa localizar os certificados de `user1` na pasta `hfc-key-store`, o comando funciona. É possível registrar somente uma identidade a cada vez. Se você tiver problemas, tente executar `registerUser.js` com um novo nome de usuário.

<!-- you can eliminate this section when the certificates article goes live --->

### Registrando Utilizando o Fabric CA Client
{: #register-app-caclient}

1. Emita o comando a seguir para localizar sua afiliação e o nome da sua organização em sua rede de blockchain.
  ```
  ./fabric-ca-lista de afiliação do cliente -- tls.certfiles pathToPem
  ```
  {:codeblock}

  Você verá informações semelhantes ao exemplo a seguir:
  ```
  affiliation: ibp affiliation: ibp.PeerOrg1
  ```
  {:codeblock}

  Anote o segundo valor de **afiliação**, por exemplo, `ibp.PeerOrg1`. Você precisará usar esse valor no comando abaixo.

2. Execute o comando a seguir para registrar o peer.
  ```
  ./fabric-ca-client register --id.name <name> --id.affiliation <affiliation> --id.secret <password> --tls.certfiles pathToPem
  ```
  {:codeblock}

  Especifique um nome e uma senha para seu peer e substitua `name` e
`password` pelo nome e senha do peer. Tome nota destas informações. Você precisará disso ao configurar seu peer. Por exemplo:
  ```
  ./fabric-ca-client register --id.name user1 --id.affiliation ibp.PeerOrg1 --id.secret userpw --tls.certfiles --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

  É possível registrar somente uma identidade a cada vez. Se você tiver algum problema, tente um comando com um novo nome do usuário e senha.

  Quando o comando for concluído com êxito, você verá informações semelhantes ao exemplo a seguir:
  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-remote/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```
  {:codeblock}

### Registrando-se utilizando o Monitor de Rede

Como alternativa, é possível registrar e inscrever seu aplicativo cliente usando a guia **Autoridade de certificação** do Monitor de rede. Consulte estas [informações](v10_dashboard.html#ca) para obter mais instruções.

## Emitindo transações chamando e consultando o chaincode
{: #invoke-query}

Seu aplicativo precisa interagir com a rede de blockchain completa para enviar uma transação.

1. O aplicativo envia uma proposta de transação para ser endossada por peers no canal.
2. Os peers endossadores retornam a transação endossada para o aplicativo.
3. O aplicativo envia a transação endossada para o serviço de solicitação incluir a transação no livro-razão.

Para obter mais informações sobre o fluxo de transação completo, consulte [Fluxo de transação ![Ícone de link externo](images/external_link.svg "Ícone de link externo")]( https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html "Fluxo de transação"){:new_window} na documentação do Hyperledger Fabric. Depois de iniciar com este tutorial, visite a seção de [conectividade e disponibilidade do aplicativo](#app-connectivity-availability) para obter dicas sobre como gerenciar a interação do SDK com a rede.

As amostras a seguir demonstram como o Node SDK configura a topologia de rede, define a proposta de transação e, em seguida, envia a transação para a rede. É possível usar o arquivo `invoke.js` para chamar funções dentro do chaincode `fabcar`. Essas funções permitem criar e transferir ativos no livro-razão do blockchain. Este tutorial usa a função `initLedger` para incluir novos dados em seu canal e, em seguida, usa o arquivo `query.js` para consultar os dados.

### Chamando o Chaincode
{: #invoke}

Abra o arquivo `invoke.js` em um editor de texto.

1. Inclua `var creds = require('./creds.json')` na parte superior do arquivo. Essa linha de código permite que o arquivo `invoke.js` leia informações do arquivo de credenciais `creds.json`.

2. Use a classe [Fabric Client ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Client.html "Fabric Client"){:new_window} para configurar a rede de malha usando os terminais de API de seus recursos de rede. Essa etapa define o canal e os peers para os quais seu cliente enviará a proposta, além do serviço de solicitação para o qual o SDK enviará, então, a transação endossada. ** Edite **  o bloco de código relevante abaixo. A linha `creds.peers["org1-peer1"].url` importa a URL do peer de seu arquivo de credenciais.
  ```
  # setup the fabric network
  var channel = fabric_client.newChannel('defaultchannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  var order = fabric_client.newOrderer(creds.orderers.orderer.url, { pem: creds.orderers.orderer.tlsCACerts.pem , 'ssl-target-name-override': null})
  channel.addOrderer(order);
  ```
  {:codeblock}

  Ao incluir a URL do peer no método `fabric_client.newPeer`, você também importará os certificados TLS relevantes de seu perfil de conexão usando o fragmento de código abaixo. Você fez o mesmo quando incluiu a URL do serviço de solicitação. É necessário usar esses certificados TLS para autenticar a comunicação com a rede.
  ```
  {1}-peer1" ] .tlsCACerts.pem, 'ssl-target-name-override ': null }
  ```
  {:codeblock}

  Se a política de endosso exigir que as transações sejam endossadas por outras organizações no canal, você precisará incluir essas organizações peers usando os métodos `newPeer()` e `channel.addPeer()` quando configurar a rede. O envio das organizações precisa enviar a você a lista dos peers que foram associados a um canal específico. As informações sobre terminais e os certificados TLS estarão disponíveis no perfil de conexão. O SDK enviará a transação para todos os peers incluídos no canal.

  Também é possível incluir peers adicionais pertencentes à sua organização que foram associados ao canal como uma etapa para [tornar seu aplicativo altamente disponível](#ha-app). Isso fornecerá ao SDK um failover no caso de um de seus peers ser interrompido.

3. Depois de configurar a rede de malha e importar a identidade do aplicativo e o signCert da etapa de registro, o arquivo `invoke.js` definirá a proposta que você enviará para a rede. É possível usar a função `initLedger` no chaincode `fabcar` para incluir alguns dados iniciais no livro-razão. Também é possível editar o bloco de códigos para chamar outras funções que podem ser localizadas no chaincode `fabcar`.
  ```
  solicitação var = {
    //targets: let default to the peer assigned to the client
    chaincodeId: 'fabcar',
    fcn: 'initLedger',
    args: [''],
    chainId: 'mychannel',
    txId: tx_id
  };
  ```
  {:codeblock}

  Depois de definir a solicitação, será possível enviar uma [proposta de transação ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Channel.html#sendTransactionProposal "sendTransactionProposal") para os peers no canal. Depois que a proposta for retornada dos peers, será possível [enviar a transação ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction") para o serviço de solicitação.

4. É possível incluir um serviço de evento para tornar o fluxo de transação mais eficiente. **Edite** a seção a seguir:
  ```
  let event_hub = fabric_client.newEventHub();
  event_hub.setPeerAddr(creds.peers["org1-peer1"].eventUrl, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  ```
  {:codeblock}

  Embora a amostra use um serviço de evento baseado no peer, é necessário usar um listener baseado em canal. É possível saber mais na seção de [gerenciamento de transações](#managing-transactions) e na [documentação do Node SDK ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "serviço de evento baseado em canal"){:new_window}.

5. Por padrão, `invoke.js` envia a transação como `user1`. Será possível editar o arquivo `invoke.js` se você tiver registrado um nome diferente.

Depois de concluir a edição do arquivo, emita o comando `node invoke.js` para enviar a transação para a rede. A seguinte confirmação de que a chamada foi bem-sucedida deve ser exibida.
```
Successfully sent Proposal and received ProposalResponse: Status - 200, message - "OK"
```

Isso indica que seu aplicativo chamou com sucesso o chaincode e incluiu dados no livro-razão.

### Consultando o chaincode
{: #query}

Agora é possível usar `query.js` para ler o livro-razão. Abra o arquivo `query.js` em um editor de texto.

1. Inclua `var creds = require('./creds.json')` na parte superior do arquivo.
2. Atualize o arquivo com o nome do canal e as informações sobre terminais do peer. Como essa operação lê apenas os dados que estão armazenados no peer, não é necessário incluir as informações sobre terminais do serviço de solicitação. `query.js` também assume que você envia a proposta como `user1`.
  ```
  var channel = fabric_client.newChannel('mychannel');
  var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
  channel.addPeer(peer);
  ```
  {:codeblock}

Depois de concluir a edição do arquivo, emita o comando `node query.js` e você deverá ver a lista de carros no livro-razão, que é semelhante ao exemplo a seguir.
```
Query has completed, checking results
Response is  
  [{"Key":"CAR0", "Record":{"colour":"blue","make":"Toyota","model":"Prius","owner":"Tomoko"}},
  {"Key":"CAR1", "Record":{"colour":"red","make":"Ford","model":"Mustang","owner":"Brad"}},
  ...
```
{:codeblock}

Para obter mais informações sobre o aplicativo fabcar e as funções usadas, é possível visitar o tutorial completo de [Gravando seu primeiro aplicativo ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/write_first_app.html "gravando seu primeiro aplicativo"){:new_window} na documentação do Hyperledger Fabric.

## Usando seu Perfil de conexão com o SDK
{: #using-your-connection-profile-with-the-sdk}

Em vez de importar as informações sobre terminais de sua rede manualmente, é possível fazer com que o SDK se conecte à sua rede usando o **Perfil de conexão** por meio da tela **Visão geral** de seu Monitor de rede. Isso simplifica o processo de conexão com a Autoridade de certificação para inscrição e registro. Também elimina a necessidade de definir sua rede de malha antes de enviar uma transação. O SDK localizará os peers e os solicitadores no canal relevante diretamente do Perfil de conexão. É possível localizar mais informações sobre como usar um Perfil de conexão na [documentação do Node SDK ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/tutorial-network-config.html "tutorial do perfil de conexão"){:new_window}.

Vamos usar o arquivo `invoke.js` como um exemplo para ver a eficiência de uso de um Perfil de conexão, em vez de terminais manuais. É possível estabelecer uma nova instância do cliente de malha usando a classe`loadFromConfig`. Substitua `var fabric_client = new Fabric_Client();` pelo código a seguir.
```
var fabric_client = Fabric_Client.loadFromConfig (path.join (__dirname, './connection-profile.json'));
```
{:codeblock}

Em vez de configurar a rede de malha criando peers e solicitadores e, em seguida, incluindo-os em um canal, é possível usar a linha a seguir para criar um novo canal.

```
var channel = fabric_client.newChannel ('defaultchannel');
```
{:codeblock}

Em seguida, o SDK inclui os peers e o serviço de solicitação definidos no canal usando o Perfil de conexão. Isso torna a composição de aplicativos mais eficiente e facilita a atualização de seus aplicativos à medida que os membros da rede se associam, saem e iniciam novos canais. Consulte o [tutorial do perfil de conexão ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/tutorial-network-config.html "tutorial do perfil de conexão"){:new_window} na documentação do Node SDK para saber mais sobre as etapas adicionais envolvidas. É possível usar esta [versão do tutorial do fabcar ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/developerworks/cloud/library/cl-deploy-fabcar-sample-application-ibm-blockchain-starter-plan/index.html){:new_window} que usa um perfil de conexão, em vez de conexões de terminal manuais.

É possível enviar transações para peers fora de sua organização para endosso, editando seu Perfil de conexão. O Perfil de conexão já contém as informações sobre terminais e os certificados TLS de peers de outras organizações em sua rede do {{site.data.keyword.blockchainfull}} Platform. Inclua o nome do peer no canal relevante na seção `channels` do perfil para incluir o peer no canal.

## Conectividade e disponibilidade do aplicativo
{: #app-connectivity-availability}

O [Fluxo de transação ![Ícone de link externo](images/external_link.svg "Ícone de link externo")]( https://hyperledger-fabric.readthedocs.io/en/release-1.1/txflow.html "Fluxo de transação"){:new_window} do Hyperledger Fabric abrange vários componentes, com os aplicativos clientes desempenhando uma função exclusiva. O SDK envia propostas de transação para os peers para endosso. Em seguida, ele coleta as propostas endossadas a serem enviadas para o serviço de solicitação, que então envia blocos de transações para os peers a serem incluídos nos livros-razão do canal. Os desenvolvedores de aplicativos de produção devem estar preparados para gerenciar suas interações entre o SDK e suas redes para eficiência e disponibilidade.

### Gerenciando Transações
{: #managing-transactions}

Os aplicativos clientes devem assegurar que suas propostas de transação sejam validadas e que as propostas sejam concluídas com sucesso. Uma proposta pode ser atrasada ou perdida por vários motivos, como uma indisponibilidade de rede ou uma falha de componente. É necessário preparar seu aplicativo para [alta disponibilidade](#ha-app) para manipular a falha do componente. Também é possível [aumentar os valores de tempo limite](#set-timeout-in-sdk) em seu aplicativo para evitar que as propostas excedam o tempo limite antes que a rede possa responder.

Se um chaincode não estiver em execução, a primeira proposta de transação enviada para esse chaincode iniciará o chaincode. Enquanto o chaincode está iniciando, todas as outras propostas são rejeitadas com um erro indicando que o chaincode está iniciando atualmente. Isso é diferente da invalidação da transação. Se alguma proposta for rejeitada enquanto o chaincode estiver iniciando, os aplicativos clientes precisarão enviar as propostas rejeitadas novamente após o início do chaincode. Os aplicativos clientes podem usar uma fila de mensagens para evitar a perda de propostas de transação.

É possível usar um serviço de evento baseado em canal para monitorar transações e construir filas de mensagens. Os listeners baseados em canal deverão ser usados em vez de serviços baseados em peer por causa de sua capacidade de escalar para diversos canais e distinguir entre tráfego em diferentes canais. A classe [channelEventHub ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/ChannelEventHub.html "channelEventHub"){:new_window} pode registrar listeners com base em eventos de transação, de bloco e de chaincode. É possível aprender como configurar um [serviço de evento baseado em canal ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/tutorial-channel-events.html "serviço de evento baseado em canal"){:new_window} usando a documentação do Node SDK.

### Fechando conexões de rede

Ao incluir peers e solicitadores no SDK antes de enviar uma proposta de transação, você está abrindo uma conexão de fluxo grpc para o componente de rede. Por exemplo, o comando
```
var peer = fabric_client.newPeer(creds.peers["org1-peer1"].url, { pem: creds.peers["org1-peer1"].tlsCACerts.pem , 'ssl-target-name-override': null});
```
O abrirá uma conexão com o  ` org1-peer1 `. Se você tiver um aplicativo em execução contínua, será necessário fechar conexões quando elas não forem necessárias usando os comandos `peer.close()` e `orderer.close()` para liberar recursos e evitar degradação de desempenho. É possível localizar mais detalhes nas classes de [peer](https://fabric-sdk-node.github.io/Peer.html#close__anchor) e [solicitador](https://fabric-sdk-node.github.io/Orderer.html#close__anchor) da documentação do Node SDK.

### Aplicativos Altamente Disponíveis
{: #ha-app}

Como uma boa prática de alta disponibilidade, é altamente recomendável implementar um mínimo de dois peers por organização para failover. Também será necessário adaptar seus aplicativos para alta disponibilidade. Instale o chaincode em ambos os peers e inclua-os em seus canais. Em seguida, prepare-se para [enviar propostas de transação](#invoke) para ambos os terminais de peer ao configurar sua rede e construir sua lista de metas do peer. Se você tiver uma rede do Enterprise Plan, também será possível incluir outro solicitador na rede também. Se você estiver usando o [Perfil de conexão](#using-your-connection-profile-with-the-sdk) em vez de incluir terminais de rede manualmente, assegure-se de que seu perfil esteja atualizado e que os peers e solicitadores adicionais tenham sido incluídos no canal relevante na seção `channels` do perfil. Em seguida, o SDK incluirá os componentes associados ao canal usando o Perfil de conexão.

## Ativando o TLS mútuo
{: #mutual-tls}

Se estiver executando redes do Enterprise Plan que está no nível do Fabric V1.1, você terá a opção de [ativar o TLS mútuo](v10_dashboard.html#network-preferences) para seus aplicativos. Se você ativar o TLS mútuo, será necessário atualizar os seus aplicativos para suportarem essa função. Caso contrário, seus aplicativos não poderão se comunicar com a rede.

No Perfil de conexão, localize a seção `certificateAuthorities`, na qual é possível localizar os atributos a seguir que são necessários para inscrever e obter os certificados para se comunicar com sua rede usando o TLS mútuo.

- `url`: URL para conexão com a autoridade de certificação que pode fornecer certificados do TLS mútuo
- `enrollId`: ID de inscrição para usar para obter um certificado
- `enrollSecret`: segredo de inscrição para usar para obter um certificado
- `x-tlsCAName`: o nome da CA a ser usado para obter o certificado que permite que o aplicativo se comunique com o TLS mútuo.

Para obter mais informações sobre como atualizar seus aplicativos para suportar o TLS mútuo, consulte [Como configurar o TLS mútuo ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/tutorial-mutual-tls.html "tls mútuo"){:new_window}.

## (Opcional) Operando sua rede usando o SDK
{: #operate-sdk}

Também é possível usar o SDK para operar sua rede de blockchain. Este tutorial explica como é possível usar o SDK para associar os peers a canais, instalar o chaincode nos peers e instanciar o chaincode em canais. Essas etapas serão opcionais porque também será possível executar essas operações usando o Monitor de rede ou as APIs na [IU do Swagger](howto/swagger_apis.html) se todos os peers estiverem sendo executados no {{site.data.keyword.blockchainfull}} Platform.

É necessário fazer upload do signCert do administrador para o {{site.data.keyword.blockchainfull}} Platform para concluir essas etapas. É possível localizar instruções sobre como fazer upload do signCert no final da [seção de inscrição](#enroll-app-sdk)

### Unindo um canal
{: #join-channel-sdk}

Depois de criar um canal usando o Monitor de rede ou as APIs, será possível usar o SDK para associar o peer ao canal.

1. [Busque o bloco genesis ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Channel.html#getGenesisBlock "busque o bloco genesis"){:new_window} do canal por meio do serviço de solicitação.
2. Passe o bloco genesis para o método [associar canal ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Channel.html#joinChannel "joinChannel"){:new_window} para associar o peer ao canal.

Para usar a amostra `fabcar` para associar um canal, use o arquivo `invoke.js` como seu ponto de início. Você precisará enviar essa solicitação como o administrador em vez do aplicativo, portanto, substitua `user1` por `admin` no método `getUserContext`. Iniciando onde você define sua solicitação de chamada de chaincode em `var request = {`, substitua o fluxo de transação por uma solicitação de junção de canal com base no fragmento de código abaixo da [documentação do Node SDK ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/tutorial-channel-create.html "tls mútuo"){:new_window}.
  ```
  let g_request = {
    txId :     tx_id
  };

  // get the genesis block from the orderer
  channel.getGenesisBlock(g_request).then((block) =>{
    genesis_block = block;
    tx_id = client.newTransactionID();
    let j_request = {
      targets : targets,
      block : genesis_block,
      txId :     tx_id
    };
    // send genesis block to the peer
    return channel.joinChannel(j_request);
  }).then((results) =>{
    if (resultados & & results [ 0 ] .response & & results [ 0 ] .response.status == 200) {
    // good  
    } else {
      // not good
    }
  });
  ```
### Instalando um chaincode
{: #install-cc-sdk}

É possível usar o método de [instalação do chaincode ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Client.html#installChaincode "installChaincode"){:new_window} por meio da classe [Fabric Client ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Client.html "Fabric Client"){:new_window} para instalar o chaincode no peer.

Para usar a amostra `fabcar` para instalar o chaincode `fabcar` no peer, use o arquivo `query.js` como uma linha de base e edite-o. Você precisará enviar essa solicitação como o administrador em vez do aplicativo, portanto, substitua `user1` por `admin` no método `getUserContext`. Substitua o objeto de proposta da transação por uma solicitação de chaincode de instalação usando o exemplo a seguir:
```
solicitação var = {
    targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
    channelNames: 'mychannel'
	 };
```
{:codeblock}

Envie esse objeto para `return fabric_client.installChaincode(request);`, em vez da linha `return channel.queryByChaincode(request);` atualmente no arquivo.

### Instanciando um Chaincode
{: #instantiate-cc-sdk}

Para instanciar o chaincode, é necessário enviar uma [proposta de instanciação ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal"){:new_window} para o peer e, em seguida, enviar uma [solicitação de transação ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Channel.html#sendTransaction "sendTransaction){:new_window} para o serviço de solicitação.

Para usar a amostra `fabcar` para instanciar seu chaincode, use o arquivo `invoke.js` como seu ponto de início. Você precisará enviar essa solicitação como o administrador em vez do aplicativo, portanto, substitua `user1` por `admin` no método `getUserContext`. Substitua o objeto de proposta da transação por uma solicitação de chaincode de instalação usando o exemplo a seguir:
```
solicitação var = {
		targets: peer,
    chaincodePath: chaincode_path,
    chaincodeId: 'fabcar',
    chaincodeType: 'golang',
    chaincodeVersion: 'v1',
		channelNames: 'mychannel'
		};

```
{:codeblock}

Envie esta solicitação para `return channel.sendInstantiateProposal(request);` em vez de `return channel.sendTransactionProposal(request);` atualmente no arquivo. Depois de enviar a solicitação de instanciação para o canal, será necessário enviar a proposta endossada como uma transação para o serviço de solicitação. São usados os mesmos métodos que o envio de uma transação, portanto, é possível manter o restante do arquivo sem mudanças. Talvez você queira [aumentar o valor de tempo limite](#set-timeout-in-sdk) na proposta de instanciação. Caso contrário, a solicitação poderá atingir o tempo limite antes que a plataforma possa iniciar o contêiner de chaincode.

## (Opcional) Configurando valores de tempo limite nos SDKs do Fabric
{: #set-timeout-in-sdk}

SDKs do Fabric configuram valores de tempo limite padrão em aplicativos clientes para eventos na rede de blockchain. Veja o exemplo a seguir sobre as configurações de tempo limite padrão no SDK do Fabric Java. O caminho de arquivo é `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
    /**
     * Timeout settings
     **/
    public static final String PROPOSAL_WAIT_TIME = "org.hyperledger.fabric.sdk.proposal.wait.time";
    public static final String CHANNEL_CONFIG_WAIT_TIME = "org.hyperledger.fabric.sdk.channelconfig.wait_time";
    public static final String TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME = "org.hyperledger.fabric.sdk.client.transaction_cleanup_up_timeout_wait_time";
    public static final String ORDERER_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer_retry.wait_time";
    public static final String ORDERER_WAIT_TIME = "org.hyperledger.fabric.sdk.orderer.ordererWaitTimeMilliSecs";
    public static final String PEER_EVENT_REGISTRATION_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.eventRegistration.wait_time";
    public static final String PEER_EVENT_RETRY_WAIT_TIME = "org.hyperledger.fabric.sdk.peer.retry_wait_time";
    public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
    public static final String EVENTHUB_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.eventhub.reconnection_warning_rate";
    public static final String PEER_EVENT_RECONNECTION_WARNING_RATE = "org.hyperledger.fabric.sdk.peer.reconnection_warning_rate";
    public static final String GENESISBLOCK_WAIT_TIME = "org.hyperledger.fabric.sdk.channel.genesisblock_wait_time";

    ...

    // Default values
    /**
     * Timeout settings
     **/
    defaultProperty(PROPOSAL_WAIT_TIME, "20000");
    defaultProperty(CHANNEL_CONFIG_WAIT_TIME, "15000");
    defaultProperty(ORDERER_RETRY_WAIT_TIME, "200");
    defaultProperty(ORDERER_WAIT_TIME, "10000");
    defaultProperty(PEER_EVENT_REGISTRATION_WAIT_TIME, "5000");
    defaultProperty(PEER_EVENT_RETRY_WAIT_TIME, "500");
    defaultProperty(EVENTHUB_CONNECTION_WAIT_TIME, "5000");
    defaultProperty(GENESISBLOCK_WAIT_TIME, "5000");
    /**
     * This will NOT complete any transaction futures time out and must be kept WELL above any expected future timeout
     * for transactions sent to the Orderer. Para a limpeza interna apenas.
     */
    defaultProperty(TRANSACTION_CLEANUP_UP_TIMEOUT_WAIT_TIME, "600000"); //10 min.
```
{:codeblock}

No entanto, você pode precisar mudar os valores de tempo limite padrão em seu próprio aplicativo. Por exemplo, quando seu aplicativo chama uma transação que precisa de mais de 5000 ms, que é o valor de tempo limite padrão para a conexão do hub de eventos, para resposta, é possível obter um erro com falha porque o evento de chamada termina em 5000 ms antes da conclusão da transação. É possível configurar a propriedade de sistema para sobrescrever os valores padrão de seu aplicativo cliente. Como os valores padrão são inicializados antes de você configurar a propriedade de sistema, a propriedade de sistema pode não entrar em vigor. Portanto, é necessário configurar a propriedade de sistema para tempo limite em uma construção estática em seu aplicativo cliente. Veja o exemplo a seguir sobre a mudança do valor de tempo limite para a conexão de hub de evento para 15000 milissegundos no SDK do Fabric Java. O caminho de arquivo é `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty (EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE); }
```
{:codeblock}

Se você estiver usando o Node SDK, será possível especificar os valores de tempo limite diretamente no método chamado. Como um exemplo, você usaria a linha abaixo para aumentar o valor de tempo limite para [instanciar um chaincode ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal "sendInstantiateProposal") para 5 minutos.
```
channel.sendInstantiateProposal (solicitação, 300000);
```
{:codeblock}

## (Opcional) Utilizando índices de CouchDB
{: #couchdb-indices}

Se a rede usar CouchDB e você quiser aproveitar o recurso de indexação CouchDB para aprimorar o desempenho do CouchDB, será necessário empacotar os índices com o chaincode.

Para obter mais informações sobre o CouchDB e como configurar os índices, consulte [CouchDB como o banco de dados de estado ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB como o banco de dados de estado"){:new_window}. na documentação do Hyperledger Fabric. Também é possível localizar um exemplo que usa um índice com chaincode neste [Tutorial do Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html){:new_window}.

<!--# Next steps-->

## Hospedando aplicativos
{: #host-app}

É possível hospedar seu aplicativo em seu sistema de arquivos local ou enviá-lo por push para o {{site.data.keyword.Bluemix_notm}}. Para enviar seu aplicativo por push para o {{site.data.keyword.Bluemix_notm}}, conclua as etapas a seguir:
1. Instale o [Cloud Foundry Command Line Installer ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://github.com/cloudfoundry/cli/releases).  Teste sua instalação com o comando `cf`.
    * Se sua instalação for bem-sucedida, você deverá ver um monte de saída de texto em seu terminal.
    * Se você vir "comando não localizado", sua instalação não foi bem-sucedida ou o CF não foi incluído em seu caminho de sistema.
2. Configure o terminal de API e efetue login com seu ID e senha do {{site.data.keyword.Bluemix_notm}} emitindo os comandos a seguir:
    ```
    > cf api https://api.ng.bluemix.net
    > cf login
    ```
    {:codeblock}
3. Procure pelo diretório de seu aplicativo e envie seu aplicativo por push emitindo o comando a seguir. Isso pode levar vários minutos, dependendo do tamanho de seu aplicativo. É possível ver logs por meio do {{site.data.keyword.Bluemix_notm}} em seu terminal. Os logs cessarão quando o aplicativo for ativado com êxito.
	```
	> cf push YOUR_APP_NAME_HERE
	```
	{:codeblock}
	É possível verificar seus logs do aplicativo emitindo um dos comandos a seguir:
	* `> cf logs YOUR_APP_NAME_HERE`
	* `> cf logs YOUR_APP_NAME_HERE --recent`

## Desconectando seu aplicativo da rede
{: #disconnect-app}

Conclua as etapas a seguir para remover a conexão entre seu aplicativo e a rede de blockchain no {{site.data.keyword.cloud_notm}}.
1. Remova as informações do terminal da API de seu arquivo de configuração do aplicativo. Para referência, consulte [Incluindo terminais de API de rede em seu aplicativo](#api-endpoints).
2. Exclua seu contêiner de chaincode.
  1. Na tela "Canal" do Monitor de Rede, localize o canal no qual o chaincode está instalado.
  2. Na tela do canal específico, localize o chaincode que deseja desativar.
  3. Clique no botão **Excluir** e clique em **Enviar** no painel de exclusão do chaincode. O seu contêiner do chaincode será removido.
	![Excluir um chaincode](images/channel_chaincode.png "Excluir um chaincode")
