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
{:important: .important}
{:note: .note}
{:pre: .pre}

# Criando e gerenciando identidades
{: #ibp-console-identities}

Os nós do {{site.data.keyword.blockchainfull_notm}} Platform são baseados no Hyperledger Fabric e constroem redes de blockchain permissionárias. Isso significa que todos os participantes do consórcio de blockchain precisam ter identidades que são continuamente verificadas pela Infraestrutura de chave pública. O console do {{site.data.keyword.blockchainfull_notm}} Platform permite criar essas identidades usando as autoridades de certificação (CAs) de sua organização. É necessário armazenar essas identidades em sua carteira eletrônica do console para que você possa usá-las para operar sua rede.

**Público-alvo:** este tópico é projetado para operadores de rede que são responsáveis por criar, monitorar e gerenciar a rede de blockchain.

## Gerenciando autoridades de certificação
{: #ibp-console-identities-manage-ca}

Uma CA é semelhante a um tabelião publicamente confiável que atua como âncora de confiança entre múltiplas partes, com cada organização em um consórcio mantendo sua própria CA. Sua CA cria as identidades que pertencem à sua organização e emite para cada identidade uma chave pública e privada. Essas chaves são o que permitem que todos os nós e aplicativos assinem e verifiquem suas ações. Para obter mais detalhes sobre como as CAs são usadas para estabelecer identidade, visite [o tópico de identidade ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/identity/identity.html "identidade") na documentação do Hyperledger Fabric.

Quando você cria uma CA usando o console do {{site.data.keyword.blockchainfull_notm}} Platform, duas CAs são criadas com a mesma URL de terminal: uma CA raiz e uma CA TLS. É possível visualizar ambas as CAs sob o mesmo nome de exibição na página **Nós** de seu console. A CA raiz fornece chaves para seus nós e aplicativos. A CA TLS fornece certificados usados para proteger a comunicação dentro de sua rede. Para saber mais sobre o TLS em sua rede, veja [Usando sua CA TLS](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-tlsca).

Quando você cria seus nós, é necessário usar sua CA raiz para criar as identidades a seguir que são necessárias para implementar, operar e interagir com sua rede:
- **Administrador de CA:** sua CA contém uma identidade de administrador de CA padrão. Essa identidade tem um ID de inscrição e um segredo, que são análogos a um nome do usuário e senha, que você especifica durante a implementação de sua CA. É possível usar esse nome do usuário administrativo e senha para operar sua CA e criar novas identidades. Se você criou uma CA usando o console, o administrador de CA de sua CA raiz também é o administrador da CA TLS, a menos que decida criar um administrador de CA TLS separado.
- **Peers ou solicitadores:** você precisa registrar uma identidade para cada um dos peers e solicitadores que pertencem à sua organização. Em seguida, seus nós usarão essas identidades durante a implementação para gerar as chaves que eles precisam para participar da rede. Por segurança, crie um ID e um segredo de inscrição exclusivos para cada nó que você implementar.
- **Administradores de peer ou solicitador:** os nós do {{site.data.keyword.blockchainfull_notm}} Platform são implementados com as chaves públicas de identidades de administradores de componentes dentro deles. Esses certificados permitem que os administradores operem o componente por meio de um cliente remoto ou usando o console.
- **Administradores de organização:** ao se associar a um consórcio hospedado por um serviço de pedido, você fornece as chaves públicas de identidades que se tornarão os administradores para sua organização. É possível usar essas identidades para criar ou editar canais.
- **Aplicativos:** seus aplicativos precisam assinar suas transações antes de enviá-las para serem validadas pela rede. É necessário criar identidades que podem ser usadas para assinar transações de seus aplicativos clientes.

É possível usar o console para criar essas identidades usando o [processo de registro](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register). Depois de registrar suas identidades de administrador, é necessário emitir para cada identidade uma chave privada e pública, fornecer a chave pública para sua definição do MSP da organização e incluir a identidade na carteira eletrônica do console. É possível concluir essas etapas para uma identidade de administrador ao [criar seu MSP da organização](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp). É possível usar identidades separadas como administradores de organização ou administradores de nó ou é possível usar uma identidade para executar ambas as tarefas. O [tutorial Construir uma rede](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) usa uma identidade para ser um administrador para cada organização criada no tutorial.

## Configurando uma identidade de CA
{: #ibp-console-identities-ca-identity}

Antes de trabalhar com identidades, é necessário configurar a identidade do administrador de CA, usando a identidade de administrador criada durante a criação da CA ou estabelecendo uma nova. Abra a CA na guia **Nós**. O ID de inscrição da identidade atualmente ativa está visível ao lado do nome da CA na parte superior do painel. É possível usar essa identidade de administrador para criar outras identidades usando o botão **Registrar** para registrar identidades que se tornarão administradores de organização e identidades de nó ou para usar o botão **Inscrever** para gerar uma identidade e exportá-la para a carteira eletrônica.

Para alternar para uma identidade diferente para ser usada como o administrador da autoridade de certificação, clique no ícone **Configurações** e, em seguida, clique em **Configurar identidade** na régua de controle. É possível especificar uma identidade que exista na carteira eletrônica usando a guia **Identidade existente**. Como alternativa, é possível usar a guia **Nova identidade** para fazer upload de um arquivo JSON, base64 ou PEM que contenha os certificados para um novo administrador.

Nem todas as identidades têm a capacidade de registrar novos usuários. Para obter mais informações, incluindo como estabelecer um administrador de CA adicional, veja [ Criando novos administradores de CA](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-admin).
{: note}

## Registrando identidades
{: #ibp-console-identities-register}

A primeira etapa na criação de uma identidade é conhecida como **registro**. Durante o registro, um ID de inscrição e um segredo são criados, que podem, então, ser usados por um nó ou um administrador da organização para **inscrever** essa identidade gerando uma chave pública e privada.

É necessário inserir as informações a seguir quando registrar uma nova identidade com sua CA.

- **ID de inscrição** e **Segredo de inscrição**: essa identidade tem um ID e um segredo, que são análogos a um nome do usuário e senha, especificados durante a implementação de sua CA. É possível usar esse ID de administrador e segredo para criar certificados com essa identidade usando esse console ou o cliente Fabric CA.
- **Tipo**: quando a CA foi implementada, o administrador especificou os tipos de identidades emitidas pela CA. Exemplos comuns de tipos de identidade incluiriam o peer, o solicitador e o cliente (aplicativos). Selecione o tipo de identidade para esse usuário na lista de tipos disponíveis.
- **Afiliação**: a parte de sua organização que você deseja afiliar a esse usuário. Por exemplo, isso pode ser um departamento ou unidade que opera um aplicativo ou um peer. É possível limitar um determinado administrador de CA para somente ser capaz de registrar usuários em seu próprio departamento com uma organização.
- **Inscrições máximas**: opcionalmente, insira o número de vezes que é possível inscrever ou gerar certificados usando essa identidade. Especificar um número limitado de inscrições ajuda a proteger a segurança de seus nós e aplicativos. Ela é padronizada para cadastramentos ilimitados.
- **Atributos**: opcionalmente, é possível especificar quaisquer atributos de [controle de acesso baseado em atributo ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#attribute-based-access-control "Controle de acesso baseado em atributo") para o usuário. Por exemplo, é possível usar essa seção para [criar outro administrador de CA](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity) com autoridade para registrar e inscrever novas identidades. É possível ver uma lista completa de atributos do Fabric CA disponíveis na seção [Registrando uma nova identidade ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity "Registrando uma nova identidade") do guia de usuários do Fabric CA.

Clique no botão **Registrar usuário** no painel de visão geral de CA para criar um novo usuário. Certifique-se de que você tenha [configurado a identidade](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity)
usando uma identidade que tenha a capacidade de registrar novos usuários antes de tentar essa tarefa. Em geral, esse é o usuário `admin`. Se o botão for cinza, isso significa que você não configurou uma identidade ou que a identidade não pode criar novas identidades.  

Clicar em **Registrar usuário** abrirá uma série de painéis laterais:
1. No primeiro painel lateral, insira o **ID de inscrição** e o **Segredo de inscrição** da nova identidade. **Salve esses valores**, uma vez que eles não são armazenados pelo console.
2. No segundo painel lateral, selecione a identidade **Tipo**. A lista suspensa contém a lista de tipos que essa CA suporta. Em seguida, selecione a **Afiliação** à qual a nova identidade pertencerá. É possível escolher uma afiliação existente na lista suspensa ou digitar uma nova.
3. No terceiro painel lateral, insira o **Máximo de inscrições** permitido para essa identidade. Se não especificado, o valor será padronizado para inscrições ilimitadas.
4. No último painel lateral, inclua os **Atributos** da identidade que você está criando.

Depois de clicar em **Registrar**, a nova identidade será incluída na lista de **Usuários autenticados** que foram criados por sua CA. As identidades são listadas por seu **ID de inscrição**, juntamente com seu **Tipo** e **Afiliação**. Clicar em uma identidade na tabela abrirá um painel lateral que permite visualizar o número de **Máximo de inscrições** e **Atributos** que foram criados durante o registro.

### Criando novos administradores de CA
{: #ibp-console-identities-ca-admin}

Por padrão, somente o administrador de CA criado durante a implementação tem a capacidade de registrar novas identidades. Também é possível criar outras identidades com a capacidade de registrar novos usuários usando o painel **Atributos** do processo de registro.

No Painel lateral 4, clique no botão **Incluir atributo**. Forneça um  ** nome do atributo **  de  ` hf.Registrar.Roles `. Insira um  ** valor de atributo **  de  ` * `. Também é possível usar esse painel para criar uma identidade que possa registrar somente determinados tipos de identidade, como clientes ou peers, ou dentro de uma determinada afiliação. Também é possível criar uma identidade que tenha a capacidade de revogar uma identidade e todos os certificados para os quais a identidade foi emitida. É possível ver uma lista integral dos atributos na seção [Registrando uma nova identidade ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric-ca.readthedocs.io/en/release-1.4/users-guide.html#registering-a-new-identity "Registrando uma nova identidade") do guia de usuários do Fabric CA.

## Inrolando uma identidade
{: #ibp-console-identities-enroll}

É possível gerar o certificado público e a chave privada para cada identidade que tenha sido registrada com a sua autoridade de certificação. Se você tiver registrado identidades administrativas adicionais com a sua autoridade de certificação, será possível gerar as chaves para a identidade do administrador e, em seguida, incluí-la adicionalmente quando você [criar seu MSP da organização](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp).

Antes de inscrever uma identidade, é necessário [configurar a identidade](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-ca-identity) para ser capaz de operar a autoridade de certificação. Normalmente, você a configura para a identidade de administrador que foi especificada quando você criou a autoridade de certificação. É possível confirmar que a sua autoridade de certificação está configurada para essa identidade examinando a página de detalhes da autoridade de certificação e visualizando o ID de inscrição da identidade atualmente ativa ao lado do nome da autoridade de certificação. Depois de confirmar que a identidade está configurada para a sua identidade de administrador, clique em **Inscrever identificar** no menu overflow do usuário para gerar o certificado e a chave para qualquer usuário registrado com a autoridade de certificação.

- Insira o  ` Segredo de enrolo do usuário `.
- Na próxima etapa, as chaves geradas serão exibidas.
  - A chave pública é exibida no campo **Certificado**. Esse certificado também é referido como certificado de inscrição, certificado de assinatura ou signCert. É necessário exportar o signCert para um arquivo em seu sistema local para que ele possa ser usado ao criar um aplicativo cliente com a extensão VSCode.
  - É possível localizar a chave privada correspondente no campo **Chave privada**. Novamente, é necessário exportar a chave privada para o seu sistema local para uso com um aplicativo cliente criado com a extensão VSCode.
  - O certificado e a chave privada criados clicando em **Inscrever identidade** são gerados somente uma vez e não armazenados pelo console ou pelo seu navegador. Clicar no botão **Inscrever identidade** também será contado com relação ao número máximo de inscrições que você configurou para o administrador de CA. Como parte dessa inscrição, é necessário armazenar o par de chaves fazendo download da identidade para seu sistema de arquivos local ou incluindo-a em sua carteira eletrônica do console. Insira um novo nome para esse par de chaves pública e privada no campo **Nome** para recuperá-las.
- **Importante:** Clique em **Exportar identidade** para fazer download do certificado e da chave para o sistema de arquivos local como um único arquivo no formato JSON. Você é responsável por proteger e gerenciar essas chaves.
- Clique em **Incluir identidade na carteira eletrônica** para incluir esses certificados na carteira eletrônica do console. É possível, então, localizar o nome e as chaves dessa identidade em um novo tile em sua [guia de carteira eletrônica](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-wallet)

Também é possível usar o cliente Fabric CA ou os SDKs do Fabric para inscrever as identidades criadas no console. O console fornece todas as informações que serão necessárias para concluir essas etapas. Assegure-se de ter salvado o **ID de inscrição** e o **Segredo de inscrição** que você especificou durante o registro.

## Usando sua CA TLS
{: #ibp-console-identities-tlsca}

A comunicação dentro de sua rede é assegurada por certificados TLS. O TLS criptografa a comunicação entre seus nós e entre seus nós e seus aplicativos. O uso de TLS evita que os invasores interrompam a comunicação entre seus nós ou leiam as transações enviadas de seus aplicativos. As chaves e os certificados usados para TLS são diferentes dos certificados usados para assinar e validar suas transações e são emitidos por uma Autoridade de certificação separada.

Cada CA criada pelo console do {{site.data.keyword.blockchainfull_notm}} Platform contém uma CA raiz e uma CA TLS. É possível visualizar ambas as CAs sob o mesmo nome de exibição na guia de nós de seu console. Clique em seu painel de visão geral da autoridade de certificação e, em seguida, clique em **Autoridade de certificação TLS** para operar a sua autoridade de certificação TLS. Sua CA TLS tem o [mesmo registro](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-register) e [processo de inscrição](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll) que sua CA raiz. Ambas as CAs são implementadas com o mesmo ID de administrador e segredo de CA.

Cada nó de peer ou solicitador que você implementa precisa gerar um certificado TLS público. Ao criar um nó usando o console, é solicitado que você forneça um ID de inscrição e o segredo de uma identidade registrada com sua CA TLS. O [tutorial Construir sua rede](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network) usa a identidade de administrador de CA por conveniência. No entanto, a melhor prática é registrar uma identidade exclusiva com sua CA TLS para cada nó. O nó usará essa identidade para gerar seu certificado TLS durante a implementação. Esse certificado será requerido por qualquer aplicativo que precise se comunicar com o solicitador ou peer. É possível localizar o certificado TLS de um nó navegando até o painel de visão geral do nó e clicando em Configurações. Também é possível localizar os Certificados TLS de seus peers e solicitadores no perfil de conexão que podem ser transferidos por download [por meio da guia de contratos inteligentes](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-connect-to-SDK).

Ao criar um peer ou solicitador usando o console, também é possível usar a CA TLS para especificar um nome de domínio adicional para cada nó. Insira o novo nome de domínio no campo **Nome do host de CSR TLS** ao implementar seu solicitador ou peer. Esse nome do host será incluído na lista de nomes comuns no certificado TLS emitido para seu nó.


## Expiração do certificado
{: #ibp-console-identities-expiration}

Os certificados gerados usando o {{site.data.keyword.blockchainfull_notm}} Platform 2.0 CAs expirarão após um ano. O período de expiração é o mesmo para certificados que são gerados usando os SDKs do Fabric, o cliente Fabric CA ou usando o console. Se os certificados expirarem, seus aplicativos não poderão mais interagir com sua rede e você precisará se inscrever novamente para gerar novos certificados. Se você tiver atingido o limite de inscrição de um usuário, será possível registrar um novo usuário e, em seguida, inscrever.

É possível usar a linha de comandos para verificar a data de expiração de seus certificados. Primeiro, você precisará converter os certificados que estão em base64 em formato PEM executando o comando a seguir em sua máquina local:

```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi) cat $HOME/<path-to-certificate>/cert.pem | base64 $FLAG
```
{:codeblock}

Execute o comando a seguir para exibir o certificado codificado pelo PEM em um formato legível:
```
openssl x509 -in <certificate file> -text
```
{:codeblock}

O certificado será semelhante ao exemplo a seguir:

```
Certificate:
    Data:
        Version: 3 (0x2)
    Número de série: 20:3d:3e:c5:31:4f:85:7a:30:9f:b5:67:47:3d:b0:10:70:80:f6:18 Algoritmo de assinatura: ecdsa-with-SHA256 Emissor: C=US, ST=North Carolina, O=Hyperledger, OU=Fabric, CN=fabric-ca-server-org1CA Validade não antes de: 28 de nov 19:18:00 2018 GMT Não após: 28 de nov 19:23:00 2019 GMT Assunto: C=US, ST=North Carolina, O=Hyperledger, OU=peer, OU=org1, CN=1peeradmin...
    ...
```

É possível localizar a data de expiração na seção **Validade** e segue `Not After:`. Neste exemplo, o certificado expirará em 28 de novembro de 2019.

## Armazenando identidades em sua carteira eletrônica do console
{: #ibp-console-identities-wallet}

A carteira eletrônica armazena as identidades e chaves que o console do {{site.data.keyword.blockchainfull_notm}} Platform usa para operar os nós de sua rede. É necessário incluir seus administradores de peer, solicitador e organização nessa carteira eletrônica antes de poder usar o console para trabalhar com canais e contratos inteligentes. Também é possível usar a carteira eletrônica para armazenar convenientemente as identidades que serão usadas por seus aplicativos. É possível usar a carteira eletrônica para exportá-las a qualquer momento. Use a navegação à esquerda para procurar o painel de visão geral de carteira eletrônica. É possível incluir, atualizar e exportar identidades nessa carteira eletrônica usando a tela de visão geral.

A carteira eletrônica é um componente do console e não um serviço separado. Ela armazena suas chaves no armazenamento local do navegador que você usa para acessar o console em vez de seu sistema de arquivos local. Se você acessar seu console por meio de um navegador diferente ou iniciar uma sessão de navegação privada, não será possível acessar as identidades armazenadas na carteira eletrônica. **É recomendável exportar suas identidades de administrador por meio do console e armazená-las em um local seguro**.
{:important}

### Incluindo identidades
{: #ibp-console-identities-add}

É possível incluir uma identidade de administrador em sua carteira eletrônica ao [criar seu MSP da organização](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp). Uma CA gerenciada pelo console também pode incluir uma identidade em sua carteira eletrônica durante o [processo de inscrição](/docs/services/blockchain/howto/ibp-console-identities.html#ibp-console-identities-enroll).

É possível usar o botão **Incluir identidade** na tela de visão geral para importar uma identidade diretamente para a carteira eletrônica. Clicar nesse botão abrirá um painel lateral que permite incluir o par de chaves pública e privada de uma identidade.
- **Nome:** insira um nome de identidade que é usado somente para sua referência.
- **Certificado:** faça upload de um arquivo que contenha a chave pública da identidade (no formato base64 ou PEM) gerada usando sua CA.
- **Chave privada:** faça upload de um arquivo que contenha a chave privada da identidade (no formato base64 ou PEM) gerada usando sua CA.


Também é possível incluir uma identidade fazendo upload de um arquivo JSON no formato abaixo. Também é possível usar o botão **Fazer upload do JSON** para fazer upload de múltiplas identidades de uma vez.

```
{
    "name": "peerAdminCerts",
    "private_key": "LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tDQpNSUdIQWdFQU1CTUdCeXFHU000OUFnRUdDQ3FHU000OUF3RUhCRzB3YXdJQkFRUWdIVk5Kc0tHYnl6eTVZWEQ2DQovaEhKOVZlR0QrZVhmenpxUi9PQWVZYnY5UWloUkFOQ0FBUmZ1WlB1OTRzNnJQSEVCMjJlWFhpSWozd0lKYkg4DQpRYVpaRkJlNFp5SnU5UllHbkxQWUdLRnhETkRVMUZkU1NxUGNLcnczUXpxT3hXNU1NZDVWbEtVdw0KLS0tLS1FTkQgUFJJVkFURSBLRVktLS0tLQ0K",
    "cert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlCNURDQ0FZdWdBd0lCQWdJVUhhUFNNdlcxOEwyaGNTeGlJK1ZqT1d0WnlyZ3dDZ1lJS29aSXpqMEVBd0l3YTHJNM1o5TUZicGt3SGthYnB0MmZBekFLQmdncWhrak9QUVFEQWdOSEFEQkVBaUFmdUhjY2R6WExqZ3BLDQplVFhnNmNNcnRzRUs4ZVlKTVFMNlZLU0xwUXIvN3dJZ1hyK2ZuVjUrb2RHWnNRUU94SjZ1aDVTV0tJVkdZQ2ZGDQp1Ykw4VmJNdnRRZz0NCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0NCg=="
}
```
{:codeblock}

Se você inscreveu uma identidade usando o cliente Fabric CA ou os SDKs do Fabric, suas chaves precisam ser convertidas do formato PEM para o formato base64. É possível converter os certificados no formato base64 executando o comando a seguir em sua máquina local:
```
export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi) cat $HOME/<path-to-certificate>/cert.pem | base64 $FLAG
```
{:codeblock}

### Visualizando e atualizando identidades
{: #ibp-console-identities-update-identities}

Na guia **Carteira eletrônica**, clique em um tile para visualizar, atualizar ou remover uma identidade da carteira eletrônica. Pode ser necessário atualizar suas identidades se seus certificados tiverem expirado e eles precisarem que novas chaves sejam emitidas por meio da CA. Também é possível usar essa guia para excluir chaves de seu console e de seu sistema local.

Clicar em uma identidade abre um painel lateral que exibe as suas chaves públicas e privadas no formato base64. Clique em **Exportar** para fazer download dos certificados da identidade para o seu sistema de arquivos local. Clique em **Atualizar** para mudar o nome da identidade na carteira eletrônica ou colar um novo conjunto de chaves no painel. Clique em **Remover** quando você não precisar mais usar essa identidade e desejar excluir suas chaves.

## Associando identidades
{: #ibp-console-identities-associate-admin}

É necessário fornecer a chave pública de seus administradores de organização e de nó [para a definição do MSP da organização](/docs/services/blockchain/howto/ibp-console-organizations.html#console-organizations-create-msp). Os nós ou canais criados pelo console usam os certificados da definição do MSP para verificar quem é um administrador válido. Como resultado, o mesmo par de chaves privada e pública que você usou para incluir um certificado de administrador na definição do MSP precisa ser armazenado dentro de sua carteira eletrônica do console.

Quando usar o console para criar um solicitador ou peer, você encontrará um painel **Associar identidade**. Selecione uma identidade na carteira eletrônica cujo certificado também está dentro de sua definição do MSP da organização. Você também precisará selecionar uma identidade de administrador na seção **Associar identidade** quando criar ou editar um canal. Isso permitirá que o console saiba qual identidade usar quando se comunicar com seus peers, solicitadores e consórcio de serviço de pedido.
