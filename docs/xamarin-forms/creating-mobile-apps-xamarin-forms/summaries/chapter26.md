---
title: Riepilogo del capitolo 26. Layout personalizzati
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 26. Layout personalizzati'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 1fc42207d26f2e2154c7bd6634cc90fead4b0b17
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998934"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Riepilogo del capitolo 26. Layout personalizzati

Xamarin. Forms include diverse classi derivate da [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1):

* `StackLayout`,
* `Grid`,
* `AbsoluteLayout` e
* `RelativeLayout`.

In questo capitolo descrive come creare le classi che derivano da `Layout<View>`.

## <a name="an-overview-of-layout"></a>Una panoramica del layout

Non vi è alcun sistema centralizzato che gestisce il layout di xamarin. Forms. Ogni elemento è responsabile di determinare quale deve essere la propria dimensione e su come eseguire il rendering all'interno di una determinata area.

### <a name="parents-and-children"></a>Elementi padre e figlio

Tutti gli elementi che contiene elementi figlio sono responsabili per il posizionamento di tali elementi figlio all'interno di se stesso. È l'elemento padre che decide cosa ridimensionare gli elementi figlio deve essere basato sulle dimensioni è disponibile e la dimensione figlio deve essere.

### <a name="sizing-and-positioning"></a>Ridimensionamento e posizionamento

Layout in cui inizia la parte superiore della struttura visiva con la pagina e quindi procede con tutti i rami. Il metodo pubblico più importante nel layout viene [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) definiti da `VisualElement`. Tutti gli elementi che sono di altre chiamate di elementi padre `Layout` per ognuno dei relativi elementi figlio per specificare l'elemento figlio, dimensioni e posizione rispetto a se stesso sotto forma di un [ `Rectangle` ](xref:Xamarin.Forms.Rectangle) valore. Questi `Layout` chiamate propagano attraverso la struttura ad albero visuale.

Una chiamata a `Layout` è obbligatorio per un elemento venga visualizzato sullo schermo e fa sì che le seguenti proprietà di sola lettura da impostare. Siano coerenti con il `Rectangle` passato al metodo:

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) di tipo `Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X) di tipo `double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y) di tipo `double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width) di tipo `double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height) di tipo `double`

Nelle versioni precedenti al `Layout` , chiamare `Height` e `Width` includono valori fittizi &ndash;1.

Una chiamata a `Layout` attiva anche le chiamate ai metodi protetti seguenti:

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)), che chiama
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double)), che può essere sottoposto a override.

Infine, viene generato l'evento seguente:

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

Il `OnSizeAllocated` metodo esegue l'override `Page` e `Layout`, quali sono le uniche due classi in xamarin. Forms che può avere elementi figlio. Le chiamate del metodo sottoposto a override

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout) per la `Page` derivati e [ `UpdateChildrenLayout` ](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) per `Layout` derivati, che chiama
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) per la `Page` derivati e [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) per `Layout` derivati.

`LayoutChildren` chiama quindi `Layout` per ognuno degli elementi figlio dell'elemento. Se almeno un elemento figlio è disponibile un nuovo `Bounds` impostazione, quindi viene generato l'evento seguente:

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged) per la `Page` derivati e [ `LayoutChanged` ](xref:Xamarin.Forms.Layout.LayoutChanged) per `Layout` derivati

### <a name="constraints-and-size-requests"></a>I vincoli e le dimensioni richieste

Per `LayoutChildren` per chiamare in modo intelligente `Layout` in tutti i relativi elementi figlio, è necessario conoscere un *preferito* oppure *desiderato* dimensioni per gli elementi figlio. Di conseguenza le chiamate a `Layout` per ognuno degli elementi figlio in genere sono precedute dalle chiamate a

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

Dopo che è stato pubblicato il libro, il `GetSizeRequest` metodo è stato deprecato e sostituito con

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

Il `Measure` alcune variazioni per il metodo di [ `Margin` ](xref:Xamarin.Forms.View.Margin) proprietà e include un argomento di tipo [ `MeasureFlag` ](xref:Xamarin.Forms.MeasureFlags), che contiene due membri:

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None) Per non includere i margini

Per molti elementi `GetSizeRequest` o `Measure` Ottiene le dimensioni dell'elemento nativa dal renderer. Entrambi i metodi presentano i parametri per la larghezza e altezza *vincoli*. Ad esempio, un `Label` utilizzerà il vincolo di larghezza per determinare come eseguire il wrapping di più righe di testo.

