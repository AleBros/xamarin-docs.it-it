---
title: Scrittura di applicazioni reattive
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: b8c113b67b3fbfa57ca86c72e11ddeb0e4e1a9ab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763501"
---
# <a name="writing-responsive-applications"></a>Scrittura di applicazioni reattive

Una delle chiavi per mantenere reattiva GUI consiste nell'eseguire attività a esecuzione prolungata in un thread in background, pertanto l'interfaccia utente grafica, non vengono bloccata. Si supponga di voler calcolare un valore da visualizzare all'utente, ma che il valore 5 secondi per il calcolo:

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

Questo funzionerà, ma l'applicazione verrà "blocco" per 5 secondi mentre il valore viene calcolato. Durante questo periodo, l'applicazione non risponde alle alcuna interazione dell'utente. Per evitare questo problema, si desidera eseguire i calcoli in un thread in background:

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

Ora è calcolare il valore in un thread in background, pertanto l'interfaccia utente grafica rimane attiva durante il calcolo. Tuttavia, durante il calcolo viene eseguito, l'app arresti anomali, lasciare questa nel registro:

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

Questo avviene perché è necessario aggiornare l'interfaccia utente grafica dal thread dell'interfaccia utente grafica. Questo codice aggiorna l'interfaccia utente grafica dal thread di pool di thread, causando l'arresto anomalo dell'app. È necessario calcolare il valore sul thread in background, senza effettuare l'aggiornamento del thread di interfaccia utente grafica, viene gestito con [Activity.RunOnUIThread](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)):

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

Questo codice funziona come previsto. Questa interfaccia GUI rimane attiva e viene aggiornata correttamente una volta il calcolo comple.

Nota che questa tecnica non viene usata solo per il calcolo di un valore costosa. E può essere utilizzato per qualsiasi attività di lunga durata che può essere eseguita in background, ad esempio una chiamata al servizio web o il download dei dati di internet.
