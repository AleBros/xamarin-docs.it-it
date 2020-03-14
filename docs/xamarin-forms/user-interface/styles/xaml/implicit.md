---
title: Stili impliciti in xamarin. Forms
description: Uno stile implicito è quello usato da tutti i controlli di TargetType stesso, senza richiedere ogni controllo per fare riferimento allo stile.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: d58ba81596cccf470b7246514d71f35968599880
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305276"
---
# <a name="implicit-styles-in-xamarinforms"></a>Stili impliciti in xamarin. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Uno stile implicito è quello usato da tutti i controlli dello stesso TargetType, senza richiedere a ogni controllo di fare riferimento allo stile._

## <a name="create-an-implicit-style-in-xaml"></a>Creare uno stile implicito in XAML

Per dichiarare un [`Style`](xref:Xamarin.Forms.Style) a livello di pagina, è necessario aggiungere una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) alla pagina e quindi includere una o più dichiarazioni di `Style` nel `ResourceDictionary`. Un `Style` viene reso *implicito* non specificando un attributo `x:Key`. Lo stile verrà quindi applicato agli elementi visivi che corrispondono esattamente `TargetType`, ma non agli elementi derivati dal valore `TargetType`.

Nell'esempio di codice riportato di seguito viene illustrato uno stile *implicito* dichiarato in XAML in un `ResourceDictionary`della pagina e applicato alle istanze [`Entry`](xref:Xamarin.Forms.Entry) della pagina:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
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

Il [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) definisce un unico stile *implicito* che viene applicato alle istanze [`Entry`](xref:Xamarin.Forms.Entry) della pagina. Il `Style` viene usato per visualizzare il testo blu su uno sfondo giallo, impostando anche altre opzioni relative all'aspetto. Il `Style` viene aggiunto al [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) della pagina senza specificare un attributo di `x:Key`. Pertanto, l'`Style` viene applicata a tutte le istanze di `Entry` in modo implicito in quanto corrispondono alla proprietà [`TargetType`](xref:Xamarin.Forms.Style.TargetType) del `Style` esattamente. Tuttavia, il `Style` non viene applicato all'istanza `CustomEntry`, che è un `Entry`sottoclassato. Il risultato è l'aspetto illustrato negli screenshot seguenti:

[Esempio ![stili impliciti](implicit-images/implicit-styles.png)](implicit-images/implicit-styles-large.png#lightbox)

Inoltre, il quarto [`Entry`](xref:Xamarin.Forms.Entry) esegue l'override delle proprietà [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) e [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) dello stile implicito in valori di `Color` diversi.

### <a name="create-an-implicit-style-at-the-control-level"></a>Creare uno stile implicito a livello di controllo

Oltre a creare stili *impliciti* a livello di pagina, possono anche essere creati a livello di controllo, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
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

In questo esempio, il [`Style`](xref:Xamarin.Forms.Style) *implicito* viene assegnato alla raccolta [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) del controllo [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Lo stile *implicito* può quindi essere applicato al controllo e ai relativi elementi figlio.

Per informazioni sulla creazione di stili nel [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)di un'applicazione, vedere [stili globali](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-implicit-style-in-c35"></a>Creare uno stile implicito in C&#35;

è possibile aggiungere istanze di [`Style`](xref:Xamarin.Forms.Style) alla raccolta [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) di una pagina C# in creando un nuovo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), quindi aggiungendo le istanze di `Style` al `ResourceDictionary`, come illustrato nell'esempio di codice seguente:

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

Il costruttore definisce un unico stile *implicito* che viene applicato alle istanze [`Entry`](xref:Xamarin.Forms.Entry) della pagina. Il `Style` viene usato per visualizzare il testo blu su uno sfondo giallo, impostando anche altre opzioni relative all'aspetto. Il `Style` viene aggiunto al [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) della pagina senza specificare una stringa di `key`. Pertanto, l'`Style` viene applicata a tutte le istanze di `Entry` in modo implicito in quanto corrispondono alla proprietà [`TargetType`](xref:Xamarin.Forms.Style.TargetType) del `Style` esattamente. Tuttavia, il `Style` non viene applicato all'istanza `CustomEntry`, che è un `Entry`sottoclassato.

## <a name="apply-a-style-to-derived-types"></a>Applicare uno stile ai tipi derivati

La proprietà [`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) consente di applicare uno stile ai controlli derivati dal tipo di base a cui fa riferimento la proprietà [`TargetType`](xref:Xamarin.Forms.Style.TargetType) . L'impostazione di questa proprietà su `true` consente pertanto a un singolo stile di avere come destinazione più tipi, purché i tipi derivano dal tipo di base specificato nella proprietà `TargetType`.

Nell'esempio seguente viene illustrato uno stile implicito che imposta il colore di sfondo delle istanze di [`Button`](xref:Xamarin.Forms.Button) su rosso:

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

Inserendo questo stile in una [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a livello di pagina, questo verrà applicato a tutte le istanze di [`Button`](xref:Xamarin.Forms.Button) nella pagina e anche a tutti i controlli che derivano da `Button`. Tuttavia, se la proprietà [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) è rimasta non impostata, lo stile verrebbe applicato solo alle istanze di `Button`.

Il codice C# equivalente è il seguente:

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
- [Stili di base (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Utilizzo degli stili (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
