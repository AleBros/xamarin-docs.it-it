---
title: Xamarin. Forms in progetti Xamarin Native
description: Questo articolo illustra come usare pagine derivate ContentPage che vengono aggiunti direttamente a progetti nativi per Xamarin e come spostarsi tra di essi.
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
ms.openlocfilehash: e02c04afe656b0eca3b7ae12b8b30f35836b9368
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60954909"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin. Forms in progetti Xamarin Native

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/Native2Forms/)

_Form nativi consentire derivate da xamarin. Forms ContentPage pagine devono essere utilizzate dai progetti xamarin. IOS, xamarin. Android e Universal Windows Platform (UWP) nativi. Progetti nativi possono utilizzare pagine derivate ContentPage che vengono aggiunti direttamente al progetto, o da una libreria .NET Standard, libreria .NET Standard o progetto condiviso. Questo articolo illustra come usare pagine derivate ContentPage che vengono aggiunti direttamente a progetti nativi e come spostarsi tra di essi._

In genere, un'applicazione xamarin. Forms include una o più pagine da cui deriva [ `ContentPage` ](xref:Xamarin.Forms.ContentPage), e queste pagine sono condivise da tutte le piattaforme in un progetto condiviso o un progetto di libreria .NET Standard. Tuttavia, consente ai moduli nativi `ContentPage`-derivato pagine per essere aggiunti direttamente alle applicazioni native di xamarin. IOS, xamarin. Android e UWP. Rispetto alla presenza di utilizzare il progetto nativo `ContentPage`-pagine derivate da un progetto di libreria .NET Standard o un progetto condiviso, il vantaggio dell'aggiunta di pagine direttamente a progetti nativi è che le pagine possono essere estese con le visualizzazioni native. Visualizzazioni native possono quindi essere denominate in XAML con `x:Name` e con riferimenti dal code-behind. Per altre informazioni sulle visualizzazioni native, vedere [le visualizzazioni Native](~/xamarin-forms/platform/native-views/index.md).

Il processo per l'utilizzo di un xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pagina derivata in un progetto nativo è come segue:

1. Aggiungere il pacchetto NuGet di xamarin. Forms al progetto nativo.
1. Aggiungere il [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagina ed eventuali dipendenze, per il progetto nativo.
1. Chiamare il metodo `Forms.Init`.
1. Costruire un'istanza di [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pagina derivata e convertirlo nel tipo nativo appropriato utilizzando uno dei metodi di estensione seguenti: `CreateViewController` per iOS, `CreateSupportFragment` per Android, o `CreateFrameworkElement` per PIATTAFORMA UWP.
1. Passare alla rappresentazione di tipo nativo del [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pagina usando l'API di spostamento native derivata.

Xamarin. Forms deve essere inizializzato chiamando il `Forms.Init` metodo prima di un progetto nativo è possibile costruire una [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pagina derivata. Scelta del momento eseguire questa operazione principalmente dipende da quando è più pratico nel flusso dell'applicazione, può essere eseguita all'avvio dell'applicazione, o immediatamente prima che il `ContentPage`-pagina derivata viene costruito. In questo articolo e le applicazioni di esempio associato, il `Forms.Init` viene chiamato all'avvio dell'applicazione.

> [!NOTE]
> Il **NativeForms** soluzione dell'applicazione di esempio non contiene tutti i progetti xamarin. Forms. In alternativa, è costituito da un progetto xamarin. IOS, un progetto xamarin. Android e un progetto UWP. Ogni progetto è un progetto nativo che utilizza form nativi per consumare [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagine. Tuttavia, non è necessario perché non è stato possibile utilizzare i progetti nativi `ContentPage`-derivato pagine da un progetto condiviso o un progetto di libreria .NET Standard.

Quando si usano moduli nativi, ad esempio funzionalità di xamarin. Forms [ `DependencyService` ](xref:Xamarin.Forms.DependencyService), [ `MessagingCenter` ](xref:Xamarin.Forms.MessagingCenter)e il motore di data binding, tutti continuano a funzionare. Tuttavia, navigazione tra le pagine deve essere eseguita mediante l'API di spostamento nativo.

## <a name="ios"></a>iOS

In iOS, il `FinishedLaunching` esegue l'override nel `AppDelegate` classe è in genere la posizione in cui eseguire l'applicazione avvio attività correlate. Viene chiamato dopo l'applicazione ha lanciato e in genere viene sottoposto a override per configurare la finestra principale e visualizzare i controller. Nell'esempio di codice riportato di seguito viene illustrato il `AppDelegate` classe nell'applicazione di esempio:

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

- Xamarin. Forms viene inizializzato chiamando il `Forms.Init` (metodo).
- Un riferimento al `AppDelegate` classe viene archiviata nel `static` `Instance` campo. Si tratta di fornire un meccanismo per chiamare i metodi definiti in ad altre classi di `AppDelegate` classe.
- Il `UIWindow`, che è il contenitore principale per le viste nelle applicazioni native per iOS, viene creata.
- Il `PhonewordPage` classe, ovvero un xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagina definita in XAML, viene costruito e convertito in un `UIViewController` usando la `CreateViewController` metodo di estensione.
- Il `Title` proprietà del `UIViewController` è impostata, che verrà visualizzato nella `UINavigationBar`.
- Oggetto `UINavigationController` viene creato per la gestione di navigazione gerarchica. Il `UINavigationController` classe gestisce uno stack di controller di visualizzazione e il `UIViewController` passato nel costruttore verrà visualizzato inizialmente quando il `UINavigationController` viene caricato.
- Il `UINavigationController` istanza viene impostata come di primo livello `UIViewController` per il `UIWindow`e il `UIWindow` è impostata come la finestra di chiave per l'applicazione e viene reso visibile.

Una volta il `FinishedLaunching` metodo ha eseguito, l'interfaccia utente definita in xamarin. Forms `PhonewordPage` classe verranno visualizzate, come illustrato nello screenshot seguente:

[![](native-forms-images/ios-phonewordpage.png "iOS PhonewordPage")](native-forms-images/ios-phonewordpage-large.png#lightbox "iOS PhonewordPage")

L'interazione con l'interfaccia utente, ad esempio toccando un [ `Button` ](xref:Xamarin.Forms.Button), genererà i gestori di eventi nel `PhonewordPage` l'esecuzione di code-behind. Ad esempio, quando un utente tocca il **Call History** viene eseguito sul pulsante, il gestore eventi seguente:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToCallHistoryPage();
}
```

Il `static` `AppDelegate.Instance` campo consente la `AppDelegate.NavigateToCallHistoryPage` chiamata del metodo, che è illustrato nell'esempio di codice seguente:

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateViewController();
    callHistoryPage.Title = "Call History";
    _navigation.PushViewController(callHistoryPage, true);
}
```

Il `NavigateToCallHistoryPage` metodo converte xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato dalla pagina un `UIViewController` con il `CreateViewController` il metodo di estensione e imposta il `Title` proprietà del `UIViewController`. Il `UIViewController` viene quindi inserito `UINavigationController` dal `PushViewController` (metodo). Pertanto, l'interfaccia utente definita in xamarin. Forms `CallHistoryPage` classe verranno visualizzate, come illustrato nello screenshot seguente:

[![](native-forms-images/ios-callhistorypage.png "iOS CallHistoryPage")](native-forms-images/ios-callhistorypage-large.png#lightbox "iOS CallHistoryPage")

Quando il `CallHistoryPage` viene visualizzata, toccando la parte posteriore freccia viene visualizzata la `UIViewController` per il `CallHistoryPage` classe il `UINavigationController`, restituzione all'utente del `UIViewController` per il `PhonewordPage` classe.

## <a name="android"></a>Android

In Android, il `OnCreate` esegue l'override nel `MainActivity` classe è in genere la posizione in cui eseguire l'applicazione avvio attività correlate. Nell'esempio di codice riportato di seguito viene illustrato il `MainActivity` classe nell'applicazione di esempio:

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

        var mainPage = new PhonewordPage().CreateSupportFragment(this);
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

- Xamarin. Forms viene inizializzato chiamando il `Forms.Init` (metodo).
- Un riferimento al `MainActivity` classe viene archiviata nel `static` `Instance` campo. Si tratta di fornire un meccanismo per chiamare i metodi definiti in ad altre classi di `MainActivity` classe.
- Il `Activity` content è impostato da una risorsa di layout. Nell'applicazione di esempio, il layout è costituito un `LinearLayout` che contiene un `Toolbar`e un `FrameLayout` di agire come un contenitore di frammento.
- Il `Toolbar` viene recuperato e impostato come la barra delle azioni per il `Activity`, e viene impostato il titolo della barra di azione.
- Il `PhonewordPage` classe, ovvero un xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagina definita in XAML, viene costruito e convertito in un `Fragment` usando la `CreateSupportFragment` metodo di estensione.
- Il `SupportFragmentManager` classe crea ed esegue il commit di una transazione che sostituisce il `FrameLayout` dell'istanza con la `Fragment` per il `PhonewordPage` classe.

Per altre informazioni sui frammenti, vedere [frammenti](~/android/platform/fragments/index.md).

Una volta il `OnCreate` metodo ha eseguito, l'interfaccia utente definita in xamarin. Forms `PhonewordPage` classe verranno visualizzate, come illustrato nello screenshot seguente:

[![](native-forms-images/android-phonewordpage.png "Android PhonewordPage")](native-forms-images/android-phonewordpage-large.png#lightbox "PhonewordPage Android")

L'interazione con l'interfaccia utente, ad esempio toccando un [ `Button` ](xref:Xamarin.Forms.Button), genererà i gestori di eventi nel `PhonewordPage` l'esecuzione di code-behind. Ad esempio, quando un utente tocca il **Call History** viene eseguito sul pulsante, il gestore eventi seguente:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToCallHistoryPage();
}
```

Il `static` `MainActivity.Instance` campo consente la `MainActivity.NavigateToCallHistoryPage` chiamata del metodo, che è illustrato nell'esempio di codice seguente:

```csharp
public void NavigateToCallHistoryPage()
{
    var callHistoryPage = new CallHistoryPage().CreateSupportFragment(this);
    SupportFragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, callHistoryPage)
        .Commit();
}
```

Il `NavigateToCallHistoryPage` metodo converte xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato dalla pagina una `Fragment` con le `CreateSupportFragment` metodo di estensione e aggiunge il `Fragment` al frammento stack indietro. Pertanto, l'interfaccia utente definita in xamarin. Forms `CallHistoryPage` verrà visualizzato, come illustrato nello screenshot seguente:

[![](native-forms-images/android-callhistorypage.png "Android CallHistoryPage")](native-forms-images/android-callhistorypage-large.png#lightbox "CallHistoryPage Android")

Quando la `CallHistoryPage` viene visualizzata, toccando la parte posteriore freccia viene visualizzata la `Fragment` per il `CallHistoryPage` dallo stack indietro di frammento, la restituzione all'utente del `Fragment` per il `PhonewordPage` classe.

### <a name="enabling-back-navigation-support"></a>Abilitazione del supporto di navigazione indietro

Il `SupportFragmentManager` classe ha un `BackStackChanged` evento che viene attivato ogni volta che il contenuto dello stack indietro frammento viene modificato. Il `OnCreate` metodo di `MainActivity` classe contiene un gestore eventi anonimo per questo evento:

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Call History" : "Phoneword";
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

Quando un'applicazione è costituita da più attività, [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pagine derivate possono essere incorporate in ognuna delle attività. In questo scenario, il `Forms.Init` metodo debba essere chiamato solo nel `OnCreate` eseguire l'override del primo `Activity` che consente di incorporare una xamarin. Forms `ContentPage`. Tuttavia, ciò comporta le conseguenze seguenti:

- Il valore di `Xamarin.Forms.Color.Accent` saranno presi dal `Activity` che ha chiamato la `Forms.Init` (metodo).
- Il valore di `Xamarin.Forms.Application.Current` verrà associato il `Activity` che ha chiamato la `Forms.Init` (metodo).

### <a name="choosing-a-file"></a>Scelta di un File

Quando si incorpora un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagina che utilizza un [ `WebView` ](xref:Xamarin.Forms.WebView) che necessita di supporto HTML "Choose File" pulsante, il `Activity` sarà necessario eseguire l'override il `OnActivityResult` metodo:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

Nella piattaforma UWP, l'oggetto nativo `App` classe è in genere la posizione in cui eseguire l'applicazione avvio attività correlate. Xamarin. Forms viene in genere inizializzata, nelle applicazioni UWP di xamarin. Forms, nel `OnLaunched` esegue l'override in nativi `App` (classe), passare il `LaunchActivatedEventArgs` argomento per il `Forms.Init` (metodo). Per questo motivo, le applicazioni UWP native che utilizzano un xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pagina derivato è possibile chiamare più facilmente il `Forms.Init` metodo dal `App.OnLaunched` (metodo).

Per impostazione predefinita, l'oggetto nativo `App` classe avvia il `MainPage` classi come prima pagina dell'applicazione. Nell'esempio di codice riportato di seguito viene illustrato il `MainPage` classe nell'applicazione di esempio:

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

- La memorizzazione nella cache è abilitata per la pagina, in modo che un nuovo `MainPage` non è stata costruita quando l'utente passa alla pagina.
- Un riferimento al `MainPage` classe viene archiviata nel `static` `Instance` campo. Si tratta di fornire un meccanismo per chiamare i metodi definiti in ad altre classi di `MainPage` classe.
- Il `PhonewordPage` classe, ovvero un xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagina definita in XAML, viene costruito e convertito in un `FrameworkElement` usando la `CreateFrameworkElement` metodo di estensione e quindi impostarlo come contenuto del `MainPage` classe.

Una volta il `MainPage` esecuzione del costruttore, l'interfaccia utente definita in xamarin. Forms `PhonewordPage` classe verranno visualizzate, come illustrato nello screenshot seguente:

[![](native-forms-images/uwp-phonewordpage.png "Piattaforma UWP PhonewordPage")](native-forms-images/uwp-phonewordpage-large.png#lightbox "PhonewordPage UWP")

L'interazione con l'interfaccia utente, ad esempio toccando un [ `Button` ](xref:Xamarin.Forms.Button), genererà i gestori di eventi nel `PhonewordPage` l'esecuzione di code-behind. Ad esempio, quando un utente tocca il **Call History** viene eseguito sul pulsante, il gestore eventi seguente:

```csharp
void OnCallHistory(object sender, EventArgs e)
{
    Phoneword.UWP.MainPage.Instance.NavigateToCallHistoryPage();
}
```

Il `static` `MainPage.Instance` campo consente la `MainPage.NavigateToCallHistoryPage` chiamata del metodo, che è illustrato nell'esempio di codice seguente:

```csharp
public void NavigateToCallHistoryPage()
{
    this.Frame.Navigate(new CallHistoryPage());
}
```

Navigazione nella piattaforma UWP viene in genere eseguita con il `Frame.Navigate` metodo, che accetta un `Page` argomento. Xamarin. Forms definisce una `Frame.Navigate` metodo di estensione che accetta una [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-pagina istanza derivata. Pertanto, quando la `NavigateToCallHistoryPage` metodo viene eseguito, l'interfaccia utente definito in xamarin. Forms `CallHistoryPage` verrà visualizzato, come illustrato nello screenshot seguente:

[![](native-forms-images/uwp-callhistorypage.png "Piattaforma UWP CallHistoryPage")](native-forms-images/uwp-callhistorypage-large.png#lightbox "CallHistoryPage UWP")

Quando la `CallHistoryPage` viene visualizzata, toccando la parte posteriore freccia viene visualizzata la `FrameworkElement` per il `CallHistoryPage` dallo stack indietro di in-app, la restituzione all'utente del `FrameworkElement` per il `PhonewordPage` classe.

### <a name="enabling-back-navigation-support"></a>Abilitazione del supporto di navigazione indietro

Nella piattaforma UWP, le applicazioni è necessario abilitare navigazione indietro per tutti i pulsanti Indietro hardware e software, tra diversi form factor. Questa operazione può essere eseguita registrando un gestore eventi per il `BackRequested` evento, che può essere eseguita nel `OnLaunched` metodo nativo `App` classe:

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

Quando viene avviata l'applicazione, il `GetForCurrentView` recuperata dal metodo di `SystemNavigationManager` oggetto associato alla visualizzazione corrente e quindi Registra un gestore eventi per il `BackRequested` evento. L'applicazione riceve questo evento solo se l'applicazione in primo piano, in risposta, chiama il `OnBackRequested` gestore dell'evento:

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

Il `OnBackRequested` chiamate del gestore eventi di `GoBack` metodo sul frame radice dell'applicazione e del set del `BackRequestedEventArgs.Handled` proprietà `true` per contrassegnare l'evento come gestito. Il sistema esce dall'applicazione (per la famiglia di dispositivi mobili) o ignorare l'evento (per la famiglia di dispositivi desktop) potrebbe causare un errore per contrassegnare l'evento come gestito.

L'applicazione si basa su un pulsante Indietro fornita dal sistema in un telefono, ma sceglie se visualizzare un pulsante Indietro sulla barra del titolo nei dispositivi desktop. Questo risultato viene ottenuto impostando il `AppViewBackButtonVisibility` una delle proprietà il `AppViewBackButtonVisibility` valori di enumerazione:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

Il `OnNavigated` gestore eventi, che viene eseguito in risposta al `Navigated` generazione dell'evento e aggiorna la visibilità della barra pulsante Indietro del titolo quando si verifica la navigazione. Ciò garantisce che il pulsante Indietro sulla barra del titolo è visibile se lo stack indietro all'interno dell'applicazione non è vuoto o rimosso dalla barra del titolo, se lo stack indietro all'interno dell'applicazione è vuoto.

Per altre informazioni sul supporto di navigazione indietro nella piattaforma UWP, vedere [cronologia di navigazione con le versioni e navigazione per le app UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="summary"></a>Riepilogo

Form nativi consentire a xamarin. Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-derivato pagine devono essere utilizzate dai progetti xamarin. IOS, xamarin. Android e Universal Windows Platform (UWP) nativi. Progetti nativi possono usare `ContentPage`-derivato pagine che vengono aggiunti direttamente al progetto o da un progetto condiviso o un progetto di libreria .NET Standard. Questo articolo ha illustrato come utilizzare `ContentPage`-derivato pagine che vengono aggiunti direttamente a progetti nativi e su come spostarsi tra di esse.


## <a name="related-links"></a>Collegamenti correlati

- [NativeForms (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Native2Forms/)
- [Visualizzazioni native](~/xamarin-forms/platform/native-views/index.md)
