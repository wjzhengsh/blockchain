---

copyright:
  years: 2019
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


# Administrando seu console
{: #ibp-console-manage-console}

Há diversas ações que podem ser tomadas para gerenciar o comportamento de seu console. Este tópico descreve ações fora das operações do nó de blockchain que auxiliam no uso diário do console.
{:shortdesc}

**Público-alvo:** este tópico é projetado para operadores de rede que são responsáveis por criar, monitorar e gerenciar a rede de blockchain.

## Incluir e remover usuários do console
{: #ibp-console-manage-console-add-remove}

O processo de autorização do console está mudando durante o programa beta. Se tiver uma instância de serviço de blockchain existente que usou a autenticação de ID original do {{site.data.keyword.IBM_notm}}, não será mais possível ver seus usuários listados na guia **Usuários**. Deve-se conceder o acesso ao console usando o processo do IAM descrito a seguir.
{:important}

Cada usuário que acessa o console deve ter uma política de acesso designada com uma função de usuário do {{site.data.keyword.cloud_notm}} Identity and Access Management (IAM) definida. A política determina quais ações o usuário pode executar dentro do console. O console do {{site.data.keyword.blockchainfull}} Platform é provisionado com o endereço de e-mail do proprietário do {{site.data.keyword.cloud_notm}} como o administrador do console.  Por padrão, é fornecida a esse usuário do {{site.data.keyword.cloud_notm}} a função `Manager` para o serviço do Blockchain Platform 2.0 no IAM. Em seguida, será possível que o administrador do console conceda a outros usuários o acesso ao console usando a IU do IAM. Para obter mais informações sobre o IAM, consulte [O que é o IAM ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](/docs/iam?topic=iam-iamoverview#iamoverview "O que é o IAM?").  

Quando você [usa o IAM para convidar usuários ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](/docs/iam?topic=iam-iamuserinv#iamuserinv "Usar o IAM para convidar usuários"), é necessário concluir as etapas a seguir para configurar suas funções e acesso ao console:
 1. Na barra de menus, clique em **Gerenciar** > **Acesso (IAM)** e, em seguida, selecione **Usuários**.
 2. Clique em **Convidar usuário**.
 3. Digite o endereço de e-mail do ou dos usuários.
 4. Na lista suspensa `Services`, selecione `Blockchain Platform 2.0`.
 5. Role para baixo até `Select roles`.
 6. Em `Assign service access roles`, escolha uma função para o usuário, que pode ser `Manager`, `Writer` e `Reader`.
 7. Clique em **Convidar usuários**.

| Função | Recursos |
|--------|----------|
| Gerente | Como um Gerenciador, você tem permissões que vão além da função Gravador. É possível fazer tudo que um Leitor e um Gravador podem fazer, bem como: <ul><li>Incluir novos componentes.</li><li>Excluir componentes provisionados.</li><li>Mudar os níveis de log do console.</li></ul> |
| Gravador | Como um Gravador, você tem permissões que vão além da função Leitor, incluindo: <ul><li>Importar componentes.</li><li>Remover componentes importados.</li><li>Registrar usuários em uma CA.</li></ul>  |
| Leitor | Como um leitor, é possível executar ações somente leitura, incluindo: <ul><li>Visualizar a IU do console.</li><li>Visualizar o log do console.</li><li>Exportar componentes.</li></ul> | |

 As permissões são acumulativas. Se você selecionar uma função `Manager`, o usuário também será capaz de executar todas as ações de `Writer` e `Reader` sem a necessidade de marcar adicionalmente essas funções.   Da mesma forma, um usuário com a função `Writer` será capaz de executar todas as ações da função `Reader`.  Para acesso ao console, somente é necessário selecionar uma função em `Service Access Roles`, sem a necessidade de selecionar algo em `Platform Access Roles`.

![Update Kubernetes version](../images/AddICPUser.gif)

Depois de incluir novos usuários no console, eles poderão não ser capazes de visualizar todos os nós, canais ou chaincode implementados por outros. Para trabalhar com esses componentes, cada usuário precisa importar as identidades associadas para sua própria carteira eletrônica do console. Para obter mais informações, consulte [Armazenando identidades em sua carteira eletrônica do console](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-wallet).
{:important}

Se for necessário modificar a função de um usuário:
 1. Na barra de menus, clique em **Gerenciar** > **Acesso (IAM)** e, em seguida, selecione **Usuários**.
 2. Clique no menu Ações próximo ao usuário que deseja modificar e selecione **Designar acesso**.
 3. Selecione o quadro **Designar acesso aos recursos**.
 4. Na lista suspensa `Services`, selecione `Blockchain Platform 2.0`.
 5. Role para baixo até `Select roles`.
 6. Em `Assign service access roles`, escolha uma função para o usuário, que pode ser `Manager`, `Writer` e `Reader`.
 7. Clique em **Designar**.

Quando precisar remover o acesso de um usuário ao console, siga as instruções no tópico [Removendo usuários do IAM ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](/docs/iam?topic=iam-remove#remove "Removendo usuários").

### Designar funções de acesso a indivíduos ou grupos de usuários no IAM
{: #ibp-console-manage-console-users-groups}

Quando você configura políticas do {{site.data.keyword.cloud_notm}} IAM, é possível designar funções a um usuário individual ou a um grupo de usuários.

<dl>
<dt>Usuários individuais</dt>
<dd>Você pode ter um usuário específico que precise de mais ou menos permissões do que o restante de sua equipe. É possível customizar permissões em uma base individual para que cada pessoa tenha as permissões que elas precisam para concluir suas tarefas. É possível designar mais de uma função do {{site.data.keyword.Bluemix_notm}} IAM a cada usuário.</dd>
<dt>Vários usuários em um grupo de acesso</dt>
<dd>É possível criar um grupo de usuários e, em seguida, designar permissões a esse grupo. Por exemplo, é possível agrupar todos os chefes de equipe e designar acesso de administrador ao grupo. Em seguida, é possível agrupar todos os desenvolvedores e designar somente acesso de gravação a esse grupo. É possível designar mais de uma função do {{site.data.keyword.Bluemix_notm}} IAM para cada grupo de acesso. Quando você designa permissões a um grupo, qualquer usuário que é incluído ou removido desse grupo é afetado. Se você incluir um usuário no grupo, ele também terá o acesso adicional. Se ele for removido, seu acesso será revogado.</dd>
</dl>

Os usuários incluídos no IAM são simplesmente endereços de e-mail de usuários que podem efetuar login no console. Eles não têm nenhum relacionamento com as **Organizações disponíveis** na guia Organizações ou com as identidades armazenadas pela carteira eletrônica do console.
{:note}

## Atualizar o endereço de e-mail do administrador

Se o console for usado por diversas pessoas ou organizações, recomenda-se criar um endereço de e-mail funcional para o acesso a sua rede. O uso de um e-mail funcional permite que você continue acessando o console mesmo quando o administrador original sai de sua organização ou tem seu endereço de e-mail suspenso. Somente um único endereço de e-mail pode ser usado como o contato do administrador.

Efetue login como o administrador do console para atualizar o endereço de e-mail configurado para ele durante a implementação do console. Navegue até a guia **Usuários** e clique em **Configurar** no quadro **{{site.data.keyword.IBM_notm}}id**. No painel que é aberto, especifique um novo endereço de e-mail para o administrador do console.

## Visualizando seus logs
{: #ibp-console-manage-logs}

Quando você usa o console do {{site.data.keyword.blockchainfull_notm}} Platform, pode ser necessário visualizar logs para depurar um problema.

### Visualizando os logs do console
{: #ibp-console-manage-console-logs}

Será possível acessar facilmente os logs do console se você precisar depurar problemas que encontrar ao usar o console ou operar seus nós. Também é possível configurar o nível de criação de log para aumentar ou diminuir a quantidade de logs coletados pelo console. Os logs do console são coletados separadamente dos [logs do nó](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs), que são coletados pelo serviço {{site.data.keyword.cloud_notm}} Kubernetes.

Navegue para a guia **Configurações** no navegador do console para mudar as configurações de criação de log. Os logs do console são coletados de duas origens separadas:

  * **Criação de log do cliente:** esses logs são coletados quando os comandos são enviados de seu navegador para o console.
  * **Criação de log do servidor:** esses logs são coletados quando o console envia comandos para os seus nós e por meio da implementação do console. Esses logs incluem a saída de criação de log do Hyperledger Fabric.

Configure a quantidade de logs coletados usando a lista suspensa em cada tipo de log. Por exemplo, **Erro** e **Aviso** coletam a menor quantia de logs, enquanto **Depuração** e **Todos** coletam o máximo.

É possível visualizar apenas os logs do console ao efetuar login como um administrador do console. Para visualizar os logs na guia **Configurações**, substitua a palavra `settings` na URL do navegador por `api/v1/logs`. Por exemplo, se a URL do console for `localhost:3001/settings`, será possível visualizar os logs navegando para `localhost:3001/api/v1/logs`. Os logs do cliente e do servidor são coletados em arquivos separados. Os logs mais recentes estão na parte superior da página.

### Visualizando os logs do nó
{: #ibp-console-manage-console-node-logs}

Os logs de seus peers, solicitadores e Autoridades de Certificação são coletados pelo serviço {{site.data.keyword.IBM_notm}} Kubernetes. Use as etapas abaixo para visualizar os logs de seus nós por meio do cluster no qual você implementou a sua rede do {{site.data.keyword.blockchainfull_notm}} Platform 2.0.

1. Abra o painel do [{{site.data.keyword.cloud_notm}} ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/resources) e navegue até a tela de visão geral de seu cluster do serviço {{site.data.keyword.IBM_notm}} Kubernetes.
2. Acima da tela de visão geral do cluster, clique em **Painel do Kubernetes**.
3. Dentro do painel do Kubernetes, use a lista suspensa Namespace da navegação à esquerda para mudar o namespace para a instância de serviço do {{site.data.keyword.blockchainfull_notm}} Platform. O nome da instância de serviço será uma sequência longa de letras e números. É possível localizar o nome da instância de serviço no início da URL de seu console do {{site.data.keyword.blockchainfull_notm}} Platform.
4. Na navegação à esquerda, clique em **Pods** para visualizar a lista de pods de nó que você implementou.
5. Clique em um pod. Em seguida, clique em **Visualizar logs** no menu superior para abrir os logs de seu nó. Acima dos logs, é possível usar o menu suspenso subsequente a **Logs de** para visualizar os logs dos diferentes contêineres dentro do pod. Por exemplo, seu peer e o banco de dados de estado (CouchDB, por exemplo) são executados em contêineres diferentes e geram logs diferentes.

Por padrão, os logs de seus nós são coletados localmente dentro de seu cluster. Também é possível usar o serviço {{site.data.keyword.cloud_notm}} Log Analysis ou um serviço de terceiro para coletar, armazenar e analisar os logs de sua rede. Para obter mais informações, consulte [Criação de log e monitoramento para o serviço {{site.data.keyword.IBM_notm}} Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.cloud.ibm.com/docs/containers?topic=containers-health#health "Criação de log e monitoramento para o serviço {{site.data.keyword.IBM_notm}} Kubernetes"). Recomenda-se que você aproveite o serviço [{{site.data.keyword.cloud_notm}} LogDNA ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/docs/services/Log-Analysis-with-LogDNA?topic=LogDNA-kube#kube "Gerenciando logs do cluster Kubernetes com o IBM Log Analysis with LogDNA"), que permite uma análise fácil e em tempo real dos logs.

### Visualizando os logs do contêiner de contrato inteligente
{: #ibp-console-manage-console-container-logs}

Se você encontrar problemas com o seu contrato inteligente, será possível visualizar os logs do contêiner do contrato inteligente, ou chaincode, para depurar um problema:

- Abra seu painel do Kubernetes e clique no pod do peer no qual o contrato inteligente está em execução.
- Clique no link `exec` por meio de seu painel para executar no pod. Por padrão, ele aponta para o contêiner de peer.
- Alterne para o contêiner `dind` selecionando-o na lista suspensa.
- Execute o comando `docker ps -a` para ver a lista de contêineres de chaincode.
- Execute `docker logs <chaincode-container-ID>` substituindo <chaincode-container-ID> pelo ID de seu contêiner de chaincode.

## Expiração de cluster Kubernetes
{: #ibp-console-manage-console-cluster-expiration}

Se estiver usando um cluster gratuito do serviço {{site.data.keyword.cloud_notm}} Kubernetes, ele expirará após 30 dias. Quando isso acontecer, não será mais possível acessar seu console. Todos os nós e certificados associados também serão excluídos. É permitido somente um cluster gratuito por vez. Portanto, antes de ser possível criar outra instância de serviço de blockchain, será necessário certificar-se de que o cluster expirado e sua instância de serviço associada tenham sido excluídos do {{site.data.keyword.cloud_notm}}. Para confirmar se já foram excluídos ou para excluir manualmente esses recursos, siga estas etapas:

1. No painel de seu {{site.data.keyword.cloud_notm}}, clique no menu hambúrguer e abra a **Lista de recursos**.
2. Role até a seta **Serviços** e expanda-a para visualizar sua instância de serviço.
3. Se a sua instância estiver listada, clique no menu Ação para ela e, em seguida, em **Excluir** para excluí-la.
4. Role até a seta **Clusters Kubernetes** e expanda-a para visualizar seu cluster gratuito.
5. Se seu cluster gratuito estiver listado, clique no menu Ação para ele e, em seguida, clique em **Excluir** para excluí-lo.

Quando concluir essas ações, será possível seguir as [etapas originais](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks) para criar uma nova instância de serviço de blockchain e do cluster Kubernetes na página do catálogo do IBM Cloud.
