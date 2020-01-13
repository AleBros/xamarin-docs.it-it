---
title: Riepilogo del capitolo 15. L'interfaccia interattiva
description: "Creazione di app per dispositivi mobili con Xamarin.Forms: Riepilogo del capitolo 15. L'interfaccia interattiva"
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 1c30f87b9173d2ca4de0b2d91ad13145031e9b0a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760765"
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Riepilogo del capitolo 15. L'interfaccia interattiva

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)

In questo capitolo esamina otto `View` derivazioni che consentono di interagire con l'utente.

## <a name="view-overview"></a>Panoramica di View

Xamarin.Forms contiene 20 istanziabile classi che derivano da `View` ma non `Layout`. Sei di queste sono state trattate nei precedenti capitoli:

- `Label`: [**Capitolo 2. Anatomia di un'app**](chapter02.md)
- `BoxView`: [**Capitolo 3. Scorrimento dello stack**](chapter03.md)
- `Button`: [**Capitolo 6. Clic sui pulsanti**](chapter06.md)
- `Image`: [**Capitolo 13. Bitmap**](chapter13.md)
- `ActivityIndicator`: [**Capitolo 13. Bitmap**](chapter13.md)
- `ProgressBar`: [**Capitolo 14. AbsoluteLayout**](chapter14.md)

Le otto viste in questo capitolo consentono in modo efficace l'utente può interagire con tipi di dati .NET di base:

|Tipo di dati|Visualizzazioni|
|--- |--- |
|`Double`|[`Slider`](xref:Xamarin.Forms.Slider), [`Stepper`](xref:Xamarin.Forms.Stepper)|
|`Boolean`|[`Switch`](xref:Xamarin.Forms.Switch)|
|`String`|[`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor), [`SearchBar`](xref:Xamarin.Forms.SearchBar)|
|`DateTime`|[`DatePicker`](xref:Xamarin.Forms.DatePicker), [`TimePicker`](xref:Xamarin.Forms.TimePicker)|

È possibile considerare queste viste come rappresentazioni visive di interattive dei tipi di dati sottostante. Questo concetto viene esaminato più in capitolo successivo [ **capitolo 16. Associazione dati**](chapter16.md).

Le viste di sei rimanenti vengono trattate nei capitoli seguenti:

- `WebView`: [**Capitolo 16. Associazione dati**](chapter16.md)
- `Picker`: [**Capitolo 19. Visualizzazioni di raccolta**](chapter19.md)
- `ListView`: [**Capitolo 19. Visualizzazioni di raccolta**](chapter19.md)
- `TableView`: [**Capitolo 19. Visualizzazioni di raccolta**](chapter19.md)
- `Map`: [**Capitolo 28. Posizione e mappe**](chapter28.md)
- `OpenGLView`: Non trattato in questo libro (e nessun supporto per le piattaforme Windows)

## <a name="slider-and-stepper"></a>Dispositivo di scorrimento e gestore di istruzioni

Entrambe [ `Slider` ](xref:Xamarin.Forms.Slider) e [ `Stepper` ](xref:Xamarin.Forms.Stepper) consentono all'utente di scegliere un valore numerico da un intervallo. Il `Slider` è un intervallo continuo mentre i `Stepper` prevede valori discreti.

### <a name="slider-basics"></a>Nozioni di base di dispositivo di scorrimento

Il [ `Slider` ](xref:Xamarin.Forms.Slider) è una barra che rappresenta un intervallo di valori da un minimo a sinistra per un massimo di destra orizzontale. Definisce tre proprietà pubbliche:

- [`Value`](xref:Xamarin.Forms.Slider.Value) di tipo `double`, il valore predefinito di 0
- [`Minimum`](xref:Xamarin.Forms.Slider.Minimum) di tipo `double`, il valore predefinito di 0
- [`Maximum`](xref:Xamarin.Forms.Slider.Maximum) di tipo `double`, il valore predefinito di 1

Le proprietà associabili che supportano queste proprietà verificare che siano coerenti con:

- Per tutte le tre proprietà, il [ `coerceValue` ](xref:Xamarin.Forms.BindableProperty.CoerceValueDelegate) metodo specificato per la proprietà associabile assicura che `Value` tra `Minimum` e `Maximum`.
- Il [ `validateValue` ](xref:Xamarin.Forms.BindableProperty.ValidateValueDelegate) metodo sul `MinimumProperty` restituisce `false` se `Minimum` viene impostata su un valore maggiore o uguale a `Maximum`e così via per `MaximumProperty`. Restituzione `false` dal `validateValue` metodo provoca un `ArgumentException` da generare.

`Slider` viene attivato il [ `ValueChanged` ](xref:Xamarin.Forms.Slider.ValueChanged) evento con un [ `ValueChangedEventArgs` ](xref:Xamarin.Forms.ValueChangedEventArgs) argomento quando il `Value` le modifiche alle proprietà, a livello di programmazione o quando l'utente modifica il `Slider`.

Il [ **SliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) esempio dimostra l'uso semplice del `Slider`.

### <a name="common-pitfalls"></a>Problemi comuni

Nel codice e in XAML, il `Minimum` e `Maximum` vengono impostate proprietà nell'ordine specificato. Assicurarsi di inizializzare queste proprietà in modo che `Maximum` è sempre maggiore `Minimum`. In caso contrario, viene generata un'eccezione.

L'inizializzazione di `Slider` proprietà possono causare il `Value` proprietà da modificare e il `ValueChanged` evento generato. È necessario assicurarsi che il `Slider` gestore dell'evento non accedere alle viste che non sono ancora state create durante l'inizializzazione della pagina.

Il `ValueChanged` evento non viene attivato durante `Slider` inizializzazione, a meno che il `Value` le modifiche alle proprietà. È possibile chiamare il `ValueChanged` gestore direttamente dal codice.

### <a name="slider-color-selection"></a>Selezione di colore dispositivo di scorrimento

Il [ **RgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) programma contiene tre `Slider` gli elementi che consentono di selezionare in modo interattivo un colore specificando i relativi valori RGB:

[![Tripla schermata di Slider B G R](images/ch15fg03-small.png "dispositivi di scorrimento RGB")](images/ch15fg03-large.png#lightbox "dispositivi di scorrimento RGB")

Il [ **TextFade** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) esempio vengono usati due `Slider` elementi da spostare due `Label` elementi attraverso un `AbsoluteLayout` e dissolvenza uno in altro.

### <a name="the-stepper-difference"></a>La differenza di gestore di istruzioni

Il [ `Stepper` ](xref:Xamarin.Forms.Stepper) definisce le stesse proprietà e gli eventi come `Slider` ma la `Maximum` proprietà viene inizializzata su 100 e `Stepper` definisce una proprietà quarta:

- [`Increment`](xref:Xamarin.Forms.Stepper.Increment) di tipo `double`, inizializzato in base 1

In modo visivo, il `Stepper` è costituito da due pulsanti contrassegnati **&ndash;** e **+** . Premendo **&ndash;** diminuisce `Value` dal `Increment` a un minimo di `Minimum`. Premendo **+** aumenta `Value` dal `Increment` a un massimo di `Maximum`.

Ciò viene dimostrata la [ **StepperDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) esempio.

## <a name="switch-and-checkbox"></a>Commutatore e casella di controllo

Il [ `Switch` ](xref:Xamarin.Forms.Switch) consente all'utente di specificare un valore booleano.

### <a name="switch-basics"></a>Nozioni di base commutatore

Visivamente il `Switch` è costituito da un elemento toggle che è possibile attivare e disattivare. La classe definisce una proprietà:

- [`IsToggled`](xref:Xamarin.Forms.Switch.IsToggled) di tipo `bool`

`Switch` definisce un evento:

- [`Toggled`](xref:Xamarin.Forms.Switch.Toggled) accompagnato da un [ `ToggledEventArgs` ](xref:Xamarin.Forms.ToggledEventArgs) oggetto, viene generato quando il `IsToggled` le modifiche alle proprietà.

Il [ **SwitchDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) programma illustra il `Switch`.

### <a name="a-traditional-checkbox"></a>Una casella di controllo tradizionale

Alcuni sviluppatori potrebbero preferire un più tradizionale `CheckBox` per il `Switch`. Il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria contiene una `CheckBox` classe che deriva da `ContentView`. `CheckBox` viene implementato dal [CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) e [CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) file. `CheckBox` definisce tre proprietà (`Text`, `FontSize`, e `IsChecked`) e un `CheckedChanged` evento.

Il [ **CheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) esempio viene illustrata questa `CheckBox`.

## <a name="typing-text"></a>Digitare un testo

Xamarin.Forms definisce tre visualizzazioni che consentono di immettere e modificare il testo:

- [`Entry`](xref:Xamarin.Forms.Entry) per una singola riga di testo
- [`Editor`](xref:Xamarin.Forms.Editor) per più righe di testo
- [`SearchBar`](xref:Xamarin.Forms.SearchBar) per una singola riga di testo per scopi di ricerca.

`Entry` e `Editor` derivano da [ `InputView` ](xref:Xamarin.Forms.InputView), che deriva da `View`. `SearchBar` deriva direttamente dalla `View`.

### <a name="keyboard-and-focus"></a>Tastiera e lo stato attivo

Su telefoni e Tablet senza tastiere fisiche, il `Entry`, `Editor`, e `SearchBar` tutti gli elementi causano una tastiera virtuale visualizzata. La presenza di questo tipo di tastiera su schermo è correlata a uno stato attivo. Una vista deve avere entrambe relativi [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) e [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) impostate su `true` per ottenere lo stato attivo.

Con stato attivo di input sono coinvolti due metodi, proprietà di sola lettura una e due eventi. Questi sono tutti definiti da `VisualElement`:

- Il [ `Focus` ](xref:Xamarin.Forms.VisualElement.Focus) metodo tenta di impostare lo stato attivo a un elemento e restituisce `true` se ha esito positivo
- Il [ `Unfocus` ](xref:Xamarin.Forms.VisualElement.Unfocus) metodo rimuove lo stato attivo di input da un elemento
- Il [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused) proprietà di sola lettura indica se l'elemento ha lo stato attivo di input
- Il [ `Focused` ](xref:Xamarin.Forms.VisualElement.Focused) eventi indica quando un elemento riceve lo stato attivo
- Il [ `Unfocused` ](xref:Xamarin.Forms.VisualElement.Unfocused) eventi indica quando un elemento perde lo stato attivo

### <a name="choosing-the-keyboard"></a>Scelta della tastiera

Il [ `InputView` ](xref:Xamarin.Forms.InputView) classe dalla quale `Entry` e `Editor` derivare definisce una sola proprietà:

- [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) di tipo [`Keyboard`](xref:Xamarin.Forms.Keyboard)

Indica il tipo di tastiera che viene visualizzato. Alcune tastiere sono ottimizzate per i numeri o URI.

Il `Keyboard` classe consente di definire una tastiera con un valore statico [ `Keyboard.Create` ](xref:Xamarin.Forms.Keyboard.Create(Xamarin.Forms.KeyboardFlags)) metodo con un argomento di tipo [ `KeyboardFlags` ](xref:Xamarin.Forms.KeyboardFlags), un'enumerazione con i flag di bit seguenti:

- `None` Impostare su 0
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) Impostare su 1
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) impostato su 2
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) Impostare su 4
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) Impostare su \xFFFFFFFF

Quando si usa la proprietà multiline [ `Editor` ](xref:Xamarin.Forms.Editor) se si prevede che più di un paragrafo di testo, la chiamata `Keyboard.Create` è un buon approccio per la selezione di una tastiera. Per il controllo a riga singola [ `Entry` ](xref:Xamarin.Forms.Entry), le proprietà di sola lettura statiche seguenti di `Keyboard` sono utili:

- [`Default`](xref:Xamarin.Forms.Keyboard.Default)
- [`Text`](xref:Xamarin.Forms.Keyboard.Text)
- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat)
- [`Url`](xref:Xamarin.Forms.Keyboard.Url)
- [`Email`](xref:Xamarin.Forms.Keyboard.Email)
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone)
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) per i numeri positivi con o senza un separatore decimale.

Il [ `KeyboardTypeConverter` ](xref:Xamarin.Forms.KeyboardTypeConverter) consente di specificare queste proprietà in XAML, come dimostrato dal [ **EntryKeyboards** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) programma.

### <a name="entry-properties-and-events"></a>Voce proprietà ed eventi

Il controllo a riga singola [ `Entry` ](xref:Xamarin.Forms.Entry) definisce le proprietà seguenti:

- [`Text`](xref:Xamarin.Forms.Entry.Text) di tipo `string`, il testo visualizzato di `Entry`
- [`TextColor`](xref:Xamarin.Forms.Entry.TextColor) di tipo `Color`
- [`FontFamily`](xref:Xamarin.Forms.Entry.FontFamily) di tipo `string`
- [`FontSize`](xref:Xamarin.Forms.Entry.FontSize) di tipo `double`
- [`FontAttributes`](xref:Xamarin.Forms.Entry.FontAttributes) di tipo `FontAttributes`
- [`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) di tipo `bool`, in modo che i caratteri da mascherare
- [`Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) typu `string`, per il testo scarsamente colorato che viene visualizzato nel `Entry` prima di tutto ciò è tipizzato
- [`PlaceholderColor`](xref:Xamarin.Forms.Entry.PlaceholderColor) di tipo `Color`

Il `Entry` definisce inoltre due eventi:

- [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) con un [ `TextChangedEventArgs` ](xref:Xamarin.Forms.TextChangedEventArgs) oggetto, viene generato ogni volta che il `Text` le modifiche alle proprietà
- [`Completed`](xref:Xamarin.Forms.Entry.Completed), viene generato quando l'utente ha terminato e viene chiusa la tastiera. L'utente indica il completamento in modo specifico della piattaforma

Il [ **QuadraticEquations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) esempio vengono illustrati questi due eventi.

### <a name="the-editor-difference"></a>La differenza di Editor

La proprietà multiline [ `Editor` ](xref:Xamarin.Forms.Editor) definisce gli stessi `Text` e `Font` proprietà come `Entry` ma non altre proprietà. `Editor` definisce inoltre le stesse due proprietà di `Entry`.

[**JustNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) è un programma di note per la creazione in formato libero che consente di salvare e ripristina il contenuto del `Editor`.

### <a name="the-searchbar"></a>Il SearchBar

Il [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) non deriva da `InputView`, in modo che non dispone di un `Keyboard` proprietà. Ma dispone di tutti i `Text`, `Font`, e `Placeholder` delle proprietà che `Entry` definisce. Inoltre, `SearchBar` definisce tre proprietà aggiuntive:

- [`CancelButtonColor`](xref:Xamarin.Forms.SearchBar.CancelButtonColor) di tipo `Color`
- [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) typu [ `ICommand` ](xref:System.Windows.Input.ICommand) per l'uso con data binding e MVVM
- [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) di tipo `Object`, per l'utilizzo con `SearchCommand`

La specifica della piattaforma Annulla pulsante Cancella il testo. Il `SearchBar` ha anche un pulsante di ricerca specifici della piattaforma. Premendo uno di questi pulsanti genera uno dei due eventi che `SearchBar` definisce:

- [`TextChanged`](xref:Xamarin.Forms.SearchBar.TextChanged) accompagnato da un [ `TextChangedEventArgs` ](xref:Xamarin.Forms.TextChangedEventArgs) oggetto
- [`SearchButtonPressed`](xref:Xamarin.Forms.SearchBar.SearchButtonPressed)

Il [ **SearchBarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) esempio viene illustrato il `SearchBar`.

## <a name="date-and-time-selection"></a>Selezione data e ora

Il [ `DatePicker` ](xref:Xamarin.Forms.DatePicker) e [ `TimePicker` ](xref:Xamarin.Forms.TimePicker) viste implementano controlli specifici della piattaforma che consentono all'utente di specificare una data o ora.

### <a name="the-datepicker"></a>Il controllo DatePicker

[`DatePicker`](xref:Xamarin.Forms.DatePicker) definisce le quattro proprietà:

- [`MinimumDate`](xref:Xamarin.Forms.DatePicker.MinimumDate) di tipo `DateTime`, inizializzato in base 1 gennaio 1900
- [`MaximumDate`](xref:Xamarin.Forms.DatePicker.MaximumDate) di tipo `DateTime`, inizializzato in base al 31 dicembre 2100
- [`Date`](xref:Xamarin.Forms.DatePicker.Date) di tipo `DateTime`, inizializzato in `DateTime.Today`
- [`Format`](xref:Xamarin.Forms.DatePicker.Format) di tipo `string`, .NET inizializzato su "d", il modello di data breve stringa di formattazione risultante in una visualizzazione della data, ad esempio "7/20/1969" negli Stati Uniti.

È possibile impostare il `DateTime` proprietà in XAML che esprime le proprietà come elementi di proprietà e usando la data breve indipendente dalle impostazioni cultura di formato ("20/7/1969").   

Il [ **DaysBetweenDates** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) esempio calcola il numero di giorni tra due date selezionate dall'utente.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>Il controllo TimePicker (o si tratta di un TimeSpanPicker?)

[`TimePicker`](xref:Xamarin.Forms.TimePicker) definisce due proprietà e nessun evento:

- [`Time`](xref:Xamarin.Forms.TimePicker.Time) JE typu `TimeSpan` anziché `DateTime`, che indica il tempo trascorso dalla mezzanotte del
- [`Format`](xref:Xamarin.Forms.TimePicker.Format) di tipo `string`, .NET inizializzato su "t", il modello di ora breve, risultante in una visualizzazione cronologica, ad esempio "1:45 PM" stringa di formattazione negli Stati Uniti.

Il [ **SetTimer** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) programma illustra come usare il `TimePicker` per specificare un'ora di un timer. Il programma funziona solo se è mantenerla in primo piano.

**SetTimer** viene illustrato l'uso di [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) metodo `Page` per visualizzare una finestra di avviso.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 15 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Capitolo 15 campioni](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Dispositivo di scorrimento](~/xamarin-forms/user-interface/slider.md)
- [Entry](~/xamarin-forms/user-interface/text/entry.md)
- [Editor](~/xamarin-forms/user-interface/text/editor.md)
- [DatePicker](~/xamarin-forms/user-interface/datepicker.md)
