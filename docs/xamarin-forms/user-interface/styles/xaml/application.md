---
title: Stili globali inXamarin.Forms
description: Gli stili possono essere resi disponibili a livello globale aggiungendoli al dizionario risorse dell'applicazione. Ciò consente di evitare la duplicazione degli stili in pagine o controlli.
ms.prod: xamarin
ms.assetid: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2a222c3ee2234904cce94b52a14654728a1aa6d1
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84140127"
---
# <a name="global-styles-in-xamarinforms"></a>Stili globali inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Gli stili possono essere resi disponibili a livello globale aggiungendoli al dizionario risorse dell'applicazione. Ciò consente di evitare la duplicazione degli stili in pagine o controlli._

## <a name="create-a-global-style-in-xaml"></a>Creare uno stile globale in XAML

Per impostazione predefinita, tutte le Xamarin.Forms applicazioni create da un modello usano la classe **app** per implementare la [`Application`](xref:Xamarin.Forms.Application) sottoclasse. Per dichiarare un oggetto [`Style`](xref:Xamarin.Forms.Style) a livello di applicazione, nell'applicazione [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) usando XAML, la classe **app** predefinita deve essere sostituita da una classe **app** XAML e code-behind associato. Per altre informazioni, vedere [uso della classe App](~/xamarin-forms/app-fundamentals/application-class.md).

Nell'esempio di codice riportato di seguito viene illustrato un oggetto [`Style`](xref:Xamarin.Forms.Style) dichiarato a livello di applicazione:

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

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)Definisce un singolo stile *esplicito* , `buttonStyle` , che verrà utilizzato per impostare l'aspetto delle [`Button`](xref:Xamarin.Forms.Button) istanze. Tuttavia, gli stili globali possono essere *espliciti* o *impliciti*.

Nell'esempio di codice seguente viene illustrata una pagina XAML che applica alle `buttonStyle` istanze della pagina [`Button`](xref:Xamarin.Forms.Button) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Il risultato è l'aspetto illustrato negli screenshot seguenti:

[![](application-images/application-styles-1.png "Global Styles Example")](application-images/application-styles-1-large.png#lightbox "Global Styles Example")

Per informazioni sulla creazione di stili in una pagina [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , vedere stili [espliciti](~/xamarin-forms/user-interface/styles/explicit.md) e [stili impliciti](~/xamarin-forms/user-interface/styles/implicit.md).

### <a name="override-styles"></a>Stili di sostituzione

Gli stili inferiori nella gerarchia di visualizzazione hanno la precedenza su quelli definiti più in alto. Ad esempio, l'impostazione di un oggetto [`Style`](xref:Xamarin.Forms.Style) che imposta [`Button.TextColor`](xref:Xamarin.Forms.Button.TextColor) su `Red` a livello di applicazione verrà sottoposta a override da uno stile a livello di pagina che imposta `Button.TextColor` su `Green` . Allo stesso modo, uno stile a livello di pagina verrà sottoposto a override da uno stile del livello di controllo. Inoltre, se `Button.TextColor` è impostato direttamente su una proprietà del controllo, avrà la precedenza su qualsiasi stile. Questa precedenza è illustrata nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" IconImageSource="xaml.png">
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

L' `buttonStyle` istanza originale, definita a livello di applicazione, viene sottoposta a override dall' `buttonStyle` istanza definita a livello di pagina. Inoltre, lo stile a livello di pagina viene sottoposto a override dal livello di controllo `buttonStyle` . Le [`Button`](xref:Xamarin.Forms.Button) istanze vengono pertanto visualizzate con testo blu, come illustrato nelle schermate seguenti:

[![](application-images/application-styles-2.png "Overriding Styles Example")](application-images/application-styles-2-large.png#lightbox "Overriding Styles Example")

## <a name="create-a-global-style-in-c35"></a>Creare uno stile globale in C&#35;

[`Style`](xref:Xamarin.Forms.Style)è possibile aggiungere istanze alla raccolta dell'applicazione [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) in C# creando un nuovo oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e quindi aggiungendo le `Style` istanze a `ResourceDictionary` , come illustrato nell'esempio di codice seguente:

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

Il costruttore definisce un unico stile *esplicito* per l'applicazione alle [`Button`](xref:Xamarin.Forms.Button) istanze nell'intera applicazione. In *modo esplicito* [`Style`](xref:Xamarin.Forms.Style) le istanze vengono aggiunte a [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) utilizzando il [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) metodo, specificando una `key` stringa per fare riferimento all' `Style` istanza di. L' `Style` istanza può quindi essere applicata a tutti i controlli del tipo corretto nell'applicazione. Tuttavia, gli stili globali possono essere *espliciti* o *impliciti*.

Nell'esempio di codice seguente viene illustrata una pagina C# che applica l'oggetto `buttonStyle` alle istanze della pagina [`Button`](xref:Xamarin.Forms.Button) :

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

`buttonStyle`Viene applicato alle [`Button`](xref:Xamarin.Forms.Button) istanze di impostando le relative [`Style`](xref:Xamarin.Forms.NavigableElement.Style) proprietà e controlla l'aspetto delle `Button` istanze.

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili di base (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Utilizzo degli stili (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
