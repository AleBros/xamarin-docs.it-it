---
title: Approfondimenti per la guida di avvio rapido di Xamarin.Forms
description: Questo articolo esamina i concetti fondamentali dello sviluppo di applicazioni con Xamarin.Forms. Gli argomenti trattati includono l'anatomia di un'applicazione Xamarin.Forms, le nozioni di base su architettura e applicazione e l'interfaccia utente.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.custom: video
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
ms.openlocfilehash: c0e8ec27898cc842d485967e525c2936d7a0f56d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "77131056"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Approfondimenti per la guida di avvio rapido di Xamarin.Forms

Nell'argomento di [avvio rapido di Xamarin.Forms](~/get-started/index.yml) è stata compilata l'applicazione Notes. Questo articolo esamina ciò che è stato compilato per comprendere meglio le nozioni di base del funzionamento delle applicazioni Xamarin.Forms.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introduzione a Visual Studio

Visual Studio organizza il codice in *soluzioni* e *progetti*. Una soluzione è un contenitore per uno o più progetti. Un progetto può essere un'applicazione, una libreria di supporto, un'applicazione di test e altro ancora. L'applicazione Notes è costituita da una soluzione contenente quattro progetti, come illustra lo screenshot seguente:

![](deepdive-images/vs/solution.png "Visual Studio Solution Explorer")

I progetti sono:

- Notes: progetto di libreria .NET Standard che contiene tutto il codice condiviso e l'interfaccia utente condivisa.
- Notes.Android: progetto che contiene il codice specifico di Android ed è il punto di ingresso per l'applicazione Android.
- Notes.iOS: progetto che contiene il codice specifico di iOS ed è il punto di ingresso per l'applicazione iOS.
- Notes.UWP: progetto che contiene il codice specifico della piattaforma UWP (Universal Windows Platform) ed è il punto di ingresso per l'applicazione UWP.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia di un'applicazione Xamarin.Forms

Lo screenshot seguente illustra il contenuto del progetto di libreria .NET Standard Notes in Visual Studio:

![](deepdive-images/vs/net-standard-project.png "Phoneword .NET Standard Project Contents")

Il progetto ha un nodo **Dipendenze** contenente i nodi **NuGet** e **SDK**:

- **** NuGet&ndash;: pacchetti NuGet Xamarin.Forms e sqlite-net-pcl aggiunti al progetto.
- **SDK** &ndash; `NETStandard.Library` il metapacchetto che fa riferimento al set completo di pacchetti NuGet che definiscono .NET Standard.

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introduzione a Visual Studio per Mac

[Visual Studio per Mac](/visualstudio/mac/) segue la pratica di Visual Studio di organizzare il codice in *soluzioni* e *progetti*. Una soluzione è un contenitore per uno o più progetti. Un progetto può essere un'applicazione, una libreria di supporto, un'applicazione di test e altro ancora. L'applicazione Notes è costituita da una soluzione contenente tre progetti, come illustra lo screenshot seguente:

![](deepdive-images/vsmac/solution.png "Visual Studio for Mac Solution Pane")

I progetti sono:

- Notes: progetto di libreria .NET Standard che contiene tutto il codice condiviso e l'interfaccia utente condivisa.
- Notes.Android: progetto che contiene il codice specifico di Android ed è il punto di ingresso per le applicazioni Android.
- Notes.iOS: progetto che contiene il codice specifico di iOS ed è il punto di ingresso per le applicazioni iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia di un'applicazione Xamarin.Forms

Lo screenshot seguente illustra il contenuto del progetto di libreria .NET Standard Notes in Visual Studio per Mac:

![](deepdive-images/vsmac/net-standard-project.png "Phoneword .NET Standard Library Project Contents")

Il progetto ha un nodo **Dipendenze** contenente i nodi **NuGet** e **SDK**:

- **** NuGet&ndash;: pacchetti NuGet Xamarin.Forms e sqlite-net-pcl aggiunti al progetto.
- **SDK** &ndash; `NETStandard.Library` il metapacchetto che fa riferimento al set completo di pacchetti NuGet che definiscono .NET Standard.

