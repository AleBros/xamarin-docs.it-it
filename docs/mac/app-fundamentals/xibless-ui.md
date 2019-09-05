---
title: . Storyboard/. xib-progettazione dell'interfaccia utente in Novell. Mac
description: Questo articolo illustra la creazione di un'interfaccia utente dell'applicazione Novell. Mac C# direttamente dal codice, senza file con estensione storyboard, file xib o Interface Builder.
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: 5776855039120b0c856a76a31334420ded2a2d65
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283323"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>. Storyboard/. xib-progettazione dell'interfaccia utente in Novell. Mac

_Questo articolo illustra la creazione di un'interfaccia utente dell'applicazione Novell. Mac C# direttamente dal codice, senza file con estensione storyboard, file xib o Interface Builder._

## <a name="overview"></a>Panoramica

Quando si lavora C# con e .NET in un'applicazione Novell. Mac, è possibile accedere agli stessi elementi e strumenti dell'interfaccia utente che uno sviluppatore lavora in *Objective-C* e *Xcode* . In genere, quando si crea un'applicazione Novell. Mac, per creare e gestire l'interfaccia utente dell'applicazione verrà usato il Interface Builder di Xcode con i file. Storyboard o. xib.

È anche possibile creare alcune o tutte le interfacce utente dell'applicazione Novell. Mac direttamente nel C# codice. In questo articolo verranno illustrate le nozioni di base per la creazione di interfacce utente C# e elementi dell'interfaccia utente nel codice.

