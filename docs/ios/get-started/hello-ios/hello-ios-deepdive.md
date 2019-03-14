---
title: Hello, iOS - Approfondimento
description: Questo documento esamina in modo dettagliato l'applicazione di esempio Hello, iOS, prendendo in considerazione l'architettura, l'interfaccia utente, la gerarchia di visualizzazione del contenuto, il test, la distribuzione e molto altro.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 61ba3a7e-fe11-4439-8bc8-9809512b8eff
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/05/2018
ms.openlocfilehash: 12fcf6ff1b289c773e0dce03be4ac62105f6acd4
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669466"
---
# <a name="hello-ios--deep-dive"></a>Hello, iOS - Approfondimento

La procedura dettagliata Guida introduttiva ha presentato la compilazione e l'esecuzione di un'applicazione Xamarin.iOS di base. È ora il momento di acquisire una conoscenza più approfondita del funzionamento delle applicazioni iOS, per poter creare programmi più complessi. Questa guida esamina i passaggi illustrati nella procedura dettagliata Hello, iOS per consentire la comprensione dei concetti fondamentali dello sviluppo di applicazioni iOS.

Questa guida consente di sviluppare le competenze e le conoscenze necessarie per creare un'applicazione iOS a schermata singola. Al termine di questa guida, si sarà in grado di comprendere le diverse parti di un'applicazione Xamarin.iOS e il modo in cui si integrano tra loro.

::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introduzione a Visual Studio per Mac

Visual Studio per Mac è un ambiente di sviluppo integrato open source gratuito che combina le funzionalità di Visual Studio e di XCode. Include una finestra di progettazione visiva completamente integrata, un editor di testo completo di strumenti di refactoring, un browser di assembly, l'integrazione del codice sorgente e altro ancora. Questa guida presenta alcune funzionalità di base di Visual Studio per Mac, ma se non si ha familiarità con questo strumento, vedere la documentazione di [Visual Studio per Mac](https://docs.microsoft.com/visualstudio/mac/).

Visual Studio per Mac segue la prassi di Visual Studio di organizzare il codice in *soluzioni* e *progetti*. Una soluzione è un contenitore per uno o più progetti. Un progetto può essere un'applicazione (ad esempio per iOS o Android), una libreria di supporto, un'applicazione di test e altro ancora. Nell'app Phoneword è stato aggiunto un nuovo progetto di iPhone tramite il modello **App visualizzazione singola**. La soluzione iniziale era simile a quanto segue:

![](hello-ios-deepdive-images/image30.png "Screenshot della soluzione iniziale")

::: zone-end
::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introduzione a Visual Studio

Visual Studio è un potente ambiente di sviluppo integrato di Microsoft. Include una finestra di progettazione visiva completamente integrata, un editor di testo completo di strumenti di refactoring, un browser di assembly, l'integrazione del codice sorgente e altro ancora. Questa guida introduce alcune funzionalità di Visual Studio di base con gli Strumenti Xamarin per Visual Studio.

Visual Studio consente di organizzare il codice in soluzioni e progetti. Una soluzione è un contenitore per uno o più progetti. Un progetto può essere un'applicazione (ad esempio per iOS o Android), una libreria di supporto, un'applicazione di test e altro ancora. Nell'app Phoneword è stato aggiunto un nuovo progetto di iPhone tramite il modello **App visualizzazione singola**. La soluzione iniziale era simile a quanto segue:

![](hello-ios-deepdive-images/vs-image30.png "Screenshot della soluzione iniziale")

::: zone-end

## <a name="anatomy-of-a-xamarinios-application"></a>Anatomia di un'applicazione Xamarin.iOS

::: zone pivot="macos"

A sinistra è presente il **Pad della soluzione**, che contiene la struttura di directory e tutti i file associati alla soluzione:

![](hello-ios-deepdive-images/image31.png "Pad della soluzione, che contiene la struttura di directory e tutti i file associati alla soluzione")

::: zone-end
::: zone pivot="windows"

A destra è presente il **riquadro della soluzione**, che contiene la struttura di directory e tutti i file associati alla soluzione:

![](hello-ios-deepdive-images/vs-image31.png "Riquadro della soluzione, che contiene la struttura di directory e tutti i file associati alla soluzione")

::: zone-end

Nella procedura dettagliata [Hello, iOS](~/ios/get-started/hello-ios/hello-ios-quickstart.md) è stata creata la soluzione **Phoneword** ed è stato inserito un progetto iOS, **Phoneword_iOS**, all'interno di questa. Gli elementi all'interno del progetto sono:

- **Riferimenti**: contiene gli assembly necessari per compilare ed eseguire l'applicazione. Espandere la directory per visualizzare riferimenti ad assembly .NET quali [System](https://docs.microsoft.com/dotnet/api/system), System.Core e [System.Xml](https://docs.microsoft.com/dotnet/api/system.xml), nonché un riferimento all'assembly Xamarin.iOS.
- **Pacchetti**: la directory Pacchetti contiene pacchetti NuGet pronti all'uso.
- **Risorse**: nella cartella Risorse sono archiviati altri file multimediali.
- **Main.cs**: contiene il punto di ingresso principale dell'applicazione. Per avviare l'applicazione, è necessario passare il nome della classe principale dell'applicazione, `AppDelegate`.
- **AppDelegate.cs**: questo file contiene la classe principale dell'applicazione ed è responsabile della creazione della finestra, della compilazione dell'interfaccia utente e dell'ascolto di eventi generati dal sistema operativo.
- **Main.storyboard**: lo Storyboard contiene la progettazione visiva dell'interfaccia utente dell'applicazione. I file dello Storyboard vengono aperti in un editor grafico, iOS Designer.
- **ViewController.cs**: il controller di visualizzazione aziona la schermata (visualizzazione) che viene visualizzata e toccata dall'utente. Il controller di visualizzazione è responsabile della gestione delle interazioni tra l'utente e la visualizzazione.
- **ViewController.designer.cs**: `designer.cs` è un file generato automaticamente che funge da elemento di associazione tra i controlli nella visualizzazione e le relative rappresentazioni in codice nel controller di visualizzazione. Poiché si tratta di un file di meccanismi interni, l'IDE sovrascriverà tutte le modifiche manuali e nella maggior parte dei casi questo file può essere ignorato. Per altre informazioni sulla relazione tra la finestra di progettazione visiva e il codice di supporto, vedere la guida [Introduction to the iOS Designer](~/ios/user-interface/designer/introduction.md) (Introduzione a iOS Designer).
- **Info.plist**: in **Info.plist** vengono impostate le proprietà dell'applicazione, ad esempio il nome, le icone, le immagini di avvio e così via. Si tratta di un file avanzato, per il quale è disponibile un'introduzione completa nella guida [Working with Property Lists](~/ios/app-fundamentals/property-lists.md) (Uso degli elenchi di proprietà).
- **Entitlements.plist**: l'elenco delle proprietà di entitlement consente di specificare le *funzionalità* dell'applicazione (dette anche tecnologie dell'App Store), ad esempio iCloud, PassKit e così via. Altre informazioni sul file **Entitlements.plist** sono disponibili nella guida [Working with Property Lists](~/ios/app-fundamentals/property-lists.md) (Uso degli elenchi di proprietà). Per un'introduzione generale agli entitlement, vedere la guida [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md).

## <a name="architecture-and-app-fundamentals"></a>Concetti fondamentali dell'architettura e delle app

Prima che un'applicazione iOS possa caricare un'interfaccia utente, è necessario eseguire due operazioni. In primo luogo, è necessario definire un*punto di ingresso* dell'applicazione, ovvero il codice da eseguire per primo quando il processo dell'applicazione viene caricato in memoria. In secondo luogo, è necessario definire una classe che gestisca gli eventi a livello dell'intera applicazione e interagisca con il sistema operativo.

Questa sezione studia le relazioni illustrate nel diagramma seguente:

[![](hello-ios-deepdive-images/image32.png "In questo diagramma sono illustrate le relazioni dei concetti fondamentali dell'architettura e delle app")](hello-ios-deepdive-images/image32.png#lightbox)

### <a name="main-method"></a>metodo Main

Il punto di ingresso principale di un'applicazione iOS è la classe `Application`. La classe `Application` è definita nel file **Main.cs** e contiene un metodo `Main` statico. Contiene una nuova istanza dell'applicazione Xamarin.iOS e passa il nome della classe *delegata dell'applicazione* che gestirà gli eventi del sistema operativo. Di seguito è riportato il codice del modello per il metodo statico `Main`:

```csharp
using System;
using UIKit;

namespace Phoneword_iOS
{
    public class Application
    {
        static void Main (string[] args)
        {
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="application-delegate"></a>Classe delegata dell'applicazione

In iOS, la classe *delegata dell'applicazione* gestisce gli eventi di sistema. Questa classe si trova all'interno di **AppDelegate.cs**. La classe `AppDelegate` gestisce la finestra (*Window*) dell'applicazione. Questa finestra è una singola istanza della classe `UIWindow` che funge da contenitore per l'interfaccia utente. Per impostazione predefinita, un'applicazione ha una sola finestra in cui caricare il contenuto e tale finestra è associata a una *schermata* (una singola istanza di `UIScreen`) che fornisce il rettangolo delimitatore corrispondente alle dimensioni dello schermo fisico del dispositivo.

*AppDelegate* è anche responsabile della sottoscrizione agli aggiornamenti di sistema relativi a eventi importanti dell'applicazione, ad esempio il completamento dell'avvio o una riduzione eccessiva della memoria disponibile.

Di seguito è riportato il codice del modello di AppDelegate:

```csharp
using System;
using Foundation;
using UIKit;

namespace Phoneword_iOS
{

    [Register ("AppDelegate")]
    public partial class AppDelegate : UIApplicationDelegate
    {
        public override UIWindow Window {
            get;
            set;
        }

        ...
    }
}
```

Dopo che l'applicazione ha definito la finestra (Window), può iniziare a caricare l'interfaccia utente. Nella prossima sezione viene illustrata la creazione dell'interfaccia utente.

## <a name="user-interface"></a>Interfaccia utente

L'interfaccia utente di un'app iOS è come una vetrina: l'applicazione ha in genere una sola vetrina (corrispondente alla finestra), ma può riempirla con tutti gli oggetti necessari. Gli oggetti e la loro disposizione possono essere modificati secondo le esigenze dell'applicazione. Gli oggetti di questo scenario, ovvero gli elementi visibili per l'utente, sono chiamati visualizzazioni. In un'applicazione con un'unica finestra, le visualizzazioni vengono disposte una sopra l'altra in una *gerarchia di visualizzazione del contenuto* gestita da un unico controller di visualizzazione. Le applicazioni con più schermate hanno più gerarchie di visualizzazione del contenuto, ognuna con un proprio controller di visualizzazione, e l'applicazione inserisce visualizzazioni nella finestra per creare gerarchie di visualizzazione del contenuto diverse in base alla schermata visualizzata dall'utente.

Questa sezione approfondisce l'argomento dell'interfaccia utente descrivendo le visualizzazioni, le gerarchie di visualizzazione del contenuto e iOS Designer.

### <a name="ios-designer-and-storyboards"></a>iOS Designer e storyboard

iOS Designer è uno strumento visivo per la creazione di interfacce utente in Xamarin. È possibile avviare iOS Designer facendo doppio clic su un qualsiasi file Storyboard (con estensione storyboard), che verrà aperto in una visualizzazione simile allo screenshot seguente:

::: zone pivot="macos"

![](hello-ios-deepdive-images/image33.png "Interfaccia di iOS Designer")

Uno *Storyboard* è un file che contiene le progettazioni visive delle schermate dell'applicazione, nonché le transizioni e le relazioni tra le schermate. La rappresentazione di una schermata di un'applicazione in uno Storyboard è chiamata _scena_. Ogni scena rappresenta un controller di visualizzazione e la pila di visualizzazioni gestite da quest'ultimo (gerarchia di visualizzazione del contenuto). Quando si crea un nuovo progetto di **applicazione visualizzazione singola** da un modello, Visual Studio per Mac genera automaticamente un file Storyboard denominato `Main.storyboard` e lo popola con un'unica scena, come illustrato nello screenshot seguente:

![](hello-ios-deepdive-images/image34.png "Visual Studio per Mac genera automaticamente un file Storyboard denominato Main.storyboard e lo popola con un'unica scena")

È possibile selezionare la barra di colore nero nella parte inferiore della schermata dello Storyboard per scegliere il controller di visualizzazione per la scena. Il controller di visualizzazione è un'istanza della classe `UIViewController` che contiene il codice sottostante per la gerarchia di visualizzazione del contenuto. Le proprietà del controller di visualizzazione possono essere visualizzate e impostate all'interno del **riquadro delle proprietà**, come illustrato nello screenshot seguente:

![](hello-ios-deepdive-images/image35.png "Riquadro Proprietà")

::: zone-end
::: zone pivot="windows"

![](hello-ios-deepdive-images/vs-image33.png "Interfaccia di iOS Designer")

Uno *Storyboard* è un file che contiene le progettazioni visive delle schermate dell'applicazione, nonché le transizioni e le relazioni tra le schermate. La rappresentazione di una schermata di un'applicazione in uno Storyboard è chiamata _scena_. Ogni scena rappresenta un controller di visualizzazione e la pila di visualizzazioni gestite da quest'ultimo (gerarchia di visualizzazione del contenuto). Quando si crea un nuovo progetto di **applicazione visualizzazione singola** da un modello, Visual Studio genera automaticamente un file Storyboard denominato `Main.storyboard` e lo popola con un'unica scena, come illustrato nello screenshot seguente:

![](hello-ios-deepdive-images/vs-image34.png "Visual Studio genera automaticamente un file Storyboard denominato Main.storyboard e lo popola con un'unica scena")

È possibile selezionare la barra nella parte inferiore della schermata dello Storyboard per scegliere il controller di visualizzazione per la scena. Il controller di visualizzazione è un'istanza della classe `UIViewController` che contiene il codice sottostante per la gerarchia di visualizzazione del contenuto. Le proprietà del controller di visualizzazione possono essere visualizzate e impostate all'interno del **riquadro Proprietà**, come illustrato nello screenshot seguente:

![](hello-ios-deepdive-images/vs-image35.png "Riquadro Proprietà")

::: zone-end

La _visualizzazione_ può essere selezionata facendo clic all'interno della parte bianca della scena. La visualizzazione è un'istanza della classe `UIView` che definisce un'area della schermata e fornisce le interfacce da usare con il contenuto in tale area. La visualizzazione predefinita corrisponde a una *visualizzazione radice* singola che riempie l'intero schermo del dispositivo.

A sinistra della scena si trova una freccia grigia con l'icona della bandierina, come illustrato nello screenshot seguente:

 [![](hello-ios-deepdive-images/image37.png "Freccia grigia con l'icona della bandierina")](hello-ios-deepdive-images/image37.png#lightbox)

La freccia grigia rappresenta una transizione dello Storyboard denominata *Segue* (si pronuncia "seg-way"). Poiché questa transizione non ha origine, viene detta *Sourceless Segue*. Un Sourceless Segue punta alla prima scena le cui visualizzazioni vengono caricate nella finestra dell'applicazione all'avvio di quest'ultima. Tale scena e le visualizzazioni all'interno di questa sono i primi elementi che l'utente vede quando l'applicazione viene caricata.

Quando si crea un'interfaccia utente, è possibile trascinare visualizzazioni aggiuntive dalla **casella degli strumenti** alla visualizzazione principale nell'area di progettazione, come illustrato nello screenshot seguente:

::: zone pivot="macos"

![](hello-ios-deepdive-images/image38.png "È possibile trascinare visualizzazioni aggiuntive dalla casella degli strumenti alla visualizzazione principale nell'area di progettazione")

::: zone-end
::: zone pivot="windows"

![](hello-ios-deepdive-images/vs-image38.png "È possibile trascinare visualizzazioni aggiuntive dalla casella degli strumenti alla visualizzazione principale nell'area di progettazione")

::: zone-end

Queste visualizzazioni aggiuntive sono dette *visualizzazioni secondarie*. La visualizzazione radice e le visualizzazioni secondarie fanno parte di una *gerarchia di visualizzazione del contenuto* gestita da `ViewController`. La struttura di tutti gli elementi presenti nella scena può essere esaminata nel riquadro **Struttura documento**:

::: zone pivot="macos"

![](hello-ios-deepdive-images/image39.png "Riquadro Struttura documento")

::: zone-end
::: zone pivot="windows"

![](hello-ios-deepdive-images/vs-image39.png "Riquadro Struttura documento")

::: zone-end

Le visualizzazioni secondarie sono evidenziate nel diagramma seguente:

::: zone pivot="macos"

![](hello-ios-deepdive-images/image40.png "Le visualizzazioni secondarie sono evidenziate nel diagramma")

::: zone-end
::: zone pivot="windows"

![](hello-ios-deepdive-images/vs-image40.png "Le visualizzazioni secondarie sono evidenziate nel diagramma")

::: zone-end

La prossima sezione analizza la gerarchia di visualizzazione del contenuto rappresentato da questa scena.

## <a name="content-view-hierarchy"></a>Gerarchia di visualizzazione del contenuto

Una _gerarchia di visualizzazione del contenuto_ è una serie di visualizzazioni e visualizzazioni secondarie disposte in pila e gestite da un unico controller di visualizzazione, come illustrato nel diagramma riportato di seguito:

 [![](hello-ios-deepdive-images/image41.png "Gerarchia di visualizzazione del contenuto")](hello-ios-deepdive-images/image41.png#lightbox)

È possibile facilitare la visualizzazione della gerarchia di visualizzazione del contenuto di `ViewController` modificando temporaneamente in giallo il colore di sfondo della visualizzazione radice nella sezione di visualizzazione del **riquadro delle proprietà**, come illustrato nello screenshot seguente:

::: zone pivot="macos"

![](hello-ios-deepdive-images/image42.png "Modifica del colore di sfondo della visualizzazione radice in giallo nella sezione di visualizzazione del riquadro delle proprietà")

::: zone-end
::: zone pivot="windows"

![](hello-ios-deepdive-images/vs-image42.png "Modifica del colore di sfondo della visualizzazione radice in giallo nella sezione di visualizzazione del riquadro delle proprietà")

::: zone-end

Il diagramma seguente illustra le relazioni tra la finestra, le visualizzazioni, le visualizzazioni secondarie e il controller di visualizzazione che visualizzano l'interfaccia utente sullo schermo del dispositivo:

[![](hello-ios-deepdive-images/image43.png "Relazioni tra la finestra, le visualizzazioni, le visualizzazioni secondarie e il controller di visualizzazione")](hello-ios-deepdive-images/image43.png#lightbox)

La prossima sezione illustra come usare le visualizzazioni nel codice e come creare un programma per l'interazione utente usando i controller di visualizzazione e il ciclo di vita della visualizzazione.

## <a name="view-controllers-and-the-view-lifecycle"></a>Controller di visualizzazione e ciclo di vita della visualizzazione

A ogni gerarchia di visualizzazione del contenuto corrisponde un controller di visualizzazione che dà vita all'interazione utente. Il ruolo del controller di visualizzazione è di gestire le visualizzazioni presenti nella gerarchia di visualizzazione del contenuto. Il controller di visualizzazione non fa parte della gerarchia di visualizzazione del contenuto e non è un elemento dell'interfaccia ma fornisce il codice che dà vita alle interazioni dell'utente con gli oggetti sullo schermo.

### <a name="view-controllers-and-storyboards"></a>Controller di visualizzazione e storyboard

::: zone pivot="macos"

All'interno di uno Storyboard il controller di visualizzazione è rappresentato sotto forma di una barra nella parte inferiore della scena. Quando si seleziona il controller di visualizzazione, vengono visualizzate le proprietà nel **riquadro delle proprietà**:

![](hello-ios-deepdive-images/image44.png "Quando si seleziona il controller di visualizzazione, vengono visualizzate le proprietà nel riquadro Proprietà")

È possibile impostare una classe controller di visualizzazione personalizzata per la gerarchia di visualizzazione del contenuto rappresentata da questa scena modificando la proprietà **Classe** nella sezione **Identità** del **riquadro delle proprietà**. L'applicazione **Phoneword**, ad esempio, imposta `ViewController` come controller di visualizzazione per la prima schermata, come illustrato nello screenshot riportato di seguito:

![](hello-ios-deepdive-images/image45new.png "L'applicazione Phoneword imposta ViewController come controller di visualizzazione")

::: zone-end
::: zone pivot="windows"

All'interno di uno Storyboard il controller di visualizzazione è rappresentato sotto forma di una barra nella parte inferiore della scena. Quando si seleziona il controller di visualizzazione, vengono visualizzate le proprietà nel **riquadro Proprietà**:

![](hello-ios-deepdive-images/vs-image44.png "Quando si seleziona il controller di visualizzazione, vengono visualizzate le proprietà nel riquadro Proprietà")

È possibile impostare una classe controller di visualizzazione personalizzata per la gerarchia di visualizzazione del contenuto rappresentata da questa scena modificando la proprietà **Classe** nella sezione **Identità** del **riquadro Proprietà**. L'applicazione **Phoneword**, ad esempio, imposta `ViewController` come controller di visualizzazione per la prima schermata, come illustrato nello screenshot riportato di seguito:

![](hello-ios-deepdive-images/vs-image45.png "L'applicazione Phoneword imposta ViewController come controller di visualizzazione")

::: zone-end

Questa operazione collega la rappresentazione del controller di visualizzazione nello Storyboard alla classe C# `ViewController`. Aprire il file `ViewController.cs`. Si noti che ViewController è una *sottoclasse* di `UIViewController`, come illustrato nel codice seguente:

```csharp
public partial class ViewController : UIViewController
{
    public ViewController (IntPtr handle) : base (handle)
    {

    }
}
```

`ViewController` ora consente il funzionamento delle interazioni della gerarchia di visualizzazione del contenuto associate a questo controller di visualizzazione nello Storyboard. Verrà quindi presentato il processo detto del ciclo di vita della visualizzazione, attraverso il quale si apprenderà il ruolo del controller di visualizzazione nella gestione delle visualizzazioni.

> [!NOTE]
> Per le schermate di sola visualizzazione che non richiedono alcuna interazione con l'utente, la proprietà **Classe** nel **riquadro delle proprietà** può essere lasciata vuota. In questo modo la classe sottostante del controller di visualizzazione viene impostata come implementazione predefinita di `UIViewController`. Ciò è corretto se non si prevede di aggiungere codice personalizzato.

### <a name="view-lifecycle"></a>Ciclo di vita della visualizzazione

Il controller di visualizzazione ha il compito di caricare e scaricare le gerarchie di visualizzazione del contenuto dalla finestra. Quando un'azione importante interessa una visualizzazione all'interno della gerarchia di visualizzazione del contenuto, il sistema operativo informa il controller di visualizzazione tramite eventi del ciclo di vita della visualizzazione. Eseguendo l'override dei metodi nel ciclo di vita della visualizzazione è possibile interagire con gli oggetti sullo schermo e creare un'interfaccia utente dinamica e reattiva.

Ecco i metodi di base del ciclo di vita e le funzioni corrispondenti:

- **ViewDidLoad**: viene chiamato *una volta* la prima volta che il controller di visualizzazione carica la propria gerarchia di visualizzazione del contenuto in memoria. Questo è un ottimo momento per eseguire la configurazione iniziale, perché è il momento in cui le visualizzazioni secondarie diventano disponibili nel codice.
- **ViewWillAppear**: viene chiamato ogni volta che una visualizzazione del controller di visualizzazione sta per essere aggiunta a una gerarchia di visualizzazione del contenuto e compare sullo schermo.
- **ViewWillDisappear**: viene chiamato ogni volta che una visualizzazione del controller di visualizzazione sta per essere rimossa da una gerarchia di visualizzazione del contenuto e scompare dallo schermo. Questo evento del ciclo di vita viene usato per la pulizia e il salvataggio dello stato.
- **ViewDidAppear** e **ViewDidDisappear**: vengono chiamati quando una visualizzazione viene rispettivamente aggiunta o rimossa dalla gerarchia di visualizzazione del contenuto.

Quando si aggiunge codice personalizzato a una qualsiasi fase del ciclo di vita, è necessario *eseguire l'override* dell'*implementazione di base* del metodo corrispondente del ciclo di vita. A questo scopo, toccare il metodo del ciclo di vita esistente, a cui è già associato del codice, e ampliare quest'ultimo con codice aggiuntivo. L'implementazione di base viene chiamata dall'interno del metodo per assicurarsi che il codice originale venga eseguito prima del nuovo codice. Un esempio di queste operazioni è illustrato nella prossima sezione.

Per altre informazioni sull'uso dei controller di visualizzazione, vedere [View Controller Programming Guide for iOS](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/index.html#//apple_ref/doc/uid/TP40007457-CH2-SW1) (Guida alla programmazione del controller di visualizzazione) e [UIViewController reference](https://developer.apple.com/documentation/uikit/uiviewcontroller?language=objc) (Informazioni di riferimento su UIViewController) nel sito Web Apple.

### <a name="responding-to-user-interaction"></a>Risposta all'interazione dell'utente

Il ruolo più importante del controller di visualizzazione è rispondere all'interazione dell'utente, ad esempio alla pressione di pulsanti, allo spostamento e così via. Il modo più semplice per gestire l'interazione dell'utente è di associare un controllo per l'ascolto dell'input dell'utente e di collegare un gestore dell'evento per rispondere all'input. È ad esempio possibile associare un pulsante per rispondere a un evento di tocco, come illustrato nell'app Phoneword.

Ora che si è acquisita una comprensione più approfondita delle visualizzazioni e dei controller di visualizzazione, è possibile esaminarne il funzionamento.
Nel progetto `Phoneword_iOS`, è stato aggiunto il pulsante `TranslateButton` alla gerarchia di visualizzazione del contenuto:

[![](hello-ios-deepdive-images/image1.png "È stato aggiunto il pulsante TranslateButton alla gerarchia di visualizzazione del contenuto")](hello-ios-deepdive-images/image1.png#lightbox)

Quando viene assegnato un **Nome** al controllo **Pulsante** nel **riquadro delle proprietà**, iOS Designer ne esegue automaticamente il mapping a un controllo in  **ViewController.designer.cs**, rendendo disponibile il pulsante `TranslateButton` all'interno della classe `ViewController`. I controlli diventano disponibili a partire dalla fase `ViewDidLoad` del ciclo di vita della visualizzazione. Per rispondere al tocco dell'utente viene quindi usato questo metodo del ciclo di vita:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // wire up TranslateButton here
}
```

Per l'ascolto del tocco dell'utente, l'app Phoneword usa un evento di tocco denominato `TouchUpInside`. `TouchUpInside` è in ascolto di eventi di tocco (sollevamento del dito dallo schermo) che seguono un contatto (contatto del dito con lo schermo) all'interno dei limiti del controllo. L'evento contrario a `TouchUpInside` è l'evento `TouchDown`, generato quando l'utente preme un controllo. L'evento `TouchDown` acquisisce molto rumore e non offre all'utente la possibilità di annullare il tocco trascinando il dito fuori dal controllo. `TouchUpInside` rappresenta il modo più comune per rispondere al tocco di un elemento **Pulsante** e crea l'esperienza che l'utente si aspetta quando preme un pulsante. Altre informazioni su questo argomenti sono disponibili in [iOS Human Interface Guidelines](https://developer.apple.com/library/ios/documentation/userexperience/conceptual/MobileHIG/index.html) (Linee guida per l'interfaccia umana iOS) nel sito Web Apple.

L'app ha gestito l'evento `TouchUpInside` con un'espressione lambda, ma avrebbe potuto usare anche un delegato o un gestore dell'evento denominato. Il codice finale del pulsante è simile al seguente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    string translatedNumber = "";

    TranslateButton.TouchUpInside += (object sender, EventArgs e) => {
      translatedNumber = Core.PhonewordTranslator.ToNumber(PhoneNumberText.Text);
      PhoneNumberText.ResignFirstResponder ();

      if (translatedNumber == "") {
        CallButton.SetTitle ("Call", UIControlState.Normal);
        CallButton.Enabled = false;
      } else {
        CallButton.SetTitle ("Call " + translatedNumber, UIControlState.Normal);
        CallButton.Enabled = true;
      }
  };
}
```

## <a name="additional-concepts-introduced-in-phoneword"></a>Altri concetti introdotti in Phoneword

L'applicazione Phoneword ha introdotto alcuni concetti non trattati in questa guida. Essi includono:

- **Modifica del testo del pulsante**: l'app Phoneword ha illustrato come modificare il testo di un **Pulsante** chiamando `SetTitle` per il **Pulsante** stesso e passando il nuovo testo e lo  **stato del controllo**del _Pulsante_. Il codice seguente, ad esempio, modifica il testo dell'elemento CallButton in "Call" (Chiama):

    ```csharp
    CallButton.SetTitle ("Call", UIControlState.Normal);
    ```

- **Abilitazione e disabilitazione dei pulsanti**: lo stato degli elementi **Pulsante** può essere `Enabled` o `Disabled`. Un **Pulsante** disabilitato non risponde all'input dell'utente. Ad esempio, il codice seguente disabilita `CallButton`:

    ```csharp
    CallButton.Enabled = false;
    ```

    Per altre informazioni sui pulsanti, vedere la guida relativa ai [pulsanti](~/ios/user-interface/controls/buttons.md).

- **Chiusura della tastiera**: quando l'utente tocca il campo di testo, iOS visualizza la tastiera per consentire all'utente di immettere input. Non esiste una funzionalità incorporata che consenta di chiudere la tastiera. Il codice seguente viene aggiunto a `TranslateButton` per nascondere la tastiera quando l'utente preme `TranslateButton`:

    ```csharp
    PhoneNumberText.ResignFirstResponder ();
    ```

    Per un altro esempio di rimozione della tastiera, fare riferimento alla sezione [Dismiss the Keyboard](https://github.com/xamarin/recipes/tree/master/Recipes/ios/input/keyboards/dismiss_the_keyboard) (Nascondere la tastiera).

- **Chiamata telefonica con URL**: nell'app Phoneword per avviare l'app telefono di sistema viene usato uno schema URL Apple. Lo schema URL personalizzato è costituito dal prefisso "tel:" e dal numero di telefono convertito, come illustrato nel codice seguente:

    ```csharp
    var url = new NSUrl ("tel:" + translatedNumber);
    if (!UIApplication.SharedApplication.OpenUrl (url))
    {
        // show alert Controller
    }
    ```

- **Visualizzazione di un avviso**: quando un utente tenta di effettuare una chiamata telefonica in un dispositivo che non supporta tali chiamate, ad esempio il simulatore o un iPod Touch, viene visualizzata una finestra di dialogo di avviso che informa l'utente che la chiamata non può essere effettuata. Il codice seguente crea e popola un controller di avvisi:

    ```csharp
    if (!UIApplication.SharedApplication.OpenUrl (url)) {
                    var alert = UIAlertController.Create ("Not supported", "Scheme 'tel:' is not supported on this device", UIAlertControllerStyle.Alert);
                    alert.AddAction (UIAlertAction.Create ("Ok", UIAlertActionStyle.Default, null));
                    PresentViewController (alert, true, null);
                }
    ```

    Per altre informazioni sulla visualizzazione di avvisi in iOS, vedere [Alert Controller recipe](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller) (Recipe controller di avvisi).

## <a name="testing-deployment-and-finishing-touches"></a>Test, distribuzione e finalizzazione

Visual Studio per Mac e Visual Studio offrono entrambi numerose opzioni per il test e la distribuzione di un'applicazione. Questa sezione descrive le opzioni di debug, illustra le funzionalità di test di applicazioni in un dispositivo e presenta alcuni strumenti per la creazione di icone e immagini di avvio dell'app personalizzate.

### <a name="debugging-tools"></a>Strumenti di debug

I problemi del codice di un'applicazione sono talvolta difficili da diagnosticare. Per facilitare la diagnosi di problemi complessi del codice, è possibile [impostare un punto di interruzione](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), [eseguire il codice un'istruzione alla volta](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) o [visualizzare le informazioni di output nella finestra del log](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

### <a name="deploy-to-a-device"></a>Eseguire la distribuzione in un dispositivo

Il simulatore iOS consente di testare le applicazioni in modo rapido. Il simulatore è dotato di numerose ottimizzazioni utili per l'esecuzione di test, ad esempio un percorso fittizio, [la simulazione di movimento](https://github.com/xamarin/recipes/tree/master/Recipes/ios/multitasking/test_location_changes_in_simulator) e così via. Gli utenti, tuttavia, non utilizzeranno l'app finale in un simulatore. È necessario testare tutte le applicazioni con dispositivi reali in modo tempestivo e frequente.

Il provisioning di un dispositivo richiede tempo e un account sviluppatore Apple. La guida [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md) offre istruzioni esaurienti per la predisposizione di un dispositivo per lo sviluppo.

> [!NOTE]
> Al momento, a causa di un requisito Apple, per compilare codice per un dispositivo o un simulatore è necessario un certificato di sviluppo o un'_identità di firma_. Per soddisfare questo requisito, seguire i passaggi nella [guida Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/manual-provisioning.md).

Dopo il provisioning del dispositivo, per usare il dispositivo stesso come destinazione della distribuzione è necessario collegarlo, modificare la destinazione sulla barra degli strumenti di compilazione per il dispositivo iOS e premere **Avvia** ( **Riproduci**) come illustrato nello screenshot seguente:

::: zone pivot="macos"

![](hello-ios-deepdive-images/image46new.png "Premere Avvia/Riproduci")

::: zone-end
::: zone pivot="windows"

![](hello-ios-deepdive-images/vs-image46.png "Premere Avvia/Riproduci")

::: zone-end

L'app verrà distribuita nel dispositivo iOS:

[![](hello-ios-deepdive-images/image1.png "L'app verrà distribuita nel dispositivo iOS ed eseguita")](hello-ios-deepdive-images/image1.png#lightbox)

### <a name="generate-custom-icons-and-launch-images"></a>Generare icone e immagini di avvio personalizzate

Non tutti hanno a disposizione un designer che crei le icone e le immagini di avvio personalizzate necessarie per mettere in risalto un'app. Ecco alcuni approcci alternativi per la generazione di grafica personalizzata per le app:

::: zone pivot="macos"

- [**Sketch**](https://www.sketchapp.com"): Sketch è un'app Mac per la progettazione di interfacce utente, icone e altro ancora. Questa è l'app con cui è stato progettato il set delle icone e delle immagini di avvio dell'app di Xamarin. Sketch 3 è disponibile nell'App Store. È anche possibile provare lo [strumento Sketch](http://bohemiancoding.com/sketch/tool/) gratuito.
- [**Pixelmator**](http://www.pixelmator.com/): versatile app di modifica delle immagini per Mac a un costo di circa 30 dollari.
- [**Glyphish**](http://www.glyphish.com/): set di icone predefinito di alta qualità disponibile per il download gratuito e l'acquisto.
- [**Fiverr**](http://www.fiverr.com/): consente di scegliere all'interno di un ampio pool un designer che crei il set di icone dell'applicazione, a partire da 5 dollari.  I risultati possono essere imprevedibili, ma si tratta di una risorsa valida nel caso in cui sia necessario un set di icone in tempi brevi

::: zone-end
::: zone pivot="windows"

* Visual Studio: è possibile usare questo strumento per creare un set di icone semplici per l'app direttamente nell'IDE.
- [**Glyphish**](http://www.glyphish.com/): set di icone predefinito di alta qualità disponibile per il download gratuito e l'acquisto.
- [**Fiverr**](http://www.fiverr.com/): consente di scegliere all'interno di un ampio pool un designer che crei il set di icone dell'applicazione, a partire da 5 dollari.  I risultati possono essere imprevedibili, ma si tratta di una risorsa valida nel caso in cui sia necessario un set di icone in tempi brevi

::: zone-end

Per altre informazioni sulle dimensioni e sui requisiti delle icone, vedere la [guida Uso delle immagini](~/ios/app-fundamentals/images-icons/index.md).

## <a name="summary"></a>Riepilogo

La procedura è stata completata. A questo punto si ha una solida conoscenza dei componenti delle applicazioni Xamarin.iOS e degli strumenti necessari per creare tali applicazioni.
Nella [prossima esercitazione della serie introduttiva](~/ios/get-started/hello-ios-multiscreen/index.md), l'applicazione verrà estesa in modo da gestire più schermate. Nello stesso tempo, verrà implementato un controller di spostamento, verranno illustrati gli Storyboard Segue e verrà presentato lo schema MVC (Model, View, Controller).

## <a name="related-links"></a>Collegamenti correlati

- [Hello, iOS (sample)](https://developer.xamarin.com/samples/monotouch/Hello_iOS/) (Hello, iOS - Esempio)
- [Linee guida dell'interfaccia umana iOS](https://developer.apple.com/design/human-interface-guidelines/ios/overview/themes/)
- [Portale di provisioning iOS](https://developer.apple.com/account/#/overview)
