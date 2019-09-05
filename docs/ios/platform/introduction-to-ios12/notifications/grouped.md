---
title: Notifiche raggruppate in Novell. iOS
description: Con iOS 12, è possibile raggruppare le notifiche nel centro notifiche o nella schermata di blocco in base all'applicazione o al thread. Questo documento descrive come inviare notifiche con thread e senza Threading con Novell. iOS.
ms.prod: xamarin
ms.assetid: C6FA7C25-061B-4FD7-8E55-88597D512F3C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/04/2018
ms.openlocfilehash: 12d60a193385593bb3ec22186b54a4a809370e2d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291259"
---
# <a name="grouped-notifications-in-xamarinios"></a>Notifiche raggruppate in Novell. iOS

Per impostazione predefinita, iOS 12 inserisce tutte le notifiche di un'app in un gruppo. La schermata di blocco e il centro notifiche visualizzano questo gruppo come stack con la notifica più recente in primo piano. Gli utenti possono espandere il gruppo per visualizzare tutte le notifiche contenute ed eliminare il gruppo nel suo complesso.

Le app possono anche raggruppare le notifiche in base al thread, semplificando la ricerca e l'interazione degli utenti con le informazioni specifiche a cui si è interessati.

## <a name="sample-app-groupednotifications"></a>App di esempio: GroupedNotifications

Per informazioni su come usare le notifiche raggruppate con Novell. iOS, vedere l'app di esempio [GroupedNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-groupednotifications) .

Questa app di esempio simula le conversazioni con diversi amici, inviando una notifica per ogni messaggio e eseguendone il raggruppamento per thread. Viene inoltre illustrato il modo in cui le notifiche senza thread vengono inserite in un gruppo a livello di applicazione.

I frammenti di codice in questa guida provengono da questa applicazione di esempio.

## <a name="request-authorization-and-allow-foreground-notifications"></a>Richiedi autorizzazione e Consenti notifiche in primo piano

Prima che un'app possa inviare notifiche locali, deve richiedere l'autorizzazione per eseguire questa operazione. Nell'app di [`AppDelegate`](xref:UIKit.UIApplicationDelegate)esempio, il metodo [`FinishedLaunching`](xref:UIKit.UIApplicationDelegate.FinishedLaunching(UIKit.UIApplication,Foundation.NSDictionary)) richiede l'autorizzazione seguente:

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

(Impostato sopra) per un oggetto [`UNUserNotificationCenter`](xref:UserNotifications.UNUserNotificationCenter) decide se un'app in primo piano deve visualizzare o meno una notifica in ingresso chiamando il gestore di completamento passato a [`WillPresentNotification`:](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.WillPresentNotification(UserNotifications.IUNUserNotificationCenterDelegate,UserNotifications.UNUserNotificationCenter,UserNotifications.UNNotification,System.Action{UserNotifications.UNNotificationPresentationOptions})) [`Delegate`](xref:UserNotifications.UNUserNotificationCenter.Delegate)

```csharp
[Export("userNotificationCenter:willPresentotification:withCompletionHandler:")]
public void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, System.Action<UNNotificationPresentationOptions> completionHandler)
{
    completionHandler(UNNotificationPresentationOptions.Alert);
}
```

Il [`UNNotificationPresentationOptions.Alert`](xref:UserNotifications.UNNotificationPresentationOptions) parametro indica che l'app deve visualizzare l'avviso ma non riprodurre un suono o aggiornare una notifica.

## <a name="threaded-notifications"></a>Notifiche con thread

Toccare ripetutamente il messaggio dell'app di esempio con il pulsante **Alice** per inviare notifiche per una conversazione con un amico denominato Alice.
Poiché le notifiche di questa conversazione fanno parte dello stesso thread, la schermata di blocco e il centro notifiche li raggruppano insieme.

Per avviare una conversazione con un altro amico, toccare il pulsante **Scegli un nuovo amico** . Le notifiche per questa conversazione vengono visualizzate in un gruppo separato.

### <a name="threadidentifier"></a>ThreadIdentifier

Ogni volta che l'app di esempio avvia un nuovo thread, viene creato un identificatore univoco del thread:

```csharp
void StartNewThread()
{
    threadId = $"message-{friend}";
    // ...
}
```

Per inviare una notifica a thread, l'app di esempio:

- Verifica se l'app dispone dell'autorizzazione per l'invio di una notifica.
- Crea un oggetto[`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
oggetto per il contenuto della notifica e imposta la relativa proprietà[`ThreadIdentifier`](xref:UserNotifications.UNMutableNotificationContent.ThreadIdentifier)
all'identificatore del thread creato in precedenza.
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

Tutte le notifiche della stessa app con lo stesso identificatore di thread verranno visualizzate nello stesso gruppo di notifiche.

> [!NOTE]
> Per impostare un identificatore di thread in una notifica remota, aggiungere `thread-id` la chiave al payload JSON della notifica. Per altri dettagli, vedere la pagina relativa alla generazione di un documento di [notifica remoto](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) di Apple.

### <a name="summaryargument"></a>SummaryArgument

`SummaryArgument`Specifica il modo in cui una notifica avrà un effetto sul testo di riepilogo visualizzato nell'angolo inferiore sinistro di un gruppo di notifica a cui appartiene la notifica. iOS aggrega il testo di riepilogo dalle notifiche nello stesso gruppo per creare una descrizione complessiva del riepilogo.

L'app di esempio usa l'autore del messaggio come argomento di riepilogo. Con questo approccio, il testo di riepilogo per un gruppo di sei notifiche con Alice potrebbe essere costituito **da altre 5 notifiche da Alice e me**.

## <a name="unthreaded-notifications"></a>Notifiche non thread

Ogni tocco del pulsante di **promemoria appuntamento** dell'app di esempio invia una delle varie notifiche di sollecito appuntamento. Poiché questi promemoria non sono threaded, vengono visualizzati nel gruppo di notifica a livello di applicazione nella schermata di blocco e nel centro notifiche.

Per inviare una notifica non thread, il metodo dell'app di `ScheduleUnthreadedNotification` esempio usa codice simile a quello riportato in precedenza.
Tuttavia, non imposta l' `ThreadIdentifier` `UNMutableNotificationContent` oggetto sull'oggetto.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio-GroupedNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-groupednotifications)
- [Framework delle notifiche utente in Novell. iOS](~/ios/platform/user-notifications/index.md)
- [Novità delle notifiche utente (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [Uso delle notifiche raggruppate (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/711/)
- [Procedure consigliate e novità delle notifiche utente (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [Generazione di una notifica remota (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
