---
title: Input tocco in Android
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: f1ccc86a20cb441bfdda864b8c7e263a691f5ab7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30767492"
---
# <a name="touch-in-android"></a>Input tocco in Android

Molto come iOS, Android crea un oggetto che contiene dati sull'interazione del fisico dell'utente con lo schermo &ndash; un `Android.View.MotionEvent` oggetto. Questo oggetto contiene i dati, ad esempio l'azione da eseguire, in cui ha impiegato il tocco, di valutare la pressione è stato applicato, e così via. Oggetto `MotionEvent` oggetto suddivide lo spostamento in per i valori seguenti:

-  Un codice di azione che descrive il tipo di movimento, ad esempio premendo iniziale, il tocco lo spostamento tra la schermata o la fine di tocco.

-  Un set di valori dell'asse che descrivono la posizione del `MotionEvent` e altre proprietà di spostamento, ad esempio in cui si sta eseguendo il tocco, quando è avvenuta il tocco e la quantità richiesta è stata utilizzata.
   I valori dell'asse possono essere diversi a seconda del dispositivo, pertanto l'elenco precedente non sono descritti tutti i valori dell'asse.


Il `MotionEvent` oggetto verrà passato a un metodo appropriato in un'applicazione. Esistono tre modi per un'applicazione di xamarin rispondere a un evento di tocco:

-  *Assegnare un gestore eventi per `View.Touch`*  - `Android.Views.View` classe dispone di un `EventHandler<View.TouchEventArgs>` le applicazioni che è possono assegnare un gestore per. Questo è il comportamento tipico .NET.

-  *Implementazione di `View.IOnTouchListener`*  -istanze di questa interfaccia possono essere assegnate a un oggetto vista utilizzando la visualizzazione. `SetOnListener` metodo. Questo è funzionalmente equivalente all'assegnazione di un gestore eventi per il `View.Touch` evento. Se è presente una logica comune o condivisa che diverse visualizzazioni potrebbe essere necessario quando vengono modificate, sarà più efficiente per creare una classe e implementare questo metodo più to assegnare ogni visualizzazione il proprio gestore eventi.

-  *Eseguire l'override `View.OnTouchEvent`*  -tutte le visualizzazioni in Android sottoclasse `Android.Views.View`. Quando viene eseguita una vista Android chiamerà il `OnTouchEvent` e passare un `MotionEvent` oggetto come parametro.


> [!NOTE]
> Non tutti i dispositivi Android supportano touch screen. 

Aggiungendo il seguente tag al file di manifesto comporta Google Play visualizzato solo l'app per i dispositivi di tocco abilitato:

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>Movimenti

Un movimento è una forma di mano su touch screen. Un movimento può avere uno o più tracce, ogni tratto costituito da una sequenza di punti creato da un diverso punto di contatto con la schermata. Android supporta molti tipi diversi di movimenti, da un semplice fling sullo schermo per i movimenti complessi che coinvolgono Multi-touch.

Android fornisce il `Android.Gestures` dello spazio dei nomi in modo specifico per la gestione e risponde ai movimenti. AT il cuore di tutti i movimenti è una classe speciale denominata `Android.Gestures.GestureDetector`. Come suggerisce il nome, questa classe sarà in ascolto per i movimenti e gli eventi in base `MotionEvents` fornito dal sistema operativo.

Per implementare un rilevatore di movimento, è necessario creare un'istanza di un'attività un `GestureDetector` classe e fornisce un'istanza di `IOnGestureListener`, come illustrato dal frammento di codice seguente:

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

Un'attività deve inoltre implementare il OnTouchEvent e passare il MotionEvent per il rilevamento del movimento. Frammento di codice seguente viene illustrato un esempio:

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

Quando un'istanza di `GestureDetector` identifica un movimento di interesse, avvisa l'attività o l'applicazione genera un evento o tramite un callback fornito dal `GestureDetector.IOnGestureListener`.
Questa interfaccia fornisce i sei metodi per i vari movimenti:

-  *OnDown* -chiamato quando si verifica una scelta, ma non viene rilasciata.

-  *OnFling* -chiamato quando un fling si verifica e fornisce i dati iniziali e finali tocco che ha attivato l'evento.

-  *OnLongPress* -chiamato quando si verifica la pressione di un lungo.

-  *OnScroll* -chiamato quando si verifica un evento di scorrimento.

-  *OnShowPress* - chiamato dopo che si è verificato un OnDown e lo spostamento o l'evento non è stata eseguita.

-  *OnSingleTapUp* -chiamato quando si verifica un unico tocco.


In molti casi applicazioni potrebbero essere interessate solo a un sottoinsieme dei movimenti. In questo caso, le applicazioni devono estendere la classe GestureDetector.SimpleOnGestureListener e l'override dei metodi che corrispondono agli eventi che sono interessati.

