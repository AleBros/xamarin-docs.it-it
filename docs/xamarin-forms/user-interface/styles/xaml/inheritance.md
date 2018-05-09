---
title: Ereditarietà degli stili
description: Stili possono ereditare da altri stili per ridurre la duplicazione e consentire il riutilizzo.
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: ffddc14145a3b187c03a6b2ba5ac6f8d6414468c
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="style-inheritance"></a>Ereditarietà degli stili

_Stili possono ereditare da altri stili per ridurre la duplicazione e consentire il riutilizzo._

## <a name="style-inheritance-in-xaml"></a>Ereditarietà degli stili in XAML

Ereditarietà degli stili viene eseguita impostando il [ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/) proprietà esistente [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/). In XAML, questo risultato si ottiene impostando il `BasedOn` proprietà per un `StaticResource` estensione di markup che fa riferimento a un oggetto creato in precedenza `Style`. In c#, questo risultato si ottiene impostando il `BasedOn` proprietà per un `Style` istanza.

Gli stili che ereditano da uno stile di base possono includere [ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/) istanze per le proprietà di nuovo, o utilizzarli per eseguire l'override degli stili dallo stile di base. Inoltre, gli stili che ereditano da uno stile di base devono avere come destinazione lo stesso tipo o un tipo che deriva dal tipo di destinazione per lo stile di base. Ad esempio, se è destinato a un stile di base [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) istanze, gli stili che si basano lo stile di base possono avere come destinazione `View` istanze o tipi che derivano dal `View` classe, ad esempio [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) e [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) istanze.

Il codice seguente illustra *esplicita* stile ereditarietà in una pagina XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
            </Style>
            <Style x:Key="labelStyle" TargetType="Label"
                   BasedOn="{StaticResource baseStyle}">
                ...
                <Setter Property="TextColor" Value="Teal" />
            </Style>
            <Style x:Key="buttonStyle" TargetType="Button"
                   BasedOn="{StaticResource baseStyle}">
                <Setter Property="BorderColor" Value="Lime" />
                ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelStyle}" />
            ...
            <Button Text="So is the button"
                    Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Il `baseStyle` destinazioni [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) le istanze e imposta il [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) proprietà. Il `baseStyle` non è impostato direttamente su tutti i controlli. In alternativa, `labelStyle` e `buttonStyle` ereditano da essa, i valori di proprietà associabile aggiuntive. Il `labelStyle` e `buttonStyle` vengono quindi applicati al [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) istanze e [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) istanza, impostando i relativi [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà. Ciò comporta l'aspetto illustrato nelle schermate seguenti:

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> Uno stile implicito può essere derivato da un stile esplicito, ma un stile esplicito non può essere derivato da uno stile implicito.

### <a name="respecting-the-inheritance-chain"></a>Rispettando la catena di ereditarietà

Uno stile può ereditare solo da stili allo stesso livello o superiore nella gerarchia della visualizzazione. Vale a dire che:

- Una risorsa a livello dell'applicazione può ereditare solo da altre risorse di livello applicazione.
- Una risorsa di livello di pagina può ereditare da risorse di livello applicazione e altre risorse di livello di pagina.
- Una risorsa di livello di controllo può ereditare da risorse di livello applicazione, risorse a livello di pagina e altre risorse di livello di controllo.

Questa catena di ereditarietà è illustrata nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelStyle" TargetType="Label" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                    <Style x:Key="buttonStyle" TargetType="Button" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

In questo esempio, `labelStyle` e `buttonStyle` è risorse a livello di controllo mentre `baseStyle` è una risorsa di livello di pagina. Tuttavia, se `labelStyle` e `buttonStyle` ereditare `baseStyle`, non è possibile per `baseStyle` da cui ereditare `labelStyle` o `buttonStyle`, a causa di nelle rispettive posizioni nella gerarchia della visualizzazione.

## <a name="style-inheritance-in-c35"></a>Ereditarietà degli stili in C&#35;

Equivalente in c# pagina, in cui [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) istanze sono assegnate direttamente al [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà dei controlli necessari, è illustrato nell'esempio di codice seguente:

```csharp
public class StyleInheritancePageCS : ContentPage
{
    public StyleInheritancePageCS ()
    {
        var baseStyle = new Style (typeof(View)) {
            Setters = {
                new Setter {
                    Property = View.HorizontalOptionsProperty, Value = LayoutOptions.Center },
                ...
            }
        };

        var labelStyle = new Style (typeof(Label)) {
            BasedOn = baseStyle,
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Teal }
            }
        };

        var buttonStyle = new Style (typeof(Button)) {
            BasedOn = baseStyle,
            Setters = {
                new Setter { Property = Button.BorderColorProperty, Value = Color.Lime },
                ...
            }
        };
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelStyle },
                ...
                new Button { Text = "So is the button", Style = buttonStyle }
            }
        };
    }
}
```

Il `baseStyle` destinazioni [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) le istanze e imposta il [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) proprietà. Il `baseStyle` non è impostato direttamente su tutti i controlli. In alternativa, `labelStyle` e `buttonStyle` ereditano da essa, i valori di proprietà associabile aggiuntive. Il `labelStyle` e `buttonStyle` vengono quindi applicati al [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) istanze e [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) istanza, impostando i relativi [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà.

## <a name="summary"></a>Riepilogo

Stili possono ereditare da altri stili per ridurre la duplicazione e consentire il riutilizzo. Ereditarietà degli stili viene eseguita impostando il [ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/) proprietà esistente [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/).


## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili di base (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Utilizzo degli stili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Stile](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Metodo set](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
