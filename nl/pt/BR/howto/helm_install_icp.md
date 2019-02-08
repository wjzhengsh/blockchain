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

# Instalando o {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.cloud_notm}} Private
{: #remote-peer-icp}

O {{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private é entregue como um arquivo de gráfico Helm que pode ser instalado em um cluster ICP local. Depois de instalar o gráfico do Helm, é possível localizar o {{site.data.keyword.blockchainfull_notm}} Platform como um aplicativo no catálogo do ICP.

Antes de instalar o {{site.data.keyword.blockchainfull_notm}} Platform for ICP, revise as [Considerações e limitações](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-considerations). Para obter mais informações sobre como implementar os componentes de blockchain que estão incluídos no gráfico Helm, na precificação e no suporte, consulte [Sobre o {{site.data.keyword.blockchainfull_notm}} Platform for ICP](/docs/services/blockchain/ibp-for-icp-about.html).

O {{site.data.keyword.blockchainfull_notm}} Platform for ICP entrega duas edições:

- O {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private é entregue por meio do Passport Advantage (PPA). É necessário ter a licença necessária para acessar o [Passport Advantage Online ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online"). Mediante a compra, o suporte técnico para o {{site.data.keyword.blockchainfull_notm}} Platform é incluído.

- O {{site.data.keyword.blockchainfull_notm}} Platform Community Edition é uma oferta gratuita para exploração, desenvolvimento e teste. Essa versão gratuita pode ser acessada por meio do [GitHub ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts"). Observe que o {{site.data.keyword.IBM_notm}} não fornece suporte para o Community Edition.

## Pré-requisitos para instalar o gráfico Helm
{: #helm-install-prereqs}

Antes de instalar o gráfico Helm, deve-se ter configurado um cluster ICP. Revise as instruções para [instalar e configurar um cluster ICP](/docs/services/blockchain/ICP_setup.html).

## Instalando o {{site.data.keyword.blockchainfull_notm}} Platform atrás de um firewall
{: #helm-install-prereqs-firewall}

É possível implementar os componentes do {{site.data.keyword.blockchainfull_notm}} Platform atrás de um firewall sem conectividade com a Internet. O pacote PPA inclui todas as imagens do Docker do componente do Fabric que o {{site.data.keyword.blockchainfull_notm}} Platform usará e não é necessário fazer download do DockerHub durante a implementação.

No entanto, o gráfico Helm da Community Edition não inclui as imagens do Docker do componente Fabric necessárias porque essa edição está configurada para fazer download dessas imagens a partir do DockerHub durante a implementação. A implementação falhará se não houver conectividade com a Internet disponível. Portanto, é necessário concluir etapas extras para criar archives em uma máquina conectada à Internet antes de poder instalar os archives em seu cluster ICP. As imagens a seguir são necessárias:
- [Peer do Farbic ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Peer do Farbic")
- [Fabric CA ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "Fabric CA")
- [ Solicitador do Fabric ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Solicitador do Fabric")
- [Banco de dados Couch do Fabric ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Banco de dados couch do Fabric")
- A imagem [`Init` usada para autoinicialização e configuração de componentes, incluindo as pastas MSP ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [Imagem do Docker-in-Docker (DinD) usada pelos peers para executar contêineres de chaincode ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hub.docker.com/r/ibmcom/ibp-dind/)

Para obter mais informações sobre como usar essas imagens, consulte [Incluindo aplicativos qualificados em clusters sem conectividade com a Internet ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html). Observe que é possível localizar o arquivo de especificação `manifest.yaml` no diretório `ibm-blockchain-platform-dev/ibm_cloud_pak` no gráfico Helm.

## Importando o gráfico Helm para o ICP

1. Faça download do arquivo do gráfico Helm do IBM Blockchain Platform for ICP a partir do [Passport Advantage Online ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online") ou para a Community Edition gratuita a partir do [GitHub ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.0.tgz "IBM/charts"). Esse pacote de gráficos Helm contém três subgráficos Helm para a CA, solicitador e peer.

2. Se você ainda não tiver feito, efetue login em seu cluster ICP.

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

4. Assegure-se de que a [CLI do Docker](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/manage_images/configuring_docker_cli.html) esteja configurada. Depois de configurar a CLI do Docker, acesse o registro de imagem em seu cluster usando o comando a seguir:
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

5. Localize o nome do repositório no ICP para fazer upload de seu gráfico Helm usando o comando a seguir:
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  Quando esse comando for concluído com êxito, será possível ver uma lista de repositórios em seu cluster. Escolha o nome do repositório de destino e salve-o. É necessário usá-lo no comando a seguir.

6. Importe o gráfico Helm usando a linha de comandos.
  O comando que você executa para importar o gráfico Helm depende de o gráfico Helm ter sido transferido por download a partir do Passport Advantage (PPA) ou do GitHub.

  - **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

    Siga essas instruções se você transferiu por download o gráfico Helm a partir do PPA.
    No diretório no qual você armazenou o pacote de gráfico Helm transferido por download do PPA, execute o comando a seguir na CLI do ICP para importar o gráfico Helm para seu cluster de ICP.

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    Substitua os seguintes valores:
    - `<archive-name>` pelo nome do arquivo `.tgz` transferido por download.
    - `<cluster_CA_domain>:8500` pelo domínio que você usa para efetuar login em seu cluster de ICP.
    - `<repo-name>` com o repositório do helm no qual você deseja fazer upload do gráfico. Execute 'cloudctl catalog repos' para listar seus repositórios.

    Quando esse comando for concluído com êxito, você verá algo semelhante às informações a seguir:
    ```
    Expanding archive
    OK

    Importing docker images
      Processing image: ibmblockchain/hlfabric-orderer-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-amd64:1.2.1
      Processing image: ibmblockchain/hlfabric-orderer-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-orderer-s390x:1.2.1
      Processing image: ibmblockchain/hlfabric-ca-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-amd64:1.2.1
      Processing image: ibmblockchain/hlfabric-ca-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/hlfabric-ca-s390x:1.2.1
      Processing image: ibmblockchain/v1fabric-peer-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-amd64:1.2.1
      Processing image: ibmblockchain/v1fabric-peer-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/v1fabric-peer-s390x:1.2.1
      Processing image: ibmblockchain/fabric-couchdb-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-amd64:1.2.1
      Processing image: ibmblockchain/fabric-couchdb-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmblockchain/fabric-couchdb-s390x:1.2.1
      Processing image: ibmcom/icp-dind-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-amd64:1.2.1
      Processing image: ibmcom/icp-dind-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-dind-s390x:1.2.1
      Processing image: ibmcom/icp-busybox-s390x:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-s390x:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-s390x:1.2.1
      Processing image: ibmcom/icp-busybox-amd64:1.2.1
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-amd64:1.2.1
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/icp-busybox-amd64:1.2.1
    OK

    Uploading helm charts
      Processing chart: charts/ibm-blockchain-platform-0.1.5.tgz
      Updating chart values.yaml
      Uploading chart
    Loaded helm chart
    OK

    Synch charts
    Synch started
    OK
    ```

  - **Community edition transferida por download a partir do GitHub**
    Siga essas instruções se você transferiu por download o gráfico Helm a partir do GitHub.

    No diretório em que você armazenou o pacote de gráficos Helm transferido por download do GitHub, execute o comando a seguir na CLI do ICP para importar o gráfico Helm para seu cluster de ICP. Para importar o gráfico Helm transferido por download do GitHub, execute o comando a seguir:
    ```
    cloudctl catalog load-chart --archive <helm_chart_from_github> --repo <repo-name>
    ```
    {:codeblock}

    Substitua os seguintes valores:
    - `<helm_chart_from_github>` pelo nome do arquivo .tgz transferido por download.
    - `<repo-name>` com o repositório do helm no qual você deseja fazer upload do gráfico. Execute 'cloudctl catalog repos' para listar seus repositórios.

    Quando esse comando for concluído com êxito, você verá algo semelhante às informações a seguir:
    ```
    Loading helm chart
  Loaded helm chart

    Synch charts
    Synch started
    OK
    ```

Clique no botão **Catálogo** no console do ICP e, em seguida, clique em **Blockchain** no painel de navegação esquerdo para verificar se a importação foi bem-sucedida. Se sim, o tile **ibm-blockchain-platform-prod** ou **ibm-blockchain-platform-dev** deverá estar visível na página do Catálogo do ICP.


## Requisitos de PodSecurityPolicy

Depois de importar o gráfico Helm para o {{site.data.keyword.cloud_notm}} Private, é necessário ligar um [PodSecurityPolicy ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/policy/pod-security-policy/ "Políticas de segurança de pod") ao espaço de nomes de destino antes de instalar os componentes. Escolha um PodSecurityPolicy predefinido ou peça que o administrador de cluster crie um PodSecurityPolicy customizado para você:
- Nome do PodSecurityPolicy predefinido: [`ibm-privileged-psp`](https://ibm.biz/cpkspec-psp)
- Definição de PodSecurityPolicy customizado:
  ```
  apiVersion: extensions/v1beta1
  kind: PodSecurityPolicy
  metadata:
    name: ibm-blockchain-platform-psp
  spec:
    hostIPC: false
    hostNetwork: false
    hostPID: false
    privileged: true
    allowPrivilegeEscalation: true
    readOnlyRootFilesystem: false
    seLinux:
      rule: RunAsAny
    supplementalGroups:
      rule: RunAsAny
    runAsUser:
      rule: RunAsAny
    fsGroup:
      rule: RunAsAny
    requiredDropCapabilities:
    - ALL
    allowedCapabilities:
    - NET_BIND_SERVICE
    - CHOWN
    - DAC_OVERRIDE
    - SETGID
    - SETUID
    volumes:
    - '*'
  ```
  {:codeblock}
- ClusterRole customizado para o PodSecurityPolicy customizado:
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRole
  metadata:
    annotations:
    name: ibm-blockchain-platform-clusterrole
  rules:
  - apiGroups:
    - extensions
    resourceNames:
    - ibm-blockchain-platform-psp
    resources:
    - podsecuritypolicies
    verbs:
    - use
  - apiGroups:
    - ""
    resources:
    - secrets
    verbs:
    - create
    - delete
    - get
    - list
    - patch
    - update
    - watch
  ```
  {:codeblock}

- ClusterRoleBinding customizado para o ClusterRole customizado:
  ```
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRoleBinding
  metadata:
   name: ibm-blockchain-platform-clusterrolebinding
  roleRef:
   apiGroup: rbac.authorization.k8s.io
   kind: ClusterRole
   name: ibm-blockchain-platform-clusterrole
  subjects:
  - kind: ServiceAccount
    name: default
    namespace: default
  ```
  {:codeblock}

## Implementando componentes individuais

Depois de instalar o gráfico Helm, clique no tile **ibm-blockchain-platform-prod** ou **ibm-blockchain-platform-dev** em seu catálogo ICP para abri-lo. É possível usar a página de configuração para implementar qualquer um dos componentes individuais de sua rede de blockchain. Para obter mais detalhes sobre os componentes que são necessários para a sua solução de blockchain e a ordem na qual eles devem ser implementados, consulte [Guia de implementação do {{site.data.keyword.blockchainfull_notm}} Platform for ICP](/docs/services/blockchain/ibp_for_icp_deployment_guide.html).

Em seguida, implemente os componentes individuais:

- Se você estiver implementando um solicitador, primeiro será necessário configurar uma Autoridade de Certificação para o solicitador. A CA gerará certificados que serão usados por outros componentes em sua organização. Para obter mais informações, consulte [Implementando uma autoridade de certificação do {{site.data.keyword.blockchainfull_notm}} Platform no ICP](/docs/services/blockchain/howto/CA_deploy_icp.html). Em seguida, é possível implementar o solicitador que será a ligação comum da rede. Para obter mais informações, consulte [Implementando um solicitador do {{site.data.keyword.blockchainfull_notm}} Platform no ICP](/docs/services/blockchain/howto/orderer_deploy_icp.html)

- Se você estiver implementando um peer, primeiro será necessário configurar uma Autoridade de Certificação para o peer. A CA gerará certificados que serão usados pelo peer. Para obter mais informações, consulte [Implementando uma autoridade de certificação do {{site.data.keyword.blockchainfull_notm}} Platform no ICP](/docs/services/blockchain/howto/CA_deploy_icp.html). Em seguida, quando você estiver pronto para associar-se a uma rede, será possível implementar os peers que unirão canais, endossarão transações e armazenarão seus dados. Para obter mais informações, consulte [Implementando um peer do {{site.data.keyword.blockchainfull_notm}} no ICP](/docs/services/blockchain/howto/peer_deploy_icp.html) ou [Implementando um peer do {{site.data.keyword.blockchainfull_notm}} para uma rede Starter ou Enterprise Plan](/docs/services/blockchain/howto/peer_deploy_ibp.html), dependendo de a qual rede de blockchain o peer se associará.
