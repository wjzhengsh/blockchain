---

copyright:
  years: 2017, 2018
lastupdated: "2018-09-05"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Operando os Peers Remotos em  {{site.data.keyword.cloud_notm}}  Particular
{: #remote-peer-operate}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Depois de configurar os peers remotos do {{site.data.keyword.blockchainfull}} Platform no {{site.data.keyword.cloud_notm}} Private (ICP), é necessário concluir várias etapas operacionais antes que seu peer possa emitir transações para consultar e chamar o livro-razão da rede de blockchain. As etapas incluem a inclusão de sua organização em um canal, a associação de seu peer remoto ao canal, a instalação do chaincode no peer remoto, a instanciação do chaincode no canal e a conexão de aplicativos em seu peer remoto.
{:shortdesc}

É necessário concluir algumas etapas de pré-requisito por meio do Cluster do ICP para operar seu peer remoto.

** Pré-requisitos: **
- [ Recuperando suas informações de terminal peer remoto ](#remote-peer-retrieve-endpoint-info)
- [Efetuando login no contêiner de peer usando kubectl](#remote-peer-kubectl-configure)
- [ Reiniciando seu peer remoto ](#remote-peer-restart)

Em seguida, é possível usar um dos métodos a seguir para operar seu peer remoto.

** Caminhos de Operações: **
- [ Utilizando os SDKs do Fabric ](#remote-peer-operate-with-sdk)
- [ Usando a Linha de Comandos ](#remote-peer-cli-operate)

Os SDKs do Fabric são o caminho recomendado, embora as instruções assumam familiaridade com a operação do SDK.

Recomenda-se implementar pelo menos duas instâncias do gráfico de Helm do peer remoto para [alta disponibilidade](remote_peer_icp.html#high-availability). Portanto, é necessário seguir estas etapas de operações uma vez para cada peer. Quando estiver pronto para chamar e consultar o chaincode de seu aplicativo, conecte-se a ambos os peers para assegurar-se de que seus [aplicativos sejam altamente disponíveis](../v10_application.html#ha-app).

**Nota**: um peer remoto do {{site.data.keyword.blockchainfull_notm}} Platform não tem acesso à funcionalidade completa ou ao suporte de peers hospedados no {{site.data.keyword.blockchainfull_notm}} Platform. Como resultado, não é possível usar o Monitor de rede para operar um peer remoto. Antes de iniciar a execução de peers remotos, assegure-se de revisar as [considerações](remote_peer.html#remote-peer-limitations).

## Pré-requisitos

Se você planeja usar os SDKs ou a linha de comandos, é necessário concluir as etapas nesta seção no curso de operação de seu peer. É possível concluir as duas primeiras etapas, recuperando o terminal de peer e configurando o kubectl, antes de iniciar.

### Recuperando as informações sobre terminais de peer remoto
{: #remote-peer-retrieve-endpoint-info}

Efetue login no console do ICP. Clique no ícone **Menu** no canto superior esquerdo. Clique em  ** Carga de Trabalho **  >  ** Implementações **. Em seguida, localize e clique no nome de sua instância de peer remoto para abrir a tela de visão geral da implementação. Role para baixo até a seção **Expor detalhes** e localize `Ingress IP` e `Ports` de seu peer remoto.

```
Ingress IP: <IP address>
Ports: < Port List>
```
{:codeblock}

Anote o endereço IP de Ingresso e as portas do Nó para se conectar ao peer remoto. É necessário usar essas informações ao instalar e instanciar o chaincode no peer remoto. No exemplo a seguir, seu endereço peer é `9.46.126.89:30162`, `9.46.126.89:30260` ou `9.46.126.89:32051`.

```
Ingress IP: <9.46.126.89>
Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
```
{:codeblock}

### Configurando a CLI kubectl
{: #remote-peer-kubectl-configure}

É necessário usar a ferramenta de linha de comandos **kubectl** para realizar algumas operações necessárias dentro do contêiner de peer executado no ICP. Efetue login no cluster usando a ferramenta CLI do {{site.data.keyword.cloud_notm}} Private:

```
bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation

```
{:codeblock}
<!-- removing per defect #208
docker login <cluster_CA_domain>:8500
-->

Em seguida, efetue login no painel do Cluster do ICP. Clique em seu avatar do usuário no canto superior direito da IU do ICP e, em seguida, clique em **Configurar cliente**. Copie, cole e execute os comandos na janela do terminal em sua máquina local. É possível verificar se a configuração funcionou com sucesso executando o comando a seguir:

```
kubectl -n < namespace > get pods
```
{:codeblock}

em que  ` -n <namespace>` deve ser especificado quando o namespace `default` não está sendo usado. Substitua  `<namespace>` com o valor do seu namespace em seu cluster de ICP.


O comando deve resultar em uma resposta, que é semelhante ao exemplo a seguir. `remotepeer-bd65c4769-95rmm` é o nome do contêiner de peer.

```
NAME                                 READY      STATUS             RESTARTS   AGE
remotepeer-bd65c4769-95rmm           2/2        Running            0          6h
```

Se você não reconhecer o nome do pod dos peers remotos na resposta, será possível localizar o nome do pod na área de janela **Pods** do painel de implementação.

Usando exec no contêiner de peer remoto, execute o seguinte comando substituindo o valor de `<PEER_CONTAINER_NAME>` pelo nome do Pod acima.

```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
{:codeblock}
em que  ` -n <namespace>` deve ser especificado quando o namespace `default` não está sendo usado. Substitua  `<namespace>` com o valor do seu namespace em seu cluster de ICP.

Por exemplo:

```
kubectl exec -it remotepeer-bd65c4769-95rmm bash
```
{:codeblock}

Agora você está pronto para usar a CLI do contêiner de peer. Será necessário usar a CLI para fazer download do certificado TLS de peers e fazer upload do certificado de assinatura para o peer se você estiver usando os SDKs ou a linha de comandos.

## Reiniciando um peer remoto executado no ICP
{: #remote-peer-restart}

Sempre que um certificado é incluído no peer remoto, o nó do peer precisa ser reiniciado para que ele reconheça o novo certificado.

É possível usar os comandos **kubectl** para reiniciar o peer remoto executado no ICP. O comando requer o nome do peer remoto **pod** e seu **contêiner** como parâmetros de comando. Para obter mais informações sobre como usar os comandos kubectl, consulte [Configurando a CLI de kubectl](#remote-peer-kubectl-configure).

1. No console do ICP, localize o nome **pod** e o nome do **contêiner** para a implementação do peer remoto.

  1. Clique no ícone de menu no canto superior esquerdo do console e, em seguida, clique em **Cargas de trabalho > Implementações**.
  2. Localize e clique em sua liberação de peer remoto na tabela para abri-la.
  3. Role para baixo para visualizar o nome **pod** associado. Anote o nome do **pod**.
  4. Clique no pod para abri-lo.
  5. Clique na guia **Contêineres**. Anote o nome do **contêiner** do peer remoto.

2. Na linha de comandos, execute o comando a seguir para reiniciar o peer, substituindo `<REMOTE_PEER_POD_NAME>` e `<REMOTE_PEER_CONTAINER_NAME>` pelos valores acima.

  ```
  Kubectl exec < REMOTE_PEER_POD_NAME> -c < REMOTE_PEER_CONTAINER_NAME> /sbin/killall5
  ```
  {:codeblock}

3. Será possível verificar se o peer remoto foi reiniciado executando o comando `kubectl get pods` e examinando a saída para a contagem **RESTART** de seu pod.

Além disso, é possível usar a [solicitação de HEAD](monitor_network.html#monitor-nodes) para verificar a disponibilidade de seu peer remoto.


## Usando SDKs do Fabric para operar o peer remoto
{: #remote-peer-operate-with-sdk}

Os SDKs do Hyperledger Fabric fornecem um conjunto poderoso de APIs que permitem que os aplicativos interajam e operem redes de blockchain. É possível localizar a lista mais recente de idiomas suportados e a lista completa de APIs disponíveis nos SDKs do Fabric na [documentação da comunidade do Hyperledger Fabric SDK ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "documentação da comunidade do Hyperledger Fabric SDK"). É possível usar os SDKs do Fabric para associar o peer remoto a um canal no {{site.data.keyword.blockchainfull_notm}} Platform, instalar um chaincode no peer e instanciar o chaincode em um canal.

As instruções a seguir usam o [Fabric Node SDK ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/ "Fabric Node SDK") para operar o peer remoto e assumir familiaridade anterior com o SDK. É possível usar o [tutorial de desenvolvimento de aplicativos](../v10_application.html) para aprender a usar o Node SDK antes de iniciar e como um guia para desenvolver aplicativos com seu peer remoto quando estiver pronto para chamar e consultar o chaincode.

### Instalando o SDK do Nó

É possível usar o NPM para instalar o [Node SDK ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/ "Node SDK"):
```
malha de instalação npm-client@1.1
```
{:codeblock}

Recomenda-se usar a versão 1.1 do Node SDK.

### Preparando o SDK para trabalhar com o peer remoto
{: #remote-peer-node-sdk}

Antes de usar o SDK para operar o peer remoto, é necessário gerar os certificados necessários (inscrição) que permitirão que seu aplicativo se comunique com sua rede no {{site.data.keyword.blockchainfull_notm}} Platform e no peer remoto. Siga as etapas para [se inscrever com o SDK](../v10_application.html#enroll-app-sdk) usando sua identidade de **administrador**. O tutorial [Desenvolvendo aplicativos](../v10_application.html) também é inscrito como o **administrador**, portanto, não é necessário modificar o código de amostra.

### Fazendo Upload de um signCert para a Plataforma  {{site.data.keyword.blockchainfull_notm}}
{: #remote-peer-upload-sdk}

É necessário fazer upload de seu certificado de assinatura do SDK para a rede no {{site.data.keyword.blockchainfull_notm}} Platform para que seu aplicativo tenha permissão para operar a rede.

- É possível localizar seu certificado de assinatura no diretório que o SDK gerou seu material de criptografia, no arquivo denominado administrador. Copie o certificado dentro das aspas depois do campo `certificate`, iniciando com `-----BEGIN CERTIFICATE-----` e terminando com `-----END CERTIFICATE-----`. É possível usar a CLI para converter o certificado no formato PEM, emitindo o comando `echo -e "<CERT>" > admin.pem`. Em seguida, é possível colar o conteúdo do certificado na rede de blockchain usando o Monitor de rede. Efetue login na rede no {{site.data.keyword.blockchainfull_notm}} Platform. Na tela "Membros" do Monitor de rede, clique em **Certificados** > **Incluir certificado**. Especifique qualquer nome para o seu certificado e cole o conteúdo no campo **Certificado**. Clique em **Reiniciar** quando for perguntado se você deseja reiniciar os peers. Essa ação deve ser executada antes que sua organização se associe ao canal.

### Fazendo upload de um signCert para o peer remoto
{: #remote-peer-upload-signcert}

Também é necessário fazer upload do certificado de assinatura do SDK para o peer remoto e reiniciá-lo. É necessário instalar o mesmo certificado de assinatura [transferido por upload para o {{site.data.keyword.blockchainfull_notm}} Platform ](#remote-peer-upload-sdk) dentro do contêiner de peer remoto.

Execute os comandos a seguir de [dentro do contêiner de peer](#remote-peer-kubectl-configure) para fazer upload de seu certificado.
```
cd /mnt/crypto/peer/peer/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

Substitua o texto  `<CERT.PEM>` pelo signCert, inciando com `-----BEGIN CERTIFICATE-----` e terminando com `-----END CERTIFICATE-----`. **Observe** que um arquivo cert.pem já existe nesse diretório. Dê o nome `cert2.pem` ou semelhante ao novo certificado para evitar sobrescrever o que já está dentro do contêiner.

Como um novo certificado foi incluído, será necessário reiniciar o contêiner para que o peer retire o certificado. Siga essas [instruções](#remote-peer-restart) para reiniciar o peer.

### Passando o certificado TLS do peer remoto para o SDK
{: #icp-remote-peer-download-tlscert}

É necessário copiar o conteúdo do `cacert.pem` do TLS do contêiner de peer para seu aplicativo para autenticar a comunicação do SDK. No [contêiner da CLI do peer](#remote-peer-kubectl-configure), execute o comando a seguir:
```
cat /mnt/certs/tls/cacert.pem
```
{:codeblock}

Copie o texto inteiro do arquivo `cacert.pem` para sua área de transferência, que inicia com o primeiro `-----BEGIN CERTIFICATE-----` e termina com o último `-----END CERTIFICATE-----`. Salve esse certificado em seu sistema de arquivos local no formato PEM. Em seguida, é possível importar o certificado TLS para seu aplicativo usando um comando de arquivo de leitura simples.
```
var caCert = fs.readFileSync (path.join (__dirname, './cacert.pem'));
```
{:codeblock}

### Fornecendo as informações sobre terminal de peer remoto para o SDK
{: #remote-peer-SDK-endpoints}

Localize as [informações sobre terminais de seu peer remoto](#remote-peer-retrieve-endpoint-info) e forneça-as para o SDK declarando uma nova variável de peer ou atualizando seu Perfil de conexão. O exemplo a seguir define o peer como um terminal em sua rede de malha e passa para ele o certificado TLS importado.
```
var peer = fabric_client.newPeer('grpcs://9.46.126.89:31618', { pem:  Buffer.from(caCert).toString(), 'ssl-target-name-override': remotepeer.blockchain.com});
```
{:codeblock}

É necessário especificar um `ssl-target-name-override` de `<something>.blockchain.com` para que o peer resolva a solicitação de DNS.

**Nota:** como o peer é remoto, outras organizações na rede do {{site.data.keyword.blockchainfull_notm}} Platform não poderão localizar as informações sobre terminais do peer no Perfil de conexão. Se outras organizações precisarem enviar ao peer remoto uma transação para ser endossada, será necessário fornecer o IP público e os certificados TLS em uma operação fora da banda.

### Usando o SDK para se associar a um canal
{: #remote-peer-join-channel-sdk}

Como membro da rede de blockchain, sua organização precisa ser incluída em um canal na rede antes de poder associar o peer remoto ao canal.

  - É possível iniciar um novo canal para o peer remoto. Como o inicializador de canais, é possível incluir automaticamente a sua organização durante a [criação do canal](create_channel.html#creating-a-channel). Observe que você precisa ter pelo menos um peer no {{site.data.keyword.blockchainfull_notm}} Platform antes de poder criar um canal no Monitor de rede.  

  - Outro membro da rede de blockchain também pode incluir sua organização em um canal existente usando uma [atualização de canal](create_channel.html#updating-a-channel). Um membro do canal com peers no {{site.data.keyword.blockchainfull_notm}} Platform pode usar o Monitor de rede para incluir a organização no canal, mesmo que você não hospede nenhum peer na plataforma.

    Após sua organização ser incluída em um canal, será necessário incluir o certificado de assinatura do seu parceiro no canal para que outros membros possam verificar sua assinatura digital durante as transações. O peer remoto faz upload de seu certificado de assinatura durante a instalação, para que você precise apenas sincronizar o certificado com o canal. Na tela "Canais" do Monitor de rede, localize o canal ao qual sua organização se associa e selecione **Certificado de sincronização** na lista suspensa sob o cabeçalho **Ação**. Essa ação sincroniza os certificados em todos os peers no canal.

Quando sua organização fizer parte do canal, siga as instruções de [associação a um canal](../v10_application.html#join-channel-sdk). É necessário fornecer a URL do serviço de solicitação e o nome do canal.

### Usando o SDK para instalar o chaincode no peer
{: #remote-peer-install-cc-sdk}

Use as instruções a seguir para usar o SDK para [instalar um chaincode](../v10_application.html#install-cc-sdk) no peer remoto.

### Usando o SDK para instanciar o chaincode no canal
{: #remote-peer-instantiate-cc-sdk}

Apenas um membro do canal precisa instanciar ou atualizar o chaincode. Portanto, qualquer membro de rede do canal com peers no {{site.data.keyword.blockchainfull_notm}} Platform pode usar o Monitor de rede para instanciar o chaincode e especificar políticas de endosso. No entanto, se você quiser usar o peer remoto para instanciar chaincode em um canal, será possível usar o SDK e seguir as instruções para [instanciar um chaincode](../v10_application.html#instantiate-cc-sdk).


## Usando a CLI para operar o peer remoto
{: #remote-peer-cli-operate}

`Também é possível operar seu peer remoto por meio da linha de comandos usando o Fabric CA Client e o contêiner de ferramentas do Fabric. Nestas instruções, geraremos primeiramente os certificados necessários usando o Fabric CA Client e, em seguida, usaremos o contêiner de ferramentas do Fabric para operar o peer associando-o a um canal, instalando um chaincode e, em seguida, instanciando o chaincode em um canal.

### Inscrição usando o cliente Fabric CA
{: #peer-client-enroll}

A primeira etapa é gerar os certificados necessários (inscrição) usando o Fabric CA Client. Primeiro, é necessário instalar o Fabric CA Client. Faça download dos [binários de fabric-ca v1.1.0 de sua plataforma](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) para a máquina local, extraia-os e mova-os para uma pasta, como `$HOME/fabric-ca-remote/`.

1.  Prepare seu ambiente para usar o Fabric CA Client. Mude para o diretório no qual os binários do cliente foram movidos para que seja possível referenciá-lo diretamente em seus comandos.
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  Configure o caminho no qual o cliente pode criar suas chaves. Se esta for a primeira vez que executará o comando `enroll`, a pasta `msp` e o arquivo `.yaml` não existirão. Caso tenha executado o comando `enroll` antes, é importante excluir qualquer material de configuração das tentativas de inscrição anteriores, usando os comandos `rm` abaixo:
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-remote
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3.  É necessário recuperar as informações sobre terminais de sua Autoridade de certificação no {{site.data.keyword.blockchainfull_notm}} Platform. Na tela "Visão geral" do Monitor de rede, clique no botão **Perfil de conexão** e abra o arquivo JSON que contém as credenciais de rede. Role para baixo até a seção `certificateAuthorities` e anote as informações abaixo.
    ```
    Hostname and port for CA: url (without the https prefix)
    Admin user ID: enrollId
    Admin password: enrollSecret
    CA Name: caName
    ```

4.  Também será necessário recuperar um certificado TLS por meio do {{site.data.keyword.blockchainfull_notm}} Platform para se comunicar com a CA. As credenciais de rede acima incluem o certificado necessário. Na seção `certificateAuthorities` do arquivo de Perfil de conexão, copie o certificado que segue `"pem:"`, que começa com `-----BEGIN CERTIFICATE-----` e termina com `-----END CERTIFICATE-----`. ** Não inclua as aspas **.

    Execute o comando a seguir por meio de uma janela do terminal, substituindo `<certificate>` pelo certificado copiado.
    ```
    echo -e "< certificate>" > $HOME/fabric-ca-remote/cert.pem
    ```
    {:codeblock}

 Armazene esse certificado em um local que possa ser referenciado em comandos futuros.

5.  Agora você está pronto para gerar os certificados necessários. Executando o comando a seguir:
    ```
    ./fabric-ca-client enroll -u https://<enroll_id>:<enrollSecret>@<ca_hostname_with_port> --caname <ca_name> --tls.certfiles <tls_cert_file>
    ```
    {:codeblock}

    Por
Exemplo:
    ```
    ./fabric-ca-client enroll -u https://admin:C25A062870@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
    ```
    {:codeblock}

    Quando o comando for concluído com sucesso, será possível ver uma resposta semelhante ao exemplo a seguir:
    ```
    2018/06/13 09:00:45 [INFO] Created a default configuration file at
    /fabric-ca-remote/fabric-ca-client-config.yaml
    2018/06/13 09:00:45 [INFO] TLS Enabled
    2018/06/13 09:00:45 [INFO] generating key: &{A:ecdsa S:256}
    2018/06/13 09:00:45 [INFO] encoded CSR
    2018/06/13 09:00:46 [INFO] Stored client certificate at
    /fabric-ca-remote/msp/signcerts/cert.pem
    2018/06/13 09:00:46 [INFO] Stored root CA certificate
    at /fabric-ca-remote/msp/cacerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    2018/06/13 09:00:46 [INFO] Stored intermediate CA certificates at
    /fabric-ca-remote/intermediatecerts/ash-4-secure-blockchain-ibm-com-71139-PeerOrg1CA.pem
    ```

    O comando gerará os certificados necessários e os armazenará em seguida em uma pasta denominada `msp` em `$FABRIC_CA_CLIENT_HOME`. Essa pasta e seus certificados serão importantes nas etapas a seguir.

### Gerenciando os certificados no sistema local
{: #manage-certs}

Antes de operarmos o peer remoto, precisaremos fazer algum gerenciamento dos certificados em nossa máquina local e fazer upload de alguns dos certificados gerados pelo Fabric CA Client para o {{site.data.keyword.blockchainfull_notm}} Platform e seu peer. Também será necessário fazer download de certificados TLS do Platform e do peer. Se quiser saber mais sobre os certificados com os quais estará trabalhando e as tarefas que estará executando, consulte [Gerenciando certificados no {{site.data.keyword.blockchainfull_notm}} Platform](../certificates.html).

Na máquina local, abra um terminal de comando e navegue para o diretório para o qual moveu os binários do Fabric-CA-Client e armazenou a pasta MSP.
1. Copie o arquivo `cert.pem` da pasta `signcerts` para uma nova pasta `admincerts`.  
  ```
  cd $FABRIC_CA_CLIENT_HOME/msp
  mkdir admincerts
  cd admincerts/
  cp ../signcerts/cert.pem  .
  ```
  {:codeblock}

2. Também será necessário copiar o certificado TLS do peer remoto do contêiner de peer no ICP para a máquina local.

  - [Efetue login no contêiner do peer](#remote-peer-kubectl-configure) e execute o comando a seguir:
    ```
    cat /mnt/certs/tls/cacert.pem
    ```
    {:codeblock}

    Copie o texto inteiro do arquivo `cacert.pem` para sua área de transferência, iniciando com o primeiro `-----BEGIN CERTIFICATE-----` e terminando com o último `-----END CERTIFICATE-----`.

  - Altere novamente para a máquina local e execute os comandos a seguir. Substitua o texto  `<CACERT.PEM>`  com o texto da área de transferência.
    ```
    cd /$FABRIC_CA_CLIENT_HOME/msp
    mkdir tls
    cd tls/
    echo -e "<CACERT.PEM>" > cacert.pem
    ```
    {:codeblock}

3. Para fornecer à CLI a autorização para instalar o chaincode no peer, é necessário fazer upload do signCert gerado pelo Fabric CA Client para a pasta do administrador de peers e reiniciar o peer. Portanto, copie o certificado `admincert/cert.pem` da máquina local para o diretório `/mnt/crypto/peer/peer/msp/admincerts/` no contêiner de peer como `cert2.pem`.

    - Em sua máquina local, execute os comandos a seguir:

      ```
      cd $FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - Copie o texto do arquivo `cert.pem` para a área de transferência iniciando com `-----BEGIN CERTIFICATE-----` e terminando com `-----END CERTIFICATE-----`.

    - Altere novamente para o contêiner de peer e execute os comandos a seguir para incluir o cert.pem nos certificados de peer. Substitua o texto  `<CERT.PEM>`  com o texto da área de transferência.

      ```
      cd /mnt/crypto/peer/peer/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **Nota:** já existe um arquivo cert.pem nesse diretório. Não o sobrescreva.

    - Como um novo certificado foi incluído, será necessário reiniciar o contêiner para que o peer retire o certificado. Siga essas [instruções](#remote-peer-restart) para reiniciar o peer.

4. É necessário fazer upload do mesmo certificado `admincert/cert.pem` da máquina local para o {{site.data.keyword.blockchainfull_notm}} Platform para que uma CLI ou um aplicativo remoto executem operações de canal, como buscar o bloco genesis do canal e instanciar o chaincode.
    1. Na máquina local, execute cat do arquivo `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` e, em seguida, copie-o para a área de transferência.
    2. Entre no Monitor de rede de sua rede no {{site.data.keyword.blockchainfull_notm}} Platform.
    3. Clique em **Membros** no navegador esquerdo e clique na guia **Certificados**.
    4. Clique no botão  ** Incluir Certificado ** .
    5. Na janela **Incluir certificado**, forneça um nome para o certificado, como `fabrictools.pem`, cole o certificado recém-copiado na área de transferência e clique em **Enviar**.
    6. Será perguntado se você deseja reiniciar os peers. Clique em  ** Reiniciar **.
    7. Clique em **Canais** no navegador esquerdo e localize o canal ao qual o peer remoto será associado.
    8. Clique nos três pontos sob o cabeçalho **Ações** e clique em **Sincronizar certificado**. Na janela **Sincronizar certificado**, clique em **Enviar**.

    **Nota**: é importante sincronizar o certificado de canal antes que o peer se associe ao canal abaixo.

### Configurando o Contêiner de Ferramentas do Fabric

Depois de movermos todos os nossos certificados para o local necessário, estamos prontos para instalar e usar o contêiner de ferramentas do Fabric por meio do docker. Mais uma vez, é necessário executar esses comandos por meio do diretório no qual a pasta MSP foi armazenada.

1.  Faça download da imagem do docker das ferramentas do Fabric com o seguinte comando:
    ```
    docker pull ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

2.  Execute os comandos a seguir para iniciar o contêiner de ferramentas do Fabric.
    ```
    docker network create blockchain.com
    docker run -ti --network blockchain.com -v ${PWD}:/mnt ibmblockchain/fabric-tools:1.1.0
    ```
    {:codeblock}

    Esse comando monta o diretório MSP dentro do contêiner de ferramentas.

3.  O endereço do peer remoto deve ser resolvido para um nome de domínio com um sufixo de `blockchain.com`. Isso pode ser feito por meio de seu DNS ou modificando o arquivo /etc/hosts. Para propósitos destas instruções, modificaremos o arquivo `/etc/hosts` no contêiner de ferramentas do Fabric. Efetue login no console do ICP e localize as [informações sobre terminais](#remote-peer-retrieve-endpoint-info) de seus peers remotos. Depois de ter os peers `INGRESS_IP`, execute o comando a seguir com o endereço de seu peer remoto.
    ```
    echo -e "< INGRESS_IP> remotepeer.blockchain.com" > > /etc/hosts
    ```
    {:codeblock}

### Usando o contêiner de ferramentas do Fabric para associar o peer remoto ao canal
{: #icp-cli-join-peer-to-channel}

Antes de ser possível executar os comandos da CLI para associar o peer remoto a um canal, sua organização precisará ser incluída em um canal na rede.

  - É possível iniciar um novo canal para o peer remoto. Como o inicializador de canais, é possível incluir automaticamente a sua organização durante a [criação do canal](create_channel.html#creating-a-channel). Observe que você precisa ter pelo menos um peer no {{site.data.keyword.blockchainfull_notm}} Platform antes de poder criar um canal no Monitor de rede.  

  - Outro membro da rede de blockchain também pode incluir sua organização em um canal existente usando uma [atualização de canal](create_channel.html#updating-a-channel). Um membro do canal com peers no {{site.data.keyword.blockchainfull_notm}} Platform pode usar o Monitor de rede para incluir a organização no canal, mesmo que você não hospede nenhum peer na plataforma.

    Após sua organização ser incluída em um canal, será necessário incluir o certificado de assinatura do seu parceiro no canal para que outros membros possam verificar sua assinatura digital durante as transações. O peer remoto faz upload de seu certificado de assinatura durante a instalação, para que você precise apenas sincronizar o certificado com o canal. Na tela "Canais" do Monitor de rede, localize o canal ao qual sua organização se associa e selecione **Certificado de sincronização** na lista suspensa sob o cabeçalho **Ação**. Essa ação sincroniza os certificados em todos os peers no canal.

1. Recupere informações de configuração do arquivo `creds.json` disponíveis no painel **Visão geral** do Monitor de rede. Clique em  ** Perfil de Conexão ** e, em seguida, em  ** Download **.

  - Localize a URL do solicitador procurando **solicitadores**, localizado em `orderers > url`. Anote a URL que inicia com o nome da rede. A URL é semelhante ao exemplo a seguir:
    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Localize o nome de sua organização procurando **organizações**. Essa deve ser a mesma organização que usada para registrar seu peer remoto. É possível localizar o nome de sua organização junto com seu `mspid` associado. Anote o valor de `mspid`.

2. [Localize as informações sobre terminais de peers remotos](#remote-peer-retrieve-endpoint-info), desta vez, anotando as `Ports` listadas ao lado de `Ingress IP`.

  ```
  Ingress IP: <IP address>
  Ports: < Port List>
  ```
  {:codeblock}

  Anote as portas no intervalo de 30 K. Essas são as portas endereçáveis do contêiner de ferramentas do Fabric, que usaremos para configurar a variável de ambiente `PEERADDR`.

  ```
  Ingress IP: <9.46.126.89>
  Ports: <7051 7051/TCP 30162/NodePort7052 7052/TCP 30260/NodePort7053 7053/TCP 32051/NodePort>
  ```
  {:codeblock}

  Portanto, o `PEERADDR` que usamos foi construído com o valor especificado no arquivo `/etc/hosts` junto à porta ligada a `7051`, que é `30162` em nosso exemplo. Portanto, o valor resultante de `PEERADDR`
seria `remotepeer.blockchain.com:30162`.

3. Execute os comandos a seguir para configurar as variáveis de ambiente no contêiner de ferramentas do Fabric.

  ```
  export ORDERER_1=<ORDERER_URL>
  export CHANNEL=<CHANNEL_NAME>
  export CC_NAME=<CC_NAME>
  export ORGID=<ORGANIZATION_MSP_ID>
  export PEERADDR=<PEER_ADDR>
  ```
  {:codeblock}

  Substitua os campos por suas próprias informações.
    - Substitua `<ORDERER_URL>` pelo nome do host e a porta do solicitador do arquivo `creds.json`.
    - Substitua `<CHANNEL_NAME>` pelo nome do canal associado ao peer.
    - Substitua `<CC_NAME>` por qualquer nome que se refira ao chaincode.
    - Substitua `<ORGANIZATION_MSP_ID>` pelo nome da organização do arquivo `creds.json`.
    - Substitua `<PEER_ADDR>` pelo valor construído na etapa anterior.

  Por exemplo:

  ```
  export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
  export CHANNEL=defaultchannel
  export CC_NAME=mycc
  export ORGID=PeerOrg1
  export PEERADDR=remotepeer.blockchain.com:30162
  ```
  {:codeblock}

4. Busque o bloco genesis do canal para construir o livro-razão no peer. Primeiro, use `cd` para ir para o diretório-raiz e, em seguida, execute o comando para buscar o bloco genesis.

  ```
  cd /

  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/tls/cacert.pem --tls
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

5. Associe o peer ao canal usando o comando a seguir:

  ```
  CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  Quando isso funciona com sucesso, são exibidas informações semelhantes ao exemplo a seguir:

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Usando o contêiner de ferramentas do Fabric para instalar o chaincode no peer remoto
{: #icp-toolcontainer-install-cc}
Agora estamos prontos para instalar e instanciar o chaincode no peer remoto. Para estas instruções, instalaremos o chaincode de `fabcar` por meio do repositório `fabric-samples`. Faça download do chaincode de `fabric-samples` por meio de `github.com` usando os seguintes comandos:

  ```
  cd /
  mkdir /src
  cd /src
  git clone https://github.com/hyperledger/fabric-samples
  cd /
  ```
  {:codeblock}

Execute o comando da CLI do Peer a seguir para instalar o chaincode de `fabcar` no peer remoto.

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode install -n ${CC_NAME} -v v0 -p fabric-samples/chaincode/fabcar/go/
  ```
  {:codeblock}

  Quando esse comando for concluído com êxito, você verá algo semelhante a:

  ```
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
  2018-07-06 18:39:00.461 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
  2018-07-06 18:39:01.142 UTC [main] main -> INFO 003 Exiting.....
  ```

### Usando o contêiner de ferramentas do Fabric para instanciar o chaincode em um canal
{: #icp-toolcontainer-instantiate-cc}

Como apenas um peer precisa instanciar o chaincode em um canal, você não precisará usar seu peer remoto para instanciar o chaincode. Os peers hospedados no {{site.data.keyword.blockchainfull_notm}} Platform podem fazer isso. No entanto, se quiser que o peer remoto instancie o chaincode no canal, será possível executar o comando a seguir no contêiner de ferramentas do Fabric:

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/tls/cacert.pem -P ""
```
{:codeblock}

Quando esse comando for concluído com êxito, você verá algo semelhante a:

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## Visualizando Logs de Mesmo Nível Remotos no  {{site.data.keyword.cloud_notm}}  Privado
{: #remote-peer-log-icp}

É possível acessar os logs de peer remoto por meio do console do ICP e verificar os logs na interface do Kibana.

1. No console do ICP, clique no ícone **Menu** no canto superior esquerdo.
2. Na lista de menu, clique em **Cargas de trabalho** > **Liberações de Helm**.
3. Na tabela Liberações de Helm, clique no nome de sua liberação de helm.
4. Role para baixo até a seção **Pod** e clique no link **Visualizar logs** no final da linha da tabela. Os logs do peer remoto são abertos na interface do Kibana.

## Atualizando o chaincode

Com o tempo, é provável que você precisará modificar o chaincode que está em execução no peer remoto. Como o chaincode é instalado nos peers e instanciado no canal, é necessário atualizar o chaincode em todos os peers no canal.

Conclua as etapas a seguir para atualizar seu chaincode:

1. Para atualizar o chaincode em cada peer remoto, basta executar novamente o processo usado para instalar o chaincode nos peers, usando um aplicativo cliente ou um comando da CLI. Certifique-se de especificar o mesmo nome de chaincode usado originalmente. No entanto, desta vez, incremente a `Version` do chaincode. Todos os peers precisam usar o mesmo nome e versão do chaincode.

2. Depois de instalar o novo chaincode em todos os peers no canal, use o Monitor de rede ou o
comando [peer chaincode upgrade ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) para atualizar o canal para usar o novo chaincode.

Consulte a etapa dois destas [instruções](install_instantiate_chaincode.html#updating-a-chaincode) para obter mais informações sobre o uso do painel "Instalar código" do Monitor de rede para atualizar o chaincode no canal.

## Detecção de problemas
{: #icp-troubleshooting}

<!-- Commenting out per issue #228
### **Problem:** Error when installing helm release by using the helm install command
{: #icp-invalid-helm}

Using the `helm install` command to install the helm release fails with the error:

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

**Solution:**  

This error can occur when the helm CLI version on your local machine is
ahead of helm cli version on your server. To resolve the issue, downgrade the
version of your helm CLI by performing the following steps:

1.  Remove current version of helm client from `/usr/local/bin`.  Or you can
optionally just move binary file to a different directory.
```
rm /usr/local/bin/helm
```

2.  Follow these instructions to install helm client by using a script that is provided
by [helm](https://docs.helm.sh/using_helm/#from-script), which includes the following commands:

   * `curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh`
   * `chmod 700 get_helm.sh`
   * `./get_helm.sh --version v2.7.2`

   In the last step, ensure the version selected is compatible with ICP 2.1.0.3, `v2.7.2 ` is recommended.

3.  After the helm install completes, [login](#remote-peer-kubectl-configure}) to your ICP cluster
and run the following command:

 ```
 bx pr cluster-config <cluster_name>
 ```

 sample output:

 ```
 root@xcephu27:~/helmcli# bx pr cluster-config condev2
 Configuring kubectl: /root/.bluemix/plugins/icp/clusters/condev2/kube-config
 Property "clusters.condev2" unset.
 Property "users.condev2-user" unset.
 Property "contexts.condev2-context" unset.
 Cluster "condev2" set.
 User "condev2-user" set.
 Context "condev2-context" created.
 Switched to context "condev2-context".

 Cluster condev2 configured successfully.

 Configuring helm: /root/.helm
 Helm configured successfully

 OK
 ```

4. Add helm home directory from step three to your env:

 ```
 export HELM_HOME=/root/.helm
 ```

5. Check helm version:

  ```
  helm version --tls
  ```

  sample output:

  ```
  root@xcephu27:~/helmcli# helm version --tls
  Client: &version.Version{SemVer:"v2.7.2", GitCommit:"8478fb4fc723885b155c924d1c8c410b7a9444e6", GitTreeState:"clean"}
  Server: &version.Version{SemVer:"v2.7.3+icp", GitCommit:"27442e4cfd324d8f82f935fe0b7b492994d4c289", GitTreeState:"dirty"}
  ```

6. Rerun your `helm install` command.

-->

### **Problema:** erro ao efetuar login no contêiner do peer
{: #icp-bash-error}

Esse problema ocorre ao usar a ferramenta kubectl para execução em seu peer executando o seguinte comando:
```
kubectl exec -it -n <namespace> <PEER_CONTAINER_NAME> bash
```
falha e produz uma mensagem de erro semelhante ao exemplo a seguir:
```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "open /dev/pts/4294967296: no such file or directory": unknown
```

** Solução: **  

Esse erro ocorre quando, em sistemas big endian, como o IBM System Z, duas sessões bash do docker são abertas no mesmo contêiner do docker. A segunda sessão bash pode não ser capaz de se conectar ao contêiner em execução. Para resolver esse problema, [reinicie o contêiner de peer](#remote-peer-restart) e tente novamente o comando `kubectl exec` após a reinicialização no mesmo nível.
