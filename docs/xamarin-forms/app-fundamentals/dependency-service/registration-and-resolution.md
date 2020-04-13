---
title: Registrazione e risoluzione di DependencyService di Xamarin.Forms
description: Questo articolo illustra come usare la classe DependencyService di Xamarin.Forms per richiamare la funzionalità della piattaforma nativa.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/05/2019
ms.openlocfilehash: 6e666c16c9b1afc3478f524cae2f84d6704319c2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70199229"
---
# <a name="xamarinforms-dependencyservice-registration-and-resolution"></a>Registrazione e risoluzione di DependencyService di Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

Quando si utilizza Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) per richiamare la funzionalità della `DependencyService`piattaforma nativa, le implementazioni della piattaforma devono essere registrate con l'oggetto , quindi risolte dal codice condiviso per richiamarle.

## <a name="register-platform-implementations"></a>Registrare le implementazioni della piattaforma

Le implementazioni della piattaforma [`DependencyService`](xref:Xamarin.Forms.DependencyService) devono essere registrate con l'oggetto in modo che Xamarin.Forms possa individuarle in fase di esecuzione.

La registrazione può [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)essere eseguita [`Register`](xref:Xamarin.Forms.DependencyService.Register*) con il , o con i metodi .

> [!IMPORTANT]
> Le build di rilascio di progetti UWP che usano [`Register`](xref:Xamarin.Forms.DependencyService.Register*) la compilazione nativa .NET devono registrare le implementazioni della piattaforma con i metodi.

### <a name="registration-by-attribute"></a>Registrazione in base all'attributo

L'oggetto [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) può essere utilizzato per [`DependencyService`](xref:Xamarin.Forms.DependencyService)registrare un'implementazione della piattaforma con l'oggetto . L'attributo indica che il tipo specificato offre un'implementazione concreta dell'interfaccia.

L'esempio seguente [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) viene illustrato l'utilizzo di `IDeviceOrientationService` per registrare l'implementazione iOS dell'interfaccia:The following example shows using the to register the iOS implementation of the interface:

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

In questo esempio, l'oggetto [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) registra con `DeviceOrientationService` l'oggetto [`DependencyService`](xref:Xamarin.Forms.DependencyService). Il tipo concreto viene quindi registrato per l'interfaccia che implementa.

Analogamente, le implementazioni dell'interfaccia `IDeviceOrientationService` su altre [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)piattaforme devono essere registrate con l'oggetto .

> [!NOTE]
> La registrazione [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) con l'oggetto viene eseguita a livello di spazio dei nomi.

### <a name="registration-by-method"></a>Registrazione in base al metodo

I [`DependencyService.Register`](xref:Xamarin.Forms.DependencyService.Register*) metodi possono essere utilizzati per [`DependencyService`](xref:Xamarin.Forms.DependencyService)registrare un'implementazione della piattaforma con l'oggetto .

L'esempio seguente [`Register`](xref:Xamarin.Forms.DependencyService.Register*) viene illustrato l'utilizzo del `IDeviceOrientationService` metodo per registrare l'implementazione iOS dell'interfaccia:

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

In questo esempio, il [`Register`](xref:Xamarin.Forms.DependencyService.Register*) metodo registra `DeviceOrientationService`il `IDeviceOrientationService` tipo concreto, , in base all'interfaccia . In alternativa, è [`Register`](xref:Xamarin.Forms.DependencyService.Register*) possibile utilizzare un overload del metodo [`DependencyService`](xref:Xamarin.Forms.DependencyService)per registrare un'implementazione della piattaforma con:

```csharp
DependencyService.Register<DeviceOrientationService>();
```

In questo esempio, il [`Register`](xref:Xamarin.Forms.DependencyService.Register*) `DeviceOrientationService` metodo [`DependencyService`](xref:Xamarin.Forms.DependencyService)registra l'oggetto con l'oggetto . Il tipo concreto viene quindi registrato per l'interfaccia che implementa.

Analogamente, le implementazioni dell'interfaccia `IDeviceOrientationService` su altre [`Register`](xref:Xamarin.Forms.DependencyService.Register*) piattaforme possono essere registrate con i metodi.

> [!IMPORTANT]
> La registrazione [`Register`](xref:Xamarin.Forms.DependencyService.Register*) con i metodi deve essere eseguita nei progetti di piattaforma, prima che la funzionalità fornita dall'implementazione della piattaforma venga richiamata dal codice condiviso.

## <a name="resolve-the-platform-implementations"></a>Risolvere le implementazioni della piattaforma

Le implementazioni della piattaforma devono essere risolte prima di essere richiamate. Questa operazione viene in genere [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) eseguita nel codice condiviso utilizzando il metodo . Tuttavia, può anche essere [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) eseguita con il metodo.

