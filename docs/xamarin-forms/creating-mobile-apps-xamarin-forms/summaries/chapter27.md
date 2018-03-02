---
title: Riepilogo del capitolo 27. Renderer personalizzato
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 0c1dc9ba5cf382551a1142110c68d16421db07e4
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Riepilogo del capitolo 27. Renderer personalizzato

Un elemento di xamarin. Forms, ad esempio `Button` viene eseguito il rendering con un pulsante specifico della piattaforma incapsulato in una classe denominata `ButtonRenderer`.  Ecco il [versione di iOS `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), [versione Android di `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)e il [versione di Windows Runtime di `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.WinRT/ButtonRenderer.cs).

In questo capitolo viene illustrato come scrivere il propria renderer per creare visualizzazioni personalizzate che eseguono il mapping agli oggetti specifici della piattaforma.

## <a name="the-complete-class-hierarchy"></a>Gerarchia di classe completo

Sono disponibili sette assembly che contengono il codice specifico della piattaforma xamarin. Forms.
È possibile visualizzare l'origine su GitHub con questi collegamenti:

- [**Xamarin.Forms.Platform** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (piccola)
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Platform.WinRT** ](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT) (maggiore di tre successivo)
- [**Xamarin.Forms.Platform.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)
- [**Xamarin.Forms.Platform.WinRT.Tablet**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT.Tablet)
- [**Xamarin.Forms.Platform.WinRT.Phone**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.WinRT.Phone)

Il [ **PlatformClassHierarchy** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) esempio consente di visualizzare una gerarchia di classi per gli assembly che sono validi per la piattaforma di esecuzione.

Si noterà una classe importante denominata `ViewRenderer`. Questa è la classe di derivazione durante la creazione di un renderer specifico della piattaforma. Esiste in tre versioni diverse, poiché è legato al sistema di vista della piattaforma di destinazione:

IOS [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L26) dispone di argomenti generici:

- `TView` vincolato a [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeView` vincolato a [`UIKit.UIView`](https://developer.xamarin.com/api/type/UIKit.UIView/)

Il Android [ `ViewRenderer<TView, TNativeView>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L14) dispone di argomenti generici:

- `TView` vincolato a [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeView` vincolato a [`Android.Views.View`](https://developer.xamarin.com/api/type/Android.Views.View/)

Windows Runtime [ `ViewRenderer<TElement, TNativeElement>` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.WinRT/ViewRenderer.cs#L12) è denominato in modo diverso gli argomenti generici:

- `TElement` vincolato a [`Xamarin.Forms.View`](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)
- `TNativeElement` vincolato a [`Windows.UI.Xaml.FrameworkElement`](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.frameworkelement.aspx)

Quando si scrive un renderer, si verrà derivare una classe da `View`e quindi la scrittura di più `ViewRenderer` classi, uno per ogni piattaforma supportata. Ogni implementazione specifica della piattaforma farà riferimento a una classe nativa da cui deriva il tipo specificato come il `TNativeView` o `TNativeElement` parametro.

## <a name="hello-custom-renderers"></a>Hello, renderer personalizzato.

Il [ **HelloRenderers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) programma fa riferimento a una visualizzazione personalizzata denominata `HelloView` nel relativo [ `App` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) classe.

Il [ `HelloView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) classe è inclusa nel **HelloRenderers** del progetto e deriva semplicemente da `View`.

Il [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) classe il **HelloRenderers.iOS** progetto deriva da `ViewRenderer<HelloView, UILabel>`. Nel `OnElementChanged` sostituzione, viene creato un iOS nativo `UILabel` e chiama `SetNativeControl`.

Il [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) classe il **HelloRenderers.Droid** progetto deriva da `ViewRenderer<HelloView, TextView>`. Nel `OnElementChanged` sostituzione, viene creato un Android `TextView` e chiama `SetNativeControl`.

Il [ `HelloViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) classe il **HelloRenderers.UWP** e altri progetti Windows deriva da `ViewRenderer<HelloView, TextBlock>`. Nel `OnElementChanged` sostituzione, viene creato un Windows `TextBlock` e chiama `SetNativeControl`.

Tutti i `ViewRenderer` derivati contengono un `ExportRenderer` attributo a livello di assembly che associa il `HelloView` classe con la particolare `HelloViewRenderer` classe. Ecco come xamarin. Forms individua renderer nei progetti per piattaforma:

[![Schermata di triplo della vista Hello](images/ch27fg02-small.png "renderer personalizzati")](images/ch27fg02-large.png "renderer personalizzati")

## <a name="renderers-and-properties"></a>Renderer e proprietà

Il set successivo di renderer implementa disegno puntini di sospensione e si trova nei vari progetti del [ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) soluzione.

Il [ `EllipseView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) classe si trova nel **Xamarin.FormsBook.Platform** piattaforma. La classe è simile a `BoxView` e definisce solo una singola proprietà: `Color` di tipo `Color`.

Il renderer possibile trasferire i valori di proprietà impostati per un `View` all'oggetto nativo eseguendo l'override di `OnElementPropertyChanged` nel renderer del metodo. All'interno del metodo ed entro la maggior parte dei renderer, sono disponibili due proprietà:

- `Element`, l'elemento di xamarin. Forms
- `Control`, la visualizzazione o l'oggetto widget o di controllo

I tipi di queste proprietà sono determinati dai parametri generici per `ViewRenderer`. In questo esempio, `Element` è di tipo `EllipseView`.

Il `OnElementPropertyChanged` override, pertanto è possibile trasferire il `Color` valore il `Element` a nativo `Control` oggetto, probabilmente con un tipo di conversione. Il tre renderer sono:

- iOS: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), che utilizza un [ `EllipseUIView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) classe per l'ellisse.
- Android: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), che utilizza un [ `EllipseDrawableView` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) classe per l'ellisse.
- Windows Runtime: [ `EllipseViewRenderer` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), che possono utilizzare le finestre native [ `Ellipse` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.shapes.ellipse.aspx) classe.

Il [ **EllipseDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) classe Visualizza alcuni di questi `EllipseView` oggetti:

[![Schermata triplo della Demo ellisse](images/ch27fg03-small.png "renderer personalizzati EllipseView")](images/ch27fg03-large.png "renderer personalizzati EllipseView")

Il [ **BouncingBall** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) aperture un `EllipseView` off i lati della schermata.

## <a name="renderers-and-events"></a>Renderer ed eventi

È inoltre possibile per renderer indirettamente generare eventi. Il [ `StepSlider` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) classe è simile al normale xamarin. Forms `Slider` ma consente di specificare un numero di passaggi distinti tra il `Minimum` e `Maximum` valori.

Il tre renderer sono:

- iOS: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- Windows Runtime: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

Il renderer di rilevare le modifiche apportate al controllo nativo e quindi chiamare `SetValueFromRenderer`, che fa riferimento a una proprietà associabile definita nel `StepSlider`, una modifica che causa il `StepSlider` per generare un `ValueChanged` evento.

Il [ **StepSliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) esempio viene illustrato questo nuovo dispositivo di scorrimento.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 27 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Esempi di capitolo 27](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
