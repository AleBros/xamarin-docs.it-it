---
title: Altezza della riga di ridimensionamento automatico
ms.topic: article
ms.prod: xamarin
ms.assetid: CE45A385-D40A-482A-90A0-E8382C2BFFB9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: c8d66ff8199d451ce7469fa893b7673589c9e320
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="auto-sizing-row-height"></a>Altezza della riga di ridimensionamento automatico

A partire da iOS 8, Apple aggiunta la possibilità di creare una visualizzazione tabella (`UITableView`) che può automaticamente crescere e ridurre l'altezza di una determinata riga in base alla dimensione del relativo contenuto usando i vincoli, le classi di dimensioni e Layout automatico.

iOS 11 ha aggiunto la capacità espandere automaticamente le righe. Le intestazioni, piè di pagina e delle celle possono ora essere automaticamente ridimensionate in base al relativo contenuto. Tuttavia, se la tabella viene creata in iOS progettazione, interfaccia generatore, o se è corretto altezza delle righe è necessario abilitare manualmente self ridimensionamento celle, come descritto in questa Guida.

## <a name="cell-layout-in-the-ios-designer"></a>Layout della cella nella finestra di progettazione iOS

Aprire lo storyboard per la visualizzazione della tabella che si desidera disporre auto-resize della riga nella finestra di progettazione, iOS selezionare la cella *prototipo* e progettare il layout della cella. Ad esempio:

[![](autosizing-row-height-images/table01.png "Progettazione di prototipo della cella")](autosizing-row-height-images/table01.png#lightbox)

Per ogni elemento nel prototipo, aggiungere vincoli per mantenere gli elementi nella posizione corretta, mentre la visualizzazione della tabella viene ridimensionata per rotazione o iOS diverse dimensioni dello schermo di dispositivi. Ad esempio, il blocco di `Title` in alto a sinistra e destra della cella *visualizzazione contenuto*:

[![](autosizing-row-height-images/table02.png "Il titolo in alto, a sinistra e destra di visualizzazione del contenuto delle celle di blocco")](autosizing-row-height-images/table02.png#lightbox)

Nel caso di tabella di esempio, la piccola `Label` (sotto il `Title`) è il campo che è possibile compattare e aumento delle dimensioni per aumentare o diminuire l'altezza della riga. Per ottenere questo effetto, aggiungere i vincoli seguenti per aggiungere sinistro, destro, superiore e inferiore dell'etichetta:

[![](autosizing-row-height-images/table03.png "Questi vincoli per aggiungere sinistro, destro, superiore e inferiore dell'etichetta")](autosizing-row-height-images/table03.png#lightbox)

Ora che è stato completamente vincolata gli elementi nella cella, è necessario chiarire quale elemento deve essere estesa. A tale scopo, impostare il **contenuto priorità un sacco di abbracci** e **contenuto compressione resistenza priorità** in base alle esigenze di **Layout** sezione del riquadro proprietà:

[![](autosizing-row-height-images/table03a.png "Sezione del Layout del riquadro proprietà")](autosizing-row-height-images/table03a.png#lightbox)

Impostare l'elemento che si desidera espandere per avere un **inferiore** il valore di priorità di un sacco di abbracci e un **inferiore** valore di priorità resistenza di compressione.

Successivamente, è necessario selezionare il prototipo di cella e assegnargli un univoco **identificatore**:

[![](autosizing-row-height-images/table04.png "Fornendo il prototipo di cella di un identificatore univoco")](autosizing-row-height-images/table04.png#lightbox)

Nel caso di questo esempio, `GrowCell`. Questo valore verrà utilizzata in un secondo momento quando si popola la tabella.

> [!IMPORTANT]
> **Nota:** se la tabella contiene più di un tipo di cella (**prototipo**), è necessario assicurarsi che ogni tipo ha un proprio univoco `Identifier` per il ridimensionamento di righe automaticamente funzionare.

Per ogni elemento del prototipo cella, assegnare un **nome** esporlo al codice c#. Ad esempio:

[![](autosizing-row-height-images/table05.png "Assegnare un nome di averlo esposto al codice c#")](autosizing-row-height-images/table05.png#lightbox)

Successivamente, aggiungere una classe personalizzata per il `UITableViewController`, `UITableView` e `UITableCell` (prototipo). Ad esempio: 

[![](autosizing-row-height-images/table06.png "Aggiunta di una classe personalizzata per il UITableViewController, il UITableView e il UITableCell")](autosizing-row-height-images/table06.png#lightbox)

Infine, per assicurarsi che tutti i previsti contenuto viene visualizzato nell'etichetta, impostare il **righe** proprietà `0`:

[![](autosizing-row-height-images/table06.png "La proprietà di righe è impostata su 0")](autosizing-row-height-images/table06a.png#lightbox)

Con l'interfaccia utente definito, aggiungere il codice per consentire il ridimensionamento di altezza riga automatica.

## <a name="enabling-auto-resizing-height"></a>Consentendo il ridimensionamento automatico di altezza

In una origine dati della visualizzazione per la tabella (`UITableViewDatasource`) o di origine (`UITableViewSource`), quando si rimozione dalla coda di una cella è necessario utilizzare il `Identifier` definite nella finestra di progettazione. Ad esempio:

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

Per impostazione predefinita, la visualizzazione della tabella verrà impostata per il ridimensionamento automatico dell'altezza di riga. A tale scopo, il `RowHeight` proprietà deve essere impostata su `UITableView.AutomaticDimension`. È inoltre necessario impostare il `EstimatedRowHeight` proprietà nel nostro `UITableViewController`. Ad esempio:

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

Questa stima non deve necessariamente essere esatto, solo una stima approssimativa dell'altezza di ogni riga nella visualizzazione tabella Media.

Con questo codice, quando si esegue l'app, ogni riga verrà compatta e aumento delle dimensioni in base all'altezza dell'ultima etichetta nel prototipo di cella. Ad esempio:

[![](autosizing-row-height-images/table07.png "Esecuzione di una tabella di esempio")](autosizing-row-height-images/table07.png#lightbox)


## <a name="related-links"></a>Collegamenti correlati

- [GrowRowTable (esempio)](https://developer.xamarin.com/samples/monotouch/GrowRowTable/)
