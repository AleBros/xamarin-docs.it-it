---
title: Etichetta di Novell. Forms
description: Questo articolo illustra come usare la classe Novell. Forms Label per visualizzare il testo a più righe e un singolo nelle applicazioni.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: 6ce4e39986afb7444e7d126e9789760d9311ca45
ms.sourcegitcommit: 737c7fbbe8aed1f33110a5217d7e6d6ef3e5b785
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793222"
---
# <a name="xamarinforms-label"></a>Etichetta di Novell. Forms

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Visualizzare il testo in Novell. Forms_

La [`Label`](xref:Xamarin.Forms.Label) vista viene utilizzata per la visualizzazione di testo, sia singoli che su più righe. Le etichette possono avere decorazioni di testo, testo colorato e usare tipi di carattere personalizzati (famiglie, dimensioni e opzioni).

## <a name="text-decorations"></a>Decorazioni del testo

È possibile applicare le decorazioni di testo sottolineato e [`Label`](xref:Xamarin.Forms.Label) barrato `Label.TextDecorations` alle istanze impostando la `TextDecorations` proprietà su uno o più membri dell'enumerazione:

- `None`
- `Underline`
- `Strikethrough`

Nell'esempio di codice XAML riportato di `Label.TextDecorations` seguito viene illustrata l'impostazione della proprietà:

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

Gli screenshot seguenti mostrano i membri `TextDecorations` dell'enumerazione applicati alle [`Label`](xref:Xamarin.Forms.Label) istanze:

![Etichette con decorazioni di testo](label-images/label-textdecorations.png)

