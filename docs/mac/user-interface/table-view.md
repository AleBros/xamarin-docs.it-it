---
title: Visualizzazioni di tabelle in xamarin. Mac
description: Questo articolo descrive l'uso delle visualizzazioni tabella in un'applicazione xamarin. Mac. Descrive la creazione di viste di tabella in Xcode e Interface Builder e sull'interazione con essi nel codice.
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 1361f81c20b070c73b1f1f308ea4a5a351c4840d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104674"
---
# <a name="table-views-in-xamarinmac"></a>Visualizzazioni di tabelle in xamarin. Mac

_Questo articolo descrive l'uso delle visualizzazioni tabella in un'applicazione xamarin. Mac. Descrive la creazione di viste di tabella in Xcode e Interface Builder e sull'interazione con essi nel codice._

Quando si usa c# e .NET in un'applicazione xamarin. Mac, si ha accesso alla stessa tabella che visualizza gli sviluppatori che lavorano *Objective-C* e *Xcode* viene. Poiché xamarin. Mac si integra direttamente con Xcode, è possibile usare Xcode _Interface Builder_ per creare e gestire le visualizzazioni di tabella (oppure crearle direttamente nel codice c#).

Visualizzazione di una tabella consente di visualizzare i dati in un formato tabulare che contiene uno o più colonne di informazioni in più righe. In base al tipo della visualizzazione di tabella viene creato, l'utente può ordinare per colonna, riorganizzare le colonne, aggiungere colonne, rimuovere le colonne o modificare i dati contenuti all'interno della tabella.

