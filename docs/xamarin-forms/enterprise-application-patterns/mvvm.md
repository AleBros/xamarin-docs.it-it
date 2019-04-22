---
title: Il modello Model-View-ViewModel
description: In questo capitolo illustra come l'app per dispositivi mobili di eShopOnContainers Usa il modello MVVM per separare correttamente la logica di business e la presentazione dell'app dalla propria interfaccia utente.
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 87448c556c66ea086db70699848227e1f671792b
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59019386"
---
# <a name="the-model-view-viewmodel-pattern"></a>Il modello Model-View-ViewModel

L'esperienza di sviluppo xamarin. Forms in genere implica la creazione di un'interfaccia utente in XAML e quindi aggiungendo code-behind che opera nell'interfaccia utente. Come le app vengono modificate e aumentano di dimensioni e l'ambito, possono verificarsi problemi di una manutenzione complessa. Queste comprendono l'accoppiamento stretto fra i controlli dell'interfaccia utente e la logica di business, aumentando così il costo di apportare le modifiche dell'interfaccia utente e le difficoltà di unit test di tale codice.

Il modello Model-View-ViewModel (MVVM) consente di separare correttamente la logica di business e la presentazione di un'applicazione dalla relativa interfaccia utente (UI). Mantenere una netta separazione tra logica dell'applicazione e l'interfaccia utente consente di risolvere numerosi problemi di sviluppo e rendono più semplice da testare, gestire e sviluppare un'applicazione. Viene anche notevolmente può migliorare le opportunità di riutilizzo di codice e consente agli sviluppatori le finestre di progettazione dell'interfaccia utente più facilmente per collaborare e quando si sviluppano le rispettive parti di un'app.

## <a name="the-mvvm-pattern"></a>Il modello MVVM

Esistono tre componenti di base nel modello MVVM: il modello, la visualizzazione e il modello di visualizzazione. Ognuno ha uno scopo distinto. Figura 2-1 mostra le relazioni tra i tre componenti.

![](mvvm-images/mvvm.png "Il modello MVVM")

**Figura 2-1**: Il modello MVVM

Oltre a comprendere le responsabilità di ogni componente, è anche importante comprendere come interagiscono tra loro. A livello generale, la vista "conoscenza" modello di visualizzazione e il modello di visualizzazione "conoscenza" il modello, ma il modello non è a conoscenza del modello di visualizzazione e il modello di visualizzazione è a conoscenza della visualizzazione. Pertanto, il modello di visualizzazione consente di isolare la visualizzazione dal modello e consente al modello di evolversi in modo indipendente la visualizzazione.

Come indicato di seguito sono riportati i vantaggi dell'uso del pattern MVVM:

-   Se è presente un'implementazione esistente di modello che incapsula la logica di business esistente, può essere difficile o rischiose per modificarlo. In questo scenario, il modello di visualizzazione agisce come un adattatore per le classi del modello e consente di evitare di apportare modifiche importanti al codice del modello.
-   Gli sviluppatori possono creare gli unit test per il modello di visualizzazione e il modello, senza usare la vista. Gli unit test per il modello di visualizzazione può esercitare esattamente la stessa funzionalità come quelle utilizzate da parte della vista.
-   L'interfaccia utente dell'app può essere riprogettata senza modificare il codice, a condizione che la vista viene implementata interamente in XAML. Pertanto, una nuova versione della visualizzazione dovrebbe funzionare con il modello di vista esistente.
-   Progettisti e sviluppatori possono lavorare in modo indipendente e simultaneamente sui relativi componenti durante il processo di sviluppo. Finestre di progettazione possono concentrarsi sulla visualizzazione, mentre gli sviluppatori possono operare nel modello di visualizzazione e componenti del modello.

La chiave per l'utilizzo in modo efficace MVVM sta nel comprendere come eseguire il refactoring di codice dell'app nelle classi corrette e comprendere l'interagiscono tra le classi. Le sezioni seguenti illustrano le responsabilità di ognuna delle classi nel modello MVVM.

### <a name="view"></a>Visualizza

La visualizzazione è responsabile della definizione di struttura, layout e l'aspetto di ciò che l'utente visualizza sullo schermo. Idealmente, ogni vista è definito in XAML, con un code-behind limitato che non contiene la logica di business. Tuttavia, in alcuni casi, il code-behind potrebbe contenere per la logica dell'interfaccia utente che implementa il comportamento visivo che è difficile esprimere in XAML, ad esempio le animazioni.

