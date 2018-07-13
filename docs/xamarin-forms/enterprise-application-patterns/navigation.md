---
title: Spostamento di App aziendali
description: In questo capitolo viene illustrato come l'app per dispositivi mobili di eShopOnContainers esegue view model-first navigazione dalla visualizzazione di modelli.
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d306b0c1c0d08129671e27b96911ec771acb658e
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994770"
---
# <a name="enterprise-app-navigation"></a>Spostamento di App aziendali

Xamarin. Forms include il supporto per la navigazione tra le pagine, che in genere i risultati dall'interazione dell'utente con l'interfaccia utente o dall'app stessa in seguito a modifiche di stato interno basato su logica. Tuttavia, navigazione può essere complessa da implementare nelle App che usano il modello Model-View-ViewModel (MVVM), come devono essere soddisfatte le seguenti sfide:

-   Come identificare la vista per essere esplorato, usando un approccio che non introduce un accoppiamento rigido e le dipendenze tra le visualizzazioni.
-   Come coordinare il processo mediante il quale la visualizzazione a reindirizzato viene creata un'istanza e inizializzata. Quando si usa MVVM, la visualizzazione e il modello di visualizzazione necessario creare istanze e associati tra loro tramite il contesto di associazione della visualizzazione. Quando un'app Usa un contenitore di inserimento delle dipendenze, la creazione di istanze di viste e i modelli di visualizzazione potrebbe richiedere un meccanismo di formulazione specifica.
-   Se si desidera eseguire navigazione visualizzazione-first o visualizzare navigazione model-first. Con la navigazione visualizzazione-first, la pagina per passare a fa riferimento al nome del tipo di visualizzazione. Durante la navigazione, viene creata la visualizzazione specificata, con il corrispondente modello di visualizzazione e altri servizi dipendenti. Un approccio alternativo consiste nell'utilizzare la visualizzazione model-first, in cui la pagina per passare a fa riferimento al nome del tipo di modello di visualizzazione.
-   Come per correttamente separare il comportamento dell'app per la navigazione tra le visualizzazioni e i modelli di visualizzazione. Il modello MVVM fornisce una separazione tra interfaccia utente dell'app e la sua presentazione e logica di business. Tuttavia, il comportamento di navigazione di un'app sarà spesso si estendono le parti dell'interfaccia utente e le presentazioni dell'app. L'utente sarà spesso avviare la navigazione da una visualizzazione e la visualizzazione verrà sostituita in seguito a riquadro di spostamento. Tuttavia, navigazione potrebbe essere necessario spesso anche essere avviata o coordinato all'interno del modello di visualizzazione.
-   Come passare i parametri durante la navigazione per l'inizializzazione. Ad esempio, se l'utente passa a una vista per aggiornare i dettagli dell'ordine, i dati dell'ordine dovrà essere passati alla visualizzazione in modo che sia possibile visualizzare i dati corretti.
-   Modalità di navigazione di coordinata, per garantire che determinate regole di business vengono osservate. Ad esempio, agli utenti potrebbero essere richiesto prima di uscire da una vista in modo che è possibile correggere eventuali dati non validi o essere richiesto di inviare o annullare le modifiche dei dati che sono state apportate all'interno della visualizzazione.

In questo capitolo affronta queste sfide, presentando un `NavigationService` classe che viene usato per eseguire la visualizzazione pagina model-first.

> [!NOTE]
> Il `NavigationService` usato per l'app è progettata unicamente per eseguire la navigazione gerarchica tra le istanze di ContentPage. Uso del servizio per spostarsi tra gli altri tipi di pagina potrebbe causare comportamenti imprevisti.

## <a name="navigating-between-pages"></a>Spostamento tra le pagine

Per la logica di navigazione può risiedere nel code-behind di una vista o nei dati di un modello di visualizzazione associata. Inserire la logica di navigazione in una vista potrebbe essere l'approccio più semplice, ma non facilmente testabile tramite unit test. Inserire la logica di navigazione nella visualizzazione di classi del modello significa che la logica può essere usata tramite unit test. Inoltre, il modello di visualizzazione possa quindi implementare la logica per controllare il passaggio per garantire che vengano applicate alcune regole di business. Ad esempio, un'app potrebbe non consentire all'utente esce dalla pagina senza prima verificare che i dati immessi siano validi.

Oggetto `NavigationService` classe in genere viene richiamata dalla visualizzazione di modelli, per promuovere di testabilità. Tuttavia, passare ad visualizzazioni dai modelli di visualizzazione richiederebbe la visualizzazione di modelli fanno riferimento a viste, e in particolare le viste che il modello di visualizzazione attiva non è associato, che non è consigliata. Pertanto, il `NavigationService` presentato in questo caso specifica il tipo di modello di visualizzazione come destinazione a cui passare.

