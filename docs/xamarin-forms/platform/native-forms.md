---
title: Novell. Forms nei progetti nativi Novell
description: Questo articolo illustra come utilizzare le pagine derivate da ContentPage che vengono aggiunte direttamente ai progetti nativi Novell e come spostarsi tra di essi.
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/19/2019
ms.openlocfilehash: 0c84b844455b8a792b8cbe2f4dac97097e5ebd97
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "69621064"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Novell. Forms nei progetti nativi Novell

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)

In genere, un'applicazione Novell. Forms include una o più pagine che derivano da [`ContentPage`](xref:Xamarin.Forms.ContentPage)e queste pagine sono condivise da tutte le piattaforme in un progetto di libreria .NET standard o in un progetto condiviso. Tuttavia, i moduli nativi consentono di aggiungere pagine derivate da `ContentPage` direttamente alle applicazioni native Novell. iOS, Novell. Android e UWP. Rispetto al fatto che il progetto nativo utilizza pagine derivate da `ContentPage` da un progetto di libreria .NET Standard o da un progetto condiviso, il vantaggio di aggiungere pagine direttamente ai progetti nativi è che le pagine possono essere estese con le visualizzazioni native. Le visualizzazioni native possono quindi essere denominate in XAML con `x:Name` e a cui viene fatto riferimento dal code-behind. Per ulteriori informazioni sulle visualizzazioni native, vedere [visualizzazioni native](~/xamarin-forms/platform/native-views/index.md).

Il processo per l'utilizzo di una pagina derivata da [`ContentPage`](xref:Xamarin.Forms.ContentPage)Novell. Forms in un progetto nativo è il seguente:

1. Aggiungere il pacchetto NuGet Novell. Forms al progetto nativo.
1. Aggiungere la pagina derivata da [`ContentPage`](xref:Xamarin.Forms.ContentPage)e tutte le dipendenze al progetto nativo.
1. Chiamare il metodo `Forms.Init`.
1. Costruire un'istanza della pagina derivata da [`ContentPage`](xref:Xamarin.Forms.ContentPage)e convertirla nel tipo nativo appropriato usando uno dei seguenti metodi di estensione: `CreateViewController` per iOS, `CreateSupportFragment` per Android o `CreateFrameworkElement` per UWP.
1. Passare alla rappresentazione del tipo nativo della pagina derivata da [`ContentPage`](xref:Xamarin.Forms.ContentPage)usando l'API di navigazione nativa.

Novell. Forms deve essere inizializzato chiamando il metodo `Forms.Init` prima che un progetto nativo possa costruire una pagina derivata da [`ContentPage`](xref:Xamarin.Forms.ContentPage). Scegliere quando eseguire questa operazione dipende principalmente da quando è più comodo nel flusso dell'applicazione, che può essere eseguito all'avvio dell'applicazione o subito prima della creazione della pagina derivata da `ContentPage`. In questo articolo e nelle applicazioni di esempio associate, il metodo `Forms.Init` viene chiamato all'avvio dell'applicazione.

> [!NOTE]
> La soluzione dell'applicazione di esempio **NativeForms** non contiene alcun progetto Novell. Forms. È invece costituito da un progetto Novell. iOS, da un progetto Novell. Android e da un progetto UWP. Ogni progetto è un progetto nativo che utilizza moduli nativi per utilizzare pagine derivate da [`ContentPage`](xref:Xamarin.Forms.ContentPage). Tuttavia, non esiste alcun motivo per cui i progetti nativi non possono utilizzare pagine derivate da `ContentPage` da un progetto di libreria .NET Standard o da un progetto condiviso.

Quando si usano i moduli nativi, le funzionalità di Novell. Forms, ad esempio [`DependencyService`](xref:Xamarin.Forms.DependencyService), [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)e il motore Data Binding, funzionano comunque. Tuttavia, l'esplorazione delle pagine deve essere eseguita usando l'API di navigazione nativa.

## <a name="ios"></a>iOS

In iOS, l'override `FinishedLaunching` nella classe `AppDelegate` è in genere il luogo in cui eseguire le attività correlate all'avvio dell'applicazione. Viene chiamato dopo che l'applicazione è stata avviata ed è in genere sottoposta a override per configurare la finestra principale e il controller di visualizzazione. Nell'esempio di codice seguente viene illustrata la classe `AppDelegate` nell'applicazione di esempio:

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