> [!NOTE]
> Le decorazioni di testo possono essere applicate [`Span`](xref:Xamarin.Forms.Span) anche alle istanze di. Per ulteriori informazioni sulla `Span` classe, vedere [testo formattato](#Formatted_Text).

## <a name="character-spacing"></a>Spaziatura caratteri

È possibile applicare la spaziatura [`Label`](xref:Xamarin.Forms.Label) dei caratteri alle istanze `Label.CharacterSpacing` impostando la `double` proprietà su un valore:

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

Il codice C# equivalente è il seguente:

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

Il risultato è che i caratteri nel testo visualizzato da [`Label`](xref:Xamarin.Forms.Label) sono unità separate da `CharacterSpacing` dispositivi indipendenti dal dispositivo.

## <a name="new-lines"></a>Nuove righe

Esistono due tecniche principali per forzare il testo in un [`Label`](xref:Xamarin.Forms.Label) oggetto su una nuova riga, da XAML:

1. Usare il carattere di avanzamento riga Unicode, che è&amp;"#10;".
1. Specificare il testo usando la sintassi dell' *elemento Property* .

Il codice seguente illustra un esempio di entrambe le tecniche:

```xaml
<!-- Unicode line feed character -->
<Label Text="First line &#10; Second line" />

<!-- Property element syntax -->
<Label>
    <Label.Text>
        First line
        Second line
    </Label.Text>
</Label>
```

In C# è possibile forzare il testo su una nuova riga con il carattere "\n":

```csharp
Label label = new Label { Text = "First line\nSecond line" };
```

## <a name="colors"></a>Colori

Le etichette possono essere impostate in modo da usare un colore del testo [`TextColor`](xref:Xamarin.Forms.Label.TextColor) personalizzato tramite la proprietà associabile.

È necessario prestare particolare attenzione per garantire che i colori saranno utilizzabili in ogni piattaforma. Poiché ogni piattaforma ha impostazioni predefinite diverse per il testo e i colori di sfondo, è necessario prestare attenzione a scegliere un valore predefinito che funzioni su ognuno di essi.

Nell'esempio di codice XAML seguente viene impostato il colore `Label`del testo di un oggetto:

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

Gli screenshot seguenti mostrano il risultato dell'impostazione della `TextColor` proprietà:

![Esempio di etichetta TextColor](label-images/textcolor.png)

Per ulteriori informazioni sui colori, vedere [colori](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Tipi di carattere

Per ulteriori informazioni sulla specifica dei tipi di `Label`carattere in un, vedere [tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Troncamento e wrapping

Le `LineBreakMode` etichette possono essere impostate per gestire il testo che non può essere inserito in una sola riga in uno dei diversi modi esposti dalla proprietà. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)è un'enumerazione con i valori seguenti:

- **HeadTruncation** &ndash; tronca l'intestazione del testo, mostrando la fine.
- **CharacterWrap** &ndash; esegue il wrapping del testo su una nuova riga in corrispondenza di un limite di caratteri.
- **MiddleTruncation** &ndash; Visualizza l'inizio e la fine del testo, con la sostituzione intermedia con i puntini di sospensione.
- **Nowrap** &ndash; non esegue il wrapping del testo, visualizzando solo il testo che può essere inserito in una sola riga.
- **TailTruncation** &ndash; Mostra l'inizio del testo, troncando la fine.
- **WordWrap** &ndash; esegue il wrapping del testo al confine di parola.

## <a name="display-a-specific-number-of-lines"></a>Visualizza un numero specifico di righe

Il numero di righe visualizzate da un [`Label`](xref:Xamarin.Forms.Label) oggetto può essere specificato impostando `Label.MaxLines` la proprietà su `int` un valore:

- Quando `MaxLines` è-1, ovvero il valore predefinito, la `Label` [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) proprietà rispetta il valore della proprietà per visualizzare solo una riga, possibilmente troncata, o tutte le righe con tutto il testo.
- Quando `MaxLines` è 0, `Label` non viene visualizzato.
- Quando `MaxLines` è 1, il risultato è identico all'impostazione [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) della proprietà [`NoWrap`](xref:Xamarin.Forms.LineBreakMode)su [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode), [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode), o [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode). Tuttavia, l' `Label` oggetto rispetterà il valore [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) della proprietà per quanto riguarda la posizione dei puntini di sospensione, se applicabile.
- Quando `MaxLines` è maggiore di 1, `Label` viene visualizzato fino al numero di righe specificato, rispettando al contempo il valore della [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) proprietà per quanto riguarda la posizione dei puntini di sospensione, se applicabile. Tuttavia, l'impostazione `MaxLines` della proprietà su un valore maggiore di 1 non ha alcun effetto [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) se la proprietà è [`NoWrap`](xref:Xamarin.Forms.LineBreakMode)impostata su.

Nell'esempio di codice XAML seguente viene `MaxLines` illustrata l' [`Label`](xref:Xamarin.Forms.Label)impostazione della proprietà su un oggetto:

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

Gli screenshot seguenti mostrano il risultato dell'impostazione della `MaxLines` proprietà su 2, quando il testo è sufficientemente lungo da occupare più di 2 righe:

![Esempio di etichetta MaxLines](label-images/label-maxlines.png)

## <a name="display-html"></a>Visualizzare HTML

La [`Label`](xref:Xamarin.Forms.Label) classe dispone di `TextType` una proprietà che determina se l' `Label` istanza deve visualizzare testo normale o testo HTML. Questa proprietà deve essere impostata su uno dei membri dell' `TextType` enumerazione:

- `Text`indica che in `Label` verrà visualizzato testo normale e il valore predefinito della `Label.TextType` proprietà.
- `Html`indica che visualizzerà il `Label` testo HTML.

Le [`Label`](xref:Xamarin.Forms.Label) istanze possono pertanto visualizzare il codice `Label.TextType` HTML impostando la `Html`proprietà su e `Label.Text` la proprietà su una stringa HTML:

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

Nell'esempio precedente, le virgolette doppie nel codice HTML devono essere precedute da un carattere `\` di escape usando il simbolo.

In XAML, le stringhe HTML possono diventare illeggibili a causa dell'escape dei `<` simboli e `>` :

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

In alternativa, per una maggiore leggibilità, il codice HTML può essere inline in `CDATA` una sezione:

```xaml
<Label TextType="Html">
    <![CDATA[
    This is <strong style="color:red">HTML</strong> text.
    ]]>
</Label>
```

In questo esempio, la `Label.Text` proprietà viene impostata sulla stringa HTML inline nella `CDATA` sezione. Questa operazione funziona perché `Text` la proprietà è `ContentProperty` per la `Label` classe.

Gli screenshot seguenti mostrano una [`Label`](xref:Xamarin.Forms.Label) visualizzazione HTML:

![Screenshot di un'etichetta che Visualizza HTML, in iOS e Android](label-images/label-html.png)

> [!IMPORTANT]
> La visualizzazione del codice [`Label`](xref:Xamarin.Forms.Label) HTML in un è limitata ai tag HTML supportati dalla piattaforma sottostante.

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Testo formattato

Le etichette espongono una [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) proprietà che consente la presentazione del testo con più tipi di carattere e colori nella stessa visualizzazione.

La `FormattedText` proprietà è di tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString), che include una o più [`Span`](xref:Xamarin.Forms.Span) istanze, impostate tramite la [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) proprietà. Per impostare `Span` l'aspetto visivo, è possibile usare le proprietà seguenti:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor): colore dello sfondo dell'intervallo.
- `CharacterSpacing`, di tipo `double`, indica la spaziatura tra i caratteri `Span` del testo.
- [`Font`](xref:Xamarin.Forms.Span.Font): tipo di carattere per il testo nell'intervallo.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes): gli attributi del tipo di carattere per il testo nell'intervallo.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily): famiglia di caratteri a cui appartiene il tipo di carattere del testo nell'intervallo.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize): dimensione del tipo di carattere per il testo nell'intervallo.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor): colore per il testo nell'intervallo. Questa proprietà è obsoleta ed è stata sostituita `TextColor` dalla proprietà.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight): moltiplicatore da applicare all'altezza della riga predefinita dell'intervallo. Per ulteriori informazioni, vedere [altezza riga](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style): stile da applicare all'intervallo.
- [`Text`](xref:Xamarin.Forms.Span.Text): testo dell'intervallo.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor): colore per il testo nell'intervallo.
- `TextDecorations`: decorazioni da applicare al testo nell'intervallo. Per altre informazioni, vedere [decorazioni di testo](#text-decorations).

