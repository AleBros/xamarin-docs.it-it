---
title: Riepilogo del capitolo 14. Layout assoluto
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 14. Layout assoluto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: b8fe8f8bb0ba3204a312f3d91bc59b8fa3f7c03a
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241403"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Riepilogo del capitolo 14. Layout assoluto

Ad esempio `StackLayout`, [ `AbsoluteLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) deriva da `Layout<View>` ed eredita un `Children` proprietà. `AbsoluteLayout` implementa un sistema di layout che richiede al programmatore di specificare le posizioni dei relativi elementi figlio e, facoltativamente, le dimensioni. La posizione specificata dall'angolo superiore destro dell'elemento figlio rispetto all'angolo superiore sinistro del `AbsoluteLayout` in unità indipendenti dal dispositivo. `AbsoluteLayout` implementa anche una funzionalità di ridimensionamento e posizionamento proporzionale.

`AbsoluteLayout` deve essere considerato come un sistema di layout speciale per essere utilizzato solo quando il programmatore può imporre una dimensione sugli elementi figlio (ad esempio, `BoxView` elementi) o quando le dimensioni dell'elemento non influenza il posizionamento degli altri elementi figlio. Il `HorizontalOptions` e `VerticalOptions` non hanno effetto su elementi figlio di un `AbsoluteLayout`.

In questo capitolo introduce inoltre una caratteristica importante degli *proprietà associabile associate* che consentono di proprietà definite in una classe (in questo caso `AbsoluteLayout`) da associare a un'altra classe (un elemento figlio del `AbsoluteLayout`).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout nel codice

È possibile aggiungere un elemento figlio di `Children` insieme di un `AbsoluteLayout` utilizzando lo standard [ `Add` ](https://developer.xamarin.com/api/member/System.Collections.Generic.ICollection%3CT%3E.Add/p/T/) (metodo), ma `AbsoluteLayout` fornisce inoltre un'estesa [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Rectangle/Xamarin.Forms.AbsoluteLayoutFlags/) metodo che consente di specificare un [ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/). Un altro [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout+IAbsoluteList%3CT%3E.Add/p/Xamarin.Forms.View/Xamarin.Forms.Point/) metodo richiede solo un [ `Point` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Point/), nel qual caso l'elemento figlio non è vincolato e ridimensiona.

È possibile creare un `Rectangle` valore con un [costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Rectangle.Rectangle/p/System.Double/System.Double/System.Double/System.Double/) che richiede quattro valori &mdash; le prime due che indica la posizione dell'angolo superiore sinistro dell'elemento figlio rispetto al padre e i due secondi che indica il dimensione dell'elemento figlio. Oppure è possibile utilizzare un [costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Rectangle.Rectangle/p/Xamarin.Forms.Point/Xamarin.Forms.Size/) che richiede un `Point` e [ `Size` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Size/) valore.

Questi `Add` i metodi sono illustrati in [ **AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), quali posizioni `BoxView` elementi utilizzando `Rectangle` valori e un `Label` elemento utilizzando solo un `Point` valore.

Il [ **ChessboardFixed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) Usa 32 `BoxView` elementi per creare il modello conoscerli. Il programma offre il `BoxView` dimensioni elementi un hardcoded di quadrato di 35 unità. Il `AbsoluteLayout` è relativo `HorizontalOptions` e `VerticalOptions` impostato su `LayoutOptions.Center`, causando il `AbsoluteLayout` avrà una dimensione totale del quadrato 280 unità.

## <a name="attached-bindable-properties"></a>Proprietà associabili associate

È anche possibile impostare la posizione e, facoltativamente, le dimensioni di un elemento figlio di un `AbsoluteLayout` dopo che è stato aggiunto il `Children` raccolta utilizzando il metodo statico [ `AbsoluteLayout.SetLayoutBounds` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.SetLayoutBounds/p/Xamarin.Forms.BindableObject/Xamarin.Forms.Rectangle/). Il primo argomento è l'elemento figlio; il secondo è un `Rectangle` oggetto. È possibile specificare che l'elemento figlio Ridimensiona orizzontalmente o verticalmente impostando i valori di larghezza e altezza il [ `AbsoluteLayout.AutoSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.AbsoluteLayout.AutoSize/) costante.

Il [ **ChessboardDynamic** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) esempio inserisce il `AbsoluteLayout` in un `ContentView` con un `SizeChanged` gestore da chiamare `AbsoluteLayout.SetLayoutBounds` su tutti gli elementi figlio in modo da renderle più grande possibile.  

