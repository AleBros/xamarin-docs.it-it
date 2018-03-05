---
title: Hello, tvOS Guida introduttiva
description: Questa guida descrive come creare la prima app Xamarin.tvOS e la toolchain di sviluppo. Introduce anche la finestra di progettazione di Xamarin, che espone i controlli dell'interfaccia utente al codice e viene illustrato come compilare, eseguire e testare un'applicazione Xamarin.tvOS.
ms.topic: article
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 02/02/2018
ms.openlocfilehash: fe58aa8ffb74a9b6e937be5a7f1dde0432794405
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="hello-tvos-quick-start-guide"></a>Hello, tvOS Guida introduttiva

_Questa guida descrive come creare la prima app Xamarin.tvOS e la toolchain di sviluppo. Introduce anche la finestra di progettazione di Xamarin, che espone i controlli dell'interfaccia utente al codice e viene illustrato come compilare, eseguire e testare un'applicazione Xamarin.tvOS._

Apple ha rilasciato la generazione della Apple TV, Apple TV 4 KB, che viene eseguito tvOS 11-5.

La piattaforma Apple TV è aperta per gli sviluppatori, consentendo loro di creare applicazioni avanzate, coinvolgente e rilasciarli tramite l'App Store Apple TV incorporato.

Se si ha familiarità con lo sviluppo di xamarin. IOS, è necessario trovare la transizione a tvOS piuttosto semplice. La maggior parte delle funzionalità e API sono gli stessi, tuttavia, molte API comuni non sono disponibili (ad esempio WebKit). Inoltre, funziona con il con remoto Siri pone alcuni problemi di progettazione che non sono presenti nei dispositivi iOS touchscreen basato su.

Questa guida fornisce un'introduzione all'utilizzo con tvOS in un'app Xamarin. Per ulteriori informazioni su tvOS, vedere Apple [prepararsi per Apple TV 4K](https://developer.apple.com/tvos/) documentazione.

## <a name="overview"></a>Panoramica

Xamarin.tvOS consente di sviluppare App per Apple TV completamente native in c# e .NET utilizzando le stesse librerie OS X e i controlli dell'interfaccia utilizzata durante lo sviluppo in *Swift* (o *Objective-C*) e *Xcode*.

Inoltre, poiché Xamarin.tvOS App scritte in c# e .NET, comune, il codice di back-end può essere condivisa con App xamarin. IOS, xamarin e Xamarin.Mac. pur offrendo un'esperienza nativa in ciascuna piattaforma.

In questo articolo illustrano i concetti chiavi necessari per creare un'App di Apple TV con Xamarin.tvOS e Visual Studio, si illustrando il processo di compilazione di base **Hello, tvOS** app che conta il numero di volte in cui ha un pulsante si è fatto clic:

[ ![](hello-tvos-images/run05.png "Eseguire app di esempio")](hello-tvos-images/run05.png)

Verranno trattati i concetti seguenti:

-  **Visual Studio per Mac** : Introduzione a Visual Studio per Mac e come creare applicazioni Xamarin.tvOS con esso.
-  **Anatomia di un'App Xamarin.tvOS** -app Xamarin.tvOS una cosa è costituito.
-  **Creazione di un'interfaccia utente** : utilizzo di progettazione di Xamarin per iOS per creare un'interfaccia utente.
-  **Distribuzione e test** : come eseguire e testare l'app in tvOS simulatore e sull'hardware tvOS reale.

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>A partire da una nuova App Xamarin.tvOS Visual Studio per Mac

Come descritto in precedenza, verrà creata un'applicazione di Apple TV denominata `Hello-tvOS` che aggiunge un singolo pulsante e un'etichetta alla schermata principale. Quando viene fatto clic sul pulsante, l'etichetta visualizza il numero di volte in cui è stato fatto clic.

Per iniziare, è possibile eseguire le operazioni seguenti:

1. Avviare Visual Studio per Mac:

    [ ![](hello-tvos-images/setup01.png "Visual Studio per Mac")](hello-tvos-images/setup01.png)
2. Fare clic su di **nuova soluzione...**  collegamento nell'angolo superiore sinistro della schermata per aprire la **nuovo progetto** la finestra di dialogo.
3. Selezionare **tvOS** > **App** > **singola vista App** e fare clic su di **Avanti** pulsante:

    [ ![](hello-tvos-images/setup02.png "Selezionare una singola visualizzazione App")](hello-tvos-images/setup02.png)
