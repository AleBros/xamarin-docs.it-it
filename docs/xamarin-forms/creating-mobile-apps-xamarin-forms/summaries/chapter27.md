---
title: "Riepilogo del capitolo 27. Renderer personalizzati "Description:" creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 27. Renderer personalizzati "ms. prod: Novell MS. Technology: Novell-Forms ms. AssetID: 49961953-9336-4FD4-A42F-6D9B05FF52E7 Author: davidbritch ms. Author: dabritch ms. Date: 07/18/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-27-custom-renderers"></a>Riepilogo del capitolo 27. Renderer personalizzati

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)

> [!NOTE] 
> Le note in questa pagina indicano Xamarin.Forms le aree in cui è stato divergente rispetto al materiale presentato nel libro.

Xamarin.FormsViene eseguito il rendering di un elemento, ad esempio, `Button` con un pulsante specifico della piattaforma incapsulato in una classe denominata `ButtonRenderer` .  Di seguito è illustrata la versione di [iOS di `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/Renderers/ButtonRenderer.cs), la [versione di Android di `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/Renderers/ButtonRenderer.cs)e la [versione UWP di `ButtonRenderer` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ButtonRenderer.cs).

In questo capitolo viene illustrato come scrivere renderer personalizzati per creare visualizzazioni personalizzate che eseguono il mapping a oggetti specifici della piattaforma.

## <a name="the-complete-class-hierarchy"></a>Gerarchia di classi completa

Sono disponibili quattro assembly che contengono il Xamarin.Forms codice specifico della piattaforma.
È possibile visualizzare l'origine in GitHub usando i collegamenti seguenti:

- [**Xamarin.Forms. Piattaforma**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform) (molto piccola)
- [**Xamarin.Forms. Platform. iOS**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.iOS)
- [**Xamarin.Forms. Platform. Android**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.Android)
- [**Xamarin.Forms. Platform. UAP**](https://github.com/xamarin/Xamarin.Forms/tree/master/Xamarin.Forms.Platform.UAP)

> [!NOTE]
> Gli `WinRT` assembly indicati nel libro non fanno più parte di questa soluzione. 

Nell'esempio [**PlatformClassHierarchy**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/PlatformClassHierarchy) viene visualizzata una gerarchia di classi per gli assembly validi per la piattaforma in esecuzione.

Si noterà una classe importante denominata `ViewRenderer` . Si tratta della classe da cui deriva quando si crea un renderer specifico della piattaforma. Esiste in tre diverse versioni, perché è legata al sistema di visualizzazione della piattaforma di destinazione:

IOS [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.iOS/ViewRenderer.cs#L25) presenta argomenti generici:

- `TView`vincolato a[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView`vincolato a[`UIKit.UIView`](xref:UIKit.UIView)

Android [`ViewRenderer<TView, TNativeView>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.Android/ViewRenderer.cs#L17) presenta argomenti generici:

- `TView`vincolato a[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeView`vincolato a[`Android.Views.View`](xref:Android.Views.View)

Il UWP [`ViewRenderer<TElement, TNativeElement>`](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Platform.UAP/ViewRenderer.cs#L6) ha argomenti generici denominati in modo diverso:

- `TElement`vincolato a[`Xamarin.Forms.View`](xref:Xamarin.Forms.View)
- `TNativeElement`vincolato a[`Windows.UI.Xaml.FrameworkElement`](/uwp/api/Windows.UI.Xaml.FrameworkElement)

Quando si scrive un renderer, viene derivata una classe da `View` e quindi si scrivono più `ViewRenderer` classi, una per ogni piattaforma supportata. Ogni implementazione specifica della piattaforma fa riferimento a una classe nativa che deriva dal tipo specificato come `TNativeView` `TNativeElement` parametro o.

## <a name="hello-custom-renderers"></a>Hello, renderer personalizzati

Il programma [**HelloRenderers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/HelloRenderers) fa riferimento a una vista personalizzata denominata `HelloView` nella relativa [`App`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/App.cs) classe.

La [`HelloView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers/HelloView.cs) classe è inclusa nel progetto **HelloRenderers** e deriva semplicemente da `View` .

La [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.iOS/HelloViewRenderer.cs) classe nel progetto **HelloRenderers. iOS** deriva da `ViewRenderer<HelloView, UILabel>` . Nell' `OnElementChanged` override crea un iOS nativo `UILabel` e chiama `SetNativeControl` .

La [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.Droid/HelloViewRenderer.cs) classe nel progetto **HelloRenderers. Droid** deriva da `ViewRenderer<HelloView, TextView>` . Nell' `OnElementChanged` override crea un Android `TextView` e chiama `SetNativeControl` .

La [`HelloViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter27/HelloRenderers/HelloRenderers/HelloRenderers.UWP/HelloViewRenderer.cs) classe in **HELLORENDERERS. UWP** e altri progetti Windows derivano da `ViewRenderer<HelloView, TextBlock>` . Nell' `OnElementChanged` override crea una finestra `TextBlock` e chiama `SetNativeControl` .

Tutti i `ViewRenderer` derivati contengono un `ExportRenderer` attributo a livello di assembly che associa la `HelloView` classe alla classe specifica `HelloViewRenderer` . Questo è il modo Xamarin.Forms in cui individua i renderer nei singoli progetti della piattaforma:

[![Schermata tripla della visualizzazione Hello](images/ch27fg02-small.png "Renderer personalizzati")](images/ch27fg02-large.png#lightbox "Renderer personalizzati")

## <a name="renderers-and-properties"></a>Renderer e proprietà

Il set successivo di renderer implementa il disegno di ellissi e si trova nei vari progetti della soluzione [**Novell. FormsBook. Platform**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform) .

La [`EllipseView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/EllipseView.cs) classe si trova nella piattaforma **Novell. FormsBook. Platform** . La classe è simile a `BoxView` e definisce solo una singola proprietà: `Color` di tipo `Color` .

I renderer possono trasferire i valori delle proprietà impostati su un `View` oggetto all'oggetto nativo eseguendo l'override del `OnElementPropertyChanged` metodo nel renderer. All'interno di questo metodo (e nella maggior parte dei renderer) sono disponibili due proprietà:

- `Element`, l' Xamarin.Forms elemento
- `Control`, la visualizzazione nativa, il widget o l'oggetto controllo

I tipi di queste proprietà sono determinati dai parametri generici a `ViewRenderer` . In questo esempio `Element` è di tipo `EllipseView` .

L' `OnElementPropertyChanged` override può quindi trasferire il `Color` valore di `Element` all' `Control` oggetto nativo, probabilmente con qualche tipo di conversione. I tre renderer sono:

- iOS: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseViewRenderer.cs) , che usa una [`EllipseUIView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/EllipseUIView.cs) classe per l'ellisse.
- Android: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseViewRenderer.cs) , che usa una [`EllipseDrawableView`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/EllipseDrawableView.cs) classe per l'ellisse.
- UWP: [`EllipseViewRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/EllipseViewRenderer.cs) , che può utilizzare la classe nativa di Windows [`Ellipse`](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse) .

La classe [**EllipseDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/EllipseDemo) Visualizza molti di questi `EllipseView` oggetti:

[![Schermata tripla della demo ellisse](images/ch27fg03-small.png "Renderer personalizzati EllipseView")](images/ch27fg03-large.png#lightbox "Renderer personalizzati EllipseView")

Il [**BouncingBall**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/BouncingBall) rimbalza sul `EllipseView` lato dello schermo.

## <a name="renderers-and-events"></a>Renderer ed eventi

È anche possibile che i renderer generino gli eventi in modo indiretto. La [`StepSlider`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/StepSlider.cs) classe è simile alla normale Xamarin.Forms `Slider` , ma consente di specificare una serie di passaggi discreti tra i `Minimum` `Maximum` valori e.

I tre renderer sono:

- iOS[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/StepSliderRenderer.cs)
- Android[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/StepSliderRenderer.cs)
- UWP[`StepSliderRenderer`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/StepSliderRenderer.cs)

I renderer rilevano le modifiche apportate al controllo nativo e quindi chiamano `SetValueFromRenderer` , che fa riferimento a una proprietà associabile definita in `StepSlider` , una modifica a che determina l'attivazione `StepSlider` di un evento da parte dell'oggetto `ValueChanged` .

L'esempio [**StepSliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27/StepSliderDemo) illustra questo nuovo dispositivo di scorrimento.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 27 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch27-Apr2016.pdf)
- [Esempi del capitolo 27](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter27)
- [Renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
