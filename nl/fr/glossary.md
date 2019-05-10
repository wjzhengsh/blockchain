---

copyright:
  years: 2017, 2019
lastupdated: "2019-04-17"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Glossaire
{: #glossary}

Cette rubrique définit les termes spécifiques à {{site.data.keyword.blockchainfull}} Platform qui apparaissent dans cette documentation. Pour une meilleure compréhension de ces termes, et pour afficher un glossaire des termes relatifs aux concepts Hyperledger Fabric, voir la section [Glossaire Hyperledger Fabric![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.2/glossary.html).
{:shortdesc}

## AC
{: #glossary-CA}
Abréviation de "Autorité de certification". Il s'agit du composant qui émet des certificats pour tous les membres participants. Ces certificats représentent l'identité d'un membre. Toutes les entités du réseau (homologues, services de tri, clients, etc.) doivent posséder une identité pour communiquer, authentifier et enfin effectuer des transactions. Ces identités sont nécessaires à toute participation directe au réseau de blockchain.

## Adhésion
{: #glossary-endorsement}
Processus par lequel les transactions de code blockchain sont validées. Les règles de validation sont implémentées en indiquant des directives de validation.

## Appartenance dynamique
{: #glossary-dynamic-memership}
Un membre peut être ajouté de manière dynamique au réseau par un utilisateur disposant du privilège **registre**. Les membres se voient également affecter des rôles et des attributs, qui contrôlent leur accès et leurs droits sur le réseau. Les rôles et les attributs ne peuvent pas être affectés de manière dynamique. Hyperledger Fabric prend en charge l'ajout ou le retrait de membres, homologues, et noeuds de service de classement, sans compromettre les opérations du réseau dans son ensemble. L'appartenance dynamique est essentielle lors de l'ajustement des relations commerciales et que les entités doivent être ajoutées ou retirées pour différentes raisons.

## Base de données d'état
{: #glossary-state-database}
L'état actuel est stocké dans une base de données sur les homologues pour des lectures et requêtes efficaces depuis le code blockchain. Les réseaux du plan Starter utilisent CouchDB comme base de données d'état. Les réseaux du plan Enterprise peuvent utiliser soit LevelDB, soit CouchDB.

## Bloc
{: #glossary-block}
Ensemble ordonné de transactions, lié cryptographiquement au bloc précédent dans un canal.

## Bloc d'origine
{: #glossary-genesis-block}
Bloc de configuration qui initialise un réseau de blockchain ou un canal, et fait également office de premier bloc dans une chaîne.

## Canal
{: #glossary-channel}
Un canal se compose d'un sous-ensemble de membres réseau qui souhaitent effectuer des transactions en mode privé. Les canaux fournissent l'isolement de données et la confidentialité en autorisant les membres d'un canal à établir des règles spécifiques et un registre, auxquels seuls les membres du canal peuvent accéder. Les homologues, qui sont les noeuds qui fonctionnent en tant que noeuds finaux de transaction pour les organisations, sont joints aux canaux.

## Certificat signCert
{: #glossary-sign-cert}
Certificat que des entités, qu'il s'agisse d'organisations ou d'admin, joignent à leurs propositions ou réponses à une proposition. Ces certificats signCerts sont uniques à une entité et ils sont vérifiés par le service de tri qui s'assure qu'ils correspondent au certificat signCert dans le fichier de cette entité.

## Chaîne
{: #glossary-chain}
La chaîne du registre est un journal des transactions structuré sous la forme de blocs de transactions liés par hachage. Les homologues reçoivent des blocs de transactions du service de tri, marquent les transactions de bloc comme étant valides ou non valides sur la base de règles de validation et de violations des accès concurrents, puis ajoutent les blocs à la chaîne de hachage sur le système de fichiers de l'homologue.

## Client
{: #glossary-client}
Le client représente l'entité qui agit pour le compte d'un utilisateur. Il doit se connecter à un homologue pour communiquer avec la blockchain. Le client peut se connecter à un homologue de son choix. Les clients créent et par conséquent appellent des transactions. Le client soumet un appel de transaction aux valideurs, puis diffuse les propositions de transaction au service de tri.

## Code blockchain
{: #glossary-chaincode}

Egalement appelé **contrats intelligents**, le code blockchain est constitué d'éléments logiciels qui contiennent un ensemble de fonctions permettant d'interroger et de mettre à jour le registre.

## Consensus
{: #glossary-consensus}
Processus collaboratif qui permet de conserver les transactions de registre synchronisées au sein du réseau. Le consensus garantit que les registres sont mises à jour uniquement lorsque les participants appropriés approuvent les transactions, et que les registres sont mis à jour avec les mêmes transactions dans le même ordre. De nombreux modes algorithmiques différents permettent d'atteindre ce consensus.

## Consortium
{: #glossary-consortium}
Groupe d'organisations autres que des services de tri non répertoriées sur le canal système du service de tri. Il s'agit des seules organisations qui peuvent créer des canaux. Lors de la création du canal, toutes les organisations ajoutées au canal doivent faire partie d'un consortium. Cependant, une organisation qui n'est pas définie dans un consortium peut être ajoutée à un canal existant. Alors qu'un réseau peut avoir plusieurs consortiums, la plupart des réseaux de blockchain ont un seul consortium.

## CouchDB
{: #glossary-couchdb}
Magasin de documents utilisé pour la base de données d'état dans les réseaux du plan Starter. CouchDB est également une option pour les réseaux du plan Enterprise, de pair avec LevelDB. CouchDB prend en charge l'utilisation d'index et vous permet d'exécuter des requêtes approfondies sur les données de votre homologue.

## Données d'identification du service
{: #glossary-service-credentials}
Les données d'identification du service sont disponibles au format JSON et elles contiennent les informations de noeud final d'API et les ID d'inscription/valeurs confidentielles de vos ressources réseau, c'est-à-dire les autorités de certification, les services de tri et les homologues. Votre application interagit avec les ressources réseau via ces noeuds finaux d'API.

## Données d'identification réseau
{: #glossary-network-credentials}
Les données d'identification sont visibles depuis l'écran "API" du Moniteur réseau. Elles incluent votre "clé" (nom d'utilisateur) et votre "valeur confidentielle" (mot de passe) dans l'interface utilisateur Swagger. Vous devez utiliser ces données d'identification réseau pour vous authentifier avant de tester les API REST.

## Etat en cours
{: #glossary-current-state}

L'état en cours du registre représente les valeurs les plus récentes pour toutes les clés qui sont incluses dans son journal des transactions de chaîne. Etant donné que l'état actuel représente toutes les valeurs de clé les plus récentes connues du canal, il est parfois appelé "World State". Le code blockchain exécute les propositions de transaction en fonction des données d'état actuel. L'état actuel change chaque fois que la valeur d'une clé change ou qu'une nouvelle clé est ajoutée. L'état actuel est critique pour un flux de transaction car la dernière paire clé-valeur doit être connue pour pouvoir être modifiée. Les homologues valident les valeurs les plus récentes en fonction de l'état actuel du registre pour chaque transaction valide dans un bloc. L'état actuel est stocké dans une base de données d'état d'un homologue.

## Gossip
{: #glossary-gossip}
Hyperledger Fabric permet aux homologues de regrouper d'importantes informations réseau les uns des autres sans avoir à reposer sur le service de tri. Le [protocole de dissémination de données Gossip![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://hyperledger-fabric.readthedocs.io/en/release-1.1/gossip.html) constitue un moyen sécurisé, fiable et évolutif pour que les homologues puissent échanger des messages entre eux. Par exemple, si des homologues perdent certains blocs en raison de retards, de pannes réseau, ou pour d'autres raisons, ils peuvent se synchroniser sur l'état du registre actuel en utilisant des messages Gossip pour contacter d'autres homologues en possession de ces blocs manquants.

## Homologue
{: #glossary-peer}
Ressource de réseau blockchain qui fournit les services permettant d'exécuter et de valider les transactions, et de gérer les registres. L'homologue exécute du code blockchain et il détient l'historique de transactions ainsi que l'état en cours des actifs sur les canaux du réseau, c'est-à-dire le registre. Ceux-ci sont détenus et gérés par des organisations et associés à des canaux.

## HSM
{: #glossary-hsm}
Module de sécurité matérielle (HSM). Permet un chiffrement à la demande, la gestion des clés et le stockage de clés en tant que service géré. HSM est un dispositif physique qui gère les tâches gourmandes en ressources de chiffrement et réduit le temps de latence des applications. Pour plus d'informations, voir [Module de sécurité matérielle ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://www.ibm.com/cloud/hardware-security-module)

## Hyperledger Fabric
{: #glossary-hyperledger-fabric}
[Hyperledger Fabric ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](http://hyperledger-fabric.readthedocs.io/en/release-1.4/) est une architecture de blockchain d'entreprise hébergée par Linux Foundation pour servir de base au développement d'applications ou de solutions blockchain avec une architecture modulaire. Les composants Hyperledger Fabric comme les services de consensus et d'appartenance sont de type plug-and-play.

## Installer
{: #glossary-install}
Processus consistant à placer un code blockchain sur le système de fichiers d'un homologue. Vous devez installer le code blockchain sur chaque homologue qui va exécuter le code blockchain.

## Instancier
{: #glossary-instantiate}
Processus consistant à démarrer et à initialiser un conteneur de code blockchain sur un canal spécifique. Dès que le code blockchain est installé sur les homologues et que chaque homologue a rejoint le canal, le code blockchain doit être instancié sur le canal. L'instanciation doit effectuer l'initialisation nécessaire du code blockchain, ce qui inclut la base de données "world state" initiale d'un code blockchain. Après l'instanciation, les homologues sur lesquels le code blockchain est installé peuvent accepter les appels de code blockchain.

## Kafka
{: #glossary-kafka}
Implémentation de plug-in de consensus pour Hyperledger Fabric qui se traduit par un cluster de noeuds de service de tri dans le réseau de blockchain. Une implémentation Kafka est destinée à un réseau de production.

## LevelDB
{: #glossary-leveldb}
Magasin clés-valeurs en option pour la base de données d'état des réseaux du plan Enterprise, ainsi que CouchDB. LevelDB stocke l'état actuel dans des paires clé-valeur et ne gère pas l'utilisation d'index ou de recherches approfondies.

## Membre
{: #glossary-member}

Egalement appelés "organisations", les membres d'un réseau de blockchain, semblables aux membres d'un groupe, forment la structure du réseau. Un membre peut avoir la taille d'une multinationale ou d'un individu. Les membres sont inscrits sur le réseau avec un certificat qui leur accorde le droit d'utiliser le réseau en tant que fournisseur de services (par exemple, en émettant des certificats, en validant/ordonnant des transactions) ou en tant que consommateur. Le premier fournit des services de blockchain de base qui incluent la validation de transaction, le classement des transactions ainsi que des services de gestion de certificats. Les membres consommateurs utilisent le réseau pour appeler des transactions par rapport au registre partagé. Les membres peuvent comporter plusieurs homologues.

## Moniteur réseau
{: #glossary-network-monitor}
Tableau de bord de l'interface graphique fourni par {{site.data.keyword.blockchainfull_notm}} Platform pour afficher et gérer le réseau de blockchain.

## MSP
{: #glossary-msp}
Membership Service Provider (Fournisseur de services aux membres).  Ensemble de mécanismes et protocoles de chiffrement pour l'émission et la validation des certificats et des identités au sein du réseau de blockchain. Les identités qui sont émises dans la portée d'un fournisseur de services d'appartenance peuvent être évaluées au sein des règles de validation des règles du fournisseur de services d'appartenance. Le fournisseur MSP est installé sur chaque homologue de canal afin de garantir que les demandes de transaction qui sont émises pour l'homologue proviennent d'une identité utilisateur authentifiée et autorisée.

## Noeud
{: #glossary-node}
Entité de communication de la blockchain. Il existe trois types de noeuds : AC, Homologue et Service de tri.

## Organisation
{: #glossary-organization}
Voir [Membre](/docs/services/blockchain/glossary.html#glossary-member).

## Participant
{: #glossary-participant}
Organisation, individu, application ou appareil qui interagit avec le réseau de blockchain. Sous l'appellation participant, on distingue deux groupes : les membres et les utilisateurs.

## Profil de connexion
{: #glossary-connection-profile}
Le profil de connexion est visible dans l'écran "Présentation" du Moniteur réseau lorsque vous cliquez sur le bouton **Profil de connexion**. Les informations sont disponibles au format JSON et elles contiennent les informations de noeud final d'API et les ID d'inscription/valeurs confidentielles de vos ressources réseau, c'est-à-dire les homologues, les services de tri et les autorités de certification. Votre application interagit avec les ressources réseau via ces noeuds finaux d'API.

## Registre
{: #glossary-ledger}
Le registre comprend une "chaîne de blocs" littérale qui stocke un enregistrement immuable et séquencé de transactions, ainsi qu'une base de données d'état pour le maintien de l'état en cours. Il y a un registre par canal, et les mises à jour de ce dernier sont gérées par le processus de consensus en fonction des règles d'un canal particulier.

## Règle d'adhésion
{: #glossary-endorsement-policy}
Définit les noeuds homologue sur un canal qui doivent exécuter des transactions liées à une application de code blockchain spécifique, ainsi que la combinaison nécessaire de réponses (adhésions). Une règle peut exiger qu'une transaction soit validée par un nombre minimum d'homologues valideurs, un pourcentage minimum d'homologues valideurs ou par tous les homologues valideurs qui sont affectés à une application de code blockchain spécifique. Les règles peuvent être organisées en fonction de l'application et du niveau souhaité de résilience par rapport à un comportement inapproprié, qu'il soit ou non délibéré, par les homologues d'adhésion. Une transaction soumise doit respecter la règle de validation pour pouvoir être marquée comme valide par les homologues d'adhésion. Une règle distincte de validation pour l'installation et l'instanciation des transactions est également requise.

## Réseau
{: #glossary-network}
Instance d'un service {{site.data.keyword.blockchainfull_notm}} Platform sur {{site.data.keyword.cloud_notm}}.

## Ressource
{: #glossary-asset}
Biens matériels ou immatériels, services ou propriété représentés en tant qu'élément qui fait l'objet d'une transaction sur le réseau de blockchain.

## SDK
{: #glossary-sdk}
Hyperledger Fabric prend en charge deux kits SDK : Node SDK et Java SDK.  Node SDK peut être installé via NPM et Java SDK via Maven.  Les kits SDK ont leurs propres référentiels Git, c'est-à-dire, [Fabric Node SDK ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://github.com/hyperledger/fabric-sdk-node) et  [Fabric Java SDK ![Icône de lien externe](images/external_link.svg "Icône de lien externe")](https://github.com/hyperledger/fabric-sdk-java), ainsi que la documentation associée aux API disponibles. Les kits SDK de Hyperledger Fabric Client permettent l'interaction entre votre application client et votre réseau de blockchain.

## Service de tri
{: #glossary-orderer}
Noeud qui collecte les transactions des membres réseau, trie les transactions et les regroupe dans des blocs. Ces blocs sont ensuite distribués aux homologues, lesquels vérifient les blocs et les ajoutent aux registres sur chaque canal. Les services de tri contiennent les identités de chiffrement qui sont liées à chaque membre et ils authentifient l'identité des clients et des homologues pour l'accès au réseau. La fonction globale fournie par un noeud ou une collection de noeuds de service de tri est appelée **service de tri**.

## Smart contracts
{: #glossary-smart-contracts}
Voir [Code blockchain](/docs/services/blockchain/glossary.html#glossary-chaincode).

## SOLO
{: #glossary-solo}
Implémentation de plug-in de consensus pour Hyperledger Fabric qui se traduit par un seul service de tri dans le réseau de blockchain. Le réseau de plan Starter utilise l'implémentation SOLO. Une implémentation SOLO n'est pas destinée à un réseau de production. L'alternative au consensus SOLO est un cluster Kafka.

## Transaction
{: #glossary-transaction}
Mécanisme mis en oeuvre par les participants sur le réseau de blockchain pour interagir avec les ressources. Une transaction crée un nouveau code blockchain ou appelle une opération dans un code blockchain existant.

## Utilisateur
{: #glossary-user}
Un utilisateur a le rôle d'un participant au sein d'un réseau de blockchain et qui a un accès indirect au registre via une relation d'accréditation avec un membre existant.

## World state
{: #glossary-world-state}
Voir [Etat en cours](/docs/services/blockchain/glossary.html#glossary-current-state).
