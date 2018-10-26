---
title: Ottenere una Google Maps chiave API
description: Come ottenere una chiave API viene eseguito il mapping di Google per l'aggiunta di esegue il mapping di funzionalità all'app.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: bfeb9d8fa2a0b5a9b18ab8266500586e2e3b6c68
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120021"
---
# <a name="obtaining-a-google-maps-api-key"></a>Ottenere una Google Maps chiave API

Per usare la funzionalità di Google Maps in Android, è necessario registrarsi per una chiave API di mappe con Google. Fino a quando non in questo caso, verranno visualizzati solo una griglia vuota invece di una mappa delle applicazioni. È necessario ottenere una chiave di Google Maps Android API v2 - chiavi dal v1 chiave Google Maps Android API precedenti non funzioneranno.

Come ottenere una chiave di v2 API Maps prevede i passaggi seguenti:

1.  Recuperare l'impronta digitale SHA1 dell'archivio chiavi usato per firmare l'applicazione.
2.  Creare un progetto nella Google APIs console.
3.  Come ottenere la chiave API.


## <a name="obtaining-your-signing-key-fingerprint"></a>Ottenere l'ID digitale della chiave di firma

Per richiedere una chiave API di mappe da Google, è necessario conoscere l'impronta digitale SHA1 dell'archivio chiavi usato per firmare l'applicazione.
Ciò significa in genere, è necessario determinare l'impronta digitale SHA-1 per l'archivio di chiavi di debug e quindi dell'impronta digitale SHA-1 per il keystore che verrà usato per firmare l'applicazione per il rilascio.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per impostazione predefinita i keystore che verrà usato per firmare le versioni di debug di xamarin. Android può essere trovato nel percorso seguente:

**C:\\gli utenti\\[USERNAME]\\AppData\\locale\\Xamarin\\Mono for Android\\debug. KeyStore**

Le informazioni su un archivio chiavi si ottengono eseguendo il comando `keytool` dal JDK. Questo strumento si trova in genere nella directory bin di Java:

**C:\\Program Files (x86)\\Java\\jdk [versione]\\bin\\keytool.exe**

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per impostazione predefinita i keystore che verrà usato per firmare le versioni di debug di xamarin. Android può essere trovato nel percorso seguente:

**/Users/[USERNAME]/.Local/Share/Xamarin/mono per Android/debug.keystore**

Le informazioni su un archivio chiavi si ottengono eseguendo il comando `keytool` dal JDK. Questo strumento si trova in genere nella directory bin di Java:

**/System/Library/Java/JavaVirtualMachines/[Version].JDK/Contents/home/bin/keytool**

-----


Esecuzione dello strumento keytool usando il comando seguente (con i percorsi dei file illustrati in precedenza):

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Esempio di debug. KeyStore

Per la chiave di debug predefinito (che viene creata automaticamente per il debug), usare questo comando:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----


### <a name="production-keys"></a>Chiavi di produzione

Quando si distribuisce un'app in Google Play, deve essere [firmati con una chiave privata](~/android/deploy-test/signing/index.md).
Il `keytool` dovrà essere eseguito con i dettagli chiave privati e l'impronta digitale SHA-1 risulta consente di creare una chiave API Google Maps di produzione. Ricordarsi di aggiornare il **androidmanifest. XML** file con la chiave API Google Maps corretta prima della distribuzione.

### <a name="keytool-output"></a>Output dello strumento keytool

Dovrebbe essere simile al seguente output nella finestra della console:

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

Si userà l'impronta digitale SHA-1 (elencate dopo **SHA1**) più avanti in questa Guida.

## <a name="creating-an-api-project"></a>Creazione di un progetto di API

Dopo aver recuperato l'ID digitale SHA-1 della firma dell'archivio chiavi, è necessario creare un nuovo progetto nella Google APIs console (o aggiungere il servizio v2 di Google Maps Android API a un progetto esistente).

