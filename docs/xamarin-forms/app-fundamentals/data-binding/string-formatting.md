---
title: Xamarin.FormsFormattazione di stringhe
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d08a89d5394a227e3cee76603e4e97d2cf9495e0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84135759"
---
# <a name="xamarinforms-string-formatting"></a>Xamarin.FormsFormattazione di stringhe

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

In alcuni casi è utile usare i data binding per visualizzare la rappresentazione di un oggetto o di un valore sotto forma di stringa. Ad esempio, si può usare `Label` per visualizzare il valore corrente di un elemento `Slider`. In questo data binding, `Slider` rappresenta l'origine, mentre la destinazione è la proprietà `Text` di `Label`.

Quando si visualizzano stringhe nel codice, lo strumento più potente è il [`String.Format`](xref:System.String.Format(System.String,System.Object)) metodo statico. La stringa di formattazione include codici di formattazione specifici per i vari tipi di oggetti e ai valori da formattare è possibile aggiungere altro testo. Per altre informazioni sulla formattazione delle stringhe, vedere [Formattazione di tipi in .NET](/dotnet/standard/base-types/formatting-types/).

## <a name="the-stringformat-property"></a>La proprietà StringFormat

Questa funzionalità viene trasferita in data bindings: la [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) proprietà di `Binding` (o la [`StringFormat`](xref:Xamarin.Forms.Xaml.BindingExtension.StringFormat) proprietà dell' `Binding` estensione di markup) viene impostata su una stringa di formattazione .NET standard con un segnaposto:

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

Si noti che la stringa di formattazione è delimitata da virgolette singole (apostrofi) per evitare che il parser XAML consideri le parentesi graffe come un'altra estensione di markup XAML. In caso contrario, la stringa senza le virgoletta singole corrisponderebbe alla stringa utilizzata per visualizzare un valore a virgola mobile in una chiamata a `String.Format`. Una specifica di formattazione `F2` fa sì che il valore venga visualizzato con due cifre decimali.

La proprietà `StringFormat` risulta utile solo quando la proprietà di destinazione è di tipo `string`, e la modalità di binding è `OneWay` o `TwoWay`. Per i data binding bidirezionali, `StringFormat` è applicabile solo per i valori che passano dall'origine alla destinazione.

Come si vedrà nel prossimo articolo sul [Percorso di binding](binding-path.md), i data binding possono diventare piuttosto complessi e intricati. Durante il debug di questi data binding, è possibile aggiungere nel file XAML un elemento `Label` con `StringFormat` per visualizzare alcuni risultati intermedi. Anche se utilizzato solo per visualizzare il tipo di un oggetto, può essere utile.

La pagina di **formattazione delle stringhe** illustra diversi utilizzi della proprietà `StringFormat`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             x:Class="DataBindingDemos.StringFormattingPage"
             Title="String Formatting">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Blue" />
                <Setter Property="HeightRequest" Value="2" />
                <Setter Property="Margin" Value="0, 5" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <Slider x:Name="slider" />
        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The slider value is {0:F2}'}" />

        <BoxView />

        <TimePicker x:Name="timePicker" />
        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time,
                              StringFormat='The TimeSpan is {0:c}'}" />

        <BoxView />

        <Entry x:Name="entry" />
        <Label Text="{Binding Source={x:Reference entry},
                              Path=Text,
                              StringFormat='The Entry text is &quot;{0}&quot;'}" />

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:DateTime.Now}">
            <Label Text="{Binding}" />
            <Label Text="{Binding Path=Ticks,
                                  StringFormat='{0:N0} ticks since 1/1/1'}" />
            <Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
            <Label Text="{Binding StringFormat='The long date is {0:D}'}" />
        </StackLayout>

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:Math.PI}">
            <Label Text="{Binding}" />
            <Label Text="{Binding StringFormat='PI to 4 decimal points = {0:F4}'}" />
            <Label Text="{Binding StringFormat='PI in scientific notation = {0:E7}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

I data binding su `Slider` e `TimePicker` mostrano l'uso di specifiche di formato speciali per i tipi di dati `double` e `TimeSpan`. L'elemento `StringFormat` che visualizza il testo dalla vista `Entry` illustra come specificare virgolette doppie nella stringa di formattazione usando l'entità HTML `&quot;`.

La sezione successiva del file XAML contiene un elemento `StackLayout` con un `BindingContext` impostato su un'estensione di markup `x:Static` che fa riferimento alla proprietà statica `DateTime.Now`. Il primo data binding non ha proprietà:

```xaml
<Label Text="{Binding}" />
```

Verrà semplicemente visualizzato il valore `DateTime` di `BindingContext` con formattazione predefinita. Il secondo data binding visualizza la proprietà `Ticks` di `DateTime`, mentre gli altri due visualizzano `DateTime` con formattazione specifica. Si noti questo `StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

Per visualizzare parentesi graffe di apertura o chiusura nella stringa di formattazione, usarne una coppia.

Quest'ultima sezione imposta `BindingContext` sul valore di `Math.PI` e lo visualizza con la formattazione predefinita e due tipi diversi di formattazione numerica.

Ecco il programma in esecuzione:

[![Formattazione delle stringhe](string-formatting-images/stringformatting-small.png "Formattazione delle stringhe")](string-formatting-images/stringformatting-large.png#lightbox "Formattazione delle stringhe")

## <a name="viewmodels-and-string-formatting"></a>ViewModel e formattazione delle stringhe

Quando si usa `Label` e `StringFormat` per visualizzare il valore di una vista che rappresenta anche la destinazione di un elemento ViewModel, è possibile definire l'associazione dalla vista a `Label` oppure da ViewModel a `Label`. In generale, il secondo approccio è migliore perché verifica che le associazioni tra View e ViewModel funzionino.

Questo approccio è illustrato nell'esempio **Better Color Selector** che usa lo stesso elemento ViewModel del programma **Simple Color Selector** illustrato nell'articolo [**Binding Mode**](binding-mode.md) (Modalità di binding):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.BetterColorSelectorPage"
             Title="Better Color Selector">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

Sono ora disponibili tre coppie di elementi `Slider` e `Label` associate alla stessa proprietà di origine nell'oggetto `HslColorViewModel`. L'unica differenza è che per `Label` è presente una proprietà `StringFormat` per visualizzare ogni valore `Slider`.

[![Selettore colori ottimizzato](string-formatting-images/bettercolorselector-small.png "Selettore colori ottimizzato")](string-formatting-images/bettercolorselector-large.png#lightbox "Selettore colori ottimizzato")

È lecito chiedersi come si possono visualizzare valori RGB (rosso, verde, blu) nel tradizionale formato esadecimale a due cifre. Questi valori interi non sono disponibili direttamente nella struttura `Color`. Una soluzione sarebbe calcolare i valori interi dei componenti dei colori all'interno dell'elemento ViewModel ed esporli come proprietà. A questo punto, si potrebbe formattarli utilizzando la specifica di formattazione `X2`.

Un'alternativa consiste nell'adottare un approccio più generale: scrivere un *convertitore dei valori di associazione* come illustrato nell'articolo [**Binding Value Converters**](converters.md) (Convertitori dei valori di associazione).

L'articolo seguente, tuttavia, illustra il [**Percorso di binding**](binding-path.md) in dettaglio e mostra come usarlo creare riferimenti a sottoproprietà ed elementi nelle raccolte.

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demo di data binding)
- [Capitolo di data binding da Xamarin.Forms book](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
