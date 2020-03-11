---
title: Riepilogo del capitolo 14. Layout assoluto
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 14. Layout assoluto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c489bf244396cf180ed8e1272308048a14b67300
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771137"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Riepilogo del capitolo 14. Layout assoluto

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

Analogamente a `StackLayout`, [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) deriva da `Layout<View>` ed eredita una proprietà `Children`. `AbsoluteLayout` implementa un sistema di layout che richiede al programmatore di specificare le posizioni degli elementi figlio e, facoltativamente, le relative dimensioni. La posizione viene specificata dall'angolo superiore sinistro del figlio rispetto all'angolo superiore sinistro del `AbsoluteLayout` in unità indipendenti dal dispositivo. `AbsoluteLayout` implementa inoltre una funzionalità di posizionamento e dimensionamento proporzionale.

`AbsoluteLayout` deve essere considerato un sistema di layout per scopi specifici da utilizzare solo quando il programmatore è in grado di imporre una dimensione agli elementi figlio (ad esempio, `BoxView` elementi) o quando le dimensioni dell'elemento non influiscono sul posizionamento di altri elementi figlio. Le proprietà `HorizontalOptions` e `VerticalOptions` non hanno effetto sugli elementi figlio di un `AbsoluteLayout`.

In questo capitolo viene inoltre introdotta la caratteristica importante delle *proprietà associabili associate* che consentono di associare le proprietà definite in una classe (in questo caso `AbsoluteLayout`) a un'altra classe (un figlio della `AbsoluteLayout`).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout nel codice

È possibile aggiungere un elemento figlio alla raccolta `Children` di un `AbsoluteLayout` utilizzando il metodo [`Add`](xref:System.Collections.Generic.ICollection`1.Add*) standard, ma `AbsoluteLayout` fornisce anche un metodo di [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) esteso che consente di specificare un [`Rectangle`](xref:Xamarin.Forms.Rectangle). Un altro metodo [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) richiede solo una [`Point`](xref:Xamarin.Forms.Point), nel qual caso l'elemento figlio non è vincolato e le dimensioni stesse.

È possibile creare un valore di `Rectangle` con un [Costruttore](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) che richiede quattro valori &mdash; primi due indicanti la posizione dell'angolo superiore sinistro del figlio rispetto al relativo padre e la seconda due che indica le dimensioni del figlio. In alternativa, è possibile usare un [Costruttore](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size)) che richiede un `Point` e un valore [`Size`](xref:Xamarin.Forms.Size) .

Questi `Add` metodi sono illustrati in [**AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), che posiziona `BoxView` elementi usando valori `Rectangle` e un elemento `Label` usando solo un valore di `Point`.

L'esempio [**ChessboardFixed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) usa gli elementi 32 `BoxView` per creare il pattern scacchiera. Il programma fornisce agli elementi `BoxView` una dimensione hardcoded di 35 unità quadrate. Il `AbsoluteLayout` dispone di `HorizontalOptions` e `VerticalOptions` impostato su `LayoutOptions.Center`, che fa sì che le dimensioni totali del `AbsoluteLayout` siano pari a 280 unità quadrate.

## <a name="attached-bindable-properties"></a>Proprietà associabili associate

È anche possibile impostare la posizione e, facoltativamente, le dimensioni di un elemento figlio di un `AbsoluteLayout` dopo che è stato aggiunto alla raccolta `Children` utilizzando il metodo statico [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle)). Il primo argomento è figlio; il secondo è un oggetto `Rectangle`. È possibile specificare che l'elemento figlio si ridimensiona orizzontalmente e/o verticalmente impostando i valori di larghezza e altezza sulla costante [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) .

L'esempio [**ChessboardDynamic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) inserisce il `AbsoluteLayout` in una `ContentView` con un gestore `SizeChanged` per chiamare `AbsoluteLayout.SetLayoutBounds` su tutti gli elementi figlio per renderli più grandi possibile.  

La proprietà associabile associata che `AbsoluteLayout` definisce è il campo statico di sola lettura di tipo `BindableProperty` [`AbsoluteLayout.LayoutBoundsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty)denominato. Il metodo `AbsoluteLayout.SetLayoutBounds` statico viene implementato chiamando `SetValue` sull'elemento figlio con il `AbsoluteLayout.LayoutBoundsProperty`. L'elemento figlio contiene un dizionario in cui sono archiviate le proprietà associabili associata e il relativo valore. Durante il layout, il `AbsoluteLayout` può ottenere tale valore chiamando [`AbsoluteLayout.GetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject)), che viene implementato con una chiamata di `GetValue`.

## <a name="proportional-sizing-and-positioning"></a>Ridimensionamento proporzionale e posizionamento

`AbsoluteLayout` implementa una funzionalità di ridimensionamento e posizionamento proporzionale. La classe definisce una seconda proprietà associabile associata, [`LayoutFlagsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty), con i metodi statici correlati [`AbsoluteLayout.SetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags)) e [`AbsoluteLayout.GetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject)).

