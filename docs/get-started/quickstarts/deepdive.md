---
title: Approfondimento di Guida introduttiva di xamarin. Forms
description: Questo articolo esamina i concetti fondamentali dello sviluppo di applicazioni con Xamarin.Forms. Gli argomenti trattati includono l'anatomia di un'applicazione Xamarin.Forms, le nozioni di base su architettura e applicazione e l'interfaccia utente.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
ms.openlocfilehash: 67b189254cc08fac0323b7df5fcbab5abd994c05
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58855016"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Approfondimento di Guida introduttiva di xamarin. Forms

Nel [Guida introduttiva di xamarin. Forms](~/get-started/index.yml), è stata compilata l'applicazione di Lotus Notes. Questo articolo esamina ciò che è stato compilato per comprendere meglio le nozioni di base del funzionamento delle applicazioni Xamarin.Forms.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introduzione a Visual Studio

Visual Studio consente di organizzare il codice in *soluzioni* e *progetti*. Una soluzione è un contenitore per uno o più progetti. Un progetto può essere un'applicazione, una libreria di supporto, un'applicazione di test e altro ancora. L'applicazione di Lotus Notes è costituita da una soluzione contenente quattro progetti, come illustrato nello screenshot seguente:

![](deepdive-images/vs/solution.png "Esplora soluzioni di Visual Studio")

I progetti sono:

- Note: questo progetto è il progetto di libreria .NET Standard che contiene tutto il codice condiviso e l'interfaccia utente condivisa.
- Notes.Android: questo progetto contiene codice specifico di Android ed è il punto di ingresso per l'applicazione Android.
- Notes.iOS: questo progetto contiene codice specifico di iOS ed è il punto di ingresso per l'applicazione iOS.
- Notes.UWP: questo progetto contiene codice specifico di Universal Windows Platform (UWP) e il punto di ingresso per l'applicazione UWP.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia di un'applicazione xamarin. Forms

Lo screenshot seguente illustra il contenuto del progetto di libreria Standard di Lotus Notes .NET in Visual Studio:

![](deepdive-images/vs/net-standard-project.png "Contenuti del progetto .NET Standard Phoneword")

Il progetto ha un nodo **Dipendenze** contenente i nodi **NuGet** e **SDK**:

- **NuGet** &ndash; di xamarin. Forms e i pacchetti NuGet di sqlite-net-libreria di classi portabile che sono stati aggiunti al progetto.
- **SDK** &ndash; il `NETStandard.Library` metapacchetto fa riferimento al set completo di pacchetti NuGet che definiscono .NET Standard.

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introduzione a Visual Studio per Mac

[Visual Studio per Mac](/visualstudio/mac/) segue la prassi di Visual Studio di organizzare il codice in *soluzioni* e *progetti*. Una soluzione è un contenitore per uno o più progetti. Un progetto può essere un'applicazione, una libreria di supporto, un'applicazione di test e altro ancora. L'applicazione di Lotus Notes è costituita da una soluzione contenente tre progetti, come illustrato nello screenshot seguente:

![](deepdive-images/vsmac/solution.png "Riquadro di Visual Studio per Mac")

I progetti sono:

- Note: questo progetto è il progetto di libreria .NET Standard che contiene tutto il codice condiviso e l'interfaccia utente condivisa.
- Notes.Android: questo progetto contiene codice specifico di Android ed è il punto di ingresso per le applicazioni Android.
- Notes.iOS: questo progetto contiene codice di specifico di iOS ed è il punto di ingresso per le applicazioni iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia di un'applicazione xamarin. Forms

Lo screenshot seguente illustra il contenuto del progetto di libreria Standard di Lotus Notes .NET in Visual Studio per Mac:

![](deepdive-images/vsmac/net-standard-project.png "Contenuti del progetto Libreria .NET Standard Phoneword")

Il progetto ha un nodo **Dipendenze** contenente i nodi **NuGet** e **SDK**:

- **NuGet** &ndash; di xamarin. Forms e i pacchetti NuGet di sqlite-net-libreria di classi portabile che sono stati aggiunti al progetto.
- **SDK** &ndash; il `NETStandard.Library` metapacchetto fa riferimento al set completo di pacchetti NuGet che definiscono .NET Standard.

