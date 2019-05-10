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
{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:pre: .pre}

# Traitement des incidents
{: #ibp-v2-troubleshooting}

Des problèmes généraux peuvent se produire lorsque vous utilisez la console pour gérer des noeuds, des canaux ou des contrats intelligents. Dans de nombreux cas, ces problèmes peuvent être résolus en quelques opérations simples.

- [Lorsque je survole mon noeud, l'état est `Statut non disponible`. Qu'est-ce que cela signifie ?](#ibp-v2-troubleshooting-status-unavailable)
- [Lorsque je survole mon noeud, l'état est `Statut non détectable`. Qu'est-ce que cela signifie ?](#ibp-v2-troubleshooting-status-undetectable)
- [Pourquoi mes opérations de noeud échouent après la création d'un homologue ou d'un service de tri ?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-build-network-troubleshoot-entry1)
- [Pourquoi mon homologue ne parvient pas à démarrer ? ](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-build-network-troubleshoot-entry2)
- [Pourquoi ne puis-je pas installer, instancier ou mettre à niveau mes contrats intelligents ?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-smart-contracts-troubleshoot-entry1)
- [Comment puis-je consulter les journaux de mon conteneur de contrat intelligent ?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-console-smart-contracts-troubleshoot-entry2)
- [Mon canal, mes contrats intelligents et mes identités ont disparu de la console. Comment puis-je les récupérer ?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-browser-storage)
- [Pourquoi est-ce que je reçois le message d'erreur `Une erreur s'est produite lors de la mise à jour du canal` lorsque j'essaie d'ajouter une organisation à mon canal ?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-update-channel)
- [Mon cluster Kubernetes est arrivé à expiration. Qu'est-ce que cela signifie ?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-cluster-expired)
- [Pourquoi les transactions que je soumet depuis VSCode échouent ?](/docs/services/blockchain/howto/ibp-v2-troubleshooting.html#ibp-v2-troubleshooting-anchor-peer)

## Lorsque je survole mon noeud, l'état est `Statut non disponible`. Qu'est-ce que cela signifie ?
{: #ibp-v2-troubleshooting-status-unavailable}

Le statut de noeud dans la vignette du noeud de l'autorité de certification, de l'homologue ou du service de tri est grisé, ce qui signifie que le statut du noeud est non disponible. Dans l'idéal, lorsque vous survolez un noeud, l'état doit être `En fonctionnement`.
{: tsSymptoms}

Ce problème peut se produire si le noeud a récemment été créé et que le processus de déploiement n'est pas terminé. Si le noeud est une autorité de certification, il est probable que le noeud n'est pas en cours d'exécution. Si le noeud est un homologue ou un service de tri, cette condition se produit lorsque le programme de diagnostic d'intégrité d'intégrité qui s'exécute sur le noeud ou le service de tri ne parvient pas à contacter le noeud. La demande de statut peut échouer avec une erreur de délai d'attente car le noeud n'a pas répondu dans un délai spécifique, le noeud est arrêté ou la connectivité du réseau est hors service.
{: tsCauses}

S'il s'agit d'un nouveau noeud, attendez quelques minutes que le déploiement se termine. Si le noeud n'est pas nouveau,
[examinez les journaux noeud associés](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs) pour rechercher permettant d'en déterminer la cause.
{: tsResolve}

## Lorsque je survole mon noeud, le statut est `Statut non détectable`. Qu'est-ce que cela signifie ?
{: #ibp-v2-troubleshooting-status-undetectable}

Le statut de noeud dans la vignette de l'homologue ou service de tri est jaune, ce qui signifie que le statut du noeud ne peut pas être détecté. Dans l'idéal, lorsque vous survolez un noeud, l'état doit être `En fonctionnement`.
{: tsSymptoms}

Cette condition se produit uniquement sur les noeuds homologue et service de tri qui ont été *importés* sur la console et si le programme de diagnostic d'intégrité ne peut pas s'exécuter sur le noeud. Ce statut existe car aucun élément `operations_url` n'a été spécifié lorsque le noeud a été importé. Une URL d'opérations est requise pour que le programme de diagnostic d'intégrité puisse s'exécuter. Le noeud lui-même est probablement `en cours d'exécution`, mais comme aucune URL d'opérations n'a été spécifiée, son statut ne peut pas être déterminé.
{: tsCauses}

Vous pouvez résoudre ce problème en suivant les étapes ci-après :
 1. Cliquez sur la vignette de noeud afin de l'ouvrir.
 2. Cliquez sur l'icône **Paramètres**.
 3. Cliquez sur **Associer une identité**, consultez et notez l'identité qui est associée à ce noeud. Cliquez sur **Annuler** pour refermer cet écran.
 4. Cliquez sur **Exporter** pour générer un fichier `JSON` pour le noeud.
 5. Editez le fichier `JSON` généré et entre l'URL `operations_url` pour le noeud. La valeur de `operations_url` dépend de la manière dont l'YRL a été configurée ainsi que de divers paramètre réseau. Cette valeur doit être fournie par l'administrateur de réseau qui a déployé le noeud.
 6. Cliquez sur **Supprimer**. Cette étape supprime le noeud importé à partir de la console, mais ne supprime pas le noeud réel.
 7. Depuis l'onglet **Noeuds**, cliquez sur **Ajouter un homologue** ou **Ajouter un service de tri** suivi de **Importer un homologue existant** ou **Importer un service de tri existant**.
 8. Cliquez sur **Télécharger JSON** et recherchez le fichier JSON que vous venez d'éditer. Cliquez sur **Suivant**.
 9. Associez l'identité que vous avez notée à l'étape 3.
 10. Cliquez sur **Ajouter un homologue** ou **Ajouter un service de tri**.

Le programme de diagnostic d'intégrité peut maintenant s'exécuter sur le noeud et indiquer le statut du noeud.
{: tsResolve}

## Pourquoi mes opérations de noeud échouent après la création d'un homologue ou d'un service de tri ?
{: #ibp-console-build-network-troubleshoot-entry1}

Il est possible que vous rencontriez une erreur lors de la gestion d'un noeud existant. Lorsque cela arrive, il est souvent utile de consulter les journaux de ce noeud.  

Par exemple, lorsque vous essayez d'exploiter le noeud, l'action peut échouer.
{: tsSymptoms}

Après la création d'un homologue ou d'un service de tri, selon la configuration de stockage de cluster, quelques minutes peuvent être nécessaires pour que le noeud soit opérationnel.
{: tsCauses}

Consultez votre tableau de bord Kubernetes et vérifiez que l'homologue ou le noeud est à l'état `En cours d'exécution`. Renouvelez ensuite votre opération. Si vous rencontrez encore des problèmes une fois le noeud opérationnel, [recherchez d'éventuelles erreurs dans les journaux de ce noeud](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs).  
{: tsResolve}

## Pourquoi mon homologue ne parvient pas à démarrer ?
{: #ibp-console-build-network-troubleshoot-entry2}

Plusieurs cas de figure sont possibles pour cette erreur.

Le journal de l'homologue comporte `2019-02-06 19:43:24.159 UTC [main] InitCmd -> ERRO 001 Cannot run peer because cannot init crypto, folder “/certs/msp” does not exist`
{: tsSymptoms}

- Cette erreur peut survenir dans les conditions suivantes :
  - Lors de la création de la définition MSP de l'organisation de l'homologue ou du service de tri, vous avez indiqué un ID et un secret d'inscription qui correspondent à un type d'identité `homologue` et non `client`. Or le type doit être `client`.
  - Lors de la création de la définition MSP de l'organisation de l'homologue ou du service de tri, vous avez indiqué un ID et un secret d'inscription qui ne correspondent pas à l'ID et au secret d'inscription de l'identité admin de l'organisation correspondante.
  - Lors de la création de l'homologue ou du service de tri, vous avez indiqué l'ID et le secret d'inscription d'une identité qui n'est pas de type 'homologue'.

- Ouvrez le noeud de l'autorité de certification de votre homologue ou service de tri et consultez les identités enregistrées figurant dans le tableau **Utilisateurs enregistrés**.
- Supprimez l'homologue ou le service de tri et recréez-le, en veillant à indiquer l'ID et le secret d'inscription corrects.
- Notez qu'avant de créer l'homologue ou le service de tri, vous devez créer un ID admin organisation de type 'client'. Veillez à indiquer le même ID que l'ID d'inscription lors de la création de la définition MSP de l'organisation. Consultez les instructions relatives à l'[enregistrement des identités d'homologue](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-org1) et celles relatives à l'[enregistrement des identités de service de tri](/docs/services/blockchain/howto/ibp-console-build-network.html#ibp-console-build-network-use-CA-orderer).
{: tsResolve}

## Pourquoi ne puis-je pas installer, instancier ou mettre à niveau mes contrats intelligents ?
{: #ibp-console-smart-contracts-troubleshoot-entry1}

Il est possible que vous rencontriez une erreur lors de l'installation, de l'instanciation ou de la mise à niveau d'un contrat intelligent.  Par exemple, lorsque vous essayez d'installer un contrat intelligent sur un homologue, l'opération peut échouer avec le message d'erreur `Une erreur s'est produite lors de l'installation du contrat intelligent sur l'homologue.`
{: tsSymptoms}

Vous pouvez recevoir cette erreur si cette version de contrat intelligent existe déjà sur l'homologue, ou si votre homologue n'a plus de ressources.
{: tsCauses}

- Ouvrez votre tableau de bord Kubernetes et vérifiez que l'homologue est à l'état `En cours d'exécution`.  
- Ouvrez le noeud homologue et vérifiez que la version du contrat intelligent n'existe pas déjà sur l'homologue, puis relancez l'opération avec la version appropriée.
- Si vous rencontrez encore des problèmes une fois le noeud opérationnel, [recherchez d'éventuelles erreurs dans les journaux de ce noeud](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-node-logs).  
{: tsResolve}

## Comment puis-je consulter les journaux de mon conteneur de contrat intelligent ?
{: #ibp-console-smart-contracts-troubleshoot-entry2}

Vous devrez peut-être consulter les journaux de votre conteneur de contrat intelligent, ou de code blockchain, pour déboguer un problème de contrat intelligent.
{: tsSymptoms}

Suivant les instructions de la rubrique relative à la [consultation des journaux de votre conteneur](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-container-logs).
{: tsResolve}

## Mon canal, mes contrats intelligents et mes identités ont disparu de la console. Comment puis-je les récupérer ?
{: #ibp-v2-troubleshooting-browser-storage}

Les identités de votre portefeuille de console se composent d'un paire de clés publiques et privées qui vous permet de gérer les composants de votre blockchain. Cependant, ces clés sont stockées uniquement dans le stockage local de votre navigateur. Vous êtes responsable de la sécurisation et de la gestion de ces clés. Nous vous recommandons de les exporter sur votre système de fichiers après leur création. Chaque fois que vous créez un nouveau noeud, vous associez une identité de votre portefeuille de console à ce noeud. Cette identité admin est celle qui vous permet de gérer le noeud. Lorsque vous changez de navigateur ou lorsque vous passez à un navigateur sur un autre ordinateur, ces identités ne sont plus dans votre portefeuille. Par conséquent, vous ne pouvez pas gérer les composants.
{: tsSymptoms}

A compter de la version 2.0 d'{{site.data.keyword.blockchainfull_notm}} Platform, vous êtes désormais responsable de la sécurisation et de la gestion de vos certificats. Par conséquent, ces derniers sont uniquement conservés dans le stockage local du navigateur pour vous permettre de gérer le composant. Si vous utilisez un navigateur privé et que vous passez à une autre fenêtre de navigateur ou dans une fenêtre de navigateur non privée, les identités que vous avez créées seront supprimé de votre portefeuille de console dans la nouvelle session de navigateur. Par conséquent, il est nécessaire d'exporter les identités depuis le portefeuille de console dans votre session de navigateur privée pour votre système de fichiers. Vous pouvez ensuite les importer dans votre session de navigateur non privée si nécessaire. Sinon, il n'est pas possible de les récupérer.
{: tsCauses}

- Chaque fois que vous créez une nouvelle définition MSP d'organisation, vous générez des clés pour une identité qui est autorisée à administrer l'organisation. Par conséquent, pendant ce processus vous devez cliquer sur les boutons **Générer** et **Exporter** pour stocker l'identité générée dans votre portefeuille de console, puis la sauvegarder sur votre système de fichiers dans un fichier JSON.
- Pour résoudre ce problème dans votre navigateur, vous devez importer ces identités et les associer au noeud correspondant :
  - Dans le navigateur concerné par ce problème, cliquez sur l'onglet **Portefeuille** puis sur **Ajouter une identité** afin d'importer le fichier JSON dans votre portefeuille.
  - Cliquez sur **Télécharger JSON** et recherchez le fichier JSON que vous avez exporté à l'aide du bouton **Ajouter des fichiers**.
  - Cliquez sur **Soumettre**.
  - Ouvrez ensuite le noeud de l'homologue ou du service de tri auquel cette identité a été initialement associée, puis cliquez sur l'icône **Paramètres**.
  - Cliquez sur le bouton **Associer une identité**.
  - Sélectionnez dans la liste déroulante l'identité que vous venez d'importer dans votre portefeuille de console.
  - Cliquez sur **Associer**.
- Recommencez ce processus pour chaque identité figurant dans le portefeuille du navigateur d'origine.
{: tsResolve}

## Pourquoi est-ce que je reçois le message d'erreur `Une erreur s'est produite lors de la mise à jour du canal` lorsque j'essaie d'ajouter une organisation à mon canal ?
{: #ibp-v2-troubleshooting-update-channel}

Lorsque vous essayez d'ajouter une autre organisation à un canal, la mise à jour échoue avec le message `Une erreur s'est produite lors de la mise à jour du canal`.
{: tsSymptoms}

Cette erreur se produit lorsque l'**ID MSP de programme de mise à jour de canal** sélectionné dans le panneau **Mettre à jour un canal** n'est pas un admin du canal.
{: tsCauses}

Dans le panneau **Mettre à jour un canal**, accédez à la section **ID MSP de programme de mise à jour de canal** et sélectionnez l'ID MSP qui a été spécifié lors de la création du canal ou indiquez l'ID MSP qui est l'admin du canal.
{: tsResolve}

## Mon cluster Kubernetes est arrivé à expiration. Qu'est-ce que cela signifie ?
{: #ibp-v2-troubleshooting-cluster-expired}

J'ai reçu un e-mail indiquant que mon cluster Kubernetes Service {{site.data.keyword.IBM_notm}} est sur le point d'expirer ou que son statuts est `Expiré`. Ou bien vous ne pouvez pas accéder à la console au bout de 30 jours.
{: tsSymptoms}

Les clusters Kubernetes gratuits ne sont valides que pendant 30 jours.
{: tsCauses}

Il n'est pas possible de migrer depuis un cluster gratuit vers un cluster payant. Au bout de 30 jours vous ne pouvez pas accéder à la console et tous vos noeuds et certificats sont supprimés. Pour plus d'informations sur cette opération et les actions que vous pouvez effectuer, consultez la rubrique relative à l'[expiration du cluster Kubernetes](/docs/services/blockchain/howto/ibp-console-manage.html#ibp-console-manage-console-cluster-expiration).
{: tsResolve}

## Pourquoi les transactions que je soumet depuis VSCode échouent ?
{: #ibp-v2-troubleshooting-anchor-peer}

Les transactions soumises depuis VSCode échouent avec une erreur semblable à ce qui suit :
```
Error submitting transaction: No endorsement plan available for {"chaincodes":[{"name":"hello-world"}]}
```
{: tsSymptoms}

Cette erreur se produit si vous utilisez la fonction Reconnaissance de service Fabric et que vous n'avez configuré aucun homologue d'ancrage sur votre canal.
{: tsCauses}

Suivez l'étape 3 de la [rubrique relative aux données privées](/docs/services/blockchain/howto/ibp-console-smart-contracts.html#ibp-console-smart-contracts-private-data) dans le tutoriel Déployer un contrat intelligent pour configurer vos homologues d'ancrage.
