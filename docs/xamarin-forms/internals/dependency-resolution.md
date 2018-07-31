---
title: Risoluzione delle dipendenze in xamarin. Forms
description: Questo articolo illustra come inserire un metodo di risoluzione delle dipendenze in xamarin. Forms in modo da contenitore di inserimento delle dipendenze di un'applicazione ha il controllo tramite la creazione e la durata del renderer personalizzati e gli effetti DependencyService implementazioni.
ms.prod: xamarin
ms.assetid: 491B87DC-14CB-4ADC-AC6C-40A7627B2524
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2018
ms.openlocfilehash: 8952f98045d9830e9b8f25a7d4b93a5e4310cb32
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351583"
---
# <a name="dependency-resolution-in-xamarinforms"></a>Risoluzione delle dipendenze in xamarin. Forms

_Questo articolo illustra come inserire un metodo di risoluzione delle dipendenze in xamarin. Forms in modo da contenitore di inserimento delle dipendenze di un'applicazione ha il controllo tramite la creazione e la durata del renderer personalizzati e gli effetti DependencyService implementazioni. Gli esempi di codice in questo articolo vengono forniti dal [risoluzione delle dipendenze con i contenitori](https://developer.xamarin.com/samples/xamarin-forms/Advanced/DependencyResolution/DIContainerDemo/) esempio._

