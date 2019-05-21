---
title: Classi di stile di xamarin. Forms
description: Le classi di stile di xamarin. Forms abilitano più stili da applicare a un controllo, senza dover ricorrere a ereditarietà degli stili.
ms.prod: xamarin
ms.assetid: 4762401E-2B48-48F1-B6E4-61F7AF8AA46F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: a3ef0f96bcc955dcac4231f9eb9cf1ab16ee61aa
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925284"
---
# <a name="xamarinforms-style-classes"></a>Classi di stile di xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_Le classi di stile di xamarin. Forms abilitano più stili da applicare a un controllo, senza dover ricorrere a ereditarietà degli stili._

## <a name="create-style-classes"></a>Creare le classi di stile

Una classe di stile può essere creata impostando il [ `Class` ](xref:Xamarin.Forms.Style.Class) proprietà in un [ `Style` ](xref:Xamarin.Forms.Style) da un `string` che rappresenta il nome della classe. Il vantaggio di questo offre, rispetto alla definizione di un stile esplicito tramite il `x:Key` dell'attributo, è che più classi di stile possono essere applicate a un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).

> [!IMPORTANT]
> Stili multipli possono condividere lo stesso nome di classe, purché destinati a diversi tipi. In questo modo più classi di stile, che sono denominate in modo identico, a diversi tipi di destinazione.

L'esempio seguente mostra tre [ `BoxView` ](xref:Xamarin.Forms.BoxView) applicare uno stile a classi e un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) applicare uno stile di classe:

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

Il `Separator`, `Rounded`, e `Circle` ogni set di classi di stile [ `BoxView` ](xref:Xamarin.Forms.BoxView) specifici valori delle proprietà.

Il `Rotated` classe di stile di visualizzazione dispone di un [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) dei [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), vale a dire che può essere applicato solo a `VisualElement` istanze. Tuttavia, relativi [ `ApplyToDerivedTypes` ](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) è impostata su `true`, che assicura che può essere applicato a tutti i controlli che derivano da `VisualElement`, ad esempio [ `BoxView` ](xref:Xamarin.Forms.BoxView). Per altre informazioni sull'applicazione di uno stile a un tipo derivato, vedere [applicare uno stile ai tipi derivati](implicit.md#apply-a-style-to-derived-types).

Il codice C# equivalente è:

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

## <a name="consume-style-classes"></a>Utilizzare le classi di stile

Le classi di stile possono essere utilizzate impostando il [ `StyleClass` ](xref:Xamarin.Forms.NavigableElement.StyleClass) proprietà del controllo, che è di tipo `IList<string>`, per un elenco di nomi di classe di stile. Verranno applicate le classi di stile, condizione che corrisponde al tipo di controllo di [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) delle classi di stile.

L'esempio seguente mostra tre [ `BoxView` ](xref:Xamarin.Forms.BoxView) istanze, entrambi impostati su classi di stile diverso:

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

In questo esempio, il primo [ `BoxView` ](xref:Xamarin.Forms.BoxView) è concepito per essere un separatore di riga, mentre il terzo `BoxView` è circolare. Il secondo `BoxView` dispongono di due classi di stile applicati, che offrono gli angoli arrotondati it e ruotare di 45 gradi:

![](style-class-images/boxviews.png "BoxViews uno stile definito con le classi di stile")

> [!IMPORTANT]
> Più classi di stile possono essere applicate a un controllo perché il [ `StyleClass` ](xref:Xamarin.Forms.NavigableElement.StyleClass) proprietà è di tipo `IList<string>`. In questo caso, le classi di stile vengono applicate in modo crescente elenco. Pertanto, quando più classi di stile impostano proprietà identiche, la proprietà nella classe di stile che si trova nella posizione più alta elenco avrà la precedenza.

Il codice C# equivalente è:

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

- [Stili di base (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
