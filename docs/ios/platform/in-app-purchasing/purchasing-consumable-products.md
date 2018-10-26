---
title: Acquisto di prodotti di consumo in xamarin. IOS
description: Questo documento descrive i prodotti di consumo in xamarin. IOS. Prodotti di consumo sono a uso singolo parti di funzionalità, ad esempio valuta di gioco.
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: b55465a700974e0ce5ceb8893d96311d920e04ae
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123648"
---
# <a name="purchasing-consumable-products-in-xamarinios"></a>Acquisto di prodotti di consumo in xamarin. IOS

Prodotti di consumo sono il più semplice da implementare, poiché non c'è Nessun requisito 'restore'. Sono utili per i prodotti come valuta nel gioco o una parte del singolo utilizzo della funzionalità. Gli utenti possono nuovamente acquistare prodotti di consumo over-e-over nuovamente.

## <a name="built-in-product-delivery"></a>Fornitura di prodotti predefinite

Il codice di esempio che accompagna questo documento illustra i prodotti incorporati: gli ID prodotto sono hardcoded nell'applicazione poiché essi sono strettamente collegati al codice che sblocca le funzionalità dopo il pagamento. Il processo di acquisto può essere visualizzato come segue:   
   
[![La visualizzazione processo di acquisto](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png#lightbox)     
   
 Il flusso di lavoro di base è:   
   
 1. L'app aggiunge un `SKPayment` alla coda. Se l'utente verrà richiesto l'ID Apple e viene richiesto di confermare il pagamento necessari.   
   
 2. StoreKit invia la richiesta al server per l'elaborazione.   
   
 3. Quando la transazione viene completata, il server risponde con un valore di ricezione delle transazioni.   
   
 4. Il `SKPaymentTransactionObserver` sottoclasse riceve la ricezione e lo elabora.   
   
 5. L'applicazione consente il prodotto (aggiornando `NSUserDefaults` o un altro meccanismo) e quindi chiama di StoreKit `FinishTransaction`.

È un altro tipo di flusso di lavoro *prodotti Server-Delivered* , vale a dire descritte più avanti in questo documento (vedere la sezione *Server-Delivered prodotti e ricezione verifica*).

## <a name="consumable-products-example"></a>Esempio di prodotti utilizzabile

Il [codice InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) contiene un progetto chiamato *sussidiarie* che implementa una basic 'all'interno del gioco valuta' (denominata "apportare crediti"). Nell'esempio viene illustrato come implementare due prodotti di acquisto in-app per consentire all'utente di acquistare perché molti "crediti monkey" desiderato: in un'applicazione reale sarebbero anche esserci un modo per spesa li!   
   
   
   
 L'applicazione viene illustrata nelle schermate illustrate, ogni acquisto aggiunge altri "crediti monkey" per il bilanciamento dell'utente:   
   
   
   
 [![Ogni acquisto consente di aggiungere altri crediti monkey al saldo degli utenti](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png#lightbox)   
   
   
   
 Le interazioni tra classi personalizzate, l'App Store e StoreKit simile al seguente:   
   
   
   
 [![Le interazioni tra classi personalizzate, l'App Store e StoreKit](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png#lightbox)

&nbsp;

### <a name="viewcontroller-methods"></a>Metodi di ViewController

Oltre alle proprietà e metodi necessari per recuperare le informazioni sul prodotto, il controller di visualizzazione richiede agli osservatori di notifica aggiuntiva per l'ascolto delle notifiche sugli acquisti. Si tratta semplicemente `NSObjects` che verrà registrato e rimossa a partire `ViewWillAppear` e `ViewWillDisappear` rispettivamente.

```csharp
NSObject succeededObserver, failedObserver;
```

Il costruttore verrà inoltre creato il `SKProductsRequestDelegate` sottoclasse ( `InAppPurchaseManager`) che a sua volta crea e registra il `SKPaymentTransactionObserver` ( `CustomPaymentObserver`).   
   
   
   
 La prima parte dell'elaborazione di una transazione di acquisto in-app consiste nella gestione di pressione del pulsante quando l'utente desidera acquistare un elemento, come illustrato nel codice seguente dall'applicazione di esempio:

```csharp
buy5Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy5ProductId);
};
buy10Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy10ProductId);
};
```

   
   
 La seconda parte dell'interfaccia utente gestisce la notifica che la transazione ha avuto esito positivo, in questo caso, aggiornare il saldo visualizzato:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionSucceededNotification,
(notification) => {
   balanceLabel.Text = CreditManager.Balance() + " monkey credits";
});
```

La parte finale dell'interfaccia utente viene visualizzato un messaggio se una transazione è stata annullata per qualche motivo. Nell'esempio di codice viene semplicemente scritto un messaggio nella finestra di output:

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

Oltre a questi metodi nel controller di visualizzazione, una transazione di acquisto del prodotto può essere utilizzato anche richiede codice sul `SKProductsRequestDelegate` e il `SKPaymentTransactionObserver`.

### <a name="inapppurchasemanager-methods"></a>Metodi InAppPurchaseManager

Il codice di esempio implementa un numero di acquisti relativi metodi sulla classe InAppPurchaseManager, tra cui il `PurchaseProduct` metodo che crea un `SKPayment` dell'istanza e lo aggiunge alla coda per l'elaborazione:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

Aggiunta del pagamento per la coda è un'operazione asincrona. L'applicazione riacquisirà il controllo mentre StoreKit elabora la transazione e lo invia al server di Apple. È in questa fase che verrà verificato che iOS l'utente è connesso a Store l'App e relativa richiesta di un ID Apple e la password se richiesto.   
   
   
   
 Supponendo correttamente che l'utente esegue l'autenticazione con l'App Store e accetta di transazione, il `SKPaymentTransactionObserver` riceverà una risposta di StoreKit e chiamare il metodo seguente per completare la transazione e completa le.

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

L'ultimo passaggio consiste nel garantire che una notifica di StoreKit è che sono stati soddisfatti correttamente la transazione, chiamando `FinishTransaction`:

```csharp
public void FinishTransaction(SKPaymentTransaction transaction, bool wasSuccessful)
{
   // remove the transaction from the payment queue.
   SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);  // THIS IS IMPORTANT - LET'S APPLE KNOW WE'RE DONE !!!!
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {transaction},new NSObject[] {new NSString("transaction")});
       if (wasSuccessful) {
           // send out a notification that we've finished the transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionSucceededNotification, this, userInfo);
       } else {
           // send out a notification for the failed transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionFailedNotification, this, userInfo);
       }
   }
}
```

Una volta che viene recapitato il prodotto, `SKPaymentQueue.DefaultQueue.FinishTransaction` deve essere chiamato per rimuovere la transazione dalla coda del pagamento.

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>Metodi SKPaymentTransactionObserver (CustomPaymentObserver)

Le chiamate di StoreKit il `UpdatedTransactions` metodo quando riceve una risposta dal server di Apple e passa una matrice di `SKPaymentTransaction` oggetti per il codice da esaminare. Il metodo scorre in ciclo ogni transazione ed esegue una funzione diversa basata sullo stato della transazione (come illustrato di seguito):

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
           case SKPaymentTransactionState.Purchased:
              theManager.CompleteTransaction(transaction);
               break;
           case SKPaymentTransactionState.Failed:
              theManager.FailedTransaction(transaction);
               break;
           default:
               break;
       }
   }
}
```

Il `CompleteTransaction` metodo è stato analizzato in precedenza in questa sezione: Salva i dettagli dell'acquisto per `NSUserDefaults`, Finalizza la transazione con StoreKit e infine invia una notifica all'interfaccia utente di aggiornamento.

### <a name="purchasing-multiple-products"></a>Acquisto di più prodotti

Se ha senso nell'applicazione per l'acquisto di più prodotti, utilizzare il `SKMutablePayment` classe e impostare il campo Quantity:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

Il codice che gestisce le transazioni completate debba anche eseguire una query la proprietà relativa alla quantità per soddisfare in modo corretto l'acquisto:

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   var qty = transaction.Payment.Quantity;
   if (productId == ConsumableViewController.Buy5ProductId)
       CreditManager.Add(5 * qty);
   else if (productId == ConsumableViewController.Buy10ProductId)
       CreditManager.Add(10 * qty);
   else
       Console.WriteLine ("Shouldn't happen, there are only two products");
   FinishTransaction(transaction, true);
}
```

Quando l'utente acquista quantità multiple, all'avviso conferma StoreKit riflette la quantità, il prezzo unitario e il prezzo totale che verrà addebitate, come illustrato nello screenshot seguente:

[![Conferma acquisto](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png#lightbox)

## <a name="handling-network-outages"></a>Gestione delle interruzioni di rete

Acquisti in-app richiedono una connessione di rete funzionante per StoreKit comunicare con i server Apple. Se non è disponibile una connessione di rete, quindi acquisti in-app sarà disponibili.

### <a name="product-requests"></a>Richieste di prodotto

Se la rete non è disponibile durante la creazione di un `SKProductRequest`, il `RequestFailed` metodo per il `SKProductsRequestDelegate` sottoclasse ( `InAppPurchaseManager`) verrà chiamato, come illustrato di seguito:

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {error},new NSObject[] {new NSString("error")});
       // send out a notification for the failed transaction
       NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerRequestFailedNotification, this, userInfo);
   }
}
```

ViewController quindi attende la notifica e viene visualizzato un messaggio nei pulsanti di acquisto:

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

Poiché una connessione di rete può essere temporanea nei dispositivi mobili, applicazioni potrebbero da monitorare lo stato della rete usando il framework SystemConfiguration e provare nuovamente ad quando è disponibile una connessione di rete. Fare riferimento a Apple o l'oggetto che lo usa.

### <a name="purchase-transactions"></a>Transazioni di acquisto

La coda di pagamento di StoreKit archivierà e acquisto inoltro chiede se possibile, quindi l'effetto di un'interruzione della rete variano a seconda se la rete non è riuscita durante il processo di acquisto.   
   
   
   
 Se si verifica un errore durante una transazione, il `SKPaymentTransactionObserver` sottoclasse ( `CustomPaymentObserver`) avranno la `UpdatedTransactions` metodo chiamato e `SKPaymentTransaction` classe sarà nello stato non riuscito.

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
           case SKPaymentTransactionState.Purchased:
               theManager.CompleteTransaction(transaction);
               break;
           case SKPaymentTransactionState.Failed:
               theManager.FailedTransaction(transaction);
               break;
           default:
               break;
       }
   }
}
```

Il `FailedTransaction` metodo rileva se l'errore era dovuto all'annullamento dall'utente, come illustrato di seguito:

```csharp
public void FailedTransaction (SKPaymentTransaction transaction)
{
   //SKErrorPaymentCancelled == 2
   if (transaction.Error.Code == 2) // user cancelled
       Console.WriteLine("User CANCELLED FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   else // error!
       Console.WriteLine("FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   FinishTransaction(transaction,false);
}
```

Anche se una transazione ha esito negativo, il `FinishTransaction` metodo deve essere chiamato per rimuovere la transazione dalla coda di pagamento:

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

L'esempio di codice invia una notifica in modo che ViewController è possibile visualizzare un messaggio. Le applicazioni non devono visualizzare un altro messaggio se l'utente ha annullato la transazione. Altri codici di errore che potrebbero verificarsi includono:

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>Gestione delle restrizioni

Il **Impostazioni > generali > restrizioni** funzionalità di iOS consente agli utenti di bloccare alcune funzionalità del dispositivo.   
   
   
   
 È possibile eseguire una query se l'utente è autorizzato per effettuare acquisti in-app tramite il `SKPaymentQueue.CanMakePayments` (metodo). Se il risultato è false, l'utente non può accedere acquisti in-app. StoreKit verrà automaticamente visualizzato un messaggio di errore all'utente se viene tentato un acquisto. Selezionando questo valore l'applicazione può invece nascondere i pulsanti di acquisto o eseguire un'altra azione per consentire all'utente.   
   
   
   
 Nel `InAppPurchaseManager.cs` file di `CanMakePayments` metodo include la funzione di StoreKit simile al seguente:

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

Per testare questo metodo, usare il **restrizioni** funzionalità di iOS per disabilitare **acquisti In-App**:   
   
   
   
 [![Usare la funzionalità relativa alle restrizioni di iOS per disabilitare gli acquisti In-App](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png#lightbox)   
   
   
   
 Questo codice di esempio dal `ConsumableViewController` reagisce agli `CanMakePayments` restituzione di false visualizzando **disabilitato dall'App Store** testo sui pulsanti disabilitati.

```csharp
// only if we can make payments, request the prices
if (iap.CanMakePayments()) {
   // now go get prices, if we don't have them already
   if (!pricesLoaded)
       iap.RequestProductData(products); // async request via StoreKit -> App Store
} else {
   // can't make payments (purchases turned off in Settings?)
   // the buttons are disabled by default, and only enabled when prices are retrieved
   buy5Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
   buy10Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
}
```

L'applicazione è simile a questa operazione quando il **acquisti In-App** funzionalità è limitata: i pulsanti di acquisto sono disabilitati.   
   
   
   
 [![L'applicazione è simile quando gli acquisti In-App funzionalità è limitata all'acquisto pulsanti sono disabilitati](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png#lightbox)   
   
   
   

Informazioni sui prodotti può comunque essere richieste quando `CanMakePayments` è false, in modo che l'app può comunque recuperare e visualizzare i prezzi. Ciò significa che se è stata rimossa la `CanMakePayments` controllo dal codice i pulsanti di acquisto venga comunque essere attivo, tuttavia, quando un acquisto viene tentato l'utente visualizzerà un messaggio che **acquisti In-app non sono consentiti** (generati da StoreKit Quando la coda di pagamento è accessibile):   
   
   
   
 [![Acquisti in-app non sono consentiti](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png#lightbox)   
   
   
   
 Applicazioni reali potrebbero adottare un approccio diverso alla gestione di restrizione, ad esempio nascondendo completamente i pulsanti e forse offrendo un messaggio di avviso che StoreKit Mostra automaticamente più dettagliato.

