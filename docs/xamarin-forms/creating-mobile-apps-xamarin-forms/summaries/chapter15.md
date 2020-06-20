---
title: Riepilogo del capitolo 15. Interfaccia interattiva
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 15. Interfaccia interattiva'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9bb930c2d0e4b6281b1aa48589ea5245eedc1a60
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136747"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Riepilogo del capitolo 15. Interfaccia interattiva

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

Questo capitolo esamina otto `View` derivati che consentono l'interazione con l'utente.

## <a name="view-overview"></a>Visualizza panoramica

Xamarin.Formscontiene 20 classi istanziabile che derivano da `View` ma non `Layout` . Sei di questi sono stati trattati nei precedenti capitoli:

- `Label`: [ **Capitolo 2. Anatomia di un'app**](chapter02.md)
- `BoxView`: [ **Capitolo 3. Scorrimento dello stack**](chapter03.md)
- `Button`: [ **Capitolo 6. Clic sui pulsanti**](chapter06.md)
- `Image`: [ **Capitolo 13. Bitmap**](chapter13.md)
- `ActivityIndicator`: [ **Capitolo 13. Bitmap**](chapter13.md)
- `ProgressBar`: [ **Capitolo 14. AbsoluteLayout**](chapter14.md)

Le otto visualizzazioni di questo capitolo consentono di interagire efficacemente con i tipi di dati .NET di base:

|Tipo di dati|Viste|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

È possibile considerare queste viste come rappresentazioni visive interattive dei tipi di dati sottostanti. Questo concetto viene approfondito più avanti nel capitolo [**16. Data Binding**](chapter16.md).

Le restanti sei visualizzazioni sono descritte nei capitoli seguenti:

- `WebView`: [ **Capitolo 16. Data Binding**](chapter16.md)
- `Picker`: [ **Capitolo 19. Viste di raccolta**](chapter19.md)
- `ListView`: [ **Capitolo 19. Viste di raccolta**](chapter19.md)
- `TableView`: [ **Capitolo 19. Viste di raccolta**](chapter19.md)
- `Map`: [ **Capitolo 28. Località e mappe**](chapter28.md)
- `OpenGLView`: Non trattato in questo libro (e nessun supporto per le piattaforme Windows)

## <a name="slider-and-stepper"></a>Dispositivo di scorrimento e stepper

Sia [`Slider`](xref:Xamarin.Forms.Slider) che [`Stepper`](xref:Xamarin.Forms.Stepper) consentono all'utente di scegliere un valore numerico da un intervallo. `Slider`È un intervallo continuo mentre l'oggetto `Stepper` prevede valori discreti.

### <a name="slider-basics"></a>Nozioni fondamentali sul dispositivo di scorrimento

[`Slider`](xref:Xamarin.Forms.Slider)È una barra orizzontale che rappresenta un intervallo di valori da un minimo a sinistra a un massimo a destra. Definisce tre proprietà pubbliche:

- [`Value`](xref:Xamarin.Forms.Slider.Value)di tipo `double` , valore predefinito 0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum)di tipo `double` , valore predefinito 0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum)di tipo `double` , valore predefinito 1

Le proprietà associabili che eseguono il backup di queste proprietà garantiscono che siano coerenti:

- Per tutte e tre le proprietà, il [`coerceValue`](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate) metodo specificato per la proprietà associabile garantisce che `Value` sia compreso tra `Minimum` e `Maximum` .
- Il [`validateValue`](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate) metodo su `MinimumProperty` restituisce `false` se `Minimum` viene impostato su un valore maggiore o uguale a `Maximum` e analogo a `MaximumProperty` . La restituzione `false` dal `validateValue` metodo comporta la `ArgumentException` generazione di un oggetto.

`Slider`genera l' [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) evento con un [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) argomento quando la `Value` proprietà viene modificata a livello di codice o quando l'utente modifica l'oggetto `Slider` .

Nell'esempio [**SliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) viene illustrato l'utilizzo semplice di `Slider` .

### <a name="common-pitfalls"></a>Inconvenienti comuni

Sia nel codice che in XAML, le `Minimum` `Maximum` proprietà e vengono impostate nell'ordine specificato. Assicurarsi di inizializzare queste proprietà in modo che `Maximum` sia sempre maggiore di `Minimum` . In caso contrario, viene generata un'eccezione.

L'inizializzazione delle proprietà può comportare `Slider` la `Value` modifica della proprietà e l' `ValueChanged` evento da generare. È necessario assicurarsi che il `Slider` gestore eventi non acceda alle viste che non sono ancora state create durante l'inizializzazione della pagina.

L' `ValueChanged` evento non viene attivato durante l' `Slider` inizializzazione, a meno che la proprietà non venga `Value` modificata. È possibile chiamare il `ValueChanged` gestore direttamente dal codice.

### <a name="slider-color-selection"></a>Selezione colore dispositivo di scorrimento

Il programma [**RgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) contiene tre `Slider` elementi che consentono di selezionare in modo interattivo un colore specificando i relativi valori RGB:

