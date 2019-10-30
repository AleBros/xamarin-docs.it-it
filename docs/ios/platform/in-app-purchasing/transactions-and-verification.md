---
title: Transazioni e verifica in Novell. iOS
description: Questo documento descrive come consentire il ripristino degli acquisti precedenti in un'app Novell. iOS. Vengono inoltre illustrati i modi per proteggere gli acquisti e i prodotti distribuiti dal server.
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: d92de14dc42f7c20a1f25b6454623c7ad4441e8a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032292"
---
# <a name="transactions-and-verification-in-xamarinios"></a>Transazioni e verifica in Novell. iOS

## <a name="restoring-past-transactions"></a>Ripristino di transazioni passate

Se l'applicazione supporta i tipi di prodotto ripristinabili, è necessario includere alcuni elementi dell'interfaccia utente per consentire agli utenti di ripristinare tali acquisti.
Questa funzionalità consente a un cliente di aggiungere il prodotto a dispositivi aggiuntivi o di ripristinare il prodotto sullo stesso dispositivo dopo la cancellazione o la rimozione e la reinstallazione dell'app. I tipi di prodotto seguenti sono ripristinabili:

- Prodotti non utilizzabili
- Sottoscrizioni rinnovate automaticamente
- Sottoscrizioni gratuite

Il processo di ripristino dovrebbe aggiornare i record conservati sul dispositivo per soddisfare i prodotti. Il cliente può scegliere di eseguire il ripristino in qualsiasi momento, su qualsiasi dispositivo. Il processo di ripristino invia nuovamente tutte le transazioni precedenti per tale utente; il codice dell'applicazione deve quindi determinare l'azione da intraprendere con tali informazioni (ad esempio, verificare se è già presente un record di tale acquisto nel dispositivo e, in caso contrario, creare un record dell'acquisto e abilitare il prodotto per l'utente).

### <a name="implementing-restore"></a>Implementazione del ripristino

Il pulsante **ripristino** interfaccia utente chiama il metodo seguente, che attiva RestoreCompletedTransactions nel `SKPaymentQueue`.

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit invierà la richiesta di ripristino ai server Apple in modo asincrono.   
   
Poiché il `CustomPaymentObserver` viene registrato come Observer di transazione, riceverà messaggi quando i server Apple rispondono. La risposta conterrà tutte le transazioni eseguite dall'utente in questa applicazione (in tutti i dispositivi). Il codice scorre ogni transazione, rileva lo stato ripristinato e chiama il metodo `UpdatedTransactions` per elaborarlo, come illustrato di seguito:

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

Se per l'utente non sono presenti prodotti ripristinabili, `UpdatedTransactions` non viene chiamato.   
   
Il codice più semplice possibile per ripristinare una determinata transazione nell'esempio esegue le stesse operazioni di quando viene acquistato un acquisto, ad eccezione del fatto che la proprietà `OriginalTransaction` viene utilizzata per accedere all'ID prodotto:

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

Un'implementazione più sofisticata può verificare altre proprietà `transaction.OriginalTransaction`, ad esempio la data originale e il numero di ricezione. Queste informazioni saranno utili per alcuni tipi di prodotto, ad esempio le sottoscrizioni.

#### <a name="restore-completion"></a>Completamento ripristino

Il `CustomPaymentObserver` dispone di due metodi aggiuntivi che verranno chiamati da StoreKit al termine del processo di ripristino (esito positivo o negativo), illustrato di seguito:

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

Nell'esempio questi metodi non eseguono alcuna operazione, tuttavia un'applicazione reale può scegliere di implementare un messaggio per l'utente o altre funzionalità.

## <a name="securing-purchases"></a>Protezione degli acquisti

I due esempi in questo documento usano `NSUserDefaults` per tenere traccia degli acquisti:   
   
 Beni di **consumo** : il "saldo" degli acquisti di credito è un semplice `NSUserDefaults` valore intero che viene incrementato a ogni acquisto.   
   
 **Non materiali di consumo** : ogni acquisto di filtro foto viene archiviato come coppia chiave-valore in `NSUserDefaults`.

L'uso di `NSUserDefaults` mantiene il codice di esempio semplice, ma non offre una soluzione molto sicura, perché potrebbe essere possibile per gli utenti tecnicamente aggiornare le impostazioni (ignorando il meccanismo di pagamento).   
   
