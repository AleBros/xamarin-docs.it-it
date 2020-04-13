---
title: Pagina master-dettagli di Xamarin.Forms
description: 'MasterDetailPage di Xamarin.Forms è una pagina che gestisce due pagine correlate di informazioni: una pagina master che presenta gli elementi e una pagina di dettaglio che presenta i dettagli relativi agli elementi della pagina master. Questo articolo illustra come usare una classe MasterDetailPage e come spostarsi tra le pagine di informazioni.'
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 702ec35bca051f7255c5c9d67d2dc68d4f89ca52
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "68645960"
---
# <a name="xamarinforms-master-detail-page"></a>Pagina master-dettagli di Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)

_Il Xamarin.Forms MasterDetailPage è una pagina che gestisce due pagine correlate di informazioni, ovvero una pagina master che presenta gli elementi e una pagina di dettaglio che presenta i dettagli sugli elementi nella pagina master. In questo articolo viene illustrato come utilizzare un MasterDetailPage e spostarsi tra le relative pagine di informazioni._

## <a name="overview"></a>Panoramica

Una pagina master in genere visualizza un elenco di elementi, come illustrato negli screenshot seguenti:

[![](master-detail-page-images/masterpage-components.png "Master Page Components")](master-detail-page-images/masterpage-components-large.png#lightbox "Master Page Components")

La posizione dell'elenco di elementi è identica in ogni piattaforma e la selezione di uno degli elementi consente di passare alla pagina di dettaglio corrispondente. Nella pagina master è inoltre disponibile una barra di navigazione contenente un pulsante che può essere usato per passare alla pagina di dettaglio attiva:

- In iOS la barra di navigazione si trova nella parte superiore della pagina e include un pulsante che consente di passare alla pagina di dettaglio. È possibile passare alla pagina di dettaglio attiva anche scorrendo rapidamente la pagina master verso sinistra.
- In Android la barra di navigazione si trova nella parte superiore della pagina e visualizza un titolo, un'icona e un pulsante che consente di passare alla pagina di dettaglio. L'icona è definita nell'attributo `[Activity]` che decora la classe `MainActivity` nel progetto specifico della piattaforma Android. È possibile passare alla pagina di dettaglio attiva anche scorrendo rapidamente la pagina master verso sinistra, toccando la pagina di dettaglio all'estremità destra dello schermo e toccando il pulsante *Indietro* nella parte inferiore dello schermo.
- Nella piattaforma UWP (Universal Windows Platform) la barra di navigazione si trova nella parte superiore della pagina e include un pulsante che consente di passare alla pagina di dettaglio.

Un pagina di dettaglio visualizza i dati corrispondenti all'elemento selezionato nella pagina master. I componenti principali della pagina di dettaglio sono illustrati negli screenshot seguenti:

![](master-detail-page-images/detailpage-components.png "Detail Page Components")

La pagina di dettaglio contiene una barra di navigazione il cui contenuto dipende dalla piattaforma:

- In iOS, la barra di spostamento è presente nella parte superiore della pagina e visualizza un titolo e dispone di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) un pulsante che torna alla pagina master, a condizione che l'istanza della pagina di dettaglio viene eseguito il wrapping nell'istanza. È possibile tornare alla pagina master anche scorrendo rapidamente la pagina di dettaglio verso destra.
- In Android è disponibile una barra di navigazione nella parte superiore della pagina in cui sono visualizzati un titolo, un'icona e un pulsante che consente di tornare alla pagina master. L'icona è definita nell'attributo `[Activity]` che decora la classe `MainActivity` nel progetto specifico della piattaforma Android.
- In UWP la barra di navigazione si trova nella parte superiore della pagina e include un titolo e un pulsante che consente di tornare alla pagina master.

### <a name="navigation-behavior"></a>Comportamento di navigazione

Il comportamento dell'esperienza di navigazione tra le pagine master e di dettaglio dipende dalla piattaforma:

- In iOS la pagina di dettaglio *scorre* verso destra mentre la pagina master scorre verso sinistra e la parte sinistra della pagina di dettaglio è ancora visibile.
- In Android le pagine di dettaglio e master vengono *sovrapposte* le une alle altre.
- Nella piattaforma UWP, la pagina master scorre dalla parte rimanente della pagina di dettaglio, a condizione che la [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) proprietà sia impostata su `Popover`. Per altre informazioni, vedere [Controllo del comportamento di visualizzazione della pagina di dettaglio](#Controlling_the_Detail_Page_Display_Behavior).

Un comportamento simile si osserverà in modalità orizzontale, ad eccezione del fatto che la pagina master in iOS e Android ha una larghezza simile alla pagina master in modalità verticale, quindi sarà visibile una parte maggiore della pagina di dettaglio.

Per informazioni su come controllare il comportamento di navigazione, vedere [Controllo del comportamento di visualizzazione della pagina di dettaglio](#Controlling_the_Detail_Page_Display_Behavior).

## <a name="creating-a-masterdetailpage"></a>Creazione di una classe MasterDetailPage

Un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) contiene [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) e le proprietà [`Page`](xref:Xamarin.Forms.Page)che sono entrambe di tipo , che vengono utilizzate per ottenere e impostare rispettivamente le pagine master e di dettaglio.

> [!IMPORTANT]
> Un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) oggetto è progettato per essere una pagina radice e l'utilizzo come pagina figlio in altri tipi di pagina potrebbe causare un comportamento imprevisto e incoerente. Inoltre, è consigliabile che la pagina [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) master di [`ContentPage`](xref:Xamarin.Forms.ContentPage) un oggetto sia sempre un'istanza [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)e `ContentPage` che la pagina di dettaglio venga popolata solo con , e istanze. In questo modo si potrà garantire un'esperienza utente uniforme su tutte le piattaforme.

