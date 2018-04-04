---
title: Acquisto di prodotti di consumo
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 5c2c84c044ff41cced2c97e414502faff45341ec
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="purchasing-consumable-products"></a>Acquisto di prodotti di consumo

I prodotti utilizzabili sono più semplici da implementare, poiché non c'è Nessun requisito 'restore'. Sono utili per i prodotti come valuta di gioco o una parte a utilizzo singolo della funzionalità. Gli utenti possono acquistare nuovamente prodotti consumo over-e-over nuovamente.

## <a name="built-in-product-delivery"></a>Recapito del prodotto incorporata

Il codice di esempio che accompagna questo documento illustra i prodotti incorporati: gli ID prodotto sono hardcoded nell'applicazione in quanto essi sono strettamente collegate al codice che sblocca le funzionalità dopo il pagamento. Il processo di acquisto può essere visualizzato come segue:   
   
[![La visualizzazione di processo di acquisto](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png#lightbox)     
   
 Il flusso di lavoro di base è:   
   
   
   
 1. Aggiunge l'app un `SKPayment` alla coda. Se necessario, l'utente verrà richiesto per l'ID Apple e viene richiesto di confermare il pagamento.   
   
 2. StoreKit invia la richiesta al server per l'elaborazione.   
   
 3. Quando la transazione è stata completata, il server risponde con un carico di transazione.   
   
 4. Il `SKPaymentTransactionObserver` sottoclasse riceve la ricezione e lo elabora.   
   
 5. L'applicazione consente il prodotto (aggiornando `NSUserDefaults` o un altro meccanismo), quindi chiama del StoreKit `FinishTransaction`.

Un altro tipo di flusso di lavoro: *Server-Delivered prodotti* , ovvero descritte più avanti in questo documento (vedere la sezione *verifica ricevuta e i prodotti Server-Delivered*).

## <a name="consumable-products-example"></a>Esempio di prodotti consumo

Il [InAppPurchaseSample codice](https://developer.xamarin.com/samples/monotouch/StoreKit/) contiene un progetto denominato *sussidiarie* che implementa un 'di gioco currency' (denominata "apportare crediti") di base. Nell'esempio viene illustrato come implementare i due prodotti di acquisto in-app per consentire all'utente per l'acquisto come molti "crediti monkey" preferita – in un'applicazione reale sono anche sarebbe in grado di spesa li!   
   
   
   
 L'applicazione viene visualizzato in queste schermate: ogni acquisto aggiunge ulteriori "crediti monkey" al saldo dell'utente:   
   
   
   
 [![Ogni fornitore aggiunge ulteriori crediti monkey al saldo di utenti](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png#lightbox)   
   
   
   
 Le interazioni tra le classi personalizzate, StoreKit e l'archivio di App è simile al seguente:   
   
   
   
 [![Le interazioni tra le classi personalizzate, StoreKit e App Store](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png#lightbox)

&nbsp;

### <a name="viewcontroller-methods"></a>Metodi ViewController

Oltre alle proprietà e metodi necessari per recuperare informazioni sul prodotto, il controller di visualizzazione richiede agli osservatori di notifica aggiuntivo per l'ascolto per le notifiche relative al fornitore. Si tratta semplicemente `NSObjects` che verrà registrato e rimossa in `ViewWillAppear` e `ViewWillDisappear` rispettivamente.

```csharp
NSObject succeededObserver, failedObserver;
```

Il costruttore verrà inoltre creato il `SKProductsRequestDelegate` sottoclasse ( `InAppPurchaseManager`) che a sua volta crea e registra il `SKPaymentTransactionObserver` ( `CustomPaymentObserver`).   
   
   
   
 La prima parte dell'elaborazione di una transazione di acquisto in-app è per gestire la pressione del pulsante quando l'utente desidera acquistare, come illustrato nel codice seguente dall'applicazione di esempio:

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

La parte finale dell'interfaccia utente viene visualizzato un messaggio se una transazione viene annullata per qualche motivo. Nell'esempio di codice viene semplicemente scritto un messaggio nella finestra di output:

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

Oltre a questi metodi nel controller di visualizzazione, una transazione di acquisto del prodotto utilizzabile anche richiede codice sul `SKProductsRequestDelegate` e `SKPaymentTransactionObserver`.

### <a name="inapppurchasemanager-methods"></a>Metodi InAppPurchaseManager

L'esempio di codice implementa un numero di acquistare i relativi metodi sulla classe InAppPurchaseManager, inclusi il `PurchaseProduct` metodo che crea un `SKPayment` istanza e lo aggiunge alla coda per l'elaborazione:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

Aggiungendo il pagamento alla coda è un'operazione asincrona. L'applicazione otterrà di nuovo controllo mentre StoreKit elabora la transazione e la invia al server di Apple. È a questo punto che si verificherà che iOS l'utente è connesso all'App Store e richiedere un ID Apple e una password se necessario.   
   
   
   
 Supponendo che l'utente correttamente l'autenticazione con l'archivio di App e accetta di transazione, il `SKPaymentTransactionObserver` riceverà una risposta del StoreKit e chiamare il metodo seguente per completare la transazione e completa le.

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

L'ultimo passaggio consiste nell'assicurarsi di notificare a StoreKit che siano soddisfatti, la transazione completata chiamando `FinishTransaction`:

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

Una volta che viene recapitato il prodotto, `SKPaymentQueue.DefaultQueue.FinishTransaction` deve essere chiamato per rimuovere la transazione dalla coda di pagamento.

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>Metodi SKPaymentTransactionObserver (CustomPaymentObserver)

Chiamate StoreKit il `UpdatedTransactions` metodo quando riceve una risposta dal server di Apple e passa una matrice di `SKPaymentTransaction` oggetti per il codice controllare. Il metodo esegue il ciclo di ogni transazione ed esegue una funzione diversa in base allo stato di transazione (come illustrato di seguito):

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

Il `CompleteTransaction` metodo è stato analizzato in precedenza in questa sezione: Salva i dettagli di acquisto per `NSUserDefaults`, completa la transazione con StoreKit e infine invia una notifica all'interfaccia utente di aggiornamento.

### <a name="purchasing-multiple-products"></a>Acquistare più prodotti

Se è utile nell'applicazione per acquistare più prodotti, utilizzare il `SKMutablePayment` e impostare il campo Quantity:

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

Il codice di gestione della transazione completata deve anche eseguire una query la proprietà quantità per completare correttamente l'acquisto:

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

Quando l'utente acquista più, l'avviso di conferma StoreKit rifletterà la quantità, il prezzo unitario e il prezzo totale che verrà addebitati essi, come illustrato nella schermata seguente:

[![Conferma acquisto](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png#lightbox)

## <a name="handling-network-outages"></a>Gestione delle interruzioni della rete

Acquisti in-app richiedono una connessione di rete funzionante per StoreKit comunicare con i server Apple. Se non è disponibile una connessione di rete, quindi di acquisto in-app non sarà disponibile.

### <a name="product-requests"></a>Richieste di prodotto

Se la rete non è disponibile durante la creazione un `SKProductRequest`, `RequestFailed` metodo il `SKProductsRequestDelegate` sottoclasse ( `InAppPurchaseManager`) viene chiamato, come illustrato di seguito:

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

Il ViewController quindi attende la notifica e viene visualizzato un messaggio nei pulsanti di acquisto:

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

Poiché una connessione di rete può essere temporanea nei dispositivi mobili, le applicazioni possono desidera monitorare lo stato di rete utilizzando il framework SystemConfiguration e ritentare quando è disponibile una connessione di rete. Fare riferimento a di Apple o che utilizza.

### <a name="purchase-transactions"></a>Le transazioni di acquisto

La coda di pagamento StoreKit archivierà e acquisto inoltro chiede se possibile, quindi l'effetto di un'interruzione di rete variano a seconda se la rete non è riuscita durante il processo di acquisto.   
   
   
   
 Se si verifica un errore durante una transazione, il `SKPaymentTransactionObserver` sottoclasse ( `CustomPaymentObserver`) avrà il `UpdatedTransactions` metodo chiamato e `SKPaymentTransaction` classe sarà nello stato non riuscito.

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

Il `FailedTransaction` metodo rileva se l'errore è dovuto all'annullamento di utente, come illustrato di seguito:

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

Anche se una transazione non riesce, il `FinishTransaction` metodo deve essere chiamato per rimuovere la transazione dalla coda di pagamento:

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

Nell'esempio di codice, quindi, invia una notifica in modo che il ViewController è possibile visualizzare un messaggio. Applicazione non deve visualizzare un ulteriore messaggio se l'utente ha annullato la transazione. Altri codici di errore che potrebbero verificarsi includono:

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>Limitazioni di gestione

Il **Impostazioni > generale > restrizioni** funzionalità di iOS consente agli utenti di bloccare alcune funzionalità del dispositivo.   
   
   
   
 È possibile eseguire una query se l'utente è consentito effettuare acquisti in-app tramite il `SKPaymentQueue.CanMakePayments` metodo. Se il risultato è false l'utente non può accedere acquisto in-app. StoreKit verrà automaticamente visualizzato un messaggio di errore all'utente se viene tentato un acquisto. Selezionando questo valore l'applicazione può invece nascondere i pulsanti di acquisto o eseguire altre azioni per consentire all'utente.   
   
   
   
 Nel `InAppPurchaseManager.cs` file il `CanMakePayments` metodo include la funzione StoreKit simile al seguente:

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

Per testare questo metodo, utilizzare il **restrizioni** funzionalità di iOS per disabilitare **acquisti In-App**:   
   
   
   
 [![Utilizzare la funzionalità di restrizioni di iOS per disabilitare gli acquisti In-App](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png#lightbox)   
   
   
   
 Questo codice di esempio da `ConsumableViewController` reagisce al `CanMakePayments` la restituzione di false visualizzando **AppStore disabilitato** testo dei pulsanti disabilitati.

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

L'applicazione simile a questa operazione quando il **acquisti In-App** funzionalità è limitata: i pulsanti di acquisto sono disabilitati.   
   
   
   
 [![L'applicazione è simile al seguente quando gli acquisti In-App è limitato all'acquisto pulsanti sono disabilitati](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png#lightbox)   
   
   
   

Informazioni sul prodotto può essere ancora richiesto quando `CanMakePayments` è false, pertanto l'app può comunque recuperare e visualizzare i prezzi. Ciò significa che se sono state rimosse le `CanMakePayments` controllo dal codice i pulsanti di acquisto verrebbero comunque essere attivo, tuttavia, quando un acquisto viene tentato l'utente verrà visualizzato un messaggio che **acquisti In-app non sono consentiti** (generati da StoreKit Quando la coda di pagamento è possibile accedere):   
   
   
   
 [![Acquisti in-app non sono consentiti.](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png#lightbox)   
   
   
   
 Applicazioni reali possono adottare un approccio diverso per la gestione di restrizione, ad esempio nascondere completamente i pulsanti e forse offerta di un messaggio di avviso indicante automaticamente StoreKit più dettagliato.

