---
title: Etichetta di Novell. Forms
description: Questo articolo illustra come usare la classe Novell. Forms Label per visualizzare il testo a più righe e un singolo nelle applicazioni.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/28/2019
ms.openlocfilehash: d51ecc728ef6d22391f1cbc682aa204a3ce44b8a
ms.sourcegitcommit: 9fa7cf9fae44ed092bc9cab17c843a443001734e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72971222"
---
# <a name="xamarinforms-label"></a>Etichetta di Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Visualizzare il testo in Novell. Forms_

La visualizzazione [`Label`](xref:Xamarin.Forms.Label) viene utilizzata per la visualizzazione di testo, sia singoli che su più righe. Le etichette possono avere decorazioni di testo, testo colorato e usare tipi di carattere personalizzati (famiglie, dimensioni e opzioni).

## <a name="text-decorations"></a>Decorazioni del testo

È possibile applicare le decorazioni di testo sottolineato e barrato a istanze di [`Label`](xref:Xamarin.Forms.Label) impostando la proprietà `Label.TextDecorations` su uno o più membri dell'enumerazione `TextDecorations`:

- `None`
- `Underline`
- `Strikethrough`

Nell'esempio di codice XAML riportato di seguito viene illustrata l'impostazione della proprietà `Label.TextDecorations`:

```xaml
<Label Text="This is underlined text." TextDecorations="Underline"  />
<Label Text="This is text with strikethrough." TextDecorations="Strikethrough" />
<Label Text="This is underlined text with strikethrough." TextDecorations="Underline, Strikethrough" />
```

Il codice C# equivalente è il seguente:

```csharp
var underlineLabel = new Label { Text = "This is underlined text.", TextDecorations = TextDecorations.Underline };
var strikethroughLabel = new Label { Text = "This is text with strikethrough.", TextDecorations = TextDecorations.Strikethrough };
var bothLabel = new Label { Text = "This is underlined text with strikethrough.", TextDecorations = TextDecorations.Underline | TextDecorations.Strikethrough };
```

Gli screenshot seguenti mostrano i membri dell'enumerazione `TextDecorations` applicati alle istanze di [`Label`](xref:Xamarin.Forms.Label) :

![Etichette con decorazioni di testo](label-images/label-textdecorations.png)

