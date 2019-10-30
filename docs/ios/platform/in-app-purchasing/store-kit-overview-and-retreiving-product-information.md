---
title: Panoramica di StoreKit e recupero di informazioni sul prodotto in Novell. iOS
description: Questo documento fornisce una panoramica di StoreKit. Descrive le classi usate con StoreKit, testando le interazioni StoreKit, visualizzando i prodotti per la vendita, gestendo prodotti non validi e visualizzando i prezzi localizzati.
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 086dcb87f03ed4abbf3b82dc10add0f5698d52b3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032324"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>Panoramica di StoreKit e recupero di informazioni sul prodotto in Novell. iOS

L'interfaccia utente per un acquisto in-app è illustrata negli screenshot seguenti.
Prima che venga apportata una transazione, l'applicazione deve recuperare il prezzo e la descrizione del prodotto per la visualizzazione. Quando l'utente preme **Acquista**, l'applicazione effettua una richiesta a StoreKit che gestisce la finestra di dialogo di conferma e l'account di accesso Apple ID. Supponendo che la transazione abbia esito positivo, StoreKit notifica il codice dell'applicazione, che deve archiviare il risultato della transazione e fornire all'utente l'accesso all'acquisto.   

 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit notifies the application code, which must store the transaction result and provide the user with access to their purchase")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>Classi

L'implementazione di acquisti in-app richiede le classi seguenti dal Framework StoreKit:   
   
 **SKProductsRequest** : una richiesta di StoreKit per i prodotti approvati da vendere (App Store). Può essere configurato con un numero di ID prodotto.

- **SKProductsRequestDelegate** : dichiara i metodi per gestire le richieste e le risposte del prodotto. 
- **SKProductsResponse** : inviato di nuovo al delegato da StoreKit (App Store). Contiene SKProducts che corrispondono agli ID prodotto inviati con la richiesta. 
- **SKProduct** : prodotto recuperato da StoreKit (configurato in iTunes Connect). Contiene informazioni sul prodotto, ad esempio ID prodotto, titolo, descrizione e prezzo. 
- **SKPayment** : creato con un ID prodotto e aggiunto alla coda di pagamento per effettuare un acquisto. 
- **SKPaymentQueue** : richieste di pagamento in coda da inviare ad Apple. Le notifiche vengono attivate in seguito all'elaborazione di ogni pagamento. 
- **SKPaymentTransaction** : rappresenta una transazione completata (una richiesta di acquisto che è stata elaborata dall'App Store e restituita all'applicazione tramite StoreKit). È possibile che la transazione sia stata acquistata, ripristinata o non riuscita. 
- **SKPaymentTransactionObserver** : sottoclasse personalizzata che risponde agli eventi generati dalla coda di pagamento di StoreKit. 
- **Le operazioni StoreKit sono asincrone** , dopo l'avvio di un SKProductRequest o l'aggiunta di un SKPayment alla coda, il controllo viene restituito al codice. StoreKit chiamerà i metodi sulla sottoclasse SKProductsRequestDelegate o SKPaymentTransactionObserver quando riceve i dati dai server Apple. 

Il diagramma seguente illustra le relazioni tra le varie classi StoreKit (le classi astratte devono essere implementate nell'applicazione):   

 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "The relationships between the various StoreKit classes abstract classes must be implemented in the app")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   

Queste classi sono descritte in modo più dettagliato più avanti in questo documento.

## <a name="testing"></a>Test

Per la maggior parte delle operazioni StoreKit è necessario un dispositivo reale per il test. Recupero delle informazioni sul prodotto (ad esempio il prezzo &amp; Descrizione) funzionerà nel simulatore, ma le operazioni di acquisto e ripristino restituiranno un errore, ad esempio FailedTransaction code = 5002 si è verificato un errore sconosciuto.

Nota: StoreKit non funziona nel simulatore iOS. Quando si esegue l'applicazione nel simulatore iOS, StoreKit registra un avviso se l'applicazione tenta di recuperare la coda di pagamento. È necessario eseguire il test dell'archivio sui dispositivi effettivi.   

Importante: non accedere con l'account di test nell'applicazione Impostazioni. È possibile usare l'applicazione Settings per disconnettersi da qualsiasi account Apple ID esistente, quindi è necessario attendere che venga richiesto *all'interno della sequenza di acquisto in-app* di effettuare l'accesso usando un ID Apple di test.   

Se si tenta di accedere all'archivio reale con un account di prova, questo verrà convertito automaticamente in un ID Apple reale. Tale account non sarà più utilizzabile per i test.

Per testare il codice StoreKit, è necessario disconnettere l'account di test di iTunes normale e accedere con un account di test speciale (creato in iTunes Connect) collegato all'archivio di test. Per disconnettersi dall'account corrente, vedere **le impostazioni > iTunes e App Store** , come illustrato di seguito:

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "To sign out of the current account visit Settings iTunes and App Store")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)

