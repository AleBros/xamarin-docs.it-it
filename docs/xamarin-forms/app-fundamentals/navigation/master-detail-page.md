---
title: Pagina master-details
description: "Il MasterDetailPage xamarin. Forms è una pagina che gestisce due pagine correlate di informazioni: una pagina master che presenta elementi e una pagina di dettaglio che visualizza i dettagli sugli elementi della pagina master. In questo articolo viene illustrato come utilizzare un MasterDetailPage e spostarsi tra le pagine di informazioni."
ms.topic: article
ms.prod: xamarin
ms.assetid: 119945E3-58B8-4630-A3D2-8B561529D53B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 9d774870a541630d8c6519f9dfeaeb21cacb98e8
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="master-detail-page"></a>Pagina master-details

_Il MasterDetailPage xamarin. Forms è una pagina che gestisce due pagine correlate di informazioni: una pagina master che presenta elementi e una pagina di dettaglio che visualizza i dettagli sugli elementi della pagina master. In questo articolo viene illustrato come utilizzare un MasterDetailPage e spostarsi tra le pagine di informazioni._

## <a name="overview"></a>Panoramica

Una pagina master viene in genere visualizzato un elenco di elementi, come illustrato nelle schermate seguenti:

[![](master-detail-page-images/masterpage-components.png "Componenti della pagina master")](master-detail-page-images/masterpage-components-large.png#lightbox "componenti della pagina Master")

Il percorso dell'elenco di elementi è identico in ogni piattaforma, e se si seleziona uno degli elementi per passare alla pagina di dettaglio corrispondente. Inoltre, la pagina master include anche una barra di navigazione che contiene un pulsante che consente di passare alla pagina di dettaglio attiva:

- In iOS, la barra di spostamento è presente nella parte superiore della pagina e dispone di un pulsante che consente di passare alla pagina di dettaglio. Inoltre, la pagina di dettaglio attiva per spostarsi scorrendo la pagina master a sinistra.
- In Android, la barra di spostamento è presente nella parte superiore della pagina e consente di visualizzare un titolo, un'icona e un pulsante che consente di passare alla pagina di dettaglio. L'icona è definito nel `[Activity]` attributo che decora il `MainActivity` classe nel progetto specifico della piattaforma Android. Inoltre, la pagina dei dettagli attivo può essere selezionata scorrendo la pagina master a sinistra, toccando la pagina dei dettagli nella parte destra della schermata e toccando il *nuovamente* nella parte inferiore della schermata.
- Nel Windows piattaforma UWP (Universal), barra di spostamento è presente nella parte superiore della pagina e dispone di un pulsante che consente di passare alla pagina di dettaglio.

Un pagina vengono visualizzati dati dettaglio che corrisponde all'elemento selezionato nello schema pagina e i componenti principali della pagina dettagli vengono visualizzati nelle schermate seguenti:

![](master-detail-page-images/detailpage-components.png "Componenti della pagina di dettaglio")

La pagina dei dettagli contiene una barra di spostamento, il cui contenuto è dipendente dalla piattaforma:

- In iOS, la barra di spostamento è presente nella parte superiore della pagina consente di visualizzare un titolo e dispone di un pulsante che restituisce la pagina master, che viene eseguito il wrapping di un'istanza della pagina di dettaglio nel [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) istanza. Inoltre, la pagina master può essere restituita per scorrendo la pagina dei dettagli a destra.
- In Android, una barra di spostamento è presente nella parte superiore della pagina e consente di visualizzare un titolo, un'icona e un pulsante che restituisce la pagina master. L'icona è definito nel `[Activity]` attributo che decora il `MainActivity` classe nel progetto specifico della piattaforma Android.
- Nella piattaforma UWP, la barra di spostamento è presente nella parte superiore della pagina consente di visualizzare un titolo e dispone di un pulsante che restituisce la pagina master.

### <a name="navigation-behavior"></a>Comportamento di spostamento

Il comportamento dell'esperienza di navigazione tra le pagine master e di dettaglio è dipendente dalla piattaforma:

- In iOS, la pagina dettagli *diapositive* a destra, come le diapositive pagina master da sinistra e la parte sinistra del dettaglio è comunque visibile pagina.
- In Android, le pagine master e di dettaglio sono *sovrapposta* tra loro.
- Nella piattaforma UWP, le pagine master e di dettaglio sono *scambiato*.

Un comportamento simile verrà osservato in modalità orizzontale, ad eccezione del fatto che la pagina master in iOS e Android ha una larghezza simile come pagina master in modalità verticale, in modo più della pagina dettagli saranno visibili.

Per informazioni su come controllare il comportamento di navigazione, vedere [controllare il comportamento di visualizzazione pagina dettagli](#Controlling_the_Detail_Page_Display_Behavior).

## <a name="creating-a-masterdetailpage"></a>Creazione di un MasterDetailPage

Oggetto [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) contiene [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) e [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) le proprietà che sono entrambi di tipo [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), che consentono di ottenere e impostare rispettivamente le pagine master e di dettaglio.

> [!IMPORTANT]
> Oggetto [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) è progettato per essere una pagina principale e utilizzarla come pagina figlio in altri tipi di pagina potrebbe causare un comportamento imprevisto e non coerente. Inoltre, è consigliabile che la pagina master di un [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) deve essere sempre un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) istanza e che la pagina dei dettagli solo deve essere popolata con [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/), [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), e `ContentPage` istanze. Ciò è utile per garantire un'esperienza utente coerente in tutte le piattaforme.

Nell'esempio di codice XAML seguente viene un [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) che imposta il [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) e [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) proprietà:

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

L'esempio di codice seguente mostra l'equivalente [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) creato in c#:

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

Il [ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) è impostata su un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) istanza. Il [ `MasterDetailPage.Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) è impostata su un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) contenente un `ContentPage` istanza.

### <a name="creating-the-master-page"></a>Creazione di una pagina Master

L'esempio di codice XAML seguente viene illustrata la dichiarazione del `MasterPage` oggetto, che viene fatto riferimento tramite il [ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) proprietà:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="using:MasterDetailPageNavigation"
             x:Class="MasterDetailPageNavigation.MasterPage"
             Padding="0,40,0,0"
             Icon="hamburger.png"
             Title="Personal Organiser">
    <StackLayout>
        <ListView x:Name="listView">
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

La pagina è costituito da un [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) che viene popolata con i dati in XAML impostando il relativo [ `ItemsSource` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/) proprietà a una matrice di `MasterPageItem` istanze. Ogni `MasterPageItem` definisce `Title`, `IconSource`, e `TargetType` proprietà.

A [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/) è assegnata la [ `ListView.ItemTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) proprietà, per visualizzare ogni `MasterPageItem`. Il `DataTemplate` contiene un [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) costituito da un [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) e [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/). Il [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) consente di visualizzare il `IconSource` valore della proprietà e [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) consente di visualizzare il `Title` per ogni valore della proprietà `MasterPageItem`.

