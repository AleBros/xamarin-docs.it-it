---
title: Verifica l'orientamento del dispositivo
description: Utilizzare DependencyService per orientamento del dispositivo l'accesso da codice condiviso
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 09cd92b436be97f5490ac74890e4b0723bcd5701
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2018
---
# <a name="checking-device-orientation"></a>Verifica l'orientamento del dispositivo

In questo articolo consentirà di utilizzare [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) per controllare l'orientamento del dispositivo da codice condiviso utilizzando le API native in ciascuna piattaforma. Questa procedura dettagliata è basata su esistente `DeviceOrientation` plug-in da Ali Özgür. Vedere il [repository GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) per ulteriori informazioni.

- **[La creazione dell'interfaccia](#Creating_the_Interface)**  &ndash; comprendere come l'interfaccia viene creato nel codice condiviso.
- **[iOS implementazione](#iOS_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per iOS.
- **[Implementazione Android](#Android_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per Android.
- **[Implementazione UWP](#WindowsImplementation)**  &ndash; imparare a implementare l'interfaccia nel codice nativo per la piattaforma UWP (Universal Windows).
- **[Implementazione di codice condiviso](#Implementing_in_Shared_Code)**  &ndash; imparare a usare `DependencyService` chiamino l'implementazione native da codice condiviso.

L'applicazione utilizzando `DependencyService` avrà la struttura seguente:

![](device-orientation-images/orientation-diagram.png "Struttura dell'applicazione DependencyService")

> [!NOTE]
> È possibile rilevare se il dispositivo è in orientamento verticale o orizzontale in codice condiviso, come illustrato in [dispositivo Orientation]/guides/xamarin-forms/user-interface/layouts/device-orientation/#changes-in-orientation). Il metodo descritto in questo articolo Usa funzionalità native per ottenere ulteriori informazioni sull'orientamento, ad esempio se il dispositivo viene capovolta.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creazione dell'interfaccia

Innanzitutto, creare un'interfaccia nel codice condiviso che esprime la funzionalità che si intende implementare. Per questo esempio, l'interfaccia contiene un singolo metodo:

```csharp
namespace DependencyServiceSample.Abstractions
{
    public enum DeviceOrientations
    {
        Undefined,
        Landscape,
        Portrait
    }

    public interface IDeviceOrientation
    {
        DeviceOrientations GetOrientation();
    }
}
```

Scrivere codice per questa interfaccia nel codice condiviso consentirà l'app xamarin. Forms per l'orientamento del dispositivo API in ciascuna piattaforma di accesso.

> [!NOTE]
> Le classi che implementano l'interfaccia devono avere un costruttore senza parametri per funzionare con il `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementazione di iOS

L'interfaccia deve essere implementata in ogni progetto di applicazione specifico della piattaforma. Si noti che la classe ha un costruttore senza parametri in modo che il `DependencyService` possibile creare nuove istanze:

```csharp
using UIKit;
using Foundation;

namespace DependencyServiceSample.iOS
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation(){ }

        public DeviceOrientations GetOrientation()
        {
            var currentOrientation = UIApplication.SharedApplication.StatusBarOrientation;
            bool isPortrait = currentOrientation == UIInterfaceOrientation.Portrait
                || currentOrientation == UIInterfaceOrientation.PortraitUpsideDown;

            return isPortrait ? DeviceOrientations.Portrait: DeviceOrientations.Landscape;
        }
    }
}
```

Infine, aggiungere questo `[assembly]` attributo di sopra della classe e di fuori di qualsiasi spazio dei nomi che è stati definiti, incluse le necessarie `using` istruzioni:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

Questo attributo consente di registrare la classe come un'implementazione del `IDeviceOrientation` interfaccia, il che significa che `DependencyService.Get<IDeviceOrientation>` può essere utilizzato nel codice condiviso per creare un'istanza.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementazione di Android

Il codice seguente implementa `IDeviceOrientation` in Android:

```csharp
using DependencyServiceSample.Droid;
using Android.Hardware;

namespace DependencyServiceSample.Droid
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public static void Init() { }

        public DeviceOrientations GetOrientation()
        {
            IWindowManager windowManager = Android.App.Application.Context.GetSystemService(Context.WindowService).JavaCast<IWindowManager>();

            var rotation = windowManager.DefaultDisplay.Rotation;
            bool isLandscape = rotation == SurfaceOrientation.Rotation90 || rotation == SurfaceOrientation.Rotation270;
            return isLandscape ? DeviceOrientations.Landscape : DeviceOrientations.Portrait;
        }
    }
}
```

Aggiungere questo `[assembly]` attributo di sopra della classe e di fuori di qualsiasi spazio dei nomi che è stati definiti, incluse le necessarie `using` istruzioni:

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

Questo attributo consente di registrare la classe come un'implementazione del `IDeviceOrientaiton` interfaccia, il che significa che `DependencyService.Get<IDeviceOrientation>` può essere utilizzato nel codice condiviso è possibile creare un'istanza.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementazione della piattaforma Windows universale

Il codice seguente implementa il `IDeviceOrientation` interfaccia nella piattaforma Windows universale:

```csharp
namespace DependencyServiceSample.WindowsPhone
{
    public class DeviceOrientationImplementation : IDeviceOrientation
    {
        public DeviceOrientationImplementation() { }

        public DeviceOrientations GetOrientation()
        {
            var orientation = Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().Orientation;
            if (orientation == Windows.UI.ViewManagement.ApplicationViewOrientation.Landscape) {
                return DeviceOrientations.Landscape;
            }
            else {
                return DeviceOrientations.Portrait;
            }
        }
    }
}
```

Aggiungere il `[assembly]` attributo di sopra della classe e di fuori di qualsiasi spazio dei nomi che è stati definiti, incluse le necessarie `using` istruzioni:

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

Questo attributo consente di registrare la classe come un'implementazione del `DeviceOrientationImplementation` interfaccia, il che significa che `DependencyService.Get<IDeviceOrientation>` può essere utilizzato nel codice condiviso è possibile creare un'istanza.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementazione di codice condiviso

Ora è possibile scrivere e testare il codice condiviso che consente di accedere il `IDeviceOrientation` interfaccia. Questa pagina semplice include un pulsante che aggiorna il proprio testo in base all'orientamento del dispositivo. Usa il `DependencyService` per ottenere un'istanza del `IDeviceOrientation` interfaccia &ndash; in fase di esecuzione questa istanza è l'implementazione specifica della piattaforma che dispone dell'accesso completo a SDK nativo:

```csharp
public MainPage ()
{
    var orient = new Button {
        Text = "Get Orientation",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    orient.Clicked += (sender, e) => {
       var orientation = DependencyService.Get<IDeviceOrientation>().GetOrientation();
       switch(orientation){
           case DeviceOrientations.Undefined:
                orient.Text = "Undefined";
                break;
           case DeviceOrientations.Landscape:
                orient.Text = "Landscape";
                break;
           case DeviceOrientations.Portrait:
                orient.Text = "Portrait";
                break;
       }
    };
    Content = orient;
}
```

Questa applicazione in esecuzione iOS, Android o le piattaforme Windows e premere il pulsante comporterà il testo del pulsante aggiornamento con l'orientamento del dispositivo.

![](device-orientation-images/orientation.png "Esempio di orientamento dispositivo")


## <a name="related-links"></a>Collegamenti correlati

- [Utilizzando DependencyService (esempio)](https://developer.xamarin.com/samples/UsingDependencyService)
- [DependencyService (esempio)](https://developer.xamarin.com/samples/DependencyService/DependencyServiceSample/)
- [Esempi di Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
