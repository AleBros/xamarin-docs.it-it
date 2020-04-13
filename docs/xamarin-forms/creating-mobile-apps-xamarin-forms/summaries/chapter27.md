---
title: Riassunto del capitolo 27. Renderer personalizzati
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 27. Renderer personalizzati'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: fd4014fa4db4e90596c100d454cf0467512240a4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760497"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Riassunto del capitolo 27. Renderer personalizzati

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui Xamarin.Forms si è discostata dal materiale presentato nel libro.

Un elemento Xamarin.Forms, ad `Button` esempio, viene eseguito il `ButtonRenderer`rendering con un pulsante specifico della piattaforma incapsulato in una classe denominata .  Ecco la [versione iOS `ButtonRenderer`di ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), la versione Android [di `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)e la versione [UWP di `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs).

In questo capitolo viene illustrato come è possibile scrivere i propri renderer per creare visualizzazioni personalizzate che eseguono il mapping a oggetti specifici della piattaforma.

## <a name="the-complete-class-hierarchy"></a>Gerarchia completa delle classi

Esistono quattro assembly che contengono il codice specifico della piattaforma Xamarin.Forms.
È possibile visualizzare l'origine su GitHub usando questi collegamenti:You can view the source on GitHub using these links:

- [**Xamarin.Forms.Piattaforma**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (molto piccola)
- [**Xamarin.Forms.Platform.iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms.Platform.Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms.Piattaforma.UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> Le `WinRT` assemblee menzionate nel libro non fanno più parte di questa soluzione. 

[**Nell'esempio PlatformClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) viene visualizzata una gerarchia di classi per gli assembly validi per la piattaforma in esecuzione.

Si noterà una classe `ViewRenderer`importante denominata . Questa è la classe da cui si deriva quando si crea un renderer specifico della piattaforma. Esiste in tre diverse versioni, perché è legato al sistema di visualizzazione della piattaforma di destinazione:

Il iOS ha argomenti generici:The iOS [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) has generic arguments:

- `TView`vincolato a[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView`vincolato a[`UIKit.UIView`](xref:UIKit.UIView)

L'Android [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) ha argomenti generici:

- `TView`vincolato a[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView`vincolato a[`Android.Views.View`](xref:Android.Views.View)

La piattaforma [`ViewRenderer<TElement, TNativeElement>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) UWP ha argomenti generici denominati in modo diverso:The UWP has differently named generic arguments:

- `TElement`vincolato a[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement`vincolato a[`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

Quando si scrive un renderer, si `View`deriva una classe `ViewRenderer` da , quindi si scrivono più classi, una per ogni piattaforma supportata. Ogni implementazione specifica della piattaforma farà riferimento a una classe `TNativeView` `TNativeElement` nativa che deriva dal tipo specificato come parametro o .

## <a name="hello-custom-renderers"></a>Ciao, renderer personalizzati!

Il programma [**HelloRenderers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) fa `HelloView` riferimento [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) a una visualizzazione personalizzata denominata nella relativa classe.

La [`HelloView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) classe è inclusa nel progetto **HelloRenderers** e deriva semplicemente da `View`.

La [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) classe nel progetto **HelloRenderers.iOS** deriva da `ViewRenderer<HelloView, UILabel>`. Nell'override, `OnElementChanged` crea un iOS `UILabel` `SetNativeControl`nativo e chiama .

La [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) classe nel progetto **HelloRenderers.Droid** deriva da `ViewRenderer<HelloView, TextView>`. Nell'override, `OnElementChanged` crea un `TextView` Android `SetNativeControl`e chiama .

La [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) classe nel **file HelloRenderers.UWP** e `ViewRenderer<HelloView, TextBlock>`altri progetti Windows deriva da . Nell'override `OnElementChanged` viene creato `TextBlock` un `SetNativeControl`oggetto Windows e viene chiamato .

Tutte `ViewRenderer` le derivate `ExportRenderer` contengono un attributo `HelloView` a livello `HelloViewRenderer` di assembly che associa la classe alla classe specifica. Questo è il modo in cui Xamarin.Forms individua i renderer nei singoli progetti di piattaforma:

[![Tripla schermata di Hello View](images/ch27fg02-small.png "Renderer personalizzati")](images/ch27fg02-large.png#lightbox "Renderer personalizzati")

## <a name="renderers-and-properties"></a>Renderer e proprietà

Il set successivo di renderer implementa il disegno dell'ellisse e si trova nei vari progetti della soluzione [**Xamarin.FormsBook.Platform.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)

La [`EllipseView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) classe si trova nella piattaforma **Xamarin.FormsBook.Platform.** La classe è `BoxView` simile e definisce `Color` solo `Color`una singola proprietà: di tipo .

I renderer possono trasferire i `View` valori delle proprietà impostati su un all'oggetto nativo eseguendo l'override del `OnElementPropertyChanged` metodo nel renderer. All'interno di questo metodo (e all'interno della maggior parte del renderer), sono disponibili due proprietà:

- `Element`, l'elemento Xamarin.Forms
- `Control`, la vista nativa o il widget o l'oggetto di controllo

I tipi di queste proprietà sono `ViewRenderer`determinati dai parametri generici di . In questo `Element` esempio, `EllipseView`è di tipo .

L'override `OnElementPropertyChanged` può `Color` quindi trasferire `Element` il `Control` valore di all'oggetto nativo, probabilmente con un qualche tipo di conversione. I tre renderer sono:

- iOS: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), che [`EllipseUIView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) usa una classe per l'ellisse.
- Android: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), che [`EllipseDrawableView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) utilizza una classe per l'ellisse.
- UWP: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), che può [`Ellipse`](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) utilizzare la classe Windows nativa.

La classe [**EllipseDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) visualizza `EllipseView` diversi oggetti seguenti:

[![Triplo screenshot di Ellipse Demo](images/ch27fg03-small.png "Renderer personalizzati EllipseViewEllipseView Custom Renderers")](images/ch27fg03-large.png#lightbox "Renderer personalizzati EllipseViewEllipseView Custom Renderers")

Il [**BouncingBall**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) rimbalza `EllipseView` un fuori dai lati dello schermo.

## <a name="renderers-and-events"></a>Renderer ed eventi

È anche possibile che i renderer generino indirettamente eventi. La [`StepSlider`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) classe è simile al normale Xamarin.Forms, `Slider` ma consente di `Minimum` `Maximum` specificare una serie di passaggi discreti tra i valori e .

I tre renderer sono:

- Ios:[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android:[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- Uwp:[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

I renderer rilevano le modifiche al `SetValueFromRenderer`controllo nativo, quindi chiamano `StepSlider`, che fa riferimento `StepSlider` a `ValueChanged` una proprietà associabile definita in , una modifica a cui causa la gestione di un evento.

Il [**StepSliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) esempio viene illustrato questo nuovo dispositivo di scorrimento.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 27 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Capitolo 27 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
