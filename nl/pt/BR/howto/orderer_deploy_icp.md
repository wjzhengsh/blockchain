---

copyright:
  years: 2018, 2019
lastupdated: "2019-04-23"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Implementando um solicitador no {{site.data.keyword.cloud_notm}} Private
{: #icp-orderer-deploy}

Os solicitadores autenticam clientes, transações de pedidos e transações de transmissão em uma rede de blockchain com o componente solicitador. Para obter mais informações sobre os solicitadores e a função que eles desempenham em uma rede de blockchain, consulte [Visão geral sobre componentes de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview).
{:shortdesc}

Antes de implementar um serviço de ordenação, revise as [Considerações e limitações](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations).

## Recursos necessários
{: #icp-orderer-deploy-resources-required}

Assegure-se de que seu sistema {{site.data.keyword.cloud_notm}} Private atenda aos requisitos mínimos de recurso de hardware:

| Componente | vCPU | RAM | Disco para armazenamento de dados |
|-----------|------|-----|-----------------------|
| Solicitador | 2 | 512 MB | 100 GB com a capacidade de expandir |


 **Notas:**
 - Um vCPU é um núcleo virtual que será designado a uma máquina virtual ou a um núcleo do processador físico se o servidor não for particionado para máquinas virtuais. É necessário considerar os requisitos de vCPU ao decidir o virtual processor core (VPC) de sua implementação no {{site.data.keyword.cloud_notm}} Private. A VPC é uma unidade de medida para determinar o custo de licenciamento de produtos IBM. Para obter mais informações sobre os cenários para decidir o VPC, consulte [Virtual processor core (VPC) ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Esses níveis de recursos mínimos são suficientes para teste e experimentação. Para um ambiente com um grande volume de transações, é importante alocar uma quantidade suficientemente grande de armazenamento; 500 GB para seu serviço de ordenação, por exemplo. A quantidade de armazenamento a ser usada depende do número de transações e do número de assinaturas que são necessárias de sua rede. Se você estiver prestes a esgotar o armazenamento em seu solicitador, deverá implementar um novo solicitador com um sistema de arquivos maior e permitir que ele sincronize por meio de outros componentes nos mesmos canais.

## Armazenamento
{: #icp-orderer-deploy-storage}

É necessário determinar o armazenamento que seu solicitador usará. Se você usar as configurações padrão, o gráfico do Helm criará uma nova Solicitação de Volume Persistente (PVC) de 8 Gi com o nome de `orderer-data` para os seus dados do solicitador.

Se você não desejar usar as configurações de armazenamento padrão, assegure-se de que uma *nova* `storageClass` seja configurada durante a instalação do {{site.data.keyword.cloud_notm}} Private ou o administrador do sistema do Kubernetes precisará criar uma storageClass antes da implementação.

É possível optar por implementar o solicitador em plataformas amd64 ou s390x. No entanto, esteja ciente de que o [Fornecimento dinâmico ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Fornecimento de volume dinâmico") está disponível somente para nós AMD64 no {{site.data.keyword.cloud_notm}} Private. Se o cluster incluir uma combinação de nós do trabalhador s390x e amd64, o fornecimento dinâmico não poderá ser usado.

Se você não usar o fornecimento dinâmico, [Volumes persistentes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Volumes persistentes") deverão ser criados e configurados com rótulos que podem ser usados para refinar o processo de ligação do PVC do Kubernetes.


## Pré-requisitos para implementar um solicitador
{: #icp-orderer-deploy-prerequisites}

1. Antes de poder instalar um solicitador no {{site.data.keyword.cloud_notm}} Private, deve-se [instalar o {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup) e [instalar o gráfico do Helm do {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

2. Se você usar a Community Edition e desejar executar esse gráfico do Helm em um cluster do {{site.data.keyword.cloud_notm}} Private sem conectividade com a Internet, será necessário criar archives em uma máquina conectada à Internet antes de ser possível instalar os archives no cluster do {{site.data.keyword.cloud_notm}} Private. Para obter mais informações, veja [Incluindo aplicativos de destaque em clusters sem conectividade à Internet ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_package_offline.html "Incluindo aplicativos de destaque em clusters sem conectividade à Internet"){:new_window}. Observe que é possível localizar o arquivo de especificação `manifest.yaml` em ibm-blockchain-platform-dev/ibm_cloud_pak no gráfico Helm.

3. Recupere o valor do endereço IP do Proxy do cluster de sua CA no console do {{site.data.keyword.cloud_notm}} Private. **Nota:** você precisará ser um [Administrador de cluster ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/user_management/assign_role.html "Funções e ações de administrador de cluster") para acessar seu IP de proxy. Efetue login no cluster do {{site.data.keyword.cloud_notm}} Private. No painel de navegação à esquerda, clique em **Plataforma** e, em seguida, em **Nós** para visualizar os nós que estão definidos no cluster. Clique no nó com a função `proxy` e, em seguida, copie o valor do `IP do host` da tabela. **Importante:** salve esse valor e você o usará ao configurar o campo `Proxy IP` do gráfico do Helm.

4. Crie um [arquivo de configuração do solicitador e armazene-o como um segredo do Kubernetes no {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-config-file).

## Criando um arquivo de configuração do solicitador
{: #icp-orderer-deploy-config-file}

Antes de implementar um solicitador, é necessário criar um arquivo de configuração contendo informações importantes sobre a identidade do solicitador e sua CA. Em seguida, é necessário passar esse arquivo para o gráfico do Helm durante a configuração usando um objeto [Segredo do Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/configuration/secret/). Esse arquivo permitirá que o solicitador obtenha os certificados que ele precisa da CA para ingressar em uma rede de blockchain. Ele também contém um certificado de administrador que permitirá que você opere o solicitador como um usuário administrador. Siga as instruções em [usando a CA para implementar um solicitador ou peer](/docs/services/blockchain/howto/CA_operate.html#ca-operate-deploy-orderer-peer) antes de configurar o solicitador.

É necessário fornecer os nomes de host do CSR para o arquivo de configuração. Os nomes do host do CSR incluem o endereço IP do proxy do cluster no qual você implementará o componente, assim como o nome do host do serviço que será seu nome do host do gráfico do Helm. O `service host name` é baseado no `helm release name` que você especifica durante a implementação. O `service host name` é o `helm_release_name` que você especifica com a sequência `-orderer` incluída no final. Por exemplo, se você especificar um `helm release name` igual a `orderer1`, será possível inserir o valor a seguir na seção `"csr"` do arquivo:

```
"csr": {
  "hosts": [
    "9.42.134.44",
    "orderer1-orderer"
  ]
}
```

Depois de salvar o arquivo de configuração, é necessário codificá-lo no formato base64 antes de fornecer o arquivo para o {{site.data.keyword.cloud_notm}} Private como um objeto de segredo. Um segredo do Kubernetes permite que você proteja e compartilhe informações sem ter que passá-las diretamente para a implementação.

1. Codifique o arquivo de configuração no formato base64, executando os comandos a seguir a partir de uma janela do terminal:

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat <config_file> | base64 $FLAG
  ```
  {:codeblock}

  **Nota:** é importante que a sequência gerada usando o comando acima seja formatada como uma única linha. Isso deve ser semelhante a:

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
   ```
   não assim:

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
   ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
   Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
   VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
   WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
   ```

  Salve a saída resultante para a Etapa 4 abaixo.

2. Efetue login no console do {{site.data.keyword.cloud_notm}} Private. No painel de navegação à esquerda, clique em **Configuração** e, em seguida, em **Segredos**. Clique no botão **Criar segredo** para abrir um painel pop que permite gerar um novo objeto de segredo.

3. Na guia **Geral**, preencha os campos a seguir:
  - **Nome:** forneça ao seu segredo um nome exclusivo dentro de seu cluster. Você usará esse nome ao implementar seu solicitador. O nome deve ser todo em minúsculas.  
  **Nota:** quando você implementa um solicitador, um novo segredo é gerado automaticamente pela implementação com o nome `<helm_release_name>-orderer-mspsecret`. Portanto, quando você nomear seu segredo, certifique-se de que ele seja diferente de `<helm_release_name>-orderer-mspsecret`. Caso contrário, a implementação do gráfico do helm falhará porque o segredo que ele tenta criar já existe.
  - **Namespace:** o namespace para incluir seu segredo. Selecione o `namespace` no qual você deseja implementar seu solicitador.
  - **Tipo:** insira o valor `Opaque`.

4. Clique na guia **Dados** na área de janela de navegação à esquerda dessa janela. Nos campos `Name`, especifique `secret.json` e, no campo de valor, cole na sequência codificada `base64` de seu arquivo de configuração.

5. Clique em **Criar** para salvar seu objeto de segredo.

**Nota:** o segredo de configuração do solicitador não será removido de seu cluster do {{site.data.keyword.cloud_notm}} Private quando você excluir sua liberação do Helm. Se você excluir seu solicitador, será necessário excluir esse segredo também.

## Configuração
{: #icp-orderer-deploy-configuration}


Depois de criar o objeto de segredo de configuração do solicitador, é possível configurar e instalar o solicitador no {{site.data.keyword.cloud_notm}} Private com as etapas a seguir. É possível instalar apenas um solicitador por vez.

1. Efetue login no console do {{site.data.keyword.cloud_notm}} Private e clique no link **Catálogo** no canto superior direito.
2. Clique em `Blockchain` no painel de navegação à esquerda para localizar o tile rotulado `ibm-blockchain-platform-prod` ou `ibm-blockchain-platform-dev` se tiver transferido por download o Community Edition. Clique no tile para abri-lo e é possível ver um arquivo Leia-me que inclui informações sobre como instalar e configurar o gráfico do Helm.
3. Clique na guia **Configuração** na parte superior do painel ou clique no botão **Configurar** no canto inferior direito.
4. Especifique os valores para os [Parâmetros de configuração geral](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-configuration-parms) e aceite o contrato de licença.
5. Abra a seta `Todos os parâmetros` e especifique o valor para os [Parâmetros de configuração global](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-global-parameters).
6. Role para baixo até a seção **Configuração do solicitador**. Marque a caixa de seleção `Instalar solicitador` e conclua as [definições de configuração](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-parameters) para o solicitador.
7. Clique em **Instalar**.


**Nota:** ao implementar o solicitador no s390x, o contêiner de inicialização pode retornar o erro a seguir:

`panic: Error while trying to open DB: no locks available`

Isso ocorre porque o solicitador usa um BD de arquivo simples e o Sistema de Arquivos NFS requer um pacote adicional para permitir que o serviço de pedido consulte os arquivos para verificar se há bloqueios exclusivos sobre eles e para criar bloqueios exclusivos. Para corrigir o problema, deve-se ativar o pacote `rpc-statd`.

`sudo systemctl enable rpc-statd`
`sudo systemctl start rpc-statd`

Isso deve ser feito a partir sistema em que o sistema de arquivos NFS é atendido.

### Parâmetros de configuração
{: #icp-orderer-configuration-parms}

A tabela a seguir lista os parâmetros configuráveis do {{site.data.keyword.blockchainfull_notm}} Platform, **específicos para o componente do solicitador** e seus valores padrão. **Embora a IU do gráfico Helm diga que nenhuma configuração adicional é necessária, é necessário inserir determinados parâmetros para implementar um solicitador.**

#### Parâmetros de configuração global e geral
{: #icp-orderer-deploy-global-parameters}

|  Parâmetro     | Descrição    | Padrão  | Requerido |
| --------------|-----------------|-------|------- |
|**Parâmetros gerais**| Parâmetros que configuram o gráfico do Helm | | |
| `Helm release name`| O nome da liberação de helm. Deve iniciar com uma letra minúscula e terminar com qualquer caractere alfanumérico, deve conter apenas hifens e caracteres alfanuméricos minúsculos. Deve-se usar um nome de liberação do Helm exclusivo toda vez que tentar instalar um componente. **Importante:** esse valor deve corresponder ao valor que você usou para gerar o 'service host name' para o campo "hosts" em seu [arquivo de segredo JSON.](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy-config-file) | nenhum | sim  |
| ` Namespace de destino `| Escolha o namespace do Kubernetes para instalar o gráfico do Helm. | nenhum | sim |
| `Políticas de namespace de destino`| Exibe as políticas de segurança de pod do namespace escolhido, que devem incluir uma política **`ibm-privileged-psp`**. Caso contrário, [ligue uma PodSecurityPolicy](/docs/services/blockchain?topic=blockchain-icp-setup#icp-setup-psp) ao seu namespace. | nenhum | não |
|**Configuração global**| Parâmetros que se aplicam a todos os componentes no gráfico do Helm|||
| `Service account name`| Insira o nome da [conta do serviço ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) que você usará para executar o pod. | padrão | não |

#### Parâmetros de configuração do solicitador
{: #icp-orderer-deploy-parameters}

|  Parâmetro     | Descrição    | Padrão  | Requerido |
| --------------|-----------------|-------|------- |
| `Install Orderer`| Selecione para instalar um solicitador. | desmarcado | sim, se você deseja implementar um solicitador |
| `Orderer worker node architecture`| Selecione sua arquitetura de nó do trabalhador do {{site.data.keyword.cloud_notm}} Private (AMD64 ou S390X). | Arquitetura de detecção automática com base no nó principal | sim |
| `Orderer configuration`| É possível customizar a configuração do solicitador colando seu próprio arquivo de configuração `orderer.yaml` nesse campo. Para ver um arquivo `orderer.yaml` de amostra, consulte a configuração de amostra [`orderer.yaml` ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/hyperledger/fabric/blob/release-1.4/sampleconfig/orderer.yaml)**Somente para usuários avançados**. | nenhum | não |
| `Organization MSP secret (Required)`| Especifique o nome do objeto secreto que contém certificados MSP e chaves da organização. | nenhum | sim |
| `Orderer data persistence enabled` | Os dados estarão disponíveis quando o contêiner for reiniciado. Se desmarcado, todos os dados serão perdidos no caso de um failover ou de uma reinicialização de pod. | marcado | não |
| `Orderer use dynamic provisioning` | Marque para ativar o fornecimento dinâmico para volumes de armazenamento. | marcado | não |
| `Orderer image repository` | Local do gráfico Helm do Solicitador. Esse campo é preenchido automaticamente para o caminho instalado. Se você estiver usando o Community Edition e não tiver acesso à Internet, mude esse campo para o local em que você transferiu por download a imagem do solicitador do Fabric. | ibmcom/ibp-fabric-orderer | não |
| `Orderer Docker image tag`| Um registro da imagem do Docker. Esse campo é preenchido automaticamente para a versão de imagem. Não mude isso.| 1.4.0 | sim |
| `Orderer consensus type`| O tipo de consenso do serviço de ordenação. | SOLO | sim |
| `Orderer organization name`| Especifique o nome que você gostaria de usar para a organização do solicitador. Se você também planeja implementar peers, certifique-se de usar um nome diferente daquele que fornecerá aos seus peers. Por exemplo, forneça à sua organização do solicitador um nome como `ordererOrg` | nenhum | sim |
| `Orderer Org MSP ID`| Especifique o nome que deseja usar para o ID MSP da organização do solicitador. Esse deve ser o mesmo nome que você fornece à sua organização do solicitador e será configurado como uma variável de ambiente pelo processo de implementação. Tome nota desse valor, será necessário referenciá-lo posteriormente. | nenhum | sim |
| `Orderer storage class name`| Especifique um nome de classe de armazenamento para o solicitador. | nenhum | Depende de como o cluster do {{site.data.keyword.cloud_notm}} Private está configurado. Verifique com seu administrador de cluster |
| `Orderer existing volume claim`| Especifique o nome de uma Solicitação de volume existente e deixe todos os outros campos em branco. | nenhum | não |
| `Orderer selector label`| [Rótulo do seletor ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) para seu PVC. | nenhum | não |
| `Orderer selector value`| [Valor do seletor ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) para seu PVC. | nenhum | não |
| `Orderer storage access mode`| Especifique o [modo de acesso ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes) de armazenamento para o PVC. | ReadWriteMany | sim |
| `Orderer volume claim size`| Escolha o tamanho do disco a ser usado, que deve ser pelo menos 2 Gi. | 8 Gi | sim |
| `Orderer service type` | Usado para especificar se [portas externas devem ser expostas ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) no peer. Selecione NodePort para expor as portas externamente (recomendado) e ClusterIP para não expor as portas. O LoadBalancer e o ExternalName não são suportados nesta liberação | NodePort | sim |
| `Orderer CPU request`| Especifique o número mínimo de CPUs a serem alocadas para o Solicitador. | 1 | sim |
| `Orderer CPU limit`| Especifique o número máximo de CPUs a serem alocadas para o Solicitador. | 2 | sim |
| `Orderer memory request`| Especifique a quantidade mínima de memória a ser alocada para o Solicitador. | 1 Gi | sim |
| `Orderer memory limit`| Especifique a quantidade máxima de memória a ser alocada para o Solicitador. | 2 Gi | sim |
| `gRPC web proxy CPU request`| Especifique o número mínimo de CPUs, em millicpus (m), a serem alocadas para o proxy da web gRPC. | 100 m | sim |
| `gRPC web proxy CPU limit`| Especifique o número máximo de CPUs, em millicpus (m), a serem alocadas para o proxy da web gRPC. | 200 m | sim |
| `gRPC web proxy memory request`| Especifique a quantidade mínima de memória a ser alocada para o proxy da web gRPC. | 100 Mi | sim |
| `gRPC web proxy memory limit`| Especifique a quantidade máxima de memória a ser alocada para o proxy da web gRPC. | 200 Mi | sim |


### Usando a linha de comandos do Helm para instalar a liberação do Helm
{: #icp-orderer-deploy-helm-cli}

Como alternativa, é possível usar a CLI do Helm para instalar a liberação do Helm. Antes de executar o comando `helm install`, assegure-se de [incluir o repositório do Helm de seu cluster no ambiente da CLI do Helm ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/app_center/add_int_helm_repo_to_cli.html "Incluindo o repositório do Helm interno na CLI do Helm").

É possível configurar os parâmetros necessários para a instalação, criando um arquivo `yaml` e passando-o para o comando `helm install` a seguir.

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```
{:codeblock}

em que:

- `<helm_release name>` representa o nome que você deseja fornecer à liberação do helm.
- `<helm_chart>` representa o nome do gráfico do Helm que é importado para o catálogo.
- `<helm_chart_version>` representa a versão do gráfico do Helm que é importado para o catálogo.
- `<customvalues.yaml>` é o nome do arquivo yaml que contém os parâmetros de configuração.

Por exemplo:

```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values orderer-s390x-values.yaml \
--tls
```

É possível criar um novo a

## Verificando a instalação do solicitador
{: #icp-orderer-deploy-verify-install}

Depois de concluir os parâmetros de configuração e clicar no botão **Instalar**, clique no botão **Visualizar liberação do Helm** para visualizar sua implementação. Se isso foi bem-sucedido, você verá o valor 1 nos campos `DESIRED`, `CURRENT`, `UP TO DATE` e `AVAILABLE` na tabela Implementação. Você pode precisar clicar em atualizar e aguardar que a tabela seja atualizada. Também é possível localizar a tabela Implementação clicando no ícone **Menu** no canto superior esquerdo no console do {{site.data.keyword.cloud_notm}} Private. Na lista de menu, clique em **Cargas de trabalho** e, em seguida, **Liberações do Helm**.

## Visualizando os logs de solicitador
{: #icp-orderer-deploy-view-logs}

Os logs de componentes podem ser visualizados na linha de comandos usando os [`kubectl CLI commands`](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-kubectl-configure) ou por meio do [Kibana ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.elastic.co/products/kibana "Sua janela no Elastic Search"), que está incluído no cluster do {{site.data.keyword.cloud_notm}} Private. Para obter mais informações, consulte estas [instruções para acessar os logs](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate-orderer-view-logs).
