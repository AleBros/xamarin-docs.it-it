---
title: Notifiche remote con Firebase Cloud Messaging
description: Questa procedura dettagliata fornisce informazioni dettagliate su come utilizzare la messaggistica Cloud Firebase per implementare le notifiche remote (detto anche le notifiche push) in un'applicazione di xamarin. Viene illustrato come implementare varie classi che sono necessari per le comunicazioni con Firebase Cloud Messaging (FCM), vengono forniti esempi su come configurare il file manifesto Android per l'accesso a FCM e dimostra downstream messaggistica utilizzando il Firebase Console.
ms.prod: xamarin
ms.assetid: 4D7C5F46-C997-49F6-AFDA-6763E68CDC90
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: c6e1d36d871b4bb41a1e53d6e58ba8940813b29f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="remote-notifications-with-firebase-cloud-messaging"></a>Notifiche remote con Firebase Cloud Messaging

_Questa procedura dettagliata fornisce informazioni dettagliate su come utilizzare la messaggistica Cloud Firebase per implementare le notifiche remote (detto anche le notifiche push) in un'applicazione di xamarin. Viene illustrato come implementare varie classi che sono necessari per le comunicazioni con Firebase Cloud Messaging (FCM), vengono forniti esempi su come configurare il file manifesto Android per l'accesso a FCM e dimostra downstream messaggistica utilizzando il Firebase Console._

## <a name="fcm-notifications-overview"></a>Panoramica delle notifiche FCM

In questa procedura dettagliata, un'applicazione di base chiamato **FCMClient** verrà creata per illustrare gli aspetti principali di messaggistica FCM. **FCMClient** controlla la presenza di Google Play Services, riceve i token di registrazione da FCM, Visualizza notifiche remote che inviano dalla Console di Firebase e sottoscrive i messaggi di argomento:

[![Schermata di esempio di app](remote-notifications-with-fcm-images/00-app-example-sml.png)](remote-notifications-with-fcm-images/00-app-example.png#lightbox)

Verranno esaminati gli argomenti seguenti:

1.  Notifiche di sfondo

2.  Messaggi di argomento

3.  Notifiche di primo piano

Durante questa procedura dettagliata, verrà aggiunto in modo incrementale funzionalità **FCMClient** ed eseguirlo in un dispositivo o l'emulatore per comprendere come essa interagisce con FCM. Si utilizzerà la registrazione per le transazioni di applicazione in tempo reale con i server FCM di controllo del mirroring, e sarà possibile osservare come le notifiche vengono generate dai messaggi FCM immesso nell'interfaccia utente Firebase Console notifiche. 

## <a name="requirements"></a>Requisiti

Prima di procedere con questa procedura dettagliata, è necessario acquisire le credenziali necessarie per l'utilizzo FCM server di Google; Questo processo è illustrato in [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). In particolare, è necessario scaricare il **google services.json** file da utilizzare con il codice riportato di seguito viene presentato in questa procedura dettagliata. Se si have non ancora creato un progetto nella Console di Firebase (o se non è stato ancora scaricato il **google services.json** file), vedere [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). 

Per eseguire l'applicazione di esempio, è necessario un emulatore compatibile con Firebase o un dispositivo Android test. Messaggistica Cloud firebase supporta i client che eseguono in Android 2.3 (pupazzi) o versione successiva e questi dispositivi devono anche avere la è installata l'app di Google Play Store (Google Play Services 9.2.1 o versione successiva è richiesto). Se non si dispone ancora dell'app di Google Play Store installata nel dispositivo, visitare il [Google Play](https://support.google.com/googleplay) sito web per scaricarlo e installarlo. In alternativa, è possibile utilizzare l'emulatore di Android SDK che eseguono Android 2.3 o versioni successive, anziché un dispositivo di test (non è necessario installare Google Play Store, se si utilizza l'emulatore di Android SDK). 

## <a name="start-an-app-project"></a>Avviare un progetto di App

Per iniziare, creare un nuovo progetto xamarin vuoto denominato **FCMClient**. Se non si ha familiarità con la creazione di progetti di xamarin, vedere [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md). Dopo aver creata la nuova app, il passaggio successivo viene impostato il nome del pacchetto e installare alcuni pacchetti NuGet che verranno utilizzati per la comunicazione con FCM. 

### <a name="set-the-package-name"></a>Impostare il nome del pacchetto

In [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md), si specifica un nome di pacchetto per l'app abilitata FCM. Questo nome di pacchetto funge anche dal *ID applicazione* associato con la chiave API. Configura l'applicazione per usare questo nome pacchetto:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Aprire le proprietà per il **FCMClient** progetto. 

2.  Nel **manifesto Android** pagina, impostare il nome del pacchetto. 

Nell'esempio seguente, il nome del pacchetto è impostato su `com.xamarin.fcmexample`: 

[![L'impostazione del nome di pacchetto](remote-notifications-with-fcm-images/01-package-name-vs-sml.png)](remote-notifications-with-fcm-images/01-package-name-vs.png#lightbox)

Mentre si sta aggiornando il **manifesto Android**, controllare anche per assicurarsi che il `Internet` autorizzazione è abilitata. 

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1.  Aprire le proprietà per il **FCMClient** progetto. 

2.  Nel **applicazione Android** pagina, impostare il nome del pacchetto. 

Nell'esempio seguente, il nome del pacchetto è impostato su `com.xamarin.fcmexample`: 

[![L'impostazione del nome di pacchetto](remote-notifications-with-fcm-images/01-package-name-xs-sml.png)](remote-notifications-with-fcm-images/01-package-name-xs.png#lightbox)

Mentre stanno aggiornando il **manifesto Android**, controllare anche per assicurarsi che il `INTERNET` autorizzazione è abilitata (in **delle autorizzazioni necessarie**). 

-----

Si noti che l'app client in grado di ricevere un token di registrazione da FCM se il nome del pacchetto non *esattamente* corrisponde al nome del pacchetto immesso nella Console di Firebase. 

### <a name="add-the-xamarin-google-play-services-base-package"></a>Aggiungere il pacchetto di Base di Xamarin Google Play Services

Poiché Firebase Cloud Messaging dipende da Google Play Services il [Xamarin Google Play Services - Base](https://www.nuget.org/packages/Xamarin.GooglePlayServices.Base/) pacchetto NuGet deve essere aggiunto al progetto xamarin. Sarà necessario versione 29.0.0.2 o versione successiva.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  In Visual Studio, fare doppio clic su **riferimenti > Gestisci pacchetti NuGet...** . 

2.  Fare clic su di **Sfoglia** scheda e cercare **Xamarin.GooglePlayServices.Base**. 

3.  Installare il pacchetto nel **FCMClient** progetto: 

    [![L'installazione di Base di Google Play Services](remote-notifications-with-fcm-images/02-google-play-services-vs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1.  In Visual Studio per Mac, fare doppio clic su **pacchetti > Aggiungi pacchetti...** . 

2.  Cercare **Xamarin.GooglePlayServices.Base**. 

3.  Installare il pacchetto nel **FCMClient** progetto: 

    [![L'installazione di Base di Google Play Services](remote-notifications-with-fcm-images/02-google-play-services-xs-sml.png)](remote-notifications-with-fcm-images/02-google-play-services-xs.png#lightbox)

-----

Se si verifica un errore durante l'installazione di NuGet, chiudere il **FCMClient** del progetto, aprire di nuovo e ripetere l'installazione di NuGet. 

Quando si installa **Xamarin.GooglePlayServices.Base**, vengono installate anche tutte le dipendenze necessarie. Modifica **Mainactivity** e aggiungere le seguenti `using` istruzione: 

```csharp
using Android.Gms.Common;
```

Questa istruzione consente di rendere il `GoogleApiAvailability` classe **Xamarin.GooglePlayServices.Base** disponibili per **FCMClient** codice. 
`GoogleApiAvailability` viene utilizzato per verificare la presenza di Google Play Services. 

### <a name="add-the-xamarin-firebase-messaging-package"></a>Aggiungere il Xamarin Firebase messaggistica pacchetto

Per ricevere messaggi da FCM, il [Xamarin Firebase - messaggistica](https://www.nuget.org/packages/Xamarin.Firebase.Messaging/) pacchetto NuGet deve essere aggiunto al progetto di app. Senza questo pacchetto, un'applicazione Android non è possibile ricevere messaggi da server FCM.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  In Visual Studio, fare doppio clic su **riferimenti > Gestisci pacchetti NuGet...** . 

2.  Controllare **Includi versione preliminare** e cercare **Xamarin.Firebase.Messaging**. 

3.  Installare il pacchetto nel **FCMClient** progetto: 

    [![L'installazione di Xamarin Firebase messaggistica](remote-notifications-with-fcm-images/03-firebase-messaging-vs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-vs.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1.  In Visual Studio per Mac, fare doppio clic su **pacchetti > Aggiungi pacchetti...** . 

2.  Controllare **Mostra i pacchetti pre-release** e cercare **Xamarin.Firebase.Messaging**. 

3.  Installare il pacchetto nel **FCMClient** progetto: 

    [![L'installazione di Xamarin Firebase messaggistica](remote-notifications-with-fcm-images/03-firebase-messaging-xs-sml.png)](remote-notifications-with-fcm-images/03-firebase-messaging-xs.png#lightbox)

-----
 
Quando si installa **Xamarin.Firebase.Messaging**, vengono installate anche tutte le dipendenze necessarie.

Successivamente, modificare **Mainactivity** e aggiungere le seguenti `using` istruzioni:

```csharp
using Firebase.Messaging;
using Firebase.Iid;
using Android.Util;
```

Le prime due istruzioni in modo che i tipi nel **Xamarin.Firebase.Messaging** pacchetto NuGet disponibili per **FCMClient** codice. **Android.Util** aggiunge la funzionalità di registrazione che verrà utilizzata per osservare le transazioni con FMS. 


### <a name="add-the-google-services-json-file"></a>Aggiungere il File JSON di Google servizi

Il passaggio successivo consiste nell'aggiungere il **google services.json** file nella directory radice del progetto: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Copia **google services.json** alla cartella del progetto.

2.  Aggiungere **google services.json** al progetto di app (fare clic su **Mostra tutti i file** nel **Esplora soluzioni**, fare clic destro **google-services.json**, quindi selezionare **Includi nel progetto**). 

3.  Selezionare **google services.json** nel **Esplora** finestra.

4.  Nel **proprietà** riquadro, impostare il **azione di compilazione** a **GoogleServicesJson** (se il **GoogleServicesJson** azione di compilazione non è visibile, salvare e chiudere la soluzione, quindi riaprirlo):

    [![Impostazione dell'azione di compilazione per GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-vs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-vs.png#lightbox)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1.  Copia **google services.json** alla cartella del progetto.

2.  Aggiungere **google services.json** al progetto di app. 

3.  Fare doppio clic su **google services.json**.

4.  Impostare il **azione di compilazione** a **GoogleServicesJson**: 

    [![Impostazione dell'azione di compilazione per GoogleServicesJson](remote-notifications-with-fcm-images/04-google-services-json-xs-sml.png)](remote-notifications-with-fcm-images/04-google-services-json-xs.png#lightbox)
 
-----
 

Quando **google services.json** viene aggiunto al progetto (e **GoogleServicesJson** è impostato l'azione di compilazione), il processo di compilazione estrae la chiave di ID e API client e quindi aggiunge queste credenziali per l'oggetto unito / generato **AndroidManifest.xml** che si trova in **obj/Debug/android/AndroidManifest.xml**. Questo processo di merge aggiunge automaticamente tutte le autorizzazioni e gli altri elementi che sono necessari per la connessione ai server FCM FCM. 


## <a name="check-for-google-play-services"></a>Controllo per i servizi Google Play

Innanzitutto verrà creato un layout iniziale per l'interfaccia utente dell'applicazione. Modifica **Resources/layout/Main.axml** e sostituirne il contenuto con il codice XML seguente: 

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

Questo `TextView` verrà utilizzato per visualizzare i messaggi che indicano se Google Play Services è installato. Salvare le modifiche in **axml**. Modifica **Mainactivity** e aggiungere la seguente dichiarazione di variabile istanza per la `MainActivity` classe: 

```csharp
TextView msgText;
```

Google consiglia le app Android verificare la presenza di Google Play Services file APK prima di accedere alle funzionalità di Google Play Services (per ulteriori informazioni, vedere [cercare Google Play services](https://firebase.google.com/docs/cloud-messaging/android/client#sample-play)). Nell'esempio seguente, il `OnCreate` metodo verificherà che Google Play Services sia disponibile prima che l'applicazione tenta di utilizzare servizi FCM. Aggiungere il metodo seguente per la `MainActivity` classe: 

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

Questo codice controlla il dispositivo per verificare se è installato il Google Play Services APK. Se non è installato, viene visualizzato un messaggio nel `TextBox` che indica all'utente di scaricare un file APK da Google Play Store (o per abilitare la funzionalità impostazioni di sistema del dispositivo). 

Sostituire il metodo `OnCreate` con il codice seguente: 

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);
    SetContentView (Resource.Layout.Main);
    msgText = FindViewById<TextView> (Resource.Id.msgText);
    IsPlayServicesAvailable ();
}
```

`IsPlayServicesAvailable` viene chiamato alla fine di `OnCreate` in modo da Google Play Services verificare viene eseguito ogni ora di avvio dell'applicazione. Se l'applicazione ha un `OnResume` , il metodo deve chiamare `IsPlayServicesAvailable` da `OnResume` anche. Completamente ricompilare ed eseguire l'app. Se tutto è configurato correttamente, vedrai una schermata simile alla schermata riportata di seguito: 

[![App indica che è disponibile Google Play Services](remote-notifications-with-fcm-images/05-gps-available-sml.png)](remote-notifications-with-fcm-images/05-gps-available.png#lightbox)

Se non si ottiene questo risultato, verificare che il Google Play Services APK sia installato nel dispositivo (per ulteriori informazioni, vedere [impostazione di Google Play Services](https://developers.google.com/android/guides/setup)). Verificare inoltre che siano state aggiunte le **Xamarin.Google.Play.Services.Base** pacchetto il **FCMClient** progetto come spiegato in precedenza.


## <a name="add-the-instance-id-receiver"></a>Aggiungere il destinatario di ID di istanza

Il passaggio successivo consiste nell'aggiungere un servizio che si estende `FirebaseInstanceIdService` per gestire la creazione, la rotazione e l'aggiornamento dei token di registrazione Firebase. (Per ulteriori informazioni sui token di registrazione, vedere [registrazione con FCM](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md).) Il `FirebaseInstanceIdService` servizio è necessario per FCM essere in grado di inviare messaggi al dispositivo. Quando il `FirebaseInstanceIdService` servizio viene aggiunto all'app client, l'app verrà automaticamente messaggi FCM e visualizzarli come notifiche ogni volta che l'app è backgrounded. 

### <a name="declare-the-receiver-in-the-android-manifest"></a>Dichiarare il ricevitore nel manifesto Android

Modifica **AndroidManifest.xml** e inserire il seguente `<receiver>` gli elementi di `<application>` sezione: 

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

-   Dichiara un `FirebaseInstanceIdReceiver` implementazione che fornisce un [identificatore univoco](https://developers.google.com/instance-id/) per ogni istanza di applicazione. Il ricevitore autentica e autorizza azioni. 

-   Dichiara un interno `FirebaseInstanceIdInternalReceiver` implementazione che viene utilizzata per avviare i servizi in modo sicuro.

Il `FirebaseInstanceIdReceiver` è un `WakefulBroadcastReceiver` che riceve `FirebaseInstanceId` e `FirebaseMessaging` gli eventi e li recapita alla classe derivata da `FirebaseInstanceIdService`. 

### <a name="implement-the-firebase-instance-id-service"></a>Implementare il servizio dell'ID istanza Firebase

Le operazioni di registrazione dell'applicazione con FCM viene gestita dall'oggetto personalizzato `FirebaseInstanceIdService` servizio specificata dall'utente. 
`FirebaseInstanceIdService` esegue i passaggi seguenti: 

1.  Usa il [API dell'ID istanza](https://developers.google.com/android/reference/com/google/android/gms/iid/InstanceID) per generare i token di sicurezza che autorizzano l'app client per accedere a FCM e il server di app. In cambio, l'app consente di ottenere una registrazione di token da FCM. 

2.  Inoltra il token di registrazione al server app, se richiesto per il server di app.

Aggiungere un nuovo file denominato **MyFirebaseIIDService.cs** e sostituire il codice del modello con le operazioni seguenti: 

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

Il servizio implementa un `OnTokenRefresh` metodo che viene richiamato quando il token di registrazione viene inizialmente creato o modificato. Quando `OnTokenRefresh` viene eseguito, viene recuperato il token dal più recente di `FirebaseInstanceId.Instance.Token` proprietà (che viene aggiornato in modo asincrono dal FCM). In questo esempio, il token di aggiornamento viene registrato in modo che possa essere visualizzato nella finestra di output: 

```csharp
var refreshedToken = FirebaseInstanceId.Instance.Token;
Log.Debug(TAG, "Refreshed token: " + refreshedToken);
```

`OnTokenRefresh` viene richiamato raramente: viene usato per aggiornare il token nelle circostanze seguenti: 

-   Quando l'app viene installata o disinstallata.

-   Quando l'utente elimina i dati dell'app. 

-   Quando l'app consente di cancellare l'ID di istanza.

-   Quando è stata compromessa la sicurezza del token.

In base a Google [ID istanza](https://developers.google.com/instance-id/guides/android-implementation) documentazione, il servizio di ID istanza FCM richiede che l'applicazione aggiorna periodicamente il relativo token (in genere, ogni 6 mesi). 

`OnTokenRefresh` chiama anche `SendRegistrationToAppServer` per la registrazione dell'utente di associare token con l'account sul lato server (se presente) che viene gestita dall'applicazione: 

```csharp
void SendRegistrationToAppServer (string token)
{
    // Add custom implementation here as needed.
}
```

Poiché questa implementazione dipende dalla progettazione del server app, in questo esempio viene fornito un corpo del metodo vuoto. Se il server app richiede informazioni di registrazione FCM, modificare `SendRegistrationToAppServer` per associare il token dell'utente FCM istanza ID a qualsiasi account sul lato server gestiti dalla tua app. Si noti che il token è opaco per l'applicazione client. 

Quando un token viene inviato al server di app, `SendRegistrationToAppServer` devono mantenere un valore booleano per indicare se il token è stato inviato al server. Se il valore booleano è false, `SendRegistrationToAppServer` invia il token al server app &ndash; in caso contrario, il token è stato già inviato al server di app in una chiamata precedente. In alcuni casi (ad esempio questo `FCMClient` esempio), il server di app non è necessario il token; pertanto, questo metodo non è necessario per questo esempio. 

## <a name="implement-client-app-code"></a>Implementare codice di App Client

Ora che i servizi di ricezione sono presenti, è possibile scrivere codice dell'applicazione client per poter sfruttare questi servizi. Nelle sezioni seguenti viene aggiunto un pulsante nell'interfaccia utente di accedere il token di registrazione (chiamato anche il *token ID istanza*), e altro codice viene aggiunto a `MainActivity` per visualizzare `Intent` informazioni quando l'app viene avviata da un notifica: 

[![Pulsante di Token log aggiunto alla schermata dell'app](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

### <a name="log-tokens"></a>Token di log

Il codice aggiunto in questo passaggio è destinato solo a scopo dimostrativo &ndash; un'app client di produzione non ha alcuna necessità di accedere il token di registrazione. Modifica **Resources/layout/Main.axml** e aggiungere le seguenti `Button` dichiarazione immediatamente dopo il `TextView` elemento: 

```xml
<Button
  android:id="@+id/logTokenButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:text="Log Token" />
```

Modifica **Mainactivity** e aggiungere la seguente dichiarazione di membro per la `MainActivity` classe:

```csharp
const string TAG = "MainActivity";
```

Aggiungere il codice seguente alla fine del `OnCreate` metodo: 

```csharp
var logTokenButton = FindViewById<Button>(Resource.Id.logTokenButton);
logTokenButton.Click += delegate {
    Log.Debug(TAG, "InstanceID token: " + FirebaseInstanceId.Instance.Token);
};
```

Questo codice registra il token corrente nella finestra di output quando il **Log Token** pulsante. 

### <a name="handle-notification-intents"></a>Gestire i tipi di notifica

Quando l'utente tocca una notifica emesso da **FCMClient**, tutti i dati che accompagna tale notifica messaggio viene resa disponibile in `Intent` extra. Modifica **Mainactivity** e aggiungere il codice seguente all'inizio del `OnCreate` metodo (prima della chiamata a `IsPlayServicesAvailable`): 

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

Avvio dell'app `Intent` viene generato quando l'utente tocca il messaggio di notifica, pertanto questo codice verrà registrati tutti i relativi dati di `Intent` nella finestra di output. Se un altro `Intent` deve essere generato il `click_action` campo del messaggio di notifica deve essere impostato su che `Intent` (l'utilità di avvio `Intent` viene utilizzato se non si `click_action` è specificato). 


## <a name="background-notifications"></a>Notifiche di sfondo

Compilare ed eseguire il **FCMClient** app. Il **Log Token** pulsante viene visualizzato:

[![Viene visualizzato il pulsante di Token log](remote-notifications-with-fcm-images/06-log-token-sml.png)](remote-notifications-with-fcm-images/06-log-token.png#lightbox)

Toccare il **Log Token** pulsante. Deve essere visualizzato un messaggio simile al seguente nella finestra di output IDE: 

[![Token ID istanza visualizzato nella finestra di Output](remote-notifications-with-fcm-images/07-token-received-sml.png)](remote-notifications-with-fcm-images/07-token-received.png#lightbox)

La lunga stringa di etichetta con **token** è il token di ID di istanza che verranno incollati nella Console di Firebase &ndash; selezionare e copiare questa stringa negli Appunti. Se non è presente un token di ID di istanza, aggiungere la riga seguente all'inizio del `OnCreate` metodo per verificare che **google services.json** è stato analizzato correttamente:

```csharp
Log.Debug(TAG, "google app id: " + Resource.String.google_app_id);
```

Il `google_app_id` valore registrato nella finestra di output deve corrispondere il `mobilesdk_app_id` valore registrato **google services.json**. 

### <a name="send-a-message"></a>Inviare un messaggio

Accedere al [Firebase Console](https://console.firebase.google.com), selezionare il progetto, fare clic su **notifiche**, fare clic su **invia il primo messaggio**: 

[![Il primo messaggio pulsante Invia](remote-notifications-with-fcm-images/08-first-notification-sml.png)](remote-notifications-with-fcm-images/08-first-notification.png#lightbox)

Nel **Scrivi messaggio** pagina, immettere il testo del messaggio e selezionare **singolo dispositivo**. Copiare il token ID istanza dalla finestra di output di IDE e incollarlo il **token di registrazione FCM** campo della Console Firebase: 

[![Creare una finestra di messaggio](remote-notifications-with-fcm-images/09-compose-message-sml.png)](remote-notifications-with-fcm-images/09-compose-message.png#lightbox)

Android dispositivo (o emulatore), l'applicazione in background toccando il Android **Panoramica** pulsante e toccare la schermata iniziale. Quando il dispositivo è pronto, fare clic su **Invia messaggio** nella Console di Firebase: 

[![Invio messaggio](remote-notifications-with-fcm-images/10-send-message-sml.png)](remote-notifications-with-fcm-images/10-send-message.png#lightbox)

Quando il **messaggio revisione** viene visualizzata una finestra di dialogo, fare clic su **inviare**.
L'icona di notifica visualizzata nell'area di notifica del dispositivo (o dell'emulatore): 

[![Viene visualizzata l'icona di notifica](remote-notifications-with-fcm-images/11-notification-icon-sml.png)](remote-notifications-with-fcm-images/11-notification-icon.png#lightbox)

Aprire l'icona di notifica per visualizzare il messaggio. Il messaggio di notifica deve essere esattamente ciò che è stato digitato nel **testo del messaggio** campo della Console Firebase: 

[![Viene visualizzato il messaggio di notifica sul dispositivo](remote-notifications-with-fcm-images/12-notification-sml.png)](remote-notifications-with-fcm-images/12-notification.png#lightbox)

Toccare l'icona di notifica per ripristinare il **FCMClient** app. Il `Intent` extra inviati **FCMClient** sono elencati nella finestra di output IDE: 

[![Elenchi di extra preventivo provenienti dalla chiave, ID di messaggio e chiave di compressione](remote-notifications-with-fcm-images/13-intent-extras-sml.png)](remote-notifications-with-fcm-images/13-intent-extras.png#lightbox)

In questo esempio, il **da** chiave è impostata per il numero di progetto Firebase dell'app (in questo esempio, `41590732`) e **collapse_key** è impostato per il nome del pacchetto ( **com.xamarin.fcmexample**). Se non si riceve un messaggio, provare a eliminare il **FCMClient** app nel dispositivo (o nell'emulatore) e ripetere i passaggi precedenti. 


> [!NOTE]
> Se si forza e di chiusura dell'applicazione, FCM verrà arrestato il recapito delle notifiche. Android impedisce le trasmissioni di servizio in background di inavvertitamente o inutilmente avviare i componenti di applicazioni arrestate. (Per ulteriori informazioni su questo comportamento, vedere [avviare controlli per le applicazioni arrestate](https://developer.android.com/about/versions/android-3.1.html#launchcontrols).) Per questo motivo, è necessario disinstallare manualmente l'app ogni volta che si eseguirlo e arrestare una sessione di debug &ndash; forza FCM per generare un nuovo token in modo che i messaggi continueranno a essere ricevuti.

### <a name="add-a-custom-default-notification-icon"></a>Aggiungere un'icona di notifica predefinito personalizzato

Nell'esempio precedente, l'icona di notifica è impostata sull'icona dell'applicazione. Il codice XML seguente consente di configurare un'icona personalizzata predefinita per le notifiche. Android Visualizza questa icona predefiniti personalizzati per tutti i messaggi di notifica in cui l'icona di notifica non è impostata in modo esplicito. 

Per aggiungere un'icona di notifica predefinito personalizzato, aggiungere l'icona per il **risorse/drawable** directory, modificare **AndroidManifest.xml**e inserire il seguente `<meta-data>` elemento nel `<application>`sezione: 

```xml
<meta-data
    android:name="com.google.firebase.messaging.default_notification_icon"
    android:resource="@drawable/ic_stat_ic_notification" />
```

In questo esempio, l'icona di notifica che si trova in **risorse/drawable/ic\_stat\_ic\_notification.png** verrà utilizzata come icona predefinita personalizzata. Se un'icona personalizzata predefinita non è configurata in **AndroidManifest.xml** e nessuna icona impostata nel payload di notifica e Android sull'icona dell'applicazione come l'icona di notifica (come illustrato nella schermata di icona notifica precedente). 

## <a name="handle-topic-messages"></a>Gestire i messaggi di argomento

Il codice scritto finora gestisce i token di registrazione e aggiunge la funzionalità di notifica remoto all'app. L'esempio successivo viene aggiunto codice che è in attesa di *messaggi argomento* e li inoltra all'utente remote come le notifiche. Argomento messaggi sono FCM che vengono inviati a uno o più dispositivi che sottoscrivono un determinato argomento. Per ulteriori informazioni sui messaggi di argomento, vedere [argomento messaggistica](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md). 

### <a name="subscribe-to-a-topic"></a>Sottoscrivere un argomento

Modifica **Resources/layout/Main.axml** e aggiungere le seguenti `Button` dichiarazione immediatamente dopo il precedente `Button` elemento:

```xml
<Button
  android:id="@+id/subscribeButton"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  android:layout_gravity="center_horizontal"
  android:layout_marginTop="20dp"
  android:text="Subscribe to Notifications" />
```

Questo codice XML aggiunge un **sottoscrizione di notifica** pulsante al layout.
Modifica **Mainactivity** e aggiungere il codice seguente alla fine del `OnCreate` metodo: 

```csharp
var subscribeButton = FindViewById<Button>(Resource.Id.subscribeButton);
subscribeButton.Click += delegate {
    FirebaseMessaging.Instance.SubscribeToTopic("news");
    Log.Debug(TAG, "Subscribed to remote notifications");
};
```

Questo codice consente di individuare il **sottoscrizione di notifica** nel layout del pulsante e assegna il relativo gestore click per il codice che chiama `FirebaseMessaging.Instance.SubscribeToTopic`, passando l'argomento sottoscritto, _notizie_. Quando l'utente tocca il **Sottoscrivi** pulsante, l'app sottoscrive il _notizie_ argomento. Nella sezione seguente, un _notizie_ argomento messaggio verrà inviato dalla GUI di notifiche di Firebase Console. 

### <a name="send-a-topic-message"></a>Inviare un messaggio di argomento

Disinstallare l'applicazione, ricompilare ed eseguirlo nuovamente. Fare clic su di **sottoscrivere le notifiche** pulsante:

[![Sottoscrivere il pulsante di notifiche](remote-notifications-with-fcm-images/14-subscribe-sml.png)](remote-notifications-with-fcm-images/14-subscribe.png#lightbox)

Se l'app sottoscritti, dovrebbe essere **sincronizzazione argomento ha avuto esito positivo** finestra di output nell'IDE: 

[![Finestra di output visualizza il messaggio di sincronizzazione ha avuto esito positivo di argomento](remote-notifications-with-fcm-images/15-topic-sync-sml.png)](remote-notifications-with-fcm-images/15-topic-sync.png#lightbox)

Per inviare un messaggio di argomento, procedere come segue:

1.  Nella Console di Firebase, fare clic su **nuovo messaggio**. 

2.  Nel **Scrivi messaggio** pagina, immettere il testo del messaggio e selezionare **argomento**. 

3.  Nel **argomento** dal menu a discesa selezionare l'argomento predefinito, **notizie**: 

    [![Selezionando l'argomento di notizie](remote-notifications-with-fcm-images/16-topic-message-sml.png)](remote-notifications-with-fcm-images/16-topic-message.png#lightbox)

4.  Android dispositivo (o emulatore), l'applicazione in background toccando il Android **Panoramica** pulsante e toccare la schermata iniziale. 

5.  Quando il dispositivo è pronto, fare clic su **Invia messaggio** nella Console di Firebase. 

6.  Controllare la finestra di output IDE per vedere **/argomenti/notizie** nell'output del log: 

    [![Messaggio da /topic/news viene visualizzato](remote-notifications-with-fcm-images/17-message-arrived-sml.png)](remote-notifications-with-fcm-images/17-message-arrived.png#lightbox)

Quando questo messaggio viene visualizzato nella finestra di output, l'icona di notifica deve inoltre visualizzati nell'area di notifica sul dispositivo Android. Aprire l'icona di notifica per visualizzare il messaggio dell'argomento: 

[![Il messaggio viene visualizzato come una notifica](remote-notifications-with-fcm-images/18-other-news-sml.png)](remote-notifications-with-fcm-images/18-other-news.png#lightbox)

Se non si riceve un messaggio, provare a eliminare il **FCMClient** app nel dispositivo (o nell'emulatore) e ripetere i passaggi precedenti. 

## <a name="foreground-notifications"></a>Notifiche di primo piano

Per ricevere le notifiche nelle App foregrounded, è necessario implementare `FirebaseMessagingService`. Questo servizio è anche necessario per la ricezione di payload dei dati e per l'invio di messaggi a monte. Nell'esempio seguente viene illustrato come implementare un servizio che si estende `FirebaseMessagingService` &ndash; l'app risulta sarà in grado di gestire le notifiche remote mentre è in esecuzione in primo piano. 

### <a name="implement-firebasemessagingservice"></a>Implementare FirebaseMessagingService

Il `FirebaseMessagingService` servizio include un `OnMessageReceived` metodo scritto per gestire i messaggi in ingresso. Si noti che `OnMessageReceived` viene richiamato per i messaggi di notifica *solo* quando viene eseguita l'app in primo piano. Quando l'app è in esecuzione in background, viene visualizzata una notifica generata automaticamente (come illustrato in precedenza in questa procedura dettagliata). 

Aggiungere un nuovo file denominato **MyFirebaseMessagingService.cs** e sostituire il codice del modello con le operazioni seguenti: 

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

Si noti che il `MESSAGING_EVENT` preventivo filtro deve essere dichiarato in modo che i nuovi messaggi FCM vengono indirizzati a `MyFirebaseMessagingService`: 

```csharp
[IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
```

Quando l'applicazione client riceve un messaggio da FCM, `OnMessageReceived` estrae il contenuto del messaggio da passato `RemoteMessage` oggetto chiamando il relativo `GetNotification` metodo. Successivamente, il contenuto del messaggio viene registrato in modo che possa essere visualizzato nella finestra di output IDE: 

```csharp
Log.Debug(TAG, "From: " + message.From);
Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
```

> [!NOTE]
> Se si imposta punti di interruzione `FirebaseMessagingService`, la sessione di debug può o non può raggiungere i punti di interruzione a causa di modalità FCM recapita i messaggi.
 

### <a name="send-another-message"></a>Inviare un messaggio

Disinstallare l'applicazione, ricompilare, eseguirlo di nuovo e seguire questi passaggi per l'invio di un altro messaggio:

1.  Nella Console di Firebase, fare clic su **nuovo messaggio**. 

2.  Nel **Scrivi messaggio** pagina, immettere il testo del messaggio e selezionare **singolo dispositivo**. 

3.  Copiare la stringa del token dalla finestra di output di IDE e incollarlo il **token di registrazione FCM** campo della Console Firebase come prima. 

4.  Verificare che l'app è in esecuzione in primo piano, quindi fare clic su **Invia messaggio** nella Console di Firebase: 

    [![L'invio di un altro messaggio dalla Console di](remote-notifications-with-fcm-images/19-hello-again-sml.png)](remote-notifications-with-fcm-images/19-hello-again.png#lightbox)

5.  Quando il **messaggio revisione** viene visualizzata una finestra di dialogo, fare clic su **inviare**.

6.  Il messaggio in ingresso viene registrato per la finestra di output IDE:

    [![Corpo del messaggio visualizzato nella finestra di output](remote-notifications-with-fcm-images/20-logged-message.png)](remote-notifications-with-fcm-images/20-logged-message.png#lightbox)


### <a name="add-a-local-notifications-sender"></a>Aggiungere un mittente notifiche locale

In questo esempio rimanente, il messaggio in arrivo FCM sarà convertito una notifica locale che viene avviata durante l'esecuzione dell'app in primo piano. Modifica **MyFirebaseMessageService.cs** e aggiungere le seguenti `using` istruzioni: 

```csharp
using FCMClient;
using System.Collections.Generic;
```

Aggiungere il seguente metodo alla `MyFirebaseMessagingService`: 

```csharp
void SendNotification(string messageBody, IDictionary<string, string> data)
{
    var intent = new Intent(this, typeof(MainActivity));
    intent.AddFlags(ActivityFlags.ClearTop);
    foreach (string key in data.Keys)
    {
        intent.PutExtra(key, data[key]);
    }
    var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

    var notificationBuilder = new Notification.Builder(this)
        .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
        .SetContentTitle("FCM Message")
        .SetContentText(messageBody)
        .SetAutoCancel(true)
        .SetContentIntent(pendingIntent);

    var notificationManager = NotificationManager.FromContext(this);
    notificationManager.Notify(0, notificationBuilder.Build());
}
```

Per distinguere questa notifica dalle notifiche di sfondo, questo codice contrassegna le notifiche con un'icona che è diverso dall'icona applicatiion. Aggiungere [ic\_stat\_ic\_notification.png](remote-notifications-with-fcm-images/ic-stat-ic-notification.png) a **risorse/drawable** e includerlo nel **FCMClient** progetto. 

Il `SendNotification` metodo utilizza `Notification.Builder` per creare la notifica, e `NotificationManager` viene utilizzato per avviare la notifica. La notifica contiene un `PendingIntent` che consentono all'utente di aprire l'app e visualizzare il contenuto della stringa passato `messageBody`. A seconda delle versioni di Android che si desidera utilizzare con l'app, si desidera utilizzare `NotificationCompat.Builder` invece. Per ulteriori informazioni su `Notification.Builder`, vedere [notifiche locale](~/android/app-fundamentals/notifications/local-notifications.md). 

Chiamare il `SendNotification` metodo alla fine di `OnMessageReceived` metodo: 

```csharp
SendNotification(message.GetNotification().Body, message.Data);
```

In seguito a queste modifiche, `SendNotification` verrà eseguito ogni volta che viene ricevuta una notifica, mentre l'app è in primo piano e la notifica è visualizzata nell'area di notifica. Se l'app è backgrounded, `SendNotification` non verrà eseguito e, al contrario, verrà avviata una notifica in background (illustrata in precedenza in questa procedura dettagliata). 

### <a name="send-the-last-message"></a>Invio dell'ultimo messaggio

Disinstallare l'applicazione, ricompilare, eseguirlo di nuovo, quindi utilizzare la procedura seguente per inviare l'ultimo messaggio:

1.  Nella Console di Firebase, fare clic su **nuovo messaggio**. 

2.  Nel **Scrivi messaggio** pagina, immettere il testo del messaggio e selezionare **singolo dispositivo**. 

3.  Copiare la stringa del token dalla finestra di output di IDE e incollarlo il **token di registrazione FCM** campo della Console Firebase come prima. 

4.  Verificare che l'app è in esecuzione in primo piano, quindi fare clic su **Invia messaggio** nella Console di Firebase: 

    [![L'invio del messaggio di primo piano](remote-notifications-with-fcm-images/21-console-fg-msg-sml.png)](remote-notifications-with-fcm-images/21-console-fg-msg.png#lightbox)

Questa volta, il messaggio che sono stato registrato nella finestra di output viene anche compresso in una nuova notifica &ndash; l'icona di notifica viene visualizzata nella barra di notifica durante l'esecuzione dell'app in primo piano: 

[![Icona di notifica per il messaggio di primo piano](remote-notifications-with-fcm-images/22-foreground-icon-sml.png)](remote-notifications-with-fcm-images/22-foreground-icon.png#lightbox)

Quando si apre la notifica, si dovrebbe vedere l'ultimo messaggio inviato dalla GUI di notifiche di Firebase Console: 

[![Notifica di primo piano illustrata con icona di primo piano](remote-notifications-with-fcm-images/23-foreground-msg-sml.png)](remote-notifications-with-fcm-images/23-foreground-msg.png#lightbox)

 
## <a name="troubleshooting"></a>Risoluzione dei problemi

Il seguente sono descritti problemi e soluzioni alternative che possono verificarsi quando si utilizza la messaggistica Cloud Firebase con xamarin.

### <a name="firebaseapp-is-not-initialized"></a>FirebaseApp non è inizializzato.

In alcuni casi, è possibile visualizzare questo messaggio di errore:

```shell
Java.Lang.IllegalStateException: Default FirebaseApp is not initialized in this process
Make sure to call FirebaseApp.initializeApp(Context) first.
```

Si tratta di un problema noto che è possibile risolvere la soluzione di pulizia e ricompilare il progetto (**compilazione > Pulisci soluzione**, **compilazione > Ricompila soluzione**). Per ulteriori informazioni, vedere questo [forum di discussione](https://forums.xamarin.com/discussion/96263/default-firebaseapp-is-not-initialized-in-this-process).


## <a name="summary"></a>Riepilogo

Questa procedura dettagliata in dettaglio i passaggi per l'implementazione di messaggistica Cloud Firebase remote notifiche in un'applicazione di xamarin. Descritto come installare i pacchetti richiesti, necessari per le comunicazioni FCM e spiegato come configurare il file manifesto Android per accedere ai server FCM. Viene fornito codice di esempio che illustra come verificare la presenza di Google Play Services. Illustrato come implementare un servizio di listener di ID istanza che negozia con FCM per un token di registrazione e spiegato come questo codice crea le notifiche in background mentre l'app è backgrounded. Descritto come sottoscrivere i messaggi di argomento e fornito un esempio di implementazione di un servizio di listener di messaggio che viene utilizzato per ricevere e visualizzare le notifiche remote mentre l'app è in esecuzione in primo piano. 


## <a name="related-links"></a>Collegamenti correlati

- [FCMNotifications (esempio)](https://developer.xamarin.com/samples/monodroid/Firebase/FCMNotifications)
- [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md)
