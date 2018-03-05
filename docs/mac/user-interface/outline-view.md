---
title: Visualizzazioni della struttura
description: In questo articolo viene descritto l'utilizzo con visualizzazioni della struttura in un'applicazione Xamarin.Mac. Descrive la creazione e la gestione delle visualizzazioni della struttura in Xcode e il generatore di interfaccia e utilizzarli a livello di codice.
ms.topic: article
ms.prod: xamarin
ms.assetid: 675B9405-D9A7-49F0-94AD-417F10A71D11
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: a125c2991c9b8c2453fad396c1d0baebe10be015
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="outline-views"></a>Visualizzazioni della struttura

_In questo articolo viene descritto l'utilizzo con visualizzazioni della struttura in un'applicazione Xamarin.Mac. Descrive la creazione e la gestione delle visualizzazioni della struttura in Xcode e il generatore di interfaccia e utilizzarli a livello di codice._

Quando si utilizza c# e .NET in un'applicazione Xamarin.Mac, è possibile accedere alla stessa struttura viste che uno sviluppatore che lavora *Objective-C* e *Xcode* does. Poiché Xamarin.Mac si integra direttamente con Xcode, è possibile utilizzare del Xcode _generatore interfaccia_ per creare e gestire le visualizzazioni della struttura (o, facoltativamente, crearli direttamente nel codice c#).

Una visualizzazione struttura è un tipo di tabella che consente all'utente di espandere o comprimere le righe di dati gerarchici. Come una visualizzazione tabella, una visualizzazione della struttura sono visualizzati i dati per un set di elementi correlati, le righe che rappresentano singoli elementi e le colonne che rappresentano gli attributi di tali elementi. A differenza di una visualizzazione tabella, gli elementi in una visualizzazione struttura non in un elenco semplice, in cui sono organizzati in una gerarchia, ad esempio file e cartelle su un disco rigido.

[ ![](outline-view-images/populate03.png "Eseguire un'app di esempio")](outline-view-images/populate03.png)

