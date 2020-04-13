---
title: Riassunto del capitolo 15. L'interfaccia interattiva
description: "Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 15. L'interfaccia interattiva"
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 5f96d2f4b619bbb10bb58e9b1b5dc7007c1ce888
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291926"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Riassunto del capitolo 15. L'interfaccia interattiva

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

Questo capitolo esplora `View` otto derivati che consentono l'interazione con l'utente.

## <a name="view-overview"></a>Visualizza panoramica

Xamarin.Forms contiene 20 classi istanze `View` che `Layout`derivano da ma non . Sei di questi sono stati trattati nei capitoli precedenti:

- `Label`: [ **Capitolo 2. Anatomia di un'app**](chapter02.md)
- `BoxView`: [ **Capitolo 3. Scorrimento della pila**](chapter03.md)
- `Button`: [ **Capitolo 6. Clic sul pulsante**](chapter06.md)
- `Image`: [ **Capitolo 13. Bitmap**](chapter13.md)
- `ActivityIndicator`: [ **Capitolo 13. Bitmap**](chapter13.md)
- `ProgressBar`: [ **Capitolo 14. AbsoluteLayout (Layout assoluto)**](chapter14.md)

Le otto visualizzazioni in questo capitolo consentono in modo efficace all'utente di interagire con i tipi di dati .NET di base:The eight views in this chapter effectively allow the user to interact with basic .NET data types:

|Tipo di dati|Viste|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

È possibile considerare queste visualizzazioni come rappresentazioni interattive visive dei tipi di dati sottostanti. Questo concetto viene approfondito nel capitolo successivo, [**capitolo 16. Associazione dati**](chapter16.md).

Le restanti sei viste sono trattate nei seguenti capitoli:

- `WebView`: [ **Capitolo 16. Associazione dati**](chapter16.md)
- `Picker`: [ **Capitolo 19. Visualizzazioni raccolta**](chapter19.md)
- `ListView`: [ **Capitolo 19. Visualizzazioni raccolta**](chapter19.md)
- `TableView`: [ **Capitolo 19. Visualizzazioni raccolta**](chapter19.md)
- `Map`: [ **Capitolo 28. Posizione e mappe**](chapter28.md)
- `OpenGLView`: non trattato in questo libro (e nessun supporto per le piattaforme Windows)

## <a name="slider-and-stepper"></a>Dispositivo di scorrimento e stepper

Entrambi [`Slider`](xref:Xamarin.Forms.Slider) [`Stepper`](xref:Xamarin.Forms.Stepper) e consentono all'utente di scegliere un valore numerico da un intervallo. L'oggetto `Slider` è un `Stepper` intervallo continuo, mentre l'oggetto include valori discreti.

### <a name="slider-basics"></a>Nozioni di base sui cursori

Si [`Slider`](xref:Xamarin.Forms.Slider) tratta di una barra orizzontale che rappresenta un intervallo di valori da un minimo a sinistra a un massimo a destra. Definisce tre proprietà pubbliche:

- [`Value`](xref:Xamarin.Forms.Slider.Value)di `double`tipo , valore predefinito 0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum)di `double`tipo , valore predefinito 0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum)di `double`tipo , valore predefinito 1

Le proprietà associabili che eseguono il backup di queste proprietà garantiscono che siano coerenti:The bindable properties that back these properties ensure that they are consistent:

- Per tutte e [`coerceValue`](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate) tre le proprietà, il metodo `Value` specificato `Minimum` `Maximum`per la proprietà associabile garantisce che sia compreso tra e .
- Il [`validateValue`](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate) metodo `MinimumProperty` `false` on `Minimum` restituisce se viene impostato su `Maximum`un valore `MaximumProperty`maggiore o uguale a , e simile per . Il `false` ritorno `validateValue` dal metodo `ArgumentException` determina la sollatura di un oggetto .

