---
title: Schermata iniziale
description: Un'app per Android richiede tempo per l'avvio, soprattutto quando l'app viene avviata prima di tutto in un dispositivo. La schermata iniziale potrebbe visualizzare start up lo stato di avanzamento all'utente o per indicare informazioni personalizzate distintive del.
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/06/2018
ms.openlocfilehash: 1a9856a5b6257c82b2072e9eb44be481373abfcf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61308212"
---
# <a name="splash-screen"></a>Schermata iniziale

_Un'app per Android richiede tempo per l'avvio, soprattutto quando l'app viene avviata prima di tutto in un dispositivo. La schermata iniziale potrebbe visualizzare start up lo stato di avanzamento all'utente o per indicare informazioni personalizzate distintive del._


## <a name="overview"></a>Panoramica

Un'app per Android richiede del tempo per l'avvio, soprattutto durante la prima volta che l'app viene eseguita in un dispositivo (ciò è talvolta detta un _avvio a freddo_). Può visualizzare la schermata iniziale di avvio lo stato di avanzamento all'utente, o è possibile visualizzare le informazioni di personalizzazione per identificare e promuovere l'applicazione.

Questa guida illustra una tecnica per implementare una schermata in un'applicazione Android. Viene descritto come la procedura seguente:

1.  Creazione di una risorsa drawable per la schermata iniziale.

2.  Definizione di un nuovo tema che visualizzerà le risorse drawable.

3.  Aggiunta di una nuova attività per l'applicazione che verrà usata come schermata iniziale definita dal tema creato nel passaggio precedente.

