---
title: Modello Model-View-ViewModel
description: Questo capitolo illustra il modo in cui l'app per dispositivi mobili eShopOnContainers usa il modello MVVM per separare chiaramente la logica aziendale e di presentazione dell'app dalla relativa interfaccia utente.
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d6c9b74c9abc1a2c493c31699b52969a7d129429
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760205"
---
# <a name="the-model-view-viewmodel-pattern"></a>Modello Model-View-ViewModel

L'esperienza di sviluppo di Novell. Forms comporta in genere la creazione di un'interfaccia utente in XAML e quindi l'aggiunta del code-behind che opera sull'interfaccia utente. Quando le app vengono modificate e aumentano le dimensioni e l'ambito, possono verificarsi problemi di manutenzione complessi. Questi problemi includono il stretto accoppiamento tra i controlli dell'interfaccia utente e la logica di business, che aumenta il costo di apportare modifiche all'interfaccia utente e la difficoltà di eseguire unit test di tale codice.

Il modello MVC (Model-View-ViewModel) consente di separare nettamente la logica di business e di presentazione di un'applicazione dalla relativa interfaccia utente (UI). Il mantenimento di una netta separazione tra la logica dell'applicazione e l'interfaccia utente consente di risolvere numerosi problemi di sviluppo e può semplificare la verifica, la manutenzione e l'evoluzione di un'applicazione. Può anche migliorare significativamente le opportunità di riutilizzo del codice e consente agli sviluppatori e ai progettisti di interfaccia utente di collaborare più facilmente durante lo sviluppo delle rispettive parti di un'app.

## <a name="the-mvvm-pattern"></a>Modello MVVM

Nel modello MVVM sono disponibili tre componenti principali: il modello, la visualizzazione e il modello di visualizzazione. Ognuno di essi svolge uno scopo distinto. Nella figura 2-1 sono illustrate le relazioni tra i tre componenti.

![](mvvm-images/mvvm.png "Modello MVVM")

**Figura 2-1**: Modello MVVM

Oltre a comprendere le responsabilità di ogni componente, è anche importante comprendere il modo in cui interagiscono tra loro. A un livello elevato, la vista "conosce" il modello di visualizzazione e il modello di visualizzazione "sa" del modello, ma il modello non è a conoscenza del modello di visualizzazione e il modello di visualizzazione non è a conoscenza della vista. Pertanto, il modello di visualizzazione consente di isolare la visualizzazione dal modello e consente al modello di evolversi indipendentemente dalla visualizzazione.

I vantaggi derivanti dall'uso del modello MVVM sono i seguenti:

- Se è presente un'implementazione del modello esistente che incapsula la logica di business esistente, può essere difficile o rischiosa modificarla. In questo scenario, il modello di visualizzazione funge da adattatore per le classi del modello e consente di evitare di apportare modifiche importanti al codice del modello.
- Gli sviluppatori possono creare unit test per il modello di visualizzazione e il modello, senza utilizzare la vista. Gli unit test per il modello di visualizzazione possono esercitare esattamente la stessa funzionalità utilizzata dalla visualizzazione.
- L'interfaccia utente dell'app può essere riprogettata senza toccare il codice, a condizione che la vista sia implementata interamente in XAML. Pertanto, una nuova versione della vista dovrebbe funzionare con il modello di visualizzazione esistente.
- I progettisti e gli sviluppatori possono lavorare in modo indipendente e simultaneo sui rispettivi componenti durante il processo di sviluppo. I progettisti possono concentrarsi sulla visualizzazione, mentre gli sviluppatori possono lavorare sul modello di visualizzazione e sui componenti del modello.

La chiave per l'uso di MVVM è la comprensione del modo in cui il codice dell'app viene fattorizzato nelle classi corrette e nella comprensione del modo in cui le classi interagiscono. Le sezioni seguenti illustrano le responsabilità di ognuna delle classi nel modello MVVM.

### <a name="view"></a>Visualizza

La visualizzazione è responsabile della definizione della struttura, del layout e dell'aspetto degli elementi visualizzati dall'utente sullo schermo. Idealmente, ogni vista è definita in XAML, con un code-behind limitato che non contiene la logica di business. Tuttavia, in alcuni casi, il code-behind potrebbe contenere la logica dell'interfaccia utente che implementa il comportamento visivo difficile da esprimere in XAML, ad esempio le animazioni.