`Slider`[`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) genera l'evento [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) con un `Value` argomento quando la proprietà viene modificata, a livello di codice o quando l'utente modifica l'oggetto `Slider`.

Nell'esempio [**SliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) viene illustrato `Slider`il semplice utilizzo di .

### <a name="common-pitfalls"></a>Inconvenienti comuni

Sia nel codice che `Minimum` in `Maximum` XAML, le proprietà e vengono impostate nell'ordine specificato. Assicurarsi di inizializzare `Maximum` queste proprietà `Minimum`in modo che sia sempre maggiore di . In caso contrario, viene generata un'eccezione.

L'inizializzazione delle `Slider` `Value` proprietà può causare `ValueChanged` la modifica della proprietà e la generato dell'evento. È necessario assicurarsi che il `Slider` gestore eventi non acceda alle visualizzazioni che non sono ancora state create durante l'inizializzazione della pagina.

L'evento `ValueChanged` non viene `Slider` generato `Value` durante l'inizializzazione a meno che la proprietà non venga modificata. È possibile `ValueChanged` chiamare il gestore direttamente dal codice.

### <a name="slider-color-selection"></a>Selezione colore dispositivo di scorrimento

Il programma [**RgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) contiene tre `Slider` elementi che consentono di selezionare in modo interattivo un colore specificandone i valori RGB:

[![Triplo screenshot dei cursori R G B](images/ch15fg03-small.png "Cursori RGB")](images/ch15fg03-large.png#lightbox "Cursori RGB")

Nell'esempio [**TextFade**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) vengono utilizzati due `Slider` elementi per spostare due `Label` elementi in un `AbsoluteLayout` oggetto e sfumare uno nell'altro.

### <a name="the-stepper-difference"></a>La differenza Stepper

[`Stepper`](xref:Xamarin.Forms.Stepper) L'oggetto definisce le `Slider` stesse `Maximum` proprietà e gli stessi eventi, ma la proprietà viene inizializzata su 100 e definisce una quarta proprietà:The defines the same properties and events as but the property is initialized to 100 and `Stepper` defines a fourth property:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment)di `double`tipo , inizializzato su 1

Visivamente, il `Stepper` è costituito **&ndash;** da **+** due pulsanti etichettati e . Premendo **&ndash;** `Value` diminuisce `Increment` fino a `Minimum`un minimo di . Premendo **+** `Value` aumenta `Increment` fino a `Maximum`un massimo di .

Ciò è illustrato dal [**StepperDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) esempio.

## <a name="switch-and-checkbox"></a>Switch e CheckBox

L'oggetto [`Switch`](xref:Xamarin.Forms.Switch) consente all'utente di specificare un valore booleano.

### <a name="switch-basics"></a>Nozioni di base sulle commutazioni

Visivamente, il `Switch` è costituito da un interruttore che può essere spento e acceso. La classe definisce una proprietà:The class defines one property:

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)di tipo`bool`

`Switch`definisce un evento:

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled)accompagnato da [`ToggledEventArgs`](xref:Xamarin.Forms.ToggledEventArgs) un oggetto, `IsToggled` generato quando la proprietà cambia.

Il programma [**SwitchDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) illustra l'oggetto `Switch`.

### <a name="a-traditional-checkbox"></a>Un CheckBox tradizionale

Alcuni sviluppatori potrebbero preferire `CheckBox` un `Switch`più tradizionale al . La libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene una `CheckBox` `ContentView`classe che deriva da . `CheckBox`viene implementato dai file [CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) e [CheckBox.xaml.cs.](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) `CheckBox`definisce tre`Text`proprietà `FontSize`( `IsChecked`, `CheckedChanged` , e ) e un evento.

[**Nell'esempio CheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) viene illustrato questo `CheckBox`oggetto .

## <a name="typing-text"></a>Digitazione di testo

Xamarin.Forms definisce tre visualizzazioni che consentono all'utente di immettere e modificare il testo:

- [`Entry`](xref:Xamarin.Forms.Entry)per una singola riga di testo
- [`Editor`](xref:Xamarin.Forms.Editor)per più righe di testo
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)per una singola riga di testo a scopo di ricerca.

`Entry`e `Editor` derivano da [`InputView`](xref:Xamarin.Forms.InputView), `View`che deriva da . `SearchBar`deriva direttamente `View`da .

### <a name="keyboard-and-focus"></a>Tastiera e messa a fuoco

Su telefoni e tablet senza tastiere fisiche, gli `Entry`elementi , `Editor`e `SearchBar` fanno apparire una tastiera virtuale. La presenza di questa tastiera sullo schermo è correlata allo stato attivo per l'input. Una visualizzazione deve [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) avere [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) entrambe `true` le proprietà e impostate su per ottenere lo stato attivo per l'input.

Due metodi, una proprietà di sola lettura e due eventi sono coinvolti con lo stato attivo per l'input. Questi sono tutti `VisualElement`definiti da:

- Il [`Focus`](xref:Xamarin.Forms.VisualElement.Focus) metodo tenta di impostare lo `true` stato attivo per l'input su un elemento e restituisce se ha esito positivo
- Il [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus) metodo rimuove lo stato attivo per l'input da un elemento
- La [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) proprietà di sola lettura indica se l'elemento ha lo stato attivo per l'input
- L'evento [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) indica quando un elemento ottiene lo stato attivo per l'input
- L'evento [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) indica quando un elemento perde lo stato attivo per l'input

### <a name="choosing-the-keyboard"></a>Scelta della tastiera

La [`InputView`](xref:Xamarin.Forms.InputView) classe `Entry` da `Editor` cui e derivano definisce una sola proprietà:The class from which and derive defines only one property:

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard)di tipo[`Keyboard`](xref:Xamarin.Forms.Keyboard)

Indica il tipo di tastiera visualizzata. Alcune tastiere sono ottimizzate per URI o numeri.

La `Keyboard` classe consente di definire [`Keyboard.Create`](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags)) una tastiera con [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)un metodo statico con un argomento di tipo , un'enumerazione con i seguenti flag di bit:

- `None`impostato su 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence)impostato su 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck)impostato su 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions)impostato su 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All)impostato su xFFFFFFFF

Quando si utilizza [`Editor`](xref:Xamarin.Forms.Editor) la multilinea quando è previsto `Keyboard.Create` un paragrafo o più di testo, la chiamata è un buon approccio alla selezione di una tastiera. Per la riga [`Entry`](xref:Xamarin.Forms.Entry)singola , sono utili `Keyboard` le seguenti proprietà statiche di sola lettura di:

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric)per i numeri positivi con o senza separatore decimale.

Il [`KeyboardTypeConverter`](xref:Xamarin.Forms.KeyboardTypeConverter) consente di specificare queste proprietà in XAML come dimostrato dal [**entryKeyboards**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) programma.

### <a name="entry-properties-and-events"></a>Proprietà ed eventi della voce

La riga [`Entry`](xref:Xamarin.Forms.Entry) singola definisce le seguenti proprietà:

- [`Text`](xref:Xamarin.Forms.InputView.Text)digitare `string`, il testo visualizzato nella casella`Entry`
- [`TextColor`](xref:Xamarin.Forms.InputView.TextColor)di tipo`Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily)di tipo`string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize)di tipo`double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes)di tipo`FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword)di `bool`tipo , che fa sì che i caratteri vengano mascherati
- [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)di `string`tipo , per il testo di `Entry` colore dimora che appare nel prima che venga digitato qualsiasi cosa
- [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)di tipo`Color`

Il `Entry` definisce anche due eventi:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)con [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) un oggetto, `Text` generato ogni volta che la proprietà cambia
- [`Completed`](xref:Xamarin.Forms.Entry.Completed), generato quando l'utente è terminato e la tastiera viene chiusa. L'utente indica il completamento in modo specifico della piattaforma

[**Nell'esempio QuadraticEquations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) vengono illustrati questi due eventi.

### <a name="the-editor-difference"></a>La differenza Editor

[`Editor`](xref:Xamarin.Forms.Editor) La multilinea definisce `Text` `Font` la `Entry` stessa e le proprietà come ma non le altre proprietà. `Editor`definisce anche le stesse `Entry`due proprietà di .

[**JustNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) è un programma di prendere appunti in formato libero `Editor`che salva e ripristina il contenuto del file .

### <a name="the-searchbar"></a>Il SearchBar

Non [`SearchBar`](xref:Xamarin.Forms.SearchBar) deriva da `InputView`, pertanto non `Keyboard` dispone di una proprietà . Ma ha tutte `Text`le `Font`proprietà `Placeholder` , `Entry` e che definisce. Inoltre, `SearchBar` definisce tre proprietà aggiuntive:

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)di tipo`Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)tipo [`ICommand`](xref:System.Windows.Input.ICommand) da utilizzare con associazioni dati e MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)di `Object`tipo , da utilizzare con`SearchCommand`

