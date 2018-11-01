---
title: Pagina Master-Detail xamarin. Forms
description: 'MasterDetailPage di xamarin. Forms è una pagina che gestisce due pagine correlate di informazioni: una pagina master che presenta elementi e una pagina di dettaglio che presenta i dettagli relativi agli elementi della pagina master. Questo articolo illustra come usare un MasterDetailPage e spostarsi tra le pagine di informazioni.'
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 25f6cf341fcf47d5dc5320f73855bb2a4e29a9e8
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675523"
---
# <a name="xamarinforms-master-detail-page"></a>Pagina Master-Detail xamarin. Forms

_MasterDetailPage di xamarin. Forms è una pagina che gestisce due pagine correlate di informazioni: una pagina master che presenta elementi e una pagina di dettaglio che presenta i dettagli relativi agli elementi della pagina master. Questo articolo illustra come usare un MasterDetailPage e spostarsi tra le pagine di informazioni._

## <a name="overview"></a>Panoramica

Una pagina master in genere visualizza un elenco di elementi, come illustrato negli screenshot seguenti:

[![](master-detail-page-images/masterpage-components.png "Componenti della pagina master")](master-detail-page-images/masterpage-components-large.png#lightbox "componenti della pagina Master")

Il percorso dell'elenco di elementi è identico in ogni piattaforma e selezionando uno degli elementi verrà visualizzata la pagina dei dettagli corrispondenti. Inoltre, la pagina master include anche una barra di navigazione che contiene un pulsante che può essere utilizzato per passare alla pagina di dettaglio attiva:

- In iOS, la barra di spostamento è presente nella parte superiore della pagina e dispone di un pulsante che consente di passare alla pagina di dettaglio. Inoltre, la pagina dei dettagli active per spostarsi tramite scorrimento rapido verso la pagina master a sinistra.
- In Android, la barra di spostamento è presente nella parte superiore della pagina e consente di visualizzare un titolo, un'icona e un pulsante che consente di passare alla pagina di dettaglio. L'icona è definito nel `[Activity]` attributo che decora il `MainActivity` classe nel progetto specifico della piattaforma Android. Inoltre, la pagina dei dettagli active per spostarsi tramite scorrimento rapido verso la pagina master a sinistra, toccando la pagina dei dettagli all'estrema destra della schermata e toccando il *nuovamente* nella parte inferiore della schermata.
- In Universal Windows Platform (UWP), barra di spostamento è presente nella parte superiore della pagina e dispone di un pulsante che consente di passare alla pagina di dettaglio.

Un pagina vengono visualizzati dati dettaglio che corrisponde all'elemento selezionato nel master pagina e i componenti principali nella pagina dei dettagli vengono visualizzati negli screenshot seguenti:

![](master-detail-page-images/detailpage-components.png "Componenti della pagina di dettaglio")

La pagina di dettaglio contiene una barra di spostamento, il cui contenuto è dipendente dalla piattaforma:

- In iOS, la barra di spostamento è presente nella parte superiore della pagina consente di visualizzare un titolo e dispone di un pulsante che torna alla pagina master, a condizione che l'istanza di pagina di dettaglio viene inserito nel [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) istanza. Inoltre, la pagina master può essere restituita per scorrendo rapidamente verso la pagina dei dettagli a destra.
- In Android, una barra di spostamento è presente nella parte superiore della pagina e consente di visualizzare un titolo, un'icona e un pulsante che torna alla pagina master. L'icona è definito nel `[Activity]` attributo che decora il `MainActivity` classe nel progetto specifico della piattaforma Android.
- Nella piattaforma UWP, la barra di spostamento è presente nella parte superiore della pagina consente di visualizzare un titolo e dispone di un pulsante che torna alla pagina master.

### <a name="navigation-behavior"></a>Comportamento di navigazione

Il comportamento dell'esperienza di navigazione tra le pagine master e di dettaglio è dipendente dalla piattaforma:

- In iOS, la pagina di dettaglio *diapositive* verso destra a sinistra e la parte sinistra del dettaglio come le diapositive della pagina master pagina sia ancora visibile.
- In Android, le pagine di dettaglio e master sono *sovrapposto* tra loro.
- Nella piattaforma UWP, le pagine di dettaglio e master sono *scambiato*.

Viene rispettato un comportamento simile in modalità orizzontale, ad eccezione del fatto che la pagina master in iOS e Android ha una larghezza simile come pagina master in modalità verticale, in modo più nella pagina dei dettagli saranno visibili.

Per informazioni su come controllare il comportamento di navigazione, vedere [controllano il comportamento di visualizzazione pagina dettagli](#Controlling_the_Detail_Page_Display_Behavior).

## <a name="creating-a-masterdetailpage"></a>Creazione di un MasterDetailPage

Oggetto [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) contiene [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) e [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) proprietà che sono entrambi di tipo [ `Page` ](xref:Xamarin.Forms.Page), che consentono di ottenere e impostare rispettivamente le pagine master e di dettaglio.

> [!IMPORTANT]
> Oggetto [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) è progettato per essere una pagina radice e usarlo come pagina figlio in altri tipi di pagina può comportare un comportamento imprevisto e non coerente. Inoltre, è consigliabile che la pagina master di un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) deve essere sempre un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) istanza e che la pagina dei dettagli solo deve essere popolata con [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), e `ContentPage` istanze. Ciò è utile per garantire un'esperienza utente uniforme tra tutte le piattaforme.

