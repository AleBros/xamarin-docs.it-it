---
title: " Xamarin.Forms entry" Description: "questo articolo spiega come usare la Xamarin.Forms classe entry per accettare l'input di testo o password a riga singola in un'applicazione".
ms. prod: Novell MS. AssetID: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 09/25/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-entry"></a>Xamarin.FormsVoce

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Input di testo o password a riga singola_

Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) Viene utilizzato per l'input di testo a riga singola. `Entry`, Come la [`Editor`](xref:Xamarin.Forms.Editor) visualizzazione, supporta più tipi di tastiera. Inoltre, `Entry` può essere utilizzato come campo password.

## <a name="display-customization"></a>Personalizzazione della visualizzazione

### <a name="setting-and-reading-text"></a>Impostazione e lettura di testo

, `Entry` Come altre visualizzazioni di presentazione del testo, espone la [`Text`](xref:Xamarin.Forms.InputView.Text) Proprietà. Questa proprietà può essere utilizzata per impostare e leggere il testo presentato da `Entry` . Nell'esempio seguente viene illustrata l'impostazione della `Text` Proprietà in XAML:

```xaml
<Entry Text="I am an Entry" />
```

In C#:

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Per leggere il testo, accedere alla `Text` Proprietà in C#:

```csharp
var text = MyEntry.Text;
```

### <a name="setting-placeholder-text"></a>Impostazione del testo segnaposto

Il [`Entry`](xref:Xamarin.Forms.Entry) parametro può essere impostato in modo da visualizzare il testo segnaposto quando non archivia l'input dell'utente. Questa operazione viene eseguita impostando la [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) proprietà su un oggetto `string` e viene spesso usata per indicare il tipo di contenuto appropriato per `Entry` . Inoltre, il colore del testo segnaposto può essere controllato impostando la [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) proprietà su [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> La larghezza di un oggetto `Entry` può essere definita impostando la relativa `WidthRequest` Proprietà. Non dipendono dalla larghezza di un oggetto `Entry` definito in base al valore della relativa `Text` Proprietà.

### <a name="preventing-text-entry"></a>Impedire l'immissione di testo

È possibile impedire agli utenti di modificare il testo in un impostando [`Entry`](xref:Xamarin.Forms.Entry) la `IsReadOnly` proprietà, il cui valore predefinito è `false` , su `true` :

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> La `IsReadonly` proprietà non modifica l'aspetto visivo di un oggetto [`Entry`](xref:Xamarin.Forms.Entry) , a differenza della `IsEnabled` proprietà che modifica anche l'aspetto visivo di `Entry` in grigio.

### <a name="limiting-input-length"></a>Limitazione della lunghezza di input

La [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) proprietà può essere utilizzata per limitare la lunghezza di input consentita per l'oggetto [`Entry`](xref:Xamarin.Forms.Entry) . Questa proprietà deve essere impostata su un numero intero positivo:

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Il [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) valore 0 indica che nessun input sarà consentito e il valore `int.MaxValue` , che rappresenta il valore predefinito per un oggetto [`Entry`](xref:Xamarin.Forms.Entry) , indica che non esiste alcun limite effettivo per il numero di caratteri che è possibile immettere.

### <a name="character-spacing"></a>Spaziatura caratteri

La spaziatura dei caratteri può essere applicata a un oggetto impostando [`Entry`](xref:Xamarin.Forms.Entry) la `Entry.CharacterSpacing` proprietà su un `double` valore:

```xaml
<Entry ...
       CharacterSpacing="10" />
```

Il codice C# equivalente è il seguente:

```csharp
Entry entry = new Entry { CharacterSpacing = 10 };
```

Il risultato è che i caratteri nel testo visualizzato da [`Entry`](xref:Xamarin.Forms.Entry) sono `CharacterSpacing` unità separate da dispositivi indipendenti dal dispositivo.

> [!NOTE]
> Il `CharacterSpacing` valore della proprietà viene applicato al testo visualizzato dalle `Text` proprietà e `Placeholder` .

### <a name="password-fields"></a>Campi password

`Entry`fornisce la `IsPassword` Proprietà. Quando `IsPassword` è `true` , il contenuto del campo verrà presentato come cerchi neri:

In XAML:

```xaml
<Entry IsPassword="true" />
```

In C#:

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![Esempio di voce di password](entry-images/password.png)

I segnaposto possono essere utilizzati con le istanze di `Entry` configurate come campi password:

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

La [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) proprietà può essere utilizzata per restituire o impostare la posizione in cui verrà inserito il carattere successivo nella stringa archiviata nella [`Text`](xref:Xamarin.Forms.InputView.Text) proprietà:

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

Il valore predefinito della [`CursorPosition`](xref:Xamarin.Forms.Entry.CursorPosition) proprietà è 0, che indica che il testo verrà inserito all'inizio dell'oggetto `Entry` .

Inoltre, la [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) proprietà può essere utilizzata per restituire o impostare la lunghezza della selezione di testo all'interno di `Entry` :

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

Il valore predefinito della [`SelectionLength`](xref:Xamarin.Forms.Entry.SelectionLength) proprietà è 0, che indica che non è selezionato alcun testo.

### <a name="displaying-a-clear-button"></a>Visualizzazione di un pulsante Cancella

La `ClearButtonVisibility` proprietà può essere utilizzata per controllare se un oggetto [`Entry`](xref:Xamarin.Forms.Entry) Visualizza un pulsante Cancella, che consente all'utente di cancellare il testo. Questa proprietà deve essere impostata su un `ClearButtonVisibility` membro di enumerazione:

- `Never`indica che un pulsante Cancella non verrà mai visualizzato. Questo è il valore predefinito per la proprietà `Entry.ClearButtonVisibility`.
- `WhileEditing`indica che un pulsante Clear verrà visualizzato in [`Entry`](xref:Xamarin.Forms.Entry) , mentre ha lo stato attivo e il testo.

Nell'esempio seguente viene illustrata l'impostazione della proprietà in XAML:

```xaml
<Entry Text="Xamarin.Forms"
       ClearButtonVisibility="WhileEditing" />
```

Il codice C# equivalente è il seguente:

```csharp
var entry = new Entry { Text = "Xamarin.Forms", ClearButtonVisibility = ClearButtonVisibility.WhileEditing };
```

Gli screenshot seguenti mostrano un [`Entry`](xref:Xamarin.Forms.Entry) con il pulsante Clear abilitato:

![Screenshot di una voce con un pulsante Cancella, in iOS e Android](entry-images/entry-clear-button.png)

### <a name="customizing-the-keyboard"></a>Personalizzazione della tastiera

La tastiera visualizzata quando gli utenti interagiscono con un [`Entry`](xref:Xamarin.Forms.Entry) può essere impostata a livello di codice tramite la [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) proprietà, su una delle proprietà seguenti della [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe:

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat): usato per il testo e le posizioni in cui emoji è utile.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default): la tastiera predefinita.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email): usato per l'immissione di indirizzi di posta elettronica.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric): usato per l'immissione di numeri.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain): usato quando si immette il testo, senza alcun [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) oggetto specificato.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone): usato per l'immissione di numeri di telefono.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text): usato quando si immette il testo.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url): consente di immettere i percorsi dei file & indirizzi Web.

