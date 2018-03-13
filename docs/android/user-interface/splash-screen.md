---
title: Schermata iniziale
description: Un'app Android richiede del tempo di avvio, in particolare quando viene avviata l'app prima in un dispositivo. La schermata iniziale visualizzi inizio fino all'utente o per indicare di branding lo stato di avanzamento.
ms.topic: article
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 9f88899d390f7f268f1b2f435617dc952f9eb205
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="splash-screen"></a>Schermata iniziale

_Un'app Android richiede del tempo di avvio, in particolare quando viene avviata l'app prima in un dispositivo. La schermata iniziale visualizzi inizio fino all'utente o per indicare di branding lo stato di avanzamento._


## <a name="overview"></a>Panoramica

Un'app Android richiede del tempo per avviare, in particolare durante la prima volta che si esegue l'app in un dispositivo (talvolta ciò viene considerato un _avvio a freddo_). La schermata è possibile che venga visualizzato lo stato di avanzamento all'utente di avvio, o può visualizzare le informazioni di personalizzazione per identificare e alzare di livello applicazione.

Questa guida viene illustrata una tecnica per implementare una schermata in un'applicazione Android. Vengono illustrati i passaggi seguenti:

1.  Creazione di una risorsa drawable per la schermata iniziale.

2.  Definizione di un nuovo tema che consente di visualizzare la risorsa drawable.

3.  Aggiunta di una nuova attività all'applicazione che verrà utilizzato come schermata iniziale definita dal tema creato nel passaggio precedente.

[![Schermata di esempio Xamarin logo iniziale seguita da una schermata dell'app](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)


## <a name="requirements"></a>Requisiti

Questa guida si presuppone che l'applicazione è destinata API Android livello 15 (Android è 4.0.3) o versione successiva. L'applicazione deve inoltre disporre di **Xamarin.Android.Support.v4** e **Xamarin.Android.Support.v7.AppCompat** pacchetti NuGet aggiunti al progetto.

Tutto il codice e XML in questa guida sono disponibili nel [SplashScreen](https://developer.xamarin.com/samples/monodroid/SplashScreen) progetto di esempio di questa Guida.


## <a name="implementing-a-splash-screen"></a>Implementazione di una schermata iniziale

Il modo più rapido per eseguire il rendering e visualizzare la schermata iniziale consiste nel creare un tema personalizzato e applicarlo a un'attività che mostra la schermata iniziale. Quando l'attività viene eseguito il rendering, carica il tema e si applica la risorsa drawable (a cui fa riferimento il tema) per lo sfondo dell'attività. Questo approccio evita la necessità di creazione di un file di layout.

La schermata viene implementata come un'attività che consente di visualizzare il marchio drawable, esegue le inizializzazioni e tutte le attività di avvio. Dopo l'applicazione ha avviato automaticamente, la schermata iniziale di attività viene avviata l'attività principale e viene rimosso dallo stack indietro dell'applicazione.


### <a name="creating-a-drawable-for-the-splash-screen"></a>Creazione di un Drawable per la schermata iniziale

La schermata verrà visualizzato un XML drawable dello sfondo della schermata iniziale di attività. È necessario utilizzare un'immagine bitmap (ad esempio, PNG o JPG) per l'immagine da visualizzare.

In questa Guida, si usa un [elenco livello](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) al centro e l'immagine della schermata iniziale nell'applicazione. Nel frammento seguente è riportato un esempio di un `drawable` risorse utilizzando un `layer-list`:

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

Questo `layer-list` verrà centrare l'immagine della schermata iniziale **splash.png** su uno sfondo specificato da di `@color/splash_background` risorse.
Inserire il file nel **risorse/drawable** cartella (ad esempio, **Resources/drawable/splash_screen.xml**).

Dopo aver creata la schermata iniziale drawable, il passaggio successivo consiste nel creare un tema per la schermata iniziale.


### <a name="implementing-a-theme"></a>Implementazione di un tema

Per creare un tema personalizzato per la schermata di attività, modificare (o aggiungere) il file **values/styles.xml** e creare un nuovo `style` elemento per la schermata iniziale. Un esempio **values/style.xml** file è illustrato di seguito con un `style` denominato **MyTheme.Splash**:

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

**MyTheme.Splash** è molto spartan &ndash; dichiara lo sfondo della finestra, in modo esplicito la barra del titolo verrà rimossa dalla finestra e dichiara di essere a schermo intero. Se si desidera creare una schermata che emula l'interfaccia utente dell'app prima che l'attività ingrandisce il primo layout, è possibile utilizzare `windowContentOverlay` anziché `windowBackground` nella definizione di stile. In questo caso, è necessario modificare anche il **splash_screen.xml** drawable in modo che venga visualizzato un'emulazione dell'interfaccia utente.


### <a name="create-a-splash-activity"></a>Creare un'attività iniziale

Ora abbiamo bisogno di una nuova attività per Android da avviare con l'immagine iniziale che esegue qualsiasi attività di avvio. Il codice riportato di seguito è riportato un esempio di un'implementazione della schermata iniziale completa:

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

`SplashActivity` utilizza in modo esplicito il tema è stato creato nella sezione precedente, si esegue l'override il tema predefinito dell'applicazione.
Non è necessario caricare un layout in `OnCreate` come il tema dichiara un drawable come sfondo.

È importante impostare il `NoHistory=true` attributo in modo che l'attività viene rimossa dallo stack indietro. Per impedire che il pulsante indietro l'annullamento del processo di avvio, è inoltre possibile sostituire `OnBackPressed` e fare in modo non eseguire alcuna azione:

```csharp
public override void OnBackPressed() { }
```

Il lavoro di avvio viene eseguito in modo asincrono in `OnResume`. Ciò è necessario in modo che il lavoro di avvio non rallentare o un ritardo della visualizzazione della schermata di avvio. Quando il lavoro è stata completata, `SplashActivity` avvierà `MainActivity` e l'utente può avviare l'interazione con l'app.

Questa nuova `SplashActivity` è impostato come l'attività di avvio per l'applicazione impostando il `MainLauncher` attributo `true`. Poiché `SplashActivity` è ora l'attività di avvio, è necessario modificare `MainActivity.cs`e rimuovere il `MainLauncher` dall'attributo `MainActivity`:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```


## <a name="summary"></a>Riepilogo

In questa guida viene descritto un modo per implementare una schermata in un'applicazione di xamarin. in particolare, l'applicazione di un tema personalizzato per l'attività di avvio.


## <a name="related-links"></a>Collegamenti correlati

- [Schermata iniziale (esempio)](https://developer.xamarin.com/samples/monodroid/SplashScreen)
- [elenco dei livelli Drawable](http://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [ Modelli di progettazione materiale - schermate di avvio](https://www.google.com/design/spec/patterns/launch-screens.html)
