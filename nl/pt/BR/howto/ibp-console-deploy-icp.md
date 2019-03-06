---

copyright:
  years: 2019
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

# Implementando o console do  {{site.data.keyword.blockchainfull_notm}}  Platform no  {{site.data.keyword.cloud_notm}}  Private
{: #ibp-console-deploy-icp}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Estas instruções descrevem como implementar um console do {{site.data.keyword.blockchainfull}} Platform em sua própria infraestrutura por meio do {{site.data.keyword.cloud_notm}} Private. Você não precisa implementar o console no mesmo ambiente que os seus outros componentes de blockchain.
{:shortdesc}

Você usará um gráfico do Helm para implementar o console do {{site.data.keyword.blockchainfull_notm}} Platform em seu {{site.data.keyword.cloud_notm}} Private. Cada uma de suas implementações instala uma única instância do console em seu namespace do {{site.data.keyword.cloud_notm}} Private.

## Considerações
{: #ibp-console-deploy-icp-considerations}

Antes de implementar o console, assegure-se de que entenda as considerações a seguir:

- Os componentes de blockchain já devem ter sido implementados no {{site.data.keyword.cloud_notm}} Private ou Amazon Web Services (AWS). Se isso ainda não tiver sido feito, consulte [Sobre o {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private] ou Sobre o [IBM Blockchain Platform for AWS] para obter mais informações.
- Você é responsável pelo gerenciamento de monitoramento de funcionamento, segurança, criação de log e uso de recursos de seu console.
- É possível conectar seu console somente aos componentes de blockchain que estão no nível do Hyperledger Fabric v1.2.1.

## Recursos necessários
{: #ibp-console-deploy-icp-resources-required}

Assegure-se de que seu sistema {{site.data.keyword.cloud_notm}} Private atenda aos requisitos mínimos de recurso de hardware:

| Componente | vCPU | RAM | Disco para armazenamento de dados |
|-----------|------|-----|-----------------------|
| Console do IBP | 1 | 192 MB | 1 GB |
| CouchDB para console IBP | 1 | 1 GB | 1 GB |

 **Notas:**
 - Um vCPU é um núcleo virtual que será designado a uma máquina virtual ou a um núcleo do processador físico se o servidor não for particionado para máquinas virtuais. É necessário considerar os requisitos de vCPU ao decidir o virtual processor core (VPC) de sua implementação no {{site.data.keyword.cloud_notm}} Private. A VPC é uma unidade de medida para determinar o custo de licenciamento de produtos IBM. Para obter mais informações sobre os cenários para decidir o VPC, consulte [Virtual processor core (VPC) ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Esses níveis de recursos mínimos são suficientes para teste e experimentação. Para um ambiente com um volume grande de transações, é importante alocar uma quantia suficientemente grande de armazenamento; 250 GB para o console do {{site.data.keyword.blockchainfull_notm}} Platform como um exemplo. A quantidade de armazenamento a ser usada depende do número de transações e do número de assinaturas que são necessárias de sua rede. Se você está prestes a esgotar o armazenamento em seu peer ou solicitador, deve-se implementar um novo console com um sistema de arquivos maior e importar seus componentes de rede novamente de forma adequada.

## Armazenamento
{: #ibp-console-deploy-icp-resources-required-storage}

É necessário determinar o armazenamento que seu console usará. Se você usar as configurações padrão, o gráfico do Helm criará uma nova Solicitação de Volume Persistente (PVC) 8 Gi com o nome de `console-pvc` para os dados do console e outro PVC 8 Gi com o nome de `couchdb-pvc` para o CouchDB.

Se você não deseja usar as configurações de armazenamento padrão, assegure-se de que uma *nova* `storageClass` seja configurada durante a instalação do {{site.data.keyword.cloud_notm}} Private ou o administrador do sistema do Kubernetes precisará criar uma storageClass antes da implementação.

É possível escolher implementar o console nas plataformas AMD64 ou S390X. No entanto, esteja ciente de que o [Fornecimento dinâmico](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/) está disponível somente para nós AMD64 no {{site.data.keyword.cloud_notm}} Private. Se o cluster incluir uma combinação de nós do trabalhador S390X e AMD64, o fornecimento dinâmico não poderá ser usado.

Se você não usar o fornecimento dinâmico, [Volumes Persistentes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) deverão ser criados e configurados com rótulos que possam ser usados para refinar o processo de ligação de PVC do Kubernetes.

## Pré-requisitos
{: #ibp-console-icp-prereq}

Antes de implementar o console do {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.cloud_notm}} Private, assegure-se de concluir as etapas nos tutoriais de [Configurando o {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup).

Também é necessário instalar ou configurar as dependências a seguir antes de implementar o console do {{site.data.keyword.blockchainfull_notm}} Platform.

- [Registre-se com o IBM App ID Service](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id)
- [Instale o Docker](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-docker)

### Registrando com o serviço App ID
{: #ibp-console-icp-prereq-app-id}

O [App ID ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/docs/services/appid/index.html#gettingstarted "App ID") é um serviço de gerenciamento de usuários fornecido pelo {{site.data.keyword.cloud_notm}}. O console do {{site.data.keyword.blockchainfull_notm}} Platform usa o serviço App ID para gerenciar quem será capaz de usar o painel e operar sua rede de blockchain. Isso permite que os usuários da IU usem credenciais emitidas por sua própria organização ou por terceiros, como o Google ou o Facebook, sem que eles precisem ter IBMids. Somente o usuário que implementa o console precisará de um {{site.data.keyword.IBM_notm}}id para se registrar com o serviço.

Antes de implementar o console, é necessário obter um IBMid para executar um registro único com o Diretório da nuvem do App ID. Em seguida, é necessário buscar as credenciais de serviço do APP ID para passar para o console. Use as etapas a seguir para se registrar com o serviço e obter suas credenciais de serviço.

1. Se você ainda não tiver um, será necessário criar um [{{site.data.keyword.IBM_notm}}id ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/account/reg/us-en/signup?formid=urx-19776&target=https%3A%2F%2Fidaas.iam.ibm.com%2Fidaas%2Foidc%2Fendpoint%2Fdefault%2Fauthorize%3Fresponse_type%3Dcode%26client_id%3Dmyibmlondonprod%26state%3DbfAvZHoYtGHytcifRjeE%26redirect_uri%3Dhttps%3A%2F%2Fmyibm.ibm.com%2Fmymga%2Foidcclient%2Fredirect%2Famapp-runtime-BlueIDProd%26scope%3Dopenid).

2. Use o seu {{site.data.keyword.IBM_notm}}id para efetuar login ou se inscrever para o [{{site.data.keyword.cloud_notm}}![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/app-id "IBM Cloud App ID"). Em seguida, acesse o serviço [App ID ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/app-id "IBM Cloud App ID") no Catálogo. Selecione o plano `Lite` ou o `Graduated tier` dependendo de suas necessidades de serviço. Em seguida, clique no botão **Criar** para ativar a tela "Bem-vindo".

3. Quando você estiver na tela "Bem-vindo", clique no link **Gerenciar** no painel de navegação à esquerda para visualizar ou atualizar os provedores de identidade preferenciais. Eles podem ser provedores de terceiros, como o Google, ou o próprio serviço de identidade das organizações.

4. Clique na guia **Configurações** na tabela para configurar uma URL de redirecionamento. Essa será a URL na qual você implementará e acessará o console do {{site.data.keyword.blockchainfull_notm}} Platform. Para usar o host local, especifique `http://localhost/auth/cb` e clique no sinal `+`.

5. Inclua seu endereço de e-mail no serviço para que seja possível efetuar login quando você configurar o console pela primeira vez. Clique no link **Usuários** no painel de navegação à esquerda e clique no botão **Incluir usuário**. Insira as suas informações de e-mail e clique em **Salvar**.

6. Clique no link **Credenciais de serviço** no painel de navegação à esquerda. Se essa for a primeira vez que você usa o serviço, será necessário gerar credenciais clicando em **Nova credencial**. Clique em **Visualizar credenciais** para copiar as credenciais que são necessárias para acessar o serviço. Quando os detalhes para criar novas credenciais aparecerem, selecione a opção `reader` e um ID do serviço se você desejar designar à IU um nome específico. As credenciais do App ID são semelhantes aos valores JSON conforme a seguir:
![Credenciais de serviço](../images/service_credentials.gif "Credenciais de serviço")

7. Clique no ícone Copiar e salve suas credenciais. Você as usará quando configurar o console.

### Instalando o Docker
{: #ibp-console-icp-prereq-docker}

Instale o [Docker ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.docker.com/get-started) versão 17.06.2-ce ou superior no ambiente no qual você implementará o console do {{site.data.keyword.blockchainfull_notm}} Platform. É possível verificar a versão do Docker que você instalou com o comando a seguir por meio de uma janela do terminal:

```
docker -- version
```
{:codeblock}

**Nota:** a instalação do Docker for Mac ou Windows ou do Docker Toolbox também instalará o Docker Compose. Se você já tinha o Docker instalado, verifique se tem o Docker Compose versão 1.14.0 ou superior instalado. Se não, recomendamos que instale uma versão mais recente do Docker. É possível verificar a versão do Docker Compose que você instalou com o comando a seguir por meio de uma janela do terminal:

```
docker-compose -- version
```
{:codeblock}


## Etapa um: fazer o download do pacote do console do {{site.data.keyword.blockchainfull_notm}} Platform por meio do {{site.data.keyword.IBM_notm}} Passport Advantage
{: #ibp-console-icp-download-icp}

Entre em contato com o seu vendedor {{site.data.keyword.IBM_notm}} para fazer download do pacote do console do {{site.data.keyword.blockchainfull_notm}} Platform por meio do [Passport Advantage ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/software/passportadvantage/pao_customer.html "Passport Advantage Online para clientes"). Não tem um vendedor IBM? Visite este <link> para registrar e obter um...

## Etapa dois: importar o gráfico do Helm do console do {{site.data.keyword.blockchainfull_notm}} Platform para o {{site.data.keyword.cloud_notm}} Private
{: #ibp-console-icp-import-helmchart}

Para referência, veja [Instalando o {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

## Etapa três: implementar o console do {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.cloud_notm}} Private
{: #ibp-console-deploy-helmchart-icp}

Depois de importar o gráfico do Helm do console do {{site.data.keyword.blockchainfull_notm}} Platform, é possível usar as etapas a seguir para configurar e instalar o console.

1. Efetue login no console do {{site.data.keyword.cloud_notm}} Private e clique no link **Catálogo** no canto superior direito.
2. Clique em `Blockchain` no painel de navegação à esquerda para localizar o tile rotulado `ibm-blockchain-platform-ui`. Clique no tile para abri-lo e será possível ver um arquivo Leia-me que inclui informações sobre como configurar e instalar o gráfico do Helm.
3. Clique na guia **Configuração** na parte superior do painel ou clique no botão **Configurar** no canto inferior direito.
4. Conclua as  [ definições de configuração ](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-parameters).
5. Clique em **Instalar**.

### Parâmetros do console do {{site.data.keyword.blockchainfull_notm}}  Platform
{: #ibp-console-icp-parameters}

A tabela a seguir lista os parâmetros configuráveis do console do {{site.data.keyword.blockchainfull_notm}} Platform e seus valores padrão.

|  Parâmetro     | Descrição    | Padrão  | Requerido |
| --------------|-----------------|-------|------- |
| `Helm release name`| O nome de sua liberação do Helm. Deve iniciar com uma letra minúscula e terminar com qualquer caractere alfanumérico, deve conter apenas hifens e caracteres alfanuméricos minúsculos. Deve-se usar um nome exclusivo da liberação do Helm toda vez que você tenta instalar um console. | nenhum | sim |
| ` Namespace de destino `| Escolha o namespace do Kubernetes para instalar o gráfico do Helm. | nenhum | sim |
| ` Repositório de imagem `| Local do gráfico do Helm do console do {{site.data.keyword.blockchainfull_notm}} Platform. | registry.ng.bluemix.net/op-tools/op-tools | sim |
| ` Tag de imagem `| Valor da tag que está associada à imagem do console do {{site.data.keyword.blockchainfull_notm}} Platform. Esse campo é preenchido automaticamente para a versão de imagem. Não mude isso. | v0.0.34 | sim |
| `Service type` | Usado para especificar se [portas externas devem ser expostas ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) no peer. Selecione NodePort para expor as portas externamente (recomendado) e ClusterIP para não expor as portas. O LoadBalancer e o ExternalName não são suportados nesta liberação. | NodePort | sim |
| `Console ingress` | Especifique o nome do host que você deseja usar para acessar o console do {{site.data.keyword.blockchainfull_notm}} Platform. | nenhum | não |
| `Configtxlator ingress` | Especifique o nome do host que você deseja usar para acessar o serviço configtxlator. | nenhum | não |
| `Persistence enabled` | Se marcado, os dados estarão disponíveis quando o contêiner for reiniciado. Caso contrário, todos os dados serão perdidos no caso de um failover ou reinicialização de pod. | marcado | não |
| `Use dynamic provisioning` | Marque para ativar o fornecimento dinâmico para volumes de armazenamento. | marcado | não |
| ` Nome da classe de armazenamento `| Especifique um nome de classe de armazenamento exclusivo. Caso contrário, a classe de armazenamento padrão no cluster será usada. | nenhum | Depende de como o cluster do {{site.data.keyword.cloud_notm}} Private está configurado. Verifique com seu administrador de cluster. |
| ` solicitação de volume existente `| Especifique o nome de uma Solicitação de volume existente e deixe todos os outros campos em branco. | nenhum | não |
| ` Rótulo do seletor `| [Rótulo do seletor ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Rótulos e seletores") para seu PVC. | nenhum | não |
| ` Valor do Seletor `| [Valor do seletor ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Rótulos e seletores") para seu PVC. | nenhum | não |
| ` Modo de acesso ao armazenamento `| Especifique o armazenamento [modo de acesso](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modos de Acesso") para o PVC. | ReadWriteMany | sim |
| ` Nome do Volume `|Especifique o nome do PVC. | nenhum | sim |
| `CPU request` | Número mínimo de CPUs a serem alocadas para o console. | 250m | sim |
| `CPU limit` | Número máximo de CPUs a serem alocadas para o console.| 500 m | sim |
| `Memory request` | Quantia mínima de memória a ser alocada para o console. | 256 Mi | sim |
| `Memory limit` | Quantia máxima de memória a ser alocada para o console. | 1 Gi | sim |

## Etapa quatro: Configurar IP de Proxy
{: #ibp-console-icp-config-proxy-ip}

Após a conclusão da instalação, clique no botão **Visualizar liberação do Helm** para abrir a página de liberação do Helm de seu console. Também é possível clicar em **Menu** > **Cargas de trabalho** > **Liberações do Helm** no {{site.data.keyword.cloud_notm}} Private e clicar em sua liberação do Helm.

Na página de liberação do Helm de seu console, localize o IP do Proxy do cluster do {{site.data.keyword.cloud_notm}} Private, por exemplo, `9.12.19.115`.

Na máquina do cliente em que você deseja acessar o console, inclua entradas em `/etc/hosts` para mapear os nomes de host que você inseriu na etapa anterior para os serviços optools e configtxlator para o IP do Proxy. Por exemplo:
```
9.12.19.115     ibp-optools.ibm.com
9.12.19.115     ibp-configtxlator.ibm.com
```

## Etapa cinco: Configurar o console do  {{site.data.keyword.blockchainfull_notm}}  Platform
{: #ibp-console-icp-setup}

*Esta seção se beneficiaria de uma animação gif.*

1. No {{site.data.keyword.cloud_notm}}, acesse **Provedores de identidade** > **Gerenciar** da navegação esquerda e selecione a guia **Configurações de autenticação**.
2. No campo **Incluir URLs de redirecionamento da web**, especifique a URL que você deseja usar para acessar o console com seu App ID.
3. Em seu navegador da web, acesse o console com o nome do host que você configurou no {{site.data.keyword.cloud_notm}} Private. Para o primeiro login, você verá os painéis de configuração do console.
  1. Na guia **Autenticação**, insira o seu App ID.
  2. Na guia **Configuração**, cole as credenciais de serviço do App ID que você copiou em [Etapa um: registrar com o serviço App ID](/docs/services/blockchain/howto/ibp-console-deploy-icp.html#ibp-console-icp-prereq-app-id).
  3. Na guia **Incluir usuários**, especifique uma lista de endereços de e-mail de usuários `Admin` e `General` que estão autorizados para o console. Os domínios de e-mail são restritos ao conjunto de provedores de identidade, como corporativo, Google ou Facebook, que foram selecionados quando o serviço App ID foi registrado no {{site.data.keyword.cloud_notm}}.
    - A função `Admin` é necessária para ser capaz de incluir novos usuários ou remover usuários existentes da lista de autorização do console. **Dica:** se você será a pessoa que administrará o console, lembre-se de incluir seu próprio endereço de e-mail na lista.
    - A função `General` permite aos usuários o acesso **somente visualização** para os componentes no console.

    Finalmente, é necessário especificar um único `Admin contact email` que é exibido como um endereço de e-mail de contato quando um usuário não autorizado, um que não está em nenhuma das listas acima, tenta acessar o console. Esse endereço de e-mail não precisa ser um usuário administrativo no console. Observe que essas informações também podem ser criadas ou modificadas posteriormente na guia **Membros** no console.
  4. Clique em  ** Continuar para efetuar login **. Todos os usuários que são especificados nas listas acima podem agora efetuar login no console do {{site.data.keyword.blockchainfull_notm}} Platform usando seus endereços de e-mail.
