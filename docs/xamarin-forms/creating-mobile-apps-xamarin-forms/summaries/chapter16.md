---
title: Riepilogo del capitolo 16. Associazione dati
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 16. Associazione dati'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 2d61413fb1d8c28a3957da53601d0ad682f35518
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771095"
---
# <a name="summary-of-chapter-16-data-binding"></a>Riepilogo del capitolo 16. Associazione dati

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

I programmatori spesso trovano autonomamente la scrittura di gestori di eventi che rilevano quando una proprietà di un oggetto è stato modificato e usano per modificare il valore di una proprietà in un altro oggetto. Questo processo può essere automatizzato con la tecnica di *Data Binding*. Le associazioni dati sono in genere definite in XAML e diventano parte della definizione dell'interfaccia utente.

Queste associazioni di dati molto spesso, connettono oggetti dell'interfaccia utente ai dati sottostanti. Si tratta di una tecnica più approfondita nel [**capitolo 18. MVVM**](chapter18.md). Tuttavia, i data binding può anche connettersi due o più elementi dell'interfaccia utente. La maggior parte degli esempi iniziali dell'associazione dati in questo capitolo illustra questa tecnica.

## <a name="binding-basics"></a>Nozioni di base di associazione

Diverse proprietà, metodi e classi sono coinvolti nell'associazione dati:

- La classe [`Binding`](xref:Xamarin.Forms.Binding) deriva da [`BindingBase`](xref:Xamarin.Forms.BindingBase) e incapsula molte caratteristiche di un data binding
- La proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) è definita dalla classe [`BindableObject`](xref:Xamarin.Forms.BindableObject)
- Il metodo [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) viene definito anche dalla classe [`BindableObject`](xref:Xamarin.Forms.BindableObject)
- La classe [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions) definisce tre metodi di `SetBinding` aggiuntivi

Le due classi seguenti supportano le estensioni di markup XAML per i binding:

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) supporta l'estensione di markup `Binding`
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) supporta l'estensione di markup `x:Reference`

Nel data binding, sono coinvolti due interfacce:

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) nello spazio dei nomi `System.ComponentModel` è per l'implementazione di notifiche quando una proprietà viene modificata
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) viene utilizzato per definire classi di piccole dimensioni che convertono i valori da un tipo a un altro nelle associazioni dati

Un'associazione di dati si connette due proprietà dell'oggetto stesso, o (più comunemente) due oggetti diversi. Queste due proprietà sono denominate *origine* e *destinazione*. In generale, una modifica della proprietà di origine causa una modifica avvenga in proprietà di destinazione, ma in alcuni casi la direzione viene invertita. Indipendentemente dal fatto che:

- la proprietà di *destinazione* deve essere supportata da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- in genere, la proprietà di *origine* è un membro di una classe che implementa [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

Una classe che implementa `INotifyPropertyChanged` genera un evento di [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) quando una proprietà modifica il valore. `BindableObject` implementa `INotifyPropertyChanged` e genera automaticamente un evento `PropertyChanged` quando una proprietà supportata da un `BindableProperty` modifica i valori, ma è possibile scrivere classi personalizzate che implementano `INotifyPropertyChanged` senza derivare da `BindableObject`.

## <a name="code-and-xaml"></a>Codice e XAML

Nell'esempio [**OpacityBindingCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) viene illustrato come impostare un data binding nel codice:

- L'origine è la proprietà `Value` di un `Slider`
- La destinazione è la proprietà `Opacity` di un `Label`

I due oggetti sono connessi impostando il `BindingContext` dell'oggetto `Label` sull'oggetto `Slider`. Le due proprietà sono connesse chiamando un metodo di estensione [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) sul `Label` che fa riferimento alla proprietà `OpacityProperty` associabile e alla proprietà `Value` del `Slider` espresso come stringa.

Se si modifica il `Slider`, la `Label` si dissolverà e non sarà più visualizzata.

[**OpacityBindingXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) è lo stesso programma con il Data Binding impostato in XAML. Il `BindingContext` del `Label` è impostato su un'estensione di markup `x:Reference` che fa riferimento al `Slider`e la proprietà `Opacity` del `Label` viene impostata sull'estensione di markup `Binding` con la relativa proprietà [`Path`](xref:Xamarin.Forms.Binding.Path) che fa riferimento alla proprietà `Value` del `Slider`.

## <a name="source-and-bindingcontext"></a>Origine e BindingContext

L'esempio [**BindingSourceCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) illustra un approccio alternativo nel codice. Un oggetto `Binding` viene creato impostando la proprietà [`Source`](xref:Xamarin.Forms.Binding.Source) sull'oggetto `Slider` e la proprietà [`Path`](xref:Xamarin.Forms.Binding.Path) su "value". Il metodo [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) di `BindableObject` viene quindi chiamato sull'oggetto `Label`.

È inoltre possibile utilizzare il [costruttore`Binding`](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) per definire l'oggetto `Binding`.

L'esempio [**BindingSourceXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) illustra la tecnica analoga in XAML. La proprietà `Opacity` del `Label` è impostata su un'estensione di markup `Binding` con [`Path`](xref:Xamarin.Forms.Binding.Path) impostata sulla proprietà `Value` e [`Source`](xref:Xamarin.Forms.Binding.Source) impostata su un'estensione di markup `x:Reference` incorporata.

In sintesi, esistono due modi per fare riferimento all'oggetto di origine di associazione:

- Tramite la proprietà `BindingContext` della destinazione
- Tramite la proprietà `Source` dell'oggetto `Binding` stesso

Se vengono specificati entrambi, il secondo ha la precedenza. Il vantaggio del `BindingContext` è che viene propagato attraverso la struttura ad albero visuale. Questa operazione è *molto* utile se più proprietà di destinazione sono associate allo stesso oggetto di origine.

Il programma [**WebViewDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) illustra questa tecnica con l'elemento [`WebView`](xref:Xamarin.Forms.WebView) . Due `Button` elementi per spostarsi all'indietro e in avanti ereditano una `BindingContext` dal relativo elemento padre che fa riferimento al `WebView`. Le proprietà `IsEnabled` dei due pulsanti hanno quindi estensioni di markup semplici `Binding` destinate al pulsante `IsEnabled` proprietà basate sulle impostazioni del [`CanGoBack`](xref:Xamarin.Forms.WebView.CanGoBack) e [`CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward) proprietà di sola lettura del `WebView`.

## <a name="the-binding-mode"></a>La modalità di associazione

Impostare la proprietà [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) di `Binding` su un membro dell'enumerazione [`BindingMode`](xref:Xamarin.Forms.BindingMode) :

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) in modo che le modifiche nella proprietà di origine influiscano sulla destinazione
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) in modo che le modifiche nella proprietà di destinazione influiscano sull'origine
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) in modo che le modifiche nell'origine e nella destinazione si influiscano reciprocamente
- [`Default`](xref:Xamarin.Forms.BindingMode.Default) utilizzare la [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) specificata al momento della creazione del `BindableProperty` di destinazione. Se non è stato specificato alcun valore, per impostazione predefinita viene `OneWay` per le proprietà associabili normali e `OneWayToSource` per le proprietà associabili di sola lettura.

> [!NOTE]
> L'enumerazione `BindingMode` ora include anche `OnTime` per l'applicazione di un'associazione solo quando il contesto di associazione cambia e non quando la proprietà di origine viene modificata.

Le proprietà che sono probabilmente le destinazioni delle associazioni dati negli scenari MVVM hanno in genere un `DefaultBindingMode` di `TwoWay`. ovvero:

- Proprietà `Value` di `Slider` e `Stepper`
- Proprietà `IsToggled` di `Switch`
- `Text` proprietà di `Entry`, `Editor`e `SearchBar`
- Proprietà `Date` di `DatePicker`
- Proprietà `Time` di `TimePicker`

L'esempio [**BindingModes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) illustra le quattro modalità di associazione con una data binding in cui la destinazione è la proprietà `FontSize` di un `Label` e l'origine è la proprietà `Value` di un `Slider`. Questo consente a ogni `Slider` di controllare le dimensioni del carattere della `Label`corrispondente. Ma gli elementi `Slider` non vengono inizializzati perché il `DefaultBindingMode` della proprietà `FontSize` è `OneWay`.

L'esempio [**Reverser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) imposta le associazioni sulla proprietà `Value` della `Slider` che fa riferimento alla proprietà `FontSize` di ogni `Label`. Sembra essere all'indietro, ma funziona meglio in initialzing gli elementi `Slider` perché la proprietà `Value` della `Slider` ha una `DefaultBindingMode` di `TwoWay`.

[![Schermata tripla dell'associazione inversa](images/ch16fg06-small.png "Associazione inversa")](images/ch16fg06-large.png#lightbox "Associazione inversa")

Questo comportamento è analogo a come le associazioni vengono definite in MVVM, e si userà questo tipo di associazione spesso.

## <a name="string-formatting"></a>Formattazione delle stringhe

Quando la proprietà di destinazione è di tipo `string`, è possibile usare la proprietà [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) definita da `BindingBase` per convertire l'origine in un `string`. Impostare la proprietà `StringFormat` su una stringa di formattazione .NET che verrà utilizzata con il formato di [`String.Format`](xref:System.String.Format(System.String,System.Object)) statico per visualizzare l'oggetto. Quando si usa questa stringa di formattazione all'interno di un'estensione di markup, racchiuderlo tra virgolette singole in modo che le parentesi graffe non essere confusi con un'estensione di markup incorporato.

Nell'esempio [**ShowViewValues**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) viene illustrato come utilizzare `StringFormat` in XAML.

Nell'esempio [**WhatSizeBindings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) viene illustrata la visualizzazione delle dimensioni della pagina con le associazioni alle proprietà `Width` e `Height` dell'`ContentPage`.

## <a name="why-is-it-called-path"></a>Il motivo per cui si è denominato "Percorso"?

La proprietà [`Path`](xref:Xamarin.Forms.Binding.Path) di `Binding` è così chiamata perché può essere una serie di proprietà e indicizzatori separati da punti. L'esempio [**BindingPathDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) Mostra diversi esempi.

## <a name="binding-value-converters"></a>Convertitori di valori di associazione

Quando le proprietà di origine e di destinazione di un'associazione sono tipi diversi, è possibile convertire tra i tipi utilizzando un convertitore di associazioni. Si tratta di una classe che implementa l'interfaccia [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) e contiene due metodi: [`Convert`](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) per convertire l'origine nella destinazione e [`ConvertBack`](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) per convertire la destinazione nell'origine.

La classe [`IntToBoolConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) è un esempio per la conversione di un `int` in un `bool`. Viene illustrato dall'esempio [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) , che Abilita solo la `Button` se è stato digitato almeno un carattere in un `Entry`.

La classe [`BoolToStringConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) converte una `bool` in un `string` e definisce due proprietà per specificare quale testo deve essere restituito per `false` e `true` valori.
Il [`BoolToColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) è simile. Nell'esempio [**SwitchText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) viene illustrato l'utilizzo di questi due convertitori per visualizzare testi diversi in colori diversi in base a un'impostazione `Switch`.

Il [`BoolToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) generico può sostituire l'`BoolToStringConverter` e `BoolToColorConverter` e fungere da convertitore da `bool`a oggetto generalizzato di qualsiasi tipo.

## <a name="bindings-and-custom-views"></a>Associazioni e le visualizzazioni personalizzate

È possibile semplificare i controlli personalizzati utilizzando le associazioni dati. Il file di codice [`NewCheckBox.cs`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) definisce `Text`, `TextColor`, `FontSize`, `FontAttributes`e `IsChecked` proprietà, ma non dispone di alcuna logica per gli oggetti visivi del controllo.
Il file di [`NewCheckBox.cs.xaml`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) contiene invece tutto il markup per gli oggetti visivi del controllo tramite Data Binding sugli elementi `Label` in base alle proprietà definite nel file code-behind.

Nell'esempio [**NewCheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) viene illustrato il `NewCheckBox` controllo personalizzato.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 16 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Capitolo 16 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [Data binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
