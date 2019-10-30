---
title: Creazione di app macOS moderne
description: Questo articolo illustra diversi suggerimenti, funzionalità e tecniche che uno sviluppatore può usare per creare un'app macOS moderna in Novell. Mac.
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 0ddf6cfc26e811505a50c2d89596f830658f0c1d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029897"
---
# <a name="building-modern-macos-apps"></a>Creazione di app macOS moderne

_Questo articolo illustra diversi suggerimenti, funzionalità e tecniche che uno sviluppatore può usare per creare un'app macOS moderna in Novell. Mac._

<a name="Building-Modern-Looks-with-Modern-Views" />

## <a name="building-modern-looks-with-modern-views"></a>Creazione di look moderni con visualizzazioni moderne

Un aspetto moderno includerà un aspetto moderno della finestra e della barra degli strumenti, ad esempio l'app di esempio illustrata di seguito:

[![](modern-cocoa-apps-images/content08.png "An example of a modern Mac app UI")](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views" />

### <a name="enabling-full-sized-content-views"></a>Abilitazione di visualizzazioni di contenuto con dimensioni complete

Per ottenere questo risultato in un'app Novell. Mac, lo sviluppatore vuole usare una visualizzazione del _contenuto con dimensioni complete_, ovvero il contenuto si estende sotto le aree dello strumento e della barra del titolo e verrà offuscato automaticamente da MacOS.

Per abilitare questa funzionalità nel codice, creare una classe personalizzata per il `NSWindowController` e renderla simile alla seguente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Set window to use Full Size Content View
            Window.StyleMask = NSWindowStyle.FullSizeContentView;
        }
        #endregion
    }
}
```

Questa funzionalità può essere abilitata anche nella Interface Builder di Xcode selezionando la finestra e controllando la **visualizzazione del contenuto con dimensioni complete**:

[![](modern-cocoa-apps-images/content01.png "Editing the main storyboard in Xcode's Interface Builder")](modern-cocoa-apps-images/content01.png#lightbox)

Quando si usa una visualizzazione del contenuto con dimensioni complete, lo sviluppatore potrebbe dover sfalsare il contenuto sotto le aree del titolo e della barra degli strumenti in modo che il contenuto specifico (ad esempio le etichette) non scivoli sotto di essi.

Per complicare questo problema, le aree del titolo e della barra degli strumenti possono avere un'altezza dinamica basata sull'azione che l'utente sta attualmente eseguendo, sulla versione di macOS installata dall'utente e/o sull'hardware Mac su cui è in esecuzione l'app.

Di conseguenza, è sufficiente codificare l'offset quando il layout dell'interfaccia utente non funziona. Lo sviluppatore dovrà adottare un approccio dinamico.

Apple ha incluso la proprietà [chiave-valore osservabile](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes) `ContentLayoutRect` della classe `NSWindow` per ottenere l'area di contenuto corrente nel codice. Lo sviluppatore può usare questo valore per posizionare manualmente gli elementi necessari quando viene modificata l'area di contenuto.

La soluzione migliore consiste nell'usare il layout automatico e le classi di dimensioni per posizionare gli elementi dell'interfaccia utente in codice o in Interface Builder.

Il codice come l'esempio seguente può essere usato per posizionare gli elementi dell'interfaccia utente usando il layout automatico e le classi di dimensioni nel controller di visualizzazione dell'app:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        #region Computed Properties
        public NSLayoutConstraint topConstraint { get; set; }
        #endregion

        ...

        #region Override Methods
        public override void UpdateViewConstraints ()
        {
            // Has the constraint already been set?
            if (topConstraint == null) {
                // Get the top anchor point
                var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
                var topAnchor = contentLayoutGuide.TopAnchor;

                // Found?
                if (topAnchor != null) {
                    // Assemble constraint and activate it
                    topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
                    topConstraint.Active = true;
                }
            }

            base.UpdateViewConstraints ();
        }
        #endregion
    }
}
```

Questo codice crea spazio di archiviazione per un vincolo top che verrà applicato a un'etichetta (`ItemTitle`) per assicurarsi che non scivoli sotto l'area del titolo e della barra degli strumenti:

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

Eseguendo l'override del metodo `UpdateViewConstraints` del controller di visualizzazione, lo sviluppatore può testare per verificare se il vincolo necessario è già stato compilato e crearlo, se necessario.

Se è necessario compilare un nuovo vincolo, il `ContentLayoutGuide` proprietà della finestra il controllo che deve essere vincolato viene eseguito l'accesso e il cast in un `NSLayoutGuide`:

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

Viene eseguito l'accesso alla proprietà di ancoraggio del `NSLayoutGuide` e, se disponibile, viene usata per compilare un nuovo vincolo con la quantità di offset desiderata e il nuovo vincolo viene reso attivo per l'applicazione:

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars" />

