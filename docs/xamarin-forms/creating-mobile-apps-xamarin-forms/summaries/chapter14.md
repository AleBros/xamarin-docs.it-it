---
title: Riepilogo del capitolo 14. Layout assoluto
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 14. Layout assoluto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 6393794a20a80fae43e31a96efd4941f4b633c28
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563719"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Riepilogo del capitolo 14. Layout assoluto

Ad esempio `StackLayout`, [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout) deriva da `Layout<View>` ed eredita un `Children` proprietà. `AbsoluteLayout` implementa un sistema di layout che richiede al programmatore di specificare le posizioni dei relativi elementi figlio e, facoltativamente, le relative dimensioni. La posizione è specificata per l'angolo superiore sinistro dell'elemento figlio rispetto all'angolo superiore sinistro del `AbsoluteLayout` in unità indipendenti dal dispositivo. `AbsoluteLayout` implementa anche una funzionalità di ridimensionamento e posizionamento proporzionale.

`AbsoluteLayout` deve essere considerata come un sistema di layout speciale per essere usato solo quando il programmatore può imporre una dimensione sugli elementi figlio (ad esempio, `BoxView` elementi) o quando le dimensioni dell'elemento non influenza il posizionamento degli altri figli. Il `HorizontalOptions` e `VerticalOptions` delle proprietà non hanno effetto sugli elementi figlio di un `AbsoluteLayout`.

In questo capitolo introduce inoltre la funzionalità importante di *collegati proprietà associabili* che consentono le proprietà definite in una classe (in questo caso `AbsoluteLayout`) da associare a un'altra classe (un elemento figlio del `AbsoluteLayout`).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout nel codice

È possibile aggiungere un elemento figlio per il `Children` raccolta di un `AbsoluteLayout` usando lo standard [ `Add` ](xref:System.Collections.Generic.ICollection`1.Add*) metodo, ma `AbsoluteLayout` fornisce inoltre un'estesa [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Rectangle/Xamarin.Forms.AbsoluteLayoutFlags/) metodo che consente di specificare una [ `Rectangle` ](xref:Xamarin.Forms.Rectangle). Un'altra [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Point/) metodo richiede solo una [ `Point` ](xref:Xamarin.Forms.Point), nel qual caso l'elemento figlio è non vincolato e ridimensionato in modo.

È possibile creare un `Rectangle` valore con un [costruttore](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) che richiede quattro valori &mdash; le prime due che indica la posizione dell'angolo superiore sinistro dell'elemento figlio rispetto al padre e i due secondi che indica il dimensioni del bambino. In alternativa è possibile usare una [costruttore](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size)) che richiede un `Point` e un [ `Size` ](xref:Xamarin.Forms.Size) valore.

Questi `Add` vengono illustrati i metodi [ **AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), quali posizioni `BoxView` elementi utilizzando `Rectangle` valori e un `Label` elemento utilizzando solo un `Point` valore.

Il [ **ChessboardFixed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) esempio Usa 32 `BoxView` elementi per creare il modello conoscerli. Il programma offre il `BoxView` dimensioni di un impostate come hardcoded gli elementi di 35 unità quadrato. Il `AbsoluteLayout` ha relativi `HorizontalOptions` e `VerticalOptions` impostata su `LayoutOptions.Center`, determinando in tal modo il `AbsoluteLayout` per avere una dimensione totale del quadrato 280 unità.

## <a name="attached-bindable-properties"></a>Proprietà associabili associate

È anche possibile impostare la posizione e, facoltativamente, le dimensioni di un elemento figlio di un `AbsoluteLayout` dopo che è stato aggiunto per il `Children` raccolta usando il metodo statico [ `AbsoluteLayout.SetLayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle)). Il primo argomento è l'elemento figlio; il secondo è un `Rectangle` oggetto. È possibile specificare che l'elemento figlio ridimensionato in modo orizzontale e/o in verticale impostando i valori di larghezza e altezza il [ `AbsoluteLayout.AutoSize` ](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) costante.

Il [ **ChessboardDynamic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) esempio inserisce il `AbsoluteLayout` in un `ContentView` con un `SizeChanged` gestore da chiamare `AbsoluteLayout.SetLayoutBounds` su tutti i nodi figlio per renderli più grande possibile.  

