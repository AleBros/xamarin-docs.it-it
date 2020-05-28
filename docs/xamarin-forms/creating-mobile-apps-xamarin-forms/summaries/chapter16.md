---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 16. Data binding''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ece93730100001e8339a5f50cdb7ac437d96fa62
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136734"
---
# <a name="summary-of-chapter-16-data-binding"></a>Riepilogo del capitolo 16. Data binding

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)

> [!NOTE] 
> Le note in questa pagina indicano Xamarin.Forms le aree in cui è stato divergente rispetto al materiale presentato nel libro.

I programmatori spesso si trovano a scrivere gestori eventi che rilevano quando una proprietà di un oggetto è cambiata e lo usano per modificare il valore di una proprietà in un altro oggetto. Questo processo può essere automatizzato con la tecnica di *Data Binding*. Le associazioni dati sono in genere definite in XAML e diventano parte della definizione dell'interfaccia utente.

Molto spesso, queste associazioni dati connettono gli oggetti dell'interfaccia utente ai dati sottostanti. Si tratta di una tecnica più approfondita nel [**capitolo 18. MVVM**](chapter18.md). Tuttavia, le associazioni dati possono anche connettere due o più elementi dell'interfaccia utente. La maggior parte degli esempi iniziali di data binding in questo capitolo illustra questa tecnica.

## <a name="binding-basics"></a>Nozioni fondamentali sull'associazione

In data binding sono incluse diverse proprietà, metodi e classi:

- La [`Binding`](xref:Xamarin.Forms.Binding) classe deriva da [`BindingBase`](xref:Xamarin.Forms.BindingBase) e incapsula molte caratteristiche di un data binding
- La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) proprietà è definita dalla [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe
- [ `SetBinding` ] (Xrif: Xamarin.Forms . BindableObject. SetBinding ( Xamarin.Forms . BindableProperty, Xamarin.Forms . BindingBase)) il metodo è definito anche dalla [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe
- La [`BindableObjectExtensions`](xref:Xamarin.Forms.BindableObjectExtensions) classe definisce tre `SetBinding` metodi aggiuntivi

Le due classi seguenti supportano le estensioni di markup XAML per le associazioni:

- [`BindingExtension`](xref:Xamarin.Forms.Xaml.BindingExtension)supporta l' `Binding` estensione di markup
- [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension)supporta l' `x:Reference` estensione di markup

In data binding sono incluse due interfacce:

- [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)nello `System.ComponentModel` spazio dei nomi per l'implementazione di notifiche quando una proprietà viene modificata
- [`IValueConverter`](xref:Xamarin.Forms.IValueConverter)viene usato per definire classi di piccole dimensioni che convertono i valori da un tipo a un altro nelle associazioni dati

Un data binding connette due proprietà dello stesso oggetto o (più comunemente) due oggetti diversi. Queste due proprietà sono denominate *origine* e *destinazione*. In genere, una modifica nella proprietà di origine causa una modifica nella proprietà di destinazione, ma a volte la direzione è invertita. Indipendentemente dal

- la proprietà di *destinazione* deve essere supportata da un[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)
- in genere, la proprietà di *origine* è un membro di una classe che implementa[`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged)

Una classe che implementa `INotifyPropertyChanged` genera un [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged) evento quando una proprietà modifica il valore. `BindableObject`implementa `INotifyPropertyChanged` e genera automaticamente un `PropertyChanged` evento quando una proprietà supportata da un oggetto `BindableProperty` modifica i valori, ma è possibile scrivere classi personalizzate che implementano `INotifyPropertyChanged` senza derivare da `BindableObject` .

## <a name="code-and-xaml"></a>Codice e XAML

Nell'esempio [**OpacityBindingCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingCode) viene illustrato come impostare un data binding nel codice:

- L'origine è la `Value` proprietà di un oggetto.`Slider`
- La destinazione è la `Opacity` proprietà di un oggetto.`Label`

I due oggetti sono connessi impostando il valore `BindingContext` dell' `Label` oggetto sull' `Slider` oggetto. Le due proprietà sono connesse chiamando un [`SetBinding`](xref:Xamarin.Forms.BindableObjectExtensions.SetBinding*) metodo di estensione sull'oggetto che fa `Label` riferimento alla `OpacityProperty` proprietà associabile e la `Value` proprietà dell'oggetto `Slider` espresso come stringa.

Se si modifica l'oggetto `Slider` , l'oggetto viene `Label` dissolvente in entrata e in uscita.

[**OpacityBindingXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/OpacityBindingXaml) è lo stesso programma con il Data Binding impostato in XAML. Il `BindingContext` valore di `Label` è impostato su un' `x:Reference` estensione di markup che fa riferimento `Slider` a e la `Opacity` proprietà di `Label` viene impostata sull' `Binding` estensione di markup con la relativa proprietà che fa [`Path`](xref:Xamarin.Forms.Binding.Path) riferimento alla `Value` proprietà dell'oggetto `Slider` .

## <a name="source-and-bindingcontext"></a>Source e BindingContext

L'esempio [**BindingSourceCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceCode) illustra un approccio alternativo nel codice. Un `Binding` oggetto viene creato impostando la [`Source`](xref:Xamarin.Forms.Binding.Source) proprietà sull' `Slider` oggetto e la [`Path`](xref:Xamarin.Forms.Binding.Path) proprietà su "value". [ `SetBinding` ] (Xrif: Xamarin.Forms . BindableObject. SetBinding ( Xamarin.Forms . BindableProperty, Xamarin.Forms . BindingBase)) il metodo di `BindableObject` viene quindi chiamato sull' `Label` oggetto.

[ `Binding` Constructor] (xrif: Xamarin.Forms . Binding .% 23ctor (System. String, Xamarin.Forms . BindingMode, Xamarin.Forms . Per definire l'oggetto, è anche possibile usare IValueConverter, System. Object, System. String, System. Object) `Binding` .

L'esempio [**BindingSourceXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingSourceXaml) illustra la tecnica analoga in XAML. La `Opacity` proprietà di `Label` è impostata su un' `Binding` estensione di markup con [`Path`](xref:Xamarin.Forms.Binding.Path) impostato sulla `Value` proprietà e [`Source`](xref:Xamarin.Forms.Binding.Source) impostata su un'estensione di `x:Reference` markup incorporata.

In breve, esistono due modi per fare riferimento all'oggetto di origine del binding:

- Tramite la `BindingContext` proprietà della destinazione
- Tramite la `Source` proprietà dell' `Binding` oggetto stesso

Se vengono specificati entrambi, il secondo avrà la precedenza. Il vantaggio di `BindingContext` è che viene propagato attraverso la struttura ad albero visuale. Questa operazione è *molto* utile se più proprietà di destinazione sono associate allo stesso oggetto di origine.

Il programma [**WebViewDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WebViewDemo) illustra questa tecnica con l' [`WebView`](xref:Xamarin.Forms.WebView) elemento. Due `Button` elementi per spostarsi all'indietro e in avanti ereditano `BindingContext` dall'elemento padre un oggetto che fa riferimento a `WebView` . Le `IsEnabled` proprietà dei due pulsanti hanno quindi `Binding` estensioni di markup semplici destinate alle proprietà del pulsante `IsEnabled` in base alle impostazioni delle [`CanGoBack`](xref:Xamarin.Forms.WebView.CanGoBack) proprietà di sola [`CanGoForward`](xref:Xamarin.Forms.WebView.CanGoForward) lettura e di `WebView` .

## <a name="the-binding-mode"></a>Modalità di associazione

Impostare la [`Mode`](xref:Xamarin.Forms.BindingBase.Mode) proprietà di `Binding` su un membro dell' [`BindingMode`](xref:Xamarin.Forms.BindingMode) enumerazione:

- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay)in modo che le modifiche nella proprietà di origine influiscano sulla destinazione
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource)in modo che le modifiche nella proprietà di destinazione influiscano sull'origine
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay)in modo che le modifiche nell'origine e nella destinazione si influiscano tra loro
- [`Default`](xref:Xamarin.Forms.BindingMode.Default)per utilizzare l' [`DefaultBindingMode`](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) oggetto specificato quando `BindableProperty` è stata creata la destinazione. Se non è stato specificato alcun valore, l'impostazione predefinita è `OneWay` per le proprietà associabili normali e `OneWayToSource` per le proprietà associabili di sola lettura.

> [!NOTE]
> L' `BindingMode` enumerazione include ora anche `OnTime` per l'applicazione di un'associazione solo quando viene modificato il contesto di associazione e non quando la proprietà di origine viene modificata.

Le proprietà che probabilmente sono le destinazioni delle associazioni dati negli scenari MVVM hanno in genere un `DefaultBindingMode` di `TwoWay` . Si tratta di:

- Proprietà `Value` di `Slider` e `Stepper`
- Proprietà `IsToggled` di `Switch`
- `Text`Proprietà di `Entry` , `Editor` e`SearchBar`
- Proprietà `Date` di `DatePicker`
- Proprietà `Time` di `TimePicker`

L'esempio [**BindingModes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingModes) illustra le quattro modalità di associazione con una data binding in cui la destinazione è la `FontSize` proprietà di un oggetto `Label` e l'origine è la `Value` proprietà di un oggetto `Slider` . Questo consente `Slider` a ognuno di controllare le dimensioni del carattere dell'oggetto corrispondente `Label` . Gli `Slider` elementi, tuttavia, non vengono inizializzati perché il `DefaultBindingMode` della `FontSize` proprietà è `OneWay` .

L'esempio [**Reverser**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ReverseBinding) imposta le associazioni sulla `Value` proprietà di che `Slider` fanno riferimento alla `FontSize` proprietà di ogni oggetto `Label` . Sembra essere all'indietro, ma funziona meglio in initialzing gli `Slider` elementi perché la `Value` proprietà di `Slider` ha un valore `DefaultBindingMode` di `TwoWay` .

[![Schermata tripla dell'associazione inversa](images/ch16fg06-small.png "Associazione inversa")](images/ch16fg06-large.png#lightbox "Associazione inversa")

Questo è analogo al modo in cui vengono definite le associazioni in MVVM e si utilizzerà spesso questo tipo di associazione.

## <a name="string-formatting"></a>Formattazione di stringhe

Quando la proprietà di destinazione è di tipo `string` , è possibile usare la [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) proprietà definita da `BindingBase` per convertire l'origine in un oggetto `string` . Impostare la `StringFormat` proprietà su una stringa di formattazione .NET che verrà utilizzata con il [`String.Format`](xref:System.String.Format(System.String,System.Object)) formato statico per visualizzare l'oggetto. Quando si usa questa stringa di formattazione all'interno di un'estensione di markup, racchiuderla tra virgolette singole, in modo che le parentesi graffe non vengano confuse per un'estensione di markup incorporata.

Nell'esempio [**ShowViewValues**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ShowViewValues) viene illustrato come utilizzare `StringFormat` in XAML.

Nell'esempio [**WhatSizeBindings**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/WhatSizeBindings) viene illustrata la visualizzazione delle dimensioni della pagina con le associazioni `Width` alle `Height` proprietà e dell'oggetto `ContentPage` .

## <a name="why-is-it-called-path"></a>Perché è denominato "Path"?

La [`Path`](xref:Xamarin.Forms.Binding.Path) proprietà di `Binding` viene chiamata in quanto può essere una serie di proprietà e indicizzatori separati da punti. L'esempio [**BindingPathDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/BindingPathDemos) Mostra diversi esempi.

## <a name="binding-value-converters"></a>Convertitori di valori di binding

Quando le proprietà di origine e di destinazione di un'associazione sono tipi diversi, è possibile eseguire la conversione tra i tipi utilizzando un convertitore di associazione. Si tratta di una classe che implementa l' [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) interfaccia e contiene due metodi: [`Convert`](xref:Xamarin.Forms.IValueConverter.Convert(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) per convertire l'origine nella destinazione e [`ConvertBack`](xref:Xamarin.Forms.IValueConverter.ConvertBack(System.Object,System.Type,System.Object,System.Globalization.CultureInfo)) per convertire la destinazione nell'origine.

La [`IntToBoolConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/IntToBoolConverter.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) è un esempio per la conversione di un oggetto `int` in un oggetto `bool` . Viene illustrato dall'esempio [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/ButtonEnabler) , che Abilita solo `Button` se è stato digitato almeno un carattere in un oggetto `Entry` .

La [`BoolToStringConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToStringConverter.cs) classe converte un oggetto `bool` in un oggetto `string` e definisce due proprietà per specificare quale testo deve essere restituito per `false` `true` i valori e.
[`BoolToColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToColorConverter.cs)È simile. Nell'esempio [**SwitchText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/SwitchText) viene illustrato l'utilizzo di questi due convertitori per visualizzare testi diversi in colori diversi in base a un' `Switch` impostazione.

Il generico [`BoolToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BoolToObjectConverter.cs) può sostituire `BoolToStringConverter` e `BoolToColorConverter` e fungere da convertitore generalizzato `bool` a oggetto di qualsiasi tipo.

## <a name="bindings-and-custom-views"></a>Associazioni e visualizzazioni personalizzate

È possibile semplificare i controlli personalizzati utilizzando le associazioni dati. Il [`NewCheckBox.cs`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml.cs) file di codice definisce le `Text` `TextColor` proprietà,,, `FontSize` `FontAttributes` e `IsChecked` , ma non ha alcuna logica per gli oggetti visivi del controllo.
Il [`NewCheckBox.cs.xaml`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NewCheckBox.xaml) file contiene invece tutto il markup per gli oggetti visivi del controllo tramite Data Binding sugli `Label` elementi in base alle proprietà definite nel file code-behind.

L'esempio [**NewCheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16/NewCheckBoxDemo) illustra il `NewCheckBox` controllo personalizzato.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 16 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch16-Apr2016.pdf)
- [Capitolo 16 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter16)
- [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
