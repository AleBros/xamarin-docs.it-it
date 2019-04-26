---
title: progettazione dell'interfaccia utente.Storyboard/.xib-less in xamarin. Mac
description: Questo articolo illustra la creazione dell'interfaccia utente di un'applicazione xamarin. Mac direttamente da C# codice, senza file storyboard, file con estensione xib o Interface Builder.
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 076c6464359a58c2b36d157d9620673b0644cd4a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61042286"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>progettazione dell'interfaccia utente.Storyboard/.xib-less in xamarin. Mac

_Questo articolo illustra la creazione dell'interfaccia utente di un'applicazione xamarin. Mac direttamente da C# codice, senza file storyboard, file con estensione xib o Interface Builder._

## <a name="overview"></a>Panoramica

Quando si lavora con C# e .NET in un'applicazione xamarin. Mac, disporre dell'accesso agli elementi dell'interfaccia utente stessa e degli strumenti che gli sviluppatori che lavorano *Objective-C* e *Xcode* viene. In genere, quando si crea un'applicazione xamarin. Mac, si userà Interface Builder di Xcode con i file con estensione xib o storyboard per creare e gestire è interfaccia utente dell'applicazione.

Hai anche la possibilità di creare alcune o tutte dell'interfaccia utente dell'applicazione xamarin. Mac direttamente in C# codice. In questo articolo, illustreremo le nozioni di base della creazione di interfacce utente e gli elementi dell'interfaccia utente in C# codice.

