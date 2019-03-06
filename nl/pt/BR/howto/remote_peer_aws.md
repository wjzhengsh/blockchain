---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Introdução ao {{site.data.keyword.blockchainfull_notm}} Platform for AWS
{: #remote-peer-aws}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Estas instruções descrevem como usar um modelo de Iniciação Rápida do Amazon Web Services (AWS) para criar um peer do {{site.data.keyword.blockchainfull}} Platform for AWS e, em seguida, conectá-lo a uma rede em um {{site.data.keyword.blockchainfull_notm}} Platform.
{:shortdesc}

Para obter mais informações sobre o AWS, consulte o [documento da visão geral do AWS ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://d1.awsstatic.com/whitepapers/aws-overview.pdf "documento da visão geral do AWS").

O {{site.data.keyword.blockchainfull_notm}} Platform for AWS permite que os peers aproveitem o perfil de conexão, as autoridades de certificação do Hyperledger Fabric (autoridades de certificação) e o serviço de ordenação de uma rede existente do Starter ou do Enterprise Plan no {{site.data.keyword.cloud_notm}} para processar transações por meio de um modelo de Iniciação Rápida do AWS. A Iniciação Rápida permite que você implemente peers usando modelos AWS CloudFormation. Esse modelo é para os tomadores de decisão de infraestrutura de TI e administradores do sistema que desejam configurar, implementar e executar rapidamente os peers do {{site.data.keyword.blockchainfull_notm}} Platform hospedados pelo AWS que estão conectados a uma rede do Starter ou Enterprise Plan. É possível usar o modelo para construir uma nova nuvem virtual privada (VPC) no AWS ou implementar o peer em um VPC existente.

A Iniciação Rápida conclui as configurações a seguir:
 * Uma arquitetura altamente disponível que abrange duas zonas de disponibilidade.
 * Um VPC configurado com sub-redes públicas de acordo com as melhores práticas do AWS. Isso fornece a você sua própria rede virtual no AWS.
 * Um gateway de Internet para permitir acesso à Internet.
 * Nas sub-redes públicas, dois peers em duas Zonas de Disponibilidade (um par em cada sub-rede).
 * Em cada sub-rede pública, um contêiner de peer com um banco de dados LevelDB integrado ou um contêiner CouchDB secundário.

Antes de implementar os peers do {{site.data.keyword.blockchainfull_notm}} Platform for AWS, revise as [considerações sobre o peer](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about-limitations).

## Pré-requisitos
{: #remote-peer-aws-prerequisites}

Para usar um peer do {{site.data.keyword.blockchainfull_notm}} Platform for AWS (peer remoto), deve-se ter uma organização que seja membro de uma rede de blockchain que esteja hospedada no IBM Blockchain Platform. É necessário usar o Monitor de Rede no IBM Cloud para acessar as credenciais de rede e os terminais de API de sua rede. Se você não for membro de nenhuma rede de blockchain, precisará criar ou associar-se a uma rede. Para obter mais informações, consulte [Criando uma rede](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network) ou [Associando-se a uma rede](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-join-nw).

O tipo de instância VPC padrão para o peer é `m4.xlarge`.  É necessário otimizar o tipo de instância escolhido com base em seus requisitos de CPU, memória e armazenamento. O peer requer pelo menos:  
-	2x CPU
-	2 GB de RAM
-	4 GB de espaço para chaincode
-	10 GB de espaço para o livro-razão com a capacidade de crescer conforme o livro-razão se expande.

Esses níveis de recursos mínimos são suficientes para teste e experimentação. Para um ambiente de produção, é importante alocar uma quantidade suficientemente grande de armazenamento, por exemplo, 100 GB. A quantidade de armazenamento usada dependerá do número de transações e do número de assinaturas necessários para sua rede. Se você esgotar o armazenamento em seu peer, deverá <!-- either expand the storage or --> implementar um novo peer com um sistema de arquivos maior e permitir que ele sincronize por meio de seus outros peers nos mesmos canais.


## Opções de implementação
{: #remote-peer-aws-deploy-options}

A Iniciação Rápida fornece duas opções de implementação:

* Implemente o {{site.data.keyword.blockchainfull_notm}} Platform for AWS em um novo VPC (implementação de ponta a ponta). Essa opção cria um novo ambiente do AWS que consiste em VPC, sub-redes, gateways NAT, grupos de segurança, hosts de bastiões e outros componentes de infraestrutura e, em seguida, implementa o peer nesse novo VPC.

* Implemente o {{site.data.keyword.blockchainfull_notm}} Platform for AWS em um VPC existente. Essa opção provisiona o peer do {{site.data.keyword.blockchainfull_notm}} Platform for AWS em sua infraestrutura existente do AWS. Há modelos separados para essas opções em que é possível configurar blocos CIDR, tipos de instância e configurações de Peer, conforme discutido posteriormente neste guia.

## Etapa um: prepare sua conta do AWS
{: #remote-peer-aws-account}

1. Se você ainda não tiver uma conta do AWS, crie uma [aqui ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://aws.amazon.com "https//aws/amazon.com") seguindo as instruções na tela.

2. Use o seletor de região na barra de navegação para escolher a Região do AWS na qual você deseja implementar o peer no AWS.

3. Crie um par de chaves em sua região preferencial.

4. Se necessário, solicite um aumento de limite de serviço para o tipo do Amazon EC2 <> tipo de instância. Talvez seja necessário fazer isso se você já tiver uma implementação existente que usa esse tipo de instância e você acha que poderá exceder o limite padrão com essa implementação.

## Etapa dois: recuperar suas informações de configuração de peer remoto
{: #remote-peer-aws-network-endpoints}

É necessário fornecer os terminais de API de sua rede para seu peer durante a configuração. Esses terminais permitem que um peer localize e conecte-se à rede no {{site.data.keyword.blockchainfull_notm}} Platform. Na tela **Visão geral** de seu Monitor de rede, clique no botão **Configuração do peer remoto**.

![Configuração do peer remoto](../images/myresources_starter.png "Configuração do peer remoto")
*Figura 1. Painel de configuração de peer remoto*

Uma janela pop-up é aberta e exibe os valores dos campos a seguir. Salve os valores dos campos a seguir, eles são necessários quando você configura o peer usando o modelo de Iniciação Rápida do AWS.

- ** MSP da Organização **
- ** Nome da Autoridade de Certificação (CA) **
- ** URL da Autoridade de Certificação (CA) **
- ** Certificado TLS da Autoridade de Certificação (CA) **

É possível copiar e colar cada campo diretamente no modelo de Iniciação Rápida ou salvá-los em um arquivo JSON clicando no link **Download**.

O modelo de Iniciação rápida espera que o certificado TLS seja formatado com quebras de linha `\r\n`. Se você estiver usando um navegador com o Monitor de rede em um S.O. `*NIX`, será necessário reformatar o certificado que você copiar da IU. Substitua todas as ocorrências de `\n` por `\r\n` e cole a sequência resultante no campo rotulado `Certificate Authority (CA) TLS Certificate Chain`.
{:important}

**Nota:** se você fizer download das informações no JSON, será necessário converter o certificado TLS para o formato PEM antes de fornecê-lo ao peer. Converta o **Certificado TLS da autoridade de certificação (CA)** no arquivo JSON transferido por download para o formato PEM, emitindo o comando:
```
echo -e "<CERT>" > admin.pem
```
{:codeblock}

Substitua `<CERT>` pelo valor do **Certificado TLS da autoridade de certificação (CA)**. Em seguida, quando solicitado para o ** Certificado TLS da autoridade de certificação (CA)** no modelo de Iniciação Rápida, `cat` o arquivo admin.pem e, em seguida, copie e cole o conteúdo no campo.  

## Etapa três: registrar um peer do {{site.data.keyword.blockchainfull_notm}} Platform for AWS
{: #remote-peer-aws-register-peer}

É necessário incluir uma nova identidade de peer em sua rede no {{site.data.keyword.blockchainfull_notm}} Platform antes que o peer do {{site.data.keyword.blockchainfull_notm}} Platform for AWS possa se associar à rede. Conclua as etapas a seguir para registrar um peer.

**Nota:** para Alta Disponibilidade, o modelo de Iniciação Rápida cria dois nós de peer em duas zonas de disponibilidade. Portanto, são requeridos dois IDs e segredos de inscrição. **Repita esse processo duas vezes para gerar dois IDs e segredos de inscrição.**

1. Efetue login no Monitor de Rede de sua rede no {{site.data.keyword.blockchainfull_notm}} Platform. Na tela "Autoridade de certificação" de seu Monitor de Rede, é possível visualizar todas as identidades que foram registradas com a rede, como seus aplicativos de administrador ou cliente.
![Tela da autoridade de certificação](../images/CA_screen_starter.png "Tela da autoridade de certificação")
  *Figura 2. Tela da autoridade de certificação*

2. Clique no botão **Incluir usuário** no painel. Uma tela pop-up é aberta e permite que você registre seu peer na rede depois de preencher os campos abaixo. **Salve o valor do ID e do Segredo para mais tarde ao configurar seu peer no modelo de Iniciação Rápida.**
  - **ID de inscrição:** o nome que você deseja usar para seu peer, que é referido como seu `enroll ID` ao configurar seu peer. **Salve esse valor** para uso futuro.
  - **Segredo de inscrição:** a senha que você deseja usar para seu peer, que é referida como seu `enroll Secret` ao configurar o peer. **Salve esse valor** para uso futuro.
  - **Tipo:** Selecione `peer` para este campo.
  - **Afiliação:** essa é a afiliação sob sua organização, `org1`, por exemplo, à qual seu peer pertence. É possível especificar uma nova afiliação ou usar uma existente.
  - **Máximo de inscrições:** é possível usar esse campo para limitar o número de vezes que é possível inscrever-se ou gerar certificados usando essa identidade. Se não especificado, o valor será padronizado para inscrições ilimitadas.

  Depois de preencher os campos, clique em **Enviar** para registrar o peer. O peer registrado é, então, listado na tabela como uma identidade na rede. Como uma medida de segurança, use cada identidade e o enrollID e segredo associados para implementar somente um peer. Não reutilize os IDs e senhas do peer.

## Etapa quatro: Ativar a Iniciação Rápida
{: #remote-peer-aws-launchqs}

Você é responsável pelo custo dos serviços do AWS que você usa enquanto executa esta implementação de referência de Iniciação Rápida. Não há custo adicional para usar esta Iniciação Rápida. Para obter detalhes completos, consulte as páginas de precificação para cada serviço do AWS que você precisa usar nessa Iniciação Rápida. Os preços estão sujeitos a mudanças.

1. Escolha uma das opções a seguir para ativar o modelo do AWS CloudFormation em sua conta do AWS. Para ajudar a escolher uma opção, consulte as opções de implementação anteriormente neste guia. Cada implementação leva cerca de 10 minutos para ser concluída.  

  * [Implemente o {{site.data.keyword.blockchainfull_notm}} Platform for AWS em um novo VPC no AWS ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://fwd.aws/v43nk "Implemente o {{site.data.keyword.blockchainfull_notm}} Platform for AWS em um novo VPC no AWS").  

  * [Implemente o {{site.data.keyword.blockchainfull_notm}} Platform for AWS em um VPC existente no AWS ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://fwd.aws/zrP4g "Implemente o {{site.data.keyword.blockchainfull_notm}} Platform for AWS em um VPC existente no AWS").

  **Importante:**     
  Se você estiver implementando o {{site.data.keyword.blockchainfull_notm}} Platform for AWS em um VPC existente, certifique-se de que seu VPC tenha duas sub-redes públicas em Zonas de Disponibilidade diferentes para as instâncias de banco de dados. Essas sub-redes requerem gateways do NAT ou instâncias do NAT em suas tabelas de rotas, para permitir que as instâncias façam download de pacotes e software sem expô-los à Internet. Você também precisará da opção de nome de domínio configurada nas opções de DHCP, conforme explicado na documentação do Amazon VPC.  

  Além disso, certifique-se de criar um grupo de segurança vinculado ao VPC existente e inclua regras de entrada nas portas 22 e 7051 para esse grupo de segurança. As conexões TCP na porta 22 permitem acesso de SSH à instância gerada enquanto as conexões TCP na porta 7051 permitem acesso de gRPC externo à instância de peer (necessário para operar o peer usando a CLI das ferramentas Fabric e os SDKs do Fabric). Você será solicitado a fornecer essas configurações de VPC quando ativar a Iniciação rápida.

2. Verifique a região que é exibida no canto superior direito da barra de navegação e mude-a, se necessário. É aqui que a infraestrutura de rede para o peer será construída. O modelo é ativado na Região do Leste dos EUA (Ohio) por padrão.

3. Na página Selecionar modelo, mantenha a configuração padrão para a URL do modelo e, em seguida, escolha `Avançar`.

4. Na página Especificar detalhes, mude o nome da pilha, se necessário. Revise os parâmetros para o modelo. Forneça valores para os parâmetros que requerem entrada. Para todos os outros parâmetros, revise as configurações padrão e customize-as conforme necessário. Quando concluir a revisão e a customização dos parâmetros, escolha `Next`.

Nas tabelas a seguir, os parâmetros são listados por categoria e descritos separadamente para as duas opções de implementação:

  * [ Parâmetros para implementar o {{site.data.keyword.blockchainfull_notm}} Platform for AWS em um novo VPC](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-parameters-newvpc)

  * [Parâmetros para implementar o {{site.data.keyword.blockchainfull_notm}} Platform for AWS em um VPC existente](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-parameters-existvpc).

### Parâmetros para implementar o {{site.data.keyword.blockchainfull_notm}} Platform for AWS em um novo VPC
{: #remote-peer-aws-parameters-newvpc}

A tabela a seguir lista os parâmetros configuráveis do gráfico AWS e seus valores padrão. Todos os valores são necessários.

|  Parâmetro    | Descrição | Padrão |
| --------------|-------------|---------|
| `Stack name` |O nome da pilha é um identificador que ajuda a localizar uma pilha específica a partir de uma lista de pilhas. Um nome de pilha pode conter apenas caracteres alfanuméricos (com distinção entre maiúsculas e minúsculas) e hifens. Ele deve iniciar com um caractere alfabético e não pode ter mais de 128 caracteres.| |
| | | |
| **Configuração de rede** | |
| `Availability Zones` |As duas Zonas de Disponibilidade a serem usadas para as sub-redes no VPC. Nota: a ordem lógica é preservada. | |
| `Allowed SSH access CIDR` | [Bloco CIDR ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-resize "VPCs e sub-redes") permitido para acesso externo ao SSH para as instâncias do IBM Blockchain Peer. Ele pode ser configurado como 0.0.0.0/0 para permitir acesso de qualquer lugar (não recomendado). | |
| `PeerEndpointAccessCIDR` | [Bloco CIDR ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-resize "VPCs e sub-redes") permitido para o acesso gRPC externo às instâncias do IBM Blockchain Peer. Geralmente configurado como 0.0.0.0/0 para permitir acesso de qualquer lugar (não recomendado). | |
| | | |
| **Configuração do Amazon EC2** | | |
| `InstanceType` | Tipo de instância EC2 para as instâncias de peer. | m4.xlarge |
| `KeyPairName` | Nome de um par de chaves EC2 existente na região do AWS. Deve-se gerar isso. | |
| | | |
|** Configuração do IBM Blockchain** | |
| `IBMBlockchainVersion` | Versão do IBM Blockchain para implementação. | 1.2.1 |
| `StateDatabase` | O tipo de banco de dados a ser usado para armazenar o estado de blockchain. Essa seleção deve corresponder ao tipo de Banco de Dados de Estado usado pelo restante da rede. | CouchDB|
| `PeerVolumeSize` | Tamanho do Volume de EBS que é usado para armazenar dados persistentes (livro-razão, banco de dados de estado, MSP) para o peer em GBs. | 100 |
| `Peer 1 enroll ID`| O ID de Inscrição que você inseriu em seu painel Autoridade de Certificação da IU do IBM Blockchain Platform para seu primeiro peer. |  |
| `Peer 1 enroll secret` | O Segredo de Inscrição que você inseriu em seu painel Autoridade de Certificação da IU do IBM Blockchain Platform para seu primeiro peer. | |
| `Peer 2 enroll ID` | O ID de Inscrição que você inseriu no painel Autoridade de certificação da IU do IBM Blockchain Platform para seu segundo peer. | |
| `Peer 2 enroll secret` | O Secret de Inscrição que você inseriu no painel Autoridade de certificação da IU do IBM Blockchain Platform para seu segundo peer. | |
| | | |
|**Credenciais de serviço do IBM Blockchain**| | |
| `Organization MSP` | Esse valor pode ser localizado em sua IU do IBM Blockchain Platform. Clique no botão Configuração de peer remoto no painel Visão geral e copie e cole essas informações aqui. | |
| `Certificate Authority (CA) Name` | Esse valor pode ser localizado em sua IU do IBM Blockchain Platform. Clique no botão Configuração de peer remoto no painel Visão geral e copie e cole essas informações aqui.| |
| `Certificate Authority (CA) URL` | Esse valor pode ser localizado em sua IU do IBM Blockchain Platform. Clique no botão Configuração do peer remoto no painel Visão geral e copie e cole essas informações aqui, incluindo a porta. Se não especificado, a porta padrão será 443. | |
| `Certificate Authority (CA)  TLS Certificate`| Esse valor pode ser localizado em sua IU do IBM Blockchain Platform. Clique no botão Configuração de peer remoto no painel Visão geral e copie e cole essas informações aqui.| |
| | | |
|**Outros parâmetros**| | |
| `QSS3BucketName` | Nome do depósito S3 para os ativos de Iniciação Rápida. O nome do depósito de Iniciação Rápida pode incluir números, letras minúsculas, letras maiúsculas e hifens (-). Ele não pode iniciar ou terminar com um hífen (-). | `aws-quickstart` |
| `QSS3KeyPrefix` | Prefixo de chave S3 para os ativos de Iniciação Rápida. O prefixo da chave de Iniciação Rápida pode incluir números, letras minúsculas, letras maiúsculas, hifens (-) e barra (/). | `quickstart-ibm-fabric/` |

1. Na página Opções, é possível especificar marcações (pares chave-valor) para recursos em sua pilha e configurar opções avançadas. Quando tiver concluído, escolha Avançar.

2. Na página Revisar, revise e confirme as configurações do modelo. Em Capacidades, selecione a caixa de seleção para reconhecer que o modelo criará recursos do IAM.

3. Escolha Criar para implementar a pilha.

4. Monitore o status da pilha. Quando o status de todas as pilhas é `CREATE_COMPLETE`, o cluster do peer está pronto. Após a conclusão bem-sucedida, deve-se ter uma pilha raiz com quatro pilhas aninhadas, se couchDB foi escolhido ou duas pilhas aninhadas, se o levelDB foi escolhido.

5. Use as informações exibidas na guia Saídas para a pilha para visualizar os recursos que foram criados.


### Parâmetros para implementar um Peer do {{site.data.keyword.blockchainfull_notm}} Platform em um VPC existente
{: #remote-peer-aws-parameters-existvpc}

Se você estiver implementando o peer do {{site.data.keyword.blockchainfull_notm}} Platform for AWS em um VPC existente, os itens a seguir deverão ser levados em consideração:

 - Assegure-se de que seu VPC tenha duas sub-redes privadas em Zonas de Disponibilidade diferentes para as instâncias de banco de dados. Essas sub-redes requerem gateways do NAT ou instâncias do NAT em suas tabelas de rotas, para permitir que as instâncias façam download de pacotes e software sem expô-los à Internet.

 - Configure a opção de nome de domínio nas opções do DHCP, conforme explicado na documentação do [Amazon VPC ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_DHCP_Options.html "Conjuntos de opções de DHCP").  

- Crie um grupo de segurança vinculado ao VPC existente e inclua regras de entrada nas portas 22 e 7051 para esse grupo de segurança. As conexões TCP na porta 22 permitem acesso de SSH à instância gerada enquanto as conexões TCP na porta 7051 permitem acesso de gRPC externo à instância de peer (necessário para operar o peer usando a CLI das ferramentas Fabric e os SDKs do Fabric). Você será solicitado a fornecer essas configurações de VPC quando ativar a Iniciação rápida.

 Ao implementar um peer do {{site.data.keyword.blockchainfull_notm}} Platform for AWS em um VPC existente, os parâmetros a seguir substituem os parâmetros nas seções correspondentes [acima](/docs/services/blockchain/howto/remote_peer_aws.html#remote-peer-aws-parameters-newvpc):

|  Parâmetro    | Descrição | Padrão |
| --------------|-------------|---------|
| **Configuração de rede** | | |
| `VPCID` |	ID de seu VPC existente para implementação.| |
| `AvailabilityZone1` | A Zona de Disponibilidade na qual implementar o primeiro nó de peer. | |
| `SubnetID1` |	ID da sub-rede a ser usado para o primeiro nó de peer. A sub-rede deve pertencer à VPC escolhida. Para localizar as sub-redes pertencentes à VPC, verifique seu painel de VPC do AWS e selecione o menu "sub-redes".| |
| `AvailabilityZone2` | A Zona de Disponibilidade na qual implementar o segundo nó de peer. | |
| `SubnetID2` |	ID da sub-rede a ser usado para o segundo nó de peer. A sub-rede deve pertencer à VPC escolhida. Para localizar as sub-redes pertencentes à VPC, verifique seu painel de VPC do AWS e selecione o menu "sub-redes". | |
| | | |
| **Configuração do Amazon EC2**| | |
| `InstanceType` 	| Tipo de instância EC2 para as instâncias de peer. 	| m4.xlarge |
| `KeyPairName` |	Nome de um par de chaves EC2 existente na região do AWS. Deve-se gerar isso. | |
| `SecurityGroup` | ID de um grupo de segurança EC2 existente dentro da região do AWS. Você deve permitir conexões TCP de entrada nas portas 22 e 7051. |	| |

## Etapa cinco: testar a implementação
{: #remote-peer-aws-test}

Quando o modelo AWS CloudFormation tiver criado com êxito a pilha, duas instâncias de peer do {{site.data.keyword.blockchainfull_notm}} Platform for AWS estarão em execução em sua conta do AWS. As instâncias serão nomeadas com base na combinação de `Organization MSP` e `Peer enroll id` especificada no modelo de Iniciação Rápida. Por exemplo, `org1-remotepeer1`.  

![Peer em instâncias do AWS EC2](../images/remote_peer_AWS_EC2_instances.png "Peer em instâncias do AWS EC2")  
*Figura 3. Peer em instâncias do AWS EC2*

Para verificar se o peer está em execução:

  * Efetue SSH em seu VPC recém-criado selecionando a instância no console do AWS (clique em **Serviços > EC2 > Instâncias**) e, em seguida, clicando no botão  **Conectar**. Siga as instruções do AWS para emitir o comando `ssh`.  
  * Na linha de comandos, execute `docker ps` para visualizar os contêineres em execução. Cada máquina virtual inclui um contêiner de peer e um ecs-agent. Se você selecionou CouchDB como seu banco de dados de livro-razão, também haverá um contêiner CouchDB.

  ```
  CONTAINER ID        IMAGE                                STATUS              PORTS                          NAMES
  fb3c49fe52fe        amazon/amazon-ecs-agent:latest       Created                                            ecs-agent
  667780cf3cd3        ibmblockchain/fabric-peer:1.2.1      Up                  0.0.0.0:7051->7051/tcp         peer
  2aa143c81027        ibmblockchain/fabric-couchdb:0.4.6   Up                  4369/tcp, 5984/tcp, 9100/tcp   couchdb
  ```

  * É possível criar uma sessão de shell dentro do contêiner de peer executando `docker exec -it peer sh`.

Opcionalmente, se você deseja uma verificação adicional de que a conexão de peer está funcionando em sua rede do {{site.data.keyword.blockchainfull_notm}} Platform, é possível executar o comando da CLI `peer channel fetch` de dentro do contêiner de peer. Caso contrário, é possível ir adiante para obter instruções para [Operar seu peer](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate).  

Execute o comando da CLI `peer channel fetch` para buscar o bloco genesis do canal:

1. Recupere as informações de configuração de seu `Connection Profile` disponível no painel Visão geral de seu Monitor de Rede. Clique em **Perfil de Conexão** e, em seguida, em **Download**.

   - Localize a URL do solicitador procurando **solicitadores**, localizado em `orderers > url`. Anote a URL que inicia com o nome da rede. A URL é semelhante ao exemplo a seguir:

   ```
   ash-zbc07b.4.secure.blockchain.ibm.com:21239
   ```

   - Localize o nome de sua organização procurando **organizações**. Essa deve ser a mesma organização que você usa para registrar seu peer. É possível localizar o nome de sua organização junto com seu `mspid` associado. Esse valor também está disponível no painel Visão geral do Monitor de Rede. Clique no botão **Configuração de peer remoto**. O valor é exibido em `Organization MSP`. Anote o valor de `mspid`.

   - Se você ainda não tiver feito isso, crie uma sessão de shell dentro do contêiner de peer executando `docker exec -it peer sh`.

   ```
   docker exec -it peer sh
   ```
   {:codeblock}

   - Copie o certificado TLS do solicitador do perfil de conexão para o peer. Navegue para a seção **solicitadores**. Copie o certificado que segue "pem:", começando com -----BEGIN CERTIFICATE----- e terminando com -----END CERTIFICATE -----. Não inclua as aspas. Execute o comando a seguir a partir da linha de comandos, substituindo `<orderer cert>` pelo conteúdo copiado do arquivo creds.json.

   ```
   echo -e "<orderer cert>" > /etc/hyperledger/<PEER_ENROLL_ID>/orderer_tlscacert.pem
   ```
   {:codeblock}

   Substitua `<PEER_ENROLL_ID>` pelo ID de inscrição que está especificado no modelo de Iniciação Rápida e está associado a essa instância de peer.

2. Sua organização precisa ser incluída em um canal na rede antes que você possa buscar o bloco genesis.

  - É possível iniciar um novo canal para o peer. Como o inicializador de canais, é possível incluir automaticamente a sua organização durante a [criação do canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-creating-a-channel).

  - Outro membro da rede de blockchain também pode incluir sua organização em um canal existente usando uma [atualização de canal](/docs/services/blockchain/howto/create_channel.html#ibp-create-channel-updating-a-channel).

  - Depois que sua organização é incluída em um canal, é necessário incluir o certificado de assinatura do peer no canal. O peer faz upload de seu certificado de assinatura durante a instalação, de modo que você precise somente sincronizar o certificado para o canal. Na tela "Canais" do Monitor de rede, localize o canal que sua organização associou e selecione **Sincronizar certificado** na lista suspensa sob o cabeçalho **Ação**. Essa ação sincroniza os certificados em todos os peers no canal.

3. Execute os comandos a seguir para configurar as variáveis de ambiente no contêiner de peer.

   ```
   export ORDERER_1=<ORDERER_URL>
   export CHANNEL=<CHANNEL_NAME>
   export ORGID=<ORGANIZATION_MSP_ID>
   export PEERADDR=<PEER_ADDR>
   ```
   {:codeblock}

   Substitua os campos por suas próprias informações.
     - Substitua `<ORDERER_URL>` pelo nome do host e a porta do solicitador do arquivo `creds.json`.
     - Substitua `<CHANNEL_NAME>` pelo nome do canal ao qual o peer se associará.
     - Substitua `<ORGANIZATION_MSP_ID>` pelo nome da organização do arquivo `creds.json`.
     - Substitua `<PEER_ADDR>` por `localhost:7051`

   Por exemplo:

   ```
   export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
   export CHANNEL=defaultchannel
   export ORGID=PeerOrg1
   export PEERADDR=localhost:7051
   ```

4. Execute o comando da CLI de peer a seguir para buscar o bloco genesis do canal.

   **IMPORTANTE:** no comando a seguir, substitua cada ocorrência de `<PEER_ENROLL_ID>` pelo ID de inscrição associado a essa instância de peer que foi especificado no modelo de Iniciação Rápida. Esse valor pode ser localizado executando o comando `ls /etc/hyperledger/`. Haverá duas pastas listadas: `fabric` e a segunda é o seu `<PEER_ENROLL_ID>`.

   ```
   CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/<PEER_ENROLL_ID>/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /etc/hyperledger/<PEER_ENROLL_ID>/orderer_tlscacert.pem --tls
   ```
   {:codeblock}


   **Nota:** ao executar qualquer um desses comandos da CLI, é possível que você possa experimentar o aviso a seguir, que poderá ser ignorado com segurança.

   ```
   [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
   /etc/hyperledger/<PEER_ENROLL_ID>/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /etc/hyperledger/<PEER_ENROLL_ID>/msp/intermediatecerts/<intermediate cert name>.pem
   ```

   Verifique se o comando funcionou com êxito e se o bloco genesis foi incluído no contêiner de peer executando o comando a seguir:

   ```
   ls *.block
   ```
   {:codeblock}

   Você sabe que o bloco genesis foi incluído com sucesso ao ver algo semelhante ao exemplo a seguir:

   ```
   defaultchannel_0.block
   ```

   Parabéns! Seu peer do {{site.data.keyword.blockchainfull_notm}} Platform for AWS foi conectado com sucesso à sua rede do {{site.data.keyword.blockchainfull_notm}} Platform.

## Perguntas Frequentes
{: #remote-peer-aws-faq}

* **P**. Eu encontrei um erro CREATE_FAILED quando ativei a Iniciação Rápida.
* **R**. Se o AWS CloudFormation falhar ao criar a pilha, recomendamos que você reative o modelo com Recuperar na falha configurado como `No`. (Essa configuração está em Avançado no console do AWS CloudFormation, página Opções.) Com essa configuração, o estado da pilha será retido e a instância será deixada em execução, para que você possa solucionar o problema. (Examine os arquivos de log em ` %ProgramFiles%\Amazon\EC2ConfigService` e `C:\cfn\log`.)

  - Ao configurar Recuperar na falha como `No`, você continuará incorrendo
encargos do AWS para essa pilha. Certifique-se de excluir a pilha quando concluir a resolução de problemas. Para obter informações adicionais, consulte [Resolução de problemas do AWS CloudFormation ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/troubleshooting.html "Resolução de problemas do AWS CloudFormation") no website do AWS.

* **P**. Eu encontrei um erro de limitação de tamanho quando implementei os modelos do AWS Cloudformation.
* **R**. Recomendamos que você ative os modelos de Iniciação Rápida a partir do local que fornecemos ou de outro depósito S3. Se você implementar os modelos a partir de uma cópia local em seu computador ou a partir de um local não S3, poderá encontrar limitações de tamanho do modelo ao criar a pilha. Para obter mais informações sobre os limites do AWS CloudFormation, consulte a [Documentação do AWS ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html "Limites do AWS CloudFormation").

## O que Vem Depois
{: #remote-peer-aws-whats-next}

Depois de configurar o peer no AWS, é possível concluir várias etapas operacionais antes de poder enviar transações e ler o livro-razão distribuído a partir da rede de blockchain. Para obter mais informações, veja [Operando peers no AWS](/docs/services/blockchain/howto/remote_peer_operate_aws.html#remote-peer-aws-operate).

## Alta Disponibilidade (HA)
{: #remote-peer-aws-high-availability}

Por padrão, para o suporte de HA, o modelo de Iniciação Rápida implementa duas instâncias do peer, em duas zonas de disponibilidade diferentes.
Para alavancar esse suporte de HA, também é necessário configurar seus [aplicativos clientes para alta disponibilidade](/docs/services/blockchain/v10_application.html#dev-app-ha-app).

## Considerações de segurança
{: #remote-peer-aws-security}

O AWS Cloud fornece uma plataforma escalável e altamente confiável que ajuda os clientes a implementar aplicativos e dados de forma rápida e segura. Ao construir sistemas na infraestrutura do AWS, as responsabilidades de segurança são compartilhadas entre você e o AWS. Esse modelo compartilhado pode reduzir sua carga operacional à medida que o AWS opera, gerencia e controla os componentes do sistema operacional do host e da camada de virtualização até a segurança física das instalações em que os serviços operam. Por sua vez, você assume a responsabilidade e o gerenciamento do sistema operacional guest (incluindo atualizações e correções de segurança), outros aplicativos associados, bem como a configuração do firewall do grupo de segurança fornecido pelo AWS. Para obter mais informações sobre a segurança no AWS, visite o [AWS Cloud Security ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://aws.amazon.com/security/ "AWS Cloud Security").

### AWS Identity and Access Management (IAM)
{: #remote-peer-aws-iam}

Essa solução alavanca uma função do IAM com acesso menos privilegiado. Não é necessário ou recomendado armazenar chaves SSH, chaves secretas ou accesskeys nas instâncias provisionadas.

### Segurança do S.O.
{: #remote-peer-aws-ossecurity}

O usuário raiz nos nós do cluster pode ser acessado apenas usando a chave SSH especificada durante o processo de implementação. O AWS não armazena essas chaves SSH, portanto, se você perder a chave SSH, poderá perder o acesso a essas instâncias. As correções do sistema operacional são sua responsabilidade e devem ser executadas periodicamente.

### Grupos de segurança
{: #remote-peer-aws-securitygroups}

Um grupo de segurança age como um firewall que controla o tráfego para uma ou mais instâncias. Quando você ativa uma instância, associa um ou mais grupos de segurança à instância. Você inclui regras em cada grupo de segurança que permitem o tráfego para ou a partir de suas instâncias associadas. É possível modificar as regras para um grupo de segurança a qualquer momento. As novas regras são aplicadas automaticamente a todas as instâncias que estão associadas ao grupo de segurança. Os grupos de segurança criados e designados às instâncias individuais como parte dessa solução são restritos o máximo possível enquanto permitem o acesso às várias funções necessárias para o peer. Recomendamos a revisão de grupos de segurança para restringir ainda mais o acesso, conforme necessário, uma vez que o cluster esteja funcionando.

### Segurança do peer
{: #remote-peer-aws-peer-security}

Os peers são implementados fora do {{site.data.keyword.blockchainfull_notm}} Platform; portanto, você é responsável por gerenciar a segurança do peer. Isso inclui áreas importantes de segurança fornecidas pelas redes do Enterprise Plan, como gerenciamento de chaves e criptografia de dados. Revise os tópicos a seguir quando considerar a segurança de seus peers.

#### Segurança de dados
{: #remote-peer-aws-security-data}

O {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan usa criptografia de disco inteiro que é baseada em [Criptografia de chave simétrica ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Criptografia simétrica") para proteger todos os dados que as redes usam. Deve-se executar etapas semelhantes em seu próprio ambiente para proteger seus dados do peer.

Os dados em seu banco de dados de estado, independentemente de você estar usando o levelDB ou couchDB, não são criptografados. É possível usar a criptografia de nível do aplicativo para proteger os dados em repouso em seu banco de dados de estado.

#### Residência de dados
{: #remote-peer-aws-data-residency}

A residência de dados obriga que o processamento e armazenamento de todos os dados do livro-razão de blockchain permaneçam dentro da fronteira de um único país.
Para obter mais informações sobre como isso pode ser realizado, veja [Residência de dados](/docs/services/blockchain/howto/remote_peer.html#remote-peer-aws-about-data-residency).

#### Gerenciamento de chave
{: #remote-peer-aws-security-key-management}

O gerenciamento de chaves é um aspecto crítico da segurança do peer. Se uma chave privada for comprometida ou perdida, agentes hostis poderão acessar os dados e a funcionalidade de seu peer. O Enterprise Plan do {{site.data.keyword.blockchainfull_notm}} Platform usa os [Hardware Security Modules](/docs/services/blockchain/glossary.html#glossary-hsm) (HSM) para armazenar as chaves privadas de sua rede. O HSM é um dispositivo físico que evita que outras partes acessem a sua chave privada.

Ao implementar um peer no AWS, você é responsável por gerenciar suas chaves privadas. Embora o {{site.data.keyword.blockchainfull_notm}} Platform gere suas chaves privadas, essas chaves não são armazenadas na Plataform. É essencial assegurar-se de que você armazene suas chaves em um local seguro para que elas não sejam comprometidas. É possível localizar a chave privada de seu peer na pasta de keystore de seu peer MSP, no diretório `/etc/hyperledger/<PEER_ENROLL_ID>/msp/keystore/` dentro de seu contêiner de peer. Para obter mais informações sobre os certificados dentro de seu peer, visite a seção [Membership Services Provider](/docs/services/blockchain/certificates.html#managing-certificates-msp) do tópico [Gerenciando certificados no {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates).

É possível usar o Key Escrow para recuperar chaves privadas perdidas. Isso precisa ser executado antes da perda de qualquer chave. Se uma chave privada não puder ser recuperada, será necessário obter novas chaves privadas obtendo um novo signCert da Autoridade de Certificação. Deve-se também remover e substituir seu certificado de administrador em quaisquer canais aos quais você esteja associado.

<!---
In {{site.data.keyword.blockchainfull_notm}} Platform when a private key is created, two sets of independent key material is generated, in custody of two different entities. Those two sets of key materials are then combined to create the private key.
--->

#### TLS
{: #remote-peer-aws-security-tls}

[A Segurança da Camada de Transporte ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "Uma visão geral do handshake SSL ou TLS") (TLS) é integrada ao modelo de confiança do Hyperledger Fabric. Todos os componentes no {{site.data.keyword.blockchainfull_notm}} Platform usam TLS para autenticação e comunicação entre si. Portanto, os componentes de rede no {{site.data.keyword.blockchainfull_notm}} Platform precisam ser capazes de concluir um handshake TLS com seus peers. Uma implicação disso é que você precisa ativar o intermediário, usando a lista de aplicativos confiáveis, por exemplo, em seu firewall de aplicativos clientes para seu peer.


#### Configuração do Provedor de Serviços de Associação
{: #remote-peer-aws-security-MSP}

Os componentes do IBM Blockchain Platform consomem identidades por meio de Membership Service Providers (MSPs). MSPs associam os certificados que as autoridades de certificação emitem com as funções de rede e canal. Consulte este [tópico](/docs/services/blockchain/certificates.html#managing-certificates-msp) para obter mais informações sobre como os MSPs trabalham com o peer.

#### Segurança do aplicativo
{: #remote-peer-aws-security-appl}

Como todas as chamadas de chaincode são assinadas, o Fabric gerencia a segurança do aplicativo. Além disso, o Fabric também inclui verificações de nível de aplicativo baseadas na ACL.

## Licença e precificação
{: #remote-peer-aws-license-pricing-aws}

Deve-se aceitar uma versão da licença da Community edition do {{site.data.keyword.blockchainfull_notm}} Platform for AWS para poder usar a solução de implementação ativada pela Iniciação Rápida. O uso do {{site.data.keyword.blockchainfull_notm}} Platform for AWS (incluindo todos os pacotes fornecidos por meio da oferta de Iniciação Rápida e os pacotes derivados desses pacotes) não é destinado ao uso de produção. A IBM pode decidir desautorizar o acesso ao código e o uso desse código a qualquer momento.
O contrato de licença de software do {{site.data.keyword.blockchainfull_notm}} Platform for AWS contém mais detalhes sobre os termos de licenciamento. Ao ativar a Iniciação Rápida, é solicitado que você leia e concorde com os termos do contrato.
