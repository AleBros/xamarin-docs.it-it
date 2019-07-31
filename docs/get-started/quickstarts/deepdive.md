---
title: Approfondimento sulla Guida introduttiva a Novell. Forms
description: Questo articolo esamina i concetti fondamentali dello sviluppo di applicazioni con Xamarin.Forms. Gli argomenti trattati includono l'anatomia di un'applicazione Xamarin.Forms, le nozioni di base su architettura e applicazione e l'interfaccia utente.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
ms.openlocfilehash: c0e772e0207d2ccc9a94aefd9655dc1bb6776bba
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653476"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Approfondimento sulla Guida introduttiva a Novell. Forms

Nella [Guida introduttiva a Novell. Forms](~/get-started/index.yml)è stata compilata l'applicazione Note. Questo articolo esamina ciò che è stato compilato per comprendere meglio le nozioni di base del funzionamento delle applicazioni Xamarin.Forms.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introduzione a Visual Studio

Visual Studio consente di organizzare il codice in *soluzioni* e *progetti*. Una soluzione è un contenitore per uno o più progetti. Un progetto può essere un'applicazione, una libreria di supporto, un'applicazione di test e altro ancora. L'applicazione Notes è costituita da una soluzione contenente quattro progetti, come illustrato nello screenshot seguente:

![](deepdive-images/vs/solution.png "Esplora soluzioni di Visual Studio")

I progetti sono:

- Note: questo progetto è il progetto di libreria .NET Standard che include tutto il codice condiviso e l'interfaccia utente condivisa.
- Note. Android: questo progetto include codice specifico per Android ed è il punto di ingresso per l'applicazione Android.
- Note. iOS: questo progetto include codice specifico per iOS ed è il punto di ingresso per l'applicazione iOS.
- Notes. UWP: questo progetto include piattaforma UWP (Universal Windows Platform) (UWP) codice specifico e è il punto di ingresso per l'applicazione UWP.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia di un'applicazione Novell. Forms

Lo screenshot seguente illustra il contenuto del progetto di libreria .NET Standard di note in Visual Studio:

![](deepdive-images/vs/net-standard-project.png "Contenuti del progetto .NET Standard Phoneword")

Il progetto ha un nodo **Dipendenze** contenente i nodi **NuGet** e **SDK**:

- **NuGet** &ndash; i pacchetti NuGet Novell. Forms e SQLite-NET-PCL che sono stati aggiunti al progetto.
- **SDK** &ndash; il `NETStandard.Library` metapacchetto fa riferimento al set completo di pacchetti NuGet che definiscono .NET Standard.

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introduzione a Visual Studio per Mac

[Visual Studio per Mac](/visualstudio/mac/) segue la prassi di Visual Studio di organizzare il codice in *soluzioni* e *progetti*. Una soluzione è un contenitore per uno o più progetti. Un progetto può essere un'applicazione, una libreria di supporto, un'applicazione di test e altro ancora. L'applicazione Notes è costituita da una soluzione contenente tre progetti, come illustrato nello screenshot seguente:

![](deepdive-images/vsmac/solution.png "Riquadro di Visual Studio per Mac")

I progetti sono:

- Note: questo progetto è il progetto di libreria .NET Standard che include tutto il codice condiviso e l'interfaccia utente condivisa.
- Note. Android: questo progetto include codice specifico per Android ed è il punto di ingresso per le applicazioni Android.
- Note. iOS: questo progetto include il codice specifico di iOS ed è il punto di ingresso per le applicazioni iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia di un'applicazione Novell. Forms

Lo screenshot seguente illustra il contenuto del progetto di libreria .NET Standard di note in Visual Studio per Mac:

![](deepdive-images/vsmac/net-standard-project.png "Contenuti del progetto Libreria .NET Standard Phoneword")

Il progetto ha un nodo **Dipendenze** contenente i nodi **NuGet** e **SDK**:

- **NuGet** &ndash; i pacchetti NuGet Novell. Forms e SQLite-NET-PCL che sono stati aggiunti al progetto.
- **SDK** &ndash; il `NETStandard.Library` metapacchetto fa riferimento al set completo di pacchetti NuGet che definiscono .NET Standard.

