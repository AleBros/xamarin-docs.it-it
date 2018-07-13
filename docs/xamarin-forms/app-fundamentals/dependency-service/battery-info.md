---
title: Verifica dello stato della batteria
description: Questo articolo illustra come usare la classe di xamarin. Forms DependencyService per accedere alle informazioni di batteria in modo nativo per ogni piattaforma.
ms.prod: xamarin
ms.assetid: CF1C5A73-84ED-407D-BDC5-EB1D83D2D3DB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: cbb4a01ac2c6d933fe40a0b3c2571d1fe3ce75c0
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998395"
---
# <a name="checking-battery-status"></a>Verifica dello stato della batteria

Questo articolo illustra la creazione di un'applicazione che controlla lo stato della batteria. Questo articolo si basa sul plug-in batteria dal James Montemagno. Per altre informazioni, vedere la [repository GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery).

Poiché xamarin. Forms non include la funzionalità per verificare lo stato della batteria corrente, questa applicazione sarà necessario utilizzare [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) possa sfruttare i vantaggi delle API native.  Questo articolo verranno illustrati i passaggi seguenti per utilizzare `DependencyService`:

- **[Creazione dell'interfaccia](#Creating_the_Interface)**  &ndash; comprendere come viene creata l'interfaccia nel codice condiviso.
- **[Implementazione di iOS](#iOS_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per iOS.
- **[Implementazione di Android](#Android_Implementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per Android.
- **[L'implementazione della piattaforma Windows Universal](#UWPImplementation)**  &ndash; informazioni su come implementare l'interfaccia nel codice nativo per la Universal Windows Platform (UWP).
- **[Implementazione nel codice condiviso](#Implementing_in_Shared_Code)**  &ndash; informazioni su come usare `DependencyService` chiamino l'implementazione native da codice condiviso.

Al termine, l'applicazione usando `DependencyService` avrà la struttura seguente:

![](battery-info-images/battery-diagram.png "Struttura dell'applicazione DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Creazione dell'interfaccia

Innanzitutto, creare un'interfaccia nel codice condiviso che esprime la funzionalità desiderata. Nel caso di una batteria verifica dell'applicazione, le informazioni rilevanti sono la percentuale di rimanente della batteria, se il dispositivo è l'addebito ha o non e modo in cui il dispositivo sta ricevendo power:

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

Scrivere il codice per questa interfaccia nel codice condiviso consentirà l'app xamarin. Forms per le API di gestione dell'alimentazione in ogni piattaforma di accesso.

> [!NOTE]
> Classi che implementano l'interfaccia devono avere un costruttore senza parametri per lavorare con i `DependencyService`. Nejde definovat konstruktory dalle interfacce.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementazione di iOS

Il `IBattery` interfaccia deve essere implementata in ogni progetto specifico della piattaforma dell'applicazione. L'implementazione di iOS verrà utilizzato l'oggetto nativo [ `UIDevice` ](https://developer.xamarin.com/api/type/UIKit.UIDevice/) API per accedere alle informazioni della batteria. Si noti che la classe seguente ha un costruttore senza parametri in modo che il `DependencyService` può creare nuove istanze:

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

Infine, aggiungere questo `[assembly]` dell'attributo di sopra della classe e all'esterno di qualsiasi spazio dei nomi che è stati definiti, tra cui le operazioni `using` istruzioni:

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

Questo attributo registra la classe come implementazione del `IBattery` interfaccia, il che significa che `DependencyService.Get<IBattery>` utilizzabile nel codice condiviso per creare un'istanza di esso:

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementazione di Android

L'implementazione di Android Usa la [ `Android.OS.BatteryManager` ](https://developer.xamarin.com/api/type/Android.OS.BatteryManager/) API. Questa implementazione è più complessa rispetto alla versione di iOS, che richiedono controlli per gestire la mancanza di autorizzazioni della batteria:

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

Aggiungere quanto segue `[assembly]` dell'attributo di sopra della classe e all'esterno di qualsiasi spazio dei nomi che è stati definiti, tra cui le operazioni `using` istruzioni:

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

Questo attributo registra la classe come implementazione del `IBattery` interfaccia, il che significa che `DependencyService.Get<IBattery>` può essere usato nel codice condiviso è possibile creare un'istanza.

<a name="UWPImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementazione della piattaforma Windows universale

L'implementazione di UWP Usa la `Windows.Devices.Power` API per ottenere informazioni sullo stato della batteria:

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

Il `[assembly]` attributo sopra la dichiarazione dello spazio dei nomi consente di registrare la classe come implementazione del `IBattery` interfaccia, il che significa che `DependencyService.Get<IBattery>` utilizzabile nel codice condiviso per creare un'istanza.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementazione nel codice condiviso

Ora che è stata implementata l'interfaccia per ogni piattaforma, l'applicazione condivisa può essere scritta per sfruttare i vantaggi di esso. L'applicazione sarà costituito da una pagina con un pulsante che, se lo si tocca aggiornamenti il testo con lo stato della batteria corrente. Usa il `DependencyService` per ottenere un'istanza del `IBattery` interfaccia. In fase di esecuzione, questa istanza sarà l'implementazione specifica della piattaforma che dispone dell'accesso completo a SDK nativi.

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

Esecuzione dell'applicazione in iOS, Android, o UWP e premendo il pulsante comporterà il testo del pulsante l'aggiornamento in modo da riflettere lo stato corrente di alimentazione del dispositivo.

![](battery-info-images/battery.png "Esempio di stato della batteria")


## <a name="related-links"></a>Collegamenti correlati

- [DependencyService (esempio)](https://developer.xamarin.com/samples/DependencyService)
- [Con DependencyService (esempio)](https://developer.xamarin.com/samples/UsingDependencyService/)
- [Esempi di Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
