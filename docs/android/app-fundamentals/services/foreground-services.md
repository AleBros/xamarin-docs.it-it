---
title: Servizi in primo piano
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: df917896f901060a5518076afa859d34a03f4d6d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61012349"
---
# <a name="foreground-services"></a>Servizi in primo piano

Un servizio di primo piano è un tipo speciale di un servizio associato a un servizio avviato. In alcuni casi verranno eseguite le attività che gli utenti devono tenere attivamente di, questi servizi sono detti _servizi in primo piano_. Un esempio di un servizio di primo piano è un'app che fornisce all'utente con le direzioni durante piedi o in auto. Anche se l'app è in background, è comunque importante che il servizio abbia risorse sufficienti per funzionare correttamente e che l'utente disponga di un modo rapido e comodo per accedere all'app. Per un'app per Android, ciò significa che un servizio di primo piano deve ricevere la priorità più elevata rispetto a un servizio "normale" e un servizio di primo piano deve fornire un `Notification` che Android visualizzerà fino a quando il servizio è in esecuzione.
 
Per avviare un servizio di primo piano, l'app deve inviare un Intent che chiede ad Android per avviare il servizio. Quindi il servizio deve registrarsi come un servizio di primo piano con Android. Le App in esecuzione in Android 8.0 (o versione successiva) devono usare il `Context.StartForegroundService` per avviare il servizio, mentre le App in esecuzione su dispositivi con una versione precedente di Android devono usare (metodo) `Context.StartService`

Questo metodo di estensione c# è un esempio di come avviare un servizio di primo piano. In Android 8.0 e versioni successive utilizzeranno i `StartForegroundService` metodo, in caso contrario, meno recente `StartService` verrà utilizzato il metodo.  

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

## <a name="registering-as-a-foreground-service"></a>La registrazione come un servizio di primo piano

Dopo aver avviato un servizio di primo piano, è necessario registrarsi con Android richiamando il [ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/). Se il servizio viene avviato con il `Service.StartForegroundService` (metodo), ma non è registrato, quindi Android arresterà il servizio e contrassegna l'app non risponde.

`StartForeground` accetta due parametri, che sono obbligatori:
 
* Valore intero univoco all'interno dell'applicazione per identificare il servizio.
* Oggetto `Notification` oggetto che Android verrà visualizzato nella barra di stato per fino a quando il servizio è in esecuzione.

Android visualizzerà la notifica nella barra di stato per fino a quando il servizio è in esecuzione. La notifica, come minimo, offrirà un'indicazione visiva per l'utente che esegue il servizio. In teoria, la notifica deve fornire l'utente con un collegamento all'applicazione o eventualmente alcuni pulsanti di azione per controllare l'applicazione. Un esempio di questo è un lettore musicale &ndash; la notifica visualizzata potrebbero essere disponibili pulsanti per sospendere/Riproduci musica, per tornare al brano precedente o per andare al brano successivo. 

Questo frammento di codice è riportato un esempio di registrazione di un servizio come un servizio di primo piano:   

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

La notifica precedente verrà visualizzata una notifica della barra di stato simile al seguente:

![Immagine che illustra la notifica nella barra di stato](foreground-services-images/foreground-services-01.png "immagine che illustra la notifica nella barra di stato")

Questo screenshot Mostra la notifica espansa nell'area di notifica con due azioni che consentono all'utente controllare il servizio:

![Immagine che illustra la notifica espansa](foreground-services-images/foreground-services-02.png "immagine che illustra la notifica espansa.")

Ulteriori informazioni sulle notifiche sono disponibile nel [notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md) sezione del [notifiche Android](~/android/app-fundamentals/notifications/index.md) Guida.

## <a name="unregistering-as-a-foreground-service"></a>Annullamento della registrazione come un servizio di primo piano

Un servizio può deprovisioning elenco stesso come un servizio di primo piano chiamando il metodo `StopForeground`. `StopForeground` non interrompe il servizio, ma verrà quindi rimossa l'icona di notifica e segnala Android che questo servizio può essere arrestato se necessario.

La notifica della barra di stato visualizzato può essere rimosso anche passando `true` al metodo: 

```csharp
StopForeground(true);
```

Se il servizio viene interrotto con una chiamata a `StopSelf` o `StopService`, verrà rimossa la notifica della barra di stato.

## <a name="related-links"></a>Collegamenti correlati

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForeground](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [Notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
