---
title: Riassunto del capitolo 14. Layout assoluto
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 14. Layout assoluto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 88882A48-3226-42D1-96ED-241250B64A84
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c489bf244396cf180ed8e1272308048a14b67300
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771137"
---
# <a name="summary-of-chapter-14-absolute-layout"></a>Riassunto del capitolo 14. Layout assoluto

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)

Like `StackLayout` [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) , deriva `Layout<View>` da ed `Children` eredita una proprietà . `AbsoluteLayout`implementa un sistema di layout che richiede al programmatore di specificare le posizioni dei relativi elementi figlio e, facoltativamente, le dimensioni. La posizione è specificata dall'angolo superiore sinistro del figlio rispetto `AbsoluteLayout` all'angolo superiore sinistro delle unità indipendenti dal dispositivo. `AbsoluteLayout`implementa anche una funzione di posizionamento e ridimensionamento proporzionale.

`AbsoluteLayout`deve essere considerato come un sistema di layout speciale da utilizzare solo quando il programmatore può imporre una dimensione agli elementi figlio (ad esempio, `BoxView` gli elementi) o quando le dimensioni dell'elemento non influiscono sul posizionamento di altri elementi figlio. Le `HorizontalOptions` `VerticalOptions` proprietà e non hanno `AbsoluteLayout`alcun effetto sugli elementi figlio di un oggetto .

