---
title: Modifiche di StoreKit in iOS 6
description: "iOS 6 vengono introdotte due modifiche all'API di Kit Store: la possibilità di visualizzare iTunes (e Store di App/Erotico) opzione in cui Apple ospiterà i file scaricabili di acquisto di prodotti all'interno di una nuova in-app e l'app. Questo documento illustra come implementare tali funzionalità con xamarin. IOS."
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 5d1bb5ab636cd7527a560332a9890e9907fac454
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118318"
---
# <a name="changes-to-storekit-in-ios-6"></a>Modifiche di StoreKit in iOS 6

_iOS 6 sono state introdotte due modifiche all'API di Kit Store: la possibilità di visualizzare iTunes (e Store di App/Erotico) opzione in cui Apple ospiterà i file scaricabili di acquisto di prodotti all'interno di una nuova in-app e l'app. Questo documento illustra come implementare tali funzionalità con xamarin. IOS._

Le principali modifiche apportate al Kit Store iOS6 sono questi due nuove funzionalità:

- **Visualizzazione di contenuto in-App & Purchasing** : gli utenti possono acquistare e scaricare le app, musica, documentazione e altri iTunes di contenuto senza uscire dall'app. È anche possibile collegare alle proprie App alzare di livello di acquisto o semplicemente incoraggiare recensioni e classificazioni.
- **Contenuto ospitato di acquisto in-App** : verrà archiviata e fornirà il contenuto associato con i prodotti di acquisto in-app, che elimina la necessità di un server separato ospitare i file, automaticamente supporta il download in background e ti permette di Apple scrivere meno codice.

Vedere le [acquisto In-App](~/ios/platform/in-app-purchasing/index.md) Guide per una descrizione dettagliata di StoreKit APIs.

## <a name="requirements"></a>Requisiti

Le funzionalità di Kit Store illustrate in questo documento richiedono iOS 6 e Xcode 4.5, insieme a xamarin. IOS 6.0.

## <a name="in-app-content-display--purchasing"></a>Visualizzazione del contenuto in-App e acquisti

La nuova funzionalità di acquisto in-app in iOS consente agli utenti di visualizzare le informazioni sul prodotto e acquistare o scaricare il prodotto all'interno dell'app.
In precedenza applicazioni dovranno attivare Erotico, il che comporterebbe l'utente di uscire dall'applicazione originale, l'App Store o iTunes. Questa nuova funzionalità restituisce automaticamente l'utente all'App quando vengono eseguite.

[![](changes-to-storekit-images/image1.png "La restituzione a un'app automaticamente dopo l'acquisto")](changes-to-storekit-images/image1.png#lightbox)

Esempi di come potrebbe essere utilizzata:

- **Incoraggiando gli utenti di classificare l'app** – è possibile aprire la pagina App Store in modo che l'utente può valutare ed esaminare l'app senza lasciandolo.
- **Cross-innalzamento di livello app** : consentono all'utente di vedere le altre App che si pubblica, con la possibilità di acquistare e scaricare immediatamente.
- **Consentire agli utenti di trovare e scaricare il contenuto** : consentire agli utenti di acquistare il contenuto che l'app consente di trovare, gestisce o aggrega (ad es. un'app correlata alla musica potrebbe fornire un elenco di riproduzione dei brani e consentire ogni canzone a essere acquistati all'interno dell'app).

Una volta il `SKStoreProductViewController` è stato visualizzato l'utente può interagire con le informazioni sul prodotto, come se fossero in iTunes, l'App Store o di Erotico. L'utente può:

- Screenshot di visualizzazione (per le App),
- Canzoni di esempio o un video (per la musica, programmi TV e filmati),
- Verifiche (lettura e scrittura),
- Acquistare e scaricare, che avviene interamente all'interno di controller di visualizzazione e Kit Store.

Alcune opzioni all'interno di `SKStoreProductViewController` verranno comunque forza l'utente per lasciare l'app e aprire l'app di store pertinente, ad esempio facendo clic su **Related Products** o un'app **supporto** collegamento.

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

L'API per mostrare un prodotto all'interno di qualsiasi app è semplice: è necessario solo creare e visualizzare un `SKStoreProductViewController`. Seguire questi passaggi per creare e mostrare un prodotto:

1. Creare un `StoreProductParameters` oggetto passare i parametri per il controller di visualizzazione, inclusi il `productId` nel costruttore.
1. Creare un'istanza di `SKProductViewController`. Assegnarla a un campo a livello di classe.
1. Assegnare un gestore per il controller di visualizzazione `Finished` evento, che debba ignorare il controller di visualizzazione. Questo evento viene chiamato quando l'utente preme Annulla; o in caso contrario, completa una transazione all'interno di controller di visualizzazione.
1. Chiamare il `LoadProduct` metodo passando la `StoreProductParameters` e un gestore di completamento. Il gestore di completamento deve verificare che la richiesta per il prodotto è stato correttamente e in questo caso, presentare il `SKProductViewController` come form modale. Gestione appropriata degli errori devono essere aggiunti nel caso in cui il prodotto non può essere recuperato.

### <a name="example"></a>Esempio

