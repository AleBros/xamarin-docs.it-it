---
title: Voce Novell. Forms
description: Questo articolo illustra come usare la classe di immissione Novell. Forms per accettare l'input di testo o password a riga singola in un'applicazione.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: 106d22cd7f20f8368bc728aa45c4d7f19d09c44c
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697067"
---
# <a name="xamarinforms-entry"></a>Voce Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Input di testo o password a riga singola_

Il [`Entry`](xref:Xamarin.Forms.Entry) Novell. Forms viene utilizzato per l'input di testo a riga singola. Il `Entry`, ad esempio la visualizzazione [`Editor`](xref:Xamarin.Forms.Editor) , supporta più tipi di tastiera. Inoltre, il `Entry` può essere usato come campo password.

## <a name="display-customization"></a>Personalizzazione della visualizzazione

### <a name="setting-and-reading-text"></a>Impostazione e lettura di testo

Il `Entry`, analogamente ad altre visualizzazioni di presentazione del testo, espone la proprietà [`Text`](xref:Xamarin.Forms.Entry.Text) . Questa proprietà può essere utilizzata per impostare e leggere il testo presentato dal `Entry`. Nell'esempio seguente viene illustrata l'impostazione della proprietà `Text` in XAML:

```xaml
<Entry Text="I am an Entry" />
```

In C#:

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Per leggere il testo, accedere alla proprietà `Text` C#in:

```csharp
var text = MyEntry.Text;
```

### <a name="setting-placeholder-text"></a>Impostazione del testo segnaposto

Il [`Entry`](xref:Xamarin.Forms.Entry) può essere impostato in modo da visualizzare il testo segnaposto quando non archivia l'input dell'utente. Questa operazione viene eseguita impostando la proprietà [`Placeholder`](xref:Xamarin.Forms.Entry.Placeholder) su un `string` e viene spesso usata per indicare il tipo di contenuto appropriato per il `Entry`. Inoltre, il colore del testo segnaposto può essere controllato impostando la proprietà [`PlaceholderColor`](xref:Xamarin.Forms.Entry.PlaceholderColor) su un [`Color`](xref:Xamarin.Forms.Color):

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> È possibile definire la larghezza di un `Entry` impostando la relativa proprietà `WidthRequest`. Non dipendere dalla larghezza di un `Entry` definito in base al valore della relativa proprietà `Text`.

### <a name="preventing-text-entry"></a>Impedisci immissione di testo

È possibile impedire agli utenti di modificare il testo in un [`Entry`](xref:Xamarin.Forms.Entry) impostando la proprietà `IsReadOnly`, che ha un valore predefinito di `false`, per `true`:

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> La proprietà `IsReadonly` non modifica l'aspetto visivo di un [`Entry`](xref:Xamarin.Forms.Entry), a differenza della proprietà `IsEnabled` che modifica anche l'aspetto visivo del `Entry` in grigio.

### <a name="limiting-input-length"></a>Limitazione della lunghezza di input

È possibile utilizzare la proprietà [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) per limitare la lunghezza di input consentita per l' [`Entry`](xref:Xamarin.Forms.Entry). Questa proprietà deve essere impostata su un numero intero positivo:

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Il valore 0 di [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) proprietà indica che nessun input sarà consentito e il valore `int.MaxValue`, che rappresenta il valore predefinito per un [`Entry`](xref:Xamarin.Forms.Entry), indica che non esiste alcun limite effettivo per il numero di caratteri che è possibile immettere.

### <a name="character-spacing"></a>Spaziatura caratteri

La spaziatura dei caratteri può essere applicata a un [`Entry`](xref:Xamarin.Forms.Entry) impostando la proprietà `Entry.CharacterSpacing` su un valore `double`:

```xaml
<Entry ...
       CharacterSpacing="10" />
```

Il codice C# equivalente è il seguente:

```csharp
Entry entry = new Entry { CharacterSpacing = 10 };
```

Il risultato è che i caratteri nel testo visualizzato dal [`Entry`](xref:Xamarin.Forms.Entry) sono spaziati `CharacterSpacing` unità indipendenti dal dispositivo.

> [!NOTE]
> Il valore della proprietà `CharacterSpacing` viene applicato al testo visualizzato dalle proprietà `Text` e `Placeholder`.

