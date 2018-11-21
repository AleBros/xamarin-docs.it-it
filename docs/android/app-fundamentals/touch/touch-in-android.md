---
title: Tocco in Android
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 84767975eece4f8f0efae1fe53463cbc053bd836
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171478"
---
# <a name="touch-in-android"></a>Tocco in Android

Molto, ad esempio iOS, Android crea un oggetto che contiene dati sull'interazione fisica dell'utente con la schermata &ndash; un `Android.View.MotionEvent` oggetto. Questo oggetto contiene i dati, ad esempio l'azione da eseguire, dove ha richiesto il tocco, di valutare la pressione è stata applicata e così via. Oggetto `MotionEvent` oggetto suddivide lo spostamento sui valori seguenti:

-  Un codice di azione che descrive il tipo di movimento, ad esempio il tocco iniziale, il tocco lo spostamento tra la schermata o la fine di tocco.

-  Un set di valori dell'asse che descrivono la posizione del `MotionEvent` e altre proprietà di spostamento, ad esempio in cui il tocco sia in esecuzione, quando il tocco ha avuto luogo e valutare la pressione è stata usata.
   I valori dell'asse possono essere diversi a seconda del dispositivo, in modo che l'elenco precedente non sono descritti tutti i valori dell'asse.


Il `MotionEvent` oggetto verrà passato al metodo appropriato in un'applicazione. Esistono tre modi per un'applicazione xamarin. Android rispondere a un evento di tocco:

-  *Assegnare un gestore eventi al `View.Touch`*  - il `Android.Views.View` classe ha un `EventHandler<View.TouchEventArgs>` quali applicazioni possono assegnare un gestore di. Questo è tipico comportamento .NET.

-  *Implementazione `View.IOnTouchListener`*  -istanze di questa interfaccia possono essere assegnate a un oggetto di visualizzazione usando la visualizzazione. `SetOnListener` metodo. Questa funzionalità equivale all'assegnazione di un gestore eventi per il `View.Touch` evento. Se è presente una logica comune o condivisa che molte visualizzazioni diverse potrebbe essere necessario quando vengono modificate, sarà più efficiente per creare una classe e implementare questo metodo più to assegnare ogni visualizzazione del proprio gestore eventi.

-  *Eseguire l'override `View.OnTouchEvent`*  -tutte le viste nella sottoclasse Android `Android.Views.View`. Quando una visualizzazione viene manipolata, Android chiama il `OnTouchEvent` e passargli un `MotionEvent` oggetto come parametro.


> [!NOTE]
> Non tutti i dispositivi Android supportano touch screen. 

Aggiungendo il tag seguente al file manifesto fa sì che Google Play da visualizzare solo le app in tali dispositivi touch abilitata:

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>Movimenti

Un movimento è una forma di mano su touch screen. Un movimento può avere uno o più tratti, ogni tratto costituito da una sequenza di punti creato da un altro punto di contatto con la schermata. Android può supportare molti tipi diversi di movimenti, da un semplice fling sullo schermo per i movimenti di complessi che coinvolgono Multi-touch.

Android offre il `Android.Gestures` dello spazio dei nomi in modo specifico per la gestione e risponde ai movimenti. AT il cuore di tutti i movimenti è una classe speciale chiamata `Android.Gestures.GestureDetector`. Come suggerisce il nome, questa classe sarà in ascolto per i movimenti e gli eventi in base `MotionEvents` fornita dal sistema operativo.

Per implementare un rilevatore di movimento, è necessario creare un'istanza di un'attività una `GestureDetector` classe e specificare un'istanza di `IOnGestureListener`, come illustrato dal frammento di codice seguente:

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

Un'attività deve inoltre implementare l'OnTouchEvent e passare il MotionEvent il rilevatore di movimento. Il frammento di codice seguente viene illustrato un esempio:

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

Quando un'istanza del `GestureDetector` identifica un movimento di interesse, avvisa l'attività o l'applicazione genera un evento o tramite un callback fornito dal `GestureDetector.IOnGestureListener`.
Questa interfaccia fornisce sei metodi per i movimenti diversi:

-  *OnDown* -viene chiamato quando un tocco viene generato ma non viene rilasciato.

-  *OnFling* -viene chiamato quando un fling si verifica e fornisce i dati sul tocco che ha attivato l'evento iniziale e finale.

-  *OnLongPress* -chiamati quando si verifica una pressione prolungata.

