---
title: Riepilogo del capitolo 16. Associazione dati
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: ED997DB0-C229-4868-A5FB-928703B377D6
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: cf08874f66c9ab21cd0ede642c8c94821b6c5a2a
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-16-data-binding"></a>Riepilogo del capitolo 16. Associazione dati

I programmatori spesso gli uni scrittura di gestori di eventi che rilevano quando una proprietà di un oggetto è stato modificato e da utilizzare per modificare il valore di una proprietà in un altro oggetto. Questo processo può essere automatizzato con la tecnica di *associazione dati*. Le associazioni dati sono in genere definite in XAML e diventano parte della definizione dell'interfaccia utente.

Molto spesso, le associazioni dati connettono oggetti dell'interfaccia utente ai dati sottostanti. Questa è una tecnica che viene esaminata più in [ **capitolo 18. MVVM**](chapter18.md). Tuttavia, le associazioni di dati possono inoltre connettersi a due o più elementi dell'interfaccia utente. La maggior parte degli esempi anticipati del data binding in questo capitolo viene illustrato questa tecnica.

## <a name="binding-basics"></a>Nozioni fondamentali di associazione

Diverse proprietà, metodi e le classi coinvolte nell'associazione di dati:

- Il [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/) deriva dalla classe [ `BindingBase` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingBase/) e include molte caratteristiche di un'associazione dati
- Il [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) sia definita tramite la [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) classe
- Il [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) metodo viene definito anche dal [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) classe
- Il [ `BindableObjectExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObjectExtensions/) classe definisce tre ulteriori `SetBinding` metodi

Le due classi seguenti supportano le estensioni di markup XAML per le associazioni:

- [`BindingExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/) supporta il `Binding` estensione di markup
- [`ReferenceExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/) supporta il `x:Reference` estensione di markup

Due interfacce coinvolti nell'associazione di dati:

- [`INotifyPropertyChanged`](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) nel `System.ComponentModel` è lo spazio dei nomi per l'implementazione di notifica quando una proprietà viene modificato
- [`IValueConverter`](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) viene utilizzato per definire le classi di piccole dimensioni che convertono i valori da un tipo a un altro data binding

Un'associazione di dati si connette due proprietà dell'oggetto stesso, o (di solito) due oggetti diversi. Queste due proprietà sono dette il *origine* e *destinazione*. In genere, una modifica nella proprietà origine provoca una modifica di proprietà di destinazione, ma in alcuni casi la direzione è invertita. Indipendentemente dal fatto che:

- il *destinazione* proprietà deve essere supportata da un [`BindableProperty`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)
- il *origine* proprietà in genere è un membro di una classe che implementa [`INotifyPropertyChanged`](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/)

Una classe che implementa `INotifyPropertyChanged` viene attivato un [ `PropertyChanged` ](https://developer.xamarin.com/api/event/System.ComponentModel.INotifyPropertyChanged.PropertyChanged/) evento quando un valore della proprietà. `BindableObject` implementa `INotifyPropertyChanged` e viene generato automaticamente un `PropertyChanged` evento quando una proprietà supportata da un `BindableProperty` vengono modificati i valori, ma è possibile scrivere le proprie classi che implementano `INotifyPropertyChanged` senza la derivazione da `BindableObject`.

## <a name="code-and-xaml"></a>Codice e XAML

Il [ **OpacityBindingCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) esempio viene illustrato come impostare un data binding nel codice:

- L'origine è il `Value` proprietà di un `Slider`
- La destinazione è il `Opacity` proprietà di un `Label`

I due oggetti connessi impostando il `BindingContext` del `Label` dell'oggetto per il `Slider` oggetto. Le due proprietà sono connessi tramite la chiamata un [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObjectExtensions.SetBinding/p/Xamarin.Forms.BindableObject/Xamarin.Forms.BindableProperty/System.String/) metodo di estensione sul `Label` che fanno riferimento al `OpacityProperty` proprietà associabile e `Value` proprietà del `Slider` espresso come un stringa.

La modifica di `Slider` causa il `Label` dissolvenza rimosso dalla visualizzazione.

Il [ **OpacityBindingXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) il programma con l'associazione di dati impostato in XAML. Il `BindingContext` del `Label` è impostata su un `x:Reference` riferimento estensione di markup di `Slider`e il `Opacity` proprietà del `Label` è impostato sul `Binding` estensione di markup con relativo [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) riferimento alle proprietà di `Value` proprietà del `Slider`.

## <a name="source-and-bindingcontext"></a>Origine e BindingContext

Il [ **BindingSourceCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) nel codice di esempio viene illustrato un approccio alternativo. A `Binding` oggetto viene creato mediante l'impostazione di [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Source/) proprietà per il `Slider` oggetto e il [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) proprietà su "Value". Il [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) metodo `BindableObject` viene quindi chiamata nel `Label` oggetto.

Il [ `Binding` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Binding.Binding/p/System.String/Xamarin.Forms.BindingMode/Xamarin.Forms.IValueConverter/System.Object/System.String/System.Object/) può anche avere usato per definire il `Binding` oggetto.

Il [ **BindingSourceXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) illustra la tecnica confrontabile in XAML. Il `Opacity` proprietà del `Label` è impostata su un `Binding` estensione di markup con [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) impostato sul `Value` proprietà e [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Source/) impostato su un incorporata `x:Reference` estensione di markup.

In sintesi, esistono due modi per fare riferimento all'oggetto di origine di associazione:

- Tramite il `BindingContext` proprietà della destinazione
- Tramite il `Source` proprietà del `Binding` stesso oggetto

Se vengono specificati entrambi, il secondo ha la precedenza. Il vantaggio del `BindingContext` è che si è propagato tramite la struttura ad albero visuale. Si tratta di *molto* utile se più proprietà di destinazione sono associate allo stesso oggetto di origine.

Il [ **WebViewDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) programma illustra questa tecnica con il [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) elemento. Due `Button` elementi per lo spostamento all'indietro e Avanti di ereditare un `BindingContext` dal relativo elemento padre che fa riferimento il `WebView`. Il `IsEnabled` quindi le proprietà dei due pulsanti sono semplici `Binding` le estensioni di markup che il pulsante di destinazione `IsEnabled` proprietà in basano alle impostazioni del [ `CanGoBack` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.CanGoBack/) e [ `CanGoForward` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.CanGoForward/) le proprietà di sola lettura del `WebView`.

## <a name="the-binding-mode"></a>La modalità di associazione

Impostare il [ `Mode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.Mode/) proprietà di `Binding` a un membro del [ `BindingMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingMode/) enumerazione:

- [`OneWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWay/) in modo che le modifiche alla proprietà di origine ripercuoterà
- [`OneWayToSource`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.OneWayToSource/) in modo che le modifiche alla proprietà di destinazione influisce sull'origine
- [`TwoWay`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.TwoWay/) in modo che le modifiche nell'origine e destinazione interagiscono
- [`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.BindingMode.Default/) Utilizzare il [ `DefaultBindingMode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableProperty.DefaultBindingMode/) specificato quando la destinazione `BindableProperty` è stato creato. Se non è stato specificato, il valore predefinito è `OneWay` per le proprietà associabili normale, e `OneWayToSource` per le proprietà associabili di sola lettura.

