---
title: Pagina carousel di Xamarin.Forms
description: CarouselPage di Xamarin.Forms è una pagina che gli utenti possono scorrere rapidamente da un lato all'altro per spostarsi nelle pagine di contenuto, come una raccolta. Questo articolo illustra come usare una classe CarouselPage per spostarsi in una raccolta di pagine.
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 5cfaeb8589514972dfcccd11009a02c00f95bfac
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "72696436"
---
# <a name="xamarinforms-carousel-page"></a>Pagina carousel di Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)

_Il Xamarin.Forms CarouselPage è una pagina che gli utenti possono scorrere da un lato all'altro per spostarsi tra le pagine di contenuto, ad esempio una galleria. In questo articolo viene illustrato come utilizzare un CarouselPage per spostarsi all'interno di una raccolta di pagine._

> [!IMPORTANT]
> L'oggetto [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) è stato [`CarouselView`](xref:Xamarin.Forms.CarouselView)sostituito dalla , che fornisce un layout scorrevole in cui gli utenti possono scorrere rapidamente per spostarsi all'otta' a una raccolta di elementi. Per ulteriori informazioni `CarouselView`su , vedere [Xamarin.Forms CarouselView](~/xamarin-forms/user-interface/carouselview/index.md).

Le schermate seguenti [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) mostrano un su ogni piattaforma:The following screenshots show a on each platform:

![](carousel-page-images/thirdpage.png "CarouselPage Third Item")

Il layout [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) di a è identico su ogni piattaforma. Si può passare da una pagina all'altra scorrendo rapidamente da destra a sinistra per passare alla pagina successiva della raccolta e scorrendo rapidamente da sinistra a destra per passare alla pagina precedente della raccolta. Le schermate seguenti mostrano [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) la prima pagina in un'istanza:The following screenshots show the first page in a instance:

![](carousel-page-images/firstpage.png "CarouselPage First Item")

Scorrendo rapidamente da destra a sinistra si passa alla seconda pagina, come illustrato negli screenshot seguenti:

![](carousel-page-images/secondpage.png "CarouselPage Second Item")

Scorrendo di nuovo rapidamente da destra a sinistra si passa alla terza pagina, mentre scorrendo rapidamente da sinistra a destra si torna alla pagina precedente.

> [!NOTE]
> Il [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) non supporta la virtualizzazione dell'interfaccia utente. Di conseguenza, la presenza di troppi elementi figlio nella `CarouselPage` può influire sulle prestazioni.

Se [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) un oggetto [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) è incorporato [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)nella [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) pagina di `false` un oggetto , `CarouselPage` la `MasterDetailPage`proprietà deve essere impostata su per evitare conflitti di movimento tra e e .

Per ulteriori informazioni [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)sull'oggetto , vedere il [capitolo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del libro Xamarin.Forms di Charles Petzold.

## <a name="create-a-carouselpage"></a>Creare un'istanza di CarouselPage

Due approcci possono essere [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)utilizzati per creare un:

- [Popolare](#Populating_a_CarouselPage_with_a_Page_Collection) la `CarouselPage` con una raccolta di istanze [`ContentPage`](xref:Xamarin.Forms.ContentPage) figlio.
- [Assegnare](#Populating_a_CarouselPage_with_a_Template) una raccolta alla proprietà [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e assegnare una classe [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) alla proprietà [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) per restituire le istanze di [`ContentPage`](xref:Xamarin.Forms.ContentPage) per gli oggetti della raccolta.

Con entrambi gli approcci, `CarouselPage` visualizzerà a sua volta ogni pagina, con un'interazione di scorrimento che consente di passare alla pagina successiva da visualizzare.

> [!NOTE]
> Un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) può essere [`ContentPage`](xref:Xamarin.Forms.ContentPage) popolato `ContentPage` solo con istanze o derivati.

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populate-a-carouselpage-with-a-page-collection"></a>Popolare un'istanza di CarouselPage con una raccolta di pagine

L'esempio di codice [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) XAML [`ContentPage`](xref:Xamarin.Forms.ContentPage) seguente mostra un che visualizza tre istanze:The following XAML code example shows a that displays three instances:

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

Ognuno [`ContentPage`](xref:Xamarin.Forms.ContentPage) mostra [`Label`](xref:Xamarin.Forms.Label) semplicemente un per [`BoxView`](xref:Xamarin.Forms.BoxView) un colore particolare e uno di quel colore.

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populate-a-carouselpage-with-a-template"></a>Popolare un'istanza di CarouselPage con un modello

Nell'esempio di codice [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) XAML seguente [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) viene [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) illustrato un oggetto costruito assegnando un alla proprietà per restituire le pagine per gli oggetti nella raccolta:The following XAML code example shows a constructed by assigning a to the property to return pages for objects in the collection:

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

L'oggetto [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) viene popolato [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) con i dati impostando la proprietà nel costruttore per il file code-behind:

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

Nell'esempio di codice [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) riportato di seguito viene illustrato l'equivalente creato in C:

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

Ognuno [`ContentPage`](xref:Xamarin.Forms.ContentPage) mostra [`Label`](xref:Xamarin.Forms.Label) semplicemente un per [`BoxView`](xref:Xamarin.Forms.BoxView) un colore particolare e uno di quel colore.

## <a name="related-links"></a>Collegamenti correlati

- [Varietà di pagine](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)
- [CarouselPageTemplate (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
