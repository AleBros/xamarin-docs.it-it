---
title: Xamarin.Forms Cells
description: Le celle di xamarin. Forms possono essere aggiunti a controlli ListView e TableViews. Questo articolo elenca le celle incluse in xamarin. Forms.
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 1e003a80b58f783829f5af3b74801fc3c91c88e9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655611"
---
# <a name="xamarinforms-cells"></a>Xamarin.Forms Cells

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Le celle di xamarin. Forms possono essere aggiunti a controlli ListView e TableViews._

Oggetto *cella* è un elemento specializzato usato per gli elementi in una tabella e viene descritta la modalità di rendering di ogni elemento in un elenco. Il [`Cell`](xref:Xamarin.Forms.Cell) deriva dalla classe [`Element`](xref:Xamarin.Forms.Element), da cui [`VisualElement`](xref:Xamarin.Forms.Element) deriva anche. Una cella è di per sé un elemento visivo; è invece un modello per la creazione di un elemento visivo.

`Cell` viene usato esclusivamente [ `ListView` ](views.md#listView) e [ `TableView` ](views.md#tableView) controlli. Per informazioni su come usare e personalizzare celle, vedere la [`ListView`](~/xamarin-forms/user-interface/listview/index.md) e [`TableView`](~/xamarin-forms/user-interface/tableview.md) documentazione.

## <a name="cells"></a>Celle

Xamarin. Forms supporta i tipi di cella seguenti:

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| Oggetto [`TextCell`](xref:Xamarin.Forms.TextCell) Visualizza uno o due stringhe di testo. Impostare il [`Text`](xref:Xamarin.Forms.TextCell.Text) proprietà e, facoltativamente, il [`Detail`](xref:Xamarin.Forms.TextCell.Detail) proprietà per le stringhe di testo.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.TextCell) / [Guida](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) | [![Esempio TextCell](cells-images/TextCell.png "esempio TextCell")](cells-images/TextCell-Large.png#lightbox "TextCell esempio")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| Il [`ImageCell`](xref:Xamarin.Forms.ImageCell) vengono visualizzate le stesse informazioni [`TextCell`](#textCell) ma include una mappa di bit è impostato con la [`Source`](xref:Xamarin.Forms.Image.Source) proprietà.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.ImageCell) / [Guida](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell) | [![Esempio ImageCell](cells-images/ImageCell.png "esempio ImageCell")](cells-images/ImageCell-Large.png#lightbox "ImageCell esempio")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| Contiene il set di testo con [`Text`](xref:Xamarin.Forms.SwitchCell.Text) la proprietà e un'opzione di attivazione/disattivazione impostata inizialmente con la proprietà booleana [`On`.](xref:Xamarin.Forms.SwitchCell.On) [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) Gestire le [`OnChanged`](xref:Xamarin.Forms.SwitchCell.OnChanged) eventi per ricevere una notifica quando il `On` le modifiche alle proprietà.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.SwitchCell) / [Guida](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![Esempio SwitchCell](cells-images/SwitchCell.png "esempio SwitchCell")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell esempio")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| Il [`EntryCell`](xref:Xamarin.Forms.EntryCell) definisce una [`Label`](xref:Xamarin.Forms.EntryCell.Label) proprietà che identifica la cella e una singola riga di testo modificabili nel [`Text`](xref:Xamarin.Forms.EntryCell.Text) proprietà. Gestire le [`Completed`](xref:Xamarin.Forms.EntryCell.Completed) eventi per ricevere una notifica quando l'utente ha completato l'immissione di testo.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.EntryCell) / [Guida](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![Esempio EntryCell](cells-images/EntryCell.png "esempio EntryCell")](cells-images/EntryCell-Large.png#lightbox "EntryCell esempio")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |


## <a name="related-links"></a>Collegamenti correlati

- [Esempio di xamarin. Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Esempi di Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentazione per le API di Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
