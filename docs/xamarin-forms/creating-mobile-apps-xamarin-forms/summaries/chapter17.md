---
title: Riepilogo del capitolo 17. Il controllo della griglia
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 17. Il controllo della griglia'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 37b5e2bbafa816de27390771ae6daa33c74f7651
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760632"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Riepilogo del capitolo 17. Il controllo della griglia

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

Il [`Grid`](xref:Xamarin.Forms.Grid) è un meccanismo di layout potente che dispone gli elementi figlio in righe e colonne di celle. A differenza dell'elemento HTML `table` simile, il `Grid` è esclusivamente a scopo di layout anziché presentazione.

## <a name="the-basic-grid"></a>Griglia di base

`Grid` deriva da [`Layout<View>`](xref:Xamarin.Forms.Layout`1), che definisce una proprietà [`Children`](xref:Xamarin.Forms.Layout`1.Children) che `Grid` eredita. È possibile compilare questa raccolta in XAML o codice.

### <a name="the-grid-in-xaml"></a>La griglia in XAML

La definizione di un `Grid` in XAML inizia in genere con il riempimento delle raccolte [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) e [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) della `Grid` con [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) e [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) oggetti. Questo è il modo in cui viene stabilito il numero di righe e colonne del `Grid`e le relative proprietà.

`RowDefinition` dispone di una proprietà [`Height`](xref:Xamarin.Forms.RowDefinition.Height) e `ColumnDefinition` ha una proprietà [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) , entrambe di tipo [`GridLength`](xref:Xamarin.Forms.GridLength), una struttura.

In XAML, il [`GridLengthTypeConverter`](xref:Xamarin.Forms.GridLengthTypeConverter) converte le stringhe di testo semplici in valori `GridLength`. Dietro le quinte, il [costruttore`GridLength`](xref:Xamarin.Forms.GridLength.%23ctor(System.Double,Xamarin.Forms.GridUnitType)) crea il valore `GridLength` in base a un numero e a un valore di tipo [`GridUnitType`](xref:Xamarin.Forms.GridUnitType), un'enumerazione con tre membri:

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute) &mdash; la larghezza o l'altezza è specificata in unità indipendenti dal dispositivo (un numero in XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto) &mdash; l'altezza o la larghezza viene ridimensionata automaticamente in base al contenuto delle celle ("auto" in XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star) &mdash; altezza o larghezza residua allocata proporzionalmente (un numero con "\*", denominato *Star*, in XAML)

A ogni figlio del `Grid` devono essere assegnate anche una riga e una colonna (in modo esplicito o implicito). Si estende su riga e colonna intervalli sono facoltativi. Tutti questi elementi vengono specificati usando proprietà associabili associate &mdash; proprietà definite dall'`Grid` ma impostate sui figli della `Grid`. `Grid` definisce quattro proprietà associabili associate statiche:

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty) &mdash; riga in base zero; il valore predefinito è 0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty) &mdash; colonna in base zero; il valore predefinito è 0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty) &mdash; il numero di righe in cui si estende l'elemento figlio; il valore predefinito è 1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) &mdash; il numero di colonne su cui si estende l'elemento figlio; il valore predefinito è 1

Nel codice, un programma può usare otto metodi statici per impostare e ottenere questi valori:

- [`Grid.SetRow`](xref:Xamarin.Forms.Grid.SetRow(Xamarin.Forms.BindableObject,System.Int32)) e [`Grid.GetRow`](xref:Xamarin.Forms.Grid.GetRow(Xamarin.Forms.BindableObject))
- [`Grid.SetColumn`](xref:Xamarin.Forms.Grid.SetColumn(Xamarin.Forms.BindableObject,System.Int32)) e [`Grid.GetColumn`](xref:Xamarin.Forms.Grid.GetColumn(Xamarin.Forms.BindableObject))
- [`Grid.SetRowSpan`](xref:Xamarin.Forms.Grid.SetRowSpan(Xamarin.Forms.BindableObject,System.Int32)) e [`Grid.GetRowSpan`](xref:Xamarin.Forms.Grid.GetRowSpan(Xamarin.Forms.BindableObject))
- [`Grid.SetColumnSpan`](xref:Xamarin.Forms.Grid.SetColumnSpan(Xamarin.Forms.BindableObject,System.Int32)) e [`Grid.GetColumnSpan`](xref:Xamarin.Forms.Grid.GetColumnSpan(Xamarin.Forms.BindableObject))

