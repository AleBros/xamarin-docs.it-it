---
title: Aggiornamento di un'applicazione in Background
ms.topic: article
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: d878f922b74ea3e95fd0e1ebce9e7445063a2946
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="updating-an-application-in-the-background"></a>Aggiornamento di un'applicazione in Background

L'aggiornamento in background è il processo di riattivazione di un'applicazione che è stato sospeso o non è in esecuzione e l'aggiornamento con nuovo contenuto. iOS fornisce tre opzioni per l'aggiornamento del contenuto in background:

1.  *Area monitoraggio* e *servizio di modifiche significative percorso* -background trigger APIs Location-aware Aggiorna in base alle modifiche nel percorso dell'utente. Queste API possono essere utilizzate con cautela per aggiornare il contenuto nelle applicazioni non basate su percorso iOS 6, in cui non sono disponibili altre opzioni.
1.  *(IOS 7 +) di recupero in background* -un approccio temporale per l'aggiornamento *non critici* contenuto che aggiorna *spesso* .
1.  *Le notifiche remote (iOS 7 +)* -applicazioni che ricevono le notifiche push possono utilizzare le notifiche per attivare aggiornamenti in background di contenuto. Questo metodo può essere utilizzato per l'aggiornamento con *importante, scadenza* contenuto che aggiorna *sporadicamente* .


Le sezioni seguenti illustrano i concetti di base di queste opzioni.

## <a name="region-monitoring-and-significant-location-changes"></a>Area monitoraggio e la modifica del percorso significativo

iOS fornisce due API con riconoscimento del percorso con backgrounding funzionalità:

1.  *Area monitoraggio* è il processo di impostazione di aree con i limiti e riattivazione del dispositivo quando l'utente entra o esce da un'area. Le aree sono circolari e possono essere di dimensioni diverse. Quando l'utente supera il limite di un'area, il dispositivo verrà attivata per gestire l'evento, in genere per la generazione di una notifica o avviando un'attività. Area monitoraggio richiede GPS e incrementa batteria e l'utilizzo di dati.
1.  Il *servizio di modifiche significative percorso* è un'opzione più semplice, risparmio energia disponibili per i dispositivi con radio cellulare. Un'applicazione in attesa per le modifiche significative percorso riceverà una notifica quando il dispositivo passa towers cella. Questo servizio può essere utilizzato per riattivare un'applicazione sospesa o è stata terminata e offre l'opportunità di verificare la presenza di nuovo contenuto in background. Attività in background è limitato a circa 10 secondi, a meno che non associato a un [attività in Background](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md) .


Non è necessario il percorso di un'applicazione `UIBackgroundMode` per usare queste API con riconoscimento del percorso. Poiché iOS non registrare i tipi di attività che può essere eseguito quando il dispositivo viene riattivato dalle modifiche apportate alla posizione dell'utente, le API forniscono una soluzione alternativa per l'aggiornamento del contenuto in background in iOS 6. *Tenere presente che gli aggiornamenti in background con le API in base alla posizione di attivazione verrà disegnato su risorse del dispositivo e potrebbe confondere gli utenti che non conoscono perché un'applicazione richiede l'accesso al percorso*. Usare cautela quando si implementa il monitoraggio di area o modifiche significative di percorso per le applicazioni che non sono già in uso l'API del percorso di elaborazione in background.

Le app tramite il percorso di monitoraggio per l'elaborazione in background espongono un difetto nella iOS 6: se le esigenze di un'applicazione non rientrano in una categoria di background necessarie, è limitata backgrounding opzioni. Con l'introduzione di due nuove API, *Background recuperare* e *notifiche remoto*, iOS 7 (e versioni successive) offre backgrounding opportunità di altre applicazioni. Le due sezioni successive introducono le nuove API.

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>Recupero in background (iOS 7 e versioni successive)

In iOS 6, un'applicazione di immissione di primo piano la necessità di esecuzione per caricare nuovo contenuto, una breve presentazione agli utenti con contenuto che già visto. Recupero in background consente alle applicazioni di caricare nuovi dati *prima* un utente avvia l'applicazione e forniscono all'utente il contenuto più aggiornato.

Per implementare il recupero in background, modificare *Info. plist* e controllare il **abilitare la modalità di Background** e **recupero in Background** caselle di controllo:

 [ ![](updating-an-application-in-the-background-images/fetch.png "Modificare il file Info. plist e selezionare le caselle di controllo Abilita modalità di Background e il recupero in Background")](updating-an-application-in-the-background-images/fetch.png)