::: zone-end

Il progetto è costituito anche da alcuni file:

- **Data\NoteDatabase.cs**: questa classe contiene il codice per creare il database, leggere i dati dal database, scrivere i dati in esso ed eliminarli.
- **Models\Note.cs**: questa classe definisce un modello `Note` le cui istanze archiviano i dati relativi a ogni nota nell'applicazione.
- **App.xaml**: markup XAML per la classe `App`, che definisce un dizionario risorse per l'applicazione.
- **App.Xaml.cs**: code-behind per la classe `App`, che è responsabile della creazione di istanze della prima pagina visualizzata dall'applicazione in ogni piattaforma e della gestione degli eventi del ciclo di vita dell'applicazione.
- **AssemblyInfo.cs**: questo file contiene un attributo dell'applicazione relativo al progetto, applicato a livello di assembly.
- **NotesPage.xaml**: markup XAML per la classe `NotesPage`, che definisce l'interfaccia utente per la pagina visualizzata all'avvio dell'applicazione.
- **NotesPage.xaml.cs**: code-behind per la classe `NotesPage`, che contiene la logica di business eseguita quando l'utente interagisce con la pagina.
- **NoteEntryPage.xaml**: markup XAML per la classe `NoteEntryPage`, che definisce l'interfaccia utente per la pagina visualizzata quando l'utente immette una nota.
- **NoteEntryPage.xaml.cs**: code-behind per la classe `NoteEntryPage`, che contiene la logica di business eseguita quando l'utente interagisce con la pagina.

Per altre informazioni sull'anatomia di un'applicazione Xamarin.iOS, vedere l'[analisi dettagliata di un'applicazione Xamarin.iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application). Per altre informazioni sull'anatomia di un'applicazione Xamarin.Android, vedere l'[analisi dettagliata di un'applicazione Xamarin.Android](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy).

## <a name="architecture-and-application-fundamentals"></a>Architettura e concetti fondamentali dell'applicazione

Un'applicazione Xamarin.Forms ha la stessa struttura di un'applicazione multipiattaforma tradizionale. Il codice condiviso in genere viene inserito in una libreria .NET Standard e le applicazioni specifiche della piattaforma usano il codice condiviso. Il diagramma seguente illustra una panoramica di questa relazione per l'applicazione Notes:

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "Notes Architecture")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "Notes Architecture")

::: zone-end

Per ottimizzare il riuso del codice di avvio, le applicazioni Xamarin.Forms hanno una sola classe denominata `App`, responsabile della creazione dell'istanza della prima pagina visualizzata dall'applicazione in ogni piattaforma, come illustra l'esempio di codice che segue:

```csharp
using Xamarin.Forms;

namespace Notes
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new NavigationPage(new NotesPage());
        }
        ...
    }
}
```

Questo codice `MainPage` imposta la `App` proprietà [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) della classe su `NotesPage` un'istanza il cui contenuto è un'istanza.

Il file **AssemblyInfo.cs** contiene inoltre un singolo attributo dell'applicazione, applicato a livello di assembly:

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

L'attributo [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attiva il compilatore XAML, in modo che XAML venga compilato direttamente nel linguaggio intermedio. Per altre informazioni, vedere [XAML Compilation](~/xamarin-forms/xaml/xamlc.md) (Compilazione XAML).

## <a name="launching-the-application-on-each-platform"></a>Avvio dell'applicazione in ogni piattaforma

### <a name="ios"></a>iOS

Per l'avvio della pagina iniziale di Xamarin.Forms in iOS, il progetto Notes.iOS definisce la classe `AppDelegate` che eredita dalla classe `FormsApplicationDelegate`:

```csharp
namespace Notes.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            global::Xamarin.Forms.Forms.Init();
            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }
    }
}
```

La sostituzione `FinishedLaunching` inizializza il framework Xamarin.Forms chiamando il metodo `Init`. In questo modo l'implementazione specifica di Xamarin.Forms per iOS viene caricata nell'applicazione prima che il controller della visualizzazione radice sia impostato dalla chiamata del metodo `LoadApplication`.

### <a name="android"></a>Android

