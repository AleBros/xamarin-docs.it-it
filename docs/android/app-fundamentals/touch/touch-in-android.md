---
title: Tocco in Android
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 0619dd9a81c40633c7c61f23ef9e8f3ef92c2e6a
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70197628"
---
# <a name="touch-in-android"></a>Tocco in Android

Analogamente a iOS, Android crea un oggetto che include i dati sull'interazione fisica dell'utente con la &ndash; schermata `Android.View.MotionEvent` di un oggetto. Questo oggetto include dati quali l'azione eseguita, la posizione in cui si è verificato il tocco, la quantità di pressione applicata e così via. Un `MotionEvent` oggetto suddivide lo spostamento in con i valori seguenti:

- Codice di azione che descrive il tipo di movimento, ad esempio il tocco iniziale, il tocco in movimento sullo schermo o il tocco finale.

- Set di valori dell'asse che descrivono la posizione di `MotionEvent` e altre proprietà di spostamento, ad esempio il punto in cui si verifica il tocco, il momento in cui si è verificato il tocco e la quantità di pressione utilizzata.
   I valori dell'asse possono essere diversi a seconda del dispositivo, quindi l'elenco precedente non descrive tutti i valori dell'asse.


L' `MotionEvent` oggetto verrà passato a un metodo appropriato in un'applicazione. È possibile che un'applicazione Novell. Android risponda a un evento Touch in tre modi:

- *Assegnare un gestore eventi a `View.Touch`*  : la `Android.Views.View` classe dispone di `EventHandler<View.TouchEventArgs>` un oggetto a cui le applicazioni possono assegnare un gestore. Si tratta di un comportamento tipico di .NET.

- *Implementazione`View.IOnTouchListener`* : le istanze di questa interfaccia possono essere assegnate a un oggetto visualizzazione utilizzando la vista. `SetOnListener`Metodo. Dal `View.Touch` punto di vista funzionale equivale all'assegnazione di un gestore eventi all'evento. Se è presente una logica comune o condivisa che molte visualizzazioni diverse potrebbero avere bisogno quando vengono modificate, sarà più efficiente creare una classe e implementare questo metodo anziché assegnare a ogni visualizzazione il proprio gestore eventi.

- *Esegue `View.OnTouchEvent` l'override* di tutte le viste `Android.Views.View`nella sottoclasse Android. Quando viene toccata una visualizzazione, Android `OnTouchEvent` chiamerà e passa un `MotionEvent` oggetto come parametro.


> [!NOTE]
> Non tutti i dispositivi Android supportano le schermate touch. 

Se si aggiunge il tag seguente al file manifesto, Google Play Visualizza solo l'app per i dispositivi abilitati per il tocco:

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>Movimenti

Un gesto è una forma disegnata a mano sul touchscreen. Un movimento può avere uno o più tratti, ogni tratto costituito da una sequenza di punti creati da un altro punto di contatto con lo schermo. Android può supportare molti tipi diversi di movimenti, da una semplice Fling sullo schermo a movimenti complessi che coinvolgono il multitocco.

Android fornisce lo `Android.Gestures` spazio dei nomi specifico per la gestione e la risposta ai movimenti. Il fulcro di tutti i movimenti è una classe speciale denominata `Android.Gestures.GestureDetector`. Come suggerisce il nome, questa classe resterà in ascolto di movimenti ed eventi in `MotionEvents` base a forniti dal sistema operativo.

Per implementare un rilevatore di movimenti, un'attività deve `GestureDetector` creare un'istanza di una classe `IOnGestureListener`e fornire un'istanza di, come illustrato nel frammento di codice seguente:

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

Un'attività deve implementare anche OnTouchEvent e passare MotionEvent al rilevatore di movimento. Il frammento di codice seguente mostra un esempio di questo:

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

Quando un'istanza di `GestureDetector` identifica un movimento di interesse, invierà una notifica all'attività o all'applicazione generando un evento o tramite un callback fornito `GestureDetector.IOnGestureListener`da.
Questa interfaccia fornisce sei metodi per i diversi movimenti:

- *Ondown* : viene chiamato quando si verifica un tap ma non viene rilasciato.

- *Onfling* : viene chiamato quando si verifica un'operazione di Fling e fornisce i dati sul tocco iniziale e finale che ha attivato l'evento.

- *OnLongPress* : viene chiamato quando si verifica una pressione prolungata.

- *Onscroll* : chiamato quando si verifica un evento Scroll.

- *OnShowPress* : viene chiamato dopo che si è verificato un ondown e non è stato eseguito un evento di spostamento o di discesa.