Il *ProductView* del progetto nel *StoreKit* codice di esempio per questo articolo implementa un `Buy` metodo che accetta qualsiasi prodotto dell'ID Apple e visualizza il `SKStoreProductViewController`. Il codice seguente consente di visualizzare le informazioni sul prodotto per qualsiasi ID Apple specificato:

```csharp
void Buy (int productId)
{
    var spp = new StoreProductParameters(productId);
    var productViewController = new SKStoreProductViewController ();
    // must set the Finished handler before displaying the view controller
    productViewController.Finished += (sender, err) => {
        // Apple's docs says to use this method to close the view controller
        this.DismissModalViewControllerAnimated (true);
    };
    productViewController.LoadProduct (spp, (ok, err) => { // ASYNC !!!
        if (ok) {
            PresentModalViewController (productViewController, true);
        } else {
            Console.WriteLine (" failed ");
            if (err != null)
                Console.WriteLine (" with error " + err);
        }
    });
}
```

L'app sembra simile allo screenshot seguente quando si esegue – download o l'acquisto viene generato interamente all'interno di `SKStoreProductViewController`:

[![](changes-to-storekit-images/image2.png "L'app è simile durante l'esecuzione")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>Supporto di sistemi operativi meno recenti

L'applicazione di esempio include codice che mostra come aprire l'App Store, iTunes o l'Erotico nelle versioni precedenti di iOS. Usare la `OpenUrl` metodo per aprire un correttamente predisposto **itunes.com** URL.

È possibile implementare un controllo della versione per determinare il codice da eseguire, come illustrato di seguito:

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (6,0)) {
    // do iOS6+ stuff, using SKStoreProductViewController as shown above
} else {
    // don't do stuff requiring iOS 6.0, use the old syntax 
    // (which will take the user out of your app)
    var nsurl = new NSUrl("http://itunes.apple.com/us/app/angry-birds/id343200656?mt=8");
    UIApplication.SharedApplication.OpenUrl (nsurl);
}
```

### <a name="errors"></a>Errori

Il seguente errore si verificherà se l'ID Apple utilizzato non è valido, che può generare confusione perché implica un problema di rete o l'autenticazione di qualche tipo.

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>Leggere la documentazione di Objective-C

Agli sviluppatori che leggono su Kit Store nel portale per sviluppatori di Apple verranno visualizzato un protocollo – [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) : illustrati in relazione a questa nuova funzionalità. Il protocollo di delegato ha un solo metodo – productViewControllerDidFinish: quale è stato esposto come il `Finished` evento sul `SKStoreProductViewController` in xamarin. IOS.

## <a name="determining-apple-ids"></a>Determinare l'ID Apple

L'ID Apple, necessari per il `SKStoreProductViewController` è un *numero* (non deve essere confusa con gli ID Bundle, ad esempio "com.xamarin.mwc2012"). Esistono alcuni modi diversi, che è possibile trovare l'ID Apple per i prodotti che si desiderano visualizzare, elencati di seguito:

### <a name="itunesconnect"></a>iTunesConnect

Per le applicazioni pubblicate, è facile trovare i **ID Apple** in iTunes Connect:

[![](changes-to-storekit-images/image3.png "Trovare l'ID Apple in iTunes Connect")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />

### <a name="search-api"></a>API di ricerca

Apple offre un'API di ricerca dinamica per eseguire query su tutti i prodotti nell'App Store iTunes e l'Erotico. Informazioni su come accedere l'API di ricerca disponibili nei [risorse di applicazioni Affiliate di Apple](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html), anche se l'API viene esposta a tutti gli utenti (non appena registrate consociate). Il file JSON risultante può essere analizzato per individuare il `trackId` che rappresenta l'ID Apple da usare con `SKStoreProductViewController`.

I risultati includeranno anche altri metadati, inclusi URL disegno che può essere utilizzato per eseguire il rendering del prodotto nell'app e le informazioni di visualizzazione.

Ecco alcuni esempi:

- **app iBooks** – [ http://itunes.apple.com/search?term=ibooks&amp; entità = software&amp;country = us](http://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us)
- **Punto e il iBook che salta** – [ http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp; entità = e-book&amp;country = us](http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us)

### <a name="enterprise-partner-feed"></a>Feed di Partner dell'organizzazione

Apple offre ai partner approvati con un dump completo dei dati di tutti i loro prodotti, sotto forma di file flat pronte database scaricabili. Se si è idonei per l'accesso per il [Feed Partner Enterprise](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-enterprise-partner-feed.html), quindi l'ID Apple per tutti i prodotti sono disponibili in tale set di dati.

Numero di utenti del Feed di Partner di Enterprise è membri del [programma Affiliate](http://www.apple.com/itunes/affiliates) che consente di commissioni all'atro sulle vendite prodotto. `SKStoreProductViewController` non supporta gli ID di applicazioni Affiliate (al momento della scrittura).

### <a name="direct-product-links"></a>Prodotto collegamenti diretti

L'ID Apple per un prodotto può essere dedotto dal relativo collegamento all'URL di anteprima di iTunes.
In qualsiasi iTunes collegamenti ai prodotti (per le app, musica o libri) individuare la parte dell'URL inizia con `id` e usare il numero che segue.

Ad esempio, il collegamento diretto al iBooks è

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

ed è l'ID Apple **364709193**. In modo analogo per l'app MWC2012, è il collegamento diretto

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

ed è l'ID Apple **496963922**.

## <a name="in-app-purchase-hosted-content"></a>Il contenuto ospitato acquisto in-App

Se gli acquisti in-app è costituito da contenuto scaricabile (ad esempio libri o altri supporti, art a livello di gioco e configurazione o altri file di grandi dimensioni) quindi tali file utilizzati per essere ospitati nel server web e App incorporare il codice per scaricarli in modo sicuro dopo acquistare. A partire da iOS 6, Apple ospiterà i file nei server, eliminando la necessità di un server separato. La funzionalità è disponibile solo per i prodotti Non riproducibile (non può essere utilizzato o sottoscrizioni). Vantaggi dell'uso di servizio di hosting di Apple:

- Riduci i costi di hosting e della larghezza di banda.
- Probabilmente più scalabile rispetto a qualsiasi host del server attualmente in uso. 
- Meno codice da scrivere, poiché non è necessario creare alcuna elaborazione sul lato server. 
- Il download in background viene implementato automaticamente.

Nota: il test ospitato contenuto di acquisto in-app in iOS che Simulator non è supportato, pertanto è necessario testare con un dispositivo reale.

### <a name="hosted-content-basics"></a>Nozioni di base del contenuto ospitati

Prima di iOS 6, si sono verificati due modi per fornire un prodotto (descritto più dettagliatamente [acquisto In-App di Xamarin](~/ios/platform/in-app-purchasing/index.md) documentazione):

- **Prodotti incorporati** : funzionalità che sono 'sbloccati' tramite l'acquisto, ma che vengono compilati nell'applicazione (ovvero come codice o le risorse incorporate). Sono esempi di prodotti predefiniti sbloccato foto viene applicato il filtro o nel gioco power-up.
- **I prodotti server-recapitati** – dopo l'acquisto, l'applicazione deve scaricare il contenuto da un server in cui si opera. Questo contenuto è scaricato durante l'acquisto, archiviato nel dispositivo e quindi eseguito il rendering nell'ambito della specifica del prodotto. Ad esempio libri, riviste problemi o livelli di gioco costituiti da file di configurazione e immagini in background.

In iOS Apple 6 offre una variazione dei prodotti fornita dal server: si ospiterà i file contenuti nei server. Questo rende molto più semplice compilare prodotti fornita dal server perché non è necessario gestire un server separato e Store Kit fornisce funzionalità di download in background che in precedenza era necessario scrivere autonomamente. Per sfruttare i vantaggi dell'hosting di Apple, abilitare l'hosting di contenuto per i nuovi prodotti di acquisto in-app e modificare il codice di Kit Store per sfruttare i vantaggi di esso. I file di contenuto di prodotto sono quindi compilati usando Xcode e caricati nei server di Apple per la revisione e rilascio.

[![](changes-to-storekit-images/image4.png "Il processo di compilazione e distribuzione")](changes-to-storekit-images/image4.png#lightbox)

Uso di App Store per offrire acquisti in-app *con il contenuto ospitato* richiede l'installazione e la configurazione seguente:

- **iTunes Connect** – si *necessario* fornite le informazioni bancarie e fiscali ad Apple, in modo che possibile rimettere fondi raccolti per tuo conto. È quindi possibile configurare i prodotti per vendere e configurare gli account utente di sandbox per testare l'acquisto.  _È inoltre necessario configurare contenuto ospitato per tali prodotti non di consumo che si desiderano ospitare con Apple_.
- **Portale di Provisioning iOS** : creazione di un identificatore del Bundle e abilitazione dell'accesso di App Store per l'app, come si farebbe per qualsiasi applicazione che supporti acquisti in-app.
- **Store Kit** – aggiunta di codice all'App per la visualizzazione dei prodotti, acquisto di prodotti e il ripristino delle transazioni.  _IOS 6 Kit Store verranno inoltre di gestire il download del contenuto del prodotto, in background, con gli aggiornamenti di stato di avanzamento._
- **Codice personalizzato** : per rilevare gli acquisti effettuati dai clienti e fornire i prodotti o servizi che hai acquistato. Utilizzare nuove classi di Kit Store di iOS 6, ad esempio `SKDownload` per recuperare il contenuto ospitato da Apple.

Le sezioni seguenti illustrano come implementare il contenuto ospitato, dalla creazione e caricamento del pacchetto per la gestione dell'acquisto e download di processo, usando il codice di esempio per questo articolo.

### <a name="sample-code"></a>Codice di esempio

Il progetto di esempio *HostedNonConsumables* (in StoreKitiOS6.zip) usa il contenuto ospitato. L'app offre due "capitoli" per la vendita, il contenuto per cui è ospitato nel server di Apple. Il contenuto è costituito da un file di testo e un'immagine, anche se contenuto molto più complesso può essere usato in un'applicazione reale.

L'app è simile prima, durante e dopo un acquisto:

 [![](changes-to-storekit-images/image5.png "L'app è simile prima, durante e dopo un acquisto")](changes-to-storekit-images/image5.png#lightbox)

Il file di testo e l'immagine vengono scaricati e copiati nella directory dei documenti dell'applicazione. Per altre informazioni sulle diverse directory disponibili per l'archiviazione delle applicazioni, vedere la [la documentazione del sistema del file](~/ios/app-fundamentals/file-system.md).

## <a name="itunes-connect"></a>iTunes Connect

Quando la creazione di nuovi prodotti che useranno Apple contenuta dell'hosting, assicurarsi di selezionare il **Non riproducibile** tipo di prodotto. Altri tipi di prodotto non supportano l'hosting del contenuto. Inoltre, non è necessario abilitare l'hosting del contenuto per *esistenti* prodotti che si vendono; attivare solo l'hosting del contenuto per i nuovi prodotti.

 [![](changes-to-storekit-images/image6.png "Selezionare il tipo di prodotto Non riproducibile")](changes-to-storekit-images/image6.png#lightbox)

Immettere un **ID prodotto**. Questo ID sarà necessario in seguito quando si crea il contenuto per questo prodotto.

 [![](changes-to-storekit-images/image7.png "Immettere un ID prodotto")](changes-to-storekit-images/image7.png#lightbox)

Hosting di contenuto è impostata nella sezione dei dettagli. Prima di acquisto in-app sarà in tempo reale, deselezionare il **ospitare il contenuto con Apple** casella di controllo se si vuole annullare (anche se è stato caricato del contenuto di prova). Tuttavia hosting del contenuto non può essere rimosso dopo l'acquisto in-app è stata rilasciata.

 [![](changes-to-storekit-images/image8.png "Hosting di contenuto con Apple")](changes-to-storekit-images/image8.png#lightbox)

Dopo che è stata attivata l'hosting di contenuto, il prodotto entrerà **attesa del caricamento** lo stato e visualizzare più questo messaggio:

 [![](changes-to-storekit-images/image9.png "Il prodotto verrà immettere in attesa per lo stato di caricamento e visualizzare più questo messaggio")](changes-to-storekit-images/image9.png#lightbox)

Il pacchetto di contenuto deve essere creato con Xcode e caricati utilizzando lo strumento di archiviazione. Istruzioni per la creazione di pacchetti di contenuto vengono fornite nella sezione successiva **creazione in corso. File PKG**.

## <a name="creating-pkg-files"></a>La creazione. File PKG

I file di contenuto viene caricato in Apple devono soddisfare le restrizioni seguenti:

- Non può superare i 2 GB di dimensioni.
- Non può contenere codice eseguibile (o i collegamenti simbolici che puntano all'esterno del contenuto).
- Deve essere formattato correttamente (tra cui una **plist** file) e hanno un **pkg** estensione di file. Tutto avverrà automaticamente se si seguono queste istruzioni sull'uso di Xcode.

È possibile aggiungere molti diversi file e tipi di file, purché soddisfino tali restrizioni. Il contenuto è compresso prima della consegna all'applicazione e decompresso da Kit Store prima che il codice vi accede.

Dopo aver caricato un pacchetto di contenuto, può essere sostituito con il contenuto più recente. Nuovo contenuto deve essere caricato e inviato per la revisione/approvazione tramite il normale processo. Incremento di `ContentVersion` campo nei pacchetti di contenuto aggiornati per indicare che è più recente.

### <a name="xcode-in-app-purchase-content-projects"></a>Progetti Xcode In-App acquisto contenuto

Creazione di pacchetti di contenuto per i prodotti di acquisto in-app attualmente richiede Xcode. C'è nessun OBJECTIVE-C è necessario scrivere codice; Xcode ha un nuovo tipo di progetto per i pacchetti che contiene solo i file e un file plist.

L'applicazione di esempio è suddiviso in capitoli libro per la vendita, ogni pacchetto di contenuto capitolo conterrà:

-  un file di testo e
-  un'immagine per rappresentare il capitolo.


Iniziare selezionando **File > Nuovo progetto** dal menu e scegliendo **contenuto di acquisto In-App**:

 [![](changes-to-storekit-images/image10.png "Scegliere il contenuto di acquisto In-App")](changes-to-storekit-images/image10.png#lightbox)

Immettere il **Product Name** e **identificatore società** in modo che il **identificatore del Bundle** corrisponde il **ID prodotto** immesso in iTunes Connetti per questo prodotto.

[![](changes-to-storekit-images/image11.png "Immettere il nome e l'identificatore")](changes-to-storekit-images/image11.png#lightbox)

A questo punto si disporrà di uno spazio vuoto **contenuto di acquisto In-App** progetto. È possibile fare doppio clic e **Aggiungi file...** o trascinarli nel **Project Navigator**. Verificare che il **ContentVersion** sia corretto (deve iniziare da 1.0, ma se si sceglie in un secondo momento aggiornare il contenuto, ricordare di incrementare il valore).

Questo screenshot Mostra Xcode con i file di contenuto inclusi nel progetto e le voci di file plist visibile nella finestra principale:

[![](changes-to-storekit-images/image12.png "In questo screenshot appare Xcode con i file di contenuto inclusi nel progetto e le voci di file plist visibile nella finestra principale")](changes-to-storekit-images/image12.png#lightbox)

Dopo aver aggiunto tutti i file di contenuto è possibile salvare il progetto e modificarlo in un secondo momento o avviare il processo di caricamento.

## <a name="uploading-pkg-files"></a>Caricamento. File PKG

È il modo più semplice per caricare i pacchetti di contenuto con il **Xcode Archive strumento**. Scegli **Product > archivio** dal menu per iniziare:

![](changes-to-storekit-images/image13.png "Scegliere Archiven")

Il pacchetto di contenuto verrà quindi visualizzato nell'archivio come illustrato di seguito. Il tipo di archivio e l'icona Mostra questa riga è un **archivio contenuto acquisti In-App**. Fare clic su **convalida...** Per controllare il pacchetto di contenuto per gli errori senza eseguire effettivamente il caricamento.

[![](changes-to-storekit-images/image14.png "Convalidare il pacchetto")](changes-to-storekit-images/image14.png#lightbox)

Account di accesso con l'iTunes Connect credenziali:

[![](changes-to-storekit-images/image15.png "Account di accesso con l'iTunes Connect credenziali")](changes-to-storekit-images/image15.png#lightbox)

Scegliere l'applicazione corretta e l'acquisto in-app per associare il contenuto con:

[![](changes-to-storekit-images/image16.png "Scegliere l'applicazione corretta e acquisto in-app da associare con questo contenuto")](changes-to-storekit-images/image16.png#lightbox)

Si verrà visualizzato un messaggio simile allo screenshot:

![Un esempio alcun messaggio problemi](changes-to-storekit-images/image17.png "riportato alcun messaggio di problemi")

A questo punto passare attraverso un processo simile, ma facendo clic su **Distribuisci...** verrà effettivamente caricare il contenuto.

[![Distribuire l'app](changes-to-storekit-images/image18.png "distribuire l'app")](changes-to-storekit-images/image18.png#lightbox)

Selezionare la prima opzione, per caricare il contenuto:

![Caricare il contenuto](changes-to-storekit-images/image19.png "caricare il contenuto")

Accedere di nuovo:

[![](changes-to-storekit-images/image15.png "Accedi")](changes-to-storekit-images/image15.png#lightbox)

Scegliere l'applicazione corretta e un record di acquisto in-app per caricare il contenuto da:

[![](changes-to-storekit-images/image20.png "Scegliere il record di acquisto dell'applicazione e in-app")](changes-to-storekit-images/image20.png#lightbox)

Attendere mentre vengono caricati i file:

[![](changes-to-storekit-images/image21.png "La finestra di dialogo di caricamento del contenuto")](changes-to-storekit-images/image21.png#lightbox)

Una volta completato il caricamento, verrà visualizzato un messaggio che avvisa che il contenuto è stato inviato per l'App Store.

[![](changes-to-storekit-images/image22.png "Un esempio di caricamento corretto di messaggio")](changes-to-storekit-images/image22.png#lightbox)

Una volta che sia stata eseguita, quando si torna alla pagina del prodotto in iTunes Connect verrà Mostra i dettagli del pacchetto e trovarsi nella **pronto per Submit** dello stato. Quando il prodotto è in questo stato, è possibile iniziare il test nell'ambiente sandbox. NON devi 'invio' prodotto a scopo di test nell'ambiente sandbox.

[![](changes-to-storekit-images/image23.png "iTunes Connect verrà Mostra i dettagli del pacchetto ed essere pronto per lo stato di invio")](changes-to-storekit-images/image23.png#lightbox)

Può richiedere tempo (ad es. alcuni minuti) tra il caricamento dell'archivio e stato di iTunes Connect in corso l'aggiornamento. È possibile inviare separatamente il prodotto per la revisione o inviarlo in combinazione con un file binario dell'applicazione. Solo dopo che Apple ha approvato ufficialmente il contenuto sarà disponibile nell'App Store di produzione per l'acquisto nell'app.

### <a name="pkg-file-format"></a>Formato di File PKG

L'utilizzo di Xcode e lo strumento di archiviazione per creare e caricare un pacchetto di contenuto ospitato indica che non viene mai visualizzato il contenuto del pacchetto stesso. Il file e directory nei pacchetti creati per ottenere l'aspetto di app di esempio simile allo screenshot seguente, con la **plist** file nella radice e i file di prodotto in un **contenuto** sottodirectory:

[![](changes-to-storekit-images/image24.png "Il file con estensione plist nella radice e i file di prodotto in una sottodirectory del contenuto")](changes-to-storekit-images/image24.png#lightbox)

Si noti la struttura di directory del pacchetto (in particolare la posizione dei file nei `Contents` sottodirectory) poiché è necessario comprendere queste informazioni per estrarre i file dal pacchetto nel dispositivo.

### <a name="updating-package-content"></a>L'aggiornamento del contenuto del pacchetto

La procedura per l'aggiornamento del contenuto dopo che è stata approvata:

- Modificare il progetto del contenuto di acquisto In-App in Xcode.
- Promuovere il numero di versione.
- Caricare di nuovo a iTunes Connect. Gli addetti agli acquisti successive otterrà automaticamente la versione più recente, ma gli utenti hanno già la versione precedente non riceveranno alcuna notifica.
- L'app è responsabile per informare gli utenti e incoraggiarli per recuperare una versione più recente del contenuto. L'app è necessario anche creare una funzione che consente di scaricare la nuova versione, usando la funzionalità di ripristino del Kit Store.
- Per determinare se esiste una versione più recente, è possibile compilare una funzionalità nell'app per recuperare SKProducts (ad es. stesso processo usato per recuperare i prezzi dei prodotti) e confrontare la proprietà ContentVersion.

## <a name="purchasing-overview"></a>Panoramica sugli acquisti

Prima di leggere questa sezione, esaminare l'oggetto esistente [documentazione di acquisto In-App](~/ios/platform/in-app-purchasing/index.md).

La sequenza di eventi che si verifica quando un prodotto con contenuto ospitato viene acquistata e il download è illustrato nella figura seguente:

[![](changes-to-storekit-images/image25.png "La sequenza di eventi che si verifica quando un prodotto con contenuto ospitato viene acquistata e download")](changes-to-storekit-images/image25.png#lightbox)

1. È possibile creare nuovi prodotti in iTunes Connect con contenuto ospitato abilitato. Il contenuto effettivo viene costruito separatamente in Xcode (nel modo semplicemente come il trascinamento di file in una cartella) e quindi archiviato e caricato in iTunes (è necessario alcun codice). Ogni prodotto viene quindi inviato per l'approvazione, dopo il quale diventa disponibile per l'acquisto. Nell'esempio di codice questi ID prodotto sono hardcoded, ma l'hosting di contenuto con Apple è più flessibile se si archivia l'elenco dei prodotti disponibili in un server remoto in modo che può essere aggiornata quando si invia nuovi prodotti e contenuti a iTunes Connect.
1. Quando l'utente acquista un prodotto, una transazione viene inserita nella coda di pagamento per l'elaborazione.
1. Kit Store inoltra la richiesta di acquisto al server di iTunes per l'elaborazione.
1. Completamento della transazione nel server di iTunes (ad es. viene addebitato dal cliente) e una ricevuta viene restituita all'app, con informazioni sul prodotto associati incluso se è possibile scaricare (e in questo caso, le dimensioni del file e altri metadati).
1. Il codice deve verificare se il prodotto è scaricabile e in tal caso, effettuare una richiesta di download del contenuto anche inserito nella coda del pagamento. Kit Store invia la richiesta al server di iTunes.
1. Server restituisce file di contenuto al Kit Store, che fornisce un callback per restituire l'avanzamento del download e l'ora le stime per il codice rimanente.
1. Al termine dell'operazione, si ottengono una notifica e passare un percorso del file nella cartella della Cache.
1. Il codice deve copiare i file e verificarli, salvare qualsiasi stato che è necessario ricordare che è stato acquistato il prodotto. Cogliere questa opportunità per impostare il flag backup correttamente i nuovi file (Suggerimento: se provengono da un server e non vengono mai modificati dall'utente, è consigliabile ignorare il backup, perché l'utente può sempre recuperarli dal server di Apple in futuro).
1. Chiamare FinishTransaction. Questo passaggio è importante poiché la transazione viene rimosso dalla coda di pagamento. È anche importante che non è necessario chiamare FinishTransaction fino a dopo aver copiato il contenuto all'esterno della directory Cache. Dopo aver chiamato FinishTransaction, sono probabile che essere eliminati rapidamente i file memorizzati nella cache.

## <a name="implementing-hosted-content-purchase"></a>Implementazione di acquisto contenuto ospitato

Le informazioni seguenti devono essere letti in combinazione con l'intero [documentazione di acquisti In-App](~/ios/platform/in-app-purchasing/index.md). Le informazioni contenute in questo documento vengono illustrate le differenze tra il contenuto ospitato e l'implementazione precedente.

### <a name="classes"></a>Classi

Le classi seguenti sono stati aggiunte o modificate al supporto di hosted contenuto in iOS 6:

- **SKDownload** : nuova classe che rappresenta un download in corso. L'API consente più di un singolo prodotto, tuttavia, inizialmente solo uno è stato implementato.
- **SKProduct** – nuove proprietà aggiunti: `Downloadable`, `ContentVersion`, `ContentLengths` matrice.
- **SKPaymentTransaction** – aggiunta di nuove proprietà: `Downloads`, che contiene una raccolta di `SKDownload` oggetti se il contenuto disponibile per il download è ospitato da questo prodotto.
- **SKPaymentQueue** – nuovo metodo aggiunto: `StartDownloads`. Chiamare questo metodo con `SKDownload` oggetti per recuperare il contenuto ospitato. Il download può verificarsi in background.
- **SKPaymentTransactionObserver** – nuovo metodo: `UpdateDownloads`. Kit Store chiama questo metodo con le informazioni di stato di avanzamento corrente operazioni di download.

Dettagli del nuovo `SKDownload` classe:

- **Lo stato di avanzamento** : un valore compreso tra 0 e 1 che è possibile usare per visualizzare un indicatore percentuale di completamento per l'utente. Non usare lo stato di avanzamento = = 1 per rilevare se è stato completato il download, verificare lo stato di = = operazione completata.
- **TimeRemaining** : stima del download tempo rimanente, espresso in secondi. -1 indica che sta ancora calcolando la stima.
- **Stato** : attivo, in attesa, completato, non è riuscita, sospesa o annullata.
- **ContentURL** : percorso in cui il contenuto è stato inserito nel disco, in File di `Cache` directory. Popolato solo una volta completato il download.
- **Errore** – controllare questa proprietà se lo stato è non riuscito.

Le interazioni tra le classi nel codice di esempio sono illustrate nella figura seguente (il codice specifico per gli acquisti di contenuto ospitati è visualizzato in verde):

[![](changes-to-storekit-images/image26.png "Gli acquisti di contenuto ospitati è visualizzata in verde nel diagramma")](changes-to-storekit-images/image26.png#lightbox)

La parte restante di questa sezione è illustrato il codice di esempio in cui queste classi sono state utilizzate:

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

Modificare l'oggetto esistente `UpdatedTransactions` sottoposto a override per verificare la presenza di contenuto scaricabile e chiamare `StartDownloads` se necessario:

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
    foreach (SKPaymentTransaction transaction in transactions) {
        switch (transaction.TransactionState) {
        case SKPaymentTransactionState.Purchased:
            // UPDATED FOR iOS 6
            if (transaction.Downloads != null && transaction.Downloads.Length > 0) {
                // Purchase complete, and it has downloads... so download them!
                SKPaymentQueue.DefaultQueue.StartDownloads (transaction.Downloads);
                // CompleteTransaction() call has moved after downloads complete
            } else {
                // complete the transaction now
                theManager.CompleteTransaction(transaction);
            }
            break;
        case SKPaymentTransactionState.Failed:
            theManager.FailedTransaction(transaction);
            break;
        case SKPaymentTransactionState.Restored:
            // TODO: you must decide how to handle restored transactions.
            // Triggering all the downloads at once is not advisable.
            theManager.RestoreTransaction(transaction);
            break;
        default:
            break;
        }
    }
}
```

