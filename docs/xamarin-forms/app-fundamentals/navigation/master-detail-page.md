---
title: " Xamarin.Forms pagina master-details" Description: " Xamarin.Forms MasterDetailPage è una pagina che gestisce due pagine correlate di informazioni, ovvero una pagina master che presenta elementi, e una pagina di dettaglio che presenta informazioni dettagliate sugli elementi della pagina master. Questo articolo illustra come usare un MasterDetailPage e spostarsi tra le pagine di informazioni ".
ms. prod: Novell MS. AssetID: 119945E3-58B8-4630-A3D2-8B561529D53B ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 12/01/2017 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-master-detail-page"></a>Xamarin.FormsPagina master-dettagli

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)

_Xamarin.FormsMasterDetailPage è una pagina che gestisce due pagine di informazioni correlate, ovvero una pagina master che presenta elementi, e una pagina di dettaglio che presenta informazioni dettagliate sugli elementi della pagina master. Questo articolo illustra come usare un MasterDetailPage e spostarsi tra le pagine di informazioni._

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

- In iOS, la barra di spostamento è presente nella parte superiore della pagina e visualizza un titolo e un pulsante che torna alla pagina master, a condizione che l'istanza della pagina dei dettagli sia incapsulata nell' [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) istanza. È possibile tornare alla pagina master anche scorrendo rapidamente la pagina di dettaglio verso destra.
- In Android è disponibile una barra di navigazione nella parte superiore della pagina in cui sono visualizzati un titolo, un'icona e un pulsante che consente di tornare alla pagina master. L'icona è definita nell'attributo `[Activity]` che decora la classe `MainActivity` nel progetto specifico della piattaforma Android.
- In UWP la barra di navigazione si trova nella parte superiore della pagina e include un titolo e un pulsante che consente di tornare alla pagina master.

### <a name="navigation-behavior"></a>Comportamento di navigazione

Il comportamento dell'esperienza di navigazione tra le pagine master e di dettaglio dipende dalla piattaforma:

- In iOS la pagina di dettaglio *scorre* verso destra mentre la pagina master scorre verso sinistra e la parte sinistra della pagina di dettaglio è ancora visibile.
- In Android le pagine di dettaglio e master vengono *sovrapposte* le une alle altre.
- In UWP la pagina master scorre dalla parte sinistra della pagina dei dettagli, a condizione che la [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) proprietà sia impostata su `Popover` . Per altre informazioni, vedere [Controllo del comportamento di visualizzazione della pagina di dettaglio](#controlling-the-detail-page-display-behavior).

Un comportamento simile si osserverà in modalità orizzontale, ad eccezione del fatto che la pagina master in iOS e Android ha una larghezza simile alla pagina master in modalità verticale, quindi sarà visibile una parte maggiore della pagina di dettaglio.

Per informazioni su come controllare il comportamento di navigazione, vedere [Controllo del comportamento di visualizzazione della pagina di dettaglio](#controlling-the-detail-page-display-behavior).

## <a name="creating-a-masterdetailpage"></a>Creazione di una classe MasterDetailPage

Un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) oggetto [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) contiene [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) le proprietà e che sono entrambe di tipo [`Page`](xref:Xamarin.Forms.Page) , che vengono utilizzate per ottenere e impostare rispettivamente le pagine master e Detail.

> [!IMPORTANT]
> Un oggetto [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) è progettato per essere una pagina radice e l'utilizzo come pagina figlio in altri tipi di pagina può causare un comportamento imprevisto e incoerente. Inoltre, è consigliabile che la pagina master di un oggetto [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) sia sempre un' [`ContentPage`](xref:Xamarin.Forms.ContentPage) istanza e che la pagina dei dettagli debba essere popolata solo con le [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) istanze, e `ContentPage` . In questo modo si potrà garantire un'esperienza utente uniforme su tutte le piattaforme.

Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) che imposta le [`Master`](xref:Xamarin.Forms.MasterDetailPage.Master) [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) proprietà e:

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

L'esempio di codice seguente illustra l'equivalente [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) creato in C#:

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

La [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) proprietà è impostata su un' [`ContentPage`](xref:Xamarin.Forms.ContentPage) istanza di. La [`MasterDetailPage.Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) proprietà è impostata su un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) contenente un' `ContentPage` istanza di.

### <a name="creating-the-master-page"></a>Creazione della pagina master

Nell'esempio di codice XAML riportato di seguito viene illustrata la dichiarazione dell' `MasterPage` oggetto, a cui viene fatto riferimento tramite la [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master) proprietà:

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

La pagina è costituita da un oggetto [`ListView`](xref:Xamarin.Forms.ListView) popolato con dati in XAML, impostando la relativa [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) proprietà su una matrice di `MasterPageItem` istanze. Ogni `MasterPageItem` definisce le proprietà `Title`, `IconSource` e `TargetType`.

