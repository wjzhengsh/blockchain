---

copyright:
  years: 2019
lastupdated: "2019-02-11"


---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Introdução ao beta 2.0 grátis do {{site.data.keyword.blockchainfull_notm}} Platform
{: #ibp-v2-deploy-iks}

***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

O {{site.data.keyword.blockchainfull}} Platform 2.0 é uma liberação beta grátis que inclui o console do {{site.data.keyword.blockchainfull_notm}} Platform, uma GUI que pode simplificar e acelerar sua jornada para implementar e gerenciar componentes de blockchain. Este tutorial descreve como começar a usar o {{site.data.keyword.blockchainfull_notm}} Platform 2.0 e usar o console para implementar e gerenciar componentes de blockchain em seu cluster do {{site.data.keyword.IBM_notm}} Kubernetes Service no {{site.data.keyword.cloud_notm}}.
{:shortdesc}

 Depois de implementar o {{site.data.keyword.blockchainfull}} Platform no cluster Kubernetes, é possível ativar o console para criar e gerenciar os componentes de blockchain. Usando um cluster do {{site.data.keyword.IBM_notm}} Kubernetes Service para implementar o {{site.data.keyword.blockchainfull_notm}} Platform 2.0, você experimenta os benefícios importantes a seguir:

- **Controle:** você controla e gerencia seus componentes e certificados de blockchain por meio de um console central. Implemente somente os componentes necessários para seus negócios e inclua mais à medida que sua necessidade aumentar.
- **Implementação flexível baseada em Kubernetes:** é possível aproveitar as opções de cálculo (CPU, memória, armazenamento) para o cluster Kubernetes e alavancar as opções de HA e DR integrados.

## Considerações
{: #ibp-v2-deploy-iks-considerations}

Antes de implementar o console, assegure-se de que entenda as considerações a seguir:

- O {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta foi desenvolvido com o Hyperledger Fabric v1.4.
- Todos os peers implementados com o free 2.0 beta usam CouchDB como seu banco de dados de estado.
- Você tem a opção de usar um cluster Kubernetes gratuito para avaliação da oferta beta, no entanto, a capacidade e o desempenho são limitados, nenhum de seus dados pode ser migrado e o cluster é excluído depois de 30 dias.
- Você é responsável pelo gerenciamento do monitoramento de funcionamento, da segurança e da criação de log do cluster Kubernetes. Consulte estas informações [ ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/docs/containers/cs_responsibilities.html#your-responsibilities-by-using-ibm-cloud-kubernetes-service "Responsabilidades de gerenciamento do cluster") para obter detalhes sobre o que o {{site.data.keyword.cloud_notm}} gerencia e pelo que você é responsável.
- Você também é responsável por monitorar o uso de recursos de seu cluster Kubernetes usando o painel do Kubernetes. Se você precisar aumentar a capacidade de armazenamento ou o desempenho de seu cluster, consulte estas informações sobre como [modificar seu volume existente ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/docs/containers/cs_storage_file.html#change_storage_configuration "Mudando o tamanho e o IOPS de seu dispositivo de armazenamento existente").
- Você é responsável por gerenciar e proteger seus certificados, suas chaves públicas e privadas. A IBM não armazena seus certificados no cluster Kubernetes.
- A oferta free 2.0 beta está disponível apenas na região **Dallas** do {{site.data.keyword.cloud_notm}}. Portanto, todos os componentes de blockchain residirão no data center de Dallas. Eles não são implementados em outro lugar.
- Embora a oferta beta seja grátis, ainda será necessário pagar pelo cluster Kubernetes, se você escolher um cluster pago.
- O Kubernetes deve ser a versão 1.11 ou mais recente no cluster Kubernetes do {{site.data.keyword.cloud_notm}}. Use estas instruções para [fazer upgrade de seus clusters novos e existentes](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes) para esta versão.

## Antes de iniciar
{: #ibp-v2-deploy-iks-prereq}

Antes de iniciar:

- Assegure-se de que você tenha uma [Conta paga do {{site.data.keyword.cloud_notm}} ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.cloud.ibm.com/catalog/services/ibm-blockchain-platform-free-20-beta "Catálogo"). Se você não tiver uma conta:
1. Clique no botão  ** Inscrever-se ** .
2. Após você criar uma conta de avaliação grátis, faça upgrade dela para um tipo **Pré-pago** acessando **Gerenciar** > **Faturamento e uso** > **Faturamento** no console do {{site.data.keyword.cloud_notm}} e clicando em **Incluir cartão de crédito**.

Ao planejar usar a instância de serviço no contexto de uma solução mais ampla de toda a organização, recomenda-se que as organizações participantes usem um endereço de e-mail funcional para criar sua rede. Nesse caso, o acesso à rede não depende da disponibilidade de um único indivíduo.
{:tip}  

- Se você planeja usar um cluster existente do {{site.data.keyword.cloud_notm}} Kubernetes Service, verifique a versão do Kubernetes e faça upgrade dela para 1.11 ou superior, se necessário. Para obter mais informações sobre como determinar qual versão do Kubernetes seu cluster está executando e como fazer upgrade da versão, veja [Atualizando a versão do Kubernetes de seu cluster](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes).

### Navegadores
{: #ibp-v2-deploy-iks-browsers}
A lista a seguir especifica o software do navegador mínimo necessário para o console do {{site.data.keyword.blockchainfull_notm}} Platform:

- Chrome: versão mais recente para seu sistema operacional
- Firefox: versões mais recentes regulares (não ESR) para seu sistema operacional
- Safari: versão mais recente para Mac

### Recursos necessários
{: #ibp-v2-deploy-iks-resources-required}

Para implementar o console do {{site.data.keyword.blockchainfull_notm}} Platform em um cluster do {{site.data.keyword.cloud_notm}} Kubernetes Service, é necessário assegurar que o cluster Kubernetes atenda aos requisitos mínimos de recurso de hardware:

|Tipo de cluster do Kubernetes | Caso de uso | CPU | RAM | Nós do Trabalhador |
|-----------|------|-----|-----------------------|
|Padrão (Recomendado) | Adequado para MVPs | 4 (Compartilhado) | 16 GB (compartilhado)|múltiplos|
|Grátis | Adequado para avaliação | 2 | 4 GB | 1 |  

Esses recursos são suficientes para teste e experimentação. Se você usar um cluster Kubernetes grátis, esteja ciente de que o cluster será excluído após a avaliação de 30 dias e todos os ativos associados serão removidos. O desempenho é significativamente mais lento em um cluster grátis.
{:note}

## Etapa um: criar uma instância de serviço no {{site.data.keyword.cloud_notm}}
{: #ibp-v2-deploy-iks-create-service-instance}

Use as etapas a seguir para criar uma instância de serviço do {{site.data.keyword.blockchainfull_notm}} Platform 2.0 no {{site.data.keyword.cloud_notm}}.

1. Localize o [Serviço de blockchain ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.cloud.ibm.com/catalog/services/blockchain-platform-20) no Catálogo do {{site.data.keyword.cloud_notm}} ou procure por `Blockchain` em sua página Catálogo do {{site.data.keyword.cloud_notm}}.
2. Opcionalmente, é possível renomear o **Nome do serviço** para sua instância para que seja possível reconhecê-la facilmente no futuro.
3. Para Beta, **Dallas** é a única região disponível e não pode ser mudada.
4. É possível deixar os campos de grupo de recursos e de tags sem mudanças.
5. Clique em **Criar** para provisionar a instância de serviço.

## Etapa dois: Implementar o  {{site.data.keyword.blockchainfull_notm}}  Platform 2.0
{: #ibp-v2-deploy-iks-steps}

É possível seguir a orientação para implementar o {{site.data.keyword.blockchainfull_notm}} Platform 2.0 imediatamente depois de criar a instância de serviço.

1. A etapa  ** Welcome & pré-ites ** . Se você já tem um cluster do {{site.data.keyword.IBM_notm}} Kubernetes Service existente na região **Dallas**, marque a caixa de seleção e **Se você tiver um cluster do IBM Kubernetes Service e desejar usá-lo para seu serviço Blockchain, a próxima etapa poderá ser ignorada. Mas certifique-se de que a versão do Kubernetes esteja na v1.11 ou mais recente**. Clique em  ** Continuar **.
2. A etapa  ** Criar cluster ** . Se você marcar a caixa de seleção na etapa 1 para usar um cluster Kubernetes existente, essa etapa será ignorada. Caso contrário, clique em **Criar um novo cluster**, que ativa o painel do {{site.data.keyword.cloud_notm}} Kubernetes para criar um cluster. Para obter mais informações, veja [Introdução ao {{site.data.keyword.cloud_notm}} Kubernetes Service ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/docs/containers/container_index.html). Permita tempo extra para que esse processo seja concluído.
  - Independentemente do tipo de cluster escolhido, deve-se selecionar o local do cluster Kubernetes de **Dallas** para a liberação Beta.
  - Escolha **Cluster padrão (recomendado):** se você precisar de uma opção de prazo mais longo que inclua múltiplos nós para alta disponibilidade e possa ser migrada da oferta Beta para a oferta GA quando ela estiver disponível.
  - Escolha **Cluster grátis:** se você planeja usar o cluster por menos de 30 dias. **Observe** que não é possível migrar de um cluster grátis para um cluster pago. O tipo grátis de cluster oferece armazenamento limitado e rendimento de transação.
  - Para obter mais informações sobre as diferenças entre os clusters Kubernetes grátis e pago no {{site.data.keyword.cloud_notm}}, veja [Comparação de clusters grátis e padrão ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.cloud.ibm.com/docs/containers?topic=containers-cluster_types#cluster_types "Comparação de clusters grátis e padrão").  

   Deve-se retornar a essa guia em seu navegador depois de criar o cluster para que seja possível concluir o processo de implementação do {{site.data.keyword.blockchainfull_notm}} Platform 2.0.  
   {:important}  

  É necessário aguardar até que seu cluster seja implementado com êxito. Em seguida, clique no botão **Eu tenho um cluster**.
3. Sua versão do Kubernetes em execução em seu cluster deve estar na versão 1.11 ou superior. Siga estas [etapas](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes) para verificar sua versão do cluster e fazer upgrade dela, se necessário. Em seguida, volte e continue com estas instruções.
4. A etapa  ** Implementar no cluster ** . Selecione o cluster Kubernetes no qual você deseja implementar o {{site.data.keyword.blockchainfull_notm}} Platform 2.0 na lista suspensa e clique em **Implementar no cluster**.

  Você pode estar nas situações a seguir em que não é possível localizar seu cluster Kubernetes na lista suspensa:
  - O processo de criação de cluster pode levar cerca de 10 minutos para ser concluído. Se você criou um cluster, permita algum tempo extra até que o status de seu cluster precise ser **Normal**.
  - Clusters que estão fora da região **Dallas** não são visíveis e não podem ser usados.
  - Certifique-se de que você não esteja usando a versão ESR do Firefox. Se estiver, alterne para outro navegador, como Chrome, e tente novamente.

5. A etapa  ** Ativar console ** . Após que seu {{site.data.keyword.blockchainfull_notm}} Platform 2.0 tiver sido implementado com êxito, clique em **Ativar o {{site.data.keyword.blockchainfull_notm}} Platform** para abrir o console do {{site.data.keyword.blockchainfull_notm}} Platform. Pode levar alguns minutos para que o botão seja ativado enquanto o console está sendo provisionado.

## (Opcional) Inclua usuários adicionais no console
{: #ibp-v2-deploy-iks-add-users}

Por padrão, o console usa o [ID do {{site.data.keyword.IBM_notm}} ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/docs/iam/index.html#iamoverview "IBM Cloud Identity and Access Management") como o provedor de serviços de identidade. Seu console do {{site.data.keyword.blockchainfull_notm}} Platform é provisionado configurando o endereço de e-mail do proprietário do {{site.data.keyword.IBM_notm}} como Administrador do console. Como um Administrador, esse usuário está autorizado a conceder aos outros usuários acesso ao console por meio de seus endereços de e-mail.  

### Como incluir um novo usuário
{: #ibp-v2-deploy-iks-add-users-howto}

Clique no ícone **Usuários** na navegação à esquerda para visualizar a lista de usuários que podem efetuar login no console. Se você estiver com login efetuado como um administrador do console, será possível clicar em **Incluir novos usuários** para autorizar novos usuários para o console e conceder a eles acesso de **Administrador** ou **Geral**. Os usuários com a função **Administrador** podem incluir ou excluir usuários.

## Próximas etapas
{: #ibp-v2-deploy-iks-next-steps}

Agora que seu console está pronto para uso, é possível continuar a [construir uma rede](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

## Atualizando a versão do Kubernetes de seu cluster
{: #ibp-v2-deploy-iks-updating-kubernetes}

Se você usar um cluster do {{site.data.keyword.cloud_notm}} Kubernetes Service existente, assegure-se de que a versão do Kubernetes esteja na versão 1.11 ou superior.

É possível verificar a versão do Kubernetes de seu cluster na [página de clusters do Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://console.cloud.ibm.com/containers-kubernetes/clusters) no {{site.data.keyword.cloud_notm}}, que lista todos os seus clusters em uma tabela.

Se a versão do Kubernetes não estiver na versão 1.11 ou superior, será necessário concluir as etapas a seguir para atualizar a versão do Kubernetes de seu cluster.

1. Clique no ícone de menu overflow no final da linha e selecione **Atualizar versão**. Esse processo leva cerca de uma hora para ser concluído. Se a versão for atualizada com êxito, será possível ver a versão atualizada de seu cluster na coluna **Versão do Kubernetes**.  
2. Selecione uma versão mais alta que 1.11 na lista suspensa da versão do Kubernetes e clique em **Atualizar**.
3. Clique em seu cluster e acesse a guia **Nós do trabalhador**. Marque a caixa de seleção antes do nó de trabalho que você deseja atualizar e clique em **Atualizar o Kubernetes** na barra de menus pop-up. Se o seu cluster contiver múltiplos nós de trabalho, todos eles deverão ser atualizados.

  As atualizações para os nós do trabalhador podem causar tempo de inatividade para seus apps e serviços. Sua máquina do nó do trabalhador terá a imagem reinstalada e os dados serão excluídos se não [armazenados fora do pod ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/docs/containers/cs_storage_planning.html#persistent_storage_overview).
  {:important}

![Update Kubernetes version](../images/update_k8s_version.gif)

Para obter mais informações sobre a atualização da versão do Kubernetes para um cluster do {{site.data.keyword.IBM_notm}} Kubernetes Service e nós de trabalho, veja [Atualizando clusters, nós do trabalhador e complementos ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/docs/containers/cs_cluster_update.html#update).  

Deve-se aguardar a conclusão da atualização antes de poder [continuar a implementação do {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-steps).