-  *OnScroll* -viene chiamato quando si verifica un evento di scorrimento.

-  *OnShowPress* : chiamato dopo che si è verificato un OnDown e lo spostamento o evento di rilascio non è stata eseguita.

-  *OnSingleTapUp* -chiamati quando si verifica un singolo tocco.


In molti casi le applicazioni possono risultare utili solo un subset dei movimenti. In questo caso, applicazioni devono estendere la classe GestureDetector.SimpleOnGestureListener ed eseguire l'override di metodi che corrispondono agli eventi che sono interessati.

## <a name="custom-gestures"></a>Movimenti personalizzati

I movimenti sono un ottimo modo per gli utenti di interagire con un'applicazione. Le API che abbiamo visto finora dovrebbe essere sufficiente per i movimenti semplici, ma potrebbero risultare un po' onerose per movimenti più complicati. Per risolvere i movimenti più complicati, Android offre un altro set di API nello spazio dei nomi Android.Gestures che semplificherà l'onere associati movimenti personalizzati.

### <a name="creating-custom-gestures"></a>Creazione di movimenti personalizzati

Poiché Android 1.6, Android SDK viene fornito con un'applicazione di pre-installata sull'emulatore chiamato movimenti Builder. Questa applicazione consente agli sviluppatori di creare i movimenti predefiniti che possono essere incorporati in un'applicazione. Lo screenshot seguente mostra un esempio di movimenti generatore:

[![Schermata di movimenti generatore con i movimenti di esempio](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

Una versione migliorata dell'applicazione, denominata strumento di movimento è reperibile Google Play. Lo strumento di movimento è molto simile a movimenti generatore con la differenza che consente di testare i movimenti dopo che sono stati creati. In questo screenshot successivo illustra i movimenti generatore:

[![Schermata di Tool in movimento con gesti di esempio](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

Lo strumento di movimento è un po' più utile per la creazione di movimenti personalizzati in quanto consente i movimenti da sottoporre a test in cui sono state create ed è facilmente disponibile tramite Google Play.

Lo strumento di movimento consente di creare un movimento di disegno sullo schermo e assegnando un nome. Dopo aver creati i movimenti vengono salvati in un file binario nella scheda SD del dispositivo. Questo file deve essere recuperato dal dispositivo e quindi inserite con un'applicazione in /Resources/raw la cartella. Questo file può essere recuperato dall'emulatore tramite Android Debug Bridge. L'esempio seguente illustra la copia del file da un dispositivo Galaxy Nexus per la directory delle risorse di un'applicazione:

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

Dopo aver recuperato il file deve essere inclusi nel pacchetto di applicazione all'interno di /Resources. directory/raw. Il modo più semplice per usare questo file di movimento è per caricare il file in un GestureLibrary, come illustrato nel frammento di codice seguente:

```csharp
GestureLibrary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>Uso di movimenti personalizzati

Per riconoscere i movimenti personalizzati in un'attività, deve disporre di un oggetto Android.Gesture.GestureOverlay aggiunto per il relativo layout. Il frammento di codice seguente viene illustrato come aggiungere a livello di codice un GestureOverlayView a un'attività:

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

Il `GestureOverlayView` ha diversi eventi che verranno generati durante il processo di disegno un movimento. L'evento più interessante è `GesturePerformed`. Questo evento viene generato quando l'utente ha completato i movimenti di disegno.

Quando questo evento viene generato, l'attività richiede un `GestureLibrary` per prova e far corrispondere il movimento creati dall'utente con uno dei movimenti dallo strumento di movimento. `GestureLibrary` Restituisce un elenco di oggetti di stima.

Ogni oggetto stima ha un punteggio e nome di uno dei movimenti nel `GestureLibrary`. Maggiore il punteggio, più è probabile che il movimento denominato nella stima consente di ricercare il movimento disegnato dall'utente.
In generale, i punteggi più bassi rispetto a 1.0 verranno considerati corrispondenti scarse.

Il codice seguente illustra un esempio di corrispondenza un movimento:

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

Con questa operazione, è necessario comprendere come usare touch e movimenti in un'applicazione xamarin. Android. Elaborando ulteriormente ora passare a una procedura dettagliata, vedere tutti i concetti in un'applicazione di esempio funzionante.



## <a name="related-links"></a>Collegamenti correlati

- [Android toccare Start (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android Touch finale (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
