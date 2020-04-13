---
title: Android 9 Pie
description: Come iniziare a sviluppare app per Android 9 Pie usando Xamarin.Android.
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 0105b43116df697bc6688becb77298c236dfa601
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019879"
---
# <a name="android-pie-features"></a>Caratteristiche di Android Pie

_Come iniziare a sviluppare app per Android 9 Pie usando Xamarin.Android._

[Android 9 Pie](https://developer.android.com/about/versions/pie/) è ora disponibile da Google. Un certo numero di nuove funzionalità e API sono stati resi disponibili in questa versione, e molti di loro sono necessari per sfruttare le nuove funzionalità hardware nei dispositivi Android più recenti.

![Immagine dell'eroe di Android Pie](pie-images/01-android-p-logo.png)

Questo articolo è strutturato per aiutarti a iniziare a sviluppare app Xamarin.Android per Android Pie. Viene illustrato come installare gli aggiornamenti necessari, configurare l'SDK e preparare un emulatore o un dispositivo per il test. Fornisce inoltre una struttura delle nuove funzionalità di Android Pie e fornisce codice sorgente di esempio che illustra come usare alcune delle funzionalità principali di Android Pie.

Xamarin.Android 9.0 fornisce il supporto per Android Pie. Per altre informazioni sul supporto di Xamarin.Android per Android Pie, vedere le note sulla versione di [Android P Developer Preview 3.For](https://docs.microsoft.com/xamarin/android/release-notes/9/9.0/#android-p-dp1) more information about Xamarin.Android support for Android Pie, see the Android P Developer Preview 3 release notes.

## <a name="requirements"></a>Requisiti

L'elenco seguente è necessario per utilizzare le funzionalità di Android Pie nelle app basate su Xamarin:

- **È** &ndash; consigliabile Visual Studio Visual Studio 2019.
    Se si utilizza Visual Studio 2017, in Windows Update a Visual Studio 2017 versione 15.8 o successiva. In macOS, eseguire l'aggiornamento a Visual Studio 2017 per Mac versione 7.6 o successiva.

- **Xamarin.Android** &ndash; Xamarin.Android 9.0.0.17 o versione successiva deve essere installato con Visual Studio (Xamarin.Android viene installato automaticamente come parte dello sviluppo mobile con carico di lavoro **.NET).**

- **Lo** &ndash; sviluppo di Java Developer Kit Xamarin Android 9.0 richiede [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (oppure puoi provare l'anteprima della distribuzione di Microsoft di [OpenJDK](~/android/get-started/installation/openjdk.md)). JDK8 viene installato automaticamente come parte dello sviluppo mobile con carico di lavoro **.NET.**

- **Android SDK** &ndash; SDK SDK API 28 o versione successiva deve essere installato tramite Android SDK Manager.

## <a name="getting-started"></a>Introduzione

Per iniziare a sviluppare app Per Android Pie con Xamarin.Android, devi scaricare e installare gli strumenti e i pacchetti SDK più recenti prima di poter creare il tuo primo progetto Android Pie:

1. È consigliato Visual Studio 2019. Se si utilizza Visual Studio 2017, eseguire l'aggiornamento a [Visual Studio 2017 versione 15.8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) o successiva. Se si usa Visual Studio per Mac, eseguire l'aggiornamento a [Visual Studio 2017 per Mac versione 7.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) o successiva.

2. Installare i pacchetti e gli strumenti **di Android Pie (API 28)** tramite Gestione SDK.

3. Creare un nuovo progetto Xamarin.Android destinato **a Android 9.0.**

4. Configurare un emulatore o un dispositivo per il test delle app Android Pie.Configure an emulator or device for testing Android Pie apps.

Ognuno di questi passaggi è illustrato nelle sezioni seguenti:Each of these steps is explained in the following sections:

### <a name="update-visual-studio"></a>Aggiornare Visual Studio

Visual Studio 2019 è consigliato per la creazione di app per torte Android con Xamarin.

Se si utilizza Visual Studio 2017, eseguire l'aggiornamento a Visual Studio 2017 versione 15.8 o successiva (per istruzioni, vedere [Aggiornare Visual Studio 2017 alla versione più recente](https://docs.microsoft.com/visualstudio/install/update-visual-studio)). In macOS, eseguire l'aggiornamento a Visual Studio 2017 per Mac 7.6 o versione successiva (per istruzioni, vedere Installazione e installazione di [Visual Studio per Mac).](https://docs.microsoft.com/visualstudio/mac/installation)

### <a name="install-the-android-sdk"></a>Installare Android SDK

Per creare un progetto con Xamarin.Android 9.0, devi prima usare Android SDK Manager per installare la piattaforma SDK per **Android Pie (livello API 28)** o versione successiva.

1. Avviare Gestione SDK. In Visual Studio fare clic su **Strumenti > Android > Android SDK Manager**. In Visual Studio per Mac fare clic su **Strumenti > Gestione SDK**.

2. Nell'angolo in basso a destra, fai clic sull'icona a forma di ingranaggio e seleziona **Repository > Google (non supportato):**

    [![Impostazione del repository su Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. Installare i pacchetti **di Android Pie** SDK, elencati come Android SDK Platform **28** nella scheda **Piattaforme** (per ulteriori informazioni sull'utilizzo di Gestione SDK, vedere [Installazione di Android SDK](~/android/get-started/installation/android-sdk.md)):

    [![Installazione dei pacchetti Android Pie](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. Se si utilizza un emulatore, creare un dispositivo virtuale che supporti **il livello API 28.** Per ulteriori informazioni sulla creazione di dispositivi virtuali, vedere [Gestione di dispositivi virtuali con Gestione dispositivi Android](~/android/get-started/installation/android-emulator/device-manager.md).

### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto Xamarin.Android

Creare un nuovo progetto Xamarin.Android.Create a new Xamarin.Android project. Se non si ha familiarità con lo sviluppo Android con Xamarin, vedere Hello, Android per informazioni sulla creazione di progetti Xamarin.Android.If you are new to Android development with Xamarin, see [Hello, Android](~/android/get-started/hello-android/index.md) to learn about creating Xamarin.Android projects.

Quando crei un progetto Android, devi configurare le impostazioni della versione per Android 9.0 o versioni successive. Ad esempio, per impostare il progetto come destinazione per Android Pie, è necessario configurare il livello di API Android di destinazione del progetto su **Android 9.0** (API 28). È consigliabile impostare anche il livello di Framework di destinazione su API 28 o versione successiva. Per altre informazioni sulla configurazione dei livelli api Android, vedere [Informazioni sui livelli api Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-a-device-or-emulator"></a>Configurare un dispositivo o un emulatore

Se si utilizza un dispositivo fisico come un Nexus o un Pixel, è possibile aggiornare il dispositivo ad Android Pie seguendo le istruzioni in Immagini di [fabbrica per Nexus e dispositivi Pixel](https://developers.google.com/android/images).

Se si usa un emulatore, creare un dispositivo virtuale per il livello API 28 e selezionare un'immagine basata su x86. Per informazioni sull'utilizzo di Gestione dispositivi Android per creare e gestire dispositivi virtuali, vedere [Gestione di dispositivi virtuali con Gestione dispositivi Android.](~/android/get-started/installation/android-emulator/device-manager.md)
Per informazioni sull'utilizzo dell'emulatore Android per il test e il debug, vedere [Debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md).

## <a name="new-features"></a>Nuove funzionalità

Android Pie introduce una varietà di nuove funzionalità. Alcune di queste nuove funzionalità hanno lo scopo di sfruttare le nuove funzionalità hardware offerte dai più recenti dispositivi Android, mentre altre sono progettate per migliorare ulteriormente l'esperienza utente Android:

- **Display Cutout Support** &ndash; Fornisce API per trovare la posizione e la forma del _ritaglio_ nella parte superiore dello schermo sui dispositivi Android più recenti.

- **Miglioramenti delle notifiche** &ndash; I messaggi di notifica `Person` possono ora visualizzare le immagini e una nuova classe viene utilizzata per semplificare i partecipanti alla conversazione.

- Supporto della piattaforma di **posizionamento** &ndash; indoor per il protocollo WiFi Round-Trip-Time, che consente alle app di utilizzare i dispositivi WiFi per la navigazione nelle impostazioni interne.

- **Supporto** &ndash; multicamera Offre la possibilità di accedere ai flussi contemporaneamente da più videocamere fisiche (ad esempio telecamere dual-front e dual-back).

Le sezioni seguenti evidenziano queste funzionalità e forniscono brevi esempi di codice per iniziare a usarle nell'app.

### <a name="display-cutout-support"></a>Supporto del ritaglio del display

Molti dispositivi Android più recenti con schermi edge-to-edge hanno un *taglio display* (o "notch") nella parte superiore del display per fotocamera e altoparlanti.
La schermata seguente fornisce un esempio di emulatore di ritaglio:The following screenshot provides an emulator example of a cutout:

[![Emulatore Android che simula un ritaglio](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

Per gestire il modo in cui la finestra dell'app visualizza il contenuto nei dispositivi con un ritaglio di visualizzazione, Android Pie ha aggiunto un nuovo attributo di layout della finestra [LayoutInDisplayCutoutMode.To](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) manage how your app window displays its content on devices with a display cutout, Android Pie has added a new LayoutInDisplayCutoutMode window layout attribute. Questo attributo può essere impostato su uno dei seguenti valori:

- [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; La finestra non può mai sovrapporsi all'area di ritaglio.

- [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; La finestra può estendersi nell'area di ritaglio ma solo sui bordi corti dello schermo. 

- [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; La finestra può estendersi nell'area di ritaglio se il ritaglio è contenuto all'interno di una barra di sistema.

Ad esempio, per evitare che la finestra dell'app si sovrapponga all'area di ritaglio, imposta la modalità di ritaglio del layout *su mai:* 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

Gli esempi seguenti forniscono esempi di queste modalità di ritaglio. Il primo screenshot a sinistra è dell'app in modalità non a schermo intero. Nella schermata centrale, l'app `LayoutInDisplayCutoutMode` va `LayoutInDisplayCutoutModeShortEdges`a schermo intero con impostato su . Si noti che lo sfondo bianco dell'app si estende nell'area di ritaglio dello schermo:

[![Esempio di modalità di ritaglio di visualizzazione nell'emulatore](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

Nella schermata finale (sopra `LayoutInDisplayCutoutMode` a destra), è impostato su `LayoutInDisplayCutoutModeShortNever` prima che passi a schermo intero.
Si noti che lo sfondo bianco dell'app non è consentito estendersi nell'area di ritaglio dello schermo.

Se sono necessarie informazioni più dettagliate sull'area di ritaglio sul dispositivo, è possibile utilizzare la nuova classe [DisplayCutout.](https://developer.android.com/reference/android/view/DisplayCutout.html) `DisplayCutout`rappresenta l'area del display che non può essere utilizzata per visualizzare il contenuto. Puoi usare queste informazioni per recuperare la posizione e la forma del ritaglio in modo che l'app non tenti di visualizzare il contenuto in quest'area non funzionale.

Per ulteriori informazioni sulle nuove funzionalità di ritaglio in Android P, consultate [Supporto del ritaglio di visualizzazione.](https://developer.android.com/about/versions/pie/android-9.0#cutout)

### <a name="notifications-enhancements"></a>Miglioramenti delle notifiche

Android Pie introduce i seguenti miglioramenti per migliorare l'esperienza di messaggistica:

- I canali di notifica (introdotti in [Android Oreo](~/android/platform/oreo.md)) ora supportano il blocco dei gruppi di canali.

- Il sistema di notifica dispone di tre nuove categorie Do-Not-Disturb (assegnazione delle priorità agli allarmi, ai suoni di sistema e alle sorgenti multimediali). Inoltre, sono disponibili sette nuove modalità Do-Not-Disturb che possono essere utilizzate per sopprimere le interruzioni visive (come badge, luci di notifica, apparizioni della barra di stato e avvio di attività a schermo intero).

- È stata aggiunta una nuova classe [Person](https://developer.android.com/reference/android/app/Person.html) per rappresentare il mittente di un messaggio. L'uso di questa classe consente di ottimizzare il rendering di ogni notifica identificando le persone coinvolte in una conversazione (inclusi gli avatar e gli URI).

- Le notifiche possono ora visualizzare le immagini. 

Nell'esempio seguente viene illustrato come utilizzare le nuove API per generare una notifica che contiene un'immagine. Nelle schermate seguenti, una notifica di testo viene pubblicata ed è seguita da una notifica con un'immagine incorporata. Quando le notifiche vengono espanse (come mostrato a destra), viene visualizzato il testo della prima notifica e l'immagine incorporata nella seconda notifica viene ingrandita:

[![Esempio di notifica con immagine](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

L'esempio seguente illustra come includere un'immagine in una notifica Android `Person` Pie e illustra l'utilizzo della nuova classe:The following example illustrates how to include an image in an Android Pie notification, and it demonstrates usage of the new class:

1. Creare `Person` un oggetto che rappresenta il mittente. Ad esempio, il nome e l'icona `fromPerson`del mittente sono inclusi in:

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. Creare `Notification.MessagingStyle.Message` un che contiene l'immagine da inviare, passando l'immagine al nuovo [notification.MessagingStyle.Message.SetData](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri) metodo.
   Ad esempio:

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. Aggiungere il messaggio `Notification.MessagingStyle` a un oggetto. Ad esempio:

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. Collegare questo stile al generatore di notifiche. Ad esempio:

    ```csharp
    builder = new Notification.Builder(this, MY_CHANNEL)
        .SetContentTitle("Tour of the Colosseum")
        .SetContentText("I'm standing right here!")
        .SetSmallIcon(Resource.Mipmap.ic_notification)
        .SetStyle(style)
        .SetChannelId(MY_CHANNEL);
    ```

5. Pubblicare la notifica. Ad esempio:

    ```csharp
    const int notificationId = 1000;
    notificationManager.Notify(notificationId, builder.Build());
    ```

Per ulteriori informazioni sulla creazione di notifiche, vedere [Notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md).

### <a name="indoor-positioning"></a>Posizionamento interno

Android Pie fornisce il supporto per IEEE 802.11mc (noto anche come _WiFi Round-Trip-Time_ o _WiFi RTT_), che consente alle applicazioni di rilevare la distanza da uno o più punti di accesso Wi-Fi. Usando queste informazioni, è possibile che l'app sfrutti il *posizionamento interno* con una precisione di uno o due metri. Nei dispositivi Android che forniscono supporto hardware per IEEE 801.11mc, l'app può offrire funzionalità di navigazione come il controllo basato sulla posizione degli elettrodomestici intelligenti o istruzioni turn-by-turn tramite uno store:

[![Esempio di navigazione interna con WIFi RTT](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

La nuova classe [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) e diverse classi helper forniscono i mezzi per misurare la distanza dai dispositivi Wi-Fi. Per ulteriori informazioni sulle API di posizionamento indoor introdotte in Android P, vedere [Android.Net.Wifi.Rtt](https://developer.android.com/reference/android/net/wifi/rtt/package-summary).

### <a name="multi-camera-support"></a>Supporto per più telecamere

Molti dispositivi Android più recenti dispongono di fotocamere dual-front e/o dual-back che sono utili per funzionalità quali la visione stereo, effetti visivi migliorati e una migliore capacità di zoom. Android P introduce una nuova API [multicamera](https://developer.android.com/about/versions/pie/android-9.0#camera) che consente all'app di usare una *fotocamera logica* (o *multicamera logica)* supportata da due o più videocamere fisiche.
Per determinare se il dispositivo supporta una fotocamera multicamera logica, è possibile esaminare le funzionalità di ogni fotocamera nel dispositivo per verificare se supporta [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA).

Android Pie include anche una nuova classe [SessionConfiguration](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) che può essere utilizzata per ridurre i ritardi durante l'acquisizione iniziale ed eliminare la necessità di avviare e avviare il flusso della fotocamera.

Per ulteriori informazioni sul supporto di più telecamere in Android P, consultate Supporto per più [telecamere e aggiornamenti della fotocamera](https://developer.android.com/about/versions/pie/android-9.0#camera).

### <a name="other-features"></a>Altre funzionalità

Inoltre, Android Pie supporta diverse altre nuove funzionalità:

- La nuova classe [AnimatedImageDrawable,](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html) che può essere utilizzata per il disegno e la visualizzazione di immagini animate.

- Nuova classe [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) che `BitmapFactory`sostituisce . `ImageDecoder`può essere utilizzato per `AnimatedImageDrawable`decodificare un file .

- Supporto per video HDR (High Dynamic Range) e immagini HEIF (High Efficiency Image File Format).

- [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) è stato migliorato per gestire in modo più intelligente i processi relativi alla rete. Il nuovo metodo [GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29) della classe [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters) restituisce la rete migliore per l'esecuzione di qualsiasi richiesta di rete per un determinato processo.

Per ulteriori informazioni sulle funzionalità più recenti di Android Pie, vedere [API e funzionalità di Android 9](https://developer.android.com/about/versions/pie/android-9.0).

## <a name="behavior-changes"></a>Modifiche funzionali

Quando la versione di Android di destinazione è impostata sul livello API 28, esistono diverse modifiche alla piattaforma che possono influire sul comportamento dell'app anche se non si implementano le nuove funzionalità descritte in precedenza. L'elenco seguente è un breve riepilogo di queste modifiche:

- Le app devono ora richiedere l'autorizzazione in primo piano prima di usare i servizi in primo piano.

- Se l'app ha più di un processo, non può condividere una singola directory di dati [WebView](xref:Android.Webkit.WebView) tra i processi.

- L'accesso diretto alla directory dei dati di un'altra app tramite percorso non è più consentito.

Per altre informazioni sulle modifiche di comportamento per le app destinate ad Android P, vedere [Modifiche al comportamento](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps).

## <a name="sample-code"></a>Codice di esempio

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) è un'app di esempio Xamarin.Android per Android Pie che illustra `Person` come impostare le modalità di visualizzazione dei ritagli, come usare la nuova classe e come inviare una notifica che include un'immagine.

## <a name="summary"></a>Riepilogo

Questo articolo ha introdotto Android Pie e ha spiegato come installare e configurare gli strumenti e i pacchetti più recenti per lo sviluppo di Xamarin.Android con Android Pie. Ha fornito una panoramica delle funzionalità principali disponibili in Android Pie, con codice sorgente di esempio per molte di queste funzionalità.
Sono inclusi collegamenti alla documentazione dell'API e agli argomenti di Android Developer per aiutarti a iniziare a creare app per Android Pie. Ha anche evidenziato le più importanti modifiche di comportamento di Android Pie che potrebbero influire sulle app esistenti.

## <a name="related-links"></a>Collegamenti correlati

- [Android 9 Pie](https://developer.android.com/about/versions/pie/)
