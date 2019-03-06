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

# Importando nós
{: #ibp-console-import-nodes}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

O console inclui a opção para importar nós que são criados usando outro console do {{site.data.keyword.blockchainfull}} Platform.
{:shortdesc}  

## Por que importar um nó?

Importe os nós de CA, solicitador e peer de outros consoles do {{site.data.keyword.blockchainfull_notm}} Platform quando você precisar operá-los junto com nós que já existem no seu console. Por exemplo, é possível usar a opção de peer de importação quando você deseja associar peers de organizações fora de seu console a canais em seu console. Como os serviços do {{site.data.keyword.blockchainfull_notm}} Platform 2.0 são implementados em múltiplos ambientes do {{site.data.keyword.cloud_notm}}, é provável que algumas instâncias de serviço incluam somente CAs e peers, enquanto outras hospedem os serviços de pedido. A importação dos nós distintos para o console fornece uma maneira de visualizar e operar esses componentes distribuídos por meio de uma única interface com o usuário para que eles possam transacionar juntos no blockchain.

Após a importação dos nós no console, um conjunto robusto de capacidade operacional se torna disponível, por exemplo:
- Incluir novas organizações no consórcio
- Criar novos canais
- Associar peers aos canais
- Instalar contratos inteligentes em peers, independentemente de onde eles foram implementados
- Instanciar contratos inteligentes em canais
- Fazer upgrade de contratos inteligentes em canais

Ao importar um componente, é necessário fornecer suas informações de conexão. Ao importar um componente, você não importa o próprio componente físico; em vez disso, ele usa as informações de conexão para construir uma representação do componente no console para que ele possa ser operado por meio do console.

 Após a importação de um nó para o console, também é possível modificar suas informações de conexão usando a guia **Configurações** do nó.

{: note}
Antes de poder importar os nós para o console, eles precisam ser exportados do console do {{site.data.keyword.blockchainfull_notm}} Platform no qual eles foram criados. Basta exportar as informações do nó do console do console para um arquivo `JSON` e importar o arquivo gerado usando o botão `Upload JSON` na etapa do painel lateral do nó de importação do console.


## Iniciar aqui: Reunindo Certificados ou Credenciais

Antes de poder importar um componente de blockchain existente de outra instância de serviço do {{site.data.keyword.blockchainfull_notm}} Platform 2.0, deve-se importar a identidade de administrador do componente para a carteira eletrônica do console. A importação da identidade para a carteira eletrônica do console aperfeiçoa a operação do nó. O operador de rede que criou o nó precisa fornecer essas credenciais para você.

## Importando uma CA
{: #ibp-console-import-ca}

Um nó de CA é o componente de blockchain que emite certificados para todas as entidades de rede (peers, solicitadores, clientes e assim por diante) para que essas entidades possam se comunicar, autenticar e, finalmente, transacionar. Cada organização tem a sua própria CA que age como a sua raiz de confiança. Será necessário incluir suas organizações se você estiver se associando ou construindo um consórcio de blockchain. É possível saber mais sobre CAs na [visão geral de componentes de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-ca).  

Há várias razões pelas quais você pode desejar importar uma CA para seu console. Após a importação da CA, é possível usá-la para incluir mais peers na organização do mesmo nível clicando em **Registrar usuário**. Ou, é possível usar a CA para criar identidades de administrador adicionais para um peer ou um solicitador.

Para importar uma CA para o console do {{site.data.keyword.blockchainfull_notm}} Platform e operá-la, deve-se já ter exportado a CA de outro {{site.data.keyword.blockchainfull_notm}} Platform. A importação de uma CA permite registrar novos usuários e [inscrever identidades](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll).

### Antes de iniciar

- Assegure-se de que você já tenha importado o arquivo JSON de identidade de administrador da CA para sua carteira eletrônica do console.
- Assegure-se de que o arquivo JSON de CA que foi exportado do console no qual ele foi criado esteja disponível.

### Como Importar uma CA  

A importação de uma CA é executada na guia **Nós**. Clique no sinal `+` em **Incluir autoridade de certificação** na seção Autoridades de certificação, seguido por **Importar uma CA existente**. Siga as etapas no painel lateral que se abre para especificar as informações de conexão.

- Clique no botão **Fazer upload de JSON** para ignorar a entrada manual das informações e procurar o arquivo JSON que foi exportado do console no qual a CA foi criada.
- Configure a identidade de administrador para a CA clicando em **Identidade existente** e selecionando a identidade de administrador de CA em sua carteira eletrônica do console.

Após a importação da CA para o console, é possível usar sua CA para criar novas identidades e gerar os certificados necessários para operar seus componentes e enviar transações para a rede. Para saber mais, veja [Gerenciando autoridades de certificação](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-manage-ca).

### Importando identidades de administrador para a carteira eletrônica do console

Cada componente do {{site.data.keyword.blockchainfull_notm}} Platform é implementado com a sua própria chave pública, o certificado de assinatura, de um administrador de componente dentro. Quando as ações são executadas com relação ao componente pelo administrador, essa chave pública é anexada à transação e validada com relação à configuração do componente. As chaves permitem que o administrador opere seus componentes, como registrar novos usuários, criar um novo canal ou instalar contratos inteligentes em peers. Se você deseja usar o console para operar um solicitador ou peer que foi criado usando outro console, é necessário fazer upload da identidade de administrador associada para a carteira eletrônica do console. Em seguida, é possível associar o nó importado à identidade na carteira eletrônica do console. Essas identidades devem ser exportadas do console no qual elas foram criadas e são necessárias quando o nó é importado.

Para importar uma nova identidade, abra a guia **Carteira eletrônica** e clique em **Incluir identidade**. Clique em **Fazer upload de JSON** para procurar o arquivo de identidade JSON que foi exportado pelo operador de rede de onde o nó foi criado.

Depois de concluir o painel **Incluir identidade** e clicar em enviar, será possível visualizar a nova identidade de administrador na tela de visão geral da carteira eletrônica. Agora é possível referir-se a essas identidades ao importar componentes de CA, peer ou solicitador.

## Importando um Orderer
{: #ibp-console-import-orderer}

Um nó de solicitador é o componente de blockchain que coleta transações de membros de rede, pede as transações e as empacota em blocos. O serviço de pedido, que é composto por uma coleção de solicitadores, é a ligação comum de consórcios de blockchain e precisará ser implementado se você estiver fundando um consórcio ao qual as outras organizações se associarão. É possível aprender mais sobre solicitadores na [visão geral de componentes de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-orderer).

A importação de um solicitador no console permite criar novos canais para os peers para transacionar privadamente.

### Antes de iniciar

- Assegure-se de que você já tenha importado o arquivo JSON de identidade de administrador do solicitador para a sua carteira eletrônica do console.
- Assegure-se de que o arquivo JSON do solicitador que foi exportado do console no qual ele foi criado esteja disponível.

### Como Importar um Orderer
A importação de um solicitador é executada na guia **Nós**. Clique no sinal `+` em **Incluir solicitador** na seção Solicitador, seguido por **Importar um solicitador existente**. Siga as etapas no painel lateral que se abre para especificar manualmente as informações de conexão.
- Clique no botão **Fazer upload de JSON** para ignorar a entrada manual das informações e procurar o arquivo JSON que foi exportado do console no qual o solicitador foi criado.
- Configure a identidade de administrador para o solicitador clicando em **Identidade existente** e selecionando a identidade de administrador do solicitador em sua carteira eletrônica do console.

Depois de ter importado o solicitador para o console, é possível incluir novos membros da organização e selecionar o solicitador ao criar novos canais.

## Importando um Período
{: #ibp-console-import-peer}

Um nó de peer é o componente de blockchain que mantém um livro-razão e executa um contrato inteligente para executar operações de consulta e atualização no livro-razão. Os membros da organização possuem e mantêm os peers.  Cada organização que se associa a um consórcio deve implementar pelo menos um peer. É possível aprender mais sobre os peers na [visão geral de componentes de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-peer).  

Após a importação de um peer para o console, é possível instalar contratos inteligentes no peer e associar o peer a outros canais em seu blockchain.

**Nota:** se você precisar incluir mais peers na organização de peer ou criar identidades de administrador adicionais para um peer, será necessário importar a CA do peer e, em seguida, usar essa CA para executar essas operações.

### Antes de iniciar

Antes de poder importar um peer, é necessário reunir as informações e os certificados a seguir:

- Assegure-se de que você já tenha importado o arquivo JSON de identidade de administrador do peer para sua carteira eletrônica do console.
- Assegure-se de que o arquivo JSON do peer que foi exportado do console no qual ele foi criado esteja disponível.

### Como Importar um Período

A importação de um peer é executada por meio da guia **Nós**. Clique no sinal `+` em **Incluir peer** na seção Peers, seguido por **Importar um peer existente**. Siga as etapas no painel lateral que se abre para especificar as informações de conexão.
- Clique no botão **Fazer upload de JSON** para ignorar a entrada manual das informações e procurar o arquivo JSON que foi exportado do console no qual o peer foi criado.
- Configure a identidade de administrador para o peer clicando em **Identidade existente** e selecionando a identidade de administrador de peer em sua carteira eletrônica do console.

Após a importação do peer para o console, é possível instalar contratos inteligentes no peer e associar o peer aos canais em seu blockchain.


<!-- ## Importing an MSP -->