L'app per dispositivi mobili di eShopOnContainers Usa i `NavigationService` classe per la consultazione view model-first. Questa classe implementa il `INavigationService` interfaccia, che viene visualizzato nell'esempio di codice seguente:

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

Questa interfaccia specifica che una classe di implementazione deve fornire i metodi seguenti:

|Metodo|Scopo|
|--- |--- |
|`InitializeAsync`|Eseguire la navigazione a una delle due pagine quando viene avviata l'app.|
|`NavigateToAsync`|Esegue la navigazione gerarchica in una pagina specificata.|
|`NavigateToAsync(parameter)`|Esegue la navigazione gerarchica in una pagina specificata, passando un parametro.|
|`RemoveLastFromBackStackAsync`|Rimuove lo stack di navigazione alla pagina precedente.|
|`RemoveBackStackAsync`|Rimuove tutte le pagine precedenti dallo stack di navigazione.|

Inoltre, il `INavigationService` interfaccia specifica che una classe di implementazione deve fornire un `PreviousPageViewModel` proprietà. Questa proprietà restituisce il tipo di modello di visualizzazione associato alla pagina precedente nello stack di navigazione.

> [!NOTE]
> Un' `INavigationService` interfaccia sarebbe in genere specificare anche un `GoBackAsync` metodo, che consente di restituire a livello di codice alla pagina precedente nello stack di navigazione. Tuttavia, questo metodo è disponibile l'app per dispositivi mobili di eShopOnContainers perché non è obbligatorio.

### <a name="creating-the-navigationservice-instance"></a>Creazione dell'istanza NavigationService

Il `NavigationService` classe che implementa il `INavigationService` interfaccia, viene registrato come un singleton con il contenitore di inserimento delle dipendenze di Autofac, come illustrato nell'esempio di codice seguente:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

Il `INavigationService` interfaccia è stato risolto nel `ViewModelBase` costruttore della classe, come illustrato nell'esempio di codice seguente:

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

