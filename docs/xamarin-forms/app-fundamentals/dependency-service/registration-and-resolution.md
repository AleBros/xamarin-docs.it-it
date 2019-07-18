---
title: Registrazione e risoluzione di DependencyService di Xamarin.Forms
description: Questo articolo illustra come usare la classe DependencyService di Xamarin.Forms per richiamare la funzionalità della piattaforma nativa.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
ms.openlocfilehash: aab5f56594f9b9b81acb9c447eee238d151bd533
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832188"
---
# <a name="xamarinforms-dependencyservice-registration-and-resolution"></a>Registrazione e risoluzione di DependencyService di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-samples/tree/master/DependencyService)

Quando si usa Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) per richiamare la funzionalità della piattaforma nativa, le implementazioni della piattaforma devono essere registrate con `DependencyService` e quindi risolte da codice condiviso per richiamarle.

## <a name="register-platform-implementations"></a>Registrare le implementazioni della piattaforma

Le implementazioni della piattaforma devono essere registrate con [`DependencyService`](xref:Xamarin.Forms.DependencyService) in modo che Xamarin.Forms possa individuarle in fase di esecuzione.

È possibile eseguire la registrazione con [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) o con i metodi [`Register`](xref:Xamarin.Forms.DependencyService.Register*).

> [!IMPORTANT]
> Le build di versione dei progetti UWP che usano la compilazione .NET nativa devono registrare le implementazioni della piattaforma con i metodi [`Register`](xref:Xamarin.Forms.DependencyService.Register*).

### <a name="registration-by-attribute"></a>Registrazione in base all'attributo

È possibile usare [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) per registrare un'implementazione della piattaforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService). L'attributo indica che il tipo specificato offre un'implementazione concreta dell'interfaccia.

L'esempio seguente illustra l'uso di [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) per registrare l'implementazione di iOS dell'interfaccia `IDeviceOrientationService`:

```csharp
using Xamarin.Forms;

[assembly: Dependency(typeof(DeviceOrientationService))]
namespace DependencyServiceDemos.iOS
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            ...
        }
    }
}
```

In questo esempio, [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) registra `DeviceOrientationService` con [`DependencyService`](xref:Xamarin.Forms.DependencyService). Il tipo concreto viene quindi registrato per l'interfaccia che implementa.

Analogamente, le implementazioni dell'interfaccia `IDeviceOrientationService` in altre piattaforme devono essere registrate con [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute).

> [!NOTE]
> La registrazione con [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) viene eseguita a livello di spazio dei nomi.

### <a name="registration-by-method"></a>Registrazione in base al metodo

È possibile usare i metodi [`DependencyService.Register`](xref:Xamarin.Forms.DependencyService.Register*) per registrare un'implementazione della piattaforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService).

L'esempio seguente illustra l'uso del metodo [`Register`](xref:Xamarin.Forms.DependencyService.Register*) per registrare l'implementazione di iOS dell'interfaccia `IDeviceOrientationService`:

```csharp
[Register("AppDelegate")]
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
                global::Xamarin.Forms.Forms.Init();
                LoadApplication(new App());
                DependencyService.Register<IDeviceOrientationService, DeviceOrientationService>();
                return base.FinishedLaunching(app, options);
        }
}
```

In questo esempio, il metodo [`Register`](xref:Xamarin.Forms.DependencyService.Register*) registra il tipo concreto `DeviceOrientationService` per l'interfaccia `IDeviceOrientationService`. In alternativa è possibile usare un overload del metodo [`Register`](xref:Xamarin.Forms.DependencyService.Register*) per registrare un'implementazione della piattaforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService):

```csharp
DependencyService.Register<DeviceOrientationService>();
```

In questo esempio, il metodo [`Register`](xref:Xamarin.Forms.DependencyService.Register*) registra `DeviceOrientationService` con [`DependencyService`](xref:Xamarin.Forms.DependencyService). Il tipo concreto viene quindi registrato per l'interfaccia che implementa.

Analogamente, le implementazioni dell'interfaccia `IDeviceOrientationService` in altre piattaforme possono essere registrate con i metodi [`Register`](xref:Xamarin.Forms.DependencyService.Register*).

> [!IMPORTANT]
> La registrazione con i metodi [`Register`](xref:Xamarin.Forms.DependencyService.Register*) deve essere eseguita nei progetti di piattaforma, prima che funzionalità fornita dall'implementazione della piattaforma venga richiamata da codice condiviso.

## <a name="resolve-the-platform-implementations"></a>Risolvere le implementazioni della piattaforma

Le implementazioni della piattaforma devono essere risolte prima di essere richiamate. Questa operazione viene in genere eseguita nel codice condiviso tramite il metodo [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*). Tuttavia, può essere eseguita anche con il metodo [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*).

