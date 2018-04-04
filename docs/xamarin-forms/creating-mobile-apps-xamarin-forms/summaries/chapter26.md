---
title: Riepilogo del capitolo 26. Layout personalizzati
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 5d2dc3e809026a36d186c9a582fcd047f6be24fe
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Riepilogo del capitolo 26. Layout personalizzati

Xamarin. Forms include diverse classi derivate da [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/):

* `StackLayout`,
* `Grid`,
* `AbsoluteLayout` e
* `RelativeLayout`.

In questo capitolo viene illustrato come creare le classi che derivano da `Layout<View>`.

## <a name="an-overview-of-layout"></a>Una panoramica del layout

Non è centralizzato sistema che gestisce il layout di xamarin. Forms. Ogni elemento è responsabile di determinare quale devono essere relative dimensioni e su come eseguire il rendering all'interno di una determinata area.

### <a name="parents-and-children"></a>Elementi padre e figlio

Ogni elemento che dispone di elementi figlio è responsabile per il posizionamento di tali elementi figlio all'interno di se stesso. È l'elemento padre per la determinazione di ciò che ridimensionare gli elementi figlio deve essere basata sulla dimensione è disponibile e desidera inoltre le dimensioni, l'elemento figlio.

### <a name="sizing-and-positioning"></a>Ridimensionamento e posizionamento

Layout inizia nella parte superiore dell'albero visuale con la pagina e quindi procede attraverso tutti i rami. Il metodo pubblico più importante nel layout è [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) definito da `VisualElement`. Ogni elemento che è un elemento padre di altre chiamate di elementi `Layout` per ognuno dei relativi elementi figlio per specificare l'elemento figlio, dimensioni e posizione rispetto alla stessa sotto forma di un [ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/) valore. Questi `Layout` chiamate propagano la struttura ad albero visuale.

Una chiamata a `Layout` è obbligatorio per un elemento da visualizzare sullo schermo e fa sì che le seguenti proprietà di sola lettura da impostare. Siano coerenti con il `Rectangle` passato al metodo:

- [`Bounds`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Bounds/) di tipo `Rectangle`
- [`X`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.X/) di tipo `double`
- [`Y`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Y/) di tipo `double`
- [`Width`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) di tipo `double`
- [`Height`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) di tipo `double`

Nelle versioni precedenti al `Layout` chiama, `Height` e `Width` hanno valori fittizi di &ndash;1.

Una chiamata a `Layout` attiva anche le chiamate a metodi protetti seguenti:

- [`SizeAllocated`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.SizeAllocated/p/System.Double/System.Double/), che chiama
- [`OnSizeAllocated`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnSizeAllocated/p/System.Double/System.Double/), che può essere sottoposto a override.

Infine, viene generato l'evento seguente:

- [`SizeChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.SizeChanged/)

Il `OnSizeAllocated` metodo sottoposto a override da `Page` e `Layout`, quali sono le uniche due classi in xamarin. Forms che può avere elementi figlio. Le chiamate al metodo sottoposto a override

- [`UpdateChildrenLayout`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.UpdateChildrenLayout()/) per `Page` derivati e [ `UpdateChildrenLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.UpdateChildrenLayout()/) per `Layout` derivati, che chiama
- [`LayoutChildren`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) per `Page` derivati e [ `LayoutChildren` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildren/p/System.Double/System.Double/System.Double/System.Double/) per `Layout` derivati.

`LayoutChildren` chiama quindi `Layout` per ogni elemento figlio dell'elemento. Se almeno un elemento figlio è un nuovo `Bounds` impostazione, quindi viene generato l'evento seguente:

- [`LayoutChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.LayoutChanged/) per `Page` derivati e [ `LayoutChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Layout.LayoutChanged/) per `Layout` derivati

### <a name="constraints-and-size-requests"></a>I vincoli e le richieste di dimensioni

Per `LayoutChildren` per chiamare in modo intelligente `Layout` su tutti gli elementi figlio, è necessario conoscere un *preferito* o *desiderato* dimensioni per gli elementi figlio. Pertanto le chiamate a `Layout` per ognuno degli elementi figlio sono in genere preceduti da chiamate a

- [`GetSizeRequest`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.GetSizeRequest/p/System.Double/System.Double/)

Dopo il libro è stato pubblicato, il `GetSizeRequest` metodo è obsoleto e sostituito con

- [`Measure`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Measure/p/System.Double/System.Double/Xamarin.Forms.MeasureFlags/)

Il `Measure` metodo supporta il [ `Margin` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/) proprietà e include un argomento di tipo [ `MeasureFlag` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MeasureFlags/), che contiene due membri:

- [`IncludeMargins`](https://developer.xamarin.com/api/field/Xamarin.Forms.MeasureFlags.IncludeMargins/)
- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.MeasureFlags.None/) non includere i margini

Per molti elementi, `GetSizeRequest` o `Measure` Ottiene le dimensioni dell'elemento nativa dal renderer. Entrambi metodi dispongono di parametri per la larghezza e altezza *vincoli*. Ad esempio, un `Label` utilizzerà il vincolo di larghezza per determinare come eseguire il wrapping di più righe di testo.

