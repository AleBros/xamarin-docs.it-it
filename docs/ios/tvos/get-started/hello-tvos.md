---
title: Hello, tvOS Guida introduttiva
description: Questa guida illustra la creazione della prima app Xamarin.tvOS e del relativo ciclo di vita di sviluppo. Viene inoltre introdotta la finestra di progettazione Novell, che espone i controlli dell'interfaccia utente al codice e viene illustrato come compilare, eseguire e testare un'applicazione Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: 35f0bc0668c6f80ee00b1253b16a8ac71770a9be
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030808"
---
# <a name="hello-tvos-quick-start-guide"></a>Hello, tvOS Guida introduttiva

_Questa guida illustra la creazione della prima app Xamarin.tvOS e del relativo ciclo di vita di sviluppo. Viene inoltre introdotta la finestra di progettazione Novell, che espone i controlli dell'interfaccia utente al codice e viene illustrato come compilare, eseguire e testare un'applicazione Xamarin.tvOS._

Apple ha rilasciato la quinta generazione di Apple TV, Apple TV 4K, che esegue tvOS 11.

La piattaforma Apple TV è aperta agli sviluppatori, consentendo loro di creare app immersive avanzate e di rilasciarle tramite l'App Store di Apple TV incorporata.

Se si ha familiarità con lo sviluppo di Xamarin.iOS, si dovrebbe trovare la transizione a tvOS abbastanza semplice. La maggior parte delle API e delle funzionalità sono le stesse, ma molte API comuni non sono disponibili, ad esempio WebKit. Inoltre, l'utilizzo del con Siri Remote pone alcune difficoltà di progettazione che non sono presenti nei dispositivi iOS basati su touchscreen.

Questa guida fornirà un'introduzione all'uso di tvOS in un'app Novell. Per altre informazioni su tvOS, vedere la documentazione relativa alla [preparazione per Apple TV 4K](https://developer.apple.com/tvos/) .

## <a name="overview"></a>Panoramica

Xamarin.tvOS ti permette di sviluppare app Apple TV completamente native in C# e .NET usando le stesse librerie OS X e i controlli di interfaccia usati per lo sviluppo in *Swift* (o *Objective-C*) e *Xcode*.

Inoltre, poiché le app Xamarin.tvOS sono scritte C# in e .NET, il codice back-end comune può essere condiviso con le app Xamarin.iOS, Xamarin.Android e Xamarin.Mac; tutto pur offrendo un'esperienza nativa su ogni piattaforma.

Questo articolo illustra i concetti chiave necessari per creare un'app Apple TV usando Xamarin.tvOS e Visual Studio seguendo il processo di creazione di un'app **Hello, tvOS** di base che conta il numero di volte in cui è stato fatto clic su un pulsante:

