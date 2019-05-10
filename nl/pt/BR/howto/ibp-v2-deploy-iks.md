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

# Introdução ao beta 2.0 grátis do {{site.data.keyword.blockchainfull_notm}} Platform
{: #ibp-v2-deploy-iks}

O {{site.data.keyword.blockchainfull}} Platform 2.0 é uma liberação beta grátis que inclui o console do {{site.data.keyword.blockchainfull_notm}} Platform, uma GUI que pode simplificar e acelerar sua jornada para implementar e gerenciar componentes de blockchain. Este tutorial descreve como começar a usar o {{site.data.keyword.blockchainfull_notm}} Platform 2.0 e usar o console para implementar e gerenciar componentes de blockchain em seu cluster do {{site.data.keyword.cloud_notm}} Kubernetes Service no {{site.data.keyword.cloud_notm}}. Para obter mais informações sobre o Kubernetes e o {{site.data.keyword.cloud_notm}} Kubernetes Service, consulte [Kubernetes](/docs/services/blockchain/reference/k8s.html "Kubernetes").
{:shortdesc}

**Público-alvo:** este tópico destina-se a administradores de sistema responsáveis pela configuração de um cluster Kubernetes no {{site.data.keyword.cloud_notm}} e pela implementação do {{site.data.keyword.blockchainfull_notm}} Platform.

Após vincular seu {{site.data.keyword.blockchainfull_notm}} Platform ao seu cluster do {{site.data.keyword.cloud_notm}} Kubernetes, é possível ativar o console para criar e gerenciar seus componentes de blockchain. Ao usar um cluster do Kubernetes Service para implementar o {{site.data.keyword.blockchainfull_notm}} Platform 2.0, você experimenta os importantes benefícios a seguir:

- **Controle:** você controla e gerencia seus componentes e certificados de blockchain por meio de um console central. Implemente somente os componentes necessários para seus negócios e inclua mais à medida que sua necessidade aumentar.
- **Implementação flexível baseada em Kubernetes:** é possível aproveitar as opções de cálculo (CPU, memória, armazenamento) para o cluster Kubernetes e alavancar as opções de HA e DR integrados.


## Considerações
{: #ibp-v2-deploy-iks-considerations}

Antes de implementar o console, assegure-se de que entenda as considerações a seguir:

- O {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta foi desenvolvido com o Hyperledger Fabric v1.4.
- Todos os peers implementados com o free 2.0 beta usam CouchDB como seu banco de dados de estado.
- Você tem a opção de usar um cluster Kubernetes gratuito para avaliação da oferta beta, no entanto, a capacidade e o desempenho são limitados, nenhum de seus dados pode ser migrado e o cluster é excluído depois de 30 dias.
- Você é responsável pelo gerenciamento do monitoramento de funcionamento, da segurança e da criação de log do cluster Kubernetes. Consulte essas [informações ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/docs/containers/cs_responsibilities.html#your-responsibilities-by-using-ibm-cloud-kubernetes-service "Responsabilidades de gerenciamento de cluster") para obter detalhes sobre o que o {{site.data.keyword.cloud_notm}} gerencia e sobre o que é de sua responsabilidade.
- Você também é responsável por monitorar o uso de recursos de seu cluster Kubernetes usando o painel do Kubernetes. Se você precisar aumentar a capacidade de armazenamento ou o desempenho de seu cluster, consulte estas informações sobre como [modificar seu volume existente ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/docs/containers/cs_storage_file.html#change_storage_configuration "Mudando o tamanho e o IOPS de seu dispositivo de armazenamento existente").
- Você é responsável por gerenciar e proteger seus certificados, suas chaves públicas e privadas. A IBM não armazena seus certificados no cluster Kubernetes.
- A oferta beta grátis 2.0 está disponível apenas na região de **Dallas** do serviço do {{site.data.keyword.cloud_notm}} Kubernetes. Essa região inclui data centers em Dallas, São José, Houston e Brasil. Portanto, todos os componentes de blockchain podem residir em qualquer um desses quatro data centers. Eles não são implementados em outro lugar.
- Embora a oferta beta seja grátis, ainda será necessário pagar pelo cluster Kubernetes, se você escolher um cluster pago.
- O Kubernetes deve ser a versão 1.11 ou mais recente no cluster Kubernetes do {{site.data.keyword.cloud_notm}}. Use estas instruções para [fazer upgrade de seus clusters novos e existentes](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes) para esta versão.

## Tutorial de vídeo
{: #ibp-v2-deploy-video}

Assista ao vídeo a seguir para saber mais sobre o beta grátis do {{site.data.keyword.blockchainfull}} Platform 2.0 e como é possível começar a implementar o beta grátis do {{site.data.keyword.blockchainfull_notm}} Platform 2.0.

<iframe class="embed-responsive-item" id="youtubeplayer" title="Vídeo do IBM Blockchain Platform free 2.0 beta: tutorial de implementação" type="text/html" width="640" height="390" src="https://www.youtube.com/embed/gPnkVQiHRqk" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen> </iframe>

## Antes de iniciar
{: #ibp-v2-deploy-iks-prereq}

Antes de iniciar:

- Certifique-se de que você tenha uma conta paga do [{{site.data.keyword.cloud_notm}} ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/catalog/services/blockchain "Catálogo"). Se você não tiver uma conta:
   1. Clique no botão  ** Inscrever-se ** .
   2. Após você criar uma conta de avaliação grátis, faça upgrade dela para um tipo **Pré-pago** acessando **Gerenciar** > **Faturamento e uso** > **Faturamento** no console do {{site.data.keyword.cloud_notm}} e clicando em **Incluir cartão de crédito**.
   3. Certifique-se de que o usuário tenha as funções Administrador e Gerenciador para o cluster Kubernetes que ele vinculará à sua instância de serviço de blockchain. Consulte essas etapas sobre [como designar as funções de acesso do Kubernetes](#ibp-v2-deploy-iks-k8x-access-roles) para obter mais informações.

Ao planejar usar a instância de serviço no contexto de uma solução mais ampla de toda a organização, recomenda-se que as organizações participantes usem um endereço de e-mail funcional para criar sua rede. Nesse caso, o acesso à rede não depende da disponibilidade de um único indivíduo.
{:tip}  

- Se você planeja usar um cluster existente do {{site.data.keyword.cloud_notm}} Kubernetes Service, verifique a versão do Kubernetes e faça upgrade dela para 1.11 ou superior, se necessário. Para obter mais informações sobre como determinar qual versão do Kubernetes seu cluster está executando e como fazer upgrade da versão, veja [Atualizando a versão do Kubernetes de seu cluster](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes).

### Navegadores
{: #ibp-v2-deploy-iks-browsers}
A lista a seguir especifica o software do navegador mínimo necessário para o console do {{site.data.keyword.blockchainfull_notm}} Platform:

- Chrome: versão mais recente para seu sistema operacional
- Firefox: versões mais recentes regulares (não ESR) para seu sistema operacional
- Safari: versão mais recente para Mac
- Borda: v44.17763.1.0 ou mais recente

### Recursos necessários
{: #ibp-v2-deploy-iks-resources-required}

#### Clusters grátis
{: #ibp-v2-deploy-iks-resources-required-free}

Para implementar o console do {{site.data.keyword.blockchainfull_notm}} Platform em um cluster do {{site.data.keyword.cloud_notm}} Kubernetes Service, é necessário assegurar que o cluster Kubernetes atenda aos requisitos mínimos de recurso de hardware:

|Tipo de cluster do Kubernetes | Caso de uso | CPU | RAM | Nós do Trabalhador |
|-----------|------|-----|-----------------------|
|Padrão (Recomendado) | Adequado para MVPs | 4 (Compartilhado) | 16 GB (compartilhado)|múltiplos|
|Grátis | Adequado para avaliação | 2 | 4 GB | 1 |  

Esses recursos são suficientes para teste e experimentação. O [tutorial Construir uma rede](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network), no qual você cria dois peers, duas CAs e um solicitador, ocupa aproximadamente 1.1 CPU, permitindo algum espaço adicional para propósitos de teste (por exemplo, a criação de diversos canais, na qual cada um possuirá um livro-razão distinto). Se você usar um cluster Kubernetes gratuito, esteja ciente de que ele será excluído após a avaliação de 30 dias e que todos os ativos associados serão removidos. Além disso, o desempenho é significativamente mais lento em um cluster grátis.
{:note}

#### Clusters pagos
{: #ibp-v2-deploy-iks-resources-required-paid}

As implementações no nível de produção do {{site.data.keyword.blockchainfull_notm}} Platform serão realizadas em um cluster pago do serviço {{site.data.keyword.cloud_notm}} Kubernetes. O tamanho e a configuração desse cluster dependerão das necessidades de seu caso de uso específico. Implementações maiores precisarão necessariamente ser realizadas em clusters maiores. Você decide quão maior o seu cluster será do que a implementação que você projetou. Ter pelo menos algum espaço livre é desejável, pois isso permitirá que peers e solicitadores se associem a canais adicionais e assumam um rendimento maior sem ter que implementar recursos adicionais em seu cluster Kubernetes **antes** de ajustar o tamanho de seus nós. Para obter mais informações sobre como esses valores são ajustados, consulte [Realocando recursos](/docs/services/blockchain/howto/ibp-console-govern.html#ibp-console-govern-reallocate-resources).

A criação de uma implementação inicial de tamanho suficiente para permitir o crescimento é particularmente importante para usuários que escolherão não usar o dimensionador automático do serviço [{{site.data.keyword.cloud_notm}} Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](/docs/containers?topic=containers-ca#ca "Dimensionador automático do serviço {{site.data.keyword.cloud_notm}} Kubernetes"), que pode assumir parte da carga de implementações de nós e pods adicionais para o usuário.

Embora seja mais simples ter recursos suficientes implementados no serviço {{site.data.keyword.cloud_notm}} Kubernetes e ser capaz de expandir seus pods e nós do trabalhador conforme adequado, sem ter que aumentar primeiro sua implementação do cluster Kubernetes, quanto maior a implementação no serviço Kubernetes, mais cara ela será. Os usuários precisarão considerar suas opções cuidadosamente e reconhecer as trocas que estão fazendo, independentemente da opção que escolherem.

Para entender a quantidade necessária de armazenamento e cálculo em seu cluster, consulte esse gráfico, que contém os padrões atuais para o peer, o solicitador e a CA:

| **Componente** (todos os contêineres) | CPU (em millicpus) | CPU (em CPUs) | Memória (em megabytes) | Memória (em gigabytes) | Armazenamento (em gigabytes) |
|--------------------------------|--------------------|---------------|-----------------------|-----------------------|------------------------|
| **Peer**                       | 1100               | 1.1           | 2200                  | 2.2                   | 200                    |
| **CA**                         | 300                | .3            | 600                   | .6                    | 10                     |
| **Solicitador**                    | 450                | .45           | 900                   | .9                    | 100                    |



## Etapa um: criar uma instância de serviço no {{site.data.keyword.cloud_notm}}
{: #ibp-v2-deploy-iks-create-service-instance}

Use as etapas a seguir para criar uma instância de serviço do {{site.data.keyword.blockchainfull_notm}} Platform 2.0 no {{site.data.keyword.cloud_notm}}.

1. Localize o [Serviço de blockchain ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/catalog/services/blockchain) no Catálogo do {{site.data.keyword.cloud_notm}} ou procure por `Blockchain` em sua página Catálogo do {{site.data.keyword.cloud_notm}}.
2. Opcionalmente, é possível renomear o **Nome do serviço** para sua instância para que seja possível reconhecê-la facilmente no futuro.
3. Para Beta, **Dallas** é a única região disponível e não pode ser mudada.
4. É possível deixar os campos de grupo de recursos e de tags sem mudanças.
5. Clique em **Criar** para provisionar a instância de serviço.

## Etapa dois: Implementar o  {{site.data.keyword.blockchainfull_notm}}  Platform 2.0
{: #ibp-v2-deploy-iks-steps}

É possível seguir a orientação para implementar o {{site.data.keyword.blockchainfull_notm}} Platform 2.0 imediatamente depois de criar a instância de serviço.

1. A etapa  ** Welcome & pré-ites ** . Se você já tiver um cluster do {{site.data.keyword.IBM_notm}} Kubernetes Service existente na região **Dallas** e desejar usá-lo para o seu serviço de blockchain, marque a caixa de seleção. **Se você usar um cluster existente, será possível ignorar a próxima etapa, mas certifique-se de que a versão do Kubernetes seja a v1.11 ou mais recente**. Clique em  ** Continuar **.
2. A etapa  ** Criar cluster ** . Se você marcar a caixa de seleção na etapa 1 para usar um cluster Kubernetes existente, essa etapa será ignorada. Caso contrário, clique em **Criar um novo cluster**, que ativa o painel do {{site.data.keyword.cloud_notm}} Kubernetes para criar um cluster. Para obter mais informações, veja [Introdução ao {{site.data.keyword.cloud_notm}} Kubernetes Service ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](/docs/containers/container_index.html). Permita tempo extra para que esse processo seja concluído.
  - Independentemente do tipo de cluster escolhido, deve-se selecionar o local do cluster Kubernetes de **Dallas** para a liberação Beta.
  - Escolha **Cluster padrão (recomendado):** se precisar de uma opção de prazo mais longo que inclua diversos nós para a alta disponibilidade. **Certifique-se de escolher o Kubernetes versão v1.11 ou mais recente.** Para implementar um cluster pago, consulte [Criando um cluster padrão ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](/docs/containers?topic=containers-clusters#clusters_ui_standard "Criando um cluster padrão"). Observe que, se desejar a alta disponibilidade ou a recuperação de desastre, será necessário tomar uma decisão sobre a classe de armazenamento que está sendo usada. A classe de armazenamento `default` no cluster será usada pelo fornecimento dinâmico. Portanto, os clientes podem configurar qualquer classe de armazenamento como o padrão. Para obter mais informações, consulte [Decidindo a configuração do armazenamento de arquivos ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](/docs/containers?topic=containers-file_storage#file_predefined_storageclass "Decidindo a configuração do armazenamento de arquivos").
  - Escolha **Cluster grátis:** se você planeja usar o cluster por menos de 30 dias. **Observe** que não é possível migrar de um cluster grátis para um cluster pago. O tipo grátis de cluster oferece armazenamento limitado e rendimento de transação. Para obter instruções sobre o que fazer quando o cluster Kubernetes expirar, consulte este tópico em [Expiração do cluster Kubernetes](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-cluster-expiration).
  - Para obter mais informações sobre as diferenças entre os clusters Kubernetes grátis e pago no {{site.data.keyword.cloud_notm}}, veja [Comparação de clusters grátis e padrão ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/docs/containers?topic=containers-cluster_types#cluster_types "Comparação de clusters grátis e padrão").  

   Deve-se retornar a essa guia em seu navegador depois de criar o cluster para que seja possível concluir o processo de implementação do {{site.data.keyword.blockchainfull_notm}} Platform 2.0.  
   {:important}  

  É necessário aguardar até que seu cluster seja implementado com êxito. Em seguida, clique no botão **Eu tenho um cluster**.
3. Sua versão do Kubernetes em execução em seu cluster deve estar na versão 1.11 ou superior. Siga estas [etapas](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-updating-kubernetes) para verificar sua versão do cluster e fazer upgrade dela, se necessário. Em seguida, volte e continue com estas instruções.
4. A etapa  ** Implementar no cluster ** . Selecione o cluster Kubernetes no qual você deseja implementar o {{site.data.keyword.blockchainfull_notm}} Platform 2.0 na lista suspensa e clique em **Implementar no cluster**.  

  Se o cluster Kubernetes não estiver visível na lista suspensa, isso poderá ser causado pelas condições a seguir:
  - O processo de criação do cluster pode levar até 60 minutos para ser concluído. Se você criou um cluster, permita algum tempo extra até que o estado de seu cluster se torne **Normal**.
  - Clusters que estão fora da região **Dallas** não são visíveis e não podem ser usados.
  - Certifique-se de que você não esteja usando a versão ESR do Firefox. Se estiver, alterne para outro navegador, como Chrome, e tente novamente.

5. A etapa  ** Ativar console ** . Após que seu {{site.data.keyword.blockchainfull_notm}} Platform 2.0 tiver sido implementado com êxito, clique em **Ativar o {{site.data.keyword.blockchainfull_notm}} Platform** para abrir o console do {{site.data.keyword.blockchainfull_notm}} Platform. Pode levar alguns minutos para que o botão seja ativado enquanto o console está sendo provisionado.

## (Opcional) Inclua usuários adicionais no console
{: #ibp-v2-deploy-iks-add-users}

Por padrão, o console usa o [{{site.data.keyword.cloud_notm}} Identity and Access Management (IAM) ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](/docs/iam?topic=iam-iamoverview#iamoverview "IBM Cloud Identity and Access Management") como o provedor de serviços de identidade do {{site.data.keyword.cloud_notm}}. Seu console do {{site.data.keyword.blockchainfull_notm}} Platform é provisionado configurando o endereço de e-mail do proprietário do {{site.data.keyword.IBM_notm}} como Administrador do console. Como um Administrador, esse usuário está autorizado a conceder aos outros usuários acesso ao console por meio de seus endereços de e-mail.  Consulte estas instruções sobre como [incluir e remover usuários do console](/docs/services/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove) para obter mais informações.

## Próximas etapas
{: #ibp-v2-deploy-iks-next-steps}

Agora que seu console está pronto para uso, é possível avançar para o [tutorial Construir uma rede](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network).

## Atualizando a versão do Kubernetes de seu cluster
{: #ibp-v2-deploy-iks-updating-kubernetes}

Se você usar um cluster do {{site.data.keyword.cloud_notm}} Kubernetes Service existente, assegure-se de que a versão do Kubernetes esteja na versão 1.11 ou superior.

É possível verificar a versão do Kubernetes de seu cluster na [página de clusters do Kubernetes ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/kubernetes/clusters) no {{site.data.keyword.cloud_notm}}, que lista todos os seus clusters em uma tabela.

Se a versão do Kubernetes não estiver na versão 1.11 ou superior, será necessário concluir as etapas a seguir para atualizar a versão do Kubernetes de seu cluster.

1. Clique no ícone de menu overflow no final da linha e selecione **Atualizar versão**. Esse processo leva cerca de uma hora para ser concluído. Se a versão for atualizada com êxito, será possível ver a versão atualizada de seu cluster na coluna **Versão do Kubernetes**.  
2. Selecione a versão 1.11 ou mais recente do Kubernetes na lista suspensa de versões do Kubernetes e clique em **Atualizar**.
3. Clique em seu cluster e acesse a guia **Nós do trabalhador**. Marque a caixa de seleção antes do nó de trabalho que você deseja atualizar e clique em **Atualizar o Kubernetes** na barra de menus pop-up. Se o seu cluster contiver múltiplos nós de trabalho, todos eles deverão ser atualizados.

  As atualizações para os nós do trabalhador podem causar tempo de inatividade para seus apps e serviços. Sua máquina do nó do trabalhador terá a imagem reinstalada e os dados serão excluídos se não [armazenados fora do pod ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/docs/containers/cs_storage_planning.html#persistent_storage_overview).
  {:important}

![Update Kubernetes version](../images/update_k8s_version.gif)

Para obter mais informações sobre a atualização da versão do Kubernetes para um cluster do {{site.data.keyword.IBM_notm}} Kubernetes Service e nós de trabalho, veja [Atualizando clusters, nós do trabalhador e complementos ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](/docs/containers?topic=containers-update#update).  

Deve-se aguardar a conclusão da atualização antes de poder [continuar a implementação do {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/services/blockchain/howto/ibp-v2-deploy-iks.html#ibp-v2-deploy-iks-steps).

## Como designar funções de acesso do Kubernetes
{: #ibp-v2-deploy-iks-k8x-access-roles}

O usuário que vincula a instância de serviço de blockchain ao cluster Kubernetes deve ter as funções Administrador e Gerenciador no Kubernetes.
Para configurar esse acesso, deve-se concluir as etapas a seguir:
   1. No painel do {{site.data.keyword.cloud_notm}}, clique na lista suspensa **Gerenciar** e, em seguida, em **Acessar (IAM)**.
   2. No menu de navegação à esquerda, clique em **Usuários** e no ID do usuário que vinculará a instância de serviço para o cluster Kubernetes.
   3. Clique em **Políticas de acesso** e, em seguida, em **Designar acesso**.
   4. Clique no quadro **Designar acesso aos recursos**.
   5. Na lista suspensa **Serviços**, selecione **Serviço Kubernetes**.
   6. Marque as funções **Administrador** e **Gerenciador** para esse usuário.
   7. Clique em **Designar**.

![Update Kubernetes version](../images/k8sAccess.gif)

Para obter mais informações sobre o controle de acesso do Kubernetes, consulte [como escolher a política de acesso e a função ideais para seus usuários](/docs/containers?topic=containers-users#access_roles).

## Excluindo uma Instância de Serviço
{: #ibp-v2-deploy-iks-delete-service-instance}

Quando você não precisar mais de sua instância de serviço, ela poderá ser excluída do cluster Kubernetes para liberar recursos. É possível usar a interface com o usuário do IBM Cloud para excluir uma instância de serviço do {{site.data.keyword.blockchainfull_notm}} Platform free 2.0 beta.

1. Navegue para o [painel do {{site.data.keyword.cloud_notm}} ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://cloud.ibm.com/ "Painel").
2. Localize o quadro **Resumo de recursos** e clique em **Serviços**.
3. Sob a seta **Serviços**, localize a instância de serviço que você deseja excluir e clique em **Excluir** no menu Ações.

Se a exclusão da sua instância de serviço falhar, isso pode ser porque o cluster Kubernetes não está acessível. Se isso ocorrer, abra um [chamado de suporte](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-cases) para que a instância de serviço seja excluída para você.
