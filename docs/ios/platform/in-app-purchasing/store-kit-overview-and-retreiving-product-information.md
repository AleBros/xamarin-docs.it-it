---
title: Archiviare una panoramica di Kit e il recupero delle informazioni di prodotto
ms.topic: article
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: bafada037f912007201fd5e81b17302b21de3092
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="store-kit-overview-and-retrieving-product-information"></a>Archiviare una panoramica di Kit e il recupero delle informazioni di prodotto

L'interfaccia utente per un acquisto in-app viene visualizzato nelle schermate riportate di seguito.
Prima di qualsiasi transazione viene eseguita, l'applicazione deve recuperare prezzo del prodotto e la descrizione per la visualizzazione. Quindi quando l'utente preme **acquistare**, l'applicazione effettua una richiesta a StoreKit che gestisce la finestra di dialogo di conferma e account di accesso ID Apple. Presupponendo che la transazione ha esito positivo, StoreKit notifica al codice dell'applicazione, che deve memorizzare il risultato della transazione e fornire all'utente l'accesso per l'acquisto.   

   
 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit notifica al codice dell'applicazione, che è necessario archiviare il risultato della transazione e fornire all'utente l'accesso per l'acquisto")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>Classi

Implementazione di acquisti in-app richiede le seguenti classi di framework StoreKit:   
   
 **SKProductsRequest** : una richiesta per StoreKit prodotti approvati da vendere (App di Store). Può essere configurato con un numero di ID prodotto.

-   **SKProductsRequestDelegate** : dichiara i metodi per gestire le richieste di prodotti e le risposte. 
-   **SKProductsResponse** – inviati nuovamente al delegato StoreKit (App di Store). Contiene SKProducts che corrisponde al prodotto ID inviato con la richiesta. 
-   **SKProduct** – un prodotto recuperati StoreKit (che è stato configurato in iTunes Connect). Contiene informazioni sul prodotto, ad esempio ID prodotto, titolo, descrizione e prezzo. 
-   **SKPayment** – creato con un ID prodotto e aggiunto alla coda di pagamento per eseguire un acquisto. 
-   **SKPaymentQueue** -richieste di pagamento in coda da inviare ad Apple. Le notifiche vengono attivate in seguito a ogni pagamento in fase di elaborazione. 
-   **SKPaymentTransaction** – rappresenta una transazione completata (una richiesta di acquisto che è stata elaborata dall'App Store e inviata all'applicazione tramite StoreKit). La transazione può essere acquistato, ripristinato o non riuscito. 
-   **SKPaymentTransactionObserver** – sottoclasse personalizzata che risponde agli eventi generati dalla coda StoreKit pagamento. 
-   **StoreKit operazioni sono asincrone** : dopo l'avvio di un SKProductRequest o un SKPayment viene aggiunto alla coda, il controllo viene restituito al codice. StoreKit chiamerà i metodi di una sottoclasse SKProductsRequestDelegate o SKPaymentTransactionObserver quando riceve dati dal server di Apple. 


Il diagramma seguente mostra le relazioni tra le varie classi StoreKit (classi astratte devono essere implementate nell'applicazione):   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "Le relazioni tra le varie classi astratte di classi StoreKit devono essere implementate nell'app")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   
   
   
   
 Queste classi sono illustrate in dettaglio più avanti in questo documento.

## <a name="testing"></a>Test

La maggior parte delle operazioni di StoreKit richiedono un dispositivo reale per il test. Il recupero delle informazioni di prodotto (ie. prezzo &amp; descrizione) funzioneranno nel simulatore ma acquisto e operazioni di ripristino verranno restituito un errore (ad esempio codice FailedTransaction = 5002 si è verificato un errore sconosciuto).

Nota: StoreKit non funziona nel simulatore iOS. Quando si esegue l'applicazione nel simulatore iOS, StoreKit registra un avviso se l'applicazione tenta di recuperare la coda di pagamento. L'archivio di test devono essere eseguite su dispositivi effettivi.   
   
   
   
 Importante: Non accedere con l'account di test dell'applicazione di impostazioni. È possibile utilizzare l'applicazione delle impostazioni di accesso da qualsiasi account ID Apple esistente, quindi è necessario attendere che venga richiesta *all'interno della sequenza di acquisto In-App* di accesso tramite un ID Apple di test   
   
   
   

Se si tenta di accedere all'archivio reale con un account di prova, verrà automaticamente convertito in un ID Apple reale Tale account non saranno utilizzabile per il test.

Per testare il codice StoreKit è necessario disconnettersi l'account di prova iTunes regolare e un account di accesso con un account di prova speciale (creato in iTunes Connect) che è collegato per l'archivio dei test. Per disconnettersi da visitare l'account corrente **Impostazioni > iTunes App Store e** come illustrato di seguito:

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "Per disconnettersi dalle visitare account corrente impostazioni iTunes App Store")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)
 
quindi accedere con un account di prova *quando richiesto da StoreKit all'interno dell'app*:



Per creare gli utenti di test in iTunes Connect fare clic su **utenti e ruoli** nella pagina principale.

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "Per creare gli utenti di test in iTunes Connect fare clic su utenti e ruoli nella pagina principale")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

Selezionare **tester Sandbox**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "Se si seleziona tester Sandbox")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

Viene visualizzato l'elenco di utenti esistenti. È possibile aggiungere un nuovo utente o eliminare un record esistente. Il portale non (attualmente) consentono di visualizzazione o modifica esistente verificare gli utenti, è consigliabile mantenere un record di ogni utente test creato (in particolare la password assegnata) valido. Quando si elimina un utente di prova l'indirizzo di posta elettronica non può essere riutilizzato per un altro account di prova.  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "Viene visualizzato l'elenco di utenti esistenti")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 Nuovi utenti di test dispongono di attributi simili a un ID Apple reale (ad esempio nome, una password, domanda e risposta). Mantenere un record di tutti i dettagli immesso. Il **selezionare iTunes Store** campo determinerà la valuta e verrà acquisti in-app la lingua da utilizzare quando registrato in come tale utente.

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "Il campo selezionare iTunes Store determinerà valuta dell'utente e la lingua loro acquisti in-app")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>Durante il recupero delle informazioni sul prodotto

Viene visualizzato il primo passaggio nella vendita di un prodotto di acquisto in-app: recuperare il prezzo corrente e la descrizione dall'App Store per la visualizzazione.   
   
   
   
 Indipendentemente dal tipo di prodotti un'app vende (che può essere utilizzato, Non riproducibile o un tipo di sottoscrizione), il processo di recupero di informazioni sul prodotto per la visualizzazione è lo stesso. Il codice di InAppPurchaseSample che accompagna questo articolo contiene un progetto denominato *sussidiarie* che illustra come recuperare le informazioni di produzione per la visualizzazione. Viene illustrato come:

-  Creare un'implementazione di `SKProductsRequestDelegate` e implementare il `ReceivedResponse` metodo astratto. Il codice di esempio chiama questo il `InAppPurchaseManager` classe. 
-  Controllare con StoreKit per vedere se sono consentiti i pagamenti (tramite `SKPaymentQueue.CanMakePayments` ). 
-  Creare un'istanza di un `SKProductsRequest` con gli ID dei prodotti che sono state definite in iTunes Connect. Questa operazione viene eseguita dell'esempio `InAppPurchaseManager.RequestProductData` metodo. 
-  Chiamare il metodo Start sul `SKProductsRequest` . In questo modo viene attivata una chiamata asincrona al server di App Store. Il delegato ( `InAppPurchaseManager` ) verrà chiamato-back con i risultati. 
-  Il delegato ( `InAppPurchaseManager` ) `ReceivedResponse` metodo aggiorna l'interfaccia utente con i dati restituiti dall'App Store (prezzi dei prodotti e le descrizioni o messaggi sui prodotti non validi). 

L'interazione complessivo è simile al seguente ( **StoreKit** è incorporato in iOS e **App Store** rappresenta i server Apple):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "Grafico di durante il recupero delle informazioni sul prodotto")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>Esempio di informazioni sulla visualizzazione del prodotto

Il [InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) *sussidiarie* codice di esempio viene illustrato come è possibile recuperare informazioni sul prodotto. La schermata principale dell'esempio visualizza informazioni per i due prodotti viene recuperate dall'App Store:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "La schermata principale Visualizza prodotti informazioni recuperati dall'App Store")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   
   
   
   
 Il codice di esempio per recuperare e visualizzare informazioni sul prodotto è illustrato in dettaglio più avanti.


#### <a name="viewcontroller-methods"></a>Metodi ViewController

La `ConsumableViewController` classe gestirà la visualizzazione dei prezzi per i due prodotti i cui ID prodotto sono hardcoded nella classe.

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

La classe livello ci deve anche essere un NSObject dichiarato che verrà utilizzato per il programma di installazione un `NSNotificationCenter` observer:

```csharp
NSObject priceObserver;
```

Nel metodo ViewWillAppear l'osservatore è creato e assegnato utilizzando il centro notifiche predefinito:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

   
   
 Alla fine del `ViewWillAppear` metodo, chiamare il `RequestProductData` per avviare la richiesta StoreKit. Dopo avere creata la richiesta, in modo asincrono StoreKit contatta i server Apple per ottenere le informazioni e il feed all'app. A questo scopo, il `SKProductsRequestDelegate` sottoclasse ( `InAppPurchaseManager`) che viene descritto nella sezione successiva.

```csharp
iap.RequestProductData(products);
```

Il codice per visualizzare il prezzo e la descrizione semplicemente recupera le informazioni dal SKProduct e assegnarlo ai controlli UIKit (si noti che, viene visualizzato il `LocalizedTitle` e `LocalizedDescription` – StoreKit risolve automaticamente il testo corretto e prezzi in base alle impostazioni dell'account dell'utente). Il codice seguente fa parte la notifica creato in precedenza:

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

