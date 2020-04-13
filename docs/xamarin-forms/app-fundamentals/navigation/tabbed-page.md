---
title: Xamarin.Forms TabbedPage
description: TabbedPage di Xamarin.Forms è costituita da un elenco di schede e un'area dei dettagli più grande con ogni scheda che carica il contenuto nell'area dei dettagli. Questo articolo illustra come usare una classe TabbedPage per spostarsi in una raccolta di pagine.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 986045a4be352da0e439de87fdc70e2958b48d36
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305087"
---
# <a name="xamarinforms-tabbedpage"></a>Xamarin.Forms TabbedPage

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) è costituito da un elenco di schede e un'area di dettaglio più grande, con ogni scheda che carica il contenuto nell'area di dettaglio. Le schermate seguenti `TabbedPage` mostrano un su iOS e Android:

[![Screenshot di un tabbedPage contenente tre schede, in iOS e Android](tabbed-page-images/tabbedpage-today.png "TabbedPage con tre schede")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage con tre schede")

In iOS l'elenco delle schede è visualizzato nella parte inferiore dello schermo e l'area dei dettagli è al di sopra di essa. Ogni scheda è costituita da un titolo e un'icona, che dovrebbe essere un file PNG con un canale alfa. In orientamento verticale, le icone della barra delle schede vengono visualizzate sopra i titoli delle schede. Nell'orientamento orizzontale, le icone e i titoli vengono visualizzati affiancati. Inoltre, è possibile che venga visualizzata una barra delle schede regolare o compatta, a seconda del dispositivo e dell'orientamento. Se sono disponibili più di cinque schede, verrà visualizzata una scheda **Altro** che può essere usata per accedere alle altre schede. Per informazioni sui requisiti delle icone, vedere [Dimensione dell'icona della barra delle](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons#tab-bar-icon-size) schede in developer.apple.com.

> [!TIP]
> Il `TabbedRenderer` per iOS dispone `GetIcon` di un metodo sottoponibile a override che può essere utilizzato per caricare le icone delle schede da un'origine specificata. Questo override rende possibile l'uso di immagini SVG come icone in una `TabbedPage`. È anche possibile specificare versioni selezionate e non selezionate di un'icona.

In Android, l'elenco delle schede viene visualizzato nella parte superiore dello schermo e l'area dei dettagli è sotto. Ogni scheda è costituita da un titolo e un'icona, che dovrebbe essere un file PNG con un canale alfa. Tuttavia, le schede possono essere spostate nella parte inferiore dello schermo con una specifica della piattaforma. Se sono presenti più di cinque schede e l'elenco delle schede si trova nella parte inferiore dello schermo, verrà visualizzata una scheda *Altro* che può essere utilizzata per accedere alle schede aggiuntive. Per informazioni sui requisiti delle icone, consulta [Schede](https://material.io/components/tabs/#) in material.io e Supporta densità di [pixel diverse](https://developer.android.com/training/multiscreen/screendensities) in developer.android.com. Per informazioni sullo spostamento delle schede nella parte inferiore dello schermo, vedere Impostazione del posizionamento e del [colore della barra degli strumenti TabbedPage](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md).

> [!TIP]
> Il `TabbedPageRenderer` per Android AppCompat `GetIconDrawable` dispone di un metodo sottoponibile `Drawable`a override che può essere utilizzato per caricare le icone delle schede da un oggetto personalizzato . Questo override rende possibile l'uso di immagini SVG come icone in una `TabbedPage` e funziona sia con le barre delle schede superiori sia con quelle inferiori. In alternativa, si può usare il metodo `SetTabIcon` sottoponibile a override per caricare le icone delle schede da un elemento `Drawable` personalizzato per le barre delle schede superiori.

Nella piattaforma UWP (Universal Windows Platform), l'elenco delle schede viene visualizzato nella parte superiore dello schermo e l'area dei dettagli è riportata di seguito. Ogni scheda è costituita da un titolo. Tuttavia, le icone possono essere aggiunte a ogni scheda con una specifica della piattaforma. Per ulteriori informazioni, vedere [Icone di TabbedPage in Windows](~/xamarin-forms/platform/windows/tabbedpage-icons.md).

## <a name="create-a-tabbedpage"></a>Creare un'istanza di TabbedPage

Due approcci possono essere [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)utilizzati per creare un:

- Popolare [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) l'oggetto con [`Page`](xref:Xamarin.Forms.Page) una raccolta di [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetti figlio, ad esempio una raccolta di oggetti. Per ulteriori informazioni, vedere [Popolare un TabbedPage con una raccolta di](#populate-a-tabbedpage-with-a-page-collection)pagine .
- Assegnare una raccolta alla proprietà [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e assegnare una classe [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) alla proprietà [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) per restituire le pagine per gli oggetti della raccolta. Per ulteriori informazioni, consultate [Popolare un TabbedPage con un modello](#populate-a-tabbedpage-with-a-template).

Con entrambi gli [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) approcci, il verrà visualizzato ogni pagina come l'utente seleziona ogni scheda.

> [!IMPORTANT]
> È consigliabile compilare un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) con e solo le istanze. In questo modo si potrà garantire un'esperienza utente uniforme su tutte le piattaforme.

Inoltre, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) definisce le seguenti proprietà:

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor), di [`Color`](xref:Xamarin.Forms.Color)tipo , il colore di sfondo della barra delle schede.
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor), di [`Color`](xref:Xamarin.Forms.Color)tipo , il colore del testo sulla barra delle schede.
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor), di [`Color`](xref:Xamarin.Forms.Color)tipo , il colore della scheda quando viene selezionata.
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor), di [`Color`](xref:Xamarin.Forms.Color)tipo , il colore della scheda quando è deselezionata.