- Novell. Forms viene inizializzato chiamando il metodo `Forms.Init`.
- Un riferimento alla classe `AppDelegate` viene archiviato nel campo `Instance` `static`. Ciò consente alle altre classi di chiamare i metodi definiti nella classe `AppDelegate`.
- Il `UIWindow`, ovvero il contenitore principale per le visualizzazioni nelle applicazioni iOS native, viene creato.
- La proprietà `FolderPath` viene inizializzata in un percorso nel dispositivo in cui verranno archiviati i dati di nota.
- La classe `NotesPage`, ovvero una pagina derivata da [`ContentPage`](xref:Xamarin.Forms.ContentPage)Novell. Forms definita in XAML, viene costruita e convertita in un `UIViewController` usando il metodo di estensione `CreateViewController`.
- Viene impostata la proprietà `Title` della `UIViewController`, che verrà visualizzata nella `UINavigationBar`.
- Viene creata una `AppNavigationController` per la gestione della navigazione gerarchica. Si tratta di una classe del controller di spostamento personalizzata che deriva da `UINavigationController`. L'oggetto `AppNavigationController` gestisce uno stack di controller di visualizzazione e i `UIViewController` passati nel costruttore verranno presentati inizialmente quando viene caricato il `AppNavigationController`.
- L'oggetto `AppNavigationController` viene impostato come `UIViewController` di primo livello per la `UIWindow` e il `UIWindow` viene impostato come finestra chiave per l'applicazione e viene reso visibile.

