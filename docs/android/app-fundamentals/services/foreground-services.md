---
title: Servizi di primo piano
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 03/19/2018
ms.openlocfilehash: 3088fa4b5cfa21ac57533ef331ffcc15414e14b4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="foreground-services"></a>Servizi di primo piano

Un servizio di primo piano è un tipo speciale di un servizio associato o un servizio avviato. In alcuni casi services eseguirà le attività che gli utenti devono tenere conto attivamente, questi servizi sono note come _servizi di primo piano_. Un esempio di un servizio di primo piano è un'applicazione che fornisce l'utente con le direzioni durante a piedi o Auto. Anche se l'applicazione in background, è comunque importante che il servizio disponga di risorse sufficienti per funzionare correttamente e che l'utente disponga di un modo rapido e facile per accedere all'app. Per un'app per Android, ciò significa che un servizio di primo piano deve ricevere una priorità maggiore rispetto a un servizio "normale" e un servizio di primo piano è necessario fornire un `Notification` che Android consente di visualizzare fino a quando il servizio è in esecuzione.
 
Per avviare un servizio di primo piano, l'app deve essere inviato un intento indicante Android per avviare il servizio. Quindi il servizio deve eseguire la registrazione come un servizio di primo piano con Android. Le App in esecuzione su Android 8.0 (o versione successiva) devono utilizzare il `Context.StartForegroundService` metodo per avviare il servizio, mentre devono usare le App in esecuzione su dispositivi con una versione precedente di Android `Context.StartService`

Questo metodo di estensione c# è un esempio di come avviare un servizio di primo piano. In Android 8.0 e versioni successive verrà utilizzato il `StartForegroundService` metodo, in caso contrario il precedente `StartService` verrà utilizzato il metodo.  

```csharp
public static void StartForegroundServiceComapt<T>(this Context context, Bundle args = null) where T : Service
{
    var intent = new Intent(context, typeof(T));
    if (args != null) 
    {
        intent.PutExtras(args);
    }

    if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.O)
    {
        context.StartForegroundService(intent);
    }
    else
    {
        context.StartService(intent);
    }
}
```

## <a name="registering-as-a-foreground-service"></a>Registrazione come un servizio di primo piano

Una volta avviato un servizio di primo piano, deve eseguire la registrazione con Android richiamando il [ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/). Se il servizio viene avviato con il `Service.StartForegroundService` metodo ma non è registrato, quindi Android verrà arrestato il servizio e contrassegnare l'applicazione non risponde.

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

Se il servizio viene interrotto con una chiamata a `StopSelf` o `StopService`, verrà rimossa la notifica della barra di stato.

## <a name="related-links"></a>Collegamenti correlati

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForegrond](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [Notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
