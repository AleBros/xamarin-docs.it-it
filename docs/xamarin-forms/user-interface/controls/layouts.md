---
title: Layout di Xamarin.Forms
description: Layout di Xamarin.Forms vengono utilizzati per la composizione di controlli dell'interfaccia utente nelle strutture visual. Questo articolo elenca i layout inclusi in Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
ms.openlocfilehash: 4747ce6555a6440c687dc3d239d75307f68683ca
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "78293085"
---
# <a name="xamarinforms-layouts"></a>Layout di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_I layout di Novell. Forms vengono usati per comporre controlli dell'interfaccia utente in strutture visive._

Le classi [`Layout`](xref:Xamarin.Forms.Layout) e [`Layout<T>`](xref:Xamarin.Forms.Layout`1) in Novell. Forms sono sottotipi specializzati di viste che fungono da contenitori per le visualizzazioni e altri layout. La classe `Layout` deriva da [`View`](views.md). Un derivato `Layout` contiene in genere la logica per impostare la posizione e le dimensioni degli elementi figlio nelle applicazioni Novell. Forms.

[![Tipi di layout Novell. Forms](layouts-images/layouts-sml.png "Tipi di layout Novell. Forms")](layouts-images/layouts.png#lightbox "Tipi di layout Novell. Forms")

Le classi che derivano da `Layout` possono essere divise in due categorie:

## <a name="layouts-with-single-content"></a>Layout con contenuto singolo

Queste classi derivano da [`Layout`](xref:Xamarin.Forms.Layout), che definisce le proprietà [`Padding`](xref:Xamarin.Forms.Layout.Padding) e [`IsClippedToBounds`](xref:Xamarin.Forms.Layout.IsClippedToBounds) .

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView) contiene un singolo elemento figlio impostato con la proprietà [`Content`](xref:Xamarin.Forms.ContentView.Content) . La proprietà `Content` può essere impostata su qualsiasi `View` derivata, inclusi altri `Layout` derivati. `ContentView` viene usato principalmente come elemento strutturale e funge da classe base per [`Frame`](#frame).<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.ContentView) / [Guida](~/xamarin-forms/user-interface/layouts/contentview.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/) | [![Esempio di ContentView](layouts-images/ContentView.png "Esempio di ContentView")](layouts-images/ContentView-Large.png#lightbox "Esempio di ContentView")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Frame

|     |     |
| --- | --- |
| La classe [`Frame`](xref:Xamarin.Forms.Frame) deriva da [`ContentView`](#contentView) e visualizza un bordo, o frame, intorno al relativo elemento figlio. La classe `Frame` ha un valore [`Padding`](xref:Xamarin.Forms.Layout.Padding) predefinito pari a 20 e definisce anche le proprietà [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor), [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius)e [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) .<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Frame) / [Guida](~/xamarin-forms/user-interface/layouts/frame.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/) | [![Esempio di frame](layouts-images/Frame.png "Esempio di frame")](layouts-images/Frame-Large.png#lightbox "Esempio di frame")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView) è in grado di scorrere il contenuto. Impostare la proprietà [`Content`](xref:Xamarin.Forms.ScrollView.Content) su una vista o un layout troppo grande per adattarlo allo schermo. Il contenuto di un `ScrollView` è spesso una [`StackLayout`](#stackLayout). Impostare la proprietà [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) per indicare se lo scorrimento deve essere verticale, orizzontale o entrambi.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.ScrollView) / [Guida](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Esempio di ScrollView](layouts-images/ScrollView.png "Esempio di ScrollView")](layouts-images/ScrollView-Large.png#lightbox "Esempio di ScrollView")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) Visualizza il contenuto con un modello di controllo ed è la classe di base per [`ContentView`](#contentView).<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.TemplatedView) / [Guida](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Esempio di TemplatedView](layouts-images/TemplatedView.png "Esempio di TemplatedView")](layouts-images/TemplatedView.png#lightbox "Esempio di TemplatedView") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) è un gestore di layout per le visualizzazioni basate su modelli, utilizzato all'interno di un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) per contrassegnare il punto in cui viene visualizzato il contenuto da presentare.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.ContentPresenter) / [Guida](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Esempio di ContentPresenter](layouts-images/ContentPresenter.png "Esempio di ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "Esempio di ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Layout con più elementi figlio

Queste classi derivano da [`Layout<View>`](xref:Xamarin.Forms.Layout`1).

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout) posiziona gli elementi figlio in uno stack orizzontalmente o verticalmente in base alla proprietà [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) . La proprietà [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) regola la spaziatura tra gli elementi figlio e il valore predefinito è 6.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.StackLayout) / [Guida](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)| [![Esempio di StackLayout](layouts-images/StackLayout.png "Esempio di StackLayout")](layouts-images/StackLayout-Large.png#lightbox "Esempio di StackLayout")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grid

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid) posiziona gli elementi figlio in una griglia di righe e colonne. La posizione di un elemento figlio viene indicata usando le [proprietà associate](~/xamarin-forms/xaml/attached-properties.md) [`Row`](xref:Xamarin.Forms.Grid.RowProperty), [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty), [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty)e [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty).<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Grid) / [Guida](~/xamarin-forms/user-interface/layouts/grid.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Esempio di griglia](layouts-images/Grid.png "Esempio di griglia")](layouts-images/Grid-Large.png#lightbox "Esempio di griglia")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) posiziona gli elementi figlio in posizioni specifiche rispetto al relativo elemento padre. La posizione di un elemento figlio viene indicata usando le [proprietà associate](~/xamarin-forms/xaml/attached-properties.md) [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) e [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty). Un `AbsoluteLayout` è utile per animare le posizioni delle visualizzazioni.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.AbsoluteLayout) / [Guida](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Esempio di AbsoluteLayout](layouts-images/AbsoluteLayout.png "Esempio di AbsoluteLayout")](layouts-images/AbsoluteLayout-Large.png#lightbox "Esempio di AbsoluteLayout")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutDemoPage.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) posiziona gli elementi figlio rispetto al `RelativeLayout` stesso o ai relativi elementi di pari livello. La posizione di un elemento figlio viene indicata usando le [proprietà associate](~/xamarin-forms/xaml/attached-properties.md) impostate su oggetti di tipo [`Constraint`](xref:Xamarin.Forms.Constraint) e [`BoundsConstraint`](xref:Xamarin.Forms.Constraint).<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.RelativeLayout) / [Guida](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Esempio di sul relativelayout](layouts-images/RelativeLayout.png "Esempio di sul relativelayout")](layouts-images/RelativeLayout-Large.png#lightbox "Esempio di sul relativelayout")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) è basato sul modulo CSS [flexible box layout](https://www.w3.org/TR/css-flexbox-1/), comunemente noto come _Flex layout_ o _Flex-box_. `FlexLayout` definisce sei proprietà associabili e cinque proprietà associabili associate che consentono di impilare o incapsulare elementi figlio con numerose opzioni di allineamento e orientamento.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.FlexLayout) / [Guida](~/xamarin-forms/user-interface/layouts/flex-layout.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos) | [![Esempio di FlexLayout](layouts-images/FlexLayout.png "Esempio di FlexLayout")](layouts-images/FlexLayout-Large.png#lightbox "Esempio di FlexLayout")<br />codice per questa pagina / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs) |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Novell. Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Esempi di Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentazione per le API di Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
