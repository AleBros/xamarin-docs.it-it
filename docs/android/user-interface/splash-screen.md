---
title: Schermata iniziale
description: L'avvio di un'app Android richiede tempo, soprattutto quando l'app viene avviata per la prima volta in un dispositivo. Una schermata iniziale può visualizzare lo stato di avanzamento dell'avvio per l'utente o per indicare la personalizzazione.
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/02/2019
ms.openlocfilehash: c8363adf479f0880bfbdf6a047d495da4b849395
ms.sourcegitcommit: a2ec4aef8457eab4310cde2a41485931263ce16b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74902254"
---
# <a name="splash-screen"></a>Schermata iniziale

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen)

_L'avvio di un'app Android richiede tempo, soprattutto quando l'app viene avviata per la prima volta in un dispositivo. Una schermata iniziale può visualizzare lo stato di avanzamento dell'avvio per l'utente o per indicare la personalizzazione._

## <a name="overview"></a>Panoramica di

Un'app Android impiega un po' di tempo per l'avvio, specialmente durante la prima esecuzione dell'app in un dispositivo (a volte viene definita _avvio a freddo_). La schermata iniziale può visualizzare lo stato di avanzamento dell'avvio per l'utente oppure può visualizzare informazioni sulla personalizzazione per identificare e promuovere l'applicazione.

Questa guida illustra una tecnica per implementare una schermata iniziale in un'applicazione Android. Vengono illustrati i passaggi seguenti:

1. Creazione di una risorsa disegnatore per la schermata iniziale.

2. Definizione di un nuovo tema che visualizzerà la risorsa che può essere disegnato.

3. Aggiunta di una nuova attività all'applicazione che verrà usata come schermata iniziale definita dal tema creato nel passaggio precedente.

