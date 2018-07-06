---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Instalando um ambiente de desenvolvimento
{: #installing-a-development-environment}

Siga estas instruções para obter as ferramentas de desenvolvimento do {{site.data.keyword.blockchainfull}} Platform: Develop para criar e testar redes de negócios. Para fornecer flexibilidade e ativar o número máximo de cenários de desenvolvimento, teste e implementação, as ferramentas de desenvolvimento são entregues como módulos npm, que são controlados por meio da linha de comandos.

Diferentes versões de alguns componentes são necessárias para uso com o Starter Plan e o Enterprise Plan. Para usar o ambiente de desenvolvimento com o Starter Plan, os módulos devem ser instalados na versão `0.19.x`. Para usar o ambiente de desenvolvimento com o Enterprise Plan, os módulos devem ser instalados na versão `0.16.x`

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

As ferramentas de linha de comandos incluem **composer-cli**, o módulo de linha de comandos principal do {{site.data.keyword.blockchainfull_notm}} Platform: Develop; **generator-hyperledger-composer**, **composer-rest-server** e **Yeoman**, esses módulos são usados para gerar redes de negócios, servidores REST sob medida e aplicativos Angular.

1. Para uso com o Starter Plan, instale **composer-cli** usando o comando a seguir:

    ```
    Npm install -g composer-cli@0.19.x
    ```
    {:codeblock}

    Para uso com o Enterprise Plan, instale o **composer-cli** usando o comando a seguir:

    ```
    Npm install -g composer-cli@0.16.x
    ```
    {:codeblock}

2. Para uso com o Starter Plan, instale **composer-rest-server** usando o comando a seguir:

    ```
    Npm install -g composer-rest-server@0.19.x
    ```
    {:codeblock}

    Para uso com o Enterprise Plan, instale **composer-rest-server** usando o comando a seguir:

    ```
    Npm install -g composer-rest-server@0.16.x
    ```
    {:codeblock}

    Use o módulo **composer-rest-server** para criar um Servidor REST em sua máquina para expor as suas redes de negócios como APIs RESTful.

3. Para uso com o Starter Plan, instale **generator-hyperledger-composer** usando o comando a seguir:

    ```
    Npm install -g generator-hyperledger-composer@0.19.x
    ```
    {:codeblock}

    Para uso com o Enterprise Plan, instale **generator-hyperledger-composer** usando o comando a seguir:

    ```
    Npm install -g generator-hyperledger-composer@0.16.x
    ```
    {:codeblock}

    Use **generator-hyperledger-composer** para gerar as estruturas de rede de negócios de estrutura básica, modelos e um aplicativo Angular.

4. O Yeoman é uma ferramenta para gerar aplicativos, que utiliza `generator-hyperledger-composer`:

    ```
    npm install -g yo
    ```
    {:codeblock}

## Etapa dois: Instale um Playground local

O playground do Hyperledger Composer é uma interface com o usuário que pode ser conectada a um {{site.data.keyword.blockchain}} real ou usada como um ambiente simulado para testar uma rede de negócios. Para uso com o Starter Plan, instale o playground usando o comando a seguir:

```
Npm install -g composer-playground@0.19.x
```
{:codeblock}


Para uso com o Enterprise Plan, instale o playground usando o comando a seguir:


```
npm install -g composer-playground@0.16.x
```
{:codeblock}

## Opcional: Configure o seu IDE

É possível usar o playground para desenvolver, editar e testar as redes de negócios. No entanto, se você desejar desenvolver uma rede de negócios em um IDE, haverá uma extensão do VSCode disponível, que incluirá destaque de sintaxe para a linguagem de modelagem do Hyperledger Composer.

1. Instale o VSCode por meio dessa URL: [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. Abra o VSCode, acesse Extensões e, em seguida, procure e instale a extensão do Hyperledger Composer do mercado de trabalho.


## Somente Enterprise Plan: instale um Hyperledger Fabric local e se conecte a ele com o Playground

Implementando uma instância local do Hyperledger Fabric, é possível testar totalmente regras de controle de acesso e redes de negócios. Assegure-se de que você tenha iniciado o Docker antes de seguir estas etapas.

1. Crie o diretório `fabric-tools` usando o comando a seguir:

   ```
   mkdir ~/fabric-tools &&  cd ~/fabric-tools
   ```
   {:codeblock}

Em um diretório de sua escolha, por exemplo, `~/fabric-tools`, obtenha o arquivo `.tar.gz` que contém as ferramentas para instalar o Hyperledger Fabric:

2. Faça download do arquivo `tar.gz` que contém ferramentas para instalar o Hyperledger Fabric em um ambiente do Docker.

   ```
   curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.tar.gz
        tar -xvf fabric-dev-servers.tar.gz
   ```
   {:codeblock}

    Um `.zip` também está disponível. Substitua o arquivo `.tar.gz` por `fabric-dev-servers.zip` e o comando `tar -xvf` por um comando `unzip` no fragmento anterior.

3. Use os comandos a seguir para fazer download de um tempo de execução local do Hyperledger Fabric v1:

   ```
   cd ~/fabric-tools
   export FABRIC_VERSION=hlfv1
   ./downloadFabric.sh
   ```
   {:codeblock}

   O diretório `fabric-tools` contém um conjunto de scripts que controlam a instância do Hyperledger Fabric transferida por download. Deve-se executar o comando `./createPeerAdminCard.sh` para gerar uma placa de rede de negócios, que é possível usar para implementar a rede de negócios no Docker que hospeda a instância do Hyperledger Fabric.

4. Execute os comandos a seguir para iniciar a instância do Hyperledger Fabric v1:

   ```
   cd ~/fabric-tools
   export FABRIC_VERSION=hlfv1
   ./startFabric.sh
   ./createPeerAdminCard.sh
   ```
   {:codeblock}

   Para começar a desenvolver uma rede de negócios, use o playground do Hyperledger Composer ou o seu IDE.

5. Para iniciar o playground, execute o comando a seguir:

    ```
    composer-playground
    ```
    {:codeblock}

    O playground abre na URL a seguir: http://localhost:8080/login. O cartão **PeerAdmin@hlfv1** criado na etapa anterior pode ser usado para implementar uma rede de negócios.


### Iniciando e parando o Hyperledger Fabric

Há um conjunto de scripts no diretório `fabric-tools`, que controlam a instância do Hyperledger Fabric. É possível parar e iniciar o tempo de execução usando `~/fabric-tools/stopFabric.sh` e `~/fabric-tools/startFabric.sh`.

O `~/fabric-tools/teardownFabric.sh` exigirá que você crie um novo cartão PeerAdmin quando o ambiente for iniciado.
