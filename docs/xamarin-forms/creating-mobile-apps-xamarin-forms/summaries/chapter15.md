---
title: Riepilogo del capitolo 15. L'interfaccia interattiva
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F54E86F4-1CDA-474E-9B09-242060C2C13D
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 09b999771ec500409e40dc2aef671045bf9f5565
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2018
---
# <a name="summary-of-chapter-15-the-interactive-interface"></a>Riepilogo del capitolo 15. L'interfaccia interattiva

In questo capitolo viene esaminata otto `View` derivati che consentono l'interazione dell'utente.

## <a name="view-overview"></a>Visualizza panoramica

Xamarin. Forms contiene 20 istanziabili classi che derivano da `View` ma non `Layout`. Sei di queste sono state trattate nei capitoli precedenti:

- `Label`: [ **Capitolo 2. Anatomia di un'app**](chapter02.md)
- `BoxView`: [ **Capitolo 3. Lo stack di scorrimento**](chapter03.md)
- `Button`: [ **Capitolo 6. Fa clic sul pulsante**](chapter06.md)
- `Image`: [ **Capitolo 13. Bitmaps**](chapter13.md)
- `ActivityIndicator`: [ **Capitolo 13. Bitmaps**](chapter13.md)
- `ProgressBar`: [ **Capitolo 14. AbsoluteLayout**](chapter14.md)

Le viste in questo capitolo otto consentono in modo efficace all'utente di interagire con tipi di dati .NET di base:

|Tipo di dati|Visualizzazioni|
|--- |--- |
|`Double`|[`Slider`](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/), [`Stepper`](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/)|
|`Boolean`|[`Switch`](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/)|
|`String`|[`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/), [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/), [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/)|
|`DateTime`|[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/), [`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/)|

È possibile pensare di queste visualizzazioni come rappresentazioni visive di interattive dei tipi di dati sottostante. Questo concetto viene esaminato più nel capitolo successivo, [ **capitolo 16. Associazione dati**](chapter16.md).

Le viste di sei rimanenti sono illustrate nei capitoli seguenti:

- `WebView`: [ **Capitolo 16. associazione dati**](chapter16.md)
- `Picker`: [ **Capitolo 19. Viste di raccolta**](chapter19.md)
- `ListView`: [ **Capitolo 19. Viste di raccolta**](chapter19.md)
- `TableView`: [ **Capitolo 19. Viste di raccolta**](chapter19.md)
- `Map`: [ **Capitolo 28. Percorso e mappe**](chapter28.md)
- `OpenGLView`: Non trattati in questo manuale (e nessun supporto per piattaforme Windows)

## <a name="slider-and-stepper"></a>Dispositivo di scorrimento e gestore di istruzioni

Entrambi [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) e [ `Stepper` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) consentire all'utente di scegliere un valore numerico da un intervallo. Il `Slider` è un intervallo continuo durante il `Stepper` prevede valori discreti.

### <a name="slider-basics"></a>Nozioni fondamentali di dispositivo di scorrimento

Il [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) è una barra di stato che rappresenta un intervallo di valori da un minimo di sinistra fino a destra. Definisce tre proprietà pubbliche:

- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Value/) di tipo `double`, il valore predefinito di 0
- [`Minimum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Minimum/) di tipo `double`, il valore predefinito di 0
- [`Maximum`](https://developer.xamarin.com/api/property/Xamarin.Forms.Slider.Maximum/) di tipo `double`, il valore predefinito di 1

Le proprietà associabili che supportano queste proprietà assicurarsi che siano coerenti:

- Per tutte e tre le proprietà, il [ `coerceValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+CoerceValueDelegate/) metodo specificato per la proprietà associabile garantisce che `Value` tra `Minimum` e `Maximum`.
- Il [ `validateValue` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty+ValidateValueDelegate/) metodo `MinimumProperty` restituisce `false` se `Minimum` viene impostata su un valore maggiore o uguale a `Maximum`e così via per `MaximumProperty`. Restituzione di `false` dal `validateValue` metodo provoca un `ArgumentException` da generare.

`Slider` viene attivato il [ `ValueChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Slider.ValueChanged/) evento con un [ `ValueChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ValueChangedEventArgs/) argomento quando il `Value` modifiche delle proprietà, a livello di codice o quando l'utente modifica il `Slider`.