### <a name="enabling-streamlined-toolbars"></a>Abilitazione di barre degli strumenti semplificate

Una normale finestra macOS include una barra del titolo standard in esecuzioni lungo il bordo superiore della finestra. Se la finestra include anche una barra degli strumenti, verrà visualizzata in questa area della barra del titolo:

[![](modern-cocoa-apps-images/content02.png "A standard Mac Toolbar")](modern-cocoa-apps-images/content02.png#lightbox)

Quando si usa una barra degli strumenti semplificata, l'area del titolo scompare e la barra degli strumenti viene spostata nella posizione della barra del titolo, in linea con i pulsanti Chiudi, Riduci a icona e Ingrandisci della finestra:

[![](modern-cocoa-apps-images/content03.png "A streamlined Mac Toolbar")](modern-cocoa-apps-images/content03.png#lightbox)

La barra degli strumenti semplificata viene abilitata eseguendo l'override del `ViewWillAppear` metodo del `NSViewController` e rendendola simile alla seguente:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

Questo effetto viene in genere usato per _le applicazioni_ di tipo scarpe (una finestra) come mappe, calendari, note e preferenze di sistema. 

<a name="Using-Accessory-View-Controllers" />

### <a name="using-accessory-view-controllers"></a>Uso di controller di visualizzazione accessori

A seconda della progettazione dell'app, lo sviluppatore potrebbe anche voler integrare l'area della barra del titolo con un controller di visualizzazione accessorio visualizzato immediatamente sotto l'area del titolo o della barra degli strumenti per fornire controlli sensibili al contesto per l'utente in base all'attività attualmente impegnato in:

[![](modern-cocoa-apps-images/content04.png "An example Accessory View Controller")](modern-cocoa-apps-images/content04.png#lightbox)

Il controller di visualizzazione degli accessori verrà automaticamente sfocato e ridimensionato dal sistema senza l'intervento dello sviluppatore.

Per aggiungere un controller di visualizzazione accessori, procedere come segue:

1. In **Esplora soluzioni** fare doppio clic sul file `Main.storyboard` per aprirlo e modificarlo.
2. Trascinare un **controller di visualizzazione personalizzato** nella gerarchia della finestra: 

    [![](modern-cocoa-apps-images/content05.png "Adding a new Custom View Controller")](modern-cocoa-apps-images/content05.png#lightbox)
3. Layout dell'interfaccia utente della visualizzazione accessoria: 

    [![](modern-cocoa-apps-images/content06.png "Designing the new view")](modern-cocoa-apps-images/content06.png#lightbox)
4. Esporre la visualizzazione accessoria come punto di **uscita** e qualsiasi altra **azione** o **Outlet** per la relativa interfaccia utente: 

    [![](modern-cocoa-apps-images/content07.png "Adding the required OUtlet")](modern-cocoa-apps-images/content07.png#lightbox)
5. Salvare le modifiche.
6. Tornare a Visual Studio per Mac per sincronizzare le modifiche.

Modificare il `NSWindowController` e fare in modo che l'aspetto sia simile al seguente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }
        #endregion
    }
}
```

I punti chiave di questo codice sono la posizione in cui la vista è impostata sulla visualizzazione personalizzata definita in Interface Builder ed esposta come **uscita**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

E la `LayoutAttribute` che definisce la _posizione in cui_ verrà visualizzato l'accessorio:

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

Poiché macOS è ora completamente localizzato, le proprietà `Left` e `Right` `NSLayoutAttribute` sono state deprecate e devono essere sostituite con `Leading` e `Trailing`.

<a name="Using-Tabbed-Windows" />

### <a name="using-tabbed-windows"></a>Uso di finestre a schede

Inoltre, il sistema macOS potrebbe aggiungere controller di visualizzazione accessori alla finestra dell'app. Ad esempio, per creare finestre a schede in cui molte delle finestre dell'app vengono unite in un'unica finestra virtuale:

[![](modern-cocoa-apps-images/content08.png "An example of a tabbed Mac Window")](modern-cocoa-apps-images/content08.png#lightbox)

In genere, lo sviluppatore dovrà eseguire un'azione limitata usando le finestre a schede nelle app Novell. Mac, che verranno gestite automaticamente dal sistema nel modo seguente:

- Windows verrà automaticamente a schede quando viene richiamato il metodo `OrderFront`.
- Quando viene richiamato il metodo `OrderOut`, verrà automaticamente eseguita la tabulazione di Windows.
- Nel codice tutte le finestre a schede sono ancora considerate "visibili", tuttavia tutte le schede non più anteriore sono nascoste dal sistema usando CoreGraphics.
- Utilizzare la proprietà `TabbingIdentifier` di `NSWindow` per raggruppare le finestre in tabulazioni.
- Se si tratta di un'app basata su `NSDocument`, molte di queste funzionalità verranno abilitate automaticamente, ad esempio il pulsante più aggiunto alla barra delle schede, senza alcuna azione da parte dello sviluppatore.
- Le app non basate su`NSDocument` possono abilitare il pulsante "più" nel gruppo di schede per aggiungere un nuovo documento eseguendo l'override del metodo `GetNewWindowForTab` della `NSWindowsController`.

Insieme a tutti i componenti, il `AppDelegate` di un'app che voleva usare le finestre a schede basate su sistema potrebbe avere un aspetto simile al seguente:

```csharp
using AppKit;
using Foundation;

namespace MacModern
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewDocumentNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom Actions
        [Export ("newDocument:")]
        public void NewDocument (NSObject sender)
        {
            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow (this);
        } 
        #endregion
    }
}
```

Dove la proprietà `NewDocumentNumber` tiene traccia del numero di nuovi documenti creati e il metodo `NewDocument` crea un nuovo documento e lo Visualizza.

Il `NSWindowController` potrebbe essere simile al seguente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Application Access
        /// <summary>
        /// A helper shortcut to the app delegate.
        /// </summary>
        /// <value>The app.</value>
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void SetDefaultDocumentTitle ()
        {
            // Is this the first document?
            if (App.NewDocumentNumber == 0) {
                // Yes, set title and increment
                Window.Title = "Untitled";
                ++App.NewDocumentNumber;
            } else {
                // No, show title and count
                Window.Title = $"Untitled {App.NewDocumentNumber++}";
            }
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Prefer Tabbed Windows
            Window.TabbingMode = NSWindowTabbingMode.Preferred;
            Window.TabbingIdentifier = "Main";

            // Set default window title
            SetDefaultDocumentTitle ();

            // Set window to use Full Size Content View
            // Window.StyleMask = NSWindowStyle.FullSizeContentView;

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }

        public override void GetNewWindowForTab (NSObject sender)
        {
            // Ask app to open a new document window
            App.NewDocument (this);
        }
        #endregion
    }
}
```

Dove la proprietà `App` statica fornisce un collegamento per ottenere l'`AppDelegate`. Il metodo `SetDefaultDocumentTitle` imposta un nuovo titolo dei documenti in base al numero di nuovi documenti creati.

Il codice seguente indica a macOS che l'app preferisce usare le schede e fornisce una stringa che consente di raggruppare le finestre dell'app in schede:

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

E il metodo di override seguente aggiunge un pulsante più alla barra delle schede che creerà un nuovo documento quando viene fatto clic dall'utente:

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation" />

### <a name="using-core-animation"></a>Uso dell'animazione Core

L'animazione principale è un motore di rendering grafico ad alta tecnologia incorporato in macOS. L'animazione principale è stata ottimizzata per sfruttare i vantaggi della GPU (unità di elaborazione grafica) disponibile nell'hardware macOS moderno anziché eseguire le operazioni grafiche sulla CPU, che può rallentare il computer.

Il `CALayer`, fornito dall'animazione principale, può essere usato per attività quali lo scorrimento rapido e fluido e le animazioni. L'interfaccia utente di un'app deve essere composta da più visualizzazioni e livelli per sfruttare appieno i vantaggi dell'animazione di base.

Un oggetto `CALayer` fornisce diverse proprietà che consentono allo sviluppatore di controllare gli elementi visualizzati sullo schermo per l'utente, ad esempio:

- `Content`: può essere un `NSImage` o `CGImage` che fornisce il contenuto del livello.
- `BackgroundColor`: imposta il colore di sfondo del livello come `CGColor`
- `BorderWidth`-imposta lo spessore del bordo.
- `BorderColor`: imposta il colore del bordo.

Per usare la grafica principale nell'interfaccia utente dell'app, è necessario usare le visualizzazioni _supportate dal livello_ , che Apple suggerisce che lo sviluppatore deve sempre abilitare nella visualizzazione contenuto della finestra. In questo modo, tutte le visualizzazioni figlio erediteranno automaticamente anche il supporto di livelli.

Inoltre, Apple suggerisce di usare le visualizzazioni supportate dal livello anziché aggiungere un nuovo `CALayer` come sottolivello, perché il sistema gestirà automaticamente diverse impostazioni obbligatorie, ad esempio quelle richieste da una visualizzazione retina.

Per abilitare il supporto del livello, è possibile impostare il `WantsLayer` di un `NSView` su `true` o all'interno del Interface Builder di Xcode sotto il controllo **Visualizza effetti** controllando il **livello di animazione principale**:

[![](modern-cocoa-apps-images/content09.png "The View Effects Inspector")](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers" />

#### <a name="redrawing-views-with-layers"></a>Ridisegno di viste con livelli

Un altro passaggio importante quando si usano le visualizzazioni supportate da un livello in un'app Novell. Mac è l'impostazione della `LayerContentsRedrawPolicy` della `NSView` per `OnSetNeedsDisplay` nel `NSViewController`. Esempio:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Se lo sviluppatore non imposta questa proprietà, la vista verrà ridisegnato ogni volta che viene modificata l'origine del frame, che non si desidera per motivi di prestazioni. Se questa proprietà è impostata su `OnSetNeedsDisplay` lo sviluppatore dovrà impostare manualmente `NeedsDisplay` su `true` per forzare il riprogetto del contenuto.

Quando una visualizzazione viene contrassegnata come Dirty, il sistema controlla la proprietà `WantsUpdateLayer` della vista. Se restituisce `true` viene chiamato il metodo `UpdateLayer`, in caso contrario viene chiamato il metodo `DrawRect` della vista per aggiornare il contenuto della visualizzazione.

Apple presenta i suggerimenti seguenti per aggiornare un contenuto delle visualizzazioni quando necessario:

- Apple preferisce usare `UpdateLater` su `DrawRect` quando possibile, poiché offre un miglioramento significativo delle prestazioni.
- Usare lo stesso `layer.Contents` per gli elementi dell'interfaccia utente che hanno un aspetto simile.
- Apple preferisce anche che lo sviluppatore possa comporre la propria interfaccia utente usando visualizzazioni standard, ad esempio `NSTextField`, quando possibile.

Per usare `UpdateLayer`, creare una classe personalizzata per il `NSView` e fare in modo che il codice sia simile al seguente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView
    {
        #region Computed Properties
        public override bool WantsLayer {
            get { return true; }
        }

        public override bool WantsUpdateLayer {
            get { return true; }
        }
        #endregion

        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void DrawRect (CoreGraphics.CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

        }

        public override void UpdateLayer ()
        {
            base.UpdateLayer ();

            // Draw view 
            Layer.BackgroundColor = NSColor.Red.CGColor;
        }
        #endregion
    }
}
```

<a name="Using-Modern-Drag-and-Drop" />

## <a name="using-modern-drag-and-drop"></a>Uso del trascinamento della selezione moderna

Per presentare un'esperienza di trascinamento della selezione per l'utente, lo sviluppatore _deve adottare il trascinamento_ della selezione nelle operazioni di trascinamento della selezione dell'app. Il trascinamento della moltitudine è il punto in cui ogni singolo file o elemento che viene trascinato inizialmente viene visualizzato come un singolo elemento che si raggruppa sotto il cursore con un conteggio del numero di elementi, perché l'utente continua l'operazione di trascinamento.

Se l'utente termina l'operazione di trascinamento, i singoli elementi si alleveranno e torneranno ai percorsi originali.

Il codice di esempio seguente consente di trascinare il floccaggio in una visualizzazione personalizzata:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView, INSDraggingSource, INSDraggingDestination
    {
        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void MouseDragged (NSEvent theEvent)
        {
            // Create group of string to be dragged
            var string1 = new NSDraggingItem ((NSString)"Item 1");
            var string2 = new NSDraggingItem ((NSString)"Item 2");
            var string3 = new NSDraggingItem ((NSString)"Item 3");

            // Drag a cluster of items
            BeginDraggingSession (new [] { string1, string2, string3 }, theEvent, this);
        }
        #endregion
    }
}
```

L'effetto di affollamento è stato ottenuto inviando ogni elemento trascinato al metodo `BeginDraggingSession` della `NSView` come elemento separato in una matrice.

Quando si utilizza un `NSTableView` o `NSOutlineView`, utilizzare il metodo `PastboardWriterForRow` della classe `NSTableViewDataSource` per avviare l'operazione di trascinamento:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDataSource: NSTableViewDataSource
    {
        #region Constructors
        public ContentsTableDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override INSPasteboardWriting GetPasteboardWriterForRow (NSTableView tableView, nint row)
        {
            // Return required pasteboard writer
            ...
            
            // Pasteboard writer failed
            return null;
        }
        #endregion
    }
}
```

Ciò consente allo sviluppatore di fornire un singolo `NSDraggingItem` per ogni elemento della tabella trascinato anziché il metodo precedente `WriteRowsWith` che scrivono tutte le righe come un singolo gruppo nel tavolo di montaggio.

Quando si utilizza `NSCollectionViews`, utilizzare nuovamente il metodo `PasteboardWriterForItemAt` anziché il metodo `WriteItemsAt` al momento del trascinamento.

Lo sviluppatore deve sempre evitare di inserire file di grandi dimensioni nel tavolo di montaggio. Una novità di macOS Sierra, le _promesse dei file_ consentono allo sviluppatore di inserire i riferimenti ai file specificati nel tavolo di montaggio che in un secondo momento verranno soddisfatti quando l'utente completa l'operazione di rilascio usando le nuove classi `NSFilePromiseProvider` e `NSFilePromiseReceiver`.

<a name="Using-Modern-Event-Tracking" />

## <a name="using-modern-event-tracking"></a>Uso del rilevamento degli eventi moderni

Per un elemento dell'interfaccia utente, ad esempio un `NSButton`, che è stato aggiunto a un titolo o a un'area della barra degli strumenti, l'utente deve essere in grado di fare clic sull'elemento e di generare un evento come normale, ad esempio visualizzando una finestra popup. Tuttavia, poiché l'elemento si trova anche nell'area titolo o barra degli strumenti, l'utente deve essere in grado di fare clic e trascinare l'elemento per spostare anche la finestra.

Per eseguire questa operazione nel codice, creare una classe personalizzata per l'elemento, ad esempio `NSButton`, ed eseguire l'override dell'evento `MouseDown` come indicato di seguito:

```csharp
public override void MouseDown (NSEvent theEvent)
{
    var shouldCallSuper = false;

    Window.TrackEventsMatching (NSEventMask.LeftMouseUp, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Handle event as normal
        stop = true;
        shouldCallSuper = true;
    });

    Window.TrackEventsMatching(NSEventMask.LeftMouseDragged, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Pass drag event to window
        stop = true;
        Window.PerformWindowDrag (evt);
    });

    // Call super to handle mousedown
    if (shouldCallSuper) {
        base.MouseDown (theEvent);
    }
}
```

Questo codice usa il metodo `TrackEventsMatching` della `NSWindow` a cui è associato l'elemento dell'interfaccia utente per intercettare gli eventi di `LeftMouseUp` e `LeftMouseDragged`. Per un evento `LeftMouseUp`, l'elemento dell'interfaccia utente risponde normalmente. Per l'evento `LeftMouseDragged`, l'evento viene passato al metodo `PerformWindowDrag` della `NSWindow`per spostare la finestra sullo schermo.

La chiamata al metodo `PerformWindowDrag` della classe `NSWindow` offre i vantaggi seguenti:

- Consente lo spostamento della finestra, anche se l'app è bloccata (ad esempio durante l'elaborazione di un ciclo profondo).
- Il cambio di spazio funzionerà come previsto.
- La barra degli spazi verrà visualizzata normalmente.
- Il blocco e l'allineamento delle finestre funzionano normalmente.

<a name="Using-Modern-Container-View-Controls" />

## <a name="using-modern-container-view-controls"></a>Uso di controlli di visualizzazione contenitore moderni

macOS Sierra offre molti miglioramenti moderni ai controlli di visualizzazione del contenitore esistenti disponibili nella versione precedente del sistema operativo.

<a name="Table View Enhancements" />

## <a name="table-view-enhancements"></a>Miglioramenti di visualizzazione tabella

Lo sviluppatore deve sempre usare la nuova versione basata su `NSView` dei controlli di visualizzazione del contenitore, ad esempio `NSTableView`. Esempio:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }
        #endregion
    }
}
```