Una volta eseguito il `FinishedLaunching` metodo, viene visualizzata l'interfaccia utente definita nella classe Novell. Forms `NotesPage`, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione Novell. iOS che usa un'interfaccia utente definita in XAML](native-forms-images/ios-notespage.png "App Novell. iOS con un'interfaccia utente XAML")](native-forms-images/ios-notespage-large.png#lightbox "App Novell. iOS con un'interfaccia utente XAML")

L'interazione con l'interfaccia utente, ad esempio toccando il **+** [`Button`](xref:Xamarin.Forms.Button), comporterà l'esecuzione del seguente gestore eventi nel code-behind `NotesPage`:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

Il `static` campo `AppDelegate.Instance` consente di richiamare il metodo `AppDelegate.NavigateToNoteEntryPage`, illustrato nell'esempio di codice seguente:

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

Il metodo `NavigateToNoteEntryPage` converte la pagina derivata da [`ContentPage`](xref:Xamarin.Forms.ContentPage)Novell. Forms in un `UIViewController` con il metodo di estensione `CreateViewController` e imposta la proprietà `Title` del `UIViewController`. Il `UIViewController` viene quindi inserito `AppNavigationController` dal metodo di `PushViewController`. Quindi, viene visualizzata l'interfaccia utente definita nella classe Novell. Forms `NoteEntryPage`, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione Novell. iOS che usa un'interfaccia utente definita in XAML](native-forms-images/ios-noteentrypage.png "App Novell. iOS con un'interfaccia utente XAML")](native-forms-images/ios-noteentrypage-large.png#lightbox "App Novell. iOS con un'interfaccia utente XAML")

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

In Android, il `OnCreate` override nella classe `MainActivity` è in genere il luogo in cui eseguire le attività correlate all'avvio dell'applicazione. Nell'esempio di codice seguente viene illustrata la classe `MainActivity` nell'applicazione di esempio:

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

- Novell. Forms viene inizializzato chiamando il metodo `Forms.Init`.
- Un riferimento alla classe `MainActivity` viene archiviato nel campo `Instance` `static`. Ciò consente alle altre classi di chiamare i metodi definiti nella classe `MainActivity`.
- Il contenuto del `Activity` viene impostato da una risorsa di layout. Nell'applicazione di esempio, il layout è costituito da una `LinearLayout` che contiene una `Toolbar` e da un `FrameLayout` funge da contenitore di frammenti.
- Il `Toolbar` viene recuperato e impostato come barra delle azioni per la `Activity` e viene impostato il titolo della barra delle azioni.
- La proprietà `FolderPath` viene inizializzata in un percorso nel dispositivo in cui verranno archiviati i dati di nota.
- La classe `NotesPage`, ovvero una pagina derivata da [`ContentPage`](xref:Xamarin.Forms.ContentPage)Novell. Forms definita in XAML, viene costruita e convertita in un `Fragment` usando il metodo di estensione `CreateSupportFragment`.
- La classe `SupportFragmentManager` crea ed Esegui il commit di una transazione che sostituisce l'istanza `FrameLayout` con la `Fragment` per la classe `NotesPage`.

Per ulteriori informazioni sui frammenti, vedere [frammenti](~/android/platform/fragments/index.md).

Una volta eseguito il `OnCreate` metodo, viene visualizzata l'interfaccia utente definita nella classe Novell. Forms `NotesPage`, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione Novell. Android che usa un'interfaccia utente definita in XAML](native-forms-images/android-notespage.png "App Novell. Android con un'interfaccia utente XAML")](native-forms-images/android-notespage-large.png#lightbox "App Novell. Android con un'interfaccia utente XAML")

L'interazione con l'interfaccia utente, ad esempio toccando il **+** [`Button`](xref:Xamarin.Forms.Button), comporterà l'esecuzione del seguente gestore eventi nel code-behind `NotesPage`:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

Il `static` campo `MainActivity.Instance` consente di richiamare il metodo `MainActivity.NavigateToNoteEntryPage`, illustrato nell'esempio di codice seguente:

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

Il metodo `NavigateToNoteEntryPage` converte la pagina derivata da [`ContentPage`](xref:Xamarin.Forms.ContentPage)Novell. Forms in un `Fragment` con il metodo di estensione `CreateSupportFragment` e aggiunge il `Fragment` allo stack indietro del frammento. Quindi, viene visualizzata l'interfaccia utente definita nel `NoteEntryPage` Novell. Forms, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione Novell. Android che usa un'interfaccia utente definita in XAML](native-forms-images/android-noteentrypage.png "App Novell. Android con un'interfaccia utente XAML")](native-forms-images/android-noteentrypage-large.png#lightbox "App Novell. Android con un'interfaccia utente XAML")

Quando viene visualizzata la `NoteEntryPage`, toccando la freccia indietro si estrae la `Fragment` per il `NoteEntryPage` dallo stack indietro del frammento, restituendo l'utente al `Fragment` per la classe `NotesPage`.

### <a name="enable-back-navigation-support"></a>Abilita supporto per la navigazione indietro

La classe `SupportFragmentManager` dispone di un evento di `BackStackChanged` che viene attivato ogni volta che viene modificato il contenuto dello stack indietro del frammento. Il metodo `OnCreate` nella classe `MainActivity` contiene un gestore eventi anonimo per questo evento:

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

Questo gestore eventi Visualizza un pulsante indietro sulla barra delle azioni purché esistano una o più istanze di `Fragment` nello stack indietro del frammento. La risposta al tocco del pulsante indietro viene gestita dal `OnOptionsItemSelected` override:

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

L'override del `OnOptionsItemSelected` viene chiamato ogni volta che viene selezionata una voce nel menu opzioni. Questa implementazione estrae il frammento corrente dallo stack indietro del frammento, a condizione che sia stato selezionato il pulsante indietro e che esistano una o più istanze di `Fragment` nello stack indietro del frammento.

### <a name="multiple-activities"></a>Più attività

Quando un'applicazione è costituita da più attività, le pagine derivate da [`ContentPage`](xref:Xamarin.Forms.ContentPage)possono essere incorporate in ognuna delle attività. In questo scenario, è necessario chiamare il metodo `Forms.Init` solo nell'override del `OnCreate` della prima `Activity` che incorpora un `ContentPage` Novell. Forms. Questa operazione, tuttavia, ha l'effetto seguente:

- Il valore di `Xamarin.Forms.Color.Accent` verrà tratto dal `Activity` che ha chiamato il metodo `Forms.Init`.
- Il valore di `Xamarin.Forms.Application.Current` verrà associato al `Activity` che ha chiamato il metodo `Forms.Init`.

### <a name="choose-a-file"></a>Scegliere un file

Quando si incorpora una pagina derivata da [`ContentPage`](xref:Xamarin.Forms.ContentPage)che usa una [`WebView`](xref:Xamarin.Forms.WebView) che deve supportare un pulsante "Choose file" HTML, è necessario che il `Activity` esegua l'override del metodo `OnActivityResult`:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

In UWP, la classe `App` nativa è in genere la posizione in cui eseguire le attività correlate all'avvio dell'applicazione. Novell. Forms viene in genere inizializzato, in Novell. Forms UWP Applications, nel `OnLaunched` override nella classe nativa `App`, per passare l'argomento `LaunchActivatedEventArgs` al metodo `Forms.Init`. Per questo motivo, le applicazioni UWP native che utilizzano una pagina derivata di Novell. Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage)possono chiamare più facilmente il metodo `Forms.Init` dal metodo `App.OnLaunched`.

Per impostazione predefinita, la classe `App` nativa avvia la classe `MainPage` come prima pagina dell'applicazione. Nell'esempio di codice seguente viene illustrata la classe `MainPage` nell'applicazione di esempio:

```csharp
public sealed partial class MainPage : Page
{
    public static MainPage Instance;

    public static string FolderPath { get; private set; }

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        this.Content = new Notes.UWP.Views.NotesPage().CreateFrameworkElement();
    }
    ...
}
```

Il costruttore `MainPage` esegue le attività seguenti:

- La memorizzazione nella cache è abilitata per la pagina, in modo che non venga costruita una nuova `MainPage` quando un utente torna alla pagina.
- Un riferimento alla classe `MainPage` viene archiviato nel campo `Instance` `static`. Ciò consente alle altre classi di chiamare i metodi definiti nella classe `MainPage`.
- La proprietà `FolderPath` viene inizializzata in un percorso nel dispositivo in cui verranno archiviati i dati di nota.
- La classe `NotesPage`, che è una pagina derivata da [`ContentPage`](xref:Xamarin.Forms.ContentPage)Novell. Forms definita in XAML, viene costruita e convertita in un `FrameworkElement` usando il metodo di estensione `CreateFrameworkElement` e quindi impostato come contenuto della classe `MainPage`.

Una volta eseguito il costruttore `MainPage`, viene visualizzata l'interfaccia utente definita nella classe Novell. Forms `NotesPage`, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione UWP che usa un'interfaccia utente definita con Novell. Forms XAML](native-forms-images/uwp-notespage.png "App UWP con interfaccia utente XAML di Novell. Forms")](native-forms-images/uwp-notespage-large.png#lightbox "App UWP con interfaccia utente XAML di Novell. Forms")

L'interazione con l'interfaccia utente, ad esempio toccando il **+** [`Button`](xref:Xamarin.Forms.Button), comporterà l'esecuzione del seguente gestore eventi nel code-behind `NotesPage`:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

Il `static` campo `MainPage.Instance` consente di richiamare il metodo `MainPage.NavigateToNoteEntryPage`, illustrato nell'esempio di codice seguente:

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    this.Frame.Navigate(new NoteEntryPage
    {
        BindingContext = note
    });
}
```

La navigazione in UWP viene in genere eseguita con il metodo `Frame.Navigate`, che accetta un argomento `Page`. Novell. Forms definisce un metodo di estensione `Frame.Navigate` che accetta un'istanza di pagina derivata da [`ContentPage`](xref:Xamarin.Forms.ContentPage). Pertanto, quando viene eseguito il `NavigateToNoteEntryPage` metodo, viene visualizzata l'interfaccia utente definita nel `NoteEntryPage` Novell. Forms, come illustrato nello screenshot seguente:

[![Screenshot di un'applicazione UWP che usa un'interfaccia utente definita con Novell. Forms XAML](native-forms-images/uwp-noteentrypage.png "App UWP con interfaccia utente XAML di Novell. Forms")](native-forms-images/uwp-noteentrypage-large.png#lightbox "App UWP con interfaccia utente XAML di Novell. Forms")

Quando viene visualizzata la `NoteEntryPage`, toccando la freccia indietro si estrae la `FrameworkElement` per il `NoteEntryPage` dallo stack back in-app, restituendo l'utente al `FrameworkElement` per la classe `NotesPage`.

### <a name="enable-back-navigation-support"></a>Abilita supporto per la navigazione indietro

In UWP, le applicazioni devono abilitare la navigazione indietro per tutti i pulsanti hardware e software indietro in diversi fattori di forma dei dispositivi. Questa operazione può essere eseguita registrando un gestore eventi per l'evento `BackRequested`, che può essere eseguito nel metodo `OnLaunched` della classe nativa `App`:

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;

    if (rootFrame == null)
    {
        ...      
        // Place the frame in the current Window
        Window.Current.Content = rootFrame;

        SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
    }
    ...
}
```

