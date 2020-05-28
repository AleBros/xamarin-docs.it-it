---
title: Xamarin.FormsLayout
description: Xamarin.FormsI layout vengono usati per comporre controlli dell'interfaccia utente in strutture visive. Questo articolo elenca i layout inclusi in Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c39bf29feceaf598ac8fd38e6af3d227b6deddc0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137306"
---
# <a name="xamarinforms-layouts"></a>Xamarin.FormsLayout

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_I layout di Novell. Forms vengono usati per comporre controlli dell'interfaccia utente in strutture visive._

Le [`Layout`](xref:Xamarin.Forms.Layout) [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classi e in Xamarin.Forms sono sottotipi specializzati di viste che fungono da contenitori per le visualizzazioni e altri layout. La `Layout` classe stessa deriva da [`View`](views.md) . Un `Layout` derivato contiene in genere la logica per impostare la posizione e le dimensioni degli elementi figlio nelle Xamarin.Forms applicazioni.

[![Xamarin.FormsTipi di layout](layouts-images/layouts-sml.png "[! OP. Tipi di layout NO-LOC (Novell. Forms)]")](layouts-images/layouts.png#lightbox "[! OP. Tipi di layout NO-LOC (Novell. Forms)]")

Le classi che derivano da `Layout` possono essere divise in due categorie:

## <a name="layouts-with-single-content"></a>Layout con contenuto singolo

Queste classi derivano da [`Layout`](xref:Xamarin.Forms.Layout) , che definisce le [`Padding`](xref:Xamarin.Forms.Layout.Padding) [`IsClippedToBounds`](xref:Xamarin.Forms.Layout.IsClippedToBounds) proprietà e.

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView)contiene un singolo elemento figlio impostato con la [`Content`](xref:Xamarin.Forms.ContentView.Content) Proprietà. La `Content` proprietà può essere impostata su qualsiasi `View` derivato, inclusi altri `Layout` derivati. `ContentView`viene usato principalmente come elemento strutturale e funge da classe di base per [`Frame`](#frame) .<br /><br />[Documentazione API](xref:Xamarin.Forms.ContentView)  /  [Guida](~/xamarin-forms/user-interface/layouts/contentview.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/) di | [![Esempio di ContentView](layouts-images/ContentView.png "Esempio di ContentView")](layouts-images/ContentView-Large.png#lightbox "Esempio di ContentView")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Frame

|     |     |
| --- | --- |
| La [`Frame`](xref:Xamarin.Forms.Frame) classe deriva da [`ContentView`](#contentView) e visualizza un bordo, o frame, intorno al relativo elemento figlio. La `Frame` classe ha un [`Padding`](xref:Xamarin.Forms.Layout.Padding) valore predefinito di 20 e definisce anche le [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor) [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius) proprietà, e [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) .<br /><br />[Documentazione API](xref:Xamarin.Forms.Frame)  /  [Guida](~/xamarin-forms/user-interface/layouts/frame.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/) di | [![Esempio di frame](layouts-images/Frame.png "Esempio di frame")](layouts-images/Frame-Large.png#lightbox "Esempio di frame")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView)è in grado di scorrere il contenuto. Impostare la [`Content`](xref:Xamarin.Forms.ScrollView.Content) proprietà su una vista o un layout troppo grande per adattarlo allo schermo. Il contenuto di un oggetto `ScrollView` è molto spesso [`StackLayout`](#stackLayout) . Impostare la [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) proprietà per indicare se lo scorrimento deve essere verticale, orizzontale o entrambi.<br /><br />[Documentazione API](xref:Xamarin.Forms.ScrollView)  /  [Guida](~/xamarin-forms/user-interface/layouts/scroll-view.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) di | [![Esempio di ScrollView](layouts-images/ScrollView.png "Esempio di ScrollView")](layouts-images/ScrollView-Large.png#lightbox "Esempio di ScrollView")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)Visualizza il contenuto con un modello di controllo ed è la classe di base per [`ContentView`](#contentView) .<br /><br />[Documentazione API](xref:Xamarin.Forms.TemplatedView)  /  [Guida](~/xamarin-forms/app-fundamentals/templates/control-template.md) di | [![Esempio di TemplatedView](layouts-images/TemplatedView.png "Esempio di TemplatedView")](layouts-images/TemplatedView.png#lightbox "Esempio di TemplatedView") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)è un gestore di layout per le visualizzazioni basate su modelli, utilizzato all'interno di un oggetto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) per contrassegnare il punto in cui viene visualizzato il contenuto da presentare.<br /><br />[Documentazione API](xref:Xamarin.Forms.ContentPresenter)  /  [Guida](~/xamarin-forms/app-fundamentals/templates/control-template.md) di | [![Esempio di ContentPresenter](layouts-images/ContentPresenter.png "Esempio di ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "Esempio di ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Layout con più elementi figlio

Queste classi derivano da [`Layout<View>`](xref:Xamarin.Forms.Layout`1) .

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout)posiziona gli elementi figlio in uno stack orizzontalmente o verticalmente in base alla [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) Proprietà. La [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) Proprietà governa la spaziatura tra gli elementi figlio e il valore predefinito è 6.<br /><br />[Documentazione API](xref:Xamarin.Forms.StackLayout)  /  [Guida](~/xamarin-forms/user-interface/layouts/stacklayout.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) di| [![Esempio di StackLayout](layouts-images/StackLayout.png "Esempio di StackLayout")](layouts-images/StackLayout-Large.png#lightbox "Esempio di StackLayout")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Pannello Grid

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid)posiziona gli elementi figlio in una griglia di righe e colonne. La posizione di un elemento figlio viene indicata usando le [proprietà associate](~/xamarin-forms/xaml/attached-properties.md) [`Row`](xref:Xamarin.Forms.Grid.RowProperty) ,, [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty) [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) e [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) .<br /><br />[Documentazione API](xref:Xamarin.Forms.Grid)  /  [Guida](~/xamarin-forms/user-interface/layouts/grid.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) di | [![Esempio di griglia](layouts-images/Grid.png "Esempio di griglia")](layouts-images/Grid-Large.png#lightbox "Esempio di griglia")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)posiziona gli elementi figlio in posizioni specifiche rispetto al relativo elemento padre. La posizione di un elemento figlio viene indicata usando le [proprietà associate](~/xamarin-forms/xaml/attached-properties.md) [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) e [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) . Un oggetto `AbsoluteLayout` è utile per l'animazione delle posizioni delle visualizzazioni.<br /><br />[Documentazione API](xref:Xamarin.Forms.AbsoluteLayout)  /  [Guida](~/xamarin-forms/user-interface/layouts/absolute-layout.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) di | [![Esempio di AbsoluteLayout](layouts-images/AbsoluteLayout.png "Esempio di AbsoluteLayout")](layouts-images/AbsoluteLayout-Large.png#lightbox "Esempio di AbsoluteLayout")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)posiziona gli elementi figlio relativi a `RelativeLayout` se stesso o ai relativi elementi di pari livello. La posizione di un elemento figlio viene indicata usando le [proprietà associate](~/xamarin-forms/xaml/attached-properties.md) impostate su oggetti di tipo [`Constraint`](xref:Xamarin.Forms.Constraint) e [`BoundsConstraint`](xref:Xamarin.Forms.Constraint) .<br /><br />[Documentazione API](xref:Xamarin.Forms.RelativeLayout)  /  [Guida](~/xamarin-forms/user-interface/layouts/relative-layout.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) di | [![Esempio di sul relativelayout](layouts-images/RelativeLayout.png "Esempio di sul relativelayout")](layouts-images/RelativeLayout-Large.png#lightbox "Esempio di sul relativelayout")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout)si basa sul modulo CSS [flexible box layout](https://www.w3.org/TR/css-flexbox-1/), comunemente noto come _Flex layout_ o _Flex-box_. `FlexLayout`definisce sei proprietà associabili e cinque proprietà associabili associate che consentono di impilare o incapsulare elementi figlio con numerose opzioni di allineamento e orientamento.<br /><br />[Documentazione API](xref:Xamarin.Forms.FlexLayout)  /  [Guida](~/xamarin-forms/user-interface/layouts/flex-layout.md)  /  di [Esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos) di | [![Esempio di FlexLayout](layouts-images/FlexLayout.png "Esempio di FlexLayout")](layouts-images/FlexLayout-Large.png#lightbox "Esempio di FlexLayout")<br />[Codice C# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs)  /  [Pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Xamarin.FormsEsempio FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsCampioni](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentazione API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
