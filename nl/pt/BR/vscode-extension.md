---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Ferramentas para contratos inteligentes
{: #overview}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


A extensão do {{site.data.keyword.blockchainfull}} Platform Visual Studio Code fornece um ambiente no Visual Studio Code para desenvolvimento, empacotamento e implementação de pacotes de contratos inteligentes. A extensão também inclui comandos para configurar uma instância local pré-configurada do Hyperledger Fabric para o desenvolvimento de contratos inteligentes locais simplificados.

**Observe:** a extensão do {{site.data.keyword.blockchainfull_notm}} Platform é compatível com as redes do {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan e com o Hyperledger Fabric 1.3.x.

## Pré-requisitos

Antes de instalar a extensão do {{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code, conclua os pré-requisitos a seguir.

- **Instale o Yeoman**

  Yeoman é uma ferramenta do gerador usada para criar projetos de contratos inteligentes de estrutura básica. Instale o Yeoman usando o comando a seguir: `npm install -g yo`

- **Instale o gerador do Fabric para o Yeoman**

  Depois de instalar o Yeoman, instale o gerador do Fabric para criar pacotes de contrato inteligente de estrutura básica. Instale o gerador do Fabric usando o comando a seguir: `npm install -g generator-fabric`

- **Instale o Docker**

  Para executar a instância pré-configurada do Hyperledger Fabric, assegure-se de ter instalado o [Docker](https://www.docker.com/).

- **Requisitos do sistema operacional**

  Atualmente, essa extensão é compatível com Mac, Windows e Linux.

- **Requisitos de versão do Hyperledger Fabric**

  Essa extensão é compatível com o Hyperledger Fabric versões 1.3.0 e mais recente.

- **Requisitos de versão do Hyperledger Fabric**

  Essa extensão é compatível com o Hyperledger Fabric versões 1.3.0 e mais recente.


## Instalando a extensão

1. Navegue para a [página de marketplace da extensão do Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=IBMBlockchain.ibm-blockchain-platform) ou procure por ** {{site.data.keyword.blockchainfull_notm}} Platform** no painel de extensões no Visual Studio Code.
2. Clique em **Instalar**.
3. Reinicie o Visual Studio Code para concluir a instalação da extensão.

## Paleta de comandos do Visual Studio Code

Essas extensões incluem muitos comandos na paleta de comandos do Visual Studio Code. Muitas das operações detalhadas neste documento também podem ser iniciadas executando o comando a partir da paleta de comandos do Visual Studio Code.

## Criando um projeto

Para criar um novo projeto, conclua as etapas a seguir:

1. Abra a paleta de comandos, execute o comando **Blockchain: criar projeto de contrato inteligente**.
2. Selecione a linguagem na qual você deseja criar um contrato inteligente, atualmente as opções são JavaScript ou TypeScript.
3. Crie uma pasta com o nome de seu projeto e abra-a.
4. Selecione como abrir seu novo projeto. A pasta do projeto agora deve ser aberta.

## Conectando-se e desconectando-se de uma rede

Ao usar essa extensão, você instala e instancia pacotes de contratos inteligentes em peers e canais em sua instância do Hyperledger Fabric. Essa extensão pode inicializar uma instância local pré-configurada do Hyperledger Fabric usando o Docker.

### Conectando-se à instância pré-configurada do Hyperledger Fabric

Para conectar-se à instância pré-configurada do Hyperledger Fabric, primeiro assegure-se de que o Docker esteja em execução em sua máquina:

1. Abra a guia _ {{site.data.keyword.blockchainfull_notm}} Platform_ no Visual Studio Code.
2. Na área de janela _ {{site.data.keyword.blockchainfull_notm}} Platform_, clique em **local_fabric**. Se o Docker estiver em execução, a instância local do Hyperledger Fabric deverá ser transferida por download e iniciada.
3. Depois que a instância local do Hyperledger Fabric tiver sido iniciada, dê um clique duplo em **local_fabric** para conectar-se a ela. Agora você deverá ver um canal chamado `mychannel`.

#### Reiniciando o tempo de execução pré-configurado do Hyperledger Fabric

Para reiniciar o tempo de execução `local_fabric`:

1. Clique com o botão direito na conexão `local_fabric` quando ela for estabelecida.
2. Selecione **Reiniciar tempo de execução do Fabric**.

Os contêineres do Hyperledger Fabric serão interrompidos e reiniciados.

#### Derrube o tempo de execução do Hyperledger Fabric

Para derrubar o tempo de execução `local_fabric`:

1. Clique com o botão direito na conexão `local_fabric` quando ela for estabelecida.
2. Selecione **Derrubar tempo de execução do Fabric**.

A execução de uma derrubada da rede `local_fabric` fechará todos os contêineres do Hyperledger Fabric. **Observação**: isso resultará na perda dos dados do livro-razão e do estado mundial.

#### Ativando o modo de desenvolvimento no tempo de execução pré-configurado do Hyperledger Fabric

Na operação normal, um peer criará e manterá um contêiner de chaincode para executar contratos inteligentes instanciados. Ao alternar para o modo de desenvolvimento, o peer permite que o contêiner de chaincode seja executado manualmente. Executar o contêiner do chaincode manualmente, na linha de comandos ou em um terminal, auxilia no desenvolvimento iterativo e na depuração de contratos inteligentes.

Para ativar o modo de desenvolvimento com o tempo de execução pré-configurado do Hyperledger Fabric:

1. Clique com o botão direito na conexão `local_fabric` quando ela for estabelecida.
2. Selecione **Alternar modo de desenvolvimento**.

O modo de desenvolvimento deve ser ativado para usar o recurso de depuração da extensão.

### Conectando-se ao {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan

A extensão do {{site.data.keyword.blockchainfull_notm}} Platform Visual Studio Code pode ser usada para se conectar à uma instância do {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan. Depois de conectado, a extensão pode ser usada para desenvolver e implementar contratos inteligentes.

**Nota:** atualmente, a extensão não é compatível com o {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan.

1. Se você não tiver uma instância do {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan, crie uma [aqui](https://console.bluemix.net/catalog/services/blockchain).
2. Abra a IU do IBP clicando em **Ativar**.
3. Recupere seu perfil de conexão clicando em **Visão geral** > **Perfil de conexão** > **Download**.
4. Gere os certificados de administrador necessários clicando em **Autoridade de certificação** > **Gerar certificado** e salvando o certificado e a chave privada em seu sistema de arquivos.
5. Inclua os certificados para sua instância do {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan clicando em **Membros** > **Certificados** > **Incluir certificado** e selecionando o certificado criado na etapa precedente.
6. No Visual Studio Code, abra a visualização de extensão do {{site.data.keyword.blockchainfull_notm}} Platform e clique em **Incluir nova conexão**.
7. Insira o nome da conexão e o caminho para o perfil de conexão, o certificado e a chave privada.

### Conectando-se à sua própria instância do Hyperledger Fabric

Usando essa extensão, é possível se conectar a uma instância local pré-configurada do Hyperledger Fabric ou é possível se conectar à sua própria instância do Hyperledger Fabric. Se você optar por se conectar à sua própria instância do Fabric, ela deverá ser Fabric 1.3.0 ou mais recente.

Para conectar sua própria instância do Hyperledger Fabric, conclua as etapas a seguir:

1. Abra a guia _{{site.data.keyword.blockchainfull_notm}} Platform_ no Visual Studio Code.
2. Na área de janela _{{site.data.keyword.blockchainfull_notm}} Plataforma_, clique em **Incluir nova conexão**.
3. Insira um nome para a conexão. Esse nome será exibido na área de janela _ {{site.data.keyword.blockchainfull_notm}} Platform_.
4. Insira o caminho de arquivo completo de seu perfil de conexão do Hyperledger Fabric.
5. Insira o caminho de arquivo completo do certificado para sua identidade.
6. Insira o caminho de arquivo completo da chave privada para uso com o certificado fornecido na etapa anterior.
7. Sua conexão não deverá aparecer na lista de conexões abaixo de `local_fabric`. Dê um clique duplo no nome da conexão para se conectar.

Se você não tiver uma instância pré-existente da versão 1.3.0 ou mais recente do Hyperledger Fabric, será possível se conectar usando a instância pré-configurada do Hyperledger Fabric ou executar os comandos a seguir para extrair e identificar as imagens da versão 1.3.0:

```
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ca:amd64-1.3.0-stable hyperledger/fabric-ca
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-orderer:amd64-1.3.0-stable hyperledger/fabric-orderer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-peer:amd64-1.3.0-stable hyperledger/fabric-peer
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-tools:amd64-1.3.0-stable hyperledger/fabric-tools
docker pull nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable
docker tag nexus3.hyperledger.org:10001/hyperledger/fabric-ccenv:amd64-1.3.0-stable hyperledger/fabric-ccenv
```
{:codeblock}

### Desconectando-se de uma rede

Quando conectado a uma rede, é possível fechar a conexão clicando no ícone de desconexão no canto superior direito do painel Conexões do Blockchain. Também é possível desconectar executando o comando **Desconectar-se de um blockchain** a partir da paleta de comandos.

## Editando ou excluindo uma conexão

As conexões podem ser editadas ou excluídas na área de janela de conexões.

### Editando uma conexão

Ao editar uma conexão, é possível mudar o caminho de arquivo para o perfil de conexão, o nome fornecido para a conexão e os caminhos de arquivo para os certificados de identidade e as chaves privadas.

Para editar uma conexão:

1. Na extensão, clique com o botão direito na conexão para editar na parte inferior esquerda, isso abre um menu contextual com as opções para incluir uma identidade, editar a conexão ou excluir a conexão.
2. Selecione **Editar conexão**.
3. A página _Configurações do usuário_ será aberta, com os detalhes da conexão destacados.
4. Faça quaisquer mudanças e salve a página de configurações.

### Excluindo uma conexão

As conexões podem ser excluídas por:

1. Na extensão, clique com o botão direito na conexão para editar na parte inferior esquerda, isso abre um menu contextual com as opções para incluir uma identidade, editar a conexão ou excluir a conexão.
2. Selecione **Excluir conexão**.
3. Uma caixa de diálogo aparece para confirmar a exclusão da conexão. Clique em **Sim**.

A conexão será excluída.

## Incluindo identidades

Ao incluir uma conexão, deve-se enviar um perfil de conexão, um certificado e uma chave privada. A identidade que está associada ao par de certificado/chave privada será configurada como a identidade padrão a ser usada ao se conectar a essa instância do Hyperledger Fabric.

Para incluir uma identidade em uma conexão que já está estabelecida:

1. Na extensão, clique com o botão direito na conexão para editar na parte inferior esquerda, isso abre um menu contextual com as opções para incluir uma identidade, editar a conexão ou excluir a conexão.
2. Selecione **Incluir uma identidade**.
3. Forneça os caminhos de arquivo para os certificados de identidade e a chave privada.

## Explorando conexões

Depois de se conectar a uma instância do Hyperledger Fabric, é possível ver uma lista dos canais disponíveis e peers nesses canais na área de janela _{{site.data.keyword.blockchainfull_notm}} Platform_. Passe um minuto explorando sua instância do Hyperledger Fabric para se familiarizar com a estrutura. Primeiro são listados os canais dentro de uma conexão, sob um canal há uma lista dos peers que são membros desse canal e uma lista de pacotes de contratos inteligentes instanciados nesse canal. Sob cada peer na lista há uma lista de todos os pacotes de contratos inteligentes instalados nesse peer. Também é possível ver as versões de pacotes de contratos inteligentes instalados ou instanciados.

### Exportando detalhes de conexão para o tempo de execução do Hyperledger Fabric pré-configurado

Os detalhes da conexão necessários para se conectar à conexão `local_fabric` podem ser exportados. Os detalhes da conexão para a conexão `local_fabric` são úteis para testar aplicativos clientes destinados a se conectar ou interagir com sua instância do Hyperledger Fabric.

Para exportar os detalhes da conexão `local_fabric`:

1. Inicie o tempo de execução do Hyperledger Fabric pré-configurado.
2. Clique com o botão direito na conexão `local_fabric` e selecione **Exportar detalhes da conexão**.

Os detalhes da conexão são salvos em um diretório chamado `local_fabric` contido em seu diretório de projeto atual.

## Empacotando um contrato inteligente

Quando um contrato inteligente está pronto para ser implementado, ele deve primeiro ser empacotado. Para empacotar um contrato inteligente, conclua as etapas a seguir:

1. No Visual Studio Code, navegue para o painel _{{site.data.keyword.blockchainfull_notm}} Platform_.
2. Na área de janela _Pacotes de contrato inteligente_, clique no ícone +. Se você tiver um projeto de contrato inteligente aberto no visualizador de arquivos, ele será empacotado automaticamente e será mostrado na área de janela _Pacotes de contratos inteligentes_. Se você tiver várias pastas de contratos inteligentes abertas, será perguntado qual será empacotada. Se você não tiver pastas de contrato inteligente abertas, receberá uma mensagem de erro.

## Instalando pacotes de contrato inteligente

Depois de se conectar a uma instância do Hyperledger Fabric, será possível instalar e instanciar pacotes de contratos inteligentes nos peers.

1. Na área de janela _{{site.data.keyword.blockchainfull_notm}} Platform_, navegue até o peer no qual você deseja instalar o pacote de contrato inteligente.
2. Clique com o botão direito no peer no qual instalar o pacote de contrato inteligente e selecione **Instalar contrato inteligente**.

## Instanciando pacotes de contrato inteligente

Para iniciar a execução em um canal, um pacote de contrato inteligente instalado deve primeiro ser instanciado.

1. Conecte-se à instância do Hyperledger Fabric na qual o pacote de contrato inteligente está instalado.
2. Clique com o botão direito do mouse no canal no qual você deseja instanciar o pacote de contrato inteligente e clique em **Instanciar contrato inteligente**.
3. Selecione o pacote de contrato inteligente e a versão a serem instanciados. O pacote de contrato inteligente deve ser instalado em um peer que seja um membro desse canal.
4. Insira o nome da função de instanciação em seu contrato inteligente.
5. Insira quaisquer argumentos requeridos por sua função de instanciação.

## Exportando ou excluindo um pacote de contrato inteligente

Depois que um contrato inteligente foi empacotado, ele pode ser exportado como um arquivo `.cds` ou excluído se não for mais necessário.

Para excluir um pacote de contrato inteligente:

1. No painel de extensão do {{site.data.keyword.blockchainfull_notm}} Platform, clique com o botão direito no pacote do contrato inteligente a ser excluído.
2. Selecione **Excluir pacote**.

O pacote agora será excluído e desaparecerá da lista de pacotes de contratos inteligentes.

Para exportar um pacote de contrato inteligente:

1. No painel de extensão do {{site.data.keyword.blockchainfull_notm}} Platform, clique com o botão direito no pacote do contrato inteligente a ser exportado.
2. Selecione **Exportar pacote**.
3. Escolha o diretório para salvar seu arquivo de pacote de contrato inteligente e clique em **Exportar**.

## Testando um contrato inteligente instanciado

Testes para um contrato inteligente podem ser gerados após o contrato inteligente ter sido instanciado. Os testes podem ser gerados como JavaScript ou TypeScript e executados ou depurados.

Para gerar testes de contrato inteligente:

1. Assegure-se de que o contrato inteligente tenha sido instanciado.
2. Em **Contratos inteligentes instanciados**, clique com o botão direito no contrato inteligente para o qual gerar testes.
3. Selecione **Gerar testes de contrato inteligente**.
4. Agora, selecione a linguagem para o arquivo de teste, **JavaScript** ou **TypeScript**. A extensão do {{site.data.keyword.blockchainfull_notm}} Platform agora instalará os módulos npm necessários e construirá o arquivo de teste.

Depois que o arquivo de teste tiver sido construído, os testes poderão ser executados clicando no botão **Executar testes** no arquivo.

## Depurando um contrato inteligente usando o tempo de execução pré-configurado do Hyperledger Fabric

A depuração de um contrato inteligente permite que você execute as transações do contrato inteligente com pontos de interrupção e saída, assegurando que as transações funcionem como pretendido. Para depurar seu contrato inteligente:

1. Assegure-se de que você esteja conectado à conexão `local_fabric` no modo de desenvolvimento.
2. Abra seu projeto de contrato inteligente.
3. Abra a visualização de depuração no Visual Studio Code usando a barra de navegação esquerda.
4. Selecione a configuração Depurar Contrato Inteligente usando a lista suspensa na parte superior esquerda.
5. Empacote e instale o contrato inteligente clicando no botão **reproduzir**.
6. Inclua pontos de interrupção no contrato inteligente clicando nos números de linha relevantes em seus arquivos de contrato inteligente.
7. Clique com o botão direito no contrato inteligente instalado e selecione **Instanciar**. Agora é possível clicar com o botão direito para enviar transações e a execução será pausada em pontos de interrupção definidos.

Para fazer mudanças em seu contrato inteligente durante a depuração, clique no botão **reiniciar** depois de fazer as mudanças em seu contrato inteligente. Reiniciar a depuração significa que não é necessário instanciar o contrato novamente.

**Observe**: a reinicialização da depuração armazena o contrato inteligente na memória local, para um grande número de mudanças em contratos inteligentes grandes, talvez seja necessário reinstanciar o contrato inteligente.

## Fazendo upgrade de um contrato inteligente instanciado

Depois que um contrato inteligente tiver sido instalado em um peer e instanciado em um canal, ele poderá ser atualizado para implementar uma versão mais recente do contrato inteligente.

1. Assegure-se de que o contrato inteligente que você deseja atualizar esteja instanciado.
2. Instale a nova versão do contrato inteligente para um peer na mesma rede.
3. Clique com o botão direito no contrato inteligente instanciado e selecione **Fazer upgrade do contrato inteligente**.
4. Opcionalmente, escolha uma transação para executar à medida que o novo contrato inteligente é instanciado.

## Enviando transações

Depois que um contrato inteligente tiver sido instalado e instanciado, as transações poderão ser enviadas a partir da área de janela de conexões no painel de extensão do {{site.data.keyword.blockchainfull_notm}} Platform.

Para enviar uma transação:

1. Assegure-se de que seu contrato inteligente esteja instalado e instanciado e que você esteja conectado à rede.
2. Na área de janela de conexões, expanda os **Contratos inteligentes instanciados**.
3. Expandir o contrato inteligente que contém a transação a ser enviada.
4. Clique com o botão direito na transação a ser enviada e selecione **Enviar transação**.
5. Insira quaisquer argumentos necessários para a transação e pressione **Enter**.