[![](table-view-images/intro01.png "Una tabella di esempio")](table-view-images/intro01.png#lightbox)

In questo articolo, si affronterà le nozioni di base dell'utilizzo delle visualizzazioni tabella in un'applicazione xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` comandi utilizzato per wireup classi c# per gli oggetti di Objective-C e gli elementi dell'interfaccia utente.

<a name="Introduction_to_Table_Views" />

## <a name="introduction-to-table-views"></a>Introduzione alle visualizzazioni di tabella

Visualizzazione di una tabella consente di visualizzare i dati in un formato tabulare che contiene uno o più colonne di informazioni in più righe. Viste delle tabelle vengono visualizzate all'interno di viste di scorrimento (`NSScrollView`) e a partire da macOS 10.7, è possibile usare qualsiasi `NSView` anziché le celle (`NSCell`) per visualizzare sia le righe e colonne. Ciò premesso, è comunque possibile usare `NSCell` tuttavia, si sarà in genere sottoclasse `NSTableCellView` e creare righe personalizzate e colonne.

Visualizzazione di una tabella non archivia i propri dati, ma si basa su un'origine dati (`NSTableViewDataSource`) per fornire sia le righe e colonne necessari, su una base alle esigenze.

Comportamento della visualizzazione di una tabella può essere personalizzato fornendo una sottoclasse del delegato di visualizzazione tabella (`NSTableViewDelegate`) per supportare la gestione di colonne di tabella, digitare per selezionare funzionalità, la selezione della riga e la modifica, rilevamento personalizzati e visualizzazioni personalizzate per singole colonne e righe.

Quando si creano le visualizzazioni di tabelle, le seguenti suggerite da Apple:

* Consente all'utente di ordinare la tabella facendo clic su un'intestazione di colonna.
* Creare le intestazioni di colonna che sono o breve locuzioni che descrivono i dati attualmente visualizzati in tale colonna.

Per altre informazioni, vedere la [viste contenuto](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) sezione di Apple [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating-and-Maintaining-Table-Views-in-Xcode" />

## <a name="creating-and-maintaining-table-views-in-xcode"></a>Creare e gestire le visualizzazioni di tabella in Xcode

Quando si crea una nuova applicazione xamarin. Mac Cocoa, si ottiene una normale finestra vuota, per impostazione predefinita. Questo windows è definito in un `.storyboard` file automaticamente inclusi nel progetto. Per modificare la progettazione di windows, nelle **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file:

[![](table-view-images/edit01.png "Selezionando lo storyboard principale")](table-view-images/edit01.png#lightbox)

Verrà aperta la progettazione di finestra in Interface Builder di Xcode:

[![](table-view-images/edit02.png "Modifica l'interfaccia utente in Xcode")](table-view-images/edit02.png#lightbox)

Tipo di `table` nella **Library Inspector** casella di ricerca per renderne più semplice trovare i controlli di visualizzazione di tabella:

[![](table-view-images/edit03.png "Selezione di una visualizzazione tabella dalla libreria")](table-view-images/edit03.png#lightbox)

Trascinare il Controller di visualizzazione in una visualizzazione tabella il **Editor dell'interfaccia**, renderlo riempire l'area di contenuto del Controller di visualizzazione e impostarlo su cui si riduce e cresce con la finestra di **Editor vincoli di**:

[![](table-view-images/edit04.png "Modifica di vincoli")](table-view-images/edit04.png#lightbox)

Selezionare la vista tabella le **gerarchia delle interfacce** e le proprietà seguenti sono disponibili nel **attributo Inspector**:

[![](table-view-images/edit05.png "Attribute Inspector")](table-view-images/edit05.png#lightbox)

- **Modalità di contenuto** -consente di usare entrambe le viste (`NSView`) o le celle (`NSCell`) per visualizzare i dati in righe e colonne. A partire da macOS 10.7, è necessario utilizzare le viste.
- **Gruppo di righe è mobile rispetto** - se `true`, visualizzazione della tabella verrà disegnato celle raggruppate come se si sono variabili.
- **Colonne** -definisce il numero di colonne visualizzate.
- **Le intestazioni** - se `true`, le colonne avranno le intestazioni.
- **Riordinamento** - se `true`, l'utente sarà in grado di trascinare riordinare le colonne nella tabella.
- **Il ridimensionamento** - se `true`, l'utente sarà in grado di trascinare le intestazioni di colonna per ridimensionare le colonne.
- **Ridimensionamento di colonne** -controlla come la tabella verrà automaticamente le colonne di dimensioni.
- **Evidenziare** -controlli il tipo di evidenziazione della tabella usati quando è selezionata una cella.
- **Le righe alternate** - se `true`, mai altra riga avrà un colore di sfondo differente.
- **Griglia orizzontale** -consente di selezionare il tipo di bordo disegnato tra le celle orizzontalmente.
- **Griglia verticale** -consente di selezionare il tipo di bordo disegnata in verticale tra le celle.
- **Colore griglia** -imposta il colore del bordo della cella.
- **Sfondo** -imposta il colore di sfondo della cella.
- **Selezione** -consentono di controllare la modalità con cui l'utente può selezionare le celle nella tabella come:
    - **Più** - se `true`, l'utente può selezionare più righe e colonne.
    - **Colonna** - se `true`, l'utente può selezionare le colonne.
    - **Digitare Select** - se `true`, l'utente può digitare un carattere da utilizzare per selezionare una riga.
    - **Vuoto** - se `true`, l'utente non è necessario selezionare una riga o colonna, la tabella consente la selezione non è affatto.
- **Salvataggio automatico** -Salva il nome che il formato di tabelle è automaticamente sotto.
- **Informazioni sulle colonne** - se `true`, l'ordine e la larghezza delle colonne verranno salvate automaticamente.
- **Interruzioni di riga** - selezionare in che modo la cella gestisce le interruzioni di riga.
- **Tronca l'ultima riga visibile** - se `true`, verrà troncata dell'oggetto dati è possibile non adattarsi all'interno dei limiti della cella.

> [!IMPORTANT]
> A meno che non si esegue la manutenzione di un'applicazione xamarin. Mac legacy `NSView` basati su viste delle tabelle deve essere utilizzate su `NSCell` basati su viste delle tabelle. `NSCell` viene considerato legacy e potrebbero non essere supportate in futuro.

Selezionare una colonna della tabella nel **gerarchia delle interfacce** e le proprietà seguenti sono disponibili nel **attributo Inspector**:

[![](table-view-images/edit06.png "Attribute Inspector")](table-view-images/edit06.png#lightbox)

- **Titolo** -imposta il titolo della colonna.
- **Allineamento** -impostare l'allineamento del testo all'interno delle celle.
- **Tipo di carattere del titolo** -consente di selezionare il tipo di carattere per il testo dell'intestazione della cella.
- **Chiave di ordinamento** -è la chiave usata per ordinare i dati della colonna. Lasciare vuoto se l'utente non è possibile ordinare questa colonna.
- **Selettore** -è il **azione** usato per eseguire l'ordinamento. Lasciare vuoto se l'utente non è possibile ordinare questa colonna.
- **Ordine** -è l'ordinamento per i dati di colonne.
- **Il ridimensionamento** -seleziona il tipo di ridimensionamento per la colonna.
- **Modificabile** - se `true`, l'utente può modificare le celle in una tabella di cella basati su.
- **Nascosto** - se `true`, la colonna è nascosta.

È anche possibile ridimensionare la colonna mediante il trascinamento dell'handle (centrati verticalmente nella parte destra della colonna) a sinistra o destra.

È possibile selezionare ogni colonna nella visualizzazione tabella e assegnare alla prima colonna un' **Title** dei `Product` e la seconda figura `Details`.

Selezionare una visualizzazione di cella di tabella (`NSTableViewCell`) nel **gerarchia delle interfacce** e le proprietà seguenti sono disponibili nel **attributo Inspector**:

[![](table-view-images/edit07.png "Attribute Inspector")](table-view-images/edit07.png#lightbox)

Queste sono tutte le proprietà di una vista standard. È inoltre la possibilità di ridimensionare le righe per questa colonna qui.

Selezionare una cella di visualizzazione tabella (per impostazione predefinita, questo è un `NSTextField`) nei **gerarchia delle interfacce** e le proprietà seguenti sono disponibili nel **attributo Inspector**:

[![](table-view-images/edit08.png "Attribute Inspector")](table-view-images/edit08.png#lightbox)

È possibile tutte le proprietà di un campo di testo standard da questa pagina. Per impostazione predefinita, un campo di testo standard consente di visualizzare i dati per una cella in una colonna.

Selezionare una visualizzazione di cella di tabella (`NSTableFieldCell`) nel **gerarchia delle interfacce** e le proprietà seguenti sono disponibili nel **attributo Inspector**:

[![](table-view-images/edit09.png "Attribute Inspector")](table-view-images/edit09.png#lightbox)

Di seguito le impostazioni più importanti sono:

- **Layout** : selezionare la modalità di celle in questa colonna vengono disposti.
- **Usa modalità a riga singola** - se `true`, la cella è limitata a una singola riga.
- **Prima fase di esecuzione Layout larghezza** : se `true`, preferisce la larghezza impostata per tale (automaticamente o manualmente) quando la cella viene visualizzato alla prima esecuzione dell'applicazione.
- **Azione** -esegue i controlli di modifica **azione** viene inviato per la cella.
- **Comportamento** -definisce se una cella è selezionabile né modificabile.
- **Rich Text** - se `true`, la cella può visualizzare il testo formattato e con stile.
- **Annullare** - se `true`, la cella si assume la responsabilità per è annullare il comportamento.

Selezionare la visualizzazione della cella di tabella (`NSTableFieldCell`) nella parte inferiore di una colonna di tabella nel **gerarchia interfaccia**:

[![](table-view-images/edit10.png "Selezione di visualizzazione della cella di tabella")](table-view-images/edit10.png#lightbox)

In questo modo è possibile modificare la vista di cella di tabella usata come base _Pattern_ per tutte le celle create per la colonna specificata.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Aggiunta di azioni e gli Outlet

Proprio come qualsiasi altro controllo interfaccia utente di Cocoa, è necessario esporre la visualizzazione di tabella e si tratta di colonne e le celle di codice c# tramite **azioni** e **Outlet** (basato sulle funzionalità necessarie).

Il processo è lo stesso per qualsiasi elemento di visualizzazione tabella che si desidera esporre:

1. Passare al **Assistente dell'Editor** e assicurarsi che il `ViewController.h` viene selezionato un file: 

    [![](table-view-images/edit11.png "Assistente dell'Editor")](table-view-images/edit11.png#lightbox)
2. Selezionare la visualizzazione di tabella dal **gerarchia delle interfacce**, CTRL + clic e trascinare il `ViewController.h` file.
3. Creare un **Outlet** per la visualizzazione della tabella denominata `ProductTable`: 

    [![](table-view-images/edit13.png "Configurazione di un Outlet")](table-view-images/edit13.png#lightbox)
4. Creare **Outlet** per le colonne di tabelle anche chiamato `ProductColumn` e `DetailsColumn`: 

    [![](table-view-images/edit14.png "Configurazione di un Outlet")](table-view-images/edit14.png#lightbox)
5. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Successivamente, si scriverà la visualizzazione codice alcuni dati per la tabella quando viene eseguita l'applicazione.

<a name="Populating_the_Table_View" />

## <a name="populating-the-table-view"></a>Popolare la visualizzazione di tabella

Con la visualizzazione di tabella progettate in Interface Builder e vengono esposte tramite un **Outlet**, successivamente è necessario creare il codice c# per popolarlo.

In primo luogo, è possibile creare un nuovo `Product` classe per contenere le informazioni per le singole righe. Nel **Esplora soluzioni**, fare clic sul progetto e selezionare **Add** > **nuovo File...** Selezionare **generali** > **classe vuota**, immettere `Product` per il **nome** e fare clic su di **New** pulsante:

[![](table-view-images/populate01.png "Creazione di una classe vuota")](table-view-images/populate01.png#lightbox)

Rendere il `Product.cs` file aspetto simile al seguente:

```csharp
using System;

namespace MacTables
{
    public class Product
    {
        #region Computed Propoperties
        public string Title { get; set;} = "";
        public string Description { get; set;} = "";
        #endregion

        #region Constructors
        public Product ()
        {
        }

        public Product (string title, string description)
        {
            this.Title = title;
            this.Description = description;
        }
        #endregion
    }
}

```

Successivamente, è necessario creare una sottoclasse di `NSTableDataSource` per fornire i dati per la tabella di base alle esigenze. Nel **Esplora soluzioni**, fare clic sul progetto e selezionare **Add** > **nuovo File...** Selezionare **generali** > **classe vuota**, immettere `ProductTableDataSource` per il **nome** e fare clic su di **New** pulsante.

Modificare il `ProductTableDataSource.cs` file e renderlo simile al seguente:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
    public class ProductTableDataSource : NSTableViewDataSource
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Constructors
        public ProductTableDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override nint GetRowCount (NSTableView tableView)
        {
            return Products.Count;
        }
        #endregion
    }
}

```

Questa classe dispone di spazio di archiviazione per gli elementi della visualizzazione tabella ed esegue l'override di `GetRowCount` per restituire il numero di righe nella tabella.

Infine, è necessario creare una sottoclasse di `NSTableDelegate` per fornire il comportamento per la tabella. Nel **Esplora soluzioni**, fare clic sul progetto e selezionare **Add** > **nuovo File...** Selezionare **generali** > **classe vuota**, immettere `ProductTableDelegate` per il **nome** e fare clic su di **New** pulsante.

Modificare il `ProductTableDelegate.cs` file e renderlo simile al seguente:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacTables
{
    public class ProductTableDelegate: NSTableViewDelegate
    {
        #region Constants 
        private const string CellIdentifier = "ProdCell";
        #endregion

        #region Private Variables
        private ProductTableDataSource DataSource;
        #endregion

        #region Constructors
        public ProductTableDelegate (ProductTableDataSource datasource)
        {
            this.DataSource = datasource;
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)tableView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Product":
                view.StringValue = DataSource.Products [(int)row].Title;
                break;
            case "Details":
                view.StringValue = DataSource.Products [(int)row].Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

Quando si crea un'istanza del `ProductTableDelegate`, viene anche passato in un'istanza del `ProductTableDataSource` che fornisce i dati per la tabella. Il `GetViewForItem` metodo è responsabile della restituzione di una visualizzazione (dati) per visualizzare la cella per una colonna e riga. Se possibile, una vista esistente verrà riutilizzata per la cella di visualizzazione, se non è necessario creare una nuova visualizzazione.

Per popolare la tabella, è possibile modificare il `ViewController.cs` file e apportare le `AwakeFromNib` metodo aspetto simile al seguente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create the Product Table Data Source and populate it
    var DataSource = new ProductTableDataSource ();
    DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));

    // Populate the Product Table
    ProductTable.DataSource = DataSource;
    ProductTable.Delegate = new ProductTableDelegate (DataSource);
}
```

Se si esegue l'applicazione, verrà visualizzato quanto segue:

[![](table-view-images/populate02.png "Esecuzione di un'app di esempio")](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>L'ordinamento per colonna

È possibile consentire all'utente di ordinare i dati nella tabella facendo clic su un'intestazione di colonna. In primo luogo, fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo in Interface Builder. Selezionare il `Product` colonna, immettere `Title` per il **chiave di ordinamento**, `compare:` per i **selettore** e selezionare `Ascending` per il **ordine**:

[![](table-view-images/sort01.png "Impostazione della chiave di ordinamento")](table-view-images/sort01.png#lightbox)

Selezionare il `Details` colonna, immettere `Description` per il **chiave di ordinamento**, `compare:` per i **selettore** e selezionare `Ascending` per il **ordine**:

[![](table-view-images/sort02.png "Impostazione della chiave di ordinamento")](table-view-images/sort02.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

A questo punto è possibile modificare il `ProductTableDataSource.cs` file e aggiungere i metodi seguenti:

```csharp
public void Sort(string key, bool ascending) {

    // Take action based on key
    switch (key) {
    case "Title":
        if (ascending) {
            Products.Sort ((x, y) => x.Title.CompareTo (y.Title));
        } else {
            Products.Sort ((x, y) => -1 * x.Title.CompareTo (y.Title));
        }
        break;
    case "Description":
        if (ascending) {
            Products.Sort ((x, y) => x.Description.CompareTo (y.Description));
        } else {
            Products.Sort ((x, y) => -1 * x.Description.CompareTo (y.Description));
        }
        break;
    }

}

