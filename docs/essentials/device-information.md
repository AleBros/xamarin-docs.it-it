---
title: 'Xamarin.Essentials: Le informazioni sul dispositivo'
description: Questo documento descrive la classe DeviceInfo in Xamarin.Essentials, che fornisce informazioni sul dispositivo cui viene eseguita l'applicazione.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 18fe081372cc190e5ead2045f36d63652f8702c3
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353802"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials: Le informazioni sul dispositivo

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **DeviceInfo** classe fornisce informazioni sul dispositivo cui viene eseguita l'applicazione.

## <a name="using-deviceinfo"></a>Uso di DeviceInfo

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Le informazioni seguenti viene esposta tramite l'API:

```csharp
// Device Model (SMG-950U, iPhone10,6)
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

`DeviceInfo.Platform` è correlata a una stringa costante che viene eseguito il mapping al sistema operativo. È possibile verificare i valori con il `Platforms` classe:

- **DeviceInfo.Platforms.iOS** -iOS
- **DeviceInfo.Platforms.Android** : Android
- **DeviceInfo.Platforms.UWP** – UWP
- **DeviceInfo.Platforms.Unsupported** : non supportato

## <a name="idiomsxrefxamarinessentialsdeviceinfoidioms"></a>[Idiomi](xref:Xamarin.Essentials.DeviceInfo.Idioms)

`DeviceInfo.Idiom` è correlata una stringa costante che esegue il mapping al tipo di dispositivo, l'applicazione è in corso. È possibile verificare i valori con il `Idioms` classe:

- **DeviceInfo.Idioms.Phone** – telefono
- **DeviceInfo.Idioms.Tablet** – Tablet
- **DeviceInfo.Idioms.Desktop** -Desktop
- **DeviceInfo.Idioms.TV** – TV
- **DeviceInfo.Idioms.Unsupported** : non supportato

## <a name="device-type"></a>Tipo di dispositivo

`DeviceInfo.DeviceType` mette in correlazione un'enumerazione per determinare se l'applicazione è in esecuzione in un dispositivo fisico o virtuale. Un dispositivo virtuale è un simulatore o un emulatore.

## <a name="platform-implementation-specifics"></a>Funzionalità specifiche di implementazione della piattaforma

# <a name="iostabios"></a>[iOS](#tab/ios)

iOS non espone un'API per sviluppatori ottenere il nome di un dispositivo iOS specifico. Un identificatore hardware viene restituito, ad esempio _iPhone10, 6_ che fa riferimento a iPhone X. Un mapping di questi identificatori non vengono fornite da Apple, ma sono disponibili nella [iPhone Wiki](https://www.theiphonewiki.com/wiki/Models) (un'origine di origine non ufficiale).

--------------

## <a name="api"></a>API

- [Codice sorgente DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Documentazione dell'API di DeviceInfo](xref:Xamarin.Essentials.DeviceInfo)
