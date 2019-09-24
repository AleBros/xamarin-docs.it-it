---
title: Introduzione a DependencyService di Xamarin.Forms
description: Questo articolo illustra come usare la classe DependencyService di Xamarin.Forms per richiamare la funzionalità della piattaforma nativa.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: b27b4b0c3c5662c6cc1c2c151dd9ebe1523da3a4
ms.sourcegitcommit: 76f930ce63b193ca3f7f85f768b031e59cb342ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198522"
---
# <a name="xamarinforms-dependencyservice-introduction"></a>Introduzione a DependencyService di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)

La classe [`DependencyService`](xref:Xamarin.Forms.DependencyService) è un localizzatore di servizi che consente alle applicazioni Xamarin.Forms di richiamare la funzionalità della piattaforma nativa dal codice condiviso.

La procedura per usare [`DependencyService`](xref:Xamarin.Forms.DependencyService) per richiamare la funzionalità della piattaforma nativa prevede i passaggi seguenti:

1. Creare un'interfaccia per la funzionalità della piattaforma nativa nel codice condiviso. Per altre informazioni, vedere [Creare un'interfaccia](#create-an-interface).
1. Implementare l'interfaccia nei progetti di piattaforma richiesti. Per altre informazioni, vedere [Implementare l'interfaccia in ogni piattaforma](#implement-the-interface-on-each-platform).
1. Registrare le implementazioni della piattaforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService). Ciò consente a Xamarin.Forms di individuare le implementazioni della piattaforma in fase di esecuzione. Per altre informazioni, vedere [Registrare le implementazioni della piattaforma](#register-the-platform-implementations).
1. Risolvere le implementazioni della piattaforma dal codice condiviso e richiamarle. Per altre informazioni, vedere [Risolvere le implementazioni della piattaforma](#resolve-the-platform-implementations).

Il diagramma seguente illustra come viene richiamata la funzionalità della piattaforma nativa in un'applicazione Xamarin.Forms:

![Panoramica della posizione del servizio con la classe DependencyService di Xamarin.Forms](introduction-images/dependency-service.png "Posizione del servizio DependencyService")

## <a name="create-an-interface"></a>Creare un'interfaccia

Il primo passaggio per riuscire a richiamare la funzionalità della piattaforma nativa dal codice condiviso consiste nel creare un'interfaccia che definisce l'API per l'interazione con la funzionalità della piattaforma nativa. Questa interfaccia deve essere inserita nel progetto del codice condiviso.

L'esempio seguente mostra un'interfaccia per un'API che può essere usata per recuperare l'orientamento di un dispositivo:

```csharp
public interface IDeviceOrientationService
{
    DeviceOrientation GetOrientation();
}
```

## <a name="implement-the-interface-on-each-platform"></a>Implementare l'interfaccia in ogni piattaforma

Dopo aver creato l'interfaccia che definisce l'API per l'interazione con la funzionalità della piattaforma nativa, l'interfaccia deve essere implementata nel progetto di ogni piattaforma.

### <a name="ios"></a>iOS

L'esempio di codice seguente mostra l'implementazione dell'interfaccia `IDeviceOrientationService` in iOS:

```csharp
namespace DependencyServiceDemos.iOS
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            UIInterfaceOrientation orientation = UIApplication.SharedApplication.StatusBarOrientation;

            bool isPortrait = orientation == UIInterfaceOrientation.Portrait ||
                orientation == UIInterfaceOrientation.PortraitUpsideDown;
            return isPortrait ? DeviceOrientation.Portrait : DeviceOrientation.Landscape;
        }
    }
}
```

### <a name="android"></a>Android

L'esempio di codice seguente mostra l'implementazione dell'interfaccia `IDeviceOrientationService` in Android:

```csharp
namespace DependencyServiceDemos.Droid
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            IWindowManager windowManager = Android.App.Application.Context.GetSystemService(Context.WindowService).JavaCast<IWindowManager>();

            SurfaceOrientation orientation = windowManager.DefaultDisplay.Rotation;
            bool isLandscape = orientation == SurfaceOrientation.Rotation90 ||
                orientation == SurfaceOrientation.Rotation270;
            return isLandscape ? DeviceOrientation.Landscape : DeviceOrientation.Portrait;
        }
    }
}
```

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

L'esempio di codice seguente mostra l'implementazione dell'interfaccia `IDeviceOrientationService` per la piattaforma UWP (Universal Windows Platform):

```csharp
namespace DependencyServiceDemos.UWP
{
    public class DeviceOrientationService : IDeviceOrientationService
    {
        public DeviceOrientation GetOrientation()
        {
            ApplicationViewOrientation orientation = ApplicationView.GetForCurrentView().Orientation;
            return orientation == ApplicationViewOrientation.Landscape ? DeviceOrientation.Landscape : DeviceOrientation.Portrait;
        }
    }
}
```

## <a name="register-the-platform-implementations"></a>Registrare le implementazioni della piattaforma

Dopo avere implementato l'interfaccia nel progetto di ogni piattaforma, le implementazioni della piattaforma devono essere registrate con [`DependencyService`](xref:Xamarin.Forms.DependencyService), in modo che Xamarin.Forms possa individuarle in fase di esecuzione. Questa operazione viene in genere eseguita con [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute), che indica che il tipo specificato fornisce un'implementazione dell'interfaccia.

L'esempio seguente illustra l'uso di [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) per registrare l'implementazione di iOS dell'interfaccia `IDeviceOrientationService`:

```csharp
using Xamarin.Forms;

[assembly: Dependency(typeof(DependencyServiceDemos.iOS.DeviceOrientationService))]
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

In questo esempio, [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute) registra `DeviceOrientationService` con [`DependencyService`](xref:Xamarin.Forms.DependencyService). Analogamente, le implementazioni dell'interfaccia `IDeviceOrientationService` in altre piattaforme devono essere registrate con [`DependencyAttribute`](xref:Xamarin.Forms.DependencyAttribute).

Per altre informazioni sulla registrazione delle implementazioni della piattaforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService), vedere [Registrazione e risoluzione di DependencyService di Xamarin.Forms](registration-and-resolution.md).

## <a name="resolve-the-platform-implementations"></a>Risolvere le implementazioni della piattaforma

Dopo la registrazione delle implementazioni della piattaforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService), le implementazioni devono essere risolte prima di poter essere richiamate. Questa operazione viene in genere eseguita nel codice condiviso tramite il metodo [`DependencyService.Get<T>`](xref:Xamarin.Forms.DependencyService.Get*).

Il codice seguente mostra un esempio della chiamata del metodo [`Get<T>`](xref:Xamarin.Forms.DependencyService.Get*) per risolvere l'interfaccia `IDeviceOrientationService` e della chiamata del relativo metodo `GetOrientation`:

```csharp
IDeviceOrientationService service = DependencyService.Get<IDeviceOrientationService>();
DeviceOrientation orientation = service.GetOrientation();
```

Questo codice può essere in alternativa compresso in una singola riga:

```csharp
DeviceOrientation orientation = DependencyService.Get<IDeviceOrientationService>().GetOrientation();
```

Per altre informazioni sulla risoluzione delle implementazioni della piattaforma con [`DependencyService`](xref:Xamarin.Forms.DependencyService), vedere [Registrazione e risoluzione di DependencyService di Xamarin.Forms](registration-and-resolution.md).

## <a name="related-links"></a>Collegamenti correlati

- [Demo di DependencyService (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Registrazione e risoluzione di DependencyService di Xamarin.Forms](registration-and-resolution.md)
