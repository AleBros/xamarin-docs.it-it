---
title: Stili espliciti
description: "Uno stile esplicito è quello che viene applicato in modo selettivo a controlli impostando le proprietà di stile."
ms.topic: article
ms.prod: xamarin
ms.assetid: C0DF9F8F-B431-4374-A574-325BC3C41A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 1fbc12288527c053a24041aa6c49cc1a4abdde55
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="explicit-styles"></a>Stili espliciti

_Uno stile esplicito è quello che viene applicato in modo selettivo a controlli impostando le proprietà di stile._

## <a name="creating-an-explicit-style-in-xaml"></a>Creazione di uno stile esplicito in XAML

Per dichiarare un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) a livello di pagina, un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) deve essere aggiunto per la pagina e quindi uno o più `Style` dichiarazioni possono essere inclusi nel `ResourceDictionary`. A `Style` è *esplicita* , assegnando la dichiarazione di un `x:Key` attributo, che fornisce una chiave descrittiva nel `ResourceDictionary`. *Esplicita* stili devono quindi essere applicati per specifici elementi visivi impostando i relativi [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà.

Nell'esempio di codice riportato di seguito viene *esplicita* gli stili dichiarati in XAML in una pagina `ResourceDictionary` e applicata alla pagina [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) istanze:

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

Il [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) definisce tre *esplicita* gli stili applicati alla pagina [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) istanze. Ogni `Style` viene utilizzato per visualizzare il testo in un colore diverso, durante l'impostazione anche il tipo di carattere delle opzioni di layout di dimensioni e orizzontali e verticali. Ogni `Style` viene applicato a un altro `Label` impostando il relativo [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà utilizzando il `StaticResource` estensione di markup. Ciò comporta l'aspetto illustrato nelle schermate seguenti:

[![](explicit-images/explicit-styles.png "Esempio stili esplicita")](explicit-images/explicit-styles-large.png#lightbox "esempio stili esplicita")

Inoltre finale [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) ha un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) applicato, ma esegue inoltre l'override di [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) proprietà da un diverso `Color`valore.

### <a name="creating-an-explicit-style-at-the-control-level"></a>Creazione di uno stile esplicito il controllo livello

Oltre a creare *esplicita* stili a livello di pagina, possono anche essere creati a livello di controllo, come illustrato nell'esempio di codice seguente:

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

In questo esempio, il *esplicita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) istanze vengono assegnate al [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) insieme il [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) controllo. Gli stili possono essere applicati per il controllo e i relativi elementi figlio.

Per informazioni sulla creazione di stili in un'applicazione [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), vedere [stili globali](~/xamarin-forms/user-interface/styles/application.md).

## <a name="creating-an-explicit-style-in-c35"></a>Creazione di uno stile esplicito in C&#35;

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) le istanze possono essere aggiunte a una pagina [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) raccolta in c# tramite la creazione di un nuovo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)e quindi aggiungendo il `Style` istanze per il `ResourceDictionary`, come illustrato di esempio di codice seguente:

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red  }
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
                new Label { Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

Il costruttore definisce tre *esplicita* gli stili applicati alla pagina [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) istanze. Ogni *esplicita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) viene aggiunto al [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) utilizzando il [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ResourceDictionary.Add/p/System.String/System.Object/) metodo, specificando un `key` stringa per fare riferimento al `Style` istanza. Ogni `Style` viene applicato a un altro `Label` impostando i relativi [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà.

Tuttavia, non comporta alcun vantaggio è un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) qui. Al contrario, [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) istanze possono essere assegnate direttamente al [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà degli elementi di visual obbligatori e `ResourceDictionary` possono essere rimossi, come illustrato nell'esempio seguente esempio di codice:

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

Il costruttore definisce tre *esplicita* gli stili applicati alla pagina [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) istanze. Ogni `Style` viene utilizzato per visualizzare il testo in un colore diverso, durante l'impostazione anche il tipo di carattere delle opzioni di layout di dimensioni e orizzontali e verticali. Ogni `Style` viene applicato a un altro `Label` impostando il relativo [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà. Inoltre, gli ultimi `Label` ha un `Style` applicato, ma esegue inoltre l'override di `TextColor` proprietà a un altro `Color` valore.

## <a name="summary"></a>Riepilogo

A [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) viene effettuata *esplicita* , assegnando la dichiarazione di un `x:Key` attributo e quindi in modo selettivo applicarlo ai controlli impostando i relativi [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà.



## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili di base (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Utilizzo degli stili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Stile](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Metodo set](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