Per impostazione [`DependencyService`](xref:Xamarin.Forms.DependencyService) predefinita, il risolverà solo le implementazioni della piattaforma che hanno costruttori senza parametri. È tuttavia possibile inserire in Xamarin.Forms un metodo di risoluzione delle dipendenze che usa un contenitore di inserimento delle dipendenze o metodi factory per risolvere le implementazioni della piattaforma. Questo approccio può essere usato per risolvere le implementazioni della piattaforma che hanno costruttori con parametri. Per altre informazioni, vedere [Risoluzione delle dipendenze in Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

> [!IMPORTANT]
> La chiamata di un'implementazione della [`DependencyService`](xref:Xamarin.Forms.DependencyService) piattaforma che `NullReferenceException` non è stata registrata con l'avrà come generato un'eccezione.

### <a name="resolve-using-the-getlttgt-method"></a>Risolvere con il metodo Get&lt;T&gt;

Il [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) metodo recupera l'implementazione della piattaforma dell'interfaccia `T` in fase di esecuzione e ne crea un'istanza come singleton. Questa istanza avrà la stessa durata dell'applicazione e tutte le chiamate successive risolte nella stessa implementazione della piattaforma recupereranno la stessa istanza.

Nel codice seguente viene illustrato [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) un esempio `IDeviceOrientationService` di chiamata al `GetOrientation` metodo per risolvere l'interfaccia e quindi richiamarne il metodo:The following code shows an example of calling the method to resolve the interface, and then invoking its method:

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Questo codice può essere in alternativa compresso in una singola riga:

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> Il [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) metodo crea un'istanza dell'implementazione della piattaforma dell'interfaccia `T` come singleton, per impostazione predefinita. Tuttavia, è possibile modificare questo comportamento. Per altre informazioni, vedere [Gestire la durata degli oggetti risolti](#manage-the-lifetime-of-resolved-objects).

### <a name="resolve-using-the-resolvelttgt-method"></a>Risolvere con il metodo Resolve&lt;T&gt;

Il [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) metodo recupera l'implementazione della piattaforma dell'interfaccia `T` in fase di esecuzione, utilizzando un metodo [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver) di risoluzione delle dipendenze che è stato inserito in Xamarin.Forms con la classe . Se un metodo di risoluzione delle dipendenze non è stato `Resolve<T>` inserito in [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) Xamarin.Forms, il metodo eseguirà il fallback alla chiamata del metodo per recuperare l'implementazione della piattaforma. Per altre informazioni sull'inserimento di un metodo di risoluzione delle dipendenze in Xamarin.Forms, vedere [Risoluzione delle dipendenze in Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

Nel codice seguente viene illustrato [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) un esempio `IDeviceOrientationService` di chiamata al `GetOrientation` metodo per risolvere l'interfaccia e quindi richiamarne il metodo:The following code shows an example of calling the method to resolve the interface, and then invoking its method:

```csharp
IDeviceOrientationService service = DependencyService.Resolve<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Questo codice può essere in alternativa compresso in una singola riga:

```csharp
DeviceOrientation orientation = DependencyService.Resolve<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> Quando [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) il metodo esegue [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) il fallback alla chiamata al metodo `T` , crea un'istanza dell'implementazione della piattaforma dell'interfaccia come singleton, per impostazione predefinita. Tuttavia, è possibile modificare questo comportamento. Per altre informazioni, vedere [Gestire la durata degli oggetti risolti](#manage-the-lifetime-of-resolved-objects).

## <a name="manage-the-lifetime-of-resolved-objects"></a>Gestire la durata degli oggetti risolti

Il comportamento predefinito [`DependencyService`](xref:Xamarin.Forms.DependencyService) della classe consiste nel risolvere le implementazioni della piattaforma come singleton. Di conseguenza, le implementazioni della piattaforma avranno la stessa durata di un'applicazione.

Questo comportamento viene [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) specificato con [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) l'argomento facoltativo sui metodi e [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) . L'enumerazione [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) definisce due membri:

- `GlobalInstance`, che restituisce l'implementazione della piattaforma come singleton.
- `NewInstance`, che restituisce una nuova istanza dell'implementazione della piattaforma. L'applicazione è quindi responsabile della gestione della durata dell'istanza dell'implementazione della piattaforma.

I [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) metodi e impostano [`DependencyFetchTarget.GlobalInstance`](xref:Xamarin.Forms.DependencyFetchTarget)entrambi gli argomenti facoltativi su , e pertanto le implementazioni della piattaforma vengono sempre risolte come singleton. Questo comportamento può essere modificato, in modo che vengano [`DependencyFetchTarget.NewInstance`](xref:Xamarin.Forms.DependencyFetchTarget) create nuove `Get<T>` istanze delle implementazioni della piattaforma, specificando come argomenti per i metodi e : `Resolve<T>`

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
```

In questo esempio, l'oggetto [`DependencyService`](xref:Xamarin.Forms.DependencyService) crea una `ITextToSpeechService` nuova istanza dell'implementazione della piattaforma per l'interfaccia. Anche tutte le chiamate successive per risolvere `ITextToSpeechService` creeranno nuove istanze.

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

- [Demo di DependencyService (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Risoluzione delle dipendenze in Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md)
