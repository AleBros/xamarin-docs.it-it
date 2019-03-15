---
title: Etichetta di xamarin. Forms
description: Questo articolo illustra come usare la classe di etichetta di xamarin. Forms per visualizzare il testo singolo e multi-linea nelle applicazioni.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: e220abc575270f483c0a822ae01f979966691432
ms.sourcegitcommit: 97dca3face7c4ad5555dfaca88f5b45a70ca556d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57972676"
---
# <a name="xamarinforms-label"></a>Etichetta di xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)

_Testo visualizzato in xamarin. Forms_

Il [ `Label` ](xref:Xamarin.Forms.Label) vista utilizzata per la visualizzazione di testo, un o più righe. Le etichette prevede le decorazioni di testo, testo colorato e usare i tipi di carattere personalizzati (famiglie di dimensioni e opzioni).

## <a name="text-decorations"></a>Decorazioni di testo

Decorazioni di testo barrato e sottolineato possono essere applicate a [ `Label` ](xref:Xamarin.Forms.Label) istanze impostando il `Label.TextDecorations` a uno o più proprietà `TextDecorations` membri dell'enumerazione:

- `None`
- `Underline`
- `Strikethrough`

Nell'esempio XAML seguente viene illustrata l'impostazione di `Label.TextDecorations` proprietà:

```xaml
<Label Text="This is underlined text." TextDecorations="Underline"  />
<Label Text="This is text with strikethrough." TextDecorations="Strikethrough" />
<Label Text="This is underlined text with strikethrough." TextDecorations="Underline, Strikethrough" />
```

Il codice c# equivalente è:

```csharp
var underlineLabel = new Label { Text = "This is underlined text.", TextDecorations = TextDecorations.Underline };
var strikethroughLabel = new Label { Text = "This is text with strikethrough.", TextDecorations = TextDecorations.Strikethrough };
var bothLabel = new Label { Text = "This is underlined text with strikethrough.", TextDecorations = TextDecorations.Underline | TextDecorations.Strikethrough };
```

L'esempio di screenshot seguente mostra le `TextDecorations` applicati a membri dell'enumerazione [ `Label` ](xref:Xamarin.Forms.Label) istanze:

![](label-images/label-textdecorations.png "Etichette con le decorazioni di testo")