::: zone-end

Il progetto è costituito anche da alcuni file:

- **Data\NoteDatabase.cs** : questa classe contiene il codice per creare il database, leggerli da quest'ultimo, scrivere i dati ad esso ed eliminare dati da esso.
- **Models\Note.cs** : questa classe definisce un `Note` modello le cui istanze memorizzare i dati relativi a ogni nota nell'applicazione.
- **App.xaml**: markup XAML per la classe `App`, che definisce un dizionario risorse per l'applicazione.
- **App.Xaml.cs**: code-behind per la classe `App`, che è responsabile della creazione di istanze della prima pagina visualizzata dall'applicazione in ogni piattaforma e della gestione degli eventi del ciclo di vita dell'applicazione.
- **AssemblyInfo.cs** : questo file contiene un attributo dell'applicazione sul progetto, che viene applicato a livello di assembly.
- **NotesPage.xaml** : markup XAML il per il `NotesPage` classe che definisce l'interfaccia utente per la pagina visualizzata all'avvio dell'applicazione.
- **NotesPage.xaml.cs** – il code-behind per il `NotesPage` (classe), che contiene la logica di business che viene eseguita quando l'utente interagisce con la pagina.
- **NoteEntryPage.xaml** : markup XAML il per il `NoteEntryPage` classe che definisce l'interfaccia utente per la pagina visualizzata quando l'utente immette una nota.
- **NoteEntryPage.xaml.cs** – il code-behind per il `NoteEntryPage` (classe), che contiene la logica di business che viene eseguita quando l'utente interagisce con la pagina.

Per altre informazioni sull'anatomia di un'applicazione Xamarin.iOS, vedere l'[analisi dettagliata di un'applicazione Xamarin.iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application). Per altre informazioni sull'anatomia di un'applicazione Xamarin.Android, vedere l'[analisi dettagliata di un'applicazione Xamarin.Android](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy).

## <a name="architecture-and-application-fundamentals"></a>Nozioni fondamentali su architettura e l'applicazione

Un'applicazione Xamarin.Forms ha la stessa struttura di un'applicazione multipiattaforma tradizionale. Il codice condiviso in genere viene inserito in una libreria .NET Standard e le applicazioni specifiche della piattaforma usano il codice condiviso. Il diagramma seguente mostra una panoramica di questa relazione per l'applicazione note:

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "Architettura di Lotus Notes")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "Architettura di Lotus Notes")

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

Questo codice imposta la `MainPage` proprietà del `App` classe a un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) istanza il cui contenuto è un `NotesPage` istanza.

Inoltre, il **AssemblyInfo.cs** file contiene un attributo di sola applicazione, che viene applicato a livello di assembly:

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

