---
title: Inizializzazione e configurazione del mapping di Novell. Forms
description: Questo articolo illustra come usare la classe di mapping Novell. Forms per usare le API della mappa nativa su ogni piattaforma per offrire un'esperienza di mapping familiare per gli utenti.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/07/2019
ms.openlocfilehash: d9b1b93b0667415281bb04e2c4264f03be19bd83
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697388"
---
# <a name="xamarinforms-map-initialization-and-configuration"></a>Inizializzazione e configurazione del mapping di Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

_Novell. Forms usa le API della mappa nativa in ogni piattaforma._

Novell. Forms. Maps usa le API della mappa nativa in ogni piattaforma. Ciò offre un'esperienza di mapping veloce e familiare per gli utenti, ma significa che alcuni passaggi di configurazione sono necessari per rispettare le esigenze dell'API per ogni piattaforma.
Una volta configurata, il controllo `Map` funziona esattamente come qualsiasi altro elemento Novell. Forms nel codice comune.

Il controllo mappa è stato usato nell'esempio [MapsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps) , illustrato di seguito.

 [![Mappe nell'esempio MobileCRM](map-images/maps-zoom-sml.png "Esempio di controllo mappa")](map-images/maps-zoom.png#lightbox "Esempio di controllo mappa")

Per migliorare ulteriormente la funzionalità di mapping, è possibile creare un [renderer personalizzato della mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md).

<a name="Maps_Initialization" />

## <a name="map-initialization"></a>Inizializzazione mappa

Quando si aggiungono mappe a un'applicazione Novell. Forms, **Novell. Forms. Maps** è un pacchetto NuGet separato che è necessario aggiungere a ogni progetto nella soluzione.
In Android è anche presente una dipendenza da GooglePlayServices (un altro NuGet) che viene scaricata automaticamente quando si aggiunge Novell. Forms. maps.

Dopo l'installazione del pacchetto NuGet, è necessario un codice di inizializzazione in ogni progetto di applicazione, *dopo* la chiamata al metodo `Xamarin.Forms.Forms.Init`. Per iOS usare il codice seguente:

```csharp
Xamarin.FormsMaps.Init();
```

In Android è necessario passare gli stessi parametri `Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

Per il piattaforma UWP (Universal Windows Platform) (UWP) usare il codice seguente:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Aggiungere questa chiamata nei file seguenti per ogni piattaforma:

- file **iOS** -AppDelegate.cs, nel metodo `FinishedLaunching`.
- File **Android** -MainActivity.cs, nel metodo `OnCreate`.
- File **UWP** -MainPage.XAML.cs nel costruttore `MainPage`.

Una volta aggiunto il pacchetto NuGet e il metodo di inizializzazione chiamato all'interno di ogni applicazione, `Xamarin.Forms.Maps` API possono essere utilizzate nel progetto di libreria .NET Standard comune o nel codice del progetto condiviso.

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>Configurazione della piattaforma

Per alcune piattaforme è necessario eseguire ulteriori passaggi di configurazione prima che venga visualizzata la mappa.

### <a name="ios"></a>iOS

Per accedere ai servizi di posizione in iOS, è necessario impostare le chiavi seguenti in **info. plist**:

- iOS 11
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : per l'uso dei servizi di posizione quando l'app è in uso
  - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) : per usare i servizi di posizione in qualsiasi momento
- iOS 10 e versioni precedenti
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : per l'uso dei servizi di posizione quando l'app è in uso
  - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) : per usare i servizi di posizione in qualsiasi momento    

Per supportare iOS 11 e versioni precedenti, è possibile includere tutte e tre le chiavi: `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription` e `NSLocationAlwaysUsageDescription`.

Di seguito è riportata la rappresentazione XML per queste chiavi in **info. plist** . È necessario aggiornare i valori di `string` per riflettere il modo in cui l'applicazione usa le informazioni sul percorso:

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

Le voci **info. plist** possono essere aggiunte anche nella visualizzazione **origine** durante la modifica del file **info. plist** :

![INFO. plist per iOS 8](map-images/ios8-map-permissions.png "iOS 8 richieste Info. plist voci")

### <a name="android"></a>Android

Per usare l' [API di Google Maps v2](https://developers.google.com/maps/documentation/android/) in Android, è necessario generare una chiave API e aggiungerla al progetto Android.
Seguire le istruzioni riportate nella documentazione di Novell per [ottenere una chiave di Google Maps API v2](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).
Dopo aver seguito le istruzioni, incollare la chiave API nel file **Properties/file AndroidManifest. XML** (visualizzare l'origine e trovare/aggiornare il seguente elemento):

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

Senza una chiave API valida, il controllo Maps viene visualizzato come una casella grigia in Android.

> [!NOTE]
> Si noti che, per consentire all'APK di accedere a Google Maps, è necessario includere le impronte digitali SHA-1 e i nomi di pacchetto per ogni archivio chiavi (debug e versione) usati per firmare l'APK. Ad esempio, se si usa un computer per il debug e un altro computer per la generazione del file APK della versione, è necessario includere l'impronta digitale del certificato SHA-1 dall'archivio chiavi di debug del primo computer e l'impronta digitale del certificato SHA-1 dal keystore versione di secondo computer. Ricordarsi anche di modificare le credenziali chiave se il **nome del pacchetto** dell'app viene modificato. Vedere [ottenere una chiave dell'API v2 di Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

È anche necessario abilitare le autorizzazioni appropriate facendo clic con il pulsante destro del mouse sul progetto Android e scegliendo **opzioni > compila > applicazione Android** e selezionando le opzioni seguenti:

- `AccessCoarseLocation`
- `AccessFineLocation`
- `AccessLocationExtraCommands`
- `AccessMockLocation`
- `AccessNetworkState`
- `AccessWifiState`
- `Internet`

Alcune di queste sono illustrate nella schermata seguente:

![Autorizzazioni necessarie per Android](map-images/android-map-permissions.png "Autorizzazioni necessarie per Android")

Le ultime due sono richieste perché le applicazioni richiedono una connessione di rete per scaricare i dati della mappa. Per altre informazioni, vedere le informazioni sulle [autorizzazioni](https://developer.android.com/reference/android/Manifest.permission.html) per Android.

Android 9 ha inoltre rimosso la libreria client Apache HTTP dal bootclasspath, quindi non è disponibile per le applicazioni destinate all'API 28 o versioni successive. È necessario aggiungere la riga seguente al nodo `application` del file **file AndroidManifest. XML** per continuare a usare il client HTTP Apache nelle applicazioni destinate all'API 28 o versioni successive:

```xml
<application ...>
    ...
    <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

Per usare le mappe nella piattaforma UWP (Universal Windows Platform) è necessario generare un token di autorizzazione. Per ulteriori informazioni, vedere [Request a Maps Authentication Key](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx) in MSDN.

Il token di autenticazione deve quindi essere specificato nella chiamata al metodo `FormsMaps.Init("AUTHORIZATION_TOKEN")` per autenticare l'app con Bing Maps.

<a name="Using_Maps" />

## <a name="map-configuration"></a>Configurazione della mappa

Vedere [MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) nell'esempio mobileCRM per un esempio di come è possibile usare il controllo Map nel codice. Una semplice classe `MapPage` potrebbe avere un aspetto simile al seguente. si noti che viene creata una nuova `MapSpan` per posizionare la visualizzazione della mappa:

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

Il contenuto della mappa può essere modificato anche impostando la proprietà `MapType`, in modo da visualizzare una normale mappa stradale (impostazione predefinita), un'immagine satellite o una combinazione di entrambi.

```csharp
map.MapType = MapType.Street;
```

I valori `MapType` validi sono:

- `Hybrid`
- `Satellite`
- `Street` (impostazione predefinita)

### <a name="map-region-and-mapspan"></a>Area mappa e MapSpan

Come illustrato nel frammento di codice precedente, fornire un'istanza `MapSpan` a un costruttore della mappa imposta la visualizzazione iniziale, ovvero il punto centrale e il livello di zoom, della mappa quando viene caricata. Esistono due modi per creare una nuova istanza di `MapSpan`:

- **MapSpan. FromCenterAndRadius ()** : metodo statico per creare un intervallo da un `Position` e specificare una `Distance`.
- **nuovo costruttore MapSpan ()** che utilizza un `Position` e i gradi di latitudine e Longitudine da visualizzare.

Il metodo `MoveToRegion` sulla classe Map può quindi essere utilizzato per modificare la posizione o il livello di zoom della mappa. Per modificare il livello di zoom della mappa senza modificare la posizione, creare una nuova `MapSpan` usando il percorso corrente dalla proprietà `VisibleRegion.Center` del controllo mappa. È possibile usare un `Slider` per controllare lo zoom della mappa come questo (Tuttavia, lo zoom diretto nel controllo mappa non può attualmente aggiornare il valore del dispositivo di scorrimento):

```csharp
Slider slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) =>
{
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

[![Mappe con zoom](map-images/maps-zoom-sml.png "Zoom controllo mappa")](map-images/maps-zoom.png#lightbox "Zoom controllo mappa")

Inoltre, la classe [`Map`](xref:Xamarin.Forms.Maps.Map) dispone di una proprietà `MoveToLastRegionOnLayoutChange` di tipo `bool`, supportata da una proprietà associabile. Per impostazione predefinita, questa proprietà è `true`, che indica che l'area mappa visualizzata passerà dall'area corrente all'area impostata in precedenza quando si verifica una modifica del layout, ad esempio la rotazione del dispositivo. Quando questa proprietà è impostata su `false`, l'area mappa visualizzata rimarrà centrata quando si verifica una modifica del layout. Nell'esempio seguente viene illustrata l'impostazione di questa proprietà:

```csharp
map.MoveToLastRegionOnLayoutChange = false;
```

### <a name="map-clicks"></a>Clic mappa

`Map` definisce un evento di `MapClicked` generato quando si tocca la mappa. L'oggetto `MapClickedEventArgs` che accompagna l'evento `MapClicked` dispone di una singola proprietà denominata `Position`, di tipo `Position`. Quando viene generato l'evento, il valore della proprietà `Position` viene impostato sul percorso della mappa che è stato toccato.

Nell'esempio di codice riportato di seguito viene illustrato un gestore eventi per l'evento `MapClicked`:

```csharp
map.MapClicked += OnMapClicked;

void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

In questo esempio, il gestore dell'evento `OnMapClicked` restituisce la latitudine e la longitudine che rappresenta la posizione della mappa toccata.

<a name="Using_Xaml" />

### <a name="create-a-map-in-xaml"></a>Creare una mappa in XAML

È anche possibile creare mappe in XAML, come illustrato nell'esempio seguente:

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map x:Name="MyMap"
                  Clicked="OnMapClicked"
                  WidthRequest="320"
                  HeightRequest="200"                  
                  IsShowingUser="true"
                  MapType="Hybrid" />
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> Per fare riferimento ai controlli Novell. Forms. Maps è necessaria una definizione di spazio dei nomi `xmlns` aggiuntiva. Nell'esempio precedente viene fatto riferimento allo spazio dei nomi `Xamarin.Forms.Maps` tramite la parola chiave `maps`.

Il `MapRegion` può essere impostato nel codice usando il riferimento denominato per l'`Map`:

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Pin di Novell. Forms. Maps](~/xamarin-forms/user-interface/map/pins.md).
- [API Maps](xref:Xamarin.Forms.Maps)
- [Renderer personalizzato mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
