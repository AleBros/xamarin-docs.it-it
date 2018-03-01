---
title: Ottenere un Google esegue il mapping di chiave API
ms.topic: article
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: b529d0090595cc8a3020f37606d5dc3db5f0db74
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="obtaining-a-google-maps-api-key"></a>Ottenere un Google esegue il mapping di chiave API

Per usare la funzionalità mappe di Google Android, è necessario registrarsi per una chiave API di mappe con Google. Fino a quel momento, verranno visualizzati solo una griglia vuota anziché una mappa delle applicazioni. È necessario ottenere una chiave v2 API Android di Google Maps - chiavi dal v1 chiave API Android di Google Maps precedenti non funzioneranno.

Recupero chiave dell'API di mappe di v2 prevede i passaggi seguenti:

1.  Recuperare l'impronta digitale SHA-1 di keystore che verrà utilizzato per firmare l'applicazione.
2.  Creare un progetto in Google APIs console.
3.  Ottenere la chiave API.

<a name="Step_1_-_Obtaining_your_Signing_Key_Fingerprint" />

## <a name="obtaining-your-signing-key-fingerprint"></a>Ottenere l'impronta digitale chiave firma

Per richiedere una chiave API di mappe di Google, è necessario conoscere l'impronta digitale SHA-1 di keystore che verrà utilizzato per firmare l'applicazione.
Ciò significa in genere, è necessario determinare l'impronta digitale SHA-1 per il debug di keystore e quindi di impronte digitali SHA-1 per il file keystore che verrà utilizzato per firmare l'applicazione per il rilascio.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per impostazione predefinita il file keystore che verrà utilizzato per firmare le versioni di debug di un xamarin applicazione può essere trovato nel percorso seguente:

**C:\\utenti\\[USERNAME]\\AppData\\locale\\Xamarin\\Mono per Android\\debug.keystore**

Le informazioni su un archivio chiavi si ottengono eseguendo il comando `keytool` dal JDK. Questo strumento si trova in genere nella directory bin Java:

**C:\\programmi (x86)\\Java\\jdk [versione]\\bin\\keytool.exe**

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per impostazione predefinita il file keystore che verrà utilizzato per firmare le versioni di debug di un xamarin applicazione può essere trovato nel percorso seguente:

**/Users/[USERNAME]/.Local/Share/Xamarin/mono per Android/debug.keystore**

Le informazioni su un archivio chiavi si ottengono eseguendo il comando `keytool` dal JDK. Questo strumento si trova in genere nella directory bin Java:

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----


Eseguire keytool usando il comando seguente (utilizzando i percorsi dei file illustrati in precedenza):

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Esempio debug.KeyStore

Per la chiave di debug predefinito (che viene creata automaticamente per il debug), utilizzare questo comando:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----


### <a name="production-keys"></a>Chiavi di produzione

Quando si distribuisce un'app da Google Play, deve essere [firmato con una chiave privata](~/android/deploy-test/signing/index.md).
Il `keytool` dovrà essere eseguito con i dettagli della chiave privati e l'impronta digitale SHA-1 risulta utilizzato per creare una chiave di Google Maps API di produzione. Ricordarsi di aggiornare il **AndroidManifest.xml** file con la chiave API di Google Maps corretta prima della distribuzione.

### <a name="keytool-output"></a>Output keytool

Dovrebbe essere simile al seguente output nella finestra di console:

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

Si utilizzerà l'impronta digitale SHA-1 (elencate dopo **SHA1**) più avanti in questa Guida.

<a name="Step_2_-Create_an_API_project" />

## <a name="creating-an-api-project"></a>Creazione di un progetto di API

Dopo aver recuperato l'impronta digitale SHA-1 dell'archivio chiavi di firma, è necessario creare un nuovo progetto nella Google APIs console (o aggiungere il servizio di v2 API Android di Google Maps a un progetto esistente).