> [!NOTE]
> È inoltre possibile applicare le decorazioni di testo alle istanze di [`Span`](xref:Xamarin.Forms.Span) . Per ulteriori informazioni sulla classe `Span`, vedere [testo formattato](#Formatted_Text).

## <a name="character-spacing"></a>Spaziatura caratteri

È possibile applicare la spaziatura dei caratteri alle istanze di [`Label`](xref:Xamarin.Forms.Label) impostando la proprietà `Label.CharacterSpacing` su un valore `double`:

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

Il codice C# equivalente è il seguente:

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

Il risultato è che i caratteri nel testo visualizzato dal [`Label`](xref:Xamarin.Forms.Label) sono spaziati `CharacterSpacing` unità indipendenti dal dispositivo.

## <a name="colors"></a>Colori

Le etichette possono essere impostate in modo da usare un colore del testo personalizzato tramite la proprietà associabile [`TextColor`](xref:Xamarin.Forms.Label.TextColor) .

È necessario prestare particolare attenzione per garantire che i colori saranno utilizzabili in ogni piattaforma. Poiché ogni piattaforma ha impostazioni predefinite diverse per il testo e i colori di sfondo, è necessario prestare attenzione a scegliere un valore predefinito che funzioni su ognuno di essi.

Nell'esempio di codice XAML seguente viene impostato il colore del testo di un `Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo">
    <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a green label." />
    </StackLayout>
</ContentPage>
```

Il codice C# equivalente è il seguente:

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();

        var layout = new StackLayout { Padding = new Thickness(5,10) };
        var label = new Label { Text="This is a green label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
        this.Content = layout;
    }
}
```

Gli screenshot seguenti mostrano il risultato dell'impostazione della proprietà `TextColor`:

![Esempio di etichetta TextColor](label-images/textcolor.png)

Per ulteriori informazioni sui colori, vedere [colori](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Tipi di carattere

Per ulteriori informazioni sulla specifica dei tipi di carattere in una `Label`, vedere [tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Troncamento e wrapping

Le etichette possono essere impostate per gestire il testo che non può essere inserito in una riga in uno dei diversi modi esposti dalla proprietà `LineBreakMode`. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) è un'enumerazione con i valori seguenti:

- **HeadTruncation** &ndash; tronca l'intestazione del testo, mostrando la fine.
- **CharacterWrap** &ndash; esegue il wrapping del testo su una nuova riga in corrispondenza di un limite di caratteri.
- **MiddleTruncation** &ndash; Visualizza l'inizio e la fine del testo, con la sostituzione intermedia con i puntini di sospensione.
- **Nowrap** &ndash; non esegue il wrapping del testo, visualizzando solo la quantità di testo che può rientrare in una sola riga.
- **TailTruncation** &ndash; Mostra l'inizio del testo, troncando la fine.
- **WordWrap** &ndash; esegue il wrapping del testo al confine della parola.

## <a name="display-a-specific-number-of-lines"></a>Visualizza un numero specifico di righe

Il numero di righe visualizzate da un [`Label`](xref:Xamarin.Forms.Label) può essere specificato impostando la proprietà `Label.MaxLines` su un valore `int`:

- Quando `MaxLines` è 0, il `Label` rispetta il valore della proprietà [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) per visualizzare solo una riga, possibilmente troncata, o tutte le righe con tutto il testo.
- Quando `MaxLines` è 1, il risultato è identico all'impostazione della proprietà [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) su [`NoWrap`](xref:Xamarin.Forms.LineBreakMode), [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode), [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode)o [0](xref:Xamarin.Forms.LineBreakMode). Tuttavia, il `Label` rispetterà il valore della proprietà [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) per quanto riguarda la posizione dei puntini di sospensione, se applicabile.
- Quando `MaxLines` è maggiore di 1, il `Label` Visualizza fino al numero di righe specificato, rispettando al contempo il valore della proprietà [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) per quanto riguarda la posizione dei puntini di sospensione, se applicabile. Tuttavia, l'impostazione della proprietà `MaxLines` su un valore maggiore di 1 non produce alcun effetto se la proprietà [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) è impostata su [`NoWrap`](xref:Xamarin.Forms.LineBreakMode).

Nell'esempio di codice XAML riportato di seguito viene illustrata l'impostazione della proprietà `MaxLines` in un [`Label`](xref:Xamarin.Forms.Label):

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       MaxLines="2" />
```

Il codice C# equivalente è il seguente:

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  MaxLines = 2
};
```

Gli screenshot seguenti mostrano il risultato dell'impostazione della proprietà `MaxLines` su 2, quando il testo è sufficientemente lungo da occupare più di 2 righe:

![Esempio di etichetta MaxLines](label-images/label-maxlines.png)


## <a name="display-html"></a>Visualizza HTML

La classe [`Label`](xref:Xamarin.Forms.Label) dispone di una proprietà `TextType`, che determina se l'istanza di `Label` deve visualizzare testo normale o testo HTML. Questa proprietà deve essere impostata su uno dei membri dell'enumerazione `TextType`:

- `Text` indica che nella `Label` verrà visualizzato testo normale e il valore predefinito della proprietà `Label.TextType`.
- `Html` indica che il `Label` visualizzerà il testo HTML.

Le istanze [`Label`](xref:Xamarin.Forms.Label) possono pertanto visualizzare il codice HTML impostando la proprietà `Label.TextType` su `Html` e la proprietà `Label.Text` su una stringa HTML:

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

Nell'esempio precedente, le virgolette doppie nel codice HTML devono essere precedute da un carattere di escape usando il simbolo `\`.

In XAML, le stringhe HTML possono diventare illeggibili a causa della fuoriuscita del `<` e della `>` dei simboli:

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

In alternativa, per una maggiore leggibilità, il codice HTML può essere inline in una `CDATA` sezione:

```xaml
<Label TextType="Html">
    <x:String>
        <![CDATA[
        This is <strong style="color:red">HTML</strong> text.
        ]]>
    </x:String>
</Label>
```

In questo esempio, la proprietà `Label.Text` è impostata sulla stringa HTML inline nella sezione `CDATA`. Questa operazione funziona perché la proprietà `Text` è la `ContentProperty` per la classe `Label`.

Gli screenshot seguenti mostrano un [`Label`](xref:Xamarin.Forms.Label) la visualizzazione del codice HTML:

![Screenshot di un'etichetta che Visualizza HTML, in iOS e Android](label-images/label-html.png)

> [!IMPORTANT]
> La visualizzazione del codice HTML in una [`Label`](xref:Xamarin.Forms.Label) è limitata ai tag HTML supportati dalla piattaforma sottostante.

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Testo formattato

Le etichette espongono una proprietà di [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) che consente la presentazione del testo con più tipi di carattere e colori nella stessa visualizzazione.

La proprietà `FormattedText` è di tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString), che include una o più istanze di [`Span`](xref:Xamarin.Forms.Span) , impostate tramite la proprietà [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) . Per impostare l'aspetto visivo, è possibile usare le proprietà `Span` seguenti:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) : colore dello sfondo dell'intervallo.
- `CharacterSpacing`, di tipo `double`, è la spaziatura tra i caratteri del testo `Span`.
- [`Font`](xref:Xamarin.Forms.Span.Font) : tipo di carattere per il testo nell'intervallo.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) : gli attributi del tipo di carattere per il testo nell'intervallo.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) : la famiglia di caratteri a cui appartiene il tipo di carattere del testo nell'intervallo.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) : dimensione del tipo di carattere per il testo nell'intervallo.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) : colore per il testo nell'intervallo. Questa proprietà è obsoleta ed è stata sostituita dalla proprietà `TextColor`.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) : il moltiplicatore da applicare all'altezza della riga predefinita dell'intervallo. Per ulteriori informazioni, vedere [altezza riga](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style) : lo stile da applicare all'intervallo.
- [`Text`](xref:Xamarin.Forms.Span.Text) : il testo dell'intervallo.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) : colore per il testo nell'intervallo.
- `TextDecorations`: decorazioni da applicare al testo nell'intervallo. Per altre informazioni, vedere [decorazioni di testo](#text-decorations).

