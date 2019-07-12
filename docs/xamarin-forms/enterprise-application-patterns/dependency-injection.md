---
title: Inserimento di dipendenze
description: In questo capitolo illustra come l'app per dispositivi mobili di eShopOnContainers Usa inserimento delle dipendenze per disaccoppiare tipi concreti dal codice che dipende da questi tipi.
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 97b95ccb3e756f02c945adc63b9e173a9f9e0226
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832682"
---
# <a name="dependency-injection"></a>Inserimento di dipendenze

In genere, viene richiamato un costruttore di classe quando si crea un oggetto e tutti i valori necessari per l'oggetto vengono passati come argomenti al costruttore. Questo è un esempio di inserimento delle dipendenze e in particolare è detta *inserimento del costruttore*. Le dipendenze che dell'oggetto deve vengono inserite nel costruttore.

Specifica le dipendenze come tipi di interfaccia, inserimento di dipendenze consente la separazione dei tipi concreti dal codice che dipende da questi tipi. In genere Usa un contenitore che include un elenco delle registrazioni e i mapping tra le interfacce e tipi astratti e i tipi concreti che implementano o estendono questi tipi.

Sono disponibili anche altri tipi di inserimento delle dipendenze, ad esempio *inserimento di setter di proprietà*, e *inserimento chiamata di metodo*, ma sono meno di frequente sono visibili. Pertanto, in questo capitolo verrà incentrarsi esclusivamente sull'esecuzione di inserimento del costruttore con un contenitore di inserimento delle dipendenze.

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>Introduzione all'inserimento delle dipendenze

Inserimento di dipendenze è una versione specializzata del modello Inversion of Control (IoC), in cui il problema viene invertito è il processo per ottenere la dipendenza richiesta. Con inserimento delle dipendenze, un'altra classe è responsabile dell'inserimento di dipendenze in un oggetto in fase di esecuzione. Nell'esempio di codice riportato di seguito viene illustrato come il `ProfileViewModel` classe è strutturata quando si usa l'inserimento delle dipendenze:

```csharp
public class ProfileViewModel : ViewModelBase  
{  
    private IOrderService _orderService;  

    public ProfileViewModel(IOrderService orderService)  
    {  
        _orderService = orderService;  
    }  
    ...  
}
```

Il `ProfileViewModel` costruttore riceve un `IOrderService` istanza come argomento, inserito da un'altra classe. L'unica dipendenza nel `ProfileViewModel` classe è nel tipo di interfaccia. Pertanto, il `ProfileViewModel` classe non dispone di alcuna conoscenza della classe che è responsabile della creazione di un'istanza di `IOrderService` oggetto. La classe che è responsabile della creazione di un'istanza di `IOrderService` dell'oggetto e l'inserimento nel `ProfileViewModel` classe, è noto come il *contenitore dell'inserimento delle dipendenze*.

I contenitori di dipendenze di ridurre l'accoppiamento tra gli oggetti, fornendo una funzionalità per creare un'istanza di istanze della classe e gestirne la durata in base alla configurazione del contenitore. Durante la creazione di oggetti, il contenitore inserisce tutte le dipendenze che richiede l'oggetto al suo interno. Se queste dipendenze non sono ancora state create, il contenitore viene creato e risolve prima di tutto le relative dipendenze.

> [!NOTE]
> Inserimento delle dipendenze può anche essere implementata manualmente tramite le factory. Utilizzo di un contenitore offre tuttavia funzionalità aggiuntive, ad esempio la gestione della durata e la registrazione tramite l'analisi di assembly.

Esistono diversi vantaggi rispetto all'utilizzo di un contenitore di inserimento delle dipendenze:

-   Un contenitore Elimina la necessità di una classe localizzarne le dipendenze e gestire le loro durate.
-   Un contenitore consente il mapping delle dipendenze implementate senza influire sulla classe.
-   Un contenitore facilita la testabilità consentendo le dipendenze essere fittizie.
-   Un contenitore aumenta la gestibilità, consentendo a nuove classi da aggiungere con facilità all'app.

Nel contesto di un'app xamarin. Forms che utilizza MVVM, un contenitore di inserimento delle dipendenze verrà utilizzato in genere per la registrazione e la risoluzione di visualizzazione di modelli e per la registrazione di servizi e li inserisce nei modelli di visualizzazione.