Le proprietà che potrebbero essere la destinazione dell'associazione di dati in scenari MVVM in genere presentano una `DefaultBindingMode` di `TwoWay`. Questi sono:

- `Value` proprietà di `Slider` e `Stepper`
- `IsToggled` proprietà di `Switch`
- `Text` proprietà di `Entry`, `Editor`, e `SearchBar`
- `Date` proprietà di `DatePicker`
- `Time` proprietà di `TimePicker`

Il [ **BindingModes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) illustra le modalità di quattro associazione con un'associazione di dati in cui la destinazione è il `FontSize` proprietà di un `Label` e l'origine è il `Value` proprietà di un `Slider`. In questo modo ogni `Slider` per controllare le dimensioni del carattere dell'oggetto corrispondente `Label`. Ma la `Slider` elementi non vengono inizializzati perché il `DefaultBindingMode` del `FontSize` proprietà `OneWay`.

Il [ **ReverseBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) esempio imposta le associazioni sul `Value` proprietà del `Slider` che fa riferimento il `FontSize` proprietà di ogni `Label`. Viene visualizzato a ritroso, ma funziona meglio l'inizializzazione di `Slider` elementi perché il `Value` proprietà del `Slider` ha un `DefaultBindingMode` di `TwoWay`.

[![Schermata triplo di associazione inversa](images/ch16fg06-small.png "associazione inversa")](images/ch16fg06-large.png#lightbox "associazione inversa")

Questo comportamento è analogo a come le associazioni vengono definite in MVVM e si utilizzerà questo tipo di associazione di frequente.

## <a name="string-formatting"></a>Formattazione di stringhe

Quando la proprietà di destinazione è di tipo `string`, è possibile utilizzare il [ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.StringFormat/) definita dalla proprietà `BindingBase` per convertire l'origine per un `string`. Impostare il `StringFormat` proprietà da una stringa che si utilizzerebbero con statica di formattazione .NET [ `String.Format` ](https://developer.xamarin.com/api/member/System.String.Format/p/System.String/System.Object/) formato per visualizzare l'oggetto. Quando si utilizza questa stringa di formattazione all'interno di un'estensione di markup, racchiuderlo tra virgolette singole in modo da non essere confusi le parentesi graffe per un'estensione di markup incorporato.

Il [ **ShowViewValues** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) esempio viene illustrato come utilizzare `StringFormat` in XAML.

Il [ **WhatSizeBindings** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) esempio viene illustrato come visualizzare le dimensioni della pagina con associazioni al `Width` e `Height` le proprietà del `ContentPage`.

## <a name="why-is-it-called-path"></a>Motivo per cui denominato "Path"?

Il [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) proprietà `Binding` viene pertanto chiamato poiché può essere una serie di proprietà e indicizzatori separati da punti. Il [ **BindingPathDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) illustra alcuni esempi.

## <a name="binding-value-converters"></a>Convertitori di valori di associazione

Quando le proprietà di origine e di destinazione di un'associazione sono tipi diversi, è possibile convertire tra i tipi di utilizzo di un convertitore di associazione. Si tratta di una classe che implementa il [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/) l'interfaccia e contiene due metodi: [ `Convert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IValueConverter.Convert/p/System.Object/System.Type/System.Object/System.Globalization.CultureInfo/) per convertire l'origine alla destinazione e [ `ConvertBack` ](https://developer.xamarin.com/api/member/Xamarin.Forms.IValueConverter.ConvertBack/p/System.Object/System.Type/System.Object/System.Globalization.CultureInfo/) Per convertire l'origine di destinazione.

Il [ `IntToBoolConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria è un esempio di conversione di un `int` per un `bool`. Viene fornita dal [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) esempio, che consente solo di `Button` se almeno un carattere che è stato digitato in un `Entry`.

Il [ `BoolToStringConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) classe converte un `bool` per un `string` e definisce due proprietà per specificare il testo deve essere restituito per `false` e `true` valori.
Il [ `BoolToColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs) è simile. Il [ **SwitchText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) esempio viene illustrato l'utilizzo di questi due convertitori di tipi per visualizzare i testi diversi colori diversi in base a un `Switch` impostazione.

Generico [ `BoolToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) possibile sostituire il `BoolToStringConverter` e `BoolToColorConverter` e fungono da un generalizzato `bool`-a-convertitore di oggetti di qualsiasi tipo.

## <a name="bindings-and-custom-views"></a>Associazioni e visualizzazioni personalizzate

È possibile semplificare i controlli personalizzati usando le associazioni di dati. Il [ `NewCheckBox.cs` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) file di codice definisce `Text`, `TextColor`, `FontSize`, `FontAttributes`, e `IsChecked` proprietà, ma non ha la logica per gli oggetti visivi del controllo.
Invece di [ `NewCheckBox.cs.xaml` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) file contiene tutti i commenti per gli oggetti visivi del controllo tramite le associazioni dati sul `Label` elementi basano sulle proprietà definite nel file code-behind.

Il [ **NewCheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) esempio viene illustrato il `NewCheckBox` controllo personalizzato.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 16 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Esempi di capitolo 16](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
