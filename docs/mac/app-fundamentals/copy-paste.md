---
title: Copiare e incollare in xamarin. Mac
description: Questo articolo descrive come usare il tavolo alla invierà copia e Incolla in un'applicazione xamarin. Mac. Viene illustrato come lavorare con i tipi di dati standard che possono essere condivisi tra più App e come supportare dati personalizzati all'interno di una determinata app.
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 728e0264f7da8f3adfef360dd473772dd7e28a11
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251073"
---
# <a name="copy-and-paste-in-xamarinmac"></a>Copiare e incollare in xamarin. Mac

_Questo articolo descrive come usare il tavolo alla invierà copia e Incolla in un'applicazione xamarin. Mac. Viene illustrato come lavorare con i tipi di dati standard che possono essere condivisi tra più App e come supportare dati personalizzati all'interno di una determinata app._

## <a name="overview"></a>Panoramica

Quando si usa c# e .NET in un'applicazione xamarin. Mac, è possibile utilizzare lo stesso tavolo di montaggio (copia e Incolla) supporto che dispone di uno sviluppatore che lavora in Objective-C.

In questo articolo è verranno illustrati due modi principali per usare tavolo in un'app xamarin. Mac:

1. **Tipi di dati standard** -poiché operazioni tavolo di montaggio sono in genere effettuate tra due App non correlate, nessuna delle due app conosce i tipi di dati che l'altro supporta. Per aumentare al massimo il potenziale per la condivisione, tavolo può contenere più rappresentazioni di un determinato articolo (tramite un set standard di tipi di dati comuni), questo consente all'app dispendiosa in termini di selezionare la versione più adatta per le esigenze.
2. **Dati personalizzati** : per supportare le operazioni copia e Incolla di dati complessi all'interno di xamarin. Mac è possibile definire un tipo di dati personalizzato che verrà gestito da tavolo. Ad esempio, un'app di disegno vettoriale che consente all'utente copiare e incollare forme complesse costituite da più tipi di dati e punti.

[![Esempio di app in esecuzione](copy-paste-images/intro01.png "esempio di app in esecuzione")](copy-paste-images/intro01-large.png#lightbox)

In questo articolo, si affronterà le nozioni di base dell'utilizzo di area di montaggio in un'applicazione xamarin. Mac per supportare la copia e incollare le operazioni. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` attributi utilizzato per impostare backup di classi c# per gli oggetti di Objective-C e interfaccia utente di elementi.

## <a name="getting-started-with-the-pasteboard"></a>Introduzione a tavolo

Tavolo presenta un meccanismo standard per lo scambio di dati all'interno di una determinata applicazione o tra le applicazioni. L'utilizzo tipico per un tavolo in un'applicazione xamarin. Mac è gestire copia e Incolla le operazioni, tuttavia, un numero di altre operazioni è supportato anche (ad esempio trascinamento e rilascio e servizi dell'applicazione).

Per ottenere rapidamente è il piede giusto, si intende iniziare con una semplice introduzione pratica a uso pasteboards in un'app xamarin. Mac. In seguito, forniremo una spiegazione dettagliata del funzionamento di spazio di lavoro e i metodi usati.

Per questo esempio, che verrà creata un'applicazione semplice documento basato che gestisce una finestra contenente una visualizzazione di immagini. L'utente sarà in grado di copiare e incollare le immagini tra i documenti nell'app e a o da altre App o più finestre all'interno dell'app stessa.

### <a name="creating-the-xamarin-project"></a>Creazione del progetto Xamarin

In primo luogo, si intende creare una nuova app xamarin. Mac documento basato che abbiamo verrà aggiunta di copia e incollare il supporto per.

Seguire questa procedura:

1. Avviare Visual Studio per Mac e fare clic su di **nuovo progetto...**  collegamento.
2. Selezionare **Mac** > **App** > **App Cocoa**, quindi fare clic su di **successivo** pulsante: 

    [![Crea un nuovo progetto di app Cocoa](copy-paste-images/sample01.png "creando un nuovo progetto di app Cocoa")](copy-paste-images/sample01-large.png#lightbox)
3. Immettere `MacCopyPaste` per il **nome progetto** e mantenere tutto il resto come predefinito. Fare clic su Avanti: 

    [![Impostazione del nome del progetto](copy-paste-images/sample01a.png "impostazione del nome del progetto")](copy-paste-images/sample01a-large.png#lightbox)

4. Scegliere il **Create** pulsante: 

    [![Conferma le nuove impostazioni di progetto](copy-paste-images/sample02.png "conferma le nuove impostazioni di progetto")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>Aggiungere un NSDocument

Ora si aggiungerà custom `NSDocument` classe che verrà usato come risorsa di archiviazione in background per l'interfaccia utente dell'applicazione. Verrà contengono una singola visualizzazione di immagini e sapere come copiare un'immagine dalla visualizzazione nell'area di montaggio predefinito e su come acquisire un'immagine da tavolo predefinito e visualizzarlo nella visualizzazione immagine.

Pulsante destro del mouse sul progetto xamarin. Mac nel **riquadro della soluzione** e selezionare **Add** > **nuovo File...** :

![L'aggiunta al progetto un NSDocument](copy-paste-images/sample03.png "aggiungendo un NSDocument al progetto")

Immettere `ImageDocument` in **Nome** e fare clic sul pulsante **Nuovo**. Modificare il **ImageDocument.cs** classe e renderlo simile al seguente:

```csharp
using System;
using AppKit;
using Foundation;
using ObjCRuntime;

namespace MacCopyPaste
{
    [Register("ImageDocument")]
    public class ImageDocument : NSDocument
    {
        #region Computed Properties
        public NSImageView ImageView {get; set;}

        public ImageInfo Info { get; set; } = new ImageInfo();

        public bool ImageAvailableOnPasteboard {
            get {
                // Initialize the pasteboard
                NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
                Class [] classArray  = { new Class ("NSImage") };

                // Check to see if an image is on the pasteboard
                return pasteboard.CanReadObjectForClasses (classArray, null);
            }
        }
        #endregion

        #region Constructor
        public ImageDocument ()
        {
        }
        #endregion

        #region Public Methods
        [Export("CopyImage:")]
        public void CopyImage(NSObject sender) {

            // Grab the current image
            var image = ImageView.Image;

            // Anything to process?
            if (image != null) {
                // Get the standard pasteboard
                var pasteboard = NSPasteboard.GeneralPasteboard;

                // Empty the current contents
                pasteboard.ClearContents();

                // Add the current image to the pasteboard
                pasteboard.WriteObjects (new NSImage[] {image});

                // Save the custom data class to the pastebaord
                pasteboard.WriteObjects (new ImageInfo[] { Info });

                // Using a Pasteboard Item
                NSPasteboardItem item = new NSPasteboardItem();
                string[] writableTypes = {"public.text"};

                // Add a data provier to the item
                ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
                var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

                // Save to pasteboard
                if (ok) {
                    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
                }
            }

        }

        [Export("PasteImage:")]
        public void PasteImage(NSObject sender) {

            // Initialize the pasteboard
            NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
            Class [] classArray  = { new Class ("NSImage") };

            bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
                NSImage image = (NSImage)objectsToPaste[0];

                // Display the new image
                ImageView.Image = image;
            }

            Class [] classArray2 = { new Class ("ImageInfo") };
            ok = pasteboard.CanReadObjectForClasses (classArray2, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
                ImageInfo info = (ImageInfo)objectsToPaste[0];

            }

        }
        #endregion
    }
}
```

Diamo un'occhiata ad alcune del codice in dettaglio di seguito.

Il codice seguente fornisce una proprietà per verificare l'esistenza dei dati dell'immagine sul tavolo predefinito, se è disponibile, un'immagine `true` viene restituito else `false`:

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

Il codice seguente consente di copiare un'immagine dalla visualizzazione immagine allegata nell'area di montaggio predefinito:

```csharp
[Export("CopyImage:")]
public void CopyImage(NSObject sender) {

    // Grab the current image
    var image = ImageView.Image;

    // Anything to process?
    if (image != null) {
        // Get the standard pasteboard
        var pasteboard = NSPasteboard.GeneralPasteboard;

        // Empty the current contents
        pasteboard.ClearContents();

        // Add the current image to the pasteboard
        pasteboard.WriteObjects (new NSImage[] {image});

        // Save the custom data class to the pastebaord
        pasteboard.WriteObjects (new ImageInfo[] { Info });

        // Using a Pasteboard Item
        NSPasteboardItem item = new NSPasteboardItem();
        string[] writableTypes = {"public.text"};

        // Add a data provider to the item
        ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
        var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

        // Save to pasteboard
        if (ok) {
            pasteboard.WriteObjects (new NSPasteboardItem[] { item });
        }
    }

}
```

E il codice seguente consente di incollare un'immagine da tavolo predefinito e lo visualizza nella visualizzazione immagine allegata (se tavolo contiene un'immagine valida):

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0]
    }
}
```

