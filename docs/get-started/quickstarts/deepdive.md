---
title: Xamarin.FormsApprofondimento sulla Guida introduttiva
description: Questo articolo esamina le nozioni di base dello sviluppo di applicazioni con Xamarin.Forms . Gli argomenti trattati includono l'anatomia di un' Xamarin.Forms applicazione, l'architettura e le nozioni fondamentali dell'applicazione e l'interfaccia utente.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.custom: video
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1bfb76f71a2ac9d8bc9ae84152501909000b9623
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84132522"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Xamarin.FormsApprofondimento sulla Guida introduttiva

Nella [ Xamarin.Forms Guida introduttiva](~/get-started/index.yml)è stata compilata l'applicazione Note. Questo articolo esamina gli elementi che sono stati compilati per comprendere le nozioni di base del Xamarin.Forms funzionamento delle applicazioni.

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Introduzione a Visual Studio

Visual Studio organizza il codice in *soluzioni* e *progetti*. Una soluzione è un contenitore per uno o più progetti. Un progetto può essere un'applicazione, una libreria di supporto, un'applicazione di test e altro ancora. L'applicazione Notes è costituita da una soluzione contenente quattro progetti, come illustra lo screenshot seguente:

![](deepdive-images/vs/solution.png "Visual Studio Solution Explorer")

I progetti sono:

- Notes: progetto di libreria .NET Standard che contiene tutto il codice condiviso e l'interfaccia utente condivisa.
- Notes.Android: progetto che contiene il codice specifico di Android ed è il punto di ingresso per l'applicazione Android.
- Notes.iOS: progetto che contiene il codice specifico di iOS ed è il punto di ingresso per l'applicazione iOS.
- Notes.UWP: progetto che contiene il codice specifico della piattaforma UWP (Universal Windows Platform) ed è il punto di ingresso per l'applicazione UWP.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia di un' Xamarin.Forms applicazione

Lo screenshot seguente illustra il contenuto del progetto di libreria .NET Standard Notes in Visual Studio:

![](deepdive-images/vs/net-standard-project.png "Phoneword .NET Standard Project Contents")

Il progetto ha un nodo **Dipendenze** contenente i nodi **NuGet** e **SDK**:

- **NuGet** &ndash; i Xamarin.Forms pacchetti NuGet e SQLite-NET-PCL che sono stati aggiunti al progetto.
- **SDK** &ndash; di il `NETStandard.Library` metapacchetto che fa riferimento al set completo di pacchetti NuGet che definiscono .NET standard.

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Introduzione a Visual Studio per Mac

[Visual Studio per Mac](/visualstudio/mac/) segue la pratica di Visual Studio per organizzare il codice in *soluzioni* e *progetti*. Una soluzione è un contenitore per uno o più progetti. Un progetto può essere un'applicazione, una libreria di supporto, un'applicazione di test e altro ancora. L'applicazione Notes è costituita da una soluzione contenente tre progetti, come illustra lo screenshot seguente:

![](deepdive-images/vsmac/solution.png "Visual Studio for Mac Solution Pane")

I progetti sono:

- Notes: progetto di libreria .NET Standard che contiene tutto il codice condiviso e l'interfaccia utente condivisa.
- Notes.Android: progetto che contiene il codice specifico di Android ed è il punto di ingresso per le applicazioni Android.
- Notes.iOS: progetto che contiene il codice specifico di iOS ed è il punto di ingresso per le applicazioni iOS.

## <a name="anatomy-of-a-xamarinforms-application"></a>Anatomia di un' Xamarin.Forms applicazione

Lo screenshot seguente illustra il contenuto del progetto di libreria .NET Standard Notes in Visual Studio per Mac:

![](deepdive-images/vsmac/net-standard-project.png "Phoneword .NET Standard Library Project Contents")

Il progetto ha un nodo **Dipendenze** contenente i nodi **NuGet** e **SDK**:

- **NuGet** &ndash; i Xamarin.Forms pacchetti NuGet e SQLite-NET-PCL che sono stati aggiunti al progetto.
- **SDK** &ndash; di il `NETStandard.Library` metapacchetto che fa riferimento al set completo di pacchetti NuGet che definiscono .NET standard.

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

Un' Xamarin.Forms applicazione è progettata in modo analogo a un'applicazione multipiattaforma tradizionale. Il codice condiviso in genere viene inserito in una libreria .NET Standard e le applicazioni specifiche della piattaforma usano il codice condiviso. Il diagramma seguente illustra una panoramica di questa relazione per l'applicazione Notes:

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "Notes Architecture")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "Notes Architecture")

::: zone-end

Per ottimizzare il riutilizzo del codice di avvio, le Xamarin.Forms applicazioni dispongono di una sola classe denominata `App` responsabile della creazione di un'istanza della prima pagina che verrà visualizzata dall'applicazione in ogni piattaforma, come illustrato nell'esempio di codice seguente:

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

Questo codice imposta la `MainPage` proprietà della `App` classe su un' [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) istanza il cui contenuto è un' `NotesPage` istanza di.

Il file **AssemblyInfo.cs** contiene inoltre un singolo attributo dell'applicazione, applicato a livello di assembly:

```csharp
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
```

L' [`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) attributo attiva il compilatore XAML, in modo che XAML venga compilato direttamente in linguaggio intermedio. Per altre informazioni, vedere [XAML Compilation](~/xamarin-forms/xaml/xamlc.md) (Compilazione XAML).

## <a name="launching-the-application-on-each-platform"></a>Avvio dell'applicazione in ogni piattaforma

### <a name="ios"></a>iOS

Per avviare la Xamarin.Forms pagina iniziale in iOS, il progetto Notes. iOS definisce la `AppDelegate` classe che eredita dalla `FormsApplicationDelegate` classe:

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

L' `FinishedLaunching` override inizializza il Xamarin.Forms Framework chiamando il `Init` metodo. In questo modo l'implementazione specifica di iOS di viene Xamarin.Forms caricata nell'applicazione prima che il controller di visualizzazione radice venga impostato dalla chiamata al `LoadApplication` metodo.

### <a name="android"></a>Android

Per avviare la Xamarin.Forms pagina iniziale in Android, il progetto Notes. Android include il codice che crea un oggetto `Activity` con l' `MainLauncher` attributo, con l'attività che eredita dalla `FormsAppCompatActivity` classe:

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

L' `OnCreate` override inizializza il Xamarin.Forms Framework chiamando il `Init` metodo. In questo modo l'implementazione specifica di Android di Xamarin.Forms verrà caricata nell'applicazione prima che l' Xamarin.Forms applicazione venga caricata.

::: zone pivot="windows"

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Nelle applicazioni piattaforma UWP (Universal Windows Platform) (UWP), il `Init` metodo che inizializza il Xamarin.Forms Framework viene richiamato dalla `App` classe:

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

In questo modo l'implementazione specifica di UWP di Xamarin.Forms viene caricata nell'applicazione. La Xamarin.Forms pagina iniziale viene avviata dalla `MainPage` classe:

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

L' Xamarin.Forms applicazione viene caricata con il `LoadApplication` metodo.

> [!NOTE]
> Piattaforma UWP (Universal Windows Platform) app possono essere compilate con Xamarin.Forms , ma solo con Visual Studio in Windows.

::: zone-end

## <a name="user-interface"></a>Interfaccia utente

Per creare l'interfaccia utente di un'applicazione sono disponibili quattro gruppi di controllo principali Xamarin.Forms :