[![Schermata tripla dei dispositivi di scorrimento R G B](images/ch15fg03-small.png "Dispositivi di scorrimento RGB")](images/ch15fg03-large.png#lightbox "Dispositivi di scorrimento RGB")

L'esempio [**TextFade**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) usa due `Slider` elementi per spostare due `Label` elementi in un oggetto `AbsoluteLayout` e dissolverne uno nell'altro.

### <a name="the-stepper-difference"></a>Differenza di stepper

[`Stepper`](xref:Xamarin.Forms.Stepper)Definisce le stesse proprietà ed eventi di, `Slider` ma la `Maximum` proprietà viene inizializzata su 100 e `Stepper` definisce una quarta proprietà:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment)di tipo `double` , inizializzato su 1

Visivamente, `Stepper` è costituito da due pulsanti **&ndash;** con etichetta e **+** . **&ndash;** La pressione diminuisce di `Value` `Increment` fino a un minimo di `Minimum` . La pressione di **+** aumenta di `Value` `Increment` fino a un massimo di `Maximum` .

Questa operazione è illustrata nell'esempio [**StepperDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) .

## <a name="switch-and-checkbox"></a>Opzione e casella di controllo

[`Switch`](xref:Xamarin.Forms.Switch)Consente all'utente di specificare un valore booleano.

### <a name="switch-basics"></a>Nozioni fondamentali su switch

Visivamente, `Switch` è costituito da un interruttore che può essere disattivato e attivato. La classe definisce una proprietà:

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled)di tipo`bool`

`Switch`definisce un evento:

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled)accompagnato da un [`ToggledEventArgs`](xref:Xamarin.Forms.ToggledEventArgs) oggetto, generato quando la `IsToggled` proprietà viene modificata.

Il programma [**SwitchDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) illustra il `Switch` .

### <a name="a-traditional-checkbox"></a>Casella di controllo tradizionale

Alcuni sviluppatori potrebbero preferire un più tradizionale `CheckBox` a `Switch` . La libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene una `CheckBox` classe che deriva da `ContentView` . `CheckBox`viene implementato dai file [CheckBox. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) e [CheckBox.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) . `CheckBox`definisce tre proprietà ( `Text` , `FontSize` e `IsChecked` ) e un `CheckedChanged` evento.

Questa operazione è illustrata nell'esempio [**CheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) `CheckBox` .

## <a name="typing-text"></a>Digitazione di testo

Xamarin.Formsdefinisce tre visualizzazioni che consentono all'utente di immettere e modificare il testo:

- [`Entry`](xref:Xamarin.Forms.Entry)per una singola riga di testo
- [`Editor`](xref:Xamarin.Forms.Editor)per più righe di testo
- [`SearchBar`](xref:Xamarin.Forms.SearchBar)per una singola riga di testo per scopi di ricerca.

`Entry`e `Editor` derivano da [`InputView`](xref:Xamarin.Forms.InputView) , che deriva da `View` . `SearchBar`deriva direttamente da `View` .

### <a name="keyboard-and-focus"></a>Tastiera e stato attivo

Sui telefoni e sui tablet senza tastiere fisici, gli elementi, e comportano la `Entry` `Editor` `SearchBar` comparsa di una tastiera virtuale. La presenza di questa tastiera sullo schermo è correlata allo stato attivo per l'input. Una vista deve avere entrambe le [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) proprietà e impostate su `true` per ottenere lo stato attivo per l'input.

Due metodi, una proprietà di sola lettura e due eventi sono interessati dallo stato attivo per l'input. Sono tutti definiti da `VisualElement` :

- Il [`Focus`](xref:Xamarin.Forms.VisualElement.Focus) metodo tenta di impostare lo stato attivo per l'input su un elemento e restituisce `true` se ha esito positivo
- Il [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus) metodo rimuove lo stato attivo per l'input da un elemento
- La [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) proprietà di sola lettura indica se l'elemento ha lo stato attivo per l'input
- L' [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) evento indica quando un elemento ottiene lo stato attivo per l'input
- L' [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) evento indica quando un elemento perde lo stato attivo per l'input

### <a name="choosing-the-keyboard"></a>Scelta della tastiera

La [`InputView`](xref:Xamarin.Forms.InputView) classe da cui `Entry` e `Editor` derivano definisce solo una proprietà:

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard)di tipo[`Keyboard`](xref:Xamarin.Forms.Keyboard)

Indica il tipo di tastiera visualizzato. Alcune tastiere sono ottimizzate per URI o numeri.

La `Keyboard` classe consente di definire una tastiera con un [ `Keyboard.Create` ] statico (xrif: Xamarin.Forms . Keyboard. Create ( Xamarin.Forms . KeyboardFlags)) con un argomento di tipo [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) , un'enumerazione con i flag di bit seguenti:

- `None`imposta su 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence)imposta su 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck)imposta su 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions)imposta su 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All)imposta su \xFFFFFFFF

Quando si utilizza la riga a [`Editor`](xref:Xamarin.Forms.Editor) più righe quando è previsto un paragrafo o più testo, la chiamata a `Keyboard.Create` è un metodo efficace per la selezione di una tastiera. Per la singola riga [`Entry`](xref:Xamarin.Forms.Entry) , le seguenti proprietà statiche di sola lettura di `Keyboard` sono utili:

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric)per i numeri positivi con o senza un separatore decimale.

