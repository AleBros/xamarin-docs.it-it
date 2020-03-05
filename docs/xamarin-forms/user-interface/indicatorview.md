---
title: Novell. Forms IndicatorView
description: IndicatorView è un controllo che visualizza indicatori che rappresentano il numero di elementi e la posizione corrente in un CarouselView.
ms.prod: xamarin
ms.assetId: BBCC223B-4B02-46B7-80BB-EE0E86A67CE2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2020
ms.openlocfilehash: e76cf6e766a95994fa2862deb9eb73928f4769a2
ms.sourcegitcommit: 5d22f37dfc358678df52a4d17c57261056a72cb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "78292591"
---
# <a name="xamarinforms-indicatorview"></a>Novell. Forms IndicatorView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)

Il `IndicatorView` è un controllo che consente di visualizzare gli indicatori che rappresentano il numero di elementi e la posizione corrente in una `CarouselView`:

[![Screenshot di CarouselView e IndicatorView, su iOS e Android](indicatorview-images/circles.png "Cerchi IndicatorView")](indicatorview-images/circles-large.png#lightbox "Cerchi IndicatorView")

`IndicatorView` è disponibile in Novell. Forms 4,4 nelle piattaforme iOS e Android e in 4,5 nel piattaforma UWP (Universal Windows Platform). Tuttavia, è attualmente sperimentale e può essere usata solo aggiungendo la riga di codice seguente alla classe `AppDelegate` in iOS o alla classe `MainActivity` in Android, prima di chiamare `Forms.Init`:

```csharp
Forms.SetFlags("IndicatorView_Experimental");
```

`IndicatorView` definisce le proprietà seguenti:

- `Count`, di tipo `int`, il numero di indicatori.
- `HideSingle`, di tipo `bool`, indica se l'indicatore deve essere nascosto quando ne esiste solo uno. Il valore predefinito è `true`.
- `IndicatorColor`, di tipo `Color`, il colore degli indicatori.
- `IndicatorSize`, di tipo `double`, la dimensione degli indicatori. Il valore predefinito è 6,0.
- `IndicatorLayout`, di tipo `Layout<View>`, definisce la classe di layout utilizzata per eseguire il rendering della `IndicatorView`. Questa proprietà viene impostata da Novell. Forms e in genere non deve essere impostata dagli sviluppatori.
- `IndicatorTemplate`, di tipo `DataTemplate`, il modello che definisce l'aspetto di ogni indicatore.
- `IndicatorsShape`, di tipo `IndicatorShape`, la forma di ogni indicatore.
- `ItemsSource`, di tipo `IEnumerable`, la raccolta per la quale verranno visualizzati gli indicatori. Questa proprietà verrà impostata automaticamente quando viene impostata la proprietà `CarouselView.IndicatorView`.
- `MaximumVisible`, di tipo `int`, il numero massimo di indicatori visibili. Il valore predefinito è `int.MaxValue`.
- `Position`, di tipo `int`, l'indice dell'indicatore attualmente selezionato. Questa proprietà usa un'associazione `TwoWay`. Questa proprietà verrà impostata automaticamente quando viene impostata la proprietà `CarouselView.IndicatorView`.
- `SelectedIndicatorColor`, di tipo `Color`, il colore dell'indicatore che rappresenta l'elemento corrente nell'`CarouselView`.

Queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , il che significa che possono essere destinazioni di data binding e con stile.

## <a name="create-an-indicatorview"></a>Creare un IndicatorView

Nell'esempio seguente viene illustrato come creare un'istanza di un `IndicatorView` in XAML:

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

In questo esempio viene eseguito il rendering del `IndicatorView` sotto l'`CarouselView`, con un indicatore per ogni elemento nella `CarouselView`. Il `IndicatorView` viene popolato con i dati impostando la proprietà `CarouselView.IndicatorView` sull'oggetto `IndicatorView`. Ogni indicatore è un cerchio grigio chiaro, mentre l'indicatore che rappresenta l'elemento corrente nel `CarouselView` è grigio scuro.

> [!IMPORTANT]
> Impostando la proprietà `CarouselView.IndicatorView` si ottiene il binding della proprietà `IndicatorView.Position` alla proprietà `CarouselView.Position` e l'associazione della proprietà `IndicatorView.ItemsSource` alla proprietà `CarouselView.ItemsSource`.

## <a name="change-indicator-shape"></a>Modifica forma indicatore

La classe `IndicatorView` dispone di una proprietà `IndicatorsShape`, che determina la forma degli indicatori. Questa proprietà può essere impostata su uno dei membri dell'enumerazione `IndicatorShape`:

- `Circle` specifica che le forme dell'indicatore saranno circolari. Questo è il valore predefinito per la proprietà `IndicatorView.IndicatorsShape`.
- `Square` indica che le forme dell'indicatore saranno quadrate.

Nell'esempio seguente viene illustrato un `IndicatorView` configurato per l'utilizzo di indicatori quadrati:

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorsShape="Square"
               IndicatorColor="LightGray"
               SelectedIndicatorColor="DarkGray" />
```

## <a name="change-indicator-size"></a>Modifica dimensioni indicatore

La classe `IndicatorView` dispone di una proprietà `IndicatorSize`, di tipo `double`, che determina le dimensioni degli indicatori in unità indipendenti dal dispositivo. Il valore predefinito di questa proprietà è 6,0.

Nell'esempio seguente viene illustrato un `IndicatorView` configurato per visualizzare indicatori di dimensioni maggiori:

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorSize="18" />
```

## <a name="limit-the-number-of-indicators-displayed"></a>Limita il numero di indicatori visualizzati

La classe `IndicatorView` dispone di una proprietà `MaximumVisible`, di tipo `int`, che determina il numero massimo di indicatori visibili.

Nell'esempio seguente viene illustrato un `IndicatorView` configurato per visualizzare un massimo di sei indicatori:

```xaml
<IndicatorView x:Name="indicatorView"
               MaximumVisible="6" />
```

## <a name="define-indicator-appearance"></a>Definisci aspetto indicatore

È possibile definire l'aspetto di ogni indicatore impostando la proprietà `IndicatorView.IndicatorTemplate` su un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

Gli elementi specificati nel [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definiscono l'aspetto di ogni indicatore. In questo esempio, ogni indicatore è un [`Image`](xref:Xamarin.Forms.Image) che visualizza un'icona del tipo di carattere usando l'estensione di markup `FontImage`.

Gli screenshot seguenti mostrano gli indicatori sottoposti a rendering usando un'icona del tipo di carattere:

[![Screenshot di un IndicatorView basato su modelli, in iOS e Android](indicatorview-images/templated.png "IndicatorView basato su modelli")](indicatorview-images/templated-large.png#lightbox "IndicatorView basato su modelli")

Per ulteriori informazioni sull'estensione di markup `FontImage`, vedere [FontImage Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension).

## <a name="related-links"></a>Collegamenti correlati

- [IndicatorView (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)
- [Estensione del markup FontImage](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)