Le proprietà associabili [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor), [`Text`](xref:Xamarin.Forms.Span.Text)e [`Text`](xref:Xamarin.Forms.Span.Text) hanno una modalità di associazione predefinita [`OneWay`](xref:Xamarin.Forms.BindingMode). Per ulteriori informazioni su questa modalità di associazione, vedere [la modalità di associazione predefinita](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) nella Guida alla [modalità di associazione](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md) .

Inoltre, la proprietà [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) può essere utilizzata per definire una raccolta di riconoscitori di movimento che risponderanno ai movimenti nel [`Span`](xref:Xamarin.Forms.Span).

> [!NOTE]
> Non è possibile visualizzare il codice HTML in un [`Span`](xref:Xamarin.Forms.Span).

Nell'esempio di codice XAML riportato di seguito viene illustrata una proprietà `FormattedText` costituita da tre istanze [`Span`](xref:Xamarin.Forms.Span) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo - XAML">
    <StackLayout Padding="5,10">
        ...
        <Label LineBreakMode="WordWrap">
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Red Bold, " TextColor="Red" FontAttributes="Bold" />
                    <Span Text="default, " Style="{DynamicResource BodyStyle}">
                        <Span.GestureRecognizers>
                            <TapGestureRecognizer Command="{Binding TapCommand}" />
                        </Span.GestureRecognizers>
                    </Span>
                    <Span Text="italic small." FontAttributes="Italic" FontSize="Small" />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

Il codice C# equivalente è il seguente:

```csharp
public class LabelPageCode : ContentPage
{
    public LabelPageCode ()
    {
        var layout = new StackLayout{ Padding = new Thickness (5, 10) };
        ...
        var formattedString = new FormattedString ();
        formattedString.Spans.Add (new Span{ Text = "Red bold, ", ForegroundColor = Color.Red, FontAttributes = FontAttributes.Bold });

        var span = new Span { Text = "default, " };
        span.GestureRecognizers.Add(new TapGestureRecognizer { Command = new Command(async () => await DisplayAlert("Tapped", "This is a tapped Span.", "OK")) });
        formattedString.Spans.Add(span);
        formattedString.Spans.Add (new Span { Text = "italic small.", FontAttributes = FontAttributes.Italic, FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)) });

        layout.Children.Add (new Label { FormattedText = formattedString });
        this.Content = layout;
    }
}
```

> [!IMPORTANT]
> È possibile impostare la proprietà [`Text`](xref:Xamarin.Forms.Span.Text) di un `Span` tramite Data Binding. Per altre informazioni, vedere [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Si noti che un [`Span`](xref:Xamarin.Forms.Span) può anche rispondere a qualsiasi movimento aggiunto alla raccolta di [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) dell'intervallo. Ad esempio, un [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) è stato aggiunto alla seconda `Span` negli esempi di codice precedenti. Pertanto, quando questo `Span` viene toccato, il `TapGestureRecognizer` risponderà eseguendo la `ICommand` definita dalla proprietà [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) . Per altre informazioni sui riconoscitori di movimento, vedere [movimenti Novell. Forms](~/xamarin-forms/app-fundamentals/gestures/index.md).

Gli screenshot seguenti mostrano il risultato dell'impostazione della proprietà `FormattedString` su tre istanze `Span`:

![Esempio di FormattedText etichetta](label-images/formattedtext.png)

## <a name="line-height"></a>Altezza riga

L'altezza verticale di un [`Label`](xref:Xamarin.Forms.Label) e una [`Span`](xref:Xamarin.Forms.Span) può essere personalizzata impostando la proprietà [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) o [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) su un valore `double`. In iOS e Android questi valori sono moltiplicatori dell'altezza della riga originale e nel piattaforma UWP (Universal Windows Platform) (UWP) il valore della proprietà `Label.LineHeight` è un moltiplicatore delle dimensioni del carattere dell'etichetta.

> [!NOTE]
>
> - In iOS le proprietà [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) e [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) modificano l'altezza della riga del testo che si adatta a una singola riga e il testo che esegue il wrapping su più righe.
> - In Android le proprietà [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) e [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) cambiano solo l'altezza della riga del testo che esegue il wrapping su più righe.
> - In UWP la proprietà [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) modifica l'altezza della riga del testo che esegue il wrapping su più righe e la proprietà [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) non ha alcun effetto.

Nell'esempio di codice XAML riportato di seguito viene illustrata l'impostazione della proprietà [`LineHeight`](xref:Xamarin.Forms.Label.LineHeight) in un [`Label`](xref:Xamarin.Forms.Label):

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       LineHeight="1.8" />
```

Il codice C# equivalente è il seguente:

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  LineHeight = 1.8
};
```

Gli screenshot seguenti mostrano il risultato dell'impostazione della proprietà [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) su 1,8:

![Esempio di etichetta LineHeight](label-images/label-lineheight.png)

Nell'esempio di codice XAML riportato di seguito viene illustrata l'impostazione della proprietà [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) in un [`Span`](xref:Xamarin.Forms.Span):

```xaml
<Label LineBreakMode="WordWrap">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In a tincidunt sem. Phasellus mollis sit amet turpis in rutrum. Sed aliquam ac urna id scelerisque. "
                  LineHeight="1.8"/>
            <Span Text="Nullam feugiat sodales elit, et maximus nibh vulputate id."
                  LineHeight="1.8" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Il codice C# equivalente è il seguente:

```csharp
var formattedString = new FormattedString();
formattedString.Spans.Add(new Span
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In a tincidunt sem. Phasellus mollis sit amet turpis in rutrum. Sed aliquam ac urna id scelerisque. ",
  LineHeight = 1.8
});
formattedString.Spans.Add(new Span
{
  Text = "Nullam feugiat sodales elit, et maximus nibh vulputate id.",
  LineHeight = 1.8
});
var label = new Label
{
  FormattedText = formattedString,
  LineBreakMode = LineBreakMode.WordWrap
};
```

Gli screenshot seguenti mostrano il risultato dell'impostazione della proprietà [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) su 1,8:

![Esempio di span LineHeight](label-images/span-lineheight.png)

## <a name="padding"></a>Spaziatura interna

La spaziatura interna rappresenta lo spazio tra un elemento e i relativi elementi figlio e viene utilizzato per separare l'elemento dal relativo contenuto. La spaziatura interna può essere applicata alle istanze di [`Label`](xref:Xamarin.Forms.Label) impostando la proprietà `Label.Padding` su un valore [`Thickness`](xref:Xamarin.Forms.Thickness) :

```xaml
<Label Padding="10">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Lorem ipsum" />
            <Span Text="dolor sit amet." />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Il codice C# equivalente è il seguente:

```csharp
FormattedString formattedString = new FormattedString();
formattedString.Spans.Add(new Span
{
  Text = "Lorem ipsum"
});
formattedString.Spans.Add(new Span
{
  Text = "dolor sit amet."
});
Label label = new Label
{
    FormattedText = formattedString,
    Padding = new Thickness(20)
};
```

> [!IMPORTANT]
> In iOS, quando viene creata una [`Label`](xref:Xamarin.Forms.Label) che imposta la proprietà `Padding`, viene applicata la spaziatura interna e il valore di riempimento può essere aggiornato in un secondo momento. Tuttavia, quando viene creata una `Label` che non imposta la proprietà `Padding`, il tentativo di impostarlo in un secondo momento non avrà alcun effetto.
>
> In Android e in piattaforma UWP (Universal Windows Platform), è possibile specificare il valore della proprietà `Padding` quando viene creato il `Label` o versione successiva.

Per ulteriori informazioni sulla spaziatura interna, vedere [margini e spaziatura interna](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).


## <a name="hyperlinks"></a>Collegamenti ipertestuali

Il testo visualizzato dalle istanze [`Label`](xref:Xamarin.Forms.Label) e [`Span`](xref:Xamarin.Forms.Span) può essere trasformato in collegamenti ipertestuali con l'approccio seguente:

1. Impostare le proprietà `TextColor` e `TextDecoration` del [`Label`](xref:Xamarin.Forms.Label) o [`Span`](xref:Xamarin.Forms.Span).
1. Aggiungere un [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) alla raccolta [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) della [`Label`](xref:Xamarin.Forms.Label) o [`Span`](xref:Xamarin.Forms.Span), la cui proprietà [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) è associata a un 0 e la cui proprietà [2](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) contiene l'URL da aprire.
1. Definire la `ICommand` che verrà eseguita dall' [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer).
1. Scrivere il codice che verrà eseguito dal `ICommand`.

