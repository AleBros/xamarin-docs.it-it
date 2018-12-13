---
title: Pagina carousel di Xamarin.Forms
description: CarouselPage di Xamarin.Forms è una pagina che gli utenti possono scorrere rapidamente da un lato all'altro per spostarsi nelle pagine di contenuto, come una raccolta. Questo articolo illustra come usare una classe CarouselPage per spostarsi in una raccolta di pagine.
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 48c009b836ac109e0d54cd2fdb036c46e17c4387
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121503"
---
# <a name="xamarinforms-carousel-page"></a>Pagina carousel di Xamarin.Forms

_CarouselPage di Xamarin.Forms è una pagina che gli utenti possono scorrere rapidamente da un lato all'altro per spostarsi nelle pagine di contenuto, come una raccolta. Questo articolo illustra come usare una classe CarouselPage per spostarsi in una raccolta di pagine._

## <a name="overview"></a>Panoramica

Gli screenshot seguenti illustrano una [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) in ogni piattaforma:

![](carousel-page-images/thirdpage.png "Terzo elemento di CarouselPage")

Il layout di una [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) è identico in ogni piattaforma. Si può passare da una pagina all'altra scorrendo rapidamente da destra a sinistra per passare alla pagina successiva della raccolta e scorrendo rapidamente da sinistra a destra per passare alla pagina precedente della raccolta. Gli screenshot seguenti illustrano la prima pagina in un'istanza di [`CarouselPage`](xref:Xamarin.Forms.CarouselPage):

![](carousel-page-images/firstpage.png "Primo elemento di CarouselPage")

Scorrendo rapidamente da destra a sinistra si passa alla seconda pagina, come illustrato negli screenshot seguenti:

![](carousel-page-images/secondpage.png "Secondo elemento di CarouselPage")

Scorrendo di nuovo rapidamente da destra a sinistra si passa alla terza pagina, mentre scorrendo rapidamente da sinistra a destra si torna alla pagina precedente.

<!--
> [!NOTE]
> The [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) has been deprecated, and will be removed from Xamarin.Forms in a future release. Instead, the [`CarouselView`](xref:Xamarin.Forms.CarouselView) should be used to provide a gallery-like view, where users can swipe from side to side to move through a collection of items.
-->

## <a name="creating-a-carouselpage"></a>Creazione di una classe CarouselPage

Per creare una [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) è possibile usare due approcci:

- [Popolare](#Populating_a_CarouselPage_with_a_Page_Collection) la `CarouselPage` con una raccolta di istanze [`ContentPage`](xref:Xamarin.Forms.ContentPage) figlio.
- [Assegnare](#Populating_a_CarouselPage_with_a_Template) una raccolta alla proprietà [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e assegnare una classe [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) alla proprietà [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) per restituire le istanze di [`ContentPage`](xref:Xamarin.Forms.ContentPage) per gli oggetti della raccolta.

Con entrambi gli approcci, `CarouselPage` visualizzerà a sua volta ogni pagina, con un'interazione di scorrimento che consente di passare alla pagina successiva da visualizzare.

> [!NOTE]
> Una [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) può essere popolata solo con istanze di [`ContentPage`](xref:Xamarin.Forms.ContentPage) o derivate di `ContentPage`.

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populating-a-carouselpage-with-a-page-collection"></a>Popolamento di una classe CarouselPage con una raccolta di pagine

L'esempio di codice XAML seguente illustra una [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) che visualizza tre istanze di [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<CarouselPage xmlns="http://xamarin.com/schemas/2014/forms"
              xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
              x:Class="CarouselPageNavigation.MainPage">
    <ContentPage>
        <ContentPage.Padding>
          <OnPlatform x:TypeArguments="Thickness">
              <On Platform="iOS, Android" Value="0,40,0,0" />
          </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
            <Label Text="Red" FontSize="Medium" HorizontalOptions="Center" />
            <BoxView Color="Red" WidthRequest="200" HeightRequest="200" HorizontalOptions="Center" VerticalOptions="CenterAndExpand" />
        </StackLayout>
    </ContentPage>
    <ContentPage>
        ...
    </ContentPage>
    <ContentPage>
        ...
    </ContentPage>
</CarouselPage>
```

L'esempio di codice seguente illustra l'interfaccia utente equivalente in C#:

```csharp
public class MainPageCS : CarouselPage
{
    public MainPageCS ()
    {
        Thickness padding;
        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
            case Device.Android:
                padding = new Thickness(0, 40, 0, 0);
                break;
            default:
                padding = new Thickness();
                break;
        }

        var redContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                Children = {
                    new Label {
                        Text = "Red",
                        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
                        HorizontalOptions = LayoutOptions.Center
                    },
                    new BoxView {
                        Color = Color.Red,
                        WidthRequest = 200,
                        HeightRequest = 200,
                        HorizontalOptions = LayoutOptions.Center,
                        VerticalOptions = LayoutOptions.CenterAndExpand
                    }
                }
            }
        };
        var greenContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                ...
            }
        };
        var blueContentPage = new ContentPage {
            Padding = padding,
            Content = new StackLayout {
                ...
            }
        };

        Children.Add (redContentPage);
        Children.Add (greenContentPage);
        Children.Add (blueContentPage);
    }
}
```

Ogni classe [`ContentPage`](xref:Xamarin.Forms.ContentPage) visualizza semplicemente una classe [`Label`](xref:Xamarin.Forms.Label) per un particolare colore e una classe [`BoxView`](xref:Xamarin.Forms.BoxView) di quel colore.

> [!NOTE]
> La classe [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) non supporta la virtualizzazione dell'interfaccia utente. Di conseguenza, la presenza di troppi elementi figlio nella `CarouselPage` può influire sulle prestazioni.

Se una [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) è incorporata nella pagina [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) di una classe [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage), la proprietà [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) deve essere impostata su `false` per evitare conflitti di movimento tra `CarouselPage` e `MasterDetailPage`.

Per altre informazioni sulla [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), vedere il [capitolo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del manuale di Xamarin.Forms redatto da Charles Petzold.

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populating-a-carouselpage-with-a-template"></a>Popolamento di una classe CarouselPage con un modello

L'esempio di codice XAML seguente illustra una [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) costruita assegnando una classe [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) alla proprietà [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) per restituire le pagine per gli oggetti della raccolta:

```xaml
<CarouselPage xmlns="http://xamarin.com/schemas/2014/forms"
              xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
              x:Class="CarouselPageNavigation.MainPage">
    <CarouselPage.ItemTemplate>
        <DataTemplate>
            <ContentPage>
                <ContentPage.Padding>
                  <OnPlatform x:TypeArguments="Thickness">
                    <On Platform="iOS, Android" Value="0,40,0,0" />
                  </OnPlatform>
                </ContentPage.Padding>
                <StackLayout>
                    <Label Text="{Binding Name}" FontSize="Medium" HorizontalOptions="Center" />
                    <BoxView Color="{Binding Color}" WidthRequest="200" HeightRequest="200" HorizontalOptions="Center" VerticalOptions="CenterAndExpand" />
                </StackLayout>
            </ContentPage>
        </DataTemplate>
    </CarouselPage.ItemTemplate>
