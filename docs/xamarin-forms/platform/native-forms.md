---
title: Xamarin.Forms in progetti Xamarin Native
description: Questo articolo illustra come usare pagine derivate ContentPage che vengono aggiunti direttamente a progetti nativi per Xamarin e come spostarsi tra di essi.
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/19/2019
ms.openlocfilehash: 83b445b8379ad5181ab1dce142cca06625be79ad
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487334"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin.Forms in progetti Xamarin Native

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)

In genere, un'applicazione Xamarin.Forms include una o più pagine da cui deriva [ `ContentPage` ](xref:Xamarin.Forms.ContentPage), e queste pagine sono condivise da tutte le piattaforme in un progetto condiviso o un progetto di libreria .NET Standard. Tuttavia, consente ai moduli nativi `ContentPage`-derivato pagine per essere aggiunti direttamente alle applicazioni native di Xamarin.IOS, Xamarin.Android e UWP. Rispetto alla presenza di utilizzare il progetto nativo `ContentPage`-pagine derivate da un progetto di libreria .NET Standard o un progetto condiviso, il vantaggio dell'aggiunta di pagine direttamente a progetti nativi è che le pagine possono essere estese con le visualizzazioni native. Visualizzazioni native possono quindi essere denominate in XAML con `x:Name` e con riferimenti dal code-behind. Per altre informazioni sulle visualizzazioni native, vedere [le visualizzazioni Native](~/xamarin-forms/platform/native-views/index.md).

Il processo per l'utilizzo di un Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pagina derivata in un progetto nativo è come segue:

1. Aggiungere il pacchetto NuGet di Xamarin.Forms al progetto nativo.
1. Aggiungere il [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagina ed eventuali dipendenze, per il progetto nativo.
1. Chiamare il metodo `Forms.Init`.
1. Costruire un'istanza di [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pagina derivata e convertirlo nel tipo nativo appropriato utilizzando uno dei metodi di estensione seguenti: `CreateViewController` per iOS, `CreateSupportFragment` per Android, o `CreateFrameworkElement` per PIATTAFORMA UWP.
1. Passare alla rappresentazione di tipo nativo del [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pagina usando l'API di spostamento native derivata.

Xamarin.Forms deve essere inizializzato chiamando il `Forms.Init` metodo prima di un progetto nativo è possibile costruire una [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pagina derivata. Scelta del momento eseguire questa operazione principalmente dipende da quando è più pratico nel flusso dell'applicazione, può essere eseguita all'avvio dell'applicazione, o immediatamente prima che il `ContentPage`-pagina derivata viene costruito. In questo articolo e le applicazioni di esempio associato, il `Forms.Init` viene chiamato all'avvio dell'applicazione.

> [!NOTE]
> Il **NativeForms** soluzione dell'applicazione di esempio non contiene tutti i progetti Xamarin.Forms. In alternativa, è costituito da un progetto Xamarin.IOS, un progetto Xamarin.Android e un progetto UWP. Ogni progetto è un progetto nativo che utilizza form nativi per consumare [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagine. Tuttavia, non è necessario perché non è stato possibile utilizzare i progetti nativi `ContentPage`-derivato pagine da un progetto condiviso o un progetto di libreria .NET Standard.

Quando si usano moduli nativi, ad esempio funzionalità di Xamarin.Forms [ `DependencyService` ](xref:Xamarin.Forms.DependencyService), [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)e il motore di data binding, tutti continuano a funzionare. Tuttavia, navigazione tra le pagine deve essere eseguita mediante l'API di spostamento nativo.

## <a name="ios"></a>iOS

In iOS, il `FinishedLaunching` esegue l'override nel `AppDelegate` classe è in genere la posizione in cui eseguire l'applicazione avvio attività correlate. Viene chiamato dopo l'applicazione ha lanciato e in genere viene sottoposto a override per configurare la finestra principale e visualizzare i controller. Nell'esempio di codice riportato di seguito viene illustrato il `AppDelegate` classe nell'applicazione di esempio:

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static AppDelegate Instance;
    UIWindow _window;
    AppNavigationController _navigation;

    public static string FolderPath { get; private set; }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        Forms.Init();

        Instance = this;
        _window = new UIWindow(UIScreen.MainScreen.Bounds);

        UINavigationBar.Appearance.SetTitleTextAttributes(new UITextAttributes
        {
            TextColor = UIColor.Black
        });

        FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
        UIViewController mainPage = new NotesPage().CreateViewController();
        mainPage.Title = "Notes";

        _navigation = new AppNavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    // ...
}
```

Il `FinishedLaunching` metodo esegue le attività seguenti:

- Xamarin.Forms viene inizializzato chiamando il `Forms.Init` (metodo).
- Un riferimento al `AppDelegate` classe viene archiviata nel `static` `Instance` campo. Si tratta di fornire un meccanismo per chiamare i metodi definiti in ad altre classi di `AppDelegate` classe.
- Il `UIWindow`, che è il contenitore principale per le viste nelle applicazioni native per iOS, viene creata.
- La proprietà `FolderPath` viene inizializzata in un percorso nel dispositivo in cui verranno archiviati i dati di nota.
- Il `NotesPage` classe, ovvero un Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagina definita in XAML, viene costruito e convertito in un `UIViewController` usando la `CreateViewController` metodo di estensione.
- Il `Title` proprietà del `UIViewController` è impostata, che verrà visualizzato nella `UINavigationBar`.
- Oggetto `AppNavigationController` viene creato per la gestione di navigazione gerarchica. Si tratta di una classe del controller di spostamento personalizzata che deriva da `UINavigationController`. L'oggetto `AppNavigationController` gestisce uno stack di controller di visualizzazione e i `UIViewController` passati nel costruttore verranno presentati inizialmente quando viene caricato il `AppNavigationController`.
- L'oggetto `AppNavigationController` viene impostato come `UIViewController` di primo livello per la `UIWindow`e il `UIWindow` viene impostato come finestra chiave per l'applicazione e viene reso visibile.

Una volta il `FinishedLaunching` metodo ha eseguito, l'interfaccia utente definita in Xamarin.Forms `NotesPage` classe verranno visualizzate, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione Xamarin.iOS che usa un'interfaccia utente definita in XAML](native-forms-images/ios-notespage.png "App Xamarin.iOS con un'interfaccia utente XAML")](native-forms-images/ios-notespage-large.png#lightbox "App Xamarin.iOS con un'interfaccia utente XAML")

L'interazione con l'interfaccia utente, ad esempio toccando il **+** [`Button`](xref:Xamarin.Forms.Button), comporterà l'esecuzione del seguente gestore eventi nel code-behind `NotesPage`:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

Il `static` `AppDelegate.Instance` campo consente la `AppDelegate.NavigateToNoteEntryPage` chiamata del metodo, che è illustrato nell'esempio di codice seguente:

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    var noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateViewController();
    noteEntryPage.Title = "Note Entry";
    _navigation.PushViewController(noteEntryPage, true);
}
```

Il `NavigateToNoteEntryPage` metodo converte Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato dalla pagina un `UIViewController` con il `CreateViewController` il metodo di estensione e imposta il `Title` proprietà del `UIViewController`. Il `UIViewController` viene quindi inserito `AppNavigationController` dal `PushViewController` (metodo). Pertanto, l'interfaccia utente definita in Xamarin.Forms `NoteEntryPage` classe verranno visualizzate, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione Xamarin.iOS che usa un'interfaccia utente definita in XAML](native-forms-images/ios-noteentrypage.png "App Xamarin.iOS con un'interfaccia utente XAML")](native-forms-images/ios-noteentrypage-large.png#lightbox "App Xamarin.iOS con un'interfaccia utente XAML")

Quando viene visualizzata la `NoteEntryPage`, lo spostamento indietro estrae il `UIViewController` per la classe `NoteEntryPage` dall'`AppNavigationController`, restituendo l'utente al `UIViewController` per la classe `NotesPage`. Tuttavia, la visualizzazione di un `UIViewController` dallo stack di navigazione nativo iOS non elimina automaticamente l'`UIViewController` e l'oggetto `Page` collegato. Pertanto, la classe `AppNavigationController` esegue l'override del metodo `PopViewController`, per eliminare i controller di visualizzazione nella navigazione all'indietro:

```csharp
public class AppNavigationController : UINavigationController
{
    //...
    public override UIViewController PopViewController(bool animated)
    {
        UIViewController topView = TopViewController;
        if (topView != null)
        {
            // Dispose of ViewController on back navigation.
            topView.Dispose();
        }
        return base.PopViewController(animated);
    }
}
```

L'override del `PopViewController` chiama il metodo `Dispose` sull'oggetto `UIViewController` che è stato estratto dallo stack di navigazione nativo iOS. In caso contrario, il `UIViewController` e il `Page` oggetto associato saranno orfani.

> [!IMPORTANT]
> Gli oggetti orfani non possono essere sottoposti a Garbage Collection, causando una perdita di memoria.

## <a name="android"></a>Android

In Android, il `OnCreate` esegue l'override nel `MainActivity` classe è in genere la posizione in cui eseguire l'applicazione avvio attività correlate. Nell'esempio di codice riportato di seguito viene illustrato il `MainActivity` classe nell'applicazione di esempio:

```csharp
public class MainActivity : AppCompatActivity
{
    public static string FolderPath { get; private set; }

    public static MainActivity Instance;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        Instance = this;

        SetContentView(Resource.Layout.Main);
        var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
        SetSupportActionBar(toolbar);
        SupportActionBar.Title = "Notes";

        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        Android.Support.V4.App.Fragment mainPage = new NotesPage().CreateSupportFragment(this);
        SupportFragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

Il `OnCreate` metodo esegue le attività seguenti:

- Xamarin.Forms viene inizializzato chiamando il `Forms.Init` (metodo).
- Un riferimento al `MainActivity` classe viene archiviata nel `static` `Instance` campo. Si tratta di fornire un meccanismo per chiamare i metodi definiti in ad altre classi di `MainActivity` classe.
- Il `Activity` content è impostato da una risorsa di layout. Nell'applicazione di esempio, il layout è costituito un `LinearLayout` che contiene un `Toolbar`e un `FrameLayout` di agire come un contenitore di frammento.
- Il `Toolbar` viene recuperato e impostato come la barra delle azioni per il `Activity`, e viene impostato il titolo della barra di azione.
- La proprietà `FolderPath` viene inizializzata in un percorso nel dispositivo in cui verranno archiviati i dati di nota.
- Il `NotesPage` classe, ovvero un Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagina definita in XAML, viene costruito e convertito in un `Fragment` usando la `CreateSupportFragment` metodo di estensione.
- Il `SupportFragmentManager` classe crea ed esegue il commit di una transazione che sostituisce il `FrameLayout` dell'istanza con la `Fragment` per il `NotesPage` classe.

Per altre informazioni sui frammenti, vedere [frammenti](~/android/platform/fragments/index.md).

Una volta il `OnCreate` metodo ha eseguito, l'interfaccia utente definita in Xamarin.Forms `NotesPage` classe verranno visualizzate, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione Xamarin.Android che usa un'interfaccia utente definita in XAML](native-forms-images/android-notespage.png "App Xamarin.Android con un'interfaccia utente XAML")](native-forms-images/android-notespage-large.png#lightbox "App Xamarin.Android con un'interfaccia utente XAML")

L'interazione con l'interfaccia utente, ad esempio toccando il **+** [`Button`](xref:Xamarin.Forms.Button), comporterà l'esecuzione del seguente gestore eventi nel code-behind `NotesPage`:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

Il `static` `MainActivity.Instance` campo consente la `MainActivity.NavigateToNoteEntryPage` chiamata del metodo, che è illustrato nell'esempio di codice seguente:

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    Android.Support.V4.App.Fragment noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateSupportFragment(this);
    SupportFragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, noteEntryPage)
        .Commit();
}
```

Il `NavigateToNoteEntryPage` metodo converte Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato dalla pagina una `Fragment` con le `CreateSupportFragment` metodo di estensione e aggiunge il `Fragment` al frammento stack indietro. Pertanto, l'interfaccia utente definita in Xamarin.Forms `NoteEntryPage` verrà visualizzato, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione Xamarin.Android che usa un'interfaccia utente definita in XAML](native-forms-images/android-noteentrypage.png "App Xamarin.Android con un'interfaccia utente XAML")](native-forms-images/android-noteentrypage-large.png#lightbox "App Xamarin.Android con un'interfaccia utente XAML")

Quando la `NoteEntryPage` viene visualizzata, toccando la parte posteriore freccia viene visualizzata la `Fragment` per il `NoteEntryPage` dallo stack indietro di frammento, la restituzione all'utente del `Fragment` per il `NotesPage` classe.

### <a name="enable-back-navigation-support"></a>Abilita supporto per la navigazione indietro

Il `SupportFragmentManager` classe ha un `BackStackChanged` evento che viene attivato ogni volta che il contenuto dello stack indietro frammento viene modificato. Il `OnCreate` metodo di `MainActivity` classe contiene un gestore eventi anonimo per questo evento:

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

Questo gestore eventi viene visualizzato un pulsante Indietro sulla barra delle azioni purché non vi sia uno o più `Fragment` istanze sul frammento stack indietro. La risposta al tocco del pulsante Indietro viene gestita dal `OnOptionsItemSelected` eseguire l'override:

```csharp
public override bool OnOptionsItemSelected(Android.Views.IMenuItem item)
{
    if (item.ItemId == global::Android.Resource.Id.Home && SupportFragmentManager.BackStackEntryCount > 0)
    {
        SupportFragmentManager.PopBackStack();
        return true;
    }
    return base.OnOptionsItemSelected(item);
}
```

Il `OnOptionsItemSelected` override viene chiamato ogni volta che viene selezionato un elemento nel menu di opzioni. Questa implementazione viene visualizzato il frammento corrente dallo stack indietro di frammento, condizione che è stato selezionato il pulsante Indietro e sono presenti uno o più `Fragment` istanze sul frammento stack indietro.

### <a name="multiple-activities"></a>Più attività

Quando un'applicazione è costituita da più attività, [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pagine derivate possono essere incorporate in ognuna delle attività. In questo scenario, il `Forms.Init` metodo debba essere chiamato solo nel `OnCreate` eseguire l'override del primo `Activity` che consente di incorporare una Xamarin.Forms `ContentPage`. Tuttavia, ciò comporta le conseguenze seguenti:

- Il valore di `Xamarin.Forms.Color.Accent` saranno presi dal `Activity` che ha chiamato la `Forms.Init` (metodo).
- Il valore di `Xamarin.Forms.Application.Current` verrà associato il `Activity` che ha chiamato la `Forms.Init` (metodo).

### <a name="choose-a-file"></a>Scegliere un file

Quando si incorpora un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagina che utilizza un [ `WebView` ](xref:Xamarin.Forms.WebView) che necessita di supporto HTML "Choose File" pulsante, il `Activity` sarà necessario eseguire l'override il `OnActivityResult` metodo:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

Nella piattaforma UWP, l'oggetto nativo `App` classe è in genere la posizione in cui eseguire l'applicazione avvio attività correlate. Xamarin.Forms viene in genere inizializzata, nelle applicazioni UWP di Xamarin.Forms, nel `OnLaunched` esegue l'override in nativi `App` (classe), passare il `LaunchActivatedEventArgs` argomento per il `Forms.Init` (metodo). Per questo motivo, le applicazioni UWP native che utilizzano un Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pagina derivato è possibile chiamare più facilmente il `Forms.Init` metodo dal `App.OnLaunched` (metodo).

Per impostazione predefinita, l'oggetto nativo `App` classe avvia il `MainPage` classi come prima pagina dell'applicazione. Nell'esempio di codice riportato di seguito viene illustrato il `MainPage` classe nell'applicazione di esempio:

```csharp
public sealed partial class MainPage : Page
{
    NotesPage notesPage;
    NoteEntryPage noteEntryPage;
    
    public static MainPage Instance;
    public static string FolderPath { get; private set; }

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        notesPage = new Notes.UWP.Views.NotesPage();
        this.Content = notesPage.CreateFrameworkElement();
        // ...        
    } 
    // ...
}
```

Il `MainPage` costruttore esegue le attività seguenti:

- La memorizzazione nella cache è abilitata per la pagina, in modo che un nuovo `MainPage` non è stata costruita quando l'utente passa alla pagina.
- Un riferimento al `MainPage` classe viene archiviata nel `static` `Instance` campo. Si tratta di fornire un meccanismo per chiamare i metodi definiti in ad altre classi di `MainPage` classe.
- La proprietà `FolderPath` viene inizializzata in un percorso nel dispositivo in cui verranno archiviati i dati di nota.
- Il `NotesPage` classe, ovvero un Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagina definita in XAML, viene costruito e convertito in un `FrameworkElement` usando la `CreateFrameworkElement` metodo di estensione e quindi impostarlo come contenuto del `MainPage` classe.

Una volta il `MainPage` esecuzione del costruttore, l'interfaccia utente definita in Xamarin.Forms `NotesPage` classe verranno visualizzate, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione UWP che usa un'interfaccia utente definita con Xamarin.Forms XAML](native-forms-images/uwp-notespage.png "App UWP con interfaccia utente XAML di Xamarin.Forms")](native-forms-images/uwp-notespage-large.png#lightbox "App UWP con interfaccia utente XAML di Xamarin.Forms")

L'interazione con l'interfaccia utente, ad esempio toccando il **+** [`Button`](xref:Xamarin.Forms.Button), comporterà l'esecuzione del seguente gestore eventi nel code-behind `NotesPage`:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

Il `static` `MainPage.Instance` campo consente la `MainPage.NavigateToNoteEntryPage` chiamata del metodo, che è illustrato nell'esempio di codice seguente:

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    noteEntryPage = new Notes.UWP.Views.NoteEntryPage
    {
        BindingContext = note
    };
    this.Frame.Navigate(noteEntryPage);
}
```

Navigazione nella piattaforma UWP viene in genere eseguita con il `Frame.Navigate` metodo, che accetta un `Page` argomento. Xamarin.Forms definisce una `Frame.Navigate` metodo di estensione che accetta una [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pagina istanza derivata. Pertanto, quando la `NavigateToNoteEntryPage` metodo viene eseguito, l'interfaccia utente definito in Xamarin.Forms `NoteEntryPage` verrà visualizzato, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione UWP che usa un'interfaccia utente definita con Xamarin.Forms XAML](native-forms-images/uwp-noteentrypage.png "App UWP con interfaccia utente XAML di Xamarin.Forms")](native-forms-images/uwp-noteentrypage-large.png#lightbox "App UWP con interfaccia utente XAML di Xamarin.Forms")

Quando la `NoteEntryPage` viene visualizzata, toccando la parte posteriore freccia viene visualizzata la `FrameworkElement` per il `NoteEntryPage` dallo stack indietro di in-app, la restituzione all'utente del `FrameworkElement` per il `NotesPage` classe.

### <a name="enable-page-resizing-support"></a>Abilita supporto per il ridimensionamento delle pagine

Quando si ridimensiona la finestra dell'applicazione UWP, anche il contenuto di Xamarin.Forms deve essere ridimensionato. Questa operazione viene eseguita registrando un gestore eventi per l'evento `Loaded`, nel costruttore `MainPage`:

```csharp
public MainPage()
{
    // ...
    this.Loaded += OnMainPageLoaded;
    // ...
}
```

L'evento `Loaded` viene generato quando la pagina viene disposta, sottoposta a rendering e pronta per l'interazione ed esegue il metodo di `OnMainPageLoaded` in risposta:

```csharp
void OnMainPageLoaded(object sender, RoutedEventArgs e)
{
    this.Frame.SizeChanged += (o, args) =>
    {
        if (noteEntryPage != null)
            noteEntryPage.Layout(new Xamarin.Forms.Rectangle(0, 0, args.NewSize.Width, args.NewSize.Height));
        else
            notesPage.Layout(new Xamarin.Forms.Rectangle(0, 0, args.NewSize.Width, args.NewSize.Height));
    };
}
```

Il metodo `OnMainPageLoaded` registra un gestore eventi anonimo per l'evento `Frame.SizeChanged`, che viene generato quando le proprietà `ActualHeight` o `ActualWidth` cambiano nella `Frame`. In risposta, il contenuto di Xamarin.Forms per la pagina attiva viene ridimensionato chiamando il metodo `Layout`.

### <a name="enable-back-navigation-support"></a>Abilita supporto per la navigazione indietro

Nella piattaforma UWP, le applicazioni è necessario abilitare navigazione indietro per tutti i pulsanti Indietro hardware e software, tra diversi form factor. Questa operazione può essere eseguita registrando un gestore eventi per l'evento `BackRequested`, che può essere eseguito nel costruttore `MainPage`:

```csharp
public MainPage()
{
    // ...
    SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
}
```

Quando viene avviata l'applicazione, il `GetForCurrentView` recuperata dal metodo di `SystemNavigationManager` oggetto associato alla visualizzazione corrente e quindi Registra un gestore eventi per il `BackRequested` evento. L'applicazione riceve questo evento solo se l'applicazione in primo piano, in risposta, chiama il `OnBackRequested` gestore dell'evento:

```csharp
void OnBackRequested(object sender, BackRequestedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        e.Handled = true;
        rootFrame.GoBack();
        noteEntryPage = null;
    }
}
```

Il `OnBackRequested` chiamate del gestore eventi di `GoBack` metodo sul frame radice dell'applicazione e del set del `BackRequestedEventArgs.Handled` proprietà `true` per contrassegnare l'evento come gestito. L'impossibilità di contrassegnare l'evento come gestito potrebbe causare l'evento ignorato.

L'applicazione sceglie se visualizzare un pulsante indietro sulla barra del titolo. Questa operazione viene eseguita impostando la proprietà `AppViewBackButtonVisibility` su uno dei valori di enumerazione `AppViewBackButtonVisibility`, nella classe `App`:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

Il `OnNavigated` gestore eventi, che viene eseguito in risposta al `Navigated` generazione dell'evento e aggiorna la visibilità della barra pulsante Indietro del titolo quando si verifica la navigazione. Ciò garantisce che il pulsante Indietro sulla barra del titolo è visibile se lo stack indietro all'interno dell'applicazione non è vuoto o rimosso dalla barra del titolo, se lo stack indietro all'interno dell'applicazione è vuoto.

Per altre informazioni sul supporto di navigazione indietro nella piattaforma UWP, vedere [cronologia di navigazione con le versioni e navigazione per le app UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="related-links"></a>Collegamenti correlati

- [NativeForms (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)
- [Visualizzazioni native](~/xamarin-forms/platform/native-views/index.md)
