---
title: Popolamento di una tabella con i dati in Novell. iOS
description: Questo documento descrive come popolare una tabella con i dati in un'applicazione Novell. iOS. Vengono illustrati UITableViewSource, il riutilizzo delle celle, l'aggiunta di un indice e le intestazioni e i piè di pagina.
ms.prod: xamarin
ms.assetid: 6FE64DDF-1029-EB9B-6EEC-1C7DFDFDF3AF
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: d180345c36531b58c13eebbd97dc4f7555b8f13c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768864"
---
# <a name="populating-a-table-with-data-in-xamarinios"></a>Popolamento di una tabella con i dati in Novell. iOS

Per aggiungere righe a un `UITableView` oggetto, è necessario implementare `UITableViewSource` una sottoclasse ed eseguire l'override dei metodi che la visualizzazione tabella chiama per popolare.

Questa guida illustra:

- Sottoclasse di un UITableViewSource
- Riutilizzo celle
- Aggiunta di un indice
- Aggiunta di intestazioni e piè di pagina

<a name="Subclassing_UITableViewSource" />

## <a name="subclassing-uitableviewsource"></a>Sottoclasse UITableViewSource

Una `UITableViewSource` sottoclasse viene assegnata `UITableView`a ogni. La visualizzazione tabella esegue una query sulla classe di origine per determinare come eseguire il rendering, ad esempio il numero di righe necessarie e l'altezza di ogni riga se diversa da quella predefinita. In particolare, l'origine fornisce ogni visualizzazione di cella popolata con i dati.

Per fare in modo che una tabella visualizzi i dati, sono necessari solo due metodi obbligatori:

- **RowsInSection** : restituisce un [`nint`](~/cross-platform/macios/nativetypes.md) conteggio del numero totale di righe di dati che devono essere visualizzate nella tabella.
- **GetCell** : restituisce un `UITableCellView` oggetto popolato con i dati per l'indice di riga corrispondente passato al metodo.

Il file di esempio BasicTable **TableSource.cs** include l'implementazione più semplice possibile `UITableViewSource`di. Nel frammento di codice riportato di seguito viene accettata una matrice di stringhe da visualizzare nella tabella e viene restituito uno stile di cella predefinito contenente ogni stringa:

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

Un `UITableViewSource` oggetto può usare qualsiasi struttura di dati, da una matrice di stringhe semplice (come illustrato in questo esempio) a un elenco < > o un'altra raccolta. L'implementazione dei `UITableViewSource` metodi isola la tabella dalla struttura dei dati sottostante.

Per usare questa sottoclasse, creare una matrice di stringhe per costruire l'origine, quindi assegnarla a `UITableView`un'istanza di:

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

La tabella risultante ha un aspetto simile al seguente:

 [![](populating-a-table-with-data-images/image3.png "Tabella di esempio in esecuzione")](populating-a-table-with-data-images/image3.png#lightbox)

La maggior parte delle tabelle consente all'utente di toccare una riga per selezionarla ed eseguire altre azioni, ad esempio la riproduzione di un brano o la chiamata di un contatto o la visualizzazione di un'altra schermata. A tale scopo, è necessario eseguire alcune operazioni. Prima di tutto, creare un AlertController per visualizzare un messaggio quando l'utente fa clic su una riga aggiungendo quanto segue al `RowSelected` metodo:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("Row Selected", tableItems[indexPath.Row], UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    ...

    tableView.DeselectRow (indexPath, true);
}
```

Successivamente, creare un'istanza del controller di visualizzazione:

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

Modificare il metodo ViewDidLoad in cui viene creata la classe UITableViewSource per passare `this` il riferimento:

```csharp
table.Source = new TableSource(tableItems, this);
```

Infine, tornando al `RowSelected` metodo, chiamare `PresentViewController` sul campo memorizzato nella cache:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    ...
    owner.PresentViewController (okAlertController, true, null);

    ...
}
```