Per impostazione predefinita, la classe [`DependencyService`](xref:Xamarin.Forms.DependencyService) risolverà solo le implementazioni della piattaforma che hanno costruttori senza parametri. È tuttavia possibile inserire in Xamarin.Forms un metodo di risoluzione delle dipendenze che usa un contenitore di inserimento delle dipendenze o metodi factory per risolvere le implementazioni della piattaforma. Questo approccio può essere usato per risolvere le implementazioni della piattaforma che hanno costruttori con parametri. Per altre informazioni, vedere [Risoluzione delle dipendenze in Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

> [!IMPORTANT]
> Se si richiama un'implementazione della piattaforma che non è stata registrata con [`DependencyService`](xref:Xamarin.Forms.DependencyService) verrà generata una `NullReferenceException`.

### <a name="resolve-using-the-getlttgt-method"></a>Risolvere con il metodo Get&lt;T&gt;

Il metodo [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) recupera l'implementazione della piattaforma dell'interfaccia `T` in fase di esecuzione e ne crea un'istanza come singleton. Questa istanza avrà la stessa durata dell'applicazione e tutte le chiamate successive risolte nella stessa implementazione della piattaforma recupereranno la stessa istanza.

Il codice seguente mostra un esempio della chiamata del metodo [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) per risolvere l'interfaccia `IDeviceOrientationService` e della chiamata del relativo metodo `GetOrientation`:

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Questo codice può essere in alternativa compresso in una singola riga:

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> Il metodo [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) crea un'istanza dell'implementazione della piattaforma dell'interfaccia `T` come singleton, per impostazione predefinita. Tuttavia, è possibile modificare questo comportamento. Per altre informazioni, vedere [Gestire la durata degli oggetti risolti](#manage-the-lifetime-of-resolved-objects).

### <a name="resolve-using-the-resolvelttgt-method"></a>Risolvere con il metodo Resolve&lt;T&gt;

Il metodo [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) recupera l'implementazione della piattaforma dell'interfaccia `T` in fase di esecuzione usando un metodo di risoluzione delle dipendenze inserito in Xamarin.Forms con la classe [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver). Se un metodo di risoluzione delle dipendenze non è stato inserito in Xamarin.Forms, il metodo `Resolve<T>` eseguirà il fallback chiamando il metodo [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) per recuperare l'implementazione della piattaforma. Per altre informazioni sull'inserimento di un metodo di risoluzione delle dipendenze in Xamarin.Forms, vedere [Risoluzione delle dipendenze in Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

Il codice seguente mostra un esempio della chiamata del metodo [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) per risolvere l'interfaccia `IDeviceOrientationService` e della chiamata del relativo metodo `GetOrientation`:

```csharp
IDeviceOrientationService service = DependencyService.Resolve<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Questo codice può essere in alternativa compresso in una singola riga:

```csharp
DeviceOrientation orientation = DependencyService.Resolve<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> Quando il metodo [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) esegue il fallback chiamando il metodo [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) crea un'istanza dell'implementazione della piattaforma dell'interfaccia `T` come singleton, per impostazione predefinita. Tuttavia, è possibile modificare questo comportamento. Per altre informazioni, vedere [Gestire la durata degli oggetti risolti](#manage-the-lifetime-of-resolved-objects).

## <a name="manage-the-lifetime-of-resolved-objects"></a>Gestire la durata degli oggetti risolti

Il comportamento predefinito della classe [`DependencyService`](xref:Xamarin.Forms.DependencyService) prevede la risoluzione delle implementazioni della piattaforma come singleton. Di conseguenza, le implementazioni della piattaforma avranno la stessa durata di un'applicazione.

Questo comportamento viene specificato con l'argomento facoltativo [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) per i metodi [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) e [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*). L'enumerazione [`DependencyFetchTarget` ](xref:Xamarin.Forms.DependencyFetchTarget) definisce due membri:

- `GlobalInstance`, che restituisce l'implementazione della piattaforma come singleton.
- `NewInstance`, che restituisce una nuova istanza dell'implementazione della piattaforma. L'applicazione è quindi responsabile della gestione della durata dell'istanza dell'implementazione della piattaforma.

Entrambi i metodi [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) e [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) impostano i relativi argomenti facoltativi su [`DependencyFetchTarget.GlobalInstance`](xref:Xamarin.Forms.DependencyFetchTarget) e quindi le implementazioni della piattaforma vengono sempre risolte come singleton. Questo comportamento può essere modificato, in modo che vengano create nuove istanze delle implementazioni della piattaforma, specificando [`DependencyFetchTarget.NewInstance`](xref:Xamarin.Forms.DependencyFetchTarget) come argomenti per i metodi `Get<T>` e `Resolve<T>`:

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
```

In questo esempio, [`DependencyService`](xref:Xamarin.Forms.DependencyService) crea una nuova istanza dell'implementazione della piattaforma per l'interfaccia `ITextToSpeechService`. Anche tutte le chiamate successive per risolvere `ITextToSpeechService` creeranno nuove istanze.

La conseguenza di creare sempre una nuova istanza di un'implementazione della piattaforma è che l'applicazione diventa responsabile della gestione della durata delle istanze. Questo significa che se si sottoscrive un evento definito in un'implementazione della piattaforma, è necessario annullare la sottoscrizione dall'evento quando l'implementazione della piattaforma non è più necessaria. Significa anche che potrebbe essere necessario per le implementazioni della piattaforma implementare `IDisposable` e gestire la pulizia delle relative risorse nei metodi `Dispose`. L'applicazione di esempio illustra questo scenario nelle implementazioni della piattaforma `TextToSpeechService`.

Quando un'applicazione termina l'uso di un'implementazione della piattaforma che implementa `IDisposable`, deve chiamare l'implementazione `Dispose` dell'oggetto. Un modo per ottenere questo risultato è un'istruzione `using`:

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
using (service as IDisposable)
{
        await service.SpeakAsync("Hello world");
}
```

In questo esempio, dopo la chiamata del metodo `SpeakAsync`, l'istruzione `using` elimina automaticamente l'oggetto implementazione della piattaforma. Il risultato è la chiamata del metodo `Dispose` dell'oggetto, che esegue la pulizia necessaria.

Per altre informazioni sulla chiamata del metodo `Dispose` di un oggetto, vedere [Uso di oggetti che implementano IDisposable](/dotnet/standard/garbage-collection/using-objects).

## <a name="related-links"></a>Collegamenti correlati

- [Demo di DependencyService (esempio)](https://github.com/xamarin/xamarin-forms-samples/tree/master/DependencyService)
- [Risoluzione delle dipendenze in Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md)
