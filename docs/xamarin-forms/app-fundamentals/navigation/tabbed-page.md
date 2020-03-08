---
title: Novell. Forms TabbedPage
description: TabbedPage di Xamarin.Forms è costituita da un elenco di schede e un'area dei dettagli più grande con ogni scheda che carica il contenuto nell'area dei dettagli. Questo articolo illustra come usare una classe TabbedPage per spostarsi in una raccolta di pagine.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 986045a4be352da0e439de87fdc70e2958b48d36
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910673"
---
# <a name="xamarinforms-tabbedpage"></a>Novell. Forms TabbedPage

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

Il tipo [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) di Xamarin.Forms è costituito da un elenco di schede e un'area più grande dei dettagli, dove ogni scheda carica contenuto nell'area dei dettagli. Gli screenshot seguenti mostrano un `TabbedPage` in iOS e Android:

[![Screenshot di un TabbedPage contenente tre schede, in iOS e Android](tabbed-page-images/tabbedpage-today.png "TabbedPage con tre schede")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage con tre schede")

In iOS l'elenco delle schede è visualizzato nella parte inferiore dello schermo e l'area dei dettagli è al di sopra di essa. Ogni scheda è costituita da un titolo e da un'icona, che deve essere un file PNG con un canale alfa. In orientamento verticale, le icone della barra delle schede vengono visualizzate sopra i titoli delle schede. Nell'orientamento orizzontale, le icone e i titoli vengono visualizzati affiancati. Inoltre, è possibile che venga visualizzata una barra delle schede normale o compatta, a seconda del dispositivo e dell'orientamento. Se sono disponibili più di cinque schede, verrà visualizzata una scheda **Altro** che può essere usata per accedere alle altre schede. Per informazioni sui requisiti delle icone, vedere la pagina relativa alle [dimensioni dell'icona della barra scheda](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons#tab-bar-icon-size) in Developer.Apple.com.

> [!TIP]
> Il `TabbedRenderer` per iOS dispone di un metodo di `GetIcon` sottoponibile a override che può essere utilizzato per caricare icone di tabulazione da un'origine specificata. Questo override rende possibile l'uso di immagini SVG come icone in una `TabbedPage`. È anche possibile specificare versioni selezionate e non selezionate di un'icona.

In Android, l'elenco di schede viene visualizzato nella parte superiore della schermata e l'area dei dettagli è riportata di seguito. Ogni scheda è costituita da un titolo e da un'icona, che deve essere un file PNG con un canale alfa. Tuttavia, le schede possono essere spostate nella parte inferiore dello schermo con una specifica della piattaforma. Se sono presenti più di cinque schede e la scheda si trova nella parte inferiore della schermata, verrà visualizzata una scheda *maggiore* che può essere usata per accedere alle schede aggiuntive. Per informazioni sui requisiti delle icone, vedere le [schede](https://material.io/components/tabs/#) in material.io e [supportare diverse densità di pixel](https://developer.android.com/training/multiscreen/screendensities) in Developer.Android.com. Per informazioni sullo stato di trasferimento delle schede nella parte inferiore della schermata, vedere [impostazione del posizionamento e del colore della barra degli strumenti TabbedPage](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md).

> [!TIP]
> Il `TabbedPageRenderer` per Android AppCompat ha un metodo di `GetIconDrawable` sottoponibile a override che può essere usato per caricare icone di tabulazione da un `Drawable`personalizzato. Questo override rende possibile l'uso di immagini SVG come icone in una `TabbedPage` e funziona sia con le barre delle schede superiori sia con quelle inferiori. In alternativa, si può usare il metodo `SetTabIcon` sottoponibile a override per caricare le icone delle schede da un elemento `Drawable` personalizzato per le barre delle schede superiori.

Nella piattaforma UWP (Universal Windows Platform) (UWP), l'elenco di schede viene visualizzato nella parte superiore della schermata e l'area dei dettagli è riportata di seguito. Ogni scheda è costituita da un titolo. Tuttavia, le icone possono essere aggiunte a ogni scheda con una specifica della piattaforma. Per altre informazioni, vedere [Icone TabbedPage in Windows](~/xamarin-forms/platform/windows/tabbedpage-icons.md).

## <a name="create-a-tabbedpage"></a>Creare un TabbedPage

Per creare una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) è possibile usare due approcci:

- Popolare il [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) con una raccolta di oggetti [`Page`](xref:Xamarin.Forms.Page) figlio, ad esempio una raccolta di oggetti di [`ContentPage`](xref:Xamarin.Forms.ContentPage) . Per altre informazioni, vedere [popolare un TabbedPage con una raccolta di pagine](#populate-a-tabbedpage-with-a-page-collection).
- Assegnare una raccolta alla proprietà [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e assegnare una [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) alla proprietà [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) per restituire le pagine per gli oggetti nella raccolta. Per altre informazioni, vedere [popolare un TabbedPage con un modello](#populate-a-tabbedpage-with-a-template).

Con entrambi gli approcci, la classe [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) visualizzerà ogni pagina al momento della selezione di ogni scheda da parte dell'utente.

> [!IMPORTANT]
> È consigliabile popolare una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) solo con istanze di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e [`ContentPage`](xref:Xamarin.Forms.ContentPage). In questo modo si potrà garantire un'esperienza utente uniforme su tutte le piattaforme.

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) definisce inoltre le proprietà seguenti:

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor), di tipo [`Color`](xref:Xamarin.Forms.Color), il colore di sfondo della barra della scheda.
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor), di tipo [`Color`](xref:Xamarin.Forms.Color), il colore del testo sulla barra delle schede.
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor), di tipo [`Color`](xref:Xamarin.Forms.Color), il colore della scheda quando viene selezionato.
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor), di tipo [`Color`](xref:Xamarin.Forms.Color), il colore della scheda quando è deselezionata.

Tutte queste proprietà sono supportate da oggetti [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) e ciò significa che supportano l'applicazione di stili e che le proprietà possono essere destinazioni di data binding.

