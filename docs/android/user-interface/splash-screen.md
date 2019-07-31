---
title: Schermata iniziale
description: L'avvio di un'app Android richiede tempo, soprattutto quando l'app viene avviata per la prima volta in un dispositivo. Una schermata iniziale può visualizzare lo stato di avanzamento dell'avvio per l'utente o per indicare la personalizzazione.
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/06/2018
ms.openlocfilehash: 30e471174d30e58b126c174fd4eacfdbe87d97b0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646053"
---
# <a name="splash-screen"></a>Schermata iniziale

_L'avvio di un'app Android richiede tempo, soprattutto quando l'app viene avviata per la prima volta in un dispositivo. Una schermata iniziale può visualizzare lo stato di avanzamento dell'avvio per l'utente o per indicare la personalizzazione._


## <a name="overview"></a>Panoramica

Un'app Android impiega un po' di tempo per l'avvio, specialmente durante la prima esecuzione dell'app in un dispositivo (a volte viene definita _avvio a freddo_). La schermata iniziale può visualizzare lo stato di avanzamento dell'avvio per l'utente oppure può visualizzare informazioni sulla personalizzazione per identificare e promuovere l'applicazione.

Questa guida illustra una tecnica per implementare una schermata iniziale in un'applicazione Android. Vengono illustrati i passaggi seguenti:

1.  Creazione di una risorsa disegnatore per la schermata iniziale.

2.  Definizione di un nuovo tema che visualizzerà la risorsa che può essere disegnato.

3.  Aggiunta di una nuova attività all'applicazione che verrà usata come schermata iniziale definita dal tema creato nel passaggio precedente.