Nuovo metodo sottoposto a override `UpdatedDownloads` è illustrato di seguito. Kit Store chiama questo metodo dopo aver `StartDownloads` viene attivata nella `UpdatedTransactions`. Questo metodo viene chiamato *più volte* a intervalli indeterminati per fornire è con stato del download e quindi nuovamente quando ha terminato il download. Si noti che il metodo accetta una matrice di `SKDownload` oggetti, in modo che ogni chiamata al metodo è possibile ottenere lo stato di download più nella coda. Come illustrato nell'implementazione riportata di seguito che sono gli stati di download selezionata ogni volta e l'azione appropriata.

```csharp
// ENTIRELY NEW METHOD IN iOS6
public override void PaymentQueueUpdatedDownloads (SKPaymentQueue queue, SKDownload[] downloads)
{
    Console.WriteLine (" -- PaymentQueueUpdatedDownloads");
    foreach (SKDownload download in downloads) {
        switch (download.DownloadState) {
        case SKDownloadState.Active:
            // TODO: implement a notification to the UI (progress bar or something?)
            Console.WriteLine ("Download progress:" + download.Progress);
            Console.WriteLine ("Time remaining:   " + download.TimeRemaining); // -1 means 'still calculating'
            break;
        case SKDownloadState.Finished:
            Console.WriteLine ("Finished!!!!");
            Console.WriteLine ("Content URL:" + download.ContentUrl);

            // UNPACK HERE! Calls FinishTransaction when it's done
            theManager.SaveDownload (download);

            break;
        case SKDownloadState.Failed:
            Console.WriteLine ("Failed"); // TODO: UI?
            break;
        case SKDownloadState.Cancelled:
            Console.WriteLine ("Canceled"); // TODO: UI?
            break;
        case SKDownloadState.Paused:
        case SKDownloadState.Waiting:
            break;
        default:
            break;
        }
    }
}
```

