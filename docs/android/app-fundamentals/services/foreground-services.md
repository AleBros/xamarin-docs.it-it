---
title: Servizi in primo piano
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: aaef03d68f4ec374a3bc706daffe636e575e42ff
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024750"
---
# <a name="foreground-services"></a>Servizi in primo piano

Un servizio in primo piano è un tipo speciale di servizio associato o un servizio avviato. Occasionalmente i servizi eseguiranno le attività che gli utenti devono conoscere attivamente, questi servizi sono noti come _Servizi in primo piano_. Un esempio di servizio in primo piano è costituito da un'app che fornisce all'utente le istruzioni per la guida o l'esplorazione. Anche se l'app è in background, è ancora importante che il servizio disponga di risorse sufficienti per funzionare correttamente e che l'utente disponga di un modo rapido e pratico per accedere all'app. Per un'app Android, questo significa che un servizio in primo piano deve ricevere una priorità più alta rispetto a un servizio "normale" e un servizio in primo piano deve fornire un `Notification` che verrà visualizzato da Android fino a quando il servizio è in esecuzione.

Per avviare un servizio in primo piano, l'app deve inviare una finalità che indica ad Android di avviare il servizio. Il servizio deve quindi registrarsi come servizio in primo piano con Android. Le app in esecuzione in Android 8,0 (o versioni successive) devono usare il metodo `Context.StartForegroundService` per avviare il servizio, mentre le app in esecuzione su dispositivi con una versione precedente di Android devono usare `Context.StartService`

Questo C# metodo di estensione è un esempio di come avviare un servizio in primo piano. In Android 8,0 e versioni successive utilizzerà il metodo `StartForegroundService`. in caso contrario, verrà usato il metodo di `StartService` precedente.

```csharp
public static void StartForegroundServiceCompat<T>(this Context context, Bundle args = null) where T : Service
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

## <a name="registering-as-a-foreground-service"></a>Registrazione come servizio in primo piano

Dopo l'avvio di un servizio in primo piano, è necessario registrarsi con Android richiamando il [`StartForeground`](xref:Android.App.Service.StartForeground*). Se il servizio viene avviato con il metodo `Service.StartForegroundService` ma non registra se stesso, Android arresterà il servizio e contrassegnerà l'app come non reattiva.

`StartForeground` accetta due parametri, entrambi obbligatori:

- Valore integer univoco all'interno dell'applicazione per identificare il servizio.
- Un `Notification` oggetto che verrà visualizzato da Android nella barra di stato per tutto il tempo in cui il servizio è in esecuzione.

Android visualizzerà la notifica sulla barra di stato per tutto il tempo in cui il servizio è in esecuzione. La notifica, come minimo, fornirà un segnale visivo all'utente che il servizio è in esecuzione. Idealmente, la notifica deve fornire all'utente un collegamento all'applicazione o forse alcuni pulsanti di azione per controllare l'applicazione. Un esempio è un lettore musicale &ndash; la notifica visualizzata potrebbe avere pulsanti per sospendere/riprodurre la musica, per tornare al brano precedente o per passare al brano successivo. 

Questo frammento di codice è un esempio di registrazione di un servizio come servizio in primo piano:   

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

La notifica precedente visualizzerà una notifica della barra di stato simile alla seguente:

![Immagine che mostra la notifica sulla barra di stato](foreground-services-images/foreground-services-01.png "Immagine che mostra la notifica sulla barra di stato")

Questa schermata mostra la notifica espansa nella barra delle notifiche con due azioni che consentono all'utente di controllare il servizio:

![Immagine che mostra la notifica espansa](foreground-services-images/foreground-services-02.png "Immagine che mostra la notifica espansa.")

Altre informazioni sulle notifiche sono disponibili nella sezione [notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md) della Guida alle [notifiche di Android](~/android/app-fundamentals/notifications/index.md) .

## <a name="unregistering-as-a-foreground-service"></a>Annullamento della registrazione come servizio in primo piano

Un servizio può deelencare se stesso come servizio in primo piano chiamando il metodo `StopForeground`. `StopForeground` non arresterà il servizio, ma rimuoverà l'icona di notifica e segnalerà a Android che questo servizio può essere arrestato se necessario.

La notifica della barra di stato visualizzata può essere rimossa anche passando `true` al metodo: 

```csharp
StopForeground(true);
```

Se il servizio viene interrotto con una chiamata a `StopSelf` o `StopService`, la notifica della barra di stato verrà rimossa.

## <a name="related-links"></a>Collegamenti correlati

- [Android. app. Service](xref:Android.App.Service)
- [Android. app. Service. StartForeground](xref:Android.App.Service.StartForeground*)
- [Notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-foregroundservicedemo)
