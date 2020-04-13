---
title: Etichetta Xamarin.Forms
description: In questo articolo viene illustrato come utilizzare la classe Xamarin.Forms Label per visualizzare testo a riga singola e su più righe nelle applicazioni.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: bed6b8a774ecb352427f16b78d10e50821f92701
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987595"
---
# <a name="xamarinforms-label"></a>Etichetta Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Testo visualizzato in Xamarin.Forms_

La [`Label`](xref:Xamarin.Forms.Label) vista viene utilizzata per la visualizzazione del testo, sia a riga singola che a più righe. Le etichette possono avere decorazioni di testo, testo colorato e utilizzare caratteri personalizzati (famiglie, dimensioni e opzioni).

## <a name="text-decorations"></a>Decorazioni di testo

Le decorazioni di testo sottolineate [`Label`](xref:Xamarin.Forms.Label) e barrate possono essere applicate alle istanze impostando la `Label.TextDecorations` proprietà su uno o più `TextDecorations` membri di enumerazione:

- `None`
- `Underline`
- `Strikethrough`

Nell'esempio XAML seguente `Label.TextDecorations` viene illustrata l'impostazione della proprietà:

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

Le schermate seguenti `TextDecorations` mostrano [`Label`](xref:Xamarin.Forms.Label) i membri dell'enumerazione applicati alle istanze:The following screenshots show the enumeration members applied to instances:

![Etichette con decorazioni di testo](label-images/label-textdecorations.png)

