---
title: Introduzione agli stili di Novell. Forms
description: Gli stili consentono di personalizzare gli elementi visivi. Gli stili vengono definiti per un tipo specifico e contengono i valori per le proprietà disponibili in tale tipo.
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 35f8dad3590c07ceb3c93aa735b8c02d75098498
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "70228167"
---
# <a name="introduction-to-xamarinforms-styles"></a>Introduzione agli stili di Novell. Forms

_Gli stili consentono di personalizzare gli elementi visivi. Gli stili vengono definiti per un tipo specifico e contengono i valori per le proprietà disponibili in tale tipo._

Le applicazioni Novell. Forms spesso contengono più controlli che hanno un aspetto identico. Ad esempio, un'applicazione può avere più [`Label`](xref:Xamarin.Forms.Label) istanze con le stesse opzioni relative ai tipi di carattere e le stesse opzioni di layout, come illustrato nell'esempio di codice XAML seguente:

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

Ogni [`Label`](xref:Xamarin.Forms.Label) istanza ha valori di proprietà identici per controllare l'aspetto del testo visualizzato `Label`da. Il risultato è l'aspetto illustrato negli screenshot seguenti:

[![Aspetto etichetta senza stili](introduction-images/no-styles.png)](introduction-images/no-styles-large.png#lightbox)

L'impostazione dell'aspetto di ogni singolo controllo può essere ripetitiva e soggetta a errori. È invece possibile creare uno stile che definisca l'aspetto e quindi applicato ai controlli richiesti.

## <a name="create-a-style"></a>Creare uno stile

La [`Style`](xref:Xamarin.Forms.Style) classe raggruppa una raccolta di valori di proprietà in un oggetto che può quindi essere applicato a più istanze di elementi visivi. Questo consente di ridurre il markup ripetitivo e consente di modificare l'aspetto delle applicazioni in modo più semplice.

Sebbene gli stili siano stati progettati principalmente per le applicazioni basate su XAML, possono essere creati C#anche in:

- [`Style`](xref:Xamarin.Forms.Style)le [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) [istanzecreateinXAMLvengonoingeneredefiniteinunoggettoassegnatoallaraccoltadiuncontrollo,diunapaginaoallaraccoltadell'applicazione.`Resources`](xref:Xamarin.Forms.Application.Resources)
- [`Style`](xref:Xamarin.Forms.Style)le istanze create C# in vengono in genere definite nella classe della pagina o in una classe a cui è possibile accedere a livello globale.

La scelta della posizione in cui definire un oggetto [`Style`](xref:Xamarin.Forms.Style) ha effetto sulla posizione in cui può essere usato:

- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di controllo possono essere applicate solo al controllo e ai relativi elementi figlio.
- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di pagina possono essere applicate solo alla pagina e ai relativi elementi figlio.
- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di applicazione possono essere applicate all'interno dell'applicazione.

Ogni [`Style`](xref:Xamarin.Forms.Style) istanza contiene una raccolta di uno o più [`Setter`](xref:Xamarin.Forms.Setter) oggetti, ognuno `Setter` dei quali ha [`Property`](xref:Xamarin.Forms.Setter.Property) un oggetto [`Value`](xref:Xamarin.Forms.Setter.Value)e un oggetto. È il nome della proprietà associabile dell'elemento a cui viene applicato lo stile `Value` e è il valore applicato alla proprietà. `Property`

Ogni [`Style`](xref:Xamarin.Forms.Style) istanza può essere *esplicita*o *implicita*:

- Un'istanza *esplicita* [`Style`](xref:Xamarin.Forms.Style) viene definita specificando [`TargetType`](xref:Xamarin.Forms.Style.TargetType) un oggetto `x:Key` e un valore `x:Key` e impostando la [`Style`](xref:Xamarin.Forms.NavigableElement.Style) proprietà dell'elemento di destinazione sul riferimento. Per ulteriori informazioni sugli stili *espliciti* , vedere [stili espliciti](~/xamarin-forms/user-interface/styles/explicit.md).
- Un'istanza *implicita* [`Style`](xref:Xamarin.Forms.Style) viene definita specificando solo [`TargetType`](xref:Xamarin.Forms.Style.TargetType)un. L' `Style` istanza verrà quindi applicata automaticamente a tutti gli elementi di quel tipo. Si noti che le `TargetType` `Style` sottoclassi di non hanno automaticamente applicato. Per ulteriori informazioni sugli stili *impliciti* , vedere [stili impliciti](~/xamarin-forms/user-interface/styles/implicit.md).

Quando si crea [`Style`](xref:Xamarin.Forms.Style)un oggetto [`TargetType`](xref:Xamarin.Forms.Style.TargetType) , la proprietà è sempre obbligatoria. Nell'esempio di codice riportato di seguito viene illustrato uno stile `x:Key` *esplicito* (si noti il) creato in XAML:

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Per applicare un `Style`oggetto, l'oggetto di destinazione deve [`VisualElement`](xref:Xamarin.Forms.VisualElement) essere un che [`TargetType`](xref:Xamarin.Forms.Style.TargetType) corrisponde `Style`al valore della proprietà di, come illustrato nell'esempio di codice XAML seguente:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Stili di livello inferiori nella gerarchia di visualizzazione hanno la precedenza rispetto a quelli definiti più backup. Ad esempio, impostando un [ `Style` ](xref:Xamarin.Forms.Style) che consente di scegliere [ `Label.TextColor` ](xref:Xamarin.Forms.Label.TextColor) a `Red` nell'applicazione livello eseguirà l'override di uno stile a livello di pagina che imposta `Label.TextColor` a `Green`. Analogamente, uno stile a livello di pagina verrà sostituito da uno stile a livello di controllo. Inoltre, se `Label.TextColor` è impostato direttamente su una proprietà del controllo, ha la precedenza su qualsiasi stile.

Gli articoli di questa sezione illustrano come creare e applicare stili *espliciti* e *impliciti* , come creare stili globali, ereditarietà dello stile, come rispondere alle modifiche di stile in fase di esecuzione e come usare gli stili incorporati inclusi in Novell. Forms.

> [!NOTE]
> **Che cos'è StyleId?**
>
> Prima di Novell. Forms 2,2, [`StyleId`](xref:Xamarin.Forms.Element.StyleId) la proprietà è stata usata per identificare singoli elementi in un'applicazione per l'identificazione nei test dell'interfaccia utente e nei motori di tema come Pixate. Tuttavia, Novell. Forms 2,2 ha [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) introdotto la proprietà, che ha sostituito [`StyleId`](xref:Xamarin.Forms.Element.StyleId) la proprietà.

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Stile](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