L'esempio di codice [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) XAML [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) seguente [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) mostra un che imposta le proprietà e:

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  xmlns:local="clr-namespace:MasterDetailPageNavigation;assembly=MasterDetailPageNavigation"
                  x:Class="MasterDetailPageNavigation.MainPage">
    <MasterDetailPage.Master>
        <local:MasterPage x:Name="masterPage" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage>
            <x:Arguments>
                <local:ContactsPage />
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

Nell'esempio di codice [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) riportato di seguito viene illustrato l'equivalente creato in C:

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        masterPage = new MasterPageCS ();
        Master = masterPage;
        Detail = new NavigationPage (new ContactsPageCS ());
        ...
    }
    ...
}
```

La [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) proprietà è [`ContentPage`](xref:Xamarin.Forms.ContentPage) impostata su un'istanza. La [`MasterDetailPage.Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) proprietà è [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) impostata `ContentPage` su un contenente un'istanza.

### <a name="creating-the-master-page"></a>Creazione della pagina master

Nell'esempio di codice XAML `MasterPage` seguente viene illustrata la [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) dichiarazione dell'oggetto, a cui viene fatto riferimento tramite la proprietà:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             IconImageSource="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView" x:FieldModifier="public">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:MasterPageItem}">
                    <local:MasterPageItem Title="Contacts" IconSource="contacts.png" TargetType="{x:Type local:ContactsPage}" />
                    <local:MasterPageItem Title="TodoList" IconSource="todo.png" TargetType="{x:Type local:TodoListPage}" />
                    <local:MasterPageItem Title="Reminders" IconSource="reminders.png" TargetType="{x:Type local:ReminderPage}" />
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid Padding="5,10">
                            <Grid.ColumnDefinitions>
                                <ColumnDefinition Width="30"/>
                                <ColumnDefinition Width="*" />
                            </Grid.ColumnDefinitions>
                            <Image Source="{Binding IconSource}" />
                            <Label Grid.Column="1" Text="{Binding Title}" />
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

La pagina è [`ListView`](xref:Xamarin.Forms.ListView) costituita da un oggetto popolato con dati in XAML impostandone la [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) proprietà su una matrice di `MasterPageItem` istanze. Ogni `MasterPageItem` definisce le proprietà `Title`, `IconSource` e `TargetType`.

A [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) viene assegnato [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) alla proprietà `MasterPageItem`, per visualizzare ogni oggetto . `DataTemplate` L'oggetto [`ViewCell`](xref:Xamarin.Forms.ViewCell) contiene un [`Image`](xref:Xamarin.Forms.Image) oggetto [`Label`](xref:Xamarin.Forms.Label)che è costituito da un oggetto e un oggetto . Visualizza [`Image`](xref:Xamarin.Forms.Image) il `IconSource` valore della [`Label`](xref:Xamarin.Forms.Label) proprietà `Title` e il valore `MasterPageItem`della proprietà , per ogni oggetto .

La pagina [`Title`](xref:Xamarin.Forms.Page.Title) ha [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) le proprietà e. L'icona verrà visualizzata nella pagina di dettaglio a condizione che in quest'ultima sia presente una barra del titolo. Questa operazione deve essere abilitata in iOS [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) eseguendo il wrapping dell'istanza della pagina di dettaglio in un'istanza.

> [!NOTE]
> La [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) pagina deve [`Title`](xref:Xamarin.Forms.Page.Title) avere la proprietà impostata, altrimenti si verificherà un'eccezione.

L'esempio di codice seguente illustra la pagina equivalente creata in C#:

```csharp
public class MasterPageCS : ContentPage
{
  public ListView ListView { get { return listView; } }

  ListView listView;

  public MasterPageCS ()
  {
    var masterPageItems = new List<MasterPageItem> ();
    masterPageItems.Add (new MasterPageItem {
      Title = "Contacts",
      IconSource = "contacts.png",
      TargetType = typeof(ContactsPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "TodoList",
      IconSource = "todo.png",
      TargetType = typeof(TodoListPageCS)
    });
    masterPageItems.Add (new MasterPageItem {
      Title = "Reminders",
      IconSource = "reminders.png",
      TargetType = typeof(ReminderPageCS)
    });

    listView = new ListView {
      ItemsSource = masterPageItems,
      ItemTemplate = new DataTemplate (() => {
        var grid = new Grid { Padding = new Thickness(5, 10) };
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(30) });
        grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });

        var image = new Image();
        image.SetBinding(Image.SourceProperty, "IconSource");
        var label = new Label { VerticalOptions = LayoutOptions.FillAndExpand };
        label.SetBinding(Label.TextProperty, "Title");

        grid.Children.Add(image);
        grid.Children.Add(label, 1, 0);

        return new ViewCell { View = grid };
      }),
      SeparatorVisibility = SeparatorVisibility.None
    };

    IconImageSource = "hamburger.png";
    Title = "Personal Organiser";
    Content = new StackLayout
    {
      Children = { listView }
    };
  }
}
```

