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

# Prezzi

Questo manuale ti aiuta a comprendere i prezzi per i piani di adesione {{site.data.keyword.blockchainfull}} Platform e quanto pagherai man mano che sviluppi e accresci la tua rete blockchain.  
{:shortdesc}

{{site.data.keyword.blockchainfull}} Platform addebita mensilmente i costi per adesioni e peer alle organizzazioni che creano delle reti blockchain. I costi sono diversi, a seconda del piano di adesione da te scelto e delle risorse di rete utilizzate dalla tua rete. La seguente tabella mostra una panoramica dei prezzi di {{site.data.keyword.blockchainfull_notm}} Platform.

| Elementi dei prezzi | Costo del piano Starter al mese | Costo del piano Enterprise al mese |
|-----|-----|-----|
| Costo adesione | $250 | $1000 |
| Costo peer | $125 | $1000 |

*Figura 1. Panoramica dei prezzi di {{site.data.keyword.blockchainfull}} Platform*

Il costo mensile viene addebitato ogni giorno su base proporzionale. Ad esempio, un membro (costo di adesione associato di $1.000) di due peer (costo per ogni peer di $1.000 X 2 peer) deve pagare $3.000 ogni mese. Se il mese ha 30 giorni, il membro paga $ 100 ($3.000/30) ogni giorno. Per ulteriori informazioni su come pagare per le tue reti, consulta [Modalità di pagamento](paying_mode.html).

