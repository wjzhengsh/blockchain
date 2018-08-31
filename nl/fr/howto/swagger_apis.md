---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Interaction avec le réseau à l'aide d'API Swagger

{{site.data.keyword.blockchainfull_notm}} Platform expose dans Swagger un certain nombre d'API REST que vous pouvez utiliser pour gérer les noeuds, les homologues et les membres de votre réseau. Vos applications peuvent recourir à ces API pour gérer d'importantes ressources réseau sans utiliser le moniteur réseau.

{:shortdesc}

Avant de commencer, vous devez créer une instance de service de plateforme [{{site.data.keyword.blockchain}} ![Icône de lien externe](../images/external_link.svg "Icône de lien externe")](https://console.bluemix.net/catalog/services/blockchain) on {{site.data.keyword.Bluemix_notm}} et créer ou rejoindre le réseau de <!--or Enterprise Plan -->blockchain d'un plan Starter.


## Extraction des données d'identification réseau

{: #retrieving-network-credentials}

Accédez au Moniteur réseau de votre réseau de blockchain et ouvrez l'écran "API" dans le navigateur de gauche. Vous pouvez voir vos données d'identification réseau pour les API REST. Vous pourrez autoriser ultérieurement les API à l'aide des valeurs de "key" et "secret" affichées ici, puis exécuter les API avec "network_id" comme paramètre. Cliquez sur **Afficher le secret** pour afficher la valeur de la zone de valeur confidentielle. Copiez les valeurs des zones key, secret et network_id, que vous pouvez utiliser plus tard dans l'interface utilisateur Swagger.

La **Figure 1** illustre l'écran "API" :
![Ecran API](../images/API_screen_starter.png "Ecran API")
*Figure 1. API*

Si vous utilisez le plan Starter, il est possible de basculer entre les organisations dans le Moniteur réseau. Avec le plan Starter, deux organisations sont configurées par défaut. le basculement entre organisations peut être utile pour tester les API REST du point de vue de chaque organisation. Pour obtenir les données d'identification d'une autre organisation de votre réseau, cliquez sur votre nom d'utilisateur dans l'angle supérieur droit de la console Moniteur réseau. Dans le menu qui s'ouvre, cliquez sur la flèche déroulante en regard de la vue Organisation pour afficher toutes les organisations. Sélectionnez l'organisation vers laquelle vous voulez basculer et afficher les données d'identification réseau associées.

La **Figure 2** illustre le basculement entre organisations :

![Basculement entre organisations](../images/switch_orgs_starter.gif "Basculement entre organisations")  
*Figure 2. Basculement entre organisations*


## Autorisation d'API Swagger

Cliquez sur le lien **Interface utilisateur swagger** sur l'écran "API" pour ouvrir l'interface utilisateur swagger.  
<!-- remove this line because the link is different depending on if you are starter or enterprise plan
You can also open the Swagger UI with the URL in the connection profiles. For example, `http://blockchain-swagger-dev.stage1.mybluemix.net`.
-->

Dans l'interface utilisateur swagger, cliquez sur le bouton **Autoriser** afin d'ouvrir la fenêtre d'autorisation. Entrez la valeur de "key" et "secret" dans vos données d'identification réseau en tant que nom d'utilisateur et mot de passe, puis cliquez sur **Autoriser** et **Terminé**. Vous êtes maintenant prêt à exécuter les API. Notez que si vous actualisez votre navigateur, vous devez autoriser de nouveau avec vos données d'identification.

Avec l'authentification de base, les données d'identification que vous indiquez dans la fenêtre Autoriser sont stockées dès que vous cliquez sur les boutons **Autoriser** et **Terminé** et elles sont transmises sur chaque appel d'API REST.

La **Figure 3** illustre la procédure d'autorisation d'API Swagger :

![Autorisation des API](../images/swaggerUIAuthorize.gif "Autorisation des API")  
*Figure 3. Autorisation des API*


## Test des API

Cliquez sur l'API REST que vous voulez exécuter puis sur le bouton **Essayez**.

La **Figure 4** illustre le bouton "Essayez" dans l'interface utilisateur Swagger :

![Bouton Essayez dans l'interface utilisateur Swagger](../images/swaggerUITryItOut.png "Bouton Essayez dans l'interface utilisateur Swagger")  
*Figure 4. Bouton "Essayez" dans l'interface utilisateur Swagger*

Après avoir cliqué sur le bouton **Essayez**, vous pouvez entrer les paramètres requis pour utiliser l'API. Vous pouvez trouver la valeur `networkID` dans vos données d'identification réseau et les autres paramètres dans votre Moniteur réseau. Après voir entré les paramètres, cliquez sur **Exécuter** pour lancer l'appel d'API REST sur votre réseau.

La **Figure 5** illustre les paramètres dans l'interface utilisateur Swagger :

![Paramètres dans l'interface utilisateur Swagger](../images/swaggerUIParams.png "Paramètres dans l'interface utilisateur Swagger")  
*Figure 5. Saisie des paramètres*  

Après avoir cliqué sur **Exécuter**, vous pouvez observer la réponse de l'appel d'API sur votre réseau. Vous pouvez aussi voir une commande CURL permettant d'appeler directement l'API depuis votre ligne de commande.

La **Figure 6** illustre le corps de la réponse de l'API, l'URL, et la commande CURL :

![Réponse d'API dans l'interface utilisateur Swagger](../images/swaggerUICurlResponse.png "Réponse d'API dans l'interface utilisateur Swagger")  
*Figure 6. Réponse d'API*    

## Conseils pour l'identification et la résolution des problèmes

### 401 non autorisé  
  Vérifiez que vous avez autorisé les API REST en indiquant vos données d'identification réseau. Pour plus de détails, voir [Autorisation d'API Swagger](#authorizing-swagger-apis).

### 400 Erreur : Demande incorrecte
  Certaines API peuvent prendre un argument dans le corps de la demande qui fait office de filtre pour afficher les résultats uniquement pour un homologue spécifique. Un exemple de fragment est fourni dans le corps, lequel s'il est utilisé, doit être édité pour indiquer l'homologue ou la liste des homologues sur lesquels vous voulez filtrer. Pour éviter cette erreur, éditez le fragment afin d'indiquer un homologue de votre réseau ou de retirer le fragment dans son intégralité.
