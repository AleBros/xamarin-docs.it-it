---
title: Stili espliciti in xamarin. Forms
description: Uno stile esplicito è quello che viene applicato in modo selettivo a controlli impostando le relative proprietà di stile. Questo articolo illustra come usare stili espliciti in un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: C0DF9F8F-B431-4374-A574-325BC3C41A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 5b3d6f486f802fda1a384b7a8e53b104c1251fdd
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058246"
---
# <a name="explicit-styles-in-xamarinforms"></a>Stili espliciti in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_Uno stile esplicito è quello che viene applicato in modo selettivo a controlli impostando le relative proprietà di stile._

## <a name="creating-an-explicit-style-in-xaml"></a>Creazione di uno stile esplicito in XAML

Per dichiarare un [ `Style` ](xref:Xamarin.Forms.Style) a livello di pagina, un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) deve essere aggiunto alla pagina e quindi uno o più `Style` le dichiarazioni possono essere inclusi nel `ResourceDictionary`. Oggetto `Style` viene effettuata *esplicita* assegnando la dichiarazione di un `x:Key` attributo, che assegna una chiave descrittiva nel `ResourceDictionary`. *Explicit* stili devono quindi essere applicati per specifici elementi visivi impostando loro [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) proprietà.

Il codice seguente esempio illustra *esplicite* stili dichiarato in XAML in una pagina `ResourceDictionary` e applicate alla pagina [ `Label` ](xref:Xamarin.Forms.Label) istanze:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="labelRedStyle" TargetType="Label">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
            <Style x:Key="labelGreenStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Green" />
            </Style>
            <Style x:Key="labelBlueStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelRedStyle}" />
            <Label Text="are demonstrating"
                   Style="{StaticResource labelGreenStyle}" />
            <Label Text="explicit styles,"
                   Style="{StaticResource labelBlueStyle}" />
            <Label Text="and an explicit style override"
                   Style="{StaticResource labelBlueStyle}"
                   TextColor="Teal" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Il [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) definisce tre *esplicita* stili applicati alla pagina [ `Label` ](xref:Xamarin.Forms.Label) istanze. Ogni `Style` consente di visualizzare il testo in un colore diverso, impostando allo stesso tempo il tipo di carattere opzioni di layout, dimensioni e orizzontali e verticali. Ogni `Style` viene applicato a un altro `Label` mediante l'impostazione relativa [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) proprietà mediante il `StaticResource` estensione di markup. Ciò comporta l'aspetto illustrato negli screenshot seguenti:

[![](explicit-images/explicit-styles.png "Esempio di stili espliciti")](explicit-images/explicit-styles-large.png#lightbox "esempio stili espliciti")

Inoltre, l'elemento finale [ `Label` ](xref:Xamarin.Forms.Label) ha un [ `Style` ](xref:Xamarin.Forms.Style) applicati, ma esegue anche l'override la [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) proprietà a un diverso `Color`valore.

### <a name="creating-an-explicit-style-at-the-control-level"></a>Creazione di uno stile esplicito il controllo a livello

Oltre alla creazione *esplicita* stili a livello di pagina, possono anche essere creati a livello di controllo, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelRedStyle" TargetType="Label">
                      ...
                    </Style>
                    ...
                </ResourceDictionary>
            </StackLayout.Resources>
            <Label Text="These labels" Style="{StaticResource labelRedStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

In questo esempio, il *esplicita* [ `Style` ](xref:Xamarin.Forms.Style) le istanze sono assegnate per i [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) raccolta del [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) controllo. Gli stili possono quindi essere applicati al controllo e i relativi elementi figlio.

Per informazioni sulla creazione di stili in un'applicazione [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), vedere [stili globali](~/xamarin-forms/user-interface/styles/application.md).

## <a name="creating-an-explicit-style-in-c35"></a>Creazione di uno stile esplicito in C&#35;

[`Style`](xref:Xamarin.Forms.Style) le istanze possono essere aggiunte a una pagina [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) raccolta in c# mediante la creazione di un nuovo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e quindi aggiungendo il `Style` alle istanze il `ResourceDictionary`, come illustrato di esempio di codice seguente:

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red    }
            }
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Green }
            }
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Blue }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("labelRedStyle", labelRedStyle);
        Resources.Add ("labelGreenStyle", labelGreenStyle);
        Resources.Add ("labelBlueStyle", labelBlueStyle);
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels",
                            Style = (Style)Resources ["labelRedStyle"] },
                new Label { Text = "are demonstrating",
                            Style = (Style)Resources ["labelGreenStyle"] },
                new Label { Text = "explicit styles,",
                            Style = (Style)Resources ["labelBlueStyle"] },
                new Label {    Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

Il costruttore definisce tre *esplicite* stili applicati alla pagina [ `Label` ](xref:Xamarin.Forms.Label) istanze. Ogni *esplicite* [ `Style` ](xref:Xamarin.Forms.Style) viene aggiunto al [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) usando la [ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) metodo, specificando un `key` stringa per fare riferimento al `Style` istanza. Ciascuna `Style` viene applicato a un altro `Label` impostando loro [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) proprietà.

Tuttavia, non offre alcun vantaggio all'utilizzo di un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) qui. Al contrario, [ `Style` ](xref:Xamarin.Forms.Style) istanze possono essere assegnate direttamente al [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) le proprietà degli oggetti visivi necessari e il `ResourceDictionary` può essere rimosso, come illustrato nell'esempio seguente esempio di codice:

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            ...
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            ...
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            ...
        };
        ...
        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelRedStyle },
                new Label { Text = "are demonstrating", Style = labelGreenStyle },
                new Label { Text = "explicit styles,", Style = labelBlueStyle },
                new Label { Text = "and an explicit style override", Style = labelBlueStyle,
                            TextColor = Color.Teal }
            }
        };
    }
}
```

Il costruttore definisce tre *esplicite* stili applicati alla pagina [ `Label` ](xref:Xamarin.Forms.Label) istanze. Ogni `Style` consente di visualizzare il testo in un colore diverso, impostando allo stesso tempo il tipo di carattere opzioni di layout, dimensioni e orizzontali e verticali. Ciascun `Style` viene applicato a un altro `Label` mediante l'impostazione relativa [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) proprietà. Inoltre, l'elemento finale `Label` ha un `Style` applicato, ma esegue anche l'override la `TextColor` proprietà a un altro `Color` valore.

## <a name="summary"></a>Riepilogo

Un [ `Style` ](xref:Xamarin.Forms.Style) viene effettuata *esplicita* assegnando la dichiarazione di un `x:Key` attributo e quindi in modo selettivo applicarla ai controlli impostando loro [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) proprietà.



## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili di base (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Utilizzo di stili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Stile di visualizzazione](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
