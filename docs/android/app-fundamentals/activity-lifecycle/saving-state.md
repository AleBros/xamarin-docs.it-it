---
title: Procedura dettagliata - Salvataggio dello stato dell'attività
description: Abbiamo trattato la teoria dietro il salvataggio dello stato nella guida del ciclo di vita delle attività; Esaminiamo ora un esempio.
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: e449e6a62d0c8ca283f20c689477c1f1482611c5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017012"
---
# <a name="walkthrough---saving-the-activity-state"></a>Procedura dettagliata - Salvataggio dello stato dell'attività

_Abbiamo trattato la teoria dietro il salvataggio dello stato nella guida del ciclo di vita delle attività; Esaminiamo ora un esempio._

## <a name="activity-state-walkthrough"></a>Procedura dettagliata sullo stato dell'attività

Aprire il progetto **ActivityLifecycle_Start** (nell'esempio [ActivityLifecycle](https://docs.microsoft.com/samples/xamarin/monodroid-samples/activitylifecycle) ), compilarlo ed eseguirlo. Si tratta di un progetto molto semplice con due attività per illustrare il ciclo di vita delle attività e il modo in cui vengono chiamati i vari metodi del ciclo di vita. Quando si avvia l'applicazione, viene visualizzata la schermata di `MainActivity`:

[![attività A schermata](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>Visualizzazione delle transizioni di stato

Ogni metodo in questo esempio scrive nella finestra di output dell'applicazione IDE per indicare lo stato dell'attività. Per aprire la finestra di output in Visual Studio, digitare **CTRL + ALT + O**; per aprire la finestra di output in Visual Studio per Mac, fare clic su **Visualizza > rilievi > output dell'applicazione**.

Quando l'app viene avviata per la prima volta, nella finestra di output vengono visualizzate le modifiche dello stato dell' *attività A*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Quando si fa clic sul pulsante **Avvia attività b** , viene visualizzata l' *attività* sospensione e arresto mentre l' *attività B* passa attraverso le modifiche dello stato: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

Di conseguenza, l' *attività B* viene avviata e visualizzata al posto dell' *attività a*: 

[schermata attività B![](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

Quando si fa clic sul pulsante **indietro** , l' *attività B* viene distrutta e l' *attività A* viene ripresa: 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```

### <a name="adding-a-click-counter"></a>Aggiunta di un contatore di clic

A questo punto, si modificherà l'applicazione in modo che sia presente un pulsante che consente di contare e visualizzare il numero di volte in cui viene fatto clic. Prima di tutto, aggiungere una variabile di istanza di `_counter` `MainActivity`:

```csharp
int _counter = 0;
```

Modificare quindi il file di layout **Resource/layout/Main. aXML** e aggiungere una nuova `clickButton` che visualizzi il numero di volte in cui l'utente ha fatto clic sul pulsante. Il **Main. aXML** risultante sarà simile al seguente: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/myButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/mybutton_text" />
    <Button
        android:id="@+id/clickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/counterbutton_text" />
</LinearLayout>
```

Aggiungere il codice seguente alla fine del metodo [OnCreate](xref:Android.App.Activity.OnCreate*) in `MainActivity` &ndash; questo codice gestisce gli eventi click dal `clickButton`:

```csharp
var clickbutton = FindViewById<Button> (Resource.Id.clickButton);
clickbutton.Text = Resources.GetString (
    Resource.String.counterbutton_text, _counter);
clickbutton.Click += (object sender, System.EventArgs e) =>
{
    _counter++;
    clickbutton.Text = Resources.GetString (
        Resource.String.counterbutton_text, _counter);
} ;
```

Quando si compila e si esegue di nuovo l'app, viene visualizzato un nuovo pulsante che incrementa e visualizza il valore di `_counter` a ogni clic:

[![aggiungere il numero di tocchi](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

Tuttavia, quando si ruota il dispositivo in modalità orizzontale, il conteggio viene perso:

[![la rotazione a orizzontale imposta il conteggio di nuovo su zero](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

Esaminando l'output dell'applicazione, si noterà che l' *attività A* è stata sospesa, arrestata, distrutta, ricreata, riavviata, quindi ripresa durante la rotazione dalla modalità verticale a orizzontale: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Poiché l' *attività A* viene distrutta e ricreata di nuovo quando il dispositivo viene ruotato, lo stato dell'istanza viene perso. Successivamente, verrà aggiunto il codice per salvare e ripristinare lo stato dell'istanza.

### <a name="adding-code-to-preserve-instance-state"></a>Aggiunta del codice per mantenere lo stato dell'istanza

Si aggiungerà un metodo per `MainActivity` per salvare lo stato dell'istanza. Prima che *l'attività A* venga distrutta, Android chiama automaticamente [OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) e passa un [bundle](xref:Android.OS.Bundle) che è possibile usare per archiviare lo stato dell'istanza. Verrà usato per salvare il conteggio dei clic come valore intero:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

Quando l' *attività A* viene ricreata e ripresa, Android passa questo `Bundle` al metodo `OnCreate`. Aggiungere il codice `OnCreate` per ripristinare il valore `_counter` dal `Bundle`passato. Aggiungere il codice seguente immediatamente prima della riga in cui è definito `clickbutton`: 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

Compilare ed eseguire nuovamente l'app, quindi fare clic sul secondo pulsante alcune volte. Quando si ruota il dispositivo in modalità orizzontale, il conteggio viene mantenuto.

[![rotazione della schermata mostra il numero di quattro conservati](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)

Diamo un'occhiata alla finestra output per vedere cosa è successo:

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - Saving instance state
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - Recovered instance state
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Prima della chiamata al metodo [OnStop](xref:Android.App.Activity.OnStop) , è stato chiamato il nuovo metodo `OnSaveInstanceState` per salvare il valore `_counter` in un `Bundle`. Android ha passato questa `Bundle` a Microsoft quando ha chiamato il metodo `OnCreate`, ed è stato possibile usarlo per ripristinare il valore di `_counter` a quello in cui ci siamo lasciati.

## <a name="summary"></a>Riepilogo

In questa procedura dettagliata è stata usata la conoscenza del ciclo di vita delle attività per conservare i dati di stato.

## <a name="related-links"></a>Collegamenti correlati

- [ActivityLifecycle (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/activitylifecycle)
- [Ciclo di vita dell'attività](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Attività Android](xref:Android.App.Activity)
