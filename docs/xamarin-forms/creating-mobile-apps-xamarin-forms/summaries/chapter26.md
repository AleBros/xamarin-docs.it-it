---
title: Riepilogo del capitolo 26. Layout personalizzati
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 26. Layout personalizzati'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1eb5153f8ab295696e373f4fdb65a4f8820a05bc
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "70770934"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Riepilogo del capitolo 26. Layout personalizzati

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Novell. Forms include diverse classi derivate da [`Layout<View>`](xref:Xamarin.Forms.Layout`1):

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout` e
- `RelativeLayout`.

Questo capitolo descrive come creare classi personalizzate che derivano da `Layout<View>`.

## <a name="an-overview-of-layout"></a>Una panoramica del layout

Non vi è alcun sistema centralizzato che gestisce il layout di xamarin. Forms. Ogni elemento è responsabile di determinare quale deve essere la propria dimensione e su come eseguire il rendering all'interno di una determinata area.

### <a name="parents-and-children"></a>Elementi padre e figlio

Tutti gli elementi che contiene elementi figlio sono responsabili per il posizionamento di tali elementi figlio all'interno di se stesso. È l'elemento padre che decide cosa ridimensionare gli elementi figlio deve essere basato sulle dimensioni è disponibile e la dimensione figlio deve essere.

### <a name="sizing-and-positioning"></a>Ridimensionamento e posizionamento

Layout in cui inizia la parte superiore della struttura visiva con la pagina e quindi procede con tutti i rami. Il metodo pubblico più importante nel layout è [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) definito da `VisualElement`. Ogni elemento che è un elemento padre di altri elementi chiama `Layout` per ognuno dei relativi figli per fornire al figlio una dimensione e una posizione rispetto a se stessa sotto forma di valore di [`Rectangle`](xref:Xamarin.Forms.Rectangle) . Queste `Layout` chiamate si propagano attraverso la struttura ad albero visuale.

Una chiamata a `Layout` è necessaria per la visualizzazione di un elemento sullo schermo e determina l'impostazione delle seguenti proprietà di sola lettura. Sono coerenti con il `Rectangle` passato al metodo:

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) di tipo `Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X) di tipo `double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y) di tipo `double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width) di tipo `double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height) di tipo `double`

Prima della chiamata `Layout`, `Height` e `Width` hanno valori fittizi &ndash;1.

Una chiamata a `Layout` attiva anche le chiamate ai metodi protetti seguenti:

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)), che chiama
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double)), di cui è possibile eseguire l'override.

Infine, viene generato l'evento seguente:

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

Il metodo `OnSizeAllocated` viene sottoposto a override da `Page` e `Layout`, che sono le uniche due classi di Novell. Forms che possono avere elementi figlio. Le chiamate del metodo sottoposto a override

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout) per `Page` derivati e [`UpdateChildrenLayout`](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) per `Layout` derivati, che chiama
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) per `Page` derivati e [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) per `Layout` derivati.

`LayoutChildren` chiama quindi `Layout` per ogni elemento figlio dell'elemento. Se almeno un elemento figlio ha una nuova impostazione di `Bounds`, viene generato l'evento seguente:

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged) per `Page` derivati e [`LayoutChanged`](xref:Xamarin.Forms.Layout.LayoutChanged) per `Layout` derivati

### <a name="constraints-and-size-requests"></a>I vincoli e le dimensioni richieste

Per `LayoutChildren` per chiamare in modo intelligente `Layout` su tutti i relativi elementi figlio, è necessario che conosca una dimensione *preferita* o *desiderata* per gli elementi figlio. Pertanto, le chiamate a `Layout` per ogni elemento figlio sono in genere precedute da chiamate a

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

Dopo la pubblicazione del libro, il metodo `GetSizeRequest` è stato deprecato e sostituito con

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

Il metodo `Measure` supporta la proprietà [`Margin`](xref:Xamarin.Forms.View.Margin) e include un argomento di tipo [`MeasureFlag`](xref:Xamarin.Forms.MeasureFlags), che dispone di due membri:

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None) non includere margini

Per molti elementi, `GetSizeRequest` o `Measure` ottiene la dimensione nativa dell'elemento dal renderer. Entrambi i metodi hanno parametri per i *vincoli*Width e Height. Ad esempio, un `Label` utilizzerà il vincolo Width per determinare come eseguire il wrapping di più righe di testo.

