---
title: Modifiche apportate a StoreKit
description: "iOS 6 introduce due modifiche all'archivio Kit API: la possibilità di visualizzare iTunes (e App Store/iBookstore) prodotti all'interno dell'app e una nuova in-app acquistano in Apple ospiterà i file scaricabili. Questo documento illustra come implementare tali funzionalità con xamarin. IOS."
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 8a7a70c3f84518141cf44d630fb4137051d0c866
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="changes-to-storekit"></a>Modifiche apportate a StoreKit

_iOS 6 introduce due modifiche all'archivio Kit API: la possibilità di visualizzare iTunes (e App Store/iBookstore) prodotti all'interno dell'app e una nuova in-app acquistano in Apple ospiterà i file scaricabili. Questo documento illustra come implementare tali funzionalità con xamarin. IOS._

Le modifiche principali al Kit di archivio in iOS6 sono queste due nuove funzionalità:

-   **Visualizzazione di contenuto in App & Purchasing** : gli utenti possono acquistare e scaricare App, musica, documentazione e altre iTunes contenuto senza chiudere l'app. È anche possibile collegare alle App per promuovere l'acquisto o semplicemente incoraggiare verifiche e valutazioni. 
-   **Contenuto ospitato acquisti in-App** – Apple verrà archiviata e recapitare il contenuto associato ai prodotti di acquisto in-app, che elimina la necessità di un server separato ospitare i file, automaticamente supporta il download in background e consente di scrivere meno codice. 


È consigliabile che il documento venga letto in combinazione con la xamarin esistente [acquisto In-App](~/ios/platform/in-app-purchasing/index.md) documentazione.

## <a name="requirements"></a>Requisiti

Le funzionalità di archivio Kit illustrate in questo documento richiedono iOS 6 e 4.5 Xcode e xamarin. IOS 6.0.


## <a name="in-app-content-display--purchasing"></a>La visualizzazione del contenuto in-App e acquisti

La nuova funzionalità di acquisto in-app in iOS consente agli utenti di visualizzare informazioni sul prodotto e acquistare o scaricare il prodotto all'interno dell'app.
In precedenza applicazioni sarebbe necessario attivare iTunes App Store o iBookstore che causerebbe l'utente e l'applicazione originale. Questa nuova funzionalità restituisce automaticamente l'utente all'applicazione quando vengono eseguite.

 [![](changes-to-storekit-images/image1.png "Restituzione automaticamente a un'app dopo l'acquisto")](changes-to-storekit-images/image1.png#lightbox)

Esistono diversi scenari in cui potrebbe essere utile, tra cui (ma non è limitato a):

-   **Incoraggiando agli utenti di valutare l'app** : È possibile aprire la pagina di App Store in modo che l'utente può valutare ed esaminare l'app senza lasciare. 
-   **La promozione tra app** : consentono all'utente di vedere altre App che si pubblica, con la possibilità di acquistare e scaricare immediatamente. 
-   **Consentire agli utenti di trovare e scaricare il contenuto** : consentire agli utenti di acquistare i contenuti dell'app trova, gestisce o aggrega (ad es. un'app di musica potrebbe fornire una playlist di brani e consentire a ogni canzone a essere acquistati all'interno dell'app). 


Una volta il `SKStoreProductViewController` è stato visualizzato l'utente può interagire con le informazioni sul prodotto, come se fossero in iTunes App Store o il iBookstore. vale a dire:

-  Visualizzazione delle schermate (per App)
-  Brani campionamento o video (per la musica, TV e film),
-  Revisioni (lettura e scrittura),
-  Acquisto di & download, situazione che si verifica completamente all'interno di controller di visualizzazione e il Kit di archivio. Non è necessario fornire codice aggiuntivo per il funzionamento di questa applicazione. 


Tenere presente che alcune opzioni all'interno di `SKStoreProductViewController` vengono comunque forza l'utente per lasciare l'applicazione e aprire l'app store pertinente, ad esempio facendo clic su **prodotti correlati** o un'app **supporto** collegamento.

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

L'API per mostrare un prodotto in qualsiasi app è molto semplice: è necessario solo creare e visualizzare un `SKStoreProductViewController`. Seguire questi passaggi per creare e visualizzare un prodotto:

1.  Creare un `StoreProductParameters` oggetto per passare i parametri per il controller di visualizzazione, inclusi il `productId` nel costruttore. 
1.  Creare un'istanza di `SKProductViewController` . Assegnarlo a un campo di livello di classe. 
1.  Assegnare un gestore per il controller di visualizzazione `Finished` evento, che deve ignorare il controller di visualizzazione. Questo evento viene chiamato quando l'utente preme Annulla; o in caso contrario completa una transazione all'interno del controller di visualizzazione. 
1.  Chiamare il `LoadProduct` metodo passando la `StoreProductParameters` e un gestore di completamento. Il gestore di completamento deve verificare che la richiesta del prodotto è stato correttamente e in tal caso, presentare il `SKProductViewController` come form modale. Gestione appropriata degli errori devono essere aggiunti nel caso in cui il prodotto non può essere recuperato. 

### <a name="example"></a>Esempio

Il *ProductView* nel progetto il *StoreKit* codice di esempio per questo articolo implementa un `Buy` metodo che accetta qualsiasi prodotto dell'ID Apple e visualizza il `SKStoreProductViewController`. Il codice seguente consente di visualizzare le informazioni sul prodotto per qualsiasi ID Apple specificato:

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

L'app è simile al seguente quando si esegue – scaricare o acquistare si verifica interamente all'interno di `SKStoreProductViewController`:

 [![](changes-to-storekit-images/image2.png "L'app è simile al seguente quando si esegue")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>Supporto di sistemi operativi precedenti

L'applicazione di esempio include il codice viene illustrato come aprire App Store iTunes o la iBookstore nelle versioni precedenti di iOS. Utilizzare il `OpenUrl` per aprire un correttamente predisposto **itunes.com** URL.

È possibile implementare un controllo della versione per determinare quale codice da eseguire, simile al seguente:

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

Gli sviluppatori informazioni Kit archivio nel portale per sviluppatori di Apple visualizzeranno un protocollo: [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) : indicato in relazione a questa nuova funzionalità. Il protocollo delegato ha un solo metodo – productViewControllerDidFinish – cui è stato esposto come il `Finished` evento il `SKStoreProductViewController` in xamarin. IOS.


## <a name="determining-apple-ids"></a>Determinazione degli ID Apple

L'ID Apple necessari per il `SKStoreProductViewController` è un *numero* (da non confondere con gli ID Bundle come "com.xamarin.mwc2012"). Esistono alcuni modi diversi, che è possibile individuare l'ID Apple per i prodotti che si desiderano visualizzare, elencati di seguito:

### <a name="itunesconnect"></a>iTunesConnect

Per le applicazioni che si esegue la pubblicazione, è facile trovare il **ID Apple** in iTunes Connect:

 [![](changes-to-storekit-images/image3.png "Trovare l'ID Apple in iTunes Connect")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />


### <a name="search-api"></a>API di ricerca

Apple fornisce un'API di ricerca dinamica per eseguire una query tutti i prodotti in App Store iTunes e il iBookstore. Informazioni su come accedere alle API di ricerca sono reperibile [risorse di applicazioni Affiliate di Apple](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html), anche se l'API viene esposta a tutti gli utenti (non appena registrati sue consociate). Il risultato JSON può essere analizzato per individuare il `trackId` che rappresenta l'ID Apple da usare con `SKStoreProductViewController`.

I risultati includeranno anche altri metadati, tra cui URL di disegno che può essere usato per eseguire il rendering del prodotto nell'app e informazioni di visualizzazione.

Ecco alcuni esempi:

-   **app iBooks*- [http://itunes.apple.com/search?term=ibooks&amp;entità = software&amp;country = us](http://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us) 
-   **Punto e il iBook che salta*- [http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entità = ebook&amp;country = us](http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us) 


### <a name="enterprise-partner-feed"></a>Organizzazione Partner Feed

Apple fornisce approvati partner con un dump completo dei dati di tutti i loro prodotti, sotto forma di file flat database ready scaricabili. Che soddisfano determinati requisiti per l'accesso al [Enterprise Partner Feed](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-enterprise-partner-feed.html) quindi l'ID Apple per qualsiasi prodotto è reperibile nel set di dati.

Si noti che molti utenti di Feed di Partner dell'organizzazione sono membri del [programma affiliata](http://www.apple.com/itunes/affiliates) che consente le commissioni essere ottenuto su vendite prodotto. `SKStoreProductViewController` non supporta gli ID di applicazioni Affiliate (al momento della scrittura; ciò può essere aggiunto da Apple in futuro).

### <a name="direct-product-links"></a>Prodotto collegamenti diretti

L'ID Apple per un prodotto può essere dedotto dal relativo collegamento URL anteprima iTunes.
In qualsiasi iTunes collegamento prodotto (per le app, musica o documentazione) individuare la parte dell'URL inizi con `id` , utilizzare il numero che segue.

Ad esempio, il collegamento diretto all'iBooks è

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

e l'ID Apple è **364709193**. In modo analogo per l'app MWC2012 è il collegamento diretto

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

e l'ID Apple è **496963922**.

## <a name="in-app-purchase-hosted-content"></a>Il contenuto ospitato acquisto in-App

Se gli acquisti in-app sono costituiti da contenuto scaricabile (ad esempio documentazione o altri supporti, gioco locandina del livello e configurazione o altri file di grandi dimensioni) quindi questi file deve essere ospitato nel server web e App devono incorporare il codice per scaricarli in modo sicuro dopo acquisto. In iOS 6 Apple ha introdotto un'opzione in cui che ospiterà i file server, eliminando la necessità di un server separato. La funzionalità è disponibile solo per i prodotti Non riproducibile (non può essere utilizzato o sottoscrizioni). Vantaggi dell'utilizzo di servizio di hosting di Apple:

-  Ridurre i costi di larghezza di banda e di hosting.
-  Probabilmente più scalabile rispetto a qualsiasi host del server attualmente in uso. 
-  Minore di codice da scrivere, poiché non è necessario creare alcuna elaborazione sul lato server. 
-  Il download in background è stato implementato.


Nota: il test ospitato il contenuto di acquisto in-app in iOS che simulatore non è supportato, pertanto è necessario testare con un dispositivo reale.

### <a name="hosted-content-basics"></a>Nozioni di base contenuti ospitati

Prima di iOS 6, si sono verificati due modi per fornire un prodotto (descritto in dettaglio in [acquisto In-App di Xamarin](~/ios/platform/in-app-purchasing/index.md) documentazione):

-   **Prodotti incorporati** – funzionalità che sono 'sbloccati' con l'acquisto, ma che sono incorporate all'applicazione (come codice o le risorse incorporate). Sono esempi di prodotti predefiniti sbloccato foto viene applicato il filtro o gioco power-gruppo di continuità. 
-   **I prodotti server-recapitati** – dopo l'acquisto, l'applicazione deve scaricare contenuto da un server in cui si opera. Questo contenuto viene scaricato durante l'acquisto, archiviato nel dispositivo e quindi eseguito il rendering come parte della specifica del prodotto. Sono esempi di documentazione, problemi di rivista o a livelli giochi costituiti da file di configurazione e di art di sfondo. 


In iOS 6 Apple offre una variazione dei prodotti recapitati al server: ospiterà i file di contenuto nei relativi server. Questo rende molto più semplice compilare prodotti recapitati al server perché non è necessario utilizzare un server separato e Store Kit fornisce funzionalità di download in background che in precedenza era necessario scrivere manualmente. Per sfruttare i vantaggi dell'hosting di Apple, abilitare l'hosting del contenuto per i nuovi prodotti di acquisto in-app e modificare il codice Store Kit per sfruttare i vantaggi. I file di contenuto di prodotto vengono compilati con Xcode e caricati nei server di Apple per la revisione e versione.

 [![](changes-to-storekit-images/image4.png "Il processo di compilazione e recapito")](changes-to-storekit-images/image4.png#lightbox)

Uso dell'archivio di App per fornire l'acquisto in-app *con il contenuto ospitato* richiede la configurazione seguente:

-   **iTunes Connect** – è *deve* fornite le informazioni di servizi bancari e fiscali ad Apple in modo possibile rimettere i fondi raccolti per conto dell'utente. È quindi possibile configurare i prodotti per vendita e configurare gli account utente di sandbox per testare l'acquisto.  *È inoltre necessario configurare contenuto ospitato**per tali prodotti non riproducibili che si desiderano ospitare con Apple* *.* 
-   **Portale di Provisioning iOS** : creazione di un identificatore di raggruppamento e l'abilitazione dell'accesso App Store per l'app, come si farebbe per qualsiasi applicazione che supporta l'acquisto in-app. 
-   **Archiviare Kit** : aggiunta di codice per l'app per la visualizzazione di prodotti, acquisto di prodotti e il ripristino delle transazioni.  *IOS 6 Store Kit verrà inoltre di gestire il download del contenuto del prodotto, in background, con gli aggiornamenti di stato di avanzamento.* 
-   **Codice personalizzato** : per rilevare gli acquisti effettuati dai clienti e fornire i prodotti o servizi vengono acquistate. Utilizzare le classi di Kit di archivio nuova iOS 6 come `SKDownload` per recuperare il contenuto ospitato da Apple. 


Le sezioni seguenti illustrano come implementare contenuto ospitato, creazione e caricamento del pacchetto per la gestione di acquisto e il download di processo, utilizzare il codice di esempio per questo articolo.


### <a name="sample-code"></a>Codice di esempio

Il progetto di esempio *HostedNonConsumables* (in StoreKitiOS6.zip) viene illustrato come compilare un'applicazione che utilizza ospitato il contenuto. L'applicazione offre due "capitoli" per la vendita, il contenuto per cui è ospitato in server di Apple. Il contenuto è costituito da un file di testo e un'immagine, anche se molto più complesso contenuto può essere utilizzato in un'applicazione reale.

Prima, durante e dopo un acquisto, l'app è simile al seguente:

 [![](changes-to-storekit-images/image5.png "L'app è simile al seguente prima, durante e dopo un acquisto")](changes-to-storekit-images/image5.png#lightbox)

Il file di testo e immagine vengono scaricati e copiati nella cartella documenti dell'applicazione. Vedere il [funziona con la documentazione di File System](~/ios/app-fundamentals/file-system.md) per ulteriori informazioni sulle diverse directory disponibili per l'archiviazione di applicazioni.

## <a name="itunes-connect"></a>iTunes Connect

Quando la creazione di nuovi prodotti che utilizzeranno Apple del contenuto di hosting assicurarsi di selezionare il **Non riproducibile** tipo di prodotto. Altri tipi di prodotto non supportano l'hosting del contenuto. Inoltre, non è necessario abilitare l'hosting del contenuto per *esistente* prodotti venduti; attivare solo hosting del contenuto per i nuovi prodotti.

 [![](changes-to-storekit-images/image6.png "Selezionare il tipo di prodotto Non riproducibile")](changes-to-storekit-images/image6.png#lightbox)

Immettere un **ID prodotto**. Questo sarà necessario in un secondo momento quando si crea il contenuto per questo prodotto.

 [![](changes-to-storekit-images/image7.png "Immettere un ID prodotto")](changes-to-storekit-images/image7.png#lightbox)

Hosting del contenuto è impostata nella sezione dei dettagli. Prima di acquisto in-app passare in tempo reale, è sufficiente deselezionare la casella di controllo "Host contenuto con Apple" Se si desidera annullare (anche se è stato caricato del contenuto di prova). Tuttavia hosting del contenuto non può essere rimosso dopo l'acquisto in-app è stata rilasciata.

 [![](changes-to-storekit-images/image8.png "Ospitare il contenuto di Apple")](changes-to-storekit-images/image8.png#lightbox)

Dopo avere attivato ospitare il contenuto, il prodotto verrà immesso **in attesa di caricamento** lo stato e visualizzare più questo messaggio:

 [![](changes-to-storekit-images/image9.png "Il prodotto verrà immettere in attesa per lo stato di caricamento e visualizzare più questo messaggio")](changes-to-storekit-images/image9.png#lightbox)

Il contenuto deve ora essere creato con Xcode e caricati utilizzando lo strumento di archiviazione. Istruzioni per la creazione di pacchetti di contenuto viene fornito nella sezione successiva **creazione. File PKG**.

## <a name="creating-pkg-files"></a>La creazione. File PKG

I file di contenuto caricato in Apple devono soddisfare le restrizioni seguenti:

-  Non può superare i 2GB di dimensioni.
-  Non può contenere codice eseguibile (o i collegamenti simbolici che puntano all'esterno del contenuto). 
-  Deve essere formattato correttamente (incluso un file con estensione plist) e presenta un'estensione di file con estensione pkg. Questa verrà eseguita automaticamente se si seguono queste istruzioni utilizzando Xcode. 


È possibile aggiungere molti diversi file e tipi di file, a condizione che soddisfino tali restrizioni. Il contenuto viene compresso prima di recapito all'applicazione e decompressi da Kit archivio prima che il codice accede ad esso.

Dopo aver caricato un pacchetto di contenuto, può essere sostituito con contenuto più recente. Nuovo contenuto deve essere caricato e inviato per la revisione/approvazione tramite il normale processo. È necessario incrementare il `ContentVersion` campo nei pacchetti di contenuto aggiornati.

### <a name="xcode-in-app-purchase-content-projects"></a>Progetti di contenuti di acquisto In-App di Xcode

La creazione di pacchetti di contenuto per i prodotti di acquisto in-app attualmente richiede Xcode. È presente alcun OBJECTIVE-C la necessità di codice; Xcode è un nuovo tipo di progetto per i pacchetti che contiene solo i file e un plist.

L'applicazione di esempio con capitoli del libro rappresentati in vendita – ogni pacchetto di contenuto capitolo conterrà:

-  un file di testo e
-  un'immagine per rappresentare il capitolo.


Iniziare selezionando **File > Nuovo progetto** dal menu e scegliendo **contenuto di acquisto In-App**:

 [![](changes-to-storekit-images/image10.png "Scegliere il contenuto di acquisto In-App")](changes-to-storekit-images/image10.png#lightbox)

Immettere il **Product Name** e **identificatore società** in modo che il **identificatore Bundle** corrisponde la **ID prodotto** immesso in iTunes La connessione per questo prodotto.

 [![](changes-to-storekit-images/image11.png "Immettere il nome e un identificatore")](changes-to-storekit-images/image11.png#lightbox)

Ora si disporrà di uno spazio vuoto **contenuto di acquisto In-App** progetto. È possibile fare doppio clic e **Aggiungi file...** o trascinarli nel **Project Navigator**. Verificare che il **ContentVersion** corretto (deve partono 1.0, ma se si sceglie in un secondo momento aggiornare il contenuto, ricordare di incrementare il).

Questa schermata mostra Xcode con i file di contenuto inclusi nel progetto e le voci plist visibile nella finestra principale:

 [![](changes-to-storekit-images/image12.png "Questa schermata mostra Xcode con i file di contenuto inclusi nel progetto e le voci plist visibile nella finestra principale")](changes-to-storekit-images/image12.png#lightbox)

Dopo aver aggiunto tutti i file di contenuto è possibile salvare il progetto e modificarlo in un secondo momento o avviare il processo di caricamento.

## <a name="uploading-pkg-files"></a>Il caricamento. File PKG

È il modo più semplice per caricare i pacchetti di contenuto con il **Xcode archivio strumento**. Scegliere **prodotto > archivio** dal menu per iniziare:

 ![](changes-to-storekit-images/image13.png "Scegliere Archiven")

Il pacchetto di contenuto verrà quindi visualizzato nell'archivio come illustrato di seguito. Si noti che il tipo di archivio e l'icona Mostra questo è un **archivio del contenuto di acquisti In-App**. Fare clic su **convalidare...** Per controllare il pacchetto di contenuto per gli errori senza effettivamente preforming il caricamento.

 [![](changes-to-storekit-images/image14.png "Convalidare il pacchetto")](changes-to-storekit-images/image14.png#lightbox)

Account di accesso con le credenziali di connessione iTunes:

 [![](changes-to-storekit-images/image15.png "Account di accesso con le credenziali di connessione iTunes")](changes-to-storekit-images/image15.png#lightbox)

Scegliere l'applicazione corretta e acquisto in-app per associare il contenuto con:

 [![](changes-to-storekit-images/image16.png "Scegliere l'applicazione corretta e acquisto in-app per associare il contenuto con")](changes-to-storekit-images/image16.png#lightbox)

Verrà visualizzato un messaggio simile al seguente:

 ![](changes-to-storekit-images/image17.png "Un esempio alcun messaggio di problemi")

Ora passare attraverso un processo simile, ma facendo clic su **Distribuisci...** Consente di caricare effettivamente il contenuto.

 [![](changes-to-storekit-images/image18.png "Distribuire l'app")](changes-to-storekit-images/image18.png#lightbox)

Selezionare la prima opzione, per caricare il contenuto:

 ![](changes-to-storekit-images/image19.png "Caricare il contenuto")

Ripetere l'accesso:

 [![](changes-to-storekit-images/image15.png "Account di accesso")](changes-to-storekit-images/image15.png#lightbox)

Scegliere l'applicazione corretta e il record di acquisto in-app per il contenuto da caricare:

 [![](changes-to-storekit-images/image20.png "Scegliere il record di acquisto dell'applicazione e in-app")](changes-to-storekit-images/image20.png#lightbox)

Attendere mentre vengono caricati i file:

 [![](changes-to-storekit-images/image21.png "La finestra di dialogo di caricamento del contenuto")](changes-to-storekit-images/image21.png#lightbox)

Una volta completato il caricamento, verrà visualizzato un messaggio per indicare che il contenuto è stato inviato all'App Store.

 [![](changes-to-storekit-images/image22.png "Un messaggio di caricamento ha esito positivo di esempio")](changes-to-storekit-images/image22.png#lightbox)

Una volta che sia stata eseguita, quando si torna alla pagina del prodotto in iTunes Connect verrà Mostra i dettagli del pacchetto e in **pronto per l'invio** stato. Quando il prodotto è in questo stato, è possibile iniziare il test nell'ambiente sandbox. NON è necessario inviare il prodotto per il test in sandbox.

 [![](changes-to-storekit-images/image23.png "iTunes Connect verrà Mostra i dettagli del pacchetto ed essere pronto per lo stato di invio")](changes-to-storekit-images/image23.png#lightbox)

Può richiedere del tempo (ad es. alcuni minuti) tra il caricamento dell'archivio e iTunes lo stato di connessione in corso l'aggiornamento. È possibile inviare il prodotto per la revisione separatamente o inviarla insieme a un file binario dell'applicazione. Solo dopo che Apple ha approvato ufficialmente il contenuto possa essere disponibile nell'ambiente di produzione App Store per l'acquisto nell'app.

### <a name="pkg-file-format"></a>Formato di File PKG

Utilizzo di Xcode e lo strumento di archiviazione per creare e caricare un pacchetto di contenuto ospitato significa mai visualizzato il contenuto del pacchetto stesso. I file e directory nei pacchetti creati per l'app di esempio simile al seguente, con la `plist` file nella radice e i file di prodotto in un `Contents` sottodirectory:

 [![](changes-to-storekit-images/image24.png "Il file plist nella radice e i file di prodotto in una sottodirectory del contenuto")](changes-to-storekit-images/image24.png#lightbox)

Si noti la struttura di directory del pacchetto (in particolare il percorso dei file nel `Contents` sottodirectory) perché è necessario comprendere queste informazioni per estrarre i file del pacchetto nel dispositivo.

### <a name="updating-package-content"></a>Contenuto del pacchetto di aggiornamento

La procedura per l'aggiornamento del contenuto dopo che è stata approvata:

-  Modificare il contenuto di acquisto In-App progetto in Xcode.
-  Aumentarne il numero di versione.
-  Caricare nuovamente in iTunes Connect. Gli addetti agli acquisti successive avranno automaticamente la versione più recente, ma gli utenti che dispongono della versione precedente non riceverà alcuna notifica. 
-  L'app è responsabile della notifica degli utenti e incoraggiando la loro di recuperare una versione più recente del contenuto. L'app deve anche creare una funzione che consente di scaricare la nuova versione. Questa operazione deve essere eseguita utilizzando la funzionalità di ripristino del Kit di archivio. 
-  Per determinare se esiste una versione più recente è possibile compilare una funzionalità in un'applicazione per recuperare SKProducts (ad es. stesso processo utilizzato per recuperare i prezzi dei prodotti) e confrontare la proprietà ContentVersion. 

## <a name="purchasing-overview"></a>Panoramica sugli acquisti

Prima di leggere questa sezione, esaminare esistente [documentazione di acquisto In-App](~/ios/platform/in-app-purchasing/index.md).

La sequenza di eventi che si verifica quando un prodotto con contenuto ospitato viene acquistata e download è illustrato nella figura seguente:

 [![](changes-to-storekit-images/image25.png "La sequenza di eventi che si verifica quando un prodotto con contenuto ospitato viene acquistata e scaricare")](changes-to-storekit-images/image25.png#lightbox)

1.  È possibile creare nuovi prodotti in iTunes Connect con contenuto ospitato abilitato. Il contenuto effettivo viene costruito separatamente in Xcode (come semplicemente come il trascinamento di file in una cartella) e quindi archiviato e caricato in iTunes (è necessario alcun codice). Ogni prodotto viene quindi inviato per l'approvazione, dopo la quale diventa disponibile per l'acquisto. Nell'esempio di codice questi ID prodotto sono hardcoded, ma ospitare il contenuto di Apple è più flessibile, se si archivia l'elenco dei prodotti disponibili in un server remoto in modo che possono essere aggiornato quando si invia nuovi prodotti e il contenuto in iTunes Connect. 
1.  Quando l'utente acquista un prodotto, una transazione viene inserita nella coda di pagamento per l'elaborazione. 
1.  Archivio Kit inoltra la richiesta di acquisto a iTunes server per l'elaborazione. 
1.  Completamento della transazione sui server iTunes (ad es. viene addebitato cliente) e un valore di ricezione viene restituito all'app, con informazioni sul prodotto, associate inclusi sia scaricabile (e in tal caso, le dimensioni del file e altri metadati). 
1.  Il codice deve verificare se il prodotto è downloadble e in tal caso, effettuare una richiesta di download del contenuto che viene inoltre inserita nella coda del pagamento. Kit di archivio invia la richiesta ai server di iTunes. 
1.  Server restituisce i file di contenuto al Kit di archivio, che offre un callback per restituire lo stato di avanzamento di download e il tempo rimanente stime al codice. 
1.  Al termine dell'operazione, si ottengono una notifica e passato un percorso di file nella cartella della Cache. 
1.  Il codice deve copiare i file e verificare, salvare lo stato che è necessario ricordare che è stato acquistato il prodotto. Sfruttare questa opportunità per impostare il flag backup correttamente sui nuovi file (Suggerimento: se provengono da un server e non vengono mai modificati dall'utente, è consigliabile probabilmente ignorare eseguirne il backup, poiché l'utente può sempre recuperarli dal server di Apple in futuro). 
1.  Chiamare FinishTransaction. Questo è importante poiché la transazione viene rimosso dalla coda di pagamento. È anche importante che non si chiama FinishTransaction fino a dopo aver copiato il contenuto dalla directory della Cache. Dopo aver chiamato FinishTransaction, intendono genere essere eliminati rapidamente i file memorizzati nella cache. 


## <a name="implementing-hosted-content-purchase"></a>Implementazione di acquisto contenuto ospitato

Le informazioni seguenti devono essere letti in combinazione con l'intero [documentazione acquisti In-App](~/ios/platform/in-app-purchasing/index.md). Le informazioni contenute in questo documento vengono illustrate le differenze tra il contenuto ospitato e l'implementazione precedente.


### <a name="classes"></a>Classi

Le classi seguenti sono stati aggiunte o modificate per il contenuto di supporto ospitato in iOS 6:

-   **SKDownload** – nuova classe che rappresenta un download in corso. L'API consente più di un singolo prodotto, tuttavia, inizialmente solo uno è stato implementato. 
-   **SKProduct** : aggiunta di nuove proprietà: `Downloadable` , `ContentVersion` , `ContentLengths` matrice. 
-   **SKPaymentTransaction** – nuova proprietà aggiunta: `Downloads` , che contiene una raccolta di `SKDownload` oggetti se questo prodotto è ospitato il contenuto disponibile per il download. 
-   **SKPaymentQueue** – nuovo metodo aggiunto: `StartDownloads` . Chiamare questo metodo con `SKDownload` oggetti per recuperare il contenuto ospitato. Il download può verificarsi in background. 
-   **SKPaymentTransactionObserver** – nuovo metodo: `UpdateDownloads` . Archivio Kit chiama questo metodo con le informazioni di stato di avanzamento corrente operazioni di download. 


Dettagli del nuovo `SKDownload` classe:

-   **Lo stato di avanzamento** : un valore compreso tra 0 e 1 che è possibile utilizzare per visualizzare un indicatore della percentuale di completamento per l'utente. Non utilizzare lo stato di avanzamento = = 1 per rilevare se è stato completato il download, verificare lo stato di = = operazione completata. 
-   **TimeRemaining** : stima del download tempo rimanente, in secondi. -1 indica che ancora in corso il calcolo stime. 
-   **Stato** : attivo, in attesa, completato, non è riuscita, sospesa o annullata. 
-   **ContentURL** : percorso in cui il contenuto inserito nel disco in File di `Cache` directory. Popolato solo una volta completato il download. 
-   **Errore** : controllare questa proprietà se lo stato è operazione non riuscito. 


Le interazioni tra le classi nel codice di esempio vengono visualizzate in questo diagramma (il codice specifico per gli acquisti contenuti ospitati viene visualizzato in verde):

 [![](changes-to-storekit-images/image26.png "Acquisti contenuti ospitati è visualizzata in verde in questo diagramma")](changes-to-storekit-images/image26.png#lightbox)

Il codice di esempio in cui sono state utilizzate queste classi viene visualizzato nella parte restante di questa sezione:

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

Modificare esistenti `UpdatedTransactions` sostituzione consente di verificare la presenza di contenuto scaricabile e chiamare `StartDownloads` se necessario:

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

Nuovo metodo sottoposto a override `UpdatedDownloads` è illustrato di seguito. Archivio Kit chiama questo metodo dopo `StartDownloads` viene attivato `UpdatedTransactions`. Questo metodo viene chiamato *più volte* a intervalli indeterminati per fornire è con stato del download e quindi nuovamente quando ha terminato il download. Si noti che il metodo accetta una matrice di `SKDownload` oggetti, ogni chiamata al metodo è possibile ottenere lo stato di download più nella coda. Come illustrato nell'implementazione che sono gli stati di download selezionata ogni volta e l'azione appropriata.

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
            Console.WriteLine ("Cancelled"); // TODO: UI?
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

Questa classe contiene un nuovo metodo `SaveDownload` che viene chiamato dopo che ogni download viene completato correttamente.

Il contenuto ospitato è stato scaricato correttamente e decompresso nel `Cache` directory. La struttura del. File PKG richiede tutti i file da salvare un `Contents` sottodirectory, pertanto il codice seguente consente di estrarre i file dall'interno di `Contents` sottodirectory.

Il codice scorre tutti i file nel pacchetto di contenuto e li copia nel `Documents` directory, in una sottocartella denominata per il `ProductIdentifier`. Infine viene chiamato `CompleteTransaction`, che chiama `FinishTransaction` per rimuovere la transazione dalla coda di pagamento.

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

Quando `FinishTransaction` viene chiamato scaricato i file sono non necessariamente la `Cache` directory. Tutti i file devono essere copiati prima di chiamare `FinishTransaction`.


## <a name="other-considerations"></a>Altre considerazioni

Nell'esempio di codice precedente illustra un'implementazione semplice di acquisto contenuto ospitato. Esistono alcuni aspetti aggiuntivi che è necessario considerare:

### <a name="detecting-updated-content"></a>Individuazione del contenuto aggiornato

Sebbene sia possibile aggiornare i pacchetti di contenuto ospitati, Store Kit non fornisce alcun meccanismo per effettuare il push questi aggiornamenti agli utenti che hanno già scaricato e acquistato il prodotto. Per implementare questa funzionalità, il codice può verificare il nuovo `SKProduct.ContentVersion` proprietà (se il `SKProduct` è `Downloadable`), regolarmente e per rilevare se il valore viene incrementato. In alternativa è possibile creare un sistema di notifica push.

### <a name="installing-updated-content-versions"></a>L'installazione di versioni aggiornate di contenuto

Il codice di esempio sopra riportato ignora la copia dei file se il file esiste già. Questo non è consigliabile se si desidera supportare le versioni più recenti del contenuto viene scaricato.

Potrebbe essere un'alternativa a copiare il contenuto in una cartella denominata per la versione e tenere traccia di quale sia la versione corrente (ad es. in `NSUserDefaults` o ovunque si archiviano i record di acquisto completato).

### <a name="restoring-transactions"></a>Il ripristino delle transazioni

Quando `SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions` viene chiamato, Store Kit restituisce tutte le transazioni precedenti per l'utente. Se hanno acquistato un numero elevato di elementi o se ogni fornitore dispone di pacchetti di contenuto molto grandi, il ripristino potrebbe comportare una notevole quantità di traffico di rete come tutti gli elementi Ottiene messo in coda per il download in una sola volta.

È consigliabile tenere traccia dei se un prodotto è stato acquistato separatamente dal download effettivo del pacchetto di contenuto associato.

### <a name="pausing-restarting-and-canceling-downloads"></a>La sospensione, il riavvio e l'annullamento del download

Anche se il codice di esempio non viene illustrata questa funzionalità, è possibile sospendere e riavviare i download di contenuto ospitati. Il `SKPaymentQueue.DefaultQueue` dispone di metodi per `PauseDownloads`, `ResumeDownloads` e `CancelDownloads`.

Se il codice chiama `FinishTransaction` nella coda del pagamento prima il download da `Finished` quindi che il download viene annullato automaticamente.

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>Impostazione del Flag SKIP Backup al contenuto scaricato

Linee guida di Apple iCloud Backup suggerire cui contenuto non dell'utente che viene ripristinato facilmente da un server deve essere *non* essere sottoposti a backup (perché inutilmente utilizza spazio di archiviazione iCloud). Consultare il [funziona con il File System](~/ios/app-fundamentals/file-system.md) documentazione per ulteriori informazioni sull'impostazione dell'attributo di backup.

## <a name="summary"></a>Riepilogo

In questo articolo è state introdotte due nuove funzionalità di archivio Kit iOS6: acquisto in iTunes e altro contenuto all'interno dell'app e l'utilizzo di server di Apple per ospitare i propri acquisti in-app. In questa introduzione deve essere letta in combinazione con esistente [documentazione di acquisto In-App](~/ios/platform/in-app-purchasing/index.md) per informazioni complete di implementazione della funzionalità di archivio Kit.

## <a name="related-links"></a>Collegamenti correlati

- [StoreKit (sample)](https://developer.xamarin.com/samples/StoreKit/)
- [Acquisti in-app](~/ios/platform/in-app-purchasing/index.md)
- [Riferimento di Framework StoreKit](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [Riferimento alla classe SKStoreProductViewController](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [riferimento all'API di ricerca iTunes](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [Video WWDC: In vendita i prodotti con il Kit di archivio](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