[![Visual Studio per l'editor del codice Mac](xibless-ui-images/intro01.png "di Visual Studio per l'editor di codice Mac")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>Cambio di una finestra per l'uso di codice

Quando si crea una nuova applicazione xamarin. Mac Cocoa, si ottiene una normale finestra vuota, per impostazione predefinita. Questo windows è definito in un **Main. Storyboard** (o in genere una **MainWindow.xib**) file automaticamente inclusi nel progetto. Ciò include anche un **ViewController.cs** file che gestisce visualizzazione principale dell'app (o anche in questo caso tradizionalmente un **MainWindow.cs** e un **MainWindowController.cs** file).

Per passare a una finestra Xibless per un'applicazione, effettuare le operazioni seguenti:

1. Aprire l'applicazione che si desidera interrompere l'uso di `.storyboard` o file con estensione xib per definire l'interfaccia utente in Visual Studio per Mac.
2. Nel **riquadro della soluzione**, fare clic sul **Main. Storyboard** oppure **MainWindow.xib** file e selezionare **rimuovere**: 

    ![Rimozione di storyboard principale o nella finestra](xibless-ui-images/switch01.png "rimuovendo dello storyboard principale o la finestra")
3. Dal **finestra di dialogo Rimuovi**, fare clic sui **eliminare** pulsante per rimuovere completamente la storyboard o con estensione xib dal progetto: 

    ![Per confermare l'eliminazione](xibless-ui-images/switch02.png "per confermare l'eliminazione")

A questo punto sarà necessario modificare il **MainWindow.cs** file per definire il layout della finestra e modificare le **ViewController.cs** oppure **MainWindowController.cs** file da creare un istanza del nostro `MainWindow` classe poiché si sta usando non è più il file con estensione xib o storyboard.

App moderne di xamarin. Mac che usano storyboard per la propria interfaccia utente potrebbe non includere automaticamente il **MainWindow.cs**, **ViewController.cs** o **MainWindowController.cs** file. In base alle esigenze, è sufficiente aggiungere una nuova classe vuota C# classe al progetto (**Add** > **nuovi File...**   >  **Generali** > **classe vuota**) e denominarlo quello utilizzato per il file mancante. 


### <a name="defining-the-window-in-code"></a>Definizione della finestra nel codice

Successivamente, modificare il **MainWindow.cs** file e renderlo simile al seguente:

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

Verranno ora esaminati alcuni degli elementi principali.

In primo luogo, abbiamo aggiunto alcune _proprietà calcolate_ che fungerà da Outlet (come se la finestra è stata creata in un file con estensione xib o storyboard):

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

Questi fornirà l'accesso agli elementi dell'interfaccia utente che si intende visualizzare nella finestra. Poiché la finestra non viene sottoposto a inflating da un file con estensione xib o storyboard, è necessario un modo per creare un'istanza della (come si vedrà successivamente nel `MainWindowController` classe). Ovvero del funzionamento di questo nuovo metodo di costruttore:

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

Si tratta in cui verrà il layout della finestra di progettazione e inserire tutti gli elementi dell'interfaccia utente necessari per creare l'interfaccia utente richiesto. Prima di poter aggiungere eventuali elementi dell'interfaccia utente a una finestra, è necessario un _visualizzazione contenuto_ per contenere gli elementi:

```csharp
ContentView = new NSView (Frame);
```

Questo crea una visualizzazione del contenuto che sarà compilato la finestra. Ora aggiungiamo il primo elemento dell'interfaccia utente, un `NSButton`, nella finestra:

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

La prima cosa da notare è che, a differenza di iOS, macOS utilizza notazione matematica per definire il sistema di coordinate della finestra. Pertanto, il punto di origine è nell'angolo inferiore sinistro della finestra, con i valori crescenti a destra e verso l'angolo superiore destro della finestra. Quando si crea il nuovo `NSButton`, questo vengono presi in considerazione quando si definiscono la posizione e dimensioni sullo schermo.

Il `AutoresizingMask = NSViewResizingMask.MinYMargin` proprietà indica il pulsante a cui si desidera che vengano rimanere nello stesso percorso dalla parte superiore della finestra quando la finestra viene ridimensionata in senso verticale. Anche in questo caso, ciò è necessario perché (0,0) è nella parte inferiore della finestra.

Infine, il `ContentView.AddSubview (ClickMeButton)` metodo aggiunge il `NSButton` alla visualizzazione del contenuto in modo che non verrà visualizzato sullo schermo quando l'applicazione viene eseguita e la finestra visualizzata.

Successivamente viene aggiunta un'etichetta alla finestra che verrà visualizzato il numero di volte che il `NSButton` è stato fatto clic: 

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

Poiché macOS non ha uno specifico _Label_ elemento dell'interfaccia utente, è stata aggiunta una speciale con stile, non modificabile `NSTextField` come un'etichetta. Proprio come il pulsante prima, la dimensione e il percorso tiene conto di (0,0) è nella parte inferiore della finestra. Il `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin` proprietà utilizza il **oppure** operatore per combinare due `NSViewResizingMask` funzionalità. In questo modo l'etichetta rimanere nello stesso percorso dalla parte superiore della finestra quando la finestra viene ridimensionata in senso verticale e riduzione e aumento delle dimensioni in larghezza la finestra viene ridimensionata in senso orizzontale.

Anche in questo caso, il `ContentView.AddSubview (ClickMeLabel)` metodo aggiunge il `NSTextField` alla visualizzazione del contenuto in modo che non verrà visualizzato nella schermata quando viene eseguita l'applicazione e la finestra aperta.


### <a name="adjusting-the-window-controller"></a>Modificare il controller di finestra

Poiché la progettazione del `MainWindow` non viene caricato da un file con estensione xib o storyboard, è necessario apportare alcune modifiche per il controller di finestra. Modificare il **MainWindowController.cs** file e renderlo simile al seguente:

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

Consente di discutere gli elementi chiave di questa modifica.

In primo luogo, definiamo una nuova istanza di `MainWindow` classi e assegnarla al controller di finestra di base `Window` proprietà:

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

Si definisce la posizione della finestra di schermata con una `CGRect`. Proprio come il sistema di coordinate della finestra, la schermata definisce (0,0) come l'angolo inferiore sinistro. Successivamente, si definirà lo stile della finestra usando il **oppure** operatore per combinare due o più `NSWindowStyle` funzionalità:

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
``` 

Nell'esempio `NSWindowStyle` funzionalità sono disponibili:

- **Senza bordi** -la finestra non avrà nessun bordo.
- **Intitolata** -la finestra avrà una barra del titolo.
- **Essere chiusa** -la finestra dispone di un pulsante Chiudi e può essere chiusa.
- **Miniaturizable** -la finestra dispone di un pulsante Miniaturize e può essere ridotto a icona.
- **Ridimensionabile** -la finestra avrà un pulsante Ridimensiona e possono essere ridimensionati.
- **Utilità** -la finestra è una finestra di tipo utilità (pannello).
- **DocModal** -se la finestra è un pannello, potrà essere modale documento anziché sistema modale. 
- **NonactivatingPanel** -se la finestra è un pannello, non diventa la finestra principale.
- **TexturedBackground** -la finestra avrà uno sfondo con trama.
- **Non in scala** -non sarà possibile ridurre la finestra.
- **UnifiedTitleAndToolbar** -aree di titolo e la barra degli strumenti della finestra verranno aggiunti.
- **Hud** -verrà visualizzata la finestra come una visualizzazione hud pannello.
- **FullScreenWindow** -finestra di può attivare la modalità schermo intero.
- **FullSizeContentView** -visualizzazione di contenuto della finestra è dietro il titolo e l'Area della barra degli strumenti.

Le ultime due proprietà definiscono i _tipo di memorizzazione nel buffer_ per la finestra e se verrà rinviata disegno della finestra. Per ulteriori informazioni sul `NSWindows`, vedere di Apple [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) documentazione.

Infine, poiché la finestra non viene sottoposto a inflating da un file storyboard o con estensione xib, è necessario simulare nel nostro **MainWindowController.cs** chiamando il windows `AwakeFromNib` metodo:

```csharp
Window.AwakeFromNib ();
```

Questa operazione consentirà di codice per la finestra semplicemente come una finestra standard caricata da un file con estensione xib o storyboard.


### <a name="displaying-the-window"></a>Visualizzazione della finestra

Con il file storyboard o con estensione xib rimosso e il **MainWindow.cs** e **MainWindowController.cs** file modificati, sarà possibile usare la finestra esattamente come si farebbe qualsiasi finestra normale che è stata creata in Interface Builder di Xcode con un file con estensione xib.

Di seguito verrà creare una nuova istanza della finestra e il controller e visualizzare la finestra sullo schermo:

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

A questo punto, se viene eseguita l'applicazione e un paio di volte selezionato il pulsante, di seguito verrà visualizzato:

![Esempio di app eseguito](xibless-ui-images/run01.png "eseguire un'app di esempio")


## <a name="adding-a-code-only-window"></a>Aggiunta di una finestra del solo codice

Se si vuole aggiungere un solo codice, finestra xibless a un'applicazione xamarin. Mac esistente, pulsante destro del mouse sul progetto nel **riquadro della soluzione** e selezionare **Add** > **nuovo File...** . Nel **nuovo File** finestra di dialogo Scegli **xamarin. Mac** > **finestra Cocoa con Controller**, come illustrato di seguito:

![Aggiunge un nuovo controller di finestra](xibless-ui-images/add01.png "aggiunge un nuovo controller di finestra") 

Esattamente come in precedenza, verranno eliminati il file di storyboard o con estensione xib predefiniti dal progetto (in questo caso **SecondWindow.xib**) e seguire i passaggi descritti nel [cambio di una finestra per utilizzare codice](#Switching_a_Window_to_use_Code) sezione precedente per coprire il definizione della finestra del codice.


## <a name="adding-a-ui-element-to-a-window-in-code"></a>Aggiunta di un elemento dell'interfaccia utente per una finestra del codice

Se una finestra è stata creata nel codice o caricata da un file storyboard o con estensione xib, potrebbero esserci volte in cui si vuole aggiungere un elemento dell'interfaccia utente a una finestra dal codice. Ad esempio:

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

Il codice precedente crea una nuova `NSButton` e lo aggiunge al `MyWindow` istanza finestra per la visualizzazione. Fondamentalmente qualsiasi elemento dell'interfaccia utente che può essere definita in Interface Builder di Xcode in un file storyboard o con estensione xib può essere creato nel codice e visualizzato in una finestra.


## <a name="defining-the-menu-bar-in-code"></a>Definizione della barra dei menu nel codice

A causa di limitazioni correnti in xamarin. Mac, non è consigliabile creare barra dei Menu – dell'applicazione xamarin. Mac`NSMenuBar`-in di codice, ma continuare a usare il **Main. Storyboard** o **MainMenu.xib** file di definizione. Ciò premesso, è possibile aggiungere e rimuovere i menu e voci di Menu C# codice.

Ad esempio, modificare il **AppDelegate.cs** del file e verificare il `DidFinishLaunching` metodo aspetto simile al seguente:

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

Il codice precedente crea un menu della barra di stato dal codice e lo visualizza quando viene avviata l'applicazione. Per altre informazioni sull'utilizzo dei menu, vedere la [menu](~/mac/user-interface/menu.md) documentazione.


## <a name="summary"></a>Riepilogo

Questo articolo ha avuto un quadro dettagliato di creazione dell'interfaccia utente di un'applicazione xamarin. Mac in C# codice invece di usare Interface Builder di Xcode con i file con estensione xib o storyboard.



## <a name="related-links"></a>Collegamenti correlati

- [MacXibless (esempio)](https://developer.xamarin.com/samples/mac/MacXibless/)
- [Windows](~/mac/user-interface/window.md)
- [Menu](~/mac/user-interface/menu.md)
- [Linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Introduzione a Windows](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
