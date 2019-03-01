---

copyright:
  years: 2017, 2019
lastupdated: "2019-02-08"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}
{:note: .note}
{:important: .important}
{:tip: .tip}

# Problemas conhecidos
{: #known-issues}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Os problemas a seguir já foram relatados:
- **Configuração de uma autoridade de certificação externa ainda não é suportada**. Como alternativa, é possível gerar e fazer upload de certificados administrativos por meio do Monitor de Rede. Para obter mais informações, veja [Gerando os certificados do lado do cliente](/docs/services/blockchain/v10_application.html#dev-app-enroll-panel) e a descrição na [guia "Certificados" da tela "Membro"](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members) no Monitor de rede.
- No Monitor de rede de uma rede do Starter Plan, quando você clicar em **Visualizar logs** nos nós listados na tela "Visão geral", a interface do {{site.data.keyword.cloud}} Log Kibana será aberta. **Por padrão, o Kibana vem pré-configurado para mostrar logs dos últimos 30 dias de atividade**. Se não houve atividade nos últimos 30 dias, você verá uma mensagem informando *Nenhum resultado localizado*. Para visualizar outros logs, é possível clicar no ícone de cronômetro no canto superior direito sob seu nome de usuário e configurar um intervalo de tempo mais amplo, como *Início do ano até hoje*
- Os logs de sua rede do Starter Plan são reunidos pelo [Serviço {{site.data.keyword.cloud_notm}} Log Analysis![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/log-analysis). Por padrão, os seus logs são coletados pelo Plano Lite do serviço de Análise do log. Esse plano é grátis e **apenas permite procurar os primeiros 500 MB de seus logs por dia**. Se os logs de sua rede excederem 500 MB, você não poderá visualizar novos logs no Kibana. Se a sua rede gerar mais de 500 MB de logs, será possível fazer upgrade para uma versão paga do Serviço de Análise do log.
- Como o Starter Plan não é um ambiente de produção, **talvez os aplicativos não possam atingir imediatamente uma rede de recursos**.
  - Se isso acontecer, recomenda-se como um primeiro passo aumentar os valores de tempo limite padrão no SDK do Fabric. Para obter mais informações sobre como configurar valores de tempo limite, veja [Configurando valores de tempo limite em SDKs do Fabric](/docs/services/blockchain/v10_application.html#dev-app-set-timeout-in-sdk).
  - Também é possível tentar novamente a sua solicitação no nível do aplicativo.
- **Os contêineres do chaincode podem, às vezes, ser parados** por um problema de rede de plano de fundo e podem precisar ser reconstruídos e reiniciados após serem chamados por um usuário. Se isso acontecer, o seu chaincode poderá levar alguns minutos para responder.
- Devido à limitação de recurso na rede do Starter Plan, isto é, 1 CPU e 4 Gi de RAM para cada peer, **é possível encontrar um erro `REQUEST_TIMEOUT` durante a instanciação de chaincode**. Se isso acontecer, tente novamente a etapa de instanciação. Se o erro continuar, será possível aumentar o tempo limite de instanciação do chaincode. No perfil de conexão, o tempo limite de instanciação do chaincode é configurado como 300 segundos.
  - Se você usar o valor de tempo limite padrão no SDK, copie a seção **cliente** no perfil de conexão conforme mostrado abaixo, que configura o tempo limite para 300 segundos e assegure-se de que o seu SDK leia. Observe que para o Node SDK, essa configuração de tempo limite no perfil de conexão afeta todas as chamadas, como `invoke`, `queries` e assim por diante.
    ```
    "client": {
       "organization": "Org1",
       "connection": {
           "timeout": {
               "peer": {
                   "endorser": "300"
               }
           }
       }
    },
    ```
    {:codeblock}
  - Se você sobrescrever a configuração de tempo limite de comandos de instanciação do chaincode em seus SDKs, configure novamente para o valor padrão ou mude para 300 segundos ou mais.
    - Se você usar o Node SDK, será possível mudar a configuração de tempo limite do método `sendInstantiateProposal (solicitação, tempo limite)`. Para obter mais informações, veja [sendInstantiateProposal (solicitação, tempo limite) ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://fabric-sdk-node.github.io/Channel.html#sendInstantiateProposal).
    - Se você usar o Java SDK, será possível mudar a configuração de tempo limite do comando `instantiateProposalRequest.setProposalWaitTime (DEPLOYWAITTIME)`, que está no arquivo `src/test/java/org/hyperledger/fabric/sdkintegration/End2endIT.java`.

Para obter suporte e ajuda com a sua rede do {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.cloud_notm}}, veja [Obtendo suporte](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support).

## Atualizando o chaincode para upgrade de rede do Enterprise Plan
{: #known-issues-ibp-about-chaincode-migration}

Se a sua rede do Enterprise Plan estiver na V1.0 no nível do Hyperledger Fabric, o {{site.data.keyword.blockchainfull_notm}} Platform planejará um upgrade para a sua rede para migrar para o Hyperledger Fabric V1.1. Com o upgrade de rede, se você usar o chaincode complexo com dependências, será necessário atualizar as dependências em seu chaincode. Caso contrário, é possível ter uma interrupção do serviço.

**Notas**:
- Se você usar o chaincode simples com um único arquivo `.go` ou `.js`, não será necessário atualizar o seu chaincode.
- O chaincode atualizado pode não funcionar em sua rede antiga; portanto, será necessário atualizar o seu chaincode após o upgrade planejado de rede.
- É possível testar o seu chaincode instalando e instanciando-o em uma rede do Starter Plan. Todos os chaincodes que trabalham no Starter Plan também funcionarão no Enterprise Plan após o upgrade de rede.

Execute as etapas a seguir para atualizar o seu chaincode:
1. Use qualquer ferramenta de vendas Golang para atualizar o seu chaincode. É mais fácil usar a mesma ferramenta que foi usada para incluir dependências no arquivo original. Por exemplo, se o seu chaincode usar a ferramenta **govendor** que muitas amostras do Fabric usam, será possível executar o comando a seguir no diretório acima da pasta do fornecedor para atualizar as dependências de seu chaincode:
    ```
    govendor update all +v
    ```
    {:codeblock}

    É possível, então, usar `go build` para verificar se o novo código é compilado e se a atualização de chaincode funciona.

2. Após o upgrade de sua rede, será possível [atualizar o seu chaincode](/docs/services/blockchain/howto/install_instantiate_chaincode.html#install-instantiate-chaincode-update-cc) no Monitor de rede.