In un'applicazione Novell. Forms una vista è in genere [`Page`](xref:Xamarin.Forms.Page)una classe derivata [`ContentView`](xref:Xamarin.Forms.ContentView)da o derivata da. Tuttavia, le visualizzazioni possono essere rappresentate anche da un modello di dati, che specifica gli elementi dell'interfaccia utente da usare per rappresentare visivamente un oggetto quando viene visualizzato. Un modello di dati come vista non dispone di code-behind ed è progettato per l'associazione a un tipo di modello di visualizzazione specifico.

> [!TIP]
> Evitare di abilitare e disabilitare gli elementi dell'interfaccia utente nel code-behind. Verificare che i modelli di visualizzazione siano responsabili della definizione di modifiche di stato logiche che interessano alcuni aspetti della visualizzazione della vista, ad esempio se un comando è disponibile o se un'operazione è in sospeso. Quindi, abilitare e disabilitare gli elementi dell'interfaccia utente mediante associazione per visualizzare le proprietà del modello, anziché abilitarle e disabilitarle nel code-behind.

Sono disponibili diverse opzioni per l'esecuzione di codice nel modello di visualizzazione in risposta alle interazioni sulla vista, ad esempio la selezione di un pulsante o di un elemento. Se un controllo supporta i comandi, la `Command` proprietà del controllo può essere associata a dati a una `ICommand` proprietà nel modello di visualizzazione. Quando viene richiamato il comando del controllo, il codice nel modello di visualizzazione verrà eseguito. Oltre ai comandi, i comportamenti possono essere collegati a un oggetto nella vista e possono restare in ascolto di un comando da richiamare o dell'evento da generare. In risposta, il comportamento può quindi richiamare un `ICommand` oggetto nel modello di visualizzazione o un metodo nel modello di visualizzazione.

### <a name="viewmodel"></a>ViewModel

Il modello di visualizzazione implementa le proprietà e i comandi a cui è possibile associare i dati della visualizzazione e notifica la visualizzazione di eventuali modifiche di stato tramite gli eventi di notifica delle modifiche. Le proprietà e i comandi forniti dal modello di visualizzazione definiscono la funzionalità che verrà offerta dall'interfaccia utente, ma la visualizzazione determina la modalità di visualizzazione della funzionalità.

> [!TIP]
> Mantieni la risposta dell'interfaccia utente con le operazioni asincrone. Le app per dispositivi mobili devono tenere sbloccato il thread dell'interfaccia utente per migliorare la percezione delle prestazioni dell'utente. Pertanto, nel modello di visualizzazione, utilizzare i metodi asincroni per le operazioni di I/O e generare eventi per notificare in modo asincrono le visualizzazioni delle modifiche delle proprietà.

Il modello di visualizzazione è inoltre responsabile del coordinamento delle interazioni della vista con le classi del modello richieste. Esiste in genere una relazione uno-a-molti tra il modello di visualizzazione e le classi del modello. Il modello di visualizzazione potrebbe scegliere di esporre le classi del modello direttamente alla vista in modo che i controlli nella visualizzazione possano associarli direttamente. In questo caso, le classi del modello devono essere progettate per supportare data binding e modificare gli eventi di notifica.

Ogni modello di visualizzazione fornisce i dati di un modello in un modulo che può essere utilizzato facilmente dalla visualizzazione. A tale scopo, il modello di visualizzazione a volte esegue la conversione dei dati. L'inserimento di questa conversione dei dati nel modello di visualizzazione è una scelta consigliata, perché fornisce proprietà a cui è possibile associare la visualizzazione. Ad esempio, il modello di visualizzazione potrebbe combinare i valori di due proprietà per renderlo più semplice da visualizzare nella visualizzazione.

> [!TIP]
> Centralizzare le conversioni dei dati in un livello di conversione. È anche possibile usare i convertitori come un livello di conversione dati separato che si trova tra il modello di visualizzazione e la visualizzazione. Questa operazione può essere necessaria, ad esempio, quando i dati richiedono una formattazione speciale non fornita dal modello di visualizzazione.

