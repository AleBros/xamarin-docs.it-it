---
title: Compilazione di moderne macOS App
description: "Questo articolo vengono descritti alcuni suggerimenti, funzionalità e tecniche di che uno sviluppatore può utilizzare per compilare un'app moderne macOS in Xamarin.Mac."
ms.topic: article
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 446db5c04849ac6fa320f3fe3b7e22b3d10bf9cf
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="building-modern-macos-apps"></a>Compilazione di moderne macOS App

_Questo articolo vengono descritti alcuni suggerimenti, funzionalità e tecniche di che uno sviluppatore può utilizzare per compilare un'app moderne macOS in Xamarin.Mac._

<a name="Building-Modern-Looks-with-Modern-Views" />

## <a name="building-modern-looks-with-modern-views"></a>La compilazione ha un aspetto moderno con visualizzazioni moderne

Un aspetto moderno includerà un aspetto moderno finestra e barra degli strumenti, ad esempio l'applicazione di esempio illustrato di seguito:

[![](modern-cocoa-apps-images/content08.png "Un esempio di una moderna app Mac dell'interfaccia utente")](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views" />

### <a name="enabling-full-sized-content-views"></a>Abilitazione Full dimensioni viste contenute

Per ottenere questo aspetto in un'app Xamarin.Mac, lo sviluppatore sarà necessario utilizzare un _completo ridimensionare la visualizzazione contenuto_, vale a dire il contenuto si estende nelle aree dello strumento e barra del titolo e sarà possibile sfocatura automaticamente da macOS.

Per abilitare questa funzionalità nel codice, creare una classe personalizzata per il `NSWindowController` e renderlo simile al seguente:

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

Questa funzionalità può inoltre essere abilitata in interfaccia generatore del Xcode selezionando la finestra e controllo **dimensioni contenuto la visualizzazione completa**:

[![](modern-cocoa-apps-images/content01.png "La modifica di storyboard principale in Generatore del Xcode di interfaccia")](modern-cocoa-apps-images/content01.png#lightbox)

Quando si usa una visualizzazione di contenuto completa di dimensioni, lo sviluppatore potrebbe essere necessario compensare il contenuto sotto le aree di barra del titolo e lo strumento in modo che il contenuto specifico (ad esempio etichette) non diapositiva sotto di essi.

Per complicare questo problema, le aree di titolo e la barra degli strumenti possono avere un'altezza dinamica in base all'azione che l'utente attualmente in esecuzione, la versione di macOS l'utente ha installato e/o hardware Mac è in esecuzione l'app.

Di conseguenza, semplicemente hardcoded di offset durante il layout dell'interfaccia utente non funzioneranno. Lo sviluppatore sarà necessario adottare un approccio dinamico.

Apple ha incluso il [chiave-valore Observable](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes) `ContentLayoutRect` proprietà del `NSWindow` classe per ottenere l'Area del contenuto corrente nel codice. Lo sviluppatore può utilizzare questo valore per posizionare manualmente gli elementi necessari quando viene modificato l'Area del contenuto.

La soluzione migliore consiste nell'utilizzare Layout automatico e le classi di dimensioni per posizionare gli elementi dell'interfaccia utente nel codice o il generatore di interfaccia.

Per posizionare gli elementi dell'interfaccia utente utilizzando le classi di dimensioni e di layout nella visualizzazione Controller dell'app, è possibile utilizzare codice analogo al seguente:

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

Questo codice crea spazio di archiviazione per un vincolo principale che verrà applicato a un'etichetta (`ItemTitle`) per garantire che non ignorato nell'area titolo e la barra degli strumenti:

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

Eseguendo l'override del Controller di visualizzazione `UpdateViewConstraints` (metodo), lo sviluppatore può verificare se è già stato creato il vincolo necessario e crearla se necessario.

Se un nuovo vincolo deve essere compilato il `ContentLayoutGuide` proprietà della finestra di controllo che deve essere vincolato accesso e il cast in un `NSLayoutGuide`:

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

La proprietà TopAnchor del `NSLayoutGuide` è possibile accedere a e, se è disponibile, viene utilizzato per creare un nuovo vincolo con la quantità di offset desiderato e il nuovo vincolo viene reso attivo per applicarlo:

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars" />

### <a name="enabling-streamlined-toolbars"></a>Abilitazione di barre degli strumenti semplificate

Una normale macOS finestra include standard che barra del titolo lungo il bordo superiore della finestra. Se la finestra include anche una barra degli strumenti, verrà visualizzato in questa area della barra del titolo:

[![](modern-cocoa-apps-images/content02.png "Una barra degli strumenti standard di Mac")](modern-cocoa-apps-images/content02.png#lightbox)

Quando si utilizza una semplice barra degli strumenti, l'Area del titolo viene chiusa e la barra degli strumenti Sposta verso l'alto nella posizione della barra del titolo, in linea con il pulsante Chiudi finestra ridotta a icona e Ingrandisci:

[![](modern-cocoa-apps-images/content03.png "Una semplice barra degli strumenti Mac")](modern-cocoa-apps-images/content03.png#lightbox)

È abilitata la barra degli strumenti semplificato eseguendo l'override di `ViewWillAppear` metodo il `NSViewController` e rendendolo aspetto simile alla seguente:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

Questo effetto viene generalmente utilizzato per _applicazioni raccolta immagini_ (app di una finestra), ad esempio mappe, calendario, note e le preferenze di sistema. 

<a name="Using-Accessory-View-Controllers" />

### <a name="using-accessory-view-controllers"></a>Utilizzo di controller di visualizzazione degli accessori

A seconda della progettazione dell'app, lo sviluppatore potrebbe essere necessario anche integrare la barra del titolo area con un Controller di visualizzazione accessori visualizzata di sotto dell'area di barra del titolo o strumenti per fornire sensibile al contesto controlli per l'utente in base all'attività sono attualmente occupato in:

[![](modern-cocoa-apps-images/content04.png "Un esempio accessori View Controller")](modern-cocoa-apps-images/content04.png#lightbox)

Il controller di accessori vista verrà automaticamente Sfocatura e ridimensionato dal sistema senza l'intervento di uno sviluppatore.

Per aggiungere un Controller di visualizzazione di accessori, eseguire le operazioni seguenti:

1. In **Esplora soluzioni** fare doppio clic sul file `Main.storyboard` per aprirlo e modificarlo.
2. Trascinare un **Custom View Controller** nella gerarchia della finestra: 

    [![](modern-cocoa-apps-images/content05.png "Aggiunge un nuovo Controller di visualizzazione personalizzata")](modern-cocoa-apps-images/content05.png#lightbox)
3. Del layout della visualizzazione degli accessori interfaccia utente: 

    [![](modern-cocoa-apps-images/content06.png "La nuova vista di progettazione")](modern-cocoa-apps-images/content06.png#lightbox)
4. Visualizzazione degli accessori come esporre un **presa** e qualsiasi altro **azioni** o **prese** per la relativa interfaccia utente: 

    [![](modern-cocoa-apps-images/content07.png "Aggiunta di uscita obbligatorio")](modern-cocoa-apps-images/content07.png#lightbox)
5. Salvare le modifiche.
6. Tornare a Visual Studio per Mac sincronizzare le modifiche.

Modificare il `NSWindowController` e renderlo simile al seguente:

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

I punti chiave di questo codice sono in cui la visualizzazione è impostata per la visualizzazione personalizzata che è stata definita nel generatore di interfaccia ed esposto come un **presa**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

E `LayoutAttribute` che definisce _dove_ verrà visualizzata la funzione di accesso:

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

Poiché macOS ora completamente localizzata, la `Left` e `Right` `NSLayoutAttribute` proprietà sono state deprecate e deve essere sostituita con `Leading` e `Trailing`.

<a name="Using-Tabbed-Windows" />

### <a name="using-tabbed-windows"></a>Utilizzo di finestre a schede

Inoltre, il sistema macOS potrebbe aggiungere controller di visualizzazione accessori per la finestra dell'app. Ad esempio, per creare finestre a schede in cui diverse di Windows dell'App vengono unite in una finestra virtuale:

[![](modern-cocoa-apps-images/content08.png "Un esempio di una finestra a schede Mac")](modern-cocoa-apps-images/content08.png#lightbox)

In genere, lo sviluppatore dovrà richiedere l'uso di azioni limitate a schede di Windows nelle proprie App Xamarin.Mac, il sistema li gestirà automaticamente come indicato di seguito:

- Windows sarà automaticamente a schede quando il `OrderFront` metodo viene richiamato.
- Windows verrà automaticamente Untabbed quando il `OrderOut` metodo viene richiamato.
- Nel codice di tutte le finestre a schede vengono comunque considerate "visible", tuttavia tutte le schede non in primo piano sono nascoste dal sistema tramite CoreGraphics.
- Utilizzare il `TabbingIdentifier` proprietà `NSWindow` al gruppo di Windows in schede.
- Se si tratta di un `NSDocument` app di base, alcune di queste funzionalità saranno abilitati automaticamente (ad esempio, il pulsante più da aggiungere alla barra degli strumenti della scheda) senza alcuna azione per sviluppatori.
- Non`NSDocument` applicazioni è possono abilitare il pulsante "più" nel gruppo di schede per aggiungere un nuovo documento eseguendo l'override di `GetNewWindowForTab` metodo il `NSWindowsController`.

Unione di tutte le parti, il `AppDelegate` di un'app che desideri usare schede di sistema basato su Windows potrebbe essere simile al seguente:

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

In cui il `NewDocumentNumber` proprietà tiene traccia del numero di nuovi documenti creati e `NewDocument` metodo crea un nuovo documento e lo visualizza.

Il `NSWindowController` come potrebbe apparire quindi:

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

In cui il metodo statico `App` proprietà fornisce un collegamento per ottenere il `AppDelegate`. Il `SetDefaultDocumentTitle` metodo imposta un nuovo titolo documenti in base al numero di nuovi documenti creati.

Il codice seguente, indica macOS che l'app si preferisce utilizzare schede e fornisce una stringa che consente a Windows dell'applicazione di essere raggruppati in schede:

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

E il metodo di override seguente aggiunge un pulsante più a barra della scheda che consente di creare un nuovo documento quando l'utente fa clic su:

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation" />

### <a name="using-core-animation"></a>Utilizzo dell'animazione Core

Animazione di base è un motore di rendering di grafica alto spenta incorporato in macOS. Animazione di base è stato ottimizzato per sfruttare i vantaggi della GPU (Graphics Processing Unit) disponibili in macOS moderno hardware anziché esegue le operazioni di grafica nella CPU, che può rallentare il computer.

Il `CALayer`, fornita dall'animazione Core, che possono essere utilizzati per attività, ad esempio lo scorrimento rapido e semplice e le animazioni. Interfaccia utente di un'app deve essere composta da più sottoviste e livelli per sfruttare appieno i vantaggi di animazione Core.

Oggetto `CALayer` oggetto fornisce diverse proprietà che consentono agli sviluppatori di controllare ciò che viene visualizzato sullo schermo per l'utente, ad esempio:

- `Content` -Può essere un `NSImage` o `CGImage` che fornisce il contenuto del livello.
- `BackgroundColor` -Imposta il colore di sfondo del livello come un `CGColor`
- `BorderWidth` -Imposta lo spessore del bordo.
- `BorderColor` -Imposta il colore del bordo.

Per utilizzare grafica di base nell'interfaccia utente dell'applicazione, deve utilizzare _livello eseguito_ viste, Apple suggerisce che lo sviluppatore deve sempre abilitare nella visualizzazione contenuto della finestra. In questo modo, tutte le visualizzazioni figlio erediteranno automaticamente il backup di livello anche.

Inoltre, Apple suggerisce l'utilizzo di viste stato eseguito il livello rispetto all'aggiunta di un nuovo `CALayer` come un sottolivello perché il sistema consente di gestire automaticamente le impostazioni necessarie (ad esempio quelli richiesti da un Display Retina) diversi.

Livello di esecuzione del backup può essere abilitata impostando il `WantsLayer` di un `NSView` per `true` o all'interno di generatore del Xcode di interfaccia nel **vista effetti controllo** controllando **il livello di animazione principale**:

[![](modern-cocoa-apps-images/content09.png "Il controllo di effetti di visualizzazione")](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers" />

#### <a name="redrawing-views-with-layers"></a>Ogni nuovo disegno di viste con i livelli

Un altro passaggio importante durante l'impostazione di utilizzo delle visualizzazioni di stato eseguito il livello in un'app Xamarin.Mac il `LayerContentsRedrawPolicy` del `NSView` a `OnSetNeedsDisplay` nel `NSViewController`. Ad esempio:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Se lo sviluppatore non impostare questa proprietà, la vista verrà ridisegnata ogni volta che viene modificata l'origine del frame, che non è necessaria per motivi di prestazioni. Con questa proprietà è impostata su `OnSetNeedsDisplay` è necessario impostare manualmente lo sviluppatore `NeedsDisplay` a `true` per forzare il contenuto per ridisegnare, tuttavia.

Quando una vista è contrassegnata come danneggiata, il sistema verifica la `WantsUpdateLayer` proprietà della vista. Se restituisce `true` il `UpdateLayer` metodo viene chiamato, altrimenti il `DrawRect` della visualizzazione viene chiamato per aggiornare il contenuto della visualizzazione.

Apple ha i seguenti suggerimenti per l'aggiornamento di un contenuto viste quando necessario:

- Apple preferisce utilizzando `UpdateLater` su `DrawRect` ogni qualvolta possibile perché fornisce un notevole miglioramento delle prestazioni.
- Utilizzare lo stesso `layer.Contents` per gli elementi dell'interfaccia utente simili.
- Apple preferisce anche allo sviluppatore di creare l'interfaccia utente utilizzando le visualizzazioni standard, ad esempio `NSTextField`, nuovamente ogni volta che è possibile.

Per utilizzare `UpdateLayer`, creare una classe personalizzata per il `NSView` e rendere il codice simile al seguente:

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

## <a name="using-modern-drag-and-drop"></a>Tramite trascinamento moderna

Per offrire un'esperienza moderna di trascinamento della selezione per l'utente, è necessario adottare lo sviluppatore _trascinare floccaggio_ nella propria app trascinare e operazioni di trascinamento. Trascinare Flocking è in cui ogni singolo file o un elemento trascinato inizialmente viene visualizzato come un singolo elemento che branchi (gruppo sotto il cursore con un conteggio del numero di elementi), come l'utente continua l'operazione di trascinamento.

Se l'utente termina l'operazione di trascinamento, i singoli elementi verranno Unflock e restituire nei percorsi originali.

Esempio di codice seguente consente di trascinare floccaggio in una visualizzazione personalizzata:

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

L'effetto le è stata realizzata l'invio di ogni elemento trascinato il `BeginDraggingSession` metodo il `NSView` come elemento separato in una matrice.

Quando si lavora con un `NSTableView` o `NSOutlineView`, utilizzare il `PastboardWriterForRow` metodo la `NSTableViewDataSource` classe per avviare l'operazione di trascinamento:

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

Questo consente allo sviluppatore di specificare un singolo `NSDraggingItem` per ogni elemento nella tabella che viene trascinata anziché il metodo precedente `WriteRowsWith` che scrivere tutte le righe come un unico gruppo tavolo.

Quando si lavora con `NSCollectionViews`, utilizzare nuovamente la `PasteboardWriterForItemAt` metodo anziché il `WriteItemsAt` metodo quando si trascinano inizia.

Lo sviluppatore deve sempre evitare di inserire i file di grandi dimensioni sul tavolo di montaggio. Familiarità con macOS Sierra, _File promesse_ consente allo sviluppatore di inserire riferimenti a concesso file sul tavolo che verrà evaso in un secondo momento quando l'utente termina l'operazione di trascinamento utilizzando il nuovo `NSFilePromiseProvider` e `NSFilePromiseReceiver` classi.

<a name="Using-Modern-Event-Tracking" />

## <a name="using-modern-event-tracking"></a>Utilizzo del rilevamento di eventi più recenti

Per un elemento dell'interfaccia utente (ad esempio un `NSButton`) che è stato aggiunto a un'area del titolo o sulla barra degli strumenti, l'utente deve essere in grado di fare clic sull'elemento e di generare un evento come di consueto (ad esempio la visualizzazione di una finestra popup). Tuttavia, poiché anche l'elemento è nell'area del titolo o sulla barra degli strumenti, l'utente deve essere in grado di fare clic e trascinare l'elemento per spostare anche la finestra.

A questo scopo, nel codice, creare una classe personalizzata per l'elemento (ad esempio `NSButton`) ed eseguire l'override di `MouseDown` evento come indicato di seguito:

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

Questo codice Usa il `TrackEventsMatching` metodo il `NSWindow` che l'elemento dell'interfaccia utente è associato a intercettare il `LeftMouseUp` e `LeftMouseDragged` eventi. Per un `LeftMouseUp` evento, l'elemento dell'interfaccia utente risponde come di consueto. Per il `LeftMouseDragged` evento, l'evento viene passato per il `NSWindow`del `PerformWindowDrag` metodo per spostare la finestra sullo schermo.

La chiamata di `PerformWindowDrag` metodo la `NSWindow` classe offre i vantaggi seguenti:

- Consente infatti di finestra da spostare, anche se l'app è bloccato (ad esempio quando l'elaborazione di un ciclo completo).
- Spazio di commutazione funzionerà come previsto.
- Verrà visualizzata la barra di spazi come di consueto.
- Finestra di allineamento e l'allineamento funzionare normalmente.

<a name="Using-Modern-Container-View-Controls" />

## <a name="using-modern-container-view-controls"></a>Utilizzo di controlli di visualizzazione contenitore moderna

macOS Sierra offre numerosi miglioramenti più recenti per i controlli della visualizzazione contenitore esistente disponibile nella versione precedente del sistema operativo.

<a name="Table View Enhancements" />

## <a name="table-view-enhancements"></a>Miglioramenti alle viste di tabella

Lo sviluppatore deve sempre utilizzare il nuovo `NSView` base versione del contenitore di controlli di visualizzazione, ad esempio `NSTableView`. Ad esempio:

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

In questo modo per le azioni personalizzate riga di tabella da collegare al dato le righe della tabella (ad esempio scorrimento rapido da destra a eliminare la riga). Per abilitare questo comportamento, eseguire l'override di `RowActions` metodo il `NSTableViewDelegate`:

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

Il metodo statico `NSTableViewRowAction.FromStyle` viene utilizzato per creare una nuova azione di riga nella tabella degli stili seguenti:

- `Regular` -Esegue un'azione non distruttive standard, ad esempio modifica il contenuto della riga.
- `Destructive` -Esegue un'azione distruttiva ad esempio elimina la riga dalla tabella. Queste azioni verranno visualizzate con uno sfondo rosso.

<a name="Scroll-View-Enhancements" />

## <a name="scroll-view-enhancements"></a>Miglioramenti alle viste di scorrimento 

Quando si usa una visualizzazione a scorrimento (`NSScrollView`), direttamente o come parte di un altro controllo (ad esempio `NSTableView`), può spostare il contenuto della visualizzazione scorrevole nelle aree titolo e la barra degli strumenti in un'app Xamarin.Mac utilizzando un aspetto moderno e viste.

Di conseguenza, il primo elemento nell'area di contenuto visualizzazione a scorrimento può essere parzialmente nascosto dall'area del titolo e barra degli strumenti.

Per risolvere questo problema, Apple ha aggiunto due nuove proprietà per il `NSScrollView` classe:

- `ContentInsets` -Consente agli sviluppatori di fornire un `NSEdgeInsets` oggetto che definisce l'offset che verrà applicata alla parte superiore della visualizzazione scorrevole.
- `AutomaticallyAdjustsContentInsets` -Se `true` la visualizzazione a scorrimento consente di gestire automaticamente il `ContentInsets` per lo sviluppatore.

Tramite il `ContentInsets` lo sviluppatore può modificare l'inizio della visualizzazione a scorrimento per consentire l'inclusione di accessori, ad esempio:

- Un indicatore di ordinamento, come quella illustrata nell'app di posta elettronica.
- Un campo di ricerca.
- Un pulsante di aggiornamento o aggiornamento.

<a name="Auto-Layout-and-Localization-in-Modern-Apps" />

## <a name="auto-layout-and-localization-in-modern-apps"></a>Layout automatico e la localizzazione di applicazioni moderne

Apple ha incluso diverse tecnologie in Xcode che consentono agli sviluppatori di creare facilmente un'app macOS IDN. Xcode ora consente allo sviluppatore di separare il testo di rivolta all'utente di progettazione dell'interfaccia utente dell'applicazione nei relativi file di Storyboard e offre strumenti per gestire questa separazione, se viene modificata l'interfaccia utente.

Per ulteriori informazioni, vedere Apple [internazionalizzazione e localizzazione Guida](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html).

<a name="Implementing-Base-Internationalization" />

### <a name="implementing-base-internationalization"></a>Implementazione di Base di internazionalizzazione 

Mediante l'implementazione di internazionalizzazione di Base, lo sviluppatore può specificare un unico file di Storyboard per rappresentare l'interfaccia utente dell'applicazione e separare tutte le stringhe rivolta all'utente. 

Quando lo sviluppatore sta creando il file Storyboard iniziale (o file) che definiscono l'interfaccia utente dell'applicazione, verranno generati in internazionalizzazione di Base (la lingua che lo sviluppatore pronuncia).

Successivamente, lo sviluppatore può esportare processi e le stringhe di internazionalizzazione di Base (nella progettazione dell'interfaccia utente Storyboard) che possono essere convertite in più lingue.

In un secondo momento, questi processi possono essere importati e Xcode genera i file di stringhe specifiche della lingua per lo Storyboard.

<a name="Implementing-Auto-Layout-to-Support-Localization" />

### <a name="implementing-auto-layout-to-support-localization"></a>Implementazione di Layout automatico per supportare la localizzazione

Poiché le versioni localizzate di stringa di valori possono avere dimensioni diverse e/o direzione di lettura, lo sviluppatore deve utilizzare Auto Layout per posizionare e ridimensionare l'interfaccia utente dell'applicazione in un file di Storyboard.

Apple vengono indicate le operazioni seguenti:

- **Rimuovere i vincoli di larghezza fissa** -tutte le viste basate su testo devono essere consentite ridimensionare in base al contenuto. A larghezza fissa vista potrebbe ritagliare il contenuto in lingue specifiche.
- **Utilizzare dimensioni contenuto intrinseco** - predefinita basata su testo viste verranno Ridimensiona per adattarsi al contenuto. Per una vista basata su testo che non sono ridimensionamento correttamente, selezionarli in interfaccia generatore del Xcode, quindi scegliere **modifica** > **dimensioni in base al contenuto**.
- **Applicare iniziali e finali attributi** , perché è possibile modificare la direzione del testo in base alla lingua dell'utente, utilizzare il nuovo `Leading` e `Trailing` attributi vincolo anziché esistente `Right` e `Left` attributi. `Leading` e `Trailing` regolerà automaticamente in base alla direzione di lingue.
- **Viste di pin da visualizzazioni adiacenti** -in questo modo le viste riposizionare e ridimensionare come le viste intorno ad essi cambiano in risposta alla lingua selezionata.
- **Non impostare un valore minimo di Windows e/o le dimensioni massime dei** -Consenti Windows per modificare le dimensioni come la lingua selezionata ridimensiona le aree di contenuto.
- **Test costantemente le modifiche di Layout** - durante lo sviluppo in app deve essere testato costantemente in diverse lingue. Vedere Apple [test IDN Your app](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1) documentazione per maggiori dettagli.
- **Utilizzare NSStackViews per Pin viste insieme**  -  `NSStackViews` consente di ridimensionare il relativo contenuto in tenere premuto MAIUSC e aumento delle dimensioni in modo prevedibile e il contenuto in base alla lingua selezionata.

<a name="Localizing-in-Xcodes-Interface-Builder" />

### <a name="localizing-in-xcodes-interface-builder"></a>Localizzazione in Generatore di interfaccia del Xcode

Apple fornisce diverse funzionalità in interfaccia generatore del Xcode che lo sviluppatore può utilizzare durante la progettazione o la modifica dell'interfaccia utente di un'app per supportare la localizzazione. Il **direzione del testo** sezione del **controllo attributo** consente allo sviluppatore di fornire suggerimenti in modalità di utilizzo e di aggiornamento in un'istruzione select visualizzazione basata su testo direzione (ad esempio `NSTextField`):

[![](modern-cocoa-apps-images/content10.png "Le opzioni di direzione del testo")](modern-cocoa-apps-images/content10.png#lightbox)

Esistono tre possibili valori per il **direzione del testo**:

- **Naturale** -il layout si basa sulla stringa di cui è assegnata al controllo.
- **Da sinistra a destra** -il layout viene sempre eseguito da sinistra a destra.
- **Da destra a sinistra** -viene sempre eseguito il layout da destra a sinistra.

Esistono due possibili valori per il **Layout**:

- **Da sinistra a destra** -il layout sempre da sinistra a destra.
- **Da destra a sinistra** -il layout è sempre da destra a sinistra.

In genere questi non devono essere modificati a meno che non è richiesto un allineamento specifico.

Il **Mirror** proprietà indica al sistema per riflettere le proprietà del controllo specifici (ad esempio la posizione della cella immagine). Ha tre valori possibili:

- **Automaticamente** -la posizione verrà modificato automaticamente in base alla direzione della lingua selezionata.
- **Nell'interfaccia di destra a sinistra** -la posizione verrà modificata solo in a destra per lingue in base a sinistra.
- **Mai** -la posizione non verrà mai modificato.

Se lo sviluppatore ha specificato **Center**, **giustifica** o **completo** l'allineamento del contenuto di una vista basata su testo, queste non potranno mai essere selezionata una lingua in base a capovolta.

Prima di macOS Sierra, controlli creati nel codice potrebbero non eseguire il mirroring automaticamente. Lo sviluppatore doveva utilizzare codice simile al seguente per gestire il mirroring:

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

In cui il `Alignment` e `ImagePosition` vengono impostate in base il `UserInterfaceLayoutDirection` del controllo.

macOS Sierra aggiunge molti nuovi costruttori di praticità (tramite il metodo statico `CreateButton` metodo) che accettano parametri diversi (ad esempio titolo, immagine e azione) e verrà automaticamente speculare correttamente. Ad esempio:

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances" />

## <a name="using-system-appearances"></a>Utilizzando gli aspetti del sistema

App moderne macOS può adottare un nuovo aspetto dell'interfaccia scuro che funziona per le applicazioni di creazione, modifica o una presentazione di immagine:

[![](modern-cocoa-apps-images/content11.png "Un esempio di un'interfaccia utente finestra Mac scuro")](modern-cocoa-apps-images/content11.png#lightbox)

Questa operazione può essere eseguita mediante l'aggiunta di una riga di codice prima che la finestra viene visualizzata. Ad esempio:

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

Il metodo statico `GetAppearance` metodo il `NSAppearance` classe viene utilizzata per ottenere un aspetto denominato dal sistema (in questo caso `NSAppearance.NameVibrantDark`).

Apple ha i seguenti suggerimenti per l'utilizzo di aspetti del sistema:

- Preferire i colori denominati valori hardcoded (ad esempio `LabelColor` e `SelectedControlColor`).
- Dove possibile, utilizzare lo stile di controllo standard di sistema.

Un'applicazione di Mac OS che utilizza gli aspetti del sistema automaticamente funzionerà correttamente per gli utenti che hanno attivato la funzionalità di accessibilità da app preferenze di sistema. Di conseguenza, Apple suggerisce che lo sviluppatore deve utilizzare sempre gli aspetti di sistema nelle proprie App macOS.

<a name="Designing-UIs-with-Storyboards" />

## <a name="designing-uis-with-storyboards"></a>Progettazione di interfacce utente con storyboard

Gli storyboard consentono allo sviluppatore di non solo i singoli elementi che costituiscono di un'app interfaccia utente, ma per visualizzare e progettare l'interfaccia utente del flusso di progettazione e la gerarchia degli elementi specificati.

Controller consentire allo sviluppatore di raccolta di elementi in un'unità di composizione e Segues astratta e rimuovere il tipico "associare codice" necessario per spostare in tutta la gerarchia di visualizzazione:

[![](modern-cocoa-apps-images/content12.png "L'interfaccia utente in Generatore di interfaccia del Xcode di modifica")](modern-cocoa-apps-images/content12.png#lightbox)

Per ulteriori informazioni, vedere il nostro [Introduzione agli storyboard](~/mac/platform/storyboards/index.md) documentazione.

Sono presenti molte istanze in cui una determinata scena definita in uno storyboard richiedono dati da una scena precedente nella gerarchia della visualizzazione. Apple ha i seguenti suggerimenti per passare le informazioni tra le scene:

- Dipendenze di dati devono sempre catena verso il basso attraverso la gerarchia.
- Evitare dipendenze strutturale dell'interfaccia utente di hardcoded, perché questo limita la flessibilità dell'interfaccia utente.
- Utilizzare le interfacce in c# per fornire dipendenze di dati generici.

Il Controller di visualizzazione che funge da origine di Segue, è possibile ignorare il `PrepareForSegue` metodo ed effettuare qualsiasi inizializzazione prima del Segue necessario (ad esempio, il passaggio di dati) viene eseguita per visualizzare il Controller di vista di destinazione. Ad esempio:

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

Per ulteriori informazioni, vedere il nostro [Segues](~/mac/platform/storyboards/indepth.md#Segues) documentazione.

<a name="Propagating-Actions" />

## <a name="propagating-actions"></a>Azioni di propagazione

In base alla struttura dell'app macOS, potrebbero esserci volte quando il gestore migliore per un'azione su un controllo dell'interfaccia utente potrebbe essere in un' posizione nella gerarchia dell'interfaccia utente. Questo vale in genere di menu e voci di Menu che in tempo reale i propri scena, separata dal resto dell'interfaccia utente dell'applicazione.

Per gestire questa situazione, lo sviluppatore può creare un'azione personalizzata e passare l'azione fino alla catena risponditore. Per ulteriori informazioni, vedere il nostro [utilizzo di azioni della finestra personalizzato](~/mac/user-interface/menu.md) documentazione.

<a name="Modern-Mac-Features" />

## <a name="modern-mac-features"></a>Funzionalità Mac moderna

Apple include diverse funzionalità per utenti in macOS Sierra che consentono agli sviluppatori di sfruttare al meglio la piattaforma Mac, ad esempio:

- **NSUserActivity** -in questo modo l'app descrivere l'attività che l'utente è attualmente interessato. `NSUserActivity` è stato inizialmente creato per supportare uniforme, in cui un'attività avviata in uno dei dispositivi dell'utente potrebbe essere prelevata e continua in un altro dispositivo. `NSUserActivity` funziona in macOS come avviene in iOS così consigliabile il nostro [Introduzione a Handoff](~/ios/platform/handoff.md) documentazione iOS per maggiori dettagli.
- **Siri su Mac** -Siri Usa l'attività corrente (`NSUserActivity`) per fornire un contesto per i comandi di un utente può eseguire.
- **Stato ripristino** : quando l'utente di chiusura di un'app su macOS e successivamente, verrà eseguito il app relaunches automaticamente restituito lo stato precedente. Lo sviluppatore può utilizzare l'API di ripristino dello stato per codificare e il ripristino di stati dell'interfaccia utente temporanei prima che venga visualizzata l'interfaccia utente per l'utente. Se l'app è `NSDocument` base, il ripristino dello stato viene gestito automaticamente. Per abilitare il ripristino dello stato per non`NSDocument` applicazioni, impostare il `Restorable` del `NSWindow` classe `true`.
- **Documenti nel Cloud** -prima macOS Sierra, un'app era necessario in modo esplicito acconsentire esplicitamente all'utilizzo di documenti in iCloud dell'utente unità. In macOS del Sierra l'utente **Desktop** e **documenti** cartelle possono essere sincronizzate con le unità iCloud automaticamente dal sistema. Di conseguenza, le copie locali dei documenti potrebbero essere eliminate per liberare spazio sul computer dell'utente. `NSDocument` applicazioni gestirà automaticamente questa modifica. Tutti gli altri tipi di app saranno necessario utilizzare un `NSFileCoordinator` per la sincronizzazione di lettura e scrittura di documenti.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati alcuni suggerimenti, funzionalità e tecniche di che uno sviluppatore può utilizzare per compilare un'app moderne macOS in Xamarin.Mac.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di macOS](https://developer.xamarin.com/samples/mac/)
