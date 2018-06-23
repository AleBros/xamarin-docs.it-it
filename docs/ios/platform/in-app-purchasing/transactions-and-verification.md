---
title: Le transazioni e la verifica in xamarin. IOS
description: Questo documento descrive come consentire per il ripristino degli ultimi acquisti in un'app xamarin. IOS. Illustra anche modi per proteggere gli acquisti e prodotti recapitati al server.
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: ac24c70ed16c6439480903b807add38fb388b4dd
ms.sourcegitcommit: 0be3d10bf08d1f76eab109eb891ed202615ac399
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321389"
---
# <a name="transactions-and-verification-in-xamarinios"></a>Le transazioni e la verifica in xamarin. IOS

## <a name="restoring-past-transactions"></a>Ripristinare le transazioni passata

Se l'applicazione supporta tipi di prodotti che sono ripristinabili, è necessario includere alcuni elementi dell'interfaccia utente per consentire agli utenti di ripristinare tali acquisti.
Questa funzionalità consente a un cliente per aggiungere ulteriori dispositivi del prodotto oppure per ripristinare il prodotto allo stesso dispositivo dopo viene pulita o la rimozione e reinstallazione dell'app. I seguenti tipi di prodotto sono ripristinabili:

-  Prodotti non riproducibile
-  Sottoscrizioni Auto rinnovabile
-  Sottoscrizioni gratuite

Il processo di ripristino deve aggiornare i record mantenere sul dispositivo per soddisfare i prodotti. Il cliente può scegliere di ripristinare in qualsiasi momento, in uno qualsiasi dei propri dispositivi. Il processo di ripristino invia nuovamente tutte le transazioni precedenti per tale utente. il codice dell'applicazione deve quindi determinare l'azione da intraprendere con le informazioni (ad esempio, verifica se è già presente un record di tale fornitore sul dispositivo e non, la creazione di un record di acquisto e l'abilitazione del prodotto per l'utente).

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
   
Poiché il `CustomPaymentObserver` è registrato come un osservatore delle transazioni, riceverà i messaggi quando i server Apple risponderanno. La risposta conterrà tutte le transazioni di cui che questo utente effettuate in questa applicazione (tra tutti i dispositivi). Il codice scorre ogni transazione, rileva lo stato di ripristino e chiamate di `UpdatedTransactions` metodo elaborarla come illustrato di seguito:

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
   
Il codice più semplice possibile ripristinare una transazione specificata nell'esempio non le stesse azioni quando un acquisto viene eseguita, con la differenza che la `OriginalTransaction` proprietà viene utilizzata per accedere all'ID prodotto:

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

Un'implementazione più sofisticata può verificare altri `transaction.OriginalTransaction` proprietà, ad esempio il numero originale di data e di ricezione. Queste informazioni saranno utili per alcuni tipi di prodotto (ad esempio le sottoscrizioni).

#### <a name="restore-completion"></a>Ripristinare il completamento

Il `CustomPaymentObserver` dispone di due metodi aggiuntivi che verranno chiamati da StoreKit quando il processo di ripristino è stata completata (correttamente o con un errore), vedere di seguito:

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

Nell'esempio questi metodi non eseguono alcuna operazione, tuttavia, un'applicazione reale potrebbe scegliere di implementare un messaggio all'utente o altre funzionalità.

## <a name="securing-purchases"></a>La protezione degli acquisti

I due esempi in questo documento usa `NSUserDefaults` per tenere traccia degli acquisti:   
   
 **Beni di consumo** – il saldo della carta di credito acquisti è un semplice `NSUserDefaults` valore intero che viene incrementato ad ogni acquisto.   
   
 **Non sussidiarie** – ogni acquisto filtro foto viene archiviata come una coppia chiave-valore in `NSUserDefaults`.

Utilizzando `NSUserDefaults` mantiene il codice di esempio semplice, ma non offre una soluzione molto sicura come potrebbe avvenire per tecnicamente offrire agli utenti di aggiornare le impostazioni (esclusione del meccanismo di pagamento).   
   
