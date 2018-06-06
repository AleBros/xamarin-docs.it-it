---
title: Viste delle tabelle in Xamarin.Mac
description: In questo articolo viene descritto l'utilizzo con le visualizzazioni di tabella in un'applicazione Xamarin.Mac. Vengono descritte la creazione di viste di tabella in Xcode e il generatore di interfaccia e l'interazione con essi nel codice.
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: da26810869f23b8861ffb4409248c56bff12a521
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793230"
---
# <a name="table-views-in-xamarinmac"></a>Viste delle tabelle in Xamarin.Mac

_In questo articolo viene descritto l'utilizzo con le visualizzazioni di tabella in un'applicazione Xamarin.Mac. Vengono descritte la creazione di viste di tabella in Xcode e il generatore di interfaccia e l'interazione con essi nel codice._

Quando si utilizza c# e .NET in un'applicazione Xamarin.Mac, è possibile accedere alla stessa tabella viste che uno sviluppatore che lavora *Objective-C* e *Xcode* does. Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile utilizzare del Xcode _generatore interfaccia_ per creare e gestire le visualizzazioni di tabella (o, facoltativamente, crearli direttamente nel codice c#).

Visualizzazione di una tabella sono riportati i dati in un formato tabulare che contiene uno o più colonne di informazioni in più righe. In base al tipo di vista di tabella viene creato, l'utente può ordinare per colonna, riorganizzare le colonne, aggiungere colonne, rimuovere le colonne o modificare i dati contenuti all'interno della tabella.

