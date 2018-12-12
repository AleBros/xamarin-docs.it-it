---
title: Funzionalità di Oreo
description: Come iniziare a usare xamarin. Android per sviluppare App per la versione più recente di Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 07/06/2018
ms.openlocfilehash: 765494b5d9a55aedd9c9b3f0ea29ea389346ae3c
ms.sourcegitcommit: 2868c968f418cd7cc110f9664f3c3ffb6df1f9af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53267625"
---
# <a name="oreo-features"></a>Funzionalità di Oreo

_Come iniziare a usare xamarin. Android per sviluppare App per la versione più recente di Android._

[Android 8.0 Oreo](https://developer.android.com/index.html) è la versione più recente di Android disponibile da Google. Android Oreo offre molte nuove funzionalità di interesse per gli sviluppatori di xamarin. Android. Queste funzionalità includono i canali di notifica, notifiche sulla notifica, i tipi di carattere personalizzati in XML, i tipi di carattere scaricabile, riempimento automatico e picture in picture (PIP). Android Oreo include nuove API per queste nuove funzionalità e queste API sono disponibili per le app xamarin. Android quando si usa xamarin. Android 8.0 e versioni successive.

[![Immagine hero di Android Oreo](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

Questo articolo è strutturato per aiutarti a iniziare lo sviluppo di App xamarin. Android per Android 8.0 Oreo. Viene spiegato come installare gli aggiornamenti necessari, configurare il SDK e creare un emulatore (o un dispositivo) per il test. Fornisce inoltre una descrizione delle nuove funzionalità in Android Oreo 8.0, con collegamenti alle app di esempio che illustrano come usare le funzionalità di Android Oreo nelle App xamarin. Android.


## <a name="requirements"></a>Requisiti

Per utilizzare le funzionalità di Android Oreo nelle App basate su Xamarin è necessario quanto segue:

-   **Visual Studio** &ndash; se si usa Windows, è necessaria una versione 15.5 o versioni successive di Visual Studio.  Se si usa un Mac, è necessario Visual Studio per Mac versione 7.2.0.

-   **Xamarin. Android** &ndash; xamarin. Android 8.0 o versioni successive deve essere installato e configurato con Visual Studio.

-   **Android SDK** &ndash; Android SDK 8.0 (API 26) o versione successiva deve essere installato tramite Android SDK Manager.



## <a name="getting-started"></a>Introduzione

Per iniziare a usare Android Oreo con xamarin. Android, è necessario scaricare e installare gli strumenti più recenti e i pacchetti SDK prima di poter creare un progetto Android Oreo:

1. Aggiornamento alla versione più recente di Visual Studio.

2. Installare il **8.0.0 Android (API 26)** o versione successiva i pacchetti e strumenti tramite SDK Manager.

3. Creare un nuovo progetto xamarin. Android destinata a Android Oreo (API 26).

4. Configurare un emulatore o dispositivo per il test delle app di Android Oreo.

Ognuno di questi passaggi è illustrato nelle sezioni seguenti:



### <a name="update-visual-studio-and-xamarinandroid"></a>Aggiornare Visual Studio e xamarin. Android

Per aggiungere il supporto di Android Oreo a Visual Studio, eseguire le operazioni seguenti:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

-   Se si usa Visual Studio 2017: 

    1. Aggiornamento a Visual Studio 2017 versione 15.7 o successiva (vedere [aggiornare Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

    2. Usare la [SDK Manager](~/android/get-started/installation/android-sdk.md) per installare il livello API 26.0 o versione successivo.

-   Se si usa Visual Studio 2015, è consigliabile il downgrade di SDK Tools a 25 e tramite la GUI di Gestione emulatori Google precedente. Strumenti SDK 25 possono essere comunque usati insieme alle API 26, 27 e successive e non influisce su sviluppo per nuove piattaforme. Ciò fornirà un'interfaccia per la gestione di Android SDK per le versioni precedenti di Visual Studio.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

-   Aggiornare alla versione stabile più recente di Visual Studio 2017 per Mac come descritto [l'aggiornamento di Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/update).

-----

Per altre informazioni sul supporto di Xamarin per Android Oreo, vedere la [note sulla versione di xamarin. Android 8.0](https://docs.microsoft.com/xamarin/android/release-notes/8/8.0/).



### <a name="install-the-android-sdk"></a>Installare Android SDK

Per creare un progetto con xamarin. Android 8.0, è necessario usare Xamarin Android SDK Manager prima di installare il SDK della piattaforma per **Android 8.0 - Oreo** o versione successiva. È anche necessario installare Android SDK Tools 26.0 o versione successiva.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Avviare SDK Manager (in Visual Studio, fare clic su **strumenti > Android > Android SDK Manager**).

2. Installare il **Android 8.0 - Oreo** pacchetti. Se si usa l'emulatore di Android SDK, assicurarsi di includere il **x86** immagini del sistema che è necessario:

    [![Selezionare i pacchetti Android 8.0 in Android SDK Manager](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Installare **Android SDK Tools 26.0.2** o versioni successive **Android SDK Platform Tools 26.0.0** o versioni successive, e **Android SDK Build Tools 26.0.0** (o versioni successive):

    [![Selezione di Android SDK Tools 26 di Android SDK Manager](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Avviare SDK Manager (in Visual Studio per Mac, fare clic su **strumenti > SDK Manager**).

2. Installare il **Android 8.0 - Oreo** pacchetti SDK. Se si usa l'emulatore di Android SDK, assicurarsi di includere il **x86** immagini del sistema che è necessario:

    [![Selezionare i pacchetti Android 8.0 in SDK Manager](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Installare **Android SDK Tools 26.0.2** o versioni successive **Android SDK Platform Tools 26.0.0** o versioni successive, e **Android SDK Build Tools 26.0.0** (o versioni successive):

    [![Selezione di Android SDK Tools 26 in SDK Manager](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto xamarin. Android

Creare un nuovo progetto xamarin. Android. Se si ha familiarità con lo sviluppo di Android con Xamarin, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per altre informazioni sulla creazione di progetti xamarin. Android.

Quando si crea un progetto Android, è necessario configurare le impostazioni di versione di destinazione Android 8.0 o versione successiva. Ad esempio, per impostare come destinazione del progetto per Android 8.0, è necessario configurare il livello API Android di destinazione del progetto per **Android 8.0 (API 26)**. È consigliabile impostare il livello di framework di destinazione per API 26 o versione successiva. Per altre informazioni sulla configurazione dei livelli a livello API Android, vedere [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-an-emulator-or-device"></a>Configurare un emulatore o dispositivo

Se si tenta di avviare l'impostazione predefinita basata su GUI Google AVD Manager dopo l'installazione di Android SDK Tools 26.0 o versione successiva, potrebbe essere visualizzato il messaggio di errore seguente, è possibile usare lo strumento da riga di comando AVD manager in modo che **avdmanager** invece :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Finestra di dialogo Avviso Gestione emulatori Android](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![Finestra di dialogo Avviso Gestione emulatori Android](oreo-images/mac/03-avd-warning.png)

-----

Questo messaggio viene visualizzato perché Google non è più disponibile un computer autonomo GUI AVD manager che supporta le API 26.0 e versioni successive. Per Android Oreo 8.0, è necessario usare Xamarin Android Emulator Manager o la riga di comando `avdmanager` dello strumento per creare dispositivi virtuali per Android Oreo.

Per usare la gestione di dispositivi Android per creare e gestire i dispositivi virtuali, vedere [gestione dei dispositivi virtuali con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).
Per creare dispositivi virtuali senza Android Device Manager, seguire i passaggi nella sezione successiva.


#### <a name="creating-virtual-devices-using-avdmanager"></a>Creazione di dispositivi virtuali usando avdmanager

Per utilizzare **avdmanager** per creare un nuovo dispositivo virtuale, seguire questa procedura:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Aprire una finestra del prompt dei comandi e impostare `JAVA_HOME` nel percorso di Java SDK nel computer. Per un'installazione tipica di Xamarin, è possibile usare il comando seguente:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2.  Aggiungere il percorso di Android SDK `bin` cartella in cui il `PATH`.
    Per un'installazione tipica di Xamarin, è possibile usare il comando seguente:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3.  Chiudere la finestra del prompt dei comandi e aprire una nuova finestra del prompt dei comandi. Creare un nuovo dispositivo virtuale tramite il [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) comando. Ad esempio, per creare un dispositivo virtuale Android denominato **AVD-Oreo-8.0** usando x86 immagine del sistema per il livello API 26, usare il comando seguente:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4.  Quando viene richiesto con **si vuole creare un profilo hardware personalizzato [n]** immessi **alcun** e accettare il profilo hardware predefinito. Se si sceglie **yes**, **avdmanager** verrà richiesto con un elenco di domande per personalizzare il profilo hardware.

Dopo aver **avdmanager** per creare il dispositivo virtuale, verrà incluso nel menu a discesa dei dispositivi:

[![Nuovo AVD aggiunto al menu a discesa dispositivo](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1.  Aprire una **Terminal** finestra e modificare la posizione della directory di strumenti Android SDK nel computer Mac. Per un'installazione tipica di Xamarin, è possibile usare il comando seguente:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2.  Creare un nuovo dispositivo virtuale tramite il [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) comando. Ad esempio, per creare un dispositivo virtuale Android denominato **AVD-Oreo-8.0** usando x86 immagine del sistema per il livello API 26, usare il comando seguente:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3.  Quando viene richiesto con **si vuole creare un profilo hardware personalizzato [n]** immessi **alcun** e accettare il profilo hardware predefinito. Se si sceglie **yes**, **avdmanager** verrà richiesto con un elenco di domande per personalizzare il profilo hardware.

Dopo aver usato **avdmanager** per creare il dispositivo virtuale, verrà incluso nel menu a discesa dei dispositivi:

[![Nuovo AVD aggiunto al menu a discesa dispositivo](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Per altre informazioni sulla configurazione di un emulatore Android per i test e debug, vedere [debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md).

Se si usa un dispositivo fisico, ad esempio un Nexus o un Pixel, è possibile aggiornare il dispositivo tramite automatico tramite gli aggiornamenti di air (OTA) o scaricare un'immagine del sistema e flash direttamente il dispositivo. Per altre informazioni sull'aggiornamento manuale del dispositivo per Android Oreo, vedere [Factory di immagini per i dispositivi di Pixel e Nexus](https://developers.google.com/android/images).



## <a name="new-features"></a>Nuove funzionalità

Android Oreo introduce un'ampia gamma di nuove caratteristiche e funzionalità, ad esempio i canali di notifica, notifica badge, i tipi di carattere personalizzati nel codice XML, i tipi di carattere scaricabile, riempimento automatico e picture in picture. Le sezioni seguenti vengono evidenziate queste funzionalità e forniscono i collegamenti che consentono di iniziare a usarle nell'app.



### <a name="notification-channels"></a>I canali di notifica

*I canali di notifica* sono categorie definite dall'app per le notifiche.
È possibile creare un canale di notifica per ogni tipo di notifica che è necessario inviare, e si possono creare canali di notifica in modo da riflettere le scelte effettuate dagli utenti dell'app. La nuova funzionalità di canali di notifica rende possibile per fornire agli utenti di controllare con granularità fine su diversi tipi di notifiche. Se si implementa un'app di messaggistica, ad esempio, è possibile creare canali di notifica separate per ogni gruppo di conversazioni creato da un utente.

[I canali di notifica](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) viene illustrato come creare un canale di notifica e usarlo per la registrazione delle notifiche locali. Per un esempio di codice reale, vedere la [canali di notifica](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) esempio; questa app di esempio consente di gestire due canali e imposta le opzioni di notifica aggiuntivi.



### <a name="notification-badges"></a>Notifica badge

Le notifiche di notifica sono piccoli punti visualizzati tramite le icone dell'app, come illustrato in questo screenshot:

[![Notifiche di notifica di esempio nelle icone delle app](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Questi punti indicano che sono presenti nuove notifiche per uno o più canali di notifica nell'app associata a tale icona app &ndash; queste sono le notifiche non ancora chiusa o utilizzata dall'utente. Gli utenti possono prolungata-fare clic su un'icona per visualizzare rapidamente le notifiche associate a un badge di notifica, ignorando o che agisce sulle notifiche nel menu di pressione prolungata tale appeaars.

Per altre informazioni sulle notifiche di notifica, vedere l'Android Developer [notifica badge](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) argomento.



### <a name="custom-fonts-in-xml"></a>Tipi di carattere personalizzati in formato XML

Android Oreo introduce *tipi di carattere in formato XML*, che rende possibile incorporare caratteri personalizzati come risorse. OpenType (**otf**) e TrueType (**ttf**) sono supportati i formati di carattere. Per aggiungere i tipi di carattere come risorse, eseguire le operazioni seguenti:

1. Creare un **risorse/tipo di carattere** cartella.

2. Copiare i file del tipo di carattere (esempio **ttf** e **otf** file) per **risorse/tipo di carattere**. 

3. Se necessario, rinominare il file di ogni tipo di carattere in modo che rispetti le convenzioni di denominazione dei file Android (ad esempio, usare solo caratteri minuscoli *-z*, *0-9*e caratteri di sottolineatura nei nomi di file). Ad esempio, il file del tipo di carattere `Pacifico-Regular.ttf` è stato possibile rinominare su un valore come `pacifico.ttf`.

4. Applicare il tipo di carattere personalizzato usando il nuovo `android:fontFamily` attributo nel layout XML. Ad esempio, il seguente `TextView` dichiarazione Usa aggiunto **pacifico.ttf** risorsa tipo di carattere:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

È anche possibile creare un file XML del tipo di carattere della famiglia che descrive più tipi di carattere, nonché i dettagli dello stile e spessore. Per altre informazioni, vedere l'Android Developer [tipi di carattere in formato XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) argomento.


### <a name="downloadable-fonts"></a>Tipi di carattere scaricabile

A partire da Android Oreo, le app possono richiedere tipi di carattere da un provider, piuttosto che da raggruppare i file in file APK. I tipi di carattere vengono scaricati dalla rete solo in base alle esigenze. Questa funzionalità riduce le dimensioni APK, risparmiando utilizzo telefono cellulare e memoria dei dati. È anche possibile usare questa funzionalità nelle versioni API Android 14 e versioni successive installando il pacchetto Android 26 Library di supporto.

Quando l'app necessita di un tipo di carattere, si crea una `FontsRequest` (che specifica il tipo di carattere per il download) e quindi passarlo a un `FontsContract` metodo per scaricare il tipo di carattere. I passaggi seguenti descrivono il processo di download del tipo di carattere in modo più dettagliato:

1.  Creare un'istanza di un [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) oggetto. 

2.  Sottoclasse e creare un'istanza [FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3.  Implementare il [FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) metodo, che viene utilizzato per gestire il completamento della richiesta del tipo di carattere.

4.  Implementare il [FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) metodo, che viene utilizzato per informare l'app di qualsiasi errore che si verificano durante il processo di richiesta del tipo di carattere.

5.  Chiamare il [FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) metodo per recuperare il tipo di carattere dal provider del tipo di carattere. 

Quando si chiama il `RequestFonts` metodo, innanzitutto verificato se il tipo di carattere è nella cache locale (da una chiamata precedente a `RequestFont`). Se non viene memorizzato, chiama il provider del tipo di carattere, recupera il tipo di carattere in modo asincrono e quindi passa i risultati all'App tramite la chiamata di `OnTypeFaceRetrieved` (metodo).

Il [i tipi di carattere scaricabile](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) esempio viene illustrato come utilizzare la funzionalità di carattere scaricabile introdotta in Android Oreo. 

Per altre informazioni sul download di tipi di carattere, vedere l'Android Developer [i tipi di carattere scaricabile](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) argomento.



### <a name="autofill"></a>Riempimento automatico

Il nuovo _Autofill_ framework in Android Oreo rende più semplice per gli utenti gestire le attività ripetitive, ad esempio account di accesso, la creazione dell'account e le transazioni con carta di credito. Gli utenti dedicare meno tempo immettere nuovamente le informazioni (che possono causare errori di input). Prima che l'app possa funzionare con il Framework di riempimento automatico, un servizio di riempimento automatico deve essere abilitato nelle impostazioni di sistema (gli utenti possono abilitare o disabilitare il riempimento automatico).

Il [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework/) esempio dimostra l'uso di Autofill Framework. Fornisce le implementazioni del client attività con le visualizzazioni che devono essere autofilled e un servizio che può fornire dati di riempimento automatico al client le attività.

Per altre informazioni su come ottimizzare l'app per il riempimento automatico e la nuova funzionalità di riempimento automatico, vedere per sviluppatori Android [Autofill Framework](https://developer.android.com/guide/topics/text/autofill.html) argomento.



### <a name="picture-in-picture-pip"></a>Picture in Picture (PIP)

Android Oreo rende possibile per un'attività da avviare nella modalità (PIP) picture in picture sovrapporre la schermata di un'altra attività. Questa funzionalità è destinata la riproduzione di video.

Per specificare che l'attività dell'app può utilizzare la modalità PIP, impostare il flag seguente su true nel manifesto Android:

```xml
android:supportsPictureInPicture
```

Per specificare come comportamento dell'attività quando è in modalità di PIP, usare il nuovo [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html) oggetto. `PictureInPictureParams` rappresenta un set di parametri utilizzati per inizializzare e aggiornare un'attività in modalità PIP (ad esempio, dell'attività preferite proporzioni). Sono stati aggiunti i seguenti nuovi metodi PIP per `Activity` in Android Oreo:

-   [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; inserisce l'attività in modalità PIP. L'attività viene posizionata nell'angolo della schermata, e il resto dello schermo viene riempito con l'attività precedente che è stata sullo schermo.

-   [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; Aggiorna impostazioni di configurazione dell'attività PIP (ad esempio, una modifica nel rapporto di aspetto).

Il [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) esempio illustra l'utilizzo di base della modalità Picture in Picture (PiP) per dispositivi portatili introdotta in Oreo. L'esempio riproduce un video che continua senza interruzioni durante il passaggio avanti e indietro tra le modalità di visualizzazione o altre attività.



### <a name="other-features"></a>Altre funzionalità

Android Oreo contiene molte altre nuove funzionalità, ad esempio i limiti di background Emoji libreria, API di posizione, supporto, a livello di colore per le app, nuovi codec audio, miglioramenti di WebView, supporto di navigazione tramite tastiera migliorata e una nuova API AAudio (audio pro) per audio a bassa latenza a prestazioni elevate, per altre informazioni su queste funzionalità, vedere l'Android Developer [API e le funzionalità di Android Oreo](https://developer.android.com/about/versions/oreo/android-8.0.html) argomento.



## <a name="behavior-changes"></a>Modifiche del comportamento

Android Oreo include una vasta gamma di sistema e le modifiche di comportamento di API che possono avere un impatto sulle funzionalità delle App esistenti. Queste modifiche sono descritte come segue.


### <a name="background-execution-limits"></a>Limiti di esecuzione in background

Per migliorare l'esperienza utente, Android Oreo impone limitazioni sulle operazioni che è possano eseguire le app durante l'esecuzione in background. Ad esempio, se l'utente è guardare un video o un gioco, un'app in esecuzione in background potrebbe compromettere le prestazioni di un'app a elevato utilizzo di video in esecuzione in primo piano. Di conseguenza, Android Oreo posiziona le restrizioni seguenti sulle App che non interagiscono direttamente con l'utente:

1.  **Limitazioni del servizio di sfondo** &ndash; quando un'app è in esecuzione in background, dispone di una finestra di alcuni minuti in cui è ancora consentito creare e usare i servizi. Al termine di tale finestra, si arresta servizio in background dell'app Android e lo considera come in corso _inattività_.

2.  **Limitazioni di trasmissione** &ndash; Android 7.0 (API 25) inserito limitazioni su trasmissioni che si registra un'app per la ricezione. Android Oreo rende più severi queste limitazioni. Ad esempio, le app di Android Oreo non più possono registrare ricevitori di trasmissioni per le trasmissioni implicite nei cui manifesti.

Per altre informazioni sui nuovi limiti di esecuzione in background, vedere l'Android Developer [limiti di esecuzione in Background](https://developer.android.com/about/versions/oreo/background.html) argomento.


### <a name="breaking-changes"></a>Modifiche di interruzione

App destinate a Android Oreo o versione successiva devono modificare le proprie App per supportare le modifiche seguenti, dove applicabile:

- Android Oreo depreca la possibilità di impostare la priorità delle singole notifiche. È invece impostare un livello di importanza consigliato quando si crea un canale di notifica. Il livello di importanza che è assegnare a un canale di notifica si applica a tutti i messaggi di notifica che si registrano ad esso.

- Per le app destinate ad Android Oreo, `PendingIntent.GetService()` non funziona a causa di un nuovo limiti imposti su servizi in esecuzione in background. Se la destinazione è Android Oreo, è consigliabile usare [PendingIntent.GetBroadcast](https://developer.xamarin.com/api/member/Android.App.PendingIntent.GetBroadcast/p/Android.Content.Context/System.Int32/Android.Content.Intent/Android.App.PendingIntentFlags/) invece.  


## <a name="sample-code"></a>Codice di esempio

Alcuni esempi di xamarin. Android sono disponibili per mostrarvi come sfruttare i vantaggi delle funzionalità di Android Oreo:

-   [NotificationsChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) illustra come usare il nuovo sistema di canali di notifica introdotto in Android Oreo. In questo esempio gestisce due canali di notifica: uno con la priorità predefinita e l'altra con priorità alta.

-   [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) illustra l'utilizzo di base della modalità Picture in Picture (PiP) per dispositivi portatili introdotta in Oreo. L'esempio riproduce un video che continua senza interruzioni durante il passaggio avanti e indietro tra le modalità di visualizzazione o altre attività.

-   [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework) illustra l'uso di Autofill Framework. Fornisce le implementazioni del client attività con le visualizzazioni che devono essere autofilled e un servizio che può fornire dati di riempimento automatico al client le attività.

-   [I tipi di carattere scaricabile](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) viene fornito un esempio di come usare la funzionalità scaricabile di carattere descritta in precedenza.

-   [EmojiCompat](https://developer.xamarin.com/samples/monodroid/android-o/EmojiCompat) viene illustrato l'utilizzo della libreria di supporto EmojiCompat. È possibile usare questa libreria per evitare che l'app da mostrare mancante emoji caratteri come caratteri "tofu".

-   [Posizione gli aggiornamenti Pendingintent](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdPendIntent) illustra l'uso dell'API Location per ottenere gli aggiornamenti sulla posizione di un dispositivo usando un `PendingIntent`.

-   [Posizione gli aggiornamenti in primo piano servizio](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdFgService) illustra come usare l'API Location per ottenere gli aggiornamenti sulla posizione di un dispositivo usando un servizio di primo piano associato e avviato.


## <a name="video"></a>Video

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Sviluppo Android 8.0 Oreo con c#**


## <a name="summary"></a>Riepilogo

Questo articolo introdotto Android Oreo e ha spiegato come installare e configurare gli strumenti e i pacchetti per lo sviluppo di xamarin. Android più recenti in Android Oreo. Fornita una panoramica delle funzionalità chiave disponibili in Android Oreo, con collegamenti a codice sorgente di esempio per diverse nuove funzionalità. Inclusi collegamenti alla documentazione API e per sviluppatori Android argomenti che consentono di iniziare la creazione di App per Android Oreo. Ha rivelato anche le più importanti modifiche al comportamento di Android Oreo che potrebbero influire sulle App esistenti.


## <a name="related-links"></a>Collegamenti correlati

- [Android 8.0 Oreo](https://developer.android.com/index.html)