Affinché il modello di visualizzazione partecipi a data binding bidirezionali con la vista, le relative proprietà devono generare l' `PropertyChanged` evento. I modelli di visualizzazione soddisfano questo requisito `INotifyPropertyChanged` implementando l'interfaccia e `PropertyChanged` generando l'evento quando viene modificata una proprietà.

Per le raccolte, è disponibile la `ObservableCollection<T>` visualizzazione intuitiva. Questa raccolta implementa la notifica modificata della raccolta, evitando allo sviluppatore di dover implementare `INotifyCollectionChanged` l'interfaccia nelle raccolte.

### <a name="model"></a>Modello

Le classi modello sono classi non visive che incapsulano i dati dell'app. Pertanto, il modello può essere considerato come la rappresentazione del modello di dominio dell'applicazione, che in genere include un modello di dati insieme alla logica di business e di convalida. Esempi di oggetti modello includono Data Transfer Objects (dto), Plain Old CLR Objects (POCO) e oggetti proxy e entità generati.

Le classi modello vengono in genere usate insieme ai servizi o ai repository che incapsulano l'accesso ai dati e la memorizzazione nella cache.

## <a name="connecting-view-models-to-views"></a>Connessione di modelli di visualizzazione alle viste

I modelli di visualizzazione possono essere connessi alle viste usando le funzionalità di data binding di Novell. Forms. Esistono molti approcci che possono essere usati per costruire viste e visualizzare modelli e associarli in fase di esecuzione. Questi approcci rientrano in due categorie, note come prima composizione della vista e visualizzano la prima composizione del modello. La scelta tra Visualizza prima composizione e visualizza prima composizione modello è un problema di preferenza e complessità. Tuttavia, tutti gli approcci condividono lo stesso obiettivo, ovvero la visualizzazione deve disporre di un modello di visualizzazione assegnato alla relativa proprietà BindingContext.

Con la prima composizione di visualizzazione l'app è costituita concettualmente da visualizzazioni che si connettono ai modelli di visualizzazione da cui dipendono. Il vantaggio principale di questo approccio è che semplifica la creazione di app Unite a regime di controllo libero, perché i modelli di visualizzazione non hanno alcuna dipendenza dalle visualizzazioni stesse. È anche facile comprendere la struttura dell'app seguendo la relativa struttura visiva, anziché dover tenere traccia dell'esecuzione del codice per comprendere come vengono create e associate le classi. Inoltre, la prima costruzione di visualizzazione è allineata con il sistema di navigazione Novell. Forms, responsabile della creazione di pagine quando si verifica la navigazione, rendendo complessa la prima composizione del modello di visualizzazione e non allineata alla piattaforma.

Con la prima composizione del modello di visualizzazione l'app è costituita concettualmente dai modelli di visualizzazione, con un servizio responsabile dell'individuazione della vista per un modello di visualizzazione. La prima composizione del modello di visualizzazione risulta più naturale per alcuni sviluppatori, perché la creazione della vista può essere ricavata, consentendo loro di concentrarsi sulla struttura logica non dell'interfaccia utente dell'app. Consente inoltre di creare modelli di visualizzazione da altri modelli di visualizzazione. Tuttavia, questo approccio è spesso complesso e può diventare difficile capire come vengono create e associate le varie parti dell'app.

> [!TIP]
> Mantieni i modelli di visualizzazione e le visualizzazioni indipendenti. Il binding di viste a una proprietà in un'origine dati deve essere la dipendenza principale della vista nel modello di visualizzazione corrispondente. In particolare, non fare riferimento ai tipi di [`Button`](xref:Xamarin.Forms.Button) vista [`ListView`](xref:Xamarin.Forms.ListView), ad esempio e, dai modelli di visualizzazione. Seguendo i principi descritti in questo articolo, i modelli di visualizzazione possono essere testati in isolamento, riducendo di conseguenza la probabilità di errori software limitando l'ambito.

Nelle sezioni seguenti vengono illustrati gli approcci principali per la connessione di modelli di visualizzazione alle viste.

