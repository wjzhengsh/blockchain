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

# Operando peers no {{site.data.keyword.cloud_notm}} Private com Starter Plan ou Enterprise Plan
{: #ibp-peer-operate}

Depois de configurar um {{site.data.keyword.blockchainfull}} Platform no peer do {{site.data.keyword.cloud_notm}} Private, é necessário concluir várias etapas operacionais antes que seu peer possa enviar transações para uma rede do Starter Plan ou do Enterprise Plan. As etapas envolvem incluir sua organização em um canal, associar seu peer ao canal, instalar o chaincode em seu peer, instanciar o chaincode no canal e conectar aplicativos a seu peer.
{:shortdesc}

É necessário concluir algumas etapas de pré-requisito em seu Cluster do {{site.data.keyword.cloud_notm}} Private para operar seu peer.

** Pré-requisitos: **
- [Configurar suas CLIs](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure)
- [Recuperar suas informações sobre terminais de peer](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint)
- [Fazer download de seu certificado TLS de peer](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert)

Em seguida, é possível usar um dos métodos a seguir para operar seu peer.

** Caminhos de Operações: **
- [Utilizando os SDKs do Fabric](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-operate-with-sdk)
- [Utilizando a Linha de Comandos](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-cli-operate)

Os SDKs do Fabric são o caminho recomendado, embora as instruções assumam familiaridade com a operação do SDK. Se você preferir usar a linha de comandos, poderá usar o cliente do peer do Fabric.

<!--
It is recommended that you deploy at least two instances of the peer Helm chart for [high availability](peer_icp.html#high-availability). Therefore, you need to follow these operations steps once for each peer. When you are ready to invoke and query chaincode from your application, connect to both peers to ensure that your [applications are highly available](../v10_application.html#ha-app).
-->

**Nota**: um peer do {{site.data.keyword.blockchainfull_notm}} Platform não tem acesso à funcionalidade completa ou ao suporte de peers que estão hospedados no {{site.data.keyword.blockchainfull_notm}} Platform. Como resultado, não é possível usar o Monitor de rede para operar um peer no {{site.data.keyword.cloud_notm}} Private. Antes de iniciar a execução de peers, assegure-se de revisar as [considerações e limitações](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations).

## Pré-requisitos
{: #ibp-peer-operate-prerequisites}

Se você planeja usar os SDKs ou a linha de comandos, é necessário concluir as etapas nesta seção no curso de operação de seu peer. É possível concluir todas essas etapas antes de iniciar.

### Configurando as CLIs
{: #ibp-peer-operate-kubectl-configure}

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
{: #ibp-peer-operate-endpoint}

É necessário destinar seu terminal de peer por meio do SDK ou do cliente Fabric CA para se associar ao canal ou instalar contratos inteligentes. É possível localizar o terminal de seu peer usando a IU do console do {{site.data.keyword.cloud_notm}} Private. Você precisará ser um [Administrador de cluster ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Funções e ações do administrador de cluster") para concluir as etapas a seguir:

1. Efetue login no console do {{site.data.keyword.cloud_notm}} Private e clique no ícone **Menu** no canto superior esquerdo.
2. Clique em **Carga de trabalho** > **Liberações do Helm**.
3. Localize o nome de sua Liberação do Helm e abra o painel de detalhes da Liberação do Helm.
4. Role para baixo até a seção **Notas** na parte inferior do painel. Na seção **Notas**, é possível ver um conjunto de comandos para ajudá-lo a operar sua implementação de peer.
5. Siga as etapas para configurar a [CLI kubectl](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure) se ainda não tiver feito isso. Você usará o kubectl para interagir com seu contêiner de peer.
6. Em sua CLI, execute o primeiro comando na nota, que segue **1. Obtenha a URL do aplicativo executando estes comandos:**. Esse comando imprimirá a URL e a porta do peer, que são semelhantes ao exemplo a seguir. Salve essa URL para os comandos futuros.

  ```
  http://9.30.94.174:30159
  ```
  {:codeblock}

**Nota:** se você implementar seu peer atrás de um firewall, será necessário abrir um intermediário para ativar a rede na plataforma para concluir um handshake TlS com seu peer. É necessário somente ativar um intermediário para a porta do Node ligada à porta 7051 de seu peer. Para obter mais informações, consulte [localizando suas informações de terminal de peer](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint).

### Fazer download de seu certificado TLS de peer
{: #ibp-peer-operate-tls-cert}

É necessário fazer download do certificado TLS de peer e passá-lo para seus comandos para se comunicar com seu peer por meio de um cliente remoto.

1. Efetue login no console do {{site.data.keyword.cloud_notm}} Private e clique no ícone **Menu** no canto superior esquerdo.
2. Clique em **Carga de trabalho** > **Liberações do Helm**.
3. Localize o nome de sua Liberação do Helm e abra o painel de detalhes da Liberação do Helm.
4. Role para baixo até a seção **Notas** na parte inferior do painel. Na seção **Notas**, é possível ver um conjunto de comandos para ajudá-lo a operar sua implementação de peer.
5. Siga as etapas para configurar a [CLI kubectl](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure) se ainda não tiver feito isso. Você usará o kubectl para interagir com seu contêiner de peer.
6. Em sua CLI, execute o primeiro comando na nota, que segue **3. Obtenha o arquivo de certificado raiz TLS de peer**. Esse comando salvará seu certificado TLS como o arquivo `cacert.pem` em sua máquina local.
7. Mova o certificado para o local onde é possível referenciá-lo em comandos futuros e renomeie-o como `peertls.pem`.

  ```
  mkdir $HOME/fabric-ca-client/peer-tls cp cacert.pem $HOME/fabric-ca-client/peer-tls/peertls.pem
  ```
  {:codeblock}

## Usando SDKs do Fabric para operar o peer
{: #ibp-peer-operate-operate-with-sdk}

Os SDKs do Hyperledger Fabric fornecem um conjunto poderoso de APIs que permitem que os aplicativos interajam e operem redes de blockchain. É possível localizar a lista mais recente de idiomas suportados e a lista completa de APIs disponíveis nos SDKs do Fabric na [documentação da comunidade do Hyperledger Fabric SDK ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "documentação da comunidade do Hyperledger Fabric SDK"). É possível usar os SDKs do Fabric para associar seu peer a um canal no {{site.data.keyword.blockchainfull_notm}} Platform, instalar um chaincode em seu peer e instanciar o chaincode em um canal.

As instruções a seguir usam o [SDK do Fabric Node ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/ "SDK do Fabric Node") para operar o peer e presumir familiaridade anterior com o SDK. É possível usar o [tutorial de desenvolvimento de aplicativos](/docs/services/blockchain/v10_application.html#dev-app) para aprender a usar o SDK do Node antes de iniciar e como um guia para desenvolver aplicativos com seu peer quando estiver pronto para chamar e consultar o chaincode.

### Instalando o SDK do Nó
{: #ibp-peer-operate-install-sdk}

É possível usar o NPM para instalar o [Node SDK ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/ "Node SDK"):

```
npm install fabric-client@1.2
```
{:codeblock}

É recomendado que você use a versão 1.2 do SDK do Node.

### Preparando o SDK para trabalhar com o peer
{: #ibp-peer-operate-prepare-node-sdk}

Seu peer é implementado com o signCert de dentro de seu administrador de peer. Isso permite que você use os certificados do administrador de peer e a pasta MSP para operar o peer.

Localize os certificados que você criou quando [inscreveu seu administrador de peer](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin). Se você usou os comandos de exemplo, é possível localizar sua pasta MSP do administrador de peer em `$HOME/fabric-ca-client/peer-admin`.
  - É possível construir o contexto do usuário administrador de peer com o SDK usando a chave signCert (chave pública) e a chave privada na pasta MSP. É possível localizar essas chaves nos locais a seguir:
    - O signCert pode ser localizado na pasta **signcerts**: `$HOME/fabric-ca-client/peer-admin/msp/signcerts`
    - A chave privada pode ser localizada na pasta **keystore:**: `$HOME/fabric-ca-client/peer-admin/msp/keystore`
    É possível localizar um exemplo de como formar um contexto do usuário e operar o SDK usando apenas as chaves pública e privada [nesta seção do tutorial de desenvolvimento de aplicativos](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel).

Também é possível usar o SDK para gerar o signCert do administrador de peer e a chave privada usando as informações de terminal da CA no Starter Plan ou no Enterprise Plan e seu [nome de usuário e senha do administrador de peer](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin).

### Fazendo upload do signCert do administrador de peer para o Starter Plan ou o Enterprise Plan
{: #ibp-peer-operate-upload-sdk}

É necessário fazer upload do signCert do administrador de peer para a rede no {{site.data.keyword.blockchainfull_notm}} Platform para que seu aplicativo tenha permissão para operar a rede.

- É possível localizar seu signCert no diretório em que seu SDK gerou seu material de criptografia, no arquivo nomeado após seu administrador de peer. Copie o certificado dentro das aspas depois do campo `certificate`, iniciando com `-----BEGIN CERTIFICATE-----` e terminando com `-----END CERTIFICATE-----`. É possível usar a CLI para converter o certificado no formato PEM, emitindo o comando `echo -e "<CERT>" > admin.pem`. Em seguida, é possível colar o conteúdo do certificado na rede de blockchain usando o Monitor de rede. Efetue login na rede no {{site.data.keyword.blockchainfull_notm}} Platform. Na tela "Membros" do Monitor de rede, clique em **Certificados** > **Incluir certificado**. Especifique qualquer nome para o seu certificado e cole o conteúdo no campo **Certificado**. Clique em **Reiniciar** quando for perguntado se você deseja reiniciar os peers. Essa ação deve ser executada antes que sua organização se associe ao canal.

### Passando o certificado TLS de seu peer para o SDK
{: #ibp-peer-operate-download-tlscert}

É necessário referenciar o certificado TLS de seus peers para autenticar a comunicação com seu SDK. Localize o certificado TLS que você [transferiu por download de seu contêiner de peer](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert) e salve-o onde ele possa ser referenciado por seu aplicativo. Se você usou os comandos de exemplo, é possível localizar o certificado TLS de peer em `$HOME/fabric-ca-client/peer-tls/peertls.pem`. Em seguida, é possível importar o certificado TLS para seu aplicativo usando um comando de arquivo de leitura simples.

```
var peerTLSCert = fs.readFileSync(path.join(__dirname, './peertls.pem'));
```
{:codeblock}

### Fornecendo informações sobre terminais de peer para o SDK
{: #ibp-peer-operate-SDK-endpoints}

Localize as [informações de terminal de seu peer](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint) e forneça-as ao SDK, declarando uma nova variável de peer ou atualizando seu Perfil de Conexão. O exemplo a seguir define o peer como um terminal em sua rede de malha e passa para ele o certificado TLS importado.

```
var peer = fabric_client.newPeer('grpcs://9.30.94.174:30167', { pem:  Buffer.from(peerTLSCert).toString(), 'ssl-target-name-override': null});
```
{:codeblock}

<!--
You need to specify a `ssl-target-name-override` of `<something>.blockchain.com` in order for the peer to resolve the DNS request.
-->

**Nota:** como o peer está fora do {{site.data.keyword.cloud_notm}}, outras organizações na rede do {{site.data.keyword.blockchainfull_notm}} Platform não serão capazes de localizar as informações de terminal de seu peer em seu Perfil de conexão. Se outras organizações precisarem enviar ao seu peer uma transação a ser endossada, será necessário fornecer a elas a URL do peer dentro e fora da operação da banda.

### Usando o SDK para se associar a um canal
{: #ibp-peer-operate-peer-join-channel-sdk}

Sua organização precisa ser um membro de um canal antes que seja possível associar o canal ao seu peer.

  - É possível iniciar um novo canal para o peer. Se você for o inicializador de canais, será possível incluir automaticamente sua organização durante a [criação de canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel).

  - Outro membro da rede de blockchain também pode incluir sua organização em um canal existente usando uma [atualização de canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel).

    Após sua organização ser incluída em um canal, é necessário incluir o signCert de seu peer no canal para que outros membros possam verificar sua assinatura digital durante as transações. O peer faz upload de seu signCert durante a instalação, o que significa que é necessário apenas sincronizar o certificado para o canal. Na tela "Canais" do Monitor de rede, localize o canal ao qual sua organização se associa e selecione **Certificado de sincronização** na lista suspensa sob o cabeçalho **Ação**. Essa ação sincroniza os certificados em todos os peers no canal. Pode ser necessário aguardar alguns minutos para que a sincronização do canal possa ser concluída antes de emitir comandos `join channel`.

    **Nota:** você somente será capaz visualizar novos blocos sendo incluídos no canal, o chaincode sendo instanciado e outras atualizações de canal na tela "Canais" de seu Monitor de rede se o peer que estiver sendo incluído no {{site.data.keyword.cloud_notm}} Private e conectado a uma rede do Starter Plan ou Enterprise Plan fizer parte da mesma organização que um peer que foi incluído com o Monitor de rede. Isso é porque o Network Monitor reúne informações sobre a tela "Canais" de seu peer e não tem visibilidade para os peers fora do {{site.data.keyword.cloud_notm}}. Se nenhum de seus peers estiver usando o recurso Dados Privados, as informações no Monitor de Rede serão as mesmas para um peer em uma organização que para o outro.

Depois que sua organização se tornar um membro de um canal, siga as instruções para [associar seu peer a um canal](/docs/services/blockchain/v10_application.html#dev-app-join-channel-sdk) usando o SDK. É necessário fornecer a URL do serviço de ordenação e o nome do canal.

### Usando o SDK para instalar o chaincode no peer
{: #ibp-peer-operate-install-cc-sdk}

Use as instruções a seguir para usar o SDK para [instalar um chaincode](/docs/services/blockchain/v10_application.html#dev-app-install-cc-sdk) em seu peer.

### Usando o SDK para instanciar o chaincode no canal
{: #ibp-peer-operate-instantiate-cc-sdk}

Apenas um membro do canal precisa instanciar ou atualizar o chaincode. Portanto, qualquer membro de rede do canal com peers no {{site.data.keyword.blockchainfull_notm}} Platform pode usar o Monitor de rede para instanciar o chaincode e especificar políticas de endosso. No entanto, se você desejar usar o peer para instanciar o chaincode em um canal, será possível usar o SDK e seguir as instruções para [instanciar um chaincode](/docs/services/blockchain/v10_application.html#dev-app-instantiate-cc-sdk).

## Usando a CLI para operar o peer
{: #ibp-peer-operate-cli-operate}

Também é possível operar seu peer a partir da linha de comandos usando o cliente `peer` do Fabric.

Seu peer foi implementado com o signCert de seu administrador de peer dentro, permitindo que essa identidade opere o peer. As instruções a seguir usarão a pasta MSP do administrador de peer que foi gerada quando você [implementou seu peer](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin) para se associar ao peer em um canal, instalar um chaincode no peer e, em seguida, instanciar o chaincode em um canal.

### Fazendo download do cliente de peer Fabric
{: #ibp-peer-operate-download-fabric-client}

A maneira mais fácil de obter o cliente de peer é fazer download de todos os binários de ferramenta do Fabric a partir do Hyperledger. Navegue para um diretório no qual você gostaria de fazer download dos binários com sua linha de comandos e busque-os emitindo o comando abaixo. Se você não tiver instalado o [Curl ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/prereqs.html#install-curl "Curl"), será necessário fazer isso primeiro.

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

Fazer o download dos binários criará uma pasta `config` que contém um arquivo core.yaml, orderer.yaml e configtx.yaml. Configure o caminho de configuração do Fabric para este diretório `config`:

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
{: #manage-certs}

Alterne o diretório no qual a pasta MSP do administrador de peer é gerada. Se você seguiu as etapas de exemplo nesta documentação, será possível localizar a pasta MSP em um diretório conforme a seguir:

```
cd $HOME/fabric-ca-client/peer-admin/msp
```
{:codeblock}

Antes de poder operar o peer, é necessário executar algum gerenciamento dos certificados em sua máquina local. Por exemplo, será necessário fazer upload do signCert do administrador do peer para o Starter Plan ou o Enterprise Plan e assegurar que você possa acessar os certificados TLS do peer e de sua rede no {{site.data.keyword.cloud_notm}}. Para obter mais informações sobre os certificados a serem usados e as tarefas a serem executadas, consulte [Gerenciando certificados no {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates).

1. Mova o signCert do administrador de peer para uma nova pasta denominada `admincerts`:

  ```
  cd $HOME/fabric-ca-client/peer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. Faça upload desse signCert para a rede no {{site.data.keyword.cloud_notm}} para que uma CLI ou aplicativo remoto possa realizar operações de canal, como buscar o bloco genesis de canal ou instanciar o chaincode.
    1. Em sua máquina local, abra o arquivo `HOME/fabric-ca-client/peer-admin/msp/admincerts/cert.pem` e copie-o para a área de transferência.
    2. Entre no Monitor de rede de sua rede no {{site.data.keyword.blockchainfull_notm}} Platform.
    3. Clique em **Membros** no navegador esquerdo e clique na guia **Certificados**.
    4. Clique no botão  ** Incluir Certificado ** .
    5. Na janela **Incluir certificado**, forneça ao seu certificado um nome, como "peer-admin", cole no certificado que você acabou de copiar para a área de transferência e clique em **Enviar**.
    6. Será perguntado se você deseja reiniciar os peers. Clique em **Reiniciar**.
    7. Clique em **Canais** no navegador à esquerda e localize o canal ao qual o peer se associará.
    8. Clique nos três pontos sob o cabeçalho **Ações** e clique em **Sincronizar certificado**. Na janela **Sincronizar certificado**, clique em **Enviar**.

    **Nota**: é importante sincronizar o certificado de canal antes que o peer se associe ao canal ou instancie o chaincode no canal. Pode ser necessário aguardar por alguns minutos para que a sincronização do canal possa ser concluída antes de emitir o canal de junção ou instanciar comandos de chaincode.

3. Assegure-se de que você [tenha transferido por download seu certificado TLS de peer](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-tls-cert) e possa referenciá-lo a partir de sua linha de comandos. Se você seguiu os comandos de exemplo, será possível localizar esse certificado TLS no arquivo `$HOME/fabric-ca-client/peer-tls/peertls.pem`.

4. Também é necessário referenciar o certificado TLS que você usou para se comunicar com sua CA do Starter Plan ou do Enterprise Plan quando você [inscreveu seu administrador de peer](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin). Se você seguiu os comandos de exemplo nesta documentação, será possível localizar o certificado TLS no arquivo `$HOME/fabric-ca-client/tls-ibp/tls.pem`.

É possível executar um comando de árvore para verificar se você concluiu essas etapas. Navegue para o diretório no qual você armazenou seus certificados. Um comando de árvore deve gerar um resultado semelhante à estrutura a seguir:
```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│ │ └── 9-12-19-115-31873-SampleOrgCA.pem
│       ├── keystore
│ │ └── c44ec1e708f84b6d0359f58ce2c9c8a289919ba81f2cf4bb5187c4ad5a43cbb0_sk
│ └── signcerts
│ | └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── peer-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│ ├── admincerts
│ │ └── cert.pem
│       ├── cacerts
│ │ └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│ │ └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── peer-tls
│   └── peertls.pem
├── tls-ibp
│   └── tls.pem
├── tls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │ └── bd57fa20283dfc76ada83f989ee0f62ce23e98c94dbd26f6cd23202d8084e38e_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### Configurando variáveis de ambiente da CLI
{: #ibp-peer-operate-environment-variables}

Depois de mover todos os nossos certificados para o local necessário, é preciso configurar algumas variáveis de ambiente para que seus comandos usem. Em seguida, você está pronto para usar o cliente de peer do Fabric para operar seu peer.

1. Recupere as informações de configuração do Starter Plan ou do Enterprise Plan do arquivo **Perfil de conexão** que está disponível na tela **Visão geral** do Monitor de Rede. Clique em **Perfil de Conexão** e, em seguida, em **Download**.

  - Localize a URL do solicitador procurando **solicitadores**, localizado em `orderers > url`. Anote a URL que inicia com o nome da rede. A URL é semelhante ao exemplo a seguir:

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Localize o nome de sua organização procurando **organizações**. Essa deve ser a mesma organização que você usa para registrar seu peer. É possível localizar o nome de sua organização junto com seu `mspid` associado. Anote o valor de `mspid`.

2. [Localize as informações do terminal de seu peer](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-endpoint). É necessário usar o terminal de peer para configurar a variável de ambiente `PEERADDR`. Assegure-se de excluir o `http://` no início.

3. Execute os comandos a seguir para configurar as variáveis de ambiente.

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export CORE_PEER_ADDRESS=<PEERADDR>
  export ORDERER_1=<ORDERER_URL>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_PEER_TLS_CERT>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_PEER_BEING_USED>
  ```
  {:codeblock}

  Substitua os campos por suas próprias informações.
    - Substitua `<full_path_to_config_folder>` pela pasta de configuração que foi criada quando você [transferiu por download o cliente de peer](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-download-fabric-client).
    - Substitua `<CHANNEL_NAME>` pelo nome do canal associado ao peer.
    - Substitua `<CC_NAME>` por qualquer nome que se refira ao chaincode.
    - Substitua `<PEERADDR>` pelo terminal de peer da etapa anterior para o peer que você está usando atualmente.
    - Substitua `<ORDERER_URL>` pelo nome do host e porta do solicitador de seu Perfil de Conexão.
    - Substitua `<PATH_TO_ADMIN_MSP>` com o caminho para a pasta MSP de seu administrador de peer.
    - Substitua `<PATH_TO_PEER_TLS_CERT>` pelo caminho para o certificado TLS de peer que você transferiu por download.
    - Substitua `<MSPID_OF_PEER_BEING_USED>` com o MSPID da organização do peer que você está usando para buscar um bloco genesis ou se associar a um canal ou instalar o chaincode.

  Por exemplo:

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export CORE_PEER_ADDRESS=9.30.94.174:30159
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/peer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/peer-tls/peertls.pem
  export CORE_PEER_LOCALMSPID=org1
  ```
  {:codeblock}

  Você também desejará ativar o TLS, o que pode ser feito emitindo:

  ```
  export CORE_PEER_TLS_ENABLED=true
  ```
  {:codeblock}


### Usando a CLI para se associar ao peer no canal
{: #ibp-peer-operate-cli-join-channel}

Antes de poder executar os comandos da CLI para associar o peer a um canal, sua organização precisa ser incluída em um canal na rede de uma das maneiras a seguir.

  - É possível iniciar um novo canal para o peer. Como o inicializador de canais, é possível incluir automaticamente a sua organização durante a [criação do canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel).
  - Outro membro da rede de blockchain também pode incluir sua organização em um canal existente usando uma [atualização de canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel).

    Após sua organização ser incluída em um canal, é necessário incluir o signCert de seu peer no canal para que outros membros possam verificar sua assinatura digital durante as transações. O peer faz upload de seu signCert durante a instalação, de modo que você precise sincronizar apenas o certificado para o canal. Na tela "Canais" do Monitor de rede, localize o canal ao qual sua organização se associa e selecione **Certificado de sincronização** na lista suspensa sob o cabeçalho **Ação**. Essa ação sincroniza os certificados em todos os peers no canal.

    **Nota:** se o seu peer do {{site.data.keyword.cloud_notm}} Private que se conectar a uma rede do Starter Plan ou do Enterprise Plan fizer parte da mesma organização que outro peer que foi incluído com o Monitor de Rede, será possível visualizar somente novos os blocos que forem incluídos no canal, o chaincode que for instanciado e outras atualizações de canal na tela "Canais" de seu Monitor de rede. Isso é porque o Network Monitor reúne informações sobre a tela "Canais" de seu peer e não tem visibilidade para os peers fora do {{site.data.keyword.cloud_notm}}. Se nenhum de seus peers usar o recurso Dados Privados, as informações no Monitor de Rede serão as mesmas para um peer em uma organização que para o outro peer.

1. Certifique-se de que você tenha configurado as [variáveis de ambiente em sua CLI](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-environment-variables).

2. Busque o bloco genesis do canal para construir o livro-razão no peer. Observe que `$HOME/fabric-ca-client/tls-ibp/tls.pem` representa o caminho para seu certificado TLS do Starter Plan ou do Enterprise Plan. Se seu caminho for diferente, certifique-se de configurar o correto.

  ```
  peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem --tls
  ```
  {:codeblock}

  **Nota:** é possível que ao executar qualquer um desses comandos da CLI, você possa obter o aviso a seguir que pode ser seguramente ignorado.

  ```
  [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

  Execute o comando a seguir para verificar se o bloco genesis foi incluído no contêiner de peer com sucesso:

  ```
  ls *.block
  ```
  {:codeblock}

  Você sabe que o bloco genesis foi incluído com sucesso ao ver algo semelhante ao exemplo a seguir:

  ```
  defaultchannel_0.block
  ```
  {:codeblock}

3. Depois de buscar o bloco genesis do canal, é possível associar o peer ao canal usando o comando a seguir:

  ```
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
{: #ibp-peer-operate-toolcontainer-install-cc}

Agora, estamos prontos para instalar e instanciar o chaincode no peer. Para essas instruções, instalaremos o chaincode `fabcar` por meio do repositório `fabric-samples`. Assegure-se de que você tenha [configurado seu GOPATH ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/dev-setup/devenv.html?highlight=gopath#set-your-gopath "Configurar seu GOPATH") antes e, em seguida, faça download do chaincode `fabric-samples` do github usando os comandos a seguir:

```
cd $GOPATH/src
git clone https://github.com/hyperledger/fabric-samples
```
{:codeblock}

Execute o comando da CLI do Peer a seguir para instalar o chaincode `fabcar` no peer:

```
peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
```
{:codeblock}

Quando esse comando for concluído com êxito, você verá algo semelhante a:

```
2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
```

**Nota:** se esse chaincode já tiver sido instalado e instanciado em outro peer em execução no Starter ou Enterprise Plan, haverá etapas adicionais que devem ser executadas antes de instalar o chaincode no peer. Veja este [tópico de resolução de problemas](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-troubleshooting) para obter mais instruções sobre como evitar erros associados a como esse chaincode é instalado.

### Usando a CLI para instanciar o chaincode em um canal
{: #ibp-peer-operate-toolcontainer-instantiate-cc}

Como somente um peer tem que instanciar o chaincode em um canal, você não tem que usar o seu peer para instanciar o chaincode. Os peers hospedados no {{site.data.keyword.blockchainfull_notm}} Platform podem fazer isso. No entanto, se você desejar que o peer instancie o chaincode no canal, será possível fazer isso executando o comando abaixo. Assegure-se de que você tenha configurado o valor de `CORE_PEER_TLS_ROOTCERT_FILE` com o caminho do certificado TLS de sua CA no Starter Plan ou no Enterprise Plan.

```
peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile $HOME/fabric-ca-client/tls-ibp/tls.pem -P ""
```
{:codeblock}

Quando esse comando for concluído com êxito, você verá algo semelhante a:

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
```

Após o chaincode ter sido instanciado, é possível usar a consulta de chaincode e chamar comandos para ler e gravar dados no livro-razão do canal. Para obter mais informações, veja os comandos [peer chaincode ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/commands/peerchaincode.html) na documentação do Hyperledger Fabric. Será necessário transmitir o terminal do solicitador para seus comandos de chamada usando o IP do proxy e a porta externa do solicitador. É necessário somente passar o terminal de peer para um comando de consulta.

## Atualizando o chaincode
{: #ibp-peer-operate-update-chaincode}

Com o tempo, é provável que você precise modificar o chaincode que está em execução no peer. Como o chaincode é instalado nos peers e instanciado no canal, é necessário atualizar o chaincode em todos os peers no canal.

Conclua as etapas a seguir para atualizar seu chaincode:

1. Para atualizar o chaincode em cada peer, simplesmente execute novamente o processo usado para instalar o chaincode nos peers, usando um aplicativo cliente ou um comando da CLI. Certifique-se de especificar o mesmo nome de chaincode usado originalmente. No entanto, desta vez, incremente a `Version` do chaincode. Todos os peers precisam usar o mesmo nome e versão de chaincode.

2. Depois de instalar o novo chaincode em todos os peers no canal, use o Monitor de rede ou o
comando [peer chaincode upgrade ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) para atualizar o canal para usar o novo chaincode.

Consulte a etapa dois destas [instruções](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-update-cc) para obter mais informações sobre o uso do painel "Instalar código" do Monitor de rede para atualizar o chaincode no canal.

## Visualizando os logs de peer
{: #ibp-peer-operate-view-logs}

Os logs de componentes podem ser visualizados na linha de comandos usando os [comandos da CLI `kubectl`](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure) ou por meio do [Kibana ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.elastic.co/products/kibana "Sua janela no Elastic Search"), que está incluído no cluster do {{site.data.keyword.cloud_notm}} Private.

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

- Como alternativa, é possível acessar logs usando o [console de gerenciamento de cluster do {{site.data.keyword.cloud_notm}} Private](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html), que abre os logs no Kibana.

   **Nota:** ao visualizar seus logs no Kibana, você pode receber a resposta `No results found`. Essa condição poderá ocorrer se o {{site.data.keyword.cloud_notm}} Private usar o endereço IP do nó do trabalhador como seu nome do host. Para resolver esse problema, remova o filtro que inicia com `node.hostname.keyword` na parte superior do painel e os logs se tornarão visíveis.

## Detecção de problemas
{: #ibp-peer-operate-troubleshooting}

### **Problema:** o comando Invoke falha no peer com um erro `chaincode fingerprint inmatch`
{: #ibp-peer-operate-install-error}

É possível receber um erro `chaincode fingerprint mismatch` ao executar uma solicitação `peer chaincode invoke` em um peer que está em execução no {{site.data.keyword.cloud_notm}} Private:

```
Error: Error endorsing invoke: rpc error: code = Unknown desc = error executing chaincode: could not get ChaincodeDeploymentSpec for marbles_rp:v0: get ChaincodeDeploymentSpec for marbles_rp/nancyremotepeer from LSCC error: chaincode fingerprint mismatch data mismatch -
```

Esse erro poderá ser causado se houver uma inconsistência entre os peers que estão executando o chaincode em uma das áreas a seguir:

- Nome do chaincode
- Versão do chaincode
- Caminho do chaincode
- Binário do chaincode

Esse erro pode ocorrer se a IU do Monitor de Rede foi usada para instalar e instanciar o chaincode em um peer em execução no plano Starter ou Enterprise e, em seguida, você instalou o chaincode em um peer em execução no {{site.data.keyword.cloud_notm}} Private. O erro ocorre na solicitação `invoke` porque os caminhos de chaincode resultantes nos peers são diferentes.

**Solução:**
Se desejar executar o chaincode em peers no {{site.data.keyword.cloud_notm}}, como Starter ou Enterprise Plan, e no {{site.data.keyword.cloud_notm}} Private, não use a IU do Monitor de Rede para instalar o chaincode. Em vez disso, empacote o chaincode com o comando [`peer chaincode package`![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package) e, em seguida, instale o pacote em todos os peers executando o comando [`peer chaincode install`](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-toolcontainer-install-cc).

Se o chaincode já estiver instalado e instanciado em um canal antes de tentar instalar o chaincode em um peer do {{site.data.keyword.cloud_notm}} Private, será necessário concluir as etapas a seguir para evitar o problema:

1. Empacote o chaincode com o comando [`peer chaincode package`![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-package).
2. Instale o pacote do chaincode no peer que está em execução no {{site.data.keyword.cloud_notm}} Private executando o comando `peer chaincode install`.
3. Se você tiver os binários específicos da plataforma, será possível executar o comando [`peer chaincode upgrade`![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html?highlight=peer%20chaincode%20package#peer-chaincode-upgrade) para fazer upgrade do chaincode que está em execução no peer do Starter ou do Enterprise plan, que usa o pacote do chaincode.
4. Instancie o chaincode recém-instalado no canal usando a IU do Monitor de Rede ou a CLI.

O processo para fazer upgrade do chaincode também pode ser localizado em [`Chaincode for Operators`![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/chaincode4noah.html) na documentação do Hyperledger Fabric.
