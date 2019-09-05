---
title: Notifiche push in iOS
description: Questo documento descrive come usare le notifiche push in iOS 9 e versioni precedenti. Vengono illustrati i certificati, la registrazione con il servizio gateway di notifiche push di Apple (APNS) e altro ancora.
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: ee2c54f2369682571520370e09d6d39c2cd86fa5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290583"
---
# <a name="push-notifications-in-ios"></a>Notifiche push in iOS

> [!IMPORTANT]
> Le informazioni contenute in questa sezione sono relative a iOS 9 e versioni precedenti, ma sono state lasciate qui per supportare le versioni precedenti di iOS. Per iOS 10 e versioni successive, vedere la [Guida per il Framework di notifica utente](~/ios/platform/user-notifications/index.md) per il supporto di notifiche locali e remote in un dispositivo iOS.

Le notifiche push devono essere mantenute brevi e contenere solo dati sufficienti per notificare all'applicazione per dispositivi mobili che deve contattare l'applicazione server per un aggiornamento. Ad esempio, quando arriva un nuovo messaggio di posta elettronica, l'applicazione server invia una notifica all'applicazione per dispositivi mobili solo dopo l'arrivo di un nuovo messaggio. La notifica non conterrà il nuovo messaggio di posta elettronica. L'applicazione per dispositivi mobili recupererà quindi i nuovi messaggi di posta elettronica dal server quando era appropriato

Al centro delle notifiche push in iOS è il *servizio gateway di notifica push di Apple (APNS)* . Si tratta di un servizio fornito da Apple che è responsabile del routing delle notifiche da un server applicazioni a dispositivi iOS.
Nell'immagine seguente viene illustrata la topologia delle notifiche push per iOS: ![](remote-notifications-in-ios-images/image4.png "Questa immagine illustra la topologia delle notifiche push per iOS")

