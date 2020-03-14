---
title: Editor di xamarin. Forms
description: Questo articolo illustra come usare il controllo dell'Editor di xamarin. Forms per accettare l'input di testo su più righe in un'applicazione.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/26/2019
ms.openlocfilehash: 1ae176cfebdde31038c30895d1bf562ff3396eaa
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305535"
---
# <a name="xamarinforms-editor"></a>Editor di xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Input di testo su più righe_

Il controllo [`Editor`](xref:Xamarin.Forms.Editor) viene usato per accettare l'input su più righe. Questo articolo riguarda:

- **[Personalizzazione](#customization)** &ndash; opzioni di tastiera e colore.
- **[Interattività](#interactivity)** &ndash; gli eventi che possono essere ascoltati per fornire interattività.

## <a name="customization"></a>Personalizzazione

### <a name="setting-and-reading-text"></a>Impostazione e la lettura di testo

Il [`Editor`](xref:Xamarin.Forms.Editor), analogamente ad altre visualizzazioni di presentazione del testo, espone la proprietà `Text`. Questa proprietà può essere utilizzata per impostare e leggere il testo presentato dal `Editor`. Nell'esempio seguente viene illustrata l'impostazione della proprietà `Text` in XAML:

```xaml
<Editor Text="I am an Editor" />
```

In C#:

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Per leggere il testo, accedere alla proprietà `Text` C#in:

```csharp
var text = MyEditor.Text;
```

### <a name="setting-placeholder-text"></a>Impostazione del testo segnaposto

Il [`Editor`](xref:Xamarin.Forms.Editor) può essere impostato in modo da visualizzare il testo segnaposto quando non archivia l'input dell'utente. Questa operazione viene eseguita impostando la proprietà [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) su un `string`e viene spesso usata per indicare il tipo di contenuto appropriato per il `Editor`. Inoltre, il colore del testo segnaposto può essere controllato impostando la proprietà [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) su un [`Color`](xref:Xamarin.Forms.Color):

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="preventing-text-entry"></a>Impedisci immissione di testo

È possibile impedire agli utenti di modificare il testo in un [`Editor`](xref:Xamarin.Forms.Editor) impostando la proprietà `IsReadOnly`, che ha un valore predefinito di `false`, per `true`:

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> La proprietà `IsReadonly` non modifica l'aspetto visivo di un [`Editor`](xref:Xamarin.Forms.Editor), a differenza della proprietà `IsEnabled` che modifica anche l'aspetto visivo del `Editor` in grigio.

### <a name="limiting-input-length"></a>Limitazione della lunghezza di Input

È possibile utilizzare la proprietà [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) per limitare la lunghezza di input consentita per l' [`Editor`](xref:Xamarin.Forms.Editor). Questa proprietà deve essere impostata su un numero intero positivo:

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

Il valore 0 di [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) proprietà indica che nessun input sarà consentito e il valore `int.MaxValue`, che rappresenta il valore predefinito per un [`Editor`](xref:Xamarin.Forms.Editor), indica che non esiste alcun limite effettivo per il numero di caratteri che è possibile immettere.

### <a name="character-spacing"></a>Spaziatura caratteri

La spaziatura dei caratteri può essere applicata a un [`Editor`](xref:Xamarin.Forms.Editor) impostando la proprietà `Editor.CharacterSpacing` su un valore `double`:

```xaml
<Editor ...
        CharacterSpacing="10" />
```

Il codice C# equivalente è il seguente:

```csharp
Editor editor = new editor { CharacterSpacing = 10 };
```

Il risultato è che i caratteri nel testo visualizzato dal [`Editor`](xref:Xamarin.Forms.Editor) sono spaziati `CharacterSpacing` unità indipendenti dal dispositivo.

> [!NOTE]
> Il valore della proprietà `CharacterSpacing` viene applicato al testo visualizzato dalle proprietà `Text` e `Placeholder`.

### <a name="auto-sizing-an-editor"></a>Un Editor di ridimensionamento automatico

È possibile creare un [`Editor`](xref:Xamarin.Forms.Editor) per ridimensionare automaticamente il contenuto impostando la proprietà [`Editor.AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) su [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), ovvero un valore dell'enumerazione [`EditoAutoSizeOption`](xref:Xamarin.Forms.EditorAutoSizeOption) . Questa enumerazione contiene due valori:

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) indica che il ridimensionamento automatico è disabilitato e è il valore predefinito.
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) indica che il ridimensionamento automatico è abilitato.

Ciò può essere eseguito nel codice come indicato di seguito:

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

Quando è abilitata la funzionalità di ridimensionamento automatico, l'altezza del [`Editor`](xref:Xamarin.Forms.Editor) aumenterà quando l'utente riempie il testo e l'altezza diminuirà quando l'utente elimina il testo.

> [!NOTE]
> Se è stata impostata la proprietà [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) , non è possibile ridimensionare automaticamente un [`Editor`](xref:Xamarin.Forms.Editor) .

### <a name="customizing-the-keyboard"></a>Personalizzazione di tastiera

La tastiera visualizzata quando gli utenti interagiscono con un [`Editor`](xref:Xamarin.Forms.Editor) possono essere impostate a livello di codice tramite la proprietà [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) , a una delle seguenti proprietà della classe [`Keyboard`](xref:Xamarin.Forms.Keyboard) :

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
<Editor Keyboard="Chat" />
```

Il codice C# equivalente è il seguente:

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
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
<Editor>
    <Editor.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Editor.Keyboard>
</Editor>
```

Il codice C# equivalente è il seguente:

```csharp
var editor = new Editor();
editor.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="enabling-and-disabling-spell-checking"></a>Abilitazione e disabilitazione di controllo ortografico

La proprietà [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) controlla se il controllo ortografico è abilitato. Per impostazione predefinita, la proprietà è impostata su `true`. Quando l'utente ne immette di testo, sono indicati gli errori di ortografia.

Tuttavia, per alcuni scenari di immissione di testo, ad esempio l'immissione di un nome utente, il controllo ortografico offre un'esperienza negativa e pertanto deve essere disabilitato impostando la proprietà [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) su `false`:

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando la proprietà [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) è impostata su `false`e non viene usata una tastiera personalizzata, il controllo ortografico nativo verrà disabilitato. Tuttavia, se è stato impostato un [`Keyboard`](xref:Xamarin.Forms.Keyboard) che disabilita il controllo ortografico, ad esempio [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat), la proprietà `IsSpellCheckEnabled` viene ignorata. Di conseguenza, non è possibile usare la proprietà per abilitare il controllo ortografico per una `Keyboard` che la Disabilita in modo esplicito.

### <a name="enabling-and-disabling-text-prediction"></a>Abilitazione e disabilitazione di completamento del testo

La proprietà `IsTextPredictionEnabled` controlla se è abilitata la stima del testo e la correzione automatica del testo. Per impostazione predefinita, la proprietà è impostata su `true`. Quando l'utente ne immette di testo, vengono presentate le stime di word.

Tuttavia, per alcuni scenari di immissione di testo, ad esempio l'immissione di un nome utente, la stima del testo e la correzione automatica del testo offrono un'esperienza negativa ed è necessario disabilitarla impostando la proprietà `IsTextPredictionEnabled` su `false`:

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Quando la proprietà `IsTextPredictionEnabled` è impostata su `false`e non viene utilizzata una tastiera personalizzata, la stima del testo e la correzione automatica del testo sono disabilitate. Tuttavia, se è stata impostata una [`Keyboard`](xref:Xamarin.Forms.Keyboard) che disabilita la stima del testo, la proprietà `IsTextPredictionEnabled` viene ignorata. Pertanto, la proprietà non può essere utilizzata per abilitare la stima del testo per una `Keyboard` che la Disabilita in modo esplicito.

### <a name="colors"></a>Colori

è possibile impostare `Editor` per utilizzare un colore di sfondo personalizzato tramite la proprietà `BackgroundColor`. Particolare attenzione è necessario assicurarsi che i colori saranno utilizzabili in ogni piattaforma. Poiché ogni piattaforma ha valori predefiniti diversi per il colore del testo, si potrebbe essere necessario impostare un colore di sfondo personalizzato per ogni piattaforma. Per ulteriori informazioni sull'ottimizzazione dell'interfaccia utente per ogni piattaforma, vedere [utilizzo delle modifiche alla piattaforma](~/xamarin-forms/platform/device.md) .

In C#:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        //dark blue on UWP & Android, light blue on iOS
        var editor = new Editor { BackgroundColor = Device.RuntimePlatform == Device.iOS ? Color.FromHex("#A4EAFF") : Color.FromHex("#2c3e50") };
        layout.Children.Add(editor);
    }
}
```

In XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="TextSample.EditorPage"
    Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor>
                <Editor.BackgroundColor>
                    <OnPlatform x:TypeArguments="x:Color">
                        <On Platform="iOS" Value="#a4eaff" />
                        <On Platform="Android, UWP" Value="#2c3e50" />
                    </OnPlatform>
                </Editor.BackgroundColor>
            </Editor>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

![](editor-images/textbackgroundcolor.png "Editor with BackgroundColor Example")

Assicurarsi che i colori di sfondo e del testo che è scegliere possono essere utilizzati in ogni piattaforma e non nascondano qualsiasi testo segnaposto.

## <a name="interactivity"></a>Interattività

`Editor` espone due eventi:

- [TextChanged](xref:Xamarin.Forms.InputView.TextChanged) &ndash; generato quando il testo viene modificato nell'editor. Fornisce il testo prima e dopo la modifica.
- [Completato](xref:Xamarin.Forms.Editor.Completed) &ndash; generato quando l'utente ha terminato l'input premendo il tasto di ritorno sulla tastiera.

> [!NOTE]
> La classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) , da cui eredita [`Entry`](xref:Xamarin.Forms.Entry) , dispone anche di eventi di [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) e [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) .

### <a name="completed"></a>Operazione completata

L'evento `Completed` viene utilizzato per rispondere al completamento di un'interazione con una `Editor`. `Completed` viene generato quando l'utente termina l'input con un campo immettendo il tasto di restituzione sulla tastiera (o premendo il tasto TAB in UWP). Il gestore per l'evento è un gestore eventi generico che accetta il mittente e `EventArgs`:

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

L'evento completato può essere sottoscritti nel codice e XAML:

In C#:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.Completed += EditorCompleted;
        layout.Children.Add(editor);
    }
}
```

In XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor Completed="EditorCompleted" />
        </StackLayout>
    </ContentPage.Content>
</Contentpage>
```

### <a name="textchanged"></a>TextChanged

L'evento `TextChanged` viene utilizzato per rispondere a una modifica nel contenuto di un campo.

`TextChanged` viene generato ogni volta che viene modificato il `Text` dell'`Editor`. Il gestore per l'evento accetta un'istanza di `TextChangedEventArgs`. `TextChangedEventArgs` fornisce l'accesso ai valori precedenti e nuovi del `Text` di `Editor` tramite le proprietà `OldTextValue` e `NewTextValue`:

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

L'evento completato può essere sottoscritti nel codice e XAML:

Nel codice:

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.TextChanged += EditorTextChanged;
        layout.Children.Add(editor);
    }
}
```

In XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor TextChanged="EditorTextChanged" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

## <a name="related-links"></a>Collegamenti correlati

- [Testo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [API Editor](xref:Xamarin.Forms.Editor)
