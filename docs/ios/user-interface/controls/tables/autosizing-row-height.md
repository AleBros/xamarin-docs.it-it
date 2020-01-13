---
title: Ridimensionamento automatico dell'altezza delle righe in Xamarin.iOS
description: Questo documento descrive come aggiungere alle app Xamarin.iOS le righe con le altezze che variano in base al contenuto. Viene illustrato il layout delle celle nella finestra di progettazione iOS e viene abilitata l'altezza di ridimensionamento automatico.
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 4129370ecb465340a893e0a7f16703a08cc1db72
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021922"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>Ridimensionamento automatico dell'altezza delle righe in Xamarin.iOS

A partire da iOS 8, Apple ha aggiunto la possibilità di creare una visualizzazione tabella (`UITableView`) che può aumentare e ridurre automaticamente l'altezza di una determinata riga in base alla dimensione del contenuto usando il layout automatico, le classi di dimensioni e i vincoli.

iOS 11 ha aggiunto la possibilità di espandere automaticamente le righe. Le intestazioni, i piè di pagina e le celle possono ora essere ridimensionati automaticamente in base al relativo contenuto. Tuttavia, se la tabella viene creata nella finestra di progettazione di iOS, Interface Builder o se dispone di altezze di riga fisse, è necessario abilitare manualmente le celle di ridimensionamento automatico, come descritto in questa guida.

## <a name="cell-layout-in-the-ios-designer"></a>Layout di celle in iOS designer

Aprire lo storyboard per la visualizzazione tabella per cui si desidera applicare il ridimensionamento automatico della riga in iOS designer, selezionare il *prototipo* della cella e progettare il layout della cella. Esempio:

[![](autosizing-row-height-images/table01.png "The Cell's Prototype design")](autosizing-row-height-images/table01.png#lightbox)

Per ogni elemento nel prototipo, aggiungere vincoli per Mantenete gli elementi nella posizione corretta, perché la visualizzazione tabella viene ridimensionata per la rotazione o per le diverse dimensioni dello schermo del dispositivo iOS. Ad esempio, l'aggiunta del `Title` alla parte superiore, a sinistra e a destra della *visualizzazione contenuto*della cella:

[![](autosizing-row-height-images/table02.png "Pinning the Title to the top, left and right of the Cells Content View")](autosizing-row-height-images/table02.png#lightbox)

Nel caso della tabella di esempio, il piccolo `Label` (sotto la `Title`) è il campo che può ridursi e crescere per aumentare o diminuire l'altezza della riga. Per ottenere questo risultato, aggiungere i vincoli seguenti per bloccare il lato sinistro, destro, superiore e inferiore dell'etichetta:

[![](autosizing-row-height-images/table03.png "These constraints to pin the left, right, top and bottom of the label")](autosizing-row-height-images/table03.png#lightbox)

Ora che sono stati completamente limitati gli elementi nella cella, è necessario chiarire quale elemento deve essere esteso. A tale scopo, impostare il **contenuto che abbracci** la priorità e la **priorità di resistenza della compressione del contenuto** , se necessario, nella sezione del **layout** del riquadro delle proprietà:

[![](autosizing-row-height-images/table03a.png "The Layout section of the Properties Pad")](autosizing-row-height-images/table03a.png#lightbox)

Impostare l'elemento che si desidera espandere per avere un valore di priorità **più basso** e un valore di priorità di resistenza di compressione **inferiore** .

Successivamente, è necessario selezionare il prototipo di cella e assegnargli un **identificatore**univoco:

[![](autosizing-row-height-images/table04.png "Giving the Cell Prototype a unique Identifier")](autosizing-row-height-images/table04.png#lightbox)

Nel caso dell'esempio, `GrowCell`. Questo valore verrà usato in un secondo momento quando si popola la tabella.

> [!IMPORTANT]
> Se la tabella contiene più di un tipo di cella (**prototipo**), è necessario assicurarsi che ogni tipo disponga di un `Identifier` univoco per il ridimensionamento automatico delle righe.

Per ogni elemento del prototipo di cella, assegnare un **nome** per esporlo C# al codice. Esempio:

[![](autosizing-row-height-images/table05.png "Assign a Name to expose it to C# code")](autosizing-row-height-images/table05.png#lightbox)

Aggiungere quindi una classe personalizzata per la `UITableViewController`, il `UITableView` e il `UITableCell` (prototipo). Esempio: 

[![](autosizing-row-height-images/table06.png "Adding a custom class for the UITableViewController, the UITableView and the UITableCell")](autosizing-row-height-images/table06.png#lightbox)

Infine, per assicurarsi che tutto il contenuto previsto venga visualizzato nell'etichetta, impostare la proprietà **Lines** su `0`:

[![](autosizing-row-height-images/table06.png "The Lines property set to 0")](autosizing-row-height-images/table06a.png#lightbox)

Con l'interfaccia utente definita, aggiungere il codice per abilitare il ridimensionamento automatico dell'altezza delle righe.

## <a name="enabling-auto-resizing-height"></a>Abilitazione dell'altezza di ridimensionamento automatico

Nell'origine dati (`UITableViewDatasource`) o nell'origine (`UITableViewSource`) della visualizzazione tabella, quando si rimuove la coda di una cella è necessario usare il `Identifier` definito nella finestra di progettazione. Esempio:

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

Per impostazione predefinita, la visualizzazione tabella verrà impostata per l'altezza della riga con ridimensionamento automatico. Per garantire questo problema, è necessario impostare la proprietà `RowHeight` su `UITableView.AutomaticDimension`. È anche necessario impostare la proprietà `EstimatedRowHeight` nel `UITableViewController`. Esempio:

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

Con questo codice, quando viene eseguita l'app, ogni riga viene compattata e aumentata in base all'altezza dell'ultima etichetta nel prototipo di cella. Esempio:

[![](autosizing-row-height-images/table07.png "A sample table run")](autosizing-row-height-images/table07.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [GrowRowTable (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/growrowtable)