Sono disponibili più contenitori di inserimento delle dipendenze, con l'app per dispositivi mobili di eShopOnContainers Usa Autofac per gestire la creazione dell'istanza del modello di visualizzazione e le classi nell'app del servizio. Semplifica la creazione di App loosely-coupled Autofac e fornisce tutte le funzionalità comunemente presente nei contenitori di inserimento delle dipendenze, inclusi i metodi per registrare i mapping dei tipi e istanze di oggetti, risolvere gli oggetti, la gestione della durata degli oggetti e inserire oggetti dipendenti nei costruttori di oggetti che viene risolto. Per altre informazioni sulle Autofac, vedere [Autofac](http://autofac.readthedocs.io/en/latest/index.html) in readthedocs.io.

In Autofac, il `IContainer` interfaccia fornisce il contenitore di inserimento delle dipendenze. Figura 3-1 mostra le dipendenze quando si usa questo contenitore, che crea un'istanza di un `IOrderService` dell'oggetto e lo inserisca nel `ProfileViewModel` classe.

![](dependency-injection-images/dependencyinjection.png "Esempio di dipendenze quando si usa l'inserimento delle dipendenze")

**Figura 3-1:** Dipendenze quando si usa l'inserimento delle dipendenze

In fase di esecuzione, il contenitore deve sapere quale implementazione del `IOrderService` interfaccia deve creare un'istanza, prima è possibile creare un'istanza di un `ProfileViewModel` oggetto. A questo scopo:

-   Il contenitore prima di decidere come creare un'istanza di un oggetto che implementa il `IOrderService` interfaccia. Questo è noto come *registrazione*.
-   Il contenitore creando un'istanza dell'oggetto che implementa il `IOrderService` interfaccia e il `ProfileViewModel` oggetto. Questo è noto come *risoluzione*.

Infine, l'app verrà completata utilizzando la `ProfileViewModel` oggetto e verrà resa disponibile per operazioni di garbage collection. A questo punto, il garbage collector devono smaltire il `IOrderService` se altre classi non condividono la stessa istanza dell'istanza.

> [!TIP]
> Scrivere codice indipendente dal contenitore. Provare sempre a scrivere codice indipendente dal contenitore per disaccoppiare l'app dal contenitore delle dipendenze specifiche in uso.

## <a name="registration"></a>Registrazione

Prima che le dipendenze possono essere inserite in un oggetto, i tipi di dipendenze devono prima essere registrati con il contenitore. Registrazione di un tipo in genere implica il passaggio del contenitore, un'interfaccia e un tipo concreto che implementa l'interfaccia.

Esistono due modi per la registrazione di tipi e gli oggetti nel contenitore tramite codice:

-   Registrare un tipo o un mapping con il contenitore. Quando richiesto, il contenitore creerà un'istanza del tipo specificato.
-   Registrare un oggetto esistente nel contenitore come un singleton. Quando richiesto, il contenitore restituirà un riferimento all'oggetto esistente.

> [!TIP]
> I contenitori di dipendenze non sono sempre adatti. Inserimento di dipendenze introduce requisiti che potrebbero non essere utile o appropriato per le app di piccole dimensioni e complessità aggiuntive. Se una classe è presente alcuna dipendenza, o non è una dipendenza per gli altri tipi, si potrebbe non avere senso per inserirlo nel contenitore. Inoltre, se una classe dispone di un singolo set di dipendenze che sono essenziali per il tipo e non verrà mai modificato, potrebbe non avere senso per inserirlo nel contenitore.

La registrazione dei tipi che richiedono l'inserimento di dipendenze deve essere eseguita in un singolo metodo in un'app, e questo metodo deve essere richiamato nelle prime fasi del ciclo di vita dell'app per assicurarsi che l'app sia a conoscenza delle dipendenze tra le relative classi. Nell'app eShopOnContainers l'operazione viene eseguita la `ViewModelLocator` classe, quali compilazioni il `IContainer` oggetto ed è l'unica classe nell'app che contiene un riferimento a tale oggetto. Esempio di codice seguente viene illustrato come l'app per dispositivi mobili di eShopOnContainers viene dichiarata la `IContainer` dell'oggetto nel `ViewModelLocator` classe:

```csharp
private static IContainer _container;
```

