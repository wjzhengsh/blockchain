---

copyright:
  years: 2019
lastupdated: "2019-03-05"

subcollection: blockchain

---


{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:pre: .pre}

# Detecção de problemas
{: #ibp-v2-troubleshooting}

Problemas gerais podem ocorrer ao usar o console para gerenciar nós, canais ou contratos inteligentes. Em muitos casos, é possível recuperar-se desses problemas seguindo algumas etapas simples.

## Por que minhas operações de nó estão falhando após eu criar o peer ou o solicitador?
{: #ibp-console-build-network-troubleshoot-entry1}

Possivelmente, um erro ocorreu ao gerenciar um nó existente. Geralmente, quando isso acontece, é útil consultar os logs do nó.  

Por exemplo, ao tentar operar o nó, a ação pode falhar.
{: tsSymptoms}

Depois de criar um novo peer ou solicitador, dependendo da configuração de seu armazenamento de cluster, pode demorar alguns minutos para que o nó esteja pronto para operação.
{: tsCauses}

Verifique o painel do Kubernetes e assegure-se de que o status do peer ou do nó seja `Running`. Em seguida, tente sua ação novamente. Se você ainda estiver tendo problemas após o nó estar ativo, [verifique os seus logs de nó](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) quanto a erros.  
{: tsResolve}

## Por que meu peer falha ao ser iniciado?
{: #ibp-console-build-network-troubleshoot-entry2}

É possível que esse erro tenha acontecido sob uma variedade de condições.

O log do peer inclui `2019-02-06 19:43:24.159 UTC [main] InitCmd -> ERRO 001 Cannot run peer because cannot init crypto, folder “/certs/msp” does not exist`
{: tsSymptoms}

- Esse erro pode ocorrer sob as condições a seguir:
  - Quando você criou a definição de MSP da organização do peer ou do solicitador, você especificou um ID de inscrição e um segredo que correspondem a uma identidade do tipo `peer` e não `client`. Ele deve ser do tipo  ` client `.
  - Quando você criou a definição de MSP da organização do peer ou do solicitador, você especificou um ID de inscrição e um segredo que não correspondem ao ID de inscrição ou ao segredo da identidade administrativa da organização correspondente.
  - Quando você criou o peer ou o solicitador, você especificou o ID de inscrição e o segredo de uma identidade que não é do tipo 'peer'.

- Abra seu nó de autoridade de certificação de peer ou solicitador e visualize as identidades registradas listadas na tabela **Usuários registrados**.
- Exclua o peer ou o solicitador e recrie-o, sendo cuidadoso para especificar o ID de inscrição e o segredo corretos.
- Observe que, antes de criar o peer ou o solicitador, é necessário criar um ID de administrador da organização do tipo 'client'. Certifique-se de especificar o mesmo ID que o ID de inscrição ao criar a definição de MSP da organização. Consulte essas instruções para [registrar identidades de peer](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-org1) e essas instruções para [registrar identidades de solicitador](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-orderer).
{: tsResolve}

## Por que minha instalação, instanciação ou upgrade do contrato inteligente falha?
{: #ibp-console-smart-contracts-troubleshoot-entry1}

É possível que tenha ocorrido um erro ao instalar, instanciar ou fazer upgrade de um contrato inteligente. Por exemplo, ao tentar instalar um contrato inteligente em um peer, ele falha com o erro `An error occurred when installing smart contract on peer.`
{: tsSymptoms}

Você poderá receber esse erro se essa versão do contrato inteligente já existir no peer ou se o seu peer estiver sem recursos.
{: tsCauses}

- Abra o painel do Kubernetes e assegure-se de que o status do peer seja `Running`.  
- Abra o nó de peer e assegure-se de que a versão do contrato inteligente ainda não exista no peer e tente novamente com a versão adequada.
- Se você ainda estiver tendo problemas após o nó estar ativo, [verifique os seus logs de nó](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) quanto a erros.  
{: tsResolve}

## Como posso visualizar os logs de contêiner do meu contrato inteligente?
{: #ibp-console-smart-contracts-troubleshoot-entry2}

Talvez seja necessário visualizar os logs de contêiner do seu contrato inteligente, ou chaincode, para depurar um problema do contrato inteligente.
{: tsSymptoms}

Siga estas instruções para [visualizar os logs do contêiner](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-container-logs).
{: tsResolve}

## Meu canal, contratos inteligentes e identidades desapareceram do console. Como posso pegá-los de volta?
{: #ibp-v2-troubleshooting-browser-storage}

Suas identidades de carteira eletrônica do console consistem em um par de chaves pública e privada que permite gerenciar os componentes de blockchain, mas eles são armazenados apenas no armazenamento local do seu navegador. Você é responsável por proteger e gerenciar essas identidades. Recomendamos que você as exporte para o sistema de arquivos depois de criá-las. Sempre que você cria um novo nó, associa uma identidade de sua carteira eletrônica do console ao nó. O que permite o gerenciamento do nó é essa identidade de administrador. Quando você alterna os navegadores ou muda para um navegador em uma máquina diferente, essas identidades não estão mais em sua carteira eletrônica. Portanto, não é possível gerenciar os componentes.
{: tsSymptoms}

Um dos novos recursos do {{site.data.keyword.blockchainfull_notm}} Platform 2.0 é que agora você é responsável por proteger e gerenciar seus certificados. Portanto, eles são persistidos apenas no armazenamento local do navegador para permitir que você gerencie o componente.
{: tsCauses}

- Sempre que uma nova definição de MSP da organização é criada, são geradas chaves para uma identidade que tem permissão para administrar a organização. Portanto, durante esse processo, deve-se clicar nos botões **Gerar** e, em seguida, **Exportar** para armazenar a identidade gerada em sua carteira eletrônica do console e, em seguida, salvá-la em seu sistema de arquivos como um arquivo JSON.
- Para resolver esse problema em seu navegador, é necessário importar essas identidades e associá-las ao nó correspondente:
  - No navegador no qual você está tendo o problema, clique na guia **Carteira eletrônica**, seguido por **Incluir identidade** para importar o arquivo JSON em sua carteira eletrônica.
  - Clique em **Fazer upload de JSON** e navegue para o arquivo JSON que você exportou usando o botão **Incluir arquivos**.
  - Clique em **Enviar**.
  - Agora, abra o nó do peer ou do solicitador ao qual essa identidade foi originalmente associada e clique no ícone **Configurações**.
  - Clique no botão  ** Associar identidade ** .
  - Selecione a identidade que você acabou de importar para a sua carteira eletrônica do console por meio da lista suspensa.
  - Clique em  ** Associar **.
- Repita esse processo para cada identidade que estava na carteira eletrônica do navegador original.
{: tsResolve}

## Por que estou obtendo o erro `An error occurred when updating channel` quando tento incluir uma organização em meu canal?
{: #ibp-v2-troubleshooting-update-channel}

Quando você tenta incluir outra organização em um canal, a atualização falha com a mensagem `An error occurred when updating channel`.
{: tsSymptoms}

Esse erro ocorre quando o **ID do MSP do atualizador de canal** selecionado no painel **Atualizar canal** não é um administrador do canal.
{: tsCauses}

No painel **Atualizar canal**, role para baixo para o **ID do MSP do atualizador de canal** e selecione o ID do MSP que foi especificado quando o canal foi criado ou especifique o ID do MSP que é o administrador do canal.
{: tsResolve}
