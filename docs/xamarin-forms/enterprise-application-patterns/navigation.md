---
title: Esplorazione di app aziendali
description: Questo capitolo illustra il modo in cui l'app per dispositivi mobili eShopOnContainers esegue la navigazione del primo modello di visualizzazione dai modelli di visualizzazione.
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0f523c7149366cff85164f26f3f47b87801002cb
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305255"
---
# <a name="enterprise-app-navigation"></a>Esplorazione di app aziendali

Novell. Forms include il supporto per la navigazione tra le pagine, che in genere risulta dall'interazione dell'utente con l'interfaccia utente o dall'app stessa in seguito a modifiche dello stato basato su logica interna. Tuttavia, la navigazione può essere complessa da implementare nelle app che usano il modello MVC (Model-View-ViewModel), in quanto devono essere soddisfatte le seguenti difficoltà:

- Come identificare la vista a cui spostarsi, usando un approccio che non introduce un accoppiamento stretto e dipendenze tra le visualizzazioni.
- Viene creata un'istanza e viene inizializzata la modalità di coordinamento del processo in base al quale è possibile spostarsi nella visualizzazione. Quando si usa MVVM, è necessario creare un'istanza del modello di visualizzazione e visualizzazione e associarlo tra loro tramite il contesto di associazione della visualizzazione. Quando un'app usa un contenitore di inserimento delle dipendenze, la creazione di istanze di viste e modelli di visualizzazione potrebbe richiedere un meccanismo di costruzione specifico.
- Indica se eseguire la navigazione prima o dopo la visualizzazione del primo modello. Con la navigazione di visualizzazione-prima, la pagina in cui spostarsi si riferisce al nome del tipo di visualizzazione. Durante lo spostamento, viene creata un'istanza della visualizzazione specificata, insieme al modello di visualizzazione corrispondente e ad altri servizi dipendenti. Un approccio alternativo consiste nell'utilizzare la navigazione Visualizza prima modello, in cui la pagina a cui spostarsi fa riferimento al nome del tipo di modello di visualizzazione.
- Come separare in modo semplice il comportamento di spostamento dell'app tra le visualizzazioni e i modelli di visualizzazione. Il modello MVVM fornisce una separazione tra l'interfaccia utente dell'app e la relativa presentazione e la logica di business. Tuttavia, il comportamento di navigazione di un'app spesso si estende sull'interfaccia utente e le parti di presentazione dell'app. L'utente avvierà spesso la navigazione da una vista e la visualizzazione verrà sostituita come risultato della navigazione. Tuttavia, la navigazione potrebbe spesso dover essere avviata o coordinata dall'interno del modello di visualizzazione.
- Come passare i parametri durante la navigazione a scopo di inizializzazione. Ad esempio, se l'utente passa a una visualizzazione per aggiornare i dettagli dell'ordine, i dati dell'ordine dovranno essere passati alla visualizzazione in modo che sia in grado di visualizzare i dati corretti.
- Come coordinare la navigazione, per assicurarsi che vengano rispettate determinate regole business. Ad esempio, è possibile che venga richiesto agli utenti prima di uscire da una vista, in modo che possano correggere eventuali dati non validi o richiedere l'invio o l'eliminazione di eventuali modifiche apportate ai dati nella vista.

Questo capitolo affronta questi problemi presentando una classe `NavigationService` utilizzata per eseguire la navigazione della pagina del modello di visualizzazione.

> [!NOTE]
> Il `NavigationService` usato dall'app è progettato solo per eseguire la navigazione gerarchica tra le istanze di ContentPage. L'uso del servizio per spostarsi tra gli altri tipi di pagina potrebbe causare un comportamento imprevisto.

## <a name="navigating-between-pages"></a>Spostamento tra le pagine

La logica di navigazione può risiedere nel code-behind di una vista o in un modello di visualizzazione con associazione a dati. Sebbene la logica di spostamento in una visualizzazione possa essere l'approccio più semplice, non è facile da verificare tramite unit test. L'inserimento della logica di navigazione nelle classi del modello di visualizzazione significa che la logica può essere esercitata tramite unit test. Inoltre, il modello di visualizzazione può implementare la logica per controllare la navigazione per assicurarsi che vengano applicate determinate regole business. Ad esempio, un'app potrebbe non consentire all'utente di uscire da una pagina senza prima verificare che i dati immessi siano validi.

