---
title: Pagina di sequenza
description: "Il CarouselPage xamarin. Forms è una pagina che gli utenti possono scorrere verso destra per spostarsi tra le pagine di contenuto, ad esempio una raccolta. In questo articolo viene illustrato come utilizzare un CarouselPage per spostarsi di una raccolta di pagine."
ms.topic: article
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: c4f8d70625fd8ee0e07832f8bdb3b7e502b5b38f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="carousel-page"></a>Pagina di sequenza

_Il CarouselPage xamarin. Forms è una pagina che gli utenti possono scorrere verso destra per spostarsi tra le pagine di contenuto, ad esempio una raccolta. In questo articolo viene illustrato come utilizzare un CarouselPage per spostarsi di una raccolta di pagine._

## <a name="overview"></a>Panoramica

Le schermate seguenti viene illustrato un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) in ogni piattaforma:

![](carousel-page-images/thirdpage.png "Elemento Thid CarouselPage")

Il layout di un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) è identico in ogni piattaforma. È possibile esplorare le pagine tramite scorrendo rapidamente verso destra a sinistra per spostarsi avanti tramite la raccolta e scorrendo rapidamente verso sinistra a destra per spostarsi all'interno dell'insieme. La schermata seguente mostra la prima pagina in un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) istanza:

![](carousel-page-images/firstpage.png "Elemento di primo CarouselPage")

Passaggio da destra a sinistra consente di passare alla seconda pagina, come illustrato nelle schermate seguenti:

![](carousel-page-images/secondpage.png "Elemento CarouselPage secondo")

Passaggio da destra a sinistra di nuovo sposta nella terza pagina durante il passaggio da sinistra a destra restituisce alla pagina precedente.

<!--
> [!NOTE]
> The [`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) has been deprecated, and will be removed from Xamarin.Forms in a future release. Instead, the [`CarouselView`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselView/) should be used to provide a gallery-like view, where users can swipe from side to side to move through a collection of items.
-->

## <a name="creating-a-carouselpage"></a>Creazione di un CarouselPage

Due approcci possono essere utilizzati per creare un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/):

- [Popolare](#Populating_a_CarouselPage_with_a_Page_Collection) il `CarouselPage` con una raccolta di figlio [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) istanze.
- [Assegnare](#Populating_a_CarouselPage_with_a_Template) una raccolta per il [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/) proprietà e assegnare un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) per il [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/) proprietà da restituire [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) istanze per gli oggetti nella raccolta.

Con entrambi gli approcci, il `CarouselPage` verrà quindi visualizzare ogni pagina a sua volta, con un'interazione scorrere lo spostamento alla pagina successiva deve essere visualizzato. Questa esperienza di navigazione conferiranno naturale e familiari agli utenti di Windows Phone.

> [!NOTE]
> Oggetto [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) può essere usato solo con [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) istanze, o `ContentPage` derivati.

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populating-a-carouselpage-with-a-page-collection"></a>Popolamento di un CarouselPage con una raccolta di pagina

Nell'esempio di codice XAML seguente viene un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) che consente di visualizzare tre [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) istanze:

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

Esempio di codice seguente viene illustrato l'equivalente dell'interfaccia utente in c#:

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

Ogni [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) visualizza semplicemente una [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) per un determinato colore e un [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) del colore.

> [!NOTE]
> Il [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) non supporta la virtualizzazione dell'interfaccia utente. Pertanto, le prestazioni possono essere interessate se la `CarouselPage` contiene troppi elementi figlio.

Se un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) viene incorporata nel [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) pagina di un [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/), [ `MasterDetailPage.IsGestureEnabled` ](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty/) proprietà deve essere impostata su `false` per evitare conflitti di movimento tra il `CarouselPage` e `MasterDetailPage`.

Per ulteriori informazioni sul [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/), vedere [capitolo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) di libro di Petzolds xamarin. Forms.

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populating-a-carouselpage-with-a-template"></a>Popolamento di un CarouselPage con un modello

Nell'esempio di codice XAML seguente viene un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) costruito assegnando un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) per il [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/) proprietà da restituire per le pagine oggetti nella raccolta:

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

Il [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) viene popolato con i dati impostando il [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/) proprietà nel costruttore per il file code-behind:

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

L'esempio di codice seguente mostra l'equivalente [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) creato in c#:

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

Ogni [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) visualizza semplicemente una [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) per un determinato colore e un [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) del colore.

> [!NOTE]
> Il [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) non supporta la virtualizzazione dell'interfaccia utente. Pertanto, le prestazioni possono essere interessate se la `CarouselPage` contiene troppi elementi figlio.

Se un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) viene incorporata nel [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) pagina di un [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/), [ `MasterDetailPage.IsGestureEnabled` ](https://developer.xamarin.com/api/field/Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty/) proprietà deve essere impostata su `false` per evitare conflitti di movimento tra il `CarouselPage` e `MasterDetailPage`.

Per ulteriori informazioni sul [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/), vedere [capitolo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) di libro di Petzolds xamarin. Forms.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare un [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) per spostarsi tra un insieme di pagine. Il `CarouselPage` è una pagina che gli utenti possono scorrere verso destra per spostarsi tra le pagine di contenuto, ad esempio una raccolta e offre un'esperienza di navigazione che ritiene naturale e familiari agli utenti di Windows Phone.


## <a name="related-links"></a>Collegamenti correlati

- [Tipi di pagina](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/)
- [CarouselPageTemplate (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/)
- [CarouselPage](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/)