Tutte queste proprietà sono [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) supportate da oggetti, il che significa che possono essere stili e le proprietà possono essere le destinazioni delle associazioni dati.

> [!WARNING]
> In [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)un [`Page`](xref:Xamarin.Forms.Page) oggetto , ogni `TabbedPage` oggetto viene creato quando viene costruito l'oggetto . Ciò può causare una scarsa esperienza `TabbedPage` utente, in particolare se il è la pagina radice dell'applicazione. Tuttavia, Xamarin.Forms Shell consente di creare pagine accessibili tramite una barra delle schede su richiesta, in risposta alla navigazione. Per ulteriori informazioni, vedere [Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="populate-a-tabbedpage-with-a-page-collection"></a>Popolare un TabbedPage con una raccolta PagePopulate a TabbedPage with a Page collection

Un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) oggetto può essere popolato con una raccolta di oggetti figlio, [`Page`](xref:Xamarin.Forms.Page) ad esempio una raccolta di [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetti. Ciò si ottiene `Page` aggiungendo [`TabbedPage.Children`](xref:Xamarin.Forms.MultiPage`1.Children*) gli oggetti alla raccolta. Questa operazione viene eseguita in XAML come segue:This is accomplished in XAML as follows:

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
> La [`Children`](xref:Xamarin.Forms.MultiPage`1.Children*) proprietà [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1) della classe [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , da cui `ContentProperty` `MultiPage<T>`deriva, è la di . Pertanto, in XAML non è necessario [`Page`](xref:Xamarin.Forms.Page) assegnare `Children` in modo esplicito gli oggetti alla proprietà.

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

In questo esempio, l'oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) viene popolato con due [`Page`](xref:Xamarin.Forms.ContentPage) oggetti. Il primo elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage) figlio è un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e `ContentPage` il secondo elemento figlio è un oggetto che contiene un oggetto.

Le schermate seguenti [`ContentPage`](xref:Xamarin.Forms.ContentPage) mostrano [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)un oggetto in un:

[![Screenshot di un tabbedPage contenente tre schede, in iOS e Android](tabbed-page-images/tabbedpage-today.png "TabbedPage con tre schede")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage con tre schede")

Selezionando un'altra [`ContentPage`](xref:Xamarin.Forms.ContentPage) scheda viene visualizzato l'oggetto che rappresenta la scheda:

[![Screenshot di un TabbedPage contenente schede, su iOS e Android](tabbed-page-images/tabbedpage-week.png "TabbedPage con schede")](tabbed-page-images/tabbedpage-week-large.png#lightbox "TabbedPage con schede")

Nella scheda **Pianificazione,** l'oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) viene racchiuso in un oggetto.

> [!WARNING]
> Mentre [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) un oggetto può [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)essere inserito in un `TabbedPage` oggetto `NavigationPage`, non è consigliabile inserire un oggetto in un file . Ciò è dovuto al fatto che in iOS un elemento `UITabBarController` funge sempre da wrapper per `UINavigationController`. Per altre informazioni, vedere [Combined View Controller Interfaces](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) (Interfacce combinate del controller di visualizzazione) nella libreria per sviluppatori iOS.

## <a name="navigate-within-a-tab"></a>Spostarsi all'interno di una scheda

La navigazione può essere eseguita [`ContentPage`](xref:Xamarin.Forms.ContentPage) all'interno di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) una scheda, a condizione che l'oggetto sia racchiuso in un oggetto. Questa operazione viene eseguita [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) richiamando [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) il [`ContentPage`](xref:Xamarin.Forms.ContentPage) metodo sulla proprietà dell'oggetto:

```csharp
await Navigation.PushAsync (new UpcomingAppointmentsPage ());
```

La pagina a cui ci si sposta [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) viene specificata come argomento del metodo. In questo esempio, la `UpcomingAppointmentsPage` pagina viene inserita nello stack di navigazione, dove diventa la pagina attiva:In this example, the page is pushed onto the navigation stack, where it becomes the active page:

[![Screenshot della navigazione all'interno di una scheda, in iOS e Android](tabbed-page-images/tabbedpage-upcoming.png "TabbedPage di navigazione in una scheda")](tabbed-page-images/tabbedpage-upcoming-large.png#lightbox "TabbedPage di navigazione in una scheda")

Per ulteriori informazioni sull'esecuzione della navigazione mediante la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe , vedere Navigazione [gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="populate-a-tabbedpage-with-a-template"></a>Popolare un TabbedPage con un modello

È [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) possibile compilare un oggetto a cui [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) possono essere popolate [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) le [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) pagine assegnando una [`Page`](xref:Xamarin.Forms.Page) raccolta di dati alla proprietà e assegnando un alla proprietà che modellii i dati come oggetti. Questa operazione viene eseguita in XAML come segue:This is accomplished in XAML as follows:

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

In questo esempio, ogni [`ContentPage`](xref:Xamarin.Forms.ContentPage) scheda è [`Image`](xref:Xamarin.Forms.Image) [`Label`](xref:Xamarin.Forms.Label) costituita da un oggetto che utilizza e da oggetti per visualizzare i dati per la scheda:

[![Screenshot di un oggetto TabbedPage basato su modelli, in iOS e Android](tabbed-page-images/tabbedpage-template.png "TabbedPage basato su modelli")](tabbed-page-images/tabbedpage-template-large.png#lightbox "TabbedPage basato su modelli")

Selezionando un'altra [`ContentPage`](xref:Xamarin.Forms.ContentPage) scheda viene visualizzato l'oggetto che rappresenta la scheda.

## <a name="related-links"></a>Collegamenti correlati

- [TabbedPageWithNavigationPage (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Varietà di pagine](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPage API](xref:Xamarin.Forms.TabbedPage)
