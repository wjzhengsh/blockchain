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

# Paiement des frais du réseau

**Remarque **: Le plan Starter de la plateforme {{site.data.keyword.blockchainfull}} est gratuit dans sa version bêta.

Les frais de la plateforme {{site.data.keyword.blockchainfull_notm}} sont mensuels avec des frais d'appartenance et des frais d'homologue. Selon le plan de réseau que vous choisissez et la quantité de ressources que vous utilisez, vos paiements peuvent varier.  Si l'on prend l'exemple du plan Enterprise, les membres réseau doivent régler 1 000 dollars par mois de frais d'appartenance et 1 000 euros mensuels supplémentaires pour chacun des homologues du réseau. Les frais mensuels sont facturés par jour au prorata. Par exemple, une organisation (avec des frais d'appartenance de 1 000 dollars) ayant deux homologues (frais par homologue de 1 000 dollars X 2 homologues) doit payer 3 000 dollars par mois. Si le mois comporte 30 jours, le membre paie 100 dollars (3 000/30) par jour.

Les membres réseau peuvent régler leur facture avec leurs comptes IBM Cloud qui contiennent l'espace nécessaire à la création de l'instance de réseau. Un membre réseau peut aussi couvrir les factures de tous les membres du réseau et payer pour l'intégralité du réseau.


## Paiement de vos frais
Les frais de vos comptes {{site.data.keyword.cloud_notm}} sont quotidiens avec des frais d'appartenance et des frais d'homologue.


## Paiement des frais de l'intégralité du réseau
Un membre du réseau peut couvrir les frais de tous les membres du réseau.  Pour ce type de paiement unique, le débiteur et les autres membres réseau doivent suivre la procédure ci-après.

### Prérequis
Chaque membre réseau doit disposer d'un compte {{site.data.keyword.cloud_notm}}. Dans le cas contraire, vous devez en [enregistrer](https://console.bluemix.net/registration/) un.

### Procédures
1. Le débiteur crée des espaces Cloud Foundry distincts dans {{site.data.keyword.cloud_notm}} pour chaque utilisateur qui sera invité à rejoindre le réseau :
   1. Connectez-vous à {{site.data.keyword.cloud_notm}}.
   2. Dans la barre de menus, cliquez sur **Gérer** > **Compte** > **Organisations Cloud Foundry**.
   3. Cliquez sur **Afficher Détails** en regard de l'organisation Cloud Foundry pour laquelle vous allez créer le réseau.  Si vous n'avez pas d'organisation pour le réseau, cliquez sur le bouton **Ajouter une nouvelle organisation Cloud Foundry** pour en créer une.
   4. Cliquez sur le bouton **Ajouter un espace Cloud Foundry** afin de créer un espace pour un membre réseau.  Sélectionnez la région de l'espace, donnez-lui un nom, puis cliquez sur **Ajouter**.  Notez que seul le créateur de l'espace peut accéder à l'espace créé.
   5. Répétez l'étape 4 pour créer des espaces distincts pour tous les utilisateurs qui vont être invités à rejoindre le réseau.
2. Le débiteur invite d'autres utilisateurs à rejoindre le compte {{site.data.keyword.cloud_notm}} du débiteur et il leur accorde l'accès à un espace spécifique.  Chaque utilisateur n'a accès qu'à un seul espace.
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
   3. Répétez les étapes pour inviter des utilisateurs et attribuer des droits d'accès à chaque utilisateur.
3. Dans le tableau de bord de la plateforme {{site.data.keyword.blockchainfull_notm}}, le débiteur invite les utilisateurs à rejoindre le réseau de blockchain. Pour plus d'informations sur les membres réseau, voir [Membres](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members).
4. Chaque utilisateur reçoit ensuite une courrier électronique de notification contenant une invitation à rejoindre le réseau.  Vous pouvez procéder comme suit pour rejoindre le réseau.
   1. Cliquez sur le bouton "Poursuivre" dans le courrier électronique de notification. Vous êtes alors redirigé vers la page de service blockchain dans {{site.data.keyword.cloud_notm}}.
   2. Vérifiez que vous utilisez une organisation et un espace {{site.data.keyword.cloud_notm}} corrects.
      1. Connectez-vous à {{site.data.keyword.cloud_notm}} et cliquez sur l'avatar de profil dans l'angle supérieur droit.
      2. Choisissez le compte du débiteur dans la liste déroulante **Compte**.  Notez que le compte et l'organisation dans laquelle vous mettez à disposition l'instance de service blockchain sont ceux qui sont facturés.  
   4. Sur la page de service blockchain, créez votre instance de service blockchain :
      1. Dans la zone **Nom du service**, entrez un nom descriptif à des fins de reconnaissance future.
      2. Vérifiez que l'organisation et l'espace sont ceux que le débiteur vous a invité à rejoindre.
      3. Sélectionnez le plan d'appartenance avec lequel vous allez créer votre instance de réseau.
      4. Cliquez sur **Créer**.
   5. Dès que vous avez créé l'instance de service blockchain, suivez les instructions de l'assistant pour rejoindre le réseau.  Pour plus d'informations, voir [Rejoindre un réseau](https://console.bluemix.net/docs/services/blockchain/get_start.html#joining-a-network).

### Limitations connues
- Etant donné que tous les membres figurent dans le compte {{site.data.keyword.cloud_notm}} des débiteurs, le débiteur a accès à toutes les instances de blockchain des membres et il peut en emprunter l'identité.  Par conséquent, ce mode de paiement est le mieux adapté pour les environnements POC, ou dans le cas où le débiteur traite toutes les opérations de gestion de la plateforme {{site.data.keyword.blockchainfull_notm}} et lorsque les membres sont fournis avec une seule application.  
- Une fois que tous les membres ont été ajoutés au compte {{site.data.keyword.cloud_notm}} du débiteur et que l'accès leur est accordé pour mettre à disposition les instances de blockchain et rejoindre le réseau, le débiteur accorde également aux membres l'accès à la création d'autres services, ce qui peut entraîner des frais supplémentaires.  
- Tous les membres de l'organisation Cloud Foundry du débiteur peuvent voir tous les espaces au sein de l'organisation.  Toutefois, ils ne peuvent pas éditer ou modifier les organisations car ils ne disposent pas des accès.
