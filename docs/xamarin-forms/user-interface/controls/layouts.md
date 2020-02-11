---
title: Layout di Xamarin.Forms
description: Layout di Xamarin.Forms vengono utilizzati per la composizione di controlli dell'interfaccia utente nelle strutture visual. Questo articolo elenca i layout inclusi in Xamarin.Forms.
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
ms.openlocfilehash: e247be8387ce984d6695431ec432119d01344b42
ms.sourcegitcommit: 211fed94fb96127a3e158ae1ff5d7eb831a203d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75955774"
---
# <a name="xamarinforms-layouts"></a>Layout di Xamarin.Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Layout di Xamarin.Forms vengono utilizzati per comporre i controlli dell'interfaccia utente in strutture visual._

Il [ `Layout` ](xref:Xamarin.Forms.Layout) e [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) classi in Xamarin.Forms sono specializzati sottotipi di viste che fungono da contenitori per le visualizzazioni e altri layout. Il `Layout` stessa classe deriva da [ `View` ](views.md). Oggetto `Layout` derivato contiene in genere per la logica per impostare la posizione e dimensioni degli elementi figlio nelle applicazioni Xamarin.Forms.

[![Tipi di layout Xamarin.Forms](layouts-images/layouts-sml.png "Tipi di layout Xamarin.Forms")](layouts-images/layouts.png#lightbox "Tipi di layout Xamarin.Forms")

Le classi che derivano da `Layout` possono essere suddivisi in due categorie:

## <a name="layouts-with-single-content"></a>Layout con contenuto singolo

Tali classi derivano da [ `Layout` ](xref:Xamarin.Forms.Layout), che definisce [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) e [ `IsClippedToBounds` ](xref:Xamarin.Forms.Layout.IsClippedToBounds) proprietà.

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView) contiene un singolo elemento figlio che viene impostato con il [ `Content` ](xref:Xamarin.Forms.ContentView.Content) proprietà. Il `Content` proprietà può essere impostata su qualsiasi `View` derivati, inclusi altri `Layout` derivati. `ContentView` viene principalmente utilizzato come un elemento strutturale e funge da classe base per [ `Frame` ](#frame).<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.ContentView) / [Guida](~/xamarin-forms/user-interface/layouts/contentview.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-cardview/) | [![Esempio di ContentView](layouts-images/ContentView.png "Esempio di ContentView")](layouts-images/ContentView-Large.png#lightbox "Esempio di ContentView")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Frame

|     |     |
| --- | --- |
| La classe [`Frame`](xref:Xamarin.Forms.Frame) deriva da [`ContentView`](#contentView) e visualizza un bordo, o frame, intorno al relativo elemento figlio. La classe `Frame` ha un valore [`Padding`](xref:Xamarin.Forms.Layout.Padding) predefinito pari a 20 e definisce anche le proprietà [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor), [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius)e [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) .<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Frame) / [Guida](~/xamarin-forms/user-interface/layouts/frame.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/) | [![Esempio di frame](layouts-images/Frame.png "Esempio di frame")](layouts-images/Frame-Large.png#lightbox "Esempio di frame")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView) è in grado di scorrere il contenuto. Impostare il [ `Content` ](xref:Xamarin.Forms.ScrollView.Content) proprietà su una vista o a un layout troppo grande per adattarlo sullo schermo. (Il contenuto di un `ScrollView` molto spesso è una [ `StackLayout` ](#stackLayout).) Impostare il [ `Orientation` ](xref:Xamarin.Forms.ScrollView.Orientation) proprietà che indica se lo scorrimento dovrebbe essere verticale, orizzontale, o entrambi.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.ScrollView) / [Guida](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Esempio di ScrollView](layouts-images/ScrollView.png "Esempio di ScrollView")](layouts-images/ScrollView-Large.png#lightbox "Esempio di ScrollView")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) Visualizza il contenuto con un modello di controllo ed è la classe base per [ `ContentView` ](#contentView).<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.TemplatedView) / [Guida](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Esempio di TemplatedView](layouts-images/TemplatedView.png "Esempio di TemplatedView")](layouts-images/TemplatedView.png#lightbox "Esempio di TemplatedView") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) rappresenta un gestore di layout per le viste basate su modelli, utilizzato all'interno di un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) per contrassegnare in cui viene visualizzato il contenuto che deve essere presentato.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.ContentPresenter) / [Guida](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Esempio di ContentPresenter](layouts-images/ContentPresenter.png "Esempio di ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "Esempio di ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Layout con più elementi figlio

Tali classi derivano da [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1).

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout) Posiziona gli elementi figlio in uno stack orizzontalmente o verticalmente in base il [ `Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation) proprietà. Il [ `Spacing` ](xref:Xamarin.Forms.StackLayout.Spacing) proprietà determina la spaziatura tra gli elementi figlio e ha un valore predefinito pari a 6.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.StackLayout) / [Guida](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)| [![Esempio di StackLayout](layouts-images/StackLayout.png "Esempio di StackLayout")](layouts-images/StackLayout-Large.png#lightbox "Esempio di StackLayout")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grid

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid) Posiziona gli elementi figlio in una griglia di righe e colonne. Posizione del figlio sia indicato utilizzando il [le proprietà associate](~/xamarin-forms/xaml/attached-properties.md) [ `Row` ](xref:Xamarin.Forms.Grid.RowProperty), [ `Column` ](xref:Xamarin.Forms.Grid.ColumnProperty), [ `RowSpan` ](xref:Xamarin.Forms.Grid.RowSpanProperty), e [ `ColumnSpan` ](xref:Xamarin.Forms.Grid.ColumnSpanProperty).<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.Grid) / [Guida](~/xamarin-forms/user-interface/layouts/grid.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Esempio di griglia](layouts-images/Grid.png "Esempio di griglia")](layouts-images/Grid-Large.png#lightbox "Esempio di griglia")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) Posiziona gli elementi figlio in percorsi specifici rispetto al padre. Posizione del figlio sia indicato utilizzando il [le proprietà associate](~/xamarin-forms/xaml/attached-properties.md) [ `LayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) e [ `LayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty). Un `AbsoluteLayout` è utile per l'animazione le posizioni delle visualizzazioni.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.AbsoluteLayout) / [Guida](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Esempio di AbsoluteLayout](layouts-images/AbsoluteLayout.png "Esempio di AbsoluteLayout")](layouts-images/AbsoluteLayout-Large.png#lightbox "Esempio di AbsoluteLayout")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutdDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) con [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) Posiziona gli elementi figlio relativa al `RelativeLayout` stesso o per i nodi di pari livello. Posizione del figlio sia indicato utilizzando il [le proprietà associate](~/xamarin-forms/xaml/attached-properties.md) impostati su oggetti di tipo [ `Constraint` ](xref:Xamarin.Forms.Constraint) e [ `BoundsConstraint` ](xref:Xamarin.Forms.Constraint).<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.RelativeLayout) / [Guida](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Esempio di sul relativelayout](layouts-images/RelativeLayout.png "Esempio di sul relativelayout")](layouts-images/RelativeLayout-Large.png#lightbox "Esempio di sul relativelayout")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) è basato sul foglio di stile CSS [flessibile Box Layout Module](https://www.w3.org/TR/css-flexbox-1/), noto come _flex layout_ oppure _flex-finestra_. `FlexLayout` definisce le proprietà associabili sei e cinque proprietà associabili associate che gli elementi figlio possano essere disposti in pila o stato eseguito il wrapping con molte opzioni di allineamento e orientamento.<br /><br />[Documentazione dell'API](xref:Xamarin.Forms.FlexLayout) / [Guida](~/xamarin-forms/user-interface/layouts/flex-layout.md) / [esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos) | [![Esempio di FlexLayout](layouts-images/FlexLayout.png "Esempio di FlexLayout")](layouts-images/FlexLayout-Large.png#lightbox "Esempio di FlexLayout")<br />[Codice c# per questa pagina](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs) / [pagina XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Xamarin.Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Esempi di Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentazione per le API di Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
