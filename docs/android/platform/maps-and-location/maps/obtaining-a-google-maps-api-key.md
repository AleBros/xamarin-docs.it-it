---
title: Ottenere una chiave API Google Maps
description: Come ottenere una chiave API di Google Maps per aggiungere la funzionalità Maps all'app.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: 3868b2a35894cdcd7a11c626268307338744ecb4
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250055"
---
# <a name="obtaining-a-google-maps-api-key"></a>Ottenere una chiave API Google Maps

Per usare la funzionalità Google Maps in Android, è necessario registrarsi per una chiave API Maps con Google. Fino a quando non si esegue questa operazione, verrà visualizzata solo una griglia vuota anziché una mappa nelle applicazioni. È necessario ottenere le chiavi chiave di Google Maps Android API v2 dalla versione precedente dell'API Android di Google Maps non funziona.

Il recupero di una chiave dell'API di Maps v2 prevede i passaggi seguenti:

1. Recuperare l'impronta digitale SHA-1 dell'archivio chiavi usato per firmare l'applicazione.
2. Creare un progetto nella console di Google APIs.
3. Ottenere la chiave API.

## <a name="obtaining-your-signing-key-fingerprint"></a>Acquisizione dell'impronta digitale della chiave di firma

Per richiedere una chiave API Maps da Google, è necessario avere a conoscenza l'impronta digitale SHA-1 dell'archivio chiavi usato per firmare l'applicazione.
In genere, ciò significa che sarà necessario determinare l'impronta digitale SHA-1 per l'archivio chiavi di debug e quindi l'impronta digitale SHA-1 per l'archivio chiavi usato per firmare l'applicazione per il rilascio.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per impostazione predefinita, l'archivio chiavi usato per firmare le versioni di debug di un'applicazione Novell. Android si trova nel percorso seguente:

**C:\\Users\\[username\\]\\AppData\\LocalNovell\\mono for\\Android Debug. keystore**

Le informazioni su un archivio chiavi si ottengono eseguendo il comando `keytool` dal JDK. Questo strumento si trova in genere nella directory bin java:

**C:\\programmi (x86)\\Java\\JDK [versione]\\bin\\Tool. exe**

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per impostazione predefinita, l'archivio chiavi usato per firmare le versioni di debug di un'applicazione Novell. Android si trova nel percorso seguente:

**/Utenti/[USERNAME]/.local/share/Xamarin/Mono per Android/Debug. keystore**

Le informazioni su un archivio chiavi si ottengono eseguendo il comando `keytool` dal JDK. Questo strumento si trova in genere nella directory bin java:

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----

Eseguire lo strumento con il comando seguente (usando i percorsi di file illustrati in precedenza):

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Esempio di debug. keystore

Per la chiave di debug predefinita, creata automaticamente per il debug, usare questo comando:

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

Quando si distribuisce un'app Google Play, deve essere [firmata con una chiave privata](~/android/deploy-test/signing/index.md).
Il `keytool` deve essere eseguito con i dettagli della chiave privata e l'impronta digitale SHA-1 risultante usata per creare una chiave API di Google Maps di produzione. Ricordarsi di aggiornare il file **file AndroidManifest. XML** con la chiave dell'API Google Maps corretta prima della distribuzione.

### <a name="keytool-output"></a>Output della funzione

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

Si userà l'impronta digitale SHA-1 (elencata dopo **SHA1**) più avanti in questa guida.

## <a name="creating-an-api-project"></a>Creazione di un progetto API

Dopo aver recuperato l'impronta digitale SHA-1 dell'archivio chiavi di firma, è necessario creare un nuovo progetto nella console di Google APIs oppure aggiungere il servizio Google Maps Android API v2 a un progetto esistente.

