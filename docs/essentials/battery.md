---
title: Batteria Xamarin.Essentials
description: La classe batteria consente di controllare le informazioni sulla batteria del dispositivo e monitorare le modifiche.
ms.assetid: 47EB26D8-8C62-477B-A13C-6977F74E6E43
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 171f65f0ab26faaacddbd8fef37056d7f892d973
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2018
---
# <a name="xamarinessentials-battery"></a>Batteria Xamarin.Essentials

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **batteria** classe consente di controllare le informazioni sulla batteria del dispositivo e monitorare le modifiche.

## <a name="getting-started"></a>Introduzione

Per l'accesso di **batteria** è necessaria la funzionalità le seguenti impostazioni specifiche della piattaforma.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Il `Battery` l'autorizzazione è obbligatorio e deve essere configurata nel progetto Android. Questo può essere aggiunto nei modi seguenti:

Aprire la **AssemblyInfo.cs** file sotto il **proprietà** cartella e aggiungere:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Battery)]
```

O aggiornare il manifesto Android:

Aprire la **androidmanifest. XML** file sotto il **proprietà** cartella e aggiungere il codice seguente all'interno del **manifesto** nodo.

```xml
<uses-permission android:name="android.permission.BATTERY" />
```

Oppure fare clic con il pulsante destro sul progetto di piattaforma Android e aprire le proprietà del progetto. Sotto **manifesto Android** trovare il **delle autorizzazioni necessarie:** area e controllare il **batteria** l'autorizzazione. Verrà automaticamente aggiornato il **Androidmanifest** file.

# <a name="iostabios"></a>[iOS](#tab/ios)

Non è necessaria alcuna configurazione aggiuntiva.

# <a name="uwptabuwp"></a>[PIATTAFORMA UWP](#tab/uwp)

Non è necessaria alcuna configurazione aggiuntiva.

-----

## <a name="using-battery"></a>Utilizzo della batteria

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
    case BatteryPowerSource.Ac:
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

Ogni volta che una delle proprietà della batteria modifica viene generato un evento:

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

## <a name="platform-differences"></a>Differenze di piattaforma

| Piattaforma | Differenza |
| --- | --- |
| iOS | Dispositivo deve essere utilizzato per le API di test. |
| iOS | Solo restituiranno Ac o della batteria per PowerSource. |
| UWP | Solo restituiranno Ac o della batteria per PowerSource. |

## <a name="api"></a>API

- [Codice sorgente della batteria](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Documentazione dell'API di batteria](xref:Xamarin.Essentials.Battery)
