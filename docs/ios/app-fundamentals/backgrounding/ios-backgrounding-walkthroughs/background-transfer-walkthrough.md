---
title: 'Procedura dettagliata: utilizzo di NSURLSession e il servizio di trasferimento in Background'
description: "In questa procedura dettagliata, è utilizzare il servizio trasferimento in Background e l'API NSURLSession per avviare il download di un'immagine di grandi dimensioni che continua a essere scaricato quando l'app è in background."
ms.topic: article
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: d5a8baec164eb5c70f6dae5b2fa4fd5271afbd1c
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="walkthrough---using-background-transfer-service-and-nsurlsession"></a>Procedura dettagliata: utilizzo di NSURLSession e il servizio di trasferimento in Background

_In questa procedura dettagliata, è utilizzare il servizio trasferimento in Background e l'API NSURLSession per avviare il download di un'immagine di grandi dimensioni che continua a essere scaricato quando l'app è in background._

Viene avviato un trasferimento in background tramite la configurazione di uno sfondo `NSURLSession` e caricare o scaricare operazioni di Accodamento. Se il completamento di attività durante l'applicazione è backgrounded, sospeso o terminato, iOS avviserà l'applicazione chiamando il gestore di completamento dell'applicazione *AppDelegate*. Nel diagramma seguente illustra questo processo in azione:

 [![](background-transfer-walkthrough-images/transfer.png "Viene avviato un trasferimento in background tramite la configurazione di uno sfondo NSURLSession e Accodamento caricare o scaricare attività")](background-transfer-walkthrough-images/transfer.png#lightbox)

Di seguito viene illustrato questo aspetto nel codice.

## <a name="configuring-a-background-session"></a>Configurazione di una sessione in Background

Per configurare una sessione in background, creare un nuovo `NSUrlSession` e configurarlo mediante un `NSUrlSessionConfiguration` oggetto.

L'oggetto di configurazione determina cosa è possibile eseguire la sessione e i tipi di attività può essere eseguito.
Sessioni configurate tramite la `CreateBackgroundSessionConfiguration` metodo verrà eseguito in un processo separato ed eseguire trasferimenti (Wi-Fi) discrezionali per conservare i dati e durata della batteria.
Esempio di codice riportato di seguito viene illustrato come corretta configurazione di una sessione di trasferimento in background tramite la `CreateBackgroundSessionConfiguration` metodo e un identificatore di stringa univoco:

```csharp
public partial class SimpleBackgroundTransferViewController : UIViewController
{
  NSUrlSession session = null;

  NSUrlSessionConfiguration configuration =
      NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.SimpleBackgroundTransfer.BackgroundSession");
  session = NSUrlSession.FromConfiguration
      (configuration, (NSUrlSessionDelegate) new MySessionDelegate, new NSOperationQueue());

}
```

Oltre a un oggetto di configurazione, la sessione richiede un delegato di sessione e una coda.
La coda determina l'ordine in cui l'attività verrà completata. Il delegato sessione chaperones il processo di trasferimento e gestisce l'autenticazione, la memorizzazione nella cache e altri problemi relativi alla sessione.

## <a name="working-with-tasks-and-delegates"></a>Utilizzo di attività e delegati

Ora che è stata configurata una sessione in background, si avviano attività per gestire il trasferimento. È possibile tenere traccia di queste operazioni usando l'un `NSUrlSessionDelegate` istanza chiamato un delegato di sessione. Il delegato di sessione è responsabile per la riattivazione di un'applicazione interrotte o sospesa in background per l'autenticazione di handle, errori o completamento trasferimento.

Un `NSUrlSessionDelegate` fornisce i metodi di base seguenti per controllare lo stato di trasferimento:

-  *DidFinishEventsForBackgroundSession* -questo metodo viene chiamato quando tutte le attività e il trasferimento è stato completato.
-  *DidReceiveChallenge* : viene chiamato a richiesta delle credenziali quando è necessaria l'autorizzazione.
-  *DidBecomeInvalidWithError* -chiamata eseguita se il `NSURLSession` viene invalidata.


Le sessioni di background richiedono più specializzati delegati a seconda dei tipi di attività in esecuzione. Le sessioni in background sono limitate a due tipi di attività:

-  *Caricare l'attività* -attività di tipo `NSUrlSessionUploadTask` utilizzare il `NSUrlSessionTaskDelegate` , che eredita da `NSUrlSessionDelegate` . Questo delegato fornisce metodi aggiuntivi per tenere traccia di caricano lo stato di avanzamento, reindirizzamento HTTP handle e altro ancora.
-  *Scaricare attività* -attività di tipo `NSUrlSessionDownloadTask` utilizzare il `NSUrlSessionDownloadDelegate` , che eredita da `NSUrlSessionTaskDelegate` . Questo delegato fornisce che tutti i metodi per caricare l'attività, nonché di metodi specifici di download per tenere traccia dell'avanzamento del download e di determinare quando un'attività di download ha ripreso o completato.


Il codice seguente definisce un'attività che può essere utilizzata per scaricare un'immagine da un URL. Lanciamo attività chiamando `CreateDownloadTask` la sessione in background e il passaggio di richiesta dell'URL:

```csharp
const string DownloadURLString = "http://cdn1.xamarin.com/webimages/images/xamarin.png";
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

Successivamente, si creerà un delegato di download nuova sessione per tenere traccia di tutte le attività di download in questa sessione:

```csharp
public class MySessionDelegate : NSUrlSessionDownloadDelegate
{
  public override void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
  {
    Console.WriteLine (string.Format ("DownloadTask: {0}  progress: {1}", downloadTask, progress));
    InvokeOnMainThread( () => {
      // update UI with progress bar, if desired
    });
  }
  ...
}
```

Se si desidera individuare lo stato di avanzamento di un'attività di download, è possibile ignorare il `DidWriteData` metodo per rilevare lo stato di avanzamento e persino aggiornare l'interfaccia utente. Se l'applicazione è in primo piano o in attesa per l'utente alla successiva apertura dell'applicazione, verranno visualizzata immediatamente gli aggiornamenti dell'interfaccia utente.

L'API di delegato sessione fornisce un ampio toolkit per l'interazione con le attività. Per un elenco completo di sessione di metodi di delega, consultare il `NSUrlSessionDelegate` documentazione dell'API.

> [!IMPORTANT]
> **Nota**: Background avviate in un thread in background, pertanto qualsiasi chiamata per aggiornare l'interfaccia utente deve essere eseguito in modo esplicito nel thread dell'interfaccia utente chiamando `InvokeOnMainThread` per evitare di terminare l'app iOS. 


## <a name="handling-transfer-completion"></a>Completamento trasferimento di gestione

Il passaggio finale consiste nel consentire all'applicazione di sapere quando completato tutte le attività associate alla sessione e gestire il nuovo contenuto.

Nel *AppDelegate*, sottoscrivere il `HandleEventsForBackgroundUrl` evento. Quando l'applicazione passerà in background ed è in esecuzione una sessione di trasferimento, questo metodo viene chiamato e il sistema passa a un gestore di completamento:

```csharp
public System.Action backgroundSessionCompletionHandler;

public override void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

Verrà utilizzato il gestore di completamento per informare iOS quando viene eseguita l'applicazione di elaborazione.

Tenere presente che una sessione è possibile distribuire diverse attività per elaborare un trasferimento. Al termine dell'ultima attività, un'applicazione sospesa o è stata terminata viene nuovamente avviata in background. Quindi, l'applicazione si connette nuovamente al `NSURLSession` utilizzando l'identificatore di sessione univoco, quindi chiama `DidFinishEventsForBackgroundSession` sul delegato di sessione. Questo metodo è il possibilità dell'applicazione per gestire i nuovi contenuti, tra cui l'aggiornamento dell'interfaccia utente in base ai risultati del trasferimento:

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

Al termine gestione nuovo contenuto, viene chiamato il gestore di completamento per informare il sistema che è consigliabile acquisire uno snapshot dell'applicazione e tornare alla modalità sospensione:

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  var appDelegate = UIApplication.SharedApplication.Delegate as AppDelegate;

  // Handle new information, update UI, etc.

  // call completion handler when you're done
  if (appDelegate.backgroundSessionCompletionHandler != null) {
    NSAction handler = appDelegate.backgroundSessionCompletionHandler;
    appDelegate.backgroundSessionCompletionHandler = null;
    handler.Invoke ();
  }
}
```

In questa procedura dettagliata, trattati i passaggi di base per implementare il servizio di trasferimento in Background in iOS 7.



## <a name="related-links"></a>Collegamenti correlati

- [Trasferimento in Background semplice (esempio)](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
