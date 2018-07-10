---
title: Procedura dettagliata - uso del tocco in Android
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/09/2018
ms.openlocfilehash: d379630e3b7fa2b42bd9530e1dccd75e9634dd2f
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935527"
---
# <a name="walkthrough---using-touch-in-android"></a>Procedura dettagliata - uso del tocco in Android

Possiamo vedere come usare i concetti nella sezione precedente in un'applicazione funzionante. Si creerà un'applicazione con quattro attività. La prima attività sarà un menu o un pannello che si desidera visualizzare le altre attività per illustrare le diverse API. Lo screenshot seguente mostra l'attività principale:

[![Screenshot di esempio con Me tocco sul pulsante](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

La prima attività, esempio di tocco, mostrerà come utilizzare i gestori eventi per modificare le visualizzazioni. L'attività di riconoscitore di movimento illustrerà come sottoclasse `Android.View.Views` e gestire gli eventi, nonché illustrano come gestire i movimenti zoom indietro. La terza e ultima attività, **movimenti personalizzato**, verrà. illustrano come usare i movimenti personalizzati. Per semplificare la procedura da seguire e absorb, si sarà suddividere questa procedura dettagliata in sezioni, con ogni sezione fermiamo a considerare una delle attività.

## <a name="touch-sample-activity"></a>Attività di esempio tocco

-   Aprire il progetto **TouchWalkthrough\_avviare**. Il **MainActivity** è tutto pronto per passare &ndash; spetta Stati Uniti per implementare il comportamento di tocco nell'attività. Se si esegue l'applicazione e fare clic su **Touch esempio**, dovrebbe avviarsi l'attività seguente:

    [![Screenshot dell'attività con Touch inizia visualizzato](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

-   Ora che abbiamo verificato che l'attività di avvio, aprire il file **TouchActivity.cs** e aggiungere un gestore per il `Touch` evento del `ImageView`:

    ```csharp
    _touchMeImageView.Touch += TouchMeImageViewOnTouch;
    ```

-   Successivamente, aggiungere il metodo seguente alla **TouchActivity.cs**:

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

Si noti che nel codice precedente che si considera il `Move` e `Down` azione stesso. Infatti, anche se l'utente potrebbe non sollevare il dito il `ImageView`, è possibile spostarsi all'interno o alla pressione esercitata dall'utente può cambiare. Questi tipi di modifiche verranno generato un `Move` azione.

Ogni volta che l'utente tocca il `ImageView`, il `Touch` verrà generato l'evento e il nostro gestore verrà visualizzato il messaggio **Touch inizia** sullo schermo, come illustrato nello screenshot seguente:

[![Screenshot dell'attività con Touch inizia](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

Fino a quando l'utente tocca il `ImageView`, **Touch inizia** verrà visualizzato nel `TextView`. Quando l'utente non tocca il `ImageView`, il messaggio **Touch termina** verrà visualizzato nel `TextView`, come illustrato nello screenshot seguente:

[![Screenshot dell'attività con Touch termina](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)


## <a name="gesture-recognizer-activity"></a>Attività di riconoscitore di movimento

Ora consente di implementare l'attività di riconoscitore di movimento. Questa attività verrà illustrato come trascinare una visualizzazione attorno alla schermata e illustrano come implementare zoom con avvicinamento delle dita.

-   Aggiungere una nuova attività per l'applicazione denominata `GestureRecognizer`.
    Modificare il codice per l'attività in modo che risulti simile al seguente:

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

-   Aggiungere un nuovo Android consente di visualizzare al progetto e denominarlo `GestureRecognizerView`. Per questa classe, aggiungere le variabili seguenti:

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

-   Aggiungere il costruttore seguente alla `GestureRecognizerView`. Questo costruttore verrà aggiunto un `ImageView` per l'attività. A questo punto il codice ancora non verrà compilato &ndash; è necessario creare la classe `MyScaleListener` contribuire con il ridimensionamento di `ImageView` quando l'utente pinches:

    ```csharp
    public GestureRecognizerView(Context context): base(context, null, 0)
    {
        _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
        _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
        _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
    }
    ```

-   Per disegnare l'immagine nel nostro impegno, è necessario eseguire l'override di `OnDraw` metodo della classe di visualizzazione, come illustrato nel frammento di codice seguente. Questo codice si sposterà il `ImageView` nella posizione specificata da `_posX` e `_posY` anche come ridimensionare l'immagine in base al fattore di scala:

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

-   Successivamente è necessario aggiornare la variabile di istanza `_scaleFactor` come utente pinches il `ImageView`. Si aggiungerà una classe denominata `MyScaleListener`. Questa classe sarà in ascolto per gli eventi di scalabilità che verranno generati da Android quando l'utente pinches il `ImageView`.
    Aggiungere la seguente classe interna per `GestureRecognizerView`. Questa classe è un `ScaleGesture.SimpleOnScaleGestureListener`. Questa classe è una classe utile che i listener possono creare una sottoclasse quando si è interessati a un subset dei movimenti:

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

-   Il metodo successivo è necessario eseguire l'override nel `GestureRecognizerView` è `OnTouchEvent`. Il codice seguente elenca l'implementazione completa di questo metodo. È presente una grande quantità di codice in questo caso, pertanto essere necessari alcuni minuti e osservare cosa sta succedendo. La prima cosa, questo metodo non è scalare l'icona, se necessario &ndash; questa operazione viene gestita chiamando `_scaleDetector.OnTouchEvent`. Successivamente si tenta di determinare l'azione che ha chiamato questo metodo:

    - Se l'utente interessate allo schermo, si registrano le posizioni X e Y e l'ID del primo puntatore che modificate la schermata.

    - Se l'utente spostato i touch sullo schermo, quindi è scoprire fino a quando l'utente ha spostato il puntatore.

    - Se l'utente ha sollevato il dito fuori dallo schermo, quindi si interromperà i movimenti di rilevamento.

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

-   Ora eseguire l'applicazione e avviare l'attività di riconoscitore di movimento.
    Quando avvia la schermata avrà un aspetto simile allo screenshot seguente:

    [![Schermata iniziale di riconoscitore di movimento con icona di Android](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

-   Ora toccare l'icona e trascinarlo attorno alla schermata. Provare il movimento di zoom con avvicinamento delle dita. A un certo punto la schermata potrebbe essere simile alla schermata riportata di seguito:

    [![Icona di spostamento i movimenti attorno alla schermata](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

A questo punto è necessario assegnare manualmente un token di accesso personale sul retro: in un'applicazione Android è stata implementata solo zoom con avvicinamento delle dita! Richiedere una piccola pausa e consente di passare alla terza e ultima attività in questa procedura dettagliata &ndash; tramite movimenti personalizzati.

## <a name="custom-gesture-activity"></a>Attività di movimenti personalizzati

Schermata finale in questa procedura dettagliata verrà usato di movimenti personalizzati.

Ai fini di questa procedura dettagliata, la libreria di movimenti già creata usando lo strumento di movimento e aggiunta al progetto nel file **risorse/raw/movimenti**. Con questa attività di ordinaria amministrazione dall'area di lavoro, consente di visualizzare con l'attività finale nella procedura dettagliata.

-   Aggiungere un file di layout denominato **personalizzati\_movimento\_layout.axml** al progetto con il contenuto seguente. Il progetto contiene già tutte le immagini nella **risorse** cartella:

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

-   Quindi aggiungere una nuova attività al progetto e denominarlo `CustomGestureRecognizerActivity.cs`. Aggiungere due variabili di istanza alla classe, come illustrato nelle due righe di codice seguente:

    ```csharp
    private GestureLibrary _gestureLibrary;
    private ImageView _imageView;
    ```

-   Modificare il `OnCreate` metodo di questa attività in modo che sia simile al seguente codice. Consente di richiedere un minuto per spiegare cosa sta succedendo in questo codice. È la prima cosa creare un'istanza di un `GestureOverlayView` e impostarlo come la visualizzazione principale dell'attività.
    Viene inoltre assegnato un gestore eventi per il `GesturePerformed` eventi di `GestureOverlayView`. Successivamente si aumento il file di layout che è stato creato in precedenza e aggiungerle come visualizzazione figlio del `GestureOverlayView`. Il passaggio finale consiste nell'inizializzare la variabile `_gestureLibrary` e caricare il file di movimenti dalle risorse dell'applicazione. Se non è possibile caricare il file di movimenti per qualche motivo, non c'è molto può eseguire questa attività, in modo che è stata arrestata:

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

-   L'ultimo aspetto è necessario implementare il metodo `GestureOverlayViewOnGesturePerformed` come illustrato nel frammento di codice seguente. Quando il `GestureOverlayView` rileva un movimento, richiama questo metodo. La prima cosa si tenta di ottenere un `IList<Prediction>` gli oggetti che corrispondono al movimento chiamando `_gestureLibrary.Recognize()`. Utilizziamo un po' di LINQ per ottenere il `Prediction` con il punteggio più elevato per il movimento.

    Se si è verificato non corrisponde ad alcuna di movimento con un valore alto sufficiente punteggio, quindi il gestore eventi viene chiusa senza eseguire alcuna operazione. In caso contrario è controllare il nome della stima e modificare l'immagine viene visualizzata in base al nome del movimento:

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

-   Eseguire l'applicazione e avviare l'attività personalizzata riconoscitore di movimento. Dovrebbe essere simile alla seguente:

    [![Screenshot con Me controllo immagine](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

    Disegna un segno di spunta nella schermata e la bitmap visualizzata dovrebbe essere molto simile a quello illustrato negli screenshot successivi:

    [![Segno di spunta disegnata, segno di spunta viene riconosciuto](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

    Infine, disegnare un disegno a mano libera sullo schermo. La casella di controllo dovrebbe cambiare nuovamente per l'immagine originale, come illustrato nelle schermate illustrate:

    [![Viene visualizzato nella schermata immagine originale Scribble](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

Ora è comprendere come integrare touch e movimenti in un'applicazione Android usando xamarin. Android.


## <a name="related-links"></a>Collegamenti correlati

- [Android toccare Start (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android Touch finale (esempio)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
