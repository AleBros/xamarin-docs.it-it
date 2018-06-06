---
title: 'Xamarin.Essentials: Le informazioni sul dispositivo'
description: Questo documento descrive la classe DeviceInfo in Xamarin.Essentials, che fornisce informazioni sul dispositivo è in esecuzione l'applicazione.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: b7246afca19607ef2f70288d4643696f4ac35d52
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782398"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials: Le informazioni sul dispositivo

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **DeviceInfo** classe fornisce informazioni sul dispositivo è in esecuzione l'applicazione.

## <a name="using-deviceinfo"></a>Utilizzo di DeviceInfo

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Le seguenti informazioni vengono esposte tramite l'API:

```csharp
// Device Model (SMG-950U)
var device = DeviceInfo.Model;

// Manufacturer (Samsung)
var manufacturer = DeviceInfo.Manufacturer;

// Device Name (Motz's iPhone)
var deviceName = DeviceInfo.Name;

// Operating System Version Number (7.0)
var version = DeviceInfo.VersionString;

// Platform (Android)
var platform = DeviceInfo.Platform;

// Idiom (Phone)
var idiom = DeviceInfo.Idiom;

// Device Type (Physical)
var deviceType = DeviceInfo.DeviceType;
```

## <a name="platformsxrefxamarinessentialsdeviceinfoplatforms"></a>[Piattaforme](xref:Xamarin.Essentials.DeviceInfo.Platforms)

`DeviceInfo.Platform` mette in correlazione con una stringa costante che esegue il mapping al sistema operativo. È possono verificare i valori con il `Platforms` classe:

- **DeviceInfo.Platforms.iOS** – iOS
- **DeviceInfo.Platforms.Android** – Android
- **DeviceInfo.Platforms.UWP** – UWP
- **DeviceInfo.Platforms.Unsupported** : non supportato

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[Idiomi](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` è correlata una stringa costante che esegue il mapping al tipo di dispositivo che l'applicazione è in esecuzione. È possono verificare i valori con il `Idioms` classe:

- **DeviceInfo.Idioms.Phone** -telefono
- **DeviceInfo.Idioms.Tablet** – Tablet
- **DeviceInfo.Idioms.Desktop** – Desktop
- **DeviceInfo.Idioms.TV** – TV
- **DeviceInfo.Idioms.Unsupported** : non supportato

## <a name="device-type"></a>Tipo di dispositivo

`DeviceInfo.DeviceType` correla un'enumerazione per determinare se l'applicazione è in esecuzione su fisico o un dispositivo virtuale. Un dispositivo virtuale è un emulatore o il simulatore.

## <a name="api"></a>API

- [Codice sorgente DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Documentazione dell'API di DeviceInfo](xref:Xamarin.Essentials.DeviceInfo)
