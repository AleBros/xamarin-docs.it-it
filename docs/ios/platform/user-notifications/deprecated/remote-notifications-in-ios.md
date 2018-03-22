---
title: Notifiche push in iOS
description: "In questa sezione verrà illustrate le notifiche push in iOS. Introduce il servizio Gateway di notifiche di Push di Apple e il ruolo pubblicazione notifiche per le applicazioni iOS. Verrà illustrato come creare i certificati di sicurezza necessario per abilitare le notifiche push e discutere. Infine in questa sezione verrà illustrate alcune delle attività di manutenzione che i server applicazioni devono eseguire per tenere traccia dei dispositivi mobili client."
ms.topic: article
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 3af74fb9d93e22e361f2e3db00961d7955eda689
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2018
---
# <a name="push-notifications-in-ios"></a>Notifiche push in iOS

_In questa sezione verrà illustrate le notifiche push in iOS. Introduce il servizio Gateway di notifiche di Push di Apple e il ruolo pubblicazione notifiche per le applicazioni iOS. Verrà illustrato come creare i certificati di sicurezza necessario per abilitare le notifiche push e discutere. Infine in questa sezione verrà illustrate alcune delle attività di manutenzione che i server applicazioni devono eseguire per tenere traccia dei dispositivi mobili client._

> [!IMPORTANT]
> Le informazioni in questa sezione si riferiscono a iOS 9 e precedenti, è stato lasciato qui per supportare le versioni precedenti di iOS. Per iOS 10 e versioni successive, vedere il [Guida utente notifica Framework](~/ios/platform/user-notifications/index.md) per il supporto sia locali che Remote notifica in un dispositivo iOS.

Notifiche push devono essere mantenute breve e contengono solo dati sufficienti per notificare l'applicazione per dispositivi mobili che è necessario contattare l'applicazione server per un aggiornamento. Ad esempio, quando arriva nuovo indirizzo e-mail, l'applicazione server sarebbe notifica solo l'applicazione per dispositivi mobili è arrivata la nuova posta elettronica. La notifica non contiene il nuovo indirizzo e-mail stesso. L'applicazione per dispositivi mobili potrebbe recuperare i nuovi messaggi di posta elettronica dal server quando era appropriato

Al centro del push è notifiche in iOS il *Apple Push Notification Gateway Service (APNS)*. Si tratta di un servizio fornito da Apple che è responsabile per le notifiche di routine da un server applicazioni per dispositivi iOS.
L'immagine seguente illustra la topologia di notifica push per iOS: ![ ] (remote-notifications-in-ios-images/image4.png "questa immagine illustra la topologia di notifica push per iOS")

