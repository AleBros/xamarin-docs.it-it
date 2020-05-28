---
title: Xamarin.FormsEditor
description: Questo articolo illustra come usare il Xamarin.Forms controllo editor per accettare l'input di testo su più righe in un'applicazione.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 02749c9f8f55427bb1742e78464bbc003f1f7358
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136175"
---
# <a name="xamarinforms-editor"></a>Xamarin.FormsEditor

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Input di testo su più righe_

Il [`Editor`](xref:Xamarin.Forms.Editor) controllo viene usato per accettare l'input su più righe. Questo articolo riguarda:

- **[Personalizzazione](#customization)** &ndash; di opzioni della tastiera e del colore.
- **[Interattività](#interactivity)** &ndash; eventi che possono essere ascoltati per fornire interattività.

## <a name="customization"></a>Personalizzazione

### <a name="setting-and-reading-text"></a>Impostazione e lettura di testo

, [`Editor`](xref:Xamarin.Forms.Editor) Come altre visualizzazioni di presentazione del testo, espone la `Text` Proprietà. Questa proprietà può essere utilizzata per impostare e leggere il testo presentato da `Editor` . Nell'esempio seguente viene illustrata l'impostazione della `Text` Proprietà in XAML:

```xaml
<Editor Text="I am an Editor" />
```

In C#:

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Per leggere il testo, accedere alla `Text` Proprietà in C#:

```csharp
var text = MyEditor.Text;
```

### <a name="setting-placeholder-text"></a>Impostazione del testo segnaposto

Il [`Editor`](xref:Xamarin.Forms.Editor) parametro può essere impostato in modo da visualizzare il testo segnaposto quando non archivia l'input dell'utente. Questa operazione viene eseguita impostando la [`Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) proprietà su un oggetto `string` e viene spesso usata per indicare il tipo di contenuto appropriato per `Editor` . Inoltre, il colore del testo segnaposto può essere controllato impostando la [`PlaceholderColor`](xref:Xamarin.Forms.InputView.PlaceholderColor) proprietà su [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="preventing-text-entry"></a>Impedire l'immissione di testo

È possibile impedire agli utenti di modificare il testo in un impostando [`Editor`](xref:Xamarin.Forms.Editor) la `IsReadOnly` proprietà, il cui valore predefinito è `false` , su `true` :

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> La `IsReadonly` proprietà non modifica l'aspetto visivo di un oggetto [`Editor`](xref:Xamarin.Forms.Editor) , a differenza della `IsEnabled` proprietà che modifica anche l'aspetto visivo di `Editor` in grigio.

### <a name="limiting-input-length"></a>Limitazione della lunghezza di input

La [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) proprietà può essere utilizzata per limitare la lunghezza di input consentita per l'oggetto [`Editor`](xref:Xamarin.Forms.Editor) . Questa proprietà deve essere impostata su un numero intero positivo:

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

Il [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) valore 0 indica che nessun input sarà consentito e il valore `int.MaxValue` , che rappresenta il valore predefinito per un oggetto [`Editor`](xref:Xamarin.Forms.Editor) , indica che non esiste alcun limite effettivo per il numero di caratteri che è possibile immettere.

### <a name="character-spacing"></a>Spaziatura caratteri

La spaziatura dei caratteri può essere applicata a un oggetto impostando [`Editor`](xref:Xamarin.Forms.Editor) la `Editor.CharacterSpacing` proprietà su un `double` valore:

```xaml
<Editor ...
        CharacterSpacing="10" />
```

Il codice C# equivalente è il seguente:

```csharp
Editor editor = new editor { CharacterSpacing = 10 };
```

Il risultato è che i caratteri nel testo visualizzato da [`Editor`](xref:Xamarin.Forms.Editor) sono `CharacterSpacing` unità separate da dispositivi indipendenti dal dispositivo.

> [!NOTE]
> Il `CharacterSpacing` valore della proprietà viene applicato al testo visualizzato dalle `Text` proprietà e `Placeholder` .

### <a name="auto-sizing-an-editor"></a>Ridimensionamento automatico di un editor

[`Editor`](xref:Xamarin.Forms.Editor)È possibile impostare una dimensione automatica sul contenuto impostando la [`Editor.AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) proprietà su [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) , che è un valore dell' [`EditoAutoSizeOption`](xref:Xamarin.Forms.EditorAutoSizeOption) enumerazione. Questa enumerazione ha due valori:

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled)indica che il ridimensionamento automatico è disabilitato e è il valore predefinito.
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges)indica che il ridimensionamento automatico è abilitato.

Questa operazione può essere eseguita nel codice nel modo seguente:

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

Quando il ridimensionamento automatico è abilitato, l'altezza di aumenterà [`Editor`](xref:Xamarin.Forms.Editor) quando l'utente riempie il testo e l'altezza diminuirà quando l'utente elimina il testo.

> [!NOTE]
> [`Editor`](xref:Xamarin.Forms.Editor)Se la [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) proprietà è stata impostata, non viene automaticamente ridimensionata.

### <a name="customizing-the-keyboard"></a>Personalizzazione della tastiera

