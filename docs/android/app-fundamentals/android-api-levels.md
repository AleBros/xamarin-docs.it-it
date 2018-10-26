---
title: Informazioni sui livelli API Android
description: Xamarin. Android ha diverse impostazioni a livello API Android di determinano la compatibilità della tua app con più versioni di Android. Questa guida illustra il significato di queste impostazioni, come configurarli e sugli effetti hanno sull'app in fase di esecuzione.
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: aa522e5226d78c1b43bb52b97991b989491d251f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120060"
---
# <a name="understanding-android-api-levels"></a>Understanding Android API Levels (Informazioni sui livelli API Android)

_Xamarin. Android ha diverse impostazioni a livello API Android di determinano la compatibilità della tua app con più versioni di Android. Questa guida illustra il significato di queste impostazioni, come configurarli e sugli effetti hanno sull'app in fase di esecuzione._


## <a name="quick-start"></a>Avvio rapido

Xamarin. Android espone tre impostazioni di progetto a livello API Android:

-   [Framework di destinazione](#framework) &ndash; specifica il framework da utilizzare nella compilazione dell'applicazione. Questo livello di API viene specificato alla *compile* tempo da xamarin. Android.

-   [Versione minima di Android](#minimum) &ndash; specifica la versione meno recente di Android che si vuole che l'app per il supporto. Questo livello di API viene specificato alla *eseguire* tempo da Android.

-   [Versione di Android di destinazione](#target) &ndash; specifica la versione di Android che l'app è deve essere eseguita su. Questo livello di API viene specificato alla *eseguire* tempo da Android.

Prima di poter configurare un livello di API per il progetto, è necessario installare i componenti SDK della piattaforma per lo specifico livello di API. Per altre informazioni sul download e installazione dei componenti di Android SDK, vedere [installazione di Android SDK](~/android/get-started/installation/android-sdk.md).

> [!NOTE]
> A partire da agosto 2018, Google Play Console richiederà da nuove app di destinazione a livello di API 26 (Android 8.0) o versione successiva.
Le app esistenti verranno chiesto di destinazione a livello di API 26 o versione successiva a partire da novembre 2018. Per altre informazioni, vedere [miglioramento della protezione di app e le prestazioni in Google Play per anni a venire](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

In genere, tutti e tre i livelli API xamarin. Android sono impostati sullo stesso valore. Nel **Application** pagina, impostare **esegue la compilazione usando la versione di Android (Framework di destinazione)** alla versione dell'API stabile più recente (o, come minimo, per la versione di Android che include tutte le funzionalità necessarie).
Nello screenshot seguente, il Framework di destinazione è impostato su **Android 7.1 (API livello 25 - Nougat)**:

[![Impostazioni predefinite di versione Framework di compilazione Usa la versione Android di destinazione](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

Nel **manifesto Android** pagina, impostare la versione minima di Android **Usa la compilazione con versione del SDK** e impostare la versione Android di destinazione per lo stesso valore come la versione di Framework di destinazione (nell'esempio seguente schermata, il Framework di destinazione di Android è impostato su **Android 7.1 (Nougat)**):

[![Versioni Android di destinazione e minima impostata sulla versione di Framework di destinazione](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

Se si desidera mantenere la compatibilità con le versioni precedenti con una versione precedente di Android, impostare **versione minima di Android di destinazione** la versione meno recente di Android che si vuole che l'app per il supporto. (Si noti che a livello API 14 è il livello API minimo necessario per [Google Play services e il supporto di Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).) La configurazione di esempio seguente supporta le versioni di Android da 14 a livello di API a livello API 25:

[![La compilazione con livello API 25 Nougat, versione minima di Android impostata a livello API 14](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

In genere, tutti e tre i livelli API xamarin. Android sono impostati sullo stesso valore. Impostare **framework di destinazione** alla versione dell'API stabile più recente (o, come minimo, per la versione di Android che include tutte le funzionalità necessarie). Per impostare il **framework di destinazione**, passare alla **compilazione > Generale** nel **opzioni progetto**. Nello screenshot seguente, il Framework di destinazione è impostato su **usare la versione più recente piattaforma installata (8.0)**:

[![Framework di destinazione utilizzando per impostazione predefinita per Usa la piattaforma installata più recente](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

Le impostazioni di versione minimo e di destinazione di Android sono disponibili in **compilazione > applicazione Android** nelle **opzioni progetto**. Impostare la versione minima di Android **automatico - versione di framework di destinazione usare** e impostare la versione Android di destinazione per lo stesso valore come la versione del Framework di destinazione. Nello screenshot seguente, il Framework di destinazione di Android è impostato su **Android 8.0 (livello API 26)** corrisponde all'impostazione di .NET Framework di destinazione precedente:

[![Impostare i livelli di destinazione e framework in Opzioni progetto](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

Se si desidera mantenere la compatibilità con le versioni precedenti con una versione precedente di Android, modificare **Minimum Android version** la versione meno recente di Android che si vuole che l'app per il supporto. Si noti che a livello API 14 è il livello API minimo necessario per [Google Play services e il supporto di Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).
La configurazione seguente, ad esempio, supporta le versioni di Android non appena il livello API 14:

[![Valore minimo e le versioni di destinazione impostate su automatico - usano versione framework di destinazione](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----


Se l'app supporta più versioni di Android, il codice deve includere i controlli di runtime per assicurarsi che l'app funziona con l'impostazione della versione minima di Android (vedere [controlli di Runtime per le versioni di Android](#runtimechecks) seguito per informazioni dettagliate). Se si utilizzano o si crea una libreria, vedere [librerie e i livelli API](#libraries) sotto per le procedure consigliate per la configurazione delle API di livello le impostazioni per le raccolte.



## <a name="android-versions-and-api-levels"></a>Versioni di Android e i livelli API

La piattaforma Android si evolve e vengono rilasciate nuove versioni di Android, ogni versione di Android viene assegnato un identificatore univoco di tipo integer, denominato il *livello API*. Pertanto, ogni versione di Android corrisponde a un solo livello API Android. Poiché gli utenti installano le App in precedenti versioni anche come le più recenti di Android, le app Android reali devono essere progettate per funzionare con più livelli API Android.


### <a name="android-versions"></a>Versioni di Android

Ogni versione di Android va dal più nomi:

-   La versione di Android, ad esempio **9.0 Android**
-   Un codice (dessert), o il nome, ad esempio _a torta_
-   Un'API corrispondente a livello, ad esempio **livello API 28**

Un nome in codice Android può corrispondere a più versioni e i livelli di API (come illustrato nella tabella seguente), ma ogni versione di Android corrisponde a un solo livello di API.

Definisce anche xamarin. Android *compilare i codici versione* che eseguire il mapping per i livelli API Android attualmente noti. Nella tabella seguente può facilitare la conversione tra livello API Android versione, nome in codice e codice versione build di xamarin. Android (i codici versione di build sono definiti nel `Android.OS` dello spazio dei nomi):

[!include[](~/android/includes/api-levels.md)]

Come indicato in questa tabella, le nuove versioni di Android vengono rilasciate frequentemente &ndash; talvolta più di una versione all'anno. Di conseguenza, l'universo di dispositivi Android che potrebbe eseguire l'app include una vasta gamma di versioni di Android precedenti e più recenti. In che modo possibile si garantisce che l'app viene eseguita in modo coerente e affidabile in così tante diverse versioni di Android? I livelli API di Android possono aiutarti a gestire questo problema.


### <a name="android-api-levels"></a>Livelli API Android

Ogni dispositivo Android viene eseguito nel esattamente *uno* a livello di API &ndash; questo livello di API è sicuramente univoco per ogni versione della piattaforma Android. Il livello API identifica con precisione la versione del set di API che l'app può effettuare chiamate nel; Identifica la combinazione di elementi del manifesto, autorizzazioni e così via codice per gli sviluppatori. Sistema di Android dei livelli API consente Android di determinare se un'applicazione è compatibile con un'immagine del sistema Android prima di installare l'applicazione in un dispositivo.

Quando viene compilata un'applicazione, contiene le informazioni a livello dell'API seguente:

-   Il *destinazione* livello API di Android che l'app viene compilato per l'esecuzione.

-   Il *minimo* livello API Android che per eseguire l'app deve avere un dispositivo Android. 

Queste impostazioni vengono usate per garantire che le funzionalità necessarie per eseguire correttamente l'app sono disponibile nel dispositivo Android al momento dell'installazione. In caso contrario, l'app viene impedito l'esecuzione in tale dispositivo. Ad esempio, se il livello API di un dispositivo Android è inferiore a livello API minimo specificato per l'app, il dispositivo Android impedirà l'utente installa l'app.


## <a name="project-api-level-settings"></a>Impostazioni a livello di progetto API

Le sezioni seguenti illustrano come usare SDK Manager per preparare l'ambiente di sviluppo per i livelli API di destinazione, seguito da una spiegazione dettagliata delle jak nakonfigurovat *Framework di destinazione*, *minimo Versione di Android*, e *Target Android version* impostazioni in xamarin. Android.


### <a name="android-sdk-platforms"></a>Piattaforme di Android SDK

Prima di poter selezionare un livello API minimo o di destinazione in xamarin. Android, è necessario installare la versione della piattaforma Android SDK che corrisponde a tale livello API. La gamma di opzioni disponibili per .NET Framework di destinazione, la versione minima di Android e Target Android version è limitata alle versioni di intervallo di Android SDK installata. È possibile usare SDK Manager per verificare che siano installate le versioni di Android SDK necessari, e che è possibile usarlo per aggiungere eventuali nuovi livelli di API che è necessario per l'app. Se non ha familiarità con le procedure per installare i livelli API, vedere [installazione di Android SDK](~/android/get-started/installation/android-sdk.md).

<a name="framework" />

### <a name="target-framework"></a>Framework di destinazione

Il *Framework di destinazione* (noto anche come `compileSdkVersion`) è la versione specifica del framework Android (livello API) che è stata compilata l'app in fase di compilazione. Questa impostazione indica quali API l'app *prevede* da utilizzare quando viene eseguito, ma non ha alcun effetto in cui le API effettivamente disponibili per l'app quando viene installato. Di conseguenza, la modifica dell'impostazione di .NET Framework di destinazione non modifica il comportamento di runtime.

Il Framework di destinazione identifica quali versioni della libreria è collegato all'applicazione &ndash; questa impostazione determina quali API è possibile usare nell'app. Ad esempio, se si desidera utilizzare il [NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) metodo che è stato introdotto in Android 5.0 Lollipop, è necessario impostare il Framework di destinazione su **API livello 21 (Lollipop)** o versione successiva. Se si imposta il Framework di destinazione del progetto a un'API a livello, ad esempio **API livello 19 (KitKat)** e tenta di chiamare il `SetCategory` metodo nel codice, si otterrà un errore di compilazione.

È consigliabile che la compilazione sempre con il *più recente* versione Framework di destinazione disponibile. In questo modo offre utili messaggi di avviso per le API deprecate che potrebbero essere chiamate dal codice. La versione più recente di .NET Framework di destinazione è particolarmente importante quando si usano le ultime versioni della libreria di supporto &ndash; ogni libreria prevede che l'app sia compilata a livello API minimo di tale libreria di supporto o versione successiva. 


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per accedere all'impostazione di .NET Framework di destinazione in Visual Studio, aprire le proprietà del progetto in **Esplora soluzioni** e selezionare il **applicazione** pagina:

[![Pagina di applicazione della proprietà progetto](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

Impostare il Framework di destinazione, selezionare un livello di API nel menu a discesa sotto **esegue la compilazione usando la versione di Android** come illustrato in precedenza.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per accedere all'impostazione del Framework di destinazione in Visual Studio per Mac, il pulsante destro del mouse e selezionare **opzioni**; questo consente di aprire le **opzioni progetto** finestra di dialogo. In questa finestra di dialogo, individuare **compilazione > Generale** come illustrato di seguito:

[![Creare una sezione generale della pagina di opzioni progetto](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

Impostare il Framework di destinazione, selezionare un livello di API nel menu a destra dell'elenco a discesa **framework di destinazione** come illustrato in precedenza.

-----


<a name="minimum" />

### <a name="minimum-android-version"></a>Versione minima di Android

Il *Minimum Android version* (noto anche come `minSdkVersion`) è la versione meno recente del sistema operativo Android (ad esempio, il livello API minimo) che possono installare ed eseguire l'applicazione. Per impostazione predefinita, un'app può essere solo installato nei dispositivi l'impostazione del Framework di destinazione di corrispondenza o versione successiva. Se è l'impostazione della versione minima di Android *inferiore* rispetto all'impostazione del Framework di destinazione, è anche possibile eseguire l'app in versioni precedenti di Android. Ad esempio, se si imposta il Framework di destinazione **Android 7.1 (Nougat)** e impostare la versione minima di Android **Android 4.0.3 (Ice Cream Sandwich)**, l'app può essere installato su qualsiasi piattaforma da API livello 15 a livello API 25, inclusivo.

Anche se l'app è stata può compilare e installare su questa ampia gamma di piattaforme, questo non garantisce che verrà completata *eseguire* su tutte queste piattaforme. Ad esempio, se l'app viene installata nei **Android 5.0 (Lollipop)** e il codice chiama un'API che è disponibile solo nelle **Android 7.1 (Nougat)** e successive, l'app verrà visualizzato un errore di runtime e possibilmente di arresto anomalo. Il codice deve pertanto assicurarsi &ndash; in fase di esecuzione &ndash; che chiama solo le API sono supportate per il dispositivo Android in cui viene eseguito. Il codice in altre parole, deve includere i controlli di runtime esplicita per garantire che l'app Usa API più recenti solo sui dispositivi che sono abbastanza recenti per supportarli.
[Controlli di runtime per le versioni di Android](#runtimechecks), più avanti in questa guida viene illustrato come aggiungere questi controlli di runtime al codice.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per accedere all'impostazione di versione minima di Android in Visual Studio, aprire le proprietà del progetto in **Esplora soluzioni** e selezionare il **manifesto Android** pagina. Nel menu a discesa sotto **Minimum Android version** è possibile selezionare la versione minima di Android per l'applicazione:

[![Versione minima di Android all'opzione di destinazione impostato per la compilazione con versione del SDK](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

Se si seleziona **utilizzare la compilazione con versione del SDK**, la versione minima di Android sarà quello utilizzato per l'impostazione del Framework di destinazione.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per accedere alla versione minima di Android in Visual Studio per Mac, il pulsante destro del mouse e selezionare **opzioni**; questo consente di aprire le **opzioni progetto** finestra di dialogo. Passare a **compilazione > applicazione Android**.
Usando il menu di riepilogo a discesa a destra della **Minimum Android version**, è possibile impostare la versione minima di Android per l'applicazione:

[![Versione minima di Android è impostato su automatico - versione di framework di destinazione di utilizzo](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

Se si seleziona **automatici &ndash; usare la versione di framework di destinazione**, la versione minima di Android sarà quello utilizzato per l'impostazione del Framework di destinazione.

-----


<a name="target" />

### <a name="target-android-version"></a>Versione di Android di destinazione

Il *la versione Android di destinazione* (noto anche come `targetSdkVersion`) è l'API a livello di dispositivo Android in cui l'app prevede di eseguire. Android Usa questa impostazione per determinare se abilitare eventuali comportamenti di compatibilità &ndash; in questo modo si garantisce che l'app continui a funzionare come previsto. Android Usa l'impostazione della versione Android di destinazione dell'app per individuare quali modifiche di comportamento possono essere applicate all'App senza interromperla (si tratta come Android fornisce compatibilità con le versioni).

Il Framework di destinazione e la versione Android di destinazione, mentre con nomi molto simili, non si equivalgono. L'impostazione del Framework di destinazione comunica informazioni a livello API di destinazione per xamarin. Android per l'uso *fase di compilazione*, mentre la versione di destinazione Android comunica informazioni a livello API di destinazione di Android per l'uso  *fase di esecuzione* (quando l'app è installato e in esecuzione in un dispositivo).

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per accedere a questa impostazione in Visual Studio, aprire le proprietà del progetto in **Esplora soluzioni** e selezionare il **manifesto Android** pagina. Nel menu a discesa sotto **Target Android version** è possibile selezionare la versione Android di destinazione per l'applicazione:

[![Versione di Android di destinazione impostato per la compilazione con versione del SDK](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

È consigliabile impostare in modo esplicito la versione Android di destinazione per la versione più recente di Android che consente di testare l'app. Idealmente, deve essere impostato per la versione più recente di Android SDK &ndash; questo modo è possibile usare le nuove API prima di lavorare con le modifiche del comportamento. Per la maggior parte degli sviluppatori, abbiamo *non li* consigliabile impostare la versione di destinazione Android **Usa la compilazione con versione del SDK**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per accedere a questa impostazione in Visual Studio per Mac, il pulsante destro del mouse e selezionare **opzioni**; questo consente di aprire le **opzioni progetto** finestra di dialogo. Passare a **compilazione > applicazione Android**. Usando il menu di riepilogo a discesa a destra della **Target Android version**, è possibile impostare la versione Android di destinazione per l'applicazione:

[![Versione di Android di destinazione impostato su automatico - versione di framework di destinazione di utilizzo](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

È consigliabile impostare in modo esplicito la versione Android di destinazione per la versione più recente di Android che consente di testare l'app. Idealmente, deve essere impostato per l'ultima versione disponibile di Android SDK &ndash; questo modo è possibile usare le nuove API prima di lavorare con le modifiche del comportamento. Per la maggior parte degli sviluppatori, non è consigliabile impostare la versione Android di destinazione **automatico - versione di framework di destinazione usare**.

-----

In generale, la versione Android di destinazione devono essere delimitata da versione minima di Android e il Framework di destinazione. Ovvero:

**Versione minima di Android < = versione Android di destinazione < = Framework di destinazione**

Per altre informazioni sui livelli di SDK, vedere l'Android Developer [usi sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) documentazione.


<a name="runtimechecks" />

## <a name="runtime-checks-for-android-versions"></a>Controlli di runtime per le versioni di Android

Quando viene rilasciato ogni nuova versione di Android, il framework API viene aggiornato per offrire nuove o funzionalità sostitutiva. Con poche eccezioni, la funzionalità di API rispetto alle versioni di Android precedenti viene trasferita in versioni più recenti di Android senza modifiche. Di conseguenza, se l'app viene eseguita in un determinato livello API Android, in genere sarà in grado di eseguire in un livello API Android versione successiva senza modifiche. Ma cosa accade se si vuole anche eseguire l'app in versioni precedenti di Android?

Se si seleziona una versione minima di Android *inferiore* rispetto l'impostazione del Framework di destinazione, alcune API potrebbero non essere disponibili per l'app in fase di esecuzione. Tuttavia, l'app può comunque eseguita in un dispositivo precedente, ma con funzionalità ridotte. Per ogni API che non è disponibile nelle piattaforme Android corrispondente all'impostazione di versione minima di Android, il codice deve verificare in modo esplicito il valore della `Android.OS.Build.VERSION.SdkInt` proprietà per determinare il livello API della piattaforma su cui è in esecuzione l'app. Se il livello API *inferiore* rispetto alla versione minima di Android che supporta l'API da chiamare, quindi il codice deve trovare un modo per funzionare correttamente senza effettuare questa chiamata API.

Ad esempio, si supponga che si desidera usare il [NotificationBuilder.SetCategory](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetCategory/p/System.String/) metodo per suddividere una notifica quando si esegue **Android 5.0 Lollipop** (e versioni successive), ma si vuole comunque che l'App nell'app per eseguire, ad esempio in versioni precedenti di Android **Android 4.1 Jelly Bean** (dove `SetCategory` non disponibile). Facendo riferimento alla tabella delle versioni di Android all'inizio di questa Guida, è possibile vedere che il codice della versione build per **Android 5.0 Lollipop** è `Android.OS.BuildVersionCodes.Lollipop`. Per supportare le versioni precedenti di Android where `SetCategory` è non disponibile, il nostro codice possibile rilevare il livello di API in fase di esecuzione e richiedere condizionatamente `SetCategory` solo quando il livello API è maggiore o uguale al codice Lollipop compilazione versione:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

In questo esempio, il Framework di destinazione dell'app è impostato su **Android 5.0 (API livello 21)** e la versione minima di Android è impostata su **Android 4.1 (livello API 16)**. In quanto `SetCategory` è disponibile nel livello API `Android.OS.BuildVersionCodes.Lollipop` e versioni successive, questo esempio di codice chiamerà `SetCategory` solo quando è effettivamente disponibile &ndash; verrà *non* tenta di chiamare `SetCategory` quando l'API livello è 16, 17, 18, 19 o 20. La funzionalità viene ridotto in queste versioni di Android in precedenza solo nella misura in cui le notifiche non sono ordinate correttamente (in quanto essi non sono organizzati per tipo), anche se le notifiche vengono comunque pubblicate per avvisare l'utente. L'app continui a funzionare, ma la relativa funzionalità è leggermente ridotta.

In generale, il controllo della versione build aiuta il codice a decidere in fase di esecuzione tra adottato il nuovo modo rispetto alla vecchia maniera. Ad esempio:

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

Non sono presenti regole veloce e semplice che illustra come ridurre o modificare le funzionalità dell'app quando è in esecuzione in versioni precedenti di Android che mancano una o più API. In alcuni casi (ad esempio nel `SetCategory` esempio precedente), è sufficiente omettere la chiamata dell'API quando non è disponibile. Tuttavia, in altri casi, potrebbe essere necessario implementare funzionalità alternativa quando `Android.OS.Build.VERSION.SdkInt` viene rilevata deve per essere inferiore rispetto all'API di livello che l'app necessita di presentare la sua esperienza ottimale.

<a name="libraries" />

## <a name="api-levels-and-libraries"></a>Le librerie e i livelli API

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Quando si crea un progetto di libreria xamarin. Android (ad esempio una libreria di classi o una libreria di binding), è possibile configurare solo l'impostazione del Framework di destinazione &ndash; la versione minima di Android e le impostazioni di versione Android di destinazione non sono disponibili. Perché è presente alcun **manifesto Android** pagina:

[![È disponibile solo la compilazione usando l'opzione di versione di Android](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Quando si crea un progetto di libreria xamarin. Android, è presente alcun **applicazione Android** pagina in cui è possibile configurare la versione minima di Android e la versione di destinazione Android &ndash; la versione minima di Android e destinazione Impostazioni versione di Android non sono disponibili.
Perché è presente alcun **compilazione > applicazione Android** pagina:

[![Pagina generale senza le opzioni di versione minimo e di destinazione di compilazione](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

La versione minima di Android e le impostazioni delle versioni di Android di destinazione non sono disponibili perché la raccolta risulta non è un'app autonoma &ndash; la libreria può essere eseguita in qualsiasi versione di Android, a seconda dell'app che viene incluso nel pacchetto con. È possibile specificare come la libreria deve essere *compilato*, ma non è possibile prevedere quale piattaforma di livello API della libreria deve essere eseguita su. Con questa premessa, saranno esaminate le procedure consigliate seguenti quando si utilizzano o creazione di librerie:

-   **Quando si utilizza una libreria Android** &ndash; se si sta utilizzando una libreria Android nell'applicazione, assicurarsi di impostare il Framework di destinazione dell'app l'impostazione per un'API livello vale a dire *almeno rilevarono* la destinazione Impostazione del framework della libreria.

-   **Quando si crea una libreria Android** &ndash; se si sta creando una libreria Android per l'uso da altre applicazioni, assicurarsi di impostare l'impostazione del Framework di destinazione per il livello API minimo necessarie per la compilazione.

Queste procedure consigliate sono consigliate per evitare che la situazione in cui una libreria tenta di chiamare un'API che non è disponibile in fase di esecuzione (che può causare l'arresto anomalo dell'app). Se sei uno sviluppatore di libreria, è consigliabile limitare l'utilizzo di chiamate API per un subset di piccole dimensioni e consolidato dell'area della superficie totale di API. In tal modo eviterai assicurarsi che la libreria può essere utilizzata in modo sicuro tra versioni in più ampia gamma di Android.


## <a name="summary"></a>Riepilogo

Questa guida ha illustrato come i livelli API Android vengono utilizzati per gestire la compatibilità delle app tra versioni diverse di Android. Fornita la procedura dettagliata per la configurazione di xamarin. Android *Framework di destinazione*, *Minimum Android version*, e *Target Android version* impostazioni del progetto. Fornito istruzioni per l'uso di Android SDK Manager per installare i pacchetti SDK, inclusi esempi di come scrivere codice per gestire diversi livelli di API in fase di esecuzione ed è stato spiegato come gestire i livelli API durante la creazione o l'elaborazione delle librerie di Android. Ha anche fornito un elenco completo che correla i livelli di API per i numeri di versione di Android (ad esempio Android 4.4), i nomi di versione di Android (ad esempio Kitkat) e i codici versione build di xamarin. Android.


## <a name="related-links"></a>Collegamenti correlati

- [Installazione di Android SDK](~/android/get-started/installation/android-sdk.md)
- [Gli strumenti CLI di SDK viene modificato](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Selezione di compileSdkVersion minSdkVersion e targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [Qual è il livello API?](http://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [Nomi in codice i tag e i numeri di Build](https://source.android.com/source/build-numbers)
