---
title: L'aggiornamento di un'App xamarin. IOS in Background
description: Questo documento descrive vari modi per aggiornare un'app xamarin. IOS che si trova in background, ad esempio area monitoraggio, recupero in background e le notifiche remote.
ms.prod: xamarin
ms.assetid: A2B2231A-C045-4C11-8176-F9966485197A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 835dccaea79467582f56fd4b8b6b3b8f42acd632
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103231"
---
# <a name="updating-a-xamarinios-app-in-the-background"></a>L'aggiornamento di un'App xamarin. IOS in Background

Aggiornamento in background è il processo di risvegliare il gigante che un'applicazione che è stato sospeso o non in esecuzione e lo aggiorna con nuovo contenuto. iOS offre tre opzioni per l'aggiornamento del contenuto in background:

1.  *Monitoraggio di area* e *significativi percorso modifiche servizio* -sfondo trigger APIs con riconoscimento della posizione degli aggiornamenti in base alle modifiche nella posizione dell'utente. Queste API possono essere utilizzate con discrezione di aggiornamento del contenuto nelle applicazioni non basate su posizione iOS 6, in cui non sono disponibili altre opzioni.
1.  *Sfondo (iOS 7 +) di recupero* -un approccio temporale per l'aggiornamento *non critici* contenuto che aggiorna *spesso* .
1.  *Notifiche remote (iOS 7 +)* -applicazioni che ricevono le notifiche push possono usare le notifiche per attivare gli aggiornamenti del contenuto in background. Questo metodo può essere utilizzato per l'aggiornamento con *importanti, scadenza* contenuto che consente di aggiornare *sporadicamente* .


Le sezioni seguenti illustrano le nozioni di base di queste opzioni.

## <a name="region-monitoring-and-significant-location-changes"></a>Area monitoraggio e la modifica del percorso significativo

iOS offre due API con riconoscimento della posizione con elaborazione in background in funzionalità:

1.  *Area monitoraggio* è il processo di impostazione di aree con i limiti e riattivare il dispositivo quando l'utente entra o esce da un'area. Le aree sono circolari e possono essere di dimensioni diverse. Quando l'utente supera un limite di area, il dispositivo attiveranno per gestire l'evento, in genere per la generazione di una notifica o di avvio di un'attività. Area monitoraggio richiede GPS e aumenta di batteria e consumo dei dati.
1.  Il *servizio di modifiche significative percorso* è disponibile per i dispositivi con radio cellulare un'opzione più semplice e risparmio di energia. Un'applicazione in ascolto per le modifiche significative percorso riceverà una notifica quando il dispositivo passa towers cella. Questo servizio può essere utilizzato per riattivare un'applicazione sospesa o terminata e offre l'opportunità di verificare la presenza di nuovi contenuti in background. Attività in background è limitato a circa 10 secondi, a meno che non associato a un [attività in Background](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/ios-backgrounding-with-tasks.md) .


Un'applicazione non è necessario il percorso `UIBackgroundMode` usare queste API con riconoscimento della posizione. Poiché iOS non tenere traccia dei tipi di attività che possono essere eseguite quando il dispositivo viene attivato dalle modifiche apportate alla posizione dell'utente, queste API forniscono una soluzione alternativa per l'aggiornamento del contenuto in background in iOS 6. *Tenere a mente che attivare gli aggiornamenti in background con le API basate sulla posizione verrà disegnato su risorse di dispositivo e potrebbe confondere gli utenti che non conoscono il motivo per cui un'applicazione richiede l'accesso al relativo percorso*. Prestare attenzione durante l'implementazione di modifiche significative di posizione o area monitoraggio per elaborazione nelle applicazioni che non si usano già le API di posizione in background.

Le app tramite il percorso di monitoraggio per l'elaborazione in background esporre un difetto in iOS 6: se esigenze di un'applicazione non possono essere inseriti in una categoria in background necessarie, l'ha limitata le opzioni di elaborazione in background in. Con l'introduzione di due nuove API, *recupero in Background* e *notifiche Remote*, iOS 7 (e versioni successive) offre l'opportunità di elaborazione in background in altre applicazioni. Due sezioni successive introducono le nuove API.

<a name="background_fetch" />

## <a name="background-fetch-ios-7-and-greater"></a>Recupero in background (iOS 7 e versioni successive)

In iOS 6, un'applicazione di immissione di primo piano necessaria ora caricare nuovo contenuto, presentando brevemente gli utenti con contenuto che già visto. Recupero in background consente alle applicazioni di caricare i dati nuovi *prima di* un utente avvia l'applicazione e forniscono all'utente il contenuto più aggiornato.