> [!NOTE]
> Decorazioni di testo possono essere applicate anche a [ `Span` ](xref:Xamarin.Forms.Span) istanze. Per altre informazioni sul `Span` classe, vedere [testo formattato](#Formatted_Text).

## <a name="colors"></a>Colori

Le etichette possono essere impostate su utilizza un colore del testo personalizzato tramite l'associabile [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) proprietà.

Particolare attenzione è necessario assicurarsi che i colori saranno utilizzabili in ogni piattaforma. Poiché ogni piattaforma ha valori predefiniti diversi per i colori di sfondo e testo, è necessario prestare attenzione a scegliere un valore predefinito che funziona su ognuno.

Nell'esempio XAML seguente imposta il colore del testo di un `Label`:

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

Il codice c# equivalente è:

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

Le schermate seguenti illustrano il risultato dell'impostazione di `TextColor` proprietà:

![](label-images/textcolor.png "Esempio di etichetta TextColor")

Per altre informazioni sui colori, vedere [colori](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Tipi di carattere

Per altre informazioni su come specificare i tipi di carattere in un `Label`, vedere [tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Il troncamento e ritorno a capo

Le etichette possono essere impostate su gestione di testo non può essere contenuta in una sola riga in uno dei diversi modi, esposti dal `LineBreakMode` proprietà. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) è un'enumerazione con i valori seguenti:

- **HeadTruncation** &ndash; Tronca l'intestazione del testo, che mostra la fine.
- **CharacterWrap** &ndash; esegue il wrapping di testo in una nuova riga in un confine di carattere.
- **MiddleTruncation** &ndash; Visualizza l'inizio e alla fine del testo, con la sostituzione intermedio dai puntini di sospensione.
- **NoWrap** &ndash; non va a capo del testo, visualizzando solo il testo come può rientrare in una sola riga.
- **TailTruncation** &ndash; Mostra l'inizio del testo troncamento di fine.
- **WordWrap** &ndash; esegue il wrapping di testo il confine di parola.

## <a name="displaying-a-specific-number-of-lines"></a>Visualizzazione di un determinato numero di righe

Il numero di righe visualizzate da un [ `Label` ](xref:Xamarin.Forms.Label) può essere specificato impostando il `Label.MaxLines` proprietà su un `int` valore:

- Quando `MaxLines` è 0, il `Label` rispetta il valore della [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) proprietà per visualizzare sia sufficiente una riga, probabilmente troncata, o tutte le righe con tutto il testo.
- Quando `MaxLines` è 1, il risultato è identico all'impostazione di [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) proprietà [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode), [ `HeadTruncation` ](xref:Xamarin.Forms.LineBreakMode), [ `MiddleTruncation` ](xref:Xamarin.Forms.LineBreakMode), o [ `TailTruncation` ](xref:Xamarin.Forms.LineBreakMode). Tuttavia, il `Label` rispetterà il valore della [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) proprietà in relazione al posizionamento dei puntini di sospensione, se applicabile.
- Quando `MaxLines` è maggiore di 1, il `Label` verranno visualizzati fino al numero specificato di righe, rispettando il valore della [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) proprietà in relazione al posizionamento dei puntini di sospensione, se applicabile. Tuttavia, impostando il `MaxLines` proprietà su un valore maggiore di 1 non ha alcun effetto se il [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) viene impostata su [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode).

Nell'esempio XAML seguente viene illustrata l'impostazione di `MaxLines` proprietà in un [ `Label` ](xref:Xamarin.Forms.Label):

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       MaxLines="2" />
```

Il codice c# equivalente è:

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  MaxLines = 2
};
```

Le schermate seguenti illustrano il risultato dell'impostazione di `MaxLines` proprietà su 2, quando il testo è sufficientemente lungo da occupare più di 2 righe:

![](label-images/label-maxlines.png "Esempio di etichetta MaxLines")

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Testo formattato

Le etichette di espongono una [ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText) proprietà che consente la presentazione del testo con più tipi di carattere e colori nella stessa visualizzazione.

Il `FormattedText` proprietà è di tipo [ `FormattedString` ](xref:Xamarin.Forms.FormattedString), che include uno o più [ `Span` ](xref:Xamarin.Forms.Span) istanze, viene impostate tramite la [ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans) proprietà . Nell'esempio `Span` proprietà possono essere usate per impostare l'aspetto visivo:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) : il colore di sfondo span.
- [`Font`](xref:Xamarin.Forms.Span.Font) : il tipo di carattere per il testo nel controllo span.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) : gli attributi del tipo di carattere per il testo nel controllo span.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) – la famiglia di caratteri a cui appartiene il tipo di carattere per il testo nel controllo span.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) – le dimensioni del carattere per il testo nel controllo span.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) : il colore del testo nel controllo span. Questa proprietà è obsoleta ed è stata sostituita dal `TextColor` proprietà.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) -il moltiplicatore da applicare per l'altezza della riga predefinita dell'intervallo. Per altre informazioni, vedere [altezza riga](#line-height).
- [`Text`](xref:Xamarin.Forms.Span.Text)  – lo stile da applicare all'intervallo.
- [`Text`](xref:Xamarin.Forms.Span.Text) -il testo dell'intervallo.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) : il colore del testo nel controllo span.
- `TextDecorations` -gli effetti da applicare al testo nel controllo span. Per altre informazioni, vedere [decorazioni di testo](#text-decorations).

> [!NOTE]
> Il [ `BackgroundColor` ](xref:Xamarin.Forms.Span.BackgroundColor), [ `Text` ](xref:Xamarin.Forms.Span.Text), e [ `Text` ](xref:Xamarin.Forms.Span.Text) proprietà associabili hanno una modalità di associazione predefinito di [ `OneWay` ](xref:Xamarin.Forms.BindingMode). Per altre informazioni su questa modalità di associazione, vedere [la modalità di associazione predefinito](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) nel [modalità di associazione](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md) Guida.

Inoltre, il [ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers) proprietà può essere utilizzata per definire una raccolta di riconoscitori di movimento che risponde ai movimenti nella [ `Span` ](xref:Xamarin.Forms.Span).

L'esempio XAML seguente illustra un `FormattedText` proprietà che è costituito da tre [ `Span` ](xref:Xamarin.Forms.Span) istanze:

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

Il codice c# equivalente è:

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
> Il [ `Text` ](xref:Xamarin.Forms.Span.Text) proprietà di un `Span` possono essere impostate tramite associazione dati. Per altre informazioni, vedere [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Si noti che un [ `Span` ](xref:Xamarin.Forms.Span) inoltre può rispondere a tutti i movimenti che vengono aggiunti per l'intervallo [ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers) raccolta. Ad esempio, un [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) è stato aggiunto al secondo `Span` negli esempi di codice riportato sopra. Pertanto, quando ciò `Span` toccando il `TapGestureRecognizer` risponderà eseguendo il `ICommand` definito dal [ `Command` ](xref:Xamarin.Forms.TapGestureRecognizer.Command) proprietà. Per altre informazioni sui riconoscitori di movimento, vedere [movimenti di xamarin. Forms](~/xamarin-forms/app-fundamentals/gestures/index.md).

Le schermate seguenti illustrano il risultato dell'impostazione di `FormattedString` tre proprietà `Span` istanze:

![](label-images/formattedtext.png "Esempio di etichetta FormattedText")

## <a name="line-height"></a>Altezza riga

L'altezza verticale di un [ `Label` ](xref:Xamarin.Forms.Label) e un [ `Span` ](xref:Xamarin.Forms.Span) può essere personalizzato tramite l'impostazione di [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) proprietà o [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) a un `double` valore. In iOS e Android tali valori sono i moltiplicatori dell'altezza della riga originale e in Universal Windows Platform (UWP) il `Label.LineHeight` valore della proprietà è un moltiplicatore delle dimensioni del carattere etichetta.

> [!NOTE]
> - In iOS, il [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) e [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) proprietà modificare l'altezza della riga di testo che rientra in una singola riga e che esegue il wrapping su più righe.
> - In Android, il [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) e [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) proprietà solo modificare l'altezza della riga di testo che esegue il wrapping su più righe.
> - Nella piattaforma UWP, il [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) proprietà viene modificata l'altezza della riga di testo che esegue il wrapping su più righe, e il [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) proprietà non ha alcun effetto.

Nell'esempio XAML seguente viene illustrata l'impostazione di [ `LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) proprietà in un [ `Label` ](xref:Xamarin.Forms.Label):

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       LineHeight="1.8" />
```

Il codice c# equivalente è:

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  LineHeight = 1.8
};
```

Le schermate seguenti illustrano il risultato dell'impostazione di [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) proprietà 1.8:

![](label-images/label-lineheight.png "Esempio di etichetta LineHeight")

Nell'esempio XAML seguente viene illustrata l'impostazione di [ `LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) proprietà in un [ `Span` ](xref:Xamarin.Forms.Span):

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

Il codice c# equivalente è:

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

Le schermate seguenti illustrano il risultato dell'impostazione di [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) proprietà 1.8:

![](label-images/span-lineheight.png "Esempio LineHeight span")

## <a name="hyperlinks"></a>Collegamenti ipertestuali

Il testo visualizzato da [ `Label` ](xref:Xamarin.Forms.Label) e [ `Span` ](xref:Xamarin.Forms.Span) istanze possono essere trasformate in collegamenti ipertestuali con l'approccio seguente:

1. Impostare il `TextColor` e `TextDecoration` le proprietà del [ `Label` ](xref:Xamarin.Forms.Label) oppure [ `Span` ](xref:Xamarin.Forms.Span).
1. Aggiungere un [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) per il [ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers) raccolta del [ `Label` ](xref:Xamarin.Forms.Label) o [ `Span` ](xref:Xamarin.Forms.Span), la cui [ `Command` ](xref:Xamarin.Forms.TapGestureRecognizer.Command) proprietà viene associata a un `ICommand`e il cui [ `CommandParameter` ](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) proprietà contiene l'URL da aprire.
1. Definire il `ICommand` che verrà eseguito per il [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer).
1. Scrivere il codice che verrà eseguito dalla `ICommand`.

Esempio di codice seguente, tratto dal [demo di collegamento ipertestuale](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/HyperlinkDemos) esempio, viene illustrata una [ `Label` ](xref:Xamarin.Forms.Label) il cui contenuto è impostato da più [ `Span` ](xref:Xamarin.Forms.Span) istanze:

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

In questo esempio, il primo e terzo [ `Span` ](xref:Xamarin.Forms.Span) istanze includono testo, mentre il secondo `Span` rappresenta un collegamento ipertestuale tappable. Si ha il colore del testo impostato su blu e ha un effetto di testo sottolineato. Ciò consente di creare l'aspetto di un collegamento ipertestuale, come illustrato negli screenshot seguenti:

[![I collegamenti ipertestuali](label-images/hyperlinks-small.png "collegamenti ipertestuali")](label-images/hyperlinks-large.png#lightbox)

Quando si tocca il collegamento ipertestuale, il [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) risponderà eseguendo il `ICommand` definito da relativo [ `Command` ](xref:Xamarin.Forms.TapGestureRecognizer.Command) proprietà. Inoltre, l'URL specificato dal [ `CommandParameter` ](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) verrà passata alla proprietà di `ICommand` come parametro.

Contiene il code-behind per la pagina XAML di `TapCommand` implementazione:

```csharp
public partial class MainPage : ContentPage
{
    public ICommand TapCommand => new Command<string>(OpenBrowser);

    public MainPage()
    {
        InitializeComponent();
        BindingContext = this;
    }

    void OpenBrowser(string url)
    {
        Device.OpenUri(new Uri(url));
    }
}
```

Il `TapCommand` viene eseguita la `OpenBrowser` , passando il [ `TapGestureRecognizer.CommandParameter` ](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) come parametro un valore di proprietà. A sua volta, questo metodo chiama il [ `Device.OpenUri` ](xref:Xamarin.Forms.Device.OpenUri*) metodo per aprire l'URL in un web browser. Pertanto, l'effetto generale è che quando toccando il collegamento ipertestuale nella pagina, viene visualizzato un web browser e viene esplorato l'URL associato al collegamento ipertestuale.

### <a name="creating-a-reusable-hyperlink-class"></a>Creazione di una classe riutilizzabile collegamento ipertestuale

L'approccio precedente per creare un collegamento ipertestuale richiede la scrittura di codice ripetitivo ogni volta che è necessario un collegamento ipertestuale nell'applicazione. Tuttavia, entrambe le [ `Label` ](xref:Xamarin.Forms.Label) e [ `Span` ](xref:Xamarin.Forms.Span) classi possono essere sottoclassate per creare `HyperlinkLabel` e `HyperlinkSpan` classi, con il riconoscitore di movimento e codice di formattazione del testo aggiunto qui.

Esempio di codice seguente, tratto dal [demo di collegamento ipertestuale](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/HyperlinkDemos) esempio, viene illustrata una `HyperlinkSpan` classe:

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
            Command = new Command(() => Device.OpenUri(new Uri(Url)))
        });
    }
}
```

Il `HyperlinkSpan` classe definisce un `Url` proprietà e associato [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty), e il costruttore imposta l'aspetto di collegamento ipertestuale e la [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) che risponderà Quando si tocca il collegamento ipertestuale. Quando un `HyperlinkSpan` toccando, il `TapGestureRecognizer` risponderà eseguendo il [ `Device.OpenUri` ](xref:Xamarin.Forms.Device.OpenUri*) metodo per aprire l'URL, specificata dal `Url` proprietà, in un web browser.

Il `HyperlinkSpan` classe può essere utilizzata tramite l'aggiunta di un'istanza della classe per il XAML:

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

## <a name="styling-labels"></a>Lo stile etichette

Le sezioni precedenti trattati impostazione [ `Label` ](xref:Xamarin.Forms.Label) e [ `Span` ](xref:Xamarin.Forms.Span) proprietà in ogni istanza. Tuttavia, i set di proprietà possono essere raggruppati in uno stile che viene applicato in modo coerente a uno o più viste. Ciò può migliorare la leggibilità del codice e apportare modifiche alla struttura più semplice da implementare. Per altre informazioni, vedere [stili](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Collegamenti correlati

- [Testo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [Collegamenti ipertestuali (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Hyperlinks)
- [Creazione di App per dispositivi mobili con xamarin. Forms, capitolo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API di etichetta](xref:Xamarin.Forms.Label)
- [Intervallo di API](xref:Xamarin.Forms.Span)
