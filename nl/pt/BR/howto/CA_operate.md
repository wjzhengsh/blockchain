---

copyright:
  years: 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Operando uma autoridade de certificação no {{site.data.keyword.cloud_notm}} Private
{: #ca-operate}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

As autoridades de certificação (CAs) fornecem identidades na rede. Uma CA pode ser considerada como sendo semelhante a um tabelião público confiável que é usado para estabelecer a confiança entre várias partes. Cada entidade na rede recebe um certificado que uma CA raiz sinaliza para encapsular a identidade digital da entidade. Esse certificado é a raiz de confiança para todas as operações de assinatura e de verificação que são executadas na rede.
{:shortdesc}

Cada organização em uma rede de blockchain multi-cloud deve implementar sua própria CA. A CA de sua organização assinará solicitações para os componentes que sua organização opera, como um serviço de ordenação, peers ou aplicativos. Portanto, é necessário implementar sua CA antes de implementar quaisquer outros componentes.

É necessário executar algumas etapas de pré-requisito para operar sua Autoridade de Certificação:

- [Configurar suas CLIs](#ca-kubectl-configure)
- [Recuperar a URL de sua autoridade de certificação](#ca-url)
- [Faça download de seu certificado TLS da CA](#ca-tls)
- [Configurar o cliente da CA do Fabric](#fabric-ca-client)
- [Gerar certificados com seu administrador de CA](#enroll-admin)

Depois de concluir as etapas de pré-requisito, é possível usar sua CA para registrar novas identidades na rede com sua organização e gerar certificados que seus aplicativos poderão usar.

- [Usando a CA para implementar um solicitador ou peer](#deploy-orderer-peer)
<!-- [Register and enroll client applications](#register-enroll-app)-->

Este guia também fornece algumas informações conceituais importantes sobre como operar sua CA e como usar seus certificados para gerenciar uma rede de blockchain.

- [Fornecedores de serviço de associação (MSPs)](#msp)
- [Certificados TLS](#tls)

## Pré-requisitos
{: #prerequisites}

Se você estiver fundando ou associando uma rede, será necessário concluir essas etapas de pré-requisitos para usar sua CA e implementar outros componentes e identidades que pertencem à sua organização.

### Configurando as CLIs
{: #ca-kubectl-configure}

É necessário usar a ferramenta de linha de comandos **kubectl** para se conectar ao contêiner da CA que é executado no ICP.

1. Efetue login na IU do cluster do ICP. Navegue para a guia **Ferramentas de linha de comandos** e clique em **CLI do Cloud Private**. Você verá as ferramentas a seguir que podem ser transferidas por download.

   * Instalar a CLI e os plug-ins do IBM Cloud Private
   * Instalar a CLI Kubectl
   * Instalar o Helm
   * Instalar a CLI do Istio

  Para operar uma CA, é necessário usar as primeiras **três** ferramentas, entre as quais o Helm é opcional. Clique em cada um deles e execute os comandos `curl` para o tipo de máquina que você está usando. Em seguida, emita os comandos `chmod` e `sudo mv` para cada ferramenta. O comando `chmod` mudará a permissão da CLI em questão para torná-la executável e o comando `sudo mv` moverá o arquivo e o renomeará.

  Os comandos para a primeira ferramenta **cloudctl** podem ser semelhantes ao exemplo a seguir:

  ```
  chmod +x cloudctl-darwin-amd64<suffix of your binary> sudo mv path/to/cloudctl-darwin-amd64<suffix> /usr/local/bin/cloudctl
  ```
  {:codeblock}

  Os comandos para a segunda ferramenta **kubectl** podem ser semelhantes ao exemplo a seguir:

  ```
  chmod +x kubectl-darwin-amd64<suffix of your binary> sudo mv path/to/kubectl-darwin-amd64<suffix> /usr/local/bin/kubectl
  ```
  {:codeblock}

2. Depois de configurar o **kubectl**, execute o comando a seguir:

  ```
  kubectl get pods
  ```
  {:codeblock}

  Se bem-sucedido, o comando deve retornar uma resposta semelhante ao exemplo a seguir, em que `ca-6cf85f6687-hcxpl` é o nome do contêiner da CA.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  ca-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Agora você está pronto para usar a ferramenta **kubectl** para obter a URL da CA.

3. Opcionalmente, se você desejar usar **Helm**, conclua algumas etapas adicionais. Observe que o Helm é opcional para ser instalado e você não precisa usá-lo nestas instruções. No entanto, pode ser útil gerenciar suas liberações do Helm e criar seus próprios archives para implementar no ICP.

  1. Clique em "Instalar o Helm" e execute o comando `curl` por meio da UI do ICP.
  2. Descompacte o arquivo `tar` executando o comando a seguir:  
    ```
    tar -xzvf helm-darwin-amd64<suffix>
    ```
    {:codeblock}
  3. Execute o comando `sudo mv` anexando `/helm` a `darwin-amd64`. Observe que não é necessário executar o comando `chmod` para o Helm. Por exemplo:  
    ```
    sudo mv darwin-amd64/helm/ /usr/local/bin/helm
    ```
    {:codeblock}

  É possível executar o comando `helm help` para confirmar se o Helm está instalado com êxito.

### Recuperando a URL da autoridade de certificação
{: #ca-url}

É necessário destinar a URL da CA para solicitações para gerar certificados ou registrar-se com uma nova identidade. É possível localizar sua URL de CA usando a IU do console do ICP. Você precisará ser um [Administrador de cluster ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Funções e ações do administrador de cluster") para concluir as etapas a seguir:

1. Efetue login no console do ICP e clique no ícone **Menu** no canto superior esquerdo.
2. Clique em **Carga de trabalho** > **Liberações do Helm**.
3. Localize o nome de sua Liberação do Helm e abra o painel de detalhes da Liberação do Helm.
4. Role para baixo até a seção **Notas** na parte inferior do painel. Na seção **Notas**, é possível ver um conjunto de comandos para ajudar a operar sua implementação de CA.
5. Se ainda não tiver feito isso, siga as etapas para configurar a [CLI kubectl](#ca-kubectl-configure), que você precisa usar para interagir com seu contêiner de CA.
6. Em sua CLI, execute o primeiro comando na nota, que segue **1. Obtenha a URL do aplicativo executando estes comandos:** Esse comando imprime a URL da CA e a porta, que é semelhante ao exemplo a seguir. Salve esse valor como a URL do aplicativo a ser usada com os comandos subsequentes.

  ```
  http://9.30.94.174:30167
  ```

**Nota:** se você implementar sua CA atrás de um firewall, será necessário abrir um intermediário para ativar a rede na plataforma para concluir um handshake TLS com sua CA. É necessário ativar um intermediário apenas para a porta que foi concatenada com a URL da CA.


### Recuperando o certificado TLS da CA
{: #ca-tls}

É necessário fazer download de seu certificado TLS da CA e transmiti-lo junto com seus comandos para se comunicar com sua CA a partir de um cliente remoto.

1. Efetue login no console do ICP. Clique no ícone **Menu** no canto superior esquerdo.
2. Clique em **Carga de trabalho** > **Liberações do Helm**.
3. Localize o nome de sua Liberação do Helm e abra o painel de detalhes da Liberação do Helm.
4. Role para baixo até a seção **Notas** na parte inferior do painel. Na seção **Notas**, é possível ver um conjunto de comandos para ajudar a operar sua implementação de CA.
5. Se ainda não tiver feito isso, siga as etapas para configurar a [CLI kubectl](#ca-kubectl-configure), que você precisa usar para interagir com seu contêiner de CA.
6. Em sua CLI, execute os comandos na terceira nota, que segue **3. Obter certificado TLS:** Esse comando salva seu certificado TLS como o arquivo `tls.pem` em seu diretório local. É necessário referenciar esse certificado em um comando futuro. Tome nota de sua localização.
7. O comando também converte o certificado no formato base64 e o imprime. O resultado é semelhante à sequência abaixo:

  ```
    LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg==
  ```

  **Importante:** é necessário copiar essa sequência e salvá-la como seu `ICP CA TLS certificate`. Você a usará quando implementar componentes adicionais.

### Configurando o cliente Fabric CA
{: #fabric-ca-client}

É possível usar o cliente Fabric CA para operar sua CA. Essas instruções explicam como usar o cliente Fabric CA para inscrever-se e registrar identidades para outros componentes que pertencem à sua organização. Também é possível usar os SDKs do Fabric para concluir as etapas de pré-requisito.

1. É necessário fazer download do [cliente Fabric CA ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html#fabric-ca-client "Fazer download do cliente Fabric CA") para seu sistema de arquivos local.

  A maneira mais fácil de obter o cliente Fabric CA é fazer download de todos os binários da ferramenta Fabric diretamente. Navegue para um diretório no qual gostaria de fazer download dos binários com sua linha de comandos e busque-os executando o comando [Curl ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl") a seguir.

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
  ```
  {:codeblock}

  Esse comando instala os binários em um diretório `bin/`.

2. Configure o caminho `PATH` para o diretório no qual os binários da ferramenta Fabric foram transferidos por download:

  ```
  export PATH=$PATH:<full/path/to/fabric-client/bin>
  ```
  {:codeblock}

  Por exemplo, se instalasse os binários em seu diretório inicial, você configuraria seu `PATH` como:

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. Crie a pasta na qual armazenará os certificados de seu administrador de CA.

  ```
  mkdir -p $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

4. Configure o valor da variável de ambiente `$FABRIC_CA_CLIENT_HOME` para que seja o caminho em que o cliente de CA armazenará os certificados [MSP](#msp) gerados. Assegure-se de remover o material de configuração que pode ser criado por tentativas anteriores. Se você não executou o comando `enroll` antes, a pasta `msp` e o arquivo `.yaml` não existem.

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/ca-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

5. Copie o certificado TLS que você [transferiu por download do ICP](#ca-tls) para um diretório no qual é possível referenciá-lo em comandos futuros. Assegure-se de que você esteja em seu diretório `$HOME/fabric-ca-client`.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

### Gerando certificados com seu administrador de CA
{: #enroll-ca-admin}

Antes de implementar componentes ou aplicativos clientes com sua CA, é necessário gerar certificados que autenticam você como um administrador com a capacidade de registrar novos usuários. O processo de geração dos certificados necessários, sua chave privada e seu certificado público (também conhecido como seu certificado de inscrição ou signCert), é chamado de **inscrição**.

É possível gerar certificados apenas usando identidades que foram registradas com sua Autoridade de Certificação. Forneça o nome da identidade e o segredo para gerar certificados. Uma identidade do **administrador de CA** foi registrada automaticamente para você quando você implementou sua CA. Agora é possível usar esse nome de administrador e senha para emitir um comando `enroll` com o cliente Fabric CA para gerar uma pasta MSP com certificados que são, então, usados para registrar outras identidades de peer ou solicitador.

1. Assegure-se de concluir as etapas para [configurar o cliente Fabric CA](#fabric-ca-client) e que `$FABRIC_CA_CLIENT_HOME` esteja configurado no diretório no qual você deseja armazenar seus certificados de administrador de CA.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```
  {:codeblock}

2. Execute o comando a seguir para gerar certificados:

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name>  --tls.certfiles  <ca_tls_cert_path>
  ```
  {:codeblock}

  O `<enroll_id>` e `<enroll_password>` no comando são o [Nome e a senha do usuário administrador de CA](CA_deploy_icp.html#admin-secret) que você passou para o segredo de Kubernetes quando implementou a Autoridade de certificação. Insira a [URL da CA](#ca-url) dentro da `<ca_url_with_port>`. Deixe fora o `http://` no início. O `<ca_name>` é o valor que você forneceu para o campo `CA Name` ao implementar a CA.

  O `<ca_tls_cert_path>` é o caminho completo de seu [certificado TLS da CA](#ca-tls).

  Uma chamada real pode ser semelhante ao comando de exemplo a seguir:

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  O comando `enroll` gera um conjunto completo de certificados, que é conhecido como uma pasta Membership Service Provider (MSP), que está localizada dentro do diretório no qual você configura o caminho `$HOME` para seu cliente Fabric CA. Por exemplo, `$HOME/fabric-ca-client/ca-admin`. Para obter mais informações sobre MSPs e o que a pasta MSP contém, consulte [Membership Service Providers](#msp).

  Se o comando `enroll` falhar, consulte o [tópico de Resolução de Problemas](#ca-enroll-error) para obter as possíveis causas.

É possível executar um comando de árvore para verificar se você concluiu todas as etapas de pré-requisito. Navegue para o diretório no qual você armazenou seus certificados. Um comando de árvore deve gerar um resultado semelhante à estrutura a seguir:

```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
└── catls
    └── tls.pem
```

## Usando a CA para implementar um solicitador ou peer
{: #deploy-orderer-peer}

Antes de implementar um solicitador ou peer, é necessário criar um arquivo JSON de configuração que contenha informações importantes sobre a identidade do componente e sua CA. Em seguida, é necessário passar esse arquivo para o gráfico do Helm durante a configuração usando um objeto [Segredo do Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/configuration/secret/). Esse arquivo permite que seu solicitador ou peer obtenha os certificados necessários da CA para ingressar em uma rede de blockchain. Esse arquivo também contém um certificado de administrador que permite que você opere seu componente como um usuário administrador.

As instruções a seguir fornecem a você um [arquivo de configuração JSON de modelo](#config-file-template) que pode ser editado e salvo em seu sistema de arquivos local e o instruem sobre como usar sua CA para concluir este arquivo.

- Siga as instruções abaixo se estiver implementando um solicitador no ICP ou implementando um peer para se conectar a um consórcio que está hospedado no ICP.
- Se você deseja implementar um peer para se conectar ao Starter Plan ou ao Enterprise Plan, siga as instruções em [Implementando peers no IBM Cloud Private para conectar-se ao Starter Plan ou ao Enterprise Plan](/docs/services/blockchain/peer_deploy_ibp.html). Essas etapas o instruem sobre como usar a CA no {{site.data.keyword.blockchainfull_notm}} Platform para implementar nosso peer no ICP.

### Arquivo de configuração
{: #config-file-template}

O modelo para o arquivo de configuração pode ser localizado abaixo:
```
{
	"enrollment": {
		"component": {
			"cahost": "", 			"caport": "", 			"caname": "", 			"catls": {
				"cacert": ""
			}, 			"enrollid": "", 			"enrollsecret": "", 			"admincerts": [""]
		}, 		"tls": {
			"cahost": "", 			"caport": "", 			"caname": "", 			"catls": {
				"cacert": ""
			}, 			"enrollid": "", 			"enrollsecret": "", 			"csr": {
				"hosts": [""] }
		}
	}
}
```
{:codeblock}

Copie esse arquivo inteiro em um editor de texto no qual é possível editá-lo e salvá-lo em seu sistema de arquivos local como um arquivo JSON.

### Informações de conexão da CA

Primeiro, precisamos fornecer as informações de conexão de sua CA no ICP. Use as informações a seguir para concluir valores na seção `"component"` do arquivo:

- Os valores `"cahost"` e `"caport"` são a URL e a porta da [URL da CA](#ca-url). Por exemplo, se sua URL da CA for http://9.30.94.174:30167, o valor de `"cahost"` será `9.30.94.174` e a `"caport"` será `30167`.
- O `"caname"` é o nome da CA que foi especificado quando você implementou o gráfico Helm da CA. Você referenciou o nome da CA em seu comando quando se inscreveu usando a [identidade do administrador da CA](#enroll-admin).
- O `"cacert"` é o certificado TLS codificado em base64 de sua CA. [Recupere o certificado TLS de sua CA](#ca-tls) a partir de seu console do ICP usando a nota em sua liberação do Helm e insira a saída base64 dos comandos de nota na seção a seguir.
  ```
  "catls": {
    "cacert": ""
  ```
  {:codeblock}

  Após a atualização, o campo `"cacert"` é semelhante ao exemplo a seguir:

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

- Os campos na seção `"tls"` abaixo requerem as mesmas informações que você transmitiu para as seções do componente acima, exceto que é necessário usar o valor do nome da instância TLS da CA que foi especificado durante a implementação da CA. Use o mesmo certificado TLS para o valor de `"cacert"`.
  ```
  "tls": {
    "cahost": "",
    "caport": "",
    "caname": "",
    "catls": {
      "cacert": ""
  ```
  {:codeblock}

Depois de recuperar as informações de conexão da Autoridade de Certificação, é necessário usar o cliente Fabric CA para concluir várias etapas operacionais:

1. [Registre o novo componente com sua CA](#register-component).

2. Também é necessário [registrar um administrador de componente](#register-admin) e, em seguida, [gerar certificados para o administrador do componente](#enroll-admin) dentro de uma pasta MSP. Não será necessário concluir essa etapa se você já tiver registrado um administrador para implementar outro componente.

3. [Registre o novo componente com sua CA TLS](#tls-register-component).

### Registrando a identidade do componente com a CA
{: #register-component}

Se você deseja localizar um consórcio implementando um serviço de ordenação e incluindo organizações nele, ou para implementar os peers e associá-los aos canais, primeiro é necessário registrar a identidade do componente com sua CA. Sua implementação de componente pode, então, gerar certificados que são necessários para que o peer ou o solicitador participe de uma rede.

1. [Gere certificados com seu administrador de CA](#enroll-ca-admin) usando o cliente Fabric CA. Use esses certificados de administrador para emitir os comandos a seguir. Assegure-se de que `$FABRIC_CA_CLIENT_HOME` esteja configurado como `$HOME/fabric-ca-client/ca-admin`.

  ```
  echo $FABRIC_CA_CLIENT_HOME
  $HOME/fabric-ca-client/ca-admin
  ```

2. Emita o comando a seguir para localizar sua afiliação e seu nome da organização.
  ```
  fabric-ca-client affiliation list --caname <ca_name> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  Seu comando pode ser semelhante ao exemplo a seguir:
  ```
  fabric-ca-client affiliation list --caname org1CA --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Você verá informações semelhantes ao exemplo a seguir:
  ```
  affiliation: org1 affiliation: org1.department1
  ```

  Anote o segundo valor de **afiliação**, por exemplo, `org1.department1`. Você precisará usar esse valor no comando abaixo.

3. Execute o comando a seguir para registrar o solicitador ou peer.

  ```
  fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type <component_type> --id.secret <secret> --tls.certfiles <ca_tls_cert_path>
  ```
  {:codeblock}

  Crie um nome e uma senha para o componente e, em seguida, use-os para substituir `name` e `secret`. **Importante:** anote essas informações. Configure o `--id.type` como `orderer` se você estiver implementando um solicitador ou configure-o como `peer` se estiver implementando um peer. O comando pode ser semelhante ao exemplo a seguir:

  ```
  fabric-ca-client register --caname org1CA --id.affiliation org1.department1 --id.name peer --id.secret peerpw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Depois de executar esse comando, é necessário inserir o `name` e `secret` como o `"enrollid"` e `"enrollsecret"` no arquivo de configuração, na seção `"component"`:

  ```
  "component": {...
    },
    "enrollid": "peer1",
    "enrollsecret": "peerpw",
  ```

  É possível registrar uma identidade apenas uma vez. Se você tiver algum problema, tente um comando com um novo nome do usuário e senha. Como uma medida de segurança, use cada identidade e o enrollID e segredo associados para implementar somente um peer. Embora seja possível usar uma identidade de **administrador** para vários componentes (essa é a nossa estratégia de implementação recomendada), não reutilize IDs de peer e senhas.

  Quando o comando for concluído com êxito, você verá informações semelhantes ao exemplo a seguir:

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peerpw
  ```

#### Registrando o administrador
{: #register-admin}

Depois de registrar o componente, também é necessário criar uma identidade de administrador que possa ser usada para operar o componente. Primeiro, é necessário registrar essa nova identidade com sua CA e usá-la para gerar uma pasta MSP. Em seguida, é necessário incluir o signCert dos usuários administrativos no arquivo de configuração, no qual ele se tornará um certificado de administrador do solicitador ou peer durante a implementação. Isso permite que você use os certificados da identidade do administrador para operar sua rede de blockchain, como iniciando um novo canal ou instalando o chaincode em seus peers.

É necessário criar apenas uma identidade de administrador para os componentes que pertencem à sua organização. Se você estiver implementando vários peers ou um solicitador, será necessário concluir essas etapas apenas uma vez. É possível usar o signCert que você gerou para um componente para implementar qualquer um dos peers ou o solicitador.

Assegure-se de que seu `$FABRIC_CA_CLIENT_HOME` esteja configurado com o caminho para o MSP de seu Administrador de CA.

```
echo $FABRIC_CA_CLIENT_HOME
$HOME/fabric-ca-client/ca-admin
```
{:codeblock}

Registre a identidade de administrador do componente com a CA executando o comando a seguir:

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type user --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Crie uma nova identidade de usuário `name` e `secret` para o administrador. Essa identidade se tornará o administrador para todos os componentes implementados. Certifique-se de usar valores diferentes do que para a identidade do peer ou solicitador que você acabou de registrar. O comando é semelhante ao exemplo a seguir.

```
fabric-ca-client register --caname org1CA --id.name peeradmin --id.affiliation org1.department1 --id.type user --id.secret peeradminpw --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

**Importante:** anote essas informações. Você usará esse `name` e `secret` para gerar a pasta MSP usando o comando `enroll`.

#### Gerando a pasta MSP do administrador
{: #enroll-admin}

Após o registro do administrador do componente, é possível gerar os certificados usando o comando a seguir:

```
fabric-ca-client enroll -u https://<peer_admin_enroll_id>:<peer_admin_enroll_password>@<ca_url_with_port> --caname <ca_name> -M $HOME/path/to/peer-admin/msp --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Por exemplo:

```
fabric-ca-client enroll -u https://peeradmin:peeradminpw@9.30.94.174:30167 --caname org1CA -M $HOME/fabric-ca-client/peer-admin/msp --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Depois que esse comando for concluído com êxito, ele gerará uma nova pasta MSP no diretório que você especificou usando o sinalizador `-M`. Esse diretório contém os certificados que serão usados para operar seus componentes. É possível verificar se o comando enroll funcionou usando um comando de árvore. Navegue para o diretório no qual você armazenou seus certificados. Um comando de árvore deve gerar um resultado semelhante à estrutura a seguir:

```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 2a97952445b38a6e0a14db134645981b74a3f93992d9ddac54cb4b4e19cdf525_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── orderer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-admin
│   └── msp
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │   └── 45a7838b1a91ddfe3d4d22a5a7f2639b868493bcce594af3e3ceb9c07899d117_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

Neste diretório MSP, abra o arquivo signCert do novo usuário e converta-o para o formato base64 usando os comandos a seguir:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/<path-to-peer-admin>/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

**Nota:** é importante que a sequência gerada usando o comando acima seja formatada como uma única linha. Isso deve ser semelhante a:

 ```
 LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
 ```
 não assim:

 ```
 LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
 ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
 Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
 VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
 WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
 ```

Por exemplo:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
```
{:codeblock}

Esse comando imprime uma sequência semelhante ao exemplo a seguir:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
```

Copie essa sequência para o campo `"admincerts"` na seção do componente no arquivo de configuração.

#### Registrando a identidade do componente com a CA do TLS
{: #tls-register-component}

Também é necessário registrar o solicitador ou peer com a CA do TLS. Para fazer isso, primeiro será necessário se inscrever usando o administrador da CA do TLS. Mude `$FABRIC_CA_CLIENT_HOME` para um diretório no qual você deseja armazenar seus certificados de administrador da CA do TLS.

```
cd $HOME/fabric-ca-client
mkdir tlsca-admin
export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
```
{:codeblock}

Execute o comando abaixo para se inscrever como seu administrador com relação à CA do TLS. O comando é o mesmo que você usou para se inscrever como seu [administrador da CA](#enroll-ca-admin), você apenas usará o nome da instância do TLS da CA que foi especificado durante sua [configuração da CA.](CA_deploy_icp.html#icp-ca-configuration-parms) Certifique-se de usar o mesmo `ca-admin-name` e `ca-admin-password` em seu segredo de CA.

```
fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Uma chamada real pode ser semelhante ao exemplo a seguir:

```
fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname org1tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Depois de se inscrever, você terá os certificados necessários para registrar seu componente com a CA do TLS.
Execute o comando a seguir para registrar o solicitador ou peer:

```
fabric-ca-client register --caname <ca_name> --id.name <name> --id.affiliation org1.department1 --id.type peer --id.secret <password> --tls.certfiles <ca_tls_cert_path>
```
{:codeblock}

Esse comando é semelhante ao usado para registrar a identidade do componente com a CA, exceto o uso do nome da CA do TLS. Se você estiver implementando um solicitador em vez de um peer, configure `--id.type` como `orderer` em vez de `peer`. Deve-se fornecer a essa identidade um nome de usuário e senha diferentes do que foi usado com relação à sua CA padrão. Um registro real pode ser semelhante ao comando a seguir:

```
fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
```

Copie os valores de `name` e `secret` para `"enrollid"` e `"enrollsecret"` sob a seção `"tls"` do arquivo de configuração:

```
"tls": {...
  },
  "enrollid": "peertls",
  "enrollsecret": "peertlspw",
```

### Hosts de CSR (Solicitação de Assinatura de Certificado)
{: #csr-hosts}

É necessário fornecer um nome do host CSR para implementar um solicitador ou peer. Esse nome do host inclui o endereço IP do proxy do cluster no qual você implementará o componente e o `service host name` que é gerado quando o gráfico Helm é implementado.

#### Localizando o valor do endereço IP do proxy do cluster

Se você deseja implementar um solicitador ou peer no mesmo cluster ICP no qual implementou sua CA, assegure-se de que tenha uma função [Administrador de cluster ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Funções e ações do administrador de cluster") no cluster ICP em que o solicitador ou o peer será implementado. Em seguida, insira o mesmo IP de proxy que você usou quando [configurou para sua CA](CA_deploy_icp.html#icp-ca-configuration-parms). Se desejar implementar o solicitador ou peer em um cluster diferente, será possível recuperar o valor do endereço IP do proxy do cluster a partir do console do ICP, concluindo as etapas a seguir:

1. Efetue login no console do ICP. No painel de navegação à esquerda, clique em **Plataforma** e, em seguida, em **Nós** para visualizar os nós que estão definidos no cluster.
2. Clique no nó com a função `proxy` e, em seguida, copie o valor do `IP do host` da tabela.
3. Insira o `Host IP` como o valor para `"hosts"` na seção `"csr"` do arquivo de configuração abaixo:

  ```
  "csr": {
    "hosts": [""]
  }
  ```
  {:codeblock}

#### Determinando o nome do host do serviço

Um `service host name` é gerado quando um gráfico Helm é implementado. Ele é baseado no `helm release name` que você usa quando implementa o gráfico Helm.

- Se você estiver implementando um solicitador, o `service host name` será o `helm_release_name` que você especifica durante a implementação com a sequência `-orderer` incluída no final. Por exemplo, se o endereço do proxy IP do cluster for `9.42.134.44` e você especificar um `helm release name` igual a `org1orderer`, será possível inserir os valores a seguir na seção `"csr"` do arquivo:

  ```
  "csr": {
    "hosts": [
       "9.42.134.44",
       "org1orderer-orderer"
     ]
  }
  ```
  {:codeblock}

- Se você estiver implementando um peer, o `service host name` será o `helm release name` que você especifica durante a implementação sem uma sequência suplementar. Por exemplo, se o endereço do Proxy IP do cluster for "9.42.134.44" e o `helm release name` for `org1peer1`, será possível inserir os valores a seguir em "hosts" csr:

  ```
  "csr": {
    "hosts": [
       "9.42.134.44",
      "org1peer1"
    ]
  }
  ```
  {:codeblock}

### Concluindo o arquivo de configuração

Depois de concluir todas as etapas acima, seu arquivo de configuração atualizado poderá ser semelhante ao exemplo a seguir:

```
{
    "enrollment": {
        "component": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "chandra46CA",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJ0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peer",
            "enrollsecret": "peerpw",
            "admincerts": [
                "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMzRENDQW9PZ0F3SUJBZ0lVS2Vib0drZEJqenM5bllRbkVQTWp0VG56YTVrd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE9URTNNRE13TUZvWERURTVNVEV4T1RFM01EZ3dNRm93ZmpFTE1BaKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApjbXhsWkdkbGNqRXVNQXNHQTFVRUN4TUVkWE5sY2pBTEJnTlZCQXNUQkc5eVp6RXdFZ1lEVlFRTEV3dGtaWEJoCmNuUnRaVzUwTVRFUU1BNEdBMVVFQXhNSFlXUnRhVzR4Y0RCWk1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUgKQTBJQUJLbjUwdEU5TmpZb0RFNDBqalh6RUJ2T2c3Y3RtOElRd0laMnRkS29iNEwwVVhKdSs1Tkt5S2dyUk9vbApWcjBmQmg5cWZWMjl4Nms0T2dmMFNiVklBZWlqZ2ZRd2dmRXdEZ1lEVlIwUEFRSC9CQVFEQWdlQU1Bd0dBMVVkCkV3RUIvd1FDTUFBd0hRWURWUjBPQkJZRUZOYWFkV0VzcGp2Smk1akpiVktiS2M3ZU1wUmhNQjhHQTFVZEl3UVkKTUJhQUZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQ2NHQTFVZEVRUWdNQjZDSEdOb1lXNWtjbUZ6TFcxaQpjQzV5WVd4bGFXZG9MbWxpYlM1amIyMHdhQVlJS2dNRUJRWUhDQUVFWEhzaVlYUjBjbk1pT25zaWFHWXVRV1ptCmFXeHBZWFJwYjI0aU9pSnZjbWN4TG1SbGNHRnlkRzFsYm5ReElpd2lhR1l1Ulc1eWIyeHNiV1Z1ZEVsRUlqb2kKWVdSdGFXNHhjQ0lzSW1obUxsUjVjR1VpT2lKMWMyVnlJbjE5TUFvR0NDcUdTTTQ5QkFNQ0EwY0FNRVFDSURGeApDYzE1bDZUZ1dqYnhSZzlmNjczOGV0K0NZZ1I3VVpGR200VHdoQk5jQWlBNmtUMFFwbDV6WnBUN3BzM0dySXlVCmEydDRHSTQ5ZTdjUm5PMmdrSzl6Z3c9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="
            ]
        },
        "tls": {
            "cahost": "9.30.20.70",
            "caport": "32129",
            "caname": "tlsca",
            "catls": {
                "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVTmlUbkdTandSdU1JVXhpWGcwMGZZWXhPSndJd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVJrd0Z3WURWUVFERXhCbVlXSnlhV010ClkyRXRjMlZ5ZG1WeU1CNFhEVEU0TVRFeE5qRTJNVEF3TUZvWERUTXpNVEV4TWpFMk1UQXdNRm93YURFTE1Ba0cKQTFVRUJoTUNWVk14RnpBVkJnTlZCQWdURGs1dmNuUm9JRU5oY205c2FXNWhNUlF3RWdZRFZRUUtFd3RJZVhCbApXhsWkdkbGNqRVBNQTBHQTFVRUN4TUdSbUZpY21sak1Sa3dGd1lEVlFRREV4Qm1ZV0p5YVdNdFkyRXRjMlZ5CmRtVnlNRmt3RXdZSEtvWkl6ajBDQVFZSUtvWkl6ajBEQVFjRFFnQUU1dlBucDJUNTdkY2hTOGRLNExsMFJRZEEKd284RmJsMzBPcnBGdWFHUld5TFl4eGcxcVFTemhUY3hTcGtHZjh3a1FzVDVFb01lSWcrRytldjBOU01FUTZORgpNRU13RGdZRFZSMFBBUUgvQkFRREFnRUdNQklHQTFVZEV3RUIvd1FJTUFZQkFmOENBUUV3SFFZRFZSME9CQllFCkZMd2d1N0J3Uk9lQ2hzV2hWQWptMTdmalh1eVBNQW9HQ0NxR1NNNDlCQU1DQTBjQU1FUUNJR0FCRmNSdXhtSkIKY3c4OTJJOXhPS3YxVmYyT0JHZUh5N2pFQzRBRm5najFBaUJqdHFvdjBXMXdxZjhwcGttYkxIQkJoWW1vS3ZqRwo4bDQyeVQ5bWYxWVQrZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K"
            },
            "enrollid": "peertls",
            "enrollsecret": "peertlspw",
            "csr": {
                "hosts": [
                    "9.30.20.70",
                    "chandra48peer1"
                ]
            }
        }
    }
}
```
{:codeblock}

É possível deixar os outros campos em branco. Salve esse arquivo e você precisará utilizá-lo ao implementar um [solicitador](/docs/services/blockchain/howto/orderer_deploy_icp.html) ou [peer](/docs/services/blockchain/howto/peer_deploy_icp.html).

## Fornecedores de serviço de associação (MSPs)
{: #msp}

Os componentes do {{site.data.keyword.blockchainfull_notm}} Platform consomem identidades via Membership Service Providers (MSPs). Os MSPs associam os certificados que as autoridades de certificação emitem com permissões e funções. Para obter mais informações sobre MSPs, consulte [o tópico Associação na documentação do Hyperledger Fabric ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "o tópico Associação na documentação do Hyperledger Fabric").

As pastas MSP devem ter uma estrutura definida para ser usada pelos componentes do Fabric. O cliente da CA do Fabric estabelece essa estrutura criando as pastas MSP a seguir:

- **cacerts:** contém o certificado da CA raiz de sua rede.
- **intermediatecerts:** esses são os certificados de quaisquer autoridades de certificação intermediárias em sua cadeia de confiança (levando de volta a uma autoridade de certificação raiz ou autoridades de certificação). Cada organização do Plano corporativo tem duas autoridades de certificação intermediárias para failover e alta disponibilidade.
- **signCerts:** essa pasta contém seu certificado de assinatura pública, também conhecido como seu signCert ou certificado de inscrição. Esse certificado é anexado às suas chamadas à rede (uma chamada de chaincode, por exemplo) quando você referencia o seu diretório do MSP por meio da linha de comandos ou constrói um objeto de contexto do usuário com os SDKs. É possível fazer upload desse certificado para o IBP se desejar operar uma rede a partir do SDK ou da linha de comandos.
- **keystore:** essa pasta contém sua chave privada. Essa chave é usada para assinar chamadas para a rede quando você referencia seu diretório MSP a partir da linha de comandos ou constrói um objeto de contexto do usuário com os SDKs, mas ela não é anexada às chamadas da maneira como o signCert é. É **crucial** que essa chave seja mantida segura. Se ela ficar comprometida, poderá ser usada para personificar sua identidade.

Também é possível construir uma pasta MSP que o fabric-ca-client pode referenciar usando o Monitor de Rede e as APIs do Swagger.

- **cacerts** e **intermediatecerts**: é possível buscar esses certificados com as [APIs do Swagger](/docs/services/blockchain/howto/swagger_apis.html) emitindo uma solicitação `Get` para a API do MSP.
- **signCerts** e **keystore**: é possível gerar esses certificados clicando no botão **Gerar certificados** no painel "Autoridade de Certificação". Uma janela pop-up é aberta com dois certificados listados. Copie e armazene o **Certificado** em signCerts e a **Chave Privada** no keystore. Mantenha esses certificados em um local seguro porque eles não ficam armazenados na plataforma.

Muitos componentes do Fabric contêm informações adicionais dentro de sua pasta MSP. Por exemplo, se você operar um peer remoto, poderá ver as pastas a seguir:

- **admincerts:** essa pasta contém a lista de administradores para essa organização ou esse componente. Será necessário fazer upload de seu signCert para essa pasta se você estiver operando um peer remoto por meio da linha de comandos ou dos SDKs. Se você usar o cliente Fabric CA, também precisará de uma pasta admincerts em seu MSP que contenha o signCert correspondente a ser reconhecido como certificados do administrador.
- **tls:** essa é uma pasta na qual você armazena os certificados TLS usados para comunicação com outros componentes de rede.

Para obter mais informações sobre a estrutura de MSPs, consulte [Associação ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Associação") e [Membership Service Providers ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html "Membership Service Providers") na documentação do Hyperledger Fabric.


## Visualizando os logs de CA
{: #ca-operate-view-logs}

Os logs de componentes podem ser visualizados por meio da linha de comandos, usando os [`kubectl CLI commands`](#ca-kubectl-configure) ou por meio do [Kibana ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.elastic.co/products/kibana "Sua janela no Elastic Search"), que está incluído em seu cluster do ICP.

- Use o comando `kubectl logs` para visualizar os logs do contêiner dentro do pod. Se você não tiver certeza de seu nome do pod, execute o comando a seguir para visualizar sua lista de pods.

   ```
   kubectl get pods
   ```
   {:codeblock}

   Em seguida, execute o comando a seguir para recuperar os logs para o contêiner de CA que reside dentro do pod, substituindo `pod_name` pelo nome de seu pod a partir da saída de comando acima:

   ```
   kubectl logs <pod_name> -c ca
   ```
   {:codeblock}

   Para obter mais informações sobre o comando `kubectl logs`, veja [Documentação do Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- Como alternativa, é possível acessar eventos de implementação e logs usando o [console de gerenciamento de cluster do ICP](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html), que abre os logs no Kibana.

  **Nota:** ao visualizar seus logs no Kibana, você pode receber a resposta `No results found`. Essa condição poderá ocorrer se o ICP usar o endereço IP do nó do trabalhador como seu nome do host. Para resolver esse problema, remova o filtro que inicia com `node.hostname.keyword` na parte superior do painel e os logs se tornarão visíveis.

## Detecção de problemas
{: #ca-operate-troubleshooting}

### **Problema:** erro ao executar o comando `enroll`
{: #ca-enroll-error}

Ao executar o comando de inscrição do cliente Fabric CA, é possível que o comando falhe com o erro a seguir:

```
Error: Failed to read config file at '/Users/chandra/fabric-ca-client/ca-admin/fabric-ca-client-config.yaml': While parsing config: yaml: line 42: mapping values are not allowed in this context
```
{:codeblock}

**Solução:**

Esse erro pode ocorrer quando o cliente Fabric CA tenta se inscrever, mas não pode se conectar à sua CA. Isso poderá acontecer se:   

- Seu comando `enroll` contém um `https://` extra no parâmetro `-u`.
- O nome da autoridade de certificação está incorreto.
- O nome do usuário ou senha está incorreto.

Revise os parâmetros especificados em seu comando `enroll` e assegure que nenhuma dessas condições existam.
