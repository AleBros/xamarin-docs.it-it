---
title: Modifiche di StoreKit in iOS 6
description: "iOS 6 introduce due modifiche all'API del kit di archiviazione: la possibilità di visualizzare i prodotti iTunes (e App Store/da iBookstore) dall'app e una nuova opzione di acquisto in-app in cui Apple ospiterà i file scaricabili. In questo documento viene illustrato come implementare tali funzionalità con Novell. iOS."
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: d7491af1ced4e8e0309bb3e22298d33ee5a042be
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571545"
---
# <a name="changes-to-storekit-in-ios-6"></a>Modifiche di StoreKit in iOS 6

_iOS 6 ha introdotto due modifiche all'API di Store Kit: la possibilità di visualizzare i prodotti iTunes (e App Store/da iBookstore) dall'app e una nuova opzione di acquisto in-app in cui Apple ospiterà i file scaricabili. In questo documento viene illustrato come implementare tali funzionalità con Novell. iOS._

Le principali modifiche apportate al kit di archiviazione in iOS6 sono queste due nuove funzionalità:

- Il **contenuto in-app visualizza & acquisti** : gli utenti possono acquistare e scaricare app, musica, libri e altro contenuto iTunes senza uscire dall'app. È anche possibile collegarsi alle proprie app per promuovere l'acquisto o semplicemente incoraggiare le verifiche e le valutazioni.
- **Contenuto ospitato per l'acquisto in-app** : Apple archivia e distribuisce il contenuto associato ai prodotti di acquisto in-app, che elimina la necessità di un server separato per ospitare i file, supporta automaticamente il download in background e consente di scrivere meno codice.

Vedere le guide di [acquisto in-app](~/ios/platform/in-app-purchasing/index.md) per la copertura dettagliata delle API StoreKit.

## <a name="requirements"></a>Requisiti

Le funzionalità di Store Kit descritte in questo documento richiedono iOS 6 e Xcode 4,5, insieme a Novell. iOS 6,0.

## <a name="in-app-content-display--purchasing"></a>Visualizzazione del contenuto in-app & acquisti

La nuova funzionalità di acquisto in-app in iOS consente agli utenti di visualizzare le informazioni sul prodotto e acquistare o scaricare il prodotto dall'interno dell'app.
In precedenza le applicazioni avrebbero dovuto attivare iTunes, l'App Store o il da iBookstore, il che comporterebbe l'uscita dell'applicazione originale da parte dell'utente. Questa nuova funzionalità restituisce automaticamente l'utente all'app al termine dell'operazione.