Con questo documento nella posizione, si creerà l'interfaccia utente per l'app xamarin. Mac.

### <a name="building-the-user-interface"></a>Creazione dell'interfaccia utente

Fare doppio clic il **Main. Storyboard** file per aprirlo in Xcode. Successivamente, aggiungere anche una barra degli strumenti e un'immagine e configurarli come illustrato di seguito:

[![Barra degli strumenti di modifica](copy-paste-images/sample04.png "barra degli strumenti di modifica")](copy-paste-images/sample04-large.png#lightbox)

Aggiungere una copia e Incolla **immagine della barra degli strumenti** sul lato sinistro della barra degli strumenti. Verrà usato come tasti di scelta rapida per copiare e incollare dal menu Modifica. Successivamente, aggiungere quattro **immagine della barra degli strumenti elementi** sul lato destro della barra degli strumenti. Si userà queste per popolare l'immagine con alcune immagini predefinite.

Per altre informazioni sull'uso delle barre degli strumenti, vedere la [barre degli strumenti](~/mac/user-interface/toolbar.md) documentazione.

Successivamente, è possibile esporre anche il Outlet e azioni per gli elementi della barra degli strumenti e l'immagine seguente:

[![Creazione di Outlet e azioni](copy-paste-images/sample05.png "creazione Outlet e azioni")](copy-paste-images/sample05-large.png#lightbox)

Per altre informazioni sull'uso di Outlet e azioni, vedere la [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) sezione del nostro [Hello, Mac](~/mac/get-started/hello-mac.md) documentazione.

### <a name="enabling-the-user-interface"></a>Abilitazione dell'interfaccia utente

Con l'interfaccia utente creata in Xcode e l'elemento dell'interfaccia utente vengono esposte tramite Outlet e azioni, è necessario aggiungere il codice per abilitare l'interfaccia utente. Fare doppio clic il **ImageWindow.cs** del file nei **riquadro della soluzione** e renderlo simile al seguente:

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCopyPaste
{
    public partial class ImageWindow : NSWindow
    {
        #region Private Variables
        ImageDocument document;
        #endregion

        #region Computed Properties
        [Export ("Document")]
        public ImageDocument Document {
            get {
                return document;
            }
            set {
                WillChangeValue ("Document");
                document = value;
                DidChangeValue ("Document");
            }
        }

        public ViewController ImageViewController {
            get { return ContentViewController as ViewController; }
        }

        public NSImage Image {
            get {
                return ImageViewController.Image;
            }
            set {
                ImageViewController.Image = value;
            }
        }
        #endregion

        #region Constructor
        public ImageWindow (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create a new document instance
            Document = new ImageDocument ();

            // Attach to image view
            Document.ImageView = ImageViewController.ContentView;
        }
        #endregion

        #region Public Methods
        public void CopyImage (NSObject sender)
        {
            Document.CopyImage (sender);
        }

        public void PasteImage (NSObject sender)
        {
            Document.PasteImage (sender);
        }

        public void ImageOne (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image01.jpg");

            // Set image info
            Document.Info.Name = "city";
            Document.Info.ImageType = "jpg";
        }

        public void ImageTwo (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image02.jpg");

            // Set image info
            Document.Info.Name = "theater";
            Document.Info.ImageType = "jpg";
        }

        public void ImageThree (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image03.jpg");

            // Set image info
            Document.Info.Name = "keyboard";
            Document.Info.ImageType = "jpg";
        }

        public void ImageFour (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image04.jpg");

            // Set image info
            Document.Info.Name = "trees";
            Document.Info.ImageType = "jpg";
        }
        #endregion
    }
}
```

Diamo un'occhiata a questo codice in dettaglio di seguito.

In primo luogo, è necessario esporre un'istanza di `ImageDocument` classe creati in precedenza:

```csharp
private ImageDocument _document;
...

[Export ("Document")]
public ImageDocument Document {
    get { return _document; }
    set {
        WillChangeValue ("Document");
        _document = value;
        DidChangeValue ("Document");
    }
}
```

Usando `Export`, `WillChangeValue` e `DidChangeValue`, è stato configurato il `Document` la proprietà chiave-valore di codifica e il Data Binding in Xcode.

Si espone anche l'immagine dall'immagine che e abbiamo aggiunto alla nostra interfaccia utente in Xcode con la proprietà seguente:

```csharp
public ViewController ImageViewController {
    get { return ContentViewController as ViewController; }
}

public NSImage Image {
    get {
        return ImageViewController.Image;
    }
    set {
        ImageViewController.Image = value;
    }
}
```

Quando la finestra principale viene caricata e visualizzata, viene creata un'istanza del nostro `ImageDocument` classe e collegarvi immagine dell'interfaccia utente anche con il codice seguente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create a new document instance
    Document = new ImageDocument ();

    // Attach to image view
    Document.ImageView = ImageViewController.ContentView;
}
```

Infine, in risposta all'utente facendo clic sugli elementi della barra degli strumenti di copia e Incolla, viene chiamato l'istanza del `ImageDocument` classe per eseguire le operazioni effettive:

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>Abilitare i menu File e modifica

L'ultima cosa dobbiamo fare è abilitare la **New** voce di menu dalle **File** menu (per creare nuove istanze della nostra finestra principale) e abilitare il **Taglia**, **copia**  e **incollare** voci di menu dal **modifica** menu.

Per abilitare il **New** dal menu Modifica, il **AppDelegate.cs** file e aggiungere il codice seguente:

```csharp
public int UntitledWindowCount { get; set;} =1;
...

[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

Per altre informazioni, vedere la [utilizzo di Windows più](~/mac/user-interface/window.md) sezione del nostro [Windows](~/mac/user-interface/window.md) documentazione.

Per abilitare la **tagliare**, **copia** e **Incolla** voci di menu, modificare il **AppDelegate.cs** file e aggiungere il codice seguente:

```csharp
[Export("copy:")]
void CopyImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);
}