Infine, il `ViewWillDisappear` metodo deve garantire l'osservatore è stato rimosso:

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>Metodi SKProductRequestDelegate (InAppPurchaseManager)

Il `RequestProductData` metodo viene chiamato quando l'applicazione desidera recuperare i prezzi dei prodotti e altre informazioni. Analizza la raccolta di ID prodotto in tipo di dati corretto, quindi crea un `SKProductsRequest` con queste informazioni. Chiamando il metodo Start provoca una richiesta di rete per i server Apple. La richiesta verrà eseguita in modo asincrono e chiamare il `ReceivedResponse` metodo del delegato quando è stata completata correttamente.

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

iOS indirizzerà automaticamente la richiesta alla versione 'produzione' o 'sandbox' dell'archivio di App, a seconda di quale l'applicazione è in esecuzione con: il profilo di provisioning in modo durante lo sviluppo o test dell'app la richiesta avrà accesso a tutti i prodotti configurata in iTunes Connect (anche quelli che non ancora inviata o approvata da Apple). Quando l'applicazione è in produzione, le richieste StoreKit restituirà solo le informazioni per **approvato** prodotti.   
   
   
   
 Il `ReceivedResponse` metodo sottoposto a override viene chiamato dopo che i server Apple hanno risposto con i dati. Perché viene chiamato in background, il codice dovrebbe analizzare i dati validi e utilizzare una notifica per inviare le informazioni sul prodotto a qualsiasi ViewControllers ' ascolto ' per la notifica. Il codice per raccogliere informazioni sul prodotto valido e inviare una notifica è illustrato di seguito:

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

Sebbene non illustrato nel diagramma, il `RequestFailed` (metodo) deve inoltre essere sottoposto a override in modo che è possibile fornire un feedback all'utente, nel caso in cui i server di App Store non sono raggiungibili (o si verifica un altro errore). Nell'esempio di codice viene semplicemente scritto nella console, ma un'applicazione reale potrebbe scegliere di eseguire query `error.Code` proprietà e implementare un comportamento personalizzato (ad esempio, un avviso all'utente).

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

Questa schermata mostra l'applicazione di esempio immediatamente dopo il caricamento (quando è disponibile alcuna informazione di prodotto):

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "L'app di esempio immediatamente dopo il caricamento quando è disponibile alcuna informazione di prodotto")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>Prodotti non validi

Un `SKProductsRequest` può inoltre restituire un elenco di ID prodotto non valido. I prodotti non validi vengono restituiti in genere a causa di uno dei seguenti:   
   
   
   
 **ID prodotto è stato digitato correttamente** – vengono accettate solo ID prodotto valido.   
   
 **Prodotto non è stato approvato** : durante il test, tutti i prodotti che non sono selezionati per la vendita devono essere restituiti da un `SKProductsRequest`; tuttavia nell'ambiente di produzione vengono restituiti solo i prodotti approvato.   
   
 **ID dell'App non è esplicito** – Wildcard IDs dell'App (con un asterisco (*) non consentono l'acquisto in-app.   
   
 **Profilo di provisioning corretto** : se si apportano modifiche alla configurazione dell'applicazione nel portale di provisioning (ad esempio l'abilitazione di acquisti in-app), è necessario generare di nuovo e utilizzare il profilo di provisioning corretto quando si compila l'app.   
   
 **contratto a pagamento applicazioni iOS non è presente sul posto** – StoreKit funzionalità non funzionerà affatto, a meno che non c'è un contratto valido per l'Account per sviluppatori di Apple.   
   
 **Il file binario è stato rifiutato** : se è presente un file binario inviato in precedenza nello stato rifiutato (o dal team di App Store, dallo sviluppatore) StoreKit funzionalità non saranno disponibili.

Il `ReceivedResponse` i prodotti non validi nella console di output del metodo nel codice di esempio:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>Visualizzazione di prezzi localizzati

Livelli di prezzo specifica di un prezzo specifico per ogni prodotto in tutti gli archivi di applicazioni internazionali. Per garantire che i prezzi sono visualizzati correttamente per ogni valuta, utilizzare il seguente metodo di estensione (definito in `SKProductExtension.cs`) anziché la proprietà di prezzo di ogni `SKProduct`:

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

Il codice che imposta il titolo del pulsante utilizza il metodo di estensione simile al seguente:

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

Utilizzo di due account di test diversi iTunes (uno per l'archivio American) e uno per il giapponese archivio risultati nelle schermate seguenti:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "Due iTunes diversi test che mostra linguaggio risultati specifici dell'account")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   
   
   
   
 Si noti che l'archivio influisce sulla lingua utilizzata per la valuta di informazioni e il prezzo del prodotto, mentre l'impostazione della lingua del dispositivo influisce sulle etichette e altri contenuti localizzati.   
   
   
   
 Tenere presente che per utilizzare un archivio diverso test account è necessario **Sign Out** nel **Impostazioni > iTunes App Store e** e avviare nuovamente l'applicazione di accedere con un account diverso. Per modificare la lingua del dispositivo, passare a **Impostazioni > generale > internazionale > lingua**.

