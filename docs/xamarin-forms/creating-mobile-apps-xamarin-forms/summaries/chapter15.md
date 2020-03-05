---
title: Riepilogo del capitolo 15. L'interfaccia interattiva
description: "Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 15. L'interfaccia interattiva"
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 5f96d2f4b619bbb10bb58e9b1b5dc7007c1ce888
ms.sourcegitcommit: ccbf914615c0ce6b3f308d930f7a77418aeb4dbc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "78291926"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Riepilogo del capitolo 15. L'interfaccia interattiva

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

Questo capitolo esamina otto derivati `View` che consentono l'interazione con l'utente.

## <a name="view-overview"></a>Panoramica di View

Novell. Forms contiene 20 classi istanziabile che derivano da `View` ma non `Layout`. Sei di queste sono state trattate nei precedenti capitoli:

- `Label`: [ **capitolo 2. Anatomia di un'app**](chapter02.md)
- `BoxView`: [ **capitolo 3. Scorrimento dello stack**](chapter03.md)
- `Button`: [ **capitolo 6. Clic sui pulsanti**](chapter06.md)
- `Image`: [ **capitolo 13. Bitmap**](chapter13.md)
- `ActivityIndicator`: [ **capitolo 13. Bitmap**](chapter13.md)
- `ProgressBar`: [ **capitolo 14. AbsoluteLayout**](chapter14.md)

Le otto viste in questo capitolo consentono in modo efficace l'utente può interagire con tipi di dati .NET di base:

|Tipo di dati|Viste|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

È possibile considerare queste viste come rappresentazioni visive di interattive dei tipi di dati sottostante. Questo concetto viene approfondito più avanti nel capitolo [**16. Data Binding**](chapter16.md).

Le viste di sei rimanenti vengono trattate nei capitoli seguenti:

- `WebView`: [ **capitolo 16. Data Binding**](chapter16.md)
- `Picker`: [ **capitolo 19. Viste di raccolta**](chapter19.md)
- `ListView`: [ **capitolo 19. Viste di raccolta**](chapter19.md)
- `TableView`: [ **capitolo 19. Viste di raccolta**](chapter19.md)
- `Map`: [ **capitolo 28. Località e mappe**](chapter28.md)
- `OpenGLView`: non trattato in questo libro (e nessun supporto per le piattaforme Windows)

## <a name="slider-and-stepper"></a>Dispositivo di scorrimento e gestore di istruzioni

Sia [`Slider`](xref:Xamarin.Forms.Slider) che [`Stepper`](xref:Xamarin.Forms.Stepper) consentono all'utente di scegliere un valore numerico da un intervallo. Il `Slider` è un intervallo continuo mentre l'`Stepper` comporta valori discreti.

### <a name="slider-basics"></a>Nozioni di base di dispositivo di scorrimento

Il [`Slider`](xref:Xamarin.Forms.Slider) è una barra orizzontale che rappresenta un intervallo di valori da un minimo a sinistra a un massimo a destra. Definisce tre proprietà pubbliche:

- [`Value`](xref:Xamarin.Forms.Slider.Value) di tipo `double`, valore predefinito 0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) di tipo `double`, valore predefinito 0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) di tipo `double`, valore predefinito 1

Le proprietà associabili che supportano queste proprietà verificare che siano coerenti con:

- Per tutte e tre le proprietà, il metodo [`coerceValue`](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate) specificato per la proprietà associabile garantisce che `Value` sia compreso tra `Minimum` e `Maximum`.
- Il metodo [`validateValue`](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate) su `MinimumProperty` restituisce `false` se `Minimum` viene impostato su un valore maggiore o uguale a `Maximum`e simile per `MaximumProperty`. La restituzione di `false` dal metodo `validateValue` causa la generazione di un `ArgumentException`.