### <a name="password-fields"></a>Campi password

`Entry` fornisce la proprietà `IsPassword`. Quando `IsPassword` viene `true`, il contenuto del campo verrà presentato come cerchi neri:

In XAML:

```xaml
<Entry IsPassword="true" />
```

In C#:

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![Esempio di voce di password](entry-images/password.png)

I segnaposto possono essere utilizzati con istanze di `Entry` configurate come campi password:

In XAML:

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

In C#:

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![Esempio di immissione di password e segnaposto](entry-images/passwordplaceholder.png)

### <a name="setting-the-cursor-position-and-text-selection-length"></a>Impostazione della posizione del cursore e della lunghezza della selezione del testo

È possibile utilizzare la proprietà [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) per restituire o impostare la posizione in cui verrà inserito il carattere successivo nella stringa archiviata nella proprietà [`Text`](xref:Xamarin.Forms.Entry.Text) :

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

Il valore predefinito della proprietà [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) è 0, che indica che il testo verrà inserito all'inizio del `Entry`.

Inoltre, è possibile utilizzare la proprietà [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) per restituire o impostare la lunghezza della selezione del testo all'interno del `Entry`:

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

Il valore predefinito della proprietà [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) è 0, che indica che non è selezionato alcun testo.

### <a name="displaying-a-clear-button"></a>Visualizzazione di un pulsante Cancella

È possibile usare la proprietà `ClearButtonVisibility` per controllare se un [`Entry`](xref:Xamarin.Forms.Entry) Visualizza un pulsante Cancella, che consente all'utente di cancellare il testo. Questa proprietà deve essere impostata su un membro di enumerazione `ClearButtonVisibility`:

- `Never` indica che un pulsante Cancella non verrà mai visualizzato. È il valore predefinito per la proprietà `Entry.ClearButtonVisibility`.
- `WhileEditing` indica che verrà visualizzato un pulsante Cancella nel [`Entry`](xref:Xamarin.Forms.Entry), mentre è attivo e il testo.

Nell'esempio seguente viene illustrata l'impostazione della proprietà in XAML:

```xaml
<Entry Text="Xamarin.Forms"
       ClearButtonVisibility="WhileEditing" />
```

Il codice C# equivalente è il seguente:

```csharp
var entry = new Entry { Text = "Xamarin.Forms", ClearButtonVisibility = ClearButtonVisibility.WhileEditing };
```

Gli screenshot seguenti mostrano un [`Entry`](xref:Xamarin.Forms.Entry) con il pulsante Cancella abilitato:

![Screenshot di una voce con un pulsante Cancella, in iOS e Android](entry-images/entry-clear-button.png)

### <a name="customizing-the-keyboard"></a>Personalizzazione della tastiera

La tastiera visualizzata quando gli utenti interagiscono con un [`Entry`](xref:Xamarin.Forms.Entry) possono essere impostate a livello di codice tramite la proprietà [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) , a una delle seguenti proprietà della classe [`Keyboard`](xref:Xamarin.Forms.Keyboard) :

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) - usata per messaggi di testo e posizioni in cui sono utili gli emoji.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) - tastiera predefinita.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) - usata per l'immissione di indirizzi di posta elettronica.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) - usata per l'immissione di numeri.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) - usata per l'immissione di testo, senza [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) specificati.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) - usata per l'immissione di numeri di telefono.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) - usata per l'immissione di testo.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) - usata per l'immissione di percorsi di file e indirizzi Web.

Per ottenere questo risultato, è possibile procedere come segue in XAML:

```xaml
<Entry Keyboard="Chat" />
```