Il [ `XamlCompilation` ](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attributo attiva il compilatore XAML, in modo che XAML venga compilato direttamente in linguaggio intermedio. Per altre informazioni, vedere [XAML Compilation](~/xamarin-forms/xaml/xamlc.md) (Compilazione XAML).

## <a name="launching-the-application-on-each-platform"></a>L'avvio dell'applicazione in ogni piattaforma

### <a name="ios"></a>iOS

Per avviare la pagina iniziale di xamarin. Forms in iOS, il progetto Notes.iOS definisce il `AppDelegate` classe che eredita dal `FormsApplicationDelegate` classe:

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

Per avviare la pagina iniziale di xamarin. Forms in Android, il progetto Notes.Android include codice che crea un' `Activity` con il `MainLauncher` attributo, con l'attività che eredita dal `FormsAppCompatActivity` classe:

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

Di conseguenza viene caricata nell'applicazione l'implementazione di Xamarin.Forms specifica per la piattaforma UWP. La pagina iniziale di xamarin. Forms viene avviata per il `MainPage` classe:

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
> Le app della piattaforma Windows universale possono essere compilata con xamarin. Forms, ma solo usando Visual Studio in Windows.

::: zone-end

## <a name="user-interface"></a>Interfaccia utente

Sono disponibili quattro gruppi di controllo principale usati per creare l'interfaccia utente di un'applicazione xamarin. Forms:

1. **Pagine**: le pagine di Xamarin.Forms rappresentano le schermate dell'applicazione multipiattaforma per dispositivi mobili. L'applicazione note Usa il [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) classe per visualizzare le schermate single. Per altre informazioni sulle pagine, vedere [Xamarin.Forms Pages](~/xamarin-forms/user-interface/controls/pages.md) (Pagine di Xamarin.Forms).
1. **Visualizzazioni**: le visualizzazioni di Xamarin.Forms sono i controlli visualizzati nell'interfaccia utente, ad esempio le etichette, i pulsanti e le caselle di immissione testo. L'applicazione finita note Usa la [ `ListView` ](xref:Xamarin.Forms.ListView), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Button` ](xref:Xamarin.Forms.Button) viste. Per altre informazioni sulle visualizzazioni, vedere [Xamarin.Forms Views](~/xamarin-forms/user-interface/controls/views.md) (Visualizzazioni di Xamarin.Forms).
1. **Layout**: i layout di Xamarin.Forms sono contenitori usati per convertire le visualizzazioni in strutture logiche. L'applicazione note Usa la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) classe per disporre le visualizzazioni in uno stack verticale e il [ `Grid` ](xref:Xamarin.Forms.Grid) classe per disporre i pulsanti orizzontalmente. Per altre informazioni sui layout, vedere [Xamarin.Forms Layouts](~/xamarin-forms/user-interface/controls/layouts.md) (Layout di Xamarin.Forms).
1. **Celle**: le celle di Xamarin.Forms sono elementi speciali usati per le voci di un elenco e descrivono la rappresentazione di ogni elemento di un elenco. L'applicazione note Usa la [ `TextCell` ](xref:Xamarin.Forms.TextCell) per visualizzare due elementi per ogni riga nell'elenco. Per altre informazioni sulle celle, vedere [Xamarin.Forms Cells](~/xamarin-forms/user-interface/controls/cells.md) (Celle di Xamarin.Forms).

In fase di runtime ogni controllo viene mappato al controllo nativo equivalente, che viene restituito nel rendering.

### <a name="layout"></a>Layout

L'applicazione note Usa la [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) per semplificare lo sviluppo di applicazioni multipiattaforma disponendo automaticamente le visualizzazioni nella schermata indipendentemente dalle dimensioni dello schermo. Gli elementi figlio vengono posizionati uno dopo l'altro, orizzontalmente o verticalmente, nell'ordine in cui sono stati aggiunti. La quantità di spazio usata dall'elemento `StackLayout` dipende dall'impostazione delle proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions), ma per impostazione predefinita `StackLayout` prova a usare l'intero schermo.

Il codice XAML seguente viene illustrato un esempio dell'uso di un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) al layout di `NoteEntryPage`:

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

Per impostazione predefinita il [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) presuppone un orientamento verticale. Tuttavia, può essere modificata in un orientamento orizzontale impostando il [ `StackLayout.Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation) proprietà per il [ `StackOrientation.Horizontal` ](xref:Xamarin.Forms.StackOrientation.Horizontal) membro di enumerazione.

> [!NOTE]
> Le dimensioni delle visualizzazioni possono essere impostata tramite il `HeightRequest` e `WidthRequest` proprietà.