Tipi e le istanze vengono registrate nel `RegisterDependencies` metodo nel `ViewModelLocator` classe. Questo risultato viene ottenuto creando innanzitutto un `ContainerBuilder` istanza, come illustrato nell'esempio di codice seguente:

```csharp
var builder = new ContainerBuilder();
```

Tipi e le istanze vengono quindi registrate con il `ContainerBuilder` oggetto e il codice seguente viene illustrata la forma più comune di registrazione del tipo:

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

Il `RegisterType` metodo illustrato di seguito esegue il mapping di un tipo di interfaccia per un tipo concreto. Indica al contenitore per creare un'istanza di un `RequestProvider` oggetto quando si crea un'istanza di un oggetto che richiede un inserimento di un `IRequestProvider` tramite un costruttore.

Tipi concreti possono essere registrati direttamente senza un mapping da un tipo di interfaccia, come illustrato nell'esempio di codice seguente:

```csharp
builder.RegisterType<ProfileViewModel>();
```

Quando il `ProfileViewModel` tipo viene risolto, il contenitore inserirà le relative dipendenze necessarie.

Autofac consente inoltre la registrazione dell'istanza, in cui il contenitore è responsabile del mantenimento di un riferimento a un'istanza singleton di un tipo. Ad esempio, il codice seguente viene illustrato come l'app per dispositivi mobili di eShopOnContainers registra il tipo concreto da utilizzare quando un `ProfileViewModel` istanza richiede un `IOrderService` istanza:

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

Il `RegisterType` metodo illustrato di seguito esegue il mapping di un tipo di interfaccia per un tipo concreto. Il `SingleInstance` metodo consente di configurare la registrazione in modo che ogni oggetto dipendente riceva la stessa istanza condivisa. Pertanto, una sola `OrderService` istanza sarà presente nel contenitore, che è condiviso da oggetti che richiedono un inserimento di un `IOrderService` tramite un costruttore.

La registrazione dell'istanza possono essere eseguite anche con il `RegisterInstance` metodo, come illustrato nell'esempio di codice seguente:

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

Il `RegisterInstance` metodo illustrato di seguito crea un nuovo `OrderMockService` dell'istanza e lo registra con il contenitore. Pertanto, una sola `OrderMockService` istanza presente nel contenitore, che è condiviso da oggetti che richiedono un inserimento di un `IOrderService` tramite un costruttore.

Dopo la registrazione di tipo e l'istanza, il `IContainer` oggetto deve essere compilato, cui è illustrato nell'esempio di codice seguente:

```csharp
_container = builder.Build();
```

Richiama il `Build` metodo su di `ContainerBuilder` istanza compila un nuovo contenitore di inserimento delle dipendenze che contiene le registrazioni che sono state apportate.

