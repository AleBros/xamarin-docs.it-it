---
title: Creazione di App macOS moderne
description: Questo articolo descrive alcuni suggerimenti, funzionalità e tecniche di che uno sviluppatore può utilizzare per creare un'app macOS moderne in xamarin. Mac.
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 53bfc9f147b6cf369b8f5ce8d1257dbaf6b0f807
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113521"
---
# <a name="building-modern-macos-apps"></a>Creazione di App macOS moderne

_Questo articolo descrive alcuni suggerimenti, funzionalità e tecniche di che uno sviluppatore può utilizzare per creare un'app macOS moderne in xamarin. Mac._

<a name="Building-Modern-Looks-with-Modern-Views" />

## <a name="building-modern-looks-with-modern-views"></a>La compilazione ha un aspetto moderno con visualizzazioni moderne

Un aspetto rinnovato e moderno includerà un aspetto moderno di finestra e barra degli strumenti, ad esempio l'app di esempio illustrato di seguito:

[![](modern-cocoa-apps-images/content08.png "Un esempio di un'app Mac moderna interfaccia utente")](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views" />

### <a name="enabling-full-sized-content-views"></a>Abilitazione Full dimensioni visualizzazioni del contenuto

Per ottenere questo aspetto in un'app xamarin. Mac, lo sviluppatore dovrà usare un _completo ridimensionare la visualizzazione contenuto_, vale a dire il contenuto si estende nelle aree dello strumento e sulla barra del titolo e sarà sfocata automaticamente da macOS.

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

Questa funzionalità può anche essere attivata in Interface Builder di Xcode selezionando la finestra e controllando **dimensioni contenuto la visualizzazione completa**:

[![](modern-cocoa-apps-images/content01.png "Modifica dello storyboard principale in Interface Builder di Xcode")](modern-cocoa-apps-images/content01.png#lightbox)

Quando si usa una visualizzazione di contenuti completa di dimensioni, lo sviluppatore potrebbe essere necessario eseguire l'offset il contenuto di sotto di aree di barra del titolo e strumento in modo che il contenuto specifico (ad esempio etichette) non far scorrere in essi.

Per complicare questo problema, le aree di titolo e sulla barra degli strumenti possono avere un'altezza dinamica in base all'azione che l'utente attualmente in esecuzione, la versione di macOS installata dall'utente e/o l'hardware Mac che viene eseguita l'app.

Di conseguenza, è sufficiente hardcoded offset durante il layout dell'interfaccia utente non funzionerà. Lo sviluppatore dovrà adottare un approccio dinamico.

Apple ha incluso il [chiave-valore osservabile](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes) `ContentLayoutRect` proprietà del `NSWindow` classe per ottenere l'Area di contenuto corrente nel codice. Lo sviluppatore può utilizzare questo valore per posizionare manualmente gli elementi necessari quando l'Area di contenuto cambia.

La soluzione migliore consiste nell'usare Layout automatico e le classi di dimensioni per posizionare gli elementi dell'interfaccia utente nel codice o Interface Builder.

Codice simile all'esempio seguente può essere utilizzato per posizionare gli elementi dell'interfaccia utente utilizzando le classi di dimensioni e il layout automatico nel Controller di visualizzazione dell'app:

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

Questo codice Crea risorsa di archiviazione per un vincolo principale che verrà applicato a un'etichetta (`ItemTitle`) per garantire che non viene ignorato nell'area del titolo e sulla barra degli strumenti:

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

Eseguendo l'override del Controller di visualizzazione `UpdateViewConstraints` (metodo), lo sviluppatore può eseguire un test per vedere se il vincolo necessario è già stato compilato e crearla se necessario.

Se un nuovo vincolo deve essere compilato, la `ContentLayoutGuide` proprietà della finestra di controllo che deve essere vincolato di accesso e il cast in un `NSLayoutGuide`:

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

La proprietà TopAnchor del `NSLayoutGuide` effettuando l'accesso e se è disponibile, viene usato per creare un nuovo vincolo con la quantità di offset desiderato e il nuovo vincolo viene reso attivo di metterle in pratica:

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars" />

### <a name="enabling-streamlined-toolbars"></a>Abilitazione di barre degli strumenti semplificate

Una normale finestra di macOS include uno standard che barra del titolo è posizionata nella parte e il bordo superiore della finestra. Se la finestra include anche una barra degli strumenti, verrà visualizzato in questa area della barra del titolo:

[![](modern-cocoa-apps-images/content02.png "Una barra degli strumenti standard di Mac")](modern-cocoa-apps-images/content02.png#lightbox)

Quando si usa una semplice barra degli strumenti, viene rimosso l'Area del titolo e la barra degli strumenti Sposta verso l'alto nella posizione della barra del titolo, in linea con i pulsanti Chiudi finestra, Riduci a icona e Ingrandisci:

[![](modern-cocoa-apps-images/content03.png "Una barra degli strumenti Mac semplificata")](modern-cocoa-apps-images/content03.png#lightbox)

La barra degli strumenti semplificata è abilitata per l'override di `ViewWillAppear` metodo del `NSViewController` e rendendolo aspetto analogo al seguente:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

Questo effetto viene generalmente utilizzato per _Shoebox applicazioni_ (app di una finestra), ad esempio mappe, calendario, note e le preferenze di sistema. 

<a name="Using-Accessory-View-Controllers" />

### <a name="using-accessory-view-controllers"></a>Usare i controller di visualizzazione degli accessori

A seconda della progettazione dell'app, lo sviluppatore potrebbe essere necessario anche a complemento di area con un Controller di visualizzazione accessorio che viene visualizzato a destra sotto l'area di barra del titolo/degli strumenti per fornire sensibile al contesto controlla all'utente in base all'attività sono la barra del titolo attualmente interessato da:

[![](modern-cocoa-apps-images/content04.png "Un esempio accessorio View Controller")](modern-cocoa-apps-images/content04.png#lightbox)

Il controller di visualizzazione accessorio verrà automaticamente meno netti e ridimensionato dal sistema senza l'intervento per gli sviluppatori.

Per aggiungere un Controller di visualizzazione di accessori, eseguire le operazioni seguenti:

1. In **Esplora soluzioni** fare doppio clic sul file `Main.storyboard` per aprirlo e modificarlo.
2. Trascinare un **Controller di visualizzazione personalizzata** nella gerarchia della finestra: 

    [![](modern-cocoa-apps-images/content05.png "Aggiunge un nuovo Controller di visualizzazione personalizzata")](modern-cocoa-apps-images/content05.png#lightbox)
3. Del layout della visualizzazione degli accessori interfaccia utente: 

    [![](modern-cocoa-apps-images/content06.png "La nuova visualizzazione di progettazione")](modern-cocoa-apps-images/content06.png#lightbox)
4. Esporre la visualizzazione degli accessori come un **Outlet** e qualsiasi altra **azioni** oppure **Outlet** per visualizzarne l'interfaccia utente: 

    [![](modern-cocoa-apps-images/content07.png "Aggiunta dell'OUtlet obbligatorio")](modern-cocoa-apps-images/content07.png#lightbox)
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

I punti chiave di questo codice sono in cui la visualizzazione è impostata per la visualizzazione personalizzata che è stata definita in Interface Builder ed esposta come un **Outlet**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

E il `LayoutAttribute` che definisce _dove_ verrà visualizzato l'accessorio:

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

Poiché macOS è ora completamente localizzata, il `Left` e `Right` `NSLayoutAttribute` delle proprietà sono state deprecate e deve essere sostituite con `Leading` e `Trailing`.

<a name="Using-Tabbed-Windows" />

### <a name="using-tabbed-windows"></a>Utilizzo di Windows a schede

Inoltre, il sistema di macOS potrebbe aggiungere i controller di visualizzazione accessorio alla finestra dell'app. Ad esempio, per creare Windows a schede in cui diverse di Windows dell'App vengono uniti in un'unica finestra virtuale:

[![](modern-cocoa-apps-images/content08.png "Un esempio di una finestra a schede Mac")](modern-cocoa-apps-images/content08.png#lightbox)

In genere, lo sviluppatore avrà bisogno di richiedere l'uso di azioni limitate a schede di Windows nelle proprie App xamarin. Mac, il sistema li gestirà automaticamente come indicato di seguito:

- Windows sarà automaticamente a schede quando il `OrderFront` metodo viene richiamato.
- Windows verrà automaticamente Untabbed quando il `OrderOut` metodo viene richiamato.
- Nel codice di tutte le finestre a schede sono comunque considerate "visibili", tuttavia tutte le schede non in primo piano sono nascosti per il sistema utilizzando CoreGraphics.
- Usare la `TabbingIdentifier` proprietà di `NSWindow` al gruppo Windows in schede.
- Se si tratta di un `NSDocument` basato su app, alcune di queste funzionalità verrà abilitata automaticamente (ad esempio, il pulsante più da aggiungere alla barra delle schede) senza eseguire alcuna azione per gli sviluppatori.
- Non -`NSDocument` basato su app è possibile abilitare il pulsante "più (+)" nel gruppo di schede per aggiungere un nuovo documento eseguendo l'override di `GetNewWindowForTab` metodo del `NSWindowsController`.

Riunire tutti i componenti, il `AppDelegate` di un'app che è necessaria per l'utilizzo del sistema in base a schede Windows può avere un aspetto simile al seguente:

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

Il `NSWindowController` quindi un aspetto simile:

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

Il codice seguente, indica a macOS preferenziale per l'app per usare le schede e fornisce una stringa che consente a Windows dell'app da raggruppare in schede:

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

E il metodo di override seguente aggiunge un pulsante segno più per la barra di schede che verrà creato un nuovo documento quando l'utente fa clic su:

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation" />

### <a name="using-core-animation"></a>Utilizzo di Core Animation

Animazione di core è un motore di rendering della grafica estrema incorporato in macOS. Animazione di base è stata ottimizzata per sfruttare i vantaggi della GPU (Graphics Processing Unit) disponibili in macOS moderne hardware invece di dover eseguire le operazioni di grafica sulla CPU, che possono rallentare la macchina.

Il `CALayer`, forniti da Core Animation, possono essere utilizzati per attività, ad esempio lo scorrimento veloce e fluido e animazioni. Un'interfaccia utente di app dovrebbe essere composto da più livelli per sfruttare appieno i vantaggi di Core Animation e le visualizzazioni secondarie.

Oggetto `CALayer` oggetto fornisce diverse proprietà che consentono allo sviluppatore di controllare ciò che viene visualizzato sullo schermo all'utente, ad esempio:

- `Content` -Può essere un' `NSImage` o `CGImage` che fornisce il contenuto del livello.
- `BackgroundColor` -Imposta il colore di sfondo del livello come una `CGColor`
- `BorderWidth` : Imposta lo spessore del bordo.
- `BorderColor` -Imposta il colore del bordo.

Per poter usare Core Graphics nell'interfaccia utente dell'app, è necessario usare _sottoposti a livello_ . tali viste suggerite da Apple che lo sviluppatore deve sempre abilitare nella visualizzazione contenuto della finestra. In questo modo, tutte le visualizzazioni figlio erediterà automaticamente il backup di livello anche.

Inoltre, suggerite da Apple usando le viste stato eseguito il livello invece aggiunta una nuova `CALayer` come un sottolivello perché il sistema gestirà automaticamente alcune delle impostazioni necessarie (ad esempio quelli richiesti da un Display Retina).

Livello di backup può essere abilitata impostando il `WantsLayer` di un `NSView` al `true` o all'interno di Interface Builder di Xcode nel **View Effects Inspector** controllando **Core animazione livello**:

[![](modern-cocoa-apps-images/content09.png "Il controllo effetti visualizzazioni")](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers" />

#### <a name="redrawing-views-with-layers"></a>Ridisegno visualizzazioni con i livelli

Un altro passaggio importante durante l'impostazione di utilizzo delle visualizzazioni eseguito il livello in un'app xamarin. Mac la `LayerContentsRedrawPolicy` del `NSView` al `OnSetNeedsDisplay` nel `NSViewController`. Ad esempio:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

Se lo sviluppatore non impostare questa proprietà, la visualizzazione verrà ridisegnata ogni volta che Cambia origine frame, che non si desidera per motivi di prestazioni. Con questa proprietà è impostata su `OnSetNeedsDisplay` è necessario impostare manualmente lo sviluppatore `NeedsDisplay` a `true` per forzare il contenuto di ridisegnare, tuttavia.

Quando una visualizzazione è contrassegnata come dirty, il sistema verifica la `WantsUpdateLayer` proprietà della visualizzazione. Se viene restituito `true` il `UpdateLayer` viene chiamato, altrimenti il `DrawRect` della visualizzazione viene chiamato per aggiornare il contenuto della visualizzazione.

Apple ha i seguenti suggerimenti per l'aggiornamento di un contenuto viste quando necessario:

- Si preferisce Apple usando `UpdateLater` failover `DrawRect` ogniqualvolta possibile perché fornisce un miglioramento significativo delle prestazioni.
- Usare lo stesso `layer.Contents` per gli elementi dell'interfaccia utente simili.
- Apple preferisce anche agli sviluppatori di comporre l'interfaccia utente usando le visualizzazioni standard, ad esempio `NSTextField`, anche in questo caso laddove possibile.

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

## <a name="using-modern-drag-and-drop"></a>Uso della selezione e trascinamento moderne

Per presentare un'esperienza di trascinamento della selezione moderna per l'utente, lo sviluppatore deve adottare _trascinamento uccelli_ nella propria app trascinare e rilasciare le operazioni. Trascinare Flocking è dove ogni singolo file o elemento trascinato inizialmente viene visualizzato come un singolo elemento che branchi (gruppo sotto il cursore con un conteggio del numero di elementi), come l'utente continua l'operazione di trascinamento.

Se l'utente termina l'operazione di trascinamento, i singoli elementi verranno Unflock e restituire la posizione originale.

Esempio di codice seguente consente di trascinare uccelli su una visualizzazione personalizzata:

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

L'effetto le è stato ottenuto mediante l'invio di ogni elemento trascinato per il `BeginDraggingSession` metodo del `NSView` come elemento separato in una matrice.

Quando si lavora con un `NSTableView` o `NSOutlineView`, utilizzare il `PastboardWriterForRow` metodo il `NSTableViewDataSource` classe per avviare l'operazione di trascinamento:

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

Questo consente allo sviluppatore di fornire un individuo `NSDraggingItem` per ogni elemento della tabella che viene trascinata anziché il metodo precedente `WriteRowsWith` che scrivere tutte le righe come un unico gruppo tavolo.

Quando si lavora con `NSCollectionViews`, usare nuovamente il `PasteboardWriterForItemAt` metodo anziché il `WriteItemsAt` metodo quando si trascinano inizia.

Lo sviluppatore deve sempre evitare di inserire file di grandi dimensioni sul tavolo. Familiarità con macOS Sierra, _File promesse_ consente allo sviluppatore di posizionare i riferimenti a specificato i file sul tavolo che verrà soddisfatto in un secondo momento quando l'utente termina l'operazione di rilascio usando le nuove `NSFilePromiseProvider` e `NSFilePromiseReceiver` classi.

<a name="Using-Modern-Event-Tracking" />

## <a name="using-modern-event-tracking"></a>Uso del rilevamento di eventi moderne

Per un elemento dell'interfaccia utente (ad esempio un `NSButton`) che è stato aggiunto a un'area del titolo o sulla barra degli strumenti, l'utente deve essere in grado di fare clic sull'elemento e fare in modo che generare un evento come di consueto (ad esempio visualizzando una finestra popup). Tuttavia, poiché l'elemento è anche nell'area del titolo o sulla barra degli strumenti, l'utente deve essere in grado di fare clic e trascinare l'elemento per spostare anche la finestra.

A tale scopo, nel codice, creare una classe personalizzata per l'elemento (ad esempio `NSButton`) ed eseguire l'override di `MouseDown` evento come indicato di seguito:

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

Questo codice Usa il `TrackEventsMatching` metodo per il `NSWindow` che è associato l'elemento dell'interfaccia utente per intercettare la `LeftMouseUp` e `LeftMouseDragged` eventi. Per un `LeftMouseUp` evento, l'elemento dell'interfaccia utente risponde come di consueto. Per il `LeftMouseDragged` evento, l'evento viene passato per il `NSWindow`del `PerformWindowDrag` metodo per spostare la finestra sullo schermo.

Chiama il `PerformWindowDrag` metodo del `NSWindow` classe offre i vantaggi seguenti:

- Consente la finestra di spostare, anche se l'app è bloccato (ad esempio, quando l'elaborazione di un ciclo completo).
- Lo spazio di commutazione funzionerà come previsto.
- La barra di spazi verrà visualizzata come di consueto.
- Finestra di allineamento e l'allineamento funzionano come di consueto.

<a name="Using-Modern-Container-View-Controls" />

## <a name="using-modern-container-view-controls"></a>Utilizzo dei controlli di visualizzazione contenitore moderne

macOS Sierra fornisce numerosi miglioramenti moderni per i controlli di visualizzazione contenitore esistente disponibile nella versione precedente del sistema operativo.

<a name="Table View Enhancements" />

## <a name="table-view-enhancements"></a>Miglioramenti alle viste di tabella

Lo sviluppatore deve sempre usare il nuovo `NSView` basato su versione di contenitore di controlli di visualizzazione, ad esempio `NSTableView`. Ad esempio:

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

In questo modo per azioni personalizzate di riga nella tabella da allegare al dato righe della tabella (ad esempio scorrendo rapidamente a destra per eliminare la riga). Per abilitare questo comportamento, eseguire l'override di `RowActions` metodo di `NSTableViewDelegate`:

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

Il metodo statico `NSTableViewRowAction.FromStyle` viene usato per creare una nuova azione di riga nella tabella degli stili seguenti:

- `Regular` -Esegue un'azione standard non distruttiva, ad esempio modifica il contenuto della riga.
- `Destructive` -Esegue un'azione distruttiva, ad esempio eliminare la riga dalla tabella. Verranno eseguito il rendering di queste azioni con uno sfondo rosso.

<a name="Scroll-View-Enhancements" />

## <a name="scroll-view-enhancements"></a>Miglioramenti alle viste di scorrimento 

Quando si usa una visualizzazione a scorrimento (`NSScrollView`), direttamente o come parte di un altro controllo (ad esempio `NSTableView`), far scorrere il contenuto della visualizzazione scorrimento sotto le aree di titolo e sulla barra degli strumenti in un'app xamarin. Mac con un aspetto moderno e viste.

Di conseguenza, il primo elemento nell'area di contenuto visualizzazione a scorrimento possa essere parzialmente oscurato da area del titolo e sulla barra degli strumenti.

Per risolvere questo problema, Apple ha aggiunto due nuove proprietà per il `NSScrollView` classe:

- `ContentInsets` -Consente allo sviluppatore di fornire un `NSEdgeInsets` oggetto che definisce l'offset che verrà applicata alla parte superiore della visualizzazione scorrimento.
- `AutomaticallyAdjustsContentInsets` -Se `true` gestirà automaticamente la visualizzazione a scorrimento la `ContentInsets` per gli sviluppatori.

Tramite il `ContentInsets` lo sviluppatore può modificare l'inizio della visualizzazione scorrimento per consentire l'inclusione di accessori, ad esempio:

- Un indicatore di ordinamento, come quella illustrata in app di posta elettronica.
- Un campo di ricerca.
- Un pulsante di aggiornamento o aggiornamento.

<a name="Auto-Layout-and-Localization-in-Modern-Apps" />

## <a name="auto-layout-and-localization-in-modern-apps"></a>Layout automatico e la localizzazione nelle App moderne

Apple ha inserito diverse tecnologie in Xcode che consente allo sviluppatore di creare facilmente un'app macOS internazionalizzati. Xcode ora consente allo sviluppatore di separare il testo rivolta all'utente di progettazione dell'interfaccia utente dell'app nel relativo file Storyboard e fornisce gli strumenti per gestire questa separazione, se l'interfaccia utente cambia.

Per altre informazioni, vedere di Apple [Guida di localizzazione e internazionalizzazione](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html).

<a name="Implementing-Base-Internationalization" />

### <a name="implementing-base-internationalization"></a>Implementazione di Base di internazionalizzazione 

Mediante l'implementazione di internazionalizzazione di Base, lo sviluppatore può specificare un singolo file di Storyboard per rappresentare l'interfaccia utente dell'app e separare tutte le stringhe destinate all'utente. 

Quando lo sviluppatore crea il file Storyboard iniziale (o file) che definiscono l'interfaccia utente dell'app, verranno generati dell'internazionalizzazione di Base, il linguaggio che legge lo sviluppatore.

Lo sviluppatore può esportare localizzazioni e le stringhe di internazionalizzazione di Base (nella progettazione dell'interfaccia utente dello Storyboard) che possono essere convertite in più lingue.

In un secondo momento, è possibile importare questi localizzazioni e Xcode genererà i file di stringhe specifiche della lingua per Storyboard.

<a name="Implementing-Auto-Layout-to-Support-Localization" />

### <a name="implementing-auto-layout-to-support-localization"></a>Implementazione di Layout automatico per supportare la localizzazione

Perché le versioni localizzate di stringa di valori possono avere dimensioni notevolmente diverse e/o la lettura di direzione, lo sviluppatore deve usare Layout automatico per posizionare e ridimensionare l'interfaccia utente dell'app in un file Storyboard.

Apple consiglia le operazioni seguenti:

- **Rimuovere i vincoli di larghezza fissa** -tutte le viste basate su testo devono essere consentite ridimensionare in base al relativo contenuto. A larghezza fissa vista potrebbe ritagliare il contenuto in lingue specifiche.
- **Usare dimensioni contenuto intrinseco** : per impostazione predefinita basata su testo viste verranno ridimensionamento automatico per adattarsi al contenuto. Per visualizzazione basata su testo che non sono di ridimensionamento in modo corretto, selezionarli in Interface Builder di Xcode, quindi scegliere **Edit** > **dimensione in base al contenuto**.
- **Applicare iniziali e finali degli attributi** - perché è possibile modificare la direzione del testo in base la lingua dell'utente, usare il nuovo `Leading` e `Trailing` gli attributi di vincolo anziché esistente `Right` e `Left` attributi. `Leading` e `Trailing` regolerà automaticamente in base alla direzione di lingue.
- **Visualizzazioni di pin da visualizzazioni adiacenti** -in questo modo le viste da riposizionare e ridimensionare man mano che le visualizzazioni attorno a esse cambiano in risposta alla lingua selezionata.
- **Non impostare un valore minimo di Windows e/o dimensioni massime** -Consenti di Windows per modificare le dimensioni quando la lingua selezionata ridimensiona le aree di contenuto.
- **Testare costantemente le modifiche di Layout** - durante lo sviluppo di app deve essere continuamente testato in lingue diverse. Vedere di Apple [Testing Internationalized Your app](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1) informazioni più dettagliate.
- **Usare NSStackViews per Pin viste contemporaneamente**  -  `NSStackViews` consente il relativo contenuto in shift e aumentare in modo prevedibile e il contenuto cambiano dimensione in base alla lingua selezionata.

<a name="Localizing-in-Xcodes-Interface-Builder" />

### <a name="localizing-in-xcodes-interface-builder"></a>Localizzazione in Interface Builder di Xcode

Apple ha fornito funzionalità diversi in Interface Builder di Xcode che lo sviluppatore può utilizzare durante la progettazione o la modifica dell'interfaccia utente di un'app per supportare la localizzazione. Il **direzione del testo** sezione del **attributo Inspector** consente allo sviluppatore di fornire suggerimenti su come direzione deve essere usata e aggiornata in un'istruzione select vista basata su testo (come `NSTextField`):

[![](modern-cocoa-apps-images/content10.png "Le opzioni di direzione del testo")](modern-cocoa-apps-images/content10.png#lightbox)

Esistono tre possibili valori per il **direzione del testo**:

- **Naturale** -il layout è basato sulla stringa assegnata al controllo.
- **Da sinistra a destra** -viene sempre eseguito il layout da sinistra a destra.
- **Zprava Doleva** -viene sempre eseguito il layout da destra a sinistra.

Esistono due possibili valori per il **Layout**:

- **Da sinistra a destra** -layout sempre da sinistra a destra.
- **Zprava Doleva** -il layout è sempre da destra a sinistra.

In genere questi non devono essere modificati a meno che non è necessario un allineamento specifico.

Il **Mirror** proprietà indica al sistema di capovolgere le proprietà del controllo specifico (ad esempio, la posizione della cella immagine). Ha tre valori possibili:

- **Automaticamente** -la posizione verrà automaticamente modificato in base alla direzione nella lingua selezionata.
- **Nell'interfaccia di destra a sinistra** -posizione verrà modificata solo nel diritto di lingue in base a sinistra.
- **Mai** -la posizione non verrà mai modificato.

Se lo sviluppatore ha specificato **centro**, **giustifica** oppure **completo** l'allineamento del contenuto di una vista basata su testo, questi non sarà mai capovolta lingua di base selezionata.

Prima di macOS Sierra, controlli creati nel codice potrebbero non essere reso speculare automaticamente. Lo sviluppatore doveva utilizzare codice simile al seguente per gestire il mirroring:

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

In cui il `Alignment` e `ImagePosition` vengono impostati sulla base di `UserInterfaceLayoutDirection` del controllo.

macOS Sierra aggiunge diversi nuovi costruttori di comodità (tramite il metodo statico `CreateButton` (metodo)) che accettano parametri diversi (ad esempio titolo, immagine e azione) e si eseguirà automaticamente il mirroring in modo corretto. Ad esempio:

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances" />

## <a name="using-system-appearances"></a>Usando gli aspetti del sistema

App macOS moderne può adottare un nuovo aspetto scuro interfaccia che funziona bene per le app di creazione, modifica o una presentazione di immagine:

[![](modern-cocoa-apps-images/content11.png "Un esempio di un'interfaccia utente finestra Mac scuro")](modern-cocoa-apps-images/content11.png#lightbox)

Questa operazione può essere eseguita mediante l'aggiunta di una riga di codice prima che viene visualizzata la finestra. Ad esempio:

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

Il metodo statico `GetAppearance` metodo per il `NSAppearance` classe viene utilizzata per ottenere un aspetto denominato dal sistema (in questo caso `NSAppearance.NameVibrantDark`).

Apple ha i seguenti suggerimenti per l'uso di aspetti di sistema:

- Preferire i colori denominati valori hardcoded (ad esempio `LabelColor` e `SelectedControlColor`).
- Laddove possibile, utilizzare lo stile di controllo standard del sistema.

Per gli utenti che sono abilitate le funzionalità di accessibilità dall'app preferenze di sistema, un'app macOS che usa gli aspetti del sistema automaticamente funzionerà correttamente. Ne consegue suggerite da Apple che lo sviluppatore deve usare sempre gli aspetti del sistema nelle proprie App macOS.

<a name="Designing-UIs-with-Storyboards" />

## <a name="designing-uis-with-storyboards"></a>Progettazione di interfacce utente con gli storyboard

Gli storyboard consentono allo sviluppatore non solo di progettazione del flusso di singoli elementi che costituiscono l'interfaccia utente dell'app, ma per visualizzare e progettare l'interfaccia utente e la gerarchia degli elementi specificati.

I controller consentono allo sviluppatore di raccolta di elementi in un'unità di composizione e segue abstract e rimuovere il tipico "glue codice" necessari per spostare in tutta la gerarchia di visualizzazione:

[![](modern-cocoa-apps-images/content12.png "Modifica l'interfaccia utente in Interface Builder di Xcode")](modern-cocoa-apps-images/content12.png#lightbox)

Per altre informazioni, vedere la [Introduzione agli storyboard](~/mac/platform/storyboards/index.md) documentazione.

Sono presenti molte istanze in cui una scena specificata definita in uno storyboard richiederà i dati da una scena precedente nella gerarchia di visualizzazione. Apple ha i suggerimenti seguenti per passare le informazioni tra le scene:

- Dipendenze di dati sempre devono sovrapporsi verso il basso nella gerarchia.
- Evitare dipendenze strutturale dell'interfaccia utente di impostare come hardcoded, perché questo limita la flessibilità dell'interfaccia utente.
- Usare C# le interfacce per fornire dipendenze di dati generici.

Il Controller di visualizzazione che rappresenta l'origine dell'elemento Segue, è possibile ignorare il `PrepareForSegue` metodo ed effettuare qualsiasi inizializzazione prima l'elemento Segue necessario (ad esempio, il passaggio di dati) viene eseguita per visualizzare il Controller di visualizzazione di destinazione. Ad esempio:

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

Per altre informazioni, vedere la [segue](~/mac/platform/storyboards/indepth.md#Segues) documentazione.

<a name="Propagating-Actions" />

## <a name="propagating-actions"></a>La propagazione di azioni

In base alla struttura dell'app macOS, potrebbero esserci volte quando il gestore migliore per un'azione su un controllo dell'interfaccia utente potrebbe essere un' posizione nella gerarchia dell'interfaccia utente. Questo vale in genere di menu e voci di Menu che si trovano nel proprio scena, separata dal resto dell'interfaccia utente dell'app.

Per gestire questa situazione, lo sviluppatore può creare un'azione personalizzata e passare l'azione fino alla catena risponditore. Per altre informazioni, vedere la [uso di azioni della finestra personalizzato](~/mac/user-interface/menu.md) documentazione.

<a name="Modern-Mac-Features" />

## <a name="modern-mac-features"></a>Funzionalità moderne di Mac

Apple ha incluso numerose funzionalità destinate all'utente in macOS Sierra che consentono agli sviluppatori di sfruttare al meglio la piattaforma Mac, ad esempio:

- **NSUserActivity** -in questo modo l'app descrivere l'attività che l'utente è attualmente coinvolto in. `NSUserActivity` è stato inizialmente creato per supportare la continuità, in cui un'attività di avvio in uno dei dispositivi dell'utente potrebbe essere prelevata e continua in un altro dispositivo. `NSUserActivity` funziona in macOS come avviene in iOS ed è quindi necessario vedere nostri [Introduzione a Handoff](~/ios/platform/handoff.md) documentazione iOS per altri dettagli.
- **Siri nel Mac** -Siri Usa l'attività corrente (`NSUserActivity`) per fornire un contesto per i comandi di un utente può emettere.
- **Stato ripristino** : quando l'utente dimissioni un'app su macOS e successivamente riavvio di esso, l'app verrà automaticamente restituito lo stato precedente. Lo sviluppatore può utilizzare l'API di ripristino dello stato per codificare e ripristinare uno stato temporaneo dell'interfaccia utente prima che l'interfaccia utente venga visualizzato all'utente. Se l'app è `NSDocument` base, il ripristino dello stato viene gestito automaticamente. Per abilitare il ripristino dello stato per non -`NSDocument` basato su App, impostare il `Restorable` del `NSWindow` classe `true`.
- **I documenti nel Cloud** -prima macOS Sierra, un'app era necessario acconsentire esplicitamente all'uso di documenti in iCloud dell'utente unità. In macOS del Sierra l'utente **Desktop** e **documenti** cartelle possono essere sincronizzate con loro iCloud Drive automaticamente dal sistema. Di conseguenza, le copie locali dei documenti potrebbero essere eliminate per liberare spazio sul computer dell'utente. `NSDocument` App basata su gestirà automaticamente questa modifica. Tutti gli altri tipi di app saranno necessario utilizzare un `NSFileCoordinator` per sincronizzare la lettura e scrittura di documenti.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato alcuni suggerimenti, funzionalità e tecniche di che uno sviluppatore può utilizzare per creare un'app macOS moderne in xamarin. Mac.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di macOS](https://developer.xamarin.com/samples/mac/)
