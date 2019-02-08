---

copyright:
  years: 2017
lastupdated: "2017-12-06"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Creazione di canali gestiti 

**ATTENZIONE:** prima di utilizzare un'offerta {{site.data.keyword.blockchainfull}}, leggi le informazioni tecniche e di supporto nella sezione [Dichiarazione di non responsabilità](/docs/services/blockchain/needtoknow.html).   
{:shortdesc}

In alcuni casi d'uso, ad esempio dei mercati di cambio di valute estere altamente regolamentati, potrebbe essere necessario affidare a una terza parte attendibile i ruoli amministrativi sui canali che normalmente sarebbero gestiti da diversi operatori o membri. 

Il processo per fornire una rete di questo tipo è simile a quello della creazione di una qualsiasi rete. La differenza principale riguarda le assegnazioni di autorizzazioni ai membri relative al modo in cui interagiscono con il libro mastro nei canali.  

La procedura per creare una rete e invitare membri è disponibile qui: [Governance della rete](/docs/services/blockchain/get_start.html#creating-a-network). 

**Nota**: in un esempio di utilizzo reale, l'operatore di una rete di questo tipo potrebbe installare del chaincode personalizzato durante la fase "Create Network" utilizzando l'editor delle politiche ma, al fine di questo esempio, presumeremo che la tua configurazione di rete sia quella standard. 

## Crea il canale

Una volta che hai una rete e che i membri che hai invitato sono passati per il processo di accoglienza, vai a **Channels** nella barra degli strumenti. 

Fai clic su **New Channel**. Questo porterà a una schermata dove assegnerai un nome e una descrizione al canale (la descrizione è facoltativa). 

Dopo aver fatto clic su **Next**, ti troverai sulla schermata dove invitare i membri al canale e gestire le loro autorizzazioni. Al fine di questo esempio, poniamo che stai fungendo da terza parte attendibile in un cambio di valuta estera tra due banche. Tu, ossia la terza parte attendibile, indichi te stesso come solo operatore (Operator) per il canale e assegni le altre due banche come scrittori (Writer). Questo ti darà l'autorizzazione esclusiva a modificare il canale (istanziando un chaincode su di esso, ad esempio; ne riparleremo più avanti in maggiore dettaglio) concedendo al tempo stesso alle due banche la possibilità di richiamare delle transazioni. La schermata che vedi dovrebbe essere simile al seguente: 

  ![Seleziona ruoli dei membri](images/selectmemberroles.png "Seleziona ruoli dei membri") 
*Dove “JoeCo” sei tu, ossia la terza parte attendibile, e “IBM” e “Chris” sono le due banche (questo ovviamente è solo un esempio).* 

Dopo che hai assegnato le autorizzazioni corrette, fai clic su **Next**. 

Questo ti porterà a una schermata di aggiornamento delle politiche di canale. Poiché c'è un solo operatore su questo canale (tu), seleziona il numero di membri necessari per creare il canale come "1". Fai clic quindi su **Submit Request**. 

Nota: anche se sei il solo operatore di questo canale, questa continua a essere solo una richiesta; devi comunque andare alla schermata corretta per approvare e inviare la richiesta per creare il canale.  

Ai membri che hai invitato vengono inviate delle email che chiedono loro di partecipare al canale. Tuttavia, hanno solo i privilegi di "scrittura" e tu sei responsabile di completare il processo di creazione del canale. Passa alla schermata "Notifications" nel Network Monitor, dove è stato visualizzato un avviso di creazione del canale. Individua la richiesta da gestire nelle schede secondarie "All" o "Pending". Fai clic su **Review Request** per visualizzare i dettagli del canale e fai clic su **Approve**. Fai clic quindi su **Submit Request**. 

Congratulazioni. Hai appena creato un canale gestito. 

In una rete con 15 membri (14 banche e tu, ossia la terza parte attendibile), potresti potenzialmente vedere dozzine di questi canali. Le banche A e B su un canale. Le banche B e F su un canale. Le banche potrebbero scegliere di avere dei canali separati per ogni tipo di valuta che trattano. Tuttavia, in ogni caso, la terza parte attendibile crea il canale, approva le transazioni e funge da unico operatore. 

# Istanziazione del chaincode

Il tuo canale è stato creato correttamente ma ha ancora bisogno che a esso sia collegato un chaincode. In un canale dedicato agli scambi di valuta estera, questo chaincode richiederà presumibilmente che tutti e tre i membri del canale (le due banche in questione e tu, ossia la terza parte attendibile) approvino **ogni** transazione. Questo chaincode potrebbe anche essere scritto per inviare un record del *risultato* di questa transazione a un canale "di sola lettura" gestito composto da ogni membro nella rete (ne riparleremo in maggiore dettaglio più avanti). Tuttavia, prima di potere essere istanziato, il chaincode deve essere installato sui peer dei membri. Dopo essere stato installato, può essere istanziato dall'operatore del canale (tu).  

L'effettiva procedura per installare e istanziare questo tipo di chaincode non è diversa da quella per qualsiasi chaincode (a parte il numero di operatori necessari per approvare l'istanziazione); attieniti pertanto alle istruzioni che trovi qui: [Installazione e istanziazione di un chaincode](/docs/services/blockchain/install_instantiate_chaincode.html.html).

# Un canale gestito di sola lettura

I regolamenti finanziari nel mercato di scambio di valute estere, per continuare con questo caso d'uso, potrebbero richiedere che ci sia anche un canale nella rete su cui è presente ogni banca in una modalità passiva "di sola lettura", consentendo di tracciare il **risultato** di queste transazioni di valuta. Questo serve a garantire che, ad esempio, la Banca A che ha appena perso milioni di dollari per una "scommessa" con la Banca B (sulla misura in cui il valore di una valuta sarebbe salito o sceso in un periodo di tempo) non manchi di approvare la transazione che invierà questi milioni di dollari alla Banca B. 

Per creare questo tipo di canale, attieniti alla stessa procedura di creazione di un canale a cui ti sei attenuto prima solo che, invece di invitare due banche e renderle scrittori (Writer) (con te stesso come unico operatore), inviti **ogni** banca e li rendi tutti lettori (Reader). Attieniti quindi allo stesso processo per installare e istanziare il chaincode. 

## {{site.data.keyword.IBM_notm}} Support 

{{site.data.keyword.IBM_notm}} offre un supporto sulle soluzioni {{site.data.keyword.blockchain}} implementate da {{site.data.keyword.IBM_notm}}. Accedi ai dettagli del supporto {{site.data.keyword.blockchainfull_notm}} tramite il [{{site.data.keyword.blockchainfull_notm}} DockerHub ![Icona link esterno](images/external_link.svg "Icona link esterno")](https://hub.docker.com/u/ibmblockchain/) ed esplora gli impegni di supporto disponibili.

Per dei dettagli completi su tutte le funzioni e la funzionalità di Hyperledger Fabric v1.0, consulta [la documentazione di Hyperledger Fabric ![Icona link esterno](images/external_link.svg "Icona link esterno")](http://hyperledger-fabric.readthedocs.io/en/latest/).