La proprietà associabile associata che `AbsoluteLayout` definisce è il campo statico di sola lettura di tipo `BindableProperty` denominate [ `AbsoluteLayout.LayoutBoundsProperty` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty). Il metodo statico `AbsoluteLayout.SetLayoutBounds` metodo viene implementato mediante la chiamata `SetValue` sull'elemento figlio con il `AbsoluteLayout.LayoutBoundsProperty`. L'elemento figlio contiene un dizionario in cui sono archiviate le proprietà associabili associata e il relativo valore. Durante il layout, il `AbsoluteLayout` può ottenere tale valore chiamando [ `AbsoluteLayout.GetLayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject)), che viene implementato con un `GetValue` chiamare.

## <a name="proportional-sizing-and-positioning"></a>Ridimensionamento proporzionale e posizionamento

`AbsoluteLayout` implementa un ridimensionamento proporzionale e funzionalità di posizionamento. La classe definisce una seconda proprietà associabili collegate, [ `LayoutFlagsProperty` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty), con i metodi statici correlati [ `AbsoluteLayout.SetLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags)) e [ `AbsoluteLayout.GetLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject)).

L'argomento `AbsoluteLayout.SetLayoutFlags` e il valore restituito di `AbsoluteLayout.GetLayoutFlags` è un valore di tipo [ `AbsoluteLayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayoutFlags), un'enumerazione con i membri seguenti:

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None) (uguale a 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional) (1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional) (2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional) (3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional) (4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional) (8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional) (12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All) (\xFFFFFFFF)

È possibile combinare questi valori con il linguaggio c# operatore OR.

Con questi set di flag, determinate proprietà del `Rectangle` struttura dei limiti di layout utilizzato per posizionare e ridimensionare l'elemento figlio vengono interpretati in modo proporzionale.

Quando la `WidthProportional` flag è impostato, un `Width` pari a 1 indica che l'elemento figlio è la stessa larghezza di `AbsoluteLayout`. Viene utilizzato un approccio simile per l'altezza.

Il posizionamento proporzionale accetta le dimensioni conto. Quando la `XProportional` flag è impostato, il `X` proprietà del `Rectangle` dei limiti del layout sono proporzionali. Un valore pari a 0 indica che l'elemento figlio a sinistra del bordo è posizionato in corrispondenza il bordo sinistro del `AbsoluteLayout`, ma una posizione 1 indica che bordo destro del figlio è posizionato in corrispondenza il bordo destro del `AbsoluteLayout`, non oltre il bordo destro del `AbsoluteLayout` come si farebbe expec t. Un' `X` proprietà pari a 0,5 Centra l'elemento figlio in orizzontale il `AbsoluteLayout`.

Il [ **ChessboardProportional** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) esempio dimostra l'uso di ridimensionamento proporzionale e posizionamento.

## <a name="working-with-proportional-coordinates"></a>Utilizzo con coordinate proporzionale

In alcuni casi, è facile pensare in modo diverso rispetto a come viene implementato nel posizionamento proporzionale di `AbsoluteLayout`. È preferibile utilizzare proporzionale coordinate in cui un' `X` proprietà 1 posiziona bordo sinistro del figlio (anziché il bordo destro) contro il bordo destro del `AbsoluteLayout`.

Questo schema di posizionamento alternativa può essere chiamato "coordinates figlio frazionario". È possibile convertire da coordinate frazionari figlio fino ai limiti di layout necessari per `AbsoluteLayout` utilizzando le formule seguenti:

layoutBounds.X = (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

Il [ **ProportionalCoordinateCalc** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) esempio viene illustrata questa.

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout e XAML

È possibile usare un `AbsoluteLayout` in XAML e impostare le proprietà associabili associate per gli elementi figlio di un `AbsoluteLayout` usando i valori di attributo `AbsoluteLayout.LayoutBounds` e `AbsoluteLayout.LayoutFlags`. Ciò è dimostrato nel [ **AbsoluteXamlDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) e il [ **ChessboardXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) esempi. Il programma di quest'ultimo contiene 32 `BoxView` elementi, ma Usa implicita `Style` che include il `AbsoluteLayout.LayoutFlags` proprietà per mantenere il markup fino a un valore minimo.

È un attributo in XAML che è costituito da un nome di classe, un punto e il nome della proprietà *sempre* una proprietà associabile associata.

## <a name="overlays"></a>Sovrimpressioni

È possibile usare `AbsoluteLayout` per costruire un' *sovrimpressione*, che prevede la pagina con altri controlli, ad esempio per impedire che l'utente interagisce con i normali controlli nella pagina.

Il [ **SimpleOverlay** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) sample illustra questa tecnica e viene inoltre illustrato il [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar), che consente di visualizzare l'extent in cui un programma è stata completata una attività.

## <a name="some-fun"></a>Alcuni divertenti

Il [ **DotMatrixClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) esempio visualizza l'ora corrente con un display ad 5x7 aghi simulato. Ogni punto rappresenta un `BoxView` (sono presenti 228 di essi) ridimensionato e posizionato in corrispondenza di `AbsoluteLayout`.

[![Tripla screenshot dell'orologio aghi](images/ch14fg08-small.png "Clock aghi")](images/ch14fg08-large.png#lightbox "aghi orologio")

Il [ **BouncingText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) programma anima due `Label` oggetti rimbalzare orizzontalmente e verticalmente sullo schermo.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 14 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Esempi di capitolo 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md)
