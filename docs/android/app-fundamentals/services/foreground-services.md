---
title: Servizi di primo piano
ms.topic: article
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/09/2018
ms.openlocfilehash: 96e8d1a3658a515b6b1d37cf0fdd93157954c01d
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="foreground-services"></a>Servizi di primo piano

Alcuni servizi esegue alcune attività che gli utenti siano informati attivamente, questi servizi sono note come _servizi di primo piano_. Un esempio di un servizio di primo piano è un'applicazione che fornisce l'utente con le direzioni durante a piedi o Auto. Anche se l'applicazione in background, è comunque importante che il servizio disponga di risorse sufficienti per funzionare correttamente e che l'utente disponga di un modo rapido e facile per accedere all'app. Per un'app per Android, ciò significa che un servizio di primo piano deve ricevere una priorità maggiore rispetto a un servizio "normale" e un servizio di primo piano è necessario fornire un `Notification` che Android consente di visualizzare fino a quando il servizio è in esecuzione.
 
Un servizio di primo piano viene creato e avviato come qualsiasi altro servizio. Durante l'avvio di servizio, verranno registrati automaticamente con Android come un servizio di primo piano.
 
Questa Guida verrà illustrati i passaggi aggiuntivi che devono essere prese per registrare un servizio di primo piano e per arrestare il servizio dopo averlo.

## <a name="registering-as-a-foreground-service"></a>Registrazione come un servizio di primo piano

Un servizio di primo piano è un tipo speciale di un servizio associato o un servizio avviato. Il servizio, una volta avviata, chiama il [ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/) metodo di registrarsi con Android come un servizio di primo piano.   

`StartForeground` accetta due parametri, che sono obbligatorie:
 
* Valore integer che è univoco all'interno dell'applicazione per identificare il servizio.
* Oggetto `Notification` oggetto visualizzato nella barra di stato per Android, purché il servizio è in esecuzione.

Android visualizzerà la notifica sulla barra di stato per fino a quando il servizio è in esecuzione. La notifica, come minimo, fornirà un indicatore visivo per l'utente che il servizio è in esecuzione. Idealmente, la notifica deve fornire all'utente con un collegamento all'applicazione o eventualmente alcuni pulsanti di azione per controllare l'applicazione. Un esempio di questo è un lettore musicale &ndash; la notifica che viene visualizzata potrebbero essere disponibili pulsanti per sospendere o play musica, torna brano precedente o per ignorare il brano successivo. 

Questo frammento di codice è riportato un esempio di registrazione di un servizio come servizio in primo piano:   

```csharp
// This is any integer value unique to the application.
public const int SERVICE_RUNNING_NOTIFICATION_ID = 10000;

public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
{
    // Code not directly related to publishing the notification has been omitted for clarity.
    // Normally, this method would hold the code to be run when the service is started.
    
    var notification = new Notification.Builder(this)
        .SetContentTitle(Resources.GetString(Resource.String.app_name))
        .SetContentText(Resources.GetString(Resource.String.notification_text))
        .SetSmallIcon(Resource.Drawable.ic_stat_name)
        .SetContentIntent(BuildIntentToShowMainActivity())
        .SetOngoing(true)
        .AddAction(BuildRestartTimerAction())
        .AddAction(BuildStopServiceAction())
        .Build();

    // Enlist this instance of the service as a foreground service
    StartForeground(SERVICE_RUNNING_NOTIFICATION_ID, notification);
}
```

La notifica precedente visualizzerà una notifica della barra di stato che è simile al seguente:

![Immagine che illustra la notifica sulla barra di stato](foreground-services-images/foreground-services-01.png "immagine che illustra la notifica sulla barra di stato")

Questa schermata mostra la notifica espansa nella barra delle applicazioni con due azioni che consentono all'utente di controllare il servizio di notifica:

![Immagine che illustra la notifica espansa](foreground-services-images/foreground-services-02.png "immagine che illustra la notifica espansa.")

Ulteriori informazioni sulle notifiche sono disponibile nel [notifiche locale](~/android/app-fundamentals/notifications/local-notifications.md) sezione la [notifiche Android](~/android/app-fundamentals/notifications/index.md) Guida.

## <a name="unregistering-as-a-foreground-service"></a>Annullamento della registrazione come un servizio di primo piano

Un servizio può deallocare elenco se stesso come un servizio di primo piano chiamando il metodo `StopForeground`. `StopForeground` non interrompe il servizio, ma verranno rimosse l'icona di notifica e segnali Android che questo servizio può essere arrestato se necessario.

La notifica della barra di stato visualizzate può essere rimossi anche passando `true` al metodo: 

```csharp
StopForeground(true);
```

Se il servizio viene interrotto con una chiamata a `StopSelf` o `StopService`, quindi verrà rimossa in modo analogo la notifica della barra di stato.


## <a name="related-links"></a>Collegamenti correlati

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForegrond](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [Notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
