---
title: 'Xamarin.Essentials: batteria'
description: Questo documento descrive la classe di batteria in Xamarin.Essentials, che consente di controllare le informazioni sulla batteria del dispositivo e monitorare eventuali modifiche.
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 1deafed85e9400bf7d4592fc06f71c22cc0015f0
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353454"
---
# <a name="xamarinessentials-battery"></a>Xamarin.Essentials: batteria

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **batteria** classe consente di controllare le informazioni sulla batteria del dispositivo e monitorare eventuali modifiche.

## <a name="getting-started"></a>Introduzione

Per l'accesso di **batteria** è necessaria la funzionalità le seguenti impostazioni specifiche della piattaforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Il `Battery` l'autorizzazione è obbligatorio e deve essere configurata nel progetto Android. Ciò è possibile aggiungere nei modi seguenti:

Aprire il **AssemblyInfo.cs** file sotto il **proprietà** cartella e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.BatteryStats)]
```

In alternativa, aggiornare il manifesto di Android:

Aprire il **androidmanifest. XML** file sotto il **proprietà** cartella e aggiungere il codice seguente all'interno del **manifesto** nodo.

```xml
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

Oppure fare clic con il pulsante destro sul progetto Android e aprire le proprietà del progetto. Sotto **manifesto Android** trovare il **autorizzazioni necessarie:** area e selezionare il **batteria** autorizzazione. Si aggiornerà automaticamente il **androidmanifest. XML** file.

# <a name="iostabios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

Non è necessaria alcuna configurazione aggiuntiva.

-----

## <a name="using-battery"></a>Usa la batteria

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

Controllare le informazioni sulla batteria corrente:

```csharp
var level = Battery.ChargeLevel; // returns 0.0 to 1.0 or -1.0 if unable to determine.

var state = Battery.State;

switch (state)
{
    case BatteryState.Charging:
        // Currently charging
        break;
    case BatteryState.Full:
        // Battery is full
        break;
    case BatteryState.Discharging:
    case BatteryState.NotCharging:
        // Currently discharging battery or not being charged
        break;
    case BatteryState.NotPresent:
        // Battery doesn't exist in device (desktop computer)
    case BatteryState.Unknown:
        // Unable to detect battery state
        break;
}

var source = Battery.PowerSource;

switch (source)
{
    case BatteryPowerSource.Battery:
        // Being powered by the battery
        break;
    case BatteryPowerSource.AC:
        // Being powered by A/C unit
        break;
    case BatteryPowerSource.Usb:
        // Being powered by USB cable
        break;
    case BatteryPowerSource.Wireless:
        // Powered via wireless charging
        break;
    case BatteryPowerSource.Unknown:
        // Unable to detect power source
        break;
}
```

Ogni volta che una delle proprietà della batteria cambia viene attivato un evento:

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryChanged += Battery_BatteryChanged;
    }

    void Battery_BatteryChanged(BatteryChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

Nessuna differenza di piattaforma.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Dispositivo deve essere usato per testare le API. 
* Solo restituirà `AC` oppure `Battery` per `PowerSource`.
* Non è possibile annullare vibrazione.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

* Solo restituirà `AC` oppure `Battery` per `PowerSource`.

-----

## <a name="api"></a>API

- [Codice sorgente della batteria](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Documentazione dell'API della batteria](xref:Xamarin.Essentials.Battery)
