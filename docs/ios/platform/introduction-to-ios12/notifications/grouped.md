---
title: Notifiche raggruppate in xamarin. IOS
description: Con iOS 12, è possibile per le notifiche di gruppo nel centro notifiche o la schermata di blocco dall'applicazione o dal thread. Questo documento descrive come inviare a thread e le notifiche non filettate con xamarin. IOS.
ms.prod: xamarin
ms.assetid: C6FA7C25-061B-4FD7-8E55-88597D512F3C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 6798c4c5fa7502ba5e99cb8bc209468acaa4a9ec
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61402421"
---
# <a name="grouped-notifications-in-xamarinios"></a>Notifiche raggruppate in xamarin. IOS

Per impostazione predefinita, iOS 12 inserisce tutte le notifiche di un'app in un gruppo. La schermata di blocco e il centro notifiche di questo gruppo viene visualizzato come un mazzo con la notifica più recente nella parte superiore. Gli utenti possono espandere il gruppo per visualizzare tutte le notifiche contiene e chiudere il gruppo nel suo complesso.

Le app possono anche le notifiche di gruppo da thread, rendendo più semplice per gli utenti a individuare e interagire con le informazioni specifiche sono interessati.

## <a name="sample-app-groupednotifications"></a>App di esempio: GroupedNotifications

Per informazioni su come usare le notifiche raggruppate con xamarin. IOS, esaminiamo il [GroupedNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/GroupedNotifications) app di esempio.

Questa app di esempio simula le conversazioni con vari elementi Friend, inviare una notifica per ogni messaggio e il loro raggruppamento dal thread. Viene inoltre illustrato come non filettati ' s land notifiche in un gruppo a livello di applicazione.

Frammenti di codice in questa guida provengono da questa app di esempio.

## <a name="request-authorization-and-allow-foreground-notifications"></a>Richiedere l'autorizzazione e consentire le notifiche di primo piano

Prima di un'app può inviare le notifiche locali, deve richiedere l'autorizzazione per eseguire questa operazione. Nell'app di esempio [ `AppDelegate` ](xref:UIKit.UIApplicationDelegate), il [ `FinishedLaunching` ](xref:UIKit.UIApplicationDelegate.FinishedLaunching(UIKit.UIApplication,Foundation.NSDictionary)) metodo richiede questa autorizzazione:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    center.RequestAuthorization(UNAuthorizationOptions.Alert, (bool success, NSError error) =>
    {
        // Set the Delegate regardless of success; users can modify their notification
        // preferences at any time in the Settings app.
        center.Delegate = this;
    });
    return true;
}
```

Il [ `Delegate` ](xref:UserNotifications.UNUserNotificationCenter.Delegate) (Imposta come in precedenza) per una [ `UNUserNotificationCenter` ](xref:UserNotifications.UNUserNotificationCenter) decide se un'app in primo piano deve visualizzare una notifica in ingresso mediante la chiamata del gestore di completamento passato al [`WillPresentNotification`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.WillPresentNotification(UserNotifications.IUNUserNotificationCenterDelegate,UserNotifications.UNUserNotificationCenter,UserNotifications.UNNotification,System.Action{UserNotifications.UNNotificationPresentationOptions})):

```csharp
[Export("userNotificationCenter:willPresentotification:withCompletionHandler:")]
public void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, System.Action<UNNotificationPresentationOptions> completionHandler)
{
    completionHandler(UNNotificationPresentationOptions.Alert);
}
```

Il [ `UNNotificationPresentationOptions.Alert` ](xref:UserNotifications.UNNotificationPresentationOptions) parametro indica che l'app deve visualizzare l'avviso ma non riprodurre un suono o aggiornare un badge.

## <a name="threaded-notifications"></a>Notifiche a thread

Toccare l'app di esempio **messaggi con Alice** pulsante più volte in modo che l'invio di notifiche per una conversazione con un amico denominato Alice.
Poiché le notifiche della conversazione fanno parte dello stesso thread, la schermata di blocco e il centro notifiche raggrupparli insieme.

Per avviare una conversazione con un amico diversi, toccare il **scegliere un nuovo amico** pulsante. Le notifiche per questa conversazione vengono visualizzate in un gruppo separato.

### <a name="threadidentifier"></a>ThreadIdentifier

Qualsiasi momento che l'app di esempio avvia un nuovo thread, crea un identificatore univoco del thread:

```csharp
void StartNewThread()
{
    threadId = $"message-{friend}";
    // ...
}
```

Per inviare una notifica con thread, l'app di esempio:

- Verifica se l'app ha l'autorizzazione per inviare una notifica.
- Crea un [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
oggetto per la notifica del contenuto e imposta il [`ThreadIdentifier`](xref:UserNotifications.UNMutableNotificationContent.ThreadIdentifier)
Identificatore di thread creato in precedenza.
- Crea una richiesta e pianifica la notifica:

```csharp
async partial void ScheduleThreadedNotification(UIButton sender)
{
    var center = UNUserNotificationCenter.Current;

    UNNotificationSettings settings = await center.GetNotificationSettingsAsync();
    if (settings.AuthorizationStatus != UNAuthorizationStatus.Authorized)
    {
        return;
    }

    string author =  // ...
    string message = // ...

    var content = new UNMutableNotificationContent()
    {
        ThreadIdentifier = threadId,
        Title = author,
        Body = message,
        SummaryArgument = author
    };

    var request = UNNotificationRequest.FromIdentifier(
        Guid.NewGuid().ToString(),
        content,
        UNTimeIntervalNotificationTrigger.CreateTrigger(1, false)
    );

    center.AddNotificationRequest(request, null);

    // ...
}
```

Tutte le notifiche dall'app stessa con identico identificatore del thread verranno visualizzati nello stesso gruppo di notifica.

> [!NOTE]
> Per impostare un identificatore di thread su una notifica remota, aggiungere il `thread-id` chiave al payload JSON della notifica. Vedere di Apple [generazione di una notifica remota](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) documento per altri dettagli.

### <a name="summaryargument"></a>SummaryArgument

`SummaryArgument` Specifica come una notifica influirà il testo di riepilogo che viene visualizzato nell'angolo inferiore sinistro di un gruppo di notifica a cui appartiene la notifica. iOS aggrega il testo di riepilogo di notifiche nello stesso gruppo per creare una descrizione di riepilogo complessivo.

L'app di esempio Usa l'autore del messaggio come argomento di riepilogo. In questo modo, potrebbe essere il testo di riepilogo per un gruppo di sei notifiche con Alice **5 altre notifiche da Alice e mi**.

## <a name="unthreaded-notifications"></a>Notifiche non filettate

Ogni toccare l'app di esempio **promemoria appuntamento** pulsante Invia una delle varie notifiche promemoria appuntamento. Poiché questi promemoria non vengono concatenati, vengono visualizzati nel gruppo di notifica a livello di applicazione nella schermata di blocco e nel centro notifiche.

Per inviare una non filettata, l'app di esempio della notifica `ScheduleUnthreadedNotification` metodo utilizza codice analogo come illustrato in precedenza.
Tuttavia, non imposta il `ThreadIdentifier` nella `UNMutableNotificationContent` oggetto.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio: GroupedNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/GroupedNotifications)
- [Framework per le notifiche utente in xamarin. IOS](~/ios/platform/user-notifications/index.md)
- [Novità in notifiche utente (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Uso delle notifiche raggruppate (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/711/)
- [Le procedure consigliate e quali sono le novità per le notifiche utente (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generazione di una notifica remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