Per implementare il recupero in background, modificare *Info. plist* e selezionare il **Abilita modalità in Background** e **recupero in Background** caselle di controllo:

 [![](updating-an-application-in-the-background-images/fetch.png "Modificare il file Info. plist e selezionare le caselle di controllo Abilita modalità in Background e per il recupero in Background")](updating-an-application-in-the-background-images/fetch.png#lightbox)

Successivamente, nella `AppDelegate`, eseguire l'override di `FinishedLaunching` metodo per impostare l'intervallo di recupero minimo. In questo esempio viene consentito il sistema operativo scegliere la frequenza con cui recuperare il nuovo contenuto:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
  UIApplication.SharedApplication.SetMinimumBackgroundFetchInterval (UIApplication.BackgroundFetchIntervalMinimum);
  return true;
}
```

Infine, eseguire l'operazione di recupero eseguendo l'override di `PerformFetch` metodo nella `AppDelegate`e passando un *gestore completamento*. Il gestore di completamento è un delegato che accetta un `UIBackgroundFetchResult`:

```csharp
public override void PerformFetch (UIApplication application, Action<UIBackgroundFetchResult> completionHandler)
{
  // Check for new data, and display it
  ...
  
  // Inform system of fetch results
  completionHandler (UIBackgroundFetchResult.NewData);
}
```

Al termine l'aggiornamento del contenuto, è informare il sistema operativo, chiamare il gestore di completamento con lo stato appropriato. iOS offre tre opzioni per lo stato del gestore di completamento:

1.  `UIBackgroundFetchResult.NewData` -Viene chiamato quando il nuovo contenuto è stato recuperato e l'applicazione è stata aggiornata.
1.  `UIBackgroundFetchResult.NoData` -Viene chiamato quando l'operazione di recupero per il nuovo contenuto è stato eseguito, ma non è disponibile alcun contenuto.
1.  `UIBackgroundFetchResult.Failed` : Utile per la gestione degli errori, viene chiamato quando l'operazione di recupero non è riuscito a passare attraverso.


Applicazioni con recupero in Background possono effettuare chiamate per aggiornare l'interfaccia utente dallo sfondo. Quando l'utente apre l'app, l'interfaccia utente saranno aggiornate e visualizzare il nuovo contenuto. Snapshot della selezione di App dell'applicazione, verranno aggiornate anche in modo che l'utente vede quando l'applicazione dispone di nuovi contenuti.

> [!IMPORTANT]
> Una volta `PerformFetch` viene chiamato, l'applicazione ha circa 30 secondi per avviare il download del contenuto nuovo e chiamare il blocco del gestore completamento. Se si impiega troppo tempo, l'app verrà terminato. È consigliabile usare il recupero in Background con il _servizio di trasferimento in Background_ durante il download di supporto o altri file di grandi dimensioni.


### <a name="backgroundfetchinterval"></a>BackgroundFetchInterval

Nel codice di esempio precedente, viene consentito il sistema operativo scegliere la frequenza con cui recuperare il contenuto nuovo impostando l'intervallo di recupero minimo su `BackgroundFetchIntervalMinimum`. iOS offre tre opzioni per l'intervallo di recupero:

1.  `BackgroundFetchIntervalNever` -Indica al sistema mai recuperare nuovi contenuti. Questa scheda consente di disattivare il recupero in determinate situazioni, ad esempio quando l'utente non è connesso. Questo è il valore predefinito per l'intervallo di recupero. 
1.  `BackgroundFetchIntervalMinimum` -Consentire al sistema di definire la frequenza di recupero basato su modelli utente, la durata della batteria, utilizzo dei dati e le esigenze di altre applicazioni.
1.  `BackgroundFetchIntervalCustom` -Se si conosce con quale frequenza viene aggiornato il contenuto di un'applicazione, è possibile specificare un intervallo di "sospensione" dopo ogni operazione di recupero, durante il quale l'applicazione verrà impedita il recupero dei nuovi contenuti. Quando questo intervallo è attivo, il sistema determina se recuperare il contenuto.


Entrambe `BackgroundFetchIntervalMinimum` e `BackgroundFetchIntervalCustom` si basano sul sistema per pianificare operazioni di recupero. Questo intervallo è dinamico e adattarsi alle esigenze del dispositivo, nonché sulle abitudini dell'utente individuale. Ad esempio, se un utente archivia un'applicazione ogni mattina, e un'altra verifica ogni ora, iOS garantirà il contenuto viene aggiornata sia per gli utenti ogni volta che aprono l'applicazione.

Recupero in background deve essere utilizzato per le applicazioni che aggiornano frequentemente con contenuto non critici. Per le applicazioni con gli aggiornamenti critici, usare le notifiche Remote. Notifiche remote sono basate sul recupero in Background e condividono lo stesso gestore completamento. Verrà esaminato successivamente notifiche Remote.

 <a name="remote_notifications" />


## <a name="remote-notifications-ios-7-and-greater"></a>Notifiche remote (iOS 7 e versioni successive)

Le notifiche push sono i messaggi JSON inviati da un provider a un dispositivo tramite il *il servizio Apple Push Notification (APN)*.

In iOS 6, una notifica push in ingresso indica al sistema per avvisare l'utente che qualcosa di interessante si è verificati in un'applicazione. Facendo clic sulla notifica Scarica l'applicazione perde lo stato di sospensione o terminato e l'app è preferibile iniziare l'aggiornamento del contenuto. iOS 7 (e versioni successive) estende le notifiche push ordinario fornendo applicazioni possibilità di aggiornare il contenuto in background *prima di* avvisare l'utente, in modo che l'utente può aprire l'applicazione e visualizzata con il nuovo contenuto immediatamente.

Per implementare le notifiche remote, modificare *Info. plist* e selezionare il **Abilita modalità in Background** e **notifiche Remote** caselle di controllo:

 [![](updating-an-application-in-the-background-images/remote.png "Imposta la modalità in background per abilitare la modalità in Background e le notifiche Remote")](updating-an-application-in-the-background-images/remote.png#lightbox)

Impostare quindi il `content-available` flag di notifica push su 1. In questo modo l'applicazione in grado di recuperare nuovi contenuti prima di visualizzare l'avviso:

```csharp
'aps' {
  'content-available': 1,
  'alert': 'Something new has happened in your app!''
}
```

Nel *AppDelegate*, eseguire l'override di `DidReceiveRemoteNotification` metodo per verificare il payload della notifica per il contenuto disponibile, chiamare il blocco del gestore di completamento appropriato:

```csharp
public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
{
  if([content-available]) {
    // fetch content
    completionHandler (UIBackgroundFetchResult.NewData);
  }
}
```

Notifiche remote devono essere utilizzate per gli aggiornamenti non frequenti con contenuto che è fondamentale per le funzionalità dell'applicazione. Per altre informazioni sulle notifiche remote, vedere il Xamarin [notifiche Push in iOS](~/ios/platform/user-notifications/deprecated/remote-notifications-in-ios.md) Guida.

> [!IMPORTANT]
> Poiché il meccanismo di aggiornamento nelle notifiche Remote è basato su recupero in Background, l'applicazione deve avviare il download del contenuto nuovo e chiamare il blocco del gestore completamento entro 30 secondi di ricevere la notifica oppure per terminare l'applicazione iOS. Prendere in considerazione le notifiche Remote con accoppiamento _servizio di trasferimento in Background_ durante il download di supporto o altri file di grandi dimensioni in background.


### <a name="silent-remote-notifications"></a>Notifiche Remote invisibile all'utente

Notifiche remote sono un modo semplice per notificare alle applicazioni di aggiornamenti e avviare il recupero dei nuovi contenuti, ma vi sono casi in cui non è necessario informare l'utente che qualcosa è stato modificato. Ad esempio, se un utente di flag di un file per la sincronizzazione, non dobbiamo inviare una notifica ogni volta che aggiorna il file. Sincronizzazione file non è un evento sorprendente, e non è necessario un intervento immediato dell'utente. Gli utenti si aspettano semplicemente il file sia aggiornata quando i colleghi aprono il collegamento.

Per casi come quello sopra riportato, iOS consente di notifiche push da inviare in modo invisibile, vale a dire, senza un avviso. Per attivare una notifica in uno invisibile all'utente normale, rimuovere semplicemente l'avviso dal payload della notifica:

```csharp
'aps' {
  'content-available': 1
}
```

#### <a name="rate-limits"></a>Limiti di velocità

La differenza principale tra le notifiche di normali e invisibile all'utente dalla prospettiva dello sviluppatore è che un push invisibile all'utente è frequenza limitata. Se la frequenza di push diventi troppo elevata, APNs ritarderà il recapito di notifiche push invisibile all'utente al dispositivo. Questo modo si garantisce che le applicazioni non sottrarre risorse a dispositivi con numero eccessivo di notifiche invisibile all'utente.

Tuttavia, APNs consentirà le notifiche silent "occuparsi" insieme a una normale notifica remota o una risposta keep-alive. Poiché notifiche periodiche non sono a frequenza limitata, possono essere utilizzati per effettuare il push stored backup le notifiche silent da APNs al dispositivo, come illustrato nel diagramma seguente:

 [![](updating-an-application-in-the-background-images/silent.png "Notifiche periodiche possono essere utilizzate per eseguire il push le notifiche silent stored da APNs al dispositivo, come illustrato in questo diagramma")](updating-an-application-in-the-background-images/silent.png#lightbox)

> [!IMPORTANT]
> Apple incoraggia gli sviluppatori per inviare notifiche push invisibile all'utente ogni volta che l'applicazione richiede, lasciando che il servizio APNs pianificare il recapito.


In questa sezione, abbiamo affrontato le varie opzioni per l'aggiornamento del contenuto in background per eseguire le attività che non rientrano in una categoria in background necessarie. A questo punto, esaminiamo alcune di queste API in azione.

 [Passaggio successivo: Parte 4 - procedure dettagliate di elaborazione in background in iOS](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/index.md)