[`KeyboardTypeConverter`](xref:Xamarin.Forms.KeyboardTypeConverter)Consente di specificare queste proprietà in XAML come dimostrato dal programma [**EntryKeyboards**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) .

### <a name="entry-properties-and-events"></a>Proprietà ed eventi di immissione

La riga singola [`Entry`](xref:Xamarin.Forms.Entry) definisce le proprietà seguenti:

- [`Text`](xref:Xamarin.Forms.InputView.Text)di tipo `string` , il testo visualizzato nel`Entry`
- [`TextColor`](xref:Xamarin.Forms.InputView.TextColor)di tipo`Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily)di tipo`string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize)di tipo`double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes)di tipo`FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword)di tipo `bool` , che fa sì che i caratteri vengano mascherati
- [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder)di tipo `string` , per testo debolmente colorato visualizzato in prima che `Entry` qualsiasi elemento sia tipizzato
- [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor)di tipo`Color`

`Entry`Definisce inoltre due eventi:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)con un [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) oggetto, generato ogni volta che la `Text` proprietà viene modificata
- [`Completed`](xref:Xamarin.Forms.Entry.Completed), generato al termine dell'utente e la tastiera viene rilasciata. L'utente indica il completamento in modo specifico della piattaforma

L'esempio [**QuadraticEquations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) illustra questi due eventi.

### <a name="the-editor-difference"></a>Differenza dell'editor

Il multiriga [`Editor`](xref:Xamarin.Forms.Editor) definisce le `Text` stesse `Font` proprietà e come, `Entry` ma non le altre proprietà. `Editor`definisce inoltre le stesse due proprietà di `Entry` .

[**JustNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) è un programma per la creazione di note in formato libero che consente di salvare e ripristinare il contenuto del `Editor` .

### <a name="the-searchbar"></a>Oggetto SearchBar

Non [`SearchBar`](xref:Xamarin.Forms.SearchBar) deriva da `InputView` , quindi non dispone di una `Keyboard` Proprietà. Ma ha tutte le `Text` `Font` proprietà, e `Placeholder` che `Entry` definisce. Inoltre, `SearchBar` definisce tre proprietà aggiuntive:

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor)di tipo`Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand)di tipo [`ICommand`](xref:System.Windows.Input.ICommand) da usare con le associazioni dati e MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter)di tipo `Object` , da utilizzare con`SearchCommand`

Il pulsante Annulla specifico della piattaforma Cancella il testo. Il `SearchBar` dispone anche di un pulsante di ricerca specifico della piattaforma. Se si preme uno di questi pulsanti, viene generato uno dei due eventi che `SearchBar` definisce:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)accompagnato da un [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) oggetto
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

Nell'esempio [**SearchBarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) viene illustrato `SearchBar` .

## <a name="date-and-time-selection"></a>Selezione data e ora

Le [`DatePicker`](xref:Xamarin.Forms.DatePicker) [`TimePicker`](xref:Xamarin.Forms.TimePicker) visualizzazioni e implementano controlli specifici della piattaforma che consentono all'utente di specificare una data o un'ora.

### <a name="the-datepicker"></a>DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker)definisce quattro proprietà:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate)di tipo `DateTime` , inizializzato al 1 ° gennaio 1900
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate)di tipo `DateTime` , inizializzato al 31 dicembre 2100
- [`Date`](xref:Xamarin.Forms.DatePicker.Date)di tipo `DateTime` , inizializzato su`DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format)di tipo `string` , la stringa di formattazione .NET inizializzata su "d", il modello di data breve, con conseguente visualizzazione di una data simile a "7/20/1969" negli Stati Uniti.

È possibile impostare le `DateTime` Proprietà in XAML esprimendo le proprietà come elementi della proprietà e utilizzando il formato di data breve ("7/20/1969") indipendente dalle impostazioni cultura.   

L'esempio [**DaysBetweenDates**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) calcola il numero di giorni tra due date selezionate dall'utente.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>Il TimePicker (o è un TimeSpanPicker?)

[`TimePicker`](xref:Xamarin.Forms.TimePicker)definisce due proprietà e nessun evento:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time)è di tipo `TimeSpan` anziché `DateTime` , che indica l'intervallo di tempo trascorso dalla mezzanotte
- [`Format`](xref:Xamarin.Forms.TimePicker.Format)di tipo `string` , la stringa di formattazione .NET inizializzata su "t", il modello di ora breve, con conseguente visualizzazione dell'ora come "1:45 PM" negli Stati Uniti.

In [**questo programma viene**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) illustrato come utilizzare `TimePicker` per specificare un'ora per un timer. Il programma funziona solo se lo si mantiene in primo piano.

**SetTimer** [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) Per la visualizzazione di una casella di avviso, viene inoltre illustrato l'utilizzo del metodo di `Page` .

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 15 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Esempi del capitolo 15](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Dispositivo di scorrimento](~/xamarin-forms/user-interface/slider.md)
- [Voce](~/xamarin-forms/user-interface/text/entry.md)
- [Editor](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
