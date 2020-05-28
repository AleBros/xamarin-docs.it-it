---
title: Xamarin.FormsClassi di stile
description: Xamarin.Formsle classi di stile consentono di applicare più stili a un controllo senza ricorrere all'ereditarietà dello stile.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2afb06c2d97e6f15c2041b9c2e9cad092b13d90d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138762"
---
# <a name="xamarinforms-style-classes"></a>Xamarin.FormsClassi di stile

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Le classi di stile Novell. Forms consentono di applicare più stili a un controllo senza ricorrere all'ereditarietà dello stile._

## <a name="create-style-classes"></a>Creare classi di stile

È possibile creare una classe di stile impostando la [`Class`](xref:Xamarin.Forms.Style.Class) proprietà su un oggetto [`Style`](xref:Xamarin.Forms.Style) `string` che rappresenta il nome della classe. Il vantaggio offerto dalla definizione di uno stile esplicito con l' `x:Key` attributo consiste nel fatto che è possibile applicare più classi di stile a un oggetto [`VisualElement`](xref:Xamarin.Forms.VisualElement) .

> [!IMPORTANT]
> Più stili possono condividere lo stesso nome di classe, purché siano destinati a tipi diversi. In questo modo, più classi di stile, che hanno un nome identico, hanno come destinazione tipi diversi.

Nell'esempio seguente vengono illustrate tre [`BoxView`](xref:Xamarin.Forms.BoxView) classi di stile e una [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe di stile:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="BoxView"
               Class="Separator">
            <Setter Property="BackgroundColor"
                    Value="#CCCCCC" />
            <Setter Property="HeightRequest"
                    Value="1" />
        </Style>

        <Style TargetType="BoxView"
               Class="Rounded">
            <Setter Property="BackgroundColor"
                    Value="#1FAECE" />
            <Setter Property="HorizontalOptions"
                    Value="Start" />
            <Setter Property="CornerRadius"
                    Value="10" />
        </Style>    

        <Style TargetType="BoxView"
               Class="Circle">
            <Setter Property="BackgroundColor"
                    Value="#1FAECE" />
            <Setter Property="WidthRequest"
                    Value="100" />
            <Setter Property="HeightRequest"
                    Value="100" />
            <Setter Property="HorizontalOptions"
                    Value="Start" />
            <Setter Property="CornerRadius"
                    Value="50" />
        </Style>

        <Style TargetType="VisualElement"
               Class="Rotated"
               ApplyToDerivedTypes="true">
            <Setter Property="Rotation"
                    Value="45" />
        </Style>        
    </ContentPage.Resources>
</ContentPage>
```

Le `Separator` `Rounded` classi, e `Circle` di stile consentono di impostare le [`BoxView`](xref:Xamarin.Forms.BoxView) proprietà su valori specifici.

La `Rotated` classe Style è [`TargetType`](xref:Xamarin.Forms.Style.TargetType) di tipo [`VisualElement`](xref:Xamarin.Forms.VisualElement) , ovvero può essere applicata solo a istanze di `VisualElement` . Tuttavia, la [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) proprietà è impostata su `true` , che garantisce che possa essere applicata a tutti i controlli che derivano da `VisualElement` , ad esempio [`BoxView`](xref:Xamarin.Forms.BoxView) . Per ulteriori informazioni sull'applicazione di uno stile a un tipo derivato, vedere [applicare uno stile ai tipi derivati](implicit.md#apply-a-style-to-derived-types).

Il codice C# equivalente è il seguente:

```csharp
var separatorBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Separator",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#CCCCCC")
        },
        new Setter
        {
            Property = VisualElement.HeightRequestProperty,
            Value = 1
        }
    }
};

var roundedBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Rounded",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#1FAECE")
        },
        new Setter
        {
            Property = View.HorizontalOptionsProperty,
            Value = LayoutOptions.Start
        },
        new Setter
        {
            Property = BoxView.CornerRadiusProperty,
            Value = 10
        }
    }
};

var circleBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Circle",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#1FAECE")
        },
        new Setter
        {
            Property = VisualElement.WidthRequestProperty,
            Value = 100
        },
        new Setter
        {
            Property = VisualElement.HeightRequestProperty,
            Value = 100
        },
        new Setter
        {
            Property = View.HorizontalOptionsProperty,
            Value = LayoutOptions.Start
        },
        new Setter
        {
            Property = BoxView.CornerRadiusProperty,
            Value = 50
        }
    }
};

var rotatedVisualElementStyle = new Style(typeof(VisualElement))
{
    Class = "Rotated",
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.RotationProperty,
            Value = 45
        }
    }
};

Resources = new ResourceDictionary
{
    separatorBoxViewStyle,
    roundedBoxViewStyle,
    circleBoxViewStyle,
    rotatedVisualElementStyle
};
```

## <a name="consume-style-classes"></a>Utilizzare classi di stile

Le classi di stile possono essere utilizzate impostando la [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) proprietà del controllo, che è di tipo `IList<string>` , su un elenco di nomi di classi di stile. Verranno applicate le classi di stile, purché il tipo del controllo corrisponda alle [`TargetType`](xref:Xamarin.Forms.Style.TargetType) classi di stile.

Nell'esempio seguente vengono illustrate tre [`BoxView`](xref:Xamarin.Forms.BoxView) istanze, ciascuna delle quali è impostata su classi di stile diverse:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        ...
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <BoxView StyleClass="Separator" />       
        <BoxView WidthRequest="100"
                 HeightRequest="100"
                 HorizontalOptions="Center"
                 StyleClass="Rounded, Rotated" />
        <BoxView HorizontalOptions="Center"
                 StyleClass="Circle" />
    </StackLayout>
</ContentPage>    
```

In questo esempio, il primo [`BoxView`](xref:Xamarin.Forms.BoxView) viene applicato come separatore di riga, mentre il terzo `BoxView` è circolare. Al secondo `BoxView` sono state applicate due classi di stile, che forniscono angoli arrotondati e ne ruotano 45 gradi:

![BoxViews con stile classi](style-class-images/boxviews.png)

> [!IMPORTANT]
> È possibile applicare più classi di stile a un controllo perché la [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) proprietà è di tipo `IList<string>` . Quando si verifica questa situazione, le classi di stile vengono applicate nell'ordine crescente dell'elenco. Pertanto, quando più classi di stile impostano proprietà identiche, la proprietà nella classe di stile che si trova nella posizione di elenco più alta avrà la precedenza.

Il codice C# equivalente è il seguente:

```csharp
...
Content = new StackLayout
{
    Children =
    {
        new BoxView { StyleClass = new [] { "Separator" } },
        new BoxView { WidthRequest = 100, HeightRequest = 100, HorizontalOptions = LayoutOptions.Center, StyleClass = new [] { "Rounded", "Rotated" } },
        new BoxView { HorizontalOptions = LayoutOptions.Center, StyleClass = new [] { "Circle" } }
    }
};
```

## <a name="related-links"></a>Collegamenti correlati

- [Stili di base (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