public override void SortDescriptorsChanged (NSTableView tableView, NSSortDescriptor[] oldDescriptors)
{
    // Sort the data
    if (oldDescriptors.Length > 0) {
        // Update sort
        Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
    } else {
        // Grab current descriptors and update sort
        NSSortDescriptor[] tbSort = tableView.SortDescriptors; 
        Sort (tbSort[0].Key, tbSort[0].Ascending); 
    }
            
    // Refresh table
    tableView.ReloadData ();
}
```

Il `Sort` metodo consentono di ordinare i dati nell'origine dati basata su un determinato `Product` campo della classe in ordine crescente o decrescente. Sottoposto a override `SortDescriptorsChanged` metodo verrà chiamato ogni volta che l'uso fa clic su un'intestazione di colonna. Verrà passato il **chiave** valore su cui è impostati in Interface Builder e l'ordinamento per colonna.

Se si esegue l'applicazione e fare clic su nelle intestazioni di colonna, le righe verranno ordinate in base alla colonna:

[![](table-view-images/sort03.png "Eseguire un'app di esempio")](table-view-images/sort03.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Selezione riga

Se si vuole consentire all'utente di selezionare una singola riga, fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo in Interface Builder. Selezionare la vista tabella il **gerarchia delle interfacce** e deselezionare il **più** casella di controllo il **attributo Inspector**:

[![](table-view-images/select01.png "Attribute Inspector")](table-view-images/select01.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.


Modificare quindi il `ProductTableDelegate.cs` file e aggiungere il metodo seguente:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
    return true;
}
```