Gli screenshot seguenti illustrano la pagina master in ogni piattaforma:

![](master-detail-page-images/masterpage.png "Master Page Example")

### <a name="creating-and-displaying-the-detail-page"></a>Creazione e visualizzazione della pagina di dettaglio

`MasterPage` L'istanza `ListView` contiene una proprietà [`ListView`](xref:Xamarin.Forms.ListView) che espone `MainPage` [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) la relativa istanza in modo [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) che l'istanza può registrare un gestore eventi per gestire l'evento. Ciò `MainPage` consente all'istanza di impostare [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) la `ListView` proprietà sulla pagina che rappresenta l'elemento selezionato. L'esempio di codice seguente illustra il gestore eventi:

```csharp
public partial class MainPage : MasterDetailPage
{
    public MainPage ()
    {
        ...
        masterPage.listView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as MasterPageItem;
        if (item != null) {
            Detail = new NavigationPage ((Page)Activator.CreateInstance (item.TargetType));
            masterPage.listView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

Il metodo `OnItemSelected` esegue le azioni seguenti:

- Recupera [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) l'oggetto [`ListView`](xref:Xamarin.Forms.ListView) dall'istanza e purché `null`non sia impostato su , imposta la pagina `TargetType` di dettaglio su una nuova istanza del tipo di pagina archiviato nella proprietà dell'oggetto `MasterPageItem`. Il tipo di pagina [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) viene eseguito il wrapping in [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) un'istanza per garantire che l'icona a cui viene fatto riferimento tramite la proprietà nella visualizzazione è visualizzata nella pagina di dettaglio in iOS.The page type is wrapped in a instance to ensure that the icon referenced through the property on the `MasterPage` is shown on the detail page in iOS.
- L'elemento [`ListView`](xref:Xamarin.Forms.ListView) selezionato in `null` è impostato per garantire `ListView` che nessuno degli `MasterPage` elementi verrà selezionato la prossima volta che viene presentato.
- La pagina dei dettagli viene presentata [`MasterDetailPage.IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) all'utente impostando la proprietà su `false`. Questa proprietà controlla la visualizzazione della pagina master o di quella di dettaglio. Impostarla su `true` per visualizzare la pagina master e su `false` per visualizzare la pagina di dettaglio.

Gli screenshot seguenti illustrano la pagina di dettaglio `ContactPage` che viene visualizzata dopo che è stata selezionata nella pagina master:

![](master-detail-page-images/detailpage.png "Detail Page Example")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>Controllo del comportamento di visualizzazione della pagina di dettaglio

Il [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) modo in cui gestisce le pagine master e di dettaglio dipende dal fatto che l'applicazione sia in esecuzione su un telefono o un tablet, l'orientamento del dispositivo e il valore della [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) proprietà. Questa proprietà determina il modo in cui verrà visualizzata la pagina di dettaglio. I valori possibili sono:

- **Default**: le pagine sono visualizzate usando l'impostazione predefinita della piattaforma.
- **Popover**: la pagina di dettaglio copre, o copre parzialmente, la pagina master.
- **Split**: la pagina master viene visualizzata a sinistra e la pagina di dettaglio a destra.
- **SplitOnLandscape**: quando il dispositivo è in orientamento orizzontale viene usata una schermata suddivisa.
- **SplitOnPortrait**: quando il dispositivo è in orientamento verticale viene usata una schermata suddivisa.

Nell'esempio di codice XAML [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) seguente viene [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)illustrato come impostare la proprietà su un:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

Nell'esempio di codice [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) riportato di seguito viene illustrato l'equivalente creato in C:

```csharp
public class MainPageCS : MasterDetailPage
{
    MasterPageCS masterPage;

    public MainPageCS ()
    {
        MasterBehavior = MasterBehavior.Popover;
        ...
    }
}
```

Tuttavia, il [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) valore della proprietà influisce solo sulle applicazioni in esecuzione sui tablet o sul desktop. Per le applicazioni in esecuzione sui telefoni è sempre impostato il comportamento *Popover*.

## <a name="summary"></a>Riepilogo

In questo articolo è [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) stato illustrato come utilizzare e spostarsi tra le pagine di informazioni. `MasterDetailPage` di Xamarin.Forms è una pagina che gestisce due pagine di informazioni correlate: una pagina master che presenta gli elementi e una pagina di dettaglio che presenta i dettagli relativi agli elementi della pagina master.

## <a name="related-links"></a>Collegamenti correlati

- [Varietà di pagine](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
