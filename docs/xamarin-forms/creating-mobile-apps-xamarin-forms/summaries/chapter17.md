---
title: "Riepilogo del capitolo 17. Master della griglia "Description:" creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 17. Mastering the Grid "ms. prod: Novell MS. Technology: Novell-Forms ms. AssetID: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1 Author: davidbritch ms. Author: dabritch ms. Date: 11/07/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="summary-of-chapter-17-mastering-the-grid"></a>Riepilogo del capitolo 17. Il controllo della griglia

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

[`Grid`](xref:Xamarin.Forms.Grid)È un meccanismo di layout potente che dispone gli elementi figlio in righe e colonne di celle. A differenza dell'elemento HTML simile `table` , l'oggetto `Grid` è esclusivamente a scopo di layout anziché presentazione.

## <a name="the-basic-grid"></a>Griglia di base

`Grid`deriva da [`Layout<View>`](xref:Xamarin.Forms.Layout`1) , che definisce una [`Children`](xref:Xamarin.Forms.Layout`1.Children) proprietà che `Grid` eredita. È possibile compilare questa raccolta in XAML o nel codice.

### <a name="the-grid-in-xaml"></a>Griglia in XAML

La definizione di un oggetto `Grid` in XAML inizia in genere con il riempimento delle [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) raccolte e di `Grid` con [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) gli oggetti e. Questo è il modo in cui viene stabilito il numero di righe e colonne di `Grid` e le relative proprietà.

`RowDefinition`dispone di una proprietà [`Height`](xref:Xamarin.Forms.RowDefinition.Height) e dispone di una `ColumnDefinition` [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) proprietà, entrambe di tipo [`GridLength`](xref:Xamarin.Forms.GridLength) , una struttura.

In XAML, [`GridLengthTypeConverter`](xref:Xamarin.Forms.GridLengthTypeConverter) converte le stringhe di testo semplici `GridLength` in valori. Dietro le quinte, il [ `GridLength` Costruttore] (xrif: Xamarin.Forms . GridLength .% 23ctor (System. Double, Xamarin.Forms . GridUnitType)) crea il `GridLength` valore in base a un numero e a un valore di tipo [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) , un'enumerazione con tre membri:

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute)&mdash;la larghezza o l'altezza è specificata in unità indipendenti dal dispositivo (un numero in XAML)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto)&mdash;l'altezza o la larghezza viene ridimensionata automaticamente in base al contenuto delle celle ("auto" in XAML)
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star)&mdash;l'altezza o la larghezza rimanente viene allocata proporzionalmente (un numero con " \* ", denominato *Star*, in XAML)

A ogni figlio di `Grid` deve essere assegnata anche una riga e una colonna (in modo esplicito o implicito). Gli intervalli di righe e gli intervalli di colonne sono facoltativi. Tutti questi elementi vengono specificati usando le proprietà associate associabili &mdash; che sono definite da `Grid` ma impostate sui figli di `Grid` . `Grid`definisce quattro proprietà associabili associate statiche:

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty)&mdash;riga in base zero. il valore predefinito è 0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty)&mdash;colonna in base zero. il valore predefinito è 0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty)&mdash;numero di righe in cui si estende l'elemento figlio. il valore predefinito è 1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty)&mdash;numero di colonne in cui si estende l'elemento figlio. il valore predefinito è 1

Nel codice, un programma può usare otto metodi statici per impostare e ottenere questi valori:

- [ `Grid.SetRow` ] (xrif: Xamarin.Forms . Griglia. SetRow ( Xamarin.Forms . BindableObject, System. Int32) e [ `Grid.GetRow` ] (xrif: Xamarin.Forms . Grid. GetRow ( Xamarin.Forms . BindableObject))
- [ `Grid.SetColumn` ] (xrif: Xamarin.Forms . Grid. secolumn ( Xamarin.Forms . BindableObject, System. Int32) e [ `Grid.GetColumn` ] (xrif: Xamarin.Forms . Grid. GetColumn ( Xamarin.Forms . BindableObject))
- [ `Grid.SetRowSpan` ] (xrif: Xamarin.Forms . Grid. SetRowSpan ( Xamarin.Forms . BindableObject, System. Int32) e [ `Grid.GetRowSpan` ] (xrif: Xamarin.Forms . Grid. GetRowSpan ( Xamarin.Forms . BindableObject))
- [ `Grid.SetColumnSpan` ] (xrif: Xamarin.Forms . Grid. SetColumnSpan ( Xamarin.Forms . BindableObject, System. Int32) e [ `Grid.GetColumnSpan` ] (xrif: Xamarin.Forms . Grid. GetColumnSpan ( Xamarin.Forms . BindableObject))

In XAML usare gli attributi seguenti per l'impostazione di questi valori:

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

L'esempio [**SimpleGridDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo) illustra la creazione e l'inizializzazione di un oggetto `Grid` in XAML.

`Grid`Eredita la [`Padding`](xref:Xamarin.Forms.Layout.Padding) proprietà da `Layout` e definisce due proprietà aggiuntive che forniscono spaziatura tra le righe e le colonne:

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing)il valore predefinito è 6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing)il valore predefinito è 6

Le `RowDefinitions` `ColumnDefinitions` raccolte e non sono strettamente obbligatorie. Se assente, `Grid` Crea righe e colonne per gli `Grid` elementi figlio e assegna a tutti il valore predefinito `GridLength` " \* " (asterisco).

### <a name="the-grid-in-code"></a>Griglia nel codice

Nell'esempio [**GridCodeDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo) viene illustrato come creare e popolare un oggetto `Grid` nel codice. È possibile impostare le proprietà associate per ogni elemento figlio direttamente o indirettamente chiamando metodi aggiuntivi `Add` come [`Add`](xref:Xamarin.Forms.Grid.IGridList`1.Add*) definito dall'interfaccia [Grid. IGridList <T> ](xref:Xamarin.Forms.Grid.IGridList`1) .

### <a name="the-grid-bar-chart"></a>Grafico a barre della griglia

Nell'esempio [**GridBarChart**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart) viene illustrato come aggiungere più `BoxView` elementi a un oggetto `Grid` utilizzando il [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) Metodo bulk. Per impostazione predefinita, questi `BoxView` elementi hanno la stessa larghezza. L'altezza di ogni `BoxView` può quindi essere controllata per assomigliare a un grafico a barre.

Il `Grid` nell'esempio **GridBarChart** condivide un `AbsoluteLayout` elemento padre con un oggetto inizialmente invisibile `Frame` . Il programma imposta anche un `TapGestureRecognizer` su ogni `BoxView` per usare `Frame` per visualizzare le informazioni sulla barra toccata.

### <a name="alignment-in-the-grid"></a>Allineamento nella griglia

Nell'esempio [**GridAlignment**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment) viene illustrato come utilizzare le `VerticalOptions` `HorizontalOptions` proprietà e per allineare gli elementi figlio in una `Grid` cella.

Nell'esempio [**SpacingButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons) gli elementi vengono ugualmente spazi `Button` al centro delle `Grid` celle.

### <a name="cell-dividers-and-borders"></a>Separatori di celle e bordi

In `Grid` non è inclusa una funzionalità che consente di disegnare i separatori o i bordi delle celle. Tuttavia, è possibile crearne di personalizzati.

[**GridCellDividers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers) illustra come definire righe e colonne aggiuntive in modo specifico per `BoxView` gli elementi sottili per simulare le linee di divisione.

Il programma [**GridCellBorders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders) non crea altre celle, ma allinea `BoxView` gli elementi di ogni cella per simulare il bordo di una cella.

## <a name="almost-real-life-grid-examples"></a>Esempi di griglie praticamente reali

L'esempio [**KeypadGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid) USA `Grid` per visualizzare una tastiera:

[![Schermata tripla della griglia del tastierino](images/ch17fg12-small.png "Griglia tastiera")](images/ch17fg12-large.png#lightbox "Griglia tastiera")

### <a name="responding-to-orientation-changes"></a>Risposta alle modifiche dell'orientamento

`Grid`Può aiutare a strutturare un programma per rispondere alle modifiche dell'orientamento. L'esempio [**GridRgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders) illustra una tecnica che sposta un elemento tra una seconda riga di un telefono orientato al ritratto e la seconda colonna di un telefono orientato al paesaggio.

Il programma inizializza gli `Slider` elementi in un intervallo compreso tra 0 e 255 e usa le associazioni dati per visualizzare il valore dei dispositivi di scorrimento in formato esadecimale. Poiché i `Slider` valori sono a virgola mobile e la stringa di formattazione .NET per l'esadecimale funziona solo con numeri interi, una [`DoubleToIntConvert`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) risulta utile.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 17 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [Esempi del capitolo 17](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Grid](~/xamarin-forms/user-interface/layouts/grid.md)
