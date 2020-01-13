---
title: Strutturare le visualizzazioni in Xamarin.Mac
description: Questo articolo illustra l'uso delle visualizzazioni struttura in un'applicazione Xamarin.Mac. Descrive la creazione e la gestione delle visualizzazioni struttura in Xcode e Interface Builder e l'uso a livello di codice.
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: dd710f13d9324ce31e08641f214241e0433fe809
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73004335"
---
# <a name="outline-views-in-xamarinmac"></a>Strutturare le visualizzazioni in Xamarin.Mac

_Questo articolo illustra l'uso delle visualizzazioni struttura in un'applicazione Xamarin.Mac. Descrive la creazione e la gestione delle visualizzazioni struttura in Xcode e Interface Builder e l'uso a livello di codice._

Quando si lavora C# con e .NET in un'applicazione Xamarin.Mac, è possibile accedere alle stesse visualizzazioni struttura che uno sviluppatore lavora in *Objective-C* e *Xcode* . Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile usare la _Interface Builder_ di Xcode per creare e gestire le visualizzazioni struttura (o, facoltativamente, crearle direttamente nel C# codice).

Una visualizzazione struttura è un tipo di tabella che consente all'utente di espandere o comprimere righe di dati gerarchici. Analogamente a una visualizzazione tabella, una visualizzazione struttura consente di visualizzare i dati per un set di elementi correlati, con righe che rappresentano singoli elementi e colonne che rappresentano gli attributi di tali elementi. Diversamente da una vista tabella, gli elementi in una visualizzazione struttura non sono in un elenco semplice, sono organizzati in una gerarchia, ad esempio file e cartelle su un disco rigido.

