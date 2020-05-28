---
title: Xamarin.Formsnei progetti nativi Novell
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9fb741a03d1c8dd2a8754120d0b46567d8889a0b
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132275"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin.Formsnei progetti nativi Novell

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)

In genere, un' Xamarin.Forms applicazione include una o più pagine che derivano da [`ContentPage`](xref:Xamarin.Forms.ContentPage) e queste pagine sono condivise da tutte le piattaforme in un progetto di libreria .NET standard o in un progetto condiviso. Tuttavia, i moduli nativi consentono `ContentPage` di aggiungere pagine derivate da direttamente alle applicazioni native Novell. iOS, Novell. Android e UWP. Rispetto al fatto che il progetto nativo utilizza `ContentPage` pagine derivate da un progetto di libreria .NET standard o un progetto condiviso, il vantaggio di aggiungere pagine direttamente ai progetti nativi è che le pagine possono essere estese con le visualizzazioni native. Le visualizzazioni native possono quindi essere denominate in XAML con `x:Name` e a cui viene fatto riferimento dal code-behind. Per ulteriori informazioni sulle visualizzazioni native, vedere [visualizzazioni native](~/xamarin-forms/platform/native-views/index.md).

Il processo per l'utilizzo di una Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina derivata da in un progetto nativo è il seguente:

1. Aggiungere il Xamarin.Forms pacchetto NuGet al progetto nativo.
1. Aggiungere la [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina derivata da e tutte le dipendenze al progetto nativo.
1. Chiamare il metodo `Forms.Init` .
1. Costruire un'istanza della [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina derivata da e convertirla nel tipo nativo appropriato usando uno dei metodi di estensione seguenti: `CreateViewController` per iOS, `CreateSupportFragment` per Android o `CreateFrameworkElement` per UWP.
1. Passare alla rappresentazione del tipo nativo della [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina derivata da usando l'API di navigazione nativa.

Xamarin.Formsdeve essere inizializzato chiamando il `Forms.Init` metodo prima che un progetto nativo possa costruire una [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina derivata da. Scegliere quando eseguire questa operazione dipende principalmente da quando è più utile nel flusso dell'applicazione, che può essere eseguito all'avvio dell'applicazione o subito prima che `ContentPage` venga costruita la pagina derivata da. In questo articolo e nelle applicazioni di esempio associate, il `Forms.Init` metodo viene chiamato all'avvio dell'applicazione.

> [!NOTE]
> La soluzione dell'applicazione di esempio **NativeForms** non contiene alcun Xamarin.Forms progetto. È invece costituito da un progetto Novell. iOS, da un progetto Novell. Android e da un progetto UWP. Ogni progetto è un progetto nativo che utilizza moduli nativi per utilizzare [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagine derivate da. Tuttavia, non esiste alcun motivo per cui i progetti nativi non possono utilizzare `ContentPage` pagine derivate da un progetto di libreria .NET standard o un progetto condiviso.

Quando si usano i moduli nativi, le Xamarin.Forms funzionalità come [`DependencyService`](xref:Xamarin.Forms.DependencyService) , [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) e il motore di data binding funzionano ancora. Tuttavia, l'esplorazione delle pagine deve essere eseguita usando l'API di navigazione nativa.

## <a name="ios"></a>iOS

In iOS, l' `FinishedLaunching` override nella `AppDelegate` classe è in genere il luogo in cui eseguire le attività correlate all'avvio dell'applicazione. Viene chiamato dopo che l'applicazione è stata avviata ed è in genere sottoposta a override per configurare la finestra principale e il controller di visualizzazione. Nell'esempio di codice seguente viene illustrata la `AppDelegate` classe nell'applicazione di esempio:

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

Il metodo `FinishedLaunching` esegue le attività seguenti:

- Xamarin.Formsviene inizializzata chiamando il `Forms.Init` metodo.
- Un riferimento alla `AppDelegate` classe viene archiviato nel `static` `Instance` campo. Ciò consente alle altre classi di chiamare i metodi definiti nella `AppDelegate` classe.
- `UIWindow`Viene creato il contenitore principale per le visualizzazioni nelle applicazioni iOS native.
- La `FolderPath` proprietà viene inizializzata in un percorso nel dispositivo in cui verranno archiviati i dati di nota.
- La `NotesPage` classe, che è una Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina derivata da definita in XAML, viene costruita e convertita in un oggetto `UIViewController` usando il `CreateViewController` metodo di estensione.
- La `Title` proprietà di `UIViewController` è impostata, che verrà visualizzata nell'oggetto `UINavigationBar` .
- `AppNavigationController`Viene creato un oggetto per la gestione della navigazione gerarchica. Si tratta di una classe del controller di spostamento personalizzata che deriva da `UINavigationController` . L' `AppNavigationController` oggetto gestisce uno stack di controller di visualizzazione e il `UIViewController` passato nel costruttore verrà presentato inizialmente quando `AppNavigationController` viene caricato.
- L' `AppNavigationController` oggetto viene impostato come di primo livello `UIViewController` per e viene `UIWindow` `UIWindow` impostato come finestra chiave per l'applicazione e viene reso visibile.

Una volta `FinishedLaunching` eseguito il metodo, verrà visualizzata l'interfaccia utente definita nella Xamarin.Forms `NotesPage` classe, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione Novell. iOS che usa un'interfaccia utente definita in XAML](native-forms-images/ios-notespage.png "App Novell. iOS con un'interfaccia utente XAML")](native-forms-images/ios-notespage-large.png#lightbox "App Novell. iOS con un'interfaccia utente XAML")

L'interazione con l'interfaccia utente, ad esempio toccando su, comporterà **+** [`Button`](xref:Xamarin.Forms.Button) l'esecuzione del seguente gestore eventi nel `NotesPage` code-behind:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

Il `static` `AppDelegate.Instance` campo consente `AppDelegate.NavigateToNoteEntryPage` di richiamare il metodo, come illustrato nell'esempio di codice seguente:

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

Il `NavigateToNoteEntryPage` metodo converte la Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina derivata da in un oggetto `UIViewController` con il `CreateViewController` metodo di estensione e imposta la `Title` proprietà dell'oggetto `UIViewController` . `UIViewController`Viene quindi eseguito il push `AppNavigationController` di sul `PushViewController` metodo. Pertanto, verrà visualizzata l'interfaccia utente definita nella Xamarin.Forms `NoteEntryPage` classe, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione Novell. iOS che usa un'interfaccia utente definita in XAML](native-forms-images/ios-noteentrypage.png "App Novell. iOS con un'interfaccia utente XAML")](native-forms-images/ios-noteentrypage-large.png#lightbox "App Novell. iOS con un'interfaccia utente XAML")

Quando l'oggetto `NoteEntryPage` viene visualizzato, lo spostamento indietro estrae l'oggetto `UIViewController` per la `NoteEntryPage` classe da `AppNavigationController` , restituendo l'utente a `UIViewController` per la `NotesPage` classe. Tuttavia, la visualizzazione di un `UIViewController` dallo stack di navigazione nativo iOS non elimina automaticamente l' `UIViewController` oggetto e l' `Page` oggetto collegato. Pertanto, la `AppNavigationController` classe esegue l'override del `PopViewController` metodo, per eliminare i controller di visualizzazione nella navigazione all'indietro:

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

L' `PopViewController` override chiama il `Dispose` metodo sull' `UIViewController` oggetto che è stato estratto dallo stack di navigazione nativo iOS. In caso contrario `UIViewController` , il e l'oggetto associato saranno `Page` orfani.

> [!IMPORTANT]
> Gli oggetti orfani non possono essere sottoposti a Garbage Collection, causando una perdita di memoria.

## <a name="android"></a>Android

In Android, l' `OnCreate` override nella `MainActivity` classe è in genere il luogo in cui eseguire le attività correlate all'avvio dell'applicazione. Nell'esempio di codice seguente viene illustrata la `MainActivity` classe nell'applicazione di esempio:

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

Il metodo `OnCreate` esegue le attività seguenti:

- Xamarin.Formsviene inizializzata chiamando il `Forms.Init` metodo.
- Un riferimento alla `MainActivity` classe viene archiviato nel `static` `Instance` campo. Ciò consente alle altre classi di chiamare i metodi definiti nella `MainActivity` classe.
- Il `Activity` contenuto viene impostato da una risorsa di layout. Nell'applicazione di esempio, il layout è costituito da un oggetto `LinearLayout` che contiene un oggetto `Toolbar` e da un oggetto che `FrameLayout` funge da contenitore di frammenti.
- `Toolbar`Viene recuperato e impostato come barra delle azioni per `Activity` e viene impostato il titolo della barra delle azioni.
- La `FolderPath` proprietà viene inizializzata in un percorso nel dispositivo in cui verranno archiviati i dati di nota.
- La `NotesPage` classe, che è una Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina derivata da definita in XAML, viene costruita e convertita in un oggetto `Fragment` usando il `CreateSupportFragment` metodo di estensione.
- La `SupportFragmentManager` classe crea ed Esegui il commit di una transazione che sostituisce l' `FrameLayout` istanza di con `Fragment` per la `NotesPage` classe.

Per ulteriori informazioni sui frammenti, vedere [frammenti](~/android/platform/fragments/index.md).

Una volta `OnCreate` eseguito il metodo, verrà visualizzata l'interfaccia utente definita nella Xamarin.Forms `NotesPage` classe, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione Novell. Android che usa un'interfaccia utente definita in XAML](native-forms-images/android-notespage.png "App Novell. Android con un'interfaccia utente XAML")](native-forms-images/android-notespage-large.png#lightbox "App Novell. Android con un'interfaccia utente XAML")

L'interazione con l'interfaccia utente, ad esempio toccando su, comporterà **+** [`Button`](xref:Xamarin.Forms.Button) l'esecuzione del seguente gestore eventi nel `NotesPage` code-behind:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

Il `static` `MainActivity.Instance` campo consente `MainActivity.NavigateToNoteEntryPage` di richiamare il metodo, come illustrato nell'esempio di codice seguente:

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

Il `NavigateToNoteEntryPage` metodo converte la Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina derivata da in un oggetto `Fragment` con il `CreateSupportFragment` metodo di estensione e aggiunge l'oggetto `Fragment` allo stack indietro del frammento. Pertanto, verrà visualizzata l'interfaccia utente definita in Xamarin.Forms `NoteEntryPage` , come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione Novell. Android che usa un'interfaccia utente definita in XAML](native-forms-images/android-noteentrypage.png "App Novell. Android con un'interfaccia utente XAML")](native-forms-images/android-noteentrypage-large.png#lightbox "App Novell. Android con un'interfaccia utente XAML")

Quando `NoteEntryPage` viene visualizzato, toccando la freccia indietro si estrae l'oggetto `Fragment` per l'oggetto `NoteEntryPage` dallo stack indietro del frammento, restituendo l'utente a `Fragment` per la `NotesPage` classe.

### <a name="enable-back-navigation-support"></a>Abilita supporto per la navigazione indietro

La `SupportFragmentManager` classe ha un `BackStackChanged` evento che viene attivato ogni volta che viene modificato il contenuto dello stack posteriore del frammento. Il `OnCreate` metodo nella `MainActivity` classe contiene un gestore eventi anonimo per questo evento:

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

Questo gestore eventi Visualizza un pulsante indietro sulla barra delle azioni purché esistano una o più `Fragment` istanze nello stack indietro del frammento. La risposta al tocco del pulsante indietro viene gestita dalla `OnOptionsItemSelected` sostituzione:

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

La `OnOptionsItemSelected` sostituzione viene chiamata ogni volta che viene selezionata una voce nel menu opzioni. Questa implementazione estrae il frammento corrente dallo stack indietro del frammento, a condizione che sia stato selezionato il pulsante indietro e che esistano una o più `Fragment` istanze nello stack indietro del frammento.

### <a name="multiple-activities"></a>Più attività

Quando un'applicazione è costituita da più attività, [`ContentPage`](xref:Xamarin.Forms.ContentPage) le pagine derivate da possono essere incorporate in ognuna delle attività. In questo scenario, il `Forms.Init` metodo deve essere chiamato solo nell' `OnCreate` override del primo oggetto `Activity` che incorpora un Xamarin.Forms `ContentPage` . Questa operazione, tuttavia, ha l'effetto seguente:

- Il valore di `Xamarin.Forms.Color.Accent` sarà tratto dall'oggetto `Activity` che ha chiamato il `Forms.Init` metodo.
- Il valore di `Xamarin.Forms.Application.Current` verrà associato all'oggetto `Activity` che ha chiamato il `Forms.Init` metodo.

### <a name="choose-a-file"></a>Scegliere un file

Quando si incorpora una [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina derivata da che usa un [`WebView`](xref:Xamarin.Forms.WebView) che deve supportare un pulsante HTML "Choose file", deve `Activity` eseguire l'override del `OnActivityResult` Metodo:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>Piattaforma UWP

In UWP la classe nativa `App` è in genere la posizione in cui eseguire le attività correlate all'avvio dell'applicazione. Xamarin.Formsviene in genere inizializzato, in Xamarin.Forms applicazioni UWP, nell' `OnLaunched` override della `App` classe nativa, per passare l' `LaunchActivatedEventArgs` argomento al `Forms.Init` metodo. Per questo motivo, le applicazioni UWP native che utilizzano una Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina derivata da possono chiamare più facilmente il `Forms.Init` metodo dal `App.OnLaunched` metodo.

Per impostazione predefinita, la classe nativa `App` Avvia la `MainPage` classe come prima pagina dell'applicazione. Nell'esempio di codice seguente viene illustrata la `MainPage` classe nell'applicazione di esempio:

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

- La memorizzazione nella cache è abilitata per la pagina, in modo che non venga creato un nuovo oggetto `MainPage` quando un utente torna alla pagina.
- Un riferimento alla `MainPage` classe viene archiviato nel `static` `Instance` campo. Ciò consente alle altre classi di chiamare i metodi definiti nella `MainPage` classe.
- La `FolderPath` proprietà viene inizializzata in un percorso nel dispositivo in cui verranno archiviati i dati di nota.
- La `NotesPage` classe, che è una Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina derivata da definita in XAML, viene costruita e convertita in un oggetto `FrameworkElement` usando il `CreateFrameworkElement` metodo di estensione e quindi impostata come contenuto della `MainPage` classe.

Una volta `MainPage` eseguito il costruttore, verrà visualizzata l'interfaccia utente definita nella Xamarin.Forms `NotesPage` classe, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione UWP che usa un'interfaccia utente definita con Xamarin.Forms XAML](native-forms-images/uwp-notespage.png "App UWP con [! OP. Interfaccia utente XAML non-LOC (Novell. Forms)]")](native-forms-images/uwp-notespage-large.png#lightbox "App UWP con [! OP. Interfaccia utente XAML non-LOC (Novell. Forms)]")

L'interazione con l'interfaccia utente, ad esempio toccando su, comporterà **+** [`Button`](xref:Xamarin.Forms.Button) l'esecuzione del seguente gestore eventi nel `NotesPage` code-behind:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

Il `static` `MainPage.Instance` campo consente `MainPage.NavigateToNoteEntryPage` di richiamare il metodo, come illustrato nell'esempio di codice seguente:

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

La navigazione in UWP viene in genere eseguita con il `Frame.Navigate` metodo, che accetta un `Page` argomento. Xamarin.Formsdefinisce un `Frame.Navigate` metodo di estensione che accetta un' [`ContentPage`](xref:Xamarin.Forms.ContentPage) istanza della pagina derivata da. Pertanto, quando il `NavigateToNoteEntryPage` metodo viene eseguito, viene visualizzata l'interfaccia utente definita in Xamarin.Forms `NoteEntryPage` , come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione UWP che usa un'interfaccia utente definita con Xamarin.Forms XAML](native-forms-images/uwp-noteentrypage.png "App UWP con [! OP. Interfaccia utente XAML non-LOC (Novell. Forms)]")](native-forms-images/uwp-noteentrypage-large.png#lightbox "App UWP con [! OP. Interfaccia utente XAML non-LOC (Novell. Forms)]")

Quando l'oggetto `NoteEntryPage` viene visualizzato, toccando la freccia indietro si estrae l'oggetto `FrameworkElement` per l'oggetto `NoteEntryPage` dallo stack back in-app, restituendo l'utente a `FrameworkElement` per la `NotesPage` classe.

### <a name="enable-page-resizing-support"></a>Abilita supporto per il ridimensionamento delle pagine

Quando si ridimensiona la finestra dell'applicazione UWP, anche il Xamarin.Forms contenuto deve essere ridimensionato. Questa operazione viene eseguita registrando un gestore eventi per l' `Loaded` evento nel `MainPage` costruttore:

```csharp
public MainPage()
{
    // ...
    this.Loaded += OnMainPageLoaded;
    // ...
}
```

L' `Loaded` evento viene generato quando la pagina viene disposta, sottoposta a rendering e pronta per l'interazione e il metodo viene eseguito `OnMainPageLoaded` in risposta:

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

Il `OnMainPageLoaded` metodo registra un gestore eventi anonimo per l' `Frame.SizeChanged` evento, che viene generato quando si `ActualHeight` `ActualWidth` modificano le proprietà o su `Frame` . In risposta, il Xamarin.Forms contenuto per la pagina attiva viene ridimensionato chiamando il `Layout` metodo.

### <a name="enable-back-navigation-support"></a>Abilita supporto per la navigazione indietro

In UWP, le applicazioni devono abilitare la navigazione indietro per tutti i pulsanti hardware e software indietro in diversi fattori di forma dei dispositivi. Questa operazione può essere eseguita registrando un gestore eventi per l' `BackRequested` evento, che può essere eseguito nel `MainPage` costruttore:

```csharp
public MainPage()
{
    // ...
    SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
}
```

Quando l'applicazione viene avviata, il `GetForCurrentView` metodo recupera l' `SystemNavigationManager` oggetto associato alla visualizzazione corrente, quindi registra un gestore eventi per l' `BackRequested` evento. L'applicazione riceve questo evento solo se si tratta dell'applicazione in primo piano e, in risposta, chiama il `OnBackRequested` gestore eventi:

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

Il `OnBackRequested` gestore eventi chiama il `GoBack` metodo sul frame radice dell'applicazione e imposta la proprietà su `BackRequestedEventArgs.Handled` `true` per contrassegnare l'evento come gestito. L'impossibilità di contrassegnare l'evento come gestito potrebbe causare l'evento ignorato.

L'applicazione sceglie se visualizzare un pulsante indietro sulla barra del titolo. Questa operazione viene eseguita impostando la `AppViewBackButtonVisibility` proprietà su uno dei `AppViewBackButtonVisibility` valori di enumerazione nella `App` classe:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

Il `OnNavigated` gestore eventi, che viene eseguito in risposta alla `Navigated` generazione dell'evento, aggiorna la visibilità del pulsante indietro della barra del titolo quando si verifica la navigazione della pagina. In questo modo si garantisce che il pulsante indietro della barra del titolo sia visibile se lo stack back in-app non è vuoto o rimosso dalla barra del titolo se lo stack di back-app è vuoto.

Per altre informazioni sul supporto per la navigazione indietro in UWP, vedere [cronologia di navigazione e navigazione all'indietro per le app UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="related-links"></a>Collegamenti correlati

- [NativeForms (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)
- [Visualizzazioni native](~/xamarin-forms/platform/native-views/index.md)
