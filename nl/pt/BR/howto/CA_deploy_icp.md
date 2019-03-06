---

copyright:
  years: 2018, 2019
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

# Implementando uma autoridade de certificação no {{site.data.keyword.cloud_notm}} Private
{: #ca-deploy}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Após a importação do gráfico do Helm do {{site.data.keyword.blockchainfull}} Platform no {{site.data.keyword.cloud_notm}} Private, é possível implementar os componentes individuais. A Autoridade de Certificação (CA) é a raiz de confiança para sua organização e permite gerar credenciais para os outros componentes que serão implementados. Como resultado, é necessário implementar uma CA antes de implementar os outros componentes. Cada organização em uma rede de blockchain multi-cloud deve implementar sua própria CA.  Para obter mais informações sobre autoridades de certificação e a função que elas desempenham em uma rede de blockchain, consulte [Autoridades de certificação](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-ca).
{:shortdesc}

Antes de implementar uma Autoridade de Certificação, revise as [Considerações e limitações](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations).

## Recursos necessários
{: #ca-deploy-resources-required}

Assegure-se de que o cluster do {{site.data.keyword.cloud_notm}} Private atenda aos requisitos mínimos de recurso de hardware:

| Componente | vCPU | RAM | Disco para armazenamento de dados |
|-----------|------|-----|-----------------------|
| Autoridade de certificação | 1 |192 MB | 1 GB |

**Notas:**
- Um vCPU é um núcleo virtual que será designado a uma máquina virtual ou a um núcleo do processador físico se o servidor não for particionado para máquinas virtuais. É necessário considerar os requisitos de vCPU ao decidir o virtual processor core (VPC) de sua implementação no {{site.data.keyword.cloud_notm}} Private. A VPC é uma unidade de medida para determinar o custo de licenciamento de produtos IBM. Para obter mais informações sobre os cenários para decidir o VPC, consulte [Virtual processor core (VPC) ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "Virtual Processor Core").
- O requisito de armazenamento de dados depende de quantas identidades e certificados são armazenados. O armazenamento da autoridade de certificação não é tão pesado quanto o armazenamento de peer ou de solicitador, mas isso dependerá do caso de uso. Quanto mais usuários, mais armazenamento é necessário.
- Esses níveis de recursos mínimos são suficientes para teste e experimentação. Para um ambiente com um grande número de transações, é importante alocar uma quantidade suficientemente grande de armazenamento para sua autoridade de certificação. A quantidade de armazenamento a ser usada depende do número de transações e do número de assinaturas que são necessárias de sua rede. Se você esgotar o armazenamento em sua autoridade de certificação, será necessário implementar uma nova CA com um sistema de arquivos maior e deixá-la sincronizar por meio de suas outras autoridades de certificação nos mesmos canais.

## Armazenamento
{: #ca-deploy-storage}

É necessário determinar o armazenamento que sua CA usará. Se você usar as configurações padrão, o gráfico Helm criará uma nova Solicitação de Volume Persistente (PVC) de 2 Gi com o nome de `fabric-ca-pvc` para sua CA.

Se você não desejar usar as configurações de armazenamento padrão, assegure-se de que um *novo* `storageClass` seja configurado durante a instalação do {{site.data.keyword.cloud_notm}} Private ou o administrador do sistema do Kubernetes precisará criar um storageClass antes de implementar a CA.

É possível optar por implementar a CA em plataformas AMD64 ou S390X. No entanto, esteja ciente de que o [Fornecimento dinâmico ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) está disponível somente para nós AMD64 no {{site.data.keyword.cloud_notm}} Private. Se o cluster incluir uma combinação de nós do trabalhador S390X e AMD64, o fornecimento dinâmico não poderá ser usado.

Se você não usar o fornecimento dinâmico, [Volumes Persistentes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) deverão ser criados e configurados com rótulos que possam ser usados para refinar o processo de ligação de PVC do Kubernetes.

## Pré-requisitos para implementar uma CA
{: #ca-deploy-prerequisites}

1. Antes de poder instalar uma CA no {{site.data.keyword.cloud_notm}} Private, deve-se [instalar o {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup) e [instalar o gráfico do Helm do {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

2. Se você usar a Community Edition e desejar executar esse gráfico do Helm em um cluster do {{site.data.keyword.cloud_notm}} Private sem conectividade com a Internet, será necessário criar archives em uma máquina conectada à Internet antes de ser possível instalar os archives no cluster do {{site.data.keyword.cloud_notm}} Private. Para obter mais informações, veja [Incluindo aplicativos de destaque em clusters sem conectividade à Internet ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Incluindo aplicativos de destaque em clusters sem conectividade à Internet"){:new_window}. Observe que é possível localizar o arquivo de especificação manifest.yaml em ibm-blockchain-platform-dev/ibm_cloud_pak no gráfico Helm.

3. Recupere o valor do endereço IP do Proxy do cluster por meio do console do {{site.data.keyword.cloud_notm}} Private. **Nota:** você precisará ser um [Administrador de cluster ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Funções e ações de administrador de cluster") para acessar seu IP de proxy. Efetue login no cluster do {{site.data.keyword.cloud_notm}} Private. No painel de navegação à esquerda, clique em **Plataforma** e, em seguida, em **Nós** para visualizar os nós que estão definidos no cluster. Clique no nó com a função `proxy` e, em seguida, copie o valor do `IP do host` da tabela.

  Salve esse valor e você o usará quando configurar o campo `Proxy IP` do gráfico do Helm.
  {:important}

4. Crie o nome do usuário administrativo e a senha do CA e armazene-os dentro de um objeto de segredo no {{site.data.keyword.cloud_notm}} Private. É possível localizar as etapas para criar o segredo na [próxima seção](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-admin-secret).

## Criando o segredo do administrador de CA
{: #ca-deploy-admin-secret}

Quando sua CA é iniciada pela primeira vez, ela contém uma identidade de administrador que você cria para operar sua CA. É necessário criar um nome de usuário e uma senha para essa identidade de administrador antes de implementar a CA. É importante tomar nota desses valores. Eles serão usados posteriormente quando você operar sua CA e implementar componentes de rede adicionais. Crie um [Segredo do Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/configuration/secret/ "Segredos") para armazenar o `username` e a `password` do administrador para sua implementação de CA. Um segredo do Kubernetes permite que você proteja e compartilhe informações sem ter que passá-las diretamente para a implementação.

1. Crie um nome de usuário e uma senha de administrador e codifique os valores no formato base64. Execute os comandos a seguir em uma janela do terminal e substitua o valor de `admin` e `adminpw` pelos valores que você deseja usar.
  ```
  echo -n 'admin' | base64
  echo -n 'adminpw' | base64
  ```
  {:code_block}

  **Importante:** salve os valores de `admin` e `adminpw` que são criados acima. Além disso, salve os valores codificados desses campos, que são necessários quando você cria o objeto secreto do Kubernetes.

2. Efetue login no console do {{site.data.keyword.cloud_notm}} Private. No painel de navegação à esquerda, clique em **Configuração** e, em seguida, em **Segredos**. Clique no botão **Criar segredo** para abrir um painel pop que permite gerar um novo objeto de segredo.

3. Na guia **Geral**, preencha os campos a seguir:
  - **Nome:** forneça ao seu segredo um nome exclusivo dentro de seu cluster. Você usará esse nome para configurar sua CA. O nome deve ser todo em minúsculas.
  - **Namespace:**  o espaço de nomes para incluir seu segredo. Selecione o `namespace` no qual você deseja implementar sua CA.
  - **Tipo:** insira o valor `Opaque`.

4. Deixe a guia **Anotações** em branco.

5. Na guia **Dados**, inclua o nome do usuário e a senha como pares chave-valor.
  1. No primeiro campo **Nome**, insira `ca-admin-name`.
  2. No primeiro campo **Valor**, insira o resultado de `echo -n admin | base64` da etapa um acima.
  3. Clique no botão **Incluir dados** para incluir um segundo par chave-valor.
  4. No segundo campo **Nome**, insira `ca-admin-password`.
  5. No segundo campo **Valor**, insira o resultado de `echo -n adminpw | base64` da etapa um acima.  
    A **Figura 1** mostra o que o segredo do console do {{site.data.keyword.cloud_notm}} Private pode conter dependendo dos valores especificados para as chaves `ca-admin-name` e `ca-admin-password`.

    ![Segredo do console do {{site.data.keyword.cloud_notm}} Private](../images/CreateCASecret.png "Segredo do console do {{site.data.keyword.cloud_notm}} Private")  
    *Figura 1. Segredo do console do {{site.data.keyword.cloud_notm}} Private*
  6. Clique em **Criar** para formar um novo objeto Segredo.

O gráfico Helm da CA requer que você use os nomes `ca-admin-name` e `ca-admin-password` como os valores de chave para implementar o segredo para a liberação do Helm.

**Nota:** o segredo do administrador de CA não é removido de seu cluster do {{site.data.keyword.cloud_notm}} Private quando você exclui sua liberação do Helm. Você é responsável por gerenciar seus segredos em seu cluster do {{site.data.keyword.cloud_notm}} Private. Se você planeja implementar outra CA no futuro, é possível reutilizar o segredo do administrador da CA. Caso contrário, você será responsável por excluí-lo de seu cluster do {{site.data.keyword.cloud_notm}} Private.

## Configuração
{: #ca-deploy-configuration}

Depois de criar seu segredo administrativo de CA, é possível usar as etapas a seguir para configurar e instalar sua CA. É possível instalar apenas uma CA por vez.

1. Efetue login no console do {{site.data.keyword.cloud_notm}} Private e clique no link **Catálogo** no canto superior direito.
2. Clique em `Blockchain` no painel de navegação à esquerda para localizar o tile rotulado `ibm-blockchain-platform-prod` ou `ibm-blockchain-platform-dev` se tiver transferido por download o Community Edition. Clique no tile para abri-lo e é possível ver um arquivo Leia-me que inclui informações sobre como instalar e configurar o gráfico do Helm.
3. Clique na guia **Configuração** na parte superior do painel ou clique no botão **Configurar** no canto inferior direito.
4. Especifique os valores para os [Parâmetros de configuração geral](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-global-parameters) e aceite o contrato de licença.
5. Abra a seta `Todos os parâmetros` e especifique o valor para os [Parâmetros de configuração global](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-global-parameters).
6. Role para baixo até a seção **Configuração da CA**. Marque a caixa de seleção `Instalar CA` e conclua as [definições de configuração](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-parameters) para a CA.  
7. Clique em **Instalar**.

### Parâmetros de configuração
{: #ca-deploy-configuration-parms}

A tabela a seguir lista os parâmetros configuráveis do {{site.data.keyword.blockchainfull_notm}} Platform **específicos do componente de CA** e seus valores padrão.  

**Embora a IU do gráfico do Helm diga que nenhuma configuração adicional é necessária, é necessário inserir determinados parâmetros para implementar uma CA.**

#### Parâmetros de configuração global e geral
{: #ca-deploy-global-parameters}

|  Parâmetro     | Descrição    | Padrão  | Requerido |
| --------------|-----------------|-------|------- |
| `Helm release name`| O nome de sua liberação do Helm. Deve iniciar com uma letra minúscula e terminar com qualquer caractere alfanumérico, deve conter apenas hifens e caracteres alfanuméricos minúsculos. Deve-se usar um nome de liberação do Helm exclusivo toda vez que tentar instalar um componente. | nenhum | sim |
| ` Namespace de destino `| Escolha o namespace do Kubernetes para instalar o gráfico do Helm. | nenhum | sim |
|**Configuração global**| Parâmetros que se aplicam a todos os componentes no gráfico do Helm|||
| `Service account name`| Insira o nome da [conta do serviço ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ "Configurar contas do serviço para Pods") que você usará para executar o pod. | padrão | não |

#### Parâmetros de configuração de CA
{: #ca-deploy-parameters}

|  Parâmetro     | Descrição    | Padrão  | Requerido |
| --------------|-----------------|-------|------- |
| `Install CA`| Selecione para instalar uma CA. | desmarcado | sim, se você deseja implementar uma CA |
| `CA name`| Especifique um nome a ser usado para a Autoridade de Certificação. **Importante:** tome nota desse valor. Ele é necessário posteriormente quando você configura um solicitador ou peer. | SampleOrgCA | sim |
| `CA worker node architecture`| Selecione sua arquitetura de nó do trabalhador do {{site.data.keyword.cloud_notm}} Private (ADM64 ou S390X). | AMD64 | sim|
| `CA database type`| O tipo de banco de dados para armazenar dados de CA. Apenas SQLite é suportado. | SQLite | sim |
| `CA data persistence enabled` | Se marcado, os dados estarão disponíveis quando o contêiner for reiniciado. Caso contrário, todos os dados serão perdidos no caso de um failover ou reinicialização de pod. | marcado | não |
| `CA use dynamic provisioning` | Marque para ativar o fornecimento dinâmico para volumes de armazenamento. | marcado | não |
| `CA storage class name`| Especifique um nome de classe de armazenamento exclusivo. Caso contrário, a classe de armazenamento padrão no cluster será usada. | nenhum | Depende de como o cluster do {{site.data.keyword.cloud_notm}} Private está configurado. Verifique com seu administrador de cluster |
| `CA existing volume claim`| Especifique o nome de uma Solicitação de volume existente e deixe todos os outros campos em branco. | nenhum | não |
| `CA selector label`| [Rótulo do seletor ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Rótulos e seletores") para seu PVC. | nenhum | não |
| `CA selector value`| [Valor do seletor ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Rótulos e seletores") para seu PVC. | nenhum | não |
| `CA storage access mode`| Especifique o [modo de acesso ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modos de acessos") de armazenamento para o PVC. | ReadWriteMany | sim |
| `CA volume claim size`| Escolha o tamanho do disco a ser usado. | 2 Gi | sim |
| `CA image repository`| Local do gráfico Helm da CA. | ibmcom/ibp-fabric-ca | sim |
| `CA Docker image tag`| Valor da tag que está associada à imagem de CA. Esse campo é preenchido automaticamente para a versão de imagem. Não mude isso.| 1.2.1 | sim |
| `CA service type` | Usado para especificar se [portas externas devem ser expostas ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) no peer. Selecione NodePort para expor as portas externamente (recomendado) e ClusterIP para não expor as portas. O LoadBalancer e o ExternalName não são suportados nesta liberação | NodePort | sim |
| `CA secret (Required)`| Insira o nome do objeto secreto do Kubernetes que você criou para seu `ca-admin-name` e `ca-admin-password`. | nenhum | sim |
| `CA CPU request`| Especifique o número mínimo de CPUs a serem alocadas para a CA. | 1 | sim |
| `CA CPU limit`| Especifique o número máximo de CPUs a serem alocadas para a CA. | 2 | sim |
| `CA memory request`| Especifique a quantidade mínima de memória a ser alocada para a CA. | 1 Gi | sim |
| `CA memory limit`| Especifique a quantidade máxima de memória a ser alocada para a CA. | 4Gi | sim |
| `CA TLS instance name`| Especifique um nome da instância TLS da CA que será usada para inscrever um solicitador ou peer. | tlsca | sim |
| `CSR common name`| Especifique o Nome Comum (CN) que o cert raiz da CA gerado apresentará quando contatado. | tlsca-common | sim |
| `Proxy IP`| Insira o [IP do Nó do Proxy para o cluster ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/install_proxy.html "Instalação do IBM Cloud Private por trás de um proxy HTTP") no qual a CA é implementada. | 127.0.0.1 | sim |


### Usando a linha de comandos do Helm para instalar a liberação do Helm
{: #ca-deploy-helm-cli}

Como alternativa, é possível usar a CLI do Helm para instalar a liberação do Helm. Antes de executar o comando `helm install`, assegure-se de [incluir o repositório do Helm de seu cluster no ambiente da CLI do Helm ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Incluindo o repositório do Helm interno na CLI do Helm").

É possível configurar os parâmetros que são necessários para instalação criando um arquivo `yaml` e transmitindo-o para o comando `helm install` a seguir.

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
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values ca-s390x-values.yaml \
--tls
```

É possível criar um novo arquivo `yaml` editando `values.yaml` que está incluído no archive transferido por download. O arquivo `values.yaml` inclui todos os parâmetros necessários com suas configurações padrão.

## Verificando a instalação da CA
{: #verifiying-ca-installation}

Depois de concluir os parâmetros de configuração e clicar no botão **Instalar**, clique no botão **Visualizar liberação do Helm** para visualizar sua implementação. Se isso foi bem-sucedido, você verá o valor 1 nos campos `DESIRED`, `CURRENT`, `UP TO DATE` e `AVAILABLE` na tabela Implementação. Você pode precisar clicar em atualizar e aguardar que a tabela seja atualizada. Também é possível localizar a tabela Implementação clicando no ícone **Menu** no canto superior esquerdo no console do {{site.data.keyword.cloud_notm}} Private. Na lista de menu, clique em **Cargas de trabalho** e, em seguida, **Liberações do Helm**.

Se você rolar para baixo para a seção `Notes`, será possível localizar informações importantes que serão usadas para [operar sua CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate).

Depois de instalar a CA do {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.cloud_notm}} Private, um configmap será criado com as configurações de variáveis de ambiente padrão. Em seguida, é possível mudar ou incluir variáveis de ambiente para o servidor da CA para configurar seu comportamento. Para obter mais informações sobre os parâmetros de configuração do servidor da CA, consulte [Documentação do servidor da CA do Fabric ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric-ca.readthedocs.io/en/latest/users-guide.html#fabric-ca-server "Servidor da CA do Fabric").

Depois de configurar o configmap, será necessário reiniciar o servidor da CA antes que as mudanças entrem em vigor. Para reiniciar o servidor da CA, é possível excluir o POD do servidor de CA do Fabric. O {{site.data.keyword.cloud_notm}} Private criará um novo POD que reflita as mudanças.

## Visualizando os logs de CA
{: #ca-deploy-view-logs}

Os logs de componentes podem ser visualizados na linha de comandos usando os [`kubectl CLI commands`](/docs/services/blockchain/howto/CA_operate.html#ca-operate-kubectl-configure) ou por meio do [Kibana ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.elastic.co/products/kibana "Sua janela no Elastic Search"), que está incluído no cluster do {{site.data.keyword.cloud_notm}} Private. Para obter mais informações, consulte estas [instruções para acessar os logs](/docs/services/blockchain/howto/CA_operate.html#ca-operate-view-logs).

## Operando sua CA
{: #ca-deploy-operate}

Sua CA será a raiz de confiança para a sua organização. É necessário usar sua CA para gerar certificados para seus outros componentes.  Como resultado, antes de implementar um solicitador ou um peer, deve-se [configurar sua CA e concluir várias etapas operacionais](/docs/services/blockchain/howto/CA_operate.html#ca-operate)
