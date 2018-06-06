---
title: progettazione dell'interfaccia utente.Storyboard/.xib-less in Xamarin.Mac
description: Questo articolo vengono illustrate la creazione di un Xamarin.Mac interfaccia utente direttamente dal codice c#, senza file .storyboard, .xib oppure il generatore di interfaccia.
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 239133c8a5bcce97aca0c4444624fe0541600354
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792401"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>progettazione dell'interfaccia utente.Storyboard/.xib-less in Xamarin.Mac

_Questo articolo vengono illustrate la creazione di un Xamarin.Mac interfaccia utente direttamente dal codice c#, senza file .storyboard, .xib oppure il generatore di interfaccia._

## <a name="overview"></a>Panoramica

Quando si lavora con c# e .NET in un'applicazione Xamarin.Mac, disporre dell'accesso agli elementi dell'interfaccia utente stesso e strumenti che uno sviluppatore che lavora *Objective-C* e *Xcode* does. In genere, quando si crea un'applicazione Xamarin.Mac, userai interfaccia generatore del Xcode con .storyboard o .xib file per creare e gestire è l'interfaccia utente dell'applicazione.

È inoltre l'opzione di creazione di alcuni o tutti dell'interfaccia utente dell'applicazione Xamarin.Mac direttamente nel codice c#. In questo articolo verranno descritte le nozioni di base della creazione di interfacce utente e gli elementi dell'interfaccia utente nel codice c#.

