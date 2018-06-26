---
title: Funzionalità Oreo
description: Come iniziare a usare xamarin. Android per sviluppare App per la versione più recente di Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: a23072427a74119bfa339fea8a695cd13b775685
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935103"
---
# <a name="oreo-features"></a>Funzionalità Oreo

_Come iniziare a usare xamarin. Android per sviluppare App per la versione più recente di Android._

[Android Oreo 8.0](https://developer.android.com/index.html) disponibile la versione più recente di Android da Google. Android Oreo offre molte nuove caratteristiche di interesse per gli sviluppatori di xamarin. Android. Queste funzionalità includono i canali di notifica, badge di notifica, i tipi di carattere personalizzati nel XML, i tipi di carattere scaricabile, riempimento automatico e un'immagine in immagine (PIP). Android Oreo include nuove API per questi nuovi capabilties e queste API sono disponibili per le app xamarin quando si usa xamarin. Android 8.0 e versioni successive.

[![Immagine di hero Oreo Android](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

In questo articolo è strutturato per agevolare lo sviluppo di xamarin. Android le App per Android Oreo 8.0. Viene spiegato come installare gli aggiornamenti necessari, configurare il SDK e creare un emulatore di dispositivo (o) per il testing. Fornisce inoltre una struttura di nuove funzionalità di Android Oreo 8.0, con collegamenti alle app di esempio illustrano come usare le funzionalità di Android Oreo nelle App xamarin. Android.


## <a name="requirements"></a>Requisiti

Di seguito è necessario utilizzare le funzionalità di Android Oreo nelle App basate su Xamarin:

-   **Visual Studio** &ndash; se si utilizza Windows, è necessario 15,5 o versione successiva di Visual Studio.  Se si utilizza un computer Mac, è necessario Visual Studio per Mac versione 7.2.0.

-   **Xamarin. Android** &ndash; xamarin 8.0 o versione successiva deve essere installato e configurato con Visual Studio.

-   **Android SDK** &ndash; Android SDK 8.0 (API 26) o versioni successive deve essere installato mediante Android SDK Manager.



## <a name="getting-started"></a>Introduzione

Per iniziare a usare Oreo Android con xamarin. Android, è necessario scaricare e installare i pacchetti SDK e gli strumenti più recenti prima di poter creare un progetto Android Oreo:

1. Aggiornare la versione più recente di Visual Studio.

2. Installare il **8.0.0 Android (API 26)** o versione successiva i pacchetti e strumenti tramite SDK Manager.

3. Crea un nuovo progetto di xamarin. Android destinato Oreo Android (API 26).

4. Configurare un emulatore o dispositivo per test di App Android Oreo.

Ognuno di questi passaggi verrà illustrato nelle sezioni seguenti:



### <a name="update-visual-studio-and-xamarinandroid"></a>Aggiornamento di Visual Studio e xamarin. Android

Per aggiungere supporto Oreo Android in Visual Studio, eseguire le operazioni seguenti:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   Se si utilizza Visual Studio 2017: 

    1. Aggiornamento a Visual Studio 2017 15,5 o versione successiva (vedere [aggiornamento Visual Studio 2017](https://docs.microsoft.com/en-us/visualstudio/install/update-visual-studio)).

    2. Usare il SDK Manager [le istruzioni di installazione](~/android/get-started/installation/android-sdk.md#installation) installare Xamarin SDK Manager.
       Perché Google non fornisce un standlone manager GUI SDK che supporta le API 26,0 e versioni successive, è necessario installare Xamarin SDK Manager.

-   Se si utilizza Visual Studio 2015, è consigliabile downgrade strumenti SDK a 25 e utilizzando Gestione emulatori di Google precedente GUI. Strumenti SDK 25 possono ancora essere utilizzati insieme alle API 26, 27 e successive e non influisce sullo sviluppo per nuove piattaforme. Questa query fornirà un'interfaccia per la gestione dell'Android SDK per le versioni precedenti di Visual Studio.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

-   Aggiornare alla versione stabile più recente di Visual Studio 2017 per Mac come descritto nei [l'aggiornamento di Visual Studio per Mac](https://docs.microsoft.com/en-us/visualstudio/mac/update).

-----

Per ulteriori informazioni sul supporto di Xamarin per Oreo Android, vedere la [note sulla versione di xamarin. Android 8.0](https://developer.xamarin.com/releases/android/xamarin.android_8/xamarin.android_8.0/).



### <a name="install-the-android-sdk"></a>Installare Android SDK

Per creare un progetto con xamarin. Android 8.0, è necessario utilizzare innanzitutto Xamarin Android SDK Manager per la piattaforma SDK per installare **8.0 Android - Oreo** o versione successiva. È inoltre necessario installare strumenti Android SDK 26,0 o versione successivo.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Avviare Gestione SDK (in Visual Studio, fare clic su **strumenti > Android > Android SDK Manager**).

2. Installare il **8.0 Android - Oreo** pacchetti. Se si utilizza l'emulatore di Android SDK, assicurarsi di includere il **x86** immagini del sistema che è necessario:

    [![Selezionare i pacchetti Android 8.0 in Android SDK Manager](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Installare **strumenti Android SDK 26.0.2** o versione successiva **strumenti Android SDK Platform-26.0.0** o in un secondo momento, e **strumenti Android SDK Build-26.0.0** (o versione successiva):

    [![Selezionare gli strumenti di Android SDK 26 in Android SDK Manager](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Avviare Gestione SDK (in Visual Studio per Mac, fare clic su **strumenti > SDK Manager**).

2. Installare il **8.0 Android - Oreo** pacchetti SDK. Se si utilizza l'emulatore di Android SDK, assicurarsi di includere il **x86** immagini del sistema che è necessario:

    [![Selezionare i pacchetti Android 8.0 in SDK Manager](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Installare **strumenti Android SDK 26.0.2** o versione successiva **strumenti Android SDK Platform-26.0.0** o in un secondo momento, e **strumenti Android SDK Build-26.0.0** (o versione successiva):

    [![Selezionare gli strumenti di Android SDK 26 in SDK Manager](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto xamarin

Creare un nuovo progetto di xamarin. Android. Se si ha familiarità con lo sviluppo di Android con Xamarin, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per informazioni sulla creazione di progetti di xamarin. Android.

Quando si crea un progetto Android, è necessario configurare le impostazioni di versione di destinazione Android 8.0 o versione successiva. Ad esempio, per destinare il progetto per Android 8.0, è necessario configurare il livello API Android di destinazione del progetto per **Android 8.0 (API 26)**. È consigliabile impostare il livello di framework di destinazione a 26 API o versioni successive. Per ulteriori informazioni sulla configurazione dei livelli a livello di API Android, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-an-emulator-or-device"></a>Configurare un emulatore o dispositivo

Se si tenta di avviare il valore predefinito basato su interfaccia utente grafica di Google AVD Manager dopo l'installazione di Android SDK strumenti 26.0 o versioni successive, è possibile che venga visualizzato il seguente messaggio di errore, è possibile utilizzare lo strumento della riga di comando AVD manager in modo che **avdmanager** invece :

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Finestra di dialogo Avviso Android Emulator Manager](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Finestra di dialogo Avviso Android Emulator Manager](oreo-images/mac/03-avd-warning.png)

-----

Questo messaggio viene visualizzato perché Google non è più disponibile un standlone GUI AVD manager che supporta le API 26,0 e versioni successive. Per Android Oreo 8.0, è necessario usare Xamarin Android Emulator Manager o la riga di comando `avdmanager` strumento per creare i dispositivi virtuali per Oreo Android.

Per usare la gestione di dispositivi Android per creare e gestire i dispositivi virtuali, vedere [gestione dei dispositivi virtuali con Gestione dispositivi Android](~/android/get-started/installation/android-emulator/device-manager.md).
Per creare i dispositivi virtuali senza gestione dispositivi Android, seguire i passaggi nella sezione successiva.


#### <a name="creating-virtual-devices-using-avdmanager"></a>Creazione tramite i dispositivi virtuali avdmanager

Per utilizzare **avdmanager** per creare un nuovo dispositivo virtuale, seguire questi passaggi:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Aprire una finestra del prompt dei comandi e impostare `JAVA_HOME` nel percorso di Java SDK nel computer. Per un'installazione tipica di Xamarin, è possibile utilizzare il comando seguente:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2.  Aggiungere il percorso di Android SDK `bin` cartella in cui il `PATH`.
    Per un'installazione tipica di Xamarin, è possibile utilizzare il comando seguente:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3.  Chiudere la finestra del prompt dei comandi e aprire una nuova finestra del prompt dei comandi. Creare un nuovo dispositivo virtuale utilizzando il [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) comando. Ad esempio, per creare un AVD denominato **AVD-Oreo-8.0** x86 immagine del sistema per il livello di API 26, utilizzare il comando seguente:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4.  Quando viene chiesto **si desidera creare un profilo hardware personalizzato [n]** è possibile immettere **nessun** e accettare il profilo hardware predefinito. Se si sceglie **yes**, **avdmanager** si visualizzerà un elenco di domande per personalizzare il profilo hardware.

Dopo aver **avdmanager** per creare il dispositivo virtuale, verrà incluso nel menu a discesa di dispositivo:

[![Nuovo AVD aggiunto al menu a discesa di dispositivo](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1.  Aprire una **Terminal** finestra e modificare la posizione della directory di strumenti di Android SDK nel Mac. Per un'installazione tipica di Xamarin, è possibile utilizzare il comando seguente:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2.  Creare un nuovo dispositivo virtuale utilizzando il [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) comando. Ad esempio, per creare un AVD denominato **AVD-Oreo-8.0** x86 immagine del sistema per il livello di API 26, utilizzare il comando seguente:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3.  Quando viene chiesto **si desidera creare un profilo hardware personalizzato [n]** è possibile immettere **nessun** e accettare il profilo hardware predefinito. Se si sceglie **Sì**, **avdmanager** si visualizzerà un elenco di domande per la personalizzazione il profilo hardware.

Dopo aver utilizzato **avdmanager** per creare il dispositivo virtuale, verrà incluso nel menu a discesa di dispositivo:

[![Nuovo AVD aggiunto al menu a discesa di dispositivo](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Per ulteriori informazioni sulla configurazione di un emulatore Android di test e debug, vedere [debug nell'emulatore Android](~/android/deploy-test/debugging/debug-on-emulator.md).

Se si utilizza un dispositivo fisico, ad esempio un nodo o un Pixel, è possibile aggiornare il dispositivo tramite automatico sugli aggiornamenti air (OTA) o scaricare un'immagine del sistema e flash direttamente il dispositivo. Per ulteriori informazioni sull'aggiornamento manuale del dispositivo per Oreo Android, vedere [immagini Factory per i dispositivi Pixel e Nexus](https://developers.google.com/android/images).



## <a name="new-features"></a>Nuove funzionalità

Android Oreo introduce tutta una serie di nuove caratteristiche e funzionalità, ad esempio i canali di notifica, badge di notifica, caratteri personalizzati nel XML, i tipi di carattere scaricabile, riempimento automatico e immagine in immagine. Nelle sezioni seguenti evidenziano queste funzionalità e forniscono i collegamenti che consentono di iniziare a usarli nell'app.



### <a name="notification-channels"></a>I canali di notifica

*I canali di notifica* sono categorie definite dall'app per le notifiche.
È possibile creare un canale di notifica per ogni tipo di notifica che è necessario per l'invio ed è possibile creare canali di notifica per riflettere le scelte effettuate dagli utenti dell'app. La nuova funzionalità di canali di notifica rende possibile per fornire un controllo accurato utenti diversi tipi di notifiche. Se si sta implementando un'app di messaggistica, ad esempio, è possibile creare canali di notifica separato per ogni gruppo di conversazioni creato da un utente.

[I canali di notifica](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) viene illustrato come creare un canale di notifica e usarlo per la registrazione delle notifiche locale. Per un esempio di codice del mondo reale, vedere la [NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) sample; questa app di esempio gestisce due canali e imposta le opzioni di notifica aggiuntivo.



### <a name="notification-badges"></a>Notifica badge

Le notifiche di notifica sono piccoli punti che vengono visualizzati sulle icone di app, come mostrato in questo screenshot:

[![Notifiche di notifica di esempio sulle icone di app](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Questi punti indicano che sono presenti nuove notifiche per uno o più canali di notifica nell'app associata tale icona app &ndash; sono le notifiche che l'utente ha chiuso non ancora o intervenire. Gli utenti possono prolungata-premere su un'icona per osservare le notifiche associate a un badge di notifica, ignorando o che agiscono sulle notifiche dal menu di pressione prolungata tale appeaars.

Per ulteriori informazioni sui simboli di notifica, vedere Android Developer [notifica badge](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) argomento.



### <a name="custom-fonts-in-xml"></a>Tipi di carattere personalizzati in XML

Android Oreo introduce *tipi di carattere in XML*, che rende possibile incorporare caratteri personalizzati come risorse. OpenType (**otf**) e TrueType (**ttf**) sono supportati i formati di tipo di carattere. Per aggiungere i tipi di carattere come risorse, eseguire le operazioni seguenti:

1. Creare una **risorse/tipo di carattere** cartella.

2. Copiare i file del tipo di carattere (esempio **ttf** e **otf** file) a **risorse/tipo di carattere**. 

3. Se necessario, rinominare il file di ogni tipo di carattere in modo che rispetti le convenzioni di denominazione dei file Android (ad esempio, utilizzare solo caratteri minuscoli *-z*, *0-9*e caratteri di sottolineatura nei nomi dei file). Ad esempio, il file del tipo di carattere `Pacifico-Regular.ttf` è stato possibile rinominare un nome, ad esempio `pacifico.ttf`.

4. Applicare il tipo di carattere personalizzato tramite la nuova `android:fontFamily` attributo nel layout XML. Ad esempio, il seguente `TextView` dichiarazione Usa aggiunto **pacifico.ttf** risorse di tipo di carattere:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

È anche possibile creare un file XML di famiglia del tipo di carattere che descrive più tipi di carattere, nonché i dettagli di uno stile e spessore. Per altre informazioni, vedere Android Developer [tipi di carattere in XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) argomento.


### <a name="downloadable-fonts"></a>Tipi di carattere trasferibili

A partire da Android Oreo, le applicazioni possono richiedere tipi di carattere da un provider, piuttosto che da essi unendo nell'APK. I tipi di carattere vengono scaricati dalla rete solo in base alle esigenze. Questa funzionalità riduce le dimensioni di file APK, conservando l'utilizzo di dati di memoria e rete cellulare phone. È inoltre possibile utilizzare questa funzionalità sul versioni API Android 14 e versioni successive installando il pacchetto Android 26 della libreria di supporto.

Quando l'app deve un tipo di carattere, si crea un `FontsRequest` oggetto (che specifica il tipo di carattere per il download) e quindi lo si passa a un `FontsContract` metodo per scaricare il tipo di carattere. I passaggi seguenti descrivono il processo di download del tipo di carattere in modo più dettagliato:

1.  Creare un'istanza di un [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) oggetto. 

2.  Sottoclasse e creare un'istanza [FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3.  Implementare il [FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) metodo, che viene utilizzato per gestire il completamento della richiesta del tipo di carattere.

4.  Implementare il [FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) metodo, che viene utilizzato per informare l'app di qualsiasi errore che si verificano durante il processo di richiesta del tipo di carattere.

5.  Chiamare il [FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) metodo per recuperare il tipo di carattere dal provider di tipo di carattere. 

Quando si chiama il `RequestFonts` metodo, controllato innanzitutto se il tipo di carattere è nella cache locale (da una precedente chiamata a `RequestFont`). Se non viene memorizzato, chiama il provider di tipo di carattere, recupera il tipo di carattere in modo asincrono e quindi passa i risultati all'App richiamando il `OnTypeFaceRetrieved` metodo.

Il [tipi di carattere trasferibili](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) esempio viene illustrato come utilizzare la funzionalità dei tipi di carattere scaricabile introdotta in Oreo Android. 

Per ulteriori informazioni sul download dei tipi di carattere, vedere Android Developer [tipi di carattere trasferibili](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) argomento.



### <a name="autofill"></a>Riempimento automatico

Il nuovo _riempimento automatico_ framework in Android Oreo rende più semplice per gli utenti di gestire le attività ripetitive, ad esempio account di accesso, la creazione dell'account e le transazioni con carta di credito. Gli utenti operano per meno tempo digitando nuovamente informazioni (che possono causare errori di input). Prima che l'app possa operare con il Framework di riempimento automatico, un servizio di riempimento automatico deve essere abilitato nelle impostazioni del sistema (gli utenti possono abilitare o disabilitare il riempimento automatico).

Il [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework/) esempio dimostra l'uso di Framework riempimento automatico. Include le implementazioni di client attività con le viste che devono essere autofilled e un servizio che può fornire dati di riempimento automatico al client le attività.

Per ulteriori informazioni su come ottimizzare l'app per il riempimento automatico e la nuova funzionalità di riempimento automatico, vedere Android Developer [Framework riempimento automatico](https://developer.android.com/guide/topics/text/autofill.html) argomento.



### <a name="picture-in-picture-pip"></a>Un'immagine in immagine (PIP)

Android Oreo rende possibile per un'attività devono essere avviati in modalità di immagine in immagine (PIP), la sovrapposizione la schermata di un'altra attività. Questa funzionalità è destinata la riproduzione video.

Per specificare che l'attività dell'app possono utilizzare modalità PIP, impostare il flag seguente su true nel manifesto Android:

```xml
android:supportsPictureInPicture
```

Per specificare il comportamento dell'attività quando è in modalità PIP, usare il nuovo [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html) oggetto. `PictureInPictureParams` rappresenta un set di parametri utilizzati per inizializzare e aggiornare un'attività in modalità PIP (ad esempio, dell'attività preferito proporzioni). Sono stati aggiunti i seguenti nuovi metodi PIP `Activity` in Oreo Android:

-   [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; inserisce l'attività in modalità PIP. L'attività viene posizionato nell'angolo della schermata, e il resto della schermata viene compilato con l'attività precedente è stata sullo schermo.

-   [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; Aggiorna impostazioni di configurazione dell'attività PIP (ad esempio, una modifica delle proporzioni).

Il [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) esempio viene illustrato l'utilizzo di base della modalità di immagine in immagine (PiP) introdotto in Oreo nei dispositivi palmari. L'esempio riproduce un video che continua senza interruzioni durante il passaggio avanti e indietro tra le modalità di visualizzazione o altre attività.



### <a name="other-features"></a>Altre funzionalità

Android Oreo contiene molte altre nuove funzionalità, ad esempio Emoji limiti di supporto per API di percorso, di libreria, in background, ampia colore per le app, nuovi codec audio, miglioramenti WebView, supporto di navigazione della tastiera migliorate e una nuova API AAudio (audio pro) per audio a bassa latenza ad alte prestazioni, per ulteriori informazioni su queste funzionalità, vedere Android Developer [Android Oreo funzionalità e API](https://developer.android.com/about/versions/oreo/android-8.0.html) argomento.



## <a name="behavior-changes"></a>Modifiche del comportamento

Oreo Android inclusa una varietà di sistema e le modifiche di comportamento API che possono avere un impatto sulle funzionalità delle App esistenti. Tali modifiche sono descritte come segue.


### <a name="background-execution-limits"></a>Limiti di esecuzione in background

Per migliorare l'esperienza utente, Oreo Android impone limitazioni sulle operazioni che è possano eseguire le app durante l'esecuzione in background. Ad esempio, se l'utente è guardare un video o un gioco, un'app in esecuzione in background potrebbe compromettere le prestazioni di un'applicazione a elevato utilizzo di video in esecuzione in primo piano. Di conseguenza, Oreo Android posiziona le restrizioni seguenti sulle App che non interagiscono direttamente con l'utente:

1.  **Limitazioni del servizio di sfondo** &ndash; quando un'applicazione viene eseguita in background, ha una finestra di alcuni minuti in cui è ancora consentito creare e utilizzare i servizi. Al termine di tale finestra, si arresta servizio in background dell'app Android e lo considera come in corso _inattivo_.

2.  **Limitazioni di trasmissione** &ndash; Android 7.0 (API 25) posizionato limitazioni su trasmissioni che si registra un'app per la ricezione. Android Oreo rende più severi queste limitazioni. Ad esempio, le app Android Oreo non più possono registrare ricevitori broadcast per le trasmissioni implicite nei cui manifesti.

Per ulteriori informazioni sui nuovi limiti di esecuzione in background, vedere Android Developer [i limiti di esecuzione in Background](https://developer.android.com/about/versions/oreo/background.html) argomento.


### <a name="breaking-changes"></a>Modifiche di interruzione

App di Android Oreo di destinazione o su un valore maggiore necessario modificare le proprie App per supportare le modifiche seguenti, dove applicabile:

- Android Oreo depreca la possibilità di impostare la priorità delle singole notifiche. Al contrario, impostare un livello di importanza consigliata quando si crea un canale di notifica. Il livello di importanza che si assegna a un canale di notifica si applica a tutti i messaggi di notifica che la registrazione a esso.

- Per le app destinate ad Android Oreo, `PendingIntent.GetService()` non funziona perché nuovi limiti imposti su servizi in esecuzione in background. Se la destinazione è Android Oreo, è necessario utilizzare [PendingIntent.GetBroadcast](https://developer.xamarin.com/api/member/Android.App.PendingIntent.GetBroadcast/p/Android.Content.Context/System.Int32/Android.Content.Intent/Android.App.PendingIntentFlags/) invece.  


## <a name="sample-code"></a>Codice di esempio

Alcuni esempi di xamarin. Android sono disponibili per viene illustrato come sfruttare le funzionalità di Android Oreo:

-   [NotificationsChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) viene illustrato come utilizzare il nuovo sistema di canali di notifica introdotto in Oreo Android. In questo esempio gestisce due canali di notifica: uno con la priorità predefinita e l'altro con priorità alta.

-   [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) viene illustrato l'utilizzo di base della modalità di immagine in immagine (PiP) introdotto in Oreo nei dispositivi palmari. L'esempio riproduce un video che continua senza interruzioni durante il passaggio avanti e indietro tra le modalità di visualizzazione o altre attività.

-   [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework) viene illustrato come utilizzare il Framework di riempimento automatico. Include le implementazioni di client attività con le viste che devono essere autofilled e un servizio che può fornire dati di riempimento automatico al client le attività.

-   [Tipi di carattere trasferibili](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) fornisce un esempio di come utilizzare la funzionalità dei tipi di carattere scaricabile descritta in precedenza.

-   [EmojiCompat](https://developer.xamarin.com/samples/monodroid/android-o/EmojiCompat) viene illustrato l'utilizzo della libreria di supporto EmojiCompat. È possibile utilizzare questa libreria per evitare che l'app da caratteri emoji mancanti che mostra come caratteri "tofu".

-   [Posizione gli aggiornamenti in sospeso finalità](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdPendIntent) illustra l'utilizzo dell'API di posizione per ottenere gli aggiornamenti sulla posizione di un dispositivo utilizzando un `PendingIntent`.

-   [Posizione gli aggiornamenti in primo piano servizio](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdFgService) viene illustrato come utilizzare l'API di posizione per ottenere gli aggiornamenti sulla posizione di un dispositivo utilizzando un servizio di primo piano associato e avviato.


## <a name="video"></a>Video

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**8.0 Oreo lo sviluppo di Android con c#**


## <a name="summary"></a>Riepilogo

In questo articolo introdotte Oreo Android e viene illustrato come installare e configurare gli strumenti e i pacchetti per lo sviluppo di xamarin. Android più recenti su Oreo Android. Fornita una panoramica delle funzionalità principali disponibile in Android Oreo, con collegamenti ad esempi di codice sorgente per numerose nuove funzionalità. Inclusi i collegamenti alla documentazione API e sviluppatori di Android argomenti che consentono di iniziare la creazione di App per Android Oreo. Inoltre evidenziato le più importanti modifiche al comportamento Oreo Android che potrebbero influire sulle App esistenti.


## <a name="related-links"></a>Collegamenti correlati

- [Android Oreo 8.0](https://developer.android.com/index.html)
