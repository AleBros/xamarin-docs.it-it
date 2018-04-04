---
title: Immagini
description: In questo articolo viene descritto l'utilizzo di immagini e icone in un'applicazione Xamarin.Mac. Descrive la creazione e la manutenzione delle immagini necessarie per creare l'icona dell'applicazione e l'utilizzo di immagini nel codice c# e nel generatore di interfaccia di Xcode.
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: dc33dc78c09c0b5b7cb7533afdd2f95b8ebd9c4e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="images"></a>Immagini

_In questo articolo viene descritto l'utilizzo di immagini e icone in un'applicazione Xamarin.Mac. Descrive la creazione e la manutenzione delle immagini necessarie per creare l'icona dell'applicazione e l'utilizzo di immagini nel codice c# e nel generatore di interfaccia di Xcode._


## <a name="overview"></a>Panoramica

Quando si utilizza c# e .NET in un'applicazione Xamarin.Mac, è possibile accedere alla stessa immagine e l'icona di strumenti che uno sviluppatore che lavora *Objective-C* e *Xcode* does.

Esistono diversi modi quell'immagine asset vengono utilizzati all'interno di un'applicazione macOS (precedentemente noto come Mac OS X). Semplicemente la visualizzazione di un'immagine come parte dell'interfaccia utente dell'applicazione, l'assegnazione a un controllo dell'interfaccia utente, ad esempio una barra degli strumenti o una voce dell'elenco di origine, icone, invece di fornire Xamarin.Mac rende facile aggiungere grafica ottima per le applicazioni macOS nei modi seguenti : 

- **Elementi dell'interfaccia utente** -immagini possono essere visualizzate come sfondo o come parte dell'applicazione in una visualizzazione di immagini (`NSImageView`).
- **Pulsante** -le immagini possono essere visualizzate nei pulsanti (`NSButton`).
- **Immagine cella** - come parte di un controllo basato su tabella (`NSTableView` o `NSOutlineView`), immagini possono essere utilizzate in una cella di immagine (`NSImageCell`).
- **Barra degli strumenti** -immagini possono essere aggiunti a una barra degli strumenti (`NSToolbar`) come elemento della barra degli strumenti immagine (`NSToolbarItem`).
- **Icona di elenco di origine** - come parte di un elenco di origine (una formattazione speciale `NSOutlineView`).
- **Icona app** -una serie di immagini possono essere raggruppata in un `.icns` impostato e utilizzato come icona dell'applicazione. Vedere il nostro [icona applicazione](~/mac/deploy-test/app-icon.md) documentazione per ulteriori informazioni.

Inoltre, macOS fornisce un set di immagini predefinite che possono essere usate nell'applicazione.