Per altre informazioni sulla classe [`StackLayout`](xref:Xamarin.Forms.StackLayout), vedere [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

### <a name="responding-to-user-interaction"></a>Risposta all'interazione dell'utente

Un oggetto definito in XAML può generare un evento che viene gestito nel file code-behind. Nell'esempio di codice riportato di seguito viene illustrato il `OnSaveButtonClicked` metodo nel code-behind per il `NoteEntryPage` (classe), che viene eseguito in risposta al [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) generazione dell'evento sul *Salva* pulsante .

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    await App.Database.SaveNoteAsync(note);
    await Navigation.PopAsync();
}
```

Il `OnSaveButtonClicked` metodo salva la nota nel database e torna alla pagina precedente.

> [!NOTE]
> Il file code-behind per una classe XAML è in grado di accedere a un oggetto definito in XAML usando il nome assegnato con l'attributo `x:Name`. Il valore assegnato a questo attributo ha le stesse regole delle variabili di C#, poiché deve iniziare con una lettera o un carattere di sottolineatura e non contenere spazi incorporati.

Il cablaggio del salvataggio sul pulsante per la `OnSaveButtonClicked` metodo si verifica nel markup XAML per il `NoteEntryPage` classe:

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>Elenchi

Il [ `ListView` ](xref:Xamarin.Forms.ListView) è responsabile della visualizzazione verticalmente di una raccolta di elementi in un elenco. Ogni elemento di `ListView` sarà contenuto in una singola cella.

Nell'esempio di codice riportato di seguito viene illustrato il [ `ListView` ](xref:Xamarin.Forms.ListView) dal `NotesPage`:

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

Il layout di ogni riga nel [ `ListView` ](xref:Xamarin.Forms.ListView) è definito all'interno di [ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) elemento e Usa i dati di associazione per visualizzare eventuali note che vengono recuperati dall'applicazione. Il [ `ListView.ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource) viene impostata per l'origine dati, in `NotesPage.xaml.cs`:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Questo codice popola la [ `ListView` ](xref:Xamarin.Forms.ListView) con eventuali note archiviati nel database.

Quando è selezionata una riga nel [ `ListView` ](xref:Xamarin.Forms.ListView), il [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) viene generato l'evento. Un gestore eventi, denominato `OnListViewItemSelected`, viene eseguito quando viene generato l'evento:

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

Il [ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected) eventi possono accedere all'oggetto che era associato alla cella tramite la [ `e.SelectedItem` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) proprietà.

