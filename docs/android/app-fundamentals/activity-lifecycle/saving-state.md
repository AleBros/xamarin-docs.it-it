---
title: Procedura dettagliata - Salvataggio dello stato dell'attività
description: Sono stati illustrati il salvataggio dello stato nella Guida all'attività del ciclo di vita; la teoria a questo punto, passiamo a esaminare un esempio.
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: c8f92e55648dff469227cc3bad981ad5f6e6d0ac
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61019158"
---
# <a name="walkthrough---saving-the-activity-state"></a>Procedura dettagliata - Salvataggio dello stato dell'attività

_Sono stati illustrati il salvataggio dello stato nella Guida all'attività del ciclo di vita; la teoria a questo punto, passiamo a esaminare un esempio._

## <a name="activity-state-walkthrough"></a>Procedura dettagliata di stato dell'attività

È possibile aprire il **ActivityLifecycle_Start** progetto (nelle [ActivityLifecycle](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle) esempio), compilarla ed eseguirla. Si tratta di un progetto molto semplice che include due attività per illustrare il ciclo di vita di attività e come vengono chiamati diversi metodi del ciclo di vita. Quando si avvia l'applicazione, la schermata di `MainActivity` viene visualizzato: 

[![Schermata di attività A](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>Transizioni di stato di visualizzazione

Ogni metodo in questo esempio scrive la finestra di output dell'applicazione di IDE per indicare lo stato dell'attività. (Per aprire la finestra di output in Visual Studio, digita **CTRL-ALT-O**; per aprire la finestra di output in Visual Studio per Mac, fare clic su **visualizzazione > riquadri > Output applicazione**.)

Quando l'app viene avviata prima di tutto, la finestra di output visualizza le modifiche dello stato *attività A*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Quando si fa clic sul **avviare attività B** button, noteremo *attività A* Sospendi e arresta durante *attività B* passa attraverso il relativo stato cambia: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

Ne consegue *attività B* sia avviato e visualizzato al posto del *attività A*: 

[![Schermata attività B](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

Quando si fa clic il **nuovamente** pulsante *attività B* viene eliminato definitivamente e *attività A* viene ripreso: 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```
### <a name="adding-a-click-counter"></a>Aggiunta di un contatore di fare clic su

Successivamente, dobbiamo modificare l'applicazione in modo che è presente un pulsante che calcola e visualizza il numero di volte in cui che viene fatto clic. In primo luogo, è possibile aggiungere un `_counter` variabile di istanza alla `MainActivity`:

```csharp
int _counter = 0;
```

Successivamente, è possibile modificare il **Resource/layout/Main.axml** layout di file e aggiungere un nuovo `clickButton` che visualizza il numero di volte in cui l'utente ha fatto clic sul pulsante. L'oggetto risultante **Main. axml** dovrebbe essere simile al seguente: 

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

È possibile aggiungere il codice seguente alla fine della [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) metodo nella `MainActivity` &ndash; questo codice gestisce gli eventi dal click il `clickButton`:

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

Quando si compila e si esegue nuovamente l'app, nuovo viene visualizzato un pulsante che incrementa e visualizza il valore di `_counter` su ogni clic:

[![Aggiungere il numero di tocco](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

Tuttavia, quando si ruota il dispositivo in modalità orizzontale, questo conteggio viene perso:

[![Ruotare un oggetto per landscape imposta il conteggio su zero](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

Esaminare l'output dell'applicazione, è possibile vedere che *attività A* è stato messo in pausa, arrestato, eliminato, ricreato, riavviato, quindi ripresa durante la rotazione da verticale in modalità orizzontale: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

In quanto *attività A* viene eliminata e ricreata nuovamente quando il dispositivo viene ruotato, lo stato dell'istanza viene perso. Successivamente, si aggiungerà codice per salvare e ripristinare lo stato dell'istanza.

### <a name="adding-code-to-preserve-instance-state"></a>Aggiunta di codice per mantenere lo stato dell'istanza

È possibile aggiungere un metodo per `MainActivity` per salvare lo stato dell'istanza. Prima di *attività A* viene eliminata definitivamente, Android chiama automaticamente [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) e passa un [Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) che è possibile usare per archiviare lo stato dell'istanza. È possibile usarla per salvare il numero di clic come un valore integer:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

Quando *attività A* viene ricreata e ripreso, Android passa questo `Bundle` nuovamente nel nostro `OnCreate` (metodo). È possibile aggiungere codice al `OnCreate` per ripristinare il `_counter` valore passato `Bundle`. Aggiungere il codice seguente appena prima della riga in cui `clickbutton` è definito: 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

Creare ed eseguire nuovamente l'app, quindi fare clic sul secondo pulsante alcune volte. Quando si ruota il dispositivo in modalità orizzontale, viene mantenuto il conteggio.

[![Rotazione schermo Mostra il numero di quattro mantenuto](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)


Diamo un'occhiata alla finestra di output per vedere cosa è successo:
    
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

Prima di [OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/) metodo è stato chiamato, nuovo `OnSaveInstanceState` metodo è stato chiamato per salvare il `_counter` valore in un `Bundle`. Android passate a questa `Bundle` torna a Microsoft quando è chiamata nostro `OnCreate` metodo e abbiamo potuto usato per ripristinare il `_counter` valore in cui le avevamo lasciate.


## <a name="summary"></a>Riepilogo

In questa procedura dettagliata, abbiamo utilizzato la Knowledge base del ciclo di vita delle attività per mantenere i dati sullo stato. 



## <a name="related-links"></a>Collegamenti correlati

- [ActivityLifecycle (esempio)](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)
- [Ciclo di vita dell'attività](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Attività Android](https://developer.xamarin.com/api/type/Android.App.Activity/)
