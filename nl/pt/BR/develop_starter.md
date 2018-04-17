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

# Implementando uma rede de negócios no Starter Plan
{: #deploying-a-business-networks-on-starter-plan}

As redes de negócios podem ser desenvolvidas e implementadas em um ambiente do Starter Plan usando o ambiente de desenvolvedor do {{site.data.keyword.blockchainfull}} Platform: Develop e o conjunto de ferramentas de desenvolvedor do Hyperledger Composer.

Usando o ambiente de desenvolvedor, é possível rapidamente modelar e testar redes de negócios do {{site.data.keyword.blockchain}} e implementá-las em uma instância do {{site.data.keyword.blockchainfull_notm}} Platform.

## Antes de iniciar

Leia [Sobre o Starter Plan](./starter_plan.html) e [Introdução ao Starter Plan](./get_start_starter_plan.html). Verifique se também instalou o ambiente do desenvolvedor do [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](./develop_install.html) e criou uma instância do {{site.data.keyword.blockchainfull_notm}} Platform Starter Plan seguindo as instruções em [Governar a rede do Starter Plan](./get_start_starter_plan.html).


## Etapa um: Recuperar o segredo do administrador

1. Na tela de visão geral do Starter Plan, clique em **Perfil de conexão**.

2. Dentro do perfil de conexão está uma propriedade **segredo do administrador**. Recupere o segredo e salve uma cópia dele.

## Etapa dois: Criando um cartão de autoridade de certificação

O segredo recuperado na etapa anterior será usado para criar uma placa de rede de negócios para a autoridade de certificação (CA). O cartão de autoridade de certificação será importado e o cartão será usado para trocar o **segredo do administrador** por certificados válidos da autoridade de certificação do Starter Plan.

1. Usando o segredo observado da etapa um, execute o comando a seguir para criar a placa de rede de negócios da autoridade de certificação:

        composer card create -f ca.card -p ./config/connection-profile.json -u admin -s ${SECRET}

2. Importe o cartão usando o comando a seguir:

        composer card import -f ca.card -n ca

3. Agora que o cartão foi importado, ele poderá ser usado para trocar o **segredo do administrador** por certificados válidos da autoridade de certificação. Execute o comando a seguir para solicitar certificados da autoridade de certificação:

        composer identity request --card ca --path ./credentials

O comando `composer identity request` cria um diretório `credentials` que contém arquivos de certificado `.pem`.

## Etapa três: Incluindo os certificados na instância do Starter Plan

Os certificados devem ser incluídos na instância do Starter Plan. Por conveniência, eles podem ser incluídos usando a API do {{site.data.keyword.blockchainfull_notm}} Platform. Os certificados devem ser incluídos e, então, os peers devem ser parados e reiniciados e os certificados sincronizados. Os dados que são necessários nas variáveis das chamadas API podem ser recuperados do **Perfil de conexão** no Monitor de rede do Starter Plan.

1. Inclua o certificado usando a chamada API a seguir:

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary <body> ${API_URL}/api/v1/networks/${NETWORKID}/certificates

    No exemplo acima, o corpo é deixado em branco, o código a seguir é um exemplo do corpo da solicitação de API:

        {
        "msp_id": "ExamplePeerOrg1",
        "adminCertName": "exampleAdminCert1",
        "adminCertificate": "-----BEGIN CERTIFICATE-----\nMIIBkzCCATqgAwIB...",
        "peer_names": [
          "example-fabric-peer-1234a"
        ],
        "SKIP_CACHE": true
        }

2. Pare os peers usando a chamada API a seguir:

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/stop

3. Reinicie os peers usando a chamada API a seguir:

        curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} --data-binary '{}' ${API_URL}/api/v1/networks/${NETWORKID}/nodes/${PEER}/start

4. Sincronize os certificados usando a chamada API a seguir:

        curl -X GET --header 'Content-Type: application/json' --header 'Accept: application/json' --basic --user ${USERID}:${PASSWORD} ${API_URL}/api/v1/networks/${NETWORKID}/nodes/status

## Etapa quatro: Criando uma placa de rede de negócios do administrador

Agora que os certificados corretos foram sincronizados com os peers, é possível criar placas de rede de negócios que tenham as permissões para instalar o tempo de execução do Hyperledger Composer e iniciar o chaincode.

1. Crie um cartão do administrador com o administrador do canal e funções administrativas de peer usando o comando a seguir:

        composer card create -f adminCard.card -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem --role PeerAdmin --role ChannelAdmin

    Este cartão será usado para implementar uma rede de negócios para o Starter Plan.

2. Importe o cartão criado na etapa anterior usando o comando a seguir:

        composer card import -f adminCard.card -n adminCard

## Etapa cinco: Instalando e iniciando a rede de negócios

Em seguida, o cartão criado na etapa anterior pode ser usado para instalar e iniciar uma rede de negócios. Para este guia, nós instalaremos a amostra de rede de manufatura de veículo. O comando para iniciar uma rede de negócios também criará um cartão. Para o Starter Plan, esse cartão deve ser excluído, o comando de exemplo fornecido nomeia esse cartão `delete_me.card` para que possa ser facilmente distinguido.

1. Instale o tempo de execução do Hyperledger Composer com o comando a seguir:

        composer runtime install -c adminCard -n vehicle-manufacture-network

2. Inicie a rede de negócios com o comando a seguir:

        composer network start -c adminCard -a vehicle-manufacture-network.bna -A admin -C ./credentials/admin-pub.pem -f delete_me.card

3. Exclua a placa de rede de negócios chamada `delete_me.card`.

4. Crie uma nova placa de rede de negócios e referencie os certificados que foram recuperados anteriormente com o comando a seguir"

        composer card create -n vehicle-manufacture-network -p ./config/connection-profile.json -u admin -c ./credentials/admin-pub.pem -k ./credentials/admin-priv.pem

5. Importe a placa de rede de negócios com o comando a seguir:

        composer card import -f ./admin@vehicle-manufacture-network.card

A rede de negócios agora está implementada na instância do Starter Plan.
