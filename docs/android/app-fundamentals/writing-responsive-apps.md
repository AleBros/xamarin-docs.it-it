---
title: Scrittura di applicazioni reattive
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: a1642c4cbb790cf09d2a31e629408afc61d5b7ab
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121776"
---
# <a name="writing-responsive-applications"></a>Scrittura di applicazioni reattive

Una delle chiavi per garantire una velocità di risposta interfaccia utente grafica consiste nell'eseguire attività con esecuzione prolungata in un thread in background in modo che non venga bloccata l'interfaccia utente grafica. Si supponga di che volere calcolare un valore da visualizzare all'utente, ma tale valore ha 5 secondi per la quale calcolare:

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

Questa tecnica funziona, ma l'applicazione si "bloccherà" per 5 secondi mentre il valore viene calcolato. Durante questo periodo, l'app non rispondere alla richiesta alcuna interazione dell'utente. Per evitare questo problema, è necessario eseguire i calcoli in un thread in background:

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

A questo punto si calcola il valore su un thread in background in modo che l'interfaccia utente grafica rimanga reattiva durante il calcolo. Tuttavia, quando viene eseguito il calcolo, l'App nell'app si blocca, presente nel log:

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

Questo avviene perché è necessario aggiornare l'interfaccia utente grafica dal thread dell'interfaccia utente grafica. Il codice aggiorna l'interfaccia utente grafica da thread di pool di thread, causando l'arresto anomalo dell'app. È necessario calcolare il valore sul thread in background, quindi eseguire l'aggiornamento sul thread della GUI, che viene gestito con [Activity.RunOnUIThread](https://developer.xamarin.com/api/member/Android.App.Activity.RunOnUiThread/(System.Action)):

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

Questo codice funziona come previsto. Questa interfaccia GUI rimane reattiva e viene aggiornata correttamente una volta il calcolo comple.

Si noti come che questa tecnica non viene usata solo per calcolare un valore di costoso. Può essere utilizzato per qualsiasi attività con esecuzione prolungata che può essere eseguita in background, ad esempio una chiamata al servizio web o il download dei dati su internet.
