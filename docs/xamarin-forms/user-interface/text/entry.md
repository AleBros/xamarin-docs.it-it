---
title: Voce di xamarin. Forms
description: Questo articolo illustra come usare la classe di ingresso di xamarin. Forms per accettare input di password in un'applicazione o di testo a riga singola.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2018
ms.openlocfilehash: c9be74bcc6e5d149856e06c98d3bef9a841d4bab
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667308"
---
# <a name="xamarinforms-entry"></a>Voce di xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)

_Testo a riga singola o la password di input_

Xamarin. Forms [ `Entry` ](xref:Xamarin.Forms.Entry) viene usato per l'input di testo a riga singola. Il `Entry`, ad esempio il [ `Editor` ](xref:Xamarin.Forms.Editor) visualizzare, supporta più tipi di tastiera. Inoltre, il `Entry` può essere utilizzato come un campo della password.

## <a name="display-customization"></a>Personalizzazione della visualizzazione

### <a name="setting-and-reading-text"></a>Impostazione e la lettura di testo

Il `Entry`, come altre visualizzazioni di testo-presentazione, espone il [ `Text` ](xref:Xamarin.Forms.Entry.Text) proprietà. Questa proprietà può essere utilizzata per impostare e leggere il testo visualizzato dal `Entry`. Nell'esempio seguente viene illustrata l'impostazione di `Text` proprietà in XAML:

```xaml
<Entry Text="I am an Entry" />
```

In C#:

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

Per leggere il testo, accedere il `Text` proprietà in c#:

```csharp
var text = MyEntry.Text;
```

### <a name="setting-placeholder-text"></a>Impostazione del testo segnaposto

Il [ `Entry` ](xref:Xamarin.Forms.Entry) può essere impostato su Mostra il testo del segnaposto quando non l'archiviazione dell'input dell'utente. Questa operazione viene eseguita impostando il [ `Placeholder` ](xref:Xamarin.Forms.Entry.Placeholder) proprietà a un `string`e viene spesso usato per indicare il tipo di contenuto appropriato per il `Entry`. Inoltre, il colore del testo segnaposto può essere controllato definendo i [ `PlaceholderColor` ](xref:Xamarin.Forms.Entry.PlaceholderColor) proprietà a un [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> La larghezza di un `Entry` può essere definita impostando relativo `WidthRequest` proprietà. Non basarsi sulla larghezza di un `Entry` definito in base al valore del relativo `Text` proprietà.

### <a name="preventing-text-entry"></a>Impedisce l'immissione di testo

Gli utenti è possibile impedire la modifica del testo in un [ `Entry` ](xref:Xamarin.Forms.Entry) impostando il `IsReadOnly` proprietà, che ha un valore predefinito di `false`, a `true`:

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> Il `IsReadonly` proprietà non altera l'aspetto visivo di un [ `Entry` ](xref:Xamarin.Forms.Entry), a differenza il `IsEnabled` proprietà che viene modificato anche l'aspetto visivo del `Entry` in grigio.

### <a name="limiting-input-length"></a>Limitazione della lunghezza di Input

Il [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) proprietà può essere utilizzata per limitare la lunghezza di input valido per il [ `Entry` ](xref:Xamarin.Forms.Entry). Questa proprietà deve essere impostata su un numero intero positivo:

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Oggetto [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) ha valore 0 indica che non sarà consentito alcun input e il valore `int.MaxValue`, che è il valore predefinito per un [ `Entry` ](xref:Xamarin.Forms.Entry), indica che è presente alcun limite effettivo per il numero di caratteri che possono essere immessi.

### <a name="password-fields"></a>Campi di password

`Entry` fornisce il `IsPassword` proprietà. Quando `IsPassword` è `true`, verrà visualizzato il contenuto del campo come cerchi neri:

In XAML:

```xaml
<Entry IsPassword="true" />
```

In C#:

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![](entry-images/password.png "Esempio di voce IsPassword")

Segnaposto possono essere utilizzati con istanze di `Entry` che sono configurati come campi di password:

In XAML:

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

In C#:

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![](entry-images/passwordplaceholder.png "Esempio di segnaposto e IsPassword voce")

### <a name="setting-the-cursor-position-and-text-selection-length"></a>Impostazione posizione del cursore e lunghezza selezione testo

Il [ `CursorPosition` ](xref:Xamarin.Forms.Entry.CursorPosition) proprietà può essere utilizzata per restituire o impostare la posizione in corrispondenza del quale verrà inserito il carattere successivo nella stringa archiviata nella [ `Text` ](xref:Xamarin.Forms.Entry.Text) proprietà:

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

Il valore predefinito il [ `CursorPosition` ](xref:Xamarin.Forms.Entry.CursorPosition) proprietà è 0, che indica che verrà inserito all'inizio del testo di `Entry`.

Inoltre, il [ `SelectionLength` ](xref:Xamarin.Forms.Entry.SelectionLength) proprietà può essere utilizzata per restituire o impostare la lunghezza della selezione di testo all'interno di `Entry`:

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