Ciò consentirà all'utente di selezionare ogni singola riga nella visualizzazione della tabella. Restituire `false` per il `ShouldSelectRow` per ogni riga che non si desidera l'utente sia in grado di selezionare o `false` per ogni riga, se non si desidera l'utente sia in grado di selezionare tutte le righe.

Visualizzazione della tabella (`NSTableView`) contiene i metodi seguenti per l'uso di selezione di riga:

- `DeselectRow(nint)` -Deseleziona determinata riga nella tabella.
- `SelectRow(nint,bool)` -Consente di selezionare la riga specificata. Passare `false` per il secondo parametro selezionare solo una riga alla volta.
- `SelectedRow` -Restituisce la riga corrente nella tabella selezionata.
- `IsRowSelected(nint)` : Restituisce `true` se è selezionata nella riga specificata.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Selezione di più righe

Se si vuole consentire all'utente di selezionare un più righe, fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo in Interface Builder. Selezionare la vista tabella il **gerarchia delle interfacce** e controllare il **più** casella di controllo il **attributo Inspector**:

[![](table-view-images/select02.png "Attribute Inspector")](table-view-images/select02.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.


Modificare quindi il `ProductTableDelegate.cs` file e aggiungere il metodo seguente:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
    return true;
}
```

Ciò consentirà all'utente di selezionare ogni singola riga nella visualizzazione della tabella. Restituire `false` per il `ShouldSelectRow` per ogni riga che non si desidera l'utente sia in grado di selezionare o `false` per ogni riga, se non si desidera l'utente sia in grado di selezionare tutte le righe.

Visualizzazione della tabella (`NSTableView`) contiene i metodi seguenti per l'uso di selezione di riga:

- `DeselectAll(NSObject)` -Consente di deselezionare tutte le righe nella tabella. Usare `this` per il primo parametro inviare l'oggetto che esegue la selezione. 
- `DeselectRow(nint)` -Deseleziona determinata riga nella tabella.
- `SelectAll(NSobject)` -Consente di selezionare tutte le righe nella tabella. Usare `this` per il primo parametro inviare l'oggetto che esegue la selezione.
- `SelectRow(nint,bool)` -Consente di selezionare la riga specificata. Passare `false` deselezione della selezione per il secondo parametro e si seleziona una sola riga, passare `true` per estendere la selezione e includere questa riga.
- `SelectRows(NSIndexSet,bool)` -Consente di selezionare il set specificato di righe. Passare `false` per il secondo parametro deselezionarla e selezionare solo un queste righe, passano `true` per estendere la selezione e includere queste righe.
- `SelectedRow` -Restituisce la riga corrente nella tabella selezionata.
- `SelectedRows` : Restituisce un `NSIndexSet` contenente gli indici delle righe selezionate.
- `SelectedRowCount` : Restituisce il numero di righe selezionate.
- `IsRowSelected(nint)` : Restituisce `true` se è selezionata nella riga specificata.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Tipo per selezionare righe

Se si desidera consentire all'utente di digitare un carattere con la vista della tabella selezionata e selezionare la prima riga che contiene tale carattere, fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo in Interface Builder. Selezionare la vista tabella il **gerarchia delle interfacce** e controllare il **tipo selezionare** casella di controllo il **attributo Inspector**:

[![](table-view-images/type01.png "Impostare il tipo di selezione")](table-view-images/type01.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

A questo punto è possibile modificare il `ProductTableDelegate.cs` file e aggiungere il metodo seguente:

```csharp
public override nint GetNextTypeSelectMatch (NSTableView tableView, nint startRow, nint endRow, string searchString)
{
    nint row = 0;
    foreach(Product product in DataSource.Products) {
        if (product.Title.Contains(searchString)) return row;

        // Increment row counter
        ++row;
    }

    // If not found select the first row
    return 0;
}
```

Il `GetNextTypeSelectMatch` metodo accetta il dato `searchString` e restituisce la riga del primo `Product` che presenta tale stringa nel relativo `Title`.

Se si esegue l'applicazione e un carattere di tipo, viene selezionata una riga:

[![](table-view-images/type02.png "Esecuzione di un'app di esempio")](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Riordinamento di colonne

Se si vuole consentire all'utente di trascinare riordinare le colonne nella visualizzazione della tabella, fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo in Interface Builder. Selezionare la vista tabella il **gerarchia delle interfacce** e controllare il **riordinamento** casella di controllo il **attributo Inspector**:

[![](table-view-images/reorder01.png "Attribute Inspector")](table-view-images/reorder01.png#lightbox)

Se si assegna un valore per il **salvataggio automatico** proprietà e selezionare il **informazioni sulla colonna** campo, eventuali modifiche apportate al layout della tabella verranno salvate automaticamente per noi e ripristino la volta successiva che l'applicazione viene eseguito.

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

A questo punto è possibile modificare il `ProductTableDelegate.cs` file e aggiungere il metodo seguente:

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

Il `ShouldReorder` metodo deve restituire `true` per tutte le colonne che desidera consentire a essere riordinati in trascinare il `newColumnIndex`, altrimenti restituire `false`;

Se si esegue l'applicazione, è possibile trascinare le intestazioni di colonna per riordinare le colonne:

[![](table-view-images/reorder02.png "Un esempio di riordinate colonne")](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>La modifica delle celle

Se si vuole consentire all'utente di modificare i valori per una cella specificata, modificare il `ProductTableDelegate.cs` file e modificare il `GetViewForItem` metodo come indicato di seguito:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{
    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTextField view = (NSTextField)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTextField ();
        view.Identifier = tableColumn.Title;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = true;

        view.EditingEnded += (sender, e) => {
                    
            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.Tag].Title = view.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.Tag].Description = view.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

Ora se si esegue l'applicazione, l'utente può modificare le celle nella visualizzazione della tabella:

[![](table-view-images/editing01.png "Un esempio di modifica di una cella")](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views" />

## <a name="using-images-in-table-views"></a>Usare le immagini nelle visualizzazioni di tabella

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

Per altre informazioni, vedere la [usando immagini con le visualizzazioni di tabelle](~/mac/app-fundamentals/image.md) sezione del nostro [utilizzo di immagini](~/mac/app-fundamentals/image.md) documentazione.

<a name="Adding-a-Delete-Button-to-a-Row" />

## <a name="adding-a-delete-button-to-a-row"></a>Aggiunta di un pulsante di eliminazione a una riga

In base ai requisiti dell'app, potrebbero esserci casi in cui è necessario fornire un pulsante di azione per ogni riga nella tabella. Come esempio di ciò, è possibile espandere l'esempio di visualizzazione tabella creata in precedenza per includere un **eliminare** pulsante su ogni riga.

In primo luogo, modificare il `Main.storyboard` in Interface Builder di Xcode, selezionare la visualizzazione di tabella e aumentare il numero di colonne da tre (3). Successivamente, modificare il **Title** della nuova colonna a `Action`:

[![](table-view-images/delete01.png "Modifica il nome della colonna")](table-view-images/delete01.png#lightbox)

Salvare le modifiche apportate allo Storyboard e tornare a Visual Studio per Mac sincronizzare le modifiche.

Modificare quindi il `ViewController.cs` file e aggiungere il metodo pubblico seguente:

```csharp
public void ReloadTable ()
{
    ProductTable.ReloadData ();
}
```

Nello stesso file, modificare la creazione del nuovo delegato visualizzazione tabella all'interno di `ViewDidLoad` metodo come indicato di seguito:

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

A questo punto, modificare il `ProductTableDelegate.cs` file da includere una connessione privata per il Controller di visualizzazione e per portare il controller come parametro quando si crea una nuova istanza del delegato:

```csharp
#region Private Variables
private ProductTableDataSource DataSource;
private ViewController Controller;
#endregion

