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


# Administrando seu console
{: #ibp-console-manage-console}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

Há várias ações que podem ser tomadas para gerenciar o comportamento do console. Este tópico descreve ações fora das operações do nó de blockchain que auxiliam no uso diário do console.
{:shortdesc}

## Incluir e remover usuários do console

Este console do {{site.data.keyword.blockchainfull}} Platform é provisionado com o endereço de e-mail do proprietário {{site.data.keyword.IBM_notm}} como um administrador do console. O administrador pode, então, conceder a outros usuários o acesso ao console.

Clique em **Usuários** na navegação à esquerda para conceder acesso aos novos usuários para o console por meio de seus endereços de e-mail. Clique em **Incluir novos usuários** e especifique uma lista de endereços de e-mail que você deseja autorizar junto com seu tipo de usuário: `Admin` ou `General`. Os usuários com o tipo `Admin` podem incluir novos usuários ou excluir usuários existentes e visualizar logs do console. Os usuários `general` podem efetuar login no console, mas não podem incluir ou remover usuários ou visualizar os logs.


Os usuários listados neste painel são simplesmente endereços de e-mail de usuários que podem efetuar login no console. Eles não têm nenhum relacionamento com as **Organizações disponíveis** na guia Organizações ou com as identidades armazenadas pela carteira eletrônica do console.
{:note}

## Atualizar o endereço de e-mail do administrador

Se o console é usado por múltiplas pessoas ou organizações, é recomendado criar um endereço de e-mail funcional para acessar sua rede. O uso de um e-mail funcional permitirá acessar o console se o administrador original deixar sua organização ou tiver seu endereço de e-mail suspenso. Se não for possível criar um e-mail funcional, será possível fornecer acesso `Admin` a múltiplos usuários para evitar uma dependência em um único indivíduo.

Para atualizar o endereço de e-mail do administrador do console que foi configurado quando o console foi implementado, deve-se estar com login efetuado como um administrador do console. Navegue para a guia **Usuários** e clique em **Configurar** no tile **IBMid**. No painel que é aberto, especifique um novo endereço de e-mail para o administrador do console.


## Visualizando os logs do console

Será possível acessar facilmente os logs do console se você precisar depurar problemas que encontrar ao usar o console ou operar seus nós. Também será possível aumentar ou diminuir a quantia de logs coletados pelo console, configurando o nível de criação de log. Os logs do console são coletados separadamente dos [logs de seus nós](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs), que são coletados pelo serviço {{site.data.keyword.IBM_notm}} Kubernetes.

Navegue para a guia **Configurações** no navegador do console para mudar as configurações de criação de log. Os logs do console são coletados de duas origens separadas:

  * **Criação de log do cliente:** esses logs são coletados quando os comandos são enviados de seu navegador para o console.
  * **Criação de log do servidor:** esses logs são coletados quando o console envia comandos para os seus nós e por meio da implementação do console. Esses logs incluem a saída de criação de log do Hyperledger Fabric.

Configure a quantia de logs coletados usando a lista suspensa sob cada tipo de log. Por exemplo, **Erro** e **Aviso** coletam a menor quantia de logs, enquanto **Depuração** e **Todos** coletam o máximo.

Será possível visualizar os logs do console somente se você estiver com login efetuado como um administrador do console. Para visualizar os logs na guia **Configurações**, substitua a palavra `settings` na URL do navegador por `api/v1/logs`. Por exemplo, se a URL do console for `localhost:3001/settings`, será possível visualizar os logs navegando para `localhost:3001/api/v1/logs`. Os logs do cliente e do servidor são coletados em arquivos separados. Os logs mais recentes estão na parte superior da página.


## Visualizando os logs do nó
{: #ibp-console-manage-console-node-logs}

Os logs de seus peers, solicitadores e Autoridades de certificação são coletados pelo serviço {{site.data.keyword.IBM_notm}} Kubernetes. Use as etapas abaixo para visualizar os logs de seus nós por meio do cluster no qual você implementou a sua rede do {{site.data.keyword.blockchainfull_notm}} Platform 2.0.

1. Abra o [Painel do {{site.data.keyword.cloud_notm}} ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/dashboard/apps/) e navegue para a tela de visão geral de seu cluster do serviço {{site.data.keyword.IBM_notm}} Kubernetes.
2. Acima da tela de visão geral do cluster, clique em **Painel do Kubernetes**.
3. Dentro do painel do Kubernetes, use a lista suspensa Namespace da navegação à esquerda para mudar o namespace para a instância de serviço do {{site.data.keyword.blockchainfull_notm}} Platform. O nome da instância de serviço será uma sequência longa de letras e números. É possível localizar o nome da instância de serviço no início da URL de seu console do {{site.data.keyword.blockchainfull_notm}} Platform.
4. Na navegação à esquerda, clique em **Pods** para visualizar a lista de pods de nó que você implementou.
5. Clique em um pod. Em seguida, clique em **Visualizar logs** no menu superior para abrir os logs de seu nó. Acima dos logs, é possível usar o menu suspenso após **Logs de** para visualizar os logs dos diferentes contêineres dentro do pod. Por exemplo, seu peer e o banco de dados de estado (CouchDB, por exemplo) são executados em contêineres diferentes e geram logs diferentes.

Por padrão, os logs de seus nós são coletados localmente dentro de seu cluster. Também é possível usar o serviço {{site.data.keyword.cloud_notm}} Log Analysis ou um serviço de terceiro para coletar, armazenar e analisar os logs de sua rede. Para obter informações, veja [Criação de log e monitoramento para o serviço {{site.data.keyword.IBM_notm}} Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.cloud.ibm.com/docs/containers?topic=containers-health#health "Criação de log e monitoramento para o serviço {{site.data.keyword.IBM_notm}} Kubernetes").


## Monitorando o uso de

Quando os nós do {{site.data.keyword.cloud_notm}} Platform são implementados, eles são pré-configurados com as configurações de CPU, de memória e de armazenamento padrão e esses valores não podem ser modificados. É possível monitorar seu uso de recurso usando o painel {{site.data.keyword.IBM_notm}} Kubernetes Service. Se você tentar criar um novo nó no console do {{site.data.keyword.cloud_notm}} Platform e obtiver um erro de que precisa expandir o cluster do Kubernetes, será possível incluir mais armazenamento no cluster do Kubernetes. Veja estas [informações ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/docs/containers/cs_storage_file.html#change_storage_configuration "Mudando o tamanho e o IOPS de seu dispositivo de armazenamento existente") sobre como aumentar a capacidade de armazenamento ou o desempenho de seu volume existente.
