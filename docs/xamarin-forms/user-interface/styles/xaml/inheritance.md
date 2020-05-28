---
title: Ereditarietà dello stile inXamarin.Forms
description: Gli stili possono ereditare da altri stili per ridurre la duplicazione e consentire il riutilizzo. Questo articolo illustra come eseguire l'ereditarietà dello stile in un' Xamarin.Forms applicazione.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 80cc419ae098f4a0cbbd782785c0ec5ba03fa703
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138957"
---
# <a name="style-inheritance-in-xamarinforms"></a>Ereditarietà dello stile inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Gli stili possono ereditare da altri stili per ridurre la duplicazione e consentire il riutilizzo._

## <a name="style-inheritance-in-xaml"></a>Ereditarietà dello stile in XAML

L'ereditarietà dello stile viene eseguita impostando la [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) proprietà su un oggetto esistente [`Style`](xref:Xamarin.Forms.Style) . In XAML, questa operazione viene eseguita impostando la `BasedOn` proprietà su un' `StaticResource` estensione di markup che fa riferimento a un oggetto creato in precedenza `Style` . In C#, questa operazione viene eseguita impostando la `BasedOn` proprietà su un' `Style` istanza.

Gli stili che ereditano da uno stile di base possono includere [`Setter`](xref:Xamarin.Forms.Setter) istanze per nuove proprietà o usarle per eseguire l'override degli stili dallo stile di base. Inoltre, gli stili che ereditano da uno stile di base devono avere come destinazione lo stesso tipo o un tipo che deriva dal tipo di destinazione dello stile di base. Se ad esempio uno stile di base è destinato a [`View`](xref:Xamarin.Forms.View) istanze, gli stili basati sullo stile di base possono avere `View` come destinazione istanze o tipi che derivano dalla `View` classe, ad esempio [`Label`](xref:Xamarin.Forms.Label) istanze di e [`Button`](xref:Xamarin.Forms.Button) .

Il codice seguente illustra l'ereditarietà dello stile *esplicito* in una pagina XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" IconImageSource="xaml.png">
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

Le `baseStyle` istanze di destinazione [`View`](xref:Xamarin.Forms.View) e imposta le [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà e. L'oggetto `baseStyle` non è impostato direttamente su alcun controllo. Ma `labelStyle` `buttonStyle` ereditano da esso, impostando valori di proprietà associabili aggiuntivi. `labelStyle`E `buttonStyle` vengono quindi applicati alle [`Label`](xref:Xamarin.Forms.Label) istanze e all' [`Button`](xref:Xamarin.Forms.Button) istanza, impostando le relative [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Proprietà. Il risultato è l'aspetto illustrato negli screenshot seguenti:

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> Uno stile implicito può derivare da uno stile esplicito, ma non è possibile derivare uno stile esplicito da uno stile implicito.

### <a name="respecting-the-inheritance-chain"></a>Rispetto della catena di ereditarietà

Uno stile può ereditare solo da stili allo stesso livello o superiore nella gerarchia di visualizzazione. Ciò significa che:

- Una risorsa a livello di applicazione può ereditare solo da altre risorse a livello di applicazione.
- Una risorsa a livello di pagina può ereditare dalle risorse a livello di applicazione e da altre risorse a livello di pagina.
- Una risorsa a livello di controllo può ereditare dalle risorse a livello di applicazione, dalle risorse a livello di pagina e da altre risorse a livello di controllo.

Questa catena di ereditarietà è illustrata nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" IconImageSource="xaml.png">
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

In questo esempio, `labelStyle` e `buttonStyle` sono risorse a livello di controllo, mentre `baseStyle` è una risorsa a livello di pagina. Tuttavia, mentre `labelStyle` e `buttonStyle` ereditano da `baseStyle` , non è possibile che `baseStyle` erediti da `labelStyle` o, a `buttonStyle` causa delle rispettive posizioni nella gerarchia di visualizzazione.

## <a name="style-inheritance-in-c35"></a>Ereditarietà dello stile in C&#35;

[`Style`](xref:Xamarin.Forms.Style)Nell'esempio di codice seguente viene illustrata la pagina C# equivalente, in cui le istanze vengono assegnate direttamente alle [`Style`](xref:Xamarin.Forms.NavigableElement.Style) proprietà dei controlli necessari:

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

Le `baseStyle` istanze di destinazione [`View`](xref:Xamarin.Forms.View) e imposta le [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) proprietà e. L'oggetto `baseStyle` non è impostato direttamente su alcun controllo. Ma `labelStyle` `buttonStyle` ereditano da esso, impostando valori di proprietà associabili aggiuntivi. `labelStyle`E `buttonStyle` vengono quindi applicati alle [`Label`](xref:Xamarin.Forms.Label) istanze e all' [`Button`](xref:Xamarin.Forms.Button) istanza, impostando le relative [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Proprietà.

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stili di base (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Utilizzo degli stili (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Stile](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
