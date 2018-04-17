---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Desenvolvendo aplicativos
{: #dev_app}
Com aplicativos, é possível chamar o chaincode para consultar ou atualizar um livro-razão específico do canal em sua rede de {{site.data.keyword.blockchain}}.
{:shortdesc}

## Configurando o ambiente de desenvolvimento de aplicativo
É necessário instalar os pré-requisitos de software para desenvolver aplicativos que possam interagir com a rede de {{site.data.keyword.blockchain}} no {{site.data.keyword.Bluemix}}.
{:shortdesc}

*	Git ([Página de download do Git![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://git-scm.com/downloads){:new_window})

	Git é uma ferramenta de controle de versão com a qual se familiarizar, tanto para o desenvolvimento de chaincode como para o desenvolvimento de software em geral. Além disso, o Bash Git, que é instalado com Git no Windows, é uma alternativa excelente para o prompt de comandos do Windows.

	Use o comando a seguir para verificar sua instalação do Git.  Você deverá ver uma saída semelhante à abaixo:
	```
	$ git --version
	git version 2.11.1.windows.1
	```
	{:screen}

*	GoLang ([Página de download do GoLang ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://golang.org/dl){:new_window})

	A instalação do GoLang instala um conjunto de ferramentas de CLI Go, que são muito úteis para gravar o chaincode.  Por exemplo, o comando `go build` permite verificar se seu chaincode é realmente compilado antes de você tentar implementá-lo em uma rede.  No momento da gravação, sabe-se que esse chaincode é construído com êxito com a versão `1.8.3`.

	Use o comando a seguir para verificar sua versão do GoLang.  Você deverá ver uma saída semelhante à abaixo:
	```
	$ go version
	go version go1.8.3 windows/amd64
	```
	{:screen}

	Siga as [Instruções de Instalação ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://golang.org/doc/install){:new_window} para configurar corretamente as variáveis de ambiente. Verifique seu `GOPATH` usando o comando a seguir. Observe que o `GOPATH` não precisa corresponder ao exemplo; o importante é que você tenha essa variável configurada em um diretório válido em seu sistema de arquivos.
	```
	$ echo $GOPATH
C:\gopath
	```
	{:screen}

	É possível, então, verificar sua instalação do GoLang compilando o código GoLang com o exemplo do [hello world ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://golang.org/doc/install#testing){:new_window}.

*	Node.js ([Página de download do Node.js![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://nodejs.org/en/download/){:new_window}).  Escolha uma versão entre 6.9.5 < 7. Versões do Nó maiores que 7 podem causar erros ao fazer download dos módulos SDK.

	Use os comandos a seguir para verificar sua instalação do Node.js.  Você deverá ver uma saída semelhante à abaixo:
	```
	$ node -v
	v6.10.1

	$ npm -v
	3.10.10
	```
	{:screen}

## Gerando os certificados do lado do cliente
Não vamos nos aprofundar nas minúcias do x509 e da infraestrutura de chave pública; há muitos recursos externos para isso. Basta dizer que os fluxos de comunicação no Fabric usam operações de assinatura/verificação em cada ponto de contato. Desse modo, qualquer cliente que envie chamadas (ou seja, transações) para a rede precisará assinar cargas úteis (chave privada) e anexar um certificado x509 assinado corretamente para propósitos de verificação (certificado assinado). A chave privada e o certificado assinado, juntamente com um identificador MSP e o certificado raiz da Autoridade de certificação (CA) compõem o que é referido como o objeto de "contexto do usuário". Novamente, não há necessidade de detalhes extrínsecos. Nós simplesmente nos comunicaremos com a Autoridade de certificação apropriada e recuperaremos as chaves e os certificados que permitem que o objeto seja formado. Este processo é referido como "inscrição". Depois de formar o objeto de contexto do usuário, a "configuração" ou "obtenção" desse contexto do usuário é tão fácil quanto chamar uma API do seu aplicativo. Neste ponto, o aplicativo (ou seja, o cliente) é equipado com todos os artefatos necessários e está pronto para se comunicar com a rede. Veremos as duas abordagens para recuperar as chaves e os certificados.

### Linha de comandos
Esta é a mais simples das duas abordagens. Primeiro, siga as instruções para construir o [cliente Fabric CA ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html). Esta etapa permite se comunicar com um Servidor de CA e receber de volta certificados e chaves formatados adequadamente.

Segundo, faça download dos certificados TLS em [seu {{site.data.keyword.Bluemix_notm}} ![Ícone de fazer download](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert) e salve o conteúdo em uma pasta, por exemplo, ``$HOME/tls``.  Esta etapa permite que o fluxo de dados seja criptografado na conexão.

Finalmente, abra o arquivo JSON **Perfil de conexão** na sua tela **Visão geral** no Monitor de rede e localize as variáveis relevantes:
* URL para CA: `url` sob `certificateAuthorities`
* ID do usuário administrador: ``enrollId``
* Senha do administrador: ``enrollSecret``
* Nome da CA: ``caName``

Usando o cliente Fabric CA, podemos enviar uma chamada de "inscrição" à nossa Autoridade de Certificação, passando no caminho de certificados TLS e pelas quatro sequências acima com o comando a seguir:
```
$GOPATH/bin/fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
```

O binário ``fabric-ca-client`` é colocado em ``$GOPATH/bin``, portanto, você precisará estar no local apropriado em sua máquina local quando executar este comando.  Uma chamada real pode ser semelhante ao comando de exemplo a seguir:
```
./fabric-ca-client enroll -u https://admin:B84F2C5436@tor-zbc01a.3.secure.blockchain.ibm.com:23042 --tls.certfiles /Users/XYZ/Downloads/3.secure.blockchain.ibm.com.rootcert --caname PeerOrg1CA
```

Localize seu certificado de administrador em `$HOME/.fabric-ca-client/msp/signcerts/cert.pem`. É possível, então, fazer upload do certificado administrativo para sua rede de blockchain do Monitor de Rede. Para obter mais informações sobre como incluir certificados, veja [a guia "Certificados" da tela "Membro"](v10_dashboard.html#members) no Monitor de rede.

Também é possível localizar o certificado raiz da CA e a chave privada do administrador nos diretórios a seguir:
* Certificado raiz da CA: `$HOME/.fabric-ca-client/msp/cacerts/--<ca_name>.pem`
* A chave privada do administrador: `$HOME/.fabric-ca-client/msp/keystore/<>_sk file`

### SDK
Há dois repositórios do Hyperledger que contêm excelentes recursos e scripts para entender como interagir programaticamente com uma Autoridade de certificação. O repositório ``fabric-samples`` contém o exemplo de "transferência de saldo" e o repositório ``fabric-sdk-node`` possui uma série de testes de Serviços da CA. Se você pretende emitir as suas solicitações de inscrição no lado do aplicativo, então, precisará entender completamente as APIs que precisam ser expostas nos pacotes ``fabric-ca-client`` e ``fabric-client``.  Use esses scripts e repositórios como uma linha de base para estruturar seu aplicativo.

Vamos dar uma olhada rápida em alguns trechos de código chave do exemplo de "transferência de saldo":

Primeiro, precisamos criar o nosso objeto do cliente e configurar uma instância de armazenamento de chave/valor na qual os nossos certificados e chaves serão estacionados. Podemos fazer isso com um factory method simples -- ``newCryptoSuite`` - que se estende até a classe ``Client`` do ``BaseClient``. Aqui está uma olhada rápida no código:

```
# <PUBLIC_PRIVATE_KEY_PATH> denota o caminho em sua máquina local no qual você deseja armazenar sua chave e certificado
let cryptoSuite = hfc.newCryptoSuite();
cryptoSuite.setCryptoKeyStore(hfc.newCryptoKeyStore({path: <PUBLIC_PRIVATE_KEY_PATH>)}));
client.setCryptoSuite(cryptoSuite);
```

A prática comum seria exportar uma variável de ambiente que define o caminho de chave/valor em sua máquina e transmiti-la para a função acima. Agora que já definimos nosso KVS, vamos usar alguns métodos da classe `` FabricCAServices``. Esta classe é uma implementação do cliente Fabric CA e como tal permitirá que nos comuniquemos com o Servidor de CA. Primeiramente, precisamos passar algumas informações ao nosso cliente de autoridade de certificação, a saber, a URL da Autoridade de certificação:

```
# a caURL pode ser definida manualmente ou configurando uma variável de ambiente
# a variável copService é definida por cima do programa
let caUrl = "https://XXX:7054";
var caClient = new copService(caUrl, null, '' /* default CA */, cryptoSuite);
```

Em seguida, vamos realmente enviar a chamada de "inscrição" para o Servidor de CA. Isso retornará ao cliente uma chave privada e uma chave pública que foram agrupadas em um certificado x509 e assinadas pela CA de destino -- chamamos isso de signcert ou certificado de inscrição. Esse certificado de inscrição ou "eCert" é a peça chave, porque permite que entidades de rede verifiquem transações e chamadas originadas do cliente:

```
return caClient.enroll({
enrollmentID: username,
enrollmentSecret: password
```

E a tarefa final é realmente configurar o conjunto de criptografia e construir o contexto do usuário:

```
	member.setCryptoSuite(client.getCryptoSuite());
	return member.setEnrollment(enrollment.key, enrollment.certificate, getMspID(userOrg));
}).then(() => {
	return client.setUserContext(member);
```

É possível, então, fazer upload do certificado administrativo para sua rede de blockchain do Monitor de Rede. Para obter mais informações sobre como incluir certificados, veja [a guia "Certificados" da tela "Membro"](v10_dashboard.html#members) no Monitor de rede.

## Desenvolvendo aplicativos
{: #developing-applications}
É possível desenvolver o seu aplicativo em JavaScript ou Java e alavancar as APIs disponíveis nos SDKs do Hyperledger Fabric Client para permitir a interação entre o seu aplicativo e a sua rede. Um aplicativo precisa incluir pelo menos as informações a seguir:
* Nome e versão do chaincode a ser chamado.
* Informações do terminal de API de seus recursos de rede, incluindo solicitantes, CA e peers.
* Funções para consultar ou atualizar o livro-razão na rede.  Se você desejar alta disponibilidade, precisará considerar o failover de nó em seu aplicativo.

É possível localizar aplicativos de amostra para o **Enterprise Plan** no {{site.data.keyword.Bluemix_short}} em [Aplicativos de amostra](howto/sample_applications.html).  Use esses chaincodes e aplicativos de amostra como um modelo para criar sua própria solução de negócios.

## Incluindo terminais de API de rede em seu aplicativo
É necessário incluir terminais de API de seus recursos de rede em seu aplicativo para que ele possa interagir com a sua rede do {{site.data.keyword.blockchain}} no {{site.data.keyword.Bluemix_short}}. Se você não tiver uma rede do {{site.data.keyword.blockchain}} no {{site.data.keyword.Bluemix_short}}, será possível criar uma com o Starter Plan ou o Enterprise Plan. Para obter mais informações, veja [Governar a rede do Starter Plan](get_start_starter_plan.html) e [Governar a rede do Enterprise Plan](get_start.html.

É possível localizar os terminais de API no perfil de conexão de sua rede. O perfil de conexão está no formato JSON e contêm as informações do terminal de API e enrollIDs/segredos para os seus recursos de rede, incluindo solicitante, autoridade de certificação e nós de peer. O seu aplicativo irá interagir com peers e outros recursos de rede por meio desses terminais de API.

1. Recupere as informações do terminal de API de seus recursos de rede por meio de seu Monitor de rede com um dos métodos a seguir:
	* Para obter as informações do terminal de API que são específicas para um chaincode, na tela do canal específico, na qual o chaincode está em execução, localize o chaincode e clique no botão **JSON**.
	![Terminais de API por chaincode](images/channel_chaincode_detail.png "Terminais de API por chaincode")
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

	**Nota**: se desejar ter como alvo peers adicionais na rede, por exemplo, requerer endosso de um peer que não pertence à sua organização, será necessário obter as informações de terminal de API corretas desses peers.  Também é necessário armazenar o certificado de CA para a outra organização para verificar respostas que retornam ao seu aplicativo. Essas informações não são expostas em seu perfil de conexão; portanto, deve-se contatar o administrador apropriado para a Organização do Cloud Foundry e adquirir essas informações em uma operação fora da banda. A URL de serviço de solicitação é comum na rede; não é necessária nenhuma informação específica do membro para solicitar o serviço.

3. Vincule as informações do terminal de API a um arquivo de configuração de seu aplicativo, conforme mostrado no exemplo a seguir:
	```
	orderer_url: 'grpcs://fft-zbc01a.4.secure.blockchain.ibm.com:18603'
	```

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
3. Procure pelo diretório de seu aplicativo e envie seu aplicativo por push emitindo o comando a seguir. Isso pode levar vários minutos, dependendo do tamanho de seu aplicativo. Você verá os logs do {{site.data.keyword.Bluemix_notm}} em seu terminal; eles cessarão quando o aplicativo for ativado com êxito.
	```
	> cf push YOUR_APP_NAME_HERE
	```
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
	![Excluir um chaincode](images/channel_chaincode_detail.png "Excluir um chaincode")
