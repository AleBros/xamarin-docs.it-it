---
title: Introduzione a xamarin. Forms stili
description: Gli stili consentono l'aspetto degli elementi visivi per la personalizzazione. Gli stili vengono definiti per un tipo specifico e contengono i valori per le proprietà disponibili su tale tipo.
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 4048ec78d48b810b39d46fbcb7708860c478cce3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023422"
---
# <a name="introduction-to-xamarinforms-styles"></a>Introduzione a xamarin. Forms stili

_Gli stili consentono l'aspetto degli elementi visivi per la personalizzazione. Gli stili vengono definiti per un tipo specifico e contengono i valori per le proprietà disponibili su tale tipo._

Le applicazioni xamarin. Forms contengono spesso più controlli che hanno un aspetto identico. Ad esempio, un'applicazione può disporre di più [ `Label` ](xref:Xamarin.Forms.Label) istanze che hanno le stesse opzioni del tipo di carattere e le opzioni di layout, come illustrato nell'esempio di codice XAML seguente:

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

L'esempio di codice seguente illustra la pagina equivalente creata in C#:

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

Ciascuna [ `Label` ](xref:Xamarin.Forms.Label) istanza dispone di valori di proprietà identici per controllare l'aspetto del testo visualizzato per il `Label`. Il risultato è l'aspetto illustrato negli screenshot seguenti:

[![](introduction-images/no-styles.png "Etichetta aspetto senza stili")](introduction-images/no-styles-large.png#lightbox "etichettare aspetto senza stili")

Impostazione dell'aspetto di ogni controllo singoli possono essere ricorrenti e tendente all'errore. Al contrario, uno stile può essere creato che definisce l'aspetto e quindi applicati per i controlli necessari.

## <a name="create-a-style"></a>Creare uno stile

Il [ `Style` ](xref:Xamarin.Forms.Style) classe Raggruppa un insieme di valori di proprietà in un oggetto che può quindi essere applicato a più istanze di oggetto visivo. Ciò consente di ridurre markup ripetitiva e consente un aspetto delle applicazioni da modificare più facilmente.

Anche se gli stili sono stati progettati principalmente per le applicazioni basate su XAML, possono anche essere creati nel linguaggio c#:

- [`Style`](xref:Xamarin.Forms.Style) le istanze create in XAML vengono in genere definite un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) che viene assegnato al [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) raccolta di un controllo, pagina o al [ `Resources` ](xref:Xamarin.Forms.Application.Resources) raccolta dell'applicazione.
- [`Style`](xref:Xamarin.Forms.Style) le istanze create nel linguaggio c# sono in genere definite nella classe della pagina, o in una classe che è possibile accedere a livello globale.

La scelta della posizione in cui definire un oggetto [`Style`](xref:Xamarin.Forms.Style) ha effetto sulla posizione in cui può essere usato:

- [`Style`](xref:Xamarin.Forms.Style) le istanze definite a livello di controllo possono essere applicate solo al controllo e ai relativi figli.
- [`Style`](xref:Xamarin.Forms.Style) le istanze definite a livello di pagina possono essere applicate solo alla pagina e ai relativi figli.
- [`Style`](xref:Xamarin.Forms.Style) le istanze definite a livello di applicazione possono essere applicate in tutta l'applicazione.

Ciascuna [ `Style` ](xref:Xamarin.Forms.Style) istanza contiene una raccolta di uno o più [ `Setter` ](xref:Xamarin.Forms.Setter) oggetti, con molti `Setter` aventi un [ `Property` ](xref:Xamarin.Forms.Setter.Property) e un [`Value`](xref:Xamarin.Forms.Setter.Value). Il `Property` è il nome della proprietà associabile dell'elemento, viene applicato lo stile e il `Value` è il valore che viene applicato alla proprietà.

Ciascuna [ `Style` ](xref:Xamarin.Forms.Style) istanza può essere *esplicita*, oppure *implicita*:

- Un' *esplicite* [ `Style` ](xref:Xamarin.Forms.Style) istanza viene definita specificando un [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) e un `x:Key` valore e impostando l'elemento di destinazione [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) proprietà di `x:Key` riferimento. Per altre informazioni sulle *esplicite* stili, vedere [stili espliciti](~/xamarin-forms/user-interface/styles/explicit.md).
- Un' *implicita* [ `Style` ](xref:Xamarin.Forms.Style) istanza viene definita specificando solo un [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType). Il `Style` istanza verrà automaticamente applicata a tutti gli elementi di quel tipo. Si noti che rappresenti le sottoclassi del `TargetType` non hanno automaticamente la `Style` applicato. Per altre informazioni sulle *implicita* stili, vedere [stili impliciti](~/xamarin-forms/user-interface/styles/implicit.md).

Quando si crea una [ `Style` ](xref:Xamarin.Forms.Style), il [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) proprietà è sempre obbligatorio. Il codice seguente esempio illustra un' *esplicita* stile (nota di `x:Key`) creati in XAML:

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Per applicare un `Style`, l'oggetto di destinazione deve essere un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) che corrisponde al [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) valore della proprietà di `Style`, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Stili di livello inferiori nella gerarchia di visualizzazione hanno la precedenza rispetto a quelli definiti più backup. Ad esempio, impostando un [ `Style` ](xref:Xamarin.Forms.Style) che consente di scegliere [ `Label.TextColor` ](xref:Xamarin.Forms.Label.TextColor) a `Red` nell'applicazione livello eseguirà l'override di uno stile a livello di pagina che imposta `Label.TextColor` a `Green`. Analogamente, uno stile a livello di pagina verrà sostituito da uno stile a livello di controllo. Inoltre, se `Label.TextColor` è impostato direttamente su una proprietà di controllo, questo ha la precedenza su tutti gli stili.

Gli articoli in questa sezione illustrano e spiegano come creare e applicare *esplicite* e *implicita* stili, come creare stili globali, applicare uno stile a ereditarietà, come rispondere alle modifiche di stile di visualizzazione in fase di esecuzione e come utilizzare gli stili predefinite inclusi in xamarin. Forms.

> [!NOTE]
> **Che cos'è StyleId?**
>
> Precedenti la 2.2 di xamarin. Forms, il [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) proprietà è stata usata per identificare i singoli elementi in un'applicazione per l'identificazione in test dell'interfaccia utente e in motori di tema, ad esempio Pixate. Tuttavia, ha introdotto xamarin. Forms 2.2 il [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId) proprietà, che ha sostituito il [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) proprietà.

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stile](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
