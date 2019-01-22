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
{:tip: .tip}

# Desenvolvendo redes de negócios
{: #develop-the-network}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Desenvolver soluções de blockchain usando o {{site.data.keyword.blockchainfull}} Platform alavanca o conjunto de ferramentas de desenvolvimento de software livre do Hyperledger Composer. O Hyperledger Composer usa uma linguagem de modelagem personalizada, que é combinada com transações JavaScript e regras de controle de acesso para modelar uma rede de negócios blockchain por completo _antes_ de implementar qualquer coisa em uma rede de blockchain real.
{:shortdesc}

**Nota:** o Starter Plan fornece aplicativos de amostra que você pode aprender e experimentar. É possível executar aplicativos de amostra no Starter Plan antes de você desenvolver a sua rede de negócios customizada. Para obter mais informações, veja [Sobre o Starter Plan](starter_plan.html).

Este tutorial passa pelo desenvolvimento de uma solução de {{site.data.keyword.blockchain}} que começa com a criação e a modelagem de uma rede de negócios e, em seguida, implementa-a em uma rede de blockchain em execução.

As etapas a seguir descrevem o desenvolvimento básico de uma solução do {{site.data.keyword.blockchain}} que é hospedada no {{site.data.keyword.blockchainfull_notm}} Platform:

1. Crie a estrutura de dados para conter a definição de rede de negócios. A rede de negócios é definida em termos de ativos, participantes e transações usando a [Linguagem de modelagem](https://hyperledger.github.io/composer/latest/reference/cto_language).

2. Modele a rede de negócios definindo as classes de ativos, as classes de participante, as classes de transação, as classes de evento e as regras de controle de acesso que serão usadas após a implementação.

3. Empacote a rede de negócios definida em um único arquivo implementável.

4. Implemente a rede de negócios empacotada no {{site.data.keyword.blockchainfull_notm}} Platform. Assim que a rede de negócios for implementada, um servidor REST poderá ser gerado para permitir a integração com aplicativos externos.

## Antes de iniciar

Assegure-se de que você tenha instalado as [ferramentas de desenvolvimento do {{site.data.keyword.blockchainfull_notm}}: Develop](./develop_install.html).

## Etapa Um: Criando a estrutura de rede de negócios

Um conceito chave para desenvolver soluções do {{site.data.keyword.blockchain}} é a **definição de rede de negócios (BND)**. O BND define o modelo de dados, a lógica de transação e as regras de controle de acesso para a sua solução de blockchain. Para criar um BND, é necessário criar uma estrutura de projeto adequada no disco.

A maneira mais fácil para a introdução é usar o gerador do Yeoman para criar uma rede de negócios de estrutura básica. O gerador do Yeoman cria um diretório que contém todos os componentes de uma rede de negócios, que podem ser modificados para o seu caso de uso específico.

1. Crie um novo diretório para o seu projeto e navegue até ele na linha de comandos.

2. No seu novo diretório, use o Yeoman para criar uma rede de negócios de estrutura básica. O comando a seguir requer um nome de rede de negócios, uma descrição, um nome do autor, um endereço de e-mail do autor, uma seleção de licença e um namespace:
    ```
    yo hyperledger-composer:businessnetwork
    ```
    {:codeblock}

3. Insira `tutorial-network` para o nome da rede e as informações desejadas para descrição, nome do autor e e-mail do autor.

4. Selecione `Apache-2.0` como a licença.

5. Selecione `org.acme.biznet` como o namespace.

## Etapa dois: Definindo a rede de negócios

Uma rede de negócios é composta de ativos, participantes, transações, regras de controle de acesso e, opcionalmente, eventos e consultas. A rede de negócios de estrutura básica que é criada na etapa um inclui um arquivo de modelo (`.cto`), que contém as definições de classe para todos os ativos, os participantes e as transações na rede de negócios. A rede de negócios de estrutura básica também contém um documento de controle de acesso (`permissions.acl`) com regras de controle de acesso básico, um arquivo de script (`logic.js`) que contém funções do processador de transação e um arquivo de pacote (`package.json`) que contém metadados de rede de negócios.

### Modelando ativos, participantes e transações

O primeiro documento para atualização é o arquivo de modelo (`.cto`). O arquivo de modelo é gravado usando a [Linguagem de modelagem Hyperledger Composer](https://hyperledger.github.io/composer/latest/reference/cto_language). O arquivo de modelo contém as definições de cada classe de ativo, transação, participante e evento. Ele implicitamente estende o modelo do sistema que é descrito na documentação de linguagem de modelagem.

1. Abra o arquivo de modelo `org.acme.biznet.cto` em um editor de sua escolha. Ele está localizado na pasta `models` da rede de negócios que você criou na última etapa.

2. Substitua os conteúdos pelas informações a seguir:

    ```
        /**
         * Minha rede de negócios de mercadoria
         */
        namespace org.acme.biznet
        Mercadoria de ativo identificada por tradingSymbol {
            o String tradingSymbol
            o String description
            o String mainExchange
            o Double quantity
            --> Proprietário do operador
        }
        Operador participante identificado por tradeId {
            o String tradeId
            o String firstName
            o String lastName
        }
        Comércio de transação {
            --> mercadoria de Commodity
            --> newOwner do operador
        }
    ```
    {:codeblock}

3. Salve as suas mudanças no arquivo `org.acme.biznet.cto`.


### Incluindo a lógica de transação do JavaScript

No arquivo de modelo, uma transação `Trade` foi definida, que especifica um relacionamento com um ativo e um participante. O arquivo de função do processador de transação contém a lógica do JavaScript para executar as transações que estiverem definidas no arquivo de modelo.

A transação `Trade` destina-se a simplesmente aceitar o identificador do ativo `Commodity` que estiver sendo negociado e o identificador do participante `Trader` para configurar como o novo proprietário.

1. Abra o arquivo de script `logic.js` no diretório `lib`.

2. Substitua os conteúdos pelas informações a seguir:

    ```
        /**
         * Rastreie o comércio de uma mercadoria de um operador para outro
         * @param {org.acme.biznet.Trade} trade - o comércio a ser processado
         * @transaction
         */
        function tradeCommodity(trade) {
            trade.commodity.owner = trade.newOwner;
            return getAssetRegistry('org.acme.biznet.Commodity')
                .then(function (assetRegistry) {
                    return assetRegistry.update(trade.commodity);
                });
        }
    ```
    {:codeblock}

3. Salve as suas mudanças em `logic.js`.

### Incluindo o controle de acesso

O controle de acesso é uma parte de chave de uma rede de negócios. Todas as redes de negócios devem ter um arquivo de controle de acesso que é chamado `permissions.acl`. Sempre que qualquer processo for chamado em uma rede de negócios implementada, a lista de controle de acesso será verificada para assegurar que a identidade de chamada ou o participante pode chamar a operação.

Para os propósitos deste tutorial, configure uma ACL simples. Observe que isso não deve ser implementado em um ambiente de produção.

1. Crie um arquivo `permissions.acl` no diretório `tutorial-network`.

2. Inclua as regras de controle de acesso a seguir em `permissions.acl`:

    ```
        /**
         * Regras de controle de acesso para rede de tutorial
         */
        rule Default {
            descrição: "Permitir o acesso de todos os participantes a todos os recursos"
            participante: "ANY"
            operação: ALL
            recurso: "org.acme.biznet.*"
            ação: ALLOW }

        rule SystemACL {
          description:  "System ACL to permit all access"
          participant: "ANY"
          operation: ALL
          resource: "org.hyperledger.composer.system.**"
          ação: ALLOW }
    ```
    {:codeblock}

3. Salve as suas mudanças em `permissions.acl`.

## Etapa três: Empacotando a rede de negócios

Após a rede de negócios ser definida, ela deverá ser empacotada em um archive de rede de negócios implementável (`.bna`).

1. Usando a linha de comandos, navegue para o diretório `tutorial-network`.

2. No diretório `tutorial-network`, execute o comando a seguir:

    ```
    composer archive create -t dir -n .
    ```
    {:codeblock}

Após a execução do comando, um archive de rede de negócios (`tutorial-network@0.0.1.bna`) será criado no diretório `tutorial-network`.

## Etapa quatro: Implementando a rede de negócios e gerando um servidor REST

Implemente a rede de negócios no {{site.data.keyword.blockchainfull_notm}} Platform usando o [Starter Plan](./develop_starter.html) ou o [Enterprise Plan](./develop_enterprise.html).

Após a rede de negócios ser implementada no {{site.data.keyword.blockchainfull_notm}} Platform, um servidor REST personalizado poderá ser gerado. O servidor REST examina o conteúdo de negócios de rede e constrói chamadas API RESTful para os ativos, transações e participantes da rede. As instruções para gerar um servidor REST são incluídas na [Documentação do Hyperledger Composer](https://hyperledger.github.io/composer/latest/integrating/getting-started-rest-api).
