---
title: Panoramica di StoreKit e durante il recupero delle informazioni sul prodotto in xamarin. IOS
description: Questo documento viene fornita una panoramica di StoreKit. Descrive le classi usate con StoreKit, test StoreKit interazioni, visualizzazione dei prodotti per la vendita, la gestione di prodotti non è validi e prezzi localizzati vengono visualizzati.
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 0dcda2e4fd1ca7773668a0a6fdf46e01f2f0841d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61366966"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>Panoramica di StoreKit e durante il recupero delle informazioni sul prodotto in xamarin. IOS

L'interfaccia utente per un acquisto in-app è illustrato nelle schermate seguenti.
Prima di qualsiasi transazione viene eseguita, l'applicazione deve recuperare prezzo del prodotto e la descrizione per la visualizzazione. Quindi quando l'utente preme **acquistare**, l'applicazione effettua una richiesta di StoreKit che gestisce la finestra di dialogo di conferma e account di accesso ID Apple. Supponendo che la transazione ha esito positivo, StoreKit notifica al codice dell'applicazione, che devono archiviare il risultato della transazione e fornire all'utente l'accesso per l'acquisto.   

   
 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "Il codice dell'applicazione, che deve archiviare il risultato della transazione e fornire all'utente l'accesso per l'acquisto di notifica StoreKit")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>Classi

Implementazione di acquisti in-app richiede le seguenti classi dal framework di StoreKit:   
   
 **SKProductsRequest** : una richiesta di StoreKit per prodotti approvati per la vendita (App Store). Può essere configurato con un numero di ID di prodotto.

-   **SKProductsRequestDelegate** : dichiara i metodi per gestire le richieste di prodotti e le risposte. 
-   **SKProductsResponse** – inviati nuovamente al delegato dal StoreKit (App Store). Contiene il SKProducts che corrisponde al prodotto ID inviato con la richiesta. 
-   **SKProduct** – un prodotto recuperata da StoreKit (che è stata configurata in iTunes Connect). Contiene informazioni sul prodotto, ad esempio ID prodotto, titolo, descrizione e prezzo. 
-   **SKPayment** – creato con un ID prodotto e aggiunti alla coda di pagamento per eseguire un acquisto. 
-   **SKPaymentQueue** : richieste di pagamento in coda per essere inviata ad Apple. Le notifiche vengono attivate in seguito a ogni pagamenti in fase di elaborazione. 
-   **SKPaymentTransaction** – rappresenta una transazione completata (una richiesta di acquisto che è stata elaborata per l'App Store e inviata all'applicazione tramite StoreKit). La transazione può essere acquistato, ripristinata o Failed. 
-   **SKPaymentTransactionObserver** – sottoclasse personalizzata che risponde a eventi generati dalla coda di pagamento di StoreKit. 
-   **Le operazioni di StoreKit sono asincrone** : dopo un SKProductRequest è avviato o un SKPayment viene aggiunto alla coda, il controllo venga restituito al codice. StoreKit chiamerà i metodi nella sottoclasse SKProductsRequestDelegate o SKPaymentTransactionObserver quando riceve i dati dai server Apple. 


Il diagramma seguente mostra le relazioni tra le varie classi di StoreKit (classi astratte devono essere implementate nell'applicazione):   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "Le relazioni tra le varie classi astratte le classi di StoreKit devono essere implementate nell'app")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   
   
   
   
 Queste classi sono illustrate in dettaglio più avanti in questo documento.

## <a name="testing"></a>Test

La maggior parte delle operazioni di StoreKit richiedono un dispositivo reale per i test. Recupero di informazioni sul prodotto (ad esempio, prezzo &amp; descrizione) funzioneranno nel simulatore ma acquisto e operazioni di ripristino verranno restituito un errore (ad esempio codice FailedTransaction 5002 = si è verificato un errore sconosciuto).

Nota: StoreKit non venga eseguita nel simulatore iOS. Quando si esegue l'applicazione nel simulatore iOS, StoreKit registra un avviso se l'applicazione tenta di recuperare la coda di pagamento. Test nell'archivio devono essere eseguite sui dispositivi effettivi.   
   
   
   
 Importante: Non accedere con l'account di test nell'applicazione impostazioni. È possibile usare l'applicazione delle impostazioni di accesso all'esterno di qualsiasi account ID Apple esistente, quindi è necessario attendere che venga richiesta *all'interno della sequenza di acquisto In-App* accedere usando un test di ID Apple.   
   
   
   

Se si tenta di accedere all'archivio reale con un account di test, si verranno convertito automaticamente per un reale ID Apple. Tale account non è più sarà facile da usare per il test.

Per testare il codice di StoreKit devi disconnessione dell'account di test di iTunes regolari e account di accesso con un account di test speciali (creato in iTunes Connect) collegata all'archivio del test. Per disconnettersi la visita di account attuale **Impostazioni > iTunes App Store e** come illustrato di seguito:

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "Per disconnettersi da visita l'account corrente iTunes impostazioni e App Store")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)
 
