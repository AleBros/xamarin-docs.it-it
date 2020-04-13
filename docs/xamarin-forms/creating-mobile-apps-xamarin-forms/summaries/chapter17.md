---
title: Riassunto del capitolo 17. Il controllo della griglia
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 17. Il controllo della griglia'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 37b5e2bbafa816de27390771ae6daa33c74f7651
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760632"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Riassunto del capitolo 17. Il controllo della griglia

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

Il [`Grid`](xref:Xamarin.Forms.Grid) è un potente meccanismo di layout che dispone i suoi elementi figlio in righe e colonne di celle. A differenza `table` dell'elemento `Grid` HTML simile, il è esclusivamente per scopi di layout piuttosto che di presentazione.

## <a name="the-basic-grid"></a>La griglia di base

`Grid`deriva da [`Layout<View>`](xref:Xamarin.Forms.Layout`1), che [`Children`](xref:Xamarin.Forms.Layout`1.Children) definisce `Grid` una proprietà che eredita. Puoi compilare questa raccolta in XAML o codice.

### <a name="the-grid-in-xaml"></a>Griglia in XAMLThe Grid in XAML

La definizione `Grid` di un in [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) XAML [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) inizia `Grid` in [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) genere con il riempimento delle raccolte e degli oggetti with e . In questo modo si stabilisce il `Grid`numero di righe e colonne di , e delle relative proprietà.

`RowDefinition`ha [`Height`](xref:Xamarin.Forms.RowDefinition.Height) una `ColumnDefinition` proprietà [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) e ha una [`GridLength`](xref:Xamarin.Forms.GridLength)proprietà, entrambi di tipo , una struttura.

In XAML, [`GridLengthTypeConverter`](xref:Xamarin.Forms.GridLengthTypeConverter) converte semplici `GridLength` stringhe di testo in valori. Dietro le quinte, il `GridLength` [ `GridLength` costruttore](xref:Xamarin.Forms.GridLength.%23ctor(System.Double,Xamarin.Forms.GridUnitType)) crea il valore [`GridUnitType`](xref:Xamarin.Forms.GridUnitType)in base a un numero e un valore di tipo , un'enumerazione con tre membri:

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute)&mdash; la larghezza o l'altezza è specificata in unità indipendenti dal dispositivo (un numero in XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto)&mdash; l'altezza o la larghezza viene ridimensionata automaticamente in base al contenuto della cella ("Auto" in XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star)&mdash; l'altezza o la larghezza rimanenti\*vengono allocate proporzionalmente (un numero con " ", denominato *star*, in XAML)

A ogni `Grid` elemento figlio dell'oggetto deve essere inoltre assegnata una riga e una colonna (in modo esplicito o implicito). Gli intervalli di righe e gli intervalli di colonne sono facoltativi. Questi vengono tutti specificati utilizzando &mdash; le proprietà associabili `Grid` associate definite dall'oggetto ma impostato sugli elementi figlio dell'oggetto `Grid`. `Grid`definisce quattro proprietà associabili associate statiche:

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty)&mdash; la riga in base zero; il valore predefinito è 0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty)&mdash; la colonna in base zero; il valore predefinito è 0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty)&mdash; il numero di righe su cui si estende il figlio; il valore predefinito è 1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty)&mdash; il numero di colonne occupate dall'elemento figlio; il valore predefinito è 1

Nel codice, un programma può utilizzare otto metodi statici per impostare e ottenere questi valori:In code, a program can use eight static methods to set and get these values:

- [`Grid.SetRow`](xref:Xamarin.Forms.Grid.SetRow(Xamarin.Forms.BindableObject,System.Int32))E[`Grid.GetRow`](xref:Xamarin.Forms.Grid.GetRow(Xamarin.Forms.BindableObject))
- [`Grid.SetColumn`](xref:Xamarin.Forms.Grid.SetColumn(Xamarin.Forms.BindableObject,System.Int32))E[`Grid.GetColumn`](xref:Xamarin.Forms.Grid.GetColumn(Xamarin.Forms.BindableObject))
- [`Grid.SetRowSpan`](xref:Xamarin.Forms.Grid.SetRowSpan(Xamarin.Forms.BindableObject,System.Int32))E[`Grid.GetRowSpan`](xref:Xamarin.Forms.Grid.GetRowSpan(Xamarin.Forms.BindableObject))
- [`Grid.SetColumnSpan`](xref:Xamarin.Forms.Grid.SetColumnSpan(Xamarin.Forms.BindableObject,System.Int32))E[`Grid.GetColumnSpan`](xref:Xamarin.Forms.Grid.GetColumnSpan(Xamarin.Forms.BindableObject))

