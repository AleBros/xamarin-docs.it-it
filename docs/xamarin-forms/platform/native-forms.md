---
title: Moduli nativi
description: I moduli nativi consentono pagine derivate ContentPage xamarin. Forms deve essere utilizzato da progetti nativi, xamarin. IOS, xamarin e della piattaforma UWP (Universal Windows). Progetti nativi possono utilizzare le pagine derivate ContentPage che vengono aggiunti direttamente al progetto o da una libreria di classe portabile (PCL), la libreria Standard di .NET o progetto condiviso. In questo articolo viene illustrato come utilizzare pagine derivate ContentPage aggiunti direttamente a progetti nativi e come passare tra di essi.
ms.topic: article
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/11/2018
ms.openlocfilehash: 5a7d4ab69ff3ccd1a50ea4fccb6e494f7c73fc72
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="native-forms"></a>Moduli nativi

_I moduli nativi consentono pagine derivate ContentPage xamarin. Forms deve essere utilizzato da progetti nativi, xamarin. IOS, xamarin e della piattaforma UWP (Universal Windows). Progetti nativi possono utilizzare le pagine derivate ContentPage che vengono aggiunti direttamente al progetto o da una libreria di classe portabile (PCL), la libreria Standard di .NET o progetto condiviso. In questo articolo viene illustrato come utilizzare pagine derivate ContentPage aggiunti direttamente a progetti nativi e come passare tra di essi._

In genere, un'applicazione di xamarin. Forms include una o più pagine che derivano da [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/), e queste pagine sono condivisi da tutte le piattaforme in una libreria di classi Portabile, la libreria Standard di .NET o progetto condiviso. Tuttavia, i moduli nativi consente `ContentPage`-derivato pagine da aggiungere direttamente alle applicazioni native di xamarin. IOS, xamarin e UWP. Confrontare con il progetto nativo utilizzano `ContentPage`-pagine derivate da una libreria di classi Portabile, la libreria Standard di .NET o progetto condiviso, il vantaggio di aggiunta di pagine direttamente a progetti nativi è che le pagine possono essere esteso con viste native. Viste native possono quindi essere denominate in XAML con `x:Name` e a cui viene fatto riferimento dal code-behind. Per ulteriori informazioni sulle viste native, vedere [viste nativo](~/xamarin-forms/platform/native-views/index.md).

Il processo per l'utilizzo di un xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-pagina derivata in un progetto nativo è come segue:

1. Aggiungere il pacchetto NuGet di xamarin. Forms per il progetto nativo.
1. Aggiungere il [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-pagina e tutte le dipendenze, al progetto nativo derivati.
1. Chiamare il metodo `Forms.Init`.
1. Creare un'istanza di [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-pagina derivata e convertirlo nel tipo nativo appropriato utilizzando uno dei seguenti metodi di estensione: `CreateViewController` per iOS, `CreateFragment` o `CreateSupportFragment` per Android, o `CreateFrameworkElement` per piattaforma UWP.
1. Passare alla rappresentazione di tipo nativo del [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-pagina utilizzando la navigazione native API derivata.

Xamarin. Forms deve essere inizializzato chiamando il `Forms.Init` metodo prima di un progetto nativo è possibile costruire un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-pagina derivata. Scelta del momento a tale scopo principalmente dipende da quando è più semplice nel flusso dell'applicazione: è stato possibile eseguire all'avvio dell'applicazione, o immediatamente prima che il `ContentPage`-pagina derivata viene costruito. In questo articolo e le applicazioni di esempio associato, il `Forms.Init` metodo viene chiamato all'avvio dell'applicazione.

> [!NOTE]
> Il **NativeForms** soluzione dell'applicazione di esempio non contiene tutti i progetti di xamarin. Forms. In alternativa, è costituito da un progetto xamarin. IOS, un progetto xamarin e un progetto UWP. Ogni progetto è un progetto nativo che utilizza form Native di utilizzare [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-pagine derivate. Tuttavia, non è necessario perché non è stato possibile utilizzare i progetti nativi `ContentPage`-deriva le pagine da una libreria di classi Portabile, la libreria Standard di .NET o progetto condiviso.

Quando si utilizzano moduli nativi, ad esempio funzionalità di xamarin. Forms [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/), [ `MessagingCenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MessagingCenter/)e il motore di associazione dati, tutto il lavoro ancora.

## <a name="ios"></a>iOS

In iOS, il `FinishedLaunching` esegue l'override nel `AppDelegate` classe è in genere possibile eseguire l'applicazione avvio attività correlate. Viene chiamato dopo l'applicazione è avviato e in genere viene eseguito l'override per configurare la finestra principale e visualizzare i controller. Nell'esempio di codice riportato di seguito viene illustrato il `AppDelegate` classe nell'applicazione di esempio:

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static AppDelegate Instance;

    UIWindow _window;
    UINavigationController _navigation;

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        Forms.Init();

        Instance = this;
        _window = new UIWindow(UIScreen.MainScreen.Bounds);

        UINavigationBar.Appearance.SetTitleTextAttributes(new UITextAttributes
        {
            TextColor = UIColor.Black
        });

        var mainPage = new PhonewordPage().CreateViewController();
        mainPage.Title = "Phoneword";

        _navigation = new UINavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    ...
}
```

Il `FinishedLaunching` metodo esegue le attività seguenti:

- Xamarin. Forms viene inizializzato chiamando il `Forms.Init` metodo.
- Un riferimento di `AppDelegate` classe viene archiviata nel `static` `Instance` campo. Si tratta di fornire un meccanismo per chiamare i metodi definiti nel ad altre classi di `AppDelegate` classe.
- Il `UIWindow`, che è il contenitore principale per le viste nelle applicazioni native per iOS, viene creato.
- Il `PhonewordPage` (classe), ovvero un xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-pagina definito in XAML derivata, viene costruito e convertito in un `UIViewController` utilizzando il `CreateViewController` metodo di estensione.
- Il `Title` proprietà del `UIViewController` è impostata, che verrà visualizzato nella `UINavigationBar`.
- Oggetto `UINavigationController` viene creato per la gestione di navigazione gerarchica. Il `UINavigationController` classe gestisce uno stack di controller di visualizzazione e la `UIViewController` passato nel costruttore visualizzerà inizialmente quando il `UINavigationController` viene caricato.
- Il `UINavigationController` istanza viene impostata come di primo livello `UIViewController` per il `UIWindow`e `UIWindow` è impostata come la finestra di chiave per l'applicazione e viene reso visibile.

Una volta il `FinishedLaunching` il metodo è stato eseguito, l'interfaccia utente definiti in di xamarin. Forms `PhonewordPage` classe verrà visualizzata come mostrato nella schermata seguente:

[![](native-forms-images/ios-phonewordpage.png "iOS PhonewordPage")](native-forms-images/ios-phonewordpage-large.png#lightbox "PhonewordPage iOS")

L'interazione con l'interfaccia utente, ad esempio toccandoli un [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/), comporterà gestori eventi nel `PhonewordPage` l'esecuzione di codice. Ad esempio, quando un utente tocca il **cronologia chiamare** pulsante, il seguente gestore eventi viene eseguita:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToCallHistoryPage();
}
```

Il `static` `AppDelegate.Instance` campo consente di `AppDelegate.NavigateToCallHistoryPage` metodo da richiamare, come illustrato nell'esempio di codice seguente:

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateViewController();
    callHistoryPage.Title = "Call History";
    _navigation.PushViewController(callHistoryPage, true);
}
```

Il `NavigateToCallHistoryPage` metodo converte il xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivato dalla pagina un `UIViewController` con il `CreateViewController` metodo di estensione e imposta il `Title` proprietà del `UIViewController`. Il `UIViewController` viene quindi inserito in `UINavigationController` dal `PushViewController` metodo. Pertanto, l'interfaccia utente definita di xamarin. Forms `CallHistoryPage` classe verrà visualizzata come mostrato nella schermata seguente:

[![](native-forms-images/ios-callhistorypage.png "iOS CallHistoryPage")](native-forms-images/ios-callhistorypage-large.png#lightbox "iOS CallHistoryPage")

Quando il `CallHistoryPage` viene visualizzato, toccare il back freccia verrà visualizzata la `UIViewController` per il `CallHistoryPage` classe il `UINavigationController`, restituzione all'utente del `UIViewController` per il `PhonewordPage` classe.

## <a name="android"></a>Android

In Android, il `OnCreate` esegue l'override nel `MainActivity` classe è in genere possibile eseguire l'applicazione avvio attività correlate. Nell'esempio di codice riportato di seguito viene illustrato il `MainActivity` classe nell'applicazione di esempio:

```csharp
public class MainActivity : AppCompatActivity
{
    public static MainActivity Instance;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        Instance = this;

        SetContentView(Resource.Layout.Main);
        var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
        SetSupportActionBar(toolbar);
        SupportActionBar.Title = "Phoneword";

        var mainPage = new PhonewordPage().CreateFragment(this);
        FragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

Il `OnCreate` metodo esegue le attività seguenti:

- Xamarin. Forms viene inizializzato chiamando il `Forms.Init` metodo.
- Un riferimento di `MainActivity` classe viene archiviata nel `static` `Instance` campo. Si tratta di fornire un meccanismo per chiamare i metodi definiti nel ad altre classi di `MainActivity` classe.
- Il `Activity` content è impostato da una risorsa di layout. Nell'applicazione di esempio, il layout è costituito un `LinearLayout` che contiene un `Toolbar`e un `FrameLayout` di agire come un contenitore di frammento.
- Il `Toolbar` viene recuperato e impostato come la barra delle azioni per il `Activity`, e viene impostato il titolo della barra di azione.
- Il `PhonewordPage` (classe), ovvero un xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-pagina definito in XAML derivata, viene costruito e convertito in un `Fragment` utilizzando il `CreateFragment` metodo di estensione.
- Il `FragmentManager` classe crea ed esegue il commit di una transazione che sostituisce il `FrameLayout` istanza con il `Fragment` per la `PhonewordPage` classe.

Per ulteriori informazioni sui frammenti, vedere [frammenti](~/android/platform/fragments/index.md).

> [!NOTE]
> Oltre al `CreateFragment` metodo di estensione, xamarin. Forms include anche un `CreateSupportFragment` metodo. Il `CreateFragment` metodo crea un `Android.App.Fragment` che può essere usata nelle applicazioni destinate a 11 API e maggiore. Il `CreateSupportFragment` metodo crea un `Android.Support.V4.App.Fragment` che può essere utilizzato nelle applicazioni destinate a versioni API precedenti a 11.

Una volta il `OnCreate` il metodo è stato eseguito, l'interfaccia utente definiti in di xamarin. Forms `PhonewordPage` classe verrà visualizzata come mostrato nella schermata seguente:

[![](native-forms-images/android-phonewordpage.png "Android PhonewordPage")](native-forms-images/android-phonewordpage-large.png#lightbox "PhonewordPage Android")

L'interazione con l'interfaccia utente, ad esempio toccandoli un [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/), comporterà gestori eventi nel `PhonewordPage` l'esecuzione di codice. Ad esempio, quando un utente tocca il **cronologia chiamare** pulsante, il seguente gestore eventi viene eseguita:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToCallHistoryPage();
}
```

Il `static` `MainActivity.Instance` campo consente di `MainActivity.NavigateToCallHistoryPage` metodo da richiamare, come illustrato nell'esempio di codice seguente:

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateFragment(this);
    FragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, callHistoryPage)
        .Commit();
}
```

Il `NavigateToCallHistoryPage` metodo converte il xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivato dalla pagina un `Fragment` con il `CreateFragment` metodo di estensione e aggiunge il `Fragment` al frammento stack indietro. Pertanto, l'interfaccia utente definita di xamarin. Forms `CallHistoryPage` verranno visualizzati, come illustrato nella schermata seguente:

[![](native-forms-images/android-callhistorypage.png "Android CallHistoryPage")](native-forms-images/android-callhistorypage-large.png#lightbox "Android CallHistoryPage")

Quando il `CallHistoryPage` viene visualizzato, toccare il back freccia verrà visualizzata la `Fragment` per il `CallHistoryPage` dallo stack indietro di frammento, restituzione all'utente del `Fragment` per la `PhonewordPage` classe.

### <a name="enabling-back-navigation-support"></a>Abilitazione del supporto di navigazione all'indietro

Il `FragmentManager` classe dispone di un `BackStackChanged` evento che viene generato ogni volta che il contenuto dello stack indietro frammento viene modificato. Il `OnCreate` metodo la `MainActivity` classe contiene un gestore eventi anonimo per questo evento:

```csharp
FragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = FragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Call History" : "Phoneword";
};
```

Questo gestore eventi viene visualizzato un pulsante Indietro sulla barra delle azioni, purché non vi sia uno o più `Fragment` istanze sul frammento stack indietro. La risposta a toccando il pulsante Indietro è gestita dal `OnOptionsItemSelected` override:

```csharp
public override bool OnOptionsItemSelected(Android.Views.IMenuItem item)
{
    if (item.ItemId == global::Android.Resource.Id.Home && FragmentManager.BackStackEntryCount > 0)
    {
        FragmentManager.PopBackStack();
        return true;
    }
    return base.OnOptionsItemSelected(item);
}
```

Il `OnOptionsItemSelected` override viene chiamato ogni volta che viene selezionato un elemento nel menu di opzioni. Questa implementazione estrae il frammento corrente dallo stack indietro di frammento, a condizione che è stato selezionato il pulsante Indietro e sono presenti uno o più `Fragment` istanze sul frammento stack indietro.

### <a name="multiple-activities"></a>Più attività

Quando un'applicazione è composto da più attività, [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-pagine derivate possono essere incorporate in ognuna delle attività. In questo scenario, il `Forms.Init` metodo debba essere chiamato solo nel `OnCreate` eseguire l'override del primo `Activity` che incorpora un xamarin. Forms `ContentPage`. Tuttavia, questo presenta le seguenti conseguenze:

- Il valore di `Xamarin.Forms.Color.Accent` verrà copiato il `Activity` che ha chiamato il `Forms.Init` (metodo).
- Il valore di `Xamarin.Forms.Application.Current` verrà associato il `Activity` che ha chiamato la `Forms.Init` metodo.

### <a name="choosing-a-file"></a>Scelta di un File

Quando si incorpora un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivato pagina che utilizza un [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) che è necessario per supportare HTML "Scegli File" pulsante, il `Activity` sarà necessario eseguire l'override di `OnActivityResult` metodo:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

Nella piattaforma UWP, nativo `App` classe è in genere possibile eseguire l'applicazione avvio attività correlate. Xamarin. Forms viene in genere inizializzata, nelle applicazioni UWP xamarin. Forms, nel `OnLaunched` override nativo `App` (classe), per passare il `LaunchActivatedEventArgs` argomento per il `Forms.Init` metodo. Per questo motivo, le applicazioni UWP native che utilizzano un xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-pagina derivato è possibile chiamare più facilmente il `Forms.Init` metodo il `App.OnLaunched` metodo.

Per impostazione predefinita, l'oggetto nativo `App` classe avvia la `MainPage` classe come la prima pagina dell'applicazione. Nell'esempio di codice riportato di seguito viene illustrato il `MainPage` classe nell'applicazione di esempio:

```csharp
public sealed partial class MainPage : Page
{
    public static MainPage Instance;

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        this.Content = new Phoneword.UWP.Views.PhonewordPage().CreateFrameworkElement();
    }
    ...
}
```

Il `MainPage` costruttore esegue le attività seguenti:

- La memorizzazione nella cache è abilitata per la pagina, in modo che un nuovo `MainPage` non viene costruito quando un utente passa alla pagina.
- Un riferimento di `MainPage` classe viene archiviata nel `static` `Instance` campo. Si tratta di fornire un meccanismo per chiamare i metodi definiti nel ad altre classi di `MainPage` classe.
- Il `PhonewordPage` (classe), ovvero un xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-pagina definito in XAML derivata, viene costruito e convertito in un `FrameworkElement` utilizzando il `CreateFrameworkElement` metodo di estensione e quindi impostare come contenuto del `MainPage` classe.

Una volta il `MainPage` costruttore è stato eseguito, l'interfaccia utente definiti in di xamarin. Forms `PhonewordPage` classe verrà visualizzata come mostrato nella schermata seguente:

[![](native-forms-images/uwp-phonewordpage.png "UWP PhonewordPage")](native-forms-images/uwp-phonewordpage-large.png#lightbox "PhonewordPage UWP")

L'interazione con l'interfaccia utente, ad esempio toccandoli un [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/), comporterà gestori eventi nel `PhonewordPage` l'esecuzione di codice. Ad esempio, quando un utente tocca il **cronologia chiamare** pulsante, il seguente gestore eventi viene eseguita:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    Phoneword.UWP.MainPage.Instance.NavigateToCallHistoryPage();
}
```