Nota: È necessario adottare un meccanismo protetto per l'archiviazione acquistati contenuti non soggette alla manomissione utente applicazioni reali. Questa operazione potrebbe comportare la crittografia e/o altre tecniche, ad esempio autenticazione di server remoto.   
   
 Il meccanismo deve anche essere progettato per sfruttare le funzionalità predefinite di backup e ripristino di iOS, iTunes e iCloud. In questo modo che dopo che un utente viene ripristinato un backup degli acquisti precedenti saranno immediatamente disponibili.   
   
Apple Secure la generazione di codice per vedere Guida altre linee guida specifiche per iOS.

## <a name="receipt-verification-and-server-delivered-products"></a>Verifica del carico e i prodotti recapitati al Server

Gli esempi in questo documento finora sono comprendeva esclusivamente l'applicazione comunica direttamente con i server di App Store di condurre transazioni di acquisto, quale sbloccare caratteristiche o funzionalità già codificate nell'app.   
   
Apple fornisce un ulteriore livello di sicurezza di acquisto di consentendo le conferme di acquisto in modo indipendente da verificare con un altro server, che può essere utile per convalidare una richiesta prima di recapitare contenuto digitale come parte di un acquisto (ad esempio un libro digitale o Magazine).   
   
 **Prodotti incorporati** – come gli esempi in questo documento, il prodotto viene acquistato esiste come funzionalità fornite con l'applicazione. Un acquisto in-app consente all'utente di accedere alla funzionalità.
Gli ID prodotto sono hardcoded.   
   
 **Server-recapitati prodotti** : il prodotto è costituita da contenuto scaricabile che viene archiviato in un server remoto fino a quando una transazione con esito positivo provoca il download del contenuto.
Esempi possono essere documentazione o rivista problemi. ID prodotto in genere vengono originati da un server esterno (in cui il contenuto del prodotto è anche ospitato). Le applicazioni devono implementare un modo efficiente di registrazione quando una transazione è stata completata, in modo che se ha esito negativo di scaricare il contenuto può essere eseguito un tentativo senza confondere l'utente.

### <a name="server-delivered-products"></a>Prodotti recapitati al server

Alcuni prodotti del contenuto, ad esempio documentazione e riviste (o anche un livello di gioco) necessario essere scaricato da un server remoto durante il processo di acquisto. In tal caso che è necessario un server aggiuntivo per archiviare e distribuire il contenuto del prodotto una volta acquistato.

#### <a name="getting-prices-for-server-delivered-products"></a>Recupero di prezzi per i prodotti recapitati al Server

