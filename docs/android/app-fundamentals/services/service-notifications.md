---
title: Notifiche del servizio
description: Questa guida illustra come un servizio di Android può usare le notifiche locali per inviare informazioni a un utente.
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d56f67254a9eae334fa8ac3f08d3ef270800c309
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61012354"
---
# <a name="service-notifications"></a>Notifiche del servizio

_Questa guida illustra come un servizio di Android può usare le notifiche locali per inviare informazioni a un utente._


## <a name="service-notifications-overview"></a>Panoramica delle notifiche di servizio

Notifiche del servizio di consentono a un'app visualizzare le informazioni all'utente, anche se l'applicazione Android non è in primo piano. È possibile che una notifica per suggerire le azioni dell'utente, ad esempio la visualizzazione di un'attività da un'applicazione. Esempio di codice seguente viene illustrato come un servizio può inviare una notifica a un utente:

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

Questa schermata è riportato un esempio di notifica che viene visualizzata:

[![Icona di notifica visualizzato nella barra di stato](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

Quando la schermata notifica dall'alto verso il basso le diapositive utente, viene visualizzata la notifica completa:

![Notifica a visualizzato nella barra di notifica](service-notifications-images/02-fullnotification.png)


## <a name="updating-a-notification"></a>L'aggiornamento di una notifica

Per aggiornare una notifica, il servizio viene ripubblicato la notifica usando lo stesso ID di notifica. Android consente di visualizzare o aggiornare la notifica nella barra di stato in base alle esigenze.

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

Ulteriori informazioni sulle notifiche sono disponibile nel [notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md) sezione del [notifiche Android](~/android/app-fundamentals/notifications/index.md) Guida.


## <a name="related-links"></a>Collegamenti correlati

- [Notifiche locali in Android](~/android/app-fundamentals/notifications/local-notifications.md)