quindi accedere con un account di prova *quando richiesto da StoreKit all'interno dell'app*:



Per creare utenti di test in iTunes Connect fare clic su **utenti e ruoli** nella pagina principale.

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "Per creare gli utenti di test in iTunes Connect fare clic su utenti e ruoli nella pagina principale")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

Selezionare **tester Sandbox**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "Se si seleziona i tester di Sandbox")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

Viene visualizzato l'elenco di utenti esistenti. È possibile aggiungere un nuovo utente o eliminare un record esistente. Il portale non vengono (attualmente) consentono di visualizzazione o modifica esistente test users, pertanto è consigliabile mantenere un buona record di ogni utente di test che viene creato (in particolare la password è assegnare). Se si elimina un utente test di indirizzo di posta elettronica non può essere riutilizzato per un altro account di test.  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "Viene visualizzato l'elenco di utenti esistenti")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 Nuovi utenti di test hanno attributi simili a un ID Apple reale (ad esempio nome, password, domanda e risposta). Tenere traccia di tutti i dettagli immessi qui. Il **Select iTunes Store** campo determinerà la valuta e linguaggio acquisti in-app userà quando connesso aggiuntivo come tale utente.

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "Il campo selezionare iTunes Store è determineranno valuta dell'utente e la lingua per gli acquisti in-app")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>Durante il recupero delle informazioni sul prodotto

Viene visualizzato il primo passaggio nella vendita di un prodotto di acquisto in-app: recuperare il prezzo corrente e la descrizione da Store di App per la visualizzazione.   
   
   
   
 Indipendentemente dal tipo di prodotti di un'app vende (che può essere utilizzato, Non riproducibile o un tipo di sottoscrizione), il processo di recupero di informazioni sul prodotto per la visualizzazione è lo stesso. Il codice InAppPurchaseSample che accompagna questo articolo contiene un progetto denominato *sussidiarie* che illustra come recuperare le informazioni di produzione per la visualizzazione. Viene illustrato come:

-  Creare un'implementazione di `SKProductsRequestDelegate` e implementare il `ReceivedResponse` metodo astratto. L'esempio di codice viene chiamato il `InAppPurchaseManager` classe. 
-  Verificare con StoreKit per vedere se sono consentiti i pagamenti (tramite `SKPaymentQueue.CanMakePayments` ). 
-  Creare un'istanza di un `SKProductsRequest` con gli ID prodotto che sono state definite in iTunes Connect. Questa operazione viene eseguita dell'esempio `InAppPurchaseManager.RequestProductData` (metodo). 
-  Chiamare il metodo Start sul `SKProductsRequest` . In questo modo viene attivata una chiamata asincrona al server di App Store. Il delegato ( `InAppPurchaseManager` ) sarà chiamato-back con i risultati. 
-  Il delegato ( `InAppPurchaseManager` ) `ReceivedResponse` metodo aggiorna l'interfaccia utente con i dati restituiti da Store di App (prezzi dei prodotti & descrizioni o i messaggi sui prodotti non validi). 

L'interazione generale si presenta come segue ( **StoreKit** è incorporato in iOS e il **Store App** rappresenta i server Apple):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "Grafico durante il recupero di informazioni sul prodotto")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>Visualizzazione di esempio di informazioni di prodotto

Il [InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) *sussidiarie* codice di esempio viene illustrato come è possibile recuperare le informazioni sul prodotto. La schermata principale dell'esempio vengono visualizzate informazioni per due prodotti recuperati da Store di App:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "La schermata principale Visualizza prodotti informazioni recuperati da Store di App")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   
   
   
   
 Il codice di esempio per recuperare e visualizzare le informazioni sul prodotto viene spiegato in dettaglio più avanti.


#### <a name="viewcontroller-methods"></a>Metodi di ViewController