1. In un browser passare al [Dashboard Google Developers Console API & Services](https://console.developers.google.com/apis/dashboard/) e fare clic su **Seleziona un progetto**. Fare clic su un nome di progetto o crearne uno nuovo facendo clic su **nuovo progetto**:

   [![Pulsante Crea progetto di Google Developer Console](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. Se è stato creato un nuovo progetto, immettere il nome del progetto nella finestra di dialogo **nuovo progetto** visualizzata. In questa finestra di dialogo verrà prodotto un ID progetto univoco basato sul nome del progetto. Fare quindi clic sul pulsante **Crea** , come illustrato nell'esempio seguente:

   [![Il nuovo progetto è denominato XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Dopo un minuto, il progetto viene creato e viene eseguita la pagina **Dashboard** del progetto. Da qui, fare clic su **Abilita API e servizi**:

   [![Selezione dell'API Android di Google Maps nella sezione libreria](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. Dalla pagina **libreria API** fare clic su **Maps SDK per Android**. Nella pagina successiva fare clic su **Abilita** per attivare il servizio per il progetto:

   [![Fare clic sul pulsante Abilita nella sezione Dashboard](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

A questo punto è stato creato il progetto API e l'API di Google Maps Android V2 è stata aggiunta. Tuttavia, non è possibile usare questa API nel progetto finché non vengono create le relative credenziali. La sezione successiva illustra come creare una chiave API ed elencare un elenco di applicazioni Novell. Android in modo che sia autorizzato a usare questa chiave.

## <a name="obtaining-the-api-key"></a>Ottenere la chiave API

Dopo aver creato il progetto API della **console Google Developer** , è necessario creare una chiave API Android. Le applicazioni Novell. Android devono avere una chiave API prima di concedere l'accesso all'API della mappa Android V2.

1. Nella pagina **Maps SDK per Android** visualizzata (dopo aver fatto clic su **Abilita** nel passaggio precedente) andare alla scheda **Credentials (credenziali** ) e fare clic sul pulsante **create Credentials (crea credenziali** ):

   [![Maps SDK per il messaggio di credenziali Android](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Fare clic su **chiave API**:

   [![Aggiungere credenziali alla finestra di dialogo del progetto](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. Quando si fa clic su questo pulsante, viene generata la chiave API. Successivamente, è necessario limitare questa chiave in modo che solo l'app possa chiamare le API con questa chiave. Fare clic su **limita chiave**:

   [![Fare clic su limita chiave nella pagina credenziali](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Modificare il campo **nome** da **chiave API 1** a un nome che consenta di ricordare la chiave utilizzata per (**XamarinMapsDemoKey** viene usato in questo esempio). Fare quindi clic sul pulsante di opzione **app Android** :

   [![Selezione delle app Android nella pagina credenziali](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Per aggiungere l'impronta digitale SHA-1, fare clic su **+ Aggiungi nome pacchetto e impronta digitale**:

   [![Fare clic su Aggiungi nome pacchetto e impronta digitale](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Immettere il nome del pacchetto dell'app e immettere l'impronta digitale del certificato SHA-1 `keytool` , ottenuta tramite, come illustrato in precedenza in questa guida. Nell'esempio seguente viene immesso il nome del `XamarinMapsDemo` pacchetto per, seguito dall'impronta digitale del certificato SHA-1 ottenuta da **debug. keystore**:

   [![Il nome del pacchetto immesso è com. Novell. docs. Android. map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Si noti che, affinché il pacchetto APK l'accesso a Google Maps, è necessario includere le impronte digitali SHA-1 e creare un pacchetto nomi per ogni archivio chiavi (debug e rilascio) che usano per firmare l'APK. Ad esempio, se si usa un computer per il debug e un altro computer per la generazione del file APK di rilascio, è necessario includere l'impronta digitale certificato SHA-1 dall'archivio di chiavi di debug del computer prima e l'impronta digitale certificato SHA-1 dalla versione dell'archivio chiavi di il secondo computer. Fare clic su **+ Aggiungi nome pacchetto e impronta digitale** per aggiungere un'altra impronta digitale e il nome del pacchetto, come illustrato nell'esempio seguente:

   [![L'aggiunta di un'altra impronta digitale crea un altro certificato SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Fare clic sul pulsante **Save** (Salva) per salvare le modifiche. Successivamente, viene restituito l'elenco delle chiavi API. Se sono presenti altre chiavi API create in precedenza, verranno elencate qui. In questo esempio viene elencata una sola chiave API (creata nei passaggi precedenti):

   [![XamarinMapsDemoKey viene visualizzato nell'elenco delle chiavi API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Connettere il progetto a un account fatturabile

A partire dall'11 giugno 2018, la chiave API non funzionerà se il progetto non è connesso a un account fatturabile (anche se il servizio è ancora disponibile per le app per dispositivi mobili).

1. Fare clic sul pulsante di menu hamburger e selezionare la pagina **fatturazione** :

   [![Selezione della sezione di fatturazione del menu hamburger](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Collegare il progetto a un account di fatturazione facendo clic su **collega un account di fatturazione** seguito da **Crea account di fatturazione** nel popup visualizzato (se non si dispone di un account, verrà richiesto di crearne uno nuovo):

   [![Collega progetto a account di fatturazione](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Aggiunta della chiave al progetto

Aggiungere infine questa chiave API al file **file AndroidManifest. XML** dell'app Novell. Android. Nell'esempio seguente, `YOUR_API_KEY` deve essere sostituito con la chiave API generata nei passaggi precedenti:

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

- [Console di Google APIs](https://code.google.com/apis/console/)
- [Chiave API di Google Maps](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
