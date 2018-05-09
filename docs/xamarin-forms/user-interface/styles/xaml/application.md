---
title: Stili globali
description: Gli stili possono essere resi disponibili a livello globale aggiungendoli al dizionario di risorse dell'applicazione. Ciò consente di evitare la duplicazione degli stili nelle pagine o controlli.
ms.prod: xamarin
ms.assetid: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: a505720e5fef8fe9e9ef82d03e53370210772f45
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="global-styles"></a>Stili globali

_Gli stili possono essere resi disponibili a livello globale aggiungendoli al dizionario di risorse dell'applicazione. Ciò consente di evitare la duplicazione degli stili nelle pagine o controlli._

## <a name="creating-a-global-style-in-xaml"></a>Creazione di uno stile globale in XAML

Per impostazione predefinita, tutte le applicazioni di xamarin. Forms create da un modello utilizzano il **App** classe per implementare il [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/) sottoclasse. Per dichiarare un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) a livello di applicazione, l'applicazione [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) tramite XAML, il valore predefinito **App** classe deve essere sostituita con un file XAML **App** classe e code-behind associato. Per ulteriori informazioni, vedere [si utilizza la classe App](~/xamarin-forms/app-fundamentals/application-class.md).

Nell'esempio di codice riportato di seguito viene illustrato un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) dichiarato a livello di applicazione:

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

Questo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) definisce una singola *esplicita* stile, `buttonStyle`, che verrà utilizzato per impostare l'aspetto di [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) istanze. Gli stili globali possono tuttavia essere *esplicita* o *implicita*.

L'esempio di codice seguente mostra una pagina XAML l'applicazione di `buttonStyle` alla pagina [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) istanze:

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

Ciò comporta l'aspetto illustrato nelle schermate seguenti:

[![](application-images/application-styles-1.png "Gli stili globali esempio")](application-images/application-styles-1-large.png#lightbox "esempio stili globali")

Per informazioni sulla creazione di stili in una pagina [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), vedere [stili esplicita](~/xamarin-forms/user-interface/styles/explicit.md) e [stili impliciti](~/xamarin-forms/user-interface/styles/implicit.md).

### <a name="overriding-styles"></a>L'override degli stili

Gli stili di livello inferiori nella gerarchia di visualizzazione hanno la precedenza su quelli definiti più backup. Ad esempio, impostando un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) che imposta [ `Button.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.TextColor/) a `Red` l'applicazione di livello verrà sottoposto a override da un tipo di livello di pagina che imposta `Button.TextColor` a `Green`. Analogamente, uno stile di livello di pagina verrà sostituito da un tipo di livello di controllo. Inoltre, se `Button.TextColor` è impostato direttamente su una proprietà di controllo, si avrà la precedenza su tutti gli stili. La precedenza è illustrata nell'esempio di codice seguente:

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

Originale `buttonStyle`, definiti a livello di applicazione, viene sottoposto a override di `buttonStyle` istanza definita a livello di pagina. Inoltre, lo stile di livello di pagina viene sottoposta a override dal livello di controllo `buttonStyle`. Pertanto, il [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) istanze vengono visualizzate con testo blu, come illustrato nelle schermate seguenti:

[![](application-images/application-styles-2.png "Si esegue l'override degli stili esempio")](application-images/application-styles-2-large.png#lightbox "si esegue l'override di esempio di stili")

## <a name="creating-a-global-style-in-c35"></a>Creazione di uno stile globale in C&#35;

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) le istanze possono essere aggiunte all'applicazione [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) raccolta in c# tramite la creazione di un nuovo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)e quindi aggiungendo il `Style` istanze per il `ResourceDictionary`, come Nell'esempio di codice seguente:

```csharp
public class App : Application
{
    public App ()
    {
        var buttonStyle = new Style (typeof(Button)) {
            Setters = {
                ...
                new Setter { Property = Button.TextColorProperty,   Value = Color.Teal }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("buttonStyle", buttonStyle);
        ...
    }
    ...
}
```

Il costruttore definisce una singola *esplicita* stile per l'applicazione a [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) istanze in tutta l'applicazione. *Esplicita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) vengono aggiunte istanze di [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) utilizzando il [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ResourceDictionary.Add/p/System.String/System.Object/) metodo, specificando un `key`stringa per fare riferimento al `Style` istanza. Il `Style` istanza può essere applicata a tutti i controlli del tipo corretto nell'applicazione. Gli stili globali possono tuttavia essere *esplicita* o *implicita*.

Esempio di codice seguente viene illustrato un c# pagina applicazione di `buttonStyle` alla pagina [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) istanze:

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

Il `buttonStyle` viene applicato al [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) istanze impostando i relativi [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà e controlla l'aspetto del `Button` istanze.

## <a name="summary"></a>Riepilogo

Gli stili possono essere resi disponibili a livello globale, aggiungerli all'applicazione [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/). Ciò consente di evitare la duplicazione degli stili nelle pagine o controlli.



## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili di base (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Utilizzo degli stili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Stile](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Metodo set](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
