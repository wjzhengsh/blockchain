---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Implementando peers no {{site.data.keyword.cloud_notm}} Private
{: #peer-icp}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

As instruções a seguir descrevem como implementar um peer do {{site.data.keyword.blockchainfull}} Platform no {{site.data.keyword.cloud_notm}} Private (ICP). Essas instruções permitem que você se conecte a um {{site.data.keyword.blockchainfull_notm}} Platform no ICP. Se você desejar conectar um peer a uma rede do Starter Plan ou do Enterprise Plan no {{site.data.keyword.cloud_notm}}, consulte [Implementando peers para conectar-se ao Starter Plan ou ao Enterprise Plan](peer_deploy_ibp.html).
{:shortdesc}

Antes de implementar um peer, revise as [Considerações e limitações](../ibp-for-icp-about.html#ibp-icp-considerations).

## Recursos necessários
{: #peer-resources-required}

Assegure-se de que o sistema do ICP atenda aos requisitos mínimos de recurso de hardware:

| Componente | vCPU | RAM | Disco para armazenamento de dados |
|-----------|------|-----|-----------------------|
| Peer | 2 | 2 GB | 50 GB com a capacidade de expandir |
| CouchDB para peer | 2| 2 GB |50 GB com a capacidade de expandir |

 **Notas:**
 - Um vCPU é um núcleo virtual que será designado a uma máquina virtual ou a um núcleo do processador físico se o servidor não for particionado para máquinas virtuais. É necessário considerar os requisitos de vCPU ao decidir o virtual processor core (VPC) para sua implementação no ICP. A VPC é uma unidade de medida para determinar o custo de licenciamento de produtos IBM. Para obter mais informações sobre os cenários para decidir o VPC, consulte [Virtual processor core (VPC) ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Esses níveis de recursos mínimos são suficientes para teste e experimentação. Para um ambiente com um grande volume de transações, é importante alocar uma quantia suficientemente grande de armazenamento; 250 GB para seu peer como um exemplo. A quantidade de armazenamento a ser usada depende do número de transações e do número de assinaturas que são necessárias de sua rede. Se você está prestes a esgotar o armazenamento em seu peer ou solicitador, deve-se implementar um novo peer ou solicitador com um sistema de arquivos maior e permitir que ele seja sincronizado por meio de seus outros componentes nos mesmos canais.

## Armazenamento
{: #storage}

É necessário determinar o armazenamento que seu peer usará. Se você usar as configurações padrão, o gráfico do Helm criará uma nova Solicitação de Volume Persistente (PVC) de 8 Gi com o nome de `my-data-pvc` para seus dados do peer e outra PVC de 8 Gi com o nome de `statedb-pvc` para seu banco de dados de estado.

Se você não desejar usar as configurações de armazenamento padrão, assegure-se de que um *novo* `storageClass` esteja configurado durante a instalação do ICP ou o administrador do sistema do Kubernetes precisará criar um storageClass antes de implementar.

É possível escolher implementar o peer nas plataformas amd64 ou s390x. No entanto, esteja ciente de que o [Fornecimento dinâmico](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) está disponível somente para nós amd64 no ICP. Se o cluster incluir uma combinação de nós do trabalhador s390x e amd64, o fornecimento dinâmico não poderá ser usado.

Se você não usar o fornecimento dinâmico, os [Volumes persistentes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) deverão ser criados e configurados com rótulos que podem ser usados para refinar o processo de ligação de PVC do Kubernetes.

## Pré-requisitos para implementar um peer
{: #prerequisites-peer-icp}

1. Antes de poder instalar um peer no ICP, deve-se [instalar o ICP](../ICP_setup.html) e [instalar o gráfico do Helm do {{site.data.keyword.blockchainfull_notm}} Platform](helm_install_icp.html).

2. Se você usar o Community Edition e desejar executar esse gráfico do Helm em um cluster do ICP sem conectividade à Internet, será necessário criar archives em uma máquina conectada à Internet antes de poder instalar os archives em seu cluster do ICP. Para obter mais informações, veja [Incluindo aplicativos de destaque em clusters sem conectividade à Internet ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Incluindo aplicativos de destaque em clusters sem conectividade à Internet"){:new_window}. Observe que é possível localizar o arquivo de especificação `manifest.yaml` em `ibm-blockchain-platform-dev/ibm_cloud_pak` no gráfico Helm.

3. Deve-se primeiro [implementar uma CA](CA_deploy_icp.html) no ICP. É necessário usar uma CA para criar um [arquivo de configuração de peer e armazená-lo como um segredo do Kubernetes no ICP](#peer-config-file).

4. Recupere o valor do endereço IP do Proxy do cluster de sua CA por meio do console do ICP. **Nota:** você precisará ser um [Administrador de cluster ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Funções e ações de administrador de cluster") para acessar seu IP de proxy. Efetue login no cluster do ICP. No painel de navegação à esquerda, clique em **Plataforma** e, em seguida, em **Nós** para visualizar os nós que estão definidos no cluster. Clique no nó com a função `proxy` e, em seguida, copie o valor do `IP do host` da tabela. **Importante:** salve esse valor e você o usará ao configurar o campo `Proxy IP` do gráfico do Helm.


## Criando o segredo de configuração de peer
{: #peer-config-file}

Para implementar um peer, é necessário criar um arquivo de configuração que contenha informações importantes sobre a identidade do peer e a autoridade de certificação. Em seguida, é necessário passar esse arquivo para o gráfico do Helm durante a configuração usando um objeto [Segredo do Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/configuration/secret/). Esse arquivo permitirá que seu peer obtenha os certificados de que precisa da autoridade de certificação para ingressar em uma rede de blockchain. Ele também contém um certificado de administrador que permitirá que você opere seu peer. Siga as instruções para [usar a CA para implementar um solicitador ou peer](CA_operate.html#deploy-orderer-peer) antes da configuração do peer.

É necessário fornecer os nomes de host do CSR para o arquivo de configuração. Isso inclui o `service host name` que terá o mesmo valor que o `helm release name` especificado durante a implementação. Por exemplo, se você especificar um `helm release name` igual a `org1peer1`, lembre-se de inserir o valor a seguir na seção `"csr"` do arquivo:
```
"csr": {
  "hosts": [
     "9.42.134.44",
    "org1peer1"
  ]
}
```
{:codeblock}

Depois de salvar o arquivo de configuração, é necessário codificá-lo para o formato base64 antes de fornecê-lo ao ICP como um objeto secreto. Um segredo do Kubernetes permite que você proteja e compartilhe informações sem ter que passá-las diretamente para a implementação.

1. Para codificar o arquivo de configuração no formato base64, execute os comandos a seguir por meio de uma janela do terminal:
```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi) cat <config_file> | base64 $FLAG
```
{:codeblock}

**Nota:** é importante que a sequência gerada pelo comando acima seja formatada como uma linha única. Isso deve ser semelhante a:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
```

Mas não assim:

```
LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
```

Salve a saída resultante para a Etapa 4 abaixo.

2. Efetue login no console do ICP. No painel de navegação à esquerda, clique em **Configuração** e, em seguida, em **Segredos**. Clique no botão **Criar segredo** para abrir um painel pop que permite gerar um novo objeto de segredo.

3. Na guia **Geral**, preencha os campos a seguir:
  - **Nome:** forneça ao seu segredo um nome exclusivo dentro de seu cluster. Você usará esse nome ao implementar seu peer. O nome deve ser todo em minúsculas.  
  **Nota:** ao implementar um peer, um novo segredo é gerado automaticamente pela implementação com o nome `<helm_release_name>-secret`. Portanto, ao nomear seu segredo, certifique-se de que o nome do segredo seja diferente do `<helm_release_name>-secret`. Caso contrário, a implementação do gráfico do helm falhará porque o segredo que ele tenta criar já existe.
  - **Namespace:** o namespace para incluir seu segredo. Selecione o `namespace` no qual você deseja implementar seu peer.
  - **Tipo:** insira o valor `Opaque`.

4. Clique na guia **Dados** na área de janela de navegação à esquerda dessa janela.
  - Nos campos `Name`, especifique `secret.json` e, no campo de valor, cole na sequência codificada `base64` de seu arquivo de configuração.

5. (Opcional) Se você planeja usar o `CouchDB` como seu banco de dados de estado, será necessário incluir dois valores adicionais nesse objeto secreto. Na guia **Dados**, clique no botão **Incluir dados** para incluir o ID do usuário e a senha do CouchDB a serem usados para o banco de dados quando o contêiner for implementado.
  1. Crie seu nome do usuário e senha e codifique os valores no formato base64. Execute os comandos a seguir em uma janela do terminal e substitua `admin` e `adminpw` pelos valores que você deseja usar.
    ```
    echo -n 'couch' | base64 $FLAG echo -n 'couchpw' | base64 $FLAG
    ```
    {:code_block}

  2. No campo **Nome**, insira o valor `couchdbuser`. No campo **Valor**, insira o resultado de `echo -n 'couch' | base64 $FLAG` da etapa um acima.
  3. Clique no botão **Incluir dados** para incluir um segundo par chave-valor.
  4. No segundo campo **Nome**, insira o valor `couchdbpwd`. No campo **Valor** correspondente, insira o resultado de `echo -n 'couchpw' | base64 $FLAG` da etapa um acima.

6. Clique em **Criar** para salvar seu objeto de segredo.

**Nota:** o segredo de configuração de peer não será removido do seu cluster do ICP quando você excluir sua liberação do Helm. Se você excluir seu peer, será necessário excluir esse segredo também.

## Configuração
{: #peer-configuration}

Depois de criar seu objeto de segredo de configuração de peer, é possível configurar e instalar seu peer no ICP com as etapas a seguir. É possível instalar somente um peer por vez.


1. Efetue login no console do ICP e clique no link **Catálogo** no canto superior direito.
2. Clique em `Blockchain` no painel de navegação esquerdo para localizar o tile rotulado `ibm-blockchain-platform-prod` ou `ibm-blockchain-platform-dev` se você tiver transferido por download a Community edition. Clique no tile para abri-lo e é possível ver um arquivo Leia-me que inclui informações sobre como instalar e configurar o gráfico do Helm.
3. Clique na guia **Configuração** na parte superior do painel ou clique no botão **Configurar** no canto inferior direito.
4. Especifique os valores para os [Parâmetros de configuração geral](#peer-global-parameters) e aceite o contrato de licença.
5. Abra a seta `Todos os parâmetros` e especifique o valor para os [Parâmetros de configuração global](#peer-global-parameters).
6. Role para baixo até a seção **Configuração de peer**. Marque a caixa de seleção `Instalar o peer` e conclua as [definições de configuração](#peer-parameters) para o peer.
7. Clique em **Instalar**.

### Parâmetros de configuração
{: #icp-peer-configuration-parms}

A tabela a seguir lista os parâmetros configuráveis do {{site.data.keyword.blockchainfull_notm}} Platform, **específicos do componente de peer**, e seus valores padrão. Se você estiver experimentando com o peer ou iniciando pela primeira vez, use os valores padrão dos parâmetros de banco de dados e de armazenamento. Role para a seção do componente peer para marcar a caixa de seleção `Install Peer` e forneça as informações de configuração associadas apenas para esse componente. **Embora a UI do gráfico do Helm indique que nenhuma configuração adicional é necessária, você precisa inserir determinados parâmetros para implementar um peer.**

#### Parâmetros de configuração global e geral
{: #peer-global-parameters}

|  Parâmetro     | Descrição    | Padrão  | Requerido |
| --------------|-----------------|-------|------- |
| `Helm release name`| O nome de sua liberação do Helm. Deve iniciar com uma letra minúscula e terminar com qualquer caractere alfanumérico, deve conter apenas hifens e caracteres alfanuméricos minúsculos. Deve-se usar um nome de liberação do Helm exclusivo toda vez que tentar instalar um componente. **Importante:** esse valor deve corresponder ao valor que você usou para gerar o 'service host name' para o campo "hosts" em seu [arquivo de segredo JSON.](#peer-config-file) | nenhum | sim |
| ` Namespace de destino `| Escolha o namespace do Kubernetes para instalar o gráfico do Helm. | nenhum | sim |
|**Configuração global**| Parâmetros que se aplicam a todos os componentes no gráfico do Helm|||
| `Service account name`| Insira o nome da [conta do serviço ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) que você usará para executar o pod. | padrão | não |

#### Parâmetros de configuração de peer
{: #peer-parameters}

|  Parâmetro     | Descrição    | Padrão  | Requerido |
| --------------|-----------------|-------|------- |
|**Configuração do cluster** |**Informações de configuração do cluster** | ||
| `Install Peer` | Selecione para instalar um peer|desmarcado | sim, se você deseja implementar um peer |
| `Peer worker node architecture`| Selecione sua arquitetura da plataforma de nuvem (AMD64 ou S390x)| AMD64 | sim |
| `Peer image repository`| Local do gráfico do Helm de peer. Esse campo é preenchido automaticamente para o caminho instalado. | ibmcom/ibp-fabric-peer | sim |
| `Peer Docker image tag`|Valor da tag que está associada à imagem de peer. |1.2.1, preenchido automaticamente com o valor correto.|sim|
| `Peer configuration`| É possível customizar a configuração do peer colando seu próprio arquivo de configuração `core.yaml` nesse campo. Para ver um arquivo `core.yaml` de amostra, veja [Configuração de amostra `core.yaml` ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/core.yaml) **Somente para usuários avançados**. | nenhum | não |
| `Peer configuration secret (Required)`|Nome do [Segredo de configuração do peer](#peer-config-secret) criado no ICP. | nenhum | sim |
|`Organization MSP (Required)`| É possível criar um novo valor de MSPID da Organização, como 'org1', ou especificar um MSP da Organização existente do qual o peer fará parte. Se você tiver implementado uma organização do solicitador, assegure-se de que os MSPIDs de peer sejam diferentes de seu MSPID solicitado. Além disso, anote esse valor, pois você precisará dele para seu `CORE_PEER_LOCALMSPID` e `configtx.yaml` posteriormente. | nenhum | sim |
|`Peer service type`| Usado para especificar se [portas externas devem ser expostas ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) no peer. Selecione NodePort para expor as portas externamente (recomendado) e ClusterIP para não expor as portas. O LoadBalancer e o ExternalName não são suportados nesta liberação. | NodePort | sim |
| `State database`| O [banco de dados de estado](../glossary.html#state-database) usado para armazenar o livro-razão do canal. O peer precisa usar o mesmo banco de dados que sua [rede de blockchain](../v10_dashboard.html#network-preferences). | nenhum | sim |
|`CouchDB image repository`| Aplica-se somente se o CouchDB foi selecionado como o banco de dados de livro-razão. Esse campo é preenchido automaticamente para o caminho instalado. Se você estiver usando o Community Edition e não tiver acesso à Internet, ele deverá corresponder ao diretório no qual a imagem do Fabric CouchDB foi transferida por download.| ibmcom/ibp-fabric-couchdb | sim |
| `CouchDB Docker image tag`| Aplica-se somente se o CouchDB foi selecionado como o banco de dados de livro-razão. Valor da tag associada à imagem do CouchDB. | Preenchido automaticamente com o valor correto. | sim |
| `Peer Data persistence enabled`| Ative a capacidade de persistir dados após reinicializações ou falhas do cluster. Consulte [armazenamento no Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/ "Volumes") para obter mais informações. *Se desmarcado, todos os dados serão perdidos no caso de um failover ou de uma reinicialização de pod.* | verificado | não |
| `Peer use dynamic provisioning`| Marque para ativar o fornecimento dinâmico para volumes de armazenamento. | verificado | não |
| `Peer persistent volume claim`| Somente para nova solicitação. Insira um nome para sua nova Solicitação de Volume Persistente (PVC) a ser criada. | my-data-pvc | não |
| `Peer storage class name`| Especifique um nome de classe de armazenamento para o peer. | Em branco se estiver criando uma nova PVC; caso contrário, especifique a classe de armazenamento associada à PVC existente. | não |
| `Peer existing volume claim`| Especifique o nome de uma Solicitação de volume existente e deixe todos os outros campos em branco. | nenhum | não |
| `Peer selector label`| [Rótulo do seletor ![Ícone de link externo](../images/external_link.svg "Ícone de link externo") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Rótulos e seletores") para seu PVC.| nenhum | não |
| `Peer selector value`| [Valor do seletor ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Rótulos e seletores") para seu PVC. | nenhum | não |
| `Peer storage access mode`| Especifique o [modo de acesso ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modos de acessos") de armazenamento para o PVC.  | ReadWriteMany| não |
| `Peer volume claim size`| O tamanho da Solicitação de volume deve ser maior que 2 Gi. | 8Gi  | sim |
| `State database persistent volume claim`| Somente para nova solicitação. Insira um nome para sua nova Solicitação de Volume Persistente (PVC) a ser criada. | nome_do_pvc | não |
| ` Nome da classe de armazenamento do banco de dados de| Especifique um nome de classe de armazenamento para o banco de dados de estado | nenhum | não |
| ` solicitação de volume existente do banco de dados de estado| Especifique o nome de uma Solicitação de volume existente e deixe todos os outros campos em branco. | nenhum | não |
| `State database selector label`| [Rótulo do seletor](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) para seu PVC| nenhum | não |
| `State database selector value`| [Valor do seletor](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) para seu PVC | nenhum | não |
| ` Modo de acesso de armazenamento do banco de dados de| Especifique o [modo de acesso ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modos de acessos") de armazenamento para o PVC. | ReadWriteMany| não |
| ` Tamanho da reivindicação do volume do banco de dados| Escolha o tamanho do disco a ser usado. | 8Gi | sim |
| `CouchDB - Data persistence enabled`| Para o contêiner do CouchDB, os dados do livro-razão estarão disponíveis quando o contêiner for reiniciado. *Se desmarcado, todos os dados serão perdidos no caso de um failover ou de uma reinicialização de pod.*| verificado | não |
| `CouchDB - Use dynamic provisioning`| Para o contêiner do CouchDB, use o armazenamento dinâmico do Kubernetes.| verificado | não |
| `Peer CPU request` | Número mínimo de CPUs a serem alocadas para o peer. | 1 | sim |
| `Peer CPU limit` | Número máximo de CPUs a serem alocadas para o peer.| 2 | sim |
| `Peer Memory request` | Quantia mínima de memória a ser alocada para o peer. | 1 Gi | sim |
| `Peer Memory limit` | Quantia máxima de memória a ser alocada para o peer. | 4Gi | sim |
| `CouchDB CPU request` | Número mínimo de CPUs a serem alocadas para o CouchDB.| 1 | sim |
| `CouchDB CPU limit` | Número máximo de CPUs a serem alocadas para o CouchDB. | 2 | sim |
| `CouchDB Memory request` | Quantia mínima de memória a ser alocada para o CouchDB.| 1 Gi | sim |
| `CouchDB Memory limit` | Quantia máxima de memória a ser alocada para o CouchDB. | 4Gi | sim |


Quando o CouchDB é selecionado como o banco de dados de peer, dois contêineres são criados no pod, um para o peer e um para o CouchDB.
O contêiner do Peer inclui uma única montagem de volume para a PVC do Peer que armazena os blocos e as transações no sistema de arquivos. O contêiner do CouchDB monta a PVC do banco de dados de estado do peer que contém os dados do livro-razão.

<!-- LevelDB is not supported on the Peer
When LevelDB is selected as the peer database, a single container is created in the pod for running both the peer and LevelDB
processes. This container has two volume mounts, one for the Peer PVC and the second volume mount is for the peer state database PVC that contains the ledger data.
-->
<!--
| Peer database selection  | Contents of Container #1  | Contents of Container #2 |
| --------------|-----------------|---------------|
| CouchDB | Peer that mounts the Peer PVC| CouchDB that mounts the state database PVC |
| LevelDB | Peer and LevelDB that mount the Peer PVC and the state database PVC | n/a |
-->

### Usando a linha de comandos do Helm para instalar a liberação do Helm
{: #icp-helm-cli}

Como alternativa, é possível usar a CLI `helm` para instalar a liberação do Helm. Antes de executar o comando `helm install`, assegure-se de [incluir o repositório do Helm de seu cluster no ambiente da CLI do Helm ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Incluindo o repositório do Helm interno na CLI do Helm").

É possível configurar os parâmetros necessários para a instalação, criando um arquivo `yaml` e passando-o para o comando `helm install` a seguir.
```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```
{:codeblock}

Em que:
- `<helm_release name>` representa o nome que você deseja fornecer à liberação do helm.
- `<helm_chart>` representa o nome do gráfico do Helm que é importado para o catálogo.
- `<helm_chart_version>` representa a versão do gráfico do Helm que é importado para o catálogo.
- `<customvalues.yaml>` é o nome do arquivo yaml que contém os parâmetros de configuração.

Por exemplo:
```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \ --version 1.1.0 \ --values peer-s390x-values.yaml \ --tls
```

É possível criar um novo arquivo `yaml` editando o `values.yaml` incluído no archive transferido por download, que inclui todos os parâmetros necessários com suas configurações padrão.

## Verificar a instalação de peer
{: #verify-installation-icp}

Depois de concluir os parâmetros de configuração e clicar no botão **Instalar**, clique no botão **Visualizar liberação do Helm** para visualizar sua implementação. Se isso foi bem-sucedido, você verá o valor 1 nos campos `DESIRED`, `CURRENT`, `UP TO DATE` e `AVAILABLE` na tabela Implementação. Você pode precisar clicar em atualizar e aguardar que a tabela seja atualizada. Também é possível localizar a tabela Implementação, clicando no ícone **Menu** no canto superior esquerdo do console do ICP. Na lista de menu, clique em **Cargas de trabalho** e, em seguida, **Liberações do Helm**.

Se você rolar para baixo para a seção `Notas`, haverá informações importantes que serão usadas ao [operar seu peer](peer_operate_icp.html).

## Visualizando os logs de peer
{: #peer-deploy-view-logs}

Os logs de peer podem ser visualizados usando os [comandos da CLI kubectl](peer_operate_icp.html#peer-kubectl-configure) ou por meio de [Kibana ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.elastic.co/products/kibana "Sua janela para o Elastic Search"). Para obter mais informações, consulte estas [instruções para acessar os logs](peer_operate_icp.html#peer-icp-view-logs).

## O que vem depois

Depois de implementar o peer, é necessário concluir várias etapas operacionais antes de poder enviar transações e ler o livro-razão distribuído a partir da rede de blockchain. Para obter mais informações, consulte [Peers operacionais com uma rede multi-cloud](peer_operate_icp.html).
