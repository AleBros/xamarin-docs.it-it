---
title: Visualizzazione della struttura in xamarin. Mac
description: Questo articolo descrive l'uso delle visualizzazioni struttura in un'applicazione xamarin. Mac. Descrive la creazione e gestire le visualizzazioni struttura in Xcode e Interface Builder e utilizzarli a livello di codice.
ms.prod: xamarin
ms.assetid: 043248EE-11DA-4E96-83A3-08824A4F2E01
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: fd97dbbe102c5a755c4a8974cf1a952c0050ac7c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61292695"
---
# <a name="outline-views-in-xamarinmac"></a>Visualizzazione della struttura in xamarin. Mac

_Questo articolo descrive l'uso delle visualizzazioni struttura in un'applicazione xamarin. Mac. Descrive la creazione e gestire le visualizzazioni struttura in Xcode e Interface Builder e utilizzarli a livello di codice._

Quando si lavora con C# e .NET in un'applicazione xamarin. Mac, è possibile utilizzare la stessa struttura vede che gli sviluppatori che lavorano *Objective-C* e *Xcode* viene. Poiché xamarin. Mac si integra direttamente con Xcode, è possibile usare Xcode _Interface Builder_ per creare e gestire le visualizzazioni di struttura (oppure crearle direttamente nel codice c#).

Una visualizzazione della struttura è un tipo di tabella che consente all'utente di espandere o comprimere le righe di dati gerarchici. Ad esempio la visualizzazione di una tabella, una visualizzazione della struttura sono visualizzati i dati per un set di elementi correlati, le righe che rappresentano singoli elementi e le colonne che rappresentano gli attributi di tali elementi. A differenza di una visualizzazione tabella, gli elementi in una visualizzazione della struttura non sono in un elenco semplice, sono organizzati in una gerarchia, ad esempio file e cartelle su un disco rigido.

