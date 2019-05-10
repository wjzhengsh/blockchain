---

copyright:
  years: 2018, 2019
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

# A propos d'{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private
{: #ibp-icp-about}

{{site.data.keyword.blockchainfull}} Platform publie {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private, qui est une plateforme applicative pour le développement et la gestion d'applications conteneurisées. L'offre {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private repose sur Kubernetes, ce qui permet aux utilisateurs de déployer des autorités de certification, des services de tri, ainsi que des homologues sur x86, LinuxONE et IBM Z. Vous pouvez déployer {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private à l'aide de chartes Helm Kubernetes.
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private est mis à niveau vers Hyperledger Fabric version 1.4.0 le 23 avril 2019. Toutefois, les données gossip et privées ne seront pas encore prises en charge.
{:note}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private offre les composants nécessaires à l'exécution d'un réseau de blockchain sur votre propre infrastructure via {{site.data.keyword.cloud_notm}} Private. Ces composants incluent Hyperledger Fabric, une autorité de certification, un service de tri, ainsi qu'un homologue, que vous déployez, gérez et configurez à l'aide de chartes Helm Kubernetes. **Cette offre est destinée aux clients dotés d'une bonne connaissance d'Hyperledger Fabric.**

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private permet le déploiement de réseaux de blockchain sur un cloud privé pour répondre à des exigences relatives à l'hébergement de données, aux réglementations du marché et aux préférences d'infrastructure. Il simplifie le déploiement des principaux éléments d'un réseau de blockchain au sein de votre propre infrastructure via {{site.data.keyword.cloud_notm}} Private, une plateforme applicative basée sur Kubernetes pour le développement et la gestion d'applications sur site et conteneurisées.

 * Il permet aux clients de gérer des réseaux {{site.data.keyword.blockchainfull_notm}} Platform au sein de leur propre infrastructure. Une édition Community gratuite permet aux clients de lancer des exécutions dans leurs propres environnements isolés et sécurisés, mais aucun support n'est assuré.
 * Il permet aux clients de configurer Fabric sur Kubernetes à l'aide de chartes Helm et d'une documentation détaillée pour les opérations.
 * Il propose aux clients un support technique avancé, sauf si vous utilisez l'édition Community gratuite.

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private est un produit regroupé pour les clients {{site.data.keyword.cloud_notm}} Private qui déploient des composants de blockchain dans leur environnement local. Une fois que vous avez importé la charte Helm, vous pouvez la trouver sous forme d'unbe vignette {{site.data.keyword.blockchainfull_notm}} Platform dans le catalogue {{site.data.keyword.cloud_notm}} Private. Pour plus d'informations sur {{site.data.keyword.cloud_notm}} Private, voir la documentation relative à [{{site.data.keyword.cloud_notm}} Private version 3.1.2 ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.2/kc_welcome_containers.html "{{site.data.keyword.cloud_notm}} Private 3.1.2").

## Offres d'{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private vous permet de déployer l'ensemble des composants de base d'une blockchain Hyperledger Fabric : une autorité de certification, un service de tri et des homologues. Vous avez ainsi la possibilité de déployer différents composants en fonction de vos besoins métier. Vous pouvez utiliser {{site.data.keyword.blockchainfull_notm}} for {{site.data.keyword.cloud_notm}} Private pour démarrer un réseau de blockchain en déployant et en configurant un service de tri qui lie les organisations entre elles dans un consortium de blockchain. Vous pouvez également déployer des homologues et rejoindre d'autres réseaux qui utilisent des composants basés sur Fabric, y compris des réseaux {{site.data.keyword.blockchainfull_notm}} Platform déployés sur des clouds à l'aide d'{{site.data.keyword.cloud_notm}} Private, ou de réseaux Starter Plan et Enterprise Plan hébergés dans IBM Cloud. Pour plus d'informations sur les blocs de construction des réseaux Hyperledger Fabric, voir la [présentation du composant de blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview).

## {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private vous convient-il ?

L'exécution de composants {{site.data.keyword.blockchainfull_notm}} Platform à l'extérieur de {{site.data.keyword.cloud_notm}} vous offre davantage de souplesse pour croître ou rejoindre un réseau de blockchain. Les créateurs de réseaux peuvent ainsi autoriser l'accès de nouveaux membres en utilisant la plateforme de leur choix. Les organisations qui souhaitent rejoindre des réseaux de blockchain peuvent ainsi regrouper leurs homologues avec leurs applications existantes ou s'intégrer à leurs systèmes d'enregistrement.

Le processus de déploiement de {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private est complexe et il suppose un haut degré d'expertise dans Fabric. Si vous débutez avec Fabric, {{site.data.keyword.cloud_notm}} Private ou {{site.data.keyword.blockchainfull_notm}} Platform, et que vous avez pour objectif de définir un environnement de développement ou une validation de concept, utilisez plutôt un [Plan Starter](/docs/services/blockchain/starter_plan.html#starter-plan-about). Notez également que certaines configurations de déploiement potentielles ne sont pas prises en charge dans {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private.
{:improtant}

Les utilisateurs de cette offre gèrent leur propre sécurité et infrastructure. {{site.data.keyword.cloud_notm}} ne fournit pas ces services. Passez en revue les [Considerations et limitations](/docs/services/blockchain/ibp-for-icp-about.html#ibp-icp-about-considerations) de la section suivante avant de poursuivre.

## Considérations et limitations
{: #ibp-icp-about-considerations}

Avant de commencer, assurez-vous de comprendre les **considérations** et **limitations** suivantes :

- Vous êtes responsable de la surveillance de l'intégrité, de la sécurité, de la journalisation et de la gestion de l'utilisation des ressources de vos composants.
- Les composants qui s'exécutent dans d'autres environnements de cloud ne sont pas visibles dans le Moniteur de réseau des réseaux s'exécutant sur {{site.data.keyword.cloud_notm}}.
- Les chartes Helm déploient une seule instance d'un service de tri, d'un homologue ou d'une autorité de certification.
- Vous pouvez déployer plusieurs composants dans un espace de nom unique dans {{site.data.keyword.cloud_notm}} Private dès lors qu'ils ont des noms d'édition différents.
- Les composants ne peuvent pas être adressés à l'aide de l'interface utilisateur swagger dans l'interface utilisateur du Moniteur réseau.
- TLS mutuel n'est pas pris en charge.
- La technologie sous-jacente, Hyperledger Fabric, utilise des conteneurs pour exécuter du code blockchain et Docker pour démarrer ce conteneur. Le seul moyen pour un homologue de démarrer un conteneur de code blockchain est d'utiliser Docker-in-Docker qui requiert un accès privilégié.

**Considérations relatives à l'autorité de certification**
- Cette charte Helm déploie une instance unique de l'autorité de certification. Comme il est préférable de disposer d'une autorité de certification pour chaque organisation, il peut être nécessaire de déployer plusieurs autorités de certification. Par exemple, si vous prévoyez de déployer un service de tri et trois homologues, vous aurez besoin d'au moins deux autorités de certification (une pour l'organisation du service de tri et une autre pour l'organisation de l'homologue).
- Même si vous pouvez choisir d'exécuter une autre base de données MySQL, cette option n'est pas présente dans la charte Helm. Celle-ci va, cependant, déployer une base de données SQLite dans l'autorité de certification afin de gérer les exigences de base de données de l'autorité de certification, ce qui inclut le suivi du nombre d'inscriptions par utilisateur et les certificats révoqués.

**Considérations relatives au service de tri**
- Le service de tri est compatible avec tout composant en version 1.1 d'Hyperledger Fabric.
- Cette charte Helm déploie une instance unique du service de tri SOLO (un service de tri). Notez qu'il n'est pas possible de déployer plusieurs services de tri SOLO sur un canal pour rendre le service de commande hautement disponible. C'est la raison pour laquelle les services de tri SOLO sont pris en compte plutôt pour les environnements de développement que pour les environnements de production. Toutefois, vous pouvez déployer plusieurs instances du service de tri SOLO sur différents réseaux (c'est-à-dire, avec un autre consortium).

**Considérations relatives aux homologues**

- Vous pouvez connecter vos homologues uniquement aux réseaux de blockchain au niveau Fabric version 1.1 ou suivante. Pour connaître la votre version d'Hyperledger Fabric, ouvriez la [fenêtre Préférences réseau](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-network-preferences) dans votre Moniteur réseau. Suivez les [instructions](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-peer-connection-information) relatives à l'extraction des informations de connexion homologue depuis les réseaux Starter ou Enterprise.
- Le type de base de données de l'homologue doit correspondre au type de base de données de votre réseau blockchain, LevelDB ou CouchDB.
- L'interface CouchDB Fauxton n'est pas disponible sur l'homologue.
- [Gossip](/docs/services/blockchain/glossary.html#glossary-gossip) pour les homologues n'est pas pour l'instant pris en charge. Cela suppose que les fonctions Fabric qui dépendent de gossip, comme les [données privées ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data-arch.html "données privées") et la [reconnaissance de service![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/discovery-overview.html "reconnaissance de service"), ne sont également pas prises en charge.

## Configuration système prérequise
{: #ibp-icp-about-prerequisites}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private prend en charge les systèmes d'exploitation suivants :
- Red Hat Enterprise Linux (RHEL) 7.3, 7.4, 7.5
- Ubuntu 18.04 LTS et 16.04 LTS
- SUSE Linux Enterprise Server (SLES) 12 SP3

La charte Helm {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private est validée pour une exécution sur des clusters {{site.data.keyword.cloud_notm}} Private version 3.1.2 sur Ubuntu Linux en utilisant les noeuds worker et le stockage de secours suivants :

- **LinuxONE et IBM Z** : z/VM et KVM, qui utilisent NFS.
- **x86** : Linux 64 bits qui utilise GlusterFS.

## Licence et tarification
{: #ibp-icp-about-license-pricing}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private comporte deux éditions, une offre payante qui peut être téléchargée depuis Passport Advantage et une édition Community gratuite qui est disponible dans [GitHub ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://github.com/IBM/charts/tree/master/stable/ibm-blockchain-platform-dev "IBM/chartes").

### Licence
{: #ibp-icp-about-license}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private offre les composants nécessaires à l'exécution d'un réseau de blockchain sur votre propre infrastructure et est il déployé en tant qu'application {{site.data.keyword.cloud_notm}} Private. Vous pouvez accéder à PPA et [télécharger la charte Helm](/docs/services/blockchain/howto/helm_install_icp.html#helm-install). Un support technique de {{site.data.keyword.blockchainfull_notm}} est inclus dans l'achat.

L'édition Community de {{site.data.keyword.blockchainfull_notm}} Platform for IBM Cloud Private est une offre gratuite qui convient pour une évaluation et une expérimentation et elle est déployée en tant qu'application {{site.data.keyword.cloud_notm}} Private. N'utilisez pas l'édition Community pour la production. {{site.data.keyword.blockchainfull_notm}} Platform n'assure pas de support pour l'édition Community. Vous pouvez accéder au [package GitHub![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://github.com/IBM/charts/blob/master/repo/stable/ibm-blockchain-platform-dev-1.0.2.tgz "IBM/chartes") et télécharger la charte Helm.

### Tarification
{: #ibp-icp-about-pricing}

La tarification de {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private dépend du nombre de coeurs de processeur virtuels (VPC) utilisés. Un VPC est un coeur virtuel qui est affecté à un serveur virtuel ou à un coeur de processeur physique sur un serveur non partitionné. Vous devez obtenir une autorisation de licence pour chaque VPC rendu disponible pour {{site.data.keyword.blockchainfull_notm}} Platform. <!-- A VPC is a unit of measurement by which a program can be licensed.-->

Pour plus d'informations sur la manière de déterminer votre utilisation des VPC, consultez cet article sur les["coeurs de processeur virtuels" ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/Inventory/overview/c_virtual_processor_core_licenses.html "Virtual coeurs de processeur virtuels (VPC)") dans l'{{site.data.keyword.IBM_notm}} Knowledge Center. Vous pouvez utiliser [{{site.data.keyword.IBM_notm}} License Metric Tool](https://www.ibm.com/support/knowledgecenter/en/SS8JFY_9.2.0/com.ibm.lmt.doc/welcome/LMT_welcome.html) pour configurer et créer un rapport que vous pouvez utiliser pour déterminer le nombre de VPC que vous devez définir sous licence.


## Installation d'{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private
{: #ibp-icp-about-install}

{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private est fourni en tant que fichier de charte Helm qui peut être installé dans un cluster {{site.data.keyword.cloud_notm}} Private local. Une fois que vous avez installé la charte Helm, vous pouvez trouver {{site.data.keyword.blockchainfull_notm}} Platform en tant qu'application dans le catalogue {{site.data.keyword.cloud_notm}} Private.

- {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private est fourni via Passport Advantage. Vous devez posséder la licence requise pour accéder à [Passport Advantage Online](https://www.ibm.com/software/passportadvantage/pao_customer.html). Dans le cadre de cet achat, le support technique de {{site.data.keyword.blockchainfull_notm}} Platform est inclus.

- L'édition Community de {{site.data.keyword.blockchainfull_notm}} Platform Community est destinée à l'exploration, au développement et aux tests. Cette version gratuite d'{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private est accessible via GitHub. **Remarque :** {{site.data.keyword.blockchainfull_notm}} Platform n'assure pas de support pour l'édition Community.

Pour plus de détails sur l'installation de la charte Helm et des prérequis, voir [Installation de {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/helm_install_icp.html#helm-install).

Si vous êtes un nouvel utilisateur de {{site.data.keyword.cloud_notm}} Private et souhaitez des informations et des conseils sur l'installation et le développement d'{{site.data.keyword.cloud_notm}} Private, voir [Configuration de {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ICP_setup.html#icp-setup).

Une fois que vous avez installé {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private, vous devez déployer chaque composant de votre réseau de manière individuelle. Vous ne pouvez pas déployer plusieurs composants en même temps. Consultez le document [Initiation à {site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/ibp_for_icp_deployment_guide.html#get-started-icp) pour en savoir plus sur les meilleures pratiques en termes de génération d'un réseau de blockchain et comment le rejoindre. Ensuite, passez en revue les étapes de déploiement et d'exploitation des composants individuels dans les sections ci-après.

### Installation d' {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private derrière un pare-feu
{: #ibp-icp-about-firewall}

Vous pouvez déployer les composants de {{site.data.keyword.blockchainfull_notm}} Platform derrière un pare-feu sans avoir à accéder à l'Internet public. Le package de charte Helm téléchargé inclut l'ensemble des images Docker du composant Fabric utilisées par {{site.data.keyword.blockchainfull_notm}} Platform, sans leur extraction de DockerHub durant le déploiement.

Le package de charte Helm de l'édition Community de {{site.data.keyword.blockchainfull_notm}} Platform n'inclut pas les images Docker nécessaires du composant Fabric. Il est configuré pour télécharger ces images depuis DockerHub lors du déploiement et il échoue sans avoir accès à l'Internet public. Cependant, vous pouvez effectuer quelques étapes supplémentaires pour déployer les composants de l'édition Community derrière un pare-feu. Pour plus d'informations, voir [Installation de l'édition Community derrière un pare-feu](/docs/services/blockchain/howto/helm_install_icp.html#helm-install-prereqs-firewall).


## A propos des autorités de certification sur {{site.data.keyword.cloud_notm}} Private
{: #ibp-icp-about-ca}

Les autorités de certification (AC) fournissent l'identité sur le réseau. Une autorité de certification peut être considérée comme un notaire qui agit comme point d'ancrage entre plusieurs parties. Toutes les entités du réseau reçoivent un certificat signé par une autorité de certification racine qui encapsule leur identité numérique. Ce certificat est la racine de confiance pour toutes les opérations de signature et de vérification effectuées sur le réseau. Pour plus d'informations sur les autorités de certification et leur rôle dans un réseau de blockchain, consultez la [présentation du composant blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview).

L'autorité de certification va valider l'identité et émettre des certificats pour les autres composants de votre réseau que vous devez déployer. Par conséquent, vous devez déployer une autorité de certification pour votre organisation avant de déployer d'autres composants.

- Pour savoir comment configurer et déployer une autorité de certification¨après l'installation de charte Helm, voir [Déploiement d'une autorité de certification dans {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_deploy_icp.html#ca-deploy).

- Pour savoir comment utiliser votre autorité de certification pour générer des certificats et effectuer les étapes prérequises pour déployer des composants supplémentaires, voir [Utilisation d'une autorité de certification dans {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/CA_operate.html#ca-operate).

## A propos des services de tri dans {{site.data.keyword.cloud_notm}} Private
{: #ibp-icp-about-orderer}

Les services de tri authentifient les clients, trient les transactions et diffusent les transactions dans un réseau de blockchain. Il s'agit d'une liaison commune des réseaux de blockchain basée sur Hyperledger Fabric. Par conséquent, l'organisation qui génère un réseau doit déployer et démarrer un "service de tri" (le noeud ou l'ensemble de noeuds qui effectuent le tri) pour que d'autres organisations puissent déployer leurs homologues, rejoindre des canaux, et démarrez des transactions sur le réseau. Pour plus d'informations sur les services de tri et leur rôle dans un réseau blockchain, voir la [présentation du composant blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview-orderer).

Si vous générez un réseau de blockchain, vous devez déployer un service de tri. Une fois celui-ci déployé, vous pouvez inviter d'autres organisations à votre consortium, qui peuvent ensuite créer leurs propres canaux.

- Pour en savoir plus sur la configuration et le déploiement d'un service de tri après l'installation de la charte Helm, voir [Déploiement d'un service de tri sur {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/orderer_deploy_icp.html#icp-orderer-deploy).

- Pour savoir comment créer un consortium, voir [Utilisation d'un service de tri sur {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/orderer_operate.html#icp-orderer-operate).

## A propos des homologues sur {{site.data.keyword.cloud_notm}} Private
{: #ibp-icp-about-peer}

Les homologues sont un élément fondamental du réseau car ils hébergent des registres et des contrats intelligents. Les contrats intelligents et les registres sont utilisés pour encapsuler les processus partagés et les informations partagées au sein d'un réseau, respectivement. Pour plus d'informations sur les homologues et leur rôle dans un réseau blockchain, voir la [présentation du composant blockchain](/docs/services/blockchain/blockchain_component_overview.html#blockchain-component-overview).

- Lorsque vous êtes prêt à rejoindre un réseau, vous pouvez déployer un homologue qui rejoindra des canaux, validera des transactions, et stockera les registres de canal. Pour plus d'informations sur le déploiement d'un homologue sur {{site.data.keyword.cloud_notm}} Private qui se connectera à d'autres composants sur {{site.data.keyword.cloud_notm}} Private, voir [Déploiement d'un homologue sur {{site.data.keyword.cloud_notm}} Private](/docs/services/blockchain/howto/peer_deploy_icp.html#icp-peer-deploy). Pour plus d'informations sur le déploiement d'un homologue sur {{site.data.keyword.cloud_notm}} Private qui se connectera à un réseau Starter or Enterprise Plan, voir [Déploiement d'un homologue qui se connectera à un réseau Starter ou Enterprise Plan](/docs/services/blockchain/howto/peer_deploy_ibp.html#ibp-peer-deploy).

- Une fois que vous avez configuré l'homologue, vous devez effectuer quelques étapes supplémentaires avant de soumettre des transactions et lire le registre partagé depuis le réseau de blockchain.

  - Si vous connectez votre homologue à une plateforme {{site.data.keyword.blockchainfull_notm}} Platform déployée sur {{site.data.keyword.cloud_notm}} Private, voir [Exploitation des homologues sur {{site.data.keyword.cloud_notm}} Private avec un réseau multi-cloud](/docs/services/blockchain/howto/peer_operate_icp.html#icp-peer-operate).
  - Si vous connectez votre homologue à un réseau Starter Plan ou Enterprise Plan qui est déployé sur {{site.data.keyword.cloud_notm}}, voir [Exploitation des homologues sur {{site.data.keyword.cloud_notm}} Private avec un réseau Starter Plan ou Enterprise Plan](/docs/services/blockchain/howto/peer_operate_ibp.html#ibp-peer-operate).

## Remarques sur la sécurité
{: #ibp-icp-about-security}

Etant donné que les composants sont déployés à l'extérieur de {{site.data.keyword.cloud_notm}}, vous êtes responsable de la gestion de leur sécurité. Cela inclut des zones de sécurité importantes, comme la gestion des clés et le chiffrement de données. Passez en revue les rubriques suivantes concernant la sécurité de vos composants.

### Sécurité des données
{: #ibp-icp-about-security-data}

Les plans {{site.data.keyword.blockchainfull_notm}} Platform Starter et Enterprise utilisent le chiffrement de disque intégral qui repose sur la [cryptographie symétrique ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Cryptographie symétrique") pour protéger l'ensemble des données utilisées par le réseau. Vous devez suivre des étapes similaires dans votre environnement pour protéger les données de votre homologue.

Les données de votre base de données d'état, que vous utilisiez LevelDB ou CouchDB, ne sont pas chiffrées. Vous pouvez utiliser le chiffrement au niveau de l'application afin de protéger les données au repos dans votre base de données d'état.

### Hébergement de données
{: #ibp-icp-about-security-data-residency}

L'hébergement de données indique que le traitement et le stockage de l'ensemble des données de registre de blockchain demeurent à l'intérieur des frontières d'un pays unique. Pour plus de détails sur la mise en oeuvre de l'hébergement de données, voir [Hébergement de données](#ibp-icp-about-data-residency).

### Gestion des clés
{: #ibp-icp-about-security-key-management}

La gestion des clés est un aspect essentiel de la sécurité. Si une clé privée est compromise ou égarée, des acteurs hostiles pourraient accéder aux données et aux fonctionnalités. IBM utilise des dispositifs physiques appelés [modules de sécurité matérielle](/docs/services/blockchain/glossary.html#glossary-hsm) (HSM) pour stocker les clés privées des réseaux {{site.data.keyword.blockchainfull_notm}} Platform Enterprise Plan.

Lorsque vous déployez un composant sur {{site.data.keyword.cloud_notm}} Private, vous êtes responsable de la gestion de vos clés privées. Bien qu'{{site.data.keyword.blockchainfull_notm}} Platform génère des clés privées, ces clés ne sont pas stockées sur cette plateforme. Il est essentiel que vous stockiez vos clés dans un emplacement sécurisé afin qu'elles ne soient pas compromises. Vous pouvez trouver la clé privée de votre homologue dans le dossier du magasin de clés de votre MSP, dans le répertoire `/mnt/crypto/peer/peer/msp/keystore/` au sein de votre composant. Pour plus d'informations sur les certificats au sein de votre homologue, voir la section [Membership Services Provider](/docs/services/blockchain/certificates.html#managing-certificates-msp) du tutoriel [Gestion des certificats sur {{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/certificates.html#managing-certificates).

Vous pouvez utiliser Key Escrow pour récupérer des clés privées perdues. Cette opération doit être effectuée avant la perte de clés. Si une clé privée ne peut pas être récupérée, vous devez obtenir de nouvelles clés privées en enregistrant une nouvelle identité avec votre autorité de certification. Vous devez également retirer et remplacer votre signCert des canaux que vous avez rejoints.

### TLS
{: #ibp-icp-about-security-tls}

[Transport Layer Security ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "Une présentation de l'établissement de liaison SSL ou TLS") (TLS) est intégré au modèle de confiance d'Hyperledger Fabric. Tous les composants Starter et Enterprise sur {{site.data.keyword.blockchainfull_notm}} Platform utilisent TLS pour s'authentifier et communiquer entre eux. Par conséquent, les composants réseau sur Platform doivent pouvoir effectuer un établissement de liaison TLS avec vos homologues. Ceci implique entre autres que vous devez activer le passe-système, en utilisant des listes blanches par exemple, dans le pare-feu entre vos applications client et votre homologue.

### Configuration de Membership Service Provider (Fournisseur de services aux membres)
{: #ibp-icp-about-security-MSP}

Les composants d'{{site.data.keyword.blockchainfull_notm}} Platform consomment des identités via des Fournisseur de services aux membres (MSP). Les MSP associent les certificats émis par les autorités de certification à des rôles de réseau et de canal. Pour plus d'informations sur le fonctionnement des MSP avec l'homologue, voir [Membership Service Providers (MSPs)](/docs/services/blockchain/certificates.html#managing-certificates-msp).

### Sécurité des applications
{: #ibp-icp-about-security-appl}

Tous les appels de code blockchain étant signés, Fabric gère la sécurité des applications. De plus, Fabric inclut également des vérifications au niveau de l'application basées sur ACL.

## Hébergement de données
{: #ibp-icp-about-data-residency}

Etant donné que les réseaux de blockchain dépendent du type de données qui sont traitées, des étapes supplémentaires sont parfois nécessaires pour assurer la sécurité de certains types de données. L'exigence la plus courante sur l'hébergement de données est associée aux réglementations de certains pays, qui demandent que toutes les données qui sont traitées et stockées sur un système informatique doivent demeurer à l'intérieur des frontières d'un pays donné. De même, certaines entreprises dans des secteurs extrêmement régulés, comme le gouvernement, la santé et les services financiers, exigent que les données soient entièrement stockées derrière leur pare-feu. Par conséquent, pour appliquer l'hébergement de données, tous les composants du réseau de blockchain doivent faire partie du même [canal](/docs/services/blockchain/glossary.html#glossary-channel) et se trouver à l'intérieur des frontières d'un seul pays.

Pour respecter les exigences d'hébergement de données, il est important de comprendre l'architecture Hyperledger Fabric qui est sous-jacente à {{site.data.keyword.blockchainfull_notm}} Platform. L'architecture est centrée autour de trois composants clés : un service de tri (composé de tris), des autorités de certification et des homologues. Un homologue reçoit les mises à jour de l'état de tri sous la forme de blocs du service de tri et gère l'état et la registre. Par conséquent, un homologue et un service de tri ont une relation directe. Le registre contient les valeurs les plus récentes de toutes les clés et les données incluses dans les journaux de transaction.

En outre, les applications client utilisent les [logiciels SDK Fabric](/docs/services/blockchain/v10_application.html#dev-app-fabric-sdks) pour envoyer des transactions aux homologues et au service de tri. Ces transactions incluent les données [read-write set ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/readwrite.html "Read-Write set semantics"), qui contiennent les paires clé-valeur sur le registre.

Si l'hébergement de données dans le pays est une exigence, le service de tri, les homologues et les applications client doivent résider dans le même pays. Lorsqu'un réseau {{site.data.keyword.blockchainfull_notm}} Platform est créé dans {{site.data.keyword.cloud_notm}}, vous avez la possibilité de choisir un emplacement pour le réseau. <!--For a Starter Plan network, you can select from US South, United Kingdom, and Sydney. For an Enterprise Plan network, you can select from currently available locations, which include Dallas, Frankfurt, London, Sao Paulo, Tokyo, and Toronto. -->Pour plus d'informations sur les régions et les emplacements, voir [Régions et emplacements d'{{site.data.keyword.blockchainfull_notm}} Platform](/docs/services/blockchain/reference/ibp_regions.html#ibp-regions-locations).

### Cas d'utilisation de l'hébergement des données dans un pays

Envisagez d'utiliser un réseau {{site.data.keyword.blockchainfull_notm}} Platform qui inclut le service de tri défini et une autorité de certification avec un consortium de quatre organisations. Les organisations ont une ou plusieurs noeuds homologues, et les quatre organisations font partie d'un canal unique et tous les composants de réseau résident dans la région (par exemple, Francfort) où le réseau {{site.data.keyword.blockchainfull_notm}} Platform a été déployé. Enfin, les applications client qui interagissent avec les homologues résident également en Allemagne.

Dans cet exemple, l'hébergement de données est géré.

![Hébergement de données lorsque tous les composants  résident dans le même pays](images/remote_peer_data_res_1.png "Hébergement de données lorsque tous les composants résident dans le même pays")
*Figure 1. Hébergement de données lorsque tous les composants résident dans le même pays*

Nous allons maintenant étudier les conséquences lorsqu'un **homologue distribué** rejoint l'une des organisations. Un homologue distribué peut résider dans la même région que le reste du réseau ou en dehors de la région du réseau de {{site.data.keyword.blockchainfull_notm}} Platform :

-	Si l'homologue réside dans le même pays que le reste du réseau, l'hébergement de données est géré. Toutes les données du registre demeurent en Allemagne comme illustré dans la **Figure 1** ci-dessus.
-	Si l'homologue réside dans un pays différent (par exemple, les Etats-Unis), l'hébergement de données n'est plus géré car les données sur le registre homologue sont partagées à l'extérieur des frontières.

Pour résoudre ce problème, des **canaux** peuvent être utilisés pour séparer les données dans un sous-ensemble d'homologues sur le réseau. Lorsque le réseau {{site.data.keyword.blockchainfull_notm}} Platform comporte un homologue distribué et des services de tri entres les frontières de pays, les canaux assurent l'isolement des données de registre des organisations avec des homologues à l'extérieur des frontières.

**Remarque :** Les services de tri sont toujours situés dans la région du centre de données que vous avez sélectionnée pour héberger le réseau. Il n'est pas possible d'avoir plusieurs services de tri entre les frontières. Les homologues peuvent cependant se situer dans le centre de données ou sur un site distant à l'extérieur de {{site.data.keyword.cloud_notm}}.

![Hébergement de données lorsque les homologues résident à l'extérieur du pays de la région {{site.data.keyword.blockchainfull_notm}} Platform](images/remote_peer_data_res_2.png "Hébergement de données lorsque les homologues résident à l'extérieur du pays de la région d'{{site.data.keyword.blockchainfull_notm}} Platform")
*Figure 2. Hébergement de données lorsque les homologues résident à l'extérieur du pays de la région {{site.data.keyword.blockchainfull_notm}} Platform*

Dans la **Figure 2**, l'hébergement de données n'est pas obligatoire pour `OrgC` et `OrgD`. En fait, `OrgD` inclut désormais deux homologues distribués,`OrgD-peer1` et `OrgD-peer2`, qui résident aux *Etats-Unis*. Par conséquent, pour que `OrgA`, `OrgB` et leurs applications client et homologues respectifs qui résident en Allemagne puissent isoler les données de registre sur le canal `X`, un nouveau canal `Y` est créé pour `OrgC` et `OrgD`.

Pour une meilleure compréhension du flux de données sur le réseau de {{site.data.keyword.blockchainfull_notm}} Platform, voir la [documentation Fabric sur le flux de transactions![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html "Flux de transactions").

A l'avenir, la nouvelle technologie dans Hyperledger Fabric améliorera la capacité à réaliser d'autres hébergements de données l'étranger en utilisant des [collectes de données privées ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html "Collectes de données privées") et zéro connaissance.

- Une collecte de données privées garantit que les données privées sont partagés homologue par homologue (via le protocole gossip) sur des homologues autorisés uniquement pour voir si, par exemple, des homologues se trouvent entre les frontières d'un pays. Les données sont stockées dans une base de données privée sur l'homologue. Le service de tri n'est pas impliqué ici et il ne voit pas les données privées. Un hachage de ces données est écrit dans les registres de chaque homologue sur le canal. Le hachage qui est utilisé pour la validation d'état fait office de preuve de la transaction, et il peut être utilisé à des fins d'audit. Des données privées sont disponibles pour les réseaux sur {{site.data.keyword.blockchainfull_notm}} Platform qui s'exécutent dans Fabric version 1.2.1 ou ultérieure. Toutefois, la fonction Données privées n'est pas disponible pour les homologues s'exécutant dans {{site.data.keyword.cloud_notm}} Private, car les réseaux {{site.data.keyword.cloud_notm}} Private n'utilisent pas actuellement gossip.

- Une preuve ZKP (Zero-Knowledge Proof) permet à un “approbateur” de garantir à un “vérificateur” qu'ils ont la connaissance d'une valeur confidentielle sans révéler la valeur confidentielle elle-même.

Vous trouverez davantage d'informations sur ces technologies dans le livre blanc sur les [transactions privées et confidentielles avec Hyperledger Fabric ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://developer.ibm.com/tutorials/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/ "Transactions privées et confidentielles avec Hyperledger Fabric").

## Support
{: #ibp-icp-about-support}

Pour plus d'informations sur les offres de support numérique, voir les [ressources et forums de support](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-resources) d'{{site.data.keyword.blockchainfull_notm}} Platform.

### {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private

Si vous avez acheté une licence {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private et souhaitez contacter le support client, consultez les informations relatives à l'[accès à IBM Support Community et à l'ouverture d'un ticket de demande de service![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www-01.ibm.com/support/docview.wss?uid=ibm10740041 "{{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} Private Support").

### Edition Community d'{{site.data.keyword.blockchainfull_notm}} Platform

L'édition Community est destinée à l'exploration, au développement et aux tests. {{site.data.keyword.blockchainfull_notm}} Platform n'assure pas de support pour l'édition Community d'{{site.data.keyword.blockchainfull_notm}} Platform.

Si vous rencontrez des problèmes liés à vos composants blockchain, vous pouvez utiliser des ressources développeur de blockchain gratuites, des forums de support et obtenir de l'aide auprès d'IBM et de la communauté Fabric. Pour plus d'informations, voir les [ressources et forums de support blockchain](/docs/services/blockchain/ibmblockchain_support.html#blockchain-support-resources).

Pour les problèmes liés à IBM Cloud Private, vous pouvez bénéficier d'un [support numérique gratuit et d'un support payant proposés par {{site.data.keyword.cloud_notm}} Private](https://www.ibm.com/developerworks/community/blogs/fe25b4ef-ea6a-4d86-a629-6f87ccf4649e/entry/Learn_more_about_IBM_Cloud_Private_Support?lang=en_us).
