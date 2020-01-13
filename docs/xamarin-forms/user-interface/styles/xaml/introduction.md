---
title: Introduzione agli stili di Xamarin.Forms
description: Gli stili consentono di personalizzare gli elementi visivi. Gli stili vengono definiti per un tipo specifico e contengono i valori per le proprietà disponibili in tale tipo.
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 35f8dad3590c07ceb3c93aa735b8c02d75098498
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70228167"
---
# <a name="introduction-to-xamarinforms-styles"></a>Introduzione agli stili di Xamarin.Forms

_Gli stili consentono di personalizzare gli elementi visivi. Gli stili vengono definiti per un tipo specifico e contengono i valori per le proprietà disponibili in tale tipo._

Le applicazioni Xamarin.Forms spesso contengono più controlli che hanno un aspetto identico. Ad esempio, un'applicazione può avere più istanze di [`Label`](xref:Xamarin.Forms.Label) con le stesse opzioni relative ai tipi di carattere e le stesse opzioni di layout, come illustrato nell'esempio di codice XAML seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="Styles.NoStylesPage"
    Title="No Styles"
    IconImageSource="xaml.png">
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
        IconImageSource = "csharp.png";
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

Ogni istanza di [`Label`](xref:Xamarin.Forms.Label) dispone di valori di proprietà identici per controllare l'aspetto del testo visualizzato dall'`Label`. Il risultato è l'aspetto illustrato negli screenshot seguenti:

[![Label aspetto senza stili](introduction-images/no-styles.png)](introduction-images/no-styles-large.png#lightbox)

L'impostazione dell'aspetto di ogni singolo controllo può essere ripetitiva e soggetta a errori. È invece possibile creare uno stile che definisca l'aspetto e quindi applicato ai controlli richiesti.

## <a name="create-a-style"></a>Creare uno stile

La classe [`Style`](xref:Xamarin.Forms.Style) raggruppa una raccolta di valori di proprietà in un oggetto che può quindi essere applicato a più istanze di elementi visivi. Questo consente di ridurre il markup ripetitivo e consente di modificare l'aspetto delle applicazioni in modo più semplice.

Sebbene gli stili siano stati progettati principalmente per le applicazioni basate su XAML, possono essere creati C#anche in:

- [`Style`](xref:Xamarin.Forms.Style) istanze create in XAML vengono in genere definite in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) assegnato alla raccolta di [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) di un controllo, una pagina o alla raccolta [`Resources`](xref:Xamarin.Forms.Application.Resources) dell'applicazione.
- [`Style`](xref:Xamarin.Forms.Style) istanze create in C# vengono in genere definite nella classe della pagina o in una classe a cui è possibile accedere a livello globale.

La scelta della posizione in cui definire un oggetto [`Style`](xref:Xamarin.Forms.Style) ha effetto sulla posizione in cui può essere usato:

- [`Style`](xref:Xamarin.Forms.Style) istanze definite a livello di controllo possono essere applicate solo al controllo e ai relativi elementi figlio.
- [`Style`](xref:Xamarin.Forms.Style) istanze definite a livello di pagina possono essere applicate solo alla pagina e ai relativi elementi figlio.
- [`Style`](xref:Xamarin.Forms.Style) istanze definite a livello di applicazione possono essere applicate all'interno dell'applicazione.

Ogni istanza di [`Style`](xref:Xamarin.Forms.Style) contiene una raccolta di uno o più oggetti [`Setter`](xref:Xamarin.Forms.Setter) , `Setter` ognuno dei quali ha un [`Property`](xref:Xamarin.Forms.Setter.Property) e un [`Value`](xref:Xamarin.Forms.Setter.Value). Il `Property` è il nome della proprietà associabile dell'elemento a cui viene applicato lo stile e il `Value` è il valore applicato alla proprietà.

Ogni istanza di [`Style`](xref:Xamarin.Forms.Style) può essere *esplicita*o *implicita*:

- Un'istanza di [`Style`](xref:Xamarin.Forms.Style) *esplicita* viene definita specificando una [`TargetType`](xref:Xamarin.Forms.Style.TargetType) e un valore `x:Key` e impostando la proprietà [`Style`](xref:Xamarin.Forms.NavigableElement.Style) dell'elemento di destinazione sul riferimento `x:Key`. Per ulteriori informazioni sugli stili *espliciti* , vedere [stili espliciti](~/xamarin-forms/user-interface/styles/explicit.md).
- Un'istanza di [`Style`](xref:Xamarin.Forms.Style) *implicita* viene definita specificando solo una [`TargetType`](xref:Xamarin.Forms.Style.TargetType). L'istanza di `Style` verrà quindi applicata automaticamente a tutti gli elementi di quel tipo. Si noti che le sottoclassi dell'`TargetType` non hanno automaticamente l'`Style` applicato. Per ulteriori informazioni sugli stili *impliciti* , vedere [stili impliciti](~/xamarin-forms/user-interface/styles/implicit.md).

Quando si crea una [`Style`](xref:Xamarin.Forms.Style), la proprietà [`TargetType`](xref:Xamarin.Forms.Style.TargetType) è sempre obbligatoria. L'esempio di codice seguente mostra uno stile *esplicito* (si noti la `x:Key`) creato in XAML:

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Per applicare una `Style`, l'oggetto di destinazione deve essere un [`VisualElement`](xref:Xamarin.Forms.VisualElement) corrispondente al valore della proprietà [`TargetType`](xref:Xamarin.Forms.Style.TargetType) della `Style`, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Gli stili inferiori nella gerarchia di visualizzazione hanno la precedenza su quelli definiti più in alto. Se, ad esempio, si imposta un [`Style`](xref:Xamarin.Forms.Style) che imposta [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor) su `Red` a livello di applicazione verrà sostituito da uno stile a livello di pagina che imposta `Label.TextColor` su `Green`. Allo stesso modo, uno stile a livello di pagina verrà sottoposto a override da uno stile del livello di controllo. Inoltre, se `Label.TextColor` è impostato direttamente su una proprietà del controllo, avrà la precedenza su qualsiasi stile.

Gli articoli di questa sezione illustrano come creare e applicare stili *espliciti* e *impliciti* , come creare stili globali, ereditarietà dello stile, come rispondere alle modifiche di stile in fase di esecuzione e come usare gli stili incorporati inclusi in Xamarin.Forms.

> [!NOTE]
> **Che cos'è StyleId?**
>
> Prima di Xamarin.Forms 2,2, la proprietà [`StyleId`](xref:Xamarin.Forms.Element.StyleId) è stata usata per identificare i singoli elementi in un'applicazione per l'identificazione nei test dell'interfaccia utente e nei motori di tema come Pixate. Tuttavia, in Xamarin.Forms 2,2 è stata introdotta la proprietà [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) , che ha sostituito la proprietà [`StyleId`](xref:Xamarin.Forms.Element.StyleId) .

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stile](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
