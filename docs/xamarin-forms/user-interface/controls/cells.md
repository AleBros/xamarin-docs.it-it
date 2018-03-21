---
title: Xamarin.Forms Cells
description: "È possibile aggiungere controlli ListView e TableViews celle xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 0f8886546004702adbdbca7d991c67d5700e453e
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2018
---
# <a name="xamarinforms-cells"></a>Xamarin.Forms Cells

_È possibile aggiungere controlli ListView e TableViews celle xamarin. Forms._

Oggetto *cella* è un elemento specifico utilizzato per gli elementi in una tabella e viene descritta la modalità di rendering di ogni elemento in un elenco. Il [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) deriva dalla classe [ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/), da cui [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/) deriva. Una cella non si tratta di un elemento visivo; è invece un modello per la creazione di un elemento visivo. 

`Cell` viene utilizzato esclusivamente con [ `ListView` ](views.md#listView) e [ `TableView` ](views.md#tableView) controlli. Per informazioni su come usare e personalizzare celle, consultare il [ `ListView` ](~/xamarin-forms/user-interface/listview/index.md) e [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) documentazione.

## <a name="cells"></a>Celle

Xamarin. Forms supporta i tipi di cella seguenti:

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| Oggetto [ `TextCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell) Visualizza uno o due stringhe di testo. Impostare il [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Text/) proprietà e, facoltativamente, il [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Detail/) proprietà per le stringhe di testo.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell) / [Guida](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) | [![Esempio TextCell](cells-images/TextCell.png "TextCell esempio")](cells-images/TextCell-Large.png#lightbox "TextCell esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| Il [ `ImageCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell) Visualizza le stesse informazioni [ `TextCell` ](#textCell) ma include una bitmap che viene impostata con la [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) proprietà.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell) / [Guida](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell) | [![Esempio ImageCell](cells-images/ImageCell.png "esempio ImageCell")](cells-images/ImageCell-Large.png#lightbox "ImageCell esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| Il [ `SwitchCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell) contiene testo impostato con il [ `Text`'](https://developer.xamarin.com/api/property/Xamarin.Forms.SwitchCellText/) proprietà ed e interruttore inizialmente impostata con il valore booleano [ `On` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SwitchCell.On/) proprietà. Gestire il [ `OnChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.SwitchCell.OnChanged/) eventi per ricevere una notifica quando il `On` le modifiche alle proprietà.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell) / [Guida](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![Esempio SwitchCell](cells-images/SwitchCell.png "SwitchCell esempio")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| Il [ `EntryCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell) definisce un [ `Label` ](https://developer.xamarin.com/api/property/Xamarin.Forms.EntryCell.Label/) proprietà che identifica la cella e una singola riga di testo modificabile nel [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.EntryCell.Text/) proprietà. Gestire il [ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.EntryCell.Completed/) eventi per ricevere una notifica quando l'utente ha completato l'immissione di testo.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell) / [Guida](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![Esempio EntryCell](cells-images/EntryCell.png "EntryCell esempio")](cells-images/EntryCell-Large.png#lightbox "EntryCell esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |


## <a name="related-links"></a>Collegamenti correlati

- [Introduction to Xamarin.Forms (Introduzione a Xamarin.Forms)](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Esempio FormsGallery xamarin. Forms](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentazione di xamarin. Forms API](https://developer.xamarin.com/api/root/Xamarin.Forms/)