4. Immettere `Hello, tvOS` per il **nome App**, immettere il **identificatore organizzazione** e fare clic su di **Avanti** pulsante:

    [ ![](hello-tvos-images/setup04.png "Immettere Hello, tvOS")](hello-tvos-images/setup04.png)
5. Immettere `Hello_tvOS` per il **nome progetto** e fare clic su di **crea** pulsante:

    [ ![](hello-tvos-images/setup03.png "Immettere HellotvOS")](hello-tvos-images/setup03.png)

Visual Studio per Mac creare la nuova app Xamarin.tvOS e visualizzare i file predefiniti che vengono aggiunti alla soluzione dell'applicazione:

 [ ![](hello-tvos-images/project01.png "La visualizzazione di file predefinita")](hello-tvos-images/project01.png)

Visual Studio per Mac gestisce **soluzioni** e **progetti**, nello stesso modo esatto che esegue Visual Studio. Una soluzione è un contenitore che può includere uno o più progetti. I progetti possono includere applicazioni, librerie di supporto, applicazioni di test e così via. In questo caso, Visual Studio per Mac ha creato una soluzione sia un progetto di applicazione automaticamente.

Se si desidera, è possibile creare codice di uno o più progetti di librerie che contengono codice condiviso comune. Questi progetti di libreria possono essere utilizzati dal progetto di applicazione o condivisi con altri progetti di app Xamarin.tvOS (o xamarin. IOS, xamarin e Xamarin.Mac in base al tipo di codice), esattamente come se si genera un'applicazione .NET standard.

## <a name="anatomy-of-a-xamarintvos-app"></a>Anatomia di un'App Xamarin.tvOS

Se si ha familiarità con iOS di programmazione, si noterà molte analogie qui. In effetti, tvOS 9 è un subset di iOS 9, pertanto molti concetti si intersecano qui.

Esaminiamo ora i file nel progetto:

-   `Main.cs`: contiene il punto di ingresso principale dell'app. All'avvio l'app contiene la prima classe e il primo metodo eseguiti.
-   `AppDelegate.cs` : Questo file contiene la classe principale dell'applicazione che è responsabile per l'ascolto di eventi dal sistema operativo.
-   `Info.plist` : Questo file contiene le proprietà dell'applicazione, ad esempio il nome dell'applicazione, icone e così via.
-   `ViewController.cs` -Questa è la classe che rappresenta la finestra principale e ne controlla il ciclo di vita di esso.
-   `ViewController.designer.cs` : Questo file contiene codice che consente di integrare con interfaccia utente della schermata principale.
-  `Main.storyboard` : L'interfaccia utente per la finestra principale. Questo file possa essere creato e gestito dalla finestra di progettazione di Xamarin per iOS.

Nelle sezioni seguenti, l'utente verrà reindirizzato a un rapido controllo di alcuni di questi file. Si esamineranno loro in modo più dettagliato in un secondo momento, ma è consigliabile comprendere le nozioni di base a questo punto.

### <a name="maincs"></a>Main.cs

Il `Main.cs` file contiene un valore statico `Main` metodo che crea una nuova istanza di applicazione Xamarin.tvOS e passa il nome della classe che gestirà gli eventi del sistema operativo, che in questo caso è la `AppDelegate` classe:

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

Il `AppDelegate.cs` file contiene il nostro `AppDelegate` (classe), che è responsabile per creare la finestra e attesa di eventi del sistema operativo:

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

Questo codice è probabilmente non si ha dimestichezza, a meno che un'applicazione iOS prima di una volta compilato, ma è piuttosto semplice. Esaminiamo le righe.

In primo luogo, esaminiamo la dichiarazione di variabile a livello di classe:

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

Il `Window` proprietà consente di accedere alla finestra principale. tvOS utilizza quello che è noto come il *Model View Controller* modello (MVC). In genere, per ogni finestra che consente di creare e per molte altre operazioni all'interno di windows, è un controller, che è responsabile per ciclo di vita della finestra, ad esempio visualizzazione, aggiunta di nuove visualizzazioni (controlli), e così via.