Il codice C# equivalente è il seguente:

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
```

Esempi di ogni tastiera sono disponibili nel repository [Recipes](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) .

La classe [`Keyboard`](xref:Xamarin.Forms.Keyboard) include anche un metodo factory [`Create`](xref:Xamarin.Forms.Keyboard.Create*) che può essere usato per personalizzare una tastiera, specificando il comportamento di maiuscole/minuscole, controllo ortografico e suggerimenti. I valori di enumerazione [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) vengono specificati come argomenti del metodo, con la restituzione di un elemento `Keyboard` personalizzato. L'enumerazione `KeyboardFlags` contiene i valori seguenti:

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) - non vengono aggiunte funzionalità alla tastiera.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) - indica che la prima lettera della prima parola di ogni frase immessa verrà automaticamente scritta in maiuscolo.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) - indica che verrà eseguito il controllo ortografico sul testo immesso.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) – indica che verranno offerti completamenti di parole per il testo immesso.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) - indica che la prima lettera di ogni parola verrà automaticamente scritta in maiuscolo.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) - indica che ogni carattere verrà scritto automaticamente in maiuscolo.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) - indica che non verrà applicata automaticamente la conversione in maiuscolo.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) - indica che il controllo ortografico, i completamenti delle parole e la conversione in maiuscolo per le frasi verranno applicati al testo immesso.

L'esempio di codice XAML seguente mostra come personalizzare l'elemento [`Keyboard`](xref:Xamarin.Forms.Keyboard) predefinito per offrire i completamenti delle parole e convertire in maiuscolo tutti i caratteri immessi:

```xaml
<Entry Placeholder="Enter text here">
    <Entry.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Entry.Keyboard>
</Entry>
```

Il codice C# equivalente è il seguente:

```csharp
var entry = new Entry { Placeholder = "Enter text here" };
entry.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

#### <a name="customizing-the-return-key"></a>Personalizzazione della chiave restituita

L'aspetto del tasto Return sulla tastiera soft, che viene visualizzato quando un [`Entry`](xref:Xamarin.Forms.Entry) ha lo stato attivo, può essere personalizzato impostando la proprietà [`ReturnType`](xref:Xamarin.Forms.Entry.ReturnType) su un valore dell'enumerazione [`ReturnType`](xref:Xamarin.Forms.ReturnType) :

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) : indica che non è richiesta alcuna chiave restituita specifica e che verrà utilizzata l'impostazione predefinita della piattaforma.
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) : indica una chiave di ritorno "Done".
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) : indica una chiave di ritorno "go".
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) : indica una chiave di ritorno "Next".
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) : indica una chiave di restituzione "Search".
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) : indica una chiave di restituzione "Send".

Nell'esempio di codice XAML riportato di seguito viene illustrato come impostare la chiave restituita:

```xaml
<Entry ReturnType="Send" />
```

Il codice C# equivalente è il seguente:

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> L'aspetto esatto della chiave restituita dipende dalla piattaforma. In iOS il tasto restituito è un pulsante basato su testo. Tuttavia, nelle piattaforme Android e Universal Windows la chiave restituita è un pulsante basato sull'icona.

Quando viene premuto il tasto Return, viene generato l'evento [`Completed`](xref:Xamarin.Forms.Entry.Completed) e vengono eseguiti tutti i `ICommand` specificati dalla proprietà [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) . Inoltre, tutti i `object` specificati dalla proprietà [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) verranno passati al `ICommand` come parametro. Per altre informazioni sui comandi, vedere l'articolo sull'[interfaccia di comando](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

### <a name="enabling-and-disabling-spell-checking"></a>Abilitazione e disabilitazione del controllo ortografico

La proprietà [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) controlla se il controllo ortografico è abilitato. Per impostazione predefinita, la proprietà è impostata su `true`. Quando l'utente immette testo, vengono indicati errori di ortografia.

