---
title: Editor di xamarin. Forms
description: Questo articolo illustra come usare il controllo dell'Editor di xamarin. Forms per accettare l'input di testo su più righe in un'applicazione.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2018
ms.openlocfilehash: 684bdb21309028e02362dcc6bfd8c91c539430bd
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770175"
---
# <a name="xamarinforms-editor"></a>Editor di xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Input di testo multilinea_

Il [`Editor`](xref:Xamarin.Forms.Editor) controllo viene utilizzato per accettare l'input multiriga. Questo articolo illustra le attività seguenti:

- **[Personalizzazione](#customization)**  &ndash; opzioni della tastiera e il colore.
- **[Interattività](#interactivity)**  &ndash; gli eventi che possono restare in ascolto per fornire l'interattività.

## <a name="customization"></a>Personalizzazione

### <a name="setting-and-reading-text"></a>Impostazione e la lettura di testo

Il [`Editor`](xref:Xamarin.Forms.Editor), come altre visualizzazioni di testo-presentazione, espone la `Text` proprietà. Questa proprietà può essere utilizzata per impostare e leggere il testo visualizzato dal `Editor`. Nell'esempio seguente viene illustrata l'impostazione di `Text` proprietà in XAML:

```xaml
<Editor Text="I am an Editor" />
```

In C#:

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Per leggere il testo, accedere il `Text` proprietà in C#:

```csharp
var text = MyEditor.Text;
```

### <a name="setting-placeholder-text"></a>Impostazione del testo segnaposto

Il [`Editor`](xref:Xamarin.Forms.Editor) può essere impostato su Mostra il testo del segnaposto quando non l'archiviazione dell'input dell'utente. Questa operazione viene eseguita impostando il [`Placeholder`](xref:Xamarin.Forms.Editor.Placeholder) proprietà a un `string`e viene spesso usato per indicare il tipo di contenuto appropriato per il `Editor`. Inoltre, il colore del testo segnaposto può essere controllato definendo i [`PlaceholderColor`](xref:Xamarin.Forms.Editor.PlaceholderColor) proprietà a un [`Color`](xref:Xamarin.Forms.Color):

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="preventing-text-entry"></a>Impedisci immissione di testo

È possibile impedire agli utenti di modificare il testo in [`Editor`](xref:Xamarin.Forms.Editor) un impostando `IsReadOnly` la proprietà, il cui valore predefinito `true`è ,su:`false`

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> La `IsReadonly` proprietà non modifica l'aspetto visivo di un oggetto [`Editor`](xref:Xamarin.Forms.Editor), `Editor` a differenza della `IsEnabled` proprietà che modifica anche l'aspetto visivo di in grigio.

### <a name="limiting-input-length"></a>Limitazione della lunghezza di Input

Il [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) proprietà può essere utilizzata per limitare la lunghezza di input valido per il [`Editor`](xref:Xamarin.Forms.Editor). Questa proprietà deve essere impostata su un numero intero positivo:

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

Oggetto [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) ha valore 0 indica che non sarà consentito alcun input e il valore `int.MaxValue`, che è il valore predefinito per un [`Editor`](xref:Xamarin.Forms.Editor), indica che è presente alcun limite effettivo per il numero di caratteri che possono essere immessi.

### <a name="auto-sizing-an-editor"></a>Un Editor di ridimensionamento automatico

Un' [`Editor`](xref:Xamarin.Forms.Editor) può essere effettuata per il ridimensionamento automatico in base al contenuto impostando le [`Editor.AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) proprietà [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges), che è un valore della [`EditoAutoSizeOption`](xref:Xamarin.Forms.EditorAutoSizeOption) enumerazione. Questa enumerazione contiene due valori:

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) indica che il ridimensionamento automatico è disabilitato e il valore predefinito.
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) indica che il ridimensionamento automatico è abilitato.

Ciò può essere eseguito nel codice come indicato di seguito:

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

Quando il ridimensionamento automatico è abilitato, l'altezza, la [`Editor`](xref:Xamarin.Forms.Editor) aumenta quando l'utente vi inserisce testo e l'altezza diminuirà quando l'utente elimina il testo.

> [!NOTE]
> Un' [`Editor`](xref:Xamarin.Forms.Editor) verrà se non il ridimensionamento automatico la [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) proprietà è stata impostata.

### <a name="customizing-the-keyboard"></a>Personalizzazione di tastiera

La tastiera che viene visualizzata quando gli utenti interagiscono con un' [`Editor`](xref:Xamarin.Forms.Editor) possono essere impostati a livello di codice usando la [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) proprietà per una delle seguenti proprietà dal [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe:

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) : usato per inviato e posizioni in cui sono utili emoji.
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) - tastiera predefinita.
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) - usata per l'immissione di indirizzi di posta elettronica.
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) - usata per l'immissione di numeri.
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) - usata per l'immissione di testo, senza [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) specificati.
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) - usata per l'immissione di numeri di telefono.
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) - usata per l'immissione di testo.
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) - usata per l'immissione di percorsi di file e indirizzi Web.

Ciò può essere eseguito in XAML come indicato di seguito:

```xaml
<Editor Keyboard="Chat" />
```