[![](outline-view-images/populate03.png "Eseguire un'app di esempio")](outline-view-images/populate03.png#lightbox)

In questo articolo, si affronterà le nozioni di base dell'uso delle visualizzazioni struttura in un'applicazione xamarin. Mac. È altamente consigliabile usare la [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare le [Introduzione a Xcode e Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) e [Outlet e azioni](~/mac/get-started/hello-mac.md#outlets-and-actions) le sezioni, perché illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

È possibile ottenere un quadro il [c# esposizione di classi / metodi di Objective-c](~/mac/internals/how-it-works.md) sezione del [meccanismi interni di xamarin. Mac](~/mac/internals/how-it-works.md) del documento, viene spiegato il `Register` e `Export` comandi utilizzato per wireup classi c# per gli oggetti di Objective-C e gli elementi dell'interfaccia utente.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>Introduzione a visualizzazioni della struttura

Una visualizzazione della struttura è un tipo di tabella che consente all'utente di espandere o comprimere le righe di dati gerarchici. Ad esempio la visualizzazione di una tabella, una visualizzazione della struttura sono visualizzati i dati per un set di elementi correlati, le righe che rappresentano singoli elementi e le colonne che rappresentano gli attributi di tali elementi. A differenza di una visualizzazione tabella, gli elementi in una visualizzazione della struttura non sono in un elenco semplice, sono organizzati in una gerarchia, ad esempio file e cartelle su un disco rigido.

Se un elemento in una visualizzazione struttura contiene altri elementi, possono essere espansi o compressi dall'utente. Un elemento espandibile viene visualizzato un triangolo di visualizzazione, che fa riferimento a destra quando l'elemento è compresso e punta verso il basso, quando l'elemento è espanso. Facendo clic sul triangolo divulgazione fa sì che l'elemento espandere o comprimere.

Visualizzazione della struttura (`NSOutlineView`) è una sottoclasse della visualizzazione della tabella (`NSTableView`) e di conseguenza, gran parte del suo comportamento eredita dalla classe padre. Di conseguenza, molte operazioni supportate da una visualizzazione tabella, ad esempio la selezione di righe o colonne, il riposizionamento delle colonne trascinando le intestazioni di colonna e così via, sono supportate anche da una visualizzazione della struttura. Un'applicazione xamarin. Mac ha il controllo di queste funzionalità ed è possibile configurare i parametri della visualizzazione della struttura (sia nel codice o Interface Builder) per consentire o impedire determinate operazioni.

Una visualizzazione della struttura non archivia i propri dati, ma si basa su un'origine dati (`NSOutlineViewDataSource`) per fornire sia le righe e colonne necessari, su una base alle esigenze.

Comportamento di una visualizzazione della struttura può essere personalizzato specificando una sottoclasse del delegato di visualizzazione di struttura (`NSOutlineViewDelegate`) per supportare la gestione di colonna di struttura, digitare per selezionare funzionalità, la selezione della riga e la modifica, rilevamento personalizzati e visualizzazioni personalizzate per singoli le colonne e righe.

Poiché una visualizzazione della struttura condivide molte delle funzionalità e il suo comportamento con una visualizzazione tabella, è possibile passare attraverso il [viste delle tabelle](~/mac/user-interface/table-view.md) documentazione prima di continuare con questo articolo.

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>Creare e gestire la visualizzazione della struttura in Xcode

Quando si crea una nuova applicazione xamarin. Mac Cocoa, si ottiene una normale finestra vuota, per impostazione predefinita. Questo windows è definito in un `.storyboard` file automaticamente inclusi nel progetto. Per modificare la progettazione di windows, nelle **Esplora soluzioni**, fare doppio clic il `Main.storyboard` file:

[![](outline-view-images/edit01.png "Selezionando lo storyboard principale")](outline-view-images/edit01.png#lightbox)

Verrà aperta la progettazione di finestra in Interface Builder di Xcode:

[![](outline-view-images/edit02.png "Modifica l'interfaccia utente in Xcode")](outline-view-images/edit02.png#lightbox)

Tipo di `outline` nella **Library Inspector** casella di ricerca per renderne più semplice trovare i controlli di visualizzazione struttura:

[![](outline-view-images/edit03.png "Selezione di una visualizzazione della struttura dalla libreria")](outline-view-images/edit03.png#lightbox)

Trascinare il Controller di visualizzazione in una visualizzazione struttura la **Editor dell'interfaccia**, apportare riempire l'area di contenuto del Controller di visualizzazione e impostarlo su cui si riduce e cresce con la finestra nel **Editor vincoli di**:

[![](outline-view-images/edit04.png "Modifica di vincoli")](outline-view-images/edit04.png#lightbox)

Selezionare la vista struttura le **gerarchia delle interfacce** e le proprietà seguenti sono disponibili nel **attributo Inspector**:

[![](outline-view-images/edit05.png "Attribute Inspector")](outline-view-images/edit05.png#lightbox)

- **Colonna di struttura** -la colonna della tabella in cui viene visualizzati i dati gerarchici.
- **Colonna struttura di salvataggio automatico** - se `true`, la colonna di struttura automaticamente salvata e ripristinata tra l'esecuzione dell'applicazione.
- **Rientro** -la quantità per il rientro delle colonne in un elemento espanso.
- **Rientro segue celle** - se `true`, il contrassegno di rientro verrà aumentato il rientro insieme le celle.
- **Salva automaticamente gli elementi espansi** - se `true`, lo stato espanso o compresso degli elementi verrà automaticamente salvato e ripristinato tra l'esecuzione dell'applicazione.
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
> A meno che non si esegue la manutenzione di un'applicazione xamarin. Mac legacy, `NSView` in base la visualizzazione della struttura deve essere utilizzata su `NSCell` basati su viste delle tabelle. `NSCell` viene considerato legacy e potrebbero non essere supportate in futuro.

Selezionare una colonna della tabella nel **gerarchia delle interfacce** e le proprietà seguenti sono disponibili nel **attributo Inspector**:

[![](outline-view-images/edit06.png "Attribute Inspector")](outline-view-images/edit06.png#lightbox)

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

[![](outline-view-images/edit07.png "Attribute Inspector")](outline-view-images/edit07.png#lightbox)

Queste sono tutte le proprietà di una vista standard. È inoltre la possibilità di ridimensionare le righe per questa colonna qui.

Selezionare una cella di visualizzazione tabella (per impostazione predefinita, questo è un `NSTextField`) nei **gerarchia delle interfacce** e le proprietà seguenti sono disponibili nel **attributo Inspector**:

[![](outline-view-images/edit08.png "Attribute Inspector")](outline-view-images/edit08.png#lightbox)

È possibile tutte le proprietà di un campo di testo standard per questa pagina. Per impostazione predefinita, un campo di testo standard consente di visualizzare i dati per una cella in una colonna.

Selezionare una visualizzazione di cella di tabella (`NSTableFieldCell`) nel **gerarchia delle interfacce** e le proprietà seguenti sono disponibili nel **attributo Inspector**:

[![](outline-view-images/edit09.png "Attribute Inspector")](outline-view-images/edit09.png#lightbox)

Di seguito le impostazioni più importanti sono:

- **Layout** : selezionare la modalità di celle in questa colonna vengono disposti.
- **Usa modalità a riga singola** - se `true`, la cella è limitata a una singola riga.
- **Prima fase di esecuzione Layout larghezza** : se `true`, preferisce la larghezza impostata per tale (automaticamente o manualmente) quando la cella viene visualizzato alla prima esecuzione dell'applicazione.
- **Azione** -esegue i controlli di modifica **azione** viene inviato per la cella.
- **Comportamento** -definisce se una cella è selezionabile né modificabile.
- **Rich Text** - se `true`, la cella può visualizzare il testo formattato e con stile.
- **Annullare** - se `true`, la cella si assume la responsabilità per è annullare il comportamento.

Selezionare la visualizzazione della cella di tabella (`NSTableFieldCell`) nella parte inferiore di una colonna di tabella nel **gerarchia interfaccia**:

[![](outline-view-images/edit11.png "Selezione di visualizzazione della cella di tabella")](outline-view-images/edit10.png#lightbox)

In questo modo è possibile modificare la vista di cella di tabella usata come base _Pattern_ per tutte le celle create per la colonna specificata.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Aggiunta di azioni e gli Outlet

Proprio come qualsiasi altro controllo interfaccia utente di Cocoa, è necessario esporre la visualizzazione di struttura e si tratta di colonne e le celle di codice c# tramite **azioni** e **Outlet** (basato sulle funzionalità necessarie).

Il processo è lo stesso per qualsiasi elemento di visualizzazione struttura che si desidera esporre:

1. Passare al **Assistente dell'Editor** e assicurarsi che il `ViewController.h` viene selezionato un file: 

    [![](outline-view-images/edit11.png "Selezionare il file corretto. h")](outline-view-images/edit11.png#lightbox)
2. Visualizzazione della struttura da selezionare la **gerarchia delle interfacce**, CTRL + clic e trascinare il `ViewController.h` file.
3. Creare un **Outlet** per la visualizzazione di struttura denominato `ProductOutline`: 

    [![](outline-view-images/edit13.png "Configurazione di un Outlet")](outline-view-images/edit13.png#lightbox)
4. Creare **Outlet** per le colonne di tabelle anche chiamato `ProductColumn` e `DetailsColumn`: 

    [![](outline-view-images/edit14.png "Configurazione di un Outlet")](outline-view-images/edit14.png#lightbox)
5. Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Successivamente, si scriverà la visualizzazione codice alcuni dati per la struttura quando viene eseguita l'applicazione.

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>Popolamento di visualizzazione della struttura

Con la visualizzazione struttura progettata in Interface Builder e vengono esposte tramite un **Outlet**, successivamente è necessario creare il codice c# per popolarlo.

In primo luogo, è possibile creare un nuovo `Product` classe per contenere le informazioni per le singole righe e gruppi di prodotti sub. Nel **Esplora soluzioni**, fare clic sul progetto e selezionare **Add** > **nuovo File...** Selezionare **generali** > **classe vuota**, immettere `Product` per il **nome** e fare clic su di **New** pulsante:

[![](outline-view-images/populate01.png "Creazione di una classe vuota")](outline-view-images/populate01.png#lightbox)

Rendere il `Product.cs` file aspetto simile al seguente:

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

Successivamente, è necessario creare una sottoclasse di `NSOutlineDataSource` per fornire i dati per la descrizione di base alle esigenze. Nel **Esplora soluzioni**, fare clic sul progetto e selezionare **Add** > **nuovo File...** Selezionare **generali** > **classe vuota**, immettere `ProductOutlineDataSource` per il **nome** e fare clic su di **New** pulsante.

Modificare il `ProductTableDataSource.cs` file e renderlo simile al seguente:

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

Questa classe dispone di spazio di archiviazione per gli elementi della visualizzazione struttura ed esegue l'override di `GetChildrenCount` per restituire il numero di righe nella tabella. Il `GetChild` restituisce un elemento padre o figlio specifico (come richiesto dalla visualizzazione della struttura) e il `ItemExpandable` definisce l'elemento specificato come padre o figlio.

Infine, è necessario creare una sottoclasse di `NSOutlineDelegate` per fornire il comportamento per il contorno. Nel **Esplora soluzioni**, fare clic sul progetto e selezionare **Add** > **nuovo File...** Selezionare **generali** > **classe vuota**, immettere `ProductOutlineDelegate` per il **nome** e fare clic su di **New** pulsante.

Modificare il `ProductOutlineDelegate.cs` file e renderlo simile al seguente:

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

Quando si crea un'istanza del `ProductOutlineDelegate`, viene anche passato in un'istanza del `ProductOutlineDataSource` che fornisce i dati per il contorno. Il `GetView` metodo è responsabile della restituzione di una visualizzazione (dati) per visualizzare la cella per una colonna e riga. Se possibile, una vista esistente verrà riutilizzata per la cella di visualizzazione, se non è necessario creare una nuova visualizzazione.

Per popolare la struttura, è possibile modificare il `MainWindow.cs` file e apportare le `AwakeFromNib` metodo aspetto simile al seguente:

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

Se si esegue l'applicazione, verrà visualizzato quanto segue:

[![](outline-view-images/populate02.png "La visualizzazione compressa")](outline-view-images/populate02.png#lightbox)

Se si espande un nodo nella visualizzazione della struttura, avrà un aspetto simile al seguente:

[![](outline-view-images/populate03.png "Visualizzazione espansa")](outline-view-images/populate03.png#lightbox)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>L'ordinamento per colonna

È possibile consentire all'utente di ordinare i dati nella struttura facendo clic su un'intestazione di colonna. In primo luogo, fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo in Interface Builder. Selezionare il `Product` colonna, immettere `Title` per il **chiave di ordinamento**, `compare:` per i **selettore** e selezionare `Ascending` per il **ordine**:

[![](outline-view-images/sort01.png "Impostazione dell'ordine di chiave di ordinamento")](outline-view-images/sort01.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

A questo punto è possibile modificare il `ProductOutlineDataSource.cs` file e aggiungere i metodi seguenti:

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

Il `Sort` metodo consentono di ordinare i dati nell'origine dati basata su un determinato `Product` campo della classe in ordine crescente o decrescente. Sottoposto a override `SortDescriptorsChanged` metodo verrà chiamato ogni volta che l'uso fa clic su un'intestazione di colonna. Verrà passato il **chiave** valore su cui è impostati in Interface Builder e l'ordinamento per colonna.

Se si esegue l'applicazione e fare clic su nelle intestazioni di colonna, le righe verranno ordinate in base alla colonna:

[![](outline-view-images/sort02.png "Esempio di output ordinato")](outline-view-images/sort02.png#lightbox)

<a name="Row_Selection" />

## <a name="row-selection"></a>Selezione riga

Se si vuole consentire all'utente di selezionare una singola riga, fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo in Interface Builder. Selezionare la vista struttura il **gerarchia delle interfacce** e deselezionare il **più** casella di controllo nel **attributo Inspector**:

[![](outline-view-images/select01.png "Attribute Inspector")](outline-view-images/select01.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.


Modificare quindi il `ProductOutlineDelegate.cs` file e aggiungere il metodo seguente:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Questa operazione consentirà all'utente di selezionare ogni singola riga nella visualizzazione della struttura. Restituire `false` per il `ShouldSelectItem` per qualsiasi elemento che non si desidera l'utente sia in grado di selezionare o `false` per ogni elemento se non si desidera l'utente sia in grado di selezionare gli elementi.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Selezione di più righe

Se si vuole consentire all'utente di selezionare un più righe, fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo in Interface Builder. Selezionare la vista struttura il **gerarchia delle interfacce** e controllare il **più** casella di controllo il **attributo Inspector**:

[![](outline-view-images/select02.png "Attribute Inspector")](outline-view-images/select02.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.


Modificare quindi il `ProductOutlineDelegate.cs` file e aggiungere il metodo seguente:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

Questa operazione consentirà all'utente di selezionare ogni singola riga nella visualizzazione della struttura. Restituire `false` per il `ShouldSelectRow` per qualsiasi elemento che non si desidera l'utente sia in grado di selezionare o `false` per ogni elemento se non si desidera l'utente sia in grado di selezionare gli elementi.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Tipo per selezionare righe

Se si desidera consentire all'utente di digitare un carattere con la visualizzazione di struttura selezionata e selezionare la prima riga che contiene tale carattere, fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo in Interface Builder. Selezionare la vista struttura il **gerarchia delle interfacce** e controllare il **tipo selezionare** casella di controllo il **attributo Inspector**:

[![](outline-view-images/type01.png "Modifica il tipo di riga")](outline-view-images/type01.png#lightbox)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

A questo punto è possibile modificare il `ProductOutlineDelegate.cs` file e aggiungere il metodo seguente:

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

Il `GetNextTypeSelectMatch` metodo accetta il determinato `searchString` e restituisce l'elemento del primo `Product` che presenta tale stringa nel relativo `Title`.

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Riordinamento di colonne

Se si vuole consentire all'utente di trascinare riordinare le colonne nella visualizzazione della struttura, fare doppio clic il `Main.storyboard` file per aprirlo e modificarlo in Interface Builder. Selezionare la vista struttura il **gerarchia delle interfacce** e controllare il **riordinamento** casella di controllo il **attributo Inspector**:

[![](outline-view-images/reorder01.png "Attribute Inspector")](outline-view-images/reorder01.png#lightbox)

Se si assegna un valore per il **salvataggio automatico** proprietà e selezionare il **informazioni sulla colonna** campo, eventuali modifiche apportate al layout della tabella verranno salvate automaticamente per noi e ripristino la volta successiva che l'applicazione viene eseguito.

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

A questo punto è possibile modificare il `ProductOutlineDelegate.cs` file e aggiungere il metodo seguente:

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

Il `ShouldReorder` metodo deve restituire `true` per tutte le colonne che desidera consentire a essere riordinati in trascinare il `newColumnIndex`, altrimenti restituire `false`;

Se si esegue l'applicazione, è possibile trascinare le intestazioni di colonna per riordinare le colonne:

[![](outline-view-images/reorder02.png "Esempio di riordinamento colonne")](outline-view-images/reorder02.png#lightbox)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>La modifica delle celle

Se si vuole consentire all'utente di modificare i valori per una cella specificata, modificare il `ProductOutlineDelegate.cs` file e modificare il `GetViewForItem` metodo come indicato di seguito:

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

Ora se si esegue l'applicazione, l'utente può modificare le celle nella visualizzazione della tabella:

[![](outline-view-images/editing01.png "Un esempio di modifica di celle")](outline-view-images/editing01.png#lightbox)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>Usare le immagini nelle visualizzazioni di struttura

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

Per altre informazioni, vedere la [usando immagini con la visualizzazione della struttura](~/mac/app-fundamentals/image.md) sezione del nostro [utilizzo di immagini](~/mac/app-fundamentals/image.md) documentazione.

<a name="Data_Binding_Outline_Views" />

## <a name="data-binding-outline-views"></a>Visualizzazioni della struttura di Data Binding

Utilizzando le tecniche di codifica di chiave-valore e un Data Binding nell'applicazione xamarin. Mac, è possibile ridurre la quantità di codice che è necessario scrivere e mantenere per popolare e lavorare con gli elementi dell'interfaccia utente. È anche il vantaggio di un'ulteriore separazione dei dati di backup (_modello di dati_) dal front end interfaccia utente (_Model-View-Controller_), sfociando in più facilmente gestibile, più flessibile dell'applicazione progettazione.

Dati XML di codifica (i, chiave-valore KVM) è un meccanismo per l'accesso a proprietà di un oggetto indirettamente, tramite chiavi (stringhe di formattazione speciale) per identificare le proprietà anziché accedervi tramite le variabili di istanza o metodi di accesso (`get/set`). Grazie all'implementazione chiave-valore di codifica compatibili con funzioni di accesso nell'applicazione xamarin. Mac, è possibile accedere ad altre funzionalità macOS come chiave-valore osservando (KVO), Data Binding, Core Data, le associazioni di Cocoa e scriptability.

Per altre informazioni, vedere la [associazione di dati di visualizzazione struttura](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding) sezione del nostro [Data Binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md) documentazione.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha fatto un quadro dettagliato di utilizzo delle visualizzazioni struttura in un'applicazione xamarin. Mac. Abbiamo visto i diversi tipi e si usa la visualizzazione della struttura, come creare e gestire la visualizzazione della struttura in Interface Builder di Xcode e come usare la visualizzazione della struttura nel codice c#.

## <a name="related-links"></a>Collegamenti correlati

- [MacOutlines (esempio)](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [MacImages (sample)](https://developer.xamarin.com/samples/mac/MacImages/) (MacImages - Esempio)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Table Views](~/mac/user-interface/table-view.md) (Visualizzazioni tabelle)
- [Source Lists](~/mac/user-interface/source-list.md) (Elenchi di risorse)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Linee guida dell'interfaccia umana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione a visualizzazioni della struttura](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
