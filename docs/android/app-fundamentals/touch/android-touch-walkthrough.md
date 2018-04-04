---
title: 'Procedura dettagliata: utilizzo di tocco in Android'
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: ae4d5b0b8cd384a11d130bc9258894e1cf4cfa36
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---using-touch-in-android"></a>Procedura dettagliata: utilizzo di tocco in Android

Possiamo vedere come utilizzare i concetti della sezione precedente in un'applicazione funzionante. Si creerà un'applicazione con quattro attività. La prima attività sarà un menu o un pannello comandi che si desidera visualizzare le altre attività per illustrare le varie API. Nella schermata seguente mostra l'attività principale:

[![Schermata di esempio con Touch Me pulsante](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

La prima attività, esempio tocco, viene illustrato come utilizzare i gestori eventi per modificare le visualizzazioni. L'attività riconoscitore di movimento verrà illustrato come sottoclasse `Android.View.Views` e la gestione degli eventi, nonché illustrano come gestire i movimenti zoom indietro. La terza e ultima attività, **movimenti personalizzato**, Mostra come utilizzerà movimenti personalizzati. Per semplificare la procedura da seguire e assorbire, di interruzione in questa procedura dettagliata sezioni, con ogni sezione porre l'attenzione su una delle attività.

## <a name="touch-sample-activity"></a>Attività di esempio tocco

-   Aprire il progetto **TouchWalkthrough\_avviare**. Il **MainActivity** tutte è impostato su go &ndash; spetta per implementare il comportamento di tocco nell'attività. Se si esegue l'applicazione e fare clic su **esempio tocco**, deve avviare l'attività seguente:

    [![Schermata di attività con Touch inizia visualizzato](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

-   Ora che è stata confermata che l'attività di avvio, aprire il file **TouchActivity.cs** e aggiungere un gestore per il `Touch` evento del `ImageView`:

    ```csharp
    _touchMeImageView.Touch += TouchMeImageViewOnTouch;
    ```

-   Successivamente, aggiungere il metodo seguente alla **TouchActivity.cs**:

    ```csharp
    private void TouchMeImageViewOnTouch(object sender, View.TouchEventArgs touchEventArgs)
    {
        string message;
        switch (touchEventArgs.Event.Action & MotionEventArgs.Mask)
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

Si noti che nel codice precedente che si considera il `Move` e `Down` azione stesso. Infatti, anche se l'utente non può solleva il dito il `ImageView`, è possibile spostarsi all'interno o alla pressione esercitata dall'utente può cambiare. Questi tipi di modifiche genererà un `Move` azione.

Ogni volta che l'utente ultimi ritocchi il `ImageView`, `Touch` verrà generato l'evento e il gestore verrà visualizzato il messaggio **tocco inizia** sullo schermo, come illustrato nella schermata seguente:

[![Schermata di attività con Touch inizia](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

Fino a quando l'utente tocca il `ImageView`, **tocco inizia** verrà visualizzato nel `TextView`. Quando l'utente non è più tocca il `ImageView`, il messaggio **tocco termina** verrà visualizzato nel `TextView`, come illustrato nella schermata seguente:

[![Schermata di attività con Touch termina](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)


## <a name="gesture-recognizer-activity"></a>Attività riconoscitore di movimento

Ora consente di implementare l'attività riconoscitore di movimento. Questa attività verrà illustrato come trascinare una visualizzazione sullo schermo e viene illustrato come implementare a zoom indietro-avanti.

-   Aggiungere una nuova attività per l'applicazione denominata `GestureRecognizer`.
    Modificare il codice per l'attività in modo che risulti simile nel codice seguente:

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

-   Aggiungere un nuovo Android visualizzare al progetto e denominarlo `GestureRecognizerView`. Per questa classe, aggiungere le seguenti variabili:

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

-   Aggiungere il seguente costruttore a `GestureRecognizerView`. Questo costruttore verrà aggiunto un `ImageView` per l'attività. A questo punto il codice ancora non verrà compilato &ndash; è necessario creare la classe `MyScaleListener` che facilita la creazione di ridimensionamento di `ImageView` quando l'utente pinches:

    ```csharp
    public GestureRecognizerView(Context context): base(context, null, 0)
    {
        _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
        _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
        _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
    }
    ```

-   Per disegnare l'immagine sull'attività, è necessario eseguire l'override di `OnDraw` metodo della classe di visualizzazione, come illustrato nel frammento riportato di seguito. Questo codice si sposterà il `ImageView` nella posizione specificata da `_posX` e `_posY` nonché come ridimensionare l'immagine in base al fattore di scala:

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

-   Successivamente è necessario aggiornare la variabile di istanza `_scaleFactor` come utente pinches il `ImageView`. Si aggiungerà una classe denominata `MyScaleListener`. Questa classe sarà in ascolto per gli eventi di scala che verranno generati da Android quando l'utente pinches il `ImageView`.
    Aggiungere la seguente classe interna `GestureRecognizerView`. Questa classe è un `ScaleGesture.SimpleOnScaleGestureListener`. Questa classe è una classe utile che listener è possibile creare una sottoclasse quando si è interessati a un sottoinsieme dei movimenti:

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

            _iconview.Invalidate();
            return true;
        }
    }
    ```

-   Il metodo successivo, è necessario eseguire l'override in `GestureRecognizerView` è `OnTouchEvent`. Il codice seguente è riportata l'implementazione completa di questo metodo. È una grande quantità di codice in questo caso, pertanto consente di richiedere un minuto e osservare cosa accade in questa posizione. La prima cosa, questo metodo non è scalare l'icona, se necessario &ndash; questa operazione viene gestita chiamando `_scaleDetector.OnTouchEvent`. Abbiamo provare a individuare l'azione che ha chiamato questo metodo:

    - Se l'utente interessate allo schermo, si registra le posizioni X e Y e l'ID del puntatore prima che la schermata interessate.

    - Se l'utente spostato loro tocco sullo schermo, è scoprire quanto l'utente ha spostato il puntatore.

    - Se l'utente ha sollevato il dito sullo schermo, quindi viene arrestata i movimenti di rilevamento.

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

-   Esegui l'applicazione e avviare l'attività riconoscitore di movimento.
    Quando viene avviata la schermata dovrebbe essere simile di schermata seguente:

    [![Schermata iniziale di riconoscitore di movimento con icona di Android](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

-   Ora toccare l'icona e trascinarla sullo schermo. Provare il movimento zoom con avvicinamento delle dita. A un certo punto dello schermo potrebbe risultare simile cattura di schermata seguente:

    [![Icona di spostamento i movimenti sullo schermo](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

A questo punto è necessario assegnare manualmente un pat sul retro: è stato implementato solo a zoom indietro-avanti di un'applicazione Android. Una pausa veloce e consente di passare alla terza e ultima attività in questa procedura dettagliata &ndash; tramite movimenti personalizzati.

## <a name="custom-gesture-activity"></a>Attività di movimenti personalizzati

Nella schermata finale in questa procedura dettagliata verrà utilizzato movimenti personalizzati.

Ai fini di questa procedura dettagliata, la libreria di movimenti già creata utilizzando lo strumento di movimento e aggiunta al progetto nel file **risorse/raw/movimenti**. Il seguente frammento di in modo di manutenzione, consente di ottenere con l'attività finale nella procedura dettagliata.

-   Aggiungere un file di layout denominato **personalizzato\_movimenti\_layout.axml** al progetto con il seguente contenuto. Il progetto contiene già tutte le immagini **risorse** cartella:

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

-   Successivamente, aggiungere una nuova attività al progetto e denominarlo `CustomGestureRecognizerActivity.cs`. Aggiungere due variabili di istanza della classe, come illustrato nelle due righe di codice seguente:

    ```csharp
    private GestureLibrary _gestureLibrary;
    private ImageView _imageView;
    ```

-   Modificare il `OnCreate` metodo di questa attività in modo che è simile al codice seguente. Consente di richiedere un minuto per spiegare ciò che accade in questo codice. La prima operazione da effettuare è creare un'istanza di un `GestureOverlayView` e impostarlo come la visualizzazione dell'attività radice.
    È inoltre assegnare un gestore eventi per il `GesturePerformed` evento di `GestureOverlayView`. Successivamente è il file di layout che è stato creato in precedenza di ingrandimento e aggiungerla come una visualizzazione figlio del `GestureOverlayView`. Il passaggio finale consiste nell'inizializzare la variabile `_gestureLibrary` e caricare il file movimenti dalle risorse dell'applicazione. Se non è possibile caricare il file movimenti per qualche motivo, non c'è molto può eseguire questa attività, in modo che venga chiusa:

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

-   L'ultimo, è necessario implementare il metodo `GestureOverlayViewOnGesturePerformed` come illustrato nel frammento di codice seguente. Quando il `GestureOverlayView` rileva un movimento, richiama questo metodo. La prima cosa si tenta di ottenere un `IList<Prediction>` gli oggetti che soddisfano il movimento chiamando `_gestureLibrary.Recognize()`. Utilizziamo un bit di LINQ per ottenere il `Prediction` con il punteggio più elevato per l'azione.

    Se si è verificato non corrisponde ad alcuna di movimento con elevati sufficiente punteggio, quindi il gestore dell'evento viene chiuso senza eseguire alcuna operazione. In caso contrario è il nome della stima e modificare l'immagine viene visualizzata in base al nome del movimento:

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

-   Eseguire l'applicazione e avviare l'attività personalizzata riconoscitore di movimento. Dovrebbe essere simile al seguente nella schermata seguente:

    [![Schermata con verificare Me immagine](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

    Disegna un segno di spunta nella schermata e la bitmap viene visualizzata dovrebbe apparire simile a quello illustrato nelle schermate Avanti:

    [![Segno di spunta disegnato](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)
    [![Recognized segno di spunta](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

    Infine, disegnare un scribble sullo schermo. La casella di controllo deve ripristinare l'immagine originale come illustrato in queste schermate:

    [![Scribble sullo schermo](android-touch-walkthrough-images/image22.png)](android-touch-walkthrough-images/image22.png#lightbox)
    [![viene visualizzata l'immagine originale](android-touch-walkthrough-images/image23.png)](android-touch-walkthrough-images/image23.png#lightbox)

È ora la comprensione della modalità di integrazione di tocco e i movimenti in un'applicazione Android usando xamarin.


## <a name="related-links"></a>Collegamenti correlati

- [Android toccare avviare (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android tocco finale (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