In un'applicazione xamarin. Forms, una vista è in genere un [ `Page` ](xref:Xamarin.Forms.Page)-derivato o [ `ContentView` ](xref:Xamarin.Forms.ContentView)-classe derivata. Tuttavia, le visualizzazioni possono anche essere rappresentate da un modello di dati che specifica gli elementi dell'interfaccia utente da utilizzare per rappresentare visivamente un oggetto quando viene visualizzato. Un modello di dati come una visualizzazione non ha alcun code-behind e consente di associare a un tipo di modello di visualizzazione specifica.

> [!TIP]
> Evitare l'abilitazione e disabilitazione di elementi dell'interfaccia utente nel code-behind. Assicurarsi che i modelli di visualizzazione sono responsabili per la definizione delle modifiche di stato logico che interessano alcuni aspetti della visualizzazione della vista, ad esempio se è disponibile un comando o un'indicazione che è in sospeso un'operazione. Pertanto, attivare e disattivare gli elementi dell'interfaccia utente mediante l'associazione per visualizzare le proprietà del modello, anziché l'abilitazione e disabilitazione nel code-behind.

Sono disponibili diverse opzioni per l'esecuzione del codice nel modello di visualizzazione in risposta alle interazioni nella visualizzazione, ad esempio un pulsante o la selezione di elementi. Se un controllo supporta i comandi, il controllo `Command` proprietà può essere associato a dati a un `ICommand` proprietà nel modello di visualizzazione. Quando viene richiamato il comando del controllo, verrà eseguito il codice nel modello di visualizzazione. Oltre ai comandi, i comportamenti possono essere collegati a un oggetto nella vista e possono restare in ascolto per essere generato l'evento o un comando da richiamare. In risposta, è possibile quindi richiamare il comportamento di un `ICommand` nel modello di visualizzazione o un metodo nel modello di visualizzazione.

### <a name="viewmodel"></a>ViewModel

Il modello di visualizzazione implementa proprietà e i comandi su cui è possibile associare i dati della visualizzazione e notifica alla visualizzazione di tutte le modifiche dello stato tramite gli eventi di notifica di modifica. La funzionalità verrà proposto dall'interfaccia utente di definire le proprietà e i comandi che fornisce il modello di visualizzazione, ma determina la visualizzazione come tale funzionalità deve essere visualizzato.

> [!TIP]
> Mantenere l'interfaccia utente reattiva con operazioni asincrone. App per dispositivi mobili è consigliabile mantenere il thread dell'interfaccia utente ha sbloccato per migliorare la percezione di prestazioni. Pertanto, nel modello di visualizzazione, usare i metodi asincroni per le operazioni dei / o e generare eventi per notificare in modo asincrono le viste delle modifiche delle proprietà.

Il modello di visualizzazione è anche responsabile del coordinamento delle interazioni della visualizzazione con classi modello che sono necessari. È in genere esiste una relazione uno-a-molti tra il modello di visualizzazione e le classi del modello. Il modello di visualizzazione è possibile scegliere di esporre classi di modello direttamente alla vista in modo che i controlli nella visualizzazione è possibile associare i dati in modo diretto. In questo caso, le classi del modello saranno necessario essere progettata per supportare il data binding e gli eventi di notifica di modifica.

Ogni modello di visualizzazione fornisce i dati da un modello in un form che può utilizzare facilmente la visualizzazione. A tale scopo, il modello di visualizzazione esegue a volte la conversione dei dati. Questa conversione dei dati nel modello di visualizzazione consiste nell'inserire una buona idea poiché fornisce le proprietà di cui è possibile associare la visualizzazione. Il modello di visualizzazione, ad esempio, possibile combinare i valori delle due proprietà per renderne più semplice per la visualizzazione da parte della vista.

> [!TIP]
> Centralizzare le conversioni di dati in un livello di conversione. È anche possibile usare i convertitori di tipi come un livello di conversione di dati separati che si trova tra il modello di visualizzazione e la visualizzazione. Ciò può essere necessario, ad esempio, quando i dati richiedono una formattazione speciale che non fornisce il modello di visualizzazione.

Affinché il modello di visualizzazione partecipare al data binding bidirezionale con la visualizzazione, è necessario aumentare le relative proprietà di `PropertyChanged` evento. Visualizzazione di modelli soddisfa questo requisito mediante l'implementazione di `INotifyPropertyChanged` interfaccia e la generazione di `PropertyChanged` evento quando viene modificata una proprietà.

Per le raccolte, la vista orientato `ObservableCollection<T>` viene fornito. Questa raccolta implementa notifica collection modificato, lo sviluppatore di dover implementare di sblocco di `INotifyCollectionChanged` interfaccia nelle raccolte.