Tuttavia, per alcuni scenari di immissione di testo, ad esempio l'immissione di un nome utente, il controllo ortografico offre un'esperienza negativa e deve essere disabilitato impostando la proprietà [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) su `false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando la proprietà [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) è impostata su `false` e non viene usata una tastiera personalizzata, il controllo ortografico nativo verrà disabilitato. Tuttavia, se è stato impostato un [`Keyboard`](xref:Xamarin.Forms.Keyboard) che disabilita il controllo ortografico, ad esempio [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat), la proprietà `IsSpellCheckEnabled` viene ignorata. Di conseguenza, non è possibile usare la proprietà per abilitare il controllo ortografico per una `Keyboard` che la Disabilita in modo esplicito.

### <a name="enabling-and-disabling-text-prediction"></a>Abilitazione e disabilitazione della stima del testo

La proprietà [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) controlla se è abilitata la stima del testo e la correzione automatica del testo. Per impostazione predefinita, la proprietà è impostata su `true`. Quando l'utente immette testo, vengono presentate le stime delle parole.

Tuttavia, per alcuni scenari di immissione di testo, ad esempio l'immissione di un nome utente, la stima del testo e la correzione automatica del testo offrono un'esperienza negativa ed è necessario disabilitarla impostando la proprietà [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) su `false`:

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Quando la proprietà [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) è impostata su `false` e non viene utilizzata una tastiera personalizzata, la stima del testo e la correzione automatica del testo sono disabilitate. Tuttavia, se è stata impostata una [`Keyboard`](xref:Xamarin.Forms.Keyboard) che disabilita la stima del testo, la proprietà `IsTextPredictionEnabled` viene ignorata. Pertanto, la proprietà non può essere utilizzata per abilitare la stima del testo per una `Keyboard` che la Disabilita in modo esplicito.

### <a name="colors"></a>Colori

È possibile impostare la voce per usare uno sfondo personalizzato e i colori del testo tramite le proprietà associabili seguenti:

- **TextColor** &ndash; imposta il colore del testo.
- **BackgroundColor** &ndash; imposta il colore visualizzato dietro il testo.

È necessario prestare particolare attenzione per garantire che i colori saranno utilizzabili in ogni piattaforma. Poiché ogni piattaforma ha impostazioni predefinite diverse per il testo e i colori di sfondo, sarà spesso necessario impostare entrambe se ne è stata impostata una.

Usare il codice seguente per impostare il colore del testo di una voce:

In XAML:

```xaml
<Entry TextColor="Green" />
```

In C#:

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![Esempio di TextColor entry](entry-images/textcolor.png)

Si noti che il segnaposto non è influenzato dal `TextColor` specificato.

Per impostare il colore di sfondo in XAML:

```xaml
<Entry BackgroundColor="#2c3e50" />
```

In C#:

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![Esempio di voce BackgroundColor](entry-images/textbackgroundcolor.png)

Prestare attenzione a verificare che i colori di sfondo e di testo scelti siano utilizzabili in ogni piattaforma e non nascondano testo segnaposto.

## <a name="events-and-interactivity"></a>Eventi e interattività

La voce espone due eventi:

- [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) &ndash; generato quando il testo viene modificato nella voce. Fornisce il testo prima e dopo la modifica.
- [`Completed`](xref:Xamarin.Forms.Entry.Completed) &ndash; generato quando l'utente ha terminato l'input premendo il tasto INVIO sulla tastiera.

> [!NOTE]
> La classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) , da cui eredita [`Entry`](xref:Xamarin.Forms.Entry) , dispone anche di eventi di [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) e [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) .

### <a name="completed"></a>Completato

L'evento `Completed` viene utilizzato per rispondere al completamento di un'interazione con una voce. `Completed` viene generato quando l'utente termina l'input con un campo premendo il tasto INVIO sulla tastiera oppure premendo il tasto TAB in UWP. Il gestore per l'evento è un gestore eventi generico che accetta il mittente e `EventArgs`:

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

È possibile sottoscrivere l'evento completed in XAML:

```xaml
<Entry Completed="Entry_Completed" />
```

e C#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

Una volta generato l'evento [`Completed`](xref:Xamarin.Forms.Entry.Completed) , vengono eseguiti tutti i `ICommand` specificati dalla proprietà [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) , con il `object` specificato dalla proprietà [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) passata al `ICommand`.

### <a name="textchanged"></a>TextChanged

L'evento `TextChanged` viene utilizzato per rispondere a una modifica nel contenuto di un campo.

`TextChanged` viene generato ogni volta che viene modificato il `Text` dell'`Entry`. Il gestore per l'evento accetta un'istanza di `TextChangedEventArgs`. `TextChangedEventArgs` fornisce l'accesso ai valori precedenti e nuovi del `Text` di `Entry` tramite le proprietà `OldTextValue` e `NewTextValue`:

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

È possibile sottoscrivere l'evento `TextChanged` in XAML:

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

e C#:

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```

## <a name="related-links"></a>Collegamenti correlati

- [Testo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [API Voce](xref:Xamarin.Forms.Entry)