quindi, accedere con un account *di prova quando richiesto da StoreKit all'interno dell'app*:

Per creare utenti di test in iTunes Connect, fare clic su **utenti e ruoli** nella pagina principale.

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "To create test users in iTunes Connect click on Users and Roles on the main page")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

Seleziona **tester sandbox**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "Selecting Sandbox Testers")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

Viene visualizzato l'elenco degli utenti esistenti. È possibile aggiungere un nuovo utente o eliminare un record esistente. Il portale non (attualmente) consente di visualizzare o modificare gli utenti di test esistenti, quindi è consigliabile tenere un record valido di ogni utente di test creato (in particolare la password assegnata). Quando si elimina un utente test, l'indirizzo di posta elettronica non può essere riutilizzato per un altro account di test.  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "The list of existing users is displayed")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 I nuovi utenti di test hanno attributi simili a un ID Apple reale, ad esempio nome, password, domanda e risposta segrete. Tenere traccia di tutti i dettagli immessi qui. Il campo **Seleziona iTunes Store** determinerà la valuta e la lingua che gli acquisti in-app useranno quando si è connessi come tale utente.

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "The Select iTunes Store field will determine the user's currency and language for their in-app purchases")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>Recupero delle informazioni sul prodotto

Il primo passaggio per la vendita di un prodotto di acquisto in-app è visualizzarlo: recupero del prezzo e della descrizione correnti dall'App Store per la visualizzazione.   

Indipendentemente dal tipo di prodotti che un'app vende (utilizzabile, non utilizzabile o da un tipo di sottoscrizione), il processo di recupero delle informazioni sul prodotto per la visualizzazione è lo stesso. Il codice InAppPurchaseSample che accompagna questo articolo contiene un progetto denominato *materiali di consumo* che illustra come recuperare le informazioni di produzione per la visualizzazione. Viene illustrato come:

- Creare un'implementazione di `SKProductsRequestDelegate` e implementare il metodo astratto `ReceivedResponse`. Il codice di esempio chiama questa classe `InAppPurchaseManager`. 
- Verificare con StoreKit per verificare se sono consentiti i pagamenti (usando `SKPaymentQueue.CanMakePayments`). 
- Creare un'istanza di un `SKProductsRequest` con gli ID prodotto definiti in iTunes Connect. Questa operazione viene eseguita nel metodo di `InAppPurchaseManager.RequestProductData` dell'esempio. 
- Chiamare il metodo Start sul `SKProductsRequest`. Viene attivata una chiamata asincrona ai server dell'app Store. Il delegato (`InAppPurchaseManager`) verrà richiamato con i risultati. 
- Il metodo del `ReceivedResponse` (`InAppPurchaseManager`) del delegato aggiorna l'interfaccia utente con i dati restituiti dall'App Store (prezzi prodotto & descrizioni o messaggi relativi a prodotti non validi). 

L'interazione complessiva ha un aspetto simile al seguente ( **StoreKit** è incorporato in iOS e l' **App Store** rappresenta i server Apple):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "Retrieving Product Information graph")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>Visualizzazione dell'esempio di informazioni sul prodotto

Il codice di esempio di [InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit) *Consumable* -code dimostra come recuperare le informazioni sul prodotto. La schermata principale dell'esempio Visualizza informazioni per due prodotti recuperati dall'App Store:   

 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "The main screen displays information products  retrieved from the App Store")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   

Il codice di esempio per il recupero e la visualizzazione delle informazioni sul prodotto è illustrato in modo più dettagliato di seguito.

#### <a name="viewcontroller-methods"></a>Metodi ViewController

La classe `ConsumableViewController` gestirà la visualizzazione dei prezzi per due prodotti i cui ID prodotto sono hardcoded nella classe.

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

A livello di classe deve essere presente anche un NSObject dichiarato che verrà usato per configurare un Observer `NSNotificationCenter`:

```csharp
NSObject priceObserver;
```

Nel metodo ViewWillAppear, l'Observer viene creato e assegnato utilizzando il centro notifiche predefinito:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

Alla fine del metodo di `ViewWillAppear`, chiamare il metodo `RequestProductData` per avviare la richiesta StoreKit. Una volta effettuata la richiesta, StoreKit contatterà in modo asincrono i server Apple per ottenere le informazioni e riportarle nell'app. Questa operazione viene eseguita dalla sottoclasse `SKProductsRequestDelegate` (`InAppPurchaseManager`) descritta nella sezione successiva.

```csharp
iap.RequestProductData(products);
```

Il codice per visualizzare il prezzo e la descrizione recupera semplicemente le informazioni da SKProduct e le assegna ai controlli UIKit (si noti che vengono visualizzate le `LocalizedTitle` e `LocalizedDescription`-StoreKit risolve automaticamente il testo e i prezzi corretti in base alle Impostazioni account). Il codice seguente appartiene alla notifica creata in precedenza:

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

