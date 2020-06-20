---
title: Riepilogo del capitolo 14. Layout assoluto
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 14. Layout assoluto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 72ee9c4a481388e69aeeb52dbd5b8eeaabb164f6
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136760"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Riepilogo del capitolo 14. Layout assoluto

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

Like `StackLayout` , [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) deriva da `Layout<View>` ed eredita una `Children` Proprietà. `AbsoluteLayout`implementa un sistema di layout che richiede al programmatore di specificare le posizioni degli elementi figlio e, facoltativamente, le relative dimensioni. La posizione viene specificata dall'angolo superiore sinistro del figlio rispetto all'angolo superiore sinistro dell'oggetto `AbsoluteLayout` in unità indipendenti dal dispositivo. `AbsoluteLayout`implementa inoltre una funzionalità di posizionamento e dimensionamento proporzionale.

`AbsoluteLayout`deve essere considerato come un sistema di layout per scopi specifici da usare solo quando il programmatore può imporre una dimensione agli elementi figlio (ad esempio, `BoxView` elementi) o quando le dimensioni dell'elemento non influiscono sul posizionamento di altri elementi figlio. Le `HorizontalOptions` `VerticalOptions` proprietà e non hanno effetto sugli elementi figlio di un oggetto `AbsoluteLayout` .

Questo capitolo introduce anche la caratteristica importante delle *proprietà associabili associate* che consentono di associare le proprietà definite in una classe (in questo caso `AbsoluteLayout` ) a un'altra classe (un figlio di `AbsoluteLayout` ).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout nel codice

È possibile aggiungere un elemento figlio alla `Children` raccolta di un oggetto `AbsoluteLayout` usando il [`Add`](xref:System.Collections.Generic.ICollection`1.Add*) metodo standard, ma `AbsoluteLayout` fornisce anche un [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) Metodo esteso che consente di specificare un oggetto [`Rectangle`](xref:Xamarin.Forms.Rectangle) . Un altro [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) metodo richiede solo un oggetto [`Point`](xref:Xamarin.Forms.Point) , nel qual caso l'elemento figlio non è vincolato e viene ridimensionato.

È possibile creare un `Rectangle` valore con un [Costruttore](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) che richiede quattro valori &mdash; i primi due indicanti la posizione dell'angolo superiore sinistro del figlio rispetto al relativo elemento padre e la seconda due che indica le dimensioni del figlio. In alternativa, è possibile usare un [Costruttore] (xrif: Xamarin.Forms . Rettangolo .% 23ctor ( Xamarin.Forms . Punto, Xamarin.Forms . Size)) che richiede un oggetto `Point` e un [`Size`](xref:Xamarin.Forms.Size) valore.

Questi `Add` metodi vengono illustrati in [**AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), che posiziona `BoxView` gli elementi usando `Rectangle` i valori e un `Label` elemento usando solo un `Point` valore.

L'esempio [**ChessboardFixed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) USA `BoxView` gli elementi 32 per creare il pattern scacchiera. Il programma fornisce agli `BoxView` elementi una dimensione hardcoded di 35 unità quadrate. Il `AbsoluteLayout` valore di è `HorizontalOptions` e `VerticalOptions` impostato `LayoutOptions.Center` su, che determina la `AbsoluteLayout` dimensione totale di 280 unità.

## <a name="attached-bindable-properties"></a>Proprietà associabili associate

È anche possibile impostare la posizione e, facoltativamente, la dimensione di un elemento figlio di un oggetto `AbsoluteLayout` dopo che è stato aggiunto alla `Children` raccolta usando il metodo statico [ `AbsoluteLayout.SetLayoutBounds` ] (xrif: Xamarin.Forms . AbsoluteLayout. SetLayoutBounds ( Xamarin.Forms . BindableObject, Xamarin.Forms . Rettangolo)). Il primo argomento è figlio; il secondo è un `Rectangle` oggetto. È possibile specificare che l'elemento figlio si ridimensiona orizzontalmente e/o verticalmente impostando i valori di larghezza e altezza sulla [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) costante.

L'esempio [**ChessboardDynamic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) inserisce `AbsoluteLayout` in un oggetto `ContentView` con un `SizeChanged` gestore per chiamare `AbsoluteLayout.SetLayoutBounds` su tutti gli elementi figlio per renderli più grandi possibile.  

La proprietà associabile associata che `AbsoluteLayout` definisce è il campo statico di sola lettura di tipo `BindableProperty` denominato [`AbsoluteLayout.LayoutBoundsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) . Il `AbsoluteLayout.SetLayoutBounds` metodo statico viene implementato chiamando `SetValue` sull'elemento figlio con `AbsoluteLayout.LayoutBoundsProperty` . L'elemento figlio contiene un dizionario in cui vengono archiviate la proprietà associabile associata e il relativo valore. Durante il layout, `AbsoluteLayout` può ottenere tale valore chiamando [ `AbsoluteLayout.GetLayoutBounds` ] (xrif: Xamarin.Forms . AbsoluteLayout. GetLayoutBounds ( Xamarin.Forms . BindableObject)), implementato con una chiamata a `GetValue` .

## <a name="proportional-sizing-and-positioning"></a>Dimensionamento e posizionamento proporzionali

