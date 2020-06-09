---
title: Informazioni sui livelli dell'API Android
description: Novell. Android presenta diverse impostazioni a livello di API Android che determinano la compatibilità dell'app con più versioni di Android. Questa guida illustra le implicazioni di queste impostazioni, le modalità di configurazione e l'effetto che hanno sull'app in fase di esecuzione.
ms.prod: xamarin
ms.assetid: 58CB7B34-3140-4BEB-BE2E-209928C1878C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 188cc5b2ffba4540766edf0403d3a55228a21d6d
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84568984"
---
# <a name="understanding-android-api-levels"></a>Understanding Android API Levels (Informazioni sui livelli API Android)

_Novell. Android presenta diverse impostazioni a livello di API Android che determinano la compatibilità dell'app con più versioni di Android. Questa guida illustra le implicazioni di queste impostazioni, le modalità di configurazione e l'effetto che hanno sull'app in fase di esecuzione._

## <a name="quick-start"></a>Avvio rapido

Novell. Android espone tre impostazioni di progetto a livello di API Android:

- [Framework](#framework) &ndash; di destinazione Specifica il Framework da usare per la compilazione dell'applicazione. Questo livello API viene usato in fase di *compilazione* da Novell. Android.

- Versione minima di [Android](#minimum) &ndash; Specifica la versione di Android meno recente che si vuole che l'app supporti. Questo livello API viene usato in fase di *esecuzione* da Android.

- Versione di destinazione di [Android](#target) &ndash; Specifica la versione di Android in cui deve essere eseguita l'app. Questo livello API viene usato in fase di *esecuzione* da Android.

Prima di poter configurare un livello API per il progetto, è necessario installare i componenti della piattaforma SDK per tale livello API. Per ulteriori informazioni sul download e sull'installazione dei componenti di Android SDK, vedere [Android SDK installazione](~/android/get-started/installation/android-sdk.md).

> [!NOTE]
> A partire dall'agosto 2018, la console di Google Play richiederà che le nuove app siano destinate a livello API 26 (Android 8,0) o versione successiva.
Le app esistenti saranno necessarie per il livello API di destinazione 26 o superiore a partire dal 2018 novembre. Per ulteriori informazioni, vedere [migliorare la sicurezza delle app e le prestazioni in Google Play per anni](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html).

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

In genere, tutti e tre i livelli di API Novell. Android sono impostati sullo stesso valore. Nella pagina **dell'applicazione** impostare **Compila usando la versione di Android (Framework di destinazione)** per la versione più recente dell'API stabile (o, come minimo, per la versione di Android con tutte le funzionalità necessarie).
Nello screenshot seguente il Framework di destinazione è impostato su **Android 7,1 (API Level 25-torrone)**:

[![Impostazioni predefinite della versione del Framework di destinazione da compilare con la versione di Android](android-api-levels-images/vs-defaults-sml.png)](android-api-levels-images/vs-defaults.png#lightbox)

Nella pagina del **manifesto Android** impostare la versione minima di Android per **usare la compilazione con la versione SDK** e impostare la versione di Android di destinazione sullo stesso valore della versione del Framework di destinazione. nello screenshot seguente, il Framework di Android di destinazione è impostato su **Android 7,1 (torrone)**):

[![Versioni di Android minime e di destinazione impostate sulla versione del Framework di destinazione](android-api-levels-images/vs-manifest-defaults-sml.png)](android-api-levels-images/vs-manifest-defaults.png#lightbox)

Se si vuole mantenere la compatibilità con le versioni precedenti di Android, impostare la **versione minima di Android su destinazione** per la versione meno recente di Android che si vuole che l'app supporti. Si noti che il livello API 14 è il livello API minimo richiesto per i [servizi Google Play e il supporto Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html). La configurazione di esempio seguente supporta le versioni di Android dal livello API 14 al livello API 25:

[![Eseguire la compilazione usando l'API Level 25 torrone, versione minima di Android impostata sul livello API 14](android-api-levels-images/vs-minimum-sml.png)](android-api-levels-images/vs-minimum.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

In genere, tutti e tre i livelli di API Novell. Android sono impostati sullo stesso valore. Impostare **Framework di destinazione** sulla versione più recente dell'API stabile (o, come minimo, per la versione di Android con tutte le funzionalità necessarie). Per impostare il **Framework di destinazione**, passare a **Compila > generale** nelle **Opzioni del progetto**. Nella schermata seguente, il Framework di destinazione è impostato per **usare la piattaforma installata più recente (8,0)**:

[![Impostazione predefinita del Framework di destinazione per l'utilizzo della piattaforma installata più recente](android-api-levels-images/xs-default-target-sml.png)](android-api-levels-images/xs-default-target.png#lightbox)

Le impostazioni versione minima e di destinazione di Android sono disponibili in **compilare > applicazione Android** in **Opzioni progetto**. Impostare la versione minima di Android su **automatico: usare la versione del Framework di destinazione** e impostare la versione di Android di destinazione sullo stesso valore della versione del Framework di destinazione. Nello screenshot seguente il Framework di Android di destinazione è impostato su **android 8,0 (livello API 26)** in modo che corrisponda all'impostazione del Framework di destinazione precedente:

[![Impostazione dei livelli di destinazione e di Framework nelle opzioni del progetto](android-api-levels-images/xs-default-app-sml.png)](android-api-levels-images/xs-default-app.png#lightbox)

Se si vuole mantenere la compatibilità con le versioni precedenti di Android, modificare la **versione minima di Android** con la versione meno recente di Android che si vuole che l'app supporti. Si noti che il livello API 14 è il livello API minimo richiesto per i [servizi Google Play e il supporto Firebase](https://android-developers.googleblog.com/2016/11/google-play-services-and-firebase-for-android-will-support-api-level-14-at-minimum.html).
Ad esempio, la configurazione seguente supporta le versioni di Android come prima del livello API 14:

[![Versioni minime e di destinazione impostate su automatico: usare la versione del Framework di destinazione](android-api-levels-images/xs-minimum-sml.png)](android-api-levels-images/xs-minimum.png#lightbox)

-----

Se l'app supporta più versioni di Android, il codice deve includere i controlli di runtime per assicurarsi che l'app funzioni con l'impostazione minima della versione Android (vedere i [controlli di runtime per le versioni di Android](#runtimechecks) più avanti per informazioni dettagliate). Se si utilizza o si crea una libreria, vedere le [librerie e i livelli API](#libraries) seguenti per le procedure consigliate per la configurazione delle impostazioni a livello di API per le librerie.

## <a name="android-versions-and-api-levels"></a>Versioni di Android e livelli API

Man mano che la piattaforma Android evolve e vengono rilasciate nuove versioni di Android, a ogni versione di Android viene assegnato un identificatore intero univoco, denominato *livello API*. Ogni versione di Android corrisponde quindi a un singolo livello API Android. Poiché gli utenti installano le app in versioni precedenti e le versioni più recenti di Android, le app Android reali devono essere progettate per funzionare con più livelli di API Android.

### <a name="android-versions"></a>Versioni di Android

Ogni versione di Android va da più nomi:

- Versione di Android, ad esempio **android 9,0**
- Nome di codice (o dessert), ad esempio _torta_
- Un livello API corrispondente, ad esempio il **livello API 28**

Un nome di codice Android può corrispondere a più versioni e a livelli API, come illustrato nella tabella seguente, ma ogni versione di Android corrisponde esattamente a un livello API.

Inoltre, Novell. Android definisce i *codici della versione di compilazione* che vengono mappati ai livelli API Android attualmente noti. La tabella seguente può essere utile per la conversione tra il livello API, la versione di Android, il nome del codice e il codice della versione build di Novell. Android (i codici della versione di build sono definiti nello `Android.OS` spazio dei nomi):

[!include[](~/android/includes/api-levels.md)]

Come indicato nella tabella, le nuove versioni di Android vengono rilasciate spesso &ndash; a volte più di una versione all'anno. Di conseguenza, l'universo di dispositivi Android che potrebbero eseguire l'app include una vasta gamma di versioni di Android precedenti e più recenti. Come è possibile garantire che l'app venga eseguita in modo coerente e affidabile in un numero così elevato di versioni diverse di Android? I livelli API di Android possono aiutare a gestire questo problema.

### <a name="android-api-levels"></a>Livelli di API Android

Ogni dispositivo Android viene eseguito esattamente a *un* livello API &ndash; . questo livello API è sicuramente univoco per ogni versione della piattaforma Android. Il livello API identifica con precisione la versione del set di API che può essere chiamata dall'app; identifica la combinazione di elementi manifesto, autorizzazioni e così via con cui si esegue il codice come sviluppatore. Il sistema dei livelli API di Android aiuta Android a determinare se un'applicazione è compatibile con un'immagine del sistema Android prima di installare l'applicazione in un dispositivo.

Quando viene compilata un'applicazione, contiene le informazioni sul livello API seguenti:

- Il livello API di *destinazione* di Android su cui è compilata l'app per l'esecuzione.

- Il livello *minimo* dell'API Android necessario per l'esecuzione dell'app per un dispositivo Android. 

Queste impostazioni vengono usate per assicurarsi che la funzionalità necessaria per eseguire correttamente l'app sia disponibile nel dispositivo Android al momento dell'installazione. In caso contrario, l'esecuzione dell'app è bloccata nel dispositivo. Ad esempio, se il livello API di un dispositivo Android è inferiore al livello API minimo specificato per l'app, il dispositivo Android impedisce all'utente di installare l'app.

## <a name="project-api-level-settings"></a>Impostazioni livello API progetto

Le sezioni seguenti illustrano come usare SDK Manager per preparare l'ambiente di sviluppo per i livelli API di destinazione, seguiti da spiegazioni dettagliate su come configurare il *Framework di destinazione*, la *versione minima di Android*e le impostazioni della versione di Android di *destinazione* in Novell. Android.

### <a name="android-sdk-platforms"></a>Piattaforme Android SDK

Prima di poter selezionare una destinazione o un livello API minimo in Novell. Android, è necessario installare la versione della piattaforma Android SDK corrispondente a tale livello API. L'intervallo di opzioni disponibili per il Framework di destinazione, la versione minima di Android e la versione di destinazione di Android è limitato all'intervallo di versioni di Android SDK installate. È possibile usare SDK Manager per verificare che le versioni Android SDK richieste siano installate ed è possibile usarle per aggiungere i nuovi livelli API necessari per l'app. Se non si ha familiarità con la procedura di installazione dei livelli API, vedere [Android SDK installazione](~/android/get-started/installation/android-sdk.md).

<a name="framework"></a>

### <a name="target-framework"></a>Framework di destinazione

Il *Framework di destinazione* (noto anche come `compileSdkVersion` ) è la versione specifica del Framework Android (livello API) per la compilazione dell'app in fase di compilazione. Questa impostazione specifica le API che l'app *prevede* di usare durante l'esecuzione, ma non ha alcun effetto sulle API attualmente disponibili per l'app quando viene installata. Di conseguenza, la modifica dell'impostazione del Framework di destinazione non comporta la modifica del comportamento in fase di esecuzione.

Il Framework di destinazione identifica le versioni di libreria che l'applicazione è collegata &ndash; a questa impostazione determina le API che è possibile usare nell'app. Se ad esempio si vuole usare il metodo [NotificationBuilder. secategory](xref:Android.App.Notification.Builder.SetCategory*) introdotto in Android 5,0 Lollipop, è necessario impostare il Framework di destinazione su **API level 21 (Lollipop)** o versione successiva. Se si imposta il Framework di destinazione del progetto su un livello API, ad esempio **API level 19 (KitKat)** e si tenta di chiamare il `SetCategory` metodo nel codice, si otterrà un errore di compilazione.

Si consiglia di eseguire sempre la compilazione con la versione *più recente* del Framework di destinazione disponibile. Questa operazione fornisce messaggi di avviso utili per le API deprecate che potrebbero essere chiamate dal codice. Usare la versione più recente del Framework di destinazione è particolarmente importante quando si usano le versioni più recenti della libreria &ndash; di supporto. ogni libreria prevede che l'app venga compilata in base al livello API minimo o superiore della libreria di supporto.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Per accedere all'impostazione del Framework di destinazione in Visual Studio, aprire le proprietà del progetto in **Esplora soluzioni** e selezionare la pagina **dell'applicazione** :

[![Pagina applicazione delle proprietà del progetto](android-api-levels-images/vs-target-framework-sml.png)](android-api-levels-images/vs-target-framework.png#lightbox)

Per impostare il Framework di destinazione, selezionare un livello API nel menu a discesa in **Compila usando la versione di Android** , come illustrato in precedenza.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Per accedere all'impostazione del Framework di destinazione in Visual Studio per Mac, fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Opzioni**. verrà visualizzata la finestra di dialogo **Opzioni progetto** . In questa finestra di dialogo passare a **compila > generale** , come illustrato di seguito:

[![Compilare la sezione generale della pagina Opzioni progetto](android-api-levels-images/xs-target-framework-sml.png)](android-api-levels-images/xs-target-framework.png#lightbox)

Impostare il Framework di destinazione selezionando un livello API nel menu a discesa a destra di Framework di **destinazione** , come illustrato in precedenza.

-----

<a name="minimum"></a>

### <a name="minimum-android-version"></a>Versione minima di Android

La *versione minima di Android* (nota anche come `minSdkVersion` ) è la versione meno recente del sistema operativo Android (ovvero il livello API più basso) che può installare ed eseguire l'applicazione. Per impostazione predefinita, un'app può essere installata solo nei dispositivi che corrispondono all'impostazione del Framework di destinazione o successiva. Se l'impostazione minima della versione di Android è *inferiore* a quella dell'impostazione del Framework di destinazione, l'app può essere eseguita anche in versioni precedenti di Android. Se ad esempio si imposta il Framework di destinazione su **android 7,1 (torrone)** e si imposta la versione minima di Android su **Android 4.0.3 (Ice Cream Sandwich)**, l'app può essere installata su qualsiasi piattaforma, dal livello API 15 al livello API 25, inclusi.

Sebbene l'app possa essere compilata e installata correttamente in questa gamma di piattaforme, questo non garantisce che venga *eseguito* correttamente su tutte queste piattaforme. Se, ad esempio, l'app è installata in **android 5,0 (Lollipop)** e il codice chiama un'API disponibile solo in **Android 7,1 (torrone)** e versioni successive, l'app riceverà un errore di runtime e potrebbe arrestarsi in modo anomalo. Il codice deve quindi assicurarsi &ndash; in fase di esecuzione &ndash; che chiami solo le API supportate dal dispositivo Android su cui è in esecuzione. In altre parole, il codice deve includere controlli di runtime espliciti per assicurarsi che l'app usi le API più recenti solo nei dispositivi abbastanza recenti per supportarli.
I [controlli di runtime per le versioni di Android](#runtimechecks), più avanti in questa guida, spiegano come aggiungere questi controlli di runtime al codice.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Per accedere all'impostazione minima della versione di Android in Visual Studio, aprire le proprietà del progetto in **Esplora soluzioni** e selezionare la pagina **manifesto Android** . Nel menu a discesa sotto **versione minima di Android** è possibile selezionare la versione minima di Android per l'applicazione:

[![Opzione minima da Android a destinazione impostata su Compila con la versione dell'SDK](android-api-levels-images/vs-minimum-version-sml.png)](android-api-levels-images/vs-minimum-version.png#lightbox)

Se si seleziona **USA compila con la versione SDK**, la versione minima di Android sarà identica a quella dell'impostazione del Framework di destinazione.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Per accedere alla versione minima di Android in Visual Studio per Mac, fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Opzioni**. verrà visualizzata la finestra di dialogo **Opzioni progetto** . Passare a **compilare > applicazione Android**.
Usando il menu a discesa a destra della **versione minima di Android**, è possibile impostare la versione minima di Android per l'applicazione:

[![Versione minima di Android impostata su automatica: usare la versione del Framework di destinazione](android-api-levels-images/xs-minimum-version-sml.png)](android-api-levels-images/xs-minimum-version.png#lightbox)

Se si seleziona l'opzione ** &ndash; Usa la versione del Framework di destinazione automatica**, la versione minima di Android sarà identica a quella dell'impostazione del Framework di destinazione.

-----

<a name="target"></a>

### <a name="target-android-version"></a>Versione di destinazione di Android

La *versione di destinazione di Android* (nota anche come `targetSdkVersion` ) è il livello API del dispositivo Android in cui è prevista l'esecuzione dell'app. Android usa questa impostazione per determinare se abilitare eventuali comportamenti di compatibilità in &ndash; modo da garantire che l'app continui a funzionare come previsto. Android usa l'impostazione della versione di Android di destinazione dell'app per individuare le modifiche del comportamento che è possibile applicare all'app senza suddividerle (questo è il modo in cui Android fornisce la compatibilità con le versioni con il futuro).

Il Framework di destinazione e la versione di destinazione di Android, con nomi molto simili, non sono la stessa cosa. L'impostazione del Framework di destinazione comunica le informazioni a livello di API di destinazione a Novell. Android per l'uso in *fase di compilazione*, mentre la versione di destinazione di Android comunica le informazioni a livello di API di destinazione ad Android per l'uso in fase di *esecuzione* (quando l'app viene installata e in esecuzione in un dispositivo).

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Per accedere a questa impostazione in Visual Studio, aprire le proprietà del progetto in **Esplora soluzioni** e selezionare la pagina **manifesto Android** . Nel menu a discesa in versione di **destinazione di Android** è possibile selezionare la versione di destinazione di Android per l'applicazione:

[![Versione di destinazione di Android impostata per la compilazione con la versione dell'SDK](android-api-levels-images/vs-target-version-sml.png)](android-api-levels-images/vs-target-version.png#lightbox)

Si consiglia di impostare in modo esplicito la versione di Android di destinazione sulla versione più recente di Android usata per testare l'app. Idealmente, è consigliabile impostare la versione più recente di Android SDK &ndash; Questo consente di usare le nuove API prima di eseguire le modifiche del comportamento. Per la maggior parte degli sviluppatori, *non* è consigliabile impostare la versione di Android di destinazione per l' **uso della compilazione con la versione dell'SDK**.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Per accedere a questa impostazione in Visual Studio per Mac, fare clic con il pulsante destro del mouse sul nome del progetto e scegliere **Opzioni**. verrà visualizzata la finestra di dialogo **Opzioni progetto** . Passare a **compilare > applicazione Android**. Usando il menu a discesa a destra della versione di **destinazione di Android**, è possibile impostare la versione di destinazione di Android per l'applicazione:

[![Versione di destinazione di Android impostata su automatico: usare la versione del Framework di destinazione](android-api-levels-images/xs-target-version-sml.png)](android-api-levels-images/xs-target-version.png#lightbox)

Si consiglia di impostare in modo esplicito la versione di Android di destinazione sulla versione più recente di Android usata per testare l'app. Idealmente, è consigliabile impostare la versione più recente di Android SDK disponibile &ndash; . Questo consente di usare le nuove API prima di eseguire le modifiche del comportamento. Per la maggior parte degli sviluppatori, non è consigliabile impostare la versione di Android di destinazione per l' **uso automatico della versione del Framework di destinazione**.

-----

In generale, la versione di Android di destinazione deve essere limitata dalla versione minima di Android e dal framework di destinazione. Ovvero:

**Versione minima di Android <= versione di Android di destinazione <= framework di destinazione**

Per ulteriori informazioni sui livelli SDK, vedere la documentazione relativa all' [utilizzo](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html) di SDK per sviluppatori Android.

<a name="runtimechecks"></a>

## <a name="runtime-checks-for-android-versions"></a>Controlli di runtime per le versioni di Android

Quando viene rilasciata una nuova versione di Android, l'API del Framework viene aggiornata per fornire funzionalità nuove o di sostituzione. Con poche eccezioni, le funzionalità delle API di versioni precedenti di Android vengono trasferite nelle versioni più recenti di Android senza modifiche. Di conseguenza, se l'app viene eseguita in un particolare livello API Android, sarà in genere possibile eseguirla in un livello API Android successivo senza apportare modifiche. Ma cosa accade se si vuole anche eseguire l'app in versioni precedenti di Android?

Se si seleziona una versione minima di Android *inferiore* rispetto all'impostazione del Framework di destinazione, alcune API potrebbero non essere disponibili per l'app in fase di esecuzione. Tuttavia, l'app può comunque essere eseguita su un dispositivo precedente, ma con funzionalità ridotte. Per ogni API che non è disponibile nelle piattaforme Android corrispondenti all'impostazione minima della versione di Android, il codice deve controllare in modo esplicito il valore della `Android.OS.Build.VERSION.SdkInt` proprietà per determinare il livello API della piattaforma in cui è in esecuzione l'app. Se il livello API è *inferiore* alla versione minima di Android che supporta l'API che si vuole chiamare, il codice deve trovare un modo per funzionare correttamente senza effettuare questa chiamata API.

Si supponga, ad esempio, di voler usare il metodo [NotificationBuilder. secategory](xref:Android.App.Notification.Builder.SetCategory*) per suddividere in categorie una notifica in caso di esecuzione in **Android 5,0 Lollipop** (e versioni successive), ma si vuole comunque che l'app venga eseguita in versioni precedenti di Android, ad esempio **Android 4,1 Jelly Bean** (dove `SetCategory` non è disponibile). Facendo riferimento alla tabella delle versioni di Android all'inizio di questa guida, si noterà che il codice della versione della build per **android 5,0 Lollipop** è `Android.OS.BuildVersionCodes.Lollipop` . Per supportare le versioni precedenti di Android `SetCategory` in cui non è disponibile, il codice può rilevare il livello API in fase di esecuzione e chiamare in modo condizionale `SetCategory` solo quando il livello API è maggiore o uguale al codice della versione della build Lollipop:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop)
{
    builder.SetCategory(Notification.CategoryEmail);
}
```

In questo esempio, il Framework di destinazione dell'app è impostato su **android 5,0 (livello API 21)** e la versione minima di Android è impostata su **Android 4,1 (livello API 16)**. Poiché `SetCategory` è disponibile a livello di API `Android.OS.BuildVersionCodes.Lollipop` e versioni successive, questo codice di esempio chiamerà `SetCategory` solo quando è effettivamente disponibile, &ndash; *non* tenterà di chiamare `SetCategory` quando il livello API è 16, 17, 18, 19 o 20. La funzionalità è ridotta in queste versioni precedenti di Android solo nella misura in cui le notifiche non vengono ordinate correttamente, perché non sono categorizzate in base al tipo, ma le notifiche vengono ancora pubblicate per avvisare l'utente. L'app funziona ancora, ma la sua funzionalità è leggermente ridotta.

In generale, il controllo della versione di compilazione consente al codice di decidere in fase di esecuzione tra l'esecuzione di un nuovo metodo rispetto al metodo precedente. Ad esempio:

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

Non esiste alcuna regola rapida e semplice che illustra come ridurre o modificare la funzionalità dell'app quando viene eseguita in versioni di Android precedenti che non hanno una o più API. In alcuni casi, ad esempio nell' `SetCategory` esempio precedente, è sufficiente omettere la chiamata API quando non è disponibile. Tuttavia, in altri casi, potrebbe essere necessario implementare una funzionalità alternativa per quando `Android.OS.Build.VERSION.SdkInt` viene rilevato che è inferiore al livello API necessario all'app per presentare la propria esperienza ottimale.

<a name="libraries"></a>

## <a name="api-levels-and-libraries"></a>Librerie e livelli API

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Quando si crea un progetto di libreria Novell. Android (ad esempio una libreria di classi o una libreria di associazioni), è possibile configurare solo l'impostazione del Framework di destinazione &ndash; la versione minima di Android e le impostazioni della versione di Android di destinazione non sono disponibili. Ciò è dovuto al fatto che non esiste alcuna pagina del **manifesto Android** :

[![È disponibile solo l'opzione Compila con la versione Android](android-api-levels-images/vs-library-options-sml.png)](android-api-levels-images/vs-library-options.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Quando si crea un progetto di libreria Novell. Android, non è disponibile alcuna pagina **dell'applicazione Android** in cui è possibile configurare la versione minima di Android e la versione di Android di destinazione. &ndash; le impostazioni minime per la versione di Android e la versione di Android di destinazione non sono disponibili.
Ciò è dovuto al fatto che non esiste alcuna pagina di **compilazione > applicazione Android** :

[![Pagina generale di compilazione senza opzioni versione minima e di destinazione](android-api-levels-images/xs-library-options-sml.png)](android-api-levels-images/xs-library-options.png#lightbox)

-----

Le impostazioni minime per la versione di Android e la versione di Android di destinazione non sono disponibili perché la libreria risultante non è un'app autonoma &ndash; che la libreria può essere eseguita in qualsiasi versione di Android, a seconda dell'app con cui viene incluso nel pacchetto. È possibile specificare il modo in cui la libreria deve essere *compilata*, ma non è possibile prevedere il livello API della piattaforma in cui verrà eseguita la libreria. Tenendo presente questo aspetto, è opportuno osservare le procedure consigliate seguenti quando si utilizzano o si creano librerie:

- **Quando si usa una libreria Android** &ndash; Se si utilizza una libreria Android nell'applicazione, assicurarsi di impostare l'impostazione del Framework di destinazione dell'applicazione su un livello API almeno *quanto* l'impostazione del Framework di destinazione della libreria.

- **Quando si crea una libreria Android** &ndash; Se si sta creando una libreria Android per l'uso da parte di altre applicazioni, assicurarsi di impostare la relativa impostazione del Framework di destinazione sul livello API minimo necessario per la compilazione.

Queste procedure consigliate sono consigliate per evitare la situazione in cui una libreria tenta di chiamare un'API che non è disponibile in fase di esecuzione (che può causare l'arresto anomalo dell'app). Se si è uno sviluppatore di librerie, è consigliabile limitare l'utilizzo delle chiamate API a un subset piccolo e ben stabilito della superficie di attacco dell'API totale. Questa operazione consente di garantire che la libreria possa essere usata in modo sicuro in una gamma più ampia di versioni di Android.

## <a name="summary"></a>Summary

Questa guida ha illustrato come vengono usati i livelli di API Android per gestire la compatibilità delle app in versioni diverse di Android. Sono stati forniti i passaggi dettagliati per configurare il *Framework di destinazione*Novell. Android, la *versione minima di Android*e le impostazioni di progetto della *versione di Android di destinazione* . Sono state fornite istruzioni per l'uso di gestione Android SDK per installare i pacchetti SDK, inclusi esempi di come scrivere codice per gestire diversi livelli API in fase di esecuzione e come gestire i livelli di API durante la creazione o l'utilizzo di librerie Android. È stato inoltre fornito un elenco completo che mette in correlazione i livelli API ai numeri di versione di Android, ad esempio Android 4,4, i nomi delle versioni Android (ad esempio KitKat) e i codici della versione di build Novell. Android.

## <a name="related-links"></a>Collegamenti correlati

- [Installazione di Android SDK](~/android/get-started/installation/android-sdk.md)
- [Modifiche agli strumenti dell'interfaccia della riga di comando SDK](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Selezione di compileSdkVersion, minSdkVersion e targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd)
- [Che cos'è il livello API?](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
- [Codenames, tag e numeri di Build](https://source.android.com/source/build-numbers)
