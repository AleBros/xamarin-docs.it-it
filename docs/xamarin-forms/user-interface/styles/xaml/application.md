---
title: Stili globali in xamarin. Forms
description: Gli stili possono essere resi disponibili a livello globale aggiungendoli al dizionario risorse dell'applicazione. Ciò consente di evitare la duplicazione degli stili nelle pagine o controlli.
ms.prod: xamarin
ms.assetid: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: b663a7f2a4a67a9c3e18ed474d9935227fe34294
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53059561"
---
# <a name="global-styles-in-xamarinforms"></a>Stili globali in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_Gli stili possono essere resi disponibili a livello globale aggiungendoli al dizionario risorse dell'applicazione. Ciò consente di evitare la duplicazione degli stili nelle pagine o controlli._

## <a name="creating-a-global-style-in-xaml"></a>Creazione di uno stile globale in XAML

Per impostazione predefinita, tutte le applicazioni xamarin. Forms create da un modello utilizzano le **App** classe per implementare le [ `Application` ](xref:Xamarin.Forms.Application) sottoclasse. Per dichiarare un [ `Style` ](xref:Xamarin.Forms.Style) a livello di applicazione, dell'applicazione [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) tramite XAML, il valore predefinito **App** classe deve essere sostituita con un XAML **App** classe e code-behind associato. Per altre informazioni, vedere [funziona con la classe App](~/xamarin-forms/app-fundamentals/application-class.md).

Nell'esempio di codice riportato di seguito viene illustrato un [ `Style` ](xref:Xamarin.Forms.Style) dichiarato a livello di applicazione:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.App">
    <Application.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BorderColor" Value="Lime" />
                <Setter Property="BorderRadius" Value="5" />
                <Setter Property="BorderWidth" Value="5" />
                <Setter Property="WidthRequest" Value="200" />
                <Setter Property="TextColor" Value="Teal" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Ciò [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) definisce un singolo *esplicita* stile, `buttonStyle`, che verrà utilizzato per impostare l'aspetto della [ `Button` ](xref:Xamarin.Forms.Button) istanze. Tuttavia, possono essere globali stili *esplicite* o *implicita*.

Esempio di codice seguente mostra una pagina XAML applicando la `buttonStyle` alla pagina [ `Button` ](xref:Xamarin.Forms.Button) istanze:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Ciò comporta l'aspetto illustrato negli screenshot seguenti:

[![](application-images/application-styles-1.png "Stili globali riportato")](application-images/application-styles-1-large.png#lightbox "esempio stili globali")

Per informazioni sulla creazione di stili in una pagina [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), vedere [stili espliciti](~/xamarin-forms/user-interface/styles/explicit.md) e [stili impliciti](~/xamarin-forms/user-interface/styles/implicit.md).

### <a name="overriding-styles"></a>L'override degli stili

Stili di livello inferiori nella gerarchia di visualizzazione hanno la precedenza rispetto a quelli definiti più backup. Ad esempio, impostando un [ `Style` ](xref:Xamarin.Forms.Style) che consente di scegliere [ `Button.TextColor` ](xref:Xamarin.Forms.Button.TextColor) a `Red` nell'applicazione livello eseguirà l'override di uno stile a livello di pagina che imposta `Button.TextColor` a `Green`. Analogamente, uno stile a livello di pagina verrà sostituito da uno stile a livello di controllo. Inoltre, se `Button.TextColor` è impostato direttamente su una proprietà di controllo, si avrà la precedenza su tutti gli stili. Questa precedenza è illustrata nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                ...
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="buttonStyle" TargetType="Button">
                        ...
                        <Setter Property="TextColor" Value="Blue" />
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Originale `buttonStyle`, definite a livello di applicazione, viene sottoposto a override il `buttonStyle` istanza definita a livello di pagina. Inoltre, lo stile a livello di pagina esegue l'override del livello di controllo `buttonStyle`. Pertanto, il [ `Button` ](xref:Xamarin.Forms.Button) istanze vengono visualizzate con testo in blu, come illustrato negli screenshot seguenti:

[![](application-images/application-styles-2.png "Si esegue l'override degli stili riportato")](application-images/application-styles-2-large.png#lightbox "eseguendo l'override di esempio degli stili")

## <a name="creating-a-global-style-in-c35"></a>Creazione di uno stile globale in C&#35;

[`Style`](xref:Xamarin.Forms.Style) è possibile aggiungere istanze dell'applicazione [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) raccolta in c# mediante la creazione di un nuovo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e quindi aggiungendo il `Style` alle istanze di `ResourceDictionary`, come illustrato nell'esempio di codice seguente:

```csharp
public class App : Application
{
    public App ()
    {
        var buttonStyle = new Style (typeof(Button)) {
            Setters = {
                ...
                new Setter { Property = Button.TextColorProperty,    Value = Color.Teal }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("buttonStyle", buttonStyle);
        ...
    }
    ...
}
```

Il costruttore definisce un singolo *esplicita* stile di visualizzazione per l'applicazione ai [ `Button` ](xref:Xamarin.Forms.Button) istanze in tutta l'applicazione. *Espliciti* [ `Style` ](xref:Xamarin.Forms.Style) istanze vengono aggiunte alle [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) usando la [ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) metodo, specificando un `key`per fare riferimento alla stringa di `Style` istanza. Il `Style` istanza può quindi essere applicata a tutti i controlli del tipo corretto nell'applicazione. Tuttavia, possono essere globali stili *esplicite* o *implicita*.

Esempio di codice seguente mostra un c# pagina applicando la `buttonStyle` alla pagina [ `Button` ](xref:Xamarin.Forms.Button) istanze:

```csharp
public class ApplicationStylesPageCS : ContentPage
{
    public ApplicationStylesPageCS ()
    {
        ...
        Content = new StackLayout {
            Children = {
                new Button { Text = "These buttons", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "are demonstrating", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "application styles", Style = (Style)Application.Current.Resources ["buttonStyle"]
                }
            }
        };
    }
}
```

Il `buttonStyle` viene applicato al [ `Button` ](xref:Xamarin.Forms.Button) istanze impostando loro [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) proprietà e controlla l'aspetto del `Button` istanze.

## <a name="summary"></a>Riepilogo

Gli stili possono essere rese disponibili a livello globale vengono aggiunte all'applicazione [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Ciò consente di evitare la duplicazione degli stili nelle pagine o controlli.



## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili di base (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Utilizzo di stili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Stile di visualizzazione](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