Una classe `NavigationService` viene in genere richiamata dai modelli di visualizzazione per promuovere la testabilità. Tuttavia, se si passa alle viste dai modelli di visualizzazione, è necessario che i modelli di visualizzazione facciano riferimento alle viste e, in particolare, le visualizzazioni a cui il modello di visualizzazione attivo non è associato, operazione sconsigliata. Pertanto, il `NavigationService` presentato qui specifica il tipo di modello di visualizzazione come destinazione a cui passare.

L'app per dispositivi mobili eShopOnContainers usa la classe `NavigationService` per fornire la navigazione del primo modello di visualizzazione. Questa classe implementa l'interfaccia `INavigationService`, illustrata nell'esempio di codice seguente:

```csharp
public interface INavigationService  
{  
    ViewModelBase PreviousPageViewModel { get; }  
    Task InitializeAsync();  
    Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase;  
    Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase;  
    Task RemoveLastFromBackStackAsync();  
    Task RemoveBackStackAsync();  
}
```

Questa interfaccia specifica che una classe di implementazione deve fornire i metodi seguenti:

|Metodo|Scopo|
|--- |--- |
|`InitializeAsync`|Esegue lo spostamento a una delle due pagine quando viene avviata l'app.|
|`NavigateToAsync`|Esegue la navigazione gerarchica in una pagina specificata.|
|`NavigateToAsync(parameter)`|Esegue la navigazione gerarchica in una pagina specificata, passando un parametro.|
|`RemoveLastFromBackStackAsync`|Rimuove la pagina precedente dallo stack di navigazione.|
|`RemoveBackStackAsync`|Rimuove tutte le pagine precedenti dallo stack di navigazione.|

Inoltre, l'interfaccia `INavigationService` specifica che una classe di implementazione deve fornire una proprietà `PreviousPageViewModel`. Questa proprietà restituisce il tipo di modello di visualizzazione associato alla pagina precedente nello stack di navigazione.

> [!NOTE]
> Un'interfaccia `INavigationService` in genere specifica anche un metodo di `GoBackAsync`, che viene usato per tornare a livello di codice alla pagina precedente nello stack di navigazione. Tuttavia, questo metodo non è presente nell'app per dispositivi mobili eShopOnContainers perché non è obbligatorio.

### <a name="creating-the-navigationservice-instance"></a>Creazione dell'istanza NavigationService

La classe `NavigationService`, che implementa l'interfaccia `INavigationService`, è registrata come singleton con il contenitore di inserimento delle dipendenze di Autofac, come illustrato nell'esempio di codice seguente:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

L'interfaccia `INavigationService` viene risolta nel costruttore della classe `ViewModelBase`, come illustrato nell'esempio di codice seguente:

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

Restituisce un riferimento all'oggetto `NavigationService` archiviato nel contenitore di inserimento delle dipendenze Autofac, creato dal metodo `InitNavigation` nella classe `App`. Per altre informazioni, vedere [navigazione all'avvio dell'app](#navigating_when_the_app_is_launched).

La classe `ViewModelBase` archivia l'istanza di `NavigationService` in una proprietà `NavigationService` di tipo `INavigationService`. Pertanto, tutte le classi del modello di visualizzazione, che derivano dalla classe `ViewModelBase`, possono utilizzare la proprietà `NavigationService` per accedere ai metodi specificati dall'interfaccia `INavigationService`. In questo modo si evita di dover inserire l'oggetto `NavigationService` dal contenitore di inserimento delle dipendenze Autofac in ogni classe del modello di visualizzazione.

### <a name="handling-navigation-requests"></a>Gestione delle richieste di navigazione

