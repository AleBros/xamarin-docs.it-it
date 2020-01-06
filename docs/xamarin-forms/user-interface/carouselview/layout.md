---
title: Layout di Novell. Forms CarouselView
description: Per impostazione predefinita, un CarouselView Visualizza i relativi elementi orizzontalmente. Tuttavia, è anche possibile un orientamento verticale.
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/14/2019
ms.openlocfilehash: 0149a66fedd98a94f1c9d96bf8e7e57715d1b90b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488257"
---
# <a name="xamarinforms-carouselview-layout"></a>Layout di Novell. Forms CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) definisce le proprietà seguenti che controllano il layout:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), di tipo `LinearItemsLayout`, specifica il layout da utilizzare.
- `PeekAreaInsets`, di tipo [`Thickness`](xref:Xamarin.Forms.Thickness), specifica il modo in cui gli elementi adiacenti devono essere parzialmente visibili da.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che le proprietà possono essere destinazioni di associazioni dati.

Per impostazione predefinita, un [`CarouselView`](xref:Xamarin.Forms.CarouselView) visualizzerà gli elementi in un orientamento orizzontale. Sullo schermo verrà visualizzato un singolo elemento, con movimenti di scorrimento che comportano la navigazione in avanti e indietro nella raccolta di elementi. Tuttavia, è anche possibile un orientamento verticale. Ciò è dovuto al fatto che la proprietà [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) è di tipo `LinearItemsLayout`, che eredita dalla classe [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) . La classe `ItemsLayout` definisce le proprietà seguenti:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), di tipo [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation), specifica la direzione in cui il [`CarouselView`](xref:Xamarin.Forms.CarouselView) si espande durante l'aggiunta di elementi.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), di tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), specifica il modo in cui i punti di allineamento sono allineati con gli elementi.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), di tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), specifica il comportamento dei punti di blocco durante lo scorrimento.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che le proprietà possono essere destinazioni di associazioni dati. Per ulteriori informazioni sui punti di aggancio, vedere la pagina relativa ai [punti](scrolling.md#snap-points) di blocco nella Guida allo [scorrimento di Novell. Forms](scrolling.md) .

L'enumerazione [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) definisce i membri seguenti:

- `Vertical` indica che l' [`CarouselView`](xref:Xamarin.Forms.CarouselView) verrà espansa verticalmente quando vengono aggiunti elementi.
- `Horizontal` indica che l' [`CarouselView`](xref:Xamarin.Forms.CarouselView) verrà espansa orizzontalmente Man seconda che gli elementi vengono aggiunti.

La classe `LinearItemsLayout` eredita dalla classe [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) e definisce una proprietà `ItemSpacing`, di tipo `double`, che rappresenta lo spazio vuoto intorno a ogni elemento. Il valore predefinito di questa proprietà è 0 e il relativo valore deve essere sempre maggiore o uguale a 0. La classe `LinearItemsLayout` definisce inoltre i membri `Vertical` e `Horizontal` statici. Questi membri possono essere utilizzati rispettivamente per creare elenchi verticali o orizzontali. In alternativa, è possibile creare un oggetto `LinearItemsLayout`, specificando un [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro di enumerazione come argomento.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) usa i motori di layout nativi per eseguire il layout.

## <a name="horizontal-layout"></a>Layout orizzontale

Per impostazione predefinita, [`CarouselView`](xref:Xamarin.Forms.CarouselView) Visualizza i relativi elementi orizzontalmente. Non è quindi necessario impostare la proprietà [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) per utilizzare questo layout:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Frame HasShadow="True"
                       BorderColor="DarkGray"
                       CornerRadius="5"
                       Margin="20"
                       HeightRequest="300"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand">
                    <StackLayout>
                        <Label Text="{Binding Name}"
                               FontAttributes="Bold"
                               FontSize="Large"
                               HorizontalOptions="Center"
                               VerticalOptions="Center" />
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="150"
                               WidthRequest="150"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Location}"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Details}"
                               FontAttributes="Italic"
                               HorizontalOptions="Center"
                               MaxLines="5"
                               LineBreakMode="TailTruncation" />
                    </StackLayout>
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

In alternativa, questo layout può essere eseguito anche impostando la proprietà [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) su un oggetto `LinearItemsLayout`, specificando il `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro di enumerazione come valore della proprietà `Orientation`:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = LinearItemsLayout.Horizontal
};
```

In questo modo si ottiene un layout che aumenta orizzontalmente Man mano che vengono aggiunti nuovi elementi.

## <a name="vertical-layout"></a>Layout verticale

