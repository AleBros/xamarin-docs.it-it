---
title: Viste tabella in Xamarin.Mac
description: Questo articolo illustra l'uso delle visualizzazioni tabella in un'applicazione Xamarin.Mac. Viene descritta la creazione di visualizzazioni di tabella in Xcode e Interface Builder e l'interazione con questi elementi nel codice.
ms.prod: xamarin
ms.assetid: 3B55B858-4769-4331-966A-7F53B3B7C720
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 36bed05c1e60004125406c3ed2df66fcfe2be10b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008332"
---
# <a name="table-views-in-xamarinmac"></a>Viste tabella in Xamarin.Mac

_Questo articolo illustra l'uso delle visualizzazioni tabella in un'applicazione Xamarin.Mac. Viene descritta la creazione di visualizzazioni di tabella in Xcode e Interface Builder e l'interazione con questi elementi nel codice._

Quando si lavora C# con e .NET in un'applicazione Xamarin.Mac, è possibile accedere alle stesse visualizzazioni di tabella che uno sviluppatore utilizza in *Objective-C* e *Xcode* . Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile usare _Interface Builder_ di Xcode per creare e gestire le visualizzazioni di tabella (o, facoltativamente, crearle direttamente nel C# codice).

Una vista tabella consente di visualizzare i dati in formato tabulare contenenti una o più colonne di informazioni in più righe. In base al tipo di visualizzazione tabella da creare, l'utente può ordinare in base alla colonna, riorganizzare le colonne, aggiungere colonne, rimuovere colonne o modificare i dati contenuti nella tabella.