### <a name="model"></a>Modello

Classi modello sono non visivo che incapsula i dati dell'app. Pertanto, il modello può essere considerato come modello di dominio dell'app, che in genere include un modello di dati insieme alla logica di business e la convalida. Oggetti di trasferimento dati (DTO), Plain Old CLR Object (poco) ed entità generate e oggetti proxy sono esempi di oggetti modello.

Le classi del modello vengono in genere usate in combinazione con i servizi o i repository che incapsulano l'accesso ai dati e la memorizzazione nella cache.

## <a name="connecting-view-models-to-views"></a>Modelli di visualizzazione che si connette alle visualizzazioni

Visualizzazione di modelli può essere connesse alle viste utilizzando la funzionalità di data binding di xamarin. Forms. Esistono molti approcci che possono essere utilizzati per costruire le visualizzazioni e visualizzare i modelli e associarli in fase di esecuzione. Questi approcci rientrano in due categorie, note come composizione della prima vista e composizione del primo modello view. Scelta tra composizione della vista prima e la visualizzazione di composizione del primo modello è un problema di preferenza e complessità. Tuttavia, tutti gli approcci condividono stesso obiettivo, ovvero per la vista dispone di un modello di vista assegnato alla relativa proprietà BindingContext.

Con la visualizzazione composizione prima l'app a livello concettuale comprende le viste che si connettono ai modelli di visualizzazione che dipendono. Il vantaggio principale di questo approccio è che rende semplice la creazione loosely-coupled, unit App testabili perché i modelli di visualizzazione non dispongono di alcuna dipendenza dalle visualizzazioni autonomamente. È anche facile da comprendere la struttura dell'app seguendo la struttura visiva, anziché dover tenere traccia dell'esecuzione di codice per comprendere come classi vengono create e associate. Inoltre, costruzione prima visualizzazione consente di allineare con il sistema di spostamento xamarin. Forms che è responsabile della costruzione pagine quando si verifica la navigazione, che rende una composizione primo modello di visualizzazione non allineato con la piattaforma e complessi.

Con la visualizzazione a livello concettuale è composto da di visualizzazione di modelli, composizione del primo modello dell'app con un servizio di essere responsabili per la visualizzazione per un modello di visualizzazione di individuazione. Composizione del primo modello View ritiene più naturali per alcuni sviluppatori, poiché la creazione di visualizzazione può essere astratte, consentendo loro di concentrarsi sulla struttura logica non di interfaccia utente dell'app. Consente, inoltre, i modelli di visualizzazione da creare con altri modelli di visualizzazione. Tuttavia, questo approccio è spesso complesso e può diventare difficile da comprendere come le varie parti dell'app vengono create e associate.

> [!TIP]
> Mantenere i modelli di visualizzazione e le visualizzazioni indipendenti. L'associazione delle viste a una proprietà in un'origine dati deve essere delle dipendenze di entità della vista nel relativo modello di visualizzazione corrispondente. In particolare, non fare riferimento a tipi di viste, ad esempio [ `Button` ](xref:Xamarin.Forms.Button) e [ `ListView` ](xref:Xamarin.Forms.ListView), dalla visualizzazione di modelli. Seguono i principi illustrati in questo articolo, i modelli di visualizzazione possono essere testati in isolamento, riducendo pertanto il rischio di difetti del software, limitando l'ambito.

Le sezioni seguenti illustrano gli approcci principali per la connessione di visualizzazione di modelli per le visualizzazioni.

### <a name="creating-a-view-model-declaratively"></a>Creazione di un modello di visualizzazione in modo dichiarativo

L'approccio più semplice è per la vista da creare in modo dichiarativo il corrispondente modello di visualizzazione in XAML. Quando viene creata la visualizzazione, verrà creata anche l'oggetto modello di visualizzazione corrispondente. Questo metodo viene dimostrato nell'esempio di codice seguente:

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

Quando la [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) viene creata un'istanza del `LoginViewModel` automaticamente viene costruito e impostare come la vista [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext).

Questa costruzione dichiarativa e l'assegnazione del modello di visualizzazione da parte della vista ha il vantaggio che è semplice, ma ha lo svantaggio che richiede un costruttore (senza parametri) predefinito nel modello di visualizzazione.

### <a name="creating-a-view-model-programmatically"></a>Creazione di un modello di visualizzazione a livello di codice

Una vista può includere codice nel file code-behind che determina il modello di visualizzazione da assegnare alla relativa [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) proprietà. Questa operazione viene spesso eseguita nel costruttore della visualizzazione, come illustrato nell'esempio di codice seguente:

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