Il pulsante di annullamento specifico della piattaforma cancella il testo. Il `SearchBar` ha anche un pulsante di ricerca specifico della piattaforma. Premendo uno di questi pulsanti si `SearchBar` genera uno dei due eventi che definisce:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)accompagnato da [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) un oggetto
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

[**Nell'esempio SearchBarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) viene illustrato l'utilizzo di `SearchBar`.

## <a name="date-and-time-selection"></a>Selezione di data e ora

Le [`DatePicker`](xref:Xamarin.Forms.DatePicker) [`TimePicker`](xref:Xamarin.Forms.TimePicker) visualizzazioni e implementano controlli specifici della piattaforma che consentono all'utente di specificare una data o un'ora.

### <a name="the-datepicker"></a>Il controllo DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker)definisce quattro proprietà:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate)di `DateTime`tipo , inizializzato al 1 gennaio 1900
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate)di `DateTime`tipo , inizializzato al 31 dicembre 2100
- [`Date`](xref:Xamarin.Forms.DatePicker.Date)di `DateTime`tipo , inizializzato su`DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format)di `string`tipo , la stringa di formattazione .NET inizializzata su "d", il modello di data breve, risultante in una visualizzazione della data come "7/20/1969" negli Stati Uniti.

È possibile `DateTime` impostare le proprietà in XAML esprimendo le proprietà come elementi proprietà e usando il formato di data breve invariante delle impostazioni cultura ("7/20/1969").   

Nell'esempio [**DaysBetweenDates**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) viene calcolato il numero di giorni tra due date selezionate dall'utente.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>Il TimePicker (o è un TimeSpanPicker?)

[`TimePicker`](xref:Xamarin.Forms.TimePicker)definisce due proprietà e nessun evento:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time)è di `TimeSpan` tipo `DateTime`anziché , indicando il tempo trascorso dalla mezzanotte
- [`Format`](xref:Xamarin.Forms.TimePicker.Format)di `string`tipo , la stringa di formattazione .NET inizializzata su "t", il modello di tempo breve, con conseguente visualizzazione dell'ora come "1:45 PM" negli Stati Uniti.

Il programma [**SetTimer**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) viene `TimePicker` illustrato come utilizzare il per specificare un'ora per un timer. Il programma funziona solo se lo si mantiene in primo piano.

**SetTimer** illustra inoltre [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) l'utilizzo del metodo di visualizzazione di una casella di `Page` avviso.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 15 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Esempi del capitolo 15](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Dispositivo di scorrimento](~/xamarin-forms/user-interface/slider.md)
- [Voce](~/xamarin-forms/user-interface/text/entry.md)
- [Editor](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
