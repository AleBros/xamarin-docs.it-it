---
title: Immagini in Novell. Mac
description: Questo articolo illustra l'uso di immagini e icone in un'applicazione Novell. Mac. Viene descritto come creare e gestire le immagini necessarie per creare l'icona dell'applicazione e usare le immagini nel codice C# e in Interface Builder di Xcode.
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: b6681832ce83bbc2783ba874c5902c30013c8c94
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84572493"
---
# <a name="images-in-xamarinmac"></a>Immagini in Novell. Mac

_Questo articolo illustra l'uso di immagini e icone in un'applicazione Novell. Mac. Viene descritto come creare e gestire le immagini necessarie per creare l'icona dell'applicazione e usare le immagini nel codice C# e in Interface Builder di Xcode._

## <a name="overview"></a>Panoramica

Quando si lavora con C# e .NET in un'applicazione Novell. Mac, si ha accesso agli stessi strumenti per immagini e icone che uno sviluppatore lavora in *Objective-C* e *Xcode* .

Sono disponibili diversi modi per usare le risorse dell'immagine in un'applicazione macOS (nota in precedenza come Mac OS X). Dalla semplice visualizzazione di un'immagine come parte dell'interfaccia utente dell'applicazione a, assegnarla a un controllo dell'interfaccia utente, ad esempio una barra degli strumenti o un elemento dell'elenco di origine, per fornire le icone, Novell. Mac consente di aggiungere in modo semplice un'ottima grafica alle applicazioni macOS nei modi seguenti: 

- **Elementi dell'interfaccia utente** : le immagini possono essere visualizzate come sfondi o come parte dell'applicazione in una visualizzazione immagine ( `NSImageView` ).
- **Button** : le immagini possono essere visualizzate nei pulsanti ( `NSButton` ).
- **Cella immagine** : come parte di un controllo basato su tabella ( `NSTableView` o `NSOutlineView` ), le immagini possono essere utilizzate in una cella immagine ( `NSImageCell` ).
- **Elemento della barra degli strumenti** -le immagini possono essere aggiunte a una barra degli strumenti ( `NSToolbar` ) come elemento della barra degli strumenti immagine ( `NSToolbarItem` ).
- **Icona dell'elenco di origine** : come parte di un elenco di origine (formattato in modo specifico `NSOutlineView` ).
- **Icona dell'app** : è possibile raggruppare una serie di immagini in un `.icns` set e usarle come icona dell'applicazione. Per ulteriori informazioni, vedere la documentazione [sull'icona dell'applicazione](~/mac/deploy-test/app-icon.md) .

MacOS fornisce inoltre un set di immagini predefinite che possono essere usate in tutta l'applicazione.

