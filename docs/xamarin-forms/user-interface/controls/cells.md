---
title: Xamarin.FormsCelle
description: Xamarin.Formsè possibile aggiungere celle a ListViews e TableViews. Questo articolo elenca le celle incluse in Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bd1a2398787fe39c0b4cbd08ccd5c5793775d5cf
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137280"
---
# <a name="xamarinforms-cells"></a>Xamarin.FormsCelle

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_È possibile aggiungere celle Novell. Forms ai ListView e TableViews._

Una *cella* è un elemento specializzato usato per gli elementi di una tabella e descrive il rendering di ogni elemento in un elenco. La [`Cell`](xref:Xamarin.Forms.Cell) classe deriva da [`Element`](xref:Xamarin.Forms.Element) , da cui [`VisualElement`](xref:Xamarin.Forms.Element) deriva anche. Una cella non è a sua volta un elemento visivo. si tratta invece di un modello per la creazione di un elemento visivo.

`Cell`viene utilizzato esclusivamente con [`ListView`](views.md#listview) i [`TableView`](views.md#tableview) controlli e. Per informazioni su come usare e personalizzare le celle, vedere la [`ListView`](~/xamarin-forms/user-interface/listview/index.md) documentazione di e [`TableView`](~/xamarin-forms/user-interface/tableview.md) .

## <a name="cells"></a>Celle

Xamarin.Formssupporta i tipi di cella seguenti:

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| Un oggetto [`TextCell`](xref:Xamarin.Forms.TextCell) Visualizza una o due stringhe di testo. Impostare la [`Text`](xref:Xamarin.Forms.TextCell.Text) proprietà e, facoltativamente, la [`Detail`](xref:Xamarin.Forms.TextCell.Detail) proprietà su queste stringhe di testo.<br /><br />[Documentazione API](xref:Xamarin.Forms.TextCell)  /  [Guida](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) di | [![Esempio di TextCell](cells-images/TextCell.png "Esempio di TextCell")](cells-images/TextCell-Large.png#lightbox "Esempio di TextCell")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| [`ImageCell`](xref:Xamarin.Forms.ImageCell)Visualizza le stesse informazioni di, [`TextCell`](#textCell) ma include una bitmap impostata con la [`Source`](xref:Xamarin.Forms.Image.Source) Proprietà.<br /><br />[Documentazione API](xref:Xamarin.Forms.ImageCell)  /  [Guida](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) di | [![Esempio di ImageCell](cells-images/ImageCell.png "Esempio di ImageCell")](cells-images/ImageCell-Large.png#lightbox "Esempio di ImageCell")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)Contiene il set di testo con la [`Text`](xref:Xamarin.Forms.SwitchCell.Text) proprietà e un'opzione di attivazione/disattivazione impostata inizialmente con la proprietà booleana [`On`](xref:Xamarin.Forms.SwitchCell.On) . Gestire l' [`OnChanged`](xref:Xamarin.Forms.SwitchCell.OnChanged) evento per ricevere una notifica quando la `On` proprietà viene modificata.<br /><br />[Documentazione API](xref:Xamarin.Forms.SwitchCell)  /  [Guida](~/xamarin-forms/user-interface/tableview.md#switchcell) di | [![Esempio di SwitchCell](cells-images/SwitchCell.png "Esempio di SwitchCell")](cells-images/SwitchCell-Large.png#lightbox "Esempio di SwitchCell")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| [`EntryCell`](xref:Xamarin.Forms.EntryCell)Definisce una [`Label`](xref:Xamarin.Forms.EntryCell.Label) proprietà che identifica la cella e una singola riga di testo modificabile nella [`Text`](xref:Xamarin.Forms.EntryCell.Text) Proprietà. [`Completed`](xref:Xamarin.Forms.EntryCell.Completed)Consente di gestire l'evento per ricevere una notifica quando l'utente ha completato la voce di testo.<br /><br />[Documentazione API](xref:Xamarin.Forms.EntryCell)  /  [Guida](~/xamarin-forms/user-interface/tableview.md#entrycell) di | [![Esempio di EntryCell](cells-images/EntryCell.png "Esempio di EntryCell")](cells-images/EntryCell-Large.png#lightbox "Esempio di EntryCell")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.FormsEsempio FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsCampioni](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentazione API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