### <a name="inapppurchasemanager-skproductsrequestdelegate"></a>InAppPurchaseManager (SKProductsRequestDelegate)

Questa classe contiene un nuovo metodo `SaveDownload` che viene chiamato dopo ogni download viene completato correttamente.

Il contenuto ospitato è stato scaricato e decompresso nel `Cache` directory. La struttura della. File PKG richiede tutti i file da salvare un `Contents` sottodirectory, in modo che il codice seguente estrae file dall'interno di `Contents` sottodirectory.

Il codice scorre tutti i file nel pacchetto di contenuto e li copia nel `Documents` directory, in una sottocartella denominata per il `ProductIdentifier`. Chiama infine `CompleteTransaction`, che chiama `FinishTransaction` per rimuovere la transazione dalla coda di pagamento.

```csharp
// ENTIRELY NEW METHOD IN iOS 6
public void SaveDownload (SKDownload download)
{
    var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
    var targetfolder = System.IO.Path.Combine (documentsPath, download.Transaction.Payment.ProductIdentifier);
    // targetfolder will be "/Documents/com.xamarin.storekitdoc.montouchimages/" or something like that
    if (!System.IO.Directory.Exists (targetfolder))
        System.IO.Directory.CreateDirectory (targetfolder);
    foreach (var file in System.IO.Directory.EnumerateFiles 
             (System.IO.Path.Combine(download.ContentUrl.Path, "Contents"))) { // Contents directory is the default in .PKG files
        var fileName = file.Substring (file.LastIndexOf ("/") + 1);
        var newFilePath = System.IO.Path.Combine(targetfolder, fileName);
        if (!System.IO.File.Exists(newFilePath)) // HACK: this won't support new versions...
            System.IO.File.Copy (file, newFilePath);
        else
            Console.WriteLine ("already exists " + newFilePath);
    }
    CompleteTransaction (download.Transaction); // so it gets 'finished'
}
```