Le notifiche remote stesse sono conformi al formato di stringhe di formato JSON e protocolli specificato in [il Payload di notifica](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1) sezione la [locale e Push notifica Guida per programmatori](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)nel [documentazione per sviluppatori iOS](https://developer.apple.com/devcenter/ios/index.action).

Apple gestisce due ambienti del servizio APN: un *Sandbox* e *produzione* ambiente. L'ambiente Sandbox è progettato per il testing durante la fase di sviluppo ed è reperibile in `gateway.sandbox.push.apple.com` sul protocollo TCP porta 2195. L'ambiente di produzione è destinato a essere utilizzato nelle applicazioni che sono state distribuite ed sono reperibile in `gateway.push.apple.com` sul protocollo TCP porta 2195.

## <a name="requirements"></a>Requisiti

Notifica push è necessario rispettare le regole seguenti che dipendono dall'architettura del servizio APN:

-  **256 byte limite messaggi** -la dimensione dell'intero messaggio della notifica non deve superare 256 byte.
-  **Nessuna conferma di ricezione** -APN non fornisce al mittente con qualsiasi notifica che un messaggio viene reso al destinatario prescelto. Se il dispositivo non è raggiungibile e vengono inviate le notifiche sequenziali più, tutte le notifiche, ad eccezione dell'ultima andranno perse. Verrà recapitata solo la notifica più recente al dispositivo.
-  **Ogni applicazione richiede un certificato protetto** -comunicazione con il servizio APN deve essere eseguita tramite SSL.


## <a name="creating-and-using-certificates"></a>Creazione e utilizzo di certificati

Ognuno degli ambienti riportati nella sezione precedente richiede il proprio certificato. In questa sezione illustra le procedure per creare un certificato, associarlo a un profilo di provisioning e quindi ottenere un certificato di scambio di informazioni personali per l'utilizzo con PushSharp.

1.  Per creare un certificati, passare al iOS portale di Provisioning nel sito Web di Apple, come illustrato nella schermata seguente (si noti la voce di menu a sinistra ID App):

    [![](remote-notifications-in-ios-images/image5new.png "Il portale di Provisioning nel sito Web di mele iOS")](remote-notifications-in-ios-images/image5new.png#lightbox)

2.  Successivamente, passare alla sezione ID App e creare un nuovo ID di app, come illustrato nella schermata seguente:

    [![](remote-notifications-in-ios-images/image6new.png "Passare alla sezione ID App e creare un nuovo ID di app")](remote-notifications-in-ios-images/image6new.png#lightbox)

3.  Facendo clic di  **+**  pulsante, sarà possibile immettere la descrizione e un identificatore di raggruppamento per l'ID dell'app, come illustrato nella schermata successiva:

    [![](remote-notifications-in-ios-images/image7new.png "Immettere la descrizione e un identificatore di raggruppamento per l'ID dell'app")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. Assicurarsi di selezionare **ID App esplicito** e che l'identificatore Bundle non termina con un `*` . Verrà creato un identificatore valido per più applicazioni e i certificati di notifica push devono essere una singola applicazione.

1. In servizi di App, selezionare **le notifiche Push**:

    [![](remote-notifications-in-ios-images/image8new.png "Selezionare le notifiche Push")](remote-notifications-in-ios-images/image8new.png#lightbox)

2. Premere **Invia** per confermare la registrazione del nuovo ID di App:

    [![](remote-notifications-in-ios-images/image9new.png "Confermare la registrazione del nuovo ID di App")](remote-notifications-in-ios-images/image9new.png#lightbox)

3.  Successivamente, è necessario creare il certificato per l'ID di app. Nel riquadro di spostamento a sinistra, passare a **certificati > tutti** e selezionare il `+` pulsante, come illustrato nella schermata seguente:

    [![](remote-notifications-in-ios-images/image10new.png "Creare il certificato per l'ID dell'app")](remote-notifications-in-ios-images/image8.png#lightbox)

4.  Selezionare se si desidera utilizzare un certificato di sviluppo o di produzione:

    [![](remote-notifications-in-ios-images/image11new.png "Selezionare un certificato di sviluppo o di produzione")](remote-notifications-in-ios-images/image11new.png#lightbox)

5. E quindi selezionare il nuovo ID di App che abbiamo appena creato:

    [![](remote-notifications-in-ios-images/image12new.png "Selezionare il nuovo ID applicazione appena creato")](remote-notifications-in-ios-images/image12new.png#lightbox)

6.  Istruzioni che saranno necessario eseguire il processo di creazione verrà visualizzato un *richiesta di firma certificato* utilizzando il **accesso portachiavi** applicazione sul Mac.

7.  Ora che il certificato è stato creato, e deve essere utilizzato come parte del processo di compilazione per firmare l'applicazione in modo che è possibile registrare con il servizio APN. È necessario creare e installare un profilo di provisioning che utilizza il certificato.

8.  Per creare un profilo di provisioning di sviluppo, individuare il **i profili di Provisioning** sezione e seguire i passaggi per crearla, utilizzando l'App Id appena creato.

9.  Dopo aver creato il profilo di provisioning, aprire **Xcode libreria** e aggiornarlo. Se il profilo di provisioning è stato creato non viene visualizzata, potrebbe essere necessario scaricare il profilo dal portale di Provisioning iOS e importarlo manualmente. La schermata seguente mostra un esempio della libreria con il profilo di provisioning aggiunto:

    [![](remote-notifications-in-ios-images/image13new.png "Questa schermata mostra un esempio della libreria con il profilo di provisioning aggiunto")](remote-notifications-in-ios-images/image13new.png#lightbox)

10.  A questo punto è necessario configurare il progetto xamarin. IOS per usare questo profilo di provisioning appena creato. Questa operazione viene eseguita da **opzioni progetto** finestra di dialogo, in **firma Bundle iOS** scheda, come illustrato nella schermata seguente:

    [![](remote-notifications-in-ios-images/image11.png "Configurare il progetto xamarin. IOS per usare questo profilo di provisioning appena creato")](remote-notifications-in-ios-images/image11.png#lightbox)



A questo punto l'applicazione è configurata per utilizzare le notifiche push. Tuttavia, esistono ancora alcuni passaggi aggiuntivi necessari con il certificato. Questo certificato è in formato DER che non è compatibile con PushSharp, che richiede un certificato di scambio di informazioni personali (o PKCS12). Per convertire il certificato in modo che sia utilizzabile da PushSharp, seguire la procedura finale:

1.  **Scaricare il file del certificato** -account di accesso al portale di Provisioning iOS scegliere la scheda certificati, selezionare il certificato associato con il profilo di provisioning corretto e scegliere **scaricare** .
1.  **Aprire Accesso Portachiavi** -si tratta di applicazione è un'interfaccia utente grafica per il sistema di gestione delle password in OS X.
1.  **Importare il certificato** : se il certificato non è già installata, **File... Importare elementi** dal menu di accesso portachiavi. Passare al certificato esportato in precedenza e selezionarlo.
1.  **Esportare il certificato** : espande il certificato, quindi la chiave privata associata è visibile, fare doppio clic sulla chiave e ha scelto di esportazione. Verrà richiesto di un nome di file e una password per il file esportato.

A questo punto è terminati con i certificati. Abbiamo creato un certificato che verrà utilizzato per firmare applicazioni iOS e convertire tale certificato in un formato che può essere utilizzato con PushSharp in un'applicazione server. Avanti si esaminerà l'interagiscono di applicazioni iOS con il servizio APN.

## <a name="registering-with-apns"></a>Registrazione con il servizio APN

Prima di iOS dell'applicazione può ricevere notifica remota che deve eseguire la registrazione con il servizio APN. APN genererà un token univoco del dispositivo e che restituiscono all'applicazione iOS. L'applicazione iOS verrà quindi richiedere il token del dispositivo e quindi registrarsi con il server applicazioni. Una volta tutti in questo caso, la registrazione è stata completata e il server applicazioni può eseguire il push delle notifiche al dispositivo mobile.

In teoria, il token del dispositivo potrebbe cambiare ogni volta che un'applicazione iOS registra se stesso con il servizio APN, ma in pratica questo non accade che spesso. Per motivi di ottimizzazione un'applicazione può memorizzare nella cache il token del dispositivo più recente e aggiornare solo il server applicazioni quando viene modificato. Il diagramma seguente illustra il processo di registrazione e ottenere un token del dispositivo:

 ![](remote-notifications-in-ios-images/image12.png "Questo diagramma illustra il processo di registrazione e ottenere un token del dispositivo")

Registrazione con il servizio APN è gestita nel `FinishedLaunching` metodo della classe del delegato applicazioni chiamando `RegisterForRemoteNotificationTypes` su corrente `UIApplication` oggetto. Quando un'applicazione iOS registrati con il servizio APN, è necessario specificare i tipi di notifiche remote desidera ricevere. Questi tipi di notifica remota vengono dichiarati nell'enumerazione `UIRemoteNotificationType`. Frammento di codice seguente è riportato un esempio di come registrare un'applicazione iOS per la ricezione di notifiche di avviso e badge remote:

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

La richiesta di registrazione del servizio APN avviene in background - quando viene ricevuta la risposta, iOS chiamerà il metodo `RegisteredForRemoteNotifications` nel `AppDelegate` classe e passare il token di dispositivo registrato. Il token sarà contenuto un `NSData` oggetto. Frammento di codice seguente viene illustrato come recuperare il token del dispositivo che APN fornito:

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

Se la registrazione non riesce per qualche motivo (ad esempio il dispositivo non è connesso a Internet), iOS chiamerà `FailedToRegisterForRemoteNotifications` sull'applicazione classe delegata. Frammento di codice riportato di seguito viene illustrato come visualizzare un avviso informa che la registrazione non riuscita:

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>Manutenzione di Token di dispositivo

Token di dispositivo verranno scadere o cambiare nel tempo. Per questo motivo, le applicazioni server saranno necessario eseguire alcune parti di pulizia ed eliminare questi token scaduti o modificati. Quando un'applicazione invia come notifica push a un dispositivo che dispone di un token scaduto, APN verrà registrare e salvare tale token scaduto. I server possono quindi eseguire una query APNS per scoprire quali token scaduti.

APNS utilizzato per fornire un *servizio Feedback* -un endpoint HTTPS che esegue l'autenticazione tramite il certificato è stato creato per inviare push delle notifiche e invia nuovamente i dati sui quali token scaduti. Questo è stato deprecato da Apple e rimosso.

Viene invece un nuovo codice di stato HTTP per il caso che in precedenza è stato segnalato dal servizio di commenti e suggerimenti:

> 410 - il token del dispositivo non è più attivo per l'argomento.

Inoltre, un nuovo `timestamp` sarà chiave dei dati JSON nel corpo della risposta:

> Se il valore di: intestazione di stato è 410, il valore di questa chiave è l'ultima volta in cui APN confermato che il token del dispositivo non è valido per l'argomento.
>
> Arrestare l'invio di notifiche fino a quando il dispositivo registra un token con un timestamp successivo con il provider.

## <a name="summary"></a>Riepilogo

In questa sezione introduce i concetti chiave notifiche push in iOS. Descritto il ruolo di Apple Push Notification Gateway Service (APNS). Quindi illustrate la creazione e l'utilizzo dei certificati di sicurezza che sono essenziali per servizio APN. Infine questo documento terminata con una discussione su come server applicazioni è possibile utilizzare il *servizi di commenti e suggerimenti* per l'arresto del rilevamento modifiche scaduto token di dispositivo.


## <a name="related-links"></a>Collegamenti correlati

- [Notifiche - dimostrazione notifiche locali e remote (esempio)](https://developer.xamarin.com/samples/monotouch/Notifications/)
- [Locale e inviare notifiche per gli sviluppatori](https://developer.apple.com/notifications/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UIRemoteNotificationType](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIRemoteNotificationType)
