---
title: Riepilogo del capitolo 27. Renderer personalizzati
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 27. Renderer personalizzati'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 49961953-9336-4FD4-A42F-6D9B05FF52E7
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: fd4014fa4db4e90596c100d454cf0467512240a4
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760497"
---
# <a name="summary-of-chapter-27-custom-renderers"></a>Riepilogo del capitolo 27. Renderer personalizzati

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

Viene eseguito il rendering di un elemento Novell. Forms, ad esempio `Button`, con un pulsante specifico della piattaforma incapsulato in una classe denominata `ButtonRenderer`.  Di seguito è illustrata la [versione di `ButtonRenderer`iOS ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), la [versione di `ButtonRenderer`di Android ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)e la [versione UWP di `ButtonRenderer`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs).

In questo capitolo illustra come scrivere il proprio renderer per creare viste personalizzate che eseguono il mapping agli oggetti specifici della piattaforma.

## <a name="the-complete-class-hierarchy"></a>La gerarchia di classe completo

Esistono quattro assembly che contengono il codice specifico della piattaforma xamarin. Forms.
È possibile visualizzare l'origine su GitHub tramite i collegamenti seguenti:

- [**Novell. Forms. Platform**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (molto piccola)
- [**Novell. Forms. Platform. iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Novell. Forms. Platform. Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Novell. Forms. Platform. UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> Gli assembly `WinRT` citati nel libro non fanno più parte di questa soluzione. 

Nell'esempio [**PlatformClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) viene visualizzata una gerarchia di classi per gli assembly validi per la piattaforma in esecuzione.

Si noterà una classe importante denominata `ViewRenderer`. Si tratta della classe che è derivare da durante la creazione di un renderer specifico della piattaforma. Esiste in tre versioni diverse, poiché quest'ultima è associata al sistema di vista della piattaforma di destinazione:

Il [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) iOS presenta argomenti generici:

- `TView` vincolato a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` vincolato a [`UIKit.UIView`](xref:UIKit.UIView)

Il [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) Android presenta argomenti generici:

- `TView` vincolato a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView` vincolato a [`Android.Views.View`](xref:Android.Views.View)

Il [`ViewRenderer<TElement, TNativeElement>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) UWP ha argomenti generici denominati in modo diverso:

- `TElement` vincolato a [`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement` vincolato a [`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

Quando si scrive un renderer, viene derivata una classe da `View`e quindi vengono scritte più classi `ViewRenderer`, una per ogni piattaforma supportata. Ogni implementazione specifica della piattaforma fa riferimento a una classe nativa che deriva dal tipo specificato come parametro `TNativeView` o `TNativeElement`.

## <a name="hello-custom-renderers"></a>Hello, renderer personalizzati.

Il programma [**HelloRenderers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) fa riferimento a una vista personalizzata denominata `HelloView` nella relativa classe [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) .

La classe [`HelloView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) è inclusa nel progetto **HelloRenderers** e deriva semplicemente da `View`.

La classe [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) nel progetto **HelloRenderers. iOS** deriva da `ViewRenderer<HelloView, UILabel>`. Nella `OnElementChanged` override crea una `UILabel` iOS nativa e chiama `SetNativeControl`.

La classe [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) nel progetto **HelloRenderers. Droid** deriva da `ViewRenderer<HelloView, TextView>`. Nel `OnElementChanged` eseguire l'override, viene creato un `TextView` Android e viene chiamato `SetNativeControl`.

La classe [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) in **HelloRenderers. UWP** e altri progetti Windows derivano da `ViewRenderer<HelloView, TextBlock>`. Nel `OnElementChanged` eseguire l'override, viene creato un `TextBlock` Windows e viene chiamato `SetNativeControl`.

Tutti i derivati `ViewRenderer` contengono un attributo `ExportRenderer` a livello di assembly che associa la classe `HelloView` a una determinata classe `HelloViewRenderer`. Si tratta di modalità di individuazione di renderer nei singoli progetti di piattaforma xamarin. Forms:

[![Schermata tripla della visualizzazione Hello](images/ch27fg02-small.png "Renderer personalizzati")](images/ch27fg02-large.png#lightbox "Renderer personalizzati")

## <a name="renderers-and-properties"></a>Renderer e proprietà

Il set successivo di renderer implementa il disegno di ellissi e si trova nei vari progetti della soluzione [**Novell. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) .

La classe [`EllipseView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) si trova nella piattaforma **Novell. FormsBook. Platform** . La classe è simile a `BoxView` e definisce solo una singola proprietà: `Color` di tipo `Color`.

I renderer possono trasferire i valori di proprietà impostati in un `View` all'oggetto nativo eseguendo l'override del metodo `OnElementPropertyChanged` nel renderer. All'interno di questo metodo ed entro la maggior parte dei renderer, sono disponibili due proprietà:

- `Element`, elemento Novell. Forms
- `Control`, visualizzazione nativa o oggetto widget o controllo

I tipi di queste proprietà sono determinati dai parametri generici da `ViewRenderer`. In questo esempio `Element` è di tipo `EllipseView`.

L'override del `OnElementPropertyChanged` può pertanto trasferire il valore `Color` della `Element` all'oggetto `Control` nativo, probabilmente con qualche tipo di conversione. I renderer di tre sono:

- iOS: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs), che usa una classe [`EllipseUIView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) per l'ellisse.
- Android: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs), che usa una classe [`EllipseDrawableView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) per l'ellisse.
- UWP: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs), che può utilizzare la classe nativa [`Ellipse`](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) di Windows.

La classe [**EllipseDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) Visualizza molti di questi oggetti `EllipseView`:

[![Schermata tripla della demo ellisse](images/ch27fg03-small.png "Renderer personalizzati EllipseView")](images/ch27fg03-large.png#lightbox "Renderer personalizzati EllipseView")

Il [**BouncingBall**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) rimbalza un `EllipseView` all'esterno dei lati dello schermo.

## <a name="renderers-and-events"></a>I renderer ed eventi

È anche possibile per i renderer indirettamente generare eventi. La classe [`StepSlider`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) è simile al normale Novell. forms `Slider` ma consente di specificare una serie di passaggi discreti tra i valori di `Minimum` e `Maximum`.

I renderer di tre sono:

- iOS: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP: [`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

I renderer rilevano le modifiche apportate al controllo nativo e quindi chiamano `SetValueFromRenderer`, che fa riferimento a una proprietà associabile definita nell'`StepSlider`, una modifica a che determina l'attivazione del `StepSlider` un evento `ValueChanged`.

L'esempio [**StepSliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) illustra questo nuovo dispositivo di scorrimento.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 27 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Esempi del capitolo 27](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