L'argomento da `AbsoluteLayout.SetLayoutFlags` e il valore restituito di `AbsoluteLayout.GetLayoutFlags` è un valore di tipo [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags), un'enumerazione con i membri seguenti:

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None) (uguale a 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional) (1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional) (2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional) (3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional) (4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional) (8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional) (12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All) (\xFFFFFFFF)

È possibile combinare questi valori con il linguaggio c# operatore OR.

Con questi flag impostati, alcune proprietà della struttura dei limiti del layout `Rectangle` utilizzata per posizionare e ridimensionare l'elemento figlio vengono interpretate in modo proporzionale.

Quando viene impostato il flag di `WidthProportional`, un valore `Width` 1 indica che l'elemento figlio ha la stessa larghezza del `AbsoluteLayout`. Viene utilizzato un approccio simile per l'altezza.

Il posizionamento proporzionale accetta le dimensioni conto. Quando viene impostato il flag di `XProportional`, la proprietà `X` dei limiti del layout `Rectangle` è proporzionale. Il valore 0 indica che il bordo sinistro del figlio è posizionato sul bordo sinistro del `AbsoluteLayout`, ma la posizione 1 indica che il bordo destro del figlio è posizionato sul bordo destro della `AbsoluteLayout`, non oltre il bordo destro del `AbsoluteLayout` come si può immaginare. Una proprietà `X` di 0,5 centra l'elemento figlio orizzontalmente nell'`AbsoluteLayout`.

Nell'esempio [**ChessboardProportional**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) viene illustrato l'utilizzo delle dimensioni e del posizionamento proporzionali.

## <a name="working-with-proportional-coordinates"></a>Utilizzo con coordinate proporzionale

In alcuni casi, è più facile pensare al posizionamento proporzionale in modo diverso rispetto al modo in cui viene implementato nel `AbsoluteLayout`. Potrebbe essere preferibile usare Coordinate proporzionali in cui una proprietà `X` di 1 posiziona il bordo sinistro del figlio (anziché il bordo destro) sul bordo destro della `AbsoluteLayout`.

Questo schema di posizionamento alternativa può essere chiamato "coordinates figlio frazionario". È possibile eseguire la conversione da coordinate figlio frazionarie ai limiti del layout necessari per `AbsoluteLayout` usando le formule seguenti:

layoutBounds.X = (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

Questa operazione è illustrata nell'esempio [**ProportionalCoordinateCalc**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) .

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout e XAML

È possibile utilizzare un `AbsoluteLayout` in XAML e impostare le proprietà associabili associate negli elementi figlio di un `AbsoluteLayout` utilizzando i valori di attributo `AbsoluteLayout.LayoutBounds` e `AbsoluteLayout.LayoutFlags`. Questa operazione viene illustrata negli esempi di [**AbsoluteXamlDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) e [**ChessboardXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) . Quest'ultimo programma contiene 32 elementi `BoxView` ma usa una `Style` implicita che include la proprietà `AbsoluteLayout.LayoutFlags` per ridurre al minimo il markup.

Un attributo in XAML costituito da un nome di classe, un punto e un nome di proprietà è *sempre* una proprietà associabile associata.

## <a name="overlays"></a>Sovrimpressioni

È possibile utilizzare `AbsoluteLayout` per costruire una *sovrimpressione*che copre la pagina con altri controlli, forse per impedire all'utente di interagire con i normali controlli della pagina.

Nell'esempio [**SimpleOverlay**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) viene illustrata questa tecnica e viene inoltre illustrata la [`ProgressBar`](xref:Xamarin.Forms.ProgressBar), che Visualizza l'ambito in cui un programma ha completato un'attività.

## <a name="some-fun"></a>Alcuni divertenti

L'esempio [**DotMatrixClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) Visualizza l'ora corrente con una visualizzazione della matrice punto 5x7 simulata. Ogni punto è un `BoxView` (sono presenti 228) ridimensionato e posizionato sulla `AbsoluteLayout`.

[![Schermata tripla del clock della matrice di punti](images/ch14fg08-small.png "Clock della matrice a punti")](images/ch14fg08-large.png#lightbox "Clock della matrice a punti")

Il programma [**BouncingText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) anima due oggetti `Label` per eseguire il rimbalzo orizzontalmente e verticalmente sullo schermo.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 14 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Esempi del capitolo 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md)