In XAML si usano gli attributi seguenti per l'impostazione di questi valori:

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

Nell'esempio [**SimpleGridDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) viene illustrata la creazione e l'inizializzazione di un `Grid` oggetto in XAML.

Eredita `Grid` la [`Padding`](xref:Xamarin.Forms.Layout.Padding) proprietà `Layout` da e definisce due proprietà aggiuntive che forniscono la spaziatura tra le righe e le colonne:

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing)ha un valore predefinito di 6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing)ha un valore predefinito di 6

Le `RowDefinitions` `ColumnDefinitions` collezioni e non sono strettamente necessarie. Se assente, `Grid` il crea `Grid` righe e colonne per `GridLength` gli\*elementi figlio e dà a tutti un valore predefinito di " " (stella).

### <a name="the-grid-in-code"></a>La griglia nel codiceThe Grid in code

[**Nell'esempio GridCodeDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) viene illustrato `Grid` come creare e popolare un nel codice. È possibile impostare le proprietà associate per ogni elemento `Add` figlio [`Add`](xref:Xamarin.Forms.Grid.IGridList`1.Add*) direttamente o indirettamente chiamando metodi aggiuntivi, ad esempio definiti dal [Grid.IGridList<T> ](xref:Xamarin.Forms.Grid.IGridList`1) interfaccia.

### <a name="the-grid-bar-chart"></a>Il grafico a barre della griglia

Il [**GridBarChart**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) esempio viene `BoxView` illustrato come `Grid` aggiungere [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) più elementi a un utilizzando il metodo bulk. Per impostazione `BoxView` predefinita, questi elementi hanno larghezza uguale. L'altezza `BoxView` di ciascuno può quindi essere controllata per assomigliare a un grafico a barre.

Nell'esempio `Grid` **GridBarChart** condivide `AbsoluteLayout` un elemento padre `Frame`con un oggetto inizialmente invisibile. Il programma imposta `TapGestureRecognizer` anche `BoxView` un `Frame` su ciascuno per utilizzare il per visualizzare le informazioni sulla barra di sbarramento.

### <a name="alignment-in-the-grid"></a>Allineamento nella griglia

[**Nell'esempio GridAlignment**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) viene `VerticalOptions` illustrato `HorizontalOptions` come utilizzare le `Grid` proprietà e per allineare gli elementi figlio in una cella.

Il [**SpacingButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) esempio `Button` spazi equi degli elementi centrati nelle `Grid` celle.

### <a name="cell-dividers-and-borders"></a>Divisori cellulari e bordi

Il `Grid` non include una funzione che disegna divisori di cella o bordi. Tuttavia, si può fare il proprio.

Il [**GridCellDividers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) viene illustrato come definire righe `BoxView` e colonne aggiuntive in modo specifico per gli elementi sottili per simulare le linee di divisione.

Il programma [**GridCellBorders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) non crea altre celle, ma allinea gli `BoxView` elementi in ogni cella per simulare il bordo di una cella.

## <a name="almost-real-life-grid-examples"></a>Esempi di Griglia quasi realistica

L'esempio [**KeypadGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) utilizza un `Grid` per visualizzare una tastiera:

[![Tripla schermata della griglia della tastiera](images/ch17fg12-small.png "Griglia del tastierino")](images/ch17fg12-large.png#lightbox "Griglia del tastierino")

### <a name="responding-to-orientation-changes"></a>Risposta alle modifiche di orientamento

Il `Grid` può aiutare a strutturare un programma per rispondere alle modifiche di orientamento. [**Nell'esempio GridRgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) viene illustrata una tecnica che sposta un elemento tra una seconda riga di un telefono orientato all'orientamento verticale e la seconda colonna di un telefono orientato all'orientamento orizzontale.

Il programma `Slider` inizializza gli elementi su un intervallo compreso tra 0 e 255 e utilizza associazioni dati per visualizzare il valore dei cursori in formato esadecimale. Poiché `Slider` i valori sono a virgola mobile e la stringa di formattazione [`DoubleToIntConvert`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) .NET per esadecimale funziona solo con numeri interi, una classe nella libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) risulta.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 17 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Capitolo 17 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Griglia](~/xamarin-forms/user-interface/layouts/grid.md)
