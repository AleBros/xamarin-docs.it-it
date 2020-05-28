---
title: Xamarin.FormsPagina carosello
description: Xamarin.FormsCarouselPage è una pagina che gli utenti possono scorrere da un lato all'altro per spostarsi tra le pagine di contenuto, ad esempio una raccolta. Questo articolo illustra come usare una classe CarouselPage per spostarsi in una raccolta di pagine.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4d0cebe98b66f3596e72624efed90e965b942bfc
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137527"
---
# <a name="xamarinforms-carousel-page"></a>Xamarin.FormsPagina carosello

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)

_Xamarin.FormsCarouselPage è una pagina che gli utenti possono scorrere da un lato all'altro per spostarsi tra le pagine di contenuto, ad esempio una raccolta. Questo articolo illustra come usare un CarouselPage per spostarsi in una raccolta di pagine._

> [!IMPORTANT]
> L'oggetto [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) è stato sostituito da [`CarouselView`](xref:Xamarin.Forms.CarouselView) , che fornisce un layout scorrevole in cui gli utenti possono scorrere rapidamente una raccolta di elementi. Per ulteriori informazioni su `CarouselView` , vedere [ Xamarin.Forms CarouselView](~/xamarin-forms/user-interface/carouselview/index.md).

Gli screenshot seguenti mostrano un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) in ogni piattaforma:

![](carousel-page-images/thirdpage.png "CarouselPage Third Item")

Il layout di un [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) è identico in ogni piattaforma. Si può passare da una pagina all'altra scorrendo rapidamente da destra a sinistra per passare alla pagina successiva della raccolta e scorrendo rapidamente da sinistra a destra per passare alla pagina precedente della raccolta. Gli screenshot seguenti mostrano la prima pagina in un' [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) istanza:

![](carousel-page-images/firstpage.png "CarouselPage First Item")

Scorrendo rapidamente da destra a sinistra si passa alla seconda pagina, come illustrato negli screenshot seguenti:

![](carousel-page-images/secondpage.png "CarouselPage Second Item")

Scorrendo di nuovo rapidamente da destra a sinistra si passa alla terza pagina, mentre scorrendo rapidamente da sinistra a destra si torna alla pagina precedente.

> [!NOTE]
> Non [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) supporta la virtualizzazione dell'interfaccia utente. Di conseguenza, la presenza di troppi elementi figlio nella `CarouselPage` può influire sulle prestazioni.

Se un oggetto [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) è incorporato nella [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) pagina di un oggetto [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) , la [`MasterDetailPage.IsGestureEnabled`](xref:Xamarin.Forms.MasterDetailPage.IsGestureEnabledProperty) proprietà deve essere impostata su `false` per impedire conflitti di movimento tra `CarouselPage` e `MasterDetailPage` .

Per ulteriori informazioni su [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) , vedere il [capitolo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del libro di Charles Petzold Xamarin.Forms .

## <a name="create-a-carouselpage"></a>Creare un'istanza di CarouselPage

Per creare un, è possibile usare due approcci [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) :

- [Popolare](#Populating_a_CarouselPage_with_a_Page_Collection) la `CarouselPage` con una raccolta di istanze [`ContentPage`](xref:Xamarin.Forms.ContentPage) figlio.
- [Assegnare](#Populating_a_CarouselPage_with_a_Template) una raccolta alla proprietà [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e assegnare una classe [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) alla proprietà [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) per restituire le istanze di [`ContentPage`](xref:Xamarin.Forms.ContentPage) per gli oggetti della raccolta.

Con entrambi gli approcci, `CarouselPage` visualizzerà a sua volta ogni pagina, con un'interazione di scorrimento che consente di passare alla pagina successiva da visualizzare.

> [!NOTE]
> Un oggetto [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) può essere popolato solo con [`ContentPage`](xref:Xamarin.Forms.ContentPage) istanze o `ContentPage` derivati.

<a name="Populating_a_CarouselPage_with_a_Page_Collection" />

### <a name="populate-a-carouselpage-with-a-page-collection"></a>Popolare un'istanza di CarouselPage con una raccolta di pagine

Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) che visualizza tre [`ContentPage`](xref:Xamarin.Forms.ContentPage) istanze:

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

Ogni [`ContentPage`](xref:Xamarin.Forms.ContentPage) viene semplicemente visualizzato un oggetto [`Label`](xref:Xamarin.Forms.Label) per un colore specifico e un oggetto [`BoxView`](xref:Xamarin.Forms.BoxView) di tale colore.

<a name="Populating_a_CarouselPage_with_a_Template" />

### <a name="populate-a-carouselpage-with-a-template"></a>Popolare un'istanza di CarouselPage con un modello

Nell'esempio di codice XAML seguente viene illustrato un oggetto [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) costruito assegnando un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) alla [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) proprietà per restituire le pagine per gli oggetti nella raccolta:

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

[`CarouselPage`](xref:Xamarin.Forms.CarouselPage)Viene popolato con i dati impostando [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) la proprietà nel costruttore per il file code-behind:

```csharp
public MainPage ()
{
    ...
    ItemsSource = ColorsDataModel.All;
}
```

L'esempio di codice seguente illustra l'equivalente [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) creato in C#:

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

Ogni [`ContentPage`](xref:Xamarin.Forms.ContentPage) viene semplicemente visualizzato un oggetto [`Label`](xref:Xamarin.Forms.Label) per un colore specifico e un oggetto [`BoxView`](xref:Xamarin.Forms.BoxView) di tale colore.

## <a name="related-links"></a>Collegamenti correlati

- [Tipi di pagina](~/xamarin-forms/user-interface/controls/pages.md)
- [CarouselPage (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpage)
- [CarouselPageTemplate (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-carouselpagetemplate)
- [CarouselPage](xref:Xamarin.Forms.CarouselPage)
