---
title: Inserimento di dipendenze
description: Questo capitolo illustra il modo in cui l'app per dispositivi mobili eShopOnContainers usa l'inserimento delle dipendenze per separare i tipi concreti dal codice che dipende da questi tipi.
ms.prod: xamarin
ms.assetid: a150f2d1-06f8-4aed-ab4e-7a847d69f103
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
ms.openlocfilehash: 08fb22627ab6b40c94c17d94321ed0bac60beedd
ms.sourcegitcommit: 9dd0b076ab4ecdbbd1b029d2e0d67d900e1c4494
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73567910"
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

Nel contesto di un'app Xamarin.Forms che usa MVVM, viene in genere usato un contenitore di inserimento delle dipendenze per la registrazione e la risoluzione di modelli di visualizzazione e per la registrazione dei servizi e l'inserimento in modelli di visualizzazione.

Sono disponibili molti contenitori di inserimento delle dipendenze, con l'app per dispositivi mobili eShopOnContainers che usa TinyIoC per gestire la creazione di istanze di classi di servizi e modelli di visualizzazione nell'app. TinyIoC è stato scelto dopo la valutazione di una serie di contenitori diversi e offre prestazioni superiori sulle piattaforme mobili rispetto alla maggior parte dei contenitori noti. Semplifica la creazione di app a regime di controllo libero e fornisce tutte le funzionalità comunemente presenti nei contenitori di inserimento delle dipendenze, inclusi i metodi per registrare i mapping dei tipi, risolvere oggetti, gestire la durata degli oggetti e inserire oggetti dipendenti in costruttori di oggetti risolti. Per ulteriori informazioni su TinyIoC, vedere [TinyIoC](https://github.com/grumpydev/TinyIoC/wiki) in GitHub.com.

In TinyIoC, il tipo di `TinyIoCContainer` fornisce il contenitore di inserimento delle dipendenze. La figura 3-1 Mostra le dipendenze quando si usa questo contenitore, che crea un'istanza di un oggetto `IOrderService` e lo inserisce nella classe `ProfileViewModel`.

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

La registrazione dei tipi che richiedono l'inserimento delle dipendenze deve essere eseguita in un singolo metodo in un'app e questo metodo deve essere richiamato prima nel ciclo di vita dell'app per assicurarsi che l'app sia in grado di riconoscere le dipendenze tra le classi. Nell'app eShopOnContainers per dispositivi mobili questa operazione viene eseguita dalla classe `ViewModelLocator`, che compila l'oggetto `TinyIoCContainer` ed è l'unica classe nell'app che include un riferimento a tale oggetto. L'esempio di codice seguente illustra come l'app per dispositivi mobili eShopOnContainers dichiara l'oggetto `TinyIoCContainer` nella classe `ViewModelLocator`:

```csharp
private static TinyIoCContainer _container;
```

I tipi vengono registrati nel costruttore `ViewModelLocator`. Questa operazione viene eseguita creando prima un'istanza di `TinyIoCContainer`, illustrata nell'esempio di codice seguente:

```csharp
_container = new TinyIoCContainer();
```

I tipi vengono quindi registrati con l'oggetto `TinyIoCContainer` e nell'esempio di codice seguente viene illustrata la forma più comune di registrazione del tipo:

```csharp
_container.Register<IRequestProvider, RequestProvider>();
```

Il metodo `Register` illustrato di seguito esegue il mapping di un tipo di interfaccia a un tipo concreto. Per impostazione predefinita, ogni registrazione dell'interfaccia viene configurata come singleton, in modo che ogni oggetto dipendente riceva la stessa istanza condivisa. Pertanto, nel contenitore sarà presente una sola istanza di `RequestProvider`, che è condivisa da oggetti che richiedono un inserimento di un `IRequestProvider` tramite un costruttore.

I tipi concreti possono anche essere registrati direttamente senza un mapping da un tipo di interfaccia, come illustrato nell'esempio di codice seguente:

```csharp
_container.Register<ProfileViewModel>();
```

Per impostazione predefinita, ogni registrazione di classe concreta è configurata come istanza a più istanze in modo che ogni oggetto dipendente riceva una nuova istanza. Pertanto, quando il `ProfileViewModel` viene risolto, viene creata una nuova istanza e il contenitore inserisce le dipendenze necessarie.

<a name="resolution" />

## <a name="resolution"></a>Risoluzione

Una volta registrato, un tipo può essere risolto o inserito come dipendenza. Quando un tipo viene risolto e il contenitore deve creare una nuova istanza, inserisce tutte le dipendenze nell'istanza.

In genere, quando un tipo viene risolto, si verifica una delle tre situazioni seguenti:

1. Se il tipo non è stato registrato, il contenitore genera un'eccezione.
1. Se il tipo è stato registrato come singleton, il contenitore restituisce l'istanza singleton. Se è la prima volta che il tipo viene chiamato, il contenitore lo crea se necessario e mantiene un riferimento a esso.
1. Se il tipo non è stato registrato come singleton, il contenitore restituisce una nuova istanza e non mantiene un riferimento a tale istanza.

Nell'esempio di codice seguente viene illustrato come è possibile risolvere il tipo di `RequestProvider` precedentemente registrato con TinyIoC:

```csharp
var requestProvider = _container.Resolve<IRequestProvider>();
```

In questo esempio, a TinyIoC viene richiesto di risolvere il tipo concreto per il tipo di `IRequestProvider`, insieme alle dipendenze. In genere, il metodo `Resolve` viene chiamato quando è richiesta un'istanza di un tipo specifico. Per informazioni sul controllo della durata degli oggetti risolti, vedere [gestione della durata degli oggetti risolti](#managing_the_lifetime_of_resolved_objects).

L'esempio di codice seguente mostra come l'app per dispositivi mobili eShopOnContainers crea un'istanza dei tipi di modello di visualizzazione e delle relative dipendenze:

```csharp
var viewModel = _container.Resolve(viewModelType);
```

In questo esempio, TinyIoC viene richiesto di risolvere il tipo di modello di visualizzazione per un modello di visualizzazione richiesto e il contenitore risolverà anche eventuali dipendenze. Quando si risolve il tipo di `ProfileViewModel`, le dipendenze da risolvere sono un oggetto `ISettingsService` e un oggetto `IOrderService`. Poiché le registrazioni dell'interfaccia venivano utilizzate durante la registrazione delle classi `SettingsService` e `OrderService`, TinyIoC restituisce le istanze singleton per le classi `SettingsService` e `OrderService` e le passa quindi al costruttore della classe `ProfileViewModel`. Per altre informazioni sul modo in cui l'app per dispositivi mobili eShopOnContainers costruisce i modelli di visualizzazione e li associa alle visualizzazioni, vedere [creazione automatica di un modello di visualizzazione con un localizzatore di modello di visualizzazione](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator).

> [!NOTE]
> La registrazione e la risoluzione dei tipi con un contenitore comportano un costo in termini di prestazioni perché il contenitore usa la reflection per la creazione di ogni tipo, soprattutto se le dipendenze vengono ricostruite per la navigazione di ogni pagina nell'app. Se le dipendenze presenti sono numerose o complete, il costo della creazione può aumentare in modo significativo.

<a name="managing_the_lifetime_of_resolved_objects" />

## <a name="managing-the-lifetime-of-resolved-objects"></a>Gestione della durata degli oggetti risolti

Dopo la registrazione di un tipo mediante una registrazione di classe concreta, il comportamento predefinito di TinyIoC consiste nel creare una nuova istanza del tipo registrato ogni volta che il tipo viene risolto o quando il meccanismo di dipendenza inserisce istanze in altre classi. In questo scenario, il contenitore non tiene un riferimento all'oggetto risolto. Tuttavia, quando si registra un tipo mediante la registrazione dell'interfaccia, il comportamento predefinito di TinyIoC consiste nel gestire la durata dell'oggetto come singleton. Pertanto, l'istanza rimane nell'ambito mentre il contenitore è nell'ambito e viene eliminato quando il contenitore esce dall'ambito ed è sottoposto a Garbage Collection o quando il codice elimina il contenitore in modo esplicito.

È possibile eseguire l'override del comportamento predefinito della registrazione TinyIoC usando il `AsSingleton` Fluent e i metodi API `AsMultiInstance`. Il metodo `AsSingleton`, ad esempio, può essere utilizzato con il metodo `Register`, in modo che il contenitore crei o restituisca un'istanza singleton di un tipo quando viene chiamato il metodo `Resolve`. Nell'esempio di codice seguente viene illustrato come viene indicato a TinyIoC di creare un'istanza singleton della classe `LoginViewModel`:

```csharp
_container.Register<LoginViewModel>().AsSingleton();
```

La prima volta che il tipo di `LoginViewModel` viene risolto, il contenitore crea un nuovo oggetto `LoginViewModel` e ne mantiene un riferimento. Per tutte le risoluzioni successive del `LoginViewModel`, il contenitore restituisce un riferimento all'oggetto `LoginViewModel` creato in precedenza.

> [!NOTE]
> I tipi registrati come Singleton vengono eliminati quando il contenitore viene eliminato.

## <a name="summary"></a>Riepilogo

L'inserimento di dipendenze consente di separare i tipi concreti dal codice che dipende da questi tipi. USA in genere un contenitore che include un elenco di registrazioni e mapping tra interfacce e tipi astratti e i tipi concreti che implementano o estendono questi tipi.

TinyIoC è un contenitore leggero che offre prestazioni superiori sulle piattaforme mobili rispetto alla maggior parte dei contenitori noti. Semplifica la creazione di app a regime di controllo libero e fornisce tutte le funzionalità comunemente presenti nei contenitori di inserimento delle dipendenze, inclusi i metodi per registrare i mapping dei tipi, risolvere oggetti, gestire la durata degli oggetti e inserire oggetti dipendenti in costruttori di oggetti risolti.

## <a name="related-links"></a>Collegamenti correlati

- [Scarica eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (esempio)](https://github.com/dotnet-architecture/eShopOnContainers)