::: zone-end

Il progetto è costituito anche da alcuni file:

- **Data\NoteDatabase.cs** : questa classe contiene codice per la creazione del database, la lettura dei dati, la scrittura dei dati e l'eliminazione dei dati.
- **Models\Note.cs** : questa classe definisce un `Note` modello le cui istanze archiviano i dati su ciascuna nota nell'applicazione.
- **App.xaml**: markup XAML per la classe `App`, che definisce un dizionario risorse per l'applicazione.
- **App.Xaml.cs**: code-behind per la classe `App`, che è responsabile della creazione di istanze della prima pagina visualizzata dall'applicazione in ogni piattaforma e della gestione degli eventi del ciclo di vita dell'applicazione.
- **AssemblyInfo.cs** : questo file contiene un attributo dell'applicazione sul progetto, applicato a livello di assembly.
- **NotesPage. XAML** : markup XAML per la `NotesPage` classe, che definisce l'interfaccia utente per la pagina visualizzata quando l'applicazione viene avviata.
- **NotesPage.XAML.cs** : code-behind per la `NotesPage` classe, che contiene la logica di business che viene eseguita quando l'utente interagisce con la pagina.
- **NoteEntryPage. XAML** : markup XAML per la `NoteEntryPage` classe, che definisce l'interfaccia utente per la pagina visualizzata quando l'utente immette una nota.
- **NoteEntryPage.XAML.cs** : code-behind per la `NoteEntryPage` classe, che contiene la logica di business che viene eseguita quando l'utente interagisce con la pagina.

Per altre informazioni sull'anatomia di un'applicazione Xamarin.iOS, vedere l'[analisi dettagliata di un'applicazione Xamarin.iOS](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application). Per altre informazioni sull'anatomia di un'applicazione Xamarin.Android, vedere l'[analisi dettagliata di un'applicazione Xamarin.Android](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy).

## <a name="architecture-and-application-fundamentals"></a>Nozioni fondamentali sull'architettura e sulle applicazioni

Un'applicazione Xamarin.Forms ha la stessa struttura di un'applicazione multipiattaforma tradizionale. Il codice condiviso in genere viene inserito in una libreria .NET Standard e le applicazioni specifiche della piattaforma usano il codice condiviso. Il diagramma seguente mostra una panoramica di questa relazione per l'applicazione Note:

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "Architettura note")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "Architettura note")

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

Questo codice imposta la `MainPage` proprietà `App` della classe su un' [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) istanza il cui contenuto è un' `NotesPage` istanza di.

Inoltre, il file **AssemblyInfo.cs** contiene un solo attributo applicazione, che viene applicato a livello di assembly:

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

L' [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attributo attiva il compilatore XAML, in modo che XAML venga compilato direttamente in linguaggio intermedio. Per altre informazioni, vedere [XAML Compilation](~/xamarin-forms/xaml/xamlc.md) (Compilazione XAML).

## <a name="launching-the-application-on-each-platform"></a>Avvio dell'applicazione in ogni piattaforma

### <a name="ios"></a>iOS

Per avviare la pagina iniziale di Novell. Forms in iOS, il progetto Notes. iOS `AppDelegate` definisce la classe che eredita `FormsApplicationDelegate` dalla classe:

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

Per avviare la pagina iniziale di Novell. Forms in Android, il progetto Notes. Android include il codice `Activity` che crea `MainLauncher` un oggetto con l'attributo, con l' `FormsAppCompatActivity` attività che eredita dalla classe:

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

Di conseguenza viene caricata nell'applicazione l'implementazione di Xamarin.Forms specifica per la piattaforma UWP. La pagina iniziale di Novell. Forms viene avviata dalla `MainPage` classe:

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
> Piattaforma UWP (Universal Windows Platform) app possono essere compilate con Novell. Forms, ma solo con Visual Studio in Windows.

::: zone-end

## <a name="user-interface"></a>Interfaccia utente

Per creare l'interfaccia utente di un'applicazione Novell. Forms, sono disponibili quattro gruppi di controllo principali:

1. **Pagine**: le pagine di Xamarin.Forms rappresentano le schermate dell'applicazione multipiattaforma per dispositivi mobili. L'applicazione Notes usa la [`ContentPage`](xref:Xamarin.Forms.ContentPage) classe per visualizzare le singole schermate. Per altre informazioni sulle pagine, vedere [Xamarin.Forms Pages](~/xamarin-forms/user-interface/controls/pages.md) (Pagine di Xamarin.Forms).
1. **Visualizzazioni**: le visualizzazioni di Xamarin.Forms sono i controlli visualizzati nell'interfaccia utente, ad esempio le etichette, i pulsanti e le caselle di immissione testo. Nell'applicazione Note finite vengono utilizzate [`ListView`](xref:Xamarin.Forms.ListView)le [`Editor`](xref:Xamarin.Forms.Editor)viste, [`Button`](xref:Xamarin.Forms.Button) e. Per altre informazioni sulle visualizzazioni, vedere [Xamarin.Forms Views](~/xamarin-forms/user-interface/controls/views.md) (Visualizzazioni di Xamarin.Forms).
1. **Layout**: i layout di Xamarin.Forms sono contenitori usati per convertire le visualizzazioni in strutture logiche. L'applicazione Notes usa la [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe per disporre le visualizzazioni in uno stack verticale e la [`Grid`](xref:Xamarin.Forms.Grid) classe per disporre i pulsanti orizzontalmente. Per altre informazioni sui layout, vedere [Xamarin.Forms Layouts](~/xamarin-forms/user-interface/controls/layouts.md) (Layout di Xamarin.Forms).
1. **Celle**: le celle di Xamarin.Forms sono elementi speciali usati per le voci di un elenco e descrivono la rappresentazione di ogni elemento di un elenco. L'applicazione Notes USA [`TextCell`](xref:Xamarin.Forms.TextCell) per visualizzare due elementi per ogni riga nell'elenco. Per altre informazioni sulle celle, vedere [Xamarin.Forms Cells](~/xamarin-forms/user-interface/controls/cells.md) (Celle di Xamarin.Forms).

In fase di runtime ogni controllo viene mappato al controllo nativo equivalente, che viene restituito nel rendering.

### <a name="layout"></a>Layout

L'applicazione Notes USA [`StackLayout`](xref:Xamarin.Forms.StackLayout) per semplificare lo sviluppo di applicazioni multipiattaforma, organizzando automaticamente le visualizzazioni sullo schermo indipendentemente dalle dimensioni dello schermo. Gli elementi figlio vengono posizionati uno dopo l'altro, orizzontalmente o verticalmente, nell'ordine in cui sono stati aggiunti. La quantità di spazio usata dall'elemento `StackLayout` dipende dall'impostazione delle proprietà [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions), ma per impostazione predefinita `StackLayout` prova a usare l'intero schermo.

Il codice XAML seguente mostra un esempio di utilizzo di [`StackLayout`](xref:Xamarin.Forms.StackLayout) un oggetto per `NoteEntryPage`il layout dell'oggetto:

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

Per impostazione predefinita [`StackLayout`](xref:Xamarin.Forms.StackLayout) , presuppone un orientamento verticale. Tuttavia, può essere modificato in un orientamento orizzontale impostando la [`StackLayout.Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) proprietà sul membro dell' [`StackOrientation.Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal) enumerazione.

> [!NOTE]
> Le dimensioni delle visualizzazioni possono essere impostate tramite le `HeightRequest` proprietà `WidthRequest` e.

Per altre informazioni sulla classe [`StackLayout`](xref:Xamarin.Forms.StackLayout), vedere [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md).

### <a name="responding-to-user-interaction"></a>Risposta all'interazione dell'utente

Un oggetto definito in XAML può generare un evento che viene gestito nel file code-behind. Nell'esempio di codice seguente viene `OnSaveButtonClicked` illustrato il metodo nel code-behind per `NoteEntryPage` la classe, che viene eseguito in risposta alla [`Clicked`](xref:Xamarin.Forms.Button.Clicked) generazione dell'evento sul pulsante *Salva* .

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

Il collegamento del pulsante Salva al `OnSaveButtonClicked` metodo si verifica nel markup XAML per la `NoteEntryPage` classe:

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>Elenchi

