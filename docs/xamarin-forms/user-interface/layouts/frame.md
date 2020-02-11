---
title: Frame Xamarin.Forms
description: La classe frame Xamarin.Forms è un layout utilizzato per eseguire il wrapping di una vista o di un layout con un bordo che può essere configurato con il colore, l'ombreggiatura e altre opzioni.
ms.prod: xamarin
ms.assetId: 4E074714-0928-41C8-A468-B60E23236A8C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/06/2019
ms.openlocfilehash: 619b29a9d65594b1badd805c3361fe1a174d7174
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "69976493"
---
# <a name="xamarinforms-frame"></a>Frame Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)

La classe [`Frame`](xref:Xamarin.Forms.Frame) Xamarin.Forms è un layout utilizzato per eseguire il wrapping di una visualizzazione con un bordo che può essere configurato con il colore, l'ombreggiatura e altre opzioni. I frame vengono comunemente usati per creare bordi intorno ai controlli, ma possono essere usati per creare un'interfaccia utente più complessa. Per ulteriori informazioni, vedere [utilizzo avanzato dei frame](#advanced-frame-usage).

Lo screenshot seguente mostra `Frame` controlli in iOS e Android:

[![esempi di frame in iOS e Android](frame-images/frame-cropped.png)](frame-images/frame-full.png#lightbox "Esempi di frame in iOS e Android")

La classe `Frame` definisce le proprietà seguenti:

* [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor) è un valore `Color` che determina il colore del bordo della `Frame`.
* [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius) è un valore `float` che determina il raggio arrotondato dell'angolo.
* [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) è un valore `bool` che determina se il frame ha un'ombreggiatura.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che l'`Frame` può essere la destinazione di associazioni dati.

> [!NOTE]
> Il comportamento della proprietà `HasShadow` è dipendente dalla piattaforma. Il valore predefinito è `true` in tutte le piattaforme. Non viene tuttavia eseguito il rendering delle ombreggiature UWP. Le ombreggiature vengono visualizzate in Android e iOS, ma le ombreggiature in iOS sono più scure e occupano più spazio.

## <a name="create-a-frame"></a>Creazione di un frame

È possibile creare un'istanza di un `Frame` in XAML. L'oggetto `Frame` predefinito ha uno sfondo bianco, un'ombreggiatura e nessun bordo. Un oggetto `Frame` in genere esegue il wrapping di un altro controllo. Nell'esempio seguente viene illustrato un `Frame` predefinito per il wrapping di un oggetto `Label`:

```xaml
<Frame>
  <Label Text="Example" />
</Frame>
```

Nel codice è anche possibile creare un `Frame`:

```csharp
Frame defaultFrame = new Frame
{
    Content = new Label { Text = "Example" }
};
```

`Frame` gli oggetti possono essere personalizzati con angoli arrotondati, bordi colorati e ombreggiature, impostando le proprietà in XAML. Nell'esempio seguente viene illustrato un oggetto `Frame` personalizzato:

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

La classe `Frame` eredita da `ContentView`, ovvero può contenere qualsiasi tipo di oggetto `View`, inclusi `Layout` oggetti. Questa possibilità consente di usare la `Frame` per creare oggetti dell'interfaccia utente complessi, ad esempio le schede.

### <a name="create-a-card-with-a-frame"></a>Creare una scheda con un frame

La combinazione di un oggetto `Frame` con un oggetto `Layout`, ad esempio un oggetto `StackLayout`, consente la creazione di interfacce utente più complesse. Lo screenshot seguente mostra una scheda di esempio, creata usando un oggetto `Frame`:

[![screenshot di una scheda creata con un frame](frame-images/frame-card-cropped.png)](frame-images/frame-full.png#lightbox "Screenshot di una scheda creata con un frame")

Il codice XAML seguente mostra come creare una scheda con la classe `Frame`:

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

Per creare un'immagine del cerchio, è possibile usare la proprietà `CornerRadius` del controllo `Frame`. Lo screenshot seguente mostra un esempio di immagine rotonda, creata usando un oggetto `Frame`:

[![ "screenshot di un'immagine del cerchio creata con un frame"](frame-images/circle-image-cropped.png)](frame-images/frame-full.png#lightbox "Screenshot di un'immagine del cerchio creata con un frame")

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

È necessario aggiungere l'immagine **outdoor. jpg** a ogni progetto di piattaforma e il modo in cui questa operazione viene eseguita varia in base alla piattaforma. Per altre informazioni, vedere [Immagini in Xamarin.Forms](~/xamarin-forms/user-interface/images.md).

> [!NOTE]
> Gli angoli arrotondati hanno un comportamento leggermente diverso nelle diverse piattaforme. Il `Margin` dell'oggetto `Image` deve essere la metà della differenza tra la larghezza dell'immagine e la larghezza del fotogramma padre e deve essere negativo per centrare uniformemente l'immagine all'interno dell'oggetto `Frame`. Tuttavia, la larghezza e l'altezza richieste non sono garantite, quindi le proprietà `Margin`, `HeightRequest` e `WidthRequest` potrebbero essere modificate in base alle dimensioni dell'immagine e ad altre opzioni di layout.

## <a name="related-links"></a>Collegamenti correlati

* [Demo frame](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)
* [Immagini in Xamarin.Forms](~/xamarin-forms/user-interface/images.md)
