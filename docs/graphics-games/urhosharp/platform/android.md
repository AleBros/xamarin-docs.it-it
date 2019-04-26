---
title: Supporto di Android di UrhoSharp
description: Questo documento descrive il programma di installazione di Android specifici e informazioni relative alla funzionalità di UrhoSharp. In particolare, illustra le architetture supportate, come creare un progetto, configurando e avviando Urho e l'incorporamento personalizzato di Urho.
ms.prod: xamarin
ms.assetid: 8409BD81-B1A6-4F5D-AE11-6BBD3F7C6327
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: e7371fa85fd5955e9a0fd285adb32844001821b3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61285179"
---
# <a name="urhosharp-android-support"></a>Supporto di Android di UrhoSharp

_Funzionalità e installazione specifiche di android_

Sebbene Urho è una libreria di classi portabile e consente la stessa API da usare nell'intera piattaforma diverse per la logica del gioco, devi comunque inizializzare Urho nel driver specifici della piattaforma e in alcuni casi, è possibile sfruttare le funzionalità specifiche della piattaforma .

Nelle pagine seguenti, si supponga che `MyGame` è una sottoclasse del `Application` classe.

## <a name="architectures"></a>Architetture

**Architetture supportate**: x86, armeabi, armeabi-v7a

## <a name="create-a-project"></a>Creare un progetto

Creare un progetto Android e aggiungere il pacchetto NuGet di UrhoSharp.

Aggiungere dati che contiene gli asset per il **asset** directory e assicurarsi che tutti i file hanno **AndroidAsset** come il **azione di compilazione**.

![Il programma di installazione del progetto](android-images/image-3.png "aggiungere dati che contiene gli asset nella directory di asset")

## <a name="configure-and-launching-urho"></a>Configurare e avviare Urho

Aggiungere usando le istruzioni per la `Urho` e `Urho.Android` spazi dei nomi e quindi aggiungere il codice per l'inizializzazione Urho, nonché l'avvio dell'applicazione.

Il modo più semplice per eseguire un gioco, come implementato nella classe MyGame consiste nel chiamare

```csharp
UrhoSurface.RunInActivity<MyGame>();
```

Verrà aperta un'attività a schermo intero con il gioco come un contenuto.

## <a name="custom-embedding-of-urho"></a>Incorporamento personalizzato di Urho

È possibile in alternativa alla presenza di Urho occupi la schermata dell'intera applicazione, e per usarlo come un componente dell'applicazione, è possibile creare un `SurfaceView` tramite:

```csharp
var surface = UrhoSurface.CreateSurface<MyGame>(activity)
```

È necessario anche inoltrare alcuni eventi da parte dell'utente attività a UrhoSharp, ad esempio:

```csharp
protected override void OnPause()
{
    UrhoSurface.OnPause();
    base.OnPause();
}
```

È necessario eseguire la stessa: `OnResume`, `OnPause`, `OnLowMemory`, `OnDestroy`, `DispatchKeyEvent` e `OnWindowFocusChanged`.

Viene illustrato un impegno tipico che consente di avviare il gioco:

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

