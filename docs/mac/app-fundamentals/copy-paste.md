---
title: Copiare e incollare
description: "In questo articolo viene descritto l'utilizzo con tavolo per fornire copia e Incolla in un'applicazione Xamarin.Mac. Viene illustrato come lavorare con i tipi di dati standard che possono essere condivisi tra più App e come supportare dati personalizzati all'interno di un'applicazione specificata."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: ba937a6eae7f0f74bcf044f1248d49a421e82de5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="copy-and-paste"></a>Copiare e incollare

_In questo articolo viene descritto l'utilizzo con tavolo per fornire copia e Incolla in un'applicazione Xamarin.Mac. Viene illustrato come lavorare con i tipi di dati standard che possono essere condivisi tra più App e come supportare dati personalizzati all'interno di un'applicazione specificata._

## <a name="overview"></a>Panoramica

Quando si utilizza c# e .NET in un'applicazione Xamarin.Mac, è possibile accedere a quello stesso tavolo di montaggio (copia e Incolla) che dispone di uno sviluppatore che lavora in Objective-C.

In questo articolo è verranno illustrati i due modi principali per utilizzare tavolo in un'app Xamarin.Mac:

1. **Tipi di dati standard** -poiché tavolo di montaggio sono in genere eseguite operazioni tra due applicazioni non correlate, nessuna delle due app conosce i tipi di dati che supporta l'altro. Per ottimizzare il potenziale per la condivisione, tavolo può contenere più rappresentazioni di un elemento specificato (utilizzando un set standard di tipi di dati), in questo modo l'app dispendiosa in termini di scegliere la versione più adatta per le esigenze.
2. **Dati personalizzati** : per supportare la copia e Incolla di dati complessi all'interno del Xamarin.Mac è possibile definire un tipo di dati personalizzato che verrà gestito dal tavolo. Ad esempio, un'applicazione di disegno vettoriale che consente all'utente di copiare e incollare forme complesse costituite da più tipi di dati e i punti.

