---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Desenvolvendo aplicativos
{: #dev_app}
Com aplicativos, é possível chamar o chaincode para consultar ou atualizar um livro-razão específico do canal em sua rede de blockchain.
{:shortdesc}

## Configurando o ambiente de desenvolvimento de aplicativo
Você precisa instalar os pré-requisitos de software para desenvolver aplicativos que possam interagir com a rede de blockchain no {{site.data.keyword.cloud}}.
{:shortdesc}

*	Git ([Página de download do Git![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://git-scm.com/downloads){:new_window})  
	Git é uma ferramenta de controle de versão com a qual se familiarizar, tanto para o desenvolvimento de chaincode como para o desenvolvimento de software em geral. Além disso, o Bash Git, que é instalado com Git no Windows, é uma alternativa excelente para o prompt de comandos do Windows.

*	GoLang ([Página de download do GoLang ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://golang.org/dl){:new_window})  
	A instalação do GoLang instala um conjunto de ferramentas de CLI Go, que são muito úteis para gravar o chaincode. Por exemplo, o comando `go build` permite verificar se seu chaincode é realmente compilado antes de você tentar implementá-lo em uma rede.

	Siga as [Instruções de Instalação ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://golang.org/doc/install){:new_window} para configurar corretamente as variáveis de ambiente. Verifique seu `GOPATH` usando o comando a seguir. Observe que o `GOPATH` não precisa corresponder ao exemplo; o importante é que você tenha essa variável configurada em um diretório válido em seu sistema de arquivos.

	```
	$ echo $GOPATH
C:\gopath
	```
	{:codeblock}

	É possível, então, verificar sua instalação do GoLang compilando o código GoLang com o exemplo do [hello world ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://golang.org/doc/install#testing){:new_window}.

* Java ([Página de download do Java ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://java.com/en/download/){:new_window}).
*	Node.js ([Página de download do Node.js![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://nodejs.org/en/download/){:new_window}).

O desenvolvimento do chaincode é suportado atualmente em Node.js, Go e Java.

## Gerando os certificados do lado do cliente
Não vamos nos aprofundar nas minúcias do x509 e da infraestrutura de chave pública; há muitos recursos externos para isso. Basta dizer que os fluxos de comunicação no Fabric usam operações de assinatura/verificação em cada ponto de contato. Desse modo, qualquer cliente que envie chamadas (ou seja, consultas ou atualizações contábeis) para a rede precisará assinar cargas úteis e anexar um certificado x509 assinado corretamente para propósitos de verificação. A chave privada e o certificado assinado, juntamente com um identificador do MSP e o certificado raiz da Autoridade de Certificação (CA), compõem o que é referido como o objeto "contexto do usuário".

O importante aqui é o processo de "inscrição", no qual as chaves e os certificados que permitem que o objeto seja formado são recuperados por meio da autoridade de certificação apropriada. Depois de formar o objeto de contexto do usuário, será possível chamar uma API no seu aplicativo para "configurar" ou "obter" esse contexto de usuário. Neste ponto, o aplicativo (ou seja, o cliente) é equipado com todos os artefatos necessários e está pronto para se comunicar com a rede. Veremos as duas abordagens para recuperar as chaves e os certificados: linha de comando e SDK.

### Linha de comandos
Usar a linha de comandos é a mais simples das duas abordagens.

1. Siga as instruções para construir o [cliente de autoridade de certificação do Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html). Essa etapa permite se comunicar com um Servidor de autoridade de certificação e receber certificados e chaves corretamente formatados.

2. Faça download dos certificados do TLS do {{site.data.keyword.cloud_notm}} dependendo do plano de serviço, local e cluster que você usa.
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

4. Usando o cliente Fabric CA, podemos enviar uma chamada de "inscrição" à nossa Autoridade de Certificação, passando no caminho de certificados TLS e pelas quatro sequências acima com o comando a seguir:
  ```
  $GOPATH/bin/fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  O binário ``fabric-ca-client`` é colocado em ``$GOPATH/bin``, portanto, você precisará estar no local apropriado em sua máquina local quando executar este comando.  Uma chamada real pode ser semelhante ao comando de exemplo a seguir:
  ```
  ./fabric-ca-client enroll -u https://admin:B84F2C5436@tor-zbc01a.3.secure.blockchain.ibm.com:23042 --tls.certfiles /Users/XYZ/Downloads/3.secure.blockchain.ibm.com.rootcert --caname PeerOrg1CA
  ```
  {:codeblock}

5. Localize seu certificado de administrador em `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`. É possível, então, fazer upload do certificado administrativo para sua rede de blockchain do Monitor de Rede. Para obter mais informações sobre como incluir certificados, veja [a guia "Certificados" da tela "Membro"](v10_dashboard.html#members) no Monitor de rede.

  Também é possível localizar o certificado raiz da CA e a chave privada do administrador nos diretórios a seguir:
  * Certificado raiz da CA: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
  * A chave privada do administrador: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`

### SDK
Há alguns repositórios do Fabric que contêm excelentes recursos e scripts para entender como interagir programaticamente com uma Autoridade de Certificação. O repositório ``fabric-samples`` contém o exemplo ``balance transfer`` e o repositório ``fabric-sdk-node`` tem uma série de testes de Serviços da autoridade de certificação. Se você pretende emitir as suas solicitações de inscrição no lado do aplicativo, então, precisará entender completamente as APIs que precisam ser expostas nos pacotes ``fabric-ca-client`` e ``fabric-client``. Use esses scripts e repositórios como uma linha de base para estruturar seu aplicativo.

Vamos dar uma olhada rápida em alguns trechos chaves do exemplo `balance transfer`:

Primeiro, precisamos criar o nosso objeto do cliente e configurar uma instância de armazenamento de valor da chave na qual os nossos certificados e chaves serão colocados. Podemos fazer isso com um factory method simples -- ``newCryptoSuite`` - que se estende até a classe ``Client`` do ``BaseClient``. Aqui está uma olhada rápida no código:

```
# <PUBLIC_PRIVATE_KEY_PATH> denota o caminho em sua máquina local no qual você deseja armazenar sua chave e certificado
let cryptoSuite = hfc.newCryptoSuite();
cryptoSuite.setCryptoKeyStore(hfc.newCryptoKeyStore({path: <PUBLIC_PRIVATE_KEY_PATH>)}));
client.setCryptoSuite(cryptoSuite);
```
{:codeblock}

A prática comum seria exportar uma variável de ambiente que define o caminho de chave/valor em sua máquina e transmiti-la para a função acima. Agora que já definimos nosso KVS, vamos usar alguns métodos da classe `` FabricCAServices``. Esta classe é uma implementação do cliente Fabric CA e como tal permitirá que nos comuniquemos com o Servidor de CA. Primeiramente, precisamos passar algumas informações ao nosso cliente de autoridade de certificação, a saber, a URL da Autoridade de certificação:

```
# a caURL pode ser definida manualmente ou configurando uma variável de ambiente
# a variável copService é definida por cima do programa
let caUrl = "https://XXX:7054";
var caClient = new copService(caUrl, null, '' /* default CA */, cryptoSuite);
```
{:codeblock}

Em seguida, vamos realmente enviar a chamada de "inscrição" para o Servidor de CA. Isso retornará ao cliente uma chave privada e uma chave pública que foram agrupadas em um certificado x509 e assinadas pela CA de destino -- chamamos isso de signcert ou certificado de inscrição. Esse certificado de inscrição ou "eCert" é a peça chave, porque permite que entidades de rede verifiquem transações e chamadas originadas do cliente:

```
return caClient.enroll({
enrollmentID: username,
enrollmentSecret: password
```
{:codeblock}

E a tarefa final é realmente configurar o conjunto de criptografia e construir o contexto do usuário:

```
	member.setCryptoSuite(client.getCryptoSuite());
	return member.setEnrollment(enrollment.key, enrollment.certificate, getMspID(userOrg));
}).then(() => {
	return client.setUserContext(member);
```
{:codeblock}

É possível, então, fazer upload do certificado administrativo para sua rede de blockchain do Monitor de Rede. Para obter mais informações sobre como incluir certificados, veja [a guia "Certificados" da tela "Membro"](v10_dashboard.html#members) no Monitor de rede.

## Desenvolvendo aplicativos
{: #developing-applications}

Uma maneira fácil de começar a desenvolver aplicativos é usar o chaincode de amostra e aplicativos como um modelo para criar a sua própria solução de negócios. É possível localizar aplicativos de amostra para as redes de blockchain no {{site.data.keyword.cloud_notm}} em [Aplicativos de amostra](howto/prebuilt_samples.html). Também é possível começar a desenvolver aplicativos do zero com base em suas próprias necessidades de negócios.

É possível desenvolver o seu aplicativo em JavaScript ou Java e alavancar as APIs disponíveis nos SDKs do Hyperledger Fabric Client para permitir a interação entre o seu aplicativo e a sua rede.  Um aplicativo precisa incluir pelo menos as informações a seguir:
* Nome e versão do chaincode a ser chamado.
* Informações do terminal de API de seus recursos de rede, incluindo solicitantes, CA e peers.
* Funções para consultar ou atualizar o livro-razão na rede.  Se você desejar alta disponibilidade, precisará considerar o failover de nó em seu aplicativo.

### Usando SDKs do Fabric
{: #use-sdks}

O Fabric oferece SDK do Node.js e SDK do Java atualmente e suportará mais linguagens de programação, como Python, REST ou GO, em liberações futuras. Para obter mais informações sobre os SDKs do Fabric e como usá-los, veja a documentação a seguir:

- [Documentação de SDK do Hyperledger Fabric Node ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/){:new_window}
- [Documentação de SDK do Hyperledger Fabric Java ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://github.com/hyperledger/fabric-sdk-java){:new_window}

### Configurando Valores de Tempo Limite de SDKs do Fabric
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

No entanto, você pode precisar mudar os valores de tempo limite padrão em seu próprio aplicativo. Por exemplo, quando o seu aplicativo chamar uma transação que precise de mais de 5.000 milissegundos, que é o valor de tempo limite padrão para a conexão de hub de evento, para resposta, você obterá um erro com falha porque o evento de chamada termina em 5.000 milissegundos antes de a transação ser concluída. É possível configurar a propriedade de sistema para sobrescrever os valores padrão de seu aplicativo cliente. Como os valores padrão são inicializados antes de você configurar a propriedade de sistema, a propriedade de sistema pode não entrar em vigor. Portanto, é necessário configurar a propriedade de sistema para tempo limite em uma construção estática em seu aplicativo cliente. Veja o exemplo a seguir sobre a mudança do valor de tempo limite para a conexão de hub de evento para 15000 milissegundos no SDK do Fabric Java. O caminho de arquivo é `src\main\java\org\hyperledger\fabric\sdk\helper\Config.java`.

```
 public static final String EVENTHUB_CONNECTION_WAIT_TIME = "org.hyperledger.fabric.sdk.eventhub_connection.wait_time";
 private static final long EVENTHUB_CONNECTION_WAIT_TIME_VALUE = 15000;

 static {
     System.setProperty (EVENTHUB_CONNECTION_WAIT_TIME, EVENTHUB_CONNECTION_WAIT_TIME_VALUE); }
```
{:codeblock}


## Incluindo terminais de API de rede em seu aplicativo
É necessário incluir terminais de API de seus recursos de rede em seu aplicativo para que ele possa interagir com a sua rede do {{site.data.keyword.blockchain}} no {{site.data.keyword.Bluemix_short}}.  Se você não tiver uma rede do {{site.data.keyword.blockchain}} no {{site.data.keyword.Bluemix_short}}, será possível criar uma com o Starter Plan ou o Enterprise Plan. Para obter mais informações, veja [Governar a rede do Starter Plan](get_start_starter_plan.html) e [Governar a rede do Enterprise Plan](get_start.html).

É possível localizar os terminais de API no perfil de conexão de sua rede. O perfil de conexão está no formato JSON e contêm as informações do terminal de API e enrollIDs/segredos para os seus recursos de rede, incluindo solicitante, autoridade de certificação e nós de peer. O seu aplicativo irá interagir com peers e outros recursos de rede por meio desses terminais de API.

1. Recupere as informações do terminal de API de seus recursos de rede por meio de seu Monitor de rede com um dos métodos a seguir:
	* Para obter as informações do terminal de API que são específicas para um chaincode, na tela do canal específico, na qual o chaincode está em execução, localize o chaincode e clique no botão **JSON**.
	![Terminais de API por chaincode](images/channel_chaincode.png "Terminais de API por chaincode")
	* Para obter um conjunto completo de informações do terminal de API sobre todos os recursos de rede, clique no botão **Perfil de conexão** na tela "Visão geral".
	![Perfil de conexão no Monitor de rede](images/service_credentials.png "Perfil de conexão no Monitor de rede")

2. Localize as informações do terminal de API de seus recursos de rede, que são semelhantes ao exemplo a seguir:
	```
	"peers": {
            "fabric-peer-org3-18400c": {
                "url": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:18400",
                "eventUrl": "grpcs://tor-zbc06c.3.secure.blockchain.ibm.com:13547",
                ...
	```
	{:codeblock}
	**Nota**: se desejar ter como alvo peers adicionais na rede, por exemplo, requerer endosso de um peer que não pertence à sua organização, será necessário obter as informações de terminal de API corretas desses peers.  Também é necessário armazenar o certificado de CA para a outra organização para verificar respostas que retornam ao seu aplicativo. Essas informações não são expostas em seu perfil de conexão; portanto, deve-se contatar o administrador apropriado para a Organização do Cloud Foundry e adquirir essas informações em uma operação fora da banda. A URL de serviço de solicitação é comum na rede; não é necessária nenhuma informação específica do membro para solicitar o serviço.

3. Vincule as informações do terminal de API a um arquivo de configuração de seu aplicativo, conforme mostrado no exemplo a seguir:
	```
	orderer_url: 'grpcs://fft-zbc01a.4.secure.blockchain.ibm.com:18603'
	```
	{:codeblock}

## Usando Índices CouchDB

Se a sua rede usar o CouchDB e você desejar aproveitar o recurso de indexação do CouchDB (que melhora o desempenho de CouchDB) os índices precisarão ser empacotados com o chaincode.

Para saber mais sobre o CouchDB e como configurar índices, consulte a [Documentação do Fabric sobre o CouchDB ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html)

## Hospedando aplicativos
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
3. Procure pelo diretório de seu aplicativo e envie seu aplicativo por push emitindo o comando a seguir. Isso pode levar vários minutos, dependendo do tamanho de seu aplicativo. Você verá os logs do {{site.data.keyword.Bluemix_notm}} em seu terminal; eles cessarão quando o aplicativo for ativado com êxito.
	```
	> cf push YOUR_APP_NAME_HERE
	```
	{:codeblock}
	É possível verificar seus logs do aplicativo emitindo um dos comandos a seguir:
	* `> cf logs YOUR_APP_NAME_HERE`
	* `> cf logs YOUR_APP_NAME_HERE --recent`


## Desconectando seu aplicativo da rede
{: #disconnect}
Conclua as etapas a seguir para remover a conexão entre seu aplicativo e a rede de {{site.data.keyword.blockchain}} no {{site.data.keyword.Bluemix_short}}.
1. Remova as informações do terminal da API de seu arquivo de configuração do aplicativo. Para referência, veja [Incluindo terminais de API de rede em seu aplicativo](#adding-network-api-endpoints-to-your-application).
2. Exclua seu contêiner de chaincode.
	1. Na tela "Canal" do Monitor de Rede, localize o canal no qual o chaincode está instalado.
	2. Na tela do canal específico, localize o chaincode que deseja desativar.
	3. Clique no botão **Excluir** e clique em **Enviar** no painel de exclusão do chaincode. O seu contêiner do chaincode será removido.
	![Excluir um chaincode](images/channel_chaincode.png "Excluir um chaincode")
