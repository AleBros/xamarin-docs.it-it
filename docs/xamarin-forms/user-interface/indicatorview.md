---
title: Xamarin.FormsIndicatorView
description: ''
ms.prod: ''
ms.assetId: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a6bf11fd80dd5226ae26dd392e80f784a9b296bf
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84131989"
---
# <a name="xamarinforms-indicatorview"></a>Xamarin.FormsIndicatorView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)

`IndicatorView`È un controllo che visualizza indicatori che rappresentano il numero di elementi e la posizione corrente in un `CarouselView` :

[![Screenshot di CarouselView e IndicatorView, su iOS e Android](indicatorview-images/circles.png "Cerchi IndicatorView")](indicatorview-images/circles-large.png#lightbox "Cerchi IndicatorView")

`IndicatorView`è disponibile in Xamarin.Forms 4,4 nelle piattaforme iOS e Android e in 4,5 nel piattaforma UWP (Universal Windows Platform). Tuttavia, è attualmente sperimentale e può essere usata solo aggiungendo la seguente riga di codice alla `AppDelegate` classe in iOS o alla `MainActivity` classe in Android, prima di chiamare `Forms.Init` :

```csharp
Forms.SetFlags("IndicatorView_Experimental");
```

`IndicatorView` definisce le proprietà seguenti:

- `Count`, di tipo `int` , il numero di indicatori.
- `HideSingle`, di tipo `bool` , indica se l'indicatore deve essere nascosto quando ne esiste solo uno. Il valore predefinito è `true`.
- `IndicatorColor`, di tipo `Color` , il colore degli indicatori.
- `IndicatorSize`, di tipo `double` , la dimensione degli indicatori. Il valore predefinito è 6,0.
- `IndicatorLayout`, di tipo `Layout<View>` , definisce la classe layout utilizzata per eseguire il rendering dell'oggetto `IndicatorView` . Questa proprietà viene impostata da Xamarin.Forms e in genere non deve essere impostata dagli sviluppatori.
- `IndicatorTemplate`, di tipo `DataTemplate` , il modello che definisce l'aspetto di ogni indicatore.
- `IndicatorsShape`, di tipo `IndicatorShape` , la forma di ogni indicatore.
- `ItemsSource`, di tipo `IEnumerable` , la raccolta per la quale verranno visualizzati gli indicatori. Questa proprietà verrà impostata automaticamente quando `CarouselView.IndicatorView` viene impostata la proprietà.
- `MaximumVisible`, di tipo `int` , il numero massimo di indicatori visibili. Il valore predefinito è `int.MaxValue`.
- `Position`, di tipo `int` , l'indice dell'indicatore attualmente selezionato. Questa proprietà usa un' `TwoWay` associazione. Questa proprietà verrà impostata automaticamente quando `CarouselView.IndicatorView` viene impostata la proprietà.
- `SelectedIndicatorColor`, di tipo `Color` , il colore dell'indicatore che rappresenta l'elemento corrente nell'oggetto `CarouselView` .

Queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che possono essere destinazioni di data binding e con stile.

## <a name="create-an-indicatorview"></a>Creare un IndicatorView

Nell'esempio seguente viene illustrato come creare un'istanza di `IndicatorView` in XAML:

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

In questo esempio `IndicatorView` viene eseguito il rendering di sotto l'oggetto `CarouselView` , con un indicatore per ogni elemento in `CarouselView` . `IndicatorView`Viene popolato con i dati impostando `CarouselView.IndicatorView` la proprietà sull' `IndicatorView` oggetto. Ogni indicatore è un cerchio grigio chiaro, mentre l'indicatore che rappresenta l'elemento corrente nel `CarouselView` è grigio scuro.

> [!IMPORTANT]
> L'impostazione della `CarouselView.IndicatorView` proprietà comporta l' `IndicatorView.Position` associazione della proprietà alla `CarouselView.Position` proprietà e l' `IndicatorView.ItemsSource` associazione della proprietà alla proprietà `CarouselView.ItemsSource` .

## <a name="change-indicator-shape"></a>Modifica forma indicatore

La `IndicatorView` classe dispone `IndicatorsShape` di una proprietà che determina la forma degli indicatori. Questa proprietà può essere impostata su uno dei `IndicatorShape` membri dell'enumerazione:

- `Circle`Specifica che le forme dell'indicatore saranno circolari. Questo è il valore predefinito per la proprietà `IndicatorView.IndicatorsShape`.
- `Square`indica che le forme dell'indicatore saranno quadrate.

Nell'esempio seguente viene illustrato un oggetto `IndicatorView` configurato per l'utilizzo di indicatori quadrati:

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorsShape="Square"
               IndicatorColor="LightGray"
               SelectedIndicatorColor="DarkGray" />
```

## <a name="change-indicator-size"></a>Modifica dimensioni indicatore

La `IndicatorView` classe ha una `IndicatorSize` proprietà di tipo `double` , che determina le dimensioni degli indicatori in unità indipendenti dal dispositivo. Il valore predefinito di questa proprietà è 6,0.

Nell'esempio seguente viene illustrato un oggetto `IndicatorView` configurato per la visualizzazione di indicatori più grandi:

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorSize="18" />
```

## <a name="limit-the-number-of-indicators-displayed"></a>Limita il numero di indicatori visualizzati

La `IndicatorView` classe dispone `MaximumVisible` di una proprietà di tipo `int` , che determina il numero massimo di indicatori visibili.

Nell'esempio seguente viene illustrato un oggetto `IndicatorView` configurato per la visualizzazione di un massimo di sei indicatori:

```xaml
<IndicatorView x:Name="indicatorView"
               MaximumVisible="6" />
```

## <a name="define-indicator-appearance"></a>Definisci aspetto indicatore

L'aspetto di ogni indicatore può essere definito impostando la `IndicatorView.IndicatorTemplate` proprietà su un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="Black"
                   HorizontalOptions="Center">
        <IndicatorView.IndicatorTemplate>
            <DataTemplate>
                <Image Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=12}" />
            </DataTemplate>
        </IndicatorView.IndicatorTemplate>
    </IndicatorView>
</StackLayout>
```

Gli elementi specificati in [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definiscono l'aspetto di ogni indicatore. In questo esempio, ogni indicatore è un oggetto [`Image`](xref:Xamarin.Forms.Image) che visualizza un'icona del tipo di carattere usando l' `FontImage` estensione di markup.

Gli screenshot seguenti mostrano gli indicatori sottoposti a rendering usando un'icona del tipo di carattere:

[![Screenshot di un IndicatorView basato su modelli, in iOS e Android](indicatorview-images/templated.png "IndicatorView basato su modelli")](indicatorview-images/templated-large.png#lightbox "IndicatorView basato su modelli")

Per ulteriori informazioni sull' `FontImage` estensione di markup, vedere [FontImage Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension).

## <a name="related-links"></a>Collegamenti correlati

- [IndicatorView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)
- [Estensione del markup FontImage](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)
