---
title: Pagina a schede di xamarin. Forms
description: TabbedPage di xamarin. Forms è costituito da un elenco di schede e un'area più grande di dettaglio, con ogni scheda di caricamento del contenuto nell'area dei dettagli. Questo articolo illustra come usare un TabbedPage per scorrere una raccolta di pagine.
ms.prod: xamarin
ms.assetid: C946057F-C77C-412D-82A0-DAF475A24EF5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2017
ms.openlocfilehash: 3eb978780222da2050fc91dfa41c68ef4bd3b6f4
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996295"
---
# <a name="xamarinforms-tabbed-page"></a>Pagina a schede di xamarin. Forms

_TabbedPage di xamarin. Forms è costituito da un elenco di schede e un'area più grande di dettaglio, con ogni scheda di caricamento del contenuto nell'area dei dettagli. Questo articolo illustra come usare un TabbedPage per scorrere una raccolta di pagine._

## <a name="overview"></a>Panoramica

L'esempio di screenshot seguente mostra una [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) in ogni piattaforma:

![](tabbed-page-images/tab1.png "Esempio TabbedPage")

Le schermate seguenti si concentrano sul formato di scheda in ogni piattaforma:

![](tabbed-page-images/tabbedpage-components.png "TabbedPage scheda componenti")

Il layout di un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)e le relative schede dipende dalla piattaforma:

- In iOS, verrà visualizzato l'elenco di schede nella parte inferiore dello schermo e l'area dei dettagli è sopra. Ogni scheda è presente anche un'immagine dell'icona che deve essere un 30 x 30 PNG con trasparenza per la risoluzione del normale, 60x60 per ad alta risoluzione e 90 x 90 per iPhone 6 Plus risoluzione. Se sono presenti più di cinque schede, una *altre* verrà visualizzata la scheda, che può essere utilizzato per accedere le schede aggiuntive. Per altre informazioni sul caricamento di immagini in un'applicazione xamarin. Forms, vedere [utilizzo di immagini](~/xamarin-forms/user-interface/images.md). Per altre informazioni sui requisiti di icona, vedere [creazione di applicazioni a schede](~/ios/user-interface/controls/creating-tabbed-applications.md).

    > [!NOTE]
  > Si noti che il `TabbedRenderer` per iOS ha un sottoponibile a override `GetIcon` metodo che può essere utilizzato per caricare le icone di scheda da un'origine specificata. Questa sostituzione consente di usare le immagini SVG come icone su una `TabbedPage`. Inoltre, possono essere fornite selezionate e le versioni di un'icona.

- In Android, viene visualizzato l'elenco di schede nella parte superiore della schermata per impostazione predefinita, e l'area dei dettagli è inferiore. Elenco di schede, tuttavia, può essere spostato nella parte inferiore della schermata con una specifica della piattaforma. Per altre informazioni, vedere [posizionamento di impostazione TabbedPage sulla barra degli strumenti e il colore](~/xamarin-forms/platform/platform-specifics/consuming/android.md#tabbedpage-toolbar).

    > [!NOTE]
  > Si noti che quando si usa AppCompat in Android, ciascuna scheda verrà anche visualizzata un'icona. Inoltre, il `TabbedPageRenderer` per AppCompat Android ha un sottoponibile a override `SetTabIcon` metodo che può essere utilizzato per caricare le icone di scheda da una classe personalizzata `Drawable`. Questa sostituzione consente di usare le immagini SVG come icone su una `TabbedPage`.

- In Windows tablet fattori di forma, le schede non sono sempre visibili e gli utenti devono scorrere rapidamente verso il basso (o destro del mouse e se dispongono di un mouse collegato) per visualizzare le schede in una `TabbedPage` (come illustrato di seguito).

![](tabbed-page-images/windows-tabs.png "Schede TabbedPage su Windows")

## <a name="creating-a-tabbedpage"></a>Creazione di un TabbedPage

Due approcci possono essere utilizzati per creare un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage):

