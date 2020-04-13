---
title: Riassunto del capitolo 26. Layout personalizzati
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 26. Layout personalizzati'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2B7F4346-414E-49FF-97FB-B85E92D98A21
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1eb5153f8ab295696e373f4fdb65a4f8820a05bc
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70770934"
---
# <a name="summary-of-chapter-26-custom-layouts"></a>Riassunto del capitolo 26. Layout personalizzati

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)

Xamarin.Forms include diverse [`Layout<View>`](xref:Xamarin.Forms.Layout`1)classi derivate da :

- `StackLayout`,
- `Grid`,
- `AbsoluteLayout` e
- `RelativeLayout`.

In questo capitolo viene descritto come creare `Layout<View>`classi personalizzate che derivano da .

## <a name="an-overview-of-layout"></a>Panoramica del layout

Non esiste un sistema centralizzato che gestisca il layout Xamarin.Forms. Ogni elemento è responsabile di determinare quale deve essere la propria dimensione e come eseguire il rendering all'interno di una determinata area.

### <a name="parents-and-children"></a>Genitori e figli

Ogni elemento che ha figli è responsabile del posizionamento di tali elementi all'interno di sé. È l'elemento padre che determina in ultima analisi le dimensioni che i suoi figli devono avere in base alle dimensioni disponibili e alle dimensioni che l'elemento figlio vuole avere.

### <a name="sizing-and-positioning"></a>Ridimensionamento e posizionamento

Il layout inizia nella parte superiore della struttura ad albero visuale con la pagina e quindi procede attraverso tutti i rami. Il metodo pubblico più [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) importante `VisualElement`nel layout è definito da . Ogni elemento padre di altri `Layout` elementi richiede che ognuno dei relativi elementi figlio dia all'elemento figlio una dimensione e una posizione rispetto a se stessa sotto forma di [`Rectangle`](xref:Xamarin.Forms.Rectangle) valore. Queste `Layout` chiamate si propagano attraverso la struttura ad albero visuale.

Una chiamata `Layout` a è necessaria per un elemento da visualizzare sullo schermo e fa sì che le seguenti proprietà di sola lettura da impostare. Essi sono coerenti con il `Rectangle` passato al metodo:

- [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds)di tipo`Rectangle`
- [`X`](xref:Xamarin.Forms.VisualElement.X)di tipo`double`
- [`Y`](xref:Xamarin.Forms.VisualElement.Y)di tipo`double`
- [`Width`](xref:Xamarin.Forms.VisualElement.Width)di tipo`double`
- [`Height`](xref:Xamarin.Forms.VisualElement.Height)di tipo`double`

Prima della `Layout` `Height` chiamata, `Width` e hanno &ndash;valori fittizi di 1.

Una chiamata `Layout` a attiva anche le chiamate ai seguenti metodi protetti:

- [`SizeAllocated`](xref:Xamarin.Forms.VisualElement.SizeAllocated(System.Double,System.Double)), che chiama
- [`OnSizeAllocated`](xref:Xamarin.Forms.VisualElement.OnSizeAllocated(System.Double,System.Double)), che può essere sostituito.

Infine, viene generato il seguente evento:

- [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged)

Il `OnSizeAllocated` metodo viene `Page` sottoposto a override da e `Layout`, che sono le uniche due classi in Xamarin.Forms che possono avere elementi figlio. Chiamate al metodo sottoposto a override

- [`UpdateChildrenLayout`](xref:Xamarin.Forms.Page.UpdateChildrenLayout)derivati `Page` e [`UpdateChildrenLayout`](xref:Xamarin.Forms.Layout.UpdateChildrenLayout) `Layout` derivati, che
- [`LayoutChildren`](xref:Xamarin.Forms.Page.LayoutChildren(System.Double,System.Double,System.Double,System.Double))derivati `Page` e [`LayoutChildren`](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) `Layout` derivati.

`LayoutChildren`quindi `Layout` chiama per ciascuno degli elementi figlio dell'elemento. Se almeno un elemento `Bounds` figlio ha una nuova impostazione, viene generato il seguente evento:

- [`LayoutChanged`](xref:Xamarin.Forms.Page.LayoutChanged)derivati `Page` e [`LayoutChanged`](xref:Xamarin.Forms.Layout.LayoutChanged) `Layout` derivati

### <a name="constraints-and-size-requests"></a>Vincoli e richieste di dimensioni

Per `LayoutChildren` chiamare `Layout` in modo intelligente tutti i suoi figli, deve conoscere una dimensione *preferita* o *desiderata* per i bambini. Pertanto, `Layout` le richieste a ciascuno dei bambini sono generalmente precedute da

- [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double))

Dopo la pubblicazione del `GetSizeRequest` libro, il metodo è stato deprecato e sostituito con

- [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))

Il `Measure` metodo supporta [`Margin`](xref:Xamarin.Forms.View.Margin) la proprietà e [`MeasureFlag`](xref:Xamarin.Forms.MeasureFlags)include un argomento di tipo , che dispone di due membri:

- [`IncludeMargins`](xref:Xamarin.Forms.MeasureFlags.IncludeMargins)
- [`None`](xref:Xamarin.Forms.MeasureFlags.None)per non includere i margini

Per molti `GetSizeRequest` elementi `Measure` o ottiene la dimensione nativa dell'elemento dal relativo renderer. Entrambi i metodi dispongono di parametri per *i vincoli*di larghezza e altezza. Ad esempio, `Label` un oggetto utilizzerà il vincolo width per determinare come eseguire il wrapping di più righe di testo.

Entrambi `GetSizeRequest` `Measure` e restituiscono [`SizeRequest`](xref:Xamarin.Forms.SizeRequest)un valore di tipo , che dispone di due proprietà:

- [`Request`](xref:Xamarin.Forms.SizeRequest.Request)di tipo`Size`
- [`Minimum`](xref:Xamarin.Forms.SizeRequest.Minimum)di tipo`Size`

Molto spesso questi due valori sono `Minimum` uguali e il valore in genere può essere ignorato.

`VisualElement`definisce anche un metodo `GetSizeRequest` protetto simile `GetSizeRequest`a quello chiamato da :

- [`OnSizeRequest`](xref:Xamarin.Forms.VisualElement.OnSizeRequest(System.Double,System.Double))restituisce `SizeRequest` un valore

Tale metodo è ora deprecato e sostituito con:

- [`OnMeasure`](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))

Ogni classe che `Layout` deriva `Layout<T>` da `OnSizeRequest` `OnMeasure`o deve eseguire l'override di o . Questo è dove una classe di layout determina le proprie dimensioni, che è generalmente `GetSizeRequest` `Measure` in base alle dimensioni dei suoi elementi figlio, che ottiene chiamando o sugli elementi figlio. Prima e `OnSizeRequest` dopo `OnMeasure` `GetSizeRequest` la `Measure` chiamata o , oppure apporta modifiche in base alle seguenti proprietà:

- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)di `double`tipo , `Request` influisce sulla proprietà di`SizeRequest`
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)di `double`tipo , `Request` influisce sulla proprietà di`SizeRequest`
- [`MinimumWidthRequest`](xref:Xamarin.Forms.VisualElement.MinimumWidthRequest)di `double`tipo , `Minimum` influisce sulla proprietà di`SizeRequest`
- [`MinimumHeightRequest`](xref:Xamarin.Forms.VisualElement.MinimumHeightRequest)di `double`tipo , `Minimum` influisce sulla proprietà di`SizeRequest`

### <a name="infinite-constraints"></a>Vincoli infiniti

Gli argomenti del `GetSizeRequest` vincolo passati a (o `Measure`) e `OnSizeRequest` `OnMeasure`(o `Double.PositiveInfinity`) possono essere infiniti (ad esempio, valori di ). Tuttavia, `SizeRequest` il restituito da questi metodi non può contenere dimensioni infinite.

Vincoli infiniti indicano che la dimensione richiesta deve riflettere la dimensione naturale dell'elemento. Un `StackLayout` verticale `GetSizeRequest` `Measure`chiama (o ) sui relativi elementi figlio con un vincolo di altezza infinito. Un layout dello `GetSizeRequest` stack `Measure`orizzontale chiama (o ) sui relativi elementi figlio con un vincolo di larghezza infinita. Un `AbsoluteLayout` `GetSizeRequest` chiama `Measure`(o ) sui relativi elementi figlio con vincoli di larghezza e altezza infiniti.

### <a name="peeking-inside-the-process"></a>Sbirciare all'interno del processo

[**EsploraChildSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/ExploreChildSizes) visualizza le informazioni sulle richieste di vincoli e dimensioni per un layout semplice.

## <a name="deriving-from-layoutview"></a>Derivazione dal\<> della vista Layout

Una classe di layout `Layout<View>`personalizzata deriva da . Ha due responsabilità:

- Eseguire `OnMeasure` l'override per chiamare `Measure` tutti gli elementi figlio del layout. Restituire una dimensione richiesta per il layout stesso
- Eseguire `LayoutChildren` l'override per chiamare `Layout` tutti gli elementi figlio del layout

Il `for` `foreach` ciclo o in questi override `IsVisible` deve ignorare `false`qualsiasi elemento figlio la cui proprietà è impostata su .

Una chiamata `OnMeasure` a non è garantita. `OnMeasure`non verrà chiamato se l'elemento padre del layout regola le dimensioni del layout (ad esempio, un layout che riempie una pagina). Per questo `LayoutChildren` motivo, non è `OnMeasure` possibile basarsi sulle dimensioni figlio ottenute durante la chiamata. Molto spesso, `LayoutChildren` è `Measure` necessario chiamare i figli del layout, oppure è possibile implementare un tipo di logica di memorizzazione nella cache di dimensioni (da discutere più avanti).

### <a name="an-easy-example"></a>Un esempio semplice

L'esempio [**VerticalStackDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/VerticalStackDemo) [`VerticalStack`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter26/VerticalStackDemo/VerticalStackDemo/VerticalStackDemo/VerticalStack.cs) contiene una classe semplificata e una dimostrazione del relativo utilizzo.

### <a name="vertical-and-horizontal-positioning-simplified"></a>Posizionamento verticale e orizzontale semplificato

Uno dei processi `VerticalStack` che devono `LayoutChildren` essere eseguiti si verifica durante la sostituzione. Il metodo utilizza la `HorizontalOptions` proprietà dell'elemento figlio per determinare `VerticalStack`come posizionare l'elemento figlio all'interno del relativo slot nell'oggetto . È invece possibile [`Layout.LayoutChildIntoBoundingRect`](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle))chiamare il metodo statico . Questo metodo `Measure` chiama l'elemento `HorizontalOptions` `VerticalOptions` figlio e utilizza le proprietà e per posizionare l'elemento figlio all'interno del rettangolo specificato.

### <a name="invalidation"></a>Invalidazione

Spesso una modifica nella proprietà di un elemento influisce sul modo in cui tale elemento viene visualizzato nel layout. Il layout deve essere invalidato per attivare un nuovo layout.

`VisualElement`definisce un [`InvalidateMeasure`](xref:Xamarin.Forms.VisualElement.InvalidateMeasure)metodo protetto , che viene in genere chiamato dal gestore di proprietà modificata di qualsiasi proprietà associabile la cui modifica influisce sulle dimensioni dell'elemento. Il `InvalidateMeasure` metodo genera [`MeasureInvalidated`](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) un evento.

La `Layout` classe definisce un [`InvalidateLayout`](xref:Xamarin.Forms.Layout.InvalidateLayout)metodo protetto `Layout` simile denominato , che un derivato deve richiedere qualsiasi modifica che influisca sul modo in cui posiziona e ridimensiona i relativi elementi figlio.

### <a name="some-rules-for-coding-layouts"></a>Alcune regole per la codifica dei layout

1. Le proprietà `Layout<T>` definite da derivate devono essere supportate da proprietà `InvalidateLayout`associabili e i gestori delle proprietà modificate devono chiamare .

2. Un `Layout<T>` derivato che definisce le [`OnAdded`](xref:Xamarin.Forms.Layout`1.OnAdded*) proprietà associabili associate deve eseguire [`OnRemoved`](xref:Xamarin.Forms.Layout`1.OnRemoved*) l'override per aggiungere un gestore di proprietà modificate ai relativi elementi figlio e per rimuovere tale gestore. Il gestore deve verificare la presenza di modifiche `InvalidateLayout`in queste proprietà associabili associate e rispondere chiamando .

3. Un `Layout<T>` derivato che implementa una `InvalidateLayout` cache [`OnChildMeasureInvalidated`](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) di dimensioni figlio deve eseguire l'override e cancellare la cache quando vengono chiamati questi metodi.

### <a name="a-layout-with-properties"></a>Un layout con proprietà

La [`WrapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/WrapLayout.cs) classe in [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) presuppone che tutti i relativi elementi figlio abbiano le stesse dimensioni e che gli elementi figlio vengano disposti a capo da una riga (o colonna) alla successiva. Definisce una `Orientation` proprietà `StackLayout`come `ColumnSpacing` `RowSpacing` , `Grid`e e proprietà come , e memorizza nella cache le dimensioni figlio.