Quando viene avviata l'applicazione, il metodo `GetForCurrentView` recupera l'oggetto `SystemNavigationManager` associato alla visualizzazione corrente, quindi registra un gestore eventi per l'evento `BackRequested`. L'applicazione riceve questo evento solo se è l'applicazione in primo piano e, in risposta, chiama il gestore dell'evento `OnBackRequested`:

```csharp
void OnBackRequested(object sender, BackRequestedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        e.Handled = true;
        rootFrame.GoBack();
    }
}
```

Il gestore dell'evento `OnBackRequested` chiama il metodo `GoBack` sul frame radice dell'applicazione e imposta la proprietà `BackRequestedEventArgs.Handled` su `true` per contrassegnare l'evento come gestito. L'impossibilità di contrassegnare l'evento come gestito potrebbe causare l'evento ignorato.

L'applicazione sceglie se visualizzare un pulsante indietro sulla barra del titolo. Questa operazione viene eseguita impostando la proprietà `AppViewBackButtonVisibility` su uno dei valori di enumerazione `AppViewBackButtonVisibility`:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

Il gestore dell'evento `OnNavigated`, che viene eseguito in risposta alla generazione dell'evento `Navigated`, aggiorna la visibilità del pulsante indietro della barra del titolo quando si verifica la navigazione della pagina. In questo modo si garantisce che il pulsante indietro della barra del titolo sia visibile se lo stack back in-app non è vuoto o rimosso dalla barra del titolo se lo stack di back-app è vuoto.

Per altre informazioni sul supporto per la navigazione indietro in UWP, vedere [cronologia di navigazione e navigazione all'indietro per le app UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="related-links"></a>Collegamenti correlati

- [NativeForms (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)
- [Visualizzazioni native](~/xamarin-forms/platform/native-views/index.md)