Nel contesto di un'applicazione xamarin. Forms che utilizza il modello Model-View-ViewModel (MVVM), un contenitore di inserimento delle dipendenze può essere utilizzato per la registrazione e la risoluzione di visualizzazione di modelli e per la registrazione di servizi e li inserisce nei modelli di visualizzazione. Durante la creazione di modelli di visualizzazione, il contenitore inserisce tutte le dipendenze necessarie. Se queste dipendenze non sono state create, il contenitore viene creato e risolve le dipendenze prima di tutto. Per altre informazioni sull'inserimento di dipendenze, inclusi gli esempi di inserimento di dipendenze in visualizzazione di modelli, vedere [inserimento delle dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

Controllare la creazione e della durata dei tipi nei progetti di piattaforma è in genere eseguita da xamarin. Forms, che usa il `Activator.CreateInstance` metodo per creare istanze di renderer personalizzati, gli effetti, e [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implementazioni. Sfortunatamente, questo limita il controllo dello sviluppatore tramite la creazione e la durata di questi tipi e la possibilità di inserire le dipendenze al loro interno. Questo comportamento può essere modificato dall'inserimento di un metodo di risoluzione delle dipendenze in xamarin. Forms che controlla come tipi verranno creati: contenitore di inserimento delle dipendenze dell'applicazione oppure da xamarin. Forms. Si noti tuttavia che non è necessario inserire un metodo di risoluzione delle dipendenze in xamarin. Forms. Xamarin. Forms continuerà a creare e gestire la durata dei tipi nei progetti di piattaforma, se non viene inserito un metodo di risoluzione delle dipendenze.

> [!NOTE]
> Questo articolo è incentrato sulla inserimento di un metodo di risoluzione delle dipendenze in xamarin. Forms che risolve i tipi registrati tramite un contenitore di inserimento delle dipendenze, è anche possibile inserire un metodo di risoluzione delle dipendenze che usa i metodi factory per risolvere tipi registrati. Per altre informazioni, vedere la [risoluzione delle dipendenze con metodi Factory](https://developer.xamarin.com/samples/xamarin-forms/Advanced/DependencyResolution/FactoriesDemo/) esempio.

## <a name="injecting-a-dependency-resolution-method"></a>Inserimento di un metodo di risoluzione delle dipendenze

Il [ `DependencyResolver` ](xref:Xamarin.Forms.Internals.DependencyResolver) classe offre la possibilità di inserire un metodo di risoluzione delle dipendenze in xamarin. Forms, utilizzando le [ `ResolveUsing` ](Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) (metodo). Quindi, quando xamarin. Forms è un'istanza di un determinato tipo, il metodo di risoluzione delle dipendenze viene data la possibilità per fornire l'istanza. Se restituisce il metodo di risoluzione delle dipendenze `null` per un tipo di richiesto, xamarin. Forms opta per provare a creare il tipo di istanza stesso tramite il `Activator.CreateInstance` (metodo).

Nell'esempio seguente viene illustrato come impostare il metodo di risoluzione delle dipendenze con il [ `ResolveUsing` ](Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) metodo:

```csharp
using Autofac;
using Xamarin.Forms.Internals;
...

public partial class App : Application
{
    // IContainer and ContainerBuilder are provided by Autofac
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();

    public App()
    {
        ...
        DependencyResolver.ResolveUsing(type => container.IsRegistered(type) ? container.Resolve(type) : null);
        ...
    }
    ...
}
```

In questo esempio, il metodo di risoluzione delle dipendenze è impostato su un'espressione lambda che utilizza il contenitore di inserimento delle dipendenze di Autofac per risolvere tutti i tipi che sono stati registrati con il contenitore. In caso contrario, `null` verrà restituito, verrà visualizzato un risultato nel tentativo di risolvere il tipo di xamarin. Forms.

> [!NOTE]
> L'API usata da un contenitore di inserimento delle dipendenze è specifico per il contenitore. Gli esempi di codice in questo articolo usano Autofac come contenitore dependency injection, che fornisce il `IContainer` e `ContainerBuilder` tipi. I contenitori di dipendenze alternativo sono ugualmente possibile usare, ma usano API diverse rispetto a descritte di seguito.

Si noti che non è necessario impostare il metodo di risoluzione delle dipendenze durante l'avvio dell'applicazione. Può essere impostata in qualsiasi momento. L'unico vincolo è che xamarin. Forms deve conoscere il metodo di risoluzione delle dipendenze nel momento in cui l'applicazione tenta di utilizzare i tipi archiviati nel contenitore di inserimento delle dipendenze. Pertanto, se sono presenti servizi nel contenitore di inserimento delle dipendenze che richiederà l'applicazione durante l'avvio, il metodo di risoluzione delle dipendenze avrà da impostare nelle prime fasi del ciclo di vita dell'applicazione. Analogamente, se il contenitore di inserimento delle dipendenze gestisce la creazione e la durata di un determinato [ `Effect` ](xref:Xamarin.Forms.Effect), xamarin. Forms dovrà prendere in considerazione il metodo di risoluzione delle dipendenze prima tenta di creare una vista che che verrà usata `Effect`.

> [!WARNING]
> La registrazione e la risoluzione dei tipi con un contenitore di inserimento delle dipendenze influisce sulle prestazioni dei costi a causa di uso del contenitore della reflection per la creazione di ogni tipo, soprattutto se le dipendenze sono in corso ricostruite per ogni navigazione tra le pagine nell'applicazione. Se sono presenti dipendenze molte o complete, può aumentare sensibilmente il costo della creazione.

## <a name="registering-types"></a>Registrazione dei tipi

Tipi devono essere registrati con il contenitore di inserimento delle dipendenze e poter risolvere essi tramite il metodo di risoluzione delle dipendenze. Esempio di codice seguente illustra i metodi di registrazione che espone l'applicazione di esempio nel `App` (classe), per il contenitore Autofac:

```csharp
using Autofac;
using Autofac.Core;
...

public partial class App : Application
{
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();
    ...

    public static void RegisterType<T>() where T : class
    {
        builder.RegisterType<T>();
    }

    public static void RegisterType<TInterface, T>() where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>().As<TInterface>();
    }

    public static void RegisterTypeWithParameters<T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where T : class
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        });
    }

    public static void RegisterTypeWithParameters<TInterface, T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        }).As<TInterface>();
    }

    public static void BuildContainer()
    {
        container = builder.Build();
    }
    ...
}
```

Quando un'applicazione usa un metodo di risoluzione delle dipendenze per risolvere i tipi da un contenitore, le registrazioni di tipo vengono in genere eseguite dai progetti di piattaforma. In questo modo i progetti di piattaforma registrare i tipi di renderer personalizzati, gli effetti, e [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implementazioni.

Dopo la registrazione di tipo da un progetto di piattaforma, il `IContainer` oggetto deve essere compilato, quale operazione viene eseguita chiamando il `BuildContainer` (metodo). Questo metodo richiama di Autofac `Build` metodo di `ContainerBuilder` istanza, che compila un nuovo contenitore di inserimento delle dipendenze che contiene le registrazioni che sono state apportate.

Nelle sezioni che seguono, un `Logger` classe che implementa il `ILogger` interfaccia viene inserita nelle costruttori della classe. Il `Logger` funzionalità di registrazione semplice implementa classe usando il `Debug.WriteLine` metodo e viene usato per illustrare come servizi possono essere inseriti nel renderer personalizzati, gli effetti, e [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implementazioni.

### <a name="registering-custom-renderers"></a>La registrazione di renderer personalizzati

L'applicazione di esempio include una pagina che riproduce video web, la cui origine XAML viene illustrato nell'esempio seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             ...>
    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />
</ContentPage>
```

Il `VideoPlayer` visualizzazione è implementata per ogni piattaforma da un `VideoPlayerRenderer` (classe), che fornisce la funzionalità per la riproduzione del video. Per altre informazioni su queste classi renderer personalizzati, vedere [implementazione di un lettore video](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md).

In iOS e Universal Windows Platform (UWP), il `VideoPlayerRenderer` classi hanno il costruttore seguente, che richiede un `ILogger` argomento:

```csharp
public VideoPlayerRenderer(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

In tutti e tre le piattaforme, registrazione di tipo con il contenitore di inserimento delle dipendenze viene eseguita mediante il `RegisterTypes` metodo, che viene richiamato prima la piattaforma il caricamento dell'applicazione con il `LoadApplication(new App())` (metodo). L'esempio seguente illustra il `RegisterTypes` metodo nella piattaforma iOS:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<FormsVideoLibrary.iOS.VideoPlayerRenderer>();
    App.BuildContainer();
}
```

In questo esempio, il `Logger` tipo concreto è registrato tramite un mapping per il tipo di interfaccia e il `VideoPlayerRenderer` tipo viene registrato direttamente senza un mapping di interfaccia. Quando l'utente passa alla pagina contenente le `VideoPlayer` visualizzazione, verrà richiamato il metodo di risoluzione delle dipendenze per risolvere il `VideoPlayerRenderer` tipo dal contenitore di inserimento delle dipendenze, che consente anche di risolvere e inserire il `Logger` digitare in il `VideoPlayerRenderer` costruttore.

Il `VideoPlayerRenderer` costruttore nella piattaforma Android è leggermente più complicato perché richiede un `Context` argomenti oltre al `ILogger` argomento:

```csharp
public VideoPlayerRenderer(Context context, ILogger logger) : base(context)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

L'esempio seguente illustra il `RegisterTypes` metodo nella piattaforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<FormsVideoLibrary.Droid.VideoPlayerRenderer>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

In questo esempio, il `App.RegisterTypeWithParameters` metodo registra la `VideoPlayerRenderer` con il contenitore di inserimento delle dipendenze. Il metodo di registrazione assicura che il `MainActivity` istanza verrà inserita come il `Context` argomento e che il `Logger` tipo verrà inserito come il `ILogger` argomento.

### <a name="registering-effects"></a>La registrazione di effetti

L'applicazione di esempio include una pagina che utilizza un touch rilevamento effetto a trascinare [ `BoxView` ](xref:Xamarin.Forms.BoxView) istanze intorno alla pagina. Il [ `Effect` ](xref:Xamarin.Forms.Effect) viene aggiunto al `BoxView` usando il codice seguente:

```csharp
var boxView = new BoxView { ... };
var touchEffect = new TouchEffect();
boxView.Effects.Add(touchEffect);
```

Il `TouchEffect` classe è una [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) che viene implementato in ogni piattaforma da un `TouchEffect` classe che ha un `PlatformEffect`. La piattaforma `TouchEffect` classe fornisce la funzionalità per il trascinamento di `BoxView` intorno alla pagina. Per altre informazioni su queste classi effetto, vedere [richiamo di eventi dagli effetti](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

In tutti e tre le piattaforme, il `TouchEffect` classe ha il seguente costruttore, che richiede un `ILogger` argomento:

```csharp
public TouchEffect(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

In tutti e tre le piattaforme, registrazione di tipo con il contenitore di inserimento delle dipendenze viene eseguita mediante il `RegisterTypes` metodo, che viene richiamato prima la piattaforma il caricamento dell'applicazione con il `LoadApplication(new App())` (metodo). L'esempio seguente illustra il `RegisterTypes` metodo nella piattaforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<TouchTracking.Droid.TouchEffect>();
    App.BuildContainer();
}
```

In questo esempio, il `Logger` tipo concreto è registrato tramite un mapping per il tipo di interfaccia e il `TouchEffect` tipo viene registrato direttamente senza un mapping di interfaccia. Quando l'utente passa alla pagina contenente una [ `BoxView` ](xref:Xamarin.Forms.BoxView) istanza con il `TouchEffect` collegati a esso, il metodo di risoluzione delle dipendenze verrà richiamato per risolvere la piattaforma `TouchEffect` tipo dalla dipendenza contenitore di inserimento, che consente anche di risolvere e inserire il `Logger` digitato il `TouchEffect` costruttore.

### <a name="registering-dependencyservice-implementations"></a>La registrazione di implementazioni DependencyService

L'applicazione di esempio include una pagina che utilizza [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implementazioni in ogni piattaforma per consentire all'utente di scegliere una foto dalla raccolta immagini del dispositivo. Il `IPhotoPicker` interfaccia definisce la funzionalità implementata dal `DependencyService` implementazioni e viene mostrato nell'esempio seguente:

```csharp
public interface IPhotoPicker
{
    Task<Stream> GetImageStreamAsync();
}
```

In ogni progetto della piattaforma, il `PhotoPicker` classe implementa il `IPhotoPicker` interfaccia usando le API della piattaforma. Per altre informazioni su questi servizi di dipendenza, vedere [selezionando una foto dalla raccolta immagini di](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

In iOS e UWP, il `PhotoPicker` classi hanno il costruttore seguente, che richiede un `ILogger` argomento:

```csharp
public PhotoPicker(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

In tutti e tre le piattaforme, registrazione di tipo con il contenitore di inserimento delle dipendenze viene eseguita mediante il `RegisterTypes` metodo, che viene richiamato prima la piattaforma il caricamento dell'applicazione con il `LoadApplication(new App())` (metodo). L'esempio seguente illustra il `RegisterTypes` metodo nella piattaforma UWP:

```csharp
void RegisterTypes()
{
    DIContainerDemo.App.RegisterType<ILogger, Logger>();
    DIContainerDemo.App.RegisterType<IPhotoPicker, Services.UWP.PhotoPicker>();
    DIContainerDemo.App.BuildContainer();
}
```

In questo esempio, il `Logger` tipo concreto è registrato tramite un mapping per il tipo di interfaccia e il `PhotoPicker` tipo viene registrato anche tramite un mapping dell'interfaccia.

Il `PhotoPicker` costruttore nella piattaforma Android è leggermente più complicato perché richiede un `Context` argomenti oltre al `ILogger` argomento:

```csharp
public PhotoPicker(Context context, ILogger logger)
{
    _context = context ?? throw new ArgumentNullException(nameof(context));
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

L'esempio seguente illustra il `RegisterTypes` metodo nella piattaforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<IPhotoPicker, Services.Droid.PhotoPicker>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

In questo esempio, il `App.RegisterTypeWithParameters` metodo registra la `PhotoPicker` con il contenitore di inserimento delle dipendenze. Il metodo di registrazione assicura che il `MainActivity` istanza verrà inserita come il `Context` argomento e che il `Logger` tipo verrà inserito come il `ILogger` argomento.

Quando l'utente passa alla pagina di selezione di foto e sceglie di selezionare una foto, il `OnSelectPhotoButtonClicked` viene eseguito il gestore:

```csharp
async void OnSelectPhotoButtonClicked(object sender, EventArgs e)
{
    ...
    var photoPickerService = DependencyService.Resolve<IPhotoPicker>();
    var stream = await photoPickerService.GetImageStreamAsync();
    if (stream != null)
    {
        image.Source = ImageSource.FromStream(() => stream);
    }
    ...
}
```

Quando la [ `DependencyService.Resolve<T>` ](xref:Xamarin.Forms.DependencyService.Resolve*) metodo viene richiamato, verrà richiamato il metodo di risoluzione delle dipendenze per risolvere il `PhotoPicker` tipo dal contenitore di inserimento delle dipendenze, che consente anche di risolvere e inserire il `Logger` tipo nel `PhotoPicker` costruttore.

> [!NOTE]
> Il [ `Resolve<T>` ](xref:Xamarin.Forms.DependencyService.Resolve*) metodo deve essere utilizzato durante la risoluzione di un tipo dal contenitore di inserimento delle dipendenze dell'applicazione tramite il [ `DependencyService` ](xref:Xamarin.Forms.DependencyService).

## <a name="related-links"></a>Collegamenti correlati

- [Risoluzione delle dipendenze con i contenitori (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Advanced/DependencyResolution/DIContainerDemo/)
- [Inserimento di dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)
- [Implementazione di un lettore video](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)
- [Richiamo di eventi dagli effetti](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
- [Selezione di una foto dalla raccolta immagini di](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
