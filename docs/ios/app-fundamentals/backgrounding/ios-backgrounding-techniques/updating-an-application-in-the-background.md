---
title: Aggiornamento di un'app Novell. iOS in background
description: Questo documento descrive i vari modi per aggiornare un'app Novell. iOS in background, ad esempio il monitoraggio dell'area, il recupero in background e le notifiche remote.
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 2d56af364d63ff78bafbdd7d8043ae4d75d97959
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78915767"
---
# <a name="updating-a-xamarinios-app-in-the-background"></a>Aggiornamento di un'app Novell. iOS in background

L'aggiornamento in background è il processo di riattivazione di un'applicazione sospesa o non in esecuzione e l'aggiornamento con nuovo contenuto. iOS offre tre opzioni per l'aggiornamento del contenuto in background:

1. Il *monitoraggio dell'area* e la *posizione significativa cambiano* le API in grado di riconoscere la posizione per attivare gli aggiornamenti in background in base alle modifiche apportate alla posizione dell'utente. Queste API possono essere usate con discrezione per aggiornare il contenuto in applicazioni iOS 6 non basate sulla posizione, in cui non sono disponibili altre opzioni.
1. *Recupero in background (iOS 7 +)* : approccio temporale per l'aggiornamento di contenuti *non critici* che vengono aggiornati di *frequente* .
1. *Notifiche remote (iOS 7 +)* : le applicazioni che ricevono notifiche push possono usare le notifiche per attivare gli aggiornamenti del contenuto in background. Questo metodo può essere utilizzato per aggiornare con un contenuto *importante e sensibile al tempo* che si aggiorna *sporadicamente* .

Le sezioni seguenti illustrano le nozioni di base di queste opzioni.

## <a name="region-monitoring-and-significant-location-changes"></a>Monitoraggio delle aree e modifiche significative della località

iOS fornisce due API in grado di riconoscere la posizione con funzionalità in background:

1. Il *monitoraggio dell'area* è il processo di configurazione delle aree con limiti e la riattivazione del dispositivo quando l'utente immette o esce da un'area. Le aree sono circolari e possono avere dimensioni variabili. Quando l'utente supera il limite di un'area, il dispositivo viene riattivato per gestire l'evento, in genere inviando una notifica o avviando un'attività. Il monitoraggio dell'area richiede il GPS e aumenta la batteria e l'utilizzo dei dati.
1. Il *servizio di modifica della posizione significativa* è un'opzione più semplice e di risparmio energia disponibile per i dispositivi con radiotelefonia. Un'applicazione in ascolto di modifiche di posizione significative riceverà una notifica quando il dispositivo passa alla cella. Questo servizio può essere utilizzato per riattivare un'applicazione sospesa o terminata e offre l'opportunità di verificare la presenza di nuovi contenuti in background. L'attività in background è limitata a circa 10 secondi, a meno che non sia associata a un' [attività in background](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md) .

Per un'applicazione non è necessario il percorso `UIBackgroundMode` usare queste API in grado di riconoscere la posizione. Poiché iOS non tiene traccia dei tipi di attività che è possibile eseguire quando il dispositivo viene riattivato dalle modifiche nella posizione dell'utente, queste API forniscono una soluzione per l'aggiornamento del contenuto in background in iOS 6. *Tenere presente che l'attivazione degli aggiornamenti in background con le API basate sulla posizione trarrà sulle risorse del dispositivo e può confondere gli utenti che non sono in grado di comprendere il motivo per cui un'applicazione richiede l'accesso alla propria posizione*. Usare la discrezione durante l'implementazione del monitoraggio dell'area o modifiche significative per l'elaborazione in background nelle applicazioni che non usano già le API location.

Le app che usano il monitoraggio della posizione per l'elaborazione in background espongono un difetto in iOS 6: se le esigenze di un'applicazione non rientrano in una categoria di background necessaria, sono disponibili opzioni di background limitate. Con l'introduzione di due nuove API, il *recupero in background* e le *notifiche remote*, iOS 7 (e versioni successive) offre opportunità in background per più applicazioni. Nelle due sezioni successive vengono introdotte queste nuove API.

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>Recupero in background (iOS 7 e versioni successive)

In iOS 6, un'applicazione che entra in primo piano necessario per caricare nuovo contenuto, presenta brevemente agli utenti il contenuto già visto. Il recupero in background consente alle applicazioni di caricare nuovi dati *prima* che l'applicazione venga avviata da un utente e fornire all'utente il contenuto più aggiornato.

