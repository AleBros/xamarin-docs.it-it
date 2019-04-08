---
title: Immagini in xamarin. Mac
description: Questo articolo descrive l'uso delle immagini e icone in un'applicazione xamarin. Mac. Descrive la creazione e la manutenzione delle immagini necessarie per creare icone dell'applicazione e l'utilizzo di immagini nel codice C# e Interface Builder di Xcode.
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 719efc87b8843d0d2fcd2643aab23aa6849d940a
ms.sourcegitcommit: 190808013249005ceffbc798f9f4570e8cdc943a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2019
ms.locfileid: "54841380"
---
# <a name="images-in-xamarinmac"></a>Immagini in xamarin. Mac

_Questo articolo descrive l'uso delle immagini e icone in un'applicazione xamarin. Mac. Descrive la creazione e la manutenzione delle immagini necessarie per creare icone dell'applicazione e l'utilizzo di immagini nel codice C# e Interface Builder di Xcode._

## <a name="overview"></a>Panoramica

Quando si usa C# e .NET in un'applicazione xamarin. Mac, è possibile utilizzare la stessa immagine di strumenti e icone che gli sviluppatori che lavorano *Objective-C* e *Xcode* viene.

Esistono diversi modi quell'immagine vengono usati gli asset all'interno di un'applicazione macOS (precedentemente noto come Mac OS X). Visualizza semplicemente un'immagine come parte dell'interfaccia utente dell'applicazione, assegnarlo a un controllo dell'interfaccia utente, ad esempio una barra degli strumenti o elemento elenco di origine, alla fornitura di icone, xamarin. Mac è molto semplice aggiungere opere eccezionali per le applicazioni di macOS nei modi seguenti : 