[![Editor di codice Visual Studio per Mac](xibless-ui-images/intro01.png "Editor di codice Visual Studio per Mac")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>Spostamento di una finestra per l'utilizzo del codice

Quando si crea una nuova applicazione Novell. Mac Cocoa, per impostazione predefinita si ottiene una finestra vuota standard. Questa finestra è definita in un file **Main. Storyboard** (o tradizionalmente un file **MainWindow. xib**) incluso automaticamente nel progetto. Include anche un file **ViewController.cs** che gestisce la visualizzazione principale dell'app (o di solito un file **MainWindow.cs** e **MainWindowController.cs** ).

Per passare a una finestra Xibless per un'applicazione, eseguire le operazioni seguenti:

1. Aprire l'applicazione che si desidera arrestare utilizzando `.storyboard` i file o XIB per definire l'interfaccia utente in Visual Studio per Mac.
2. Nel **riquadro della soluzione**fare clic con il pulsante destro del mouse sul file **Main. Storyboard** o **MainWindow. xib** e selezionare **Rimuovi**:

    ![Rimozione dello storyboard o della finestra principale](xibless-ui-images/switch01.png "Rimozione dello storyboard o della finestra principale")
3. Nella **finestra di dialogo Rimuovi**fare clic sul pulsante **Elimina** per rimuovere completamente il. Storyboard o. xib dal progetto:

    ![Conferma dell'eliminazione](xibless-ui-images/switch02.png "Conferma dell'eliminazione")

A questo punto è necessario modificare il file **MainWindow.cs** per definire il layout della finestra e modificare il file **ViewController.cs** o **MainWindowController.cs** per `MainWindow` creare un'istanza della classe poiché non si sta più usando. storyboard o file XIB.

Le app Novell. Mac moderne che usano gli storyboard per la loro interfaccia utente potrebbero non includere automaticamente i file **MainWindow.cs**, **ViewController.cs** o **MainWindowController.cs** . Se necessario, aggiungere semplicemente una nuova classe C# vuota al progetto (**Aggiungi** > **nuovo file...** Classe vuotaGenerale > ) e denominarla come il file mancante.  > 


### <a name="defining-the-window-in-code"></a>Definizione della finestra nel codice

Modificare quindi il file **MainWindow.cs** e apparirà come segue:

```csharp
using System;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindow : NSWindow
    {
        #region Private Variables
        private int NumberOfTimesClicked = 0;
        #endregion

        #region Computed Properties
        public NSButton ClickMeButton { get; set;}
        public NSTextField ClickMeLabel { get ; set;}
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }

        public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
            // Define the user interface of the window here
            Title = "Window From Code";

            // Create the content view for the window and make it fill the window
            ContentView = new NSView (Frame);

            // Add UI elements to window
            ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
                AutoresizingMask = NSViewResizingMask.MinYMargin
            };
            ContentView.AddSubview (ClickMeButton);

            ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
                BackgroundColor = NSColor.Clear,
                TextColor = NSColor.Black,
                Editable = false,
                Bezeled = false,
                AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
                StringValue = "Button has not been clicked yet."
            };
            ContentView.AddSubview (ClickMeLabel);
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Wireup events
            ClickMeButton.Activated += (sender, e) => {
                // Update count
                ClickMeLabel.StringValue = (++NumberOfTimesClicked == 1) ? "Button clicked one time." : string.Format("Button clicked {0} times.",NumberOfTimesClicked);
            };
        }
        #endregion

    }
}
```

Verranno ora esaminati alcuni elementi chiave.

In primo luogo, sono state aggiunte alcune _proprietà calcolate_ che fungeranno da Outlet (come se la finestra fosse stata creata in un file con estensione storyboard o XIB):

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

Questi forniranno l'accesso agli elementi dell'interfaccia utente che verranno visualizzati nella finestra. Poiché la finestra non viene ingrandita da un file con estensione storyboard o XIB, è necessario un modo per crearne un'istanza (come si vedrà più avanti nella `MainWindowController` classe). Ecco come funziona questo nuovo metodo del costruttore:

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

Questa è la posizione in cui verrà progettato il layout della finestra e tutti gli elementi dell'interfaccia utente necessari per creare l'interfaccia utente richiesta. Prima di poter aggiungere elementi dell'interfaccia utente a una finestra, è necessaria una _visualizzazione contenuto_ per contenere gli elementi:

```csharp
ContentView = new NSView (Frame);
```

Verrà creata una visualizzazione contenuto che compilerà la finestra. A questo punto, viene aggiunto il primo elemento `NSButton`dell'interfaccia utente, un oggetto, alla finestra:

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

Il primo aspetto da notare è che, a differenza di iOS, macOS usa la notazione matematica per definire il sistema di coordinate della finestra. Quindi il punto di origine si trova nell'angolo in basso a sinistra della finestra, con valori che aumentano verso destra e verso l'angolo superiore destro della finestra. Quando si crea il nuovo `NSButton`oggetto, questo viene preso in considerazione durante la definizione della posizione e della dimensione sullo schermo.

La `AutoresizingMask = NSViewResizingMask.MinYMargin` proprietà indica al pulsante che deve rimanere nella stessa posizione dalla parte superiore della finestra quando la finestra viene ridimensionata verticalmente. Questa operazione è necessaria perché (0,0) si trova nella parte inferiore sinistra della finestra.

Infine, il `ContentView.AddSubview (ClickMeButton)` metodo `NSButton` aggiunge alla visualizzazione contenuto in modo che venga visualizzato sullo schermo quando l'applicazione viene eseguita e viene visualizzata la finestra.

Viene quindi aggiunta un'etichetta alla finestra che visualizzerà il numero di volte in cui `NSButton` è stato fatto clic sul pulsante:

```csharp
ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
    BackgroundColor = NSColor.Clear,
    TextColor = NSColor.Black,
    Editable = false,
    Bezeled = false,
    AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
    StringValue = "Button has not been clicked yet."
};
ContentView.AddSubview (ClickMeLabel);
```

Poiché MacOS non dispone di un elemento dell'interfaccia utente di _etichetta_ specifico, è stato aggiunto un oggetto con stile `NSTextField` , non modificabile, che funge da etichetta. Analogamente al pulsante precedente, la dimensione e la posizione prendono in considerazione che (0,0) si trova nella parte inferiore sinistra della finestra. La `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin` proprietà usa l'operatore **or** per combinare due `NSViewResizingMask` funzionalità. In questo modo l'etichetta viene mantenuta nella stessa posizione dalla parte superiore della finestra quando la finestra viene ridimensionata verticalmente e viene compattata e aumentata a larghezza quando la finestra viene ridimensionata orizzontalmente.

Anche `NSTextField` in questo `ContentView.AddSubview (ClickMeLabel)` caso, il metodo aggiunge alla visualizzazione contenuto in modo che venga visualizzato sullo schermo quando viene eseguita l'applicazione e la finestra viene aperta.


### <a name="adjusting-the-window-controller"></a>Regolazione del controller della finestra

Poiché la progettazione del `MainWindow` non viene più caricata da un file con estensione storyboard o XIB, è necessario apportare alcune modifiche al controller della finestra. Modificare il file **MainWindowController.cs** e renderlo simile al seguente:

```csharp
using System;

using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindowController : NSWindowController
    {
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindowController (NSCoder coder) : base (coder)
        {
        }

        public MainWindowController () : base ("MainWindow")
        {
            // Construct the window from code here
            CGRect contentRect = new CGRect (0, 0, 1000, 500);
            base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);

            // Simulate Awaking from Nib
            Window.AwakeFromNib ();
        }

        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }

        public new MainWindow Window {
            get { return (MainWindow)base.Window; }
        }
    }
}

```

Si esamineranno gli elementi chiave di questa modifica.

In primo luogo, si definisce una nuova istanza `MainWindow` della classe e la si assegna alla `Window` proprietà del controller della finestra di base:

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

Si definisce la posizione della finestra dello schermo con un `CGRect`. Proprio come il sistema di coordinate della finestra, la schermata definisce (0,0) come angolo in basso a sinistra. Si definisce quindi lo stile della finestra usando l'operatore **or** per combinare due o più `NSWindowStyle` funzionalità:

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
```

Sono disponibili `NSWindowStyle` le seguenti funzionalità:

- Senza **bordi** : la finestra non avrà alcun bordo.
- **Titolo** : la finestra avrà una barra del titolo.
- **Closable** : la finestra dispone di un pulsante Chiudi e può essere chiusa.
- **Miniaturizable** -la finestra ha un pulsante miniaturizzare e può essere ridotta a icona.
- **Ridimensionabile: la** finestra avrà un pulsante Ridimensiona e sarà ridimensionabile.
- **Utilità** : la finestra è una finestra di stile utilità (pannello).
- **DocModal** -se la finestra è un pannello, sarà modale per i documenti anziché per il sistema modale.
- **NonactivatingPanel** -se la finestra è un pannello, la finestra principale non verrà creata.
- **TexturedBackground** : la finestra avrà uno sfondo con trama.
- Non **ridimensionato** : la finestra non verrà ridimensionata.
- **UnifiedTitleAndToolbar** : le aree del titolo e della barra degli strumenti della finestra verranno unite.
- **HUD** : la finestra verrà visualizzata come pannello di visualizzazione Heads-up.
- **FullScreenWindow** -la finestra può passare alla modalità schermo intero.
- **FullSizeContentView** : la visualizzazione contenuto della finestra è dietro il titolo e l'area della barra degli strumenti.

Le ultime due proprietà definiscono il _tipo di buffering_ per la finestra e se il disegno della finestra verrà posticipato. Per ulteriori informazioni su `NSWindows`, vedere l' [introduzione di Apple alla](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) documentazione di Windows.

Infine, poiché la finestra non viene ingrandita da un file con estensione storyboard o XIB, è necessario simularla nel **MainWindowController.cs** chiamando il metodo Windows `AwakeFromNib` :

```csharp
Window.AwakeFromNib ();
```

Ciò consentirà di eseguire il codice in base alla finestra come una finestra standard caricata da un file con estensione storyboard o XIB.


### <a name="displaying-the-window"></a>Visualizzazione della finestra

Se il file con estensione storyboard o XIB è stato rimosso e i file **MainWindow.cs** e **MainWindowController.cs** sono stati modificati, si userà la finestra Analogamente a qualsiasi finestra normale che era stata creata nel Interface Builder di Xcode con un file con estensione XIB.

Di seguito viene creata una nuova istanza della finestra e del relativo controller e visualizzata la finestra sullo schermo:

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

A questo punto, se l'applicazione viene eseguita e il pulsante ha fatto clic su un paio di volte, verrà visualizzato quanto segue:

![Esecuzione di un'app di esempio](xibless-ui-images/run01.png "Esecuzione di un'app di esempio")


## <a name="adding-a-code-only-window"></a>Aggiunta di una finestra solo codice

Se si vuole aggiungere un solo codice, finestra xibless a un'applicazione Novell. Mac esistente, fare clic con il pulsante destro del mouse sul progetto nella **riquadro della soluzione** e scegliere **Aggiungi** > **nuovo file.** Nella finestra di dialogo **nuovo file** scegliere **Novell. Mac** > **Cocoa con controller**, come illustrato di seguito:

![Aggiunta di un nuovo controller di finestra](xibless-ui-images/add01.png "Aggiunta di un nuovo controller di finestra")

Esattamente come in precedenza, il file default. Storyboard o. xib verrà eliminato dal progetto (in questo caso **SecondWindow. xib**) e seguirà i passaggi nella sezione [passaggio a una finestra per utilizzare il codice](#Switching_a_Window_to_use_Code) precedente per coprire la definizione della finestra nel codice.


## <a name="adding-a-ui-element-to-a-window-in-code"></a>Aggiunta di un elemento dell'interfaccia utente a una finestra nel codice

Se una finestra è stata creata nel codice o è stata caricata da un file con estensione storyboard o XIB, potrebbe essere necessario aggiungere un elemento dell'interfaccia utente a una finestra dal codice. Ad esempio:

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

Il codice precedente crea un nuovo `NSButton` oggetto e lo aggiunge all' `MyWindow` istanza della finestra per la visualizzazione. In pratica, qualsiasi elemento dell'interfaccia utente che può essere definito in Interface Builder di Xcode in un file con estensione storyboard o XIB può essere creato nel codice e visualizzato in una finestra.


## <a name="defining-the-menu-bar-in-code"></a>Definizione della barra dei menu nel codice

A causa delle limitazioni correnti in Novell. Mac, non è consigliabile creare la barra`NSMenuBar`dei menu dell'applicazione Novell. Mac, ovvero nel codice, ma continuare a usare il file **Main. Storyboard** o **MainMenu. xib** per definirlo. Detto ciò, è possibile aggiungere e rimuovere menu e voci di menu C# nel codice.

Modificare ad esempio il file **AppDelegate.cs** e fare in modo `DidFinishLaunching` che il metodo sia simile al seguente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    // Create a Status Bar Menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Phrases";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Phrases");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        Console.WriteLine("Address Selected");
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        Console.WriteLine("Date Selected");
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        Console.WriteLine("Greetings Selected");
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        Console.WriteLine("Signature Selected");
    };
    item.Menu.AddItem (signature);
}
```

Il codice precedente crea un menu della barra di stato dal codice e lo Visualizza quando viene avviata l'applicazione. Per ulteriori informazioni sull'utilizzo dei menu, consultare la documentazione relativa ai [menu](~/mac/user-interface/menu.md) .


## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato in modo dettagliato la creazione di un'interfaccia utente dell'applicazione Novell. Mac C# nel codice anziché l'uso di Interface Builder di Xcode con i file. Storyboard o. xib.



## <a name="related-links"></a>Collegamenti correlati

- [MacXibless (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/macxibless)
- [Windows](~/mac/user-interface/window.md)
- [Menu](~/mac/user-interface/menu.md)
- [Linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Introduzione a Windows](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
