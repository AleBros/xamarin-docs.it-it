---
title: Trasferimento in background e NSURLSession in xamarin. IOS
description: Questo documento fornisce una procedura dettagliata che illustra come usare il trasferimento in background e NSUrlSession per avviare il download di un'immagine di grandi dimensioni, quindi continuare che vengono scaricati quando l'app viene inserito in background.
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 4e525388290d92901e68e61f1ffa81866f5aac4d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114236"
---
# <a name="background-transfer-and-nsurlsession-in-xamarinios"></a>Trasferimento in background e NSURLSession in xamarin. IOS

Un trasferimento in background viene avviato tramite la configurazione di uno sfondo `NSURLSession` e caricare o scaricare operazioni di Accodamento. Se le attività completate mentre l'applicazione è supportata da un background, sospeso o terminata, iOS informerà l'applicazione chiamando il gestore di completamento dell'applicazione *AppDelegate*. Il diagramma seguente illustra questo processo in azione:

 [![](background-transfer-walkthrough-images/transfer.png "Un trasferimento in background viene avviato tramite la configurazione di uno sfondo NSURLSession e Accodamento caricare o scaricare attività")](background-transfer-walkthrough-images/transfer.png#lightbox)

Di seguito viene illustrato come appare nel codice.

## <a name="configuring-a-background-session"></a>Configurazione di una sessione in Background

Per configurare una sessione in background, creare una nuova `NSUrlSession` e configurarlo mediante un `NSUrlSessionConfiguration` oggetto.

L'oggetto di configurazione determina ciò che è possibile eseguire la sessione e i tipi di attività può essere eseguita.
Le sessioni configurate tramite la `CreateBackgroundSessionConfiguration` metodo verrà eseguito in un processo separato ed eseguire i trasferimenti (Wi-Fi) discrezionali per conservare i dati e durata delle batterie.
Esempio di codice seguente viene illustrato il programma di installazione appropriato di una sessione di trasferimento in background usando il `CreateBackgroundSessionConfiguration` (metodo) e un identificatore di stringa univoco:

```csharp
public partial class SimpleBackgroundTransferViewController : UIViewController
{
  NSUrlSession session = null;

  NSUrlSessionConfiguration configuration =
      NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.SimpleBackgroundTransfer.BackgroundSession");
  session = NSUrlSession.FromConfiguration
      (configuration, (NSUrlSessionDelegate) new MySessionDelegate(), new NSOperationQueue());

}
```

Oltre a un oggetto di configurazione, la sessione richiede anche un delegato di sessione e una coda.
La coda determina l'ordine in cui l'attività verrà completata. Il delegato di sessione chaperones il processo di trasferimento e gestisce l'autenticazione, la memorizzazione nella cache e altri problemi relativi alla sessione.

## <a name="working-with-tasks-and-delegates"></a>Utilizzo di attività e i delegati

Ora che abbiamo configurato una sessione in background, è possibile avviare le attività per gestire il trasferimento. È possibile tenere traccia di queste attività usando l'un `NSUrlSessionDelegate` istanza chiamato un delegato di sessione. Il delegato di sessione è responsabile per risvegliare il gigante che un'applicazione interrotte o sospesa in background per l'autenticazione di handle, errori o completamento del trasferimento.

Un `NSUrlSessionDelegate` fornisce i metodi di base seguenti per controllare lo stato di trasferimento:

-  *DidFinishEventsForBackgroundSession* -questo metodo viene chiamato quando tutte le attività sono completate e il trasferimento è stato completato.
-  *DidReceiveChallenge* : viene chiamato alla richiesta delle credenziali quando è necessaria l'autorizzazione.
-  *DidBecomeInvalidWithError* -chiamato se il `NSURLSession` viene invalidata.


Le sessioni in background sono necessari più specializzati delegati a seconda dei tipi di attività in esecuzione. Le sessioni in background sono limitate a due tipi di attività:

-  *Le attività di caricamento* -le attività di tipo `NSUrlSessionUploadTask` utilizzare il `NSUrlSessionTaskDelegate` , che eredita da `NSUrlSessionDelegate` . Questo delegato fornisce metodi aggiuntivi per tenere traccia di caricano lo stato di avanzamento, reindirizzamento HTTP handle e altro ancora.
-  *Scaricare le attività* -le attività di tipo `NSUrlSessionDownloadTask` usare i `NSUrlSessionDownloadDelegate` , che eredita da `NSUrlSessionTaskDelegate` . Questo delegato fornisce che tutti i metodi per caricare le attività, nonché metodi specifici di download per tenere traccia dell'avanzamento del download e determinare quando un'attività di download è stata ripresa o completata.


Il codice seguente definisce un'attività che può essere usata per scaricare un'immagine da un URL. Lanciamo attività chiamando `CreateDownloadTask` sul nostro sessione in background e passando la richiesta dell'URL:

```csharp
const string DownloadURLString = "http://cdn1.xamarin.com/webimages/images/xamarin.png";
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

Successivamente, verrà creato un delegato di download nuova sessione per tenere traccia di tutte le attività di download in questa sessione:

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

Se si vuole esaminare l'avanzamento di un'attività di download, è possibile ignorare il `DidWriteData` metodo per tenere traccia dello stato e anche aggiornare l'interfaccia utente. Aggiornamenti dell'interfaccia utente verranno visualizzato immediatamente se l'applicazione è in primo piano o in attesa per l'utente alla successiva che apertura dell'applicazione.

L'API di delegato sessione offre un vasto toolkit per l'interazione con le attività. Per un elenco completo di sessione di delegare i metodi, vedere il `NSUrlSessionDelegate` documentazione dell'API.

> [!IMPORTANT]
> Le sessioni in background vengono avviate in un thread in background, in modo che tutte le chiamate per aggiornare l'interfaccia utente devono essere eseguite in modo esplicito nel thread dell'interfaccia utente tramite la chiamata `InvokeOnMainThread` per evitare di terminare l'app iOS. 


## <a name="handling-transfer-completion"></a>Completamento del trasferimento la gestione

Il passaggio finale consiste per informare l'applicazione dopo il completamento di tutte le attività associate alla sessione e gestire il nuovo contenuto.

Nel *AppDelegate*, sottoscrivere il `HandleEventsForBackgroundUrl` evento. Quando l'applicazione passerà in background e una sessione di trasferimento sia in esecuzione, questo metodo viene chiamato e il sistema passa a un gestore di completamento:

```csharp
public System.Action backgroundSessionCompletionHandler;

public override void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

Si userà il gestore di completamento per informare iOS quando viene eseguita l'applicazione di elaborazione.

È importante ricordare che una sessione è possibile distribuire diverse attività per elaborare un trasferimento. Al termine dell'ultima attività, un'applicazione sospesa oppure termina viene riavviata in background. Quindi, l'applicazione si connette nuovamente al `NSURLSession` usando l'identificatore di sessione univoco e le chiamate `DidFinishEventsForBackgroundSession` sul delegato sessione. Questo metodo è possibilità dell'applicazione per gestire nuovi contenuti, tra cui l'aggiornamento dell'interfaccia utente in modo da riflettere i risultati del trasferimento:

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

Dopo aver completato la gestione nuovo contenuto, viene chiamato il gestore di completamento per comunicare al sistema che è possibile creare uno snapshot dell'applicazione e tornare alla modalità sospensione:

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

In questa procedura dettagliata sono illustrati i passaggi di base per implementare il servizio di trasferimento in Background in iOS 7.



## <a name="related-links"></a>Collegamenti correlati

- [Trasferimento in Background semplice (esempio)](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
