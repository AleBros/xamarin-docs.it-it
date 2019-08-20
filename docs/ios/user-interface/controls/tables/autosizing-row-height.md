---
title: Ridimensionamento automatico dell'altezza delle righe in Novell. iOS
description: Questo documento descrive come aggiungere alle app Novell. iOS le righe con le altezze che variano in base al contenuto. Viene illustrato il layout delle celle nella finestra di progettazione iOS e viene abilitata l'altezza di ridimensionamento automatico.
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 025c3ee6fc176df02f72e78395b880153d6b841d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655721"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>Ridimensionamento automatico dell'altezza delle righe in Novell. iOS

A partire da iOS 8, Apple ha aggiunto la possibilità di creare una visualizzazione`UITableView`tabella () in grado di aumentare e ridurre automaticamente l'altezza di una determinata riga in base alla dimensione del contenuto usando il layout automatico, le classi di dimensioni e i vincoli.

iOS 11 ha aggiunto la possibilità di espandere automaticamente le righe. Le intestazioni, i piè di pagina e le celle possono ora essere ridimensionati automaticamente in base al relativo contenuto. Tuttavia, se la tabella viene creata nella finestra di progettazione di iOS, Interface Builder o se dispone di altezze di riga fisse, è necessario abilitare manualmente le celle di ridimensionamento automatico, come descritto in questa guida.

## <a name="cell-layout-in-the-ios-designer"></a>Layout di celle in iOS designer

Aprire lo storyboard per la visualizzazione tabella per cui si desidera applicare il ridimensionamento automatico della riga in iOS designer, selezionare il prototipo della cella e progettare il layout della cella. Ad esempio:

[![](autosizing-row-height-images/table01.png "Progettazione del prototipo della cella")](autosizing-row-height-images/table01.png#lightbox)

Per ogni elemento nel prototipo, aggiungere vincoli per Mantenete gli elementi nella posizione corretta, perché la visualizzazione tabella viene ridimensionata per la rotazione o per le diverse dimensioni dello schermo del dispositivo iOS. Ad esempio, l'aggiunta `Title` dell'oggetto alla parte superiore, a sinistra e a destra della *visualizzazione contenuto*della cella:

[![](autosizing-row-height-images/table02.png "Aggiunta del titolo in alto, a sinistra e a destra della visualizzazione contenuto celle")](autosizing-row-height-images/table02.png#lightbox)

Nel caso della tabella di esempio, il piccolo `Label` ( `Title`sotto) è il campo che può ridursi e aumentare per aumentare o diminuire l'altezza della riga. Per ottenere questo risultato, aggiungere i vincoli seguenti per bloccare il lato sinistro, destro, superiore e inferiore dell'etichetta:

[![](autosizing-row-height-images/table03.png "Questi vincoli per aggiungere l'etichetta a sinistra, a destra, in alto e in basso")](autosizing-row-height-images/table03.png#lightbox)

Ora che sono stati completamente limitati gli elementi nella cella, è necessario chiarire quale elemento deve essere esteso. A tale scopo, impostare il **contenuto che abbracci** la priorità e la **priorità di resistenza della compressione del contenuto** , se necessario, nella sezione del **layout** del riquadro delle proprietà:

[![](autosizing-row-height-images/table03a.png "Sezione del layout dell'riquadro delle proprietà")](autosizing-row-height-images/table03a.png#lightbox)

Impostare l'elemento che si desidera espandere per avere un valore di priorità **più basso** e un valore di priorità di resistenza di compressione **inferiore** .

Successivamente, è necessario selezionare il prototipo di cella e assegnargli un **identificatore**univoco:

[![](autosizing-row-height-images/table04.png "Assegnazione di un identificatore univoco al prototipo di cella")](autosizing-row-height-images/table04.png#lightbox)

Nel caso dell'esempio, `GrowCell`. Questo valore verrà usato in un secondo momento quando si popola la tabella.

> [!IMPORTANT]
> Se la tabella contiene più di un tipo di cella(prototipo), è necessario assicurarsi che ogni tipo disponga di `Identifier` un univoco per il ridimensionamento automatico delle righe.

Per ogni elemento del prototipo di cella, assegnare un **nome** per esporlo C# al codice. Ad esempio:

[![](autosizing-row-height-images/table05.png "Assegnare un nome per esporlo al C# codice")](autosizing-row-height-images/table05.png#lightbox)

Aggiungere quindi una classe personalizzata per `UITableViewController` `UITableView` , e `UITableCell` (Prototype). Ad esempio: 

[![](autosizing-row-height-images/table06.png "Aggiunta di una classe personalizzata per UITableViewController, UITableView e UITableCell")](autosizing-row-height-images/table06.png#lightbox)

Infine, per assicurarsi che tutto il contenuto previsto venga visualizzato nell'etichetta, impostare la proprietà **Lines** su `0`:

[![](autosizing-row-height-images/table06.png "La proprietà Lines è impostata su 0")](autosizing-row-height-images/table06a.png#lightbox)

Con l'interfaccia utente definita, aggiungere il codice per abilitare il ridimensionamento automatico dell'altezza delle righe.

## <a name="enabling-auto-resizing-height"></a>Abilitazione dell'altezza di ridimensionamento automatico

Nell'origine dati (`UITableViewDatasource`) o nell'origine (`UITableViewSource`) della visualizzazione tabella, quando si rimuove la coda di una cella è necessario usare `Identifier` l'oggetto definito nella finestra di progettazione. Ad esempio:

```csharp
public string CellID {
    get { return "GrowCell"; }
}
...

public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (CellID, indexPath) as GrowRowTableCell;
    var item = Items [indexPath.Row];

    // Setup
    cell.Image = UIImage.FromFile(item.ImageName);
    cell.Title = item.Title;
    cell.Description = item.Description;

    return cell;
}
```

Per impostazione predefinita, la visualizzazione tabella verrà impostata per l'altezza della riga con ridimensionamento automatico. Per garantire questo problema, `RowHeight` la proprietà deve essere impostata `UITableView.AutomaticDimension`su. È anche necessario impostare la `EstimatedRowHeight` proprietà `UITableViewController`in. Ad esempio:

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Initialize table
    TableView.DataSource = new GrowRowTableDataSource(this);
    TableView.Delegate = new GrowRowTableDelegate (this);
    TableView.RowHeight = UITableView.AutomaticDimension;
    TableView.EstimatedRowHeight = 40f;
    TableView.ReloadData ();
}
```

Questa stima non è necessariamente esatta, ma è sufficiente una stima approssimativa dell'altezza media di ogni riga della visualizzazione tabella.

Con questo codice, quando viene eseguita l'app, ogni riga viene compattata e aumentata in base all'altezza dell'ultima etichetta nel prototipo di cella. Ad esempio:

[![](autosizing-row-height-images/table07.png "Esecuzione di una tabella di esempio")](autosizing-row-height-images/table07.png#lightbox)


## <a name="related-links"></a>Collegamenti correlati

- [GrowRowTable (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/growrowtable)