La proprietà associabile associata che `AbsoluteLayout` definisce è il campo statico di sola lettura di tipo `BindableProperty` denominato [ `AbsoluteLayout.LayoutBoundsProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty/). Il metodo statico `AbsoluteLayout.SetLayoutBounds` metodo è implementato mediante la chiamata `SetValue` sull'elemento figlio con il `AbsoluteLayout.LayoutBoundsProperty`. L'elemento figlio contiene un dizionario in cui sono archiviate le proprietà associabili associata e il relativo valore. Durante il layout di `AbsoluteLayout` possibile ottenere tale valore chiamando [ `AbsoluteLayout.GetLayoutBounds` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.GetLayoutBounds/p/Xamarin.Forms.BindableObject/), che viene implementato con un `GetValue` chiamare.

## <a name="proportional-sizing-and-positioning"></a>Ridimensionamento proporzionale e posizionamento

`AbsoluteLayout` implementa un ridimensionamento proporzionale e funzionalità di posizionamento. La classe definisce una proprietà associabile secondo associata, [ `LayoutFlagsProperty` ](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty/), con i metodi statici correlati [ `AbsoluteLayout.SetLayoutFlags` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.SetLayoutFlags/p/Xamarin.Forms.BindableObject/Xamarin.Forms.AbsoluteLayoutFlags/) e [ `AbsoluteLayout.GetLayoutFlags` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AbsoluteLayout.GetLayoutFlags/p/Xamarin.Forms.BindableObject/).

L'argomento `AbsoluteLayout.SetLayoutFlags` e il valore restituito di `AbsoluteLayout.GetLayoutFlags` è un valore di tipo [ `AbsoluteLayoutFlags` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayoutFlags/), un'enumerazione con i membri seguenti:

- [`None`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.None/) (uguale a 0)
- [`XProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.XProportional/) (1)
- [`YProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.YProportional/) (2)
- [`PositionProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional/) (3)
- [`WidthProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional/) (4)
- [`HeightProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional/) (8)
- [`SizeProportional`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional/) (12)
- [`All`](https://developer.xamarin.com/api/field/Xamarin.Forms.AbsoluteLayoutFlags.All/) (\xFFFFFFFF)

È possibile combinare queste con il linguaggio c# operatore OR.

Con questi set di flag, determinate proprietà del `Rectangle` struttura limiti di layout utilizzato per posizionare e ridimensionare l'elemento figlio vengono interpretati in modo proporzionale.

Quando il `WidthProportional` flag è impostato, un `Width` valore 1 indica che l'elemento figlio è la stessa larghezza il `AbsoluteLayout`. Un approccio simile viene utilizzato per l'altezza.

Il posizionamento proporzionale considerando le dimensioni. Quando il `XProportional` flag è impostato, il `X` proprietà del `Rectangle` dei limiti del layout è proporzionale. Un valore pari a 0 indica che l'elemento figlio a sinistra del bordo è posizionato in corrispondenza del bordo sinistro di `AbsoluteLayout`, ma una posizione pari a 1 indica che bordo destro del figlio è posizionato sul bordo destro del `AbsoluteLayout`, non oltre il bordo destro del `AbsoluteLayout` come si farebbe expec t. Un `X` proprietà pari a 0,5 Centra l'elemento figlio in orizzontale il `AbsoluteLayout`.

Il [ **ChessboardProportional** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) esempio illustra l'uso di ridimensionamento proporzionale e il posizionamento.

## <a name="working-with-proportional-coordinates"></a>Utilizzo di coordinate proporzionale

In alcuni casi, risulta più semplice considerare il posizionamento proporzionali in modo diverso da come viene implementato nel `AbsoluteLayout`. È preferibile utilizzare proporzionale coordinate in cui un `X` proprietà 1 le posizioni bordo sinistro del bambino (anziché il bordo destro) contro il bordo destro del `AbsoluteLayout`.

Questo schema di posizionamento alternativo può essere chiamato "figlio frazionari coordinate". È possibile convertire da coordinate figlio frazionari i limiti di layout necessarie per `AbsoluteLayout` utilizzando le formule seguenti:

layoutBounds.X = (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y = (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

Il [ **ProportionalCoordinateCalc** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) esempio viene illustrata questa.

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout e XAML

È possibile utilizzare un `AbsoluteLayout` in XAML e impostare le proprietà associabili associate per gli elementi figlio di un `AbsoluteLayout` utilizzando i valori degli attributi `AbsoluteLayout.LayoutBounds` e `AbsoluteLayout.LayoutFlags`. Questa funzionalità viene illustrata la [ **AbsoluteXamlDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) e [ **ChessboardXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) esempi. Il programma di quest'ultimo contiene 32 `BoxView` elementi, ma Usa implicita `Style` che include il `AbsoluteLayout.LayoutFlags` proprietà per mantenere il markup fino a un minimo.

È un attributo in XAML che include un nome di classe, un punto e un nome di proprietà *sempre* una proprietà associabile associata.

## <a name="overlays"></a>Sovrapposizioni

È possibile utilizzare `AbsoluteLayout` per costruire un *sovrapposizione*, che copre la con altri controlli, ad esempio per impedire che l'utente interagisce con i normali controlli nella pagina.

Il [ **SimpleOverlay** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) esempio viene illustrata questa tecnica e viene inoltre illustrato il [ `ProgressBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/), che consente di visualizzare l'entità a cui un programma è stata completata una attività.

## <a name="some-fun"></a>Alcuni fun

Il [ **DotMatrixClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) esempio consente di visualizzare l'ora corrente con un display simulato ad aghi 5 / 7. Ogni punto rappresenta un `BoxView` (esistono 228 di essi) ridimensionato e posizionato in corrispondenza di `AbsoluteLayout`.

[![Tripla schermata dell'orologio aghi](images/ch14fg08-small.png "aghi Clock")](images/ch14fg08-large.png#lightbox "aghi Clock")

Il [ **BouncingText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) programma aggiunge un'animazione due `Label` oggetti bounce orizzontalmente e verticalmente sullo schermo.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 14 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Esempi di capitolo 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
