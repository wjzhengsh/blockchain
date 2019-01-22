---

copyright:
  years: 2017, 2018
lastupdated: "2018-11-27"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Distribuzione di {{site.data.keyword.blockchainfull_notm}} Platform for AWS
{: #remote-peer-aws}


***[Questa pagina è utile? Faccelo sapere.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


Queste istruzioni descrivono come utilizzare un template Quick Start AWS (Amazon Web Services) per creare un peer {{site.data.keyword.blockchainfull}}  Platform for AWS e connetterlo quindi a una rete su {{site.data.keyword.blockchainfull_notm}} Platform.
{:shortdesc}

Per ulteriori informazioni su AWS, vedi il [documento di panoramica di AWS![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://d1.awsstatic.com/whitepapers/aws-overview.pdf "documento di panoramica di AWS").

Prima di distribuire i peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS, riesamina le [considerazioni sui peer](remote_peer.html#remote-peer-limitations).

## Prerequisiti
{: #prerequisites-aws}

Per utilizzare un peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS, devi avere un'organizzazione che sia un membro di una rete blockchain ospitata su IBM Blockchain Platform. Devi utilizzare il Monitoraggio della rete su IBM Cloud per accedere alle credenziali di rete e agli endpoint API della tua rete. Se non sei membro di alcuna rete blockchain, devi creare o aderire a una rete. Per ulteriori informazioni, vedi [Creazione di una rete](../get_start.html#creating-a-network) o [Adesione a una rete](../get_start.html#joining-a-network).

Il tipo di istanza VPC predefinito per il peer è `m4.xlarge`. Devi ottimizzare il tipo di istanza che scegli in base ai tuoi requisiti di CPU, memoria e archiviazione. Il peer richiede almeno:  
-	CPU 2x
-	2 GB di RAM
-	4 GB di spazio per il chaincode
-	10 GB di spazio per il libro mastro con capacità di crescere man mano che il libro mastro si espande

Questi livelli minimi di risorse sono sufficienti per l'esecuzione di test e la sperimentazione. Per un ambiente di produzione, è importante assegnare una quantità sufficientemente grande di archiviazione, 100GB ad esempio.La quantità di archiviazione utilizzata dipenderà dal numero di transazioni e dal numero di firme richiesti dalla tua rete. Se esaurisci l'archiviazione sul tuo peer, devi <!-- either expand the storage or --> distribuire un nuovo peer con un file system più grande e consentirgli di eseguire la sincronizzazione tramite il tuo altro peer (o i tuoi altri peer) sullo stesso canale (o sugli stessi canali).


## Opzioni di distribuzione
{: #remote-peer-aws-deploy-options}

Il Quick Start fornisce due opzioni di distribuzione:

* Distribuisci {{site.data.keyword.blockchainfull_notm}} Platform for AWS in un nuovo VPC (distribuzione end-to-end). Questa opzione crea un nuovo ambiente AWS che consiste di VPC, sottoreti, gateway NAT, gruppi di sicurezza, host bastion e altri componenti dell'infrastruttura e distribuisce quindi il peer in questo nuovo VPC.

* Distribuisci {{site.data.keyword.blockchainfull_notm}} Platform for AWS in un VPC esistente. Questa opzione esegue il provisioning del peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS nella tua infrastruttura AWS esistente. Ci sono template separati per queste opzioni in cui puoi configurare blocchi CIDR, tipi di istanza e impostazioni peer, come discusso più avanti in questa guida.

## Passo uno: Prepara il tuo account AWS
{: #remote-peer-aws-account}

1. Se non hai già un account AWS, creane uno [qui ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://aws.amazon.com "https//aws/amazon.com") seguendo le istruzioni visualizzate sullo schermo.

2. Utilizza il selettore della regione nella barra di navigazione per scegliere la regione AWS in cui vuoi distribuire il peer in AWS.

3. Crea una coppia di chiavi nella tua regione preferita.

4. Se necessario, richiedi un aumento del limite del servizio per il tipo di istanza Amazon EC2 <type>. Potresti aver bisogno di eseguire tale operazione se già hai una distribuzione esistente che usa questo tipo di istanza e pensi che potrebbe superare il limite predefinito con questa distribuzione.

## Passo due: Richiama le informazioni di configurazione del tuo peer remoto
{: #aws-network-endpoints}

Durante la configurazione, devi fornire gli endpoint API della tua rete al tuo peer. Questi endpoint consentono a un peer di trovare e connettersi alla rete su {{site.data.keyword.blockchainfull_notm}} Platform. Nella schermata **Panoramica** del tuo Monitoraggio della rete, fai clic sul pulsante **Configurazione peer remoto**.

![Configurazione peer remoto](../images/myresources_starter.png "Configurazione peer remoto")
*Figura 1. Pannello Configurazione peer remoto*

Si apre una finestra a comparsa che mostra i valori dei seguenti campi. Salva i valori dei seguenti campi; sono obbligatori quando configuri il peer utilizzando il template Quick Start AWS.

- **MSP organizzazione**
- **Nome CA (Certificate Authority)**
- **URL CA (Certificate Authority)**
- **Certificato TLS CA (Certificate Authority)**

Puoi copiare e incollare ciascun campo direttamente nel template Quick Start o salvarli in un file JSON facendo clic sul link **Download**.

**Nota:** se scarichi le informazioni in JSON, devi convertire il certificato TLS in formato PEM prima di fornirlo al peer. Converti il **Certificato TLS CA (Certificate Authority)** nel file JSON che hai scaricato in formato PEM immettendo il comando:
```
echo -e "<CERT>" > admin.pem
```
{:codeblock}

Sostituisci `<CERT>` con il valore del **Certificato TLS CA (Certificate Authority)**. Quindi, quando ti viene richiesto il **Certificato TLS CA (Certificate Authority)** nel template Quick Start, esegui il `cat` del file admin.pem e procedi quindi a copiare e incollare il contenuto nel campo.  

## Passo tre: Registra un peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS
{: #aws-register-peer}

Devi aggiungere una nuova identità peer alla tua organizzazione su {{site.data.keyword.blockchainfull_notm}} Platform prima che il peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS possa unirsi alla rete. Per registrare un peer, completa la seguente procedura.

**Nota:** per l'alta disponibilità, il template Quick Start crea due nodi peer nelle due zone di disponibilità. Sono pertanto necessari due segreti e ID di iscrizione.**Ripeti questo processo due volte per generare due segreti e ID di iscrizione**

1. Accedi al Monitoraggio della rete della tua rete su {{site.data.keyword.blockchainfull_notm}} Platform. Nella schermata "CA (Certificate Authority)" del tuo Monitoraggio della rete, puoi visualizzare tutte le identità che sono state registrate con la rete, come ad esempio l'amministratore o le applicazioni client.
  ![Schermata CA](../images/CA_screen_starter.png "Schermata CA")
  *Figura 2. Schermata CA*

2. Fai clic sul pulsante **Aggiungi utente** sul pannello. Si apre una schermata a comparsa che ti consente di registrare il tuo peer alla rete dopo aver compilato i campi di seguito. **Salva il valore di ID e segreto per dopo quando configuri il tuo peer nel template Quick Start,**
  - **ID di registrazione:** il nome che vuoi utilizzare per il tuo peer, a cui si fa riferimento come `enroll ID` quando configuri il tuo peer. **Salva questo valore** per un uso futuro.
  - **Segreto di registrazione:** la password che vuoi utilizzare per il tuo peer, a cui si fa riferimento come `enroll Secret` quando configuri il tuo peer. **Salva questo valore** per un uso futuro.
  - **Tipo:** seleziona `peer` per questo campo.
  - **Affiliazione:** si tratta dell'affiliazione sotto la tua organizzazione, ad esempio `org1`, a cui appartiene il tuo peer. Puoi specificare una nuova affiliazione o utilizzarne una esistente.
  - **Registrazioni massime:** utilizza questo campo per limitare il numero di volte in cui puoi registrare o generare certificati utilizzando questa identità. Se non specificato, il valore predefinito è di registrazioni illimitate.

  Dopo aver completato questi campi, fai clic su **Inoltra** per registrare il peer. Il peer registrato viene quindi elencato nella tabella come identità sulla rete. Come misura di sicurezza, utilizza ogni identità, e l'ID registrazione e il segreto di accompagnamento, per distribuire solo un singolo peer. Non riutilizzare ID e password dei peer.

## Passo quattro: Avvia Quick Start
{: #remote-peer-aws-launchqs}

Sei responsabile per il costo dei servizi AWS che utilizzi mentre esegui questa distribuzione di riferimento Quick Start. Non ci sono costi aggiuntivi per l'utilizzo di questo Quick Start. Per i dettagli completi, vedi le pagine dei prezzi per ciascun servizio AWS che devi utilizzare in questo Quick Start. I prezzi sono soggetti a modifiche.

1. Scegli una delle seguenti opzioni per avviare il template CloudFormation AWS nel tuo account AWS. Per un aiuto nella scelta di un'opzione, vedi le opzioni di distribuzione indicate in precedenza in questa guida. Il completamento di ogni distribuzione richiede circa 10 minuti.  

  * [Distribuisci {{site.data.keyword.blockchainfull_notm}} Platform for AWS in un nuovo VPC su AWS ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fwd.aws/v43nk "Distribuisci {{site.data.keyword.blockchainfull_notm}} Platform for AWS in un nuovo VPC su AWS").  

  * [Distribuisci {{site.data.keyword.blockchainfull_notm}} Platform for AWS in un VPC esistente su AWS ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://fwd.aws/zrP4g "Distribuisci {{site.data.keyword.blockchainfull_notm}} Platform for AWS in un VPC esistente su AWS").

  **Importante:**     
Se stai distribuendo {{site.data.keyword.blockchainfull_notm}} Platform for AWS in un VPC esistente, assicurati che il tuo VPC abbia due sottoreti pubbliche in zone di disponibilità differenti per le istanze del database. Queste sottoreti richiedono gateway NAT o istanze NAT nelle loro tabelle di instradamento per consentire alle istanze di scaricare pacchetti e software senza esporli a internet. Avrai anche bisogno dell'opzione di nome dominio configurata nelle opzioni DHCP, come spiegato nella documentazione di VPC Amazon.  

  Assicurati inoltre di creare un gruppo di sicurezza collegato al tuo VPC esistente e di aggiungere regole in entrata sulle porte 22 e 7051 a questo gruppo di sicurezza. Le connessioni TCP sulla porta 22 consentono l'accesso SSH all'istanza generata mentre le connessioni TCP sulla porta 7051 consentono l'accesso gRPC esterno all'istanza peer (necessario per gestire il peer utilizzando le SDK Fabric e la CLI degli strumenti Fabric). Queste impostazioni VPC ti verranno richieste quando avvii il Quick Start.

2. Controlla la regione visualizzata nell'angolo in alto a destra della barra di navigazione e modificala, se necessario. È dove verrà creata l'infrastruttura di rete per il peer. Il template viene avviato, per impostazione predefinita, nella regione Stati Uniti Est (Ohio).

3. Nella pagina Seleziona template, mantieni l'impostazione predefinita per l'URL del template e scegli quindi `Avanti`.

4. Nella pagina Specifica dettagli, modifica il nome dello stack, se necessario. Esamina i parametri per il template. Fornisci i valori per i parametri che richiedono l'input. Per tutti gli altri parametri, esamina le impostazioni predefinite e personalizzale come necessario. Quando hai finito di esaminare e personalizzare i parametri, scegli `Avanti`.

Nelle seguenti tabelle, i parametri sono elencati per categoria e descritti separatamente per le due opzioni di distribuzione:

  * [Parametri per la distribuzione di {{site.data.keyword.blockchainfull_notm}} Platform for AWS in un nuovo VPC](#remote-peer-aws-parameters-newvpc)

  * [Parametro per la distribuzione di {{site.data.keyword.blockchainfull_notm}} Platform for AWS in un VPC esistente](#remote-peer-aws-parameters-existvpc).

### Parametri per la distribuzione di {{site.data.keyword.blockchainfull_notm}} Platform for AWS in un nuovo VPC
{: #remote-peer-aws-parameters-newvpc}

La seguente tabella elenca i parametri configurabili del grafico AWS e i loro valori predefiniti. Tutti i valori sono obbligatori.

|  Parametro    | Descrizione | Valore predefinito |
| --------------|-------------|---------|
| `Stack name` |Il nome stack è un identificativo che ti aiuta a trovare uno specifico stack in un elenco di stack. Un nome stack può contenere solo caratteri alfanumerici (sensibili al maiuscolo/minuscolo) e trattini. Deve iniziare con un carattere alfabetico e non può essere più lungo di 128 caratteri.| |
| | | |
| **Configurazione di rete** | |
| `Availability Zones` |Le due zone di disponibilità da utilizzare per le sottoreti nel VPC. Nota: l'ordine logico viene conservato. | |
| `Allowed SSH access CIDR` | [Blocco CIDR ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-resize "VPC e sottoreti") per l'accesso SSH esterno alle istanze di peer IBM Blockchain. Può essere impostato su 0.0.0.0/0 per consentire l'accesso da qualsiasi luogo (non consigliato). | |
| `PeerEndpointAccessCIDR` | [Blocco CIDR ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-resize "VPC e sottoreti") per l'accesso gRPC esterno alle istanze di peer IBM Blockchain. Viene di norma impostato su 0.0.0.0/0 per consentire l'accesso da qualsiasi luogo (non consigliato). | |
| | | |
| **Configurazione EC2 Amazon** | | |
| `InstanceType` | Il tipo di istanza EC2 per le istanze peer. | m4.xlarge |
| `KeyPairName` |Il nome della coppia di chiavi EC2 esistente all'interno della regione AWS. Devi eseguirne la generazione. | |
| | | |
|**Configurazione IBM Blockchain** | |
| `IBMBlockchainVersion` | Versione di IBM Blockchain da distribuire. | 1.2.1 |
| `StateDatabase` | Il tipo di database da utilizzare per archiviare lo stato delle blockchain. Questa selezione deve corrispondere al tipo di database dello stato utilizzato dal resto della rete. | CouchDB|
| `PeerVolumeSize` | La dimensione del volume EBS utilizzato per archiviare i dati persistenti (libro mastro, database dello stato, MSP) per il peer in GB. | 100 |
| `Peer 1 enroll ID`| L'ID di registrazione (iscrizione) che hai immesso nel tuo pannello di CA (Certificate Authority - Autorità di certificazione) dell'IU di IBM Blockchain Platform per il tuo primo peer. |  |
| `Peer 1 enroll secret` | Il segreto di registrazione (iscrizione) che hai immesso nel tuo pannello di CA (Certificate Authority - Autorità di certificazione) dell'IU di IBM Blockchain Platform per il tuo primo peer. | |
| `Peer 2 enroll ID` | L'ID di registrazione (iscrizione) che hai immesso nel tuo pannello di CA (Certificate Authority - Autorità di certificazione) dell'IU di IBM Blockchain Platform per il tuo secondo peer. | |
| `Peer 2 enroll secret` | Il segreto di registrazione (iscrizione) che hai immesso nel tuo pannello di CA (Certificate Authority - Autorità di certificazione) dell'IU di IBM Blockchain Platform per il tuo secondo peer. | |
| | | |
|**Credenziali del servizio IBM Blockchain**| | |
| `Organization MSP` | Questo valore è disponibile nella tua IU di IBM Blockchain Platform. Fai clic sul pulsante Configurazione peer remota nel pannello Panoramica e copia e incolla qui tali informazioni. | |
| `Certificate Authority (CA) Name` | Questo valore è disponibile nella tua IU di IBM Blockchain Platform. Fai clic sul pulsante Configurazione peer remota nel pannello Panoramica e copia e incolla qui tali informazioni. | |
| `Certificate Authority (CA) URL` | Questo valore è disponibile nella tua IU di IBM Blockchain Platform. Fai clic sul pulsante Configurazione peer remota nel pannello Panoramica e copia e incolla qui tali informazioni, compresa la porta. Se non viene specificata, la porta predefinita è 443. | |
| `Certificate Authority (CA)  TLS Certificate`| Questo valore è disponibile nella tua IU di IBM Blockchain Platform. Fai clic sul pulsante Configurazione peer remota nel pannello Panoramica e copia e incolla qui tali informazioni. | |
| | | |
|**Altri parametri**| | |
| `QSS3BucketName` | Il nome bucket S3 per gli asset Quick Start. Il nome bucket Quick Start può includere numeri, lettere minuscole, lettere maiuscole e trattini (-). Non può iniziare o terminare con un trattino (-). | `aws-quickstart` |
| `QSS3KeyPrefix` | Il prefisso chiave S3 per gli asset Quick Start. Il prefisso chiave Quick Start può includere numeri, lettere minuscole, lettere maiuscole, trattini (-) e barre (/). | `quickstart-ibm-fabric/` |

1. Nella pagina Opzioni, puoi specificare le tag (coppie chiave-valore) per le risorse nel tuo stack e impostare le opzioni avanzate. Quando hai finito, scegli Avanti.

2. Nella pagina Riesamina, riesamina e conferma le impostazioni del template. In Funzionalità, seleziona la casella di spunta per riconoscere che il template creerà le risorse IAM.

3. Scegli Crea per distribuire lo stack. 

4. Monitora lo stato dello stack. Quando lo stato di tutti gli stack è `CREATE_COMPLETE`, il cluster Peer è pronto. Dopo il corretto completamento dell'operazione, dovresti disporre di uno stack root con quattro stack nidificati se era stato scelto couchDB o due stack nidificati se era stato scelto levelDB.

5. Utilizza le informazioni visualizzate nella scheda Output per lo stack per visualizzare le risorse che sono state create.


### Parametri per la distribuzione di un peer {{site.data.keyword.blockchainfull_notm}} Platform in un VPC esistente
{: #remote-peer-aws-parameters-existvpc}

Se stai distribuendo il peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS in un VPC esistente, tieni conto di quanto segue:

 - Assicurati che il tuo VPC abbia due sottoreti private in zone di disponibilità differenti per le istanze del database. Queste sottoreti richiedono gateway NAT o istanze NAT nelle loro tabelle di instradamento per consentire alle istanze di scaricare pacchetti e software senza esporli a internet. 

 - Configura l'opzione di nome dominio nelle opzioni DHCP, come spiegato nella [documentazione di VPC Amazon![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_DHCP_Options.html "Set di opzioni DHCP").  

- Crea un gruppo di sicurezza collegato al tuo VPC esistente e aggiungi regole in entrata sulle porte 22 e 7051 a questo gruppo di sicurezza. Le connessioni TCP sulla porta 22 consentono l'accesso SSH all'istanza generata mentre le connessioni TCP sulla porta 7051 consentono l'accesso gRPC esterno all'istanza peer (necessario per gestire il peer utilizzando le SDK Fabric e la CLI degli strumenti Fabric). Queste impostazioni VPC ti verranno richieste quando avvii il Quick Start.

 Quando distribuisci un peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS in un VPC esistente, i seguenti parametri sostituiscono i parametri nelle sezioni corrispondenti [in alto](#remote-peer-aws-parameters-newvpc):

|  Parametro    | Descrizione | Valore predefinito |
| --------------|-------------|---------|
| **Configurazione di rete** | | |
| `VPCID` |	L'ID del tuo VPC esistente per la distribuzione. | |
| `AvailabilityZone1` | La zona di disponibilità in cui distribuire il primo nodo peer. | |
| `SubnetID1` |	L'ID della sottorete da utilizzare per il primo nodo peer. La sottorete deve appartenere al VPC scelto. Per trovare le sottoreti appartenenti al VPC, controlla il tuo dashboard VPC AWS e seleziona il menu "subnets".| |
| `AvailabilityZone2` | La zona di disponibilità in cui distribuire il secondo nodo peer. | |
| `SubnetID2` |	L'ID della sottorete da utilizzare per il secondo nodo peer. La sottorete deve appartenere al VPC scelto. Per trovare le sottoreti appartenenti al VPC, controlla il tuo dashboard VPC AWS e seleziona il menu "subnets".| |
| | | |
| **Configurazione EC2 Amazon**| | |
| `InstanceType` 	| Il tipo di istanza EC2 per le istanze peer. | m4.xlarge |
| `KeyPairName` |	Il nome della coppia di chiavi EC2 esistente all'interno della regione AWS. Devi eseguirne la generazione. | |
| `SecurityGroup` | L'ID del gruppo di sicurezza EC2 esistente all'interno della regione AWS. Devi consentire le connessioni TCP in entrata sulle porte 22 e 7051. |	| |

## Passo cinque: Esegui un test della distribuzione
{: #remote-peer-aws-test}

Dopo che il template AWS CloudFormation ha creato correttamente lo stack, nel tuo account AWS saranno in esecuzione due istanze del peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS. Le istanze verranno denominate in base alla combinazione di `Organization MSP` e `Peer enroll id` specificati nel template Quick Start. Ad esempio, `org1-remotepeer1`.  

![Peer su istanze AWS EC2](../images/remote_peer_AWS_EC2_instances.png "Peer su istanze AWS EC2")  
*Figura 3. Peer remoto su istanze AWS EC2 *

Per verificare che il peer sia in esecuzione:

  * Esegui SSH nel nuovo VPC creato selezionando l'istanza nella console AWS (fai clic su **Services > EC2 > Instances**) e facendo quindi clic sul pulsante **Connect**. Attieniti alle istruzioni da AWS per l'immissione del comando `ssh`.  
  * Dalla riga di comando, esegui `docker ps` per visualizzare i contenitori in esecuzione. Ciascuna macchina virtuale include un contenitore peer e un ec-agent. Se hai selezionato CouchDB come tuo database libro mastro, ci sarà anche un contenitore CouchDB.

  ```
  CONTAINER ID        IMAGE                                STATUS              PORTS                          NAMES
  fb3c49fe52fe        amazon/amazon-ecs-agent:latest       Created                                            ecs-agent
  667780cf3cd3        ibmblockchain/fabric-peer:1.2.1      Up                  0.0.0.0:7051->7051/tcp         peer
  2aa143c81027        ibmblockchain/fabric-couchdb:0.4.6   Up                  4369/tcp, 5984/tcp, 9100/tcp   couchdb
  ```

  * Puoi creare una sessione shell all'interno del contenitore peer eseguendo `docker exec -it peer sh`.

Inoltre, per verificare che la connessione del peer alla tua rete {{site.data.keyword.blockchainfull_notm}} Platform stia funzionando, puoi eseguire un comando della CLI del peer dall'interno del contenitore peer. Esegui il comando della CLI `peer channel fetch` CLI per recuperare il blocco genesi dal canale.

1. Richiama le informazioni di configurazione dal tuo profilo di connessione (`Connection Profile`) disponibile nel pannello Panoramica del tuo Monitoraggio della rete. Fai clic su **Profilo connessione** e quindi su **Scarica**.

   - Trova l'URL ordinante ricercando gli **ordinanti**, che puoi trovare in `orderers > url`. Prendi nota dell'URL che inizia con il nome della rete. L'URL è simile al seguente esempio:

   ```
   ash-zbc07b.4.secure.blockchain.ibm.com:21239
   ```

   - Trova il nome della tua organizzazione ricercando le **organizzazioni**. Questa deve essere la stessa organizzazione utilizzata per registrare il tuo peer. Puoi trovare il nome della tua organizzazione insieme al suo `mspid` associato. Questo valore è anche disponibile nel pannello Panoramica del Monitoraggio della rete. Fai clic sul pulsante **Configurazione peer remota**. Il valore è visualizzato sotto `MSP organizzazione`. Prendi nota del valore dell'`mspid`.

   - Se non lo hai già fatto, crea una sessione shell all'interno del contenitore peer eseguendo `docker exec -it peer sh`.

   ```
   docker exec -it peer sh
   ```
   {:codeblock}

   - Copia il certificato TLS dell'ordinante dal profilo di connessione al peer. Vai alla sezione **orderers**. Copia il certificato che segue "pem:", iniziando con -----BEGIN CERTIFICATE----- e finendo con -----END CERTIFICATE-----. Non includere le virgolette. Esegui questo comando dalla riga di comando, sostituendo `<orderer cert>` con il contenuto che hai copiato dal file creds.json.

   ```
   echo -e "<orderer cert>" > /etc/hyperledger/<PEER_ENROLL_ID>/orderer_tlscacert.pem
   ```
   {:codeblock}

   Sostituisci `<PEER_ENROLL_ID>` con l'ID di iscrizione specificato nel template Quick Start e associato a questa istanza del peer.

2. La tua organizzazione deve essere aggiunta a un canale nella rete prima che tu possa recuperare il blocco genesi.

  - Puoi avviare un nuovo canale per il peer. Come iniziatore di canale, puoi includere automaticamente la tua organizzazione durante la [creazione del canale](create_channel.html#creating-a-channel).

  - Un altro membro della rete blockchain può anche aggiungere la tua organizzazione a un canale esistente usando un [aggiornamento canale](create_channel.html#updating-a-channel). 

  - Dopo che la tua organizzazione è stata aggiunta a un canale, devi aggiungere il certificato di firma del tuo peer al canale. Il peer carica il proprio certificato di firma durante l'installazione, pertanto dovrai solo sincronizzare il certificato al canale. Nella schermata "Canali" del Monitoraggio della rete, individua il canale a cui si è unita la tua organizzazione e seleziona **Sincronizza certificato** dall'elenco a discesa sotto l'intestazione **Azione**. Questa azione sincronizza i certificati su tutti i peer nel canale.

3. Esegui questi comandi per impostare le variabili di ambiente nel contenitore peer.

   ```
   export ORDERER_1=<ORDERER_URL>
   export CHANNEL=<CHANNEL_NAME>
   export ORGID=<ORGANIZATION_MSP_ID>
   export PEERADDR=<PEER_ADDR>
   ```
   {:codeblock}

   Sostituisci i campi con le tue proprie informazioni.
     - Sostituisci `<ORDERER_URL>` con il nome host e la porta dell'ordinante dal file `creds.json`.
     - Sostituisci `<CHANNEL_NAME>` con il nome del canale a cui si unirà il peer.
     - Sostituisci `<ORGANIZATION_MSP_ID>` con il nome dell'organizzazione dal file `creds.json`.
     - Sostituisci `<PEER_ADDR>` con `localhost:7051`

   Ad esempio:

   ```
   export ORDERER_1=ash-zbc07b.4.secure.blockchain.ibm.com:21239
   export CHANNEL=defaultchannel
   export ORGID=PeerOrg1
   export PEERADDR=localhost:7051
   ```

4. Esegui il seguente comando della CLI del peer per recuperare il blocco genesi del canale.

   **IMPORTANTE:** nel seguente comando, sostituisci ciascuna ricorrenza di `<PEER_ENROLL_ID>` con l'ID di iscrizione associato a questa istanza peer che era stato specificato nel template Quick Start. Questo valore può essere individuato eseguendo il comando `ls /etc/hyperledger/`. Saranno elencate due cartelle: `fabric` e la seconda è il tuo `<PEER_ENROLL_ID>`.

   ```
   CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/<PEER_ENROLL_ID>/tls/ca.crt CORE_PEER_TLS_ENABLED=true CORE_PEER_ADDRESS=${PEERADDR} CORE_PEER_LOCALMSPID=${ORGID} CORE_PEER_MSPCONFIGPATH=/etc/hyperledger/<PEER_ENROLL_ID>/msp/ GOPATH=/ peer channel fetch 0 -o ${ORDERER_1} -c ${CHANNEL} --cafile /etc/hyperledger/<PEER_ENROLL_ID>/orderer_tlscacert.pem --tls
   ```
   {:codeblock}


   **Nota:** quando esegui questi comandi CLI, è possibile che tu possa riscontrare la seguente avvertenza, che può essere tranquillamente ignorata.

   ```
   [msp] getPemMaterialFromDir -> WARN 001 Failed reading file
   /etc/hyperledger/<PEER_ENROLL_ID>/msp/intermediatecerts/<intermediate cert name>.pem: no pem content for file  /etc/hyperledger/<PEER_ENROLL_ID>/msp/intermediatecerts/<intermediate cert name>.pem
   ```

   Verifica che il comando abbia funzionato correttamente e che il blocco genesi sia stato aggiunto al tuo contenitore peer eseguendo questo comando:

   ```
   ls *.block
   ```
   {:codeblock}

   Sai che il blocco genesi viene aggiunto correttamente quando viene visualizzato qualcosa di simile al seguente esempio:

   ```
   defaultchannel_0.block
   ```

   Congratulazioni. Il tuo peer {{site.data.keyword.blockchainfull_notm}} Platform for AWS è correttamente connesso alla tua rete {{site.data.keyword.blockchainfull_notm}} Platform.

## Domande frequenti (FAQ)
{: #remote-peer-aws-faq}

* **D**. Ho riscontrato un errore CREATE_FAILED quando ho avviato Quick Start.
* **R**. Se AWS CloudFormation non riesce a creare lo stack, ti consigliamo di avviare nuovamente il template con Rollback on failure impostato su `No`. (Questa impostazione si trova in Advanced nella pagina Option della console AWS CloudFormation). Con questa impostazione, lo stato dello stack verrà conservato e l'istanza verrà lasciata in esecuzione, in modo da consentirti di risolvere il problema. (Consulta i file di log in `%ProgramFiles%\Amazon\EC2ConfigService` e `C:\cfn\log`.)

  - Quando imposti Rollback on failure su `No`, continuerai a sostenere dei costi AWS per questo stack. Assicurati di eliminare lo stack quando finisci di risolvere i problemi. Per ulteriori informazioni, vedi [Troubleshooting AWS CloudFormation ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/troubleshooting.html "Troubleshooting AWS CloudFormation") sul sito web di AWS.

* **D**. Ho riscontrato un errore di limitazione della dimensione quando ho distribuito i template AWS Cloudformation.
* **A**. ti consigliamo di avviare i template Quick Start dall'ubicazione che abbiamo fornito o da un altro bucket S3. Se distribuisci i template da una copia locale sul tuo computer o da un'ubicazione non S3, potresti riscontrare delle limitazioni della dimensione del template quando crei lo stack. Per ulteriori informazioni sui limiti AWS CloudFormation, vedi la [documentazione di AWS ![Icona link esterno](../images/external_link.svg "Icona link esterno")](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cloudformation-limits.html "Limiti di AWS CloudFormation").

## Operazioni successive
{: #whats-next-aws}

Dopo aver configurato il peer in AWS, devi completare diversi passi operativi prima di poter inoltrare transazioni alla rete blockchain e leggere il libro mastro distribuito da essa. Per ulteriori informazioni, vedi [Gestione di un peer in AWS](remote_peer_operate_aws.html#remote-peer-operate-aws).

## Elevata disponibilità (HA, High Availability)
{: #aws-high-availability}

Per impostazione predefinita, per supportare l'HA, il template Quick Start distribuisce due istanze del peer, in due zone di disponibilità differenti.
Per avvalerti di questo supporto HA, devi anche configurare le tue [applicazioni client per l'elevata disponibilità](../v10_application.html#ha-app).

## Considerazioni sulla sicurezza
{: #remote-peer-aws-security}

L'AWS Cloud fornisce una piattaforma scalabile e ad elevata affidabilità che aiuta i clienti a distribuire le applicazioni e i dati in modo rapido e protetto. Quando crei dei sistemi sull'infrastruttura AWS, le responsabilità della sicurezza sono condivise tra te e AWS. Questo modello condiviso può ridurre il carico operativo mentre AWS opera, gestisce e controlla i componenti dal livello di sistema operativo e virtualizzazione dell'host e fino alla sicurezza fisica delle strutture in cui operano i servizi. Tu, a tua volta, ti assumi la responsabilità e la gestione del sistema operativo guest (compresi gli aggiornamenti e le patch di sicurezza), di altre applicazioni associate e della configurazione del firewall del gruppo di sicurezza fornito da AWS. Per ulteriori informazioni sulla sicurezza su AWS, visita [Sicurezza nel cloud AWS ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://aws.amazon.com/security/ "Sicurezza nel cloud AWS").

### AWS IAM (Identity and Access Management)
{: #remote-peer-aws-iam}

Questa soluzione si avvale di un ruolo IAM con un accesso meno privilegiato. Non è necessario o consigliato archiviare chiavi SSH, chiavi segrete o chiavi di accesso sulle istanze di cui è stato eseguito il provisioning.

### Sicurezza del sistema operativo
{: #remote-peer-aws-ossecurity}

All'utente root sui nodi cluster è possibile accedere solo utilizzando la chiave SSH specificata durante il processo di distribuzione. AWS non archivia queste chiavi SSH; pertanto, se perdi la tua chiave SSH, puoi perdere l'accesso a queste istanze. Le patch del sistema operativo sono tua responsabilità e devono essere eseguite su base periodica.

### Gruppi di sicurezza
{: #remote-peer-aws-securitygroups}

Un gruppo di sicurezza funge da firewall che controlla il traffico per una o più istanze. Quando avvii un'istanza, associ uno o più gruppi di sicurezza con l'istanza. Aggiungi le regole a ciascun gruppo di sicurezza che consentono il traffico verso o dalle relative istanze associate. Puoi modificare le regole per un gruppo di sicurezza in qualsiasi momento. Le nuove regole vengono applicate automaticamente a tutte le istanze associate al gruppo di sicurezza. I gruppi di sicurezza creati e assegnati alle singole istanze come parte di questa soluzione sono limitati il più possibile, pur consentendo l'accesso alle diverse funzioni richieste dal peer. Consigliamo di esaminare i gruppi di sicurezza per limitare ulteriormente l'accesso come necessario una volta che il cluster è attivo e in esecuzione.

### Sicurezza dei peer
{: #aws-security}

I peer vengono distribuiti al di fuori di {{site.data.keyword.blockchainfull_notm}} Platform; pertanto, sei responsabile della gestione della sicurezza del peer. Ciò include importanti aree di sicurezza fornite dalle reti piano Enterprise, come la gestione delle chiavi e la crittografia dei dati. Esamina i seguenti argomenti quando prendi in considerazione la sicurezza per i tuoi peer.

#### Sicurezza dei dati
{: #aws-security-data}

Il piano Enterprise di {{site.data.keyword.blockchainfull_notm}} Platform utilizza la crittografia dell'intero disco, che è basata sulla[crittografia a chiave simmetrica![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.14/gtps7/s7symm.html "Crittografia simmetrica") per proteggere tutti i dati utilizzati dalle reti. Devi adottare misure simili nel tuo ambiente per proteggere i dati del tuo peer.

I dati nel tuo database dello stato, indipendentemente dal fatto che tu stia utilizzando levelDB o couchDB, non sono crittografati. Puoi utilizzare la crittografia a livello delle applicazioni per proteggere i dati inattivi nel tuo database dello stato.

#### Residenza dei dati
{: #aws-security-data-residency}

La residenza dei dati richiede che l'elaborazione e l'archiviazione di tutti i dati del libro mastro blockchain rimangano all'interno dei confini di un singolo paese.
Per ulteriori informazioni su come è possibile eseguire tale operazione, fai riferimento a questo [argomento](remote_peer.html#data-residency).

#### Gestione delle chiavi
{: #aws-security-key-management}

La gestione delle chiavi è un aspetto critico della sicurezza del peer. Se una chiave privata è compromessa o persa, attori ostili potrebbero essere in grado di accedere ai dati e alla funzionalità del tuo peer. Il piano Enterprise di {{site.data.keyword.blockchainfull_notm}} Platform utilizza gli [Hardware Security Module](../glossary.html#hsm) (HSM) per archiviare le chiavi private della tua rete. HSM è un dispositivo fisico che impedisce ad altre parti di accedere alla tua chiave privata.

Quando distribuisci un peer su AWS, sei responsabile della gestione delle tue chiavi private. Sebbene {{site.data.keyword.blockchainfull_notm}} Platform generi le tue chiavi private, tali chiavi non vengono archiviate su Platform. È essenziale assicurarsi di memorizzare le chiavi in un luogo sicuro in modo che non vengano compromesse. Puoi trovare la chiave privata del tuo peer nella cartella keystore dell'MSP del tuo peer, nella directory `/etc/hyperledger/<PEER_ENROLL_ID>/msp/keystore/` all'interno del tuo contenitore peer. Per ulteriori informazioni sui certificati all'interno del tuo peer, visita la sezione [Membership Services Provider](../certificates.html#msp) dell'argomento [Gestione dei certificati su {{site.data.keyword.blockchainfull_notm}} Platform](../certificates.html).

Puoi utilizzare Key Escrow per recuperare le chiavi private perse. Questo deve essere eseguito prima della perdita di qualsiasi chiave. Se non è possibile recuperare una chiave privata, hai bisogno di ottenere nuove chiavi private ottenendo un nuovo signCert dalla CA (Certificate Authority, Autorità di certificazione). Devi anche rimuovere e sostituire il tuo certificato di gestione da qualsiasi canale a cui ti sei unito.

<!---
In IBP when a private key is created, two sets of independent key material is generated, in custody of two different entities. Those two sets of key materials are then combined to create the private key.
--->

#### TLS
{: #aws-security-tls}

[Transport Layer Security ![Icona link esterno](../images/external_link.svg "Icona link esterno")](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm "Una panoramica dell'handshake SSL o TLS") (TLS) è integrato nel modello di attendibilità di Hyperledger Fabric. Tutti i componenti in {{site.data.keyword.blockchainfull_notm}} Platform utilizzano TLS per autenticarsi e comunicare tra loro. Pertanto, i componenti di rete su {{site.data.keyword.blockchainfull_notm}} Platform devono essere in grado di completare un handshake TLS con i tuoi peer. Ciò implica, tra le altre cose, che devi abilitare il pass-through, utilizzando ad esempio l'elenco elementi consentiti (whitelist), nel tuo firewall dalle applicazioni client al tuo peer.


#### Configurazione di Membership Service Provider
{: #aws-security-MSP}

I componenti di IBM Blockchain Platform utilizzano le identità tramite gli MSP (Membership Service Provider). Gli MSP associano i certificati emessi dalle CA ai ruoli di rete e di canale. Fai riferimento a questo [argomento](../certificates.html#msp) per ulteriori informazioni su come funzionano gli MSP con il peer.

#### Sicurezza delle applicazioni
{: #aws-security-appl}

Poiché tutti i richiami del chaincode sono firmati, Fabric gestisce la sicurezza delle applicazioni. Inoltre, Fabric include anche controlli a livello di applicazione basati su ACL.

## Licenza e determinazione dei prezzi
{: #license-pricing-aws}

Devi accettare una versione di licenza Community Edition di {{site.data.keyword.blockchainfull_notm}} Platform for AWS per poter utilizzare la soluzione di distribuzione abilitata da Quick Start. L'uso di {{site.data.keyword.blockchainfull_notm}} Platform for AWS (compresi tutti i pacchetti forniti dall'offerta Quick Start e i pacchetti da essi derivati) non è destinato a un uso di produzione. IBM potrebbe decidere di annullare l'autorizzazione di accesso al codice, e l'uso di questo codice, in qualsiasi momento.
L'accordo di licenza del software {{site.data.keyword.blockchainfull_notm}} Platform for AWS contiene ulteriori dettagli sui termini di licenza. Quando avvii Quick Start, ti viene chiesto di leggere e accettare i termini dell'accordo.
