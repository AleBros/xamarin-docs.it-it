---
title: Stili impliciti in xamarin. Forms
description: Uno stile implicito è quello che viene utilizzato da tutti i controlli di TargetType stesso, senza la necessità di ciascun controllo per fare riferimento allo stile.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 9444d6d8d743fb63496de31e23291b24d9d09993
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245396"
---
# <a name="implicit-styles-in-xamarinforms"></a>Stili impliciti in xamarin. Forms

_Uno stile implicito è quello che viene utilizzato da tutti i controlli di TargetType stesso, senza la necessità di ciascun controllo per fare riferimento allo stile._

## <a name="creating-an-implicit-style-in-xaml"></a>Creazione di uno stile implicito in XAML

Per dichiarare un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) a livello di pagina, un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) deve essere aggiunto per la pagina e quindi uno o più `Style` dichiarazioni possono essere inclusi nel `ResourceDictionary`. A `Style` diventa *implicita* non specificando un `x:Key` attributo. Verrà applicato lo stile per gli elementi visivi che soddisfano il `TargetType` esattamente, ma non per gli elementi che derivano dal `TargetType` valore.

Nell'esempio di codice riportato di seguito viene illustrato un *implicita* stile dichiarato in XAML in una pagina `ResourceDictionary`e applicate alla pagina [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) istanze:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Entry">
                <Setter Property="HorizontalOptions" Value="Fill" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BackgroundColor" Value="Yellow" />
                <Setter Property="FontAttributes" Value="Italic" />
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Entry Text="These entries" />
            <Entry Text="are demonstrating" />
            <Entry Text="implicit styles," />
            <Entry Text="and an implicit style override" BackgroundColor="Lime" TextColor="Red" />
            <local:CustomEntry Text="Subclassed Entry is not receiving the style" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Il [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) definisce una singola *implicita* stile applicato alla pagina [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) istanze. Il `Style` viene utilizzato per visualizzare testo blu su uno sfondo giallo, durante l'impostazione anche altre opzioni di visualizzazione. Il `Style` viene aggiunto alla pagina [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) senza specificare un `x:Key` attributo. Pertanto, il `Style` viene applicata a tutti il `Entry` istanze in modo implicito come corrispondano il [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) proprietà del `Style` esattamente. Tuttavia, il `Style` non viene applicato al `CustomEntry` istanza, che è una sottoclasse `Entry`. Ciò comporta l'aspetto illustrato nelle schermate seguenti:

[![](implicit-images/implicit-styles.png "Esempio degli stili impliciti")](implicit-images/implicit-styles-large.png#lightbox "esempio degli stili impliciti")

Inoltre, il quarto [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) esegue l'override di [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/) e [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/) proprietà dello stile implicito da diversi `Color`valori.

### <a name="creating-an-implicit-style-at-the-control-level"></a>Creazione di uno stile implicito il controllo livello

Oltre a creare *implicita* stili a livello di pagina, possono anche essere creati a livello di controllo, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style TargetType="Entry">
                        <Setter Property="HorizontalOptions" Value="Fill" />
                        ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Entry Text="These entries" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

In questo esempio, il *implicita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) viene assegnato al [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) insieme il [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)controllo. Il *implicita* stile può essere applicato per il controllo e i relativi elementi figlio.

Per informazioni sulla creazione di stili in un'applicazione [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), vedere [stili globali](~/xamarin-forms/user-interface/styles/application.md).

## <a name="creating-an-implicit-style-in-c35"></a>Creazione di uno stile implicito in C&#35;

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) le istanze possono essere aggiunte a una pagina [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) raccolta in c# tramite la creazione di un nuovo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)e quindi aggiungendo il `Style` istanze per il `ResourceDictionary`, come illustrato di esempio di codice seguente:

```csharp
public class ImplicitStylesPageCS : ContentPage
{
    public ImplicitStylesPageCS ()
    {
        var entryStyle = new Style (typeof(Entry)) {
            Setters = {
                ...
                new Setter { Property = Entry.TextColorProperty, Value = Color.Blue }
            }
        };

        ...
        Resources = new ResourceDictionary ();
        Resources.Add (entryStyle);

        Content = new StackLayout {
            Children = {
                new Entry { Text = "These entries" },
                new Entry { Text = "are demonstrating" },
                new Entry { Text = "implicit styles," },
                new Entry { Text = "and an implicit style override", BackgroundColor = Color.Lime, TextColor = Color.Red },
                new CustomEntry  { Text = "Subclassed Entry is not receiving the style" }
            }
        };
    }
}
```

Il costruttore definisce una singola *implicita* stile applicato alla pagina [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) istanze. Il `Style` viene utilizzato per visualizzare testo blu su uno sfondo giallo, durante l'impostazione anche altre opzioni di visualizzazione. Il `Style` viene aggiunto alla pagina [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) senza specificare un `key` stringa. Pertanto, il `Style` viene applicata a tutti il `Entry` istanze in modo implicito come corrispondano il [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) proprietà del `Style` esattamente. Tuttavia, il `Style` non viene applicato al `CustomEntry` istanza, che è una sottoclasse `Entry`.

## <a name="summary"></a>Riepilogo

Un *implicita* stile è utilizzato da tutti gli elementi visivi dello stesso [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/), senza la necessità di ciascun controllo per fare riferimento allo stile. A `Style` diventa *implicita* non specificando un `x:Key` attributo. Al contrario, il `x:Key` attributo diventerà automaticamente il valore della [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) proprietà.



## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili di base (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Utilizzo degli stili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Stile](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Metodo set](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
