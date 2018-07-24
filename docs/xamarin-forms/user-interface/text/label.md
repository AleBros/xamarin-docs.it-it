---
title: Etichetta di xamarin. Forms
description: Questo articolo illustra come usare la classe di etichetta di xamarin. Forms per visualizzare il testo singolo e multi-linea nelle applicazioni.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/16/2018
ms.openlocfilehash: b5069381126db0859508480df5596ed5ec85686f
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39203036"
---
# <a name="xamarinforms-label"></a>Etichetta di xamarin. Forms

_Testo visualizzato in xamarin. Forms_

Il [ `Label` ](xref:Xamarin.Forms.Label) vista utilizzata per la visualizzazione di testo, un o più righe. Le etichette possono avere tipi di carattere personalizzati (famiglie di dimensioni e opzioni) e il testo colorato.

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Il troncamento e ritorno a capo

Le etichette possono essere impostate su gestione di testo non può essere contenuta in una sola riga in uno dei diversi modi, esposti dal `LineBreakMode` proprietà. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) è un'enumerazione con i valori seguenti:

- **HeadTruncation** &ndash; Tronca l'intestazione del testo, che mostra la fine.
- **CharacterWrap** &ndash; esegue il wrapping di testo in una nuova riga in un confine di carattere.
- **MiddleTruncation** &ndash; Visualizza l'inizio e alla fine del testo, con la sostituzione intermedio dai puntini di sospensione.
- **NoWrap** &ndash; non va a capo del testo, visualizzando solo il testo come può rientrare in una sola riga.
- **TailTruncation** &ndash; Mostra l'inizio del testo troncamento di fine.
- **WordWrap** &ndash; esegue il wrapping di testo il confine di parola.

## <a name="fonts"></a>Tipi di carattere

Per altre informazioni su come specificare i tipi di carattere in un `Label`, vedere [tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="colors"></a>Colori

`Label`s può essere impostato su utilizza un colore del testo personalizzato tramite l'associabile [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) proprietà.

Particolare attenzione è necessario assicurarsi che i colori saranno utilizzabili in ogni piattaforma. Poiché ogni piattaforma ha valori predefiniti diversi per i colori di sfondo e testo, è necessario prestare attenzione a scegliere un valore predefinito che funziona su ognuno.

Per impostare il colore del testo di un'etichetta, utilizzare il seguente XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo">
    <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a label." />
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
        var label = new Label { Text="This is a label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
        this.Content = layout;
    }
}
```

Le schermate seguenti illustrano il risultato dell'impostazione di `TextColor` proprietà:

![](label-images/textcolor.png "Esempio di etichetta TextColor")

Per altre informazioni sui colori, vedere [colori](~/xamarin-forms/user-interface/colors.md).

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Testo formattato

Le etichette di espongono una [ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText) proprietà che consente la presentazione del testo con più tipi di carattere e colori nella stessa visualizzazione.

Il `FormattedText` proprietà è di tipo [ `FormattedString` ](xref:Xamarin.Forms.FormattedString), che include uno o più [ `Span` ](xref:Xamarin.Forms.Span) istanze, viene impostate tramite la [ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans) proprietà . Ogni `Span` possiede le proprietà seguenti:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) : il colore di sfondo span.
- [`Font`](xref:Xamarin.Forms.Span.Font) : il tipo di carattere per il testo nel controllo span.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) : gli attributi del tipo di carattere per il testo nel controllo span.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) – la famiglia di caratteri a cui appartiene il tipo di carattere per il testo nel controllo span.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) – le dimensioni del carattere per il testo nel controllo span.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) : il colore del testo nel controllo span. Questa proprietà è obsoleta ed è stata sostituita dal `TextColor` proprietà.
- [`Style`](xref:Xamarin.Forms.Span.Style) – lo stile da applicare all'intervallo.
- [`Text`](xref:Xamarin.Forms.Span.Text) -il testo dell'intervallo.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) : il colore del testo nel controllo span.

L'esempio XAML seguente illustra un `FormattedText` proprietà che è costituito da tre `Span` istanze:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo - XAML">
    <StackLayout Padding="5,10">
        ...
        <Label>
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Red Bold, " TextColor="Red" FontAttributes="Bold" />
                    <Span Text="default, " Style="{DynamicResource BodyStyle}" />
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
        formattedString.Spans.Add (new Span { Text = "default, ", Style = Device.Styles.BodyStyle });
        formattedString.Spans.Add (new Span { Text = "italic small.", FontAttributes = FontAttributes.Italic, FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)) });

        layout.Children.Add (new Label { FormattedText = formattedString });
        this.Content = layout;
    }
}
```

> [!NOTE]
> Il [ `Text` ](xref:Xamarin.Forms.Span.Text) proprietà di un `Span` possono essere impostate tramite associazione dati. Per altre informazioni, vedere [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Le schermate seguenti illustrano il risultato dell'impostazione di `FormattedString` tre proprietà `Span` istanze:

![](label-images/formattedtext.png "Esempio di etichetta FormattedText")

## <a name="styling-a-label"></a>Applicazione di stili a un'etichetta

Le sezioni precedenti trattati impostazione [ `Label` ](xref:Xamarin.Forms.Label) proprietà in ogni istanza. Tuttavia, i set di proprietà possono essere raggruppati in uno stile che viene applicato in modo coerente a uno o più viste. Ciò può migliorare la leggibilità del codice e apportare modifiche alla struttura più semplice da implementare. Per altre informazioni, vedere [stili](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di App per dispositivi mobili con xamarin. Forms, capitolo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [Testo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [API di etichetta](xref:Xamarin.Forms.Label)