Per implementare il recupero in background, modificare *info. plist* e selezionare le caselle di controllo **Abilita modalità in background** e **recupero in background** :

 [![](updating-an-application-in-the-background-images/fetch.png "Edit the Info.plist and check the Enable Background Modes and Background Fetch check boxes")](updating-an-application-in-the-background-images/fetch.png#lightbox)

Quindi, nel `AppDelegate`, eseguire l'override del metodo `FinishedLaunching` per impostare l'intervallo di recupero minimo. In questo esempio si consente al sistema operativo di decidere con quale frequenza recuperare nuovo contenuto:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

Infine, eseguire il recupero eseguendo l'override del metodo `PerformFetch` nel `AppDelegate`e passando un *gestore di completamento*. Il gestore di completamento è un delegato che accetta un `UIBackgroundFetchResult`:

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

Al termine dell'aggiornamento del contenuto, il sistema operativo viene chiamato chiamando il gestore di completamento con lo stato appropriato. iOS offre tre opzioni per lo stato del gestore di completamento:

1. `UIBackgroundFetchResult.NewData`: viene chiamato quando viene recuperato nuovo contenuto e l'applicazione è stata aggiornata.
1. `UIBackgroundFetchResult.NoData`: viene chiamato quando l'operazione di recupero per il nuovo contenuto è stata eseguita, ma non è disponibile alcun contenuto.
1. `UIBackgroundFetchResult.Failed`: utile per la gestione degli errori, questo metodo viene chiamato quando il recupero non è stato in grado di passare.

Le applicazioni che usano il recupero in background possono effettuare chiamate per aggiornare l'interfaccia utente in background. Quando l'utente apre l'app, l'interfaccia utente sarà aggiornata e visualizzerà il nuovo contenuto. Questa operazione aggiornerà anche lo snapshot dello strumento di selezione delle app dell'applicazione, in modo che l'utente possa vedere quando l'applicazione ha nuovo contenuto.

> [!IMPORTANT]
> Una volta chiamato `PerformFetch`, l'applicazione ha circa 30 secondi per avviare il download del nuovo contenuto e chiamare il blocco del gestore di completamento. Se questa operazione richiede troppo tempo, l'app verrà terminata. Si consiglia di utilizzare il recupero in background con il _servizio di trasferimento in background_ durante il download di file multimediali o di altri file di

### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

Nel codice di esempio precedente si consente al sistema operativo di decidere con quale frequenza recuperare nuovo contenuto impostando l'intervallo di recupero minimo su `BackgroundFetchIntervalMinimum`. iOS offre tre opzioni per l'intervallo di recupero:

1. `BackgroundFetchIntervalNever`: indicare al sistema di non recuperare mai nuovi contenuti. Usare questa opzione per disattivare il recupero in determinate situazioni, ad esempio quando l'utente non è connesso. Si tratta del valore predefinito per l'intervallo di recupero. 
1. `BackgroundFetchIntervalMinimum`: consentire al sistema di decidere la frequenza di recupero in base ai modelli utente, alla durata della batteria, all'utilizzo dei dati e alle esigenze di altre applicazioni.
1. `BackgroundFetchIntervalCustom`: se si conosce la frequenza con cui viene aggiornato il contenuto di un'applicazione, è possibile specificare un intervallo di "sospensione" dopo ogni operazione di recupero, durante la quale all'applicazione verrà impedito di recuperare nuovo contenuto. Una volta che l'intervallo è attivo, il sistema determinerà quando recuperare il contenuto.

Sia `BackgroundFetchIntervalMinimum` che `BackgroundFetchIntervalCustom` si basano sul sistema per pianificare i recuperi. Questo intervallo è dinamico, adattando le esigenze del dispositivo e le abitudini dei singoli utenti. Se, ad esempio, un utente controlla un'applicazione ogni mattina e un altro controllo ogni ora, iOS garantisce che il contenuto sia aggiornato per entrambi gli utenti ogni volta che aprono l'applicazione.

Il recupero in background deve essere usato per le applicazioni che vengono aggiornate di frequente con contenuto non critico. Per le applicazioni con aggiornamenti critici, è necessario usare le notifiche remote. Le notifiche remote sono basate sul recupero in background e condividono lo stesso gestore di completamento. Verranno illustrate le notifiche remote successive.

 <a name="remote_notifications" />

## <a name="remote-notifications-ios-7-and-greater"></a>Notifiche remote (iOS 7 e versioni successive)

Le notifiche push sono messaggi JSON inviati da un provider a un dispositivo tramite il *servizio di notifica push di Apple (APNS)* .

In iOS 6 le notifiche push in ingresso indicano al sistema di avvisare l'utente che si è verificato un evento interessante in un'applicazione. Facendo clic sulla notifica si estrae l'applicazione dallo stato sospeso o terminato e l'app inizierà ad aggiornare il contenuto. iOS 7 (e versioni successive) estende le notifiche push ordinarie consentendo alle applicazioni di aggiornare il contenuto in background *prima* di inviare una notifica all'utente, in modo che l'utente possa aprire l'applicazione e presentare immediatamente il nuovo contenuto.

Per implementare le notifiche remote, modificare *info. plist* e selezionare le caselle di controllo **Abilita modalità in background** e **notifiche remote** :

 [![](updating-an-application-in-the-background-images/remote.png "Background Mode set to Enable Background Modes and Remote notifications")](updating-an-application-in-the-background-images/remote.png#lightbox)

Impostare quindi il flag di `content-available` sulla notifica push stessa su 1. Ciò consente all'applicazione di recuperare nuovo contenuto prima di visualizzare l'avviso:

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

In *AppDelegate*eseguire l'override del metodo `DidReceiveRemoteNotification` per verificare il contenuto disponibile nel payload delle notifiche e chiamare il blocco del gestore di completamento appropriato:

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

Le notifiche remote devono essere usate per aggiornamenti non frequenti con contenuto cruciale per la funzionalità dell'applicazione. Per altre informazioni sulle notifiche remote, vedere la Guida alle [notifiche push di Novell in iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md) .

> [!IMPORTANT]
> Poiché il meccanismo di aggiornamento nelle notifiche remote è basato sul recupero in background, l'applicazione deve avviare il download del nuovo contenuto e chiamare il blocco del gestore di completamento entro 30 secondi dalla ricezione della notifica oppure iOS terminerà l'applicazione. È consigliabile associare notifiche remote con il _servizio di trasferimento in background_ durante il download di file multimediali o di altri file di grandi dimensioni in background.

### <a name="silent-remote-notifications"></a>Notifiche remote invisibile all'utente

Le notifiche remote sono un modo semplice per notificare le applicazioni degli aggiornamenti e avviare il recupero del nuovo contenuto, ma in alcuni casi non è necessario notificare all'utente che qualcosa è cambiato. Se, ad esempio, un utente contrassegna un file per la sincronizzazione, non è necessario notificarlo ogni volta che il file viene aggiornato. La sincronizzazione dei file non è un evento sorprendente, né richiede l'attenzione immediata dell'utente. Gli utenti si aspettano semplicemente che il file sia aggiornato all'apertura.

Per i casi come quello precedente, iOS consente di inviare notifiche push in modo invisibile all'utente, ovvero senza un avviso. Per trasformare una notifica regolare in invisibile, è sufficiente rimuovere l'avviso dal payload di notifica:

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>Limiti di velocità

La differenza principale tra le notifiche normale e invisibile all'utente dal punto di vista dello sviluppatore è che i push invisibili sono limitati. APNs ritarderà il recapito dei push in modalità invisibile al dispositivo se la velocità di Push diventa troppo elevata. In questo modo, le applicazioni non esauriscono le risorse del dispositivo con troppe notifiche automatiche.

Tuttavia, APNs consente di inviare notifiche invisibili a "Piggyback" insieme a una normale notifica remota o a una risposta Keep-Alive. Poiché le notifiche regolari non hanno una limitazione della frequenza, possono essere usate per eseguire il push delle notifiche automatiche dalla APNs al dispositivo, come illustrato nel diagramma seguente:

 [![](updating-an-application-in-the-background-images/silent.png "Regular notifications can be used to push stored silent notifications from the APNs to the device, as illustrated by this diagram")](updating-an-application-in-the-background-images/silent.png#lightbox)

> [!IMPORTANT]
> Apple invita gli sviluppatori a inviare notifiche push in modalità invisibile all'utente ogni volta che l'applicazione richiede e consentire al APNs di pianificare il recapito.

In questa sezione sono state illustrate le diverse opzioni per aggiornare il contenuto in background per eseguire attività che non rientrano in una categoria di background necessaria. Verranno ora visualizzate alcune di queste API in azione.

 [Passaggio successivo: parte 4-procedure dettagliate per l'ambiente iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