- *OnSingleTapUp* : viene chiamato quando si verifica un solo tocco.


In molti casi le applicazioni possono essere interessate solo a un subset di movimenti. In questo caso, le applicazioni devono estendere la classe GestureDetector. SimpleOnGestureListener ed eseguire l'override dei metodi che corrispondono agli eventi a cui sono interessati.

## <a name="custom-gestures"></a>Movimenti personalizzati

I movimenti sono un ottimo modo per consentire agli utenti di interagire con un'applicazione. Le API che abbiamo visto finora sono sufficienti per i semplici movimenti, ma potrebbero rivelarsi un po' oneroso per i movimenti più complicati. Per semplificare l'uso di movimenti più complessi, Android fornisce un altro set di API nello spazio dei nomi Android. gestures che faciliterà il sovraccarico associato ai movimenti personalizzati.

### <a name="creating-custom-gestures"></a>Creazione di movimenti personalizzati

Da Android 1,6, il Android SDK viene fornita con un'applicazione preinstallata nell'emulatore denominato gestures Builder. Questa applicazione consente agli sviluppatori di creare movimenti predefiniti che possono essere incorporati in un'applicazione. Lo screenshot seguente mostra un esempio di generatore di movimenti:

[![Screenshot del generatore di movimenti con movimenti di esempio](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

È possibile trovare una versione migliorata dell'applicazione denominata strumento movimento Google Play. Lo strumento gesto è molto simile al generatore di movimenti, ad eccezione del fatto che consente di testare i movimenti dopo che sono stati creati. Lo screenshot seguente mostra il generatore di movimenti:

[![Screenshot dello strumento movimento con movimenti di esempio](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

Lo strumento movimento è un po' più utile per la creazione di movimenti personalizzati in quanto consente di testare i movimenti quando vengono creati ed è facilmente disponibile tramite Google Play.

Lo strumento movimento consente di creare un movimento disegnando sullo schermo e assegnando un nome. Una volta creati i movimenti, questi vengono salvati in un file binario sulla scheda SD del dispositivo. Questo file deve essere recuperato dal dispositivo e quindi incluso in un pacchetto con un'applicazione nella cartella/Resources/RAW. Questo file può essere recuperato dall'emulatore usando il Android Debug Bridge. L'esempio seguente illustra la copia del file da un Galaxy Nexus alla directory delle risorse di un'applicazione:

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

Una volta recuperato, il file deve essere incluso nell'applicazione nella directory/Resources/RAW. Il modo più semplice per usare questo file di movimento consiste nel caricare il file in un GestureLibrary, come illustrato nel frammento di codice seguente:

```csharp
GestureLibrary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>Uso di movimenti personalizzati

Per riconoscere i movimenti personalizzati in un'attività, è necessario che l'oggetto Android. Gesture. GestureOverlay sia stato aggiunto al relativo layout. Il frammento di codice seguente illustra come aggiungere un GestureOverlayView a un'attività a livello di codice:

```csharp
GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
gestureOverlayView.AddOnGesturePerformedListener(this);
SetContentView(gestureOverlayView);
```

Il frammento di codice XML seguente mostra come aggiungere un GestureOverlayView in modo dichiarativo:

```xml
<android.gesture.GestureOverlayView
    android:id="@+id/gestures"
    android:layout_width="match_parent "
    android:layout_height="match_parent" />
```

In `GestureOverlayView` sono disponibili diversi eventi che verranno generati durante il processo di creazione di un movimento. L'evento più interessante è `GesturePerformed`. Questo evento viene generato quando l'utente ha completato il disegno del movimento.

Quando viene generato questo evento, l'attività chiede a `GestureLibrary` un oggetto di tentare di associare il movimento che l'utente a uno dei movimenti creati dallo strumento di movimento. `GestureLibrary`restituirà un elenco di oggetti di stima.

Ogni oggetto di stima include un punteggio e il nome di uno dei movimenti in `GestureLibrary`. Maggiore è il punteggio, maggiore è la probabilità che il movimento indicato nella stima corrisponda al gesto disegnato dall'utente.
In generale, i punteggi inferiori a 1,0 sono considerati scarse corrispondenze.

Il codice seguente illustra un esempio di corrispondenza di un gesto:

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

A questo proposito, è necessario comprendere come usare il tocco e i movimenti in un'applicazione Novell. Android. Passare ora a una procedura dettagliata e visualizzare tutti i concetti in un'applicazione di esempio funzionante.



## <a name="related-links"></a>Collegamenti correlati

- [Android touch Start (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android touch finale (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