[`ListView`](xref:Xamarin.Forms.ListView) È responsabile della visualizzazione di una raccolta di elementi verticalmente in un elenco. Ogni elemento nell' `ListView` oggetto sarà contenuto in un'unica cella.

Nell'esempio [`ListView`](xref:Xamarin.Forms.ListView) `NotesPage`di codice seguente viene illustrato l'oggetto da:

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

Il layout di ogni riga in [`ListView`](xref:Xamarin.Forms.ListView) viene definito all'interno dell' [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) elemento e utilizza Data Binding per visualizzare eventuali note recuperate dall'applicazione. La [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) proprietà è impostata sull'origine dati, in `NotesPage.xaml.cs`:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Questo codice popola l'oggetto [`ListView`](xref:Xamarin.Forms.ListView) con le eventuali note archiviate nel database.

Quando si seleziona una riga in [`ListView`](xref:Xamarin.Forms.ListView), viene generato l' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento. Un gestore eventi, denominato `OnListViewItemSelected`, viene eseguito quando viene generato l'evento:

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

L' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento può accedere all'oggetto associato alla cella tramite la [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) proprietà.

Per ulteriori informazioni sulla [`ListView`](xref:Xamarin.Forms.ListView) classe, vedere [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="navigation"></a>Navigazione

Xamarin.Forms offre diverse esperienze di navigazione tra le pagine, a seconda del tipo [`Page`](xref:Xamarin.Forms.Page) in uso. Per [`ContentPage`](xref:Xamarin.Forms.ContentPage) la navigazione delle istanze può essere gerarchica o modale. Per informazioni sull'esplorazione modale, vedere [Novell. Forms Modal Pages](~/xamarin-forms/app-fundamentals/navigation/modal.md).

> [!NOTE]
> Le classi [`CarouselPage`](xref:Xamarin.Forms.CarouselPage), [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) e [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) offrono esperienze di navigazione alternative. Per altre informazioni, vedere [Navigazione](~/xamarin-forms/app-fundamentals/navigation/index.md).

Nell'esplorazione gerarchica la classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) viene utilizzata per spostarsi tra uno stack di [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetti, avanti e indietro, a seconda delle esigenze. La classe implementa la navigazione come stack LIFO (Last-In, First-Out) di oggetti [`Page`](xref:Xamarin.Forms.Page). Per passare da una pagina all'altra, un'applicazione esegue il push di una nuova pagina nello stack di navigazione, in cui diventa la pagina attiva. Per tornare alla pagina precedente, l'applicazione preleva la pagina corrente dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva.

La classe `NavigationPage` aggiungerà inoltre una barra di navigazione nella parte superiore della pagina che consente di visualizzare un titolo e un pulsante **Indietro** appropriato per la piattaforma per tornare alla pagina precedente.

La prima pagina aggiunta a uno stack di navigazione viene definita pagina *radice* dell'applicazione e nell'esempio di codice seguente viene illustrato il modo in cui questa operazione viene eseguita nell'applicazione Note:

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

In questo modo viene `NoteEntryPage` eseguito il push del nuovo oggetto nello stack di navigazione, in cui diventa la pagina attiva.

La pagina attiva può essere prelevata dallo stack di navigazione premendo il pulsante *Indietro* (fisico o su schermo) del dispositivo. Per tornare a livello di codice alla pagina originale, l'oggetto `NoteEntryPage` deve chiamare il metodo [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync), come illustrato nell'esempio di codice seguente:

```csharp
await Navigation.PopAsync();
```

Per altre informazioni sulla navigazione gerarchica, vedere [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="data-binding"></a>Associazione dati

Il data binding viene usato per semplificare il modo in cui un'applicazione Xamarin.Forms visualizza i propri dati e interagisce con essi. Stabilisce una connessione tra l'interfaccia utente e l'applicazione sottostante. La classe [`BindableObject`](xref:Xamarin.Forms.BindableObject) contiene la maggior parte dell'infrastruttura per supportare il data binding.

Il data binding (o associazione di dati) consente di connettere due oggetti, detti oggetti di *origine* e di *destinazione*. L'oggetto di *origine* visualizza i dati. L'oggetto di *destinazione* usa (e spesso visualizza) i dati dall'oggetto di origine. Un [`Editor`](xref:Xamarin.Forms.Editor) (oggetto di*destinazione* ), ad esempio, in genere [`Text`](xref:Xamarin.Forms.Editor.Text) associa la relativa proprietà `string` a una proprietà pubblica in un oggetto di *origine* . Il diagramma che segue illustra la relazione di associazione:

![](deepdive-images/data-binding.png "Data Binding")

Il vantaggio principale del data binding sta nel fatto che non è più necessario preoccuparsi della sincronizzazione tra le visualizzazioni e l'origine dati. Le modifiche apportate all'oggetto di *origine* vengono trasmesse automaticamente in background all'oggetto di *destinazione* dal framework di associazione. Facoltativamente, le modifiche all'oggetto di destinazione possono anche essere respinte all'oggetto di *origine*.

La definizione di data binding è un processo in due passaggi:

- La proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) dell'oggetto di *destinazione* deve essere impostata sull'*origine*.
- È necessario stabilire un'associazione tra la *destinazione* e l'*origine*. In XAML questo si ottiene usando l'estensione di markup [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension).

Nell'applicazione Notes la destinazione del binding è [`Editor`](xref:Xamarin.Forms.Editor) la che visualizza una nota, mentre l' `Note` istanza impostata come [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) di è l' `NoteEntryPage` origine dell'associazione.

L' `BindingContext` oggetto`NoteEntryPage` di viene impostato durante la navigazione tra le pagine, come illustrato nell'esempio di codice seguente:

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

Nel metodo, che viene eseguito quando viene aggiunta una nuova nota all'applicazione, l'oggetto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) di `NoteEntryPage` viene impostato su una nuova `Note` istanza di. `OnNoteAddedClicked` `Note` `BindingContext` [`ListView`](xref:Xamarin.Forms.ListView) [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) `NoteEntryPage` Nel metodo, che viene eseguito quando viene selezionata una nota esistente in, il valore di viene impostato sull'istanza selezionata, a cui si accede tramite la proprietà. `OnListViewItemSelected`