Sia `GetSizeRequest`che `Measure` restituiscono un valore di tipo [`SizeRequest`](xref:Xamarin.Forms.SizeRequest), che ha due proprietà:

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request) di tipo `Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum) di tipo `Size`

Spesso questi due valori sono uguali e il valore `Minimum` può essere ignorato.

`VisualElement` definisce anche un metodo protetto simile a `GetSizeRequest` chiamato da `GetSizeRequest`:

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double)) restituisce un valore `SizeRequest`

Tale metodo è ora deprecato e sostituito con:

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

Ogni classe che deriva da `Layout` o `Layout<T>` deve eseguire l'override di `OnSizeRequest` o `OnMeasure`. Questo è il punto in cui una classe layout determina le proprie dimensioni, che in genere si basano sulle dimensioni dei figli, che ottiene chiamando `GetSizeRequest` o `Measure` sugli elementi figlio. Prima e dopo la chiamata di `OnSizeRequest` o `OnMeasure`, `GetSizeRequest` o `Measure` apporta modifiche in base alle proprietà seguenti:

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)di tipo `double`, influiscono sulla proprietà `Request` di `SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) di tipo `double`, influiscono sulla proprietà `Request` di `SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest) di tipo `double`, influiscono sulla proprietà `Minimum` di `SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest) di tipo `double`, influiscono sulla proprietà `Minimum` di `SizeRequest`

### <a name="infinite-constraints"></a>Vincoli infiniti

Gli argomenti del vincolo passati a `GetSizeRequest` (o `Measure`) e `OnSizeRequest` (o `OnMeasure`) possono essere infiniti, ovvero valori di `Double.PositiveInfinity`. Tuttavia, i `SizeRequest` restituiti da questi metodi non possono contenere dimensioni infinite.

Infinito vincoli indicano che le dimensioni richieste devono riflettere le dimensioni dell'elemento naturale. Una `StackLayout` verticale chiama `GetSizeRequest` (o `Measure`) sui relativi elementi figlio con un vincolo di altezza infinita. Un layout dello stack orizzontale chiama `GetSizeRequest` (o `Measure`) sugli elementi figlio con un vincolo di larghezza infinita. Un `AbsoluteLayout` chiama `GetSizeRequest` (o `Measure`) sui relativi elementi figlio con vincoli di larghezza e altezza infiniti.

### <a name="peeking-inside-the-process"></a>Visualizzazione all'interno del processo

[**ExploreChildSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) Visualizza le informazioni sulle richieste di vincoli e dimensioni per un layout semplice.

## <a name="deriving-from-layoutview"></a>Derivazione da layout\<visualizzazione >

Una classe layout personalizzata deriva da `Layout<View>`. Ha due compiti:

- Eseguire l'override di `OnMeasure` per chiamare `Measure` su tutti gli elementi figlio del layout. Restituire una dimensione richiesta per il layout di se stesso
- Eseguire l'override di `LayoutChildren` per chiamare `Layout` su tutti gli elementi figlio del layout

Il ciclo `for` o `foreach` in queste sostituzioni deve ignorare qualsiasi elemento figlio la cui proprietà `IsVisible` è impostata su `false`.

Una chiamata a `OnMeasure` non è garantita. `OnMeasure` non verrà chiamato se l'elemento padre del layout regola le dimensioni del layout, ad esempio un layout che riempie una pagina. Per questo motivo, `LayoutChildren` non può basarsi sulle dimensioni figlio ottenute durante la chiamata `OnMeasure`. Molto spesso, `LayoutChildren` necessario chiamare `Measure` sugli elementi figlio del layout oppure è possibile implementare un tipo di logica di memorizzazione nella cache di dimensioni (da discutere più avanti).

### <a name="an-easy-example"></a>Un esempio semplice

L'esempio [**VerticalStackDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) contiene una classe [`VerticalStack`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) semplificata e una dimostrazione del relativo utilizzo.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Verticale e orizzontale posizionamento semplificato

Uno dei processi che `VerticalStack` necessario eseguire si verifica durante l'override del `LayoutChildren`. Il metodo usa la proprietà `HorizontalOptions` del figlio per determinare come posizionare l'elemento figlio all'interno dello slot nel `VerticalStack`. È invece possibile chiamare il metodo statico [`Layout.LayoutChildIntoBoundingRect`](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle)). Questo metodo chiama `Measure` sull'elemento figlio e usa le proprietà `HorizontalOptions` e `VerticalOptions` per posizionare l'elemento figlio all'interno del rettangolo specificato.

### <a name="invalidation"></a>Invalidamento

Spesso una modifica nella proprietà di un elemento influisce sul modo in cui tale elemento viene visualizzato nel layout. Il layout deve essere invalidato per attivare un nuovo layout.

