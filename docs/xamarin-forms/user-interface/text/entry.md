---
title: Voce di xamarin. Forms
description: Questo articolo illustra come usare la classe di ingresso di xamarin. Forms per accettare input di password in un'applicazione o di testo a riga singola.
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 95afdfde878759d4a598e200d16fe6fb1fa2005e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998238"
---
# <a name="xamarinforms-entry"></a>Voce di xamarin. Forms

_Testo a riga singola o la password di input_

Xamarin. Forms `Entry` viene usato per l'input di testo a riga singola. Il `Entry`, ad esempio il `Editor` visualizzare, supporta più tipi di tastiera. Inoltre, il `Entry` può essere utilizzato come un campo della password.

## <a name="display-customization"></a>Personalizzazione della visualizzazione

### <a name="setting-and-reading-text"></a>Impostazione e la lettura di testo

Il `Entry`, come altre visualizzazioni di testo-presentazione, espone la `Text` proprietà. Questa proprietà può essere utilizzata per impostare e leggere il testo visualizzato dal `Entry`. Nell'esempio seguente viene illustrata l'impostazione di `Text` proprietà in XAML:

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
> La larghezza di un `Entry` può essere definita impostando relativo `WidthRequest` proprietà. Non basarsi sulla larghezza di un `Entry` definito in base al valore del relativo `Text` proprietà.

### <a name="limiting-input-length"></a>Limitazione della lunghezza di Input

Il [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) proprietà può essere utilizzata per limitare la lunghezza di input valido per il [ `Entry` ](xref:Xamarin.Forms.Entry). Questa proprietà deve essere impostata su un numero intero positivo:

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

Oggetto [ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength) ha valore 0 indica che non sarà consentito alcun input e il valore `int.MaxValue`, che è il valore predefinito per un [ `Entry` ](xref:Xamarin.Forms.Entry), indica che è presente alcun limite effettivo per il numero di caratteri che possono essere immessi.

### <a name="keyboards"></a>Tastiere

La tastiera che viene visualizzata quando gli utenti interagiscono con un' `Entry` possono essere impostate a livello di programmazione tramite le `Keyboard` proprietà.

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
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> Quando la [ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) è impostata su `false`e non viene usata una tastiera personalizzata, il correttore ortografico nativo verrà disabilitato. Tuttavia, se un [ `Keyboard` ](xref:Xamarin.Forms.Keyboard) ha stato gruppo che disabilita il controllo ortografico controllo, ad esempio [ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat), il `IsSpellCheckEnabled` proprietà viene ignorata. Pertanto, la proprietà non è utilizzabile per abilitare il controllo ortografico per un `Keyboard` che disabilita in modo esplicito.

### <a name="placeholders"></a>Segnaposto

`Entry` può essere impostato per mostrare il testo segnaposto quando l'input dell'utente non vengono archiviati. In pratica, ciò viene spesso visualizzato nel form per chiarire il contenuto appropriato per un determinato campo. Colore del testo segnaposto non è possibile personalizzare e saranno gli stessi indipendentemente il `TextColor` impostazione. Se si progettano chiamate per un colore personalizzato segnaposto, è necessario eseguire il fallback a un [renderer personalizzati](). Il seguente creerà un `Entry` con "Username" come segnaposto in XAML:

```xaml
<Entry Placeholder="Username" />
```

In C#:

```csharp
var MyEntry = new Entry { Placeholder = "Username" };
```

![](entry-images/placeholder.png "Esempio di voce di segnaposto")

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

- [TextChanged](xref:Xamarin.Forms.Entry.TextChanged) &ndash; generato quando viene modificato il testo della voce. Fornisce il testo prima e dopo la modifica.
- [Completare](xref:Xamarin.Forms.Entry.Completed) &ndash; generato quando l'utente ha terminato di input premendo il tasto INVIO sulla tastiera.

### <a name="completed"></a>Operazione completata

Il `Completed` evento viene utilizzato per rispondere al completamento di un'interazione con una voce. `Completed` viene generato quando l'utente termina con un campo di input, immettere il tasto INVIO sulla tastiera. Il gestore dell'evento è un gestore eventi generici, accettando il mittente e `EventArgs`:

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
