---
title: Editor di xamarin. Forms
description: Questo articolo illustra come usare il controllo dell'Editor di xamarin. Forms per accettare l'input di testo su più righe in un'applicazione.
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 4879ff88d5bbdab5aa92024bee7f50239a141e3b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995866"
---
# <a name="xamarinforms-editor"></a>Editor di xamarin. Forms

_Input di testo multilinea_

Il `Editor` controllo viene utilizzato per accettare l'input multiriga. Questo articolo illustrerà quanto segue:

- **[Personalizzazione](#customization)**  &ndash; opzioni della tastiera e il colore.
- **[Interattività](#interactivity)**  &ndash; gli eventi che possono restare in ascolto per fornire l'interattività.

## <a name="customization"></a>Personalizzazione

### <a name="setting-and-reading-text"></a>Impostazione e la lettura di testo

Il `Editor`, come altre visualizzazioni di testo-presentazione, espone la `Text` proprietà. Questa proprietà può essere utilizzata per impostare e leggere il testo visualizzato dal `Editor`. Nell'esempio seguente viene illustrata l'impostazione di `Text` proprietà in XAML:

```xaml
<Editor Text="I am an Editor" />
```

In C#:

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

Per leggere il testo, accedere il `Text` proprietà in c#:

```csharp
var text = MyEditor.Text;
```

### <a name="limiting-input-length"></a>Limitazione della lunghezza di Input

Il [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) proprietà può essere utilizzata per limitare la lunghezza di input valido per il [ `Editor` ](xref:Xamarin.Forms.Editor). Questa proprietà deve essere impostata su un numero intero positivo:

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

Oggetto [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) ha valore 0 indica che non sarà consentito alcun input e il valore `int.MaxValue`, che è il valore predefinito per un [ `Editor` ](xref:Xamarin.Forms.Editor), indica che è presente alcun limite effettivo per il numero di caratteri che possono essere immessi.

### <a name="keyboards"></a>Tastiere

La tastiera che viene visualizzata quando gli utenti interagiscono con un' `Editor` possono essere impostati a livello di codice usando il [ ``Keyboard`` ](xref:Xamarin.Forms.Keyboard) proprietà.

Le opzioni per il tipo di tastiera sono:

- **Impostazione predefinita** &ndash; della tastiera predefinita
- **Avvia una chat** &ndash; usato per inviato e posizioni in cui sono utili emoji
- **Messaggio di posta elettronica** &ndash; usato quando si immette gli indirizzi di posta elettronica
- **Numerico** &ndash; utilizzato durante l'immissione di numeri
- **Telefono** &ndash; usato quando si immettono i numeri di telefono
- **URL** &ndash; usato per l'immissione di percorsi di file e gli indirizzi web

È presente un' [esempio di ogni tastiera](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/) nella sezione Domande recipe.

### <a name="enabling-and-disabling-spell-checking"></a>Abilitazione e disabilitazione di controllo ortografico

Il [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) proprietà controlla se il controllo ortografico è abilitato. Per impostazione predefinita, la proprietà è impostata su `true`. Quando l'utente ne immette di testo, sono indicati gli errori di ortografia.

Tuttavia, per alcuni scenari di voce di testo, ad esempio immettendo un nome utente, controllo ortografico fornisce un'esperienza negativa e pertanto devono essere disabilitate impostando il [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) proprietà `false`:

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando la [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) è impostata su `false`e non viene usata una tastiera personalizzata, il correttore ortografico nativo verrà disabilitato. Tuttavia, se un [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) ha stato gruppo che disabilita il controllo ortografico controllo, ad esempio [ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat), il `IsSpellCheckEnabled` proprietà viene ignorata. Pertanto, la proprietà non è utilizzabile per abilitare il controllo ortografico per un `Keyboard` che disabilita in modo esplicito.

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

### <a name="completed"></a>Operazione completata

Il `Completed` evento viene utilizzato per rispondere al completamento di un'interazione con un `Editor`. `Completed` viene generato quando l'utente termina con un campo di input, immettere il tasto INVIO sulla tastiera. Il gestore dell'evento è un gestore eventi generici, accettando il mittente e `EventArgs`:

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

- [Testo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Editor di API](xref:Xamarin.Forms.Editor)
