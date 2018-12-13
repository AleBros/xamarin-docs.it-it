---
title: Pagina a schede di Xamarin.Forms
description: TabbedPage di Xamarin.Forms è costituita da un elenco di schede e un'area dei dettagli più grande con ogni scheda che carica il contenuto nell'area dei dettagli. Questo articolo illustra come usare una classe TabbedPage per spostarsi in una raccolta di pagine.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 85a6bce8a1021c75064ba06f3a5daf69b7fe3e57
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171378"
---
# <a name="xamarinforms-tabbed-page"></a>Pagina a schede di Xamarin.Forms

_TabbedPage di Xamarin.Forms è costituita da un elenco di schede e un'area dei dettagli più grande con ogni scheda che carica il contenuto nell'area dei dettagli. Questo articolo illustra come usare una classe TabbedPage per spostarsi in una raccolta di pagine._

## <a name="overview"></a>Panoramica

Gli screenshot seguenti illustrano una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) in ogni piattaforma:

![](tabbed-page-images/tab1.png "Esempio di TabbedPage")

Gli screenshot seguenti illustrano il formato della scheda in ogni piattaforma:

![](tabbed-page-images/tabbedpage-components.png "Componenti scheda di TabbedPage")

Il layout di una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) e delle relative schede dipende dalla piattaforma:

- In iOS l'elenco delle schede è visualizzato nella parte inferiore dello schermo e l'area dei dettagli è al di sopra di essa. In ogni scheda è presente anche un'immagine di icona in formato PNG 30x30 con trasparenza per la risoluzione normale, 60x60 per l'alta risoluzione e 90x90 per la risoluzione iPhone 6 Plus. Se sono disponibili più di cinque schede, verrà visualizzata una scheda *Altro* che può essere usata per accedere alle altre schede. Per altre informazioni su come caricare immagini in un'applicazione Xamarin.Forms, vedere [Uso delle immagini](~/xamarin-forms/user-interface/images.md). Per altre informazioni sui requisiti delle icone, vedere [Creating Tabbed Applications](~/ios/user-interface/controls/creating-tabbed-applications.md) (Creazione di applicazioni a schede).

  > [!NOTE]
  > Si noti che l'elemento `TabbedRenderer` per iOS include un metodo `GetIcon` sottoponibile a override che può essere usato per caricare le icone delle schede da un'origine specificata. Questo override rende possibile l'uso di immagini SVG come icone in una `TabbedPage`. È anche possibile specificare versioni selezionate e non selezionate di un'icona.

- In Android l'elenco delle schede è visualizzato, per impostazione predefinita, nella parte superiore dello schermo e l'area dei dettagli è al di sotto di essa. L'elenco di schede, tuttavia, può essere spostato nella parte inferiore dello schermo con una specifica della piattaforma. Per altre informazioni, vedere [Setting TabbedPage Toolbar Placement and Color](~/xamarin-forms/platform/platform-specifics/consuming/android.md#tabbedpage-toolbar) (Impostazione del posizionamento e del colore della barra degli strumenti di TabbedPage).

  > [!NOTE]
  > Si noti che quando si usa AppCompat in Android, ogni scheda visualizzerà anche un'icona. L'elemento `TabbedPageRenderer` per Android AppCompat include inoltre un metodo `GetIconDrawable` sottoponibile a override che può essere usato per caricare le icone delle schede da un elemento `Drawable` personalizzato. Questo override rende possibile l'uso di immagini SVG come icone in una `TabbedPage` e funziona sia con le barre delle schede superiori sia con quelle inferiori. In alternativa, si può usare il metodo `SetTabIcon` sottoponibile a override per caricare le icone delle schede da un elemento `Drawable` personalizzato per le barre delle schede superiori.

- Nei fattori di forma tablet Windows le schede non sono sempre visibili e per visualizzare le schede in una `TabbedPage` gli utenti devono scorrere verso il basso o fare clic con il pulsante destro del mouse, se è collegato un mouse, come illustrato di seguito.

![](tabbed-page-images/windows-tabs.png "Schede TabbedPage in Windows")

