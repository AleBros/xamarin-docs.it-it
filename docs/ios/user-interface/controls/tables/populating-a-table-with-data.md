---
title: Popolamento di una tabella con i dati in xamarin. IOS
description: Questo documento descrive come popolare una tabella con dati in un'applicazione xamarin. IOS. Vengono illustrati UITableViewSource, riutilizzo delle celle, aggiunta di un indice e le intestazioni e piè di pagina.
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 859afcf6ab9f3acfb56104fa68683ba28d913ce4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117137"
---
# <a name="populating-a-table-with-data-in-xamarinios"></a>Popolamento di una tabella con i dati in xamarin. IOS

Per aggiungere righe a un `UITableView` è necessario implementare un `UITableViewSource` sottoclasse ed eseguire l'override di metodi che consente di visualizzare la tabella le chiamate a popolarsi.

Questa guida illustra:

- Creazione di una sottoclasse un UITableViewSource
- Riutilizzo delle celle
- Aggiunta di un indice
- Aggiunta di intestazioni e piè di pagina


<a name="Subclassing_UITableViewSource" />

## <a name="subclassing-uitableviewsource"></a>Creazione di una sottoclasse UITableViewSource

Oggetto `UITableViewSource` sottoclasse viene assegnata a ogni `UITableView`. Visualizzazione della tabella esegue una query sulla classe di origine per stabilire come eseguire il rendering di se stessa (ad esempio, il numero di righe è necessario e l'altezza di ogni riga, se diverso da quello predefinito). L'origine fornisce ancora più importante, ogni vista cella popolato con i dati.

Sono disponibili solo due i metodi obbligatori necessari per creare una tabella di visualizzare i dati:

-   **RowsInSection** – restituito un' [ `nint` ](http://developer.xamarin.com/guides/cross-platform/macios/nativetypes/) conteggio del numero totale di righe di dati deve visualizzare la tabella.
-   **GetCell** – restituito un `UITableCellView` popolata con i dati per l'indice di riga corrispondente passato al metodo.


Il file di esempio BasicTable **TableSource.cs** è l'implementazione più semplice di `UITableViewSource`. È possibile visualizzare nel frammento di codice seguente che accetta una matrice di stringhe da visualizzare nella tabella e che restituisca uno stile di cella predefinito che contiene ogni stringa:

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

Oggetto `UITableViewSource` può usare qualsiasi struttura di dati, da una matrice di stringa semplice (come illustrato in questo esempio) per un elenco <> o un'altra raccolta. L'implementazione di `UITableViewSource` metodi isola la tabella dalla struttura di dati sottostante.

Per usare questa sottoclasse, creare una matrice di stringhe per costruire l'origine quindi assegnarla a un'istanza di `UITableView`:

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

La tabella risultante è simile alla seguente:

 [![](populating-a-table-with-data-images/image3.png "Tabella di esempio in esecuzione")](populating-a-table-with-data-images/image3.png#lightbox)

La maggior parte delle tabelle consentono all'utente di toccare una riga per selezionarla, quindi eseguire un'altra azione (ad esempio la riproduzione di un brano, la chiamata a un contatto o visualizzazione di un'altra schermata). A tale scopo, esistono alcuni aspetti che dobbiamo fare. In primo luogo, creiamo un AlertController per visualizzare un messaggio quando l'utente fa clic su una riga aggiungendo il codice seguente il `RowSelected` metodo:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("Row Selected", tableItems[indexPath.Row], UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    ...

    tableView.DeselectRow (indexPath, true);
}
```

Successivamente, creare un'istanza del Controller visualizzazione:

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
Modificare il metodo ViewDidLoad in cui viene creata la classe UITableViewSource per passare il `this` riferimento:

```csharp
table.Source = new TableSource(tableItems, this);
```
Infine di nuovo la `RowSelected` metodo, chiamare `PresentViewController` sul campo memorizzato nella cache:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```


A questo punto l'utente può toccare una riga e viene visualizzato un avviso:



 [![](populating-a-table-with-data-images/image4.png "L'avviso selezionato di righe")](populating-a-table-with-data-images/image4.png#lightbox)


## <a name="cell-reuse"></a>Riutilizzo delle celle

In questo esempio esistono solo sei elementi, pertanto non presenta alcun riutilizzo delle celle necessario. Quando si visualizzano centinaia o migliaia di righe, tuttavia, sarebbe uno spreco di memoria creare centinaia o migliaia di `UITableViewCell` oggetti quando solo alcuni rientrano nella schermata alla volta.

Per evitare questa situazione, quando una cella viene rimossa dallo schermo che la visualizzazione viene inserita in una coda per il riutilizzo. Mentre l'utente scorre, la tabella chiama `GetCell` per richiedere nuove visualizzazioni da visualizzare: consente di riutilizzare una cella esistente (che non è attualmente in corso visibile) è sufficiente chiamare il `DequeueReusableCell` (metodo). Se è disponibile per il riutilizzo che verrà restituito una cella, in caso contrario, viene restituito null e il codice deve creare una nuova istanza di cella.

Questo frammento di codice dell'esempio viene illustrato il modello:

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

Il `cellIdentifier` crea in modo efficace code separate per i diversi tipi di cella. In questo esempio tutte le celle Cerca la stessa hardcoded così solo un identificatore viene usato. Se si sono verificati diversi tipi di cella ciascuno deve avere una stringa identificatore differente, quando le relative istanze vengono create sia quando vengono richiesti dalla coda di riutilizzo.

### <a name="cell-reuse-in-ios-6"></a>Riutilizzo di cella in iOS 6 +

iOS 6 aggiunto uno schema di riutilizzo cella simile all'introduzione di uno con le visualizzazioni di raccolta. Anche se il modello esistente di riutilizzo illustrato in precedenza è ancora supportato per compatibilità, questo nuovo modello con le versioni precedenti è preferibile perché elimina la necessità per il controllo dei valori null nella cella.

Con il nuovo modello di un'applicazione registra la classe di cella o un file xib da usare tramite la chiamata a `RegisterClassForCellReuse` o `RegisterNibForCellReuse` nel costruttore del controller. Quindi, quando rimozione della cella nel `GetCell` metodo, semplicemente chiamare `DequeueReusableCell` passando l'identificatore è registrato per la classe di cella o file xib e il percorso di indice.

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

Con la classe MyCell registrata, la cella può essere rimosso dalla coda nella `GetCell` metodo di `UITableViewSource` senza la necessità per il controllo null aggiuntivo, come mostrato di seguito:

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

Tenere presente, quando si usa il nuovo modello di riutilizzare una classe di cella personalizzato, è necessario implementare il costruttore che accetta un `IntPtr`, come illustrato nel frammento di codice riportato di seguito, in caso contrario, Objective-C sarà in grado di costruire un'istanza della classe di cella:

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

È possibile visualizzare esempi degli argomenti illustrati in precedenza nel **BasicTable** esempio collegati a questo articolo.

<a name="Adding_an_Index" />

## <a name="adding-an-index"></a>Aggiunta di un indice

Un indice consente all'utente di scorrere tra lunghi elenchi, in genere ordinati alfabeticamente in anche se è possibile indicizzare da qualsiasi criterio desiderato. Il **BasicTableIndex** esempio carica un elenco molto più lungo di elementi da un file per illustrare l'indice. Ogni elemento in corrispondenza dell'indice corrisponde a una sezione della tabella.

 [![](populating-a-table-with-data-images/image5.png "La visualizzazione dell'indice")](populating-a-table-with-data-images/image5.png#lightbox)

Per supportare 'sections' i dati sottostanti nella tabella devono essere raggruppati, pertanto nell'esempio BasicTableIndex viene creato un `Dictionary<>` dalla matrice di stringhe usando la prima lettera di ogni elemento come chiave del dizionario:

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

Il `UITableViewSource` sottoclasse deve quindi i metodi seguenti, aggiunti o modificati per l'uso di `Dictionary<>` :

-   **NumberOfSections** : questo metodo è facoltativo, per impostazione predefinita la tabella presuppone una sezione. La visualizzazione di un indice di questo metodo deve restituire il numero di elementi nell'indice (ad esempio, 26 se l'indice contiene tutte le lettere dell'alfabeto inglese).
-   **RowsInSection** : restituisce il numero di righe in una sezione specificata.
-   **SectionIndexTitles** -restituisce la matrice di stringhe che verrà usato per visualizzare l'indice. Il codice di esempio restituisce una matrice di lettere.


I metodi nel file di esempio aggiornati **BasicTableIndex/TableSource.cs** simile al seguente:

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

Gli indici in genere vengono utilizzati solo con lo stile di tabella comune.


<a name="Adding_Headers_and_Footers" />

## <a name="adding-headers-and-footers"></a>Aggiunta di intestazioni e piè di pagina

Informazioni sulle intestazioni e piè di pagina è utilizzabile per raggruppare visivamente le righe in una tabella. La struttura di dati richiesta è molto simile all'aggiunta di un indice: un `Dictionary<>` funziona particolarmente bene. Invece di usare l'alfabeto per raggruppare le celle, in questo esempio Raggruppa le verdura dal tipo botanico.
L'output sarà analogo al seguente:

 [![](populating-a-table-with-data-images/image6.png "Piè di pagina e intestazioni di esempio")](populating-a-table-with-data-images/image6.png#lightbox)

Per visualizzare le intestazioni e piè di pagina il `UITableViewSource` sottoclasse richiede questi metodi aggiuntivi:

-   **TitleForHeader** : restituisce il testo da utilizzare come intestazione
-   **TitleForFooter** : restituisce il testo da utilizzare come piè di pagina.


I metodi nel file di esempio aggiornati **BasicTableHeaderFooter/Code/TableSource.cs** simile al seguente:

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

È possibile personalizzare ulteriormente l'aspetto dell'intestazione e piè di pagina con una visualizzazione dell'oggetto, utilizzando il `GetViewForHeader` e `GetViewForFooter` esegue l'override di metodo su `UITableViewSource`.


## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithTables (esempio)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)
