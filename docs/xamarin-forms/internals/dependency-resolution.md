---
title: Risoluzione delle dipendenze inXamarin.Forms
description: Questo articolo illustra come inserire un metodo di risoluzione delle dipendenze in in Xamarin.Forms modo che il contenitore di inserimento delle dipendenze di un'applicazione abbia il controllo sulla creazione e sulla durata di renderer, effetti e implementazioni di DependencyService personalizzati.
ms.prod: xamarin
ms.assetid: 491B87DC-14CB-4ADC-AC6C-40A7627B2524
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ae30b4a4b75906613baf8a2568548c8890ccb33a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139087"
---
# <a name="dependency-resolution-in-xamarinforms"></a>Risoluzione delle dipendenze inXamarin.Forms

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)

_Questo articolo illustra come inserire un metodo di risoluzione delle dipendenze in in Xamarin.Forms modo che il contenitore di inserimento delle dipendenze di un'applicazione abbia il controllo sulla creazione e sulla durata di renderer, effetti e implementazioni di DependencyService personalizzati. Gli esempi di codice in questo articolo sono tratti dall'esempio di [risoluzione delle dipendenze con i contenitori](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo) ._

Nel contesto di un' Xamarin.Forms applicazione che usa il modello MVC (Model-View-ViewModel), è possibile usare un contenitore di inserimento delle dipendenze per la registrazione e la risoluzione di modelli di visualizzazione e per la registrazione dei servizi e l'inserimento in modelli di visualizzazione. Durante la creazione del modello di visualizzazione, il contenitore inserisce tutte le dipendenze richieste. Se tali dipendenze non sono state create, il contenitore crea e risolve prima le dipendenze. Per altre informazioni sull'inserimento delle dipendenze, inclusi esempi di inserimento delle dipendenze nei modelli di visualizzazione, vedere [inserimento](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)delle dipendenze.

Il controllo sulla creazione e la durata dei tipi nei progetti di piattaforma viene tradizionalmente eseguito da Xamarin.Forms , che usa il `Activator.CreateInstance` metodo per creare istanze di renderer, effetti e [`DependencyService`](xref:Xamarin.Forms.DependencyService) implementazioni personalizzati. Sfortunatamente, questo limita il controllo degli sviluppatori sulla creazione e la durata di questi tipi e la possibilità di inserire le dipendenze. Questo comportamento può essere modificato inserendo un metodo di risoluzione delle dipendenze in Xamarin.Forms che controlla il modo in cui verranno creati i tipi, dal contenitore di inserimento delle dipendenze dell'applicazione o da Xamarin.Forms . Si noti tuttavia che non è necessario inserire un metodo di risoluzione delle dipendenze in Xamarin.Forms . Xamarin.Formscontinuerà a creare e gestire la durata dei tipi nei progetti della piattaforma se non viene inserito un metodo di risoluzione delle dipendenze.

> [!NOTE]
> Questo articolo è incentrato sull'inserimento di un metodo di risoluzione delle dipendenze in Xamarin.Forms che risolve i tipi registrati usando un contenitore di inserimento delle dipendenze, ma è anche possibile inserire un metodo di risoluzione delle dipendenze che usa i metodi factory per risolvere i tipi registrati. Per ulteriori informazioni, vedere l'esempio relativo alla [risoluzione delle dipendenze mediante i metodi factory](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-factoriesdemo) .

## <a name="injecting-a-dependency-resolution-method"></a>Inserimento di un metodo di risoluzione delle dipendenze

La [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver) classe fornisce la possibilità di inserire un metodo di risoluzione delle dipendenze in Xamarin.Forms , usando il [`ResolveUsing`](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) metodo. Quindi, quando Xamarin.Forms necessita di un'istanza di un determinato tipo, al metodo di risoluzione delle dipendenze viene data la possibilità di fornire l'istanza. Se il metodo di risoluzione delle dipendenze restituisce `null` per un tipo richiesto, viene Xamarin.Forms eseguito il tentativo di creare l'istanza del tipo utilizzando il `Activator.CreateInstance` metodo.