Le notifiche remote sono stringhe in formato JSON conformi al formato e ai protocolli specificati nella sezione [payload](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1) delle notifiche della [Guida alla programmazione delle notifiche push e locali](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) nella [documentazione per sviluppatori iOS ](https://developer.apple.com/devcenter/ios/index.action).

Apple gestisce due ambienti di APNS: un ambiente *sandbox* e un ambiente di *produzione* . L'ambiente sandbox è destinato ai test durante la fase di sviluppo e si trova in `gateway.sandbox.push.apple.com` sulla porta TCP 2195. L'ambiente di produzione deve essere usato nelle applicazioni che sono state distribuite e si trova `gateway.push.apple.com` in sulla porta TCP 2195.

## <a name="requirements"></a>Requisiti

La notifica push deve osservare le regole seguenti che sono determinate dall'architettura di APNS:

- **limite di messaggi di 256 byte** : le dimensioni dell'intero messaggio della notifica non devono superare 256 byte.
- **Nessuna conferma di ricezione** : APNS non fornisce al mittente alcuna notifica che un messaggio lo ha fatto al destinatario previsto. Se il dispositivo non è raggiungibile e vengono inviate più notifiche sequenziali, verranno perse tutte le notifiche tranne quelle più recenti. Solo la notifica più recente verrà recapitata al dispositivo.
- **Ogni applicazione richiede un certificato sicuro: la** comunicazione con APNs deve essere eseguita tramite SSL.


## <a name="creating-and-using-certificates"></a>Creazione e utilizzo di certificati

Ognuno degli ambienti indicati nella sezione precedente richiede il proprio certificato. Questa sezione illustra come creare un certificato, associarlo a un profilo di provisioning e quindi ottenere un certificato di scambio di informazioni personali da usare con PushSharp.

1. Per creare un certificato, passare al portale di provisioning iOS nel sito Web di Apple, come illustrato nella schermata seguente (si noti la voce di menu ID app a sinistra):

    [![](remote-notifications-in-ios-images/image5new.png "Portale di provisioning iOS nel sito Web Apple")](remote-notifications-in-ios-images/image5new.png#lightbox)

2. Passare quindi alla sezione ID app e creare un nuovo ID app, come illustrato nello screenshot seguente:

    [![](remote-notifications-in-ios-images/image6new.png "Passare alla sezione ID app e creare un nuovo ID app")](remote-notifications-in-ios-images/image6new.png#lightbox)

3. Quando si fa clic sul **+** pulsante, sarà possibile immettere la descrizione e un identificatore del bundle per l'ID app, come illustrato nello screenshot seguente:

    [![](remote-notifications-in-ios-images/image7new.png "Immettere la descrizione e un identificatore del bundle per l'ID app")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. Assicurarsi di selezionare l' **ID app esplicito** e che l'identificatore del bundle non termini `*` con un. Verrà creato un identificatore adatto per più applicazioni e i certificati per le notifiche push devono essere per una singola applicazione.

5. In servizi app selezionare **notifiche push**:

    [![](remote-notifications-in-ios-images/image8new.png "Selezione notifiche push")](remote-notifications-in-ios-images/image8new.png#lightbox)

6. E premere **Invia** per confermare la registrazione del nuovo ID app:

    [![](remote-notifications-in-ios-images/image9new.png "Confermare la registrazione del nuovo ID app")](remote-notifications-in-ios-images/image9new.png#lightbox)

7. Successivamente, è necessario creare il certificato per l'ID app. Nella finestra di spostamento a sinistra passare a **certificati > tutti** e selezionare il `+` pulsante, come illustrato nello screenshot seguente:

    [![](remote-notifications-in-ios-images/image10new.png "Creare il certificato per l'ID app")](remote-notifications-in-ios-images/image8.png#lightbox)

8. Specificare se si desidera utilizzare un certificato di sviluppo o di produzione:

    [![](remote-notifications-in-ios-images/image11new.png "Selezionare un certificato di sviluppo o di produzione")](remote-notifications-in-ios-images/image11new.png#lightbox)

9. Quindi selezionare il nuovo ID app appena creato:

    [![](remote-notifications-in-ios-images/image12new.png "Selezionare il nuovo ID app appena creato")](remote-notifications-in-ios-images/image12new.png#lightbox)

10. Verranno visualizzate le istruzioni che illustrano il processo di creazione di una *richiesta di firma del certificato* tramite l'applicazione di **Accesso Keychain** nel Mac.

11. Ora che il certificato è stato creato, è necessario usarlo come parte del processo di compilazione per firmare l'applicazione in modo che possa essere registrata con APNs. A tale scopo, è necessario creare e installare un profilo di provisioning che usa il certificato.

12. Per creare un profilo di provisioning di sviluppo, passare alla sezione **profili di provisioning** e seguire i passaggi per crearlo, usando l'ID app appena creato.

13. Dopo aver creato il profilo di provisioning, aprire **Xcode Organizer** e aggiornarlo. Se il profilo di provisioning creato non viene visualizzato, potrebbe essere necessario scaricare il profilo dal portale di provisioning iOS e importarlo manualmente. Lo screenshot seguente mostra un esempio della libreria con il profilo di provisioning aggiunto:  
    [![](remote-notifications-in-ios-images/image13new.png "Questo screenshot mostra un esempio della libreria con il profilo di provisioning aggiunto")](remote-notifications-in-ios-images/image13new.png#lightbox)

14. A questo punto è necessario configurare il progetto Novell. iOS in modo da usare il profilo di provisioning appena creato. Questa operazione viene eseguita dalla finestra di dialogo **Opzioni progetto** , nella scheda **firma bundle iOS** , come illustrato nello screenshot seguente:  
    [![](remote-notifications-in-ios-images/image11.png "Configurare il progetto Novell. iOS per usare il profilo di provisioning appena creato")](remote-notifications-in-ios-images/image11.png#lightbox)

A questo punto l'applicazione è configurata per l'uso con le notifiche push. Tuttavia, esistono ancora alcuni passaggi necessari con il certificato. Questo certificato è in formato DER che non è compatibile con PushSharp, che richiede un certificato PKCS12 (Personal Information Exchange). Per convertire il certificato in modo che sia utilizzabile da PushSharp, eseguire questi passaggi finali:

1. **Scaricare il file di certificato** : accedere al portale di provisioning iOS, scegliere la scheda certificati, selezionare il certificato associato al profilo di provisioning corretto e scegliere **download** .
1. **Apri Accesso Keychain** : questa applicazione è un'interfaccia GUI per il sistema di gestione delle password in OS X.
1. **Importa il certificato** : se il certificato non è già installato, **file... Importa gli elementi** dal menu di accesso portachiavi. Passare al certificato esportato in precedenza e selezionarlo.
1. **Esportare il certificato** : espandere il certificato in modo che la chiave privata associata sia visibile, fare clic con il pulsante destro del mouse sulla chiave e scegliere Esporta. Verrà richiesto di specificare un nome file e una password per il file esportato.

A questo punto, l'operazione è stata eseguita con i certificati. È stato creato un certificato che verrà usato per firmare le applicazioni iOS e convertirlo in un formato che può essere usato con PushSharp in un'applicazione server. Verrà ora esaminato il modo in cui le applicazioni iOS interagiscono con APNS.

## <a name="registering-with-apns"></a>Registrazione con APNS

Prima che un'applicazione iOS possa ricevere una notifica remota, è necessario registrarla con APNS. APNS genererà un token univoco del dispositivo e lo restituirà all'applicazione iOS. L'applicazione iOS prenderà quindi il token del dispositivo e si registrerà nel server applicazioni. Una volta eseguita questa operazione, la registrazione è completa e il server applicazioni può inviare notifiche push al dispositivo mobile.

In teoria, il token del dispositivo può cambiare ogni volta che un'applicazione iOS si registra con APNS, ma in pratica ciò non accade spesso. Come ottimizzazione, un'applicazione può memorizzare nella cache il token del dispositivo più recente e aggiornare il server applicazioni solo quando viene modificato. Il diagramma seguente illustra il processo di registrazione e il recupero di un token del dispositivo:

 ![](remote-notifications-in-ios-images/image12.png "Questo diagramma illustra il processo di registrazione e il recupero di un token del dispositivo")

La registrazione con APNs viene gestita nel `FinishedLaunching` metodo della classe delegata dell'applicazione `RegisterForRemoteNotificationTypes` chiamando sull'oggetto `UIApplication` corrente. Quando un'applicazione iOS viene registrata con APNS, è necessario specificare anche i tipi di notifiche remote che si desidera ricevere. Questi tipi di notifica remota sono dichiarati `UIRemoteNotificationType`nell'enumerazione. Il frammento di codice seguente è un esempio di come un'applicazione iOS può registrarsi per ricevere notifiche di avviso e notifiche remote:

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

La richiesta di registrazione APNs viene eseguita in background. quando viene ricevuta la risposta, iOS chiamerà il metodo `RegisteredForRemoteNotifications` `AppDelegate` nella classe e passerà il token del dispositivo registrato. Il token sarà contenuto in un `NSData` oggetto. Il frammento di codice seguente mostra come recuperare il token del dispositivo fornito da APNS:

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

Se la registrazione non riesce per qualche motivo, ad esempio se il dispositivo non è connesso a Internet, iOS `FailedToRegisterForRemoteNotifications` chiamerà sulla classe delegata dell'applicazione. Il frammento di codice seguente mostra come visualizzare un avviso per informare l'utente che la registrazione non è riuscita:

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>Gestione dei token del dispositivo

Il token del dispositivo scadrà o cambierà nel tempo. Per questo motivo, le applicazioni server dovranno eseguire una pulizia ed eliminare i token scaduti o modificati. Quando un'applicazione viene inviata come notifica push a un dispositivo con un token scaduto, APNS registrerà e salverà il token scaduto. I server possono quindi eseguire una query su APNS per individuare i token scaduti.

APNS usato per fornire un *servizio di feedback* , ovvero un endpoint HTTPS che esegue l'autenticazione tramite il certificato creato per inviare notifiche push e invia dati sui token scaduti. Questa funzionalità è stata deprecata da Apple ed è stata rimossa.

È invece disponibile un nuovo codice di stato HTTP per il caso segnalato in precedenza dal servizio di feedback:

> 410: il token del dispositivo non è più attivo per l'argomento.

Inoltre, una nuova `timestamp` chiave di dati JSON sarà nel corpo della risposta:

> Se il valore nell'intestazione: status è 410, il valore di questa chiave è l'ultima volta in cui APNs ha confermato che il token del dispositivo non è più valido per l'argomento.
>
> Arrestare il push delle notifiche fino a quando il dispositivo non registra un token con un timestamp successivo con il provider.

## <a name="summary"></a>Riepilogo

Questa sezione presenta i concetti chiave che riguardano le notifiche push in iOS. Ha illustrato il ruolo del servizio gateway di notifica push di Apple (APNS). Ha quindi trattato la creazione e l'uso dei certificati di sicurezza essenziali per APNS. Infine, questo documento ha completato una discussione sul modo in cui i server applicazioni possono usare i *servizi di feedback* per arrestare il rilevamento dei token di dispositivo scaduti.


## <a name="related-links"></a>Collegamenti correlati

- [Notifiche-dimostrazione di notifiche locali e remote (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/notifications)
- [Notifiche push e locali per gli sviluppatori](https://developer.apple.com/notifications/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UIRemoteNotificationType](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIRemoteNotificationType)