</CarouselPage>
```

La [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) viene popolata con i dati impostando la proprietà [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) nel costruttore per il file code-behind:

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

L'esempio di codice seguente illustra la [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) equivalente creata in C#:

```csharp
public class MainPageCS : CarouselPage
{
    public MainPageCS ()
    {
        Thickness padding;
        switch (Device.RuntimePlatform)
        {
            case Device.iOS:
            case Device.Android:
                padding = new Thickness(0, 40, 0, 0);
                break;
            default:
                padding = new Thickness();
                break;
        }

        ItemTemplate = new DataTemplate (() => {
            var nameLabel = new Label {
                FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
                HorizontalOptions = LayoutOptions.Center
            };
            nameLabel.SetBinding (Label.TextProperty, "Name");

            var colorBoxView = new BoxView {
                WidthRequest = 200,
                HeightRequest = 200,
                HorizontalOptions = LayoutOptions.Center,
                VerticalOptions = LayoutOptions.CenterAndExpand
            };
            colorBoxView.SetBinding (BoxView.ColorProperty, "Color");

            return new ContentPage {
                Padding = padding,
                Content = new StackLayout {
                    Children = {
                        nameLabel,
                        colorBoxView
                    }
                }
            };
        });

        ItemsSource = ColorsDataModel.All;
    }
}
```

Ogni classe [`ContentPage`](xref:Xamarin.Forms.ContentPage) visualizza semplicemente una classe [`Label`](xref:Xamarin.Forms.Label) per un particolare colore e una classe [`BoxView`](xref:Xamarin.Forms.BoxView) di quel colore.

> [!NOTE]
> La classe [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) non supporta la virtualizzazione dell'interfaccia utente. Di conseguenza, la presenza di troppi elementi figlio nella `CarouselPage` può influire sulle prestazioni.

Se una [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) è incorporata nella pagina [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) di una classe [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage), la proprietà [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) deve essere impostata su `false` per evitare conflitti di movimento tra `CarouselPage` e `MasterDetailPage`.

Per altre informazioni sulla [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), vedere il [capitolo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del manuale di Xamarin.Forms redatto da Charles Petzold.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato l'uso di una [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) per spostarsi in una raccolta di pagine. `CarouselPage` è una pagina che gli utenti possono scorrere rapidamente da un lato all'altro per spostarsi nelle pagine di contenuto, proprio come una raccolta.


## <a name="related-links"></a>Collegamenti correlati

- [Tipi di pagina](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/)
- [CarouselPageTemplate (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