[![](table-view-images/intro01.png "Una tabella di esempio")](table-view-images/intro01.png#lightbox)

In questo articolo verranno descritte le nozioni di base dell'utilizzo di viste delle tabelle in un'applicazione Xamarin.Mac. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

Si consiglia di esaminare il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` comandi utilizzato per le classi c# in transito-fino a oggetti Objective-C e gli elementi dell'interfaccia utente.

<a name="Introduction_to_Table_Views" />

## <a name="introduction-to-table-views"></a>Introduzione alle visualizzazioni di tabella

Visualizzazione di una tabella sono riportati i dati in un formato tabulare che contiene uno o più colonne di informazioni in più righe. Viste delle tabelle vengono visualizzate all'interno di viste di scorrimento (`NSScrollView`) e a partire da macOS 10.7, è possibile utilizzare qualsiasi `NSView` anziché celle (`NSCell`) per visualizzare le righe e colonne. Ciò premesso, è comunque possibile utilizzare `NSCell` , tuttavia, si sarà in genere sottoclasse `NSTableCellView` e creare colonne e righe personalizzate.

Visualizzazione di una tabella non archivia i propri dati, ma basa su un'origine dati (`NSTableViewDataSource`) per fornire le righe e colonne necessarie, in una base alla necessità.

Comportamento della visualizzazione di una tabella può essere personalizzato specificando una sottoclasse del delegato di visualizzazione di tabella (`NSTableViewDelegate`) per supportare la gestione di colonna di tabella, digitare per selezionare funzionalità, la selezione della riga e la modifica, rilevamento personalizzato e visualizzazioni personalizzate per le singole colonne e righe.

Durante la creazione di viste delle tabelle, Apple suggerisce le operazioni seguenti:

* Consente all'utente di ordinare la tabella facendo clic sulle intestazioni di colonna.
* Creare le intestazioni di colonna sono sostantivi o breve sintagmi nominali che descrivono i dati viene visualizzati nella colonna.

Per ulteriori informazioni, vedere il [viste contenuto](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) sezione di Apple [OS X umano linee guida sull'interfaccia](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/).

<a name="Creating-and-Maintaining-Table-Views-in-Xcode" />

## <a name="creating-and-maintaining-table-views-in-xcode"></a>Creazione e gestione di viste delle tabelle in Xcode

Quando si crea una nuova applicazione Xamarin.Mac Cocoa, per impostazione predefinita è ottenere una finestra vuota, standard. Questo windows è definito in un `.storyboard` file automaticamente incluso nel progetto. Per modificare la progettazione di windows, nel **Esplora**, fare doppio clic il `Main.storyboard` file:

[![](table-view-images/edit01.png "Selezione storyboard principale")](table-view-images/edit01.png#lightbox)

La progettazione della finestra verrà aperta in Generatore del Xcode di interfaccia:

[![](table-view-images/edit02.png "Modifica l'interfaccia utente in Xcode")](table-view-images/edit02.png#lightbox)

Tipo `table` nel **libreria Ispettore** casella di ricerca per renderne più semplice trovare i controlli di visualizzazione di tabella:

[![](table-view-images/edit03.png "Selezione di visualizzazione di una tabella dalla libreria")](table-view-images/edit03.png#lightbox)

Trascinare il Controller di visualizzazione di una visualizzazione tabella il **Editor dell'interfaccia**, renderlo riempire l'area del contenuto del Controller di visualizzazione e impostarlo su dove compatta e aumenta con la finestra nel **Editor vincoli di**:

[![](table-view-images/edit04.png "La modifica di vincoli")](table-view-images/edit04.png#lightbox)

Selezionare la visualizzazione tabella nel **interfaccia gerarchia** e le proprietà seguenti sono disponibili nel **controllo attributo**:

[![](table-view-images/edit05.png "Il controllo di attributo")](table-view-images/edit05.png#lightbox)

- **Modalità di contenuto** -consente di utilizzare entrambe le visualizzazioni (`NSView`) o le celle (`NSCell`) per visualizzare i dati in righe e colonne. A partire da macOS 10.7, è consigliabile utilizzare le visualizzazioni.
- **Scorre le righe del gruppo** - se `true`, la visualizzazione della tabella verrà disegnato celle raggruppate come se si sono a virgola mobile.
- **Colonne** -definisce il numero di colonne visualizzate.
- **Intestazioni** - se `true`, le colonne hanno intestazioni.
- **Riordinamento** - se `true`, l'utente sarà in grado di trascinare riordinare le colonne nella tabella.
- **Ridimensionamento** - se `true`, l'utente sarà in grado di trascinare le intestazioni di colonna per ridimensionare le colonne.
- **Ridimensionamento di colonne** -controlla la tabella automaticamente come ridimensionare le colonne.
- **Evidenziare** -controlla il tipo di evidenziazione della tabella quando viene selezionata una cella.
- **Le righe alternate** - se `true`di altre righe avrà un colore di sfondo diversi.
- **Griglia orizzontale** -seleziona il tipo di bordo disegnato tra le celle orizzontalmente.
- **Griglia verticale** -seleziona il tipo di bordo disegnata in verticale tra le celle.
- **Colore della griglia** -imposta il colore del bordo della cella.
- **Sfondo** -imposta il colore di sfondo della cella.
- **Selezione** -consente di controllare la modalità con cui l'utente può selezionare le celle della tabella come:
    - **Più** - se `true`, l'utente può selezionare più righe e colonne.
    - **Colonna** - se `true`, l'utente può selezionare le colonne.
    - **Digitare Select** - se `true`, l'utente può digitare un carattere per selezionare una riga.
    - **Vuoto** - se `true`, l'utente non è necessario selezionare una riga o colonna, la tabella consente di selezione non è affatto.
- **Salvataggio automatico** -Salva il nome che il formato di tabelle è automaticamente in.
- **Informazioni sulla colonna** - se `true`, l'ordine e la larghezza delle colonne verranno salvate automaticamente.
- **Le interruzioni di riga** - selezionare la cella gestisce le interruzioni di riga.
- **Tronca l'ultima riga visibile** - se `true`, verrà troncata, è possibile di dati non rientrano nei limiti della cella.

> [!IMPORTANT]
> A meno che non si esegue la manutenzione di un'applicazione legacy Xamarin.Mac, `NSView` viste delle tabelle di base deve essere utilizzate su `NSCell` viste delle tabelle di base. `NSCell` viene considerato legacy e potrebbe non essere supportato in futuro.

Selezionare una colonna della tabella nel **interfaccia gerarchia** e le proprietà seguenti sono disponibili nel **controllo attributo**:

[![](table-view-images/edit06.png "Il controllo di attributo")](table-view-images/edit06.png#lightbox)

- **Titolo** -imposta il titolo della colonna.
- **Allineamento** -impostare l'allineamento del testo all'interno delle celle.
- **Tipo di carattere titolo** -seleziona il tipo di carattere per il testo di intestazione della cella.
- **Chiave di ordinamento** -rappresenta la chiave utilizzata per ordinare i dati nella colonna. Lasciare vuoto se l'utente non è possibile ordinare questa colonna.
- **Selettore** -è il **azione** utilizzato per eseguire l'ordinamento. Lasciare vuoto se l'utente non è possibile ordinare questa colonna.
- **Ordine** -è l'ordinamento per i dati di colonne.
- **Ridimensionamento** -seleziona il tipo di ridimensionamento per la colonna.
- **Modificabile** - se `true`, l'utente può modificare le celle in una tabella di base di cella.
- **Nascosto** - se `true`, la colonna è nascosta.

È anche possibile ridimensionare la colonna trascinando il quadratino (centrati verticalmente sul lato destro della colonna) a sinistra o a destra.

Di seguito selezionare ogni colonna nella visualizzazione delle tabelle e assegnare la prima colonna un **titolo** di `Product` e la seconda `Details`.

Selezionare una visualizzazione di cella di tabella (`NSTableViewCell`) nel **interfaccia gerarchia** e le proprietà seguenti sono disponibili nel **controllo attributo**:

[![](table-view-images/edit07.png "Il controllo di attributo")](table-view-images/edit07.png#lightbox)

Si tratta di tutte le proprietà di una vista standard. È inoltre l'opzione di ridimensionamento delle righe per questa colonna qui.

Selezionare una cella di visualizzazione della tabella (per impostazione predefinita, si tratta di un `NSTextField`) nei **interfaccia gerarchia** e le proprietà seguenti sono disponibili nel **attributo controllo**:

[![](table-view-images/edit08.png "Il controllo di attributo")](table-view-images/edit08.png#lightbox)

È necessario che tutte le proprietà di un campo di testo standard per impostare qui. Per impostazione predefinita, un campo di testo standard viene utilizzato per visualizzare i dati per una cella in una colonna.

Selezionare una visualizzazione di cella di tabella (`NSTableFieldCell`) nel **interfaccia gerarchia** e le proprietà seguenti sono disponibili nel **controllo attributo**:

[![](table-view-images/edit09.png "Il controllo di attributo")](table-view-images/edit09.png#lightbox)

Le impostazioni di più importanti sono:

- **Layout** - selezionare la modalità di celle in questa colonna vengono disposti.
- **Utilizza la modalità a riga singola** - se `true`, la cella è limitata a una singola riga.
- **Larghezza del Layout di Runtime prima** - se `true`, la cella preferiranno alla larghezza impostata per tale (automaticamente o manualmente) quando viene visualizzato alla prima esecuzione dell'applicazione.
- **Azione** -controlla se la modifica **azione** viene inviato per la cella.
- **Comportamento** -definisce se una cella è modificabile o selezionabile.
- **Rich Text** - se `true`, la cella può visualizzare il testo formattato e stile.
- **Annulla** - se `true`, la cella si assume la responsabilità per è il comportamento di annullamento.

Selezionare la visualizzazione di cella di tabella (`NSTableFieldCell`) nella parte inferiore di una colonna di tabella nel **interfaccia gerarchia**:

[![](table-view-images/edit10.png "Selezione di visualizzazione della cella di tabella")](table-view-images/edit10.png#lightbox)

Ciò consente di modificare la visualizzazione di cella di tabella usata come base _modello_ per tutte le celle create per la colonna specificata.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Aggiunta di azioni e prese

Solo come qualsiasi altro controllo dell'interfaccia utente Cocoa, è necessario esporre la visualizzazione di tabella e si tratta di colonne e le celle all'utilizzo di codice c# **azioni** e **prese** (basati sulle funzionalità necessarie).

Il processo è lo stesso per qualsiasi elemento di visualizzazione tabella che si desidera esporre:

1. Passare al **Assistente Editor** e assicurarsi che il `ViewController.h` è selezionato il file: 

    [![](table-view-images/edit11.png "L'Editor Assistente")](table-view-images/edit11.png#lightbox)
2. Selezionare la visualizzazione della tabella dal **interfaccia gerarchia**, CTRL + clic e trascinare il `ViewController.h` file.
3. Creare un **presa** per la visualizzazione tabella denominata `ProductTable`: 

    [![](table-view-images/edit13.png "Configurazione di una presa di corrente")](table-view-images/edit13.png#lightbox)
4. Creare **prese** per le colonne di tabelle anche chiamato `ProductColumn` e `DetailsColumn`: 

    [![](table-view-images/edit14.png "Configurazione di una presa di corrente")](table-view-images/edit14.png#lightbox)
5. Per salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Successivamente, si scriverà la visualizzazione di codice alcuni dati per la tabella quando l'applicazione viene eseguita.

<a name="Populating_the_Table_View" />

## <a name="populating-the-table-view"></a>Popolamento di visualizzazione della tabella

Con la visualizzazione tabella progettata in Generatore di interfaccia e vengono esposte tramite un **presa**, successivamente è necessario creare il codice c# per compilarlo.

Innanzitutto, creare un nuovo `Product` classe per contenere le informazioni per le singole righe. Nel **Esplora**, fare clic sul progetto e selezionare **Aggiungi** > **nuovo File...** Selezionare **generale** > **classe vuota**, immettere `Product` per il **nome** e fare clic su di **New** pulsante:

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

Successivamente, è necessario creare una sottoclasse di `NSTableDataSource` per fornire i dati per la tabella, come richiesto. Nel **Esplora**, fare clic sul progetto e selezionare **Aggiungi** > **nuovo File...** Selezionare **generale** > **classe vuota**, immettere `ProductTableDataSource` per il **nome** e fare clic su di **New** pulsante.

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

Questa classe dispone di spazio di archiviazione per gli elementi della visualizzazione per la tabella ed esegue l'override di `GetRowCount` per restituire il numero di righe nella tabella.

Infine, è necessario creare una sottoclasse di `NSTableDelegate` per fornire il comportamento per la tabella. Nel **Esplora**, fare clic sul progetto e selezionare **Aggiungi** > **nuovo File...** Selezionare **generale** > **classe vuota**, immettere `ProductTableDelegate` per il **nome** e fare clic su di **New** pulsante.

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

Quando si crea un'istanza di `ProductTableDelegate`, viene inoltre passato in un'istanza del `ProductTableDataSource` che fornisce i dati per la tabella. Il `GetViewForItem` metodo è responsabile della restituzione di una vista (dati) per visualizzare la cella per una colonna e riga. Se possibile, una vista esistente verrà riutilizzata per visualizzare la cella, se non è necessario creare una nuova vista.

Per popolare la tabella, modifica il `ViewController.cs` file e verificare il `AwakeFromNib` metodo aspetto simile al seguente:

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

Se si esegue l'applicazione, viene visualizzato il seguente:

[![](table-view-images/populate02.png "Eseguire un'app di esempio")](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Ordinamento per colonna

Di seguito consente all'utente di ordinare i dati nella tabella facendo clic su un'intestazione di colonna. In primo luogo, fare doppio clic su di `Main.storyboard` file per aprirlo e modificarlo in Generatore di interfaccia. Selezionare il `Product` colonna, immettere `Title` per il **chiave di ordinamento**, `compare:` per il **selettore** e selezionare `Ascending` per il **ordine**:

[![](table-view-images/sort01.png "Impostazione della chiave di ordinamento")](table-view-images/sort01.png#lightbox)

Selezionare il `Details` colonna, immettere `Description` per il **chiave di ordinamento**, `compare:` per il **selettore** e selezionare `Ascending` per il **ordine**:

[![](table-view-images/sort02.png "Impostazione della chiave di ordinamento")](table-view-images/sort02.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Ora modifica il `ProductTableDataSource.cs` file e aggiungere i metodi seguenti:

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

Il `Sort` metodo consente di ordinare i dati nell'origine dati in base a un determinato `Product` campo della classe in ordine crescente o decrescente. Sottoposto a override `SortDescriptorsChanged` metodo verrà chiamato ogni volta che l'utilizzo fa clic sull'intestazione di colonna. Verrà passato il **chiave** valore impostato nel generatore di interfaccia e l'ordinamento per colonna.

Se si esegue l'applicazione e fare clic su nelle intestazioni di colonna, le righe verranno ordinate in base alla colonna:

[![](table-view-images/sort03.png "Eseguire un'app di esempio")](table-view-images/sort03.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Selezione di riga

Se si desidera consentire all'utente di selezionare una singola riga, fare doppio clic su di `Main.storyboard` file per aprirlo e modificarlo in Generatore di interfaccia. Selezionare la visualizzazione tabella nel **interfaccia gerarchia** e deselezionare il **più** nella casella di controllo di **controllo attributo**:

[![](table-view-images/select01.png "Il controllo di attributo")](table-view-images/select01.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.


Successivamente, modificare il `ProductTableDelegate.cs` file e aggiungere il metodo seguente:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
    return true;
}
```

In questo modo sarà possibile selezionare ogni singola riga nella visualizzazione tabella. Restituire `false` per il `ShouldSelectRow` per ogni riga che non si desidera l'utente sia in grado di selezionare o `false` per ogni riga, se non si desidera l'utente sia in grado di selezionare tutte le righe.

Visualizzazione della tabella (`NSTableView`) contiene i metodi seguenti per l'utilizzo di selezione di riga:

- `DeselectRow(nint)` -Deseleziona determinata riga nella tabella.
- `SelectRow(nint,bool)` -Seleziona la riga specificata. Passare `false` per il secondo parametro selezionare solo una riga alla volta.
- `SelectedRow` -Restituisce la riga corrente nella tabella selezionata.
- `IsRowSelected(nint)` : Restituisce `true` se è selezionata la riga specificata.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Selezione di più righe

Se si desidera consentire all'utente di selezionare un più righe, fare doppio clic su di `Main.storyboard` file per aprirlo e modificarlo in Generatore di interfaccia. Selezionare la visualizzazione tabella nel **interfaccia gerarchia** e controllare il **più** nella casella di controllo di **attributo controllo**:

[![](table-view-images/select02.png "Il controllo di attributo")](table-view-images/select02.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.


Successivamente, modificare il `ProductTableDelegate.cs` file e aggiungere il metodo seguente:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
    return true;
}
```

In questo modo sarà possibile selezionare ogni singola riga nella visualizzazione tabella. Restituire `false` per il `ShouldSelectRow` per ogni riga che non si desidera l'utente sia in grado di selezionare o `false` per ogni riga, se non si desidera l'utente sia in grado di selezionare tutte le righe.

Visualizzazione della tabella (`NSTableView`) contiene i metodi seguenti per l'utilizzo di selezione di riga:

- `DeselectAll(NSObject)` -Consente di deselezionare tutte le righe nella tabella. Utilizzare `this` per il primo parametro inviare l'oggetto che esegue la selezione. 
- `DeselectRow(nint)` -Deseleziona determinata riga nella tabella.
- `SelectAll(NSobject)` -Seleziona tutte le righe nella tabella. Utilizzare `this` per il primo parametro inviare l'oggetto che esegue la selezione.
- `SelectRow(nint,bool)` -Seleziona la riga specificata. Passare `false` per il secondo parametro deselezionare e selezionare una sola riga, passare `true` per estendere la selezione e includere questa riga.
- `SelectRows(NSIndexSet,bool)` -Consente di selezionare il set specificato di righe. Passare `false` per il secondo parametro cancellare la selezione e selezionare solo un queste righe, passare `true` per estendere la selezione e includere queste righe.
- `SelectedRow` -Restituisce la riga corrente nella tabella selezionata.
- `SelectedRows` : Restituisce un `NSIndexSet` che contiene gli indici delle righe selezionate.
- `SelectedRowCount` : Restituisce il numero di righe selezionate.
- `IsRowSelected(nint)` : Restituisce `true` se è selezionata la riga specificata.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Tipo per selezionare una riga

Se si desidera consentire all'utente di digitare un carattere con la visualizzazione della tabella selezionata e selezionare la prima riga con tale carattere, fare doppio clic su di `Main.storyboard` file per aprirlo e modificarlo in Generatore di interfaccia. Selezionare la visualizzazione tabella nel **interfaccia gerarchia** e controllare il **tipo selezionare** nella casella di controllo di **attributo controllo**:

[![](table-view-images/type01.png "Impostare il tipo di selezione")](table-view-images/type01.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Ora modifica il `ProductTableDelegate.cs` file e aggiungere il metodo seguente:

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

Il `GetNextTypeSelectMatch` metodo accetta il determinato `searchString` e restituisce la riga del primo `Product` che contiene la stringa del `Title`.

Se si esegue l'applicazione e digitare un carattere, è selezionata una riga:

[![](table-view-images/type02.png "Eseguire un'app di esempio")](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Riordinamento di colonne

Se si desidera consentire all'utente di trascinare riordinare le colonne nella visualizzazione tabella, fare doppio clic su di `Main.storyboard` file per aprirlo e modificarlo in Generatore di interfaccia. Selezionare la visualizzazione tabella nel **interfaccia gerarchia** e controllare il **riordinamento** nella casella di controllo di **attributo controllo**:

[![](table-view-images/reorder01.png "Il controllo di attributo")](table-view-images/reorder01.png#lightbox)

Se si assegna un valore per il **salvataggio** proprietà e selezionare il **informazioni sulla colonna** campo, eventuali modifiche apportate al layout della tabella verranno salvate automaticamente a Microsoft e ripristino la volta successiva che l'applicazione viene eseguito.

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Ora modifica il `ProductTableDelegate.cs` file e aggiungere il metodo seguente:

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

Il `ShouldReorder` metodo dovrebbe restituire `true` per qualsiasi colonna che desidera consentire a essere trascinamento riordinati nel `newColumnIndex`, altrimenti restituire `false`;

Se si esegue l'applicazione, è possibile trascinare le intestazioni di colonna intorno riordinamento delle colonne:

[![](table-view-images/reorder02.png "Un esempio delle colonne riordinati")](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>La modifica delle celle

Se si desidera consentire all'utente di modificare i valori per una cella specificata, modificare il `ProductTableDelegate.cs` file e modificare il `GetViewForItem` metodo come indicato di seguito:

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

Ora se si esegue l'applicazione, l'utente può modificare le celle nella visualizzazione tabella:

[![](table-view-images/editing01.png "Un esempio di modifica di una cella")](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views" />

## <a name="using-images-in-table-views"></a>Utilizzo di immagini nelle visualizzazioni di tabella

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

Per ulteriori informazioni, vedere il [con immagini con viste delle tabelle](~/mac/app-fundamentals/image.md) sezione del nostro [utilizzo immagine](~/mac/app-fundamentals/image.md) documentazione.

<a name="Adding-a-Delete-Button-to-a-Row" />

## <a name="adding-a-delete-button-to-a-row"></a>Aggiunta di un pulsante Elimina a una riga

In base ai requisiti dell'app, è possibile che in cui è necessario fornire un pulsante di azione per ogni riga nella tabella. Ad esempio di questo, si espandere nell'esempio di tabella vista creato in precedenza per includere un **eliminare** pulsante in ogni riga.

In primo luogo, modificare il `Main.storyboard` in Generatore del Xcode di interfaccia, selezionare la visualizzazione della tabella e aumentare il numero di colonne da tre (3). Successivamente, modificare il **titolo** della nuova colonna `Action`:

[![](table-view-images/delete01.png "Modifica il nome della colonna")](table-view-images/delete01.png#lightbox)

Salvare le modifiche allo Storyboard e tornare a Visual Studio per Mac sincronizzare le modifiche.

Successivamente, modificare il `ViewController.cs` file e aggiungere il metodo pubblico seguente:

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

A questo punto, modificare il `ProductTableDelegate.cs` file da includere una connessione al Controller di visualizzazione privata e portare il controller come parametro quando si crea una nuova istanza del delegato:

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

Successivamente, aggiungere il seguente nuovo metodo privato alla classe:

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

Tutte le configurazioni di visualizzazione di testo che sono state eseguite in precedenza in questa operazione richiede il `GetViewForItem` (metodo) e li inserisce in una singola posizione callable (dall'ultima colonna della tabella non include una visualizzazione di testo, ma un pulsante).

Infine, modificare il `GetViewForItem` metodo e renderlo simile al seguente:

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

Diamo un'occhiata diverse sezioni di questo codice in modo più dettagliato. Prima, se un nuovo `NSTableViewCell` viene creato l'azione viene acquisito in base al nome della colonna. Per le prime due colonne (**prodotto** e **dettagli**), il nuovo `ConfigureTextField` metodo viene chiamato.

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

Il pulsante `Tag` proprietà viene utilizzata per contenere il numero della riga in cui è in corso di elaborazione. Questo numero verrà utilizzato in un secondo momento, quando l'utente richiede una riga da eliminare il pulsante `Activated` evento:

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

All'inizio del gestore eventi, si ottiene il pulsante e il prodotto che si trova su una riga della tabella specificata. Quindi l'utente conferma l'eliminazione di righe viene visualizzato un avviso. Se l'utente sceglie di eliminare la riga, la riga specificata verrà rimossa dall'origine dati e la tabella viene ricaricata più volte:

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

Infine, se si riutilizza la cella di visualizzazione della tabella non vengono create nuove, il codice seguente configura in base alla colonna in fase di elaborazione:

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

Per il **azione** colonna, tutte le visualizzazioni Sub viene eseguita l'analisi fino a quando il `NSButton` viene trovato, viene utilizzato `Tag` proprietà viene aggiornata per puntare alla riga corrente.

Con queste modifiche sul posto, quando l'app viene eseguita ogni riga conterrà un **eliminare** pulsante:

[![](table-view-images/delete02.png "Visualizzazione della tabella con i pulsanti di eliminazione")](table-view-images/delete02.png#lightbox)

Quando l'utente fa clic su un **eliminare** pulsante, verrà visualizzato un avviso che richiede di eliminare la riga specificata:

[![](table-view-images/delete03.png "Un avviso di eliminazione riga")](table-view-images/delete03.png#lightbox)

Se l'utente sceglie Elimina, la riga verrà rimossa e verrà ridisegnata nella tabella:

[![](table-view-images/delete04.png "La tabella dopo la riga viene eliminata")](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views" />

## <a name="data-binding-table-views"></a>Visualizzazioni di tabella di associazione dati

Utilizzando le tecniche di associazione di dati e la generazione di codice chiave-valore Xamarin.Mac nell'applicazione in uso, è possibile ridurre notevolmente la quantità di codice che è necessario scrivere e mantenere per popolare e lavorare con gli elementi dell'interfaccia utente. È inoltre il vantaggio di separazione ulteriormente i dati di backup (_modello di dati_) la parte anteriore terminare l'interfaccia utente (_Model-View-Controller_), i punti iniziali per più semplici da gestire, più flessibile dell'applicazione progettazione.

Dati XML di codifica (i, chiave-valore KVM) è un meccanismo per l'accesso a proprietà di un oggetto indirettamente, utilizzando le chiavi (stringhe di formattazione speciale) per identificare le proprietà anziché accedervi tramite le variabili di istanza o metodi di accesso (`get/set`). Implementando chiave-valore di codifica compatibile con funzioni di accesso nell'applicazione Xamarin.Mac, è possibile accedere ad altre funzionalità macOS come chiave-valore osservazione (KVO), Data Binding, i dati di base, associazioni Cocoa e scriptability.

Per ulteriori informazioni, vedere il [associazione di dati di visualizzazione tabella](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding) sezione del nostro [Data Binding e la generazione di codice chiave-valore](~/mac/app-fundamentals/databinding.md) documentazione.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato l'utilizzo delle visualizzazioni di tabella in un'applicazione Xamarin.Mac un'analisi approfondita. È stato illustrato i diversi tipi e sugli usi di viste delle tabelle, come creare e gestire viste delle tabelle in interfaccia generatore del Xcode e come utilizzare le visualizzazioni di tabella nel codice c#.

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