Il [ **SliderDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SliderDemo) esempio viene illustrato l'utilizzo di semplice di `Slider`.

### <a name="common-pitfalls"></a>Problemi comuni

Nel codice e in XAML, il `Minimum` e `Maximum` proprietà vengono impostate nell'ordine specificato. Assicurarsi di inizializzare le proprietà in modo che `Maximum` è sempre maggiore di `Minimum`. In caso contrario, viene generata un'eccezione.

L'inizializzazione di `Slider` proprietà possono causare il `Value` proprietà da modificare e `ValueChanged` evento di attivazione. È consigliabile verificare che il `Slider` gestore dell'evento non accedere alle visualizzazioni non sono stati ancora creati durante l'inizializzazione della pagina.

Il `ValueChanged` evento non viene attivato durante `Slider` inizializzazione, a meno che il `Value` le modifiche alle proprietà. È possibile chiamare il `ValueChanged` gestore direttamente dal codice.

### <a name="slider-color-selection"></a>Selezione del colore dispositivo di scorrimento

Il [ **RgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/RgbSliders) programma contiene tre `Slider` gli elementi che consentono di selezionare in modo interattivo un colore specificando i relativi valori RGB:

[![Schermata di triplo dei cursori R G B](images/ch15fg03-small.png "RGB cursori")](images/ch15fg03-large.png#lightbox "RGB cursori")

Il [ **TextFade** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/TextFade) esempio vengono utilizzati due `Slider` elementi da spostare due `Label` elementi attraverso un `AbsoluteLayout` e dissolvenza uno in altro.

### <a name="the-stepper-difference"></a>La differenza di gestore di istruzioni

Il [ `Stepper` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/) definisce le stesse proprietà e gli eventi come `Slider` ma la `Maximum` proprietà viene inizializzata su 100 e `Stepper` definisce una quarta proprietà:

- [`Increment`](https://developer.xamarin.com/api/property/Xamarin.Forms.Stepper.Increment/) di tipo `double`, inizializzato su 1

In modo visivo, la `Stepper` è costituito da due pulsanti  **&ndash;**  e  **+** . Premendo  **&ndash;**  diminuisce `Value` da `Increment` a un minimo di `Minimum`. Premendo  **+**  aumenta `Value` da `Increment` a un massimo di `Maximum`.

Ciò viene dimostrata la [ **StepperDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/StepperDemo) esempio.

## <a name="switch-and-checkbox"></a>Opzione e caselle di controllo

Il [ `Switch` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) consente all'utente di specificare un valore booleano.

### <a name="switch-basics"></a>Nozioni fondamentali di commutatore

In modo visivo, la `Switch` è costituito da un elemento toggle che è possibile attivare e disattivare. La classe definisce una proprietà:

- [`IsToggled`](https://developer.xamarin.com/api/property/Xamarin.Forms.Switch.IsToggled/) di tipo `bool`

`Switch` definisce un evento:

- [`Toggled`](https://developer.xamarin.com/api/event/Xamarin.Forms.Switch.Toggled/) accompagnato da un [ `ToggledEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToggledEventArgs/) oggetto, viene generato quando il `IsToggled` le modifiche alle proprietà.

Il [ **SwitchDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SwitchDemo) programma illustra il `Switch`.

### <a name="a-traditional-checkbox"></a>Una casella di controllo tradizionale

Alcuni sviluppatori preferiscono più tradizionale `CheckBox` per il `Switch`. Il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria contiene un `CheckBox` classe che deriva da `ContentView`. `CheckBox` viene implementata dal [CheckBox.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml) e [CheckBox.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/CheckBox.xaml.cs) file. `CheckBox` definisce tre proprietà (`Text`, `FontSize`, e `IsChecked`) e un `CheckedChanged` evento.

Il [ **CheckBoxDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/CheckBoxDemo) esempio viene illustrato questo `CheckBox`.

## <a name="typing-text"></a>Digitare il testo

Xamarin. Forms definisce tre visualizzazioni che consentono di immettere e modificare testo:

- [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) per una singola riga di testo
- [`Editor`](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) per più righe di testo
- [`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) per una singola riga di testo per scopi di ricerca.

`Entry` e `Editor` derivano da [ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/), che deriva da `View`. `SearchBar` deriva direttamente da `View`.

### <a name="keyboard-and-focus"></a>Tastiera e lo stato attivo

Ai telefoni e Tablet senza tastiere fisiche, il `Entry`, `Editor`, e `SearchBar` tutti gli elementi causano una tastiera virtuale essere visualizzata. La presenza di questo tipo di tastiera sullo schermo è correlata allo stato attivo di input. Una vista deve avere entrambe relativo [ `IsVisible` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/) e [ `IsEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/) le proprietà impostate su `true` per ottenere lo stato attivo di input.

Con stato attivo di input sono coinvolti due metodi, una proprietà di sola lettura e due eventi. Questi sono tutti definiti da `VisualElement`:

- Il [ `Focus` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Focus()/) metodo tenta di impostare lo stato attivo di input a un elemento e restituisce `true` se ha esito positivo
- Il [ `Unfocus` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Unfocus()/) metodo rimuove lo stato attivo di input da un elemento
- Il [ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/) proprietà di sola lettura indica se l'elemento ha lo stato attivo di input
- Il [ `Focused` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.Focused/) evento indica quando un elemento riceve lo stato attivo di input
- Il [ `Unfocused` ](https://developer.xamarin.com/api/event/Xamarin.Forms.VisualElement.Unfocused/) evento indica quando un elemento perde lo stato attivo di input

### <a name="choosing-the-keyboard"></a>Scelta della tastiera

Il [ `InputView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.InputView/) classe da cui `Entry` e `Editor` derivano definisce solo una proprietà:

- [`Keyboard`](https://developer.xamarin.com/api/property/Xamarin.Forms.InputView.Keyboard/) di tipo [`Keyboard`](https://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/)

Indica il tipo di tastiera che viene visualizzato. Alcune tastiere sono ottimizzate per gli URI o numeri.

Il `Keyboard` classe consente la definizione di una tastiera con un valore statico [ `Keyboard.Create` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Keyboard.Create/p/Xamarin.Forms.KeyboardFlags/) metodo con un argomento di tipo [ `KeyboardFlags` ](https://developer.xamarin.com/api/type/Xamarin.Forms.KeyboardFlags/), un'enumerazione con i flag di bit seguenti:

- `None` Impostare su 0
- [`CapitalizeSentence`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.CapitalizeSentence/) Impostare su 1
- [`Spellcheck`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.Spellcheck/) impostato su 2
- [`Suggestions`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.Suggestions/) Impostare su 4
- [`All`](https://developer.xamarin.com/api/field/Xamarin.Forms.KeyboardFlags.All/) Impostare su \xFFFFFFFF

Quando si utilizza la proprietà multiline [ `Editor` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) se si prevede più di un paragrafo di testo, la chiamata `Keyboard.Create` è un buon approccio per la selezione di una tastiera. Per il controllo a riga singola [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/), le proprietà di sola lettura statiche seguenti di `Keyboard` sono utili:

- [`Default`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Default/)
- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Text/)
- [`Chat`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Chat/)
- [`Url`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Url/)
- [`Email`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Email/)
- [`Telephone`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Telephone/)
- [`Numeric`](https://developer.xamarin.com/api/property/Xamarin.Forms.Keyboard.Numeric/) per i numeri positivi con o senza un separatore decimale.

Il [ `KeyboardTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.KeyboardTypeConverter/) consente di specificare queste proprietà in XAML, come illustrato di [ **EntryKeyboards** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/EntryKeyboards) programma.

### <a name="entry-properties-and-events"></a>Gli eventi e proprietà della voce

Il controllo a riga singola [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) definisce le proprietà seguenti:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) di tipo `string`, il testo visualizzato di `Entry`
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/) di tipo `Color`
- [`FontFamily`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontFamily/) di tipo `string`
- [`FontSize`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontSize/) di tipo `double`
- [`FontAttributes`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.FontAttributes/) di tipo `FontAttributes`
- [`IsPassword`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.IsPassword/) di tipo `bool`, che converte i caratteri da mascherare
- [`Placeholder`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Placeholder/) di tipo `string`, per il testo colorato scarsamente che viene visualizzato nel `Entry` prima di qualsiasi elemento sia stato digitato
- [`PlaceholderColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.PlaceholderColor/) di tipo `Color`

Il `Entry` definisce anche due eventi:

- [`TextChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) con un [ `TextChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextChangedEventArgs/) oggetto, viene generato ogni volta che il `Text` le modifiche alle proprietà
- [`Completed`](https://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/), viene generato quando l'utente è stato terminato e viene ignorata la tastiera. L'utente indica il completamento in modo specifico della piattaforma

Il [ **QuadraticEquations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/QuadaticEquations) esempio vengono illustrati questi due eventi.

### <a name="the-editor-difference"></a>La differenza di Editor

La proprietà multiline [ `Editor` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/) definisce gli stessi `Text` e `Font` proprietà come `Entry` ma non altre proprietà. `Editor` definisce inoltre le stesse due proprietà `Entry`.

[**JustNotes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/JustNotes) è un programma per la creazione di note in formato libero che salva e ripristina il contenuto del `Editor`.

### <a name="the-searchbar"></a>Il SearchBar

Il [ `SearchBar` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) non deriva da `InputView`, pertanto non dispone di un `Keyboard` proprietà. Ma dispone di tutti i `Text`, `Font`, e `Placeholder` proprietà che `Entry` definisce. Inoltre, `SearchBar` definisce tre proprietà aggiuntive:

- [`CancelButtonColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.CancelButtonColor/) di tipo `Color`
- [`SearchCommand`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommand/) di tipo [ `ICommand` ](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/) per l'utilizzo con data binding e MVVM
- [`SearchCommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommandParameter/) di tipo `Object`, per l'utilizzo con `SearchCommand`

La specifica della piattaforma annullare pulsante Cancella il testo. Il `SearchBar` ha anche un pulsante di ricerca specifici della piattaforma. Premere uno di questi pulsanti genera uno dei due eventi che `SearchBar` definisce:

- [`TextChanged`](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.TextChanged/) accompagnato da un [ `TextChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextChangedEventArgs/) oggetto
- [`SearchButtonPressed`](https://developer.xamarin.com/api/event/Xamarin.Forms.SearchBar.SearchButtonPressed/)

Il [ **SearchBarDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SearchBarDemo) esempio viene illustrato il `SearchBar`.

## <a name="date-and-time-selection"></a>Selezione data e ora

Il [ `DatePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) e [ `TimePicker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) viste implementano controlli specifici della piattaforma che consentono all'utente di specificare una data o ora.

### <a name="the-datepicker"></a>La selezione data

[`DatePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/) definisce quattro proprietà:

- [`MinimumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MinimumDate/) di tipo `DateTime`, inizializzata con un valore 1 gennaio 1900
- [`MaximumDate`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.MaximumDate/) di tipo `DateTime`, inizializzato al 31 dicembre 2100
- [`Date`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Date/) di tipo `DateTime`, inizializzata con un valore `DateTime.Today`
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.DatePicker.Format/) di tipo `string`, .NET inizializzato su "d", il modello di data breve stringa di formattazione risultante in una visualizzazione della data come "7/20/1969" negli Stati Uniti.

È possibile impostare il `DateTime` proprietà in XAML che esprime le proprietà come elementi di proprietà e utilizzando la data breve delle impostazioni cultura invarianti formato ("20/7/1969").   

Il [ **DaysBetweenDates** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/DaysBetweenDates) esempio calcola il numero di giorni tra due date selezionate dall'utente.

### <a name="the-timepicker-or-is-it-a-timespanpicker"></a>Il TimePicker (o è un TimeSpanPicker?)

[`TimePicker`](https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/) definisce due proprietà e gli eventi non:

- [`Time`](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Time/) è di tipo `TimeSpan` anziché `DateTime`, che indica il tempo trascorso dalla mezzanotte
- [`Format`](https://developer.xamarin.com/api/property/Xamarin.Forms.TimePicker.Format/) di tipo `string`, .NET inizializzato su "t", il modello di ora breve, risultante in una visualizzazione cronologica come "1:45 PM" stringa di formattazione negli Stati Uniti.

Il [ **SetTimer** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15/SetTimer) programma viene illustrato come utilizzare il `TimePicker` per specificare un'ora di un timer. Il programma funziona solo se è mantenerla in primo piano.

**SetTimer** illustra inoltre l'utilizzo di [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) metodo `Page` per visualizzare una finestra di avviso.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 15 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch15-Apr2016.pdf)
- [Capitolo 15 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter15)
- [Entry](~/xamarin-forms/user-interface/text/entry.md)
- [Editor](~/xamarin-forms/user-interface/text/editor.md)
