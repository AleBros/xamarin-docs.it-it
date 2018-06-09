---
title: Layout di xamarin. Forms
description: Layout di xamarin. Forms vengono utilizzati per creare i controlli dell'interfaccia utente in strutture visual. Questo articolo vengono elencati i layout inclusi in xamarin. Forms.
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
ms.openlocfilehash: 6e9889bf8ec748ed2034d63acfec9784d074ca44
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243090"
---
# <a name="xamarinforms-layouts"></a>Layout di xamarin. Forms

_Layout di xamarin. Forms vengono utilizzati per creare i controlli dell'interfaccia utente in strutture visual._

Il [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout) e [ `Layout<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/) classi in xamarin. Forms sono specializzati sottotipi di viste che fungono da contenitori per viste e altri layout. Il `Layout` classe deriva da [ `View` ](views.md). Oggetto `Layout` derivato in genere contiene la logica per impostare la posizione e le dimensioni degli elementi figlio nelle applicazioni di xamarin. Forms.

[![Tipi di Layout di xamarin. Forms](layouts-images/layouts-sml.png "i tipi di Layout di xamarin. Forms")](layouts-images/layouts.png#lightbox "i tipi di Layout di xamarin. Forms")

Le classi che derivano da `Layout` possono essere suddivisi in due categorie:

## <a name="layouts-with-single-content"></a>Layout con contenuto singolo

Queste classi derivano da [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/), che definisce [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) e [ `IsClippedToBounds` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.IsClippedToBounds/) proprietà.

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) contiene un singolo elemento figlio che viene impostato con il [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) proprietà. Il `Content` proprietà può essere impostata su qualsiasi `View` derivato, inclusi altri `Layout` derivati. `ContentView` viene utilizzata principalmente come elemento struttura e funge da classe base per [ `Frame` ](#frame).<br /><br />[Documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) | [![Esempio ContentView](layouts-images/ContentView.png "ContentView esempio")](layouts-images/ContentView-Large.png#lightbox "ContentView esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Frame

|     |     |
| --- | --- |
| Il [ `Frame` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/) deriva dalla classe [ `ContentView` ](#contentView) e visualizza una cornice rettangolare intorno al relativo elemento figlio. `Frame` è un valore predefinito [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) valore pari a 20 e definisce anche [ `OutlineColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.OutlineColor/), [ `CornerRadius` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.CornerRadius/), e [ `HasShadow` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Frame.HasShadow/)proprietà.<br /><br />[Documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/) | [![Esempio di frame](layouts-images/Frame.png "Frame esempio")](layouts-images/Frame-Large.png#lightbox "Frame di esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>Elemento ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) è in grado di scorrere il contenuto. Impostare il [ `Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Content/) proprietà da una vista o a un layout troppo grande per adattarsi sullo schermo. (Il contenuto di un `ScrollView` molto spesso un [ `StackLayout` ](#stackLayout).) Impostare il [ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ScrollView.Orientation/) proprietà per indicare se lo scorrimento deve essere verticale, orizzontale o entrambi.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) / [Guida](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Esempio ScrollView](layouts-images/ScrollView.png "esempio ScrollView")](layouts-images/ScrollView-Large.png#lightbox "esempio ScrollView")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/) Visualizza il contenuto con un modello di controllo ed è la classe base per [ `ContentView` ](#contentView).<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/) / [Guida](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Esempio TemplatedView](layouts-images/TemplatedView.png "TemplatedView esempio")](layouts-images/TemplatedView.png#lightbox "TemplatedView esempio") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) è un gestore di layout per le viste basate su modelli, utilizzato all'interno di un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) per contrassegnare in cui viene visualizzato il contenuto che deve essere presentata.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) / [Guida](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Esempio di ContentPresenter](layouts-images/ContentPresenter.png "esempio ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "esempio ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Layout con più elementi figlio

Queste classi derivano da [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/).

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) Posiziona gli elementi figlio in uno stack orizzontalmente o verticalmente in base il [ `Orientation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Orientation/) proprietà. Il [ `Spacing` ](https://developer.xamarin.com/api/property/Xamarin.Forms.StackLayout.Spacing/) proprietà determina la spaziatura tra gli elementi figlio e ha un valore predefinito di 6.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) / [Guida](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)| [![Esempio StackLayout](layouts-images/StackLayout.png "StackLayout esempio")](layouts-images/StackLayout-Large.png#lightbox "StackLayout esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grid

|     |     |
| --- | --- |
| [`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) Posiziona gli elementi figlio in una griglia di righe e colonne. Posizione di un elemento figlio è indicato il [le proprietà associate](~/xamarin-forms/xaml/attached-properties.md) [ `Row` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowProperty/), [ `Column` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnProperty/), [ `RowSpan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowSpanProperty/), e [ `ColumnSpan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnSpanProperty/).<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) / [Guida](~/xamarin-forms/user-interface/layouts/grid.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Esempio di Grid](layouts-images/Grid.png "esempio griglia")](layouts-images/Grid-Large.png#lightbox "esempio griglia")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) Posiziona gli elementi figlio in percorsi specifici rispetto al padre. Posizione di un elemento figlio è indicato il [le proprietà associate](~/xamarin-forms/xaml/attached-properties.md) [ `LayoutBounds` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty/) e [ `LayoutFlags` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty/). Un `AbsoluteLayout` è utile per le posizioni delle visualizzazioni di animazione.<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) / [Guida](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Esempio AbsoluteLayout](layouts-images/AbsoluteLayout.png "esempio AbsoluteLayout")](layouts-images/AbsoluteLayout-Large.png#lightbox "AbsoluteLayout esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutdDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) con [codice](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/) Posiziona gli elementi figlio relativo al `RelativeLayout` stesso o di pari livello. Posizione di un elemento figlio è indicato il [le proprietà associate](~/xamarin-forms/xaml/attached-properties.md) impostate per gli oggetti di tipo [ `Constraint` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Constraint/) e [ `BoundsConstraint` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Constraint/).<br /><br />[La documentazione dell'API](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/) / [Guida](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Esempio RelativeLayout](layouts-images/RelativeLayout.png "RelativeLayout esempio")](layouts-images/RelativeLayout-Large.png#lightbox "RelativeLayout esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) è basato sul foglio di stile CSS [modulo di Layout casella flessibile](http://www.w3.org/TR/css-flexbox-1/), comunemente noti come _flex layout_ oppure _flex-box_. `FlexLayout` Definisce proprietà associabile sei e cinque proprietà associabile associate che gli elementi figlio possano essere disposti in pila o racchiuso tra parentesi molte opzioni di allineamento e orientamento.<br /><br />[La documentazione dell'API](xref:Xamarin.Forms.FlexLayout) / [Guida](~/xamarin-forms/user-interface/layouts/flex-layout.md) / [esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/) | [![Esempio FlexLayout](layouts-images/FlexLayout.png "esempio FlexLayout")](layouts-images/FlexLayout-Large.png#lightbox "FlexLayout esempio")<br />[Il codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Introduction to Xamarin.Forms (Introduzione a Xamarin.Forms)](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Esempio FormsGallery xamarin. Forms](https://developer.xamarin.com/samples/FormsGallery/)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentazione di xamarin. Forms API](https://developer.xamarin.com/api/root/Xamarin.Forms/)
