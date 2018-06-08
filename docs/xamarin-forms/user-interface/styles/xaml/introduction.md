---
title: Introduzione agli stili
description: Gli stili consentono l'aspetto degli elementi visivi per essere personalizzati. Gli stili definiti per un tipo specifico e contengono valori per le proprietà disponibili su tale tipo.
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: f878641f9266da74d61eda8a85d4e16de193be0e
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847966"
---
# <a name="introduction-to-styles"></a>Introduzione agli stili

_Gli stili consentono l'aspetto degli elementi visivi per essere personalizzati. Gli stili definiti per un tipo specifico e contengono valori per le proprietà disponibili su tale tipo._

Xamarin. Forms applicazioni spesso contengono più controlli che hanno un aspetto identico. Ad esempio, un'applicazione può disporre di più [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) le istanze che hanno le stesse opzioni di tipo di carattere e le opzioni di layout, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="Styles.NoStylesPage"
    Title="No Styles"
    Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="are not"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="using styles"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Esempio di codice seguente mostra la pagina di equivalente creata in c#:

```csharp
public class NoStylesPageCS : ContentPage
{
    public NoStylesPageCS ()
    {
        Title = "No Styles";
        Icon = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label {
                    Text = "These labels",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "are not",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "using styles",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                }
            }
        };
    }
}
```

Ogni [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) istanza dispone di valori delle proprietà identici per controllare l'aspetto del testo visualizzato per il `Label`. Ciò comporta l'aspetto illustrato nelle schermate seguenti:

[![](introduction-images/no-styles.png "Etichetta aspetto senza stili")](introduction-images/no-styles-large.png#lightbox "etichetta aspetto senza gli stili")

Impostare l'aspetto di ogni singolo controllo può essere ricorrenti e soggetta a errori. Al contrario, un stile può essere creato che definisce l'aspetto e quindi applicate ai controlli necessari.

## <a name="creating-a-style"></a>Creazione di uno stile

Il [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) classe Raggruppa un insieme di valori di proprietà in un unico oggetto che può essere applicato a più istanze di elemento visivo. Questo consente di ridurre i markup ripetitiva e consente un aspetto delle applicazioni più facilmente da modificare.

Anche se sono stati progettati principalmente per le applicazioni basate su XAML, possono anche essere creati in c#:

- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) le istanze create in XAML vengono in genere definite un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) assegnato al [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) raccolta di un controllo di pagina o al [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/) raccolta dell'applicazione.
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) le istanze create in c# sono in genere definite nella classe della pagina o in una classe che è possibile accedere a livello globale.

Scelta di come definire un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) impatti dove possono essere usato:

- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) le istanze definite a livello di controllo è applicabile solo per il controllo e nei relativi elementi figlio.
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) istanze definite a livello di pagina possono essere applicate solo alla pagina e ai figli.
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) le istanze definite a livello di applicazione possono essere applicate in tutta l'applicazione.

Ogni [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) istanza contiene una raccolta di uno o più [ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/) oggetti, con ogni `Setter` con un [ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/) e un [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/). Il `Property` è il nome della proprietà associabile dell'elemento, viene applicato lo stile e `Value` è il valore che viene applicato alla proprietà.

Ogni [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) istanza può essere *esplicita*, o *implicita*:

- Un *esplicita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) istanza viene definita specificando un [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) e `x:Key` valore e impostando l'elemento di destinazione [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) proprietà per il `x:Key` riferimento. Per ulteriori informazioni su *esplicita* stili, vedere [stili esplicita](~/xamarin-forms/user-interface/styles/explicit.md).
- Un *implicita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) istanza viene definita specificando solo un [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/). Il `Style` istanza verrà automaticamente applicata a tutti gli elementi di quel tipo. Si noti che le sottoclassi del `TargetType` non dispone automaticamente il `Style` applicato. Per ulteriori informazioni su *implicita* stili, vedere [stili impliciti](~/xamarin-forms/user-interface/styles/implicit.md).

Quando si crea un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/), [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) proprietà è sempre obbligatorio. Nell'esempio di codice riportato di seguito viene illustrato un *esplicita* stile (nota la `x:Key`) creato in XAML:

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Per applicare un `Style`, l'oggetto di destinazione deve essere un [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) che corrisponde il [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) valore della proprietà di `Style`, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Gli stili di livello inferiori nella gerarchia di visualizzazione hanno la precedenza su quelli definiti più backup. Ad esempio, impostando un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) che imposta [ `Label.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) a `Red` l'applicazione di livello verrà sottoposto a override da un tipo di livello di pagina che imposta `Label.TextColor` a `Green`. Analogamente, uno stile di livello di pagina verrà sostituito da un tipo di livello di controllo. Inoltre, se `Label.TextColor` è impostato direttamente su una proprietà di controllo, questo ha la precedenza su tutti gli stili.

Gli articoli in questa sezione illustrano e viene illustrato come creare e applicare *esplicita* e *implicita* stili, come creare gli stili globali, applicare uno stile di ereditarietà, come rispondere alle modifiche di stile in fase di esecuzione e come utilizzare gli stili incorporati inclusi in xamarin. Forms.

> [!NOTE]
> **Che cos'è StyleId?**
>
> Prima di xamarin. Forms 2.2, il [ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/) proprietà è stata utilizzata per identificare i singoli elementi in un'applicazione per l'identificazione nel test dell'interfaccia utente e nei motori di tema, ad esempio Pixate. Tuttavia, in cui ha introdotto 2.2 xamarin. Forms il [ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/) proprietà, che ha sostituito il [ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/) proprietà. Per ulteriori informazioni, vedere [automatizzare xamarin. Forms test e Test Cloud UITest](~/xamarin-forms/deploy-test/uitest-and-test-cloud.md).

## <a name="summary"></a>Riepilogo

Xamarin. Forms applicazioni spesso contengono più controlli che hanno un aspetto identico. Impostare l'aspetto di ogni singolo controllo può essere ricorrenti e soggetta a errori. Invece, gli stili possono essere creati che consentono di personalizzare l'aspetto del controllo da proprietà raggruppamento e le impostazioni disponibili sul tipo di controllo.


## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stile](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Metodo set](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
