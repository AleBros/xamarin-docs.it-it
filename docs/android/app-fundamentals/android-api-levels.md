---
title: Informazioni sui livelli di API Android
description: Xamarin dispone di diverse impostazioni a livello API Android di determinano la compatibilità dell'applicazione con più versioni di Android. Questa guida illustra il significato di queste impostazioni, come configurarli e sugli effetti dispongano dell'app in fase di esecuzione.
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/01/2018
ms.openlocfilehash: b942bb1be3441b1fb1a8bd65016914b3ecddbb26
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732320"
---
# <a name="understanding-android-api-levels"></a>Informazioni sui livelli di API Android

_Xamarin dispone di diverse impostazioni a livello API Android di determinano la compatibilità dell'applicazione con più versioni di Android. Questa guida illustra il significato di queste impostazioni, come configurarli e sugli effetti dispongano dell'app in fase di esecuzione._


## <a name="quick-start"></a>Avvio rapido

Xamarin espone tre impostazioni di progetto a livello di API Android:

-   [Framework di destinazione](#framework) &ndash; specifica quali framework da utilizzare nella compilazione dell'applicazione. Questo livello API viene utilizzato in *compilare* tempo xamarin.

-   [Versione minima di Android](#minimum) &ndash; specifica la versione meno recente di Android che si vuole che l'app per il supporto. Questo livello API viene utilizzato in *eseguire* tempo da Android.

-   [Versione di Android](#target) &ndash; specifica la versione di Android dell'app deve essere eseguita su. Questo livello API viene utilizzato in *eseguire* tempo da Android.

Prima di poter configurare un livello di API per il progetto, è necessario installare i componenti di platform SDK per tale livello dell'API. Per ulteriori informazioni sul download e installazione dei componenti di Android SDK, vedere [il programma di installazione di Android SDK](~/android/get-started/installation/android-sdk.md).

> [!NOTE]
> A partire da 2018 agosto, la Console di Google Play richiederà che nuove app di destinazione del livello di API 26 (8.0 Android) o versione successiva.
Saranno necessario per il livello di API 26 o superiore a partire da novembre 2018 App esistenti. Per ulteriori informazioni, vedere [miglioramento della protezione di app e delle prestazioni in Google Play per anni a venire](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

In genere, tutti e tre i livelli di API xamarin impostati sullo stesso valore. Nel **applicazione** impostare **compila con la versione Android (Framework di destinazione)** all'ultima versione stabile di API (o, come minimo, per la versione di Android che dispone di tutte le funzionalità necessarie).
Nella schermata seguente, il Framework di destinazione è impostato su **Android 7.1 (API livello 25 - Nougat)**:

[![Valore predefinito è versione Framework compilazione utilizzando la versione Android di destinazione](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

Nel **manifesto Android** pagina, impostare la versione minima di Android **utilizzare compila con la versione SDK** e impostare la versione Android di destinazione per lo stesso valore della versione di .NET Framework di destinazione (nell'esempio seguente schermata di, il Framework di destinazione Android è impostato su **Android 7.1 (Nougat)**):

[![Le versioni minima e di destinazione Android impostato sulla versione di Framework di destinazione](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

Se si desidera mantenere la compatibilità con versioni precedenti di Android, impostare **minimo Android versione di destinazione** che si vuole che l'app per supportare la versione meno recente di Android. (Si noti che l'API livello 14 è il livello API minimo richiesto per [Firebase supporto e ai servizi Google Play](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).) La configurazione di esempio seguente supporta le versioni Android da 14 livello API attraverso il livello API 25:

[![Compila con il livello di API 25 Nougat, versione minima di Android impostare a livello di API 14](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

In genere, tutti e tre i livelli di API xamarin impostati sullo stesso valore. Impostare **framework di destinazione** all'ultima versione stabile di API (o, come minimo, per la versione di Android che dispone di tutte le funzionalità necessarie). Per impostare il **framework di destinazione**, passare a **compilazione > Generale** nel **opzioni progetto**. Nella schermata seguente, il Framework di destinazione è impostato su **utilizzare la versione più recente piattaforma installata (8.0)**:

[![Framework di destinazione verrà utilizzato per la piattaforma di installazione più recente](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

Le impostazioni di versione minimo e di destinazione Android sono reperibile nella **compilazione > applicazione Android** in **opzioni progetto**. Impostare la versione minima di Android **automatico - versione di framework di destinazione utilizzare** e impostare la versione Android di destinazione per lo stesso valore della versione di .NET Framework di destinazione. Nella schermata seguente, il Framework di destinazione Android è impostato su **Android 8.0 (livello API 26)** corrisponde all'impostazione del Framework di destinazione precedente:

[![Impostando i livelli di destinazione e framework nelle opzioni di progetto](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

Se si desidera mantenere la compatibilità con versioni precedenti di Android, modificare **minimo Android versione** che si vuole che l'app per supportare la versione meno recente di Android. Si noti che l'API livello 14 è il livello API minimo richiesto per [Firebase supporto e ai servizi Google Play](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).
Ad esempio, la configurazione seguente supporta fino a 14 livello API Android versioni:

[![Valore minimo e le versioni di destinazione impostate su automatico - utilizzano versione framework di destinazione](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----


Se l'app supporta più versioni di Android, il codice deve includere i controlli di runtime per verificare che l'app funziona con l'impostazione della versione minima di Android (vedere [verificato dal Runtime per le versioni Android](#runtimechecks) sotto per informazioni dettagliate). Se si utilizzano o creazione di una libreria, vedere [livelli API e le librerie](#libraries) seguito livello impostazioni per le raccolte per le procedure consigliate per la configurazione delle API.



## <a name="android-versions-and-api-levels"></a>Livelli di API e le versioni di Android

Come la piattaforma Android si evolve e vengono rilasciate nuove versioni di Android, ogni versione di Android viene assegnato un identificatore univoco di tipo integer, denominato il *livello API*. Pertanto, ogni versione di Android corrisponde a un solo livello API Android. Poiché gli utenti installano App nelle versioni precedenti di anche come più recente di Android, le app Android reale devono essere progettate per funzionare con più livelli API Android.


### <a name="android-versions"></a>Versioni di Android

Ogni versione di Android passa più nomi di:

-   La versione di Android, ad esempio **7.1 Android**
-   Codice nome, ad esempio _Nougat_
-   Livello di un'API corrispondente, ad esempio **livello API 25**

Un nome in codice Android può corrispondere a più versioni e i livelli di API (come illustrato nell'elenco di seguito), ma ogni versione di Android corrisponde a un solo livello di API.

Definisce inoltre xamarin *codici versione di build* che eseguono il mapping ai livelli API Android attualmente noti. Nell'elenco seguente consentono di effettuare conversioni tra il livello di API, versione di Android, nome in codice e codice della versione di compilazione xamarin.

-   **API 27 (8.1 Android)** &ndash; _Oreo_rilasciata a dicembre 2017. Compilazione di codice della versione `Android.OS.BuildVersionCodes.OMr1`

-   **API 26 (Android 8.0)** &ndash; _Oreo_rilasciata agosto 2017. Compilazione di codice della versione `Android.OS.BuildVersionCodes.O`

-   **API 25 (Android 7.1)** &ndash; _Nougat_rilasciata a dicembre 2016. Compilazione di codice della versione `Android.OS.BuildVersionCodes.NMr1`

-   **API 24 (Android 7.0)** &ndash; _Nougat_rilasciata agosto 2016. Compilazione di codice della versione `Android.OS.BuildVersionCodes.N`

-   **API 23 (Android 6.0)** &ndash; _Marshmallow_rilasciata agosto 2015. Compilazione di codice della versione `Android.OS.BuildVersionCodes.M`

-   **API 22 (Android 5.1)** &ndash; _simbolo_rilasciata a marzo 2015. Compilazione di codice della versione `Android.OS.BuildVersionCodes.LollipopMr1`

-   **API 21 (Android 5.0)** &ndash; _simbolo_rilasciata a novembre 2014. Compilazione di codice della versione `Android.OS.BuildVersionCodes.Lollipop`

-   **API 20 (Android 4.4W)** &ndash; _Kitkat Watch_rilasciata a giugno 2014. Compilazione di codice della versione `Android.OS.BuildVersionCodes.KitKatWatch`

-   **19 API (Android 4.4)** &ndash; _Kitkat_rilasciata a ottobre 2013. Compilazione di codice della versione `Android.OS.BuildVersionCodes.KitKat`

-   **API 18 (Android 4.3)** &ndash; _gelatina Bean_rilasciata luglio 2013. Compilazione di codice della versione `Android.OS.BuildVersionCodes.JellyBeanMr2`

-   **API 17 (Android 4.2-4.2.2)** &ndash; _gelatina Bean_rilasciata a novembre 2012. Compilazione di codice della versione `Android.OS.BuildVersionCodes.JellyBeanMr1`

-   **API 16 (Android 4.1-4.1.1)** &ndash; _gelatina Bean_rilasciata a giugno 2012. Compilazione di codice della versione `Android.OS.BuildVersionCodes.JellyBean`

-   **API 15 (Android 4.0.3-4.0.4)** &ndash; _gelato Sandwich_rilasciata a dicembre 2011. Compilazione di codice della versione `Android.OS.BuildVersionCodes.IceCreamSandwichMr1`

-   **API 14 (Android 4.0-4.0.2)** &ndash; _gelato Sandwich_rilasciata a ottobre 2011. Compilazione di codice della versione `Android.OS.BuildVersionCodes.IceCreamSandwich`

-   **API 13 (Android 3.2)** &ndash; _Nido d'API_rilasciata a giugno 2011. Compilazione di codice della versione `Android.OS.BuildVersionCodes.HoneyCombMr2`

-   **API 12 (Android 3.1.x)** &ndash; _Nido d'API_rilasciata a maggio 2011. Compilazione di codice della versione `Android.OS.BuildVersionCodes.HoneyCombMr1`

-   **API 11 (Android 3.0. x)** &ndash; _Nido d'API_rilasciata febbraio 2011. Compilazione di codice della versione `Android.OS.BuildVersionCodes.HoneyComb`

-   **API 10 (Android 2.3.3-2.3.4)** &ndash; _marzapane_rilasciata febbraio 2011. Compilazione di codice della versione `Android.OS.BuildVersionCodes.GingerBreadMr1`

-   **API 9 (Android 2.3-2.3.2)** &ndash; _marzapane_rilasciata a novembre 2010. Compilazione di codice della versione `Android.OS.BuildVersionCodes.GingerBread`

-   **API 8 (Android 2.2)** &ndash; _Froyo_rilasciata a giugno 2010. Compilazione di codice della versione `Android.OS.BuildVersionCodes.Froyo`

-   **API 7 (Android 2.1. x)** &ndash; _Eclair_rilasciata gennaio 2010. Compilazione di codice della versione `Android.OS.BuildVersionCodes.EclairMr1`

-   **API 6 (Android 2.0.1)** &ndash; _Eclair_rilasciata a dicembre 2009. Compilazione di codice della versione `Android.OS.BuildVersionCodes.Eclair01`

-   **API 5 (2.0 Android)** &ndash; _Eclair_rilasciata a novembre 2009. Compilazione di codice della versione `Android.OS.BuildVersionCodes.Eclair`

-   **API 4 (Android 1.6)** &ndash; _anello_rilasciata settembre 2009. Compilazione di codice della versione `Android.OS.BuildVersionCodes.Donut`

-   **API 3 (Android 1.5)** &ndash; _Cupcake_rilasciata a maggio 2009. Compilazione di codice della versione `Android.OS.BuildVersionCodes.Cupcake`

-   **API 2 (Android 1.1)** &ndash; _Base_rilasciata febbraio 2009. Compilazione di codice della versione `Android.OS.BuildVersionCodes.Base11`

-   **API 1 (Android 1.0)** &ndash; _Base_rilasciata a ottobre 2008. Compilazione di codice della versione `Android.OS.BuildVersionCodes.Base`


Come indicato in questo elenco, le nuove versioni Android vengono rilasciate frequentemente &ndash; talvolta diverse versioni all'anno. Di conseguenza, l'universo dei dispositivi Android che potrebbe eseguire l'applicazione include una vasta gamma di Android versioni precedenti e più recenti. Come può garantire che l'app viene eseguita in modo coerente e affidabile in numerose versioni diverse di Android? Livelli di API di Android consentono di gestire questo problema.


### <a name="android-api-levels"></a>Livelli di API Android

Ogni dispositivo Android viene eseguito esattamente *uno* livello API &ndash; questo livello API è sicuramente univoco per ogni versione della piattaforma Android. Il livello API identifica con precisione la versione del set di API che l'app può effettuare chiamate nel; Identifica la combinazione di elementi del manifesto, autorizzazioni, e così via, codice per gli sviluppatori. Sistema di Android di livelli API consente Android di determinare se un'applicazione è compatibile con un'immagine del sistema Android prima di installare l'applicazione in un dispositivo.

Quando viene creata un'applicazione, contiene le informazioni a livello API seguenti:

-   Il *destinazione* livello API di Android che viene compilato l'app per l'esecuzione.

-   Il *minimo* API Android livello di un dispositivo Android è necessario per eseguire l'app. 

Queste impostazioni vengono utilizzate per garantire che le funzionalità necessarie per eseguire correttamente l'app sono disponibile nel dispositivo Android in fase di installazione. In caso contrario, l'app viene impedita l'esecuzione nel dispositivo. Ad esempio, se il livello API di un dispositivo Android è inferiore al livello API minimo specificato per l'app, il dispositivo Android impedirà l'utente installa l'app.


## <a name="project-api-level-settings"></a>Impostazioni a livello di API progetto

Le sezioni seguenti illustrano come usare il SDK Manager per preparare l'ambiente di sviluppo per i livelli di API di destinazione, seguito da una spiegazione dettagliata di come configurare *Framework di destinazione*, *minimo Versione di Android*, e *versione di destinazione Android* le impostazioni di xamarin.


### <a name="android-sdk-platforms"></a>Piattaforme Android SDK

Prima di poter selezionare un livello minimo API o di destinazione in xamarin, è necessario installare la versione della piattaforma Android SDK corrispondente a tale livello di API. L'intervallo delle opzioni disponibili per il Framework di destinazione, versione minima di Android e la versione Android di destinazione è limitato per le versioni di intervallo di Android SDK installato. È possibile utilizzare il SDK Manager per verificare che siano installate le versioni di Android SDK richieste e usarlo per aggiungere eventuali nuovi livelli di API che è necessario per l'app. Se non si abbia familiarità con l'installazione livelli API, vedere [il programma di installazione di Android SDK](~/android/get-started/installation/android-sdk.md).

<a name="framework" />

### <a name="target-framework"></a>Framework di destinazione

Il *Framework di destinazione* (noto anche come `compileSdkVersion`) è la versione di framework Android specifico (livello API) che viene compilato l'app in fase di compilazione. Questa impostazione specifica quali API app *prevede* da utilizzare quando viene eseguito, ma non ha alcun effetto in cui le API sono effettivamente disponibili per l'app quando viene installato. Di conseguenza, l'impostazione del Framework di destinazione non la modifica del comportamento di runtime.

Il Framework di destinazione identifica le versioni della libreria è collegata l'applicazione in &ndash; determina le API che è possibile usare in app. Ad esempio, se si desidera utilizzare il [NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) metodo che è stata introdotta in Android 5.0 simbolo, è necessario impostare il Framework di destinazione su **21 livello API (simbolo)** o versione successiva. Se si imposta il Framework di destinazione del progetto per un'API livello, ad esempio **livello 19 API (KitKat)** e tenta di chiamare il `SetCategory` metodo nel codice, si otterrà un errore di compilazione.

È consigliabile che la compilazione sempre con il *più recente* versione Framework di destinazione disponibili. In questo modo fornisce utili messaggi di avviso per le API deprecate che potrebbero essere chiamate dal codice. Utilizzando la versione più recente di .NET Framework di destinazione è particolarmente importante quando si utilizzano la libreria supporto più recenti &ndash; ogni libreria prevede che l'app compilata a livello API minimo della libreria di supporto o versione successiva. 


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per accedere all'impostazione del Framework di destinazione in Visual Studio, aprire le proprietà del progetto in **Esplora** e selezionare il **applicazione** pagina:

[![Pagina applicazione di proprietà di progetto](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

Impostare il Framework di destinazione selezionando un livello di API nel menu a discesa sotto **compila con la versione Android** come illustrato in precedenza.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per accedere all'impostazione di .NET Framework di destinazione in Visual Studio per Mac, il nome del progetto e scegliere **opzioni**; questo viene aperto il **opzioni progetto** finestra di dialogo. In questa finestra di dialogo, passare a **compilazione > Generale** come illustrato di seguito:

[![Compilare la sezione generale della pagina di opzioni progetto](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

Impostare il Framework di destinazione selezionando un livello di API nella casella a discesa a destra del **framework di destinazione** come illustrato in precedenza.

-----


<a name="minimum" />

### <a name="minimum-android-version"></a>Versione minima di Android

Il *minimo Android versione* (noto anche come `minSdkVersion`) è la versione meno recente del sistema operativo Android (ad esempio, il livello API minimo) che è possibile installare ed eseguire l'applicazione. Per impostazione predefinita, un'app solo può essere installata su dispositivi corrispondenti l'impostazione del Framework di destinazione o versione successiva. Se l'impostazione della versione minima di Android è *inferiore* rispetto all'impostazione del Framework di destinazione, è anche possibile eseguire l'app in versioni precedenti di Android. Ad esempio, se si imposta il Framework di destinazione su **Android 7.1 (Nougat)** e impostare la versione minima di Android **Android 4.0.3 (gelato Sandwich)**, l'app può essere installato su qualsiasi piattaforma da API livello 15 a livello dell'API 25, inclusivo.

Anche se l'app può compilare e installare in questo intervallo di piattaforme correttamente, questo non garantisce che verrà completata *eseguire* in tutte queste piattaforme. Ad esempio, se è installata l'app **Android 5.0 (simbolo)** e il codice chiama un'API che è disponibile solo in **Android 7.1 (Nougat)** e successive, otterrà un errore di runtime e possibile arresto anomalo dell'app. Pertanto, è necessario verificare il codice &ndash; in fase di esecuzione &ndash; che chiama solo le API supportate dal dispositivo che è in esecuzione su Android. In altre parole, il codice deve includere i controlli di runtime esplicita per garantire che l'app Usa le API più recente solo per i dispositivi che sono abbastanza recenti per supportarli.
[Controlli di runtime per le versioni Android](#runtimechecks), più avanti in questa guida viene illustrato come aggiungere questi controlli di runtime al codice.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per accedere all'impostazione di versione minima di Android in Visual Studio, aprire le proprietà del progetto in **Esplora** e selezionare il **manifesto Android** pagina. Nel menu a discesa sotto **minimo Android versione** è possibile selezionare la versione minima di Android per l'applicazione:

[![Android minimo per l'opzione di destinazione impostata su Compila con la versione SDK](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

Se si seleziona **utilizzare compila con la versione SDK**, la versione minima di Android sarà la stessa impostazione di .NET Framework di destinazione.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per accedere all'impostazione di .NET Framework di destinazione in Visual Studio per Mac, il nome del progetto e scegliere **opzioni**; questo viene aperto il **opzioni progetto** finestra di dialogo. Passare a **compilazione > applicazione Android**.
Utilizzando il menu di riepilogo a discesa a destra del **minimo Android versione**, è possibile impostare la versione minima di Android per l'applicazione:

[![Versione minima di Android è impostato su automatico - versione di framework di destinazione di utilizzo](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

Se si seleziona **automatica &ndash; utilizzare versione framework di destinazione**, la versione minima di Android sarà la stessa impostazione di .NET Framework di destinazione.

-----


<a name="target" />

### <a name="target-android-version"></a>Versione di destinazione Android

Il *destinazione Android versione* (noto anche come `targetSdkVersion`) è l'API di livello del dispositivo Android in cui è previsto per eseguire l'app. Questa impostazione viene utilizzata per determinare se si desidera attivare eventuali comportamenti di compatibilità di Android &ndash; in questo modo si garantisce che l'app continui a funzionare come previsto. Android utilizza l'impostazione della versione dell'app Android di destinazione per individuare le modifiche di comportamento possono essere applicate all'App senza interromperla (si tratta come Android fornisce compatibilità con le versioni).

Il Framework di destinazione e la versione Android di destinazione, mentre i cui nomi molto simili, non sono lo stesso significato. L'impostazione del Framework di destinazione comunica informazioni livello API di destinazione per xamarin per l'utilizzo in *fase di compilazione*, mentre la versione di destinazione Android comunica informazioni livello API di destinazione per Android per l'utilizzo in  *fase di esecuzione* (quando l'app è installato e in esecuzione in un dispositivo).

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per accedere a questa impostazione in Visual Studio, aprire le proprietà del progetto in **Esplora** e selezionare il **manifesto Android** pagina. Nel menu a discesa sotto **versione di destinazione Android** è possibile selezionare la versione Android di destinazione per l'applicazione:

[![Versione di Android di destinazione impostata su Compila con la versione SDK](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

È consigliabile impostare in modo esplicito la versione Android di destinazione per la versione più recente di Android che consente di testare l'app. Idealmente, deve essere impostato per la versione più recente di Android SDK &ndash; questo modo è possibile usare le API di nuovo prima di lavoro tramite le modifiche di comportamento. Per la maggior parte degli sviluppatori, è *non* consigliabile impostare la versione di destinazione Android **utilizzare compila con la versione SDK**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per accedere all'impostazione di .NET Framework di destinazione in Visual Studio per Mac, il nome del progetto e scegliere **opzioni**; questo viene aperto il **opzioni progetto** finestra di dialogo. Passare a **compilazione > applicazione Android**.
Utilizzando il menu di riepilogo a discesa a destra del **versione di destinazione Android**, è possibile impostare la versione Android di destinazione per l'applicazione:

[![La versione Android di destinazione è impostato su automatico - versione di framework di destinazione di utilizzo](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

È consigliabile impostare in modo esplicito la versione Android di destinazione per la versione più recente di Android che consente di testare l'app. Idealmente, deve essere impostato per la versione più recente disponibile Android SDK &ndash; questo modo è possibile usare le API di nuovo prima di lavoro tramite le modifiche di comportamento. Per la maggior parte dei casi, si consiglia di non impostare la versione di destinazione Android **automatico - versione di framework di destinazione utilizzare**.

-----

In generale, la versione Android di destinazione devono essere delimitata dalla versione di Android minimo e il Framework di destinazione. Ovvero:

**Versione minima di Android < = destinazione Android versione < = Framework di destinazione**

Per ulteriori informazioni sui livelli SDK, vedere Android Developer [utilizza sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) documentazione.


<a name="runtimechecks" />

## <a name="runtime-checks-for-android-versions"></a>Controlli di runtime per le versioni di Android

Ogni nuova versione di Android viene rilasciata, viene aggiornato il framework di API per fornire nuove funzionalità di sostituzione o. Con poche eccezioni, la funzionalità API da Android nelle versioni precedenti viene trasferita in versioni più recenti di Android senza modifiche. Di conseguenza, se l'app viene eseguita in un determinato livello di API Android, verranno in genere essere in grado di eseguire a livello di API Android successive senza modifiche. Ma cosa accade se si desidera anche eseguire l'app in versioni precedenti di Android?

Se si seleziona una versione minima di Android *inferiore* di impostazione del Framework di destinazione, alcune API potrebbero non essere disponibili per l'app in fase di esecuzione. Tuttavia, l'app ancora possibile eseguire in un dispositivo precedente, ma con funzionalità ridotte. Per ogni API che non sono disponibili su piattaforme Android corrispondente all'impostazione di versione minima di Android, il codice deve verificare in modo esplicito il valore di `Android.OS.Build.VERSION.SdkInt` proprietà per determinare il livello API della piattaforma su cui viene eseguita l'app. Se il livello API *inferiore* rispetto alla versione minima di Android che supporta l'API che si desidera chiamare quindi il codice deve individuare un modo per funzionare correttamente senza effettuare questa chiamata API.

Ad esempio, si supponga che si desidera utilizzare il [NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) metodo per suddividere una notifica quando si esegue in **Android 5.0 simbolo** (e versioni successive), ma si vuole comunque che l'app per eseguire, ad esempio nelle versioni precedenti di Android **Android 4.1 gelatina Bean** (dove `SetCategory` non è disponibile). Riferimento alla versione di Android tabella all'inizio di questa Guida, è possibile vedere che il codice della versione di build per **Android 5.0 simbolo** è `Android.OS.BuildVersionCodes.Lollipop`. Per supportare le versioni precedenti di Android where `SetCategory` è non disponibile, il codice può rilevare il livello di API in fase di esecuzione e chiamare in modo condizionale `SetCategory` solo quando il livello API è maggiore o uguale al codice della versione di build del simbolo:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

In questo esempio, il Framework di destinazione dell'applicazione è impostato su **Android 5.0 (API livello 21)** e la versione minima di Android è impostata su **Android 4.1 (API livello 16)**. Poiché `SetCategory` è disponibile nel livello API `Android.OS.BuildVersionCodes.Lollipop` e versioni successive, questo codice di esempio chiama `SetCategory` solo quando è effettivamente disponibile &ndash; verrà *non* tenta di chiamare `SetCategory` quando l'API livello è 16, 17, 18, 19 o 20. La funzionalità viene ridotto in queste versioni di Android in precedenza, solo nella misura in cui le notifiche non vengono ordinate correttamente (in quanto essi non sono organizzati per tipo), ma le notifiche sono ancora pubblicate per avvisare l'utente. L'app continui a funzionare, ma la relativa funzionalità è leggermente ridotta.

In generale, il controllo della versione build aiuta il codice di stabilire in fase di esecuzione tra qualcosa di nuovo metodo rispetto a quello precedente. Ad esempio:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    // Do things the Lollipop way
}
else
{
    // Do things the pre-Lollipop way
}
```

Non è presente alcuna regola semplice e veloce che viene illustrato come ridurre o modificare funzionalità dell'app quando viene eseguito nelle versioni precedenti di Android che mancano una o più API. In alcuni casi (ad esempio il `SetCategory` esempio precedente), è sufficiente omettere la chiamata API quando non è disponibile. Tuttavia, in altri casi, potrebbe essere necessario implementare la funzionalità alternativa quando `Android.OS.Build.VERSION.SdkInt` è minore rispetto all'API livello che l'app deve presentare la sua esperienza ottimale.

<a name="libraries" />

## <a name="api-levels-and-libraries"></a>Livelli di API e librerie

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Quando si crea un progetto libreria di xamarin (ad esempio una libreria di classi o una raccolta di associazioni), è possibile configurare solo l'impostazione del Framework di destinazione &ndash; la versione minima di Android e le impostazioni di versione di destinazione Android non sono disponibili. Perché vi è alcun **manifesto Android** pagina:

[![È disponibile solo la compilazione utilizzando l'opzione di versione di Android](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Quando si crea un progetto libreria di xamarin, vi è alcun **applicazione Android** pagina in cui è possibile configurare la versione minima di Android e la versione di destinazione Android &ndash; la versione minima di Android e destinazione Le impostazioni della versione di Android non sono disponibili.
Perché vi è alcun **compilazione > applicazione Android** pagina):

[![Pagina generale senza opzioni della versione minima e di destinazione di compilazione](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

La versione minima di Android e le impostazioni della versione di destinazione Android non sono disponibili perché la raccolta risulta non è un'app autonoma &ndash; è stato possibile eseguire in qualsiasi versione di Android, a seconda dell'app fornito con la libreria. È possibile specificare come la libreria sia *compilati*, ma non è possibile prevedere quale piattaforma di livello dell'API di libreria deve essere eseguita su. A tal fine, quando si utilizzo o la creazione di librerie devono attenersi le procedure consigliate seguenti:

-   **Quando si utilizza una libreria Android** &ndash; se si utilizza una libreria Android nell'applicazione, assicurarsi di impostare il Framework di destinazione dell'app impostazione a un'API livello *almeno a un massimo di* la destinazione Impostazione del Framework di libreria.

-   **Quando si crea una libreria Android** &ndash; se si sta creando una libreria Android per l'uso da altre applicazioni, assicurarsi di impostare l'impostazione del Framework di destinazione al livello minimo API necessarie per la compilazione.

Queste procedure consigliate si consiglia di evitare la situazione in cui una libreria tenta di chiamare un'API che non è disponibile in fase di esecuzione (che può causare l'arresto anomalo dell'app). Se si è uno sviluppatore di libreria, è consigliabile cercare di limitare l'utilizzo di chiamate API a un subset ben definito e di piccole dimensioni dell'area della superficie totale di API. In tal modo eviterai assicurarsi che la raccolta può essere utilizzata in modo sicuro tra una più ampia le versioni di intervallo di Android.


## <a name="summary"></a>Riepilogo

In questa guida viene descritto come livelli API Android consentono di gestire compatibilità delle applicazioni tra le diverse versioni di Android. Fornita la procedura dettagliata per la configurazione di xamarin *Framework di destinazione*, *minimo Android versione*, e *versione di destinazione Android* le impostazioni del progetto. Fornito istruzioni per l'utilizzo di Android SDK Manager per installare i pacchetti SDK, inclusi esempi su come scrivere codice per gestire diversi livelli di API in fase di esecuzione e viene illustrato come gestire i livelli di API durante la creazione o l'utilizzo di librerie di Android. Inoltre fornito un elenco completo che si riferisce livelli API a numeri di versione di Android (ad esempio Android 4.4), i nomi di versione di Android (ad esempio Kitkat) e i codici di versione di compilazione xamarin.


## <a name="related-links"></a>Collegamenti correlati

- [Installazione di Android SDK](~/android/get-started/installation/android-sdk.md)
- [Modifica gli strumenti SDK CLI](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Prelievo compileSdkVersion e minSdkVersion, targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [Qual è il livello API?](http://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [Nomi in codice, i tag e i numeri di Build](https://source.android.com/source/build-numbers)