Il valore predefinito di [ `SelectionLength` ](xref:Xamarin.Forms.Entry.SelectionLength) proprietà è 0, che indica che è selezionato alcun testo.

### <a name="customizing-the-keyboard"></a>Personalizzazione di tastiera

La tastiera che viene visualizzata quando gli utenti interagiscono con un' [ `Entry` ](xref:Xamarin.Forms.Entry) possono essere impostati a livello di codice usando la [ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard) proprietà per una delle seguenti proprietà dal [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) classe:

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) : usato per inviato e posizioni in cui sono utili emoji.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) – la tastiera predefinita.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) : usato durante l'immissione degli indirizzi di posta elettronica.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) : usato durante l'immissione di numeri.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) : usato durante l'immissione di testo, senza alcuna [ `KeyboardFlags` ](xref:Xamarin.Forms.KeyboardFlags) specificato.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) : usato durante l'immissione di numeri di telefono.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) : usato durante l'immissione di testo.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) : usato per l'immissione di percorsi di file e gli indirizzi web.

Ciò può essere eseguito in XAML come indicato di seguito:

```xaml
<Entry Keyboard="Chat" />
```

Il codice c# equivalente è:

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
```

Esempi di ogni tastiera sono reperibili nel nostro [recipe](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) repository.

Il [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) classe include anche una [ `Create` ](xref:Xamarin.Forms.Keyboard.Create*) metodo factory che può essere utilizzato per personalizzare una tastiera, specificando il comportamento di maiuscole/minuscole, controllo ortografico e suggerimenti. [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) i valori di enumerazione sono specificati come argomenti al metodo, con un oggetto personalizzato `Keyboard` restituiti. Il `KeyboardFlags` enumerazione contiene i valori seguenti:

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) – non vengono aggiunte funzionalità per la tastiera.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) : indica che la prima lettera della parola prima di ogni frase immessa sarà essere automaticamente la prima lettera maiuscola.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) : indica il controllo ortografico verrà eseguita nel testo immesso.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) – indica tale parola completamenti verranno offerto sul testo immesso.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) : indica che la prima lettera di ogni parola viene possibile automaticamente la prima lettera maiuscola.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) : indica che ogni carattere viene possibile automaticamente la prima lettera maiuscola.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) : indica che si verificherà alcuna conversione automatica.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) : indica che si verifichi controllo ortografico, i completamenti delle parole e uso delle maiuscole frase nel testo immesso.

Esempio di codice XAML seguente viene illustrato come personalizzare il valore predefinito [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) per offrire i completamenti delle parole e converte in maiuscolo tutti i caratteri immessi:

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

Il codice c# equivalente è:

```csharp
var entry = new Entry { Placeholder = "Enter text here" };
entry.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

#### <a name="customizing-the-return-key"></a>Personalizzare il tasto INVIO

L'aspetto del tasto INVIO sulla tastiera temporanea, che viene visualizzato quando un [ `Entry` ](xref:Xamarin.Forms.Entry) ha lo stato attivo, può essere personalizzato tramite l'impostazione di [ `ReturnType` ](xref:Xamarin.Forms.Entry.ReturnType) proprietà su un valore di [ `ReturnType` ](xref:Xamarin.Forms.ReturnType) enumerazione:

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) : indica che è richiesto alcun codice restituito specifico e che verrà utilizzato il valore predefinito di platform.
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) – indica una chiave return "Completata".
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) – indica una chiave return "Vai".
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) – indica una chiave return "Avanti".
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) – indica una chiave return "Search".
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) – indica una chiave return "Send".

Nell'esempio XAML seguente viene illustrato come impostare la chiave return:

```xaml
<Entry ReturnType="Send" />
```

Il codice c# equivalente è:

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> L'aspetto esatto della chiave restituita dipende dalla piattaforma. In iOS, la chiave restituita è un pulsante basata su testo. Tuttavia, nelle piattaforme Windows universale e Android, il tasto INVIO è un pulsante basata sull'icona.

