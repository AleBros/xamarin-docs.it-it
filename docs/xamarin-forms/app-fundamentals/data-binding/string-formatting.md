---
title: Formattazione delle stringhe di xamarin. Forms
description: Questo articolo illustra come usare le associazioni di dati di xamarin. Forms per formattare e visualizzare gli oggetti come stringhe. Questo risultato viene ottenuto impostando StringFormat dell'associazione a una stringa di formattazione .NET standard con un segnaposto.
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 8efd93204b848113e0ed95c8066a5506eb517ac6
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52170949"
---
# <a name="xamarinforms-string-formatting"></a>Formattazione delle stringhe di xamarin. Forms

In alcuni casi è consigliabile usare le associazioni dati per visualizzare la rappresentazione di stringa di un oggetto o valore. Ad esempio, voler utilizzare un `Label` per visualizzare il valore corrente di un `Slider`. In questa associazione di dati, il `Slider` è l'origine e la destinazione è il `Text` proprietà del `Label`.

Quando si visualizzano le stringhe nel codice, lo strumento più efficace è il metodo statico [ `String.Format` ](xref:System.String.Format(System.String,System.Object)) (metodo). La stringa di formattazione include codici specifici per vari tipi di oggetti di formattazione ed è possibile includere altri testo con i valori in corso di formattazione. Vedere le [formattazione di tipi in .NET](/dotnet/standard/base-types/formatting-types/) articolo per ulteriori informazioni sulla formattazione della stringa.

## <a name="the-stringformat-property"></a>La proprietà StringFormat

Questa funzionalità viene trasferita al data binding: È impostato il [ `StringFormat` ](xref:Xamarin.Forms.BindingBase.StringFormat) proprietà della `Binding` (o il [ `StringFormat` ](xref:Xamarin.Forms.Xaml.BindingExtension.StringFormat) proprietà del `Binding` estensione di markup) a un stringa con un segnaposto di formattazione .NET standard:

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

Si noti che la stringa di formattazione è delimitata da caratteri di virgoletta singola (apostrofo) per evitare che il parser XAML considerando le parentesi graffe come un'altra estensione di markup XAML. In caso contrario, la stringa senza il carattere di virgoletta singola è la stessa stringa si utilizzerebbe per visualizzare un valore a virgola mobile in una chiamata a `String.Format`. Una formattazione specifica delle `F2` fa sì che il valore deve essere visualizzato con due cifre decimali.

Il `StringFormat` proprietà risulta utile solo quando la proprietà di destinazione è di tipo `string`, e la modalità di associazione `OneWay` o `TwoWay`. Per le associazioni bidirezionali, di `StringFormat` è applicabile solo per i valori passando dall'origine alla destinazione.

Come si vedrà nel prossimo articolo nella [percorso di associazione](binding-path.md), associazioni dati possono diventare piuttosto complessi e complessa. Durante il debug di queste associazioni di dati, è possibile aggiungere un `Label` nel file XAML con un `StringFormat` per visualizzare alcuni risultati intermedi. Anche se utilizzarlo solo per visualizzare un tipo di oggetto, che può essere utile.

Il **formattazione delle stringhe** pagina vengono illustrati diversi utilizzi del `StringFormat` proprietà:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
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

Le associazioni sul `Slider` e `TimePicker` mostrano l'uso di specifiche di formato specifico per `double` e `TimeSpan` i tipi di dati. Il `StringFormat` che visualizza il testo dal `Entry` visualizzazione viene illustrato come specificare tra virgolette nella stringa di formattazione con l'uso del `&quot;` entità HTML.

La sezione successiva nel file XAML è un `StackLayout` con un `BindingContext` impostata su un `x:Static` estensione di markup che fa riferimento al metodo statico `DateTime.Now` proprietà. Prima associazione non dispone di proprietà:

```xaml
<Label Text="{Binding}" />
```

Verrà visualizzata semplicemente la `DateTime` pari al `BindingContext` con formattazione predefinita. La seconda associazione consente di visualizzare il `Ticks` proprietà di `DateTime`, mentre le altre due associazioni visualizzare il `DateTime` con formattazione specifica. In questo caso `StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

Se è necessario per la visualizzazione verso sinistra o parentesi graffe nella stringa di formattazione, è sufficiente usare una coppia di essi.

L'ultimo gruppo sezione la `BindingContext` al valore di `Math.PI` e li visualizza con formattazione predefinita e due tipi diversi di formattazione numerica.

Ecco il programma in esecuzione:

[![Formattazione delle stringhe](string-formatting-images/stringformatting-small.png "formattazione delle stringhe")](string-formatting-images/stringformatting-large.png#lightbox "formattazione delle stringhe")

## <a name="viewmodels-and-string-formatting"></a>ViewModel e la formattazione delle stringhe

Quando si usa `Label` e `StringFormat` per visualizzare il valore di una visualizzazione che rappresenta anche la destinazione di un elemento ViewModel, è possibile definire l'associazione dalla vista per il `Label` o da ViewModel al `Label`. In generale, il secondo approccio è migliore perché verifica che le associazioni tra View e ViewModel funzionino.

Questo approccio è illustrato nella **selettore di colore meglio** un esempio che usa l'elemento ViewModel stesso come il **selettore colori semplice** programma illustrato nel [ **Binding Mode** ](binding-mode.md) articolo:

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

Sono ora disponibili tre coppie di `Slider` e `Label` gli elementi associati alla stessa proprietà in origine la `HslColorViewModel` oggetto. L'unica differenza è che `Label` ha un `StringFormat` proprietà per visualizzare ogni `Slider` valore.

[![Selettore di colore meglio](string-formatting-images/bettercolorselector-small.png "selettore di colore meglio")](string-formatting-images/bettercolorselector-large.png#lightbox "selettore di colore meglio")

È lecito chiedersi come si potrebbero visualizzare valori RGB (rossi, verdi, blu) in formato esadecimale a due cifre tradizionali. Questi valori integer non sono disponibili direttamente il `Color` struttura. Una soluzione sarebbe per calcolare valori integer con i componenti di colore entro l'elemento ViewModel e li espone come proprietà. È possibile quindi formattarle utilizzando il `X2` formattazione specifica.

Un altro approccio è più generale: È possibile scrivere un *convertitore di valori di associazione* come illustrato nell'articolo successivo [ **convertitori di valori di associazione**](converters.md).

Il prossimo articolo, tuttavia, illustra le [ **percorso di associazione** ](binding-path.md) più in dettaglio e Mostra come è possibile usarlo per fare riferimento a sottoproprietà e gli elementi nelle raccolte.


## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capitolo di associazione di dati dal libro di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
