---
title: Notifiche push in iOS
description: Questo documento descrive come usare le notifiche push in iOS 9 e versioni precedenti. Vengono illustrati i certificati, la registrazione con Apple Push notifiche Gateway Service (APNS) e altro ancora.
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 8ad742607e506df436a5526d31621ac7636ac29b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61087047"
---
# <a name="push-notifications-in-ios"></a>Notifiche push in iOS

> [!IMPORTANT]
> Le informazioni in questa sezione si riferiscono ai dispositivi iOS 9 e precedenti, è stato lasciato in questo campo per supportare versioni precedenti di iOS. Per iOS 10 e versioni successive, vedere la [Guida utente notifica Framework](~/ios/platform/user-notifications/index.md) per il supporto sia locali che notifica remota in un dispositivo iOS.

Le notifiche push devono essere mantenute breve e contengono solo dati sufficienti per notificare all'applicazione per dispositivi mobili che deve contattare l'applicazione server per un aggiornamento. Ad esempio, all'arrivo di nuovo messaggio di posta elettronica, l'applicazione server solo notifichi l'applicazione per dispositivi mobili che è arrivata nuovo messaggio di posta elettronica. La notifica non contiene il nuovo indirizzo e-mail stesso. L'applicazione per dispositivi mobili potrebbe recuperare nuovi messaggi di posta elettronica dal server quando era appropriato

Al centro del push di notifiche in iOS è il *Apple Push Notification Gateway Service (APNS)*. Si tratta di un servizio fornito da Apple che ha la responsabilità per le notifiche di routine da un server applicazioni per dispositivi iOS di proprietà.
L'immagine seguente illustra la topologia di notifica push per iOS: ![](remote-notifications-in-ios-images/image4.png "Questa immagine illustra la topologia di notifica push per iOS")