[Export("cut:")]
void CutImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);

    // Clear the existing image
    window.Image = null;
}

[Export("paste:")]
void PasteImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Paste the image from the clipboard
    window.Document.PasteImage (sender);
}
```

Per ogni voce di menu, viene visualizzata la finestra corrente, in primo piano, chiave ed eseguire il cast a nostro `ImageWindow` classe:

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

Da qui definiamo il `ImageDocument` istanza della classe della finestra per gestire la copia e Incolla. Ad esempio: 

```csharp
window.Document.CopyImage (sender);
```

Vogliamo solo **tagliare**, **copia** e **Incolla** voci di menu per essere accessibili se è presente immagine dei dati in area di montaggio predefinito o nell'immagine anche della finestra attiva corrente.

È possibile aggiungere un **EditMenuDelegate.cs** file al progetto xamarin. Mac e renderlo simile al seguente:

```csharp
using System;
using AppKit;

namespace MacCopyPaste
{
    public class EditMenuDelegate : NSMenuDelegate
    {
        #region Override Methods
        public override void MenuWillHighlightItem (NSMenu menu, NSMenuItem item)
        {
        }

        public override void NeedsUpdate (NSMenu menu)
        {
            // Get list of menu items
            NSMenuItem[] Items = menu.ItemArray ();

            // Get the key window and determine if the required images are available
            var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
            var hasImage = (window != null) && (window.Image != null);
            var hasImageOnPasteboard = (window != null) && window.Document.ImageAvailableOnPasteboard;

            // Process every item in the menu
            foreach(NSMenuItem item in Items) {
                // Take action based on the menu title
                switch (item.Title) {
                case "Cut":
                case "Copy":
                case "Delete":
                    // Only enable if there is an image in the view
                    item.Enabled = hasImage;
                    break;
                case "Paste":
                    // Only enable if there is an image on the pasteboard
                    item.Enabled = hasImageOnPasteboard;
                    break;
                default:
                    // Only enable the item if it has a sub menu
                    item.Enabled = item.HasSubmenu;
                    break;
                }
            }
        }
        #endregion
    }
}
```

Anche in questo caso, viene visualizzata la finestra corrente, in primo piano e usare relativo `ImageDocument` istanza della classe per controllare se i dati dell'immagine necessaria esistano. Quindi si userà il `MenuWillHighlightItem` metodo per abilitare o disabilitare ogni elemento di base a questo stato.

Modificare il **AppDelegate.cs** del file e verificare il `DidFinishLaunching` metodo aspetto simile al seguente:
 
```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

