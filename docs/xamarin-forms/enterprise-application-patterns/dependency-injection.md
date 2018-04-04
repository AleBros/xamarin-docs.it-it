---
title: Inserimento di dipendenze
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 8db8e5b756fe770bdf292ec03c28eb5ed54acf9e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="dependency-injection"></a>Inserimento di dipendenze

In genere, un costruttore di classe viene richiamato quando si crea un oggetto e tutti i valori necessari per l'oggetto vengono passati come argomenti al costruttore. Questo è un esempio di inserimento di dipendenze e in particolare, è noto come *inserimento costruttore*. Le dipendenze, che l'oggetto deve vengono inserite nel costruttore.

Specifica le dipendenze come tipi di interfaccia, inserimento di dipendenze consente la separazione dei tipi concreti dal codice che dipende da questi tipi. In genere Usa un contenitore che include un elenco di mapping tra le interfacce e tipi astratti e le registrazioni e i tipi concreti che implementano o estendono questi tipi.

Esistono anche altri tipi di inserimento di dipendenze, ad esempio *inserimento di setter di proprietà*, e *chiamate ai metodi*, ma sono meno di frequente sono visibili. Pertanto, si esaminerà questo capitolo esclusivamente sull'esecuzione di inserimento del costruttore con un contenitore dell'inserimento di dipendenza.

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>Introduzione all'inserimento di dipendenze

Inserimento di dipendenze è una versione specializzata del modello Inversion of Control (IoC), in cui il problema viene invertito è il processo per ottenere la dipendenza richiesta. Con l'inserimento di dipendenze, un'altra classe è responsabile per l'inserimento di dipendenze in un oggetto in fase di esecuzione. Nell'esempio di codice riportato di seguito viene illustrato come la `ProfileViewModel` classe è strutturata quando si utilizza l'inserimento di dipendenze:

```csharp
public class ProfileViewModel : ViewModelBase  
{  
    private IOrderService _orderService;  

    public ProfileViewModel(IOrderService orderService)  
    {  
        _orderService = orderService;  
    }  
    ...  
}
```

Il `ProfileViewModel` costruttore riceve un `IOrderService` istanza come argomento, inserito da un'altra classe. La dipendenza del sola la `ProfileViewModel` classe è un tipo interfaccia. Pertanto, il `ProfileViewModel` classe non ha alcuna conoscenza della classe che è responsabile della creazione di un'istanza di `IOrderService` oggetto. La classe che è responsabile della creazione di un'istanza di `IOrderService` dell'oggetto e l'inserimento nel `ProfileViewModel` classe, è noto come il *contenitore dell'inserimento di dipendenza*.

I contenitori di dipendenze di ridurre l'accoppiamento tra oggetti fornendo una struttura per creare un'istanza di istanze di classe e gestire la durata in base alla configurazione del contenitore. Durante la creazione di oggetti, il contenitore inserisce tutte le dipendenze che richiede l'oggetto al suo interno. Se queste dipendenze non è sono ancora create, il contenitore crea e consente di risolvere innanzitutto le relative dipendenze.

> [!NOTE]
> Inserimento di dipendenze può anche essere implementata manualmente tramite le factory. Tuttavia, l'utilizzo di un contenitore fornisce funzionalità aggiuntive, ad esempio la gestione della durata e la registrazione tramite l'analisi degli assembly.

Esistono diversi vantaggi rispetto all'utilizzo di un contenitore dell'inserimento di dipendenze:

-   Un contenitore Elimina la necessità di una classe individuare le relative dipendenze e gestire le loro durate.
-   Un contenitore consente il mapping delle dipendenze implementate senza modificare la classe.
-   Un contenitore facilita la testabilità consentendo le dipendenze essere esempio.
-   Un contenitore aumenta la gestibilità, consentendo di nuove classi essere facilmente aggiunte all'app.

Nel contesto di un'app xamarin. Forms che utilizza MVVM, un contenitore dell'inserimento di dipendenze in genere essere utilizzato per la registrazione e la risoluzione di visualizzazione di modelli e per la registrazione di servizi e inserendo le visualizzazione di modelli.

