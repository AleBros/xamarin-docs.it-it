---
title: Notifiche di servizio
description: Questa guida viene descritto un servizio Android potrebbe consentire l'utilizzo di notifiche locale per inviare informazioni a un utente.
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 65ccb94bd4fac1f3818b4f08eb34ecf73d6c52e4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="service-notifications"></a>Notifiche di servizio

_Questa guida viene descritto un servizio Android potrebbe consentire l'utilizzo di notifiche locale per inviare informazioni a un utente._


## <a name="service-notifications-overview"></a>Panoramica delle notifiche del servizio

Le notifiche di servizio consentono un'app per visualizzare le informazioni all'utente, anche se l'applicazione di Android non è in primo piano. È possibile che una notifica suggerire le azioni per l'utente, ad esempio la visualizzazione di un'attività da un'applicazione. Esempio di codice riportato di seguito viene illustrato come un servizio può inviare una notifica a un utente:

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

Questa schermata è riportato un esempio di notifica che viene visualizzato:

[![Icona di notifica visualizzata nella barra di stato](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

Quando la schermata di notifica dall'alto verso il basso le diapositive utente, viene visualizzata la notifica completa:

![Notication visualizzato nella barra di notifica](service-notifications-images/02-fullnotification.png)


## <a name="updating-a-notification"></a>L'aggiornamento di una notifica

Per aggiornare una notifica, il servizio viene ripubblicato la notifica usando lo stesso ID di notifica. Android consente di visualizzare o aggiornare la notifica sulla barra di stato in base alle esigenze.

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

Ulteriori informazioni sulle notifiche sono disponibile nel [notifiche locale](~/android/app-fundamentals/notifications/local-notifications.md) sezione la [notifiche Android](~/android/app-fundamentals/notifications/index.md) Guida.


## <a name="related-links"></a>Collegamenti correlati

- [Notifiche locale in Android](~/android/app-fundamentals/notifications/local-notifications.md)