In primo luogo, si disabilita l'attivazione automatica e la disabilitazione di voci di menu nel menu Modifica. Successivamente, si collega un'istanza di `EditMenuDelegate` classe creati in precedenza.

Per altre informazioni, vedere la [menu](~/mac/user-interface/menu.md) documentazione.

### <a name="testing-the-app"></a>Test dell'app

Con tutti gli elementi sul posto, siamo pronti testare l'applicazione. Compilare ed eseguire l'app e viene visualizzata l'interfaccia principale:

![Esecuzione dell'applicazione](copy-paste-images/run01.png "esecuzione dell'applicazione")

Se si apre il menu Modifica, si noti che **tagliare**, **copia** e **Incolla** sono disabilitati perché non è presente alcuna immagine nell'immagine nell'area di montaggio predefinito o anche:

![Aprire il menu Modifica](copy-paste-images/run02.png "aprendo il menu di modifica")

Se si aggiunta anche un'immagine per l'immagine e si riapre il menu Modifica, gli elementi verranno abilitati:

![Che mostra le voci di menu di modifica sono abilitate](copy-paste-images/run03.png "che mostra le voci di menu di modifica sono abilitati")

Se si copia l'immagine e selezionare **New** dal menu file, è possibile incollare tale immagine in nuova finestra:

![Incollare un'immagine in una nuova finestra](copy-paste-images/run04.png "incollare un'immagine in una nuova finestra")

Nelle sezioni seguenti, articolo verrà fornita un'analisi approfondita lavora sul tavolo in un'applicazione xamarin. Mac.

## <a name="about-the-pasteboard"></a>Sull'area di montaggio

In macOS (precedentemente noto come OS X) tavolo (`NSPasteboard`) fornisce il supporto di diversi server elabora ad esempio copiare e incollare, trascinare e rilasciare e servizi dell'applicazione. Nelle sezioni seguenti verranno illustrate le diverse concetti tavolo di montaggio.

### <a name="what-is-a-pasteboard"></a>Che cos'è un tavolo?

Il `NSPasteboard` classe fornisce un meccanismo standard per lo scambio di informazioni tra applicazioni o all'interno di una determinata app. La funzione principale di un tavolo è per la gestione delle operazioni di copia e Incolla:

1. Quando l'utente seleziona un elemento in un'app e Usa il **tagliare** oppure **copia** voce di menu, uno o più rappresentazioni dell'elemento selezionato vengono posizionate sul tavolo.
2. Quando l'utente utilizza il **Incolla** voce di menu (all'interno della stessa app o uno diverso), la versione dei dati che è possibile gestire viene copiata da tavolo e aggiunta all'app.

Usi tavolo di montaggio meno ovvi includono find, trascinare, trascinamento della selezione e le operazioni di servizi delle applicazioni:

- Quando l'utente avvia un'operazione di trascinamento, i dati di trascinamento vengono copiati da tavolo. Se l'operazione di trascinamento termina con un elenco in un'altra app, che l'app consente di copiare i dati da tavolo.
- Per i servizi di traduzione, il conversione dei dati viene copiati da tavolo dall'app per la richiesta. Il servizio dell'applicazione, recupera i dati da tavolo, esegue la conversione, consente di incollare i dati nuovamente sul tavolo.

Nella forma più semplice, pasteboards consentono di spostare i dati all'interno di una determinata applicazione o tra App e, pertanto, esiste in un'area di memoria globale speciale all'esterno del processo dell'app. Mentre i concetti del pasteboards sono facilmente grasps, esistono diversi aspetti più complessi che devono essere considerati. Si parlerà in dettaglio di seguito.

### <a name="named-pasteboards"></a>Pasteboards denominata

Un tavolo può essere pubblico o privato e può essere usato per diversi scopi in un'applicazione o tra più app. macOS sono disponibili diversi pasteboards standard, ognuno con un utilizzo specifico e ben definito:

- `NSGeneralPboard` -Dello spazio di lavoro l'impostazione predefinita per **tagliare**, **copia** e **Incolla** operazioni.
- `NSRulerPboard` -Supporta **tagliare**, **copia** e **Incolla** operazioni sulle **righelli**.
- `NSFontPboard` -Supporta **tagliare**, **copia** e **Incolla** operazioni su `NSFont` oggetti.
- `NSFindPboard` -Supporta specifiche dell'applicazione Trova riquadri che possono condividere il testo di ricerca.
- `NSDragPboard` -Supporta **trascinamento della selezione** operazioni.

La maggior parte dei casi, si userà una del pasteboards definito dal sistema. Tuttavia, potrebbero esserci situazioni in cui è necessario creare il proprio pasteboards. In queste situazioni, è possibile usare la `FromName (string name)` metodo di `NSPasteboard` classe per creare un tavolo personalizzato con il nome specificato.

Facoltativamente, è possibile chiamare il `CreateWithUniqueName` metodo di `NSPasteboard` classe per creare un nome univoco dello spazio di lavoro.

### <a name="pasteboard-items"></a>Elementi tavolo di montaggio

Ogni porzione di dati che scrive un'applicazione a un tavolo viene considerata una _tavolo elemento_ , un tavolo può contenere più elementi contemporaneamente. In questo modo, un'app può scrivere più versioni di dati da copiare per un tavolo (ad esempio, testo normale e testo formattato) e il recupero delle app possono leggere solo i dati che può elaborare (ad esempio, di solo testo normale).

### <a name="data-representations-and-uniform-type-identifiers"></a>Rappresentazioni di dati e gli identificatori di tipo uniform

Operazioni tavolo di montaggio accettano in genere le applicazioni tra di due (o più) che non sono a conoscenza della loro o i tipi di dati che ognuna possa gestire. Come indicato nella sezione precedente, per aumentare al massimo il potenziale per la condivisione di informazioni, un tavolo può contenere più rappresentazioni dei dati viene copiato e incollato.

Tutte le rappresentazioni viene identificata tramite un Uniform tipo identificatore (UTI), che non è altro che una semplice stringa che identifica in modo univoco il tipo di data presentato (per altre informazioni, vedere Apple [Panoramica di identificatori di tipo Uniform ](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) documentazione). 

Se si sta creando un tipo di dati personalizzati (ad esempio, un disegno in un'app di disegno vettoriale), è possibile creare il proprio UTI per facilitarne l'identificazione nella copia e incollare le operazioni in modo univoco.

Quando si prepara un'app per incollare dati copiati da un tavolo, deve trovare la rappresentazione che meglio soddisfa le funzionalità (se presente). In genere si tratterà il tipo più completo disponibile (testo formattato, ad esempio per un'app di elaborazione di testo), eseguire il fallback su tipi più semplici disponibili come richiesto (testo normale per un semplice editor di testo).

<a name="Promised_Data" />

### <a name="promised-data"></a>Dati promessi

In generale, è necessario fornire tutte le rappresentazioni di dati da copiare possibili per ottimizzare la condivisione tra le app. A causa dei vincoli di tempo o memoria, tuttavia, può essere impraticabile necessarie per scrivere ogni tipo di dati nell'area di montaggio.

In questo caso, è possibile inserire la rappresentazione dei dati prima nell'area di montaggio e l'app ricevente può richiedere una rappresentazione diversa, che può essere generato in volo appena prima dell'operazione Incolla.

Quando si inserisce l'elemento iniziale nell'area di montaggio, si specificherà che uno o più delle altre rappresentazioni disponibili vengono forniti da un oggetto che è conforme al `NSPasteboardItemDataProvider` interfaccia. Questi oggetti fornirà le rappresentazioni aggiuntive su richiesta, come richiesto dall'applicazione ricevente.

### <a name="change-count"></a>Modificare il numero di

Ogni dello spazio di lavoro mantiene una _Changecount_ che viene incrementato ogni volta un nuovo proprietario è dichiarato. Un'app è possibile determinare se il contenuto dell'area di montaggio è stato modificato dall'ultima volta che viene esaminata controllando il valore del conteggio di modifiche.

Usare la `ChangeCount` e `ClearContents` metodi del `NSPasteboard` classe per modificare conteggio di un determinato dello spazio di lavoro di modifiche.

## <a name="copying-data-to-a-pasteboard"></a>Copia dei dati su un tavolo

Si esegue un'operazione di copia prima l'accesso a un tavolo, deselezionando qualsiasi contenuto esistente e la scrittura di tanti rappresentazioni dei dati come sono necessari per il tavolo.

Ad esempio:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

In genere, è semplicemente possibile scrivere da tavolo generale, come nell'esempio precedente è stato eseguito. Qualsiasi oggetto che si invia al `WriteObjects` metodo *deve* conformi al `INSPasteboardWriting` interfaccia. Classi predefinite diverse, (ad esempio `NSString`, `NSImage`, `NSURL`, `NSColor`, `NSAttributedString`, e `NSPasteboardItem`) rendono automaticamente conformi a questa interfaccia.

Se si sta scrivendo una classe di dati personalizzate da tavolo deve essere conforme per il `INSPasteboardWriting` interfaccia o eseguire il wrapping in un'istanza del `NSPasteboardItem` classe (vedere il [tipi di dati personalizzati](#Custom_Data_Types) sezione riportata di seguito).

## <a name="reading-data-from-a-pasteboard"></a>La lettura dei dati da un tavolo

Come indicato in precedenza, per aumentare al massimo il potenziale per la condivisione dei dati tra le app, più rappresentazioni dei dati copiati potrebbero essere scritti tavolo. Spetta all'app ricevente di selezionare la versione più ampia possibile che le funzionalità (se presente).

### <a name="simple-paste-operation"></a>Operazione Incolla semplice

Leggere dati da tavolo di montaggio usando il `ReadObjectsForClasses` (metodo). Richiederà due parametri:

1. Matrice di `NSObject` basati su tipi di classe che si desidera leggere dall'area di montaggio. Sarà necessario ordinare ciò con il tipo di dati più desiderato in primo luogo, con tutti i tipi rimanenti nel preferenza decrescente.
2. Dizionario che contiene vincoli aggiuntivi (ad esempio limitando a specifici tipi di contenuto di URL) o un dizionario vuoto se nessun altri vincoli sono necessari.

Il metodo restituisce una matrice di elementi che soddisfano i criteri che è passati e pertanto includa al massimo lo stesso numero di tipi di dati che vengono richiesti. è anche possibile che nessuno dei tipi di richiesta sono presenti e verrà restituita una matrice vuota.

Ad esempio, il codice seguente verifica se un `NSImage` presente nell'area generale di montaggio e lo visualizza in un'immagine ben in caso affermativo:

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0];
            }
}
```

### <a name="requesting-multiple-data-types"></a>La richiesta più tipi di dati

In base al tipo di applicazione xamarin. Mac viene creato, potrebbe essere in grado di gestire più rappresentazioni dei dati da incollare. In questo caso, esistono due scenari per il recupero dei dati dall'area di montaggio:

1. Una singola chiamata a rendere la `ReadObjectsForClasses` (metodo) e fornendo una matrice di tutte le rappresentazioni desiderato (nell'ordine preferito).
2. Effettuare più chiamate al `ReadObjectsForClasses` ogni volta che i tipi di metodo che richiede una matrice diversa di.

Vedere le **semplice operazione Incolla** sezione precedente per altri dettagli su come recuperare dati da un tavolo.

### <a name="checking-for-existing-data-types"></a>Controllo per tipi di dati esistenti

Esistono situazioni in cui si potrebbero voler controllare se un tavolo contiene una rappresentazione di dati specificato senza dover leggere effettivamente i dati da tavolo (ad esempio l'abilitazione di **Incolla** voce di menu solo quando esistono dati validi).

Chiamare il `CanReadObjectForClasses` del tavolo per verificare se contiene un determinato tipo di metodo.

Ad esempio, il codice seguente determina se il generale dello spazio di lavoro contiene un `NSImage` istanza:

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

### <a name="reading-urls-from-the-pasteboard"></a>La lettura di URL da tavolo

In base alla funzione di un'app xamarin. Mac, può risultare necessaria per leggere gli URL da un tavolo, ma solo se soddisfano un determinato set di criteri (ad esempio, che punta al file o URL di un tipo di dati specifici). In questo caso, è possibile specificare criteri di ricerca aggiuntivi tramite il secondo parametro del `CanReadObjectForClasses` o `ReadObjectsForClasses` metodi.

<a name="Custom_Data_Types" />

## <a name="custom-data-types"></a>Tipi di dati personalizzati

Vi sono casi quando è necessario salvare i tipi personalizzati da tavolo da un'app xamarin. Mac. Ad esempio, un disegno vettoriale app che consente all'utente di copiare e incollare oggetti disegno.

In questo caso, è necessario progettare la classe personalizzata dei dati in modo che erediti da `NSObject` e sia conforme a alcune interfacce (`INSCoding`, `INSPasteboardWriting` e `INSPasteboardReading`). Facoltativamente, è possibile usare un `NSPasteboardItem` per incapsulare i dati copiati o incollati.

Entrambe le opzioni vengono trattati in dettaglio di seguito.

### <a name="using-a-custom-class"></a>Uso di una classe personalizzata

In questa sezione verranno essere espansione dell'app di esempio semplice che abbiamo creato all'inizio di questo documento e aggiunta di una classe personalizzata per tenere traccia delle informazioni sull'immagine che si sta copiando e incollando tra le finestre.

Aggiungere una nuova classe al progetto e denominarlo **ImageInfo.cs**. Modificare il file e renderlo simile al seguente:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfo")]
    public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
    {
        #region Computed Properties
        [Export("name")]
        public string Name { get; set; }

        [Export("imageType")]
        public string ImageType { get; set; }
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfo ()
        {
        }
        
        public ImageInfo (IntPtr p) : base (p)
        {
        }

        [Export ("initWithCoder:")]
        public ImageInfo(NSCoder decoder) {

            // Decode data
            NSString name = decoder.DecodeObject("name") as NSString;
            NSString type = decoder.DecodeObject("imageType") as NSString;

            // Save data
            Name = name.ToString();
            ImageType = type.ToString ();
        }
        #endregion

        #region Public Methods
        [Export ("encodeWithCoder:")]
        public void EncodeTo (NSCoder encoder) {

            // Encode data
            encoder.Encode(new NSString(Name),"name");
            encoder.Encode(new NSString(ImageType),"imageType");
        }

        [Export ("writableTypesForPasteboard:")]
        public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
            string[] writableTypes = {"com.xamarin.image-info", "public.text"};
            return writableTypes;
        }

        [Export ("pasteboardPropertyListForType:")]
        public virtual NSObject GetPasteboardPropertyListForType (string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSKeyedArchiver.ArchivedDataWithRootObject(this);
            case "public.text":
                return new NSString(string.Format("{0}.{1}", Name, ImageType));
            }

            // Failure, return null
            return null;
        }

        [Export ("readableTypesForPasteboard:")]
        public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
            string[] readableTypes = {"com.xamarin.image-info", "public.text"};
            return readableTypes;
        }

        [Export ("readingOptionsForType:pasteboard:")]
        public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSPasteboardReadingOptions.AsKeyedArchive;
            case "public.text":
                return NSPasteboardReadingOptions.AsString;
            }

            // Default to property list
            return NSPasteboardReadingOptions.AsPropertyList;
        }

        [Export ("initWithPasteboardPropertyList:ofType:")]
        public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return new ImageInfo();
            case "public.text":
                return new ImageInfo();
            }

            // Failure, return null
            return null;
        }
        #endregion
    }
}
    
```