### <a name="creating-a-view-model-declaratively"></a>Creazione di un modello di visualizzazione in modo dichiarativo

L'approccio più semplice consiste nel fare in modo che la visualizzazione dichiarata un'istanza del modello di visualizzazione corrispondente in XAML. Quando la vista viene costruita, viene creato anche l'oggetto modello di visualizzazione corrispondente. Questo metodo viene dimostrato nell'esempio di codice seguente:

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

Quando viene creato, un'istanza `LoginViewModel` di viene automaticamente costruita e [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)impostata come oggetto della visualizzazione. [`ContentPage`](xref:Xamarin.Forms.ContentPage)

Questa costruzione dichiarativa e l'assegnazione del modello di visualizzazione da parte della vista hanno il vantaggio di essere semplice, ma presenta lo svantaggio che richiede un costruttore predefinito (senza parametri) nel modello di visualizzazione.

### <a name="creating-a-view-model-programmatically"></a>Creazione di un modello di visualizzazione a livello di codice

Una vista può includere codice nel file code-behind che determina l'assegnazione del modello di visualizzazione alla relativa [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) proprietà. Questa operazione viene spesso eseguita nel costruttore della vista, come illustrato nell'esempio di codice seguente:

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

La costruzione e l'assegnazione a livello di codice del modello di visualizzazione all'interno del code-behind della vista hanno il vantaggio di essere semplice. Tuttavia, lo svantaggio principale di questo approccio è che la visualizzazione deve fornire il modello di visualizzazione con tutte le dipendenze necessarie. L'utilizzo di un contenitore di inserimento di dipendenze consente di mantenere un accoppiamento libero tra la vista e il modello di visualizzazione. Per altre informazioni, vedere [inserimento delle dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

### <a name="creating-a-view-defined-as-a-data-template"></a>Creazione di una vista definita come modello di dati

Una vista può essere definita come modello di dati e associata a un tipo di modello di visualizzazione. I modelli di dati possono essere definiti come risorse oppure possono essere definiti inline all'interno del controllo che visualizzerà il modello di visualizzazione. Il contenuto del controllo è l'istanza del modello di visualizzazione e il modello di dati viene usato per rappresentarlo visivamente. Questa tecnica è un esempio di situazione in cui viene creata prima un'istanza del modello di visualizzazione, seguita dalla creazione della vista.

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>Creazione automatica di un modello di visualizzazione con un localizzatore di modelli di visualizzazione

Un localizzatore di modelli di visualizzazione è una classe personalizzata che gestisce la creazione di istanze dei modelli di visualizzazione e la relativa associazione alle viste. Nell'app per dispositivi mobili eShopOnContainers la `ViewModelLocator` classe dispone di una proprietà associata `AutoWireViewModel`,, che viene usata per associare i modelli di visualizzazione alle viste. Nel codice XAML della visualizzazione questa proprietà associata è impostata su true per indicare che il modello di visualizzazione deve essere connesso automaticamente alla vista, come illustrato nell'esempio di codice seguente:

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

La `AutoWireViewModel` proprietà è una proprietà associabile inizializzata su false e quando il relativo valore viene modificato `OnAutoWireViewModelChanged` , viene chiamato il gestore eventi. Questo metodo risolve il modello di visualizzazione per la visualizzazione. Nell'esempio di codice seguente viene illustrato come ottenere questo risultato:

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

Il `OnAutoWireViewModelChanged` metodo tenta di risolvere il modello di visualizzazione utilizzando un approccio basato sulle convenzioni. Questa convenzione presuppone che:

- I modelli di visualizzazione si trovano nello stesso assembly dei tipi di visualizzazione.
- Le visualizzazioni si trovano in un. Spazio dei nomi figlio views.
- I modelli di visualizzazione si trovano in un. Spazio dei nomi figlio ViewModels.
- I nomi dei modelli di visualizzazione corrispondono ai nomi delle visualizzazioni e terminano con "ViewModel".

Infine, il `OnAutoWireViewModelChanged` metodo imposta l' [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) oggetto del tipo di visualizzazione sul tipo di modello di visualizzazione risolto. Per ulteriori informazioni sulla risoluzione del tipo di modello di visualizzazione, vedere [risoluzione](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution).

