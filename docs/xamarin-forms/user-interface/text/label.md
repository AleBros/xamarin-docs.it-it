---
title: Etichetta di xamarin. Forms
description: Questo articolo illustra come usare la classe di etichetta di xamarin. Forms per visualizzare il testo singolo e multi-linea nelle applicazioni.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: ce602a84ea1024dc22298a3ec1567a9a34ad4a82
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995966"
---
# <a name="xamarinforms-label"></a>Etichetta di xamarin. Forms

_Testo visualizzato in xamarin. Forms_

Il `Label` vista utilizzata per la visualizzazione di testo, un o più righe. Le etichette possono avere tipi di carattere personalizzati (famiglie di dimensioni e opzioni) e il testo colorato. Questo articolo illustra gli argomenti seguenti:

- **[Il troncamento e ritorno a capo](#Truncation_and_Wrapping)**  &ndash; troncamento e ritorno a capo le opzioni per la gestione delle situazioni in cui il testo non può essere contenuta in una sola riga.
- **[Tipo di carattere](#Font)**  &ndash; opzioni tipi di carattere.
- **[Colore](#Color)**  &ndash; opzioni colore testo etichetta.
- **[Testo formattato](#Formatted_Text)**  &ndash; opzioni per la visualizzazione di testo con più formati o stili inline.

## <a name="styling-label"></a>Stile etichetta

Le sezioni seguenti illustrano l'impostazione delle proprietà di `Label` manualmente in ogni istanza. Si noti che set di proprietà possono essere raggruppati in uno stile che viene applicato in modo coerente a uno o più viste. Ciò può migliorare la leggibilità del codice e apportare modifiche alla struttura più semplice da implementare. Visualizzare [stili](~/xamarin-forms/user-interface/text/styles.md) per altre informazioni.

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Il troncamento e ritorno a capo

Le etichette possono essere impostate su gestione di testo non può essere contenuta in una sola riga in uno dei diversi modi, esposti dal `LineBreakMode` proprietà. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) è un'enumerazione delle opzioni seguenti:

- **HeadTruncation** &ndash; Tronca l'intestazione del testo, che mostra la fine.
- **CharacterWrap** &ndash; esegue il wrapping di testo in una nuova riga in un confine di carattere.
- **MiddleTruncation** &ndash; Visualizza l'inizio e alla fine del testo, con la sostituzione intermedio dai puntini di sospensione.
- **NoWrap** &ndash; non va a capo del testo, visualizzando solo il testo come può rientrare in una sola riga.
- **TailTruncation** &ndash; Mostra l'inizio del testo troncamento di fine.
- **WordWrap** &ndash; esegue il wrapping di testo il confine di parola.

## <a name="font"></a>Tipo di carattere

Visualizzare [funziona con i tipi di carattere](~/xamarin-forms/user-interface/text/fonts.md) per altre informazioni.

## <a name="color"></a>Colore

`Label`s può essere impostato su utilizza un colore del testo personalizzato tramite l'associabile `TextColor` proprietà.

Particolare attenzione è necessario assicurarsi che i colori saranno utilizzabili in ogni piattaforma. Poiché ogni piattaforma ha valori predefiniti diversi per i colori di sfondo e testo, è necessario prestare attenzione a scegliere un valore predefinito che funziona su ognuno.

Usare il codice seguente per impostare il colore del testo di un'etichetta:

In codice:

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var label = new Label { Text="This is a label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
    }
}
```

In XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.LabelPage"
Title="Label Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a label." />
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

![](label-images/textcolor.png "Esempio di etichetta TextColor")

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Testo formattato

Le etichette di espongono un `FormattedText` proprietà che consente di presentare il testo con più tipi di carattere e colori nella stessa visualizzazione.

Il `FormattedText` proprietà è di tipo [ `FormattedString` ](xref:Xamarin.Forms.FormattedString). Le stringhe di formato sono costituite da uno o più `Span`s, ognuno dei quali con le proprietà seguenti:

- **BackgroundColor** &ndash; può essere utilizzato per impostare un colore di sfondo, ad esempio per ottenere un effetto evidenziatore dei risultati.
- **FontAttributes** &ndash; può essere impostato su in grassetto, corsivo o nessuna.
- **FontFamily** &ndash; imposta il tipo di carattere da utilizzare.
- **FontSize** &ndash; imposta la dimensione del testo.
- **ForegroundColor** &ndash; imposta il colore del testo.
- **Testo** &ndash; il testo da presentare.

Il codice c# seguente illustra un'etichetta in cui la prima parola è in grassetto e l'ultima parola è rosso:

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
    var label = new Label { FontSize = 20 };
    var s = new FormattedString ();
    s.Spans.Add (new Span{ Text = "Red Bold", FontAttributes = FontAttributes.Bold });
    s.Spans.Add (new Span{ Text = "Default" });
    s.Spans.Add (new Span{ Text = "italic small", FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)), FontAttributes = FontAttributes.Italic});
    label.FormattedText = s;
        layout.Children.Add(label);
    }
}
```

In XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.LabelPage"
Title="Label Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
      <Label FontSize=20>
        <Label.FormattedText>
          <FormattedString>
            <Span Text="Red Bold" ForegroundColor="Red" FontAttributes="Bold" />
            <Span Text="Default" />
            <Span Text="italic small" FontAttributes="Italic" FontSize="Small" />
          </FormattedString>
        </Label.FormattedText>
      </Label>
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

![](label-images/formattedtext.png "Esempio di etichetta FormattedText")


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di App per dispositivi mobili con xamarin. Forms, capitolo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [Testo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [API di etichetta](xref:Xamarin.Forms.Label)
