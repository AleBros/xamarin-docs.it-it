---
title: Riepilogo del capitolo 26. Layout personalizzati
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 26. Layout personalizzati'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: deb46d1a70e7c707c998be8669b4af3b8e8d7ead
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136604"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Riepilogo del capitolo 26. Layout personalizzati

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin.Formsinclude diverse classi derivate da [`Layout<View>`](xref:Xamarin.Forms.Layout`1) :

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout` e
- `RelativeLayout`.

Questo capitolo descrive come creare classi personalizzate che derivano da `Layout<View>` .

## <a name="an-overview-of-layout"></a>Panoramica del layout

Non esiste alcun sistema centralizzato che gestisce il Xamarin.Forms layout. Ogni elemento è responsabile della determinazione delle dimensioni specifiche e del modo in cui eseguirne il rendering all'interno di un'area specifica.

### <a name="parents-and-children"></a>Elementi padre e figlio

Ogni elemento con elementi figlio è responsabile del posizionamento degli elementi figlio all'interno di se stesso. Si tratta dell'elemento padre che determina in definitiva le dimensioni che devono essere basate sugli elementi figlio sulle dimensioni disponibili e le dimensioni desiderate per l'elemento figlio.

### <a name="sizing-and-positioning"></a>Dimensionamento e posizionamento

Il layout inizia dalla parte superiore della struttura ad albero visuale con la pagina e quindi procede attraverso tutti i rami. Il metodo pubblico più importante nel layout è [ `Layout` ] (xrif: Xamarin.Forms . VisualElement. layout ( Xamarin.Forms . Rettangolo)) definito da `VisualElement` . Ogni elemento che è un elemento padre di altri elementi chiama `Layout` per ognuno dei relativi figli per fornire al figlio una dimensione e una posizione rispetto a se stessa sotto forma di [`Rectangle`](xref:Xamarin.Forms.Rectangle) valore. Queste `Layout` chiamate si propagano attraverso la struttura ad albero visuale.

Una chiamata a `Layout` è necessaria per la visualizzazione di un elemento sullo schermo e determina l'impostazione delle seguenti proprietà di sola lettura. Sono coerenti con l'oggetto `Rectangle` passato al metodo:

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)di tipo`Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X)di tipo`double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y)di tipo`double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width)di tipo`double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height)di tipo`double`

Prima della `Layout` chiamata `Height` e `Width` hanno valori fittizi pari a &ndash; 1.

Una chiamata a `Layout` attiva anche le chiamate ai metodi protetti seguenti:

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)), che chiama
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double)), che può essere sottoposto a override.

Infine, viene generato l'evento seguente:

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

Il `OnSizeAllocated` metodo viene sottoposto a override da `Page` e `Layout` , che sono le uniche due classi in Xamarin.Forms che possono avere elementi figlio. Chiamate al metodo sottoposte a override

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout)per i derivati `Page` e [`UpdateChildrenLayout`](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) per i `Layout` derivati, che chiama
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double))per i derivati `Page` e [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) per i `Layout` derivati.

`LayoutChildren`chiama quindi `Layout` per ogni elemento figlio dell'elemento. Se almeno un elemento figlio ha una nuova `Bounds` impostazione, viene generato l'evento seguente:

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged)per i derivati `Page` e [`LayoutChanged`](xref:Xamarin.Forms.Layout.LayoutChanged) per i `Layout` derivati

### <a name="constraints-and-size-requests"></a>Vincoli e richieste di dimensioni

Per `LayoutChildren` chiamare in modo intelligente `Layout` su tutti i relativi elementi figlio, è necessario che conosca una dimensione *preferita* o *desiderata* per gli elementi figlio. Pertanto, le chiamate a `Layout` per ogni elemento figlio sono in genere precedute da chiamate a

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

Dopo la pubblicazione del libro, il `GetSizeRequest` metodo è stato deprecato e sostituito con

- [ `Measure` ] (xrif: Xamarin.Forms . VisualElement. Measure (System. Double, System. Double, Xamarin.Forms . MeasureFlags))

Il `Measure` Metodo adatta la [`Margin`](xref:Xamarin.Forms.View.Margin) proprietà e include un argomento di tipo [`MeasureFlag`](xref:Xamarin.Forms.MeasureFlags) , che ha due membri:

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None)per non includere margini

Per molti elementi, `GetSizeRequest` o `Measure` ottiene la dimensione nativa dell'elemento dal renderer. Entrambi i metodi hanno parametri per i *vincoli*Width e Height. Ad esempio, un oggetto utilizzerà `Label` il vincolo Width per determinare come eseguire il wrapping di più righe di testo.

