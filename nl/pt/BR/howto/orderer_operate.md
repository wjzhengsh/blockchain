---

copyright:
  years: 2018, 2019
lastupdated: "2019-02-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Operando um solicitador no {{site.data.keyword.cloud_notm}} Private
{: #icp-orderer-operate}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Após a instalação do solicitador do {{site.data.keyword.blockchainfull}} Platform no {{site.data.keyword.cloud_notm}} Private, um configmap será criado e conterá configurações padrão para variáveis de ambiente. Em seguida, é possível mudar ou incluir variáveis de ambiente para o solicitador para configurar seu comportamento.

Como regra geral, os administradores do solicitador são responsáveis pela autoinicialização e pela manutenção de solicitadores, mas isso não é necessário em uma implementação SOLO em que apenas um solicitador existe. Em uma implementação SOLO, o administrador do solicitador é responsável por incluir novas organizações no canal do sistema do solicitador.

## Pré-requisitos
{: #icp-orderer-operate-prerequisites}

É necessário concluir algumas etapas de pré-requisito de seu cluster do {{site.data.keyword.cloud_notm}} Private para operar o solicitador.

** Pré-requisitos: **
- [Configurar suas CLIs](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure)
- [Recuperar suas informações sobre terminais do solicitador](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)
- [Fazer download de seu certificado TLS do solicitador](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert)

Em seguida, é possível usar as instruções neste tópico para operar seu solicitador. Observe que você operará seu solicitador por meio da linha de comandos, que requer a obtenção do binário da CLI `peer`. O binário da CLI `peer` é transferido por download juntamente com os outros binários, como `configtxlator`. Portanto, muitos dos comandos neste tópico iniciam com a palavra "peer". Ele não está realmente usando o peer, mas é somente o nome do binário.

### Configurando as CLIs
{: #icp-orderer-operate-kubectl-configure}

É necessário usar a ferramenta de linha de comandos **kubectl** para se conectar ao contêiner do solicitador que é executado no {{site.data.keyword.cloud_notm}} Private.

1. Efetue login na IU do cluster do {{site.data.keyword.cloud_notm}} Private. Navegue para a guia **Ferramentas de linha de comandos** e clique em **CLI do Cloud Private**. Você verá as ferramentas a seguir que podem ser transferidas por download.

   * Instalar a CLI e os plug-ins do IBM Cloud Private
   * Instalar a CLI Kubectl
   * Instalar o Helm
   * Instalar a CLI do Istio

  Para operar os solicitadores, é necessário usar as primeiras **três** ferramentas, entre as quais o Helm é opcional. Clique em cada um deles e execute os comandos `curl` para o tipo de máquina que você está usando. Em seguida, emita os comandos `chmod` e `sudo mv` para cada ferramenta. O comando `chmod` mudará a permissão da CLI em questão para torná-la executável e o comando `sudo mv` moverá o arquivo e o renomeará.

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

  Se bem-sucedido, o comando deve retornar uma resposta que seja semelhante ao exemplo a seguir, em que `orderer-6cf85f6687-hcxpl` é o nome do contêiner do solicitador.

  ```
  NAME                                 READY      STATUS             RESTARTS   AGE
  orderer-6cf85f6687-hcxpl          2/2        Running            0          6h
  ```

  Agora você está pronto para usar a ferramenta **kubectl** para obter as informações sobre terminais do solicitador.

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

### Recuperando informações sobre terminais do solicitador
{: #icp-orderer-operate-orderer-endpoint}

É necessário destinar o terminal do solicitador para fazer atualizações no canal do sistema do solicitador. Você precisará ser um [Administrador de cluster ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Funções e ações do administrador de cluster") para concluir as etapas a seguir:

1. Efetue login no console do {{site.data.keyword.cloud_notm}} Private e clique no ícone **Menu** no canto superior esquerdo.
2. Clique em **Carga de trabalho** > **Liberações do Helm**.
3. Localize o nome de sua Liberação do Helm e abra o painel de detalhes da Liberação do Helm.
4. Role para baixo até a seção **Notas** na parte inferior do painel. A seção **Notas** inclui um conjunto de comandos para ajudar a operar sua implementação do solicitador.
5. Se ainda não tiver feito isso, siga as instruções para configurar a [CLI kubectl](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure). É necessário usá-lo para interagir com seu contêiner do solicitador.
6. Em sua CLI, execute o primeiro comando na nota, que segue **1. Obtenha a URL do aplicativo executando estes comandos:** Esse comando imprimirá a URL e a porta do solicitador, que é semelhante ao exemplo a seguir. Salve essa URL, você precisará usá-la em comandos futuros para configurar o endereço do Proxy e a porta do nó externo.

  ```
  http://9.30.94.174:30159
  ```

Nesse exemplo, o endereço IP do Proxy é `9.30.94.174` e a porta externa do nó que corresponde a 7050 é `30159`.  

**Nota:** se você estiver implementando seu solicitador atrás de um firewall, será necessário abrir um intermediário para permitir que a rede na plataforma conclua essa tarefa.

### Fazendo download de seu certificado TLS do solicitador
{: #icp-orderer-operate-tls-cert}

É necessário fazer download do certificado TLS do solicitador e transmiti-lo para seus comandos para comunicar-se com seu solicitador a partir de um cliente remoto. Você precisará ser um [Administrador de cluster ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Funções e ações do administrador de cluster") para concluir as etapas a seguir:

1. Efetue login no console do {{site.data.keyword.cloud_notm}} Private e clique no ícone **Menu** no canto superior esquerdo.
2. Clique em **Carga de trabalho** > **Liberações do Helm**.
3. Localize o nome de sua Liberação do Helm e abra o painel de detalhes da Liberação do Helm.
4. Role para baixo até a seção **Notas** na parte inferior do painel. A seção **Notas** inclui um conjunto de comandos para ajudar a operar sua implementação do solicitador.
5. Se ainda não tiver feito isso, siga as instruções para configurar a [CLI kubectl](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure). É necessário usá-lo para interagir com seu contêiner do solicitador.
6. Em sua CLI, execute o terceiro comando na nota, que segue **3. Obter arquivo de certificado raiz TLS do solicitador**.  Esse comando salvará seu certificado TLS como o arquivo `cert.pem` em sua máquina local.

  **Nota:** antes de executar os comandos, é possível remover `app=orderer`, conforme mostrado no comando a seguir:

  ```
  export POD_NAME=$(kubectl get pods --namespace blockchain-dev -l "release=pa-orderer3" -o jsonpath="{.items[0].metadata.name}")
  ```
  {:codeblock}

7. Mova o certificado gerado para um local no qual é possível referenciá-lo em comandos futuros e renomeie-o para `orderertls.pem`.

  ```
  mkdir $HOME/fabric-ca-client/orderer-tls
  cp cert.pem $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

### Gerenciando os certificados no sistema local
{: #manage-certs}

Alterne para o diretório no qual a pasta MSP do administrador do solicitador é gerada. Dependendo de como você seguiu as etapas de exemplo nesta documentação ou de quantos componentes você está implementando, é possível localizar a pasta MSP em `$HOME/fabric-ca-client/orderer-admin/msp` ou `$HOME/fabric-ca-client/peer-admin/msp`

Antes de poder operar o solicitador, é necessário executar algum gerenciamento dos certificados em sua máquina local. Também é necessário assegurar que seja possível acessar os certificados TLS a partir do solicitador. Para obter mais informações sobre os certificados a serem usados, consulte [Membership Service Providers](/docs/services/blockchain/howto/CA_operate.html#ca-operate-msp) em [Operando uma autoridade de certificação no {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_operate.html#ca-operate).

1. Mova o signCert do administrador do solicitador para uma nova pasta denominada `admincerts`:

  ```
  cd $HOME/fabric-ca-client/orderer-admin/msp
  mkdir admincerts
  cp signcerts/cert.pem admincerts/cert.pem
  ```
  {:codeblock}

2. Assegure-se de [fazer download do certificado TLS de seu solicitador](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert) e de poder referenciá-lo em sua linha de comandos. Se você seguiu os comandos de exemplo nesta documentação, será possível localizar esse certificado TLS no arquivo `$HOME/fabric-ca-client/orderer-tls/orderertls.pem`.

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
├── orderer-admin
│   └── msp
│ ├── admincerts
│       │   └── cert.pem
│       ├── cacerts
│       │   └── 9-30-250-70-30395-SampleOrgCA.pem
│       ├── keystore
│       │   └── dfe06060490bf62e2bd709433fc747ff28cdbb1e040682c5d47a4e8598db4f2e_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── orderer-tls
│ └── orderertls.pem
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

## Incluindo organizações no canal do sistema do solicitador
{: #icp-orderer-operate-add-organizations-to-consortium}

**Nota:** se você planeja implementar um peer e conectá-lo a uma rede de plano Starter ou Enterprise, poderá ignorar esta etapa.

Quando você implementa um solicitador usando o gráfico do Helm do {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private, o bloco genesis é criado automaticamente com a organização de solicitador como o único administrador do canal do sistema. Isso significa que o administrador da organização do solicitador possui a capacidade exclusiva de incluir organizações no consórcio. A política que controla isso, que é conhecida como `mod_policy` para o canal do sistema do solicitador, não deve ser mudada depois que novas organizações são incluídas no canal do sistema do solicitador.

A inclusão de organizações no canal do sistema do solicitador faz com que elas sejam parte do "consórcio", que é a lista de membros que podem, por padrão, criar canais. Ser membro do consórcio também torna mais fácil ser incluído em um canal usando os certificados e informações que são listados no canal do sistema.

A atualização do canal do sistema do solicitador é obtida por meio de "transações de atualização de configuração", nas quais as informações relevantes do MSP das organizações são incluídas na configuração do canal do sistema do solicitador.

A inclusão de organizações no canal do sistema do solicitador é essencialmente o mesmo fluxo que a atualização de qualquer configuração de canal para incluir uma organização. No entanto, é necessário fazer algumas mudanças porque o canal a ser atualizado não é um canal de aplicativo e o administrador relevante é o administrador do solicitador em vez de uma organização peer.

Observe que é possível incluir uma organização em um canal sem primeiro associar-se ao canal do sistema. Para obter mais informações, veja o [Tutorial Incluindo uma organização em um canal ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html "Incluindo uma organização em um canal") na documentação do Hyperledger Fabric.

A lista a seguir mostra as etapas gerais e as tarefas serão executadas por conjuntos diferentes de organizações de seu consórcio.

1. Para se associar ao consórcio, cada organização precisa [preparar uma definição de organização](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition).
2. O administrador da organização do solicitador [forma o consórcio](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-consortium) incluindo organizações no canal do sistema do solicitador.
3. Qualquer organização do consórcio pode [criar um novo canal](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-channeltx) preparando uma transação de configuração de canal.

## Obtendo as ferramentas do Fabric
{: #icp-orderer-operate-get-fabric-tools}

É necessário fazer download das ferramentas do Hyperledger Fabric a seguir para atualizar o canal do sistema.
- [peer ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peercommand.html), que permitirá buscar o bloco genesis e atualizar o canal do sistema.
- [configtxlator ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/configtxlator.html), que converte o formato protobuf de uma configuração de canal para o formato JSON que é mais fácil de ler e atualizar.

1. Decida onde você deseja armazenar as ferramentas e, em seguida, execute este comando:

  ```
  curl -sSL http://bit.ly/2ysbOFE | bash -s 1.2.1 1.2.1 -d -s
  ```
  {:codeblock}

2. Configure o caminho para suas ferramentas, em que as ferramentas Fabric acima são transferidas por download:

  ```
  export PATH=$PATH:<full_path_to_bin_folder>
  ```
  {:codeblock}

  Por exemplo, se instalasse os binários em seu diretório inicial, você configuraria seu `PATH` como:

  ```
  export PATH=$PATH:$HOME/bin
  ```
  {:codeblock}

3. Fazer download dos binários criará uma pasta de configuração contendo um arquivo core.yaml, orderer.yaml e configtx.yaml. Configure o caminho de configuração do Fabric para este diretório de configuração:

  ```
  export FABRIC_CFG_PATH=<full_path_to_config_folder>
  ```
  {:codeblock}

  Por exemplo:
  ```
  export FABRIC_CFG_PATH=$HOME/config
  ```
  {:codeblock}

## Criando uma definição de organização
{: #icp-orderer-operate-org-definition}

A **definição** de uma organização contém o nome da organização (ID do MSP) e os certificados relevantes. O canal do sistema e os canais de aplicativos usarão essa definição para incluir sua organização nas políticas que controlam a criação do canal, as atualizações e o endosso da transação. Cada organização que deseja se juntar ao consórcio precisa concluir essa etapa. Para saber mais, consulte [preparando uma definição de organização](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition).

## Formando o consórcio
{: #icp-orderer-operate-consortium}

Chame novamente o fluxo de alto nível para formar um consórcio:

1. O canal do sistema do solicitador é formado apenas com a organização do solicitador como um membro do canal do sistema. Essa organização pode fazer atualizações sem exigir assinaturas adicionais.
2. O administrador da organização do solicitador recebe definições de organização de membros que desejam se juntar ao consórcio. A organização do solicitador usa as definições de organização para atualizar a configuração do canal do sistema do solicitador.

### Obtendo as definições de organização

O solicitador precisa receber as [definições de organização](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition) de membros que desejam se juntar ao consórcio. Isso precisa ser concluído em uma operação fora da banda com outros membros enviando os arquivos JSON que incluem sesu IDs do MSP e material de criptografia. Para referência nos comandos abaixo, assumimos que você tenha criado uma pasta denominada `org-definitions` e tenha colocado todos os arquivos relevantes nesse diretório.

### Buscando o bloco genesis do canal do sistema

1. Configure as variáveis de ambiente a seguir usando seu nome do MSP da organização do solicitador que foi estabelecido durante a implementação, o caminho para seu MSP do solicitador e o caminho para o certificado TLSCA de seu solicitador.

  ```
  export FABRIC_CFG_PATH=<PATH_TO_/config_FOLDER>
  export CORE_PEER_LOCALMSPID=<MSPID_OF_ORDERER_ORG>
  export CORE_PEER_MSPCONFIGPATH=<PATH_TO_ADMIN_MSP>  
  export CORE_PEER_TLS_ROOTCERT_FILE=<PATH_TO_ORDERER_TLS_CERT>
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = <PROXY_IP>
  export ORDERER_PORT = <EXTERNAL_NODE_PORT>
  ```
  {:codeblock}

  Substitua os campos por suas próprias informações.

    - Substitua `<CORE_PEER_LOCALMSPID>` pelo ID do MSP de sua organização do solicitador. Ele também fica visível dentro do contêiner do solicitador executando os comandos a seguir, substituindo `<orderer pod name>` pelo valor do pod de seu solicitador:

      ```
      kubectl exec -it <orderer pod name> -c orderer sh
      $ env | grep ORDERER_GENERAL_LOCALMSPID
      ```
      {:codeblock}

      A saída pode ser semelhante a:

      ```
      ORDERER_GENERAL_LOCALMSPID=ordererOrg
      ```

      Portanto, o `CORE_PEER_LOCALMSPID` é ordererOrg.

    - Substitua `<CORE_PEER_MSPCONFIGPATH>` pelo caminho para a pasta MSP do administrador da organização do solicitador.
    - Substitua `<CORE_PEER_TLS_ROOTCERT_FILE>` pelo caminho para o certificado da CA do TLS.
    - Substitua `<PROXY_IP>` pelo endereço IP do proxy das [informações do terminal do solicitador](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)
    - Substitua `<EXTERNAL_NODE_PORT>` pela porta do nó externo das [informações do terminal do solicitador](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)

  As variáveis de ambiente podem ser semelhantes ao exemplo a seguir:

  ```
  export FABRIC_CFG_PATH=$HOME/config
  export CORE_PEER_LOCALMSPID=ordererOrg
  export CORE_PEER_MSPCONFIGPATH=$HOME/fabric-ca-client/orderer-admin/msp/
  export CORE_PEER_TLS_ROOTCERT_FILE=$HOME/fabric-ca-client/orderer-tls/orderertls.pem 
  export CORE_PEER_TLS_ENABLED=true
  export PROXY = 9.30.94.174
  export ORDERER_PORT = 30159
  ```

  É possível verificar essas variáveis de ambiente em qualquer ponto, emitindo os comandos a seguir:

  ```
  env | grep CORE
  env | grep PATH
  ```
  {:codeblock}

2. Configure o nome do canal como uma variável de ambiente. O nome para o canal do sistema do solicitador sempre será `test-system-channel-name`.

  ```
  export CHANNEL_NAME=test-system-channel-name
  ```
  {:codeblock}

3. Busque o bloco genesis do canal do sistema. Primeiro, crie um diretório chamado `configupdate` dentro de `org-definitions` para armazenar os arquivos de configuração gerados durante o processo de atualização de configuração. É possível chamar esse diretório do que desejar, mas os comandos de amostra a seguir usarão `configupdate.`

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls --cafile <PATH_TO_ORDERER_TLS_CERT>
  ```
  {:codeblock}

  Substitua `<orderer_TLS_root_cert_file>` pelo caminho para o arquivo `orderertls.pem` que você criou nesta [etapa](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-tls-cert). Por exemplo, seu comando pode ser semelhante ao exemplo a seguir:

  ```
  peer channel fetch config ./configupdate/genesis.pb -o $PROXY:$ORDERER_PORT -c $CHANNEL_NAME --tls  --cafile $HOME/fabric-ca-client/orderer-tls/orderertls.pem
  ```
  {:codeblock}

  Se bem-sucedido, esse comando gerará a saída a seguir.

  ```
  2018-10-25 20:53:06.377 UTC [cli/common] readBlock -> INFO 002 Received block: 0
  2018-10-25 20:53:06.380 UTC [cli/common] readBlock -> INFO 003 Received block: 0
  ```

  Também é possível localizar o bloco `genesis.pb` em seu sistema de arquivos posteriormente.

### Criando a atualização do canal do sistema
{: #icp-orderer-operate-system-channel-update}

A [ferramenta do Fabric](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-get-fabric-tools) transferida por download `configtxtlator` converte o formato protobuf de uma configuração de canal para o formato JSON e vice-versa.

Estas etapas seguem o fluxo geral do tutorial de atualização de canal sobre [como converter o bloco em formato JSON ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")]( https://hyperledger-fabric.readthedocs.io/en/release-1.2/channel_update_tutorial.html#convert-the-configuration-to-json-and-trim-it-down "Converter a configuração para JSON e reduzi-la"). É necessário fazer algumas mudanças nos comandos no tutorial para refletir o fato de que você está atualizando o canal do sistema solicitador em vez de um canal de aplicativo. É possível visitar o tutorial para obter mais detalhes sobre esse processo. Esta seção simplesmente fornece os comandos para você.

1. Copie o arquivo JSON de definição de organização da pasta em que você [criou sua organização](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition) para a pasta `configupdate`. No exemplo de comando abaixo, o arquivo JSON de definição da organização é `org1definition.json`:

   ```
   cp <path_to_config_folder>/org1definition.json $HOME/fabric-ca-client/org-definitions/configupdate
   ```
   {:codeblock}

2. Converta o bloco genesis no formato JSON. Execute os seguintes comandos:

  ```
  cd configupdate
  configtxlator proto_decode --input genesis.pb --type common.Block --output ./config_block.json
  jq .data.data[0].payload.data.config ./config_block.json  > config.json
  ```
  {:codeblock}

3. Execute o comando a seguir para incluir o material de criptografia de uma organização na configuração do consórcio. Substitua <NEWORGMSP> pelo ID do MSP da organização para a [organização que você criou](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate-organization-definition).

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"<NEWORGMSP>":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

  O comando pode ser semelhante ao exemplo a seguir:

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```

4. Repita esse comando para cada organização que precisa entrar no consórcio. Assegure-se de mudar o arquivo `./orgdefinition.json` para ser o novo arquivo JSON da organização.

  ```
  jq -s '.[0] * {"channel_group":{"groups":{"Consortiums":{"groups":{"SampleConsortium":{"groups": {"org1":.[1]}}}}}}}' config.json ./orgdefinition.json > modified_config.json
  ```
  {:codeblock}

5. Execute os comandos a seguir para converter o arquivo `modified_config.json` para a atualização de configuração no formato JSON.

  ```
  configtxlator proto_encode --input config.json --type common.Config --output config.pb 
  configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb 
  configtxlator compute_update --channel_id $CHANNEL_NAME --original config.pb --updated modified_config.pb --output config_update.pb 
  configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json
  ```
  {:codeblock}

6. Após a conclusão desses comandos, coloque um envelope em `config_update`.

  ```
  echo '{"payload":{"header":{"channel_header":{"channel_id":"'$CHANNEL_NAME'", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json
  ```
  {:codeblock}

7. Execute o comando a seguir para converter a atualização de configuração de volta para o formato protobuf:

  ```
  configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output ./config_update_in_envelope.pb
  ```
  {:codeblock}

### Enviando a atualização para o canal do sistema

Antes de concluir estas etapas, verifique se `FABRIC_CFG_PATH`, `$PROXY` e `ORDERER_PORT` estão configurados corretamente. Execute o comando a seguir:

```
export ORDERER_CA=<path and file name of the orderer TLS CA cert>
export PROXY = <proxy ip address from [orderer endpoint information](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)>
export ORDERER_PORT = <external node port from [orderer endpoint information](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-endpoint)>
```
{:codeblock}

As variáveis de ambiente devem ser semelhantes ao exemplo a seguir:

```
ORDERER_CA = /Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_TLS_ROOTCERT_FILE=/Users/chandra/fabric-ca-client/orderer-tls/orderertls.pem
CORE_PEER_LOCALMSPID=ordererOrg
CORE_PEER_TLS_ENABLED=true
CORE_PEER_MSPCONFIGPATH=/Users/chandra/fabric-ca-client/orderer-admin/msp
PROXY = 9.30.94.174
ORDERER_PORT = 30159

```

Depois de criar a atualização de configuração do canal, que é `config_update_in_envelope.json`, a organização do solicitador pode enviar a atualização do canal usando o comando a seguir:

```
peer channel update -f config_update_in_envelope.pb -c $CHANNEL_NAME -o $PROXY:$ORDERER_PORT --tls --cafile $ORDERER_CA
```
{:codeblock}

Esse comando assina simultaneamente a solicitação de atualização e a envia ao solicitador. Como a organização do solicitador é o único administrador do canal do sistema, apenas uma assinatura é necessária para que essa atualização seja uma solicitação válida. Se a atualização do canal do sistema for bem-sucedida, você verá a saída a seguir:

```
2018-10-28 23:44:00.498 UTC [channelCmd] update -> INFO 002 Successfully submitted channel update
```

É possível incluir diversas definições de organização em uma única atualização de configuração do canal do sistema do solicitador, mas é uma melhor prática atualizar o canal com uma organização por vez e assegurar que a atualização seja bem-sucedida.

## Visualizando os logs de solicitador
{: #icp-orderer-operate-orderer-view-logs}

Os logs de componentes podem ser visualizados na linha de comandos usando os [`kubectl CLI commands`](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure) ou por meio do [Kibana ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.elastic.co/products/kibana "Sua janela no Elastic Search"), que está incluído no cluster do {{site.data.keyword.cloud_notm}} Private.

- Use o comando `kubectl logs` para visualizar os logs do contêiner dentro do pod. Se você não tiver certeza de seu nome do pod, execute o comando a seguir para visualizar sua lista de pods.

  ```
  kubectl get pods
  ```
  {:codeblock}

  Em seguida, execute o comando a seguir para recuperar os logs para o contêiner do solicitador que reside dentro do pod, substituindo `<pod_name>` com o nome de seu pod da saída de comando acima:

  ```
  kubectl logs <pod_name> -c orderer
  ```
  {:codeblock}

  Para obter mais informações sobre o comando `kubectl logs`, veja [Documentação do Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs “Getting Started”)

- Como alternativa, é possível acessar logs usando o [console de gerenciamento de cluster do {{site.data.keyword.cloud_notm}} Private ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/troubleshoot/events.html), que abre os logs no Kibana.

  **Nota:** ao visualizar seus logs no Kibana, você pode receber a resposta `No results found`. Essa condição poderá ocorrer se o {{site.data.keyword.cloud_notm}} Private usar o endereço IP do nó do trabalhador como seu nome do host. Para resolver esse problema, remova o filtro que inicia com `node.hostname.keyword` na parte superior do painel e os logs se tornarão visíveis.

## Detecção de problemas
{: #icp-orderer-operate-troubleshooting}

### **Problema:** o comando `peer channel update` falha com um erro.
{: #icp-orderer-operate-peer-channel-update-error}

É possível receber o erro a seguir ao executar um comando `peer channel update`:

```
INFO 001 Endorser and orderer connections initialized Error: got unexpected status: BAD_REQUEST -- initializing channelconfig failed: could not create channel Consortiums sub-group config: Attempted to define two different versions of MSP: Org1
```

Esse erro pode ser causado quando o peer está usando o mesmo ID de MSP da organização que o solicitador.  

**Solução:**  

Para resolver esse problema, é necessário reimplementar seu peer especificando um ID de MSP da Organização que seja diferente do que foi usado quando o solicitador foi implementado.
