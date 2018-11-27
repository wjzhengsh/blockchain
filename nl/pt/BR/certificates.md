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


# Gerenciando Certificados em {{site.data.keyword.blockchainfull_notm}} Plataforma
{: #certificates}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


O {{site.data.keyword.blockchainfull}} Platform baseia-se no Hyperledger Fabric e constrói redes de blockchain permitidas. Os participantes são conhecidos como membros da rede e suas atividades e acesso a recursos de rede são verificados continuamente. A identidade de um participante é fornecida na forma de um certificado digital x509 confiável. A verificação é fornecida por uma infraestrutura de chave pública subjacente e operações de assinatura/verificação que protegem transações e gerenciamento dentro da rede.
{:shortdesc}

O {{site.data.keyword.blockchainfull_notm}} Platform gerencia a maioria das operações de certificado sem que os usuários precisem manipular seus certificados. No entanto, há momentos em que você terá que gerenciar os certificados que permitem a comunicação com a rede, como desenvolver aplicativos ou expandir sua rede com um peer remoto. O texto a seguir é um guia curto para sua Autoridade de certificação e a infraestrutura de certificado subjacente. Este tutorial pode ajudá-lo a entender os certificados com os quais você estará trabalhando e as tarefas que precisam ser executadas.

## Autoridades de Certificação
{: #network-ca}

As autoridades de certificação (CAs) fornecem identidade na rede. Uma CA pode ser considerada um tabelião público confiável que age como uma âncora de confiança entre múltiplas partes. Todas as entidades na rede recebem um certificado assinado por uma autoridade de certificação raiz que encapsula sua identidade digital. Esse certificado é a raiz de confiança para todas as operações de assinatura e verificação executadas na rede. Para obter mais detalhes sobre como as autoridades de certificação são usadas para estabelecer identidade, consulte a [documentação do Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/identity/identity.html).

Cada membro da rede possui sua própria CA, que recebe um certificado da autoridade de certificação raiz da rede. Essa ligação à CA raiz estabelece a âncora de confiança para sua organização. A CA de sua organização assina solicitações para todas as entidades que sua organização possui, como seu administrador, peers ou aplicativos. Se desejar incluir um peer remoto ou um novo aplicativo na rede, será necessário registrar a nova identidade com sua Autoridade de certificação (registro). Em seguida, a CA poderá fornecer a nova entidade com os certificados que precisará para interagir com a rede (inscrição).

### Registro utilizando o Monitor de Rede
{: #ca-panel}

É possível usar o Monitor de rede do {{site.data.keyword.blockchainfull_notm}} Platform para visualizar as identidades registradas com sua CA e para incluir novas. Navegue para o painel "Autoridade de certificação" do Monitor de rede. Esse painel exibe todas as identidades que foram registradas com sua CA, incluindo o administrador da organização, os peers e os aplicativos clientes. Para registrar uma nova identidade em sua organização, clique no botão **Incluir usuário** na parte superior do painel. Uma janela pop-up é aberta, exibindo os campos a seguir que são necessários para registrar uma nova identidade.
  - **ID:** este será o nome da sua nova identidade, por vezes referido como o seu `enroll ID`. **Salve esse valor** para quando configurar um peer remoto ou inscrever-se para um novo aplicativo.
  - **Segredo:** essa será a senha para sua identidade, às vezes referida como `enroll Secret`  **Salve esse valor.** para quando você configura um peer remoto ou inscreve um novo aplicativo.  
  - **Tipo:** selecione o tipo de identidade que deseja registrar, no peer ou no aplicativo cliente.
  - **Afiliação:** essa será a afiliação dentro de sua organização, tal como
`org1`, por exemplo, à qual a identidade pertencerá.
  - **Máximo de inscrições:** é possível usar esse campo para limitar o número de vezes que é possível inscrever-se ou gerar certificados usando essa identidade. Se você deixar o campo em branco, o valor padrão será um número ilimitado de inscrições.

Será possível usar esse painel para registrar uma nova identidade de peer se você estiver implementando um [peer remoto](howto/remote_peer.html). Como alternativa, será possível registrar um cliente se você estiver desenvolvendo um aplicativo que possa enviar transações para sua rede. Visite o [tutorial de desenvolvimento de aplicativos](v10_application.html) para saber mais sobre o uso de SDKs do Fabric com a plataforma.

### Gerando Certificados do Lado do Cliente (Inscrição)
{: #enrollment}
Antes de conectar um cliente de terceiro ao {{site.data.keyword.blockchainfull_notm}} Platform, você precisará ser autenticado. O processo de geração de certificados necessários, sua chave privada e seu certificado público (também conhecido como seu certificado de inscrição ou signCert) é chamado de inscrição. Esses certificados serão necessários a qualquer momento em que seu cliente se comunicar com a rede. Qualquer cliente que enviar chamadas para a rede precisará assinar cargas úteis usando uma chave privada e anexar um certificado x509 devidamente assinado.

Visite o [tutorial de desenvolvimento de aplicativos](v10_application.html) para saber como [inscrever-se usando o Fabric Node SDK](v10_application.html). Inscrever-se com o SDK gera um signCert, uma chave pública que foi usada para criar o signCert e sua chave privada.

Também é possível gerar certificados por meio da [linha de comandos](#enroll-register-caclient) usando o Fabric CA Client. O Fabric CA Client retorna um conjunto mais completo de certificados dentro da pasta Membership Service Provider (MSP). Essa pasta contém o certificado raiz assinado pela CA, os certificados intermediários vinculados à sua afiliação, uma chave privada e seu signCert. Para saber mais sobre os MSPs e o que essa pasta contém, consulte a seção [Membership Service Providers](#msp) abaixo.

### Fazendo Upload de Certificados de Assinatura para  {{site.data.keyword.blockchainfull_notm}}  Plataforma
{: #upload-certs}

Um aplicativo requer apenas um signCert válido para enviar transações para a rede. No entanto, se um cliente desejar operar a rede, instalando o chaincode em peers ou associando peers a canais, por exemplo, o cliente precisará ser reconhecido como um administrador. Cada componente reconhece um conjunto de certificados signCerts pertencentes a um administrador. Se você precisar operar sua rede por meio de um cliente, será necessário fazer upload de seu signCert e incluí-lo na lista de certificados de administrador. É possível fazer isso na plataforma fazendo upload de seu signCert na guia **Certificados** do [painel "Visão geral"](v10_dashboard.html#members) de seu Monitor de rede. Sincronize esse certificado com seus peers pressionando o botão de reinicialização que aparece após o upload. Posteriormente, seu cliente poderá ser reconhecido como um administrador da rede.

<!----
When you add an organization to a channel, this will upload the organization signCert to the channel MSP. Synching the Sign Certs does something.
--->

## Usando Certificados TLS
{: #tls}

A [Segurança da Camada de Transporte ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm) (TLS) é integrada ao modelo de confiança do Hyperledger Fabric. Todos os componentes no {{site.data.keyword.blockchainfull_notm}} Platform usam TLS para autenticação e comunicação entre si. Portanto, é necessário anexar um certificado TLS emitido pela plataforma às suas chamadas para validar e criptografar sua comunicação. Os outros certificados discutidos neste tutorial protegem a sua capacidade de transacionar e gerenciar a rede. Os certificados TLS são usados para proteger suas chamadas para a rede.

Os certificados TLS são emitidos publicamente pela plataforma e são iguais para todos os componentes de rede. É possível fazer download dos certificados TLS com os links a seguir, dependendo de seu plano de associação e do local da nuvem. Também é possível localizar os certificados TLS no [perfil de credenciais](v10_dashboard.html#enterprise-connection-profile "perfil de credenciais"). Esse certificado pode residir em qualquer lugar, desde que seja possível referenciá-lo por meio de seu aplicativo ou da linha de comandos.

- Certificado de raiz do TLS para o Starter Plan  
  - EUA: [us01.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
  - Reino Unido: [uk01.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
  - Sydney: [aus01.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
- [Certificado de Raiz do TLS para o Enterprise Plan ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

Todas as redes do {{site.data.keyword.blockchainfull_notm}} Platform usam TLS do lado do servidor, em que a rede precisa autenticar seus clientes. As redes do Enterprise Plan também podem ativar o TLS mútuo, em que o cliente e o servidor se autenticam mutuamente, para proteger ainda mais seus aplicativos. Os certificados TLS do lado do cliente (para TLS mútuo) são emitidos pela CA do cliente e são exclusivos para sua rede. Caso use uma rede do Enterprise Plan, recomenda-se ativar o TLS mútuo. Para obter mais informações sobre o TLS mútuo, consulte estas [instruções](v10_dashboard.html#mutual-tls "instruções do TLS mútuo").

## Fornecedores de serviço de associação (MSPs)
{: #msp}

Os componentes do {{site.data.keyword.blockchainfull_notm}} Platform consomem identidades via Membership Service Providers (MSPs). MSPs associam os certificados que as autoridades de certificação emitem com as funções de rede e canal. Para obter mais informações sobre MSPs, consulte o [Tópico de conceito de associação na documentação do Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Tópico de conceito de associação na documentação do Hyperledger Fabric").

As pastas MSP dentro do Fabric têm uma estrutura definida. Ao inscrever-se usando o Fabric CA Client, o cliente armazena os certificados em uma pasta MSP em seu sistema de arquivos local com as seguintes subpastas:

- **cacerts:** essa pasta contém o certificado raiz da autoridade de certificação raiz de sua rede.
- **intermediatecerts:** esses são certificados das CAs intermediárias de sua rede. Essas CAs intermediárias são vinculadas à autoridade de certificação raiz e formam uma cadeia de confiança. Cada organização do Enterprise Plan tem duas CA intermediárias para failover e alta disponibilidade.
- **signcerts:** essa pasta contém seu certificado de assinatura pública, que também é chamado de certificado signCert ou certificado de inscrição. Esse certificado é anexado às suas chamadas à rede (uma chamada de chaincode, por exemplo) quando você referencia o seu diretório do MSP por meio da linha de comandos ou constrói um objeto de contexto do usuário com os SDKs. Será possível fazer upload desse certificado para a plataforma se você quiser operar uma rede por meio do SDK ou da linha de comandos.
- **keystore:** essa pasta contém sua chave privada. Essa chave é usada para assinar suas chamadas na rede ao referenciar seu diretório MSP por meio da linha de comandos ou construir um objeto de contexto do usuário com os SDKs. Mantenha essa chave segura para proteger sua rede e seus dados.

Também é possível construir uma pasta MSP que possa ser referenciada pelo Fabric CA Client usando o Monitor de rede e as APIs do Swagger.

- **cacerts** e **intermediatecerts**: é possível buscar esses certificados com as [APIs do Swagger](howto/swagger_apis.html) emitindo uma solicitação GET para a API do MSP.
- **signcerts** e **keystore**: é possível gerar esses certificados clicando no botão **Gerar certificados** no painel "Autoridade de certificação". Uma janela pop-up é aberta com dois certificados listados. Copie e armazene o **Certificado** em signcerts e a **Chave privada** no keystore. Mantenha esses certificados em um local seguro porque eles não ficam armazenados na plataforma.

Muitos componentes do Fabric contêm informações adicionais dentro de sua pasta MSP. Por exemplo, se você operar um peer remoto, poderá ver as pastas a seguir:

- **admincerts:** essa pasta contém a lista de administradores para essa organização ou esse componente. Será necessário fazer upload de seu certificado de assinatura para essa pasta se estiver operando um peer remoto por meio da linha de comandos ou dos SDKs.
- **tls:** essa é uma pasta na qual você armazena os certificados TLS usados para comunicação com outros componentes de rede.

Para obter mais informações sobre a estrutura de MSPs, consulte [Associação ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/membership/membership.html "Associação") e [Membership Service Providers ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html "Membership Service Providers") na documentação do Hyperledger Fabric.

## Inscrição e registro usando o Fabric CA Client.
{: #enroll-register-caclient}

Também é possível usar o Fabric CA Client para gerar certificados e registrar uma nova identidade com a Autoridade de certificação. As instruções abaixo geram certificados usando sua identidade de administrador e, em seguida, usam esses certificados para registrar um novo cliente. A inscrição usando o Fabric CA Client gerará os certificados descritos na [seção Membership Service Provider](#msp).

### Inscrevendo-se usando o Fabric CA Client
{: #enroll-app-caclient}

1. Faça download dos [binários fabric-ca](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) para sua máquina local, extraia-os e mova-os para uma pasta, como `$HOME/fabric-ca-platform/`. Mude para o diretório no qual os binários do cliente foram movidos para que seja possível referenciá-lo diretamente em seus comandos.
    ```
    cd $HOME/fabric-ca-platform/
    ```

2.  Configure o caminho no qual o cliente pode criar suas chaves. Assegure-se de remover o material de configuração criado pela tentativa anterior. Se você não executar o comando `enroll` antes, a pasta `msp` e o arquivo `.yaml` não existirão.
    ```
    export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-platform
    rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
    rm -rf $FABRIC_CA_CLIENT_HOME/msp
    ```

3. Faça download dos certificados TLS do {{site.data.keyword.cloud_notm}}, dependendo do plano de serviço, local e cluster que você usa.
  - Certificado de raiz do TLS para o Starter Plan  
    - EUA: [us01.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert")
    - Reino Unido: [uk01.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert")
    - Sydney: [aus01.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert"); [aus02.blockchain.ibm.com.cert ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://blockchain-certs.mybluemix.net/aus02.blockchain.ibm.com.cert "aus02.blockchain.ibm.com.cert")
  - [Certificado de Raiz do TLS para o Enterprise Plan ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Salve o conteúdo em uma pasta, por exemplo, ``$HOME/tls``. Esta etapa permite que o fluxo de dados seja criptografado na conexão.

4. Abra o arquivo JSON **Perfil de conexão** por meio do painel "Visão geral" no Monitor de rede e localize as variáveis a seguir:
  * URL para CA: `url` sob `certificateAuthorities`
  * ID do usuário administrador: ``enrollId``
  * Senha do administrador: ``enrollSecret``
  * Nome da CA: ``caName``

5. É possível usar o cliente da autoridade de certificação do Fabric para enviar uma chamada `enroll` para a autoridade de certificação transmitindo o caminho de certificados TLS e as quatro sequências acima com o comando a seguir:
  ```
  ./fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <tls_cert_path>
  ```
  {:codeblock}

  Você precisará executar esse comando no diretório para o qual moverá o cliente da autoridade de certificação do Fabric. Uma chamada real pode ser semelhante ao comando de exemplo a seguir:
  ```
  ./fabric-ca-client enroll -u https://admin:dda0c53f7b@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/tls/us2.blockchain.ibm.com.cert
  ```
  {:codeblock}

6. Localize seu certificado admin em  ` $FABRIC_CA_CLIENT_HOME/msp/signcerts/cert.pem `. É possível, então, fazer upload do certificado administrativo para sua rede de blockchain do Monitor de Rede. Para obter mais informações sobre a inclusão de certificados, consulte [a guia "Certificados" do painel "Membro"](v10_dashboard.html#members) no Monitor de rede.

  Também é possível localizar o certificado raiz da CA e a chave privada do administrador nos diretórios a seguir:
  * Certificado raiz CA:  ` $FABRIC_CA_CLIENT_HOME/msp/cacerts/ --<ca_name>.pem`
  * A chave privada do administrador:  ` $FABRIC_CA_CLIENT_HOME/msp/keystore/<>_sk file`

Para ver um exemplo de onde você se inscreveria usando o Fabric CA Client e usaria os certificados gerados para operar um componente de rede, consulte as instruções para [operar um peer remoto](howto/remote_peer_operate_icp.html#remote-peer-cli-operate).

### Registrando usando o Fabric CA Client
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
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-platform/admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: userpw
  ```
  {:codeblock}