1. In un browser, passare al [Google Developers Console](https://console.developers.google.com/): fare clic su **Crea progetto**:

   [![Pulsante Crea progetto di Google Developer Console](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png)

2. Nel **nuovo progetto** finestra di dialogo visualizzata, immettere il nome del progetto.
   La finestra di dialogo verrà produrre un ID univoco del progetto che si basa sul nome del progetto, come illustrato in questo esempio:

   [![Nuovo progetto viene denominato XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png)

3. Fare clic sul pulsante **Crea**. Dopo un minuto, il progetto viene creato e che viene visualizzata la **API Manager** pagina. Nel **libreria** fare clic su **API Android di Google Maps**:

   [![Fare clic su API Android di Google Maps nella sezione della libreria](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png)

4. Nella parte superiore del **API Android di Google Maps** pagina, fare clic su **abilitare** per attivare il servizio per questo progetto:

   [![Fare clic sul pulsante Attiva nella sezione Dashboard](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png)


A questo punto è stato creato il progetto di API e v2 l'API Android di Google Maps è stato aggiunto a esso. Tuttavia, è possibile usare questa API nel progetto, fino a creare le credenziali per tale. Quindi esamineremo come creare una chiave API e un'applicazione di xamarin whitelist in modo che si è autorizzato a usare questa chiave.

<a name="Obtaining_the_API_Key" />

## <a name="obtaining-the-api-key"></a>Ottenere la chiave API

Dopo il **Google Developer Console** API progetto è stato creato, è necessario creare una chiave API Android. Applicazioni di xamarin devono avere una chiave API prima di ottenere accesso alle API di Android mappa v2.

1. Nel **API Android di Google Maps** pagina visualizzata (dopo aver fatto clic **abilitare** nel passaggio precedente), fare clic su di **scegliere credenziali** pulsante:

   [![Questa API è abilitata messaggio](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png)

2. Nel **credenziali** pagina, fare clic su di **le credenziali sono necessarie?** pulsante:

   [![Aggiungere le credenziali per la finestra di dialogo del progetto](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png)

3. Dopo questo pulsante, verrà generata la chiave API. Successivamente è necessario limitare questa chiave in modo che solo l'applicazione può chiamare le API con questa chiave. Fare clic su **chiave Restrict**:

   [![Fare clic su chiave limitare nella pagina credenziali](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png)

4. Modifica il **nome** campo **1 chiave API** a un nome che consenta di identificare facilmente ciò che la chiave viene usata per (**XamarinMapsDemoKey** viene utilizzato in questo esempio). Fare clic su, il **App Android** pulsante di opzione:

   [![Selezionare le app Android nella pagina credenziali](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png)

5. Per aggiungere l'impronta digitale SHA-1, fare clic su **+ Aggiungi nome del pacchetto e delle impronte digitali**:

   [![Fare clic su Aggiungi nome del pacchetto e delle impronte digitali](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png)

6. Immettere il nome di pacchetto dell'app e l'impronta digitale certificato SHA-1 (ottenuto tramite `keytool` come descritto in precedenza in questa Guida). Nell'esempio seguente, il nome del pacchetto per `XamarinMapsDemo` è immesso, aggiungendo l'impronta digitale certificato SHA-1 ottenuto da **debug.keystore**:

   [![Nome del pacchetto immesso è com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png)

7. Si noti che, affinché l'APK l'accesso a Google Maps, è necessario includere le impronte digitali SHA-1 e i nomi per ogni archivio chiavi (debug e rilascio) utilizzato per firmare il file APK del pacchetto. Ad esempio, se si utilizza un computer per debug e un altro computer per la generazione di file APK della versione, è necessario includere l'impronta digitale certificato SHA-1 dal debug keystore del primo computer e l'impronta digitale certificato SHA-1 da keystore la versione di il secondo computer. Fare clic su **+ Aggiungi nome del pacchetto e delle impronte digitali** per aggiungere un altro nome del pacchetto e delle impronte digitali, come illustrato in questo esempio:

   [![Aggiunta di un altro impronta digitale crea un altro certificato SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png)

8. Fare clic sul pulsante **Save** (Salva) per salvare le modifiche. Successivamente, si torna all'elenco delle chiavi API. Se si dispongono di altre chiavi API che hanno creato in precedenza, queste verranno inoltre elencate di seguito. In questo esempio, viene elencata solo una chiave API (creata nei passaggi precedenti):

   [![XamarinMapsDemoKey viene visualizzato nell'elenco di chiavi API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png)


<a name="Adding_the_Key" />

## <a name="adding-the-key-to-your-project"></a>Aggiungere la chiave al progetto

Infine, aggiungere questa chiave API per la **AndroidManifest.XML** file dell'app xamarin. Nell'esempio seguente, `YOUR_API_KEY` deve essere sostituita con la chiave API generata nei passaggi precedenti:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    android:versionName="4.10" package="com.xamarin.docs.android.mapsandlocationdemo"
    android:versionCode="10">
...

  <application android:label="@string/app_name">
    <!-- Put your Google Maps V2 API Key here. -->
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_API_KEY" />
    <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
  </application>
</manifest>
```


## <a name="related-links"></a>Collegamenti correlati

- [Google APIs Console.](https://code.google.com/apis/console/)
- [La chiave API di mappe Google](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