La costruzione a livello di codice e l'assegnazione del modello di visualizzazione all'interno di code-behind della vista ha il vantaggio che è semplice. Tuttavia, lo svantaggio principale di questo approccio è che la visualizzazione deve fornire il modello di visualizzazione con tutte le dipendenze necessarie. Usando un contenitore di inserimento delle dipendenze può aiutare a mantenere loose correlazione rigida tra la visualizzazione e il modello di visualizzazione. Per altre informazioni, vedere [inserimento delle dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

### <a name="creating-a-view-defined-as-a-data-template"></a>Creazione di una vista definita come un modello di dati

Una vista può essere definita come un modello di dati e associata a un tipo di modello di visualizzazione. I modelli di dati possono essere definiti come risorse o possono essere definite inline all'interno del controllo che verrà visualizzato il modello di visualizzazione. Il contenuto del controllo è l'istanza del modello di visualizzazione e il modello di dati viene usato per rappresentare visivamente lo. Questa tecnica è un esempio di una situazione in cui il modello di visualizzazione viene creata un'istanza in primo luogo, seguita dalla creazione della vista.

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>Creazione automatica di un modello di visualizzazione con un localizzatore di modello di visualizzazione

Un localizzatore di modello di visualizzazione è una classe personalizzata che gestisce la creazione di istanze di modelli di visualizzazione e la relativa associazione alle visualizzazioni. Nell'app eShopOnContainers per dispositivi mobili, il `ViewModelLocator` classe ha una proprietà associata, `AutoWireViewModel`, che viene utilizzato per associare i modelli di visualizzazione con le visualizzazioni. Nella finestra XAML della visualizzazione, questa proprietà associata è impostata su true per indicare che il modello di visualizzazione debba essere connessi automaticamente alla visualizzazione, come illustrato nell'esempio di codice seguente:

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

Il `AutoWireViewModel` è una proprietà associabile che viene inizializzato su false e quando viene modificato il valore di `OnAutoWireViewModelChanged` gestore eventi viene chiamato. Questo metodo risolve il modello di visualizzazione per la visualizzazione. Esempio di codice seguente viene illustrato come si ottiene questo risultato:

```csharp
private static void OnAutoWireViewModelChanged(BindableObject bindable, object oldValue, object newValue)  
{  
    var view = bindable as Element;  
    if (view == null)  
    {  
        return;  
    }  

    var viewType = view.GetType();  
    var viewName = viewType.FullName.Replace(".Views.", ".ViewModels.");  
    var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;  
    var viewModelName = string.Format(  
        CultureInfo.InvariantCulture, "{0}Model, {1}", viewName, viewAssemblyName);  

    var viewModelType = Type.GetType(viewModelName);  
    if (viewModelType == null)  
    {  
        return;  
    }  
    var viewModel = _container.Resolve(viewModelType);  
    view.BindingContext = viewModel;  
}
```

Il `OnAutoWireViewModelChanged` metodo tenta di risolvere il modello di visualizzazione usando un approccio basato su convenzioni. Questa convenzione presuppone che:

-   Visualizzazione di modelli è nello stesso assembly come tipi di visualizzazione.
-   Le visualizzazioni sono una. Spazio dei nomi di visualizzazioni figlio.
-   Visualizzazione di modelli è in una. Spazio dei nomi figlio ViewModel.
-   Visualizzare i nomi di modello corrispondono con i nomi delle visualizzazioni e terminano con "ViewModel".

Infine, il `OnAutoWireViewModelChanged` metodo imposta la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del tipo di visualizzazione per il tipo di modello di vista risolto. Per altre informazioni sulla risoluzione del tipo di modello di visualizzazione, vedere [risoluzione](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution).

Questo approccio ha il vantaggio che un'app ha una singola classe responsabile per la creazione di istanze della loro connessione alle visualizzazioni e i modelli di visualizzazione.

> [!TIP]
> Usare un localizzatore di modello di visualizzazione per una maggiore facilità di sostituzione. Un localizzatore di modello di visualizzazione è anche utilizzabile come un punto di sostituzione per implementazioni alternative di dipendenze, come per i dati di ora unit test o la progettazione.

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>Consente di visualizzare le viste ad aggiornamento in risposta alle modifiche nell'oggetto sottostante modello o modello

Tutti i modello di visualizzazione e le classi di modello che sono accessibili a una vista devono implementare il `INotifyPropertyChanged` interfaccia. Implementazione di questa interfaccia in un modello di visualizzazione o una classe di modello consente alla classe fornire le notifiche di modifica per tutti i controlli con associazione a dati nella vista quando viene modificato il valore della proprietà sottostante.

Le app devono essere progettate per l'uso corretto della notifica di modifica di proprietà, soddisfacendo i requisiti seguenti:

-   Genera sempre un `PropertyChanged` evento se viene modificato il valore della proprietà pubblica. Non presupporre che la generazione di `PropertyChanged` evento può essere ignorato a causa di conoscenza del modo in cui si verifica associazione XAML.
-   Genera sempre un `PropertyChanged` eventi per qualsiasi calcolati le proprietà i cui valori vengono utilizzati dalle altre proprietà nella vista del modello o modello.
-   Generare sempre il `PropertyChanged` evento alla fine del metodo che consente di modificare una proprietà o quando l'oggetto è noto per essere in uno stato sicuro. La generazione dell'evento interrompe l'operazione richiamando in modo sincrono i gestori dell'evento. In questo caso un'operazione in corso, che si potrebbe esporre l'oggetto a funzioni di callback quando è in uno stato parzialmente aggiornato non sicuro. Inoltre, è possibile che le modifiche a catena che verrà attivata da `PropertyChanged` gli eventi. Propagazione delle modifiche richiedano in genere gli aggiornamenti per essere completata prima che la modifica a cascata è sicura da eseguire.
-   Mai generare un `PropertyChanged` evento se la proprietà non cambia. Ciò significa che è necessario confrontare i valori vecchi e nuovi prima che venga generato il `PropertyChanged` evento.
-   Mai generare il `PropertyChanged` eventi durante il costruttore di un modello di visualizzazione caso di inizializzazione di una proprietà. Controlli con associazione a dati nella vista non verranno sottoscritti per ricevere le notifiche delle modifiche a questo punto.
-   Mai più di una generazione `PropertyChanged` evento con lo stesso argomento nome proprietà all'interno di una singola chiamata sincrona di un metodo pubblico di una classe. Si consideri, ad esempio, un `NumberOfItems` proprietà il cui archivio di backup è il `_numberOfItems` campo, se un metodo consente di incrementare `_numberOfItems` 50 volte durante l'esecuzione di un ciclo, deve solo generare la notifica di modifiche sul `NumberOfItems` proprietà una sola volta, Dopo aver completata tutte le operazioni. Per i metodi asincroni, generare il `PropertyChanged` evento per un nome di proprietà specificato in ciascun segmento di una catena di continuazione asincrono sincrono.

L'app per dispositivi mobili di eShopOnContainers Usa i `ExtendedBindableObject` classe per fornire notifiche di modifica, come illustrato nell'esempio di codice seguente:

```csharp
public abstract class ExtendedBindableObject : BindableObject  
{  
    public void RaisePropertyChanged<T>(Expression<Func<T>> property)  
    {  
        var name = GetMemberInfo(property).Name;  
        OnPropertyChanged(name);  
    }  

    private MemberInfo GetMemberInfo(Expression expression)  
    {  
        ...  
    }  
}
```

Del Xamarin.Form [ `BindableObject` ](xref:Xamarin.Forms.BindableObject) classe implementa il `INotifyPropertyChanged` interfaccia e fornisce un' [ `OnPropertyChanged` ](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String)) (metodo). Il `ExtendedBindableObject` classe fornisce il `RaisePropertyChanged` metodo da richiamare proprietà notifica di modifica e a tale scopo viene utilizzata la funzionalità fornita dal `BindableObject` classe.

Ogni classe di modello di visualizzazione nell'app eShopOnContainers deriva dal `ViewModelBase` classe, che a sua volta deriva dal `ExtendedBindableObject` classe. Utilizza, pertanto, ogni classe di modello di visualizzazione di `RaisePropertyChanged` metodo nel `ExtendedBindableObject` classe per fornire la notifica di modifiche. Esempio di codice seguente viene illustrato come l'app per dispositivi mobili di eShopOnContainers richiama la notifica di modifiche utilizzando un'espressione lambda:

```csharp
public bool IsLogin  
{  
    get  
    {  
        return _isLogin;  
    }  
    set  
    {  
        _isLogin = value;  
        RaisePropertyChanged(() => IsLogin);  
    }  
}
```

Si noti che l'utilizzo di un'espressione lambda in questo modo prevede piccolo sulle prestazioni perché l'espressione lambda deve essere valutata per ogni chiamata. Sebbene l'impatto sulle prestazioni è limitata e non influirebbe in genere un'app, i costi possono presentarsi quando vi sono che molte le notifiche di modifica. Tuttavia, il vantaggio di questo approccio è che fornisce l'indipendenza dai tipi in fase di compilazione e supporto per il refactoring durante la ridenominazione della proprietà.

## <a name="ui-interaction-using-commands-and-behaviors"></a>Interazione dell'interfaccia utente usando i comandi e i comportamenti

Nelle App per dispositivi mobili, le azioni vengono richiamate in genere in risposta a un'azione dell'utente, ad esempio un clic sul pulsante, che può essere implementata mediante la creazione di un gestore dell'evento nel file code-behind. Tuttavia, nel modello MVVM, si trova la responsabilità di implementare l'azione con il modello di visualizzazione ed evitare di inserire codice nel code-behind.

I comandi forniscono un modo pratico per rappresentare le azioni che possono essere associate ai controlli nell'interfaccia utente. Le classi incapsulare il codice che implementa l'azione e contribuiscono a mantenere è disaccoppiata dalla propria rappresentazione visiva nella visualizzazione. Xamarin. Forms include controlli che possono essere connessi in modo dichiarativo a un comando e questi controlli verranno richiamato il comando quando l'utente interagisce con il controllo.

I comportamenti consentono inoltre di controlli da collegare in modo dichiarativo a un comando. Tuttavia, i comportamenti utilizzabile per richiamare un'azione che ha associato una gamma di eventi generati da un controllo. Di conseguenza, i comportamenti affrontare molti degli stessi scenari di controlli con supporto di comando, offrendo un maggiore livello di flessibilità e controllo. Inoltre, i comportamenti sono anche utilizzabile per associare gli oggetti comando o i metodi con i controlli che non sono stati appositamente progettati per interagire con i comandi.

### <a name="implementing-commands"></a>Implementazione di comandi

Visualizzazione di modelli in genere espone proprietà di comando, per l'associazione dalla visualizzazione, che sono istanze di oggetti che implementano il `ICommand` interfaccia. Specificare un numero di controlli di xamarin. Forms un `Command` proprietà, che possono essere dati associato a un `ICommand` relativo oggetto fornito dal modello di visualizzazione. Il `ICommand` interfaccia definisce un `Execute` metodo, che incapsula l'operazione stessa, un `CanExecute` metodo, che indica se il comando può essere richiamato e un `CanExecuteChanged` evento che si verifica quando vengono apportate modifiche che interessano se l'esecuzione del comando. Il [ `Command` ](xref:Xamarin.Forms.Command) e [ `Command<T>` ](xref:Xamarin.Forms.Command) classi, fornite da xamarin. Forms, implementano il `ICommand` interfaccia, in cui `T` è il tipo di argomenti da `Execute`e `CanExecute`.

All'interno di un modello di visualizzazione, non vi sarà un oggetto di tipo [ `Command` ](xref:Xamarin.Forms.Command) oppure [ `Command<T>` ](xref:Xamarin.Forms.Command) per ogni proprietà pubblica nel modello di visualizzazione di tipo `ICommand`. Il `Command` oppure `Command<T>` costruttore richiede un' `Action` oggetto callback che ha chiamato quando il `ICommand.Execute` metodo viene richiamato. Il `CanExecute` metodo è un parametro del costruttore facoltativo ed è una `Func` che restituisce un `bool`.

Il codice seguente illustra come un [ `Command` ](xref:Xamarin.Forms.Command) istanza, che rappresenta un comando di registrazione, viene costruita specificando un delegato per il `Register` visualizzare metodo del modello:

```csharp
public ICommand RegisterCommand => new Command(Register);
```

Il comando viene esposta per la visualizzazione grazie a una proprietà che restituisce un riferimento a un `ICommand`. Quando la `Execute` metodo viene chiamato sul [ `Command` ](xref:Xamarin.Forms.Command) dell'oggetto, ma inoltra semplicemente la chiamata al metodo nel modello di visualizzazione tramite il delegato specificato nel `Command` costruttore.

Un metodo asincrono può essere richiamato da un comando usando il `async` e `await` parole chiave quando si specifica il comando `Execute` delegare. Ciò indica che il callback è un `Task` e deve essere atteso. Ad esempio, i codice seguente mostra come un [ `Command` ](xref:Xamarin.Forms.Command) istanza, che rappresenta un comando di accesso, viene costruita specificando un delegato per il `SignInAsync` visualizzare metodo del modello:

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

I parametri possono essere passati per il `Execute` e `CanExecute` azioni tramite il [ `Command<T>` ](xref:Xamarin.Forms.Command) classe per creare un'istanza di comando. Ad esempio, i codice seguente mostra come un `Command<T>` istanza viene utilizzata per indicare che il `NavigateAsync` metodo richiederanno un argomento di tipo `string`:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

In entrambi i [ `Command` ](xref:Xamarin.Forms.Command) e [ `Command<T>` ](xref:Xamarin.Forms.Command) classi, il delegato per il `CanExecute` costruttore è facoltativo. Se non si specifica un delegato, il `Command` restituirà `true` per `CanExecute`. Tuttavia, il modello di visualizzazione può indicare un cambiamento del comando `CanExecute` lo stato chiamando il `ChangeCanExecute` metodo sul `Command` oggetto. In questo modo, il `CanExecuteChanged` generazione dell'evento. Tutti i controlli nell'interfaccia utente che sono associati al comando aggiornerà quindi lo stato abilitato in modo da riflettere la disponibilità del comando associato a dati.

#### <a name="invoking-commands-from-a-view"></a>La chiamata di comandi da una vista

Il codice seguente esempio viene illustrato come un [ `Grid` ](xref:Xamarin.Forms.Grid) nel `LoginView` associa al `RegisterCommand` nel `LoginViewModel` classe utilizzando un [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) istanza:

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

Un parametro di comando può anche essere facoltativamente definito usando le [ `CommandParameter` ](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) proprietà. Il tipo di argomento previsto è specificato nella `Execute` e `CanExecute` metodi di destinazione. Il [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) richiamerà automaticamente il comando di destinazione quando l'utente interagisce con il controllo associato. Il parametro del comando, se specificato, verrà passato come argomento al comando `Execute` delegare.

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>Implementazione dei comportamenti

Comportamenti consentono funzionalità da aggiungere ai controlli dell'interfaccia utente senza la necessità di sottoclasse li. La funzionalità viene invece implementata in una classe di comportamento e associata al controllo come se fosse parte del controllo stesso. I comportamenti consentono di implementare il codice che sarebbe normalmente necessario per scrivere come code-behind, in quanto interagisce direttamente con l'API del controllo, in modo che possa essere conciso associato al controllo e incluso nel pacchetto per il riutilizzo in più di una vista o app. Nel contesto del modello MVVM, i comportamenti sono un approccio utile per la connessione di controlli ai comandi.

Un comportamento che è collegato a un controllo tramite le proprietà associate è noto come un *collegati comportamento*. Il comportamento è quindi possibile usare l'API esposta dell'elemento a cui viene collegata per aggiungere funzionalità a tale controllo, o altri controlli, la struttura ad albero visuale della visualizzazione. L'app per dispositivi mobili di eShopOnContainers contiene il `LineColorBehavior` (classe), ovvero un comportamento associato. Per altre informazioni su questo comportamento, vedere [visualizzazione di errori di convalida](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors).

Un comportamento di xamarin. Forms è una classe che deriva dal [ `Behavior` ](xref:Xamarin.Forms.Behavior) oppure [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) (classe), in cui `T `è il tipo del controllo a cui applicare il comportamento. Queste classi forniscono `OnAttachedTo` e `OnDetachingFrom` metodi, che devono essere sostituiti per fornire la logica che verrà eseguita quando il comportamento è collegato a e scollegato dai controlli.

Nell'app eShopOnContainers per dispositivi mobili, il `BindableBehavior<T>` deriva dalla classe la [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) classe. Lo scopo del `BindableBehavior<T>` classe consiste nel fornire una classe di base per i comportamenti di xamarin. Forms che richiedono il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) del comportamento da impostare sul controllo associato.

Il `BindableBehavior<T>` classe fornisce un sottoponibile a override `OnAttachedTo` metodo che imposta il [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) di comportamento e un sottoponibile a override `OnDetachingFrom` metodo che pulisce la `BindingContext`. La classe archivia anche un riferimento al controllo associato nella proprietà `AssociatedObject`.

L'app per dispositivi mobili di eShopOnContainers include un `EventToCommandBehavior` classe, che esegue un comando in risposta a un evento che si verificano. Questa classe deriva dal `BindableBehavior<T>` classe in modo che il comportamento può associare a ed eseguire un' `ICommand` specificato da un `Command` proprietà quando viene utilizzato il comportamento. L'esempio di codice seguente visualizza la classe `EventToCommandBehavior`:

```csharp
public class EventToCommandBehavior : BindableBehavior<View>  
{  
    ...  
    protected override void OnAttachedTo(View visualElement)  
    {  
        base.OnAttachedTo(visualElement);  

        var events = AssociatedObject.GetType().GetRuntimeEvents().ToArray();  
        if (events.Any())  
        {  
            _eventInfo = events.FirstOrDefault(e => e.Name == EventName);  
            if (_eventInfo == null)  
                throw new ArgumentException(string.Format(  
                        "EventToCommand: Can't find any event named '{0}' on attached type",   
                        EventName));  

            AddEventHandler(_eventInfo, AssociatedObject, OnFired);  
        }  
    }  

    protected override void OnDetachingFrom(View view)  
    {  
        if (_handler != null)  
            _eventInfo.RemoveEventHandler(AssociatedObject, _handler);  

        base.OnDetachingFrom(view);  
    }  

    private void AddEventHandler(  
            EventInfo eventInfo, object item, Action<object, EventArgs> action)  
    {  
        ...  
    }  

    private void OnFired(object sender, EventArgs eventArgs)  
    {  
        ...  
    }  
}
```

Il `OnAttachedTo` e `OnDetachingFrom` metodi vengono usati per registrare e annullare la registrazione di un gestore eventi per l'evento definito nel `EventName` proprietà. Quindi, quando viene generato l'evento, il `OnFired` metodo viene richiamato, che viene eseguito il comando.

Il vantaggio dell'uso di `EventToCommandBehavior` per eseguire un comando quando viene generato un evento, è che i comandi possono essere associati ai controlli che non sono stati progettati per interagire con i comandi. Inoltre, viene spostato il codice di gestione degli eventi in visualizzazione di modelli, in cui può essere sottoposta a unit testata.

#### <a name="invoking-behaviors-from-a-view"></a>Richiamare i comportamenti da una vista

Il `EventToCommandBehavior` è particolarmente utile per il collegamento di un comando a un controllo che non supporta i comandi. Ad esempio, il `ProfileView` Usa il `EventToCommandBehavior` per eseguire il `OrderDetailCommand` quando la [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) evento viene generato nel [ `ListView` ](xref:Xamarin.Forms.ListView) che elenca gli ordini dell'utente, come illustrato nel codice seguente:

```xaml
<ListView>  
    <ListView.Behaviors>  
        <behaviors:EventToCommandBehavior             
            EventName="ItemTapped"  
            Command="{Binding OrderDetailCommand}"  
            EventArgsConverter="{StaticResource ItemTappedEventArgsConverter}" />  
    </ListView.Behaviors>  
    ...  
</ListView>
```

In fase di esecuzione, il `EventToCommandBehavior` risponderà all'interazione con il [ `ListView` ](xref:Xamarin.Forms.ListView). Quando viene selezionato un elemento nel `ListView`, il [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped) , quindi, viene generato l'evento cui verrà eseguita la `OrderDetailCommand` nel `ProfileViewModel`. Per impostazione predefinita, gli argomenti dell'evento per l'evento vengono passati al comando. Questi dati viene convertiti come viene passato tra origine e destinazione per il convertitore specificato nella `EventArgsConverter` proprietà, che restituisce il [ `Item` ](xref:Xamarin.Forms.ItemTappedEventArgs.Item) del `ListView` dal [ `ItemTappedEventArgs` ](xref:Xamarin.Forms.ItemTappedEventArgs). Pertanto, quando la `OrderDetailCommand` viene eseguita, selezionato `Order` viene passato come parametro per l'azione registrata.

Per altre informazioni sui comportamenti, vedere [comportamenti](~/xamarin-forms/app-fundamentals/behaviors/index.md).

## <a name="summary"></a>Riepilogo

Il modello Model-View-ViewModel (MVVM) consente di separare correttamente la logica di business e la presentazione di un'applicazione dalla relativa interfaccia utente (UI). Mantenere una netta separazione tra logica dell'applicazione e l'interfaccia utente consente di risolvere numerosi problemi di sviluppo e rendono più semplice da testare, gestire e sviluppare un'applicazione. Viene anche notevolmente può migliorare le opportunità di riutilizzo di codice e consente agli sviluppatori le finestre di progettazione dell'interfaccia utente più facilmente per collaborare e quando si sviluppano le rispettive parti di un'app.

Modello dell'uso di MVVM, l'interfaccia utente dell'app e la logica di presentazione e business sottostante è suddivisa in tre classi distinte: la visualizzazione, che incapsula l'interfaccia utente e dell'interfaccia utente per la logica, il modello di visualizzazione, che incapsula la logica di presentazione e stato. e il modello che incapsula la logica di business e i dati dell'app.


## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