1. In un browser, passare al [Dashboard servizi e API di Console agli sviluppatori di Google](https://console.developers.google.com/apis/dashboard/) e fare clic su **selezionare un progetto**. Fare clic sul nome di un progetto o crearne uno nuovo facendo clic **nuovo progetto**:

   [![Pulsante Crea progetto di Google Developer Console](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. Se è stato creato un nuovo progetto, immettere il nome del progetto nel **nuovo progetto** finestra di dialogo visualizzata. Questa finestra di dialogo verrà producono un ID univoco del progetto che si basa sul nome del progetto. Successivamente, fare clic il **Create** pulsante come illustrato in questo esempio:

   [![Nuovo progetto viene denominato XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Dopo circa un minuto, viene creato il progetto e viene visualizzata la **Dashboard** pagina del progetto. Da qui, fare clic su **ABILITA le API e servizi**:

   [![Facendo clic su Google Maps Android API nella sezione della libreria](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. Dal **libreria di API** pagina, fare clic su **Maps SDK per Android**. Nella pagina successiva, fare clic su **abilitare** per attivare il servizio per questo progetto:

   [![Facendo clic sul pulsante ABILITA nella sezione Dashboard](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

A questo punto il progetto di API è stato creato e Google Maps Android API v2 è stato aggiunto ad esso. Tuttavia, è possibile utilizzare questa API nel progetto finché non si crea le credenziali per tale. Nella sezione successiva illustra come creare una chiave API e un'applicazione xamarin. Android-elenco elementi consentiti in modo che è autorizzato a usare questa chiave.

## <a name="obtaining-the-api-key"></a>Come ottenere la chiave API

Dopo il **Google Developer Console** progetto API è stata creata, è necessario creare una chiave API Android. Le applicazioni xamarin. Android devono avere una chiave API prima di ottenere accesso alle API di Android mappa v2.

1. Nel **Maps SDK per Android** pagina visualizzata (dopo aver fatto clic **abilitare** nel passaggio precedente), passare al **credenziali** scheda e fare clic su di **crea le credenziali** pulsante:

   [![Esegue il mapping per il messaggio di credenziali di Android SDK](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Fare clic su **chiave API**:

   [![Aggiungere le credenziali per la finestra di dialogo del progetto](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. Dopo che viene fatto clic su questo pulsante, viene generata la chiave API. Successivamente è necessario limitare questa chiave in modo che solo l'app possa chiamare le API con questa chiave. Fare clic su **chiave RESTRICT**:

   [![Facendo clic su limitare chiave nella pagina credenziali](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Modifica il **Name** campo **API Key 1** a un nome che consenta di identificare facilmente ciò che la chiave viene usata per (**XamarinMapsDemoKey** viene usato in questo esempio). Successivamente, fare clic il **le app Android** pulsante di opzione:

   [![Selezione di App per Android nella pagina credenziali](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Per aggiungere l'impronta digitale SHA-1, fare clic su **+ Aggiungi nome del pacchetto e impronta digitale**:

   [![Impronta digitale e nome del pacchetto facendo clic su Aggiungi](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Immettere il nome di pacchetto dell'app e l'impronta digitale certificato SHA-1 (ottenuto tramite `keytool` come spiegato precedentemente in questa Guida). Nell'esempio seguente, il nome del pacchetto per `XamarinMapsDemo` viene immesso, aggiungendo l'impronta digitale certificato SHA-1 ottenuto da **KeyStore**:

   [![Nome del pacchetto immesso è com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Si noti che, affinché il pacchetto APK l'accesso a Google Maps, è necessario includere le impronte digitali SHA-1 e creare un pacchetto nomi per ogni archivio chiavi (debug e rilascio) che usano per firmare l'APK. Ad esempio, se si usa un computer per il debug e un altro computer per la generazione del file APK di rilascio, è necessario includere l'impronta digitale certificato SHA-1 dall'archivio di chiavi di debug del computer prima e l'impronta digitale certificato SHA-1 dalla versione dell'archivio chiavi di il secondo computer. Fare clic su **+ Aggiungi nome del pacchetto e impronta digitale** per aggiungere un altro nome di impronta digitale e pacchetto come illustrato in questo esempio:

   [![Aggiunta di un altro tramite impronta digitale consente di creare un altro certificato SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Fare clic sul pulsante **Save** (Salva) per salvare le modifiche. Successivamente, si torna all'elenco delle chiavi API. Se si dispongono di altre chiavi API che hanno creato in precedenza, queste verranno anche elencate di seguito. In questo esempio, viene visualizzata solo una chiave API (creata nei passaggi precedenti):

   [![XamarinMapsDemoKey viene visualizzato nell'elenco delle chiavi API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Connettere il progetto a un account fatturabile

A partire dal giugno, 11 2018, la chiave API non funzionerà se il progetto non è connesso a un account fatturabile (anche se il servizio è ancora disponibile per le App per dispositivi mobili).

1. Fare clic sul pulsante di menu hamburger e selezionare il **fatturazione** pagina:

   [![Selezionare la sezione fatturazione del menu hamburger](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Collegare il progetto a un account di fatturazione facendo **collegare un account di fatturazione** aggiungendo **creare ACCOUNT di fatturazione** nella finestra popup visualizzato (se non hai un account, si passerà per crearne uno nuovo):

   [![Progetto di collegamento all'account di fatturazione](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Aggiunta della chiave al progetto

Infine, aggiungere questa chiave API per il **androidmanifest. XML** file dell'app xamarin. Android. Nell'esempio seguente, `YOUR_API_KEY` deve essere sostituito con la chiave API generata nei passaggi precedenti:

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

- [Google APIs Console](https://code.google.com/apis/console/)
- [La chiave API di Google Maps](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