Nota: le applicazioni reali devono adottare un meccanismo sicuro per archiviare il contenuto acquistato che non è soggetto alla manomissione dell'utente. Questa operazione può comportare la crittografia e/o altre tecniche, inclusa l'autenticazione del server remoto.   
   
 Il meccanismo deve anche essere progettato per sfruttare le funzionalità di backup e ripristino predefinite di iOS, iTunes e iCloud. In questo modo, dopo che un utente ha ripristinato un backup, gli acquisti precedenti saranno immediatamente disponibili.   
   
Per altre linee guida specifiche per iOS, vedere la guida alla codifica sicura di Apple.

## <a name="receipt-verification-and-server-delivered-products"></a>Verifica della ricezione e prodotti distribuiti dal server

Gli esempi in questo documento finora sono consistiti esclusivamente dell'applicazione che comunica direttamente con i server dell'app Store per condurre le transazioni di acquisto, che sbloccano funzionalità o funzionalità già codificate nell'app.   
   
Apple fornisce un livello aggiuntivo di sicurezza degli acquisti consentendo la verifica indipendente da parte di un altro server delle ricevute di acquisto, che può essere utile per convalidare una richiesta prima di fornire contenuti digitali come parte di un acquisto, ad esempio un libro digitale o Magazine).   
   
 **Prodotti predefiniti** : come gli esempi in questo documento, il prodotto acquistato esiste come funzionalità fornita con l'applicazione. Un acquisto in-app consente all'utente di accedere alla funzionalità.
Gli ID prodotto sono hardcoded.   
   
 **Prodotti distribuiti dal server** : il prodotto è costituito da contenuti scaricabili archiviati in un server remoto fino a quando la transazione non riesce a causare il download del contenuto.
Esempi possono includere problemi relativi a libri o riviste. Gli ID prodotto sono in genere originati da un server esterno (in cui è ospitato anche il contenuto del prodotto). Le applicazioni devono implementare una modalità di registrazione affidabile quando una transazione è stata completata, in modo che se il download del contenuto non riesce, è possibile ritentarne l'esecuzione senza confusione per l'utente.

### <a name="server-delivered-products"></a>Prodotti distribuiti dal server

Il contenuto di alcuni prodotti, ad esempio libri e riviste (o anche un livello di gioco), deve essere scaricato da un server remoto durante il processo di acquisto. Ciò significa che è necessario un server aggiuntivo per archiviare e recapitare il contenuto del prodotto dopo l'acquisto.

#### <a name="getting-prices-for-server-delivered-products"></a>Ottenere i prezzi per i prodotti distribuiti dal server

