---
title: Introduzione a Xamarin.Forms
description: Questo articolo presenta Xamarin.Forms e indica come iniziare a scrivere applicazioni con questo framework.
ms.prod: xamarin
ms.assetid: f619595f-3ee7-439b-a1bc-d13e5106e6e9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/02/2016
ms.openlocfilehash: 15a26ce633e8321e9101289276c9da302e5bd8cc
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35243694"
---
# <a name="an-introduction-to-xamarinforms"></a>Introduzione a Xamarin.Forms

_Xamarin.Forms è un'astrazione toolkit per interfacce utente con supporto nativo multipiattaforma, che consente agli sviluppatori di creare con facilità interfacce utente che possono essere condivise in Android, iOS, Windows e la piattaforma UWP. Il rendering delle interfacce utente viene eseguito con i controlli nativi della piattaforma di destinazione, consentendo alle applicazioni Xamarin.Forms di mantenere l'aspetto appropriato per ogni piattaforma. Questo articolo fornisce un'introduzione a Xamarin.Forms e illustra come iniziare a scrivere applicazioni con questo framework._

<a name="Overview" />

## <a name="overview"></a>Panoramica

Xamarin.Forms è un framework che consente agli utenti di creare rapidamente interfacce utente multipiattaforma. Usa una propria astrazione per l'interfaccia utente, che viene sottoposta a rendering usando i controlli nativi su iOS, Android o la piattaforma UWP. Ciò significa che le applicazioni possono condividere gran parte del codice dell'interfaccia utente pur mantenendo l'aspetto nativo della piattaforma di destinazione.

Xamarin.Forms consente la creazione rapida di prototipi di applicazioni che con il tempo possono convertirsi in applicazioni complesse. Dato che sono applicazioni native, le applicazioni Xamarin.Forms non hanno le limitazioni di altri toolkit, quali sandbox per i browser, API limitate o prestazioni insufficienti. Le applicazioni scritte con Xamarin.Forms sono in grado di usare tutte le API o le funzionalità della piattaforma sottostante, incluse ad esempio, ma senza limitazioni, CoreMotion PassKit e StoreKit in iOS, NFC e Google Play Services in Android e Tiles in Windows. È anche possibile creare applicazioni in cui parti dell'interfaccia utente vengono create con Xamarin.Forms e altre parti vengono create con il toolkit dell'interfaccia utente nativa.

Le applicazioni Xamarin.Forms hanno la stessa struttura delle applicazioni multipiattaforma tradizionali. L'approccio più comune è l'uso di [Librerie portabili](~/cross-platform/app-fundamentals/pcl.md) o [Progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md) per il codice condiviso e la creazione di applicazioni specifiche della piattaforma che eseguono il codice condiviso.

Per la creazione di interfacce utente in Xamarin.Forms sono disponibili due tecniche. La prima tecnica è la creazione delle interfacce utente solo con codice sorgente C#. La seconda tecnica è l'uso di *Extensible Application Markup Language* (XAML), un linguaggio di markup dichiarativo per la descrizione delle interfacce utente. Per altre informazioni su XAML, vedere [Nozioni di base su XAML](~/xamarin-forms/xaml/xaml-basics/index.md).

Questo articolo illustra le nozioni di base sul framework Xamarin.Forms e include gli argomenti seguenti:

-  [Analisi di un'applicazione Xamarin.Forms](#Examining_A_Xamarin.Forms_Application).
-  [Uso delle pagine e dei controlli di Xamarin.Forms](#Views_and_Layouts).
-  [Come usare e visualizzare un elenco di dati](#Lists_in_Xamarin.Forms).
-  [Come configurare il data binding](#Data_Binding).
-  [Come passare da una pagina all'altra](#Navigation).
-  [Passaggi successivi](#Next_Steps).

<a name="Examining_A_Xamarin_Forms_Application" />

### <a name="examining-a-xamarinforms-application"></a>Analisi di un'applicazione Xamarin.Forms

In Visual Studio e Visual Studio per Mac, il modello di app Xamarin.Forms predefinito crea la soluzione Xamarin.Forms più semplice possibile, che visualizza testo per l'utente. Se si esegue l'applicazione, il suo aspetto è simile agli screenshot seguenti:

[![](introduction-to-xamarin-forms-images/image05-sml.png "Applicazione Xamarin.Forms predefinita")](introduction-to-xamarin-forms-images/image05.png#lightbox "Applicazione Xamarin.Forms predefinita")

Ogni schermata negli screenshot corrisponde a un elemento *Page* in Xamarin.Forms. L'elemento [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) rappresenta un'*Attività* in Android, un *Controller di visualizzazione* in iOS o una *Pagina* nella piattaforma UWP (Universal Windows Platform). L'esempio degli screenshot precedenti crea un'istanza di un oggetto [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) e la usa per visualizzare un elemento [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/).

Per ottimizzare il riuso del codice di avvio, le applicazioni Xamarin.Forms hanno una sola classe denominata `App`, responsabile della creazione dell'istanza del primo elemento [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) che viene visualizzato. Un esempio della classe `App` è visualizzato nel codice seguente:

```csharp
public class App : Application
{
  public App ()
  {
    MainPage = new ContentPage {
      Content =  new Label
      {
          Text = "Hello, Forms !",
          VerticalOptions = LayoutOptions.CenterAndExpand,
          HorizontalOptions = LayoutOptions.CenterAndExpand,
      }
      };
  }
}
```

Questo codice crea un'istanza di un nuovo oggetto [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) che visualizza un singolo elemento [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/), centrato sia verticalmente che orizzontalmente nella pagina.

<a name="Launching_the_Initial_Xamarin_Forms_Page_on_Each_Platform" />

### <a name="launching-the-initial-xamarinforms-page-on-each-platform"></a>Avvio della pagina iniziale di Xamarin.Forms in ogni piattaforma

Per usare questo elemento [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) all'interno di un'applicazione, l'applicazione per ogni piattaforma deve inizializzare il framework Xamarin.Forms e rendere disponibile un'istanza dell'elemento [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) all'avvio. Questo passaggio di inizializzazione varia a seconda della piattaforma e viene illustrato nelle sezioni seguenti.

<a name="Launching_in_iOS" />

#### <a name="ios"></a>iOS

Per l'avvio della pagina iniziale di Xamarin.Forms in iOS, il progetto della piattaforma include la classe `AppDelegate` che eredita dalla classe `Xamarin.Forms.Platform.iOS.FormsApplicationDelegate`, come illustrato nell'esempio di codice seguente:

```csharp
[Register("AppDelegate")]
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
      global::Xamarin.Forms.Forms.Init ();
      LoadApplication (new App ());
      return base.FinishedLaunching (app, options);
    }
}
```

La sostituzione `FinishedLoading` inizializza il framework Xamarin.Forms chiamando il metodo `Init`. In questo modo l'implementazione specifica di Xamarin.Forms per iOS viene caricata nell'applicazione prima che il controller della visualizzazione radice sia impostato dalla chiamata del metodo `LoadApplication`.

<a name="Launching_in_Android" />

#### <a name="android"></a>Android

Per l'avvio della pagina iniziale di Xamarin.Forms in Android, il progetto della piattaforma include codice che crea un elemento `Activity` con l'attributo `MainLauncher` e con l'attività che eredita dalla classe `FormsApplicationActivity`, come visualizzato nell'esempio di codice seguente:

```csharp
namespace HelloXamarinFormsWorld.Android
{
    [Activity(Label = "HelloXamarinFormsWorld", MainLauncher = true,
        ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            Xamarin.Forms.Forms.Init(this, bundle);
            LoadApplication (new App ());
        }
    }
}
```

La sostituzione `OnCreate` inizializza il framework Xamarin.Forms chiamando il metodo `Init`. In questo modo l'implementazione specifica di Xamarin.Forms per Android viene caricata nell'applicazione prima del caricamento dell'applicazione Xamarin.Forms.

#### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Nelle applicazioni della piattaforma UWP (Universal Windows Platform) il metodo `Init` che inizializza il framework Xamarin.Forms viene chiamato dalla classe `App`:

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

Di conseguenza viene caricata nell'applicazione l'implementazione di Xamarin.Forms specifica per la piattaforma UWP. La pagina iniziale di Xamarin.Forms viene avviata dalla classe `MainPage`, come illustrato nell'esempio di codice seguente:

```csharp
public partial class MainPage
{
    public MainPage()
    {
      this.InitializeComponent();
      this.LoadApplication(new HelloXamarinFormsWorld.App());
    }
}
```

L'applicazione Xamarin.Forms viene caricata con il metodo `LoadApplication`.

<a name="Views_and_Layouts" />

### <a name="views-and-layouts"></a>Visualizzazioni e layout

Per creare l'interfaccia utente di un'applicazione in Xamarin.Forms è possibile usare quattro gruppi di controlli principali.

1. **Pagine**: le pagine di Xamarin.Forms rappresentano le schermate dell'applicazione multipiattaforma per dispositivi mobili. Per altre informazioni sulle pagine, vedere [Xamarin.Forms Pages](~/xamarin-forms/user-interface/controls/pages.md) (Pagine di Xamarin.Forms).
1. **Layout**: i layout di Xamarin.Forms sono contenitori usati per convertire le visualizzazioni in strutture logiche. Per altre informazioni sui layout, vedere [Xamarin.Forms Layouts](~/xamarin-forms/user-interface/controls/layouts.md) (Layout di Xamarin.Forms).
1. **Visualizzazioni**: le visualizzazioni di Xamarin.Forms sono i controlli visualizzati nell'interfaccia utente, ad esempio le etichette, i pulsanti e le caselle di immissione testo. Per altre informazioni sulle visualizzazioni, vedere [Xamarin.Forms Views](~/xamarin-forms/user-interface/controls/views.md) (Visualizzazioni di Xamarin.Forms).
1. **Celle**: le celle di Xamarin.Forms sono elementi speciali usati per le voci di un elenco e descrivono la rappresentazione di ogni elemento di un elenco. Per altre informazioni sulle celle, vedere [Xamarin.Forms Cells](~/xamarin-forms/user-interface/controls/cells.md) (Celle di Xamarin.Forms).

In fase di runtime ogni controllo viene mappato al controllo nativo equivalente, che viene restituito nel rendering.

I controlli sono ospitati all'interno di un layout. Ora si esamina la classe [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), che implementa un layout di uso comune.

<a name="StackLayout" />

#### <a name="stacklayout"></a>StackLayout

L'elemento [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) semplifica lo sviluppo di applicazioni multipiattaforma disponendo automaticamente i controlli sullo schermo, indipendentemente dalle dimensioni dello schermo stesso. Gli elementi figlio vengono posizionati uno dopo l'altro, orizzontalmente o verticalmente, nell'ordine in cui sono stati aggiunti. La quantità di spazio usata dall'elemento `StackLayout` dipende dall'impostazione delle proprietà [`HorizontalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [`VerticalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/), ma per impostazione predefinita `StackLayout` prova a usare l'intero schermo.

Il codice XAML seguente visualizza un esempio dell'uso di [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) per disporre tre controlli [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/):

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample1" Padding="20">
  <StackLayout Spacing="10">
    <Label Text="Stop" BackgroundColor="Red" Font="20" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" />
    <Label Text="Go" BackgroundColor="Green" Font="20" />
  </StackLayout>
</ContentPage>
```

Il codice C# equivalente è visualizzato nell'esempio seguente:

```csharp
public class StackLayoutExample : ContentPage
{
    public StackLayoutExample()
    {
        Padding = new Thickness(20);
        var red = new Label
        {
            Text = "Stop", BackgroundColor = Color.Red, FontSize = 20
        };
        var yellow = new Label
        {
            Text = "Slow down", BackgroundColor = Color.Yellow, FontSize = 20
        };
        var green = new Label
        {
            Text = "Go", BackgroundColor = Color.Green, FontSize = 20
        };

        Content = new StackLayout
        {
            Spacing = 10,
            Children = { red, yellow, green }
        };
    }
}
```

Per impostazione predefinita [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) definisce l'orientamento verticale, come illustrato negli screenshot seguenti:

[![](introduction-to-xamarin-forms-images/image09-sml.png "StackLayout verticale")](introduction-to-xamarin-forms-images/image09.png#lightbox "StackLayout verticale")

L'orientamento di [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) può essere convertito in orizzontale, come illustrato nell'esempio di codice XAML seguente:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample2" Padding="20">
  <StackLayout Spacing="10" VerticalOptions="End" Orientation="Horizontal" HorizontalOptions="Start">
    <Label Text="Stop" BackgroundColor="Red" Font="20" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" />
    <Label Text="Go" BackgroundColor="Green" Font="20" />
  </StackLayout>
</ContentPage>
```

Il codice C# equivalente è visualizzato nell'esempio seguente:

```csharp
public class StackLayoutExample: ContentPage
{
    public StackLayoutExample()
    {
        // Code that creates labels removed for clarity
        Content = new StackLayout
        {
            Spacing = 10,
            VerticalOptions = LayoutOptions.End,
            Orientation = StackOrientation.Horizontal,
            HorizontalOptions = LayoutOptions.Start,
            Children = { red, yellow, green }
        };
    }
}
```

Gli screenshot seguenti visualizzano il layout finale:

[![](introduction-to-xamarin-forms-images/image10-sml.png "StackLayout orizzontale")](introduction-to-xamarin-forms-images/image10.png#lightbox "StackLayout orizzontale")

Le dimensioni dei controlli possono essere impostate tramite le proprietà `HeightRequest` e `WidthRequest`, come illustrato nell'esempio di codice XAML seguente:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml" x:Class="HelloXamarinFormsWorldXaml.StackLayoutExample3" Padding="20">
  <StackLayout Spacing="10" VerticalOptions="End" Orientation="Horizontal" HorizontalOptions="Start">
    <Label Text="Stop" BackgroundColor="Red" Font="20" WidthRequest="100" />
    <Label Text="Slow down" BackgroundColor="Yellow" Font="20" WidthRequest="100" />
    <Label Text="Go" BackgroundColor="Green" Font="20" WidthRequest="200" />
  </StackLayout>
</ContentPage>
```

Il codice C# equivalente è visualizzato nell'esempio seguente:

```csharp
var red = new Label
{
    Text = "Stop", BackgroundColor = Color.Red, FontSize = 20, WidthRequest = 100
};
var yellow = new Label
{
    Text = "Slow down", BackgroundColor = Color.Yellow, FontSize = 20, WidthRequest = 100
};
var green = new Label
{
    Text = "Go", BackgroundColor = Color.Green, FontSize = 20, WidthRequest = 200
};

Content = new StackLayout
{
    Spacing = 10,
    VerticalOptions = LayoutOptions.End,
    Orientation = StackOrientation.Horizontal,
    HorizontalOptions = LayoutOptions.Start,
    Children = { red, yellow, green }
};
```

Gli screenshot seguenti visualizzano il layout finale:

[![](introduction-to-xamarin-forms-images/image11-sml.png "StackLayout orizzontale con LayoutOptions")](introduction-to-xamarin-forms-images/image11.png#lightbox "StackLayout orizzontale con LayoutOptions")

Per altre informazioni sulla classe [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), vedere [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

<a name="Lists_in_Xamarin_Forms" />

## <a name="lists-in-xamarinforms"></a>Elenchi in Xamarin.Forms

Il controllo [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) gestisce la visualizzazione di una raccolta di elementi sullo schermo. Ogni elemento in `ListView` è incluso in una singola cella. Per impostazione predefinita, `ListView` usa il modello [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) per il rendering di una singola riga di testo.

Il codice seguente visualizza un esempio semplice di [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/):

```csharp
var listView = new ListView
{
    RowHeight = 40
};
listView.ItemsSource = new string []
{
    "Buy pears", "Buy oranges", "Buy mangos", "Buy apples", "Buy bananas"
};
Content = new StackLayout
{
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = { listView }
};
```

La schermata seguente visualizza il controllo [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) risultante:

 ![](introduction-to-xamarin-forms-images/image13.png "ListView")

Per altre informazioni sul controllo [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), vedere [ListView](~/xamarin-forms/user-interface/listview/index.md).

<a name="Binding_to_a_Custom_Class" />

### <a name="binding-to-a-custom-class"></a>Associazione a una classe personalizzata

Il controllo [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) può anche visualizzare oggetti personalizzati usando il modello [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) predefinito.

L'esempio di codice seguente visualizza la classe `TodoItem`:

```csharp
public class TodoItem
{
    public string Name { get; set; }
    public bool Done { get; set; }
}
```

Il controllo [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) può essere popolato come nell'esempio di codice seguente:

```csharp
listView.ItemsSource = new TodoItem [] {
    new TodoItem { Name = "Buy pears" },
    new TodoItem { Name = "Buy oranges", Done=true} ,
    new TodoItem { Name = "Buy mangos" },
    new TodoItem { Name = "Buy apples", Done=true },
    new TodoItem { Name = "Buy bananas", Done=true }
};
```

È possibile creare un'associazione per impostare quale proprietà `TodoItem` viene visualizzata da [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), come illustrato nell'esempio di codice seguente:

```csharp
listView.ItemTemplate = new DataTemplate(typeof(TextCell));
listView.ItemTemplate.SetBinding(TextCell.TextProperty, "Name");
```

In questo modo si crea un'associazione che specifica il percorso della proprietà `TodoItem.Name` e restituisce come risultato lo screenshot visualizzato sopra.

Per altre informazioni sull'associazione a una classe personalizzata, vedere [ListView Data Sources](~/xamarin-forms/user-interface/listview/data-and-databinding.md) (Origini dati ListView).

<a name="Selecting_an_Item_in_a_ListView" />

### <a name="selecting-an-item-in-a-listview"></a>Selezione di un elemento in un controllo ListView

Per offrire una risposta quando un utente tocca una cella in un [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) è necessario gestire l'evento [`ItemSelected`](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) come dimostrato nell'esempio di codice seguente:

```csharp
listView.ItemSelected += async (sender, e) => {
    await DisplayAlert("Tapped!", e.SelectedItem + " was tapped.", "OK");
};
```

Se è incluso in un controllo [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), il metodo [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) può essere usato per aprire una nuova pagina mediante la navigazione all'indietro incorporata. L'evento [`ItemSelected`](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/) può accedere all'oggetto che era associato alla cella mediante la proprietà [`e.SelectedItem`](https://developer.xamarin.com/api/property/Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem/), associarlo a una nuova pagina e visualizzare la nuova pagina mediante `PushAsync`, come illustrato nell'esempio di codice seguente:

```csharp
listView.ItemSelected += async (sender, e) => {
    var todoItem = (TodoItem)e.SelectedItem;
    var todoPage = new TodoItemPage(todoItem); // so the new page shows correct data
    await Navigation.PushAsync(todoPage);
};
```

L'implementazione della navigazione all'indietro incorporata varia a seconda della piattaforma. Per altre informazioni, vedere [Navigazione](#Navigation).

Per altre informazioni sulla selezione [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), vedere [ListView Interactivity](~/xamarin-forms/user-interface/listview/interactivity.md) (Interattività ListView).

<a name="Customizing_the_appearance_of_a_cell" />

### <a name="customizing-the-appearance-of-a-cell"></a>Personalizzazione dell'aspetto di una cella

È possibile personalizzare l'aspetto di una cella creando una sottoclasse della classe [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) e impostando il tipo di questa classe sulla proprietà [`ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/) di [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/).

La cella dello screenshot seguente è costituita da un controllo [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) e da due controlli [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/):

 ![](introduction-to-xamarin-forms-images/image14.png "ListView con aspetto della cella personalizzato")

Per creare questo layout personalizzato è necessario creare una sottoclasse della classe [`ViewCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/), come illustrato nell'esempio di codice seguente:

```csharp
class EmployeeCell : ViewCell
{
    public EmployeeCell()
    {
        var image = new Image
        {
            HorizontalOptions = LayoutOptions.Start
        };
        image.SetBinding(Image.SourceProperty, new Binding("ImageUri"));
        image.WidthRequest = image.HeightRequest = 40;

        var nameLayout = CreateNameLayout();
        var viewLayout = new StackLayout()
        {
           Orientation = StackOrientation.Horizontal,
           Children = { image, nameLayout }
        };
        View = viewLayout;
    }

    static StackLayout CreateNameLayout()
    {
        var nameLabel = new Label
        {
            HorizontalOptions= LayoutOptions.FillAndExpand
        };
        nameLabel.SetBinding(Label.TextProperty, "DisplayName");

        var twitterLabel = new Label
        {
           HorizontalOptions = LayoutOptions.FillAndExpand,
           Font = Fonts.Twitter
        };
        twitterLabel.SetBinding(Label.TextProperty, "Twitter");

        var nameLayout = new StackLayout()
        {
           HorizontalOptions = LayoutOptions.StartAndExpand,
           Orientation = StackOrientation.Vertical,
           Children = { nameLabel, twitterLabel }
        };
        return nameLayout;
    }
}
```

Il codice esegue le attività seguenti:

-  Aggiunge un controllo [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) e lo associa alla proprietà `ImageUri` dell'oggetto `Employee`. Per altre informazioni sul data binding, vedere [Data binding](#Data_Binding).
-  Crea un elemento [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) con orientamento verticale che contiene i due controlli [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/). I controlli `Label` vengono associati a `DisplayName` e alle proprietà `Twitter` dell'oggetto `Employee`.
-  Crea un elemento [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) che ospita gli elementi [`Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) e `StackLayout` esistenti. Gli elementi figlio vengono disposti con orientamento orizzontale.

Dopo sua creazione, la cella personalizzata può essere usata da un controllo [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) mediante wrapping in un elemento [`DataTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), come illustrato nell'esempio di codice seguente:

```csharp
List<Employee> myListOfEmployeeObjects = GetAListOfAllEmployees();
var listView = new ListView
{
    RowHeight = 40
};
listView.ItemsSource = myListOfEmployeeObjects;
listView.ItemTemplate = new DataTemplate(typeof(EmployeeCell));
```

Questo codice visualizza un elenco `List` di `Employee` a [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Il rendering di ogni cella viene eseguito mediante la classe `EmployeeCell`. `ListView` passa l'oggetto `Employee` a `EmployeeCell` come [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/).

Per altre informazioni sulla personalizzazione dell'aspetto delle celle, vedere [Cell Appearance](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md) (Aspetto delle celle).

<a name="Using_XAML_to_Create_and_Customize_A_List" />

### <a name="using-xaml-to-create-and-customize-a-list"></a>Uso del codice XAML per creare e personalizzare un elenco

L'equivalente in XAML dell'elemento [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) della sezione precedente è illustrato nell'esempio di codice seguente:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:local="clr-namespace:XamarinFormsXamlSample;assembly=XamarinFormsXamlSample"
             xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
             x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
             Title="Employee List">
  <ListView x:Name="listView" IsVisible="false" ItemsSource="{x:Static local:App.Employees}" ItemSelected="EmployeeListOnItemSelected">
    <ListView.ItemTemplate>
      <DataTemplate>
        <ViewCell>
          <ViewCell.View>
            <StackLayout Orientation="Horizontal">
              <Image Source="{Binding ImageUri}" WidthRequest="40" HeightRequest="40" />
              <StackLayout Orientation="Vertical" HorizontalOptions="StartAndExpand">
                <Label Text="{Binding DisplayName}" HorizontalOptions="FillAndExpand" />
                <Label Text="{Binding Twitter}" Font="{x:Static constants:Fonts.Twitter}"/>
              </StackLayout>
            </StackLayout>
          </ViewCell.View>
        </ViewCell>
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

Questo codice XAML definisce un [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) che contiene un [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). L'origine dati di `ListView` è impostata tramite l'attributo [`ItemsSource`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemsSource/). Il layout di ogni riga in `ItemsSource` è definito all'interno dell'elemento [`ListView.ItemTemplate`](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemsView%3CTVisual%3E.ItemTemplate/).

<a name="Data_Binding" />

## <a name="data-binding"></a>Data binding

Il data binding (o associazione di dati) consente di connettere due oggetti, detti oggetti di *origine* e di *destinazione*. L'oggetto di *origine* visualizza i dati. L'oggetto di *destinazione* usa (e spesso visualizza) i dati dall'oggetto di origine. Ad esempio un elemento [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) (oggetto di *destinazione*) associa comunemente la sua proprietà [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) a una proprietà `string` pubblica in un oggetto di *origine*. Il diagramma che segue illustra la relazione di associazione:

![](introduction-to-xamarin-forms-images/data-binding.png "Data Binding")

Il vantaggio principale del data binding sta nel fatto che non è più necessario preoccuparsi della sincronizzazione tra le visualizzazioni e l'origine dati. Le modifiche apportate all'oggetto di *origine* vengono trasmesse automaticamente in background all'oggetto di *destinazione* dal framework di associazione. Facoltativamente, le modifiche all'oggetto di destinazione possono anche essere respinte all'oggetto di *origine*.

La definizione del data binding è un processo in due fasi:

- La proprietà [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) dell'oggetto di *destinazione* deve essere impostata sull'*origine*.
- È necessario stabilire un'associazione tra la *destinazione* e l'*origine*. In XAML questo si ottiene usando l'estensione di markup [`Binding`](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.BindingExtension/). In C# questo si ottiene tramite il metodo [`SetBinding`](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/).

Per altre informazioni sul data binding, vedere [Data Binding Basics](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) (Nozioni di base sul data binding).

### <a name="xaml"></a>XAML

Il codice seguente visualizza un esempio di esecuzione di data binding in XAML:

```xaml
<Entry Text="{Binding FirstName}" ... />
```

Viene definita un'associazione tra la proprietà [`Entry.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) e la proprietà `FirstName` dell'oggetto di *origine*. Le modifiche apportate nel controllo `Entry` vengono propagate automaticamente all'oggetto `employeeToDisplay`. Analogamente, se si apportano modifiche alla proprietà `employeeToDisplay.FirstName` il motore di associazione di Xamarin.Forms aggiorna anche il contenuto del controllo `Entry`. Questa funzionalità è detta *associazione bidirezionale*. Per il funzionamento corretto dell'associazione bidirezionale è necessario che la classe del modello implementi l'interfaccia `INotifyPropertyChanged`.

Anche se la proprietà [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) della classe `EmployeeDetailPage` può essere impostata in XAML, in questo esempio viene impostata in code-behind su un'istanza di un oggetto `Employee`:

```csharp
public EmployeeDetailPage(Employee employee)
{
    InitializeComponent();
    this.BindingContext = employee;
}
```

È possibile impostare individualmente la proprietà [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) di ogni oggetto di *destinazione*, ma questa operazione non è necessaria. `BindingContext` è una proprietà speciale che viene ereditata da tutti gli elementi figlio. Pertanto quando `BindingContext` in [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) è impostato su un'istanza `Employee`, tutti gli elementi figlio di `ContentPage` hanno lo stesso `BindingContext` e possono eseguire l'associazione alle proprietà pubbliche dell'oggetto `Employee`.

### <a name="c35"></a>C&#35;

Il codice seguente visualizza un esempio di esecuzione di data binding in C#:

```csharp
public EmployeeDetailPage(Employee employeeToDisplay)
{
    this.BindingContext = employeeToDisplay;
    var firstName = new Entry()
    {
        HorizontalOptions = LayoutOptions.FillAndExpand
    };
    firstName.SetBinding(Entry.TextProperty, "FirstName");
    ...
}
```

Il costruttore [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) riceve un'istanza di un oggetto `Employee` e imposta [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) sull'oggetto da associare. Viene creata un'istanza di un controllo [`Entry`](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) e viene definita un'associazione tra la proprietà [`Entry.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Text/) e la proprietà `FirstName` dell'oggetto di *origine*. Le modifiche apportate nel controllo `Entry` vengono propagate automaticamente all'oggetto `employeeToDisplay`. Analogamente, se si apportano modifiche alla proprietà `employeeToDisplay.FirstName` il motore di associazione di Xamarin.Forms aggiorna anche il contenuto del controllo `Entry`. Questa funzionalità è detta *associazione bidirezionale*. Per il funzionamento corretto dell'associazione bidirezionale è necessario che la classe del modello implementi l'interfaccia `INotifyPropertyChanged`.

Il metodo `SetBinding` accetta due parametri. Il primo parametro specifica informazioni sul tipo di associazione. Il secondo parametro restituisce informazioni sulla destinazione dell'associazione o sulla modalità di associazione. In molti casi il secondo parametro è una semplice stringa contenente il nome della proprietà in [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/). Per l'associazione diretta a `BindingContext` viene usata la sintassi seguente:

```csharp
someLabel.SetBinding(Label.TextProperty, new Binding("."));
```

La sintassi con il punto indica a Xamarin.Forms di usare come origine dati [`BindingContext`](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) anziché una proprietà di `BindingContext`. Ciò risulta utile quando `BindingContext` è un tipo semplice, ad esempio `string` o `int`.

<a name="INotifyPropertyChanged" />

### <a name="property-change-notification"></a>Notifica di modifiche alle proprietà

Per impostazione predefinita, l'oggetto di *destinazione* riceve il valore dell'oggetto di *origine* solo dopo che è stata creata l'associazione. Per mantenere l'interfaccia utente sincronizzata con l'origine dati, è necessario avere un modo per notificare all'oggetto di *destinazione* che l'oggetto di *origine* è stato modificato. Questo meccanismo è visualizzato dall'interfaccia `INotifyPropertyChanged`. L'implementazione di questa interfaccia trasmette notifiche a qualsiasi controllo associato ai dati se il valore della proprietà sottostante viene modificato.

Gli oggetti che implementano `INotifyPropertyChanged` devono generare l'evento `PropertyChanged` quando una delle proprietà dell'oggetto viene aggiornata con un nuovo valore, come illustrato nell'esempio di codice seguente:

```csharp
public class MyObject : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    string _firstName;
    public string FirstName
    {
        get { return _firstName; }
        set
        {
            if (value.Equals(_firstName, StringComparison.Ordinal))
            {
                // Nothing to do - the value hasn't changed;
                return;
            }
            _firstName = value;
            OnPropertyChanged();

        }
    }

    void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        var handler = PropertyChanged;
        if (handler != null)
        {
            handler(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

Quando la proprietà `MyObject.FirstName` cambia viene chiamato il metodo `OnPropertyChanged`, che genera l'evento `PropertyChanged`. Per evitare la generazione di eventi non necessari, l'evento `PropertyChanged` non viene generato se il valore della proprietà non cambia.

Si noti che nel metodo `OnPropertyChanged` il parametro `propertyName` è decorato con l'attributo `CallerMemberName`. Ciò garantisce che se il metodo `OnPropertyChanged` viene chiamato con un valore `null`, l'attributo `CallerMemberName` restituisce il nome del metodo che ha chiamato `OnPropertyChanged`.

<a name="Navigation" />

## <a name="navigation"></a>Navigazione

Xamarin.Forms offre diverse esperienze di navigazione tra le pagine, a seconda del tipo [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) in uso. Per le istanze [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) sono disponibili due esperienze di navigazione:

- [Navigazione gerarchica](#Hierarchical_Navigation)
- [Navigazione modale](#Modal_Navigation)

Le classi [`CarouselPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/), [`MasterDetailPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) e [`TabbedPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) offrono esperienze di navigazione alternative. Per altre informazioni, vedere [Navigazione](~/xamarin-forms/app-fundamentals/navigation/index.md).

<a name="Hierarchical_Navigation" />

### <a name="hierarchical-navigation"></a>Navigazione gerarchica

La classe [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) offre un'esperienza di navigazione gerarchica in cui l'utente è in grado di scorrere le pagine avanti e indietro in base alle esigenze. La classe implementa la navigazione come stack LIFO (Last-In, First-Out) di oggetti [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/).

Nella navigazione gerarchica la classe [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) viene usata per navigare in uno stack di oggetti [`ContentPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/). Per passare da una pagina all'altra, un'applicazione esegue il push di una nuova pagina nello stack di navigazione, in cui diventa la pagina attiva. Per tornare alla pagina precedente, l'applicazione preleva la pagina corrente dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva.

La prima pagina aggiunta a uno stack di navigazione è definita pagina *radice* dell'applicazione e il codice seguente illustra come viene eseguita questa operazione:

```csharp
public App ()
{
    MainPage = new NavigationPage(new EmployeeListPage());
}
```

Per passare a `LoginPage` è necessario chiamare il metodo [`PushAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PushAsync/p/Xamarin.Forms.Page/) sulla proprietà [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) della pagina corrente, come illustrato nell'esempio di codice seguente:

```csharp
await Navigation.PushAsync(new LoginPage());
```

In questo modo il nuovo oggetto `LoginPage` viene inserito tramite push nello stack di navigazione, in cui diventa la pagina attiva.

La pagina attiva può essere prelevata dallo stack di navigazione premendo il pulsante *Indietro* (fisico o su schermo) del dispositivo. Per tornare a livello di codice alla pagina precedente, l'istanza `LoginPage` deve chiamare il metodo [`PopAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.NavigationPage.PopAsync()/), come illustrato nell'esempio di codice seguente:

```csharp
await Navigation.PopAsync();
```

Per altre informazioni sulla navigazione gerarchica, vedere [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

<a name="Modal_Navigation" />

### <a name="modal-navigation"></a>Navigazione modale

Xamarin.Forms dispone di supporto per le pagine modali. Una pagina modale richiede agli utenti il completamento di un'attività indipendente, dalla quale non è possibile spostarsi fino a quando non viene completata o annullata.

Una pagina modale può essere uno qualsiasi dei tipi [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) supportati da Xamarin.Forms. Per visualizzare una pagina modale, l'applicazione esegue il push della pagina nello stack di navigazione, in cui diventa la pagina attiva. Per tornare alla pagina precedente, l'applicazione preleva la pagina corrente dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva.

I metodi di navigazione modale sono esposti dalla proprietà [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) in qualsiasi tipo derivato [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). La proprietà [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) espone anche una proprietà [`ModalStack`](https://developer.xamarin.com/api/property/Xamarin.Forms.INavigation.ModalStack/) dalla quale è possibile ottenere le pagine modali nello stack di navigazione. Tuttavia non esiste nessun concetto di gestione modale dello stack o di prelievo e attivazione della pagina radice nella navigazione modale. Queste operazioni non sono supportate in tutti i casi sulle piattaforme sottostanti.

> [!NOTE]
> Per la navigazione modale nelle pagine non è necessaria un'istanza di [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/).

Per passare a `LoginPage` con la navigazione modale è necessario chiamare il metodo [`PushModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PushModalAsync(Xamarin.Forms.Page)/) sulla proprietà [`Navigation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Navigation/) della pagina corrente, come illustrato nell'esempio di codice seguente:

```csharp
await Navigation.PushModalAsync(new LoginPage());
```

Di conseguenza l'istanza `LoginPage` viene inserita tramite push nello stack di navigazione, in cui diventa la pagina attiva.

La pagina attiva può essere prelevata dallo stack di navigazione premendo il pulsante *Indietro* (fisico o su schermo) del dispositivo. Per tornare a livello di codice alla pagina originale, l'istanza `LoginPage` deve chiamare il metodo [`PopModalAsync`](https://developer.xamarin.com/api/member/Xamarin.Forms.INavigation.PopModalAsync()/), come illustrato nell'esempio di codice seguente:

```csharp
await Navigation.PopModalAsync();
```

Di conseguenza l'istanza `LoginPage` viene rimossa dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva.

Per altre informazioni sulla navigazione modale, vedere [Modal Pages](~/xamarin-forms/app-fundamentals/navigation/modal.md) (Pagine modali).

<a name="Next_Steps" />

## <a name="next-steps"></a>Passaggi successivi

Questo articolo introduttivo consente di iniziare a scrivere applicazioni Xamarin.Forms. I passaggi suggeriti che seguono includono informazioni sulle funzionalità seguenti:

- I modelli del controllo consentono di applicare e riapplicare facilmente un tema alle pagine dell'applicazione in fase di esecuzione. Per altre informazioni, vedere [Control Templates](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) (Modelli di controllo).
- I modelli di dati consentono di definire la presentazione dei dati nei controlli supportati. Per altre informazioni, vedere [Data Templates](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md) (Modelli di dati).
- Il codice condiviso può accedere alle funzionalità native tramite la classe [`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/). Per altre informazioni, vedere [Accessing Native Features with DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) (Accesso alle funzionalità native con DependencyService).
- Xamarin.Forms include un servizio di messaggistica semplice per l'invio e la ricezione di messaggi, riducendo l'accoppiamento tra le classi. Per altre informazioni, vedere [Publish and Subscribe with MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md) (Pubblicare e sottoscrivere con MessagingCenter).
- Il rendering di ogni pagina, layout e controllo viene eseguito in modo diverso su ogni piattaforma usando una classe `Renderer`, che a sua volta crea un controllo nativo, lo dispone sullo schermo e aggiunge il comportamento specificato nel codice condiviso. Gli sviluppatori possono implementare le proprie classi `Renderer` per personalizzare l'aspetto e/o il comportamento di un controllo. Per altre informazioni, vedere [Custom Renderers](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) (Renderer personalizzati).
- Anche gli effetti consentono la personalizzazione dei controlli nativi in ogni piattaforma. Gli effetti vengono creati in processi specifici per la piattaforma creando sottoclassi del controllo [`PlatformEffect`](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/) e vengono usati associandoli a un controllo di Xamarin.Forms appropriato. Per altre informazioni, vedere [Effects](~/xamarin-forms/app-fundamentals/effects/index.md) (Effetti).

In alternativa, per informazioni aggiuntive su Xamarin.Forms un buon punto di partenza è il libro Creating Mobile Apps with Xamarin.Forms (Creazione di app per dispositivi mobili con Xamarin.Forms) di Charles Petzold. Per altre informazioni, vedere [Creating Mobile Apps with Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) (Creazione di app per dispositivi mobili con Xamarin.Forms).

## <a name="summary"></a>Riepilogo

Questo articolo ha presentato Xamarin.Forms indicando come iniziare a scrivere applicazioni con questo framework. Xamarin.Forms è un'astrazione toolkit per interfacce utente con supporto nativo multipiattaforma, che consente agli sviluppatori di creare con facilità interfacce utente che possono essere condivise in Android, iOS e la piattaforma UWP. Il rendering delle interfacce utente viene eseguito con i controlli nativi della piattaforma di destinazione, consentendo alle applicazioni Xamarin.Forms di mantenere l'aspetto appropriato per ogni piattaforma.


## <a name="related-links"></a>Collegamenti correlati

- [Nozioni di base su XAML](~/xamarin-forms/xaml/xaml-basics/index.md)
- [Riferimento per i controlli](~/xamarin-forms/user-interface/controls/index.md)
- [Interfaccia utente](~/xamarin-forms/user-interface/index.md)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Esempi introduttivi](https://developer.xamarin.com/samples/xamarin-forms/GettingStarted/)
- [Xamarin.Forms](https://developer.xamarin.com/api/namespace/Xamarin.Forms/)
- [Apprendimento guidato gratuito (video)](https://university.xamarin.com/self-guided)
- [Cartella di lavoro iOS Xamarin.Forms](https://developer.xamarin.com/workbooks/xamarin-forms/getting-started/GettingStartedWithXamarinForms-ios.workbook)