Il piano Starter di **{{site.data.keyword.blockchainfull_notm}} Platform ti offre $500 di crediti di prova** prima che ti vengano fatti degli addebiti. Per ulteriori informazioni, consulta [Prezzi del piano Starter](#starter-plan-pricing).
  

## Componenti di base della rete

Per comprendere i prezzi, dobbiamo iniziare con un'introduzione ai componenti di base di una rete. {{site.data.keyword.blockchainfull_notm}} Platform consente la creazione di una rete blockchain che si basa su Hyperledger Fabric. Ad un alto livello, una rete blockchain Fabric è costituita dai seguenti componenti di base:

-	**Organizzazioni** – Qualsiasi entità che deve mantenere una copia del libro mastro blockchain e deve convalidare le transazioni. Ci possono essere più organizzazioni blockchain per una singola società.
-	**Peer** – Il nodo associato a un'organizzazione che contiene il libro mastro ed esegue la convalida delle transazioni. I peer sono associati a una singola organizzazione blockchain.
-	**Servizio ordini** – Composto da un singolo ordinante (SOLO) o una raccolta di ordinanti, il servizio ordini mette in sequenza le transazioni, crea i blocchi e li invia ai peer per la convalida.
-	**CA (Certificate Authority, Autorità di certificazione)** – Emette certificati digitali per scopi di identificazione a qualsiasi componente di rete interattivo.

{{site.data.keyword.blockchainfull_notm}} Platform offre due piani di adesione, il **piano Starter** e il **piano Enterprise**, che puoi scegliere in {{site.data.keyword.cloud_notm}}. Entrambi i piani ti consentono di creare organizzazioni e ti forniscono una CA (Certificate Authority, Autorità di certificazione). I piani differiscono per quanto riguarda peer, CA e servizio ordini.

Il piano Enterprise ti fornisce CA e peer altamente disponibili con un servizio ordini con tolleranza agli errori di arresto anomalo. Il piano Starter non ha opzioni di alta disponibilità e utilizza un servizio ordini di base di tipo SOLO. Per tale motivo, il piano Starter è progettato per essere il punto d'ingresso a {{site.data.keyword.blockchainfull_notm}} Platform per ambienti di sviluppo, test e PoC (proof-of-concept). Il piano Enterprise è l'opzione per le reti pronte per gli ambienti pilota e di produzione.

## Elementi chiave dei prezzi

Entrambi i piani Starter ed Enterprise hanno due elementi dei prezzi:

- **Costo di adesione** – Copre la creazione dell'organizzazione e l'accesso a servizio ordini e CA e viene addebitato in base alle **singole istanze**. Incluso in questo elemento di prezzi, {{site.data.keyword.blockchainfull_notm}} Platform gestisce il servizio ordini e la CA per conto della tua rete. Questo costo è necessario per avere accesso a una rete creata su {{site.data.keyword.blockchainfull_notm}} Platform.

  -	Il piano Starter ti consente un numero *illimitato* di organizzazioni per ogni singola adesione e la capacità di passare da un'organizzazione all'altra nel monitoraggio di rete. Poiché il piano Starter è progettato per gli ambenti di sviluppo, test e PoC, puoi eseguire una simulazione negli ambienti con più organizzazioni. **Nota** che l'archiviazione di rete totale è limitata a 20GB, compresi componenti, chaincode e dati del libro mastro. Le tue organizzazioni simulate condividono l'archiviazione di 20GB nella rete blockchain.

  -	Enterprise consente una singola organizzazione per ogni singola adesione. Poiché Enterprise è progettato per gli ambienti pilota e di produzione, sei collegato alla tua organizzazione specifica.

-	**Costo peer** – Copre i peer di un'organizzazione e viene addebitato in base ai **singoli peer**. Questo costo è obbligatorio solo se desideri avere un peer per mantenere una copia del libro mastro e convalidare le transazioni.

### Esempio di costo di adesione

La Figura 2 mostra un esempio di istanze di rete che può aiutare a comprendere il costo di adesione. Nella figura, lo specifico account {{site.data.keyword.cloud_notm}} esegue il provisioning di tre istanze di rete: una istanza di piano Enterprise con il nome *Blockchain-11* e due istanze di piano Starter con i nomi *Blockchain-cz* e *Blockchain-da*. Ogni istanza richiede un servizio ordini e una CA propri. In questo caso, questo specifico account {{site.data.keyword.cloud_notm}} deve pagare tre costi di adesione, uno per ciascuna istanza di rete.

![Istanze di rete blockchain](../images/ibp_instance_example.png "Istanze di rete blockchain")  
*Figura 2. Istanze di rete blockchain*


## Prezzi del piano Starter

Il provisioning delle reti di piano Starter viene eseguito con una configurazione predefinita di due organizzazioni e un peer per ogni singola organizzazione, per un totale di $500 al mese. Con la configurazione predefinita, non c'è un bisogno immediato di modificare l'ambiente per iniziare a lavorare. Puoi aggiungere ulteriori organizzazioni gratuitamente e puoi aggiungere ulteriori peer alle tue organizzazioni a un costo di $125 al mese per ogni singolo peer. La tua fattura è riflessa nella Figura 3 se crei, o aderisci a, una rete di piano Starter con la configurazione predefinita.

| Componenti dei prezzi | Costo al mese |
|-----|----------------|
| Costo adesione | $250 |
| Costo peer | $125 |
| Costo peer | $125 |
| Addebito a fine mese | $500 |

*Figura 3. Addebito di una rete di piano Starter predefinita*

### Prezzi del piano Starter di esempio

#### Prova del piano Starter
Lo scopo del piano Starter è consentire a chiunque di iniziare a lavorare e di vedere il valore aggiunto di {{site.data.keyword.blockchainfull_notm}} Platform. Pertanto, {{site.data.keyword.blockchainfull_notm}} Platform offre un sistema di credito di $500 destinato a coprire tutti gli addebiti per i nuovi utenti che maturerebbero nel corso di un mese con la configurazione di rete predefinita precedentemente descritta. La tua fattura è riflessa nella Figura 4 se crei, o aderisci a, una rete di piano Starter per la **prima volta** con la configurazione predefinita.

| Componenti dei prezzi | Costo al mese |
| ----- | ---------------- |
| Spesa di adesione rete 1 | $250 |
| Costo peer | $125 |
| Costo peer | $125 |
| Totale | $500 |
| Crediti | -$500 |
| Addebito alla fine del primo mese | $0 |

*Figura 4. Addebito di una rete di piano Starter predefinita con i crediti di prova applicati*

#### Peer aggiuntivi
Il piano Starter non limita il numero di peer che puoi aggiungere alla tua rete. Se aggiungi, ad esempio, due peer alla rete di piano Starter predefinita, uno per ciascuna delle tue organizzazioni, aumenti la tua fattura di $250 al mese. La tua fattura è riflessa nella Figura 5 se aggiungi due peer aggiuntivi all'inizio del primo mese quando crei, o aderisci a, una rete di piano Starter e consumi i crediti di prova.

| Componenti dei prezzi | Costo al mese |
|-----|----------------|
| Spesa di adesione rete 1 | $250 |
| Costo peer | $125 |
| Costo peer | $125 |
| Costo peer | $125 |
| Costo peer | $125 |
| Totale | $750 |
| Crediti | -$500 |
| Addebito alla fine del primo mese | $250 |

*Figura 5. Addebito di una rete di piano Starter predefinita con due peer aggiuntivi*

#### Reti aggiuntive
Anche il piano Starter non limita il numero di istanze di rete di cui puoi eseguire il provisioning. Se esegui il provisioning di una seconda istanza di rete del piano Starter, devi pagare una seconda spesa di adesione e una seconda serie di spese peer associate alla seconda rete. Aumenti, pertanto, la tua fattura di $500 al mese per utilizzare la configurazione di rete predefinita. Ti potresti trovare nello scenario in cui hai bisogno di reti Starter aggiuntive per i tuoi ambienti di sviluppo, test e PoC (proof-of-concept) isolati l'uno dall'altro. Un esempio è ad esempio quando desideri consentire ai tuoi ingegneri responsabili della qualità un ambiente di test per eseguire una significativa attività di test funzionale lontana dal tuo ambiente di sviluppo.

La tua fattura è riflessa nella Figura 6 se aggiungi una rete aggiuntiva all'inizio del primo mese quando crei, o aderisci a, una rete di piano Starter e consumi i crediti di prova.

| Componenti dei prezzi | Costo al mese |
|-----|----------------|
| Spesa di adesione rete 1 | $250 |
| Costo peer | $125 |
| Costo peer | $125 |
| Spesa di adesione rete 2 | $250 |
| Costo peer | $125 |
| Costo peer | $125 |
| Totale | $1000 |
| Crediti | -$500 |
| Addebito alla fine del primo mese | $500 |

*Figura 6. Addebito di due reti di piano Starter predefinite*

#### Rimozione di peer
Puoi anche rimuovere un peer dalla configurazione di rete piano Starter predefinita. In questa situazione {{site.data.keyword.blockchainfull_notm}} Platform ti offre ancora i $500 di crediti di prova Cloud ma la tua fattura verrà ridotta di $125 al mese. Il risultato consiste in $125 di crediti peer residui alla fine del mese. Ti potresti ritrovare in questo scenario quando stai collaborando con un altro membro nel tuo ambiente di piano Starter, in cui a ognuno di voi serve solo un singolo peer. La tua fattura è riflessa nella Figura 7 se rimuovi un peer dalla tua rete piano Starter e consumi i crediti di prova.

| Componenti dei prezzi | Costo al mese |
| ----- | ---------------- |
| Spesa di adesione rete 1 | $250 |
| Costo peer | $125 |
| Costo peer | N/D |
| Totale | $375 |
| Crediti | -$500 |
| Addebito alla fine del primo mese | $0 |
| Crediti adesione residui | $0 |
| Crediti peer residui | $125 |

*Figura 7. Addebito di una rete di piano Starter predefinita con crediti residui*


## Prezzi del piano Enterprise

{{site.data.keyword.blockchainfull_notm}} Platform non fornisce una configurazione predefinita per una rete di piano Enterprise. Puoi scegliere la configurazione con la quale ti piacerebbe iniziare. Quando sei pronto a usare il piano Enterprise, dovresti avere una buona comprensione di cosa dovrebbe fare la tua configurazione di rete. Come procedura ottimale di elevata disponibilità, consigliamo vivamente un minimo di due peer per ogni singola organizzazione per garantire che per la tua organizzazione non si verifichi un'interruzione di rete.

Se ti trovi in una rete di piano Enterprise con l'altro membro della rete, e ognuno di voi aggiunge due peer per la propria organizzazione, la fattura di ognuno di voi è riflessa nella Figura 8. 

| Componenti dei prezzi | Costo al mese |
|-----|----------------|
| Costo adesione | $1000 |
| Costo peer | $1000 |
| Costo peer | $1000 |
| Addebito a fine mese | $3000 |

*Figura 8. Addebito di una rete di piano Enterprise di base*

### Prezzi del piano Enterprise di esempio

#### Peer aggiuntivi
Continua con l'esempio di cui sopra, se aggiungi altri due peer alla tua organizzazione. La tua fattura aumenta di $2000 al mese. La tua fattura è riflessa nella Figura 9:

| Componenti dei prezzi | Costo al mese |
|-----|----------------|
| Costo adesione | $1000 |
| Costo peer | $1000 |
| Costo peer | $1000 |
| Costo peer | $1000 |
| Costo peer | $1000 |
| Addebito alla fine del primo mese | $5000 |

*Figura 9. Addebito di una rete di piano Enterprise con quattro peer*

Se l'altro membro mantiene la stessa configurazione di rete, la fattura del membro è uguale a prima (che è riflessa nella Figura 8).

#### Reti aggiuntive
Anche il piano Enterprise non limita il numero di istanze di rete di cui puoi eseguire il provisioning o a cui puoi aderire. Se crei una seconda rete di piano Enterprise con la stessa configurazione di rete di base, ossia una singola organizzazione con due peer, la tua fattura è riflessa nella Figura 10. Ti potresti ritrovare in questo scenario quando hai creato più reti, hai aderito a più reti o ricorre una combinazione di queste due condizioni.

| Componenti dei prezzi | Costo al mese |
|-----|----------------|
| Spesa di adesione rete 1 | $1000 |
| Costo peer | $1000 |
| Costo peer | $1000 |
| Spesa di adesione rete 2 | $1000 |
| Costo peer | $1000 |
| Costo peer | $1000 |
| Totale | $6000 |

*Figura 10: Addebito di due reti di piano Enterprise che hanno entrambe la configurazione di rete di base*

Se l'altro membro utilizza solo una rete di piano Enterprise e mantiene la stessa configurazione di rete, la fattura del membro è uguale a prima (che è riflessa nella Figura 8).