In XAML è usare gli attributi seguenti per impostare questi valori:

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

L'esempio [**SimpleGridDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) illustra la creazione e l'inizializzazione di un `Grid` in XAML.

Il `Grid` eredita la proprietà [`Padding`](xref:Xamarin.Forms.Layout.Padding) da `Layout` e definisce due proprietà aggiuntive che forniscono spaziatura tra le righe e le colonne:

- il valore predefinito di [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) è 6
- il valore predefinito di [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) è 6

Le raccolte `RowDefinitions` e `ColumnDefinitions` non sono strettamente obbligatorie. Se assente, il `Grid` crea righe e colonne per gli elementi figlio `Grid` e fornisce tutti i `GridLength` predefiniti di "\*" (Star).

### <a name="the-grid-in-code"></a>La griglia nel codice

Nell'esempio [**GridCodeDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) viene illustrato come creare e popolare un `Grid` nel codice. È possibile impostare le proprietà associate per ogni elemento figlio direttamente o indirettamente chiamando metodi di `Add` aggiuntivi, ad esempio [`Add`](xref:Xamarin.Forms.Grid.IGridList`1.Add*) definito dall'interfaccia [Grid. IGridList<T>](xref:Xamarin.Forms.Grid.IGridList`1) .

### <a name="the-grid-bar-chart"></a>Il grafico a barre della griglia

Nell'esempio [**GridBarChart**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) viene illustrato come aggiungere più elementi `BoxView` a un `Grid` utilizzando il metodo bulk [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) . Per impostazione predefinita, questi elementi `BoxView` hanno la stessa larghezza. L'altezza di ogni `BoxView` può quindi essere controllata per essere simile a un grafico a barre.

Il `Grid` nell'esempio **GridBarChart** condivide un elemento padre `AbsoluteLayout` con una `Frame`inizialmente invisibile. Il programma imposta anche un `TapGestureRecognizer` in ogni `BoxView` per usare il `Frame` per visualizzare informazioni sulla barra toccata.

### <a name="alignment-in-the-grid"></a>Nella griglia di allineamento

Nell'esempio [**GridAlignment**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) viene illustrato come utilizzare le proprietà `VerticalOptions` e `HorizontalOptions` per allineare gli elementi figlio in una cella di `Grid`.

L'esempio [**SpacingButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) viene ugualmente spaziato `Button` elementi centrati in `Grid` celle.

### <a name="cell-dividers-and-borders"></a>Bordi e i separatori di celle

In `Grid` non è inclusa una funzionalità che consente di disegnare i divisori o i bordi delle celle. Tuttavia, è possibile rendere il proprio.

[**GridCellDividers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) illustra come definire righe e colonne aggiuntive in modo specifico per gli elementi Thin `BoxView` per simulare le linee di divisione.

Il programma [**GridCellBorders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) non crea altre celle, ma allinea `BoxView` elementi in ogni cella per simulare il bordo di una cella.

## <a name="almost-real-life-grid-examples"></a>Esempi di griglia di quasi reali

L'esempio [**KeypadGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) usa un `Grid` per visualizzare una tastiera:

[![Schermata tripla della griglia del tastierino](images/ch17fg12-small.png "Griglia tastiera")](images/ch17fg12-large.png#lightbox "Griglia tastiera")

### <a name="responding-to-orientation-changes"></a>Rispondere alle modifiche apportate orientamento

Il `Grid` può aiutare a strutturare un programma per rispondere alle modifiche dell'orientamento. L'esempio [**GridRgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) illustra una tecnica che sposta un elemento tra una seconda riga di un telefono orientato al ritratto e la seconda colonna di un telefono orientato al paesaggio.

Il programma Inizializza `Slider` gli elementi in un intervallo compreso tra 0 e 255 e usa i data binding per visualizzare il valore dei dispositivi di scorrimento in formato esadecimale. Poiché i valori di `Slider` sono a virgola mobile e la stringa di formattazione .NET per l'esadecimale funziona solo con numeri interi, una classe [`DoubleToIntConvert`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) risulta utile.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 17 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Esempi del capitolo 17](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Griglia](~/xamarin-forms/user-interface/layouts/grid.md)