Restituisce un riferimento al `NavigationService` oggetto archiviato nel contenitore di inserimento delle dipendenze Autofac, che viene creato tramite il `InitNavigation` metodo nel `App` classe. Per altre informazioni, vedere [esplorazione quando l'App viene avviata](#navigating_when_the_app_is_launched).

Il `ViewModelBase` classe archivia i `NavigationService` dell'istanza in un `NavigationService` proprietà, di tipo `INavigationService`. Pertanto, in tutte le visualizzazione classi del modello, che ne derivano il `ViewModelBase` classe, può usare il `NavigationService` per accedere ai metodi specificati dalla proprietà il `INavigationService` interfaccia. Questo modo si evita il sovraccarico dell'inserimento di `NavigationService` oggetto dal contenitore Autofac dependency injection in ogni classe di modello di visualizzazione.

### <a name="handling-navigation-requests"></a>La gestione delle richieste di navigazione

Xamarin. Forms fornisce il [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) classe che implementa un'esperienza di navigazione gerarchica in cui l'utente è in grado di scorrere le pagine avanti e indietro in base alle esigenze. Per altre informazioni sulla navigazione gerarchica, vedere [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

Invece di usare la [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) classe direttamente, esegue il wrapping di app eShopOnContainers il `NavigationPage` classe la `CustomNavigationView` classe, come illustrato nell'esempio di codice seguente:

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

Lo scopo di questa disposizione è per facilitare l'applicazione di stili di [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) istanza all'interno del file XAML per la classe.

Navigazione viene eseguita all'interno delle classi di modello di visualizzazione chiamando uno del `NavigateToAsync` metodi, che specifica il tipo di modello di visualizzazione per la pagina di cui si accede a, come illustrato nell'esempio di codice seguente:

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

Nell'esempio di codice riportato di seguito viene illustrato il `NavigateToAsync` metodi forniti dal `NavigationService` classe:

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

Ogni metodo consente a qualsiasi classe di modello di visualizzazione da cui deriva il `ViewModelBase` classe per eseguire lo spostamento gerarchico richiamando il `InternalNavigateToAsync` (metodo). Inoltre, il secondo `NavigateToAsync` metodo consente di specificare come argomento passato al modello di visualizzazione a cui si accede, dove in genere viene usata per eseguire l'inizializzazione dei dati di navigazione. Per altre informazioni, vedere [passando i parametri durante la navigazione](#passing_parameters_during_navigation).

Il `InternalNavigateToAsync` metodo esegue la richiesta di navigazione e viene illustrato nell'esempio di codice seguente:

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

Il `InternalNavigateToAsync` metodo esegue la navigazione a un modello di visualizzazione da chiamare prima il `CreatePage` (metodo). Questo metodo individua la visualizzazione che corrisponde al tipo di modello di visualizzazione specificato e crea e restituisce un'istanza di questo tipo di visualizzazione. La visualizzazione che corrisponde al tipo di modello di vista di individuazione viene utilizzato un approccio basato su convenzioni, in cui si presuppone che:

-   Le visualizzazioni sono nello stesso assembly come tipi di modello di visualizzazione.
-   Le visualizzazioni sono una. Spazio dei nomi di visualizzazioni figlio.
-   Visualizzazione di modelli è in una. Spazio dei nomi figlio ViewModel.
-   Visualizzare i nomi corrispondono per visualizzare i nomi di modello, con "Modello" rimosso.

Quando viene creata un'istanza di una vista, questi vengono associati il corrispondente modello di visualizzazione. Per altre informazioni sul modo in cui ciò si verifica, vedere [creazione automatica di un modello di visualizzazione con un localizzatore di modello di visualizzazione](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Se la visualizzazione da creare è un `LoginView`, vengono incapsulati all'interno di una nuova istanza della `CustomNavigationView` classe e assegnati al [ `Application.Current.MainPage` ](xref:Xamarin.Forms.Application.MainPage) proprietà. In caso contrario, il `CustomNavigationView` istanza viene recuperata e fornito che non è null, il [ `PushAsync` ](xref:Xamarin.Forms.NavigationPage) metodo viene richiamato per eseguire il push la vista viene creata nello stack di navigazione. Tuttavia, se recuperata `CustomNavigationView` istanza è `null`, viene eseguito il wrapping di visualizzazione viene creata all'interno di una nuova istanza del `CustomNavigationView` classe e assegnato al `Application.Current.MainPage` proprietà. Questo meccanismo garantisce che durante la navigazione, vengono aggiunte pagine correttamente allo stack di navigazione sia quando è vuoto e se contiene dati.

> [!TIP]
> Prendere in considerazione la memorizzazione nella cache le pagine. Pagina memorizzazione nella cache i risultati in termini di consumo di memoria per le visualizzazioni che non sono attualmente visualizzati. Tuttavia, senza la memorizzazione nella cache di pagina significa che l'analisi XAML e la costruzione della pagina e il relativo modello di visualizzazione verrà eseguita ogni volta che una nuova pagina viene esplorata, che possono avere un impatto sulle prestazioni per una pagina complessa. Per una pagina ben progettata che non usa un numero eccessivo di controlli, le prestazioni dovrebbero essere sufficienti. Tuttavia, la memorizzazione nella cache di pagina può essere utile se si verificano tempi di caricamento pagina lenti.

Dopo la visualizzazione viene creata e aperto, il `InitializeAsync` metodo del modello di visualizzazione associata della visualizzazione viene eseguito. Per altre informazioni, vedere [passando i parametri durante la navigazione](#passing_parameters_during_navigation).

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>Esplorazione quando l'App viene avviata

Quando l'app viene avviata, il `InitNavigation` nel metodo il `App` classe viene richiamata. Esempio di codice seguente viene illustrato questo metodo:

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

Il metodo crea un nuovo `NavigationService` oggetti nel contenitore Autofac dependency injection e restituisce un riferimento a esso, prima di richiamare il `InitializeAsync` (metodo).

> [!NOTE]
> Quando la `INavigationService` interfaccia viene risolto mediante il `ViewModelBase` (classe), il contenitore restituisce un riferimento al `NavigationService` oggetto creato quando viene richiamato il metodo InitNavigation.

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

Il `MainView` viene esplorato se l'app ha un token di accesso memorizzato nella cache, che viene usato per l'autenticazione. In caso contrario, il `LoginView` si accede.

Per altre informazioni sul contenitore Autofac dependency injection, vedere [Introduzione all'inserimento delle dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>Passaggio di parametri durante la navigazione

Uno dei `NavigateToAsync` metodi, specificati da di `INavigationService` interfaccia, i dati di navigazione consente di specificare come argomento passato al modello di visualizzazione a cui si accede, dove in genere viene usata per eseguire l'inizializzazione.

Ad esempio, il `ProfileViewModel` classe contiene un `OrderDetailCommand` che viene eseguito quando l'utente seleziona un ordine del `ProfileView` pagina. A sua volta, verrà eseguito il `OrderDetailAsync` metodo, che viene visualizzato nell'esempio di codice seguente:

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

Questo metodo richiama navigazione per la `OrderDetailViewModel`, passando un' `Order` istanza che rappresenta l'ordine in cui l'utente ha selezionato nel `ProfileView` pagina. Quando il `NavigationService` classe consente di creare le `OrderDetailView`, il `OrderDetailViewModel` classe viene creata un'istanza e assegnata alla visualizzazione [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). Dopo l'accesso al `OrderDetailView`, il `InternalNavigateToAsync` metodo viene eseguito il `InitializeAsync` associato del metodo della visualizzazione modello di visualizzazione.

Il `InitializeAsync` metodo viene definito nel `ViewModelBase` classe un metodo che può essere sottoposto a override. Questo metodo viene specificato un `object` argomento che rappresenta i dati da passare a un modello di visualizzazione durante un'operazione di navigazione. Pertanto, classi di modello di visualizzazione che desidera ricevere i dati da un'operazione di navigazione forniscono la propria implementazione del `InitializeAsync` metodo per eseguire l'inizializzazione richiesta. Nell'esempio di codice riportato di seguito viene illustrato il `InitializeAsync` metodo dal `OrderDetailViewModel` classe:

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

Questo metodo recupera il `Order` illustra in dettaglio dall'istanza in cui è stato passato nel modello di visualizzazione durante l'operazione di spostamento e lo usa per recuperare l'ordine completa il `OrderService` istanza.

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>Richiamo di navigazione usando i comportamenti

Navigazione viene in genere attivata da una vista da un'interazione utente. Ad esempio, il `LoginView` esegue la navigazione dopo la riuscita dell'autenticazione. Esempio di codice seguente mostra come viene richiamata la navigazione da un comportamento:

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

In fase di esecuzione, il `EventToCommandBehavior` risponderà all'interazione con il [ `WebView` ](xref:Xamarin.Forms.WebView). Quando il `WebView` passa a una pagina web, il [ `Navigating` ](xref:Xamarin.Forms.WebView.Navigating) , quindi, viene generato l'evento cui verrà eseguita la `NavigateCommand` nel `LoginViewModel`. Per impostazione predefinita, gli argomenti dell'evento per l'evento vengono passati al comando. Questi dati viene convertiti come viene passato tra origine e destinazione per il convertitore specificato nella `EventArgsConverter` proprietà, che restituisce il [ `Url` ](xref:Xamarin.Forms.WebNavigationEventArgs.Url) dal [ `WebNavigatingEventArgs` ](xref:Xamarin.Forms.WebNavigatingEventArgs). Pertanto, quando la `NavigationCommand` viene eseguita, l'Url della pagina web viene passato come parametro per l'oggetto registrato `Action`.

A sua volta, il `NavigationCommand` esegue il `NavigateAsync` metodo, che viene visualizzato nell'esempio di codice seguente:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

Questo metodo richiama navigazione per la `MainViewModel`, e seguire navigazione, rimuove il `LoginView` pagina dallo stack di navigazione.

### <a name="confirming-or-cancelling-navigation"></a>Per confermare o annullare spostamento

Un'app potrebbe essere necessario interagire con l'utente durante un'operazione di spostamento, in modo che l'utente possa confermare o annullare lo spostamento. Questo potrebbe essere necessario, ad esempio, quando l'utente tenta di passare prima completamente dopo aver completato una pagina di immissione dati. In questo caso, un'app deve fornire una notifica che consente all'utente per uscire dalla pagina o annullare l'operazione di navigazione prima che venga eseguita. Ciò può essere ottenuto in una classe di modello di visualizzazione usando la risposta da una notifica per controllare o meno lo spostamento viene effettuato.

## <a name="summary"></a>Riepilogo

Xamarin. Forms include il supporto per la navigazione tra le pagine, che in genere comporta dall'interazione dell'utente con l'interfaccia utente o dall'app stessa, in seguito a modifiche di stato interno basato su logica. Tuttavia, navigazione può essere complessa da implementare nelle App che usano il modello MVVM.

In questo capitolo presentato un `NavigationService` (classe), che consente di eseguire view model-first navigazione dalla visualizzazione di modelli. Inserire la logica di navigazione nella visualizzazione di classi del modello significa che la logica può essere usata tramite i test automatizzati. Inoltre, il modello di visualizzazione possa quindi implementare la logica per controllare il passaggio per garantire che vengano applicate alcune regole di business.


## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
