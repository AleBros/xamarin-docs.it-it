---
title: Ottenere una chiave API Google Maps
description: Come ottenere una chiave API di Google Maps per l'aggiunta di funzionalità di mappe alla tua app.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 371876d087c7027d4cfe2d2d9ada8b0dbedb5dd5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75488972"
---
# <a name="obtaining-a-google-maps-api-key"></a>Ottenere una chiave API Google Maps

Per utilizzare la funzionalità Google Maps in Android, devi registrarti per una chiave API di Maps con Google. Fino a quando non si esegue questa operazione, verrà visualizzata solo una griglia vuota anziché una mappa nelle applicazioni. Devi ottenere una chiave API Android v2 di Google Maps: le chiavi della vecchia chiave API Android di Google Maps v1 non funzioneranno.

Per ottenere una chiave dell'API Maps v2, è necessario eseguire i passaggi seguenti:Obtaining a Maps API v2 key involves the following steps:

1. Recuperare l'impronta digitale SHA-1 dell'archivio chiavi utilizzato per firmare l'applicazione.
2. Crea un progetto nella console delle API di Google.
3. Recupero della chiave API.

## <a name="obtaining-your-signing-key-fingerprint"></a>Ottenere l'impronta digitale della chiave di firma

Per richiedere una chiave API Maps a Google, è necessario conoscere l'impronta digitale SHA-1 dell'archivio chiavi utilizzato per firmare l'applicazione.
In genere, questo significa che sarà necessario determinare l'impronta digitale SHA-1 per l'archivio chiavi di debug e quindi l'impronta digitale SHA-1 per l'archivio chiavi utilizzato per firmare l'applicazione per il rilascio.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Per impostazione predefinita, il keystore utilizzato per firmare le versioni di debug di un'applicazione Xamarin.Android è disponibile nel percorso seguente:By default the keystore that is used to sign debug versions of a Xamarin.Android application can be found at the following location:

**C:\\\\\\Utenti [NOMEUTENTE] AppData\\Locale\\Xamarin\\Mono per Android\\debug.keystore**

Le informazioni su un archivio chiavi si ottengono eseguendo il comando `keytool` dal JDK. Questo strumento si trova in genere nella directory bin Java:

**C:\\Programmi\\\\Android jdk\\microsoft_dist_openjdk_[VERSION]\\bin\\keytool.exe**

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Per impostazione predefinita, il keystore utilizzato per firmare le versioni di debug di un'applicazione Xamarin.Android è disponibile nel percorso seguente:By default the keystore that is used to sign debug versions of a Xamarin.Android application can be found at the following location:

**/Utenti/[NOMEUTENTE]/.locale/condivisione/Xamarin/Mono per Android/debug.keystore**

Le informazioni su un archivio chiavi si ottengono eseguendo il comando `keytool` dal JDK. Questo strumento si trova in genere nella directory bin Java:

**/System/Libreria/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----

Eseguire keytool utilizzando il seguente comando (utilizzando i percorsi dei file mostrati sopra):

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Esempio Debug.keystore

Per la chiave di debug predefinita (creata automaticamente per il debug), utilizzare questo comando:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----

### <a name="production-keys"></a>Chiavi di produzione

Quando si distribuisce un'app su Google Play, questa deve essere [firmata con una chiave privata.](~/android/deploy-test/signing/index.md)
L'esecuzione `keytool` dovrà essere eseguita con i dettagli della chiave privata e l'impronta digitale SHA-1 risultante utilizzata per creare una chiave API di Google Maps di produzione. Ricordati di aggiornare il file **AndroidManifest.xml** con la chiave API di Google Maps corretta prima della distribuzione.

### <a name="keytool-output"></a>Uscita Keytool

Nella finestra della console dovrebbe essere visualizzato un output simile al seguente:

```shell
Alias name: androiddebugkey
Creation date: Jan 01, 2016
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 4aa9b300
Valid from: Mon Jan 01 08:04:04 UTC 2013 until: Mon Jan 01 18:04:04 PST 2033
Certificate fingerprints:
    MD5:  AE:9F:95:D0:A6:86:89:BC:A8:70:BA:34:FF:6A:AC:F9
    SHA1: BB:0D:AC:74:D3:21:E1:43:07:71:9B:62:90:AF:A1:66:6E:44:5D:75
    Signature algorithm name: SHA1withRSA
    Version: 3
```

L'impronta digitale SHA-1 (elencata dopo **SHA1**) verrà utilizzata più avanti in questa guida.

## <a name="creating-an-api-project"></a>Creazione di un progetto APICreating an API project

Dopo aver recuperato l'impronta digitale SHA-1 dell'archivio chiavi di firma, è necessario creare un nuovo progetto nella console delle API di Google (o aggiungere il servizio Android API v2 di Google Maps a un progetto esistente).

