---
title: Riepilogo del capitolo 27. Renderer personalizzati
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 27. Renderer personalizzati'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 1a992c192cb4d7fc24490257b67c1b851abcd949
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058344"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Riepilogo del capitolo 27. Renderer personalizzati

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

Un elemento di xamarin. Forms, ad esempio `Button` viene eseguito il rendering con un pulsante specifico della piattaforma è incapsulato in una classe denominata `ButtonRenderer`.  Di seguito è riportato il [versione di iOS `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), il [versione Android di `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)e il [versione UWP del `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs).

In questo capitolo illustra come scrivere il proprio renderer per creare viste personalizzate che eseguono il mapping agli oggetti specifici della piattaforma.

## <a name="the-complete-class-hierarchy"></a>La gerarchia di classe completo

Esistono quattro assembly che contengono il codice specifico della piattaforma xamarin. Forms.
È possibile visualizzare l'origine su GitHub tramite i collegamenti seguenti:

- [**Xamarin.Forms.Platform** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (piccola)
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> Il `WinRT` assembly indicati nel libro non fanno più parte di questa soluzione. 

Il [ **PlatformClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) nell'esempio vengono visualizzati una gerarchia di classi per gli assembly che sono validi per la piattaforma in esecuzione.

Si noterà una classe importante denominata `ViewRenderer`. Si tratta della classe che è derivare da durante la creazione di un renderer specifico della piattaforma. Esiste in tre versioni diverse, poiché quest'ultima è associata al sistema di vista della piattaforma di destinazione:

IOS [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) dispone di argomenti generici:

- `TView` vincolato a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` vincolato a [`UIKit.UIView`](https://developer.xamarin.com/api/type/UIKit.UIView/)

Android [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) dispone di argomenti generici:

- `TView` vincolato a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` vincolato a [`Android.Views.View`](https://developer.xamarin.com/api/type/Android.Views.View/)

La piattaforma UWP [ `ViewRenderer<TElement, TNativeElement>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) è denominato in modo diverso gli argomenti generici:

- `TElement` vincolato a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement` vincolato a [`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

Quando si scrive un renderer, si verrà derivando una classe dal `View`e quindi scrivere più `ViewRenderer` classi, uno per ogni piattaforma supportata. Ogni implementazione specifica della piattaforma farà riferimento a una classe nativa da cui deriva il tipo si specifica come il `TNativeView` o `TNativeElement` parametro.

## <a name="hello-custom-renderers"></a>Hello, renderer personalizzati.

Il [ **HelloRenderers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) programma fa riferimento a una visualizzazione personalizzata denominata `HelloView` nel relativo [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) classe.

Il [ `HelloView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) classe è inclusa nel **HelloRenderers** progetto e deriva semplicemente dalla `View`.

Il [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) classe la **HelloRenderers.iOS** progetto deriva da `ViewRenderer<HelloView, UILabel>`. Nel `OnElementChanged` sostituzione, viene creato una native per iOS `UILabel` e chiama `SetNativeControl`.

Il [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) classe la **HelloRenderers.Droid** progetto deriva da `ViewRenderer<HelloView, TextView>`. Nel `OnElementChanged` sostituzione, viene creato un Android `TextView` e chiama `SetNativeControl`.

Il [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) classe la **HelloRenderers.UWP** e altri progetti Windows deriva da `ViewRenderer<HelloView, TextBlock>`. Nel `OnElementChanged` sostituzione, viene creato un Windows `TextBlock` e chiama `SetNativeControl`.

Tutti i `ViewRenderer` derivati contengono un `ExportRenderer` attributo a livello di assembly che associa il `HelloView` con la particolare classe `HelloViewRenderer` classe. Si tratta di modalità di individuazione di renderer nei singoli progetti di piattaforma xamarin. Forms:

[![Schermata triplo della visualizzazione Hello](images/ch27fg02-small.png "renderer personalizzati")](images/ch27fg02-large.png#lightbox "renderer personalizzati")

## <a name="renderers-and-properties"></a>Renderer e proprietà

Il successivo set di renderer implementa disegno puntini di sospensione e si trova nei vari progetti del [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) soluzione.

Il [ `EllipseView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) classe si trova nel **Xamarin.FormsBook.Platform** piattaforma. La classe è simile a `BoxView` e definisce solo una singola proprietà: `Color` di tipo `Color`.

I renderer possono trasferire i valori delle proprietà impostare su un `View` all'oggetto nativo eseguendo l'override di `OnElementPropertyChanged` nel renderer del metodo. All'interno di questo metodo ed entro la maggior parte dei renderer, sono disponibili due proprietà:

- `Element`, l'elemento di xamarin. Forms
- `Control`, la visualizzazione nativa o l'oggetto di widget o di un controllo

I tipi di queste proprietà sono determinati dai parametri generici per `ViewRenderer`. In questo esempio `Element` JE typu `EllipseView`.

Il `OnElementPropertyChanged` override può quindi trasferire le `Color` pari al `Element` all'oggetto nativo `Control` oggetto, probabilmente con un tipo di conversione. I renderer di tre sono:

- iOS: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), che usa un' [ `EllipseUIView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) classe dell'ellisse.
- Android: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), che usa un' [ `EllipseDrawableView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) classe dell'ellisse.
- Piattaforma UWP: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), che può utilizzare il Windows native [ `Ellipse` ](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) classe.

Il [ **EllipseDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) classe sono visualizzati alcuni di questi `EllipseView` oggetti:

[![Schermata triplo della Demo ellisse](images/ch27fg03-small.png "renderer personalizzati EllipseView")](images/ch27fg03-large.png#lightbox "renderer personalizzati EllipseView")

Il [ **BouncingBall** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) rimbalza un `EllipseView` disattivato i lati della schermata.

## <a name="renderers-and-events"></a>I renderer ed eventi

È anche possibile per i renderer indirettamente generare eventi. Il [ `StepSlider` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) classe è simile a xamarin. Forms normale `Slider` ma consente di specificare un numero di passaggi discreti tra il `Minimum` e `Maximum` valori.

I renderer di tre sono:

- iOS: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- PIATTAFORMA UWP: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

I renderer di rilevare le modifiche apportate al controllo nativo e quindi chiamare `SetValueFromRenderer`, che fa riferimento a una proprietà associabile definita nel `StepSlider`, una modifica che causa il `StepSlider` attivano un `ValueChanged` evento.

Il [ **StepSliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) esempio viene illustrato questo nuovo dispositivo di scorrimento.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 27 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Capitolo 27 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
