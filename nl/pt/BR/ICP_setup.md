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

# Configurando o  {{site.data.keyword.cloud_notm}}  Privado
{: #icp-setup}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Antes de implementar os componentes do {{site.data.keyword.blockchainfull}} Platform e construir a rede de blockchain no {{site.data.keyword.cloud_notm}} Private, é necessário configurar o {{site.data.keyword.cloud_notm}} Privado em seu próprio ambiente.
{:shortdesc}

## Pré-requisitos
{: #icp-setup-prerequisites}

Preencha os pré-requisitos a seguir e prepare seu ambiente para instalar o {{site.data.keyword.cloud_notm}} Private.

### Docker
O {{site.data.keyword.cloud_notm}} Private requer que o Docker esteja instalado. Siga as instruções relacionadas em [Instalando o {{site.data.keyword.cloud_notm}} Private ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/install.html "Installing o {{site.data.keyword.cloud_notm}} Private") para instalar o Docker.

### {{site.data.keyword.cloud_notm}}  Configurações privadas
Antes de instalar o {{site.data.keyword.cloud_notm}} Private, as dicas a seguir são úteis para preparar seus nós para a instalação do {{site.data.keyword.cloud_notm}} Private. Pré-requisitos adicionais do {{site.data.keyword.cloud_notm}} Private podem ser localizados na [documentação do {{site.data.keyword.cloud_notm}} Private ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/prep.html "Preparando o seu cluster para instalação").

#### Atualize a configuração de `vm.max_map_count`
O {{site.data.keyword.cloud_notm}} Private usa o Elastic Search para criação de log e medição. Para evitar exceções de falta de memória, o Elastic Search requer que a propriedade de sistema `vm.max_map_count` seja configurada. Antes de instalar o {{site.data.keyword.cloud_notm}} Private, veja as [Instruções de configuração do Elastic Search ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html "Memória virtual") para configurar essa propriedade em cada nó. É possível usar os comandos a seguir para configurar essa propriedade permanentemente:

```
sysctl -w vm.max_map_count=262144; sysctl vm.max_map_count
echo "vm.max_map_count=262144” | tee -a /etc/sysctl.conf
```
{:codeblock}

#### Configure o arquivo `/etc/hosts` em cada nó em seu cluster

- O {{site.data.keyword.cloud_notm}} Private usa o [Kubernetes ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/tutorials/kubernetes-basics/ "Saiba os princípios básicos do Kubernetes") para gerenciar aplicativos conteinerizados. O servidor de nomes de domínio (DNS) do Kubernetes falhará se os nomes de host não estiverem configurados no arquivo `/etc/hosts` em cada nó. [Insira o endereço IP, o nome do host e o nome abreviado de cada nó em seu cluster ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/installing/prep_cluster.html "Configurando seu cluster") no arquivo `/etc/hosts` em cada nó.

- [O IPv6 não é suportado pelo {{site.data.keyword.cloud_notm}} Private ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/getting_started/known_issues.html#ipv6 "O IPv6 não é suportado"). Para evitar problemas com o serviço DNS em um cluster do {{site.data.keyword.cloud_notm}} Private, desative as configurações de IPv6 no arquivo `/etc/hosts` em cada nó, comentando a linha a seguir com um sinal `#` no início da linha:
  ```
  #::1  localhost ip6-localhost ip6-loopback
  ```
  {:codeblock}

### Recursos necessários
{: #icp-setup-resources}

Assegure-se de que seu sistema {{site.data.keyword.cloud_notm}} Private atenda aos requisitos mínimos de recurso de hardware:

| Componente | vCPU | RAM | Disco para armazenamento de dados |
|-----------|------|-----|-----------------------|
| Autoridade de certificação | 1 |192 MB | 1 GB |
| Solicitador | 2 | 512 MB | 100 GB com a capacidade de expandir |
| Peer | 2 | 2 GB | 50 GB com a capacidade de expandir |
| CouchDB para peer | 2| 2 GB |50 GB com a capacidade de expandir |

 **Notas:**
 - Um vCPU é um núcleo virtual que será designado a uma máquina virtual ou a um núcleo do processador físico se o servidor não for particionado para máquinas virtuais. É necessário considerar os requisitos de vCPU ao decidir o virtual processor core (VPC) de sua implementação no {{site.data.keyword.cloud_notm}} Private. VPC é uma unidade de medida para determinar o custo de licenciamento de produtos {{site.data.keyword.IBM_notm}}. Para obter mais informações sobre os cenários para decidir o VPC, consulte [Virtual processor core (VPC) ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Esses níveis de recursos mínimos são suficientes para teste e experimentação. Para um ambiente com um grande volume de transações, é importante alocar uma quantidade suficientemente grande de armazenamento; 250 GB para seu peer e 500 GB para seu serviço de ordenação como um exemplo. A quantidade de armazenamento a ser usada depende do número de transações e do número de assinaturas que são necessárias de sua rede. Se você está prestes a esgotar o armazenamento em seu peer ou solicitador, deve-se implementar um novo peer ou solicitador com um sistema de arquivos maior e permitir que ele seja sincronizado por meio de seus outros componentes nos mesmos canais.

#### Considerações sobre armazenamento
{: #icp-setup-storage-considerations}

* É necessário determinar o armazenamento que seus componentes usarão. Se você usar as configurações padrão, o gráfico Helm do Peer criará uma nova solicitação de Volume Persistente com o nome de `my-data-pvc` para seus dados do peer. Se você selecionar CouchDB como seu banco de dados de livro-razão, o gráfico Helm criará outra solicitação de Volume Persistente com o nome `statedb-pvc` para o banco de dados de livro-razão.
* Se você não deseja usar as configurações de armazenamento padrão, assegure-se de que uma *nova* storageClass seja configurada durante a instalação do {{site.data.keyword.cloud_notm}} Private ou o administrador do sistema do Kubernetes precisará criar uma storageClass antes da implementação.
* [O fornecimento dinâmico ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Fornecimento de volume dinâmico") está disponível somente para os nós amd64 no {{site.data.keyword.cloud_notm}} Private. Portanto, se o seu cluster incluir uma combinação de nós do trabalhador s390x e amd64, o fornecimento dinâmico não poderá ser usado.
* Se o fornecimento dinâmico não for usado, [Volumes persistentes ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Volumes persistentes") deverão ser criados e configurados com rótulos que possam ser usados para refinar o processo de ligação de Solicitação de Volume Persistente (PVC) do Kubernetes.
* Se você usar Volumes Persistentes do NFS v2/v3, deverá ativar o módulo **Monitor de status NFS para os bloqueios de sistema de arquivos NFSv2/v3**, que também é conhecido como **rpc-statd**, no sistema host no qual o sistema de arquivos NFS existe. Esse módulo permite que o sistema de arquivos NFS verifique bloqueios exclusivos em arquivos que outros processos retêm. Execute os comandos a seguir para ativar esse módulo:

  ```
  sudo systemctl enable rpc-statd
  ```
  {:codeblock}

  ```
  sudo systemctl start rpc-statd
  ```
  {:codeblock}

## Instalando o  {{site.data.keyword.cloud_notm}}  Privado
{: #icp-setup-install}

Conclua as etapas a seguir para instalar e configurar o {{site.data.keyword.cloud_notm}} Private em seu ambiente.

1. Instale um cluster do [{{site.data.keyword.cloud_notm}} Private ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/kc_welcome_containers.html) na versão 3.1.0. Se você desejar usar o gráfico do Helm para desenvolvimento, teste ou experimentação, será possível instalar o [{{site.data.keyword.cloud_notm}} Private Community Edition versão 3.1.0 ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private-CE versão 3.1.0") gratuitamente.

2. Instale o {{site.data.keyword.cloud_notm}} Private CLI [3.1.0 ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/manage_cluster/install_cli.html) para instalar e operar a CA.

Depois de instalar o {{site.data.keyword.cloud_notm}} Private, será possível continuar a [importar o gráfico do Helm do {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install) para o seu cluster do {{site.data.keyword.cloud_notm}} Private.
