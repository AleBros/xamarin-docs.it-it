---
title: Controllo dell'orientamento del dispositivo
description: Questo articolo illustra come usare la classe DependencyService di Xamarin.Forms per accedere all'orientamento del dispositivo da codice condiviso.
ms.prod: xamarin
ms.assetid: 5F60975F-47DB-4361-B97C-2290D6F77D2F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: a63d0d4c90bff36e55d98b731217133fe5958c1f
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051471"
---
# <a name="checking-device-orientation"></a>Controllo dell'orientamento del dispositivo

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/UsingDependencyService)

Questo articolo illustrerà come usare [`DependencyService`](xref:Xamarin.Forms.DependencyService) per controllare l'orientamento del dispositivo da codice condiviso usando le API native in ciascuna piattaforma. Questa procedura dettagliata è basata su sul plug-in `DeviceOrientation` esistente di Ali Özgür. Vedere il [repository GitHub](https://github.com/aliozgur/Xamarin.Plugins/tree/master/DeviceOrientation) per altre informazioni.

- **[Creazione dell'interfaccia](#Creating_the_Interface)** &ndash; Informazioni sulla creazione dell'interfaccia nel codice condiviso.
- **[Implementazione in iOS](#iOS_Implementation)** &ndash; Informazioni su come implementare l'interfaccia nel codice nativo per iOS.
- **[Implementazione in Android](#Android_Implementation)**: informazioni su come implementare l'interfaccia nel codice nativo per Android.
- **[Implementazione nella piattaforma UWP](#WindowsImplementation)**: informazioni su come implementare l'interfaccia nel codice nativo per la piattaforma UWP (Universal Windows Platform).
- **[Implementazione nel codice condiviso](#Implementing_in_Shared_Code)**: informazioni su come usare `DependencyService` per eseguire chiamate nell'implementazione nativa dal codice condiviso.

L'applicazione che usa `DependencyService` avrà la struttura seguente:

![](device-orientation-images/orientation-diagram.png "Struttura dell'applicazione DependencyService")

> [!NOTE]
> È possibile rilevare se l'orientamento del dispositivo è verticale o orizzontale nel codice condiviso, come illustrato in [Orientamento del dispositivo](~/xamarin-forms/user-interface/layouts/device-orientation.md#Reacting_to_Changes_in_Orientation). Il metodo descritto in questo articolo usa funzionalità native per ottenere ulteriori informazioni sull'orientamento, incluso se il dispositivo è capovolto.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creazione dell'interfaccia

Creare prima di tutto un'interfaccia nel codice condiviso che esprime la funzionalità che si intende implementare. Per questo esempio, l'interfaccia contiene un solo metodo:

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

La scrittura del codice per questa interfaccia nel codice condiviso consentirà all'app Xamarin.Forms di accedere alle API di orientamento del dispositivo in ogni piattaforma.

> [!NOTE]
> Le classi che implementano l'interfaccia devono avere un costruttore senza parametri per usare `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementazione iOS

L'interfaccia deve essere implementata in ogni progetto di applicazione specifico della piattaforma. Si noti che la classe ha un costruttore senza parametri in modo che `DependencyService` possa creare nuove istanze:

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

Infine, aggiungere questo attributo `[assembly]` sopra la classe (e all'esterno di qualsiasi spazio dei nomi definito), incluse eventuali istruzioni `using` necessarie:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.iOS {
    ...
```

Questo attributo registra la classe come implementazione dell'interfaccia `IDeviceOrientation` e ciò significa che è possibile usare `DependencyService.Get<IDeviceOrientation>` nel codice condiviso per crearne un'istanza.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementazione Android

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

Aggiungere questo attributo `[assembly]` sopra la classe (e all'esterno di qualsiasi spazio dei nomi definito), incluse eventuali istruzioni `using` necessarie:

```csharp
using DependencyServiceSample.Droid; //enables registration outside of namespace
using Android.Hardware;

[assembly: Xamarin.Forms.Dependency (typeof (DeviceOrientationImplementation))]
namespace DependencyServiceSample.Droid {
    ...
```

Questo attributo registra la classe come implementazione dell'interfaccia `IDeviceOrientaiton` e ciò significa che è possibile usare `DependencyService.Get<IDeviceOrientation>` nel codice condiviso per crearne un'istanza.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementazione UWP (Universal Windows Platform)

Il codice seguente implementa l'interfaccia `IDeviceOrientation` nella piattaforma UWP (Universal Windows Platform):

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

Aggiungere l'attributo `[assembly]` sopra la classe (e all'esterno di qualsiasi spazio dei nomi definito), incluse eventuali istruzioni `using` necessarie:

```csharp
using DependencyServiceSample.WindowsPhone; //enables registration outside of namespace

[assembly: Dependency(typeof(DeviceOrientationImplementation))]
namespace DependencyServiceSample.WindowsPhone {
    ...
```

Questo attributo registra la classe come implementazione dell'interfaccia `DeviceOrientationImplementation` e ciò significa che è possibile usare `DependencyService.Get<IDeviceOrientation>` nel codice condiviso per crearne un'istanza.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementazione nel codice condiviso

A questo punto è possibile scrivere e testare il codice condiviso che consente di accedere all'interfaccia `IDeviceOrientation`. Questa pagina semplice include un pulsante che aggiorna il proprio testo in base all'orientamento del dispositivo. Usa `DependencyService` per ottenere un'istanza dell'interfaccia `IDeviceOrientation`. In fase di esecuzione questa istanza sarà l'implementazione specifica della piattaforma con accesso completo all'SDK nativo:

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

Se si esegue questa applicazione sulla piattaforma iOS, Android o Windows e si preme il pulsante, il testo del pulsante verrà aggiornato con l'orientamento del dispositivo.

![](device-orientation-images/orientation.png "Esempio di orientamento del dispositivo")


## <a name="related-links"></a>Collegamenti correlati

- [Using DependencyService (sample)](https://developer.xamarin.com/samples/UsingDependencyService) (Esempio d'uso di DependencyService)
- [DependencyService (sample)](https://developer.xamarin.com/samples/DependencyService/DependencyServiceSample/) (Esempio di DependencyService)
- [Esempi di Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
