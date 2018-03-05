---
title: Voce
description: Testo a riga singola o la password di input
ms.topic: article
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 288cb4bd782c041e6dc91f1a40b7080e7859fad3
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="entry"></a>Voce

_Testo a riga singola o la password di input_

Xamarin. Forms `Entry` viene utilizzato per l'input di testo a riga singola. `Entry`, ad esempio la visualizzazione dell'Editor, supporta più tipi di tastiera. Inoltre, `Entry` può essere utilizzato come un campo della password.

## <a name="display-customization"></a>Personalizzazione della visualizzazione

### <a name="setting-and-reading-text"></a>Impostazione e lettura di testo

Voce, ad esempio altre viste di presentazione di testo, espone il `Text` proprietà. `Text` Consente di impostare e leggere il testo visualizzato per il `Entry`. Nell'esempio seguente viene illustrato come impostare il testo in XAML:

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

> [!NOTE]
> **Nota**: la larghezza di un `Entry` possono essere definite tramite l'impostazione relativa `WidthRequest` proprietà. Non basarsi sulla larghezza di un `Entry` viene definito in base al valore del relativo `Text` proprietà.

### <a name="keyboards"></a>Tastiere

La tastiera che viene visualizzata quando gli utenti interagiscono con un `Entry` possono essere impostate a livello di programmazione tramite le `Keyboard` proprietà.

Le opzioni per il tipo di tasti sono:

- **Predefinito** &ndash; della tastiera predefinito
- **Chat** &ndash; utilizzato per il collaudo & posizioni in cui sono utili emoji
- **Messaggio di posta elettronica** &ndash; utilizzato durante l'immissione di indirizzi di posta elettronica
- **Numerico** &ndash; utilizzato durante l'immissione di numeri
- **Telefono** &ndash; utilizzato durante l'immissione di numeri di telefono
- **URL** &ndash; utilizzato per l'immissione di percorsi di file e gli indirizzi web

È presente un [esempio ogni tastiera](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/choose-keyboard-for-entry/) nella sezione ricette.

### <a name="placeholders"></a>Segnaposto

`Entry` può essere impostato su Mostra il testo segnaposto quando non l'archiviazione input dell'utente. In pratica, viene spesso visualizzato nel form per chiarire il contenuto appropriato per un determinato campo. Colore del testo segnaposto non è possibile personalizzare e sarà lo stesso indipendentemente dal valore di `TextColor` impostazione. Se si progettano chiamate per un colore personalizzato segnaposto, è necessario eseguire il fallback un [renderer personalizzato](). Le operazioni seguenti verranno creati un `Entry` con "Username" come segnaposto in XAML:

```xaml
<Entry Placeholder="Username" />
```

In C#:

```csharp
var MyEntry = new Entry { Placeholder = "Username" };
```

![](entry-images/placeholder.png "Esempio di voce di segnaposto")

### <a name="password-fields"></a>Campi di password

`Entry` fornisce la `IsPassword` proprietà. Quando `IsPassword` è `true`, verrà visualizzato il contenuto del campo come cerchi neri:

In XAML:

```xaml
<Entry IsPassword="true" />
```

In C#:

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![](entry-images/password.png "Esempio di voce di IsPassword")

Segnaposti possono essere utilizzati con le istanze di `Entry` che sono configurati come campi di password:

In XAML:

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

In C#:

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![](entry-images/passwordplaceholder.png "Esempio di segnaposto e IsPassword voce")


### <a name="colors"></a>Colori

Voce può essere impostata per usare uno sfondo personalizzato e colori del testo tramite le proprietà associabili ai seguenti:

- **TextColor** &ndash; imposta il colore del testo.
- **BackgroundColor** &ndash; imposta il colore visualizzato dietro il testo.

Particolare attenzione è necessario assicurarsi che i colori sia utilizzabili in ciascuna piattaforma. Poiché ogni piattaforma prevede valori predefiniti diversi per i colori di sfondo e di testo, è necessario spesso entrambi se è impostata su uno.

Utilizzare il codice seguente per impostare il colore del testo di una voce:

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

Si noti che il segnaposto non interessate dall'oggetto `TextColor`.

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

Prestare attenzione per assicurarsi che i colori di sfondo e del testo desiderato sono utilizzabili in ciascuna piattaforma e non nascondano il testo segnaposto.

## <a name="events-and-interactivity"></a>Gli eventi e l'interattività

Voce espone due eventi:

- [TextChanged](http://developer.xamarin.com/api/event/Xamarin.Forms.Entry.TextChanged/) &ndash; generato quando viene modificato il testo della voce. Fornisce il testo prima e dopo la modifica.
- [Completamento](http://developer.xamarin.com/api/event/Xamarin.Forms.Entry.Completed/) &ndash; generato quando l'utente è terminata input premendo il tasto della tastiera.

### <a name="completed"></a>Operazione completata

Il `Completed` evento viene utilizzato per rispondere al completamento di un'interazione con una voce. `Completed` viene generato quando l'utente termina di input con un campo immettendo il tasto della tastiera. Il gestore per l'evento è un gestore di evento generico, accettando il mittente e `EventArgs`:

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

L'evento completato può essere sottoscritti in XAML:

```xaml
<Entry Completed="Entry_Completed" />
```

e in c#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

### <a name="textchanged"></a>TextChanged

Il `TextChanged` evento viene utilizzato per rispondere a una modifica del contenuto di un campo.

`TextChanged` viene generato ogni volta che il `Text` del `Entry` le modifiche. Il gestore per l'evento accetta un'istanza di `TextChangedEventArgs`. `TextChangedEventArgs` fornisce l'accesso ai valori vecchi e nuovi del `Entry` `Text` tramite il `OldTextValue` e `NewTextValue` proprietà:

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

Il `TextChanged` evento può essere sottoscritti in XAML:

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

e in c#:

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```


## <a name="related-links"></a>Collegamenti correlati

- [Testo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Voce API](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)
