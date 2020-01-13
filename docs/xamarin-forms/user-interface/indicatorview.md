---
title: Xamarin.Forms IndicatorView
description: IndicatorView è un controllo che visualizza indicatori che rappresentano il numero di elementi e la posizione corrente in un CarouselView.
ms.prod: xamarin
ms.assetId: BBCC223B-4B02-46B7-80BB-EE0E86A67CE2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/17/2019
ms.openlocfilehash: 6b7845011470d83d8f2187e0227950c23e46d52d
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490402"
---
# <a name="xamarinforms-indicatorview"></a>Xamarin.Forms IndicatorView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)

Il `IndicatorView` è un controllo che consente di visualizzare gli indicatori che rappresentano il numero di elementi e la posizione corrente in una `CarouselView`:

[![Screenshot di CarouselView e IndicatorView, su iOS e Android](indicatorview-images/circles.png "Cerchi IndicatorView")](indicatorview-images/circles-large.png#lightbox "Cerchi IndicatorView")

`IndicatorView` è disponibile in Xamarin.Forms 4,4 sulle piattaforme iOS e Android. Tuttavia, è attualmente sperimentale e può essere usata solo aggiungendo la riga di codice seguente alla classe `AppDelegate` in iOS o alla classe `MainActivity` in Android, prima di chiamare `Forms.Init`:

```csharp
Forms.SetFlags("IndicatorView_Experimental");
```

`IndicatorView` definisce le proprietà seguenti:

- `Count`, di tipo `int`, il numero di indicatori.
- `HideSingle`, di tipo `bool`, indica se l'indicatore deve essere nascosto quando ne esiste solo uno. Il valore predefinito è `true`.
- `IndicatorColor`, di tipo `Color`, il colore degli indicatori.
- `IndicatorSize`, di tipo `double`, la dimensione degli indicatori. Il valore predefinito è 6,0.
- `IndicatorLayout`, di tipo `Layout<View>`, definisce la classe di layout utilizzata per eseguire il rendering della `IndicatorView`. Questa proprietà viene impostata da Xamarin.Forms e in genere non deve essere impostata dagli sviluppatori.
- `IndicatorTemplate`, di tipo `DataTemplate`, il modello che definisce l'aspetto di ogni indicatore.
- `IndicatorsShape`, di tipo `IndicatorShape`, la forma di ogni indicatore.
- `ItemsSource`, di tipo `IEnumerable`, la raccolta per la quale verranno visualizzati gli indicatori. Questa proprietà verrà impostata automaticamente quando viene impostata la proprietà `ItemsSourceBy`.
- `ItemsSourceBy`, di tipo `VisualElement`, l'oggetto `CarouselView` per cui visualizzare gli indicatori.
- `MaximumVisible`, di tipo `int`, il numero massimo di indicatori visibili. Il valore predefinito è `int.MaxValue`.
- `Position`, di tipo `int`, l'indice dell'indicatore attualmente selezionato. Questa proprietà usa un'associazione `TwoWay`. Questa proprietà verrà impostata automaticamente quando viene impostata la proprietà `ItemsSourceBy`.
- `SelectedIndicatorColor`, di tipo `Color`, il colore dell'indicatore che rappresenta l'elemento corrente nell'`CarouselView`.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che possono essere destinazioni di data binding e con stile.

## <a name="create-an-indicatorview"></a>Creare un IndicatorView

Nell'esempio seguente viene illustrato come creare un'istanza di un `IndicatorView` in XAML:

```xaml
<StackLayout>
    <CarouselView x:Name="carouselView"
                  ItemsSource="{Binding Monkeys}">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView ItemsSourceBy="carouselView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

In questo esempio viene eseguito il rendering del `IndicatorView` sotto l'`CarouselView`, con un indicatore per ogni elemento nella `CarouselView`. Il `IndicatorView` viene popolato con i dati impostando la proprietà `ItemsSourceBy` sull'oggetto `CarouselView`. Ogni indicatore è un cerchio grigio chiaro, mentre l'indicatore che rappresenta l'elemento corrente nel `CarouselView` è grigio scuro.

> [!IMPORTANT]
> Impostando la proprietà `ItemsSourceBy` si ottiene il binding della proprietà `Position` alla proprietà `CarouselView.Position` e l'associazione della proprietà `ItemsSource` alla proprietà `CarouselView.ItemsSource`.

## <a name="change-indicator-shape"></a>Modifica forma indicatore

La classe `IndicatorView` dispone di una proprietà `IndicatorsShape`, che indica la forma degli indicatori. Questa proprietà può essere impostata su uno dei membri dell'enumerazione `IndicatorShape`:

- `Circle` specifica che le forme dell'indicatore saranno circolari. Questo è il valore predefinito per la proprietà `IndicatorView.IndicatorsShape`.
- `Square` indica che le forme dell'indicatore saranno quadrate.

Nell'esempio seguente viene illustrato un `IndicatorView` configurato per l'utilizzo di indicatori quadrati:

```xaml
<IndicatorView IndicatorsShape="Square"
               ItemsSourceBy="carouselView"
               IndicatorColor="LightGray"
               SelectedIndicatorColor="DarkGray" />
```

## <a name="define-indicator-appearance"></a>Definisci aspetto indicatore

È possibile definire l'aspetto di ogni indicatore impostando la proprietà `IndicatorView.IndicatorTemplate` su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<StackLayout>
    <CarouselView x:Name="carouselView"
                  ItemsSource="{Binding Monkeys}">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView ItemsSourceBy="carouselView"
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

Gli elementi specificati nel [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definiscono l'aspetto di ogni indicatore. In questo esempio, ogni indicatore è un [`Image`](xref:Xamarin.Forms.Image) che visualizza un'icona del tipo di carattere usando l'estensione di markup `FontImage`.

Gli screenshot seguenti mostrano gli indicatori sottoposti a rendering usando un'icona del tipo di carattere:

[![Screenshot di un IndicatorView basato su modelli, in iOS e Android](indicatorview-images/templated.png "IndicatorView basato su modelli")](indicatorview-images/templated-large.png#lightbox "IndicatorView basato su modelli")

Per ulteriori informazioni sull'estensione di markup `FontImage`, vedere [FontImage Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension).

## <a name="related-links"></a>Collegamenti correlati

- [IndicatorView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)
- [Estensione di markup FontImage](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)
