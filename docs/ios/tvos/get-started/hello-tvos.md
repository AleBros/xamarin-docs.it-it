---
title: App tvos Guida introduttiva
description: Questa guida descrive come creare la prima app xamarin. tvos e la toolchain di sviluppo. Oltre a introdurre la finestra di progettazione di Xamarin, che espone a controlli dell'interfaccia utente al codice e viene illustrato come compilare, eseguire e testare un'applicazione xamarin. tvos.
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 02/02/2018
ms.openlocfilehash: 859bbd22640ba3d09324fcd3853cda26e563a1cd
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865285"
---
# <a name="hello-tvos-quick-start-guide"></a>App tvos Guida introduttiva

_Questa guida descrive come creare la prima app xamarin. tvos e la toolchain di sviluppo. Oltre a introdurre la finestra di progettazione di Xamarin, che espone a controlli dell'interfaccia utente al codice e viene illustrato come compilare, eseguire e testare un'applicazione xamarin. tvos._

Apple ha rilasciato il 5 ° generazione Apple TV Apple TV 4K, che viene eseguito tvOS 11.

La piattaforma di Apple TV è aperta agli sviluppatori, consentendo loro di creare App complete e coinvolgenti e rilasciarle attraverso la Store di App predefinite di un Apple TV.

Se si ha familiarità con lo sviluppo di xamarin. IOS, è necessario trovare la transizione a tvOS piuttosto semplice. La maggior parte delle funzionalità e le API sono le stesse, tuttavia, molte API comuni non sono disponibili (ad esempio WebKit). Inoltre collaborando con i con il remoti per Siri pone alcune problematiche di progettazione che non sono presenti nei dispositivi iOS touchscreen basato.

Questa guida offre un'introduzione all'uso di tvOS in un'app Xamarin. Per altre informazioni su tvOS, vedi di Apple [prepararsi per Apple TV 4K](https://developer.apple.com/tvos/) documentazione.

## <a name="overview"></a>Panoramica

Xamarin. tvos consente di sviluppare App completamente native per Apple TV in C# e .NET usando le stesse librerie di OS X e i controlli dell'interfaccia che vengono usati quando l'ambiente di sviluppo *Swift* (oppure *Objective-C*) e  *Xcode*.

Inoltre, poiché xamarin. tvos le app scritte in C# e il codice back-end comune, .NET, può essere condivisa con App xamarin. IOS, xamarin. Android e xamarin. Mac. offrendo un'esperienza nativa su ogni piattaforma.

Questo articolo presenta i concetti principali necessari per creare un'App per Apple TV con Visual Studio e xamarin. tvos attraverso il processo di compilazione di base **App Tvos** app che conta il numero di volte in cui ha un pulsante stato fatto clic:

[![](hello-tvos-images/run05.png "Esecuzione di app di esempio")](hello-tvos-images/run05.png#lightbox)

Trattati i concetti seguenti:

-  **Visual Studio per Mac** – Introduzione a Visual Studio per Mac e su come creare applicazioni xamarin. tvos con esso.
-  **Anatomia di un'App Xamarin.tvOS** – app tvos una cosa è costituito.
-  **Creazione di un'interfaccia utente** – come usare alla finestra di progettazione di Xamarin per iOS per creare un'interfaccia utente.
-  **Distribuzione e test** : come eseguire e testare l'app nel simulatore di tvOS e sull'hardware reale tvOS.

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>Creazione di una nuova App Xamarin.tvOS in Visual Studio per Mac

Come indicato in precedenza, si creerà un'app per Apple TV chiamata `Hello-tvOS` che aggiunge un singolo pulsante e un'etichetta alla schermata principale. Quando viene fatto clic sul pulsante, l'etichetta visualizza il numero di volte in cui è stato fatto clic.

Per iniziare, è possibile eseguire le operazioni seguenti:

1. Avviare Visual Studio per Mac:

    [![](hello-tvos-images/setup01.png "Visual Studio per Mac")](hello-tvos-images/setup01.png#lightbox)
2. Fare clic su di **nuova soluzione...**  collegamento nell'angolo superiore sinistro della schermata per aprire la **nuovo progetto** nella finestra di dialogo.
3. Selezionare **tvOS** > **App** > **App visualizzazione singola** e fare clic su di **successivo** pulsante:

    [![](hello-tvos-images/setup02.png "Selezionare App visualizzazione singola")](hello-tvos-images/setup02.png#lightbox)
4. Immettere `Hello, tvOS` per il **nome App**, immettere il **identificatore organizzazione** e fare clic sui **successivo** pulsante:

    [![](hello-tvos-images/setup04.png "Immettere prima App Tvos")](hello-tvos-images/setup04.png#lightbox)
5. Immettere `Hello_tvOS` per il **nome progetto** e fare clic sui **Create** pulsante:

    [![](hello-tvos-images/setup03.png "Immettere HellotvOS")](hello-tvos-images/setup03.png#lightbox)

Visual Studio per Mac creare la nuova app xamarin. tvos e visualizza i file predefiniti che vengono aggiunti alla soluzione dell'applicazione:

 [![](hello-tvos-images/project01.png "La visualizzazione di file predefinita")](hello-tvos-images/project01.png#lightbox)

Visual Studio per Mac Usa **Solutions** e **progetti**, nello stesso modo esatto che esegue Visual Studio. Una soluzione è un contenitore che può includere uno o più progetti. I progetti possono includere applicazioni, librerie di supporto, applicazioni di test e così via. In questo caso, Visual Studio per Mac ha creato una soluzione e un progetto di applicazione per l'utente.

Se si desidera, è possibile creare codice di uno o più progetti di librerie che contengono codice comune condiviso. Questi progetti di libreria possono essere utilizzati per il progetto di applicazione o condiviso con altri progetti app xamarin. tvos (o xamarin. IOS, xamarin. Android e xamarin. Mac in base al tipo di codice), esattamente come si farebbe se si genera un'applicazione .NET standard.

## <a name="anatomy-of-a-xamarintvos-app"></a>Anatomia di un'App xamarin. tvos

Se si ha familiarità con la programmazione iOS, si noterà qui osservare diverse analogie. In effetti, tvOS 9 è un subset di iOS 9, in modo che numerosi concetti attraverserà qui.

Esaminiamo ora i file nel progetto:

-   `Main.cs`: contiene il punto di ingresso principale dell'app. All'avvio l'app contiene la prima classe e il primo metodo eseguiti.
-   `AppDelegate.cs` : Questo file contiene la classe principale dell'applicazione che è responsabile dell'ascolto degli eventi dal sistema operativo.
-   `Info.plist` : Questo file contiene le proprietà dell'applicazione, ad esempio il nome dell'applicazione, le icone e così via.
-   `ViewController.cs` : Questa è la classe che rappresenta la finestra principale e ne controlla il ciclo di vita di esso.
-   `ViewController.designer.cs` : Questo file contiene codice plumbing che consente di integrare con interfaccia utente della schermata principale.
-  `Main.storyboard` : L'interfaccia utente per la finestra principale. Questo file possa essere creato e gestito dalla finestra di progettazione di Xamarin per iOS.

Nelle sezioni seguenti, articolo verrà fornita una breve presentazione tramite alcuni di questi file. Esploreremo loro in modo più dettagliato in un secondo momento, ma è consigliabile comprendere le nozioni di base a questo punto.

### <a name="maincs"></a>Main.cs

Il `Main.cs` file contiene un valore statico `Main` metodo che crea una nuova istanza dell'app xamarin. tvos e passa il nome della classe che gestirà gli eventi del sistema operativo, che in questo caso è il `AppDelegate` classe:

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

Il `AppDelegate.cs` contiene file nostro `AppDelegate` (classe), che è responsabile per la nostra finestra di creazione e l'ascolto di eventi del sistema operativo:

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

Questo codice è probabilmente noto, a meno che non è stata creata un'applicazione iOS prima, ma è piuttosto semplice. Esaminiamo le righe.

In primo luogo, diamo un'occhiata la dichiarazione di variabile a livello di classe:

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

Il `Window` proprietà consente di accedere alla finestra principale. tvOS Usa che cosa è noto come il *Model View Controller* modello (MVC). In genere, per ogni finestra che consente di creare (e per numerosi altri elementi all'interno di windows), è disponibile un controller che è responsabile del ciclo di vita della finestra, ad esempio apertura, dell'aggiunta di nuove visualizzazioni (controlli) e così via.

Successivamente, abbiamo il `FinishedLaunching` (metodo). Questo metodo viene eseguito dopo l'applicazione è stata creata un'istanza ed è responsabile della creazione della finestra dell'applicazione e iniziare il processo di inserimento della visualizzazione in esso effettivamente. Poiché l'app usa uno Storyboard per definire la relativa interfaccia utente, richiedere codice aggiuntivo non è necessario qui.

Esistono molti altri metodi che vengono forniti nel modello, ad esempio `DidEnterBackground` e `WillEnterForeground`. Questi possono essere rimossi se non vengono utilizzati gli eventi dell'applicazione nell'app.

### <a name="viewcontrollercs"></a>ViewController.cs

Il `ViewController` classe è il controller della finestra principale. Ciò significa che è responsabile per il ciclo di vita della finestra principale. Si userà per esaminato nel dettaglio in un secondo momento, per il momento semplicemente diamo un'occhiata veloce:

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

Il file di progettazione per la classe della finestra principale è attualmente vuoto, ma lo sarà essere popolato automaticamente da Visual Studio per Mac quando creiamo l'interfaccia utente con iOS Designer:

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

È in genere non sono interessati con i file della finestra di progettazione, come sono semplicemente gestiti automaticamente da Visual Studio per Mac e specificare semplicemente il codice plumbing dei requisiti che consente l'accesso ai controlli che viene aggiunta a qualsiasi finestra o visualizzare nella nostra applicazione.

A questo punto è stata creata l'App nell'app xamarin. tvos e abbiamo una conoscenza di base dei relativi componenti, è possibile esaminare la creazione dell'interfaccia utente.

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>Creazione dell'interfaccia utente

Non è necessario utilizzare Progettazione Xamarin per iOS per creare l'interfaccia utente per l'app xamarin. tvos, l'interfaccia utente può essere creato direttamente dalle C# codice ma che non rientra nell'ambito di questo articolo. Per ragioni di semplicità, verrà usato iOS Designer per creare l'interfaccia utente nella parte restante di questa esercitazione.

Per iniziare a creare l'interfaccia utente, è possibile fare doppio clic il `Main.storyboard` del file nei **Esplora soluzioni** per aprirlo e modificarlo in iOS Designer:

[![](hello-tvos-images/designer01.png "File Main.storyboard in Esplora soluzioni")](hello-tvos-images/designer01.png#lightbox)

Questo dovrebbe avviare la finestra di progettazione e simile al seguente:

[![](hello-tvos-images/designer02.png "La finestra di progettazione")](hello-tvos-images/designer02.png#lightbox)

Per altre informazioni su iOS Designer e il relativo funzionamento, vedere la [Introduzione a progettazione Xamarin per iOS](~/ios/user-interface/designer/introduction.md) Guida.

È ora possibile iniziare l'aggiunta di controlli all'area di progettazione dell'app xamarin. tvos.

Seguire questa procedura:

1. Individuare il **casella degli strumenti**, che deve essere a destra dell'area di progettazione:

    [![](hello-tvos-images/designer03.png "Casella degli strumenti")](hello-tvos-images/designer03.png#lightbox)

    Se è possibile individuarlo qui, individuare **visualizzazione > riquadri > casella degli strumenti** per visualizzarlo.
2. Trascinare un **Label** dalle **della casella degli strumenti** all'area di progettazione:

    [![](hello-tvos-images/designer04.png "Trascinare un'etichetta dalla casella degli strumenti")](hello-tvos-images/designer04.png#lightbox)
3. Fare clic sui **Title** proprietà nel **riquadro della proprietà** e modificare il titolo del pulsante in `Hello, tvOS` e impostare il **Font Size** a 128:

    [![](hello-tvos-images/designer05.png "Impostare il titolo per App Tvos e impostare le dimensioni del carattere su 128")](hello-tvos-images/designer05.png#lightbox)
4. Ridimensionare l'etichetta in modo che tutte le parole sono visibili e posizionarlo al centro nella parte superiore della finestra:

    [![](hello-tvos-images/designer06.png "Ridimensionare e allineare al centro l'etichetta")](hello-tvos-images/designer06.png#lightbox)
5. L'etichetta ora dovrà essere vincolato per la relativa posizione, in modo che venga visualizzato come previsto. indipendentemente dalle dimensioni dello schermo. A tale scopo, fare clic sull'etichetta finché il *quadratino T* viene visualizzata:

    [![](hello-tvos-images/designer07.png "L'handle a forma di T")](hello-tvos-images/designer07.png#lightbox)
6. Per applicare un vincolo in senso orizzontale l'etichetta, selezionare il quadrato centrale e trascinare la linea verticale tratteggiata:

    [![](hello-tvos-images/designer08.png "Selezionare il quadrato centrale")](hello-tvos-images/designer08zoom.png#lightbox)

     L'etichetta diventerà arancione.
7. Selezionare l'handle di T nella parte superiore dell'etichetta e trascinare il bordo superiore della finestra:

    [![](hello-tvos-images/designer09.png "Trascinare il quadratino al bordo superiore della finestra")](hello-tvos-images/designer09.png#lightbox)
8. Fare quindi clic la larghezza e quindi l'altezza *handle ossa* come illustrato di seguito:

    [![](hello-tvos-images/designer10.png "La larghezza e altezza ossa handle")](hello-tvos-images/designer10.png#lightbox)

     Quando ogni *handle ossa* è selezionato, selezionare sia la larghezza e altezza rispettivamente per impostare dimensioni fisse.
9. Al termine, i vincoli dovrebbero essere simili a quelle nella scheda Layout del riquadro delle proprietà:

    [![](hello-tvos-images/designer11.png "Vincoli di esempio")](hello-tvos-images/designer11.png#lightbox)
10. Trascinare un **sul pulsante** dalle **della casella degli strumenti** e posizionarlo sotto l'etichetta.
11. Fare clic sui **Title** proprietà nel **riquadro della proprietà** e modificare il titolo del pulsante in `Click Me`:

    [![](hello-tvos-images/designer12.png "Modificare il titolo di pulsanti in fare clic qui")](hello-tvos-images/designer12.png#lightbox)
12. Ripetere i passaggi da 5 a 8 precedenti per vincolare il pulsante nella finestra di tvOS. Tuttavia, invece di trascinare il quadratino di T nella parte superiore della finestra (come nel passaggio #7), trascinarlo nella parte inferiore dell'etichetta:

    [![](hello-tvos-images/designer14.png "Vincolare il pulsante")](hello-tvos-images/designer14.png#lightbox)
13. Trascinare un'altra etichetta sotto il pulsante, in modo che sia la stessa larghezza come prima etichetta e impostare le dimensioni relative **allineamento** al **Center**:

    [![](hello-tvos-images/designer15.png "Trascinare un'altra etichetta sotto il pulsante, ridimensionare in modo da essere della stessa larghezza alla prima etichetta e impostare l'allineamento al centro")](hello-tvos-images/designer15.png#lightbox)
14. Ad esempio la prima etichetta e il pulsante, impostare questa etichetta da allineare al centro e bloccarlo nella posizione e dimensioni:

    [![](hello-tvos-images/designer16.png "Aggiungere l'etichetta nella posizione e dimensioni")](hello-tvos-images/designer16.png#lightbox)
15. Salvare le modifiche all'interfaccia utente.

Durante il ridimensionamento e lo spostamenti dei comandi, si dovrebbe notare che la finestra di progettazione ti offre gli hint snap utili basati su [Human Interface Guidelines di Apple TV](https://developer.apple.com/tvos/human-interface-guidelines/). Queste linee guida consentono di creare applicazioni di qualità elevata che avranno un aspetto famigliare per gli utenti di Apple TV.

Se si osserva il **struttura documento** sezione, si noti come appaiono il layout e la gerarchia degli elementi che costituiscono l'interfaccia utente:

[![](hello-tvos-images/designer17.png "La sezione di struttura documento")](hello-tvos-images/designer17.png#lightbox)

Da qui è possibile selezionare gli elementi da modificare o trascinare per riordinare gli elementi dell'interfaccia utente, se necessario. Ad esempio, se un elemento dell'interfaccia utente è stato coperto da un altro elemento, è possibile trascinarlo nella parte inferiore dell'elenco per impostarlo come elemento superiore della finestra.

Ora che abbiamo creato l'interfaccia utente, è necessario esporre gli elementi dell'interfaccia utente in modo che xamarin. tvos possa accedere e interagire con esse, vedere C# codice.

### <a name="accessing-the-controls-in-the-code-behind"></a>L'accesso ai controlli presenti nel code-behind

Esistono due modi principali per accedere ai controlli che sono stati aggiunti in iOS designer dal codice:

* Creazione di un gestore eventi in un controllo.
* Fornendo il controllo di un nome, in modo che è possibile in un secondo momento vi fanno riferimento.

Quando uno di questi elementi vengono aggiunti, la classe parziale all'interno di `ViewController.designer.cs` verrà aggiornato per riflettere le modifiche. In questo modo sarà possibile quindi accedere ai controlli nel Controller di visualizzazione.

### <a name="creating-an-event-handler"></a>Creazione di un gestore di evento

In questa applicazione di esempio, quando si fa clic sul pulsante vogliamo _qualcosa_ viene eseguita, in modo che un gestore eventi deve essere aggiunto a un evento specifico sul pulsante. Per configurare questa impostazione, eseguire le operazioni seguenti:

1. In Xamarin iOS Designer, selezionare il pulsante sul Controller di visualizzazione.
2. Nel riquadro delle proprietà, selezionare la **eventi** scheda:

    [![](hello-tvos-images/event1.png "Nella scheda eventi")](hello-tvos-images/event1.png#lightbox)
3. Individuare l'evento TouchUpInside e assegnargli un gestore eventi denominato `Clicked`:

    [![](hello-tvos-images/event2.png "L'evento TouchUpInside")](hello-tvos-images/event2.png#lightbox)
4. Quando si preme **invio**, il **ViewController**verrà aperto il file con estensione cs, suggerendo di percorsi per il gestore eventi nel codice. Usare i tasti di direzione sulla tastiera per impostare il percorso:

    [![](hello-tvos-images/event3.png "Impostazione del percorso")](hello-tvos-images/event3.png#lightbox)
5. Si creerà un metodo parziale, come illustrato di seguito:

    [![](hello-tvos-images/event4.png "Il metodo parziale")](hello-tvos-images/event4.png#lightbox)

A questo punto siamo pronti iniziare ad aggiungere il codice per consentire la funzione.

### <a name="naming-a-control"></a>Denominazione di un controllo

Quando si fa clic sul pulsante, è necessario aggiornare l'etichetta in base al numero di clic. A tale scopo, si dovrà accedere l'etichetta nel codice. Questa operazione viene eseguita mediante l'assegnazione un nome. Seguire questa procedura:

1. Aprire lo Storyboard e selezionare l'etichetta nella parte inferiore del Controller di visualizzazione.
2. Nel riquadro delle proprietà, selezionare la **Widget** scheda:

    [![](hello-tvos-images/name1.png "Selezionare la scheda di Widget")](hello-tvos-images/name1.png#lightbox)
3. Sotto **identità > nome**, aggiungere `ClickedLabel`:

    [![](hello-tvos-images/name2.png "Impostare ClickedLabel")](hello-tvos-images/name2.png#lightbox)

A questo punto siamo pronti avviare l'aggiornamento dell'etichetta.

### <a name="how-controls-are-accessed"></a>Modalità di accesso ai controlli

Se si seleziona il `ViewController.designer.cs` nella **Esplora soluzioni** sarà in grado di vedere come il `ClickedLabel` etichetta e il `Clicked` gestore dell'evento sono stati mappati a un **Outlet** e **Azione** in C#:

[![](hello-tvos-images/accesscontrol.png "Outlet e azioni")](hello-tvos-images/accesscontrol.png#lightbox)

È anche possibile notare che `ViewController.designer.cs` è una classe parziale, in modo che Visual Studio per Mac non deve modificare `ViewController.cs` sovrascrivendo eventuali modifiche che sono stati apportati alla classe.

Esposizione di elementi dell'interfaccia utente in questo modo, consente di accedere a tali Controller di visualizzazione.

In genere non sarà mai necessario aprire le `ViewController.designer.cs` manualmente, è stata presentata esclusivamente per scopi didattici.

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>Scrittura del codice

Con l'interfaccia utente creato e gli elementi dell'interfaccia esposti al codice tramite **Outlet** e **azioni**, ora è possibile scrivere il codice per fornire la funzionalità del programma.

Nell'applicazione, ogni volta che il primo pulsante, verranno per aggiornare l'etichetta per visualizzare quante volte è stato fatto clic sul pulsante. A tale scopo, è necessario aprire la `ViewController.cs` file per la modifica facendo doppio clic su esso nel **riquadro della soluzione**:

[![](hello-tvos-images/code01.png "Il riquadro della soluzione")](hello-tvos-images/code01.png#lightbox)

In primo luogo, è necessario creare una variabile a livello di classe nel nostro `ViewController` classe per tenere traccia del numero di clic che si sono verificati. Modificare la definizione della classe nel modo seguente:

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

Successivamente, nella stessa classe (`ViewController`), è necessario eseguire l'override di **ViewDidLoad** (metodo) e aggiungere il codice per impostare il messaggio iniziale per l'etichetta:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

È necessario usare `ViewDidLoad`, anziché un altro metodo, ad esempio `Initialize`, in quanto `ViewDidLoad` viene chiamato *dopo* il sistema operativo ha caricato e creare un'istanza dell'interfaccia utente dal `.storyboard` file. Se si tenta di accedere all'etichetta prima la `.storyboard` file è stato completamente caricato e creare un'istanza, si otterrebbe un `NullReferenceException` errore perché il controllo etichetta non verrebbe ancora creato.

Successivamente, è necessario aggiungere il codice per rispondere all'utente facendo clic sul pulsante. Aggiungere il codice seguente a parziale di classe che è stato creato:

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

Questo codice verrà chiamato ogni volta che l'utente fa clic sul pulsante.

Con tutti gli elementi sul posto, abbiamo sono ora pronti per compilare e testare l'applicazione xamarin. tvos.

## <a name="testing-the-application"></a>Verifica dell'applicazione

È ora possibile compilare ed eseguire l'applicazione per assicurarsi che venga eseguito come previsto. È possibile creare ed eseguire l'App in un unico passaggio oppure è possibile compilare l'App senza eseguirla.

Ogni volta che si compila un'applicazione, è possibile scegliere il tipo di build è:

-   **Eseguire il debug** – una build di debug viene compilata in un ' file (applicazione) con metadati aggiuntivi che consente di eseguire il debug di ciò che avviene durante l'esecuzione dell'applicazione.
-   **Versione** – una build di rilascio crea anche un ' file, ma non include le informazioni di debug, quindi è più piccolo e viene eseguito più rapidamente.  

È possibile selezionare il tipo di compilazione dal **selettore configurazione** nell'angolo superiore sinistro della schermata Visual Studio per Mac:

[![](hello-tvos-images/run01.png "Selezionare il tipo di compilazione")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>Compilazione dell'applicazione

In questo caso, si desidera solo una build di debug, quindi assicurarsi che **Debug** sia selezionata. Creiamo la nostra applicazione prima di tutto, premere **⌘B**, o dal **compilare** dal menu, scegliere **compila tutto**.

Se non c'erano gli eventuali errori, verrà visualizzata una **compilazione riuscita** messaggio in Visual Studio per la barra di stato del Mac. Se si sono verificati errori, rivedere il progetto e assicurarsi di aver seguito i passaggi descritti in modo corretto. Iniziare verificando che il codice (entrambi in Xcode e Visual Studio per Mac) corrisponda al codice dell'esercitazione.

### <a name="running-the-application"></a>Esecuzione dell'applicazione

Per eseguire l'applicazione, sono disponibili tre opzioni:

-  Premere **⌘+INVIO**.
-  Scegliere **Debug** dal menu **Esegui**.
-  Fare clic sul pulsante **Esegui** nella barra degli strumenti di Visual Studio per Mac (sopra **Esplora soluzioni**).

L'applicazione verrà compilata (se non è stata compilata già), verrà avviata l'avvio in modalità debug, il simulatore tvOS e avvierà l'app e la finestra di interfaccia principale visualizzata:

[![Schermata iniziale dell'app di esempio](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

Dal **Hardware** dal menu **mostrano Apple TV remoto** non è possibile controllare il simulatore.

[![](hello-tvos-images/run04.png "Mostra seleziona Apple TV remoto")](hello-tvos-images/run04.png#lightbox)

Utilizzo remoto del simulatore, se si fa clic sul pulsante alcune volte l'etichetta deve essere aggiornata con il conteggio:

[![](hello-tvos-images/run05.png "L'etichetta con conteggio aggiornato")](hello-tvos-images/run05.png#lightbox)

Congratulazioni! Abbiamo affrontato molti argomenti qui, ma se è stata seguita l'esercitazione dall'inizio alla fine, è stata acquisita una conoscenza approfondita dei componenti di un'app xamarin. tvos, nonché gli strumenti usati per crearli.

## <a name="where-to-next"></a>Articoli successivi?

Lo sviluppo presenta le app Apple TV alcuni problemi a causa della disconnessione tra l'utente e l'interfaccia (è tutta la stanza non a disposizione dell'utente) e le limitazioni tvOS posiziona su dimensioni dell'app e l'archiviazione.

Di conseguenza, altamente consigliabile che la lettura seguenti documenti prima di passare alla progettazione di un'app xamarin. tvos:

- [Introduzione a tvOS 9](~/ios/tvos/platform/tvos9.md) : questo articolo vengono presentate tutte le nuove e modificate le API e funzionalità disponibili in tvOS 9 per gli sviluppatori di xamarin. tvos.
- [Utilizzo di navigazione e lo stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) : gli utenti dell'app xamarin. tvos verranno non interagisce con relativa interfaccia direttamente con iOS in cui vengono toccati immagini sullo schermo del dispositivo, ma indirettamente da attraverso le chat room tramite il remoti per Siri. Questo articolo illustra il concetto di messa a fuoco e come viene usato per gestire la navigazione nell'interfaccia utente di un'app xamarin. tvos.
- [Controller Bluetooth e remoti per Siri](~/ios/tvos/platform/remote-bluetooth.md) : il modo principale in cui gli utenti verrà interagisce con Apple TV e app xamarin. tvos, tramite il remoti per Siri incluso. Se l'app è un gioco, è possibile facoltativamente compilare in modalità di supporto per l'entità 3rd, apportate per periferiche di gioco iOS (IFM) Bluetooth in anche nell'app. Questo articolo illustra il supporto di nuove periferiche di gioco Bluetooth e remoti per Siri nelle App xamarin. tvos.
- [Archiviazione dei dati e risorse](~/ios/tvos/app-fundamentals/resources-data-storage.md) : a differenza dei dispositivi iOS, il nuovo televisore Apple non fornisce un'archiviazione persistente e locale per le app tvOS. Di conseguenza, se l'app xamarin. tvos deve mantenere le informazioni (ad esempio le preferenze dell'utente) deve archiviare e recuperare i dati da iCloud. Questo articolo descrive l'uso delle risorse e archiviazione persistente dei dati in un'app xamarin. tvos.
- [Uso di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md) : creazione accattivante icone e immagini sono un aspetto essenziale dello sviluppo di un'esperienza utente coinvolgenti per le app Apple TV. Questa guida illustra i passaggi necessari per creare e includere le risorse grafiche necessarie per le app xamarin. tvos.
- [Interfaccia utente](~/ios/tvos/user-interface/index.md) – la copertura generale utente esperienza inclusi i controlli di interfaccia utente (UI), usare Interface Builder di Xcode e principi di progettazione dell'esperienza utente quando si lavora con xamarin. tvos.
- [Distribuzione e test](~/ios/tvos/deploy-test/index.md) : in questa sezione vengono trattati argomenti usati per testare un'app e come distribuirla. Gli argomenti includono gli strumenti di debug, la distribuzione ai tester e come pubblicare un'applicazione per la Store di App Apple TV.

Se si verificano problemi durante l'uso con xamarin. tvos, vedere la [Troubleshooting](~/ios/tvos/troubleshooting.md) documentazione per un elenco su problemi noti e soluzioni.

## <a name="summary"></a>Riepilogo

Questo articolo è fornita una Guida introduttiva allo sviluppo di App per tvOS con Visual Studio per Mac tramite la creazione di un semplice Hello, app tvOS. Illustrato le nozioni di base di tvOS device provisioning, creazione di un'interfaccia, scrittura di codice per tvOS e testing sul simulatore tvOS.


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Creazione di App per tvOS con Xamarin (video)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
