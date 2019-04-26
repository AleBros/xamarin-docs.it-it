---
title: Le transazioni e la verifica in xamarin. IOS
description: Questo documento descrive come consentire per il ripristino degli ultimi acquisti in un'app xamarin. IOS. Illustra anche modi per proteggere gli acquisti e prodotti fornita dal server.
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 83f5fd233c004271169a4d00d0a65e70aa925b95
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369108"
---
# <a name="transactions-and-verification-in-xamarinios"></a>Le transazioni e la verifica in xamarin. IOS

## <a name="restoring-past-transactions"></a>Il ripristino di oltre le transazioni

Se l'applicazione supporta i tipi di prodotti che è possibile ripristinare, è necessario includere alcuni elementi dell'interfaccia utente per consentire agli utenti di ripristinare tali acquisti.
Questa funzionalità consente a un cliente per aggiungere il prodotto a dispositivi aggiuntivi o ripristinare il prodotto allo stesso dispositivo dopo venga cancellato pulita o la rimozione e reinstallazione dell'app. I seguenti tipi di prodotto sono ripristinabili:

-  Prodotti non riproducibile
-  Sottoscrizioni di auto-rinnovabile
-  Sottoscrizioni gratuite

Il processo di ripristino deve aggiornare il record è mantenere sul dispositivo per soddisfare i prodotti. Il cliente può scegliere di ripristinare in qualsiasi momento, in uno qualsiasi dei propri dispositivi. Il processo di ripristino invia nuovamente tutte le transazioni precedenti per tale utente. il codice dell'applicazione deve quindi determinare l'azione da intraprendere con queste informazioni (ad esempio, verifica se è già presente un record di acquisto nel dispositivo e non, creando un record di acquisto e attivazione del prodotto per l'utente).

### <a name="implementing-restore"></a>Implementazione di ripristino

L'interfaccia utente **ripristinare** pulsante chiama il metodo seguente, che attiva RestoreCompletedTransactions il `SKPaymentQueue`.

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit invierà la richiesta di ripristino per i server Apple in modo asincrono.   
   
Poiché il `CustomPaymentObserver` è registrato come un osservatore di transazione, riceveranno i messaggi quando i server Apple risponderanno. La risposta conterrà tutte le transazioni che utente effettuate in questa applicazione (in tutti i dispositivi). Il codice scorre ogni transazione, rileva lo stato di ripristinata e chiama il `UpdatedTransactions` metodo elaborarlo come illustrato di seguito:

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

Se non sono presenti prodotti per l'utente, che è possibile ripristinare `UpdatedTransactions` non viene chiamato.   
   
Il codice possibili più semplice per ripristinare una determinata transazione nell'esempio esegue le stesse azioni quando un acquisto avviene, tranne il fatto che il `OriginalTransaction` proprietà viene utilizzata per accedere all'ID prodotto:

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

Un'implementazione più sofisticata può controllare altri `transaction.OriginalTransaction` proprietà, ad esempio il numero originale di data e la ricezione. Queste informazioni saranno utili per alcuni tipi di prodotto (ad esempio le sottoscrizioni).

#### <a name="restore-completion"></a>Ripristinare il completamento

Il `CustomPaymentObserver` dispone di due metodi aggiuntivi che verranno chiamati da StoreKit quando il processo di ripristino è stata completata (riuscito o con un errore), illustrato di seguito:

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

Nell'esempio questi metodi eseguono alcuna operazione, tuttavia, un'applicazione reale è possibile scegliere di implementare un messaggio all'utente o altre funzionalità.

## <a name="securing-purchases"></a>Proteggere gli acquisti

I due esempi in questo documento usa `NSUserDefaults` per tenere traccia degli acquisti:   
   
 **Beni di consumo** – il saldo del credito acquisti è un semplice `NSUserDefaults` valore intero che viene incrementato con ogni acquisto.   
   
 **Non-sussidiarie** – ogni acquisto di filtro foto viene archiviata come una coppia chiave-valore in `NSUserDefaults`.

Usando `NSUserDefaults` mantiene il codice di esempio semplice, ma non offre una soluzione molto sicura perché potrebbe essere tecnicamente si occupano di utenti possono aggiornare le impostazioni (ignorando il meccanismo di pagamento).   
   
Nota: Applicazioni reali dovrebbero adottare un meccanismo protetto per l'archiviazione acquistati contenuto non soggette alla manomissione utente. Questa operazione potrebbe comportare la crittografia e/o altre tecniche, inclusa l'autenticazione server remoto.   
   
 Il meccanismo deve anche essere progettato per sfruttare le funzionalità di backup e ripristino incorporate di iOS, iTunes e iCloud. Ciò garantisce che dopo che un utente che ripristina un backup degli acquisti precedenti saranno immediatamente disponibili.   
   
Fare riferimento a Secure codifica Guida Apple per ulteriori linee guida specifiche iOS.

## <a name="receipt-verification-and-server-delivered-products"></a>Verifica di carico e i prodotti fornita dal Server

Gli esempi in questo documento finora hanno è costituita esclusivamente l'applicazione che comunica direttamente con i server di App Store per condurre le transazioni di acquisto, quale sbloccare caratteristiche o funzionalità già codificate nell'app.   
   
Apple offre un ulteriore livello di sicurezza di acquisto di consentendo le conferme di acquisto per la verifica in modo indipendente da un altro server, che può essere utile per convalidare una richiesta prima di recapitare contenuto digitale come parte di un acquisto (ad esempio un libro digitale o Magazine).   
   
 **Prodotti incorporati** – come negli esempi in questo documento, il prodotto viene acquistato esiste come funzionalità fornite con l'applicazione. Un acquisto in-app consente all'utente accedere alla funzionalità.
ID prodotto sono hardcoded.   
   
 **I prodotti server-recapitati** : il prodotto è costituita da contenuto scaricabile che viene archiviato in un server remoto fino a quando una transazione ha esito positivo fa sì che il contenuto da scaricare.
Esempi possono essere libri o problemi di magazine. ID prodotto generalmente sono originati da un server esterno (in cui il contenuto del prodotto è anche ospitato). Le applicazioni devono implementare un modo affidabile per la registrazione quando una transazione è stata completata, in modo che se si verifica un errore di download di contenuto può essere eseguito un tentativo senza confondere l'utente.

### <a name="server-delivered-products"></a>Prodotti fornita dal server

Del contenuto, alcuni prodotti del, ad esempio libri e riviste (o anche un livello di gioco) desidera essere scaricati da un server remoto durante il processo di acquisto. Ciò significa che un server aggiuntivo è necessaria per archiviare e trasmettere i contenuti del prodotto una volta acquistato.

#### <a name="getting-prices-for-server-delivered-products"></a>Introduzione ai prezzi per i prodotti fornita dal Server

Poiché i prodotti vengono recapitati in modalità remota, è anche possibile aggiungere più prodotti nel tempo (senza aggiornare il codice dell'app), ad esempio l'aggiunta di ulteriori libri o nuovi problemi di una rivista. In modo che l'applicazione può individuare questi prodotti di notizie e visualizzarli all'utente, il server aggiuntivo deve archiviare e fornire le informazioni.   
   
[![](transactions-and-verification-images/image38.png "Introduzione ai prezzi per i prodotti fornita dal Server")](transactions-and-verification-images/image38.png#lightbox)   
   
1. Informazioni sul prodotto devono essere archiviati in più posizioni: nel server e in iTunes Connect. Inoltre, ogni prodotto avrà i file di contenuto associati. Questi file verranno recapitati dopo un acquisto ha esito positivo.   
   
2. Quando l'utente desidera acquistare un prodotto, l'applicazione deve determinare quali prodotti sono disponibili. Queste informazioni possono essere memorizzati nella cache, ma devono essere fornite da un server remoto in cui è archiviato l'elenco master dei prodotti.   
   
3. Il server restituisce un elenco di ID prodotto per l'applicazione da analizzare.   
   
4. Quindi, l'applicazione determina a quali di questi ID prodotto per l'invio di StoreKit per recuperare i prezzi e descrizioni.   
   
5. StoreKit invia l'elenco di ID prodotto per i server Apple.   
   
6. I server di iTunes risponderanno con informazioni sul prodotto valido (descrizione e prezzo corrente).   
   
7. L'applicazione `SKProductsRequestDelegate` viene passato le informazioni sul prodotto per la visualizzazione all'utente.

#### <a name="purchasing-server-delivered-products"></a>Acquisto di prodotti fornita dal Server

Poiché il server remoto richiede un modo per convalidare che una richiesta di contenuto sia valida (ad esempio. è stata pagata per), le informazioni di ricezione vengono passate lungo per l'autenticazione. Il server remoto inoltra i dati a iTunes per la verifica e, se ha esito positivo, include il contenuto del prodotto in risposta all'applicazione.   
   
 [![](transactions-and-verification-images/image39.png "Acquisto di prodotti fornita dal Server")](transactions-and-verification-images/image39.png#lightbox)   
   
1. L'app aggiunge un `SKPayment` alla coda. Se l'utente verrà richiesto l'ID Apple e viene richiesto di confermare il pagamento necessari.   
   
2. StoreKit invia la richiesta al server per l'elaborazione.   
   
3. Quando la transazione viene completata, il server risponde con un valore di ricezione delle transazioni.   
   
4. Il `SKPaymentTransactionObserver` sottoclasse riceve la ricezione e lo elabora. Poiché il prodotto deve essere scaricato da un server, l'applicazione avvia una richiesta di rete al server remoto.   
   
5. La richiesta di download è accompagnata dai dati del carico in modo che il server remoto può verificare che è autorizzato ad accedere al contenuto. Client di rete dell'applicazione è in attesa di una risposta a questa richiesta.   
   
6. Quando il server riceve una richiesta per il contenuto, analizza i dati di conferma e invia una richiesta direttamente ai server di iTunes per verificare la ricezione sia per una transazione valida. Il server deve usare una logica per determinare se inviare la richiesta all'URL di produzione o sandbox. Suggerite da Apple sempre utilizzando l'URL di produzione e di passare a sandbox se la ricezione dello stato 21007 (ricezione sandbox inviata al server di produzione). Fare riferimento a Apple [Guida alla programmazione di ricezione convalida](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) per altri dettagli.
   
7. iTunes verifica la ricezione e restituire uno stato pari a zero se è valido.   
   
8. Il server è in attesa di risposta degli iTunes. Se riceve una risposta valida, il codice dovrebbe individuare il file di contenuto associati prodotto da includere nella risposta all'applicazione.   
  
9. L'applicazione riceve e analizza la risposta, viene salvato il contenuto di prodotto al file System del dispositivo.   
   
10. L'applicazione consente il prodotto e quindi chiama di StoreKit `FinishTransaction`. L'applicazione può quindi facoltativamente, visualizzare il contenuto acquistato (ad esempio, Mostra la prima pagina di un libro acquistata o alla rivista).

Un'implementazione alternativa per i file di contenuto di dimensioni molto grandi prodotto può comportare semplicemente se archiviare la ricezione delle transazioni nel passaggio #9 in modo che la transazione può essere completata rapidamente e fornisce un'interfaccia utente per l'utente di scaricare il contenuto effettivo del prodotto in un secondo momento. La richiesta di download successivi può inviare nuovamente il carico stored per accedere al file di contenuto di prodotto obbligatorio.

### <a name="writing-server-side-receipt-verification-code"></a>La scrittura di codice di verifica di ricezione sul lato Server

Convalida una ricevuta nel codice lato server può avvenire con una semplice HTTP POST richiesta/risposta che include i passaggi da 5 # tramite #8 nel diagramma del flusso di lavoro.   
   
Estrarre il `SKPaymentTansaction.TransactionReceipt` proprietà nell'app. Si tratta dei dati che devono essere inviate a iTunes per la verifica (passaggio #5).

Codifica Base64 i dati di ricezione delle transazioni (sia nel passaggio 5 # o #6).

Creare un semplice payload JSON simile al seguente:

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

Il codice JSON per POST HTTP [ https://buy.itunes.apple.com/verifyReceipt ](https://buy.itunes.apple.com/verifyReceipt) per la produzione o [ https://sandbox.itunes.apple.com/verifyReceipt ](https://sandbox.itunes.apple.com/verifyReceipt) per il test.   
   
 La risposta JSON conterrà le chiavi seguenti:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

Uno stato pari a zero indica una ricezione valida. Il server è possibile procedere per soddisfare il contenuto del prodotto acquistato. La chiave ricevuta contiene un dizionario JSON con le stesse proprietà di `SKPaymentTransaction` oggetto ricevuto dall'app, in modo che il codice del server può eseguire una query per recuperare informazioni quali il product_id e la quantità dell'acquisto di questo dizionario.

Vedere di Apple [Guida alla programmazione di ricezione convalida](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Introduction.html) documentazione per informazioni aggiuntive.
