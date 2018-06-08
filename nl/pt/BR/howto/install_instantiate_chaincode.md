---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-15"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Instalando e instanciando, e atualizar um chaincode

Chaincode é o software, que é gravado em Go, Java ou Node.js, que encapsula a lógica de negócios e as instruções transacionais
para criar e modificar ativos no livro razão. Um chaincode é executado em um contêiner do docker que está associado a qualquer peer que precise interagir com ele.  Para obter mais informações sobre como desenvolver chaincode, veja [Tutoriais de chaincode ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/en/latest/chaincode.html).
{:shortdesc}

O chaincode é instalado em um sistema de arquivos do peer e, em seguida, instanciado em um canal. **Todos os membros do canal precisam instalar o chaincode em cada peer que executará esse chaincode.** Para usar o mesmo chaincode, os membros do canal deverão dar ao chaincode o mesmo nome e versão quando eles instalarem o chaincode. Então, a etapa de instanciação envolve a inicialização de pares chave-valor e a implementação do contêiner de chaincode. Qualquer peer, por meio do qual os aplicativos interagirão com um chaincode, deve ter o chaincode que está instalado no sistema de arquivos do peer e ter um contêiner de chaincode em execução. **No entanto, se um peer usar o mesmo chaincode em múltiplos canais, ele precisará apenas de uma única instância do contêiner de chaincode**.

A **combinação de instalação e instanciação** é um recurso poderoso, pois permite que um peer interaja com o mesmo contêiner de chaincode em múltiplos canais. O único pré-requisito é para que os arquivos de origem de chaincode reais sejam instalados no sistema de arquivos do peer. Dessa forma, se um pedaço de chaincode comum estivesse sendo usado em vários canais, um peer precisaria apenas de um único contêiner de chaincode para executar leituras/gravações em todos os livros razão do canal. Essa abordagem leve se mostra benéfica para o desempenho e o rendimento computacionais à medida que as redes são escaladas e o chaincode se torna mais elaborado.

**Nota**: se você desenvolver o seu chaincode em uma maneira iterativa e precisar atualizar um chaincode, precisará repetir as duas etapas de instalação e instanciação para o chaincode.


## Instalando um chaincode
{: #installchaincode}

Deve-se instalar o chaincode em cada peer que executará esse chaincode. Conclua as etapas a seguir para instalar um chaincode:
1. Na tela "Instalar código" de seu Monitor de rede, selecione um peer na lista suspensa no qual instalar o chaincode. Clique no botão **Instalar chaincode**.
<!--
  ![Chaincode screen](../images/chaincode_install_overview.png "Chaincode screen")
-->

2. No painel pop-up **Instalar chaincode**, insira o nome e a versão de seu chaincode. **Observe que** as sequências de nome e de versões serão usadas em aplicativos para interagir com o chaincode instalado. Clique no botão **Navegar** e navegue por seu sistema de arquivos local para onde quer que os seus arquivos de origem de chaincode estejam armazenados. Selecione um ou mais arquivos de origem de chaincode para instalar no peer. Em seguida, selecione a sua linguagem do chaincode na lista suspensa **Tipo de chaincode**.

  ![Instalar chaincode](../images/chaincode_install.png "Instalar chaincode")



## Instancie um chaincode
Após um chaincode ser instalado no sistema de arquivos de cada peer que se associa a um canal, o chaincode deverá, então, ser instanciado no canal para que os peers possam interagir com o livro-razão por meio do contêiner de chaincode. A instanciação executa qualquer inicialização necessária do chaincode. Muitas vezes, isso envolve a configuração dos pares de valores de chave que constituem o estado geral inicial do chaincode.

Você precisa ter a autoridade de **Operador** ou **Escritor** no canal para instanciar o chaincode. O chaincode que tem o mesmo nome e versão em peers diferentes precisa ser instanciado apenas uma vez para implementar o contêiner de chaincode. Conclua as etapas a seguir para instanciar um chaincode:
1. Na tela "Instalar código" de seu Monitor de rede, selecione o peer no qual você instalou o chaincode e localize o chaincode que você deseja instanciar na tabela de chaincode. Em seguida, clique no botão **Instanciar** sob o cabeçalho **Ação**.
<!--
  ![Instantiate Chaincode](../images/chaincode_instantiate.png "Instantiate Chaincode")
-->

2. No painel pop-up **Instanciar chaincode**, configure os pares chave-valor como argumentos para a inicialização do chaincode e selecione o canal no qual será feita a instanciação. Clique em **Enviar**.
<!--
  ![Instantiate Chaincode panel](../images/chaincode_instantiate_panel.png "Instantiate Chaincode panel")
-->

## Atualizando um chaincode
É possível atualizar um chaincode para mudar a programação do chaincode, mantendo o seu relacionamento com os ativos no livro razão. Devido à combinação de instalação e instanciação, será necessário atualizar o chaincode em todos os peers que estiverem no canal com esse chaincode. Conclua as etapas a seguir para atualizar o seu chaincode.

1. Instale um chaincode com o mesmo nome que o seu antigo chaincode, mas com uma versão diferente. É possível seguir as mesmas etapas que [Instalando um chaincode](install_instatiate_chaincode.html#Installing a chaincode). Certifique-se de selecionar o mesmo canal que o seu chaincode original.

  ![Update Chaincode](../images/upgrade_chaincode.png "Update Chaincode")

2. Localize o seu novo chaincode na tabela e clique no botão **Atualizar** sob o cabeçalho **Ação**. Essa ação instancia novamente o seu chaincode e substitui o contêiner de chaincode por um novo. Observe que não é necessário inserir nenhum novo argumento como parte da função de atualização.

  ![Update button](../images/upgrade_button.png "Update button")