In questo modo è possibile associare le azioni della riga di tabella personalizzate alle righe specificate della tabella, ad esempio il diritto di scorrimento rapido per eliminare la riga. Per abilitare questo comportamento, eseguire l'override del metodo `RowActions` del `NSTableViewDelegate`:

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }

        public override NSTableViewRowAction [] RowActions (NSTableView tableView, nint row, NSTableRowActionEdge edge)
        {
            // Take action based on the edge
            if (edge == NSTableRowActionEdge.Trailing) {
                // Create row actions
                var editAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Regular, "Edit", (action, rowNum) => {
                    // Handle row being edited
                    ...
                });

                var deleteAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Destructive, "Delete", (action, rowNum) => {
                    // Handle row being deleted
                    ...
                });

                // Return actions
                return new [] { editAction, deleteAction };
            } else {
                // No matching actions
                return null;
            }
        }
        #endregion
    }
}
```

Il `NSTableViewRowAction.FromStyle` statico viene utilizzato per creare una nuova azione di riga della tabella con gli stili seguenti:

- `Regular`: esegue un'azione standard non distruttiva, ad esempio la modifica del contenuto della riga.
- `Destructive`: esegue un'azione distruttiva, ad esempio l'eliminazione della riga dalla tabella. Queste azioni verranno sottoposte a rendering con uno sfondo rosso.

<a name="Scroll-View-Enhancements" />

## <a name="scroll-view-enhancements"></a>Miglioramenti della visualizzazione a scorrimento 

Quando si usa direttamente una visualizzazione a scorrimento (`NSScrollView`) o come parte di un altro controllo, ad esempio `NSTableView`, il contenuto della visualizzazione a scorrimento può scorrere sotto le aree del titolo e della barra degli strumenti in un'app Novell. Mac usando un aspetto moderno e le visualizzazioni.

Di conseguenza, il primo elemento nell'area del contenuto della visualizzazione a scorrimento può essere parzialmente nascosto dall'area del titolo e della barra degli strumenti.

Per risolvere questo problema, Apple ha aggiunto due nuove proprietà alla classe `NSScrollView`:

- `ContentInsets`: consente allo sviluppatore di fornire un oggetto `NSEdgeInsets` che definisce l'offset che verrà applicato alla parte superiore della visualizzazione a scorrimento.
- `AutomaticallyAdjustsContentInsets`: se `true` la visualizzazione a scorrimento gestirà automaticamente le `ContentInsets` per lo sviluppatore.

Utilizzando il `ContentInsets` lo sviluppatore può modificare l'inizio della visualizzazione a scorrimento per consentire l'inclusione di accessori quali:

- Un indicatore di ordinamento come quello visualizzato nell'app di posta elettronica.
- Un campo di ricerca.
- Pulsante Aggiorna o Aggiorna.

<a name="Auto-Layout-and-Localization-in-Modern-Apps" />

## <a name="auto-layout-and-localization-in-modern-apps"></a>Layout e localizzazione automatici nelle app moderne

Apple ha incluso diverse tecnologie in Xcode che consentono allo sviluppatore di creare facilmente un'app macOS internazionalizzata. Xcode ora consente allo sviluppatore di separare il testo rivolte all'utente dalla progettazione dell'interfaccia utente dell'app nei file dello storyboard e fornisce gli strumenti per mantenere la separazione se l'interfaccia utente cambia.

Per ulteriori informazioni, vedere la Guida all' [internazionalizzazione e alla localizzazione](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)di Apple.

<a name="Implementing-Base-Internationalization" />

### <a name="implementing-base-internationalization"></a>Implementazione dell'internazionalizzazione di base 

Implementando l'internazionalizzazione di base, lo sviluppatore può fornire un singolo file storyboard per rappresentare l'interfaccia utente dell'app e separare tutte le stringhe rivolte all'utente. 

Quando lo sviluppatore crea il file o i file dello storyboard iniziale che definiscono l'interfaccia utente dell'applicazione, verranno compilati nell'internazionalizzazione di base (la lingua parlata dallo sviluppatore).

Lo sviluppatore può quindi esportare le localizzazioni e le stringhe di internazionalizzazione di base (nella progettazione dell'interfaccia utente dello storyboard) che possono essere convertite in più linguaggi.

Successivamente, è possibile importare queste localizzazioni e Xcode genererà i file stringa specifici del linguaggio per lo storyboard.

<a name="Implementing-Auto-Layout-to-Support-Localization" />

### <a name="implementing-auto-layout-to-support-localization"></a>Implementazione del layout automatico per il supporto della localizzazione

Poiché le versioni localizzate dei valori di stringa possono avere dimensioni e/o una direzione di lettura notevolmente diverse, lo sviluppatore deve usare il layout automatico per posizionare e ridimensionare l'interfaccia utente dell'app in un file storyboard.

Apple suggerisce di eseguire le operazioni seguenti:

- **Rimuovi vincoli a larghezza fissa** : è necessario consentire a tutte le visualizzazioni basate su testo di ridimensionarsi in base al contenuto. La visualizzazione a larghezza fissa può ritagliare il contenuto in lingue specifiche.
- **Usare le dimensioni del contenuto intrinseco** : per impostazione predefinita, le visualizzazioni basate su testo vengono ridimensionate automaticamente per adattarsi al contenuto. Per la visualizzazione basata su testo che non è dimensionata correttamente, selezionarla nel Interface Builder di Xcode, quindi scegliere **modifica** **dimensioni > per adattarla al contenuto**.
- **Applicare gli attributi iniziali e finali** : poiché la direzione del testo può variare in base alla lingua dell'utente, utilizzare il nuovo `Leading` e `Trailing` attributi di vincolo anziché gli attributi di `Right` e `Left` esistenti. `Leading` e `Trailing` verranno regolate automaticamente in base alla direzione dei linguaggi.
- **Aggiungi visualizzazioni alle visualizzazioni adiacenti** : consente di riposizionare e ridimensionare le visualizzazioni in base alle modifiche apportate alle visualizzazioni in risposta alla lingua selezionata.
- **Non impostare dimensioni minime e/o massime di Windows** : consente di modificare le dimensioni di Windows perché la lingua selezionata ridimensiona le aree di contenuto.
- **Le modifiche di layout del test costantemente** -durante lo sviluppo all'app devono essere testate costantemente in linguaggi diversi. Per altri dettagli, vedere la documentazione su come [testare la documentazione dell'app internazionalizzata](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1) di Apple.
- **Utilizzare NSStackViews per aggiungere visualizzazioni insieme** - `NSStackViews` consente di spostare il contenuto e di espandersi in modi prevedibili e di modificare le dimensioni del contenuto in base alla lingua selezionata.

<a name="Localizing-in-Xcodes-Interface-Builder" />

### <a name="localizing-in-xcodes-interface-builder"></a>Localizzazione nel Interface Builder di Xcode

Apple ha fornito diverse funzionalità nel Interface Builder di Xcode che lo sviluppatore può usare durante la progettazione o la modifica dell'interfaccia utente di un'app per supportare la localizzazione. La sezione di **direzione del testo** del **controllo attributi** consente allo sviluppatore di fornire suggerimenti sulla modalità di utilizzo e aggiornamento della direzione in una visualizzazione basata su testo selezionata (ad esempio `NSTextField`):

[![](modern-cocoa-apps-images/content10.png "The Text Direction options")](modern-cocoa-apps-images/content10.png#lightbox)

Per la **direzione del testo**sono disponibili tre valori possibili:

- **Natural** : il layout è basato sulla stringa assegnata al controllo.
- **Da sinistra a destra** : il layout viene sempre forzato da sinistra a destra.
- **Da destra a sinistra** : il layout viene sempre forzato da destra a sinistra.

Per il **layout**sono possibili due valori:

- **Da sinistra a destra** : il layout è sempre da sinistra a destra.
- **Da destra a sinistra** : il layout è sempre da destra a sinistra.

In genere questi non devono essere modificati a meno che non sia necessario un allineamento specifico.

La proprietà **mirror** indica al sistema di capovolgere proprietà specifiche del controllo, ad esempio la posizione dell'immagine della cella. Sono disponibili tre possibili valori:

- **Automaticamente** -la posizione verrà modificata automaticamente in base alla direzione della lingua selezionata.
- **Nell'interfaccia da destra a sinistra** : la posizione verrà modificata solo in linguaggi basati su sinistra.
- **Never** : la posizione non viene mai modificata.

Se lo sviluppatore ha specificato il **centro**, la **giustificazione** o l'allineamento **completo** sul contenuto di una visualizzazione basata su testo, queste non verranno mai invertite in base alla lingua selezionata.

Prima di macOS Sierra, i controlli creati nel codice non verrebbero rispecchiati automaticamente. Per gestire il mirroring, lo sviluppatore deve usare codice simile al seguente:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Setting a button's mirroring based on the layout direction
    var button = new NSButton ();
    if (button.UserInterfaceLayoutDirection == NSUserInterfaceLayoutDirection.LeftToRight) {
        button.Alignment = NSTextAlignment.Right;
        button.ImagePosition = NSCellImagePosition.ImageLeft;
    } else {
        button.Alignment = NSTextAlignment.Left;
        button.ImagePosition = NSCellImagePosition.ImageRight;
    }
}
```