1. In un browser, accedi [all'API console di Google Developers Console & Dashboard servizi](https://console.developers.google.com/apis/dashboard/) e fai clic su Seleziona un **progetto.** Fare clic sul nome di un progetto o crearne uno nuovo facendo clic su **NUOVO PROGETTO**:

   [![Pulsante Google Developer Console CREATE PROJECT](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. Se è stato creato un nuovo progetto, immettere il nome del progetto nella finestra di dialogo **Nuovo progetto** visualizzata. Questa finestra di dialogo produrrà un ID di progetto univoco basato sul nome del progetto. Successivamente, fare clic sul pulsante Crea come illustrato nell'esempio seguente:Next, click the **Create** button as shown in this example:

   [![Il nuovo progetto è denominato XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Dopo circa un minuto, il progetto viene creato e viene visualizzato la pagina **Dashboard** del progetto. A questo campo, fare clic su **ENABLE APIS E SERVICES**:

   [![Facendo clic su Google Maps Android API nella sezione Libreria](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. Nella pagina **Libreria API** fare clic su Maps SDK **per Android**. Nella pagina successiva, fare clic su **ENABLE** per attivare il servizio per questo progetto:

   [![Facendo clic sul pulsante ENABLE nella sezione Dashboard](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

A questo punto è stato creato il progetto API e Google Maps Android API v2 è stato aggiunto ad esso. Tuttavia, non è possibile usare questa API nel progetto finché non si creano le credenziali per tale API. Nella sezione successiva viene illustrato come creare una chiave API ed elencare in bianco un'applicazione Xamarin.Android in modo che sia autorizzato a utilizzare questa chiave.

## <a name="obtaining-the-api-key"></a>Ottenere la chiave API

Dopo aver creato il progetto API **di Google Developer Console,** è necessario creare una chiave API Android.After the Google Developer Console API project has been created, it is necessary to create an Android API key. Le applicazioni Xamarin.Android devono disporre di una chiave API prima di ottenere l'accesso a Android Map API v2.

1. Nella pagina **Maps SDK per Android** visualizzata (dopo aver fatto clic su **ENABLE** nel passaggio precedente), passare alla scheda **Credenziali** e fare clic sul pulsante **Crea credenziali:**

   [![Messaggio Mappa SDK per le credenziali AndroidMaps SDK for Android Credentials message](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Fare clic su **Chiave API**:

   [![Aggiungere credenziali alla finestra di dialogo del progetto](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. Dopo aver fatto clic su questo pulsante, viene generata la chiave API. Successivamente è necessario limitare questa chiave in modo che solo l'app possa chiamare le API con questa chiave. Fare clic su **RESTRICT KEY**:

   [![Facendo clic su Limita chiave nella pagina Credenziali](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Modificare il campo **Nome** dalla **chiave API 1** a un nome che consenta di ricordare per cosa viene utilizzata la chiave (**XamarinMapsDemoKey** viene utilizzato in questo esempio). Successivamente, fai clic sul pulsante di opzione **App Android:**

   [![Selezione delle app Android nella pagina Credenziali](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Per aggiungere l'impronta digitale SHA-1, fare clic su **Aggiungi nome pacchetto e impronta digitale**:

   [![Facendo clic su Aggiungi nome pacchetto e impronta digitale](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Inserisci il nome del pacchetto dell'app e immetti `keytool` l'impronta digitale del certificato SHA-1 (ottenuta tramite come spiegato in precedenza in questa guida). Nell'esempio seguente `XamarinMapsDemo` viene immesso il nome del pacchetto, seguito dall'impronta digitale del certificato SHA-1 ottenuta da **debug.keystore**:

   [![Il nome del pacchetto immesso è com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Tieni presente che, affinché l'APK acceda a Google Maps, devi includere le impronte digitali SHA-1 e i nomi dei pacchetti per ogni keystore (debug e release) che usi per firmare l'APK. Ad esempio, se si utilizza un computer per il debug e un altro computer per generare l'APK di rilascio, è necessario includere l'impronta digitale del certificato SHA-1 dall'archivio chiavi di debug del primo computer e l'impronta digitale del certificato SHA-1 dall'archivio chiavi di rilascio del secondo computer. Fare clic su **Aggiungi nome pacchetto e impronta digitale** per aggiungere un'altra impronta digitale e un altro nome di pacchetto, come illustrato nell'esempio seguente:

   [![L'aggiunta di un'altra impronta digitale crea un altro certificato SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Fare clic sul pulsante **Salva** per salvare le modifiche apportate. Successivamente, si torna all'elenco delle chiavi API. Se si dispone di altre chiavi API create in precedenza, verranno elencate anche qui. In questo esempio viene elencata una sola chiave API (creata nei passaggi precedenti):In this example, only one API key (created in the previous steps) is listed:

   [![XamarinMapsDemoKey viene visualizzato nell'elenco delle chiavi API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Connettere il progetto a un account fatturabile

A partire da giugno,11 2018, la chiave API non funzionerà se il progetto non è connesso a un account fatturabile (anche se il servizio è ancora gratuito per le app per dispositivi mobili).

1. Fai clic sul pulsante del menu hamburger e seleziona la pagina **Fatturazione:**

   [![Selezione della sezione di fatturazione del menu hamburger](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Collega il progetto a un account di fatturazione facendo clic su **Collega un account** di fatturazione seguito da CREATE BILLING **ACCOUNT** nella finestra popup visualizzata (se non hai un account, sarai guidato a crearne uno nuovo):

   [![Collegare il progetto all'account di fatturazione](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Aggiunta della chiave al progetto

Infine, aggiungi questa chiave API al file **AndroidManifest.XML** dell'app Xamarin.Android. Nell'esempio seguente, deve essere sostituito con la chiave API generata nei passaggi precedenti:In the following example, `YOUR_API_KEY` is to be replaced with the API key generated in the previous steps:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    android:versionName="4.10" package="com.xamarin.docs.android.mapsandlocationdemo"
    android:versionCode="10">
...
  <application android:label="@string/app_name">
    <!-- Put your Google Maps V2 API Key here. -->
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
    <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
  </application>
</manifest>
```

## <a name="related-links"></a>Collegamenti correlati

- [Console API Google](https://code.google.com/apis/console/)
- [Chiave API di Google Maps](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](https://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
