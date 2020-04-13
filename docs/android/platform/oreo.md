---
title: Caratteristiche di Oreo
description: Come iniziare a usare Xamarin.Android per sviluppare app per l'ultima versione di Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: davidortinau
ms.author: daortin
ms.date: 07/06/2018
ms.openlocfilehash: 56430f8c4988c16a31f9806b0ffb8b6355d6340b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020004"
---
# <a name="oreo-features"></a>Caratteristiche di Oreo

_Come iniziare a usare Xamarin.Android per sviluppare app per l'ultima versione di Android._

[Android 8.0 Oreo](https://developer.android.com/index.html) è l'ultima versione di Android disponibile da Google. Android Oreo offre molte nuove funzionalità di interesse per gli sviluppatori Xamarin.Android. Queste funzionalità includono canali di notifica, badge di notifica, font personalizzati in XML, tipi di carattere scaricabili, riempimento automatico e PICTURE in picture (PIP). Android Oreo include nuove API per queste nuove funzionalità e queste API sono disponibili per le app Xamarin.Android quando usi Xamarin.Android 8.0 e versioni successive.

[![Immagine dell'eroe di Android Oreo](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

Questo articolo è strutturato per aiutarti a iniziare a sviluppare app Xamarin.Android per Android 8.0 Oreo. Viene illustrato come installare gli aggiornamenti necessari, configurare l'SDK e creare un emulatore (o dispositivo) per il test. Fornisce anche una struttura delle nuove funzionalità di Android 8.0 Oreo, con collegamenti ad app di esempio che illustrano come usare le funzionalità di Android Oreo nelle app Xamarin.Android.

## <a name="requirements"></a>Requisiti

Per utilizzare le funzionalità di Android Oreo nelle app basate su Xamarin è necessario quanto segue:

- **Visual Studio** &ndash; Se si utilizza Windows, è necessaria la versione 15.5 o successiva di Visual Studio.  Se si usa un Mac, è necessario Visual Studio per Mac versione 7.2.0.

- **Xamarin.Android** &ndash; Xamarin.Android 8.0 o versione successiva deve essere installato e configurato con Visual Studio.

- **Android SDK** &ndash; Android SDK 8.0 (API 26) o versione successiva deve essere installato tramite Android SDK Manager.

## <a name="getting-started"></a>Introduzione

Per iniziare a usare Android Oreo con Xamarin.Android, devi scaricare e installare gli strumenti e i pacchetti SDK più recenti prima di poter creare un progetto Android Oreo:

1. Eseguire l'aggiornamento alla versione più recente di Visual Studio.

2. Installare i pacchetti e gli strumenti **di Android 8.0.0 (API 26)** o versioni successive tramite Gestione SDK.

3. Creare un nuovo progetto Xamarin.Android destinato a Android Oreo (API 26).

4. Configurare un emulatore o un dispositivo per testare le app Android Oreo.

Ognuno di questi passaggi è illustrato nelle sezioni seguenti:Each of these steps is explained in the following sections:

### <a name="update-visual-studio-and-xamarinandroid"></a>Aggiornare Visual Studio e Xamarin.Android

Per aggiungere il supporto Android Oreo a Visual Studio, eseguire le operazioni seguenti:To add Android Oreo support to Visual Studio, do the following:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

- Per Visual Studio 2019, usare [Gestione SDK](~/android/get-started/installation/android-sdk.md) per installare il livello API 26.0 o versione successiva.

- Se si utilizza Visual Studio 2017:

    1. Aggiornamento a Visual Studio 2017 versione 15.7 o successiva (vedere Aggiornamento di [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

    2. Utilizzare [Gestione SDK](~/android/get-started/installation/android-sdk.md) per installare il livello API 26.0 o versione successiva.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

- Eseguire l'aggiornamento all'ultima versione stabile di Visual Studio per Mac, come illustrato in Aggiornamento di [Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/update).

-----

Per ulteriori informazioni sul supporto di Xamarin per Android Oreo, vedere le note sulla versione di [Xamarin.Android 8.0](https://docs.microsoft.com/xamarin/android/release-notes/8/8.0/).

### <a name="install-the-android-sdk"></a>Installare Android SDK

Per creare un progetto con Xamarin.Android 8.0, devi prima usare Xamarin Android SDK Manager per installare la piattaforma SDK per **Android 8.0 - Oreo** o versioni successive. È inoltre necessario installare Android SDK Tools 26.0 o versione successiva.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Avviare Gestione SDK (in Visual Studio, fare clic su **Strumenti > Android > Android SDK Manager**).

2. Installare i pacchetti **Android 8.0 - Oreo.** Se si utilizza l'emulatore Android SDK, assicurarsi di includere le immagini di sistema **x86** necessarie:

    [![Selezione dei pacchetti Android 8.0 in Android SDK Manager](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Installare **Android SDK Tools 26.0.2** o versione successiva, Android SDK **Platform-Tools 26.0.0** o versione successiva e **Android SDK Build-Tools 26.0.0** (o versione successiva):

    [![Selezione di Android SDK Tools 26 in Android SDK Manager](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

1. Avviare Gestione SDK (in Visual Studio per Mac, fare clic su **Strumenti > Gestione SDK**).

2. Installare i pacchetti **Android 8.0 - Oreo** SDK. Se si utilizza l'emulatore Android SDK, assicurarsi di includere le immagini di sistema **x86** necessarie:

    [![Selezione dei pacchetti Android 8.0 in Gestione SDK](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Installare **Android SDK Tools 26.0.2** o versione successiva, Android SDK **Platform-Tools 26.0.0** o versione successiva e **Android SDK Build-Tools 26.0.0** (o versione successiva):

    [![Selezione di Android SDK Tools 26 in Gestione SDK](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----

### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto Xamarin.Android

Creare un nuovo progetto Xamarin.Android.Create a new Xamarin.Android project. Se non si ha familiarità con lo sviluppo Android con Xamarin, vedere Hello, Android per informazioni sulla creazione di progetti Xamarin.Android.If you are new to Android development with Xamarin, see [Hello, Android](~/android/get-started/hello-android/index.md) to learn about creating Xamarin.Android projects.

Quando crei un progetto Android, devi configurare le impostazioni della versione per Android 8.0 o versioni successive. Ad esempio, per impostare come destinazione il progetto per Android 8.0, è necessario configurare il livello API Android di destinazione del progetto in **Android 8.0 (API 26).** È consigliabile impostare anche il livello del framework di destinazione su API 26 o versione successiva. Per altre informazioni sulla configurazione dei livelli di API Android, vedere [Informazioni sui livelli](~/android/app-fundamentals/android-api-levels.md)di API Android .

### <a name="configure-an-emulator-or-device"></a>Configurare un emulatore o un dispositivo

Se si tenta di avviare il gestore AVD basato su GOOGLE GUI predefinito dopo l'installazione di Android SDK Tools 26.0 o versione successiva, è possibile che venga visualizzato il seguente messaggio di dialogo di errore, che indica di utilizzare lo strumento avD manager della riga di comando **avdmanager:**

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Finestra di dialogo di avviso di Gestione emulatori Android](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

![Finestra di dialogo di avviso di Gestione emulatori Android](oreo-images/mac/03-avd-warning.png)

-----

Questo messaggio viene visualizzato perché Google non fornisce più un gestore AVD GUI autonomo che supporta API 26.0 e versioni successive. Per Android 8.0 Oreo, è necessario utilizzare Xamarin Android `avdmanager` Emulator Manager o lo strumento della riga di comando per creare dispositivi virtuali per Android Oreo.

Per utilizzare Gestione dispositivi Android per creare e gestire dispositivi virtuali, vedere [Gestione di dispositivi virtuali con Gestione dispositivi Android.](~/android/get-started/installation/android-emulator/device-manager.md)
Per creare dispositivi virtuali senza Gestione dispositivi Android, seguire i passaggi nella sezione successiva.

#### <a name="creating-virtual-devices-using-avdmanager"></a>Creazione di dispositivi virtuali mediante avdmanagerCreating Virtual Devices Using avdmanager

Per utilizzare **avdmanager** per creare un nuovo dispositivo virtuale, attenersi alla seguente procedura:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Aprire una finestra del `JAVA_HOME` prompt dei comandi e impostare il percorso dell'SDK Java nel computer. Per una tipica installazione di Xamarin, è possibile utilizzare il seguente comando:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2. Aggiungere il percorso della `bin` cartella `PATH`Android SDK al file .
    Per una tipica installazione di Xamarin, è possibile utilizzare il seguente comando:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3. Chiudere la finestra del prompt dei comandi e aprire una nuova finestra del prompt dei comandi. Creare un nuovo dispositivo virtuale utilizzando il comando [avdmanager.](https://developer.android.com/studio/command-line/avdmanager.html) Ad esempio, per creare un AVD denominato **AVD-Oreo-8.0** utilizzando l'immagine del sistema x86 per il livello API 26, utilizzare il comando seguente:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4. Quando viene richiesto con **Si desidera creare un profilo hardware personalizzato [no]** è possibile immettere **no** e accettare il profilo hardware predefinito. Se dici **sì**, **avdmanager** ti chiederà un elenco di domande per personalizzare il profilo hardware.

Dopo aver creato il dispositivo virtuale, **avdmanager** verrà incluso nel menu a discesa del dispositivo:

[![Nuovo AVD aggiunto al menu a discesa del dispositivo](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

1. Apri una finestra **Terminale** e passa alla posizione della directory degli strumenti di Android SDK sul Mac. Per una tipica installazione di Xamarin, è possibile utilizzare il seguente comando:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2. Creare un nuovo dispositivo virtuale utilizzando il comando [avdmanager.](https://developer.android.com/studio/command-line/avdmanager.html) Ad esempio, per creare un AVD denominato **AVD-Oreo-8.0** utilizzando l'immagine del sistema x86 per il livello API 26, utilizzare il comando seguente:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3. Quando viene richiesto con **Si desidera creare un profilo hardware personalizzato [no]** è possibile immettere **no** e accettare il profilo hardware predefinito. Se dici **sì**, **avdmanager** ti chiederà un elenco di domande per personalizzare il profilo hardware.

Dopo aver utilizzato **avdmanager** per creare il dispositivo virtuale, questo verrà incluso nel menu a discesa del dispositivo:

[![Nuovo AVD aggiunto al menu a discesa del dispositivo](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Per ulteriori informazioni sulla configurazione di un emulatore Android per il test e il debug, vedere [Debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md).

Se si utilizza un dispositivo fisico, ad esempio un Nexus o un pixel, è possibile aggiornare il dispositivo tramite aggiornamenti automatici via etere (OTA) o scaricare un'immagine del sistema e lampeggiare direttamente il dispositivo. Per ulteriori informazioni sull'aggiornamento manuale del dispositivo ad Android Oreo, consultate Immagini di [fabbrica per Nexus e dispositivi Pixel.](https://developers.google.com/android/images)

## <a name="new-features"></a>Nuove funzionalità

Android Oreo introduce una serie di nuove funzionalità e funzionalità, come canali di notifica, badge di notifica, tipi di carattere personalizzati in XML, tipi di carattere scaricabili, riempimento automatico e picture-in-picture. Le sezioni seguenti evidenziano queste funzionalità e forniscono collegamenti per iniziare a usarle nell'app.

### <a name="notification-channels"></a>Canali di notifica

*I canali di notifica* sono categorie definite dall'app per le notifiche.
Puoi creare un canale di notifica per ogni tipo di notifica che devi inviare e puoi creare canali di notifica per riflettere le scelte effettuate dagli utenti della tua app. La nuova funzionalità dei canali di notifica consente di fornire agli utenti un controllo granulare su diversi tipi di notifiche. Ad esempio, se si implementa un'app di messaggistica, è possibile creare canali di notifica separati per ogni gruppo di conversazione creato da un utente.

[I canali](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) di notifica spiegano come creare un canale di notifica e utilizzarlo per la pubblicazione di notifiche locali. Per un esempio di codice reale, vedere il [NotificationChannels](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) esempio; questa app di esempio gestisce due canali e imposta opzioni di notifica aggiuntive.

### <a name="notification-badges"></a>Badge di notifica

I badge di notifica sono piccoli punti che vengono visualizzati sulle icone dell'app, come mostrato in questa schermata:Notification badges are small dots that appear over app icons as shown in this screenshot:

[![Badge di notifica di esempio sulle icone dell'app](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Questi punti indicano che sono presenti nuove notifiche per uno o più &ndash; canali di notifica nell'app associati all'icona dell'app, ovvero notifiche che l'utente non ha ancora chiuso o su cui ha agito. Gli utenti possono premere a lungo su un'icona per dare un'occhiata alle notifiche associate a un badge di notifica, ignorando o agendo sulle notifiche dal menu a pressione prolungata che appeaars.

Per altre informazioni sui badge di notifica, vedere l'argomento Badge di notifica per sviluppatori Android.For more information about notification [badges,](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) see the Android Developer Notification Badges topic.

### <a name="custom-fonts-in-xml"></a>Tipi di carattere personalizzati in XMLCustom Fonts in XML

Android Oreo introduce *i font in XML*, che consente di incorporare i tipi di carattere personalizzati come risorse. Sono supportati i formati dei tipi di carattere OpenType (**.otf**) e TrueType (**.ttf**). Per aggiungere tipi di carattere come risorse, eseguire le operazioni seguenti:

1. Creare una cartella **Risorse/Font.**

2. Copiare i file dei tipi di carattere (ad esempio, i file **con estensione ttf** e **otf)** in **Risorse/font**. 

3. Se necessario, rinominare ogni file di font in modo che aderisca alle convenzioni di denominazione dei file Android (ad esempio, utilizzare solo *a-z*minuscola , *0-9*e caratteri di sottolineatura nei nomi di file). Ad esempio, il `Pacifico-Regular.ttf` file del tipo `pacifico.ttf`di carattere potrebbe essere rinominato in qualcosa di simile a .

4. Applicare il tipo di `android:fontFamily` carattere personalizzato utilizzando il nuovo attributo nell'XML del layout. Ad esempio, `TextView` la seguente dichiarazione utilizza la risorsa carattere **pacifico.ttf** aggiunta:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

È inoltre possibile creare un file XML della famiglia di caratteri che descrive più tipi di carattere, nonché i dettagli relativi a stile e spessore. Per altre informazioni, vedere l'argomento Tipi di carattere per sviluppatori Android [in XML.For](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) more information, see the Android Developer Fonts in XML topic.

### <a name="downloadable-fonts"></a>Tipi di carattere scaricabili

A partire da Android Oreo, le app possono richiedere font da un provider anziché raggrupparli nell'APK. I font vengono scaricati dalla rete solo se necessario. Questa funzione riduce le dimensioni dell'APK, conservando la memoria del telefono e l'utilizzo della rete dati. È anche possibile usare questa funzionalità nelle versioni api Android 14 e successive installando il pacchetto Android Support Library 26.You can also use this feature on Android API versions 14 and higher by installing the Android Support Library 26 package.

Quando l'app necessita di `FontsRequest` un tipo di carattere, crei un `FontsContract` oggetto (specificando il tipo di carattere da scaricare) e quindi lo passi a un metodo per scaricarlo. I passaggi seguenti descrivono il processo di download dei tipi di carattere in modo più dettagliato:The following steps describe the font download process in more detail:

1. Creare un'istanza di un [fontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) oggetto. 

2. Sottoclasse e creare un'istanza di [FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3. Implementare il metodo [FontRequestCallback.OnTypeFaceRetrieved,](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) utilizzato per gestire il completamento della richiesta del tipo di carattere.

4. Implementare il metodo [FontRequestCallback.OnTypeFaceRequestFailed,](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) che viene usato per informare l'app di eventuali errori che si sono verificati durante il processo di richiesta del tipo di carattere.

5. Chiamare il [FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) metodo per recuperare il tipo di carattere dal provider di tipi di carattere. 

Quando si `RequestFonts` chiama il metodo , viene innanzitutto verificato se il `RequestFont`tipo di carattere è memorizzato nella cache locale (da una precedente chiamata a ). Se non è memorizzato nella cache, chiama il provider di tipi di carattere, recupera `OnTypeFaceRetrieved` il tipo di carattere in modo asincrono e quindi passa i risultati all'app richiamando il metodo.

Nell'esempio di tipi di [carattere scaricabili](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) viene illustrato come utilizzare la funzionalità dei tipi di carattere scaricabili introdotta in Android Oreo. 

Per altre informazioni sul download dei tipi di carattere, vedere l'argomento Tipi di carattere scaricabili per sviluppatori Android.For more information about downloading fonts, see the Android Developer [Downloadable Fonts](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) topic.

### <a name="autofill"></a>Riempimento automatico

Il nuovo framework _di riempimento automatico_ in Android Oreo rende più facile per gli utenti gestire attività ripetitive come login, creazione di account e transazioni con carta di credito. Gli utenti trascorrono meno tempo a ridigitare le informazioni (che possono causare errori di input). Prima che l'app possa funzionare con Autofill Framework, è necessario abilitare un servizio di riempimento automatico nelle impostazioni di sistema (gli utenti possono abilitare o disabilitare il riempimento automatico).

Nell'esempio [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) viene illustrato l'utilizzo di Autofill Framework. Include implementazioni di attività client con visualizzazioni che devono essere compilate automaticamente e un servizio in grado di fornire dati di riempimento automatico alle attività client.

Per altre informazioni sulla nuova funzionalità di riempimento automatico e su come ottimizzare l'app per il riempimento automatico, vedi l'argomento [Framework di riempimento automatico](https://developer.android.com/guide/topics/text/autofill.html) per sviluppatori Android.For more information about the new Autofill feature and how to optimize your app for autofill, see the Android Developer Autofill Framework topic.

### <a name="picture-in-picture-pip"></a>Picture in Picture (PIP)

Android Oreo rende possibile l'avvio di un'attività in modalità PICTURE-in-picture (PIP), sovrapponendo lo schermo di un'altra attività. Questa funzione è destinata alla riproduzione video.

Per specificare che l'attività dell'app può usare la modalità PIP, imposta il flag seguente su true nel manifesto Android:

```xml
android:supportsPictureInPicture
```

Per specificare il funzionamento dell'attività quando è in modalità PIP, utilizzare il nuovo oggetto [PictureInPictureParams.](https://developer.android.com/reference/android/app/PictureInPictureParams.html) `PictureInPictureParams`rappresenta un set di parametri che consente di inizializzare e aggiornare un'attività in modalità PIP (ad esempio, le proporzioni preferite dell'attività). I seguenti nuovi metodi `Activity` PIP sono stati aggiunti a in Android Oreo:

- [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; inserisce l'attività in modalità PIP. L'attività viene posizionata nell'angolo dello schermo e il resto dello schermo viene riempito con l'attività precedente che si trovava sullo schermo.

- [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; Aggiorna le impostazioni di configurazione PIP dell'attività (ad esempio, una modifica delle proporzioni).

Nell'esempio [Picture InPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) viene illustrato l'utilizzo di base della modalità Picture-in-Picture (PiP) per i dispositivi palmari introdotti in Oreo. L'esempio riproduce un video che continua ininterrottamente durante il passaggio avanti e indietro tra le modalità di visualizzazione o altre attività.

### <a name="other-features"></a>Altre funzionalità

Android Oreo contiene molte altre nuove funzionalità come la libreria di supporto Emoji, Location API, limiti di sfondo, colore wide-gamut per le applicazioni, nuovi codec audio, miglioramenti di WebView, supporto di navigazione da tastiera migliorato e una nuova API AAudio (pro audio) per audio ad alta latenza ad alte prestazioni, Per ulteriori informazioni su queste funzionalità, vedere l'argomento Android Developer [Android Oreo Features and APIs.](https://developer.android.com/about/versions/oreo/android-8.0.html)

## <a name="behavior-changes"></a>Differenze di funzionamento

Android Oreo include una varietà di modifiche al comportamento del sistema e delle API che possono avere un impatto sulle funzionalità delle app esistenti. Queste modifiche sono descritte di seguito.

### <a name="background-execution-limits"></a>Limiti di esecuzione in background

Per migliorare l'esperienza utente, Android Oreo impone limitazioni su ciò che le applicazioni possono fare durante l'esecuzione in background. Ad esempio, se l'utente sta guardando un video o giocando a un gioco, un'app in esecuzione in background potrebbe compromettere le prestazioni di un'app con uso intensivo di video in esecuzione in primo piano. Di conseguenza, Android Oreo pone le seguenti restrizioni sulle app che non interagiscono direttamente con l'utente:

1. **Limitazioni** &ndash; del servizio in background Quando un'app è in esecuzione in background, ha una finestra di diversi minuti in cui è ancora consentito creare e usare i servizi. Alla fine di tale finestra, Android arresta il servizio in background dell'app e lo considera come _inattivo._

2. **Limitazioni di trasmissione** &ndash; Android 7.0 (API 25) ha posto limitazioni sulle trasmissioni che un'app registra per ricevere. Android Oreo rende queste limitazioni più severe. Ad esempio, le app Android Oreo non possono più registrare i ricevitori di trasmissione per le trasmissioni implicite nei manifesti.

Per altre informazioni sui nuovi limiti di esecuzione in background, vedere l'argomento Android Developer Background Execution Limits.For more information about the new background execution [limits,](https://developer.android.com/about/versions/oreo/background.html) see the Android Developer Background Execution Limits topic.

### <a name="breaking-changes"></a>Modifiche di rilievo

Le app destinate a Android Oreo o versione successiva devono modificare le app per supportare le modifiche seguenti, se applicabile:

- Android Oreo depreca la possibilità di impostare la priorità delle singole notifiche. Al contrario, si imposta un livello di importanza consigliato durante la creazione di un canale di notifica. Il livello di importanza assegnato a un canale di notifica si applica a tutti i messaggi di notifica che pubblichi su di esso.

- Per le app destinate `PendingIntent.GetService()` ad Android Oreo, non funziona a causa dei nuovi limiti imposti ai servizi avviati in background. Se si mira Android Oreo, è necessario utilizzare [PendingIntent.GetBroadcast](xref:Android.App.PendingIntent.GetBroadcast*) invece.  

## <a name="sample-code"></a>Codice di esempio

Diversi campioni Xamarin.Android sono disponibili per mostrarti come sfruttare le funzionalità di Android Oreo:

- [NotificationsChannels](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) viene illustrato come utilizzare il nuovo sistema di canali di notifica introdotto in Android Oreo. In questo esempio vengono gestiti due canali di notifica: uno con priorità predefinita e l'altro con priorità alta.

- [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) illustra l'utilizzo di base della modalità Picture-in-Picture (PiP) per i dispositivi palmari introdotti in Oreo. L'esempio riproduce un video che continua ininterrottamente durante il passaggio avanti e indietro tra le modalità di visualizzazione o altre attività.

- [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) dimostra l'uso di Autofill Framework. Include implementazioni di attività client con visualizzazioni che devono essere compilate automaticamente e un servizio in grado di fornire dati di riempimento automatico alle attività client.

- [Font scaricabili](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) fornisce un esempio di come utilizzare la funzionalità Font scaricabili descritta in precedenza.

- [EmojiCompat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-emojicompat) illustra l'utilizzo della libreria di supporto EmojiCompat. Puoi usare questa libreria per impedire all'app di mostrare i caratteri emoji mancanti come caratteri "tofu".

- [Location Updates Pending Intent](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdpendintent) illustra l'utilizzo dell'API Location per `PendingIntent`ottenere aggiornamenti sulla posizione di un dispositivo tramite un file .

- [Il servizio di primo piano degli aggiornamenti di posizione](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdfgservice) illustra come usare l'API Location per ottenere aggiornamenti sulla posizione di un dispositivo usando un servizio in primo piano associato e avviato.

## <a name="video"></a>Video

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Android 8.0 Sviluppo Oreo con C #**

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto Android Oreo e ha spiegato come installare e configurare gli strumenti e i pacchetti più recenti per lo sviluppo di Xamarin.Android su Android Oreo. Ha fornito una panoramica delle funzionalità principali disponibili in Android Oreo, con collegamenti al codice sorgente di esempio per diverse nuove funzionalità. Includeva collegamenti alla documentazione dell'API e agli argomenti di Android Developer per aiutarti a iniziare a creare app per Android Oreo. Ha anche evidenziato le più importanti modifiche al comportamento di Android Oreo che potrebbero influire sulle app esistenti.

## <a name="related-links"></a>Collegamenti correlati

- [Android 8.0 Oreo](https://developer.android.com/index.html)
