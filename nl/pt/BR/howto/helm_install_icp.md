---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-23"

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

# Instalando o  {{site.data.keyword.blockchainfull_notm}}  Platform for  {{site.data.keyword.cloud_notm}}  Private
{: #helm-install}

O {{site.data.keyword.blockchainfull}} Platform for {{site.data.keyword.cloud_notm}} Private é fornecido como um arquivo de gráfico do Helm que pode ser instalado em um cluster local do {{site.data.keyword.cloud_notm}} Private. Depois de instalar o gráfico do Helm, é possível localizar o {{site.data.keyword.blockchainfull_notm}} Platform como um aplicativo no catálogo do {{site.data.keyword.cloud_notm}} Private.

Antes de instalar o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private, revise as [Considerações e limitações](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations). Para obter mais informações sobre como implementar os componentes de blockchain que estão incluídos no gráfico do Helm, a precificação e o suporte, veja [Sobre o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about).

O {{site.data.keyword.blockchainfull_notm}}  Platform for  {{site.data.keyword.cloud_notm}}  Private fornece duas edições:

- O {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private é entregue por meio do Passport Advantage (PPA). É necessário ter a licença necessária para acessar o [Passport Advantage Online ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online"). Mediante a compra, o suporte técnico para o {{site.data.keyword.blockchainfull_notm}} Platform é incluído.

- O {{site.data.keyword.blockchainfull_notm}} Platform Community Edition é uma oferta gratuita para exploração, desenvolvimento e teste. Essa versão gratuita pode ser acessada por meio do [GitHub ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/charts"). Observe que o {{site.data.keyword.IBM_notm}} não fornece suporte para o Community Edition.

## Pré-requisitos para instalar o gráfico Helm
{: #helm-install-prereqs}

Antes de instalar o gráfico do Helm, deve-se ter configurado um cluster do {{site.data.keyword.cloud_notm}} Private e criado um novo namespace de destino que esteja ligado a uma política de segurança de pod. Revise as instruções para [definir e configurar um cluster do {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup).

## Instalando o {{site.data.keyword.blockchainfull_notm}} Platform atrás de um firewall
{: #helm-install-prereqs-firewall}

É possível implementar os componentes do {{site.data.keyword.blockchainfull_notm}} Platform atrás de um firewall sem conectividade com a Internet. O pacote PPA inclui todas as imagens do Docker do componente do Fabric que o {{site.data.keyword.blockchainfull_notm}} Platform usará e não é necessário fazer download do DockerHub durante a implementação.

No entanto, o gráfico Helm da Community Edition não inclui as imagens do Docker do componente Fabric necessárias porque essa edição está configurada para fazer download dessas imagens a partir do DockerHub durante a implementação. A implementação falhará se não houver conectividade com a Internet disponível. Portanto, é necessário concluir etapas extras para criar archives em uma máquina conectada à Internet antes de poder instalar os archives em seu cluster do {{site.data.keyword.cloud_notm}} Private. As imagens a seguir são necessárias:
- [Peer do Farbic ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hub.docker.com/r/ibmcom/ibp-fabric-peer/ "Peer do Farbic")
- [Fabric CA ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hub.docker.com/r/ibmcom/ibp-fabric-ca/ "Fabric CA")
- [ Solicitador do Fabric ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hub.docker.com/r/ibmcom/ibp-fabric-orderer/ "Solicitador do Fabric")
- [Banco de dados Couch do Fabric ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hub.docker.com/r/ibmcom/ibp-fabric-couchdb/ "Banco de dados couch do Fabric")
- A imagem [`Init` usada para autoinicialização e configuração de componentes, incluindo as pastas MSP ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hub.docker.com/r/ibmcom/ibp-init/)
- [Imagem do Docker-in-Docker (DinD) usada pelos peers para executar contêineres de chaincode ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hub.docker.com/r/ibmcom/ibp-dind/)

Para obter mais informações sobre como usar essas imagens, consulte [Incluindo aplicativos qualificados em clusters sem conectividade com a Internet ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html). Observe que é possível localizar o arquivo de especificação `manifest.yaml` no diretório `ibm-blockchain-platform-dev/ibm_cloud_pak` no gráfico Helm.

## Importando o gráfico Helm para  {{site.data.keyword.cloud_notm}}  Privado
{: #helm-install-importing}

1. Faça download do arquivo de gráfico do Helm do IBM Blockchain Platform for {{site.data.keyword.cloud_notm}} Private por meio do [Passport Advantage Online ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online") ou da edição Community gratuita por meio do [GitHub ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.2.tgz "IBM/charts").  Esse pacote de gráficos Helm contém três subgráficos Helm para a CA, solicitador e peer.

2. Caso ainda não tenha feito isso, efetue login em seu cluster do {{site.data.keyword.cloud_notm}} Private.

  ```
  cloudctl login -a https://<cluster_CA_domain>:8443 --skip-ssl-validation
  ```
  {:codeblock}

3. Assegure-se de que a [CLI do Docker](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/manage_images/configuring_docker_cli.html) esteja configurada. Depois de configurar a CLI do Docker, acesse o registro de imagem em seu cluster usando o comando a seguir:
  ```
  docker login <cluster_CA_domain>:8500
  ```
  {:codeblock}

4. Localize o nome do repositório no {{site.data.keyword.cloud_notm}} Private para fazer upload de seu gráfico do Helm usando o comando a seguir:
  ```
  cloudctl catalog repos
  ```
  {:codeblock}

  Quando esse comando for concluído com êxito, será possível ver uma lista de repositórios em seu cluster. Escolha o nome do repositório de destino e salve-o. É necessário usá-lo no comando a seguir.

5. Importe o gráfico Helm usando a linha de comandos.
  O comando que você executa para importar o gráfico Helm depende de o gráfico Helm ter sido transferido por download a partir do Passport Advantage (PPA) ou do GitHub.

  - **{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private**

    Siga essas instruções se você transferiu por download o gráfico Helm a partir do PPA.
    No diretório em que você armazenou o pacote de gráfico do Helm transferido por download do PPA, execute o comando a seguir na CLI do {{site.data.keyword.cloud_notm}} Private para importar o gráfico do Helm para seu cluster do {{site.data.keyword.cloud_notm}} Private.

    ```
    cloudctl catalog load-archive --archive <archive-name> --registry <cluster_CA_domain>:8500 --repo <repo-name>
    ```
    {:codeblock}

    Substitua os seguintes valores:
    - `<archive-name>` pelo nome do arquivo `.tgz` transferido por download.
    - `<cluster_CA_domain>:8500` com o domínio que você usa para efetuar login no seu cluster do {{site.data.keyword.cloud_notm}} Private.
    - `<repo-name>` com o repositório do helm no qual você deseja fazer upload do gráfico. Execute 'cloudctl catalog repos' para listar seus repositórios.

    Quando esse comando for concluído com êxito, você verá algo semelhante às informações a seguir:

    ```  
    Expanding archive
    OK

    Importing docker images
      Processing image: ibmcom/ibp-fabric-orderer-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-orderer-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-orderer-s390x:1.4.0
      Processing image: ibmcom/ibp-fabric-ca-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-ca-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-ca-s390x:1.4.0
      Processing image: ibmcom/ibp-fabric-peer-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-amd64:1.4.0
      Processing image: ibmcom/ibp-fabric-peer-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-fabric-peer-s390x:1.4.0
      Processing image: ibmcom/ibp-couchdb-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-amd64:1.4.0
      Processing image: ibmcom/ibp-couchdb-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-couchdb-s390x:1.4.0
      Processing image: ibmcom/ibp-dind-amd64:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-amd64:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-amd64:1.4.0
      Processing image: ibmcom/ibp-dind-s390x:1.4.0
      Loading Image
      Tagging Image
      Pushing image as: mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-s390x:1.4.0
    Pushing image mycluster.icp:8500/bcaas-usa/ibmcom/ibp-dind-s390x:1.4.0
    OK

    Uploading helm charts
      Processing chart: charts/ibm-blockchain-platform-1.0.2.tgz
      Updating chart values.yaml
      Uploading chart
    Loaded helm chart
    OK

    Synch charts
    Synch started
    OK
    ```  
    </details>

  - **Community edition transferida por download a partir do GitHub**
    Siga essas instruções se você transferiu por download o gráfico Helm a partir do GitHub.

    No diretório em que você armazenou o pacote de gráfico do Helm transferido por download do GitHub, execute o comando a seguir na CLI do {{site.data.keyword.cloud_notm}} Private para importar o gráfico do Helm para seu cluster do {{site.data.keyword.cloud_notm}} Private. Para importar o gráfico Helm transferido por download do GitHub, execute o comando a seguir:
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

Clique no botão **Catálogo** no console do {{site.data.keyword.cloud_notm}} Private e, em seguida, clique em **Blockchain** no painel de navegação à esquerda para verificar se a importação foi bem-sucedida. Se sim, o tile **ibm-blockchain-platform-prod** ou **ibm-blockchain-platform-dev** deve estar visível na página Catálogo do {{site.data.keyword.cloud_notm}} Private.

## Implementando componentes individuais
{: #helm-install-deploying-components}

Após instalar o gráfico do Helm, clique no tile **ibm-blockchain-platform-prod** ou **ibm-blockchain-platform-dev** em seu catálogo do {{site.data.keyword.cloud_notm}} Private para abri-lo. É possível usar a página de configuração para implementar qualquer um dos componentes individuais de sua rede de blockchain. Para obter mais detalhes sobre os componentes que são necessários para a sua solução de blockchain e a ordem na qual eles devem ser implementados, veja [Introdução ao {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#get-started-icp).

Em seguida, implemente os componentes individuais:

- Se você estiver implementando um solicitador, primeiro será necessário configurar uma Autoridade de Certificação para o solicitador. A CA gerará certificados que serão usados por outros componentes em sua organização. Para obter mais informações, consulte [Implementando uma Autoridade de certificação do {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy). Em seguida, é possível implementar o solicitador que será a ligação comum da rede. Para obter mais informações, veja [Implementando um solicitador do {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy)

- Se você estiver implementando um peer, primeiro será necessário configurar uma Autoridade de Certificação para o peer. A CA gerará certificados que serão usados pelo peer. Para obter mais informações, consulte [Implementando uma Autoridade de certificação do {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy). Em seguida, quando você estiver pronto para associar-se a uma rede, será possível implementar os peers que unirão canais, endossarão transações e armazenarão seus dados. Para obter mais informações, veja [Implementando um peer do {{site.data.keyword.blockchainfull_notm}} no {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy) ou [Implementando um peer do {{site.data.keyword.blockchainfull_notm}} para uma rede do Starter ou do Enterprise Plan](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy), dependendo da rede de blockchain à qual o peer se associará.

## Fazendo upgrade do gráfico do Helm no {{site.data.keyword.cloud_notm}} Private
{: #helm-install-upgrading}

Se você usa o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.1, é possível fazer upgrade do seu gráfico do Helm para o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.2 seguindo as instruções em [Fazendo upgrade de produtos em pacote configurável ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/upgrade_helm.html "Fazendo upgrade de produtos em pacote configurável") na documentação do {{site.data.keyword.cloud_notm}} Private. Além do comando `helm upgrade` nas instruções, também é possível [usar o console do {{site.data.keyword.cloud_notm}} Private para fazer upgrade da liberação do Helm](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-upgrading-ui).

É necessário ter a função **administrador de cluster** ou **administrador da equipe** no {{site.data.keyword.cloud_notm}} Private para fazer upgrade de um gráfico do Helm.
{:note}

A recuperação do {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private v1.0.2 para a v1.0.1 não é suportada.

### Upgrade da liberação do Helm por meio do console do {{site.data.keyword.cloud_notm}} Private
{: #helm-install-upgrading-ui}

Ao fazer upgrade da liberação do Helm de um componente por meio do console do {{site.data.keyword.cloud_notm}} Private, deve-se marcar `Reuse values` e não mudar nenhum parâmetro.
{:important}  

Conclua as etapas a seguir para fazer upgrade da liberação do Helm por meio do console do {{site.data.keyword.cloud_notm}} Private:
1. Na barra de menus, clique em **Cargas de trabalho** > **Liberações do Helm**.
2. Selecione a liberação do Helm para a qual deseja fazer o upgrade.
3. Verifique o número da versão em `Available Version`, na seção **Detalhes e upgrades**.
4. Clique em **ReadMe** no número da versão para revisar as notas sobre a liberação em busca de qualquer mudança importante.
5. Clique em **Fazer upgrade** e, em seguida, selecione o repositório e a versão corretos na lista suspensa.
6. Certifique-se de que `Reuse values` esteja selecionado.
7. Clique em **Fazer upgrade**.

Aguarde a mensagem de confirmação e a atualização no status da liberação ao lado de seu nome, no canto superior esquerdo do console.

### Concluindo etapas adicionais para fazer upgrade de um peer
{: #helm-install-upgrading-peer}

Depois de fazer upgrade de um peer, há algumas etapas adicionais que precisam ser executadas para concluir o processo de upgrade. Na página de liberação do Helm do peer, é possível ver dois pods de peer na seção **Pod**:
- O novo pod do peer com o status `CrashLoopBackOff`
- O pod original do peer com o status `Running`

Anote os nomes do peer associados, pois será necessário usá-los nas etapas abaixo.
{:tip}

Conclua as etapas a seguir por meio da CLI para concluir o processo de upgrade do peer:
1. Efetue login em seu cluster por meio da CLI do {{site.data.keyword.cloud_notm}} Private e execute o comando `kubectl get replicaset`.
   ```
   cloudctl login -a https://<Cluster Master Host>:<Cluster Master API Port> --skip-ssl-validation
   kubectl get replicaset
   ```
   {:codeblock}
2. Localize o peer replicaset que corresponde ao peer original. O nome do peer replicaset deve corresponder à parte inicial do nome do peer na seção **Pod** do console do {{site.data.keyword.cloud_notm}} Private.
3. Exclua o peer replicaset.
   ```
   kubectl delete rs <peer replicaset name>
   ```
   {:codeblock}
4. Exclua o novo pod do peer. Ele possui o status `CrashLoopBackOff` na IU.
   ```
   kubectl delete po <new peer pod name>
   ```
   {:codeblock}

O upgrade de seu peer terá sido feito com sucesso quando você vir que um novo pod de peer foi criado com o status `Running`.