`Slider` genera l'evento [`ValueChanged`](xref:Xamarin.Forms.Slider.ValueChanged) con un argomento [`ValueChangedEventArgs`](xref:Xamarin.Forms.ValueChangedEventArgs) quando la proprietà `Value` viene modificata a livello di codice o quando l'utente modifica la `Slider`.

Nell'esempio [**SliderDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) viene illustrato l'utilizzo semplice del `Slider`.

### <a name="common-pitfalls"></a>Inconvenienti comuni

Sia nel codice che in XAML, le proprietà `Minimum` e `Maximum` sono impostate nell'ordine specificato. Assicurarsi di inizializzare queste proprietà in modo che `Maximum` sia sempre maggiore di `Minimum`. In caso contrario, viene generata un'eccezione.

L'inizializzazione delle proprietà di `Slider` può comportare la modifica della proprietà `Value` e l'evento `ValueChanged` da generare. È necessario assicurarsi che il gestore dell'evento `Slider` non acceda alle viste che non sono ancora state create durante l'inizializzazione della pagina.

L'evento `ValueChanged` non viene attivato durante `Slider` inizializzazione, a meno che la proprietà `Value` non venga modificata. È possibile chiamare il gestore di `ValueChanged` direttamente dal codice.

### <a name="slider-color-selection"></a>Selezione di colore dispositivo di scorrimento

Il programma [**RgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) contiene tre elementi `Slider` che consentono di selezionare in modo interattivo un colore specificando i relativi valori RGB:

[![Schermata tripla dei dispositivi di scorrimento R G B](images/ch15fg03-small.png "Dispositivi di scorrimento RGB")](images/ch15fg03-large.png#lightbox "Dispositivi di scorrimento RGB")

L'esempio [**TextFade**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) usa due elementi `Slider` per spostare due elementi `Label` in un `AbsoluteLayout` e dissolverne uno nell'altro.

### <a name="the-stepper-difference"></a>La differenza di gestore di istruzioni

Il [`Stepper`](xref:Xamarin.Forms.Stepper) definisce le stesse proprietà ed eventi di `Slider`, ma la proprietà `Maximum` viene inizializzata su 100 e `Stepper` definisce una quarta proprietà:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) di tipo `double`, inizializzato su 1

Visivamente, il `Stepper` è costituito da due pulsanti con etichetta **&ndash;** e **+** . Se si preme **&ndash;** si diminuisce `Value` per `Increment` fino a un minimo di `Minimum`. La pressione di **+** aumenta `Value` per `Increment` fino a un massimo di `Maximum`.

Questa operazione è illustrata nell'esempio [**StepperDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) .

## <a name="switch-and-checkbox"></a>Commutatore e casella di controllo

Il [`Switch`](xref:Xamarin.Forms.Switch) consente all'utente di specificare un valore booleano.

### <a name="switch-basics"></a>Nozioni di base commutatore

Visivamente, il `Switch` è costituito da un interruttore che può essere disattivato e attivato. La classe definisce una proprietà:

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) di tipo `bool`

`Switch` definisce un evento:

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) accompagnato da un oggetto [`ToggledEventArgs`](xref:Xamarin.Forms.ToggledEventArgs) , generato quando viene modificata la proprietà `IsToggled`.

Il programma [**SwitchDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) illustra il `Switch`.

### <a name="a-traditional-checkbox"></a>Una casella di controllo tradizionale

Alcuni sviluppatori potrebbero preferire un `CheckBox` più tradizionale al `Switch`. La libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contiene una classe `CheckBox` che deriva da `ContentView`. `CheckBox` viene implementato dai file [CheckBox. XAML](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) e [CheckBox.XAML.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) . `CheckBox` definisce tre proprietà (`Text`, `FontSize`e `IsChecked`) e un evento `CheckedChanged`.

Questo `CheckBox`viene illustrato nell'esempio [**CheckBoxDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) .

## <a name="typing-text"></a>Digitare un testo