In questo articolo verranno descritte le nozioni di base dell'utilizzo di visualizzazioni della struttura in un'applicazione Xamarin.Mac. È altamente consigliabile che il [Hello, Mac](~/mac/get-started/hello-mac.md) articolo prima di tutto, in particolare il [Introduzione a Xcode e interfaccia generatore](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder) e [punti vendita e le azioni](~/mac/get-started/hello-mac.md#Outlets_and_Actions) le sezioni, come illustra i concetti chiave e le tecniche che verrà usato in questo articolo.

Si consiglia di esaminare il [c# esposizione di classi / metodi per Objective-C](~/mac/internals/how-it-works.md) sezione del [Xamarin.Mac Internals](~/mac/internals/how-it-works.md) del documento, nonché viene descritto il `Register` e `Export` comandi utilizzato per le classi c# in transito-fino a oggetti Objective-C e gli elementi dell'interfaccia utente.

<a name="Introduction_to_Outline_Views" />

## <a name="introduction-to-outline-views"></a>Introduzione alle visualizzazioni della struttura

Una visualizzazione struttura è un tipo di tabella che consente all'utente di espandere o comprimere le righe di dati gerarchici. Come una visualizzazione tabella, una visualizzazione della struttura sono visualizzati i dati per un set di elementi correlati, le righe che rappresentano singoli elementi e le colonne che rappresentano gli attributi di tali elementi. A differenza di una visualizzazione tabella, gli elementi in una visualizzazione struttura non in un elenco semplice, in cui sono organizzati in una gerarchia, ad esempio file e cartelle su un disco rigido.

Se un elemento in una visualizzazione struttura contiene altri elementi, possono essere espansi o compressi dall'utente. Un elemento espandibile viene visualizzato un triangolo, che fa riferimento a destra quando l'elemento è compresso e punta verso il basso quando l'elemento è espanso. Facendo clic sul triangolo divulgazione, l'elemento espandere o comprimere.

La visualizzazione struttura (`NSOutlineView`) è una sottoclasse della visualizzazione della tabella (`NSTableView`) e di conseguenza, gran parte del relativo comportamento eredita dalla classe padre. Di conseguenza, molte operazioni supportate da una visualizzazione tabella, ad esempio la selezione di righe o colonne, riposizionamento delle colonne trascinando le intestazioni di colonna e così via, sono supportate anche da una visualizzazione struttura. Un'applicazione Xamarin.Mac ha il controllo di queste funzionalità e configura parametri della visualizzazione della struttura (sia nel codice o interfaccia generatore) per consentire o impedire determinate operazioni.

Una visualizzazione della struttura non archivia i propri dati, ma basa su un'origine dati (`NSOutlineViewDataSource`) per fornire le righe e colonne necessarie, in una base alla necessità.

Comportamento di una visualizzazione struttura può essere personalizzato, fornendo una sottoclasse del delegato di visualizzazione di struttura (`NSOutlineViewDelegate`) per supportare la gestione di colonna di struttura, digitare per selezionare funzionalità, la selezione della riga e la modifica, rilevamento personalizzato e visualizzazioni personalizzate per singoli le colonne e righe.

Poiché una visualizzazione struttura condivide molte delle funzionalità e il comportamento con una visualizzazione tabella, è possibile passare attraverso il nostro [viste delle tabelle](~/mac/user-interface/table-view.md) documentazione prima di continuare con questo articolo.

<a name="Creating_and_Maintaining_Outline_Views_in_Xcode" />

## <a name="creating-and-maintaining-outline-views-in-xcode"></a>Creazione e gestione delle visualizzazioni della struttura in Xcode

Quando si crea una nuova applicazione Xamarin.Mac Cocoa, per impostazione predefinita è ottenere una finestra vuota, standard. Questo windows è definito in un `.storyboard` file automaticamente incluso nel progetto. Per modificare la progettazione di windows, nel **Esplora**, fare doppio clic il `Main.storyboard` file:

[ ![](outline-view-images/edit01.png "Selezione storyboard principale")](outline-view-images/edit01.png)

La progettazione della finestra verrà aperta in Generatore del Xcode di interfaccia:

[ ![](outline-view-images/edit02.png "Modifica l'interfaccia utente in Xcode")](outline-view-images/edit02.png)

Tipo `outline` nel **libreria Ispettore** casella di ricerca per renderne più semplice trovare i controlli struttura:

[ ![](outline-view-images/edit03.png "Selezione di una visualizzazione struttura dalla libreria")](outline-view-images/edit03.png)

Trascinare il Controller di visualizzazione di una visualizzazione di **Editor dell'interfaccia**, renderlo riempire l'area del contenuto del Controller di visualizzazione e impostarlo su dove compatta e aumenta con la finestra nel **Editor vincoli di**:

[ ![](outline-view-images/edit04.png "Modifica di vincoli")](outline-view-images/edit04.png)

Selezionare la vista struttura il **interfaccia gerarchia** e le proprietà seguenti sono disponibili nel **controllo attributo**:

[ ![](outline-view-images/edit05.png "Il controllo di attributo")](outline-view-images/edit05.png)

- **Colonna di struttura** -la colonna della tabella in cui i dati gerarchici vengono visualizzati.
- **Colonna struttura di salvataggio automatico** - se `true`, la colonna di struttura verrà automaticamente salvata e ripristinata tra l'esecuzione dell'applicazione.
- **Rientro** -la quantità per il rientro delle colonne in un elemento espanso.
- **Rientro segue celle** - se `true`, il contrassegno di rientro verrà aumentato il rientro insieme le celle.
- **Salvataggio automatico espanso elementi** - se `true`, lo stato espanso o compresso degli elementi verrà automaticamente salvato e ripristinato tra l'esecuzione dell'applicazione.
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
> A meno che non si esegue la manutenzione di un'applicazione legacy Xamarin.Mac, `NSView` base visualizzazioni della struttura deve essere utilizzate su `NSCell` viste delle tabelle di base. `NSCell` viene considerato legacy e potrebbe non essere supportato in futuro.

Selezionare una colonna della tabella nel **interfaccia gerarchia** e le proprietà seguenti sono disponibili nel **controllo attributo**:

[ ![](outline-view-images/edit06.png "Il controllo di attributo")](outline-view-images/edit06.png)

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

[ ![](outline-view-images/edit07.png "Il controllo di attributo")](outline-view-images/edit07.png)

Si tratta di tutte le proprietà di una vista standard. È inoltre l'opzione di ridimensionamento delle righe per questa colonna qui.

Selezionare una cella di visualizzazione della tabella (per impostazione predefinita, si tratta di un `NSTextField`) nei **interfaccia gerarchia** e le proprietà seguenti sono disponibili nel **attributo controllo**:

[ ![](outline-view-images/edit08.png "Il controllo di attributo")](outline-view-images/edit08.png)

È necessario che tutte le proprietà di un campo di testo standard per impostare qui. Per impostazione predefinita, un campo di testo standard viene utilizzato per visualizzare i dati per una cella in una colonna.

Selezionare una visualizzazione di cella di tabella (`NSTableFieldCell`) nel **interfaccia gerarchia** e le proprietà seguenti sono disponibili nel **controllo attributo**:

[ ![](outline-view-images/edit09.png "Il controllo di attributo")](outline-view-images/edit09.png)

Le impostazioni di più importanti sono:

- **Layout** - selezionare la modalità di celle in questa colonna vengono disposti.
- **Utilizza la modalità a riga singola** - se `true`, la cella è limitata a una singola riga.
- **Larghezza del Layout di Runtime prima** - se `true`, la cella preferiranno alla larghezza impostata per tale (automaticamente o manualmente) quando viene visualizzato alla prima esecuzione dell'applicazione.
- **Azione** -controlla se la modifica **azione** viene inviato per la cella.
- **Comportamento** -definisce se una cella è modificabile o selezionabile.
- **Rich Text** - se `true`, la cella può visualizzare il testo formattato e stile.
- **Annulla** - se `true`, la cella si assume la responsabilità per è il comportamento di annullamento.

Selezionare la visualizzazione di cella di tabella (`NSTableFieldCell`) nella parte inferiore di una colonna di tabella nel **interfaccia gerarchia**:

[ ![](outline-view-images/edit11.png "Selezione di visualizzazione della cella di tabella")](outline-view-images/edit10.png)

Ciò consente di modificare la visualizzazione di cella di tabella usata come base _modello_ per tutte le celle create per la colonna specificata.

<a name="Adding_Actions_and_Outlets" />

### <a name="adding-actions-and-outlets"></a>Aggiunta di azioni e prese

È sufficiente come qualsiasi altro controllo dell'interfaccia utente Cocoa, è necessario esporre la visualizzazione struttura e si tratta di colonne e le celle all'utilizzo di codice c# **azioni** e **prese** (basati sulle funzionalità necessarie).

Il processo è lo stesso per qualsiasi elemento di struttura che si desidera esporre:

1. Passare al **Assistente Editor** e assicurarsi che il `ViewController.h` è selezionato il file: 

    [ ![](outline-view-images/edit11.png "Selezionare il file corretto. h")](outline-view-images/edit11.png)
2. Selezionare la visualizzazione della struttura dal **interfaccia gerarchia**, CTRL + clic e trascinare il `ViewController.h` file.
3. Creare un **presa** per la visualizzazione struttura chiamata `ProductOutline`: 

    [ ![](outline-view-images/edit13.png "Configurazione di una presa di corrente")](outline-view-images/edit13.png)
4. Creare **prese** per le colonne di tabelle anche chiamato `ProductColumn` e `DetailsColumn`: 

    [ ![](outline-view-images/edit14.png "Configurazione di una presa di corrente")](outline-view-images/edit14.png)
5. Per salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Successivamente, si verranno scritto la visualizzazione di codice alcuni dati per la struttura quando viene eseguita l'applicazione.

<a name="Populating_the_Table_View" />

## <a name="populating-the-outline-view"></a>Popolare la visualizzazione struttura

Con la visualizzazione struttura progettata in Generatore di interfaccia e vengono esposte tramite un **presa**, successivamente è necessario creare il codice c# per compilarlo.

Innanzitutto, creare un nuovo `Product` classe per contenere le informazioni per le singole righe e gruppi di prodotti sub. Nel **Esplora**, fare clic sul progetto e selezionare **Aggiungi** > **nuovo File...** Selezionare **generale** > **classe vuota**, immettere `Product` per il **nome** e fare clic su di **New** pulsante:

[ ![](outline-view-images/populate01.png "Creazione di una classe vuota")](outline-view-images/populate01.png)

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

Successivamente, è necessario creare una sottoclasse di `NSOutlineDataSource` per fornire i dati per la struttura come richiesto. Nel **Esplora**, fare clic sul progetto e selezionare **Aggiungi** > **nuovo File...** Selezionare **generale** > **classe vuota**, immettere `ProductOutlineDataSource` per il **nome** e fare clic su di **New** pulsante.

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

Questa classe dispone di spazio di archiviazione per gli elementi della visualizzazione la struttura e sostituzioni di `GetChildrenCount` per restituire il numero di righe nella tabella. Il `GetChild` restituisce un elemento padre o figlio specifico (come richiesto per la visualizzazione struttura) e `ItemExpandable` definisce l'elemento specificato come un elemento padre o figlio.

Infine, è necessario creare una sottoclasse di `NSOutlineDelegate` per fornire il comportamento per la struttura. Nel **Esplora**, fare clic sul progetto e selezionare **Aggiungi** > **nuovo File...** Selezionare **generale** > **classe vuota**, immettere `ProductOutlineDelegate` per il **nome** e fare clic su di **New** pulsante.

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

Quando si crea un'istanza di `ProductOutlineDelegate`, viene inoltre passato in un'istanza del `ProductOutlineDataSource` che fornisce i dati per la struttura. Il `GetView` metodo è responsabile della restituzione di una vista (dati) per visualizzare la cella per una colonna e riga. Se possibile, una vista esistente verrà riutilizzata per visualizzare la cella, se non è necessario creare una nuova vista.

Per popolare la struttura, modifica il `MainWindow.cs` file e verificare il `AwakeFromNib` metodo aspetto simile al seguente:

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

Se si esegue l'applicazione, viene visualizzato il seguente:

[ ![](outline-view-images/populate02.png "La visualizzazione compressa")](outline-view-images/populate02.png)

Se si espande un nodo nella visualizzazione struttura, sarà simile al seguente:

[ ![](outline-view-images/populate03.png "La visualizzazione estesa")](outline-view-images/populate03.png)

<a name="Sorting_by_Column" />

## <a name="sorting-by-column"></a>Ordinamento per colonna

Di seguito consente all'utente di ordinare i dati nella struttura facendo clic su un'intestazione di colonna. In primo luogo, fare doppio clic su di `Main.storyboard` file per aprirlo e modificarlo in Generatore di interfaccia. Selezionare il `Product` colonna, immettere `Title` per il **chiave di ordinamento**, `compare:` per il **selettore** e selezionare `Ascending` per il **ordine**:

[ ![](outline-view-images/sort01.png "Impostazione dell'ordine di chiave di ordinamento")](outline-view-images/sort01.png)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Ora modifica il `ProductOutlineDataSource.cs` file e aggiungere i metodi seguenti:

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

Il `Sort` metodo consente di ordinare i dati nell'origine dati in base a un determinato `Product` campo della classe in ordine crescente o decrescente. Sottoposto a override `SortDescriptorsChanged` metodo verrà chiamato ogni volta che l'utilizzo fa clic sull'intestazione di colonna. Verrà passato il **chiave** valore impostato nel generatore di interfaccia e l'ordinamento per colonna.

Se si esegue l'applicazione e fare clic su nelle intestazioni di colonna, le righe verranno ordinate in base alla colonna:

[ ![](outline-view-images/sort02.png "Esempio di output ordinato")](outline-view-images/sort02.png)

<a name="Row_Selection" />

## <a name="row-selection"></a>Selezione di riga

Se si desidera consentire all'utente di selezionare una singola riga, fare doppio clic su di `Main.storyboard` file per aprirlo e modificarlo in Generatore di interfaccia. Selezionare la vista struttura il **interfaccia gerarchia** e deselezionare il **più** nella casella di controllo di **attributo controllo**:

[ ![](outline-view-images/select01.png "Il controllo di attributo")](outline-view-images/select01.png)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.


Successivamente, modificare il `ProductOutlineDelegate.cs` file e aggiungere il metodo seguente:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

In questo modo sarà possibile selezionare ogni singola riga nella visualizzazione struttura. Restituire `false` per il `ShouldSelectItem` per un elemento che non si desidera l'utente sia in grado di selezionare o `false` per ogni elemento se non si desidera essere in grado di selezionare gli elementi all'utente.

<a name="Multiple_Row_Selection" />

## <a name="multiple-row-selection"></a>Selezione di più righe

Se si desidera consentire all'utente di selezionare un più righe, fare doppio clic su di `Main.storyboard` file per aprirlo e modificarlo in Generatore di interfaccia. Selezionare la vista struttura il **interfaccia gerarchia** e controllare il **più** nella casella di controllo di **attributo controllo**:

[ ![](outline-view-images/select02.png "Il controllo di attributo")](outline-view-images/select02.png)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.


Successivamente, modificare il `ProductOutlineDelegate.cs` file e aggiungere il metodo seguente:

```csharp
public override bool ShouldSelectItem (NSOutlineView outlineView, NSObject item)
{
    // Don't select product groups
    return !((Product)item).IsProductGroup;
}
```

In questo modo sarà possibile selezionare ogni singola riga nella visualizzazione struttura. Restituire `false` per il `ShouldSelectRow` per un elemento che non si desidera l'utente sia in grado di selezionare o `false` per ogni elemento se non si desidera essere in grado di selezionare gli elementi all'utente.

<a name="Type_to_Select_Row" />

## <a name="type-to-select-row"></a>Tipo per selezionare una riga

Se si desidera consentire all'utente di digitare un carattere con la visualizzazione della struttura selezionato e selezionare la prima riga con tale carattere, fare doppio clic su di `Main.storyboard` file per aprirlo e modificarlo in Generatore di interfaccia. Selezionare la vista struttura il **interfaccia gerarchia** e controllare il **tipo selezionare** nella casella di controllo di **attributo controllo**:

[ ![](outline-view-images/type01.png "Modifica il tipo di riga")](outline-view-images/type01.png)

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Ora modifica il `ProductOutlineDelegate.cs` file e aggiungere il metodo seguente:

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

Il `GetNextTypeSelectMatch` metodo accetta il determinato `searchString` e restituisce l'elemento del primo `Product` che contiene la stringa del `Title`.

<a name="Reordering_Columns" />

## <a name="reordering-columns"></a>Riordinamento di colonne

Se si desidera consentire all'utente di trascinare riordinare le colonne nella visualizzazione struttura, fare doppio clic su di `Main.storyboard` file per aprirlo e modificarlo in Generatore di interfaccia. Selezionare la vista struttura il **interfaccia gerarchia** e controllare il **riordinamento** nella casella di controllo di **attributo controllo**:

[ ![](outline-view-images/reorder01.png "Il controllo di attributo")](outline-view-images/reorder01.png)

Se si assegna un valore per il **salvataggio** proprietà e selezionare il **informazioni sulla colonna** campo, eventuali modifiche apportate al layout della tabella verranno salvate automaticamente a Microsoft e ripristino la volta successiva che l'applicazione viene eseguito.

Salvare le modifiche e tornare a Visual Studio per Mac per la sincronizzazione con Xcode.

Ora modifica il `ProductOutlineDelegate.cs` file e aggiungere il metodo seguente:

```csharp
public override bool ShouldReorder (NSOutlineView outlineView, nint columnIndex, nint newColumnIndex)
{
    return true;
}
```

Il `ShouldReorder` metodo dovrebbe restituire `true` per qualsiasi colonna che desidera consentire a essere trascinamento riordinati nel `newColumnIndex`, altrimenti restituire `false`;

Se si esegue l'applicazione, è possibile trascinare le intestazioni di colonna intorno riordinamento delle colonne:

[ ![](outline-view-images/reorder02.png "Esempio di riordinamento di colonne")](outline-view-images/reorder02.png)

<a name="Editing_Cells" />

## <a name="editing-cells"></a>La modifica delle celle

Se si desidera consentire all'utente di modificare i valori per una cella specificata, modificare il `ProductOutlineDelegate.cs` file e modificare il `GetViewForItem` metodo come indicato di seguito:

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

Ora se si esegue l'applicazione, l'utente può modificare le celle nella visualizzazione tabella:

[ ![](outline-view-images/editing01.png "Un esempio di modifica delle celle")](outline-view-images/editing01.png)

<a name="Using_Images_in_Outline_Views" />

## <a name="using-images-in-outline-views"></a>Utilizzo delle immagini in visualizzazioni della struttura

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

Per ulteriori informazioni, vedere il [con immagini con visualizzazioni della struttura](~/mac/app-fundamentals/image.md) sezione del nostro [utilizzo immagine](~/mac/app-fundamentals/image.md) documentazione.

<a name="Data_Binding_Outline_Views" />

## <a name="data-binding-outline-views"></a>Visualizzazioni della struttura di associazione dati

Utilizzando le tecniche di associazione di dati e la generazione di codice chiave-valore Xamarin.Mac nell'applicazione in uso, è possibile ridurre notevolmente la quantità di codice che è necessario scrivere e mantenere per popolare e lavorare con gli elementi dell'interfaccia utente. È inoltre il vantaggio di separazione ulteriormente i dati di backup (_modello di dati_) la parte anteriore terminare l'interfaccia utente (_Model-View-Controller_), i punti iniziali per più semplici da gestire, più flessibile dell'applicazione progettazione.

Dati XML di codifica (i, chiave-valore KVM) è un meccanismo per l'accesso a proprietà di un oggetto indirettamente, utilizzando le chiavi (stringhe di formattazione speciale) per identificare le proprietà anziché accedervi tramite le variabili di istanza o metodi di accesso (`get/set`). Implementando chiave-valore di codifica compatibile con funzioni di accesso nell'applicazione Xamarin.Mac, è possibile accedere ad altre funzionalità macOS come chiave-valore osservazione (KVO), Data Binding, i dati di base, associazioni Cocoa e scriptability.

Per ulteriori informazioni, vedere il [struttura Visualizza Data Binding](~/mac/app-fundamentals/databinding.md#Outline_View_Data_Binding) sezione del nostro [Data Binding e la generazione di codice chiave-valore](~/mac/app-fundamentals/databinding.md) documentazione.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è stato applicato l'utilizzo di visualizzazioni della struttura in un'applicazione Xamarin.Mac un'analisi approfondita. È stato illustrato i diversi tipi e sugli usi di visualizzazioni della struttura, come creare e gestire visualizzazioni della struttura in interfaccia generatore del Xcode e come utilizzare le visualizzazioni struttura nel codice c#.

## <a name="related-links"></a>Collegamenti correlati

- [MacOutlines (esempio)](https://developer.xamarin.com/samples/mac/MacOutlines/)
- [MacImages (esempio)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Table Views](~/mac/user-interface/table-view.md) (Visualizzazioni tabelle)
- [Source Lists](~/mac/user-interface/source-list.md) (Elenchi di risorse)
- [Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)
- [Linee guida sull'interfaccia umane di OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introduzione alle visualizzazioni della struttura](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/OutlineView/OutlineView.html#//apple_ref/doc/uid/10000023i)
- [NSOutlineView](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/index.html#//apple_ref/doc/uid/TP40004079)
- [NSOutlineViewDataSource](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40004175)
- [NSOutlineViewDelegate](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/NSOutlineViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40008609)