Per altre informazioni sul [ `ListView` ](xref:Xamarin.Forms.ListView) classe, vedere [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="navigation"></a>Navigazione

Xamarin.Forms offre diverse esperienze di navigazione tra le pagine, a seconda del tipo [`Page`](xref:Xamarin.Forms.Page) in uso. Per la [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) navigazione istanze può essere gerarchici, o modale. Per informazioni sulla navigazione modale, vedere [xamarin. Forms pagine modali](~/xamarin-forms/app-fundamentals/navigation/modal.md).

> [!NOTE]
> Le classi [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) e [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) offrono esperienze di navigazione alternative. Per altre informazioni, vedere [Navigazione](~/xamarin-forms/app-fundamentals/navigation/index.md).

Nella navigazione gerarchica la [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) classe viene utilizzata per spostarsi all'interno di uno stack [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) oggetti, Avanti e indietro in base alle esigenze. La classe implementa la navigazione come stack LIFO (Last-In, First-Out) di oggetti [`Page`](xref:Xamarin.Forms.Page). Per passare da una pagina all'altra, un'applicazione esegue il push di una nuova pagina nello stack di navigazione, in cui diventa la pagina attiva. Per tornare alla pagina precedente, l'applicazione preleva la pagina corrente dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva.

La classe `NavigationPage` aggiungerà inoltre una barra di navigazione nella parte superiore della pagina che consente di visualizzare un titolo e un pulsante **Indietro** appropriato per la piattaforma per tornare alla pagina precedente.

La prima pagina aggiunta a uno stack di navigazione è detto il *radice* pagina dell'applicazione e il codice seguente viene illustrato come questa operazione viene eseguita nell'applicazione note:

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

Tutte le istanze di [`ContentPage`](xref:Xamarin.Forms.ContentPage) hanno una proprietà [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) che espone i metodi per modificare lo stack della pagina. Questi metodi dovrebbero essere richiamati solo se l'applicazione include una [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Per passare alla `NoteEntryPage`, è necessario richiamare il metodo [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)) come illustrato nell'esempio di codice seguente:

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

In questo modo, il nuovo `NoteEntryPage` oggetto da inserire nello stack di navigazione, in cui diventa la pagina attiva.

La pagina attiva può essere prelevata dallo stack di navigazione premendo il pulsante *Indietro* (fisico o su schermo) del dispositivo. Per tornare a livello di codice alla pagina originale, l'oggetto `NoteEntryPage` deve chiamare il metodo [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), come illustrato nell'esempio di codice seguente:

```csharp
await Navigation.PopAsync();
```

Per altre informazioni sulla navigazione gerarchica, vedere [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="data-binding"></a>Associazione dati

Il data binding viene usato per semplificare il modo in cui un'applicazione Xamarin.Forms visualizza i propri dati e interagisce con essi. Stabilisce una connessione tra l'interfaccia utente e l'applicazione sottostante. La classe [`BindableObject`](xref:Xamarin.Forms.BindableObject) contiene la maggior parte dell'infrastruttura per supportare il data binding.

Il data binding (o associazione di dati) consente di connettere due oggetti, detti oggetti di *origine* e di *destinazione*. L'oggetto di *origine* visualizza i dati. L'oggetto di *destinazione* usa (e spesso visualizza) i dati dall'oggetto di origine. Ad esempio, un' [ `Editor` ](xref:Xamarin.Forms.Editor) (*destinazione* oggetto) associa comunemente la [ `Text` ](xref:Xamarin.Forms.Editor.Text) proprietà a un pubblico `string` proprietà in un *origine* oggetto. Il diagramma che segue illustra la relazione di associazione:

![](deepdive-images/data-binding.png "Data Binding")

Il vantaggio principale del data binding sta nel fatto che non è più necessario preoccuparsi della sincronizzazione tra le visualizzazioni e l'origine dati. Le modifiche apportate all'oggetto di *origine* vengono trasmesse automaticamente in background all'oggetto di *destinazione* dal framework di associazione. Facoltativamente, le modifiche all'oggetto di destinazione possono anche essere respinte all'oggetto di *origine*.

La definizione del data binding è un processo in due passaggi:

- La proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) dell'oggetto di *destinazione* deve essere impostata sull'*origine*.
- È necessario stabilire un'associazione tra la *destinazione* e l'*origine*. In XAML questo si ottiene usando l'estensione di markup [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension).

Nell'applicazione, note sulla destinazione del binding è il [ `Editor` ](xref:Xamarin.Forms.Editor) che verrà visualizzato un messaggio, mentre il `Note` istanza impostata come la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) di `NoteEntryPage` è l'associazione origine.

Il `BindingContext` del `NoteEntryPage` viene impostato durante la navigazione tra le pagine, come illustrato nell'esempio di codice seguente:

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

Nel `OnNoteAddedClicked` metodo, che viene eseguita quando viene aggiunta una nuova nota all'applicazione, il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) dei `NoteEntryPage` è impostata su un nuovo `Note` istanza. Nel `OnListViewItemSelected` metodo, che viene eseguita quando viene selezionata una nota esistente nel [ `ListView` ](xref:Xamarin.Forms.ListView), il `BindingContext` del `NoteEntryPage` è impostata su selezionato `Note` istanza, che è accessibile tramite il [ `e.SelectedItem` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) proprietà.

> [!IMPORTANT]
> È possibile impostare individualmente la proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) di ogni oggetto di *destinazione*, ma questa operazione non è necessaria. `BindingContext` è una proprietà speciale che viene ereditata da tutti gli elementi figlio. Pertanto, quando il `BindingContext` nella [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) è impostata su un `Note` istanza, tutti i figli del `ContentPage` hanno lo stesso `BindingContext`e consente l'associazione a proprietà pubbliche del `Note`oggetto.

Il [ `Editor` ](xref:Xamarin.Forms.Editor) in `NoteEntryPage` associa quindi il `Text` proprietà del `Note` oggetto:

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

Viene definita un'associazione tra la proprietà [`Editor.Text`](xref:Xamarin.Forms.Editor.Text) e la proprietà `Text` dell'oggetto di *origine*. Le modifiche apportate nel `Editor` verrà propagato automaticamente al `Note` oggetto. Allo stesso modo, se vengono apportate modifiche per il `Note.Text` proprietà, il motore di binding xamarin. Forms verrà aggiornato anche il contenuto del `Editor`. Questa funzionalità è detta *associazione bidirezionale*.