Successivamente, abbiamo il `FinishedLaunching` metodo. Questo metodo viene eseguito dopo l'applicazione è stata creata un'istanza ed è responsabile della creazione della finestra dell'applicazione e avviare il processo di visualizzazione in essa contenuti effettivamente. Poiché l'app utilizza uno Storyboard per definire l'interfaccia utente, ulteriore codice non è obbligatorio.

Esistono molti altri metodi che vengono forniti nel modello, ad esempio `DidEnterBackground` e `WillEnterForeground`. Questi possono essere rimossi se non vengono utilizzati gli eventi dell'applicazione in app.

### <a name="viewcontrollercs"></a>ViewController.cs

La `ViewController` classe è il controller nostri della finestra principale. Ciò significa che è responsabile per il ciclo di vita della finestra principale. Stiamo per esaminate in dettaglio in un secondo momento, per il momento Iniziamo uno sguardo rapido:

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

Il file della finestra di progettazione per la classe della finestra principale è ora vuoto, ma verrà popolato automaticamente da Visual Studio per Mac con cui è possibile creare l'interfaccia utente con la finestra di progettazione iOS:

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

È in genere non sono interessati i file della finestra di progettazione, come vengono gestiti solo automaticamente da Visual Studio per Mac e fornire solo il codice di plumbing necessarie che consente l'accesso ai controlli che è aggiunta a qualsiasi finestra o vista nell'applicazione.

Ora che è stata creata l'app Xamarin.tvOS e si dispone di una conoscenza di base dei relativi componenti, si esaminerà la creazione dell'interfaccia utente.

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>Creazione dell'interfaccia utente

Non è necessario utilizzare Progettazione Xamarin per iOS per creare l'interfaccia utente per l'app Xamarin.tvOS, l'interfaccia utente può essere creata direttamente dal codice c#, ma che non rientra nell'ambito di questo articolo. Per ragioni di semplicità, verrà usato iOS finestra di progettazione per creare l'interfaccia utente in tutto il resto di questa esercitazione.

Per iniziare a creare l'interfaccia utente, si fare doppio clic su di `Main.storyboard` file nel **Esplora** per aprirlo e modificarlo nella finestra di progettazione iOS:

[ ![](hello-tvos-images/designer01.png "File Main.storyboard in Esplora soluzioni")](hello-tvos-images/designer01.png)

Questo deve avviare la finestra di progettazione e simile al seguente:

[ ![](hello-tvos-images/designer02.png "La finestra di progettazione")](hello-tvos-images/designer02.png)

Per ulteriori informazioni sulla finestra di progettazione iOS e come funziona, consultare il [Introduzione a progettazione Xamarin per iOS](~/ios/user-interface/designer/introduction.md) Guida.

È possibile iniziare l'aggiunta di controlli all'area di progettazione della nostra app Xamarin.tvOS.

Seguire questa procedura:

1. Individuare il **della casella degli strumenti**, che deve essere a destra dell'area di progettazione:

    [![](hello-tvos-images/designer03.png "Casella degli strumenti")](hello-tvos-images/designer03.png)

    Se è possibile individuarlo qui, cercare **Vista > Pad > della casella degli strumenti** per visualizzarlo.
2. Trascinare un **etichetta** dal **della casella degli strumenti** all'area di progettazione:

    [ ![](hello-tvos-images/designer04.png "Trascinare un'etichetta dalla casella degli strumenti")](hello-tvos-images/designer04.png)
3. Fare clic sul **titolo** proprietà il **riempimento proprietà** e modificare il titolo del pulsante in `Hello, tvOS` e impostare il **dimensioni del carattere** a 128:

    [ ![](hello-tvos-images/designer05.png "Impostare il titolo Hello, tvOS e impostare le dimensioni del carattere su 128")](hello-tvos-images/designer05.png)
4. Ridimensionare l'etichetta in modo che tutte le parole sono visibili e posizionarlo centrato nella parte superiore della finestra:

    [ ![](hello-tvos-images/designer06.png "Ridimensionare e allineare al centro dell'etichetta")](hello-tvos-images/designer06.png)