Ora l'utente può toccare una riga e verrà visualizzato un avviso:

 [![](populating-a-table-with-data-images/image4.png "Avviso di riga selezionato")](populating-a-table-with-data-images/image4.png#lightbox)

## <a name="cell-reuse"></a>Riutilizzo celle

In questo esempio sono presenti solo sei elementi, quindi non è necessario riutilizzare le celle. Quando si visualizzano centinaia o migliaia di righe, tuttavia, sarebbe uno spreco di memoria per la creazione di centinaia o `UITableViewCell` migliaia di oggetti quando solo pochi si adattano allo schermo alla volta.

Per evitare questa situazione, quando una cella scompare dallo schermo, la relativa visualizzazione viene inserita in una coda per il riutilizzo. Quando l'utente scorre, la tabella chiama `GetCell` per richiedere nuove visualizzazioni da visualizzare. per riutilizzare una cella esistente (attualmente non visualizzata), chiamare semplicemente il `DequeueReusableCell` metodo. Se una cella è disponibile per il riutilizzo, verrà restituita, in caso contrario viene restituito un valore null e il codice deve creare una nuova istanza di cella.

Questo frammento di codice dell'esempio illustra il modello:

```csharp
// request a recycled cell to save memory
UITableViewCell cell = tableView.DequeueReusableCell (cellIdentifier);
// if there are no cells to reuse, create a new one
if (cell == null)
    cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
```

La `cellIdentifier` crea in modo efficace code separate per diversi tipi di cella. In questo esempio tutte le celle hanno lo stesso aspetto, quindi viene usato un solo identificatore hardcoded. Se sono presenti diversi tipi di cella, ognuno deve avere una stringa di identificatore diversa, sia quando ne viene creata un'istanza che quando vengono richiesti dalla coda di riutilizzo.

### <a name="cell-reuse-in-ios-6"></a>Riutilizzo delle celle in iOS 6 +

iOS 6 ha aggiunto un modello di riutilizzo delle celle simile a quello introdotto con le visualizzazioni di raccolta. Anche se il modello di riutilizzo esistente illustrato in precedenza è ancora supportato per la compatibilità con le versioni precedenti, questo nuovo modello è preferibile perché rimuove la necessità del controllo null nella cella.

Con il nuovo modello, un'applicazione registra la classe di celle o XIB da utilizzare chiamando `RegisterClassForCellReuse` o `RegisterNibForCellReuse` nel costruttore del controller. Quindi, quando si rimuove la coda della cella nel `GetCell` metodo, è sufficiente `DequeueReusableCell` chiamare il metodo passando l'identificatore registrato per la classe Cell o XIB e il percorso dell'indice.

Il codice seguente, ad esempio, registra una classe di celle personalizzata in un UITableViewController:

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

Con la classe `UITableViewSource` di cella registrata, la cella può essere rimessa in coda nel `GetCell` metodo di senza la necessità del controllo null aggiuntivo, come illustrato di seguito:

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

Tenere presente che quando si usa il nuovo modello di riutilizzo con una classe di celle personalizzata, è necessario implementare il costruttore `IntPtr`che accetta un oggetto, come illustrato nel frammento di codice seguente. in caso contrario, Objective-C non sarà in grado di costruire un'istanza della classe Cell:

```csharp
public class MyCell : UITableViewCell
{
  public MyCell (IntPtr p):base(p)
  {
  }
  ...
}
```

È possibile vedere gli esempi degli argomenti illustrati in precedenza nell'esempio **BasicTable** collegato a questo articolo.

<a name="Adding_an_Index" />

## <a name="adding-an-index"></a>Aggiunta di un indice

Un indice consente all'utente di scorrere gli elenchi lunghi, in genere ordinati alfabeticamente, sebbene sia possibile indicizzare i criteri desiderati. L'esempio **BasicTableIndex** carica un elenco molto più lungo di elementi da un file per illustrare l'indice. Ogni elemento dell'indice corrisponde a una ' sezione ' della tabella.

 [![](populating-a-table-with-data-images/image5.png "Visualizzazione dell'indice")](populating-a-table-with-data-images/image5.png#lightbox)

Per supportare ' sections ' i dati alla base della tabella devono essere raggruppati, quindi l'esempio BasicTableIndex crea `Dictionary<>` un oggetto dalla matrice di stringhe usando la prima lettera di ogni elemento come chiave del dizionario:

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

Per usare la `Dictionary<>` sottoclasseèquindinecessarioaggiungereomodificareimetodiseguenti:`UITableViewSource`

- **NumberOfSections** : questo metodo è facoltativo. per impostazione predefinita, la tabella presuppone una sezione. Quando si visualizza un indice, questo metodo deve restituire il numero di elementi nell'indice, ad esempio 26 se l'indice contiene tutte le lettere dell'alfabeto inglese.
- **RowsInSection** : restituisce il numero di righe in una sezione specificata.
- **SectionIndexTitles** : restituisce la matrice di stringhe che verrà utilizzata per visualizzare l'indice. Il codice di esempio restituisce una matrice di lettere.

I metodi aggiornati nel file di esempio **BasicTableIndex/TableSource. cs** hanno un aspetto simile al seguente:

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

Gli indici vengono in genere utilizzati solo con lo stile di tabella normale.

<a name="Adding_Headers_and_Footers" />

## <a name="adding-headers-and-footers"></a>Aggiunta di intestazioni e piè di pagina

Le intestazioni e i piè di pagina possono essere utilizzati per raggruppare visivamente le righe in una tabella. La struttura dei dati richiesta è molto simile all'aggiunta di un indice `Dictionary<>` . un funziona molto bene. Invece di usare l'alfabeto per raggruppare le celle, in questo esempio il gruppo di ortaggi viene raggruppato in base al tipo botanico.
L'output sarà analogo al seguente:

 [![](populating-a-table-with-data-images/image6.png "Intestazioni e piè di pagina di esempio")](populating-a-table-with-data-images/image6.png#lightbox)

Per visualizzare le intestazioni e i piè `UITableViewSource` di pagina, la sottoclasse richiede questi metodi aggiuntivi:

- **TitleForHeader** : restituisce il testo da utilizzare come intestazione
- **TitleForFooter** : restituisce il testo da utilizzare come piè di pagina.

I metodi aggiornati nel file di esempio **BasicTableHeaderFooter/code/TableSource. cs** hanno un aspetto simile al seguente:

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

È possibile personalizzare ulteriormente l'aspetto dell'intestazione e del piè di pagina con un oggetto visualizzazione, usando `GetViewForHeader` gli `GetViewForFooter` override del metodo `UITableViewSource`e su.

## <a name="related-links"></a>Collegamenti correlati

- [WorkingWithTables (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithtables)