Nelle sezioni seguenti articolo verrà fornita un'analisi approfondita questa classe.

#### <a name="inheritance-and-interfaces"></a>Ereditarietà e interfacce

Prima di una classe di dati personalizzata può essere scritto o letto da un tavolo, deve essere conforme per il `INSPastebaordWriting` e `INSPasteboardReading` interfacce. Inoltre, deve ereditare da `NSObject` e anche conforme al `INSCoding` interfaccia:

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

Deve anche esporre la classe Objective-c usando il `Register` direttiva e si devono esporre le proprietà obbligatorie o i metodi usando `Export`. Ad esempio:

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

Si sta esponendo i due campi di dati che questa classe conterrà - nome dell'immagine e il relativo tipo (jpg, png e così via). 

Per altre informazioni, vedere la [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) documentazione, viene spiegato il `Register` e `Export` attributi utilizzato per impostare backup di classi c# per gli oggetti di Objective-C e interfaccia utente di elementi.

#### <a name="constructors"></a>Costruttori

Due costruttori (correttamente esposti Objective-c) sono necessari per la nostra classe di dati personalizzati in modo che possono essere lette da un tavolo:

```csharp
[Export ("init")]
public ImageInfo ()
{
}

[Export ("initWithCoder:")]
public ImageInfo(NSCoder decoder) {

    // Decode data
    NSString name = decoder.DecodeObject("name") as NSString;
    NSString type = decoder.DecodeObject("imageType") as NSString;

    // Save data
    Name = name.ToString();
    ImageType = type.ToString ();
}
```