Sia `GetSizeRequest` che `Measure` restituiscono un valore di tipo [`SizeRequest`](xref:Xamarin.Forms.SizeRequest) , che ha due proprietà:

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request)di tipo`Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum)di tipo`Size`

Spesso questi due valori sono uguali e il `Minimum` valore può in genere essere ignorato.

`VisualElement`definisce anche un metodo protetto simile a `GetSizeRequest` che viene chiamato da `GetSizeRequest` :

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double))Restituisce un `SizeRequest` valore

Il metodo è ora deprecato e sostituito con:

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

Ogni classe che deriva da `Layout` o `Layout<T>` deve eseguire l'override di `OnSizeRequest` o `OnMeasure` . Questo è il punto in cui una classe layout determina le proprie dimensioni, che in genere si basano sulle dimensioni degli elementi figlio, ottenuti chiamando `GetSizeRequest` o `Measure` sugli elementi figlio. Prima e dopo la chiamata di `OnSizeRequest` o oppure `OnMeasure` `GetSizeRequest` `Measure` le modifiche in base alle proprietà seguenti:

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)di tipo `double` , influiscono sulla `Request` proprietà di`SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)di tipo `double` , influiscono sulla `Request` proprietà di`SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)di tipo `double` , influiscono sulla `Minimum` proprietà di`SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)di tipo `double` , influiscono sulla `Minimum` proprietà di`SizeRequest`

### <a name="infinite-constraints"></a>Vincoli infiniti

Gli argomenti del vincolo passati a `GetSizeRequest` (o `Measure` ) e `OnSizeRequest` (o `OnMeasure` ) possono essere infiniti (ovvero i valori di `Double.PositiveInfinity` ). Tuttavia, l'oggetto `SizeRequest` restituito da questi metodi non può contenere dimensioni infinite.

I vincoli infiniti indicano che le dimensioni richieste devono riflettere la dimensione naturale dell'elemento. Una `StackLayout` chiamata verticale `GetSizeRequest` (o `Measure` ) sugli elementi figlio con un vincolo di altezza infinita. Un layout dello stack orizzontale chiama `GetSizeRequest` (o `Measure` ) sugli elementi figlio con un vincolo di larghezza infinita. `AbsoluteLayout`Chiama `GetSizeRequest` (o `Measure` ) nei relativi elementi figlio con vincoli di larghezza e altezza infiniti.

### <a name="peeking-inside-the-process"></a>Visualizzazione all'interno del processo

[**ExploreChildSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) Visualizza le informazioni sulle richieste di vincoli e dimensioni per un layout semplice.

## <a name="deriving-from-layoutview"></a>Derivazione dal layout\<View>

Una classe layout personalizzata deriva da `Layout<View>` . Ha due responsabilità:

- Eseguire l'override `OnMeasure` di per chiamare `Measure` su tutti gli elementi figlio del layout. Restituisce una dimensione richiesta per il layout stesso
- Override `LayoutChildren` da chiamare `Layout` su tutti gli elementi figlio del layout

Il `for` `foreach` ciclo o in queste sostituzioni deve ignorare qualsiasi elemento figlio la cui `IsVisible` proprietà è impostata su `false` .

Una chiamata a `OnMeasure` non è garantita. `OnMeasure`non verrà chiamato se l'elemento padre del layout regola le dimensioni del layout, ad esempio un layout che riempie una pagina. Per questo motivo, `LayoutChildren` non può basarsi sulle dimensioni figlio ottenute durante la `OnMeasure` chiamata. Molto spesso, `LayoutChildren` deve essere chiamato `Measure` sugli elementi figlio del layout oppure è possibile implementare un tipo di logica di memorizzazione nella cache di dimensioni (da discutere più avanti).

### <a name="an-easy-example"></a>Esempio semplice

L'esempio [**VerticalStackDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) contiene una classe semplificata [`VerticalStack`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) e una dimostrazione del relativo utilizzo.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Posizionamento verticale e orizzontale semplificato

Uno dei processi che `VerticalStack` devono essere eseguiti si verifica durante l' `LayoutChildren` override. Il metodo usa la proprietà figlio `HorizontalOptions` per determinare come posizionare l'elemento figlio all'interno dello slot in `VerticalStack` . È invece possibile chiamare il metodo statico [ `Layout.LayoutChildIntoBoundingRect` ] (xrif: Xamarin.Forms . Layout. LayoutChildIntoBoundingRegion ( Xamarin.Forms . Oggetti visivi, Xamarin.Forms . Rettangolo)). Questo metodo chiama `Measure` sull'elemento figlio e usa le `HorizontalOptions` `VerticalOptions` proprietà e per posizionare l'elemento figlio all'interno del rettangolo specificato.

### <a name="invalidation"></a>Invalidamento

Spesso una modifica della proprietà di un elemento influiscono sulla modalità di visualizzazione di tale elemento nel layout. Il layout deve essere invalidato per attivare un nuovo layout.