Quando viene premuto il tasto INVIO, il [ `Completed` ](xref:Xamarin.Forms.Entry.Completed) viene generato l'evento ed eventuali `ICommand` specificato per il [ `ReturnCommand` ](xref:Xamarin.Forms.Entry.ReturnCommand) proprietà viene eseguita. Inoltre, qualsiasi `object` specificato dal [ `ReturnCommandParameter` ](xref:Xamarin.Forms.Entry.ReturnCommandParameter) verrà passata alla proprietà il `ICommand` come parametro. Per altre informazioni sui comandi, vedere [l'interfaccia di comando](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

### <a name="enabling-and-disabling-spell-checking"></a>Abilitazione e disabilitazione di controllo ortografico

Il [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) proprietà controlla se il controllo ortografico è abilitato. Per impostazione predefinita, la proprietà è impostata su `true`. Quando l'utente ne immette di testo, sono indicati gli errori di ortografia.

Tuttavia, per alcuni scenari di voce di testo, ad esempio immettendo un nome utente, controllo ortografico offre un'esperienza negativa e devono essere disabilitato impostando il [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) proprietà `false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando la [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) è impostata su `false`e non viene usata una tastiera personalizzata, il correttore ortografico nativo verrà disabilitato. Tuttavia, se un [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) ha stato gruppo che disabilita il controllo ortografico controllo, ad esempio [ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat), il `IsSpellCheckEnabled` proprietà viene ignorata. Pertanto, la proprietà non è utilizzabile per abilitare il controllo ortografico per un `Keyboard` che disabilita in modo esplicito.

### <a name="enabling-and-disabling-text-prediction"></a>Abilitazione e disabilitazione di completamento del testo

Il [ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) proprietà controlla se il completamento del testo e automatico è abilitata la correzione del testo. Per impostazione predefinita, la proprietà è impostata su `true`. Quando l'utente ne immette di testo, vengono presentate le stime di word.

Tuttavia, per alcuni scenari di immissione di testo, quali l'immissione di un nome utente, il completamento del testo e testo automatico correzione offre un'esperienza negativa e devono essere disabilitata impostando il [ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) proprietà `false`:

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Quando la [ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) è impostata su `false`, e non è in corso una tastiera personalizzata usato, il completamento del testo e automatizzate la correzione del testo è disabilitata. Tuttavia, se un [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) è stato impostato il completamento del testo che viene disabilitato, il `IsTextPredictionEnabled` proprietà viene ignorata. Pertanto, la proprietà non consente di abilitare il completamento del testo per un `Keyboard` che disabilita in modo esplicito.

### <a name="colors"></a>Colori

Voce può essere impostata su usare uno sfondo personalizzato e colori del testo tramite le proprietà associabili ai seguenti:

- **TextColor** &ndash; imposta il colore del testo.
- **BackgroundColor** &ndash; imposta il colore visualizzato dietro il testo.

Particolare attenzione è necessario assicurarsi che i colori saranno utilizzabili in ogni piattaforma. Poiché ogni piattaforma ha valori predefiniti diversi per i colori di sfondo e testo, è necessario spesso sia se è impostata su uno.

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

![](entry-images/textcolor.png "Esempio di voce TextColor")

Si noti che il segnaposto non è influenzato specificato `TextColor`.

Per impostare il colore di sfondo in XAML:

```xaml
<Entry BackgroundColor="#2c3e50" />
```

In C#:

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![](entry-images/textbackgroundcolor.png "Esempio di voce BackgroundColor")

Prestare attenzione per assicurarsi che i colori di sfondo e del testo che è scegliere possono essere utilizzati in ogni piattaforma e non nascondano qualsiasi testo segnaposto.

## <a name="events-and-interactivity"></a>Eventi e interattività

Voce espone due eventi:

- [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) &ndash; generato quando viene modificato il testo della voce. Fornisce il testo prima e dopo la modifica.
- [`Completed`](xref:Xamarin.Forms.Entry.Completed) &ndash; generato quando l'utente ha terminato di input premendo il tasto INVIO sulla tastiera.

> [!NOTE]
> Il [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) (classe), da cui [ `Entry` ](xref:Xamarin.Forms.Entry) eredita, ha anche [ `Focused` ](xref:Xamarin.Forms.VisualElement.Focused) e [ `Unfocused` ](xref:Xamarin.Forms.VisualElement.Unfocused)gli eventi.

### <a name="completed"></a>Operazione completata

Il `Completed` evento viene utilizzato per rispondere al completamento di un'interazione con una voce. `Completed` viene generato quando l'utente termina con un campo di input premendo il tasto INVIO sulla tastiera. Il gestore dell'evento è un gestore eventi generici, accettando il mittente e `EventArgs`:

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

L'evento completato può essere effettuata la sottoscrizione in XAML:

```xaml
<Entry Completed="Entry_Completed" />
```

e c#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

Dopo il [ `Completed` ](xref:Xamarin.Forms.Entry.Completed) evento viene generato, qualsiasi `ICommand` specificato dal [ `ReturnCommand` ](xref:Xamarin.Forms.Entry.ReturnCommand) proprietà viene eseguita, con la `object` specificato da di [ `ReturnCommandParameter` ](xref:Xamarin.Forms.Entry.ReturnCommandParameter) passati alla proprietà di `ICommand`.

### <a name="textchanged"></a>TextChanged

Il `TextChanged` evento viene utilizzato per rispondere a una modifica del contenuto di un campo.

`TextChanged` viene generato ogni volta che il `Text` del `Entry` le modifiche. Il gestore dell'evento accetta un'istanza di `TextChangedEventArgs`. `TextChangedEventArgs` fornisce l'accesso ai valori vecchi e nuovi del `Entry` `Text` tramite il `OldTextValue` e `NewTextValue` proprietà:

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

Il `TextChanged` possono essere sottoscritti eventi in XAML:

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

e c#:

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```


## <a name="related-links"></a>Collegamenti correlati

- [Testo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Voce di API](xref:Xamarin.Forms.Entry)