1. **Pagine** : le Xamarin.Forms pagine rappresentano le schermate delle applicazioni per dispositivi mobili multipiattaforma. L'applicazione Notes usa la [`ContentPage`](xref:Xamarin.Forms.ContentPage) classe per visualizzare le singole schermate. Per ulteriori informazioni sulle pagine, vedere [ Xamarin.Forms pagine](~/xamarin-forms/user-interface/controls/pages.md).
1. **Viste** : Xamarin.Forms le viste sono i controlli visualizzati nell'interfaccia utente, ad esempio le etichette, i pulsanti e le caselle di immissione di testo. Nell'applicazione Note finite vengono utilizzate [`ListView`](xref:Xamarin.Forms.ListView) le [`Editor`](xref:Xamarin.Forms.Editor) viste, e [`Button`](xref:Xamarin.Forms.Button) . Per ulteriori informazioni sulle visualizzazioni, vedere [ Xamarin.Forms visualizzazioni](~/xamarin-forms/user-interface/controls/views.md).
1. **Layout** : Xamarin.Forms i layout sono contenitori usati per comporre visualizzazioni in strutture logiche. L'applicazione Notes usa la [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe per disporre le visualizzazioni in uno stack verticale e la [`Grid`](xref:Xamarin.Forms.Grid) classe per disporre i pulsanti orizzontalmente. Per ulteriori informazioni sui layout, vedere [ Xamarin.Forms layout](~/xamarin-forms/user-interface/controls/layouts.md).
1. **Celle** : Xamarin.Forms le celle sono elementi specializzati usati per gli elementi di un elenco e descrivono come deve essere disegnato ogni elemento di un elenco. L'applicazione Notes USA [`TextCell`](xref:Xamarin.Forms.TextCell) per visualizzare due elementi per ogni riga nell'elenco. Per ulteriori informazioni sulle celle, vedere [ Xamarin.Forms celle](~/xamarin-forms/user-interface/controls/cells.md).

In fase di runtime ogni controllo viene mappato al controllo nativo equivalente, che viene restituito nel rendering.

### <a name="layout"></a>Layout

L'applicazione Notes USA [`StackLayout`](xref:Xamarin.Forms.StackLayout) per semplificare lo sviluppo di applicazioni multipiattaforma, organizzando automaticamente le visualizzazioni sullo schermo indipendentemente dalle dimensioni dello schermo. Gli elementi figlio vengono posizionati uno dopo l'altro, orizzontalmente o verticalmente, nell'ordine in cui sono stati aggiunti. La quantità di spazio che `StackLayout` verrà utilizzata da dipende dal modo [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) in [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) cui vengono impostate le proprietà e, ma per impostazione predefinita tenterà `StackLayout` di utilizzare l'intera schermata.

Il codice XAML seguente mostra un esempio di utilizzo di un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) per il layout dell'oggetto `NoteEntryPage` :

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
> Le dimensioni delle visualizzazioni possono essere impostate tramite le proprietà `HeightRequest` e `WidthRequest`.

