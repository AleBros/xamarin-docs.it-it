---
title: MVVM
ms.topic: article
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: ac8cf026fba8cac565ad622dcba24834a2ea8098
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="mvvm"></a>MVVM

L'esperienza degli sviluppatori di xamarin. Forms in genere implica la creazione di un'interfaccia utente in XAML e quindi aggiungendo code-behind che opera sull'interfaccia utente. Come le app vengono modificate e aumento delle dimensioni e dell'ambito, possono verificarsi dei problemi di manutenzione complessa. Questi problemi includono la stretta integrazione tra i controlli dell'interfaccia utente e la logica di business, che aumenta il costo di apportare le modifiche dell'interfaccia utente e le difficoltà di unit test di tale codice.

Il modello Model-View-ViewModel (MVVM) consente di separare correttamente la logica di business e la presentazione di un'applicazione dalla relativa interfaccia utente (UI). Mantenere una netta separazione tra la logica dell'applicazione e l'interfaccia utente consente di risolvere numerosi problemi di sviluppo e può rendere più facile da testare, gestire e sviluppare un'applicazione. Può migliorare notevolmente anche la possibilità di riutilizzare codice e consente agli sviluppatori e progettisti dell'interfaccia utente più collaborano facilmente quando si sviluppano le rispettive parti di un'app.

## <a name="the-mvvm-pattern"></a>Il modello MVVM

Il modello MVVM sono presenti tre componenti principali: il modello, la visualizzazione e il modello di visualizzazione. Ognuno ha uno scopo specifico. Figura 2-1 mostra le relazioni tra i tre componenti.

![](mvvm-images/mvvm.png "Il modello MVVM")

**Figura 2-1**: modello di MVVM

Oltre a comprendere le responsabilità di ogni componenti, è inoltre importante comprendere come interagiscono tra loro. In generale, la vista "conosce" modello di visualizzazione, il modello di visualizzazione "conosce" il modello, ma il modello non è a conoscenza del modello di visualizzazione e il modello di visualizzazione non è a conoscenza della vista. Pertanto, il modello di visualizzazione isola la visualizzazione del modello e consente il modello evoluzione indipendentemente dalla vista.

Come indicato di seguito sono riportati i vantaggi dell'uso il modello MVVM:

-   Se è presente un'implementazione esistente di modello che incapsula la logica di business esistente, può essere difficile o rischiosa per modificarlo. In questo scenario, il modello di visualizzazione funge da un adattatore per le classi di modello e permette di evitare le importanti modifiche al codice del modello.
-   Gli sviluppatori possono creare unit test per il modello di visualizzazione e il modello, senza utilizzare la vista. Gli unit test per il modello di visualizzazione può esercitare esattamente la stessa funzionalità come quelle utilizzate dalla vista.
-   L'interfaccia utente dell'app può riprogettato senza intervenire sul codice, purché la vista viene implementata completamente in XAML. Pertanto, una nuova versione della vista dovrebbe funzionare con il modello di visualizzazione esistente.
-   Progettisti e sviluppatori possono lavorare in modo indipendente e simultaneamente sui relativi componenti durante il processo di sviluppo. Finestre di progettazione possono concentrarsi sulla visualizzazione, mentre gli sviluppatori possono operare nel modello di visualizzazione e componenti del modello.

Chiave da usare in modo efficace MVVM consiste nel comprendere come il codice di app nelle classi corrette e comprendere l'interagiscono tra le classi. Le sezioni seguenti illustrano le responsabilità di ognuna delle classi nel modello MVVM.

### <a name="view"></a>Visualizza

La visualizzazione è responsabile della definizione della struttura, layout e l'aspetto di ciò che l'utente viene visualizzato sullo schermo. Idealmente, ogni vista è definita in XAML, con un code-behind limitato che non contengono la logica di business. In alcuni casi, tuttavia, il code-behind potrebbe contenere la logica dell'interfaccia utente che implementa il comportamento visivo che è difficile da definire in XAML, ad esempio animazioni.

In un'applicazione di xamarin. Forms, una vista è in genere un [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)-derivato o [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)-classe derivata. Viste possono anche essere rappresentate da un modello di dati, che specifica gli elementi dell'interfaccia utente da utilizzare per rappresentare visivamente un oggetto quando viene visualizzato. Un modello di dati come una vista non dispone di code-behind ed è progettato per associare un tipo di modello di visualizzazione.