[![](outline-view-images/populate03.png "An example app run")](outline-view-images/populate03.png#lightbox)

In questo articolo verranno illustrate le nozioni di base sull'uso delle visualizzazioni struttura in un'applicazione Xamarin.Mac. Si consiglia di usare prima di tutto l'articolo [Hello, Mac](~/mac/get-started/hello-mac.md) , in particolare l' [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e le sezioni [Outlets and actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , in cui vengono illustrati i concetti chiave e le tecniche che verranno usati in Questo articolo.

Si consiglia di esaminare la sezione [esporre C# classi/metodi in Objective-c](~/mac/internals/how-it-works.md) del documento [interno di Xamarin.Mac](~/mac/internals/how-it-works.md) , spiegando i comandi`Register`e`Export`usati per collegare le C# classi a Objective-c. oggetti ed elementi dell'interfaccia utente.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>Introduzione alle visualizzazioni struttura

Una visualizzazione struttura è un tipo di tabella che consente all'utente di espandere o comprimere righe di dati gerarchici. Analogamente a una visualizzazione tabella, una visualizzazione struttura consente di visualizzare i dati per un set di elementi correlati, con righe che rappresentano singoli elementi e colonne che rappresentano gli attributi di tali elementi. Diversamente da una vista tabella, gli elementi in una visualizzazione struttura non sono in un elenco semplice, sono organizzati in una gerarchia, ad esempio file e cartelle su un disco rigido.

Se un elemento in una visualizzazione struttura contiene altri elementi, può essere espanso o compresso dall'utente. Un elemento espandibile Visualizza un triangolo di divulgazione, che punta a destra quando l'elemento viene compresso e punta verso il basso quando l'elemento viene espanso. Se si fa clic sul triangolo di divulgazione, l'elemento viene espanso o compresso.

La visualizzazione struttura (`NSOutlineView`) è una sottoclasse della visualizzazione tabella (`NSTableView`) e, di conseguenza, eredita gran parte del suo comportamento dalla relativa classe padre. Di conseguenza, molte operazioni supportate da una vista tabella, ad esempio la selezione di righe o colonne, il riposizionamento delle colonne tramite il trascinamento delle intestazioni di colonna e così via, sono supportate anche da una visualizzazione struttura. Un'applicazione Xamarin.Mac ha il controllo di queste funzionalità e può configurare i parametri della visualizzazione struttura (nel codice o Interface Builder) per consentire o impedire determinate operazioni.

Una visualizzazione struttura non archivia i propri dati, ma si basa su un'origine dati (`NSOutlineViewDataSource`) per fornire le righe e le colonne necessarie, in base alle esigenze.

È possibile personalizzare il comportamento di una visualizzazione struttura fornendo una sottoclasse del delegato della visualizzazione struttura (`NSOutlineViewDelegate`) per supportare la gestione delle colonne di struttura, digitare per selezionare la funzionalità, la selezione e la modifica delle righe, il rilevamento personalizzato e le visualizzazioni personalizzate per le singole colonne e righe.

Poiché una visualizzazione struttura condivide gran parte del comportamento e delle funzionalità con una visualizzazione tabella, è possibile esaminare la documentazione delle [visualizzazioni di tabella](~/mac/user-interface/table-view.md) prima di continuare con questo articolo.

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>Creazione e gestione delle visualizzazioni struttura in Xcode

Quando si crea una nuova applicazione Xamarin.Mac Cocoa, per impostazione predefinita si ottiene una finestra vuota standard. Questa finestra è definita in un file di `.storyboard` incluso automaticamente nel progetto. Per modificare la progettazione di Windows, nella **Esplora soluzioni**fare doppio clic sul file `Main.storyboard`:

[![](outline-view-images/edit01.png "Selecting the main storyboard")](outline-view-images/edit01.png#lightbox)

Verrà aperta la progettazione della finestra nella Interface Builder di Xcode:

[![](outline-view-images/edit02.png "Editing the UI in Xcode")](outline-view-images/edit02.png#lightbox)

Digitare `outline` nella casella di ricerca di **Inspector Library** per semplificare la ricerca dei controlli di visualizzazione struttura:

[![](outline-view-images/edit03.png "Selecting an Outline View from the Library")](outline-view-images/edit03.png#lightbox)

Trascinare una visualizzazione struttura sul controller di visualizzazione nell' **Editor dell'interfaccia**, fare in modo che riempia l'area di contenuto del controller di visualizzazione e impostarla su dove compatta e si espande con la finestra nell' **editor di vincoli**:

[![](outline-view-images/edit04.png "Editing the constraints")](outline-view-images/edit04.png#lightbox)

Selezionare la visualizzazione struttura nella **gerarchia dell'interfaccia** e le proprietà seguenti sono disponibili nel **controllo attributi**:

[![](outline-view-images/edit05.png "The Attribute Inspector")](outline-view-images/edit05.png#lightbox)

- **Colonna struttura** : colonna della tabella in cui vengono visualizzati i dati gerarchici.
- **Colonna struttura salvataggio** automatico: se `true`, la colonna struttura verrà salvata e ripristinata automaticamente tra le esecuzioni dell'applicazione.
- **Rientro** : la quantità di rientro delle colonne sotto un elemento espanso.
- Il **rientro segue le celle** : se `true`, il contrassegno dei rientri verrà rientrato insieme alle celle.
- **Salva automaticamente elementi espansi** : se `true`, lo stato espanso/compresso degli elementi verrà salvato e ripristinato automaticamente tra le esecuzioni dell'applicazione.
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
> A meno che non si stia gestendo un'applicazione Xamarin.Mac legacy, è consigliabile usare `NSView` visualizzazioni struttura basate su `NSCell` viste di tabella basate su. `NSCell` è considerato Legacy e potrebbe non essere supportato in futuro.

Selezionare una colonna della tabella nella **gerarchia dell'interfaccia** e le proprietà seguenti sono disponibili nel **controllo attributi**:

[![](outline-view-images/edit06.png "The Attribute Inspector")](outline-view-images/edit06.png#lightbox)

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

[![](outline-view-images/edit07.png "The Attribute Inspector")](outline-view-images/edit07.png#lightbox)

Si tratta di tutte le proprietà di una vista standard. È anche possibile ridimensionare le righe per questa colonna.

Selezionare una cella di visualizzazione tabella (per impostazione predefinita, si tratta di un `NSTextField`) nella **gerarchia dell'interfaccia** e le proprietà seguenti sono disponibili nel **controllo attributi**:

[![](outline-view-images/edit08.png "The Attribute Inspector")](outline-view-images/edit08.png#lightbox)

Sono disponibili tutte le proprietà di un campo di testo standard da impostare qui. Per impostazione predefinita, un campo di testo standard viene utilizzato per visualizzare i dati di una cella in una colonna.

Selezionare una visualizzazione della cella della tabella (`NSTableFieldCell`) nella **gerarchia dell'interfaccia** e le proprietà seguenti sono disponibili in **controllo attributi**:

[![](outline-view-images/edit09.png "The Attribute Inspector")](outline-view-images/edit09.png#lightbox)

Di seguito sono riportate le impostazioni più importanti:

- **Layout** : consente di selezionare il layout delle celle in questa colonna.
- **Usa la modalità a riga singola** : se `true`, la cella è limitata a una singola riga.
- **Prima larghezza del layout di runtime** : se `true`, la cella preferisce la larghezza impostata (manualmente o automaticamente) quando viene visualizzata la prima volta che l'applicazione viene eseguita.
- **Azione** : controlla quando viene inviata l' **azione** di modifica per la cella.
- **Behavior** : definisce se una cella è selezionabile o modificabile.
- **Rich Text** : se `true`, la cella può visualizzare testo formattato e in stile.
- **Annulla** : se `true`, la cella assume la responsabilità del comportamento di annullamento.

Selezionare la visualizzazione della cella della tabella (`NSTableFieldCell`) nella parte inferiore di una colonna della tabella nella **gerarchia dell'interfaccia**:

[![](outline-view-images/edit11.png "Selecting the table cell view")](outline-view-images/edit10.png#lightbox)

In questo modo è possibile modificare la visualizzazione della cella della tabella utilizzata come _modello_ di base per tutte le celle create per la colonna specificata.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Aggiunta di azioni e Outlet

Analogamente a qualsiasi altro controllo dell'interfaccia utente di Cocoa, è necessario esporre la visualizzazione struttura e le colonne e C# le celle al codice usando le **azioni** e gli **Outlet** (in base alle funzionalità necessarie).

Il processo è lo stesso per qualsiasi elemento di visualizzazione struttura che si vuole esporre:

1. Passare all' **editor di assistente** e verificare che sia selezionato il file di `ViewController.h`:

    [![](outline-view-images/edit11.png "Selecting the correct .h file")](outline-view-images/edit11.png#lightbox)
2. Selezionare la visualizzazione struttura dalla **gerarchia dell'interfaccia**, fare clic su CTRL e trascinare il file `ViewController.h`.
3. Creare un' **Outlet** per la visualizzazione struttura denominata `ProductOutline`:

    [![](outline-view-images/edit13.png "Configuring an Outlet")](outline-view-images/edit13.png#lightbox)
4. Creare **Outlet** per le colonne Tables, così come `ProductColumn` e `DetailsColumn`:

    [![](outline-view-images/edit14.png "Configuring an Outlet")](outline-view-images/edit14.png#lightbox)
5. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Successivamente, si scriverà il codice per visualizzare alcuni dati per il contorno durante l'esecuzione dell'applicazione.

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>Popolamento della visualizzazione struttura

Con la visualizzazione struttura progettata in Interface Builder ed esposta tramite un **Outlet**, successivamente è necessario creare il C# codice per popolarlo.

Prima di tutto, creare una nuova classe `Product` per conservare le informazioni per le singole righe e i gruppi di sottoprodotti. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **nuovo file...** Selezionare **generale** > **classe vuota**, immettere `Product` per **nome** e fare clic sul pulsante **nuovo** :

[![](outline-view-images/populate01.png "Creating an empty class")](outline-view-images/populate01.png#lightbox)

Rendere il file `Product.cs` simile al seguente:

```csharp
using System;
using Foundation;
using System.Collections.Generic;

namespace MacOutlines
{
    public class Product : NSObject
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Computed Properties
        public string Title { get; set;} = "";
        public string Description { get; set;} = "";
        public bool IsProductGroup {
            get { return (Products.Count > 0); }
        }
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

Successivamente, è necessario creare una sottoclasse di `NSOutlineDataSource` per fornire i dati per il contorno come richiesto. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **nuovo file...** Selezionare **generale** > **classe vuota**, immettere `ProductOutlineDataSource` per **nome** e fare clic sul pulsante **nuovo** .

Modificare il file di `ProductTableDataSource.cs` e renderlo simile al seguente:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDataSource : NSOutlineViewDataSource
    {
        #region Public Variables
        public List<Product> Products = new List<Product>();
        #endregion

        #region Constructors
        public ProductOutlineDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override nint GetChildrenCount (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products.Count;
            } else {
                return ((Product)item).Products.Count;
            }

        }

        public override NSObject GetChild (NSOutlineView outlineView, nint childIndex, NSObject item)
        {
            if (item == null) {
                return Products [childIndex];
            } else {
                return ((Product)item).Products [childIndex];
            }

        }

        public override bool ItemExpandable (NSOutlineView outlineView, NSObject item)
        {
            if (item == null) {
                return Products [0].IsProductGroup;
            } else {
                return ((Product)item).IsProductGroup;
            }

        }
        #endregion
    }
}
```

Questa classe dispone di archiviazione per gli elementi della visualizzazione struttura ed esegue l'override del `GetChildrenCount` per restituire il numero di righe nella tabella. Il `GetChild` restituisce un elemento padre o figlio specifico (come richiesto dalla visualizzazione struttura) e il `ItemExpandable` definisce l'elemento specificato come padre o figlio.

Infine, è necessario creare una sottoclasse di `NSOutlineDelegate` per fornire il comportamento per la struttura. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul progetto e scegliere **Aggiungi** > **nuovo file...** Selezionare **generale** > **classe vuota**, immettere `ProductOutlineDelegate` per **nome** e fare clic sul pulsante **nuovo** .

Modificare il file di `ProductOutlineDelegate.cs` e renderlo simile al seguente:

```csharp
using System;
using AppKit;
using CoreGraphics;
using Foundation;
using System.Collections;
using System.Collections.Generic;

namespace MacOutlines
{
    public class ProductOutlineDelegate : NSOutlineViewDelegate
    {
        #region Constants
        private const string CellIdentifier = "ProdCell";
        #endregion

        #region Private Variables
        private ProductOutlineDataSource DataSource;
        #endregion

        #region Constructors
        public ProductOutlineDelegate (ProductOutlineDataSource datasource)
        {
            this.DataSource = datasource;
        }
        #endregion

        #region Override Methods

        public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
            // This pattern allows you reuse existing views when they are no-longer in use.
            // If the returned view is null, you instance up a new view
            // If a non-null view is returned, you modify it enough to reflect the new data
            NSTextField view = (NSTextField)outlineView.MakeView (CellIdentifier, this);
            if (view == null) {
                view = new NSTextField ();
                view.Identifier = CellIdentifier;
                view.BackgroundColor = NSColor.Clear;
                view.Bordered = false;
                view.Selectable = false;
                view.Editable = false;
            }

            // Cast item
            var product = item as Product;

            // Setup view based on the column selected
            switch (tableColumn.Title) {
            case "Product":
                view.StringValue = product.Title;
                break;
            case "Details":
                view.StringValue = product.Description;
                break;
            }

            return view;
        }
        #endregion
    }
}
```

Quando si crea un'istanza del `ProductOutlineDelegate`, viene passata anche un'istanza del `ProductOutlineDataSource` che fornisce i dati per la struttura. Il metodo `GetView` è responsabile della restituzione di una vista (dati) per visualizzare la cella per una colonna e una riga. Se possibile, una vista esistente verrà riutilizzata per visualizzare la cella, se non è necessario creare una nuova vista.

Per popolare il contorno, modificare il file di `MainWindow.cs` e rendere il `AwakeFromNib` metodo simile al seguente:

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create data source and populate
    var DataSource = new ProductOutlineDataSource ();

    var Vegetables = new Product ("Vegetables", "Greens and Other Produce");
    Vegetables.Products.Add (new Product ("Cabbage", "Brassica oleracea - Leaves, axillary buds, stems, flowerheads"));
    Vegetables.Products.Add (new Product ("Turnip", "Brassica rapa - Tubers, leaves"));
    Vegetables.Products.Add (new Product ("Radish", "Raphanus sativus - Roots, leaves, seed pods, seed oil, sprouting"));
    Vegetables.Products.Add (new Product ("Carrot", "Daucus carota - Root tubers"));
    DataSource.Products.Add (Vegetables);

    var Fruits = new Product ("Fruits", "Fruit is a part of a flowering plant that derives from specific tissues of the flower");
    Fruits.Products.Add (new Product ("Grape", "True Berry"));
    Fruits.Products.Add (new Product ("Cucumber", "Pepo"));
    Fruits.Products.Add (new Product ("Orange", "Hesperidium"));
    Fruits.Products.Add (new Product ("Blackberry", "Aggregate fruit"));
    DataSource.Products.Add (Fruits);

    var Meats = new Product ("Meats", "Lean Cuts");
    Meats.Products.Add (new Product ("Beef", "Cow"));
    Meats.Products.Add (new Product ("Pork", "Pig"));
    Meats.Products.Add (new Product ("Veal", "Young Cow"));
    DataSource.Products.Add (Meats);

    // Populate the outline
    ProductOutline.DataSource = DataSource;
    ProductOutline.Delegate = new ProductOutlineDelegate (DataSource);

}
```

Se si esegue l'applicazione, viene visualizzato quanto segue:

[![](outline-view-images/populate02.png "The collapsed view")](outline-view-images/populate02.png#lightbox)

Se si espande un nodo nella visualizzazione struttura, l'aspetto sarà simile al seguente:

[![](outline-view-images/populate03.png "The expanded view")](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Ordinamento per colonna

Consente all'utente di ordinare i dati nella struttura facendo clic su un'intestazione di colonna. Per prima cosa, fare doppio clic sul file `Main.storyboard` per aprirlo e modificarlo Interface Builder. Selezionare la colonna `Product`, immettere `Title` per la **chiave di ordinamento**`compare:` per il **selettore** e selezionare `Ascending` per l' **ordine**:

[![](outline-view-images/sort01.png "Setting the sort key order")](outline-view-images/sort01.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

A questo punto, modificare il file di `ProductOutlineDataSource.cs` e aggiungere i metodi seguenti:

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
    }
}

public override void SortDescriptorsChanged (NSOutlineView outlineView, NSSortDescriptor[] oldDescriptors)
{
    // Sort the data
    Sort (oldDescriptors [0].Key, oldDescriptors [0].Ascending);
    outlineView.ReloadData ();
}
```

Il metodo `Sort` consente di ordinare i dati nell'origine dati in base a un determinato campo di `Product` classe in ordine crescente o decrescente. Il metodo di `SortDescriptorsChanged` sottoposto a override verrà chiamato ogni volta che l'utilizzo fa clic su un'intestazione di colonna. Viene passato il valore della **chiave** che è stato impostato in Interface Builder e il tipo di ordinamento per la colonna.

Se si esegue l'applicazione e si fa clic nelle intestazioni di colonna, le righe verranno ordinate in base a tale colonna:

[![](outline-view-images/sort02.png "Example of sorted output")](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Selezione righe

Se si desidera consentire all'utente di selezionare una singola riga, fare doppio clic sul file `Main.storyboard` per aprirlo per la modifica in Interface Builder. Selezionare la visualizzazione struttura nella **gerarchia dell'interfaccia** e deselezionare la casella di controllo **multipla** in **controllo attributi**:

[![](outline-view-images/select01.png "The Attribute Inspector")](outline-view-images/select01.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Modificare quindi il file di `ProductOutlineDelegate.cs` e aggiungere il metodo seguente:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Ciò consentirà all'utente di selezionare una singola riga nella visualizzazione struttura. Restituire `false` per la `ShouldSelectItem` per qualsiasi elemento che non si desidera che l'utente sia in grado di selezionare o `false` per ogni elemento se non si desidera che l'utente sia in grado di selezionare elementi.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Selezione più righe

Se si desidera consentire all'utente di selezionare più righe, fare doppio clic sul file `Main.storyboard` per aprirlo per la modifica nel Interface Builder. Selezionare la visualizzazione struttura nella **gerarchia dell'interfaccia** e selezionare la casella di controllo **multipla** in **controllo attributi**:

[![](outline-view-images/select02.png "The Attribute Inspector")](outline-view-images/select02.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Modificare quindi il file di `ProductOutlineDelegate.cs` e aggiungere il metodo seguente:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Ciò consentirà all'utente di selezionare una singola riga nella visualizzazione struttura. Restituire `false` per la `ShouldSelectRow` per qualsiasi elemento che non si desidera che l'utente sia in grado di selezionare o `false` per ogni elemento se non si desidera che l'utente sia in grado di selezionare elementi.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Digitare per selezionare la riga

Se si desidera consentire all'utente di digitare un carattere con la visualizzazione struttura selezionata e selezionare la prima riga con tale carattere, fare doppio clic sul file `Main.storyboard` per aprirlo per la modifica in Interface Builder. Selezionare la visualizzazione struttura nella **gerarchia dell'interfaccia** e selezionare la casella di controllo **tipo seleziona** in **controllo attributi**:

[![](outline-view-images/type01.png "Editing the row type")](outline-view-images/type01.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

A questo punto, modificare il file di `ProductOutlineDelegate.cs` e aggiungere il metodo seguente:

```csharp
public override NSObject GetNextTypeSelectMatch (NSOutlineView outlineView, NSObject startItem, NSObject endItem, string searchString)
{
    foreach(Product product in DataSource.Products) {
        if (product.Title.Contains (searchString)) {
            return product;
        }
    }

    // Not found
    return null;
}
```

Il `GetNextTypeSelectMatch` metodo accetta il `searchString` specificato e restituisce l'elemento della prima `Product` che contiene tale stringa nel `Title`.

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Riordinamento delle colonne

Se si desidera consentire all'utente di trascinare le colonne riordinate nella visualizzazione struttura, fare doppio clic sul file `Main.storyboard` per aprirlo per modificarlo Interface Builder. Selezionare la visualizzazione struttura nella **gerarchia dell'interfaccia** e selezionare la casella di controllo **riordino** in **attributo Inspector**:

[![](outline-view-images/reorder01.png "The Attribute Inspector")](outline-view-images/reorder01.png#lightbox)

Se si assegna un valore per la proprietà **autosave** e si seleziona il campo **informazioni colonna** , tutte le modifiche apportate al layout della tabella verranno salvate automaticamente e ripristinate alla successiva esecuzione dell'applicazione.

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

A questo punto, modificare il file di `ProductOutlineDelegate.cs` e aggiungere il metodo seguente:

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

Il metodo `ShouldReorder` deve restituire `true` per tutte le colonne che si desidera consentire di trascinare riordinate nel `newColumnIndex`. in caso contrario, restituisce `false`;

Se si esegue l'applicazione, è possibile trascinare le intestazioni di colonna per riordinare le colonne:

[![](outline-view-images/reorder02.png "Example of reordering columns")](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>Modifica di celle

Se si desidera consentire all'utente di modificare i valori per una determinata cella, modificare il file di `ProductOutlineDelegate.cs` e modificare il metodo di `GetViewForItem` come segue:

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTextField view = (NSTextField)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTextField ();
        view.Identifier = tableColumn.Title;
        view.BackgroundColor = NSColor.Clear;
        view.Bordered = false;
        view.Selectable = false;
        view.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.StringValue;
            break;
        case "Details":
            prod.Description = view.StringValue;
            break;
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.StringValue = product.Title;
        break;
    case "Details":
        view.StringValue = product.Description;
        break;
    }

    return view;
}
```

A questo punto, se si esegue l'applicazione, l'utente può modificare le celle nella visualizzazione tabella:

[![](outline-view-images/editing01.png "An example of editing cells")](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>Utilizzo di immagini nelle visualizzazioni struttura

Per includere un'immagine come parte della cella in una `NSOutlineView`, è necessario modificare il modo in cui i dati vengono restituiti dal `NSTableViewDelegate's` `GetView` metodo della visualizzazione struttura per usare un `NSTableCellView` anziché il `NSTextField` tipico. Esempio:

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

Per ulteriori informazioni, vedere la sezione [using images with Outline views](~/mac/app-fundamentals/image.md) della documentazione [Working with image](~/mac/app-fundamentals/image.md) .

<a name="Data_Binding_Outline_Views" />

## <a name="data-binding-outline-views"></a>Viste struttura di data binding

Usando le tecniche di codifica e data binding nell'applicazione Xamarin.Mac, è possibile ridurre significativamente la quantità di codice da scrivere e gestire per popolare e usare gli elementi dell'interfaccia utente. Si ha anche il vantaggio di separare ulteriormente i dati di supporto (modello di_dati_) dall'interfaccia utente front-end (_Model-View-Controller_), in modo da semplificare la gestione e la progettazione di applicazioni più flessibili.

Il codice chiave-valore (KVC) è un meccanismo per accedere indirettamente alle proprietà di un oggetto, usando chiavi (stringhe formattate in modo particolare) per identificare le proprietà anziché accedervi tramite variabili di istanza o metodi di funzione di accesso (`get/set`). Implementando le funzioni di accesso conformi al codice chiave-valore nell'applicazione Xamarin.Mac, è possibile accedere ad altre funzionalità di macOS, ad esempio osservazione chiave-valore (KVO), data binding, dati principali, associazioni Cocoa e script.

Per ulteriori informazioni, vedere la sezione relativa all' [associazione dati della visualizzazione struttura](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding) del data binding e della documentazione relativa al [codice chiave-valore](~/mac/app-fundamentals/databinding.md) .

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha esaminato in dettaglio l'uso delle visualizzazioni struttura in un'applicazione Xamarin.Mac. Sono stati osservati i diversi tipi e usi delle visualizzazioni struttura, come creare e gestire le visualizzazioni struttura nel Interface Builder di Xcode e come usare le visualizzazioni struttura C# nel codice.

## <a name="related-links"></a>Collegamenti correlati

- [MacOutlines (esempio)](https://docs.microsoft.com/samples/xamarin/mac-samples/macoutlines)
- [MacImages (sample)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages) (MacImages - Esempio)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Table Views](~/mac/user-interface/table-view.md) (Visualizzazioni tabelle)
- [Source Lists](~/mac/user-interface/source-list.md) (Elenchi di risorse)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione alle visualizzazioni struttura](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
