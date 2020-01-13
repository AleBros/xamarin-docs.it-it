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
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645960"
---
# <a name="xamarinforms-master-detail-page"></a>Pagina master-dettagli di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)

_MasterDetailPage di Xamarin.Forms è una pagina che gestisce due pagine correlate di informazioni: una pagina master che presenta gli elementi e una pagina di dettaglio che presenta i dettagli relativi agli elementi della pagina master. Questo articolo illustra come usare una classe MasterDetailPage e come spostarsi tra le pagine di informazioni._

## <a name="overview"></a>Panoramica

Una pagina master in genere visualizza un elenco di elementi, come illustrato negli screenshot seguenti:

[![](master-detail-page-images/masterpage-components.png "Componenti della pagina master")](master-detail-page-images/masterpage-components-large.png#lightbox "Componenti della pagina master")

La posizione dell'elenco di elementi è identica in ogni piattaforma e la selezione di uno degli elementi consente di passare alla pagina di dettaglio corrispondente. Nella pagina master è inoltre disponibile una barra di navigazione contenente un pulsante che può essere usato per passare alla pagina di dettaglio attiva:

- In iOS la barra di navigazione si trova nella parte superiore della pagina e include un pulsante che consente di passare alla pagina di dettaglio. È possibile passare alla pagina di dettaglio attiva anche scorrendo rapidamente la pagina master verso sinistra.
- In Android la barra di navigazione si trova nella parte superiore della pagina e visualizza un titolo, un'icona e un pulsante che consente di passare alla pagina di dettaglio. L'icona è definita nell'attributo `[Activity]` che decora la classe `MainActivity` nel progetto specifico della piattaforma Android. È possibile passare alla pagina di dettaglio attiva anche scorrendo rapidamente la pagina master verso sinistra, toccando la pagina di dettaglio all'estremità destra dello schermo e toccando il pulsante *Indietro* nella parte inferiore dello schermo.
- Nella piattaforma UWP (Universal Windows Platform) la barra di navigazione si trova nella parte superiore della pagina e include un pulsante che consente di passare alla pagina di dettaglio.

Un pagina di dettaglio visualizza i dati corrispondenti all'elemento selezionato nella pagina master. I componenti principali della pagina di dettaglio sono illustrati negli screenshot seguenti:

![](master-detail-page-images/detailpage-components.png "Componenti della pagina di dettaglio")

La pagina di dettaglio contiene una barra di navigazione il cui contenuto dipende dalla piattaforma:

- In iOS la barra di navigazione si trova nella parte superiore della pagina, visualizza un titolo e include un pulsante che consente di tornare alla pagina master, a condizione che sia stato eseguito il wrapping dell'istanza della pagina di dettaglio nell'istanza di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). È possibile tornare alla pagina master anche scorrendo rapidamente la pagina di dettaglio verso destra.
- In Android è disponibile una barra di navigazione nella parte superiore della pagina in cui sono visualizzati un titolo, un'icona e un pulsante che consente di tornare alla pagina master. L'icona è definita nell'attributo `[Activity]` che decora la classe `MainActivity` nel progetto specifico della piattaforma Android.
- In UWP la barra di navigazione si trova nella parte superiore della pagina e include un titolo e un pulsante che consente di tornare alla pagina master.

### <a name="navigation-behavior"></a>Comportamento di navigazione

Il comportamento dell'esperienza di navigazione tra le pagine master e di dettaglio dipende dalla piattaforma:

- In iOS la pagina di dettaglio *scorre* verso destra mentre la pagina master scorre verso sinistra e la parte sinistra della pagina di dettaglio è ancora visibile.
- In Android le pagine di dettaglio e master vengono *sovrapposte* le une alle altre.
- Nella piattaforma UWP, la pagina master scorre da sinistra su parte della pagina di dettaglio, a condizione che la proprietà [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) sia impostata su `Popover`. Per altre informazioni, vedere [Controllo del comportamento di visualizzazione della pagina di dettaglio](#Controlling_the_Detail_Page_Display_Behavior).

Un comportamento simile si osserverà in modalità orizzontale, ad eccezione del fatto che la pagina master in iOS e Android ha una larghezza simile alla pagina master in modalità verticale, quindi sarà visibile una parte maggiore della pagina di dettaglio.

Per informazioni su come controllare il comportamento di navigazione, vedere [Controllo del comportamento di visualizzazione della pagina di dettaglio](#Controlling_the_Detail_Page_Display_Behavior).

## <a name="creating-a-masterdetailpage"></a>Creazione di una classe MasterDetailPage

Una [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) contiene proprietà [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) e [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail), entrambe di tipo [`Page`](xref:Xamarin.Forms.Page), usate per ottenere e impostare rispettivamente le pagine master e di dettaglio.

> [!IMPORTANT]
> Una [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) è progettata per essere una pagina radice e l'uso come pagina figlio in altri tipi di pagina può determinare un comportamento imprevisto e incoerente. È inoltre consigliabile che la pagina master di una [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) sia sempre un'istanza di [`ContentPage`](xref:Xamarin.Forms.ContentPage) e che la pagina di dettaglio sia popolata solo con istanze di [`TabbedPage`](xref:Xamarin.Forms.TabbedPage), [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e `ContentPage`. In questo modo si potrà garantire un'esperienza utente uniforme su tutte le piattaforme.

L'esempio di codice XAML seguente illustra una [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) che imposta le proprietà [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) e [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail):

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

L'esempio di codice seguente illustra la [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) equivalente creata in C#:

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

La proprietà [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) viene impostata su un'istanza di [`ContentPage`](xref:Xamarin.Forms.ContentPage). La proprietà [`MasterDetailPage.Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) viene impostata su una classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) contenente un'istanza di `ContentPage`.

### <a name="creating-the-master-page"></a>Creazione della pagina master

L'esempio di codice XAML seguente illustra la dichiarazione dell'oggetto `MasterPage` a cui si fa riferimento tramite la proprietà [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master):

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

La pagina è composta da una [`ListView`](xref:Xamarin.Forms.ListView) popolata con i dati in XAML impostandone la proprietà [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) su una matrice di istanze di `MasterPageItem`. Ogni `MasterPageItem` definisce le proprietà `Title`, `IconSource` e `TargetType`.

Una classe [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) è assegnata alla proprietà [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) per visualizzare ogni `MasterPageItem`. `DataTemplate` contiene una classe [`ViewCell`](xref:Xamarin.Forms.ViewCell) composta da una classe [`Image`](xref:Xamarin.Forms.Image) e una classe [`Label`](xref:Xamarin.Forms.Label). [`Image`](xref:Xamarin.Forms.Image) visualizza il valore della proprietà `IconSource` e [`Label`](xref:Xamarin.Forms.Label) visualizza il valore della proprietà `Title` per ogni `MasterPageItem`.

La pagina ha le proprietà [`Title`](xref:Xamarin.Forms.Page.Title) e [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) impostate. L'icona verrà visualizzata nella pagina di dettaglio a condizione che in quest'ultima sia presente una barra del titolo. È necessario abilitare questa funzionalità in iOS eseguendo il wrapping dell'istanza della pagina di dettaglio in un'istanza di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage).

> [!NOTE]
> La pagina [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) deve avere la proprietà [`Title`](xref:Xamarin.Forms.Page.Title) impostata. In caso contrario, verrà generata un'eccezione.

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

![](master-detail-page-images/masterpage.png "Esempio di pagina master")

### <a name="creating-and-displaying-the-detail-page"></a>Creazione e visualizzazione della pagina di dettaglio

L'istanza di `MasterPage` contiene una proprietà `ListView` che espone l'istanza di [`ListView`](xref:Xamarin.Forms.ListView) in modo che l'istanza `MainPage` [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) possa registrare un gestore eventi per la gestione dell'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected). Ciò consente all'istanza di `MainPage` di impostare la proprietà [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) sulla pagina che rappresenta l'elemento `ListView` selezionato. L'esempio di codice seguente illustra il gestore eventi:

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

- Recupera [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) dall'istanza di [`ListView`](xref:Xamarin.Forms.ListView) e, a condizione che non sia `null`, imposta la pagina di dettaglio su una nuova istanza del tipo di pagina archiviato nella proprietà `TargetType` di `MasterPageItem`. Viene eseguito il wrapping del tipo di pagina in un'istanza di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) per assicurarsi che l'icona a cui si fa riferimento tramite la proprietà [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) in `MasterPage` venga visualizzata nella pagina di dettaglio in iOS.
- L'elemento selezionato in [`ListView`](xref:Xamarin.Forms.ListView) viene impostato su `null` per assicurarsi che nessuno degli elementi `ListView` verrà selezionato alla successiva visualizzazione di `MasterPage`.
- La pagina di dettaglio viene presentata all'utente impostando la proprietà [`MasterDetailPage.IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) su `false`. Questa proprietà controlla la visualizzazione della pagina master o di quella di dettaglio. Impostarla su `true` per visualizzare la pagina master e su `false` per visualizzare la pagina di dettaglio.

Gli screenshot seguenti illustrano la pagina di dettaglio `ContactPage` che viene visualizzata dopo che è stata selezionata nella pagina master:

![](master-detail-page-images/detailpage.png "Esempio di pagina di dettaglio")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>Controllo del comportamento di visualizzazione della pagina di dettaglio

La modalità di gestione delle pagine master e di dettaglio da parte di [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) dipende dal fatto che l'applicazione sia in esecuzione in un telefono o un tablet, dall'orientamento del dispositivo e dal valore della proprietà [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior). Questa proprietà determina il modo in cui verrà visualizzata la pagina di dettaglio. I valori possibili sono:

- **Default**: le pagine sono visualizzate usando l'impostazione predefinita della piattaforma.
- **Popover**: la pagina di dettaglio copre, o copre parzialmente, la pagina master.
- **Split**: la pagina master viene visualizzata a sinistra e la pagina di dettaglio a destra.
- **SplitOnLandscape**: quando il dispositivo è in orientamento orizzontale viene usata una schermata suddivisa.
- **SplitOnPortrait**: quando il dispositivo è in orientamento verticale viene usata una schermata suddivisa.

L'esempio di codice XAML seguente illustra come impostare la proprietà [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) in una [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage):

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

L'esempio di codice seguente illustra la [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) equivalente creata in C#:

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

Il valore della proprietà [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior), tuttavia, influisce solo sulle applicazioni in esecuzione su tablet o computer desktop. Per le applicazioni in esecuzione sui telefoni è sempre impostato il comportamento *Popover*.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare una classe [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) e come spostarsi tra le pagine di informazioni. `MasterDetailPage` di Xamarin.Forms è una pagina che gestisce due pagine di informazioni correlate: una pagina master che presenta gli elementi e una pagina di dettaglio che presenta i dettagli relativi agli elementi della pagina master.

## <a name="related-links"></a>Collegamenti correlati

- [Tipi di pagina](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
