---

copyright:
  years: 2017, 2019
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
{:pre: .pre}

# Implementando peers no {{site.data.keyword.cloud_notm}} Private e conectando-se ao Starter Plan ou ao Enterprise Plan
{: #ibp-peer-deploy}

As instruções a seguir descrevem como implementar um peer do {{site.data.keyword.blockchainfull}} Platform no {{site.data.keyword.cloud_notm}} Private e conectar o peer à rede do Starter Plan ou Enterprise Plan no {{site.data.keyword.cloud_notm}} ou em seu {{site.data.keyword.cloud_notm}} Private local.
{:shortdesc}

Antes de implementar um peer, revise as [Considerações e limitações](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations).

Sua rede do Starter Plan ou do Enterprise Plan deve estar executando o Hyperledger Fabric v1.1 ou v1.2.1. É possível localizar a versão do Hyperledger Fabric abrindo a [janela Preferências de rede](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) em seu Network Monitor.

## Recursos necessários
{: #ibp-peer-deploy-resources-required}

Assegure-se de que seu sistema {{site.data.keyword.cloud_notm}} Private atenda aos requisitos mínimos de recurso de hardware:

| Componente | vCPU | RAM | Disco para armazenamento de dados |
|-----------|------|-----|-----------------------|
| Peer | 2 | 2 GB | 50 GB com a capacidade de expandir |
| CouchDB para peer | 2| 2 GB |50 GB com a capacidade de expandir |

 **Notas:**
 - Um vCPU é um núcleo virtual que será designado a uma máquina virtual ou a um núcleo do processador físico se o servidor não for particionado para máquinas virtuais. É necessário considerar os requisitos de vCPU ao decidir o virtual processor core (VPC) de sua implementação no {{site.data.keyword.cloud_notm}} Private. A VPC é uma unidade de medida para determinar o custo de licenciamento de produtos IBM. Para obter mais informações sobre os cenários para decidir o VPC, consulte [Virtual processor core (VPC) ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html).
 - Esses níveis de recursos mínimos são suficientes para teste e experimentação. Para um ambiente com um grande volume de transações, é importante alocar uma quantia suficientemente grande de armazenamento; 250 GB para seu peer como um exemplo. A quantidade de armazenamento a ser usada depende do número de transações e do número de assinaturas que são necessárias de sua rede. Se você estiver prestes a esgotar o armazenamento em seu peer, deverá implementar um novo peer com um sistema de arquivos maior e permitir que ele sincronize por meio de outros componentes nos mesmos canais.

## Armazenamento
{: #ibp-peer-deploy-storage}

É necessário determinar o armazenamento que seu peer usará. Se você usar as configurações padrão, o gráfico do Helm criará uma nova Solicitação de Volume Persistente (PVC) de 8 Gi com o nome de `my-data-pvc` para seus dados do peer e outra PVC de 8 Gi com o nome de `statedb-pvc` para seu banco de dados de estado.

Se você não deseja usar as configurações de armazenamento padrão, assegure-se de que uma *nova* `storageClass` seja configurada durante a instalação do {{site.data.keyword.cloud_notm}} Private ou o administrador do sistema do Kubernetes precisará criar uma storageClass antes da implementação.

É possível escolher implementar o peer nas plataformas amd64 ou s390x. No entanto, esteja ciente de que o [Fornecimento dinâmico ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/ "Fornecimento dinâmico") está disponível somente para nós amd64 no {{site.data.keyword.cloud_notm}} Private. Se o cluster incluir uma combinação de nós do trabalhador s390x e amd64, o fornecimento dinâmico não poderá ser usado.

Se você não usar o fornecimento dinâmico, [Volumes persistentes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/ "Volumes persistentes") deverão ser criados e configurados com rótulos que podem ser usados para refinar o processo de ligação do PVC do Kubernetes.

## Pré-requisitos para implementar um peer
{: #ibp-peer-deploy-prerequisites}

1. Antes de ser possível instalar um peer no {{site.data.keyword.cloud_notm}} Private, deve-se [instalar o {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup) e [instalar o gráfico do Helm do {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

2. Se você usar a Community Edition e desejar executar esse gráfico do Helm em um cluster do {{site.data.keyword.cloud_notm}} Private sem conectividade com a Internet, será necessário criar archives em uma máquina conectada à Internet antes de ser possível instalar os archives no cluster do {{site.data.keyword.cloud_notm}} Private. Para obter mais informações, veja [Incluindo aplicativos de destaque em clusters sem conectividade à Internet ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_package_offline.html "Incluindo aplicativos de destaque em clusters sem conectividade à Internet"){:new_window}. Observe que é possível localizar o arquivo de especificação manifest.yaml em ibm-blockchain-platform-dev/ibm_cloud_pak no gráfico Helm.

3. Deve-se ter uma organização que seja membro de uma rede Starter Plan ou Enterprise Plan no {{site.data.keyword.cloud_notm}}. O peer alavanca os terminais de API, autoridades de certificação do Hyperledger Fabric e Serviço de Ordenação da rede do {{site.data.keyword.blockchainfull_notm}} Platform para operar. Se você não for membro de nenhuma rede de blockchain, precisará criar ou associar-se a uma rede. Para obter mais informações, consulte [Criando uma rede](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-create-network) ou [Associando-se a uma rede](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-join-nw).

4. Deve-se primeiro [implementar uma CA](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy) no {{site.data.keyword.cloud_notm}} Private. Você estará usando essa CA como uma CA TLS. Siga as [etapas de pré-requisito](/docs/services/blockchain/howto/CA_operate.html#ca-operate-prerequisites) para operar uma CA no {{site.data.keyword.cloud_notm}} Private antes de implementar seu peer. Você não precisará prosseguir além dessas etapas.

5. Recupere o valor do endereço IP do Proxy do cluster de sua CA TLS por meio do console do {{site.data.keyword.cloud_notm}} Private. **Nota:** é necessário ser um [Administrador do cluster ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.0/user_management/assign_role.html "Funções e ações do administrador de cluster") para acessar seu IP de proxy. Efetue login no cluster do {{site.data.keyword.cloud_notm}} Private. No painel de navegação à esquerda, clique em **Plataforma** e, em seguida, em **Nós** para visualizar os nós que estão definidos no cluster. Clique no nó com a função `proxy` e, em seguida, copie o valor do `IP do host` da tabela. **Importante:** salve esse valor e você o usará ao configurar o campo `Proxy IP` do gráfico do Helm.

6. Crie um arquivo de configuração de peer e armazene-o como um segredo do Kubernetes no {{site.data.keyword.cloud_notm}} Private. É possível localizar as etapas para criar esse arquivo na [próxima seção](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file).

## Construindo o arquivo de configuração
{: #ibp-peer-deploy-config-file}

Antes de implementar um peer, é necessário criar um arquivo JSON de configuração que contenha informações importantes sobre a identidade do peer e sua autoridade de certificação no {{site.data.keyword.cloud_notm}}. Em seguida, é necessário passar esse arquivo para o gráfico do Helm durante a configuração usando um objeto de [Segredo do Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/configuration/secret/ "Segredos"). Esse arquivo permite que seu peer obtenha os certificados que ele precisa da autoridade de certificação no {{site.data.keyword.cloud_notm}} para associar-se a uma rede do Starter Plan ou do Enterprise Plan. Esse arquivo também contém um certificado de administrador que permite operar seu peer como um usuário administrador.

Nós forneceremos a você um modelo JSON nessas instruções que você poderá editar e salvar em seu sistema de arquivos local. Em seguida, nós mostraremos a você como usar a CA para concluir o arquivo de configuração.

### Arquivo de configuração

O modelo para o arquivo de configuração pode ser localizado abaixo:
```
{
	"enrollment": {
		"component": {
			"cahost": "", 			"caport": "", 			"caname": "", 			"catls": {
				"cacert": ""
			}, 			"enrollid": "", 			"enrollsecret": "", 			"admincerts": [""]
		}, 		"tls": {
			"cahost": "", 			"caport": "", 			"caname": "", 			"catls": {
				"cacert": ""
			}, 			"enrollid": "", 			"enrollsecret": "", 			"csr": {
				"hosts": [""] }
		}
	}
}
```
{:codeblock}

Copie esse arquivo inteiro em um editor de texto no qual é possível editá-lo e salvá-lo em seu sistema de arquivos local como um arquivo JSON. Será necessário preencher somente as duas primeiras seções desse arquivo: `"enrollment"` e `"tls"`.

Para concluir o arquivo de configuração, é necessário concluir várias etapas do Monitor de Rede de sua rede do Starter Plan ou do Enterprise Plan.

1. [Recupere as informações de terminal de sua CA do Starter Plan ou do Enterprise Plan](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-ibp-ca-endpoint).
2. [Registre o peer com sua CA](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-peer).
3. [Registre um administrador de peer](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin) que você usará para operar seu peer. Não é necessário concluir essa etapa se já tiver registrado um administrador para implementar outro peer.

Também é necessário concluir várias etapas usando o cliente Fabric CA e sua CA TLS implementada no {{site.data.keyword.cloud_notm}} Private.

1. Use o cliente Fabric CA para [gerar a pasta MSP do administrador de peer](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-enroll-admin).
2. [Recupere as informações de terminal de sua CA TLS](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-tls-ca-endpoint).
3. Use o cliente Fabric CA para [registrar o peer com sua CA TLS](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-tls-register-peer).


### Informações de CA do Starter Plan ou do Enterprise Plan
{: #ibp-peer-deploy-ibp-ca-endpoint}

Primeiro, é necessário fornecer as informações de conexão de sua CA no {{site.data.keyword.cloud_notm}} para o arquivo de configuração. Efetue login na IU do Monitor de Rede no Starter ou no Enterprise Plan. Na tela **Visão geral** de seu Monitor de rede, clique no botão **Configuração do peer remoto**. Isso abrirá um pop-up contendo as informações necessárias sobre sua CA.

![Configuração do peer remoto](../images/myresources_starter.png "Configuração do peer remoto")
*Figura 1. Painel de configuração de peer remoto*

A janela pop-up contém os campos a seguir:

  - ** MSP da Organização **
  - ** Nome da Autoridade de Certificação (CA) **
  - ** URL da Autoridade de Certificação (CA) **
  - ** Certificado TLS da Autoridade de Certificação (CA) **

No arquivo sob a seção `"components"`, insira os valores a seguir acima:
- `"caname"` é o valor do **Nome da autoridade de certificação (CA)**
- `"cahost"` é o nome do host da URL da CA. Por exemplo, se a URL da CA for `https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011`, o valor do `"cahost"` será `ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com`
- `"caport"` é a porta do `"cahost"`. Por exemplo, se a URL da CA for `https://ncaca9b06047b4bee966b3dec0cbb6671-org1-ca.stage.blockchain.ibm.com:31011`, a `"caport"` será `31011`.
- `"cacert"` é o valor do campo **Certificado TLS da autoridade de certificação (CA)**. Antes de inserir o certificado no arquivo, é necessário codificá-lo para o formato base64, executando os comandos a seguir e substituindo a sequência `<paste in Certificate Authority (CA) TLS Certificate>` pelo valor que você copiou de seu Monitor de Rede.

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo -e '<paste in Certificate Authority (CA) TLS Certificate>' | base64 $FLAG
  ```
  {:codeblock}

  **Nota:** é importante que a sequência gerada pelo comando acima seja formatada como uma linha única. Isso deve ser semelhante a:

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
  ```

  Mas não assim:
  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
  ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
  Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
  VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
  WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
  ```

Cole a sequência resultante no campo `"cacert"` em `"catls"` no arquivo. Após a atualização, o campo `"cacert"` é semelhante ao exemplo a seguir:

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

### Registre o peer
{: #ibp-peer-deploy-register-peer}

Para associar seus peers aos canais e instalar e instanciar o chaincode, é necessário primeiro registrar o peer com sua CA no {{site.data.keyword.cloud_notm}}. Sua implementação de peer poderá, então, gerar certificados que são necessários para o peer participar de uma rede Starter Plan ou Enterprise Plan. Conclua as etapas a seguir para registrar um peer com um `enroll ID` e `enroll secret`. Você colará esses dois valores no arquivo de configuração.

1. Efetue login no {{site.data.keyword.blockchainfull_notm}} Platform e acesse o Monitor de rede. Na tela "Autoridade de certificação", é possível visualizar todas as identidades registradas com sua organização, como seus aplicativos de administrador ou de cliente.
  ![Tela da autoridade de certificação](../images/CA_screen_starter.png "Tela da autoridade de certificação")
  *Figura 2. Tela da autoridade de certificação*

2. Clique no botão **Incluir usuário** na tela. Uma tela pop-up é aberta e permite que você registre seu peer depois de preencher os campos a seguir. Salve os valores do ID e do Segredo, pois serão necessários ao configurar seu peer.
  - **ID de inscrição:** o nome de usuário de seu peer, que é referido como seu `enroll ID` ao configurar o peer. **Salve esse valor** para o arquivo de configuração.
  - **Segredo de inscrição:** a senha de seu peer, que é referida como seu `enroll Secret` ao configurar seu peer. **Salve esse valor** para o arquivo de configuração.
  - **Tipo:** selecione `Peer` para esse campo.
  - **Afiliação:** esta é a afiliação sob sua organização, `org1`, por exemplo, à qual seu peer pertencerá. Selecione uma afiliação existente na lista suspensa ou digite uma nova.
  - **Máximo de inscrições:** é possível usar esse campo para limitar o número de vezes que você pode inscrever ou gerar certificados usando essa identidade. Se não especificado, o valor será padronizado para inscrições ilimitadas.

  Depois de concluir os campos, clique em **Enviar** para registrar o peer. O peer registrado é então listado na tabela como uma identidade em sua organização. Como uma medida de segurança, use cada identidade e o enrollID e segredo associados para implementar somente um peer. Não reutilize os IDs e senhas do peer.

3. No arquivo de configuração sob a seção `"components"`, insira os valores a seguir:
  - `"enrollid"` é o valor do `enroll ID` da etapa anterior.
  - `"enrollsecret"` é o valor do `enroll secret` da etapa anterior.


### Criando um administrador
{: #ibp-peer-deploy-register-admin}

Depois de registrar a identidade do peer, também é necessário criar uma identidade de administrador que você usará para operar o peer. Primeiro, é necessário registrar essa nova identidade com sua CA e usá-la para gerar uma pasta MSP. Em seguida, você incluirá os usuários administrativos signCert no arquivo de configuração, no qual será criado um certificado de administrador do peer durante a implementação. Isso permite que você use os certificados da identidade do administrador para operar sua rede de blockchain, como iniciando um novo canal ou instalando o chaincode em seus peers.

Você precisa apenas criar uma identidade de administrador para os componentes pertencentes à sua organização. Se você estiver implementando vários peers, será necessário concluir essas etapas apenas uma vez. É possível usar o signCert gerado para um peer para implementar quaisquer outros peers pertencentes à sua organização.

1. Efetue login no {{site.data.keyword.blockchainfull_notm}} Platform e acesse o Monitor de rede. Na tela "Autoridade de certificação", é possível visualizar todas as identidades registradas com sua organização, como seus aplicativos de administrador ou de cliente.
  ![Tela da autoridade de certificação](../images/CA_screen_starter.png "Tela da autoridade de certificação")
  *Figura 2. Tela da autoridade de certificação*

2. Clique no botão **Incluir usuário** na tela. Uma tela pop-up é aberta e permite que você registre seu peer depois de preencher os campos a seguir. Salve os valores do ID e do Segredo, pois serão necessários ao configurar seu peer.
  - **ID de inscrição:** o nome de usuário de seu administrador de peer, que é referido como seu `enroll ID` ao configurar o peer. **Salve esse valor** para quando você gerar a pasta MSP do administrador.
  - **Segredo de inscrição:** a senha de seu administrador de peer, que é referida como seu `enroll Secret` ao configurar seu peer. **Salve esse valor** para quando você gerar a pasta MSP do administrador.
  - **Tipo:** selecione `Peer` para esse campo.
  - **Afiliação:** esta é a afiliação sob sua organização, `org1`, por exemplo, à qual seu peer pertencerá. Selecione uma afiliação existente na lista suspensa ou digite uma nova.
  - **Máximo de inscrições:** é possível usar esse campo para limitar o número de vezes que você pode inscrever ou gerar certificados usando essa identidade. Se não especificado, o valor será padronizado para inscrições ilimitadas.

  Após inserir esses campos, clique em **Enviar** para criar o administrador. O administrador criado é, então, listado na tabela como uma identidade em sua organização.

### Gerando a pasta MSP do administrador de peer
{: #ibp-peer-deploy-enroll-admin}

Depois de registrar a identidade de administrador é necessário gerar a pasta MSP do administrador do peer e o signCert. Portanto, é necessário executar um comando de inscrição com relação à sua CA do Starter Plan ou do Enterprise Plan.

1. Faça download do [cliente Fabric CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-fabric-ca-client) se você ainda não tiver feito.
2. Navegue para o diretório no qual você armazenará o material de criptografia e crie a pasta na qual armazenará a pasta MSP de seu administrador de peer.

  ```
  cd fabric-ca-client
  mkdir peer-admin
  ```
  {:codeblock}

3. Configure o caminho no qual o cliente pode criar seus certificados como `$FABRIC_CA_CLIENT_HOME`. Assegure-se de remover o material de configuração que pode ser criado por tentativas anteriores. Se você não executou o comando `enroll` antes, a pasta `msp` e o arquivo `.yaml` não existem.

  ```
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/peer-admin
  rm -rf $FABRIC_CA_CLIENT_HOME/fabric-ca-client-config.yaml
  rm -rf $FABRIC_CA_CLIENT_HOME/msp
  ```
  {:codeblock}

4. Abra o arquivo JSON **Perfil de conexão** por meio do painel "Visão geral" no Monitor de rede e localize as variáveis a seguir:
  - URL para CA: `url` sob `certificateAuthorities`
  - Nome da CA: `caName`

5. Faça download dos certificados TLS do {{site.data.keyword.cloud_notm}}, dependendo do plano de serviço, local e cluster que você usa. É possível localizar o cluster com base no nome de domínio da URL da autoridade de certificação: `us01.blockchain.ibm.com:31011` ou `us02.blockchain.ibm.com:31011`, por exemplo.

  - Certificado TLS para o Starter Plan
    - EUA: [us01.blockchain.ibm.com.cert ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/us01.blockchain.ibm.com.cert "us01.blockchain.ibm.com.cert"); [us02.blockchain.ibm.com.cert ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/us02.blockchain.ibm.com.cert "us02.blockchain.ibm.com.cert");
    [us03.blockchain.ibm.com.cert ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/us03.blockchain.ibm.com.cert "us03.blockchain.ibm.com.cert"); [us04.blockchain.ibm.com.cert ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/us04.blockchain.ibm.com.cert "us04.blockchain.ibm.com.cert");
    [us05.blockchain.ibm.com.cert ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/us05.blockchain.ibm.com.cert "us05.blockchain.ibm.com.cert"); [us06.blockchain.ibm.com.cert ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/us06.blockchain.ibm.com.cert "us06.blockchain.ibm.com.cert");
    [us07.blockchain.ibm.com.cert ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/us07.blockchain.ibm.com.cert "us07.blockchain.ibm.com.cert"); [us08.blockchain.ibm.com.cert ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/us08.blockchain.ibm.com.cert "us08.blockchain.ibm.com.cert")
    - Reino Unido: [uk01.blockchain.ibm.com.cert ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/uk01.blockchain.ibm.com.cert "uk01.blockchain.ibm.com.cert"); [uk02.blockchain.ibm.com.cert ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/uk02.blockchain.ibm.com.cert "uk02.blockchain.ibm.com.cert"); [uk03.blockchain.ibm.com.cert ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/uk03.blockchain.ibm.com.cert "uk03.blockchain.ibm.com.cert"); [uk04.blockchain.ibm.com.cert ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/uk04.blockchain.ibm.com.cert "uk04.blockchain.ibm.com.cert")
    - Sydney: [aus01.blockchain.ibm.com.cert ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/aus01.blockchain.ibm.com.cert "aus01.blockchain.ibm.com.cert");
  - [Certificado TLS para o Enterprise Plan ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://blockchain-certs.mybluemix.net/3.secure.blockchain.ibm.com.rootcert)

  Salve o conteúdo em um diretório no qual você possa referenciá-lo em comandos futuros.

    ```
    mkdir tls-ibp
    cp us01.blockchain.ibm.com.cert $HOME/fabric-ca-client/tls-ibp/tls.pem
    ```
    {:codeblock}

6. Emita o comando a seguir para gerar certificados com sua identidade de administrador de peer:

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <ca_name> --tls.certfiles <ca-tls_cert_file>
  ```
  {:codeblock}

  O `<enroll_id>` e `<enroll_password>` acima são o **ID** e o **Segredo** do administrador de peer que foi [registrado usando o Monitor de Rede](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-register-admin). O `<ca_name>` e `<ca_url_with_port>` são os valores de `caName` e `url` de seu perfil de conexão. Deixe `http://` fora do início da URL de CA.

  Uma chamada real pode ser semelhante ao comando de exemplo a seguir:

  ```
  fabric-ca-client enroll -u https://peeradmin:peeradminpw@n7413e3b503174a58b112d30f3af55016-org1-ca.us3.blockchain.ibm.com:31011 --caname org1CA --tls.certfiles $HOME/fabric-ca-client/tls-ibp/tls.pem
  ```
  {:codeblock}

  Depois que esse comando for concluído com êxito, ele gerará uma nova pasta MSP no diretório que você especificou como `$FABRIC_CA_CLIENT_HOME`. Esse diretório contém os certificados que serão usados para operar seu peer.

7. Na pasta MSP que acabou de ser criada, abra o arquivo signCert do novo usuário e converta-o para o formato base64. Você localizará o signCert na pasta `signcerts` do diretório MSP. Se você estiver usando as etapas de exemplo, será possível usar os comandos a seguir:

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat $HOME/fabric-ca-client/peer-admin/msp/signcerts/cert.pem | base64 $FLAG
  ```
  {:codeblock}

  **Nota:** é importante que a sequência gerada usando o comando acima seja formatada como uma única linha. Isso deve ser semelhante a:

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
   ```
   não assim:

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
   ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
   Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
   VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
   WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
   ```

  Esse comando imprimirá uma sequência semelhante ao exemplo a seguir:

  ```
  LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
  ```

  Insira essa sequência no campo `"admincerts"` na seção do componente no arquivo de configuração.

### Informações de CA do TLS
{: #ibp-peer-deploy-tls-ca-endpoint}

Os campos `"tls"` no arquivo de configuração requerem informações da CA que você implementou no {{site.data.keyword.cloud_notm}} Private. Você usará essa CA como uma CA separada do TLS, o que torna seu peer mais seguro. Use as instruções a seguir para gerar as informações relevantes:

- Os valores `"cahost"` e `"caport"` são a URL e a porta da [URL de CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url). Por exemplo, se a URL de CA for `http://9.30.94.174:30167`, o valor de `cahost` será `9.30.94.174` e a `caport` será `30167`.
- O `"caname"` é o nome da CA TLS da CA que você implementou no {{site.data.keyword.cloud_notm}} Private. O nome da CA TLS é o valor que você forneceu para o campo `CA TLS instance name` durante a configuração da CA.
- O `"cacert"` é o certificado TLS codificado em base64 de sua CA. Atualize a seção a seguir com o valor da saída do comando ao recuperar seu [certificado TLS da CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) como um pré-requisito.

  ```
  "catls": {
    "cacert": ""
  ```
  {:codeblock}

  Após a atualização, o campo `"cacert"` é semelhante ao exemplo a seguir:

  ```
  "catls": {
    "cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
  ```

### Registrando o peer com a CA TLS
{: #ibp-peer-deploy-tls-register-peer}

É necessário registrar seu peer com a CA TLS no {{site.data.keyword.cloud_notm}} Private usando o cliente Fabric CA.

1. No momento, você deve ter o arquivo de certificado TLS `tls.pem` na pasta ` $HOME/fabric-ca-client/catls `. Caso contrário, será possível copiar o certificado TLS [transferido por download do {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) para um diretório no qual é possível referenciá-lo nos comandos abaixo. Assegure-se de que você esteja em seu diretório `$HOME/fabric-ca-client`.

  ```
  cd $HOME/fabric-ca-client
  mkdir catls
  cp $HOME/tls.pem $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

2. É necessário inscrever-se usando o administrador da CA TLS. Mude `$FABRIC_CA_CLIENT_HOME` para um diretório no qual você deseja armazenar seus certificados de administrador da CA do TLS.

  ```
  cd $HOME/fabric-ca-client
  mkdir tlsca-admin
  export FABRIC_CA_CLIENT_HOME=$HOME/fabric-ca-client/tlsca-admin
  ```
  {:codeblock}

3. Execute o comando a seguir para gerar certificados com o administrador de CA TLS.

  ```
  fabric-ca-client enroll -u https://<enroll_id>:<enroll_password>@<ca_url_with_port> --caname <tls_ca_name> --tls.certfiles <ca_tls_cert_file>
  ```
  {:codeblock}

  O `<enroll_id>` e `<enroll_password>` no comando são o [Nome e a senha do usuário administrador de CA](/docs/services/blockchain/CA_deploy.html#ca-deploy-admin-secret) que você passou para o segredo de Kubernetes quando implementou a Autoridade de certificação. Insira a [URL da CA](/docs/services/blockchain/howto/CA_operate.html#ca-operate-url) dentro da `<ca_url_with_port>`. Deixe fora o `http://` no início. O `<tls_ca_name>` é aquele que você especificou durante a [configuração da CA](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms).

  O `<ca_tls_cert_file>` é o nome do arquivo [CA TLS cert](/docs/services/blockchain/howto/CA_operate.html#ca-operate-tls) com seu caminho completo.

  Uma chamada real pode ser semelhante ao exemplo a seguir:

  ```
  fabric-ca-client enroll -u https://admin:adminpw@9.30.94.174:30167 --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  Depois de ter se inscrito, você tem os certificados necessários para registrar o peer com a CA TLS.

4. Emita o comando a seguir para localizar sua afiliação e seu nome da organização.

  ```
  fabric-ca-client affiliation list --caname <tls_ca_name> --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  Seu comando pode ser semelhante ao exemplo a seguir:

  ```
  fabric-ca-client affiliation list --caname tlsca --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```

  Você verá informações semelhantes ao exemplo a seguir:

  ```
  affiliation: org1 affiliation: org1.department1
  ```
  {:codeblock}

  Anote o valor de **afiliação** de sua organização, por exemplo, `org1.department1` para a organização `org1` no exemplo acima. Você precisará usar esse valor no comando abaixo.

5. Execute o comando a seguir para registrar o peer.

  ```
  fabric-ca-client register --caname <tls_ca_name> --id.name <name> --id.secret <secret>  --id.affiliation org1.department1 --id.type peer --tls.certfiles <tlsca_tls_cert_path>
  ```
  {:codeblock}

  É necessário usar seu nome de CA TLS no campo `--caname`.  Crie um nome e uma senha para o peer e, em seguida, use-os para substituir `name` e `secret`. **Importante:** anote essas informações. É necessário inserir o `name` e `secret` como `"enrollid"` e `"enrollsecret"` na seção `"tls"` no arquivo de configuração.

  Um comando de exemplo seria semelhante ao seguinte:

  ```
  fabric-ca-client register --caname tlsca --id.affiliation org1.department1 --id.name peertls --id.secret peertlspw --id.type peer --tls.certfiles $HOME/fabric-ca-client/catls/tls.pem
  ```
  {:codeblock}

  Depois de emitir esse comando, é possível atualizar o arquivo usando os valores acima:

  ```
  "tls": {...
    },
    "enrollid": "peertls",
    "enrollsecret": "peertlspw",
  ```

  É possível registrar somente uma identidade a cada vez. Se você tiver algum problema, tente um comando com um novo nome do usuário e senha. Como uma medida de segurança, use cada identidade e o enrollID e segredo associados para implementar somente um peer. Não reutilize os IDs e senhas do peer.

  Quando o comando for concluído com êxito, você verá informações semelhantes ao exemplo a seguir:

  ```
  2018/06/18 16:53:00 [INFO] Configuration file location: /fabric-ca-client/peer-admin/fabric-ca-client-config.yaml
  2018/06/18 16:53:00 [INFO] TLS Enabled
  2018/06/18 16:53:00 [INFO] TLS Enabled
  Password: peertlspw
  ```

É possível executar um comando em árvore para verificar o trabalho que você fez para preparar o arquivo de configuração. Navegue para o diretório no qual você armazenou seus certificados. Um comando de árvore deve gerar um resultado semelhante à estrutura a seguir:

```
cd $HOME/fabric-ca-client
tree
.
├── ca-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│ │ └── 9-12-19-115-31873-SampleOrgCA.pem
│       ├── keystore
│ │ └── c44ec1e708f84b6d0359f58ce2c9c8a289919ba81f2cf4bb5187c4ad5a43cbb0_sk
│ └── signcerts
│ | └── cert.pem
│       └── user
├── catls
│   └── tls.pem
├── peer-admin
│   ├── fabric-ca-client-config.yaml
│   └── msp
│       ├── cacerts
│ │ └── n4790a319014a473a8a65850c660396ab-org1-ca-us05-blockchain-ibm-com-31011-org1CA.pem
│       ├── keystore
│ │ └── 6c2a999ee80a6b0592c63c58f95193bea2df59efe5489938d513de47b83b372a_sk
│       ├── signcerts
│       │   └── cert.pem
│       └── user
├── tls-ibp
│   └── tls.pem
├── tls.pem
└── tlsca-admin
    ├── fabric-ca-client-config.yaml
    └── msp
        ├── cacerts
        │   └── 9-30-250-70-30395-tlsca.pem
        ├── keystore
        │ └── bd57fa20283dfc76ada83f989ee0f62ce23e98c94dbd26f6cd23202d8084e38e_sk
        ├── signcerts
        │   └── cert.pem
        └── user
```

### Hosts de CSR (Solicitação de Assinatura de Certificado)
{: #ibp-peer-deploy-csr-hosts}

É necessário fornecer os nomes de host do CSR para implementar um peer. Os nomes de host do CSR incluem o endereço IP do proxy do cluster no qual você implementará o componente, bem como o `service host name` que será o nome do host do gráfico Helm.

#### Localizando o valor do endereço IP do proxy do cluster

Se desejar implementar um peer no mesmo cluster do {{site.data.keyword.cloud_notm}} Private no qual implementou sua CA TLS, insira o mesmo IP de proxy que você usou quando [configurou para sua CA TLS](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy-configuration-parms). Se desejar implementar o componente em um cluster diferente, será possível recuperar o valor do endereço IP do proxy do cluster por meio do console do {{site.data.keyword.cloud_notm}} Private. É necessário ter a função administrativa do cluster do {{site.data.keyword.cloud_notm}} Private no qual o peer será implementado.

1. Efetue login no console do {{site.data.keyword.cloud_notm}} Private. No painel de navegação à esquerda, clique em **Plataforma** e, em seguida, em **Nós** para visualizar os nós que estão definidos no cluster.
2. Clique no nó com a função `proxy` e, em seguida, copie o valor do `IP do host` da tabela.
3. Insira o `Host IP` como o valor para `"hosts"` na seção `"csr"` do arquivo de configuração abaixo:

  ```
  "csr": {
    "hosts": [""]
  }
  ```

#### Determinando o nome do host do serviço
{: #ibp-peer-deploy-determine-svc-host-name}

O `service host name` será o `helm release name` que você especifica durante a implementação. Se o endereço do Proxy IP do cluster for 9.42.134.44" e o `helm release name` for `org1peer1`, você inserirá a seção `"csr"` do arquivo:

```
"csr": {
  "hosts": [
     "9.42.134.44",
     "org1peer1"
  ]
}
```
{:codeblock}

### Concluindo o arquivo de configuração
{: #ibp-peer-deploy-complete-config}

Depois de ter concluído todas as etapas, seu arquivo de configuração atualizado será semelhante ao exemplo a seguir:

```
{
	"enrollment": {
		"component": {
			"cahost": "n5ec276985ff54d73b9bf0e0a6525d37b-org2-ca.stage.blockchain.ibm.com",
			"caport": "31011",
			"caname": "org1CA",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peer1",
			"enrollsecret": "peer1pw",
			"admincerts": ["LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNuRENDQWtPZ0F3SUJBZ0lVTXF5VDhUdnlwY3lYR2sxNXRRY3hxa1RpTG9Nd0NnWUlLb1pJemowRUF3SXcKYURFTTlEKaFhTTzRTWjJ2ZHBPL1NQZWtSRUNJQ3hjUmZVSWlkWHFYWGswUGN1OHF2aCtWSkhGeHBLUnQ3dStHZDMzalNSLwotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg=="]
		}, 		"tls": {
			"cahost": "9.30.94.174",
			"caport": "30167",
			"caname": "tlsca",
			"catls": {
				"cacert": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUVsRENDQTN5Z0F3SUJBZ0lRQWYyajYyN0tkY2lJUTR0eVM4KzhrVEFOQmdrcWhraUc5dzBCQVFzRkFBkOHRiUWsKQ0FVdzdDMjlDNzlGdjFDNXFmUHJtQUVTcmNpSXhwZzBYNDBLUE1icDFaV1ZiZDQ9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KCg=="
			},
			"enrollid": "peertls",
			"enrollsecret": "peertlspw",
			"csr": {
        "hosts": [
           "9.42.134.44",
           "org1peer1"
        ]
			}
		}
  }
}
```
{:codeblock}

Depois de concluir o preenchimento nesse arquivo, é necessário salvá-lo no formato JSON e transmiti-lo para sua implementação de peer como um segredo do Kubernetes. Crie o segredo usando as etapas na [próxima seção](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file-ibp).

## Criando o segredo de configuração
{: #ibp-peer-deploy-config-file-ibp}

Um [Segredo do Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/configuration/secret/ "Segredos") permite proteger e compartilhar informações sem ter que passá-las diretamente para a implementação. Depois de ter salvo o arquivo de configuração, será necessário codificá-lo em `base64`.

1. Para codificar o arquivo de configuração no formato base64, execute os comandos a seguir por meio de uma janela do terminal:

  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  cat <config_file> | base64 $FLAG
  ```
  {:codeblock}

  **Nota:** é importante que a sequência gerada usando o comando acima seja formatada como uma única linha. Isso deve ser semelhante a:

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdLZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpFVk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFXTmxjblF1WTI5dE1TQXdIZ1lEVlFRREV4ZEVhV2RwUTJWeWRDQkhiRzlpWVd3Z1VtOXZkQ0JEDQpRVEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJBWVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBaMmxEWlhKMElGTklRVElnDQpVMlZqZFhKbElGTmxjblpsY2lC
   ```
   não assim:

   ```
   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tDQpNSUlFbERDQ0EzeWdBd0lCQWdJUUFmMmo2MjdL
   ZGNpSVE0dHlTOCs4a1RBTkJna3Foa2lHOXcwQkFRc0ZBREJoDQpNUXN3Q1FZRFZRUUdFd0pWVXpF
   Vk1CTUdBMVVFQ2hNTVJHbG5hVU5sY25RZ1NXNWpNUmt3RndZRFZRUUxFeEIzDQpkM2N1WkdsbmFX
   VEFlRncweE16QXpNRGd4TWpBd01EQmFGdzB5TXpBek1EZ3hNakF3TURCYU1FMHhDekFKQmdOVkJB
   WVRBbFZUDQpNUlV3RXdZRFZRUUtFd3hFYVdkcFEyVnlkQ0JKYm1NeEp6QWxCZ05WQkFNVEhrUnBa
   ```

  Salve a saída resultante para a etapa quatro abaixo.

2. Efetue login no console do {{site.data.keyword.cloud_notm}} Private. No painel de navegação à esquerda, clique em **Configuração** e, em seguida, em **Segredos**. Clique no botão **Criar segredo** para abrir um painel pop que permite gerar um novo objeto de segredo.

3. Na guia **Geral**, preencha os campos a seguir:
  - **Nome:** forneça ao seu segredo um nome exclusivo dentro de seu cluster. Você usará esse nome quando implementar seu peer. O nome deve ser todo em minúsculas.  
  **Nota:** ao implementar um peer, um novo segredo é gerado automaticamente pela implementação com o nome `<helm_release_name>-secret`. Portanto, ao nomear seu segredo, certifique-se de que o nome do segredo seja diferente do `<helm_release_name>-secret`. Caso contrário, a implementação do gráfico do helm falhará porque o segredo que ele tenta criar já existe.
  - **Namespace:** o namespace para incluir seu segredo. Selecione o `namespace` no qual você deseja implementar seu peer.
  - **Tipo:** insira o valor `Opaque`.

4. Clique na guia **Dados** na área de janela de navegação à esquerda dessa janela. Nos campos `Name`, especifique `secret.json` e, no campo de valor, cole na sequência codificada `base64` de seu arquivo de configuração.

5. (Opcional) Se você planeja usar o `CouchDB` como seu banco de dados de estado, será necessário incluir dois valores adicionais nesse objeto secreto. Na guia **Dados**, clique no botão **Incluir dados** para incluir o ID do usuário e a senha do CouchDB a serem usados para o banco de dados quando o contêiner for implementado.
  1. Crie seu nome do usuário e senha e codifique os valores no formato base64. Execute os comandos a seguir em uma janela do terminal e substitua `admin` e `adminpw` pelos valores que você deseja usar.
    ```
    echo -n 'couch' | base64 $FLAG echo -n 'couchpw' | base64 $FLAG
    ```
    {:code_block}

   2. No campo **Nome**, insira o valor `couchdbuser`. No campo **Valor**, insira o resultado de `echo -n 'couch' | base64 $FLAG` da etapa um acima.
   3. Clique no botão **Incluir dados** para incluir um segundo par chave-valor.
   4. No segundo campo **Nome**, insira o valor `couchdbpwd`. No campo **Valor** correspondente, insira o resultado de `echo -n 'couchpw' | base64 $FLAG` da etapa um acima.

6. Clique em **Criar** para salvar seu objeto de segredo.

**Nota:** o segredo de configuração do peer não será removido do cluster do {{site.data.keyword.cloud_notm}} Private quando você excluir sua liberação do Helm. Se você excluir seu peer, será necessário excluir esse segredo também.

## Configuração
{: #ibp-peer-deploy-peer-configuration}

Depois de criar o objeto de segredo de configuração do peer, é possível configurar e instalar seu peer no {{site.data.keyword.cloud_notm}} Private com as etapas a seguir. É possível instalar somente um peer por vez.

1. Efetue login no console do {{site.data.keyword.cloud_notm}} Private e clique no link **Catálogo** no canto superior direito.
2. Clique em `Blockchain` no painel de navegação esquerdo para localizar o tile rotulado `ibm-blockchain-platform-prod` ou `ibm-blockchain-platform-dev` se você transferiu por download a Community edition. Clique no tile para abri-lo e é possível ver um arquivo Leia-me que inclui informações sobre como instalar e configurar o gráfico do Helm.
3. Clique na guia **Configuração** na parte superior do painel ou clique no botão **Configurar** no canto inferior direito.
4. Especifique os valores para os [Parâmetros de configuração geral](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-peer-configuration-parms) e aceite o contrato de licença.
5. Abra a seta `Todos os parâmetros` e especifique o valor para os [Parâmetros de configuração global](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-global-parameters).
6. Role para baixo até a seção **Configuração de peer**. Marque a caixa de seleção `Instalar o peer` e conclua as [definições de configuração](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-peer-parameters) para o peer.
7. Clique em **Instalar**.

### Parâmetros de configuração
{: #ibp-peer-deploy-peer-configuration-parms}

A tabela a seguir lista os parâmetros configuráveis do {{site.data.keyword.blockchainfull_notm}} Platform, **específicos do componente de peer**, e seus valores padrão. Se você estiver experimentando com o peer ou iniciando pela primeira vez, use os valores padrão dos parâmetros de banco de dados e de armazenamento. Role para a seção do componente de peer para marcar a caixa de seleção `Installl Peer` e forneça as informações de configuração associadas apenas para esse componente. **Embora a UI do gráfico do Helm indique que nenhuma configuração adicional é necessária, você precisa inserir determinados parâmetros para implementar um peer.**

#### Parâmetros de configuração global e geral
{: #ibp-peer-deploy-global-parameters}

|  Parâmetro     | Descrição    | Padrão  | Requerido |
| --------------|-----------------|-------|------- |
| `Helm release name`| O nome de sua liberação do Helm. Deve iniciar com uma letra minúscula e terminar com qualquer caractere alfanumérico, deve conter apenas hifens e caracteres alfanuméricos minúsculos. Deve-se usar um nome de liberação do Helm exclusivo toda vez que tentar instalar um componente. **Importante:** esse valor deve corresponder ao valor que você usou para gerar o 'service host name' para o campo "hosts" em seu [arquivo de segredo JSON.](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-csr-hosts) | nenhum | sim  |
| ` Namespace de destino `| Escolha o namespace do Kubernetes para instalar o gráfico do Helm. | nenhum | sim |
|**Configuração global**| Parâmetros que se aplicam a todos os componentes no gráfico do Helm|||
| `Service account name`| Insira o nome da [conta do serviço ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/ "Configurar contas do serviço para pods") que você usará para executar o pod. | padrão | não |

#### Parâmetros de configuração de peer
{: #ibp-peer-deploy-peer-parameters}

|  Parâmetro     | Descrição    | Padrão  | Requerido |
| --------------|-----------------|-------|------- |
|**Configuração do cluster** |**Informações de configuração do cluster** | ||
| `Install Peer` | Selecione para instalar um peer|desmarcado | sim, se você deseja instalar um ponto |
| `Peer worker node architecture`| Selecione sua arquitetura da plataforma de nuvem (AMD64 ou S390x)| AMD64 | sim |
| `Peer image repository`| Local do gráfico do Helm de peer. Esse campo é preenchido automaticamente para o caminho instalado. Se você estiver usando o Community Edition e não tiver acesso à Internet, ele deverá corresponder ao diretório no qual você transferiu por download a imagem do peer do Fabric. | ibmcom/ibp-fabric-peer | sim |
| `Peer Docker image tag`|Valor da tag associada à imagem do peer |1.2.1, preenchido automaticamente com o valor correto.|sim|
| `Peer configuration`|É possível customizar a configuração do peer colando seu próprio arquivo de configuração `core.yaml` nesse campo. Para ver um arquivo `core.yaml` de amostra, veja a [configuração de amostra do `core.yaml` ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://github.com/hyperledger/fabric/blob/release-1.2/sampleconfig/core.yaml "hyperledger/fabric/core.yaml") **Somente para usuários avançados**. |nenhum|não|
| `Peer configuration secret (Required)`| Nome do [Segredo de configuração do peer](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy-config-file-ibp) criado no {{site.data.keyword.cloud_notm}} Private.  |nenhum|sim|
|`Organization MSP (Required)`|Esse valor pode ser localizado no Monitor de rede (IU do Starter Plan e do Enterprise Plan) clicando em "Configuração do peer remoto" na tela Visão geral.  |nenhum|sim|
|`Peer service type`| Usado para especificar se [portas externas devem ser expostas ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types "Serviços de publicação - tipos de serviço") no peer. Selecione NodePort para expor as portas externamente (recomendado) e ClusterIP para não expor as portas. O LoadBalancer e o ExternalName não são suportados nesta liberação. | NodePort |sim|
| `State database`| O [banco de dados de estado](/docs/services/blockchain/glossary.html#glossary-state-database) usado para armazenar o livro-razão do canal. O peer precisa usar o mesmo banco de dados que sua [rede de blockchain](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences). | LevelDB | sim |
|`CouchDB image repository`| Será aplicada somente se CouchDB tiver sido selecionado como o banco de dados do livro-razão. Esse campo é preenchido automaticamente para o caminho instalado. Se você estiver usando o Community Edition e não tiver acesso à Internet, ele deverá corresponder ao diretório no qual a imagem do Fabric CouchDB foi transferida por download.| ibmcom/ibp-fabric-couchdb | sim |
| `CouchDB Docker image tag`| Será aplicada somente se CouchDB tiver sido selecionado como o banco de dados do livro-razão. Valor da tag associada à imagem do CouchDB. | Preenchido automaticamente com o valor correto.| sim |
| `Peer Data persistence enabled`| Ative a capacidade de persistir dados após reinicializações ou falhas do cluster. Consulte [armazenamento no Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/ "Volumes") para obter mais informações.  *Se desmarcado, todos os dados serão perdidos no caso de um failover ou de uma reinicialização de pod.* | marcado | não |
| `Peer use dynamic provisioning`| Marque para ativar o fornecimento dinâmico para volumes de armazenamento. | marcado | não |
| `Peer persistent volume claim`| Somente para nova solicitação. Insira um nome para sua nova Solicitação de Volume Persistente (PVC) a ser criada. | my-data-pvc | não |
| `Peer storage class name`| Especifique um nome de classe de armazenamento para o peer. | Em branco se estiver criando uma nova PVC; caso contrário, especifique a classe de armazenamento associada à PVC existente. | não |
| `Peer existing volume claim`| Especifique o nome de uma Solicitação de volume existente e deixe todos os outros campos em branco. | nenhum | não |
| `Peer selector label`| [Etiqueta do seletor ![Ícone de link externo](../images/external_link.svg "Ícone de link externo") ](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Rótulos e seletores") para seu PVC.| nenhum | não |
| `Peer selector value`| [Valor do seletor ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/ "Rótulos e seletores") para seu PVC. | nenhum | não |
| `Peer storage access mode`| Especifique o [modo de acesso ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modos de acessos") de armazenamento para o PVC.  | ReadWriteMany| não |
| `Peer volume claim size`| O tamanho da Solicitação de volume deve ser maior que 2 Gi. | 8Gi  | sim |
| `State database persistent volume claim`| Somente para nova solicitação. Insira um nome para sua nova Solicitação de Volume Persistente (PVC) a ser criada. | nome_do_pvc | não |
| ` Nome da classe de armazenamento do banco de dados de| Especifique um nome de classe de armazenamento para o banco de dados de estado | nenhum | não |
| ` solicitação de volume existente do banco de dados de estado| Especifique o nome de uma Solicitação de volume existente e deixe todos os outros campos em branco. | nenhum | não |
| `State database selector label`| [Rótulo do seletor](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) para seu PVC| nenhum | não |
| `State database selector value`| [Valor do seletor](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) para seu PVC | nenhum | não |
| ` Modo de acesso de armazenamento do banco de dados de| Especifique o [modo de acesso ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes "Modos de acessos") de armazenamento para o PVC. | ReadWriteMany| não |
| ` Tamanho da reivindicação do volume do banco de dados| Escolha o tamanho do disco a ser usado. | 8Gi | sim |
| `CouchDB - Data persistence enabled`| Para o contêiner do CouchDB, os dados do livro-razão estarão disponíveis quando o contêiner for reiniciado. *Se desmarcado, todos os dados serão perdidos no caso de um failover ou de uma reinicialização de pod.*| marcado | não |
| `CouchDB - Use dynamic provisioning`| Para o contêiner do CouchDB, use o armazenamento dinâmico do Kubernetes.| marcado | não |
| `Peer CPU request` | Número mínimo de CPUs a serem alocadas para o peer. | 1 | sim |
| `Peer CPU limit` | Número máximo de CPUs a serem alocadas para o peer.| 2 | sim |
| `Peer Memory request` | Quantia mínima de memória a ser alocada para o peer. | 1 Gi | sim |
| `Peer Memory limit` | Quantia máxima de memória a ser alocada para o peer. | 4Gi | sim |
| `CouchDB CPU request` | Número mínimo de CPUs a serem alocadas para o CouchDB.| 1 | sim |
| `CouchDB CPU limit` | Número máximo de CPUs a serem alocadas para o CouchDB. | 2 | sim |
| `CouchDB Memory request` | Quantia mínima de memória a ser alocada para o CouchDB.| 1 Gi | sim |
| `CouchDB Memory limit` | Quantia máxima de memória a ser alocada para o CouchDB. | 4Gi | sim |


Quando o CouchDB é selecionado como o banco de dados de peer, dois contêineres são criados no pod, um para o peer e um para o CouchDB. O contêiner do Peer inclui uma única montagem de volume para a PVC do Peer que armazena os blocos e as transações no sistema de arquivos. O contêiner do CouchDB monta a PVC do banco de dados de estado do peer que contém os dados do livro-razão.

<!-- LevelDB is not supported on the Peer
When LevelDB is selected as the peer database, a single container is created in the pod for running both the peer and LevelDB
processes. This container has two volume mounts, one for the Peer PVC and the second volume mount is for the peer state database PVC that contains the ledger data.
-->
<!--
| Peer database selection  | Contents of Container #1  | Contents of Container #2 |
| --------------|-----------------|---------------|
| CouchDB | Peer that mounts the Peer PVC| CouchDB that mounts the state database PVC |
| LevelDB | Peer and LevelDB that mount the Peer PVC and the state database PVC | n/a |
-->

### Usando a linha de comandos do Helm para instalar a liberação do Helm
{: #ibp-peer-deploy-helm-cli}

Como alternativa, é possível usar a CLI do Helm para instalar a liberação do Helm. Antes de executar o comando `helm install`, assegure-se de [incluir o repositório do Helm de seu cluster no ambiente da CLI do Helm ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.0/app_center/add_int_helm_repo_to_cli.html "Incluindo o repositório do Helm interno na CLI do Helm").

É possível configurar os parâmetros necessários para a instalação, criando um arquivo `yaml` e passando-o para o comando `helm install` a seguir.

```
helm install --name <helm_release_name>  <helm_chart> \
--version <helm_chart_version> \
--values <customvalues.yaml> \
--tls
```

em que:
- `<helm_release name>` representa o nome que você deseja fornecer à liberação do Helm.
- `<helm_chart>` representa o nome do gráfico Helm importado para o catálogo.
- `<helm_chart_version>` representa a versão do gráfico Helm importado para o catálogo.
- `<customvalues.yaml>` é o nome do arquivo yaml que contém os parâmetros de configuração.

Por exemplo:

```
helm install --name jnchart2 mycluster/ibm-blockchain-platform \
--version 1.1.0 \
--values peer-s390x-values.yaml \
--tls
```
É possível criar um novo a

## Verificar a instalação de peer
{: #ibp-peer-deploy-verify-installation-ibp}

Depois de concluir os parâmetros de configuração e clicar no botão **Instalar**, clique no botão **Visualizar liberação do Helm** para visualizar sua implementação. Se isso foi bem-sucedido, você verá o valor 1 nos campos `DESIRED`, `CURRENT`, `UP TO DATE` e `AVAILABLE` na tabela Implementação. Você pode precisar clicar em atualizar e aguardar que a tabela seja atualizada. Também é possível localizar a tabela Implementação clicando no ícone **Menu** no canto superior esquerdo no console do {{site.data.keyword.cloud_notm}} Private. Na lista de menu, clique em **Cargas de trabalho** e, em seguida, **Liberações do Helm**.

Se você rolar para baixo para a seção `Notas`, haverá informações importantes que serão usadas ao [operar seu peer](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate).

## Visualizando os logs de peer
{: #ibp-peer-deploy-view-logs}

Os logs de componentes podem ser visualizados na linha de comandos usando os [`kubectl CLI commands`](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-kubectl-configure) ou por meio do [Kibana ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.elastic.co/products/kibana "Sua janela no Elastic Search"), que está incluído no cluster do {{site.data.keyword.cloud_notm}} Private. Para obter mais informações, consulte estas [instruções para acessar os logs](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate-view-logs).

## O que Vem Depois
{: #ibp-peer-deploy-whats-next}

Depois de ter implementado o peer, é necessário concluir várias etapas operacionais antes de poder enviar transações e ler o livro-razão distribuído da rede de blockchain. Para obter mais informações, consulte [Peers operacionais com Starter Plan ou Enterprise Plan](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate).


## Detecção de problemas
{: #ibp-peer-deploy-troubleshooting}

### **Problema:** erro com a URL da CA ao executar o comando `enroll`
{: #ibp-peer-deploy-ca-enroll-error}

O comando de inscrição do cliente Fabric CA poderá falhar se a URL de inscrição, o valor de parâmetro `-u`, contiver um caractere especial. Por exemplo, o comando a seguir com o ID de inscrição e a senha de `admin:C25A06287!0`,

```
./fabric-ca-client enroll -u https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241 --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```

falhará e produzirá o erro a seguir:

```
!pw@9.12.19.115: evento não localizado
```

### **Solução:**
É necessário codificar o caractere especial ou circundar a URL com as aspas simples. Por exemplo, `!` torna-se `%21` ou o comando é semelhante a:

```
./fabric-ca-client enroll -u 'https://admin:C25A06287!0@ash-zbc07c.4.secure.blockchain.ibm.com:21241' --tls.certfiles $HOME/fabric-ca-remote/cert.pem --caname PeerOrg1CA
```