Entrambe `GetSizeRequest`e `Measure` restituiscono un valore di tipo [ `SizeRequest` ](xref:Xamarin.Forms.SizeRequest), che dispone di due proprietà:

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request) di tipo `Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum) di tipo `Size`

Molto spesso questi due valori sono uguali e il `Minimum` valore in genere può essere ignorato.

`VisualElement` definisce anche un metodo protetto simile a `GetSizeRequest` che viene chiamato da `GetSizeRequest`:

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double)) Restituisce un `SizeRequest` valore

Tale metodo è ora deprecato e sostituito con:

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

Ogni classe che deriva da `Layout` oppure `Layout<T>` deve eseguire l'override `OnSizeRequest` o `OnMeasure`. Si tratta in cui una classe layout determina la propria dimensione, che in genere si basa sulle dimensioni dei relativi elementi figlio, ottenuto chiamando `GetSizeRequest` o `Measure` sugli elementi figlio. Prima e dopo la chiamata `OnSizeRequest` oppure `OnMeasure`, `GetSizeRequest` o `Measure` consente regolazioni in incrementi sulla base delle proprietà seguenti:

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)di tipo `double`, riguarda il `Request` proprietà di `SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) di tipo `double`, riguarda il `Request` proprietà di `SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest) di tipo `double`, riguarda il `Minimum` proprietà di `SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest) di tipo `double`, riguarda il `Minimum` proprietà di `SizeRequest`

### <a name="infinite-constraints"></a>Vincoli infiniti

Gli argomenti di vincolo passati al `GetSizeRequest` (o `Measure`) e `OnSizeRequest` (o `OnMeasure`) può essere infinita (ovvero i valori di `Double.PositiveInfinity`). Tuttavia, il `SizeRequest` restituite da questi metodi non possono contenere dimensioni infinite.

Infinito vincoli indicano che le dimensioni richieste devono riflettere le dimensioni dell'elemento naturale. Un parametro vertical `StackLayout` chiamate `GetSizeRequest` (o `Measure`) sui relativi elementi figlio con un vincolo di altezza infinito. Chiama un layout di stack orizzontale `GetSizeRequest` (o `Measure`) sui relativi elementi figlio con un vincolo di larghezza infinita. Un' `AbsoluteLayout` chiamate `GetSizeRequest` (o `Measure`) sui relativi elementi figlio con vincoli di larghezza e altezza infiniti.

### <a name="peeking-inside-the-process"></a>Visualizzazione all'interno del processo

Il [ **ExploreChildSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) vincolo Visualizza e dimensioni richiedere informazioni sul layout semplice.

## <a name="deriving-from-layoutview"></a>Derivazione da Layout<View>

Una classe di layout personalizzato deriva da `Layout<View>`. Ha due compiti:

- Eseguire l'override `OnMeasure` chiamare `Measure` nell'elemento figlio del layout. Restituire una dimensione richiesta per il layout di se stesso
- Eseguire l'override `LayoutChildren` chiamare `Layout` nell'elemento figlio del layout

Il `for` oppure `foreach` ciclo in tali override deve ignorare qualsiasi figlio il cui `IsVisible` è impostata su `false`.

Una chiamata a `OnMeasure` non è garantito. `OnMeasure` non verrà chiamato se l'elemento padre del layout è che controllano le dimensioni del layout (ad esempio, un layout che riempie una pagina). Per questo motivo `LayoutChildren` non può basarsi su dimensioni figlio ottenute durante il `OnMeasure` chiamare. Molto spesso `LayoutChildren` deve chiamare se stesso `Measure` nell'elemento figlio del layout, oppure è possibile implementare un tipo di dimensione, la memorizzazione nella cache per la logica (che verrà illustrata più avanti).

### <a name="an-easy-example"></a>Un esempio semplice

Il [ **VerticalStackDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) esempio contiene una rappresentazione semplificata [ `VerticalStack` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) classe e una dimostrazione del suo utilizzo.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Verticale e orizzontale posizionamento semplificato

Uno dei processi che `VerticalStack` deve eseguire rientra il `LayoutChildren` eseguire l'override. Il metodo Usa l'elemento figlio `HorizontalOptions` proprietà per determinare la modalità posizionare l'elemento figlio all'interno del relativo slot nel `VerticalStack`. È invece possibile chiamare il metodo statico [ `Layout.LayoutChildIntoBoundingRect` ](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle)). Questo metodo chiama `Measure` su figlio e all'utilizzo relativi `HorizontalOptions` e `VerticalOptions` proprietà per posizionare l'elemento figlio all'interno del rettangolo specificato.

### <a name="invalidation"></a>Invalidamento

Spesso una modifica nella proprietà di un elemento influisce sul modo in cui tale elemento viene visualizzato nel layout. Il layout deve essere invalidato per attivare un nuovo layout.