Nell'esempio seguente viene illustrato come impostare il metodo di risoluzione delle dipendenze con il [`ResolveUsing`](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) Metodo:

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

In questo esempio, il metodo di risoluzione delle dipendenze è impostato su un'espressione lambda che usa il contenitore di inserimento delle dipendenze di Autofac per risolvere tutti i tipi registrati con il contenitore. In caso contrario, `null` verrà restituito, che comporterà Xamarin.Forms il tentativo di risolvere il tipo.

> [!NOTE]
> L'API usata da un contenitore di inserimento delle dipendenze è specifica per il contenitore. Gli esempi di codice in questo articolo usano Autofac come contenitore di inserimento di dipendenze, che fornisce i `IContainer` `ContainerBuilder` tipi e. È possibile usare ugualmente contenitori di inserimento di dipendenze alternativi, ma usare API diverse da quelle presentate qui.

Si noti che non è necessario impostare il metodo di risoluzione delle dipendenze durante l'avvio dell'applicazione. È possibile impostarlo in qualsiasi momento. L'unico vincolo è che Xamarin.Forms deve conoscere il metodo di risoluzione delle dipendenze nel momento in cui l'applicazione tenta di utilizzare i tipi archiviati nel contenitore di inserimento delle dipendenze. Pertanto, se sono presenti servizi nel contenitore di inserimento delle dipendenze che l'applicazione richiederà durante l'avvio, il metodo di risoluzione delle dipendenze dovrà essere impostato in anticipo nel ciclo di vita dell'applicazione. Analogamente, se il contenitore di inserimento delle dipendenze gestisce la creazione e la durata di un particolare [`Effect`](xref:Xamarin.Forms.Effect) , Xamarin.Forms sarà necessario conoscere il metodo di risoluzione delle dipendenze prima di tentare di creare una vista che lo utilizza `Effect` .

> [!WARNING]
> La registrazione e la risoluzione dei tipi con un contenitore di inserimento di dipendenze comporta un costo in termini di prestazioni a causa dell'utilizzo della reflection da parte del contenitore per la creazione di ogni tipo, soprattutto se le dipendenze vengono ricostruite per ogni navigazione di pagina nell'applicazione. Se le dipendenze presenti sono numerose o complete, il costo della creazione può aumentare in modo significativo.

## <a name="registering-types"></a>Registrazione di tipi

I tipi devono essere registrati con il contenitore di inserimento delle dipendenze prima che possano risolverli tramite il metodo di risoluzione delle dipendenze. Nell'esempio di codice riportato di seguito vengono illustrati i metodi di registrazione esposti dall'applicazione di esempio nella `App` classe per il contenitore Autofac:

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

Quando un'applicazione usa un metodo di risoluzione delle dipendenze per risolvere i tipi da un contenitore, le registrazioni dei tipi vengono in genere eseguite da progetti di piattaforma. Questo consente ai progetti della piattaforma di registrare i tipi per renderer, effetti e [`DependencyService`](xref:Xamarin.Forms.DependencyService) implementazioni personalizzati.

Dopo la registrazione del tipo da un progetto di piattaforma, l' `IContainer` oggetto deve essere compilato, operazione eseguita chiamando il `BuildContainer` metodo. Questo metodo richiama il metodo di Autofac `Build` sull' `ContainerBuilder` istanza, che compila un nuovo contenitore di inserimento delle dipendenze che contiene le registrazioni effettuate.

Nelle sezioni seguenti, una `Logger` classe che implementa l' `ILogger` interfaccia viene inserita nei costruttori della classe. La `Logger` classe implementa la funzionalità di registrazione semplice utilizzando il `Debug.WriteLine` metodo e viene utilizzata per illustrare il modo in cui i servizi possono essere inseriti in renderer, effetti e [`DependencyService`](xref:Xamarin.Forms.DependencyService) implementazioni personalizzati.

