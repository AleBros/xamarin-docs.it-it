---
title: Ereditarietà degli stili in xamarin. Forms
description: Stili possono ereditare da altri stili per ridurre la duplicazione e consentire il riutilizzo. Questo articolo illustra come eseguire l'ereditarietà dello stile di un'applicazione xamarin. Forms.
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: bef48db93ae76346802b6569080bb1e54e3e51b3
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55291947"
---
# <a name="style-inheritance-in-xamarinforms"></a>Ereditarietà degli stili in xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_Stili possono ereditare da altri stili per ridurre la duplicazione e consentire il riutilizzo._

## <a name="style-inheritance-in-xaml"></a>Ereditarietà degli stili in XAML

L'ereditarietà dello stile viene eseguita impostando il [ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn) proprietà a un oggetto esistente [ `Style` ](xref:Xamarin.Forms.Style). In XAML, questo si ottiene impostando il `BasedOn` proprietà per un `StaticResource` estensione di markup che fa riferimento a una creata in precedenza `Style`. In C#, questo si ottiene impostando il `BasedOn` proprietà su un `Style` istanza.

Gli stili che ereditano da uno stile di base possono includere [ `Setter` ](xref:Xamarin.Forms.Setter) istanze per nuove proprietà, oppure usarli per eseguire l'override degli stili dallo stile di base. Inoltre, gli stili che ereditano da uno stile di base devono avere come destinazione lo stesso tipo o un tipo da cui deriva il tipo di destinazione per lo stile di base. Ad esempio, se è destinato a una base di stile [ `View` ](xref:Xamarin.Forms.View) istanze, gli stili che si basano sullo stile di base possono avere come destinazione `View` istanze o tipi che derivano dal `View` classe, ad esempio [ `Label` ](xref:Xamarin.Forms.Label) e [ `Button` ](xref:Xamarin.Forms.Button) istanze.

Il codice seguente illustra *esplicita* applicare uno stile di ereditarietà in una pagina XAML:

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

Il `baseStyle` destinazioni [ `View` ](xref:Xamarin.Forms.View) istanze e imposta il [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) e [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) proprietà. Il `baseStyle` non è impostato direttamente su tutti i controlli. Al contrario, `labelStyle` e `buttonStyle` ereditano da esso, i valori di proprietà associabili aggiuntiva. Il `labelStyle` e `buttonStyle` vengono quindi applicati al [ `Label` ](xref:Xamarin.Forms.Label) istanze e [ `Button` ](xref:Xamarin.Forms.Button) istanza, impostando i [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) delle proprietà. Ciò comporta l'aspetto illustrato negli screenshot seguenti:

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> Uno stile implicito può essere derivato da uno stile esplicito, ma uno stile esplicito non può essere derivato da uno stile implicito.

### <a name="respecting-the-inheritance-chain"></a>Rispettando la catena di ereditarietà

Uno stile può ereditare solo da stili allo stesso livello o precedente, nella gerarchia di visualizzazione. Vale a dire che:

- Una risorsa a livello di applicazione può ereditare solo da altre risorse a livello di applicazione.
- Una risorsa a livello di pagina può ereditare da risorse a livello di applicazione e altre risorse a livello di pagina.
- Una risorsa a livello di controllo può ereditare da risorse a livello di applicazione, le risorse a livello di pagina e altre risorse a livello di controllo.

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

In questo esempio `labelStyle` e `buttonStyle` sono i controlli di risorse di livello, mentre `baseStyle` è una risorsa a livello di pagina. Tuttavia, se `labelStyle` e `buttonStyle` ereditare `baseStyle`, non è possibile `baseStyle` da cui ereditare `labelStyle` o `buttonStyle`, a causa di nelle rispettive posizioni nella gerarchia di visualizzazione.

## <a name="style-inheritance-in-c35"></a>Ereditarietà degli stili in C&#35;

Equivalenti C# pagina, dove [ `Style` ](xref:Xamarin.Forms.Style) istanze sono assegnate direttamente al [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) proprietà dei controlli necessari, è illustrato nell'esempio di codice seguente:

```csharp
public class StyleInheritancePageCS : ContentPage
{
    public StyleInheritancePageCS ()
    {
        var baseStyle = new Style (typeof(View)) {
            Setters = {
                new Setter {
                    Property = View.HorizontalOptionsProperty, Value = LayoutOptions.Center    },
                ...
            }
        };

        var labelStyle = new Style (typeof(Label)) {
            BasedOn = baseStyle,
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Teal    }
            }
        };

        var buttonStyle = new Style (typeof(Button)) {
            BasedOn = baseStyle,
            Setters = {
                new Setter { Property = Button.BorderColorProperty, Value =    Color.Lime },
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

Il `baseStyle` destinazioni [ `View` ](xref:Xamarin.Forms.View) istanze e imposta il [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) e [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) proprietà. Il `baseStyle` non è impostato direttamente su tutti i controlli. Al contrario, `labelStyle` e `buttonStyle` ereditano da esso, i valori di proprietà associabili aggiuntiva. Il `labelStyle` e `buttonStyle` vengono quindi applicati al [ `Label` ](xref:Xamarin.Forms.Label) istanze e [ `Button` ](xref:Xamarin.Forms.Button) istanza, impostando i [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) delle proprietà.

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili di base (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Utilizzo di stili (esempio)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Stile di visualizzazione](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
