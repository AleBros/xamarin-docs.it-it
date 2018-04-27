---
title: Mappa
description: Xamarin. Forms Usa mappa API native in ciascuna piattaforma.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: e296ca79ee03e7fc61532758219b65946a8d4381
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2018
---
# <a name="map"></a>Mappa

_Xamarin. Forms Usa mappa API native in ciascuna piattaforma._

Xamarin.Forms.Maps utilizza la mappa native API in ciascuna piattaforma. Fornisce un'esperienza di mappe di familiarità, veloce per gli utenti, ma indica che alcuni passaggi di configurazione sono necessari per rispettare ogni requisiti API specifici piattaforme.
Una volta configurato, il `Map` controllo funziona in modo analogo a qualsiasi altro elemento di xamarin. Forms nel codice comune.

* [Esegue il mapping di inizializzazione](#Maps_Initialization) - tramite `Map` richiede il codice di inizializzazione aggiuntiva all'avvio.
* [Configurazione della piattaforma](#Platform_Configuration) -ogni piattaforma richiede una configurazione per le mappe da utilizzare.
* [Utilizzo di mappe in c#](#Using_Maps) -visualizzazione esegue il mapping e i pin tramite c#.
* [Utilizzo di mappe in XAML](#Using_Xaml) -visualizzazione di una mappa con XAML.

Il controllo mappa è stato utilizzato nel [MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/) esempio, in cui è illustrato di seguito.

 [![Nell'esempio MobileCRM mappe](map-images/maps-zoom-sml.png "esempio controllo mappa")](map-images/maps-zoom.png#lightbox "esempio controllo mappa")

Funzionalità di mapping può essere ulteriormente migliorata creando un [mapping renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

<a name="Maps_Initialization" />

## <a name="maps-initialization"></a>Inizializzazione di mappe

Quando si aggiungono mappe a un'applicazione di xamarin. Forms, **Xamarin.Forms.Maps** è un pacchetto NuGet separato che è necessario aggiungere a ogni progetto nella soluzione.
In Android, anche questo presenta una dipendenza su GooglePlayServices (un altro NuGet) che viene scaricata automaticamente quando si aggiunge Xamarin.Forms.Maps.

Dopo aver installato il pacchetto NuGet, è necessario un codice di inizializzazione in ogni progetto di applicazione, *dopo* il `Xamarin.Forms.Forms.Init` chiamata al metodo. Il codice seguente per iOS:

```csharp
Xamarin.FormsMaps.Init();
```

In Android è necessario passare gli stessi parametri `Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Per la piattaforma UWP (Universal Windows) usare il codice seguente:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Aggiungere questa chiamata nei file seguenti per ogni piattaforma:

-  **iOS** -appdelegate. cs, nel file di `FinishedLaunching` metodo.
-  **Android** -Mainactivity, nel file di `OnCreate` metodo.
-  **UWP** -MainPage.xaml.cs file, incluso nella `MainPage` costruttore.

Una volta il pacchetto NuGet è stato aggiunto e il metodo di inizializzazione chiamato all'interno di ogni tipo di applicazioni, `Xamarin.Forms.Maps` API possono essere utilizzate nel codice di libreria di classi Portabile o progetto condiviso comune.

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>Configurazione della piattaforma

Prima che la mappa verrà visualizzata, sono necessari passaggi di configurazione aggiuntive su alcune piattaforme.

### <a name="ios"></a>iOS

Per accedere a servizi di posizione su iOS, è necessario impostare le chiavi seguenti nel **Info. plist**:

- iOS 11
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : per l'utilizzo di servizi di posizione quando l'app è in uso
    - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) : per l'utilizzo di servizi di posizione in qualsiasi momento
- iOS 10 e versioni precedenti
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : per l'utilizzo di servizi di posizione quando l'app è in uso
    - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) : per l'utilizzo di servizi di posizione in qualsiasi momento    
    
Per supportare iOS 11 e versioni precedenti, è possibile includere tutte e tre le chiavi: `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription`, e `NSLocationAlwaysUsageDescription`.

Rappresentazione XML di tali chiavi in **Info. plist** è illustrato di seguito. È necessario aggiornare il `string` valori in modo da riflettere come l'applicazione usa le informazioni sul percorso:

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

Il **Info. plist** è inoltre possibile aggiungere le voci **origine** visualizzazione durante la modifica di **Info. plist** file:

![Info. plist per iOS 8](map-images/ios8-map-permissions.png "voci Info. plist obbligatorie iOS 8")


### <a name="android"></a>Android

Utilizzare il [Google Maps API v2](https://developers.google.com/maps/documentation/android/) in Android è necessario generare una chiave API e aggiungerlo al progetto Android.
Istruzioni, vedere la documentazione di Xamarin [ottenere una chiave di Google Maps API v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
Dopo aver seguito le istruzioni, incollare la chiave API di **Properties/AndroidManifest.xml** file (HTML e trova/aggiornare l'elemento seguente):

```xml
<meta-data
        android:name="com.google.android.geo.API_KEY"
        android:value="YOUR_API_KEY"/>
```

Senza una chiave API valida il controllo esegue il mapping verrà visualizzato come una casella di colore grigia in Android.

> [!NOTE]
> Ricordarsi di generare un'altra chiave utilizzando il file keystore che verrà utilizzato per firmare la versione di qualsiasi applicazione che viene caricato nell'archivio Google Play. La chiave è generare per lo sviluppo e debug non potrà funzionare e verrà interrotto l'app scaricata da Google Play visualizzazione della mappa. Ricordare inoltre di rigenerare la chiave se l'app **nome pacchetto** le modifiche.

È necessario anche abilitare le autorizzazioni appropriate facendo clic sul progetto Android e selezionando **Opzioni > compilare > applicazione Android** e scattare le operazioni seguenti:

* `AccessCoarseLocation`
* `AccessFineLocation`
* `AccessLocationExtraCommands`
* `AccessMockLocation`
* `AccessNetworkState`
* `AccessWifiState`
* `Internet`

Alcuni di questi vengono visualizzati nella schermata seguente:

![Autorizzazioni necessarie per Android](map-images/android-map-permissions.png "le autorizzazioni necessarie per Android")

Le ultime due sono necessari poiché le applicazioni richiedono una connessione di rete per scaricare i dati della mappa. Informazioni su Android [autorizzazioni](http://developer.android.com/reference/android/Manifest.permission.html) per altre informazioni.

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Per usare le mappe nella piattaforma Windows universale è necessario generare un token di autorizzazione. Per ulteriori informazioni, vedere [richiedere una chiave di autenticazione mappe](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx) su MSDN.

Il token di autenticazione deve quindi essere specificato nel `FormsMaps.Init("AUTHORIZATION_TOKEN")` chiamata al metodo, per autenticare l'app con Bing Maps.

<a name="Using_Maps" />

## <a name="using-maps"></a>Utilizzo di mappe

Vedere il [MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) nell'esempio MobileCRM per un esempio di come il controllo mappa può essere usato nel codice. Una semplice `MapPage` classe potrebbe essere simile a questo - si noti che un nuovo `MapSpan` viene creato per posizionare la visualizzazione della mappa:

```csharp
public class MapPage : ContentPage {
    public MapPage() {
        var map = new Map(
            MapSpan.FromCenterAndRadius(
                    new Position(37,-122), Distance.FromMiles(0.3))) {
                IsShowingUser = true,
                HeightRequest = 100,
                WidthRequest = 960,
                VerticalOptions = LayoutOptions.FillAndExpand
            };
        var stack = new StackLayout { Spacing = 0 };
        stack.Children.Add(map);
        Content = stack;
    }
}
```

### <a name="map-type"></a>Tipo mappa

Contenuto della mappa può anche essere modificato impostando la `MapType` proprietà, per visualizzare una mappa stradale normale (impostazione predefinita), immagini satellitari o una combinazione di entrambi.

```csharp
map.MapType == MapType.Street;
```

Valido `MapType` i valori sono:

-  Ibrida
-  Satellite
-  Via (impostazione predefinita)


### <a name="map-region-and-mapspan"></a>MapSpan e l'area della mappa

Come illustrato nel frammento di codice precedente, fornendo un `MapSpan` istanza a un costruttore di mappa consente di impostare la visualizzazione iniziale (punto centrale e livello di zoom) della mappa quando viene caricato. Il `MoveToRegion` metodo nella classe map quindi può essere utilizzato per modificare il livello di posizione o zoom della mappa. Esistono due modi per creare un nuovo `MapSpan` istanza:

-  **MapSpan.FromCenterAndRadius()** -metodo statico per creare un'estensione di un `Position` e specificando un `Distance` .
-  **New () MapSpan** -costruttore che utilizza un `Position` e i gradi di latitudine e longitudine da visualizzare.


Per modificare il livello di zoom della mappa senza modificare il percorso, creare un nuovo `MapSpan` utilizzando il percorso corrente dal `VisibleRegion.Center` proprietà del controllo mappa. Oggetto `Slider` potrebbe essere usate per controllare lo zoom della mappa simile al seguente (tuttavia, lo zoom direttamente nel controllo mappa non è attualmente di aggiornare il valore del dispositivo di scorrimento):

```csharp
var slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) => {
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

 [![Mappe con zoom](map-images/maps-zoom-sml.png "mappa controllo Zoom")](map-images/maps-zoom.png#lightbox "mappa controllo Zoom")

### <a name="map-pins"></a>Eseguire il mapping di pin

È possibile contrassegnare sulla mappa con percorsi `Pin` oggetti.

```csharp
var position = new Position(37,-122); // Latitude, Longitude
var pin = new Pin {
            Type = PinType.Place,
            Position = position,
            Label = "custom pin",
            Address = "custom detail info"
        };
map.Pins.Add(pin);
```

 `PinType` può essere impostato su uno dei valori seguenti, che possono influenzare le modalità di rendering del pin in (a seconda della piattaforma):

-  Generico
-  Sul posto
-  SavedPin
-  SearchResult


<a name="Using_Xaml" />

## <a name="using-xaml"></a>Utilizzo di Xaml

Mappe possono essere posizionate nei layout Xaml anche come illustrato in questo frammento.

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
    x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map WidthRequest="320" HeightRequest="200"
            x:Name="MyMap"
            IsShowingUser="true"
            MapType="Hybrid"
        />
    </StackLayout>
</ContentPage>
```

Il `MapRegion` e `Pins` possono essere impostate nel codice utilizzando il `MyMap` riferimento (o qualsiasi nome mappa). Si noti che un altro `xmlns` deve fare riferimento ai controlli Xamarin.Forms.Maps definizione dello spazio dei nomi.

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Il Xamarin.Forms.Maps è un NuGet separata che deve essere aggiunto a ogni progetto in una soluzione xamarin. Forms. Codice di inizializzazione è necessario, come anche alcuni passaggi di configurazione per iOS, Android e UWP.

Una volta configurata l'API viene eseguito il mapping può essere utilizzata per eseguire il rendering delle mappe con marcatori pin in poche righe di codice. Esegue il mapping può essere ulteriormente migliorato con un [renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).


## <a name="related-links"></a>Collegamenti correlati

- [MapsSample](https://developer.xamarin.com/samples/WorkingWithMaps/)
- [Eseguire il mapping di Renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