Quando `FinishTransaction` viene chiamato, scaricato i file non è più rimangono nel `Cache` directory. Tutti i file devono essere copiati prima di chiamare `FinishTransaction`.


## <a name="other-considerations"></a>Altre considerazioni

Esempio di codice precedente illustra un'implementazione semplice della modalità di acquisto contenuto ospitato. Esistono alcuni aspetti aggiuntivi che è necessario prendere in considerazione:

### <a name="detecting-updated-content"></a>Rilevamento di contenuto aggiornato

Sebbene sia possibile aggiornare i pacchetti di contenuto ospitati, Store Kit non fornisce alcun meccanismo per esegue il push di questi aggiornamenti per gli utenti che hanno già scaricato e acquistato il prodotto. Per implementare questa funzionalità, il codice può verificare il nuovo `SKProduct.ContentVersion` proprietà (se il `SKProduct` è `Downloadable`) regolarmente e per rilevare se il valore viene incrementato. In alternativa è possibile creare un sistema di notifica push.

### <a name="installing-updated-content-versions"></a>Installazione di versioni aggiornate del contenuto

Il codice di esempio precedente ignora la copia dei file quando il file esiste già. Ciò non è una buona idea se si desidera supportare le versioni più recenti del contenuto viene scaricato.

Potrebbe essere un'alternativa a copiare il contenuto in una cartella denominata per la versione e tenere traccia di quale sia la versione corrente (ad es. in `NSUserDefaults` o ovunque si archiviano i record di acquisto completato).