[![](changes-to-storekit-images/image1.png "Automatically returning to an app after purchase")](changes-to-storekit-images/image1.png#lightbox)

Di seguito sono riportati alcuni esempi di come è possibile utilizzare:

- **Incoraggiare gli utenti a valutare l'app** : è possibile aprire la pagina dell'app Store in modo che l'utente possa valutare ed esaminare l'app senza lasciarla.
- **Promozione incrociata delle app** : consente all'utente di visualizzare altre app pubblicate, con la possibilità di acquistare/scaricare immediatamente.
- **Aiutare gli utenti a trovare e scaricare il contenuto** : consentire agli utenti di acquistare contenuti che l'app trova, gestisce o aggrega (ad esempio, un'app relativa alla musica può fornire una playlist di canzoni e consentire l'acquisto di ogni brano dall'interno dell'app.

Una volta `SKStoreProductViewController` visualizzato, l'utente può interagire con le informazioni sul prodotto come se si trovasse in iTunes, in App Store o in da iBookstore. L'utente può:

- Visualizza screenshot (per le app),
- Brani o video di esempio (per musica, programmi TV e film),
- Leggere (e scrivere) recensioni,
- Acquistare & download, che si verifica interamente all'interno di View Controller e Store Kit.

Alcune opzioni all'interno `SKStoreProductViewController` di continueranno a forzare l'utente a lasciare l'app e ad aprire l'app dello Store pertinente, ad esempio facendo clic sui **prodotti correlati** o sul collegamento del **supporto** dell'app.

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

L'API per mostrare un prodotto in qualsiasi app è semplice: richiede solo la creazione e la visualizzazione di un `SKStoreProductViewController` . Per creare e visualizzare un prodotto, attenersi alla procedura seguente:

1. Creare un `StoreProductParameters` oggetto per passare parametri al controller di visualizzazione, inclusa la `productId` nel costruttore.
1. Creare un'istanza di `SKProductViewController` . Assegnarlo a un campo a livello di classe.
1. Assegnare un gestore all'evento del controller di visualizzazione `Finished` , che dovrebbe ignorare il controller di visualizzazione. Questo evento viene chiamato quando l'utente preme Annulla; o in caso contrario finalizza una transazione all'interno del controller di visualizzazione.
1. Chiamare il `LoadProduct` metodo passando l'oggetto `StoreProductParameters` e un gestore di completamento. Il gestore di completamento deve verificare che la richiesta del prodotto sia stata completata correttamente e, in tal caso, presentare il `SKProductViewController` modale. È necessario aggiungere la gestione degli errori appropriata nel caso in cui non sia possibile recuperare il prodotto.

### <a name="example"></a>Esempio

Il progetto *ProductView* nel codice di esempio *StoreKit* per questo articolo implementa un `Buy` metodo che accetta l'ID Apple di qualsiasi prodotto e visualizza il `SKStoreProductViewController` . Il codice seguente Visualizza le informazioni sul prodotto per qualsiasi ID Apple specificato:

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

L'app ha un aspetto simile alla schermata seguente durante l'esecuzione: il download o l'acquisto avviene interamente all'interno di `SKStoreProductViewController` :

[![](changes-to-storekit-images/image2.png "The app looks like this when running")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>Supporto dei sistemi operativi meno recenti

L'applicazione di esempio include codice che illustra come aprire App Store, iTunes o da iBookstore nelle versioni precedenti di iOS. Usare il `OpenUrl` metodo per aprire un URL **iTunes.com** creato correttamente.

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

### <a name="errors"></a>Errors

Se l'ID Apple usato non è valido, si verificherà l'errore seguente, che può generare confusione, perché implica un problema di rete o di autenticazione di qualche tipo.

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>Lettura della documentazione di Objective-C

Gli sviluppatori che leggono about Store Kit nel portale per sviluppatori di Apple visualizzeranno un protocollo, [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) , descritto in relazione a questa nuova funzionalità. Il protocollo delegato dispone di un solo metodo, productViewControllerDidFinish, che è stato esposto come `Finished` evento in `SKStoreProductViewController` Novell. iOS.

## <a name="determining-apple-ids"></a>Determinazione degli ID Apple

L'ID Apple richiesto dal `SKStoreProductViewController` è un *numero* (da non confondere con gli ID bundle come "com. Novell. MWC2012"). Esistono diversi modi in cui è possibile trovare l'ID Apple per i prodotti che si desidera visualizzare, elencati di seguito:

### <a name="itunesconnect"></a>iTunesConnect

Per le applicazioni pubblicate, è facile trovare l' **ID Apple** in iTunes Connect:

[![](changes-to-storekit-images/image3.png "Finding the Apple ID in iTunes Connect")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API"></a>

### <a name="search-api"></a>Cerca l'API

Apple fornisce un'API di ricerca dinamica per eseguire query su tutti i prodotti nell'App Store, in iTunes e in da iBookstore. Le informazioni su come accedere all'API di ricerca sono reperibili nelle risorse affiliate di Apple, anche se l'API è esposta a chiunque, non solo alle consociate registrate. Il codice JSON risultante può essere analizzato per individuare l' `trackId` ID Apple da usare con `SKStoreProductViewController` .

I risultati includeranno anche altri metadati, tra cui informazioni di visualizzazione e URL di grafica che possono essere usati per eseguire il rendering del prodotto nell'app.

Ecco alcuni esempi:

- **iBooks app** – [ https://itunes.apple.com/search?term=ibooks&amp ; Entity = software &amp; Country = US](https://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us)
- **Punto e canguro iBook** – [ https://itunes.apple.com/search?term=dot+and+the+kangaroo&amp ; Entity = eBook &amp; Country = US](https://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us)

### <a name="enterprise-partner-feed"></a>Feed partner Enterprise

Apple fornisce ai partner approvati un dump completo dei dati di tutti i prodotti, sotto forma di file flat disponibili per il database scaricabili. Se si è idonei per l'accesso al feed del partner aziendale, l'ID Apple per qualsiasi prodotto si trova in tale set di dati.

Molti utenti del feed partner aziendale sono membri del [programma affiliato](https://www.apple.com/itunes/affiliates) che consente di guadagnare le commissioni sulle vendite del prodotto. `SKStoreProductViewController`non supporta gli ID affiliati (al momento della stesura di questo documento).

### <a name="direct-product-links"></a>Collegamenti diretti al prodotto

L'ID Apple per un prodotto può essere dedotto dal relativo collegamento URL di anteprima iTunes.
In qualsiasi collegamento al prodotto iTunes (per app, musica o libri) trova la parte dell'URL che inizia con `id` e usa il numero che segue.

Ad esempio, il collegamento diretto a iBooks è

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

e l'ID Apple è **364709193**. Analogamente, per l'app MWC2012, il collegamento diretto è

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

e l'ID Apple è **496963922**.

## <a name="in-app-purchase-hosted-content"></a>Contenuto ospitato per l'acquisto in-app

Se gli acquisti in-app sono costituiti da contenuto scaricabile (ad esempio libri o altri supporti, grafica a livello di gioco e configurazione o altri file di grandi dimensioni), questi file vengono ospitati nel server Web e le app devono incorporare il codice per scaricarli in modo sicuro dopo l'acquisto. A partire da iOS 6, Apple ospiterà i file nei server, eliminando la necessità di un server separato. La funzionalità è disponibile solo per prodotti non utilizzabili (non utilizzabili o sottoscrizioni). I vantaggi dell'uso del servizio di hosting di Apple includono:

- Risparmiare sull'hosting & costi della larghezza di banda.
- Probabilmente più scalabile rispetto a qualsiasi host server attualmente in uso.
- Meno codice da scrivere, dal momento che non è necessario compilare alcuna elaborazione sul lato server.
- Il download in background viene implementato per l'utente.

Nota: il test del contenuto di acquisto in-app ospitato nel simulatore iOS non è supportato, pertanto è necessario eseguire il test con un dispositivo reale.

### <a name="hosted-content-basics"></a>Nozioni di base sul contenuto ospitato

Prima di iOS 6, erano disponibili due modi per fornire un prodotto (descritto più dettagliatamente nella documentazione di [acquisto in-app di Novell](~/ios/platform/in-app-purchasing/index.md) ):

- **Prodotti predefiniti** : funzionalità che vengono sbloccate acquistando, ma che sono compilate nell'applicazione (codice o risorse incorporate). Esempi di prodotti incorporati includono filtri foto sbloccati o Power-up in gioco.
- **Prodotti distribuiti dal server** : dopo l'acquisto, l'applicazione deve scaricare il contenuto da un server in uso. Questo contenuto viene scaricato durante l'acquisto, archiviato nel dispositivo e quindi sottoposto a rendering come parte del prodotto. Gli esempi includono libri, problemi di riviste o livelli di gioco costituiti da file di configurazione e arte di sfondo.

In iOS 6 Apple offre una variante dei prodotti distribuiti dal server: i file di contenuto vengono ospitati nei server. Questa operazione rende molto più semplice la creazione di prodotti distribuiti dal server poiché non è necessario utilizzare un server separato e Store Kit fornisce funzionalità di download in background che in precedenza era necessario scrivere manualmente. Per sfruttare i vantaggi offerti dall'hosting di Apple, è possibile abilitare l'hosting del contenuto per i nuovi prodotti di acquisto in-app e modificare il codice del kit di archiviazione per sfruttarne i vantaggi. I file di contenuto del prodotto vengono quindi compilati con Xcode e caricati nei server Apple per la revisione e il rilascio.

[![](changes-to-storekit-images/image4.png "The build and deliver process")](changes-to-storekit-images/image4.png#lightbox)

L'uso dell'app Store per fornire l'acquisto in-app *con contenuto ospitato* richiede l'installazione e la configurazione seguenti:

- **iTunes Connect** : è *necessario* avere fornito le informazioni bancarie e fiscali ad Apple per poter rimettere i fondi raccolti per conto dell'utente. È quindi possibile configurare i prodotti da vendere e configurare gli account utente sandbox per testare l'acquisto.  _È inoltre necessario configurare il contenuto ospitato per i prodotti non utilizzabili che si desidera ospitare con Apple_.
- **portale di provisioning iOS** : creazione di un identificatore del bundle e abilitazione dell'accesso all'App Store per l'app, come per qualsiasi applicazione che supporta l'acquisto in-app.
- **Store Kit** : aggiunta di codice all'app per la visualizzazione di prodotti, acquisto di prodotti e ripristino di transazioni.  _In iOS 6 Store Kit gestisce anche il download del contenuto del prodotto, in background, con aggiornamenti sullo stato di avanzamento._
- **Codice personalizzato** : per tenere traccia degli acquisti effettuati dai clienti e fornire i prodotti o i servizi acquistati. Usare nuove classi del kit di archiviazione iOS 6 come `SKDownload` per recuperare il contenuto ospitato da Apple.

Le sezioni seguenti illustrano come implementare contenuto ospitato, da creare e caricare il pacchetto per gestire il processo di acquisto e download, usando il codice di esempio per questo articolo.

### <a name="sample-code"></a>Codice di esempio

Il progetto di esempio *HostedNonConsumables* (in StoreKitiOS6. zip) USA contenuto ospitato. L'app offre due "capitoli del libro" per la vendita, il contenuto per il quale è ospitato nei server Apple. Il contenuto è costituito da un file di testo e da un'immagine, sebbene sia possibile utilizzare contenuto molto più complesso in un'applicazione reale.

L'app ha un aspetto simile al seguente, durante e dopo un acquisto:

 [![](changes-to-storekit-images/image5.png "The app looks like this before, during and after a purchase")](changes-to-storekit-images/image5.png#lightbox)

Il file di testo e l'immagine vengono scaricati e copiati nella directory dei documenti dell'applicazione. Per ulteriori informazioni sulle diverse directory disponibili per l'archiviazione delle applicazioni, vedere la [documentazione file System](~/ios/app-fundamentals/file-system.md).

## <a name="itunes-connect"></a>iTunes Connect

Quando si creano nuovi prodotti che utilizzeranno l'hosting del contenuto di Apple, assicurarsi di selezionare il tipo di prodotto **non** utilizzabile. Altri tipi di prodotto non supportano l'hosting del contenuto. Inoltre, è consigliabile non abilitare l'hosting del contenuto per i prodotti *esistenti* che si vendono; attivare l'hosting del contenuto solo per i nuovi prodotti.

 [![](changes-to-storekit-images/image6.png "Select the Non-Consumable product type")](changes-to-storekit-images/image6.png#lightbox)

Immettere un **ID prodotto**. Questo ID sarà necessario in un secondo momento quando si creerà il contenuto per questo prodotto.

 [![](changes-to-storekit-images/image7.png "Enter a Product ID")](changes-to-storekit-images/image7.png#lightbox)

L'hosting del contenuto è impostato nella sezione dei dettagli. Prima di procedere con l'acquisto in-app, deselezionare la casella di controllo **contenuto host con Apple** se si vuole annullare (anche se è stato caricato un contenuto di test). Tuttavia, non è possibile rimuovere l'hosting del contenuto dopo che l'acquisto in-app è andato in tempo reale.

 [![](changes-to-storekit-images/image8.png "Hosting content with Apple")](changes-to-storekit-images/image8.png#lightbox)

Dopo aver attivato l'hosting del contenuto, il prodotto entrerà **in attesa dello stato di caricamento** e visualizzerà il messaggio seguente:

 [![](changes-to-storekit-images/image9.png "The product will enter Waiting for Upload status and show this message")](changes-to-storekit-images/image9.png#lightbox)

Il pacchetto di contenuto deve essere creato con Xcode e caricato con lo strumento di archiviazione. Le istruzioni per la creazione di pacchetti di contenuto sono fornite nella sezione successiva **creazione. File PKG**.

## <a name="creating-pkg-files"></a>Creazione. File PKG

È necessario che i file di contenuto caricati in Apple soddisfino le restrizioni seguenti:

- Non può superare i 2 GB di dimensioni.
- Non può contenere codice eseguibile (o collegamenti simbolici che puntano all'esterno del contenuto).
- Deve essere formattato correttamente (incluso un file con estensione **plist** ) e avere un'estensione di file con estensione **pkg** . Questa operazione verrà eseguita automaticamente se si seguono queste istruzioni usando Xcode.

È possibile aggiungere molti file e tipi di file diversi, purché soddisfino tali restrizioni. Il contenuto viene compresso prima del recapito all'applicazione e viene decompresso dal kit di archiviazione prima che il codice ne acceda.

Dopo il caricamento di un pacchetto di contenuto, è possibile sostituirlo con contenuto più recente. Il nuovo contenuto deve essere caricato e inviato per la revisione e l'approvazione tramite il processo normale. Incrementare il `ContentVersion` campo nei pacchetti di contenuto aggiornati per indicare che è più recente.

### <a name="xcode-in-app-purchase-content-projects"></a>Progetti di contenuto di acquisto in-app Xcode

La creazione di pacchetti di contenuto per prodotti di acquisto in-app richiede attualmente Xcode. Non è necessaria alcuna codifica OBJECTIVE-C. Xcode ha un nuovo tipo di progetto per questi pacchetti che contiene solo i file e un plist.

L'applicazione di esempio include i capitoli del libro per la vendita. ogni pacchetto di contenuto del capitolo conterrà:

- un file di testo e
- immagine che rappresenta il capitolo.

Per iniziare, selezionare **File > nuovo progetto** dal menu e scegliere **contenuto di acquisto in-app**:

 [![](changes-to-storekit-images/image10.png "Choose In-App Purchase Content")](changes-to-storekit-images/image10.png#lightbox)

Immettere il **nome del prodotto** e l' **identificatore della società** in modo che l' **identificatore del bundle** corrisponda all' **ID prodotto** immesso in iTunes Connect per questo prodotto.

[![](changes-to-storekit-images/image11.png "Enter the  Name and Identifier")](changes-to-storekit-images/image11.png#lightbox)

A questo punto si disporrà di un progetto **di contenuto di acquisto in-app** vuoto. È possibile fare clic con il pulsante destro del mouse e **aggiungere file...** o trascinarli nello **strumento di navigazione del progetto**. Verificare che **ContentVersion** sia corretto (dovrebbe iniziare a 1,0, ma se in seguito si sceglie di aggiornare il contenuto, ricordarsi di incrementarlo).

Questa schermata mostra Xcode con i file di contenuto inclusi nel progetto e le voci plist visibili nella finestra principale:

[![](changes-to-storekit-images/image12.png "This screenshot shows Xcode with the content files included in the project and the plist entries visible in the main window")](changes-to-storekit-images/image12.png#lightbox)

Dopo aver aggiunto tutti i file di contenuto, è possibile salvare il progetto e modificarlo di nuovo in un secondo momento oppure iniziare il processo di caricamento.

## <a name="uploading-pkg-files"></a>Caricamento. File PKG

Il modo più semplice per caricare i pacchetti di contenuto è con lo **strumento di archiviazione Xcode**. Scegliere **Product > Archive** dal menu per iniziare:

![](changes-to-storekit-images/image13.png "Choose Archiven")

Il pacchetto di contenuto verrà quindi visualizzato nell'archivio come illustrato di seguito.
Il tipo di archivio e l'icona mostrano che questa riga è un **Archivio del contenuto di acquisto in-app**. Fare clic su **convalida...** per verificare la presenza di errori nel pacchetto di contenuto senza eseguire effettivamente il caricamento.

[![](changes-to-storekit-images/image14.png "Validate the package")](changes-to-storekit-images/image14.png#lightbox)

Accedere con le credenziali di iTunes Connect:

[![](changes-to-storekit-images/image15.png "Login with your iTunes Connect credentials")](changes-to-storekit-images/image15.png#lightbox)

Scegliere l'applicazione corretta e l'acquisto in-app per associare il contenuto a:

[![](changes-to-storekit-images/image16.png "Choose the correct application and in-app purchase to associate this content with")](changes-to-storekit-images/image16.png#lightbox)

Verrà visualizzato un messaggio simile a questo screenshot:

![Messaggio di nessun problema di esempio](changes-to-storekit-images/image17.png "Messaggio di nessun problema di esempio")

Verrà ora illustrato un processo simile, ma facendo clic su **Distribuisci...** caricherà effettivamente il contenuto.

[![Distribuire l'app](changes-to-storekit-images/image18.png "Distribuire l'app")](changes-to-storekit-images/image18.png#lightbox)

Selezionare la prima opzione per caricare il contenuto:

![Caricare il contenuto](changes-to-storekit-images/image19.png "Caricare il contenuto")

Accedi di nuovo:

[![](changes-to-storekit-images/image15.png "Login in")](changes-to-storekit-images/image15.png#lightbox)

Scegliere l'applicazione corretta e il record di acquisto in-app per caricare il contenuto in:

[![](changes-to-storekit-images/image20.png "Choose the application and in-app purchase record")](changes-to-storekit-images/image20.png#lightbox)

Attendere che i file vengano caricati:

[![](changes-to-storekit-images/image21.png "The content upload dialog")](changes-to-storekit-images/image21.png#lightbox)

Al termine del caricamento, verrà visualizzato un messaggio che informa che il contenuto è stato inviato all'App Store.

[![](changes-to-storekit-images/image22.png "An example successful upload message")](changes-to-storekit-images/image22.png#lightbox)

Al termine, quando si torna alla pagina del prodotto in iTunes Connect, verranno visualizzati i dettagli del pacchetto e sarà possibile **inviare** lo stato. Quando il prodotto è in questo stato, è possibile avviare il test nell'ambiente sandbox. NON è necessario "inviare" il prodotto per il test in sandbox.

[![](changes-to-storekit-images/image23.png "iTunes Connect it will show the package details and be in Ready to Submit status")](changes-to-storekit-images/image23.png#lightbox)

Può richiedere del tempo, ad esempio pochi minuti) tra il caricamento dell'archivio e lo stato di iTunes Connect aggiornato. È possibile inviare il prodotto per la revisione separatamente oppure inviarlo insieme a un file binario dell'applicazione. Solo dopo che Apple ha approvato ufficialmente il contenuto sarà disponibile nell'App Store di produzione per l'acquisto nell'app.

### <a name="pkg-file-format"></a>Formato file PKG

L'uso di Xcode e dello strumento Archive per creare e caricare un pacchetto di contenuto ospitato significa che non è mai possibile visualizzare il contenuto del pacchetto stesso. I file e le directory nei pacchetti creati per l'app di esempio hanno un aspetto simile a quello riportato nella schermata seguente, con il file **plist** nella radice e i file di prodotto in una sottodirectory di **contenuti** :

[![](changes-to-storekit-images/image24.png "The plist file in the root and the product files in a Contents subdirectory")](changes-to-storekit-images/image24.png#lightbox)

Prendere nota della struttura di directory del pacchetto (in particolare il percorso dei file nella `Contents` sottodirectory), perché sarà necessario comprendere queste informazioni per estrarre i file dal pacchetto nel dispositivo.

### <a name="updating-package-content"></a>Aggiornamento del contenuto del pacchetto

Procedura per aggiornare il contenuto dopo che è stato approvato:

- Modificare il progetto di contenuto di acquisto in-app in Xcode.
- Numero di versione di Bump.
- Caricare nuovamente in iTunes Connect. Gli acquirenti successivi otterranno automaticamente la versione più recente, ma gli utenti che hanno già la versione precedente non riceveranno alcuna notifica.
- L'app è responsabile della notifica agli utenti e della loro promozione per recuperare una versione più recente del contenuto. L'app deve anche compilare una funzione che Scarica la nuova versione, usando la funzionalità di ripristino di Store Kit.
- Per determinare se esiste una versione più recente, è possibile creare una funzionalità nell'app per recuperare SKProducts (ad esempio, stesso processo utilizzato per recuperare i prezzi dei prodotti e confrontare la proprietà ContentVersion.

## <a name="purchasing-overview"></a>Panoramica sugli acquisti

Prima di leggere questa sezione, esaminare la [documentazione di acquisto in-app](~/ios/platform/in-app-purchasing/index.md)esistente.

La sequenza di eventi che si verifica quando un prodotto con contenuto ospitato viene acquistato e il download viene illustrato in questo diagramma:

[![](changes-to-storekit-images/image25.png "The sequence of events that occurs when a product with hosted content is purchased and download")](changes-to-storekit-images/image25.png#lightbox)

1. È possibile creare nuovi prodotti in iTunes Connect con contenuto ospitato abilitato. Il contenuto effettivo viene costruito separatamente in Xcode (semplicemente come il trascinamento di file in una cartella) e quindi archiviato e caricato in iTunes (non è richiesto alcun codice). Ogni prodotto viene quindi inviato per l'approvazione, dopo il quale diventa disponibile per l'acquisto. Nel codice di esempio questi ID prodotto sono hardcoded, ma l'hosting del contenuto con Apple è più flessibile se si archivia l'elenco dei prodotti disponibili in un server remoto in modo che possa essere aggiornato quando si inviano nuovi prodotti e contenuto a iTunes Connect.
1. Quando l'utente acquista un prodotto, una transazione viene inserita nella coda di pagamento per l'elaborazione.
1. Il kit di archiviazione Invia la richiesta di acquisto ai server iTunes per l'elaborazione.
1. La transazione è stata completata nei server iTunes, ad esempio il cliente viene addebitato) e viene restituita una ricevuta all'app, con le informazioni sul prodotto associate, inclusa la possibilità di scaricarlo (e, in caso affermativo, le dimensioni del file e altri metadati).
1. Il codice deve verificare se il prodotto è scaricabile e, in tal caso, effettuare una richiesta di download del contenuto che viene inserita anche nella coda di pagamento. Il kit di archiviazione Invia questa richiesta ai server iTunes.
1. Il server restituisce il file di contenuto al kit di archiviazione, che fornisce un callback per restituire lo stato di avanzamento del download e le stime rimanenti del tempo al codice.
1. Al termine, viene visualizzata una notifica e viene passato un percorso di file nella cartella della cache.
1. Il codice deve copiare i file e verificarli, salvando eventuali Stati che è necessario ricordare che il prodotto è stato acquistato. Cogliere questa opportunità per impostare correttamente il flag di backup nei nuovi file (Suggerimento: se provengono da un server e non vengono mai modificati dall'utente, è consigliabile ignorare il backup, perché l'utente può sempre recuperarli dai server Apple in futuro).
1. Chiamare FinishTransaction. Questo passaggio è importante perché rimuove la transazione dalla coda di pagamento. È anche importante non chiamare FinishTransaction fino a quando il contenuto non viene copiato dalla directory della cache. Una volta chiamato FinishTransaction, è probabile che i file memorizzati nella cache vengano eliminati rapidamente.

## <a name="implementing-hosted-content-purchase"></a>Implementazione dell'acquisto di contenuto ospitato

Le informazioni seguenti devono essere lette insieme alla documentazione completa [per gli acquisti in-app](~/ios/platform/in-app-purchasing/index.md). Le informazioni contenute in questo documento sono incentrate sulle differenze tra contenuto ospitato e implementazione precedente.

### <a name="classes"></a>Classi

Le seguenti classi sono state aggiunte o modificate per supportare il contenuto ospitato in iOS 6:

- **SKDownload** : nuova classe che rappresenta un download in corso. L'API consente più di un prodotto, ma inizialmente ne è stata implementata solo una.
- **SKProduct** -sono state aggiunte nuove proprietà: `Downloadable` , `ContentVersion` , `ContentLengths` Array.
- **SKPaymentTransaction** : nuova proprietà aggiunta: `Downloads` , che contiene una raccolta di `SKDownload` oggetti se questo prodotto ha ospitato contenuto disponibile per il download.
- **SKPaymentQueue** : nuovo metodo aggiunto: `StartDownloads` . Chiamare questo metodo con `SKDownload` gli oggetti per recuperare il contenuto ospitato. Il download può essere eseguito in background.
- **SKPaymentTransactionObserver** -nuovo metodo: `UpdateDownloads` . Il kit di archiviazione chiama questo metodo con informazioni sullo stato delle operazioni di download correnti.

Dettagli della nuova `SKDownload` classe:

- **Progress** : valore compreso tra 0-1 che è possibile usare per visualizzare un indicatore di completamento percentuale per l'utente. Non usare Progress = = 1 per rilevare se il download è stato completato, verificare lo stato = = completato.
- **TimeRemaining** : stima del tempo di download rimanente, in secondi. -1 indica che è ancora in calcolo la stima.
- **Stato** : attivo, in attesa, terminato, non riuscito, sospeso, annullato.
- **ContentURL** : percorso del file in cui il contenuto è stato inserito sul disco, nella `Cache` Directory. Viene popolato solo al termine del download.
- **Errore** : controllare questa proprietà se lo stato non è riuscito.

Le interazioni tra le classi nel codice di esempio sono illustrate in questo diagramma (il codice specifico per gli acquisti di contenuto ospitato viene visualizzato in verde):

[![](changes-to-storekit-images/image26.png "Hosted content purchases is shown in green in this diagram")](changes-to-storekit-images/image26.png#lightbox)

Il codice di esempio in cui sono state usate queste classi è illustrato nella parte restante di questa sezione:

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

Modificare la `UpdatedTransactions` sostituzione esistente per verificare la presenza di contenuto scaricabile e chiamare `StartDownloads` se necessario:

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

Di seguito è riportato il nuovo metodo sottoposto a override `UpdatedDownloads` . Il kit di archiviazione chiama questo metodo dopo l' `StartDownloads` attivazione di `UpdatedTransactions` . Questo metodo viene chiamato *più volte* a intervalli indeterminati per fornire lo stato di avanzamento del download e quindi di nuovo al termine del download. Si noti che il metodo accetta una matrice di `SKDownload` oggetti, quindi ciascuna chiamata al metodo può fornire lo stato di più download nella coda. Come illustrato nell'implementazione di sotto gli Stati di download, vengono controllati ogni volta e viene eseguita un'azione appropriata.

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

Questa classe contiene un nuovo metodo `SaveDownload` che viene chiamato dopo che ogni download è stato completato correttamente.

Il contenuto ospitato è stato scaricato correttamente e decompresso nella `Cache` Directory. Struttura dell'oggetto. Il file PKG richiede che tutti i file vengano salvati in una `Contents` sottodirectory, quindi il codice seguente estrae i file dall'interno della `Contents` sottodirectory.

Il codice esegue l'iterazione di tutti i file nel pacchetto di contenuto e li copia nella `Documents` Directory, in una sottocartella denominata per il `ProductIdentifier` . Infine chiama `CompleteTransaction` , che chiama `FinishTransaction` per rimuovere la transazione dalla coda di pagamento.

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

Quando `FinishTransaction` viene chiamato il metodo, non è più garantito che i file scaricati si trovino nella `Cache` Directory. Tutti i file devono essere copiati prima di chiamare `FinishTransaction` .

## <a name="other-considerations"></a>Altre considerazioni

Il codice di esempio precedente illustra un'implementazione piuttosto semplice dell'acquisto di contenuto ospitato. È necessario considerare alcuni punti aggiuntivi:

### <a name="detecting-updated-content"></a>Rilevamento del contenuto aggiornato

Sebbene sia possibile aggiornare i pacchetti di contenuto ospitati, Store Kit non fornisce alcun meccanismo per eseguire il push di questi aggiornamenti agli utenti che hanno già scaricato e acquistato il prodotto. Per implementare questa funzionalità, il codice può controllare la nuova `SKProduct.ContentVersion` Proprietà (se `SKProduct` è `Downloadable` ) regolarmente e rilevare se il valore viene incrementato. In alternativa, è possibile creare un sistema di notifica push.

### <a name="installing-updated-content-versions"></a>Installazione delle versioni di contenuto aggiornate

Il codice di esempio precedente ignora la copia dei file se il file esiste già. NON è consigliabile se si desidera supportare versioni più recenti del contenuto scaricato.

In alternativa, è possibile copiare il contenuto in una cartella denominata per la versione e tenere traccia di quale è la versione corrente (ad esempio, in `NSUserDefaults` o in qualsiasi punto in cui si archiviano i record di acquisto completati.

### <a name="restoring-transactions"></a>Ripristino di transazioni

Quando `SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions` viene chiamato, Store Kit restituisce tutte le transazioni precedenti per l'utente. Se hanno acquistato un numero elevato di elementi o se ogni acquisto include pacchetti di contenuto di grandi dimensioni, il ripristino può comportare una grande quantità di traffico di rete, perché tutto viene accodato per il download in una sola volta.

Valutare se un prodotto è stato acquistato separatamente rispetto al download effettivo del pacchetto di contenuto associato.

### <a name="pausing-restarting-and-canceling-downloads"></a>Sospensione, riavvio e annullamento di download

Sebbene il codice di esempio non illustri questa funzionalità, è possibile sospendere e riavviare i download del contenuto ospitato. `SKPaymentQueue.DefaultQueue`Dispone di metodi per `PauseDownloads` , `ResumeDownloads` e `CancelDownloads` .

Se il codice chiama `FinishTransaction` nella coda di pagamento prima del download, il `Finished` download viene annullato automaticamente.

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>Impostazione del flag SKIP-backup sul contenuto scaricato

Le linee guida per il backup iCloud di Apple indicano che *non* è possibile eseguire il backup del contenuto non utente che viene facilmente ripristinato da un server, perché inutilmente utilizzerebbe l'archiviazione iCloud. Per ulteriori informazioni sull'impostazione dell'attributo backup, vedere la documentazione [file System](~/ios/app-fundamentals/file-system.md) .

## <a name="summary"></a>Summary

Questo articolo ha introdotto due nuove funzionalità di Store Kit in iOS6: acquisto di iTunes e altro contenuto dall'app e utilizzo del server Apple per ospitare i propri acquisti in-app. Questa introduzione deve essere letta insieme alla [documentazione di acquisto in-app](~/ios/platform/in-app-purchasing/index.md) esistente per la copertura completa dell'implementazione della funzionalità del kit di archiviazione.

## <a name="related-links"></a>Collegamenti correlati

- [StoreKit (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit)
- [Acquisti in-app](~/ios/platform/in-app-purchasing/index.md)
- [Guida di riferimento a StoreKit Framework](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [Riferimento alla classe SKStoreProductViewController](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [Video di WWDC: vendita di prodotti con Store Kit](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