Entrambi `GetSizeRequest`e `Measure` restituiscono un valore di tipo [ `SizeRequest` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SizeRequest/), che ha due proprietà:

- [`Request`](https://developer.xamarin.com/api/property/Xamarin.Forms.SizeRequest.Request/) di tipo `Size`
- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.SizeRequest.Minimum/) di tipo `Size`

Molto spesso questi due valori sono identici e `Minimum` valore in genere può essere ignorato.

`VisualElement` definisce inoltre un metodo protetto simile a `GetSizeRequest` che viene chiamato da `GetSizeRequest`:

- [`OnSizeRequest`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnSizeRequest/p/System.Double/System.Double/) Restituisce un `SizeRequest` valore

Tale metodo è ora obsoleto e sostituito con:

- [`OnMeasure`](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.OnMeasure/p/System.Double/System.Double/)

Ogni classe che deriva da `Layout` o `Layout<T>` deve eseguire l'override `OnSizeRequest` o `OnMeasure`. Si tratta in una classe di layout determina relative dimensioni, che sono in genere in base alle dimensioni dei relativi elementi figlio, ottenuto chiamando `GetSizeRequest` o `Measure` sugli elementi figlio. Prima e dopo la chiamata `OnSizeRequest` o `OnMeasure`, `GetSizeRequest` o `Measure` consente regolazioni in incrementi in base alle proprietà seguenti:

- [`WidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)di tipo `double`, interessa la `Request` proprietà di `SizeRequest`
- [`HeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) di tipo `double`, interessa la `Request` proprietà di `SizeRequest`
- [`MinimumWidthRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.MinimumWidthRequest/) di tipo `double`, interessa la `Minimum` proprietà di `SizeRequest`
- [`MinimumHeightRequest`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.MinimumHeightRequest/) di tipo `double`, interessa la `Minimum` proprietà di `SizeRequest`

### <a name="infinite-constraints"></a>Vincoli infiniti

Il vincolo di argomenti passati a `GetSizeRequest` (o `Measure`) e `OnSizeRequest` (o `OnMeasure`) può essere infinito (ad esempio, i valori di `Double.PositiveInfinity`). Tuttavia, il `SizeRequest` restituito da questi metodi non possono contenere dimensioni infinite.

Vincoli infiniti indicano che la dimensione richiesta deve riflettere le dimensioni dell'elemento naturale. Verticale `StackLayout` chiamate `GetSizeRequest` (o `Measure`) sui propri elementi figlio con un vincolo di altezza infinito. Chiama un layout di stack orizzontale `GetSizeRequest` (o `Measure`) sui propri elementi figlio con un vincolo di larghezza infinita. Un `AbsoluteLayout` chiamate `GetSizeRequest` (o `Measure`) sui propri elementi figlio con vincoli di larghezza e altezza infiniti.

### <a name="peeking-inside-the-process"></a>Visualizzazione all'interno del processo

Il [ **ExploreChildSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) vincolo Visualizza e dimensioni delle richieste di informazioni per un layout semplice.

## <a name="deriving-from-layoutview"></a>La derivazione dal Layout<View>

Una classe di layout personalizzato deriva da `Layout<View>`. È responsabilità di due:

- Eseguire l'override `OnMeasure` chiamare `Measure` negli elementi figlio del layout. Restituire una dimensione richiesta per il layout di se stesso
- Eseguire l'override `LayoutChildren` chiamare `Layout` negli elementi figlio del layout

Il `for` o `foreach` ciclo in tali override deve ignorare qualsiasi figlio il cui `IsVisible` è impostata su `false`.

Una chiamata a `OnMeasure` non è garantito. `OnMeasure` non verrà chiamato se l'elemento padre del layout è che regolano la dimensione del layout (ad esempio, un layout che riempie una pagina). Per questo motivo, `LayoutChildren` non può basarsi su dimensioni figlio ottenute durante il `OnMeasure` chiamare. Molto spesso, `LayoutChildren` deve chiamare se stesso `Measure` negli elementi figlio del layout, oppure è possibile implementare un tipo di dimensione, la memorizzazione nella cache logica (illustrato più avanti).

### <a name="an-easy-example"></a>Un esempio semplice

Il [ **VerticalStackDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) esempio contiene una rappresentazione semplificata [ `VerticalStack` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) classe e una dimostrazione dell'utilizzo.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Posizionamento verticale e orizzontale semplificato

Uno dei processi che `VerticalStack` deve eseguire si verifica durante la `LayoutChildren` eseguire l'override. Il metodo utilizza l'elemento figlio `HorizontalOptions` proprietà per determinare come posizionare l'elemento figlio all'interno del relativo slot nel `VerticalStack`. È invece possibile chiamare il metodo statico [ `Layout.LayoutChildIntoBoundingRect` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/). Questo metodo chiama `Measure` il figlio e utilizza il `HorizontalOptions` e `VerticalOptions` proprietà per posizionare l'elemento figlio all'interno del rettangolo specificato.

### <a name="invalidation"></a>Invalidamento

Spesso una modifica nella proprietà di un elemento influisce su come tale elemento viene visualizzata nel layout. Il layout deve essere invalidato per attivare un nuovo layout.

`VisualElement` definisce un metodo protetto [ `InvalidateMeasure` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.InvalidateMeasure()/), che in genere viene chiamato dal gestore di modifica della proprietà di qualsiasi proprietà associabile cui modifica influisce sulle dimensioni dell'elemento. Il `InvalidateMeasure` metodo generato un [ `MeasureInvalidated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.MeasureInvalidated/) evento.