Notifiche remote stessi sono conformi al formato di stringhe in formato JSON e protocolli specificato in [il Payload di notifica](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1) sezione il [locali e Push notifica Programming Guide](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)nel [documentazione per sviluppatori iOS](https://developer.apple.com/devcenter/ios/index.action).

Apple gestisce due ambienti del servizio APN: una *Sandbox* e una *produzione* ambiente. L'ambiente Sandbox è progettato per testare durante la fase di sviluppo e reperibili `gateway.sandbox.push.apple.com` sulla porta TCP 2195. L'ambiente di produzione è progettato per essere usati nelle applicazioni che sono state distribuite ed sono reperibile in `gateway.push.apple.com` sulla porta TCP 2195.

## <a name="requirements"></a>Requisiti

Notifica push necessario rispettare le regole seguenti che dipendono dall'architettura del servizio APN:

-  **256 byte limite messaggi** -la dimensione dell'intero messaggio della notifica non può superare 256 byte.
-  **Nessuna conferma di ricezione** -APNS non fornisce al mittente con qualsiasi notifica che un messaggio al destinatario desiderato. Se il dispositivo non è raggiungibile e vengono inviate più notifiche sequenziale, tutte le notifiche, ad eccezione dell'ultima andranno perse. Solo la più recente notifica verrà recapitata al dispositivo.
-  **Ogni applicazione richiede un certificato protetto** -comunicazione con il servizio APN deve essere eseguita tramite SSL.


## <a name="creating-and-using-certificates"></a>Creazione e utilizzo di certificati

Ogni ambiente menzionati nella sezione precedente richiedono il proprio certificato. Questa sezione illustra come creare un certificato, associarlo a un profilo di provisioning e quindi ottenere un certificato di scambio di informazioni personali per l'uso con PushSharp.

1.  Per creare un certificati, andare a iOS portale di Provisioning nel sito Web di Apple, come illustrato nello screenshot seguente (si noti che la voce di menu a sinistra gli ID App):

    [![](remote-notifications-in-ios-images/image5new.png "Il portale di Provisioning nel sito Web di Apple iOS")](remote-notifications-in-ios-images/image5new.png#lightbox)

2.  Successivamente, passare alla sezione gli ID App e creare un nuovo ID app, come illustrato nello screenshot seguente:

    [![](remote-notifications-in-ios-images/image6new.png "Passare alla sezione degli ID App e creare un nuovo ID app")](remote-notifications-in-ios-images/image6new.png#lightbox)

3.  Quando fa clic sui **+** pulsante, sarà in grado di immettere la descrizione e un identificatore del Bundle per l'ID dell'app, come illustrato nel prossimo screenshot:

    [![](remote-notifications-in-ios-images/image7new.png "Immettere la descrizione e un identificatore del Bundle per l'ID app")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. Assicurarsi di selezionare **Explicit App ID** e che l'identificatore del Bundle non termina con un `*` . Verrà creato un identificatore valido per più applicazioni e i certificati di notifica push necessario per una singola applicazione.

1. In servizi App, selezionare **notifiche Push**:

    [![](remote-notifications-in-ios-images/image8new.png "Selezionare le notifiche Push")](remote-notifications-in-ios-images/image8new.png#lightbox)

2. E premere **Submit** per confermare la registrazione del nuovo ID App:

    [![](remote-notifications-in-ios-images/image9new.png "Confermare la registrazione del nuovo ID App")](remote-notifications-in-ios-images/image9new.png#lightbox)

3.  Successivamente, è necessario creare il certificato per l'ID dell'app. Nel riquadro di spostamento a sinistra, passare a **certificati > tutti** e selezionare il `+` pulsante, come illustrato nello screenshot seguente:

    [![](remote-notifications-in-ios-images/image10new.png "Creare il certificato per l'ID app")](remote-notifications-in-ios-images/image8.png#lightbox)

4.  Selezionare se si desidera usare un certificato di sviluppo o produzione:

    [![](remote-notifications-in-ios-images/image11new.png "Selezionare un certificato di sviluppo o produzione")](remote-notifications-in-ios-images/image11new.png#lightbox)

5. E quindi selezionare il nuovo ID App appena creato:

    [![](remote-notifications-in-ios-images/image12new.png "Selezionare il nuovo ID App appena creato")](remote-notifications-in-ios-images/image12new.png#lightbox)

6.  Verranno visualizzate le istruzioni che illustra in dettaglio il processo di creazione di un *richiesta di certificato di firma* usando la **accesso portachiavi** dell'applicazione in Mac.

7.  Ora che il certificato è stato creato, deve essere utilizzata come parte del processo di compilazione per firmare l'applicazione in modo che possono registrare con il servizio APN. Ciò richiede la creazione e installazione di un profilo di provisioning che utilizza il certificato.

8.  Per creare un profilo di provisioning di sviluppo, passare al **profili di Provisioning** sezione e seguire i passaggi per crearla, usando l'App Id appena create.

9.  Dopo aver creato il profilo di provisioning, apriamo **Xcode Organizer** e aggiornarlo. Se il profilo di provisioning è stato creato non viene visualizzata, potrebbe essere necessario scaricare il profilo dal portale di Provisioning iOS e importarlo manualmente. Lo screenshot seguente mostra un esempio della libreria con il profilo di provisioning aggiunto:  
    [![](remote-notifications-in-ios-images/image13new.png "Questo screenshot Mostra un esempio della libreria con il profilo di provisioning aggiunto")](remote-notifications-in-ios-images/image13new.png#lightbox)

10.  A questo punto è necessario configurare il progetto xamarin. IOS per usare questo profilo di provisioning appena creato. Questa operazione viene eseguita dal **opzioni progetto** finestra di dialogo, sotto **firma del Bundle iOS** scheda, come illustrato nello screenshot seguente:  
    [![](remote-notifications-in-ios-images/image11.png "Configurare il progetto xamarin. IOS per usare questo profilo di provisioning appena creato")](remote-notifications-in-ios-images/image11.png#lightbox)

A questo punto l'applicazione è configurata per l'uso delle notifiche push. Tuttavia, esistono ancora pochi altri passaggi richiesti con il certificato. Questo certificato è in formato DER che non è compatibile con PushSharp, che richiede un certificato Personal Information Exchange (PKCS12). Per convertire il certificato in modo che sia utilizzabile da PushSharp, seguire questi passaggi finali:

1.  **Scaricare il file di certificato** -account di accesso al portale di Provisioning iOS scegliere la scheda certificati, selezionare il certificato associato di profilo di provisioning corretto e quindi scegliere **scaricare** .
1.  **Aprire l'accesso Keychain** -si tratta dell'applicazione è un'interfaccia utente grafica per il sistema di gestione delle password in OS X.
1.  **Importare il certificato** : se il certificato non è già installato, **File... Importare gli elementi** dal menu di accesso portachiavi. Passare al certificato esportato in precedenza e selezionarlo.
1.  **Esportare il certificato** - espandere il certificato in modo che la chiave privata associata è visibile, fare doppio clic sulla chiave e ha scelto di esportazione. Verrà richiesto un nome di file e una password per il file esportato.

A questo punto è stata completata con i certificati. Abbiamo creato un certificato che verrà usato per firmare le applicazioni iOS e convertire tale certificato in un formato che può essere utilizzato con PushSharp in un'applicazione server. Successivo verrà ora esaminato l'interagiscono di applicazioni iOS con il servizio APN.

## <a name="registering-with-apns"></a>La registrazione con il servizio APN

Prima di iOS dell'applicazione può ricevere notifiche remote che deve eseguire la registrazione con il servizio APN. APNS genererà un token del dispositivo univoco e che restituiscono all'applicazione iOS. L'applicazione iOS verrà quindi richiedere il token del dispositivo e quindi registrarsi con il server applicazioni. Dopo tutte in questo caso, la registrazione è completa e il server applicazioni può eseguire il push di notifiche al dispositivo mobile.

In teoria, il token del dispositivo può cambiare ogni volta che un'applicazione iOS si registra con il servizio APN, ma in pratica questo non accade che spesso. Come ottimizzazione un'applicazione può memorizzare nella cache il token del dispositivo più recente e aggiornare solo il server applicazioni quando viene modificato. Il diagramma seguente illustra il processo di registrazione e ottenere un token del dispositivo:

 ![](remote-notifications-in-ios-images/image12.png "Questo diagramma illustra il processo di registrazione e ottenere un token del dispositivo")

Registrazione con il servizio APN viene gestita nel `FinishedLaunching` metodo della classe delegata dell'applicazione chiamando `RegisterForRemoteNotificationTypes` sull'oggetto corrente `UIApplication` oggetto. Quando si registra un'applicazione iOS con il servizio APN, è necessario specificare anche quali tipi di notifiche remote desidera ricevere. Questi tipi di notifica remota vengono dichiarati nell'enumerazione `UIRemoteNotificationType`. Il frammento di codice seguente è riportato un esempio di come può registrare un'applicazione iOS per la ricezione di notifiche badge e avviso remote:

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
    var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

    UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications ();
} else {
    UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
    UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
}
```

La richiesta di registrazione APNS avviene in background - quando viene ricevuta la risposta, iOS chiamerà il metodo `RegisteredForRemoteNotifications` nella `AppDelegate` classe e passare il token di dispositivo registrato. Il token sarà contenuto in un `NSData` oggetto. Il frammento di codice seguente viene illustrato come recuperare il token del dispositivo che APNS fornito:

```csharp
public override void RegisteredForRemoteNotifications (
UIApplication application, NSData deviceToken)
{
    // Get current device token
    var DeviceToken = deviceToken.Description;
    if (!string.IsNullOrWhiteSpace(DeviceToken)) {
        DeviceToken = DeviceToken.Trim('<').Trim('>');
    }

    // Get previous device token
    var oldDeviceToken = NSUserDefaults.StandardUserDefaults.StringForKey("PushDeviceToken");

    // Has the token changed?
    if (string.IsNullOrEmpty(oldDeviceToken) || !oldDeviceToken.Equals(DeviceToken))
    {
        //TODO: Put your own logic here to notify your server that the device token has changed/been created!
    }

    // Save new device token
    NSUserDefaults.StandardUserDefaults.SetString(DeviceToken, "PushDeviceToken");
}
```

Se la registrazione non riesce per qualche motivo (ad esempio il dispositivo non è connesso a Internet), iOS chiamerà `FailedToRegisterForRemoteNotifications` sull'applicazione classe delegata. Il frammento di codice seguente viene illustrato come visualizzare un avviso all'utente in cui vengono informati che la registrazione non è riuscita:

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>Attività di manutenzione Token dispositivo

Token del dispositivo verranno scadere o essere modificata nel corso del tempo. Per questo motivo, le applicazioni server saranno necessario eseguire alcuni casa di pulizia e ripulire i token scaduti o modificati. Quando un'applicazione invia come notifica push a un dispositivo con un token scaduto, APNS verrà registrare e salvare tale token scaduti. I server possono quindi eseguire una query APNS per scoprire quali i token scaduti.

APNS utilizzato per fornire una *commenti e suggerimenti servizio* -un endpoint HTTPS che esegue l'autenticazione tramite il certificato che è stato creato per inviare push delle notifiche e invia nuovamente i dati sul quale i token scaduti. Questo è stato deprecato da Apple e rimosso.

Viene invece un nuovo codice di stato HTTP per il caso che in precedenza è stato segnalato dal servizio di commenti e suggerimenti:

> 410 - il token del dispositivo non è più attivo per l'argomento.

Inoltre, un nuovo `timestamp` sarà chiave dei dati JSON nel corpo della risposta:

> Se il valore nel: intestazione di stato è 410, il valore di questa chiave è l'ultima volta in corrispondenza del quale APNs confermato che il token del dispositivo non è valido per l'argomento.
>
> Arrestare il push di notifiche fino a quando il dispositivo esegue la registrazione di un token con un timestamp successivo con il provider.

## <a name="summary"></a>Riepilogo

In questa sezione descrive i concetti di base che racchiudono le notifiche push in iOS. Illustrato il ruolo di Apple Push Notification Gateway Service (APNS). Quindi illustrato la creazione e uso dei certificati di sicurezza che sono essenziali per il servizio APN. Infine questo documento terminata con una discussione su come usare i server applicazioni il *Services commenti e suggerimenti* arrestare rilevamento scaduto token del dispositivo.


## <a name="related-links"></a>Collegamenti correlati

- [Notifiche - che illustra le notifiche locali e remote (esempio)](https://developer.xamarin.com/samples/monotouch/Notifications/)
- [Locale e notifiche Push per gli sviluppatori](https://developer.apple.com/notifications/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UIRemoteNotificationType](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIRemoteNotificationType)