> [!TIP]
> Evitare l'abilitazione e disabilitazione di elementi dell'interfaccia utente nel code-behind. Verificare che i modelli di visualizzazione sono responsabili per la definizione di modifiche di stato logico che interessano alcuni aspetti della visualizzazione della vista, ad esempio, se è disponibile un comando o un'indicazione che è in sospeso un'operazione. Pertanto, abilitare e disabilitare gli elementi dell'interfaccia utente mediante l'associazione per visualizzare le proprietà dei modelli, anziché l'abilitazione e disabilitazione nel code-behind.

Esistono diverse opzioni per l'esecuzione del codice del modello di visualizzazione nella risposta interazioni nella vista, ad esempio un pulsante o la selezione di elementi. Se un controllo supporta i comandi, il controllo `Command` proprietà può essere associato a un `ICommand` proprietà nel modello di visualizzazione. Quando viene richiamato il comando del controllo, verrà eseguito il codice nel modello di visualizzazione. Oltre ai comandi, i comportamenti possono essere allegati a un oggetto nella vista e possono restare in attesa dell'evento o un comando da richiamare. In risposta, quindi è possibile richiamare il comportamento di un `ICommand` nel modello di visualizzazione o un metodo per il modello di visualizzazione.

### <a name="viewmodel"></a>ViewModel

Il modello di visualizzazione implementa proprietà e i comandi su cui è possibile associare i dati della visualizzazione e la visualizzazione di tutte le modifiche di stato tramite gli eventi di notifica di modifica di notifica. La funzionalità per l'offerta dall'interfaccia utente di definire le proprietà e i comandi che fornisce il modello di visualizzazione, ma la vista determina come tale funzionalità viene visualizzato.

> [!TIP]
> Mantenere l'interfaccia utente reattiva le operazioni asincrone. App per dispositivi mobili è consigliabile mantenere il thread dell'interfaccia utente non bloccato per migliorare la percezione dell'utente di prestazioni. Pertanto, nel modello di visualizzazione, utilizzare metodi asincroni per le operazioni dei / o e generare eventi per notificare in modo asincrono le visualizzazioni delle modifiche delle proprietà.

Il modello di visualizzazione è inoltre responsabile di coordinare le interazioni della vista con tutte le classi di modello che sono necessari. È in genere una relazione uno-a-molti tra il modello di visualizzazione e le classi di modello. Il modello di visualizzazione è possibile scegliere di esporre le classi di modello direttamente alla vista in modo che i controlli nella visualizzazione è possibile associare dati in modo diretto. Le classi di modello in questo caso, saranno necessario essere progettate per supportare il data binding e gli eventi di notifica di modifica.

Ogni modello di visualizzazione fornisce i dati da un modello in un form che è possibile utilizzare facilmente la vista. A tale scopo, in alcuni casi il modello di visualizzazione esegue la conversione dei dati. Questa conversione dei dati nel modello di visualizzazione consiste nell'inserire una buona idea perché fornisce proprietà che è possibile associare la vista. Ad esempio, il modello di visualizzazione è possibile combinare i valori delle due proprietà per renderne più semplice per la visualizzazione da parte della vista.

> [!TIP]
> Centralizzare le conversioni di dati in un livello di conversione. È anche possibile usare i convertitori di tipi come un livello di conversione di dati separato che si trova tra il modello di visualizzazione e la visualizzazione. Ciò può essere necessario, ad esempio, quando i dati richiedono una formattazione speciale che non fornisce il modello di visualizzazione.

Affinché il modello di visualizzazione deve far parte di data binding bidirezionale con la visualizzazione, è necessario aumentare le proprietà di `PropertyChanged` evento. Visualizzazione di modelli soddisfa questo requisito mediante l'implementazione il `INotifyPropertyChanged` interfaccia e la generazione di `PropertyChanged` evento quando viene modificata una proprietà.

Per le raccolte, la vista semplice `ObservableCollection<T>` viene fornito. Questa raccolta implementa notifica raccolta modificata, lo sviluppatore di dover implementare di sblocco di `INotifyCollectionChanged` interfaccia sulle raccolte.

### <a name="model"></a>Modello