Un oggetto [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) viene assegnato alla [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) proprietà per visualizzare ogni `MasterPageItem` . `DataTemplate`Contiene un oggetto [`ViewCell`](xref:Xamarin.Forms.ViewCell) costituito da un oggetto [`Image`](xref:Xamarin.Forms.Image) e un oggetto [`Label`](xref:Xamarin.Forms.Label) . [`Image`](xref:Xamarin.Forms.Image)Visualizza il `IconSource` valore della proprietà e visualizza il [`Label`](xref:Xamarin.Forms.Label) valore della `Title` proprietà, per ogni `MasterPageItem` .

Per la pagina sono [`Title`](xref:Xamarin.Forms.Page.Title) [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) impostate le proprietà e. L'icona verrà visualizzata nella pagina di dettaglio a condizione che in quest'ultima sia presente una barra del titolo. Questa operazione deve essere abilitata in iOS eseguendo il wrapping dell'istanza della pagina di dettaglio in un' [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) istanza.

> [!NOTE]
> [`MasterDetailPage.Master`](xref:Xamarin.Forms.MasterDetailPage.Master)È necessario impostare la proprietà della pagina [`Title`](xref:Xamarin.Forms.Page.Title) , altrimenti si verificherà un'eccezione.

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

L' `MasterPage` istanza di contiene una `ListView` proprietà che espone la relativa [`ListView`](xref:Xamarin.Forms.ListView) istanza in modo che l' `MainPage` [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) istanza possa registrare un gestore eventi per gestire l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento. Ciò consente all' `MainPage` istanza di impostare la [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) proprietà sulla pagina che rappresenta l'elemento selezionato `ListView` . L'esempio di codice seguente illustra il gestore eventi:

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

- Recupera l'oggetto [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) dall' [`ListView`](xref:Xamarin.Forms.ListView) istanza e, a condizione che non lo sia `null` , imposta la pagina dei dettagli su una nuova istanza del tipo di pagina archiviato nella `TargetType` proprietà di `MasterPageItem` . Il tipo di pagina viene sottoposta a incapsulamento in un' [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) istanza di per garantire che l'icona a cui viene fatto riferimento tramite la [`IconImageSource`](xref:Xamarin.Forms.Page.IconImageSource) Proprietà in `MasterPage` sia visualizzata nella pagina dei dettagli in iOS.
- L'elemento selezionato in [`ListView`](xref:Xamarin.Forms.ListView) è impostato su `null` per garantire che nessuno degli `ListView` elementi verrà selezionato la volta successiva che `MasterPage` viene visualizzato.
- La pagina dei dettagli viene presentata all'utente impostando la [`MasterDetailPage.IsPresented`](xref:Xamarin.Forms.MasterDetailPage.IsPresented) proprietà su `false` . Questa proprietà controlla la visualizzazione della pagina master o di quella di dettaglio. Impostarla su `true` per visualizzare la pagina master e su `false` per visualizzare la pagina di dettaglio.

Gli screenshot seguenti illustrano la pagina di dettaglio `ContactPage` che viene visualizzata dopo che è stata selezionata nella pagina master:

![](master-detail-page-images/detailpage.png "Detail Page Example")

### <a name="controlling-the-detail-page-display-behavior"></a>Controllo del comportamento di visualizzazione della pagina di dettaglio

Il modo in cui [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) gestisce le pagine master e di dettaglio varia a seconda che l'applicazione sia in esecuzione su un telefono o un tablet, l'orientamento del dispositivo e il valore della [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) Proprietà. Questa proprietà determina il modo in cui verrà visualizzata la pagina di dettaglio. I valori possibili sono:

- **Default**: le pagine sono visualizzate usando l'impostazione predefinita della piattaforma.
- **Popover**: la pagina di dettaglio copre, o copre parzialmente, la pagina master.
- **Split**: la pagina master viene visualizzata a sinistra e la pagina di dettaglio a destra.
- **SplitOnLandscape**: quando il dispositivo è in orientamento orizzontale viene usata una schermata suddivisa.
- **SplitOnPortrait**: quando il dispositivo è in orientamento verticale viene usata una schermata suddivisa.

Nell'esempio di codice XAML riportato di seguito viene illustrato come impostare la [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) Proprietà in un oggetto [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

L'esempio di codice seguente illustra l'equivalente [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) creato in C#:

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

Tuttavia, il valore della [`MasterBehavior`](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) proprietà influiscono solo sulle applicazioni in esecuzione su tablet o sul desktop. Per le applicazioni in esecuzione sui telefoni è sempre impostato il comportamento *Popover*.

## <a name="summary"></a>Summary

Questo articolo ha illustrato come usare un oggetto [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) e spostarsi tra le pagine di informazioni. Xamarin.Forms `MasterDetailPage` È una pagina che gestisce due pagine di informazioni correlate, ovvero una pagina master che presenta elementi, e una pagina di dettaglio che presenta informazioni dettagliate sugli elementi della pagina master.

## <a name="related-links"></a>Collegamenti correlati

- [Tipi di pagina](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-masterdetailpage)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
