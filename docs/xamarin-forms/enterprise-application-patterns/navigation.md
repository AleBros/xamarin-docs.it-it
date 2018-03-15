---
title: Navigazione
ms.topic: article
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: ec91a7c100f294437bb1498fcd56a35f5b19c399
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2018
---
# <a name="navigation"></a>Navigazione

Xamarin. Forms include il supporto per lo spostamento di pagina, che in genere comporta l'interazione dell'utente con l'interfaccia utente o l'app in seguito alle modifiche di stato interno basata sulla logica. Tuttavia, navigazione può essere complessa da implementare nelle App che usano il modello Model-View-ViewModel (MVVM), in quanto devono essere soddisfatti i seguenti problemi:

-   Come identificare la vista per essere esplorato, con un approccio che non presenta l'accoppiamento stretto e le dipendenze tra le visualizzazioni.
-   Come coordinare il processo mediante il quale la visualizzazione per spostarsi a viene creata un'istanza e inizializzata. Quando si utilizza MVVM, la visualizzazione e il modello di visualizzazione necessario essere creata e associata tramite il contesto di associazione della vista. Quando un'applicazione utilizza un contenitore dell'inserimento di dipendenza, la creazione di istanze di visualizzazioni e modelli di visualizzazione potrebbe richiedere un meccanismo di formulazione specifica.
-   L'esplorazione di vista prima di eseguire o visualizzare il primo modello di navigazione. Con la vista prima di navigazione, la pagina per passare a fa riferimento al nome del tipo di visualizzazione. Durante lo spostamento, viene creata la vista specificata, con il modello di visualizzazione corrispondenti e altri servizi dipendenti. Un approccio alternativo è usare la navigazione primo modello di visualizzazione, per passare alla pagina in cui si intende il nome del tipo di modello di visualizzazione.
-   Come per correttamente separare il comportamento dell'app per la navigazione tra le visualizzazioni e i modelli di visualizzazione. Il modello MVVM fornisce una separazione tra l'interfaccia utente dell'applicazione e la relativa presentazione e logica di business. Tuttavia, il comportamento di navigazione di un'app sarà spesso si estendono le parti dell'interfaccia utente e di presentazioni dell'app. L'utente spesso avvierà la navigazione da una vista e la visualizzazione verrà sostituita in seguito a riquadro di spostamento. Tuttavia, navigazione spesso inoltre potrebbe essere necessario essere avviata o coordinate all'interno del modello di visualizzazione.
-   Come passare i parametri durante la navigazione per l'inizializzazione. Ad esempio, se l'utente passa a una vista per aggiornare i dettagli dell'ordine, i dati dell'ordine deve essere passato alla visualizzazione in modo da poter visualizzare i dati corretti.
-   Modalità di navigazione di coordinate, per garantire che determinate regole di business sono osservate. Ad esempio, agli utenti potrebbe essere richiesto prima di spostarsi da una vista in modo che è possibile correggere i dati non validi oppure essere richiesto di inviare o annullare le modifiche di dati che sono state apportate all'interno della visualizzazione.

In questo capitolo affronta queste sfide presentando un `NavigationService` classe che viene utilizzato per eseguire navigazione modello prima pagina nella visualizzazione.

> [!NOTE]
> Il `NavigationService` utilizzato per l'applicazione è progettata esclusivamente per eseguire la navigazione gerarchica tra istanze ContentPage. Utilizzo del servizio per spostarsi tra gli altri tipi di pagina potrebbe causare un comportamento imprevisto.

## <a name="navigating-between-pages"></a>Spostamento tra le pagine

La logica di spostamento può risiedere nel code-behind di una vista o in un tipo di dati associato modello di visualizzazione. Durante l'inserimento di logica di navigazione in una vista, è possibile che sia l'approccio più semplice, non è facilmente testabile tramite unit test. Inserire la logica di navigazione nella visualizzazione classi del modello indica che la logica può essere verificata tramite unit test. Inoltre, il modello di visualizzazione possa quindi implementare la logica per navigazione del controllo per assicurarsi che vengono applicate alcune regole di business. Ad esempio, un'app potrebbe non consentire all'utente di spostarsi da una pagina senza prima verificare la validi dei dati immessi.