Sono disponibili molti contenitori di dipendenze, con l'app mobile eShopOnContainers utilizzando Autofac per gestire la creazione dell'istanza del modello di visualizzazione e le classi nell'app del servizio. Semplifica la creazione di App loosely-coupled Autofac e fornisce tutte le funzionalità comunemente reperibile in contenitori di dipendenze, inclusi i metodi per registrare i mapping dei tipi e istanze di un oggetto, risolvere gli oggetti, la gestione della durata degli oggetti e inserire oggetti dipendenti nei costruttori di oggetti che viene risolta. Per ulteriori informazioni su Autofac, vedere [Autofac](http://autofac.readthedocs.io/en/latest/index.html) su readthedocs.io.

In Autofac, il `IContainer` interfaccia fornisce il contenitore dell'inserimento di dipendenza. Figura 3-1 mostra le dipendenze quando si utilizza questo contenitore, che crea un'istanza di un `IOrderService` dell'oggetto e inserisce nel `ProfileViewModel` classe.

![](dependency-injection-images/dependencyinjection.png "Esempio di dipendenze quando si utilizza l'inserimento di dipendenze")

**Figura 3-1:** dipendenze quando si utilizza l'inserimento di dipendenze

In fase di esecuzione, il contenitore deve sapere quale implementazione del `IOrderService` interfaccia deve creare un'istanza, prima è possibile creare un'istanza di un `ProfileViewModel` oggetto. Questa operazione comporta:

-   Il contenitore decidere come creare un'istanza di un oggetto che implementa il `IOrderService` interfaccia. Questo è noto come *registrazione*.
-   Il contenitore crea un'istanza di oggetto che implementa il `IOrderService` interfaccia e `ProfileViewModel` oggetto. Questo è noto come *risoluzione*.

Infine, l'app verrà terminato di utilizzare il `ProfileViewModel` oggetto e saranno disponibili per l'operazione di garbage collection. A questo punto, il garbage collector deve eliminare il `IOrderService` se altre classi non condividono la stessa istanza dell'istanza.

> [!TIP]
> Scrivere codice indipendente dal contenitore. Provare a scrivere codice indipendente dal contenitore per separare l'app dal contenitore di dipendenza specifica viene utilizzato sempre.

## <a name="registration"></a>Registrazione

Prima di dipendenze possono essere inserite in un oggetto, i tipi delle dipendenze prima devono essere registrati con il contenitore. Registrazione di un tipo in genere prevede il passaggio del contenitore, un'interfaccia e un tipo concreto che implementa l'interfaccia.

Sono disponibili due modalità di registrazione di tipi e gli oggetti nel contenitore tramite codice:

-   Registrare un tipo o un mapping con il contenitore. Quando richiesto, il contenitore verrà compilata un'istanza del tipo specificato.
-   Registrare un oggetto esistente nel contenitore come singleton. Quando richiesto, il contenitore verrà restituito un riferimento all'oggetto esistente.

> [!TIP]
> I contenitori di dipendenze non sono sempre adatti. Inserimento di dipendenze introduce complessità e i requisiti che potrebbero non essere utile o appropriato per le applicazioni di piccole dimensioni. Se una classe dispone di dipendenze o non è una dipendenza per gli altri tipi, potrebbe senso per inserirlo nel contenitore. Inoltre, se una classe dispone di un singolo set di dipendenze che sono essenziali per il tipo e non verrà mai modificato, potrebbe non avere senso per inserirlo nel contenitore.

La registrazione di tipi che richiedono l'inserimento di dipendenze deve essere eseguita in un singolo metodo in un'applicazione e deve essere richiamato questo metodo nelle prime fasi del ciclo di vita dell'applicazione per assicurarsi che l'applicazione è compatibile con le dipendenze tra le relative classi. Nell'app mobile eShopOnContainers questa operazione viene eseguita dal `ViewModelLocator` classe quali compilazioni il `IContainer` dell'oggetto ed è l'unica classe nell'app che contiene un riferimento a tale oggetto. Esempio di codice seguente viene illustrato come l'app mobile eShopOnContainers dichiara il `IContainer` oggetto la `ViewModelLocator` classe:

```csharp
private static IContainer _container;
```

Tipi e le istanze vengono registrate nel `RegisterDependencies` metodo la `ViewModelLocator` classe. A questo scopo, creare prima un `ContainerBuilder` istanza, come illustrato nell'esempio di codice seguente:

```csharp
var builder = new ContainerBuilder();
```

Tipi e le istanze vengono quindi registrate con il `ContainerBuilder` oggetto e il codice riportato di seguito viene illustrato il formato più comune di registrazione del tipo:

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

Il `RegisterType` metodo illustrato di seguito esegue il mapping di un tipo di interfaccia a un tipo concreto. Indica il contenitore per creare un'istanza di un `RequestProvider` oggetto quando viene creata un'istanza di un oggetto che richiede un attacco intrusivo nel codice di un `IRequestProvider` tramite un costruttore.

Tipi concreti possono essere registrati direttamente senza un mapping da un tipo di interfaccia, come illustrato nell'esempio di codice seguente:

```csharp
builder.RegisterType<ProfileViewModel>();
```

Quando il `ProfileViewModel` tipo viene risolto, il contenitore inserirà le dipendenze necessarie.

Autofac consente inoltre la registrazione dell'istanza, in cui il contenitore è responsabile della gestione di un riferimento a un'istanza singleton di un tipo. Ad esempio, l'esempio di codice seguente viene illustrato come l'app mobile eShopOnContainers registra il tipo concreto da usare quando un `ProfileViewModel` istanza richiede un `IOrderService` istanza:

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

Il `RegisterType` metodo illustrato di seguito esegue il mapping di un tipo di interfaccia a un tipo concreto. Il `SingleInstance` metodo consente di configurare la registrazione in modo che ogni oggetto dipendente riceva la stessa istanza condivisa. Pertanto, una sola `OrderService` istanza presente nel contenitore, che viene condiviso da oggetti che richiedono un attacco intrusivo nel codice di un `IOrderService` tramite un costruttore.

La registrazione dell'istanza possono essere eseguite anche con il `RegisterInstance` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

Il `RegisterInstance` metodo illustrato di seguito crea un nuovo `OrderMockService` istanza e lo registra con il contenitore. Pertanto, una sola `OrderMockService` istanza presente nel contenitore, che viene condiviso da oggetti che richiedono un attacco intrusivo nel codice di un `IOrderService` tramite un costruttore.

Dopo la registrazione di tipo e l'istanza, il `IContainer` oggetto deve essere compilato, cui è illustrato nell'esempio di codice seguente:

```csharp
_container = builder.Build();
```

Richiamare il `Build` metodo il `ContainerBuilder` istanza compila un nuovo contenitore dell'inserimento di dipendenza che contiene le registrazioni che sono state apportate.