Per ulteriori informazioni sulla [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe, vedere [StackLayout](~/xamarin-forms/user-interface/layouts/stacklayout.md).

### <a name="responding-to-user-interaction"></a>Risposta all'interazione dell'utente

Un oggetto definito in XAML può generare un evento che viene gestito nel file code-behind. Nell'esempio di codice seguente viene illustrato il `OnSaveButtonClicked` metodo nel code-behind per la `NoteEntryPage` classe, che viene eseguito in risposta alla [`Clicked`](xref:Xamarin.Forms.Button.Clicked) generazione dell'evento sul pulsante *Salva* .

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

[`ListView`](xref:Xamarin.Forms.ListView)È responsabile della visualizzazione di una raccolta di elementi verticalmente in un elenco. Ogni elemento in `ListView` sarà contenuto in una singola cella.

Nell'esempio di codice seguente viene illustrato l'oggetto [`ListView`](xref:Xamarin.Forms.ListView) da `NotesPage` :

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

Il layout di ogni riga in [`ListView`](xref:Xamarin.Forms.ListView) viene definito all'interno dell' [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate) elemento e utilizza Data Binding per visualizzare eventuali note recuperate dall'applicazione. La [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) proprietà è impostata sull'origine dati, in `NotesPage.xaml.cs` :

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

Questo codice popola l'oggetto [`ListView`](xref:Xamarin.Forms.ListView) con le eventuali note archiviate nel database.

Quando si seleziona una riga in [`ListView`](xref:Xamarin.Forms.ListView) , viene [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) generato l'evento. Un gestore eventi, denominato `OnListViewItemSelected`, viene eseguito quando viene generato l'evento:

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

L' [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) evento può accedere all'oggetto associato alla cella tramite la [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) Proprietà.

Per ulteriori informazioni sulla [`ListView`](xref:Xamarin.Forms.ListView) classe, vedere [ListView](~/xamarin-forms/user-interface/listview/index.md).

## <a name="navigation"></a>Navigazione

Xamarin.Formsin sono disponibili diverse esperienze di navigazione tra le pagine, a seconda del [`Page`](xref:Xamarin.Forms.Page) tipo utilizzato. Per la [`ContentPage`](xref:Xamarin.Forms.ContentPage) navigazione delle istanze può essere gerarchica o modale. Per informazioni sull'esplorazione modale, vedere [ Xamarin.Forms pagine modali](~/xamarin-forms/app-fundamentals/navigation/modal.md).

> [!NOTE]
> Le [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) classi, e [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) offrono esperienze di navigazione alternative. Per altre informazioni, vedere [Navigazione](~/xamarin-forms/app-fundamentals/navigation/index.md).

Nell'esplorazione gerarchica la [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe viene utilizzata per spostarsi tra uno stack di [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetti, avanti e indietro, a seconda delle esigenze. La classe implementa la navigazione come stack LIFO (Last-in, First-out) di [`Page`](xref:Xamarin.Forms.Page) oggetti. Per passare da una pagina all'altra, un'applicazione esegue il push di una nuova pagina nello stack di navigazione, in cui diventa la pagina attiva. Per tornare alla pagina precedente, l'applicazione preleva la pagina corrente dallo stack di navigazione e la nuova pagina in primo piano diventa la pagina attiva.

La classe `NavigationPage` aggiungerà inoltre una barra di navigazione nella parte superiore della pagina che consente di visualizzare un titolo e un pulsante **Indietro** appropriato per la piattaforma per tornare alla pagina precedente.

La prima pagina aggiunta a uno stack di navigazione è definita pagina *radice* dell'applicazione e il codice seguente illustra come viene eseguita questa operazione nell'applicazione Notes:

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

Tutte le [`ContentPage`](xref:Xamarin.Forms.ContentPage) istanze dispongono di una [`Navigation`](xref:Xamarin.Forms.NavigableElement.Navigation) proprietà che espone i metodi per modificare lo stack di pagine. Questi metodi devono essere richiamati solo se l'applicazione include un oggetto [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Per passare a `NoteEntryPage` , è necessario richiamare [ `PushAsync` ] (xrif: Xamarin.Forms . NavigationPage. PushAsync ( Xamarin.Forms . Page)), come illustrato nell'esempio di codice seguente:

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

In questo modo il nuovo oggetto `NoteEntryPage` viene inserito tramite push nello stack di navigazione, in cui diventa la pagina attiva.

La pagina attiva può essere prelevata dallo stack di navigazione premendo il pulsante *Indietro* (fisico o su schermo) del dispositivo. Per tornare a livello di codice alla pagina originale, l' `NoteEntryPage` oggetto deve richiamare il [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) metodo, come illustrato nell'esempio di codice seguente:

```csharp
await Navigation.PopAsync();
```

Per altre informazioni sulla navigazione gerarchica, vedere [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="data-binding"></a>Associazione dati

Il data binding viene usato per semplificare la Xamarin.Forms visualizzazione e l'interazione di un'applicazione con i relativi dati. Stabilisce una connessione tra l'interfaccia utente e l'applicazione sottostante. La [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe contiene gran parte dell'infrastruttura per supportare Data Binding.

Il data binding (o associazione di dati) consente di connettere due oggetti, detti oggetti di *origine* e di *destinazione*. L'oggetto di *origine* visualizza i dati. L'oggetto di *destinazione* usa (e spesso visualizza) i dati dall'oggetto di origine. Un [`Editor`](xref:Xamarin.Forms.Editor) (oggetto di*destinazione* ), ad esempio, in genere associa la relativa [`Text`](xref:Xamarin.Forms.InputView.Text) proprietà a una `string` proprietà pubblica in un oggetto di *origine* . Il diagramma che segue illustra la relazione di associazione:

![](deepdive-images/data-binding.png "Data Binding")

Il vantaggio principale del data binding sta nel fatto che non è più necessario preoccuparsi della sincronizzazione tra le visualizzazioni e l'origine dati. Le modifiche apportate all'oggetto di *origine* vengono trasmesse automaticamente in background all'oggetto di *destinazione* dal framework di associazione. Facoltativamente, le modifiche all'oggetto di destinazione possono anche essere respinte all'oggetto di *origine*.

La definizione del data binding è un processo in due fasi:

- La [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) proprietà dell'oggetto di *destinazione* deve essere impostata sull' *origine*.
- È necessario stabilire un'associazione tra la *destinazione* e l'*origine*. In XAML, questa operazione viene eseguita usando l' [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) estensione di markup.

Nell'applicazione Notes la destinazione del binding è la [`Editor`](xref:Xamarin.Forms.Editor) che visualizza una nota, mentre l' `Note` istanza impostata come [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) di `NoteEntryPage` è l'origine dell'associazione.

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

Nel `OnNoteAddedClicked` metodo, che viene eseguito quando viene aggiunta una nuova nota all'applicazione, l'oggetto [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) di `NoteEntryPage` viene impostato su una nuova istanza di `Note` . Nel `OnListViewItemSelected` metodo, che viene eseguito quando viene selezionata una nota esistente in [`ListView`](xref:Xamarin.Forms.ListView) , il `BindingContext` `NoteEntryPage` valore di viene impostato sull'istanza selezionata, a `Note` cui si accede tramite la [`e.SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem) Proprietà.

> [!IMPORTANT]
> Sebbene [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) sia possibile impostare singolarmente la proprietà di ogni oggetto di *destinazione* , questa operazione non è necessaria. `BindingContext` è una proprietà speciale che viene ereditata da tutti gli elementi figlio. Pertanto, quando l' `BindingContext` oggetto in [`ContentPage`](xref:Xamarin.Forms.ContentPage) è impostato su un' `Note` istanza di, tutti gli elementi figlio di `ContentPage` hanno lo stesso `BindingContext` e possono eseguire l'associazione alle proprietà pubbliche dell' `Note` oggetto.

[`Editor`](xref:Xamarin.Forms.Editor)In `NoteEntryPage` viene quindi associato alla `Text` proprietà dell' `Note` oggetto:

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

Viene stabilita un'associazione tra la [`Editor.Text`](xref:Xamarin.Forms.InputView.Text) proprietà e la `Text` proprietà dell'oggetto di *origine* . Le modifiche apportate nell'`Editor` vengono propagate automaticamente all'oggetto `Note`. Analogamente, se vengono apportate modifiche alla `Note.Text` proprietà, il Xamarin.Forms motore di associazione aggiornerà anche il contenuto di `Editor` . Questa funzionalità è detta *associazione bidirezionale*.

Per ulteriori informazioni su data binding, vedere [ Xamarin.Forms Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="styling"></a>Stile

Xamarin.Formsle applicazioni contengono spesso più elementi visivi con un aspetto identico. L'impostazione dell'aspetto di ogni elemento visivo può essere ripetitiva e soggetta a errori. È invece possibile creare stili che definiscono l'aspetto e applicarli agli elementi visivi necessari.

La [`Style`](xref:Xamarin.Forms.Style) classe raggruppa una raccolta di valori di proprietà in un oggetto che può quindi essere applicato a più istanze di elementi visivi. Gli stili vengono archiviati in un oggetto a [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) livello di applicazione, a livello di pagina o a livello di visualizzazione. Scegliere dove definire un oggetto `Style` ha effetto su dove può essere usato:

- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di applicazione possono essere applicate all'interno dell'applicazione.
- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di pagina possono essere applicate alla pagina e ai relativi elementi figlio.
- [`Style`](xref:Xamarin.Forms.Style)le istanze definite a livello di visualizzazione possono essere applicate alla vista e ai relativi elementi figlio.

> [!IMPORTANT]
> Tutti gli stili usati nell'applicazione vengono archiviati nel dizionario risorse dell'applicazione per evitare duplicati. Tuttavia, il codice XAML specifico di una pagina non deve essere incluso nel dizionario risorse dell'applicazione per evitare che le risorse vengano analizzate all'avvio dell'applicazione invece che quando richiesto da una pagina.

Ogni [`Style`](xref:Xamarin.Forms.Style) istanza contiene una raccolta di uno o più [`Setter`](xref:Xamarin.Forms.Setter) oggetti, ognuno dei quali ha `Setter` un oggetto [`Property`](xref:Xamarin.Forms.Setter.Property) e un oggetto [`Value`](xref:Xamarin.Forms.Setter.Value) . `Property` è il nome della proprietà associabile dell'elemento a cui viene applicato lo stile e `Value` è il valore applicato alla proprietà. L'esempio di codice seguente illustra uno stile da `NoteEntryPage`:

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

Questo stile viene applicato a tutte le [`Editor`](xref:Xamarin.Forms.Editor) istanze nella pagina.

Quando si crea un oggetto [`Style`](xref:Xamarin.Forms.Style) , la [`TargetType`](xref:Xamarin.Forms.Style.TargetType) proprietà è sempre obbligatoria.

> [!NOTE]
> Lo stile Xamarin.Forms di un'applicazione viene tradizionalmente eseguito tramite gli stili XAML. Tuttavia, Xamarin.Forms supporta anche l'applicazione di stili agli elementi visivi utilizzando Cascading Style Sheets (CSS). Per altre informazioni, vedere [applicazione di stili Xamarin.Forms con CASCADING Style Sheets (CSS)](~/xamarin-forms/user-interface/styles/css/index.md).

Per altre informazioni sugli stili XAML, vedere [applicazione di stili Xamarin.Forms con stili XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

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

[`Style`](xref:Xamarin.Forms.Style) [`Color`](xref:Xamarin.Forms.Color) Vengono impostati valori diversi per le [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) [`BarTextColor`](xref:Xamarin.Forms.NavigationPage.BarTextColor) proprietà e di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , a seconda della piattaforma utilizzata.

Per altre informazioni sulle estensioni di markup XAML, vedere [XAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md) (Estensioni di markup XAML). Per informazioni sull'estensione di markup `OnPlatform`, vedere [Estensione di markup OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="testing-and-deployment"></a>Test e distribuzione

Visual Studio per Mac e Visual Studio offrono entrambi numerose opzioni per il test e la distribuzione di un'applicazione. Il debug delle applicazioni fa parte del ciclo di vita dello sviluppo delle applicazioni e consente di diagnosticare i problemi del codice. Per altre informazioni, vedere gli articoli relativi all'[impostazione di un punto di interruzione](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint), al [passaggio attraverso il codice](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code) e all'[output di informazioni alla finestra del log](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window).

I simulatori sono un ottimo strumento per iniziare a distribuire e testare e offrono funzionalità utili per testare le applicazioni. Tuttavia, gli utenti non useranno l'applicazione finale in un simulatore, quindi le applicazioni devono essere testate nei dispositivi reali presto e spesso. Per altre informazioni sul provisioning dei dispositivi iOS, vedere [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md). Per altre informazioni sul provisioning dei dispositivi Android, vedere [Set Up Device for Development](~/android/get-started/installation/set-up-device-for-development.md) (Configurare il dispositivo per lo sviluppo).

## <a name="next-steps"></a>Passaggi successivi

Questo approfondimento ha esaminato le nozioni di base dello sviluppo di applicazioni con Xamarin.Forms . I passaggi suggeriti che seguono includono informazioni sulle funzionalità seguenti:

- Sono disponibili quattro gruppi di controllo principali per creare l'interfaccia utente di un' Xamarin.Forms applicazione. Per altre informazioni, vedere [Riferimento per i controlli](~/xamarin-forms/user-interface/controls/index.md).
- Il data binding è una tecnica che consente di collegare le proprietà di due oggetti in modo che le modifiche apportate a una proprietà vengano automaticamente riflesse nell'altra proprietà. Per altre informazioni, vedere [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- Xamarin.Formsin sono disponibili diverse esperienze di navigazione tra le pagine, a seconda del tipo di pagina utilizzato. Per altre informazioni, vedere [Navigazione](~/xamarin-forms/app-fundamentals/navigation/index.md).
- Gli stili consentono di ridurre il markup ripetitivo e di modificare facilmente l'aspetto di un'applicazione. Per altre informazioni, vedere [ Xamarin.Forms applicazione di stili](~/xamarin-forms/user-interface/styles/index.md).
- Le estensioni di markup XAML estendono le potenzialità e la flessibilità di XAML consentendo di impostare gli attributi degli elementi da origini diverse dalle stringhe di testo letterali. Per altre informazioni, vedere [Estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md).
- I modelli di dati consentono di definire la presentazione dei dati nelle viste supportate. Per altre informazioni, vedere [Data Templates](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md) (Modelli di dati).
- Il rendering di ogni pagina, layout e vista viene eseguito in modo diverso su ogni piattaforma usando una classe `Renderer` che a sua volta crea un controllo nativo, lo dispone sullo schermo e aggiunge il comportamento specificato nel codice condiviso. Gli sviluppatori possono implementare le proprie classi `Renderer` per personalizzare l'aspetto e/o il comportamento di un controllo. Per altre informazioni, vedere [Custom Renderers](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) (Renderer personalizzati).
- Anche gli effetti consentono la personalizzazione dei controlli nativi in ogni piattaforma. Gli effetti vengono creati nei progetti specifici della piattaforma mediante la creazione di una sottoclasse della [`PlatformEffect`](xref:Xamarin.Forms.PlatformEffect`2) classe e vengono utilizzati mediante la relativa associazione a un Xamarin.Forms controllo appropriato. Per altre informazioni, vedere [Effects](~/xamarin-forms/app-fundamentals/effects/index.md) (Effetti).
- Il codice condiviso può accedere a funzionalità native tramite la [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe. Per altre informazioni, vedere [Accessing Native Features with DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md) (Accesso alle funzionalità native con DependencyService).

In alternativa, è possibile [_creare app per dispositivi mobili con Novell. Forms_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md), un libro di Charles Petzold, per saperne di più su Xamarin.Forms . Il libro è disponibile in formato PDF o in un'ampia gamma di formati di eBook.

## <a name="related-links"></a>Collegamenti correlati

- [Extensible Application Markup Language (XAML)](~/xamarin-forms/xaml/index.yml)
- [Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Riferimento per i controlli](~/xamarin-forms/user-interface/controls/index.md)
- [Estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.FormsCampioni](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Esempi di Introduzione](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms%20get%20started)
- [Xamarin.FormsRiferimento API](xref:Xamarin.Forms)
- [Apprendimento guidato gratuito (video)](https://university.xamarin.com/self-guided/)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