5. L'etichetta ora dovrà essere vincolato per la relativa posizione, in modo che venga visualizzato come previsto. indipendentemente dalle dimensioni dello schermo. A tale scopo, fare clic sull'etichetta fino a quando il *a forma di T* viene visualizzato:

    [ ![](hello-tvos-images/designer07.png "L'handle a forma di T")](hello-tvos-images/designer07.png)
6. Per limitare l'etichetta orizzontalmente, selezionare il quadrato centrale e trascinarlo per la linea verticale tratteggiata:

    [ ![](hello-tvos-images/designer08.png "Selezionare il quadrato centrale")](hello-tvos-images/designer08zoom.png)

     L'etichetta deve attivare arancione.
7. Selezionare l'handle di T nella parte superiore dell'etichetta e trascinarlo sul bordo superiore della finestra:

    [ ![](hello-tvos-images/designer09.png "Trascinare il quadratino sul bordo superiore della finestra")](hello-tvos-images/designer09.png)
8. Successivamente, scegliere la larghezza e quindi l'altezza *handle ossa* come illustrato di seguito:

    [ ![](hello-tvos-images/designer10.png "La larghezza e altezza ossa handle")](hello-tvos-images/designer10.png)

     Quando ogni *handle ossa* è selezionato, selezionare sia la larghezza e altezza rispettivamente per impostare dimensioni fisse.
9. Al termine, è possibile che i vincoli dovrebbero essere simili a quelle nella scheda Layout del riquadro proprietà:

    [ ![](hello-tvos-images/designer11.png "Vincoli di esempio")](hello-tvos-images/designer11.png)
8. Trascinare un **pulsante** dal **della casella degli strumenti** e posizionarlo sotto l'etichetta.
9. Fare clic sul **titolo** proprietà il **riempimento proprietà** e modificare il titolo del pulsante in `Click Me`:

    [ ![](hello-tvos-images/designer12.png "Modificare il titolo di pulsanti a fare clic qui")](hello-tvos-images/designer12.png)