`VisualElement` definisce un metodo protetto [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure), che in genere viene chiamato dal gestore modificato da proprietà di qualsiasi proprietà associabile la cui modifica influisca sulle dimensioni dell'elemento. Il metodo `InvalidateMeasure` genera un evento di [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) .

La classe `Layout` definisce un metodo protetto simile denominato [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout), che deve essere chiamato da un `Layout` derivato per tutte le modifiche che influiscono sulla modalità di posizionamento e ridimensionamento dei relativi elementi figlio.

### <a name="some-rules-for-coding-layouts"></a>Alcune regole per la codifica di layout

1. Le proprietà definite da `Layout<T>` derivati devono essere supportate da proprietà associabili e i gestori modificati della proprietà devono chiamare `InvalidateLayout`.

2. Una `Layout<T>` derivata che definisce le proprietà associabili associate deve eseguire l'override di [`OnAdded`](xref:Xamarin.Forms.Layout`1.OnAdded*) per aggiungere un gestore modificato da proprietà ai relativi elementi figlio e [`OnRemoved`](xref:Xamarin.Forms.Layout`1.OnRemoved*) per rimuovere il gestore. Il gestore deve verificare la presenza di modifiche nelle proprietà associabili associate e rispondere chiamando `InvalidateLayout`.

3. Una `Layout<T>` derivata che implementa una cache di dimensioni figlio deve eseguire l'override di `InvalidateLayout` e [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) e cancellare la cache quando vengono chiamati questi metodi.

### <a name="a-layout-with-properties"></a>Un layout con proprietà

La classe [`WrapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) in [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) presuppone che tutti i relativi elementi figlio abbiano le stesse dimensioni ed esegue il wrapping degli elementi figlio da una riga o una colonna a quella successiva. Definisce una proprietà `Orientation` come `StackLayout`e `ColumnSpacing` e `RowSpacing` proprietà come `Grid`e memorizza nella cache le dimensioni figlio.

L'esempio di [**fotowrap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) inserisce un `WrapLayout` in un `ScrollView` per la visualizzazione delle foto predefinite.

### <a name="no-unconstrained-dimensions-allowed"></a>Nessuna dimensione non vincolata consentita.

Il [`UniformGridLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) è concepito per visualizzare tutti gli elementi figlio all'interno. Pertanto non può gestire le dimensioni non vincolate e genera un'eccezione se viene rilevato uno.

L'esempio [**fotogrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) illustra `UniformGridLayout`:

[![Schermata tripla della griglia delle foto](images/ch26fg08-small.png "Layout di griglia uniforme")](images/ch26fg08-large.png#lightbox "Layout di griglia uniforme")

### <a name="overlapping-children"></a>La sovrapposizione di elementi figlio

Un `Layout<T>` derivato può sovrapporsi ai relativi elementi figlio. Viene tuttavia eseguito il rendering degli elementi figlio nell'ordine indicato nella raccolta `Children` e non nell'ordine in cui vengono chiamati i relativi metodi di `Layout`.

La classe `Layout` definisce due metodi che consentono di spostare un elemento figlio all'interno della raccolta:

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View)) per spostare un elemento figlio all'inizio della raccolta
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View)) spostare un elemento figlio alla fine della raccolta

Per gli elementi figlio sovrapposti, gli elementi figlio alla fine della raccolta vengono visualizzati in modo visivo sopra gli elementi figlio all'inizio della raccolta.

La classe [`OverlapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) definisce una proprietà associata per indicare l'ordine di rendering e quindi consentire la visualizzazione di uno dei relativi elementi figlio sopra gli altri. Nell'esempio [**StudentCardFile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) viene illustrato quanto segue:

[![Schermata tripla della griglia dei file delle schede degli studenti](images/ch26fg10-small.png "Elementi figlio layout sovrapposti")](images/ch26fg10-large.png#lightbox "Elementi figlio layout sovrapposti")

### <a name="more-attached-bindable-properties"></a>Più collegare proprietà associabili

La classe [`CartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) definisce proprietà associabili associate per specificare due valori `Point` e un valore di spessore e manipola `BoxView` elementi in modo analogo alle righe.

L'esempio [**UnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) lo usa per creare un cubo 3D.

### <a name="layout-and-layoutto"></a>Layout e LayoutTo

Una `Layout<T>` derivata può chiamare `LayoutTo` anziché `Layout` per animare il layout. La classe [`AnimatedCartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) esegue questa operazione e l'esempio [**AnimatedUnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) lo illustra.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 26 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Esempi del capitolo 26](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Creazione di layout personalizzati](~/xamarin-forms/user-interface/layouts/custom.md)
