---

copyright:
  years: 2018,2019
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Mode de paiement
{: #paying-mode}

Les frais d'{{site.data.keyword.blockchainfull}} Platform sont mensuels avec des frais d'appartenance et des frais d'homologue. Les membres réseau peuvent régler leur facture avec leurs comptes {{site.data.keyword.cloud_notm}} qui contiennent l'espace nécessaire à la création de l'instance de réseau. Un membre réseau peut aussi couvrir les factures d'autres membres du réseau et payer pour l'intégralité du réseau. Le débiteur peut aussi transférer les factures à d'autres membres si nécessaire.
{:shortdesc}

Selon le plan de réseau que vous choisissez et la quantité de ressources que vous utilisez, vos paiements peuvent varier. Pour plus d'informations, voir [Tarification](/docs/services/blockchain/howto/pricing.html#ibp-pricing).

Remarque : Lorsque plusieurs organisations génèrent un consortium, il leur est recommandé d'utiliser une adresse e-mail fonctionnelle pour créer leur réseau. Dans ce cas, l'accès au réseau ne dépend pas de la disponibilité d'une personne.


## Prérequis
{: #paying-mode-prereq}

Chaque membre du réseau doit disposer d'un compte {{site.data.keyword.cloud_notm}} payant, par exemple du type **Paiement à la carte**, afin de pouvoir créer des instances de service {{site.data.keyword.blockchainfull_notm}} Platform sur {{site.data.keyword.cloud_notm}}. Si vous n'avez pas de compte, [souscrivez à un compte](https://cloud.ibm.com/registration/) et mettez-le à niveau vers un compte payant. Pour mettre à niveau votre compte vers un compte de type Paiement à la carte, accédez à **Gérer** > **Facturation et utilisation** > **Facturation** dans la console {{site.data.keyword.cloud_notm}}, puis cliquez sur **Ajouter une carte de crédit**.


## Paiement de vos frais
{: #paying-mode-pay-your-own}

Après que vous ayez créé ou rejoint des réseaux de blockchain avec {{site.data.keyword.blockchainfull_notm}} Platform, votre compte {{site.data.keyword.cloud_notm}} sera facturé pour vos frais d'appartenance et vos frais d'homologues.


## Paiement pour d'autres membres du réseau
{: #paying-mode-pay-for-others}

Un membre du réseau peut couvrir les frais d'un ou de plusieurs autres membres du réseau au sein du même réseau de blockchain. Pour ce type de paiement, le débiteur et les autres membres réseau doivent suivre la procédure ci-après.

1. Le débiteur crée des espaces Cloud Foundry distincts dans {{site.data.keyword.cloud_notm}} pour les membres pour lesquels le débiteur va payer.
   1. Connectez-vous à {{site.data.keyword.cloud_notm}}.
   2. Dans la barre de menus, cliquez sur **Gérer** > **Compte** > **Organisations Cloud Foundry**.
   3. Cliquez sur **Afficher Détails** en regard de l'organisation Cloud Foundry pour laquelle vous allez créer le réseau. Si vous n'avez pas d'organisation pour le réseau, cliquez sur le bouton **Ajouter une nouvelle organisation Cloud Foundry** pour en créer une.
   4. Cliquez sur le bouton **Ajouter un espace Cloud Foundry** afin de créer un espace pour un autre membre réseau. Sélectionnez la région de l'espace, donnez-lui un nom, puis cliquez sur **Ajouter**.  Notez que seul le créateur de l'espace peut accéder à l'espace créé.  
   **Remarque **: Si le débiteur paye pour plusieurs membres, il doit recommencer cette étape pour créer des espaces distincts pour chacun des membres pour lesquels il va payer.
2. Le débiteur invite d'autres membres à rejoindre le compte {{site.data.keyword.cloud_notm}} du débiteur et il leur accorde l'accès à leur espace spécifique. Assurez-vous que chaque utilisateur n'a accès qu'à un seul espace.
   1. Dans la barre de menus, cliquez sur **Gérer** > **Compte** > **Utilisateurs**.  
   2. Cliquez sur **Inviter des utilisateurs** et affectez-leur un accès utilisateur :
      1. Indiquez l'adresse de courrier électronique d'un seul utilisateur que vous allez inviter à rejoindre votre organisation Cloud Foundry.
      2. Dans la section **Accès Cloud Foundry**, effectuez les sélections suivantes :
         - **Organisation** : Organisation Cloud Foundry pour la création du réseau.
         - **Rôles d'organisation** : Auditeur.
         - **Région** : Région dans laquelle vous avez créé l'espace de cet utilisateur.
         - **Espace** : Espace que vous avez créé pour cet utilisateur.
         - **Rôles d'espace** : Développeur.
      3. Cliquez sur **Inviter des utilisateurs**.  
   **Remarque **: Si le débiteur paye pour plusieurs membres, il doit recommencer cette étape pour inviter d'autres membres et leur accéder un accès à chacun.
3. Le débiteur crée un réseau de blockchain {{site.data.keyword.blockchainfull_notm}} Platform et invite d'autres membres au réseau de blockchain dans le Moniteur réseau du réseau. Pour plus d'informations sur les membres réseau, voir [Membres](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members).
4. Chaque membre reçoit ensuite une courrier électronique de notification contenant une invitation à rejoindre le réseau. Le membre peut procéder comme suit pour rejoindre le réseau.
   1. Cliquez sur le bouton "Poursuivre" dans le courrier électronique de notification. Vous êtes alors redirigé vers la page de service blockchain dans {{site.data.keyword.cloud_notm}}.
   2. Connectez-vous à {{site.data.keyword.cloud_notm}} et vérifiez que vous utilisez l'organisation et l'espace {{site.data.keyword.cloud_notm}} corrects, que le débiteur vous ajoute.
      1. Sur la console {{site.data.keyword.cloud_notm}}, cliquez sur l'avatar de profil dans l'angle supérieur droit.
      2. Choisissez le compte du débiteur dans la liste déroulante **Compte**.  Notez que le compte et l'organisation dans laquelle vous mettez à disposition l'instance de service blockchain sont ceux qui sont facturés.  
   3. Dans le catalogue {{site.data.keyword.cloud_notm}}, recherchez **Blockchain** et créez une instance de service.
      1. Dans la zone **Nom du service**, entrez un nom descriptif à des fins de reconnaissance future.
      2. Vérifiez que l'organisation et l'espace sont ceux auxquels le débiteur vous ajoute.
      3. Sélectionnez le plan d'appartenance avec lequel vous allez créer votre instance de réseau. Votre plan d'appartenance doit être le même que celui avec lequel le débiteur crée le réseau.
      4. Cliquez sur **Créer**.
   4. Dès que vous avez créé l'instance de service blockchain, suivez les instructions de l'assistant pour rejoindre le réseau.  Pour plus d'informations, voir [Rejoindre un réseau](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-join-nw).

### Limitations connues
{: #paying-mode-limitation}
- Etant donné que tous les membres figurent dans le compte {{site.data.keyword.cloud_notm}} des débiteurs, le débiteur a accès à toutes les instances de blockchain des membres et il peut en emprunter l'identité. Par conséquent, ce mode de paiement est le mieux adapté pour les environnements PoC, ou dans le cas où le débiteur traite toutes les opérations de gestion d'{{site.data.keyword.blockchainfull_notm}} Platform et lorsque les membres sont fournis avec une seule application.  
- Tous les membres de l'organisation Cloud Foundry du débiteur peuvent voir tous les espaces au sein de l'organisation.  Toutefois, ils ne peuvent pas éditer ou modifier les organisations car ils ne disposent pas des accès.  
- Sachez que tous les membres de l'organisation Cloud Foundry du débiteur peuvent voir les ID IBM des autres membres de la même organisation Cloud Foundry.  
- Une fois que tous les membres ont été ajoutés au compte {{site.data.keyword.cloud_notm}} du débiteur et que l'accès leur est accordé pour mettre à disposition les instances de blockchain et rejoindre le réseau, le débiteur accorde également aux membres l'accès à la création d'autres services, ce qui peut entraîner des frais supplémentaires. Tous les membres peuvent signer un accord indiquant qu'ils ne créeront pas de services non autorisés dans leurs comptes pour provoquer un coût supplémentaire sur le compte du débiteur.  

## Comment laisser un membre payer pour vous
{: #paying-mode-let-other-pay}

Au lieu d'ajouter les comptes d'autres membres au compte du débiteur, il est aussi possible que d'autres membres ajoutent le compte du débiteur à leurs comptes en tant que **Responsable de la facturation**. Pour ce type de paiement, le débiteur et les autres membres réseau doivent suivre la procédure ci-après.

1. Le débiteur crée un réseau de blockchain {{site.data.keyword.blockchainfull_notm}} Platform avec le compte du débiteur.
2. Les autres membres ajoutent le compte du débiteur à leur compte {{site.data.keyword.cloud_notm}} en tant que **Responsable de la facturation**.
  1. Dans la barre de menus en haut de la console {{site.data.keyword.cloud_notm}}, cliquez sur **Gérer** > **Compte** > **Utilisateurs**.
  2. Sur la page **Utilisateurs**, cliquez sur le bouton **Inviter des utilisateurs**.
  3. Ajoutez le compte du débiteur sous la zone **Adresse de courrier électronique**.
  4. Sous la section **Cloud Foundry access**, choisissez l'organisation que vous voulez ajouter au compte du débiteur à partir de la liste déroulante.
  5. Choisissez **Responsable de la facturation** pour **Rôles d'organisation**.
  6. Configurez les autres zones comme il convient, puis cliquez sur **Inviter des utilisateurs**.  
3. Le débiteur invite d'autres membres au réseau de blockchain dans le Moniteur réseau du réseau. Pour plus d'informations sur les membres réseau, voir [Membres](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members).
4. Chaque membre reçoit ensuite une courrier électronique de notification contenant une invitation à rejoindre le réseau. Le membre peut ensuite rejoindre le réseau. Pour plus d'informations, voir l'**étape 4** sous [Paiement pour d'autres membres du réseau](/docs/services/blockchain/howto/paying_mode.html#paying-mode-pay-for-others).

### Limitation connue
{: #paying-mode-let-other-pay-limitation}

Après l'ajout du compte {{site.data.keyword.cloud_notm}} du débiteur en tant que responsable de la facturation au compte d'autres membres et à l'achat d'autres membres d'autres services à l'aide de leur compte, le débiteur doit également couvrir ces frais. Tous les membres peuvent signer un accord indiquant qu'ils ne créeront pas de services non autorisés dans leurs comptes pour provoquer un coût supplémentaire sur le compte du débiteur.  


## Transfert des facturations à d'autres membres
{: #paying-mode-transfer-billing}

Tandis que des membres réseau peuvent avoir un membre qui paie pour eux, d'autres membres peuvent plus tard prendre le relais des facturations. Vous pouvez ainsi retirer le compte du débiteur de vos comptes afin que le débiteur puisse cesser de payer pour vous.

1. Dans la barre de menus en haut de la console {{site.data.keyword.cloud_notm}}, cliquez sur **Gérer** > **Compte** > **Organisations Cloud Foundry**.
2. Cliquez sur l'organisation à laquelle vous ajoutez le compte du débiteur.
3. Passez à l'onglet **Utilisateurs**.
4. Recherchez le compte du débiteur dans le tableau des utilisateurs et cliquez sur le lien **Retirer de l'organisation**.

Une fois que vous avez retiré le compte du débiteur de votre compte, ce dernier est facturé des frais d'appartenance des membres de votre réseau de blockchain et ds frais d'homologue.
