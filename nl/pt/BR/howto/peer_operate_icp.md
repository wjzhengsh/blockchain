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
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Operando peers no {{site.data.keyword.cloud_notm}} Private com uma rede multinuvem
{: #icp-peer-operate}

Depois de configurar um {{site.data.keyword.blockchainfull}} Platform no peer do {{site.data.keyword.cloud_notm}} Private, é necessário concluir várias etapas operacionais antes que seu peer possa emitir transações para consultar e chamar o livro-razão da rede de blockchain. As etapas envolvem incluir sua organização em um canal, associar seu peer ao canal, instalar o chaincode em seu peer, instanciar o chaincode no canal e conectar aplicativos a seu peer. Se você deseja conectar seu peer a uma rede do Starter Plan ou do Enterprise Plan, veja [Operando peers no {{site.data.keyword.cloud_notm}} Private com o Starter Plan ou o Enterprise Plan](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate).
{:shortdesc}

É necessário concluir algumas etapas de pré-requisito em seu cluster do {{site.data.keyword.cloud_notm}} Private para operar seu peer.

** Pré-requisitos: **
- [Configurar suas CLIs](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-kubectl-configure)
- [Recuperar suas informações sobre terminais de peer](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-peer-endpoint)
- [Fazer download de seu certificado TLS de peer](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-tls-cert)

Em seguida, é possível usar um dos métodos a seguir para operar seu peer.

** Caminhos de Operações: **
- [Utilizando os SDKs do Fabric](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-with-sdk)
- [Utilizando a Linha de Comandos](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-cli-operate)

Os SDKs do Fabric são o caminho recomendado, embora as instruções assumam familiaridade com a operação do SDK. Se você preferir usar a linha de comandos, poderá usar os binários de peer do Fabric.

Se sua organização ainda não for um membro de um consórcio ou canal, será possível usar essas etapas para [criar um canal](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-create-channel). As instruções orientarão você como [preparar uma definição de organização](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition). Essa definição será usada para torná-lo um membro do consórcio ao ser incluído em um canal do sistema do solicitador. Posteriormente, será possível formar um novo canal [criando uma transação de canal](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-channeltx)
<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](../v10_application.html#ha-app).
-->

## Pré-requisitos
{: #icp-peer-operate-prerequisites}

Se você planeja usar os SDKs ou a linha de comandos, é necessário concluir as etapas nesta seção no curso de operação de seu peer. É possível concluir todas essas etapas antes de iniciar.

### Configurando as CLIs
{: #icp-peer-operate-kubectl-configure}

É necessário usar a ferramenta de linha de comandos **kubectl** para se conectar ao contêiner de peer executado no {{site.data.keyword.cloud_notm}} Private.

1. Efetue login na IU do cluster do {{site.data.keyword.cloud_notm}} Private. Navegue para a guia **Ferramentas de linha de comandos** e clique em **CLI do Cloud Private**. Você verá as ferramentas a seguir que podem ser transferidas por download.

   * Instalar a CLI e os plug-ins do IBM Cloud Private
   * Instalar a CLI Kubectl
   * Instalar o Helm
   * Instalar a CLI do Istio

  Para operar um peer, é necessário usar as **três** primeiras ferramentas, entre as quais o Helm é opcional. Clique em cada um deles e execute os comandos `curl` para o tipo de máquina que você está usando. Em seguida, emita os comandos `chmod` e `sudo mv` para cada ferramenta. O comando `chmod` mudará a permissão da CLI em questão para torná-la executável e o comando `sudo mv` moverá o arquivo e o renomeará.

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

  Se bem-sucedido, o comando retornará uma resposta semelhante ao exemplo a seguir, em que `peer-6cf85f6687-hcxpl` é o nome do contêiner de peer.

  ```
  NAME READY STATUS RESTARTS AGE peer-6cf85f6687-hcxpl 2/2 Running 0 6h
  ```

  Agora você está pronto para usar a ferramenta **kubectl** para obter as informações sobre terminais de peer.

3. Opcionalmente, se você desejar usar **Helm**, conclua algumas etapas adicionais. Observe que o Helm é opcional para ser instalado e você não precisa usá-lo nestas instruções. No entanto, pode ser útil gerenciar suas liberações do Helm e criar seus próprios archives para implementação no {{site.data.keyword.cloud_notm}} Private.

  1. Clique em "Instalar Helm" e execute o comando `curl` por meio da IU do {{site.data.keyword.cloud_notm}} Private.
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

### Recuperando informações sobre terminais de peer
{: #icp-peer-operate-peer-endpoint}

É necessário destinar seu terminal de peer por meio do SDK ou do cliente Fabric CA para se associar ao canal ou instalar contratos inteligentes. É possível localizar o terminal de seu peer usando a IU do console do {{site.data.keyword.cloud_notm}} Private. Você precisará ser um [Administrador de cluster ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Funções e ações do administrador de cluster") para concluir as etapas a seguir:

1. Efetue login no console do {{site.data.keyword.cloud_notm}} Private e clique no ícone **Menu** no canto superior esquerdo.
2. Clique em **Carga de trabalho** > **Liberações do Helm**.
3. Localize o nome de sua Liberação do Helm e abra o painel de detalhes da Liberação do Helm.
4. Role para baixo até a seção **Notas** na parte inferior do painel. Na seção **Notas**, é possível ver um conjunto de comandos para ajudá-lo a operar sua implementação de peer.
5. Siga as etapas para configurar a [CLI kubectl](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-kubectl-configure) caso ainda não tenha feito isso. Você usará o kubectl para interagir com seu contêiner de peer.
6. Em sua CLI, execute o primeiro comando na nota, que segue **1. Obtenha a URL do aplicativo executando estes comandos:**. Esse comando imprimirá a URL e a porta do peer, que são semelhantes ao exemplo a seguir. Salve essa URL para os comandos futuros.

  ```
  http://9.30.94.174:30159
  ```
  {:codeblock}

Se você implementar seu peer atrás de um firewall, será necessário abrir um intermediário para permitir que a rede na plataforma conclua um handshake TLS com seu peer. É necessário somente ativar um intermediário para a porta do Node ligada à porta 7051 de seu peer. Para obter mais informações, consulte [localizando suas informações de terminal de peer](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint).
{:note}

### Fazer download de seu certificado TLS de peer
{: #icp-peer-operate-tls-cert}

É necessário fazer download do certificado TLS de peer e passá-lo para seus comandos para se comunicar com seu peer por meio de um cliente remoto.

1. Efetue login no console do {{site.data.keyword.cloud_notm}} Private e clique no ícone **Menu** no canto superior esquerdo.
2. Clique em **Carga de trabalho** > **Liberações do Helm**.
3. Localize o nome de sua Liberação do Helm e abra o painel de detalhes da Liberação do Helm.
4. Role para baixo até a seção **Notas** na parte inferior do painel. Na seção **Notas**, é possível ver um conjunto de comandos para ajudá-lo a operar sua implementação de peer.
5. Siga as etapas para configurar a [CLI kubectl](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-kubectl-configure) se ainda não tiver feito isso. Você usará o kubectl para interagir com seu contêiner de peer.
6. Em sua CLI, execute o primeiro comando na nota, que segue **3. Obtenha o arquivo de certificado raiz TLS de peer**. Esse comando salvará seu certificado TLS como o arquivo `cacert.pem` em sua máquina local.
7. Mova o certificado para o local onde é possível referenciá-lo em comandos futuros e renomeie-o como `peertls.pem`.

  ```
  mkdir $HOME/fabric-ca-client/peer-tls cp cacert.pem $HOME/fabric-ca-client/peer-tls/peertls.pem
  ```
  {:codeblock}

## Usando SDKs do Fabric para operar o peer
{: #icp-peer-operate-with-sdk}

Os SDKs do Hyperledger Fabric fornecem um conjunto poderoso de APIs que permitem que os aplicativos interajam e operem redes de blockchain. É possível localizar a lista mais recente de idiomas suportados e a lista completa de APIs disponíveis nos SDKs do Fabric na [documentação da comunidade do Hyperledger Fabric SDK ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "documentação da comunidade do Hyperledger Fabric SDK"). É possível usar os SDKs do Fabric para associar seu peer a um canal no {{site.data.keyword.blockchainfull_notm}} Platform, instalar um chaincode em seu peer e instanciar o chaincode em um canal.

As instruções a seguir usam o [SDK do Fabric Node ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/ "SDK do Fabric Node") para operar o peer e presumir familiaridade anterior com o SDK. É possível usar o [tutorial de desenvolvimento de aplicativos](/docs/services/blockchain/v10_application.html#dev-app) para aprender a usar o SDK do Node antes de iniciar e como um guia para desenvolver aplicativos com seu peer quando estiver pronto para chamar e consultar o chaincode.

### Instalando o SDK do Nó
{: #icp-peer-operate-install-sdk}

É possível usar o NPM para instalar o [Node SDK ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/ "Node SDK"):

```
npm install fabric-client@1.2
```
{:codeblock}

É recomendado que você use a versão 1.2 do SDK do Node.

### Preparando o SDK para trabalhar com o peer
{: #icp-peer-operate-node-sdk}

Seu peer foi implementado com o signCert de seu administrador de peer interno. Isso permitirá que você use os certificados do administrador de peer e a pasta MSP para operar o peer.

Localize os certificados que você criou quando [inscreveu seu administrador de peer](/docs/services/blockchain/howto/CA_operate.html#ca-operate-enroll-admin). Se você usou os comandos de exemplo, é possível localizar sua pasta MSP do administrador de peer em `$HOME/fabric-ca-client/peer-admin`.
  - É possível construir o contexto do usuário administrador de peer com o SDK usando o signCert (chave pública) e a chave privada na pasta MSP. É possível localizar essas chaves nos locais a seguir:
    - O signCert pode ser localizado na pasta **signcerts**: `$HOME/fabric-ca-client/peer-admin/msp/signcerts`
    - A chave privada pode ser localizada na pasta **keystore:**: ` $HOME/fabric-ca-client/peer-admin/msp/keystore`

<!-- You can also use the SDK to generate the peer admin signCert and private key using the endpoint information of CA on Starter Plan or Enterprise Plan and your [peer admin username and password](/docs/services/blockchain/howto/CA_operate.html#ca-operate-register-admin). -->

### Passando o certificado TLS de seu peer para o SDK
{: #icp-peer-operate-download-peer-tlscert}

É necessário referenciar o certificado TLS de seu peer para autenticar a comunicação de seu SDK. Localize o certificado TLS que você [transferiu por download de seu contêiner de peer](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-tls-cert) e salve-o onde ele possa ser referenciado por seu aplicativo. Se você usou os comandos de exemplo, é possível localizar o certificado TLS de peer em `$HOME/fabric-ca-client/peer-tls/peertls.pem`. Em seguida, é possível importar o certificado TLS para seu aplicativo usando um comando de arquivo de leitura simples.

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### Fornecendo informações sobre terminais de peer para o SDK
{: #icp-peer-operate-SDK-endpoints}

Localize as [informações sobre terminais de seu peer](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-peer-endpoint) e as forneça ao SDK, declarando uma nova variável peer. O exemplo a seguir define o peer como um terminal em sua rede de malha e passa para ele o certificado TLS importado.

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30159', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

Se você tiver uma rede grande, com vários peers pertencentes a diferentes organizações que precisem endossar suas transações, talvez você deseje trabalhar com seu consórcio para [construir um perfil de conexão comum ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/tutorial-network-config.html "perfil de conexão comum")

### Transmitindo o certificado TLS de seu solicitador para o SDK
{: #icp-peer-operate-download-orderer-tlscert}

Também é necessário o certificado TLS do solicitador de seu consórcio para associar canais e enviar transações. Se você for o administrador do solicitador, siga as instruções para [fazer download do certificado TLS do solicitador](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert).  Se você usou os comandos de exemplo, é possível localizar o certificado TLS do peer em `$HOME/fabric-ca-client/orderer-tls/orderertls.pem`. Se o solicitador for controlado por outra organização, eles precisarão fornecer a você o certificado TLS em uma operação dentro e fora da banda. Em seguida, é possível importar o certificado TLS para seu aplicativo.

```
var ordererTLSCert = fs.readFileSync(path.join(__dirname, './orderertls.pem'));
```
{:codeblock}

### Fornecendo informações do solicitador para o SDK
{: #icp-peer-operate-orderer-SDK-endpoints}

Para usar o SDK, também são necessárias as informações sobre terminais dos solicitadores em seu consórcio. Se você for o administrador do solicitador, será possível usar essas instruções para [Recuperar as informações sobre terminais do solicitador](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint). Se o solicitador for governado por outra organização, eles precisarão fornecer a você a URL do solicitador na operação dentro e fora da banda. O exemplo a seguir define um solicitador como um terminal e o transmite como um certificado TLS do solicitador.

```
var orderer = fabric_client.newOrderer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(ordererTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}
<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

### Usando o SDK para se associar a um canal
{: #icp-peer-operate-join-channel-sdk}

Sua organização precisa ser um membro de um canal antes que seja possível associar o canal ao seu peer. Se você não for membro de um canal, será possível seguir as instruções para [criar um novo canal](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-create-channel).

Depois que sua organização se tornar um membro de um canal, siga as instruções para [associar seus peers a um canal](/docs/services/blockchain/v10_application.html#dev-app-join-channel-sdk) usando o SDK.

### Usando o SDK para instalar o chaincode no peer
{: #icp-peer-operate-install-cc-sdk}

Use as instruções a seguir para usar o SDK para [instalar um chaincode](/docs/services/blockchain/v10_application.html#dev-app-install-cc-sdk) em seu peer.

### Usando o SDK para instanciar o chaincode no canal
{: #icp-peer-operate-instantiate-cc-sdk}

Apenas um membro do canal precisa instanciar ou atualizar o chaincode. Portanto, qualquer membro do canal que tenha instalado o chaincode em seus peers poderá instanciar o chaincode e especificar políticas de endosso. No entanto, se você desejar usar o peer para instanciar o chaincode em um canal, será possível usar o SDK e seguir as instruções para [instanciar um chaincode](/docs/services/blockchain/v10_application.html#dev-app-instantiate-cc-sdk).

## Usando a CLI para operar o peer
{: #icp-peer-operate-cli-operate}

Também é possível operar seu peer a partir da linha de comandos usando os binários do Fabric Peer.

Seu peer foi implementado com o signCert de seu administrador de peer dentro, permitindo que essa identidade opere o peer. As instruções a seguir usarão a pasta MSP do administrador de peer que foi gerada quando você [implementou seu peer](/docs/services/blockchain/howto/CA_operate.html#ca-operate-register-admin) para se associar ao peer em um canal, instalar um chaincode no peer e, em seguida, instanciar o chaincode em um canal.

### Fazendo download do cliente de peer Fabric
{: #icp-peer-operate-fabric-client}

Para interagir com seu peer a partir de um cliente remoto, é necessário fazer o download do [Cliente do peer do Fabric ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peercommand.html "Comando de peer do Fabric").

A maneira mais fácil de obter o cliente peer é fazer download dos binários da ferramenta do Fabric a partir do Projeto do Hyperledger. Crie um diretório no qual gostaria de fazer download dos binários com sua linha de comandos e busque-os emitindo o comando abaixo. Primeiro, é necessário instalar o [Curl ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl").

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

O comando instalará os binários em um diretório `bin/`. Configure o caminho para suas ferramentas, em que as ferramentas Fabric acima são transferidas por download:
```
export PATH=$PATH:<full_path_to_bin_folder>
```
{:codeblock}

Por exemplo, se instalasse os binários em seu diretório inicial, você configuraria seu `PATH` como:

```
export PATH=$PATH:$HOME/bin
```
{:codeblock}

Fazer download dos binários criará uma pasta de configuração contendo um arquivo core.yaml, orderer.yaml e configtx.yaml. Configure o caminho de configuração do Fabric para este diretório de configuração:

```
export FABRIC_CFG_PATH=<full_path_to_config_folder>
```
{:codeblock}

Por exemplo:
```
export FABRIC_CFG_PATH=$HOME/config
```
{:codeblock}

### Gerenciando os certificados no sistema local
{: #icp-peer-operate-manage-certs}

Alterne o diretório no qual a pasta MSP do administrador de peer é gerada. Se você seguiu as etapas de exemplo nesta documentação, será possível localizar a pasta MSP em um diretório conforme a seguir:

```
cd $HOME/fabric-ca-client/peer-admin/msp
```
{:codeblock}

Antes de poder operar o peer, é necessário executar algum gerenciamento dos certificados em sua máquina local. Também é necessário assegurar que seja possível acessar os certificados TLS a partir do peer. Para obter mais informações sobre os certificados a serem usados, consulte [Membership Service Providers](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp) em [Operando uma autoridade de certificação no {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_operate.html#ca-operate).

1. Mova o signCert do administrador de peer para uma nova pasta denominada `admincerts`:

    ```
    cd $HOME/fabric-ca-client/peer-admin/msp mkdir admincerts cp signcerts/cert.pem admincerts/cert.pem
    ```
    {:codeblock}

2. Assegure-se de que você [faça download de seu certificado TLS de peer](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-tls-cert) e possa referenciá-lo a partir de sua linha de comandos. Se você seguiu os comandos de exemplo nesta documentação, será possível localizar esse certificado TLS no arquivo `$HOME/fabric-ca-client/peer-tls/peertls.pem`.

3. Também é necessário referenciar o certificado TLS de seu solicitador. Se você for o administrador do solicitador, siga as instruções para [fazer download do certificado TLS do solicitador](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert). Se o solicitador for controlado por outra organização, eles precisarão fornecer a você o certificado TLS em uma operação dentro e fora da banda. Salve esse certificado em um local no qual seja possível referenciá-lo em comandos futuros.

É possível executar um comando de árvore para verificar se você concluiu essas etapas. Navegue para o diretório no qual você armazenou seus certificados. Um comando de árvore deve gerar um resultado semelhante à estrutura a seguir:
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
├── orderer-tls
│ └── orderertls.pem
├── peer-admin
│   └── msp
│ ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── 24f76217c5c7e641ee29b068712181294e427cbee4dbfce230a1a98b53489cbe_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
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

### Configurando variáveis de ambiente da CLI
{: #icp-peer-operate-environment-variables}

Depois de mover todos os nossos certificados para o local necessário, precisamos configurar algumas variáveis de ambiente que são usadas por nossos comandos da CLI de peer. Em seguida, estaremos prontos para usar o cliente peer do Fabric para operar nosso peer.

1. Certifique-se de ter instalado e configurado [suas CLIs](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-kubectl-configure).

2. Configure o caminho de configuração do Fabric para esse diretório de configuração que foi criado quando você [transferiu por download os binários da ferramenta do Fabric](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-fabric-client):

    ```
    export FABRIC_CFG_PATH=<full_path_to_config_folder>
    ```
    {:codeblock}

    A configuração dessa variável permitirá que você execute os comandos usando o cliente peer em qualquer diretório.

3. São necessárias as informações sobre terminais de seu solicitador. Se você for o administrador do solicitador, será possível usar essas instruções para [Recuperar as informações sobre terminais do solicitador](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint). Se o solicitador for governado por outra organização, eles precisarão fornecer a você a URL do solicitador na operação dentro e fora da banda.

4. [Recupere suas informações sobre terminais de peer](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-peer-endpoint). Usaremos essa URL para configurar a variável de ambiente `PEERADDR`. É necessário deixar o `http://` no início.

5. Execute os comandos a seguir para configurar as variáveis de ambiente:

  ```
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  Substitua os campos por suas próprias informações.
    - Substitua `<CHANNEL_NAME>` pelo nome do canal associado ao peer.
    - Substitua `<CC_NAME>` por qualquer nome que se refira ao chaincode.
    - Substitua `<PEERADDR>` pelo nome do host do terminal de peer e a porta da etapa anterior.
    - Substitua `<ORDERER_URL>` pelo nome do host e a porta de seu solicitador de consórcio.
    - Substitua `<PATH_TO_ADMIN_MSP>` com o caminho para a pasta MSP de seu administrador de peer.
    - Substitua `<MSPID_OF_PEER_BEING_USED>` com o MSPID da organização do peer que você está usando para buscar um bloco genesis ou se associar a um canal ou instalar o chaincode. Esse valor foi fornecido durante a implementação do gráfico Helm.

  Por exemplo:

  ```
  export CHANNEL=mychannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.250.70:32110
  export ORDERER_1=9.30.250.70:31507
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_LOCALMSPID=org1
  ```
  {:codeblock}

  Você também desejará ativar o TLS, o que pode ser feito emitindo:

  ```
  export CORE_PEER_TLS_ENABLED=true
  ```
  {:codeblock}


### Usando a CLI para se associar ao peer no canal
{: #icp-peer-operate-cli-join-channel}

Sua organização precisa ser um membro de um canal antes que seja possível associar o canal ao seu peer. Se você não for membro de um canal, será possível seguir as instruções para [criar um novo canal](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-create-channel).

1. Certifique-se de que você tenha configurado as [variáveis de ambiente em sua CLI](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-environment-variables).

2. Busque o bloco genesis do canal para construir o livro-razão no peer. Configure `CORE_PEER_TLS_ROOTCERT_FILE` com o caminho do certificado TLS do solicitador e execute o comando a seguir:

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --tls --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  Se isso for bem-sucedido, você verá resultados semelhantes a:
  ```
  2018-11-27 17:00:49.387 EST [cli/common] readBlock -> INFO 041 Received block: 0
  ```

  **Nota:** é possível que ao executar qualquer um desses comandos da CLI, você possa obter o aviso a seguir que pode ser seguramente ignorado.

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```
  {:codeblock}

  Execute o comando a seguir para verificar se o bloco genesis foi incluído no contêiner de peer com sucesso:

  ```
  ls *.block
  ```
  {:codeblock}

  Você sabe que o bloco genesis foi incluído com sucesso ao ver algo semelhante ao exemplo a seguir:

  ```
  defaultchannel_0.block
  ```

3. Depois de ter buscado o bloco genesis do canal, é possível associar o peer ao canal.  Configure `CORE_PEER_TLS_ROOTCERT_FILE` para o caminho de seu certificado TLS do peer e associe o canal usando o comando a seguir:

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  peer channel join -b ${CHANNEL}_0.block --tls
  ```
  {:codeblock}

  Quando isso funciona com sucesso, são exibidas informações semelhantes ao exemplo a seguir:

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Usando a CLI para instalar o chaincode no peer
{: #icp-peer-operate-toolcontainer-install-cc}

Agora, estamos prontos para instalar e instanciar o chaincode no peer. Para essas instruções, instalaremos o chaincode `fabcar` por meio do repositório `fabric-samples`. Assegure-se de que você tenha [configurado seu GOPATH ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/dev-setup/devenv.html?highlight=gopath#set-your-gopath "Configurar seu GOPATH") antes e, em seguida, faça download do chaincode `fabric-samples` do github usando os comandos a seguir:

  ```
  cd $GOPATH/src
  git clone https://github.com/hyperledger/fabric-samples
  ```
  {:codeblock}

Execute o comando da CLI de Peer a seguir para instalar o chaincode `fabcar` no peer. Configure `CORE_PEER_TLS_ROOTCERT_FILE` para o caminho de seu certificado TLS de peer.

  ```
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  Quando esse comando for concluído com êxito, você verá algo semelhante a:

  ```
  2018-11-27 17:05:54.062 EST [chaincodeCmd] install -> INFO 050 Installed remotely response:<status:200 payload:"OK"
  ```

### Usando a CLI para instanciar o chaincode em um canal
{: #icp-peer-operate-toolcontainer-instantiate-cc}

Como somente um peer tem que instanciar o chaincode em um canal, você não tem que usar o seu peer para instanciar o chaincode. Os peers hospedados no {{site.data.keyword.blockchainfull_notm}} Platform podem fazer isso. No entanto, se você desejar que o peer instancie o chaincode no canal, será possível fazer isso executando o comando abaixo. Configure o valor de `CORE_PEER_TLS_ROOTCERT_FILE` para o caminho de seu certificado TLS de peer. Configure o valor de `--cafile` com o certificado TLS do solicitador.

```
cd $HOME/fabric-ca-client
export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $PWD/orderer-tls/orderertls.pem -P ""
```
{:codeblock}

Quando esse comando for concluído com êxito, você verá algo semelhante a:

```
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 048 Using default escc
2018-11-27 17:09:28.013 EST [chaincodeCmd] checkChaincodeCmdParams -> INFO 049 Using default vscc
```

Após o chaincode ter sido instanciado, é possível usar a consulta de chaincode e chamar comandos para ler e gravar dados no livro-razão do canal. Para obter mais informações, veja os comandos [peer chaincode ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) na documentação do Hyperledger Fabric. Será necessário transmitir o terminal do solicitador para seus comandos de chamada usando o IP do proxy e a porta externa do solicitador. É necessário somente passar o terminal de peer para um comando de consulta.

## Visualizando logs de peer no {{site.data.keyword.cloud_notm}} Private
{: #peer-log-icp}

É possível acessar os logs de peer por meio do console do {{site.data.keyword.cloud_notm}} Private e verificar os logs na interface do Kibana.

1. Efetue login no console do {{site.data.keyword.cloud_notm}} Private e clique no ícone **Menu** no canto superior esquerdo.
2. Na lista de menu, clique em **Cargas de trabalho** > **Liberações de Helm**.
3. Na tabela Liberações de Helm, clique no nome de sua liberação de helm.
4. Role para baixo até a seção **Pod** e clique no link **Visualizar logs** no final da linha da tabela. Os logs do peer são abertos na interface do Kibana.

## Atualizando o chaincode

Com o tempo, é provável que você precise modificar o chaincode que está em execução no peer. Como o chaincode é instalado nos peers e instanciado no canal, é necessário atualizar o chaincode em todos os peers no canal.

Conclua as etapas a seguir para atualizar seu chaincode:

1. Para atualizar o chaincode em cada peer, simplesmente execute novamente o processo usado para instalar o chaincode nos peers, usando um aplicativo cliente ou um comando da CLI. Certifique-se de especificar o mesmo nome de chaincode usado originalmente. No entanto, desta vez, incremente a `Version` do chaincode. Todos os peers precisam usar o mesmo nome e versão do chaincode.

2. Após instalar o novo chaincode em todos os peers no canal, use o
comando [peer chaincode upgrade ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) para atualizar o canal para usar o novo chaincode.

## Reiniciando um peer que é executado no {{site.data.keyword.cloud_notm}} Private
{: #peer-restart}

Sempre que um certificado é incluído no peer, o nó do peer precisa ser reiniciado para que ele reconheça o novo certificado.

É possível usar os comandos **kubectl** para reiniciar o peer que é executado no {{site.data.keyword.cloud_notm}} Private. O comando requer o nome do **pod** do peer e seu **contêiner** como parâmetros de comando. Para obter mais informações sobre como usar os comandos kubectl, consulte [Configurando a CLI de kubectl](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-kubectl-configure).

1. No console do {{site.data.keyword.cloud_notm}} Private, localize o nome do **pod** e o nome do **contêiner** para a implementação do peer.

  1. Clique no ícone de menu no canto superior esquerdo do console e, em seguida, clique em **Cargas de trabalho > Implementações**.
  2. Localize e clique em sua liberação de peer na tabela para abri-la.
  3. Role para baixo para visualizar o nome **pod** associado. Anote o nome do **pod**.
  4. Clique no pod para abri-lo.
  5. Clique na guia **Contêineres**. Anote o nome do **contêiner** do peer.

2. Na linha de comandos, execute o comando a seguir para reiniciar o peer, substituindo `<PEER_POD_NAME>` e `<PEER_CONTAINER_NAME>` pelos valores acima.

  ```
  kubectl exec <PEER_POD_NAME> -c <PEER_CONTAINER_NAME> /usr/sbin/killall5
  ```
  {:codeblock}

3. É possível verificar se o peer foi reiniciado executando o comando `kubectl get pods` e examinando a saída para a contagem de **RESTART** para seu pod.

## Criando um canal
{: #icp-peer-operate-create-channel}

Se a sua organização ainda não for um membro de um consórcio ou canal, será possível usar as etapas abaixo para criar um canal. Também é possível usar essas etapas para atualizar um canal existente. As instruções orientarão você como [preparar uma definição de organização](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition). Essa definição será usada para torná-lo um [membro do consórcio](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-add-organizations-to-consortium) ao ser incluído em um canal do sistema do solicitador. Depois disso, você será capaz de formar um novo canal [criando uma transação de canal](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-channeltx).

Se você já tiver se associado ao consórcio, será necessário concluir apenas a etapa de preparação da transação do canal. Outro membro do consórcio também pode formar um novo canal com sua organização como membro. É possível atualizar a definição de organização quando precisar enviar atualizações para o consórcio, como definir novos peers de âncora, incluir novos certificados de administrador ou atualizar seu material de criptografia.

### Fazendo download da ferramenta configtxgen
{: #icp-peer-operate-configtxgen}

Se sua organização precisar associar-se a um consórcio ou canal, será necessário fazer download da ferramenta [configtxgen ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxgen.html "configtxgen").

A maneira mais fácil de obter o configtxgen é fazer download de todos os binários de ferramenta do Fabric a partir do Projeto do Hyperledger. Navegue para um diretório no qual gostaria de fazer download dos binários com sua linha de comandos e busque-os emitindo o comando abaixo. Primeiro, é necessário instalar o [Curl ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl").

```
curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
```
{:codeblock}

O comando instala o configtxgen em um diretório `bin/`. Configure o caminho `PATH` para o diretório no qual os binários da ferramenta Fabric foram transferidos por download:

```
export PATH=$PATH:<full_path_to_configtxgen>/bin
```
{:codeblock}

Por exemplo, se instalasse os binários em seu diretório inicial, você configuraria seu `PATH` como:

```
export PATH=$PATH:$HOME/bin
```

## Preparando uma definição de organização
{: #icp-peer-operate-organization-definition}

Depois de ter implementado seus componentes, sua organização poderá se associar a um consórcio preparando um arquivo de definição de organização. A definição contém todas as informações necessárias para participar do controle do consórcio, como por meio da criação ou junção de novos canais, da inclusão de seus peers em canais ou da instanciação do chaincode. A definição contém os certificados de nome da organização, MSPID e MSP. Todas as organizações precisam concluir esta etapa.

### Prepare seu material de criptografia
{: #icp-peer-operate-prepare-crypto}

Antes de preparar uma definição de organização, é necessário registrar e inscrever o [administrador de seus peers](/docs/services/blockchain/howto/CA_operate.html#ca-operate-register-admin). Navegue para o diretório no qual você criou a pasta MSP do administrador de peer. As etapas de exemplo criaram essa pasta em `$HOME/fabric-ca-client/peer-admin/msp`. É necessário executar algumas etapas adicionais dentro dessa pasta antes que o MSP possa ser usado pela ferramenta `configtxgen`.

1. Copie seu certificado TLS de sua CA e coloque em uma nova pasta denominada `tlscacerts`.

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir tlscacerts
  ```

  Em seguida, será necessário copiar o certificado para o diretório `tlscacerts` que você criou:

  ```
  cp $HOME/fabric-ca-client/tlsca-admin/cacerts/<xxxx>tlsca.pem tlscacerts/
  ```

  Seu comando pode ser semelhante ao seguinte:

  ```
  cp fabric-ca-client/tlsca-admin/cacerts/9-30-250-70-32129-tlsca.pem /tlscacerts/
  ```
  {:codeblock}

2. Mova seu signCert da pasta `signcerts` para uma nova pasta denominada `admincerts`.

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

A ferramenta `configtxgen` cria a definição, consumindo um arquivo configtx.yaml. É possível localizar uma versão de amostra desse arquivo abaixo:

```
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1
        # DefaultOrg defines the organization which is used in the sampleconfig
        # of the fabric.git development environment
        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: <path_to_peer-admin>/msp

        AnchorPeers:
            # AnchorPeers defines the location of peers which can be used
            # for cross org gossip communication.  Note, this value is only
            # encoded in the genesis block in the Application section context
            - Host: 9.30.250.70
              Port: 30481
```
{:codeblock}

Esse arquivo contém as informações que definem sua organização dentro do consórcio. Uma versão mais complexa desse arquivo também está disponível na pasta `/config` do [cliente de peer Fabric transferido por download](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-fabric-client). É possível optar por editar esse arquivo ou substituí-lo pela amostra acima. Anote o local desta pasta `/config` para configurar o valor de `FABRIC_CFG_PATH` abaixo. Edite a seção `Organizações` desse arquivo e configure os valores a seguir:

- O `Name:` pode ser qualquer nome que você deseje usar para sua organização.

- Os valores de `ID:` de seu MSPID, que se tornam o identificador exclusivo de sua organização. Esse valor de MSP da Organização foi especificado quando você implementou o gráfico Helm do peer. Ele também fica visível dentro do contêiner de peer executando os comandos a seguir, substituindo `<peer pod name>` pelo valor do pod de seu peer:

  ```
  kubectl exec -it <peer pod name> -c peer sh
  $ env | grep CORE_PEER_LOCALMSPID
  ```
  {:codeblock}

  A saída pode ser semelhante a:
  ```
  CORE_PEER_LOCALMSPID=org1
  ```

  Portanto, o `ID` do MSP é org1.

- O ` MSPDir:` é o caminho completo para seu MSP de administrador, por exemplo: `/Users/chandra/fabric-ca-client/peer-admin/msp`

- O campo `AnchorPeers:` é o peer que sua organização seleciona para ser o peer principal da comunicação entre organizações por meio do gossip. Isso é usado para recursos como dados privados ou descoberta de serviço; no entanto, os dados privados e a descoberta de serviço não são suportados atualmente pelo gráfico Helm de peer, embora o campo ainda seja necessário. Os valores de `Host` e `Port` estão disponíveis na página Liberação do Helm do peer do {{site.data.keyword.cloud_notm}} Private sob a seção Notas `#1. Get the application URL by running these commands`. A saída será semelhante a:

```
http://9.30.250.70:30481
```

Em que `Host:` = `9.30.250.70` e `Port:` = `30481`

Em seguida, use o comando a seguir para imprimir a definição da organização:

```
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -printOrg <org_name> > <path_to_org_definition>orgdefinition.json
```
{:codeblock}

Sua chamada pode ser semelhante ao comando de exemplo a seguir:

```
export FABRIC_CFG_PATH==<full_path_to_config_folder>
mkdir -p $HOME/fabric-ca-client/org-definitions
configtxgen -printOrg org1 > $HOME/fabric-ca-client/org-definitions/org1definition.json
```
{:codeblock}

Se esse comando for bem-sucedido, o `configtxgen` imprimirá a definição da organização no formato JSON. É necessário enviar este arquivo para a organização do solicitador em uma operação fora da banda para se juntar ao consórcio. A organização do solicitador pode, então, [formar um consórcio ou ser incluída a um consórcio existente](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-consortium) incluindo a definição no canal do sistema, permitindo que você crie novos canais e seja incluído nos canais por outros membros do consórcio.

## Criando a transação de canal
{: #icp-peer-operate-channeltx}

Antes de poder criar um novo canal, sua organização deve ter preparado uma [definição de organização](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition) e se tornar um membro do consórcio. Siga estas instruções se você precisar [formar um consórcio ou ser incluído em um](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-consortium). Os membros do consórcio também podem ser facilmente incluídos em novos canais, se a sua organização já tiver sido incluída no canal do sistema. As organizações que não são membros do canal do sistema podem unir-se manualmente a um canal, incluindo sua definição de organização no canal usando uma [solicitação de atualização de canal ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html). Também é possível usar essas etapas para atualizar um canal existente.

### Formando um novo canal
{: #icp-peer-operate-form-channel}

Para formar um novo canal, uma organização precisa criar uma proposta de transação de criação de canal usando a ferramenta [configtxgen ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxgen.html "configtxgen"). Essa ferramenta consome um arquivo `configtx.yaml` que define os novos membros do canal. Um arquivo de amostra `configtx.yaml` é fornecido abaixo. Uma versão mais complexa desse arquivo também está disponível na pasta `/config` do [cliente de peer Fabric transferido por download](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-fabric-client). É possível escolher editar esse arquivo ou substituí-lo por essa amostra. Anote o local desta pasta `/config` para configurar o valor de `FABRIC_CFG_PATH` abaixo.
```
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#
---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:
    - &org1

        Name: org1

        # ID to load the MSP definition as
        ID: org1

        MSPDir: /Users/chandra/fabric-ca-client/peer-admin/msp

        AnchorPeers:

            - Host: 9.30.250.70
              Port: 32110


################################################################################
#
#   SECTION: Capabilities
#
################################################################################
Capabilities:
    # Channel capabilities apply to both the orderers and the peers and must be
    # supported by both.  Set the value of the capability to true to require it.
    Global: &ChannelCapabilities

        V1_1: true

    # Orderer capabilities apply only to the orderers, and may be safely
    # manipulated without concern for upgrading peers.  Set the value of the
    # capability to true to require it.
    Orderer: &OrdererCapabilities

        V1_1: true

    # Application capabilities apply only to the peer network, and may be safely
    # manipulated without concern for upgrading orderers.  Set the value of the
    # capability to true to require it.
    Application: &ApplicationCapabilities

        V1_2: true

################################################################################
#
#   SECTION: Application
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for application related parameters
#
################################################################################

Application: &ApplicationDefaults

    # Organizations is the list of orgs which are defined as participants on
    # the application side of the network
    Organizations:

    Capabilities:
        <<: *ApplicationCapabilities


################################################################################
#
#   Profile
#
#   - Different configuration profiles may be encoded here to be specified
#   as parameters to the configtxgen tool
#
################################################################################

Profiles:

    mychannel:
        Consortium: SampleConsortium
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *org1
            Capabilities:
                <<: *ApplicationCapabilities

```
{:codeblock}

As três seções que são relevantes para criar um novo canal são **Organizações**, **Capacidades** e **Perfis**:

- **Organizações:** esta seção define todos os membros do consórcio. Cada organização possui uma âncora, tal como `&Org1`. Sob essa âncora, é possível localizar o nome da organização, MSPID, o diretório para sua pasta MSP e os peers de âncora de sua organização para gossip de peer cruzado. É possível preencher o perfil da organização para cada membro do consórcio com as etapas a seguir:
  1. Especifique o `Name` e o `ID` da organização usando seu MSPID. A organização que cria o canal precisa saber o MSPID que foi especificado quando o gráfico Helm de peer foi implementado.
  2. Em `MSPDir`, especifique o caminho completo para a pasta MSP que você usou para criar sua [definição de organização](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition). Observe que nenhum dos materiais de criptografia é usado na transação de criação de canal. A ferramenta *configtxgen* ignorará o conteúdo real do MSP. No entanto, ela espera uma pasta MSP nesse local, com a subestrutura de pasta correta.
  3. O parâmetro `AnchorPeers` é usado para identificar o peer de avanço que cada organização usa para comunicação entre organizações por meio de gossip. Especifique o nome do host e a porta do peer que agirá como o [peer âncora![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/glossary.html) para essa organização. Esse valor é importante para usar recursos como dados privados ou descoberta de serviço; no entanto, os dados privados e a descoberta de serviço não são suportados atualmente pelo gráfico Helm de peer.

- **Capacidades:** esta seção precisa estar no `configtx.yaml`, mas nenhuma mudança é necessária.

- Seção **Perfis:**. Essa seção contém as informações que são necessárias para criar um novo canal. O perfil contém as informações a seguir:
  1. O nome do novo canal.
  2. O nome do consórcio que é definido dentro do canal do sistema e será usado para criar o canal.
  3. A lista de organizações que serão incluídas no canal. Os nomes de organização listados usarão os peers âncora listados na seção `Organizations` para localizar o MSPID das organizações listadas e os peers âncora associados.

  A ferramenta *configtxgen* usa essa seção `Profiles` para criar a proposta de criação do canal.

Depois de atualizar o arquivo de configuração, gere a proposta de transação de atualização do canal usando o comando a seguir:

``` 
export FABRIC_CFG_PATH=<path_to_config_folder>
configtxgen -profile <channel_profile_name> -outputCreateChannelTx ./<channel_profile_name>.tx -channelID <channel_profile_name>
```
{:codeblock}

Um comando real seria semelhante ao exemplo a seguir:
``` 
export FABRIC_CFG_PATH=$HOME/config
configtxgen -profile mychannel -outputCreateChannelTx ./mychannel.tx -channelID mychannel
```
{:codeblock}


Se bem-sucedido, você verá um resultado semelhante à seguinte saída abaixo. O comando gravará a proposta de transação de atualização do canal em seu diretório `FABRIC_CFG_PATH`.

```
2018-11-27 16:39:36.130 EST [common/tools/configtxgen] main -> INFO 001 Loading configuration
2018-11-27 16:39:36.134 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 002 Generating new channel configtx
2018-11-27 16:39:36.134 EST [common/tools/configtxgen/encoder] NewApplicationGroup -> WARN 003 Default policy emission is deprecated, please include policy specificiations for the application group in configtx.yaml
2018-11-27 16:39:36.135 EST [common/tools/configtxgen/encoder] NewApplicationOrgGroup -> WARN 004 Default policy emission is deprecated, please include policy specificiations for the application org group org1 in configtx.yaml
2018-11-27 16:39:36.136 EST [common/tools/configtxgen] doOutputChannelCreateTx -> INFO 005 Writing new channel tx
```

Se sua política de endosso não exigir assinaturas adicionais de outros membros do consórcio, será possível submeter a proposta diretamente ao solicitador para formar o novo canal. Execute o comando a seguir a partir do mesmo local que você gerou a transação de atualização do canal para criar o canal.

```
export CORE_PEER_LOCALMSPID=<ORG_MSPID>
export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
peer channel create -o <orderer_url> --tls --cafile <orderer_tls_cert> -c <channel_profile_name> -f ./<channel_profile_name>.tx
```
{:codeblock}

Em que `<ORG_MSPID>` é o MSPID de sua organização e `<PATH_TO_ADMIN_MSP>` é o caminho para a pasta MSP do administrador de peer. Dentro do comando, `<orderer_url>` é a URL do solicitador que criará o canal e `<orderer_tls_cert>` é o caminho do certificado TLS dos solicitadores. Um comando real pode ser semelhante ao exemplo a seguir:
```
export CORE_PEER_LOCALMSPID=org1
export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
peer channel create -o 9.30.250.70:31507 --tls --cafile $PWD/orderer-tls/orderertls.pem -c mychannel -f ./mychannel.tx
```
{:codeblock}

```
2018-11-27 16:59:30.610 EST [cli/common] readBlock -> INFO 04f Received block: 0
```


## Endossando a proposta e enviando a solicitação
{: #icp-peer-operate-endorse-proposal}

Quando a nova proposta de criação do canal é assinada por organizações suficientes para atender à política de endosso do canal do sistema, é possível enviar a transação de atualização do canal para o solicitador para formar o novo canal

É possível usar as APIs do Node Fabric SDK para concluir a assinatura e o envio em uma única transação. Para obter mais informações, visite o tutorial [Como criar um canal do Hyperledger Fabric ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")]](https://fabric-sdk-node.github.io/release-1.3/tutorial-channel-create.html) na documentação do Node SDK.

## Visualizando os logs de peer
{: #icp-peer-operate-view-logs}

Os logs de componentes podem ser visualizados na linha de comandos usando os [`kubectl CLI commands`](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-kubectl-configure) ou por meio do [Kibana ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.elastic.co/products/kibana "Sua janela no Elastic Search"), que está incluído no cluster do {{site.data.keyword.cloud_notm}} Private.

- Use o comando `kubectl logs` para visualizar os logs do contêiner dentro do pod. Se você não tiver certeza de seu nome do pod, execute o comando a seguir para visualizar sua lista de pods.

  ```
  kubectl get pods
  ```
  {:codeblock}

  Depois, execute o comando a seguir para recuperar os logs para o contêiner de peer que reside dentro do pod, substituindo `<pod_name>` com o nome de seu pod da saída de comando acima:

  ```
  kubectl logs <pod_name> -c peer
  ```
  {:codeblock}

  Para obter mais informações sobre o comando `kubectl logs`, veja [Documentação do Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- Como alternativa, é possível acessar logs usando o [console de gerenciamento de cluster do {{site.data.keyword.cloud_notm}} Private ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html "Eventos e logs"), que abre os logs no Kibana.

  **Nota:** ao visualizar seus logs no Kibana, você pode receber a resposta `No results found`. Essa condição poderá ocorrer se o {{site.data.keyword.cloud_notm}} Private usar o endereço IP do nó do trabalhador como seu nome do host. Para resolver esse problema, remova o filtro que inicia com `node.hostname.keyword` na parte superior do painel e os logs se tornarão visíveis.


## Detecção de problemas
{: #icp-peer-operate-troubleshooting}

### **Problema:** erro ao efetuar login no contêiner do peer
{: #icp-peer-operate-bash-error}

Esse problema ocorre ao usar a ferramenta kubectl para execução em seu peer executando o seguinte comando:

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}

Falha e produz uma mensagem de erro semelhante ao exemplo a seguir:

```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

**Solução:**

Esse erro ocorre quando, em sistemas big endian como o IBM System Z, duas sessões de bash do Docker são abertas no mesmo contêiner do Docker. A segunda sessão bash pode não ser capaz de se conectar ao contêiner em execução. Para resolver esse problema, [reinicie o contêiner de peer](/docs/services/blockchain/howto/peer_operate_icp.html#peer-restart) e tente novamente o comando `kubectl exec` após a reinicialização no mesmo nível.
