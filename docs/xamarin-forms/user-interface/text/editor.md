---
title: Editor
description: Input di testo multilinea
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 035365a22c487039ff811756d91ca0a8d392d628
ms.sourcegitcommit: c024f29ff730ae20c15e99bfe0268a0e1c9d41e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2018
---
# <a name="editor"></a>Editor

_Input di testo multilinea_

Il `Editor` controllo viene utilizzato per accettare l'input di più righe. In questo articolo illustra:

- **[Personalizzazione](#customization)**  &ndash; tastiera e il colore.
- **[Interattività](#interactivity)**  &ndash; gli eventi che possono essere ascoltati per fornire funzionalità interattive.

## <a name="customization"></a>Personalizzazione

### <a name="setting-and-reading-text"></a>Impostazione e lettura di testo

Editor, ad esempio altre viste di presentazione di testo, espone il `Text` proprietà. `Text` Consente di impostare e leggere il testo visualizzato per il `Editor`. L'esempio seguente illustra l'impostazione del testo in XAML:

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

### <a name="keyboards"></a>Tastiere

La tastiera che viene visualizzata quando gli utenti interagiscono con un `Editor` possono essere impostate a livello di programmazione tramite le [ ``Keyboard`` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Keyboard/) proprietà.

Le opzioni per il tipo di tasti sono:

- **Predefinito** &ndash; della tastiera predefinito
- **Chat** &ndash; utilizzato per il collaudo & posizioni in cui sono utili emoji
- **Messaggio di posta elettronica** &ndash; utilizzato durante l'immissione di indirizzi di posta elettronica
- **Numerico** &ndash; utilizzato durante l'immissione di numeri
- **Telefono** &ndash; utilizzato durante l'immissione di numeri di telefono
- **URL** &ndash; utilizzata per immettere i percorsi di file e gli indirizzi web

È presente un [esempio ogni tastiera](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/) nella sezione ricette.

### <a name="colors"></a>Colori

`Editor` può essere impostato su utilizza un colore di sfondo personalizzato tramite la `BackgroundColor` proprietà. Particolare attenzione è necessario assicurarsi che i colori sia utilizzabili in ciascuna piattaforma. Poiché ogni piattaforma prevede valori predefiniti diversi per il colore del testo, si potrebbe essere necessario impostare un colore di sfondo personalizzato per ogni piattaforma. Vedere [utilizzo di modifiche per piattaforma](~/xamarin-forms/platform/device.md) per ulteriori informazioni sull'ottimizzazione dell'interfaccia utente per ogni piattaforma.

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

Assicurarsi che i colori di sfondo e del testo desiderato sono utilizzabili in ciascuna piattaforma e non nascondano il testo segnaposto.

## <a name="interactivity"></a>Interattività

`Editor` espone due eventi:

- [TextChanged](http://developer.xamarin.com/api/event/Xamarin.Forms.Editor.TextChanged/) &ndash; generato quando viene modificato il testo nell'editor. Fornisce il testo prima e dopo la modifica.
- [Completamento](http://developer.xamarin.com/api/event/Xamarin.Forms.Editor.Completed/) &ndash; generato quando l'utente è terminata input premendo il tasto della tastiera.

### <a name="completed"></a>Operazione completata

Il `Completed` evento viene utilizzato per rispondere al completamento di un'interazione con un `Editor`. `Completed` viene generato quando l'utente termina di input con un campo immettendo il tasto della tastiera. Il gestore per l'evento è un gestore di evento generico, accettando il mittente e `EventArgs`:

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

`TextChanged` viene generato ogni volta che il `Text` del `Editor` le modifiche. Il gestore per l'evento accetta un'istanza di `TextChangedEventArgs`. `TextChangedEventArgs` fornisce l'accesso ai valori vecchi e nuovi del `Editor` `Text` tramite il `OldTextValue` e `NewTextValue` proprietà:

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
- [Editor API](https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/)