`VisualElement` definisce un metodo protetto [ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure), che in genere viene chiamato dal gestore di modifica delle proprietà di qualsiasi proprietà associabili cui modifica influisce sulle dimensioni dell'elemento. Il `InvalidateMeasure` metodo viene attivato un [ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) evento.

Il `Layout` classe definisce un metodo protetto simile denominato [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout), ovvero un `Layout` derivato deve chiamare per qualsiasi modifica che influisce sulle modalità posizione e dimensioni relativi elementi figlio.

### <a name="some-rules-for-coding-layouts"></a>Alcune regole per la codifica di layout

1. Le proprietà definite da `Layout<T>` derivati devono essere eseguiti dalle proprietà associabile e devono chiamare i gestori di modifica della proprietà `InvalidateLayout`.

2. Oggetto `Layout<T>` deve eseguire l'override derivato che definisce le proprietà associabili associate [ `OnAdded` ](xref:Xamarin.Forms.Layout`1.OnAdded*) per aggiungere un gestore di modifica delle proprietà ai relativi figli e [ `OnRemoved` ](xref:Xamarin.Forms.Layout`1.OnRemoved*) da rimuovere che gestore. Il gestore deve verificare per le modifiche in tali associate proprietà associabili e rispondere chiamando `InvalidateLayout`.

3. Oggetto `Layout<T>` derivato che implementa una cache di dimensione figlio deve eseguire l'override `InvalidateLayout` e [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) e cancellare la cache quando vengono chiamati questi metodi.

### <a name="a-layout-with-properties"></a>Un layout con proprietà

Il [ `WrapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) presuppone che tutti gli elementi figlio sono la stessa dimensione e include gli elementi figlio da una riga (o colonna) per la successiva. Definisce un `Orientation` proprietà, ad esempio `StackLayout`, e `ColumnSpacing` e `RowSpacing` delle proprietà, ad esempio `Grid`, e lo memorizza nella cache di dimensioni figlio.

Il [ **PhotoWrap** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) esempio inserisce un `WrapLayout` in un `ScrollView` per la visualizzazione di fotografie.

### <a name="no-unconstrained-dimensions-allowed"></a>Nessuna dimensione non vincolata consentita.

Il [ `UniformGridLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) nel [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria consente di visualizzare tutti gli elementi figlio all'interno di se stesso. Pertanto non può gestire le dimensioni non vincolate e genera un'eccezione se viene rilevato uno.

Il [ **PhotoGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) esempio viene illustrato `UniformGridLayout`:

[![Schermata triplo della griglia delle foto](images/ch26fg08-small.png "Layout griglia uniforme")](images/ch26fg08-large.png#lightbox "Layout griglia uniforme")

### <a name="overlapping-children"></a>La sovrapposizione di elementi figlio

Oggetto `Layout<T>` derivato può essere sovrapposto relativi elementi figlio. Tuttavia, gli elementi figlio vengono sottoposti a rendering in base all'ordine nel `Children` raccolta e non l'ordine in cui i `Layout` metodi vengono chiamati.

Il `Layout` classe definisce due metodi che consentono di spostare un elemento figlio all'interno della raccolta:

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View)) Per spostare un elemento figlio all'inizio della raccolta
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View)) Per spostare un elemento figlio alla fine della raccolta

Per gli elementi figlio sovrapposti, gli elementi figlio alla fine della raccolta vengono visualizzati in modo visivo sopra gli elementi figlio all'inizio della raccolta.

Il [ `OverlapLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria definisce una proprietà associata per indicare l'ordine di rendering consentendo in tal modo uno dei relativi elementi figlio da visualizzare sopra le altre. Il [ **StudentCardFile** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) esempio viene illustrata questa:

[![Tripla schermata di griglia di File carta per studenti](images/ch26fg10-small.png "sovrapposti Layout figli")](images/ch26fg10-large.png#lightbox "sovrapposti figli di Layout")

### <a name="more-attached-bindable-properties"></a>Più collegare proprietà associabili

Il [ `CartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria definisce le proprietà associabili associate per specificare due `Point` valori e un oggetto valore dello spessore e manipola `BoxView` elementi simile a quella di righe.

Il [ **UnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) esempio che utilizza per disegnare un cubo 3D.

### <a name="layout-and-layoutto"></a>Layout e LayoutTo

Oggetto `Layout<T>` derivato è possibile chiamare `LayoutTo` anziché `Layout` per animare il layout. Il [ `AnimatedCartesianLayout` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) eseguita dalla classe e il [ **AnimatedUnitCube** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) esempio illustra come fare.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 26 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Capitolo 26 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Creazione di layout personalizzati](~/xamarin-forms/user-interface/layouts/custom.md)
