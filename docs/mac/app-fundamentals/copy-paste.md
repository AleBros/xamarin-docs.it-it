---
title: Copia e incolla in Novell. Mac
description: Questo articolo illustra l'uso del tavolo di montaggio per fornire copia e incolla in un'applicazione Novell. Mac. Mostra come usare i tipi di dati standard che possono essere condivisi tra più app e come supportare i dati personalizzati in una determinata app.
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/14/2017
ms.openlocfilehash: cf6835b99ea70c3922dd68bc21af3e44815cc92e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769929"
---
# <a name="copy-and-paste-in-xamarinmac"></a>Copia e incolla in Novell. Mac

_Questo articolo illustra l'uso del tavolo di montaggio per fornire copia e incolla in un'applicazione Novell. Mac. Mostra come usare i tipi di dati standard che possono essere condivisi tra più app e come supportare i dati personalizzati in una determinata app._

## <a name="overview"></a>Panoramica

Quando si lavora C# con e .NET in un'applicazione Novell. Mac, è possibile accedere allo stesso supporto di cartone (copia e incolla) dello sviluppatore che lavora in Objective-C.

In questo articolo verranno illustrati i due modi principali per usare il tavolo di montaggio in un'app Novell. Mac:

1. **Tipi di dati standard** : poiché le operazioni di tavolo di montaggio vengono in genere eseguite tra due app non correlate, nessuna app conosce i tipi di dati supportati dagli altri. Per massimizzare il rischio di condivisione, il tavolo di montaggio può tenere più rappresentazioni di un determinato elemento (usando un set standard di tipi di dati comuni), in modo da consentire all'app di utilizzo di scegliere la versione più adatta alle proprie esigenze.
2. **Dati personalizzati** : per supportare la copia e incolla di dati complessi all'interno di Novell. Mac è possibile definire un tipo di dati personalizzato che verrà gestito dal tavolo di montaggio. Ad esempio, un'app Vector Drawing che consente all'utente di copiare e incollare forme complesse che sono costituite da più tipi di dati e punti.