## <a name="creating-a-tabbedpage"></a>Creazione di una classe TabbedPage

Per creare una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) è possibile usare due approcci:

- [Popolare](#Populating_a_TabbedPage_with_a_Page_Collection) la [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) con una raccolta di oggetti [`Page`](xref:Xamarin.Forms.Page) figlio, ad esempio una raccolta di istanze di [`ContentPage`](xref:Xamarin.Forms.ContentPage).
- [Assegnare](#Populating_a_TabbedPage_with_a_Template) una raccolta alla proprietà [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) e assegnare una classe [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) alla proprietà [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) per restituire le pagine per gli oggetti della raccolta.

Con entrambi gli approcci, la classe [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) visualizzerà ogni pagina al momento della selezione di ogni scheda da parte dell'utente.

> [!NOTE]
> È consigliabile popolare una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) solo con istanze di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e [`ContentPage`](xref:Xamarin.Forms.ContentPage). In questo modo si potrà garantire un'esperienza utente uniforme su tutte le piattaforme.

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>Popolamento di una classe TabbedPage con una raccolta di pagine

L'esempio di codice XAML seguente illustra una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) costruita popolandola con una raccolta di oggetti [`Page`](xref:Xamarin.Forms.Page) figlio:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
            xmlns:local="clr-namespace:TabbedPageWithNavigationPage;assembly=TabbedPageWithNavigationPage"
            x:Class="TabbedPageWithNavigationPage.MainPage">
    <local:TodayPage />
    <NavigationPage Title="Schedule" Icon="schedule.png">
        <x:Arguments>
            <local:SchedulePage />
        </x:Arguments>
    </NavigationPage>
</TabbedPage>
```

L'esempio di codice seguente illustra la [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) equivalente creata in C#:

```csharp
public class MainPageCS : TabbedPage
{
  public MainPageCS ()
  {
    var navigationPage = new NavigationPage (new SchedulePageCS ());
    navigationPage.Icon = "schedule.png";
    navigationPage.Title = "Schedule";

    Children.Add (new TodayPageCS ());
    Children.Add (navigationPage);
  }
}
```

La [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) viene popolata con due oggetti [`Page`](xref:Xamarin.Forms.Page) figlio. Il primo elemento figlio è un'istanza di [`ContentPage`](xref:Xamarin.Forms.ContentPage) e la seconda scheda è una [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) contenente un'istanza di `ContentPage`.

> [!NOTE]
> La classe [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) non supporta la virtualizzazione dell'interfaccia utente. Di conseguenza, la presenza di troppi elementi figlio nella `TabbedPage` può influire sulle prestazioni.

Gli screenshot seguenti illustrano l'istanza `TodayPage` [`ContentPage`](xref:Xamarin.Forms.ContentPage) visualizzata nella scheda *Today* (Oggi):

![](tabbed-page-images/today-page.png "ContentPage in una TabbedPage")

Se si seleziona la scheda *Schedule* (Pianificazione) viene visualizzata l'istanza `SchedulePage` [`ContentPage`](xref:Xamarin.Forms.ContentPage), di cui è stato eseguito il wrapping in un'istanza di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e che viene illustrata nello screenshot seguente:

![](tabbed-page-images/schedule-page.png "NavigationPage in una TabbedPage")

Per informazioni sul layout di una [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), vedere [Performing Navigation](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md) (Esecuzione della navigazione).

> [!NOTE]
> Anche se è accettabile inserire una [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) in una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), non è consigliabile inserire una `TabbedPage` in una `NavigationPage`. Ciò è dovuto al fatto che in iOS un elemento `UITabBarController` funge sempre da wrapper per `UINavigationController`. Per altre informazioni, vedere [Combined View Controller Interfaces](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) (Interfacce combinate del controller di visualizzazione) nella libreria per sviluppatori iOS.

#### <a name="navigation-inside-a-tab"></a>Navigazione all'interno di una scheda

La navigazione può essere eseguita dalla seconda scheda richiamando il metodo [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync*) sulla proprietà [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) dell'istanza di [`ContentPage`](xref:Xamarin.Forms.ContentPage), come illustrato nell'esempio di codice seguente:

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

Di conseguenza l'istanza `UpcomingAppointmentsPage` viene inserita tramite push nello stack di navigazione, in cui diventa la pagina attiva. Ciò viene illustrato negli screenshot seguenti:

![](tabbed-page-images/navigationpage.png "Navigazione all'interno di una scheda")

Per altre informazioni sull'esecuzione della navigazione tramite la classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), vedere [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>Popolamento di una classe TabbedPage con un modello

L'esempio di codice XAML seguente illustra una [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) costruita assegnando una classe [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) alla proprietà [`ItemTemplate`](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) per restituire le pagine per gli oggetti della raccolta:

```xaml
<TabbedPage xmlns="http://xamarin.com/schemas/2014/forms"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:local="clr-namespace:TabbedPageDemo;assembly=TabbedPageDemo"
            x:Class="TabbedPageDemo.TabbedPageDemoPage">
  <TabbedPage.Resources>
    <ResourceDictionary>
      <local:NonNullToBooleanConverter x:Key="booleanConverter" />
    </ResourceDictionary>
  </TabbedPage.Resources>
  <TabbedPage.ItemTemplate>
    <DataTemplate>
      <ContentPage Title="{Binding Name}" Icon="monkeyicon.png">
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

La [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) viene popolata con i dati impostando la proprietà [`ItemsSource`](xref:Xamarin.Forms.MultiPage`1.ItemsSource) nel costruttore per il file code-behind:

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

L'esempio di codice seguente illustra la [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) equivalente creata in C#:

```csharp
public class TabbedPageDemoPageCS : TabbedPage
{
  public TabbedPageDemoPageCS ()
  {
    var booleanConverter = new NonNullToBooleanConverter ();

    ItemTemplate = new DataTemplate (() => {
      var nameLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label)),
        FontAttributes = FontAttributes.Bold,
        HorizontalOptions = LayoutOptions.Center
      };
      nameLabel.SetBinding (Label.TextProperty, "Name");

      var image = new Image { WidthRequest = 200, HeightRequest = 200 };
      image.SetBinding (Image.SourceProperty, "PhotoUrl");

      var familyLabel = new Label {
        FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
        FontAttributes = FontAttributes.Bold
      };
      familyLabel.SetBinding (Label.TextProperty, "Family");
      ...

      var contentPage = new ContentPage {
        Icon = "monkeyicon.png",
        Content = new StackLayout {
          Padding = new Thickness (5, 25),
          Children = {
            nameLabel,
            image,
            new StackLayout {
              Padding = new Thickness (50, 10),
              Children = {
                new StackLayout {
                  Orientation = StackOrientation.Horizontal,
                  Children = {
                    new Label { Text = "Family:", HorizontalOptions = LayoutOptions.FillAndExpand },
                    familyLabel
                  }
                },
                ...
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

Ogni scheda visualizza una classe [`ContentPage`](xref:Xamarin.Forms.ContentPage) che usa una serie di istanze di [`StackLayout`](xref:Xamarin.Forms.StackLayout) e [`Label`](xref:Xamarin.Forms.Label) per visualizzare i dati per la scheda. Gli screenshot seguenti illustrano il contenuto per la scheda *Tamarin* (Saguino):

![](tabbed-page-images/tab3.png "Popolamento di una TabbedPage con un modello")

La selezione di un'altra scheda consente di visualizzarne il contenuto.

> [!NOTE]
> La classe [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) non supporta la virtualizzazione dell'interfaccia utente. Di conseguenza, la presenza di troppi elementi figlio nella `TabbedPage` può influire sulle prestazioni.

Per altre informazioni sulla [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), vedere il [capitolo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del manuale di Xamarin.Forms redatto da Charles Petzold.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato l'uso di una classe TabbedPage per spostarsi in una raccolta di pagine. [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) di Xamarin.Forms è costituita da un elenco di schede e un'area dei dettagli più grande con ogni scheda che carica il contenuto nell'area dei dettagli.


## <a name="related-links"></a>Collegamenti correlati

- [Tipi di pagina](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](xref:Xamarin.Forms.TabbedPage)