[`CarouselView`](xref:Xamarin.Forms.CarouselView) possibile visualizzare verticalmente gli elementi impostando la proprietà [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) su un oggetto `LinearItemsLayout`, specificando il `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro di enumerazione come valore della proprietà `Orientation`:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical" />
    </CarouselView.ItemsLayout>
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Frame HasShadow="True"
                       BorderColor="DarkGray"
                       CornerRadius="5"
                       Margin="20"
                       HeightRequest="300"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand">
                    <StackLayout>
                        <Label Text="{Binding Name}"
                               FontAttributes="Bold"
                               FontSize="Large"
                               HorizontalOptions="Center"
                               VerticalOptions="Center" />
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="150"
                               WidthRequest="150"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Location}"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Details}"
                               FontAttributes="Italic"
                               HorizontalOptions="Center"
                               MaxLines="5"
                               LineBreakMode="TailTruncation" />
                    </StackLayout>
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = LinearItemsLayout.Vertical
};
```

In questo modo si ottiene un layout che cresce verticalmente Man mano che vengono aggiunti nuovi elementi.

## <a name="partially-visible-adjacent-items"></a>Elementi adiacenti parzialmente visibili

Per impostazione predefinita, [`CarouselView`](xref:Xamarin.Forms.CarouselView) Visualizza gli elementi completi in una sola volta. Tuttavia, questo comportamento può essere modificato impostando la proprietà `PeekAreaInsets` su un valore `Thickness` che specifica la quantità di elementi adiacenti parzialmente visibili da. Questa operazione può essere utile per indicare agli utenti che sono presenti altri elementi da visualizzare. Il codice XAML seguente mostra un esempio di impostazione di questa proprietà:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    ...
</CarouselView>
```

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    PeekAreaInsets = new Thickness(100)
};
```

Il risultato è che gli elementi adiacenti vengono esposti parzialmente sullo schermo.

## <a name="item-spacing"></a>Spaziatura elementi

Per impostazione predefinita, ogni elemento in un [`CarouselView`](xref:Xamarin.Forms.CarouselView) non dispone di alcuno spazio vuoto intorno a esso. Questo comportamento può essere modificato impostando le proprietà nel layout degli elementi utilizzato dal `CarouselView`.

Quando un [`CarouselView`](xref:Xamarin.Forms.CarouselView) imposta la relativa proprietà [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) su un oggetto `LinearItemsLayout`, la proprietà `LinearItemsLayout.ItemSpacing` può essere impostata su un valore `double` che rappresenta lo spazio vuoto intorno a ogni elemento:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="20" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

> [!NOTE]
> La proprietà `LinearItemsLayout.ItemSpacing` dispone di un set di callback di convalida, che assicura che il valore della proprietà sia sempre maggiore o uguale a 0.

Il codice C# equivalente è il seguente:

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

Questo codice genera un layout verticale con una spaziatura di 20 intorno a ogni elemento.

## <a name="dynamic-resizing-of-items"></a>Ridimensionamento dinamico di elementi

Gli elementi di un [`CarouselView`](xref:Xamarin.Forms.CarouselView) possono essere ridimensionati dinamicamente in fase di esecuzione modificando le proprietà correlate al layout degli elementi all'interno del [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Nell'esempio di codice seguente, ad esempio, vengono modificate le proprietà [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) e [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) di un oggetto [`Image`](xref:Xamarin.Forms.Image) e la proprietà `HeightRequest` del relativo [`Frame`](xref:Xamarin.Forms.Frame)padre:

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(150) ? 200 : 150;
    Frame frame = ((Frame)image.Parent.Parent);
    frame.HeightRequest = frame.HeightRequest.Equals(300) ? 350 : 300;
}
```

Il gestore dell'evento `OnImageTapped` viene eseguito in risposta a un oggetto [`Image`](xref:Xamarin.Forms.Image) da toccare e modifica le dimensioni dell'immagine e il relativo frame padre, in modo che sia più semplice da visualizzare.

## <a name="right-to-left-layout"></a>Layout da destra a sinistra

[`CarouselView`](xref:Xamarin.Forms.CarouselView) possibile layout del contenuto in una direzione di flusso da destra a sinistra impostando la relativa proprietà [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) su [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft). Tuttavia, la proprietà `FlowDirection` dovrebbe essere impostata idealmente in una pagina o in un layout radice, causando la risposta alla direzione del flusso di tutti gli elementi all'interno della pagina o del layout radice:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CarouselViewDemos.Views.HorizontalTemplateLayoutRTLPage"
             Title="Horizontal layout (RTL FlowDirection)"
             FlowDirection="RightToLeft">    
    <CarouselView ItemsSource="{Binding Monkeys}">
        ...
    </CarouselView>
</ContentPage>
```

Il [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) predefinito per un elemento con un elemento padre è [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Il [`CarouselView`](xref:Xamarin.Forms.CarouselView) eredita pertanto il valore della proprietà `FlowDirection` dal [`ContentPage`](xref:Xamarin.Forms.ContentPage).

Per altre informazioni sulla direzione del flusso, vedere [localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Collegamenti correlati

- [CarouselView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Scorrimento CarouselView Novell. Forms](scrolling.md)