Poiché i prodotti vengono recapitati in modalità remota, è anche possibile aggiungere altri prodotti nel tempo (senza aggiornare il codice dell'app), ad esempio aggiungendo più libri o nuovi problemi di una rivista. In modo che l'applicazione possa individuare questi prodotti e visualizzarli per l'utente, il server aggiuntivo dovrebbe archiviare e fornire tali informazioni.   
   
[![](transactions-and-verification-images/image38.png "Getting Prices for Server-Delivered Products")](transactions-and-verification-images/image38.png#lightbox)   
   
1. Le informazioni sul prodotto devono essere archiviate in più posizioni: sul server e in iTunes Connect. Inoltre, a ogni prodotto sarà associato un file di contenuto. Questi file verranno recapitati dopo un acquisto riuscito.   
   
2. Quando l'utente desidera acquistare un prodotto, l'applicazione deve determinare quali prodotti sono disponibili. Queste informazioni possono essere memorizzate nella cache, ma devono essere recapitate da un server remoto in cui è archiviato l'elenco principale di prodotti.   
   
3. Il server restituisce un elenco di ID prodotto per l'applicazione da analizzare.   
   
4. L'applicazione determina quindi quale di questi ID prodotto inviare a StoreKit per recuperare prezzi e descrizioni.   
   
5. StoreKit invia l'elenco di ID prodotto ai server Apple.   
   
6. I server iTunes rispondono con informazioni valide sul prodotto (Descrizione e prezzo corrente).   
   
7. All'`SKProductsRequestDelegate` dell'applicazione vengono passate le informazioni sul prodotto da visualizzare all'utente.

#### <a name="purchasing-server-delivered-products"></a>Acquisto di prodotti distribuiti dal server

Poiché il server remoto richiede un modo per convalidare che una richiesta di contenuto sia valida (ad esempio, è stata pagata), le informazioni di ricezione vengono passate per l'autenticazione. Il server remoto invia i dati a iTunes per la verifica e, in caso di esito positivo, include il contenuto del prodotto nella risposta all'applicazione.   
   
 [![](transactions-and-verification-images/image39.png "Purchasing Server-Delivered Products")](transactions-and-verification-images/image39.png#lightbox)   
   
1. L'app aggiunge un `SKPayment` alla coda. Se necessario, all'utente verrà richiesto l'ID Apple e verrà richiesto di confermare il pagamento.   
   
2. StoreKit Invia la richiesta al server per l'elaborazione.   
   
3. Al termine della transazione, il server risponde con una ricevuta di transazione.   
   
4. La sottoclasse `SKPaymentTransactionObserver` riceve la ricevuta e la elabora. Poiché il prodotto deve essere scaricato da un server, l'applicazione avvia una richiesta di rete al server remoto.   
   
5. La richiesta di download è accompagnata dai dati di ricezione, in modo che il server remoto possa verificare che sia autorizzato ad accedere al contenuto. Il client di rete dell'applicazione attende una risposta a questa richiesta.   
   
6. Quando il server riceve una richiesta di contenuto, analizza i dati di ricezione e invia una richiesta direttamente ai server iTunes per verificare che la ricezione sia relativa a una transazione valida. Il server deve usare una logica per determinare se inviare la richiesta all'URL di produzione o sandbox. Apple suggerisce sempre di usare l'URL di produzione e di passare a sandbox se lo stato di ricezione 21007 (ricezione sandbox inviato al server di produzione). Per altri dettagli, vedere la [Guida alla programmazione della convalida della ricevuta](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) di Apple.
   
7. iTunes verificherà la ricezione e restituirà lo stato zero se è valido.   
   
8. Il server attende la risposta di iTunes. Se viene ricevuta una risposta valida, il codice deve individuare il file di contenuto del prodotto associato da includere nella risposta all'applicazione.   
  
9. L'applicazione riceve e analizza la risposta, salvando il contenuto del prodotto nel file System del dispositivo.   
   
10. L'applicazione Abilita il prodotto e quindi chiama la `FinishTransaction`di StoreKit. L'applicazione può quindi visualizzare facoltativamente il contenuto acquistato (ad esempio, Mostra la prima pagina di un problema relativo a un libro o a una rivista acquistata).

Un'implementazione alternativa per i file di contenuto del prodotto di grandi dimensioni può comportare semplicemente l'archiviazione della ricezione della transazione nel passaggio #9 in modo che la transazione possa essere completata rapidamente e fornire un'interfaccia utente per l'utente per scaricare il contenuto effettivo del prodotto in un secondo momento. La richiesta di download successiva può inviare nuovamente la ricezione archiviata per accedere al file di contenuto del prodotto richiesto.

### <a name="writing-server-side-receipt-verification-code"></a>Scrittura del codice di verifica della ricezione lato server

La convalida di una ricevuta nel codice sul lato server può essere eseguita con una semplice richiesta/risposta HTTP POST che include i passaggi #5 tramite #8 nel diagramma del flusso di lavoro.   
   
Estrarre la proprietà `SKPaymentTansaction.TransactionReceipt` nell'app. Si tratta dei dati che devono essere inviati a iTunes per la verifica (passaggio #5).

Codifica Base64 i dati di ricezione della transazione (nel passaggio #5 o #6).

Creare un semplice payload JSON simile al seguente:

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

HTTP pubblica il codice JSON per [https://buy.itunes.apple.com/verifyReceipt](https://buy.itunes.apple.com/verifyReceipt) per la produzione o [https://sandbox.itunes.apple.com/verifyReceipt](https://sandbox.itunes.apple.com/verifyReceipt) per il test.   
   
 La risposta JSON conterrà le chiavi seguenti:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

Uno stato pari a zero indica una ricevuta valida. Il server può continuare a soddisfare il contenuto del prodotto acquistato. La chiave di ricezione contiene un dizionario JSON con le stesse proprietà dell'oggetto `SKPaymentTransaction` ricevuto dall'app, quindi il codice server può eseguire query in questo dizionario per recuperare informazioni come il product_id e la quantità di acquisto.

Per ulteriori informazioni, vedere la documentazione della [Guida alla programmazione della convalida della ricevuta](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Introduction.html) di Apple.
