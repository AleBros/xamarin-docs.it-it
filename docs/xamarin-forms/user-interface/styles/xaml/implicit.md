---
title: Stili impliciti in xamarin. Forms
description: Uno stile implicito è quello usato da tutti i controlli di TargetType stesso, senza richiedere ogni controllo per fare riferimento allo stile.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: c30e6c0060407720e0324a3327607100db3960d6
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056149"
---
# <a name="implicit-styles-in-xamarinforms"></a>Stili impliciti in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_Uno stile implicito è quello usato da tutti i controlli di TargetType stesso, senza richiedere ogni controllo per fare riferimento allo stile._

## <a name="creating-an-implicit-style-in-xaml"></a>Creazione di uno stile implicito in XAML

Per dichiarare un [ `Style` ](xref:Xamarin.Forms.Style) a livello di pagina, un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) deve essere aggiunto alla pagina e quindi uno o più `Style` le dichiarazioni possono essere inclusi nel `ResourceDictionary`. Oggetto `Style` viene effettuata *implicita* se non viene specificata un' `x:Key` attributo. Verrà applicato lo stile per gli elementi visivi che soddisfano il `TargetType` esattamente, ma non per gli elementi che derivano dal `TargetType` valore.

Il codice seguente esempio illustra un' *implicita* stile dichiarato in XAML in una pagina `ResourceDictionary`e applicate alla pagina [ `Entry` ](xref:Xamarin.Forms.Entry) istanze:

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

Il [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) definisce un singolo *implicita* stile applicato alla pagina [ `Entry` ](xref:Xamarin.Forms.Entry) istanze. Il `Style` consente di visualizzare il testo in blu su uno sfondo giallo, durante l'impostazione anche altre opzioni di visualizzazione. Il `Style` viene aggiunto alla pagina [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) senza specificare un `x:Key` attributo. Di conseguenza, il `Style` applicato a tutti la `Entry` le istanze in modo implicito i valori corrispondono il [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) proprietà del `Style` esattamente. Tuttavia, il `Style` non viene applicato al `CustomEntry` istanza, che è una sottoclasse `Entry`. Ciò comporta l'aspetto illustrato negli screenshot seguenti:

[![](implicit-images/implicit-styles.png "Esempio di stili impliciti")](implicit-images/implicit-styles-large.png#lightbox "esempio stili impliciti")

Inoltre, la quarta [ `Entry` ](xref:Xamarin.Forms.Entry) esegue l'override la [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) e [ `TextColor` ](xref:Xamarin.Forms.Entry.TextColor) proprietà dello stile implicito da diversi `Color`valori.

### <a name="creating-an-implicit-style-at-the-control-level"></a>Creazione di uno stile implicito il controllo a livello

Oltre alla creazione *implicita* stili a livello di pagina, possono anche essere creati a livello di controllo, come illustrato nell'esempio di codice seguente:

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

In questo esempio, il *implicita* [ `Style` ](xref:Xamarin.Forms.Style) viene assegnato al [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) raccolta del [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)controllo. Il *implicita* stile può quindi essere applicato al controllo e i relativi elementi figlio.

Per informazioni sulla creazione di stili in un'applicazione [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), vedere [stili globali](~/xamarin-forms/user-interface/styles/application.md).

## <a name="creating-an-implicit-style-in-c35"></a>Creazione di uno stile implicito in C&#35;

[`Style`](xref:Xamarin.Forms.Style) le istanze possono essere aggiunte a una pagina [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) raccolta in c# mediante la creazione di un nuovo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e quindi aggiungendo il `Style` alle istanze il `ResourceDictionary`, come illustrato di esempio di codice seguente:

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

Il costruttore definisce una sola *implicita* stile applicato alla pagina [ `Entry` ](xref:Xamarin.Forms.Entry) istanze. Il `Style` consente di visualizzare il testo in blu su uno sfondo giallo, durante l'impostazione anche altre opzioni di visualizzazione. Il `Style` viene aggiunto alla pagina [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) senza specificare un `key` stringa. Di conseguenza, il `Style` applicato a tutti la `Entry` le istanze in modo implicito i valori corrispondono il [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) proprietà del `Style` esattamente. Tuttavia, il `Style` non viene applicato al `CustomEntry` istanza, che è una sottoclasse `Entry`.

## <a name="summary"></a>Riepilogo

Un' *implicita* stile è quello usato da tutti gli elementi visivi dello stesso [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType), senza richiedere ogni controllo per fare riferimento allo stile. Oggetto `Style` viene effettuata *implicita* se non viene specificata un' `x:Key` attributo. Al contrario, il `x:Key` attributo verrà portato automaticamente il valore della [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) proprietà.



## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili di base (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Utilizzo di stili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Stile di visualizzazione](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