Xamarin. Forms definisce tre visualizzazioni che consentono di immettere e modificare il testo:

- [`Entry`](xref:Xamarin.Forms.Entry) per una singola riga di testo
- [`Editor`](xref:Xamarin.Forms.Editor) per più righe di testo
- [`SearchBar`](xref:Xamarin.Forms.SearchBar) per una singola riga di testo per scopi di ricerca.

`Entry` e `Editor` derivano da [`InputView`](xref:Xamarin.Forms.InputView)che deriva da `View`. `SearchBar` deriva direttamente da `View`.

### <a name="keyboard-and-focus"></a>Tastiera e lo stato attivo

Su telefoni e tablet senza tastiere fisici, gli elementi `Entry`, `Editor`e `SearchBar` comportano la comparsa di una tastiera virtuale. La presenza di questo tipo di tastiera su schermo è correlata a uno stato attivo. Per ottenere lo stato attivo per l'input, è necessario che le proprietà [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) e [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) di una vista siano impostate su `true`.

Con stato attivo di input sono coinvolti due metodi, proprietà di sola lettura una e due eventi. Tutti definiti da `VisualElement`:

- Il metodo [`Focus`](xref:Xamarin.Forms.VisualElement.Focus) tenta di impostare lo stato attivo per l'input su un elemento e restituisce `true` in caso di esito positivo
- Il metodo [`Unfocus`](xref:Xamarin.Forms.VisualElement.Unfocus) rimuove lo stato attivo per l'input da un elemento
- La proprietà di sola lettura [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) indica se l'elemento ha lo stato attivo per l'input
- L'evento [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) indica quando un elemento riceve lo stato attivo per l'input
- L'evento [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) indica quando un elemento perde lo stato attivo per l'input

### <a name="choosing-the-keyboard"></a>Scelta della tastiera

La classe [`InputView`](xref:Xamarin.Forms.InputView) da cui derivano `Entry` e `Editor` definisce solo una proprietà:

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) di tipo [`Keyboard`](xref:Xamarin.Forms.Keyboard)

Indica il tipo di tastiera che viene visualizzato. Alcune tastiere sono ottimizzate per i numeri o URI.

La classe `Keyboard` consente di definire una tastiera con un metodo di [`Keyboard.Create`](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags)) statico con un argomento di tipo [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags), un'enumerazione con i flag di bit seguenti:

- `None` impostato su 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) impostato su 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) impostato su 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) impostato su 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) impostato su \xFFFFFFFF

Quando si usa il [`Editor`](xref:Xamarin.Forms.Editor) su più righe quando è previsto un paragrafo o più testo, la chiamata `Keyboard.Create` è un approccio efficace per la selezione di una tastiera. Per la [`Entry`](xref:Xamarin.Forms.Entry)a riga singola, le seguenti proprietà statiche di sola lettura di `Keyboard` sono utili:

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) per i numeri positivi con o senza una virgola decimale.

Il [`KeyboardTypeConverter`](xref:Xamarin.Forms.KeyboardTypeConverter) consente di specificare queste proprietà in XAML come dimostrato dal programma [**EntryKeyboards**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) .

### <a name="entry-properties-and-events"></a>Voce proprietà ed eventi

Il [`Entry`](xref:Xamarin.Forms.Entry) a riga singola definisce le proprietà seguenti:

- [`Text`](xref:Xamarin.Forms.InputView.Text) di tipo `string`, il testo visualizzato nella `Entry`
- [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) di tipo `Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily) di tipo `string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize) di tipo `double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes) di tipo `FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) di tipo `bool`, che fa sì che i caratteri vengano mascherati
- [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) di tipo `string`, per un testo poco colorato visualizzato nel `Entry` prima che sia stato digitato qualsiasi elemento
- [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) di tipo `Color`

