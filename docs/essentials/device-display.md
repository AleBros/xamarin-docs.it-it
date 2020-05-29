---
title: " Xamarin.Essentials : informazioni di visualizzazione del dispositivo" Description: "questo documento descrive la classe DeviceDisplay in Xamarin.Essentials , che fornisce la metrica dello schermo per il dispositivo in cui è in esecuzione l'applicazione".
ms. AssetID: 2821C908-C613-490D-8E8C-1BD3269FCEEA Author: jamesmontemagno ms. Custom: video ms. Author: Jamont ms. Date: 11/04/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Informazioni sulla visualizzazione del dispositivo

La classe **DeviceDisplay** fornisce informazioni sulle metriche dello schermo del dispositivo in cui è in esecuzione l'applicazione e può richiedere di evitare la sospensione dello schermo quando l'applicazione è in esecuzione.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>Uso di DeviceDisplay

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

## <a name="main-display-info"></a>Informazioni sullo schermo principale

Oltre alle informazioni di base sul dispositivo, la classe **DeviceDisplay** contiene informazioni sullo schermo e l'orientamento del dispositivo.

```csharp
// Get Metrics
var mainDisplayInfo = DeviceDisplay.MainDisplayInfo;

// Orientation (Landscape, Portrait, Square, Unknown)
var orientation = mainDisplayInfo.Orientation;

// Rotation (0, 90, 180, 270)
var rotation = mainDisplayInfo.Rotation;

// Width (in pixels)
var width = mainDisplayInfo.Width;

// Height (in pixels)
var height = mainDisplayInfo.Height;

// Screen density
var density = mainDisplayInfo.Density;
```

La classe **DeviceDisplay** espone anche un evento che è possibile sottoscrivere, attivato quando le metriche dello schermo vengono modificate:

```csharp
public class DisplayInfoTest
{
    public DisplayInfoTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.MainDisplayInfoChanged += OnMainDisplayInfoChanged;
    }

    void OnMainDisplayInfoChanged(object sender, DisplayInfoChangedEventArgs  e)
    {
        // Process changes
        var displayInfo = e.DisplayInfo;
    }
}
```

La classe **DeviceDisplay** espone una proprietà `bool` denominata `KeepScreenOn` che può essere impostata per evitare lo spegnimento o il blocco dello schermo del dispositivo.

```csharp
public class KeepScreenOnTest
{
    public void ToggleScreenLock()
    {
        DeviceDisplay.KeepScreenOn = !DeviceDisplay.KeepScreenOn;
    }
}
```

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="android"></a>[Android](#tab/android)

Nessuna differenza.

# <a name="ios"></a>[iOS](#tab/ios)

- L'accesso a `DeviceDisplay` deve essere eseguito nel thread dell'interfaccia utente. In caso contrario, verrà generata un'eccezione. È possibile usare il [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) metodo per eseguire tale codice sul thread UI.

# <a name="uwp"></a>[UWP](#tab/uwp)

Nessuna differenza.

--------------

## <a name="api"></a>API

- [Codice sorgente di DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Documentazione dell'API DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Device-Display-Information-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
