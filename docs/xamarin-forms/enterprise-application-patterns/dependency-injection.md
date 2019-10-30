---
title: Inserimento di dipendenze
description: Questo capitolo illustra il modo in cui l'app per dispositivi mobili eShopOnContainers usa l'inserimento delle dipendenze per separare i tipi concreti dal codice che dipende da questi tipi.
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 975b32610b4b496e329c5c5a29b79efd2874d8cf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029494"
---
# <a name="dependency-injection"></a>Inserimento di dipendenze

In genere, viene richiamato un costruttore di classe quando si crea un'istanza di un oggetto e tutti i valori necessari per l'oggetto vengono passati come argomenti al costruttore. Questo è un esempio di inserimento di dipendenze e, in particolare, è noto come *inserimento del costruttore*. Le dipendenze necessarie per l'oggetto vengono inserite nel costruttore.

Specificando le dipendenze come tipi di interfaccia, l'inserimento delle dipendenze consente di separare i tipi concreti dal codice che dipende da questi tipi. USA in genere un contenitore che include un elenco di registrazioni e mapping tra interfacce e tipi astratti e i tipi concreti che implementano o estendono questi tipi.

Esistono anche altri tipi di inserimento delle dipendenze, ad esempio l'inserimento di *Setter di proprietà*e l'inserimento di *chiamate al metodo*, ma sono meno comuni. Pertanto, questo capitolo si concentra esclusivamente sull'esecuzione dell'inserimento di un costruttore con un contenitore di inserimento delle dipendenze.

<a name="introduction_to_dependency_injection" />

## <a name="introduction-to-dependency-injection"></a>Introduzione all'inserimento delle dipendenze

L'inserimento di dipendenze è una versione specializzata del modello di inversione del controllo (IoC), in cui la preoccupazione da invertire è il processo di recupero della dipendenza richiesta. Con l'inserimento di dipendenze, un'altra classe è responsabile di inserire le dipendenze in un oggetto in fase di esecuzione. Nell'esempio di codice seguente viene illustrata la struttura della classe `ProfileViewModel` quando si usa l'inserimento delle dipendenze:

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

Il costruttore `ProfileViewModel` riceve un'istanza `IOrderService` come argomento, inserita da un'altra classe. L'unica dipendenza nella classe `ProfileViewModel` è sul tipo di interfaccia. Pertanto, la classe `ProfileViewModel` non ha alcuna conoscenza della classe responsabile della creazione di un'istanza dell'oggetto `IOrderService`. La classe responsabile della creazione di un'istanza dell'oggetto `IOrderService` e dell'inserimento nella classe `ProfileViewModel` è nota come *contenitore di inserimento delle dipendenze*.

I contenitori di inserimento delle dipendenze riducono l'accoppiamento tra oggetti fornendo una struttura per creare istanze di classi e gestirne la durata in base alla configurazione del contenitore. Durante la creazione degli oggetti, il contenitore inserisce tutte le dipendenze necessarie per l'oggetto. Se tali dipendenze non sono ancora state create, il contenitore crea e risolve prima le dipendenze.

> [!NOTE]
> L'inserimento di dipendenze può essere implementato anche manualmente tramite Factory. Tuttavia, l'uso di un contenitore fornisce funzionalità aggiuntive, ad esempio la gestione della durata e la registrazione tramite l'analisi degli assembly.

L'utilizzo di un contenitore di inserimento delle dipendenze presenta diversi vantaggi:

- Un contenitore elimina la necessità di una classe di individuare le relative dipendenze e di gestirne la durata.
- Un contenitore consente il mapping delle dipendenze implementate senza influire sulla classe.
- Un contenitore facilita la testabilità consentendo di simulare le dipendenze.
- Un contenitore aumenta la gestibilità consentendo di aggiungere facilmente nuove classi all'app.

Nel contesto di un'app Novell. Forms che usa MVVM, viene in genere usato un contenitore di inserimento delle dipendenze per la registrazione e la risoluzione di modelli di visualizzazione e per la registrazione dei servizi e l'inserimento in modelli di visualizzazione.

