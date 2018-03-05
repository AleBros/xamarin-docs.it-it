---
title: Pagina a schede
description: "Il TabbedPage xamarin. Forms è costituito da un elenco di schede e un'area più grande di dettaglio, con ogni scheda, il caricamento del contenuto nell'area dei dettagli. In questo articolo viene illustrato come utilizzare un TabbedPage per spostarsi di una raccolta di pagine."
ms.topic: article
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: 97ca114f1160168c7fd9439e31dc475bc37b0467
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="tabbed-page"></a>Pagina a schede

_Il TabbedPage xamarin. Forms è costituito da un elenco di schede e un'area più grande di dettaglio, con ogni scheda, il caricamento del contenuto nell'area dei dettagli. In questo articolo viene illustrato come utilizzare un TabbedPage per spostarsi di una raccolta di pagine._

## <a name="overview"></a>Panoramica

Le schermate seguenti viene illustrato un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) in ogni piattaforma:

![](tabbed-page-images/tab1.png "Esempio TabbedPage")

Le seguenti catture concentrano sul formato scheda in ogni piattaforma:

![](tabbed-page-images/tabbedpage-components.png "TabbedPage scheda componenti")

Il layout di un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)e le relative schede, dipende dalla piattaforma:

- In iOS, verrà visualizzato l'elenco delle schede nella parte inferiore della schermata e nell'area dettaglio è sopra. Ogni scheda è inoltre presente un'icona che deve essere un 30 x 30 PNG con trasparenza per la normale risoluzione 60x60 per ad alta risoluzione e 90 x 90 per iPhone 6 Plus risoluzione. Se sono presenti più di cinque schede, una *più* verrà visualizzata la scheda, che può essere utilizzato per accedere le schede aggiuntive. Per ulteriori informazioni sul caricamento di immagini in un'applicazione di xamarin. Forms, vedere [utilizzo delle immagini](~/xamarin-forms/user-interface/images.md). Per ulteriori informazioni sui requisiti di icona, vedere [la creazione di applicazioni a schede](~/ios/user-interface/controls/creating-tabbed-applications.md).

    > [!NOTE]
  > Si noti che il `TabbedRenderer` per iOS è un override `GetIcon` metodo che può essere utilizzato per caricare le icone scheda da un'origine specificata. Questa sostituzione consente di utilizzare le immagini SVG sotto forma di icone in una `TabbedPage`. Inoltre, possono essere fornite selezionate e le versioni di un'icona.

- In Android, verrà visualizzato l'elenco delle schede nella parte superiore della schermata e nell'area dettaglio è inferiore. I nomi delle schede sono automaticamente in maiuscolo e l'utente può scorrere la raccolta di schede se vi sono troppi per essere contenuta in una schermata.

    > [!NOTE]
  > Si noti che quando si utilizza una delle applicazioni in Android, ogni scheda verrà anche visualizzata un'icona. Inoltre, il `TabbedPageRenderer` per Android delle applicazioni presenta un sottoponibile a override `SetTabIcon` metodo che può essere utilizzato per caricare le icone scheda da un oggetto personalizzato `Drawable`. Questa sostituzione consente di utilizzare le immagini SVG sotto forma di icone in una `TabbedPage`.

- In Windows Phone, viene visualizzato l'elenco delle schede nella parte superiore della schermata e nell'area dettaglio è inferiore. La scheda nomi vengono convertiti automaticamente in minuscole e l'utente può scorrere la raccolta di schede se vi sono troppi per essere contenuta in una schermata.
- In Windows tablet fattori di forma, le schede non sono sempre visibili e gli utenti devono scorrere verso il basso (o pulsante destro del mouse, se hanno un mouse collegato) per visualizzare le schede in un `TabbedPage` (come illustrato di seguito).

![](tabbed-page-images/windows-tabs.png "TabbedPage schede in Windows")

## <a name="creating-a-tabbedpage"></a>Creazione di un TabbedPage

Due approcci possono essere utilizzati per creare un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/):

