---
title: Altezza della riga di ridimensionamento automatico in xamarin. IOS
description: Questo documento descrive come aggiungere alle App xamarin. IOS tabella Visualizza righe la cui altezza delle righe varia in base al contenuto. Viene descritto il layout di cella in iOS Designer e altezza consentendo il ridimensionamento automatico.
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: e4446abc73817eb0672cd10a69ff6f738de0c1e1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61029121"
---
# <a name="auto-sizing-row-height-in-xamarinios"></a>Altezza della riga di ridimensionamento automatico in xamarin. IOS

A partire da iOS 8, Apple aggiunta la possibilità di creare una visualizzazione tabella (`UITableView`) che può aumentare e ridurre l'altezza di una determinata riga in base alla dimensione del relativo contenuto usando Layout automatico, le classi di dimensioni e i vincoli automaticamente.

iOS 11 è stata aggiunta la possibilità per le righe da espandere automaticamente. Le intestazioni, piè di pagina e le celle possono ora essere automaticamente ridimensionate in base al contenuto. Tuttavia, se la tabella viene creata in iOS Designer, Interface Builder, o se è risolto altezza delle righe è necessario abilitare manualmente self ridimensionamento celle, come descritto in questa Guida.

## <a name="cell-layout-in-the-ios-designer"></a>Layout della cella in iOS Designer

Aprire lo storyboard per la visualizzazione di tabella che si desidera disporre auto-resize della riga per in iOS Designer, selezionare la cella *prototipo* e progettare il layout della cella. Ad esempio:

[![](autosizing-row-height-images/table01.png "Progettazione di prototipo della cella")](autosizing-row-height-images/table01.png#lightbox)

Per ogni elemento del prototipo, aggiungere vincoli per mantenere gli elementi nella posizione corretta visualizzazione della tabella viene ridimensionata per rotazione o iOS diverse dimensioni dello schermo del dispositivo. Ad esempio, l'aggiunta di `Title` in alto a sinistra e a destra della cella *visualizzazione contenuto*:

[![](autosizing-row-height-images/table02.png "Aggiungere il titolo in alto, a sinistra e destra della visualizzazione di contenuto celle")](autosizing-row-height-images/table02.png#lightbox)

Nel caso della tabella di esempio, il piccolo `Label` (sotto il `Title`) è il campo in grado di riduzione e aumento delle dimensioni per aumentare o diminuire l'altezza della riga. Per ottenere questo effetto, aggiungere i vincoli seguenti per aggiungere a sinistra, destra, superiore e inferiore dell'etichetta:

[![](autosizing-row-height-images/table03.png "Questi vincoli per aggiungere a sinistra, destra, superiore e inferiore dell'etichetta")](autosizing-row-height-images/table03.png#lightbox)

Ora che è stato vincolato completamente gli elementi nella cella, è necessario chiarire quale elemento deve essere esteso. A questo scopo, impostare il **priorità contenuto un sacco di abbracci** e **contenuto compressione resistenza priorità** in base alle esigenze nel **Layout** sezione del riquadro delle proprietà:

[![](autosizing-row-height-images/table03a.png "Sezione del Layout del riquadro delle proprietà")](autosizing-row-height-images/table03a.png#lightbox)

Impostare l'elemento che si desidera espandere per avere una **inferiore** il valore di priorità un sacco di abbracci e una **inferiore** valore di priorità di resistenza di compressione.

Successivamente, è necessario selezionare il prototipo di cella e assegnargli un valore univoco **identificatore**:

[![](autosizing-row-height-images/table04.png "Offrendo il prototipo di cella di un identificatore univoco")](autosizing-row-height-images/table04.png#lightbox)

Nel caso di questo esempio, `GrowCell`. Si userà questo valore in un secondo momento quando si popola la tabella.

> [!IMPORTANT]
> Se la tabella contiene più di un tipo di cella (**prototipo**), è necessario assicurarsi che ogni tipo ha un proprio univoco `Identifier` per ridimensionamento riga automatico a funzionare.

Per ogni elemento del prototipo cella, assegnare un **Name** esporlo a C# codice. Ad esempio:

[![](autosizing-row-height-images/table05.png "Assegnare un nome per esporla a C# codice")](autosizing-row-height-images/table05.png#lightbox)

Successivamente, aggiungere una classe personalizzata per il `UITableViewController`, il `UITableView` e il `UITableCell` (prototipo). Ad esempio: 

[![](autosizing-row-height-images/table06.png "Aggiunta di una classe personalizzata per il UITableViewController, il UITableView e il UITableCell")](autosizing-row-height-images/table06.png#lightbox)

Infine, per assicurarsi che tutti i previsti nell'etichetta viene visualizzato contenuto, impostare il **linee** proprietà `0`:

[![](autosizing-row-height-images/table06.png "La proprietà di righe è impostata su 0")](autosizing-row-height-images/table06a.png#lightbox)

Con l'interfaccia utente definita, è possibile aggiungere il codice per abilitare il ridimensionamento di altezza riga automatica.

## <a name="enabling-auto-resizing-height"></a>Abilitare il ridimensionamento automatico di altezza

In entrambi origine dati la tabella della vista (`UITableViewDatasource`) o un'origine (`UITableViewSource`), quando abbiamo rimozione dalla coda di una cella è necessario usare il `Identifier` definito nella finestra di progettazione. Ad esempio:

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

Per impostazione predefinita, la visualizzazione della tabella verrà impostata per il ridimensionamento automatico di altezza di riga. A tale scopo, il `RowHeight` proprietà deve essere impostata su `UITableView.AutomaticDimension`. È inoltre necessario impostare il `EstimatedRowHeight` proprietà nel nostro `UITableViewController`. Ad esempio:

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

Questa stima non deve necessariamente essere esatto, solo una stima approssimativa dell'altezza della media di ogni riga nella visualizzazione della tabella.

Con questo codice, quando l'app viene eseguita, ogni riga verrà compatta e aumento delle dimensioni in base all'altezza dell'ultima etichetta nel prototipo di cella. Ad esempio:

[![](autosizing-row-height-images/table07.png "Esecuzione di una tabella di esempio")](autosizing-row-height-images/table07.png#lightbox)


## <a name="related-links"></a>Collegamenti correlati

- [GrowRowTable (esempio)](https://developer.xamarin.com/samples/monotouch/GrowRowTable/)