Il codice C# equivalente è:

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
```

Esempi di ogni tastiera sono reperibili nel nostro [recipe](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry) repository.

Il [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe include anche una [`Create`](xref:Xamarin.Forms.Keyboard.Create*) metodo factory che può essere utilizzato per personalizzare una tastiera, specificando il comportamento di maiuscole/minuscole, controllo ortografico e suggerimenti. I valori di enumerazione [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) vengono specificati come argomenti del metodo, con la restituzione di un elemento `Keyboard` personalizzato. Il `KeyboardFlags` enumerazione contiene i valori seguenti:

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) - non vengono aggiunte funzionalità alla tastiera.
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) - indica che la prima lettera della prima parola di ogni frase immessa verrà automaticamente scritta in maiuscolo.
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) - indica che verrà eseguito il controllo ortografico sul testo immesso.
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) – indica che verranno offerti completamenti di parole per il testo immesso.
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) - indica che la prima lettera di ogni parola verrà automaticamente scritta in maiuscolo.
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) - indica che ogni carattere verrà scritto automaticamente in maiuscolo.
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) - indica che non verrà applicata automaticamente la conversione in maiuscolo.
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) - indica che il controllo ortografico, i completamenti delle parole e la conversione in maiuscolo per le frasi verranno applicati al testo immesso.

Esempio di codice XAML seguente viene illustrato come personalizzare il valore predefinito [`Keyboard`](xref:Xamarin.Forms.Keyboard) per offrire i completamenti delle parole e converte in maiuscolo tutti i caratteri immessi:

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

Il codice C# equivalente è:

```csharp
var editor = new Editor();
editor.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="enabling-and-disabling-spell-checking"></a>Abilitazione e disabilitazione di controllo ortografico

Il [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) proprietà controlla se il controllo ortografico è abilitato. Per impostazione predefinita, la proprietà è impostata su `true`. Quando l'utente ne immette di testo, sono indicati gli errori di ortografia.

Tuttavia, per alcuni scenari di voce di testo, ad esempio immettendo un nome utente, controllo ortografico fornisce un'esperienza negativa e pertanto devono essere disabilitate impostando il [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) proprietà `false`:

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando la [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) è impostata su `false`e non viene usata una tastiera personalizzata, il correttore ortografico nativo verrà disabilitato. Tuttavia, se un [`Keyboard`](xref:Xamarin.Forms.Keyboard) ha stato gruppo che disabilita il controllo ortografico controllo, ad esempio [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat), il `IsSpellCheckEnabled` proprietà viene ignorata. Pertanto, la proprietà non è utilizzabile per abilitare il controllo ortografico per un `Keyboard` che disabilita in modo esplicito.

### <a name="enabling-and-disabling-text-prediction"></a>Abilitazione e disabilitazione di completamento del testo

La `IsTextPredictionEnabled` proprietà controlla se è abilitata la stima del testo e la correzione automatica del testo. Per impostazione predefinita, la proprietà è impostata su `true`. Quando l'utente ne immette di testo, vengono presentate le stime di word.

Tuttavia, per alcuni scenari di immissione di testo, ad esempio l'immissione di un nome utente, la stima del testo e la correzione automatica del testo offrono un'esperienza `IsTextPredictionEnabled` negativa e `false`devono essere disabilitati impostando la proprietà su:

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Quando la `IsTextPredictionEnabled` proprietà è impostata su `false`e non viene utilizzata una tastiera personalizzata, la stima del testo e la correzione automatica del testo sono disabilitate. Tuttavia, se un [`Keyboard`](xref:Xamarin.Forms.Keyboard) è stato impostato il completamento del testo che viene disabilitato, il `IsTextPredictionEnabled` proprietà viene ignorata. Pertanto, la proprietà non consente di abilitare il completamento del testo per un `Keyboard` che disabilita in modo esplicito.

### <a name="colors"></a>Colori

`Editor` può essere impostato su utilizza un colore di sfondo personalizzato tramite il `BackgroundColor` proprietà. Particolare attenzione è necessario assicurarsi che i colori saranno utilizzabili in ogni piattaforma. Poiché ogni piattaforma ha valori predefiniti diversi per il colore del testo, si potrebbe essere necessario impostare un colore di sfondo personalizzato per ogni piattaforma. Visualizzare [funziona con modifiche avanzate della piattaforma](~/xamarin-forms/platform/device.md) per altre informazioni sull'ottimizzazione dell'interfaccia utente per ogni piattaforma.

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

![](editor-images/textbackgroundcolor.png "Editor con esempio BackgroundColor")

Assicurarsi che i colori di sfondo e del testo che è scegliere possono essere utilizzati in ogni piattaforma e non nascondano qualsiasi testo segnaposto.

## <a name="interactivity"></a>Interattività

`Editor` espone due eventi:

- [TextChanged](xref:Xamarin.Forms.Editor.TextChanged) &ndash; generato quando viene modificato il testo nell'editor. Fornisce il testo prima e dopo la modifica.
- [Completare](xref:Xamarin.Forms.Editor.Completed) &ndash; generato quando l'utente ha terminato di input premendo il tasto INVIO sulla tastiera.

> [!NOTE]
> La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe, da cui [`Entry`](xref:Xamarin.Forms.Entry) eredita, include [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) anche gli [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) eventi e.

### <a name="completed"></a>Operazione completata

Il `Completed` evento viene utilizzato per rispondere al completamento di un'interazione con un `Editor`. `Completed`viene generato quando l'utente termina l'input con un campo immettendo il tasto di ritorno sulla tastiera (o premendo il tasto TAB in UWP). Il gestore dell'evento è un gestore eventi generici, accettando il mittente e `EventArgs`:

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

Il `TextChanged` evento viene utilizzato per rispondere a una modifica del contenuto di un campo.

`TextChanged` viene generato ogni volta che il `Text` del `Editor` le modifiche. Il gestore dell'evento accetta un'istanza di `TextChangedEventArgs`. `TextChangedEventArgs` fornisce l'accesso ai valori vecchi e nuovi del `Editor` `Text` tramite il `OldTextValue` e `NewTextValue` proprietà:

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

L'evento completato può essere sottoscritti nel codice e XAML:

In codice:

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
- [Editor di API](xref:Xamarin.Forms.Editor)