Successivamente, nel `AppDelegate`, eseguire l'override di `FinishedLaunching` metodo per impostare l'intervallo di recupero minimo. In questo esempio, si lascia il sistema operativo decidere la frequenza con cui recuperare il nuovo contenuto:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

Infine, eseguire l'operazione di recupero eseguendo l'override di `PerformFetch` metodo il `AppDelegate`e passando un *gestore di completamento*. Il gestore di completamento è un delegato che accetta un `UIBackgroundFetchResult`:

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

Al termine, l'aggiornamento del contenuto è informare il sistema operativo chiamando il gestore di completamento con lo stato appropriato. iOS offre tre opzioni per lo stato di completamento del gestore:

1.  `UIBackgroundFetchResult.NewData` -Chiamato quando il nuovo contenuto è stato recuperato e l'applicazione è stato aggiornato.
1.  `UIBackgroundFetchResult.NoData` -Chiamato quando l'operazione di recupero per il nuovo contenuto parlato, ma non è disponibile alcun contenuto.
1.  `UIBackgroundFetchResult.Failed` -Utile per la gestione degli errori, viene chiamato quando non è riuscito a eseguire l'operazione di recupero.


Applicazioni che usano il recupero in Background possono effettuare chiamate per l'aggiornamento dell'interfaccia utente di background. Quando l'utente apre l'app, l'interfaccia utente sarà fino a data e di visualizzare il nuovo contenuto. Snapshot App selezione dell'applicazione, verranno aggiornate anche in modo che l'utente può visualizzare quando l'applicazione dispone di nuovo contenuto.

> [!IMPORTANT]
> **Nota**: una volta `PerformFetch` viene chiamato, l'applicazione è circa 30 secondi per avviare il download del contenuto nuovo e chiamare il blocco del gestore di completamento. Se si impiega troppo tempo, l'applicazione verrà terminata. È consigliabile utilizzare il recupero in Background con il _servizio trasferimento in Background_ durante il download di supporto o altri file di grandi dimensioni.


### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

Nell'esempio di codice precedente, si lascia il sistema operativo decidere la frequenza con cui recuperare il contenuto nuovo impostando l'intervallo di recupero minimo su `BackgroundFetchIntervalMinimum`. iOS sono disponibili tre opzioni per l'intervallo di recupero:

1.  `BackgroundFetchIntervalNever` -Indicano mai recuperare il nuovo contenuto. Consente di disattivare il recupero in determinate situazioni, ad esempio quando l'utente non è connesso. Questo è il valore predefinito per l'intervallo di recupero. 
1.  `BackgroundFetchIntervalMinimum` -Lasciare che il sistema decide la frequenza di recuperare in base a motivi definiti dall'utente, la durata della batteria, l'utilizzo di dati e le esigenze di altre applicazioni.
1.  `BackgroundFetchIntervalCustom` -Se si conosce la frequenza con cui viene aggiornato il contenuto di un'applicazione, è possibile specificare un intervallo di "inattività" dopo ogni operazione di recupero durante i quali l'applicazione verrà impedito il recupero di nuovo contenuto. Dopo questo intervallo è attivo, il sistema determina se recuperare il contenuto.


Entrambi `BackgroundFetchIntervalMinimum` e `BackgroundFetchIntervalCustom` si affida al sistema per pianificare operazioni di recupero. Questo intervallo è dinamico, adattarsi alle esigenze del dispositivo, nonché le abitudini dal singolo utente. Ad esempio, se un utente archivia ogni mattina un'applicazione e di un'altra verifica ogni ora, iOS verificare il contenuto viene aggiornati sia per gli utenti ogni volta che aprono l'applicazione.

Recupero in background deve essere utilizzato per le applicazioni che aggiornano frequentemente con contenuto non critici. Per le applicazioni con gli aggiornamenti critici, le notifiche remoto deve essere utilizzate. Notifiche remote basate su sfondo recuperare e condividono lo stesso gestore di completamento. Verranno esaminate le notifiche remoto successivamente.

 <a name="remote_notifications" />


## <a name="remote-notifications-ios-7-and-greater"></a>Notifiche remote (iOS 7 e versioni successive)

Notifiche push sono i messaggi JSON inviati da un provider a un dispositivo tramite il *servizio Apple Push Notification (APN)*.