### <a name="restoring-transactions"></a>Il ripristino delle transazioni

Quando si `SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions` viene chiamato, Kit Store restituisce tutte le transazioni precedenti per l'utente. Se hanno acquistato un numero elevato di elementi o se ogni fornitore dispone di pacchetti di contenuto di grandi dimensioni, quindi il ripristino può comportare un notevole traffico di rete come tutto ciò che ottiene in coda per il download in una sola volta.

È consigliabile tenere traccia di se un prodotto è stato acquistato separatamente dal download effettivo del pacchetto di contenuto associato.

### <a name="pausing-restarting-and-canceling-downloads"></a>La sospensione, il riavvio e annullamento dei download

Anche se il codice di esempio non illustra questa funzionalità, è possibile sospendere e riavviare i download di contenuto ospitati. Il `SKPaymentQueue.DefaultQueue` dispone di metodi per `PauseDownloads`, `ResumeDownloads` e `CancelDownloads`.

Se il codice chiama `FinishTransaction` nella coda del pagamento prima il download in corso `Finished` quindi che il download viene annullato automaticamente.

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>Impostazione del Flag SKIP-Backup per il contenuto scaricato

Linee guida di Apple iCloud Backup suggerisce che il contenuto non utente facilmente ripristinati da un server non deve *non* essere sottoposti a backup (poiché verrebbe inutilmente utilizzata spazio di archiviazione iCloud). Per altre informazioni su come impostare l'attributo di backup, vedere la [file system](~/ios/app-fundamentals/file-system.md) documentazione.

## <a name="summary"></a>Riepilogo

Questo articolo è state introdotte due nuove funzionalità del Kit di Store in iOS6: acquisto in iTunes e altri contenuti da all'interno dell'app e l'uso di server di Apple per ospitare i propri acquisti in-app. In questa introduzione deve essere letta in combinazione con l'oggetto esistente [documentazione di acquisto In-App](~/ios/platform/in-app-purchasing/index.md) per una copertura completa dell'implementazione della funzionalità di Kit Store.

## <a name="related-links"></a>Collegamenti correlati

- [StoreKit (esempio)](https://developer.xamarin.com/samples/StoreKit/)
- [Acquisti in-app](~/ios/platform/in-app-purchasing/index.md)
- [Riferimento a Framework di StoreKit](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [Riferimento alla classe SKStoreProductViewController](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [riferimento all'API di ricerca di iTunes](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [Video WWDC: In vendita i prodotti con il Kit di Store](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