Questo approccio offre il vantaggio che un'app dispone di una singola classe responsabile della creazione di istanze dei modelli di visualizzazione e della relativa connessione alle viste.

> [!TIP]
> Usare un localizzatore di modelli di visualizzazione per semplificare la sostituzione. Un localizzatore di modello di visualizzazione può essere usato anche come punto di sostituzione per le implementazioni alternative delle dipendenze, ad esempio per i dati di testing unità o della fase di progettazione.

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>Aggiornamento di viste in risposta alle modifiche nel modello o modello di visualizzazione sottostante

Tutte le classi del modello di visualizzazione e del modello accessibili a una vista devono `INotifyPropertyChanged` implementare l'interfaccia. L'implementazione di questa interfaccia in un modello di visualizzazione o in una classe modello consente alla classe di fornire notifiche di modifica a tutti i controlli associati a dati nella vista quando viene modificato il valore della proprietà sottostante.

Le app devono essere progettate per l'uso corretto della notifica di modifica delle proprietà, rispettando i requisiti seguenti:

- Generazione sempre di `PropertyChanged` un evento se il valore di una proprietà pubblica viene modificato. Non presupporre che la generazione `PropertyChanged` dell'evento possa essere ignorata a causa della conoscenza del modo in cui si verifica l'associazione XAML.
- Generazione sempre di `PropertyChanged` un evento per qualsiasi proprietà calcolata i cui valori sono utilizzati da altre proprietà nel modello o nel modello di visualizzazione.
- Generare sempre l' `PropertyChanged` evento alla fine del metodo che apporta una modifica alla proprietà o quando l'oggetto è in uno stato sicuro. La generazione dell'evento interrompe l'operazione richiamando in modo sincrono i gestori dell'evento. Se questa situazione si verifica durante un'operazione, è possibile che l'oggetto venga esposto alle funzioni di callback quando si trova in uno stato non sicuro, parzialmente aggiornato. Inoltre, è possibile che le modifiche a catena vengano attivate dagli `PropertyChanged` eventi. Le modifiche di propagazione in genere richiedono il completamento degli aggiornamenti prima che la modifica a catena possa essere eseguita in modo sicuro.
- Non viene mai `PropertyChanged` generato un evento se la proprietà non viene modificata. Ciò significa che è necessario confrontare i valori vecchi e nuovi prima di generare `PropertyChanged` l'evento.
- Non generare mai `PropertyChanged` l'evento durante l'inizializzazione di una proprietà da parte del costruttore di un modello di visualizzazione. I controlli con associazione a dati nella vista non hanno sottoscritto la ricezione delle notifiche di modifica a questo punto.
- Non generare mai più di `PropertyChanged` un evento con lo stesso argomento del nome di proprietà all'interno di una singola chiamata sincrona di un metodo pubblico di una classe. Ad esempio, data una `NumberOfItems` proprietà il cui archivio di backup è `_numberOfItems` il campo, `_numberOfItems` se un metodo incrementa 50 volte durante l'esecuzione di un ciclo, deve generare solo una notifica di modifica della `NumberOfItems` proprietà sulla proprietà una sola volta, al termine di tutto il lavoro. Per i metodi asincroni, generare `PropertyChanged` l'evento per un nome di proprietà specificato in ogni segmento sincrono di una catena di continuazione asincrona.

L'app per dispositivi mobili eShopOnContainers `ExtendedBindableObject` usa la classe per fornire le notifiche di modifica, come illustrato nell'esempio di codice seguente:

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

