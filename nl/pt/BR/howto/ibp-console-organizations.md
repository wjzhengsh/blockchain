---

copyright:
  years: 2019
lastupdated: "2019-04-03"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Gerenciando organizações
{: #ibp-console-organizations}

É possível usar o console do {{site.data.keyword.blockchainfull}} Platform para criar uma definição de organização formal conhecida como um Membership Services Provider (MSP). A definição do MSP da sua organização permite que outros membros do consórcio de blockchain verifiquem a identidade de seus nós e aplicativos. Sua definição do MSP também contém os certificados de administrador de sua organização.

Também é possível usar o console para gerenciar quais organizações são membros de sua rede. O administrador do serviço de pedido pode usar a guia de organizações para incluir membros no [consórcio](/docs/services/blockchain/glossary.html#glossary-consortium) de blockchain. Os membros do consórcio podem, então, usar o console para incluir membros em canais novos ou existentes.

**Público-alvo:** este tópico é projetado para operadores de rede que são responsáveis por criar, monitorar e gerenciar a rede de blockchain.

## Entendendo os MSPs
{: #console-organizations-about-msp}

O {{site.data.keyword.blockchainfull_notm}} Platform é baseado no Hyperledger Fabric e constrói redes de blockchain permissionárias. Os participantes precisam ser conhecidos para a rede antes de poderem enviar transações e interagir com os ativos no livro-razão. O Fabric reconhece a identidade por meio de um grupo de organizações convidadas, conhecidas como consórcio. As organizações no consórcio são capazes de emitir credenciais válidas para seus membros e permitir que eles se tornem participantes na rede. Em seguida, os participantes podem operar nós de blockchain e enviar transações por meio de aplicativos clientes.

Cada organização no consórcio precisa operar sua própria Autoridade de certificação, conhecida como CA raiz. Essa Autoridade de certificação (ou suas CAs intermediárias) cria todas as identidades pertencentes à sua organização e emite para cada identidade uma chave pública e privada. Essas chaves são assinadas pela CA e usadas pelos membros de sua organização para assinar e verificar suas ações. Associar-se ao consórcio permite que outras organizações reconheçam sua assinatura de CAs e verifique se os seus peers e aplicativos são participantes válidos. Para obter mais informações sobre associação no Hyperledger Fabric, veja o [tópico de conceito Associação ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html "Associação") na documentação do Fabric.

Antes que sua organização possa se associar a um consórcio, ela precisa criar uma definição de organização conhecida como **Membership Services Provider (MSP)**. O MSP contém as informações a seguir:
- Um certificado assinado por sua **Autoridade de certificação raiz **. Esse certificado é usado para verificar a identidade de seus nós, canais e aplicativos.
- Um certificado assinado por sua **CA TLS**. Um certificado TLS raiz permite que os seus peers participem de gossip de organização cruzada, que é necessário para tirar proveito dos recursos de [coleções de **Dados privados**](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) e [Descoberta de serviço ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "Descoberta de serviço") do Hyperledger Fabric.
- O  ** ID do MSP **. O ID do MSP é o nome formal de sua organização dentro do consórcio. É necessário se lembrar do ID do MSP para seus nós e aplicativos.
- **Certificados de administrador** de suas identidades de **Administrador do peer** e **Administrador da organização**. Esses certificados são passados para o Serviço de pedido e são usados para verificar quais identidades em sua organização têm permissão para criar ou editar canais. Quando você usa seu console para criar um solicitador ou peer, os certificados de administrador dentro do MSP são implementados no novo nó. Esses certificados podem então ser usados para operar os peers ou solicitadores por meio seu console ou de um aplicativo cliente.

## Gerenciando MSPs no console

Navegue para a guia **Organizações**. É possível usar essa guia para [criar uma definição do MSP](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp) usando uma Autoridade de certificação que exista em seu console. Também é possível usar essa guia para [importar um MSP](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp) que foi criado por outra organização.

É possível visualizar todas as MSPs que você criou ou importou em **Organizações disponíveis**. É possível usar as definições do MSP na guia de organizações para funções importantes em seu console:
- Se você estiver criando nós de peer ou de solicitador, o MSP de sua organização será usado para fornecer certificados de administrador para o novo nó.
- Se você for o administrador de um nó de pedido, será possível usar os MSPs para [incluir novas organizações no consórcio](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium).
- Se você for um membro do consórcio, será possível importar os MSPs de outros membros do consórcio para o seu console e, em seguida, incluir os membros em canais novos ou existentes.

## Criando um MSP para sua organização
{: #console-organizations-create-msp}

Use a guia **Organizações** para gerar uma definição do MSP para sua organização. Quando você clica em **Criar MSP**, um painel lateral é aberto que permite inserir todas as informações necessárias: sua CA raiz, o ID do MSP e seus administradores da organização.

- Use a seção **Detalhes do MSP** do painel para selecionar seu ID do MSP de organizações. Esse ID é o nome formal que outros membros da rede usarão para se referir à sua organização. ** Salve seu ID do MSP. **

- Use a seção **Detalhes da autoridade de certificação raiz** para selecionar a CA raiz de sua organização. Essa é a CA que você usou (ou usará) para criar todas as identidades do nó e do aplicativo que pertencem à sua organização. Se você usa CAs intermediárias, essa é a CA que foi usada para criar essas CAs. Selecione sua CA raiz na lista de CAs gerenciadas usando o console. Se você criou uma CA usando o console, a seleção de uma CA raiz também criará um Certificado TLS raiz.

- Também é possível usar a seção **Detalhes da autoridade de certificação raiz** para gerar um de seus certificados de administrador de organizações. Antes de criar sua definição do MSP da organização, é necessário registrar seus administradores da organização e do nó com sua CA raiz. Em seguida, é necessário concluir as etapas a seguir para usar essas identidades para operar sua rede:

  1. Crie um par de chaves pública e privada para cada identidade do administrador.
  2. Forneça a chave pública (certificado) de cada identidade de administrador na definição do MSP.
  3. Inclua a identidade em sua carteira eletrônica do console. Os nós ou canais criados pelo console usam os certificados por meio do MSP para verificar quem é um administrador válido. Como resultado, o mesmo par de chaves pública e privada que você usou para incluir um certificado de administrador no MSP precisa ser armazenado dentro de sua carteira eletrônica do console.

  É possível usar o painel **Criar definição do MSP** para concluir essas ações para uma de suas identidades de administrador. Depois de selecionar sua CA raiz, conclua as etapas a seguir na seção **Gerar certificado de administrador da organização**:
  1. Insira o ID de inscrição e o Segredo de inscrição de uma identidade de administrador registrada com sua CA raiz. Depois de inserir o ID de inscrição e o Segredo de inscrição, escolha um nome para exibir a identidade em sua carteira eletrônica do console.
  2. Clique em  ** Gerar **. Isso gerará um novo conjunto de chaves públicas e privadas e incluirá automaticamente as chaves na carteira eletrônica do console. Em seguida, é possível localizar sua identidade de administrador em sua carteira eletrônica usando o nome que você selecionou nesse painel. Essas chaves são armazenadas somente em seu armazenamento local do navegador, portanto, se você mudar os navegadores, eles não estarão na carteira eletrônica do console. Será necessário exportar a identidade de seu navegador original e importá-la para a carteira eletrônica do console de seu novo navegador.
  3. Em seguida, clique em **Exportar** para fazer download do par de chaves em seu sistema de arquivos e protegê-lo.

- A seção **Administradores (Opcional)** do painel lateral contém as chaves públicas de seus administradores. O certificado gerado com o uso da seção **Gerar certificado de administrador da organização** pode ser localizado na primeira linha do campo **certificado do administrador**. Se você desejar usar múltiplas identidades de administrador para operar a sua rede, será possível colar os certificados de administradores do nó ou da organização adicionais nos campos **certificado de administrador**.

Como os seus certificados de administrador são passados para seus nós e canais usando a definição do MSP, é necessário assegurar que cada um de seus certificados de administrador de nó e organização esteja armazenado no MSP. Quando você usa o console para criar um solicitador, peer ou canal, é necessário **Associar** uma das identidades exportadas em sua carteira eletrônica do console com os certificados de administrador que foram fornecidos para a definição do MSP. Quando você encontrar uma seção ou painel **Associar identidade**, selecione uma identidade que foi gerada e salva na carteira ao criar a definição do MSP.

Depois de ter selecionado sua CA raiz, o ID do MSP e criado seus certificados de administrador, clique em **Criar definição do MSP** para criar a definição do MSP. Agora ela é listada como uma organização na guia Organizações. Você usará a definição do MSP quando implementar seus nós e se associar a um consórcio de blockchain.

## Construindo manualmente um arquivo JSON do MSP
{: #console-organizations-build-msp}

**Essa opção é destinada apenas a usuários avançados familiarizados com como os certificados são usados no gerenciamento de identidade de blockchain.**

Ao preferir usar certificados de uma **CA externa** que não seja hospedada pela {{site.data.keyword.IBM_notm}} para o peer ou solicitador, deve-se construir um arquivo JSON de definição do MSP que represente a definição do MSP da organização do peer ou do solicitador.  Crie um arquivo JSON usando o formato a seguir:

```
{
    "name": "<organization_name>",
    "msp_id": "<organization_id>",
    "type": "msp",
    "root_certs": [
        "<root_certs>"
    ],
     "intermediate_certs": [
         "<intermediate_certs>"
     ],
    "admins": [
        "<admins>"
    ],
    "tls_root_certs": [
        "<tls_root_certs>"
    ],
    "tls_intermediate_certs": [
        "<tls_intermediate_certs>"
    ]
}
```
{:codeblock}

- **organization_name**: especifique qualquer nome a ser usado para identificar essa definição do MSP no console.
- **organization_id**: especifique um ID a ser usado para representar esse MSP internamente no console.
- **root_certs**: (opcional) cole uma matriz que contenha um ou mais certificados raiz da CA externa no formato `base64`. Deve-se fornecer um certificado raiz ou intermediário da CA, sendo possível fornecer ambos.
- **intermediate_certs**: (opcional) cole uma matriz que contenha um ou mais certificados da CA intermediária externa no formato `base64`. Deve-se fornecer um certificado raiz ou intermediário da CA, sendo possível fornecer ambos.
- **admins**: cole o signcert do administrador da organização no formato `base64`.
- **tls_root_certs**: (opcional) cole uma matriz que contenha um ou mais certificados raiz da CA do TLS no formato `base64`. Deve-se fornecer um certificado raiz ou intermediário da CA externa do TLS, sendo possível fornecer ambos.
- **tls_intermediate_certs**: (opcional) cole uma matriz que contenha um ou mais certificados da CA intermediária do TLS no formato `base64`. Deve-se fornecer um certificado raiz ou intermediário da CA externa do TLS, sendo possível fornecer ambos.  

Os campos adicionais a seguir também estão disponíveis em sua definição do MSP, mas não são necessários:
- **organizational_unit_identifiers**: uma lista de Unidades Organizacionais (OU) que os membros válidos desse MSP devem incluir em seu certificado X.509. Esse é um parâmetro de configuração opcional usado quando diversas organizações utilizam a mesma raiz de confiança e CAs intermediárias, tendo reservado um campo OU para seus membros. Uma organização é geralmente dividida em diversas unidades organizacionais (OUs), cada uma das quais possui um determinado conjunto de responsabilidades. Por exemplo, a organização ORG1 pode ter as OUs ORG1-MANUFACTURING e ORG1-DISTRIBUTION para refletir essas linhas de negócios separadas. Quando uma CA emite certificados X.509, o campo OU no certificado especifica a linha de negócios à qual a identidade pertence. Consulte este tópico na documentação do Fabric em [Classificação de identidade ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/msp.html#identity-classification "Classificação de identidade") para obter mais informações.  
- **fabric_node_OUs**: OUs específicas do Fabric que ativam a classificação de identidade. `NodeOUs` contêm informações sobre como distinguir clientes, peers e solicitadores com base em sua OU. Se a verificação for cumprida, configurando Ativado como true, o MSP considerará uma identidade válida se ela for de um `client`, um `peer` ou um `orderer`. Uma identidade deve ter apenas uma dessas OUs especiais. Consulte este tópico para obter um exemplo de como especificar o `fabric_node_OU` em um MSP na documentação do [Fabric Service Discovery ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/latest/discovery-cli.html#configuration-query).
- **revocation_list**: uma lista de certificados que não são mais válidos. Para identidades baseadas em X.509, esses identificadores são pares de sequências conhecidas como Identificador de Chave de Assunto (SKI) e Identificador de Acesso à Autoridade (AKI) e são verificados sempre que o certificado X.509 está sendo usado, para garantir que ele não tenha sido revogado. Consulte este tópico na documentação do Fabric para obter mais informações sobre [Listas de revogação de certificado ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html?highlight=revocation%20list#revoking-a-certificate-or-identity "Revogar um certificado ou uma identidade").

Por exemplo, seu arquivo JSON seria semelhante a:

```
{
    "name": "Org1 MSP",
    "msp_id": "org1msp",
    "type": "msp",
    "root_certs": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNGakNDQWIyZ0F3SUJBZ0lVZFhUcWNZVVhRS3U3WHVQWmcxUHBsekpFVFlNd0NnWUlLb1pJemowRUF3SXcKYURFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTS0K"
    ],
    "admins": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlDWHpDQ0FnYWdBd0lCQWdJVVp6NFdQdWwxRXRVOUNIcTl4NFg0Y2QwakNpNHdDZ1lJS29aSXpqMEVBd0l3DQphREVMTUFrR0ExVUVCaE1DVlZNeEZ6QVZCZ05WQkFnVERrNXZjblJvSUVOaGNtOXNhVzVoTVJRd0VnWURWUVFLDQpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbFLS0tLS0NCg=="
    ],
    "tls_root_certs": [
        "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNHRENDQWI2Z0F3SUJBZ0lVTzVhWU9WbjNwTkRMZGVLTFlIanRIUEtNTnY4d0NnWUlLb1pJemowRUF3SXcKWFRFTE1Ba0dBMVVFQmhNQ1ZWTXhGekFWQmdOVkJBZ1REazV2Y25Sb0lFTmhjbTlzYVc1aE1SUXdFZ1lEVlFRSwpFd3RJZVhCbGNteGxaR2RsY2pFUE1BMEdBMVVFQ3hNR1JtRmljbWxqTVE0d0RBWamd5TURRM01EQmFNRjB4Q3pBSkJnTlZCQVlUQWxWVE1SY3cKRlFZRFZRUUlFdztLS0K"
    ]
}
```
{:codeblock}

Observe que todos os certificados devem ser codificados no formato base64.
{:important}

É possível converter o conteúdo de seu arquivo de certificado, `<cert.pem>`, do formato `PEM` para uma sequência base64 ao executar o comando a seguir em sua máquina local:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
cat <cert.pem> | base64 $FLAG
```
{:codeblock}

Salve essa definição como um arquivo `JSON`.  

Você construiu uma definição do MSP, que define a organização para seus nós de peer ou solicitador, que usa certificados de uma CA externa. Agora, é possível retornar às instruções que descrevem [Como usar certificados de uma CA externa com seu peer ou solicitador](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-third-party-ca).

## Importando um MSP
{: #console-organizations-import-msp}

Somente o administrador do solicitador pode incluir novas organizações no consórcio. Se você for o administrador do solicitador, será necessário coletar as definições do MSP de todas as organizações que foram convidadas para o consórcio e importar os MSPs para o console. Em seguida, será possível incluir os MSPs no serviço de pedido usando o nó do solicitador.

Depois que um administrador cria uma definição do MSP, ele pode usar a guia Organizações para fazer download do MSP no formato JSON para seu sistema de arquivos local. Ele podem, então, enviar o arquivo JSON do MSP em uma operação fora da banda. Navegue para a guia **Organizações** e use **Importar definição do MSP** para importar o arquivo MSP para seu console. Quando uma definição do MSP estiver visível na seção **Organizações disponíveis**, será possível navegar para seu nó do solicitador para [incluir a organização no consórcio](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-add-consortium).


## Incluindo uma organização em um consórcio
{: #console-organizations-add-consortium}

O consórcio de organizações é hospedado pelo serviço de pedido.

Se você for o administrador do serviço de pedido, será possível usar o console para incluir uma organização no consórcio. Navegue para a guia **Nós** e clique no nó de pedido. No painel do nó de pedido, em **Membros do consórcio**, clique em **Incluir organização**. Isso abrirá um painel lateral que permitirá selecionar dentre a lista de definições do MSP disponíveis que você [importou para a guia de organizações](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-import-msp). Também será possível usar a opção **Fazer upload de JSON** para importar o arquivo de definição do MSP criado por outra organização diretamente.

## Criando e editando um canal
{: #console-organizations-create-channel}

Após uma organização ser incluída no consórcio, ela pode usar o serviço de pedido para criar um novo canal ou pode ser incluída em um canal existente. As informações que permitem que você participe de um canal, como associar seus peers ao canal, instanciar contratos inteligentes e enviar transações, são fornecidas usando as definições do MSP.

Depois que sua organização for incluída em um consórcio, será possível criar um canal usando as etapas a seguir:

1. Importe o nó de pedido que hospeda o consórcio em seu console. Não é necessário ser um administrador do nó de pedido, mas é necessário fornecer as informações sobre o nome do nó e do terminal do solicitador em seu console.
2. Importe os MSPs de organizações que você deseja incluir no novo canal em seu console na guia **Organizações**. **Observe** que as organizações precisam ser incluídas no consórcio antes que possam ser incluídas em um canal.
3. Navegue para a guia **Canais** e clique em **Criar canal**. Isso abrirá um painel lateral que permite especificar o nome do canal, a associação e as políticas de canal. É possível incluir quaisquer organizações que tenham sido incluídas no consórcio para o novo canal.

Para obter mais informações sobre essas etapas, consulte [Criando um canal](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-create-channel1) no tutorial **Construir uma rede**.

### Atualizando um MSP em uma definição de canal
{: #console-organizations-update-channel}

Com o tempo, pode ser necessário atualizar os certificados em uma definição do MSP que já esteja associada a um canal. Quando essa situação ocorrer, siga estas etapas para atualizar a definição do MSP de uma organização no canal:  

1. Navegue até a guia **Canais** em seu console.
2. Clique no canal que contém o MSP da organização que deseja atualizar e abra-o.
3. Clique na guia **Detalhes do canal**.
4. Clique no bloco do membro do canal associado que desejar atualizar.
5. Se você ainda não tiver importado a definição do MSP atualizada para o console, será possível fazer upload do arquivo aqui. **Nota:** essa ação não atualizará a definição do MSP associada na guia Organizações. Se você já tiver atualizado a definição do MSP na guia Organizações do console, será possível selecioná-la na lista suspensa.