L'esempio di codice XAML seguente mostra una [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) che consente di scegliere il [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) e [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) proprietà:

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

Esempio di codice seguente viene illustrato l'equivalente [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) creato in c#:

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

Il [ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) è impostata su un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) istanza. Il [ `MasterDetailPage.Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) è impostata su un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) contenente una `ContentPage` istanza.

### <a name="creating-the-master-page"></a>Creazione della pagina Master

Esempio di codice XAML seguente illustra la dichiarazione del `MasterPage` oggetti, cui viene fatto riferimento tramite il [ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) proprietà:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             Icon="hamburger.png"
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

La pagina è costituito da un [ `ListView` ](xref:Xamarin.Forms.ListView) che viene popolato con i dati in XAML mediante l'impostazione relativa [ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) proprietà a una matrice di `MasterPageItem` istanze. Ciascuna `MasterPageItem` definisce `Title`, `IconSource`, e `TargetType` proprietà.

Un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) assegnato per il [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) proprietà, per visualizzare ogni `MasterPageItem`. Il `DataTemplate` contiene un [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) costituito da un [ `Image` ](xref:Xamarin.Forms.Image) e un oggetto [ `Label` ](xref:Xamarin.Forms.Label). Il [ `Image` ](xref:Xamarin.Forms.Image) consente di visualizzare il `IconSource` valore della proprietà e il [ `Label` ](xref:Xamarin.Forms.Label) consente di visualizzare la `Title` per ogni valore della proprietà `MasterPageItem`.

Nella pagina sono relativi [ `Title` ](xref:Xamarin.Forms.Page.Title) e [ `Icon` ](xref:Xamarin.Forms.Page.Icon) set di proprietà. L'icona verrà visualizzata nella pagina di dettaglio, a condizione che la pagina di dettaglio dispone di una barra del titolo. Questa deve essere abilitata in iOS eseguendo il wrapping l'istanza di pagina di dettaglio in un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) istanza.

> [!NOTE]
> Il [ `MasterDetailPage.Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) della pagina deve essere relativo [ `Title` ](xref:Xamarin.Forms.Page.Title) proprietà impostata o si verificherà un'eccezione.

