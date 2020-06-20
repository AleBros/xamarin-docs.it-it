---
title: Xamarin.FormsFrame
description: La Xamarin.Forms classe frame è un layout utilizzato per eseguire il wrapping di una visualizzazione o di un layout con un bordo che può essere configurato con colore, ombreggiatura e altre opzioni.
ms.prod: xamarin
ms.assetId: 4E074714-0928-41C8-A468-B60E23236A8C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 42192111befbefda7e0f62b7691a8392c2828818
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137189"
---
# <a name="xamarinforms-frame"></a>Xamarin.FormsFrame

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)

La Xamarin.Forms [`Frame`](xref:Xamarin.Forms.Frame) classe è un layout utilizzato per eseguire il wrapping di una visualizzazione con un bordo che può essere configurato con colore, ombreggiatura e altre opzioni. I frame vengono comunemente usati per creare bordi intorno ai controlli, ma possono essere usati per creare un'interfaccia utente più complessa. Per ulteriori informazioni, vedere [utilizzo avanzato dei frame](#advanced-frame-usage).

La schermata seguente mostra i `Frame` controlli in iOS e Android:

[!["Esempi di frame su iOS e Android"](frame-images/frame-cropped.png)](frame-images/frame-full.png#lightbox "Esempi di frame in iOS e Android")

La `Frame` classe definisce le proprietà seguenti:

* [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor)`Color`valore che determina il colore del `Frame` bordo.
* [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius)è un `float` valore che determina il raggio arrotondato dell'angolo.
* [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow)`bool`valore che determina se il frame dispone di un'ombreggiatura.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che `Frame` può essere la destinazione di associazioni dati.

> [!NOTE]
> Il `HasShadow` comportamento della proprietà è dipendente dalla piattaforma. Il valore predefinito è `true` su tutte le piattaforme. Non viene tuttavia eseguito il rendering delle ombreggiature UWP. Le ombreggiature vengono visualizzate in Android e iOS, ma le ombreggiature in iOS sono più scure e occupano più spazio.

## <a name="create-a-frame"></a>Creazione di un frame

`Frame`È possibile creare un'istanza di un oggetto in XAML. L' `Frame` oggetto predefinito ha uno sfondo bianco, un'ombreggiatura e nessun bordo. Un `Frame` oggetto in genere esegue il wrapping di un altro controllo. Nell'esempio seguente viene illustrata un'impostazione predefinita `Frame` di wrapping di un `Label` oggetto:

```xaml
<Frame>
  <Label Text="Example" />
</Frame>
```

Un oggetto `Frame` può essere creato anche nel codice:

```csharp
Frame defaultFrame = new Frame
{
    Content = new Label { Text = "Example" }
};
```

`Frame`gli oggetti possono essere personalizzati con angoli arrotondati, bordi colorati ed ombreggiatura impostando le proprietà in XAML. Nell'esempio seguente viene illustrato un `Frame` oggetto personalizzato:

```xaml
<Frame BorderColor="Orange"
       CornerRadius="10"
       HasShadow="True">
  <Label Text="Example" />
</Frame>
```

Queste proprietà dell'istanza possono essere impostate anche nel codice:

```csharp
Frame frame = new Frame
{
    BorderColor = Color.Orange,
    CornerRadius = 10,
    HasShadow = true,
    Content = new Label { Text = "Example" }
};
```

## <a name="advanced-frame-usage"></a>Utilizzo avanzato del frame

La `Frame` classe eredita da `ContentView` , il che significa che può contenere qualsiasi tipo di `View` oggetto, inclusi `Layout` gli oggetti. Questa possibilità consente di `Frame` usare l'oggetto per creare oggetti dell'interfaccia utente complessi, ad esempio le schede.

### <a name="create-a-card-with-a-frame"></a>Creare una scheda con un frame

La combinazione `Frame` di un oggetto con un `Layout` oggetto, ad esempio un `StackLayout` oggetto, consente la creazione di interfacce utente più complesse. La schermata seguente mostra una scheda di esempio, creata usando un `Frame` oggetto:

[!["Screenshot di una scheda creata con un frame"](frame-images/frame-card-cropped.png)](frame-images/frame-full.png#lightbox "Screenshot di una scheda creata con un frame")

Il codice XAML seguente mostra come creare una scheda con la `Frame` classe:

```xaml
<Frame BorderColor="Gray"
       CornerRadius="5"
       Padding="8">
  <StackLayout>
    <Label Text="Card Example"
           FontSize="Medium"
           FontAttributes="Bold" />
    <BoxView Color="Gray"
             HeightRequest="2"
             HorizontalOptions="Fill" />
    <Label Text="Frames can wrap more complex layouts to create more complex UI components, such as this card!"/>
  </StackLayout>
</Frame>
```

È anche possibile creare una scheda nel codice:

```csharp
Frame cardFrame = new Frame
{
    BorderColor = Color.Gray,
    CornerRadius = 5,
    Padding = 8,
    Content = new StackLayout
    {
        Children =
        {
            new Label
            {
                Text = "Card Example",
                FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(Label)),
                FontAttributes = FontAttributes.Bold
            },
            new BoxView
            {
                Color = Color.Gray,
                HeightRequest = 2,
                HorizontalOptions = LayoutOptions.Fill
            },
            new Label
            {
                Text = "Frames can wrap more complex layouts to create more complex UI components, such as this card!"
            }
        }
    }
};
```

### <a name="round-elements"></a>Elementi rotondi

La `CornerRadius` proprietà del `Frame` controllo può essere usata per creare un'immagine del cerchio. Lo screenshot seguente mostra un esempio di immagine rotonda, creata usando un `Frame` oggetto:

[!["Screenshot di un'immagine Circle creata con un frame"](frame-images/circle-image-cropped.png)](frame-images/frame-full.png#lightbox "Screenshot di un'immagine del cerchio creata con un frame")

Il codice XAML seguente illustra come creare un'immagine Circle in XAML:

```xaml
<Frame Margin="10"
       BorderColor="Black"
       CornerRadius="50"
       HeightRequest="60"
       WidthRequest="60"
       IsClippedToBounds="True"
       HorizontalOptions="Center"
       VerticalOptions="Center">
  <Image Source="outdoors.jpg"
         Aspect="AspectFill"
         Margin="-20"
         HeightRequest="100"
         WidthRequest="100" />
</Frame>
```

È anche possibile creare un'immagine Circle nel codice:

```csharp
Frame circleImageFrame = new Frame
{
    Margin = 10,
    BorderColor = Color.Black,
    CornerRadius = 50,
    HeightRequest = 60,
    WidthRequest = 60,
    IsClippedToBounds = true,
    HorizontalOptions = LayoutOptions.Center,
    VerticalOptions = LayoutOptions.Center,
    Content = new Image
    {
        Source = ImageSource.FromFile("outdoors.jpg"),
        Aspect = Aspect.AspectFill,
        Margin = -20,
        HeightRequest = 100,
        WidthRequest = 100
    }
};
```

È necessario aggiungere l'immagine **outdoors.jpg** a ogni progetto di piattaforma e il modo in cui questa operazione viene eseguita varia in base alla piattaforma. Per ulteriori informazioni, vedere [Immagini in Xamarin.Forms ](~/xamarin-forms/user-interface/images.md).

> [!NOTE]
> Gli angoli arrotondati hanno un comportamento leggermente diverso nelle diverse piattaforme. Il valore dell' `Image` oggetto `Margin` dovrebbe essere la metà della differenza tra la larghezza dell'immagine e la larghezza del frame padre e deve essere negativo per centrare uniformemente l'immagine all'interno dell' `Frame` oggetto. Tuttavia, la larghezza e l'altezza richieste non sono garantite, pertanto `Margin` `HeightRequest` `WidthRequest` potrebbe essere necessario modificare le proprietà e in base alla dimensione dell'immagine e ad altre opzioni di layout.

## <a name="related-links"></a>Collegamenti correlati

* [Demo frame](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)
* [Immagini inXamarin.Forms](~/xamarin-forms/user-interface/images.md)