[![Esempio di app in esecuzione](copy-paste-images/intro01.png "esempio di app in esecuzione")](copy-paste-images/intro01-large.png#lightbox)

In questo articolo verranno descritte le nozioni di base dell'utilizzo in un'applicazione Xamarin.Mac per supportare la copia e Incolla operazioni tavolo. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile dare un'occhiata il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` gli attributi utilizzato per associare le classi c# Objective-C e agli oggetti dell'interfaccia utente gli elementi.

## <a name="getting-started-with-the-pasteboard"></a>Introduzione a tavolo

Tavolo presenta un meccanismo standard per lo scambio di dati in una determinata applicazione o tra applicazioni. L'utilizzo tipico per un tavolo in un'applicazione Xamarin.Mac è per gestire copia e Incolla di operazioni, ma un numero di altre operazioni è anche supportato (ad esempio trascinamento e rilascio e i servizi delle applicazioni).

Per ottenere rapidamente è off zero, verrà inizia con una semplice e pratica Introduzione all'utilizzo pasteboards in un'app Xamarin.Mac. Successivamente, verrà fornita una spiegazione dettagliata del funzionamento di tavolo e i metodi utilizzati.

Per questo esempio, che verrà creata un'applicazione semplice documento in base che gestisce una finestra contenente una visualizzazione di un'immagine. L'utente sarà in grado di copiare e incollare le immagini tra i documenti nell'app e a o da altre App o più finestre all'interno dell'applicazione stessa.

### <a name="creating-the-xamarin-project"></a>Creazione del progetto Xamarin

Verrà innanzitutto, creare una nuova app Xamarin.Mac documento in base che è verrà aggiunta una copia e Incolla per.

Seguire questa procedura:

1. Avviare Visual Studio per Mac e fare clic su di **nuovo progetto...**  collegamento.
2. Selezionare **Mac** > **App** > **Cocoa App**, quindi fare clic su di **Avanti** pulsante: 

    [![Crea un nuovo progetto di app Cocoa](copy-paste-images/sample01.png "creando un nuovo progetto di app Cocoa")](copy-paste-images/sample01-large.png#lightbox)
3. Immettere `MacCopyPaste` per il **nome progetto** e mantenere tutto il resto come predefinito. Fare clic su Avanti: 

    [![L'impostazione del nome del progetto](copy-paste-images/sample01a.png "l'impostazione del nome del progetto")](copy-paste-images/sample01a-large.png#lightbox)

4. Fare clic su di **crea** pulsante: 

    [![Per confermare le nuove impostazioni di progetto](copy-paste-images/sample02.png "per confermare le nuove impostazioni di progetto")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>Aggiungere un NSDocument

Successivamente sarà necessario aggiungere personalizzato `NSDocument` classe che verrà utilizzato per l'archiviazione di sfondo per l'interfaccia utente dell'applicazione. Verrà contengono una singola visualizzazione di immagini e sapere come copia un'immagine dalla visualizzazione in tavolo predefinito e come acquisire un'immagine da tavolo predefinito e visualizzarlo nella visualizzazione immagine.

Pulsante destro del mouse sul progetto Xamarin.Mac il **soluzione riempimento** e selezionare **Aggiungi** > **nuovo File...** :

![Aggiunta di un NSDocument al progetto](copy-paste-images/sample03.png "aggiunta di un NSDocument al progetto")

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

Esaminiamo una parte del codice in dettaglio di seguito.

Il codice seguente fornisce una proprietà per verificare l'esistenza di dati dell'immagine sul tavolo di montaggio predefinito, se un'immagine è disponibile, `true` viene restituito in caso contrario `false`:

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

Il codice seguente consente di copiare un'immagine dalla visualizzazione immagine associata in tavolo predefinito:

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

E il codice seguente consente di incollare un'immagine da tavolo predefinito e viene visualizzata nella visualizzazione immagine associato (se tavolo contiene un'immagine valida):

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

Con questo documento sul posto, si creerà l'interfaccia utente per l'app Xamarin.Mac.

### <a name="building-the-user-interface"></a>Creazione dell'interfaccia utente

Fare doppio clic su di **Main** file per aprirlo in Xcode. Successivamente, aggiungere una barra degli strumenti e un'immagine anche e configurarli come illustrato di seguito:

[![Modifica la barra degli strumenti](copy-paste-images/sample04.png "la barra degli strumenti di modifica")](copy-paste-images/sample04-large.png#lightbox)

Aggiungere una copia e Incolla **immagine della barra degli strumenti** sul lato sinistro della barra degli strumenti. Verrà usato come tasti di scelta rapida per copiare e incollare dal menu Modifica. Successivamente, aggiungere quattro **immagine della barra degli strumenti elementi** sul lato destro della barra degli strumenti. Questi verrà utilizzata per popolare l'immagine con alcune immagini predefinite.

Per ulteriori informazioni sull'utilizzo di barre degli strumenti, vedere il nostro [barre degli strumenti](~/mac/user-interface/toolbar.md) documentazione.

Successivamente, si espongono anche le prese e le azioni per il nostro gli elementi della barra degli strumenti e l'immagine seguente:

[![Creazione di azioni e prese](copy-paste-images/sample05.png "la creazione di punti vendita e azioni")](copy-paste-images/sample05-large.png#lightbox)

Per ulteriori informazioni sull'utilizzo dei punti vendita e le azioni, vedere il [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) sezione del nostro [Hello, Mac](~/mac/get-started/hello-mac.md) documentazione.

### <a name="enabling-the-user-interface"></a>Abilitazione dell'interfaccia utente

Tramite l'interfaccia utente creato in Xcode e l'elemento dell'interfaccia utente vengono esposte tramite punti vendita e le azioni, è necessario aggiungere il codice per abilitare l'interfaccia utente. Fare doppio clic su di **ImageWindow.cs** file nel **soluzione riempimento** e renderlo simile al seguente:

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

Utilizzando `Export`, `WillChangeValue` e `DidChangeValue`, è necessario il programma di installazione di `Document` proprietà per consentire la generazione di codice chiave-valore e Data Binding in Xcode.

Inoltre, è necessario esporre l'immagine dall'immagine che anche abbiamo aggiunto per l'interfaccia utente in Xcode con la seguente proprietà:

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

Quando la finestra principale viene caricata e visualizzata, viene creata un'istanza del nostro `ImageDocument` classe e collegare l'immagine dell'interfaccia utente e a esso con il codice seguente:

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

Infine, in risposta all'utente facendo clic sugli elementi della barra degli strumenti di copia e Incolla, viene chiamato l'istanza del `ImageDocument` classe per eseguire il lavoro effettivo:

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>Abilitare i menu File e modifica

L'ultima operazione, è necessario eseguire è attiva la **New** voce di menu dal **File** menu (per creare nuove istanze della finestra principale) e per abilitare il **Taglia**, **copia**  e **Incolla** voci di menu dal **modifica** menu.

Per abilitare il **New** menu item, modificare il **appdelegate. cs** file e aggiungere il codice seguente:

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

Per ulteriori informazioni, vedere il [utilizzo di più finestre](~/mac/user-interface/window.md) sezione del nostro [Windows](~/mac/user-interface/window.md) documentazione.

Per abilitare il **Taglia**, **copia** e **Incolla** voci di menu, modificare il **appdelegate. cs** file e aggiungere il codice seguente:

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

Per ogni voce di menu, ottenere la finestra corrente, in primo piano, chiave e il cast al nostro `ImageWindow` classe:

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

Da qui è chiamare il `ImageDocument` istanza della classe di tale finestra per gestire la copia e Incolla di azioni. Ad esempio: 

```csharp
window.Document.CopyImage (sender);
```

Si vuole inserire solo **Taglia**, **copia** e **Incolla** voci di menu deve essere accessibile nel caso i dati sul tavolo di montaggio predefinito o nell'immagine e della finestra attiva corrente dell'immagine.

Aggiungere un **EditMenuDelegate.cs** file al progetto Xamarin.Mac e renderlo simile al seguente:

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

Nuovamente, è ottenere la finestra corrente, in primo piano e il relativo `ImageDocument` istanza della classe per vedere se i dati dell'immagine richiesto esistano. Successivamente si utilizza il `MenuWillHighlightItem` metodo per abilitare o disabilitare ogni elemento di base in questo stato.

Modificare il **appdelegate. cs** file e verificare il `DidFinishLaunching` metodo aspetto simile al seguente:
 
```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

È in primo luogo, disabilitare l'attivazione automatica e la disabilitazione di voci di menu dal menu Modifica. Successivamente, si collega un'istanza del `EditMenuDelegate` classe creati in precedenza.

Per ulteriori informazioni, vedere il nostro [menu](~/mac/user-interface/menu.md) documentazione.

### <a name="testing-the-app"></a>Test dell'app

Con tutti gli elementi sul posto, si è pronti per testare l'applicazione. Compilare ed eseguire l'app e viene visualizzata l'interfaccia principale:

![Esecuzione dell'applicazione](copy-paste-images/run01.png "esecuzione dell'applicazione")

Se si apre il menu di modifica, si noti che **Taglia**, **copia** e **Incolla** sono disabilitate perché non è presente alcuna immagine nell'immagine anche o nell'area di montaggio predefinito:

![Aprire il menu di modifica](copy-paste-images/run02.png "aprendo il menu di modifica")

Se si aggiungere anche un'immagine per l'immagine e riapre il menu di modifica, gli elementi verranno abilitati:

![Visualizzare le voci di menu di modifica sono abilitati](copy-paste-images/run03.png "che mostra le voci di menu di modifica sono abilitati.")

Se si copia l'immagine e selezionare **New** dal menu file, è possibile incollare tale immagine in nuova finestra:

![Incollare un'immagine in una nuova finestra](copy-paste-images/run04.png "incollare un'immagine in una nuova finestra")

Nelle sezioni seguenti, prenderemo in un'analisi approfondita sul tavolo in un'applicazione Xamarin.Mac utilizzo.

## <a name="about-the-pasteboard"></a>Sull'area di montaggio

In (precedentemente noto come OS X) macOS tavolo (`NSPasteboard`) fornisce il supporto di più server elabora ad esempio copiare e incollare, trascinamento della selezione e i servizi delle applicazioni. Nelle sezioni seguenti, prenderemo in informazioni dettagliate su alcuni concetti tavolo di montaggio.

### <a name="what-is-a-pasteboard"></a>Che cos'è un tavolo?

La `NSPasteboard` classe fornisce un meccanismo standard per lo scambio di informazioni tra applicazioni o all'interno di un'applicazione specificata. La funzione principale di un tavolo è per la gestione delle operazioni di copia e Incolla:

1. Quando l'utente seleziona un elemento in un'app e utilizza il **Taglia** o **copia** voce di menu, vengono installate uno o più rappresentazioni dell'elemento selezionato sul tavolo di montaggio.
2. Quando l'utente utilizza il **Incolla** voce di menu (all'interno della stessa applicazione o uno diverso), la versione dei dati in grado di gestire viene copiata da tavolo e aggiunta all'app.

Usa tavolo di montaggio meno ovvio include trova, trascinare, trascinamento della selezione e le operazioni di servizi delle applicazioni:

- Quando l'utente avvia un'operazione di trascinamento, i dati di trascinamento viene copiati tavolo. Se l'operazione di trascinamento termina con un'eliminazione in un'altra app, tale app copia i dati da tavolo.
- Per i servizi di traduzione, il conversione dei dati viene copiati da tavolo dall'applicazione richiedente. Il servizio dell'applicazione, recupera i dati da tavolo, la traduzione, quindi eseguire il backup consente di incollare i dati tavolo.

Nella forma più semplice, pasteboards vengono utilizzati per spostare i dati all'interno di un'applicazione specificata o tra le App e, pertanto, esiste in un'area di memoria globale speciale all'esterno del processo dell'applicazione. Mentre i concetti del pasteboards sono facilmente grasps, vi sono diversi dettagli più complessi che devono essere considerati. Questi verrà descritta in dettaglio di seguito.

### <a name="named-pasteboards"></a>Pasteboards denominata

Un tavolo può essere pubblico o privato e può essere utilizzata per vari scopi all'interno di un'applicazione o tra più app. sono disponibili diversi pasteboards standard, ognuna con un utilizzo specifico, ben definito, macOS:

- `NSGeneralPboard` -Tavolo predefinito per **Taglia**, **copia** e **Incolla** operazioni.
- `NSRulerPboard` -Supporta **Taglia**, **copia** e **Incolla** operazioni su **righelli**.
- `NSFontPboard` -Supporta **Taglia**, **copia** e **Incolla** operazioni su `NSFont` oggetti.
- `NSFindPboard` -Supporta specifiche dell'applicazione Trova pannelli che possono condividere testo di ricerca.
- `NSDragPboard` -Supporta **trascinamento** operazioni.

Per la maggior parte dei casi, si userà una del pasteboards definiti dal sistema. Ma ci sono situazioni in cui è necessario creare la propria pasteboards. In questi casi, è possibile utilizzare il `FromName (string name)` metodo la `NSPasteboard` classe per creare un tavolo personalizzato con il nome specificato.

Facoltativamente, è possibile chiamare il `CreateWithUniqueName` metodo la `NSPasteboard` classe per creare un nome univoco tavolo.

### <a name="pasteboard-items"></a>Elementi tavolo di montaggio

Ogni dato che un'applicazione scrive un tavolo è considerato un _elemento tavolo_ e un tavolo può contenere più elementi contemporaneamente. In questo modo, un'applicazione può scrivere più versioni dei dati vengono copiati in un tavolo (ad esempio, testo normale e testo formattato) e il recupero delle app possono leggere solo i dati che consente di elaborare (ad esempio, di solo testo normale).

### <a name="data-representations-and-uniform-type-identifiers"></a>Rappresentazioni di dati e gli identificatori di tipo uniforme

Tavolo di montaggio operazioni richiedono in genere le applicazioni tra due (o più) che non sono a conoscenza della loro o i tipi di dati che è possibile gestire ogni. Come indicato nella sezione precedente, per ottimizzare il potenziale per la condivisione di informazioni, un tavolo può contenere più rappresentazioni dei dati viene copiato e incollato.

Ogni rappresentazione è identificato tramite un Uniform tipo identificatore (UTI), che è semplicemente una stringa semplice che identifica in modo univoco il tipo di data presentato (per ulteriori informazioni, vedere Apple [Uniform Panoramica di identificatori di tipo ](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) documentazione). 

Se si sta creando un tipo di dati personalizzati (ad esempio, un disegno in un vettore di disegno app), è possibile creare la propria UTI per facilitarne l'identificazione nella copia in modo univoco e operazioni di copia.

Quando un'app Prepara incollare dati copiati da un tavolo, è necessario individuare la rappresentazione che meglio si adatta il possibilità (se presente). In genere si tratterà il tipo più completo disponibile (testo formattato, ad esempio per un'applicazione di elaborazione testi), eseguire il fallback ai moduli più semplice disponibile come richiesto (testo normale per un semplice editor di testo).

<a name="Promised_Data" />

### <a name="promised-data"></a>Dati promessi

In generale, è necessario fornire tante rappresentazioni dei dati copiati possibili per ottimizzare la condivisione tra le app. A causa di vincoli di tempo o memoria, tuttavia, potrebbe risultare poco scrivere effettivamente ogni tipo di dati nell'area di montaggio.

In questo caso, è possibile inserire la prima rappresentazione dei dati sul tavolo di montaggio e l'applicazione ricevente può richiedere una rappresentazione diversa, che può essere generato in immediatamente, appena prima dell'operazione Incolla.

Quando si inserisce l'elemento iniziale nell'area di montaggio, è necessario specificare che uno o più altre rappresentazioni disponibili vengono fornite da un oggetto che è conforme al `NSPasteboardItemDataProvider` interfaccia. Questi oggetti fornirà le rappresentazioni aggiuntive su richiesta, come richiesto dall'applicazione ricevente.

### <a name="change-count"></a>Conteggio modifiche

Ogni tavolo mantiene un _Conteggio modifiche_ che incrementi ogni volta che un nuovo proprietario è dichiarato. Un'app è possibile determinare se il contenuto dell'area di montaggio è stato modificato dall'ultima volta che viene esaminata controllando il valore di conteggio modifiche.

Utilizzare il `ChangeCount` e `ClearContents` metodi del `NSPasteboard` classe modificare il numero di modifiche del tavolo di montaggio specificato.

## <a name="copying-data-to-a-pasteboard"></a>Copia dei dati su un tavolo

Eseguire un'operazione di copia prima l'accesso a un tavolo, deselezionando qualsiasi contenuto esistente e la scrittura delle tante rappresentazioni dei dati come sono necessari per l'area di montaggio.

Ad esempio:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

In genere, è sufficiente saranno scritti per tavolo generale, come nell'esempio precedente. Qualsiasi oggetto che si invia al `WriteObjects` metodo *deve* conformi al `INSPasteboardWriting` interfaccia. Classi predefinite diverse, (ad esempio `NSString`, `NSImage`, `NSURL`, `NSColor`, `NSAttributedString`, e `NSPasteboardItem`) automaticamente sono conformi a questa interfaccia.

Se si sta scrivendo una classe di dati personalizzati a tavolo deve essere conforme al `INSPasteboardWriting` interfaccia o eseguire il wrapping in un'istanza del `NSPasteboardItem` classe (vedere il [tipi di dati personalizzati](#Custom_Data_Types) sezione riportata di seguito).

## <a name="reading-data-from-a-pasteboard"></a>Lettura di dati da un tavolo

Come descritto in precedenza, per ottimizzare il potenziale per condividere dati tra App, più rappresentazioni dei dati copiati potrebbero essere scritti tavolo. È compito dell'applicazione ricevente per selezionare la versione più completa possibile per le funzionalità (se presente).

### <a name="simple-paste-operation"></a>Operazione Incolla semplice

Leggere dati dalla tavolo di montaggio tramite la `ReadObjectsForClasses` metodo. Sarà necessario due parametri:

1. Matrice di `NSObject` basato su tipi di classe che si desidera leggere da tavolo. È consigliabile ordinare questo con il tipo di dati più desiderato in primo luogo, con eventuali tipi rimanenti nella preferenza decrescente.
2. Un dizionario che contiene vincoli aggiuntivi (ad esempio limitando a specifici tipi di contenuto URL) o un dizionario vuoto se non sono necessari ulteriori vincoli.

Il metodo restituisce una matrice di elementi che soddisfano i criteri che è passati e pertanto contiene al massimo lo stesso numero di tipi di dati che vengono richiesti. è inoltre possibile che nessuno dei tipi richiesti siano presenti e verrà restituita una matrice vuota.

Ad esempio, il codice seguente controlla se un `NSImage` presente nell'area generale di montaggio e lo visualizza in un'immagine anche se in tal caso:

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

In base al tipo di applicazione Xamarin.Mac viene creato, potrebbe essere in grado di gestire più rappresentazioni dei dati da incollare. In questo caso, esistono due scenari per il recupero dei dati dall'area di montaggio:

1. Effettuare una chiamata a singola di `ReadObjectsForClasses` (metodo) e fornendo una matrice di tutte le rappresentazioni desiderate (nell'ordine preferito).
2. Effettuare più chiamate per il `ReadObjectsForClasses` ogni volta che i tipi di metodo che richiede una matrice di diversi.

Vedere il **semplice operazione di Incolla** precedente sezione per ulteriori informazioni su come recuperare dati da un tavolo.

### <a name="checking-for-existing-data-types"></a>Verifica per i tipi di dati esistenti

Esistono volte in cui è possibile controllare se un tavolo contiene una rappresentazione di dati specificato senza effettivamente la lettura dei dati da tavolo (, quali l'abilitazione di **Incolla** voce di menu solo se sono presenti dati validi).

Chiamare il `CanReadObjectForClasses` metodo tavolo per vedere se contiene un tipo specificato.

Ad esempio, il codice seguente determina se tavolo generale contiene un `NSImage` istanza:

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

### <a name="reading-urls-from-the-pasteboard"></a>URL di lettura da tavolo

In base alla funzione di un'applicazione Xamarin.Mac specificata, può risultare necessario eseguire la lettura di URL da un tavolo, ma solo se soddisfano una determinata serie di criteri (ad esempio, che punta al file o URL di un tipo di dati specifico). In questo caso, è possibile specificare i criteri di ricerca aggiuntivi tramite il secondo parametro del `CanReadObjectForClasses` o `ReadObjectsForClasses` metodi.

<a name="Custom_Data_Types" />

## <a name="custom-data-types"></a>Tipi di dati personalizzati

Vi sono casi quando è necessario salvare i tipi personalizzati da un'app Xamarin.Mac tavolo. Ad esempio, un disegno vettoriale app che consente all'utente di copiare e incollare oggetti disegno.

In questo caso, è necessario progettare la classe personalizzata dei dati in modo che erediti da `NSObject` e sia conforme alle interfacce qualche (`INSCoding`, `INSPasteboardWriting` e `INSPasteboardReading`). Facoltativamente, è possibile utilizzare un `NSPasteboardItem` per incapsulare i dati per essere copiati o incollati.

Entrambe le opzioni verrà descritta in dettaglio di seguito.

### <a name="using-a-custom-class"></a>Utilizzo di una classe personalizzata

In questa sezione è sarà l'espansione dell'applicazione di esempio semplice creata all'inizio di questo documento e aggiunta di una classe personalizzata per tenere traccia delle informazioni sull'immagine che si sta copiando e incollando tra windows.

Aggiungere una nuova classe al progetto e chiamarlo **ImageInfo.cs**. Modificare il file e renderlo simile al seguente:

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

Nelle sezioni seguenti prenderemo in un'analisi approfondita questa classe.

#### <a name="inheritance-and-interfaces"></a>Ereditarietà e interfacce

Prima di una classe di dati personalizzato può essere scritto o letto da un tavolo, deve essere conforme al `INSPastebaordWriting` e `INSPasteboardReading` interfacce. Inoltre, deve ereditare da `NSObject` e rispettare anche i `INSCoding` interfaccia:

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

La classe deve essere esposti anche al Objective-C utilizzando la `Register` direttiva e devono esporre le proprietà necessarie o i metodi utilizzando `Export`. Ad esempio:

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

Ci stiamo esposto da due campi di dati contenenti questa classe - nome dell'immagine e il relativo tipo (jpg, png e così via). 

Per ulteriori informazioni, vedere il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) documentazione, viene spiegato il `Register` e `Export` gli attributi utilizzato per associare le classi c# Objective-C e agli oggetti dell'interfaccia utente gli elementi.

#### <a name="constructors"></a>Costruttori

Due costruttori (correttamente esposti per Objective-C) sono necessari per la classe di dati personalizzati in modo che possono essere letti da un tavolo:

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

In primo luogo, è necessario esporre la _vuoto_ costruttore in base al metodo predefinito per Objective-C `init`.

Successivamente, è necessario esporre un `NSCoding` costruttore conforme che verrà utilizzato per creare una nuova istanza dell'oggetto da tavolo durante l'operazione Incolla sotto il nome del esportato `initWithCoder`.

Il costruttore accetta un `NSCoder` (creata da un `NSKeyedArchiver` quando scritto sul tavolo di montaggio), estrae i dati associati chiave/valore e lo salva in campi delle proprietà della classe di dati.

#### <a name="writing-to-the-pasteboard"></a>Scrittura in tavolo

Conformi al `INSPasteboardWriting` , è necessario implementare l'interfaccia espone due metodi e facoltativamente un terzo metodo, in modo che la classe può essere scritta tavolo.

In primo luogo, è necessario indicare tavolo il tipo di dati rappresentazioni che è possibile scrivere la classe personalizzata:

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

Ogni rappresentazione è identificato tramite un Uniform tipo identificatore (UTI), che è semplicemente una stringa semplice che identifica in modo univoco il tipo di dati viene presentati (per ulteriori informazioni, vedere Apple [Uniform Panoramica di identificatori di tipo ](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319) documentazione).

Per il formato personalizzato, viene creato un UTI: "com.xamarin.image-info" (si noti che è in notazione inversa proprio come un identificatore App). La classe è in grado di scrivere una stringa standard tavolo (`public.text`). 

Successivamente, è necessario creare l'oggetto nel formato richiesto che ottiene effettivamente scritti sul tavolo di montaggio:

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

Per il `public.text` tipo, ci stiamo restituzione di un semplice, formattato `NSString` oggetto. L'oggetto personalizzato `com.xamarin.image-info` tipo, si sta usando un `NSKeyedArchiver` e `NSCoder` interfaccia per codificare la classe di dati personalizzati in un archivio chiave-valore associata. È necessario implementare il metodo seguente per gestire la codifica:

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

Le coppie chiave/valore singolo sono scritti al codificatore e verranno decodificate tramite il secondo costruttore che è stato aggiunto in precedenza.

Facoltativamente, è possibile includere il metodo seguente per definire le opzioni durante la scrittura di dati di tavolo:

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

Attualmente solo il `WritingPromised` opzione è disponibile e deve essere utilizzato quando un determinato tipo è prevista solo e non vengono scritto nell'area di montaggio. Per ulteriori informazioni, vedere il [promessa dati](#Promised_Data) sezione precedente.

Con questi metodi sul posto, il codice seguente è utilizzabile per scrivere la classe personalizzata tavolo:

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>La lettura da tavolo

Conformi al `INSPasteboardReading` , è necessario implementare l'interfaccia espone tre metodi, in modo che la classe di dati personalizzati può essere lette da tavolo.

In primo luogo, è necessario indicare tavolo il tipo di dati rappresentazioni in grado di leggere la classe personalizzata dagli Appunti:

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

Nuovamente, questi vengono definiti come UTIs semplice e sono gli stessi tipi definiti nel **scrittura tavolo** sezione precedente.

Successivamente, è necessario indicare tavolo _come_ ognuno dei tipi di UTI verrà letti utilizzando il metodo seguente:

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

Per il `com.xamarin.image-info` tipo, si definiranno tavolo per decodificare la coppia chiave/valore creati con il `NSKeyedArchiver` scrittura quando la classe tavolo chiamando il `initWithCoder:` costruttore aggiunto alla classe.

Infine, è necessario aggiungere il metodo seguente per leggere le altre rappresentazioni di dati UTI tavolo:

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

Con tutti i metodi sul posto, è possibile leggere la classe di dati personalizzati da tavolo utilizzando il codice seguente:

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

### <a name="using-a-nspasteboarditem"></a>Utilizzando un NSPasteboardItem

Talvolta potrebbe essere quando è necessario scrivere gli elementi personalizzati a tavolo che garantisce la creazione di una classe personalizzata o per fornire i dati in un formato comune, solo in base alle esigenze. In queste situazioni, è possibile utilizzare un `NSPasteboardItem`.

Oggetto `NSPasteboardItem` fornisce un controllo accurato per i dati vengono scritti in tavolo ed sono progettati per l'accesso temporaneo - deve essere eliminato dopo che è stata scritta sul tavolo di montaggio.

#### <a name="writing-data"></a>La scrittura dei dati

Per scrivere i dati personalizzati a un `NSPasteboardItem` , devi fornire un oggetto personalizzato `NSPasteboardItemDataProvider`. Aggiungere una nuova classe al progetto e chiamarlo **ImageInfoDataProvider.cs**. Modificare il file e renderlo simile al seguente:

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

Come è stato fatto con la classe di dati personalizzati, è necessario utilizzare il `Register` e `Export` direttive per esporla a Objective-C. La classe deve ereditare da `NSPasteboardItemDataProvider` e deve implementare il `FinishedWithDataProvider` e `ProvideDataForType` metodi.

Utilizzare il `ProvideDataForType` metodo per fornire i dati che verranno eseguito il wrapping nel `NSPasteboardItem` come indicato di seguito:

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

In questo caso, stiamo l'archiviazione di due tipi di informazioni per l'immagine (nome e ImageType) e la scrittura in una stringa semplice (`public.text`).

Tipo di scrivere i dati tavolo, utilizzare il codice seguente:

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

Per leggere i dati da tavolo, utilizzare il codice seguente:

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

In questo articolo è stato applicato a un'analisi approfondita sul tavolo in un'applicazione Xamarin.Mac per supportare la copia e incollare le operazioni di utilizzo. Introdotto un semplice esempio per acquisire familiarità con le operazioni di pasteboards standard. Successivamente, impiegato un'analisi approfondita sul tavolo e come leggere e scrivere i dati da esso. Infine, esaminato l'utilizzo di un tipo di dati personalizzati per supportare la copia e Incolla di tipi di dati complessi all'interno di un'app.



## <a name="related-links"></a>Collegamenti correlati

- [MacCopyPaste (sample)](https://developer.xamarin.com/samples/mac/MacCopyPaste/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Guida per programmatori tavolo di montaggio](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [linee guida dell'interfaccia umana macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
