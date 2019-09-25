---
title: Notifiche remote con Firebase Cloud Messaging
description: Questa procedura dettagliata fornisce una spiegazione dettagliata dell'uso di Firebase Cloud Messaging per implementare notifiche remote (dette anche notifiche push) in un'applicazione Novell. Android. Viene illustrato come implementare le varie classi necessarie per le comunicazioni con Firebase Cloud Messaging (FCM), vengono forniti esempi di come configurare il manifesto Android per l'accesso a FCM e viene illustrata la messaggistica downstream mediante Firebase Console.
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: ece503ee305fc1319e766ee5ad52ad86cee122bc
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71249935"
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>Notifiche remote con Firebase Cloud Messaging

_Questa procedura dettagliata fornisce una spiegazione dettagliata dell'uso di Firebase Cloud Messaging per implementare notifiche remote (dette anche notifiche push) in un'applicazione Novell. Android. Viene illustrato come implementare le varie classi necessarie per le comunicazioni con Firebase Cloud Messaging (FCM), vengono forniti esempi di come configurare il manifesto Android per l'accesso a FCM e viene illustrata la messaggistica downstream mediante Firebase Console._

## <a name="fcm-notifications-overview"></a>Panoramica delle notifiche FCM

In questa procedura dettagliata verrà creata un'app di base denominata **FCMClient** per illustrare gli elementi essenziali della messaggistica FCM. **FCMClient** verifica la presenza di Google Play Services, riceve i token di registrazione da FCM, Visualizza le notifiche remote inviate dalla console di Firebase e sottoscrive i messaggi degli argomenti:

[![Schermata di esempio dell'app](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

Verranno analizzate le aree degli argomenti seguenti:

1. Notifiche in background

2. Messaggi di argomento

3. Notifiche in primo piano

Durante questa procedura dettagliata, si aggiungeranno in modo incrementale la funzionalità a **FCMClient** ed eseguirla in un dispositivo o in un emulatore per comprenderne l'interazione con FCM. Si userà la registrazione per verificare le transazioni Live dell'app con i server FCM e si osserverà come vengono generate le notifiche da messaggi FCM immessi nell'interfaccia utente grafica delle notifiche di Firebase console.

## <a name="requirements"></a>Requisiti

Sarà utile acquisire familiarità con i [diversi tipi di messaggi](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) che possono essere inviati da Firebase Cloud Messaging. Il payload del messaggio determinerà il modo in cui un'app client riceverà ed elaborerà il messaggio.

Prima di procedere con questa procedura dettagliata, è necessario acquisire le credenziali necessarie per l'utilizzo dei server FCM di Google; Questo processo è descritto in [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#setup_fcm).
In particolare, è necessario scaricare il file **Google-Services. JSON** da usare con il codice di esempio presentato in questa procedura dettagliata. Se non è ancora stato creato un progetto nella console di Firebase (o se non è ancora stato scaricato il file **Google-Services. JSON** ), vedere [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

Per eseguire l'app di esempio, è necessario un dispositivo di test Android o un emulatore compatibile con con Firebase. Firebase Cloud Messaging supporta i client in esecuzione in Android 4,0 o versione successiva e anche questi dispositivi devono avere l'app Google Play Store installata (è necessario Google Play Services 9.2.1 o versione successiva). Se nel dispositivo non è ancora installata l'app Google Play Store, visitare il sito Web [Google Play](https://support.google.com/googleplay) per scaricarla e installarla. In alternativa, è possibile usare l'emulatore Android SDK con Google Play Services installato invece di un dispositivo di test (non è necessario installare il Google Play Store se si usa l'emulatore Android SDK).

## <a name="start-an-app-project"></a>Avviare un progetto di app

Per iniziare, creare un nuovo progetto Novell. Android vuoto denominato **FCMClient**. Se non si ha familiarità con la creazione di progetti Novell. Android, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).
Dopo aver creato la nuova app, il passaggio successivo consiste nell'impostare il nome del pacchetto e installare diversi pacchetti NuGet che verranno usati per la comunicazione con FCM.

### <a name="set-the-package-name"></a>Imposta il nome del pacchetto

In [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)è stato specificato un nome di pacchetto per l'app abilitata per FCM. Il nome del pacchetto funge anche da [*ID applicazione*](./firebase-cloud-messaging.md#fcm-in-action-app-id) associato alla [chiave API](firebase-cloud-messaging.md#fcm-in-action-api-key). Configurare l'app per l'uso del nome del pacchetto:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Aprire le proprietà per il progetto **FCMClient** .

2. Nella pagina del **manifesto Android** impostare il nome del pacchetto.

Nell'esempio seguente il nome del pacchetto è impostato su `com.xamarin.fcmexample`:

[![Impostazione del nome del pacchetto](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

Durante l'aggiornamento del **manifesto Android**, verificare anche che l' `Internet` autorizzazione sia abilitata.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Aprire le proprietà per il progetto **FCMClient** .

2. Nella pagina **applicazione Android** impostare il nome del pacchetto.

Nell'esempio seguente il nome del pacchetto è impostato su `com.xamarin.fcmexample`:

[![Impostazione del nome del pacchetto](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

Durante l'aggiornamento del **manifesto Android**, verificare anche che l' `INTERNET` autorizzazione sia abilitata (in **autorizzazioni obbligatorie**).

-----

> [!IMPORTANT]
> L'app client non sarà in grado di ricevere un token di registrazione da FCM se il nome del pacchetto non corrisponde *esattamente* al nome del pacchetto immesso nella console di Firebase.

### <a name="add-the-xamarin-google-play-services-base-package"></a>Aggiungere il pacchetto di base Google Play Services Novell

Poiché la messaggistica cloud Firebase dipende da Google Play Services, è necessario aggiungere il pacchetto NuGet [Google Play Services-base Novell](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) al progetto Novell. Android. Sarà necessaria la versione 29.0.0.2 o successiva.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. In Visual Studio fare clic con il pulsante destro del mouse su **riferimenti > Gestisci pacchetti NuGet...** .

2. Fare clic sulla scheda **Sfoglia** e cercare **Novell. GooglePlayServices. base**.

3. Installare il pacchetto nel progetto **FCMClient** :

    [![Installazione di Google Play Services base](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. In Visual Studio per Mac fare clic con il pulsante destro del mouse su **pacchetti > Aggiungi pacchetti...** .

2. Cercare **Novell. GooglePlayServices. base**.

3. Installare il pacchetto nel progetto **FCMClient** :

    [![Installazione di Google Play Services base](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

Se viene visualizzato un errore durante l'installazione di NuGet, chiudere il progetto **FCMClient** , riaprirlo e riprovare l'installazione di NuGet.

Quando si installa **Novell. GooglePlayServices. base**, vengono installate anche tutte le dipendenze necessarie. Modificare **MainActivity.cs** e aggiungere l'istruzione `using` seguente:

```csharp
using Android.Gms.Common;
```

Questa istruzione rende disponibile `GoogleApiAvailability` la classe in **Novell. GooglePlayServices. base** per il codice **FCMClient** .
`GoogleApiAvailability`viene utilizzato per verificare la presenza di Google Play Services.

### <a name="add-the-xamarin-firebase-messaging-package"></a>Aggiungere il pacchetto di messaggistica Firebase di Novell

Per ricevere i messaggi da FCM, è necessario aggiungere il pacchetto NuGet [Novell Firebase-Messaging](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) al progetto dell'app. Senza questo pacchetto, un'applicazione Android non può ricevere messaggi dai server FCM.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. In Visual Studio fare clic con il pulsante destro del mouse su **riferimenti > Gestisci pacchetti NuGet...** .

2. Cercare **Novell. Firebase. Messaging**.

3. Installare il pacchetto nel progetto **FCMClient** :

    [![Installazione di Novell Firebase Messaging](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. In Visual Studio per Mac fare clic con il pulsante destro del mouse su **pacchetti > Aggiungi pacchetti...** .

2. Cercare **Novell. Firebase. Messaging**.

3. Installare il pacchetto nel progetto **FCMClient** :

    [![Installazione di Novell Firebase Messaging](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----

Quando si installa **Novell. Firebase. Messaging**, vengono installate anche tutte le dipendenze necessarie.

Modificare quindi **MainActivity.cs** e aggiungere le istruzioni seguenti `using` :

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

Le prime due istruzioni rendono disponibili i tipi nel pacchetto NuGet **Novell. Firebase. Messaging** per il codice **FCMClient** . **Android. util** aggiunge la funzionalità di registrazione che verrà usata per osservare le transazioni con FMS.

### <a name="add-googleplayservices-json"></a>Aggiungere il file JSON di Google Services

Il passaggio successivo consiste nell'aggiungere il file **Google-Services. JSON** alla directory radice del progetto:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Copiare **Google-Services. JSON** nella cartella del progetto.

2. Aggiungere **Google-Services. JSON** al progetto dell'app (fare clic su **Mostra tutti i file** nella **Esplora soluzioni**, fare clic con il pulsante destro del mouse su **Google-Services. JSON**, quindi scegliere **Includi nel progetto**).

3. Selezionare **Google-Services. JSON** nella finestra **Esplora soluzioni** .

4. Nel riquadro **Proprietà** impostare l'azione di **compilazione** su **GoogleServicesJson**:

    [![Impostazione dell'azione di compilazione su GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)

    > [!NOTE] 
    > Se l'azione di compilazione **GoogleServicesJson** non viene visualizzata, salvare e chiudere la soluzione, quindi riaprirla.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Copiare **Google-Services. JSON** nella cartella del progetto.

2. Aggiungere **Google-Services. JSON** al progetto dell'app.

3. Fare clic con il pulsante destro del mouse su **Google-Services. JSON**.

4. Impostare l' **azione di compilazione** su **GoogleServicesJson**:

    [![Impostazione dell'azione di compilazione su GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)

-----

Quando **Google-Services. JSON** viene aggiunto al progetto (e viene impostata l'azione di compilazione **GoogleServicesJson** ), il processo di compilazione estrae l'ID client e la [chiave API](./firebase-cloud-messaging.md#fcm-in-action-api-key) e quindi aggiunge le credenziali all'oggetto Unito/generato  **File AndroidManifest. XML** che risiede in **obj/debug/Android/file AndroidManifest. XML**. Questo processo di merge aggiunge automaticamente eventuali autorizzazioni e altri elementi FCM necessari per la connessione ai server FCM.

## <a name="check-for-google-play-services-and-create-a-notification-channel"></a>Verificare la presenza di Google Play Services e creare un canale di notifica

Google suggerisce che le app Android verificano la presenza del Google Play Services APK prima di accedere alle funzionalità di Google Play Services (per altre informazioni, vedere Verificare la presenza di [servizi di Google Play](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)).

Verrà creato per primo un layout iniziale per l'interfaccia utente dell'app. Modificare **Resources/layout/Main. aXML** e sostituirne il contenuto con il codice XML seguente:

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

`TextView` Verrà utilizzato per visualizzare i messaggi che indicano se Google Play Services è installato. Salvare le modifiche apportate a **Main. aXML**.

Modificare **MainActivity.cs** e aggiungere le variabili di istanza seguenti alla `MainActivity` classe:

```csharp
public class MainActivity : AppCompatActivity
{
    static readonly string TAG = "MainActivity";

    internal static readonly string CHANNEL_ID = "my_notification_channel";
    internal static readonly int NOTIFICATION_ID = 100;

    TextView msgText;
```

Le variabili `CHANNEL_ID` e `NOTIFICATION_ID` verranno usate nel metodo [`CreateNotificationChannel`](#create-notification-channel-code) che verrà aggiunto a `MainActivity` più avanti in questa procedura dettagliata.

Nell'esempio seguente il `OnCreate` metodo verificherà che Google Play Services è disponibile prima che l'app tenti di usare i servizi FCM.
Aggiungere il metodo seguente alla `MainActivity` classe:

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

Questo codice controlla il dispositivo per verificare se è installato Google Play Services APK. Se non è installato, viene visualizzato un messaggio in `TextBox` che indica all'utente di scaricare un file apk dal Google Play Store o di abilitarlo nelle impostazioni di sistema del dispositivo.

<a name="create-notification-channel-code"></a>Le app in esecuzione in Android 8,0 (livello API 26) o versione successiva devono creare un [_canale di notifica_](~/android/app-fundamentals/notifications/local-notifications.md) per la pubblicazione delle notifiche.  Aggiungere il metodo seguente alla `MainActivity` classe che creerà il canale di notifica, se necessario:

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

`IsPlayServicesAvailable`viene chiamato alla fine di `OnCreate` in modo che il controllo Google Play Services venga eseguito ogni volta che l'app viene avviata. Il metodo `CreateNotificationChannel` viene chiamato per assicurarsi che esista un canale di notifica per i dispositivi che eseguono Android 8 o versione successiva. Se l'app dispone di `OnResume` un metodo, deve chiamare `IsPlayServicesAvailable` anche `OnResume` da. Ricompilare ed eseguire l'app completamente. Se tutti gli elementi sono configurati correttamente, verrà visualizzata una schermata simile alla schermata seguente:

[![App indica che Google Play Services è disponibile](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

Se non si ottiene questo risultato, verificare che il Google Play Services APK sia installato nel dispositivo (per altre informazioni, vedere [configurazione Google Play Services](https://developers.google.com/android/guides/setup)).
Verificare anche di aver aggiunto il pacchetto **Novell. Google. Play. Services. base** al progetto **FCMClient** , come illustrato in precedenza.

## <a name="add-the-instance-id-receiver"></a>Aggiungere il ricevitore dell'ID istanza

Il passaggio successivo consiste nell'aggiungere un servizio che si `FirebaseInstanceIdService` estende per gestire la creazione, la rotazione e l'aggiornamento dei [token di registrazione di Firebase](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token). Il `FirebaseInstanceIdService` servizio è necessario per FCM per poter inviare messaggi al dispositivo. Quando il `FirebaseInstanceIdService` servizio viene aggiunto all'app client, l'app riceverà automaticamente i messaggi FCM e li visualizzerà come notifiche ogni volta che l'app è in background.

### <a name="declare-the-receiver-in-the-android-manifest"></a>Dichiarare il ricevitore nel manifesto Android

Modificare **file AndroidManifest. XML** e inserire gli elementi `<receiver>` seguenti nella `<application>` sezione:

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

- Dichiara un' `FirebaseInstanceIdReceiver` implementazione di che fornisce un [identificatore univoco](https://developers.google.com/instance-id/) per ogni istanza dell'app. In questo ricevitore autentica e autorizza le azioni.

- Dichiara un interno `FirebaseInstanceIdInternalReceiver` implementazione utilizzata per avviare i servizi in modo sicuro.

- L' [ID app](./firebase-cloud-messaging.md#fcm-in-action-app-id) viene archiviato nel file **Google-Services. JSON** [aggiunto al progetto](#add-googleplayservices-json). Le associazioni Firebase di Novell. Android sostituiranno il token `${applicationId}` con l'ID app. non è necessario alcun codice aggiuntivo per l'app client per fornire l'ID app.

`WakefulBroadcastReceiver` `FirebaseInstanceId` `FirebaseInstanceIdService`È un oggetto che riceve gli `FirebaseMessaging` eventi e li recapita alla classe da cui si deriva. `FirebaseInstanceIdReceiver`

### <a name="implement-the-firebase-instance-id-service"></a>Implementare il servizio ID istanza Firebase

L'attività di registrazione dell'applicazione con FCM viene gestita dal servizio personalizzato `FirebaseInstanceIdService` fornito dall'utente.
`FirebaseInstanceIdService`esegue i passaggi seguenti:

1. Usa l' [API ID istanza](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) per generare token di sicurezza che autorizzano l'app client ad accedere a FCM e al server app. Nell'app viene restituito un [token di registrazione](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md#fcm-in-action-registration-token) da FCM.

2. Invia il token di registrazione al server app se richiesto dal server app.

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

Questo servizio implementa un `OnTokenRefresh` metodo che viene richiamato quando il token di registrazione viene inizialmente creato o modificato. Quando `OnTokenRefresh` viene eseguito, recupera il token più recente `FirebaseInstanceId.Instance.Token` dalla proprietà, che viene aggiornata in modo asincrono da FCM. In questo esempio, il token aggiornato viene registrato in modo che possa essere visualizzato nella finestra di output:

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh`viene richiamato raramente: viene usato per aggiornare il token nelle circostanze seguenti:

- Quando l'app viene installata o disinstallata.

- Quando l'utente elimina i dati dell'app.

- Quando l'app cancella l'ID istanza.

- Quando la sicurezza del token è stata compromessa.

Secondo la documentazione relativa all' [ID dell'istanza](https://developers.google.com/instance-id/guides/android-implementation) di Google, il servizio ID istanza FCM richiede che l'app aggiorni periodicamente il token (in genere, ogni 6 mesi).

`OnTokenRefresh`chiama `SendRegistrationToAppServer` anche per associare il token di registrazione dell'utente all'account lato server, se presente, gestito dall'applicazione:

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Poiché questa implementazione dipende dalla progettazione del server applicazioni, in questo esempio viene fornito un corpo del metodo vuoto. Se il server dell'app richiede le informazioni di registrazione `SendRegistrationToAppServer` di FCM, modificare per associare il token ID istanza FCM dell'utente a qualsiasi account lato server gestito dall'app. Si noti che il token è opaco per l'app client.

Quando un token viene inviato al server app, `SendRegistrationToAppServer` deve mantenere un valore booleano per indicare se il token è stato inviato al server. Se questo valore booleano è `SendRegistrationToAppServer` false, invia il token al server &ndash; app in caso contrario, il token è già stato inviato al server app in una chiamata precedente. In alcuni casi (ad esempio, `FCMClient` questo esempio), il server app non richiede il token. Pertanto, questo metodo non è necessario per questo esempio.

## <a name="implement-client-app-code"></a>Implementare il codice dell'app client

Ora che i servizi di ricezione sono disponibili, è possibile scrivere il codice dell'app client per sfruttare i vantaggi offerti da questi servizi. Nelle sezioni seguenti viene aggiunto un pulsante all'interfaccia utente per registrare il token di registrazione (detto anche *token ID istanza*) e viene aggiunto altro codice a `MainActivity` per visualizzare `Intent` le informazioni quando l'app viene avviata da una notifica:

[![Pulsante del token di log aggiunto alla schermata dell'app](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>Token di log

Il codice aggiunto in questo passaggio è destinato solo a scopi &ndash; dimostrativi che un'app client di produzione non dovrà registrare i token di registrazione. Modificare **Resources/layout/Main. aXML** e aggiungere la `Button` dichiarazione seguente subito dopo `TextView` l'elemento:

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

Questo codice registra il token corrente nella finestra output quando viene toccato il pulsante del **token di log** .

### <a name="handle-notification-intents"></a>Gestire gli Intent di notifica

Quando l'utente tocca una notifica emessa da **FCMClient**, tutti i dati associati al messaggio di notifica vengono resi disponibili `Intent` in extra. Modificare **MainActivity.cs** e aggiungere il codice seguente all'inizio del `OnCreate` metodo (prima della chiamata a `IsPlayServicesAvailable`):

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

L'utilità di avvio `Intent` dell'app viene generata quando l'utente tocca il messaggio di notifica, quindi questo codice registrerà tutti i dati `Intent` associati nella finestra di output. Se deve essere `Intent` generato un diverso, il `click_action` campo del messaggio di notifica deve essere impostato su tale `Intent` valore (l' `Intent` utilità di avvio viene `click_action` utilizzata quando non è specificato alcun parametro).

## <a name="background-notifications"></a>Notifiche in background

Compilare ed eseguire l'app **FCMClient** . Viene visualizzato il pulsante del **token di log** :

[![Viene visualizzato il pulsante del token di log](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

Toccare il pulsante **log token** . Nella finestra di output dell'IDE dovrebbe essere visualizzato un messaggio simile al seguente:

[![Token ID istanza visualizzato nella finestra di output](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

La stringa estesa etichettata con **token** è il token ID istanza che verrà incollato nella console &ndash; di Firebase. Selezionare e copiare la stringa negli Appunti. Se non viene visualizzato un token ID istanza, aggiungere la riga seguente all'inizio del `OnCreate` metodo per verificare che **Google-Services. JSON** sia stato analizzato correttamente:

```csharp
Log.Debug(TAG, "google app id: " + GetString(Resource.String.google_app_id));
```

Il `google_app_id` valore registrato nella finestra di output deve corrispondere al `mobilesdk_app_id` valore registrato in **Google-Services. JSON**.

### <a name="send-a-message"></a>Invia un messaggio

Accedere alla [console di Firebase](https://console.firebase.google.com), selezionare il progetto, fare clic su **notifiche**e quindi fare clic su **Invia il primo messaggio**:

[![Pulsante Invia il primo messaggio](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

Nella pagina **Componi messaggio** immettere il testo del messaggio e selezionare **dispositivo singolo**. Copiare il token ID istanza dalla finestra di output dell'IDE e incollarlo nel campo del **token di registrazione FCM** della console Firebase:

[![Finestra di dialogo Componi messaggio](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

Sul dispositivo Android (o sull'emulatore), in background l'app toccando il pulsante **Panoramica** Android e toccando la schermata iniziale. Quando il dispositivo è pronto, fare clic su **Invia messaggio** nella console di Firebase:

[![Pulsante Invia messaggio](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

Quando viene visualizzata la finestra di dialogo **Verifica messaggio** , fare clic su **Invia**.
L'icona di notifica dovrebbe essere visualizzata nell'area di notifica del dispositivo (o dell'emulatore):

[![Viene visualizzata l'icona di notifica](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

Aprire l'icona di notifica per visualizzare il messaggio. Il messaggio di notifica deve essere esattamente ciò che è stato digitato nel campo di **testo del messaggio** della console di Firebase:

[![Il messaggio di notifica viene visualizzato sul dispositivo](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

Toccare l'icona di notifica per avviare l'app **FCMClient** . Le `Intent` funzionalità aggiuntive inviate a **FCMClient** sono elencate nella finestra output IDE:

[![Elenchi di Intent extra da chiave, ID messaggio e chiave di compressione](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

In questo esempio la chiave **from** è impostata sul numero di progetto Firebase dell'app (in questo esempio `41590732`) e **collapse_key** è impostato sul nome del pacchetto (**com. Novell. fcmexample**).
Se non si riceve un messaggio, provare a eliminare l'app **FCMClient** nel dispositivo o nell'emulatore e ripetere i passaggi precedenti.

> [!NOTE]
> Se si forza la chiusura dell'app, FCM smetterà di recapito delle notifiche. Android impedisce la trasmissione di servizi in background da inavvertitamente o inutilmente l'avvio di componenti di applicazioni interrotte. Per ulteriori informazioni su questo comportamento, vedere [avviare i controlli sulle applicazioni arrestate](https://developer.android.com/about/versions/android-3.1.html#launchcontrols). Per questo motivo, è necessario disinstallare manualmente l'app ogni volta che viene eseguita e arrestarla da una sessione &ndash; di debug che impone a FCM di generare un nuovo token in modo che i messaggi continueranno a essere ricevuti.

### <a name="add-a-custom-default-notification-icon"></a>Aggiungere un'icona di notifica predefinita personalizzata

Nell'esempio precedente, l'icona di notifica è impostata sull'icona dell'applicazione. Nel codice XML seguente viene configurata un'icona predefinita personalizzata per le notifiche. Android Visualizza questa icona predefinita personalizzata per tutti i messaggi di notifica in cui l'icona di notifica non è impostata in modo esplicito.

Per aggiungere un'icona di notifica predefinita personalizzata, aggiungere l'icona alla directory **Resources/** di cui è possibile eseguire la modifica **file AndroidManifest. XML**e inserire l' `<application>` elemento seguente `<meta-data>` nella sezione:

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

In questo esempio, l'icona di notifica che risiede in **Resources/disegnator/\_IC\_stat\_IC Notification. png** verrà usata come icona di notifica predefinita personalizzata. Se un'icona personalizzata predefinita non è configurata in **file AndroidManifest. XML** e non è impostata alcuna icona nel payload delle notifiche, Android usa l'icona dell'applicazione come icona di notifica, come illustrato nella schermata dell'icona di notifica riportata sopra.

## <a name="handle-topic-messages"></a>Gestisci messaggi argomento

Il codice scritto finora gestisce i token di registrazione e aggiunge la funzionalità di notifica remota all'app. Nell'esempio seguente viene aggiunto il codice che rimane in ascolto dei *messaggi di argomento* e li trasmette all'utente come notifiche remote. I messaggi di argomento sono messaggi FCM inviati a uno o più dispositivi che sottoscrivono un argomento specifico. Per ulteriori informazioni sui messaggi degli argomenti, vedere l' [argomento messaggistica](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).

### <a name="subscribe-to-a-topic"></a>Sottoscrivere un argomento

Modificare **Resources/layout/Main. aXML** e aggiungere la `Button` dichiarazione seguente immediatamente dopo l' `Button` elemento precedente:

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

Il codice XML aggiunge al layout un pulsante di notifica per la **sottoscrizione** .
Modificare **MainActivity.cs** e aggiungere il codice seguente alla fine del `OnCreate` metodo:

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

Questo codice individua il pulsante **Sottoscrivi notifica** nel layout e assegna il relativo gestore clic al codice che chiama `FirebaseMessaging.Instance.SubscribeToTopic`, passando l'argomento sottoscritto, _News_. Quando l'utente tocca il pulsante **Sottoscrivi** , l'app sottoscrive l'argomento delle _notizie_ . Nella sezione seguente verrà inviato un messaggio di argomento di _notizie_ dall'interfaccia utente grafica delle notifiche di Firebase console.

### <a name="send-a-topic-message"></a>Invia un messaggio di argomento

Disinstallare l'app, ricompilarla ed eseguirla di nuovo. Fare clic sul pulsante **Sottoscrivi notifiche** :

[![Pulsante Sottoscrivi notifiche](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

Se l'app è stata sottoscritta correttamente, nella finestra di output dell'IDE dovrebbe essere visualizzato l' **argomento sincronizzazione completata** :

[![Finestra di output Mostra il messaggio sincronizzazione argomento completato](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

Per inviare un messaggio di argomento, attenersi alla procedura seguente:

1. Nella console di Firebase fare clic su **nuovo messaggio**.

2. Nella pagina **Componi messaggio** immettere il testo del messaggio e selezionare l' **argomento**.

3. Nel menu a discesa dell' **argomento** selezionare l'argomento predefinito **News**:

    [![Selezione dell'argomento News](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4. Sul dispositivo Android (o sull'emulatore), in background l'app toccando il pulsante **Panoramica** Android e toccando la schermata iniziale.

5. Quando il dispositivo è pronto, fare clic su **Invia messaggio** nella console di Firebase.

6. Controllare la finestra di output IDE per visualizzare **/topics/News** nell'output del log:

    [![Viene visualizzato il messaggio da/Topic/News](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

Quando questo messaggio viene visualizzato nella finestra di output, l'icona di notifica deve essere visualizzata anche nell'area di notifica del dispositivo Android. Aprire l'icona di notifica per visualizzare il messaggio dell'argomento:

[![Il messaggio dell'argomento viene visualizzato come notifica](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

Se non si riceve un messaggio, provare a eliminare l'app **FCMClient** nel dispositivo o nell'emulatore e ripetere i passaggi precedenti.

## <a name="foreground-notifications"></a>Notifiche in primo piano

Per ricevere le notifiche nelle app in primo piano, è `FirebaseMessagingService`necessario implementare. Questo servizio è necessario anche per la ricezione di payload di dati e per l'invio di messaggi upstream. Gli esempi seguenti illustrano come implementare un servizio che estende `FirebaseMessagingService` &ndash; l'app risultante sarà in grado di gestire le notifiche remote mentre è in esecuzione in primo piano.

### <a name="implement-firebasemessagingservice"></a>Implementare FirebaseMessagingService

Il `FirebaseMessagingService` servizio è responsabile della ricezione e dell'elaborazione dei messaggi da Firebase. Ogni app deve sottoporre `OnMessageReceived` a sottoclasse questo tipo ed eseguire l'override di per elaborare un messaggio in arrivo. Quando un'app è in primo piano, il `OnMessageReceived` callback gestirà sempre il messaggio.

> [!NOTE]
> Le app hanno solo 10 secondi per gestire un messaggio cloud Firebase in ingresso. Qualsiasi lavoro che richiede più tempo di questo dovrebbe essere pianificato per l'esecuzione in background usando una libreria, ad esempio l' [utilità di pianificazione dei processi Android](~/android/platform/android-job-scheduler.md) o il [dispatcher del processo Firebase](~/android/platform/firebase-job-dispatcher.md).

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

Si noti che `MESSAGING_EVENT` il filtro preventivo deve essere dichiarato in modo da indirizzare i nuovi `MyFirebaseMessagingService`messaggi FCM a:

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

Quando l'app client riceve un messaggio da FCM, `OnMessageReceived` estrae il contenuto del messaggio dall' `RemoteMessage` oggetto passato chiamando il relativo `GetNotification` metodo. Successivamente, registra il contenuto del messaggio in modo che possa essere visualizzato nella finestra di output dell'IDE:

```csharp
var body = message.GetNotification().Body;
Log.Debug(TAG, "Notification Message Body: " + body);
```

> [!NOTE]
> Se si impostano punti `FirebaseMessagingService`di interruzione in, la sessione di debug può o meno raggiungere questi punti di interruzione a causa del modo in cui FCM recapita i messaggi.

### <a name="send-another-message"></a>Invia un altro messaggio

Disinstallare l'app, ricompilarla, eseguirla di nuovo e seguire questa procedura per inviare un altro messaggio:

1. Nella console di Firebase fare clic su **nuovo messaggio**.

2. Nella pagina **Componi messaggio** immettere il testo del messaggio e selezionare **dispositivo singolo**.

3. Copiare la stringa di token dalla finestra di output dell'IDE e incollarla nel campo del **token di registrazione FCM** della console di Firebase come prima.

4. Verificare che l'app sia in esecuzione in primo piano, quindi fare clic su **Invia messaggio** nella console di Firebase:

    [![Invio di un altro messaggio dalla console](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5. Quando viene visualizzata la finestra di dialogo **Verifica messaggio** , fare clic su **Invia**.

6. Il messaggio in arrivo viene registrato nella finestra di output dell'IDE:

    [![Corpo del messaggio stampato nella finestra di output](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)

### <a name="add-a-local-notification-sender"></a>Aggiungere un mittente di notifica locale

In questo esempio rimanente, il messaggio FCM in arrivo verrà convertito in una notifica locale avviata durante l'esecuzione dell'app in primo piano. Modificare **MyFirebaseMessageService.cs** e aggiungere le istruzioni `using` seguenti:

```csharp
using FCMClient;
using System.Collections.Generic;
```

Aggiungere il metodo seguente a `MyFirebaseMessagingService`:

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

Per distinguere questa notifica dalle notifiche in background, questo codice contrassegna le notifiche con un'icona diversa dall'icona dell'applicazione. Aggiungere il file [IC\_stat\_IC\_Notification. png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) alle **risorse/disegnatore** e includerlo nel progetto **FCMClient** .

Il `SendNotification` metodo usa `NotificationCompat.Builder` per creare la notifica e `NotificationManagerCompat` viene usata per avviare la notifica. La notifica contiene un `PendingIntent` oggetto che consentirà all'utente di aprire l'app e visualizzare il contenuto della stringa `messageBody`passata. Per ulteriori informazioni su `NotificationCompat.Builder`, vedere [notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md).

Chiamare il `SendNotification` metodo alla fine `OnMessageReceived` del metodo:

```csharp
public override void OnMessageReceived(RemoteMessage message)
{
    Log.Debug(TAG, "From: " + message.From);

    var body = message.GetNotification().Body;
    Log.Debug(TAG, "Notification Message Body: " + body);
    SendNotification(body, message.Data);
}
```

In seguito a queste modifiche, `SendNotification` viene eseguito ogni volta che viene ricevuta una notifica mentre l'app è in primo piano e la notifica viene visualizzata nell'area di notifica.

Quando un'applicazione è in background, il [payload del messaggio](https://firebase.google.com/docs/cloud-messaging/concept-options#notifications_and_data_messages) determinerà come viene gestito il messaggio:

- **Notifica** di i messaggi verranno inviati alla **barra di sistema.** &ndash; Verrà visualizzata una notifica locale. Quando l'utente tocca la notifica, l'app viene avviata.
- **Dati** di i messaggi verranno gestiti da `OnMessageReceived`. &ndash;
- **Entrambi** &ndash; i messaggi con una notifica e un payload di dati verranno recapitati alla barra di sistema. Quando l'app viene avviata, il payload dei dati verrà `Extras` visualizzato nella `Intent` di usata per avviare l'app.

In questo esempio, se l'app è in background, `SendNotification` verrà eseguita se il messaggio dispone di un payload di dati. In caso contrario, verrà avviata una notifica in background, illustrata in precedenza in questa procedura dettagliata.

### <a name="send-the-last-message"></a>Invia l'ultimo messaggio

Disinstallare l'app, ricompilarla, eseguirla di nuovo, quindi seguire questa procedura per inviare l'ultimo messaggio:

1. Nella console di Firebase fare clic su **nuovo messaggio**.

2. Nella pagina **Componi messaggio** immettere il testo del messaggio e selezionare **dispositivo singolo**.

3. Copiare la stringa di token dalla finestra di output dell'IDE e incollarla nel campo del **token di registrazione FCM** della console di Firebase come prima.

4. Verificare che l'app sia in esecuzione in primo piano, quindi fare clic su **Invia messaggio** nella console di Firebase:

    [![Invio del messaggio in primo piano](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

Questa volta, il messaggio registrato nella finestra di output viene anche incluso in una nuova notifica &ndash; . l'icona di notifica viene visualizzata nella barra delle notifiche mentre l'app è in esecuzione in primo piano:

[![Icona di notifica per il messaggio in primo piano](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

Quando si apre la notifica, dovrebbe essere visualizzato l'ultimo messaggio inviato dall'interfaccia utente grafica delle notifiche di Firebase console:

[![Notifica in primo piano visualizzata con icona in primo piano](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)

## <a name="disconnecting-from-fcm"></a>Disconnessione da FCM

Per annullare la sottoscrizione a un argomento, chiamare il metodo [UnsubscribeFromTopic](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging.html#unsubscribeFromTopic%28java.lang.String%29) nella classe [FirebaseMessaging](https://firebase.google.com/docs/reference/android/com/google/firebase/messaging/FirebaseMessaging) . Ad esempio, per annullare la sottoscrizione dell'argomento di _notizie_ sottoscritto in precedenza, è possibile aggiungere al layout un pulsante **Annulla sottoscrizione** con il seguente codice del gestore:

```csharp
var unSubscribeButton = FindViewById<Button>(Resource.Id.unsubscribeButton);
unSubscribeButton.Click += delegate {
    FirebaseMessaging.Instance.UnsubscribeFromTopic("news");
    Log.Debug(TAG, "Unsubscribed from remote notifications");
};
```

Per annullare completamente la registrazione del dispositivo da FCM, eliminare l'ID istanza chiamando il metodo [DeleteInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId.html#deleteInstanceId%28%29) nella classe [FirebaseInstanceId](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) . Ad esempio:

```csharp
FirebaseInstanceId.Instance.DeleteInstanceId();
```

Questa chiamata al metodo elimina l'ID dell'istanza e i dati ad esso associati. Di conseguenza, l'invio periodico di dati FCM al dispositivo viene interrotto.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Di seguito vengono descritti i problemi e le soluzioni alternative che possono verificarsi quando si usa la messaggistica cloud Firebase con Novell. Android.

### <a name="firebaseapp-is-not-initialized"></a>FirebaseApp non è inizializzato

In alcuni casi, è possibile che venga visualizzato questo messaggio di errore:

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

Si tratta di un problema noto che è possibile risolvere con la pulizia della soluzione e la ricompilazione del progetto (**compilare > soluzione pulita**, **compilare la soluzione > ricompila**).

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata illustra i passaggi per l'implementazione delle notifiche remote di Firebase Cloud Messaging in un'applicazione Novell. Android. Viene descritto come installare i pacchetti necessari per le comunicazioni FCM e viene illustrato come configurare il manifesto Android per l'accesso ai server FCM. Viene fornito codice di esempio che illustra come verificare la presenza di Google Play Services. È stato illustrato come implementare un servizio listener ID istanza che negozia con FCM per un token di registrazione e spiega come questo codice crei notifiche in background mentre l'app è in background. In questo articolo è stato illustrato come sottoscrivere i messaggi dell'argomento e viene fornita un'implementazione di esempio di un servizio di listener di messaggi utilizzato per ricevere e visualizzare le notifiche remote mentre l'applicazione è in esecuzione in primo piano.

## <a name="related-links"></a>Collegamenti correlati

- [FCMNotifications (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/firebase-fcmnotifications)
- [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
- [Informazioni sui messaggi FCM](https://firebase.google.com/docs/cloud-messaging/concept-options)