In primo luogo, è necessario esporre il _vuote_ costruttore sotto il metodo di Objective-C predefinito `init`.

Successivamente, è necessario esporre un `NSCoding` costruttore conforme che verrà utilizzato per creare una nuova istanza dell'oggetto da tavolo, quando si incolla sotto il nome del esportato `initWithCoder`.

Questo costruttore non accetta un `NSCoder` (creato da un `NSKeyedArchiver` quando scritta da tavolo), estrae i dati chiave/valore associato e lo salva i campi delle proprietà della classe di dati.

#### <a name="writing-to-the-pasteboard"></a>La scrittura da tavolo

Rispettando la conformità per i `INSPasteboardWriting` , è necessario implementare l'interfaccia espone due metodi e, facoltativamente, un terzo metodo, in modo che la classe può essere scritti da tavolo.

In primo luogo, è necessario indicare il tavolo quali dati digitare rappresentazioni che è possibile scrivere la classe personalizzata:

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

Tutte le rappresentazioni viene identificata tramite un Uniform tipo identificatore (UTI), che non è altro che una semplice stringa che identifica in modo univoco il tipo di dati presentati (per altre informazioni, vedere Apple [Panoramica di identificatori di tipo Uniform ](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) documentazione).

Per il formato personalizzato, stiamo creando un UTI: "com.xamarin.image-info" (si noti che si trova in notazione inversa proprio come un identificatore dell'App). La classe è inoltre in grado di scrivere una stringa standard da tavolo (`public.text`). 

Successivamente, è necessario creare l'oggetto nel formato richiesto che in realtà vengono scritte da tavolo:

```csharp
[Export ("pasteboardPropertyListForType:")]
public virtual NSObject GetPasteboardPropertyListForType (string type) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSKeyedArchiver.ArchivedDataWithRootObject(this);
    case "public.text":
        return new NSString(string.Format("{0}.{1}", Name, ImageType));
    }

    // Failure, return null
    return null;
}
```

Per il `public.text` tipo, si sta restituendo un semplice formato `NSString` oggetto. Per l'oggetto personalizzato `com.xamarin.image-info` tipo, si sta usando una `NSKeyedArchiver` e il `NSCoder` interfaccia per codificare la classe di dati personalizzati a un archivio chiave/valore associato. È necessario implementare il metodo seguente per gestire la codifica:

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

Le coppie chiave/valore singoli vengono scritti al codificatore e verranno decodificate tramite il secondo costruttore che abbiamo aggiunto in precedenza.

Facoltativamente, possiamo includere il metodo seguente per definire le opzioni durante la scrittura di dati in area di montaggio:

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

Attualmente solo il `WritingPromised` opzione è disponibile e deve essere utilizzato quando un determinato tipo è prevista solo e non vengono scritto nell'area di montaggio. Per altre informazioni, vedere la [promesso dati](#Promised_Data) sezione precedente.

Con questi metodi posto, il codice seguente è utilizzabile per scrivere questa classe personalizzata in area di montaggio:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>La lettura da tavolo

Rispettando la conformità per i `INSPasteboardReading` , è necessario implementare l'interfaccia espone tre metodi in modo che la classe di dati personalizzate può essere lette da tavolo.

In primo luogo, è necessario indicare tavolo rappresentazioni in grado di leggere la classe personalizzata dagli Appunti di tipi di dati:

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

Anche in questo caso, questi vengono definiti come semplici uti e sono gli stessi tipi che è stato definito nel **scrivendo tavolo** sezione precedente.

Successivamente, è necessario indicare il tavolo _come_ ognuno dei tipi UTI verrà letto usando il metodo seguente:

```csharp
[Export ("readingOptionsForType:pasteboard:")]
public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSPasteboardReadingOptions.AsKeyedArchive;
    case "public.text":
        return NSPasteboardReadingOptions.AsString;
    }

    // Default to property list
    return NSPasteboardReadingOptions.AsPropertyList;
}
```

Per il `com.xamarin.image-info` tipo, si definiranno il tavolo da decodificare la coppia chiave/valore che è stata creata con il `NSKeyedArchiver` quando la scrittura della classe tavolo chiamando il `initWithCoder:` costruttore che abbiamo aggiunto alla classe.

Infine, è necessario aggiungere il metodo seguente per leggere le altre rappresentazioni di dati UTI da tavolo:

```csharp
[Export ("initWithPasteboardPropertyList:ofType:")]
public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

    // Take action based on the requested type
    switch (type) {
    case "public.text":
        return new ImageInfo();
    }

    // Failure, return null
    return null;
}
```

Tutti questi metodi posto la classe di dati personalizzate può essere lette da tavolo usando il codice seguente:

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
var classArrayPtrs = new [] { Class.GetHandle (typeof(ImageInfo)) };
NSArray classArray = NSArray.FromIntPtrs (classArrayPtrs);

// NOTE: Sending messages directly to the base Objective-C API because of this defect:
// https://bugzilla.xamarin.com/show_bug.cgi?id=31760
// Check to see if image info is on the pasteboard
ok = bool_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("canReadObjectForClasses:options:"), classArray.Handle, IntPtr.Zero);