In iOS 6, una notifica push in ingresso indica al sistema per avvisare l'utente che in un'applicazione è avvenuto qualcosa di interessante. Facendo clic sulla notifica estrae l'applicazione lo stato sospeso o è stato terminato e l'app è preferibile iniziare l'aggiornamento del contenuto. iOS 7 (e versioni successive) estende le notifiche push normale fornendo applicazioni la possibilità di aggiornare il contenuto in background *prima* notificare all'utente, in modo che l'utente può aprire l'applicazione e visualizzato con nuovo contenuto immediatamente.

Per implementare le notifiche remote, modificare *Info. plist* e controllare il **abilitare la modalità di Background** e **notifiche Remote** caselle di controllo:

 [ ![](updating-an-application-in-the-background-images/remote.png "Modalità di sfondo impostato su notifiche Remote e abilitare la modalità di Background")](updating-an-application-in-the-background-images/remote.png)

Successivamente, impostare il `content-available` flag di notifica push su 1. In questo modo l'applicazione conosce recuperare il nuovo contenuto prima di visualizzare l'avviso:

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

Nel *AppDelegate*, eseguire l'override di `DidReceiveRemoteNotification` metodo per controllare il payload di notifica per il contenuto disponibile e chiamare il blocco del gestore di completamento appropriato:

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

Notifiche remote devono essere utilizzate per gli aggiornamenti non frequenti con il contenuto è fondamentale per le funzionalità dell'applicazione. Per ulteriori informazioni sulle notifiche remote, vedere il Xamarin [le notifiche Push in iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md) Guida.

> [!IMPORTANT]
> **Nota**: perché il meccanismo di aggiornamento nelle notifiche remoto dipende dal recupero in Background, l'applicazione deve avviare il download del contenuto nuovo e chiamare il blocco del gestore di completamento entro 30 secondi di ricezione della notifica o verrà iOS terminare l'applicazione. Prendere in considerazione le notifiche remoto con l'associazione _servizio trasferimento in Background_ durante il download di supporto o altri file di grandi dimensioni in background.


### <a name="silent-remote-notifications"></a>Notifiche Remote invisibile all'utente

Le notifiche remote sono un modo semplice per notificare alle applicazioni di aggiornamenti e avviare il recupero di nuovo contenuto, ma vi sono casi in cui non è necessario notificare l'utente che sono state apportate modifiche. Ad esempio, se si contrassegna un file per la sincronizzazione, non è necessario inviare una notifica ogni volta che il file aggiornato. Sincronizzazione file non è un evento sorprendente, e non è necessario un intervento immediato dell'utente. Gli utenti si aspettano solo i file devono essere aggiornate all'apertura.

Per casi come quella illustrata sopra, iOS consente le notifiche push essere inviati automaticamente, vale a dire, senza un avviso. Per attivare una notifica regolare in una invisibile all'utente, rimuovere semplicemente l'avviso dal payload di notifica:

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>Limiti di velocità

La differenza principale tra normale e invisibile all'utente per le notifiche da una prospettiva di sviluppo è che inserimenti invisibile all'utente sono frequenza limitata. Se la velocità di push diventi troppo elevata, APN ritarderà la consegna di push invisibile all'utente al dispositivo. Questo modo si garantisce che le applicazioni non sottrarre risorse a dispositivi con un numero eccessivo di notifiche invisibile all'utente.

Tuttavia, APN consentirà di notifiche invisibile all'utente "piggyback" insieme a una normale notifica remota o una risposta keep-alive. Poiché non notifiche periodiche sono frequenza limitata, possono essere utilizzati stored backup invisibile all'utente invio delle notifiche push da APNs il dispositivo, come illustrato nel diagramma seguente:

 [ ![](updating-an-application-in-the-background-images/silent.png "Notifiche periodiche utilizzabile stored invisibile all'utente invio delle notifiche push da APNs il dispositivo, come illustrato in questo diagramma")](updating-an-application-in-the-background-images/silent.png)

> [!IMPORTANT]
> **Nota**: Apple invita gli sviluppatori per inviare notifiche push invisibile all'utente ogni volta che richiede l'applicazione e servizio APN consentono di pianificare il recapito.


In questa sezione, abbiamo trattato le varie opzioni per l'aggiornamento del contenuto in background per eseguire attività che non rientrano in una categoria di background necessarie. A questo punto, vediamo alcune di queste API in azione.

 [Passaggio successivo: Parte 4 - iOS procedure dettagliate Backgrounding](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