> [!NOTE]
> Le decorazioni di testo [`Span`](xref:Xamarin.Forms.Span) possono essere applicate anche alle istanze. Per ulteriori informazioni `Span` sulla classe , vedere [Testo formattato](#Formatted_Text).

## <a name="character-spacing"></a>Spaziatura caratteri

La spaziatura dei [`Label`](xref:Xamarin.Forms.Label) caratteri può `Label.CharacterSpacing` essere `double` applicata alle istanze impostando la proprietà su un valore:Character spacing can be applied to instances by setting the property to a value:

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

Il codice C# equivalente è il seguente:

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

Il risultato è che i caratteri [`Label`](xref:Xamarin.Forms.Label) nel `CharacterSpacing` testo visualizzato da sono unità indipendenti dal dispositivo distanziate.

## <a name="new-lines"></a>Nuove righe

Esistono due tecniche principali per forzare il testo in un [`Label`](xref:Xamarin.Forms.Label) in una nuova riga, da XAML:There are two main techniques for for cing text in a onto a new line, from XAML:

1. Utilizzare il carattere di avanzamento riga unicode, ovvero "&#10;".
1. Specificare il testo utilizzando la sintassi degli *elementi proprietà.*

Il codice seguente mostra un esempio di entrambe le tecniche:The following code shows an example of both techniques:

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

Nel linguaggio C, il testo può essere forzato su una nuova riga con il carattere "n":

```csharp
Label label = new Label { Text = "First line\nSecond line" };
```

## <a name="colors"></a>Colori

Le etichette possono essere impostate per utilizzare [`TextColor`](xref:Xamarin.Forms.Label.TextColor) un colore di testo personalizzato tramite la proprietà associabile.

Particolare attenzione è necessaria per garantire che i colori saranno utilizzabili su ogni piattaforma. Poiché ogni piattaforma ha valori predefiniti diversi per i colori del testo e dello sfondo, è necessario prestare attenzione a scegliere un valore predefinito che funzioni su ognuna.

Nell'esempio XAML seguente viene `Label`impostato il colore del testo di un oggetto :

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

Le schermate seguenti mostrano `TextColor` il risultato dell'impostazione della proprietà:

![Esempio TextColor label](label-images/textcolor.png)

Per ulteriori informazioni sui colori, consultate [Colori.](~/xamarin-forms/user-interface/colors.md)

## <a name="fonts"></a>Tipi di carattere

Per ulteriori informazioni sulla specifica `Label`dei tipi di carattere in un oggetto , vedere [Tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Troncamento e avvolgimento

Le etichette possono essere impostate per gestire il testo che non può `LineBreakMode` essere contenuto in una riga in uno dei diversi modi, esposti dalla proprietà . [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)è un'enumerazione con i seguenti valori:

- **HeadTruncation** &ndash; tronca l'intestazione del testo, mostrando la fine.
- **CharacterWrap** &ndash; manda a capo il testo su una nuova riga in corrispondenza di un limite di carattere.
- **MiddleTruncation** &ndash; visualizza l'inizio e la fine del testo, con il centro sostituito da un'ellisse.
- **NoWrap** &ndash; non manda a capo il testo, visualizzando solo la quantità di testo che può contenere su una riga.
- **TailTruncation** &ndash; mostra l'inizio del testo, troncando la fine.
- **WordWrap** &ndash; manda a capo il testo in corrispondenza del confine di parola.

## <a name="display-a-specific-number-of-lines"></a>Visualizzare un numero specifico di righe

Il numero di righe [`Label`](xref:Xamarin.Forms.Label) visualizzate da un `Label.MaxLines` oggetto può `int` essere specificato impostando la proprietà su un valore:

- Quando `MaxLines` è -1, che è `Label` il valore predefinito, [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) il rispetta il valore della proprietà per visualizzare solo una riga, possibilmente troncata, o tutte le righe con tutto il testo.
- Quando `MaxLines` è 0, l'oggetto `Label` non viene visualizzato.
- Quando `MaxLines` è 1, il risultato [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) è [`NoWrap`](xref:Xamarin.Forms.LineBreakMode) [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode)identico all'impostazione della proprietà su , , [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode), o [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode). Tuttavia, `Label` il rispetterà [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) il valore della proprietà per quanto riguarda il posizionamento di un puntine, se applicabile.
- Quando `MaxLines` è maggiore di `Label` 1, verrà visualizzato fino al numero di righe [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) specificato, rispettando il valore della proprietà per quanto riguarda la posizione di un puntini di sospensione, se applicabile. Tuttavia, `MaxLines` l'impostazione della proprietà su un [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) valore maggiore [`NoWrap`](xref:Xamarin.Forms.LineBreakMode)di 1 non ha alcun effetto se la proprietà è impostata su .

Nell'esempio XAML seguente `MaxLines` viene [`Label`](xref:Xamarin.Forms.Label)illustrata l'impostazione della proprietà su un :

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

Le schermate seguenti mostrano `MaxLines` il risultato dell'impostazione della proprietà su 2, quando il testo è abbastanza lungo da occupare più di 2 righe:

![Esempio di etichetta MaxLines](label-images/label-maxlines.png)

## <a name="display-html"></a>Visualizzare HTML

La [`Label`](xref:Xamarin.Forms.Label) classe `TextType` dispone di una `Label` proprietà , che determina se l'istanza deve visualizzare testo normale o testo HTML. Questa proprietà deve essere impostata su `TextType` uno dei membri dell'enumerazione:

- `Text`indica che `Label` l'oggetto visualizzerà testo normale ed `Label.TextType` è il valore predefinito della proprietà.
- `Html`indica che `Label` l'oggetto visualizzerà testo HTML.

[`Label`](xref:Xamarin.Forms.Label) Pertanto, le istanze `Label.TextType` possono `Html`visualizzare HTML `Label.Text` impostando la proprietà su , e la proprietà su una stringa HTML:

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

Nell'esempio precedente, i caratteri virgolette doppie `\` nel codice HTML devono essere sottoposti a escape utilizzando il simbolo.

In XAML, le stringhe HTML possono diventare illeggibili `<` a `>` causa dell'ulteriore escaping dei simboli e :

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

In alternativa, per una maggiore leggibilità il `CDATA` codice HTML può essere inline in una sezione:

```xaml
<Label TextType="Html">
    <![CDATA[
    This is <strong style="color:red">HTML</strong> text.
    ]]>
</Label>
```

In questo esempio, la `Label.Text` proprietà è impostata sulla stringa `CDATA` HTML che è inline nella sezione. Questo funziona `Text` perché la `ContentProperty` proprietà `Label` è la per la classe.

Le schermate seguenti [`Label`](xref:Xamarin.Forms.Label) mostrano un codice HTML di visualizzazione:

![Screenshot di un'etichetta che visualizza HTML, su iOS e Android](label-images/label-html.png)

> [!IMPORTANT]
> La visualizzazione di [`Label`](xref:Xamarin.Forms.Label) HTML in un è limitata ai tag HTML supportati dalla piattaforma sottostante.

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Testo formattato

Le etichette [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) espongono una proprietà che consente la presentazione di testo con più tipi di carattere e colori nella stessa visualizzazione.

La `FormattedText` proprietà è [`FormattedString`](xref:Xamarin.Forms.FormattedString)di tipo , [`Span`](xref:Xamarin.Forms.Span) che comprende una [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) o più istanze, impostate tramite la proprietà . Le `Span` seguenti proprietà possono essere utilizzate per impostare l'aspetto visivo:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)– il colore dello sfondo dell'intervallo.
- `CharacterSpacing`, di `double`tipo , è la `Span` spaziatura tra i caratteri del testo.
- [`Font`](xref:Xamarin.Forms.Span.Font)– il tipo di carattere per il testo nell'intervallo.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes)– gli attributi del carattere per il testo nell'intervallo.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily)– la famiglia di caratteri a cui appartiene il tipo di carattere per il testo nell'intervallo.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize)– la dimensione del carattere per il testo nell'intervallo.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)– il colore per il testo nell'intervallo. Questa proprietà è obsoleta ed `TextColor` è stata sostituita dalla proprietà .
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight)- il moltiplicatore da applicare all'altezza di riga predefinita dell'intervallo. Per ulteriori informazioni, vedere [Altezza riga](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style)– lo stile da applicare all'intervallo.
- [`Text`](xref:Xamarin.Forms.Span.Text)– il testo dell'intervallo.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor)– il colore per il testo nell'intervallo.
- `TextDecorations`- le decorazioni da applicare al testo nell'intervallo. Per ulteriori informazioni, consultate [Decorazioni di testo.](#text-decorations)