[![Esempio di esecuzione dell'app](image-images/intro01.png "Esempio di esecuzione dell'app")](image-images/intro01-large.png#lightbox)

In questo articolo verranno illustrate le nozioni di base per l'uso di immagini e icone in un'applicazione Novell. Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in questo articolo.

## <a name="adding-images-to-a-xamarinmac-project"></a>Aggiunta di immagini a un progetto Novell. Mac

Quando si aggiunge un'immagine da usare in un'applicazione Novell. Mac, esistono diverse posizioni e modi in cui lo sviluppatore può includere un file di immagine nell'origine del progetto:

- **Albero del progetto principale [deprecato]** -le immagini possono essere aggiunte direttamente all'albero dei progetti. Quando si chiamano le immagini archiviate nell'albero del progetto principale dal codice, non viene specificato alcun percorso di cartella. Ad esempio: `NSImage image = NSImage.ImageNamed("tags.png");`. 
- **Cartella Resources [deprecato]** -la cartella **delle risorse** speciali è per qualsiasi file che diventerà parte del bundle dell'applicazione, ad esempio icona, schermata di avvio o immagini generali (o qualsiasi altra immagine o file che lo sviluppatore desidera aggiungere). Quando si chiamano le immagini archiviate nella cartella **Resources** dal codice, proprio come le immagini archiviate nell'albero del progetto principale, non viene specificato alcun percorso di cartella. Ad esempio: `NSImage.ImageNamed("tags.png")`.
- **Cartella personalizzata o sottocartella [deprecato]** -lo sviluppatore può aggiungere una cartella personalizzata all'albero di origine dei progetti e archiviarvi le immagini. Il percorso in cui viene aggiunto il file può essere annidato in una sottocartella per facilitare l'organizzazione del progetto. Se, ad esempio, lo sviluppatore ha aggiunto una `Card` cartella al progetto e una sottocartella di `Hearts` a tale cartella, archiviare un'immagine **Jack. png** nella `Hearts` cartella, `NSImage.ImageNamed("Card/Hearts/Jack.png")` caricherà l'immagine in fase di esecuzione.
- **Asset Catalog Image Set [preferito]** -aggiunto in OS X El Capitan, i **set di immagini di asset Catalogs** contengono tutte le versioni o rappresentazioni di un'immagine che sono necessarie per supportare i vari dispositivi e i fattori di scalabilità per l'applicazione. Anziché basarsi sul nome file delle risorse immagine ( **@1x** , **@2x** ).

<a name="asset-catalogs"></a>

### <a name="adding-images-to-an-asset-catalog-image-set"></a>Aggiunta di immagini a un set di immagini del catalogo asset

Come indicato in precedenza, i **set di immagini di cataloghi asset** contengono tutte le versioni o rappresentazioni di un'immagine che sono necessarie per supportare diversi dispositivi e i fattori di scalabilità per l'applicazione. Invece di basarsi sul nome file delle risorse immagine (vedere le immagini indipendenti dalla risoluzione e la nomenclatura dell'immagine precedente), i **set di immagini** usano l'editor asset per specificare quale immagine appartiene a quale dispositivo e/o risoluzione.

1. Nel **riquadro della soluzione**fare doppio clic sul file **assets. xcassets** per aprirlo per la modifica: 

    ![Selezione degli asset. xcassets](image-images/imageset01.png "Selezione degli asset. xcassets")
2. Fare clic con il pulsante destro del mouse sull' **elenco asset** e scegliere **nuovo set di immagini**: 

    [![Aggiunta di un nuovo set di immagini](image-images/imageset02.png "Aggiunta di un nuovo set di immagini")](image-images/imageset02-large.png#lightbox)
3. Selezionare il nuovo set di immagini e l'editor verrà visualizzato: 

    [![Selezione del nuovo set di immagini](image-images/imageset03.png "Selezione del nuovo set di immagini")](image-images/imageset03-large.png#lightbox)
4. Da qui è possibile trascinare le immagini per ognuno dei diversi dispositivi e risoluzioni necessarie. 
5. Fare doppio clic sul **nome** del nuovo set di immagini nell' **elenco asset** per modificarlo: 

    [![Modifica del nome del set di immagini](image-images/imageset04.png "Modifica del nome del set di immagini")](image-images/imageset04-large.png#lightbox)
    
Una classe **vector** speciale aggiunta ai set di **Immagini** che ci permette di includere un'immagine vettoriale formattata in _formato PDF_ nel set di dati, che include invece singoli file bitmap con le diverse risoluzioni. Utilizzando questo metodo, è necessario fornire un singolo file vettoriale per la **@1x** risoluzione (formattato come file PDF vettoriale) e **@2x** le **@3x** versioni e del file verranno generate in fase di compilazione e incluse nel bundle dell'applicazione.

[![Interfaccia dell'editor del set di immagini](image-images/imageset05.png "Interfaccia dell'editor del set di immagini")](image-images/imageset05-large.png#lightbox)

Se, ad esempio, si include un `MonkeyIcon.pdf` file come vettore di un catalogo asset con una risoluzione di 150px x 150px, le risorse bitmap seguenti verranno incluse nel bundle dell'app finale al momento della compilazione:

1. **MonkeyIcon@1x.png**-150px x 150px risoluzione.
2. **MonkeyIcon@2x.png**-300px x 300px risoluzione.
3. **MonkeyIcon@3x.png**-450px x 450px risoluzione.

Quando si usano le immagini vettoriali PDF nei cataloghi di asset, è necessario tenere in considerazione quanto segue:

- Non si tratta di un supporto vettoriale completo poiché il formato PDF verrà rasterizzato in una bitmap in fase di compilazione e le bitmap fornite nell'applicazione finale.
- Non è possibile modificare le dimensioni dell'immagine dopo che è stata impostata nel catalogo asset. Se si tenta di ridimensionare l'immagine (nel codice o utilizzando il layout automatico e le classi di dimensioni), l'immagine verrà distorta esattamente come qualsiasi altra bitmap.

Quando si usa un **set di immagini** nella Interface Builder di Xcode, è possibile semplicemente selezionare il nome del set dall'elenco a discesa nel **controllo attributi**:

![Selezione di un set di immagini nella Interface Builder di Xcode](image-images/imageset06.png "Selezione di un set di immagini nella Interface Builder di Xcode")

<a name="Adding-new-Assets-Collections"></a>

### <a name="adding-new-assets-collections"></a>Aggiunta di nuove raccolte di asset

Quando si lavora con le immagini nei cataloghi di asset, a volte può essere necessario creare una nuova raccolta, anziché aggiungere tutte le immagini alla raccolta **assets. xcassets** . Ad esempio, quando si progettano risorse su richiesta.

Per aggiungere un nuovo catalogo asset al progetto:

1. Fare clic con il pulsante destro del mouse sul progetto nella **riquadro della soluzione** e scegliere **Aggiungi**  >  **nuovo file...**
2. Selezionare **Mac**  >  **Asset Catalog**, immettere un **nome** per la raccolta e fare clic sul pulsante **nuovo** : 

    ![Aggiunta di un nuovo catalogo asset](image-images/asset01.png "Aggiunta di un nuovo catalogo asset")

Da qui è possibile usare la raccolta in modo analogo alla raccolta **assets. xcassets** predefinita inclusa automaticamente nel progetto.

### <a name="adding-images-to-resources"></a>Aggiunta di immagini alle risorse

> [!IMPORTANT]
> Questo metodo di uso delle immagini in un'app macOS è stato deprecato da Apple. È consigliabile usare i [set di immagini del catalogo asset](#asset-catalogs) per dirigere le immagini dell'app.

Prima di poter usare un file di immagine nell'applicazione Novell. Mac (in codice C# o da Interface Builder), è necessario che sia incluso nella cartella **Resources** del progetto come **risorsa bundle**. Per aggiungere un file a un progetto, procedere come segue:

1. Fare clic con il pulsante destro del mouse sulla cartella **risorse** del progetto nella **riquadro della soluzione** e scegliere **Aggiungi**  >  **Aggiungi file...**: 

    ![Aggiunta di un file](image-images/add01.png "Aggiunta di un file")
2. Nella finestra di dialogo **Aggiungi file** selezionare i file di immagine da aggiungere al progetto, selezionare `BundleResource` per l' **azione Sostituisci compilazione** e fare clic sul pulsante **Apri** :

    [![Selezione dei file da aggiungere](image-images/add02.png "Selezione dei file da aggiungere")](image-images/add02-large.png#lightbox)
3. Se i file non sono già presenti nella cartella **Resources** , verrà chiesto se si desidera **copiare**, **spostare** o **collegare** i file. Scegliere la scelta più adatta alle proprie esigenze, in genere che verrà **copiata**:

    ![Selezione dell'azione Aggiungi](image-images/add04.png "Selezione dell'azione Aggiungi")
4. I nuovi file verranno inclusi nel progetto e letti per l'uso: 

    ![I nuovi file di immagine aggiunti alla riquadro della soluzione](image-images/add03.png "I nuovi file di immagine aggiunti alla riquadro della soluzione")
5. Ripetere il processo per tutti i file di immagine richiesti.

È possibile usare qualsiasi file PNG, jpg o PDF come immagine di origine nell'applicazione Novell. Mac. Nella sezione successiva si esamineranno le versioni ad alta risoluzione delle immagini e delle icone per supportare i Mac basati su retina.

> [!IMPORTANT]
> Se si aggiungono immagini alla cartella **Resources** , è possibile lasciare l' **azione Sostituisci compilazione** impostata su **predefinito**. L'azione di compilazione predefinita per questa cartella è `BundleResource` .

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>Fornire versioni ad alta risoluzione di tutte le risorse grafiche di app

Qualsiasi asset grafico aggiunto a un'applicazione Novell. Mac (icone, controlli personalizzati, cursori personalizzati, immagini personalizzate e così via) deve disporre di versioni ad alta risoluzione, oltre alle versioni di risoluzione standard. Questa operazione è necessaria in modo che l'applicazione abbia un aspetto ottimale quando viene eseguita in un computer Mac dotato di display retina.

### <a name="adopt-the-2x-naming-convention"></a>Adottare la @2x convenzione di denominazione

> [!IMPORTANT]
> Questo metodo di uso delle immagini in un'app macOS è stato deprecato da Apple. È consigliabile usare i [set di immagini del catalogo asset](#asset-catalogs) per dirigere le immagini dell'app.

Quando si creano le versioni standard e ad alta risoluzione di un'immagine, seguire questa convenzione di denominazione per la coppia di immagini quando vengono incluse nel progetto Novell. Mac:

- **Risoluzione standard**   -  **ImageName. Filename-Extension** (esempio: **Tags. png**)
- **Alta risoluzione**   -  **ImageName@2x.filename-extension** (Esempio: **tags@2x.png** )

Quando vengono aggiunti a un progetto, vengono visualizzati nel modo seguente:

![I file di immagine nel riquadro della soluzione](image-images/add03.png "I file di immagine nel riquadro della soluzione")

Quando un'immagine viene assegnata a un elemento dell'interfaccia utente in Interface Builder, è sufficiente selezionare il file in _ImageName_**.** _nome file-formato estensione_ (esempio: **Tags. png**). Allo stesso modo per l'uso di un'immagine nel codice C#, il file verrà selezionato in _ImageName_**.** _nome file-formato estensione_ .

Quando l'applicazione Novell. Mac viene eseguita in un Mac, _ImageName_**.** _nome file:_ l'immagine del formato dell'estensione verrà usata per le visualizzazioni di risoluzione standard **ImageName@2x.filename-extension** . l'immagine verrà selezionata automaticamente nei computer Mac di base della retina.

## <a name="using-images-in-interface-builder"></a>Uso di immagini in Interface Builder

Qualsiasi risorsa immagine aggiunta alla cartella **Resources** nel progetto Novell. Mac e aver impostato l'azione di compilazione su **BundleResource** verrà visualizzata automaticamente in Interface Builder e potrà essere selezionata come parte di un elemento dell'interfaccia utente (se gestisce le immagini).

Per usare un'immagine in Interface Builder, seguire questa procedura:

1. Aggiungere un'immagine alla cartella **Resources** con un' **azione di compilazione** `BundleResource` : 

     ![Risorsa immagine nel riquadro della soluzione](image-images/ib00.png "Risorsa immagine nel riquadro della soluzione")
2. Fare doppio clic sul file **Main. Storyboard** per aprirlo per la modifica in Interface Builder: 

     [![Modifica dello storyboard principale](image-images/ib01.png "Modifica dello storyboard principale")](image-images/ib01-large.png#lightbox)
3. Trascinare un elemento dell'interfaccia utente che acquisisce immagini nell'area di progettazione (ad esempio, un **elemento della barra degli strumenti immagine**): 

     ![Modifica di un elemento della barra degli strumenti](image-images/ib02.png "Modifica di un elemento della barra degli strumenti")
4. Selezionare l'immagine aggiunta alla cartella **risorse** nell'elenco a discesa **nome immagine** : 

     [![Selezione di un'immagine per un elemento della barra degli strumenti](image-images/ib03.png "Selezione di un'immagine per un elemento della barra degli strumenti")](image-images/ib03-large.png#lightbox)
5. L'immagine selezionata verrà visualizzata nell'area di progettazione: 

     ![Immagine visualizzata nell'editor della barra degli strumenti](image-images/ib04.png "Immagine visualizzata nell'editor della barra degli strumenti")
6. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

I passaggi precedenti funzionano per qualsiasi elemento dell'interfaccia utente che consente di impostare la relativa proprietà Image nel **controllo attributo**. Anche in questo caso, se è stata inclusa una **@2x** versione del file di immagine, questa verrà usata automaticamente nei computer Mac basati su display retina.

> [!IMPORTANT]
> Se l'immagine non è disponibile nell'elenco a discesa **nome immagine** , chiudere il progetto. Storyboard in Xcode e riaprirlo dalla Visual Studio per Mac. Se l'immagine non è ancora disponibile, assicurarsi che l' **azione di compilazione** sia `BundleResource` e che l'immagine sia stata aggiunta alla cartella **risorse** .

## <a name="using-images-in-c-code"></a>Uso di immagini in codice C#

Quando si carica un'immagine in memoria usando il codice C# nell'applicazione Novell. Mac, l'immagine verrà archiviata in un `NSImage` oggetto. Se il file di immagine è stato incluso nel bundle di applicazioni Novell. Mac (incluso nelle risorse), usare il codice seguente per caricare l'immagine:

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

Il codice precedente usa il `ImageNamed("...")` metodo statico della `NSImage` classe per caricare l'immagine specificata in memoria dalla cartella **delle risorse** , se non è possibile trovare l'immagine, `null` verrà restituito. Analogamente alle immagini assegnate in Interface Builder, se è stata inclusa una **@2x** versione del file di immagine, questa verrà usata automaticamente nei computer Mac basati su display retina.

Per caricare immagini all'esterno del bundle dell'applicazione (dal file system Mac), usare il codice seguente:

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"></a>

## <a name="working-with-template-images"></a>Uso delle immagini modello

In base alla progettazione dell'app macOS, a volte è necessario personalizzare un'icona o un'immagine all'interno dell'interfaccia utente in modo che corrisponda a una modifica nella combinazione di colori, ad esempio in base alle preferenze dell'utente.

Per ottenere questo risultato, impostare la _modalità di rendering_ dell'asset immagine sull' **immagine modello**:

[![Impostazione di un'immagine modello](image-images/templateimage01.png "Impostazione di un'immagine modello")](image-images/templateimage01-large.png#lightbox)

Dalla Interface Builder di Xcode assegnare l'asset immagine a un controllo dell'interfaccia utente:

![Selezione di un'immagine nella Interface Builder di Xcode](image-images/templateimage02.png "Selezione di un'immagine nella Interface Builder di Xcode")

Oppure, facoltativamente, impostare l'origine dell'immagine nel codice:

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

Aggiungere la funzione pubblica seguente al controller di visualizzazione:

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
> In particolare, con l'avvento della modalità scura in macOS Mojave, è importante evitare l' `LockFocus` API quando si Reating oggetti con rendering personalizzato `NSImage` . Tali immagini diventano statiche e non verranno aggiornate automaticamente per tenere conto delle modifiche apportate alla densità dell'aspetto o della visualizzazione.
>
> Utilizzando il meccanismo basato sul gestore precedente, il nuovo rendering per le condizioni dinamiche si verificherà automaticamente quando `NSImage` viene ospitato, ad esempio, in un oggetto `NSImageView` .

Infine, per tingere un'immagine modello, chiamare questa funzione sull'immagine per colorarla:

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views"></a>

## <a name="using-images-with-table-views"></a>Utilizzo di immagini con visualizzazioni di tabella

Per includere un'immagine come parte della cella di un `NSTableView` , è necessario modificare il modo in cui i dati vengono restituiti dal metodo della visualizzazione tabella `NSTableViewDelegate's` `GetViewForItem` per utilizzare un `NSTableCellView` anziché il tipico `NSTextField` . Ad esempio:

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

Qui sono disponibili alcune righe di interesse. In primo luogo, per le colonne che si desidera includere un'immagine, viene creato un nuovo `NSImageView` della dimensione e della posizione richieste, viene anche creato un nuovo `NSTextField` e viene posizionata la posizione predefinita a seconda che si usi o meno un'immagine:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

In secondo luogo, è necessario includere la nuova visualizzazione immagine e il campo di testo nell'elemento padre `NSTableCellView` :

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Infine, è necessario indicare al campo di testo che può essere compattato e ampliato con la cella di visualizzazione tabella:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Output di esempio:

[![Esempio di visualizzazione di un'immagine in un'app](image-images/tables01.png "Esempio di visualizzazione di un'immagine in un'app")](image-images/tables01-large.png#lightbox)

Per ulteriori informazioni sull'utilizzo delle visualizzazioni tabella, vedere la documentazione relativa alle [visualizzazioni di tabella](~/mac/user-interface/table-view.md) .

<a name="Using_Images_with_Outline_Views"></a>

## <a name="using-images-with-outline-views"></a>Utilizzo di immagini con visualizzazioni struttura

Per includere un'immagine come parte della cella di un `NSOutlineView` , è necessario modificare il modo in cui i dati vengono restituiti dal metodo della visualizzazione struttura `NSTableViewDelegate's` `GetView` per usare un `NSTableCellView` anziché il tipico `NSTextField` . Ad esempio:

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

Qui sono disponibili alcune righe di interesse. In primo luogo, per le colonne che si desidera includere un'immagine, viene creato un nuovo `NSImageView` della dimensione e della posizione richieste, viene anche creato un nuovo `NSTextField` e viene posizionata la posizione predefinita a seconda che si usi o meno un'immagine:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

In secondo luogo, è necessario includere la nuova visualizzazione immagine e il campo di testo nell'elemento padre `NSTableCellView` :

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Infine, è necessario indicare al campo di testo che può essere compattato e ampliato con la cella di visualizzazione tabella:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Output di esempio:

[![Esempio di immagine visualizzata in una visualizzazione struttura](image-images/outline01.png "Esempio di immagine visualizzata in una visualizzazione struttura")](image-images/outline01-large.png#lightbox)

Per ulteriori informazioni sull'utilizzo delle visualizzazioni struttura, consultare la documentazione relativa alle [visualizzazioni struttura](~/mac/user-interface/outline-view.md) .

## <a name="summary"></a>Summary

Questo articolo ha esaminato in dettaglio l'uso di immagini e icone in un'applicazione Novell. Mac. Abbiamo visto i diversi tipi e usi delle immagini, come usare immagini e icone nei Interface Builder di Xcode e come lavorare con immagini e icone nel codice C#.

## <a name="related-links"></a>Collegamenti correlati

- [MacImages (sample)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages) (MacImages - Esempio)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Viste tabella](~/mac/user-interface/table-view.md)
- [Visualizzazione struttura](~/mac/user-interface/outline-view.md)
- [Linee guida per l'interfaccia umana macOS X](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [About High Resolution for OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html) (Informazioni sulla risoluzione elevata per OS X)