> [!WARNING]
> In una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), ogni oggetto [`Page`](xref:Xamarin.Forms.Page) viene creato quando viene costruito il `TabbedPage`. Questo può causare un'esperienza utente inadeguata, soprattutto se il `TabbedPage` è la pagina radice dell'applicazione. Tuttavia, Novell. Forms Shell consente di creare su richiesta le pagine a cui si accede tramite una barra delle schede, in risposta alla navigazione. Per altre informazioni, vedere [Shell di Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="populate-a-tabbedpage-with-a-page-collection"></a>Popolamento di un TabbedPage con una raccolta di pagine

Un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) può essere popolato con una raccolta di oggetti [`Page`](xref:Xamarin.Forms.Page) figlio, ad esempio una raccolta di oggetti di [`ContentPage`](xref:Xamarin.Forms.ContentPage) . Questa operazione viene eseguita aggiungendo gli oggetti `Page` alla raccolta di [`TabbedPage.Children`](xref:Xamarin.Forms.MultiPage`1.Children*) . Questa operazione viene eseguita in XAML come indicato di seguito:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:TabbedPageWithNavigationPage;assembly=TabbedPageWithNavigationPage"
            x:Class="TabbedPageWithNavigationPage.MainPage">
    <local:TodayPage />
    <NavigationPage Title="Schedule" IconImageSource="schedule.png">
        <x:Arguments>
            <local:SchedulePage />
        </x:Arguments>
    </NavigationPage>
</TabbedPage>
```

> [!NOTE]
> La proprietà [`Children`](xref:Xamarin.Forms.MultiPage`1.Children*) della classe [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1) , da cui deriva [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , è il `ContentProperty` di `MultiPage<T>`. Pertanto, in XAML non è necessario assegnare in modo esplicito gli oggetti [`Page`](xref:Xamarin.Forms.Page) alla proprietà `Children`.

Il codice C# equivalente è il seguente:

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    NavigationPage navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.IconImageSource = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

In questo esempio, il [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) viene popolato con due oggetti [`Page`](xref:Xamarin.Forms.ContentPage) . Il primo elemento figlio è un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) e il secondo figlio è un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) contenente un oggetto `ContentPage`.

Gli screenshot seguenti mostrano un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage):

[![Screenshot di un TabbedPage contenente tre schede, in iOS e Android](tabbed-page-images/tabbedpage-today.png "TabbedPage con tre schede")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage con tre schede")

Selezionando un'altra scheda viene visualizzato l'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) che rappresenta la scheda:

[![Screenshot di un TabbedPage contenente schede, in iOS e Android](tabbed-page-images/tabbedpage-week.png "TabbedPage con schede")](tabbed-page-images/tabbedpage-week-large.png#lightbox "TabbedPage con schede")

Nella scheda **pianificazione** , l'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) viene incluso in un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) .

> [!WARNING]
> Mentre un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) può essere inserito in una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), non è consigliabile inserire un `TabbedPage` in un `NavigationPage`. Ciò è dovuto al fatto che in iOS un elemento `UITabBarController` funge sempre da wrapper per `UINavigationController`. Per altre informazioni, vedere [Combined View Controller Interfaces](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) (Interfacce combinate del controller di visualizzazione) nella libreria per sviluppatori iOS.

## <a name="navigate-within-a-tab"></a>Spostarsi all'interno di una scheda

La navigazione può essere eseguita all'interno di una scheda, a condizione che l'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) venga racchiuso in un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Questa operazione viene eseguita richiamando il metodo [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) sulla proprietà [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) dell'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```csharp
await Navigation.PushAsync (new UpcomingAppointmentsPage ());
```

La pagina a cui si sta eseguendo lo spostamento viene specificata come argomento per il metodo [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) . In questo esempio viene eseguito il push della pagina `UpcomingAppointmentsPage` nello stack di navigazione, in cui diventa la pagina attiva:

[![Screenshot della navigazione in una scheda, in iOS e Android](tabbed-page-images/tabbedpage-upcoming.png "Navigazione TabbedPage in una scheda")](tabbed-page-images/tabbedpage-upcoming-large.png#lightbox "Navigazione TabbedPage in una scheda")

Per altre informazioni sull'esecuzione della navigazione tramite la classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), vedere [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="populate-a-tabbedpage-with-a-template"></a>Popolamento di un TabbedPage con un modello

Un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) può essere popolato con le pagine assegnando una raccolta di dati alla proprietà [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e assegnando una [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) alla proprietà [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) che modelli i dati come [`Page`](xref:Xamarin.Forms.Page) oggetti. Questa operazione viene eseguita in XAML come indicato di seguito:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage"
            ItemsSource="{x:Static local:MonkeyDataModel.All}">            
  <TabbedPage.Resources>
    <ResourceDictionary>
      <local:NonNullToBooleanConverter x:Key="booleanConverter" />
    </ResourceDictionary>
  </TabbedPage.Resources>
  <TabbedPage.ItemTemplate>
    <DataTemplate>
      <ContentPage Title="{Binding Name}" IconImageSource="monkeyicon.png">
        <StackLayout Padding="5, 25">
          <Label Text="{Binding Name}" Font="Bold,Large" HorizontalOptions="Center" />
          <Image Source="{Binding PhotoUrl}" WidthRequest="200" HeightRequest="200" />
          <StackLayout Padding="50, 10">
            <StackLayout Orientation="Horizontal">
              <Label Text="Family:" HorizontalOptions="FillAndExpand" />
              <Label Text="{Binding Family}" Font="Bold,Medium" />
            </StackLayout>
            ...
          </StackLayout>
        </StackLayout>
      </ContentPage>
    </DataTemplate>
  </TabbedPage.ItemTemplate>
</TabbedPage>
```

Il codice C# equivalente è il seguente:

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() =>
    {
      var nameLabel = new Label
      {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label
      {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage
      {
        IconImageSource = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children =
          {
            nameLabel,
            image,
            new StackLayout
            {
              Padding = new Thickness (50, 10),
              Children =
              {
                new StackLayout
                {
                  Orientation = StackOrientation.Horizontal,
                  Children =
                  {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                // ...
              }
            }
          }
        }
      };
      contentPage.SetBinding (TitleProperty, "Name");
      return contentPage;
    });
    ItemsSource = MonkeyDataModel.All;
  }
}
```

In questo esempio, ogni scheda è costituita da un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) che utilizza [`Image`](xref:Xamarin.Forms.Image) e [`Label`](xref:Xamarin.Forms.Label) oggetti per visualizzare i dati per la scheda:

[![Screenshot di un TabbedPage basato su modelli, in iOS e Android](tabbed-page-images/tabbedpage-template.png "TabbedPage basato su modelli")](tabbed-page-images/tabbedpage-template-large.png#lightbox "TabbedPage basato su modelli")

Selezionando un'altra scheda viene visualizzato l'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) che rappresenta la scheda.

## <a name="related-links"></a>Collegamenti correlati

- [TabbedPageWithNavigationPage (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Tipi di pagina](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [API TabbedPage](xref:Xamarin.Forms.TabbedPage)