### <a name="registering-custom-renderers"></a>Registrazione di renderer personalizzati

L'applicazione di esempio include una pagina che riproduce video web, la cui origine XAML è illustrata nell'esempio seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             ...>
    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />
</ContentPage>
```

La `VideoPlayer` vista è implementata in ogni piattaforma da una `VideoPlayerRenderer` classe che fornisce la funzionalità per la riproduzione del video. Per ulteriori informazioni su queste classi renderer personalizzate, vedere [Implementing a video player](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md).

In iOS e nella piattaforma UWP (Universal Windows Platform) (UWP), le `VideoPlayerRenderer` classi hanno il costruttore seguente, che richiede un `ILogger` argomento:

```csharp
public VideoPlayerRenderer(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

In tutte le piattaforme, la registrazione dei tipi con il contenitore di inserimento delle dipendenze viene eseguita dal `RegisterTypes` metodo, che viene richiamato prima della piattaforma che carica l'applicazione con il `LoadApplication(new App())` metodo. Nell'esempio seguente viene illustrato il `RegisterTypes` Metodo sulla piattaforma iOS:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<FormsVideoLibrary.iOS.VideoPlayerRenderer>();
    App.BuildContainer();
}
```

In questo esempio, il `Logger` tipo concreto viene registrato tramite un mapping con il relativo tipo di interfaccia e il `VideoPlayerRenderer` tipo viene registrato direttamente senza un mapping di interfaccia. Quando l'utente passa alla pagina che contiene la `VideoPlayer` visualizzazione, viene richiamato il metodo di risoluzione delle dipendenze per risolvere il `VideoPlayerRenderer` tipo dal contenitore di inserimento delle dipendenze, che risolve e inserisce il `Logger` tipo nel `VideoPlayerRenderer` costruttore.

Il `VideoPlayerRenderer` costruttore nella piattaforma Android è leggermente più complicato perché richiede un `Context` argomento oltre all' `ILogger` argomento:

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

In questo esempio, il `App.RegisterTypeWithParameters` metodo registra `VideoPlayerRenderer` con il contenitore di inserimento delle dipendenze. Il metodo di registrazione garantisce che l' `MainActivity` istanza venga inserita come `Context` argomento e che il `Logger` tipo verrà inserito come `ILogger` argomento.

### <a name="registering-effects"></a>Registrazione degli effetti

Nell'applicazione di esempio è inclusa una pagina che utilizza un effetto di rilevamento del tocco per trascinare [`BoxView`](xref:Xamarin.Forms.BoxView) le istanze nella pagina. [`Effect`](xref:Xamarin.Forms.Effect)Viene aggiunto a `BoxView` utilizzando il codice seguente:

```csharp
var boxView = new BoxView { ... };
var touchEffect = new TouchEffect();
boxView.Effects.Add(touchEffect);
```

La `TouchEffect` classe è un [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) implementata in ogni piattaforma da una `TouchEffect` classe che è un oggetto `PlatformEffect` . La `TouchEffect` Classe Platform fornisce la funzionalità per trascinare l'oggetto `BoxView` intorno alla pagina. Per ulteriori informazioni su queste classi di effetto, vedere [richiamo di eventi dagli effetti](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

In tutte le piattaforme la `TouchEffect` classe presenta il costruttore seguente, che richiede un `ILogger` argomento:

```csharp
public TouchEffect(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

In tutte le piattaforme, la registrazione dei tipi con il contenitore di inserimento delle dipendenze viene eseguita dal `RegisterTypes` metodo, che viene richiamato prima della piattaforma che carica l'applicazione con il `LoadApplication(new App())` metodo. L'esempio seguente illustra il `RegisterTypes` metodo nella piattaforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<TouchTracking.Droid.TouchEffect>();
    App.BuildContainer();
}
```

In questo esempio, il `Logger` tipo concreto viene registrato tramite un mapping con il relativo tipo di interfaccia e il `TouchEffect` tipo viene registrato direttamente senza un mapping di interfaccia. Quando l'utente passa alla pagina contenente un'istanza a [`BoxView`](xref:Xamarin.Forms.BoxView) cui è `TouchEffect` associato, viene richiamato il metodo di risoluzione delle dipendenze per risolvere il tipo di piattaforma `TouchEffect` dal contenitore di inserimento delle dipendenze, che risolve e inserisce il `Logger` tipo nel `TouchEffect` costruttore.

### <a name="registering-dependencyservice-implementations"></a>Registrazione delle implementazioni di DependencyService

L'applicazione di esempio include una pagina che usa le [`DependencyService`](xref:Xamarin.Forms.DependencyService) implementazioni in ogni piattaforma per consentire all'utente di selezionare una foto dalla libreria immagini del dispositivo. L' `IPhotoPicker` interfaccia definisce la funzionalità implementata dalle `DependencyService` implementazioni e viene mostrata nell'esempio seguente:

```csharp
public interface IPhotoPicker
{
    Task<Stream> GetImageStreamAsync();
}
```

In ogni progetto di piattaforma la `PhotoPicker` classe implementa l' `IPhotoPicker` interfaccia usando le API della piattaforma. Per ulteriori informazioni su questi servizi di dipendenza, vedere [selezione di una foto dalla raccolta immagini](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

In iOS e UWP le `PhotoPicker` classi hanno il costruttore seguente, che richiede un `ILogger` argomento:

```csharp
public PhotoPicker(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

In tutte le piattaforme, la registrazione dei tipi con il contenitore di inserimento delle dipendenze viene eseguita dal `RegisterTypes` metodo, che viene richiamato prima della piattaforma che carica l'applicazione con il `LoadApplication(new App())` metodo. Nell'esempio seguente viene illustrato il `RegisterTypes` metodo in UWP:

```csharp
void RegisterTypes()
{
    DIContainerDemo.App.RegisterType<ILogger, Logger>();
    DIContainerDemo.App.RegisterType<IPhotoPicker, Services.UWP.PhotoPicker>();
    DIContainerDemo.App.BuildContainer();
}
```

In questo esempio, il `Logger` tipo concreto viene registrato tramite un mapping con il relativo tipo di interfaccia e il `PhotoPicker` tipo viene registrato anche tramite un mapping di interfaccia.

Il `PhotoPicker` costruttore nella piattaforma Android è leggermente più complicato perché richiede un `Context` argomento oltre all' `ILogger` argomento:

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

In questo esempio, il `App.RegisterTypeWithParameters` metodo registra `PhotoPicker` con il contenitore di inserimento delle dipendenze. Il metodo di registrazione garantisce che l' `MainActivity` istanza venga inserita come `Context` argomento e che il `Logger` tipo verrà inserito come `ILogger` argomento.

Quando l'utente passa alla pagina di selezione delle foto e sceglie di selezionare una foto, il `OnSelectPhotoButtonClicked` gestore viene eseguito:

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

Quando [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) viene richiamato il metodo, viene richiamato il metodo di risoluzione delle dipendenze per risolvere il `PhotoPicker` tipo dal contenitore di inserimento delle dipendenze, che risolve e inserisce il `Logger` tipo nel `PhotoPicker` costruttore.

> [!NOTE]
> Il [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) metodo deve essere utilizzato durante la risoluzione di un tipo dal contenitore di inserimento delle dipendenze dell'applicazione tramite [`DependencyService`](xref:Xamarin.Forms.DependencyService) .

## <a name="related-links"></a>Collegamenti correlati

- [Risoluzione delle dipendenze con i contenitori (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)
- [Inserimento delle dipendenze](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)
- [Implementazione di un lettore video](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)
- [Richiamo di eventi dagli effetti](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
- [Selezione di una foto dalla raccolta immagini](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
