---
title: "Funzionalità Oreo"
description: "Come iniziare a usare xamarin per sviluppare App per la versione più recente di Android."
ms.topic: article
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 03be7b624ffa9dd8774f291b96be27499cccab2b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="oreo-features"></a>Funzionalità Oreo

_Come iniziare a usare xamarin per sviluppare App per la versione più recente di Android._

[Android Oreo 8.0](https://developer.android.com/index.html) disponibile la versione più recente di Android da Google. Android Oreo offre molte nuove caratteristiche di interesse per gli sviluppatori di xamarin. Queste funzionalità includono i canali di notifica, il badge di notifica, i tipi di carattere personalizzati in XML, tipi di carattere trasferibili, riempimento automatico e immagine in immagine (PIP). Android Oreo include nuove API per questi nuovi capabilties queste API sono disponibili per le app xamarin quando si usa xamarin 8.0 e versioni successive.

[![Immagine di hero Oreo Android](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

In questo articolo è strutturato per iniziare lo sviluppo di xamarin App per Android Oreo 8.0. Viene spiegato come installare gli aggiornamenti necessari, configurare il SDK e creare un emulatore di dispositivo (o) per il test. Fornisce inoltre una struttura di nuove funzionalità di Android Oreo 8.0, con collegamenti alle applicazioni di esempio che illustrano come utilizzare le funzionalità di Android Oreo in App xamarin.


## <a name="requirements"></a>Requisiti

È necessario utilizzare le funzionalità di Android Oreo nelle App basate su Xamarin:

-   **Visual Studio** &ndash; se si utilizza Windows, è necessario 15,5 o versione successiva di Visual Studio.  Se si utilizza un computer Mac, è necessario Visual Studio per Mac versione 7.2.0.

-   **Xamarin** &ndash; xamarin 8.0 o versione successiva deve essere installato e configurato con Visual Studio.

-   **Android SDK** &ndash; Android SDK 8.0 (API 26) o versioni successive deve essere installato mediante Android SDK Manager.



## <a name="getting-started"></a>Introduzione

Per iniziare a utilizzare Oreo Android con xamarin, è necessario scaricare e installare il più recente degli strumenti e i pacchetti SDK prima di poter creare un progetto Android Oreo:

1. Aggiornare alla versione più recente di Visual Studio.

2. Installare il **8.0.0 Android (API 26)** o versione successiva i pacchetti e gli strumenti di gestione tramite SDK.

3. Creare un nuovo progetto xamarin Android Oreo (API 26) che ha come destinazione.

4. Configurare un emulatore o dispositivo per test di App Android Oreo.

Ognuno di questi passaggi verrà illustrato nelle sezioni seguenti:



### <a name="update-visual-studio-and-xamarinandroid"></a>Aggiornamento di Visual Studio e xamarin

Per aggiungere il supporto Android Oreo a Visual Studio, eseguire le operazioni seguenti:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   Se si utilizza Visual Studio 2017: 

    1. Aggiornamento di Visual Studio 2017 15,5 o versione successiva (vedere [aggiornamento Visual Studio 2017](https://docs.microsoft.com/en-us/visualstudio/install/update-visual-studio)).

    2. Usare il SDK Manager [le istruzioni di installazione](~/android/get-started/installation/android-sdk.md#installation) installare Xamarin SDK Manager.
       Perché Google non fornisce un standlone gestione GUI SDK che supporta l'API 26,0 e versioni successive, è necessario installare Xamarin SDK Manager.

-   Se si utilizza Visual Studio 2015, è consigliabile downgrade strumenti SDK a 25 e utilizzando Gestione emulatori di Google precedente GUI. Strumenti SDK 25 possono ancora essere utilizzati insieme alle API 26, 27 e successive e non influiscono sullo sviluppo per nuove piattaforme. Questa query fornirà un'interfaccia per la gestione dell'Android SDK per le versioni precedenti di Visual Studio.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

-   Aggiornare alla versione stabile più recente di Visual Studio 2017 per Mac come descritto nei [l'aggiornamento di Visual Studio per Mac](https://docs.microsoft.com/en-us/visualstudio/mac/update).

-----

Per ulteriori informazioni sul supporto di Xamarin per Oreo Android, vedere il [note sulla versione di xamarin 8.0](https://developer.xamarin.com/releases/android/xamarin.android_8/xamarin.android_8.0/).



### <a name="install-the-android-sdk"></a>Installare Android SDK

Per creare un progetto con xamarin 8.0, è necessario utilizzare innanzitutto Xamarin Android SDK Manager per installare il platform SDK per **8.0 Android - Oreo** o versione successiva. È inoltre necessario installare strumenti Android SDK 26,0 o versione successivo.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Avviare Gestione SDK (in Visual Studio, fare clic su **strumenti > Android > Android SDK Manager**).

2. Installare il **8.0 Android - Oreo** pacchetti. Se si utilizza l'emulatore di Android SDK, assicurarsi di includere il **x86** immagini del sistema che è necessario:

    [![Selezionare i pacchetti Android 8.0 in Android SDK Manager](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Installare **strumenti Android SDK 26.0.2** o versioni successive, **strumenti Android SDK Platform-26.0.0** o versione successiva e **strumenti Android SDK Build-26.0.0** (o versione successiva):

    [![Selezionare gli strumenti di Android SDK 26 nel Android SDK Manager](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Avviare Gestione SDK (in Visual Studio per Mac, fare clic su **strumenti > Gestione SDK**).

2. Installare il **8.0 Android - Oreo** pacchetti SDK. Se si utilizza l'emulatore di Android SDK, assicurarsi di includere il **x86** immagini del sistema che è necessario:

    [![Se si seleziona pacchetti 8.0 Android SDK Manager](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Installare **strumenti Android SDK 26.0.2** o versioni successive, **strumenti Android SDK Platform-26.0.0** o versione successiva e **strumenti Android SDK Build-26.0.0** (o versione successiva):

    [![Selezionare gli strumenti di Android SDK 26 nel SDK Manager](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>Avviare un progetto xamarin

Creare un nuovo progetto di xamarin. Se si ha familiarità con lo sviluppo di Android con Xamarin, vedere [Hello, Android](~/android/get-started/hello-android/index.md) per informazioni sulla creazione di progetti di xamarin.

Quando si crea un progetto Android, è necessario configurare le impostazioni della versione di destinazione Android 8.0 o versione successiva. Ad esempio, per la destinazione del progetto per Android 8.0, è necessario configurare il livello API Android di destinazione del progetto per **Android 8.0 (API 26)**. È consigliabile impostare il livello di framework di destinazione a 26 API o versioni successive. Per ulteriori informazioni sulla configurazione dei livelli di livello API Android, vedere [informazioni sui livelli di API Android](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-an-emulator-or-device"></a>Configurare un emulatore o dispositivo

Se si tenta di avviare il valore predefinito basato su interfaccia utente grafica di Google AVD Manager dopo l'installazione di Android SDK strumenti 26.0 o versioni successive, potrebbe essere visualizzato il seguente messaggio di errore che indica di usare lo strumento di gestione della riga di comando AVD **avdmanager** invece :

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Finestra di dialogo Avviso Android Emulator Manager](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

![Finestra di dialogo Avviso Android Emulator Manager](oreo-images/mac/03-avd-warning.png)

-----

Questo messaggio viene visualizzato perché Google non è più disponibile un standlone gestore AVD GUI che supporta l'API 26,0 e versioni successive. Per Android Oreo 8.0, è necessario usare Xamarin Android Emulator Manager o la riga di comando `avdmanager` strumento per la creazione di dispositivi virtuali per Oreo Android.

Per utilizzare la gestione di dispositivi Android di Xamarin per creare e gestire i dispositivi virtuali, vedere [gestione di dispositivi Android di Xamarin](~/android/get-started/installation/android-emulator/xamarin-device-manager.md).
Per creare i dispositivi virtuali senza Xamarin Android Emulator Manager, seguire i passaggi nella sezione successiva.


#### <a name="creating-virtual-devices-using-avdmanager"></a>Creazione tramite i dispositivi virtuali avdmanager

Per utilizzare **avdmanager** per creare un nuovo dispositivo virtuale, seguire questi passaggi:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Aprire una finestra del prompt dei comandi e impostare `JAVA_HOME` nel percorso di Java SDK nel computer in uso. Per un'installazione tipica di Xamarin, è possibile utilizzare il comando seguente:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2.  Aggiungere il percorso di Android SDK `bin` cartella in cui il `PATH`.
    Per un'installazione tipica di Xamarin, è possibile utilizzare il comando seguente:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3.  Chiudere la finestra del prompt dei comandi e aprire una finestra del prompt dei comandi. Creare un nuovo dispositivo virtuale utilizzando il [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) comando. Ad esempio, per creare un AVD denominato **AVD-Oreo-8.0** x86 immagine del sistema per il livello di API 26, utilizzare il comando seguente:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4.  Quando viene richiesto con **si desidera creare un profilo hardware personalizzate [no]** è possibile immettere **non** e accettare il profilo hardware predefinito. Se si risponde **Sì**, **avdmanager** si visualizzerà un elenco di domande per personalizzare il profilo hardware.

Dopo aver **avdmanager** per creare il dispositivo virtuale, verrà incluso nel menu a discesa di dispositivo:

[![Nuovo AVD aggiunto al menu a discesa di dispositivo](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1.  Aprire un **Terminal** finestra e modificare la posizione della directory di strumenti di Android SDK nel Mac. Per un'installazione tipica di Xamarin, è possibile utilizzare il comando seguente:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2.  Creare un nuovo dispositivo virtuale utilizzando il [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) comando. Ad esempio, per creare un AVD denominato **AVD-Oreo-8.0** x86 immagine del sistema per il livello di API 26, utilizzare il comando seguente:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3.  Quando viene richiesto con **si desidera creare un profilo hardware personalizzate [no]** è possibile immettere **non** e accettare il profilo hardware predefinito. Se si risponde **Sì**, **avdmanager** si visualizzerà un elenco di domande per la personalizzazione il profilo hardware.

Dopo aver utilizzato **avdmanager** per creare il dispositivo virtuale, verrà incluso nel menu a discesa di dispositivo:

[![Nuovo AVD aggiunto al menu a discesa di dispositivo](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Per ulteriori informazioni sulla configurazione di un emulatore Android di test e debug, vedere [emulatore di Android SDK](~/android/deploy-test/debugging/android-sdk-emulator/index.md).

Se si utilizza un dispositivo fisico, ad esempio un nodo o un Pixel, è possibile aggiornare il dispositivo tramite automatico sugli aggiornamenti air (OTA) o scaricare un'immagine del sistema e flash direttamente il dispositivo. Per ulteriori informazioni sull'aggiornamento manuale del dispositivo per Oreo Android, vedere [immagini Factory per i dispositivi di Pixel e Nexus](https://developers.google.com/android/images).



## <a name="new-features"></a>Nuove funzionalità

Android Oreo introduce varie nuove caratteristiche e funzionalità, ad esempio canali di notifica, badge di notifica, i tipi di carattere personalizzati nel codice XML, tipi di carattere trasferibili, riempimento automatico e picture in picture. Nelle sezioni seguenti evidenziano queste funzionalità e forniscono i collegamenti che consentono di iniziare a usarli nell'app.



### <a name="notification-channels"></a>Canali di notifica

*Canali di notifica* sono categorie definite dall'applicazione per le notifiche.
È possibile creare un canale di notifica per ogni tipo di notifica che è necessario per l'invio ed è possibile creare canali di notifica per riflettere le scelte effettuate dagli utenti dell'app. La nuova funzionalità di canali di notifica rende possibile per fornire un controllo accurato utenti diversi tipi di notifiche. Se si implementa un'applicazione di messaggistica, ad esempio, è possibile creare canali di notifica separato per ogni gruppo di conversazioni creato da un utente.

[Canali di notifica](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) viene illustrato come creare un canale di notifica e utilizzarlo per la registrazione delle notifiche locale. Per un esempio di codice del mondo reale, vedere il [NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) esempio; questa app di esempio gestisce due canali e imposta le opzioni di notifica aggiuntivo.



### <a name="notification-badges"></a>Notifica badge

Le notifiche di notifica sono piccoli punti visualizzati sulle icone di app, come illustrato in questo screenshot:

[![Notifiche di notifica di esempio sulle icone di app](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Questi punti indicano che esistono nuove notifiche per uno o più canali di notifica nell'app associata a tale icona app &ndash; queste sono le notifiche che l'utente ha non ancora chiuso o intervenire. Gli utenti possono long-premere su un'icona per osservare le notifiche associate a un badge di notifica, ignorando o agisce sulle notifiche dal menu di pressione prolungata che appeaars.

Per ulteriori informazioni sui simboli di notifica, vedere Android Developer [notifica badge](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) argomento.



### <a name="custom-fonts-in-xml"></a>Tipi di carattere personalizzati in XML

Introduce Android Oreo *tipi di carattere in XML*, che consente di incorporare caratteri personalizzati come risorse. OpenType (**otf**) e TrueType (**ttf**) sono supportati i formati di carattere. Per aggiungere tipi di carattere come risorse, eseguire le operazioni seguenti:

1. Creare un **risorse/tipo di carattere** cartella.

2. Copiare i file del tipo di carattere (esempio **ttf** e **otf** file) per **risorse/tipo di carattere**. 

3. Se necessario, rinominare il file di ogni tipo di carattere in modo che rispetti le convenzioni di denominazione dei file Android (ad esempio, utilizzare solo caratteri minuscoli *-z*, *0-9*e caratteri di sottolineatura nei nomi di file). Ad esempio, il file del tipo di carattere `Pacifico-Regular.ttf` su un valore come è stato possibile rinominare `pacifico.ttf`.

4. Applicare il tipo di carattere personalizzato tramite la nuova `android:fontFamily` attributo nel layout XML. Ad esempio, `TextView` dichiarazione Usa aggiunto **pacifico.ttf** risorse di tipo di carattere:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

È anche possibile creare un file XML di famiglia del carattere che descrive più tipi di carattere, nonché i dettagli di stile e spessore. Per ulteriori informazioni, vedere Android Developer [tipi di carattere in XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) argomento.


### <a name="downloadable-fonts"></a>Tipi di carattere trasferibili

A partire da Android Oreo, le applicazioni possono richiedere tipi di carattere da un provider, anziché da raggruppare i file nel file APK. Tipi di carattere vengono scaricati dalla rete solo in base alle esigenze. Questa caratteristica riduce la dimensione file APK, conservando l'utilizzo di dati di memoria e rete cellulare telefono. È anche possibile utilizzare questa funzionalità nelle versioni API Android 14 e versioni successive installando il pacchetto Android 26 della libreria di supporto.

Quando l'applicazione necessita di un tipo di carattere, si crea un `FontsRequest` (che specifica il tipo di carattere per il download) e passarlo a un `FontsContract` metodo per scaricare il tipo di carattere. I passaggi seguenti descrivono il processo di download del tipo di carattere in modo più dettagliato:

1.  Creare un'istanza di un [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) oggetto. 

2.  Sottoclasse e creare un'istanza [FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3.  Implementare il [FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) metodo, che viene utilizzato per gestire il completamento della richiesta di tipo di carattere.

4.  Implementare il [FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) metodo, che viene utilizzato per informare l'applicazione di eventuali errori che si verificano durante il processo di richiesta del tipo di carattere.

5.  Chiamare il [FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) metodo per recuperare il tipo di carattere dal provider di tipi di carattere. 

Quando si chiama il `RequestFonts` (metodo), viene innanzitutto verificato se il tipo di carattere è nella cache locale (da una precedente chiamata a `RequestFont`). Se non è nella cache, chiama il provider di tipi di carattere, recupera il tipo di carattere in modo asincrono e quindi passa i risultati all'App richiamando il `OnTypeFaceRetrieved` metodo.

Il [tipi di carattere trasferibili](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) esempio viene illustrato come utilizzare la funzionalità di tipi di carattere trasferibili introdotta in Oreo Android. 

Per ulteriori informazioni sul download di tipi di carattere, vedere Android Developer [tipi di carattere trasferibili](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) argomento.



### <a name="autofill"></a>Riempimento automatico

Il nuovo _riempimento automatico_ framework in Android Oreo rende più semplice per gli utenti di gestire le attività ripetitive, ad esempio account di accesso, la creazione di account e le transazioni con carta di credito. Gli utenti operano per meno tempo digitando nuovamente informazioni (che possono causare errori di input). Prima che l'app possa operare con il Framework di riempimento automatico, un servizio di riempimento automatico deve essere abilitato nelle impostazioni di sistema (gli utenti possono abilitare o disabilitare il riempimento automatico).

Il [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework/) illustra l'utilizzo di Framework riempimento automatico. Include le implementazioni di client, le attività con le viste che devono essere autofilled e un servizio che può fornire dati di riempimento automatico per le attività di client.

Per ulteriori informazioni sulla nuova funzionalità di riempimento automatico e su come ottimizzare l'app per riempimento automatico, vedere Android Developer [Framework riempimento automatico](https://developer.android.com/guide/topics/text/autofill.html) argomento.



### <a name="picture-in-picture-pip"></a>Immagine in immagine (PIP)

Android Oreo consente a un'attività di avvio in modalità di immagine in picture (PIP), sovrapporre la schermata di un'altra attività. Questa funzionalità è destinata la riproduzione video.

Per specificare che l'attività dell'applicazione possono utilizzare modalità PIP, impostare il flag seguente su true nel manifesto Android:

```xml
android:supportsPictureInPicture
```

Per specificare il comportamento dell'attività quando è in modalità PIP, usare il nuovo [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html) oggetto. `PictureInPictureParams` rappresenta un set di parametri utilizzati per inizializzare e aggiornare un'attività in modalità PIP (ad esempio, dell'attività preferito proporzioni). Sono stati aggiunti i seguenti nuovi metodi PIP `Activity` in Oreo Android:

-   [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; inserisce l'attività in modalità PIP. L'attività viene posizionato nell'angolo della schermata, e il resto della schermata viene compilato con l'attività precedente è stata sullo schermo.

-   [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; Aggiorna impostazioni di configurazione dell'attività PIP (ad esempio, una modifica proporzioni).

Il [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) esempio viene illustrato l'utilizzo di base della modalità di immagine in immagine (PiP) introdotto in Oreo nei dispositivi palmari. L'esempio riproduce un video che continua senza interruzioni durante il cambio avanti e indietro tra le modalità di visualizzazione o altre attività.



### <a name="other-features"></a>Altre funzionalità

Android Oreo contiene molte altre nuove funzionalità, ad esempio i limiti di background Emoji API percorso, delle librerie, supporto, colore ampia per App, nuovi codec audio, miglioramenti WebView, tastiera migliorato il supporto di navigazione e una nuova API AAudio (audio pro) per audio di bassa latenza ad alte prestazioni, per ulteriori informazioni su queste funzionalità, vedere Android Developer [API e le funzionalità di Android Oreo](https://developer.android.com/about/versions/oreo/android-8.0.html) argomento.



## <a name="behavior-changes"></a>Modifiche del comportamento

Android Oreo include una varietà di sistema e le modifiche di comportamento di API che possono avere un impatto sulle funzionalità delle App esistenti. Tali modifiche sono descritte come segue.


### <a name="background-execution-limits"></a>Limiti di esecuzione in background

Per migliorare l'esperienza utente, Oreo Android impone limitazioni sulle operazioni che è possano eseguire App durante l'esecuzione in background. Ad esempio, se l'utente è guardare un video o un gioco, un'app in esecuzione in background potrebbe compromettere le prestazioni di un'applicazione a elevato utilizzo di video in esecuzione in primo piano. Di conseguenza, Oreo Android posiziona le restrizioni seguenti per le applicazioni che non interagisce direttamente con l'utente:

1.  **Limitazioni del servizio in background** &ndash; quando un'applicazione viene eseguita in background, dispone di una finestra di alcuni minuti in cui è ancora consentito creare e utilizzare i servizi. Al termine di tale finestra, arresta il servizio di sfondo dell'app Android e lo considera come _inattivo_.

2.  **Limitazioni di trasmissione** &ndash; Android 7.0 (API 25) trova limitazioni trasmissioni che registra un'app per la ricezione. Android Oreo rende più severi queste limitazioni. Ad esempio, le app Android Oreo non più possono registrare ricevitori broadcast per le trasmissioni implicite nei cui manifesti.

Per ulteriori informazioni sui nuovi limiti di esecuzione in background, vedere Android Developer [limiti di esecuzione in Background](https://developer.android.com/about/versions/oreo/background.html) argomento.


### <a name="breaking-changes"></a>Modifiche di interruzione

App di destinazione Android Oreo o superiore necessario modificare le proprie App per supportare le modifiche seguenti, dove applicabile:

- Android Oreo la possibilità di impostare la priorità delle singole notifiche è deprecata. Al contrario, impostare un livello di importanza consigliata quando si crea un canale di notifica. Il livello di importanza che si assegna a un canale di notifica si applica a tutti i messaggi di notifica che la registrazione a esso.

- Per le applicazioni destinate a Android Oreo, `PendingIntent.GetService()` non funziona perché i nuovi limiti imposti su servizi in esecuzione in background. Se la destinazione è Android Oreo, è necessario utilizzare [PendingIntent.GetBroadcast](https://developer.xamarin.com/api/member/Android.App.PendingIntent.GetBroadcast/p/Android.Content.Context/System.Int32/Android.Content.Intent/Android.App.PendingIntentFlags/) invece.  


## <a name="sample-code"></a>Codice di esempio

Alcuni esempi di xamarin sono disponibili per viene illustrato come sfruttare le funzionalità di Android Oreo:

-   [NotificationsChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) viene illustrato come utilizzare il nuovo sistema di canali di notifica introdotto in Oreo Android. In questo esempio gestisce due canali di notifica: uno con la priorità predefinita e l'altro con priorità alta.

-   [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) viene illustrato l'utilizzo di base della modalità di immagine in immagine (PiP) introdotto in Oreo nei dispositivi palmari. L'esempio riproduce un video che continua senza interruzioni durante il cambio avanti e indietro tra le modalità di visualizzazione o altre attività.

-   [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework) viene illustrato come utilizzare il Framework di riempimento automatico. Include le implementazioni di client, le attività con le viste che devono essere autofilled e un servizio che può fornire dati di riempimento automatico per le attività di client.

-   [Tipi di carattere trasferibili](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) viene fornito un esempio di come utilizzare la funzionalità di carattere scaricabile descritta in precedenza.

-   [EmojiCompat](https://developer.xamarin.com/samples/monodroid/android-o/EmojiCompat) viene illustrato l'utilizzo della libreria di supporto EmojiCompat. È possibile utilizzare questa libreria per evitare che l'app da caratteri emoji mancanti che mostra come i caratteri "tofu".

-   [Posizione gli aggiornamenti in sospeso finalità](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdPendIntent) viene illustrato l'utilizzo dell'API di percorso per ottenere gli aggiornamenti sulla posizione di un dispositivo utilizzando un `PendingIntent`.

-   [Servizio di percorso gli aggiornamenti in primo piano](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdFgService) viene illustrato come utilizzare l'API del percorso per ottenere gli aggiornamenti sulla posizione di un dispositivo utilizzando un servizio di primo piano associato e avviato.


## <a name="video"></a>Video

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**8.0 Oreo lo sviluppo di Android con c#**


## <a name="summary"></a>Riepilogo

In questo articolo introdotto Oreo Android e viene illustrato come installare e configurare gli strumenti di più recenti e i pacchetti per lo sviluppo di xamarin in Android Oreo. Fornita una panoramica delle funzionalità chiave disponibili in Android Oreo, con collegamenti a codice sorgente di esempio per diverse nuove funzionalità. Inclusi i collegamenti alla documentazione API e argomenti Android Developer che consentono di iniziare la creazione di App per Android Oreo. Inoltre evidenziato le modifiche di comportamento Oreo Android più importanti che potrebbero influire sulle App esistenti.


## <a name="related-links"></a>Collegamenti correlati

- [Android Oreo 8.0](https://developer.android.com/index.html)
