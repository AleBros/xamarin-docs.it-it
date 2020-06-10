---
title: " Xamarin.Forms TabbedPage" Description: " Xamarin.Forms TabbedPage è costituito da un elenco di schede e da un'area di dettaglio più ampia, in cui ogni scheda carica contenuto nell'area dei dettagli. Questo articolo illustra come usare un TabbedPage per spostarsi in una raccolta di pagine. "
ms. prod: Novell MS. AssetID: C946057F-C77C-412D-82A0-DAF475A24EF5 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 11/07/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-tabbedpage"></a>Xamarin.FormsTabbedPage

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)

Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) È costituito da un elenco di schede e da un'area di dettaglio più ampia, in cui ogni scheda carica contenuto nell'area dei dettagli. Gli screenshot seguenti mostrano un `TabbedPage` in iOS e Android:

[![Screenshot di un TabbedPage contenente tre schede, in iOS e Android](tabbed-page-images/tabbedpage-today.png "TabbedPage con tre schede")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage con tre schede")

In iOS l'elenco delle schede è visualizzato nella parte inferiore dello schermo e l'area dei dettagli è al di sopra di essa. Ogni scheda è costituita da un titolo e da un'icona, che deve essere un file PNG con un canale alfa. In orientamento verticale, le icone della barra delle schede vengono visualizzate sopra i titoli delle schede. Nell'orientamento orizzontale, le icone e i titoli vengono visualizzati affiancati. Inoltre, è possibile che venga visualizzata una barra delle schede normale o compatta, a seconda del dispositivo e dell'orientamento. Se sono disponibili più di cinque schede, verrà visualizzata una scheda **Altro** che può essere usata per accedere alle altre schede. Per informazioni sui requisiti delle icone, vedere la pagina relativa alle [dimensioni dell'icona della barra scheda](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons#tab-bar-icon-size) in Developer.Apple.com.

> [!TIP]
> `TabbedRenderer`Per iOS è presente un metodo sottoponibile `GetIcon` a override che può essere utilizzato per caricare icone di tabulazione da un'origine specificata. Questo override rende possibile l'uso di immagini SVG come icone in una `TabbedPage`. È anche possibile specificare versioni selezionate e non selezionate di un'icona.

In Android, l'elenco di schede viene visualizzato nella parte superiore della schermata e l'area dei dettagli è riportata di seguito. Ogni scheda è costituita da un titolo e da un'icona, che deve essere un file PNG con un canale alfa. Tuttavia, le schede possono essere spostate nella parte inferiore dello schermo con una specifica della piattaforma. Se sono presenti più di cinque schede e la scheda si trova nella parte inferiore della schermata, verrà visualizzata una scheda *maggiore* che può essere usata per accedere alle schede aggiuntive. Per informazioni sui requisiti delle icone, vedere le [schede](https://material.io/components/tabs/#) in material.io e [supportare diverse densità di pixel](https://developer.android.com/training/multiscreen/screendensities) in Developer.Android.com. Per informazioni sullo stato di trasferimento delle schede nella parte inferiore della schermata, vedere [impostazione del posizionamento e del colore della barra degli strumenti TabbedPage](~/xamarin-forms/platform/android/tabbedpage-toolbar-placement-color.md).

> [!TIP]
> `TabbedPageRenderer`Per Android AppCompat è presente un metodo sottoponibile `GetIconDrawable` a override che può essere usato per caricare icone di tabulazione da un oggetto personalizzato `Drawable` . Questo override rende possibile l'uso di immagini SVG come icone in una `TabbedPage` e funziona sia con le barre delle schede superiori sia con quelle inferiori. In alternativa, si può usare il metodo `SetTabIcon` sottoponibile a override per caricare le icone delle schede da un elemento `Drawable` personalizzato per le barre delle schede superiori.

Nella piattaforma UWP (Universal Windows Platform) (UWP), l'elenco di schede viene visualizzato nella parte superiore della schermata e l'area dei dettagli è riportata di seguito. Ogni scheda è costituita da un titolo. Tuttavia, le icone possono essere aggiunte a ogni scheda con una specifica della piattaforma. Per altre informazioni, vedere [Icone TabbedPage in Windows](~/xamarin-forms/platform/windows/tabbedpage-icons.md).

## <a name="create-a-tabbedpage"></a>Creare un'istanza di TabbedPage

Per creare un, è possibile usare due approcci [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

- Popolare [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) con una raccolta di oggetti figlio [`Page`](xref:Xamarin.Forms.Page) , ad esempio una raccolta di [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetti. Per altre informazioni, vedere [popolare un TabbedPage con una raccolta di pagine](#populate-a-tabbedpage-with-a-page-collection).
- Assegnare una raccolta alla proprietà [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e assegnare una classe [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) alla proprietà [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) per restituire le pagine per gli oggetti della raccolta. Per altre informazioni, vedere [popolare un TabbedPage con un modello](#populate-a-tabbedpage-with-a-template).

Con entrambi gli approcci, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) Visualizza ogni pagina quando l'utente seleziona ogni scheda.

> [!IMPORTANT]
> È consigliabile che un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) venga popolato solo con le [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) [`ContentPage`](xref:Xamarin.Forms.ContentPage) istanze e. In questo modo si potrà garantire un'esperienza utente uniforme su tutte le piattaforme.

[`TabbedPage`](xref:Xamarin.Forms.TabbedPage)Definisce inoltre le proprietà seguenti:

- [`BarBackgroundColor`](xref:Xamarin.Forms.TabbedPage.BarBackgroundColor), di tipo [`Color`](xref:Xamarin.Forms.Color) , il colore di sfondo della barra schede.
- [`BarTextColor`](xref:Xamarin.Forms.TabbedPage.BarTextColor), di tipo [`Color`](xref:Xamarin.Forms.Color) , il colore del testo sulla barra della scheda.
- [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor), di tipo [`Color`](xref:Xamarin.Forms.Color) , il colore della scheda quando viene selezionato.
- [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor), di tipo [`Color`](xref:Xamarin.Forms.Color) , il colore della scheda quando è deselezionata.

Tutte queste proprietà sono supportate da [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetti, il che significa che è possibile applicare uno stile e le proprietà possono essere le destinazioni delle associazioni dati.

> [!WARNING]
> In un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ogni [`Page`](xref:Xamarin.Forms.Page) oggetto viene creato quando `TabbedPage` viene costruito. Questo può causare un'esperienza utente insufficiente, in particolare se `TabbedPage` è la pagina radice dell'applicazione. Tuttavia, Xamarin.Forms Shell consente di creare su richiesta le pagine a cui si accede tramite una barra delle schede, in risposta alla navigazione. Per ulteriori informazioni, vedere [ Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="populate-a-tabbedpage-with-a-page-collection"></a>Popolamento di un TabbedPage con una raccolta di pagine

Un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) può essere popolato con una raccolta di [`Page`](xref:Xamarin.Forms.Page) oggetti figlio, ad esempio una raccolta di [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetti. Questa operazione viene eseguita aggiungendo gli `Page` oggetti alla [`TabbedPage.Children`](xref:Xamarin.Forms.MultiPage`1.Children*) raccolta. Questa operazione viene eseguita in XAML come indicato di seguito:

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
> La [`Children`](xref:Xamarin.Forms.MultiPage`1.Children*) proprietà della [`MultiPage<T>`](xref:Xamarin.Forms.MultiPage`1) classe, da cui [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) deriva, è l'oggetto `ContentProperty` di `MultiPage<T>` . Pertanto, in XAML non è necessario assegnare in modo esplicito gli [`Page`](xref:Xamarin.Forms.Page) oggetti alla `Children` Proprietà.

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

In questo esempio, [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) viene popolato con due [`Page`](xref:Xamarin.Forms.ContentPage) oggetti. Il primo elemento figlio è un [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto e il secondo elemento figlio è un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) che contiene un `ContentPage` oggetto.

Gli screenshot seguenti mostrano un [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto in un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

[![Screenshot di un TabbedPage contenente tre schede, in iOS e Android](tabbed-page-images/tabbedpage-today.png "TabbedPage con tre schede")](tabbed-page-images/tabbedpage-today-large.png#lightbox "TabbedPage con tre schede")

Selezionando un'altra scheda viene visualizzato l' [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto che rappresenta la scheda:

[![Screenshot di un TabbedPage contenente schede, in iOS e Android](tabbed-page-images/tabbedpage-week.png "TabbedPage con schede")](tabbed-page-images/tabbedpage-week-large.png#lightbox "TabbedPage con schede")

Nella scheda **pianificazione** l' [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto viene incluso in un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) oggetto.

> [!WARNING]
> Mentre un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) può essere inserito in un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , non è consigliabile inserire un oggetto `TabbedPage` in un oggetto `NavigationPage` . Ciò è dovuto al fatto che in iOS un elemento `UITabBarController` funge sempre da wrapper per `UINavigationController`. Per altre informazioni, vedere [Combined View Controller Interfaces](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) (Interfacce combinate del controller di visualizzazione) nella libreria per sviluppatori iOS.

## <a name="navigate-within-a-tab"></a>Spostarsi all'interno di una scheda

La navigazione può essere eseguita all'interno di una scheda, a condizione che l' [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto sia racchiuso in un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) oggetto. Questa operazione viene eseguita richiamando il [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) Metodo sulla [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà dell' [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto:

```csharp
await Navigation.PushAsync (new UpcomingAppointmentsPage ());
```

La pagina a cui si sta eseguendo lo spostamento viene specificata come argomento del [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) metodo. In questo esempio viene eseguito il `UpcomingAppointmentsPage` push della pagina nello stack di navigazione, in cui diventa la pagina attiva:

[![Screenshot della navigazione in una scheda, in iOS e Android](tabbed-page-images/tabbedpage-upcoming.png "Navigazione TabbedPage in una scheda")](tabbed-page-images/tabbedpage-upcoming-large.png#lightbox "Navigazione TabbedPage in una scheda")

Per ulteriori informazioni sull'esecuzione della navigazione utilizzando la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe, vedere [navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="populate-a-tabbedpage-with-a-template"></a>Popolamento di un TabbedPage con un modello

Un oggetto [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) può essere popolato con le pagine assegnando una raccolta di dati alla [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) proprietà e assegnando un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) alla proprietà che consente di definire [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) i modelli dei dati come [`Page`](xref:Xamarin.Forms.Page) oggetti. Questa operazione viene eseguita in XAML come indicato di seguito:

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

In questo esempio, ogni scheda è costituita da un [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto che usa [`Image`](xref:Xamarin.Forms.Image) [`Label`](xref:Xamarin.Forms.Label) gli oggetti e per visualizzare i dati per la scheda:

[![Screenshot di un TabbedPage basato su modelli, in iOS e Android](tabbed-page-images/tabbedpage-template.png "TabbedPage basato su modelli")](tabbed-page-images/tabbedpage-template-large.png#lightbox "TabbedPage basato su modelli")

Selezionando un'altra scheda viene visualizzato l' [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto che rappresenta la scheda.

## <a name="related-links"></a>Collegamenti correlati

- [TabbedPageWithNavigationPage (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpagewithnavigationpage)
- [TabbedPage (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-tabbedpage)
- [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)
- [Tipi di pagina](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [API TabbedPage](xref:Xamarin.Forms.TabbedPage)