> [!IMPORTANT]
> È possibile impostare individualmente la proprietà [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) di ogni oggetto di *destinazione*, ma questa operazione non è necessaria. `BindingContext` è una proprietà speciale che viene ereditata da tutti gli elementi figlio. Pertanto, quando l' `BindingContext` `Note` oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) in è impostato su un' `Note` `ContentPage` istanza di, tutti gli elementi figlio di hanno lo stesso `BindingContext`e possono eseguire l'associazione alle proprietà pubbliche dell'oggetto.

[`Editor`](xref:Xamarin.Forms.Editor) In viene`NoteEntryPage`quindi associato alla`Note` proprietà dell'oggetto: `Text`

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

Viene definita un'associazione tra la proprietà [`Editor.Text`](xref:Xamarin.Forms.Editor.Text) e la proprietà `Text` dell'oggetto di *origine*. Le modifiche apportate in `Editor` verranno automaticamente propagate `Note` all'oggetto. Analogamente, se vengono apportate `Note.Text` modifiche alla proprietà, il motore `Editor`di associazione Novell. Forms aggiornerà anche il contenuto di. Questa funzionalità è detta *associazione bidirezionale*.

Per altre informazioni sul data binding, vedere [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="styling"></a>Stile

Le applicazioni Novell. Forms contengono spesso più elementi visivi con un aspetto identico. L'impostazione dell'aspetto di ogni elemento visivo può essere ripetitiva e soggetta a errori. È invece possibile creare stili che definiscono l'aspetto e quindi applicati agli elementi visivi richiesti.

La [`Style`](xref:Xamarin.Forms.Style) classe raggruppa una raccolta di valori di proprietà in un oggetto che può quindi essere applicato a più istanze di elementi visivi. Gli stili vengono archiviati in [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)un oggetto a livello di applicazione, a livello di pagina o a livello di visualizzazione. La scelta della posizione in `Style` cui definire un effetto può essere utilizzata:

- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di applicazione possono essere applicate all'interno dell'applicazione.
- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di pagina possono essere applicate alla pagina e ai relativi elementi figlio.
- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di visualizzazione possono essere applicate alla vista e ai relativi elementi figlio.

> [!IMPORTANT]
> Gli stili usati in tutta l'applicazione vengono archiviati nel dizionario risorse dell'applicazione per evitare la duplicazione. Tuttavia, il codice XAML specifico di una pagina non deve essere incluso nel dizionario risorse dell'applicazione, perché le risorse verranno analizzate all'avvio dell'applicazione anziché quando richiesto da una pagina.

Ogni [`Style`](xref:Xamarin.Forms.Style) istanza contiene una raccolta di uno o più [`Setter`](xref:Xamarin.Forms.Setter) oggetti, ognuno `Setter` dei quali ha [`Property`](xref:Xamarin.Forms.Setter.Property) un oggetto [`Value`](xref:Xamarin.Forms.Setter.Value)e un oggetto. È il nome della proprietà associabile dell'elemento a cui viene applicato lo stile `Value` e è il valore applicato alla proprietà. `Property` Nell'esempio di codice riportato di seguito viene `NoteEntryPage`illustrato uno stile da:

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

Questo stile viene applicato a tutte [`Editor`](xref:Xamarin.Forms.Editor) le istanze nella pagina.

Quando si crea [`Style`](xref:Xamarin.Forms.Style)un oggetto [`TargetType`](xref:Xamarin.Forms.Style.TargetType) , la proprietà è sempre obbligatoria.

> [!NOTE]
> Lo stile di un'applicazione Novell. Forms viene tradizionalmente eseguito tramite gli stili XAML. Tuttavia, Novell. Forms supporta anche l'applicazione di stili agli elementi visivi tramite Cascading Style Sheets (CSS). Per altre informazioni, vedere [applicazione di stili a Novell. Forms con Cascading Style Sheets (CSS)](~/xamarin-forms/user-interface/styles/css/index.md).

Per altre informazioni sugli stili XAML, vedere [Applicazione di stili alle app Xamarin.Forms con gli stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

### <a name="providing-platform-specific-styles"></a>Fornire stili specifici della piattaforma

Le `OnPlatform` estensioni di markup consentono di personalizzare l'aspetto dell'interfaccia utente in base alla piattaforma:

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

[`Color`](xref:Xamarin.Forms.Color) [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) Vengono impostati valori diversi per le proprietà [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor) e di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), a seconda della piattaforma utilizzata. [`Style`](xref:Xamarin.Forms.Style)

Per altre informazioni sulle estensioni di markup XAML, vedere [XAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md) (Estensioni di markup XAML). Per informazioni sull' `OnPlatform` estensione di markup, vedere [estensione di markup onplatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="testing-and-deployment"></a>Test e distribuzione

Visual Studio per Mac e Visual Studio offrono entrambi numerose opzioni per il test e la distribuzione di un'applicazione. Il debug delle applicazioni fa parte del ciclo di vita dello sviluppo delle applicazioni e consente di diagnosticare i problemi del codice. Per altre informazioni, vedere gli articoli relativi all'[impostazione di un punto di interruzione](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), al [passaggio attraverso il codice](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) e all'[output di informazioni alla finestra del log](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

I simulatori sono un ottimo strumento per iniziare a distribuire e testare e offrono funzionalità utili per testare le applicazioni. Tuttavia, gli utenti non useranno l'applicazione finale in un simulatore, quindi le applicazioni devono essere testate nei dispositivi reali presto e spesso. Per altre informazioni sul provisioning dei dispositivi iOS, vedere [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md). Per altre informazioni sul provisioning dei dispositivi Android, vedere [Set Up Device for Development](~/android/get-started/installation/set-up-device-for-development.md) (Configurare il dispositivo per lo sviluppo).

## <a name="next-steps"></a>Passaggi successivi

Questo approfondimento ha esaminato le nozioni di base dello sviluppo di applicazioni con Novell. Forms. I passaggi suggeriti che seguono includono informazioni sulle funzionalità seguenti:

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
- [Esempi di Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Esempi introduttivi](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms%20get%20started)
- [Informazioni di riferimento sull'API di Xamarin.Forms](xref:Xamarin.Forms)
- [Apprendimento guidato gratuito (video)](https://university.xamarin.com/self-guided/)
