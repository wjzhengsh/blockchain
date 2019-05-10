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
{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:pre: .pre}

# Detecção de problemas
{: #ibp-v2-troubleshooting}

Problemas gerais podem ocorrer ao usar o console para gerenciar nós, canais ou contratos inteligentes. Em muitos casos, é possível recuperar-se desses problemas seguindo algumas etapas simples.

- [Quando passo o mouse sobre o meu nó, o status é `Status unavailable`. O que isso significa?](#ibp-v2-troubleshooting-status-unavailable)
- [Quando passo o mouse sobre o meu nó, o status é `Status undetectable`. O que isso significa?](#ibp-v2-troubleshooting-status-undetectable)
- [Por que minhas operações de nó estão falhando após eu criar o peer ou o solicitador?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-build-network-troubleshoot-entry1)
- [Por que meu peer falha ao ser iniciado?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-build-network-troubleshoot-entry2)
- [Por que minha instalação, instanciação ou upgrade do contrato inteligente falha?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-smart-contracts-troubleshoot-entry1)
- [Como posso visualizar os logs de contêiner do meu contrato inteligente?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-smart-contracts-troubleshoot-entry2)
- [Meu canal, contratos inteligentes e identidades desapareceram do console. Como posso obtê-los de volta?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-browser-storage)
- [Por que estou obtendo o erro `An error occurred when updating channel` quando tento incluir uma organização em meu canal?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-update-channel)
- [Meu cluster Kubernetes expirou. O que isso significa?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-cluster-expired)
- [Por que as transações que eu envio do VSCode estão falhando?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-anchor-peer)

## Quando passo o mouse sobre o meu nó, o status é `Status unavailable`. O que isso significa?
{: #ibp-v2-troubleshooting-status-unavailable}

O status do nó no quadro do nó da CA, do peer ou do solicitador está cinza, o que significa que ele não está disponível. Idealmente, quando você passa o mouse sobre qualquer nó, o seu status deve ser `Running`.
{: tsSymptoms}

Esse problema poderá ocorrer caso o nó seja recém-criado e o processo de implementação não tenha sido concluído. Caso o nó seja uma CA, é provável que não esteja em execução.  Se for um peer ou um solicitador, essa condição ocorrerá quando o verificador de funcionamento executado com relação aos nós peer ou solicitador não conseguir entrar em contato com o nó. A solicitação de status pode falhar com um erro de tempo limite porque o nó não respondeu dentro de um período de tempo específico, o que significa que ele ou a conectividade de rede pode estar inativa.
{: tsCauses}

Se esse for um novo nó, aguarde mais alguns minutos até que a implementação tenha sido concluída. Se não for um novo nó, [examine seus logs associados](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) para ver os erros e determinar a causa.
{: tsResolve}

## Quando passo o mouse sobre o meu nó, o status é `Status undetectable`. O que isso significa?
{: #ibp-v2-troubleshooting-status-undetectable}

O status do nó no quadro para o nó do peer ou solicitador está amarelo, o que significa que ele não pode ser detectado. Idealmente, quando você passa o mouse sobre qualquer nó, o seu status deve ser `Running`.
{: tsSymptoms}

Essa condição ocorre somente em nós de peer e solicitador que foram *importados* para o console e o verificador de funcionamento não pode ser executado com relação ao nó. Esse status acontece porque um `operations_url` não foi especificado quando o nó foi importado. Uma URL de operações é necessária para que o verificador de funcionamento do nó seja executado. O nó em si provavelmente está `Running`, mas como a URL de operações não foi especificada, o status não pode ser determinado.
{: tsCauses}

É possível resolver esse problema executando as seguintes etapas:
 1. Clique no quadro do nó para abri-lo.
 2. Clique no ícone **Configurações**.
 3. Clique em **Associar identidade**, visualize e anote a identidade associada a esse nó. Clique em **Cancelar** para fechar esse painel.
 4. Clique em **Exportar** para gerar um arquivo `JSON` para o nó.
 5. Edite o arquivo `JSON` gerado e insira o `operations_url` para o nó. O valor de `operations_url` depende de como ele foi configurado e de diversas configurações de rede. Esse valor precisa ser fornecido pelo administrador de rede que implementou o nó.
 6. Clique em **Excluir (Delete)**. Essa etapa remove o nó importado do console, mas não exclui o nó real.
 7. Na guia **Nós**, clique em **Incluir peer** ou em **Incluir solicitador**, em seguida, clique em **Importar um peer existente** ou **Importar um solicitador existente**.
 8. Clique em **Fazer upload de JSON** e navegue até o arquivo JSON que acabou de editar. Clique em **Avançar**.
 9. Associe a mesma identidade anotada na etapa três.
 10. Clique em **Incluir peer** ou **Incluir solicitador**.

Agora, o verificador de funcionamento poderá ser executado com relação ao nó e relatar seu status.
{: tsResolve}

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
  - Quando você criou a definição do MSP da organização do peer ou do solicitador, você especificou um ID de inscrição e um segredo que correspondem a uma identidade do tipo `peer` e não `client`. Ele deve ser do tipo  ` client `.
  - Quando você criou a definição do MSP da organização do peer ou do solicitador, você especificou um ID de inscrição e um segredo que não correspondem ao ID de inscrição ou ao segredo da identidade administrativa da organização correspondente.
  - Quando você criou o peer ou o solicitador, você especificou o ID de inscrição e o segredo de uma identidade que não é do tipo 'peer'.

- Abra seu nó de autoridade de certificação de peer ou solicitador e visualize as identidades registradas listadas na tabela **Usuários registrados**.
- Exclua o peer ou o solicitador e recrie-o, sendo cuidadoso para especificar o ID de inscrição e o segredo corretos.
- Observe que, antes de criar o peer ou o solicitador, é necessário criar um ID de administrador da organização do tipo 'client'. Certifique-se de especificar o mesmo ID que o ID de inscrição ao criar a definição do MSP da organização. Consulte essas instruções para [registrar identidades de peer](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-org1) e essas instruções para [registrar identidades de solicitador](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-orderer).
{: tsResolve}

## Por que minha instalação, instanciação ou upgrade do contrato inteligente falha?
{: #ibp-console-smart-contracts-troubleshoot-entry1}

É possível que tenha ocorrido um erro ao instalar, instanciar ou fazer upgrade de um contrato inteligente.  Por exemplo, ao tentar instalar um contrato inteligente em um peer, ele falha com o erro `An error occurred when installing smart contract on peer.`
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

## Meu canal, contratos inteligentes e identidades desapareceram do console. Como posso obtê-los de volta?
{: #ibp-v2-troubleshooting-browser-storage}

Suas identidades de carteira eletrônica do console consistem em um par de chaves pública e privada que permite gerenciar os componentes de blockchain, mas eles são armazenados apenas no armazenamento local do seu navegador. Você é responsável por proteger e gerenciar essas identidades. Recomendamos que você as exporte para o sistema de arquivos depois de criá-las. Sempre que você cria um novo nó, associa uma identidade de sua carteira eletrônica do console ao nó. O que permite o gerenciamento do nó é essa identidade do administrador. Quando você alterna os navegadores ou muda para um navegador em uma máquina diferente, essas identidades não estão mais em sua carteira eletrônica. Portanto, não é possível gerenciar os componentes.
{: tsSymptoms}

Um dos novos recursos do {{site.data.keyword.blockchainfull_notm}} Platform 2.0 é que agora você é responsável por proteger e gerenciar seus certificados. Portanto, eles são persistidos apenas no armazenamento local do navegador para permitir que você gerencie o componente. Se estiver usando uma janela de navegador privada e, em seguida, alternar para uma janela de navegador não privada ou para outro navegador, as identidades criadas serão removidas da carteira eletrônica de seu console na nova sessão do navegador. Portanto, é necessário que você exporte as identidades da carteira eletrônica do console em sua sessão de navegador privada para seu sistema de arquivos. Em seguida, será possível importá-las para a sessão de navegador não privada, se necessário. Caso contrário, não haverá formas de recuperá-las.
{: tsCauses}

- Sempre que uma nova definição do MSP da organização é criada, são geradas chaves para uma identidade que tem permissão para administrar a organização. Portanto, durante esse processo, deve-se clicar nos botões **Gerar** e, em seguida, **Exportar** para armazenar a identidade gerada em sua carteira eletrônica do console e, em seguida, salvá-la em seu sistema de arquivos como um arquivo JSON.
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

## Meu cluster Kubernetes expirou. O que isso significa?
{: #ibp-v2-troubleshooting-cluster-expired}

Recebi um e-mail informando que meu cluster do serviço {{site.data.keyword.IBM_notm}} Kubernetes está prestes a expirar ou seu status é `Expired`. Ou, após 30 dias, não será possível acessar o console.
{: tsSymptoms}

Os clusters Kubernetes gratuitos são válidos por apenas 30 dias.
{: tsCauses}

Não é possível migrar de um cluster gratuito para um pago. Após 30 dias, não será possível acessar o console e todos os seus nós e certificados serão excluídos. Consulte este tópico sobre [Expiração do cluster Kubernetes](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-cluster-expiration) para obter informações sobre o que está acontecendo e o que pode ser feito.
{: tsResolve}

## Por que as transações que eu envio do VSCode estão falhando?
{: #ibp-v2-troubleshooting-anchor-peer}

As transações enviadas do VSCode falham com um erro semelhante a:
```
Error submitting transaction: No endorsement plan available for {"chaincodes":[{"name":"hello-world"}]}
```
{: tsSymptoms}

Esse erro ocorrerá se você estiver usando o recurso Fabric Service Discovery, mas não configurou nenhum peer de âncora em seu canal.
{: tsCauses}

Siga a etapa três do [tópico de dados privados](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) no tutorial Implementar um contrato inteligente para configurar seus peers de âncora.