- [Popolare](#Populating_a_TabbedPage_with_a_Page_Collection) il [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) con una raccolta di figlio [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) oggetti, ad esempio una raccolta di [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) istanze.
- [Assegnare](#Populating_a_TabbedPage_with_a_Template) una raccolta per il [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/) proprietà e assegnare un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) per il [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/) proprietà da restituire per le pagine oggetti nella raccolta.

Con entrambi gli approcci di [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) visualizzerà ogni pagina quando l'utente sceglie ogni scheda.

> [!NOTE]
> È consigliabile un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) deve essere popolato con [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) e [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)solo istanze. Ciò è utile per garantire un'esperienza utente coerente in tutte le piattaforme.

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>Popolamento di un TabbedPage con una raccolta di pagina

Nell'esempio di codice XAML seguente viene un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) costruito da popolare con una raccolta di figlio [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) oggetti:

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

L'esempio di codice seguente mostra l'equivalente [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) creato in c#:

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

Il [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) viene popolato con due figlio [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) oggetti. Il primo figlio è un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) istanza e la seconda scheda è un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) contenente un `ContentPage` istanza.

> [!NOTE]
> **Nota**: il [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) non supporta la virtualizzazione dell'interfaccia utente. Pertanto, le prestazioni possono essere interessate se la `TabbedPage` contiene troppi elementi figlio.

Le schermate seguenti viene illustrato il `TodayPage` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) istanza, viene visualizzato sul *oggi* scheda:

![](tabbed-page-images/today-page.png "In un TabbedPage ContentPage")

Selezionando il *pianificazione* scheda consente di visualizzare il `SchedulePage` [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) istanza, che viene eseguito il wrapping in un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) istanza e viene visualizzata di cattura di schermata seguente:

![](tabbed-page-images/schedule-page.png "In un TabbedPage NavigationPage")

Per informazioni sul layout di un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), vedere [esecuzione navigazione](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

> [!NOTE]
> Mentre è possibile inserire un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) in un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/), non è consigliabile inserire un `TabbedPage` in un `NavigationPage`. Questo avviene perché, in iOS, una `UITabBarController` sempre funge da wrapper per il `UINavigationController`. Per ulteriori informazioni, vedere [combinati Visualizza Controller interfacce](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) in iOS Developer Library.

#### <a name="navigation-inside-a-tab"></a>Navigazione all'interno di una scheda

Navigazione può essere eseguita dalla seconda scheda richiamando il [ `PushAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)/) metodo il [ `Navigation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) proprietà del [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) istanza, come illustrato nell'esempio di codice seguente:

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

Di conseguenza l'istanza `UpcomingAppointmentsPage` viene inserita tramite push nello stack di navigazione, in cui diventa la pagina attiva. Come illustrato nelle schermate seguenti:

![](tabbed-page-images/navigationpage.png "Navigazione all'interno di una scheda")

Per ulteriori informazioni sull'esecuzione di navigazione tramite il [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) classe, vedere [navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>Popolamento di un TabbedPage con un modello

Nell'esempio di codice XAML seguente viene un [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) costruito assegnando un [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) per il [ `ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemTemplate/) proprietà da restituire per le pagine oggetti nella raccolta:

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

Il [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) viene popolato con i dati impostando il [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiPage%601.ItemsSource/) proprietà nel costruttore per il file code-behind:

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

L'esempio di codice seguente mostra l'equivalente [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) creato in c#:

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

Ogni scheda vengono visualizzate una [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) che usa una serie di [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) e [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) istanze per visualizzare i dati per la scheda. La schermata seguente mostra il contenuto per il *Tamarin* scheda:

![](tabbed-page-images/tab3.png "Popolamento di un TabbedPage con un modello")

Selezionare quindi un'altra scheda Visualizza il contenuto per tale scheda.

> [!NOTE]
> Il [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) non supporta la virtualizzazione dell'interfaccia utente. Pertanto, le prestazioni possono essere interessate se la `TabbedPage` contiene troppi elementi figlio.

Per ulteriori informazioni sul [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/), vedere [capitolo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) di libro di Petzolds xamarin. Forms.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare un TabbedPage per spostarsi di una raccolta di pagine. Di xamarin. Forms [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) è costituito da un elenco di schede e un'area più grande di dettaglio, con ogni scheda, il caricamento del contenuto nell'area dei dettagli.


## <a name="related-links"></a>Collegamenti correlati

- [Tipi di pagina](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage (sample)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)
