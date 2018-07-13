---
title: Riepilogo del capitolo 16. Associazione dati
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 16. Associazione dati'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: c4ad067778203759a54ed8141db0b82602e40f6c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997452"
---
# <a name="summary-of-chapter-16-data-binding"></a>Riepilogo del capitolo 16. Associazione dati

I programmatori spesso trovano autonomamente la scrittura di gestori di eventi che rilevano quando una proprietà di un oggetto è stato modificato e usano per modificare il valore di una proprietà in un altro oggetto. È possibile automatizzare questo processo con la tecnica di *associazione dati*. Le associazioni dati sono in genere definite in XAML e diventano parte della definizione dell'interfaccia utente.

Queste associazioni di dati molto spesso, connettono oggetti dell'interfaccia utente ai dati sottostanti. Questa è una tecnica che verrà esaminata più nelle [ **capitolo 18. MVVM**](chapter18.md). Tuttavia, i data binding può anche connettersi due o più elementi dell'interfaccia utente. La maggior parte degli esempi iniziali dell'associazione dati in questo capitolo illustra questa tecnica.

## <a name="binding-basics"></a>Nozioni di base di associazione

Diverse proprietà, metodi e classi sono coinvolti nell'associazione dati:

- Il [ `Binding` ](xref:Xamarin.Forms.Binding) deriva dalla classe [ `BindingBase` ](xref:Xamarin.Forms.BindingBase) e incapsula numerose caratteristiche di un data binding
- Il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) proprietà è definita per il [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) classe
- Il [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) metodo è definito anche per il [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) classe
- Il [ `BindableObjectExtensions` ](xref:Xamarin.Forms.BindableObjectExtensions) classe definisce tre ulteriori `SetBinding` metodi

Le due classi seguenti supportano le estensioni di markup XAML per i binding:

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension) supporta il `Binding` estensione di markup
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) supporta il `x:Reference` estensione di markup

Nel data binding, sono coinvolti due interfacce:

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) nel `System.ComponentModel` dello spazio dei nomi è necessaria per implementare la notifica quando viene modificata una proprietà
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) Consente di definire le classi di piccole dimensioni che convertono i valori da un tipo a altro data binding

Un'associazione di dati si connette due proprietà dell'oggetto stesso, o (più comunemente) due oggetti diversi. Queste due proprietà vengono definite le *origine* e il *destinazione*. In generale, una modifica della proprietà di origine causa una modifica avvenga in proprietà di destinazione, ma in alcuni casi la direzione viene invertita. Indipendentemente dal fatto che:

- il *destinazione* proprietà deve essere supportata da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- il *origine* proprietà a livello generale è un membro di una classe che implementa [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

Una classe che implementa `INotifyPropertyChanged` viene attivato un [ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) evento quando un valore della proprietà. `BindableObject` implementa `INotifyPropertyChanged` e viene generato automaticamente un `PropertyChanged` evento quando una proprietà supportata da un `BindableProperty` vengono modificati i valori, ma è possibile scrivere le proprie classi che implementano `INotifyPropertyChanged` senza che deriva da `BindableObject`.

## <a name="code-and-xaml"></a>Codice e XAML

Il [ **OpacityBindingCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) esempio viene illustrato come impostare un data binding nel codice:

- L'origine è il `Value` proprietà di un `Slider`
- La destinazione è il `Opacity` proprietà di un `Label`

I due oggetti sono connessi tramite l'impostazione di `BindingContext` del `Label` dell'oggetto per il `Slider` oggetto. Le due proprietà sono connessi tramite una chiamata una [ `SetBinding` ](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) metodo di estensione sul `Label` che fanno riferimento al `OpacityProperty` proprietà associabile e la `Value` proprietà del `Slider` espresso come un stringa.

La modifica di `Slider` causa il `Label` a dissolvenza dalla visualizzazione.

Il [ **OpacityBindingXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) è lo stesso programma con l'associazione di dati impostato in XAML. Il `BindingContext` del `Label` è impostata su un `x:Reference` fa riferimento a estensioni di markup il `Slider`e il `Opacity` proprietà del `Label` è impostata sul `Binding` estensione di markup con relativo [ `Path` ](xref:Xamarin.Forms.Binding.Path) che fanno riferimento a proprietà di `Value` proprietà del `Slider`.

## <a name="source-and-bindingcontext"></a>Origine e BindingContext

Il [ **BindingSourceCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) nel codice di esempio viene illustrato un approccio alternativo. A `Binding` oggetto viene creato mediante l'impostazione di [ `Source` ](xref:Xamarin.Forms.Binding.Source) proprietà per il `Slider` oggetto e la [ `Path` ](xref:Xamarin.Forms.Binding.Path) proprietà su "Value". Il [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) metodo `BindableObject` viene quindi chiamata nel `Label` oggetto.

Il [ `Binding` costruttore](xref:Xamarin.Forms.Binding.%23ctor(System.String,Xamarin.Forms.BindingMode,Xamarin.Forms.IValueConverter,System.Object,System.String,System.Object)) potrebbe anche avere utilizzato per definire il `Binding` oggetto.

Il [ **BindingSourceXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) esempio vengono illustrate la tecnica confrontabile in XAML. Il `Opacity` proprietà del `Label` è impostata su un `Binding` estensione di markup con [ `Path` ](xref:Xamarin.Forms.Binding.Path) impostato sul `Value` proprietà e [ `Source` ](xref:Xamarin.Forms.Binding.Source) impostata su un Embedded `x:Reference` estensione di markup.

In sintesi, esistono due modi per fare riferimento all'oggetto di origine di associazione:

- Tramite il `BindingContext` proprietà della destinazione
- Tramite il `Source` proprietà del `Binding` oggetto stesso

Se vengono specificati entrambi, il secondo ha la precedenza. Il vantaggio del `BindingContext` è che viene propagato attraverso la struttura ad albero visuale. Si tratta *molto* comodo se più proprietà di destinazione sono associate allo stesso oggetto di origine.

Il [ **WebViewDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) programma illustra questa tecnica con la [ `WebView` ](xref:Xamarin.Forms.WebView) elemento. Due `Button` gli elementi per la navigazione all'indietro e Avanti ereditano un' `BindingContext` dal relativo elemento padre che fa riferimento il `WebView`. Il `IsEnabled` le proprietà dei due pulsanti hanno quindi semplice `Binding` le estensioni di markup che il pulsante di destinazione `IsEnabled` proprietà basate sulle impostazioni del [ `CanGoBack` ](xref:Xamarin.Forms.WebView.CanGoBack) e [ `CanGoForward` ](xref:Xamarin.Forms.WebView.CanGoForward) le proprietà di sola lettura il `WebView`.

## <a name="the-binding-mode"></a>La modalità di associazione

Impostare il [ `Mode` ](xref:Xamarin.Forms.BindingBase.Mode) proprietà della `Binding` a un membro del [ `BindingMode` ](xref:Xamarin.Forms.BindingMode) enumerazione:

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) in modo che le modifiche alla proprietà di origine ripercuoterà
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) in modo che le modifiche apportate alla proprietà di destinazione influisce sull'origine
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) in modo che le modifiche nell'origine e destinazione interferiscano tra loro
- [`Default`](xref:Xamarin.Forms.BindingMode.Default) usare la [ `DefaultBindingMode` ](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) specificato quando la destinazione `BindableProperty` è stato creato. Se è stato specificato nessuno, il valore predefinito è `OneWay` per le proprietà associabili normale, e `OneWayToSource` per le proprietà associabili di sola lettura.

Le proprietà che potrebbero essere la destinazione dell'associazione dati in scenari MVVM in genere presentano una `DefaultBindingMode` di `TwoWay`. Questi sono:

- `Value` proprietà di `Slider` e `Stepper`
- `IsToggled` proprietà di `Switch`
- `Text` proprietà del `Entry`, `Editor`, e `SearchBar`
- `Date` proprietà di `DatePicker`
- `Time` proprietà di `TimePicker`

Il [ **BindingModes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) esempio illustra le modalità di quattro associazione con un'associazione di dati in cui la destinazione è la `FontSize` proprietà di un `Label` e l'origine è il `Value` proprietà di un `Slider`. In questo modo ognuna `Slider` per controllare le dimensioni del carattere dell'oggetto corrispondente `Label`. Ma il `Slider` gli elementi non vengono inizializzati in quanto il `DefaultBindingMode` delle `FontSize` proprietà è `OneWay`.

