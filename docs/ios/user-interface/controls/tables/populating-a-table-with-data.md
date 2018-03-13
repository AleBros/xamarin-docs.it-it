---
title: Popolamento di una tabella con dati
ms.topic: article
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: fe62b917946dda4cf669f5b15c91a5e3b596a0fc
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="populating-a-table-with-data"></a>Popolamento di una tabella con dati

Per aggiungere righe a un `UITableView` è necessario implementare un `UITableViewSource` sottoclasse ed eseguire l'override chiama i metodi che consente di visualizzare la tabella per popolare automaticamente.

Questa guida illustra:

- Creazione di sottoclassi un UITableViewSource
- Riutilizzo di cella
- Aggiunta di un indice
- Aggiunta di intestazioni e piè di pagina


<a name="Subclassing_UITableViewSource" />

## <a name="subclassing-uitableviewsource"></a>Creazione di sottoclassi UITableViewSource

Oggetto `UITableViewSource` sottoclasse viene assegnata a ogni `UITableView`. Visualizzazione della tabella esegue una query per determinare come eseguire il rendering (ad esempio, il numero di righe è necessario e l'altezza di ogni riga, se diverso da quello predefinito), la classe di origine. L'origine fornisce in particolare, ogni vista cella popolato con i dati.

Sono disponibili solo due metodi obbligatori necessari per visualizzare una tabella dati:

-   **RowsInSection** : restituito un [ `nint` ](http://developer.xamarin.com/guides/cross-platform/macios/nativetypes/) conteggio del numero totale di righe di dati della tabella deve essere visualizzato.
-   **GetCell** : restituito un `UITableCellView` popolato con i dati per l'indice di riga corrispondente passato al metodo.


Il file di esempio BasicTable **TableSource.cs** è l'implementazione più semplice di `UITableViewSource`. È possibile visualizzare nel frammento di codice riportato di seguito, che accetta una matrice di stringhe da visualizzare nella tabella e restituisce uno stile di cella predefinito che contiene ogni stringa:

```csharp
public class TableSource : UITableViewSource {

        string[] TableItems;
        string CellIdentifier = "TableCell";

        public TableSource (string[] items)
        {
            TableItems = items;
        }

        public override nint RowsInSection (UITableView tableview, nint section)
        {
            return TableItems.Length;
        }

        public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
        {
            UITableViewCell cell = tableView.DequeueReusableCell (CellIdentifier);
            string item = TableItems[indexPath.Row];

            //---- if there are no cells to reuse, create a new one
            if (cell == null)
            { cell = new UITableViewCell (UITableViewCellStyle.Default, CellIdentifier); }

            cell.TextLabel.Text = item;

            return cell;
        }
}
```

Oggetto `UITableViewSource` possibile utilizzare qualsiasi struttura di dati da una matrice di stringa semplice (come illustrato in questo esempio) per un elenco di <> o un'altra raccolta. L'implementazione di `UITableViewSource` metodi isola la tabella dalla struttura di dati sottostante.

Per usare questo tipo di sottoclasse, creare una matrice di stringhe per costruire l'origine quindi assegnarla a un'istanza di `UITableView`:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    table = new UITableView(View.Bounds); // defaults to Plain style
    string[] tableItems = new string[] {"Vegetables","Fruits","Flower Buds","Legumes","Bulbs","Tubers"};
    table.Source = new TableSource(tableItems);
    Add (table);
}
```

La tabella risultante è simile al seguente:

 [![](populating-a-table-with-data-images/image3.png "Tabella di esempio in esecuzione")](populating-a-table-with-data-images/image3.png#lightbox)

La maggior parte delle tabelle consentono all'utente di toccare una riga per selezionarla, quindi eseguire un'altra azione (ad esempio la riproduzione di un brano, la chiamata di un contatto o con un'altra schermata). A tale scopo, esistono alcuni aspetti, che è necessario eseguire. In primo luogo, creare un AlertController per visualizzare un messaggio quando l'utente fa clic su una riga, aggiungendo il comando seguente per il `RowSelected` metodo:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("Row Selected", tableItems[indexPath.Row], UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    ...

    tableView.DeselectRow (indexPath, true);
}
```

Successivamente, creare un'istanza di questo Controller di visualizzazione:

```csharp
HomeScreen owner;
```
Aggiungere un costruttore alla classe UITableViewSource che accetta un controller di visualizzazione come parametro e lo salva in un campo:

```csharp
public TableSource (string[] items, HomeScreen owner)
{
    ...
    this.owner = owner;

}
```
Modificare il metodo ViewDidLoad in cui la classe UITableViewSource viene creata per passare il `this` riferimento:

```csharp
table.Source = new TableSource(tableItems, this);
```
Infine, nuovamente il `RowSelected` metodo, chiamare `PresentViewController` al campo memorizzati nella cache:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```


A questo punto l'utente può toccare una riga e verrà visualizzato un avviso:



 [![](populating-a-table-with-data-images/image4.png "L'avviso selezionato di riga")](populating-a-table-with-data-images/image4.png#lightbox)


## <a name="cell-reuse"></a>Riutilizzo di cella

In questo esempio esistono solo sei elementi, pertanto non c'è nessun riutilizzo cella richiesto. Quando si visualizzano centinaia o migliaia di righe, tuttavia, sarebbe uno spreco di memoria per creare centinaia o migliaia di `UITableViewCell` oggetti quando solo poche visualizzati sullo schermo contemporaneamente.

Per evitare questa situazione, quando una cella viene rimosso dallo schermo che la visualizzazione viene inserita in una coda per il riutilizzo. Quando l'utente scorre, la tabella chiama `GetCell` per richiedere le nuove visualizzazioni da visualizzare: consente di riutilizzare una cella esistente (che non visualizzata) è sufficiente chiamare il `DequeueReusableCell` metodo. Se una cella è disponibile per il riutilizzo, che verrà restituito, in caso contrario viene restituito un valore null e il codice è necessario creare una nuova istanza della cella.

Questo frammento di codice dell'esempio viene illustrato il modello:

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

Il `cellIdentifier` crea in modo efficace le code separate per diversi tipi di cella. In questo esempio tutte le celle esaminare l'impostazione hardcoded pertanto solo uno stesso identificatore viene usato. Se sono disponibili diversi tipi di cella deve ogni hanno una stringa di identificazione diversi, sia quando l'istanza viene creata e quando vengono richiesti dalla coda di riutilizzo.

### <a name="cell-reuse-in-ios-6"></a>Riutilizzo di cella in iOS 6 +

iOS 6 aggiunto un modello di riutilizzo cella Analogamente all'introduzione di uno con le visualizzazioni di raccolta. Anche se il criterio esistente riutilizzo indicato in precedenza è ancora supportato per compatibilità, questo nuovo modello con le versioni precedenti è preferibile perché elimina la necessità per il controllo null per la cella.

Con il nuovo modello di un'applicazione registra la classe di cella o XI utilizzabile da una chiamata a `RegisterClassForCellReuse` o `RegisterNibForCellReuse` nel costruttore del controller. Quando la cella di annullamento dell'accodamento nel `GetCell` metodo semplicemente chiamare `DequeueReusableCell` passando l'identificatore è stato registrato per la classe di cella o XI e il percorso di indice.

Ad esempio, il codice seguente registra una classe di cella personalizzato in un UITableViewController:

```csharp
public class MyTableViewController : UITableViewController
{
  static NSString MyCellId = new NSString ("MyCellId");

  public MyTableViewController ()
  {
    TableView.RegisterClassForCellReuse (typeof(MyCell), MyCellId);
  }
  ...
}
```

Con la classe MyCell registrata, la cella può essere rimosso dalla coda nel `GetCell` metodo il `UITableViewSource` senza la necessità per il controllo aggiuntivo null, come mostrato di seguito:

```csharp
class MyTableSource : UITableViewSource
{
  public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
  {
    // if cell is not available in reuse pool, iOS will create one automatically
    // no need to do null check and create cell manually
    var cell = (MyCell) tableView.DequeueReusableCell (MyCellId, indexPath);

    // do whatever you need to with cell, such as assigning properties, etc.

    return cell;
  }
}
```

Tenere presente, quando si utilizza il nuovo modello di riutilizzo con una classe di cella personalizzato, è necessario implementare il costruttore che accetta un `IntPtr`, come illustrato nel frammento riportato di seguito, in caso contrario Objective-C non sarà possibile creare un'istanza della classe di cella:

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

È possibile visualizzare esempi degli argomenti illustrati in precedenza nel **BasicTable** esempio collegato a questo articolo.

<a name="Adding_an_Index" />

## <a name="adding-an-index"></a>Aggiunta di un indice

Un indice consente all'utente di scorrere lunghi elenchi, in genere ordinati alfabeticamente anche se è possibile indicizzare da qualsiasi criterio desiderato. Il **BasicTableIndex** esempio carica un elenco molto più lungo di elementi da un file per dimostrare l'indice. Ogni elemento in corrispondenza dell'indice corrisponde a una sezione della tabella.

 [![](populating-a-table-with-data-images/image5.png "La visualizzazione dell'indice")](populating-a-table-with-data-images/image5.png#lightbox)

Per supportare i dati sottostanti la tabella devono essere raggruppati, quindi crea il codice di esempio BasicTableIndex ' sections' un `Dictionary<>` dalla matrice di stringhe tramite la prima lettera di ogni elemento come chiave del dizionario:

```csharp
indexedTableItems = new Dictionary<string, List<string>>();
foreach (var t in items) {
    if (indexedTableItems.ContainsKey (t[0].ToString ())) {
        indexedTableItems[t[0].ToString ()].Add(t);
    } else {
        indexedTableItems.Add (t[0].ToString (), new List<string>() {t});
    }
}
keys = indexedTableItems.Keys.ToArray ();
```

Il `UITableViewSource` sottoclasse deve quindi i metodi seguenti aggiunto o modificato per l'utilizzo di `Dictionary<>` :

-   **NumberOfSections** : questo metodo è facoltativo, per impostazione predefinita, la tabella si presuppone una sezione. La visualizzazione di un indice questo metodo deve restituire il numero di elementi nell'indice (ad esempio, 26 se l'indice contiene tutte le lettere dell'alfabeto inglese).
-   **RowsInSection** : restituisce il numero di righe in una sezione specificata.
-   **SectionIndexTitles** -restituisce la matrice di stringhe che verrà utilizzato per visualizzare l'indice. Il codice di esempio restituisce una matrice di lettere.


I metodi aggiornati nel file di esempio **BasicTableIndex/TableSource.cs** simile al seguente:

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return keys.Length;
}
public override nint RowsInSection (UITableView tableview, nint section)
{
    return indexedTableItems[keys[section]].Count;
}
public override string[] SectionIndexTitles (UITableView tableView)
{
    return keys;
}
```

Gli indici sono in genere utilizzati solo con lo stile di tabella normale.


<a name="Adding_Headers_and_Footers" />

## <a name="adding-headers-and-footers"></a>Aggiunta di intestazioni e piè di pagina

Per raggruppare visivamente le righe in una tabella è possibile utilizzare le intestazioni e piè di pagina. La struttura di dati richiesta è molto simile all'aggiunta di un indice: un `Dictionary<>` funziona particolarmente bene. Anziché utilizzare l'alfabeto per raggruppare le celle, in questo esempio raggruppa il verdura dal tipo botanico.
L'output sarà analogo al seguente:

 [![](populating-a-table-with-data-images/image6.png "Esempio intestazioni e piè di pagina")](populating-a-table-with-data-images/image6.png#lightbox)

Per visualizzare le intestazioni e piè di pagina il `UITableViewSource` sottoclasse richiede questi metodi aggiuntivi:

-   **TitleForHeader** : restituisce il testo da utilizzare come intestazione
-   **TitleForFooter** : restituisce il testo da utilizzare come piè di pagina.


I metodi aggiornati nel file di esempio **BasicTableHeaderFooter/Code/TableSource.cs** simile al seguente:

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    return keys[section];
}
public override string TitleForFooter (UITableView tableView, nint section)
{
    return indexedTableItems[keys[section]].Count + " items";
}
```

È possibile personalizzare ulteriormente l'aspetto dell'intestazione e piè di pagina con una visualizzazione dell'oggetto, utilizzando il `GetViewForHeader` e `GetViewForFooter` metodo esegue l'override `UITableViewSource`.


## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithTables (esempio)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
