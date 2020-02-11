---
title: Notifiche del servizio
description: Questa guida illustra come un servizio Android può usare le notifiche locali per inviare informazioni a un utente.
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: b02785863f89ef6a273c52c09f45a99c17cb6242
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024528"
---
# <a name="service-notifications"></a>Notifiche del servizio

_Questa guida illustra come un servizio Android può usare le notifiche locali per inviare informazioni a un utente._

## <a name="service-notifications-overview"></a>Panoramica sulle notifiche del servizio

Le notifiche dei servizi consentono a un'app di visualizzare le informazioni all'utente, anche se l'applicazione Android non è in primo piano. È possibile che una notifica fornisca azioni per l'utente, ad esempio la visualizzazione di un'attività da un'applicazione. Nell'esempio di codice riportato di seguito viene illustrato come un servizio può inviare una notifica a un utente:

```csharp
[Service]
public class MyService: Service 
{
    // A notification requires an id that is unique to the application.
    const int NOTIFICATION_ID = 9000;
    
    public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
    {
        // Code omitted for clarity - here is where the service would do something.
    
        // Work has finished, now dispatch anotification to let the user know.
        Notification.Builder notificationBuilder = new Notification.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_notification_small_icon)
            .SetContentTitle(Resources.GetString(Resource.String.notification_content_title))
            .SetContentText(Resources.GetString(Resource.String.notification_content_text));
        
        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.Notify(NOTIFICATION_ID, notificationBuilder.Build());
    }
}
```

Questa schermata è un esempio della notifica visualizzata:

[icona di notifica![visualizzata sulla barra di stato](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

Quando l'utente scorre la schermata di notifica dalla parte superiore, viene visualizzata la notifica completa:

![Nota visualizzata nella barra delle notifiche](service-notifications-images/02-fullnotification.png)

## <a name="updating-a-notification"></a>Aggiornamento di una notifica

Per aggiornare una notifica, il servizio ripubblicherà la notifica utilizzando lo stesso ID di notifica. Android visualizzerà o aggiornerà la notifica nella barra di stato, se necessario.

```csharp 
void UpdateNotification(string content)
{
   var notification = GetNotification(content, pendingIntent);

   NotificationManager notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
   notificationManager.Notify(NOTIFICATION_ID, notification);
}

Notification GetNotification(string content, PendingIntent intent)
{
   return new Notification.Builder(this)
           .SetContentTitle(tag)
           .SetContentText(content)
           .SetSmallIcon(Resource.Drawable.NotifyLg)
           .SetLargeIcon(BitmapFactory.DecodeResource(Resources, Resource.Drawable.Icon))
           .SetContentIntent(intent).Build();
}
```

Altre informazioni sulle notifiche sono disponibili nella sezione [notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md) della Guida alle [notifiche di Android](~/android/app-fundamentals/notifications/index.md) .

## <a name="related-links"></a>Collegamenti correlati

- [Notifiche locali in Android](~/android/app-fundamentals/notifications/local-notifications.md)