Il `ConsumableViewController` classe gestiranno la visualizzazione dei prezzi per i due prodotti il cui ID di prodotto sono hardcoded nella classe.

```csharp
public static string Buy5ProductId = "com.xamarin.storekit.testing.consume5credits",
   Buy10ProductId = "com.xamarin.storekit.testing.consume10credits";
List<string> products;
InAppPurchaseManager iap;
public ConsumableViewController () : base()
{
   // two products for sale on this page
   products = new List<string>() {Buy5ProductId, Buy10ProductId};
   iap = new InAppPurchaseManager();
}
```

Con la classe di livello deve inoltre essere presente un NSObject dichiarato che verrà usato per il programma di installazione una `NSNotificationCenter` observer:

```csharp
NSObject priceObserver;
```

Nel metodo ViewWillAppear l'osservatore è creato e assegnato con il centro di notifica predefinito:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

   
   
 Alla fine del `ViewWillAppear` metodo, chiamare il `RequestProductData` metodo per avviare la richiesta di StoreKit. Dopo questa richiesta è stata effettuata, StoreKit in modo asincrono contatterà il server di Apple per ottenere le informazioni e sottoporla all'app. A questo scopo, il `SKProductsRequestDelegate` sottoclasse ( `InAppPurchaseManager`) che viene descritto nella sezione successiva.

```csharp
iap.RequestProductData(products);
```

Il codice per visualizzare il prezzo e la descrizione semplicemente recupera le informazioni dal SKProduct e assegnarlo ai controlli UIKit (si noti che viene visualizzato il `LocalizedTitle` e `LocalizedDescription` – StoreKit risolve automaticamente il testo corretto e prezzi in base alla impostazioni dell'account dell'utente). Il codice seguente fa parte della notifica creati in precedenza:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
   var info = notification.UserInfo;
   if (info.ContainsKey(NSBuy5ProductId)) {
       var product = (SKProduct) info.ObjectForKey(NSBuy5ProductId);
       buy5Button.Enabled = true;
       buy5Title.Text = product.LocalizedTitle;
       buy5Description.Text = product.LocalizedDescription;
       buy5Button.SetTitle("Buy " + product.Price, UIControlState.Normal); // price display should be localized
   }
}
```

Infine, il `ViewWillDisappear` metodo dovrebbe verificare che l'osservatore è stato rimosso:

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>Metodi SKProductRequestDelegate (InAppPurchaseManager)

Il `RequestProductData` metodo viene chiamato quando l'applicazione desidera recuperare i prezzi del prodotto e altre informazioni. Analizza la raccolta di ID prodotto nel tipo di dati corretto, quindi crea un `SKProductsRequest` con tali informazioni. Chiamata del metodo Start fa sì che una richiesta di rete da apportare ai server di Apple. La richiesta verrà eseguita in modo asincrono e chiamare il `ReceivedResponse` metodo del delegato quando questo viene completato correttamente.

```csharp
public void RequestProductData (List<string> productIds)
{
   var array = new NSString[productIds.Count];
   for (var i = 0; i < productIds.Count; i++) {
       array[i] = new NSString(productIds[i]);
   }
   NSSet productIdentifiers = NSSet.MakeNSObjectSet<NSString>(array);
   productsRequest = new SKProductsRequest(productIdentifiers);
   productsRequest.Delegate = this; // for SKProductsRequestDelegate.ReceivedResponse
   productsRequest.Start();
}
```

iOS automaticamente instradare la richiesta alla versione 'sandbox' o 'production' di Store l'App in base alla quale viene eseguita l'applicazione con: il profilo di provisioning in modo che durante lo sviluppo o test dell'app la richiesta avrà accesso a tutti i prodotti configurata in iTunes Connect (anche quelli non ancora inviati o approvati da Apple). Quando l'applicazione è in fase di produzione, le richieste di StoreKit verranno restituite solo le informazioni per **Approved** prodotti.   
   
   
   
 Il `ReceivedResponse` metodo sottoposto a override viene chiamato dopo che i server Apple hanno risposto con i dati. Poiché si tratta in background, il codice dovrebbe analizzare i dati validi e usare una notifica per inviare le informazioni sul prodotto a qualsiasi ViewControllers ' in ascolto ' per la notifica. Seguito è riportato il codice per raccogliere informazioni sul prodotto valido e inviare una notifica:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   SKProduct[] products = response.Products;
   NSDictionary userInfo = null;
   if (products.Length > 0) {
       NSObject[] productIdsArray = new NSObject[response.Products.Length];
       NSObject[] productsArray = new NSObject[response.Products.Length];
       for (int i = 0; i < response.Products.Length; i++) {
           productIdsArray[i] = new NSString(response.Products[i].ProductIdentifier);
           productsArray[i] = response.Products[i];
       }
       userInfo = NSDictionary.FromObjectsAndKeys (productsArray, productIdsArray);
   }
   NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerProductsFetchedNotification, this, userInfo);
}
```

