---
title: Supporto UrhoSharp Android
description: Il programma di installazione di Android specifico e funzionalità per UrhoSharp.
ms.prod: xamarin
ms.assetid: 8409BD81-B1A6-4F5D-AE11-6BBD3F7C6327
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: 8d6abdac258e7a5b66e12367751652c5769405b0
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="urhosharp-android-support"></a>Supporto UrhoSharp Android

_Funzionalità e configurazione android_

Mentre Urho è una libreria di classi portabile e consente la stessa API da utilizzare nella piattaforma diversi per la logica di gioco, è comunque necessario inizializzare Urho nel driver specifico della piattaforma in alcuni casi, è possibile sfruttare le funzionalità specifiche della piattaforma .

Nelle pagine, si supponga che `MyGame` è una sottoclasse di `Application` classe.

## <a name="architectures"></a>Architetture

**Architetture supportate**: x86, armeabi, armeabi v7a

## <a name="create-a-project"></a>Creare un progetto

Creare un progetto Android e aggiungere il pacchetto UrhoSharp NuGet.

Aggiungere dati che contiene le risorse per il **asset** directory e assicurarsi che tutti i file hanno **AndroidAsset** come il **azione di compilazione**.

![Il programma di installazione del progetto](android-images/image-3.png "Aggiungi dati contenente le risorse nella directory di risorse")

## <a name="configure-and-launching-urho"></a>Configurare e avviare Urho

Aggiungere l'utilizzo di istruzioni per la `Urho` e `Urho.Android` gli spazi dei nomi e quindi aggiungere il codice per l'inizializzazione Urho, nonché di avviare l'applicazione.

Per eseguire un gioco, come implementato nella classe MyGame nel modo più semplice consiste nel chiamare

```csharp
UrhoSurface.RunInActivity<MyGame>();
```

Verrà aperta un'attività a schermo intero con il gioco come un contenuto.

## <a name="custom-embedding-of-urho"></a>Incorporamento personalizzato di Urho

È possibile in alternativa alla Urho assumere la schermata intera applicazione e per utilizzarlo come un componente dell'applicazione, è possibile creare un `SurfaceView` tramite:

```csharp
var surface = UrhoSurface.CreateSurface<MyGame>(activity)
```

È necessario anche per l'inoltro di alcuni eventi da parte dell'utente di attività per UrhoSharp, ad esempio:

```csharp
protected override void OnPause()
{
    UrhoSurface.OnPause();
    base.OnPause();
}
```

È necessario eseguire la stessa: `OnResume`, `OnPause`, `OnLowMemory`, `OnDestroy`, `DispatchKeyEvent` e `OnWindowFocusChanged`.

Viene illustrato una tipico attività che consente di avviare il gioco:

```csharp
[Activity(Label = "MyUrhoApp", MainLauncher = true,
    Icon = "@drawable/icon", Theme = "@android:style/Theme.NoTitleBar.Fullscreen",
    ConfigurationChanges = ConfigChanges.KeyboardHidden | ConfigChanges.Orientation,
    ScreenOrientation = ScreenOrientation.Portrait)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        var mLayout = new AbsoluteLayout(this);
        var surface = UrhoSurface.CreateSurface<MyUrhoApp>(this);
        mLayout.AddView(surface);
        SetContentView(mLayout);
    }

    protected override void OnResume()
    {
        UrhoSurface.OnResume();
        base.OnResume();
    }

    protected override void OnPause()
    {
        UrhoSurface.OnPause();
        base.OnPause();
    }

    public override void OnLowMemory()
    {
        UrhoSurface.OnLowMemory();
        base.OnLowMemory();
    }

    protected override void OnDestroy()
    {
        UrhoSurface.OnDestroy();
        base.OnDestroy();
    }

    public override bool DispatchKeyEvent(KeyEvent e)
    {
        if (!UrhoSurface.DispatchKeyEvent(e))
            return false;
        return base.DispatchKeyEvent(e);
    }

    public override void OnWindowFocusChanged(bool hasFocus)
    {
        UrhoSurface.OnWindowFocusChanged(hasFocus);
        base.OnWindowFocusChanged(hasFocus);
    }
}
```