> [!TIP]
> Si consideri un `IContainer` come non modificabile. Mentre Autofac fornisce un `Update` metodo per aggiornare le registrazioni in un contenitore esistente, chiamare questo metodo deve essere evitata laddove possibile. Esistono rischi per la modifica di un contenitore dopo che è stata creata, in particolare se il contenitore è stato utilizzato. Per altre informazioni, vedere [considerare un contenitore come non modificabili](http://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable) in readthedocs.io.

<a name="resolution" />

## <a name="resolution"></a>Risoluzione

Dopo aver registrato un tipo, può essere risolto o inserito come dipendenza. Quando un tipo viene risolto e il contenitore deve creare una nuova istanza, inserisce tutte le dipendenze nell'istanza.

In genere, quando un tipo viene risolto, una delle tre situazioni si verifica:

1.  Se il tipo non è ancora stato registrato, il contenitore genera un'eccezione.
1.  Se il tipo è stato registrato come un singleton, il contenitore restituisce l'istanza singleton. Se questa è la prima volta il tipo viene chiamato, il contenitore lo crea se necessario e mantiene un riferimento a esso.
1.  Se il tipo non è ancora stato registrato come un singleton, il contenitore restituisce una nuova istanza e non mantiene un riferimento a esso.

Nell'esempio di codice riportato di seguito viene illustrato come il `RequestProvider` tipo registrato in precedenza con Autofac può essere risolta:

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

In questo esempio, Autofac viene richiesto di risolvere il tipo concreto per la `IRequestProvider` tipo, insieme a eventuali dipendenze. In genere, il `Resolve` metodo viene chiamato quando è necessaria un'istanza di un tipo specifico. Per informazioni su come controllare la durata degli oggetti risolti, vedere [la gestione della durata di oggetti risolti](#managing_the_lifetime_of_resolved_objects).

Esempio di codice seguente viene illustrato come l'app per dispositivi mobili di eShopOnContainers crea un'istanza di tipi di modello di visualizzazione e le relative dipendenze:

```csharp
var viewModel = _container.Resolve(viewModelType);
```

In questo esempio, Autofac viene richiesto di risolvere il tipo di modello di visualizzazione per un modello di visualizzazione richiesto e il contenitore verrà risolto anche eventuali dipendenze. Quando si risolve la `ProfileViewModel` è di tipo, la dipendenza da risolvere un `IOrderService` oggetto. Pertanto, Autofac dapprima crea un `OrderService` dell'oggetto e quindi lo passa al costruttore del `ProfileViewModel` classe. Per altre informazioni sul modo in cui l'app per dispositivi mobili di eShopOnContainers Crea vista modellano e li associano alle viste, vedere [creazione automatica di un modello di visualizzazione con un localizzatore di modello di visualizzazione](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

> [!NOTE]
> La registrazione e la risoluzione dei tipi con un contenitore ha sulle prestazioni a causa di uso del contenitore della reflection per la creazione di ogni tipo, soprattutto se le dipendenze sono in corso ricostruite per ogni navigazione tra le pagine nell'app. Se sono presenti dipendenze molte o complete, può aumentare sensibilmente il costo della creazione.

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>La gestione della durata degli oggetti risolti

Dopo la registrazione di un tipo, il comportamento predefinito per Autofac è per creare una nuova istanza del tipo registrato ogni volta che il tipo viene risolto oppure quando il meccanismo di dipendenza inserisce le istanze in altre classi. In questo scenario, il contenitore non contiene un riferimento all'oggetto risolto. Tuttavia, quando si registra un'istanza, il comportamento predefinito per Autofac consiste nel gestire la durata dell'oggetto come un singleton. Pertanto, l'istanza rimane nell'ambito, mentre il contenitore si trova nell'ambito e viene eliminato quando il contenitore esce dall'ambito ed è sottoposti a garbage collection o quando in modo esplicito ed elimina il contenitore.

Un ambito dell'istanza Autofac è utilizzabile per specificare il comportamento singleton per un oggetto che Autofac Crea da un tipo registrato. Gli ambiti delle istanze Autofac gestiscono la durata degli oggetti creare un'istanza di contenitore. L'ambito di istanza predefinito per il `RegisterType` metodo è il `InstancePerDependency` ambito. Tuttavia, il `SingleInstance` ambito può essere usato con il `RegisterType` metodo, in modo che il contenitore viene creato o restituisce un'istanza singleton di un tipo quando si chiama il `Resolve` (metodo). Esempio di codice seguente viene illustrato come Autofac viene richiesto di creare un'istanza singleton del `NavigationService` classe:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

La prima volta che il `INavigationService` interfaccia viene risolta, crea un nuovo contenitore `NavigationService` dell'oggetto e mantiene un riferimento a esso. In qualsiasi successive risoluzioni del `INavigationService` interfaccia, il contenitore restituisce un riferimento al `NavigationService` oggetto creato in precedenza.

> [!NOTE]
> L'ambito SingleInstance Elimina gli oggetti creati quando viene eliminato il contenitore.

Autofac include gli ambiti di istanza aggiuntive. Per altre informazioni, vedere [ambito istanza](http://autofac.readthedocs.io/en/latest/lifetime/instance-scope.html) in readthedocs.io.

## <a name="summary"></a>Riepilogo

Inserimento di dipendenze consente la separazione di tipi concreti dal codice che dipende da questi tipi. In genere Usa un contenitore che include un elenco delle registrazioni e i mapping tra le interfacce e tipi astratti e i tipi concreti che implementano o estendono questi tipi.

Semplifica la creazione di App loosely-coupled Autofac e fornisce tutte le funzionalità comunemente presente nei contenitori di inserimento delle dipendenze, inclusi i metodi per registrare i mapping dei tipi e istanze di oggetti, risolvere gli oggetti, la gestione della durata degli oggetti e inserire oggetti dipendenti nei costruttori di oggetti che viene risolto.


## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (PDF 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
