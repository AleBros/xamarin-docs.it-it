---
title: Procedura dettagliata-uso del tocco in Android
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/09/2018
ms.openlocfilehash: efff9edd81f17979ce8f4441da3d512d0a8ec188
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526274"
---
# <a name="walkthrough---using-touch-in-android"></a>Procedura dettagliata-uso del tocco in Android

Viene ora illustrato come usare i concetti della sezione precedente in un'applicazione funzionante. Si creerà un'applicazione con quattro attività. La prima attività sarà costituita da un menu o da un pannello che avvierà le altre attività per illustrare le varie API. La schermata seguente mostra l'attività principale:

[![Schermata di esempio con pulsante tocco](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

Nella prima attività, touch Sample, viene illustrato come utilizzare i gestori eventi per toccare le visualizzazioni. L'attività del riconoscitore di movimento illustra come `Android.View.Views` sottoclasse e gestire gli eventi, oltre a mostrare come gestire i movimenti di tocco. La terza e ultima attività, **azione personalizzata**, mostrerà come usare i movimenti personalizzati. Per semplificare le operazioni da seguire e da assorbire, questa procedura dettagliata verrà suddivisa in sezioni, con ogni sezione dedicata a una delle attività.

## <a name="touch-sample-activity"></a>Attività di esempio touch

- Aprire il progetto **TouchWalkthrough\_Start**. Il **MainActivity** è impostato su Go &ndash; per implementare il comportamento di tocco nell'attività. Se si esegue l'applicazione e si fa clic su **touch Sample**, verrà avviata l'attività seguente:

    [![Schermata dell'attività con il tocco visualizzato](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

- Ora che è stato confermato che l'attività viene avviata, aprire il file **TouchActivity.cs** e aggiungere un gestore per `Touch` l'evento del `ImageView`:

    ```csharp
    _touchMeImageView.Touch += TouchMeImageViewOnTouch;
    ```

- Aggiungere quindi il metodo seguente a **TouchActivity.cs**:

    ```csharp
    private void TouchMeImageViewOnTouch(object sender, View.TouchEventArgs touchEventArgs)
    {
        string message;
        switch (touchEventArgs.Event.Action & MotionEventActions.Mask)
        {
            case MotionEventActions.Down:
            case MotionEventActions.Move:
            message = "Touch Begins";
            break;

            case MotionEventActions.Up:
            message = "Touch Ends";
            break;

            default:
            message = string.Empty;
            break;
        }

        _touchInfoTextView.Text = message;
    }
    ```

Si noti che nel codice precedente si tratta dell' `Move` azione `Down` e come lo stesso. Questo è dovuto al fatto che, anche se l'utente non è in `ImageView`grado di sollevare il dito, può spostarsi o la pressione esercitata dall'utente può cambiare. Questi tipi di modifiche genereranno un' `Move` azione.

Ogni volta che l'utente tocca `ImageView`, viene `Touch` generato l'evento e il gestore visualizzerà il tocco del messaggio **avviato** sullo schermo, come illustrato nello screenshot seguente:

[![Screenshot dell'attività con avvio tocco](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

Fino a quando l'utente sta toccando `ImageView`il, il **tocco** verrà visualizzato in `TextView`. Quando l'utente non tocca `ImageView`più, il **tocco** del messaggio viene visualizzato nella `TextView`, come illustrato nello screenshot seguente:

[![Screenshot dell'attività con le estremità del tocco](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)


## <a name="gesture-recognizer-activity"></a>Attività del riconoscitore di movimento

Consente ora di implementare l'attività del riconoscitore di movimento. Questa attività illustra come trascinare una visualizzazione intorno allo schermo e come illustrare un modo per implementare il pinch-to-zoom.

- Aggiungere una nuova attività all'applicazione denominata `GestureRecognizer`.
    Modificare il codice per questa attività in modo che sia simile al codice seguente:

    ```csharp
    public class GestureRecognizerActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            View v = new GestureRecognizerView(this);
            SetContentView(v);
        }
    }
    ```

- Aggiungere una nuova visualizzazione Android al progetto e denominarla `GestureRecognizerView`. Aggiungere le variabili seguenti a questa classe:

    ```csharp
    private static readonly int InvalidPointerId = -1;

    private readonly Drawable _icon;
    private readonly ScaleGestureDetector _scaleDetector;

    private int _activePointerId = InvalidPointerId;
    private float _lastTouchX;
    private float _lastTouchY;
    private float _posX;
    private float _posY;
    private float _scaleFactor = 1.0f;
    ```

- Aggiungere il costruttore seguente a `GestureRecognizerView`. Questo costruttore consente di aggiungere `ImageView` un oggetto all'attività. A questo punto, il codice non verrà compilato &ndash; , ma è necessario creare la `MyScaleListener` classe che consentirà di ridimensionare `ImageView` quando l'utente lo pizzica:

    ```csharp
    public GestureRecognizerView(Context context): base(context, null, 0)
    {
        _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
        _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
        _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
    }
    ```

- Per creare l'immagine nell'attività, è necessario eseguire l'override del `OnDraw` metodo della classe di visualizzazione, come illustrato nel frammento di codice seguente. Questo codice sposta l' `ImageView` oggetto nella posizione specificata da `_posX` e `_posY` , oltre a ridimensionare l'immagine in base al fattore di scala:

    ```csharp
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        canvas.Save();
        canvas.Translate(_posX, _posY);
        canvas.Scale(_scaleFactor, _scaleFactor);
        _icon.Draw(canvas);
        canvas.Restore();
    }
    ```

- Successivamente, è necessario aggiornare la variabile `_scaleFactor` di istanza quando l'utente pizzica il. `ImageView` Si aggiungerà una classe denominata `MyScaleListener`. Questa classe resterà in ascolto degli eventi di ridimensionamento che verranno generati da Android quando l'utente pizzica `ImageView`il.
    Aggiungere la classe interna seguente a `GestureRecognizerView`. Questa classe è un `ScaleGesture.SimpleOnScaleGestureListener`oggetto. Questa classe è una classe di praticità che i listener possono sottoclassare quando si è interessati a un subset di movimenti:

    ```csharp
    private class MyScaleListener : ScaleGestureDetector.SimpleOnScaleGestureListener
    {
        private readonly GestureRecognizerView _view;

        public MyScaleListener(GestureRecognizerView view)
        {
            _view = view;
        }

        public override bool OnScale(ScaleGestureDetector detector)
        {
            _view._scaleFactor *= detector.ScaleFactor;

            // put a limit on how small or big the image can get.
            if (_view._scaleFactor > 5.0f)
            {
                _view._scaleFactor = 5.0f;
            }
            if (_view._scaleFactor < 0.1f)
            {
                _view._scaleFactor = 0.1f;
            }

            _view.Invalidate();
            return true;
        }
    }
    ```

- Il metodo successivo in cui è necessario eseguire `GestureRecognizerView` l' `OnTouchEvent`override di è. Il codice seguente elenca l'implementazione completa di questo metodo. Qui c'è una grande quantità di codice, quindi diamo un minuto per vedere cosa succede qui. La prima operazione eseguita &ndash; `_scaleDetector.OnTouchEvent`da questo metodo è la scalabilità dell'icona, se necessario, che viene gestita chiamando. Successivamente si tenterà di individuare l'azione che ha chiamato questo metodo:

    - Se l'utente ha toccato la schermata con, vengono registrate le posizioni X e Y e l'ID del primo puntatore che ha toccato lo schermo.

    - Se l'utente ha spostato il tocco sullo schermo, è stata calcolata la distanza con cui l'utente ha spostato il puntatore.

    - Se l'utente ha sollevato il dito dallo schermo, il rilevamento dei movimenti verrà interrotto.

    ```csharp
    public override bool OnTouchEvent(MotionEvent ev)
    {
        _scaleDetector.OnTouchEvent(ev);

        MotionEventActions action = ev.Action & MotionEventActions.Mask;
        int pointerIndex;

        switch (action)
        {
            case MotionEventActions.Down:
            _lastTouchX = ev.GetX();
            _lastTouchY = ev.GetY();
            _activePointerId = ev.GetPointerId(0);
            break;

            case MotionEventActions.Move:
            pointerIndex = ev.FindPointerIndex(_activePointerId);
            float x = ev.GetX(pointerIndex);
            float y = ev.GetY(pointerIndex);
            if (!_scaleDetector.IsInProgress)
            {
                // Only move the ScaleGestureDetector isn't already processing a gesture.
                float deltaX = x - _lastTouchX;
                float deltaY = y - _lastTouchY;
                _posX += deltaX;
                _posY += deltaY;
                Invalidate();
            }

            _lastTouchX = x;
            _lastTouchY = y;
            break;

            case MotionEventActions.Up:
            case MotionEventActions.Cancel:
            // We no longer need to keep track of the active pointer.
            _activePointerId = InvalidPointerId;
            break;

            case MotionEventActions.PointerUp:
            // check to make sure that the pointer that went up is for the gesture we're tracking.
            pointerIndex = (int) (ev.Action & MotionEventActions.PointerIndexMask) >> (int) MotionEventActions.PointerIndexShift;
            int pointerId = ev.GetPointerId(pointerIndex);
            if (pointerId == _activePointerId)
            {
                // This was our active pointer going up. Choose a new
                // action pointer and adjust accordingly
                int newPointerIndex = pointerIndex == 0 ? 1 : 0;
                _lastTouchX = ev.GetX(newPointerIndex);
                _lastTouchY = ev.GetY(newPointerIndex);
                _activePointerId = ev.GetPointerId(newPointerIndex);
            }
            break;

        }
        return true;
    }
    ```

- A questo punto, eseguire l'applicazione e avviare l'attività del riconoscitore di movimento.
    All'avvio, la schermata dovrebbe essere simile alla schermata seguente:

    [![Schermata iniziale del riconoscitore di movimento con icona Android](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

- A questo punto, toccare l'icona e trascinarla intorno allo schermo. Provare a eseguire il movimento di tocco. A un certo punto la schermata potrebbe essere simile alla schermata seguente:

    [![Icona Sposta movimenti intorno allo schermo](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

A questo punto, è necessario fornire una pacca sul retro: è stato appena implementato il pinch-to-zoom in un'applicazione Android. Prendere un breve intervallo di tempo e passare alla terza e ultima attività in questa procedura dettagliata &ndash; usando i movimenti personalizzati.

## <a name="custom-gesture-activity"></a>Attività gesto personalizzato

La schermata finale in questa procedura dettagliata utilizzerà movimenti personalizzati.

Ai fini di questa procedura dettagliata, la libreria movimenti è già stata creata usando lo strumento movimento e aggiunta al progetto nelle **risorse file/RAW/gestures**. Con questo tipo di manutenzione, è possibile iniziare con l'attività finale nella procedura dettagliata.

- Aggiungere un file di layout **denominato\_Custom\_gesture layout. aXML** al progetto con il contenuto seguente. Il progetto contiene già tutte le immagini nella cartella Resources:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
        <ImageView
            android:src="@drawable/check_me"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="3"
            android:id="@+id/imageView1"
            android:layout_gravity="center_vertical" />
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
    </LinearLayout>
    ```

- Successivamente, aggiungere una nuova attività al progetto e denominarla `CustomGestureRecognizerActivity.cs`. Aggiungere due variabili di istanza alla classe, come illustrato nelle due righe di codice seguenti:

    ```csharp
    private GestureLibrary _gestureLibrary;
    private ImageView _imageView;
    ```

- Modificare il `OnCreate` metodo di questa attività in modo che sia simile al codice seguente. È possibile decifrare un minuto per spiegare cosa accade in questo codice. La prima cosa da fare è creare un'istanza `GestureOverlayView` di e impostarla come visualizzazione radice dell'attività.
    Viene inoltre assegnato un gestore eventi all' `GesturePerformed` evento di. `GestureOverlayView` A questo punto, è possibile aumentare il file di layout creato in precedenza e aggiungerlo come visualizzazione figlio del `GestureOverlayView`. Il passaggio finale consiste nell'inizializzare la `_gestureLibrary` variabile e caricare il file dei movimenti dalle risorse dell'applicazione. Se per qualche motivo non è possibile caricare il file dei movimenti, non è possibile eseguire questa attività, pertanto viene arrestato:

    ```csharp
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
        SetContentView(gestureOverlayView);
        gestureOverlayView.GesturePerformed += GestureOverlayViewOnGesturePerformed;

        View view = LayoutInflater.Inflate(Resource.Layout.custom_gesture_layout, null);
        _imageView = view.FindViewById<ImageView>(Resource.Id.imageView1);
        gestureOverlayView.AddView(view);

        _gestureLibrary = GestureLibraries.FromRawResource(this, Resource.Raw.gestures);
        if (!_gestureLibrary.Load())
        {
            Log.Wtf(GetType().FullName, "There was a problem loading the gesture library.");
            Finish();
        }
    }
    ```

- L'ultima cosa da fare è implementare il metodo `GestureOverlayViewOnGesturePerformed` , come illustrato nel frammento di codice seguente. Quando rileva `GestureOverlayView` un movimento, richiama il metodo. La prima cosa da fare è provare a `IList<Prediction>` ottenere un oggetto che corrisponda al `_gestureLibrary.Recognize()`gesto chiamando. Viene usato un bit di LINQ per ottenere l' `Prediction` oggetto con il punteggio più alto per il movimento.

    Se non esiste un movimento corrispondente con un punteggio sufficientemente elevato, il gestore eventi viene chiuso senza eseguire alcuna operazione. In caso contrario, si verificherà il nome della stima e si modificherà l'immagine da visualizzare in base al nome del movimento:

    ```csharp
    private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
    {
        IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
        orderby p.Score descending
        where p.Score > 1.0
        select p;
        Prediction prediction = predictions.FirstOrDefault();

        if (prediction == null)
        {
            Log.Debug(GetType().FullName, "Nothing seemed to match the user's gesture, so don't do anything.");
            return;
        }

        Log.Debug(GetType().FullName, "Using the prediction named {0} with a score of {1}.", prediction.Name, prediction.Score);

        if (prediction.Name.StartsWith("checkmark"))
        {
            _imageView.SetImageResource(Resource.Drawable.checked_me);
        }
        else if (prediction.Name.StartsWith("erase", StringComparison.OrdinalIgnoreCase))
        {
            // Match one of our "erase" gestures
            _imageView.SetImageResource(Resource.Drawable.check_me);
        }
    }
    ```

- Eseguire l'applicazione e avviare l'attività di riconoscimento del movimento personalizzato. Dovrebbe avere un aspetto simile allo screenshot seguente:

    [![Screenshot con immagine check me](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

    A questo punto, tracciare un segno di spunta sullo schermo e la bitmap visualizzata dovrebbe avere un aspetto simile a quello mostrato negli screenshot successivi:

    [![Segno di spunta disegnato, segno di spunta riconosciuto](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

    Infine, disegna uno scarabocchio sullo schermo. La casella di controllo deve tornare all'immagine originale, come illustrato nelle schermate seguenti:

    [![Scribble sullo schermo, viene visualizzata l'immagine originale](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

È ora possibile comprendere come integrare il tocco e i movimenti in un'applicazione Android con Novell. Android.


## <a name="related-links"></a>Collegamenti correlati

- [Android touch Start (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android touch finale (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