Il `Entry` definisce anche due eventi:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) con un oggetto [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs) , generato ogni volta che viene modificata la proprietà `Text`
- [`Completed`](xref:Xamarin.Forms.Entry.Completed), generato al termine dell'utente e la tastiera viene rilasciata. L'utente indica il completamento in modo specifico della piattaforma

L'esempio [**QuadraticEquations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) illustra questi due eventi.

### <a name="the-editor-difference"></a>La differenza di Editor

Il [`Editor`](xref:Xamarin.Forms.Editor) su più righe definisce le stesse proprietà `Text` e `Font` di `Entry`, ma non le altre proprietà. `Editor` definisce anche le stesse due proprietà di `Entry`.

[**JustNotes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) è un programma per la creazione di note in formato libero che consente di salvare e ripristinare il contenuto del `Editor`.

### <a name="the-searchbar"></a>Il SearchBar

Il [`SearchBar`](xref:Xamarin.Forms.SearchBar) non deriva da `InputView`, quindi non dispone di una proprietà `Keyboard`. Ma ha tutte le proprietà `Text`, `Font`e `Placeholder` che `Entry` definisce. Inoltre, `SearchBar` definisce tre proprietà aggiuntive:

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) di tipo `Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) di tipo [`ICommand`](xref:System.Windows.Input.ICommand) da usare con le associazioni dati e MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) di tipo `Object`, da utilizzare con `SearchCommand`

La specifica della piattaforma Annulla pulsante Cancella il testo. Il `SearchBar` dispone anche di un pulsante di ricerca specifico della piattaforma. Se si preme uno di questi pulsanti, viene generato uno dei due eventi che `SearchBar` definisce:

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) accompagnato da un oggetto [`TextChangedEventArgs`](xref:Xamarin.Forms.TextChangedEventArgs)
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

Nell'esempio [**SearchBarDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) viene illustrato il `SearchBar`.

## <a name="date-and-time-selection"></a>Selezione data e ora

Le visualizzazioni [`DatePicker`](xref:Xamarin.Forms.DatePicker) e [`TimePicker`](xref:Xamarin.Forms.TimePicker) implementano controlli specifici della piattaforma che consentono all'utente di specificare una data o un'ora.

### <a name="the-datepicker"></a>Il controllo DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker) definisce quattro proprietà:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) di tipo `DateTime`, inizializzato al 1 ° gennaio 1900
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) di tipo `DateTime`, inizializzato al 31 dicembre 2100
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) di tipo `DateTime`, inizializzato per `DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) di tipo `string`, la stringa di formattazione .NET inizializzata su "d", il modello di data breve, con conseguente visualizzazione di una data come "7/20/1969" negli Stati Uniti.

È possibile impostare le proprietà `DateTime` in XAML esprimendo le proprietà come elementi della proprietà e utilizzando il formato di data breve ("7/20/1969") delle impostazioni cultura.   

L'esempio [**DaysBetweenDates**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) calcola il numero di giorni tra due date selezionate dall'utente.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>Il controllo TimePicker (o si tratta di un TimeSpanPicker?)

[`TimePicker`](xref:Xamarin.Forms.TimePicker) definisce due proprietà e nessun evento:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) è di tipo `TimeSpan` invece che `DateTime`, che indica l'intervallo di tempo trascorso dalla mezzanotte
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) di tipo `string`, la stringa di formattazione .NET inizializzata su "t", il modello di ora breve, con conseguente visualizzazione dell'ora come "1:45 PM" negli Stati Uniti.

Il programma [**setimer**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) Mostra come usare la `TimePicker` per specificare un'ora per un timer. Il programma funziona solo se è mantenerla in primo piano.

Viene **inoltre illustrato** l'utilizzo del metodo [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) di `Page` per visualizzare una casella di avviso.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 15 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Esempi del capitolo 15](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Dispositivo di scorrimento](~/xamarin-forms/user-interface/slider.md)
- [Entry](~/xamarin-forms/user-interface/text/entry.md)
- [Editor](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
