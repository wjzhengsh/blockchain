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

# Implementando peers remotos em  {{site.data.keyword.cloud_notm}}  Privado
{: #remote-peer-icp}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


As instruções a seguir descrevem como instalar um peer remoto do {{site.data.keyword.blockchainfull}} Platform no {{site.data.keyword.cloud_notm}} Private (ICP) e conectá-lo a uma rede no {{site.data.keyword.blockchainfull_notm}} Platform.
{:shortdesc}

O {{site.data.keyword.cloud_notm}} Private é uma plataforma de aplicativo para desenvolvimento e gerenciamento de aplicativos conteinerizados no local. O {{site.data.keyword.blockchainfull_notm}} Platform Remote Peers on {{site.data.keyword.cloud_notm}} Private é um produto do {{site.data.keyword.blockchainfull_notm}} empacotado com o ICP para que os clientes implementem peers em seu ambiente local. Depois de instalar o Gráfico de Helm do peer remoto, é possível localizá-lo como um aplicativo no catálogo ICP. O peer remoto para ICP alavanca o armazenamento, a segurança, a criação de log e os serviços de suporte do ICP para gerenciamento de peer. Para obter mais informações sobre o ICP, consulte a [documentação do {{site.data.keyword.cloud_notm}} Private ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/kc_welcome_containers.html "documentação do {{site.data.keyword.cloud_notm}} Private").

Antes de instalar os peers remotos no ICP, revise as [considerações sobre peer remoto](remote_peer.html#remote-peer-limitations).

## Pré-requisitos
{: #prerequisites-icp}

Antes de instalar os peers remotos no ICP, deve-se concluir as etapas a seguir no {{site.data.keyword.cloud_notm}} Private, no sistema local e no {{site.data.keyword.blockchainfull_notm}} Platform.

1. Seu sistema local

  É necessário instalar a CLI do {{site.data.keyword.cloud_notm}} Private [2.1.0.3 ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/manage_cluster/install_cli.html "Instalando a CLI do {{site.data.keyword.cloud_notm}} Private").  
<!---
    - Fabric-ca binaries.
      1. Download the [fabric-ca binaries](https://nexus.hyperledger.org/content/repositories/releases/org/hyperledger/fabric-ca/hyperledger-fabric-ca/) to your local machine and extract them.
      2. Move the executable `fabric-ca` client from the directory where it was downloaded into a new `fabric-ca-remote` directory.
         ```
         mkdir $HOME/fabric-ca-remote
         cp {downloads}/bin/fabric-ca-client $HOME/fabic-ca-remote/
         ```
         {:codeblock}
--->
2. {{site.data.keyword.cloud_notm}}  Particular

  Você precisa de um cluster do [{{site.data.keyword.cloud_notm}} Private ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")]( https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/kc_welcome_containers.html "documentação do {{site.data.keyword.cloud_notm}} Private v2.1.0.3") versão 2.1.0.3. Se você estiver usando o peer remoto para desenvolvimento, teste ou experimentação, poderá instalar o [{{site.data.keyword.cloud_notm}} Private Community Edition ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/installing/install_containers_CE.html "Instalando o {{site.data.keyword.cloud_notm}} Private-CE") gratuitamente.

  **Notas**:
    - É necessário determinar o armazenamento que seu peer usará. Se você usar as configurações padrão, o Gráfico de Helm criará uma nova solicitação de Volume persistente de 8 Gi com o nome de `my-data-pvc` para os dados do peer. Se você selecionar CouchDB como seu banco de dados do livro-razão, o Gráfico de Helm criará outra solicitação de Volume persistente de 8 Gi com o nome de `statedb-pvc` para o banco de dados do livro-razão.
    - Se você não quiser usar as configurações de armazenamento padrão, assegure-se de que uma *nova* storageClass seja configurada durante a instalação do ICP ou o administrador do sistema Kubernetes precisará criar uma storageClass antes de implementar.
    - O [Fornecimento de volume dinâmico ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")]( https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Fornecimento de volume dinâmico") está disponível apenas para os nós amd64 no ICP. Portanto, se o seu cluster incluir uma combinação de nós do trabalhador s390x e amd64, o fornecimento dinâmico não poderá ser usado.
    - Se você não estiver usando o fornecimento dinâmico, [Volumes persistentes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Volumes persistentes") deverão ser criados e configurados com etiquetas que possam ser usadas para refinar o processo de ligação de Solicitação de volume persistente (PVC) do kubernetes.

  Além dos requisitos de hardware do ICP, o peer remoto requer pelo menos:
    - 2x vCPU
    - 2 GB de RAM
    - 4 GB de espaço para chaincode
    - 10 GB de espaço para o livro-razão com capacidade de aumento, conforme o livro-razão for expandido

3. {{site.data.keyword.blockchainfull_notm}} Platform  

  Para usar um peer remoto, deve-se ter uma organização que seja membro de uma rede de blockchain que esteja hospedada no {{site.data.keyword.blockchainfull_notm}} Platform. É necessário usar o Monitor de rede no {{site.data.keyword.cloud_notm}} para acessar as credenciais de rede e os terminais de API de sua rede. Se você não for membro de nenhuma rede de blockchain, precisará criar ou associar-se a uma rede. Para obter mais informações, consulte [Criando uma rede](../get_start.html#creating-a-network) ou [Associando-se a uma rede](../get_start.html#joining-a-network).

## Importar o Gráfico Helm para o ICP

O pacote do {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer está disponível no [Github ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")]( https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "IBM Blockchain Remote Peer"). O Gráfico de Helm é importado para o console do ICP usando a linha de comandos. Em sua máquina local, conclua as etapas a seguir para importar o Gráfico de Helm:

1. Faça download do arquivo `.tgz` do Gráfico de Helm do [Github ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "Gráfico de Helm do IBM Blockchain Remote Peer").<!--[Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage "Passport Advantage Online")-->

2. Se você ainda não estiver conectado, efetue login no cluster do ICP por meio da CLI do ICP e, em seguida, efetue login no registro de imagem privada do Docker.  
  ```
  bx pr login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

  No código,  `<cluster_CA_domain>`  é o domínio da autoridade de certificação (CA). Se você não tiver especificado um domínio de CA, o valor
padrão será `mycluster.icp`.

3. No diretório em que você armazenou o Gráfico de Helm transferido por download, execute o comando a seguir na CLI do ICP para importar o Gráfico de Helm para o cluster do ICP. Substitua `<helm_chart_from_github>` pelo nome do arquivo `.tgz` transferido por download do GitHub.

  ```
  bx pr load-helm-chart --archive <helm_chart_from_github> --clustername <cluster_CA_domain>
  ```
  {:codeblock}

  Quando esse comando for concluído com sucesso, será possível ver algo semelhante às informações a seguir:

  ```  
  Loading helm chart
  Loaded helm chart

  Synch charts
  Synch started
  OK
  ```  

  O quadro do Gráfico de Helm do {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer deve agora estar visível na página Catálogo do ICP. Clique no botão **Catálogo** no console do ICP para verificar se a importação foi bem-sucedida.

### Implementar o peer remoto

As etapas a seguir descrevem o processo necessário para implementar o peer remoto no ICP. Antes de configurar o peer remoto no ICP, recupere algumas informações sobre terminais do {{site.data.keyword.blockchainfull_notm}} Platform e crie uma identidade de usuário para o peer remoto. Será necessário usar essas informações em etapas posteriores.

## Etapa um: Recuperar as informações de configuração do peer remoto
{: #network-endpoints}

É necessário fornecer os terminais de API de sua rede para o peer remoto durante a configuração. Esses terminais permitem que um peer remoto localize e se conecte à rede no {{site.data.keyword.blockchainfull_notm}} Platform. Na tela **Visão geral** de seu Monitor de rede, clique no botão **Configuração do peer remoto**.

![Configuração do peer remoto](../images/myresources_starter.png "Configuração do peer remoto")
*Figura 1. Configuração do Peer Remoto*

Uma janela pop-up é aberta e exibe os valores dos campos a seguir. Salve esses valores para quando configurar o peer remoto.

  - ** ID da Rede **
  - ** MSP da Organização **
  - ** Nome da Autoridade de Certificação (CA) **
  - ** URL da Autoridade de Certificação (CA) **
  - ** Certificado TLS da Autoridade de Certificação (CA) **

É possível copiar e colar os campos individualmente ou salvá-los como um arquivo JSON clicando no link **Download**. **Observe** que se você fizer download das informações em JSON, precisará converter o certificado TLS no formato PEM antes de fornecê-lo ao peer remoto. É possível usar a CLI para converter o certificado no formato PEM emitindo o comando `echo -e "<CERT>" > catls.pem`.

## Etapa Dois: Registrar um Período Remoto
{: #register-peer}

É necessário incluir uma nova identidade de peer em sua organização no {{site.data.keyword.blockchainfull_notm}} Platform antes que o peer remoto possa se associar à rede. Conclua as etapas a seguir para inscrever e registrar um peer remoto.

1. Efetue login no {{site.data.keyword.blockchainfull_notm}} Platform e acesse o Monitor de rede. Na tela "Autoridade de certificação", é possível visualizar todas as identidades registradas com sua organização, como seus aplicativos de administrador ou de cliente.
  ![Tela CA](../images/CA_screen_starter.png "Tela CA")
  *Figura 2. Tela CA*

2. Clique no botão **Incluir usuário** na parte superior da tela. Uma tela pop-up é aberta e permite registrar seu peer remoto após o preenchimento dos campos a seguir. Salve os valores do ID e do Segredo, pois serão necessários ao configurar seu peer.
  - **ID:** o nome de uso de seu peer, que é referido como `enroll ID` ao configurar o peer. **Salve esse valor** para uso futuro ao implementar o peer remoto.
  - **Segredo:** a senha de seu peer, que é referida como `enroll Secret` ao configurar o peer. **Salve esse valor** para uso futuro ao implementar o peer remoto.
  - ** Tipo: **  Selecione  ` peer `  para este campo.
  - **Afiliação:** essa é a afiliação sob sua organização, `org1`, por exemplo, à qual seu peer remoto pertencerá. Selecione uma afiliação existente na lista suspensa ou digite uma nova.
  - **Máximo de inscrições:** é possível usar esse campo para limitar o número de vezes que você pode inscrever ou gerar certificados usando essa identidade. Se não especificado, o valor será padronizado para inscrições ilimitadas.

  Depois de concluir os campos, clique em **Enviar** para registrar o peer remoto. O peer registrado é então listado na tabela como uma identidade em sua organização.

## Etapa três: Configurar e instalar um peer remoto

Depois de registrar a identidade do peer no Monitor de rede, será possível configurar e instalar o peer remoto no ICP usando as instruções do console do ICP abaixo.

<!-- Issue #228 or the [helm CLI](#icp-helm-cli). -->

1. Efetue login no console do ICP e clique no link **Catálogo** no canto superior direito.
2. Localize o Gráfico de Helm do {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer e clique nele para abri-lo. É possível ver um arquivo leia-me que inclui informações sobre a instalação e a configuração do Gráfico de Helm.
3. Clique em  ** Configurar **. Na página Configuração, conclua as definições de configuração do Gráfico de Helm e confirme se você leu e concorda com o contrato de licença.
4. Clique em  ** Instalar **.

### Parâmetros de configuração
{: #icp-configuration-parms}

É necessário fornecer as informações que seu peer remoto precisa para se conectar à rede no {{site.data.keyword.blockchainfull_notm}} Platform.
A tabela a seguir lista os parâmetros configuráveis do gráfico do {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer e seus valores padrão.


|  Parâmetro     | Descrição    | Padrão  | Requerido |
| --------------|-----------------|-------|------- |
|**Configuração** | **Configure o nome da liberação e o namespace de destino do peer remoto** |  ||
| ` Nome da liberação `| O nome da liberação de helm. | nenhum | sim |
| ` Namespace de destino `| O namespace do Kubernetes. | padrão | sim |
| | | | |
|**Configuração do cluster** |**Informações de configuração do cluster** | ||
| ` Arquitetura do nó do trabalhador `| Selecione a arquitetura da plataforma de nuvem (amd64 ou S390x).| Arquitetura de detecção automática com base no nó principal | sim |
| ` Imagem `| Caminho para o Gráfico Helm. | Preenchido automaticamente para o caminho instalado; não mude esse valor | sim |
| | | | |
|**Rede de blockchain** | ** As informações de configuração de rede necessárias para o peer remoto**| | |
| `Network ID`| Valor do ID de rede localizado no Monitor de rede do {{site.data.keyword.blockchainfull_notm}} Platform. Clique no botão ` Configuração do peer remoto` na tela "Visão geral", em seguida, copie e cole essas informações aqui.| nenhum | sim |
| ` MSP da Organização `| Valor do ID do MSP da organização localizado no Monitor de rede do {{site.data.keyword.blockchainfull_notm}} Platform. Clique no botão ` Configuração do peer remoto` na tela "Visão geral", em seguida, copie e cole essas informações aqui.|nenhum|sim|
| ` Nome da Autoridade de Certificação (CA) `| Valor do nome da CA localizado no Monitor de rede do {{site.data.keyword.blockchainfull_notm}} Platform. Clique no botão `Configuração do peer remoto` na tela "Visão geral" e copie e cole essas informações aqui.|nenhum|sim|
| `Certificate Authority (CA) URL`| Valor da URL da CA localizado no Monitor de rede do {{site.data.keyword.blockchainfull_notm}} Platform. Clique no botão `Configuração do peer remoto` na tela "Visão geral" e copie e cole essas informações aqui. | nenhum | sim |
| `Certificate Authority (CA) TLS Certificate` | Sequência de certificados TLS de CA no Monitor de rede do {{site.data.keyword.blockchainfull_notm}} Platform. Clique no botão `Configuração do peer remoto` na tela "Visão geral" e copie e cole essas informações aqui. | nenhum | sim |
| | | | |
|**Identidade do peer remoto** | **O ID e o segredo de inscrição usados para registrar o peer remoto**| | |
| ` ID de enrolo do peer `| É o ID de inscrição inserido na tela "Autoridade de certificação" do Monitor de rede do {{site.data.keyword.blockchainfull_notm}} Platform. | nenhum | sim |
| ` Segredo do enrolo do peer `| É o Segredo de inscrição inserido na tela "Autoridade de certificação" do Monitor de rede do {{site.data.keyword.blockchainfull_notm}} Platform.| nenhum | sim |
| | | | |
|**Banco de dados do peer remoto** | **Tipo de banco de dados do livro-razão**| | |
| ` Banco de dados do Ledger `| O [banco de dados de estado](../glossary.html#state-database) usado para armazenar o livro-razão do canal. O peer remoto precisa usar o mesmo banco de dados que sua [rede de blockchain](../v10_dashboard.html#network-preferences). | nenhum | sim |
| ` Imagem do CouchDB `| Será aplicada somente se CouchDB tiver sido selecionado como o banco de dados do livro-razão. | Preenchido automaticamente para o caminho instalado, não mude esse valor. | sim |
|**Persistência de dados** | Ative a capacidade de persistir dados após reinicializações ou falhas do cluster. Consulte [armazenamento no Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/ "Volumes") para obter mais informações. | | |
| ` Persistência de dados ativada `| Os dados de estado estarão disponíveis quando o contêiner for reiniciado. *Se desmarcado, todos os dados serão perdidos no caso de um failover ou de uma reinicialização do pod.* | verificado | não |
| ` Utilizar Provisão Dinâmica `| Use o armazenamento dinâmico Kubernetes. | verificado | não |
| | | | |
|**Configuração de volume persistente** | **Solicitação de volume persistente a ser usada por seu peer** |  |  |
| ` Persistência de solicitação de volume `| Insira um nome para sua nova Solicitação de volume persistente (PVC) que será criada. | my-data-pvc | não |
| ` Nome da classe de armazenamento `| Nome da classe de armazenamento da chose. | Em branco se estiver criando uma nova PVC; caso contrário, especifique a classe de armazenamento associada à PVC existente. | não |
| ` solicitação de volume existente `| Especifique o nome de uma Solicitação de volume existente e deixe todos os outros campos em branco. | novo nome de solicitação | não |
| ` Modo de acesso ao armazenamento `| Especifique o armazenamento [modo de acesso ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modos de acessos") para o PVC.  | ReadOniraMuitos| não |
| ` Rótulo do seletor `| [Etiqueta do seletor ![Ícone de link externo](../images/external_link.svg "Ícone de link externo") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas e seletores") da PVC.| padrão | não |
| ` Valor do Seletor `| [Valor do seletor ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas e seletores") da PVC. | padrão | não |
| ` Tamanho da solicitação de volume `| O tamanho da Solicitação de volume deve ser maior que 2 Gi. | 8Gi  | sim |
| | | | |
|**Persistência de dados do banco de dados de estado** | **Persistir dados depois que o contêiner for reiniciado ou falhar. Consulte [armazenamento no Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/ "Volumes") para obter mais informações** | | |
| ` Persistência de dados ativada `| Os dados do livro-razão estarão disponíveis quando o contêiner for reiniciado. *Se desmarcado, todos os dados serão perdidos no caso de um failover ou reinicialização de pod.*| verificado | não |
| ` Utilizar Provisão Dinâmica `| Use o armazenamento dinâmico Kubernetes.| verificado | não |
| | | | |
|**Configuração do volume persistente do banco de dados de estado** |**Solicitação de volume persistente a ser usada para o banco de dados de estado. Consulte [armazenamento no Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/ "Volumes") para obter mais informações.** |||
| ` Persistência de solicitação de volume `| Insira um nome ou use o padrão para sua nova Solicitação de volume persistente do CouchDB que será criada. | nome_do_pvc | não |
| ` Nome da classe de armazenamento do banco de dados de|Escolha o nome da classe de armazenamento.| nenhum | não |
| ` solicitação de volume existente do banco de dados de estado| Para usar uma solicitação de volume existente, insira o nome do PVC e deixe todos os outros campos em branco. | nenhum | não |
| ` Modo de acesso de armazenamento do banco de dados de| Especifique o armazenamento [modo de acesso ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modos de acessos") para o PVC. | ReadOniraMuitos| não |
| ` Rótulo do seletor `| [Etiqueta do seletor ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas e seletores") da PVC.| padrão | não |
| ` Valor do Seletor `| [Valor do seletor ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Etiquetas e seletores") da PVC.| padrão | não |
| ` Tamanho da reivindicação do volume do banco de dados| Tamanho do Sinistro de Volume Persistente. | 8Gi | não |
| | | | |
| **Recursos do peer** | **Número mínimo e máximo de CPUs e memórias para o contêiner de peer** | | |
| `Peer CPU request` | Número mínimo de CPUs a serem alocadas para o peer. | 2 | sim |
| `Peer CPU limit` | Número máximo de CPUs a serem alocadas para o peer.| 2 | sim |
| `Peer Memory request` | Quantia mínima de memória a ser alocada para o peer. | 4Gi | sim |
| `Peer Memory limit` | Quantia máxima de memória a ser alocada para o peer. | 4Gi | sim |
| | | | |
|**Recursos do CouchDB** | **Número mínimo e máximo de CPU e memória alocadas para o contêiner do CouchDB. Esta seção se aplicará apenas se CouchDB tiver sido selecionado como o banco de dados de estado**| | | |
| `CouchDB CPU request` | Número mínimo de CPUs a serem alocadas para o CouchDB.| 2 | sim |
| `CouchDB CPU limit` | Número máximo de CPUs a serem alocadas para o CouchDB. | 2 | sim |
| `CouchDB Memory request` | Quantia mínima de memória a ser alocada para o CouchDB.| 4Gi | sim |
| `CouchDB Memory limit` | Quantia máxima de memória a ser alocada para o CouchDB. | 4Gi | sim |

Quando CouchDB é selecionado como o banco de dados do peer remoto, dois contêineres são criados no pod, um para o peer e um para o CouchDB.
O contêiner do Peer inclui uma única montagem de volume para a PVC do Peer que armazena os blocos e as transações no sistema de arquivos. O contêiner do CouchDB monta a PVC do banco de dados de estado do peer que contém os dados do livro-razão.

Quando LevelDB for selecionado como o banco de dados do peer Remoto, um único contêiner será criado no pod para executar ambos os processos, do peer e do
LevelDB. Esse contêiner tem duas montagens de volume, uma para a PVC do Peer e a segunda montagem de volume é para a PVC do banco de dados de estado do peer que contém os dados do livro-razão.

| Seleção do Banco de Dados do Peer  | Conteúdo do contêiner nº 1  | Conteúdo do Contêiner #2 |
| --------------|-----------------|---------------|
| CouchDB | Peer remoto que monta a PVC do Peer| CouchDB que monta a PVC do banco de dados de estado |
| LevelDB | Peer remoto e LevelDB que montam a PVC do Peer e a PVC do banco de dados de estado | n / a |

<!-- Issue #228

### Using the helm command line to install the helm release
{: #icp-helm-cli}

Alternatively, you can use the helm CLI to install the helm release. Before running the helm install command, complete these
[steps ![External link icon](../images/external_link.svg "External link icon")](https://www.ibm.com/support/knowledgecenter/SSBS6K_2.1.0.3/app_center/add_int_helm_repo_to_cli.html "Adding the internal Helm repository to Helm CLI") to add your cluster's helm repository to the helm CLI environment.

To install a release with the default configuration use the `helm install` CLI command, replacing the following values in `< >` based on parameter information in the table below.

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--set env.networkid=<network_id> \
--set env.key=<API_key> \
--set env.secret=<API_secret> \
--set peeridentity.peerUser=<peer_user> \
--set peeridentity.peerPassword=<peer_password> \
--set dataPVC.size=<data_volume_claim_size> \
--set couchdbPVC.size=<couchDB_volume_claim_size> \
--tls
```

where:
- `<release name>` represents the name you want to give your helm release.
- `<helm_chart>`  represents the name of the Helm Chart imported into the catalog.
- `<helm_chart_version>` represents the version of the Helm Chart imported into the catalog.


For example:
```
helm install --name jnchart2 mycluster/ibm-blockchain-platform-remote-peer-prod-1.0.0 \
--version 1.0.2 \
--set env.networkid=6f037b0186064edd9f0032ksl2dcd3c6ee \
--set env.key=PeerOrg1 \
--set env.secret=8s1MOzVd9YxmdmRvm_owlYXX0Gr5XGx30ol2o0vNo18Zvba5jM3xCIOuQ-yEGBn \
--set peeridentity.peerUser=peer621 \
--set peeridentity.peerPassword=peer621 \
--set dataPVC.size=8Gi \
--set couchdbPVC.size=8Gi \
--tls
```

Optionally, a `.yaml` file containing the parameters can be passed to the `helm install` command
using the following format:

```
helm install --name <release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

where `--values <customvalues.yaml>` could also be expressed as `-f <customvalues.yaml>`.

The `values.yaml` file includes all of the parameter names and is included in the archive downloaded from
Docker Hub.

-->

<!-- Commenting out information per issue #228

**Note:** The following error can occur when running the `helm install` command if the
helm CLI version on your local machine is ahead of helm cli version on your server.

```
Error: incompatible versions client[v2.9.1] server[v2.7.3+icp]
```

See the [troubleshooting topic](remote_peer_operate_icp.html#icp-troubleshooting) for instructions on resolving this error.

-->


<!--
### Generating your own peer TLS certificates
{: #generate-peer-tls-certs}

You can leave the `Peer TLS CA certificate`, `Peer TLS certificate`, and `Peer TLS key` fields empty or provide your own certificates.

- If you leave all these three fields empty, your remote peer will generate a set of default values for these fields when the remote peer starts.

  **Note:** If you specify values for one or two fields of these three fields, the remote peer will generate and use the default values for all three fields.

- If you don't want to use the default values, you can generate your own certificates and key and specify them in the three fields.

  **Note:** When you specify your own certificates in the three fields, ensure that the value is in one line. You can connect the content from each line with `\r\n`. For example, your certificate value might look like the following example.

  ```
  -----BEGIN CERTIFICATE-----
  MIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET
  MBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2
  MDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ
  4kyRt4UAWNvEswnLcaYCtS4ZYa8=
  -----END CERTIFICATE-----
  ```

  You need to use `\r\n` to connect all lines as shown below.

  ```
  ----BEGIN CERTIFICATE-----\r\nMIIEkDCCAnigAwIBAgIBCDANBgkqhkiG9w0BAQsFADAiMQswCQYDVQQGEwJVUzET\r\nMBEGA1UEAwwKdGVzdGNhLmNvbTAeFw0xODA4MDMwMzM2MDFaFw0yMTA0MjkwMzM2\r\nMDFaMCMxCzAJBgNVBAYTAlVTMRQwEgYDVQQDDAt0ZXN0b3JnLmNvbTCCASIwDQYJ\r\n4kyRt4UAWNvEswnLcaYCtS4ZYa8=\r\n-----END CERTIFICATE-----
  ```

Before you generate certificates, you need to create a folder that contains the `openssl-ca.cnf` and `openssl-server.cnf` files, and an empty `index.txt` file.

If you haven't, install [openssl](https://github.com/openssl/openssl) on your system.

1. Run the following command to generate **Peer TLS CA certificates**.
  ```
  openssl req -x509 -newkey rsa:4096 -sha256 -nodes -out ./cacert.pem -keyout ./cakey.pem -outform PEM  -config openssl-ca.cnf -subj "/C=US/CN=testca.com"
  ```

  This command generates the `cacert.pem` and `cakey.pem` files. The `cacert.pem` file is your Peer TLS CA certificate.

2. Run the following command to generate **Peer TLS key**.
  ```
  openssl req -newkey rsa:2048 -sha256 -nodes -out ./servercert.csr -outform PEM -keyout ./peer-key.pem -config openssl-server.cnf  -subj "/C=US/CN=testorg.com"
  ```

  This command generates `peer-key.pem` and `servercert.csr` files. The `peer-key.pem` file is your Peer TLS key.

3. Run the following command to generate **Peer TLS certificate**.
  ```
  openssl ca -batch  -config ./openssl-ca.cnf -policy signing_policy -extensions signing_req -keyfile ./cakey.pem -cert ./cacert.pem -out ./peer-cert.pem -infiles ./servercert.csr
  ```

  Esse comando gera o arquivo `peer-cert.pem`, que é o seu certificado TLS do Peer.

--->

## Etapa quatro: Verificar a instalação do peer remoto
{: #verify-installation-icp}

Depois de instalar o peer remoto, é possível visualizar o log do peer para verificar se a sua instalação do peer foi bem-sucedida.

1. Depois de clicar em **Instalar**, clique no botão **Visualizar liberação de Helm** na janela pop-up. Role para baixo até a seção  ** Pods ** . Quando o status dos Pods mostrar **Em execução**, será possível localizar a sua instância de peer remoto sob a seção **Implementações**.

2. No console do ICP, clique no ícone **Menu** no canto superior esquerdo. Na lista de menu, clique em **Cargas de trabalho** > **Implementações**. Na tabela de implementação, clique na instância do peer remoto criada e a tela de visão geral da implementação será aberta. Clique na guia **Logs** para visualizar os logs do peer remoto. É possível inserir a sequência `Started` no campo de procura.

  - Se o peer remoto for iniciado com sucesso, será possível ver os logs semelhantes ao exemplo a seguir:
    ```
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func2 -> DEBU 196[0m chaincode-support started for qscc-1.1.0
    [36m2018-06-25 14:22:36.929 UTC [inproccontroller] func1 -> DEBU 197[0m chaincode started for qscc-1.1.0
    2018-06-25 14:22:36.942 UTC [nodeCmd] serve -> INFO 1cc[0m Started peer with ID=[name:"fabric-peer" ], network ID=[fa74d88bbd9f46a48a6e4c9986e84228], address=[10.1.156.81:7051]
    ```

  - Se você não vir nenhum log na tela, seu peer remoto não foi iniciado com sucesso. Para visualizar logs adicionais para descobrir o problema, clique no ícone **Menu** no canto superior esquerdo e, em seguida, clique em **Cargas de trabalho** > **Liberações de Helm**. Clique em sua liberação do helm para abri-la. Clique no link **Visualizar logs** próximo ao **Pod** associado para visualizar logs de gráfico adicionais na interface do Kibana.

3. Para verificar se o peer remoto pode se conectar à sua rede do {{site.data.keyword.blockchainfull}} Platform, é possível
executar um comando peer da CLI dentro do contêiner de peer remoto. Execute o comando da CLI `peer channel fetch` para buscar o bloco genesis por meio de um canal:

   1. Se ainda não estiver conectado ao cluster do ICP, siga estas [instruções](remote_peer_operate_icp.html#remote-peer-kubectl-configure) para se conectar a ele e usar a CLI de dentro do contêiner do peer.

   2. A sua organização precisa ser incluída em um canal na rede antes que você possa buscar o bloco genesis.

     - É possível iniciar um novo canal para o peer remoto. Como o inicializador de canais, é possível incluir automaticamente a sua organização durante a [criação do canal](create_channel.html#creating-a-channel). Observe que você precisa ter pelo menos um peer no {{site.data.keyword.blockchainfull_notm}} Platform antes de poder criar um canal no Monitor de rede.  

     - Outro membro da rede de blockchain também pode incluir sua organização em um canal existente usando uma [atualização de canal](create_channel.html#updating-a-channel). Um membro do canal com peers no {{site.data.keyword.blockchainfull_notm}} Platform pode usar o Monitor de rede para incluir a organização no canal, mesmo que você não hospede nenhum peer na plataforma.

      O peer remoto faz upload de seu certificado de assinatura durante a instalação, para que você precise apenas sincronizar o certificado com o canal. Na tela "Canais" do Monitor de rede, localize o canal que sua organização associou e selecione **Sincronizar certificado** na lista suspensa sob o cabeçalho **Ação**. Essa ação sincroniza os certificados em todos os peers no canal.

   3. Recupere as informações de configuração de seu arquivo `creds.json` disponível na tela "Visão geral" de seu Monitor de rede. Clique em **Perfil de conexão** e, em seguida, em **Download**.

     - Localize a URL do solicitador procurando **solicitadores**, que está localizada em `orderers > url`. Anote a URL que inicia com o nome da rede. A URL é semelhante ao exemplo a seguir:

       ```
       ash-zbc07b.4.secure.blockchain.ibm.com:21239
       ```

     - Localize o nome de sua organização procurando **organizações**. Essa deve ser a mesma organização que usada para registrar seu peer remoto. É possível localizar o nome de sua organização junto com seu `mspid` associado. Anote o valor de `mspid`.

    4. Execute os comandos a seguir para configurar as variáveis de ambiente no contêiner do peer.

       ```
       export ORDERER_1=<ORDERER_URL>
       export CHANNEL=<CHANNEL_NAME>
       export CC_NAME=<CC_NAME>
       export ORGID=<ORGANIZATION_MSP_ID>
       export PEERADDR=<PEER_ADDR>
       ```
       {:codeblock}

       Substitua os campos por suas próprias informações.
         - Substitua `<ORDERER_URL>` pelo nome do host e pela porta do solicitador por meio do arquivo `creds.json`.
         - Substitua `<CHANNEL_NAME>` pelo nome do canal ao qual o peer se associa.
         - Substitua `<CC_NAME>` por qualquer nome para se referir ao chaincode.
         - Substitua `<ORGANIZATION_MSP_ID>` pelo nome da organização por meio do arquivo `creds.json`.
         - Substitua `<PEER_ADDR>` por `localhost:7051`

       Por exemplo:

       ```
       export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
       export CHANNEL=defaultchannel
       export CC_NAME=mycc
       export ORGID=PeerOrg1
       export PEERADDR=localhost:7051
       ```
       {:codeblock}

   5. Execute o comando da CLI do peer a seguir para buscar o bloco genesis do canal.

     ```
     CORE_PEER_TLS_ROOTCERT_FILE=/mnt/certs/tls/cacert.pem CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/mnt/crypto/peer/peer/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /mnt/certs/tls/cacert.pem --tls
     ```
     {:codeblock}

     **Nota:** é possível que, ao executar qualquer um desses comandos da CLI, você possa receber o aviso a seguir, que pode ser ignorado com segurança.

     ```
     [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
     /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /mnt/crypto/peer/peer/msp/intermediatecerts/<intermediate cert name>.pem
     ```

     Verifique se o comando funcionou com sucesso e se o bloco genesis foi incluído no contêiner do peer executando o comando a seguir:

     ```
     ls *.block
     ```
     {:codeblock}

     Você saberá que o bloco genesis foi incluído com sucesso ao ver algo semelhante ao exemplo a seguir:

     ```
     defaultchannel_0.block
     ```

## O que vem a seguir
{: #whats-next-icp}

Depois de configurar o peer remoto no ICP, será possível concluir várias etapas operacionais antes de poder enviar transações e ler o livro-razão distribuído por meio da rede de blockchain. Para obter mais informações, consulte [Operar um peer remoto](remote_peer_operate_icp.html).

## Alta disponibilidade
{: #high-availability}

Como uma boa prática de alta disponibilidade, é altamente recomendável implementar um mínimo de dois peers por organização.

Conclua as etapas a seguir para implementar peers remotos altamente disponíveis no ICP:

1. Revise como os [configuram seu Cluster do ICP para Alta disponibilidade ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/installing/high_availability.html "Clusters de alta disponibilidade do {{site.data.keyword.cloud_notm}}").

2. Instale o peer várias vezes em seu cluster para implementar peers em diferentes nós do trabalhador. O Gráfico de Helm contém uma política de antiafinidade que se baseia em seu ID de rede e OrgID. O cluster do ICP detecta se os seus peers estão se conectando à mesma rede por meio da mesma organização e tenta organizar esses peers em diferentes nós do trabalhador.  
  **Nota:** embora a política "prefira" colocar peers em diferentes nós do trabalhador, isso não é obrigatório. Por exemplo, se você tiver apenas um nó trabalhador, todos os seus peers serão implementados nesse mesmo nó.

Se você implementar pelo menos dois peers, também será necessário configurar os seus [aplicativos para alta disponibilidade](../v10_application.html#ha-app).

## Considerações de segurança
{: #icp-security}

Os peers remotos são implementados fora do {{site.data.keyword.blockchainfull}} Platform, portanto, você é responsável por gerenciar a segurança do peer remoto. Isso inclui áreas importantes de segurança que as redes do Enterprise Plan fornecem, como gerenciamento de chave e criptografia de dados. Revise os tópicos a seguir ao considerar a segurança para seus peers remotos.

### Controle de identidade e de acesso
{: #icp-security-node}

Fora do {{site.data.keyword.cloud_notm}}, é necessário configurar o gerenciamento de identidade, a autenticação e o controle de acesso para usuários que interagem com os peers remotos. É possível usar o [gerenciamento de usuários do ICP ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.3/user_management/access_overview.html "Segurança") para configurar esses recursos.

### Segurança de dados
{: #icp-security-data}

O Enterprise Plan do {{site.data.keyword.blockchainfull}} Platform usa a criptografia de disco inteira que se baseia em [criptografia de chave simétrica ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Criptografia simétrica") para proteger todos os dados usados pelas redes. Deve-se executar etapas semelhantes em seu próprio ambiente para proteger os dados do peer remoto.

### Residência de dados
{: #icp-data-residency}

A residência de dados determina que o processamento e o armazenamento de todos os dados do livro-razão do blockchain permaneçam dentro da fronteira de um único país.
Para obter mais informações sobre como a residência de dados pode ser realizada, consulte [Residência de dados](remote_peer.html#data-residency).

### Gerenciamento de chave
{: #icp-security-key-management}

O gerenciamento de chave é um aspecto crítico da segurança do peer remoto. Se uma chave privada estiver comprometida ou perdida, os agentes hostis poderão ser capazes de acessar os dados e a funcionalidade do peer remoto. O Enterprise Plan do {{site.data.keyword.blockchainfull_notm}} Platform usa os [Hardware Security Modules](../glossary.html#hsm) (HSM) para armazenar as chaves privadas de sua rede. O HSM é um dispositivo físico que evita que outras partes acessem a sua chave privada.

Ao implementar um peer remoto no ICP, você é responsável por gerenciar suas chaves privadas. Embora o {{site.data.keyword.blockchainfull_notm}} Platform gere suas chaves privadas, essas chaves não são armazenadas na Plataform. É essencial assegurar-se de que você armazene suas chaves em um local seguro para que elas não sejam comprometidas. É possível localizar a chave privada de seu peer remoto na pasta de keystore do MSP do peer, no diretório `/mnt/crypto/peer/peer/msp/keystore/` dentro do contêiner do peer. Para obter mais informações sobre os certificados dentro de seu peer remoto, consulte a seção [Membership Services Provider](../certificates.html#msp) do tutorial [Gerenciando certificados no {{site.data.keyword.blockchainfull_notm}} Platform](../certificates.html).

É possível usar o Caução de chave para recuperar chaves privadas perdidas. Isso precisa ser executado antes da perda de qualquer chave. Se uma chave privada não puder ser recuperada, será necessário obter novas chaves privadas registrando uma nova identidade com sua Autoridade de certificação. Também será necessário remover e substituir o seu signCert de qualquer canal ao qual tenha se associado.

<!---
No IBP, quando uma chave privada é criada, dois conjuntos de material de chave independente são gerados sob custódia de duas entidades diferentes. Esses dois conjuntos de materiais chave são, então, combinados para criar a chave privada.
--->

### TLS
{: #icp-security-tls}

A [Segurança da Camada de Transporte ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "Uma visão geral do handshake SSL ou TLS") (TLS) é integrada ao modelo de confiança do Hyperledger Fabric. Todos os componentes no {{site.data.keyword.blockchainfull_notm}} Platform usam TLS para autenticação e comunicação entre si. Portanto, os componentes de rede no {{site.data.keyword.blockchainfull_notm}} Platform precisam ser capazes de concluir um handshake TLS com seus peers remotos. Uma implicação disso é que você precisa ativar a passagem usando a lista de aplicativos confiáveis, por exemplo, em seu firewall da web, de apps do cliente para seus peers.

É possível usar o TLS mútuo, que requer a autenticação de duas vias (servidor-cliente) em vez de uma via (apenas servidor), para proteger a comunicação entre as redes do aplicativo e do Enterprise Plan. É possível usar o Monitor de rede [para ativar o TLS mútuo](../v10_dashboard.html#mutual-tls) para os peers no {{site.data.keyword.blockchainfull_notm}} Platform. Para ativar o TLS mútuo no peer remoto, siga as instruções para [ativar o TLS mútuo para nós do peer ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/enable_tls.html "Protegendo a comunicação com a Segurança da Camada de Transporte") na documentação do Hyperledger Fabric. É altamente recomendável que você ative o TLS mútuo para seus aplicativos.

### Configuração do Provedor de Serviços de Associação
{: #icp-security-MSP}

Os componentes do {{site.data.keyword.blockchainfull_notm}} Platform consomem identidades via Membership Service Providers (MSPs). MSPs associam os certificados que as autoridades de certificação emitem com as funções de rede e canal. Para obter mais informações sobre como os MSPs trabalham com o peer remoto, consulte [Membership Service Providers (MSPs)](../certificates.html#msp).

### Segurança do aplicativo
{: #icp-security-appl}

Como todas as chamadas de chaincode são assinadas, o Fabric gerencia a segurança do aplicativo. Além disso, o Fabric também inclui verificações de nível de aplicativo baseadas na ACL.

## Licença e precificação
{: #license-pricing-icp}

O {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer on {{site.data.keyword.cloud_notm}} Private é uma edição Beta grátis adequada para avaliação e experimentação e é implementada como um aplicativo ICP. É possível acessar o [pacote do Github ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-remote-peer "{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer")<!--[IBM Passport Advantage Online ![External link icon](../images/external_link.svg "External link icon")](https://www-01.ibm.com/software/passportadvantage) to purchase the license and-->e fazer download do Gráfico de Helm do [Gráfico de Helm ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/IBM/charts/tree/master/repo/stable/ibm-blockchain-platform-remote-peer-0.9.0.tgz "{{site.data.keyword.blockchainfull_notm}} Platform Remote Peer").

<!--Purchasing the remote peer on IBM Passport Advantage includes the license, a Helm Chart installation package, a Quick Start Guide, and a default level support for {{site.data.keyword.blockchainfull_notm}} Platform Remote Peer.-->