La tastiera visualizzata quando gli utenti interagiscono con un [`Editor`](xref:Xamarin.Forms.Editor) può essere impostata a livello di codice tramite la [`Keyboard`](xref:Xamarin.Forms.InputView.Keyboard) proprietà, su una delle proprietà seguenti della [`Keyboard`](xref:Xamarin.Forms.Keyboard) classe:

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
<Editor Keyboard="Chat" />
```

Il codice C# equivalente è il seguente:

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
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

### <a name="enabling-and-disabling-spell-checking"></a>Abilitazione e disabilitazione del controllo ortografico

La [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) proprietà controlla se il controllo ortografico è abilitato. Per impostazione predefinita, la proprietà è impostata su `true` . Quando l'utente immette testo, vengono indicati errori di ortografia.

Tuttavia, per alcuni scenari di immissione di testo, ad esempio l'immissione di un nome utente, il controllo ortografico offre un'esperienza negativa e pertanto deve essere disabilitato impostando la [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) proprietà su `false` :

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando la [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) proprietà è impostata su `false` e non viene usata una tastiera personalizzata, il controllo ortografico nativo verrà disabilitato. Tuttavia, se [`Keyboard`](xref:Xamarin.Forms.Keyboard) è stato impostato un oggetto che disabilita il controllo ortografico, ad esempio [`Keyboard.Chat`](xref:Xamarin.Forms.Keyboard.Chat) , la `IsSpellCheckEnabled` proprietà viene ignorata. Di conseguenza, non è possibile usare la proprietà per abilitare il controllo ortografico per un `Keyboard` che lo Disabilita in modo esplicito.

### <a name="enabling-and-disabling-text-prediction"></a>Abilitazione e disabilitazione del completamento del testo

La `IsTextPredictionEnabled` proprietà controlla se è abilitata la stima del testo e la correzione automatica del testo. Per impostazione predefinita, la proprietà è impostata su `true` . Quando l'utente immette testo, vengono presentate le stime delle parole.

Tuttavia, per alcuni scenari di immissione di testo, ad esempio l'immissione di un nome utente, la stima del testo e la correzione automatica del testo offrono un'esperienza negativa e devono essere disabilitati impostando la `IsTextPredictionEnabled` proprietà su `false` :

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> Quando la `IsTextPredictionEnabled` proprietà è impostata su `false` e non viene utilizzata una tastiera personalizzata, la stima del testo e la correzione automatica del testo sono disabilitate. Tuttavia, se [`Keyboard`](xref:Xamarin.Forms.Keyboard) è stato impostato un oggetto che disabilita la stima del testo, la `IsTextPredictionEnabled` proprietà viene ignorata. Pertanto, la proprietà non può essere utilizzata per abilitare la stima del testo per un oggetto `Keyboard` che la Disabilita in modo esplicito.

### <a name="colors"></a>Colori

`Editor`può essere impostato per utilizzare un colore di sfondo personalizzato tramite la `BackgroundColor` Proprietà. È necessario prestare particolare attenzione per garantire che i colori saranno utilizzabili in ogni piattaforma. Poiché ogni piattaforma ha impostazioni predefinite diverse per il colore del testo, potrebbe essere necessario impostare un colore di sfondo personalizzato per ogni piattaforma. Per ulteriori informazioni sull'ottimizzazione dell'interfaccia utente per ogni piattaforma, vedere [utilizzo delle modifiche alla piattaforma](~/xamarin-forms/platform/device.md) .

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

Assicurarsi che i colori di sfondo e testo scelti siano utilizzabili in ogni piattaforma e non nascondano testo segnaposto.

## <a name="interactivity"></a>Interattività

`Editor`espone due eventi:

- [TextChanged](xref:Xamarin.Forms.InputView.TextChanged) &ndash; generato quando il testo viene modificato nell'editor. Fornisce il testo prima e dopo la modifica.
- Operazione [completata](xref:Xamarin.Forms.Editor.Completed) &ndash; generato quando l'utente ha terminato l'input premendo il tasto INVIO sulla tastiera.

> [!NOTE]
> La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe, da cui [`Entry`](xref:Xamarin.Forms.Entry) eredita, include anche [`Focused`](xref:Xamarin.Forms.VisualElement.Focused) [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused) gli eventi e.

### <a name="completed"></a>Completato

L' `Completed` evento viene utilizzato per rispondere al completamento di un'interazione con un oggetto `Editor` . `Completed`viene generato quando l'utente termina l'input con un campo immettendo il tasto di ritorno sulla tastiera (o premendo il tasto TAB in UWP). Il gestore per l'evento è un gestore eventi generico che accetta il mittente e `EventArgs` :

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

È possibile sottoscrivere l'evento Completed nel codice e in XAML:

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

L' `TextChanged` evento viene utilizzato per rispondere a una modifica nel contenuto di un campo.

`TextChanged`viene generato ogni volta che viene modificato l'oggetto `Text` di `Editor` . Il gestore per l'evento accetta un'istanza di `TextChangedEventArgs` . `TextChangedEventArgs`fornisce l'accesso ai valori precedenti e nuovi di `Editor` `Text` tramite le `OldTextValue` proprietà e `NewTextValue` :

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

È possibile sottoscrivere l'evento Completed nel codice e in XAML:

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