Classi modello sono non visivi che incapsulano i dati dell'applicazione. Pertanto, il modello può essere considerato come modello di dominio dell'applicazione, che in genere include un modello di dati con la logica di business e di convalida. Trasferimento di dati oggetti DTO, Plain Old CLR Object (POCOs) ed entità generate e gli oggetti proxy sono esempi di oggetti del modello.

Classi di modello in genere vengono utilizzate in combinazione con i servizi o i repository che incapsulano l'accesso ai dati e la memorizzazione nella cache.

## <a name="connecting-view-models-to-views"></a>La connessione di visualizzazione di modelli per le visualizzazioni

Visualizzazione di modelli può essere connesso alle viste utilizzando la funzionalità di data binding di xamarin. Forms. Esistono molti approcci che possono essere utilizzati per generare visualizzazioni e visualizzare i modelli e associarli in fase di esecuzione. Questi approcci rientrano in due categorie, note come composizione prima della vista e composizione prima della vista del modello. Scelta tra composizione prima della vista e visualizzazione composizione primo modello è un problema di preferenza e complessità. Tuttavia, tutti gli approcci condividono lo stesso scopo, ovvero per la visualizzazione di un modello di visualizzazione assegnato alla proprietà BindingContext.

Visualizzazione composizione prima l'app è concettualmente composto viste che si connettono ai modelli di visualizzazione che dipendono. Il vantaggio principale di questo approccio è che rende semplice costruire regime di controllo, unità testabili App perché i modelli di visualizzazione non dispongono di alcuna dipendenza delle viste se stessi. È inoltre facile da comprendere la struttura dell'app dopo la relativa struttura visual, anziché dover tenere traccia dell'esecuzione di codice per comprendere come le classi vengono create e associate. Inoltre, costruzione prima vista Allinea con il sistema di navigazione xamarin. Forms che è responsabile della costruzione pagine quando si verifica la navigazione, che rende una composizione prima della vista modello complesso e non allineati con la piattaforma.

Con visualizzazione composizione del primo modello dell'app è concettualmente composta da modelli di visualizzazione, con un servizio responsabile per la visualizzazione per un modello di visualizzazione di individuazione. Composizione prima della vista modello abbia più naturale per alcuni sviluppatori, poiché la creazione di visualizzazione può essere estratto, consentendo di concentrarsi sulla struttura logica non dell'interfaccia utente dell'app. Consente, inoltre, i modelli di visualizzazione da creare con altri modelli di visualizzazione. Tuttavia, questo approccio è spesso complesso e può diventare difficile da comprendere come le varie parti dell'app vengono create e associate.

> [!TIP]
> Mantenere i modelli di visualizzazione e le viste indipendenti. L'associazione di viste da una proprietà in un'origine dati deve essere una dipendenza di entità della vista nel relativo modello di visualizzazione corrispondente. In particolare, non fare riferimento a tipi di viste, ad esempio [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) e [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/), dalla visualizzazione di modelli. Seguendo i principi indicati di seguito, i modelli di visualizzazione possono essere testati in isolamento, riducendo la probabilità di difetti del software, limitando l'ambito.

Le sezioni seguenti illustrano gli approcci principali per la connessione di visualizzazione di modelli per le visualizzazioni.

### <a name="creating-a-view-model-declaratively"></a>Creazione di un modello di visualizzazione in modo dichiarativo

L'approccio più semplice è per la vista da creare in modo dichiarativo il modello di visualizzazione corrispondente in XAML. Quando viene costruita la vista, l'oggetto modello di visualizzazione corrispondente è inoltre possibile creare. Questo approccio è illustrato nell'esempio di codice seguente:

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

Quando il [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) viene creata un'istanza di `LoginViewModel` automaticamente costruito e impostare come la visualizzazione [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/).

Questa costruzione dichiarativa e l'assegnazione del modello di visualizzazione da parte della vista presenta il vantaggio che è semplice, ma ha lo svantaggio che richiede un costruttore (senza parametri) predefinito nel modello di visualizzazione.

### <a name="creating-a-view-model-programmatically"></a>Creazione di un modello di visualizzazione a livello di codice

Una vista può includere codice nel file di codice che restituisce il modello di visualizzazione da assegnare alla relativa [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) proprietà. Questa operazione viene spesso eseguita nel costruttore della visualizzazione, come illustrato nell'esempio di codice seguente:

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