[![Schermata di esempio Xamarin logo iniziale seguita da una schermata dell'app](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)


## <a name="requirements"></a>Requisiti

Questa guida si presuppone che l'applicazione è destinata a livello API Android 15 (Android 4.0.3) o versione successiva. L'applicazione deve avere anche il **Xamarin.Android.Support.v4** e **Xamarin.Android.Support.v7.AppCompat** i pacchetti NuGet aggiunti al progetto.

Tutto il codice e XML in questa Guida, vedere la [SplashScreen](https://developer.xamarin.com/samples/monodroid/SplashScreen) progetto di esempio per questa Guida.


## <a name="implementing-a-splash-screen"></a>Implementazione di una schermata iniziale

Il modo più rapido per eseguire il rendering e visualizzare la schermata iniziale consiste nel creare un tema personalizzato e applicarlo a un'attività che mostra la schermata iniziale. Quando l'attività viene eseguito il rendering, carica il tema e applica le risorse drawable (fa riferimento il tema) per lo sfondo dell'attività. Questo approccio evita la necessità per la creazione di un file di layout.

La schermata iniziale viene implementata come un'attività che consente di visualizzare il marchio drawable, esegue le inizializzazioni e avvia tutte le attività. Una volta che l'app ha avviato automaticamente, la schermata iniziale di attività viene avviata l'attività principale e viene rimosso dallo stack indietro dell'applicazione.


### <a name="creating-a-drawable-for-the-splash-screen"></a>Creazione di un Drawable per la schermata iniziale

La schermata verrà visualizzato un file XML drawable dello sfondo della schermata iniziale di attività. È necessario usare un'immagine bitmap (ad esempio PNG o JPG) per l'immagine da visualizzare.

In questa Guida, si usa un' [elenco dei livelli](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) per allineare al centro l'immagine della schermata iniziale dell'applicazione. Il frammento seguente è riportato un esempio di un `drawable` risorsa usando un `layer-list`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item>
    <color android:color="@color/splash_background"/>
  </item>
  <item>
    <bitmap
        android:src="@drawable/splash"
        android:tileMode="disabled"
        android:gravity="center"/>
  </item>
</layer-list>
```

Ciò `layer-list` verrà allineare al centro l'immagine della schermata iniziale **Splash** sullo sfondo specificato da di `@color/splash_background` risorsa. Inserire questo file XML nel **risorse/drawable** cartella (ad esempio, **Resources/drawable/splash_screen.xml**).

Dopo aver creata la schermata iniziale drawable, il passaggio successivo consiste nel creare un tema per la schermata iniziale.


### <a name="implementing-a-theme"></a>Implementazione di un tema

Per creare un tema personalizzato per la schermata iniziale di attività, modifica (o aggiungere) il file **values/styles.xml** e creare un nuovo `style` (elemento) per la schermata iniziale. Un campione **values/style.xml** di seguito è riportato file con un `style` denominato **MyTheme.Splash**:

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
  </style>
</resources>
```

**MyTheme.Splash** è molto spartan &ndash; dichiara lo sfondo della finestra, in modo esplicito la barra del titolo verrà rimossa dalla finestra e dichiara di essere a schermo intero. Se si desidera creare una schermata che emula l'interfaccia utente dell'app prima che l'attività incrementa il layout del primo, è possibile usare `windowContentOverlay` anziché `windowBackground` nella definizione di stile. In questo caso, è necessario modificare anche il **splash_screen.xml** drawable affinché visualizzi un'emulazione dell'interfaccia utente.


### <a name="create-a-splash-activity"></a>Creare un'attività iniziale

A questo punto è necessario una nuova attività per Android con l'immagine iniziale che consente di eseguire eventuali attività di avvio avviare. Il codice riportato di seguito è riportato un esempio di un'implementazione di schermata iniziale completa:

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

`SplashActivity` utilizza in modo esplicito il tema è stato creato nella sezione precedente, viene sottoposto a override il tema predefinito dell'applicazione.
Non è necessario caricare un layout in `OnCreate` come il tema viene dichiarato un drawable come sfondo.

È importante impostare il `NoHistory=true` attributo in modo che l'attività viene rimossa dallo stack indietro. Per evitare che il pulsante indietro l'annullamento del processo di avvio, è anche possibile eseguire l'override `OnBackPressed` ed eseguirlo nulla:

```csharp
public override void OnBackPressed() { }
```

Le operazioni di avvio viene eseguita in modo asincrono in `OnResume`. Ciò è necessario in modo che le operazioni di avvio non rallentare o ritardare l'aspetto della schermata di avvio. Al termine il lavoro, `SplashActivity` avvierà `MainActivity` e l'utente possa iniziare l'interazione con l'app.

Questa nuova `SplashActivity` viene impostato come l'attività dell'utilità di avvio per l'applicazione impostando il `MainLauncher` dell'attributo `true`. In quanto `SplashActivity` è ora l'attività dell'utilità di avvio, è necessario modificare `MainActivity.cs`e rimuovere le `MainLauncher` dell'attributo da `MainActivity`:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>Modalità orizzontale

La schermata iniziale implementata nei passaggi precedenti verrà visualizzati correttamente in modalità sia verticale e orizzontale. Tuttavia, in alcuni casi è necessario disporre di schermate iniziali distinti per modalità verticale e orizzontale (ad esempio, se l'immagine iniziale a schermo intero).

Per aggiungere una schermata iniziale per la modalità orizzontale, procedere come segue:

1. Nel **risorse/drawable** cartella, aggiungere la versione del panorama applicativo dell'immagine della schermata iniziale da usare. In questo esempio **splash_logo_land.png** è la versione del panorama applicativo del logo che è stato usato negli esempi precedenti (utilizza mancato recapito bianco anziché blu).

2. Nel **risorse/drawable** cartella, creare una versione panorama applicativo del `layer-list` drawable che è stata definita in precedenza (ad esempio, **splash_screen_land.xml**). In questo file, impostare il percorso di bitmap a quella orizzontale dell'immagine della schermata iniziale. Nell'esempio riportato di seguito **splash_screen_land.xml** Usa **splash_logo_land.png**:

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

3.  Creare il **risorse/valori-land** cartella se non esiste già.

4.  Aggiungere i file **Colors** e **Style** al **valori land** (questi può essere copiati e modificati da esistente **values/colors.xml**e **values/style.xml** file).

5.  Modificare **valori-land/Style** in modo che utilizzi la versione del panorama applicativo del drawable per `windowBackground`. In questo esempio **splash_screen_land.xml** viene usato:

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

6.  Modificare **valori-land/Colors** per configurare i colori da usare per la versione panorama della schermata iniziale. In questo esempio viene modificato il colore di sfondo della schermata iniziale in blu per la modalità orizzontale:

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

7.  Compilare ed eseguire nuovamente l'app. Ruota il dispositivo per quella orizzontale mentre viene ancora visualizzata la schermata iniziale. La schermata iniziale di modifiche alla versione di orizzontale:

    [![Rotazione della schermata iniziale in modalità orizzontale](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)


Si noti che l'utilizzo di una schermata iniziale di modalità orizzontale non fornisce sempre un'esperienza senza problemi. Per impostazione predefinita, Android avvia l'app in modalità verticale e le transizioni per quella orizzontale, anche se il dispositivo è già in modalità orizzontale. Di conseguenza, se l'app viene avviata mentre il dispositivo è in modalità orizzontale, il dispositivo presenta brevemente la schermata iniziale verticale e quindi aggiunge un'animazione di rotazione dal verticale per la schermata iniziale orizzontale. Sfortunatamente, questa transizione orientamento verticale a orizzontale iniziale viene eseguita anche quando `ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape` specificato nel flag dell'attività iniziale. Il modo migliore per risolvere questa limitazione consiste nel creare un'immagine della schermata iniziale singolo che esegue il rendering correttamente in modalità sia verticale e orizzontale.


## <a name="summary"></a>Riepilogo

Questa guida ha illustrato come implementare una schermata iniziale in un'applicazione xamarin. Android. vale a dire, applicare un tema personalizzato per l'attività di avvio.


## <a name="related-links"></a>Collegamenti correlati

- [SplashScreen (esempio)](https://developer.xamarin.com/samples/monodroid/SplashScreen)
- [Drawable Layer-list](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [ Modelli di progettazione dei materiali - delle schermate di avvio](https://material.io/design/communication/launch-screen.html#usage)