if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = NSArray.ArrayFromHandle<Foundation.NSObject>(IntPtr_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("readObjectsForClasses:options:"), classArray.Handle, IntPtr.Zero));
    ImageInfo info = (ImageInfo)objectsToPaste[0];
}
```

### <a name="using-a-nspasteboarditem"></a>Usando un NSPasteboardItem

Talvolta potrebbe essere quando è necessario scrivere le voci personalizzate dello spazio di lavoro che non garantiscono la creazione di una classe personalizzata o si desidera fornire i dati in un formato comune, solo in base alle esigenze. Per queste situazioni, è possibile usare un `NSPasteboardItem`.

Oggetto `NSPasteboardItem` fornisce un controllo accurato dei dati che viene scritto il tavolo ed sono progettati per l'accesso temporaneo - deve essere eliminato dopo che è stata scritta da tavolo.

#### <a name="writing-data"></a>La scrittura dati

Per scrivere i dati personalizzati a un `NSPasteboardItem` sarà necessario fornire un oggetto personalizzato `NSPasteboardItemDataProvider`. Aggiungere una nuova classe al progetto e denominarlo **ImageInfoDataProvider.cs**. Modificare il file e renderlo simile al seguente:

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfoDataProvider")]
    public class ImageInfoDataProvider : NSPasteboardItemDataProvider
    {
        #region Computed Properties
        public string Name { get; set;}
        public string ImageType { get; set;}
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfoDataProvider ()
        {
        }

        public ImageInfoDataProvider (string name, string imageType)
        {
            // Initialize
            this.Name = name;
            this.ImageType = imageType;
        }

        protected ImageInfoDataProvider (NSObjectFlag t){
        }

        protected internal ImageInfoDataProvider (IntPtr handle){

        }
        #endregion

        #region Override Methods
        [Export ("pasteboardFinishedWithDataProvider:")]
        public override void FinishedWithDataProvider (NSPasteboard pasteboard)
        {
            
        }

        [Export ("pasteboard:item:provideDataForType:")]
        public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
        {

            // Take action based on the type
            switch (type) {
            case "public.text":
                // Encode the data to string 
                item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
                break;
            }

        }
        #endregion
    }
}
```