>💡 **Suggerimento**: considerare una `IContainer` come non modificabile. Mentre Autofac fornisce un `Update` per aggiornare le registrazioni in un contenitore esistente, questo metodo devono essere evitati laddove possibile. Esistono rischi alla modifica di un contenitore dopo che è stata creata, in particolare se il contenitore è stato utilizzato. Per ulteriori informazioni, vedere [prendere in considerazione un contenitore come non modificabile](http://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable) su readthedocs.io.

<a name="resolution" />

## <a name="resolution"></a>Risoluzione

Dopo la registrazione di un tipo, possono essere risolti o inserita come una dipendenza. Quando un tipo viene risolto e il contenitore deve creare una nuova istanza, inserisce le dipendenze nell'istanza.

In genere, quando un tipo viene risolto, una delle tre operazioni si verifica:

1.  Se il tipo non è stato registrato, il contenitore genera un'eccezione.
1.  Se il tipo è stato registrato come un singleton, il contenitore restituisce l'istanza singleton. Se questa è la prima volta il tipo viene chiamato per il contenitore crea se necessario e mantiene un riferimento a esso.
1.  Se il tipo non è stato registrato come un singleton, il contenitore restituisce una nuova istanza e non mantiene un riferimento a esso.

Nell'esempio di codice riportato di seguito viene illustrato come la `RequestProvider` tipo registrato in precedenza con Autofac può essere risolto:

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

In questo esempio, Autofac viene richiesto di risolvere il tipo concreto per la `IRequestProvider` tipo, insieme a eventuali dipendenze. In genere, il `Resolve` metodo viene chiamato quando è necessaria un'istanza di un tipo specifico. Per informazioni su come controllare la durata degli oggetti risolti, vedere [la gestione della durata degli oggetti risolto](#managing_the_lifetime_of_resolved_objects).

Esempio di codice seguente viene illustrato come l'app mobile eShopOnContainers crea un'istanza di tipi di modello di visualizzazione e le relative dipendenze:

```csharp
var viewModel = _container.Resolve(viewModelType);
```

In questo esempio, Autofac viene richiesto di risolvere il tipo di modello di visualizzazione per un modello di richiesta di visualizzazione e il contenitore verrà risolto anche eventuali dipendenze. Durante la risoluzione di `ProfileViewModel` tipo, per risolvere la dipendenza è un `IOrderService` oggetto. Pertanto, viene innanzitutto creata Autofac un `OrderService` dell'oggetto e quindi lo passa al costruttore del `ProfileViewModel` classe. Per ulteriori informazioni sulla modalità app mobile eShopOnContainers costruisce vista modellano e li associano alle viste, vedere [automaticamente la creazione di un modello di visualizzazione con un indicatore di posizione di visualizzazione modello](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

> [!NOTE]
> La registrazione e risoluzione dei tipi con un contenitore ha sulle prestazioni a causa di utilizzo del contenitore della reflection per la creazione di ogni tipo, soprattutto se le dipendenze sono viene ricostruite per la navigazione ogni pagina nell'app. Se sono presenti dipendenze molti o complete, può aumentare in modo significativo il costo della creazione.

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>Gestione della durata degli oggetti risolti

Dopo la registrazione di un tipo, il comportamento predefinito per Autofac è per creare una nuova istanza del tipo registrato ogni volta che il tipo viene risolto oppure quando il meccanismo di dipendenza inserisce le istanze in altre classi. In questo scenario, il contenitore non contiene un riferimento all'oggetto risolto. Tuttavia, quando si registra un'istanza, il comportamento predefinito per Autofac è per gestire la durata dell'oggetto come un singleton. Di conseguenza, l'istanza rimane nell'ambito, mentre il contenitore è nell'ambito e viene eliminato quando il contenitore esce dall'ambito e raccolti nel Garbage Collector è o quando il codice viene eliminato in modo esplicito il contenitore.

Un ambito di istanza Autofac consente di specificare il comportamento di singleton per un oggetto che Autofac Crea da un tipo registrato. Gli ambiti di istanza Autofac gestiscono la durata di oggetti creata dal contenitore. L'ambito di istanza predefinito per il `RegisterType` metodo è il `InstancePerDependency` ambito. Tuttavia, il `SingleInstance` ambito può essere utilizzato con il `RegisterType` (metodo), in modo che il contenitore crea o restituisce un'istanza singleton di un tipo quando si chiama il `Resolve` metodo. Esempio di codice seguente viene illustrato come Autofac viene richiesto di creare un'istanza singleton della `NavigationService` classe:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

La prima volta che il `INavigationService` interfaccia viene risolta, il contenitore crea un nuovo `NavigationService` dell'oggetto e mantiene un riferimento a esso. In qualsiasi successive risoluzioni del `INavigationService` interfaccia, il contenitore restituisce un riferimento di `NavigationService` oggetto creato in precedenza.

> [!NOTE]
> L'ambito SingleInstance Elimina gli oggetti creati quando il contenitore è stato eliminato.

Autofac include gli ambiti di istanza aggiuntive. Per ulteriori informazioni, vedere [ambito istanza](http://autofac.readthedocs.io/en/latest/lifetime/instance-scope.html) su readthedocs.io.

## <a name="summary"></a>Riepilogo

Inserimento di dipendenze consente la separazione di tipi concreti dal codice che dipende da questi tipi. In genere Usa un contenitore che include un elenco di mapping tra le interfacce e tipi astratti e le registrazioni e i tipi concreti che implementano o estendono questi tipi.

Semplifica la creazione di App loosely-coupled Autofac e fornisce tutte le funzionalità comunemente reperibile in contenitori di dipendenze, inclusi i metodi per registrare i mapping dei tipi e istanze di un oggetto, risolvere gli oggetti, la gestione della durata degli oggetti e inserire oggetti dipendenti nei costruttori di oggetti che viene risolta.


## <a name="related-links"></a>Collegamenti correlati

- [Scaricare eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
