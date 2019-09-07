---
title: Funzionalità di Oreo
description: Come iniziare a usare Novell. Android per sviluppare app per la versione più recente di Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 07/06/2018
ms.openlocfilehash: 0387cd91bd24080417a5e9763410d68b6e688555
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757500"
---
# <a name="oreo-features"></a>Funzionalità di Oreo

_Come iniziare a usare Novell. Android per sviluppare app per la versione più recente di Android._

[Android 8,0 Oreo](https://developer.android.com/index.html) è la versione più recente di Android disponibile in Google. Android Oreo offre molte nuove funzionalità interessanti per gli sviluppatori Novell. Android. Queste funzionalità includono i canali di notifica, le notifiche di notifica, i tipi di carattere personalizzati in XML, i tipi di carattere scaricabili, il riempimento automatico e l'immagine in immagini (PIP). Android Oreo include nuove API per queste nuove funzionalità e queste API sono disponibili per le app Novell. Android quando si usa Novell. Android 8,0 e versioni successive.

[![Immagine Hero Oreo Android](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

Questo articolo è strutturato per iniziare a sviluppare app Novell. Android per Android 8,0 Oreo. Viene illustrato come installare gli aggiornamenti necessari, configurare l'SDK e creare un emulatore (o dispositivo) per il test. Fornisce inoltre una descrizione delle nuove funzionalità di Android 8,0 Oreo, con collegamenti a app di esempio che illustrano come usare le funzionalità di Android Oreo nelle app Novell. Android.

## <a name="requirements"></a>Requisiti

Per usare le funzionalità di Android Oreo nelle app basate su Novell, è necessario quanto segue:

- **Visual Studio** &ndash; Se si usa Windows, è richiesta la versione 15,5 o successiva di Visual Studio.  Se si usa un Mac, è necessario Visual Studio per Mac versione 7.2.0.

- **Novell. Android** &ndash; Novell. Android 8,0 o versione successiva deve essere installato e configurato con Visual Studio.

- **Android SDK** &ndash; Android SDK 8,0 (API 26) o versione successiva deve essere installato tramite gestione Android SDK.

## <a name="getting-started"></a>Introduzione

Per iniziare a usare Android Oreo con Novell. Android, è necessario scaricare e installare gli strumenti e i pacchetti SDK più recenti prima di poter creare un progetto Android Oreo:

1. Eseguire l'aggiornamento alla versione più recente di Visual Studio.

2. Installare i pacchetti e gli strumenti di **Android 8.0.0 (API 26)** o versioni successive tramite SDK Manager.

3. Creare un nuovo progetto Novell. Android destinato a Android Oreo (API 26).

4. Configurare un emulatore o un dispositivo per testare le app di Android Oreo.

Ognuno di questi passaggi è descritto nelle sezioni seguenti:

### <a name="update-visual-studio-and-xamarinandroid"></a>Aggiornare Visual Studio e Novell. Android

Per aggiungere il supporto di Android Oreo a Visual Studio, eseguire le operazioni seguenti:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

- Per Visual Studio 2019, usare [SDK Manager](~/android/get-started/installation/android-sdk.md) per installare il livello API 26,0 o versione successiva.

- Se si usa Visual Studio 2017:

    1. Eseguire l'aggiornamento a Visual Studio 2017 versione 15,7 o successiva (vedere l' [aggiornamento di Visual studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

    2. Usare [SDK Manager](~/android/get-started/installation/android-sdk.md) per installare il livello API 26,0 o versione successiva.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

- Eseguire l'aggiornamento alla versione stabile più recente di Visual Studio per Mac come descritto in [aggiornamento di Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/update).

-----

Per altre informazioni sul supporto di Novell per Android Oreo, vedere le [Note sulla versione di Novell. Android 8,0](https://docs.microsoft.com/xamarin/android/release-notes/8/8.0/).

### <a name="install-the-android-sdk"></a>Installare il Android SDK

Per creare un progetto con Novell. Android 8,0, è necessario prima di tutto usare Novell Android SDK Manager per installare la piattaforma SDK per **Android 8,0-Oreo** o versione successiva. È necessario installare anche Android SDK Tools 26,0 o versione successiva.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Avviare SDK Manager (in Visual Studio fare clic su **strumenti > Android > Android SDK Manager**).

2. Installare i pacchetti **Android 8,0-Oreo** . Se si usa l'emulatore Android SDK, assicurarsi di includere le immagini di sistema **x86** che saranno necessarie:

    [![Selezione dei pacchetti Android 8,0 in gestione Android SDK](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Installare **Android SDK Tools 26.0.2** o versioni successive, **Android SDK Platform-Tools 26.0.0** o versione successiva e **Android SDK build-Tools 26.0.0** (o versione successiva):

    [![Selezione di Android SDK Tools 26 in gestione Android SDK](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Avviare SDK Manager (in Visual Studio per Mac fare clic su **strumenti > SDK Manager**).

2. Installare i pacchetti **Android 8,0-Oreo** SDK. Se si usa l'emulatore Android SDK, assicurarsi di includere le immagini di sistema **x86** che saranno necessarie:

    [![Selezione dei pacchetti Android 8,0 in SDK Manager](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Installare **Android SDK Tools 26.0.2** o versioni successive, **Android SDK Platform-Tools 26.0.0** o versione successiva e **Android SDK build-Tools 26.0.0** (o versione successiva):

    [![Selezione di Android SDK Tools 26 in SDK Manager](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----

### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto Novell. Android

Creare un nuovo progetto Novell. Android. Se non si ha familiarità con lo sviluppo di Android con Novell, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per altre informazioni sulla creazione di progetti Novell. Android.

Quando si crea un progetto Android, è necessario configurare le impostazioni della versione per la destinazione Android 8,0 o versione successiva. Ad esempio, per fare riferimento al progetto per Android 8,0, è necessario configurare il livello API Android di destinazione del progetto su **android 8,0 (API 26)** . Si consiglia inoltre di impostare il livello di Framework di destinazione su API 26 o versione successiva. Per altre informazioni sulla configurazione dei livelli a livello di API Android, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-an-emulator-or-device"></a>Configurare un emulatore o un dispositivo

Se si tenta di avviare il gestore AVD basato su Google GUI predefinito dopo l'installazione di Android SDK Tools 26,0 o versione successiva, è possibile che venga visualizzata la finestra di dialogo di errore seguente, che indica di usare lo strumento **avdmanager** della riga di comando AVD Manager:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Finestra di dialogo di avviso di emulatore Android Manager](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Finestra di dialogo di avviso di emulatore Android Manager](oreo-images/mac/03-avd-warning.png)

-----

Questo messaggio viene visualizzato perché Google non fornisce più un Manager autonomo di GUI AVD che supporta l'API 26,0 e versioni successive. Per Android 8,0 Oreo è necessario usare Novell emulatore Android Manager o lo strumento da riga `avdmanager` di comando per creare dispositivi virtuali per Android Oreo.

Per usare la Android Device Manager per creare e gestire i dispositivi virtuali, vedere [gestione di dispositivi virtuali con il Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).
Per creare dispositivi virtuali senza il Android Device Manager, attenersi alla procedura descritta nella sezione successiva.

#### <a name="creating-virtual-devices-using-avdmanager"></a>Creazione di dispositivi virtuali con avdmanager

Per usare **avdmanager** per creare un nuovo dispositivo virtuale, seguire questa procedura:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Aprire una finestra del prompt dei comandi `JAVA_HOME` e impostare sul percorso di Java SDK nel computer. Per un'installazione Novell tipica, è possibile usare il comando seguente:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2. Aggiungere il percorso della cartella Android SDK `bin` `PATH`al.
    Per un'installazione Novell tipica, è possibile usare il comando seguente:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3. Chiudere la finestra del prompt dei comandi e aprire una nuova finestra del prompt dei comandi. Creare un nuovo dispositivo virtuale usando il comando [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) . Ad esempio, per creare un AVD denominato **AVD-Oreo-8,0** usando l'immagine di sistema x86 per il livello API 26, usare il comando seguente:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4. Quando viene richiesto di **creare un profilo hardware personalizzato [No]** , è possibile immettere **No** e accettare il profilo hardware predefinito. Se si dice **Sì**, **avdmanager** richiederà un elenco di domande per la personalizzazione del profilo hardware.

Dopo aver **avdmanagerto** la creazione del dispositivo virtuale, questo verrà incluso nel menu a discesa del dispositivo:

[![Nuovo AVD aggiunto al menu a discesa del dispositivo](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Aprire una finestra del **terminale** e passare alla posizione della directory Android SDK Tools nel Mac. Per un'installazione Novell tipica, è possibile usare il comando seguente:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2. Creare un nuovo dispositivo virtuale usando il comando [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) . Ad esempio, per creare un AVD denominato **AVD-Oreo-8,0** usando l'immagine di sistema x86 per il livello API 26, usare il comando seguente:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3. Quando viene richiesto di **creare un profilo hardware personalizzato [No]** , è possibile immettere **No** e accettare il profilo hardware predefinito. Se si dice **Sì**, **avdmanager** richiederà un elenco di domande per la personalizzazione del profilo hardware.

Dopo aver usato **avdmanager** per creare il dispositivo virtuale, questo verrà incluso nel menu a discesa del dispositivo:

[![Nuovo AVD aggiunto al menu a discesa del dispositivo](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Per ulteriori informazioni sulla configurazione di un emulatore Android per il test e il debug, vedere [debug nella emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md).

Se si usa un dispositivo fisico, ad esempio un Nexus o un pixel, è possibile aggiornare il dispositivo tramite gli aggiornamenti automatici tramite aria (OTA) o scaricare un'immagine di sistema e lampeggiare direttamente il dispositivo. Per ulteriori informazioni sull'aggiornamento manuale del dispositivo ad Android Oreo, vedere [Factory images for Nexus and pixel Devices](https://developers.google.com/android/images).

## <a name="new-features"></a>Nuove funzionalità

Android Oreo introduce un'ampia gamma di nuove caratteristiche e funzionalità, ad esempio i canali di notifica, le notifiche di notifica, i tipi di carattere personalizzati in XML, i tipi di carattere scaricabili, il riempimento automatico e l'immagine in immagini. Le sezioni seguenti evidenziano queste funzionalità e forniscono collegamenti che consentono di iniziare a usarle nell'app.

### <a name="notification-channels"></a>Canali di notifica

I *canali di notifica* sono categorie definite dall'app per le notifiche.
È possibile creare un canale di notifica per ogni tipo di notifica che è necessario inviare ed è possibile creare canali di notifica per riflettere le scelte effettuate dagli utenti dell'app. La nuova funzionalità canali di notifica consente di fornire agli utenti un controllo con granularità fine su diversi tipi di notifiche. Se, ad esempio, si implementa un'app di messaggistica, è possibile creare canali di notifica distinti per ogni gruppo di conversazioni creato da un utente.

I [canali di notifica](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) spiegano come creare un canale di notifica e usarlo per la pubblicazione di notifiche locali. Per un esempio di codice reale, vedere l'esempio [canali](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) . Questa app di esempio gestisce due canali e imposta altre opzioni di notifica.

### <a name="notification-badges"></a>Notifiche di notifica

Le notifiche di notifica sono punti piccoli visualizzati sopra le icone dell'app, come illustrato in questo screenshot:

[![Notifiche di esempio sulle icone delle app](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Questi punti indicano che sono presenti nuove notifiche per uno o più canali di notifica nell'app associata a tale icona &ndash; dell'app. si tratta di notifiche che l'utente non ha ancora perso o che ha eseguito l'azione. Gli utenti possono premere a lungo un'icona per esaminare le notifiche associate a un badge di notifica, per la chiusura o l'azione delle notifiche dal menu di pressione prolungata che appeaars.

Per ulteriori informazioni sulle notifiche di notifica, vedere l'argomento [notifiche](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) per sviluppatori Android.

### <a name="custom-fonts-in-xml"></a>Tipi di carattere personalizzati in XML

Android Oreo introduce i *tipi di carattere in XML*, che consente di incorporare i tipi di carattere personalizzati come risorse. Sono supportati i formati OpenType ( **. otf**) e i tipi di carattere TrueType ( **. ttf**). Per aggiungere tipi di carattere come risorse, procedere come segue:

1. Creare una cartella **Resources/font** .

2. Copiare i file dei tipi di carattere, ad esempio file con **estensione ttf** e **OTF** , in **risorse/tipo di carattere**. 

3. Se necessario, rinominare i file del tipo di carattere in modo che siano conformi alle convenzioni di denominazione dei file Android, ovvero usare solo lettere minuscole *a-z*, *0-9*e caratteri di sottolineatura nei nomi dei file. Il file `Pacifico-Regular.ttf` del tipo di carattere, ad esempio, può essere rinominato `pacifico.ttf`come.

4. Applicare il tipo di carattere personalizzato usando il `android:fontFamily` nuovo attributo nel codice XML del layout. Ad esempio, la Dichiarazione `TextView` seguente usa la risorsa del tipo di carattere **Pacifico. ttf** aggiunta:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

È anche possibile creare un file XML della famiglia di caratteri che descriva più tipi di carattere, oltre a dettagli relativi allo stile e al peso. Per ulteriori informazioni, vedere l'argomento Android Developer [Fonts in XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) .

### <a name="downloadable-fonts"></a>Tipi di carattere scaricabili

A partire da Android Oreo, le app possono richiedere tipi di carattere da un provider anziché aggregarli nell'APK. I tipi di carattere vengono scaricati dalla rete solo se necessario. Questa funzionalità riduce le dimensioni APK, mantenendo la memoria del telefono e l'utilizzo dei dati cellulari. È anche possibile usare questa funzionalità nelle versioni 14 e successive dell'API Android installando il pacchetto della libreria di supporto Android 26.

Quando l'app necessita di un tipo di carattere, `FontsRequest` si crea un oggetto (specificando il tipo di carattere da scaricare) `FontsContract` e quindi lo si passa a un metodo per scaricare il tipo di carattere. I passaggi seguenti descrivono in modo più dettagliato il processo di download del tipo di carattere:

1. Creare un'istanza di un oggetto [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) . 

2. Sottoclasse e creare un'istanza di [FontsContract. FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3. Implementare il metodo [FontRequestCallback. OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) , usato per gestire il completamento della richiesta del tipo di carattere.

4. Implementare il metodo [FontRequestCallback. OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) , usato per informare l'app degli eventuali errori che si verificano durante il processo di richiesta del tipo di carattere.

5. Chiamare il metodo [FontsContract. RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) per recuperare il tipo di carattere dal provider del tipo di carattere. 

Quando si chiama il `RequestFonts` metodo, verifica innanzitutto se il tipo di carattere è memorizzato nella cache locale (da una chiamata precedente `RequestFont`a). Se non viene memorizzato nella cache, chiama il provider del tipo di carattere, recupera il tipo di carattere in modo asincrono e quindi passa i risultati all'app richiamando il `OnTypeFaceRetrieved` metodo.

L'esempio di [tipi di carattere scaricabili](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) illustra come usare la funzionalità per i tipi di carattere scaricabile introdotta in Android Oreo. 

Per altre informazioni sul download di tipi di carattere, vedere l'argomento relativo ai [tipi di carattere scaricabili](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) per sviluppatori Android.

### <a name="autofill"></a>Autofill

Il nuovo Framework di _riempimento automatico_ in Android Oreo rende più semplice per gli utenti gestire attività ripetitive, ad esempio account di accesso, creazione di account e transazioni con carta di credito. Gli utenti dedicano meno tempo alla ridigitazione delle informazioni (che possono causare errori di input). Prima che l'app possa funzionare con il Framework di riempimento automatico, è necessario abilitare un servizio di riempimento automatico nelle impostazioni di sistema (gli utenti possono abilitare o disabilitare il riempimento automatico).

L'esempio [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) illustra l'uso del Framework di riempimento automatico. Sono incluse le implementazioni delle attività client con le visualizzazioni che devono essere riempite automaticamente e un servizio in grado di fornire dati di riempimento automatico alle attività del client.

Per altre informazioni sulla nuova funzionalità di riempimento automatico e su come ottimizzare l'app per il riempimento automatico, vedere l'argomento relativo al Framework per la [compilazione automatica](https://developer.android.com/guide/topics/text/autofill.html) per sviluppatori Android.

### <a name="picture-in-picture-pip"></a>Immagine in immagine (PIP)

Android Oreo rende possibile l'avvio di un'attività in modalità Picture-in-Picture (PIP), sovraponendo la schermata di un'altra attività. Questa funzionalità è destinata alla riproduzione video.

Per specificare che l'attività dell'app può usare la modalità PIP, impostare il flag seguente su true nel manifesto Android:

```xml
android:supportsPictureInPicture
```

Per specificare il comportamento dell'attività in modalità PIP, utilizzare il nuovo oggetto [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html) . `PictureInPictureParams`rappresenta un set di parametri utilizzati per inizializzare e aggiornare un'attività in modalità PIP, ad esempio le proporzioni preferite dell'attività. Sono stati aggiunti i nuovi metodi PIP seguenti `Activity` a in Android Oreo:

- [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; inserisce l'attività in modalità PIP. L'attività viene posizionata nell'angolo dello schermo e il resto dello schermo viene riempito con l'attività precedente visualizzata sullo schermo.

- [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; Aggiorna le impostazioni di configurazione PIP dell'attività, ad esempio una modifica nelle proporzioni.

L'esempio [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) illustra l'uso di base della modalità PIP (immagine in immagine) per i dispositivi palmari introdotti in Oreo. Nell'esempio viene riprodotto un video che continua senza interruzioni durante lo spostamento tra le modalità di visualizzazione o altre attività.

### <a name="other-features"></a>Altre funzionalità

Android Oreo contiene molte altre nuove funzionalità, ad esempio la libreria di supporto emoji, l'API location, i limiti di sfondo, il colore Wide-Gamut per le app, i nuovi codec audio, i miglioramenti di WebView, il supporto migliorato per la navigazione da tastiera e una nuova API AAudio (Pro audio) per audio a bassa latenza e prestazioni elevate. per altre informazioni su queste funzionalità, vedere l'argomento relativo alle [funzionalità e alle API](https://developer.android.com/about/versions/oreo/android-8.0.html) per Android Developer Android Oreo.

## <a name="behavior-changes"></a>Modifiche del comportamento

Android Oreo include diverse modifiche del comportamento di sistema e API che possono avere un effetto sulla funzionalità delle app esistenti. Queste modifiche sono descritte di seguito.

### <a name="background-execution-limits"></a>Limiti di esecuzione in background

Per migliorare l'esperienza utente, Android Oreo impone limitazioni sulle app che possono essere eseguite in background. Ad esempio, se l'utente guarda un video o gioca una partita, un'app in esecuzione in background può compromettere le prestazioni di un'app a elevato utilizzo di video in esecuzione in primo piano. Di conseguenza, Android Oreo pone le restrizioni seguenti per le app che non interagiscono direttamente con l'utente:

1. **Limitazioni del servizio in background** &ndash; Quando un'app viene eseguita in background, presenta una finestra di diversi minuti in cui è ancora possibile creare e usare i servizi. Alla fine di tale finestra, Android arresta il servizio in background dell'app e lo considera _inattivo_.

2. **Limitazioni broadcast** &ndash; Android 7,0 (API 25) ha inserito limitazioni sulle trasmissioni che un'app registra per ricevere. Android Oreo rende più rigorose queste limitazioni. Ad esempio, le app Android Oreo non possono più registrare ricevitori broadcast per trasmissioni implicite nei propri manifesti.

Per ulteriori informazioni sui nuovi limiti di esecuzione in background, vedere l'argomento relativo ai [limiti di esecuzione in background](https://developer.android.com/about/versions/oreo/background.html) per sviluppatori Android.

### <a name="breaking-changes"></a>Modifiche di interruzione

Le app destinate a Android Oreo o versioni successive devono modificare le proprie app in modo da supportare le seguenti modifiche, ove applicabile:

- Android Oreo depreca la possibilità di impostare la priorità delle singole notifiche. Si imposta invece un livello di importanza consigliato durante la creazione di un canale di notifica. Il livello di importanza assegnato a un canale di notifica si applica a tutti i messaggi di notifica inviati.

- Per le app destinate a Android `PendingIntent.GetService()` Oreo, non funziona a causa dei nuovi limiti posti sui servizi avviati in background. Se la destinazione è Android Oreo, usare invece [PendingIntent. getbroadcast](xref:Android.App.PendingIntent.GetBroadcast*) .  

## <a name="sample-code"></a>Codice di esempio

Sono disponibili diversi esempi di Novell. Android per illustrare come sfruttare le funzionalità di Android Oreo:

- [NotificationsChannels](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) illustra come usare il nuovo sistema di canali di notifica introdotto in Android Oreo. Questo esempio gestisce due canali di notifica: uno con priorità predefinita e l'altro con priorità elevata.

- [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) illustra l'uso di base della modalità PIP (immagine in immagine) per i dispositivi palmari introdotti in Oreo. Nell'esempio viene riprodotto un video che continua senza interruzioni durante lo spostamento tra le modalità di visualizzazione o altre attività.

- [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) illustra l'uso del Framework di riempimento automatico. Sono incluse le implementazioni delle attività client con le visualizzazioni che devono essere riempite automaticamente e un servizio in grado di fornire dati di riempimento automatico alle attività del client.

- I [tipi di carattere scaricabili](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) forniscono un esempio di come usare la funzionalità dei tipi di carattere scaricabile descritta in precedenza.

- [EmojiCompat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-emojicompat) illustra l'uso della libreria di supporto EmojiCompat. È possibile usare questa libreria per impedire che l'app mostri caratteri emoji mancanti come caratteri "tofu".

- [Gli aggiornamenti della posizione in sospeso](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdpendintent) illustrano l'uso dell'API location per ottenere gli aggiornamenti sulla posizione di un dispositivo `PendingIntent`usando un.

- [Location Updates Foreground Service](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdfgservice) illustra come usare l'API location per ottenere gli aggiornamenti sulla posizione di un dispositivo usando un servizio in primo piano associato e avviato.

## <a name="video"></a>Video

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Sviluppo di Android 8,0 Oreo conC#**

## <a name="summary"></a>Riepilogo

Questo articolo ha presentato Android Oreo e spiega come installare e configurare gli strumenti e i pacchetti più recenti per lo sviluppo di Novell. Android in Android Oreo. Fornisce una panoramica delle funzionalità principali disponibili in Android Oreo, con collegamenti ad esempi di codice sorgente per alcune nuove funzionalità. Sono inclusi collegamenti alla documentazione API e agli argomenti per sviluppatori Android che consentono di iniziare a creare app per Android Oreo. Sono state inoltre evidenziate le modifiche più importanti del comportamento di Android Oreo che potrebbero influito sulle app esistenti.

## <a name="related-links"></a>Collegamenti correlati

- [Android 8,0 Oreo](https://developer.android.com/index.html)