Il `Layout` classe definisce un metodo protetto simile denominato [ `InvalidateLayout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.InvalidateLayout()/), quale un `Layout` derivato deve chiamare per qualsiasi modifica che influisce sulla modalità posiziona e relativi elementi figlio di dimensioni.

### <a name="some-rules-for-coding-layouts"></a>Alcune regole per la codifica di layout

1. Proprietà definite da `Layout<T>` derivati devono essere supportati da proprietà associabili e i gestori di modifica della proprietà devono chiamare `InvalidateLayout`.

2. Oggetto `Layout<T>` deve eseguire l'override derivato che definisce le proprietà associabili associate [ `OnAdded` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout%3CT%3E.OnAdded/p/T/) per aggiungere un gestore di modifica della proprietà per i relativi elementi figlio e [ `OnRemoved` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout%3CT%3E.OnRemoved/p/T/) per rimuovere i gestore. Il gestore deve verificare per le modifiche in tali associate proprietà associabili e rispondere chiamando `InvalidateLayout`.

3. Oggetto `Layout<T>` deve eseguire l'override derivata che implementa una cache di dimensione figlio `InvalidateLayout` e [ `OnChildMeasureInvalidated` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.OnChildMeasureInvalidated()/) e cancellare la cache quando vengono chiamati questi metodi.

### <a name="a-layout-with-properties"></a>Un layout con proprietà

Il [ `WrapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) si presuppone che tutti gli elementi figlio è la stessa dimensione e gli elementi figlio da una riga (o colonna) va a capo alla successiva. Definisce un `Orientation` proprietà come `StackLayout`, e `ColumnSpacing` e `RowSpacing` proprietà quali `Grid`, e memorizza nella cache di dimensione figlio.

Il [ **PhotoWrap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) esempio inserisce un `WrapLayout` in un `ScrollView` per la visualizzazione di fotografie.

### <a name="no-unconstrained-dimensions-allowed"></a>Nessuna dimensione non vincolata consentita!

Il [ `UniformGridLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) nel [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria consente di visualizzare tutti gli elementi figlio all'interno di se stesso. Pertanto, non può gestire le dimensioni non vincolate e genera un'eccezione se non viene rilevato uno.

Il [ **PhotoGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) illustra `UniformGridLayout`:

[![Schermata triplo della griglia di foto](images/ch26fg08-small.png "il Layout di griglia uniforme")](images/ch26fg08-large.png#lightbox "il Layout di griglia uniforme")

### <a name="overlapping-children"></a>Sovrapposizione di elementi figlio

Oggetto `Layout<T>` derivato può sovrapporsi nei relativi elementi figlio. Tuttavia, gli elementi figlio il rendering vengono eseguiti in base all'ordine nel `Children` insieme e non l'ordine in cui i relativi `Layout` metodi vengono chiamati.

La `Layout` classe definisce due metodi che consentono di spostare un elemento figlio all'interno della raccolta:

- [`LowerChild`](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.LowerChild/p/Xamarin.Forms.View/) Per spostare un elemento figlio all'inizio della raccolta
- [`RaiseChild`](https://developer.xamarin.com/api/member/Xamarin.Forms.Layout.RaiseChild/p/Xamarin.Forms.View/) Per spostare un elemento figlio alla fine della raccolta

Per gli elementi figlio sovrapposti, elementi figlio alla fine della raccolta vengono visualizzati visivamente sopra gli elementi figlio all'inizio della raccolta.

Il [ `OverlapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria definisce una proprietà associata per indicare l'ordine di rendering e consentono quindi uno dei relativi elementi figlio da visualizzare nella parte superiore di altri utenti. Il [ **StudentCardFile** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) esempio viene illustrato questo:

[![Schermata triplo della griglia di Student scheda File](images/ch26fg10-small.png "sovrapposti figli Layout")](images/ch26fg10-large.png#lightbox "sovrapposti figli di Layout")

### <a name="more-attached-bindable-properties"></a>Più collegare proprietà associabili

Il [ `CartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria definisce le proprietà associabili associate per specificare due `Point` valori e un valore di spessore e modifica `BoxView` elementi simile a quella di righe.

Il [ **UnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) esempio che viene utilizzato per disegnare un cubo 3D.

### <a name="layout-and-layoutto"></a>Layout e LayoutTo

Oggetto `Layout<T>` derivato è possibile chiamare `LayoutTo` anziché `Layout` animare il layout. Il [ `AnimatedCartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) eseguita dalla classe e [ **AnimatedUnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) esempio viene illustrato come.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 26 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Esempi di 26 capitolo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Creazione di layout personalizzati](~/xamarin-forms/user-interface/layouts/custom.md)