Come è stato fatto con la classe di dati personalizzate, è necessario usare il `Register` e `Export` direttive per esporla a Objective-C. La classe deve ereditare da `NSPasteboardItemDataProvider` e deve implementare il `FinishedWithDataProvider` e `ProvideDataForType` metodi.

Usare la `ProvideDataForType` metodo per fornire i dati che verranno eseguiti nel `NSPasteboardItem` come indicato di seguito:

```csharp
[Export ("pasteboard:item:provideDataForType:")]
public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
{

    // Take action based on the type
    switch (type) {
    case "public.text":
        // Encode the data to string 
        item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
        break;
    }

}
```

In questo caso, stiamo l'archiviazione dei due tipi di informazioni sui nostri immagine (nome e ImageType) e la scrittura di quelli in una stringa semplice (`public.text`).

Tipo di scrivere i dati sul tavolo, usare il codice seguente:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Using a Pasteboard Item
NSPasteboardItem item = new NSPasteboardItem();
string[] writableTypes = {"public.text"};

// Add a data provider to the item
ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

// Save to pasteboard
if (ok) {
    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
}
```

#### <a name="reading-data"></a>La lettura dei dati

Per leggere i dati da tavolo, usare il codice seguente:

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
Class [] classArray  = { new Class ("NSImage") };

bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
    NSImage image = (NSImage)objectsToPaste[0];

    // Do something with data
    ...
}
            
Class [] classArray2 = { new Class ("ImageInfo") };
ok = pasteboard.CanReadObjectForClasses (classArray2, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
    
    // Do something with data
    ...
}
```

## <a name="summary"></a>Riepilogo

Questo articolo, è state lavora sul tavolo in un'applicazione xamarin. Mac per supportare la copia e Incolla le operazioni di un'analisi approfondita. In primo luogo, ha introdotto un esempio semplice per acquisire familiarità con le operazioni di pasteboards standard. Successivamente, ha richiesto un'analisi approfondita tavolo e su come leggere e scrivere dati da esso. Infine, esaminato utilizzando un tipo di dati personalizzato per supportare le operazioni copia e Incolla dei tipi di dati complessi all'interno di un'app.



## <a name="related-links"></a>Collegamenti correlati

- [MacCopyPaste (esempio)](https://developer.xamarin.com/samples/mac/MacCopyPaste/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Guida per programmatori tavolo di montaggio](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [macOS Human Interface Guidelines](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