[![Eseguire un esempio dell'app](image-images/intro01.png "eseguire un esempio dell'app")](image-images/intro01-large.png#lightbox)

In questo articolo verranno descritte le nozioni fondamentali sull'utilizzo di immagini e icone in un'applicazione Xamarin.Mac. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in questo articolo.


## <a name="adding-images-to-a-xamarinmac-project"></a>Aggiunta di immagini a un progetto Xamarin.Mac

Quando si aggiunge un'immagine per l'utilizzo in un'applicazione Xamarin.Mac, esistono diverse posizioni e i modi che lo sviluppatore può includere file di immagine per l'origine del progetto:

- **Struttura ad albero di progetto principale [deprecato]** -immagini possono essere aggiunti direttamente alla struttura ad albero di progetti. Quando si chiama le immagini archiviate nella struttura del progetto principale dal codice, viene specificato alcun percorso di cartella. Ad esempio: `NSImage image = NSImage.ImageNamed("tags.png");`. 
- **Cartella di risorse [deprecato]** -speciale **risorse** cartella è per qualsiasi file che verrà configurati come parte dell'applicazione del Bundle, ad esempio generale, schermata di avvio o sull'icona immagini (o qualsiasi altra immagine o file lo sviluppatore si desidera aggiungere). Quando si chiama le immagini archiviate nel **risorse** cartella dal codice, proprio come le immagini archiviate nella struttura del progetto principale, viene specificato alcun percorso di cartella. Ad esempio: `NSImage.ImageNamed("tags.png")`.
- **Personalizzato cartella o una sottocartella [deprecato]** -lo sviluppatore può aggiungere una cartella personalizzata all'albero di origine di progetti e archiviare le immagini non esiste. Il percorso in cui viene aggiunto il file può essere annidato in una sottocartella per ulteriore assistenza organizzare il progetto. Ad esempio, se lo sviluppatore di aggiunta un `Card` al progetto e una sottocartella della cartella di `Hearts` a quella cartella, quindi archiviare un'immagine **Jack.png** nel `Hearts` cartella `NSImage.ImageNamed("Card/Hearts/Jack.png")` carica l'immagine fase di esecuzione.
- **Set di immagine di catalogo di asset [preferito]** - aggiunta in OS X montagna di El Capitan, **Asset cataloghi immagine set** contengono tutte le versioni o le rappresentazioni di un'immagine che sono necessari per supportare vari dispositivi e ridimensionare i fattori per il applicazione. Anziché basarsi sul nome di file di asset immagine (**@1x**, **@2x**).

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>Aggiunta di immagini a un'immagine di catalogo di asset set

Come descritto in precedenza, un **Asset cataloghi immagine set** contengono tutte le versioni o le rappresentazioni di un'immagine che sono necessari per supportare vari dispositivi e fattori per l'applicazione di scala. Anziché basarsi sul nome di file di asset immagine (vedere immagini indipendente dalla risoluzione e nomenclatura immagine precedente), **set immagine** utilizzare l'Editor di risorse per specificare quale immagine appartiene a quali dispositivi e/o risoluzione.

1. Nel **soluzione riempimento**, fare doppio clic su di **Assets.xcassets** file per aprirlo e modificarlo: 

    ![Selezione di Assets.xcassets](image-images/imageset01.png "selezionando il Assets.xcassets")
2. Fare clic su di **elenco risorse** e selezionare **nuova immagine impostata**: 

    [![Aggiunta di un nuovo set di immagini](image-images/imageset02.png "aggiunta di un nuovo set di immagini")](image-images/imageset02-large.png#lightbox)
3. Selezionare il nuovo set di immagini e verrà visualizzato l'editor: 

    [![Selezionare il nuovo set di immagini](image-images/imageset03.png "selezionando il nuovo set di immagini")](image-images/imageset03-large.png#lightbox)
4. Da qui è possibile trascinare le immagini per ciascuno dei diversi dispositivi e risoluzioni necessarie. 
5. Fare doppio clic sul nuovo set di immagini **nome** nel **elenco risorse** modificarla: 

    [![Nome del set di modifica dell'immagine](image-images/imageset04.png "nome del set di modifica dell'immagine")](image-images/imageset04-large.png#lightbox)
    
Una speciale **vettore** come è stato aggiunto a **set immagine** che consente di includere un _PDF_ formato immagine vettore di casset invece inclusi file bitmap singoli le diverse risoluzioni. Questo metodo è fornire un file singolo vettore per il **@1x** risoluzione (formattata come file PDF vettore) e **@2x** e **@3x** versioni del file verranno generate in fase di compilazione e inclusi nel bundle dell'applicazione.

[![Interfaccia dell'editor di impostare l'immagine](image-images/imageset05.png "l'immagine di imposta l'interfaccia dell'editor")](image-images/imageset05-large.png#lightbox)

Ad esempio, se si include un `MonkeyIcon.pdf` file come vettore di un catalogo di Asset con una risoluzione di 150 px x 150px, la mappa di bit seguente asset sono incluso nel bundle di app finale durante la compilazione:

1. **MonkeyIcon@1x.png** -150 px x 150px risoluzione.
2. **MonkeyIcon@2x.png** -300px x 300px risoluzione.
3. **MonkeyIcon@3x.png** -450px x 450px risoluzione.

Di seguito deve essere prese in considerazione quando si usano immagini vettoriali PDF nei cataloghi di Asset:

- Non è supporto vettore completo come PDF saranno rasterizzato a una bitmap in fase di compilazione e la bitmap fornite nell'applicazione finale.
- Una volta che è stata impostata nel catalogo di Asset non è possibile modificare le dimensioni dell'immagine. Se si tenta di ridimensionamento dell'immagine (nel codice o con Layout automatico e classi di dimensione) l'immagine verrà distorta esattamente come qualsiasi altro tipo di bitmap.

Quando si utilizza un **immagine impostata** in Generatore del Xcode di interfaccia, è possibile selezionare semplicemente il nome del set di dall'elenco a discesa nella **controllo attributo**: * *

![Se si seleziona un'immagine impostato in interfaccia generatore del Xcode](image-images/imageset06.png "se si seleziona un'immagine impostato in Generatore del Xcode di interfaccia")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>Aggiunta di nuove raccolte di risorse

Quando si lavora con immagini nei cataloghi di risorse potrebbe essere volte quando si desidera creare una nuova raccolta, anziché aggiungere tutte le immagini di **Assets.xcassets** insieme. Ad esempio, quando si progettano le risorse su richiesta.

Per aggiungere un nuovo catalogo di asset al progetto:

1. Pulsante destro del mouse sul progetto nel **soluzione riempimento** e selezionare **Aggiungi** > **nuovo File...**
2. Selezionare **Mac** > **catalogo di Asset**, immettere un **nome** per la raccolta e fare clic su di **New** pulsante: 

    ![Aggiunta di un nuovo catalogo di Asset](image-images/asset01.png "aggiunta di un nuovo catalogo di Asset")

Da qui è possibile utilizzare con la raccolta nello stesso modo come impostazione predefinita **Assets.xcassets** raccolta automaticamente incluso nel progetto.


### <a name="adding-images-to-resources"></a>Aggiunta di immagini per le risorse

> [!IMPORTANT]
> Questo metodo di utilizzo delle immagini in un'app macOS è stato deprecato da Apple. È consigliabile utilizzare [insiemi di immagini di catalogo di Asset](#asset-catalogs) al gestore delle immagini invece dell'app.

Prima di poter usare un file di immagine Xamarin.Mac nell'applicazione in uso (nel codice c# o da interfaccia generatore) deve essere incluso del progetto **risorse** cartella come una **Bundle di risorse**. Per aggiungere un file a un progetto, eseguire le operazioni seguenti:

1. Fare clic su di **risorse** cartella nel progetto nel **soluzione riempimento** e selezionare **Aggiungi** > **Aggiungi file...** : 

    ![Aggiunta di un file](image-images/add01.png "aggiunta di un file")
2. Dal **Aggiungi file** la finestra di dialogo, seleziona le immagini di file da aggiungere al progetto, selezionare `BundleResource` per il **Override azione di compilazione** e fare clic su di **aprire** pulsante:

    [![Selezionando i file da aggiungere](image-images/add02.png "selezionando i file da aggiungere")](image-images/add02-large.png#lightbox)
3. Se i file non sono già nel **risorse** cartella, verrà chiesto se si desidera **copia**, **spostare** o **collegamento** i file. Scegliere quale ogni tute le esigenze, in genere che saranno **copia**:

    ![Selezionando l'azione Aggiungi](image-images/add04.png "selezionando l'azione Aggiungi")
4. I nuovi file verranno inclusi nel progetto e di lettura per l'utilizzo: 

    ![I nuovi file di immagine aggiunti al riquadro soluzione](image-images/add03.png "i nuovi file di immagine aggiunti al riquadro soluzione")
5. Ripetere il processo per tutti i file di immagine necessaria.

È possibile utilizzare qualsiasi png, jpg o file pdf come immagine di origine Xamarin.Mac nell'applicazione in uso. Nella sezione successiva, esamineremo aggiunta versioni ad alta risoluzione di questo immagini e icone per il supporto della Retina basato su computer Mac.

> [!IMPORTANT]
> Se si desidera aggiungere immagini per il **risorse** cartella, è possibile lasciare il **azione di compilazione Override** impostato su **predefinito**. Il valore predefinito dell'azione di compilazione per questa cartella è `BundleResource`.

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>Fornire versioni ad alta risoluzione di tutte le risorse grafiche di app

Qualsiasi asset grafici che si aggiunge a un'applicazione Xamarin.Mac (icone, controlli personalizzati, i cursori personalizzati, il disegno personalizzato e così via) è necessario installare versioni ad alta risoluzione oltre le relative versioni standard di risoluzione. Ciò è necessario in modo che l'applicazione verrà visualizzate le migliori quando l'esecuzione su un Display Retina dotati di computer Mac.


### <a name="adopt-the-2x-naming-convention"></a>Adottare il @2x convenzione di denominazione

> [!IMPORTANT]
> Questo metodo di utilizzo delle immagini in un'app macOS è stato deprecato da Apple. È consigliabile utilizzare [insiemi di immagini di catalogo di Asset](#asset-catalogs) al gestore delle immagini invece dell'app.

Quando si creano le versioni standard e ad alta risoluzione di un'immagine, seguire questa convenzione di denominazione per la coppia di immagine quando vengono inclusi nel progetto Xamarin.Mac:

- **Risoluzione standard**  - **ImageName.filename estensione** (esempio: **tags.png**)
- **Ad alta risoluzione**   -  **ImageName@2x.filename-extension** (esempio: **tags@2x.png**)

Quando aggiunta a un progetto, sarebbe simile al seguente:

![I file di immagine nel riquadro soluzione](image-images/add03.png "i file di immagine nel riquadro soluzione")

Quando un'immagine viene assegnata a un elemento dell'interfaccia utente in Generatore di interfaccia sarà sufficiente selezionare il file di _ImageName_**.** _-estensione_ formato (esempio: **tags.png**). Lo stesso per l'utilizzo di un'immagine nel codice c#, verrà scelto il file di _ImageName_**.** _-estensione_ formato.

Quando si Xamarin.Mac applicazione viene eseguita su un Mac, il _ImageName_**.** _-estensione_ immagine in formato da utilizzare nelle visualizzazioni Standard di risoluzione, il **ImageName@2x.filename-extension** immagine verrà automaticamente rilevata Display Retina basi computer Mac.


## <a name="using-images-in-interface-builder"></a>Utilizzo di immagini in Generatore di interfaccia

Qualsiasi risorsa immagine di aver aggiunto il **risorse** il Xamarin.Mac cartella del progetto e avere impostato l'azione di compilazione su **BundleResource** verrà automaticamente visualizzata nell'interfaccia generatore e può essere selezionato come parte di un elemento dell'interfaccia utente (se vengono gestite le immagini).

Per utilizzare un'immagine in Generatore di interfaccia, eseguire le operazioni seguenti:

1. Aggiungere un'immagine di **risorse** cartella con un **azione di compilazione** di `BundleResource`: 

     ![Una risorsa immagine nel riquadro soluzione](image-images/ib00.png "una risorsa immagine nel riquadro soluzione")
2. Fare doppio clic su di **Main** file per aprirlo e modificarlo in Generatore di interfaccia: 

     [![La modifica di storyboard principale](image-images/ib01.png "la modifica di storyboard principale")](image-images/ib01-large.png#lightbox)
3. Trascinare un elemento dell'interfaccia utente che accetta le immagini nell'area di progettazione (ad esempio, un **immagine della barra degli strumenti**): 

     ![Modifica di un elemento della barra degli strumenti](image-images/ib02.png "modifica di un elemento della barra degli strumenti")
4. Selezionare l'immagine che è stato aggiunto per il **risorse** cartella la **nome immagine** elenco a discesa: 

     [![Selezione di un'immagine per un elemento della barra degli strumenti](image-images/ib03.png "selezionando un'immagine per un elemento della barra degli strumenti")](image-images/ib03-large.png#lightbox)
5. L'immagine selezionata verrà visualizzato nell'area di progettazione: 

     ![L'immagine viene visualizzata nell'editor barra degli strumenti](image-images/ib04.png "l'immagine viene visualizzata nell'editor barra degli strumenti")
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Utilizzare i passaggi precedenti per qualsiasi elemento dell'interfaccia utente che consente la proprietà dell'immagine da impostare nella **controllo attributo**. Anche se è stato incluso un **@2x** versione del file di immagine, usato automaticamente sul Display Retina basato su computer Mac.

> [!IMPORTANT]
> Se l'immagine non è disponibile nel **nome immagine** elenco a discesa, chiudere il progetto .storyboard in Xcode e riaprirlo da Visual Studio per Mac. Se l'immagine non è ancora disponibile, verificare che il relativo **azione di compilazione** è `BundleResource` e che l'immagine è stato aggiunto il **risorse** cartella.

## <a name="using-images-in-c-code"></a>Utilizzo di immagini nel codice c#

Quando si carica un'immagine in memoria usando codice c# Xamarin.Mac nell'applicazione in uso, l'immagine verrà archiviata in un `NSImage` oggetto. Se il file di immagine è stata inclusa nel pacchetto di applicazione Xamarin.Mac (incluso nelle risorse), utilizzare il codice seguente per caricare l'immagine:

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

Il codice precedente utilizza statica `ImageNamed("...")` metodo il `NSImage` classe per caricare l'immagine specificata nella memoria dal **risorse** cartella, se l'immagine non viene trovato, `null` verrà restituito. Ad esempio le immagini assegnate nel generatore di interfaccia, se è stato incluso un **@2x** versione del file di immagine, usato automaticamente sul Display Retina basato su computer Mac.

Per caricare immagini all'esterno di bundle dell'applicazione (dal file system Mac), utilizzare il codice seguente:

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>Utilizzo di immagini modello

In base alla struttura dell'app macOS, potrebbero esserci casi è necessario personalizzare l'immagine all'interno dell'interfaccia utente per corrispondere a una modifica nello schema di colore (ad esempio, in base alle preferenze dell'utente) o un'icona.

Per ottenere questo effetto, passare il _modalità rendering_ nell'asset di immagine per **immagine modello**:

[![Impostazione di un'immagine modello](image-images/templateimage01.png "impostazione di un'immagine modello")](image-images/templateimage01-large.png#lightbox)

Da Generatore di interfaccia di Xcode, assegnare l'Asset di immagine a un controllo dell'interfaccia utente:

![Selezione di un'immagine in interfaccia generatore del Xcode](image-images/templateimage02.png "selezionando un'immagine in Generatore del Xcode di interfaccia")

O, facoltativamente, impostare l'origine dell'immagine nel codice:

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

Aggiungere la seguente funzione pubblica al Controller di visualizzazione:

```csharp
public NSImage ImageTintedWithColor (NSImage image, NSColor tint)
{
    var tintedImage = image.Copy () as NSImage;
    var frame = new CGRect (0, 0, image.Size.Width, image.Size.Height);

    // Apply tint
    tintedImage.LockFocus ();
    tint.Set ();
    NSGraphics.RectFill (frame, NSCompositingOperation.SourceAtop);
    tintedImage.UnlockFocus ();
    tintedImage.Template = false;

    // Return tinted image
    return tintedImage;
}
```

Infine, per colorare un'immagine modello, è possibile chiamare questa funzione per l'immagine per colorare:

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>Utilizzo di immagini con viste delle tabelle

Per includere un'immagine come parte della cella in un `NSTableView`, sarà necessario modificare la modalità con cui i dati vengono restituiti da parte della vista di tabella `NSTableViewDelegate's` `GetViewForItem` metodo da utilizzare un `NSTableCellView` anziché la tipica `NSTextField`. Ad esempio:

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

Esistono alcune righe di interesse qui. Innanzitutto, per le colonne che si desidera includere un'immagine, si creerà un nuovo `NSImageView` della posizione e dimensioni necessarie, è inoltre creare un nuovo `NSTextField` e inserire la posizione predefinita in base che si sta usando un'immagine:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

In secondo luogo, è necessario includere la nuova visualizzazione immagine campo di testo nell'elemento padre `NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Infine, è necessario indicare il campo di testo che può ridurre e aumento delle dimensioni con una cella di visualizzazione della tabella:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Output di esempio:

[![Un esempio di visualizzazione di un'immagine in un'app](image-images/tables01.png "un esempio di visualizzazione di un'immagine in un'app")](image-images/tables01-large.png#lightbox)

Per ulteriori informazioni sull'utilizzo di viste di tabella, vedere il nostro [viste delle tabelle](~/mac/user-interface/table-view.md) documentazione.

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>Utilizzo di immagini con visualizzazioni della struttura

Per includere un'immagine come parte della cella in un `NSOutlineView`, sarà necessario modificare la modalità con cui i dati vengono restituiti per la visualizzazione di struttura `NSTableViewDelegate's` `GetView` metodo da utilizzare un `NSTableCellView` anziché la tipica `NSTextField`. Ad esempio:

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

Esistono alcune righe di interesse qui. Innanzitutto, per le colonne che si desidera includere un'immagine, si creerà un nuovo `NSImageView` della posizione e dimensioni necessarie, è inoltre creare un nuovo `NSTextField` e inserire la posizione predefinita in base che si sta usando un'immagine:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

In secondo luogo, è necessario includere la nuova visualizzazione immagine campo di testo nell'elemento padre `NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Infine, è necessario indicare il campo di testo che può ridurre e aumento delle dimensioni con una cella di visualizzazione della tabella:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Output di esempio:

[![Un esempio di un'immagine viene visualizzata in una visualizzazione struttura](image-images/outline01.png "un esempio di un'immagine viene visualizzata in una visualizzazione struttura")](image-images/outline01-large.png#lightbox)

Per ulteriori informazioni sull'utilizzo di visualizzazioni della struttura, consultare il nostro [visualizzazioni della struttura](~/mac/user-interface/outline-view.md) documentazione.


## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato l'utilizzo di immagini e icone in un'applicazione Xamarin.Mac un'analisi approfondita. È stato illustrato i diversi tipi e sugli usi di immagini, come utilizzare le immagini e icone in interfaccia generatore del Xcode e sull'utilizzo di immagini e icone nel codice c#.



## <a name="related-links"></a>Collegamenti correlati

- [MacImages (sample)](https://developer.xamarin.com/samples/mac/MacImages/) (MacImages - Esempio)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Viste delle tabelle](~/mac/user-interface/table-view.md)
- [Visualizzazioni della struttura](~/mac/user-interface/outline-view.md)
- [macOS X linee guida dell'interfaccia umana](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [About High Resolution for OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html) (Informazioni sulla risoluzione elevata per OS X)