Oggetto `NavigationService` classe in genere viene richiamata dalla visualizzazione di modelli, per promuovere la testabilità. Tuttavia, passare ad visualizzazioni da visualizzare modelli richiederebbe la visualizzazione di modelli fanno riferimento a viste, e in particolare le visualizzazioni che il modello di visualizzazione attiva non è associato, che non è consigliata. Pertanto, il `NavigationService` presentato qui specifica il tipo di modello di visualizzazione come destinazione a cui passare.

Gli utilizzi di app per dispositivi mobili eShopOnContainers la `NavigationService` classe per la navigazione del modello prima vista. Questa classe implementa il `INavigationService` interfaccia, come illustrato nell'esempio di codice seguente:

```csharp
public interface INavigationService  
{  
    ViewModelBase PreviousPageViewModel { get; }  
    Task InitializeAsync();  
    Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase;  
    Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase;  
    Task RemoveLastFromBackStackAsync();  
    Task RemoveBackStackAsync();  
}
```

Questa interfaccia consente di specificare che una classe di implementazione deve fornire i metodi seguenti:

|Metodo|Scopo|
|--- |--- |
|`InitializeAsync`|Eseguire la navigazione in una delle due pagine quando viene avviata l'app.|
|`NavigateToAsync`|Esegue lo spostamento gerarchico in una pagina specificata.|
|`NavigateToAsync(parameter)`|Esegue lo spostamento gerarchico a una pagina specifica, passando un parametro.|
|`RemoveLastFromBackStackAsync`|Rimuove lo stack di navigazione alla pagina precedente.|
|`RemoveBackStackAsync`|Rimuove tutte le pagine precedenti dallo stack di navigazione.|

Inoltre, il `INavigationService` interfaccia specifica che una classe di implementazione deve fornire un `PreviousPageViewModel` proprietà. Questa proprietà restituisce il tipo di modello di visualizzazione associato alla pagina precedente nello stack di navigazione.

> [!NOTE]
> Un `INavigationService` interfaccia in genere specificare anche un `GoBackAsync` metodo, che viene utilizzato per restituire a livello di codice alla pagina precedente nello stack di navigazione. Tuttavia, questo metodo è disponibile dall'app mobile eShopOnContainers perché non è necessario.

### <a name="creating-the-navigationservice-instance"></a>Creazione dell'istanza NavigationService

Il `NavigationService` classe che implementa il `INavigationService` interfaccia, viene registrato come un singleton con il contenitore dell'inserimento di dipendenza Autofac, come illustrato nell'esempio di codice seguente:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

Il `INavigationService` interfaccia è stato risolto nel `ViewModelBase` costruttore della classe, come illustrato nell'esempio di codice seguente:

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

