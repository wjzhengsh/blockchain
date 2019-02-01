---

copyright:
  years: 2017, 2018
lastupdated: "2018-08-31"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Modo de pagamento
{: #paying-mode}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


O {{site.data.keyword.blockchainfull}} Platform cobra mensalmente com taxas de associação e taxas de peer. Os membros da rede podem pagar suas faturas com suas contas do {{site.data.keyword.cloud_notm}} que contêm o espaço para criar a instância de rede. Como alternativa, um membro de rede pode cobrir a conta de outros membros na rede, ou mesmo todos os membros, para pagar a rede inteira. O pagador também pode transferir os faturamentos de volta para outros membros, se necessário.
{:shortdesc}

Dependendo do plano de rede que você escolher e da quantia de recursos que usar, os seus pagamentos serão diferentes. Para obter mais informações sobre precificação, veja [Precificação](/docs/services/blockchain/howto/pricing.html).

## Pré-requisitos
{: #prereq}

Cada membro de rede deve ter uma conta paga do {{site.data.keyword.cloud_notm}}, por exemplo, uma conta do tipo **Pré-pago**, para que seja possível criar as instâncias de serviço do {{site.data.keyword.blockchainfull_notm}} Platform no {{site.data.keyword.cloud_notm}}. Se você não tiver uma conta, [registre](https://console.bluemix.net/registration/) uma e faça upgrade dela para uma conta paga. Para fazer upgrade de sua conta para um tipo Pré-pago, acesse **Gerenciar** > **Faturamento e uso** > **Faturamento** no console do {{site.data.keyword.cloud_notm}} e clique em **Incluir cartão de crédito**.


## Pagando as suas próprias taxas
{: #pay-your-own}

Depois de criar ou se associar a quaisquer redes de blockchain no {{site.data.keyword.blockchainfull_notm}} Platform, suas contas do {{site.data.keyword.cloud_notm}} serão cobradas por sua taxa de associação e taxas de peer.


## Pagando por outros membros de rede
{: #pay-for-others}

Um membro na rede pode cobrir as taxas de um ou mais outros membros de rede na mesma rede de blockchain. Para pagar nesse modo, o pagador e outros membros da rede devem concluir as etapas a seguir.

1. O pagador cria espaços separados do Cloud Foundry no {{site.data.keyword.cloud_notm}} para os membros aos quais pagará.
   1. Efetue login no {{site.data.keyword.cloud_notm}}.
   2. Na barra de menus, clique em **Gerenciar** > **Conta** > **Organizações do Cloud Foundry**.
   3. Clique em **Visualizar detalhes** da Organização do Cloud Foundry na qual você criará a rede. Se você não tiver uma Organização para a rede, clique no botão **Incluir uma nova Organização do Cloud Foundry** para criar uma.
   4. Clique no botão **Incluir um espaço do Cloud Foundry** para criar um espaço para outro membro da rede. Selecione a região para o espaço, dê-lhe um nome e clique em **Incluir**.  Observe que apenas o criador de espaço pode acessar o espaço criado.  
   **Nota**: se o pagador pagar por mais de um membro, ele precisará repetir essa etapa para criar espaços separados para cada um dos membros aos quais pagará.
2. O pagador convida outros membros para a conta do {{site.data.keyword.cloud_notm}} do pagador e fornece a eles acesso a seus espaços específicos. Assegure-se de que cada membro possa ter acesso a apenas um espaço.
   1. Na barra de menus, clique em **Gerenciar** > **Conta** > **Usuários**.  
   2. Clique em **Convidar usuários** e designe acesso de usuário:
      1. Especifique o endereço de e-mail de um único usuário que você convidará para a sua Organização do Cloud Foundry.
      2. Na seção **Acesso do Cloud Foundry**, faça as seleções a seguir:
         - **Organização**: a Organização do Cloud Foundry para criar a rede.
         - **Funções da organização**: auditor.
         - **Região**: a região em que você criou o espaço para esse usuário.
         - **Espaço**: o espaço que você criou para esse usuário.
         - **Funções de espaço**: desenvolvedor.
      3. Clique em **Convidar usuários**.  
   **Nota**: se o pagador pagar por mais de um membro, ele precisará repetir essa etapa para convidar outros membros e designar acesso a cada um deles.
3. O pagador cria uma rede de blockchain do {{site.data.keyword.blockchainfull_notm}} Platform e convida outros membros para a rede de blockchain no Monitor da rede. Para obter mais informações sobre convidar membros de rede, veja [Membros](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members).
4. Cada membro então recebe um e-mail de notificação que contém um convite para associar-se à rede. O membro pode concluir as etapas a seguir para se associar à rede.
   1. Clique no botão "Continuar" no e-mail de notificação, que o levará para a página de serviço de blockchain no {{site.data.keyword.cloud_notm}}.
   2. Efetue login no {{site.data.keyword.cloud_notm}} e assegure-se de usar a organização e o espaço corretos do {{site.data.keyword.cloud_notm}}, aos quais o pagador o incluiu.
      1. No console do {{site.data.keyword.cloud_notm}}, clique no avatar do perfil no canto superior direito.
      2. Escolha a conta do pagador na lista suspensa **Conta**.  Observe que a conta e a organização na qual você provisiona a instância de serviço de blockchain será aquela que será cobrada.  
   3. No catálogo do {{site.data.keyword.cloud_notm}}, procure **Blockchain** e crie uma instância de serviço.
      1. No campo **Nome do serviço**, digite um nome descritivo para o seu reconhecimento futuro.
      2. Verifique se a organização e o espaço são aqueles aos quais o pagador o incluiu.
      3. Selecione o plano de associação com o qual criar a sua instância de rede. Seu plano de associação deve ser o mesmo que aquele com o qual o pagador cria a rede.
      4. Clique **Criar**.
   4. Após você criar a instância de serviço de blockchain, siga o assistente para se associar à rede.  Para obter mais informações, veja [Associando-se a uma rede](https://console.bluemix.net/docs/services/blockchain/get_start.html#joining-a-network).

### Limitações Conhecidas
{: #pay-for-others-limitation}
- Como todos os membros estão dentro da conta do {{site.data.keyword.cloud_notm}} de pagadores, o pagador tem acesso a instâncias de blockchain de todos os membros e pode personificá-los. Portanto, esse modo de pagamento é usado melhor em ambientes PoC ou nos casos em que o pagador está manipulando todo o gerenciamento do {{site.data.keyword.blockchainfull_notm}} Platform e os membros estão recendo apenas um aplicativo.  
- Qualquer membro na Organização do Cloud Foundry do pagador pode ver todos os espaços na organização.  No entanto, eles não podem editar ou modificar as organizações porque eles não têm acessos.  
- Após incluir todos os membros na conta do {{site.data.keyword.cloud_notm}} do pagador e designar a eles acesso para provisionarem instâncias de blockchain e se associarem à rede, o pagador também estará dando aos membros acesso para criarem outros serviços, o que pode causar encargos adicionais. Todos os membros podem assinar um contrato com a confiança de que não criarão nenhum serviço não autorizado em suas contas para causar um custo extra para a conta do pagador.  

## Deixar um membro pagar por você
{: #let-other-pay}

Uma alternativa de inclusão de contas de outros membros na conta do pagador, os outros membros também podem incluir a conta do pagador em suas contas como um **Gerenciador de faturamento**. Para pagar nesse modo, o pagador e outros membros da rede devem concluir as etapas a seguir.

1. O pagador cria uma rede de blockchain do {{site.data.keyword.blockchainfull_notm}} Platform com a conta do pagador.
2. Outros membros incluem a conta do pagador em suas contas do {{site.data.keyword.cloud_notm}} como um **Gerenciador de faturamento**.
  1. Na barra de menus na parte superior do console do {{site.data.keyword.cloud_notm}}, clique em **Gerenciar** > **Conta** > **Usuários**.
  2. Na página **Usuários**, clique no botão **Convidar usuários**.
  3. Inclua a conta do pagador no campo **Endereço de e-mail**.
  4. Na seção **Acesso ao Cloud Foundry**, escolha a organização na qual você deseja incluir a conta do pagador na lista suspensa.
  5. Escolha **Gerenciador de faturamento** como as **Funções de organização**.
  6. Configure outros campos adequadamente e, em seguida, clique em **Convidar usuários**.  
3. O pagador convida outros membros para a rede de blockchain no Monitor da rede. Para obter mais informações sobre convidar membros de rede, veja [Membros](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members).
4. Cada membro então recebe um e-mail de notificação que contém um convite para associar-se à rede. O membro pode, então, se associar à rede. Para obter mais informações, consulte a **etapa 4** em [Pagando para outros membros de rede](#pay-for-others).

### Limitação conhecida
{: #let-other-pay-limitation}

Depois de incluir a conta do {{site.data.keyword.cloud_notm}} do pagador como gerenciador de faturamento na conta de outros membros e outros membros comprarem quaisquer outros serviços usando suas contas, o pagador também precisará cobrir essas taxas. Todos os membros podem assinar um contrato com a confiança de que não criarão nenhum serviço não autorizado em suas contas para causar um custo extra para a conta do pagador.  


## Transferindo Bilhetes para Outros Membros
{: #transfer-billing}

Embora os membros de rede possam ter um membro para pagar por eles, mais tarde, outros membros poderão assumir os faturamentos. É possível remover a conta do pagador de suas contas para que o pagador possa parar de pagar por você.

1. Na barra de menus na parte superior do console do {{site.data.keyword.cloud_notm}}, clique em **Gerenciar** > **Conta** > **Organizações do Cloud Foundry**.
2. Clique na organização na qual você incluirá a conta do pagador.
3. Alterne para a guia  ** Usuários ** .
4. Localize a conta do pagador na tabela de usuários e clique no link **Remover da organização**.

Depois de remover a conta do pagador de sua conta, sua conta será cobrada por sua taxa de associação da rede de blockchain e a taxa peer.