10. Ripetere i passaggi da 5 a 8 per vincolare il pulsante nella finestra tvOS. Tuttavia, invece di trascinare il quadratino di T nella parte superiore della finestra (come indicato al passaggio &#7;), trascinarlo nella parte inferiore dell'etichetta:

    [ ![](hello-tvos-images/designer14.png "Vincolare il pulsante")](hello-tvos-images/designer14.png)
11. Trascinare un'altra etichetta con il pulsante, dimensione in modo che sia la stessa larghezza la prima etichetta e il set relativo **allineamento** a **Center**:

    [ ![](hello-tvos-images/designer15.png "Trascinare un'altra etichetta con il pulsante, dimensione in modo da essere la stessa larghezza alla prima etichetta e impostare l'allineamento al centro")](hello-tvos-images/designer15.png)
12. Ad esempio la prima etichetta e pulsante, impostare questa etichetta al centro e aggiungerlo in posizione e le dimensioni:

    [ ![](hello-tvos-images/designer16.png "Aggiungere l'etichetta nella posizione e dimensioni")](hello-tvos-images/designer16.png)
13. Salvare le modifiche all'interfaccia utente.

Durante il ridimensionamento e spostamento dei controlli in, si dovrebbero notare che la finestra di progettazione fornisce suggerimenti utili snap basati su [indicazioni dell'interfaccia umana Apple TV](https://developer.apple.com/tvos/human-interface-guidelines/). Queste linee guida consentono di creare applicazioni di alta qualità che avranno un aspetto familiare per gli utenti Apple TV.

Se si osserva il **struttura documento** sezione, si noti come vengono visualizzati i layout e la gerarchia degli elementi che costituiscono l'interfaccia utente:

[ ![](hello-tvos-images/designer17.png "La sezione di struttura documento")](hello-tvos-images/designer17.png)

Da qui è possibile selezionare gli elementi da modificare o trascinare per riordinare gli elementi dell'interfaccia utente, se necessario. Ad esempio, se un elemento dell'interfaccia utente è stato viene analizzato da un altro elemento, è possibile trascinarlo nella parte inferiore dell'elenco per selezionare un elemento in primo piano nella finestra di.

Ora che è disponibile l'interfaccia utente creato, è necessario esporre gli elementi dell'interfaccia utente in modo che Xamarin.tvOS può accedere e interagire con essi in codice c#.

### <a name="accessing-the-controls-in-the-code-behind"></a>Accesso ai controlli nel code-behind

Esistono due modi per accedere ai controlli che sono stati aggiunti nella finestra di progettazione iOS dal codice:

* Creazione di un gestore eventi in un controllo.
* Assegnando il controllo a un nome, in modo che è possibile in un secondo momento farvi riferimento.

Quando uno di questi elementi vengono aggiunti, la classe parziale all'interno di `ViewController.designer.cs` verrà aggiornato per riflettere le modifiche. In questo modo sarà possibile quindi accedere ai controlli nel Controller di visualizzazione.

### <a name="creating-an-event-handler"></a>Creazione di un gestore di evento

In questa applicazione di esempio, quando si fa clic sul pulsante desiderato _qualcosa_ viene eseguita, in modo da un gestore eventi deve essere aggiunto a un evento specifico sul pulsante. A tale scopo, eseguire le operazioni seguenti:

1. Nella finestra di progettazione di Xamarin iOS, selezionare il pulsante sul Controller di visualizzazione.
2. Nel riquadro proprietà, selezionare il **eventi** scheda:

    [![](hello-tvos-images/event1.png "Nella scheda eventi")](hello-tvos-images/event1.png)
3. Individuare l'evento TouchUpInside e assegnargli un gestore eventi denominato `Clicked`:

    [![](hello-tvos-images/event2.png "L'evento TouchUpInside")](hello-tvos-images/event2.png)
4. Quando si preme **invio**, **ViewController**verrà aperto il file con estensione cs, suggerendo percorsi per il gestore eventi nel codice. Utilizzare i tasti di direzione sulla tastiera per impostare il percorso:

    [![](hello-tvos-images/event3.png "Impostazione del percorso")](hello-tvos-images/event3.png)
5. Questo comando creerà un metodo parziale, come illustrato di seguito:

    [![](hello-tvos-images/event4.png "Il metodo parziale")](hello-tvos-images/event4.png)

Ci sono ora pronti per iniziare ad aggiungere codice per consentire il pulsante alla funzione.

### <a name="naming-a-control"></a>Denominazione di un controllo

Quando si fa clic sul pulsante, è necessario aggiornare l'etichetta in base al numero di clic. A tale scopo, sarà necessario accedere l'etichetta nel codice. Questa operazione viene eseguita mediante l'assegnazione di un nome. Seguire questa procedura:

1. Aprire lo Storyboard e selezionare l'etichetta nella parte inferiore del Controller di visualizzazione.
2. Nel riquadro proprietà, selezionare il **Widget** scheda:

    [![](hello-tvos-images/name1.png "Selezionare la scheda Widget")](hello-tvos-images/name1.png)
3. In **identità > nome**, aggiungere `ClickedLabel`:

    [![](hello-tvos-images/name2.png "Impostare ClickedLabel")](hello-tvos-images/name2.png)

A questo punto si è pronti avviare l'aggiornamento dell'etichetta!

### <a name="how-controls-are-accessed"></a>Modalità di accesso ai controlli

Se si seleziona il `ViewController.designer.cs` nel **Esplora soluzioni** sarà in grado di vedere come il `ClickedLabel` etichetta e il `Clicked` gestore dell'evento sono stati mappati a un **presa** e **Azione** in c#:

[ ![](hello-tvos-images/accesscontrol.png "Punti vendita e le azioni")](hello-tvos-images/accesscontrol.png)

È inoltre possibile notare che `ViewController.designer.cs` è una classe parziale, in modo che Visual Studio per Mac senza dover modificare `ViewController.cs` che sovrascriverà le modifiche che sono stati apportati alla classe.

Esposizione di elementi dell'interfaccia utente in questo modo, consente di accedere a tali Controller di visualizzazione.

È in genere, non è necessario aprire il `ViewController.designer.cs` manualmente, è stata presentata esclusivamente per scopi didattici.

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>Scrittura del codice

Con l'interfaccia utente creato e i relativi elementi dell'interfaccia utente esposte al codice tramite **prese** e **azioni**, infine, è possibile scrivere il codice per fornire le funzionalità del programma.

Nell'applicazione, ogni volta che si fa clic sul primo pulsante, verrà per aggiornare l'etichetta per visualizzare il numero di volte sul pulsante di ToolStripSplitButton. A tale scopo, è necessario aprire il `ViewController.cs` file per la modifica facendo doppio clic su esso nel **soluzione riempimento**:

[ ![](hello-tvos-images/code01.png "Il riquadro soluzione")](hello-tvos-images/code01.png)

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

Successivamente, nella stessa classe (`ViewController`), è necessario eseguire l'override di **ViewDidLoad** (metodo) e aggiungere codice per impostare il messaggio iniziale per l'etichetta:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

È necessario utilizzare `ViewDidLoad `, invece di un altro metodo, ad esempio `Initialize`perché `ViewDidLoad ` viene chiamato *dopo* il sistema operativo è stato caricato e creare l'interfaccia utente da un'istanza del `.storyboard` file. Se si tenta di accedere al controllo etichetta prima di `.storyboard` file è stata completamente caricata e creata un'istanza, si otterrebbe un `NullReferenceException` errore perché il controllo etichetta non verrebbe ancora creato.

Successivamente, è necessario aggiungere il codice per rispondere all'utente facendo clic sul pulsante. Aggiungere quanto segue parziale di classe creata:

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

Questo codice verrà chiamato ogni volta che l'utente sceglie il pulsante.

Con tutti gli elementi sul posto, ci sono ora pronte compilare e testare l'applicazione Xamarin.tvOS.

## <a name="testing-the-application"></a>Verifica dell'applicazione

È possibile compilare ed eseguire l'applicazione per verificare che venga eseguito come previsto. È possibile compilare ed eseguire un'unica operazione oppure è possibile compilare senza eseguirla.

Ogni volta che si compila un'applicazione, è possibile scegliere il tipo di compilazione è:

-   **Eseguire il debug** – una build di debug viene compilata in un ' file (applicazione) con metadati aggiuntivi che consentono di eseguire il debug di ciò che avviene durante l'esecuzione dell'applicazione.
-   **Versione** : una build di rilascio crea anche un ', ma non include informazioni di debug, pertanto è più piccolo e viene eseguito più velocemente.  

È possibile selezionare il tipo di compilazione dal **configurazione selettore** nell'angolo superiore sinistro di Visual Studio per la schermata Mac:

[ ![](hello-tvos-images/run01.png "Selezionare il tipo di compilazione")](hello-tvos-images/run01.png)

### <a name="building-the-application"></a>Compilazione dell'applicazione

In questo caso, si desidera una build di debug, quindi verificare che **Debug** è selezionata. Creare ora innanzitutto l'applicazione premendo **⌘B**, o dal **compilare** menu, scegliere **compilare tutti**.

Se non ci sono errori, verrà visualizzato un **compilazione completata** messaggio in Visual Studio per la barra di stato del Mac. Se si sono verificati errori, esaminare il progetto e assicurarsi di aver eseguito i passaggi in modo corretto. Iniziare verificando che il codice (entrambi in Xcode e in Visual Studio per Mac) corrisponde al codice dell'esercitazione.

### <a name="running-the-application"></a>Esecuzione dell'applicazione

Per eseguire l'applicazione, sono disponibili tre opzioni:

-  Premere **⌘+INVIO**.
-  Scegliere **Debug** dal menu **Esegui**.
-  Fare clic sul pulsante **Esegui** nella barra degli strumenti di Visual Studio per Mac (sopra **Esplora soluzioni**).

L'applicazione verrà compilata (se è non è stato compilato già), avvio in modalità debug, il simulatore tvOS viene avviato e l'app verrà avviare e visualizzare la finestra principale dell'interfaccia:

[ ![La schermata iniziale di app di esempio](hello-tvos-images/run03.png)](hello-tvos-images/run03.png)

Dal **Hardware** menu selezionare **Mostra Apple TV remoto** è pertanto possibile controllare il simulatore.

[ ![](hello-tvos-images/run04.png "Selezionare Mostra Apple TV remota")](hello-tvos-images/run04.png)

Utilizzo remoto del simulatore, se si fa clic sul pulsante più volte l'etichetta deve essere aggiornato con il conteggio:

[ ![](hello-tvos-images/run05.png "L'etichetta con conteggio aggiornato")](hello-tvos-images/run05.png)

La procedura è stata completata. Abbiamo parlato molta terra qui, ma se questa esercitazione è stata seguita dall'inizio alla fine, è stata acquisita una conoscenza approfondita dei componenti di un'app Xamarin.tvOS, nonché gli strumenti usati per crearli.

## <a name="where-to-next"></a>Dove successivo?

Sviluppo Visualizza App Apple TV richiede qualche a causa della disconnessione tra l'utente e l'interfaccia (si trova tra la chat non nel manuale dell'utente) e le limitazioni tvOS inserisce sulle dimensioni di app e archiviazione.

Di conseguenza, altamente consigliabile che la lettura seguenti documenti prima di passare alla progettazione dell'applicazione un Xamarin.tvOS:

- [Introduzione a tvOS 9](~/ios/tvos/platform/tvos9.md) : questo articolo descrive tutte le nuove e modificate le API e le funzionalità disponibili in tvOS 9 per gli sviluppatori Xamarin.tvOS.
- [Utilizzo di navigazione e lo stato attivo](~/ios/tvos/app-fundamentals/navigation-focus.md) : gli utenti dell'app Xamarin.tvOS verranno non l'interazione con relativa interfaccia direttamente come con iOS in cui vengono toccati immagini sullo schermo del dispositivo, ma indirettamente da attraverso la chat remota Siri utilizzando. Questo articolo descrive il concetto di messa a fuoco e come viene utilizzata per gestire la navigazione nell'interfaccia utente dell'applicazione un Xamarin.tvOS.
- [Siri remoto e i controller Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) : il modo principale in cui gli utenti verranno interagisce con Apple TV e app Xamarin.tvOS, tramite remoto Siri incluso. Se l'app è un gioco, è possibile, facoltativamente, compilare in supporto per l'entità, 3rd apportate per iOS (IFM) Bluetooth periferiche di gioco nell'app anche. Questo articolo descrive il supporto di nuovi controller di gioco Siri remoto e Bluetooth nelle App Xamarin.tvOS.
- [Archiviazione dei dati e risorse](~/ios/tvos/app-fundamentals/resources-data-storage.md) : a differenza dei dispositivi iOS, il nuovo televisore Apple non fornisce un'archiviazione persistente e locale per le app tvOS. Di conseguenza, se l'app Xamarin.tvOS deve rendere persistenti le informazioni (ad esempio le preferenze dell'utente) deve archiviare e recuperare i dati da iCloud. In questo articolo viene descritto l'utilizzo con le risorse e l'archiviazione dei dati permanenti in un'app Xamarin.tvOS.
- [Utilizzo di immagini e icone](~/ios/tvos/app-fundamentals/icons-images.md) – creazione straordinaria icone e immagini sono una parte essenziale di sviluppo di un'esperienza coinvolgente per le app di Apple TV. Questa guida illustra i passaggi necessari per creare e includere le risorse grafiche necessarie per le app Xamarin.tvOS.
- [Interfaccia utente](~/ios/tvos/user-interface/index.md) : code coverage del esperienza utente generale (UX), inclusi i controlli dell'interfaccia utente (UI), utilizzare il generatore di interfaccia di Xcode e i principi di progettazione dell'esperienza utente quando si lavora con Xamarin.tvOS.
- [Distribuzione e test](~/ios/tvos/deploy-test/index.md) : questa sezione vengono trattati gli argomenti utilizzati per testare un'app e come la distribuzione. Argomenti qui includono, ad esempio, gli strumenti utilizzati per il debug, distribuzione a tester e come pubblicare un'applicazione in App Store di Apple TV.

Se si verificano problemi durante l'utilizzo di Xamarin.tvOS, consultare il nostro [Troubleshooting](~/ios/tvos/troubleshooting.md) documentazione per un elenco di conoscere i problemi e soluzioni.

## <a name="summary"></a>Riepilogo

In questo articolo viene fornita una Guida introduttiva allo sviluppo di App per tvOS con Visual Studio per Mac mediante la creazione di un semplice Hello, tvOS app. Illustrate le nozioni di base del dispositivo tvOS provisioning, creazione dell'interfaccia, la codifica per tvOS e il testing e il simulatore tvOS.


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [Creazione di applicazioni per tvOS con Xamarin (video)](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
