---
title: Riassunto del capitolo 16. Associazione dati
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 16. Associazione dati'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 2d61413fb1d8c28a3957da53601d0ad682f35518
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771095"
---
# <a name="summary-of-chapter-16-data-binding"></a>Riassunto del capitolo 16. Associazione dati

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> Le note in questa pagina indicano le aree in cui Xamarin.Forms si è discostata dal materiale presentato nel libro.

I programmatori spesso si trovano a scrivere gestori eventi che rilevano quando una proprietà di un oggetto è stata modificata e lo usano per modificare il valore di una proprietà in un altro oggetto. Questo processo può essere automatizzato con la tecnica *dell'associazione dati.* Le associazioni dati vengono in genere definite in XAML e diventano parte della definizione dell'interfaccia utente.

Molto spesso, queste associazioni dati connettono gli oggetti dell'interfaccia utente ai dati sottostanti. Questa è una tecnica che viene esplorata più nel [**capitolo 18. MVVM**](chapter18.md). Tuttavia, le associazioni dati possono anche connettere due o più elementi dell'interfaccia utente. La maggior parte dei primi esempi di associazione dati in questo capitolo illustra questa tecnica.

## <a name="binding-basics"></a>Nozioni di base sull'associazioneBinding basics

Diverse proprietà, metodi e classi sono coinvolti nell'associazione dati:Several properties, methods, and classes are involved in data binding:

- La [`Binding`](xref:Xamarin.Forms.Binding) classe deriva [`BindingBase`](xref:Xamarin.Forms.BindingBase) da e incapsula molte caratteristiche di un'associazione dati
- La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) proprietà è [`BindableObject`](xref:Xamarin.Forms.BindableObject) definita dalla classe
- Il [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) metodo è definito [`BindableObject`](xref:Xamarin.Forms.BindableObject) anche dalla classe
- La [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions) classe definisce `SetBinding` tre metodi aggiuntivi

Le due classi seguenti supportano le estensioni di markup XAML per le associazioni:The following two classes support XAML markup extensions for bindings:

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)supporta l'estensione di `Binding` markup
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)supporta l'estensione di `x:Reference` markup

Nell'associazione dati sono coinvolte due interfacce:Two interfaces are involved in data binding:

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)nello `System.ComponentModel` spazio dei nomi è per l'implementazione di notifica quando una proprietà viene modificata
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter)viene utilizzato per definire piccole classi che convertono i valori da un tipo a un altro nelle associazioni dati

Un'associazione dati connette due proprietà dello stesso oggetto o (più comunemente) due oggetti diversi. Queste due proprietà vengono definite *origine* e *destinazione.* In genere, una modifica nella proprietà di origine causa una modifica nella proprietà di destinazione, ma a volte la direzione viene invertita. Indipendentemente:

- la proprietà *di destinazione* deve essere[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- la proprietà *di origine* è in genere un membro di una classe che implementa[`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

Una classe `INotifyPropertyChanged` che implementa [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) genera un evento quando una proprietà cambia valore. `BindableObject`implementa `INotifyPropertyChanged` e genera `PropertyChanged` automaticamente un evento quando `BindableProperty` una proprietà supportata da un `INotifyPropertyChanged` valore modificato, `BindableObject`ma è possibile scrivere classi personalizzate che implementano senza derivare da .

## <a name="code-and-xaml"></a>Codice e XAML

Il OpacityBindingCode esempio viene illustrato come impostare un'associazione dati nel codice:The [**OpacityBindingCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) sample demonstrates how to set a data binding in code:

- La fonte `Value` è di proprietà di un`Slider`
- L'obiettivo `Opacity` è di proprietà di un`Label`

I due oggetti vengono `BindingContext` connessi `Label` impostando l'oggetto sull'oggetto. `Slider` Le due proprietà sono [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) connesse `Label` chiamando un `OpacityProperty` metodo di estensione `Value` sul `Slider` riferimento alla proprietà associabile e la proprietà dell'espresso come stringa.

La modifica `Slider` di `Label` then causa la dissolvenza in entrata e in uscita dalla visualizzazione.

Il [**OpacityBindingXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) è lo stesso programma con l'associazione dati impostata in XAML. `BindingContext` L'oggetto `Label` è impostato `x:Reference` su un'estensione `Slider`di `Opacity` markup che `Label` fa riferimento `Binding` a , [`Path`](xref:Xamarin.Forms.Binding.Path) e la `Value` proprietà dell'oggetto viene impostata sull'estensione di markup con la relativa proprietà che fa riferimento alla proprietà dell'oggetto `Slider`.

## <a name="source-and-bindingcontext"></a>Origine e BindingContext

Nell'esempio [**BindingSourceCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) viene illustrato un approccio alternativo nel codice. Un `Binding` oggetto viene creato [`Source`](xref:Xamarin.Forms.Binding.Source) impostando `Slider` la proprietà [`Path`](xref:Xamarin.Forms.Binding.Path) sull'oggetto e la proprietà su "Value". Il [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) metodo `BindableObject` di viene `Label` quindi chiamato sull'oggetto.

Il [ `Binding` costruttore](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) potrebbe anche essere `Binding` stato utilizzato per definire l'oggetto.

Il [**BindingSourceXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) esempio viene illustrata la tecnica confrontabile in XAML. La `Opacity` proprietà `Label` dell'oggetto `Binding` è impostata su un'estensione di markup [`Path`](xref:Xamarin.Forms.Binding.Path) con impostato sulla `Value` proprietà e [`Source`](xref:Xamarin.Forms.Binding.Source) impostato su un'estensione di markup incorporata. `x:Reference`

In sintesi, esistono due modi per fare riferimento all'oggetto origine di associazione:In summary, there are two ways to reference the binding source object:

- Attraverso `BindingContext` la proprietà dell'obiettivo
- Attraverso `Source` la proprietà `Binding` dell'oggetto stesso

Se vengono specificati entrambi, il secondo ha la precedenza. Il vantaggio `BindingContext` di è che viene propagato attraverso la struttura ad albero visuale. Ciò è *molto* utile se più proprietà di destinazione sono associate allo stesso oggetto di origine.

Il programma [**WebViewDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) viene [`WebView`](xref:Xamarin.Forms.WebView) illustrata questa tecnica con l'elemento. Due `Button` elementi per lo spostamento all'indietro e all'indietro ereditano a `BindingContext` dal relativo elemento padre che fa riferimento a `WebView`. Le `IsEnabled` proprietà dei due pulsanti dispongono `Binding` quindi `IsEnabled` di semplici estensioni [`CanGoBack`](xref:Xamarin.Forms.WebView.CanGoBack) [`CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward) di markup che `WebView`hanno come destinazione le proprietà del pulsante in base alle impostazioni delle proprietà e di sola lettura dell'oggetto .

## <a name="the-binding-mode"></a>La modalità di rilegatura

Impostare [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) la `Binding` proprietà di [`BindingMode`](xref:Xamarin.Forms.BindingMode) su un membro dell'enumerazione:

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay)in modo che le modifiche nella proprietà di origine influiscano sulla
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource)in modo che le modifiche nella proprietà di destinazione influiscano sull'origine
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)in modo che le modifiche nella destinazione e nell'origine si influenzino a vicenda
- [`Default`](xref:Xamarin.Forms.BindingMode.Default)per utilizzare [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) l'oggetto `BindableProperty` specificato al momento della creazione della destinazione. Se non è stato specificato `OneWay` alcun valore, il `OneWayToSource` valore predefinito è per le normali proprietà associabili e per le proprietà associabili di sola lettura.

> [!NOTE]
> L'enumerazione `BindingMode` `OnTime` ora include anche per l'applicazione di un'associazione solo quando il contesto di associazione cambia e non quando la proprietà di origine viene modificata.

Le proprietà che potrebbero essere le destinazioni delle associazioni dati `DefaultBindingMode` `TwoWay`negli scenari MVVM hanno in genere un valore di . Si tratta di:

- Proprietà `Value` di `Slider` e `Stepper`
- Proprietà `IsToggled` di `Switch`
- `Text`proprietà `Entry`di `Editor`, , e`SearchBar`
- Proprietà `Date` di `DatePicker`
- Proprietà `Time` di `TimePicker`

Nell'esempio [**BindingModes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) vengono illustrate le quattro modalità `FontSize` di associazione con un'associazione dati in cui la destinazione è la proprietà di un `Label` oggetto e l'origine è la `Value` proprietà di un `Slider`oggetto . Ciò `Slider` consente a ciascuno di `Label`controllare la dimensione del carattere del file corrispondente. Ma `Slider` gli elementi non vengono `DefaultBindingMode` inizializzati `OneWay`perché la proprietà è `FontSize` .