Poiché i prodotti vengono recapitati in modalità remota, è anche possibile aggiungere più prodotti nel tempo (senza aggiornare il codice dell'app), ad esempio l'aggiunta di più libri o nuovi problemi di una rivista. In modo che l'applicazione può individuare questi prodotti di news e visualizzarle all'utente, il server aggiuntivo deve archiviare e fornire le informazioni.   
   
[![](transactions-and-verification-images/image38.png "Recupero di prezzi per i prodotti recapitati al Server")](transactions-and-verification-images/image38.png#lightbox)   
   
1. Informazioni sul prodotto devono essere archiviati in più posizioni: nel server e in iTunes Connect. Inoltre, ogni prodotto avrà i file di contenuto è associati. Questi file verranno recapitati dopo un acquisto esito positivo.   
   
2. Quando l'utente desidera acquistare un prodotto, l'applicazione deve determinare quali prodotti sono disponibili. Queste informazioni potrebbero essere memorizzate nella cache, ma devono essere recapitate da un server remoto in cui è archiviato l'elenco master dei prodotti.   
   
3. Il server restituisce un elenco di ID di prodotto per l'applicazione da analizzare.   
   
4. Quindi, l'applicazione determina a quali di questi ID prodotto da inviare a StoreKit per recuperare i prezzi e descrizioni.   
   
5. StoreKit invia l'elenco di ID di prodotto per i server Apple.   
   
6. I server iTunes risponderanno con le informazioni sul prodotto valido (descrizione e il prezzo corrente).   
   
7. L'applicazione `SKProductsRequestDelegate` viene passato le informazioni sul prodotto per la visualizzazione per l'utente.

#### <a name="purchasing-server-delivered-products"></a>Acquisto di prodotti recapitati al Server

Poiché il server remoto richiede un modo per convalidare che una richiesta di contenuto sia valida (ie. è stato pagato), le informazioni di ricezione vengono passate lungo per l'autenticazione. Il server remoto inoltra i dati a iTunes per la verifica e, se ha esito positivo, include il contenuto del prodotto in risposta all'applicazione.   
   
 [![](transactions-and-verification-images/image39.png "Acquisto di prodotti recapitati al Server")](transactions-and-verification-images/image39.png#lightbox)   
   
1. Aggiunge l'app un `SKPayment` alla coda. Se richiesto all'utente verrà richiesto per l'ID Apple e viene richiesto di confermare il pagamento.   
   
2. StoreKit invia la richiesta al server per l'elaborazione.   
   
3. Quando la transazione è stata completata, il server risponde con un valore di ricezione delle transazioni.   
   
4. Il `SKPaymentTransactionObserver` sottoclasse riceve il valore di ricezione e lo elabora. Poiché il prodotto deve essere scaricato da un server, l'applicazione avvia una richiesta di rete al server remoto.   
   
5. La richiesta di download è accompagnata dai dati del carico in modo che il server remoto è possibile verificare che è autorizzato ad accedere al contenuto. Client di rete dell'applicazione in attesa di una risposta a questa richiesta.   
   
6. Quando il server riceve una richiesta per il contenuto, analizza i dati di conferma e invia una richiesta direttamente ai server iTunes per verificare la ricezione è per una transazione valida. Il server deve utilizzare una logica per determinare se inviare la richiesta all'URL di produzione o sandbox. Apple suggerisce sempre utilizzando l'URL di produzione ed essere passati alla sandbox se di ricevere lo stato 21007 (ricezione sandbox inviata al server di produzione). Fare riferimento a Apple [Guida per programmatori ricezione convalida](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) per altri dettagli.
   
7. iTunes controlla la ricezione e restituisce uno stato pari a zero se è valido.   
   
8. Il server attende risposta degli iTunes. Se riceve una risposta valida, il codice deve trovare il file di contenuto associati prodotto da includere nella risposta all'applicazione.   
  
9. L'applicazione riceve e analizza la risposta, viene salvato il contenuto di prodotto al file System del dispositivo.   
   
10. L'applicazione consente il prodotto e quindi chiama del StoreKit `FinishTransaction`. L'applicazione può quindi facoltativamente, visualizzare il contenuto acquistato (ad esempio, Mostra la prima pagina di un libro acquistata o problemi rivista).

Un'implementazione alternativa per i file di contenuto di dimensioni molto grandi prodotto può comportare semplicemente l'archiviazione la ricezione delle transazioni nel passaggio 9 # in modo che la transazione può essere completata rapidamente e fornendo un'interfaccia utente per l'utente a scaricare il contenuto di prodotti effettivi in un secondo momento. La richiesta di download successive può inviare nuovamente la ricezione stored per accedere al file di contenuto di prodotto obbligatorio.

### <a name="writing-server-side-receipt-verification-code"></a>La scrittura di codice di verifica carico lato Server

La convalida di un valore di ricezione nel codice lato server può essere eseguita con una semplice HTTP POST richiesta/risposta che include i passaggi 5 #-#8 nel diagramma di flusso di lavoro.   
   
Estrarre il `SKPaymentTansaction.TransactionReceipt` proprietà nell'app. Si tratta dei dati che deve essere inviato a iTunes per la verifica (passaggio 5 #).

Codifica Base64 i dati di ricezione delle transazioni (sia nel passaggio 5 # o #6).

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

Uno stato pari a zero indica un valore di ricezione valido. Il server è possibile procedere per soddisfare il contenuto del prodotto acquistato. La chiave ricevuta contiene un dizionario JSON con le stesse proprietà di `SKPaymentTransaction` oggetto che è stato ricevuto dall'app, in modo che il codice del server può eseguire una query questo dizionario per recuperare informazioni quali product_id e la quantità di acquisto.

Vedere Apple [Guida per programmatori ricezione convalida](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Introduction.html) documentazione per informazioni aggiuntive.