Il `static` `MainPage.Instance` campo consente di `MainPage.NavigateToCallHistoryPage` metodo da richiamare, come illustrato nell'esempio di codice seguente:

```csharp
public void NavigateToCallHistoryPage()
{
    this.Frame.Navigate(new CallHistoryPage());
}
```

Navigazione in UWP viene in genere eseguita con il `Frame.Navigate` metodo, che accetta un `Page` argomento. Xamarin. Forms definisce un `Frame.Navigate` metodo di estensione che accetta un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-pagina istanza derivata. Pertanto, quando il `NavigateToCallHistoryPage` esecuzione del metodo, l'interfaccia utente definito in di xamarin. Forms `CallHistoryPage` verranno visualizzati, come illustrato nella schermata seguente:

[![](native-forms-images/uwp-callhistorypage.png "UWP CallHistoryPage")](native-forms-images/uwp-callhistorypage-large.png#lightbox "UWP CallHistoryPage")

Quando il `CallHistoryPage` viene visualizzato, toccare il back freccia verrà visualizzata la `FrameworkElement` per il `CallHistoryPage` dallo stack indietro nell'applicazione, la restituzione all'utente del `FrameworkElement` per la `PhonewordPage` classe.

### <a name="enabling-back-navigation-support"></a>Abilitazione del supporto di navigazione all'indietro

Nella piattaforma UWP, applicazioni è necessario abilitare la navigazione all'indietro per tutti i pulsanti Indietro hardware e software, tra i fattori di forma diversi dispositivi. Questa operazione può essere eseguita registrando un gestore eventi per il `BackRequested` evento, che può essere eseguita nel `OnLaunched` metodo nativo `App` classe:

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

Quando viene avviata l'applicazione, il `GetForCurrentView` metodo recupera il `SystemNavigationManager` oggetto associato alla visualizzazione corrente, quindi Registra un gestore eventi per il `BackRequested` evento. L'applicazione riceve questo evento solo se l'applicazione in primo piano, in risposta, chiama il `OnBackRequested` gestore eventi:

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

Il `OnBackRequested` chiamate del gestore dell'evento il `GoBack` metodo sul frame radice dell'applicazione e imposta il `BackRequestedEventArgs.Handled` proprietà `true` per contrassegnare l'evento come gestito. Per contrassegnare l'evento come gestito può verificarsi nel sistema di navigazione dall'applicazione (per la famiglia di dispositivi mobili) o ignorare l'evento (per la famiglia di dispositivi desktop).

L'applicazione si basa su un pulsante Indietro fornita dal sistema su un telefono, ma se si desidera visualizzare un pulsante Indietro sulla barra del titolo nei dispositivi desktop sceglie. Questo risultato si ottiene impostando il `AppViewBackButtonVisibility` proprietà su uno del `AppViewBackButtonVisibility` valori di enumerazione:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

Il `OnNavigated` gestore dell'evento, che viene eseguito in risposta al `Navigated` generazione dell'evento e aggiorna la visibilità della barra pulsante Indietro del titolo quando si verifica la navigazione. Ciò garantisce che pulsante Indietro sulla barra del titolo è visibile se lo stack indietro nell'applicazione non è vuoto, o rimosso dalla barra del titolo, se lo stack indietro nell'applicazione è vuoto.

Per ulteriori informazioni sul supporto di navigazione all'indietro in UWP, vedere [cronologia di navigazione e la navigazione per App UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="summary"></a>Riepilogo

Form native consentono di xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivato pagine deve essere utilizzato da progetti nativi, xamarin. IOS, xamarin e della piattaforma UWP (Universal Windows). Possono utilizzare i progetti nativi `ContentPage`-derivato pagine che vengono aggiunti direttamente al progetto o da una libreria di classi Portabile, la libreria Standard di .NET o progetto condiviso. Questo articolo ha descritto come utilizzare `ContentPage`-derivato pagine che vengono aggiunti direttamente a progetti nativi e su come spostarsi tra di esse.


## <a name="related-links"></a>Collegamenti correlati

- [NativeForms (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Native2Forms/)
- [Visualizzazioni native](~/xamarin-forms/platform/native-views/index.md)