[![](table-view-images/intro01.png "An example table")](table-view-images/intro01.png#lightbox)

In questo articolo verranno illustrate le nozioni di base sull'uso delle visualizzazioni di tabella in un'applicazione Xamarin.Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in Questo articolo.

Si consiglia di esaminare la sezione [esporre C# classi/metodi in Objective-c](~/mac/internals/how-it-works.md) del documento [interno di Xamarin.Mac](~/mac/internals/how-it-works.md) , spiegando i comandi`Register`e`Export`usati per collegare le C# classi a Objective-c. oggetti ed elementi dell'interfaccia utente.

<a name="Introduction_to_Table_Views" />

## <a name="introduction-to-table-views"></a>Introduzione alle viste tabella

Una vista tabella consente di visualizzare i dati in formato tabulare contenenti una o più colonne di informazioni in più righe. Le visualizzazioni di tabella vengono visualizzate all'interno delle visualizzazioni a scorrimento (`NSScrollView`) e a partire da macOS 10,7, è possibile usare qualsiasi `NSView` anziché le celle (`NSCell`) per visualizzare le righe e le colonne. Ciò premesso, è comunque possibile usare `NSCell` tuttavia, in genere la sottoclasse `NSTableCellView` e creare righe e colonne personalizzate.

Una vista tabella non archivia i propri dati, ma si basa su un'origine dati (`NSTableViewDataSource`) per fornire le righe e le colonne necessarie, in base alle esigenze.

È possibile personalizzare il comportamento di una visualizzazione di tabella fornendo una sottoclasse del delegato della visualizzazione tabella (`NSTableViewDelegate`) per supportare la gestione delle colonne di tabella, digitare per selezionare la funzionalità, la selezione e la modifica delle righe, il rilevamento personalizzato e visualizzazioni personalizzate per singole colonne e righe.

Quando si creano viste tabella, Apple suggerisce quanto segue:

- Consente all'utente di ordinare la tabella facendo clic su un'intestazione di colonna.
- Creare intestazioni di colonna che sono sostantivi o frasi sostantivi brevi che descrivono i dati visualizzati in tale colonna.

Per altre informazioni, vedere la sezione [visualizzazioni contenuto](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1) delle [linee guida per l'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)di Apple.

<a name="Creating-and-Maintaining-Table-Views-in-Xcode" />

## <a name="creating-and-maintaining-table-views-in-xcode"></a>Creazione e gestione di viste tabella in Xcode

Quando si crea una nuova applicazione Xamarin.Mac Cocoa, per impostazione predefinita si ottiene una finestra vuota standard. Questa finestra è definita in un file di `.storyboard` incluso automaticamente nel progetto. Per modificare la progettazione di Windows, nella **Esplora soluzioni**fare doppio clic sul file `Main.storyboard`:

[![](table-view-images/edit01.png "Selecting the main storyboard")](table-view-images/edit01.png#lightbox)

Verrà aperta la progettazione della finestra nella Interface Builder di Xcode:

[![](table-view-images/edit02.png "Editing the UI in Xcode")](table-view-images/edit02.png#lightbox)

Digitare `table` nella casella di ricerca di **Inspector Library** per semplificare la ricerca dei controlli di visualizzazione tabella:

[![](table-view-images/edit03.png "Selecting a Table View from the Library")](table-view-images/edit03.png#lightbox)

Trascinare una visualizzazione tabella sul controller di visualizzazione nell' **Editor dell'interfaccia**, fare in modo che riempia l'area di contenuto del controller di visualizzazione e impostarla su dove compatta e si espande con la finestra nell' **editor di vincoli**:

[![](table-view-images/edit04.png "Editing constraints")](table-view-images/edit04.png#lightbox)

Selezionare la visualizzazione tabella nella **gerarchia dell'interfaccia** e le proprietà seguenti sono disponibili nel **controllo attributi**:

[![](table-view-images/edit05.png "The Attribute Inspector")](table-view-images/edit05.png#lightbox)

- **Modalità contenuto** : consente di utilizzare viste (`NSView`) o celle (`NSCell`) per visualizzare i dati nelle righe e nelle colonne. A partire da macOS 10,7, è consigliabile usare le visualizzazioni.
- **Floats righe di gruppo** : se `true`, la visualizzazione tabella trarrà le celle raggruppate come se fossero mobili.
- **Columns** : definisce il numero di colonne visualizzate.
- **Intestazioni** : se `true`, le colonne avranno le intestazioni.
- **Riordinamento** : se `true`, l'utente sarà in grado di trascinare riordinare le colonne nella tabella.
- **Ridimensionamento** : se `true`, l'utente sarà in grado di trascinare le intestazioni di colonna per ridimensionare le colonne.
- **Dimensionamento colonne** : consente di controllare la modalità di ridimensionamento automatico delle colonne della tabella.
- **Highlight** : controlla il tipo di evidenziazione usato dalla tabella quando si seleziona una cella.
- **Righe alternative** : se `true`, l'altra riga avrà un colore di sfondo diverso.
- **Griglia orizzontale** : seleziona il tipo di bordo tracciato tra le celle orizzontalmente.
- **Griglia verticale** : seleziona il tipo di bordo tracciato tra le celle verticalmente.
- **Colore griglia** : imposta il colore del bordo della cella.
- **Background** : imposta il colore di sfondo della cella.
- **Selezione** : consente di controllare il modo in cui l'utente può selezionare le celle nella tabella come:
  - **Multiple** : se `true`, l'utente può selezionare più righe e colonne.
  - **Column** : se `true`, l'utente può selezionare le colonne.
  - **Digitare Select** -if `true`, l'utente può digitare un carattere per selezionare una riga.
  - **Empty** : se `true`, l'utente non deve selezionare una riga o una colonna, la tabella non consente alcuna selezione.
- **Salvataggio** automatico: il nome in base al quale il formato delle tabelle viene salvato automaticamente.
- **Informazioni colonna** : se `true`, l'ordine e la larghezza delle colonne verranno salvate automaticamente.
- **Interruzioni di riga** : consente di selezionare la modalità di gestione delle interruzioni di riga.
- **Tronca l'ultima riga visibile** : se `true`, la cella verrà troncata nei dati non può rientrare nei limiti.

> [!IMPORTANT]
> A meno che non si stia gestendo un'applicazione Xamarin.Mac legacy, è consigliabile utilizzare `NSView` viste di tabella basate su `NSCell` viste di tabella basate su. `NSCell` è considerato Legacy e potrebbe non essere supportato in futuro.

Selezionare una colonna della tabella nella **gerarchia dell'interfaccia** e le proprietà seguenti sono disponibili nel **controllo attributi**:

[![](table-view-images/edit06.png "The Attribute Inspector")](table-view-images/edit06.png#lightbox)

- **Title** : imposta il titolo della colonna.
- **Allineamento** : impostare l'allineamento del testo all'interno delle celle.
- **Carattere titolo** : consente di selezionare il tipo di carattere per il testo dell'intestazione della cella.
- **Chiave di ordinamento** : chiave utilizzata per ordinare i dati nella colonna. Lasciare vuoto se l'utente non è in grado di ordinare questa colonna.
- **Selector** : **azione** utilizzata per eseguire l'ordinamento. Lasciare vuoto se l'utente non è in grado di ordinare questa colonna.
- **Order** : è il tipo di ordinamento per i dati delle colonne.
- **Ridimensionamento** : consente di selezionare il tipo di ridimensionamento per la colonna.
- **Modificabile** : se `true`, l'utente può modificare le celle in una tabella basata su celle.
- **Nascosto** : se `true`, la colonna è nascosta.

È anche possibile ridimensionare la colonna trascinando il relativo handle (centrato verticalmente sul lato destro della colonna) a sinistra o a destra.

Selezionare ogni colonna nella vista tabella e assegnare alla prima colonna un **titolo** di `Product` e il secondo `Details`.

Selezionare una visualizzazione della cella della tabella (`NSTableViewCell`) nella **gerarchia dell'interfaccia** e le proprietà seguenti sono disponibili in **controllo attributi**:

[![](table-view-images/edit07.png "The Attribute Inspector")](table-view-images/edit07.png#lightbox)

Si tratta di tutte le proprietà di una vista standard. È anche possibile ridimensionare le righe per questa colonna.

Selezionare una cella di visualizzazione tabella (per impostazione predefinita, si tratta di un `NSTextField`) nella **gerarchia dell'interfaccia** e le proprietà seguenti sono disponibili nel **controllo attributi**:

[![](table-view-images/edit08.png "The Attribute Inspector")](table-view-images/edit08.png#lightbox)

Sono disponibili tutte le proprietà di un campo di testo standard da impostare qui. Per impostazione predefinita, un campo di testo standard viene utilizzato per visualizzare i dati di una cella in una colonna.

Selezionare una visualizzazione della cella della tabella (`NSTableFieldCell`) nella **gerarchia dell'interfaccia** e le proprietà seguenti sono disponibili in **controllo attributi**:

[![](table-view-images/edit09.png "The Attribute Inspector")](table-view-images/edit09.png#lightbox)

Di seguito sono riportate le impostazioni più importanti:

- **Layout** : consente di selezionare il layout delle celle in questa colonna.
- **Usa la modalità a riga singola** : se `true`, la cella è limitata a una singola riga.
- **Prima larghezza del layout di runtime** : se `true`, la cella preferisce la larghezza impostata (manualmente o automaticamente) quando viene visualizzata la prima volta che l'applicazione viene eseguita.
- **Azione** : controlla quando viene inviata l' **azione** di modifica per la cella.
- **Behavior** : definisce se una cella è selezionabile o modificabile.
- **Rich Text** : se `true`, la cella può visualizzare testo formattato e in stile.
- **Annulla** : se `true`, la cella assume la responsabilità del comportamento di annullamento.

Selezionare la visualizzazione della cella della tabella (`NSTableFieldCell`) nella parte inferiore di una colonna della tabella nella **gerarchia dell'interfaccia**:

[![](table-view-images/edit10.png "Selecting the Table Cell View")](table-view-images/edit10.png#lightbox)

In questo modo è possibile modificare la visualizzazione della cella della tabella utilizzata come _modello_ di base per tutte le celle create per la colonna specificata.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Aggiunta di azioni e Outlet

Analogamente a qualsiasi altro controllo dell'interfaccia utente di Cocoa, è necessario esporre la visualizzazione tabella e le colonne e C# le celle al codice usando le **azioni** e gli **Outlet** (in base alle funzionalità necessarie).

Il processo è lo stesso per qualsiasi elemento di visualizzazione tabella che si desidera esporre:

1. Passare all' **editor di assistente** e verificare che sia selezionato il file di `ViewController.h`: 

    [![](table-view-images/edit11.png "The Assistant Editor")](table-view-images/edit11.png#lightbox)
2. Selezionare la visualizzazione tabella dalla **gerarchia dell'interfaccia**, fare clic su CTRL e trascinare il file `ViewController.h`.
3. Creare un **Outlet** per la visualizzazione tabella denominata `ProductTable`: 

    [![](table-view-images/edit13.png "Configuring an Outlet")](table-view-images/edit13.png#lightbox)
4. Creare **Outlet** per le colonne Tables, così come `ProductColumn` e `DetailsColumn`: 

    [![](table-view-images/edit14.png "Configuring an Outlet")](table-view-images/edit14.png#lightbox)
5. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Si scriverà quindi il codice per visualizzare alcuni dati della tabella quando viene eseguita l'applicazione.

<a name="Populating_the_Table_View" />

## <a name="populating-the-table-view"></a>Popolamento della visualizzazione tabella

Con la visualizzazione tabella progettata in Interface Builder ed esposta tramite un **Outlet**, successivamente è necessario creare il C# codice per popolarlo.

Prima di tutto, creare una nuova classe `Product` per conservare le informazioni per le singole righe. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **nuovo file...** Selezionare **generale** > **classe vuota**, immettere `Product` per **nome** e fare clic sul pulsante **nuovo** :

[![](table-view-images/populate01.png "Creating an empty class")](table-view-images/populate01.png#lightbox)

Rendere il file `Product.cs` simile al seguente:

```csharp
using System;

namespace MacTables
{
  public class Product
  {
    #region Computed Properties
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

Successivamente, è necessario creare una sottoclasse di `NSTableDataSource` per fornire i dati per la tabella come richiesto. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **nuovo file...** Selezionare **generale** > **classe vuota**, immettere `ProductTableDataSource` per **nome** e fare clic sul pulsante **nuovo** .

Modificare il file di `ProductTableDataSource.cs` e renderlo simile al seguente:

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

Questa classe dispone di archiviazione per gli elementi della vista tabella ed esegue l'override del `GetRowCount` per restituire il numero di righe nella tabella.

Infine, è necessario creare una sottoclasse di `NSTableDelegate` per fornire il comportamento per la tabella. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **nuovo file...** Selezionare **generale** > **classe vuota**, immettere `ProductTableDelegate` per **nome** e fare clic sul pulsante **nuovo** .

Modificare il file di `ProductTableDelegate.cs` e renderlo simile al seguente:

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

Quando si crea un'istanza del `ProductTableDelegate`, viene passata anche un'istanza del `ProductTableDataSource` che fornisce i dati per la tabella. Il metodo `GetViewForItem` è responsabile della restituzione di una vista (dati) per visualizzare la cella per una colonna e una riga. Se possibile, una vista esistente verrà riutilizzata per visualizzare la cella, se non è necessario creare una nuova vista.

Per popolare la tabella, modificare il file di `ViewController.cs` e rendere il `AwakeFromNib` metodo simile al seguente:

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

Se si esegue l'applicazione, viene visualizzato quanto segue:

[![](table-view-images/populate02.png "A sample app run")](table-view-images/populate02.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Ordinamento per colonna

Consente all'utente di ordinare i dati della tabella facendo clic su un'intestazione di colonna. Per prima cosa, fare doppio clic sul file `Main.storyboard` per aprirlo e modificarlo Interface Builder. Selezionare la colonna `Product`, immettere `Title` per la **chiave di ordinamento**`compare:` per il **selettore** e selezionare `Ascending` per l' **ordine**:

[![](table-view-images/sort01.png "Setting the sort key")](table-view-images/sort01.png#lightbox)

Selezionare la colonna `Details`, immettere `Description` per la **chiave di ordinamento**`compare:` per il **selettore** e selezionare `Ascending` per l' **ordine**:

[![](table-view-images/sort02.png "Setting the sort key")](table-view-images/sort02.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

A questo punto, modificare il file di `ProductTableDataSource.cs` e aggiungere i metodi seguenti:

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

Il metodo `Sort` consente di ordinare i dati nell'origine dati in base a un determinato campo di `Product` classe in ordine crescente o decrescente. Il metodo di `SortDescriptorsChanged` sottoposto a override verrà chiamato ogni volta che l'utilizzo fa clic su un'intestazione di colonna. Viene passato il valore della **chiave** che è stato impostato in Interface Builder e il tipo di ordinamento per la colonna.

Se si esegue l'applicazione e si fa clic nelle intestazioni di colonna, le righe verranno ordinate in base a tale colonna:

[![](table-view-images/sort03.png "An example app run")](table-view-images/sort03.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Selezione righe

Se si desidera consentire all'utente di selezionare una singola riga, fare doppio clic sul file `Main.storyboard` per aprirlo per la modifica in Interface Builder. Selezionare la visualizzazione tabella nella **gerarchia dell'interfaccia** e deselezionare la casella di controllo **multipla** in **controllo attributi**:

[![](table-view-images/select01.png "The Attribute Inspector")](table-view-images/select01.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Modificare quindi il file di `ProductTableDelegate.cs` e aggiungere il metodo seguente:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

Ciò consentirà all'utente di selezionare una singola riga nella visualizzazione tabella. Restituisce `false` per la `ShouldSelectRow` per qualsiasi riga che non si vuole che l'utente sia in grado di selezionare o `false` per ogni riga se non si vuole che l'utente sia in grado di selezionare le righe.

La visualizzazione tabella (`NSTableView`) contiene i metodi seguenti per l'utilizzo della selezione righe:

- `DeselectRow(nint)`: deseleziona la riga specificata nella tabella.
- `SelectRow(nint,bool)`: seleziona la riga specificata. Passare `false` per il secondo parametro per selezionare solo una riga alla volta.
- `SelectedRow`: restituisce la riga corrente selezionata nella tabella.
- `IsRowSelected(nint)`: restituisce `true` se la riga specificata è selezionata.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Selezione più righe

Se si desidera consentire all'utente di selezionare più righe, fare doppio clic sul file `Main.storyboard` per aprirlo per la modifica nel Interface Builder. Selezionare la visualizzazione tabella nella **gerarchia dell'interfaccia** e selezionare la casella di controllo **multipla** in **controllo attributi**:

[![](table-view-images/select02.png "The Attribute Inspector")](table-view-images/select02.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Modificare quindi il file di `ProductTableDelegate.cs` e aggiungere il metodo seguente:

```csharp
public override bool ShouldSelectRow (NSTableView tableView, nint row)
{
  return true;
}
```

Ciò consentirà all'utente di selezionare una singola riga nella visualizzazione tabella. Restituisce `false` per la `ShouldSelectRow` per qualsiasi riga che non si vuole che l'utente sia in grado di selezionare o `false` per ogni riga se non si vuole che l'utente sia in grado di selezionare le righe.

La visualizzazione tabella (`NSTableView`) contiene i metodi seguenti per l'utilizzo della selezione righe:

- `DeselectAll(NSObject)`: deseleziona tutte le righe della tabella. Utilizzare `this` per il primo parametro da inviare nell'oggetto eseguendo la selezione. 
- `DeselectRow(nint)`: deseleziona la riga specificata nella tabella.
- `SelectAll(NSobject)`: seleziona tutte le righe nella tabella. Utilizzare `this` per il primo parametro da inviare nell'oggetto eseguendo la selezione.
- `SelectRow(nint,bool)`: seleziona la riga specificata. Passare `false` per il secondo parametro deselezionare la selezione e selezionare solo una singola riga, passare `true` per estendere la selezione e includere questa riga.
- `SelectRows(NSIndexSet,bool)`: seleziona il set di righe specificato. Passare `false` per il secondo parametro deselezionare la selezione e selezionare solo una di queste righe, passare `true` per estendere la selezione e includere queste righe.
- `SelectedRow`: restituisce la riga corrente selezionata nella tabella.
- `SelectedRows`: restituisce un `NSIndexSet` contenente gli indici delle righe selezionate.
- `SelectedRowCount`: restituisce il numero delle righe selezionate.
- `IsRowSelected(nint)`: restituisce `true` se la riga specificata è selezionata.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Digitare per selezionare la riga

Se si desidera consentire all'utente di digitare un carattere con la visualizzazione tabella selezionata e selezionare la prima riga con tale carattere, fare doppio clic sul file `Main.storyboard` per aprirlo per la modifica in Interface Builder. Selezionare la visualizzazione tabella nella **gerarchia dell'interfaccia** e selezionare la casella di controllo **tipo seleziona** in **controllo attributi**:

[![](table-view-images/type01.png "Setting the selection type")](table-view-images/type01.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

A questo punto, modificare il file di `ProductTableDelegate.cs` e aggiungere il metodo seguente:

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

Il `GetNextTypeSelectMatch` metodo accetta il `searchString` specificato e restituisce la riga della prima `Product` che contiene tale stringa nel `Title`.

Se si esegue l'applicazione e si digita un carattere, viene selezionata una riga:

[![](table-view-images/type02.png "A sample app run")](table-view-images/type02.png#lightbox)

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Riordinamento delle colonne

Se si desidera consentire all'utente di trascinare le colonne riordinate nella visualizzazione tabella, fare doppio clic sul file `Main.storyboard` per aprirlo per la modifica nel Interface Builder. Selezionare la visualizzazione tabella nella **gerarchia dell'interfaccia** e selezionare la casella di controllo **riordino** in **attributo Inspector**:

[![](table-view-images/reorder01.png "The Attribute Inspector")](table-view-images/reorder01.png#lightbox)

Se si assegna un valore per la proprietà **autosave** e si seleziona il campo **informazioni colonna** , tutte le modifiche apportate al layout della tabella verranno salvate automaticamente e ripristinate alla successiva esecuzione dell'applicazione.

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

A questo punto, modificare il file di `ProductTableDelegate.cs` e aggiungere il metodo seguente:

```csharp
public override bool ShouldReorder (NSTableView tableView, nint columnIndex, nint newColumnIndex)
{
  return true;
}
```

Il metodo `ShouldReorder` deve restituire `true` per tutte le colonne che si desidera consentire di trascinare riordinate nel `newColumnIndex`. in caso contrario, restituisce `false`;

Se si esegue l'applicazione, è possibile trascinare le intestazioni di colonna per riordinare le colonne:

[![](table-view-images/reorder02.png "An example of the reordered columns")](table-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Modifica di celle

Se si desidera consentire all'utente di modificare i valori per una determinata cella, modificare il file di `ProductTableDelegate.cs` e modificare il metodo di `GetViewForItem` come segue:

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

A questo punto, se si esegue l'applicazione, l'utente può modificare le celle nella visualizzazione tabella:

[![](table-view-images/editing01.png "An example of editing a cell")](table-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Table_Views" />

## <a name="using-images-in-table-views"></a>Utilizzo di immagini nelle visualizzazioni di tabella

Per includere un'immagine come parte della cella in una `NSTableView`, è necessario modificare il modo in cui i dati vengono restituiti dal `NSTableViewDelegate's` `GetViewForItem` metodo della visualizzazione tabella per utilizzare un `NSTableCellView` anziché il `NSTextField` tipico. Esempio:

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

Per ulteriori informazioni, vedere la sezione [using images with table views](~/mac/app-fundamentals/image.md) della documentazione sull'utilizzo dell' [immagine](~/mac/app-fundamentals/image.md) .

<a name="Adding-a-Delete-Button-to-a-Row" />

## <a name="adding-a-delete-button-to-a-row"></a>Aggiunta di un pulsante Elimina a una riga

In base ai requisiti dell'app, in alcuni casi è necessario fornire un pulsante di azione per ogni riga della tabella. Ad esempio, espandere l'esempio di visualizzazione tabella creato sopra per includere un pulsante **Elimina** in ogni riga.

Per prima cosa, modificare il `Main.storyboard` nella Interface Builder di Xcode, selezionare la visualizzazione tabella e aumentare il numero di colonne a tre (3). Modificare quindi il **titolo** della nuova colonna in `Action`:

[![](table-view-images/delete01.png "Editing the column name")](table-view-images/delete01.png#lightbox)

Salvare le modifiche apportate allo storyboard e tornare a Visual Studio per Mac per sincronizzare le modifiche.

Modificare quindi il file di `ViewController.cs` e aggiungere il metodo pubblico seguente:

```csharp
public void ReloadTable ()
{
  ProductTable.ReloadData ();
}
```

Nello stesso file, modificare la creazione del nuovo delegato della vista tabella all'interno del metodo `ViewDidLoad` come indicato di seguito:

```csharp
// Populate the Product Table
ProductTable.DataSource = DataSource;
ProductTable.Delegate = new ProductTableDelegate (this, DataSource);
```

Modificare quindi il file di `ProductTableDelegate.cs` per includere una connessione privata al controller di visualizzazione e prendere il controller come parametro durante la creazione di una nuova istanza del delegato:

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

Aggiungere quindi il nuovo metodo privato seguente alla classe:

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

In questo modo, tutte le configurazioni di visualizzazione testo eseguite in precedenza nel metodo `GetViewForItem` e le inserisce in un'unica posizione richiamabile (poiché l'ultima colonna della tabella non include una visualizzazione di testo ma un pulsante).

Infine, modificare il metodo `GetViewForItem` e renderlo simile al seguente:

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
        var btn = sender as NSButton;
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
      var btn = subview as NSButton;
      if (btn != null) {
        btn.Tag = row;
      }
    }
    break;
  }

  return view;
}
```

Esaminiamo più dettagliatamente alcune sezioni del codice. Prima di tutto, se viene creata un'azione nuova `NSTableViewCell` viene eseguita in base al nome della colonna. Per le prime due colonne (**Product** e **Details**), viene chiamato il nuovo metodo `ConfigureTextField`.

Per la colonna **Action** , viene creata una nuova `NSButton` che viene aggiunta alla cella come vista secondaria:

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

La proprietà `Tag` del pulsante viene utilizzata per conservare il numero della riga in fase di elaborazione. Questo numero verrà usato in un secondo momento quando l'utente richiede l'eliminazione di una riga nell'evento `Activated` del pulsante:

```csharp
// Wireup events
button.Activated += (sender, e) => {
  // Get button and product
  var btn = sender as NSButton;
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

All'inizio del gestore dell'evento, si ottengono il pulsante e il prodotto che si trova nella riga della tabella specificata. Viene quindi visualizzato un avviso all'utente per confermare l'eliminazione della riga. Se l'utente sceglie di eliminare la riga, la riga specificata viene rimossa dall'origine dati e la tabella viene ricaricata:

```csharp
// Remove the given row from the dataset
DataSource.Products.RemoveAt((int)btn.Tag);
Controller.ReloadTable ();
```

Infine, se la cella di visualizzazione tabella viene riutilizzata anziché essere creata come nuova, il codice seguente la configura in base alla colonna in fase di elaborazione:

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
    var btn = subview as NSButton;
    if (btn != null) {
      btn.Tag = row;
    }
  }
  break;
}

```

Per la colonna **Action** , tutte le viste secondarie vengono analizzate fino a quando non viene trovato il `NSButton`, quindi `Tag` proprietà viene aggiornata in modo da puntare alla riga corrente.

Con queste modifiche, quando l'app viene eseguita ogni riga avrà un pulsante **Elimina** :

[![](table-view-images/delete02.png "The table view with deletion buttons")](table-view-images/delete02.png#lightbox)

Quando l'utente fa clic su un pulsante **Elimina** , viene visualizzato un avviso in cui viene chiesto di eliminare la riga specificata:

[![](table-view-images/delete03.png "A delete row alert")](table-view-images/delete03.png#lightbox)

Se l'utente sceglie Elimina, la riga verrà rimossa e verrà ridisegnato la tabella:

[![](table-view-images/delete04.png "The table after the row is deleted")](table-view-images/delete04.png#lightbox)

<a name="Data_Binding_Table_Views" />

## <a name="data-binding-table-views"></a>Viste tabella di data binding

Usando le tecniche di codifica e data binding nell'applicazione Xamarin.Mac, è possibile ridurre significativamente la quantità di codice da scrivere e gestire per popolare e usare gli elementi dell'interfaccia utente. Si ha anche il vantaggio di separare ulteriormente i dati di supporto (modello di_dati_) dall'interfaccia utente front-end (_Model-View-Controller_), in modo da semplificare la gestione e la progettazione di applicazioni più flessibili.

Il codice chiave-valore (KVC) è un meccanismo per accedere indirettamente alle proprietà di un oggetto, usando chiavi (stringhe formattate in modo particolare) per identificare le proprietà anziché accedervi tramite variabili di istanza o metodi di funzione di accesso (`get/set`). Implementando le funzioni di accesso conformi al codice chiave-valore nell'applicazione Xamarin.Mac, è possibile accedere ad altre funzionalità di macOS, ad esempio osservazione chiave-valore (KVO), data binding, dati principali, associazioni Cocoa e script.

Per ulteriori informazioni, vedere la sezione relativa all' [associazione dati della vista tabella](~/mac/app-fundamentals/databinding.md#Table_View_Data_Binding) della documentazione relativa al [Data Binding e alla codifica chiave-valore](~/mac/app-fundamentals/databinding.md) .

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in dettaglio l'uso delle visualizzazioni di tabella in un'applicazione Xamarin.Mac. Sono stati osservati i diversi tipi e usi delle visualizzazioni di tabella, come creare e gestire le visualizzazioni di tabella nel Interface Builder di Xcode e come usare le C# visualizzazioni di tabella nel codice.

## <a name="related-links"></a>Collegamenti correlati

- [MacTables (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/mactables)
- [MacImages (sample)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages) (MacImages - Esempio)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Outline Views](~/mac/user-interface/outline-view.md) (Visualizzazioni struttura)
- [Source Lists](~/mac/user-interface/source-list.md) (Elenchi di risorse)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [NSTableView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSTableView_Class/index.html#//apple_ref/doc/uid/TP40004125)
- [NSTableViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSTableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008622)
- [NSTableViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSTableDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004178)