[![Esempio di app in esecuzione](copy-paste-images/intro01.png "Esempio di app in esecuzione")](copy-paste-images/intro01-large.png#lightbox)

In questo articolo verranno illustrate le nozioni di base relative all'uso del tavolo di montaggio in un'applicazione Novell. Mac per supportare le operazioni di copia e incolla. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in Questo articolo.

Si consiglia di esaminare la sezione [ C# esporre classi/metodi in Objective-C](~/mac/internals/how-it-works.md) del documento [interno di Novell. Mac](~/mac/internals/how-it-works.md) , spiegando `Register` gli attributi e `Export` usati per collegare le C# classi a Oggetti Objective-C ed elementi dell'interfaccia utente.

## <a name="getting-started-with-the-pasteboard"></a>Introduzione al riquadro di montaggio

Il tavolo di montaggio presenta un meccanismo standardizzato per lo scambio di dati all'interno di una determinata applicazione o tra applicazioni. L'uso tipico di un tavolo di montaggio in un'applicazione Novell. Mac consiste nel gestire le operazioni di copia e incolla, tuttavia sono supportate anche diverse altre operazioni, ad esempio il trascinamento & l'eliminazione e la Servizi per le applicazioni.

Per iniziare rapidamente, si inizierà con una semplice introduzione pratica all'uso di Pasteboards in un'app Novell. Mac. In seguito verrà fornita una spiegazione approfondita del funzionamento del tavolo di montaggio e dei metodi utilizzati.

Per questo esempio verrà creata una semplice applicazione basata su documenti che gestisce una finestra contenente una visualizzazione immagine. L'utente potrà copiare e incollare immagini tra documenti nell'app e in o da altre app o più finestre all'interno della stessa app.

### <a name="creating-the-xamarin-project"></a>Creazione del progetto Novell

In primo luogo, si creerà una nuova app Novell. Mac basata su documenti che verrà aggiunta al supporto di copia e incolla per.

Seguire questa procedura:

1. Avviare Visual Studio per Mac e fare clic sul collegamento **nuovo progetto...** .
2. Selezionare app **Mac** > **app** > **Cocoa**, quindi fare clic sul pulsante **Avanti** : 

    [![Creazione di un nuovo progetto di app Cocoa](copy-paste-images/sample01.png "Creazione di un nuovo progetto di app Cocoa")](copy-paste-images/sample01-large.png#lightbox)
3. Immettere `MacCopyPaste` per il **nome del progetto** e Mantieni tutti gli altri come predefiniti. Fare clic su Avanti: 

    [![Impostazione del nome del progetto](copy-paste-images/sample01a.png "Impostazione del nome del progetto")](copy-paste-images/sample01a-large.png#lightbox)

4. Fare clic sul pulsante **Crea** : 

    [![Conferma delle impostazioni del nuovo progetto](copy-paste-images/sample02.png "Conferma delle impostazioni del nuovo progetto")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>Aggiungere un NSDocument

Successivamente, verrà aggiunta una `NSDocument` classe personalizzata che fungerà da archivio in background per l'interfaccia utente dell'applicazione. Conterrà una singola visualizzazione immagine e sarà in grado di copiare un'immagine dalla visualizzazione nel riquadro di montaggio predefinito e come estrarre un'immagine dal tavolo di montaggio predefinito e visualizzarla nella visualizzazione immagine.

Fare clic con il pulsante destro del mouse sul progetto Novell. Mac nella **riquadro della soluzione** e selezionare **Aggiungi** > **nuovo file..** :

![Aggiunta di un NSDocument al progetto](copy-paste-images/sample03.png "Aggiunta di un NSDocument al progetto")

Immettere `ImageDocument` in **Nome** e fare clic sul pulsante **Nuovo**. Modificare la classe **ImageDocument.cs** e renderla simile alla seguente:

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

Di seguito viene illustrato il codice illustrato in dettaglio di seguito.

Il codice seguente fornisce una proprietà che consente di verificare l'esistenza di dati immagine nel tavolo di montaggio predefinito, se è disponibile un' `true` immagine, viene `false`restituito else:

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

Nel codice seguente viene copiata un'immagine dalla visualizzazione immagine collegata al tavolo di montaggio predefinito:

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

Il codice seguente incolla un'immagine dal tavolo di montaggio predefinito e la Visualizza nella visualizzazione dell'immagine connessa (se il tavolo di montaggio contiene un'immagine valida):

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

Con questo documento verrà creata l'interfaccia utente per l'app Novell. Mac.

### <a name="building-the-user-interface"></a>Compilazione dell'interfaccia utente

Fare doppio clic sul file **Main. Storyboard** per aprirlo in Xcode. Aggiungere quindi una barra degli strumenti e un'immagine e configurarle come segue:

[![Modifica della barra degli strumenti](copy-paste-images/sample04.png "Modifica della barra degli strumenti")](copy-paste-images/sample04-large.png#lightbox)

Aggiungere un **elemento della barra degli strumenti** copia e incolla immagine sul lato sinistro della barra degli strumenti. Verranno utilizzati come collegamenti per copiare e incollare dal menu Modifica. Successivamente, aggiungere quattro **elementi della barra degli strumenti immagine** sul lato destro della barra degli strumenti. Verranno usati per popolare l'immagine con alcune immagini predefinite.

Per ulteriori informazioni sull'utilizzo delle barre degli strumenti, vedere la documentazione della [barra degli strumenti](~/mac/user-interface/toolbar.md) .

Si esporrà quindi gli Outlet e le azioni seguenti per gli elementi della barra degli strumenti e l'immagine:

[![Creazione di Outlet e azioni](copy-paste-images/sample05.png "Creazione di Outlet e azioni")](copy-paste-images/sample05-large.png#lightbox)

Per altre informazioni sull'uso di Outlet e azioni, vedere la sezione [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) della documentazione [Hello, Mac](~/mac/get-started/hello-mac.md) .

### <a name="enabling-the-user-interface"></a>Abilitazione dell'interfaccia utente

Con l'interfaccia utente creata in Xcode e l'elemento dell'interfaccia utente esposto tramite Outlet e azioni, è necessario aggiungere il codice per abilitare l'interfaccia utente. Fare doppio clic sul file **ImageWindow.cs** nel **riquadro della soluzione** e fare in modo che l'aspetto sia simile al seguente:

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

Di seguito viene illustrato il codice in dettaglio.

Prima di tutto, viene esposta un'istanza `ImageDocument` della classe creata in precedenza:

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

Usando `Export`, `WillChangeValue` e `Document` , è stata impostata la proprietà per consentire la codifica e il data binding chiave-valore in Xcode. `DidChangeValue`

Si espone anche l'immagine dall'immagine aggiunta all'interfaccia utente in Xcode con la proprietà seguente:

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

Quando la finestra principale viene caricata e visualizzata, si crea un'istanza della `ImageDocument` classe e si collega l'immagine dell'interfaccia utente con il codice seguente:

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

Infine, in risposta all'utente che fa clic sugli elementi della barra degli strumenti copia e incolla, viene chiamata l' `ImageDocument` istanza della classe per eseguire il lavoro effettivo:

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>Abilitazione del file e modifica dei menu

L'ultima cosa da fare è abilitare la **nuova** voce di menu dal menu **file** (per creare nuove istanze della finestra principale) e per abilitare le voci di menu **taglia**, **copia** e **Incolla** dal menu **Modifica** .

Per abilitare la **nuova** voce di menu, modificare il file **AppDelegate.cs** e aggiungere il codice seguente:

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

Per ulteriori informazioni, vedere la sezione [utilizzo di più finestre](~/mac/user-interface/window.md) della documentazione di [Windows](~/mac/user-interface/window.md) .

Per abilitare le voci di menu **taglia**, **copia** e **incolla** , modificare il file **AppDelegate.cs** e aggiungere il codice seguente:

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

Per ogni voce di menu, si ottiene la finestra di chiave corrente, in primo piano e ne viene `ImageWindow` eseguito il cast alla classe:

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

Da qui viene chiamata l' `ImageDocument` istanza della classe di tale finestra per gestire le azioni di copia e incolla. Ad esempio: 

```csharp
window.Document.CopyImage (sender);
```

Si desidera che le voci di menu **taglia**, **copia** e **Incolla** siano accessibili solo se sono presenti dati immagine nel tavolo di montaggio predefinito o nell'immagine della finestra attiva corrente.

Aggiungere un file **EditMenuDelegate.cs** al progetto Novell. Mac e renderlo simile al seguente:

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

Anche in questo caso, si ottiene la finestra in primo piano corrente `ImageDocument` e si usa la relativa istanza di classe per verificare se sono presenti i dati di immagine richiesti. Viene quindi usato il `MenuWillHighlightItem` metodo per abilitare o disabilitare ogni elemento in base a questo stato.

Modificare il file **AppDelegate.cs** e fare in `DidFinishLaunching` modo che il metodo sia simile al seguente:

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

In primo luogo, viene disabilitata l'abilitazione e la disabilitazione automatica delle voci di menu nel menu Modifica. Si aggiunge quindi un'istanza della `EditMenuDelegate` classe creata in precedenza.

Per ulteriori informazioni, consultare la documentazione relativa ai [menu](~/mac/user-interface/menu.md) .

### <a name="testing-the-app"></a>Test dell'app

Con tutti gli elementi disponibili, è possibile testare l'applicazione. Compilare ed eseguire l'app e viene visualizzata l'interfaccia principale:

![Esecuzione dell'applicazione](copy-paste-images/run01.png "Esecuzione dell'applicazione")

Se si apre il menu modifica, si noti che **taglia**, **copia** e **Incolla** sono disabilitati perché non è presente un'immagine nell'area dell'immagine o nel tavolo di montaggio predefinito:

![Apertura del menu modifica](copy-paste-images/run02.png "Apertura del menu modifica")

Se si aggiunge un'immagine all'immagine e si riapre il menu modifica, gli elementi verranno ora abilitati:

![Visualizzazione delle voci di menu modifica abilitata](copy-paste-images/run03.png "Visualizzazione delle voci di menu modifica abilitata")

Se si copia l'immagine e si sceglie **nuovo** dal menu file, è possibile incollare l'immagine nella nuova finestra:

![Incollare un'immagine in una nuova finestra](copy-paste-images/run04.png "Incollare un'immagine in una nuova finestra")

Nelle sezioni seguenti verrà esaminata in dettaglio l'utilizzo del tavolo di montaggio in un'applicazione Novell. Mac.

## <a name="about-the-pasteboard"></a>Informazioni sul tavolo di montaggio

In MacOS (precedentemente noto come OS X) il tavolo di`NSPasteboard`montaggio () fornisce il supporto per diversi processi del server, ad esempio copia & incolla, trascina & drop e servizi per le applicazioni. Nelle sezioni seguenti verranno esaminati in dettaglio diversi concetti chiave di un tavolo di montaggio.

### <a name="what-is-a-pasteboard"></a>Che cos'è un tavolo di montaggio?

La `NSPasteboard` classe fornisce un meccanismo standardizzato per lo scambio di informazioni tra applicazioni o all'interno di una determinata app. La funzione principale di un tavolo di montaggio è la gestione delle operazioni di copia e incolla:

1. Quando l'utente seleziona un elemento in un'app e usa la voce di menu **taglia** o **copia** , una o più rappresentazioni dell'elemento selezionato vengono posizionate nel tavolo di montaggio.
2. Quando l'utente usa la voce di menu **Incolla** (all'interno della stessa app o di un altro), la versione dei dati che può gestire viene copiata dal tavolo di montaggio e aggiunta all'app.

Gli utilizzi di cartone meno ovvi sono le operazioni di ricerca, trascinamento, trascinamento e rilascio e i servizi dell'applicazione:

- Quando l'utente avvia un'operazione di trascinamento, i dati di trascinamento vengono copiati nel tavolo di montaggio. Se l'operazione di trascinamento termina con un rilascio su un'altra app, l'app copia i dati dal tavolo di montaggio.
- Per i servizi di traduzione, i dati da tradurre vengono copiati nel tavolo di montaggio dall'app richiedente. Il servizio dell'applicazione, recupera i dati da un tavolo di montaggio, esegue la traduzione, quindi incolla i dati nel tavolo di montaggio.

Nella forma più semplice, i Pasteboards vengono usati per spostare i dati all'interno di una determinata app o tra le app e sono presenti in un'area di memoria globale speciale al di fuori del processo dell'app. Sebbene i concetti di Pasteboards siano facilmente comprensibili, è necessario prendere in considerazione diversi dettagli più complessi. Questi verranno descritti in dettaglio di seguito.

### <a name="named-pasteboards"></a>Denominato Pasteboards

Un tavolo di montaggio può essere pubblico o privato e può essere usato per diversi scopi all'interno di un'applicazione o tra più app. macOS offre diversi Pasteboards standard, ognuno con un utilizzo specifico e ben definito:

- `NSGeneralPboard`: Il tavolo di montaggio predefinito per le operazioni **taglia**, **copia** e **Incolla** .
- `NSRulerPboard`: Supporta le operazioni **taglia**, **copia** e **Incolla** sui **righelli**.
- `NSFontPboard`: Supporta operazioni **taglia**, **copia** e **Incolla** sugli `NSFont` oggetti.
- `NSFindPboard`: Supporta i pannelli di ricerca specifici dell'applicazione che possono condividere il testo di ricerca.
- `NSDragPboard`: Supporta le operazioni di **Trascinamento & rilascio** .

Per la maggior parte delle situazioni, si userà uno dei Pasteboards definiti dal sistema. Tuttavia potrebbero essere presenti situazioni che richiedono la creazione di Pasteboards personalizzati. In questi casi, è possibile usare il `FromName (string name)` metodo `NSPasteboard` della classe per creare un tavolo di montaggio personalizzato con il nome specificato.

Facoltativamente, è possibile chiamare il `CreateWithUniqueName` metodo `NSPasteboard` della classe per creare un oggetto di cartone denominato in modo univoco.

### <a name="pasteboard-items"></a>Elementi di cartone

Ogni parte dei dati scritti da un'applicazione in un tavolo di montaggio viene considerata come un _elemento di cartone_ e un tavolo di montaggio può tenere più elementi contemporaneamente. In questo modo, un'app può scrivere più versioni dei dati copiati in un tavolo di montaggio (ad esempio, testo normale e testo formattato) e l'app di recupero può leggere solo i dati che è in grado di elaborare, ad esempio solo testo normale.

### <a name="data-representations-and-uniform-type-identifiers"></a>Rappresentazioni di dati e identificatori di tipi uniformi

In genere, le operazioni di montaggio avvengono tra due o più applicazioni che non hanno alcuna conoscenza dell'altra o dei tipi di dati che possono essere gestiti da ciascuno. Come indicato nella sezione precedente, per massimizzare il rischio di condivisione delle informazioni, un tavolo di montaggio può mantenere più rappresentazioni dei dati copiati e incollati.

Ogni rappresentazione viene identificata tramite un identificatore di tipo uniforme (UTI), che non è nient'altro che una semplice stringa che identifica in modo univoco il tipo di data presentato (per altre informazioni, vedere [Cenni preliminari sugli identificatori di tipo uniforme](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) di Apple). documentazione). 

Se si sta creando un tipo di dati personalizzato (ad esempio, un oggetto Drawing in un'app Vector Drawing), è possibile creare il proprio UTI per identificarlo in modo univoco nelle operazioni di copia e incolla.

Quando un'applicazione viene preparata per incollare i dati copiati da un tavolo di montaggio, deve trovare la rappresentazione più adatta alle proprie capacità (se presente). Si tratta in genere del tipo più completo disponibile, ad esempio un testo formattato per un'app per l'elaborazione di testi, che esegue il fallback alle forme più semplici disponibili come richiesto (testo normale per un semplice editor di testo).

<a name="Promised_Data" />

### <a name="promised-data"></a>Dati promessi

In generale, è necessario fornire il maggior numero di rappresentazioni dei dati copiati il più possibile per ottimizzare la condivisione tra le app. Tuttavia, a causa dei vincoli di tempo o di memoria, potrebbe non essere pratico scrivere effettivamente ogni tipo di dati nel tavolo di montaggio.

In questa situazione, è possibile inserire la prima rappresentazione di dati nel tavolo di montaggio e l'app ricevente può richiedere una rappresentazione diversa, che può essere generata immediatamente immediatamente prima dell'operazione Incolla.

Quando si inserisce l'elemento iniziale nel tavolo di montaggio, si specifica che una o più delle altre rappresentazioni disponibili vengono fornite da un oggetto conforme all' `NSPasteboardItemDataProvider` interfaccia. Questi oggetti forniranno le rappresentazioni aggiuntive su richiesta, come richiesto dall'applicazione ricevente.

### <a name="change-count"></a>Conteggio modifiche

Ogni tavolo di montaggio mantiene un _conteggio delle modifiche_ che incrementa ogni volta che viene dichiarato un nuovo proprietario. Un'app può determinare se il contenuto della tavolozza è stato modificato dall'ultima volta in cui è stato esaminato controllando il valore del conteggio delle modifiche.

Usare i `ChangeCount` metodi `ClearContents` e della `NSPasteboard` classe per modificare il numero di modifiche di un tavolo di montaggio specifico.

## <a name="copying-data-to-a-pasteboard"></a>Copia dei dati in un tavolo di montaggio

Per eseguire un'operazione di copia è necessario prima accedere a un tavolo di montaggio, cancellare tutti i contenuti esistenti e scrivere il numero di rappresentazioni dei dati necessari per il tavolo di montaggio.

Ad esempio:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

In genere, è sufficiente scrivere sul tavolo di montaggio generale, come è stato fatto nell'esempio precedente. Qualsiasi oggetto inviato al `WriteObjects` metodo *deve* essere conforme all' `INSPasteboardWriting` interfaccia. Diverse classi predefinite, ad esempio `NSString` `NSURL`, `NSImage`,, `NSColor`, `NSAttributedString`e `NSPasteboardItem`, sono conformi automaticamente a questa interfaccia.

Se si scrive una classe di dati personalizzata nel tavolo di montaggio, deve essere conforme all' `INSPasteboardWriting` interfaccia o essere racchiusa in un'istanza `NSPasteboardItem` della classe (vedere la sezione tipi di [dati personalizzati](#Custom_Data_Types) più avanti).

## <a name="reading-data-from-a-pasteboard"></a>Lettura di dati da un tavolo di montaggio

Come indicato in precedenza, per massimizzare il rischio di condivisione dei dati tra le app, è possibile scrivere più rappresentazioni dei dati copiati nel tavolo di montaggio. Spetta all'app ricevente selezionare la versione più ricca possibile per le sue funzionalità (se presente).

### <a name="simple-paste-operation"></a>Operazione Incolla semplice

È possibile leggere i dati dal tavolo di `ReadObjectsForClasses` montaggio usando il metodo. Sono necessari due parametri:

1. Matrice di tipi `NSObject` di classe basati che si desidera leggere dal tavolo di montaggio. Per prima cosa è necessario ordinare il tipo di dati più desiderato, con qualsiasi tipo rimanente in preferenza decrescente.
2. Dizionario contenente vincoli aggiuntivi, ad esempio la limitazione a tipi di contenuto di URL specifici, oppure un dizionario vuoto se non sono necessari altri vincoli.

Il metodo restituisce una matrice di elementi che soddisfano i criteri passati e che quindi contengono al massimo lo stesso numero di tipi di dati richiesti. è anche possibile che nessuno dei tipi richiesti sia presente e che venga restituita una matrice vuota.

Il codice seguente, ad esempio, verifica se esiste un `NSImage` oggetto nel tavolo di montaggio generale e lo Visualizza in un'immagine in un secondo caso:

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

### <a name="requesting-multiple-data-types"></a>Richiesta di più tipi di dati

In base al tipo di applicazione Novell. Mac da creare, può essere in grado di gestire più rappresentazioni dei dati incollati. In questa situazione sono disponibili due scenari per il recupero dei dati dal tavolo di montaggio:

1. Eseguire una singola chiamata al `ReadObjectsForClasses` metodo e fornire una matrice di tutte le rappresentazioni desiderate (nell'ordine preferenziale).
2. Eseguire più chiamate al `ReadObjectsForClasses` metodo richiedendo ogni volta una matrice di tipi diversa.

Per ulteriori informazioni sul recupero di dati da un tavolo di montaggio, vedere la sezione **operazione Incolla semplice** precedente.

### <a name="checking-for-existing-data-types"></a>Verifica dei tipi di dati esistenti

In alcuni casi potrebbe essere necessario controllare se un tavolo di montaggio contiene una data rappresentazione dati senza effettivamente leggere i dati dal tavolo di montaggio (ad esempio, abilitando la voce di menu **Incolla** solo quando esistono dati validi).

Chiamare il `CanReadObjectForClasses` metodo del tavolo di montaggio per verificare se contiene un tipo specificato.

Il codice seguente, ad esempio, determina se il tavolo di montaggio `NSImage` generale contiene un'istanza:

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

### <a name="reading-urls-from-the-pasteboard"></a>Lettura degli URL dal tavolo di montaggio

In base alla funzione di un'app Novell. Mac specificata, potrebbe essere necessario leggere gli URL da un tavolo di montaggio, ma solo se soddisfano un determinato set di criteri, ad esempio puntando a file o URL di un tipo di dati specifico. In questa situazione è possibile specificare criteri di ricerca aggiuntivi usando il secondo parametro del `CanReadObjectForClasses` metodo o. `ReadObjectsForClasses`

<a name="Custom_Data_Types" />

## <a name="custom-data-types"></a>Tipi di dati personalizzati

In alcuni casi è necessario salvare i tipi personalizzati nel tavolo di montaggio da un'app Novell. Mac. Ad esempio, un'app Vector Drawing che consente all'utente di copiare e incollare oggetti Drawing.

In questa situazione è necessario progettare la classe personalizzata dei dati in modo che erediti `NSObject` da ed è conforme a alcune interfacce (`INSCoding`, `INSPasteboardWriting` e `INSPasteboardReading`). Facoltativamente, è possibile utilizzare un `NSPasteboardItem` oggetto per incapsulare i dati da copiare o incollare.

Entrambe queste opzioni verranno descritte in dettaglio di seguito.

### <a name="using-a-custom-class"></a>Uso di una classe personalizzata

In questa sezione si espanderà l'app semplice di esempio creata all'inizio di questo documento e si aggiungerà una classe personalizzata per tenere traccia delle informazioni sull'immagine che si sta copiando e incollando tra le finestre.

Aggiungere una nuova classe al progetto e denominarla **IMAGEINFO.cs**. Modificare il file e renderlo simile al seguente:

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

Nelle sezioni seguenti verrà esaminata in dettaglio questa classe.

#### <a name="inheritance-and-interfaces"></a>Ereditarietà e interfacce

Prima che una classe di dati personalizzata possa essere scritta o letta da un tavolo di montaggio, deve essere `INSPastebaordWriting` conforme alle interfacce e `INSPasteboardReading` . Inoltre, deve ereditare da `NSObject` e essere conforme `INSCoding` all'interfaccia:

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

La classe deve essere esposta anche a Objective-C usando la `Register` direttiva ed è necessario esporre le proprietà o i metodi richiesti `Export`usando. Ad esempio:

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

Verranno esposti i due campi di dati che questa classe conterrà: il nome dell'immagine e il relativo tipo (jpg, PNG e così via). 

Per ulteriori informazioni, vedere la [sezione esporre C# classi/metodi a Objective-C](~/mac/internals/how-it-works.md) della documentazione [interna di Novell. Mac](~/mac/internals/how-it-works.md) , spiega gli `Register` attributi e `Export` usati per collegare le C# classi a Oggetti Objective-C ed elementi dell'interfaccia utente.

#### <a name="constructors"></a>Costruttori

Due costruttori (correttamente esposti a Objective-C) saranno necessari per la classe di dati personalizzata in modo che possano essere letti da un tavolo di montaggio:

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

In primo luogo, il costruttore _vuoto_ viene esposto nel metodo Objective-C predefinito `init`di.

Successivamente, viene esposto un `NSCoding` Costruttore conforme che verrà utilizzato per creare una nuova istanza dell'oggetto dal tavolo di montaggio quando si incolla con il nome esportato `initWithCoder`di.

Questo costruttore accetta un `NSCoder` oggetto, creato da un `NSKeyedArchiver` oggetto quando viene scritto nel tavolo di montaggio, estrae i dati associati chiave/valore e li salva nei campi delle proprietà della classe di dati.

#### <a name="writing-to-the-pasteboard"></a>Scrittura nel tavolo di montaggio

Conformemente all' `INSPasteboardWriting` interfaccia, è necessario esporre due metodi e, facoltativamente, un terzo metodo, in modo che la classe possa essere scritta nel tavolo di montaggio.

In primo luogo, è necessario indicare al tavolo di montaggio il tipo di dati di cui è possibile scrivere la classe personalizzata:

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

Ogni rappresentazione viene identificata tramite un identificatore di tipo uniforme (UTI), che non è nient'altro che una semplice stringa che identifica in modo univoco il tipo di dati presentati (per altre informazioni, vedere [Cenni preliminari sugli identificatori di tipo uniforme](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) di Apple). documentazione).

Per il nostro formato personalizzato, creiamo il nostro UTI: "com. Novell. Image-Info" (si noti che è in notazione inversa esattamente come un identificatore di app). La nostra classe è anche in grado di scrivere una stringa standard nel tavolo`public.text`di montaggio (). 

Successivamente, è necessario creare l'oggetto nel formato richiesto che venga effettivamente scritto nel tavolo di montaggio:

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

Per il `public.text` tipo, viene restituito un semplice oggetto formattato `NSString` . Per il tipo `com.xamarin.image-info` personalizzato, viene usato un oggetto `NSKeyedArchiver` e l' `NSCoder` interfaccia per codificare la classe di dati personalizzata in un archivio associato chiave/valore. È necessario implementare il metodo seguente per gestire effettivamente la codifica:

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

Le singole coppie chiave/valore vengono scritte nel codificatore e verranno decodificate usando il secondo costruttore aggiunto in precedenza.

Facoltativamente, è possibile includere il metodo seguente per definire qualsiasi opzione durante la scrittura dei dati nel tavolo di montaggio:

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

Attualmente solo l' `WritingPromised` opzione è disponibile e deve essere utilizzata quando un tipo specificato viene promesso e non effettivamente scritto nel tavolo di montaggio. Per ulteriori informazioni, vedere la sezione relativa ai [dati promessi](#Promised_Data) sopra.

Con questi metodi, è possibile usare il codice seguente per scrivere la classe personalizzata nel tavolo di montaggio:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>Lettura dal tavolo di montaggio

Conformemente all' `INSPasteboardReading` interfaccia, è necessario esporre tre metodi in modo che la classe di dati personalizzata possa essere letta dal tavolo di montaggio.

In primo luogo, è necessario indicare al tavolo di montaggio il tipo di dati che la classe personalizzata può leggere dagli Appunti:

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

Anche in questo caso, questi sono definiti come semplici uti e sono gli stessi tipi definiti nella sezione scrittura nella sezione **di montaggio** riportata in precedenza.

Successivamente, è necessario indicare al tavolo di montaggio _come_ ognuno dei tipi uti verrà letto usando il metodo seguente:

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

Per il `com.xamarin.image-info` tipo, viene indicato al tavolo di montaggio di decodificare la coppia chiave/valore creata `NSKeyedArchiver` con quando si scrive la classe nel tavolo di montaggio chiamando il `initWithCoder:` Costruttore aggiunto alla classe.

Infine, è necessario aggiungere il metodo seguente per leggere le altre rappresentazioni di dati UTI dal tavolo di montaggio:

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

Con tutti questi metodi sul posto, la classe di dati personalizzata può essere letta dal tavolo di montaggio usando il codice seguente:

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

### <a name="using-a-nspasteboarditem"></a>Uso di un NSPasteboardItem

In alcuni casi è possibile che sia necessario scrivere elementi personalizzati nel tavolo di montaggio che non garantiscano la creazione di una classe personalizzata o che si desideri fornire dati in un formato comune, solo se necessario. Per queste situazioni, è possibile usare un `NSPasteboardItem`.

Un `NSPasteboardItem` oggetto offre un controllo granulare dei dati scritti nel tavolo di montaggio ed è progettato per l'accesso temporaneo, ma deve essere eliminato dopo essere stato scritto nel tavolo di montaggio.

#### <a name="writing-data"></a>Scrittura di dati

Per scrivere i dati personalizzati in un `NSPasteboardItem` , è necessario fornire un oggetto personalizzato `NSPasteboardItemDataProvider`. Aggiungere una nuova classe al progetto e denominarla **ImageInfoDataProvider.cs**. Modificare il file e renderlo simile al seguente:

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

Come è stato fatto con la classe di dati personalizzata, è necessario usare `Register` le `Export` direttive e per esporla a Objective-C. La classe deve ereditare `NSPasteboardItemDataProvider` da e deve implementare `FinishedWithDataProvider` i `ProvideDataForType` metodi e.

Usare il `ProvideDataForType` metodo per fornire i dati di cui verrà eseguito il wrapper `NSPasteboardItem` nel come indicato di seguito:

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

In questo caso vengono archiviate due informazioni sull'immagine (Name e ImageType) e scritte in una stringa semplice (`public.text`).

Digitare scrivere i dati nel tavolo di montaggio, usare il codice seguente:

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

#### <a name="reading-data"></a>Lettura di dati

Per leggere nuovamente i dati dal tavolo di montaggio, usare il codice seguente:

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

Questo articolo ha esaminato in dettaglio l'uso del tavolo di montaggio in un'applicazione Novell. Mac per supportare le operazioni di copia e incolla. In primo luogo, è stato introdotto un semplice esempio per acquisire familiarità con le operazioni Pasteboards standard. Successivamente, è stata esaminata dettagliatamente il cartone e viene illustrato come leggere e scrivere i dati. Infine, è stato esaminato l'uso di un tipo di dati personalizzato per supportare la copia e incolla di tipi di dati complessi all'interno di un'app.

## <a name="related-links"></a>Collegamenti correlati

- [MacCopyPaste (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/maccopypaste)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Guida per programmatori di cartone](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [Linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
