---
title: Acquisto di prodotti utilizzabili in Novell. iOS
description: Questo documento descrive i prodotti utilizzabili in Novell. iOS. I prodotti utilizzabili sono componenti monouso di funzionalità, ad esempio la valuta nel gioco.
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: c23515c7fc7a3fef836cba76ec30279c94150da2
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281527"
---
# <a name="purchasing-consumable-products-in-xamarinios"></a>Acquisto di prodotti utilizzabili in Novell. iOS

I prodotti utilizzabili sono i più semplici da implementare, perché non esiste alcun requisito di "ripristino". Sono utili per prodotti come la valuta nel gioco o una parte di funzionalità monouso. Gli utenti possono riacquistare i prodotti utilizzabili più volte.

## <a name="built-in-product-delivery"></a>Distribuzione predefinita del prodotto

Il codice di esempio che accompagna questo documento illustra i prodotti predefiniti, ovvero gli ID del prodotto sono hardcoded nell'applicazione, perché sono strettamente collegati al codice che sblocca la funzionalità dopo il pagamento. Il processo di acquisto può essere visualizzato in questo modo:   
   
[![Visualizzazione del processo di acquisto](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png#lightbox)     
   
 Il flusso di lavoro di base è:   
   
 1. L'app aggiunge un `SKPayment` oggetto alla coda. Se necessario, all'utente verrà richiesto l'ID Apple e verrà richiesto di confermare il pagamento.   
   
 2. StoreKit Invia la richiesta al server per l'elaborazione.   
   
 3. Al termine della transazione, il server risponde con una ricevuta di transazione.   
   
 4. La `SKPaymentTransactionObserver` sottoclasse riceve la ricevuta e la elabora.   
   
 5. L'applicazione Abilita il prodotto (mediante l' `NSUserDefaults` aggiornamento o un altro meccanismo), quindi chiama `FinishTransaction`StoreKit.

È disponibile un altro tipo di flusso di lavoro, ovvero *prodotti server* , che viene illustrato più avanti nel documento (vedere la sezione *Verifica della ricezione e prodotti distribuiti dal server*).

## <a name="consumable-products-example"></a>Esempio di prodotti utilizzabili

Il [codice InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit) contiene un progetto denominato *Consumable* che implementa una "valuta nel gioco" di base (denominata "crediti Monkey"). Nell'esempio viene illustrato come implementare due prodotti di acquisto in-app per consentire all'utente di acquistare tutti i "crediti Monkey" desiderati. in un'applicazione reale esiste anche un modo per spenderli.   
   
   
   
 L'applicazione viene visualizzata in queste schermate. ogni acquisto aggiunge più "crediti Monkey" al saldo dell'utente:   
   
   
   
 [![Ogni acquisto aggiunge più crediti Monkey al saldo degli utenti](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png#lightbox)   
   
   
   
 Le interazioni tra le classi personalizzate, StoreKit e App Store hanno un aspetto simile al seguente:   
   
   
   
 [![Interazioni tra classi personalizzate, StoreKit e App Store](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png#lightbox)

&nbsp;

### <a name="viewcontroller-methods"></a>Metodi ViewController

Oltre alle proprietà e ai metodi richiesti per recuperare le informazioni sul prodotto, il controller di visualizzazione richiede altri osservatori delle notifiche per ascoltare le notifiche relative all'acquisto. Sono solo `NSObjects` che verranno registrati e rimossi rispettivamente in `ViewWillAppear` e `ViewWillDisappear` .

```csharp
NSObject succeededObserver, failedObserver;
```

Il costruttore `SKProductsRequestDelegate` creerà anche la sottoclasse ( `InAppPurchaseManager`) che a `SKPaymentTransactionObserver` sua volta crea e registra ( `CustomPaymentObserver`).   
   
   
   
 La prima parte dell'elaborazione di una transazione di acquisto in-app consiste nel gestire la pressione del pulsante quando l'utente desidera acquistare un elemento, come illustrato nel codice seguente dall'applicazione di esempio:

```csharp
buy5Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy5ProductId);
};
buy10Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy10ProductId);
};
```

   
   
 La seconda parte dell'interfaccia utente sta gestendo la notifica che la transazione ha avuto esito positivo, in questo caso aggiornando il saldo visualizzato:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionSucceededNotification,
(notification) => {
   balanceLabel.Text = CreditManager.Balance() + " monkey credits";
});
```

Nella parte finale dell'interfaccia utente viene visualizzato un messaggio se una transazione viene annullata per qualche motivo. Nel codice di esempio un messaggio viene semplicemente scritto nella finestra di output:

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

Oltre a questi metodi sul controller di visualizzazione, una transazione di acquisto di prodotti utilizzabile richiede anche codice in `SKProductsRequestDelegate` `SKPaymentTransactionObserver`e.

### <a name="inapppurchasemanager-methods"></a>Metodi InAppPurchaseManager

Il codice di esempio implementa una serie di metodi correlati all'acquisto sulla classe InAppPurchaseManager, incluso `PurchaseProduct` il metodo che crea `SKPayment` un'istanza e la aggiunge alla coda per l'elaborazione:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

L'aggiunta del pagamento alla coda è un'operazione asincrona. L'applicazione recupera il controllo mentre StoreKit elabora la transazione e la invia ai server Apple. A questo punto, iOS verificherà che l'utente sia connesso all'App Store e chiederà di specificare un ID Apple e una password, se necessario.   
   
   
   
 Supponendo che l'utente esegua l'autenticazione con l'App Store e accetti la transazione, `SKPaymentTransactionObserver` riceverà la risposta di StoreKit e chiamerà il metodo seguente per completare la transazione e finalizzarla.

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

L'ultimo passaggio consiste nel verificare che venga inviata una notifica a StoreKit che la transazione è stata soddisfatta, `FinishTransaction`chiamando:

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

Una volta recapitato il `SKPaymentQueue.DefaultQueue.FinishTransaction` prodotto, è necessario chiamare il metodo per rimuovere la transazione dalla coda di pagamento.

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>Metodi SKPaymentTransactionObserver (CustomPaymentObserver)

StoreKit chiama il `UpdatedTransactions` metodo quando riceve una risposta dai server Apple e passa una matrice di `SKPaymentTransaction` oggetti per il codice da ispezionare. Il metodo scorre ogni transazione ed esegue una funzione diversa in base allo stato della transazione (come illustrato di seguito):

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

Il `CompleteTransaction` metodo è stato trattato in precedenza in questa sezione: Salva i dettagli di `NSUserDefaults`acquisto in, finalizza la transazione con StoreKit e infine invia una notifica all'interfaccia utente per l'aggiornamento.

### <a name="purchasing-multiple-products"></a>Acquisto di più prodotti

Se è opportuno che l'applicazione acquisti più prodotti, usare la `SKMutablePayment` classe e impostare il campo Quantity:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

Il codice che gestisce la transazione completata deve anche eseguire una query sulla proprietà Quantity per soddisfare correttamente l'acquisto:

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

Quando l'utente acquista più quantità, l'avviso di conferma StoreKit rifletterà la quantità, il prezzo unitario e il prezzo totale che verrà addebitato, come illustrato nello screenshot seguente:

[![Conferma di un acquisto](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png#lightbox)

## <a name="handling-network-outages"></a>Gestione delle interruzioni della rete

Gli acquisti in-app richiedono una connessione di rete funzionante per StoreKit per comunicare con i server Apple. Se una connessione di rete non è disponibile, l'acquisto in-app non sarà disponibile.

### <a name="product-requests"></a>Richieste di prodotti

Se la rete non è disponibile durante la `SKProductRequest`creazione di `RequestFailed` un oggetto, viene chiamato `InAppPurchaseManager`il metodo della `SKProductsRequestDelegate` sottoclasse (), come illustrato di seguito:

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

Il ViewController quindi ascolta la notifica e visualizza un messaggio nei pulsanti di acquisto:

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

Poiché una connessione di rete può essere temporanea sui dispositivi mobili, le applicazioni potrebbero voler monitorare lo stato della rete tramite il Framework SystemConfiguration e ripetere l'esercitazione quando è disponibile una connessione di rete. Vedere Apple o il che lo usa.

### <a name="purchase-transactions"></a>Transazioni di acquisto

Se possibile, la coda di pagamento StoreKit archivia e invia le richieste di acquisto, quindi l'effetto di un'interruzione di rete varia a seconda del momento in cui la rete non è riuscita durante il processo di acquisto.   
   
   
   
 Se si verifica un errore durante una transazione, la `SKPaymentTransactionObserver` sottoclasse `CustomPaymentObserver`() avrà il `UpdatedTransactions` metodo chiamato e la `SKPaymentTransaction` classe sarà in stato di errore.

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

Il `FailedTransaction` metodo rileva se l'errore è stato causato dall'annullamento dell'utente, come illustrato di seguito:

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

Anche se una transazione ha esito `FinishTransaction` negativo, è necessario chiamare il metodo per rimuovere la transazione dalla coda di pagamento:

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

Il codice di esempio invia quindi una notifica in modo che ViewController possa visualizzare un messaggio. Se l'utente ha annullato la transazione, le applicazioni non dovrebbero visualizzare un ulteriore messaggio. Altri codici di errore che possono verificarsi includono:

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>Gestione delle restrizioni

Le **impostazioni > funzionalità di restrizione > generale** di iOS consentono agli utenti di bloccare determinate funzionalità del dispositivo.   
   
   
   
 È possibile eseguire una query se l'utente è autorizzato a effettuare acquisti in-app `SKPaymentQueue.CanMakePayments` tramite il metodo. Se restituisce false, l'utente non potrà accedere all'acquisto in-app. StoreKit visualizzerà automaticamente un messaggio di errore all'utente se viene effettuato un tentativo di acquisto. Selezionando questo valore, l'applicazione può invece nascondere i pulsanti di acquisto o eseguire altre azioni per aiutare l'utente.   
   
   
   
 Nel file il `CanMakePayments` metodo esegue il wrapping della funzione StoreKit come segue: `InAppPurchaseManager.cs`

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

Per testare questo metodo, usare la funzionalità **restrizioni** di iOS per disabilitare gli **acquisti in-app**:   
   
   
   
 [![Usare la funzionalità restrizioni di iOS per disabilitare gli acquisti in-app](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png#lightbox)   
   
   
   
 Questo esempio di codice `ConsumableViewController` di reagisce `CanMakePayments` alla restituzione di false visualizzando il testo di **AppStore disabilitato** sui pulsanti disabilitati.

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

L'applicazione ha un aspetto simile al seguente quando la funzionalità **acquisti in-app** è limitata, i pulsanti di acquisto sono disabilitati.   
   
   
   
 [![L'applicazione ha un aspetto simile al seguente quando la funzionalità acquisti in-app è limitata. i pulsanti di acquisto sono disabilitati](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png#lightbox)   
   
   
   

Le informazioni sul prodotto possono comunque essere `CanMakePayments` richieste quando è false, quindi l'app può comunque recuperare e visualizzare i prezzi. Ciò significa che se è stato `CanMakePayments` rimosso il controllo dal codice, i pulsanti di acquisto sarebbero ancora attivi. Tuttavia, quando viene effettuato un tentativo di acquisto, l'utente visualizzerà un messaggio che indica che gli **acquisti in-app non sono consentiti** (generati da StoreKit quando la coda di pagamento è accesso eseguito):   
   
   
   
 [![Gli acquisti in-app non sono consentiti](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png#lightbox)   
   
   
   
 Per le applicazioni reali è possibile adottare un approccio diverso per gestire la restrizione, ad esempio nascondere i pulsanti nel suo complesso ed eventualmente offrire un messaggio più dettagliato rispetto all'avviso visualizzato automaticamente da StoreKit.