Dove vengono impostati `Alignment` e `ImagePosition` in base all'`UserInterfaceLayoutDirection` del controllo.

macOS Sierra aggiunge diversi nuovi costruttori pratici (tramite il metodo di `CreateButton` statico) che accettano diversi parametri (ad esempio titolo, immagine e azione) e verranno automaticamente rispecchiati correttamente. Esempio:

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances" />

## <a name="using-system-appearances"></a>Uso dell'aspetto del sistema

Le app macOS moderne possono adottare un nuovo aspetto di interfaccia scuro che funziona bene per le app per la creazione, la modifica o la presentazione di immagini:

[![](modern-cocoa-apps-images/content11.png "An example of a dark Mac Window UI")](modern-cocoa-apps-images/content11.png#lightbox)

Questa operazione può essere eseguita aggiungendo una riga di codice prima che venga presentata la finestra. Esempio:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        ...
    
        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Apply the Dark Interface Appearance
            View.Window.Appearance = NSAppearance.GetAppearance (NSAppearance.NameVibrantDark);

            ...
        }
        #endregion
    }
}
```

Il metodo `GetAppearance` statico della classe `NSAppearance` viene usato per ottenere un aspetto denominato dal sistema (in questo caso `NSAppearance.NameVibrantDark`).

Apple presenta i suggerimenti seguenti per l'uso dell'aspetto del sistema:

- Preferisci colori denominati su valori hardcoded, ad esempio `LabelColor` e `SelectedControlColor`.
- Utilizzare lo stile del controllo standard del sistema laddove possibile.

Un'app macOS che usa l'aspetto del sistema funzionerà automaticamente per gli utenti che hanno abilitato le funzionalità di accessibilità dall'app Preferenze di sistema. Di conseguenza, Apple suggerisce che lo sviluppatore deve sempre usare l'aspetto del sistema nelle app macOS.

<a name="Designing-UIs-with-Storyboards" />

## <a name="designing-uis-with-storyboards"></a>Progettazione di interfacce utente con storyboard

Gli storyboard consentono allo sviluppatore di progettare non solo i singoli elementi che costituiscono l'interfaccia utente di un'app, ma di visualizzare e progettare il flusso dell'interfaccia utente e la gerarchia degli elementi specificati.

I controller consentono allo sviluppatore di raccogliere gli elementi in un'unità di composizione e gli elementi segue astratta e di rimuovere il tipico "codice Glue" necessario per spostarsi in tutta la gerarchia di visualizzazione:

[![](modern-cocoa-apps-images/content12.png "Editing the UI in Xcode's Interface Builder")](modern-cocoa-apps-images/content12.png#lightbox)

Per ulteriori informazioni, vedere la documentazione [introduttiva per gli storyboard](~/mac/platform/storyboards/index.md) .

Esistono molti casi in cui una determinata scena definita in uno storyboard richiederà i dati di una scena precedente nella gerarchia di visualizzazione. Apple presenta i suggerimenti seguenti per passare le informazioni tra le scene:

- I dati dipendenze devono sempre propagarsi verso il basso attraverso la gerarchia.
- Evitare il hardcoded della dipendenze strutturale dell'interfaccia utente, poiché questo limita la flessibilità dell'interfaccia utente.
- Usare C# le interfacce per fornire dipendenze di dati generici.

Il controller di visualizzazione che funge da origine di segue può eseguire l'override del metodo `PrepareForSegue` ed eseguire qualsiasi inizializzazione richiesta (ad esempio, il passaggio di dati) prima dell'esecuzione di segue per visualizzare il controller di visualizzazione di destinazione. Esempio:

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

Per ulteriori informazioni, consultare la documentazione di [gli elementi segue](~/mac/platform/storyboards/indepth.md#Segues) .

<a name="Propagating-Actions" />

## <a name="propagating-actions"></a>Propagazione di azioni

In base alla progettazione dell'app macOS, a volte il gestore migliore per un'azione in un controllo dell'interfaccia utente potrebbe trovarsi in un'altra posizione nella gerarchia dell'interfaccia utente. Questo è in genere vero per i menu e le voci di menu che risiedono nella propria scena, separati dal resto dell'interfaccia utente dell'app.

Per gestire questa situazione, lo sviluppatore può creare un'azione personalizzata e passare l'azione alla catena di risposta. Per ulteriori informazioni, vedere la documentazione sull' [utilizzo delle azioni della finestra personalizzata](~/mac/user-interface/menu.md) .

<a name="Modern-Mac-Features" />

## <a name="modern-mac-features"></a>Funzionalità Mac moderne

Apple ha incluso diverse funzionalità rivolte agli utenti in macOS Sierra che consentono allo sviluppatore di sfruttare al meglio la piattaforma Mac, ad esempio:

- **NSUserActivity** : consente all'app di descrivere l'attività di cui l'utente è attualmente impegnato. `NSUserActivity` è stato inizialmente creato per supportare la consegna, in cui un'attività avviata su uno dei dispositivi dell'utente può essere prelevata e continuata in un altro dispositivo. `NSUserActivity` funziona allo stesso modo in macOS come in iOS. per altri dettagli, vedere l' [Introduzione alla documentazione di](~/ios/platform/handoff.md) iOS.
- **Siri in Mac** -Siri usa l'attività corrente (`NSUserActivity`) per fornire il contesto ai comandi che un utente può emettere.
- **Ripristino dello stato** : quando l'utente chiude un'app in MacOS e quindi la riavvia in un secondo momento, l'app viene restituita automaticamente allo stato precedente. Lo sviluppatore può usare l'API di ripristino dello stato per codificare e ripristinare gli stati temporanei dell'interfaccia utente prima che l'interfaccia utente venga visualizzata all'utente. Se l'app è basata su `NSDocument`, il ripristino dello stato viene gestito automaticamente. Per abilitare il ripristino dello stato per le app non basate su`NSDocument`, impostare la `Restorable` della classe `NSWindow` su `true`.
- **Documenti nel cloud** -prima di MacOS Sierra, un'app doveva acconsentire esplicitamente all'uso dei documenti nell'unità iCloud dell'utente. In macOS Sierra le cartelle del **Desktop** e dei **documenti** dell'utente possono essere sincronizzate automaticamente con l'unità iCloud dal sistema. Di conseguenza, le copie locali dei documenti possono essere eliminate per liberare spazio nel computer dell'utente. le app basate su `NSDocument` gestiranno automaticamente questa modifica. Tutti gli altri tipi di app dovranno usare un `NSFileCoordinator` per sincronizzare la lettura e la scrittura di documenti.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha trattato diversi suggerimenti, funzionalità e tecniche che uno sviluppatore può usare per creare un'app macOS moderna in Novell. Mac.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di macOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