Per l'avvio della pagina iniziale di Xamarin.Forms in Android, il progetto Notes.Android include codice che crea un elemento `Activity` con l'attributo `MainLauncher` e con l'attività che eredita dalla classe `FormsAppCompatActivity`:

```csharp
namespace Notes.Droid
{
    [Activity(Label = "Notes",
              Icon = "@mipmap/icon",
              Theme = "@style/MainTheme",
              MainLauncher = true,
              ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            TabLayoutResource = Resource.Layout.Tabbar;
            ToolbarResource = Resource.Layout.Toolbar;

            base.OnCreate(savedInstanceState);
            global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
            LoadApplication(new App());
        }
    }
}
```

La sostituzione `OnCreate` inizializza il framework Xamarin.Forms chiamando il metodo `Init`. In questo modo l'implementazione specifica di Xamarin.Forms per Android viene caricata nell'applicazione prima del caricamento dell'applicazione Xamarin.Forms.

::: zone pivot="windows"

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Nelle applicazioni della piattaforma UWP (Universal Windows Platform) il metodo `Init` che inizializza il framework Xamarin.Forms viene chiamato dalla classe `App`:

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

Di conseguenza viene caricata nell'applicazione l'implementazione di Xamarin.Forms specifica per la piattaforma UWP. La pagina iniziale di Xamarin.Forms viene avviata dalla classe `MainPage`:

```csharp
namespace Notes.UWP
{
    public sealed partial class MainPage
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.LoadApplication(new Notes.App());
        }
    }
}
```

L'applicazione Xamarin.Forms viene caricata con il metodo `LoadApplication`.

> [!NOTE]
> Le app UWP (Universal Windows Platform) si possono compilare con Xamarin.Forms, ma solo usando Visual Studio in Windows.

::: zone-end

## <a name="user-interface"></a>Interfaccia utente

Per creare l'interfaccia utente di un'applicazione in Xamarin.Forms è possibile usare quattro gruppi di controlli principali:

1. **Pagine**: le pagine di Xamarin.Forms rappresentano le schermate dell'applicazione multipiattaforma per dispositivi mobili. L'applicazione Notes [`ContentPage`](xref:Xamarin.Forms.ContentPage) utilizza la classe per visualizzare singole schermate. Per altre informazioni sulle pagine, vedere [Xamarin.Forms Pages](~/xamarin-forms/user-interface/controls/pages.md) (Pagine di Xamarin.Forms).
1. **Visualizzazioni**: le visualizzazioni di Xamarin.Forms sono i controlli visualizzati nell'interfaccia utente, ad esempio le etichette, i pulsanti e le caselle di immissione testo. L'applicazione Notes [`ListView`](xref:Xamarin.Forms.ListView)finita [`Editor`](xref:Xamarin.Forms.Editor)utilizza [`Button`](xref:Xamarin.Forms.Button) le viste , e . Per altre informazioni sulle visualizzazioni, vedere [Xamarin.Forms Views](~/xamarin-forms/user-interface/controls/views.md) (Visualizzazioni di Xamarin.Forms).
1. **Layout**: i layout di Xamarin.Forms sono contenitori usati per convertire le visualizzazioni in strutture logiche. L'applicazione Notes [`StackLayout`](xref:Xamarin.Forms.StackLayout) utilizza la classe per disporre [`Grid`](xref:Xamarin.Forms.Grid) le visualizzazioni in uno stack verticale e la classe per disporre i pulsanti orizzontalmente. Per altre informazioni sui layout, vedere [Xamarin.Forms Layouts](~/xamarin-forms/user-interface/controls/layouts.md) (Layout di Xamarin.Forms).
1. **Celle**: le celle di Xamarin.Forms sono elementi speciali usati per le voci di un elenco e descrivono la rappresentazione di ogni elemento di un elenco. L'applicazione Notes [`TextCell`](xref:Xamarin.Forms.TextCell) utilizza il per visualizzare due elementi per ogni riga dell'elenco. Per altre informazioni sulle celle, vedere [Xamarin.Forms Cells](~/xamarin-forms/user-interface/controls/cells.md) (Celle di Xamarin.Forms).

