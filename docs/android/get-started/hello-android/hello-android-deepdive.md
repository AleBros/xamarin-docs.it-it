---
title: 'Hello, Android: approfondimenti'
description: In questa guida in due parti è possibile creare un'applicazione Xamarin.Android per la prima volta e comprendere le nozioni di base dello sviluppo di applicazioni Android con Xamarin. Verranno descritti gli strumenti, i concetti e i passaggi necessari per creare e distribuire un'applicazione Xamarin.Android.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: EF0E110B-20EA-43F6-9476-1A0F41AFD298
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 10a46c916654f8421dc5a9af93de3abbbae5e934
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304037"
---
# <a name="hello-android-deep-dive"></a>Hello, Android: approfondimenti

_In questa guida in due parti, si creerà la prima applicazione Xamarin.Android e sviluppare una comprensione dei fondamenti dello sviluppo di applicazioni Android con Xamarin. Lungo la strada, si verrà introdotti gli strumenti, concetti e passaggi necessari per compilare e distribuire un'applicazione Xamarin.Android._

In [Hello, Android: guida introduttiva](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-quickstart.md) è stata compilata ed eseguita la prima applicazione Xamarin.Android. È ora il momento di acquisire una conoscenza più approfondita del funzionamento delle applicazioni Android, per poter creare programmi più complessi. Questa guida riprende i passaggi eseguiti nella procedura dettagliata relativa a Hello, Android, per consentire la comprensione delle operazioni effettuate e la conoscenza delle funzioni fondamentali dello sviluppo di applicazioni Android.

Questa guida tratta gli argomenti seguenti:

::: zone pivot="windows"

- **Introduzione a Visual Studio** &ndash; Introduzione a Visual Studio e creazione di una nuova applicazione Xamarin.Android.

- **Anatomia di un'applicazione Xamarin.Android**: panoramica delle parti essenziali di un'applicazione Xamarin.Android.

- **Concetti fondamentali sulle app e nozioni di base sull'architettura** &ndash; Introduzione alle attività e al manifesto Android e caratteristiche generali dello sviluppo di app Android.

- **Interfaccia utente** &ndash; Creazione di interfacce utente con Android Designer.

- Attività e ciclo di &ndash; **vita dell'attività** Un'introduzione al ciclo di vita dell'attività e il cablaggio dell'interfaccia utente nel codice.

- **Test, distribuzione e tocco di finitura** &ndash; Completa l'applicazione con consigli su test, distribuzione, generazione di grafica e altro ancora.

::: zone-end
::: zone pivot="macos"

- **Introduzione a Visual Studio per Mac** &ndash; Introduzione a Visual Studio per Mac e creazione di una nuova applicazione Xamarin.Android.

- **Anatomia di un'applicazione Xamarin.Android** &ndash; Presentazione delle parti essenziali di un'applicazione Xamarin.Android.

- **Concetti fondamentali sulle app e nozioni di base sull'architettura** &ndash; Introduzione alle attività e al manifesto Android e caratteristiche generali dello sviluppo di app Android.

- **Interfaccia utente** &ndash; Creazione di interfacce utente con Android Designer.

- **Attività e ciclo di vita delle attività** &ndash; Introduzione al ciclo di vita delle attività e all'associazione dell'interfaccia utente nel codice.

- **Test, distribuzione e rifiniture** &ndash; Completamento dell'applicazione con suggerimenti su esecuzione di test, distribuzione, generazione di elementi grafici e altro ancora.

::: zone-end

Questa guida consente di sviluppare le competenze e le conoscenze necessarie per creare un'applicazione Android a schermata singola. Al termine di questa guida, si sarà in grado di comprendere le diverse parti di un'applicazione Xamarin.Android e il modo in cui si integrano tra loro.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introduzione a Visual Studio

Visual Studio è un potente ambiente di sviluppo integrato di Microsoft. Questo ambiente Include una finestra di progettazione visiva completamente integrata, un editor di testo completo di strumenti di refactoring, un browser di assembly, l'integrazione del codice sorgente e altro ancora. In questa guida si apprenderà come usare alcune funzionalità di base di Visual Studio con il plug-in Xamarin.

