---
title: 'Procedura dettagliata: salvataggio dello stato attività'
description: Sono stati illustrati teoria di salvataggio dello stato nel manuale dell'attività del ciclo di vita; a questo punto, verrà ora esaminato un esempio.
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: e282eeb8732bd5294da4ec4e3fe337e81107c8f3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30767427"
---
# <a name="walkthrough---saving-the-activity-state"></a>Procedura dettagliata: salvataggio dello stato attività

_Sono stati illustrati teoria di salvataggio dello stato nel manuale dell'attività del ciclo di vita; a questo punto, verrà ora esaminato un esempio._

## <a name="activity-state-walkthrough"></a>Procedura dettagliata dello stato dell'attività

Aprire il **ActivityLifecycle_Start** progetto (nel [ActivityLifecycle](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle) esempio), compilarlo ed eseguirlo. Si tratta di un progetto molto semplice che contiene due attività per illustrare il ciclo di vita di attività e la modalità in cui vengono chiamati i diversi metodi del ciclo di vita. Quando si avvia l'applicazione, la schermata di `MainActivity` viene visualizzato: 

[![Schermata di attività A](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>Transizioni di stato di visualizzazione

Ogni metodo in questo esempio scrive alla finestra di output dell'applicazione IDE per indicare lo stato dell'attività. (Per aprire la finestra di output in Visual Studio, digitare **CTRL-ALT-O**; per aprire la finestra di output in Visual Studio per Mac, fare clic su **Vista > Pad > Output dell'applicazione**.)

All'avvio dell'app, la finestra di output visualizza le modifiche di stato di *attività A*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Quando si fa clic su di **avviare attività B** pulsante, è possibile notare *attività A* pausa e arresto durante *attività B* passa attraverso il relativo stato cambia: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

Di conseguenza, *attività B* viene avviato e visualizzato al posto di *attività A*: 

[![Schermata di attività B](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

Quando si fa clic su di **nuovamente** pulsante *attività B* viene eliminato definitivamente e *attività A* viene ripreso: 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```
### <a name="adding-a-click-counter"></a>Aggiunta di un contatore di fare clic su

Successivamente, si intende modificare l'applicazione in modo che si dispone di un pulsante che calcola e visualizza il numero di volte in cui che si fa clic. In primo luogo, aggiungere un `_counter` variabile di istanza per `MainActivity`:

```csharp
int _counter = 0;
```

Successivamente, si modifica il **Resource/layout/Main.axml** layout di file e aggiungere un nuovo `clickButton` che visualizza il numero di volte in cui l'utente ha fatto clic sul pulsante. Il valore risultante **axml** dovrebbe essere simile al seguente: 

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

Aggiungere il codice seguente alla fine del [OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) metodo `MainActivity` &ndash; questo codice gestisce gli eventi da click il `clickButton`:

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

Quando si compila e si esegue nuovamente l'app, nuovo viene visualizzato un pulsante che aumenta e visualizza il valore di `_counter` , fare clic su ogni:

[![Aggiungere il numero di tocco](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

Tuttavia, quando si ruota il dispositivo in modalità orizzontale, questo numero viene perso:

[![Rotazione per landscape imposta il conteggio su zero](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

Esaminare l'output dell'applicazione, è possibile vedere che *attività A* è stato sospeso, arrestato, eliminato, ricreato, riavviare, quindi ripresa durante la rotazione da verticale in orizzontale: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Poiché *attività A* verrà eliminato e ricreato nuovamente quando il dispositivo viene ruotato, lo stato dell'istanza viene perso. Successivamente, si aggiungerà codice per salvare e ripristinare lo stato dell'istanza.

### <a name="adding-code-to-preserve-instance-state"></a>Aggiungere il codice per mantenere lo stato dell'istanza

Aggiungere un metodo per `MainActivity` per salvare lo stato dell'istanza. Prima di *attività A* viene eliminato definitivamente, Android chiama automaticamente [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) e passa un [Bundle](https://developer.xamarin.com/api/type/Android.OS.Bundle/) che è possibile utilizzare per archiviare lo stato dell'istanza. Verrà utilizzata per salvare il numero di clic come un valore integer:

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

Quando *attività A* viene ricreata e ripreso, Android passa questo `Bundle` nel nostro `OnCreate` metodo. Aggiungere codice per `OnCreate` per ripristinare il `_counter` valore passato `Bundle`. Aggiungere il seguente codice appena prima della riga in cui `clickbutton` è definito: 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

Creare ed eseguire di nuovo l'app, quindi fare clic sul secondo pulsante più volte. Quando si ruota il dispositivo in modalità orizzontale, viene mantenuto il conteggio.

[![Rotazione schermo Mostra il numero di quattro mantenuto](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)


Esaminiamo ora la finestra di output per vedere cosa è successo:
    
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

Prima di [OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/) metodo è stato chiamato, i nuovi `OnSaveInstanceState` metodo è stato chiamato per salvare il `_counter` valore in un `Bundle`. Android passate a questa `Bundle` nuovamente a Microsoft quando chiamato il nostro `OnCreate` metodo e abbiamo potuto utilizzata per ripristinare il `_counter` valore in cui è stata interrotta.


## <a name="summary"></a>Riepilogo

In questa procedura dettagliata, è stata usata una solida conoscenza del ciclo di vita di attività per conservare i dati di stato. 



## <a name="related-links"></a>Collegamenti correlati

- [ActivityLifecycle (esempio)](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)
- [Ciclo di vita dell'attività](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Attività Android](https://developer.xamarin.com/api/type/Android.App.Activity/)