Sebbene non illustrato nel diagramma, il `RequestFailed` metodo deve anche essere sottoposto a override in modo che è possibile fornire un feedback all'utente nel caso in cui il server di App Store non sono raggiungibili (o si verifica un altro errore). L'esempio di codice scrive semplicemente alla console, ma un'applicazione reale è possibile scegliere di eseguire una query da `error.Code` proprietà e implementare un comportamento personalizzato (ad esempio, un avviso all'utente).

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

Questa schermata mostra l'applicazione di esempio subito dopo il caricamento (quando è disponibile alcuna informazione di prodotto):

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "L'app di esempio subito dopo il caricamento quando è disponibile alcuna informazione di prodotto")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>Prodotti non è validi

Un `SKProductsRequest` potrebbero restituire anche un elenco di ID prodotto non è valido. Prodotti non validi vengono restituiti in genere a causa di uno dei seguenti:   
   
   
   
 **ID prodotto è stato digitato correttamente** – vengono accettati solo gli ID prodotto valido.   
   
 **Prodotto non è stato approvato** : durante il test, tutti i prodotti che non sono selezionati per la vendita devono essere restituiti da un `SKProductsRequest`; ma nell'ambiente di produzione vengono restituiti solo i prodotti approvato.   
   
 **ID App non è esplicito** – Wildcard App IDs (con un asterisco non consentono acquisti in-app.   
   
 **Il profilo di provisioning corretto** : se si apportano modifiche alla configurazione dell'applicazione nel portale di provisioning (ad esempio l'abilitazione di acquisti in-app), ricordare di generare di nuovo e utilizzare il profilo di provisioning corretto quando si compila l'app.   
   
 **contratto di applicazioni a pagamento di iOS non è sul posto** : funzionalità di StoreKit non funzionerà affatto a meno che non è presente un contratto valido per l'Account per sviluppatore Apple.   
   
 **Il file binario è stato rifiutato** : se è presente un file binario inviato in precedenza nello stato rifiutato (o dal team di App Store, dallo sviluppatore) le funzionalità di StoreKit non funzionerà.

Il `ReceivedResponse` i prodotti non è validi per la console di output del metodo nel codice di esempio:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>Visualizzare i prezzi localizzati

I livelli di prezzo specifica di un prezzo specifico per ogni prodotto in tutti gli archivi App internazionale. Per garantire i prezzi vengono visualizzati correttamente per ogni tipo di valuta, usare il metodo di estensione seguente (definito in `SKProductExtension.cs`) anziché la proprietà di prezzo della ognuno `SKProduct`:

```csharp
public static class SKProductExtension {
   public static string LocalizedPrice (this SKProduct product)
   {
       var formatter = new NSNumberFormatter ();
       formatter.FormatterBehavior = NSNumberFormatterBehavior.Version_10_4;  
       formatter.NumberStyle = NSNumberFormatterStyle.Currency;
       formatter.Locale = product.PriceLocale;
       var formattedString = formatter.StringFromNumber(product.Price);
       return formattedString;
   }
}
```

Il codice che imposta il titolo del pulsante Usa il metodo di estensione simile al seguente:

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

Utilizzo di due account di test di iTunes diversa (uno per l'archivio American) e uno per l'archivio giapponese comporta negli screenshot seguenti:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "Due iTunes diversi test che mostra linguaggio risultati specifici dell'account")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   
   
   
   
 Si noti che l'archivio influisce sulla lingua che viene usata per la valuta di informazioni e il prezzo del prodotto, mentre l'impostazione della lingua del dispositivo interessa le etichette e altri contenuti localizzati.   
   
   
   
 È importante ricordare che per usare un archivio diverso testano account è necessario **Sign Out** nel **Impostazioni > iTunes App Store e** e avviare nuovamente l'applicazione di effettuare l'accesso con un account diverso. Per modificare la lingua del dispositivo, passare a **Impostazioni > generali > International > lingua**.

