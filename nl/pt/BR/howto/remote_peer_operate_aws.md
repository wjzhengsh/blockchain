---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Operando peers no AWS
{: #remote-peer-operate-aws}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Depois de configurar peers do {{site.data.keyword.blockchainfull}} Platform no AWS, é necessário concluir várias etapas operacionais antes que seu peer possa emitir transações para consultar e chamar o livro-razão da rede de blockchain. As etapas envolvem incluir sua organização em um canal, associar seu peer ao canal, instalar o chaincode em seu peer, instanciar o chaincode no canal e conectar aplicativos a seu peer. É possível usar os [SDKs do Fabric](#aws-peer-operate-with-sdk) ou a [linha de comandos](#aws-peer-cli-operate) para concluir essas etapas operacionais. Os SDKs do Fabric são o caminho recomendado, embora as instruções assumam familiaridade com a operação do SDK.

**Nota**: um peer do {{site.data.keyword.blockchainfull_notm}} Platform no AWS não tem acesso à funcionalidade completa ou suporte de peers que estão hospedados no {{site.data.keyword.blockchainfull_notm}} Platform. Como resultado, não é possível usar o Monitor de Rede para operar seu peer. Antes de iniciar a execução de peers no AWS, assegure-se de revisar as [considerações](/docs/services/blockchain/howto/remote_peer.html#remote-peer-limitations).

## Usando SDKs do Fabric para operar o peer
{: #aws-peer-operate-with-sdk}

Os SDKs do Hyperledger Fabric fornecem um conjunto poderoso de APIs que permitem que os aplicativos interajam e operem redes de blockchain. É possível localizar a lista mais recente de idiomas suportados e a lista completa de APIs disponíveis dentro dos SDKs do Hyperledger Fabric na [documentação da comunidade do Hyperledger Fabric SDK ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/getting_started.html#hyperledger-fabric-sdks "Documentação da comunidade do Hyperledger Fabric SDK"). É possível usar os SDKs do Fabric para associar seu peer a um canal no {{site.data.keyword.blockchainfull_notm}} Platform, instalar um chaincode em seu peer e instanciar o chaincode em um canal.

As instruções a seguir usam o [SDK do Node Fabric ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/ "Hyperledger Fabric SDK para jode.js") para operar o peer e assumir a familiaridade prévia com o SDK. É possível usar o [tutorial de desenvolvimento de aplicativos](../v10_application.html) para aprender a usar o Node SDK antes de iniciar e como um guia para desenvolver aplicativos com seu peer uma vez que esteja pronto para chamar e consultar o chaincode.

O Peer do {{site.data.keyword.blockchainfull_notm}} Platform na Iniciação Rápida do AWS cria dois peers para alta disponibilidade. Portanto, é necessário seguir as etapas de operações uma vez para cada peer. Quando estiver pronto para consultar e chamar o chaincode de seu aplicativo, o SDK se conectará a ambos os peers para assegurar que seus [aplicativos estejam altamente disponíveis](/docs/services/blockchain/v10_application.html#ha-app).

### Instalando o Node SDK

É possível usar o NPM para instalar o [Node SDK ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/ "Hyperledger Fabric SDK for jode.js"):
```
npm install fabric-client@1.2
```
{:codeblock}

É recomendado que você use a versão 1.2 do SDK do Node.

### Preparando o SDK para trabalhar com o peer
{: #remote-peer-node-sdk}

Antes de usar o SDK para operar o peer, é necessário gerar os certificados necessários (inscrição) que permitirão que seu aplicativo se comunique com sua rede no {{site.data.keyword.blockchainfull_notm}} Platform e seu peer. Siga as etapas para [se inscrever com o SDK](/docs/services/blockchain/v10_application.html#enroll-app-sdk) usando sua identidade de **administrador**. O tutorial [Desenvolvendo aplicativos](/docs/services/blockchain/v10_application.html) também é inscrito como o **administrador**, portanto, não é necessário modificar o código de amostra.

### Fazendo upload de um signcert para o IBM Blockchain Platform
{: #remote-peer-upload-SDK}

É necessário fazer upload de seu certificado de assinatura SDK para a rede no {{site.data.keyword.blockchainfull_notm}} Platform para que outros membros possam reconhecer sua assinatura digital.

- É possível localizar seu certificado de assinatura no diretório que o SDK gerou seu material de criptografia, no arquivo denominado administrador. Copie o certificado dentro das aspas depois do campo `certificate`, iniciando com `-----BEGIN CERTIFICATE-----` e terminando com `-----END CERTIFICATE-----`. É possível usar a CLI para converter o certificado no formato PEM, emitindo o comando `echo -e "<CERT>" > admin.pem`. Em seguida, é possível colar o conteúdo do certificado em sua rede de blockchain usando o Monitor de Rede. Efetue login na rede no {{site.data.keyword.blockchainfull_notm}} Platform. Na tela "Membros" do Monitor de rede, clique em **Certificados** > **Incluir certificado**. Especifique qualquer nome para o seu certificado e cole o conteúdo no campo **Certificado**. Clique em **Reiniciar** quando for perguntado se você deseja reiniciar os peers. Essa ação deve ser executada antes que sua organização se associe ao canal.

### Fazendo upload de um signcert para o peer
{: #remote-peer-upload-signcert}

Também é necessário fazer upload do certificado de assinatura do SDK para o peer remoto e reiniciá-lo. É necessário instalar o mesmo certificado de assinatura que aquele que você [transferiu por upload para o IBM Blockchain Platform](#remote-peer-upload-SDK) dentro do contêiner de peer remoto.

Efetue SSH em sua instância de VPC selecionando a instância no console do AWS (clique em **Serviços > EC2 > Instâncias**) e, em seguida, clicando no botão Conectar. Siga as instruções do AWS para emitir o comando ssh.

Execute os comandos a seguir de dentro do contêiner do peer para fazer upload de seu certificado.  
```
cd /etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/
echo -e "<CERT.PEM>" > cert2.pem
```
{:codeblock}

- Substitua `<PEER_ENROLL_ID>` pelo ID de inscrição que está especificado no modelo de Iniciação Rápida e associado com essa instância do peer remoto.  
- Substitua `<CERT.PEM>` por seu signcert, que começa com `-----BEGIN CERTIFICATE-----` e termina com `-----END CERTIFICATE-----`.    

  **Nota:** se o arquivo `cert.pem` existir, não o sobrescreva, mas crie um novo arquivo, por exemplo, `cert2.pem`.

Como você inclui um novo certificado, é necessário reiniciar o contêiner para que o peer selecione o certificado. Siga estas [instruções](#remote-peer-aws-restart) para reiniciar seu peer.

### Passando o certificado TLS de seu peer para o SDK
{: #aws-remote-peer-download-tlscert}

É necessário copiar o conteúdo do `cacert.pem` do TLS do contêiner de peer para seu aplicativo para autenticar a comunicação do SDK. Execute o comando a seguir no contêiner de CLI do Peer. Substitua `<PEER_ENROLL_ID>` pelo nome da pilha do peer remoto que você especificou no modelo de Iniciação Rápida. (Duas instâncias VPC de rechamada são criadas.)
```
cat /etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt
```
{:codeblock}

Copie o texto inteiro do arquivo `ca.crt` para sua área de transferência, que inicia com o primeiro `-----BEGIN CERTIFICATE-----` e termina com o último `-----END CERTIFICATE-----`. Salve esse certificado em seu sistema de arquivos local no formato PEM. Em seguida, é possível importar o certificado TLS para seu aplicativo usando um comando de arquivo de leitura simples.
```
var caCert = fs.readFileSync(path.join(__dirname, './ca.pem'));
```
{:codeblock}

### Fornecendo informações sobre terminais de peer para o SDK
{: #remote-peer-SDK-endpoints}

Para recuperar as informações de terminal de seu peer, localize a instância do peer no console do AWS. Anote o valor do `AWS EC2 dashboard Public DNS (IPv4)` para a instância EC2 e forneça-o ao SDK, declarando uma nova variável peer ou atualizando seu perfil de conexão. Concatene o endereço DNS público com a porta `7051`. O exemplo a seguir define o peer em sua rede de malha e o transmite para o certificado TLS que você importou.

```
var peer = fabric_client.newPeer('grpcs://<AWS_EC2_dashboard_Public_DNS>:7051', { pem:  Buffer.from(caCert).toString()});
```
{:codeblock}

**Nota:** como o peer é remoto, outras organizações na rede do {{site.data.keyword.blockchainfull_notm}} Platform não poderão localizar as informações de terminal de seu peer em seu perfil de conexão. Se outras organizações precisarem enviar uma transação ao seu peer para ser endossada, será necessário fornecer os certificados IP e TLS públicos em uma operação dentro e fora da banda.

### Usando o SDK para se associar a um canal
{: #remote-peer-join-channel-sdk}

Como um membro da rede de blockchain, sua organização precisa ser incluída em um canal na rede antes de poder associar seu peer ao canal.

  - É possível iniciar um novo canal para o peer. Como o inicializador de canais, é possível incluir automaticamente a sua organização durante a [criação do canal](/docs/services/blockchain/howto/create_channel.html#creating-a-channel).

  - Outro membro da rede de blockchain também pode incluir sua organização em um canal existente usando uma [atualização de canal](/docs/services/blockchain/howto/create_channel.html#updating-a-channel).

    Após sua organização ser incluída em um canal, será necessário incluir o certificado de assinatura do seu parceiro no canal para que outros membros possam verificar sua assinatura digital durante as transações. O peer faz upload de seu certificado de assinatura durante a instalação, de modo que você precise somente sincronizar o certificado para o canal. Na tela "Canais" do Monitor de rede, localize o canal ao qual sua organização se associa e selecione **Certificado de sincronização** na lista suspensa sob o cabeçalho **Ação**. Essa ação sincroniza os certificados em todos os peers no canal. Pode ser necessário aguardar alguns minutos para que a sincronização do canal possa ser concluída antes de emitir comandos join channel.

Depois que sua organização fizer parte do canal, siga as instruções de [Associando-se a um canal](/docs/services/blockchain/v10_application.html#join-channel-sdk). É necessário fornecer a URL do serviço de ordenação e o nome do canal.

### Usando o SDK para instalar o chaincode no peer
{: #remote-peer-install-cc-sdk}

Siga as instruções para usar o SDK para [instalar um chaincode](/docs/services/blockchain/v10_application.html#install-cc-sdk) em seu peer.

### Usando o SDK para instanciar o chaincode no canal
{: #remote-peer-instantiate-cc-sdk}

Apenas um membro do canal precisa instanciar ou atualizar o chaincode. Portanto, qualquer membro de rede do canal com peers no {{site.data.keyword.blockchainfull_notm}} Platform pode usar o Monitor de rede para instanciar o chaincode e especificar políticas de endosso. No entanto, se você desejar usar o peer para instanciar o chaincode em um canal, será possível usar o SDK e seguir as instruções para [instanciar um chaincode](/docs/services/blockchain/v10_application.html#instantiate-cc-sdk).


## Usando a CLI para operar o peer
{: #aws-peer-cli-operate}

Também é possível operar seu peer a partir da linha de comandos usando o cliente Fabric CA e o contêiner de ferramentas do Fabric. Nessas instruções, primeiro gere os certificados necessários usando o cliente Fabric CA. Em seguida, usaremos o contêiner de ferramentas do Fabric para operar o peer associando-o a um canal, instalando um chaincode e, em seguida, instanciando o chaincode em um canal.

### Inscrição usando o cliente Fabric CA
{: #peer-client-enroll}

A primeira etapa é gerar os certificados necessários (inscrição) usando o Fabric CA Client. Primeiro, é necessário instalar o cliente Fabric CA. Faça download dos [binários fabric-ca v1.2.0 para sua plataforma](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) para sua máquina local, extraia-os e mova-os para uma pasta como `$HOME/fabric-ca-remote/`.

1.  Prepare seu ambiente para usar o Fabric CA Client. Mude para o diretório no qual os binários do cliente foram movidos para que seja possível referenciá-lo diretamente em seus comandos.
    ```
    cd $HOME/fabric-ca-remote/
    ```
    {:codeblock}

2.  Configure o caminho no qual o cliente pode criar suas chaves.  Se você não tiver executado o comando `enroll` antes, a pasta `msp` e o arquivo `.yaml` não existirão. Se você tiver executado o comando `enroll` antes, será importante excluir qualquer material de configuração das tentativas anteriores de inscrição usando os comandos `rm` abaixo:
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-remote
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```
    {:codeblock}

3.  Será necessário recuperar as informações de terminal de sua Autoridade de Certificação no {{site.data.keyword.blockchainfull_notm}} Platform. Na tela "Visão geral" do Monitor de rede, clique no botão **Perfil de conexão** e abra o arquivo JSON que contém as credenciais de rede. Role para baixo até a seção `certificateAuthorities` e anote as informações abaixo.
    ```
    Hostname and port for CA: url (without the https prefix)
    Admin user ID: enrollId
    Admin password: enrollSecret
    CA Name: caName
    ```

4.  Também será necessário recuperar um certificado TLS do {{site.data.keyword.blockchainfull_notm}} Platform para se comunicar com a CA. As credenciais de rede acima incluem o certificado necessário. Na seção `certificateAuthorities` do arquivo de Perfil de conexão, copie o certificado que segue `"pem:"`, que começa com `-----BEGIN CERTIFICATE-----` e termina com `-----END CERTIFICATE-----`. ** Não inclua as aspas **.

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

Antes de poder operar o peer, precisamos fazer algum gerenciamento dos certificados em nossa máquina local e fazer upload de alguns dos certificados que o cliente Fabric CA gerou para o {{site.data.keyword.blockchainfull_notm}} Platform e seu peer. Também será necessário fazer download de certificados TLS do Platform e do peer. Se desejar saber mais sobre os certificados com os quais você trabalhará e as tarefas que serão executadas, visite [Gerenciando certificados no {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html).

Na máquina local, abra um terminal de comando e navegue para o diretório para o qual moveu os binários do Fabric-CA-Client e armazenou a pasta MSP.

1. Copie o arquivo `cert.pem` da pasta `signcerts` para uma nova pasta `admincerts`.
   ```
   cd /$FABRIC_CA_CLIENT_HOME/msp/
   mkdir admincerts
   cd admincerts/
   cp ../signcerts/cert.pem  .
   ```
   {:codeblock}

2. Copie o certificado TLS dos solicitadores do {{site.data.keyword.blockchainfull_notm}} Platform para a pasta MSP. Seu perfil de conexões contém o certificado necessário. Na tela "Visão geral" de seu Monitor de Rede, clique no botão **Perfil de conexão** e abra o arquivo JSON que contém as credenciais de rede. Navegue para a seção `orderers`. Copie o certificado que segue `"pem:"`, começando com `-----BEGIN CERTIFICATE-----` e terminando com `-----END CERTIFICATE-----`. Não inclua as aspas.

    Em sua janela do terminal, execute os comandos a seguir:
    ```
    cd /$FABRIC_CA_CLIENT_HOME/msp
    echo -e "<paste in the certificate here>" > orderer_tlscacert.pem
    ```
    {:codeblock}

3. Também é necessário copiar o certificado TLS de seu peer do contêiner de peer no AWS para sua máquina local.

    - [Siga essas instruções](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-test) para efetuar login no contêiner de seu peer e execute o comando a seguir, substituindo <PEER_ENROLL_ID> pelo nome da pilha do peer que você especificou no modelo de Iniciação Rápida, acompanhado por seu número. (Lembre-se de que duas instâncias do VPC são criadas).
      ```
      cat /etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt
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

      **Nota:** por padrão, o modelo de Iniciação Rápida do AWS cria duas instâncias de peer em duas zonas de disponibilidade diferentes. Se você já tiver concluído essas etapas para um desses peers, quando executar essas etapas para a segunda instância, o arquivo cacert.pem já existirá. Vá em frente e substitua-o pelo certificado do segundo peer.

4. Para fornecer à sua CLI a autorização para instalar o chaincode no peer, faça upload do signCert gerado pelo cliente Fabric CA para a pasta do administrador do peer e reinicie o peer. Portanto, copie o certificado `admincert/cert.pem` de sua máquina local para o diretório `/etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/` no contêiner do peer como `cert2.pem`.

    - Em sua máquina local, execute os comandos a seguir:

      ```
      cd /$FABRIC_CA_CLIENT_HOME/msp/admincerts
      cat cert.pem
      ```
      {:codeblock}

    - Copie o texto do arquivo `cert.pem` para a área de transferência iniciando com `-----BEGIN CERTIFICATE-----` e terminando com `-----END CERTIFICATE-----`.

    - Alterne novamente para o contêiner Peer e execute os comandos a seguir para incluir o cert.pem nos certificados de peer. Substitua o texto `<CERT.PEM>`  com o texto da área de transferência.

      ```
      cd /etc/hyperledger/<PEER_ENROLL_ID>/msp/admincerts/
      echo -e "<CERT.PEM>" > cert2.pem
      ```
      {:codeblock}

      **Nota:** já existe um arquivo cert.pem nesse diretório. Não o sobrescreva.

    - Como incluímos um novo certificado, precisamos reiniciar o contêiner para que o peer selecione o certificado. Siga essas instruções para [reiniciar o contêiner de peer](#remote-peer-aws-restart).

5. Faça upload do mesmo certificado `admincert/cert.pem` de sua máquina local para o {{site.data.keyword.blockchainfull_notm}} Platform na ordem para uma CLI ou aplicativo remoto executar operações de canal, como buscar o bloco genesis do canal e instanciar o chaincode.
    1. Na máquina local, execute cat do arquivo `/$FABRIC_CA_CLIENT_HOME/msp/admincerts/cert.pem` e, em seguida, copie-o para a área de transferência.
    2. Entre no Monitor de rede de sua rede no {{site.data.keyword.blockchainfull_notm}} Platform.
    3. Clique em **Membros** no navegador esquerdo e clique na guia **Certificados**.
    4. Clique no botão  ** Incluir Certificado ** .
    5. Na janela **Incluir certificado**, forneça um nome para o certificado, como `fabrictools.pem`, cole o certificado recém-copiado na área de transferência e clique em **Enviar**.
    6. Será perguntado se você deseja reiniciar os peers. Clique em **Reiniciar**.
    7. Clique em **Canais** no navegador à esquerda e localize o canal ao qual o peer se associará.
    8. Clique nos três pontos sob o cabeçalho **Ações** e clique em **Sincronizar certificado**. Na janela **Sincronizar certificado**, clique em **Enviar**.

    **Nota**: é importante sincronizar o certificado de canal antes que o peer se associe ao canal ou instancie o chaincode no canal. Poderá ser necessário aguardar alguns minutos para que a sincronização do canal possa ser concluída antes de emitir um canal de associação ou de instanciar comandos do chaincode.   

### Configurando o Contêiner de Ferramentas do Fabric
{: #setup-fabric-cli}

Depois de mover todos os nossos certificados para o local necessário, é possível instalar e usar o contêiner de ferramentas do Fabric a partir do Docker. Assegure-se de executar esses comandos a partir do diretório no qual você armazenou sua pasta MSP.

1.  Faça download da imagem do Docker das ferramentas do Fabric com o comando a seguir:
    ```
    docker pull ibmblockchain/fabric-tools:1.2.0
    ```
    {:codeblock}

2.  Execute os comandos a seguir para iniciar o contêiner de ferramentas do Fabric.
    ```
    docker network create blockchain.com
    docker run -ti --network blockchain.com -v ${PWD}:/mnt ibmblockchain/fabric-tools:1.2.0
    ```
    {:codeblock}

    Esse comando montará o diretório do MSP dentro de seu contêiner de ferramentas.

<!--
3.  The peer address must resolve to a domain name with a suffix of `blockchain.com`. You can do this either via your DNS or modify your /etc/hosts file. For purposes of these instructions, we will modify the `/etc/hosts` file in the Fabric tools container. To retrieve the endpoint information of your peer, locate your peer instance in the AWS console. Make note of the value of the AWS `IPv4 Public IP` for the EC2 instance.

    ```
    echo -e "<AWS_IPv4_PUBLIC_IP> remotepeer.blockchain.com" >> /etc/hosts
    ```
    {:codeblock}
-->

### Usando a CLI de ferramentas do Fabric para associar o peer ao canal
{: #fabric-cli-join-peer-to-channel}

Antes de poder executar os comandos da CLI para associar o peer a um canal, sua organização precisa ser incluída em um canal na rede.

  - É possível iniciar um novo canal para o peer. Como o inicializador de canais, é possível incluir automaticamente a sua organização durante a [criação do canal](/docs/services/blockchain/howto/create_channel.html#creating-a-channel).

  - Outro membro da rede de blockchain também pode incluir sua organização em um canal existente usando uma [atualização de canal](/docs/services/blockchain/howto/create_channel.html#updating-a-channel).

    Após sua organização ser incluída em um canal, será necessário incluir o certificado de assinatura do seu parceiro no canal para que outros membros possam verificar sua assinatura digital durante as transações. O peer faz upload de seu certificado de assinatura durante a instalação, de modo que você precise somente sincronizar o certificado para o canal. Na tela "Canais" do Monitor de rede, localize o canal ao qual sua organização se associa e selecione **Certificado de sincronização** na lista suspensa sob o cabeçalho **Ação**. Essa ação sincroniza os certificados em todos os peers no canal.

1. Recupere as informações de configuração de seu `Connection Profile` disponível no painel Visão geral do Monitor de Rede. Clique em **Perfil de conexão** e, em seguida, em **Download**.

  - Localize a URL do solicitador procurando **solicitadores**, localizado em `orderers > url`. Anote a URL que inicia com o nome da rede. A URL é semelhante ao exemplo a seguir:

    ```
    ash-zbc07b.4.secure.blockchain.ibm.com:21239
    ```

  - Localize o nome de sua organização procurando **organizações**. Essa deve ser a mesma organização que você usa para registrar seu peer. É possível localizar o nome de sua organização junto com seu `mspid` associado. Anote o valor de `mspid`.

2. Localize as informações de terminal de seu peer VPC do AWS (o endereço IPv4 do DNS Público) no DNS público do painel do AWS EC2 (IPv4). Será algo parecido com:

  ```
  ec2-10-221-8-71.us-north-2.compute.amazonaws.com
  ```

  Use essas informações para construir a variável de ambiente `PEERADDR` concatenando o endereço com a porta '7051', de modo que o
valor de `PEERADDR` seja semelhante a:

  ```
  ec2-10-221-8-71.us-north-2.compute.amazonaws.com:7051
  ```
3. Execute os comandos a seguir para configurar as variáveis de ambiente dentro do contêiner de ferramentas do Fabric.

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
      - Substitua `<PEER_ADDR>` pelo valor que você construiu na etapa anterior.  

  Por exemplo:

    ```
    export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
    export CHANNEL=defaultchannel
    export CC_NAME=mycc
    export ORGID=PeerOrg1
    export PEERADDR=ec2-10-221-8-71.us-north-2.compute.amazonaws.com:7051
    ```
    {:codeblock}

3. Busque o bloco genesis do canal para construir o livro-razão no peer. Primeiro, `cd` para o diretório raiz e, em seguida, execute o comando para buscar o bloco genesis.

  ```
  cd /

  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/msp/orderer_tlscacert.pem --tls
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

  Quando o bloco genesis tiver sido incluído com êxito, você deverá ver algo semelhante ao exemplo a seguir:
  ```
  defaultchannel_0.block
  ```
  <!-- removing the code block since this is a result and not an executable command
  {:codeblock}
  -->
4. Agora associe o peer ao canal, transmitindo o bloco genesis usando o comando a seguir:

  ```
  GOPATH=/ CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer channel join -b ${CHANNEL}_0.block
  ```
  {:codeblock}

  Quando isso funcionar com êxito, você deverá ver algo semelhante a:

  ```
  2018-07-06 18:32:09.509 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
  2018-07-06 18:32:09.992 UTC [channelCmd] executeJoin -> INFO 002 Successfully submitted proposal to join channel
  2018-07-06 18:32:09.992 UTC [main] main -> INFO 003 Exiting.....
  ```

### Usando o contêiner de ferramentas do Fabric para instalar o chaincode no peer
{: #aws-toolcontainer-install-cc}

Agora, estamos prontos para instalar e instanciar o chaincode no peer. Para estas instruções, instalaremos o chaincode de `fabcar` por meio do repositório `fabric-samples`. Faça download do chaincode `fabric-samples` do GitHub usando os comandos a seguir:

  ```
  cd /
  mkdir /src
  cd /src
  git clone https://github.com/hyperledger/fabric-samples
  ```
  {:codeblock}

Execute o comando da CLI de Peer a seguir para instalar o chaincode `fabcar` no peer.

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
{: #aws-toolcontainer-instantiate-cc}

Como somente um peer tem que instanciar o chaincode em um canal, você não tem que usar o seu peer para instanciar o chaincode. Os peers hospedados no {{site.data.keyword.blockchainfull_notm}} Platform podem fazer isso. No entanto, se desejar que o peer instancie o chaincode no canal, será possível executar o comando a seguir no contêiner de ferramentas do Fabric:

```
CORE_PEER_TLS_ROOTCERT_FILE=/mnt/msp/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/msp/ GOPATH=/ peer chaincode instantiate -o ${ORDERER_1} -C ${CHANNEL} -n ${CC_NAME} -v v0 -c '{"Args":[""]}' --tls --cafile /mnt/msp/orderer_tlscacert.pem -P ""
```
{:codeblock}

Quando esse comando for concluído com êxito, você verá algo semelhante a:

```
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 001 Using default escc
2018-07-06 18:43:15.066 UTC [chaincodeCmd] checkChaincodeCmdParams -> INFO 002 Using default vscc
2018-07-06 18:43:50.227 UTC [main] main -> INFO 003 Exiting.....
```


## Reinicie um peer que esteja em execução no AWS
{: #remote-peer-aws-restart}

É possível iniciar, parar ou reiniciar seu peer no ambiente em que você implementa o peer. Sempre que um certificado é incluído no peer, o peer precisa ser reiniciado para que o novo certificado seja reconhecido. Há várias maneiras de fazer isso:

- No console do AWS, reinicialize a instância de VPC do peer.
- De dentro de seu contêiner de peer, execute o comando a seguir:

 ```
 docker restart peer
 ```
 {:codeblock}  

Além disso, é possível usar a [solicitação HEAD](/docs/services/blockchain/howto/monitor_network.html#monitor-nodes) para verificar a disponibilidade de seu peer.

## Visualizando logs de peer

Efetue SSH na instância do peer do VPC do AWS e, em seguida, execute o comando a seguir para visualizar os logs do peer:

```
docker logs peer
```
{:codeblock}

## Atualizando o chaincode

Ao longo do tempo, é provável que seja necessário modificar o chaincode que está em execução no peer. Como o chaincode é instalado nos peers e instanciado no canal, é necessário atualizar o chaincode em todos os peers no canal.

Conclua as etapas a seguir para atualizar seu chaincode:

1. Para atualizar o chaincode em cada peer no AWS, simplesmente execute novamente o processo usado para instalar o chaincode nos peers usando um aplicativo cliente ou um comando da CLI. Certifique-se de especificar o mesmo nome de chaincode usado originalmente. No entanto, esse tempo incrementa o número de `Version` do chaincode.

2. Depois de instalar o novo chaincode em todos os peers no canal, use o Monitor de rede ou o
comando [peer chaincode upgrade ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/commands/peerchaincode.html#peer-chaincode-upgrade) para atualizar o canal para usar o novo chaincode.

Veja a etapa dois dessas [instruções](/docs/services/blockchain/howto/install_instantiate_chaincode.html#updating-a-chaincode) para obter informações adicionais sobre a utilização do painel Código de Instalação do Monitor de Rede para atualizar o chaincode no canal.

## Detecção de problemas

### **Problema:** o peer remoto não é capaz de se conectar à rede de blockchain

A criação de pilha é concluída com êxito, mas os logs do Docker contêm o erro:

```
[main] main -> ERRO 001 Cannot run peer because error when setting up MSP of type bccsp from directory /etc/hyperledger/awspeer1/msp: Setup error: nil conf reference
```

** Solução: **  
Esse erro pode ser causado por negligência ao especificar uma porta no CAUrl quando o modelo de Iniciação Rápida foi implementado.
O CAUrl deve ser semelhante a `https://<network>-org1-ca.stage.blockchain.ibm.com:31011`.
Reimplemente o modelo de Iniciação Rápida, tendo cuidado de especificar o valor apropriado para o CAUrl.

### **Problema:** a instanciação de chaincode falha com erro

A instanciação de chaincode falha com:
```
Error: Error endorsing chaincode: rpc error: code = Unknown desc = chaincode error (status: 500, message: instantiation policy violation: signature set did not satisfy policy)
```

**Solução:**   
Assegure-se de que, após o certificado do administrador ter sido transferido por upload para o Monitor de Rede, os certificados sejam, então, sincronizados no canal. Consulte a etapa cinco dessas [instruções](#manage-certs) para obter mais informações. Esteja ciente de que é importante sincronizar o certificado do canal antes que o peer se associe ao canal.