Il [ **ReverseBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) esempio imposta le associazioni nel `Value` proprietà del `Slider` che fanno riferimento al `FontSize` proprietà della ognuno `Label`. Questa sembra essere con le versioni precedenti, ma funziona meglio nell'inizializzazione di `Slider` elementi perché la `Value` proprietà del `Slider` ha un `DefaultBindingMode` di `TwoWay`.

[![Tripla screenshot dell'associazione di invertire](images/ch16fg06-small.png "associazione inversa")](images/ch16fg06-large.png#lightbox "inversa di associazione")

Questo comportamento è analogo a come le associazioni vengono definite in MVVM, e si userà questo tipo di associazione spesso.

## <a name="string-formatting"></a>Formattazione delle stringhe

Quando la proprietà di destinazione è di tipo `string`, è possibile usare il [ `StringFormat` ](xref:Xamarin.Forms.BindingBase.StringFormat) definita dalla proprietà `BindingBase` per convertire l'origine per un `string`. Impostare il `StringFormat` proprietà da una stringa che si utilizzerebbero con il metodo statico di formattazione .NET [ `String.Format` ](xref:System.String.Format(System.String,System.Object)) formato per visualizzare l'oggetto. Quando si usa questa stringa di formattazione all'interno di un'estensione di markup, racchiuderlo tra virgolette singole in modo che le parentesi graffe non essere confusi con un'estensione di markup incorporato.

Il [ **ShowViewValues** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) esempio viene illustrato come utilizzare `StringFormat` in XAML.

Il [ **WhatSizeBindings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) esempio viene illustrato come visualizzare le dimensioni della pagina con le associazioni per il `Width` e `Height` le proprietà del `ContentPage`.

## <a name="why-is-it-called-path"></a>Il motivo per cui si è denominato "Percorso"?

Il [ `Path` ](xref:Xamarin.Forms.Binding.Path) proprietà della `Binding` viene pertanto chiamato poiché può essere una serie di proprietà e indicizzatori separati da punti. Il [ **BindingPathDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) esempio vengono illustrati diversi esempi.

## <a name="binding-value-converters"></a>Convertitori di valori di associazione

Quando le proprietà di origine e di destinazione di un'associazione sono tipi diversi, è possibile convertire tra i tipi utilizzando un convertitore di associazioni. Si tratta di una classe che implementa il [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter) interfaccia e contiene due metodi: [ `Convert` ](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) per convertire l'origine alla destinazione, e [ `ConvertBack` ](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) Per convertire la destinazione all'origine.

Il [ `IntToBoolConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria è un esempio per la conversione di un `int` a un `bool`. È dimostrato dal [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) campione, che consente solo di `Button` se almeno un carattere è stato digitato in un `Entry`.

Il [ `BoolToStringConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) classe converte un `bool` a un `string` e definisce due proprietà per specificare il tipo di testo deve essere restituito per `false` e `true` valori.
Il [ `BoolToColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) è simile. Il [ **SwitchText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) esempio viene illustrato l'utilizzo di questi due convertitori di tipi per visualizzare diversi testi in colori diversi in base un `Switch` impostazione.

Il tipo generico [ `BoolToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) possibile sostituire il `BoolToStringConverter` e `BoolToColorConverter` e vengono usati come generalizzata `bool`-a-convertitore di oggetti di qualsiasi tipo.

## <a name="bindings-and-custom-views"></a>Associazioni e le visualizzazioni personalizzate

È possibile semplificare i controlli personalizzati utilizzando le associazioni dati. Il [ `NewCheckBox.cs` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) file di codice definisce `Text`, `TextColor`, `FontSize`, `FontAttributes`, e `IsChecked` proprietà, ma non ha la logica per gli oggetti visivi del controllo.
Invece di [ `NewCheckBox.cs.xaml` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) file contiene tutto il markup per gli oggetti visivi del controllo tramite le associazioni dati sul `Label` elementi basano sulle proprietà definite nel file code-behind.

Il [ **NewCheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) esempio viene illustrato il `NewCheckBox` controllo personalizzato.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 16 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Esempi di capitolo 16](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
