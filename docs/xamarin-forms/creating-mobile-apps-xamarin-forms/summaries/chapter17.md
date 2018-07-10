---
title: Riepilogo del capitolo 17. Il controllo della griglia
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 17. Il controllo della griglia'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: b71859d0848d7bf790b3cc4beddc67a5ea86d340
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935478"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Riepilogo del capitolo 17. Il controllo della griglia

Il [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) è un meccanismo potente layout che dispone i relativi elementi figlio in righe e colonne di celle. A differenza di HTML simile `table` elemento, il `Grid` è esclusivamente per scopi di layout invece di presentazione.

## <a name="the-basic-grid"></a>Griglia di base

`Grid` deriva da [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/), che definisce una [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) proprietà che `Grid` eredita. È possibile compilare questa raccolta in XAML o codice.

### <a name="the-grid-in-xaml"></a>La griglia in XAML

La definizione di un `Grid` in XAML inizia generalmente con riempimento il [ `RowDefinitions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.RowDefinitions/) e [ `ColumnDefinitions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.ColumnDefinitions/) raccolte del `Grid` con [ `RowDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RowDefinition/) e [ `ColumnDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColumnDefinition/) oggetti. Si tratta di come si stabilisce il numero di righe e colonne di `Grid`e le relative proprietà.

`RowDefinition` è un [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.RowDefinition.Height/) proprietà e `ColumnDefinition` dispone di un [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ColumnDefinition.Width/) proprietà, entrambi di tipo [ `GridLength` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridLength/), una struttura.

In XAML, il [ `GridLengthTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridLengthTypeConverter/) converte le stringhe di testo semplice in `GridLength` valori. Dietro le quinte, il [ `GridLength` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.GridLength.GridLength/p/System.Double/Xamarin.Forms.GridUnitType/) consente di creare le `GridLength` valore basato su un numero e un valore di tipo [ `GridUnitType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridUnitType/), un'enumerazione con tre membri:

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute) &mdash; la larghezza o altezza viene specificato in unità indipendenti dal dispositivo (un numero in XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto) &mdash; l'altezza o la larghezza viene ridimensionato automaticamente in base al contenuto delle celle ("Auto" in XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star) &mdash; rimasto altezza o larghezza allocata in modo proporzionale (un numero con "\*", denominato *star*, in XAML)

Ogni figlio le `Grid` deve anche essere assegnata una riga e colonna (in modo esplicito o implicito). Si estende su riga e colonna intervalli sono facoltativi. Questi vengono tutte specificati utilizzando le proprietà associabili associate &mdash; le proprietà definite dal `Grid` ma impostare nell'elemento figlio del `Grid`. `Grid` definisce quattro proprietà associabili associate statica:

- [`RowProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowProperty/) &mdash; la riga in base zero. il valore predefinito è 0
- [`ColumnProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnProperty/) &mdash; la colonna in base zero. il valore predefinito è 0
- [`RowSpanProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowSpanProperty/) &mdash; il numero di righe si estende l'elemento figlio; valore predefinito è 1
- [`ColumnSpanProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnSpanProperty/) &mdash; il numero di colonne si estende l'elemento figlio; valore predefinito è 1

Nel codice, un programma può usare otto metodi statici per impostare e ottenere questi valori:

- [`Grid.SetRow`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetRow/p/Xamarin.Forms.BindableObject/System.Int32/) e [`Grid.GetRow`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetRow/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetColumn`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetColumn/p/Xamarin.Forms.BindableObject/System.Int32/) e [`Grid.GetColumn`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetColumn/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetRowSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetRowSpan/p/Xamarin.Forms.BindableObject/System.Int32/) e [`Grid.GetRowSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetRowSpan/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetColumnSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetColumnSpan/p/Xamarin.Forms.BindableObject/System.Int32/) e [`Grid.GetColumnSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetColumnSpan/p/Xamarin.Forms.BindableObject/)

In XAML è usare gli attributi seguenti per impostare questi valori:

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

Il [ **SimpleGridDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) esempio illustra la creazione e inizializzazione di un `Grid` in XAML.

Il `Grid` eredita le [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) proprietà `Layout` e definisce due proprietà aggiuntive che forniscono la spaziatura tra le righe e colonne:

- [`RowSpacing`](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.RowSpacing/) ha un valore predefinito pari a 6
- [`ColumnSpacing`](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.ColumnSpacing/) ha un valore predefinito pari a 6

Il `RowDefinitions` e `ColumnDefinitions` raccolte non sono strettamente necessarie. Se assente, il `Grid` consente di creare righe e colonne per il `Grid` figli e fornisce a ognuna tutti predefinito `GridLength` di "\*" (star).

### <a name="the-grid-in-code"></a>La griglia nel codice

Il [ **GridCodeDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) esempio viene illustrato come creare e popolare un `Grid` nel codice. È possibile impostare le proprietà associate per ogni elemento figlio direttamente o indirettamente tramite una chiamata aggiuntiva `Add` metodi, ad esempio [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/System.Int32/System.Int32/) definito per il [Grid.IGridList<T> ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid+IGridList%3CT%3E/) interfaccia.

### <a name="the-grid-bar-chart"></a>Il grafico a barre della griglia

Il [ **GridBarChart** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) esempio viene illustrato come aggiungere più `BoxView` elementi di un `Grid` utilizzando la maggior parte [ `AddHorizontal` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.AddHorizontal/p/System.Collections.Generic.IEnumerable%7BXamarin.Forms.View%7D/) (metodo). Per impostazione predefinita, questi `BoxView` elementi hanno la stessa. L'altezza della ognuno `BoxView` può quindi essere controllato per essere simile a un grafico a barre.

Il `Grid` nella **GridBarChart** condivisioni di esempio un' `AbsoluteLayout` padre con un inizialmente invisibile `Frame`. Il programma imposta anche un `TapGestureRecognizer` in ogni `BoxView` usare la `Frame` per visualizzare le informazioni sulla barra degli strumenti scelte.

### <a name="alignment-in-the-grid"></a>Nella griglia di allineamento

Il [ **GridAlignment** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) esempio viene illustrato come usare il `VerticalOptions` e `HorizontalOptions` le proprietà per allineare gli elementi figlio in un `Grid` cella.

Il [ **SpacingButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) esempio ugualmente spazi `Button` elementi centrato nella `Grid` celle.

### <a name="cell-dividers-and-borders"></a>Bordi e i separatori di celle

Il `Grid` non include una funzionalità che consente di disegnare i separatori di celle o bordi. Tuttavia, è possibile rendere il proprio.

Il [ **GridCellDividers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) viene illustrato come definire altre righe e colonne in modo specifico per thin `BoxView` elementi per simulare linee di demarcazione.

Il [ **GridCellBorders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) programma non crea alcuna cella aggiuntive, ma invece Allinea `BoxView` elementi in ogni cella per simulare un bordo della cella.

## <a name="almost-real-life-grid-examples"></a>Esempi di griglia di quasi reali

Il [ **KeypadGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) esempio viene usato un `Grid` per visualizzare un tastierino:

[![Schermata triplo della griglia tastierino](images/ch17fg12-small.png "griglia tastierino")](images/ch17fg12-large.png#lightbox "tastierino numerico griglia")

### <a name="responding-to-orientation-changes"></a>Rispondere alle modifiche apportate orientamento

Il `Grid` consentono di strutturare un programma per rispondere alle modifiche apportate orientamento. Il [ **GridRgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) esempio viene illustrata una tecnica che consente di spostare un elemento tra una seconda riga di un telefono e orientati alle verticale e la seconda colonna di un telefono con orientamento orizzontale.

Inizializza il programma `Slider` elementi a un intervallo di 0 e 255 e Usa le associazioni dati per visualizzare il valore di scorrimento in formato esadecimale. Perché il `Slider` i valori sono a virgola mobile e .NET formattazione stringa da esadecimale funziona solo con numeri interi, un [ `DoubleToIntConvert` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria aiuta.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 17 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Esempi di capitolo 17](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Griglia](~/xamarin-forms/user-interface/layouts/grid.md)