`VisualElement`definisce un metodo protetto [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) , che in genere viene chiamato dal gestore modificato da proprietà di qualsiasi proprietà associabile la cui modifica influisca sulle dimensioni dell'elemento. Il `InvalidateMeasure` metodo genera un [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) evento.

La `Layout` classe definisce un metodo protetto simile denominato [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout) , che `Layout` deve essere chiamato da un derivato per tutte le modifiche che influiscono sulla modalità di posizionamento e ridimensionamento dei relativi elementi figlio.

### <a name="some-rules-for-coding-layouts"></a>Alcune regole per la codifica dei layout

1. Le proprietà definite da `Layout<T>` derivati devono essere supportate da proprietà associabili e i gestori modificati della proprietà devono chiamare `InvalidateLayout` .

2. Un `Layout<T>` derivato che definisce le proprietà associabili associate deve eseguire l'override [`OnAdded`](xref:Xamarin.Forms.Layout`1.OnAdded*) di per aggiungere un gestore modificato da proprietà ai relativi elementi figlio e [`OnRemoved`](xref:Xamarin.Forms.Layout`1.OnRemoved*) rimuovere tale gestore. Il gestore deve verificare la presenza di modifiche nelle proprietà associabili associate e rispondere chiamando `InvalidateLayout` .

3. Un `Layout<T>` derivato che implementa una cache di dimensioni figlio deve eseguire l'override di `InvalidateLayout` e [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) e cancellare la cache quando questi metodi vengono chiamati.

### <a name="a-layout-with-properties"></a>Un layout con proprietà

La [`WrapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) classe in [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) presuppone che tutti i relativi elementi figlio abbiano le stesse dimensioni ed esegue il wrapping degli elementi figlio da una riga o colonna a quella successiva. Definisce una `Orientation` proprietà come `StackLayout` , e e le `ColumnSpacing` `RowSpacing` proprietà `Grid` , come e memorizza nella cache le dimensioni figlio.

L'esempio di [**fotowrap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) inserisce un oggetto `WrapLayout` in un oggetto `ScrollView` per visualizzare le foto predefinite.

### <a name="no-unconstrained-dimensions-allowed"></a>Non sono consentite dimensioni non vincolate.

[`UniformGridLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs)Nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) è stato progettato per visualizzare tutti i relativi elementi figlio all'interno. Pertanto, non è in grado di gestire le dimensioni non vincolate e genera un'eccezione se ne viene rilevata una.

L'esempio [**fotogrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) illustra `UniformGridLayout` :

[![Schermata tripla della griglia delle foto](images/ch26fg08-small.png "Layout di griglia uniforme")](images/ch26fg08-large.png#lightbox "Layout di griglia uniforme")

### <a name="overlapping-children"></a>Elementi figlio sovrapposti

Un `Layout<T>` derivato può sovrapporsi ai relativi elementi figlio. Tuttavia, viene eseguito il rendering degli elementi figlio nell'ordine della `Children` raccolta e non nell'ordine in cui `Layout` vengono chiamati i metodi.

La `Layout` classe definisce due metodi che consentono di spostare un elemento figlio all'interno della raccolta:

- [ `LowerChild` ] (xrif: Xamarin.Forms . Layout. LowerChild ( Xamarin.Forms . View)) per spostare un elemento figlio all'inizio della raccolta
- [ `RaiseChild` ] (xrif: Xamarin.Forms . Layout. RaiseChild ( Xamarin.Forms . View)) per spostare un elemento figlio alla fine della raccolta

Per gli elementi figlio sovrapposti, i figli alla fine della raccolta appaiono visivamente sopra gli elementi figlio all'inizio della raccolta.

La [`OverlapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) definisce una proprietà associata per indicare l'ordine di rendering e quindi consentire la visualizzazione di uno dei relativi elementi figlio sopra gli altri. Nell'esempio [**StudentCardFile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) viene illustrato quanto segue:

[![Schermata tripla della griglia dei file delle schede degli studenti](images/ch26fg10-small.png "Elementi figlio layout sovrapposti")](images/ch26fg10-large.png#lightbox "Elementi figlio layout sovrapposti")

### <a name="more-attached-bindable-properties"></a>Altre proprietà associabili associate

La [`CartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) definisce le proprietà associate associabili per specificare due `Point` valori e un valore di spessore e manipola `BoxView` gli elementi in modo che siano simili alle righe.

L'esempio [**UnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) lo usa per creare un cubo 3D.

### <a name="layout-and-layoutto"></a>Layout e LayoutTo

Un `Layout<T>` derivato può chiamare `LayoutTo` anziché `Layout` per animare il layout. [`AnimatedCartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs)Questa operazione viene eseguita dalla classe e nell'esempio [**AnimatedUnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) .

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 26 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Esempi del capitolo 26](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Creazione di layout personalizzati](~/xamarin-forms/user-interface/layouts/custom.md)
