---
title: Trasferimento in background e NSURLSession in Novell. iOS
description: Questo documento fornisce una procedura dettagliata che illustra come usare il trasferimento in background e NSUrlSession per avviare il download di un'immagine di grandi dimensioni e continuare il download quando l'app viene posizionata in background.
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 6004598b215c85b70b057ff156c3a905a0879138
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010708"
---
# <a name="background-transfer-and-nsurlsession-in-xamarinios"></a>Trasferimento in background e NSURLSession in Novell. iOS

Un trasferimento in background viene avviato tramite la configurazione di un `NSURLSession` in background e l'accodamento delle attività di caricamento o download. Se le attività vengono completate quando l'applicazione è in background, sospesa o terminata, iOS invierà una notifica all'applicazione chiamando il gestore di completamento nel *AppDelegate*dell'applicazione. Il diagramma seguente illustra questo comportamento:

 [![](background-transfer-walkthrough-images/transfer.png "A background transfer is initiated by configuring a background NSURLSession and enqueuing upload or download tasks")](background-transfer-walkthrough-images/transfer.png#lightbox)

Vediamo come appare nel codice.

## <a name="configuring-a-background-session"></a>Configurazione di una sessione in background

Per eseguire una sessione in background, creare una nuova `NSUrlSession` e configurarla utilizzando un oggetto `NSUrlSessionConfiguration`.

L'oggetto di configurazione determina le operazioni che la sessione può eseguire e i tipi di attività che può eseguire.
Le sessioni configurate con il metodo `CreateBackgroundSessionConfiguration` vengono eseguite in un processo separato ed eseguono trasferimenti discrezionali (WiFi) per mantenere i dati e la durata della batteria.
Nell'esempio di codice seguente viene illustrata la corretta installazione di una sessione di trasferimento in background utilizzando il metodo `CreateBackgroundSessionConfiguration` e un identificatore di stringa univoco:

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

Oltre a un oggetto di configurazione, una sessione richiede anche un delegato della sessione e una coda.
La coda determina l'ordine in cui le attività vengono completate. Il delegato della sessione accompagna il processo di trasferimento e gestisce l'autenticazione, la memorizzazione nella cache e altri problemi correlati alla sessione.

## <a name="working-with-tasks-and-delegates"></a>Utilizzo di attività e delegati

Ora che è stata configurata una sessione in background, è possibile avviare le attività per gestire il trasferimento. È possibile tenere traccia di queste attività usando un'istanza di `NSUrlSessionDelegate` denominata delegato della sessione. Il delegato della sessione è responsabile della riattivazione di un'applicazione terminata o sospesa in background per gestire l'autenticazione, gli errori o il completamento del trasferimento.

In `NSUrlSessionDelegate` sono disponibili i seguenti metodi di base per verificare lo stato del trasferimento:

- *DidFinishEventsForBackgroundSession* : questo metodo viene chiamato al termine di tutte le attività e il trasferimento viene completato.
- *DidReceiveChallenge* : chiamato per richiedere le credenziali quando è richiesta l'autorizzazione.
- *DidBecomeInvalidWithError* : viene chiamato se l'`NSURLSession` viene invalidata.

Per le sessioni in background sono necessari delegati più specializzati a seconda dei tipi di attività in esecuzione. Le sessioni in background sono limitate a due tipi di attività:

- *Attività di caricamento* : le attività di tipo `NSUrlSessionUploadTask` usano il `NSUrlSessionTaskDelegate`, che eredita da `NSUrlSessionDelegate`. Questo delegato fornisce metodi aggiuntivi per tenere traccia dello stato di avanzamento del caricamento, gestire il reindirizzamento HTTP e altro ancora.
- *Attività di download* -attività di tipo `NSUrlSessionDownloadTask` usare il `NSUrlSessionDownloadDelegate`, che eredita da `NSUrlSessionTaskDelegate`. Questo delegato fornisce tutti i metodi per caricare le attività, nonché metodi specifici del download per tenere traccia dello stato di avanzamento del download e determinare quando un'attività di download è stata ripresa o completata.

Il codice seguente definisce un'attività che può essere usata per scaricare un'immagine da un URL. L'attività viene avviata chiamando `CreateDownloadTask` nella sessione in background e passando la richiesta dell'URL:

```csharp
const string DownloadURLString = "http://cdn1.xamarin.com/webimages/images/xamarin.png";
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

Successivamente, verrà creato un nuovo delegato di download della sessione per tenere traccia di tutte le attività di download in questa sessione:

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

Per scoprire lo stato di avanzamento di un'attività di download, è possibile eseguire l'override del metodo `DidWriteData` per tenere traccia dello stato di avanzamento e aggiornare anche l'interfaccia utente. Gli aggiornamenti dell'interfaccia utente verranno visualizzati immediatamente se l'applicazione è in primo piano o sarà in attesa dell'utente alla successiva apertura dell'applicazione.

L'API del delegato della sessione fornisce un ampio Toolkit per interagire con le attività. Per un elenco completo dei metodi delegati della sessione, vedere la documentazione dell'API `NSUrlSessionDelegate`.

> [!IMPORTANT]
> Le sessioni in background vengono avviate in un thread in background, pertanto tutte le chiamate per aggiornare l'interfaccia utente devono essere eseguite in modo esplicito nel thread UI chiamando `InvokeOnMainThread` per evitare che iOS interrompa l'app. 

## <a name="handling-transfer-completion"></a>Gestione del completamento del trasferimento

Il passaggio finale consiste nel lasciare che l'applicazione sappia quando tutte le attività associate alla sessione sono state completate e gestire il nuovo contenuto.

In *AppDelegate*sottoscrivere l'evento `HandleEventsForBackgroundUrl`. Quando l'applicazione entra nello sfondo ed è in esecuzione una sessione di trasferimento, questo metodo viene chiamato e il sistema passa un gestore di completamento:

```csharp
public System.Action backgroundSessionCompletionHandler;

public override void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

Il gestore di completamento verrà usato per informare iOS al termine dell'elaborazione dell'applicazione.

Ricordare che una sessione può generare diverse attività per elaborare un trasferimento. Al termine dell'ultima attività, un'applicazione sospesa o terminata viene riavviata in background. Quindi, l'applicazione esegue nuovamente la connessione al `NSURLSession` usando l'identificatore di sessione univoco e chiama `DidFinishEventsForBackgroundSession` sul delegato della sessione. Questo metodo è l'opportunità dell'applicazione di gestire nuovo contenuto, incluso l'aggiornamento dell'interfaccia utente per riflettere i risultati del trasferimento:

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

Al termine della gestione del nuovo contenuto, viene chiamato il gestore di completamento per informare il sistema che è sicuro eseguire uno snapshot dell'applicazione e tornare alla modalità sospensione:

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

In questa procedura dettagliata sono stati descritti i passaggi di base per implementare il servizio di trasferimento in background in iOS 7.

## <a name="related-links"></a>Collegamenti correlati

- [Trasferimento in background semplice (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/simplebackgroundtransfer)