La [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe Novell. Form implementa l' `INotifyPropertyChanged` interfaccia e fornisce un [`OnPropertyChanged`](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String)) metodo. La `ExtendedBindableObject` classe fornisce il `RaisePropertyChanged` metodo per richiamare la notifica delle modifiche alle proprietà e, in questo caso, USA `BindableObject` la funzionalità fornita dalla classe.

Ogni classe del modello di visualizzazione nell'app per dispositivi mobili eShopOnContainers deriva `ViewModelBase` dalla classe, che a sua volta deriva `ExtendedBindableObject` dalla classe. Pertanto, ogni classe del modello di visualizzazione `RaisePropertyChanged` usa il metodo `ExtendedBindableObject` nella classe per fornire la notifica delle modifiche alle proprietà. L'esempio di codice seguente illustra come l'app per dispositivi mobili eShopOnContainers richiama la notifica di modifica della proprietà usando un'espressione lambda:

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

Si noti che l'uso di un'espressione lambda in questo modo comporta un piccolo costo in termini di prestazioni perché l'espressione lambda deve essere valutata per ogni chiamata. Sebbene il costo delle prestazioni sia ridotto e non influisca in genere su un'app, è possibile che i costi si accumulino quando sono presenti molte notifiche di modifica. Tuttavia, il vantaggio di questo approccio è che fornisce la sicurezza dei tipi in fase di compilazione e il supporto del refactoring quando si rinominano le proprietà.

## <a name="ui-interaction-using-commands-and-behaviors"></a>Interazione dell'interfaccia utente con comandi e comportamenti

Nelle app per dispositivi mobili, le azioni vengono in genere richiamate in risposta a un'azione dell'utente, ad esempio un clic su un pulsante, che può essere implementato creando un gestore eventi nel file code-behind. Tuttavia, nel modello MVVM, la responsabilità dell'implementazione dell'azione è il modello di visualizzazione e l'inserimento del codice nel code-behind deve essere evitato.

I comandi costituiscono un modo pratico per rappresentare le azioni che possono essere associate ai controlli nell'interfaccia utente. Incapsulano il codice che implementa l'azione e consentono di mantenerlo separato dalla relativa rappresentazione visiva nella visualizzazione. Novell. Forms include controlli che possono essere connessi in modo dichiarativo a un comando e questi controlli richiameranno il comando quando l'utente interagisce con il controllo.

I comportamenti consentono inoltre la connessione dichiarativa dei controlli a un comando. Tuttavia, i comportamenti possono essere usati per richiamare un'azione associata a un intervallo di eventi generati da un controllo. Pertanto, i comportamenti affrontano molti degli stessi scenari dei controlli abilitati per i comandi, garantendo al tempo stesso un maggiore livello di flessibilità e controllo. Inoltre, i comportamenti possono essere usati anche per associare oggetti comando o metodi con controlli non specificamente progettati per interagire con i comandi.

### <a name="implementing-commands"></a>Implementazione di comandi

I modelli di visualizzazione in genere espongono le proprietà del comando per l'associazione dalla vista, ovvero istanze `ICommand` di oggetti che implementano l'interfaccia. Un numero di controlli Novell. Forms fornisce `Command` una proprietà che può essere associata a un `ICommand` oggetto fornito dal modello di visualizzazione. L' `ICommand` interfaccia definisce un `Execute` metodo che incapsula l'operazione stessa, un `CanExecute` metodo, che indica se il comando può essere richiamato e un `CanExecuteChanged` evento che si verifica quando si verificano modifiche che influiscono sull'eventuale il comando deve essere eseguito. Le [`Command`](xref:Xamarin.Forms.Command) classi [`Command<T>`](xref:Xamarin.Forms.Command) e, fornite da Novell. Forms, implementano l' `ICommand` interfaccia `T` , dove è il tipo degli argomenti `Execute` per `CanExecute`e.

All'interno di un modello di visualizzazione deve essere presente un oggetto [`Command`](xref:Xamarin.Forms.Command) di [`Command<T>`](xref:Xamarin.Forms.Command) tipo o per ogni proprietà pubblica nel modello di visualizzazione `ICommand`di tipo. Il `Command` costruttore `Command<T>` o richiede un `Action` oggetto callback che viene chiamato quando viene `ICommand.Execute` richiamato il metodo. Il `CanExecute` metodo è un parametro del costruttore facoltativo e è un `Func` oggetto che restituisce `bool`un oggetto.

Il codice seguente mostra come un' [`Command`](xref:Xamarin.Forms.Command) istanza di, che rappresenta un comando Register, viene costruita specificando un delegato per `Register` il metodo del modello di visualizzazione:

```csharp
public ICommand RegisterCommand => new Command(Register);
```

Il comando viene esposto alla visualizzazione tramite una proprietà che restituisce un riferimento a un oggetto `ICommand`. Quando il `Execute` metodo viene chiamato [`Command`](xref:Xamarin.Forms.Command) sull'oggetto, viene semplicemente trasmessa la chiamata al metodo nel modello di visualizzazione tramite il delegato specificato nel `Command` costruttore.

Un metodo asincrono può essere richiamato da un comando usando le `async` parole `await` chiave e `Execute` quando si specifica il delegato del comando. Indica che il callback è un `Task` e deve essere atteso. Il codice seguente, ad esempio, Mostra come [`Command`](xref:Xamarin.Forms.Command) un'istanza di, che rappresenta un comando di accesso, viene costruita specificando un delegato al `SignInAsync` metodo di visualizzazione del modello:

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

I parametri possono essere passati alle `Execute` azioni `CanExecute` e usando la [`Command<T>`](xref:Xamarin.Forms.Command) classe per creare un'istanza del comando. Il codice seguente, ad esempio, Mostra come `Command<T>` un'istanza viene utilizzata per indicare che `NavigateAsync` il metodo richiederà un argomento di `string`tipo:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

In entrambe le [`Command`](xref:Xamarin.Forms.Command) classi [`Command<T>`](xref:Xamarin.Forms.Command) e `CanExecute` , il delegato al metodo in ogni costruttore è facoltativo. Se un delegato non è specificato, `Command` restituirà `true` per `CanExecute`. Tuttavia, il modello di visualizzazione può indicare una modifica nello `CanExecute` stato del comando chiamando il `ChangeCanExecute` metodo sull' `Command` oggetto. In questo modo `CanExecuteChanged` viene generato l'evento. Tutti i controlli nell'interfaccia utente associati al comando aggiorneranno lo stato abilitato in modo da riflettere la disponibilità del comando associato a dati.

#### <a name="invoking-commands-from-a-view"></a>Richiamo di comandi da una vista

Nell'esempio di codice seguente viene illustrato [`Grid`](xref:Xamarin.Forms.Grid) il modo `LoginView` in cui un oggetto `RegisterCommand` nell'oggetto `LoginViewModel` viene associato all'oggetto [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) nella classe utilizzando un'istanza:

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

È anche possibile definire facoltativamente un parametro di comando utilizzando [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) la proprietà. Il tipo dell'argomento previsto è specificato nei `Execute` metodi di destinazione e. `CanExecute` [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) Richiama automaticamente il comando di destinazione quando l'utente interagisce con il controllo associato. Il parametro del comando, se specificato, viene passato come argomento al `Execute` delegato del comando.

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>Implementazione dei comportamenti

I comportamenti consentono di aggiungere funzionalità ai controlli dell'interfaccia utente senza dover sottometterle in sottoclasse. La funzionalità viene invece implementata in una classe di comportamento e associata al controllo come se fosse parte del controllo stesso. I comportamenti consentono di implementare il codice che sarebbe normalmente necessario scrivere come code-behind, perché interagisce direttamente con l'API del controllo, in modo che possa essere collegato in modo conciso al controllo e incluso in un pacchetto per il riutilizzo in più di una vista o un'app. Nel contesto di MVVM, i comportamenti sono un approccio utile per connettere i controlli ai comandi.

Un comportamento associato a un controllo tramite le proprietà associate è noto come *comportamento associato*. Il comportamento può quindi usare l'API esposta dell'elemento a cui è collegata per aggiungere funzionalità a tale controllo, o altri controlli, nella struttura ad albero visuale della visualizzazione. L'app per dispositivi mobili eShopOnContainers `LineColorBehavior` contiene la classe, che è un comportamento associato. Per ulteriori informazioni su questo comportamento, vedere [visualizzazione degli errori di convalida](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors).

Un comportamento Novell. Forms è una classe che deriva dalla [`Behavior`](xref:Xamarin.Forms.Behavior) classe o [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) , dove `T` è il tipo del controllo a cui deve essere applicato il comportamento. Queste classi forniscono `OnAttachedTo` i `OnDetachingFrom` metodi e, che devono essere sottoposti a override per fornire la logica che verrà eseguita quando il comportamento viene collegato e scollegato dai controlli.

Nell'app per dispositivi mobili eShopOnContainers la `BindableBehavior<T>` classe deriva [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) dalla classe. Lo scopo della `BindableBehavior<T>` classe è fornire una classe base per i comportamenti di Novell. Forms che richiedono l' [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) impostazione del comportamento sul controllo associato.

La `BindableBehavior<T>` classe fornisce un metodo sottoponibile a override [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) `OnAttachedTo` che imposta l'oggetto del comportamento e un `OnDetachingFrom` metodo sottoponibile a `BindingContext`override che pulisce l'oggetto. La classe archivia anche un riferimento al controllo associato nella proprietà `AssociatedObject`.

L'app per dispositivi mobili eShopOnContainers `EventToCommandBehavior` include una classe, che esegue un comando in risposta a un evento che si verifica. Questa classe deriva dalla `BindableBehavior<T>` classe in modo che il comportamento possa essere associato a ed eseguire un `ICommand` oggetto specificato da una `Command` proprietà quando viene utilizzato il comportamento. L'esempio di codice seguente visualizza la classe `EventToCommandBehavior`:

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

I `OnAttachedTo` metodi `OnDetachingFrom` e vengono utilizzati per registrare e annullare la registrazione di un gestore eventi per `EventName` l'evento definito nella proprietà. Quindi, quando viene generato l'evento, `OnFired` viene richiamato il metodo, che esegue il comando.

Il vantaggio di usare per `EventToCommandBehavior` eseguire un comando quando viene generato un evento è che i comandi possono essere associati a controlli che non sono stati progettati per interagire con i comandi. Inoltre, sposta il codice di gestione degli eventi per visualizzare i modelli, dove possono essere unit test.

#### <a name="invoking-behaviors-from-a-view"></a>Richiamo di comportamenti da una vista

È `EventToCommandBehavior` particolarmente utile per alleghiare un comando a un controllo che non supporta i comandi. Ad `ProfileView` esempio, `OrderDetailCommand` `EventToCommandBehavior` USA per eseguire quando l' [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) evento viene attivato nell'oggetto [`ListView`](xref:Xamarin.Forms.ListView) che elenca gli ordini dell'utente, come illustrato nel codice seguente:

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

In fase di esecuzione `EventToCommandBehavior` , risponderà all'interazione con [`ListView`](xref:Xamarin.Forms.ListView)il. Quando si seleziona `ListView`un elemento in, l' [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) evento viene attivato `OrderDetailCommand` , che eseguirà in `ProfileViewModel`. Per impostazione predefinita, gli argomenti dell'evento per l'evento vengono passati al comando. Questi dati vengono convertiti come passati tra l'origine e la destinazione dal convertitore specificato nella `EventArgsConverter` proprietà, che restituisce l'oggetto [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) dell' `ListView` oggetto da [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs). Pertanto, quando `OrderDetailCommand` viene eseguito, l'oggetto selezionato `Order` viene passato come parametro all'azione registrata.

Per ulteriori informazioni sui comportamenti, vedere [comportamenti](~/xamarin-forms/app-fundamentals/behaviors/index.md).

## <a name="summary"></a>Riepilogo

Il modello MVC (Model-View-ViewModel) consente di separare nettamente la logica di business e di presentazione di un'applicazione dalla relativa interfaccia utente (UI). Il mantenimento di una netta separazione tra la logica dell'applicazione e l'interfaccia utente consente di risolvere numerosi problemi di sviluppo e può semplificare la verifica, la manutenzione e l'evoluzione di un'applicazione. Può anche migliorare significativamente le opportunità di riutilizzo del codice e consente agli sviluppatori e ai progettisti di interfaccia utente di collaborare più facilmente durante lo sviluppo delle rispettive parti di un'app.

Usando il modello MVVM, l'interfaccia utente dell'app e la presentazione sottostante e la logica di business sono separate in tre classi separate: la vista, che incapsula l'interfaccia utente e la logica dell'interfaccia utente. modello di visualizzazione che incapsula la logica di presentazione e lo stato; e il modello, che incapsula la logica di business e i dati dell'app.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