#region Constructors
public ProductTableDelegate (ViewController controller, ProductTableDataSource datasource)
{
    this.Controller = controller;
    this.DataSource = datasource;
}
#endregion
```

Successivamente, aggiungere il nuovo metodo privato seguente alla classe:

```csharp
private void ConfigureTextField (NSTableCellView view, nint row)
{
    // Add to view
    view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
    view.AddSubview (view.TextField);

    // Configure
    view.TextField.BackgroundColor = NSColor.Clear;
    view.TextField.Bordered = false;
    view.TextField.Selectable = false;
    view.TextField.Editable = true;

    // Wireup events
    view.TextField.EditingEnded += (sender, e) => {

        // Take action based on type
        switch (view.Identifier) {
        case "Product":
            DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
            break;
        case "Details":
            DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
            break;
        }
    };

    // Tag view
    view.TextField.Tag = row;
}
```

Tutte le configurazioni di visualizzazione di testo che sono state eseguite in precedenza in questa operazione richiede il `GetViewForItem` (metodo) e li inserisce in un'unica posizione chiamabile (poiché l'ultima colonna della tabella non include una visualizzazione di testo, ma un pulsante).

Infine, modificare il `GetViewForItem` (metodo) e renderlo simile al seguente:

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();

        // Configure the view
        view.Identifier = tableColumn.Title;

        // Take action based on title
        switch (tableColumn.Title) {
        case "Product":
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
            ConfigureTextField (view, row);
            break;
        case "Details":
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
            ConfigureTextField (view, row);
            break;
        case "Action":
            // Create new button
            var button = new NSButton (new CGRect (0, 0, 81, 16));
            button.SetButtonType (NSButtonType.MomentaryPushIn);
            button.Title = "Delete";
            button.Tag = row;

            // Wireup events
            button.Activated += (sender, e) => {
                // Get button and product
                var btw = sender as NSButton;
                var product = DataSource.Products [(int)btn.Tag];

                // Configure alert
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Informational,
                    InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
                    MessageText = $"Delete {product.Title}?",
                };
                alert.AddButton ("Cancel");
                alert.AddButton ("Delete");
                alert.BeginSheetForResponse (Controller.View.Window, (result) => {
                    // Should we delete the requested row?
                    if (result == 1001) {
                        // Remove the given row from the dataset
                        DataSource.Products.RemoveAt((int)btn.Tag);
                        Controller.ReloadTable ();
                    }
                });
            };

            // Add to view
            view.AddSubview (button);
            break;
        }

    }

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tag.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        view.TextField.Tag = row;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        view.TextField.Tag = row;
        break;
    case "Action":
        foreach (NSView subview in view.Subviews) {
            var btw = subview as NSButton;
            if (btw != null) {
                btn.Tag = row;
            }
        }
        break;
    }

    return view;
}
```

