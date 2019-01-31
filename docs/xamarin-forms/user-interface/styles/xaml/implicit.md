---
title: Stili impliciti in xamarin. Forms
description: Uno stile implicito è quello usato da tutti i controlli di TargetType stesso, senza richiedere ogni controllo per fare riferimento allo stile.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: 67b8bac62cacb091323d084e1c7cec9accc30844
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55291973"
---
# <a name="implicit-styles-in-xamarinforms"></a>Stili impliciti in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_Uno stile implicito è quello usato da tutti i controlli di TargetType stesso, senza richiedere ogni controllo per fare riferimento allo stile._

## <a name="create-an-implicit-style-in-xaml"></a>Creare uno stile implicito in XAML

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

### <a name="create-an-implicit-style-at-the-control-level"></a>Creare uno stile implicito a livello di controllo

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

## <a name="create-an-implicit-style-in-c35"></a>Creare uno stile implicito in C&#35;

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

## <a name="apply-a-style-to-derived-types"></a>Applicare uno stile ai tipi derivati

Il [ `Style.ApplyToDerivedTypes` ](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) proprietà consente a uno stile da applicare ai controlli che derivano dal tipo di base fa riferimento il [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) proprietà. Pertanto, impostando questa proprietà su `true` consente un solo stile come destinazione più tipi, a condizione che i tipi derivano dal tipo di base specificato nel `TargetType` proprietà.

L'esempio seguente mostra uno stile implicito che imposta il colore di sfondo [ `Button` ](xref:Xamarin.Forms.Button) istanze a rosso:

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

Inserire questo stile di visualizzazione in un livello di pagina [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) comporterà si applichino a tutti [ `Button` ](xref:Xamarin.Forms.Button) istanze nella pagina, nonché per tutti i controlli che derivano da `Button`. Tuttavia, se il [ `ApplyToDerivedTypes` ](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) proprietà rimasta non impostata, lo stile viene applicato solo a `Button` istanze.

Il codice c# equivalente è:

```csharp
var buttonStyle = new Style(typeof(Button))
{
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.Red
        }
    }
};

Resources = new ResourceDictionary { buttonStyle };
```

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili di base (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Utilizzo di stili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Stile di visualizzazione](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
