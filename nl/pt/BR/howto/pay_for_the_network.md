---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Pagando pela rede

**Nota**: o {{site.data.keyword.blockchainfull}} Platform Starter Plan é grátis para Beta.

O {{site.data.keyword.blockchainfull_notm}} Platform cobra mensalmente com taxas de associação e taxas de peer. Dependendo do plano de rede que você escolher e da quantia de recursos que usar, os seus pagamentos serão diferentes.  Tomando o Enterprise Plan como exemplo, os membros de rede devem pagar US$ 1.000 por mês como a taxa de associação, além de US$ 1.000 por mês para cada um de seus peers na rede. A taxa mensal é cobrada diariamente rateada. Por exemplo, um membro (taxa de participação associada de U$ 1.000) de dois peers (por taxa peer de U$ 1.000 × 2 peers) precisa pagar U$ 3.000 por mês. Se o mês tiver 30 dias, o membro pagará U$ 100 (U$ 3.000/30) todos os dias.

Os membros da rede podem pagar a sua conta com as suas contas do IBM Cloud que contêm o espaço para criar a instância de rede. Como alternativa, um membro de rede pode cobrir a conta para todos os membros na rede e pagar a rede inteira.


## Pagando as suas próprias taxas
As suas contas do {{site.data.keyword.cloud_notm}} serão cobradas diariamente para a sua taxa de associação e taxas de peer.


## Pagando pela rede inteira
Um membro na rede pode cobrir as taxas para todos os membros de rede.  Para pagar neste modo pagador solo, o pagador e outros membros de rede devem concluir as etapas a seguir.

### Pré-requisitos
Todo membro de rede deve ter uma conta do {{site.data.keyword.cloud_notm}}. [Registre](https://console.bluemix.net/registration/) uma se não a tiver.

### Procedimentos
1. O pagador cria diferentes espaços do Cloud Foundry no {{site.data.keyword.cloud_notm}} para cada usuário que será convidado para a rede:
   1. Efetue login no {{site.data.keyword.cloud_notm}}.
   2. Na barra de menus, clique em **Gerenciar** > **Conta** > **Organizações do Cloud Foundry**.
   3. Clique em **Visualizar detalhes** da Organização do Cloud Foundry na qual você criará a rede.  Se você não tiver uma Organização para a rede, clique no botão **Incluir uma nova Organização do Cloud Foundry** para criar uma.
   4. Clique no botão **Incluir um Espaço do Cloud Foundry** para criar um espaço para um membro de rede.  Selecione a região para o espaço, dê-lhe um nome e clique em **Incluir**.  Observe que apenas o criador de espaço pode acessar o espaço criado.
   5. Repita a etapa 4 para criar espaços separados para todos os usuários que serão convidados para a rede.
2. O pagador convida outros usuários para a conta do {{site.data.keyword.cloud_notm}} do pagador e dá a eles acesso ao espaço específico.  Cada usuário tem acesso a apenas um espaço.
   1. Na barra de menus, clique em **Gerenciar** > **Conta** > **Usuários**.  
   2. Clique em **Convidar usuários** e designe acesso de usuário:
      1. Especifique o endereço de email de um único usuário que você convidará para a sua Organização do Cloud Foundry.
      2. Na seção **Acesso do Cloud Foundry**, faça as seleções a seguir:
         - **Organização**: a Organização do Cloud Foundry para criar a rede.
         - **Funções da organização**: auditor.
         - **Região**: a região em que você criou o espaço para esse usuário.
         - **Espaço**: o espaço que você criou para esse usuário.
         - **Funções de espaço**: desenvolvedor.
      3. Clique em **Convidar usuários**.
   3. Repita as etapas para convidar usuários e designar acesso de usuário para cada usuário.
3. No painel do {{site.data.keyword.blockchainfull_notm}} Platform, o pagador convida os usuários para a rede de blockchain. Para obter mais informações sobre convidar membros de rede, veja [Membros](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members).
4. Cada usuário, então, recebe um e-mail de notificação que contém um convite para ingressar na rede.  O usuário pode concluir as etapas a seguir para se associar à rede.
   1. Clique no botão "Continuar" no e-mail de notificação, que o levará para a página de serviço de blockchain no {{site.data.keyword.cloud_notm}}.
   2. Certifique-se de usar a organização e o espaço corretos do {{site.data.keyword.cloud_notm}}.
      1. Efetue login no {{site.data.keyword.cloud_notm}} e clique no avatar de perfil na parte superior direita.
      2. Escolha a conta do pagador na lista suspensa **Conta**.  Observe que a conta e a organização na qual você provisiona a instância de serviço de blockchain será aquela que será cobrada.  
   4. Na página de serviço de blockchain, crie a sua instância de serviço de blockchain:
      1. No campo **Nome do serviço**, digite um nome descritivo para o seu reconhecimento futuro.
      2. Verifique se a organização e o espaço são aqueles para os quais o pagador convidou você.
      3. Selecione o plano de associação com o qual criar a sua instância de rede.
      4. Clique **Criar**.
   5. Após você criar a instância de serviço de blockchain, siga o assistente para se associar à rede.  Para obter mais informações, veja [Associando-se a uma rede](https://console.bluemix.net/docs/services/blockchain/get_start.html#joining-a-network).

### Limitações Conhecidas
- Como todos os membros estão dentro da conta do {{site.data.keyword.cloud_notm}} de pagadores, o pagador tem acesso a instâncias de blockchain de todos os membros e pode personificá-los.  Portanto, esse modo de pagamento é melhor utilizado para ambientes POC ou em casos em que o pagador estiver manipulando todo o gerenciamento do {{site.data.keyword.blockchainfull_notm}} Platform e os membros estiverem sendo fornecidos com apenas um aplicativo.  
- Após incluir todos os membros na conta do {{site.data.keyword.cloud_notm}} do pagador e designar a eles acesso para provisionarem instâncias de blockchain e se associarem à rede, o pagador também estará dando aos membros acesso para criarem outros serviços, o que pode causar encargos adicionais.  
- Qualquer membro na Organização do Cloud Foundry do pagador pode ver todos os espaços na organização.  No entanto, eles não podem editar ou modificar as organizações porque eles não têm acessos.
