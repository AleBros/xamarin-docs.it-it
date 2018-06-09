---
title: Formattazione di stringhe di xamarin. Forms
description: In questo articolo viene illustrato come utilizzare le associazioni dati xamarin. Forms per formattare e visualizzare gli oggetti come stringhe. Questo risultato si ottiene impostando StringFormat dell'associazione a una stringa di formattazione .NET standard con un segnaposto.
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: bdac74e4ec14797ec373f86b8a94c7af4d480951
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240301"
---
# <a name="xamarinforms-string-formatting"></a>Formattazione di stringhe di xamarin. Forms

In alcuni casi è consigliabile usare le associazioni dati per visualizzare la rappresentazione di stringa di un oggetto o un valore. Potrebbe ad esempio, si desidera utilizzare un `Label` per visualizzare il valore corrente di un `Slider`. In questa associazione di dati, il `Slider` è l'origine e la destinazione è il `Text` proprietà del `Label`.

Quando si visualizzano le stringhe nel codice, lo strumento più potente è statica [ `String.Format` ](https://developer.xamarin.com/api/member/System.String.Format/p/System.String/System.Object/) metodo. È possibile includere altri testo insieme ai valori da formattare la stringa di formattazione include codici specifici per diversi tipi di oggetti di formattazione. Vedere il [formattazione dei tipi in .NET](/dotnet/standard/base-types/formatting-types/) articolo per ulteriori informazioni sulla formattazione delle stringhe.

## <a name="the-stringformat-property"></a>La proprietà StringFormat

Questa funzionalità viene trasferita in associazioni di dati: impostare il [ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.StringFormat/) proprietà di `Binding` (o [ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.StringFormat/) proprietà del `Binding` estensione di markup) a un .NET standard stringa con un segnaposto di formattazione:

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

Si noti che la stringa di formattazione è delimitata da caratteri: virgoletta singola (apostrofo) per evitare il parser XAML trattare le parentesi graffe come un'altra estensione di markup XAML. In caso contrario, stringa che senza il carattere virgoletta singola è la stessa stringa consente di visualizzare un valore a virgola mobile in una chiamata a `String.Format`. La formattazione specifica di `F2` , il valore viene visualizzato con due cifre decimali.

Il `StringFormat` proprietà ha senso solo quando la proprietà di destinazione è di tipo `string`, e la modalità di associazione è `OneWay` o `TwoWay`. Per le associazioni bidirezionali, di `StringFormat` è applicabile solo per valori passando dall'origine alla destinazione.

Come si vedrà l'articolo successivo nel [percorso di associazione](binding-path.md), data binding possono diventare piuttosto complessi e complessa. Durante il debug di queste associazioni di dati, è possibile aggiungere un `Label` nel file XAML con un `StringFormat` per visualizzare alcuni risultati intermedi. Anche se utilizzarlo solo per visualizzare un tipo di oggetto, che può essere utile.

Il **la formattazione della stringa** pagina illustrati diversi utilizzi del `StringFormat` proprietà:

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

Le associazioni sul `Slider` e `TimePicker` mostrano l'uso di specifiche di formato specifiche `double` e `TimeSpan` tipi di dati. Il `StringFormat` che visualizza il testo dal `Entry` vista viene illustrato come specificare tra virgolette nella stringa di formattazione con l'utilizzo del `&quot;` entità HTML.

La sezione successiva nel file XAML è un `StackLayout` con un `BindingContext` impostato su un `x:Static` estensione di markup che fa riferimento al metodo statico `DateTime.Now` proprietà. La prima associazione non dispone di proprietà:

```xaml
<Label Text="{Binding}" />
```

Verrà visualizzata semplicemente il `DateTime` valore del `BindingContext` con formattazione predefinita. La seconda associazione consente di visualizzare il `Ticks` proprietà di `DateTime`, mentre le altre due associazioni visualizzare il `DateTime` con formattazione specifica. Osservare questo `StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

Se è necessario visualizzare a sinistra o parentesi graffe nella stringa di formattazione, utilizzare una coppia di essi.

L'ultimo gruppo di sezione di `BindingContext` al valore di `Math.PI` e lo visualizza con formattazione predefinita e due diversi tipi di formattazione numerica.

Di seguito è riportato il programma in esecuzione in tutti e tre le piattaforme:

[![Stringa di formattazione](string-formatting-images/stringformatting-small.png "stringa di formattazione")](string-formatting-images/stringformatting-large.png#lightbox "la formattazione di stringhe")

## <a name="viewmodels-and-string-formatting"></a>ViewModel e formattazione di stringhe

Quando si utilizza `Label` e `StringFormat` per visualizzare il valore di una vista che è anche la destinazione di un elemento ViewModel, è possibile definire l'associazione dalla vista per la `Label` o dal ViewModel per il `Label`. In generale, il secondo approccio è consigliabile perché verifica che le associazioni tra la visualizzazione e ViewModel funzionino.

Questo approccio viene illustrato nel **colori migliori** un esempio che utilizza la stessa ViewModel come il **semplice selettore del colore** programma illustrato nel [ **Binding Mode** ](binding-mode.md) articolo:

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

Esistono tre coppie di `Slider` e `Label` negli elementi associati alla stessa proprietà di origine di `HslColorViewModel` oggetto. L'unica differenza è che `Label` ha un `StringFormat` proprietà per visualizzare ogni `Slider` valore.

[![Selettore di colore meglio](string-formatting-images/bettercolorselector-small.png "selettore di colore meglio")](string-formatting-images/bettercolorselector-large.png#lightbox "meglio selettore di colore")

È possibile chiedersi come è possibile visualizzare i valori RGB (rossi, verdi e blu) in formato esadecimale a due cifre tradizionali. Tali valori integer non sono disponibili direttamente il `Color` struttura. Una soluzione, è possibile calcolare valori integer con i componenti di colore all'interno di ViewModel ed esporli come proprietà. È possibile quindi formattare utilizzando il `X2` formattazione specifica.

Un altro approccio è più generale: È possibile scrivere un *convertitore di valori di associazione* come descritto nell'articolo successive [ **convertitori di valori di associazione**](converters.md).

L'articolo successivo, tuttavia, viene esaminata la [ **percorso di associazione** ](binding-path.md) più in dettaglio e Mostra come è possibile utilizzare per fare riferimento a sottoproprietà e gli elementi nelle raccolte.


## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capitolo di associazione di dati dalla Rubrica di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
