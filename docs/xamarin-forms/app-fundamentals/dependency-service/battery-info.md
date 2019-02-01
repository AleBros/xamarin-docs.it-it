---
title: Controllo dello stato della batteria
description: Questo articolo illustra come usare la classe DependencyService di Xamarin.Forms per accedere alle informazioni sulla batteria in modo nativo per ogni piattaforma.
ms.prod: xamarin
ms.assetid: CF1C5A73-84ED-407D-BDC5-EB1D83D2D3DB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 08278c2bc380892706320dbd0e69642257b73005
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233783"
---
# <a name="checking-battery-status"></a>Controllo dello stato della batteria

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/DependencyService)

Questo articolo illustra la creazione di un'applicazione che controlla lo stato della batteria. Questo articolo si basa sul plug-in Battery di James Montemagno. Per altre informazioni, vedere il [repository GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery).

Dato che Xamarin.Forms non include la funzionalità per controllare lo stato della batteria corrente, questa applicazione dovrà usare [`DependencyService`](xref:Xamarin.Forms.DependencyService) per sfruttare le API native.  Questo articolo illustra i passaggi seguenti per l'uso di `DependencyService`:

- **[Creazione dell'interfaccia](#Creating_the_Interface)** &ndash; Informazioni sulla creazione dell'interfaccia nel codice condiviso.
- **[Implementazione in iOS](#iOS_Implementation)**: informazioni su come implementare l'interfaccia nel codice nativo per iOS.
- **[Implementazione in Android](#Android_Implementation)** &ndash; Informazioni su come implementare l'interfaccia nel codice nativo per Android.
- **[Implementazione nella piattaforma UWP (Universal Windows Platform)](#UWPImplementation)** &ndash; Informazioni su come implementare l'interfaccia nel codice nativo per la piattaforma UWP (Universal Windows Platform).
- **[Implementazione nel codice condiviso](#Implementing_in_Shared_Code)** &ndash; Informazioni su come usare `DependencyService` per una chiamata all'implementazione nativa dal codice condiviso.

Al termine, l'applicazione che usa `DependencyService` avrà la struttura seguente:

![](battery-info-images/battery-diagram.png "Struttura dell'applicazione DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creazione dell'interfaccia

In primo luogo creare un'interfaccia nel codice condiviso che esprime la funzionalità desiderata. Nel caso di un'applicazione che controlla la batteria, le informazioni rilevanti sono la percentuale di batteria rimanente, se il dispositivo è in carica o meno e come viene alimentato il dispositivo:

```csharp
namespace DependencyServiceSample
{
  public enum BatteryStatus
  {
    Charging,
    Discharging,
    Full,
    NotCharging,
    Unknown
  }

  public enum PowerSource
  {
    Battery,
    Ac,
    Usb,
    Wireless,
    Other
  }

  public interface IBattery
  {
    int RemainingChargePercent { get; }
    BatteryStatus Status { get; }
    PowerSource PowerSource { get; }
  }
}
```

La scrittura di codice per questa interfaccia nel codice condiviso consentirà all'app Xamarin.Forms di accedere alle API di gestione dell'alimentazione in ogni piattaforma.

> [!NOTE]
> Le classi che implementano l'interfaccia devono avere un costruttore senza parametri per usare `DependencyService`. I costruttori non possono essere definiti dalle interfacce.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementazione iOS

L'interfaccia `IBattery` deve essere implementata in ogni progetto dell'applicazione specifico della piattaforma. L'implementazione di iOS userà le API [`UIDevice`](xref:UIKit.UIDevice) native per accedere alle informazioni sulla batteria. Si noti che la classe seguente ha un costruttore senza parametri in modo che `DependencyService` possa creare nuove istanze:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS;

namespace DependencyServiceSample.iOS
{
  public class BatteryImplementation : IBattery
  {
    public BatteryImplementation()
    {
      UIDevice.CurrentDevice.BatteryMonitoringEnabled = true;
    }

    public int RemainingChargePercent
    {
      get
      {
        return (int)(UIDevice.CurrentDevice.BatteryLevel * 100F);
      }
    }

    public BatteryStatus Status
    {
      get
      {
        switch (UIDevice.CurrentDevice.BatteryState)
        {
          case UIDeviceBatteryState.Charging:
            return BatteryStatus.Charging;
          case UIDeviceBatteryState.Full:
            return BatteryStatus.Full;
          case UIDeviceBatteryState.Unplugged:
            return BatteryStatus.Discharging;
          default:
            return BatteryStatus.Unknown;
        }
      }
    }

    public PowerSource PowerSource
    {
      get
      {
        switch (UIDevice.CurrentDevice.BatteryState)
        {
          case UIDeviceBatteryState.Charging:
            return PowerSource.Ac;
          case UIDeviceBatteryState.Full:
            return PowerSource.Ac;
          case UIDeviceBatteryState.Unplugged:
            return PowerSource.Battery;
          default:
            return PowerSource.Other;
        }
      }
    }
  }
}
```

Infine, aggiungere questo attributo `[assembly]` sopra la classe (e all'esterno di qualsiasi spazio dei nomi definito), incluse eventuali istruzioni `using` necessarie:

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS;//necessary for registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (BatteryImplementation))]
namespace DependencyServiceSample.iOS
{
  public class BatteryImplementation : IBattery {
    ...
```

Questo attributo registra la classe come implementazione dell'interfaccia `IBattery` e ciò significa che è possibile usare `DependencyService.Get<IBattery>` nel codice condiviso per crearne un'istanza:

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementazione Android

L'implementazione Android usa l'API [`Android.OS.BatteryManager`](https://developer.xamarin.com/api/type/Android.OS.BatteryManager/). Questa implementazione è più complessa rispetto alla versione iOS e richiede controlli per gestire la mancanza di autorizzazioni della batteria:

```csharp
using System;
using Android;
using Android.Content;
using Android.App;
using Android.OS;
using BatteryStatus = Android.OS.BatteryStatus;
using DependencyServiceSample.Droid;

namespace DependencyServiceSample.Droid
{
  public class BatteryImplementation : IBattery
  {
    private BatteryBroadcastReceiver batteryReceiver;
    public BatteryImplementation() { }

    public int RemainingChargePercent
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              var level = battery.GetIntExtra(BatteryManager.ExtraLevel, -1);
              var scale = battery.GetIntExtra(BatteryManager.ExtraScale, -1);

              return (int)Math.Floor(level * 100D / scale);
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }

      }
    }

    public DependencyServiceSample.BatteryStatus Status
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              int status = battery.GetIntExtra(BatteryManager.ExtraStatus, -1);
              var isCharging = status == (int)BatteryStatus.Charging || status == (int)BatteryStatus.Full;

              var chargePlug = battery.GetIntExtra(BatteryManager.ExtraPlugged, -1);
              var usbCharge = chargePlug == (int)BatteryPlugged.Usb;
              var acCharge = chargePlug == (int)BatteryPlugged.Ac;
              bool wirelessCharge = false;
              wirelessCharge = chargePlug == (int)BatteryPlugged.Wireless;

              isCharging = (usbCharge || acCharge || wirelessCharge);
              if (isCharging)
                return DependencyServiceSample.BatteryStatus.Charging;

              switch(status)
              {
                case (int)BatteryStatus.Charging:
                  return DependencyServiceSample.BatteryStatus.Charging;
                case (int)BatteryStatus.Discharging:
                  return DependencyServiceSample.BatteryStatus.Discharging;
                case (int)BatteryStatus.Full:
                  return DependencyServiceSample.BatteryStatus.Full;
                case (int)BatteryStatus.NotCharging:
                  return DependencyServiceSample.BatteryStatus.NotCharging;
                default:
                  return DependencyServiceSample.BatteryStatus.Unknown;
              }
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }
      }
    }

    public PowerSource PowerSource
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              int status = battery.GetIntExtra(BatteryManager.ExtraStatus, -1);
              var isCharging = status == (int)BatteryStatus.Charging || status == (int)BatteryStatus.Full;

              var chargePlug = battery.GetIntExtra(BatteryManager.ExtraPlugged, -1);
              var usbCharge = chargePlug == (int)BatteryPlugged.Usb;
              var acCharge = chargePlug == (int)BatteryPlugged.Ac;

              bool wirelessCharge = false;
              wirelessCharge = chargePlug == (int)BatteryPlugged.Wireless;

              isCharging = (usbCharge || acCharge || wirelessCharge);

              if (!isCharging)
                return DependencyServiceSample.PowerSource.Battery;
              else if (usbCharge)
                return DependencyServiceSample.PowerSource.Usb;
              else if (acCharge)
                return DependencyServiceSample.PowerSource.Ac;
              else if (wirelessCharge)
                return DependencyServiceSample.PowerSource.Wireless;
              else
                return DependencyServiceSample.PowerSource.Other;
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }
      }
    }
  }
}
```

Aggiungere questo attributo `[assembly]` sopra la classe (e all'esterno di qualsiasi spazio dei nomi definito), incluse eventuali istruzioni `using` necessarie:

```csharp
...
using BatteryStatus = Android.OS.BatteryStatus;
using DependencyServiceSample.Droid; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (BatteryImplementation))]
namespace DependencyServiceSample.Droid
{
  public class BatteryImplementation : IBattery {
    ...
```

Questo attributo registra la classe come implementazione dell'interfaccia `IBattery` e ciò significa che è possibile usare `DependencyService.Get<IBattery>` nel codice condiviso per crearne un'istanza.

<a name="UWPImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementazione UWP (Universal Windows Platform)

L'implementazione UWP usa l'API `Windows.Devices.Power` per ottenere informazioni sullo stato della batteria:

```csharp
using DependencyServiceSample.UWP;
using Xamarin.Forms;

[assembly: Dependency(typeof(BatteryImplementation))]
namespace DependencyServiceSample.UWP
{
    public class BatteryImplementation : IBattery
    {
        private BatteryStatus status = BatteryStatus.Unknown;
        Windows.Devices.Power.Battery battery;

        public BatteryImplementation()
        {
        }

        private Windows.Devices.Power.Battery DefaultBattery
        {
            get
            {
                return battery ?? (battery = Windows.Devices.Power.Battery.AggregateBattery);
            }
        }

        public int RemainingChargePercent
        {
            get
            {
                var finalReport = DefaultBattery.GetReport();
                var finalPercent = -1;

                if (finalReport.RemainingCapacityInMilliwattHours.HasValue && finalReport.FullChargeCapacityInMilliwattHours.HasValue)
                {
                    finalPercent = (int)((finalReport.RemainingCapacityInMilliwattHours.Value /
                        (double)finalReport.FullChargeCapacityInMilliwattHours.Value) * 100);
                }
                return finalPercent;
            }
        }

        public BatteryStatus Status
        {
            get
            {
                var report = DefaultBattery.GetReport();
                var percentage = RemainingChargePercent;

                if (percentage >= 1.0)
                {
                    status = BatteryStatus.Full;
                }
                else if (percentage < 0)
                {
                    status = BatteryStatus.Unknown;
                }
                else
                {
                    switch (report.Status)
                    {
                        case Windows.System.Power.BatteryStatus.Charging:
                            status = BatteryStatus.Charging;
                            break;
                        case Windows.System.Power.BatteryStatus.Discharging:
                            status = BatteryStatus.Discharging;
                            break;
                        case Windows.System.Power.BatteryStatus.Idle:
                            status = BatteryStatus.NotCharging;
                            break;
                        case Windows.System.Power.BatteryStatus.NotPresent:
                            status = BatteryStatus.Unknown;
                            break;
                    }
                }
                return status;
            }
        }

        public PowerSource PowerSource
        {
            get
            {
                if (status == BatteryStatus.Full || status == BatteryStatus.Charging)
                {
                    return PowerSource.Ac;
                }
                return PowerSource.Battery;
            }
        }
    }
}
```

L'attributo `[assembly]` sopra la dichiarazione dello spazio dei nomi registra la classe come implementazione dell'interfaccia `IBattery` e ciò significa che è possibile usare `DependencyService.Get<IBattery>` nel codice condiviso per crearne un'istanza.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementazione nel codice condiviso

Ora che è stata implementata l'interfaccia per ogni piattaforma, è possibile scrivere l'applicazione condivisa per usarla. L'applicazione sarà costituita da una pagina con un pulsante che, in seguito al tocco, aggiorna il testo indicando lo stato della batteria corrente. Usa `DependencyService` per ottenere un'istanza dell'interfaccia `IBattery`. In fase di esecuzione, questa istanza sarà l'implementazione specifica della piattaforma con accesso completo all'SDK nativo.

```csharp
public MainPage ()
{
    var button = new Button {
        Text = "Click for battery info",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    button.Clicked += (sender, e) => {
        var bat = DependencyService.Get<IBattery>();

        switch (bat.PowerSource){
          case PowerSource.Battery:
            button.Text = "Battery - ";
            break;
          case PowerSource.Ac:
            button.Text = "AC - ";
            break;
          case PowerSource.Usb:
            button.Text = "USB - ";
            break;
          case PowerSource.Wireless:
            button.Text = "Wireless - ";
            break;
          case PowerSource.Other:
          default:
            button.Text = "Other - ";
            break;
        }
        switch (bat.Status){
          case BatteryStatus.Charging:
            button.Text += "Charging";
            break;
          case BatteryStatus.Discharging:
            button.Text += "Discharging";
            break;
          case BatteryStatus.NotCharging:
            button.Text += "Not Charging";
            break;
          case BatteryStatus.Full:
            button.Text += "Full";
            break;
          case BatteryStatus.Unknown:
          default:
            button.Text += "Unknown";
            break;
        }
    };
    Content = button;
}
```

Se si esegue questa applicazione in iOS, Android o UWP e si preme il pulsante, il testo del pulsante verrà aggiornato per indicare lo stato corrente dell'alimentazione del dispositivo.

![](battery-info-images/battery.png "Esempio di stato della batteria")


## <a name="related-links"></a>Collegamenti correlati

- [DependencyService (sample)](https://developer.xamarin.com/samples/DependencyService) (Esempio di DependencyService)
- [Using DependencyService (sample)](https://developer.xamarin.com/samples/UsingDependencyService/) (Esempio d'uso di DependencyService)
- [Esempi di Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
