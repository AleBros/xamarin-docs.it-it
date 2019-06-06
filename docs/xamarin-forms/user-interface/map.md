---
title: Mappa di xamarin. Forms
description: Questo articolo illustra come usare la classe Map di xamarin. Forms per usare la mappa API native in ogni piattaforma per fornire che già esegue il mapping di esperienza degli utenti.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2018
ms.openlocfilehash: 166927f2168015cb4786502d841e01b2faeb0c51
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66741007"
---
# <a name="xamarinforms-map"></a>Mappa di xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithMaps/)

_Xamarin. Forms Usa la mappa nativa le API in ciascuna piattaforma._

Verifica Usa la mappa nativa le API in ciascuna piattaforma. Questo offre un'esperienza di mappe veloce e familiare per gli utenti, ma significa che alcuni passaggi di configurazione sono necessari per soddisfare requisiti ogni API piattaforme.
Una volta configurato, il `Map` controllo funziona esattamente come qualsiasi altro elemento di xamarin. Forms nel codice comune.

Il controllo mappa è stata invece utilizzato la [MapsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithMaps/) esempio mostrata di seguito.

 [![Esegue il mapping dell'esempio MobileCRM](map-images/maps-zoom-sml.png "esempio di controllo mappa")](map-images/maps-zoom.png#lightbox "esempio controllo mappa")

La funzionalità mappa può essere migliorata ulteriormente mediante la creazione di un [eseguire il mapping di renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

<a name="Maps_Initialization" />

## <a name="maps-initialization"></a>Inizializzazione di mappe

Quando si aggiunge mappe a un'applicazione xamarin. Forms **verifica** è un pacchetto NuGet separato che è necessario aggiungere a ogni progetto nella soluzione.
In Android, anche questo presenta una dipendenza su GooglePlayServices (un altro NuGet) che viene scaricata automaticamente quando si aggiunge una verifica.

Dopo aver installato il pacchetto NuGet, è necessario un codice di inizializzazione in ogni progetto di applicazione *dopo aver* il `Xamarin.Forms.Forms.Init` chiamata al metodo. Per iOS usare il codice seguente:

```csharp
Xamarin.FormsMaps.Init();
```

In Android è necessario passare gli stessi parametri `Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Per la Universal Windows Platform (UWP) usare il codice seguente:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Aggiungere questa chiamata nei file seguenti per ogni piattaforma:

-  **iOS** -AppDelegate.cs nel file, il `FinishedLaunching` (metodo).
-  **Android** -MainActivity.cs nel file, il `OnCreate` (metodo).
-  **Piattaforma UWP** -nel file MainPage.xaml.cs di `MainPage` costruttore.

Dopo che è stato aggiunto il pacchetto NuGet e chiamato il metodo di inizializzazione all'interno di ogni applicazione, `Xamarin.Forms.Maps` API possono essere usate nel codice del progetto condiviso o progetto di libreria .NET Standard più comune.

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>Configurazione della piattaforma

In alcune piattaforme sono necessari passaggi di configurazione aggiuntiva prima che la mappa verrà visualizzata.

### <a name="ios"></a>iOS

Per accedere ai servizi di posizione su iOS, è necessario impostare le chiavi seguenti in **Info. plist**:

- iOS 11
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) -per l'uso di servizi di posizione quando l'app è in uso
    - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) -per l'uso di servizi location in qualsiasi momento
- iOS 10 e versioni precedenti
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) -per l'uso di servizi di posizione quando l'app è in uso
    - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) -per l'uso di servizi location in qualsiasi momento    

Per supportare iOS 11 e versioni precedenti, è possibile includere tutte le tre chiavi: `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription`, e `NSLocationAlwaysUsageDescription`.

La rappresentazione XML per queste chiavi nel **Info. plist** è illustrato di seguito. È necessario aggiornare il `string` valori in modo da riflettere come l'applicazione usa le informazioni sul percorso:

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

Il **Info. plist** è inoltre possibile aggiungere le voci nella **origine** visualizzazione mentre si modifica il **Info. plist** file:

![File Info. plist per iOS 8](map-images/ios8-map-permissions.png "voci Info. plist necessarie iOS 8")

### <a name="android"></a>Android

Usare la [API Google Maps v2](https://developers.google.com/maps/documentation/android/) in Android è necessario generare una chiave API e aggiungerlo al progetto Android.
Seguire le istruzioni nel documento Xamarin nella [come ottenere una chiave di Google Maps API v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
Dopo aver seguito le istruzioni, incollare la chiave API nel **Properties/AndroidManifest.xml** file (HTML e trova o aggiornare l'elemento seguente):

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

Senza una chiave API valida controllo maps verrà visualizzato come una casella grigia in Android.

> [!NOTE]
> Si noti che, affinché il pacchetto APK l'accesso a Google Maps, è necessario includere le impronte digitali SHA-1 e creare un pacchetto nomi per ogni archivio chiavi (debug e rilascio) che usano per firmare l'APK. Ad esempio, se si usa un computer per il debug e un altro computer per la generazione del file APK di rilascio, è necessario includere l'impronta digitale certificato SHA-1 dall'archivio di chiavi di debug del computer prima e l'impronta digitale certificato SHA-1 dalla versione dell'archivio chiavi di il secondo computer. Ricordare anche di modificare le credenziali della chiave, se l'app **nome del pacchetto** le modifiche. Visualizzare [come ottenere una chiave di Google Maps API v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

Sarà anche necessario abilitare le autorizzazioni appropriate facendo clic sul progetto Android e selezionando **Opzioni > compilazione > applicazione Android** e perfezione quanto segue:

* `AccessCoarseLocation`
* `AccessFineLocation`
* `AccessLocationExtraCommands`
* `AccessMockLocation`
* `AccessNetworkState`
* `AccessWifiState`
* `Internet`

Alcuni di questi vengono visualizzati nella schermata seguente:

![Autorizzazioni necessarie per Android](map-images/android-map-permissions.png "autorizzazioni necessarie per Android")

Gli ultimi due sono necessari perché le applicazioni richiedono una connessione di rete per scaricare i dati della mappa. Leggere informazioni su Android [autorizzazioni](https://developer.android.com/reference/android/Manifest.permission.html) per altre informazioni.

Inoltre, 9 Android ha rimosso la libreria client HTTP Apache dal bootclasspath e pertanto non è disponibile per le applicazioni che usano API 28 o superiore. La riga seguente deve essere aggiunto per il `application` nodo del **androidmanifest. XML** file per continuare a usare il client HTTP Apache in applicazioni destinate a API 28 o superiore:

```xml
<application ...>
    ...
    <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Per usare le mappe in Universal Windows Platform è necessario generare un token di autorizzazione. Per altre informazioni, vedere [richiedere una chiave di autenticazione mappe](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx) su MSDN.

Il token di autenticazione deve quindi essere specificato nel `FormsMaps.Init("AUTHORIZATION_TOKEN")` chiamata al metodo, per autenticare l'app con Bing Maps.

<a name="Using_Maps" />

## <a name="using-maps"></a>Utilizzo di mappe

Vedere le [MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) nell'esempio MobileCRM per un esempio di come è possibile utilizzare il controllo mappa nel codice. Un semplice `MapPage` classe potrebbe essere simile a questo - si noti che un nuovo `MapSpan` viene creato per posizionare la visualizzazione della mappa:

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

### <a name="map-type"></a>Tipo di mappa

Contenuto della mappa può anche essere modificato impostando la `MapType` proprietà, per visualizzare una mappa strada regolare (l'impostazione predefinita), immagini satellitari o una combinazione di entrambi.

```csharp
map.MapType == MapType.Street;
```

Valido `MapType` i valori sono:

-  Ibrido
-  Satellite
-  Via e numero civico (predefinito)

### <a name="map-region-and-mapspan"></a>Area della mappa e MapSpan

Come illustrato nel frammento di codice precedente, fornendo un `MapSpan` istanza a un costruttore della mappa viene impostata la visualizzazione iniziale (punto al centro e zoom a livello) della mappa quando viene caricato. Il `MoveToRegion` metodo nella classe map può essere utilizzato quindi per modificare il livello di posizione o zoom della mappa. Esistono due modi per creare un nuovo `MapSpan` istanza:

-  **MapSpan.FromCenterAndRadius()** -un metodo statico per creare un intervallo da un `Position` e specificando un `Distance` .
-  **New () MapSpan** -costruttore che utilizza un `Position` e i gradi di latitudine e longitudine da visualizzare.


Per modificare il livello di zoom della mappa senza alterare la posizione, creare una nuova `MapSpan` usando il percorso corrente dal `VisibleRegion.Center` proprietà del controllo mappa. Oggetto `Slider` potrebbe essere utilizzata per controllare lo zoom della mappa simile alla seguente (tuttavia, lo zoom direttamente nel controllo mappa non è attualmente di aggiornare il valore del dispositivo di scorrimento):

```csharp
var slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) => {
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

 [![Le mappe con zoom](map-images/maps-zoom-sml.png "mappa controllo Zoom")](map-images/maps-zoom.png#lightbox "mappa controllo Zoom")

### <a name="map-pins"></a>Eseguire il mapping pin

Percorsi possono essere contrassegnati nella mappa insieme `Pin` oggetti.

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

 `PinType` può essere impostato su uno dei valori seguenti, che possono influenzare che il pin viene eseguito il rendering (a seconda della piattaforma):

-  Generico
-  Sul posto
-  SavedPin
-  SearchResult

<a name="Using_Xaml" />

## <a name="using-xaml"></a>Usando XAML

Maps può essere posizionato anche nei layout XAML come illustrato in questo frammento di codice.

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
                  MapType="Hybrid" />
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> Un ulteriore `xmlns` definizione dello spazio dei nomi è necessario per fare riferimento ai controlli di verifica.

Il `MapRegion` e `Pins` possono essere impostate nel codice usando il `MyMap` riferimento (o qualunque sia la mappa è denominata).

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="populating-a-map-with-data-using-data-binding"></a>Popolamento di una mappa con dati mediante l'associazione dati

Il [ `Map` ](xref:Xamarin.Forms.Maps.Map) classe espone anche le proprietà seguenti:

- `ItemsSource` : specifica la raccolta di `IEnumerable` gli elementi da visualizzare.
- `ItemTemplate` : specifica la [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) da applicare a ogni elemento nella raccolta di elementi visualizzati.

Di conseguenza, un [ `Map` ](xref:Xamarin.Forms.Maps.Map) possono essere popolati con dati utilizzando un'associazione dati per l'associazione relativa `ItemsSource` proprietà da un `IEnumerable` raccolta:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
                  ItemsSource="{Binding Locations}">
            <maps:Map.ItemTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </maps:Map.ItemTemplate>
        </maps:Map>
        ...
    </Grid>
</ContentPage>
```

Il `ItemsSource` esegue l'associazione dati di proprietà per il `Locations` proprietà del modello di visualizzazione connessa, che restituisce un' `ObservableCollection` di `Location` gli oggetti che è un tipo personalizzato. Ciascuna `Location` oggetto definisce `Address` e `Description` delle proprietà, di tipo `string`e un `Position` proprietà, di tipo [ `Position` ](xref:Xamarin.Forms.Maps.Position).

L'aspetto di ogni elemento nel `IEnumerable` raccolta viene definita impostando il `ItemTemplate` proprietà a un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) che contiene un [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) dell'oggetto che esegue l'associazione dati a proprietà appropriate.

L'esempio di screenshot seguente mostra una [ `Map` ](xref:Xamarin.Forms.Maps.Map) la visualizzazione di un [ `Pin` ](xref:Xamarin.Forms.Maps.Pin) raccolta utilizzando l'associazione dati:

[![Screenshot della mappa con dati associati ai PIN, iOS e Android](map-images/pins-itemssource.png "puntine con i dati associati")](map-images/pins-itemssource-large.png#lightbox "puntine con i dati associati")

## <a name="related-links"></a>Collegamenti correlati

- [MapsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithMaps/)
- [Eseguire il mapping di Renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