[![Schermata iniziale del logo Novell di esempio seguita dalla schermata dell'app](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)


## <a name="requirements"></a>Requisiti

Questa guida presuppone che l'applicazione sia destinata a Android API level 15 (Android 4.0.3) o versione successiva. L'applicazione deve avere anche i pacchetti NuGet **Novell. Android. support. v4** e **Novell. Android. support. V7. AppCompat** aggiunti al progetto.

Tutto il codice e il codice XML in questa guida sono disponibili nel progetto di esempio [SplashScreen](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen) per questa guida.


## <a name="implementing-a-splash-screen"></a>Implementazione di una schermata iniziale

Il modo più rapido per eseguire il rendering e la visualizzazione della schermata iniziale consiste nel creare un tema personalizzato e applicarlo a un'attività che espone la schermata iniziale. Quando l'attività viene sottoposta a rendering, carica il tema e applica la risorsa disegnabile (a cui fa riferimento il tema) allo sfondo dell'attività. Questo approccio evita la necessità di creare un file di layout.

La schermata iniziale viene implementata come un'attività che Visualizza l'oggetto disegnato, esegue tutte le inizializzazioni e avvia tutte le attività. Una volta che l'app è stata avviata, l'attività schermata iniziale avvia l'attività principale e viene rimossa dallo stack di backup dell'applicazione.


### <a name="creating-a-drawable-for-the-splash-screen"></a>Creazione di un oggetto disegnato per la schermata iniziale

Nella schermata iniziale viene visualizzato un XML disegnatore in background dell'attività della schermata iniziale. È necessario usare un'immagine bitmap, ad esempio un PNG o JPG, per l'immagine da visualizzare.

In questa guida viene usato un [elenco di livelli](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) per centrare l'immagine della schermata iniziale nell'applicazione. Il frammento di codice seguente è un `drawable` esempio di risorsa `layer-list`che usa:

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

Questa `layer-list` operazione concentrerà l'immagine iniziale **Splash. png** sullo `@color/splash_background` sfondo specificato dalla risorsa. Inserire questo file XML nella cartella **Resources/** di cui è stato disegnato (ad esempio, Resources/ **splash_screen. XML**).

Al termine della creazione della schermata iniziale, il passaggio successivo consiste nel creare un tema per la schermata iniziale.


### <a name="implementing-a-theme"></a>Implementazione di un tema

Per creare un tema personalizzato per l'attività della schermata iniziale, modificare o aggiungere il file **values/styles. XML** e creare un `style` nuovo elemento per la schermata iniziale. Di seguito è riportato un file di esempio **values/Style. XML** con un `style` nome **. Splash**:

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

My **. Splash** è molto spartano &ndash; dichiara lo sfondo della finestra, rimuove in modo esplicito la barra del titolo dalla finestra e dichiara che è a schermo intero. Se si vuole creare una schermata iniziale che emula l'interfaccia utente dell'app prima che l'attività ingrandisca il primo layout, è possibile usare `windowContentOverlay` `windowBackground` anziché nella definizione dello stile. In questo caso, è necessario modificare anche **splash_screen. XML** disegnatore in modo da visualizzare un'emulazione dell'interfaccia utente.


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

`SplashActivity`USA in modo esplicito il tema creato nella sezione precedente, eseguendo l'override del tema predefinito dell'applicazione.
Non è necessario caricare un layout in `OnCreate` perché il tema dichiara un oggetto disegnatore come sfondo.

È importante impostare l' `NoHistory=true` attributo in modo che l'attività venga rimossa dallo stack indietro. Per impedire al pulsante indietro di annullare il processo di avvio, è anche possibile eseguire `OnBackPressed` l'override di e non eseguire alcuna operazione:

```csharp
public override void OnBackPressed() { }
```

Il lavoro di avvio viene eseguito in modo `OnResume`asincrono in. Questa operazione è necessaria in modo che il lavoro di avvio non rallenti o ritardi l'aspetto della schermata di avvio. Quando il lavoro è stato completato `SplashActivity` , viene `MainActivity` avviato e l'utente può iniziare a interagire con l'app.

Questo nuovo `SplashActivity` viene impostato come attività dell'utilità di avvio per l'applicazione impostando l' `true` `MainLauncher` attributo su. Poiché `SplashActivity` è ora l'attività di avvio, è necessario `MainActivity.cs`modificare e rimuovere l' `MainLauncher` attributo da `MainActivity`:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>Modalità orizzontale

La schermata iniziale implementata nei passaggi precedenti viene visualizzata correttamente in modalità verticale e orizzontale. Tuttavia, in alcuni casi è necessario avere schermate iniziali separate per le modalità verticale e orizzontale, ad esempio se l'immagine iniziale è a schermo intero.

Per aggiungere una schermata iniziale per la modalità orizzontale, attenersi alla procedura seguente:

1. Nella cartella **risorse/disegnatore** aggiungere la versione orizzontale dell'immagine della schermata iniziale che si vuole usare. In questo esempio, **splash_logo_land. png** è la versione orizzontale del logo usato negli esempi precedenti, che usa la lettera bianca anziché il blu.

2. Nella cartella **risorse/disegnatore** creare una versione orizzontale dell'oggetto `layer-list` disegnatore definito in precedenza (ad esempio, **splash_screen_land. XML**). In questo file, impostare il percorso bitmap della versione orizzontale dell'immagine della schermata iniziale. Nell'esempio seguente **splash_screen_land. XML** USA **splash_logo_land. png**:

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

3.  Creare la cartella Resources **/values-Land** se non esiste già.

4.  Aggiungere i file **Colors. XML** e **Style. XML** a **values-Land** , che possono essere copiati e modificati dai file **values/Colors. XML** e **values/Style. XML** esistenti.

5.  Modificare **values-Land/Style. XML** in modo che usi la versione orizzontale dell'oggetto disegnabile per `windowBackground`. In questo esempio viene usato **splash_screen_land. XML** :

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

6.  Modificare **values-Land/Colors. XML** per configurare i colori che si desidera utilizzare per la versione orizzontale della schermata iniziale. In questo esempio, il colore di sfondo iniziale viene modificato in blu per la modalità orizzontale:

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

7.  Compilare ed eseguire nuovamente l'app. Ruotare il dispositivo in modalità orizzontale mentre è ancora visualizzata la schermata iniziale. La schermata iniziale passa alla versione orizzontale:

    [![Rotazione della schermata iniziale in modalità orizzontale](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)


Si noti che l'uso di una schermata iniziale in modalità orizzontale non fornisce sempre un'esperienza semplice. Per impostazione predefinita, Android avvia l'app in modalità verticale e la passa alla modalità orizzontale anche se il dispositivo è già in modalità orizzontale. Di conseguenza, se l'app viene avviata mentre il dispositivo è in modalità orizzontale, il dispositivo presenta brevemente la schermata iniziale verticale e quindi aggiunge un'animazione alla rotazione dal ritratto alla schermata iniziale orizzontale. Sfortunatamente, questa transizione da verticale a orizzontale viene effettuata anche quando `ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape` viene specificato nei flag dell'attività Splash. Il modo migliore per aggirare questa limitazione consiste nel creare una singola immagine della schermata iniziale che esegue correttamente il rendering in modalità verticale e orizzontale.


## <a name="summary"></a>Riepilogo

Questa guida ha illustrato un modo per implementare una schermata iniziale in un'applicazione Novell. Android; in particolare, applicare un tema personalizzato all'attività di avvio.


## <a name="related-links"></a>Collegamenti correlati

- [SplashScreen (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen)
- [elenco di livelli disegnato](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [Modelli di progettazione materiale-schermate di avvio](https://material.io/design/communication/launch-screen.html#usage)
