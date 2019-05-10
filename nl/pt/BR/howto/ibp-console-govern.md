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
{:pre: .pre}

# Controlando componentes
{: #ibp-console-govern}

Após criar CAs, peers, solicitadores, organizações e canais, é possível usar o console para atualizar esses componentes.
{:shortdesc}

**Público-alvo:** este tópico é projetado para operadores de rede que são responsáveis por criar, monitorar e gerenciar a rede de blockchain.  

## Como o serviço {{site.data.keyword.cloud_notm}} Kubernetes interage com o console
{: #ibp-console-govern-iks-console-interaction}

É responsabilidade do operador de rede monitorar o uso de CPU, memória e armazenamento e garantir que os recursos adequados estejam disponíveis **antes** de tentar criar ou redimensionar um nó.
{:important}

Como sua instância do console do {{site.data.keyword.blockchainfull_notm}} Platform e o cluster do serviço {{site.data.keyword.cloud_notm}} Kubernetes não se comunicam diretamente sobre os recursos disponíveis em seu cluster, o processo de implementação ou redimensionamento de componentes com o uso do console deve seguir esse padrão:

1. **Dimensione a implementação desejada**. Os painéis **Alocação de recurso** para a CA, o peer e o solicitador no console oferecem alocações padrão de CPU, memória e armazenamento para cada nó. Talvez seja necessário ajustar esses valores de acordo com seu caso de uso. Se não tiver certeza, comece com alocações padrão e ajuste-as conforme entender suas necessidades. Da mesma forma, o painel **Realocação de recurso** exibe as alocações de recursos existentes.

  Para entender a quantidade necessária de armazenamento e cálculo em seu cluster, consulte o gráfico subsequente a essa lista, que contém os padrões atuais para o peer, o solicitador e a CA.

2. **Verifique se você possui recursos suficientes em seu cluster do serviço {{site.data.keyword.cloud_notm}} Kubernetes**. Para monitorar os seus recursos do Kubernetes, recomendamos usar a ferramenta [{{site.data.keyword.cloud_notm}} SysDig ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/cloud/sysdig "IBM Cloud Monitoring with Sysdig") em combinação com o painel do seu {{site.data.keyword.cloud_notm}} Kubernetes. Se não tiver espaço suficiente para implementar ou redimensionar recursos, será necessário aumentar o tamanho de seu cluster do serviço {{site.data.keyword.cloud_notm}} Kubernetes. Para obter mais informações sobre como aumentar o tamanho de um cluster, consulte [Dimensionando clusters ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](/docs/containers?topic=containers-ca#ca "Dimensionando clusters"). Se tiver espaço suficiente em seu cluster, será possível continuar com a etapa 3.
3. **Use o console para implementar ou redimensionar seu nó**. Se o nó do trabalhador no qual o pod está em execução estiver com insuficiência de recursos, será possível incluir um novo nó do trabalhador maior em seu cluster e, em seguida, excluir o existente.

| **Componente** (todos os contêineres) | CPU (em millicpus) | CPU (em CPUs) | Memória (em megabytes) | Memória (em gigabytes) | Armazenamento (em gigabytes) |
|--------------------------------|--------------------|---------------|-----------------------|-----------------------|------------------------|
| **Peer**                       | 1100               | 1.1           | 2200                  | 2.2                   | 200                    |
| **CA**                         | 300                | .3            | 600                   | .6                    | 10                     |
| **Solicitador**                    | 450                | .45           | 900                   | .9                    | 100                    |

Para casos nos quais um usuário deseja minimizar os encargos sem desativar um nó completamente ou excluí-lo, é possível diminuir sua capacidade até um mínimo de 0.001 CPU (1 milliCPU). Observe que o nó não será funcional ao usar essa quantidade de CPU.
{:important}

## Alocando recursos
{: #ibp-console-govern-allocate-resources}

Enquanto os usuários de um cluster gratuito **devem usar tamanhos padrão** para os contêineres associados a seus nós, os usuários de clusters pagos podem configurar esses valores durante a criação de seus nós.

O painel **Alocação de recurso** no console fornece valores padrão para os diversos campos envolvidos na criação de um nó. Esses valores são escolhidos porque representam uma boa maneira de começar. No entanto, cada caso de uso é diferente. Embora este tópico forneça orientações para maneiras de pensar sobre esses valores, cabe ao usuário o monitoramento de seus nós e a localização de dimensionamentos que funcionem para eles. Portanto, exceto em situações nas quais os usuários têm certeza de que precisarão de valores diferentes dos padrões, uma estratégia prática é usar esses padrões e ajustar seus valores posteriormente. Para obter uma visão geral do desempenho e do dimensionamento do Hyperledger Fabric, no qual o {{site.data.keyword.blockchainfull_notm}} Platform é baseado, consulte [Respondendo suas perguntas sobre o desempenho e o dimensionamento do Hyperledger Fabric ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/blogs/blockchain/2019/01/answering-your-questions-on-hyperledger-fabric-performance-and-scale/ "Blog sobre o desempenho e o dimensionamento do Hyperledger Fabric").

Todos os contêineres associados a um nó têm **CPU** e **memória**, no entanto, determinados contêineres associados ao peer, ao solicitador e à CA também têm **armazenamento**. Para obter mais informações sobre as diferentes opções de armazenamento disponíveis para você no {{site.data.keyword.cloud_notm}}, consulte [opções de armazenamento ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](/docs/containers?topic=containers-kube_concepts#kube_concepts "Opções de armazenamento do Kubernetes"). Estude as opções cuidadosamente antes de decidir qual classe de armazenamento usar.

Embora a CPU e a memória possam ser mudadas usando o console e o painel do serviço {{site.data.keyword.cloud_notm}} Kubernetes, somente será possível mudar o armazenamento posteriormente por meio da CLI do {{site.data.keyword.cloud_notm}}.
{:note}

Cada nó tem um contêiner de proxy da web gRPC que autoinicializa a camada de comunicação entre o console e um nó. Esse contêiner tem valores de recurso fixos e é incluído no painel Alocação de recurso para fornecer uma estimativa exata de quanto espaço é necessário no seu cluster Kubernetes para que o nó seja implementado. Como os valores para esse contêiner não podem ser mudados, não discutiremos o proxy da web gRPC nas seções a seguir.

### Autoridades de certificação (CAs)
{: #ibp-console-govern-CA}

Diferentemente de peers e solicitadores, que estão ativamente envolvidos no processo de transação, as CAs estão envolvidas somente no registro, na inscrição de identidades e na criação de um MSP. Isso significa que requerem menos CPU e memória. Para sobrecarregar uma CA, um usuário precisaria fazê-lo por meio de solicitações (provavelmente usando APIs e um script) ou tendo emitido tantos certificados a ponto de gerar insuficiência de armazenamento. Embora, como sempre, esses valores representem as necessidades de um caso de uso específico, nenhuma dessas coisas deve acontecer em operações comuns.

A CA tem apenas um contêiner associado que pode ser ajustado:

* **A própria CA**: encapsula os processos internos da CA, como registrar e inscrever nós e usuários, bem como armazenar uma cópia de cada certificado emitido.

#### Dimensionando uma CA durante a criação
{: #ibp-console-govern-CA-sizing-creation}

A CA tem apenas um contêiner único com valores que precisam de atenção, pois os valores do servidor proxy da web gRPC não podem ser mudados.

| Recursos | Condição para aumentar |
|-----------------|-----------------------|
| **CPU e memória do contêiner da CA** | Quando você espera que sua CA receba um grande volume de registros e inscrições. |
| **Armazenamento da CA** | Quando você planeja usar essa CA para registrar um grande número de usuários e aplicativos. |

### Peers
{: #ibp-console-govern-peers}

O peer tem três contêineres associados que podem ser ajustados:

- **O próprio peer**: encapsula os processos internos do peer (como validar transações) e o blockchain (em outras palavras, o histórico de transações) para todos os canais aos quais ele pertence. Observe que o armazenamento do peer também inclui os contratos inteligentes instalados nele.
- **CouchDB**: local de armazenamento dos bancos de dados de estado do peer. Lembre-se de que cada canal tem um banco de dados de estado distinto.
- **Contrato inteligente**: lembre-se de que, durante uma transação, o contrato inteligente relevante é "chamado" (em outras palavras, executado). Observe que todos os contratos inteligentes instalados no peer serão executados em um contêiner separado dentro do contêiner de seu contrato inteligente, conhecido como um contêiner do Docker-in-Docker.

#### Dimensionando um peer durante a criação
{: #ibp-console-govern-peers-sizing-creation}

Conforme observamos em nossa seção sobre [como o Kubernetes interage com o console](#ibp-console-govern-iks-console-interaction), é recomendável usar os padrões para esses contêineres de peer e ajustá-los posteriormente, quando for aparente como eles estão sendo utilizados.

| Recursos | Condição para aumentar |
|-----------------|-----------------------|
| **CPU e memória do contêiner do peer** | Quando você antecipa imediatamente um alto rendimento de transações. |
| **Armazenamento do peer** | Quando você antecipa a instalação de muitos contratos inteligentes nesse peer e para associá-lo a muitos canais. Lembre-se de que esse armazenamento também será usado para armazenar contratos inteligentes de todos os canais aos quais o peer está associado. Tenha em mente que consideramos uma transação "pequena" quando ela está no intervalo de 10.000 bytes (10k). Como o armazenamento padrão é 100 G, isso significa que até 10 milhões de transações totais serão armazenadas no peer antes que ele precise ser expandido (pela praticidade, o número máximo será menor do que esse, pois as transações podem variar em tamanho e o número não inclui contratos inteligentes). Embora 100 G possa parecer muito mais armazenamento do que é necessário, tenha em mente que ele é relativamente barato e que seu processo de aumento é mais complexo (requer o uso da linha de comandos) do que aumentar a CPU ou a memória. |
| **CPU e memória do contêiner do CouchDB** | Quando você antecipa um alto volume de consultas com relação a um grande banco de dados de estado. Esse efeito pode ser minimizado de alguma forma por meio do uso de [índices ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/couchdb_as_state_database.html#couchdb-indexes "Documentação dos índices do Hyperledger Fabric"). No entanto, os altos volumes podem sobrecarregar o CouchDB, o que pode causar tempos limite de consultas e transações. |
| **Armazenamento do CouchDB (dados de livro-razão)** | Quando você espera um alto rendimento em muitos canais e não pretende usar índices. No entanto, como o armazenamento do peer, o armazenamento padrão do CouchDB é de 100 G, o que é significativo. |
| **CPU e memória do contêiner do contrato inteligente** | Quando você espera um alto rendimento em um canal, especialmente em casos nos quais diversos contratos inteligentes serão chamados de uma vez.|

### Solicitando nós
{: #ibp-console-govern-ordering-nodes}

Como os solicitadores não mantêm o banco de dados de estado e não hospedam contratos inteligentes, requerem menos contêineres do que os peers. No entanto, eles hospedam o blockchain (o histórico de transações) porque é nele que a configuração do canal é armazenada e eles precisam saber a configuração do canal mais recente para executar sua função.

Semelhante à CA, o solicitador tem apenas um contêiner associado que pode ser ajustado:

* **O próprio solicitador**: encapsula os processos internos do solicitador (como validar transações) e o blockchain para todos os canais que ele hospeda.

#### Dimensionando um solicitador durante a criação
{: #ibp-console-govern-peers-sizing-creation}

Conforme observamos em nossa seção sobre [como o Kubernetes interage com o console](#ibp-console-govern-iks-console-interaction), é recomendável usar os padrões para esses contêineres de solicitador e ajustá-los posteriormente, quando for aparente como eles estão sendo utilizados.

| Recursos | Condição para aumentar |
|-----------------|-----------------------|
| **CPU e memória do contêiner do solicitador** | Quando você antecipa imediatamente um alto rendimento de transações. |
| **Armazenamento do solicitador** | Quando você antecipa que esse solicitador fará parte de um serviço de solicitação em diversos canais. Lembre-se de que os solicitadores mantêm uma cópia do blockchain para cada canal do qual fazem parte. O armazenamento padrão do solicitador é de 100 G, igual àquele do próprio contêiner para o peer. |

Fazer com que o tamanho da CPU e da memória de seus nós de solicitação dobre o tamanho de seus peers, aproximadamente, embora não seja obrigatório, é considerado uma melhor prática. Se um nó de solicitação Solo estiver sobrecarregado, poderá atingir tempos limite e começar a descartar transações, fazendo com que tenham que ser reenviadas. Isso causa um dano muito maior a uma rede do que um único peer que se esforça para concluir ações. Em uma configuração de serviço de solicitação Raft, um nó líder sobrecarregado pode parar o envio de mensagens de pulsação, acionando uma eleição de líder e uma suspensão temporária da solicitação da transação. Da mesma forma, um nó seguidor pode perder mensagens e tentar acionar uma eleição de líder desnecessária.
{:important}

## Realocando recursos
{: #ibp-console-govern-reallocate-resources}

Como os contêineres ainda estão sendo reconstruídos após o redimensionamento de um nó, será possível ver um atraso antes que ele entre em vigor.
{:important}

Como dito acima, recomendamos usar a ferramenta [{{site.data.keyword.cloud_notm}} SysDig ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://www.ibm.com/cloud/sysdig "{{site.data.keyword.cloud_notm}} Monitoring with Sysdig") em combinação com o painel de seu {{site.data.keyword.cloud_notm}} para monitorar o uso de seu recurso do Kubernetes. Se determinar que um nó do trabalhador está com insuficiência de recursos, será possível incluir um novo nó do trabalhador maior em seu cluster e, em seguida, excluir o existente.
{:note}

Embora seja mais fácil ter recursos suficientes implementados no serviço {{site.data.keyword.cloud_notm}} Kubernetes e ser capaz de expandir seus pods e nós do trabalhador conforme adequado, sem ter que aumentar sua implementação primeiro no serviço {{site.data.keyword.cloud_notm}} Kubernetes, quanto maior a implementação no serviço {{site.data.keyword.cloud_notm}} Kubernetes, mais cara ela será. Os usuários precisam considerar suas opções cuidadosamente e reconhecer as trocas que estão fazendo, independentemente da opção que escolherem.

É possível usar uma das maneiras a seguir para realocar os recursos que você designa aos contêineres associados ao seu nó.

1. **Use o dimensionador automático do serviço {{site.data.keyword.cloud_notm}} Kubernetes**. O dimensionador automático dimensionará os nós do trabalhador em resposta às solicitações de recursos e suas configurações de especificação de pod. Para obter mais informações sobre o dimensionador automático do serviço {{site.data.keyword.cloud_notm}} Kubernetes e como configurá-lo, consulte [Dimensionando clusters ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](/docs/containers?topic=containers-ca#ca "Dimensionando clusters") na documentação do IBM {{site.data.keyword.cloud_notm}}. Observe que permitir que o dimensionador automático ajuste seus recursos resultará em encargos para a sua conta do serviço {{site.data.keyword.cloud_notm}} Kubernetes, que variarão de acordo com seu uso.
2. **Dimensionar manualmente**. Envolve monitorar seu uso no console e no cluster do serviço {{site.data.keyword.cloud_notm}} Kubernetes. Embora envolva mais etapas manuais do que o uso do dimensionamento automático, oferece a vantagem de permitir que o usuário sempre tenha certeza do que está sendo cobrado em sua conta do serviço {{site.data.keyword.cloud_notm}} Kubernetes.

Para dimensionar manualmente, clique no nó que deseja ajustar na página **Nós** e, em seguida, clique na guia **Uso**. É possível ver um botão chamado **Realocar**, que ativará uma guia **Alocação de recurso** muito semelhante à que você viu ao criar o nó. Se desejar diminuir a quantidade de recursos disponíveis, simplesmente forneça valores mais baixos e clique em **Realocar recursos** nessa guia e na página **Resumo** resultante.

Se desejar aumentar a CPU e a memória para um nó, use a guia **Alocação de recurso** para aumentar os valores. A caixa branca na parte inferior da página incluirá os novos valores. Depois de clicar em **Realocar recursos**, a página **Resumo** converterá esse valor em uma quantidade de **VPC**, usada para calcular sua conta. Em seguida, será necessário navegar até o painel de seu serviço {{site.data.keyword.cloud_notm}} Kubernetes para certificar-se de que seu cluster tenha recursos suficientes para essa realocação. Se tiver, será possível clicar em **Realocar recursos**. Se não houver recursos suficientes disponíveis, será necessário aumentar o tamanho de seu cluster usando o painel do serviço {{site.data.keyword.cloud_notm}} Kubernetes.

O método usado para aumentar o armazenamento dependerá da classe de armazenamento escolhida para seu cluster. Consulte novamente a documentação [opções de armazenamento ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](/docs/containers?topic=containers-kube_concepts#kube_concepts "storage options) para obter informações sobre como aumentar seu armazenamento.

Diferentemente da CPU e da memória, que podem ser aumentadas usando o console (se você tiver recursos disponíveis em seu cluster do serviço {{site.data.keyword.cloud_notm}} Kubernetes), será necessário usar a CLI do {{site.data.keyword.cloud_notm}} para aumentar o armazenamento de seus nós. Para obter um tutorial sobre como fazer isso, consulte [Mudando o tamanho e o IOPS de seu dispositivo de armazenamento existente ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](/docs/containers?topic=containers-file_storage#file_change_storage_configuration "Mudando o tamanho e o IOPS de seu dispositivo de armazenamento existente").

## Ajustando seu solicitador
{: #ibp-console-govern-orderer-tuning}

O desempenho de uma plataforma de blockchain pode ser afetado por diversas variáveis, como o tamanho da transação, do bloco ou da rede e os limites do hardware. O nó do solicitador inclui um conjunto de parâmetros de ajuste que, juntos, podem ser usados para controlar o rendimento e o desempenho do solicitador.  É possível usar esses parâmetros para customizar como seu solicitador processa transações, dependendo da grande frequência de transações pequenas ou da pouca frequência de transações grandes. Essencialmente, você tem o controle para decidir quando os blocos são recortados com base no tamanho, na quantidade e na taxa de chegada das transações.

Os parâmetros a seguir estão disponíveis no console ao clicar no nó do solicitador, na guia **Nós**, e, em seguida, em seu ícone **Configurações**. Clique no botão **Avançado** para abrir a **Configuração avançada de canal** para o solicitador.

### Tamanho do lote
{: #ibp-console-govern-orderer-tuning-batch-size}

Os três parâmetros a seguir trabalham juntos para controlar quando um bloco é recortado, com base em uma combinação da configuração do número máximo de transações em um bloco e do tamanho do bloco em si.

- **Número máximo absoluto de bytes**  
  Configure esse valor para o maior tamanho de bloco, em bytes, que pode ser recortados pelo solicitador.  Nenhuma transação pode ser maior que o valor de `Absolute max bytes`. Geralmente, é seguro que essa configuração poderá ser de duas a dez vezes maior que o `Preferred max bytes`.    
  **Nota**: o tamanho máximo permitido é de 99 MB.
- **Contagem máxima de mensagens**   
  Configure esse valor para o número máximo de transações que podem ser incluídas em um único bloco.
- **Número máximo preferencial de bytes**  
  Configure esse valor para um tamanho de bloco ideal, em bytes, que seja menor que o `Absolute max bytes`. Um tamanho de transação mínimo, sem aprovações, tem cerca de 1 KB.  Se você incluir 1 KB por aprovação necessária, um tamanho de transação típico terá aproximadamente de 3 a 4 KB. Portanto, é recomendável configurar o valor de `Preferred max bytes` para algo semelhante a `Max message count * expected averaged tx size`. No tempo de execução, sempre que possível, os blocos não excederão esse tamanho. Se chegar uma transação que faça com que o bloco exceda esse tamanho, ele será recortado e um novo bloco será criado para ela. No entanto, se chegar uma transação que exceda esse valor sem exceder o `Absolute max bytes`, ela será incluída. Se chegar um bloco que seja maior que o `Preferred max bytes`, ele conterá apenas uma transação única e o tamanho dela não poderá ser maior que o `Absolute max bytes`.

Juntos, esses parâmetros podem ser configurados para otimizar o rendimento de seu solicitador.

### Tempo limite do lote
{: #ibp-console-govern-orderer-tuning-batch-timeout}

Configure o valor do **Tempo limite** para a quantidade de tempo de espera, em segundos, após a primeira transação, antes de recortar o bloco. Se você configurar esse valor muito baixo, correrá o risco de evitar que os lotes preencham seu tamanho preferencial. Se configurar muito alto, poderá fazer com que o solicitador aguarde os blocos e o desempenho geral seja comprometido. Em geral, recomendamos configurar o valor de `Batch timeout` para algo menor que `max message count / maximum transactions per second`.

Quando você modifica esses parâmetros, não afeta o comportamento de canais existentes no solicitador. Em vez disso, qualquer mudança feita na configuração do solicitador se aplica apenas aos novos canais criados nele.
{:important}

## Modificando canais
{: #ibp-console-govern-channels}

### Configurando peers de âncora
{: #ibp-console-govern-channels-anchor-peers}

Como o [boato ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html "Protocolo de disseminação de dados de boatos") entre organizações deve ser ativado para que a descoberta de serviço e os dados privados funcionem, um peer de âncora deve existir para cada organização. Esse peer de âncora não é um **tipo** especial de peer, mas apenas o peer que a organização torna conhecido para as outras, com o objetivo de autoinicializar os boatos entre organizações. Portanto, pelo menos um [peer de âncora ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/gossip.html#anchor-peers "Peers de âncora") deve ser definido para cada organização na definição de coleção.

Para configurar um peer para ser um peer de âncora, clique na guia **Canais** e abra o canal no qual o contrato inteligente foi instanciado.
 - Clique na guia  ** Detalhes do canal ** .
 - Role para baixo até a tabela de peers de Âncora e clique em **Incluir peer de âncora**.
 - Selecione pelo menos um peer de cada organização na definição de coleção que você deseja que sirva como o peer de âncora para a organização. Por motivos de redundância, é possível considerar a seleção de mais de um peer de cada organização na coleta.