Per ottenere questo risultato, è possibile procedere come segue in XAML:

```xaml
<Entry Keyboard="Chat" />
```

Il codice C# equivalente è il seguente:

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
```

Esempi di ogni tastiera sono disponibili nel repository [Recipes](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) .

La [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe dispone inoltre di un [`Create`](xref:Xamarin.Forms.Keyboard.Create*) Metodo Factory che può essere utilizzato per personalizzare una tastiera specificando le maiuscole, il controllo ortografico e il comportamento dei suggerimenti. [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)i valori di enumerazione vengono specificati come argomenti per il metodo, con una `Keyboard` restituito personalizzato. L'enumerazione `KeyboardFlags` contiene i valori seguenti:

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None): nessuna funzionalità viene aggiunta alla tastiera.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence): indica che la prima lettera della prima parola di ogni frase inserita verrà automaticamente maiuscola.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck): indica che il controllo ortografico verrà eseguito sul testo immesso.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions): indica che i completamenti di parole verranno offerti sul testo immesso.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord): indica che la prima lettera di ogni parola verrà automaticamente maiuscola.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter): indica che ogni carattere verrà automaticamente in maiuscolo.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone): indica che non si verificherà alcuna maiuscola automatica.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All): indica che il controllo ortografico, i completamenti di parole e le maiuscole/minuscole vengono eseguiti nel testo immesso.

Nell'esempio di codice XAML riportato di seguito viene illustrato come personalizzare l'impostazione predefinita [`Keyboard`](xref:Xamarin.Forms.Keyboard) per offrire completamenti di parole e come capitalizzare tutti i caratteri immessi:

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

L'aspetto del tasto Return sulla tastiera soft, che viene visualizzato quando un oggetto [`Entry`](xref:Xamarin.Forms.Entry) ha lo stato attivo, può essere personalizzato impostando la [`ReturnType`](xref:Xamarin.Forms.Entry.ReturnType) proprietà su un valore dell' [`ReturnType`](xref:Xamarin.Forms.ReturnType) enumerazione:

- [`Default`](xref:Xamarin.Forms.ReturnType.Default): indica che non è richiesta alcuna chiave restituita specifica e che verrà utilizzata l'impostazione predefinita della piattaforma.
- [`Done`](xref:Xamarin.Forms.ReturnType.Done): indica una chiave di ritorno "Done".
- [`Go`](xref:Xamarin.Forms.ReturnType.Go): indica una chiave di ritorno "go".
- [`Next`](xref:Xamarin.Forms.ReturnType.Next): indica una chiave di ritorno "Next".
- [`Search`](xref:Xamarin.Forms.ReturnType.Search): indica una chiave di restituzione "Search".
- [`Send`](xref:Xamarin.Forms.ReturnType.Send): indica una chiave di invio "Send".

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

Quando viene premuto il tasto invio, [`Completed`](xref:Xamarin.Forms.Entry.Completed) viene generato l'evento e `ICommand` viene eseguito qualsiasi oggetto specificato dalla [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) Proprietà. Inoltre, tutti `object` gli specificati dalla [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) proprietà verranno passati a `ICommand` come parametro. Per altre informazioni sui comandi, vedere l'articolo sull'[interfaccia di comando](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

### <a name="enabling-and-disabling-spell-checking"></a>Abilitazione e disabilitazione del controllo ortografico

La [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) proprietà controlla se il controllo ortografico è abilitato. Per impostazione predefinita, la proprietà è impostata su `true` . Quando l'utente immette testo, vengono indicati errori di ortografia.

Tuttavia, per alcuni scenari di immissione di testo, ad esempio l'immissione di un nome utente, il controllo ortografico offre un'esperienza negativa e deve essere disabilitato impostando la [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) proprietà su `false` :

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando la [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) proprietà è impostata su `false` e non viene usata una tastiera personalizzata, il controllo ortografico nativo verrà disabilitato. Tuttavia, se [`Keyboard`](xref:Xamarin.Forms.Keyboard) è stato impostato un oggetto che disabilita il controllo ortografico, ad esempio [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat) , la `IsSpellCheckEnabled` proprietà viene ignorata. Di conseguenza, non è possibile usare la proprietà per abilitare il controllo ortografico per un `Keyboard` che lo Disabilita in modo esplicito.

### <a name="enabling-and-disabling-text-prediction"></a>Abilitazione e disabilitazione del completamento del testo

La [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) proprietà controlla se è abilitata la stima del testo e la correzione automatica del testo. Per impostazione predefinita, la proprietà è impostata su `true` . Quando l'utente immette testo, vengono presentate le stime delle parole.

Tuttavia, per alcuni scenari di immissione di testo, ad esempio l'immissione di un nome utente, la stima del testo e la correzione automatica del testo offrono un'esperienza negativa e devono essere disabilitati impostando la [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) proprietà su `false` :

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Quando la [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) proprietà è impostata su `false` e non viene utilizzata una tastiera personalizzata, la stima del testo e la correzione automatica del testo sono disabilitate. Tuttavia, se [`Keyboard`](xref:Xamarin.Forms.Keyboard) è stato impostato un oggetto che disabilita la stima del testo, la `IsTextPredictionEnabled` proprietà viene ignorata. Pertanto, la proprietà non può essere utilizzata per abilitare la stima del testo per un oggetto `Keyboard` che la Disabilita in modo esplicito.

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

Si noti che il segnaposto non è influenzato dall'oggetto specificato `TextColor` .

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

- [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged)&ndash;generato quando il testo viene modificato nella voce. Fornisce il testo prima e dopo la modifica.
- [`Completed`](xref:Xamarin.Forms.Entry.Completed)&ndash;generato quando l'utente ha terminato l'input premendo il tasto INVIO sulla tastiera.

> [!NOTE]
> La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe, da cui [`Entry`](xref:Xamarin.Forms.Entry) eredita, include anche [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) gli eventi e.

### <a name="completed"></a>Completato

L' `Completed` evento viene utilizzato per rispondere al completamento di un'interazione con una voce. `Completed`viene generato quando l'utente termina l'input con un campo premendo il tasto INVIO sulla tastiera oppure premendo il tasto TAB in UWP. Il gestore per l'evento è un gestore eventi generico che accetta il mittente e `EventArgs` :

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

Dopo che l' [`Completed`](xref:Xamarin.Forms.Entry.Completed) evento viene generato, qualsiasi `ICommand` oggetto specificato dalla [`ReturnCommand`](xref:Xamarin.Forms.Entry.ReturnCommand) proprietà viene eseguito, con l' `object` oggetto specificato dalla [`ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter) proprietà passata a `ICommand` .

### <a name="textchanged"></a>TextChanged

L' `TextChanged` evento viene utilizzato per rispondere a una modifica nel contenuto di un campo.

`TextChanged`viene generato ogni volta che viene modificato l'oggetto `Text` di `Entry` . Il gestore per l'evento accetta un'istanza di `TextChangedEventArgs` . `TextChangedEventArgs`fornisce l'accesso ai valori precedenti e nuovi di `Entry` `Text` tramite le `OldTextValue` proprietà e `NewTextValue` :

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

`TextChanged`È possibile sottoscrivere l'evento in XAML:

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
