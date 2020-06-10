---
title: " Xamarin.Forms registrazione e risoluzione DependencyService" Descrizione: "questo articolo spiega come usare la Xamarin.Forms classe DependencyService per richiamare la funzionalità della piattaforma nativa".
ms. prod: Novell MS. AssetID: 5d019604-4f6f-4932-9b26-1fce3b4d88f8 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 06/05/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-dependencyservice-registration-and-resolution"></a>Xamarin.FormsRegistrazione e risoluzione DependencyService

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

Quando si usa Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) per richiamare la funzionalità della piattaforma nativa, le implementazioni della piattaforma devono essere registrate con `DependencyService` e quindi risolte dal codice condiviso per richiamarle.

## <a name="register-platform-implementations"></a>Registrare le implementazioni della piattaforma

Le implementazioni della piattaforma devono essere registrate in [`DependencyService`](xref:Xamarin.Forms.DependencyService) in modo che Xamarin.Forms possa individuarle in fase di esecuzione.

È possibile eseguire la registrazione con [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) o con i [`Register`](xref:Xamarin.Forms.DependencyService.Register*) metodi.

> [!IMPORTANT]
> Le build di rilascio dei progetti UWP che usano la compilazione .NET native devono registrare le implementazioni della piattaforma con i [`Register`](xref:Xamarin.Forms.DependencyService.Register*) metodi.

### <a name="registration-by-attribute"></a>Registrazione in base all'attributo