Verrà ora esaminato diverse sezioni di questo codice in modo più dettagliato. Innanzitutto, se un nuovo `NSTableViewCell` è viene creata l'azione viene acquisita in base al nome della colonna. Per le prime due colonne (**prodotto** e **dettagli**), il nuovo `ConfigureTextField` viene chiamato il metodo.

Per il **azione** colonna, un nuovo `NSButton` viene creato e aggiunto alla cella come una visualizzazione secondaria:

```csharp
// Create new button
var button = new NSButton (new CGRect (0, 0, 81, 16));
button.SetButtonType (NSButtonType.MomentaryPushIn);
button.Title = "Delete";
button.Tag = row;
...

// Add to view
view.AddSubview (button);
```

Il pulsante `Tag` proprietà viene usata per contenere il numero della riga in cui è in fase di elaborazione. Questo numero verrà usato in un secondo momento quando l'utente richiede una riga da eliminare del pulsante `Activated` evento:

```csharp
// Wireup events
button.Activated += (sender, e) => {
    // Get button and product
    var btw = sender as NSButton;
    var product = DataSource.Products [(int)btn.Tag];

    // Configure alert
    var alert = new NSAlert () {
        AlertStyle = NSAlertStyle.Informational,
        InformativeText = $"Are you sure you want to delete {product.Title}? This operation cannot be undone.",
        MessageText = $"Delete {product.Title}?",
    };
    alert.AddButton ("Cancel");
    alert.AddButton ("Delete");
    alert.BeginSheetForResponse (Controller.View.Window, (result) => {
        // Should we delete the requested row?
        if (result == 1001) {
            // Remove the given row from the dataset
            DataSource.Products.RemoveAt((int)btn.Tag);
            Controller.ReloadTable ();
        }
    });
};
```