Per altre informazioni sul data binding, vedere [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="styling"></a>Stile

Le applicazioni xamarin. Forms spesso contengono più elementi visivi che hanno un aspetto identico. Impostare l'aspetto di ogni elemento visivo può essere ricorrenti e tendente all'errore. Al contrario, gli stili possono essere creati che definiscono l'aspetto e quindi applicati per gli elementi visivi necessari.

Il [ `Style` ](xref:Xamarin.Forms.Style) classe Raggruppa un insieme di valori di proprietà in un oggetto che può quindi essere applicato a più istanze di oggetto visivo. Gli stili vengono archiviati in un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), a livello di applicazione, il livello di pagina o il livello di visualizzazione. Scegliere dove definire un `Style` impatti dove può essere utilizzato:

- [`Style`](xref:Xamarin.Forms.Style) le istanze definite a livello di applicazione possono essere applicate in tutta l'applicazione.
- [`Style`](xref:Xamarin.Forms.Style) le istanze definite a livello di pagina possono essere applicate alla pagina e ai relativi figli.
- [`Style`](xref:Xamarin.Forms.Style) le istanze definite a livello di visualizzazione possono essere applicate alla vista e ai relativi figli.

> [!IMPORTANT]
> Tutti gli stili utilizzati in tutta l'applicazione vengono archiviati nel dizionario risorse dell'applicazione per evitare la duplicazione. Tuttavia, XAML specifico di una pagina deve essere incluso nel dizionario risorse dell'applicazione, come le risorse vengano analizzate all'avvio dell'applicazione anziché quando richiesto da una pagina.

Ciascuna [ `Style` ](xref:Xamarin.Forms.Style) istanza contiene una raccolta di uno o più [ `Setter` ](xref:Xamarin.Forms.Setter) oggetti, con molti `Setter` aventi un [ `Property` ](xref:Xamarin.Forms.Setter.Property) e un [`Value`](xref:Xamarin.Forms.Setter.Value). Il `Property` è il nome della proprietà associabile dell'elemento, viene applicato lo stile e il `Value` è il valore che viene applicato alla proprietà. Esempio di codice seguente mostra uno stile da `NoteEntryPage`:

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

Questo stile viene applicato a qualsiasi [ `Editor` ](xref:Xamarin.Forms.Editor) istanze nella pagina.

Quando si crea una [ `Style` ](xref:Xamarin.Forms.Style), il [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) proprietà è sempre obbligatorio.

> [!NOTE]
> Applicazione di stili a un'applicazione xamarin. Forms viene in genere eseguita utilizzando stili XAML. Tuttavia, xamarin. Forms supporta anche gli elementi visivi di stile usando Cascading Style Sheets (CSS). Per altre informazioni, vedere [app di applicazione di stili a xamarin. Forms con Cascading Style Sheets (CSS)](~/xamarin-forms/user-interface/styles/css/index.md).