Nell'esempio [**ReverseBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) vengono impostate `Value` le `Slider` associazioni sulla `FontSize` proprietà `Label`dell'oggetto che fa riferimento alla proprietà di ogni oggetto . Questo sembra essere a ritroso, ma `Slider` funziona meglio `Value` nell'iniziale degli elementi perché la proprietà di `Slider` ha un `DefaultBindingMode` di `TwoWay`.

[![Triplo screenshot di Reverse Binding](images/ch16fg06-small.png "Associazione inversa")](images/ch16fg06-large.png#lightbox "Associazione inversa")

Questo è analogo a come le associazioni sono definite in MVVM e si userà questo tipo di associazione frequentemente.

## <a name="string-formatting"></a>Formattazione delle stringhe

Quando la proprietà di `string`destinazione è [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) di tipo `BindingBase` , è possibile `string`utilizzare la proprietà definita da per convertire l'origine in un oggetto . Impostare `StringFormat` la proprietà su una stringa di formattazione .NET da utilizzare con il formato statico [`String.Format`](xref:System.String.Format(System.String,System.Object)) per visualizzare l'oggetto. Quando si usa questa stringa di formattazione all'interno di un'estensione di markup, racchiuderla tra virgolette singole in modo che le parentesi graffe non vengano scambiate per un'estensione di markup incorporata.

Nell'esempio [**ShowViewValues**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) viene `StringFormat` illustrato come utilizzare XAML.

Nell'esempio [**WhatSizeBindings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) viene illustrata la visualizzazione delle `Width` `Height` dimensioni della `ContentPage`pagina con associazioni alle proprietà e dell'oggetto .

## <a name="why-is-it-called-path"></a>Perché si chiama "Percorso"?

La [`Path`](xref:Xamarin.Forms.Binding.Path) proprietà `Binding` di è così chiamata perché può essere una serie di proprietà e indicizzatori separati da punti. Il [**BindingPathDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) esempio vengono illustrati diversi esempi.

## <a name="binding-value-converters"></a>Convertitori di valori di associazioneBinding value converters

Quando le proprietà di origine e di destinazione di un'associazione sono tipi diversi, è possibile eseguire la conversione tra i tipi usando un convertitore di associazioni. Si tratta di una [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) classe che implementa [`Convert`](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) l'interfaccia e contiene [`ConvertBack`](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) due metodi: convertire l'origine nella destinazione e convertire la destinazione nell'origine.

La [`IntToBoolConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) classe nella libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) è un `int` esempio `bool`per la conversione di un oggetto in un oggetto . Viene illustrato dall'esempio [**ButtonEnabler,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) che `Button` abilita l'oggetto solo se almeno `Entry`un carattere è stato digitato in un oggetto .

La [`BoolToStringConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) classe `bool` converte `string` a in e definisce due proprietà per `false` `true` specificare il testo da restituire e i valori.
Il [`BoolToColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) è simile. [**Nell'esempio SwitchText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) viene illustrato l'utilizzo di questi due `Switch` convertitori per visualizzare testi diversi in colori diversi in base a un'impostazione.

Il [`BoolToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) generico `BoolToStringConverter` può `BoolToColorConverter` sostituire e e `bool`fungere da convertitore generalizzato-a-oggetto di qualsiasi tipo.

## <a name="bindings-and-custom-views"></a>Associazioni e visualizzazioni personalizzateBindings and custom views

È possibile semplificare i controlli personalizzati usando le associazioni dati. Il [`NewCheckBox.cs`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) file di `Text` `TextColor`codice `FontSize` `FontAttributes`definisce `IsChecked` le proprietà , , , e , ma non dispone di alcuna logica per gli oggetti visivi del controllo.
Il [`NewCheckBox.cs.xaml`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) file contiene invece tutto il markup per gli oggetti `Label` visivi del controllo tramite associazioni dati sugli elementi in base alle proprietà definite nel file code-behind.

[**Nell'esempio NewCheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) viene illustrato il `NewCheckBox` controllo personalizzato.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 16 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Esempi del capitolo 16](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [Data binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