Esempio di codice seguente mostra la pagina equivalente creata in c#:

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

    Icon = "hamburger.png";
    Title = "Personal Organiser";
    Content = new StackLayout
    {
      Children = { listView }
    };
  }
}
```

Le schermate seguenti illustrano la pagina master in ogni piattaforma:

![](master-detail-page-images/masterpage.png "Esempio di pagina master")

### <a name="creating-and-displaying-the-detail-page"></a>Creazione e visualizzazione della pagina di dettaglio

Il `MasterPage` istanza contiene una `ListView` proprietà che espone relativo [ `ListView` ](xref:Xamarin.Forms.ListView) istanza in modo che il `MainPage` [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) istanza può registrare un gestore eventi per gestire il [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) evento. In questo modo, il `MainPage` istanza per impostare il [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) proprietà per la pagina rappresentata selezionato `ListView` elemento. Esempio di codice seguente mostra il gestore dell'evento:

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

Il `OnItemSelected` metodo esegue le azioni seguenti:

- Recupera le [ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem) dal [ `ListView` ](xref:Xamarin.Forms.ListView) dell'istanza e forniti che non è `null`, imposta la pagina dei dettagli in una nuova istanza del tipo di pagina archiviato nel `TargetType`della proprietà di `MasterPageItem`. Il tipo di pagina viene eseguito il wrapping un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) istanza per garantire che l'icona di cui viene fatto riferimento tramite il [ `Icon` ](xref:Xamarin.Forms.Page.Icon) proprietà la `MasterPage` viene visualizzato nella pagina di dettaglio in iOS.
- L'elemento selezionato nel [ `ListView` ](xref:Xamarin.Forms.ListView) è impostata su `null` per garantire che nessuna del `ListView` elementi verranno selezionati la volta successiva il `MasterPage` viene presentato.
- La pagina dei dettagli viene presentata all'utente impostando il [ `MasterDetailPage.IsPresented` ](xref:Xamarin.Forms.MasterDetailPage.IsPresented) proprietà `false`. Questa proprietà controlla se viene visualizzata la pagina master o di dettaglio. Deve essere impostato su `true` per visualizzare la pagina master e a `false` per visualizzare la pagina di dettaglio.

Gli screenshot seguenti viene illustrato il `ContactPage` pagina dei dettagli, che viene visualizzata dopo che è stata selezionata nella pagina master:

![](master-detail-page-images/detailpage.png "Esempio di pagina di dettaglio")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>Controllare il comportamento di visualizzazione delle pagine di dettaglio

Modo in cui il [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) gestisce le pagine master e di dettaglio dipende dal fatto che l'applicazione viene eseguita in un telefono o tablet, l'orientamento del dispositivo e il valore della [ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) proprietà. Questa proprietà determina come verrà visualizzata la pagina dei dettagli. Il valori possibili sono:

- **Predefinito** : le pagine vengono visualizzate utilizzando il valore predefinito di platform.
- **Nel popover** : la pagina dei dettagli copre o parzialmente coperto dalla pagina master.
- **Suddivisione** : la pagina master viene visualizzata a sinistra e la pagina dei dettagli è a destra.
- **SplitOnLandscape** – una schermata di split viene usata quando il dispositivo ha un orientamento orizzontale.
- **SplitOnPortrait** – una schermata di split viene usata quando il dispositivo è in orientamento verticale.

Esempio di codice XAML seguente viene illustrato come impostare il [ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) proprietà in un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

Esempio di codice seguente viene illustrato l'equivalente [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) creato in c#:

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

Tuttavia, il valore della [ `MasterBehavior` ](xref:Xamarin.Forms.MasterDetailPage.MasterBehavior) proprietà influisce solo sulle applicazioni in esecuzione su Tablet e desktop. Le applicazioni eseguite su telefoni sempre hanno il *nel Popover* comportamento.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare un [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) e spostarsi tra le pagine di informazioni. Xamarin. Forms `MasterDetailPage` è una pagina che gestisce due pagine di informazioni correlate, ovvero una pagina master che presenta elementi e una pagina di dettaglio che presenta i dettagli relativi agli elementi della pagina master.


## <a name="related-links"></a>Collegamenti correlati

- [Tipi di pagina](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)
- [MasterDetailPage](xref:Xamarin.Forms.MasterDetailPage)