- [Popolare](#Populating_a_TabbedPage_with_a_Page_Collection) il [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) con una raccolta di elemento figlio [ `Page` ](xref:Xamarin.Forms.Page) oggetti, ad esempio una raccolta di [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) istanze.
- [Assegnare](#Populating_a_TabbedPage_with_a_Template) una raccolta per il [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) proprietà e assegnare una [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) per il [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) proprietà per restituire le pagine per oggetti nella raccolta.

Con entrambi gli approcci, il [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) visualizzerà ogni pagina quando l'utente seleziona ogni scheda.

> [!NOTE]
> È consigliabile che una [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) deve essere popolato con [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) e [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)solo istanze. Ciò è utile per garantire un'esperienza utente uniforme tra tutte le piattaforme.

<a name="Populating_a_TabbedPage_with_a_Page_Collection" />

### <a name="populating-a-tabbedpage-with-a-page-collection"></a>Popolamento di un TabbedPage con una raccolta di pagina

L'esempio di codice XAML seguente mostra una [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) costruito dal popolarlo con una raccolta di figlio [ `Page` ](xref:Xamarin.Forms.Page) oggetti:

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

Esempio di codice seguente viene illustrato l'equivalente [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) creato in c#:

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

Il [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) viene popolata con l'elemento figlio due [ `Page` ](xref:Xamarin.Forms.Page) oggetti. Il primo elemento figlio è un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) istanza, mentre la seconda scheda è un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) contenente una `ContentPage` istanza.

> [!NOTE]
> Il [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) non supporta la virtualizzazione dell'interfaccia utente. Di conseguenza, le prestazioni possono essere interessate se la `TabbedPage` contiene troppi elementi figlio.

Gli screenshot seguenti viene illustrato il `TodayPage` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) istanza, che viene visualizzato sul *oggi* scheda:

![](tabbed-page-images/today-page.png "In un TabbedPage ContentPage")

Selezionando il *pianificazione* scheda consente di visualizzare i `SchedulePage` [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) istanza, che viene eseguito il wrapping in un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) dell'istanza e viene visualizzata nel schermata seguente:

![](tabbed-page-images/schedule-page.png "NavigationPage in un TabbedPage")

Per informazioni sul layout di un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), vedere [esecuzione navigazione](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

> [!NOTE]
> Benché sia accettabile per inserire un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) in un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), non è consigliabile inserire un `TabbedPage` in un `NavigationPage`. Questo avviene perché, in iOS, una `UITabBarController` sempre funge da wrapper per il `UINavigationController`. Per altre informazioni, vedere [combinare interfacce del Controller di visualizzazione](https://developer.apple.com/library/ios/documentation/WindowsViews/Conceptual/ViewControllerCatalog/Chapters/CombiningViewControllers.html) in iOS Developer Library.

#### <a name="navigation-inside-a-tab"></a>Navigazione all'interno di una scheda

Navigazione può essere eseguita dalla seconda scheda richiamando il [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage.PushAsync*) metodo sul [ `Navigation` ](xref:Xamarin.Forms.VisualElement.Navigation) proprietà del [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) istanza, come illustrato nell'esempio di codice seguente:

```csharp
async void OnUpcomingAppointmentsButtonClicked (object sender, EventArgs e)
{
  await Navigation.PushAsync (new UpcomingAppointmentsPage ());
}
```

Di conseguenza l'istanza `UpcomingAppointmentsPage` viene inserita tramite push nello stack di navigazione, in cui diventa la pagina attiva. Come illustrato negli screenshot seguenti:

![](tabbed-page-images/navigationpage.png "Navigazione all'interno di una scheda")

Per altre informazioni sull'esecuzione di navigazione utilizzando la [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) classe, vedere [navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Populating_a_TabbedPage_with_a_Template" />

### <a name="populating-a-tabbedpage-with-a-template"></a>Popolamento di un TabbedPage con un modello

L'esempio di codice XAML seguente mostra una [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) costruito tramite l'assegnazione di un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) per il [ `ItemTemplate` ](xref:Xamarin.Forms.MultiPage`1.ItemTemplate) proprietà per restituire le pagine per oggetti nella raccolta:

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

Il [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) viene popolata con i dati impostando i [ `ItemsSource` ](xref:Xamarin.Forms.MultiPage`1.ItemsSource) proprietà nel costruttore per il file code-behind:

```csharp
public TabbedPageDemoPage ()
{
  ...
  ItemsSource = MonkeyDataModel.All;
}
```

Esempio di codice seguente viene illustrato l'equivalente [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) creato in c#:

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

Ogni scheda vengono visualizzate una [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) che usa una serie di [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) e [ `Label` ](xref:Xamarin.Forms.Label) istanze per visualizzare i dati per la scheda. Gli screenshot seguenti mostrano il contenuto per il *Tamarin* scheda:

![](tabbed-page-images/tab3.png "Popolamento di un TabbedPage con un modello")

Selezionare quindi un'altra scheda Visualizza il contenuto per tale scheda.

> [!NOTE]
> Il [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) non supporta la virtualizzazione dell'interfaccia utente. Di conseguenza, le prestazioni possono essere interessate se la `TabbedPage` contiene troppi elementi figlio.

Per altre informazioni sul [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), vedere [capitolo 25](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf) del libro di xamarin. Forms Petzolds.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare un TabbedPage per scorrere una raccolta di pagine. Xamarin. Forms [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) è costituito da un elenco di schede e un'area più grande di dettaglio, con ogni scheda di caricamento del contenuto nell'area dei dettagli.


## <a name="related-links"></a>Collegamenti correlati

- [Tipi di pagina](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [TabbedPageWithNavigationPage (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPageWithNavigationPage)
- [TabbedPage (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/TabbedPage/)
- [TabbedPage](xref:Xamarin.Forms.TabbedPage)