Novell. Forms fornisce la classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , che implementa un'esperienza di navigazione gerarchica in cui l'utente è in grado di spostarsi tra le pagine, avanti e indietro, a seconda delle esigenze. Per altre informazioni sulla navigazione gerarchica, vedere [Navigazione gerarchica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

Invece di usare direttamente la classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , l'app eShopOnContainers esegue il wrapping della classe `NavigationPage` nella classe `CustomNavigationView`, come illustrato nell'esempio di codice seguente:

```csharp
public partial class CustomNavigationView : NavigationPage  
{  
    public CustomNavigationView() : base()  
    {  
        InitializeComponent();  
    }  

    public CustomNavigationView(Page root) : base(root)  
    {  
        InitializeComponent();  
    }  
}
```

Lo scopo di questo wrapping è semplificare lo stile dell'istanza di [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) all'interno del file XAML per la classe.

Lo spostamento viene eseguito all'interno delle classi del modello di visualizzazione richiamando uno dei metodi `NavigateToAsync`, specificando il tipo di modello di visualizzazione per la pagina a cui si sta eseguendo lo spostamento, come illustrato nell'esempio di codice seguente:

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

Nell'esempio di codice riportato di seguito vengono illustrati i metodi `NavigateToAsync` forniti dalla classe `NavigationService`:

```csharp
public Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), null);  
}  

public Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), parameter);  
}
```

Ogni metodo consente a qualsiasi classe del modello di visualizzazione che deriva dalla classe `ViewModelBase` per eseguire la navigazione gerarchica richiamando il metodo `InternalNavigateToAsync`. Il secondo `NavigateToAsync` metodo consente inoltre di specificare i dati di navigazione come un argomento passato al modello di visualizzazione a cui si accede, in cui viene in genere usato per eseguire l'inizializzazione. Per ulteriori informazioni, vedere [passaggio di parametri durante la navigazione](#passing_parameters_during_navigation).

Il metodo `InternalNavigateToAsync` esegue la richiesta di navigazione e viene illustrato nell'esempio di codice seguente:

```csharp
private async Task InternalNavigateToAsync(Type viewModelType, object parameter)  
{  
    Page page = CreatePage(viewModelType, parameter);  

    if (page is LoginView)  
    {  
        Application.Current.MainPage = new CustomNavigationView(page);  
    }  
    else  
    {  
        var navigationPage = Application.Current.MainPage as CustomNavigationView;  
        if (navigationPage != null)  
        {  
            await navigationPage.PushAsync(page);  
        }  
        else  
        {  
            Application.Current.MainPage = new CustomNavigationView(page);  
        }  
    }  

    await (page.BindingContext as ViewModelBase).InitializeAsync(parameter);  
}  

private Type GetPageTypeForViewModel(Type viewModelType)  
{  
    var viewName = viewModelType.FullName.Replace("Model", string.Empty);  
    var viewModelAssemblyName = viewModelType.GetTypeInfo().Assembly.FullName;  
    var viewAssemblyName = string.Format(  
                CultureInfo.InvariantCulture, "{0}, {1}", viewName, viewModelAssemblyName);  
    var viewType = Type.GetType(viewAssemblyName);  
    return viewType;  
}  

private Page CreatePage(Type viewModelType, object parameter)  
{  
    Type pageType = GetPageTypeForViewModel(viewModelType);  
    if (pageType == null)  
    {  
        throw new Exception($"Cannot locate page type for {viewModelType}");  
    }  

    Page page = Activator.CreateInstance(pageType) as Page;  
    return page;  
}
```

Il metodo `InternalNavigateToAsync` esegue la navigazione a un modello di visualizzazione chiamando prima il metodo `CreatePage`. Questo metodo individua la visualizzazione che corrisponde al tipo di modello di visualizzazione specificato e crea e restituisce un'istanza di questo tipo di visualizzazione. L'individuazione della vista che corrisponde al tipo di modello di visualizzazione usa un approccio basato sulle convenzioni, che presuppone quanto segue:

- Le visualizzazioni si trovano nello stesso assembly dei tipi di modello di visualizzazione.
- Le visualizzazioni si trovano in un. Spazio dei nomi figlio views.
- I modelli di visualizzazione si trovano in un. Spazio dei nomi figlio ViewModels.
- I nomi delle viste corrispondono ai nomi dei modelli di visualizzazione, con la rimozione di "Model".

Quando viene creata un'istanza di una vista, questa viene associata al modello di visualizzazione corrispondente. Per ulteriori informazioni su questo problema, vedere [creazione automatica di un modello di visualizzazione con un localizzatore del modello di visualizzazione](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

Se la vista creata è una `LoginView`, viene incapsulata all'interno di una nuova istanza della classe `CustomNavigationView` e assegnata alla proprietà [`Application.Current.MainPage`](xref:Xamarin.Forms.Application.MainPage) . In caso contrario, viene recuperata l'istanza `CustomNavigationView` e, a condizione che non sia null, viene richiamato il metodo [`PushAsync`](xref:Xamarin.Forms.NavigationPage) per eseguire il push della visualizzazione creata nello stack di navigazione. Tuttavia, se l'istanza di `CustomNavigationView` recuperata è `null`, la vista creata viene incapsulata all'interno di una nuova istanza della classe `CustomNavigationView` e assegnata alla proprietà `Application.Current.MainPage`. Questo meccanismo garantisce che durante lo spostamento le pagine vengano aggiunte correttamente allo stack di navigazione quando è vuota e quando contiene dati.

> [!TIP]
> Si consiglia di memorizzare nella cache le pagine. La memorizzazione nella cache della pagina produce un consumo di memoria per le visualizzazioni che non sono attualmente visualizzate. Tuttavia, senza caching della pagina, significa che l'analisi e la costruzione di XAML della pagina e del relativo modello di visualizzazione si verificheranno ogni volta che viene spostata una nuova pagina, che può avere un effetto sulle prestazioni per una pagina complessa. Per una pagina ben progettata che non usa un numero eccessivo di controlli, le prestazioni dovrebbero essere sufficienti. Tuttavia, la memorizzazione nella cache delle pagine può essere utile se vengono rilevati tempi di caricamento della pagina lenti.

Una volta creata e spostata la vista, viene eseguito il metodo `InitializeAsync` del modello di visualizzazione associato della visualizzazione. Per ulteriori informazioni, vedere [passaggio di parametri durante la navigazione](#passing_parameters_during_navigation).

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>Spostamento all'avvio dell'app

Quando viene avviata l'app, viene richiamato il metodo `InitNavigation` nella classe `App`. L'esempio di codice seguente illustra il metodo:

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

Il metodo crea un nuovo oggetto `NavigationService` nel contenitore di inserimento delle dipendenze Autofac e restituisce un riferimento a esso, prima di richiamare il relativo metodo `InitializeAsync`.

> [!NOTE]
> Quando l'interfaccia `INavigationService` viene risolta dalla classe `ViewModelBase`, il contenitore restituisce un riferimento all'oggetto `NavigationService` creato quando viene richiamato il metodo InitNavigation.

Nell'esempio di codice riportato di seguito viene illustrato il metodo di `InitializeAsync` `NavigationService`:

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

Viene visualizzata la `MainView` se l'app dispone di un token di accesso memorizzato nella cache, che viene usato per l'autenticazione. In caso contrario, la `LoginView` viene spostata a.

Per altre informazioni sul contenitore di inserimento delle dipendenze di Autofac, vedere [Introduzione all'inserimento delle dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>Passaggio di parametri durante la navigazione

Uno dei metodi di `NavigateToAsync`, specificato dall'interfaccia `INavigationService`, consente di specificare i dati di navigazione come un argomento passato al modello di visualizzazione a cui si accede, in cui viene in genere usato per eseguire l'inizializzazione.

Ad esempio, la classe `ProfileViewModel` contiene una `OrderDetailCommand` eseguita quando l'utente seleziona un ordine nella pagina `ProfileView`. A sua volta, viene eseguito il metodo `OrderDetailAsync`, illustrato nell'esempio di codice seguente:

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

Questo metodo richiama la navigazione alla `OrderDetailViewModel`, passando un'istanza `Order` che rappresenta l'ordine selezionato dall'utente nella pagina `ProfileView`. Quando la classe `NavigationService` crea il `OrderDetailView`, viene creata un'istanza della classe `OrderDetailViewModel` e viene assegnata alla [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)della visualizzazione. Dopo la navigazione all'`OrderDetailView`, il metodo `InternalNavigateToAsync` esegue il `InitializeAsync` metodo del modello di visualizzazione associato della visualizzazione.

Il metodo `InitializeAsync` viene definito nella classe `ViewModelBase` come metodo di cui è possibile eseguire l'override. Questo metodo specifica un argomento `object` che rappresenta i dati da passare a un modello di visualizzazione durante un'operazione di navigazione. Pertanto, le classi di modelli di visualizzazione che desiderano ricevere dati da un'operazione di spostamento forniscono la propria implementazione del metodo `InitializeAsync` per eseguire l'inizializzazione richiesta. Nell'esempio di codice seguente viene illustrato il metodo `InitializeAsync` dalla classe `OrderDetailViewModel`:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    if (navigationData is Order)  
    {  
        ...  
        Order = await _ordersService.GetOrderAsync(  
                        Convert.ToInt32(order.OrderNumber), authToken);  
        ...  
    }  
}
```

Questo metodo recupera l'istanza di `Order` passata al modello di visualizzazione durante l'operazione di navigazione e la utilizza per recuperare i dettagli dell'ordine completo dall'istanza di `OrderService`.

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>Richiamo della navigazione mediante i comportamenti

La navigazione viene in genere attivata da una vista da un'interazione dell'utente. Ad esempio, il `LoginView` esegue la navigazione in seguito all'autenticazione riuscita. Nell'esempio di codice seguente viene illustrato come la navigazione viene richiamata da un comportamento:

```xaml
<WebView ...>  
    <WebView.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="Navigating"  
            EventArgsConverter="{StaticResource WebNavigatingEventArgsConverter}"  
            Command="{Binding NavigateCommand}" />  
    </WebView.Behaviors>  
</WebView>
```

In fase di esecuzione, il `EventToCommandBehavior` risponderà all'interazione con l' [`WebView`](xref:Xamarin.Forms.WebView). Quando il `WebView` passa a una pagina Web, viene attivato l'evento [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) , che eseguirà il `NavigateCommand` nel `LoginViewModel`. Per impostazione predefinita, gli argomenti dell'evento per l'evento vengono passati al comando. Questi dati vengono convertiti come passati tra l'origine e la destinazione dal convertitore specificato nella proprietà `EventArgsConverter`, che restituisce l' [`Url`](xref:Xamarin.Forms.WebNavigationEventArgs.Url) dal [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs). Pertanto, quando viene eseguita la `NavigationCommand`, l'URL della pagina Web viene passato come parametro al `Action`registrato.

Il `NavigationCommand` esegue a sua volta il metodo `NavigateAsync`, illustrato nell'esempio di codice seguente:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

Questo metodo richiama la navigazione all'`MainViewModel`e la navigazione seguente rimuove la pagina `LoginView` dallo stack di navigazione.

### <a name="confirming-or-cancelling-navigation"></a>Conferma o annullamento della navigazione

Un'app potrebbe dover interagire con l'utente durante un'operazione di navigazione, in modo che l'utente possa confermare o annullare la navigazione. Questo potrebbe essere necessario, ad esempio, quando l'utente tenta di spostarsi prima di completare una pagina di immissione dati. In questa situazione, un'app deve fornire una notifica che consenta all'utente di uscire dalla pagina o di annullare l'operazione di spostamento prima che si verifichi. Questa operazione può essere eseguita in una classe modello di visualizzazione utilizzando la risposta di una notifica per controllare se la navigazione viene richiamata o meno.

## <a name="summary"></a>Riepilogo

Novell. Forms include il supporto per la navigazione tra le pagine, che in genere risulta dall'interazione dell'utente con l'interfaccia utente o dall'app stessa, in seguito a modifiche dello stato guidate dalla logica interna. Tuttavia, la navigazione può essere complessa da implementare nelle app che usano il modello MVVM.

In questo capitolo è stata presentata una classe `NavigationService`, che viene utilizzata per eseguire la navigazione del primo modello di visualizzazione dai modelli di visualizzazione. L'inserimento della logica di navigazione nelle classi del modello di visualizzazione significa che la logica può essere esercitata tramite test automatizzati. Inoltre, il modello di visualizzazione può implementare la logica per controllare la navigazione per assicurarsi che vengano applicate determinate regole business.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (esempio)](https://github.com/dotnet-architecture/eShopOnContainers)