[`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute)Può essere usato per registrare un'implementazione della piattaforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService) . L'attributo indica che il tipo specificato offre un'implementazione concreta dell'interfaccia.

Nell'esempio seguente viene illustrato l'utilizzo [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) di per registrare l'implementazione di iOS dell' `IDeviceOrientationService` interfaccia:

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

In questo esempio, [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) Registra `DeviceOrientationService` con [`DependencyService`](xref:Xamarin.Forms.DependencyService) . Il tipo concreto viene quindi registrato per l'interfaccia che implementa.

Analogamente, le implementazioni dell' `IDeviceOrientationService` interfaccia su altre piattaforme devono essere registrate con [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) .

> [!NOTE]
> La registrazione con il [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) viene eseguita a livello di spazio dei nomi.

### <a name="registration-by-method"></a>Registrazione in base al metodo

I [`DependencyService.Register`](xref:Xamarin.Forms.DependencyService.Register*) metodi possono essere usati per registrare un'implementazione della piattaforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService) .

Nell'esempio seguente viene illustrato l'utilizzo del [`Register`](xref:Xamarin.Forms.DependencyService.Register*) metodo per registrare l'implementazione di iOS dell' `IDeviceOrientationService` interfaccia:

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

In questo esempio, il [`Register`](xref:Xamarin.Forms.DependencyService.Register*) metodo registra il tipo concreto, `DeviceOrientationService` , rispetto all' `IDeviceOrientationService` interfaccia. In alternativa, [`Register`](xref:Xamarin.Forms.DependencyService.Register*) è possibile usare un overload del metodo per registrare un'implementazione della piattaforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService) :

```csharp
DependencyService.Register<DeviceOrientationService>();
```

In questo esempio, il [`Register`](xref:Xamarin.Forms.DependencyService.Register*) metodo registra `DeviceOrientationService` con [`DependencyService`](xref:Xamarin.Forms.DependencyService) . Il tipo concreto viene quindi registrato per l'interfaccia che implementa.

Analogamente, le implementazioni dell' `IDeviceOrientationService` interfaccia su altre piattaforme possono essere registrate con i [`Register`](xref:Xamarin.Forms.DependencyService.Register*) metodi.

> [!IMPORTANT]
> La registrazione con i [`Register`](xref:Xamarin.Forms.DependencyService.Register*) metodi deve essere eseguita nei progetti della piattaforma, prima che la funzionalità fornita dall'implementazione della piattaforma venga richiamata dal codice condiviso.

## <a name="resolve-the-platform-implementations"></a>Risolvere le implementazioni della piattaforma

Le implementazioni della piattaforma devono essere risolte prima di essere richiamate. Questa operazione viene in genere eseguita nel codice condiviso usando il [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) metodo. Tuttavia, può anche essere eseguita con il [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) metodo.

Per impostazione predefinita, [`DependencyService`](xref:Xamarin.Forms.DependencyService) risolverà solo le implementazioni della piattaforma con costruttori senza parametri. Tuttavia, è possibile inserire un metodo di risoluzione delle dipendenze in Xamarin.Forms che usa un contenitore di inserimento delle dipendenze o metodi factory per risolvere le implementazioni della piattaforma. Questo approccio può essere usato per risolvere le implementazioni della piattaforma che hanno costruttori con parametri. Per ulteriori informazioni, vedere la pagina relativa [alla risoluzione delle dipendenze in Xamarin.Forms ](~/xamarin-forms/internals/dependency-resolution.md).

> [!IMPORTANT]
> La chiamata di un'implementazione della piattaforma che non è stata registrata con l'oggetto [`DependencyService`](xref:Xamarin.Forms.DependencyService) comporterà la generazione di un' `NullReferenceException` eccezione.

### <a name="resolve-using-the-getlttgt-method"></a>Risolvere con il metodo Get&lt;T&gt;

Il [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) metodo recupera l'implementazione della piattaforma dell'interfaccia in fase di `T` esecuzione e ne crea un'istanza come singleton. Questa istanza avrà la stessa durata dell'applicazione e tutte le chiamate successive risolte nella stessa implementazione della piattaforma recupereranno la stessa istanza.

Il codice seguente illustra un esempio di chiamata al [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) metodo per risolvere l' `IDeviceOrientationService` interfaccia e quindi richiamare il `GetOrientation` Metodo:

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

Il [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) metodo recupera l'implementazione della piattaforma dell'interfaccia `T` in fase di esecuzione, usando un metodo di risoluzione delle dipendenze inserito in Xamarin.Forms con la [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver) classe. Se un metodo di risoluzione delle dipendenze non è stato inserito in Xamarin.Forms , il `Resolve<T>` metodo effettuerà il fallback alla chiamata al [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) metodo per recuperare l'implementazione della piattaforma. Per ulteriori informazioni su come inserire un metodo di risoluzione delle dipendenze in, vedere la pagina relativa Xamarin.Forms [alla risoluzione delle dipendenze in Xamarin.Forms ](~/xamarin-forms/internals/dependency-resolution.md).

Il codice seguente illustra un esempio di chiamata al [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) metodo per risolvere l' `IDeviceOrientationService` interfaccia e quindi richiamare il `GetOrientation` Metodo:

```csharp
IDeviceOrientationService service = DependencyService.Resolve<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Questo codice può essere in alternativa compresso in una singola riga:

```csharp
DeviceOrientation orientation = DependencyService.Resolve<IDeviceOrientationService>().GetOrientation();
```

> [!NOTE]
> Quando il [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) metodo esegue il fallback alla chiamata al [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) metodo, crea un'istanza dell'implementazione della piattaforma dell'interfaccia `T` come singleton, per impostazione predefinita. Tuttavia, è possibile modificare questo comportamento. Per altre informazioni, vedere [Gestire la durata degli oggetti risolti](#manage-the-lifetime-of-resolved-objects).

## <a name="manage-the-lifetime-of-resolved-objects"></a>Gestire la durata degli oggetti risolti

Il comportamento predefinito della [`DependencyService`](xref:Xamarin.Forms.DependencyService) classe consiste nel risolvere le implementazioni della piattaforma come singleton. Di conseguenza, le implementazioni della piattaforma avranno la stessa durata di un'applicazione.

Questo comportamento viene specificato con l' [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) argomento facoltativo nei [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) metodi e [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) . L' [`DependencyFetchTarget`](xref:Xamarin.Forms.DependencyFetchTarget) enumerazione definisce due membri:

- `GlobalInstance`, che restituisce l'implementazione della piattaforma come singleton.
- `NewInstance`, che restituisce una nuova istanza dell'implementazione della piattaforma. L'applicazione è quindi responsabile della gestione della durata dell'istanza dell'implementazione della piattaforma.

I [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) metodi e impostano entrambi gli argomenti facoltativi su [`DependencyFetchTarget.GlobalInstance`](xref:Xamarin.Forms.DependencyFetchTarget) e pertanto le implementazioni della piattaforma vengono sempre risolte come singleton. Questo comportamento può essere modificato, in modo che vengano create nuove istanze delle implementazioni della piattaforma, specificando [`DependencyFetchTarget.NewInstance`](xref:Xamarin.Forms.DependencyFetchTarget) come argomenti per i `Get<T>` `Resolve<T>` metodi e:

```csharp
ITextToSpeechService service = DependencyService.Get<ITextToSpeechService>(DependencyFetchTarget.NewInstance);
```

In questo esempio, [`DependencyService`](xref:Xamarin.Forms.DependencyService) Crea una nuova istanza dell'implementazione della piattaforma per l' `ITextToSpeechService` interfaccia. Anche tutte le chiamate successive per risolvere `ITextToSpeechService` creeranno nuove istanze.

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
- [Risoluzione delle dipendenze inXamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md)
