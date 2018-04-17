---

copyright:
  years: 2017, 2018
lastupdated: "2018-3-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Instalando um ambiente de desenvolvimento
{: #installing-a-development-environment}

Siga estas instruções para obter as ferramentas de desenvolvimento do {{site.data.keyword.blockchainfull}} Plataforma: Develop para criar e testar redes de negócios. Para fornecer flexibilidade e ativar o número máximo de cenários de desenvolvimento, teste e implementação, as ferramentas de desenvolvimento são entregues como módulos npm, que são controlados por meio da linha de comandos.

## Antes de iniciar

Assegure-se de instalar os pré-requisitos a seguir:

- Ubuntu Linux 14.04 / 16.04 LTS (ambos de 64 bits) ou Mac OS 10.12
- Node v8.9 ou superior (v9 não é suportado)
- npm v5.x
- git v2.9.x ou superior
- Python v2.7.x
- Docker Engine v17.03 ou superior
- Docker-Compose v1.8 ou mais alto
- Um editor de código de sua escolha, como VSCode.

Se você estiver usando VSCode, há uma extensão de VSCode do Hyperledger Composer disponível [aqui](https://marketplace.visualstudio.com/items?itemName=HyperledgerComposer.composer-support-client).


## Etapa um: Instale as ferramentas de linha de comandos

As ferramentas de linha de comandos incluem **composer-cli**, o módulo de linha de comandos principal do {{site.data.keyword.blockchainfull_notm}} Platform: Develop; **generator-hyperledger-composer**, **composer-rest-server** e **Yeoman**, esses módulos são usados para gerar redes de negócios, servidores REST sob medida e aplicativos Angular.

1. Instale o **composer-cli** usando o comando a seguir:

        npm install -g composer-cli@next

2. Instale o **composer-rest-server** usando o comando a seguir:

        npm install -g composer-rest-server@next

    Use o módulo **composer-rest-server** para criar um Servidor REST em sua máquina para expor as suas redes de negócios como APIs RESTful.

3. Instale o **generator-hyperledger-composer** usando o comando a seguir:

        npm install -g generator-hyperledger-composer@next

    Use **generator-hyperledger-composer** para gerar as estruturas de rede de negócios de estrutura básica, modelos e um aplicativo Angular.

4. O Yeoman é uma ferramenta para gerar aplicativos, que utiliza `generator-hyperledger-composer`:

        npm install -g yo

## Etapa dois: Instale um Playground local

O playground do Hyperledger Composer é uma interface com o usuário que pode ser conectada a um {{site.data.keyword.blockchain}} real ou usada como um ambiente simulado para testar uma rede de negócios. Instale o playground usando o comando a seguir:

        npm install -g composer-playground@next

## Opcional: Configure o seu IDE

É possível usar o playground para desenvolver, editar e testar as redes de negócios. No entanto, se você desejar desenvolver uma rede de negócios em um IDE, haverá uma extensão do VSCode disponível, que incluirá destaque de sintaxe para a linguagem de modelagem do Hyperledger Composer.

1. Instale o VSCode por meio dessa URL: [https://code.visualstudio.com/download](https://code.visualstudio.com/download)

2. Abra o VSCode, acesse Extensões e, em seguida, procure e instale a extensão do Hyperledger Composer do mercado de trabalho.

## Etapa três: Instale um Hyperledger Fabric local

Implementando uma instância local do Hyperledger Fabric, é possível testar totalmente regras de controle de acesso e redes de negócios.

1. Crie o diretório `fabric-tools` usando o comando a seguir:

        mkdir ~/fabric-tools && cd ~/fabric-tools

Em um diretório de sua escolha, por exemplo, `~/fabric-tools`, obtenha o arquivo `.tar.gz` que contém as ferramentas para instalar o Hyperledger Fabric:

2. Faça download do arquivo `tar.gz` que contém ferramentas para instalar o Hyperledger Fabric em um ambiente do Docker.

        curl -O https://raw.githubusercontent.com/hyperledger/composer-tools/master/packages/fabric-dev-servers/fabric-dev-servers.tar.gz
        tar -xvf fabric-dev-servers.tar.gz

    Um `.zip` também está disponível. Substitua o arquivo `.tar.gz` por `fabric-dev-servers.zip` e o comando `tar -xvf` por um comando `unzip` no fragmento anterior.

3. Use os comandos a seguir para fazer download de um tempo de execução do Hyperledger Fabric v1.1 local:

        cd ~/fabric-tools
        export FABRIC_VERSION=hlfv11
        ./downloadFabric.sh

## Etapa quatro: Iniciando a instância do Hyperledger Fabric

O diretório `fabric-tools` contém um conjunto de scripts que controlam a instância do Hyperledger Fabric transferida por download. Deve-se executar o comando `./createPeerAdminCard.sh` para gerar uma placa de rede de negócios, que é possível usar para implementar a rede de negócios no Docker que hospeda a instância do Hyperledger Fabric.

1. Execute os comandos a seguir para iniciar a instância do Hyperledger Fabric v1.1:

        cd ~/fabric-tools
        export FABRIC_VERSION=hlfv11
        ./startFabric.sh
        ./createPeerAdminCard.sh

## Opcional: Conecte a instância do Hyperledger Fabric usando o Playground

Para começar a desenvolver uma rede de negócios, use o playground do Hyperledger Composer ou o seu IDE.

1. Para iniciar o playground, execute o comando a seguir:

        composer-playground

    O playground abre na URL a seguir: http://localhost:8080/login. O cartão **PeerAdmin@hlfv1** criado na etapa anterior pode ser usado para implementar uma rede de negócios.


## Iniciando e parando o Hyperledger Fabric

Há um conjunto de scripts no diretório `fabric-tools`, que controlam a instância do Hyperledger Fabric. É possível parar e iniciar o tempo de execução usando `~/fabric-tools/stopFabric.sh` e `~/fabric-tools/startFabric.sh`.

O `~/fabric-tools/teardownFabric.sh` exigirá que você crie um novo cartão PeerAdmin quando o ambiente for iniciado.