Sono disponibili molti contenitori di inserimento delle dipendenze, con l'app per dispositivi mobili eShopOnContainers che usa Autofac per gestire la creazione di istanze di classi di servizi e modelli di visualizzazione nell'app. Autofac semplifica la creazione di app a regime di controllo libero e fornisce tutte le funzionalità comunemente presenti nei contenitori di inserimento delle dipendenze, inclusi i metodi per registrare i mapping dei tipi e le istanze degli oggetti, risolvere oggetti, gestire la durata degli oggetti e inserire oggetti dipendenti in costruttori di oggetti che vengono risolti. Per ulteriori informazioni su Autofac, vedere [Autofac](https://autofac.readthedocs.io/en/latest/index.html) in ReadTheDocs.io.

In Autofac, l'interfaccia `IContainer` fornisce il contenitore di inserimento delle dipendenze. La figura 3-1 Mostra le dipendenze quando si usa questo contenitore, che crea un'istanza di un oggetto `IOrderService` e lo inserisce nella classe `ProfileViewModel`.

![](dependency-injection-images/dependencyinjection.png "Dependencies example when using dependency injection")

**Figura 3-1:** Dipendenze quando si usa l'inserimento delle dipendenze

In fase di esecuzione, il contenitore deve essere in grado di individuare l'implementazione dell'interfaccia `IOrderService` di cui creare un'istanza, prima di poter creare un'istanza di un oggetto `ProfileViewModel`. Questo implica:

- Il contenitore che decide come creare un'istanza di un oggetto che implementa l'interfaccia `IOrderService`. Questa operazione è nota come *registrazione*.
- Il contenitore che crea un'istanza dell'oggetto che implementa l'interfaccia `IOrderService` e l'oggetto `ProfileViewModel`. Questa operazione è nota come *risoluzione*.

Infine, l'app verrà terminata usando l'oggetto `ProfileViewModel` e diventerà disponibile per l'Garbage Collection. A questo punto, il Garbage Collector deve eliminare l'istanza `IOrderService` se altre classi non condividono la stessa istanza.

> [!TIP]
> Scrivere codice indipendente dal contenitore. Provare sempre a scrivere codice indipendente dal contenitore per separare l'app dal contenitore di dipendenza specifico usato.

## <a name="registration"></a>Registrazione

Prima di inserire le dipendenze in un oggetto, i tipi delle dipendenze devono prima essere registrati con il contenitore. Per la registrazione di un tipo è in genere necessario passare al contenitore un'interfaccia e un tipo concreto che implementi l'interfaccia.

Esistono due modi per registrare i tipi e gli oggetti nel contenitore tramite il codice:

- Registrare un tipo o un mapping con il contenitore. Quando necessario, il contenitore compilerà un'istanza del tipo specificato.
- Registrare un oggetto esistente nel contenitore come singleton. Quando necessario, il contenitore restituirà un riferimento all'oggetto esistente.

> [!TIP]
> I contenitori di inserimento delle dipendenze non sono sempre appropriati. L'inserimento di dipendenze introduce complessità e requisiti aggiuntivi che potrebbero non essere appropriati o utili per le piccole app. Se una classe non ha dipendenze o non è una dipendenza per altri tipi, potrebbe non essere opportuno inserirla nel contenitore. Inoltre, se una classe dispone di un singolo set di dipendenze integrali per il tipo e non verrà mai modificato, potrebbe non essere opportuno inserirlo nel contenitore.

La registrazione dei tipi che richiedono l'inserimento delle dipendenze deve essere eseguita in un singolo metodo in un'app e questo metodo deve essere richiamato prima nel ciclo di vita dell'app per assicurarsi che l'app sia in grado di riconoscere le dipendenze tra le classi. Nell'app eShopOnContainers per dispositivi mobili questa operazione viene eseguita dalla classe `ViewModelLocator`, che compila l'oggetto `IContainer` ed è l'unica classe nell'app che include un riferimento a tale oggetto. L'esempio di codice seguente illustra come l'app per dispositivi mobili eShopOnContainers dichiara l'oggetto `IContainer` nella classe `ViewModelLocator`:

```csharp
private static IContainer _container;
```

I tipi e le istanze sono registrati nel metodo `RegisterDependencies` nella classe `ViewModelLocator`. Questa operazione viene eseguita creando prima un'istanza di `ContainerBuilder`, illustrata nell'esempio di codice seguente:

```csharp
var builder = new ContainerBuilder();
```

I tipi e le istanze vengono quindi registrati con l'oggetto `ContainerBuilder` e nell'esempio di codice seguente viene illustrata la forma più comune di registrazione del tipo:

```csharp
builder.RegisterType<RequestProvider>().As<IRequestProvider>();
```

Il metodo `RegisterType` illustrato di seguito esegue il mapping di un tipo di interfaccia a un tipo concreto. Indica al contenitore di creare un'istanza di un oggetto `RequestProvider` quando crea un'istanza di un oggetto che richiede l'inserimento di un `IRequestProvider` tramite un costruttore.

I tipi concreti possono anche essere registrati direttamente senza un mapping da un tipo di interfaccia, come illustrato nell'esempio di codice seguente:

```csharp
builder.RegisterType<ProfileViewModel>();
```

Quando il tipo di `ProfileViewModel` viene risolto, il contenitore inserisce le dipendenze necessarie.

Autofac consente inoltre la registrazione dell'istanza, in cui il contenitore è responsabile della gestione di un riferimento a un'istanza singleton di un tipo. Ad esempio, l'esempio di codice seguente mostra come l'app per dispositivi mobili eShopOnContainers registra il tipo concreto da usare quando un'istanza di `ProfileViewModel` richiede un'istanza di `IOrderService`:

```csharp
builder.RegisterType<OrderService>().As<IOrderService>().SingleInstance();
```

Il metodo `RegisterType` illustrato di seguito esegue il mapping di un tipo di interfaccia a un tipo concreto. Il metodo `SingleInstance` configura la registrazione in modo che ogni oggetto dipendente riceva la stessa istanza condivisa. Pertanto, nel contenitore sarà presente una sola istanza di `OrderService`, che è condivisa da oggetti che richiedono un inserimento di un `IOrderService` tramite un costruttore.

È anche possibile eseguire la registrazione dell'istanza con il metodo `RegisterInstance`, illustrato nell'esempio di codice seguente:

```csharp
builder.RegisterInstance(new OrderMockService()).As<IOrderService>();
```

Il metodo `RegisterInstance` illustrato di seguito crea una nuova istanza di `OrderMockService` e la registra con il contenitore. Pertanto, nel contenitore è presente una sola istanza di `OrderMockService`, che è condivisa da oggetti che richiedono un inserimento di un `IOrderService` tramite un costruttore.

Dopo la registrazione del tipo e dell'istanza, è necessario compilare l'oggetto `IContainer`, illustrato nell'esempio di codice seguente:

```csharp
_container = builder.Build();
```

Richiamando il metodo `Build` sull'istanza di `ContainerBuilder` si compila un nuovo contenitore di inserimento delle dipendenze che contiene le registrazioni effettuate.

> [!TIP]
> Si consideri un `IContainer` come non modificabile. Mentre Autofac fornisce un metodo `Update` per aggiornare le registrazioni in un contenitore esistente, la chiamata a questo metodo deve essere evitata laddove possibile. Esistono rischi per la modifica di un contenitore dopo che è stato compilato, in particolare se è stato usato il contenitore. Per altre informazioni, vedere [considerare un contenitore come non modificabile](https://docs.autofac.org/en/latest/best-practices/#consider-a-container-as-immutable) in ReadTheDocs.io.

<a name="resolution" />

## <a name="resolution"></a>Risoluzione

Una volta registrato, un tipo può essere risolto o inserito come dipendenza. Quando un tipo viene risolto e il contenitore deve creare una nuova istanza, inserisce tutte le dipendenze nell'istanza.

In genere, quando un tipo viene risolto, si verifica una delle tre situazioni seguenti:

1. Se il tipo non è stato registrato, il contenitore genera un'eccezione.
1. Se il tipo è stato registrato come singleton, il contenitore restituisce l'istanza singleton. Se è la prima volta che il tipo viene chiamato, il contenitore lo crea se necessario e mantiene un riferimento a esso.
1. Se il tipo non è stato registrato come singleton, il contenitore restituisce una nuova istanza e non mantiene un riferimento a tale istanza.

Nell'esempio di codice seguente viene illustrato come è possibile risolvere il tipo di `RequestProvider` precedentemente registrato con Autofac:

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

In questo esempio, a Autofac viene richiesto di risolvere il tipo concreto per il tipo di `IRequestProvider`, insieme alle dipendenze. In genere, il metodo `Resolve` viene chiamato quando è richiesta un'istanza di un tipo specifico. Per informazioni sul controllo della durata degli oggetti risolti, vedere [gestione della durata degli oggetti risolti](#managing_the_lifetime_of_resolved_objects).

L'esempio di codice seguente mostra come l'app per dispositivi mobili eShopOnContainers crea un'istanza dei tipi di modello di visualizzazione e delle relative dipendenze:

```csharp
var viewModel = _container.Resolve(viewModelType);
```

In questo esempio, Autofac viene richiesto di risolvere il tipo di modello di visualizzazione per un modello di visualizzazione richiesto e il contenitore risolverà anche eventuali dipendenze. Quando si risolve il tipo di `ProfileViewModel`, la dipendenza da risolvere è un oggetto `IOrderService`. Pertanto, Autofac crea innanzitutto un oggetto `OrderService` e lo passa al costruttore della classe `ProfileViewModel`. Per altre informazioni sul modo in cui l'app per dispositivi mobili eShopOnContainers costruisce i modelli di visualizzazione e li associa alle visualizzazioni, vedere [creazione automatica di un modello di visualizzazione con un localizzatore di modello di visualizzazione](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

> [!NOTE]
> La registrazione e la risoluzione dei tipi con un contenitore comportano un costo in termini di prestazioni perché il contenitore usa la reflection per la creazione di ogni tipo, soprattutto se le dipendenze vengono ricostruite per la navigazione di ogni pagina nell'app. Se le dipendenze presenti sono numerose o complete, il costo della creazione può aumentare in modo significativo.

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>Gestione della durata degli oggetti risolti

Dopo la registrazione di un tipo, il comportamento predefinito di Autofac consiste nel creare una nuova istanza del tipo registrato ogni volta che il tipo viene risolto o quando il meccanismo di dipendenza inserisce istanze in altre classi. In questo scenario, il contenitore non tiene un riferimento all'oggetto risolto. Tuttavia, quando si registra un'istanza di, il comportamento predefinito di Autofac consiste nel gestire la durata dell'oggetto come singleton. Pertanto, l'istanza rimane nell'ambito mentre il contenitore è nell'ambito e viene eliminato quando il contenitore esce dall'ambito ed è sottoposto a Garbage Collection o quando il codice elimina il contenitore in modo esplicito.

Un ambito dell'istanza di Autofac può essere usato per specificare il comportamento singleton per un oggetto creato da Autofac da un tipo registrato. Gli ambiti dell'istanza di Autofac gestiscono la durata degli oggetti di cui è stata creata un'istanza dal contenitore. L'ambito dell'istanza predefinito per il metodo `RegisterType` è l'ambito del `InstancePerDependency`. Tuttavia, l'ambito `SingleInstance` può essere utilizzato con il metodo `RegisterType`, in modo che il contenitore crei o restituisca un'istanza singleton di un tipo quando viene chiamato il metodo `Resolve`. Nell'esempio di codice seguente viene illustrato come viene indicato a Autofac di creare un'istanza singleton della classe `NavigationService`:

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

La prima volta che l'interfaccia `INavigationService` viene risolta, il contenitore crea un nuovo oggetto `NavigationService` e ne mantiene un riferimento. Per tutte le risoluzioni successive dell'interfaccia `INavigationService`, il contenitore restituisce un riferimento all'oggetto `NavigationService` creato in precedenza.

> [!NOTE]
> L'ambito SingleInstance Elimina gli oggetti creati quando il contenitore viene eliminato.

Autofac include ambiti di istanza aggiuntivi. Per altre informazioni, vedere [ambito dell'istanza](https://autofac.readthedocs.io/en/latest/lifetime/instance-scope.html) in ReadTheDocs.io.

## <a name="summary"></a>Riepilogo

L'inserimento di dipendenze consente di separare i tipi concreti dal codice che dipende da questi tipi. USA in genere un contenitore che include un elenco di registrazioni e mapping tra interfacce e tipi astratti e i tipi concreti che implementano o estendono questi tipi.

Autofac semplifica la creazione di app a regime di controllo libero e fornisce tutte le funzionalità comunemente presenti nei contenitori di inserimento delle dipendenze, inclusi i metodi per registrare i mapping dei tipi e le istanze degli oggetti, risolvere oggetti, gestire la durata degli oggetti e inserire oggetti dipendenti nei costruttori di oggetti risolti.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (esempio)](https://github.com/dotnet-architecture/eShopOnContainers)
