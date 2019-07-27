---
title: Scrittura di applicazioni reattive
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: e3f7d788e71718f4ca1336b7906cf3d63bf07f32
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509202"
---
# <a name="writing-responsive-applications"></a>Scrittura di applicazioni reattive

Una delle chiavi per gestire un'interfaccia utente grafica reattiva è eseguire attività a esecuzione prolungata in un thread in background in modo che l'interfaccia utente grafica non venga bloccata. Supponiamo di voler calcolare un valore da visualizzare all'utente, ma tale valore impiega 5 secondi per il calcolo:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        SlowMethod ();
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

Questa operazione funzionerà, ma l'applicazione verrà "bloccata" per 5 secondi mentre viene calcolato il valore. Durante questo periodo, l'app non risponderà a nessuna interazione con l'utente. Per aggirare questo problema, è necessario eseguire i calcoli in un thread in background:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

Ora calcoliamo il valore in un thread in background in modo che l'interfaccia utente grafica rimanga reattiva durante il calcolo. Tuttavia, al termine del calcolo, l'app si arresta in modo anomalo, lasciandola nel log:

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

Questo perché è necessario aggiornare l'interfaccia utente grafica dal thread GUI. Il codice aggiorna l'interfaccia utente grafica dal thread ThreadPool, causando l'arresto anomalo dell'app. È necessario calcolare il valore nel thread in background, ma quindi eseguire l'aggiornamento sul thread della GUI, che viene gestito con [Activity. RunOnUIThread](xref:Android.App.Activity.RunOnUiThread*):

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        RunOnUiThread (() => textview.Text = "Method Complete");
    }
}
```

Questo codice funziona come previsto. Questa GUI rimane attiva e viene aggiornata correttamente una volta che il calcolo è comple.

Si noti che questa tecnica non viene usata solo per il calcolo di un valore costoso. Può essere usato per qualsiasi attività a esecuzione prolungata che può essere eseguita in background, ad esempio una chiamata al servizio Web o il download di dati Internet.