All'inizio del gestore dell'evento, otteniamo il pulsante e il prodotto che si trova su una riga della tabella specificata. Un avviso viene quindi presentato all'utente conferma l'eliminazione della riga. Se l'utente sceglie di eliminare la riga, la riga specificata verrà rimossa dall'origine dati e verrà ricaricata nella tabella:

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

Infine, se si riutilizza la cella di visualizzazione tabella non vengono create nuove, il codice seguente verrà configurato in base alla colonna in fase di elaborazione:

```csharp
// Setup view based on the column selected
switch (tableColumn.Title) {
case "Product":
    view.ImageView.Image = NSImage.ImageNamed ("tag.png");
    view.TextField.StringValue = DataSource.Products [(int)row].Title;
    view.TextField.Tag = row;
    break;
case "Details":
    view.TextField.StringValue = DataSource.Products [(int)row].Description;
    view.TextField.Tag = row;
    break;
case "Action":
    foreach (NSView subview in view.Subviews) {
        var btw = subview as NSButton;
        if (btw != null) {
            btn.Tag = row;
        }
    }
    break;
}

```

Per il **azione** colonna, vengono analizzate tutte le visualizzazioni secondarie finché il `NSButton` viene trovato, è `Tag` proprietà viene aggiornata per puntare alla riga corrente.