In questo capitolo viene inoltre introdotta l'importante funzionalità delle *proprietà associabili associate* che consentono di associare le proprietà definite in una classe (in questo caso `AbsoluteLayout`) a un'altra classe (un elemento figlio dell'oggetto `AbsoluteLayout`).

## <a name="absolutelayout-in-code"></a>AbsoluteLayout nel codice

È possibile aggiungere un `Children` elemento `AbsoluteLayout` figlio all'insieme `AbsoluteLayout` di un [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) utilizzando il metodo [`Rectangle`](xref:Xamarin.Forms.Rectangle)standard, [`Add`](xref:System.Collections.Generic.ICollection`1.Add*) ma fornisce anche un metodo esteso che consente di specificare un oggetto . Un [`Add`](xref:Xamarin.Forms.AbsoluteLayout.IAbsoluteList`1.Add*) altro metodo [`Point`](xref:Xamarin.Forms.Point)richiede solo un oggetto , nel qual caso l'elemento figlio non è vincolato e si ridimensiona.

È possibile `Rectangle` creare un valore con &mdash; un [costruttore](xref:Xamarin.Forms.Rectangle.%23ctor(System.Double,System.Double,System.Double,System.Double)) che richiede quattro valori i primi due che indicano la posizione dell'angolo superiore sinistro dell'elemento figlio rispetto al relativo elemento padre e i secondi due che indicano le dimensioni dell'elemento figlio. In alternativa, è possibile `Point` utilizzare [`Size`](xref:Xamarin.Forms.Size) un [costruttore](xref:Xamarin.Forms.Rectangle.%23ctor(Xamarin.Forms.Point,Xamarin.Forms.Size)) che richiede un valore e .

Questi `Add` metodi vengono illustrati in [**AbsoluteDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteDemo), che `BoxView` posiziona gli elementi utilizzando `Rectangle` i valori e un `Label` elemento utilizzando solo un `Point` valore.

Nell'esempio [**ChessboardFixed**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardFixed) `BoxView` vengono utilizzati 32 elementi per creare il motivo scacchiera. Il programma `BoxView` dà agli elementi una dimensione hardcoded di 35 unità quadrate. Il `AbsoluteLayout` ha `HorizontalOptions` `VerticalOptions` il `LayoutOptions.Center`suo e `AbsoluteLayout` impostato su , che fa sì che abbia una dimensione totale di 280 unità quadrate.

## <a name="attached-bindable-properties"></a>Proprietà associabili associate

È anche possibile impostare la posizione e, facoltativamente, `AbsoluteLayout` la dimensione di un `Children` elemento figlio [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds(Xamarin.Forms.BindableObject,Xamarin.Forms.Rectangle))di un oggetto dopo che è stato aggiunto all'insieme utilizzando il metodo statico . Il primo argomento è il figlio; il secondo `Rectangle` è un oggetto. È possibile specificare che l'elemento figlio si dimensioni orizzontalmente e/o verticalmente impostando i valori di larghezza e altezza sulla [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) costante.

Il [**ChessboardDynamic**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardDynamic) esempio `AbsoluteLayout` mette `ContentView` il `SizeChanged` in `AbsoluteLayout.SetLayoutBounds` a con un gestore di chiamare su tutti gli elementi figlio per renderli il più grande possibile.  

La proprietà associabile `AbsoluteLayout` associata che definisce è il `BindableProperty` [`AbsoluteLayout.LayoutBoundsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty)campo statico di sola lettura di tipo denominato . Il `AbsoluteLayout.SetLayoutBounds` metodo statico viene `SetValue` implementato chiamando `AbsoluteLayout.LayoutBoundsProperty`l'elemento figlio con l'oggetto . L'elemento figlio contiene un dizionario in cui vengono archiviati la proprietà associabile associata e il relativo valore. Durante il `AbsoluteLayout` layout, l'oggetto [`AbsoluteLayout.GetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutBounds(Xamarin.Forms.BindableObject))può ottenere tale `GetValue` valore chiamando , che viene implementato con una chiamata.

## <a name="proportional-sizing-and-positioning"></a>Ridimensionamento e posizionamento proporzionali

`AbsoluteLayout`implementa una funzione di ridimensionamento e posizionamento proporzionale. La classe definisce una seconda proprietà [`LayoutFlagsProperty`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty)associabile associata, , con i metodi [`AbsoluteLayout.SetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutFlags(Xamarin.Forms.BindableObject,Xamarin.Forms.AbsoluteLayoutFlags)) statici correlati e [`AbsoluteLayout.GetLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.GetLayoutFlags(Xamarin.Forms.BindableObject)).

L'argomento `AbsoluteLayout.SetLayoutFlags` e il `AbsoluteLayout.GetLayoutFlags` valore restituito di [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)è un valore di tipo , un'enumerazione con i seguenti membri:

- [`None`](xref:Xamarin.Forms.AbsoluteLayoutFlags.None)(uguale a 0)
- [`XProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.XProportional)(1)
- [`YProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.YProportional)(2)
- [`PositionProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.PositionProportional)(3)
- [`WidthProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.WidthProportional)(4)
- [`HeightProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.HeightProportional)(8)
- [`SizeProportional`](xref:Xamarin.Forms.AbsoluteLayoutFlags.SizeProportional)(12)
- [`All`](xref:Xamarin.Forms.AbsoluteLayoutFlags.All)(xFFFFFFFF)

È possibile combinarli con l'operatore OR bit per bit di C.

Con questi flag impostati, `Rectangle` alcune proprietà della struttura dei limiti di layout utilizzata per posizionare e ridimensionare l'elemento figlio vengono interpretate in modo proporzionale.

Quando `WidthProportional` il flag è `Width` impostato, il valore 1 indica che `AbsoluteLayout`l'elemento figlio ha la stessa larghezza di . Un approccio simile viene utilizzato per l'altezza.

Il posizionamento proporzionale tiene conto delle dimensioni. Quando `XProportional` il flag è `X` impostato, `Rectangle` la proprietà dei limiti del layout è proporzionale. Il valore 0 indica che il bordo sinistro del bambino è `AbsoluteLayout`posizionato sul bordo sinistro della proprietà , ma una posizione pari `AbsoluteLayout`a 1 indica che `AbsoluteLayout` il bordo destro del bambino è posizionato sul bordo destro dell'oggetto , non oltre il bordo destro del file , come ci si potrebbe aspettare. Una `X` proprietà di 0,5 centra `AbsoluteLayout`il bambino orizzontalmente nel file .

Il campione [**chessboardProportional**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardProportional) dimostra l'uso del ridimensionamento e del posizionamento proporzionali.

## <a name="working-with-proportional-coordinates"></a>Utilizzo delle coordinate proporzionali

A volte, è più facile pensare al posizionamento proporzionale in `AbsoluteLayout`modo diverso rispetto a come viene implementato nel file . È preferibile lavorare con coordinate `X` proporzionali in cui una proprietà di 1 posiziona il bordo sinistro `AbsoluteLayout`del bambino (anziché il bordo destro) rispetto al bordo destro del file .

Questo schema di posizionamento alternativo può essere chiamato "coordinate figlio frazionarie". È possibile convertire le coordinate figlio frazionarie `AbsoluteLayout` ai limiti di layout necessari per l'utilizzo delle seguenti formule:

layoutBounds.X (fractionalChildCoordinate.X / (1 - layoutBounds.Width))

layoutBounds.Y (fractionalChildCoordinate.Y / (1 - layoutBounds.Height))

Il [**ProportionalCoordinateCalc**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/PropCoordCalc) esempio viene illustrato questo.

## <a name="absolutelayout-and-xaml"></a>AbsoluteLayout e XAML

È possibile `AbsoluteLayout` utilizzare un in XAML e impostare le `AbsoluteLayout` proprietà associabili `AbsoluteLayout.LayoutBounds` `AbsoluteLayout.LayoutFlags`associate negli elementi figlio di un oggetto using valori di attributo di e . Ciò è illustrato nel [**AbsoluteXamlDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/AbsoluteXamlDemo) e [**ChessboardXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/ChessboardXaml) esempi. Quest'ultimo programma `BoxView` contiene 32 `Style` elementi, `AbsoluteLayout.LayoutFlags` ma utilizza un implicito che include la proprietà per mantenere il markup al minimo.

Un attributo in XAML costituito da un nome di classe, un punto e un nome di proprietà è *sempre* una proprietà associabile associata.

## <a name="overlays"></a>Sovrimpressioni

È possibile `AbsoluteLayout` utilizzare per costruire una *sovrapposizione*, che copre la pagina con altri controlli, ad esempio per proteggere l'utente dall'interazione con i normali controlli nella pagina.

Nell'esempio [**SimpleOverlay**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/SimpleOverlay) viene illustrata [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)questa tecnica e viene inoltre illustrato il , che consente di visualizzare la misura in cui un programma ha completato un'attività.

## <a name="some-fun"></a>Un po 'di divertimento

Nell'esempio [**DotMatrixClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/DotMatrixClock) viene visualizzata l'ora corrente con una visualizzazione a matrice di punti 5x7 simulata. Ogni punto `BoxView` è un (ci sono 228 di `AbsoluteLayout`loro) dimensioni e posizionato sul .

[![Tripla schermata dell'orologio a matrice di punti](images/ch14fg08-small.png "Orologio matrice di punti")](images/ch14fg08-large.png#lightbox "Orologio matrice di punti")

Il programma [**BouncingText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14/BouncingText) `Label` anima due oggetti per rimbalzare orizzontalmente e verticalmente sullo schermo.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 14 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch14-Apr2016.pdf)
- [Esempi del capitolo 14](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter14)
- [AbsoluteLayout](~/xamarin-forms/user-interface/layouts/absolute-layout.md)
- [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md)