Infine, il metodo di `ViewWillDisappear` deve garantire la rimozione dell'Observer:

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>Metodi SKProductRequestDelegate (InAppPurchaseManager)

Il metodo `RequestProductData` viene chiamato quando l'applicazione desidera recuperare i prezzi dei prodotti e altre informazioni. Viene analizzata la raccolta di ID prodotto nel tipo di dati corretto, quindi viene creato un `SKProductsRequest` con tali informazioni. La chiamata al metodo Start causa la richiesta di una richiesta di rete ai server Apple. La richiesta verrà eseguita in modo asincrono e chiamerà il metodo di `ReceivedResponse` del delegato quando viene completato correttamente.

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

iOS effettuerà automaticamente il routing della richiesta alla versione "sandbox" o "produzione" dell'app Store a seconda del profilo di provisioning con cui viene eseguita l'applicazione. in questo modo, quando si sviluppa o si testa l'app, la richiesta avrà accesso a ogni prodotto configurato in iTunes Connect (anche quelli che non sono ancora stati inviati o approvati da Apple). Quando l'applicazione è in produzione, le richieste StoreKit restituiranno solo informazioni per i prodotti **approvati** .   

Il `ReceivedResponse` metodo sottoposto a override viene chiamato dopo che i server Apple hanno risposto con i dati. Poiché questa operazione viene chiamata in background, il codice deve analizzare i dati validi e utilizzare una notifica per inviare le informazioni sul prodotto a qualsiasi ViewControllers che sia "in ascolto" per la notifica. Di seguito è riportato il codice per raccogliere le informazioni valide sul prodotto e inviare una notifica:

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

Sebbene non sia illustrato nel diagramma, è necessario eseguire l'override anche del metodo `RequestFailed` in modo che sia possibile fornire feedback all'utente nel caso in cui i server dell'app Store non siano raggiungibili (o si verifichi un altro errore). Il codice di esempio scrive semplicemente nella console, ma un'applicazione reale può scegliere di eseguire una query per `error.Code` proprietà e implementare un comportamento personalizzato, ad esempio un avviso per l'utente.

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

Questa schermata mostra l'applicazione di esempio subito dopo il caricamento (se non sono disponibili informazioni sul prodotto):

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "The sample app immediately after loading when no product information is available")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>Prodotti non validi

Un `SKProductsRequest` può inoltre restituire un elenco di ID prodotto non validi. I prodotti non validi vengono in genere restituiti a causa di uno dei seguenti elementi:   

**ID prodotto non tipizzato** . sono accettati solo ID prodotto validi.   
   
 Il **prodotto non è stato approvato** : durante i test, tutti i prodotti cancellati per la vendita devono essere restituiti da un `SKProductsRequest`; Tuttavia, nell'ambiente di produzione vengono restituiti solo i prodotti approvati.   
   
 L' **ID app non è esplicito** : gli ID app con caratteri jolly (con un asterisco) non consentono l'acquisto in-app.   
   
 **Profilo di provisioning errato** : se si apportano modifiche alla configurazione dell'applicazione nel portale di provisioning, ad esempio l'abilitazione degli acquisti in-app, ricordarsi di rigenerare e usare il profilo di provisioning corretto quando si compila l'app.   
   
 il **contratto per le applicazioni a pagamento iOS non è** disponibile: le funzionalità di StoreKit non funzioneranno a meno che non sia presente un contratto valido per l'account sviluppatore Apple.   
   
 **Il file binario si trova nello stato rifiutato** : se è presente un file binario inviato in precedenza nello stato rifiutato (dal team di App Store o dallo sviluppatore), le funzionalità di StoreKit non funzioneranno.

Il metodo `ReceivedResponse` nel codice di esempio restituisce i prodotti non validi alla console:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>Visualizzazione dei prezzi localizzati

I livelli di prezzo specificano un prezzo specifico per ogni prodotto in tutti gli App Store internazionali. Per assicurarsi che i prezzi siano visualizzati correttamente per ogni valuta, usare il metodo di estensione seguente (definito in `SKProductExtension.cs`) invece della proprietà Price di ogni `SKProduct`:

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

Il codice che imposta il titolo del pulsante Usa il metodo di estensione come il seguente:

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

L'uso di due account di test di iTunes diversi (uno per l'archivio americano e uno per l'archivio giapponese) genera gli screenshot seguenti:   

 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "Two different iTunes test accounts showing language specific results")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   

Si noti che l'archivio influiscono sulla lingua utilizzata per le informazioni sul prodotto e sulla valuta del prezzo, mentre l'impostazione della lingua del dispositivo influiscono sulle etichette e su altri contenuti localizzati.   

Ricordare che per usare un account di test di archivio diverso è necessario **disconnettersi** nelle **Impostazioni > iTunes e App Store** e riavviare l'applicazione per accedere con un account diverso. Per modificare la lingua del dispositivo, passare a **impostazioni > generale > lingua > internazionale**.