`AbsoluteLayout`implementa una funzionalità di ridimensionamento e posizionamento proporzionale. La classe definisce una seconda proprietà associabile associata, [`LayoutFlagsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) , con i metodi statici correlati [ `AbsoluteLayout.SetLayoutFlags` ] (xrif: Xamarin.Forms . AbsoluteLayout. SetLayoutFlags ( Xamarin.Forms . BindableObject, Xamarin.Forms . AbsoluteLayoutFlags)) e [ `AbsoluteLayout.GetLayoutFlags` ] (xrif: Xamarin.Forms . AbsoluteLayout. GetLayoutFlags ( Xamarin.Forms . BindableObject)).

L'argomento di `AbsoluteLayout.SetLayoutFlags` e il valore restituito di `AbsoluteLayout.GetLayoutFlags` è un valore di tipo [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) , un'enumerazione con i membri seguenti:

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None)(uguale a 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional)1
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional)2
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional)3
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional)4
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional)8
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional)12
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All)(\xFFFFFFFF)

È possibile combinare questi con l'operatore OR bit per bit C#.

Con questi flag impostati, alcune proprietà della `Rectangle` struttura dei limiti del layout utilizzata per posizionare e ridimensionare l'elemento figlio vengono interpretate in modo proporzionale.

Quando il `WidthProportional` flag è impostato, il `Width` valore 1 indica che l'elemento figlio ha la stessa larghezza dell'oggetto `AbsoluteLayout` . Viene utilizzato un approccio simile per l'altezza.

Il posizionamento proporzionale prende in considerazione le dimensioni. Quando il `XProportional` flag è impostato, la `X` proprietà dei `Rectangle` limiti del layout è proporzionale. Il valore 0 indica che il bordo sinistro del figlio è posizionato sul bordo sinistro dell'oggetto `AbsoluteLayout` , ma la posizione 1 indica che il bordo destro del figlio è posizionato sul bordo destro dell'oggetto `AbsoluteLayout` , non oltre il bordo destro di `AbsoluteLayout` come previsto. Una `X` proprietà di 0,5 centra l'elemento figlio orizzontalmente nell'oggetto `AbsoluteLayout` .

Nell'esempio [**ChessboardProportional**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) viene illustrato l'utilizzo delle dimensioni e del posizionamento proporzionali.

## <a name="working-with-proportional-coordinates"></a>Utilizzo di coordinate proporzionali

In alcuni casi, è più facile pensare al posizionamento proporzionale in modo diverso rispetto a come viene implementato in `AbsoluteLayout` . Potrebbe essere preferibile usare Coordinate proporzionali in cui una `X` proprietà di 1 posiziona il bordo sinistro del figlio (anziché il bordo destro) sul bordo destro di `AbsoluteLayout` .

Questo schema di posizionamento alternativo può essere chiamato "coordinate figlio frazionarie". È possibile eseguire la conversione da coordinate figlio frazionarie ai limiti del layout necessari per `AbsoluteLayout` utilizzare le formule seguenti:

layoutBounds. X = (fractionalChildCoordinate. X/(1-layoutBounds. Width))

layoutBounds. Y = (fractionalChildCoordinate. Y/(1-layoutBounds. Height))

Questa operazione è illustrata nell'esempio [**ProportionalCoordinateCalc**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) .

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout e XAML

È possibile utilizzare `AbsoluteLayout` in XAML e impostare le proprietà associabili associate negli elementi figlio di un oggetto `AbsoluteLayout` utilizzando i valori di attributo `AbsoluteLayout.LayoutBounds` e `AbsoluteLayout.LayoutFlags` . Questa operazione viene illustrata negli esempi di [**AbsoluteXamlDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) e [**ChessboardXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) . Quest'ultimo programma contiene 32 `BoxView` elementi ma utilizza un implicito `Style` che include la `AbsoluteLayout.LayoutFlags` proprietà per ridurre al minimo il markup.

Un attributo in XAML costituito da un nome di classe, un punto e un nome di proprietà è *sempre* una proprietà associabile associata.

## <a name="overlays"></a>Sovrimpressioni

È possibile utilizzare `AbsoluteLayout` per costruire una *sovrimpressione*che copre la pagina con altri controlli, forse per impedire all'utente di interagire con i normali controlli della pagina.

Nell'esempio [**SimpleOverlay**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) viene illustrata questa tecnica e viene inoltre illustrato [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) , che consente di visualizzare l'ambito in cui un programma ha completato un'attività.

## <a name="some-fun"></a>Divertimento

L'esempio [**DotMatrixClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) Visualizza l'ora corrente con una visualizzazione della matrice punto 5x7 simulata. Ogni punto è un `BoxView` (228) ridimensionato e posizionato in corrispondenza di `AbsoluteLayout` .

[![Schermata tripla del clock della matrice di punti](images/ch14fg08-small.png "Clock della matrice a punti")](images/ch14fg08-large.png#lightbox "Clock della matrice a punti")

Il programma [**BouncingText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) anima due `Label` oggetti per eseguire il rimbalzo orizzontalmente e verticalmente sullo schermo.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 14 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Esempi del capitolo 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md)