In fase di runtime ogni controllo viene mappato al controllo nativo equivalente, che viene restituito nel rendering.

### <a name="layout"></a>Layout

L'applicazione Notes [`StackLayout`](xref:Xamarin.Forms.StackLayout) utilizza l'oggetto per semplificare lo sviluppo di applicazioni multipiattaforma disponendo automaticamente le visualizzazioni sullo schermo indipendentemente dalle dimensioni dello schermo. Gli elementi figlio vengono posizionati uno dopo l'altro, orizzontalmente o verticalmente, nell'ordine in cui sono stati aggiunti. La quantità `StackLayout` di spazio utilizzata [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) da [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) quella utilizzata dipende da `StackLayout` come sono impostate le proprietà e , ma per impostazione predefinita tenterà di utilizzare l'intero schermo.

Il codice XAML seguente mostra [`StackLayout`](xref:Xamarin.Forms.StackLayout) un esempio `NoteEntryPage`di utilizzo di un per eseguire il layout di :The following XAML code shows an example of using a to layout the :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
             Title="Note Entry">
    ...    
    <StackLayout Margin="{StaticResource PageMargin}">
        <Editor Placeholder="Enter your note"
                Text="{Binding Text}"
                HeightRequest="100" />
        <Grid>
            ...
        </Grid>
    </StackLayout>    
</ContentPage>
```

Per impostazione predefinita, l'impostazione [`StackLayout`](xref:Xamarin.Forms.StackLayout) presuppone un orientamento verticale. Tuttavia, può essere modificato in un [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) orientamento [`StackOrientation.Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal) orizzontale impostando la proprietà sul membro di enumerazione.

> [!NOTE]
> Le dimensioni delle visualizzazioni possono essere impostate tramite le proprietà `HeightRequest` e `WidthRequest`.

Per ulteriori informazioni [`StackLayout`](xref:Xamarin.Forms.StackLayout) sulla classe , vedere [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

### <a name="responding-to-user-interaction"></a>Risposta all'interazione dell'utente

Un oggetto definito in XAML può generare un evento che viene gestito nel file code-behind. Nell'esempio di `OnSaveButtonClicked` codice riportato di seguito `NoteEntryPage` viene illustrato il metodo nel [`Clicked`](xref:Xamarin.Forms.Button.Clicked) code-behind per la classe , che viene eseguito in risposta all'evento che viene attivato sul pulsante *Salva.*

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    await App.Database.SaveNoteAsync(note);
    await Navigation.PopAsync();
}
```

Il metodo `OnSaveButtonClicked` salva la nota nel database e torna alla pagina precedente.

> [!NOTE]
> Il file code-behind per una classe XAML è in grado di accedere a un oggetto definito in XAML usando il nome assegnato con l'attributo `x:Name`. Il valore assegnato a questo attributo ha le stesse regole delle variabili di C#, poiché deve iniziare con una lettera o un carattere di sottolineatura e non contenere spazi incorporati.

L'associazione del pulsante di salvataggio al metodo `OnSaveButtonClicked` si verifica nel markup XAML per la classe `NoteEntryPage`:

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>Elenchi

L'oggetto [`ListView`](xref:Xamarin.Forms.ListView) è responsabile della visualizzazione verticale di una raccolta di elementi in un elenco. Ogni elemento in `ListView` sarà contenuto in una singola cella.

Nell'esempio `NotesPage`di [`ListView`](xref:Xamarin.Forms.ListView) codice riportato di seguito viene illustrato l'oggetto da :

```xaml
<ListView x:Name="listView"
          Margin="{StaticResource PageMargin}"
          ItemSelected="OnListViewItemSelected">
    <ListView.ItemTemplate>
        <DataTemplate>
            <TextCell Text="{Binding Text}"
                      Detail="{Binding Date}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Il layout di ogni [`ListView`](xref:Xamarin.Forms.ListView) riga nell'elemento è definito all'interno dell'elemento e utilizza l'associazione [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) dati per visualizzare eventuali note recuperate dall'applicazione. La [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) proprietà è impostata sull'origine dati, in `NotesPage.xaml.cs`:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Questo codice popola l'oggetto [`ListView`](xref:Xamarin.Forms.ListView) con tutte le note archiviate nel database.