L'esempio di codice seguente, tratto dall'esempio [demos di collegamento ipertestuale](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , mostra un [`Label`](xref:Xamarin.Forms.Label) il cui contenuto è impostato da più istanze [`Span`](xref:Xamarin.Forms.Span) :

```xaml
<Label>
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Alternatively, click " />
            <Span Text="here"
                  TextColor="Blue"
                  TextDecorations="Underline">
                <Span.GestureRecognizers>
                    <TapGestureRecognizer Command="{Binding TapCommand}"
                                          CommandParameter="https://docs.microsoft.com/xamarin/" />
                </Span.GestureRecognizers>
            </Span>
            <Span Text=" to view Xamarin documentation." />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

In questo esempio, la prima e la terza istanza [`Span`](xref:Xamarin.Forms.Span) includono testo, mentre la seconda `Span` rappresenta un collegamento ipertestuale toccabile. Il colore del testo è impostato su blu e presenta un effetto di testo sottolineato. Viene così creato l'aspetto di un collegamento ipertestuale, come illustrato nelle schermate seguenti:

[![Collegamenti ipertestuali](label-images/hyperlinks-small.png "Collegamenti ipertestuali")](label-images/hyperlinks-large.png#lightbox)

Quando viene toccato il collegamento ipertestuale, il [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) risponderà eseguendo l'`ICommand` definito dalla relativa proprietà [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) . Inoltre, l'URL specificato dalla proprietà [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) verrà passato al `ICommand` come parametro.

Il code-behind per la pagina XAML contiene l'implementazione di `TapCommand`:

```csharp
public partial class MainPage : ContentPage
{
    // Launcher.OpenAsync is provided by Xamarin.Essentials.
    public ICommand TapCommand => new Command<string>(async (url) => await Launcher.OpenAsync(url));

    public MainPage()
    {
        InitializeComponent();
        BindingContext = this;
    }
}
```

Il `TapCommand` esegue il metodo `Launcher.OpenAsync`, passando il valore della proprietà [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) come parametro. Il metodo `Launcher.OpenAsync` viene fornito da Novell. Essentials e apre l'URL in un Web browser. Pertanto, l'effetto complessivo è che, quando il collegamento ipertestuale viene toccato sulla pagina, viene visualizzato un Web browser e l'URL associato al collegamento ipertestuale viene indirizzato a.

### <a name="creating-a-reusable-hyperlink-class"></a>Creazione di una classe di collegamento ipertestuale riutilizzabile

L'approccio precedente alla creazione di un collegamento ipertestuale richiede la scrittura di codice ripetitivo ogni volta che è necessario un collegamento ipertestuale nell'applicazione. Entrambe le classi [`Label`](xref:Xamarin.Forms.Label) e [`Span`](xref:Xamarin.Forms.Span) , tuttavia, possono essere sottoclassate per creare classi di `HyperlinkLabel` e `HyperlinkSpan`, con il riconoscimento del movimento e il codice di formattazione del testo aggiunto.

L'esempio di codice seguente, tratto dall'esempio [demos di collegamento ipertestuale](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , Mostra una classe `HyperlinkSpan`:

```csharp
public class HyperlinkSpan : Span
{
    public static readonly BindableProperty UrlProperty =
        BindableProperty.Create(nameof(Url), typeof(string), typeof(HyperlinkSpan), null);

    public string Url
    {
        get { return (string)GetValue(UrlProperty); }
        set { SetValue(UrlProperty, value); }
    }

    public HyperlinkSpan()
    {
        TextDecorations = TextDecorations.Underline;
        TextColor = Color.Blue;
        GestureRecognizers.Add(new TapGestureRecognizer
        {
            // Launcher.OpenAsync is provided by Xamarin.Essentials.
            Command = new Command(async () => await Launcher.OpenAsync(Url))
        });
    }
}
```

La classe `HyperlinkSpan` definisce una proprietà `Url` e [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)associata e il costruttore imposta l'aspetto del collegamento ipertestuale e il [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) che risponderà quando viene toccato il collegamento ipertestuale. Quando viene toccato un `HyperlinkSpan`, il `TapGestureRecognizer` risponderà eseguendo il metodo `Launcher.OpenAsync` per aprire l'URL, specificato dalla proprietà `Url`, in un Web browser.

La classe `HyperlinkSpan` può essere utilizzata aggiungendo un'istanza della classe a XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:HyperlinkDemo"
             x:Class="HyperlinkDemo.MainPage">
    <StackLayout>
        ...
        <Label>
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Alternatively, click " />
                    <local:HyperlinkSpan Text="here"
                                         Url="https://docs.microsoft.com/appcenter/" />
                    <Span Text=" to view AppCenter documentation." />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

## <a name="styling-labels"></a>Etichette di stile

Nelle sezioni precedenti è stata illustrata l'impostazione delle proprietà [`Label`](xref:Xamarin.Forms.Label) e [`Span`](xref:Xamarin.Forms.Span) per ogni singola istanza. Tuttavia, i set di proprietà possono essere raggruppati in uno stile che viene applicato in modo coerente a una o più visualizzazioni. Questo può migliorare la leggibilità del codice e rendere più semplice l'implementazione delle modifiche di progettazione. Per ulteriori informazioni, vedere [stili](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Collegamenti correlati

- [Testo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Collegamenti ipertestuali (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Creazione di app per dispositivi mobili con Novell. Forms, capitolo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API Etichetta](xref:Xamarin.Forms.Label)
- [API span](xref:Xamarin.Forms.Span)
