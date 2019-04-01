---
title: 'Xamarin.Essentials: Informazioni sul dispositivo'
description: Questo documento descrive la classe DeviceInfo in Xamarin.Essentials, che fornisce informazioni sul dispositivo cui viene eseguita l'applicazione.
ms.assetid: A1AC5373-926A-4FB6-8D7D-4B87EB8EB522
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: a7868277cd5e924d55ad688df1e8e07c81c5f074
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329325"
---
# <a name="xamarinessentials-device-information"></a>Xamarin.Essentials: Informazioni sul dispositivo

La classe **DeviceInfo** fornisce informazioni sul dispositivo in cui viene eseguita l'applicazione.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-deviceinfo"></a>Uso di DeviceInfo

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Tramite l'API vengono esposte le informazioni seguenti:

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

## <a name="platforms"></a>Piattaforme

[`DeviceInfo.Platform`](xref:Xamarin.Essentials.DeviceInfo.Platform) correla a una stringa costante mappata al sistema operativo. I valori possono essere controllati con lo struct `DevicePlatform`:

- **DevicePlatform.iOS** - iOS
- **DevicePlatform.Android** - Android
- **DevicePlatform.UWP** - UWP
- **DevicePlatform.Unknown** - Sconosciuto

## <a name="idioms"></a>Idiomi

[`DeviceInfo.Idiom`](xref:Xamarin.Essentials.DeviceInfo.Idiom) correla a una stringa costante mappata al tipo di dispositivo in cui viene eseguita l'applicazione. I valori possono essere controllati con lo struct `DeviceIdiom`:

- **DeviceIdiom.Phone** - Telefono
- **DeviceIdiom.Tablet** - Tablet
- **DeviceIdiom.Desktop** - Desktop
- **DeviceIdiom.TV** - TV
- **DeviceIdiom.Watch** - Orologio
- **DeviceIdiom.Unknown** - Sconosciuto

## <a name="device-type"></a>Tipo di dispositivo

`DeviceInfo.DeviceType` correla un'enumerazione per determinare se l'applicazione è in esecuzione in un dispositivo virtuale o fisico. Un dispositivo virtuale è un simulatore o un emulatore.

## <a name="platform-implementation-specifics"></a>Informazioni di implementazione specifiche della piattaforma

# <a name="iostabios"></a>[iOS](#tab/ios)

iOS non espone un'API per consentire agli sviluppatori di ottenere il nome del dispositivo iOS specifico. Viene invece restituito un identificatore hardware, ad esempio _iPhone10,6_ che fa riferimento all'iPhone X. Apple non fornisce un mapping di questi identificatori, che è tuttavia disponibile in [The iPhone Wiki](https://www.theiphonewiki.com/wiki/Models) (una fonte non ufficiale).

--------------

## <a name="api"></a>API

- [Codice sorgente di DeviceInfo](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceInfo)
- [Documentazione dell'API DeviceInfo](xref:Xamarin.Essentials.DeviceInfo)