- **Elementi dell'interfaccia utente** -le immagini possono essere visualizzate come sfondi o come parte dell'applicazione in una visualizzazione di immagini (`NSImageView`).
- **Pulsante** -le immagini possono essere visualizzate nei pulsanti (`NSButton`).
- **Immagine cella** : come parte di un controllo basato su tabella (`NSTableView` oppure `NSOutlineView`), le immagini possono essere usate in una cella di immagine (`NSImageCell`).
- **Barra degli strumenti** -le immagini possono essere aggiunti a una barra degli strumenti (`NSToolbar`) come elemento della barra degli strumenti immagine (`NSToolbarItem`).
- **Icona di elenco di origine** : come parte di un elenco di origine (una formattazione speciale `NSOutlineView`).
- **Icona dell'app** -una serie di immagini possono essere raggruppata in un `.icns` impostata e utilizzata come icona dell'applicazione. Vedere la [icona dell'applicazione](~/mac/deploy-test/app-icon.md) per altre informazioni.

MacOS, inoltre, fornisce un set di immagini predefinite che possono essere usate nell'applicazione.

[![Un esempio di esecuzione dell'app](image-images/intro01.png "un esempio di esecuzione dell'app")](image-images/intro01-large.png#lightbox)

In questo articolo, si affronterà le nozioni di base dell'utilizzo di immagini e icone in un'applicazione xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in questo articolo.


## <a name="adding-images-to-a-xamarinmac-project"></a>Aggiunta di immagini a un progetto xamarin. Mac

Quando si aggiunge un'immagine per l'uso in un'applicazione xamarin. Mac, esistono diverse posizioni e i modi che lo sviluppatore può includere file di immagine all'origine del progetto:

- **Struttura ad albero principale del progetto [deprecato]** -immagini possono essere aggiunti direttamente all'albero di progetti. Quando si chiama le immagini archiviate nell'albero principale del progetto dal codice, viene specificato alcun percorso di cartella. Ad esempio: `NSImage image = NSImage.ImageNamed("tags.png");`. 
- **Cartella di risorse [deprecato]** -la speciale **risorse** cartella è per qualsiasi file che verrà configurati come parte dell'applicazione del Bundle, ad esempio generale, schermata di avvio o sull'icona immagini (qualsiasi immagine o file lo sviluppatore potrebbe desiderare aggiungere). Quando si chiama le immagini archiviate nel **risorse** cartella dal codice, proprio come le immagini archiviate nell'albero del progetto principale, viene specificato alcun percorso di cartella. Ad esempio: `NSImage.ImageNamed("tags.png")`.
- **Cartella personalizzata o la sottocartella [deprecato]** -lo sviluppatore può aggiungere una cartella personalizzata all'albero di origine di progetti e archiviare le immagini non esiste. Il percorso in cui viene aggiunto il file può essere annidato in una sottocartella alla assistenza aggiuntiva per organizzare il progetto. Ad esempio, se lo sviluppatore di aggiunta un `Card` al progetto e una sottocartella della cartella `Hearts` in tale cartella, quindi archiviare un'immagine **Jack.png** nel `Hearts` cartella `NSImage.ImageNamed("Card/Hearts/Jack.png")` carica l'immagine nella fase di esecuzione.
- **Set di immagini del catalogo di asset [preferito]** - aggiunto in OS X El Capitan **set di immagini di cataloghi Asset** contengono tutte le versioni o rappresentazioni di un'immagine che sono necessari per supportare vari tipi di dispositivi e per fattori di scala di applicazione. Anziché utilizzare il nome del file dell'asset di immagine (**@1x**, **@2x**).

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>Aggiunta di immagini di un'immagine di catalogo di asset set

Come indicato in precedenza, un' **set di immagini di cataloghi Asset** contengono tutte le versioni o rappresentazioni di un'immagine che sono necessari per supportare vari tipi di dispositivi e fattori per l'applicazione di scala. Anziché utilizzare il nome del file dell'asset di immagine (vedere le immagini indipendente dalla risoluzione e nomenclatura immagine precedente), **set di immagini** usare l'Editor di Asset per specificare quale immagine appartiene a quali dispositivi e/o la risoluzione.

1. Nel **riquadro della soluzione**, fare doppio clic il **assets. xcassets** file per aprirlo e modificarlo: 

    ![Selezionando l'assets. xcassets](image-images/imageset01.png "selezionando l'assets. xcassets")
2. Fare clic sui **elenco Assets** e selezionare **nuova immagine Set**: 

    [![Aggiunta di un nuovo set di immagini](image-images/imageset02.png "aggiungendo un nuovo set di immagini")](image-images/imageset02-large.png#lightbox)
3. Selezionare il nuovo set di immagini e verrà visualizzato l'editor: 

    [![Selezionare il nuovo set di immagini](image-images/imageset03.png "selezionando il nuovo set di immagini")](image-images/imageset03-large.png#lightbox)
4. Da qui è possibile trascinare nelle immagini per ognuno dei diversi dispositivi e risoluzioni necessarie. 
5. Fare doppio clic sul nuovo set di immagini **Name** nel **elenco Assets** modificarla: 

    [![Nome del set di modifica dell'immagine](image-images/imageset04.png "modifica l'immagine del nome del set")](image-images/imageset04-large.png#lightbox)
    
Una speciale **vettore** aggiunte alla classe **set di immagini** che consente di includere un _PDF_ formattato immagine vettoriale nel casset invece inclusi i file di mappa di bit singoli in le risoluzioni diverse. In questo modo, è fornire un file singolo vettore per la **@1x** risoluzione (formattata come un file PDF di vettore) e il **@2x** e **@3x** le versioni del file verranno generate in fase di compilazione e inclusi nel bundle dell'applicazione.

[![L'immagine del set interface editor](image-images/imageset05.png "l'immagine impostata dell'interfaccia dell'editor")](image-images/imageset05-large.png#lightbox)

Ad esempio, se si include un `MonkeyIcon.pdf` file come vettore di un catalogo Asset con una risoluzione di x 150px 150 px, la bitmap seguente asset sarà incluso nel bundle dell'app finale durante la compilazione:

1. **MonkeyIcon@1x.png** -150 px x 150px risoluzione.
2. **MonkeyIcon@2x.png** -resolution x 300px 300 px.
3. **MonkeyIcon@3x.png** -450px x 450px risoluzione.

Di seguito deve prendere in considerazione quando si usano immagini vettoriali PDF nei cataloghi Asset:

- Questo non è supporto vettore completo come il formato PDF sarà rasterizzato a una bitmap in fase di compilazione e le bitmap fornite con l'applicazione finale.
- Dopo che è stata impostata nel catalogo di Asset non è possibile modificare le dimensioni dell'immagine. Se si prova a ridimensionare l'immagine (nel codice oppure utilizzando Layout automatico e le classi di dimensioni) l'immagine verrà distorta esattamente come qualsiasi altra immagine bitmap.

Quando si usa un' **immagine impostata** in Interface Builder di Xcode, è possibile selezionare semplicemente il relativo nome nell'elenco a discesa nel **attributo Inspector**:

![Se si seleziona un'immagine impostato in Interface Builder di Xcode](image-images/imageset06.png "selezionando un'immagine impostata in Interface Builder di Xcode")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>Aggiunta di nuove raccolte di risorse

Quando si lavora con le immagini nei cataloghi asset a volte può essere quando si desidera creare una nuova raccolta, invece di aggiungere tutte le immagini per le **assets. xcassets** raccolta. Ad esempio, quando si progettano le risorse on demand.

Per aggiungere un nuovo catalogo asset al progetto:

1. Pulsante destro del mouse sul progetto nel **riquadro della soluzione** e selezionare **Add** > **nuovo File...**
2. Selezionare **Mac** > **catalogo Asset**, immettere un **nome** per la raccolta e fare clic sul **nuovo** pulsante: 

    ![Aggiunta di un nuovo catalogo Asset](image-images/asset01.png "aggiungendo un nuovo catalogo Asset")

Da qui è possibile rivolgersi insieme nello stesso modo come impostazione predefinita **assets. xcassets** raccolta automaticamente incluso nel progetto.


### <a name="adding-images-to-resources"></a>Aggiunta di immagini per le risorse

> [!IMPORTANT]
> Questo metodo di utilizzo di immagini in un'app macOS è stato deprecato da Apple. È consigliabile usare [set di immagini di catalogo di Asset](#asset-catalogs) al gestore delle immagini invece dell'app.

Prima di poter usare un file di immagine nell'applicazione xamarin. Mac (sia nel codice C# o da Interface Builder) deve essere incluso del progetto **le risorse** cartella come una **Bundle di risorse**. Per aggiungere un file a un progetto, eseguire le operazioni seguenti:

1. Fare clic sui **risorse** cartella nel progetto nel **riquadro della soluzione** e selezionare **Add** > **aggiungere file...** : 

    ![Aggiunta di un file](image-images/add01.png "aggiunta di un file")
2. Dal **Add Files** finestra di dialogo, seleziona le immagini di file da aggiungere al progetto, selezionare `BundleResource` per il **azione di compilazione Override** e fare clic sul **Open** pulsante:

    [![Selezionare i file da aggiungere](image-images/add02.png "selezionando i file da aggiungere")](image-images/add02-large.png#lightbox)
3. Se i file non sono già nel **risorse** cartella, viene chiesto se si desidera **copia**, **spostare** oppure **collegamento** i file. Scegliere quale ogni tute le tue esigenze, in genere che saranno **copia**:

    ![Selezionando l'azione di addizione](image-images/add04.png "selezionando l'azione Aggiungi")
4. I nuovi file verranno inclusi nel progetto e di lettura per l'uso: 

    ![I nuovi file di immagine aggiunti al riquadro della soluzione](image-images/add03.png "i nuovi file di immagine aggiunti al riquadro della soluzione")
5. Ripetere il processo per tutti i file di immagine necessari.

È possibile usare qualsiasi file con estensione pdf, png o jpg come un'immagine di origine nell'applicazione xamarin. Mac. Nella sezione successiva, esamineremo aggiungendo versioni ad alta risoluzione delle nostre immagini e icone per il supporto della Retina basati su computer Mac.

> [!IMPORTANT]
> Se si aggiungono le immagini per le **risorse** cartella, è possibile lasciare il **Override azione di compilazione** impostata su **predefinito**. Il valore predefinito dell'azione di compilazione per questa cartella è `BundleResource`.

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>Fornire versioni ad alta risoluzione di tutte le risorse di grafica di app

Qualsiasi asset grafici che si aggiunge a un'applicazione xamarin. Mac (icone, controlli personalizzati, i cursori personalizzati, la grafica personalizzata, e così via) dovrà avere versioni ad alta risoluzione, oltre alle relative versioni risoluzione standard. Ciò è necessario in modo che l'applicazione avrà un aspetto preferibile quando l'esecuzione su un Display Retina dotati di computer Mac.


### <a name="adopt-the-2x-naming-convention"></a>Adottare il @2x convenzione di denominazione

> [!IMPORTANT]
> Questo metodo di utilizzo di immagini in un'app macOS è stato deprecato da Apple. È consigliabile usare [set di immagini di catalogo di Asset](#asset-catalogs) al gestore delle immagini invece dell'app.

Quando si creano le versioni standard e ad alta risoluzione di un'immagine, seguono questa convenzione di denominazione per la coppia di immagine quando vengono inclusi nel progetto xamarin. Mac:

- **Risoluzione standard**  - **ImageName.filename-estensione** (esempio: **tags.png**)
- **Ad alta risoluzione**   -  **ImageName@2x.filename-extension** (esempio: **tags@2x.png**)

Quando aggiunta a un progetto, sarebbe simile al seguente:

![I file di immagine nel riquadro della soluzione](image-images/add03.png "i file di immagine nel riquadro della soluzione")

Quando un'immagine viene assegnata a un elemento dell'interfaccia utente in Interface Builder sarà sufficiente selezionare il file nei _ImageName_**.** _nomefile-estensione_ formato (esempio: **tags.png**). Lo stesso per l'uso di un'immagine nel codice C#, verrà scelto il file nei _ImageName_**.** _nomefile-estensione_ formato.

Quando si applicazione xamarin. Mac viene eseguito in un computer Mac, il _ImageName_**.** _nomefile-estensione_ formato immagine verrà usata su schermi a risoluzione Standard, il **ImageName@2x.filename-extension** immagine verrà prelevata automaticamente Display Retina basi i computer Mac.


## <a name="using-images-in-interface-builder"></a>Uso di immagini in Interface Builder

Si sono aggiunti a qualsiasi risorsa di immagine la **risorse** cartella di xamarin. MAC del progetto e avere impostato l'azione di compilazione su **BundleResource** verranno visualizzate automaticamente in Interface Builder e può essere selezionato come parte di un elemento dell'interfaccia utente (se vengono gestite le immagini).

Per usare un'immagine nel generatore di interfaccia, eseguire le operazioni seguenti:

1. Aggiungere un'immagine per il **le risorse** cartella con un **azione di compilazione** di `BundleResource`: 

     ![Una risorsa immagine nel riquadro della soluzione](image-images/ib00.png "una risorsa immagine nel riquadro della soluzione")
2. Fare doppio clic il **Main. Storyboard** file per aprirlo e modificarlo in Interface Builder: 

     [![Modifica dello storyboard principale](image-images/ib01.png "modifica dello storyboard principale")](image-images/ib01-large.png#lightbox)
3. Trascinare un elemento dell'interfaccia utente che accetta le immagini nell'area di progettazione (ad esempio, un **immagine della barra degli strumenti**): 

     ![Modifica di un elemento della barra degli strumenti](image-images/ib02.png "modifica di un elemento della barra degli strumenti")
4. Selezionare l'immagine che è stato aggiunto per il **le risorse** cartella nel **nome immagine** elenco a discesa: 

     [![Selezione di un'immagine per un elemento della barra degli strumenti](image-images/ib03.png "selezionando un'immagine per un elemento della barra degli strumenti")](image-images/ib03-large.png#lightbox)
5. L'immagine selezionata verrà visualizzato nell'area di progettazione: 

     ![L'immagine viene visualizzata nell'editor barra degli strumenti](image-images/ib04.png "l'immagine viene visualizzata nell'editor barra degli strumenti")
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Usare i passaggi precedenti per qualsiasi elemento dell'interfaccia utente che consente le proprietà di immagine da impostare **Inspector attributo**. Anche in questo caso, se è stato incluso un **@2x** versione del file di immagine, si verrà automaticamente utilizzato sul Display Retina basati su computer Mac.

> [!IMPORTANT]
> Se l'immagine non è disponibile nel **nome dell'immagine** elenco a discesa, chiudere il progetto di storyboard in Xcode e riaprirlo da Visual Studio per Mac. Se l'immagine non è ancora disponibile, assicurarsi che relativo **azione di compilazione** viene `BundleResource` e che l'immagine è stato aggiunto per il **risorse** cartella.

## <a name="using-images-in-c-code"></a>Uso di immagini nel codice C#

Quando si carica un'immagine in memoria usando codice C# in un'applicazione xamarin. Mac, l'immagine verrà archiviata in un `NSImage` oggetto. Se il file di immagine è stato incluso nel bundle dell'applicazione xamarin. Mac (incluso nelle risorse), usare il codice seguente per caricare l'immagine:

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

Il codice precedente Usa il metodo statico `ImageNamed("...")` metodo del `NSImage` classe per caricare l'immagine specificata nella memoria dalle **risorse** cartella, se l'immagine non viene trovato, `null` verranno restituiti. Ad esempio immagini assegnate in Interface Builder, se è stato incluso un **@2x** versione del file di immagine, si verrà automaticamente utilizzato sul Display Retina basati su computer Mac.

Per caricare immagini di fuori di bundle dell'applicazione (dal file system Mac), usare il codice seguente:

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>Utilizzo delle immagini modello

In base alla struttura dell'app per macOS, potrebbero esserci casi è necessario personalizzare un'icona o un'immagine dentro l'interfaccia utente per la corrispondenza di una modifica nella combinazione di colori (ad esempio, in base alle preferenze dell'utente).

A tale scopo, passare il _modalità di rendering_ dell'Asset per immagini **immagine modello**:

[![Impostazione di un'immagine modello](image-images/templateimage01.png "impostazione di un'immagine modello")](image-images/templateimage01-large.png#lightbox)

Da Interface Builder di Xcode assegnare l'Asset di immagine a un controllo dell'interfaccia utente:

![Selezione di un'immagine in Interface Builder di Xcode](image-images/templateimage02.png "selezionando un'immagine in Interface Builder di Xcode")

In alternativa, se lo si desidera impostare l'origine dell'immagine nel codice:

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

Aggiungere la seguente funzione pubblica per il Controller di visualizzazione:

```csharp
public NSImage ImageTintedWithColor(NSImage sourceImage, NSColor tintColor)
    => NSImage.ImageWithSize(sourceImage.Size, false, rect => {
        // Draw the original source image
        sourceImage.DrawInRect(rect, CGRect.Empty, NSCompositingOperation.SourceOver, 1f);

        // Apply tint
        tintColor.Set();
        NSGraphics.RectFill(rect, NSCompositingOperation.SourceAtop);

        return true;
    });
```

> [!IMPORTANT]
> In particolare con l'avvento della modalità scura in macOS Mojave, è importante evitare il `LockFocus` API quando esegue il rendering personalizzata reating `NSImage` oggetti. Tali immagini diventano statiche e non verranno aggiornate automaticamente per mostrare le modifiche di densità di aspetto o la visualizzazione.
>
> Usando il meccanismo precedente basato su gestore, re-rendering dinamico condizioni viene eseguita automaticamente quando il `NSImage` è ospitato, ad esempio, in un `NSImageView`.

Infine, per tingere un'immagine modello, chiamare questa funzione con l'immagine da colorare:

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>Uso di immagini con le visualizzazioni di tabelle

Includere un'immagine come parte della cella in una `NSTableView`, è necessario modificare come i dati vengono restituiti della visualizzazione tabella `NSTableViewDelegate's` `GetViewForItem` metodo da usare una `NSTableCellView` anziché il tipico `NSTextField`. Ad esempio:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = true;

        view.TextField.EditingEnded += (sender, e) => {

            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.TextField.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tags.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

Esistono alcune righe di interesse. Innanzitutto, per le colonne che si desidera includere un'immagine, si creerà una nuova `NSImageView` del dimensioni necessarie e il percorso, si crea anche un nuovo `NSTextField` e posizionare relativa posizione predefinita in base se si sta usando un'immagine:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

In secondo luogo, è necessario includere la nuova visualizzazione immagine e campo del testo nell'elemento padre `NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Infine, è necessario indicare il campo di testo che può ridurre e crescere in base alla cella di visualizzazione della tabella:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Output di esempio:

[![Un esempio di visualizzazione di un'immagine in un'app](image-images/tables01.png "un esempio di visualizzazione di un'immagine in un'app")](image-images/tables01-large.png#lightbox)

Per altre informazioni sull'uso delle visualizzazioni tabella, vedere la [viste delle tabelle](~/mac/user-interface/table-view.md) documentazione.

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>Uso di immagini con la visualizzazione della struttura

Includere un'immagine come parte della cella in una `NSOutlineView`, è necessario modificare la modalità con cui i dati vengono restituiti dalla visualizzazione della struttura `NSTableViewDelegate's` `GetView` metodo da usare una `NSTableCellView` anziché il tipico `NSTextField`. Ad esempio:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

Esistono alcune righe di interesse. Innanzitutto, per le colonne che si desidera includere un'immagine, si creerà una nuova `NSImageView` del dimensioni necessarie e il percorso, si crea anche un nuovo `NSTextField` e posizionare relativa posizione predefinita in base se si sta usando un'immagine:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

In secondo luogo, è necessario includere la nuova visualizzazione immagine e campo del testo nell'elemento padre `NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Infine, è necessario indicare il campo di testo che può ridurre e crescere in base alla cella di visualizzazione della tabella:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Output di esempio:

[![Un esempio di un'immagine viene visualizzata in una visualizzazione della struttura](image-images/outline01.png "un esempio di un'immagine viene visualizzata in una visualizzazione della struttura")](image-images/outline01-large.png#lightbox)

Per altre informazioni sull'uso delle visualizzazioni struttura, vedere la [visualizzazioni della struttura](~/mac/user-interface/outline-view.md) documentazione.


## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di utilizzo di immagini e icone in un'applicazione xamarin. Mac. Abbiamo visto i diversi tipi e gli utilizzi di immagini, come usare le immagini e icone in Interface Builder di Xcode e come lavorare con immagini e icone nel codice C#.



## <a name="related-links"></a>Collegamenti correlati

- [MacImages (sample)](https://developer.xamarin.com/samples/mac/MacImages/) (MacImages - Esempio)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Visualizzazioni di tabelle](~/mac/user-interface/table-view.md)
- [Visualizzazione della struttura](~/mac/user-interface/outline-view.md)
- [macOS Human Interface Guidelines di X](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [About High Resolution for OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html) (Informazioni sulla risoluzione elevata per OS X)