[![](hello-tvos-images/run05.png "Example app run")](hello-tvos-images/run05.png#lightbox)

Verranno illustrati i concetti seguenti:

- **Visual Studio per Mac** : introduzione al Visual Studio per Mac e alla procedura di creazione di applicazioni Xamarin.tvOS.
- **Anatomia di un'app Xamarin.tvOS** : che cos'è costituito da un'app Xamarin.tvOS.
- **Creazione di un'interfaccia utente** : come usare per Xamarin designer per iOS per creare un'interfaccia utente.
- **Distribuzione e test** : come eseguire e testare l'app nel simulatore tvOS e su hardware tvOS reale.

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>Avvio di una nuova app Xamarin.tvOS in Visual Studio per Mac

Come indicato in precedenza, verrà creata un'app Apple TV denominata `Hello-tvOS` che aggiunge un singolo pulsante e un'etichetta alla schermata principale. Quando viene fatto clic sul pulsante, l'etichetta visualizza il numero di volte in cui è stato fatto clic.

Per iniziare, eseguire le operazioni seguenti:

1. Avviare Visual Studio per Mac:

    [![](hello-tvos-images/setup01.png "Visual Studio for Mac")](hello-tvos-images/setup01.png#lightbox)
2. Fare clic sul collegamento **nuova soluzione** nell'angolo superiore sinistro della schermata per aprire la finestra di dialogo **nuovo progetto** .
3. Selezionare **tvOS** > **app** > **app visualizzazione singola** e fare clic sul pulsante **Avanti** :

    [![](hello-tvos-images/setup02.png "Select Single View App")](hello-tvos-images/setup02.png#lightbox)
4. Immettere `Hello, tvOS` per il **nome dell'app**, immettere l' **identificatore dell'organizzazione** e fare clic sul pulsante **Avanti** :

    [![](hello-tvos-images/setup04.png "Enter Hello, tvOS")](hello-tvos-images/setup04.png#lightbox)
5. Immettere `Hello_tvOS` per il **nome del progetto** e fare clic sul pulsante **Crea** :

    [![](hello-tvos-images/setup03.png "Enter HellotvOS")](hello-tvos-images/setup03.png#lightbox)

Visual Studio per Mac creerà la nuova app Xamarin.tvOS e visualizzerà i file predefiniti che vengono aggiunti alla soluzione dell'applicazione:

 [![](hello-tvos-images/project01.png "The default files view")](hello-tvos-images/project01.png#lightbox)

Visual Studio per Mac usa **soluzioni** e **progetti**esattamente come avviene in Visual Studio. Una soluzione è un contenitore che può ospitare uno o più progetti; i progetti possono includere applicazioni, librerie di supporto, applicazioni di test e così via. In questo caso, Visual Studio per Mac ha creato automaticamente una soluzione e un progetto di applicazione.

Se lo si desidera, è possibile creare uno o più progetti della libreria di codice che contengono codice condiviso comune. Questi progetti di libreria possono essere usati dal progetto dell'applicazione o condivisi con altri progetti di app Xamarin.tvOS (o Xamarin.iOS, Xamarin.Android e Xamarin.Mac in base al tipo di codice), proprio come se si creasse un'applicazione .NET standard.

## <a name="anatomy-of-a-xamarintvos-app"></a>Anatomia di un'app Xamarin.tvOS

Se si ha familiarità con la programmazione iOS, si noterà una grande quantità di similitudini. In realtà, tvOS 9 è un subset di iOS 9, quindi numerosi concetti si troveranno in questo argomento.

Verranno ora esaminati i file del progetto:

- `Main.cs`: contiene il punto di ingresso principale dell'app. All'avvio l'app contiene la prima classe e il primo metodo eseguiti.
- `AppDelegate.cs`: questo file contiene la classe principale dell'applicazione che è responsabile dell'ascolto degli eventi dal sistema operativo.
- `Info.plist`: questo file contiene le proprietà dell'applicazione, ad esempio il nome dell'applicazione, le icone e così via.
- `ViewController.cs`: questa è la classe che rappresenta la finestra principale e ne controlla il ciclo di vita.
- `ViewController.designer.cs`: questo file contiene codice plumbing che consente di eseguire l'integrazione con l'interfaccia utente della schermata principale.
- `Main.storyboard`: interfaccia utente per la finestra principale. Questo file può essere creato e gestito dal Xamarin Designer per iOS.

Nelle sezioni seguenti verranno esaminati rapidamente alcuni di questi file. Verranno esaminati in modo più dettagliato in un secondo momento, ma è consigliabile comprenderne le nozioni di base.

### <a name="maincs"></a>Main.cs

Il file di `Main.cs` contiene un metodo di `Main` statico che crea una nuova istanza dell'app Xamarin.tvOS e passa il nome della classe che gestirà gli eventi del sistema operativo, che in questo caso è la classe `AppDelegate`:

```csharp
using UIKit;

namespace Hello_tvOS
{
    public class Application
    {
        // This is the main entry point of the application.
        static void Main (string[] args)
        {
            // if you want to use a different Application Delegate class from "AppDelegate"
            // you can specify it here.
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="appdelegatecs"></a>AppDelegate.cs

Il file di `AppDelegate.cs` contiene la classe `AppDelegate`, che è responsabile della creazione della finestra e dell'ascolto degli eventi del sistema operativo:

```csharp
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    // The UIApplicationDelegate for the application. This class is responsible for launching the
    // User Interface of the application, as well as listening (and optionally responding) to application events from iOS.
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        // class-level declarations

        public override UIWindow Window {
            get;
            set;
        }

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Override point for customization after application launch.
            // If not required for your application you can safely delete this method

            return true;
        }

        public override void OnResignActivation (UIApplication application)
        {
            // Invoked when the application is about to move from active to inactive state.
            // This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message)
            // or when the user quits the application and it begins the transition to the background state.
            // Games should use this method to pause the game.
        }

        public override void DidEnterBackground (UIApplication application)
        {
            // Use this method to release shared resources, save user data, invalidate timers and store the application state.
            // If your application supports background execution this method is called instead of WillTerminate when the user quits.
        }

        public override void WillEnterForeground (UIApplication application)
        {
            // Called as part of the transition from background to active state.
            // Here you can undo many of the changes made on entering the background.
        }

        public override void OnActivated (UIApplication application)
        {
            // Restart any tasks that were paused (or not yet started) while the application was inactive.
            // If the application was previously in the background, optionally refresh the user interface.
        }

        public override void WillTerminate (UIApplication application)
        {
            // Called when the application is about to terminate. Save data, if needed. See also DidEnterBackground.
        }
    }
}
```

Questo codice probabilmente non è noto a meno che non sia stata compilata un'applicazione iOS prima, ma è piuttosto semplice. Esaminiamo le linee importanti.

Per prima cosa, esaminiamo la dichiarazione di variabile a livello di classe:

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

La proprietà `Window` fornisce accesso alla finestra principale. tvOS usa quello noto come modello MVC ( *Model View Controller* ). In genere, per ogni finestra creata, e per molti altri elementi all'interno di Windows, è disponibile un controller che è responsabile del ciclo di vita della finestra, ad esempio la visualizzazione, l'aggiunta di nuove visualizzazioni (controlli) e così via.

Quindi, abbiamo il metodo `FinishedLaunching`. Questo metodo viene eseguito dopo che è stata creata un'istanza dell'applicazione ed è responsabile della creazione effettiva della finestra dell'applicazione e dell'inizio del processo di visualizzazione della visualizzazione. Poiché l'app usa uno storyboard per definire l'interfaccia utente, non è necessario alcun codice aggiuntivo.

Nel modello sono disponibili molti altri metodi, ad esempio `DidEnterBackground` e `WillEnterForeground`. Questi possono essere rimossi in modo sicuro se gli eventi dell'applicazione non vengono usati nell'app.

### <a name="viewcontrollercs"></a>ViewController.cs

La classe `ViewController` è il controller della finestra principale. Ciò significa che è responsabile del ciclo di vita della finestra principale. Questo aspetto verrà esaminato in dettaglio in un secondo momento, per ora esaminiamo brevemente:

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
    }
}
```

#### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

Il file di progettazione per la classe della finestra principale è attualmente vuoto, ma verrà popolato automaticamente da Visual Studio per Mac durante la creazione dell'interfaccia utente con iOS designer:

```csharp
using Foundation;

namespace HellotvOS
{
    [Register ("ViewController")]
    partial class ViewController
    {
        void ReleaseDesignerOutlets ()
        {
        }
    }
}
```

In genere i file della finestra di progettazione non sono interessati, perché sono gestiti automaticamente da Visual Studio per Mac e forniscono solo il codice plumbing necessario che consente l'accesso ai controlli aggiunti a qualsiasi finestra o visualizzazione dell'applicazione.

Ora che è stata creata l'app Xamarin.tvOS e abbiamo una conoscenza di base dei relativi componenti, esaminiamo la creazione dell'interfaccia utente.

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>Creazione dell'interfaccia utente

Non è necessario usare Xamarin Designer per iOS per creare l'interfaccia utente per l'app Xamarin.tvOS, l'interfaccia utente può essere creata direttamente dal C# codice ma che esula dall'ambito di questo articolo. Per semplicità, verrà usato iOS designer per creare l'interfaccia utente durante la restante parte di questa esercitazione.

Per avviare la creazione dell'interfaccia utente, fare doppio clic sul file `Main.storyboard` nel **Esplora soluzioni** per aprirlo per la modifica in iOS designer:

[![](hello-tvos-images/designer01.png "The Main.storyboard file in the Solution Explorer")](hello-tvos-images/designer01.png#lightbox)

Verrà avviata la finestra di progettazione e avrà un aspetto simile al seguente:

[![](hello-tvos-images/designer02.png "The Designer")](hello-tvos-images/designer02.png#lightbox)

Per altre informazioni su iOS designer e sul relativo funzionamento, vedere Introduzione alla guida [Xamarin designer per iOS](~/ios/user-interface/designer/introduction.md) .

È ora possibile iniziare ad aggiungere controlli all'area di progettazione dell'app Xamarin.tvOS.

Procedere come descritto di seguito:

1. Individuare la **casella degli strumenti**, che dovrebbe essere a destra dell'area di progettazione:

    [![](hello-tvos-images/designer03.png "The Toolbox")](hello-tvos-images/designer03.png#lightbox)

    Se non è possibile individuarlo, passare a **visualizza > rilievi > casella degli strumenti** per visualizzarlo.
2. Trascinare un' **etichetta** dalla **casella degli strumenti** nell'area di progettazione:

    [![](hello-tvos-images/designer04.png "Drag a Label from the Toolbox")](hello-tvos-images/designer04.png#lightbox)
3. Fare clic sulla proprietà **title** nel **riquadro delle proprietà** e modificare il titolo del pulsante in `Hello, tvOS` e impostare le **dimensioni del carattere** su 128:

    [![](hello-tvos-images/designer05.png "Set the title to Hello, tvOS and set the Font Size to 128")](hello-tvos-images/designer05.png#lightbox)
4. Ridimensionare l'etichetta in modo che tutte le parole siano visibili e posizionarla al centro nella parte superiore della finestra:

    [![](hello-tvos-images/designer06.png "Resize and center the label")](hello-tvos-images/designer06.png#lightbox)
5. L'etichetta deve ora essere vincolata alla posizione, in modo che venga visualizzata come previsto. indipendentemente dalle dimensioni dello schermo. A tale scopo, fare clic sull'etichetta fino a quando non viene visualizzato l' *handle a forma di T* :

    [![](hello-tvos-images/designer07.png "The T-shaped handle")](hello-tvos-images/designer07.png#lightbox)
6. Per vincolare l'etichetta orizzontalmente, selezionare il quadrato centrale e trascinarlo sulla linea tratteggiata verticale:

    [![](hello-tvos-images/designer08.png "Select the center square")](hello-tvos-images/designer08zoom.png#lightbox)

     L'etichetta dovrebbe diventare arancione.
7. Selezionare il punto di manipolazione T nella parte superiore dell'etichetta e trascinarlo sul bordo superiore della finestra:

    [![](hello-tvos-images/designer09.png "Drag the handle to the top edge of the window")](hello-tvos-images/designer09.png#lightbox)
8. Quindi, fare clic sulla larghezza e quindi sul punto di controllo *dell'altezza,* come illustrato di seguito:

    [![](hello-tvos-images/designer10.png "The width and the height bone handles")](hello-tvos-images/designer10.png#lightbox)

     Quando si fa clic su ogni *handle di osso* , selezionare rispettivamente larghezza e altezza per impostare le dimensioni fisse.
9. Al termine, i vincoli dovrebbero essere simili a quelli nella scheda layout del riquadro delle proprietà:

    [![](hello-tvos-images/designer11.png "Example Constraints")](hello-tvos-images/designer11.png#lightbox)
10. Trascinare un **pulsante** dalla **casella degli strumenti** e posizionarlo sotto l'etichetta.
11. Fare clic sulla proprietà **title** nel **riquadro delle proprietà** e modificare il titolo del pulsante in `Click Me`:

    [![](hello-tvos-images/designer12.png "Change the buttons title to Click Me")](hello-tvos-images/designer12.png#lightbox)
12. Ripetere i passaggi da 5 a 8 sopra per vincolare il pulsante nella finestra tvOS. Tuttavia, invece di trascinare l'handle T nella parte superiore della finestra (come nel passaggio #7), trascinarlo nella parte inferiore dell'etichetta:

    [![](hello-tvos-images/designer14.png "Constrain the button")](hello-tvos-images/designer14.png#lightbox)
13. Trascinare un'altra etichetta sotto il pulsante, ridimensionarla in modo che abbia la stessa larghezza della prima etichetta e impostarne l' **allineamento** su **Center**:

    [![](hello-tvos-images/designer15.png "Drag another label under the button, size it to be the same width as the first label and set its Alignment to Center")](hello-tvos-images/designer15.png#lightbox)
14. Come la prima etichetta e il pulsante, impostare questa etichetta su centrata e aggiungerla a posizione e dimensioni:

    [![](hello-tvos-images/designer16.png "Pin the label into location and size")](hello-tvos-images/designer16.png#lightbox)
15. Salvare le modifiche apportate all'interfaccia utente.

Quando si ridimensionano e si spostano i controlli, si noterà che la finestra di progettazione offre suggerimenti di snap utili basati sulle [linee guida dell'interfaccia umana di Apple TV](https://developer.apple.com/tvos/human-interface-guidelines/). Queste linee guida consentono di creare applicazioni di alta qualità che avranno un aspetto familiare per gli utenti di Apple TV.

Se si osserva la sezione **struttura documento** , si noti come vengono visualizzati il layout e la gerarchia degli elementi che costituiscono l'interfaccia utente:

[![](hello-tvos-images/designer17.png "The Document Outline section")](hello-tvos-images/designer17.png#lightbox)

Da qui è possibile selezionare gli elementi da modificare o trascinare per riordinare gli elementi dell'interfaccia utente, se necessario. Se, ad esempio, un elemento dell'interfaccia utente è stato coperto da un altro elemento, è possibile trascinarlo nella parte inferiore dell'elenco per impostarlo come elemento in primo piano nella finestra.

Ora che è stata creata l'interfaccia utente, è necessario esporre gli elementi dell'interfaccia utente in modo che Xamarin.tvOS possa accedere e interagire con C# essi nel codice.

### <a name="accessing-the-controls-in-the-code-behind"></a>Accesso ai controlli nel code-behind

Esistono due modi principali per accedere ai controlli aggiunti in iOS designer dal codice:

- Creazione di un gestore eventi su un controllo.
- Assegnare un nome al controllo, in modo che sia possibile farvi riferimento in un secondo momento.

Quando uno di questi viene aggiunto, la classe parziale all'interno del `ViewController.designer.cs` verrà aggiornata per riflettere le modifiche. Ciò consentirà di accedere ai controlli nel controller di visualizzazione.

### <a name="creating-an-event-handler"></a>Creazione di un gestore eventi

In questa applicazione di esempio, quando si fa clic sul pulsante si vuole che venga eseguita un' _operazione_ , è necessario aggiungere un gestore eventi a un evento specifico sul pulsante. Per configurare questa impostazione, eseguire le operazioni seguenti:

1. In Novell iOS Designer selezionare il pulsante sul controller di visualizzazione.
2. Nel riquadro delle proprietà selezionare la scheda **eventi** :

    [![](hello-tvos-images/event1.png "The Events tab")](hello-tvos-images/event1.png#lightbox)
3. Individuare l'evento TouchUpInside e assegnargli un gestore eventi denominato `Clicked`:

    [![](hello-tvos-images/event2.png "The TouchUpInside event")](hello-tvos-images/event2.png#lightbox)
4. Quando si preme **invio**, viene aperto il file **ViewController**. cs, che suggerisce i percorsi per il gestore eventi nel codice. Usare i tasti di direzione sulla tastiera per impostare il percorso:

    [![](hello-tvos-images/event3.png "Setting the location")](hello-tvos-images/event3.png#lightbox)
5. Verrà creato un metodo parziale, come illustrato di seguito:

    [![](hello-tvos-images/event4.png "The partial method")](hello-tvos-images/event4.png#lightbox)

A questo punto è possibile iniziare ad aggiungere codice per consentire il funzionamento del pulsante.

### <a name="naming-a-control"></a>Assegnazione di un nome a un controllo

Quando si fa clic sul pulsante, l'etichetta deve essere aggiornata in base al numero di clic. A tale scopo, è necessario accedere all'etichetta nel codice. Questa operazione viene eseguita assegnando un nome. Procedere come descritto di seguito:

1. Aprire lo storyboard e selezionare l'etichetta nella parte inferiore del controller di visualizzazione.
2. Nel riquadro delle proprietà selezionare la scheda **widget** :

    [![](hello-tvos-images/name1.png "Select the Widget tab")](hello-tvos-images/name1.png#lightbox)
3. In **nome > identità**aggiungere `ClickedLabel`:

    [![](hello-tvos-images/name2.png "Set ClickedLabel")](hello-tvos-images/name2.png#lightbox)

A questo punto è possibile iniziare ad aggiornare l'etichetta.

### <a name="how-controls-are-accessed"></a>Modalità di accesso ai controlli

Se si seleziona il `ViewController.designer.cs` nel **Esplora soluzioni** , sarà possibile vedere come è stato eseguito il mapping dell'etichetta `ClickedLabel` e del gestore eventi `Clicked` a un **Outlet** e un' **azione** in C#:

[![](hello-tvos-images/accesscontrol.png "Outlets and Actions")](hello-tvos-images/accesscontrol.png#lightbox)

È anche possibile notare che `ViewController.designer.cs` è una classe parziale, in modo che Visual Studio per Mac non debba modificare `ViewController.cs` che sovrascriverebbe le modifiche apportate alla classe.

Esponendo gli elementi dell'interfaccia utente in questo modo, è possibile accedervi nel controller di visualizzazione.

Normalmente non sarà mai necessario aprire il `ViewController.designer.cs`, ma è stato presentato solo a scopo didattico.

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>Scrittura del codice

Con la creazione dell'interfaccia utente e degli elementi dell'interfaccia utente esposti al codice tramite **Outlet** e **azioni**, è possibile scrivere il codice per fornire la funzionalità del programma.

Nell'applicazione, ogni volta che si fa clic sul primo pulsante, l'etichetta verrà aggiornata per mostrare il numero di volte in cui è stato fatto clic sul pulsante. A tale scopo, è necessario aprire il file di `ViewController.cs` per la modifica facendo doppio clic su di esso nel **riquadro della soluzione**:

[![](hello-tvos-images/code01.png "The Solution Pad")](hello-tvos-images/code01.png#lightbox)

Prima di tutto, è necessario creare una variabile a livello di classe nella classe `ViewController` per tenere traccia del numero di clic che si sono verificati. Modificare la definizione della classe nel modo seguente:

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

Successivamente, nella stessa classe (`ViewController`), è necessario eseguire l'override del metodo **ViewDidLoad** e aggiungere il codice per impostare il messaggio iniziale per l'etichetta:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

È necessario usare `ViewDidLoad`, anziché un altro metodo, ad esempio `Initialize`, perché `ViewDidLoad` viene chiamato *dopo che* il sistema operativo ha caricato e creato un'istanza dell'interfaccia utente dal file `.storyboard`. Se si è tentato di accedere al controllo Label prima che il file `.storyboard` sia stato completamente caricato e ne venga creata un'istanza, verrà generato un errore `NullReferenceException` perché il controllo Label non verrà ancora creato.

Successivamente, è necessario aggiungere il codice per rispondere all'utente facendo clic sul pulsante. Aggiungere quanto segue alla classe parziale in cui è stato creato:

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

Questo codice verrà chiamato ogni volta che l'utente fa clic sul pulsante.

Con tutti gli elementi, è ora possibile compilare e testare l'applicazione Xamarin.tvOS.

## <a name="testing-the-application"></a>Verifica dell'applicazione

È il momento di compilare ed eseguire l'applicazione per assicurarsi che venga eseguita come previsto. Possiamo compilare ed eseguire tutto in un unico passaggio oppure è possibile compilarlo senza eseguirlo.

Ogni volta che si compila un'applicazione, è possibile scegliere il tipo di compilazione desiderato:

- **Debug** : una compilazione di debug viene compilata in un file '' (applicazione) con metadati aggiuntivi che consentono di eseguire il debug di ciò che accade durante l'esecuzione dell'applicazione.
- **Versione** : una build di rilascio crea anche un file '', ma non include informazioni di debug, quindi è più piccolo ed eseguito più velocemente.  

È possibile selezionare il tipo di compilazione dal **selettore configurazione** nell'angolo superiore sinistro della schermata Visual Studio per Mac:

[![](hello-tvos-images/run01.png "Select the type of build")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>Compilazione dell'applicazione

In questo caso, è sufficiente una build di debug, quindi è necessario assicurarsi che sia selezionata l'opzione **debug** . Per prima cosa, compilare l'applicazione premendo **⌘ B**oppure scegliendo **Compila tutto**dal menu **Compila** .

Se non sono stati riscontrati errori, nella barra di stato di Visual Studio per Mac verrà visualizzato un messaggio di **compilazione completata** . In caso di errori, esaminare il progetto e assicurarsi di aver seguito correttamente i passaggi. Per iniziare, verificare che il codice (sia in Xcode che in Visual Studio per Mac) corrisponda al codice nell'esercitazione.

### <a name="running-the-application"></a>Esecuzione dell'applicazione

Per eseguire l'applicazione, sono disponibili tre opzioni:

- Premere **⌘+INVIO**.
- Scegliere **Debug** dal menu **Esegui**.
- Fare clic sul pulsante **Esegui** nella barra degli strumenti di Visual Studio per Mac (sopra **Esplora soluzioni**).

L'applicazione verrà compilata (se non è già stata compilata), avviata in modalità di debug, il simulatore tvOS verrà avviato e l'app verrà avviata e visualizzata la finestra principale dell'interfaccia:

[![la schermata iniziale dell'app di esempio](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

Dal menu **hardware** selezionare **Mostra Apple TV Remote** per poter controllare il simulatore.

[![](hello-tvos-images/run04.png "Select Show Apple TV Remote")](hello-tvos-images/run04.png#lightbox)

Usando il simulatore remoto, se si fa clic sul pulsante alcune volte l'etichetta deve essere aggiornata con il conteggio:

[![](hello-tvos-images/run05.png "The label with updated count")](hello-tvos-images/run05.png#lightbox)

La procedura è stata completata. In questo articolo è stata illustrata una grande quantità di informazioni, ma se è stata seguita questa esercitazione dall'inizio alla fine, è ora necessario conoscere in modo approfondito i componenti di un'app Xamarin.tvOS, oltre agli strumenti usati per crearli.

## <a name="where-to-next"></a>Posizione successiva

Lo sviluppo di app Apple TV presenta alcune complicazioni a causa della disconnessione tra l'utente e l'interfaccia (la stanza non è presente nell'utente) e le limitazioni tvOS posizionate sulle dimensioni e l'archiviazione delle app.

Di conseguenza, è consigliabile leggere i documenti seguenti prima di passare alla progettazione di un'app Xamarin.tvOS:

- [Introduzione a tvOS 9](~/ios/tvos/platform/tvos9.md) : questo articolo presenta tutte le API nuove e modificate e le funzionalità disponibili in tvOS 9 per gli sviluppatori Xamarin.tvOS.
- [Lavorare con lo spostamento e lo stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) : gli utenti dell'app Xamarin.tvOS non interagiranno con l'interfaccia direttamente come per iOS, dove toccano immagini sullo schermo del dispositivo, ma indirettamente da tutta la stanza usando Siri remote. Questo articolo illustra il concetto di messa a fuoco e il modo in cui viene usato per gestire la navigazione nell'interfaccia utente di un'app Xamarin.tvOS.
- I [controller Siri remoto e Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) : la modalità principale con cui gli utenti interagiranno con Apple TV e con l'app Xamarin.tvOS, è tramite l'inclusivo Siri remote. Se l'app è un gioco, è possibile compilare facoltativamente il supporto per i controller di gioco Bluetooth di terze parti, creati per iOS (IFM) nell'app. Questo articolo illustra il supporto dei nuovi controller di gioco Siri remote e Bluetooth nelle app Xamarin.tvOS.
- [Risorse e archiviazione dei dati](~/ios/tvos/app-fundamentals/resources-data-storage.md) : diversamente dai dispositivi iOS, il nuovo Apple TV non fornisce archiviazione locale persistente per le app tvOS. Di conseguenza, se l'app Xamarin.tvOS deve salvare in modo permanente le informazioni, ad esempio le preferenze dell'utente, deve archiviare e recuperare i dati da iCloud. Questo articolo illustra l'uso delle risorse e dell'archiviazione dei dati permanenti in un'app Xamarin.tvOS.
- [Uso di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md) : la creazione di icone e immagini accattivanti è una parte essenziale dello sviluppo di un'esperienza utente immersiva per le app di Apple TV. Questa guida illustra i passaggi necessari per creare e includere le risorse grafiche necessarie per le app Xamarin.tvOS.
- [Interfaccia utente](~/ios/tvos/user-interface/index.md) : copertura dell'esperienza utente generale, inclusi i controlli dell'interfaccia utente, usare i principi di progettazione di Interface Builder e UX di Xcode quando si lavora con Xamarin.tvOS.
- [Distribuzione e test](~/ios/tvos/deploy-test/index.md) : questa sezione descrive gli argomenti usati per testare un'app e come distribuirla. Gli argomenti seguenti includono elementi come gli strumenti usati per il debug, la distribuzione ai tester e la pubblicazione di un'applicazione nell'App Store di Apple TV.

Se si verificano problemi durante l'uso di Xamarin.tvOS, vedere la documentazione relativa alla [risoluzione dei](~/ios/tvos/troubleshooting.md) problemi per un elenco dei problemi e delle soluzioni.

## <a name="summary"></a>Riepilogo

Questo articolo fornisce una guida introduttiva allo sviluppo di app per tvOS con Visual Studio per Mac creando una semplice app Hello, tvOS. Sono state trattate le nozioni di base del provisioning dei dispositivi tvOS, della creazione di interfacce, della codifica per tvOS e dei test nel simulatore tvOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Creazione di app per tvOS con Novell (video)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
