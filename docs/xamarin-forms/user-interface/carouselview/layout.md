---
title: Layout di Novell. Forms CarouselView
description: Per impostazione predefinita, un CarouselView Visualizza i relativi elementi orizzontalmente. Tuttavia, è anche possibile un orientamento verticale.
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/28/2020
ms.openlocfilehash: 3242148fa97e6b3795b57b2fed86f3643a5ecdf6
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517417"
---
# <a name="xamarinforms-carouselview-layout"></a>Layout di Novell. Forms CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)definisce le proprietà seguenti che controllano il layout:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), di tipo `LinearItemsLayout`, specifica il layout da utilizzare.
- `PeekAreaInsets`, di tipo [`Thickness`](xref:Xamarin.Forms.Thickness), specifica il modo in cui gli elementi adiacenti devono essere parzialmente visibili da.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati.

Per impostazione predefinita, [`CarouselView`](xref:Xamarin.Forms.CarouselView) un oggetto visualizzerà gli elementi in un orientamento orizzontale. Sullo schermo verrà visualizzato un singolo elemento, con movimenti di scorrimento che comportano la navigazione in avanti e indietro nella raccolta di elementi. Tuttavia, è anche possibile un orientamento verticale. Questa condizione è dovuta al fatto [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) che `LinearItemsLayout`la proprietà è di tipo [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) , che eredita dalla classe. La `ItemsLayout` classe definisce le proprietà seguenti:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), di tipo [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation), specifica la direzione in cui l' [`CarouselView`](xref:Xamarin.Forms.CarouselView) oggetto viene espanso durante l'aggiunta di elementi.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), di tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), specifica il modo in cui i punti di allineamento sono allineati con gli elementi.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), di tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), specifica il comportamento dei punti di blocco durante lo scorrimento.

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che le proprietà possono essere destinazioni di associazioni dati. Per ulteriori informazioni sui punti di aggancio, vedere la pagina relativa ai [punti](scrolling.md#snap-points) di blocco nella Guida allo [scorrimento di Novell. Forms](scrolling.md) .

L' [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) enumerazione definisce i membri seguenti:

- `Vertical`indica che l' [`CarouselView`](xref:Xamarin.Forms.CarouselView) oggetto verrà espanso verticalmente quando vengono aggiunti elementi.
- `Horizontal`indica che l' [`CarouselView`](xref:Xamarin.Forms.CarouselView) oggetto si espande orizzontalmente quando vengono aggiunti elementi.

La `LinearItemsLayout` classe eredita dalla [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) classe e definisce una `ItemSpacing` proprietà di tipo `double`, che rappresenta lo spazio vuoto intorno a ogni elemento. Il valore predefinito di questa proprietà è 0 e il relativo valore deve essere sempre maggiore o uguale a 0. La `LinearItemsLayout` classe definisce anche membri `Vertical` statici `Horizontal` e. Questi membri possono essere utilizzati rispettivamente per creare elenchi verticali o orizzontali. In alternativa, è `LinearItemsLayout` possibile creare un oggetto, specificando [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) un membro di enumerazione come argomento.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)USA i motori di layout nativi per eseguire il layout.

## <a name="horizontal-layout"></a>Layout orizzontale

Per impostazione predefinita [`CarouselView`](xref:Xamarin.Forms.CarouselView) , gli elementi vengono visualizzati orizzontalmente. Pertanto, non è necessario impostare la [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) proprietà per utilizzare questo layout:

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

In alternativa, questo layout può essere eseguito anche impostando la [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) proprietà su un `LinearItemsLayout` oggetto, specificando `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) il membro di enumerazione `Orientation` come valore della proprietà:

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

In questo modo si ottiene un layout che aumenta orizzontalmente Man mano che vengono aggiunti nuovi elementi:

[![Screenshot di un layout orizzontale CarouselView in iOS e Android](layout-images/horizontal.png "Layout orizzontale CarouselView")](layout-images/horizontal-large.png#lightbox "Layout orizzontale CarouselView")

## <a name="vertical-layout"></a>Layout verticale

[`CarouselView`](xref:Xamarin.Forms.CarouselView)consente di visualizzare verticalmente gli elementi impostando [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) la proprietà su `LinearItemsLayout` un oggetto, specificando il `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro di `Orientation` enumerazione come valore della proprietà:

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

In questo modo si ottiene un layout che cresce verticalmente Man mano che vengono aggiunti nuovi elementi:

[![Screenshot di un layout verticale CarouselView in iOS e Android](layout-images/vertical.png "Layout verticale CarouselView")](layout-images/vertical-large.png#lightbox "Layout verticale CarouselView")

## <a name="partially-visible-adjacent-items"></a>Elementi adiacenti parzialmente visibili

Per impostazione predefinita [`CarouselView`](xref:Xamarin.Forms.CarouselView) , Visualizza gli elementi completi contemporaneamente. Tuttavia, questo comportamento può essere modificato impostando la `PeekAreaInsets` proprietà su un `Thickness` valore che specifica la quantità di elementi adiacenti parzialmente visibili da. Questa operazione può essere utile per indicare agli utenti che sono presenti altri elementi da visualizzare. Il codice XAML seguente mostra un esempio di impostazione di questa proprietà:

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

Il risultato è che gli elementi adiacenti vengono esposti parzialmente sullo schermo:

[![Screenshot di un CollectionView con elementi adiacenti parzialmente visibili, in iOS e Android](layout-images/peek-items.png "CarouselView le impostazioni di visualizzazione dell'area")](layout-images/peek-items-large.png#lightbox "CarouselView di picco area")

## <a name="item-spacing"></a>Spaziatura elementi

Per impostazione predefinita, non è presente alcuno spazio tra gli elementi [`CarouselView`](xref:Xamarin.Forms.CarouselView)di un oggetto. Questo comportamento può essere modificato impostando la `ItemSpacing` proprietà sul layout degli elementi utilizzato da `CarouselView`.

Quando un [`CarouselView`](xref:Xamarin.Forms.CarouselView) oggetto imposta [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) la proprietà su `LinearItemsLayout` un oggetto, `LinearItemsLayout.ItemSpacing` la proprietà può essere impostata su `double` un valore che rappresenta lo spazio tra gli elementi:

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
> La `LinearItemsLayout.ItemSpacing` proprietà dispone di un set di callback di convalida, che assicura che il valore della proprietà sia sempre maggiore o uguale a 0.

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

Questo codice genera un layout verticale con una spaziatura di 20 elementi tra gli elementi.

## <a name="dynamic-resizing-of-items"></a>Ridimensionamento dinamico di elementi

Gli elementi in [`CarouselView`](xref:Xamarin.Forms.CarouselView) un oggetto possono essere ridimensionati dinamicamente in fase di esecuzione modificando le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)proprietà correlate al layout degli elementi all'interno di. Nell'esempio di codice seguente, ad esempio, [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) vengono [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) modificate le proprietà [`Image`](xref:Xamarin.Forms.Image) e di un oggetto `HeightRequest` e la proprietà dell' [`Frame`](xref:Xamarin.Forms.Frame)elemento padre:

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(150) ? 200 : 150;
    Frame frame = ((Frame)image.Parent.Parent);
    frame.HeightRequest = frame.HeightRequest.Equals(300) ? 350 : 300;
}
```

Il `OnImageTapped` gestore eventi viene eseguito in risposta a un [`Image`](xref:Xamarin.Forms.Image) oggetto da toccare e modifica le dimensioni dell'immagine (e del relativo elemento padre `Frame`), in modo che sia più semplice da visualizzare:

[![Screenshot di un CarouselView con ridimensionamento di elementi dinamici, in iOS e Android](layout-images/runtime-resizing.png "Ridimensionamento di elementi dinamici CarouselView")](layout-images/runtime-resizing-large.png#lightbox "Ridimensionamento di elementi dinamici CarouselView")

## <a name="right-to-left-layout"></a>Layout da destra a sinistra

[`CarouselView`](xref:Xamarin.Forms.CarouselView)può impostare il layout del contenuto in una direzione di flusso da destra a sinistra impostando [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) la [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)relativa proprietà su. Tuttavia, la `FlowDirection` proprietà deve essere impostata idealmente in una pagina o in un layout radice, che causa la risposta alla direzione del flusso di tutti gli elementi all'interno della pagina o del layout radice:

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

Il valore [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) predefinito per un elemento con un elemento [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)padre è. Quindi [`CarouselView`](xref:Xamarin.Forms.CarouselView) eredita il `FlowDirection` valore della proprietà da. [`ContentPage`](xref:Xamarin.Forms.ContentPage)

Per altre informazioni sulla direzione del flusso, vedere [localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Link correlati

- [CarouselView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Localizzazione da destra a sinistra](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Scorrimento CarouselView Novell. Forms](scrolling.md)