[schermata iniziale del logo Xamarin di esempio ![seguito dalla schermata dell'app](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)

## <a name="requirements"></a>Requisiti di

Questa guida presuppone che l'applicazione abbia come destinazione l'API Android livello 21 o versione successiva. L'applicazione deve avere anche i pacchetti NuGet **Xamarin.Android. support. v4** e **Xamarin.Android. support. V7. AppCompat** aggiunti al progetto.

Tutto il codice e il codice XML in questa guida sono disponibili nel progetto di esempio [SplashScreen](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen) per questa guida.

## <a name="implementing-a-splash-screen"></a>Implementazione di una schermata iniziale

Il modo più rapido per eseguire il rendering e la visualizzazione della schermata iniziale consiste nel creare un tema personalizzato e applicarlo a un'attività che espone la schermata iniziale. Quando l'attività viene sottoposta a rendering, carica il tema e applica la risorsa disegnabile (a cui fa riferimento il tema) allo sfondo dell'attività. Questo approccio evita la necessità di creare un file di layout.

La schermata iniziale viene implementata come un'attività che Visualizza l'oggetto disegnato, esegue tutte le inizializzazioni e avvia tutte le attività. Una volta che l'app è stata avviata, l'attività schermata iniziale avvia l'attività principale e viene rimossa dallo stack di backup dell'applicazione.

### <a name="creating-a-drawable-for-the-splash-screen"></a>Creazione di un oggetto disegnato per la schermata iniziale

Nella schermata iniziale viene visualizzato un XML disegnatore in background dell'attività della schermata iniziale. È necessario usare un'immagine bitmap, ad esempio un PNG o JPG, per l'immagine da visualizzare.

L'applicazione di esempio definisce un oggetto disegnato denominato **splash_screen. XML**. Questo oggetto disegnatore utilizza un [elenco di livelli](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) per centrare l'immagine della schermata iniziale nell'applicazione, come illustrato nel codice XML seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item>
    <color android:color="@color/splash_background"/>
  </item>
  <item>
    <bitmap
        android:src="@drawable/splash_logo"
        android:tileMode="disabled"
        android:gravity="center"/>
  </item>
</layer-list>
```

Questo `layer-list` centra l'immagine iniziale su un colore di sfondo specificato dalla risorsa `@color/splash_background`. L'applicazione di esempio definisce questo colore nel file **Resources/values. XML** :

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
  ...
  <color name="splash_background">#FFFFFF</color>
</resources>
```

Per ulteriori informazioni sugli oggetti di `Drawable`, vedere la [documentazione di Google su Android](https://developer.android.com/reference/android/graphics/drawable/Drawable).

### <a name="implementing-a-theme"></a>Implementazione di un tema

Per creare un tema personalizzato per l'attività della schermata iniziale, modificare (o aggiungere) il file **values/styles. XML** e creare un nuovo elemento `style` per la schermata iniziale. Di seguito è riportato un file Sample **values/Style. XML** con un `style` denominato myfilee **. Splash**:

```xml
<resources>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
  </style>

  <style name="MyTheme" parent="MyTheme.Base">
  </style>

  <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
    <item name="android:windowBackground">@drawable/splash_screen</item>
    <item name="android:windowNoTitle">true</item>  
    <item name="android:windowFullscreen">true</item>  
    <item name="android:windowContentOverlay">@null</item>  
    <item name="android:windowActionBar">true</item>  
  </style>
</resources>
```

My **. Splash** è molto spartano &ndash; dichiara lo sfondo della finestra, rimuove in modo esplicito la barra del titolo dalla finestra e dichiara che è a schermo intero. Se si vuole creare una schermata iniziale che emula l'interfaccia utente dell'app prima che l'attività ingrandisca il primo layout, è possibile usare `windowContentOverlay` anziché `windowBackground` nella definizione dello stile. In tal caso, è necessario modificare anche l'oggetto **splash_screen. XML** disegnatore, in modo che venga visualizzata un'emulazione dell'interfaccia utente.

### <a name="create-a-splash-activity"></a>Creare un'attività Splash

A questo punto è necessaria una nuova attività per Android per avviare che include l'immagine iniziale ed esegue tutte le attività di avvio. Il codice seguente è un esempio di implementazione di una schermata iniziale completa:

```csharp
[Activity(Theme = "@style/MyTheme.Splash", MainLauncher = true, NoHistory = true)]
public class SplashActivity : AppCompatActivity
{
    static readonly string TAG = "X:" + typeof(SplashActivity).Name;

    public override void OnCreate(Bundle savedInstanceState, PersistableBundle persistentState)
    {
        base.OnCreate(savedInstanceState, persistentState);
        Log.Debug(TAG, "SplashActivity.OnCreate");
    }

    // Launches the startup task
    protected override void OnResume()
    {
        base.OnResume();
        Task startupWork = new Task(() => { SimulateStartup(); });
        startupWork.Start();
    }

    // Simulates background work that happens behind the splash screen
    async void SimulateStartup ()
    {
        Log.Debug(TAG, "Performing some startup work that takes a bit of time.");
        await Task.Delay (8000); // Simulate a bit of startup work.
        Log.Debug(TAG, "Startup work is finished - starting MainActivity.");
        StartActivity(new Intent(Application.Context, typeof (MainActivity)));
    }
}
```

`SplashActivity` USA in modo esplicito il tema creato nella sezione precedente, eseguendo l'override del tema predefinito dell'applicazione.
Non è necessario caricare un layout in `OnCreate` perché il tema dichiara un oggetto disegnatore come sfondo.

It is important to set the `NoHistory=true` attribute so that the Activity is removed from the back stack. To prevent the back button from canceling the startup process, you can also override `OnBackPressed` and have it do nothing:

```csharp
public override void OnBackPressed() { }
```

The startup work is performed asynchronously in `OnResume`. This is necessary so that the startup work does not slow down or delay the appearance of the launch screen. When the work has completed, `SplashActivity` will launch `MainActivity` and the user may begin interacting with the app.

This new `SplashActivity` is set as the launcher activity for the application by setting the `MainLauncher` attribute to `true`. Because `SplashActivity` is now the launcher activity, you must edit `MainActivity.cs`, and remove the `MainLauncher` attribute from `MainActivity`:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>Landscape Mode

The splash screen implemented in the previous steps will display correctly in both portrait and landscape mode. However, in some cases it is necessary to have separate splash screens for portrait and landscape modes (for example, if the splash image is full-screen).

To add a splash screen for landscape mode, use the following steps:

1. In the **Resources/drawable** folder, add the landscape version of the splash screen image you want to use. In this example, **splash_logo_land.png** is the landscape version of the logo that was used in the above examples (it uses white lettering instead of blue).

2. In the **Resources/drawable** folder, create a landscape version of the `layer-list` drawable that was defined earlier (for example, **splash_screen_land.xml**). In this file, set the bitmap path to the landscape version of the splash screen image. In the following example, **splash_screen_land.xml** uses **splash_logo_land.png**:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
      <item>
        <color android:color="@color/splash_background"/>
      </item>
      <item>
        <bitmap
            android:src="@drawable/splash_logo_land"
            android:tileMode="disabled"
            android:gravity="center"/>
      </item>
    </layer-list>
    ```

3. Create the **Resources/values-land** folder if it doesn't already  exist.

4. Add the files **colors.xml** and **style.xml** to **values-land** (these can be copied and modified from the existing  **values/colors.xml** and **values/style.xml** files).

5. Modify **values-land/style.xml** so that it uses the landscape version of the drawable for `windowBackground`. In this example, **splash_screen_land.xml** is used:

    ```xml
    <resources>
      <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
      </style>
        <style name="MyTheme" parent="MyTheme.Base">
      </style>
      <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
        <item name="android:windowBackground">@drawable/splash_screen_land</item>
        <item name="android:windowNoTitle">true</item>  
        <item name="android:windowFullscreen">true</item>  
        <item name="android:windowContentOverlay">@null</item>  
        <item name="android:windowActionBar">true</item>  
      </style>
    </resources>
    ```

6. Modify **values-land/colors.xml** to configure the colors you want to use for the landscape version of the splash screen. In this example, the splash background color is changed to blue for landscape mode:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <resources>
      <color name="primary">#2196F3</color>
      <color name="primaryDark">#1976D2</color>
      <color name="accent">#FFC107</color>
      <color name="window_background">#F5F5F5</color>
      <color name="splash_background">#3498DB</color>
    </resources>
    ```

7. Build and run the app again. Rotate the device to landscape mode while the splash screen is still displayed. The splash screen changes to the landscape version:

    [![Rotation of splash screen to landscape mode](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)

Note that the use of a landscape-mode splash screen does not always provide a seamless experience. By default, Android launches the app in portrait mode and transitions it to landscape mode even if the device is already in landscape mode. As a result, if the app is launched while the device is in landscape mode, the device briefly presents the portrait splash screen and then animates rotation from the portrait to the landscape splash screen. Unfortunately, this initial portrait-to-landscape transition takes place even when `ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape` is specified in the splash Activity's flags. The best way to work around this limitation is to create a single splash screen image that renders correctly in both portrait and landscape modes.

## <a name="summary"></a>Riepilogo

This guide discussed one way to implement a splash screen in a Xamarin.Android application; namely, applying a custom theme to the launch activity.

## <a name="related-links"></a>Collegamenti correlati

- [SplashScreen (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen)
- [layer-list Drawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [Material Design Patterns - Launch Screens](https://material.io/design/communication/launch-screen.html#usage)