La pagina è relativo [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/) e [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) set di proprietà. Verrà visualizzata l'icona nella pagina di dettaglio, a condizione che la pagina di dettaglio una barra del titolo. Questo deve essere abilitato in iOS eseguendo il wrapping l'istanza di pagina di dettaglio in un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) istanza.

> [!NOTE]
> Il [ `MasterDetailPage.Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) della pagina deve essere relativo [ `Title` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Title/) impostata o si verificherà un'eccezione.

Esempio di codice seguente mostra la pagina di equivalente creata in c#:

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

La schermata seguente mostra la pagina master in ogni piattaforma:

![](master-detail-page-images/masterpage.png "Esempio di pagina master")

### <a name="creating-and-displaying-the-detail-page"></a>Creazione e la visualizzazione della pagina di dettaglio

Il `MasterPage` istanza contiene un `ListView` proprietà esposta relativo [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) istanza in modo che il `MainPage` [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) possibile registrare l'istanza di un gestore eventi per gestire il [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) evento. In questo modo il `MainPage` istanza per impostare il [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) proprietà alla pagina che rappresenta l'elemento selezionato `ListView` elemento. Esempio di codice seguente viene illustrato il gestore dell'evento:

```csharp
public partial class MainPage : MasterDetailPage
{
    public MainPage ()
    {
        ...
        masterPage.ListView.ItemSelected += OnItemSelected;
    }

    void OnItemSelected (object sender, SelectedItemChangedEventArgs e)
    {
        var item = e.SelectedItem as MasterPageItem;
        if (item != null) {
            Detail = new NavigationPage ((Page)Activator.CreateInstance (item.TargetType));
            masterPage.ListView.SelectedItem = null;
            IsPresented = false;
        }
    }
}
```

Il `OnItemSelected` metodo esegue le azioni seguenti:

- Recupera il [ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/) dal [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) istanza e fornito non è `null`, imposta la pagina dei dettagli in una nuova istanza del tipo di pagina archiviati nel `TargetType`proprietà del `MasterPageItem`. Il tipo di pagina viene eseguito il wrapping un [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) istanza per garantire che l'icona di cui viene fatto riferimento tramite il [ `Icon` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Icon/) proprietà il `MasterPage` viene visualizzato nella pagina di dettaglio in iOS.
- L'elemento selezionato nel [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) è impostato su `null` per garantire che nessuna del `ListView` oggetti verranno selezionati successivo il `MasterPage` viene presentato.
- La pagina dei dettagli viene presentata all'utente impostando il [ `MasterDetailPage.IsPresented` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.IsPresented/) proprietà `false`. Questa proprietà controlla se è visualizzata la pagina master o di dettaglio. Deve essere impostato su `true` per visualizzare la pagina master e a `false` per visualizzare la pagina di dettaglio.

Le schermate seguenti viene illustrato il `ContactPage` pagina dei dettagli, che viene visualizzata dopo che è stata selezionata nella pagina master:

![](master-detail-page-images/detailpage.png "Esempio di pagina di dettaglio")

<a name="Controlling_the_Detail_Page_Display_Behavior" />

### <a name="controlling-the-detail-page-display-behavior"></a>Controllare il comportamento di visualizzazione delle pagine di dettaglio

Come [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) gestisce le pagine master e di dettaglio varia a seconda se l'applicazione è in esecuzione in un telefono o tablet, l'orientamento del dispositivo e il valore di [ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/) proprietà. Questa proprietà determina la modalità di visualizzazione della pagina di dettaglio. Il valori possibili sono:

- **Predefinito** : le pagine vengono visualizzate utilizzando il valore predefinito della piattaforma.
- **Popover** : la pagina dettagli copre o analizzate parzialmente dalla pagina master.
- **Split** : la pagina master viene visualizzata a sinistra e la pagina dei dettagli è a destra.
- **SplitOnLandscape** : un'unica schermata viene utilizzata quando il dispositivo è con orientamento orizzontale.
- **SplitOnPortrait** : un'unica schermata viene utilizzata quando il dispositivo è in orientamento verticale.

L'esempio di codice XAML seguente viene illustrato come impostare il [ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/) proprietà in un [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
                  xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                  x:Class="MasterDetailPageNavigation.MainPage"
                  MasterBehavior="Popover">
  ...
</MasterDetailPage>
```

L'esempio di codice seguente mostra l'equivalente [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) creato in c#:

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

Tuttavia, il valore di [ `MasterBehavior` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.MasterBehavior/) proprietà influisce solo su applicazioni in esecuzione sul Tablet o desktop. Le applicazioni in esecuzione nei telefoni sempre il *Popover* comportamento.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come utilizzare un [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) e spostarsi tra le pagine di informazioni. Di xamarin. Forms `MasterDetailPage` è una pagina che gestisce due pagine di informazioni correlate: una pagina master che presenta elementi e una pagina di dettaglio che visualizza i dettagli sugli elementi della pagina master.


## <a name="related-links"></a>Collegamenti correlati

- [Tipi di pagina](https://developer.xamarin.com/r/xamarin-forms/book/chapter25.pdf)
- [MasterDetailPage (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Navigation/MasterDetailPage/)
- [MasterDetailPage](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)
