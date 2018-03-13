---
title: Riepilogo del capitolo 17. Crea una copia master della griglia
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 09f63dd418ea1fb523c028edb02c28c22bfdccd1
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>Riepilogo del capitolo 17. Crea una copia master della griglia

Il [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) è un meccanismo potente layout che dispone di figli in righe e colonne di celle. A differenza di HTML simile `table` elemento, il `Grid` è esclusivamente per scopi di layout, invece della presentazione.

## <a name="the-basic-grid"></a>La griglia di base

`Grid` deriva da [ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/), che definisce un [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) proprietà che `Grid` eredita. È possibile compilare questo insieme in XAML o nel codice.

### <a name="the-grid-in-xaml"></a>La griglia in XAML

La definizione di un `Grid` in XAML in genere inizia con il riempimento di [ `RowDefinitions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.RowDefinitions/) e [ `ColumnDefinitions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.ColumnDefinitions/) raccolte del `Grid` con [ `RowDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RowDefinition/) e [ `ColumnDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColumnDefinition/) oggetti. Questo è descritto come stabilire il numero di righe e colonne di `Grid`e le relative proprietà.

`RowDefinition` è un [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.RowDefinition.Height/) proprietà e `ColumnDefinition` ha un [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ColumnDefinition.Width/) proprietà, entrambi di tipo [ `GridLength` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridLength/), una struttura.

In XAML, il [ `GridLengthTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridLengthTypeConverter/) converte le stringhe di testo semplice in `GridLength` valori. Dietro le quinte, il [ `GridLength` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.GridLength.GridLength/p/System.Double/Xamarin.Forms.GridUnitType/) crea il `GridLength` valore basato su un numero e un valore di tipo [ `GridUnitType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridUnitType/), un'enumerazione con tre membri:

- [`Absolute`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Absolute/) & #x 2014; la larghezza o altezza viene specificato in unità indipendenti dal dispositivo (un numero in XAML)
- [`Auto`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Auto/) & #x 2014; l'altezza o larghezza viene ridimensionato automaticamente in base al contenuto delle celle ("Auto" in XAML)
- [`Star`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Star/) & #x 2014; rimasto altezza o larghezza allocata in modo proporzionale (un numero con "\*", denominato *star*, in XAML)

Ogni figlio di `Grid` deve inoltre essere assegnato una riga e colonna (in modo esplicito o implicito). Si estende su riga e colonna intervalli sono facoltativi. Tutti indicati, usando collegato proprietà associabili & #x 2014; le proprietà definite per il `Grid` ma impostate su elementi figlio del `Grid`. `Grid` definisce quattro proprietà associabile associate statica:

- [`RowProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowProperty/) & #x 2014; la riga in base zero. valore predefinito è 0
- [`ColumnProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnProperty/) & #x 2014; la colonna in base zero. valore predefinito è 0
- [`RowSpanProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowSpanProperty/) & #x 2014; il numero di righe si estende l'elemento figlio; valore predefinito è 1
- [`ColumnSpanProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnSpanProperty/) & #x 2014; il numero di colonne si estende l'elemento figlio; valore predefinito è 1

Nel codice, un programma può utilizzare otto metodi statici per impostare e ottenere i valori sono i seguenti:

- [`Grid.SetRow`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetRow/p/Xamarin.Forms.BindableObject/System.Int32/) e [`Grid.GetRow`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetRow/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetColumn`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetColumn/p/Xamarin.Forms.BindableObject/System.Int32/) e [`Grid.GetColumn`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetColumn/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetRowSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetRowSpan/p/Xamarin.Forms.BindableObject/System.Int32/) e [`Grid.GetRowSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetRowSpan/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetColumnSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetColumnSpan/p/Xamarin.Forms.BindableObject/System.Int32/) e [`Grid.GetColumnSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetColumnSpan/p/Xamarin.Forms.BindableObject/)

Per la configurazione di questi valori in XAML utilizzare gli attributi seguenti:

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

Il [ **SimpleGridDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) esempio illustra la creazione e inizializzazione di un `Grid` in XAML.

Il `Grid` eredita il [ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) proprietà `Layout` e definisce due proprietà aggiuntive che forniscono la spaziatura tra le righe e colonne:

- [`RowSpacing`](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.RowSpacing/) ha un valore predefinito di 6
- [`ColumnSpacing`](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.ColumnSpacing/) ha un valore predefinito di 6

Il `RowDefinitions` e `ColumnDefinitions` raccolte non sono strettamente necessarie. Se assente, il `Grid` crea le righe e colonne per la `Grid` gli elementi figlio vengono assegnati tutti predefinito `GridLength` di "\*" (asterisco).

### <a name="the-grid-in-code"></a>La griglia nel codice

Il [ **GridCodeDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) esempio viene illustrato come creare e popolare un `Grid` nel codice. È possibile impostare le proprietà associate per ogni elemento figlio direttamente o indirettamente tramite una chiamata aggiuntivi `Add` metodi, ad esempio [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/System.Int32/System.Int32/) definito dal [Grid.IGridList<T> ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid+IGridList%3CT%3E/) interfaccia.

### <a name="the-grid-bar-chart"></a>Il grafico a barre griglia

Il [ **GridBarChart** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) esempio viene illustrato come aggiungere più `BoxView` elementi da un `Grid` utilizzando la maggior parte [ `AddHorizontal` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.AddHorizontal/p/System.Collections.Generic.IEnumerable%7BXamarin.Forms.View%7D/) metodo. Per impostazione predefinita, questi `BoxView` elementi hanno la stessa. L'altezza di ogni `BoxView` può quindi essere controllato per sono simili a un grafico a barre.

Il `Grid` nel **GridBarChart** esempio condivisioni un `AbsoluteLayout` padre con un controllo invisibile inizialmente `Frame`. Il programma imposta anche un `TapGestureRecognizer` in ogni `BoxView` per utilizzare il `Frame` per visualizzare informazioni sulla barra degli strumenti scelte.

### <a name="alignment-in-the-grid"></a>Nella griglia di allineamento

Il [ **GridAlignment** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) esempio viene illustrato come utilizzare il `VerticalOptions` e `HorizontalOptions` proprietà per allineare gli elementi figlio in un `Grid` cella.

Il [ **SpacingButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) esempio ugualmente spazi `Button` elementi al centro `Grid` celle.

### <a name="cell-dividers-and-borders"></a>Bordi e i separatori di celle

Il `Grid` non include una funzionalità che consente di disegnare i separatori di celle o bordi. Tuttavia, è possibile rendere la propria.

Il [ **GridCellDividers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) viene illustrato come definire altre righe e colonne in modo specifico per thin `BoxView` elementi per simulare linee di divisione.

Il [ **GridCellBorders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) programma crea celle aggiuntive, ma invece Allinea `BoxView` elementi in ogni cella per simulare un bordo della cella.

## <a name="almost-real-life-grid-examples"></a>Esempi di griglia quasi reale

Il [ **KeypadGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) esempio viene utilizzata una `Grid` per visualizzare un tastierino numerico:

[![Schermata triplo della griglia tastierino](images/ch17fg12-small.png "tastierino griglia")](images/ch17fg12-large.png#lightbox "tastierino griglia")

### <a name="responding-to-orientation-changes"></a>Risposta alle modifiche di orientamento

Il `Grid` può agevolare la definizione di un programma per rispondere alle modifiche di orientamento. Il [ **GridRgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) esempio viene illustrata una tecnica che consente di spostare un elemento tra una seconda riga di un telefono con orientamento verticale e la seconda colonna di un telefono con orientamento orizzontale.

Inizializza il programma `Slider` elementi a un intervallo di 0 e 255 e utilizza le associazioni dati per visualizzare il valore di scorrimento in formato esadecimale. Poiché il `Slider` i valori sono a virgola mobile e la stringa di formattazione per esadecimale funziona solo con numeri interi, di .NET un [ `DoubleToIntConvert` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria consente.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 17 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Capitolo 17 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Griglia](~/xamarin-forms/user-interface/layouts/grid.md)