La costruzione a livello di codice e l'assegnazione del modello di visualizzazione all'interno di codice della vista presenta il vantaggio è semplice. Tuttavia, lo svantaggio principale di questo approccio è che la vista deve fornire il modello di visualizzazione con eventuali dipendenze richieste. Utilizzo di un contenitore di inserimento di dipendenza consente di mantenere separati accoppiamento tra la visualizzazione e il modello di visualizzazione. Per ulteriori informazioni, vedere [Dependency Injection](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

### <a name="creating-a-view-defined-as-a-data-template"></a>Creazione di una vista definita come un modello di dati

Una vista può essere definita come un modello di dati e associata a un tipo di modello di visualizzazione. È possibile definire i modelli di dati come risorse, o possono essere definite inline all'interno del controllo che consente di visualizzare il modello di visualizzazione. Il contenuto del controllo è l'istanza del modello di visualizzazione e il modello di dati viene utilizzato per rappresentare visivamente il. Questa tecnica è un esempio di una situazione in cui il modello di visualizzazione viene creata un'istanza prima, seguita dalla creazione della vista.

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>La creazione automatica di un modello di visualizzazione con un indicatore di posizione di visualizzazione del modello

Un indicatore di posizione di visualizzazione del modello è una classe personalizzata che gestisce la creazione di istanze di modelli di visualizzazione e la relativa associazione alle visualizzazioni. Nell'app mobile eShopOnContainers, il `ViewModelLocator` classe dispone di una proprietà associata, `AutoWireViewModel`, che viene utilizzato per associare i modelli di visualizzazione con le visualizzazioni. In XAML della vista, questa proprietà associata è impostata su true per indicare che il modello di visualizzazione deve essere connesso automaticamente alla visualizzazione, come illustrato nell'esempio di codice seguente:

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

Il `AutoWireViewModel` è una proprietà associabile che viene inizializzato su false e quando si modifica il valore di `OnAutoWireViewModelChanged` gestore eventi viene chiamato. Questo metodo consente di risolvere il modello di visualizzazione per la visualizzazione. Esempio di codice seguente viene illustrato come si ottiene questo risultato:

```csharp
private static void OnAutoWireViewModelChanged(BindableObject bindable, object oldValue, object newValue)  
{  
    var view = bindable as Element;  
    if (view == null)  
    {  
        return;  
    }  

    var viewType = view.GetType();  
    var viewName = viewType.FullName.Replace(".Views.", ".ViewModels.");  
    var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;  
    var viewModelName = string.Format(  
        CultureInfo.InvariantCulture, "{0}Model, {1}", viewName, viewAssemblyName);  

    var viewModelType = Type.GetType(viewModelName);  
    if (viewModelType == null)  
    {  
        return;  
    }  
    var viewModel = _container.Resolve(viewModelType);  
    view.BindingContext = viewModel;  
}
```

Il `OnAutoWireViewModelChanged` metodo tenta di risolvere il modello di visualizzazione utilizzando un approccio basato sulle convenzioni. Questa convenzione si presuppone che:

-   Visualizzazione di modelli è nello stesso assembly come tipi di visualizzazione.
-   Le visualizzazioni sono una. Spazio dei nomi figlio viste.
-   Visualizzazione di modelli è una. Spazio dei nomi figlio ViewModel.
-   Visualizzare i nomi di modello corrispondono ai nomi delle viste e terminano con "View".

Infine, il `OnAutoWireViewModelChanged` metodo imposta la [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del tipo di visualizzazione per il tipo di modello di visualizzazione risolto. Per ulteriori informazioni sulla risoluzione del tipo di modello di visualizzazione, vedere [risoluzione](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution).

Questo approccio presenta il vantaggio che un'applicazione ha un'unica classe che è responsabile per la creazione di istanze di modelli di visualizzazione e la connessione alle visualizzazioni.

> [!TIP]
> Utilizzare un indicatore di posizione di visualizzazione del modello per facilitare la sostituzione. Un localizzatore di modello di visualizzazione anche utilizzabile come punto di sostituzione per implementazioni alternative di dipendenze, ad esempio per i dati di ora unit test o di progettazione.

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>Consente di visualizzare le viste ad aggiornamento in risposta alle modifiche nell'oggetto sottostante modello o modello

Devono implementare tutte modello di visualizzazione e le classi di modello che sono accessibili a una visualizzazione di `INotifyPropertyChanged` interfaccia. Implementare questa interfaccia in un modello di visualizzazione o una classe di modello consente alla classe fornire le notifiche di modifica a tutti i controlli con associazione a dati nella vista quando cambia il valore della proprietà sottostante.

Dell'app deve essere progettata per l'uso corretto della notifica di modifica di proprietà, per soddisfare i requisiti seguenti:

-   Sempre generato un `PropertyChanged` eventi se si modifica il valore della proprietà pubblica. Non presupporre che la generazione di `PropertyChanged` evento può essere ignorato a causa di conoscenza delle modalità di associazione XAML.
-   Sempre generato un `PropertyChanged` calcolato di evento per qualsiasi proprietà i cui valori vengono utilizzati da altre proprietà nella vista del modello o modello.
-   Sempre generato il `PropertyChanged` evento alla fine del metodo che consente di modificare una proprietà o quando l'oggetto è noto in condizioni di sicurezza. La generazione dell'evento interrompe l'operazione da richiamare i gestori dell'evento in modo sincrono. In questo caso all'interno di un'operazione, perché potrebbe esporre l'oggetto per le funzioni di callback quando è in uno stato parzialmente aggiornato non sicuro. Inoltre, è possibile che le modifiche a catena verrà attivata da `PropertyChanged` eventi. Propagazione delle modifiche richiedono in genere gli aggiornamenti per essere completata prima che la modifica di propagazione è sicura per l'esecuzione.
-   Non generare mai un `PropertyChanged` evento se la proprietà non viene modificato. Ciò significa che è necessario confrontare i valori vecchi e nuovi prima che venga generato il `PropertyChanged` evento.
-   Non generare mai il `PropertyChanged` evento durante il costruttore del modello di visualizzazione quando si Inizializza una proprietà. Nella visualizzazione controlli con associazione a dati non verranno sottoscritti per la ricezione di notifiche di modifica a questo punto.
-   Mai generare più `PropertyChanged` eventi con lo stesso argomento di nome di proprietà all'interno di una singola chiamata sincrona di un metodo pubblico di una classe. Si consideri, ad esempio, un `NumberOfItems` proprietà il cui archivio di backup è il `_numberOfItems` campo, se con incrementi di un metodo `_numberOfItems` 50 volte durante l'esecuzione di un ciclo, deve solo generare la notifica di modifiche nel `NumberOfItems` una volta, proprietà Dopo che tutto il lavoro è stato completato. Per i metodi asincroni, generare il `PropertyChanged` evento per un nome di proprietà specificato in ciascun segmento sincrona di una catena di continuazione asincrona.

Gli utilizzi di app per dispositivi mobili eShopOnContainers la `ExtendedBindableObject` classe per fornire notifiche di modifica, mostrato nell'esempio di codice seguente:

```csharp
public abstract class ExtendedBindableObject : BindableObject  
{  
    public void RaisePropertyChanged<T>(Expression<Func<T>> property)  
    {  
        var name = GetMemberInfo(property).Name;  
        OnPropertyChanged(name);  
    }  

    private MemberInfo GetMemberInfo(Expression expression)  
    {  
        ...  
    }  
}
```

Del Xamarin.Form [ `BindableObject` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableObject/) classe implementa il `INotifyPropertyChanged` interfaccia e fornisce un [ `OnPropertyChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.OnPropertyChanged/p/System.String/) (metodo). Il `ExtendedBindableObject` classe fornisce il `RaisePropertyChanged` notifica di modifica del metodo da richiamare proprietà e a tale scopo viene utilizzata la funzionalità fornita dalla `BindableObject` classe.

Ogni classe di modello di visualizzazione nell'app mobile eShopOnContainers deriva il `ViewModelBase` (classe), che a sua volta deriva dalla `ExtendedBindableObject` classe. Di conseguenza, ogni classe di modello della vista utilizza la `RaisePropertyChanged` metodo nel `ExtendedBindableObject` classe per fornire la notifica di modifiche. Esempio di codice seguente viene illustrato come l'app mobile eShopOnContainers richiama la notifica di modifiche utilizzando un'espressione lambda:

```csharp
public bool IsLogin  
{  
    get  
    {  
        return _isLogin;  
    }  
    set  
    {  
        _isLogin = value;  
        RaisePropertyChanged(() => IsLogin);  
    }  
}
```

Si noti che l'utilizzo di un'espressione lambda in questo modo prevede una piccola influisce sulle prestazioni perché l'espressione lambda deve essere valutata per ogni chiamata. Sebbene l'impatto sulle prestazioni è limitata e non in genere influirebbe su un'app, i costi possono presentarsi quando sono presenti che molti notifiche di modifica. Tuttavia, il vantaggio di questo approccio è che fornisce l'indipendenza dai tipi in fase di compilazione e il refactoring del supporto durante la ridenominazione della proprietà.

## <a name="ui-interaction-using-commands-and-behaviors"></a>Interazione di interfaccia utente utilizzando i comandi e i comportamenti

App per dispositivi mobili, le azioni vengono in genere richiamate in risposta a un'azione dell'utente, ad esempio un clic del pulsante, che può essere implementata tramite la creazione di un gestore eventi nel file code-behind. Tuttavia, nel modello MVVM, si trova la responsabilità per l'implementazione dell'azione con il modello di visualizzazione e deve essere evitato l'inserimento di codice nel code-behind.

Comandi forniscono un modo pratico per rappresentare le azioni che possono essere associate a controlli nell'interfaccia utente. Il codice che implementa l'azione incapsulano e aiuta a mantenere è separato dalla relativa rappresentazione visual nella vista. Xamarin. Forms include controlli che possono essere connessi in modo dichiarativo a un comando e questi controlli richiami il comando quando l'utente interagisce con il controllo.

I comportamenti consentono inoltre a controlli in modo dichiarativo sia connessa a un comando. Tuttavia, i comportamenti consente di richiamare un'azione associata a un intervallo di eventi generati da un controllo. Di conseguenza, i comportamenti affrontare molti degli stessi scenari di comando abilitato controlli, fornendo un maggiore livello di flessibilità e controllo. Inoltre, i comportamenti anche utilizzabile per associare i controlli che non sono stati progettati per interagire con i comandi in modo specifico gli oggetti comando o i metodi.

### <a name="implementing-commands"></a>Implementazione di comandi

Visualizzazione di modelli in genere espone proprietà di comando, per l'associazione dalla visualizzazione, che sono istanze di oggetti che implementano il `ICommand` interfaccia. Fornisce un numero di controlli di xamarin. Forms un `Command` proprietà, che possono essere dati associato a un `ICommand` oggetti forniti dal modello di visualizzazione. Il `ICommand` interfaccia definisce un `Execute` (metodo), che incapsula l'operazione stessa, un `CanExecute` metodo, che indica se il comando può essere richiamato e un `CanExecuteChanged` evento che si verifica quando vengono apportate modifiche che interessano se l'esecuzione del comando. Il [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) e [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) , fornito da xamarin. Forms, che implementano il `ICommand` interfaccia, in cui `T` è il tipo degli argomenti di `Execute`e `CanExecute`.

All'interno di un modello di visualizzazione, deve essere un oggetto di tipo [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) o [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) per ogni proprietà pubblica nel modello di visualizzazione di tipo `ICommand`. Il `Command` o `Command<T>` costruttore richiede un `Action` oggetto callback che ha chiamato quando il `ICommand.Execute` metodo viene richiamato. Il `CanExecute` metodo è un parametro di costruttore facoltativo ed è un `Func` che restituisce un `bool`.

Il codice seguente viene illustrato come un [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) istanza, che rappresenta un comando register, viene creato specificando un delegato per il `Register` consente di visualizzare il metodo di modello:

```csharp
public ICommand RegisterCommand => new Command(Register);
```

Il comando è esposto alla visualizzazione tramite una proprietà che restituisce un riferimento a un `ICommand`. Quando il `Execute` metodo viene chiamato sul [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) inoltra semplicemente la chiamata al metodo nel modello di visualizzazione tramite il delegato che è stato specificato nell'oggetto di `Command` costruttore.

Un metodo asincrono può essere richiamato da un comando utilizzando il `async` e `await` parole chiave quando si specifica il comando `Execute` delegato. Ciò indica che il callback è un `Task` e deve essere atteso. Ad esempio, i codice seguente mostra come un [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) istanza, che rappresenta un comando di accesso, viene creato specificando un delegato per il `SignInAsync` consente di visualizzare il metodo di modello:

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

Parametri possono essere passati al `Execute` e `CanExecute` azioni tramite il [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) classe per creare un'istanza di comando. Ad esempio, i codice seguente mostra come un `Command<T>` istanza viene utilizzata per indicare che il `NavigateAsync` metodo richiederà un argomento di tipo `string`:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

In entrambi i [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) e [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) classi, il delegato per il `CanExecute` in ogni costruttore è facoltativo. Se non è specificato un delegato, il `Command` restituirà `true` per `CanExecute`. Tuttavia, il modello di visualizzazione può indicare un cambiamento del comando `CanExecute` stato chiamando il `ChangeCanExecute` metodo il `Command` oggetto. In questo modo il `CanExecuteChanged` dell'evento. Tutti i controlli nell'interfaccia utente associato al comando verranno quindi aggiornare il relativo stato abilitato per riflettere la disponibilità del comando associato a dati.

#### <a name="invoking-commands-from-a-view"></a>La chiamata di comandi da una vista

Nell'esempio di codice riportato di seguito viene illustrato come un [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) nel `LoginView` associa il `RegisterCommand` nel `LoginViewModel` classe utilizzando un [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) istanza:

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

Un parametro di comando può anche essere facoltativamente definito utilizzando il [ `CommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TapGestureRecognizer.CommandParameter/) proprietà. Il tipo di argomento previsto è specificato nella `Execute` e `CanExecute` metodi di destinazione. Il [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/) richiamerà automaticamente il comando di destinazione quando l'utente interagisce con il controllo associato. Il parametro di comando, se specificato, verrà passato come argomento al comando `Execute` delegato.

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>Implementazione del comportamento

I comportamenti consentono la funzionalità da aggiungere ai controlli dell'interfaccia utente senza dover sottoclasse li. Al contrario, la funzionalità è implementata in una classe di comportamento e associata al controllo come se fosse parte del controllo stesso. I comportamenti consentono di implementare il codice che in genere è necessario scrivere come code-behind, poiché interagisce direttamente con l'API del controllo, in modo tale che può essere conciso associato al controllo e incluso nel pacchetto per il riutilizzo in più di una vista o app. Nel contesto di MVVM, i comportamenti sono un approccio utile per la connessione di controlli ai comandi.

Un comportamento che è collegato a un controllo tramite le proprietà associate è noto come un *collegato comportamento*. Il comportamento è quindi possibile utilizzare l'API esposta dell'elemento a cui è collegato per aggiungere funzionalità a tale controllo, o altri controlli, la struttura ad albero visuale della vista. Contiene l'app mobile eShopOnContainers il `LineColorBehavior` (classe), che è un comportamento dell'allegato. Per ulteriori informazioni su questo comportamento, vedere [la visualizzazione di errori di convalida](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors).

Un comportamento di xamarin. Forms è una classe che deriva dal [ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/) o [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) (classe), in cui `T `è il tipo del controllo a cui applicare il comportamento. Queste classi forniscono `OnAttachedTo` e `OnDetachingFrom` metodi, che devono essere sostituiti per fornire la logica che verrà eseguita quando il comportamento è collegato e scollegato da controlli.

Nell'app mobile eShopOnContainers, il `BindableBehavior<T>` deriva dalla classe di [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) classe. Lo scopo del `BindableBehavior<T>` classe è fornire una classe di base per i comportamenti di xamarin. Forms che richiedono il [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) del comportamento da impostare per il controllo associato.

Il `BindableBehavior<T>` classe fornisce un sottoponibile a override `OnAttachedTo` metodo che imposta il [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) il comportamento e un sottoponibile a override `OnDetachingFrom` metodo che pulisce la `BindingContext`. Inoltre, la classe archivia un riferimento per il controllo associato nel `AssociatedObject` proprietà.

L'app mobile eShopOnContainers include un `EventToCommandBehavior` (classe), che esegue un comando in risposta a un evento che si verificano. Questa classe deriva dal `BindableBehavior<T>` classe in modo che il comportamento è possibile associare a ed eseguire un `ICommand` specificato da un `Command` proprietà quando viene utilizzato il comportamento. L'esempio di codice seguente visualizza la classe `EventToCommandBehavior`:

```csharp
public class EventToCommandBehavior : BindableBehavior<View>  
{  
    ...  
    protected override void OnAttachedTo(View visualElement)  
    {  
        base.OnAttachedTo(visualElement);  

        var events = AssociatedObject.GetType().GetRuntimeEvents().ToArray();  
        if (events.Any())  
        {  
            _eventInfo = events.FirstOrDefault(e => e.Name == EventName);  
            if (_eventInfo == null)  
                throw new ArgumentException(string.Format(  
                        "EventToCommand: Can't find any event named '{0}' on attached type",   
                        EventName));  

            AddEventHandler(_eventInfo, AssociatedObject, OnFired);  
        }  
    }  

    protected override void OnDetachingFrom(View view)  
    {  
        if (_handler != null)  
            _eventInfo.RemoveEventHandler(AssociatedObject, _handler);  

        base.OnDetachingFrom(view);  
    }  

    private void AddEventHandler(  
            EventInfo eventInfo, object item, Action<object, EventArgs> action)  
    {  
        ...  
    }  

    private void OnFired(object sender, EventArgs eventArgs)  
    {  
        ...  
    }  
}
```

Il `OnAttachedTo` e `OnDetachingFrom` metodi vengono utilizzati per registrare e annullare la registrazione di un gestore eventi per l'evento definito nel `EventName` proprietà. Quindi, quando viene generato l'evento, il `OnFired` metodo viene richiamato, che esegue il comando.

Il vantaggio dell'utilizzo di `EventToCommandBehavior` per eseguire un comando quando viene generato un evento, è che i comandi sono possibile associati i controlli che non sono stati progettati per interagire con i comandi. Inoltre, viene spostato il codice di gestione degli eventi in visualizzazione di modelli, in cui può essere sottoposta a unit testata.

#### <a name="invoking-behaviors-from-a-view"></a>Richiamare i comportamenti da una vista

Il `EventToCommandBehavior` è particolarmente utile per associare un comando a un controllo che non supporta i comandi. Ad esempio, il `ProfileView` utilizza il `EventToCommandBehavior` per eseguire il `OrderDetailCommand` quando il [ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/) evento viene generato nel [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) che elenca gli ordini dell'utente, come illustrato nel codice seguente:

```xaml
<ListView>  
    <ListView.Behaviors>  
        <behaviors:EventToCommandBehavior             
            EventName="ItemTapped"  
            Command="{Binding OrderDetailCommand}"  
            EventArgsConverter="{StaticResource ItemTappedEventArgsConverter}" />  
    </ListView.Behaviors>  
    ...  
</ListView>
```

In fase di esecuzione, il `EventToCommandBehavior` risponderà l'interazione con il [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Quando è selezionato un elemento nel `ListView`, [ `ItemTapped` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemTapped/) viene generato l'evento, che verrà eseguito il `OrderDetailCommand` nel `ProfileViewModel`. Per impostazione predefinita, gli argomenti dell'evento per l'evento vengono passati al comando. Questi dati viene convertiti come viene passato tra origine e di destinazione dal convertitore specificato nella `EventArgsConverter` proprietà, che restituisce il [ `Item` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ItemTappedEventArgs.Item/) del `ListView` dal [ `ItemTappedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ItemTappedEventArgs/). Pertanto, quando il `OrderDetailCommand` viene eseguita, selezionato `Order` viene passato come parametro di azione registrata.

Per ulteriori informazioni sui comportamenti, vedere [comportamenti](~/xamarin-forms/app-fundamentals/behaviors/index.md).

## <a name="summary"></a>Riepilogo

Il modello Model-View-ViewModel (MVVM) consente di separare correttamente la logica di business e la presentazione di un'applicazione dalla relativa interfaccia utente (UI). Mantenere una netta separazione tra la logica dell'applicazione e l'interfaccia utente consente di risolvere numerosi problemi di sviluppo e può rendere più facile da testare, gestire e sviluppare un'applicazione. Può migliorare notevolmente anche la possibilità di riutilizzare codice e consente agli sviluppatori e progettisti dell'interfaccia utente più collaborano facilmente quando si sviluppano le rispettive parti di un'app.

Utilizzando il MVVM di schema, l'interfaccia utente dell'app e la logica di presentazione e business sottostante è suddivisa in tre classi separate: la visualizzazione, che incapsula l'interfaccia utente e l'interfaccia utente della logica; il modello di visualizzazione, che incapsula la logica di presentazione e stato. e il modello, che incapsula la logica di business e i dati dell'applicazione.


## <a name="related-links"></a>Collegamenti correlati

- [Scaricare eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