Le [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) [`Text`](xref:Xamarin.Forms.Span.Text)proprietà [`Text`](xref:Xamarin.Forms.Span.Text) , e associabili dispongono [`OneWay`](xref:Xamarin.Forms.BindingMode)di una modalità di associazione predefinita di . Per altre informazioni su questa modalità di associazione, vedere [la modalità](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) di associazione predefinita nella guida della [modalità di associazione.](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md)

Inoltre, la [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) proprietà può essere utilizzata per definire una raccolta di riconoscitori di movimenti che risponderanno ai movimenti nel [`Span`](xref:Xamarin.Forms.Span)oggetto .

> [!NOTE]
> Non è possibile visualizzare HTML [`Span`](xref:Xamarin.Forms.Span)in un file .

Nell'esempio XAML `FormattedText` seguente viene illustrata una proprietà costituita da tre istanze:The following XAML example demonstrates a property that consists of three [`Span`](xref:Xamarin.Forms.Span) instances:

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
> La [`Text`](xref:Xamarin.Forms.Span.Text) proprietà `Span` di un oggetto può essere impostata tramite l'associazione dati. Per altre informazioni, vedere [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Si noti che un [`Span`](xref:Xamarin.Forms.Span) oggetto può anche rispondere a [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) tutti i movimenti aggiunti alla raccolta dell'intervallo. Ad esempio, [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a è stato `Span` aggiunto al secondo negli esempi di codice precedenti. Pertanto, `Span` quando questo `TapGestureRecognizer` viene toccato il `ICommand` risponderà [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) eseguendo il definito dalla proprietà. Per ulteriori informazioni sui riconoscitori di [movimento, vedere Xamarin.Forms Gestures](~/xamarin-forms/app-fundamentals/gestures/index.md).

Le schermate seguenti mostrano `FormattedString` il risultato dell'impostazione della proprietà su tre istanze:The following screenshots show the result of setting the property to three `Span` instances:

![Esempio di Label FormattedText](label-images/formattedtext.png)

## <a name="line-height"></a>Altezza riga

L'altezza [`Label`](xref:Xamarin.Forms.Label) verticale [`Span`](xref:Xamarin.Forms.Span) di a e [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) a [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) può `double` essere personalizzata impostando la proprietà o su un valore. In iOS e Android questi valori sono moltiplicatori dell'altezza della riga `Label.LineHeight` originale e nella piattaforma UWP (Universal Windows Platform) il valore della proprietà è un moltiplicatore della dimensione del carattere dell'etichetta.

> [!NOTE]
>
> - In iOS, [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) le proprietà e modificano l'altezza della riga del testo che si adatta a una singola riga e del testo che va a capo su più righe.
> - In Android, [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) le proprietà e modificano solo l'altezza della riga del testo che va a capo su più righe.
> - In UWP, [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) la proprietà modifica l'altezza della riga del [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) testo che va a capo su più righe e la proprietà non ha alcun effetto.

Nell'esempio XAML seguente [`LineHeight`](xref:Xamarin.Forms.Label.LineHeight) viene [`Label`](xref:Xamarin.Forms.Label)illustrata l'impostazione della proprietà su un :

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

Le schermate seguenti mostrano [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) il risultato dell'impostazione della proprietà su 1.8:The following screenshots show the result of setting the property to 1.8:

![Esempio di LineHeight etichetta](label-images/label-lineheight.png)

Nell'esempio XAML seguente [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) viene [`Span`](xref:Xamarin.Forms.Span)illustrata l'impostazione della proprietà su un :

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

Le schermate seguenti mostrano [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) il risultato dell'impostazione della proprietà su 1.8:The following screenshots show the result of setting the property to 1.8:

![Esempio Span LineHeight](label-images/span-lineheight.png)

## <a name="padding"></a>Riempimento

Padding rappresenta lo spazio tra un elemento e i relativi elementi figlio e viene utilizzato per separare l'elemento dal proprio contenuto. La spaziatura [`Label`](xref:Xamarin.Forms.Label) interna può `Label.Padding` essere applicata alle istanze impostando la proprietà su un valore:Padding can be applied to instances by setting the property to a [`Thickness`](xref:Xamarin.Forms.Thickness) value:

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
> In iOS, [`Label`](xref:Xamarin.Forms.Label) quando viene creato `Padding` un oggetto che imposta la proprietà, la spaziatura interna verrà applicata e il valore di riempimento può essere aggiornato in un secondo momento. Tuttavia, `Label` quando viene creato un `Padding` oggetto che non imposta la proprietà, il tentativo di impostarla in un secondo momento non avrà alcun effetto.
>
> In Android e la piattaforma `Padding` Windows universale, il `Label` valore della proprietà può essere specificato quando viene creato l'oggetto o in un secondo momento.

Per ulteriori informazioni sulla spaziatura interna, vedere [Margini e spaziatura interna](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="hyperlinks"></a>Collegamenti ipertestuali

Il testo visualizzato [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) da e le istanze possono essere trasformati in collegamenti ipertestuali con il seguente approccio:

1. Impostare `TextColor` `TextDecoration` le proprietà [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span)e dell'oggetto o .
1. Aggiungere [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) un [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) oggetto all'insieme [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) di oggetti o `ICommand` [`Span`](xref:Xamarin.Forms.Span), la [`Label`](xref:Xamarin.Forms.Label) cui proprietà viene associata a un oggetto , e la cui [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) proprietà contiene l'URL da aprire.
1. Definire `ICommand` gli che verranno [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)eseguiti dal file .
1. Scrivere il codice che verrà `ICommand`eseguito dal file .

L'esempio di codice seguente, tratto dall'esempio [Hyperlink Demos,](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) mostra un il cui contenuto è impostato da più istanze:The following code example, taken from the Hyperlink Demos sample, shows a [`Label`](xref:Xamarin.Forms.Label) whose content is set from multiple [`Span`](xref:Xamarin.Forms.Span) instances:

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

In questo esempio, la [`Span`](xref:Xamarin.Forms.Span) prima e la `Span` terza istanza comprendono il testo, mentre la seconda rappresenta un collegamento ipertestuale tappable. Ha il colore del testo impostato su blu e ha una decorazione di testo sottolineato. Questo crea l'aspetto di un collegamento ipertestuale, come mostrato nelle schermate seguenti:

[![Collegamenti ipertestuali](label-images/hyperlinks-small.png "Collegamenti ipertestuali")](label-images/hyperlinks-large.png#lightbox)

Quando il collegamento ipertestuale [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) viene toccato, `ICommand` il [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) risponderà eseguendo il definito dalla relativa proprietà. Inoltre, l'URL specificato [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) dalla proprietà verrà `ICommand` passato al parametro come parametro.

Il code-behind per la `TapCommand` pagina XAML contiene l'implementazione:The code-behind for the XAML page contains the implementation:

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

Il `TapCommand` esegue `Launcher.OpenAsync` il metodo, [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) passando il valore della proprietà come parametro. Il `Launcher.OpenAsync` metodo viene fornito da Xamarin.Essentials e apre l'URL in un browser Web. Pertanto, l'effetto complessivo è che quando il collegamento ipertestuale viene toccato nella pagina, viene visualizzato un browser web e l'URL associato al collegamento ipertestuale viene esplorato.

### <a name="creating-a-reusable-hyperlink-class"></a>Creazione di una classe di collegamenti ipertestuali riutilizzabile

L'approccio precedente alla creazione di un collegamento ipertestuale richiede la scrittura di codice ripetitivo ogni volta che si richiede un collegamento ipertestuale nell'applicazione. Tuttavia, [`Label`](xref:Xamarin.Forms.Label) sia [`Span`](xref:Xamarin.Forms.Span) le classi e possono `HyperlinkLabel` `HyperlinkSpan` essere sottoclassate per creare e classi, con il riconoscimento dei movimenti e il codice di formattazione del testo aggiunto lì.

L'esempio di codice seguente, tratto dall'esempio [Hyperlink Demos,](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) mostra una classe:The following code example, taken from the Hyperlink Demos sample, shows a `HyperlinkSpan` class:

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

La `HyperlinkSpan` classe definisce una `Url` [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)proprietà e associato , e [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) il costruttore imposta l'aspetto del collegamento ipertestuale e l'oggetto che risponderà quando viene toccato il collegamento ipertestuale. Quando `HyperlinkSpan` viene toccato `TapGestureRecognizer` un oggetto, il `Launcher.OpenAsync` risponderà eseguendo il metodo `Url` per aprire l'URL, specificato dalla proprietà, in un browser web.

La `HyperlinkSpan` classe può essere utilizzata aggiungendo un'istanza della classe al codice XAML:

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

## <a name="styling-labels"></a>Applicazione di stili alle etichette

Nelle sezioni precedenti [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) sono stati illustrati le impostazioni e le proprietà in base alle due istanze. Tuttavia, i set di proprietà possono essere raggruppati in uno stile che viene applicato in modo coerente a una o più viste. Ciò può aumentare la leggibilità del codice e semplificare l'implementazione delle modifiche di progettazione. Per ulteriori informazioni, consultate [Stili.](~/xamarin-forms/user-interface/text/styles.md)

## <a name="related-links"></a>Collegamenti correlati

- [Testo (esempio)Text (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Collegamenti ipertestuali (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Creazione di app per dispositivi mobili con Xamarin.Forms, capitolo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API Etichetta](xref:Xamarin.Forms.Label)
- [Span API](xref:Xamarin.Forms.Span)
