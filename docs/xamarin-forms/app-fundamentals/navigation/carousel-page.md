---
title: Pagina sequenza video di xamarin. Forms
description: CarouselPage di xamarin. Forms è una pagina che gli utenti possono scorrere verso destra per scorrere le pagine di contenuto, ad esempio una raccolta. Questo articolo illustra come usare un CarouselPage per scorrere una raccolta di pagine.
ms.prod: xamarin
ms.assetid: 2D14FC9D-DF5F-427E-9006-2AAE61ECF8DC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: bce3a60f3647a537906cfa11fc1dcfcc6f5cf365
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998609"
---
# <a name="xamarinforms-carousel-page"></a>Pagina sequenza video di xamarin. Forms

_CarouselPage di xamarin. Forms è una pagina che gli utenti possono scorrere verso destra per scorrere le pagine di contenuto, ad esempio una raccolta. Questo articolo illustra come usare un CarouselPage per scorrere una raccolta di pagine._

## <a name="overview"></a>Panoramica

L'esempio di screenshot seguente mostra una [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) in ogni piattaforma:

![](carousel-page-images/thirdpage.png "Elemento Thid CarouselPage")

Il layout di un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) è identica in ogni piattaforma. È possibile spostarsi attraverso le pagine scorrendo rapidamente verso sinistra a destra per spostarsi in avanti all'interno dell'insieme e scorrendo rapidamente verso sinistra a destra per spostarsi all'indietro tramite la raccolta. Le schermate seguenti illustrano la prima pagina in un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) istanza:

![](carousel-page-images/firstpage.png "Elemento First CarouselPage")

Scorrendo rapidamente da destra a sinistra consente di passare alla seconda pagina, come illustrato negli screenshot seguenti:

![](carousel-page-images/secondpage.png "Elemento CarouselPage secondo")

Scorrendo rapidamente nuovamente da destra a sinistra viene spostata nella terza pagina, mentre scorrendo rapidamente da sinistra a destra restituisce alla pagina precedente.

<!--
> [!NOTE]
> The [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) has been deprecated, and will be removed from Xamarin.Forms in a future release. Instead, the [`CarouselView`](xref:Xamarin.Forms.CarouselView) should be used to provide a gallery-like view, where users can swipe from side to side to move through a collection of items.
-->

## <a name="creating-a-carouselpage"></a>Creazione di un CarouselPage

Due approcci possono essere utilizzati per creare un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage):

- [Popolare](#Populating_a_CarouselPage_with_a_Page_Collection) il `CarouselPage` con una raccolta di figlio [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) istanze.
- [Assegnare](#Populating_a_CarouselPage_with_a_Template) una raccolta per il [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) proprietà e assegnare una [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) per il [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) proprietà da restituire [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) istanze per gli oggetti nella raccolta.

Con entrambi gli approcci di `CarouselPage` verrà quindi visualizzare ogni pagina a sua volta, con un'interazione tramite passaggio del dito lo spostamento alla pagina successiva da visualizzare.

> [!NOTE]
> Oggetto [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) può essere usato solo con [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) istanze, o `ContentPage` derivati.

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populating-a-carouselpage-with-a-page-collection"></a>Popolamento di un CarouselPage con una raccolta di pagina

L'esempio di codice XAML seguente mostra una [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) che consente di visualizzare tre [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) istanze:

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

Esempio di codice seguente mostra l'interfaccia utente di equivalente in c#:

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

Ciascuna [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) visualizza semplicemente una [ `Label` ](xref:Xamarin.Forms.Label) per un determinato colore e un [ `BoxView` ](xref:Xamarin.Forms.BoxView) del colore.

> [!NOTE]
> Il [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) non supporta la virtualizzazione dell'interfaccia utente. Di conseguenza, le prestazioni possono essere interessate se la `CarouselPage` contiene troppi elementi figlio.

Se un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) viene incorporata nel [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) pagina di un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage), il [ `MasterDetailPage.IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) proprietà deve essere impostata su `false` per evitare conflitti di movimento tra il `CarouselPage` e il `MasterDetailPage`.

Per altre informazioni sul [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage), vedere [capitolo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del libro di xamarin. Forms Petzolds.

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populating-a-carouselpage-with-a-template"></a>Popolamento di un CarouselPage con un modello

L'esempio di codice XAML seguente mostra una [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) costruito tramite l'assegnazione di un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) per il [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) proprietà per restituire le pagine per oggetti nella raccolta:

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

Il [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) viene popolata con i dati impostando i [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) proprietà nel costruttore per il file code-behind:

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

Esempio di codice seguente viene illustrato l'equivalente [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) creato in c#:

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

Ciascuna [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) visualizza semplicemente una [ `Label` ](xref:Xamarin.Forms.Label) per un determinato colore e un [ `BoxView` ](xref:Xamarin.Forms.BoxView) del colore.

> [!NOTE]
> Il [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) non supporta la virtualizzazione dell'interfaccia utente. Di conseguenza, le prestazioni possono essere interessate se la `CarouselPage` contiene troppi elementi figlio.

Se un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) viene incorporata nel [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) pagina di un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage), il [ `MasterDetailPage.IsGestureEnabled` ](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) proprietà deve essere impostata su `false` per evitare conflitti di movimento tra il `CarouselPage` e il `MasterDetailPage`.

Per altre informazioni sul [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage), vedere [capitolo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del libro di xamarin. Forms Petzolds.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare un [ `CarouselPage` ](xref:Xamarin.Forms.CarouselPage) per spostarsi tra una raccolta di pagine. Il `CarouselPage` è una pagina che gli utenti possono scorrere verso destra per spostarsi tra le pagine di contenuto, molto simile a una raccolta.


## <a name="related-links"></a>Collegamenti correlati

- [Tipi di pagina](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPage/)
- [CarouselPageTemplate (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/CarouselPageTemplate/)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