L'esempio [**PhotoWrap**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoWrap) inserisce un `WrapLayout` oggetto in un `ScrollView` oggetto per la visualizzazione delle foto stock.

### <a name="no-unconstrained-dimensions-allowed"></a>Non sono ammesse dimensioni non vincolate!

La [`UniformGridLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/UniformGridLayout.cs) libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) è destinata a visualizzare tutti i relativi elementi figlio all'interno di se stessa. Pertanto, non può gestire dimensioni non vincolate e genera un'eccezione se ne viene rilevata una.

L'esempio [**PhotoGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/PhotoGrid) illustra: `UniformGridLayout`

[![Triplo screenshot di Photo Grid](images/ch26fg08-small.png "Layout griglia uniforme")](images/ch26fg08-large.png#lightbox "Layout griglia uniforme")

### <a name="overlapping-children"></a>Bambini sovrapposti

Un `Layout<T>` derivato può sovrapporsi ai suoi figli. Tuttavia, il rendering degli elementi `Children` figlio viene eseguito nell'ordine nella raccolta e non nell'ordine in cui vengono chiamati i relativi `Layout` metodi.

La `Layout` classe definisce due metodi che consentono di spostare un elemento figlio all'interno della raccolta:The class defines two methods that allow you to move a child within the collection:

- [`LowerChild`](xref:Xamarin.Forms.Layout.LowerChild(Xamarin.Forms.View))per spostare un bambino all'inizio della collezione
- [`RaiseChild`](xref:Xamarin.Forms.Layout.RaiseChild(Xamarin.Forms.View))per spostare un bambino alla fine della collezione

Per gli elementi figlio sovrapposti, gli elementi figlio alla fine della raccolta vengono visualizzati visivamente sopra i bambini all'inizio della raccolta.

La [`OverlapLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/OverlapLayout.cs) classe nella libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) definisce una proprietà associata per indicare l'ordine di rendering e quindi consentire a uno dei relativi elementi figlio di essere visualizzato sopra gli altri. [**Nell'esempio StudentCardFile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/StudentCardFile) viene illustrato quanto segue:The StudentCardFile sample demonstrates this:

[![Tripla schermata di Griglia file scheda studente](images/ch26fg10-small.png "Layout figli sovrapposti")](images/ch26fg10-large.png#lightbox "Layout figli sovrapposti")

### <a name="more-attached-bindable-properties"></a>Proprietà associabili più associate

La [`CartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CartesianLayout.cs) classe nella libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) definisce le proprietà `Point` associabili associate per `BoxView` specificare due valori e un valore di spessore e modifica gli elementi in modo che siano simili alle linee.

Nell'esempio [**UnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/UnitCube) viene utilizzato questo valore per disegnare un cubo 3D.

### <a name="layout-and-layoutto"></a>Layout e LayoutTo

Un `Layout<T>` derivato `LayoutTo` può `Layout` chiamare piuttosto che animare il layout. La [`AnimatedCartesianLayout`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnimatedCartesianLayout.cs) classe esegue questa operazione e il [**AnimatedUnitCube**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26/AnimatedUnitCube) esempio viene illustrato.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 26 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch26-Apr2016.pdf)
- [Capitolo 26 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter26)
- [Creazione di layout personalizzati](~/xamarin-forms/user-interface/layouts/custom.md)
