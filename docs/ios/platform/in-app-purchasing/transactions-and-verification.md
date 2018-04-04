---
title: Le transazioni e la verifica
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: c8d86d0ce3119b3e104a65a170ab141484af44a7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="transactions-and-verification"></a>Le transazioni e la verifica

## <a name="restoring-past-transactions"></a>Il ripristino dopo le transazioni

Se l'applicazione supporta i tipi di prodotti che è possibile ripristinare, è necessario includere alcuni elementi dell'interfaccia utente per consentire agli utenti di ripristinare tali acquisti.
Questa funzionalità consente a un cliente per aggiungere il prodotto per altri dispositivi o per ripristinare il prodotto allo stesso dispositivo dopo viene pulita o la rimozione e reinstallazione dell'app. I seguenti tipi di prodotto sono ripristinabili:

-  Prodotti non riproducibile
-  Sottoscrizioni rinnovabile automatica
-  Sottoscrizioni gratuite


Il processo di ripristino deve aggiornare i record tenere il dispositivo per soddisfare i prodotti. Il cliente può scegliere di ripristinare in qualsiasi momento, i propri dispositivi. Il processo di ripristino invia nuovamente tutte le transazioni precedenti per tale utente. il codice dell'applicazione deve quindi determinare l'azione da intraprendere con le informazioni (ad esempio, verifica se è già presente un record di tale fornitore sul dispositivo e non, creazione di un record di acquisto e attivazione del prodotto per l'utente).

### <a name="implementing-restore"></a>Implementazione di ripristino

L'interfaccia utente **ripristinare** pulsante chiama il metodo seguente, che i trigger di RestoreCompletedTransactions il `SKPaymentQueue`.

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit invierà la richiesta di ripristino per i server Apple in modo asincrono.   
   
   
   
 Poiché il `CustomPaymentObserver` è registrato come un osservatore delle transazioni, riceverà i messaggi quando i server Apple risponderanno. La risposta conterrà tutte le transazioni che utente effettuate in questa applicazione (tra tutti i dispositivi). Il codice scorre ogni transazione, rileva la stato di ripristino e chiama il `UpdatedTransactions` metodo elaborarla, come illustrato di seguito:

```csharp
// called when the transaction status is updated
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
       case SKPaymentTransactionState.Restored:
           theManager.RestoreTransaction(transaction);
           break;
default:
           break;
       }
   }
}
```

Se non sono presenti prodotti ripristinabili per l'utente, `UpdatedTransactions` non viene chiamato.   
   
   
   
 Il codice di possibili più semplice per ripristinare una transazione specificata nell'esempio esegue le stesse azioni quando un acquisto viene eseguita, con la differenza che la `OriginalTransaction` proprietà viene utilizzata per accedere all'ID prodotto:

```csharp
public void RestoreTransaction (SKPaymentTransaction transaction)
{
   // Restored Transactions always have an 'original transaction' attached
   var productId = transaction.OriginalTransaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId); // it's as though it was purchased again
   FinishTransaction(transaction, true);
}
```

Un'implementazione più sofisticata può verificare altri `transaction.OriginalTransaction` proprietà, ad esempio il numero originale di data e di ricezione. Queste informazioni si riveleranno utili per alcuni tipi di prodotto (ad esempio le sottoscrizioni).

#### <a name="restore-completion"></a>Ripristino di completamento

Il `CustomPaymentObserver` dispone di due metodi aggiuntivi che verranno chiamati da StoreKit quando il processo di ripristino è stata completata (correttamente o con un errore), illustrato di seguito:

```csharp
public override void PaymentQueueRestoreCompletedTransactionsFinished (SKPaymentQueue queue)
{
   Console.WriteLine(" ** RESTORE Finished ");
}
public override void RestoreCompletedTransactionsFailedWithError (SKPaymentQueue queue, NSError error)
{
   Console.WriteLine(" ** RESTORE FailedWithError " + error.LocalizedDescription);
}
```

Nell'esempio questi metodi non eseguono alcuna operazione, tuttavia, un'applicazione reale è possibile scegliere di implementare un messaggio all'utente o altre funzionalità.

## <a name="securing-purchases"></a>Protezione di acquisti

I due esempi in questo documento usa `NSUserDefaults` per tenere traccia degli acquisti:   
   
 **Beni di consumo** : il saldo della carta di credito acquisti è un semplice `NSUserDefaults` valore intero che viene incrementato ad ogni acquisto.   
   
 **Beni di consumo non** : ogni acquisto filtro foto viene archiviata come una coppia chiave-valore in `NSUserDefaults`.

Utilizzando `NSUserDefaults` mantiene il codice di esempio semplice, ma non offrono una soluzione molto sicura, potrebbe essere possibile per tecnicamente offrire agli utenti di aggiornare le impostazioni (esclusione del meccanismo di pagamento).   
   
Nota: È necessario adottare un meccanismo protetto per l'archiviazione dei contenuti non soggette alla manomissione utente acquistate applicazioni reali. Questa operazione potrebbe comportare la crittografia e/o altre tecniche, ad esempio autenticazione di server remoto.   
   
 Il meccanismo di inoltre deve essere progettato per sfruttare le funzionalità predefinite di backup e ripristino di iOS, iTunes e iCloud. In questo modo che dopo che un utente viene ripristinato un backup precedenti acquisti saranno immediatamente disponibili.   
   
   
 Apple Secure la generazione di codice per vedere Guida altre linee guida specifiche della iOS.

## <a name="receipt-verification-and-server-delivered-products"></a>Verifica di ricezione e prodotti recapitati al Server

Gli esempi in questo documento finora hanno costituito esclusivamente l'applicazione di comunicare direttamente con i server di App Store per eseguire operazioni di acquisto, quale sbloccare caratteristiche o funzionalità già codificate nell'app.   
   
   
   
 Apple fornisce un ulteriore livello di sicurezza di acquisto di consentendo ricezioni di acquisto per la verifica in modo indipendente da un altro server, che può essere utile per convalidare una richiesta prima di recapitare contenuto digitale come parte di un acquisto (ad esempio un libro digitale o Magazine).   
   
   
   
 **Prodotti incorporati** – come negli esempi di questo documento, il prodotto viene acquistato esiste come funzionalità fornite con l'applicazione. Un acquisto in-app consente all'utente di accedere alla funzionalità.
ID prodotto sono hardcoded.   
   
 **I prodotti server-recapitati** : il prodotto è costituita da contenuto scaricabile che viene archiviato in un server remoto, fino a quando una transazione ha esito positivo provoca il download del contenuto.
Esempi possono essere documentazione o rivista problemi. ID prodotto in genere vengono originati da un server esterno (in cui il contenuto del prodotto è anche ospitato). Le applicazioni devono implementare un modo efficiente di registrazione quando una transazione è stata completata, in modo che se ha esito negativo di scaricare il contenuto può essere eseguito un tentativo senza confondere l'utente.

### <a name="server-delivered-products"></a>Prodotti server distribuita

Alcuni prodotti del contenuto, ad esempio libri e riviste (o anche un livello di gioco) necessario essere scaricato da un server remoto durante il processo di acquisto. Ciò significa che è necessario un server aggiuntivo per archiviare e distribuire il contenuto del prodotto una volta acquistato.

#### <a name="getting-prices-for-server-delivered-products"></a>Recupero dei prezzi per i prodotti Server distribuita

Poiché i prodotti in modalità remota vengono distribuiti, è anche possibile aggiungere più prodotti nel tempo (senza aggiornare il codice dell'applicazione), ad esempio l'aggiunta di più libri o nuovi problemi di una rivista. In modo che l'applicazione può individuare questi prodotti di news e visualizzarle all'utente, il server aggiuntivo deve archiviare e fornire le informazioni.   
   
   
   
 [![](transactions-and-verification-images/image38.png "Recupero dei prezzi per i prodotti Server distribuita")](transactions-and-verification-images/image38.png#lightbox)   
   
   
   
 1. Informazioni sul prodotto devono essere archiviati in più posizioni: il server e in iTunes Connect. Inoltre, ogni prodotto avrà i file di contenuto associati. Questi file verranno recapitati dopo un acquisto ha esito positivo.   
   
 2. Quando l'utente desidera acquistare un prodotto, l'applicazione deve determinare quali prodotti sono disponibili. Queste informazioni potrebbero essere memorizzate nella cache, ma devono essere recapitate da un server remoto in cui è memorizzato l'elenco master dei prodotti.   
   
 3. Il server restituisce un elenco di ID di prodotto per l'applicazione da analizzare.   
   
 4. Quindi, l'applicazione determina quali di questi ID prodotto da inviare al StoreKit per recuperare i prezzi e descrizioni.   
   
 5. StoreKit invia l'elenco di ID di prodotto per i server Apple.   
   
 6. I server iTunes risponderanno con le informazioni di prodotto valido (descrizione e il prezzo corrente).   
   
 7. L'applicazione `SKProductsRequestDelegate` viene passato le informazioni sul prodotto per la visualizzazione per l'utente.

#### <a name="purchasing-server-delivered-products"></a>Acquisto di prodotti recapitati al Server

Poiché il server remoto richiede una modalità di convalida che una richiesta di contenuto sia valida (ie. è stato pagato), le informazioni di ricezione viene passate per l'autenticazione. Il server remoto inoltra iTunes per la verifica che i dati e, se ha esito positivo, include il contenuto del prodotto in risposta all'applicazione.   
   
 [![](transactions-and-verification-images/image39.png "Acquisto di prodotti recapitati al Server")](transactions-and-verification-images/image39.png#lightbox)   
   
 1. Aggiunge l'app un `SKPayment` alla coda. Se necessario, l'utente verrà richiesto per l'ID Apple e viene richiesto di confermare il pagamento.   
   
 2. StoreKit invia la richiesta al server per l'elaborazione.   
   
 3. Quando la transazione è stata completata, il server risponde con un carico di transazione.   
   
 4. Il `SKPaymentTransactionObserver` sottoclasse riceve la ricezione e lo elabora. Poiché il prodotto deve essere scaricato da un server, l'applicazione avvia una richiesta di rete al server remoto.   
   
 5. La richiesta di download è accompagnata dai dati del carico in modo che sia possibile verificare il server remoto che è autorizzato ad accedere al contenuto. Il client di rete dell'applicazione è in attesa di una risposta a questa richiesta.   
   
 6. Quando il server riceve una richiesta di contenuto, analizza i dati di conferma e invia una richiesta direttamente ai server iTunes per verificare la ricezione è per una transazione valida. Il server deve utilizzare la logica per determinare se si desidera inviare la richiesta all'URL di produzione o sandbox. Apple consiglia sempre utilizzando l'URL di produzione e modificate in una sandbox, se lo stato: 21007 (ricevuta sandbox inviata al server di produzione): fare riferimento a [2259 Nota tecnica domande frequenti su #16](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html).   
   
 7. iTunes viene verificare la ricezione e viene restituito uno stato pari a zero se è valido.   
   
 8. Il server attende che la risposta di iTunes. Se riceve una risposta valida, il codice deve individuare il file di contenuto prodotto associato da includere nella risposta all'applicazione.   
   
 9. L'applicazione riceve e analizza la risposta, salvare il contenuto del prodotto al file System del dispositivo.   
   
 10. L'applicazione consente il prodotto e quindi chiama del StoreKit `FinishTransaction`. L'applicazione può quindi facoltativamente, visualizzare il contenuto acquistato (ad esempio, Mostra la prima pagina di un libro acquistata o problema rivista).

Un'implementazione alternativa per i file di contenuto di un prodotto molto grande può comportare semplicemente l'archiviazione la ricezione delle transazioni nel passaggio &#9; in modo che sia possibile completare rapidamente la transazione e un'interfaccia utente per l'utente di scaricare il contenuto di prodotti effettivi in un secondo momento. La richiesta di download successive può inviare nuovamente la ricezione stored per accedere al file di contenuto di prodotto obbligatorio.

### <a name="writing-server-side-receipt-verification-code"></a>Scrittura di codice di verifica di ricezione sul lato Server

Convalida una ricevuta nel codice sul lato server può essere eseguita con una semplice HTTP POST richiesta/risposta che include i passaggi da &#5;-#8 nel diagramma di flusso di lavoro.   
   
   
   
 Estrarre il `SKPaymentTansaction.TransactionReceipt` proprietà nell'app. Si tratta dei dati che deve essere inviato a iTunes per la verifica (passaggio &#5;).

Codifica Base64 i dati di ricezione delle transazioni (sia nel passaggio &#5; o #6).

Creare un semplice payload JSON simile al seguente:

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

JSON da POST HTTP [ https://buy.itunes.apple.com/verifyReceipt ](https://buy.itunes.apple.com/verifyReceipt) per la produzione o [ https://sandbox.itunes.apple.com/verifyReceipt ](https://sandbox.itunes.apple.com/verifyReceipt) per il testing.   
   
 La risposta JSON conterrà le chiavi seguenti:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

Uno stato pari a zero indica un valore valido di ricezione. Il server è possibile procedere per soddisfare il contenuto del prodotto acquistato. La chiave ricevuta contiene un dizionario JSON con le stesse proprietà di `SKPaymentTransaction` oggetto ricevuto dall'applicazione, pertanto il codice del server può eseguire una query questo dizionario per recuperare informazioni quali product_id e la quantità di acquisto.

Vedere Apple [verifica conferme di recapito archivio](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/StoreKitGuide/VerifyingStoreReceipts/VerifyingStoreReceipts.html#//apple_ref/doc/uid/TP40008267-CH104-SW1) documentazione per informazioni aggiuntive.

#### <a name="using-aspnet"></a>Utilizzo di ASP.NET

Per gli sviluppatori c# è disponibile un progetto open source utile denominato [APN realistici](https://github.com/Redth/APNS-Sharp) che include codice di verifica di ricezione che funziona in ASP.NET. In particolare, il `Receipt.cs` e `ReceiptVerification.cs` file il [ `Jdsoft.Apple.AppStore` ](https://github.com/Redth/APNS-Sharp/tree/master/JdSoft.Apple.AppStore) directory può essere aggiunti a un sito Web .NET di implementare facilmente i passaggi 6 di # e #8 del diagramma di flusso di lavoro Server-Delivered prodotti.   
   
   
   
 La comunicazione con i server iTunes utilizza JSON, che è facile da elaborare in c#.   
   
   
   
 Fare riferimento a acquisto In-App di Apple [ricezione convalida](https://developer.apple.com/library/ios/#releasenotes/StoreKit/IAP_ReceiptValidation/_index.html) documentazione per informazioni dettagliate su come verificare che un valore di ricezione sia valido.

### <a name="3rd-party-receipt-verification"></a>3rd party ricezione verifica

Esistono società che offrono le piattaforme per la verifica di ricezione (e altri aspetti) che è possibile utilizzare invece di compilare il proprio server. Xamarin non promuove questi servizi. si sta semplicemente sopra indicate per riferimento.

#### <a name="urban-airship"></a>Airship urbane

Airship urbano fornisce una serie di diversi servizi back-end per le app iOS, incluse le notifiche di verifica e push di ricezione.   
   
 [http://urbanairship.com/products/in-app-purchase/](http://urbanairship.com/products/in-app-purchase/)



