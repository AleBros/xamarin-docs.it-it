---
title: Inizializzazione e configurazione del mapping di Xamarin.Forms
description: Il pacchetto NuGet Xamarin.Forms. Maps è necessario per usare la funzionalità Maps in un'applicazione. Inoltre, l'accesso alla posizione dell'utente richiede l'autorizzazione per la posizione per l'applicazione.
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/06/2019
ms.openlocfilehash: 038ff27907573c1fe15516f6f4caf26d0892ab9f
ms.sourcegitcommit: 283810340de5310f63ef7c3e4b266fe9dc2ffcaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73662335"
---
# <a name="xamarinforms-map-initialization-and-configuration"></a>Inizializzazione e configurazione del mapping di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Il controllo [`Map`](xref:Xamarin.Forms.Maps.Map) usa il controllo mappa nativo in ogni piattaforma. Ciò offre un'esperienza di mapping veloce e familiare per gli utenti, ma significa che alcuni passaggi di configurazione sono necessari per rispettare le esigenze dell'API per ogni piattaforma.

## <a name="map-initialization"></a>Inizializzazione mappa

Il controllo [`Map`](xref:Xamarin.Forms.Maps.Map) viene fornito dal pacchetto NuGet [Xamarin.Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) , che deve essere aggiunto a tutti i progetti nella soluzione.

Dopo l'installazione del pacchetto NuGet [Xamarin.Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) , è necessario inizializzarlo in ogni progetto di piattaforma.

In iOS questo dovrebbe verificarsi in **AppDelegate.cs** richiamando il metodo `Xamarin.FormsMaps.Init` *dopo* il metodo `Xamarin.Forms.Forms.Init`:

```csharp
Xamarin.FormsMaps.Init();
```

In Android questa operazione dovrebbe essere eseguita in **MainActivity.cs** richiamando il metodo `Xamarin.FormsMaps.Init` *dopo* il metodo `Xamarin.Forms.Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, savedInstanceState);
```

Nel piattaforma UWP (Universal Windows Platform) (UWP), questa operazione deve essere eseguita in **MainPage.XAML.cs** richiamando il metodo `Xamarin.FormsMaps.Init` dal Costruttore `MainPage`:

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

Per informazioni sul token di autenticazione richiesto per UWP, vedere [piattaforma UWP (Universal Windows Platform)](#universal-windows-platform).

Una volta aggiunto il pacchetto NuGet e il metodo di inizializzazione chiamato all'interno di ogni applicazione, è possibile usare `Xamarin.Forms.Maps` API nel progetto di codice condiviso.

## <a name="platform-configuration"></a>Configurazione della piattaforma

È necessaria una configurazione aggiuntiva in Android e il piattaforma UWP (Universal Windows Platform) (UWP) prima che la mappa venga visualizzata. Inoltre, in iOS, Android e UWP, per accedere alla posizione dell'utente sono necessarie le autorizzazioni di posizione per l'applicazione.

### <a name="ios"></a>iOS

Per la visualizzazione e l'interazione con una mappa in iOS non è necessaria alcuna configurazione aggiuntiva. Tuttavia, per accedere ai servizi di posizione, è necessario impostare le chiavi seguenti in **info. plist**:

- iOS 11 e versioni successive
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : per l'uso dei servizi di posizione quando l'applicazione è in uso
  - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) : per usare i servizi di posizione in qualsiasi momento
- iOS 10 e versioni precedenti
  - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) : per l'uso dei servizi di posizione quando l'applicazione è in uso
  - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) : per usare i servizi di posizione in qualsiasi momento    

Per supportare iOS 11 e versioni precedenti, è possibile includere tutte e tre le chiavi: `NSLocationWhenInUseUsageDescription`, `NSLocationAlwaysAndWhenInUseUsageDescription` e `NSLocationAlwaysUsageDescription`.

Di seguito è riportata la rappresentazione XML per queste chiavi in **info. plist** . È necessario aggiornare i valori di `string` per riflettere il modo in cui l'applicazione usa le informazioni sul percorso:

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your application is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

Le voci **info. plist** possono essere aggiunte anche nella visualizzazione **origine** durante la modifica del file **info. plist** :

![INFO. plist per iOS 8](setup-images/ios8-map-permissions.png "iOS 8 richieste Info. plist voci")

Viene visualizzato un messaggio di richiesta quando l'applicazione tenta di accedere alla posizione dell'utente, richiedendo l'accesso:

[![Screenshot della richiesta di autorizzazione location in iOS](setup-images/permission-ios.png "richiesta di autorizzazione iOS")](setup-images/permission-ios-large.png#lightbox "richiesta di autorizzazione iOS")

### <a name="android"></a>Android

Il processo di configurazione per la visualizzazione e l'interazione con una mappa in Android è il seguente:

1. Ottenere una chiave API di Google Maps e aggiungerla al manifesto.
1. Specificare il numero di versione dei servizi Google Play nel manifesto.
1. Specificare il requisito per Apache HTTP legacy Library nel manifesto.
1. opzionale Specificare l'autorizzazione WRITE_EXTERNAL_STORAGE nel manifesto.
1. opzionale Specificare le autorizzazioni per il percorso nel manifesto.
1. opzionale Richiedere le autorizzazioni per il percorso di runtime nella classe `MainActivity`.

Per un esempio di file manifesto configurato correttamente, vedere [file AndroidManifest. XML](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/WorkingWithMaps.Android/Properties/AndroidManifest.xml) dall'applicazione di esempio.

#### <a name="get-a-google-maps-api-key"></a>Ottenere una chiave API di Google Maps

Per usare l' [API Google Maps](https://developers.google.com/maps/documentation/android/) in Android, è necessario generare una chiave API. A tale scopo, seguire le istruzioni riportate in [ottenere una chiave API di Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

Una volta ottenuta una chiave API, è necessario aggiungerla all'interno dell'elemento `<application>` del file **Properties/file AndroidManifest. XML** :

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="PASTE-YOUR-API-KEY-HERE" />
</application>
```

Questa operazione incorpora la chiave API nel manifesto. Senza una chiave API valida, il controllo [`Map`](xref:Xamarin.Forms.Maps.Map) visualizzerà una griglia vuota.

> [!NOTE]
> Si noti che, per consentire all'APK di accedere a Google Maps, è necessario includere le impronte digitali SHA-1 e i nomi di pacchetto per ogni archivio chiavi (debug e versione) usati per firmare l'APK. Ad esempio, se si usa un computer per il debug e un altro computer per la generazione del file APK della versione, è necessario includere l'impronta digitale del certificato SHA-1 dall'archivio chiavi di debug del primo computer e l'impronta digitale del certificato SHA-1 dal keystore versione di secondo computer. Ricordarsi anche di modificare le credenziali chiave se il **nome del pacchetto** dell'app viene modificato. Vedere [ottenere una chiave API di Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md).

#### <a name="specify-the-google-play-services-version-number"></a>Specificare il numero di versione dei servizi Google Play

Aggiungere la dichiarazione seguente all'interno dell'elemento `<application>` di **file AndroidManifest. XML**:

```xml
<meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
```

Questa operazione incorpora la versione di Google Play servizi con cui l'applicazione è stata compilata nel manifesto.

#### <a name="specify-the-requirement-for-the-apache-http-legacy-library"></a>Specificare il requisito per la libreria legacy Apache HTTP

Se l'applicazione Xamarin.Forms è destinata all'API 28 o successiva, è necessario aggiungere la dichiarazione seguente all'interno dell'elemento `<application>` di **file AndroidManifest. XML**:

```xml
<uses-library android:name="org.apache.http.legacy" android:required="false" />    
```

Ciò indica all'applicazione di usare la libreria client Apache HTTP, che è stata rimossa dal `bootclasspath` in Android 9.

#### <a name="specify-the-write_external_storage-permission"></a>Specificare l'autorizzazione WRITE_EXTERNAL_STORAGE

Se l'applicazione è destinata all'API 22 o inferiore, potrebbe essere necessario aggiungere l'autorizzazione `WRITE_EXTERNAL_STORAGE` al manifesto, come figlio dell'elemento `<manifest>`:

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

Questa operazione non è necessaria se l'applicazione è destinata all'API 23 o successiva.

#### <a name="specify-location-permissions"></a>Specificare le autorizzazioni per il percorso

Se l'applicazione deve accedere alla posizione dell'utente, è necessario richiedere l'autorizzazione aggiungendo il `ACCESS_COARSE_LOCATION` o `ACCESS_FINE_LOCATION` le autorizzazioni per il manifesto (o entrambi) come figlio dell'elemento `<manifest>`:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.myapp">
  ...
  <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
  <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
</manifest>
```

L'autorizzazione `ACCESS_COARSE_LOCATION` consente all'API di usare i dati per dispositivi mobili o Wi-Fi, o entrambi, per determinare la posizione del dispositivo. Le autorizzazioni `ACCESS_FINE_LOCATION` consentono all'API di usare i dati di Global Positioning System (GPS), Wi-Fi o mobile per determinare come possibile un percorso preciso.

In alternativa, è possibile abilitare queste autorizzazioni utilizzando l'editor manifesto per aggiungere le autorizzazioni seguenti:

- `AccessCoarseLocation`
- `AccessFineLocation`

Questi vengono visualizzati nella schermata seguente:

![Autorizzazioni necessarie per Android](setup-images/android-map-permissions.png "Autorizzazioni necessarie per Android")

#### <a name="request-runtime-location-permissions"></a>Richiedere le autorizzazioni per il percorso di runtime

Se l'applicazione è destinata all'API 23 o versione successiva e deve accedere alla posizione dell'utente, deve verificare se dispone dell'autorizzazione necessaria in fase di esecuzione e richiederla se non è presente. Per ottenere questo risultato, è possibile procedere come segue:

1. Nella classe `MainActivity` aggiungere i campi seguenti:

    ```csharp
    const int RequestLocationId = 0;

    readonly string[] LocationPermissions =
    {
        Manifest.Permission.AccessCoarseLocation,
        Manifest.Permission.AccessFineLocation
    };
    ```

1. Nella classe `MainActivity` aggiungere la seguente `OnStart` override:

    ```csharp
    protected override void OnStart()
    {
        base.OnStart();

        if ((int)Build.VERSION.SdkInt >= 23)
        {
            if (CheckSelfPermission(Manifest.Permission.AccessFineLocation) != Permission.Granted)
            {
                RequestPermissions(LocationPermissions, RequestLocationId);
            }
            else
            {
                // Permissions already granted - display a message.
            }
        }
    }
    ```

    Se l'applicazione è destinata all'API 23 o successiva, questo codice esegue un controllo delle autorizzazioni di runtime per l'autorizzazione `AccessFineLocation`. Se non è stata concessa l'autorizzazione, viene eseguita una richiesta di autorizzazione chiamando il metodo `RequestPermissions`.

1. Nella classe `MainActivity` aggiungere la seguente `OnRequestPermissionsResult` override:

    ```csharp
    public override void OnRequestPermissionsResult(int requestCode, string[] permissions, [GeneratedEnum] Permission[] grantResults)
    {
        if (requestCode == RequestLocationId)
        {
            if ((grantResults.Length == 1) && (grantResults[0] == (int)Permission.Granted))
                // Permissions granted - display a message.
            else
                // Permissions denied - display a message.
        }
        else
        {
            base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
        }
    }
    ```

    Questo override gestisce il risultato della richiesta di autorizzazione.

L'effetto generale di questo codice è che, quando l'applicazione richiede la posizione dell'utente, viene visualizzata la finestra di dialogo seguente che richiede l'autorizzazione:

[![Screenshot della richiesta di autorizzazione location in Android](setup-images/permission-android.png "Richiesta di autorizzazione Android")](setup-images/permission-android-large.png#lightbox "Richiesta di autorizzazione Android")

### <a name="universal-windows-platform"></a>Piattaforma UWP (Universal Windows Platform)

In UWP, è necessario autenticare l'applicazione prima di poter visualizzare una mappa e utilizzare i servizi di mapping. Per autenticare l'applicazione, è necessario specificare una chiave di autenticazione maps. Per altre informazioni, vedere [Request a Maps Authentication Key](/windows/uwp/maps-and-location/authentication-key). Il token di autenticazione deve quindi essere specificato nella chiamata al metodo `FormsMaps.Init("AUTHORIZATION_TOKEN")` per autenticare l'applicazione con Bing Maps.

Inoltre, se l'applicazione deve accedere alla posizione dell'utente, è necessario abilitare la funzionalità del percorso nel manifesto del pacchetto. Per ottenere questo risultato, è possibile procedere come segue:

1. In **Esplora soluzioni**fare doppio clic su **Package. appxmanifest** e selezionare la scheda **funzionalità** .
1. Nell'elenco delle **funzionalità** selezionare la casella **percorso**. Questa operazione consente di aggiungere la funzionalità del dispositivo `location` al file manifesto del pacchetto.

    ```xml
    <Capabilities>
      <!-- DeviceCapability elements must follow Capability elements (if present) -->
      <DeviceCapability Name="location"/>
    </Capabilities>
    ```

#### <a name="release-builds"></a>Build di rilascio

Le build di rilascio di UWP usano la compilazione .NET native per compilare l'applicazione direttamente nel codice nativo. Tuttavia, una conseguenza è che il renderer per il controllo [`Map`](xref:Xamarin.Forms.Maps.Map) in UWP può essere collegato al di fuori dell'eseguibile. Questo problema può essere risolto usando un overload specifico di UWP del metodo `Forms.Init` in **app.XAML.cs**:

```csharp
var assembliesToInclude = new [] { typeof(Xamarin.Forms.Maps.UWP.MapRenderer).GetTypeInfo().Assembly };
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
```

Questo codice passa l'assembly in cui risiede la classe `Xamarin.Forms.Maps.UWP.MapRenderer` al metodo `Forms.Init`. In questo modo si garantisce che l'assembly non sia collegato all'eseguibile dal processo di compilazione .NET native.

> [!IMPORTANT]
> In caso contrario, il controllo [`Map`](xref:Xamarin.Forms.Maps.Map) non verrà visualizzato durante l'esecuzione di una build di rilascio.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio di Maps](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [Pin di Xamarin.Forms. Maps](~/xamarin-forms/user-interface/map/pins.md).
- [API Maps](xref:Xamarin.Forms.Maps)
- [Renderer personalizzato mappa](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
