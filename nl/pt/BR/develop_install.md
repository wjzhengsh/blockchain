---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Instalando um ambiente de desenvolvimento de rede de negócios
{: #installing-a-development-environment}

**A IBM recomenda usar o Hyperledger Composer exclusivamente para demos e provas de conceitos. A IBM não fornece suporte para redes que usam o Hyperledger Composer na produção, incluindo a CLI do Composer, as APIs JavaScript, o servidor REST e o Web Playground.**

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Siga estas instruções para obter as ferramentas de desenvolvimento do {{site.data.keyword.blockchainfull}} Platform: Develop para criar e testar redes de negócios. Para fornecer flexibilidade e ativar o número máximo de cenários de desenvolvimento, teste e implementação, as ferramentas de desenvolvimento são entregues como módulos npm, que são controlados por meio da linha de comandos.

## Antes de iniciar

Assegure-se de instalar os pré-requisitos a seguir:

- Ubuntu Linux 14.04 / 16.04 LTS (ambos de 64 bits) ou Mac OS 10.12
- Node v8.9 ou superior (v9 não é suportado)
- npm v5.x
- git v2.9.x ou superior
- Python v2.7.x
- Um editor de código de sua escolha, como VSCode.

Se você estiver usando o Enterprise Plan, instale os pré-requisitos adicionais a seguir:

- Docker Engine v17.03 ou superior
- Docker-Compose v1.8 ou mais alto

Se você estiver usando VSCode, há uma extensão de VSCode do Hyperledger Composer disponível [aqui](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client).

## Etapa um: Instale as ferramentas de linha de comandos

As ferramentas de linha de comandos incluem **composer-cli**, o módulo de linha de comandos principal do {{site.data.keyword.blockchainfull_notm}} Platform: Develop; **generator-hyperledger-composer**, **composer-rest-server** e **Yeoman**, esses módulos são usados para gerar redes de negócios, servidores REST sob medida e aplicativos Angular. A versão que você instala depende da versão do Hyperledger Fabric que seu plano Starter ou Enterprise está executando. É possível localizar a versão do Fabric abrindo a [janela Preferências de rede](../v10_dashboard.html#network-preferences) em seu Network Monitor.

1. Para uso com novas instâncias do Starter Plan que executam o Hyperledger Fabric v1.2.1, instale o **composer-cli** usando o comando a seguir:

    ```
    npm install -g composer-cli@0.20.x
    ```
    {:codeblock}

  Para uso com instâncias do Enterprise Plan e do Starter Plan que executam o Hyperledger Fabric v1.1, instale o **composer-cli** usando o comando a seguir:

    ```
    npm install -g composer-cli@0.19.x
    ```
    {:codeblock}

2. Para uso com novas instâncias do Starter Plan que executam o Hyperledger Fabric v1.2.1, instale o **composer-rest-server** usando o comando a seguir:

    ```
    npm install -g composer-rest-server@0.20.x
    ```
    {:codeblock}

  Para uso com as instâncias do Enterprise Plan e do Starter Plan que executam o Hyperledger Fabric v1.1, instale o **composer-rest-server** usando o comando a seguir:

    ```
    npm install -g composer-rest-server@0.19.x
    ```
    {:codeblock}

 Use o módulo **composer-rest-server** para criar um Servidor REST em sua máquina para expor as suas redes de negócios como APIs RESTful.

3. Para uso com novas instâncias do Starter Plan que executam o Hyperledger Fabric v1.2.1, instale o **generator-hyperledger-composer** usando o comando a seguir:

    ```
    npm install -g generator-hyperledger-composer@0.20.x
    ```
    {:codeblock}

  Para uso com instâncias do Enterprise Plan e do Enterprise Plan que executam o Hyperledger Fabric v1.1, instale o **generator-hyperledger-composer** usando o comando a seguir:

    ```
    npm install -g generator-hyperledger-composer@0.19.x
    ```
    {:codeblock}

    Use **generator-hyperledger-composer** para gerar as estruturas de rede de negócios de estrutura básica, modelos e um aplicativo Angular.

4. O Yeoman é uma ferramenta para gerar aplicativos, que utiliza `generator-hyperledger-composer`:

    ```
    npm install -g yo
    ```
    {:codeblock}

## Etapa dois: Instale um Playground local

O playground do Hyperledger Composer é uma interface com o usuário que pode ser usada como um ambiente simulado para testar uma rede de negócios. Instale o playground usando o comando a seguir:

```
npm install -g composer-playground@0.20.x
```
{:codeblock}


## Opcional: Configure o seu IDE

É possível usar o playground para desenvolver, editar e testar as redes de negócios. No entanto, se você desejar desenvolver uma rede de negócios em um IDE, haverá uma extensão do VSCode disponível, que incluirá destaque de sintaxe para a linguagem de modelagem do Hyperledger Composer.

1. Instale o VSCode por meio dessa URL: [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. Abra o VSCode, acesse Extensões e, em seguida, procure e instale a extensão do Hyperledger Composer do mercado de trabalho.


### Iniciando e parando o Hyperledger Fabric

Há um conjunto de scripts no diretório `fabric-tools`, que controlam a instância do Hyperledger Fabric. É possível parar e iniciar o tempo de execução usando `~/fabric-tools/stopFabric.sh` e `~/fabric-tools/startFabric.sh`.

O `~/fabric-tools/teardownFabric.sh` exigirá que você crie um novo cartão PeerAdmin quando o ambiente for iniciado.