Visual Studio organizza il codice in _soluzioni_ e _progetti_. Una soluzione è un contenitore per uno o più progetti. Un progetto può essere un'applicazione (ad esempio per iOS o Android), una libreria di supporto, un'applicazione di test e altro ancora. Nell'app **Phoneword**, tramite il modello **Applicazione Android** è stato aggiunto un nuovo progetto Android alla soluzione **Phoneword** creata nella guida [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introduzione a Visual Studio per Mac

Visual Studio per Mac è un ambiente di sviluppo integrato open source gratuito simile a Visual Studio. Include una finestra di progettazione visiva completamente integrata, un editor di testo completo di strumenti di refactoring, un browser di assembly, l'integrazione del codice sorgente e altro ancora. In questa guida si apprenderà come usare alcune funzionalità di Visual Studio per Mac. Se non si ha familiarità con Visual Studio per Mac, è necessario leggere la guida [Introduzione a Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/), più approfondita.

Visual Studio per Mac segue la prassi di Visual Studio di organizzare il codice in _soluzioni_ e _progetti_. Una soluzione è un contenitore per uno o più progetti. Un progetto può essere un'applicazione (ad esempio per iOS o Android), una libreria di supporto, un'applicazione di test e altro ancora. Nell'app **Phoneword**, tramite il modello **Applicazione Android** è stato aggiunto un nuovo progetto Android alla soluzione **Phoneword** creata nella guida [Hello, Android](~/android/get-started/hello-android/hello-android-quickstart.md).

::: zone-end

<a name="anatomy" />

## <a name="anatomy-of-a-xamarinandroid-application"></a>Anatomia di un'applicazione Xamarin.Android

::: zone pivot="windows"

La schermata seguente elenca il contenuto della soluzione. Si tratta di Esplora soluzioni, che contiene la struttura di directory e tutti i file associati alla soluzione:

[![Esplora soluzioni](hello-android-deepdive-images/vs/02-solution-structure-sml.png)](hello-android-deepdive-images/vs/02-solution-structure.png#lightbox)

::: zone-end
::: zone pivot="macos"

La schermata seguente elenca il contenuto della soluzione. Si tratta del riquadro della soluzione, che contiene la struttura di directory e tutti i file associati alla soluzione:

[![Cuscinetto della soluzione](hello-android-deepdive-images/xs/02-solution-structure-sml.png)](hello-android-deepdive-images/xs/02-solution-structure.png#lightbox)

::: zone-end

È stata creata una soluzione denominata **Phoneword** e all'interno di questa è stato inserito il progetto Android **Phoneword**.

Esaminare gli elementi all'interno del progetto per visualizzare ogni cartella e il rispettivo scopo:

- **Proprietà** &ndash; Contiene il file [AndroidManifest.xml](~/android/platform/android-manifest.md), che descrive tutti i requisiti dell'applicazione Xamarin.Android, inclusi nome, numero di versione e autorizzazioni. La cartella **Proprietà** contiene anche [AssemblyInfo.cs](xref:Microsoft.VisualBasic.ApplicationServices.AssemblyInfo), un file di metadati dell'assembly .NET. È buona norma inserire in questo file alcune informazioni di base sull'applicazione.

- **Riferimenti** &ndash; Contiene gli assembly necessari per compilare ed eseguire l'applicazione. Se si espande la directory Riferimenti, vengono visualizzati riferimenti ad assembly .NET quali [System](xref:System), System.Core e [System.Xml](xref:System.Xml), nonché un riferimento all'assembly Mono.Android di Xamarin.

- **Asset** &ndash; Contiene i file che l'applicazione deve eseguire, inclusi tipi di carattere, file di dati locali e file di testo. I file inclusi in questa cartella sono accessibili tramite la classe `Assets` generata. Per altre informazioni sugli asset Android, vedere la guida [Using Android Assets](~/android/app-fundamentals/resources-in-android/android-assets.md) (Uso degli asset Android) di Xamarin.

- **Risorse** &ndash; Contiene le risorse dell'applicazione, ad esempio stringhe, immagini e layout. È possibile accedere alle risorse all'interno del codice tramite la classe `Resource` generata. La guida [Risorse Android](~/android/app-fundamentals/resources-in-android/index.md) offre altri dettagli sulla directory **Risorse**. Il modello di applicazione include anche il file **AboutResources.txt**, una guida concisa alle risorse.

### <a name="resources"></a>Risorse

La directory **Risorse** contiene quattro cartelle denominate **drawable**, **layout**, **mipmap** e **valori**, nonché un file denominato **Resource.designer.cs**.

Gli elementi sono riepilogati nella tabella riportata di seguito:

- **drawable** &ndash; La directory drawable contiene le [risorse drawable](https://developer.android.com/guide/topics/resources/drawable-resource.html), ad esempio immagini e bitmap.

- **mipmap** &ndash; La directory mipmap contiene i file drawable per icone di avvio con densità diverse. Nel modello predefinito, la directory drawable contiene il file dell'icona dell'applicazione, **Icon.png**.

::: zone pivot="windows"

- **layout** &ndash; La directory layout contiene i _file di progettazione Android_ (con estensione axml) che definiscono l'interfaccia utente per ogni schermata o attività. Il modello crea un layout predefinito denominato **activity_main.axml**.

::: zone-end
::: zone pivot="macos"

- **layout** &ndash; La directory layout contiene i _file di progettazione Android_ (con estensione axml) che definiscono l'interfaccia utente per ogni schermata o attività. Il modello crea un layout predefinito denominato **Main.axml**.

::: zone-end

- **values** &ndash; Questa directory contiene i file XML in cui sono archiviati valori semplici, ad esempio stringhe, numeri interi e colori. Il modello crea un file per l'archiviazione di valori stringa denominato **Strings.xml**.

- **Resource.designer.cs** &ndash; Noto anche come classe `Resource`, questo file è una classe parziale contenente gli ID univoci assegnati a ogni risorsa. Questo file viene creato automaticamente dagli strumenti Xamarin.Android e viene rigenerato quando necessario. Questo file non deve essere modificato manualmente, perché Xamarin.Android sovrascrive tutte le modifiche apportate manualmente.

## <a name="app-fundamentals-and-architecture-basics"></a>Concetti fondamentali sulle app e nozioni di base sull'architettura

Le applicazioni Android non hanno un singolo punto di ingresso. In altre parole, nell'applicazione non è presente una riga di codice che viene chiamata dal sistema operativo per l'avvio dell'applicazione. Un'applicazione viene invece avviata quando Android crea un'istanza di una delle classi. Durante questa operazione Android carica in memoria il processo dell'intera applicazione.

Questa caratteristica esclusiva di Android può essere estremamente utile durante la progettazione di applicazioni complesse o durante l'interazione con il sistema operativo Android. Queste opzioni, tuttavia, rendono complesso Android anche quando si ha a che fare con uno scenario di base come l'applicazione **Phoneword**. Per questo motivo, l'esplorazione dell'architettura Android è divisa in due parti. Questa guida analizza un'applicazione che usa il punto di ingresso più comune per un'app Android: la prima schermata. In [Hello, Android Multiscreen](~/android/get-started/hello-android-multiscreen/index.md), tutte le complessità dell'architettura Android vengono esaminate man mano che vengono descritte le diverse modalità di avvio di un'applicazione.

### <a name="phoneword-scenario---starting-with-an-activity"></a>Scenario Phoneword: inizio da un'attività

Quando si apre l'applicazione **Phoneword** per la prima volta in un emulatore o in un dispositivo, il sistema operativo crea la prima *attività*. Un'attività è una classe Android speciale che corrisponde a una schermata singola dell'applicazione ed è responsabile del disegno e del funzionamento dell'interfaccia utente. Quando Android crea la prima attività di un'applicazione, carica interamente l'applicazione stessa:

[![Carico attività](hello-android-deepdive-images/01-activity-load-sml.png)](hello-android-deepdive-images/01-activity-load.png#lightbox)

Poiché non esiste una progressione lineare attraverso le applicazioni Android (che infatti possono essere avviate da diversi punti), Android tiene traccia in modo molto particolare delle classi e dei file che costituiscono un'applicazione. Nell'esempio **Phoneword**, tutte le parti che costituiscono l'applicazione vengono registrate con un file XML speciale, detto **manifesto Android**. Il ruolo del **manifesto Android** consiste nel tenere traccia del contenuto, delle proprietà e delle autorizzazioni di un'applicazione e di comunicarli al sistema operativo Android. È possibile considerare l'applicazione **Phoneword** come un'unica attività (schermata) e come una raccolta di file di risorse e di helper collegati dal file manifesto Android, come illustrato nel diagramma riportato di seguito:

[![Helper per le risorse](hello-android-deepdive-images/02-resources-helpers-sml.png)](hello-android-deepdive-images/02-resources-helpers.png#lightbox)

Le prossime sezioni analizzano le relazioni tra le diverse parti dell'applicazione **Phoneword**, consentendo una maggiore comprensione del diagramma qui sopra. L'analisi inizia con l'interfaccia utente e illustra i file di progettazione e layout di Android.

## <a name="user-interface"></a>Interfaccia utente

> [!TIP]
> Le versioni più recenti di Visual Studio supportano l'apertura dei file con estensione xml all'interno di Android Designer.
>
> I file con estensione axml e xml sono entrambi supportati in Android Designer.

::: zone pivot="windows"

**activity_main.axml** è il file di layout dell'interfaccia utente per la prima schermata nell'applicazione. L'estensione axml indica che si tratta di un file di progettazione Android (AXML è l'acronimo di *Android XML*). Il nome *Main* è arbitrario dal punto di vista di Android &ndash; il file di layout avrebbe potuto avere un nome diverso. Quando si apre **activity_main.axml** nell'IDE, viene visualizzato l'editor visivo per i file di layout Android, *Android Designer*:

[![Finestra di progettazione Android](hello-android-deepdive-images/vs/03-android-designer-sml.png "Android Designer")](hello-android-deepdive-images/vs/03-android-designer.png#lightbox)

Nell'app **Phoneword** l'ID di **TranslateButton**è impostato su `@+id/TranslateButton`:

[![Impostazione ID di TranslateButton](hello-android-deepdive-images/vs/04-translatebutton-sml.png "Impostazione ID di TranslateButton")](hello-android-deepdive-images/vs/04-translatebutton.png#lightbox)

::: zone-end
::: zone pivot="macos"

**Main.axml** è il file di layout dell'interfaccia utente per la prima schermata nell'applicazione. L'estensione axml indica che si tratta di un file di progettazione Android (AXML è l'acronimo di *Android XML*). Il nome *Main* è arbitrario dal punto di vista di Android &ndash; il file di layout avrebbe potuto avere un nome diverso. Quando si apre **Main.axml** nell'IDE, viene visualizzato l'editor visivo per i file di layout Android, *Android Designer*:

[![Finestra di progettazione Android](hello-android-deepdive-images/xs/03-android-designer-sml.png)](hello-android-deepdive-images/xs/03-android-designer.png#lightbox)

Nell'app **Phoneword** l'ID di **TranslateButton**è impostato su `@+id/TranslateButton`:

[![Impostazione dell'ID TranslateButton](hello-android-deepdive-images/xs/04-translatebutton-sml.png)](hello-android-deepdive-images/xs/04-translatebutton.png#lightbox)

::: zone-end

Quando si imposta la proprietà `id` di **TranslateButton**, Android Designer esegue il mapping del controllo **TranslateButton** alla classe `Resource` e assegna a questo l'*ID risorsa*`TranslateButton`. Il mapping del controllo visivo alla classe consente di individuare e usare **TranslateButton** e altri controlli nel codice dell'app. Questo argomento verrà trattato più dettagliatamente quando si analizzerà il codice che consente il funzionamento dei controlli. Per ora è sufficiente sapere che la rappresentazione di un controllo nel codice è collegata alla rappresentazione visiva del controllo stesso nella finestra di progettazione tramite la proprietà `id`.

### <a name="source-view"></a>Visualizzazione Origine

Tutto ciò che viene definito nell'area di progettazione viene convertito in XML per consentirne l'uso da parte di Xamarin.Android. Android Designer è dotato di una visualizzazione Origine che contiene il codice XML generato dalla finestra di progettazione visiva. È possibile visualizzare il codice XML passando al pannello **Origine** nella parte inferiore sinistra della visualizzazione di progettazione, come illustrato nello screenshot riportato di seguito:

::: zone pivot="windows"

[![Visualizzazione Origine della finestra di progettazione](hello-android-deepdive-images/vs/05-source-view-sml.png "Visualizzazione Origine della finestra di progettazione")](hello-android-deepdive-images/vs/05-source-view.png#lightbox)

::: zone-end
::: zone pivot="macos"

[![Visualizzazione origine della finestra di progettazione](hello-android-deepdive-images/xs/05-source-view-sml.png)](hello-android-deepdive-images/xs/05-source-view.png#lightbox)

::: zone-end

Questo codice sorgente XML deve contenere quattro elementi di controllo: due **TextView**s, un **EditText** e un **Button** elemento. Per una panoramica più dettagliata di Android Designer, fare riferimento alla guida[Designer Overview](~/android/user-interface/android-designer/index.md) (Panoramica della finestra di progettazione) di Xamarin Android.

La trattazione degli strumenti e dei concetti alla base della parte visiva dell'interfaccia utente è completata. È il momento di passare al codice che consente il funzionamento dell'interfaccia utente, con l'analisi delle attività e del loro ciclo di vita.

## <a name="activities-and-the-activity-lifecycle"></a>Attività e ciclo di vita delle attività

La classe `Activity` contiene il codice che consente il funzionamento dell'interfaccia utente.
L'attività ha il compito di reagire all'interazione dell'utente e di creare un'esperienza utente dinamica.
Questa sezione introduce la classe `Activity`, illustra il ciclo di vita delle attività e analizza il codice che consente il funzionamento dell'interfaccia utente nell'applicazione **Phoneword**.

### <a name="activity-class"></a>Classe dell'attività

L'applicazione **Phoneword** ha una sola schermata (attività). La classe che consente il funzionamento della schermata è denominata `MainActivity` e si trova nel file **MainActivity.cs**. Il nome `MainActivity` non ha alcun significato particolare in Android &ndash;. Anche se per convenzione alla prima attività in un'applicazione viene assegnato il nome `MainActivity`, per Android non è rilevante se il nome assegnato è diverso.

Quando si apre **MainActivity.cs**, è possibile vedere che la classe `MainActivity` è una *sottoclasse* della classe `Activity` e che l'attività è decorata con l'attributo [Activity](xref:Android.App.ActivityAttribute):

```csharp
[Activity (Label = "Phone Word", MainLauncher = true)]
public class MainActivity : Activity
{
  ...
}
```

L'attributo `Activity` registra l'attività con il manifesto Android. In questo modo Android sa che questa classe fa parte dell'applicazione **Phoneword** gestita da questo manifesto. La proprietà `Label` imposta il testo che verrà visualizzato nella parte superiore della schermata.

La proprietà `MainLauncher` chiede ad Android di visualizzare questa attività quando l'applicazione viene avviata. Questa proprietà diventa importante man mano che si aggiungono altre attività (schermate) all'applicazione, come illustrato nella guida [Hello, Android Multiscreen](~/android/get-started/hello-android-multiscreen/index.md).

Ora che le nozioni di base su `MainActivity` sono state illustrate, è il momento di approfondire la comprensione del codice delle attività con l'introduzione del _ciclo di vita delle attività_.

### <a name="activity-lifecycle"></a>Ciclo di vita dell'attività

In Android, il ciclo di vita delle attività prevede fasi diverse a seconda delle interazioni con l'utente. Le attività possono essere create, avviate e messe in pausa, riprese, eliminate definitivamente e così via. La classe `Activity` contiene metodi che vengono chiamati dal sistema in punti specifici del ciclo di vita della schermata. Il diagramma seguente illustra il ciclo di vita tipico di un'attività e alcuni dei metodi corrispondenti a determinate fasi:

[![Ciclo di vita dell'attività](hello-android-deepdive-images/04-lifecycle-sml.png)](hello-android-deepdive-images/04-lifecycle.png#lightbox)

Eseguendo l'override dei metodi del ciclo di vita di `Activity`, è possibile controllare la modalità di caricamento dell'attività, il modo in cui reagisce all'interazione dell'utente e persino che cosa accade dopo che l'attività è scomparsa dallo schermo del dispositivo. È ad esempio possibile eseguire l'override dei metodi del ciclo di vita illustrati nel diagramma precedente per eseguire alcune operazioni importanti:

- **OnCreate** &ndash; Crea le visualizzazioni, inizializza le variabili ed esegue altre operazioni di preparazione che devono essere eseguite prima che l'attività sia visibile per l'utente. Questo metodo viene chiamato solo una volta quando l'attività viene caricata in memoria.

- **OnResume** &ndash; Esegue tutte le attività che devono essere eseguite ogni volta che l'attività torna sullo schermo del dispositivo.

- **OnPause** &ndash; Esegue tutte le attività che devono essere eseguite ogni volta che l'attività lascia lo schermo del dispositivo.

Quando si aggiunge codice personalizzato a un metodo del ciclo di vita di `Activity`, si *esegue l'override* dell'*implementazione di base* del metodo in questione. Toccando all'interno del metodo del ciclo di vita esistente (che contiene già del codice) è possibile estendere il metodo stesso con codice personalizzato. L'implementazione di base deve essere chiamata dall'interno del metodo perché il codice originale venga eseguito prima del nuovo codice. Un esempio di ciò è illustrato nella prossima sezione.

Il ciclo di vita delle attività è una parte importante e complessa di Android. Per altre informazioni sulle attività, dopo aver completato la serie _introduttiva_, leggere la guida [Ciclo di vita delle attività](~/android/app-fundamentals/activity-lifecycle/index.md). In questa guida, il prossimo aspetto trattato è la prima fase del ciclo di vita delle attività, `OnCreate`.

### <a name="oncreate"></a>OnCreate

Android chiama il metodo `OnCreate` di `Activity` quando crea l'attività, prima che la schermata venga visualizzata. È possibile eseguire l'override del metodo `OnCreate` del ciclo di vita per creare visualizzazioni e preparare l'attività alla visualizzazione:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);
    // Additional setup code will go here
}
```

::: zone pivot="windows"

Nell'app **Phoneword**, la prima operazione da eseguire in `OnCreate` è il caricamento dell'interfaccia utente creata in Android Designer. Per caricare l'interfaccia utente, chiamare `SetContentView` passando il *nome layout risorsa* per il file di layout: **activity_main.axml**. Il layout si trova in `Resource.Layout.activity_main`:

```csharp
SetContentView (Resource.Layout.activity_main);
```

All'avvio, `MainActivity` crea una visualizzazione basata sul contenuto del file **activity_main.axml**.

::: zone-end
::: zone pivot="macos"

Nell'app **Phoneword**, la prima operazione da eseguire in `OnCreate` è il caricamento dell'interfaccia utente creata in Android Designer. Per caricare l'interfaccia utente, chiamare `SetContentView` passando il *nome layout risorsa* per il file di layout: **Main.axml**. Il layout si trova in `Resource.Layout.Main`:

```csharp
SetContentView (Resource.Layout.Main);
```

All'avvio, `MainActivity` crea una visualizzazione basata sul contenuto del file **Main.axml**. Si noti che il nome del file di layout deve corrispondere al nome dell'attività:  *Main*.axml è il layout per l'attività *Main*. Ciò non è obbligatorio dal punto di vista di Android. Man mano che si aggiungono schermate all'applicazione, tuttavia, si noterà che questa convenzione di denominazione rende più facile far corrispondere il file di codice al rispettivo file di layout.

::: zone-end

Dopo aver preparato il file di layout, è possibile iniziare la ricerca dei controlli.
Per cercare un controllo, chiamare `FindViewById` passando l'ID risorsa del controllo:

```csharp
EditText phoneNumberText = FindViewById<EditText>(Resource.Id.PhoneNumberText);
Button translateButton = FindViewById<Button>(Resource.Id.TranslateButton);
TextView translatedPhoneWord = FindViewById<TextView>(Resource.Id.TranslatedPhoneWord);
```

Ora che nel file di layout sono presenti riferimenti ai controlli, è possibile iniziare a programmare questi ultimi perché rispondano all'interazione dell'utente.

### <a name="responding-to-user-interaction"></a>Risposta all'interazione dell'utente

In Android, l'evento `Click` è in ascolto del tocco dell'utente. In questa app, l'evento `Click` viene gestito con un'espressione lambda, ma è anche possibile usare un delegato o un gestore dell'evento denominato. Il codice finale di **TranslateButton** è simile al seguente:

```csharp
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = string.Empty;
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
    }
};
```

## <a name="testing-deployment-and-finishing-touches"></a>Test, distribuzione e finalizzazione

Visual Studio per Mac e Visual Studio offrono entrambi numerose opzioni per il test e la distribuzione di un'applicazione. Questa sezione descrive le opzioni di debug, illustra le funzionalità per il test delle applicazioni in un dispositivo e presenta alcuni strumenti per la creazione di icone dell'app personalizzate per diverse densità dello schermo.

### <a name="debugging-tools"></a>Strumenti di debug

I problemi del codice di un'applicazione possono essere difficili da diagnosticare. Per facilitare la diagnosi di problemi complessi del codice, è possibile [impostare un punto di interruzione](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [eseguire il codice un'istruzione alla volta](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) o [visualizzare le informazioni di output nella finestra del log](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

### <a name="deploy-to-a-device"></a>Eseguire la distribuzione in un dispositivo

L'emulatore è un buon punto di partenza per la distribuzione e il test di un'applicazione, ma gli utenti non utilizzeranno l'app finale in un emulatore. È buona norma testare le applicazioni in un dispositivo reale in modo tempestivo e frequente.

Perché un dispositivo Android possa essere usato per il test di applicazioni, deve essere configurato per lo sviluppo. La guida [Configurare il dispositivo per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md) fornisce istruzioni esaurienti per la predisposizione di un dispositivo per lo sviluppo.

::: zone pivot="windows"

Dopo aver configurato il dispositivo, per usarlo come destinazione della distribuzione è necessario collegarlo e selezionarlo dalla finestra di dialogo **Seleziona dispositivo** e quindi avviare l'applicazione:

![Selezionare il dispositivo di debug](hello-android-deepdive-images/vs/06-select-device.png "Selezionare il dispositivo di debug")

::: zone-end
::: zone pivot="macos"

Dopo aver configurato il dispositivo, per usarlo come destinazione della distribuzione è necessario collegarlo, premere **Avvia (Riproduci)**, selezionarlo dalla finestra di dialogo **Seleziona dispositivo** e quindi premere **OK**:

[![Selezionare il dispositivo di debug](hello-android-deepdive-images/xs/06-select-device-sml.png)](hello-android-deepdive-images/xs/06-select-device.png#lightbox)

::: zone-end

L'applicazione verrà avviata nel dispositivo:

[![Inserisci la parola da telefono](hello-android-deepdive-images/05-enter-phoneword-sml.png)](hello-android-deepdive-images/05-enter-phoneword.png#lightbox)

### <a name="set-icons-for-different-screen-densities"></a>Impostare icone per densità dello schermo diverse

Gli schermi dei dispositivi Android possono avere schermi con dimensioni e risoluzioni diverse e non tutte le immagini vengono visualizzate in modo ottimale in tutti gli schermi. Ecco, ad esempio, lo screenshot di un'icona a bassa densità in un Nexus 5 ad alta densità. Si noti com'è sfocato rispetto alle icone circostanti:

[![Icona Sfocatura](hello-android-deepdive-images/06-blurry-icon-sml.png)](hello-android-deepdive-images/06-blurry-icon.png#lightbox)

Per evitare questo problema, è buona norma inserire nella cartella **Risorse** icone di risoluzioni diverse. Android mette a disposizione versioni diverse della cartella **mipmap** per consentire la gestione di icone di avvio di densità diverse: *mdpi* per schermi a densità media, *hdpi* per schermi a densità alta e * xhdpi*, *xxhdpi* e *xxxhdpi* per schermi a densità molto alta. Icone di dimensioni diverse vengono archiviate nelle cartelle **mipmap -** appropriate:

::: zone pivot="windows"

![Cartelle mipmap](hello-android-deepdive-images/vs/07-mipmap-folders.png "Cartelle mipmap")

::: zone-end
::: zone pivot="windows"

[![Cartelle Mipmap](hello-android-deepdive-images/xs/07-mipmap-folders-sml.png)](hello-android-deepdive-images/xs/07-mipmap-folders.png#lightbox)

::: zone-end

Android selezionerà l'icona con la densità appropriata:

[![Icone alla densità appropriata](hello-android-deepdive-images/07-appropriate-density-sml.png)](hello-android-deepdive-images/07-appropriate-density.png#lightbox)

### <a name="generate-custom-icons"></a>Generare icone personalizzate

Non tutti hanno una finestra di progettazione disponibile per creare le icone personalizzate e avviare le immagini che un'app deve distinguersi. Ecco diversi approcci alternativi per generare grafica dell'app personalizzata:

::: zone pivot="windows"

- [Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/index.html) &ndash; Un generatore interno al browser e basato sul Web per tutti i tipi di icone Android, con collegamenti ad altri strumenti utili della community. Il funzionamento ottimale si ottiene con l'uso in Google Chrome.

- Visual Studio &ndash; È possibile usare questo strumento per creare un set di icone semplici per l'app direttamente nell'IDE.

- [Fiverr](https://www.fiverr.com/) &ndash; Consente di scegliere da un'ampia gamma di progettisti per creare un set di icone personalizzato, a partire da 5 dollari. I risultati possono essere imprevedibili, ma si tratta di una risorsa valida nel caso in cui sia necessario un set di icone in tempi brevi.

::: zone-end
::: zone pivot="macos"

- [Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/index.html) &ndash; Un generatore interno al browser e basato sul Web per tutti i tipi di icone Android, con collegamenti ad altri strumenti utili della community. Il funzionamento ottimale si ottiene con l'uso in Google Chrome.

- [Pixelmator](https://www.pixelmator.com/) &ndash; Versatile app di modifica delle immagini per Mac a un costo di circa 30 dollari.

- [Fiverr](https://www.fiverr.com/) &ndash; Consente di scegliere da un'ampia gamma di progettisti per creare un set di icone personalizzato, a partire da 5 dollari. I risultati possono essere imprevedibili, ma si tratta di una risorsa valida nel caso in cui sia necessario un set di icone in tempi brevi.

::: zone-end

Per altre informazioni sulle dimensioni e sui requisiti delle icone, vedere la guida [Risorse Android](~/android/app-fundamentals/resources-in-android/index.md).

::: zone pivot="macos"

### <a name="adding-google-play-services-packages"></a>Aggiunta di pacchetti Google Play Services

_Google Play Services_ è un set di librerie di componenti aggiuntivi che consente agli sviluppatori di app Android di sfruttare le funzionalità più recenti create da Google, ad esempio Google Map, Google Cloud Messaging e la fatturazione in app.
In precedenza, le associazioni a tutte le librerie Google Play Services venivano fornite da Xamarin sotto forma di un unico pacchetto &ndash; a partire da Visual Studio per Mac, è disponibile una nuova finestra di dialogo di progetto per la selezione dei pacchetti Google Play Services da includere nell'app.

Per aggiungere una o più librerie Google Play Services, fare doppio clic sul nodo **Pacchetti** nell'albero del progetto e fare clic su **Aggiungi Google Play Services... **:

[![Aggiungi google Play Service](hello-android-deepdive-images/xs/08-add-google-play-services-sml.png)](hello-android-deepdive-images/xs/08-add-google-play-services.png#lightbox)

Quando viene visualizzata la finestra di dialogo **Aggiungi Google Play Services**, selezionare i pacchetti (NuGet) che si vogliono aggiungere al progetto:

[![Selezionare i pacchetti](hello-android-deepdive-images/xs/09-add-dialog-sml.png)](hello-android-deepdive-images/xs/09-add-dialog.png#lightbox)

Quando si seleziona un servizio e si fa clic su **Aggiungi pacchetto**, Visual Studio per Mac scarica e installa il pacchetto selezionato e tutti i pacchetti Google Play Services dipendenti richiesti. In alcuni casi, può essere visualizzata la finestra di dialogo **Accettazione della licenza**, in cui è necessario fare clic su **Accetta** per poter installare i pacchetti:

[![Accettazione della licenza](hello-android-deepdive-images/xs/10-license-acceptance-sml.png)](hello-android-deepdive-images/xs/10-license-acceptance.png#lightbox)

::: zone-end

## <a name="summary"></a>Riepilogo

Congratulazioni! A questo punto si dovrebbe avere una solida conoscenza dei componenti delle applicazioni Xamarin.Android e degli strumenti necessari per creare tali applicazioni.

Nella prossima esercitazione della serie _introduttiva_, l'applicazione verrà estesa in modo da gestire più schermate e nello stesso tempo si esamineranno concetti più avanzati dell'architettura Android.