[![Visual Studio per l'editor del codice Mac](xibless-ui-images/intro01.png "Visual Studio per l'editor del codice Mac")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>Cambio di una finestra per l'utilizzo di codice

Quando si crea una nuova applicazione Xamarin.Mac Cocoa, per impostazione predefinita è ottenere una finestra vuota, standard. Questo windows è definito in un **Main** (o, in genere un **MainWindow.xib**) file automaticamente incluso nel progetto. Ciò include anche un **ViewController.cs** che gestisce la visualizzazione principale dell'applicazione (o nuovamente in genere un **MainWindow.cs** e un **MainWindowController.cs** file).

Per passare a una finestra Xibless per un'applicazione, effettuare le operazioni seguenti:

1. Aprire l'applicazione che si desidera interrompere l'uso di `.stroyboard` o .xib file per definire l'interfaccia utente in Visual Studio per Mac.
2. Nel **soluzione riempimento**, fare clic su di **Main** o **MainWindow.xib** file e selezionare **rimuovere**: 

    ![Rimozione di storyboard principale o nella finestra](xibless-ui-images/switch01.png "la rimozione di storyboard principale o nella finestra")
3. Dal **finestra di dialogo Rimuovi**, fare clic su di **eliminare** pulsante per rimuovere completamente il .storyboard o .xib dal progetto: 

    ![Per confermare l'eliminazione](xibless-ui-images/switch02.png "per confermare l'eliminazione")

Ora è necessario modificare il **MainWindow.cs** file per definire il layout della finestra e modificare il **ViewController.cs** o **MainWindowController.cs** file per creare un istanza di questo `MainWindow` classe poiché non si sta utilizzando il file .storyboard o .xib.

Moderna Xamarin.Mac le app che usano storyboard per la propria interfaccia utente potrebbe non includere automaticamente il **MainWindow.cs**, **ViewController.cs** o **MainWindowController.cs** file. In base alle esigenze, è sufficiente aggiungere una nuova vuota c# classe al progetto (**Aggiungi** > **nuovo File...**   >  **Generale** > **classe vuota**) e assegnargli il nome stesso come il file mancante. 


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

Esaminiamo alcuni degli elementi principali.

In primo luogo, abbiamo aggiunto alcune _proprietà calcolate_ che fungerà da prese (come se la finestra è stata creata in un file .storyboard o .xib):

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

Questi verranno ci fornisce l'accesso agli elementi dell'interfaccia utente che verrà visualizzato nella finestra. Poiché la finestra non viene ingrandita da un file .storyboard o .xib, è necessario un modo per crearne un'istanza (come vedremo successivamente nel `MainWindowController` classe). Che viene eseguita in questo nuovo metodo del costruttore:

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

Si tratta in cui verrà il layout della finestra di progettazione e inserire tutti gli elementi dell'interfaccia utente necessari per creare l'interfaccia utente richiesto. Prima di aggiungere tutti gli elementi dell'interfaccia utente a una finestra, è necessario un _visualizzazione contenuto_ per contenere gli elementi:

```csharp
ContentView = new NSView (Frame);
```

Viene creata una visualizzazione di contenuto che compilerà la finestra. Ora è il primo elemento dell'interfaccia utente, di aggiungere un `NSButton`, alla finestra:

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

La prima cosa da notare è che, a differenza di iOS, macOS utilizza notazione matematica per definire il relativo sistema di coordinate della finestra. Pertanto, il punto di origine è nell'angolo inferiore sinistro della finestra, con l'aumento di valori a destra e verso l'angolo superiore destro della finestra. Quando si crea il nuovo `NSButton`, abbiamo prendere in considerazione questa come è possibile definire la posizione e dimensioni sullo schermo.

Il `AutoresizingMask = NSViewResizingMask.MinYMargin` proprietà indica il pulsante che si desidera rimanere nello stesso percorso dalla parte superiore della finestra quando la finestra viene ridimensionata in senso verticale. Nuovamente, questa operazione è necessaria perché (0,0) è in basso a sinistra della finestra.

Infine, il `ContentView.AddSubview (ClickMeButton)` metodo aggiunge il `NSButton` per la visualizzazione contenuto in modo che non verrà visualizzata nella schermata quando l'applicazione è in esecuzione e la finestra visualizzata.

Successivamente viene aggiunta un'etichetta per la finestra che verrà visualizzato il numero di volte che il `NSButton` è stato fatto clic: 

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

Poiché non dispone di uno specifico macOS _etichetta_ elemento dell'interfaccia utente, è stato aggiunto un stile in modo speciale, non modificabile `NSTextField` come un'etichetta. Analogamente a pulsante prima, la dimensione e la posizione tiene conto che (0,0) è in basso a sinistra della finestra. Il `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin` proprietà utilizza il **o** operatore per combinare due `NSViewResizingMask` funzionalità. In questo modo l'etichetta rimanere nello stesso percorso dalla parte superiore della finestra quando la finestra viene ridimensionata in senso verticale e compatta e aumento delle dimensioni in larghezza, come la finestra viene ridimensionata in senso orizzontale.

Nuovamente, il `ContentView.AddSubview (ClickMeLabel)` metodo aggiunge il `NSTextField` per la visualizzazione contenuto in modo che non verrà visualizzata nella schermata quando viene eseguita l'applicazione e aprire la finestra.


### <a name="adjusting-the-window-controller"></a>Modificare il controller di finestra

Poiché la progettazione del `MainWindow` non viene caricato da un file .storyboard o .xib, sarà necessario apportare alcune modifiche al controller di finestra. Modificare il **MainWindowController.cs** file e renderlo simile al seguente:

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

In primo luogo, è possibile definire una nuova istanza di `MainWindow` classe e assegnarle il controller di base finestra `Window` proprietà:

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

È possibile definire la posizione della finestra della schermata con un `CGRect`. Come il sistema di coordinate della finestra, la schermata definisce (0,0) come l'angolo inferiore sinistro. Successivamente, è possibile definire lo stile della finestra utilizzando il **o** operatore per combinare due o più `NSWindowStyle` funzionalità:

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
``` 

Le operazioni seguenti `NSWindowStyle` funzionalità sono disponibili:

- **Senza bordi** -finestra non disporrà di alcun bordo.
- **Titolo** -finestra avrà una barra del titolo.
- **Essere chiusa** -la finestra dispone di un pulsante Chiudi e può essere chiuso.
- **Miniaturizable** -la finestra dispone di un pulsante Miniaturize e può essere ridotto a icona.
- **Ridimensionabile** -finestra avrà un pulsante di ridimensionamento ed essere ridimensionati.
- **Utilità** -la finestra è una finestra di tipo utilità (riquadro).
- **DocModal** -se la finestra è un pannello, sarà documento modale anziché modale di sistema. 
- **NonactivatingPanel** -se la finestra è un pannello, non diventa la finestra principale.
- **TexturedBackground** -finestra avrà uno sfondo con trama.
- **Non in scala** -finestra non verrà ridimensionata.
- **UnifiedTitleAndToolbar** -aree di titolo e la barra degli strumenti della finestra verranno aggiunto.
- **Hud** -verrà visualizzata la finestra come una visualizzazione preliminare pannello.
- **FullScreenWindow** -finestra è possibile immettere la modalità schermo intero.
- **FullSizeContentView** -visualizzazione contenuto della finestra si trova dietro il titolo e l'Area della barra degli strumenti.

Le ultime due proprietà definiscono la _tipo Buffering_ per la finestra e se verrà rinviata disegno della finestra. Per ulteriori informazioni su `NSWindows`, vedere Apple [Introduzione a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) documentazione.

Infine, poiché la finestra non viene ingrandita da un file .storyboard o .xib, è necessario simulare in nostro **MainWindowController.cs** chiamando windows `AwakeFromNib` metodo:

```csharp
Window.AwakeFromNib ();
```

Ciò permetterà di codice per la finestra solo come una finestra standard caricata da un file .storyboard o .xib.


### <a name="displaying-the-window"></a>La visualizzazione della finestra

Con il file .storyboard o .xib rimosso e **MainWindow.cs** e **MainWindowController.cs** file modificati, sarà possibile usare la finestra come per qualsiasi finestra normale che è stata creata in Generatore del Xcode di interfaccia con un file .xib.

Di seguito verrà creare una nuova istanza della finestra e il controller e visualizzare la finestra sullo schermo:

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

A questo punto, se viene eseguita l'applicazione e un paio di volte fatto clic sul pulsante, di seguito verrà visualizzato:

![Eseguire un'app di esempio](xibless-ui-images/run01.png "eseguire un'app di esempio")


## <a name="adding-a-code-only-window"></a>Aggiunta di una finestra del solo codice

Se si desidera aggiungere un solo codice, xibless finestra a un'applicazione Xamarin.Mac esistente, fare doppio clic sul progetto nel **soluzione riempimento** e selezionare **Aggiungi** > **nuovo File...** . Nel **nuovo File** finestra di dialogo scegliere **Xamarin.Mac** > **Cocoa finestra con Controller**, come illustrato di seguito:

![Aggiunge un nuovo controller di finestra](xibless-ui-images/add01.png "aggiunge un nuovo controller di finestra") 

Esattamente come in precedenza, il file predefinito .storyboard o .xib verranno eliminate dal progetto (in questo caso **SecondWindow.xib**) e seguire i passaggi di [cambio di una finestra per l'utilizzo di codice](#Switching_a_Window_to_use_Code) sezione precedente per coprire il definizione della finestra di codice.


## <a name="adding-a-ui-element-to-a-window-in-code"></a>Aggiunta di un elemento dell'interfaccia utente a una finestra del codice

Se una finestra è stata creata nel codice o caricata da un file .storyboard o .xib, potrebbero esserci volte in cui si desidera aggiungere un elemento dell'interfaccia utente di una finestra dal codice. Ad esempio:

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

Il codice precedente crea un nuovo `NSButton` e lo aggiunge al `MyWindow` istanza finestra per la visualizzazione. Qualsiasi elemento dell'interfaccia utente che può essere definito in Generatore del Xcode di interfaccia in un file .storyboard o .xib fondamentalmente può essere creato nel codice e visualizzato in una finestra.


## <a name="defining-the-menu-bar-in-code"></a>Definire la barra dei menu nel codice

A causa delle limitazioni correnti in Xamarin.Mac, non è consigliabile creare barra dei Menu: dell'applicazione Xamarin.Mac`NSMenuBar`: nel codice, ma continuare a usare il **Main** o **MainMenu.xib** file di definizione. Ciò premesso, è possibile aggiungere e rimuovere i menu e voci di Menu nel codice c#.

Ad esempio, modificare il **appdelegate. cs** file e verificare il `DidFinishLaunching` metodo aspetto simile al seguente:

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

Il precedente crea un menu della barra di stato dal codice e lo visualizza quando viene avviata l'applicazione. Per ulteriori informazioni sull'utilizzo dei menu, vedere il nostro [menu](~/mac/user-interface/menu.md) documentazione.


## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato dettagliate sulla creazione di un Xamarin.Mac interfaccia utente nel codice c# rispetto all'utilizzo interfaccia generatore di Xcode con .storyboard o .xib file.



## <a name="related-links"></a>Collegamenti correlati

- [MacXibless (esempio)](https://developer.xamarin.com/samples/mac/MacXibless/)
- [Windows](~/mac/user-interface/window.md)
- [Menu](~/mac/user-interface/menu.md)
- [linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Introduzione a Windows](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