Con queste modifiche posto, quando l'app viene eseguita ogni riga conterrà un **eliminare** pulsante:

[![](table-view-images/delete02.png "Visualizzazione della tabella con i pulsanti di eliminazione")](table-view-images/delete02.png#lightbox)

Quando l'utente fa clic su un **eliminare** pulsante, verrà visualizzato un avviso in cui viene chiesto loro di eliminare la riga specificata:

[![](table-view-images/delete03.png "Un avviso di eliminazione riga")](table-view-images/delete03.png#lightbox)

Se l'utente sceglie Elimina, la riga verrà rimossa e verrà ridisegnata nella tabella:

[![](table-view-images/delete04.png "La tabella dopo la riga viene eliminata")](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views" />

## <a name="data-binding-table-views"></a>Visualizzazioni di tabelle di Data Binding

Utilizzando le tecniche di codifica di chiave-valore e un Data Binding nell'applicazione xamarin. Mac, è possibile ridurre la quantità di codice che è necessario scrivere e mantenere per popolare e lavorare con gli elementi dell'interfaccia utente. È anche il vantaggio di un'ulteriore separazione dei dati di backup (_modello di dati_) dal front end interfaccia utente (_Model-View-Controller_), sfociando in più facilmente gestibile, più flessibile dell'applicazione progettazione.

Dati XML di codifica (i, chiave-valore KVM) è un meccanismo per l'accesso a proprietà di un oggetto indirettamente, tramite chiavi (stringhe di formattazione speciale) per identificare le proprietà anziché accedervi tramite le variabili di istanza o metodi di accesso (`get/set`). Grazie all'implementazione chiave-valore di codifica compatibili con funzioni di accesso nell'applicazione xamarin. Mac, è possibile accedere ad altre funzionalità macOS come chiave-valore osservando (KVO), Data Binding, Core Data, le associazioni di Cocoa e scriptability.

Per altre informazioni, vedere la [associazione di dati di visualizzazione tabella](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding) sezione del nostro [Data Binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md) documentazione.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di utilizzo delle visualizzazioni tabella in un'applicazione xamarin. Mac. Abbiamo visto i diversi tipi e Usa le visualizzazioni di tabelle, come creare e gestire le visualizzazioni di tabelle in Interface Builder di Xcode e come lavorare con le visualizzazioni di tabelle nel codice c#.

## <a name="related-links"></a>Collegamenti correlati

- [MacTables (esempio)](https://developer.xamarin.com/samples/mac/MacTables/)
- [MacImages (sample)](https://developer.xamarin.com/samples/mac/MacImages/) (MacImages - Esempio)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Outline Views](~/mac/user-interface/outline-view.md) (Visualizzazioni struttura)
- [Source Lists](~/mac/user-interface/source-list.md) (Elenchi di risorse)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [NSTableViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [NSTableViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)