## <a name="custom-gestures"></a>Movimenti personalizzati

I movimenti sono un ottimo modo per gli utenti di interagire con un'applicazione. Le API che abbiamo visto fino a questo punto dovrebbe essere sufficiente per i movimenti semplici, ma potrebbero risultare un po' onerose per i movimenti più complessi. Per risolvere i movimenti più complicati, Android offre un altro set di API nello spazio dei nomi Android.Gestures che semplificherà l'onere associato di movimenti personalizzati.

### <a name="creating-custom-gestures"></a>Creazione di movimenti personalizzati

Poiché Android 1.6, Android SDK viene fornito con un'applicazione di pre-installata in un emulatore denominato movimenti generatore. Questa applicazione consente agli sviluppatori di creare i movimenti predefiniti che possono essere incorporati in un'applicazione. La schermata seguente viene illustrato un esempio di generatore movimenti:

[![Schermata di movimenti generatore con i movimenti di esempio](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

Una versione migliorata di questa applicazione denominata strumento di movimento è reperibile Google Play. Strumento di movimento è molto simile a Generatore movimenti ad eccezione del fatto che consente di testare i movimenti dopo che sono state create. In questo screenshot successivo illustra i movimenti generatore:

[![Schermata di movimento strumento con i movimenti di esempio](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

Strumento di movimento è un po' più utile per la creazione di movimenti personalizzati, in quanto consente di movimenti da sottoporre a test come la creazione ed è facilmente disponibile tramite Google Play.

Strumento di movimento consente di creare un movimento di disegno sullo schermo e assegnando un nome. Dopo aver creati i movimenti vengono salvati in un file binario nella scheda SD del dispositivo. Questo file deve essere recuperato dal dispositivo e quindi fornito con un'applicazione in /Resources/raw la cartella. Questo file può essere recuperato dall'emulatore usando il Bridge Debug Android. Nell'esempio seguente viene illustrato come copiare il file da un nodo Galaxy per la directory delle risorse di un'applicazione:

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

Dopo aver recuperato il file deve essere collocato con l'applicazione all'interno di /risorse directory/raw. Il modo più semplice per utilizzare questo file di movimento è per caricare il file in un GestureLibrary, come illustrato nel frammento riportato di seguito:

```csharp
GestureLibary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>Utilizzo di movimenti personalizzati

Per riconoscere i movimenti personalizzati in un'attività, deve disporre di un oggetto Android.Gesture.GestureOverlay aggiunto per il layout. Frammento di codice riportato di seguito viene illustrato come aggiungere a livello di codice un GestureOverlayView a un'attività:

```csharp
GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
gestureOverlayView.AddOnGesturePerformedListener(this);
SetContentView(gestureOverlayView);
```

Il frammento XML seguente viene illustrato come aggiungere un GestureOverlayView in modo dichiarativo:

```xml
<android.gesture.GestureOverlayView
    android:id="@+id/gestures"
    android:layout_width="match_parent "
    android:layout_height="match_parent" />
```

Il `GestureOverlayView` dispone di diversi eventi che vengono generati durante il processo di creazione di un movimento. L'evento più interessante è `GesturePeformed`. Questo evento viene generato quando l'utente ha completato i movimenti di disegno.

Quando viene generato questo evento, l'attività richiede un `GestureLibrary` per provare e far corrispondere il movimento creati dall'utente con uno dei movimenti dallo strumento di movimento. `GestureLibrary` verrà restituito un elenco di oggetti di stima.

Ogni oggetto di stima include un punteggio e nome di uno dei movimenti nel `GestureLibrary`. Maggiore il punteggio, più è probabile che il movimento denominato nella stima consente di ricercare il movimento disegnato dall'utente.
In generale, minore di 1.0 punteggi verranno considerati corrispondenti insufficienti.

Il codice seguente viene illustrato un esempio di un movimento corrispondenti:

```csharp
private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
{
    // In this example _gestureLibrary was instantiated in OnCreate
    IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
    orderby p.Score descending
    where p.Score > 1.0
    select p;
    Prediction prediction = predictions.FirstOrDefault();

    if (prediction == null)
    {
        Log.Debug(GetType().FullName, "Nothing matched the user's gesture.");
        return;
    }

    Toast.MakeText(this, prediction.Name, ToastLength.Short).Show();
}
```

Al termine, è necessario avere una conoscenza relativo all'uso di tocco e i movimenti in un'applicazione di xamarin. Segnalare il problema ora spostare una procedura dettagliata e visualizzare tutti i concetti presentati in un'applicazione di esempio funzionante.



## <a name="related-links"></a>Collegamenti correlati

- [Android toccare avviare (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android tocco finale (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