Per altre informazioni sugli stili XAML, vedere [lo stile delle App xamarin. Forms usando gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

### <a name="providing-platform-specific-styles"></a>Fornire stili specifici della piattaforma

Il `OnPlatform` estensioni di markup consentono di personalizzare l'aspetto dell'interfaccia utente in base a ogni piattaforma:

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

Ciò [ `Style` ](xref:Xamarin.Forms.Style) imposta diversi [ `Color` ](xref:Xamarin.Forms.Color) valori per il [ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) e [ `BarTextColor` ](xref:Xamarin.Forms.NavigationPage.BarTextColor) proprietà del [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), a seconda della piattaforma in uso.

Per altre informazioni sulle estensioni di markup XAML, vedere [XAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md) (Estensioni di markup XAML). Per informazioni sul `OnPlatform` estensione di markup, vedere [estensione di Markup OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="testing-and-deployment"></a>Test e la distribuzione

Visual Studio per Mac e Visual Studio offrono entrambi numerose opzioni per il test e la distribuzione di un'applicazione. Il debug delle applicazioni fa parte del ciclo di vita dello sviluppo delle applicazioni e consente di diagnosticare i problemi del codice. Per altre informazioni, vedere gli articoli relativi all'[impostazione di un punto di interruzione](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), al [passaggio attraverso il codice](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) e all'[output di informazioni alla finestra del log](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

I simulatori sono un ottimo strumento per iniziare a distribuire e testare e offrono funzionalità utili per testare le applicazioni. Tuttavia, gli utenti non useranno l'applicazione finale in un simulatore, quindi le applicazioni devono essere testate nei dispositivi reali presto e spesso. Per altre informazioni sul provisioning dei dispositivi iOS, vedere [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md). Per altre informazioni sul provisioning dei dispositivi Android, vedere [Set Up Device for Development](~/android/get-started/installation/set-up-device-for-development.md) (Configurare il dispositivo per lo sviluppo).

## <a name="next-steps"></a>Passaggi successivi

In questo approfondimento è stati esaminati i concetti fondamentali dello sviluppo di applicazioni mediante xamarin. Forms. I passaggi suggeriti che seguono includono informazioni sulle funzionalità seguenti:

- Per creare l'interfaccia utente di un'applicazione in Xamarin.Forms è possibile usare quattro gruppi di controlli principali. Per altre informazioni, vedere [Riferimento per i controlli](~/xamarin-forms/user-interface/controls/index.md).
- Il data binding è una tecnica che consente di collegare le proprietà di due oggetti in modo che le modifiche apportate a una proprietà vengano automaticamente riflesse nell'altra proprietà. Per altre informazioni, vedere [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- Xamarin.Forms offre diverse esperienze di navigazione delle pagine, a seconda del tipo di pagina in uso. Per altre informazioni, vedere [Navigazione](~/xamarin-forms/app-fundamentals/navigation/index.md).
- Gli stili consentono di ridurre il markup ripetitivo e di modificare facilmente l'aspetto di un'applicazione. Per altre informazioni, vedere [Applicazione di stili alle app Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md).
- Le estensioni di markup XAML estendono le potenzialità e la flessibilità di XAML consentendo di impostare gli attributi degli elementi da origini diverse dalle stringhe di testo letterali. Per altre informazioni, vedere [Estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md).
- I modelli di dati consentono di definire la presentazione dei dati nelle viste supportate. Per altre informazioni, vedere [Data Templates](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md) (Modelli di dati).
- Il rendering di ogni pagina, layout e vista viene eseguito in modo diverso su ogni piattaforma usando una classe `Renderer` che a sua volta crea un controllo nativo, lo dispone sullo schermo e aggiunge il comportamento specificato nel codice condiviso. Gli sviluppatori possono implementare le proprie classi `Renderer` per personalizzare l'aspetto e/o il comportamento di un controllo. Per altre informazioni, vedere [Custom Renderers](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) (Renderer personalizzati).
- Anche gli effetti consentono la personalizzazione dei controlli nativi in ogni piattaforma. Gli effetti vengono creati in processi specifici per la piattaforma creando sottoclassi della classe [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) e vengono utilizzati associandoli a un controllo Xamarin.Forms appropriato. Per altre informazioni, vedere [Effects](~/xamarin-forms/app-fundamentals/effects/index.md) (Effetti).
- Il codice condiviso può accedere alle funzionalità native tramite la classe [`DependencyService`](xref:Xamarin.Forms.DependencyService). Per altre informazioni, vedere [Accessing Native Features with DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) (Accesso alle funzionalità native con DependencyService).

In alternativa, per informazioni aggiuntive su Xamarin.Forms un buon punto di partenza è il libro [_Creating Mobile Apps with Xamarin.Forms_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) (Creazione di app per dispositivi mobili con Xamarin.Forms) di Charles Petzold. Il libro è disponibile in formato PDF o in un'ampia gamma di formati di eBook.

## <a name="related-links"></a>Collegamenti correlati

- [eXtensible Application Markup Language (XAML)](~/xamarin-forms/xaml/index.md)
- [Data binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Riferimento per i controlli](~/xamarin-forms/user-interface/controls/index.md)
- [Estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Esempi introduttivi](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/)
- [Informazioni di riferimento sull'API di Xamarin.Forms](xref:Xamarin.Forms)
- [Apprendimento guidato gratuito (video)](https://university.xamarin.com/self-guided/)
