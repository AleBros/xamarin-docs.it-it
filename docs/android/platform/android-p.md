---
title: Anteprima P Android
description: Come iniziare a usare xamarin. Android per sviluppare App per la versione più recente di Android.
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 07/27/2018
ms.openlocfilehash: a3eef6f2537a4b09f603787d7cdbf70a173fca80
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351919"
---
# <a name="android-p-preview"></a>Anteprima P Android

_Come iniziare a usare xamarin. Android per sviluppare App per la versione più recente di Android._

![](~/media/shared/preview.png)

Il [Android P Developer Preview](https://developer.android.com/preview/) è ora disponibile da Google. Un numero di nuove funzionalità e le API è resi disponibile in questa versione, e molti di essi sono necessarie per sfruttare i vantaggi delle nuove funzionalità hardware nei dispositivi Android più recenti.

![Immagine hero P Android](android-p-images/01-android-p-logo.png)

Questo articolo è strutturato per aiutarti a iniziare lo sviluppo di App xamarin. Android per l'anteprima P Android. Viene spiegato come installare gli aggiornamenti necessari, configurare il SDK e preparare un emulatore o dispositivo per il test. Inoltre fornisce una descrizione delle nuove funzionalità in Android P e fornisce codice sorgente di esempio che illustra come usare alcune delle principali funzionalità Android P.


## <a name="requirements"></a>Requisiti

Per utilizzare le funzionalità di Android P nelle App basate su Xamarin è necessario quanto segue:

-   **Visual Studio** &ndash; se si usa Windows, versione 15,8 Preview 5 o versione successiva di Visual Studio è obbligatorio.  Se si usa un Mac, è necessaria la versione Beta corrente di Visual Studio per Mac o in un secondo momento.

-   **Xamarin. Android** &ndash; xamarin. Android 9.0.0.17 o versioni successive deve essere installato con Visual Studio.

-   **Java Developer Kit** &ndash; richiede lo sviluppo di Xamarin Android 9.0 [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (o è possibile provare l'anteprima di distribuzione Microsoft del [OpenJDK](~/android/get-started/installation/openjdk.md)).

-   **Android SDK** &ndash; API di Android SDK 28 o versioni successiva deve essere installato tramite Android SDK Manager.

## <a name="getting-started"></a>Introduzione

Per iniziare a usare P Android con xamarin. Android, è necessario scaricare e installare gli strumenti più recenti e i pacchetti SDK prima di poter creare il primo progetto in Android P:

1. Aggiornamento a Visual Studio 15.8 Preview 5 o versione successiva. Se si usa Visual Studio per Mac, passare a Visual Studio per Mac [Beta](https://docs.microsoft.com/visualstudio/mac/update) canale.

2. Installare il **P Android (API 28)** o versione successiva i pacchetti e strumenti tramite SDK Manager.

3. Creare un nuovo progetto xamarin. Android destinata a P Android (API 28).

4. Configurare un emulatore o dispositivo per testare App Android P.

Ognuno di questi passaggi è illustrato nelle sezioni seguenti:


### <a name="update-visual-studio"></a>Aggiornare Visual Studio

Per aggiungere il supporto P Android a Visual Studio, l'aggiornamento alla versione di Visual Studio 2017 Preview 15,8 5 o versioni successive come illustrato nella [aggiornare Visual Studio 2017 alla versione più recente](https://docs.microsoft.com/visualstudio/install/update-visual-studio).
Per aggiungere supporto P Android per Visual Studio per Mac, aggiornare alla versione Beta più recente di Visual Studio 2017 per Mac come descritto [l'aggiornamento di Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/update).


### <a name="install-the-android-sdk"></a>Installare Android SDK

Per creare un progetto xamarin. Android 9.0, è necessario usare Android SDK Manager prima di installare il SDK della piattaforma per **P Android (livello API 28)** o versione successiva.

1. Avviare SDK Manager. In Visual Studio, fare clic su **strumenti > Android > Android SDK Manager**. In Visual Studio per Mac, fare clic su **strumenti > SDK Manager**.

2. Nell'angolo inferiore destro, fare clic sull'icona a forma di ingranaggio e selezionare **Repository > Google (non supportata)**:

    [![Impostazione del Repository a Google](android-p-images/vs/set-repo-sml.png)](android-p-images/vs/set-repo.png#lightbox)

3. Installare il **P Android** pacchetti che vengono visualizzati come **Android SDK Platform 28** nel **piattaforme** scheda (per altre informazioni sull'uso di SDK Manager, vedere (Android Setup)[~/android/get-started/installation/android-sdk.md]) SDK: 

    [![Installazione di pacchetti Android P](android-p-images/vs/sdk-manager-sml.png)](android-p-images/vs/sdk-manager.png#lightbox)

4. Se si usa un emulatore, creare un dispositivo virtuale che supporta **API a livello di 28**. Per altre informazioni sulla creazione di dispositivi virtuali, vedere [gestione dei dispositivi virtuali con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).



### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto xamarin. Android

Creare un nuovo progetto xamarin. Android. Se si ha familiarità con lo sviluppo di Android con Xamarin, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per altre informazioni sulla creazione di progetti xamarin. Android.

Quando si crea un progetto Android, è necessario configurare le impostazioni di versione di destinazione Android P o versione successiva. Per impostare come destinazione del progetto per Android P, ad esempio, è necessario configurare il livello API Android di destinazione del progetto per **P Android (API 28)**. È consigliabile impostare il livello di framework di destinazione e API 28 o versioni successive. Per altre informazioni sulla configurazione dei livelli a livello API Android, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-a-device-or-emulator"></a>Configurare un dispositivo o emulatore

Se si usa un dispositivo fisico, ad esempio un Pixel, è possibile aggiornare il dispositivo per l'anteprima P Android seguendo le istruzioni disponibili nel [i dispositivi Android versione Beta di P](https://developer.android.com/preview/devices/).

Se si usa un emulatore, creare un dispositivo virtuale per il livello API 28 usando un'immagine basata su x86. Per informazioni sull'uso di gestione di dispositivi Android per creare e gestire i dispositivi virtuali, vedere [gestione dei dispositivi virtuali con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).
Per informazioni sull'uso dell'emulatore di Android per i test e debug, vedere [debug con l'emulatore Android di Google](~/android/deploy-test/debugging/android-sdk-emulator/index.md).



## <a name="new-features"></a>Nuove funzionalità

P Android introduce un'ampia gamma di nuove funzionalità. Alcune di queste nuove funzionalità sono destinati a sfruttare nuove funzionalità di hardware offerte dai dispositivi Android più recenti, mentre altri sono progettati per migliorare ulteriormente l'esperienza utente di Android:

-   **Visualizzare il supporto di ritaglio** &ndash; fornisce le API per trovare la posizione e la forma delle _ritaglio_ nella parte superiore dello schermo nei dispositivi Android più recenti.

-   **Miglioramenti di notifica** &ndash; dei messaggi di notifica possono ora visualizzare immagini e un nuovo `Person` classe viene usata per semplificare i partecipanti alla conversazione.

-   **Posizionamento silenzioso** &ndash; supporto della piattaforma per il protocollo di Round Trip-Time Wi-Fi, che rende possibile per le app di usare dispositivi Wi-Fi per la navigazione nelle impostazioni per interni.

-   **Il supporto per multi-fotocamere** &ndash; offre le funzionalità per flussi di accesso contemporaneamente da più videocamere fisici (ad esempio dual-front-end e dual-back fotocamere).


Le sezioni seguenti vengono evidenziate queste funzionalità e forniscono brevi esempi di codice che consentono di iniziare a usarle nell'app.

### <a name="display-cutout-support"></a>Visualizzare il supporto di ritaglio

Molti dispositivi Android più recente delle schermate di edge-to-edge hanno una *ritaglio visualizzare* (o "elimina banda") nella parte superiore dello schermo per la fotocamera e relatore.
Nella schermata seguente viene fornito un esempio dell'emulatore di ritaglio:

[![Ritaglio simulazione dell'emulatore Android](android-p-images/02-example-cutout-sml.png)](android-p-images/02-example-cutout.png#lightbox)

Per la gestione come la finestra dell'app Visualizza il contenuto nei dispositivi con un display di ritaglio, ha aggiunto un nuovo Android P [LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) attributo di layout della finestra. Questo attributo può essere impostato su uno dei valori seguenti:

-   [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; la finestra non può mai essere sovrapporsi con l'area di ritaglio.

-   [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; la finestra è consentita estendere nell'area di ritaglio, ma solo sui bordi breve della schermata. 

-   [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; la finestra è consentita estendere nell'area di ritaglio se il ritaglio è contenuto all'interno di una barra di sistema.

Ad esempio, per impedire la sovrapposizione con l'area di ritaglio nella finestra dell'applicazione, impostare la modalità di ritaglio del layout *mai*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

Negli esempi seguenti vengono forniti esempi di queste modalità di ritaglio. Il primo screenshot a sinistra è dell'app in modalità non a schermo intero. Nella schermata center, l'app passa a schermo con `LayoutInDisplayCutoutMode` impostato su `LayoutInDisplayCutoutModeShortEdges`. Si noti che uno sfondo bianco dell'app si estende nell'area di ritaglio della visualizzazione:

[![Esempio di visualizzare le modalità di ritaglio nell'emulatore](android-p-images/03-cutout-modes-sml.png)](android-p-images/03-cutout-modes.png#lightbox)

Nella schermata finale (in precedenza a destra), `LayoutInDisplayCutoutMode` è impostata su `LayoutInDisplayCutoutModeShortNever` prima di passare allo schermo.
Si noti che uno sfondo bianco dell'app non è consentito estendere nell'area di ritaglio della visualizzazione.

Se sono necessarie informazioni più dettagliate sull'area di ritaglio nel dispositivo, è possibile usare le nuove [DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html) classe. `DisplayCutout` rappresenta l'area di visualizzazione non può essere usata per visualizzare il contenuto. È possibile usare queste informazioni per recuperare la posizione e la forma del ritaglio in modo che l'app non tenta di visualizzare il contenuto in quest'area non funzionali.

Per altre informazioni sulle nuove funzionalità di ritaglio P Android, vedere [visualizzare il supporto di ritaglio](https://developer.android.com/preview/features#cutout).



### <a name="notifications-enhancements"></a>Miglioramenti delle notifiche

P Android introduce i miglioramenti seguenti per migliorare l'esperienza di messaggistica:

-   I canali di notifica (introdotta in [Android Oreo](~/android/platform/oreo.md)) ora supporta il blocco dei gruppi di canale.

-   Il sistema di notifica ha tre nuove-non disturbare categorie (assegnazione di priorità gli allarmi, suoni del sistema e origini multimediali). Inoltre, esistono sette nuovi-non disturbare modalità che può essere usata per eliminare le interruzioni visual (ad esempio le notifiche, luci notifica, gli aspetti di barra di stato e l'avvio delle attività a schermo intero).

-   Una nuova [persona](https://developer.android.com/reference/android/app/Person.html) classe è stata aggiunta per rappresentare il mittente di un messaggio. Utilizzo di questa classe consente di ottimizzare il rendering di ogni notifica identificando le persone coinvolte in una conversazione (inclusi i relativi URI e avatar).

-   Le notifiche possono ora visualizzare le immagini. 

L'esempio seguente illustra come usare le nuove API per generare una notifica che contiene un'immagine. Nelle schermate seguenti, una notifica di testo viene inserita ed è seguita da una notifica con un'immagine incorporata. Quando le notifiche vengono espansi (come illustrato a destra), viene visualizzato il testo della prima notifica e l'immagine incorporata in viene ingrandita della seconda notifica:

[![Esempio di notifica con l'immagine](android-p-images/04-example-notifications-sml.png)](android-p-images/04-example-notifications.png#lightbox)

L'esempio seguente illustra come includere un'immagine in una notifica P Android e viene illustrato l'utilizzo del nuovo `Person` classe:

1. Creare un `Person` oggetto che rappresenta il mittente. Ad esempio, nome del mittente e l'icona sono inclusi in `fromPerson`:

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. Creare un `Notification.MessagingStyle.Message` che contiene l'immagine per l'invio, passando l'immagine al nuovo [Notification.MessagingStyle.Message.SetData](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri) (metodo).
   Ad esempio:

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. Aggiungere il messaggio di un `Notification.MessagingStyle` oggetto. Ad esempio:

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. Collegare questo stile di visualizzazione Generatore di notifica. Ad esempio:

    ```csharp
    builder = new Notification.Builder(this, MY_CHANNEL)
        .SetContentTitle("Tour of the Colosseum")
        .SetContentText("I'm standing right here!")
        .SetSmallIcon(Resource.Mipmap.ic_notification)
        .SetStyle(style)
        .SetChannelId(MY_CHANNEL);
    ```

5. La notifica di pubblicazione. Ad esempio:

    ```csharp
    const int notificationId = 1000;
    notificationManager.Notify(notificationId, builder.Build());
    ```

Per altre informazioni sulla creazione di notifiche, vedere [notifiche locali](~/android/app-fundamentals/notifications/local-notifications.md).


### <a name="indoor-positioning"></a>Posizionamento interni

P Android offre il supporto per IEEE 802.11mc (noto anche come _Wi-Fi Round-Trip-Time_ oppure _WiFi RTT_), che rende possibile per le app rilevare la distanza su uno o più accesso Wi-Fi punta. Utilizzando queste informazioni, è possibile che l'app in modo da sfruttare *posizionamento silenzioso* con un'accuratezza di uno o due contatori. Nei dispositivi Android che forniscono il supporto hardware per IEEE 801.11mc, l'app può offrire funzionalità di navigazione, ad esempio controllo basati sulla posizione di Appliance intelligenti o attiva-istruzioni tramite un archivio:

[![Esempio di navigazione interni tramite WiFi RTT](android-p-images/05-wifi-rtt-sml.png)](android-p-images/05-wifi-rtt.png#lightbox)

Il nuovo [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) classe e diverse classi di helper fornisce i mezzi per misurare la distanza Wi-Fi nei dispositivi. Per altre informazioni sulle API di posizionamento per interni introdotte in Android P, vedere [Android.Net.Wifi.Rtt](https://developer.android.com/reference/android/net/wifi/rtt/package-summary).


### <a name="multi-camera-support"></a>Supporto multi-della fotocamera

Molti dispositivi Android più recenti sono provvisti di videocamera dual-front e/o dual-back che sono utili per funzionalità quali visione stereo, effetti visivi avanzati e funzionalità migliorate di zoom. P Android introduce una nuova [multi-fotocamera](https://developer.android.com/preview/features#camera) API che rende possibile all'app di usare un *fotocamera logica* (o *fotocamera più logica*) supportata da due o più fotocamere fisici.
Per determinare se il dispositivo supporta una fotocamera più logico, è possibile esaminare le funzionalità di ogni della fotocamera del dispositivo per verificare se supporta [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA).

P Android include anche una nuova [SessionConfiguration](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) classe che può essere usata per ridurre ritardi durante l'acquisizione iniziale ed eliminano la necessità di avviare e avviare il flusso della fotocamera.

Per altre informazioni su multi-fotocamera supporto p Android, vedere [fotocamera più supporto e fotocamera aggiornamenti](https://developer.android.com/preview/features#camera).


### <a name="other-features"></a>Altre funzionalità

Inoltre, P Android supporta diverse altre nuove funzionalità:

-   Il nuovo [AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html) (classe), che può essere usato per la creazione e visualizzazione di immagini animate.

-   Una nuova [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) classe che sostituisce `BitmapFactory`. `ImageDecoder` Consente di decodificare un `AnimatedImageDrawable`.

-   Immagini supportate per il video HDR (intervallo dinamico di elevata) e HEIF (elevata efficienza Image File Format).

-   Il [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) è stata migliorata per gestire in modo più intelligente dei processi correlati alla rete. Il nuovo [GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29) metodo per il [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters) classe restituisce la rete migliore per l'esecuzione di tutte le richieste di rete per un determinato processo.

Per altre informazioni sulle funzionalità P Android più recente, vedere [API e le funzionalità di Android P](https://developer.android.com/preview/features).


## <a name="behavior-changes"></a>Modifiche del comportamento

Quando la versione Android di destinazione viene impostata a livello di API 28, sono disponibili numerose modifiche di piattaforma che possono influenzare il comportamento dell'app anche se non si sta implementando le nuove funzionalità descritte sopra. Di seguito è riportato un breve riepilogo di queste modifiche:

-  Le app devono ora richiedere l'autorizzazione di primo piano prima di utilizzare servizi in primo piano.

-  Se l'app ha più di un processo, è possibile condividere un unico [WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) directory dei dati tra processi.

-  Accedere direttamente alla directory dei dati dell'app dal percorso non è più consentita.

Per altre informazioni sulle modifiche di comportamento per le app destinate ad Android P, vedere [modifiche del comportamento](https://developer.android.com/preview/behavior-changes.html#p-apps).


## <a name="sample-code"></a>Codice di esempio

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) è un'app di esempio xamarin. Android per modalità di ritaglio, di visualizzazione P Android che illustra come impostare come usare il nuovo `Person` classe e su come inviare una notifica che include un'immagine.


## <a name="summary"></a>Riepilogo

Questo articolo introdotta l'anteprima P Android ed è stato spiegato come installare e configurare gli strumenti e i pacchetti per lo sviluppo di xamarin. Android più recente con la versione di anteprima di Android P. Fornita una panoramica delle funzionalità chiave disponibili in Android P, con esempio di codice sorgente per molte di queste funzionalità. Inclusi collegamenti alla documentazione API e per sviluppatori Android argomenti che consentono di iniziare la creazione di App per Android P. Ha rivelato anche le modifiche di comportamento P Android più importanti che potrebbero influire sulle App esistenti.


## <a name="related-links"></a>Collegamenti correlati

- [P Android Developer Preview](https://developer.android.com/preview/)
