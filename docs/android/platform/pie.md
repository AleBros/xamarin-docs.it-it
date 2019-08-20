---
title: Android 9 Pie
description: Come iniziare a sviluppare app per Android 9 a torta con Novell. Android.
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: 52141141ab525c7407fa2f3ff2dca749473b39c1
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511446"
---
# <a name="android-pie-features"></a>Funzionalità a torta Android

_Come iniziare a sviluppare app per Android 9 a torta con Novell. Android._

[Android 9 Pie](https://developer.android.com/about/versions/pie/) è ora disponibile da Google. In questa versione sono state rese disponibili numerose nuove funzionalità e API e molte di esse sono necessarie per sfruttare le nuove funzionalità hardware nei dispositivi Android più recenti.

![Immagine del Hero a torta Android](pie-images/01-android-p-logo.png)

Questo articolo è strutturato per iniziare a sviluppare app Novell. Android per la torta Android. Viene illustrato come installare gli aggiornamenti necessari, configurare l'SDK e preparare un emulatore o un dispositivo per il test. Viene inoltre fornita una descrizione delle nuove funzionalità di Android Pie e viene fornito un esempio di codice sorgente che illustra come utilizzare alcune delle principali funzionalità della torta Android.

Novell. Android 9,0 fornisce supporto per la torta Android. Per altre informazioni sul supporto di Novell. Android per la torta Android, vedere le note sulla versione di [Android P Developer Preview 3](https://docs.microsoft.com/xamarin/android/release-notes/9/9.0/#android-p-dp1) .

## <a name="requirements"></a>Requisiti

L'elenco seguente è necessario per usare le funzionalità di torta Android nelle app basate su Novell:

- **Visual Studio** &ndash; È consigliabile usare Visual Studio 2019.
    Se si usa Visual Studio 2017, in Windows Update per Visual Studio 2017 versione 15,8 o successiva. In macOS eseguire l'aggiornamento a Visual Studio 2017 per Mac versione 7,6 o successiva.

- **Novell. Android** &ndash; Novell. Android 9.0.0.17 o versione successiva deve essere installato con Visual Studio (Novell. Android viene installato automaticamente come parte del carico di lavoro **sviluppo di applicazioni per dispositivi mobili con .NET** ).

- **Java Developer Kit** Lo sviluppo di Novell Android 9,0 richiede [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (oppure è possibile provare l'anteprima della distribuzione di Microsoft [OpenJDK](~/android/get-started/installation/openjdk.md)). &ndash; JDK8 viene installato automaticamente come parte del carico di lavoro **sviluppo di applicazioni per dispositivi mobili con .NET** .

- **Android SDK** &ndash; Android SDK API 28 o versione successiva deve essere installato tramite gestione Android SDK.

## <a name="getting-started"></a>Introduzione

Per iniziare a sviluppare app a torta Android con Novell. Android, è necessario scaricare e installare gli strumenti e i pacchetti SDK più recenti prima di poter creare il primo progetto a torta Android:

1. È consigliato Visual Studio 2019. Se si usa Visual Studio 2017, eseguire l'aggiornamento a [Visual studio 2017 versione 15,8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) o successiva. Se si usa Visual Studio per Mac, eseguire l'aggiornamento a [Visual Studio 2017 per Mac versione 7,6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) o successiva.

2. Installare i pacchetti e gli strumenti di **Android Pie (API 28)** tramite SDK Manager.

3. Creare un nuovo progetto Novell. Android destinato a **Android 9,0**.

4. Configurare un emulatore o un dispositivo per il test delle app a torta Android.

Ognuno di questi passaggi è descritto nelle sezioni seguenti:

### <a name="update-visual-studio"></a>Aggiornare Visual Studio

Visual Studio 2019 è consigliato per la creazione di app a torta Android con Novell.

Se si usa Visual Studio 2017, eseguire l'aggiornamento a Visual Studio 2017 versione 15,8 o successiva. per istruzioni, vedere [aggiornare Visual studio 2017 alla versione più recente](https://docs.microsoft.com/visualstudio/install/update-visual-studio). In macOS eseguire l'aggiornamento a Visual Studio 2017 per Mac 7,6 o versione successiva (per istruzioni, vedere [installazione e installazione Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/installation)).

### <a name="install-the-android-sdk"></a>Installare il Android SDK

Per creare un progetto con Novell. Android 9,0, è necessario prima di tutto usare gestione Android SDK per installare la piattaforma SDK per **Android Pie (livello API 28)** o versione successiva.

1. Avviare SDK Manager. In Visual Studio fare clic su **strumenti > Android > Android SDK Manager**. In Visual Studio per Mac fare clic su **strumenti > SDK Manager**.

2. Nell'angolo in basso a destra fare clic sull'icona a forma di ingranaggio e selezionare **Repository > Google (non supportato)** :

    [![Impostazione del repository su Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. Installare i pacchetti **Android Pie** SDK, elencati come **Android SDK Platform 28** nella scheda Platforms (per altre informazioni sull'uso di SDK Manager, vedere [Android SDK installazione](~/android/get-started/installation/android-sdk.md)):

    [![Installazione dei pacchetti di torta Android](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. Se si usa un emulatore, creare un dispositivo virtuale che supporti il **livello API 28**. Per ulteriori informazioni sulla creazione di dispositivi virtuali, vedere [gestione di dispositivi virtuali con il Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).

### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto Novell. Android

Creare un nuovo progetto Novell. Android. Se non si ha familiarità con lo sviluppo di Android con Novell, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per altre informazioni sulla creazione di progetti Novell. Android.

Quando si crea un progetto Android, è necessario configurare le impostazioni della versione per la destinazione Android 9,0 o versione successiva. Ad esempio, per fare riferimento al progetto per la torta Android, è necessario configurare il livello API Android di destinazione del progetto su **android 9,0** (API 28). Si consiglia inoltre di impostare il livello di Framework di destinazione sull'API 28 o versione successiva. Per altre informazioni sulla configurazione dei livelli API Android, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-a-device-or-emulator"></a>Configurare un dispositivo o un emulatore

Se si usa un dispositivo fisico, ad esempio un Nexus o un pixel, è possibile aggiornare il dispositivo alla torta Android seguendo le istruzioni in [Immagini Factory per i dispositivi Nexus e pixel](https://developers.google.com/android/images).

Se si usa un emulatore, creare un dispositivo virtuale per il livello API 28 e selezionare un'immagine basata su x86. Per informazioni sull'utilizzo del Android Device Manager per creare e gestire i dispositivi virtuali, vedere [gestione di dispositivi virtuali con il Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).
Per informazioni sull'uso dell'emulatore Android per il test e il debug, vedere [debug nella emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md).



## <a name="new-features"></a>Nuove funzionalità

Android Pie introduce un'ampia gamma di nuove funzionalità. Alcune di queste nuove funzionalità sono destinate a sfruttare le nuove funzionalità hardware offerte dai dispositivi Android più recenti, mentre altre sono progettate per migliorare ulteriormente l'esperienza utente di Android:

-   **Visualizza supporto ritaglio** Fornisce le API per trovare la posizione e la forma del ritaglio nella parte superiore dello schermo nei dispositivi Android più recenti. &ndash;

-   **Miglioramenti delle notifiche** I messaggi di notifica possono ora visualizzare immagini e una `Person` nuova classe viene utilizzata per semplificare i partecipanti alla conversazione. &ndash;

-   **Posizionamento interno** &ndash; Supporto della piattaforma per il protocollo di round trip Wi-Fi, che consente alle app di usare i dispositivi Wi-Fi per la navigazione nelle impostazioni interne.

-   **Supporto di più fotocamere** &ndash; Offre la possibilità di accedere ai flussi simultaneamente da più fotocamere fisiche, ad esempio fotocamere Dual-front e Dual-back.


Le sezioni seguenti evidenziano queste funzionalità e forniscono brevi esempi di codice che consentono di iniziare a usarle nell'app.

### <a name="display-cutout-support"></a>Visualizza supporto ritaglio

Molti dispositivi Android più recenti con schermi Edge-to-Edge hanno un ritaglio di *visualizzazione* (o "Notch") nella parte superiore dello schermo per la fotocamera e il relatore.
Lo screenshot seguente fornisce un esempio di un emulatore di ritaglio:

[![Emulatore Android che simula un ritaglio](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

Per gestire il modo in cui la finestra dell'app Visualizza il contenuto nei dispositivi con un ritaglio di visualizzazione, Android Pie ha aggiunto un nuovo attributo di layout della finestra [LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) . Questo attributo può essere impostato su uno dei valori seguenti:

-   [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; La finestra non può mai sovrapporsi all'area di ritaglio.

-   [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; È possibile estendere la finestra nell'area di ritaglio ma solo sui bordi corti dello schermo. 

-   [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; Se il ritaglio è contenuto all'interno di una barra di sistema, è possibile estendere la finestra nell'area di ritaglio.

Ad esempio, per impedire che la finestra dell'app si sovrappongano con l'area di ritaglio, impostare la modalità di ritaglio del layout su *mai*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

Negli esempi seguenti vengono forniti esempi di queste modalità di ritaglio. La prima schermata a sinistra è l'app in modalità non a schermo intero. Nello screenshot centrale, l'app viene visualizzata a schermo intero con `LayoutInDisplayCutoutMode` impostato su `LayoutInDisplayCutoutModeShortEdges`. Si noti che lo sfondo bianco dell'app si estende nell'area di ritaglio dello schermo:

[![Esempio di visualizzazione delle modalità di ritaglio nell'emulatore](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

Nello screenshot finale (sopra a destra), `LayoutInDisplayCutoutMode` viene impostato su `LayoutInDisplayCutoutModeShortNever` prima di passare a schermo intero.
Si noti che lo sfondo bianco dell'app non può estendersi all'area ritaglio visualizzazione.

Se sono necessarie informazioni più dettagliate sull'area di ritaglio sul dispositivo, è possibile usare la nuova classe [DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html) . `DisplayCutout`rappresenta l'area della visualizzazione che non può essere utilizzata per visualizzare il contenuto. È possibile usare queste informazioni per recuperare la posizione e la forma del ritaglio in modo che l'app non tenti di visualizzare il contenuto in questa area non funzionale.

Per altre informazioni sulle nuove funzionalità di ritaglio in Android P, vedere [visualizzare il supporto](https://developer.android.com/about/versions/pie/android-9.0#cutout)del ritaglio.



### <a name="notifications-enhancements"></a>Miglioramenti delle notifiche

Android Pie introduce i miglioramenti seguenti per migliorare l'esperienza di messaggistica:

-   I canali di notifica (introdotti in [Android Oreo](~/android/platform/oreo.md)) ora supportano il blocco dei gruppi di canali.

-   Il sistema di notifica ha tre nuove categorie non di disturbo, ovvero la definizione delle priorità per gli avvisi, i suoni di sistema e le origini multimediali. Sono inoltre disponibili sette nuove modalità non di disturbo che possono essere utilizzate per disattivare le interruzioni visive, ad esempio le notifiche, le luci di notifica, gli aspetti della barra di stato e l'avvio di attività a schermo intero.

-   È stata aggiunta una nuova classe [Person](https://developer.android.com/reference/android/app/Person.html) per rappresentare il mittente di un messaggio. L'uso di questa classe consente di ottimizzare il rendering di ogni notifica identificando gli utenti interessati da una conversazione (inclusi i relativi avatar e URI).

-   Le notifiche ora possono visualizzare le immagini. 

Nell'esempio seguente viene illustrato come utilizzare le nuove API per generare una notifica che contiene un'immagine. Negli screenshot seguenti viene inviata una notifica di testo che è seguita da una notifica con un'immagine incorporata. Quando le notifiche vengono espanse (come visualizzato a destra), viene visualizzato il testo della prima notifica e l'immagine incorporata nella seconda notifica viene ingrandita:

[![Notifica di esempio con immagine](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

Nell'esempio seguente viene illustrato come includere un'immagine in una notifica a torta Android e viene illustrato l'utilizzo della nuova `Person` classe:

1. Creare un `Person` oggetto che rappresenti il mittente. Ad esempio, il nome e l'icona del mittente sono inclusi `fromPerson`in:

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. Creare un `Notification.MessagingStyle.Message` oggetto che contiene l'immagine da inviare, passando l'immagine al nuovo metodo [Notification. MessagingStyle. Message. SetData](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri) .
   Ad esempio:

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. Aggiungere il messaggio a un `Notification.MessagingStyle` oggetto. Ad esempio:

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. Inserire lo stile nel generatore di notifiche. Ad esempio:

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

Per ulteriori informazioni sulla creazione di notifiche, vedere [Local notifications](~/android/app-fundamentals/notifications/local-notifications.md).


### <a name="indoor-positioning"></a>Posizionamento interno

Android Pie fornisce supporto per IEEE 802.11 MC (noto anche come _tempo di round trip WiFi_ o _RTT WiFi_), che consente alle app di rilevare la distanza da uno o più punti di accesso Wi-Fi. Usando queste informazioni, è possibile che l'app possa sfruttare i vantaggi del *posizionamento interno* con un'accuratezza di uno o due contatori. Nei dispositivi Android che forniscono supporto hardware per IEEE 801.11 MC, l'app può offrire funzionalità di navigazione, ad esempio il controllo basato sulla posizione di Smart Appliance o istruzioni turn-by-turn tramite un negozio:

[![Esempio di navigazione interna con RTT WiFi](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

La nuova classe [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) e diverse classi helper forniscono i mezzi per misurare la distanza ai dispositivi Wi-Fi. Per altre informazioni sulle API di posizionamento interno introdotte in Android P, vedere [Android .NET. WiFi. RTT](https://developer.android.com/reference/android/net/wifi/rtt/package-summary).


### <a name="multi-camera-support"></a>Supporto di più fotocamere

Molti dispositivi Android più recenti hanno fotocamere Dual-front e/o Dual-back utili per le funzionalità come la visione stereo, gli effetti visivi avanzati e la funzionalità di zoom migliorata. Android P introduce una nuova API per più [fotocamere](https://developer.android.com/about/versions/pie/android-9.0#camera) che consente all'app di usare una *fotocamera logica* (o una multifotocamera *logica*) supportata da due o più fotocamere fisiche.
Per determinare se il dispositivo supporta una macchina a più telecamere logica, è possibile esaminare le funzionalità di ogni fotocamera sul dispositivo per verificare se supporta [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA).

Android Pie include anche una nuova classe [SessionConfiguration](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) che può essere usata per ridurre i ritardi durante l'acquisizione iniziale ed eliminare la necessità di avviare e avviare il flusso della fotocamera.

Per altre informazioni sul supporto di più fotocamere in Android P, vedere [supporto per più fotocamere e aggiornamenti della fotocamera](https://developer.android.com/about/versions/pie/android-9.0#camera).


### <a name="other-features"></a>Altre funzionalità

Android Pie supporta inoltre diverse altre nuove funzionalità:

-   Nuova classe [AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html) , che può essere usata per disegnare e visualizzare immagini animate.

-   Nuova classe [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) che sostituisce `BitmapFactory`. `ImageDecoder`può essere usato per decodificare `AnimatedImageDrawable`un.

-   Supporto per le immagini HDR (High Dynamic Range) e HEIF (High Efficiency Image File Format).

-   Il [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) è stato migliorato per gestire in modo più intelligente i processi correlati alla rete. Il nuovo metodo [GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29) della classe [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters) restituisce la rete migliore per l'esecuzione di qualsiasi richiesta di rete per un determinato processo.

Per altre informazioni sulle funzionalità di torta Android più recenti, vedere le [funzionalità e le API di Android 9](https://developer.android.com/about/versions/pie/android-9.0).


## <a name="behavior-changes"></a>Modifiche del comportamento

Quando la versione di Android di destinazione è impostata sul livello API 28, esistono diverse modifiche alla piattaforma che possono influire sul comportamento dell'app anche se non si implementano le nuove funzionalità descritte in precedenza. Il seguente elenco è un breve riepilogo delle modifiche:

-  Le app devono ora richiedere l'autorizzazione Foreground prima di usare i servizi in primo piano.

-  Se l'app ha più di un processo, non è possibile condividere una singola directory di dati [WebView](xref:Android.Webkit.WebView) nei processi.

-  L'accesso diretto alla directory dei dati di un'altra app in base al percorso non è più consentito.

Per altre informazioni sulle modifiche del comportamento per le app destinate a Android P, vedere [modifiche del comportamento](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps).


## <a name="sample-code"></a>Codice di esempio

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) è un'app di esempio Novell. Android per la torta Android che illustra come impostare le modalità di ritaglio dello schermo, `Person` come usare la nuova classe e come inviare una notifica che include un'immagine.


## <a name="summary"></a>Riepilogo

Questo articolo ha presentato Android Pie ed è stato illustrato come installare e configurare gli strumenti e i pacchetti più recenti per lo sviluppo di Novell. Android con la torta Android. Fornisce una panoramica delle funzionalità principali disponibili in Android Pie, con esempi di codice sorgente per alcune di queste funzionalità.
Sono inclusi collegamenti alla documentazione API e agli argomenti per sviluppatori Android che consentono di iniziare a creare app per la torta Android. Ha evidenziato anche le modifiche più importanti del comportamento della torta Android che potrebbero influito sulle app esistenti.


## <a name="related-links"></a>Collegamenti correlati

- [Torta Android 9](https://developer.android.com/about/versions/pie/)