Restituisce un riferimento al `NavigationService` oggetto archiviato nel contenitore di injection dipendenza Autofac, che viene creato tramite il `InitNavigation` metodo la `App` classe. Per ulteriori informazioni, vedere [esplorazione quando l'App viene avviata](#navigating_when_the_app_is_launched).

Il `ViewModelBase` classe archivi di `NavigationService` dell'istanza in un `NavigationService` la proprietà del tipo `INavigationService`. Pertanto, in tutte le visualizzazione classi del modello, che derivano dal `ViewModelBase` , può utilizzare il `NavigationService` proprietà per accedere ai metodi specificati da di `INavigationService` interfaccia. Questo evita l'overhead della inserendo il `NavigationService` oggetto dal contenitore Autofac dipendenza attacco intrusivo nel codice in ogni classe di modello di visualizzazione.

### <a name="handling-navigation-requests"></a>Gestisce le richieste di navigazione

Xamarin. Forms offre il [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) classe che implementa un'esperienza di navigazione gerarchica in cui l'utente è in grado di scorrere le pagine, Avanti e indietro, in base alle esigenze. Per altre informazioni sulla navigazione gerarchica, vedere [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

Anziché utilizzare il [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) classe direttamente, esegue il wrapping di app eShopOnContainers il `NavigationPage` classe il `CustomNavigationView` classe, come illustrato nell'esempio di codice seguente:

```csharp
public partial class CustomNavigationView : NavigationPage  
{  
    public CustomNavigationView() : base()  
    {  
        InitializeComponent();  
    }  

    public CustomNavigationView(Page root) : base(root)  
    {  
        InitializeComponent();  
    }  
}
```

Lo scopo di questa disposizione è per facilitare la definizione dello stile di [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) istanza all'interno del file XAML per la classe.

Navigazione viene eseguita all'interno delle classi di modello di visualizzazione chiamando uno del `NavigateToAsync` metodi, che specifica il tipo di modello di visualizzazione per la pagina di cui si accede a, come illustrato nell'esempio di codice seguente:

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

Nell'esempio di codice riportato di seguito viene illustrato il `NavigateToAsync` metodi forniti dalla `NavigationService` classe:

```csharp
public Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), null);  
}  

public Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), parameter);  
}
```

Ogni metodo consente a qualsiasi classe di modello di visualizzazione da cui deriva il `ViewModelBase` classe per eseguire lo spostamento gerarchico richiamando il `InternalNavigateToAsync` metodo. Inoltre, il secondo `NavigateToAsync` metodo consente di specificare come argomento passato al modello di visualizzazione si sta spostando, in cui è in genere utilizzato per eseguire l'inizializzazione dei dati navigazione. Per ulteriori informazioni, vedere [passando i parametri durante la navigazione](#passing_parameters_during_navigation).

Il `InternalNavigateToAsync` metodo esegue la richiesta di spostamento e viene illustrato nell'esempio di codice seguente:

```csharp
private async Task InternalNavigateToAsync(Type viewModelType, object parameter)  
{  
    Page page = CreatePage(viewModelType, parameter);  

    if (page is LoginView)  
    {  
        Application.Current.MainPage = new CustomNavigationView(page);  
    }  
    else  
    {  
        var navigationPage = Application.Current.MainPage as CustomNavigationView;  
        if (navigationPage != null)  
        {  
            await navigationPage.PushAsync(page);  
        }  
        else  
        {  
            Application.Current.MainPage = new CustomNavigationView(page);  
        }  
    }  

    await (page.BindingContext as ViewModelBase).InitializeAsync(parameter);  
}  

private Type GetPageTypeForViewModel(Type viewModelType)  
{  
    var viewName = viewModelType.FullName.Replace("Model", string.Empty);  
    var viewModelAssemblyName = viewModelType.GetTypeInfo().Assembly.FullName;  
    var viewAssemblyName = string.Format(  
                CultureInfo.InvariantCulture, "{0}, {1}", viewName, viewModelAssemblyName);  
    var viewType = Type.GetType(viewAssemblyName);  
    return viewType;  
}  

private Page CreatePage(Type viewModelType, object parameter)  
{  
    Type pageType = GetPageTypeForViewModel(viewModelType);  
    if (pageType == null)  
    {  
        throw new Exception($"Cannot locate page type for {viewModelType}");  
    }  

    Page page = Activator.CreateInstance(pageType) as Page;  
    return page;  
}
```

Il `InternalNavigateToAsync` esegue spostamento su un modello di visualizzazione da chiamare prima il `CreatePage` metodo. Questo metodo individua la vista che corrisponde al tipo di modello di visualizzazione specificato e crea e restituisce un'istanza di questo tipo di visualizzazione. Individuare la vista che corrisponde al tipo di modello di visualizzazione, viene utilizzato un approccio basato sulle convenzioni, presupponendo che:

-   Le visualizzazioni sono nello stesso assembly come tipi di modello di visualizzazione.
-   Le visualizzazioni sono una. Spazio dei nomi figlio viste.
-   Visualizzazione di modelli è una. Spazio dei nomi figlio ViewModel.
-   Visualizzare i nomi corrispondono per visualizzare i nomi di modello, con "Modello" rimosso.

Quando viene creata un'istanza di una vista, è associata a relativo modello di visualizzazione corrispondente. Per ulteriori informazioni su come questo errore si verifica, vedere [automaticamente la creazione di un modello di visualizzazione con un indicatore di posizione di visualizzazione modello](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Se la vista viene creata una `LoginView`, che viene eseguito il wrapping in una nuova istanza del `CustomNavigationView` classe e assegnato al [ `Application.Current.MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) proprietà. In caso contrario, il `CustomNavigationView` istanza viene recuperata e fornito non è null, il [ `PushAsync` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) metodo viene richiamato per eseguire il push la vista viene creata nello stack di navigazione. Tuttavia, se recuperato `CustomNavigationView` istanza è `null`, viene eseguito il wrapping la vista viene creata all'interno di una nuova istanza del `CustomNavigationView` classe e assegnato al `Application.Current.MainPage` proprietà. Questo meccanismo garantisce che durante la navigazione, vengono aggiunte pagine correttamente allo stack di navigazione quando è vuoto e se contiene dati.

> [!TIP]
> Prendere in considerazione la memorizzazione nella cache di pagine. Pagina memorizzazione nella cache comporta il consumo di memoria per le viste che non sono attualmente visualizzate. Tuttavia, senza la memorizzazione nella cache di pagina indicano che l'analisi XAML e la costruzione della pagina e il relativo modello di visualizzazione si verificherà ogni volta che una nuova pagina ci si sposta, che possono avere un impatto sulle prestazioni per una pagina complessa. Per una pagina ben progettata che non utilizza un numero eccessivo di controlli, le prestazioni dovrebbero essere sufficienti. Tuttavia, la memorizzazione nella cache di pagina può essere utile se si verificano tempi di caricamento pagina lento.

Dopo la visualizzazione è creata e aperto, il `InitializeAsync` del metodo del modello di visualizzazione associata della vista. Per ulteriori informazioni, vedere [passando i parametri durante la navigazione](#passing_parameters_during_navigation).

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>Esplorazione di App quando viene avviata.

Quando viene avviata l'app, il `InitNavigation` metodo la `App` classe viene richiamata. Esempio di codice seguente viene illustrato questo metodo:

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

Il metodo crea un nuovo `NavigationService` nel contenitore di inserimento di dipendenza Autofac e restituisce un riferimento a essa, prima di richiamare il `InitializeAsync` metodo.

> [!NOTE]
> Quando il `INavigationService` interfaccia viene risolto mediante la `ViewModelBase` (classe), il contenitore restituisce un riferimento al `NavigationService` oggetto creato quando viene richiamato il metodo InitNavigation.

Nell'esempio di codice riportato di seguito viene illustrato il `NavigationService` `InitializeAsync` metodo:

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

Il `MainView` viene esplorato se l'applicazione dispone di un token di accesso memorizzato nella cache, viene utilizzato per l'autenticazione. In caso contrario, il `LoginView` si accede.

Per ulteriori informazioni sul contenitore Autofac dipendenza attacco intrusivo nel codice, vedere [introduzione Dependency Injection](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>Passaggio di parametri durante la navigazione

Uno del `NavigateToAsync` metodi, specificati da di `INavigationService` interfaccia, i dati di spostamento consente di specificare come argomento passato al modello di visualizzazione si sta spostando, in cui è in genere utilizzato per eseguire l'inizializzazione.

Ad esempio, il `ProfileViewModel` classe contiene un `OrderDetailCommand` che viene eseguita quando l'utente seleziona un ordine sul `ProfileView` pagina. A sua volta, si esegue il `OrderDetailAsync` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

Questo metodo richiama navigazione per il `OrderDetailViewModel`, passando un `Order` istanza che rappresenta l'ordine in cui l'utente selezionato nel `ProfileView` pagina. Quando il `NavigationService` classe crea il `OrderDetailView`, `OrderDetailViewModel` classe viene creata un'istanza e assegnata alla visualizzazione [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/). Dopo l'accesso al `OrderDetailView`, `InternalNavigateToAsync` il metodo viene eseguito il `InitializeAsync` associato della visualizzazione del modello di visualizzazione.

Il `InitializeAsync` metodo è definito nella `ViewModelBase` classe come un metodo che può essere sottoposto a override. Questo metodo specifica un `object` argomento che rappresenta i dati da passare a un modello di visualizzazione durante un'operazione di spostamento. Pertanto, di fornire la propria implementazione di classi modello di visualizzazione che desidera ricevere i dati da un'operazione di navigazione di `InitializeAsync` metodo per eseguire l'inizializzazione necessaria. Nell'esempio di codice riportato di seguito viene illustrato il `InitializeAsync` metodo la `OrderDetailViewModel` classe:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    if (navigationData is Order)  
    {  
        ...  
        Order = await _ordersService.GetOrderAsync(  
                        Convert.ToInt32(order.OrderNumber), authToken);  
        ...  
    }  
}
```

Questo metodo recupera il `Order` Dettagli istanza che è stato passato il modello di visualizzazione durante l'operazione di spostamento e lo utilizza per recuperare l'ordine completo dal `OrderService` istanza.

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>Chiamata di navigazione usando i comportamenti

Navigazione in genere viene attivata da una vista tramite un'interazione dell'utente. Ad esempio, il `LoginView` esegue navigazione dopo l'autenticazione. Esempio di codice seguente viene illustrato come viene richiamata la navigazione da un comportamento:

```xaml
<WebView ...>  
    <WebView.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="Navigating"  
            EventArgsConverter="{StaticResource WebNavigatingEventArgsConverter}"  
            Command="{Binding NavigateCommand}" />  
    </WebView.Behaviors>  
</WebView>
```

In fase di esecuzione, il `EventToCommandBehavior` risponderà l'interazione con il [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/). Quando il `WebView` passa a una pagina web, il [ `Navigating` ](https://developer.xamarin.com/api/event/Xamarin.Forms.WebView.Navigating/) viene generato l'evento, che eseguirà il `NavigateCommand` nel `LoginViewModel`. Per impostazione predefinita, gli argomenti dell'evento per l'evento vengono passati al comando. Questi dati viene convertiti come viene passato tra origine e di destinazione dal convertitore specificato nella `EventArgsConverter` proprietà, che restituisce il [ `Url` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebNavigationEventArgs.Url/) dal [ `WebNavigatingEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebNavigatingEventArgs/). Pertanto, quando il `NavigationCommand` viene eseguita, l'Url della pagina web viene passato come parametro al registrato `Action`.

A sua volta, il `NavigationCommand` esegue il `NavigateAsync` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

Questo metodo richiama navigazione per il `MainViewModel`, e dopo una navigazione, rimuove il `LoginView` pagina dallo stack di navigazione.

### <a name="confirming-or-cancelling-navigation"></a>Conferma o annullamento di navigazione

Un'app potrebbe essere necessario interagire con l'utente durante un'operazione di spostamento, in modo che all'utente di confermare o annullare lo spostamento. Questo potrebbe essere necessario, ad esempio, quando l'utente tenta di passare prima completamente dopo aver completato una pagina di immissione dati. In questo caso, un'applicazione deve fornire una notifica che consente all'utente per uscire dalla pagina o di annullare l'operazione di spostamento prima che si verifichi. Ciò può essere ottenuto in una classe di modello di visualizzazione utilizzando la risposta da una notifica per controllare o meno lo spostamento viene effettuato.

## <a name="summary"></a>Riepilogo

Xamarin. Forms include il supporto per lo spostamento di pagina, che comporta in genere dall'interazione dell'utente con l'interfaccia utente o dall'app, in seguito alle modifiche di stato interno basata sulla logica. Tuttavia, navigazione può essere complessa da implementare nelle App che usano il modello MVVM.

In questo capitolo presentato un `NavigationService` (classe), che viene utilizzato per eseguire sulla navigazione nella visualizzazione modello prima dalla visualizzazione di modelli. Inserire la logica di navigazione nella visualizzazione classi del modello indica che la logica può essere esercitata tramite test automatizzati. Inoltre, il modello di visualizzazione possa quindi implementare la logica per navigazione del controllo per assicurarsi che vengono applicate alcune regole di business.


## <a name="related-links"></a>Collegamenti correlati

- [Scaricare eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
