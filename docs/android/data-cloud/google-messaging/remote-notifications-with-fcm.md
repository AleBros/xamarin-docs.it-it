---
title: Notifiche remote con Firebase Cloud Messaging
description: Questa procedura dettagliata fornisce una spiegazione dettagliata della procedura usare Firebase Cloud Messaging per implementare le notifiche remote (detta anche le notifiche push) in un'applicazione xamarin. Android. Viene illustrato come implementare le varie classi che sono necessari per le comunicazioni con Firebase Cloud Messaging (FCM), vengono forniti esempi su come configurare il manifesto Android per l'accesso a FCM e illustra l'uso di Firebase di messaggistica a valle Console.
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: a50a2014e28becacb2c9f4965b7f3377be57ab16
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830315"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>Notifiche remote con Firebase Cloud Messaging

_Questa procedura dettagliata fornisce una spiegazione dettagliata della procedura usare Firebase Cloud Messaging per implementare le notifiche remote (detta anche le notifiche push) in un'applicazione xamarin. Android. Viene illustrato come implementare le varie classi che sono necessari per le comunicazioni con Firebase Cloud Messaging (FCM), vengono forniti esempi su come configurare il manifesto Android per l'accesso a FCM e illustra l'uso di Firebase di messaggistica a valle Console._

## <a name="fcm-notifications-overview"></a>Panoramica delle notifiche FCM

In questa procedura dettagliata, un'app di base chiamato **FCMClient** verrà creato per illustrare i passaggi fondamentali della messaggistica di FCM. **FCMClient** verifica la presenza di Google Play Services, riceve i token di registrazione da FCM, consente di visualizzare le notifiche remote inviati dalla Console Firebase e sottoscrive i messaggi dell'argomento:

[![Screenshot di esempio dell'app](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

Verranno esaminati gli argomenti seguenti:

1.  Notifiche in background

2.  Messaggi dell'argomento

3.  Notifiche di primo piano

Durante questa procedura dettagliata si aggiungerà in modo incrementale la funzionalità per **FCMClient** ed eseguirlo in un dispositivo o emulatore per comprendere come essa interagisce con FCM. Si utilizzerà la registrazione per le transazioni di app in tempo reale con i server FCM di controllo del mirroring, e sarà possibile osservare come le notifiche vengono generate dai messaggi FCM immesse nell'interfaccia utente grafica notifiche Console di Firebase.

## <a name="requirements"></a>Requisiti


Sarà utile acquisire familiarità con le [diversi tipi di messaggi](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) che può essere inviata da Firebase Cloud Messaging. Si è determineranno il payload del messaggio come un'app client riceverà ed elaborerà il messaggio.

Prima di continuare con questa procedura dettagliata, è necessario acquisire le credenziali necessarie per l'utilizzo FCM server di Google; Questo processo è illustrato in [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#setup_fcm).
In particolare, è necessario scaricare il **google-Services. JSON** file da utilizzare con il codice di esempio presentato in questa procedura dettagliata. Se si have non ancora creato un progetto nella Console di Firebase (o se non hai ancora scaricato i **google-Services. JSON** file), vedere [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

Per eseguire l'app di esempio, è necessario un dispositivo Android test o un emulatore compatibile con Firebase. Firebase Cloud Messaging supporta i client che eseguono Android 4.0 o versione successiva e questi dispositivi devono anche avere installato l'app Google Play Store (Google Play Services 9.2.1 o versione successiva è richiesto). Se non si dispone ancora di Google Play Store installi l'app nel dispositivo, visitare il [Google Play](https://support.google.com/googleplay) sito web per scaricarlo e installarlo. In alternativa, è possibile usare l'emulatore di Android SDK con Google Play Services installato anziché un dispositivo di test (non è necessario installare la Store di Google Play se si usa l'emulatore di Android SDK).

## <a name="start-an-app-project"></a>Avviare un progetto di app

Per iniziare, creare un nuovo progetto xamarin. Android vuoto denominato **FCMClient**. Se non ha familiarità con la creazione di progetti xamarin. Android, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).
Dopo aver creata la nuova app, il passaggio successivo è impostare il nome del pacchetto e installare alcuni pacchetti NuGet da usare per la comunicazione con FCM.

### <a name="set-the-package-name"></a>Impostare il nome del pacchetto

Nelle [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md), si specifica un nome di pacchetto per l'app abilitata per FCM. Questo nome pacchetto funge anche il [ *ID applicazione* ](./firebase-cloud-messaging.md#fcm-in-action-app-id) che è associato il [chiave API](firebase-cloud-messaging.md#fcm-in-action-api-key). Configurare l'app per usare questo nome pacchetto:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Aprire le proprietà per il **FCMClient** progetto.

2.  Nel **manifesto Android** pagina, impostare il nome del pacchetto.

Nell'esempio seguente, il nome del pacchetto è impostato su `com.xamarin.fcmexample`:

[![Impostare il nome del pacchetto](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

Quando si aggiornano il **manifesto Android**, selezionare anche per assicurarsi che il `Internet` è abilitata l'autorizzazione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1.  Aprire le proprietà per il **FCMClient** progetto.

2.  Nel **applicazione Android** pagina, impostare il nome del pacchetto.

Nell'esempio seguente, il nome del pacchetto è impostato su `com.xamarin.fcmexample`:

[![Impostare il nome del pacchetto](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

Mentre si sta aggiornando il **manifesto Android**, selezionare anche per assicurarsi che il `INTERNET` è abilitata l'autorizzazione (sotto **autorizzazioni necessarie**).

-----

> [!IMPORTANT]
> L'app client in grado di ricevere un token di registrazione da FCM, se il nome del pacchetto non dispone *esattamente* corrisponde al nome del pacchetto che è stata immessa nella Console di Firebase.

### <a name="add-the-xamarin-google-play-services-base-package"></a>Aggiungere il pacchetto di Base di Xamarin Google Play Services

Perché Firebase Cloud Messaging dipende da Google Play Services, il [Xamarin Google Play Services - Base](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) pacchetto NuGet deve essere aggiunto al progetto xamarin. Android. È necessaria versione 29.0.0.2 o versione successiva.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  In Visual Studio, fare doppio clic **riferimenti > Gestisci pacchetti NuGet...** .

2.  Scegliere il **esplorare** scheda e cercare **Xamarin.GooglePlayServices.Base**.

3.  Installare questo pacchetto nel **FCMClient** progetto:

    [![Installazione di Base di Google Play Services](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1.  In Visual Studio per Mac, fare doppio clic **pacchetti > Aggiungi pacchetti...** .

2.  Cercare **Xamarin.GooglePlayServices.Base**.

3.  Installare questo pacchetto nel **FCMClient** progetto:

    [![Installazione di Base di Google Play Services](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

Se si verifica un errore durante l'installazione di NuGet, chiudere la **FCMClient** del progetto, aprirla nuovamente e ripetere l'installazione di NuGet.

Quando si installa **Xamarin.GooglePlayServices.Base**, anche tutte le dipendenze necessarie siano installate. Modificare **MainActivity.cs** e aggiungere quanto segue `using` istruzione:

```csharp
using Android.Gms.Common;
```

Questa istruzione consente di rendere il `GoogleApiAvailability` classe **Xamarin.GooglePlayServices.Base** disponibili per **FCMClient** codice.
`GoogleApiAvailability` Consente di verificare la presenza di Google Play Services.

### <a name="add-the-xamarin-firebase-messaging-package"></a>Aggiungere il pacchetto Xamarin Firebase Messaging

Per ricevere messaggi da FCM, il [Xamarin Firebase - messaggistica](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) pacchetto NuGet deve essere aggiunto al progetto di app. Senza questo pacchetto, un'applicazione Android non è possibile ricevere messaggi dai server FCM.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  In Visual Studio, fare doppio clic **riferimenti > Gestisci pacchetti NuGet...** .

2. Cercare **firebase**.

3.  Installare questo pacchetto nel **FCMClient** progetto:

    [![Installazione di Xamarin Firebase Messaging](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1.  In Visual Studio per Mac, fare doppio clic **pacchetti > Aggiungi pacchetti...** .

2.  Cercare **firebase**.

3.  Installare questo pacchetto nel **FCMClient** progetto:

    [![Installazione di Xamarin Firebase Messaging](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----

Quando si installa **firebase**, anche tutte le dipendenze necessarie siano installate.

Successivamente, modificare **MainActivity.cs** e aggiungere quanto segue `using` istruzioni:

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

Le prime due istruzioni che tipi nel **firebase** pacchetto NuGet può **FCMClient** codice. **Android.Util** aggiunge funzionalità di registrazione che verrà usata per osservare le transazioni con FMS.

### <a name="add-googleplayservices-json"></a>Aggiungere il file JSON di servizi di Google

Il passaggio successivo consiste nell'aggiungere il **google-Services. JSON** file nella directory radice del progetto:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Copia **google-Services. JSON** alla cartella del progetto.

2.  Aggiungere **google-Services. JSON** al progetto di app (fare clic su **Mostra tutti i file** nel **Esplora soluzioni**, fare clic destro **google-Services. JSON**, quindi selezionare **Includi nel progetto**).

3.  Selezionare **google-Services. JSON** nel **Esplora soluzioni** finestra.

4.  Nel **delle proprietà** riquadro, impostare il **azione di compilazione** al **GoogleServicesJson**:

    [![Impostazione azione di compilazione su GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)

    > [!NOTE] 
    > Se il **GoogleServicesJson** azione di compilazione non viene visualizzata, salvare e chiudere la soluzione, quindi riaprirlo.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1.  Copia **google-Services. JSON** alla cartella del progetto.

2.  Aggiungere **google-Services. JSON** al progetto di app.

3.  Fare doppio clic su **google-Services. JSON**.

4.  Impostare il **azione di compilazione** al **GoogleServicesJson**:

    [![Impostazione azione di compilazione su GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)

-----

Quando **google-Services. JSON** viene aggiunto al progetto (e il **GoogleServicesJson** è impostata l'azione di compilazione), il processo di compilazione estrae l'ID client e [chiave API](./firebase-cloud-messaging.md#fcm-in-action-api-key) e quindi aggiunge queste credenziali per unire/generato dal **androidmanifest. XML** che si trova nella **obj/Debug/android/AndroidManifest.xml**. Questo processo di merge aggiunge automaticamente tutte le autorizzazioni e gli altri elementi necessari per la connessione ai server FCM FCM.


## <a name="check-for-google-play-services-and-create-a-notification-channel"></a>Verificare la presenza di Google Play Services e creare un canale di notifica

Google consiglia che le app Android verificare la presenza dell'APK Google Play Services prima di accedere alle funzionalità di Google Play Services (per altre informazioni, vedere [verificare la presenza di Google Play services](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)).

Verrà creato prima di tutto un layout iniziale per l'interfaccia utente dell'app. Modificare **Resources/layout/Main.axml** e sostituirne il contenuto con il codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="10dp">
    <TextView
        android:text=" "
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/msgText"
        android:textAppearance="?android:attr/textAppearanceMedium"
        android:padding="10dp" />
</LinearLayout>
```

Ciò `TextView` verrà usato per visualizzare i messaggi che indicano se è installato Google Play Services. Salvare le modifiche apportate a **Main. axml**.


Modificare **MainActivity.cs** e aggiungere le seguenti variabili di istanza per il `MainActivity` classe:

```csharp
public class MainActivity : AppCompatActivity
{
    static readonly string TAG = "MainActivity";

    internal static readonly string CHANNEL_ID = "my_notification_channel";
    internal static readonly int NOTIFICATION_ID = 100;

    TextView msgText;
```

Le variabili `CHANNEL_ID` e `NOTIFICATION_ID` verrà utilizzato nel metodo [ `CreateNotificationChannel` ](#create-notification-channel-code) che verrà aggiunto al `MainActivity` più avanti in questa procedura dettagliata.


Nell'esempio seguente, il `OnCreate` metodo verificherà che Google Play Services sia disponibile prima che l'app prova a usare i servizi FCM.
Aggiungere il metodo seguente per il `MainActivity` classe:

```csharp
public bool IsPlayServicesAvailable ()
{
    int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable (this);
    if (resultCode != ConnectionResult.Success)
    {
        if (GoogleApiAvailability.Instance.IsUserResolvableError (resultCode))
            msgText.Text = GoogleApiAvailability.Instance.GetErrorString (resultCode);
        else
        {
            msgText.Text = "This device is not supported";
            Finish ();
        }
        return false;
    }
    else
    {
        msgText.Text = "Google Play Services is available.";
        return true;
    }
}
```

Questo codice verifica il dispositivo per verificare se è installato Google Play Services APK. Se non è installato, viene visualizzato un messaggio nel `TextBox` che chiede all'utente di scaricare un file APK dalla Store di Google Play (o per abilitarlo in Impostazioni sistema del dispositivo).

<a name="create-notification-channel-code"></a>Le app che sono in esecuzione in Android 8.0 (livello API 26) o versione successiva è necessario creare un [ _canale di notifica_ ](~/android/app-fundamentals/notifications/local-notifications.md) per le notifiche di pubblicazione.  Aggiungere il metodo seguente per il `MainActivity` classe che verrà creato il canale di notifica (se necessario):

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var channel = new NotificationChannel(CHANNEL_ID,
                                          "FCM Notifications",
                                          NotificationImportance.Default)
                  {

                      Description = "Firebase Cloud Messages appear in this channel"
                  };

    var notificationManager = (NotificationManager)GetSystemService(Android.Content.Context.NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

Sostituire il metodo `OnCreate` con il codice seguente:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);

    IsPlayServicesAvailable ();

    CreateNotificationChannel();
}
```

`IsPlayServicesAvailable` viene chiamato alla fine di `OnCreate` in modo da Google Play Services verificare viene eseguito ogni volta che l'app viene avviata. Il metodo `CreateNotificationChannel` viene chiamato per verificare l'esistenza di un canale di notifica per i dispositivi che eseguono Android 8 o versione successiva. Se l'app ha un `OnResume` metodo, questo deve chiamare `IsPlayServicesAvailable` da `OnResume` anche. Completamente ricompilare ed eseguire l'app. Se tutto è configurato correttamente, si otterrà una schermata simile alla seguente:

[![App indica che Google Play Services è disponibile](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

Se non si ottiene questo risultato, verificare che l'APK Google Play Services sia installato nel dispositivo (per altre informazioni, vedere [impostazione di backup di Google Play Services](https://developers.google.com/android/guides/setup)).
Verificare inoltre che sia stato aggiunto il **Xamarin.Google.Play.Services.Base** del pacchetto per il **FCMClient** progetto come illustrato in precedenza.


## <a name="add-the-instance-id-receiver"></a>Aggiungere il destinatario di ID di istanza

Il passaggio successivo consiste nell'aggiungere un servizio che consente di estendere `FirebaseInstanceIdService` per gestire la creazione, la rotazione e l'aggiornamento delle [token di registrazione Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token). Il `FirebaseInstanceIdService` servizio è necessario per FCM essere in grado di inviare messaggi al dispositivo. Quando il `FirebaseInstanceIdService` servizio viene aggiunto all'app client, l'app riceverà messaggi FCM e visualizzarli come le notifiche ogni volta che l'app è supportata da un background automaticamente.

### <a name="declare-the-receiver-in-the-android-manifest"></a>Dichiarare il ricevitore nel manifesto Android

Modificare **androidmanifest. XML** e inserire il codice seguente `<receiver>` gli elementi nel `<application>` sezione:

```xml
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
    android:exported="false" />
<receiver
    android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
    android:exported="true"
    android:permission="com.google.android.c2dm.permission.SEND">
    <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
    </intent-filter>
</receiver>
```

Questo codice XML esegue le operazioni seguenti:

-   Dichiara un `FirebaseInstanceIdReceiver` implementazione che offre una [identificatore univoco](https://developers.google.com/instance-id/) per ogni istanza dell'app. In questo ricevitore autentica e autorizza le azioni.

-   Dichiara un interno `FirebaseInstanceIdInternalReceiver` implementazione utilizzata per avviare i servizi in modo sicuro.

-   Il [app ID](./firebase-cloud-messaging.md#fcm-in-action-app-id) viene archiviato nel **google-Services. JSON** file che è stato [aggiunto al progetto](#add-googleplayservices-json). I binding xamarin. Android Firebase sostituirà il token `${applicationId}` con l'ID dell'app; non è necessario alcun codice aggiuntivo per l'app client per fornire l'ID dell'app.

Il `FirebaseInstanceIdReceiver` è un `WakefulBroadcastReceiver` che riceve `FirebaseInstanceId` e `FirebaseMessaging` eventi e li recapita alla classe derivata da `FirebaseInstanceIdService`.

### <a name="implement-the-firebase-instance-id-service"></a>Implementare il servizio Instance ID di Firebase

Le operazioni di registrazione dell'applicazione con FCM viene gestita dall'oggetto personalizzato `FirebaseInstanceIdService` servizio offerto.
`FirebaseInstanceIdService` esegue i passaggi seguenti:

1.  Usa il [API Instance ID](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) per generare i token di sicurezza che autorizzano l'app client per accedere a FCM e del server applicazioni. In cambio, l'app riceve un [token di registrazione](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token) da FCM.

2.  Se il server app lo richiede, inoltra il token di registrazione al server di app.

Aggiungere un nuovo file denominato **MyFirebaseIIDService.cs** e sostituire il codice del modello con il codice seguente:

```csharp
using System;
using Android.App;
using Firebase.Iid;
using Android.Util;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseIIDService : FirebaseInstanceIdService
    {
        const string TAG = "MyFirebaseIIDService";
        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }
        void SendRegistrationToServer(string token)
        {
            // Add custom implementation, as needed.
        }
    }
}
```

Questo servizio implementa un `OnTokenRefresh` metodo richiamato quando il token di registrazione viene inizialmente creato o modificato. Quando `OnTokenRefresh` viene eseguito, recupera il token più recente dal `FirebaseInstanceId.Instance.Token` proprietà (che viene aggiornato in modo asincrono da FCM). In questo esempio, il token aggiornato viene registrato in modo che possa essere visualizzato nella finestra di output:

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` viene richiamato raramente: viene usato per aggiornare il token nelle circostanze seguenti:

-   Quando l'app viene installata o disinstallata.

-   Quando l'utente elimina i dati dell'app.

-   Quando l'app consente di cancellare l'ID di istanza.

-   Quando è stata compromessa la sicurezza del token.

In base a Google [ID istanza](https://developers.google.com/instance-id/guides/android-implementation) documentazione, il servizio Instance ID di FCM richiederà che l'app Aggiorna periodicamente il token (in genere, ogni 6 mesi).

`OnTokenRefresh` anche chiamata `SendRegistrationToAppServer` registrazione dell'utente di associare token con l'account sul lato server (se presente) che viene mantenuto dall'applicazione:

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Poiché questa implementazione dipende dalla progettazione del server app, in questo esempio viene fornito un corpo del metodo vuoto. Se il server di app richiede le informazioni di registrazione FCM, modificare `SendRegistrationToAppServer` per associare il token dell'utente FCM istanza ID con qualsiasi account sul lato server gestito dalla tua app. Si noti che il token è opaco per l'app client.

Quando un token viene inviato al server di app, `SendRegistrationToAppServer` devono mantenere un valore booleano che indica se il token è stato inviato al server. Se è false, il valore booleano `SendRegistrationToAppServer` invia il token al server di app &ndash; in caso contrario, il token è già stato inviato al server di app in una chiamata precedente. In alcuni casi (ad esempio questo `FCMClient` esempio), il server di app non è necessario il token; pertanto, questo metodo non è necessario per questo esempio.

## <a name="implement-client-app-code"></a>Implementare codice dell'app client

Ora che i servizi di ricezione sono presenti, è possibile scrivere codice dell'app client per sfruttare i vantaggi di questi servizi. Nelle sezioni seguenti, viene aggiunto un pulsante nell'interfaccia utente per registrare il token di registrazione (detto anche il *token ID di istanza*), e altro codice viene aggiunto alla `MainActivity` per visualizzare `Intent` informazioni quando l'app viene avviata da un notifica:

[![Pulsante Token log aggiunto alla schermata dell'app](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>Token di log

Il codice aggiunto in questo passaggio è destinato solo a scopo dimostrativo &ndash; un'app client di produzione non ha alcuna necessità di token di registrazione di log. Modificare **Resources/layout/Main.axml** e aggiungere quanto segue `Button` dichiarazione immediatamente dopo il `TextView` elemento:

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

Aggiungere il codice seguente alla fine del metodo `MainActivity.OnCreate`:

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

Questo codice registra il token corrente nella finestra di output quando la **Log Token** pulsante viene toccato.

### <a name="handle-notification-intents"></a>Gestire gli Intent di notifica

Quando l'utente tocca una notifica emesso da **FCMClient**, tutti i dati che accompagna la notifica di messaggio viene reso disponibile in `Intent` funzionalità aggiuntive. Modificare **MainActivity.cs** e aggiungere il codice seguente all'inizio del `OnCreate` metodo (prima della chiamata a `IsPlayServicesAvailable`):

```csharp
if (Intent.Extras != null)
{
    foreach (var key in Intent.Extras.KeySet())
    {
        var value = Intent.Extras.GetString(key);
        Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
    }
}
```

Utilità di avvio dell'app `Intent` viene generato quando l'utente tocca il messaggio di notifica, pertanto questo codice verrà registrati tutti i relativi dati di `Intent` nella finestra di output. Se un altro `Intent` devono essere innanzitutto attivati, il `click_action` campo del messaggio di notifica deve essere impostata su cui `Intent` (l'utilità di avvio `Intent` viene utilizzato se non si specifica `click_action` è specificato).


## <a name="background-notifications"></a>Notifiche in background

Compilare ed eseguire la **FCMClient** app. Il **Log Token** pulsante viene visualizzato:

[![Pulsante di Token di log viene visualizzato](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

Toccare il **Log Token** pulsante. Nella finestra di output dell'IDE deve essere visualizzato un messaggio simile al seguente:

[![Token dell'ID istanza visualizzato nella finestra di Output](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

La lunga stringa contrassegnati con **token** è il token ID di istanza verrà incollato nella Console di Firebase &ndash; selezionare e copiare questa stringa negli Appunti. Se non viene visualizzato un token ID di istanza, aggiungere la riga seguente all'inizio del `OnCreate` metodo per verificare che **google-Services. JSON** è stato analizzato correttamente:

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

Il `google_app_id` valore registrato per la finestra di output deve corrispondere il `mobilesdk_app_id` valore registrato **google-Services. JSON**.

### <a name="send-a-message"></a>Inviare un messaggio

Accedere al [Console di Firebase](https://console.firebase.google.com), selezionare il progetto, fare clic su **notifiche**, fare clic su **invia il primo messaggio**:

[![Il primo messaggio pulsante Invia](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

Nel **messaggio Componi** pagina, immettere il testo del messaggio e selezionare **singolo dispositivo**. Copiare il token ID di istanza dalla finestra di output dell'IDE e incollarlo nella **token di registrazione di FCM** campo della Console di Firebase:

[![Composizione della finestra di messaggio](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

In Android dispositivo (o un emulatore), in background all'app toccando l'Android **Panoramica** pulsante e toccare la schermata iniziale. Quando il dispositivo è pronto, fare clic su **SEND MESSAGE** nella Console di Firebase:

[![Inviare i messaggi, pulsante](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

Quando la **messaggio di revisione** finestra di dialogo è visualizzata, fare clic su **inviare**.
Verrà visualizzata l'icona di notifica nell'area di notifica di dispositivo (o un emulatore):

[![Viene visualizzata l'icona di notifica](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

Aprire l'icona di notifica per visualizzare il messaggio. Il messaggio di notifica deve essere esattamente ciò che è stato digitato nel **testo del messaggio** campo della Console di Firebase:

[![Viene visualizzato il messaggio di notifica sul dispositivo](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

Toccare l'icona di notifica per avviare il **FCMClient** app. Il `Intent` extras inviati a **FCMClient** sono elencati nella finestra di output dell'IDE:

[![Elenchi di Intent extras dalla chiave, ID messaggio e chiave di compressione](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

In questo esempio, il **dal** chiave è impostata sul numero di progetto Firebase dell'app (in questo esempio `41590732`) e il **collapse_key** è impostata sul relativo nome pacchetto ( **com.xamarin.fcmexample**).
Se non si riceve un messaggio, provare a eliminare il **FCMClient** app sul dispositivo (o un emulatore) e ripetere i passaggi precedenti.


> [!NOTE]
> Se si forza e di chiusura dell'app, FCM smetterà di recapito delle notifiche. Android impedisce che le trasmissioni di servizio in background inavvertitamente o inutilmente avviare i componenti di applicazioni arrestate. (Per altre informazioni su questo comportamento, vedere [avviare controlli per le applicazioni arrestate](https://developer.android.com/about/versions/android-3.1.html#launchcontrols).) Per questo motivo, è necessario disinstallare manualmente l'app ogni volta che si eseguirlo e arrestarlo in una sessione di debug &ndash; in tal modo FCM per generare un nuovo token in modo che i messaggi continueranno a essere ricevuto.

### <a name="add-a-custom-default-notification-icon"></a>Aggiungere un'icona di notifica predefinito personalizzato

Nell'esempio precedente, l'icona di notifica è impostata per l'icona dell'applicazione. Il codice XML seguente consente di configurare un'icona personalizzata predefinita per le notifiche. Android Visualizza questa icona personalizzata predefinita per tutti i messaggi di notifica in cui l'icona di notifica non è impostato in modo esplicito.

Per aggiungere un'icona di notifica predefinito personalizzato, aggiungere l'icona per il **risorse/drawable** directory, modificare **androidmanifest. XML**e inserire il codice seguente `<meta-data>` elemento nel `<application>`sezione:

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

In questo esempio, l'icona di notifica che si trova nella **risorse/drawable/ic\_stat\_ic\_notification.png** verrà utilizzata come icona di notifica personalizzato predefinito. Se un'icona personalizzata predefinita non è configurata nel **androidmanifest. XML** e nessuna icona è impostata nel payload della notifica, Android Usa l'icona dell'applicazione come l'icona di notifica (come illustrato nello screenshot di icona di notifica precedente).

## <a name="handle-topic-messages"></a>Gestire i messaggi dell'argomento

Il codice scritto finora gestisce i token di registrazione e aggiunge la funzionalità di notifica remota all'app. L'esempio successivo viene aggiunto codice che attende *i messaggi dell'argomento* e li inoltra all'utente le notifiche come remote. I messaggi dell'argomento sono messaggi FCM inviati a uno o più dispositivi che sottoscrivono un argomento particolare. Per altre informazioni sui messaggi dell'argomento, vedere [messaggistica argomento](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

### <a name="subscribe-to-a-topic"></a>Sottoscrivere un argomento

Modificare **Resources/layout/Main.axml** e aggiungere quanto segue `Button` immediatamente dopo la precedente dichiarazione `Button` elemento:

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

Questo XML consente di aggiungere un **sottoscrivere notifiche** al layout del pulsante.
Modificare **MainActivity.cs** e aggiungere il codice seguente alla fine del `OnCreate` metodo:

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

Questo codice consente di individuare il **sottoscrivere notifiche** pulsante nel layout e assegna il gestore di clic al codice che chiama `FirebaseMessaging.Instance.SubscribeToTopic`, passando nell'argomento sottoscritto _notizie_. Quando l'utente tocca il **"Subscribe"** pulsante, l'app sottoscrive il _notizie_ argomento. Nella sezione seguente, un _notizie_ argomento messaggio verrà inviato dall'interfaccia utente di notifiche della Console di Firebase.

### <a name="send-a-topic-message"></a>Inviare un messaggio di argomento

Disinstallare l'app, ricompilarlo e rieseguirla. Scegliere il **sottoscrivere le notifiche** pulsante:

[![Sottoscrizione al pulsante notifiche](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

Se l'app ha sottoscritto correttamente, si noterà **sincronizzazione argomento ha avuto esito positivo** nell'IDE di finestra di output:

[![Finestra di output mostra il messaggio di sincronizzazione ha avuto esito positivo di argomento](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

Per inviare un messaggio di argomento, procedere come segue:

1.  Nella Console di Firebase fare clic su **nuovo messaggio**.

2.  Nel **messaggio Componi** pagina, immettere il testo del messaggio e selezionare **argomento**.

3.  Nel **argomento** dal menu a discesa selezionare l'argomento predefinito **notizie**:

    [![Selezionando l'argomento di notizie](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4.  In Android dispositivo (o un emulatore), in background all'app toccando l'Android **Panoramica** pulsante e toccare la schermata iniziale.

5.  Quando il dispositivo è pronto, fare clic su **SEND MESSAGE** nella Console di Firebase.

6.  Controllare la finestra di output dell'IDE per visualizzare **/argomenti/notizie** nell'output del log:

    [![Messaggio da /topic/news viene visualizzato](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

Quando questo messaggio viene visualizzato nella finestra di output, l'icona di notifica deve inoltre visualizzati nell'area di notifica sul dispositivo Android. Aprire l'icona di notifica per visualizzare il messaggio:

[![Il messaggio viene visualizzato come una notifica](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

Se non si riceve un messaggio, provare a eliminare il **FCMClient** app sul dispositivo (o un emulatore) e ripetere i passaggi precedenti.

## <a name="foreground-notifications"></a>Notifiche di primo piano

Per ricevere le notifiche nelle App foregrounded, è necessario implementare `FirebaseMessagingService`. Questo servizio è anche necessario per la ricezione di payload dei dati e per l'invio di messaggi a monte. Gli esempi seguenti illustrano come implementare un servizio che consente di estendere `FirebaseMessagingService` &ndash; l'app risulta sarà in grado di gestire le notifiche remote durante l'esecuzione in primo piano.

### <a name="implement-firebasemessagingservice"></a>Implement FirebaseMessagingService

Il `FirebaseMessagingService` servizio è responsabile per ricevere ed elaborare i messaggi da Firebase. Ogni app deve creare una sottoclasse questo tipo e si sostituisce il `OnMessageReceived` per elaborare un messaggio in arrivo. Quando un'app è in primo piano, il `OnMessageReceived` callback gestirà sempre il messaggio.

> [!NOTE]
> Le app hanno solo 10 secondi in cui si desidera gestire un messaggio in arrivo di Cloud Firebase. Alcuna operazione che richiede più tempo rispetto a questo deve essere pianificato per l'esecuzione in background usando una libreria, ad esempio il [Android Job Scheduler](~/android/platform/android-job-scheduler.md) o nella [Firebase processo Dispatcher](~/android/platform/firebase-job-dispatcher.md).

Aggiungere un nuovo file denominato **MyFirebaseMessagingService.cs** e sostituire il codice del modello con il codice seguente:

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Media;
using Android.Util;
using Firebase.Messaging;

namespace FCMClient
{
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    {
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
        }
    }
}
```

Si noti che il `MESSAGING_EVENT` filtro intent deve essere dichiarato in modo che i nuovi messaggi FCM vengono indirizzati a `MyFirebaseMessagingService`:

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

Quando l'app client riceve un messaggio da FCM, `OnMessageReceived` estrae il contenuto del messaggio da passato `RemoteMessage` chiamando il `GetNotification` (metodo). Successivamente, registra il contenuto del messaggio in modo che possa essere visualizzato nella finestra di output dell'IDE:

```csharp
var body = message.GetNotification().Body;
Log.Debug(TAG, "Notification Message Body: " + body);
```

> [!NOTE]
> Se si impostano i punti di interruzione in `FirebaseMessagingService`, la sessione di debug può o può non raggiungere i punti di interruzione a causa di modalità FCM recapita i messaggi.


### <a name="send-another-message"></a>Inviare un altro messaggio

Disinstallare l'app, ricompilarlo, eseguirlo di nuovo e seguire questa procedura per l'invio di un altro messaggio:

1.  Nella Console di Firebase fare clic su **nuovo messaggio**.

2.  Nel **messaggio Componi** pagina, immettere il testo del messaggio e selezionare **singolo dispositivo**.

3.  Copiare la stringa del token dalla finestra di output dell'IDE e incollarlo nella **token di registrazione di FCM** campo della Console di Firebase come indicato in precedenza.

4.  Assicurarsi che l'app è in esecuzione in primo piano, quindi fare clic su **SEND MESSAGE** nella Console di Firebase:

    [![L'invio di un altro messaggio dalla Console di](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5.  Quando la **messaggio di revisione** finestra di dialogo è visualizzata, fare clic su **inviare**.

6.  Il messaggio in arrivo viene registrato per la finestra di output dell'IDE:

    [![Nella finestra di output stampato il corpo del messaggio](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)


### <a name="add-a-local-notification-sender"></a>Aggiungere un server sender notifica locale

In questo esempio rimanente, il messaggio in arrivo FCM verrà convertito in una notifica locale che viene avviata durante l'esecuzione dell'app in primo piano. Modificare **MyFirebaseMessageService.cs** e aggiungere quanto segue `using` istruzioni:

```csharp
using FCMClient;
using System.Collections.Generic;
```

Aggiungere il metodo seguente alla `MyFirebaseMessagingService`:

<a name="sendnotification-method"></a>

```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (var key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }

    var pendingIntent = PendingIntent.GetActivity(this,
                                                  MainActivity.NOTIFICATION_ID,
                                                  intent,
                                                  PendingIntentFlags.OneShot);

    var notificationBuilder = new  NotificationCompat.Builder(this, MainActivity.CHANNEL_ID)
                              .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                              .SetContentTitle("FCM Message")
                              .SetContentText(messageBody)
                              .SetAutoCancel(true)
                              .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManagerCompat.From(this);
    notificationManager.Notify(MainActivity.NOTIFICATION_ID, notificationBuilder.Build());
}
```

Per distinguere questa notifica dalle notifiche in background, questo codice contrassegna le notifiche con un'icona che è diversa dall'icona dell'applicazione. Aggiungere il file [ic\_stat\_ic\_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) alla **risorse/drawable** e includerlo nel **FCMClient** progetto .

Il `SendNotification` metodo viene utilizzato `NotificationCompat.Builder` per creare la notifica e `NotificationManagerCompat` viene usato per avviare la notifica. La notifica contiene un `PendingIntent` che consentirà all'utente di aprire l'app e visualizzare il contenuto della stringa passati `messageBody`. Per altre informazioni sulle `NotificationCompat.Builder`, vedere [le notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md).

Chiamare il `SendNotification` metodo NA konci il `OnMessageReceived` metodo:

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    Log.Debug(TAG, "From: " + message.From);

    var body = message.GetNotification().Body;
    Log.Debug(TAG, "Notification Message Body: " + body);
    SendNotification(body, message.Data);
}
```

In seguito a queste modifiche, `SendNotification` verrà eseguita ogni volta che viene ricevuta una notifica anche se l'app è in primo piano e la notifica verrà visualizzata nell'area di notifica.

Quando un'app è in background, il [payload del messaggio](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) determina come viene gestito il messaggio:

* **Notifica** &ndash; messaggi verranno inviati per il **sulla barra delle applicazioni**. Una notifica locale verrà visualizzato nella scheda. Quando l'utente tocca la notifica verrà avviata l'app.
* **I dati** &ndash; verranno gestiti i messaggi dal `OnMessageReceived`.
* **Entrambe** &ndash; vengono recapitati i messaggi con payload di notifica sia i dati sulla barra delle applicazioni. Quando l'app viene avviata, il payload di dati verrà visualizzati nella `Extras` del `Intent` che è stato usato per avviare l'app.

In questo esempio, se l'app è supportata da un background, `SendNotification` verrà eseguita se il messaggio contiene un payload di dati. In caso contrario, verrà avviata una notifica in background (illustrata in precedenza in questa procedura dettagliata).

### <a name="send-the-last-message"></a>Inviare l'ultimo messaggio

Disinstallare l'app, ricompilarlo, eseguirlo di nuovo, quindi usare la procedura seguente per inviare l'ultimo messaggio:

1.  Nella Console di Firebase fare clic su **nuovo messaggio**.

2.  Nel **messaggio Componi** pagina, immettere il testo del messaggio e selezionare **singolo dispositivo**.

3.  Copiare la stringa del token dalla finestra di output dell'IDE e incollarlo nella **token di registrazione di FCM** campo della Console di Firebase come indicato in precedenza.

4.  Assicurarsi che l'app è in esecuzione in primo piano, quindi fare clic su **SEND MESSAGE** nella Console di Firebase:

    [![L'invio del messaggio di primo piano](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

Questa volta, il messaggio che sono stato registrato nella finestra di output viene anche compresso in una nuova notifica &ndash; l'icona di notifica viene visualizzata nell'area di notifica durante l'esecuzione dell'app in primo piano:

[![Icona di notifica di messaggio di primo piano](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

Quando si apre la notifica, si dovrebbe vedere l'ultimo messaggio inviato dall'interfaccia utente di notifiche della Console di Firebase:

[![Notifica di primo piano illustrata con l'icona in primo piano](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)


## <a name="disconnecting-from-fcm"></a>Disconnessione da FCM

Per annullare la sottoscrizione da un argomento, chiamare il [UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29) metodo le [FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging) classe. Ad esempio, per annullare la sottoscrizione di _notizie_ argomento sottoscritto in precedenza, un **"UNSUBSCRIBE"** pulsante è stato possibile aggiungere al layout con il codice del gestore seguenti:

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

Per annullare la registrazione del dispositivo dal completamente FCM, eliminare l'ID di istanza chiamando il [DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29) metodo sulle [FirebaseInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) classe. Ad esempio:

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

Questa chiamata al metodo elimina l'ID dell'istanza e i dati associati. Di conseguenza, si interrompe l'invio periodico dei dati FCM al dispositivo.


## <a name="troubleshooting"></a>risoluzione dei problemi

Il seguente sono descritti problemi e soluzioni alternative che possono verificarsi durante l'uso di Firebase Cloud Messaging con xamarin. Android.

### <a name="firebaseapp-is-not-initialized"></a>Non è inizializzata FirebaseApp

In alcuni casi, è possibile visualizzare questo messaggio di errore:

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

Si tratta di un problema noto che è possibile risolvere la soluzione di pulizia e ricompilazione del progetto (**compilazione > Pulisci soluzione**, **compilazione > Ricompila soluzione**).

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata dettagliati i passaggi per l'implementazione di notifiche remote con Firebase Cloud Messaging in un'applicazione xamarin. Android. Descritto come installare i pacchetti necessari per le comunicazioni di FCM e spiega come configurare il manifesto Android per accedere ai server FCM. Fornito codice di esempio che illustra come verificare la presenza di Google Play Services. È stato illustrato come implementare un servizio listener instance ID che negozia con FCM per un token di registrazione e spiegato come questo codice crea le notifiche in background mentre l'app è supportata da un background. Illustrato come sottoscrivere i messaggi dell'argomento e fornito un esempio di implementazione di un servizio di listener di messaggio che viene usato per ricevere e visualizzare le notifiche remote durante l'esecuzione dell'app in primo piano.


## <a name="related-links"></a>Collegamenti correlati

- [FCMNotifications (esempio)](https://developer.xamarin.com/samples/monodroid/Firebase/FCMNotifications)
- [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [Informazioni sui messaggi FCM](https://firebase.google.com/docs/cloud-messaging/concept-options)