Quando viene selezionata [`ListView`](xref:Xamarin.Forms.ListView)una [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) riga nella , viene generato l'evento . Un gestore eventi, denominato `OnListViewItemSelected`, viene eseguito quando viene generato l'evento:

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

L'evento [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) può accedere all'oggetto associato [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) alla cella tramite la proprietà .

Per ulteriori informazioni [`ListView`](xref:Xamarin.Forms.ListView) sulla classe , vedere [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="navigation"></a>Navigazione

In Xamarin.Forms sono disponibili diverse esperienze [`Page`](xref:Xamarin.Forms.Page) di spostamento tra le pagine, a seconda del tipo utilizzato. Per [`ContentPage`](xref:Xamarin.Forms.ContentPage) le istanze lo spostamento può essere gerarchico o modale. Per informazioni sulla navigazione modale, vedere [Pagine modali Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

> [!NOTE]
> Le [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) classi [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) e forniscono esperienze di navigazione alternative. Per altre informazioni, vedere [Navigazione](~/xamarin-forms/app-fundamentals/navigation/index.md).

Nella navigazione gerarchica, la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe viene [`ContentPage`](xref:Xamarin.Forms.ContentPage) utilizzata per spostarsi all'interno di una pila di oggetti, avanti e indietro, come desiderato. La classe implementa la navigazione come stack di [`Page`](xref:Xamarin.Forms.Page) oggetti LIFO (Last-In, First-Out). Per passare da una pagina all'altra, un'applicazione esegue il push di una nuova pagina nello stack di navigazione, in cui diventa la pagina attiva. Per tornare alla pagina precedente, l'applicazione preleva la pagina corrente dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva.

La classe `NavigationPage` aggiungerà inoltre una barra di navigazione nella parte superiore della pagina che consente di visualizzare un titolo e un pulsante **Indietro** appropriato per la piattaforma per tornare alla pagina precedente.

La prima pagina aggiunta a uno stack di navigazione è definita pagina *radice* dell'applicazione e il codice seguente illustra come viene eseguita questa operazione nell'applicazione Notes:

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

Tutte [`ContentPage`](xref:Xamarin.Forms.ContentPage) le [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) istanze dispongono di una proprietà che espone metodi per modificare lo stack di pagine. Questi metodi devono essere richiamati solo [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)se l'applicazione include un oggetto . Per passare `NoteEntryPage`a , è necessario [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)) richiamare il metodo come illustrato nell'esempio di codice riportato di seguito:

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

In questo modo il nuovo oggetto `NoteEntryPage` viene inserito tramite push nello stack di navigazione, in cui diventa la pagina attiva.

La pagina attiva può essere prelevata dallo stack di navigazione premendo il pulsante *Indietro* (fisico o su schermo) del dispositivo. Per tornare a livello di codice `NoteEntryPage` alla pagina [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) originale, l'oggetto deve richiamare il metodo , come illustrato nell'esempio di codice seguente:To programmatically return back to the original page, the object must invoke the method, as demonstrated in the code example below:

```csharp
await Navigation.PopAsync();
```

Per altre informazioni sulla navigazione gerarchica, vedere [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="data-binding"></a>Associazione dati

Il data binding viene usato per semplificare il modo in cui un'applicazione Xamarin.Forms visualizza i propri dati e interagisce con essi. Stabilisce una connessione tra l'interfaccia utente e l'applicazione sottostante. La [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe contiene gran parte dell'infrastruttura per supportare l'associazione dati.

Il data binding (o associazione di dati) consente di connettere due oggetti, detti oggetti di *origine* e di *destinazione*. L'oggetto di *origine* visualizza i dati. L'oggetto di *destinazione* usa (e spesso visualizza) i dati dall'oggetto di origine. Ad esempio, [`Editor`](xref:Xamarin.Forms.Editor) un ( oggetto*di destinazione)* in genere associa la proprietà [`Text`](xref:Xamarin.Forms.InputView.Text) a una proprietà pubblica `string` in un oggetto di *origine.* Il diagramma che segue illustra la relazione di associazione:

![](deepdive-images/data-binding.png "Data Binding")

Il vantaggio principale del data binding sta nel fatto che non è più necessario preoccuparsi della sincronizzazione tra le visualizzazioni e l'origine dati. Le modifiche apportate all'oggetto di *origine* vengono trasmesse automaticamente in background all'oggetto di *destinazione* dal framework di associazione. Facoltativamente, le modifiche all'oggetto di destinazione possono anche essere respinte all'oggetto di *origine*.

La definizione del data binding è un processo in due fasi:

- La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) proprietà dell'oggetto *di destinazione* deve essere impostata *sull'origine.*
- È necessario stabilire un'associazione tra la *destinazione* e l'*origine*. In XAML, questo si [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) ottiene utilizzando l'estensione di markup.

Nell'applicazione Notes, la destinazione [`Editor`](xref:Xamarin.Forms.Editor) di associazione è `Note` la che [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) `NoteEntryPage` visualizza una nota, mentre l'istanza impostata come il di è l'origine di associazione.

La proprietà `BindingContext` di `NoteEntryPage` viene impostata durante la navigazione tra le pagine, come illustrato nell'esempio di codice seguente:

```csharp
async void OnNoteAddedClicked(object sender, EventArgs e)
{
    await Navigation.PushAsync(new NoteEntryPage
    {
        BindingContext = new Note()
    });
}

async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        await Navigation.PushAsync(new NoteEntryPage
        {
            BindingContext = e.SelectedItem as Note
        });
    }
}
```

Nel `OnNoteAddedClicked` metodo , che viene eseguito quando una nuova [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) nota viene aggiunta all'applicazione, il di `NoteEntryPage` è impostato su una nuova `Note` istanza. Nel `OnListViewItemSelected` metodo , che viene eseguito quando viene [`ListView`](xref:Xamarin.Forms.ListView)selezionata una nota esistente nella , `BindingContext` l'oggetto viene impostato `NoteEntryPage` sull'istanza selezionata, `Note` a cui si accede tramite la [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) proprietà .

> [!IMPORTANT]
> Mentre [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) la proprietà di ogni oggetto *di destinazione* può essere impostata singolarmente, questo non è necessario. `BindingContext` è una proprietà speciale che viene ereditata da tutti gli elementi figlio. Pertanto, `BindingContext` quando [`ContentPage`](xref:Xamarin.Forms.ContentPage) l'oggetto `Note` on è impostato su `ContentPage` un'istanza, tutti gli elementi figlio dell'oggetto hanno lo stesso `BindingContext`e possono essere associati alle proprietà pubbliche dell'oggetto. `Note`

Il [`Editor`](xref:Xamarin.Forms.Editor) `NoteEntryPage` in quindi viene `Text` associato `Note` alla proprietà dell'oggetto:

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

Viene stabilita [`Editor.Text`](xref:Xamarin.Forms.InputView.Text) un'associazione tra la proprietà e la `Text` proprietà dell'oggetto di *origine.* Le modifiche apportate nell'`Editor` vengono propagate automaticamente all'oggetto `Note`. Analogamente, se si apportano modifiche alla proprietà `Note.Text` il motore di binding di Xamarin.Forms aggiorna anche il contenuto dell'`Editor`. Questa funzionalità è detta *associazione bidirezionale*.

Per altre informazioni sul data binding, vedere [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="styling"></a>Stile

Le applicazioni Xamarin.Forms contengono spesso più elementi visivi con aspetto identico. L'impostazione dell'aspetto di ogni elemento visivo può essere ripetitiva e soggetta a errori. È invece possibile creare stili che definiscono l'aspetto e applicarli agli elementi visivi necessari.

La [`Style`](xref:Xamarin.Forms.Style) classe raggruppa una raccolta di valori di proprietà in un unico oggetto che può quindi essere applicato a più istanze di elementi visivi. Gli stili vengono [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)archiviati in un oggetto , a livello di applicazione, a livello di pagina o a livello di visualizzazione. Scegliere dove definire un oggetto `Style` ha effetto su dove può essere usato:

- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di applicazione possono essere applicate in tutta l'applicazione.
- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di pagina possono essere applicate alla pagina e ai relativi elementi figlio.
- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di vista possono essere applicate alla vista e ai relativi elementi figlio.

> [!IMPORTANT]
> Tutti gli stili usati nell'applicazione vengono archiviati nel dizionario risorse dell'applicazione per evitare duplicati. Tuttavia, il codice XAML specifico di una pagina non deve essere incluso nel dizionario risorse dell'applicazione per evitare che le risorse vengano analizzate all'avvio dell'applicazione invece che quando richiesto da una pagina.

Ogni [`Style`](xref:Xamarin.Forms.Style) istanza contiene un insieme [`Setter`](xref:Xamarin.Forms.Setter) di uno `Setter` o [`Property`](xref:Xamarin.Forms.Setter.Property) più [`Value`](xref:Xamarin.Forms.Setter.Value)oggetti, ognuno dei quali ha a e un oggetto . `Property` è il nome della proprietà associabile dell'elemento a cui viene applicato lo stile e `Value` è il valore applicato alla proprietà. L'esempio di codice seguente illustra uno stile da `NoteEntryPage`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
             Title="Note Entry">
    <ContentPage.Resources>
        <!-- Implicit styles -->
        <Style TargetType="{x:Type Editor}">
            <Setter Property="BackgroundColor"
                    Value="{StaticResource AppBackgroundColor}" />
        </Style>
        ...
    </ContentPage.Resources>
    ...
</ContentPage>
```

Questo stile viene [`Editor`](xref:Xamarin.Forms.Editor) applicato a tutte le istanze della pagina.

Quando si [`Style`](xref:Xamarin.Forms.Style)crea [`TargetType`](xref:Xamarin.Forms.Style.TargetType) un oggetto , la proprietà è sempre obbligatoria.

> [!NOTE]
> Lo stile di un'applicazione Xamarin.Forms viene in genere definito usando gli stili XAML, ma Xamarin.Forms supporta anche la definizione dello stile degli elementi visivi tramite fogli di stile CSS. Per altre informazioni, vedere [Definizione dello stile delle app Xamarin.Forms con fogli di stile CSS](~/xamarin-forms/user-interface/styles/css/index.md).

Per altre informazioni sugli stili XAML, vedere [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

### <a name="providing-platform-specific-styles"></a>Creazione di stili specifici della piattaforma

L'estensione di markup `OnPlatform` consente di personalizzare l'aspetto dell'interfaccia utente per ogni piattaforma:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.App">
    <Application.Resources>
        ...
        <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
        <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
        <Color x:Key="iOSNavigationBarTextColor">Black</Color>
        <Color x:Key="AndroidNavigationBarTextColor">White</Color>

        <Style TargetType="{x:Type NavigationPage}">
            <Setter Property="BarBackgroundColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                       Android={StaticResource AndroidNavigationBarColor}}" />
             <Setter Property="BarTextColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                       Android={StaticResource AndroidNavigationBarTextColor}}" />           
        </Style>
        ...
    </Application.Resources>
</Application>
```

In [`Style`](xref:Xamarin.Forms.Style) questo [`Color`](xref:Xamarin.Forms.Color) modo [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) vengono [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor) impostati valori diversi per le proprietà e di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), a seconda della piattaforma utilizzata.

Per altre informazioni sulle estensioni di markup XAML, vedere [XAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md) (Estensioni di markup XAML). Per informazioni sull'estensione di markup `OnPlatform`, vedere [Estensione di markup OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="testing-and-deployment"></a>Test e distribuzione

Visual Studio per Mac e Visual Studio offrono entrambi numerose opzioni per il test e la distribuzione di un'applicazione. Il debug delle applicazioni fa parte del ciclo di vita dello sviluppo delle applicazioni e consente di diagnosticare i problemi del codice. Per altre informazioni, vedere gli articoli relativi all'[impostazione di un punto di interruzione](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), al [passaggio attraverso il codice](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) e all'[output di informazioni alla finestra del log](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

I simulatori sono un ottimo strumento per iniziare a distribuire e testare e offrono funzionalità utili per testare le applicazioni. Tuttavia, gli utenti non useranno l'applicazione finale in un simulatore, quindi le applicazioni devono essere testate nei dispositivi reali presto e spesso. Per altre informazioni sul provisioning dei dispositivi iOS, vedere [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md). Per altre informazioni sul provisioning dei dispositivi Android, vedere [Set Up Device for Development](~/android/get-started/installation/set-up-device-for-development.md) (Configurare il dispositivo per lo sviluppo).

## <a name="next-steps"></a>Passaggi successivi

Questo approfondimento ha esaminato i concetti fondamentali dello sviluppo di applicazioni con Xamarin.Forms. I passaggi suggeriti che seguono includono informazioni sulle funzionalità seguenti:

- Per creare l'interfaccia utente di un'applicazione in Xamarin.Forms è possibile usare quattro gruppi di controlli principali. Per altre informazioni, vedere [Riferimento per i controlli](~/xamarin-forms/user-interface/controls/index.md).
- Il data binding è una tecnica che consente di collegare le proprietà di due oggetti in modo che le modifiche apportate a una proprietà vengano automaticamente riflesse nell'altra proprietà. Per altre informazioni, vedere [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- Xamarin.Forms offre diverse esperienze di navigazione delle pagine, a seconda del tipo di pagina in uso. Per altre informazioni, vedere [Navigazione](~/xamarin-forms/app-fundamentals/navigation/index.md).
- Gli stili consentono di ridurre il markup ripetitivo e di modificare facilmente l'aspetto di un'applicazione. Per altre informazioni, vedere [Applicazione di stili alle app Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md).
- Le estensioni di markup XAML estendono le potenzialità e la flessibilità di XAML consentendo di impostare gli attributi degli elementi da origini diverse dalle stringhe di testo letterali. Per altre informazioni, vedere [Estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md).
- I modelli di dati consentono di definire la presentazione dei dati nelle viste supportate. Per altre informazioni, vedere [Data Templates](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md) (Modelli di dati).
- Il rendering di ogni pagina, layout e vista viene eseguito in modo diverso su ogni piattaforma usando una classe `Renderer` che a sua volta crea un controllo nativo, lo dispone sullo schermo e aggiunge il comportamento specificato nel codice condiviso. Gli sviluppatori possono implementare le proprie classi `Renderer` per personalizzare l'aspetto e/o il comportamento di un controllo. Per altre informazioni, vedere [Custom Renderers](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) (Renderer personalizzati).
- Anche gli effetti consentono la personalizzazione dei controlli nativi in ogni piattaforma. Gli effetti vengono creati in progetti specifici [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) della piattaforma creando una sottoclasse della classe e vengono utilizzati associandoli a un controllo Xamarin.Forms appropriato. Per altre informazioni, vedere [Effects](~/xamarin-forms/app-fundamentals/effects/index.md) (Effetti).
- Il codice condiviso può [`DependencyService`](xref:Xamarin.Forms.DependencyService) accedere alle funzionalità native tramite la classe . Per altre informazioni, vedere [Accessing Native Features with DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) (Accesso alle funzionalità native con DependencyService).

In alternativa, per informazioni aggiuntive su Xamarin.Forms un buon punto di partenza è il libro [_Creating Mobile Apps with Xamarin.Forms_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) (Creazione di app per dispositivi mobili con Xamarin.Forms) di Charles Petzold. Il libro è disponibile in formato PDF o in un'ampia gamma di formati di eBook.

## <a name="related-links"></a>Collegamenti correlati

- [eXtensible Application Markup Language (XAML)](~/xamarin-forms/xaml/index.yml)
- [Data binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Riferimento ai controlli](~/xamarin-forms/user-interface/controls/index.md)
- [Estensioni di markup XAMLXAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Esempi di Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Esempi introduttivi](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms%20get%20started)
- [Informazioni di riferimento sull'API di Xamarin.Forms](xref:Xamarin.Forms)
- [Apprendimento guidato gratuito (video)](https://university.xamarin.com/self-guided/)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