Le [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)proprietà [`Text`](xref:Xamarin.Forms.Span.Text)associabili [`Text`](xref:Xamarin.Forms.Span.Text) , e hanno una modalità di [`OneWay`](xref:Xamarin.Forms.BindingMode)associazione predefinita. Per ulteriori informazioni su questa modalità di associazione, vedere [la modalità di associazione predefinita](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) nella Guida alla [modalità di associazione](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md) .

Inoltre, la [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) proprietà può essere utilizzata per definire una raccolta di riconoscitori di movimento che risponderanno ai movimenti in [`Span`](xref:Xamarin.Forms.Span).

> [!NOTE]
> Non è possibile visualizzare il codice HTML in un [`Span`](xref:Xamarin.Forms.Span)oggetto.

Nell'esempio di codice XAML riportato `FormattedText` di seguito viene illustrata [`Span`](xref:Xamarin.Forms.Span) una proprietà costituita da tre istanze:

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
> La [`Text`](xref:Xamarin.Forms.Span.Text) proprietà di un `Span` oggetto può essere impostata tramite Data Binding. Per altre informazioni, vedere [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Si noti che [`Span`](xref:Xamarin.Forms.Span) un oggetto può anche rispondere a qualsiasi movimento aggiunto alla [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) raccolta dell'intervallo. Ad esempio, un [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) è stato aggiunto al secondo `Span` negli esempi di codice precedenti. `Span` Pertanto, quando viene toccata `TapGestureRecognizer` , risponderà eseguendo l'oggetto `ICommand` definito dalla [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) proprietà. Per altre informazioni sui riconoscitori di movimento, vedere [movimenti Novell. Forms](~/xamarin-forms/app-fundamentals/gestures/index.md).

Gli screenshot seguenti mostrano il risultato dell'impostazione della `FormattedString` proprietà su tre `Span` istanze:

![Esempio di FormattedText etichetta](label-images/formattedtext.png)

## <a name="line-height"></a>Altezza riga

L'altezza verticale di un [`Label`](xref:Xamarin.Forms.Label) oggetto e [`Span`](xref:Xamarin.Forms.Span) un oggetto possono essere personalizzati impostando [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) la [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) proprietà o `double` su un valore. In iOS e Android questi valori sono moltiplicatori dell'altezza della riga originale e nel piattaforma UWP (Universal Windows Platform) (UWP) il `Label.LineHeight` valore della proprietà è un moltiplicatore delle dimensioni del carattere dell'etichetta.

> [!NOTE]
>
> - In iOS le [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) proprietà e [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) modificano l'altezza della riga del testo che si adatta a una singola riga e il testo che esegue il wrapping su più righe.
> - In Android le proprietà [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) e [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) cambiano solo l'altezza di riga del testo che esegue il wrapping su più righe.
> - In UWP la [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) proprietà modifica l'altezza della riga del testo che esegue il wrapping su più righe e la [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) proprietà non ha alcun effetto.

Nell'esempio di codice XAML seguente viene [`LineHeight`](xref:Xamarin.Forms.Label.LineHeight) illustrata l' [`Label`](xref:Xamarin.Forms.Label)impostazione della proprietà su un oggetto:

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

Gli screenshot seguenti mostrano il risultato dell'impostazione della [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) proprietà su 1,8:

![Esempio di etichetta LineHeight](label-images/label-lineheight.png)

Nell'esempio di codice XAML seguente viene [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) illustrata l' [`Span`](xref:Xamarin.Forms.Span)impostazione della proprietà su un oggetto:

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

Gli screenshot seguenti mostrano il risultato dell'impostazione della [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) proprietà su 1,8:

![Esempio di span LineHeight](label-images/span-lineheight.png)

## <a name="padding"></a>Riempimento

La spaziatura interna rappresenta lo spazio tra un elemento e i relativi elementi figlio e viene utilizzato per separare l'elemento dal relativo contenuto. La spaziatura interna può [`Label`](xref:Xamarin.Forms.Label) essere applicata alle istanze `Label.Padding` impostando la [`Thickness`](xref:Xamarin.Forms.Thickness) proprietà su un valore:

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
> In iOS, quando viene [`Label`](xref:Xamarin.Forms.Label) creato un oggetto che imposta `Padding` la proprietà, viene applicata la spaziatura interna e il valore di riempimento può essere aggiornato in un secondo momento. Tuttavia, quando viene `Label` creato un oggetto che non imposta `Padding` la proprietà, il tentativo di impostarlo in un secondo momento non avrà alcun effetto.
>
> In Android e in piattaforma UWP (Universal Windows Platform), `Padding` `Label` è possibile specificare il valore della proprietà quando viene creato o versione successiva.

Per ulteriori informazioni sulla spaziatura interna, vedere [margini e spaziatura interna](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="hyperlinks"></a>Collegamenti ipertestuali

Il testo visualizzato dalle [`Label`](xref:Xamarin.Forms.Label) istanze [`Span`](xref:Xamarin.Forms.Span) di e può essere trasformato in collegamenti ipertestuali con l'approccio seguente:

1. Impostare le `TextColor` proprietà `TextDecoration` e dell'oggetto [`Label`](xref:Xamarin.Forms.Label) o [`Span`](xref:Xamarin.Forms.Span).
1. Aggiungere un [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) oggetto alla [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) raccolta di [`Label`](xref:Xamarin.Forms.Label) o [`Span`](xref:Xamarin.Forms.Span), la cui [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) proprietà è associata a un `ICommand`oggetto e la [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) cui proprietà contiene l'URL da aprire.
1. Definire l' `ICommand` oggetto che verrà eseguito da [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer).
1. Scrivere il codice che verrà eseguito da `ICommand`.

L'esempio di codice seguente, tratto dall'esempio [demos di collegamento ipertestuale](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , Mostra un oggetto [`Label`](xref:Xamarin.Forms.Label) il [`Span`](xref:Xamarin.Forms.Span) cui contenuto è impostato da più istanze:

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

In questo esempio, la prima e la [`Span`](xref:Xamarin.Forms.Span) terza istanza includono testo, mentre la seconda `Span` rappresenta un collegamento ipertestuale toccabile. Il colore del testo è impostato su blu e presenta un effetto di testo sottolineato. Viene così creato l'aspetto di un collegamento ipertestuale, come illustrato nelle schermate seguenti:

[![Collegamenti ipertestuali](label-images/hyperlinks-small.png "Collegamenti ipertestuali")](label-images/hyperlinks-large.png#lightbox)

Quando viene toccato il collegamento ipertestuale, [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) risponderà eseguendo l'oggetto `ICommand` definito dalla relativa [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) proprietà. Inoltre, l'URL specificato dalla [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) proprietà verrà passato a `ICommand` come parametro.

Il code-behind per la pagina XAML contiene l' `TapCommand` implementazione:

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

`TapCommand` Esegue il `Launcher.OpenAsync` metodo passando il valore della [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) proprietà come parametro. Il `Launcher.OpenAsync` metodo viene fornito da Novell. Essentials e apre l'URL in un Web browser. Pertanto, l'effetto complessivo è che, quando il collegamento ipertestuale viene toccato sulla pagina, viene visualizzato un Web browser e l'URL associato al collegamento ipertestuale viene indirizzato a.

### <a name="creating-a-reusable-hyperlink-class"></a>Creazione di una classe di collegamento ipertestuale riutilizzabile

L'approccio precedente alla creazione di un collegamento ipertestuale richiede la scrittura di codice ripetitivo ogni volta che è necessario un collegamento ipertestuale nell'applicazione. Tuttavia, entrambe le [`Label`](xref:Xamarin.Forms.Label) classi [`Span`](xref:Xamarin.Forms.Span) e possono essere sottoclassate per creare `HyperlinkLabel` classi `HyperlinkSpan` e, con il riconoscimento del movimento e il codice di formattazione del testo aggiunto.

L'esempio di codice seguente, tratto dall'esempio [demos di collegamento ipertestuale](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , Mostra una `HyperlinkSpan` classe:

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

La `HyperlinkSpan` classe definisce una `Url` proprietà e associata [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)e il costruttore imposta l'aspetto del collegamento ipertestuale e [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) l'oggetto che risponderà quando viene toccato il collegamento ipertestuale. Quando viene `HyperlinkSpan` toccato un oggetto, il `TapGestureRecognizer` risponderà eseguendo il `Launcher.OpenAsync` metodo per aprire l'URL, specificato dalla `Url` proprietà, in un Web browser.

La `HyperlinkSpan` classe può essere utilizzata aggiungendo un'istanza della classe a XAML:

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

Nelle sezioni precedenti sono state [`Label`](xref:Xamarin.Forms.Label) descritte [`Span`](xref:Xamarin.Forms.Span) le impostazioni e le proprietà per ogni singola istanza. Tuttavia, i set di proprietà possono essere raggruppati in uno stile che viene applicato in modo coerente a una o più visualizzazioni. Questo può migliorare la leggibilità del codice e rendere più semplice l'implementazione delle modifiche di progettazione. Per ulteriori informazioni, vedere [stili](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Link correlati

- [Testo (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Collegamenti ipertestuali (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Creazione di app per dispositivi mobili con Novell. Forms, capitolo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API Etichetta](xref:Xamarin.Forms.Label)
- [API span](xref:Xamarin.Forms.Span)
