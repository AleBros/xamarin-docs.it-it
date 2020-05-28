---
title: Introduzione agli Xamarin.Forms stili
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5766af7da3a0cf550a2ccb3a926dad25fd7962eb
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138821"
---
# <a name="introduction-to-xamarinforms-styles"></a>Introduzione agli Xamarin.Forms stili

_Gli stili consentono di personalizzare gli elementi visivi. Gli stili vengono definiti per un tipo specifico e contengono i valori per le proprietà disponibili in tale tipo._

Xamarin.Formsle applicazioni contengono spesso più controlli che hanno un aspetto identico. Ad esempio, un'applicazione può avere più [`Label`](xref:Xamarin.Forms.Label) istanze con le stesse opzioni relative ai tipi di carattere e le stesse opzioni di layout, come illustrato nell'esempio di codice XAML seguente:

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

Ogni [`Label`](xref:Xamarin.Forms.Label) istanza ha valori di proprietà identici per controllare l'aspetto del testo visualizzato da `Label` . Il risultato è l'aspetto illustrato negli screenshot seguenti:

[![Aspetto etichetta senza stili](introduction-images/no-styles.png)](introduction-images/no-styles-large.png#lightbox)

L'impostazione dell'aspetto di ogni singolo controllo può essere ripetitiva e soggetta a errori. È invece possibile creare uno stile che definisca l'aspetto e quindi applicato ai controlli richiesti.

## <a name="create-a-style"></a>Creare uno stile

La [`Style`](xref:Xamarin.Forms.Style) classe raggruppa una raccolta di valori di proprietà in un oggetto che può quindi essere applicato a più istanze di elementi visivi. Questo consente di ridurre il markup ripetitivo e consente di modificare l'aspetto delle applicazioni in modo più semplice.

Anche se gli stili sono stati progettati principalmente per le applicazioni basate su XAML, possono anche essere creati in C#:

- [`Style`](xref:Xamarin.Forms.Style)le istanze create in XAML vengono in genere definite in un oggetto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) assegnato alla [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) raccolta di un controllo, di una pagina o alla [`Resources`](xref:Xamarin.Forms.Application.Resources) raccolta dell'applicazione.
- [`Style`](xref:Xamarin.Forms.Style)le istanze create in C# vengono in genere definite nella classe della pagina o in una classe a cui è possibile accedere a livello globale.

La scelta della posizione in cui definire un [`Style`](xref:Xamarin.Forms.Style) effetto può essere utilizzata:

- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di controllo possono essere applicate solo al controllo e ai relativi elementi figlio.
- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di pagina possono essere applicate solo alla pagina e ai relativi elementi figlio.
- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di applicazione possono essere applicate all'interno dell'applicazione.

Ogni [`Style`](xref:Xamarin.Forms.Style) istanza contiene una raccolta di uno o più [`Setter`](xref:Xamarin.Forms.Setter) oggetti, ognuno dei quali ha `Setter` un oggetto [`Property`](xref:Xamarin.Forms.Setter.Property) e un oggetto [`Value`](xref:Xamarin.Forms.Setter.Value) . `Property` è il nome della proprietà associabile dell'elemento a cui viene applicato lo stile e `Value` è il valore applicato alla proprietà.

Ogni [`Style`](xref:Xamarin.Forms.Style) istanza può essere *esplicita*o *implicita*:

- Un'istanza *esplicita* [`Style`](xref:Xamarin.Forms.Style) viene definita specificando un oggetto [`TargetType`](xref:Xamarin.Forms.Style.TargetType) e un `x:Key` valore e impostando la proprietà dell'elemento di destinazione sul [`Style`](xref:Xamarin.Forms.NavigableElement.Style) `x:Key` riferimento. Per ulteriori informazioni sugli stili *espliciti* , vedere [stili espliciti](~/xamarin-forms/user-interface/styles/explicit.md).
- Un'istanza *implicita* [`Style`](xref:Xamarin.Forms.Style) viene definita specificando solo un [`TargetType`](xref:Xamarin.Forms.Style.TargetType) . L' `Style` istanza verrà quindi applicata automaticamente a tutti gli elementi di quel tipo. Si noti che le sottoclassi di non `TargetType` hanno automaticamente `Style` applicato. Per ulteriori informazioni sugli stili *impliciti* , vedere [stili impliciti](~/xamarin-forms/user-interface/styles/implicit.md).

Quando si crea un oggetto [`Style`](xref:Xamarin.Forms.Style) , la [`TargetType`](xref:Xamarin.Forms.Style.TargetType) proprietà è sempre obbligatoria. Nell'esempio di codice riportato di seguito viene illustrato uno stile *esplicito* (si noti il `x:Key` ) creato in XAML:

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Per applicare un `Style` oggetto, l'oggetto di destinazione deve essere un [`VisualElement`](xref:Xamarin.Forms.VisualElement) che corrisponde al [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valore della proprietà di `Style` , come illustrato nell'esempio di codice XAML seguente:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Gli stili inferiori nella gerarchia di visualizzazione hanno la precedenza su quelli definiti più in alto. Ad esempio, l'impostazione di un oggetto [`Style`](xref:Xamarin.Forms.Style) che imposta [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor) su `Red` a livello di applicazione verrà sottoposta a override da uno stile a livello di pagina che imposta `Label.TextColor` su `Green` . Allo stesso modo, uno stile a livello di pagina verrà sottoposto a override da uno stile del livello di controllo. Inoltre, se `Label.TextColor` è impostato direttamente su una proprietà del controllo, ha la precedenza su qualsiasi stile.

Gli articoli di questa sezione illustrano come creare e applicare stili *espliciti* e *impliciti* , come creare stili globali, ereditarietà dello stile, come rispondere alle modifiche di stile in fase di esecuzione e come usare gli stili incorporati inclusi in Xamarin.Forms .

> [!NOTE]
> **Che cos'è StyleId?**
>
> Nelle versioni precedenti alla Xamarin.Forms 2,2, la [`StyleId`](xref:Xamarin.Forms.Element.StyleId) proprietà è stata usata per identificare i singoli elementi in un'applicazione per l'identificazione nei test dell'interfaccia utente e nei motori di tema, ad esempio Pixate. 2,2, tuttavia, Xamarin.Forms ha introdotto la [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) proprietà, che ha sostituito la [`StyleId`](xref:Xamarin.Forms.Element.StyleId) Proprietà.

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stile](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
