---
title: Verifica l'orientamento del dispositivo
description: Questo articolo illustra come usare la classe di xamarin. Forms DependencyService per accedere a orientamento del dispositivo da codice condiviso.
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 620404a217b2e8a31192ae6613dcec023ac366cd
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995641"
---
# <a name="checking-device-orientation"></a>Verifica l'orientamento del dispositivo

Questo articolo illustrerà come utilizzare [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) per controllare l'orientamento del dispositivo da codice condiviso, utilizzando le API native in ciascuna piattaforma. Questa procedura dettagliata è basata su esistente `DeviceOrientation` plugin in base Ali Özgür. Vedere le [repository GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) per altre informazioni.

- **[Creazione dell'interfaccia](#Creating_the_Interface)**  &ndash; comprendere come l'interfaccia viene creata nel codice condiviso.
- **[Implementazione di iOS](#iOS_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per iOS.
- **[Implementazione di Android](#Android_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per Android.
- **[Implementazione di UWP](#WindowsImplementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per la Universal Windows Platform (UWP).
- **[Implementazione nel codice condiviso](#Implementing_in_Shared_Code)**  &ndash; informazioni su come usare `DependencyService` chiamino l'implementazione native da codice condiviso.

L'applicazione usando `DependencyService` avrà la struttura seguente:

![](device-orientation-images/orientation-diagram.png "Struttura dell'applicazione DependencyService")

> [!NOTE]
> È possibile rilevare se il dispositivo è in orientamento verticale o orizzontale nel codice condiviso, come illustrato in [dispositivo Orientation]/guides/xamarin-forms/user-interface/layouts/device-orientation/#changes-in-orientation). Il metodo descritto in questo articolo Usa funzionalità native per ottenere ulteriori informazioni sull'orientamento, ad esempio se il dispositivo è capovolto.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creazione dell'interfaccia

Innanzitutto, creare un'interfaccia nel codice condiviso che esprime la funzionalità che si prevede di implementare. Per questo esempio, l'interfaccia contiene un solo metodo:

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

Scrivere il codice per questa interfaccia nel codice condiviso consentirà all'app xamarin. Forms di accedere l'orientamento del dispositivo le API in ciascuna piattaforma.

> [!NOTE]
> Classi che implementano l'interfaccia devono avere un costruttore senza parametri per lavorare con i `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementazione di iOS

L'interfaccia deve essere implementata in ogni progetto specifico della piattaforma dell'applicazione. Si noti che la classe ha un costruttore senza parametri in modo che il `DependencyService` può creare nuove istanze:

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

Infine, aggiungere questo `[assembly]` dell'attributo di sopra della classe e all'esterno di qualsiasi spazio dei nomi che è stati definiti, tra cui le operazioni `using` istruzioni:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

Questo attributo registra la classe come implementazione del `IDeviceOrientation` interfaccia, il che significa che `DependencyService.Get<IDeviceOrientation>` utilizzabile nel codice condiviso per creare un'istanza.

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

Aggiungere quanto segue `[assembly]` dell'attributo di sopra della classe e all'esterno di qualsiasi spazio dei nomi che è stati definiti, tra cui le operazioni `using` istruzioni:

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

Questo attributo registra la classe come implementazione del `IDeviceOrientaiton` interfaccia, il che significa che `DependencyService.Get<IDeviceOrientation>` può essere usato nel codice condiviso è possibile creare un'istanza.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementazione della piattaforma Windows universale

Il codice seguente implementa il `IDeviceOrientation` interfaccia sulla piattaforma Windows universale:

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

Aggiungere il `[assembly]` dell'attributo di sopra della classe e all'esterno di qualsiasi spazio dei nomi che è stati definiti, tra cui le operazioni `using` istruzioni:

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

Questo attributo registra la classe come implementazione del `DeviceOrientationImplementation` interfaccia, il che significa che `DependencyService.Get<IDeviceOrientation>` può essere usato nel codice condiviso è possibile creare un'istanza.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementazione nel codice condiviso

A questo punto possiamo scrivere e testare il codice condiviso che consente di accedere il `IDeviceOrientation` interfaccia. Questa pagina semplice include un pulsante che aggiorna il proprio testo in base l'orientamento del dispositivo. Usa il `DependencyService` per ottenere un'istanza del `IDeviceOrientation` interfaccia &ndash; in fase di esecuzione questa istanza sarà l'implementazione specifica della piattaforma che dispone dell'accesso completo a SDK nativi:

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

Esecuzione di questa applicazione su iOS, Android o le piattaforme Windows e premendo il pulsante genererà il testo del pulsante aggiornamento con l'orientamento del dispositivo.

![](device-orientation-images/orientation.png "Esempio di orientamento dei dispositivi")


## <a name="related-links"></a>Collegamenti correlati

- [Con DependencyService (esempio)](https://developer.xamarin.com/samples/UsingDependencyService)
- [DependencyService (esempio)](https://developer.xamarin.com/samples/DependencyService/DependencyServiceSample/)
- [Esempi di Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
